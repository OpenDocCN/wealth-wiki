<!--yml
category: 交易
date: 2023-09-17 20:01:36
-->

# 0008-数据处理mysqlfeed - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/170601357](https://zhuanlan.zhihu.com/p/170601357)

今天讨论数据本地化之后，如果把数据加载到backtrader中，这里使用mysqlfeed:

首先创建mysql数据库表：

*CREATE TABLE `etf510300` (*
*`date` date NOT NULL,*
*`open` double NOT NULL,*
*`high` double NOT NULL,*
*`low` double NOT NULL,*
*`close` double NOT NULL,*
*`volume` bigint NOT NULL,*
*PRIMARY KEY (`date`)*
*) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci*

*然后自定义datafeed:*

```
import backtrader as bt
import pymysql

class MysqlData(bt.feed.DataBase):
    '''
 table create statement below   CREATE TABLE `etf510300` (
 `date` date NOT NULL, `open` double NOT NULL, `high` double NOT NULL, `low` double NOT NULL, `close` double NOT NULL, `volume` bigint NOT NULL, PRIMARY KEY (`date`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci   example:   feed = MysqlData(
 db='stock_etf', dataname='etf510300', fromdate=datetime.date(2013,1,1), todate=datetime.date(2019,12,31), )
 '''
    def __init__(self, db, **kwargs):
        super(MysqlData, self).__init__(**kwargs)
        # name of the table is indicated by dataname
        # data is fetch between fromdate and todate
        assert(self.p.fromdate is not None)
        assert(self.p.todate is not None)
        # name of db
        self.db = db
        # iterator 4 data in the list
        self.iter = None
        self.data = None

    def start(self):
        if self.data is None:
            # connect to mysql db
            # hard coded db config
            connection = pymysql.connect(host='localhost',
                                         port=3306,
                                         user='root',
                                         password='seiRaefoe9jeufooT1uipei5gungiFah',
                                         db=self.db,
                                         charset='utf8mb4',
                                         cursorclass=pymysql.cursors.DictCursor)
            try:
                with connection.cursor() as cursor:
                    # Read data between fromdate and todate from db 
                    sql = 'SELECT * FROM `{:s}` WHERE date BETWEEN %s AND %s'.format(self.p.dataname)
                    cursor.execute(sql, (self.p.fromdate, self.p.todate))
                    self.data = cursor.fetchall()
            finally:
                connection.close()
        # set the iterator anyway
        self.iter = iter(self.data)

    def stop(self):
        pass

    def _load(self):
        if self.iter is None:
            # if no data ... no parsing
            return False
        # try to get 1 row of data from iterator
        try:
            row = next(self.iter)
        except StopIteration:
            # end of the list
            return False
        # fill the lines
        self.lines.datetime[0] = self.date2num(row['date'])
        self.lines.open[0] = row['open']
        self.lines.high[0] = row['high']
        self.lines.low[0] = row['low']
        self.lines.close[0] = row['close']
        self.lines.volume[0] = row['volume']
        self.lines.openinterest[0] = -1
        # Say success
        return True
```