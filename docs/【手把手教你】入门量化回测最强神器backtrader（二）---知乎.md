<!--yml
category: 交易
date: 2023-09-17 20:11:16
-->

# 【手把手教你】入门量化回测最强神器backtrader（二） - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/140425363](https://zhuanlan.zhihu.com/p/140425363)

## 01 引言

backtrader是目前功能最完善的Python量化回测框架之一，但学起来可能也是最费力的之一，对Python的元编程要求比较高。《**【手把手教你】入门量化回测最强神器backtrader（一）**》简单介绍了backtrader框架的各个组成部分，然后以20日单均线策略为例，展示了策略模块编写和回测系统的运行。本文在该文的基础上，仍以均线策略为例，进一步介绍策略模块中交易日志的编写和策略参数的寻优。

## 02 回测实例

先来回顾一下交易策略模块（Strategy）的构成。交易策略类代码包含参数或函数名如下：

（1）params-全局参数，可选：更改交易策略中变量/参数的值，可用于参数调优。

（2）log：日志，可选：记录策略的执行日志，可以打印出该函数提供的日期时间和txt变量。

（3） init：用于初始化交易策略的类实例的代码。

（4）notify_order，可选：跟踪交易指令（order）的状态。order具有提交，接受，买入/卖出执行和价格，已取消/拒绝等状态。

（5）notify_trade，可选：跟踪交易的状态，任何已平仓的交易都将报告毛利和净利润。

（6）next，必选：制定交易策略的函数，策略模块最核心的部分。

下面仍然以简单均线策略为例，重点介绍参数寻优和交易日志报告。

实现代码如下：

```
#先引入后面可能用到的包（package）
import pandas as pd  
import numpy as np
import tushare as ts 
import matplotlib.pyplot as plt
%matplotlib inline   

#正常显示画图时出现的中文和负号
from pylab import mpl
mpl.rcParams['font.sans-serif']=['SimHei']
mpl.rcParams['axes.unicode_minus']=False
```

## 策略模块编写：

可以与《**【手把手教你】入门量化回测最强神器backtrader（一）**》对比来看，params是全局参数，maperiod是MA均值的长度，默认15天，printlog为打印交易日志，默认不输出结果，策略模块的核心在next（）函数。

```
from datetime import datetime
import backtrader as bt
class MyStrategy(bt.Strategy):
    params=(('maperiod',15),
            ('printlog',False),)

    def __init__(self):
        #指定价格序列
        self.dataclose=self.datas[0].close

        # 初始化交易指令、买卖价格和手续费
        self.order = None
        self.buyprice = None
        self.buycomm = None

        #添加移动均线指标
        self.sma = bt.indicators.SimpleMovingAverage(
                      self.datas[0], period=self.params.maperiod)

    #策略核心，根据条件执行买卖交易指令（必选）
    def next(self):
        # 记录收盘价
        #self.log(f'收盘价, {dataclose[0]}')
        if self.order: # 检查是否有指令等待执行, 
            return
        # 检查是否持仓   
        if not self.position: # 没有持仓
            #执行买入条件判断：收盘价格上涨突破15日均线
            if self.dataclose[0] > self.sma[0]:
                self.log('BUY CREATE, %.2f' % self.dataclose[0])
                #执行买入
                self.order = self.buy()         
        else:
            #执行卖出条件判断：收盘价格跌破15日均线
            if self.dataclose[0] < self.sma[0]:
                self.log('SELL CREATE, %.2f' % self.dataclose[0])
                #执行卖出
                self.order = self.sell()

    #交易记录日志（可省略，默认不输出结果）
    def log(self, txt, dt=None,doprint=False):
        if self.params.printlog or doprint:
            dt = dt or self.datas[0].datetime.date(0)
            print(f'{dt.isoformat()},{txt}')

    #记录交易执行情况（可省略，默认不输出结果）
    def notify_order(self, order):
        # 如果order为submitted/accepted,返回空
        if order.status in [order.Submitted, order.Accepted]:
            return
        # 如果order为buy/sell executed,报告价格结果
        if order.status in [order.Completed]: 
            if order.isbuy():
                self.log(f'买入:\n价格:{order.executed.price},\
                成本:{order.executed.value},\
                手续费:{order.executed.comm}')
                self.buyprice = order.executed.price
                self.buycomm = order.executed.comm
            else:
                self.log(f'卖出:\n价格：{order.executed.price},\
                成本: {order.executed.value},\
                手续费{order.executed.comm}')
            self.bar_executed = len(self) 

        # 如果指令取消/交易失败, 报告结果
        elif order.status in [order.Canceled, order.Margin, order.Rejected]:
            self.log('交易失败')
        self.order = None

    #记录交易收益情况（可省略，默认不输出结果）
    def notify_trade(self,trade):
        if not trade.isclosed:
            return
        self.log(f'策略收益：\n毛收益 {trade.pnl:.2f}, 净收益 {trade.pnlcomm:.2f}')

    #回测结束后输出结果（可省略，默认输出结果）
    def stop(self):
        self.log('(MA均线： %2d日) 期末总资金 %.2f' %
                 (self.params.maperiod, self.broker.getvalue()), doprint=True)
```

下面定义一个主函数，用于对某股票指数（个股）在指定期间进行回测，使用tushare的旧接口获取数据，包含开盘价、最高价、最低价、收盘价和成交量。这里主要以3到30日均线为例进行参数寻优，考察以多少日均线与价格的交叉作为买卖信号能获得最大的收益。

```
def main(code,start,end='',startcash=10000,qts=500,com=0.001):
    #创建主控制器
    cerebro = bt.Cerebro()      
    #导入策略参数寻优
    cerebro.optstrategy(MyStrategy,maperiod=range(3, 31))    
    #获取数据
    df=ts.get_k_data(code,autype='qfq',start=start,end=end)
    df.index=pd.to_datetime(df.date)
    df=df[['open','high','low','close','volume']]
    #将数据加载至回测系统
    data = bt.feeds.PandasData(dataname=df)    
    cerebro.adddata(data)
    #broker设置资金、手续费
    cerebro.broker.setcash(startcash)           
    cerebro.broker.setcommission(commission=com)    
    #设置买入设置，策略，数量
    cerebro.addsizer(bt.sizers.FixedSize, stake=qts)   
    print('期初总资金: %.2f' %                    
    cerebro.broker.getvalue())    
    cerebro.run(maxcpus=1)    
    print('期末总资金: %.2f' % cerebro.broker.getvalue())
```

再定义一个画图函数，对相应股票（指数）在某期间的价格走势和累计收益进行可视化。

```
def plot_stock(code,title,start,end):
    dd=ts.get_k_data(code,autype='qfq',start=start,end=end)
    dd.index=pd.to_datetime(dd.date)
    dd.close.plot(figsize=(14,6),color='r')
    plt.title(title+'价格走势\n'+start+':'+end,size=15)
    plt.annotate(f'期间累计涨幅:{(dd.close[-1]/dd.close[0]-1)*100:.2f}%', xy=(dd.index[-150],dd.close.mean()), 
             xytext=(dd.index[-500],dd.close.min()), bbox = dict(boxstyle = 'round,pad=0.5',
            fc = 'yellow', alpha = 0.5),
             arrowprops=dict(facecolor='green', shrink=0.05),fontsize=12)
    plt.show()
```

以上证综指为例，回测期间为2010-01-01至2020-03-30，期间累计收益率为-15.31%，惨不忍睹。

plot_stock('sh','上证综指','2010-01-01','2020-03-30')

![](img/2baeb27a3f37a3853c10f9ba69866fce.png)

下面分别对3-30日均线进行回测，这里假设指数可以交易，初始资金为100万元，每次交易100股，注意如果指数收盘价乘以100超过可用资金，会出现交易失败的情况，换句话说在整个交易过程中，是交易固定数量的标的，因此仓位的大小跟股价有直接关系。

main('sh','2010-01-01','',1000000,100)

从输出结果不难看出，使用21日均线可以获得最大收益，期末总资金为1106552（初始资金1000000）。

下面分期间进一步回测，考察同一标的，不同区间参数是否有显著差异。将上证综指分为两个期间，2010-2015（期间收益率59.27%），205-2020（期间收益率-44%）。

plot_stock('sh','上证综指','2010-01-01','2015-06-12')

![](img/309547a9ad9d946fa2a465731433116d.png)

main('sh','2010-01-01','2015-06-12',1000000,100)

结果显示是21日均线最优。

plot_stock('sh','上证综指','2015-06-13','')

![](img/a390a5fad3d0c7e922021621891c8d59.png)

main('sh','2015-06-13','',1000000,100)

这一期间指数收益率是-44%，采用均线策略进行交易最后获得的收益也是负的，最好的结果是采用16日均线，亏损相对小些。

再看一下个股情况，以XXX为例：

plot_stock('600000','浦发银行','2010-01-01','2020-03-30')

![](img/e43f7796cf93df21cb63329c91356849.png)

注意，这里初始资金设置为10000，每次交易1000股，这个可以根据自己需要进行设定。从下面结果可以看出整个回测期间收益率没有很高，这个与仓位设定有关，每次交易数量如果设定太大可能导致交易失败，如果设置太小，导致资金没有充分利用，总的收益会比较小。

main('600000','2010-01-01','',10000,1000)

从结果可以看出，xxx的最优参数是14日均线，看来使用均线进行交易，最优参数的选择可能因标的期间选择的不同而存在差异。

下面以继续以xxx为例，输出整个回测过程中的交易日志，即报告买入卖出价格、成本、手续费、策略收益等。

```
# 初始化cerebro回测系统设置                           
cerebro = bt.Cerebro()
#获取数据
df=ts.get_k_data('600000',autype='qfq',start='2010-01-01',end='2020-03-30')
df.index=pd.to_datetime(df.date)
df=df[['open','high','low','close','volume']]
data = bt.feeds.PandasData(dataname=df,                               
                            fromdate=datetime(2010, 1, 1),                               
                            todate=datetime(2020, 3, 30) )
# 加载数据
cerebro.adddata(data) 
# 将交易策略加载到回测系统中
#设置printlog=True，表示打印交易日志log
cerebro.addstrategy(MyStrategy,maperiod=14,printlog=True) 
# 设置初始资本为10,000
cerebro.broker.setcash(10000.0) 
# 设置交易手续费为 0.1%
cerebro.broker.setcommission(commission=0.001) 
#设置买入设置，策略，数量
cerebro.addsizer(bt.sizers.FixedSize, stake=1000)  

#回测结果
cerebro.run()
#获取最后总资金
portvalue = cerebro.broker.getvalue()
#Print out the final result
print(f'总资金: {portvalue:.2f}')
```

## 输出结果为

```
2010-01-25,买入:
价格:6.437,成本:6437.0,手续费:6.437
2010-01-27,SELL CREATE, 6.22
2010-01-28,卖出:
价格：6.193, 成本: 6437.0, 手续费6.193
2010-01-28,策略收益：
毛收益 -244.00, 净收益 -256.63
......
2020-03-09,卖出:
价格：11.0, 成本: 11230.0,手续费11.0
2020-03-09,策略收益：
毛收益 -230.00, 净收益 -252.23
2020-03-30,(MA均线：14日) 期末总资金 13253.89
总资金: 13253.89
```

回测图形（使用matplotlib画的原生图有点丑）

```
%matplotlib inline 
cerebro.plot()
```

![](img/0dc14916940c1f0b10f60c39448efd5c.png)

## 03 结语

backtrader作为目前功能最完善的Python开源框架之一，其Strategy模块具有很高的灵活性和扩展性。本文以单均线交易策略为例，简单介绍了backtrader交易模块（Strategy）中策略参数寻优（params）和交易日志（log）报告的编写方法，希望为大家学习backtrader起到抛砖引玉的作用。下一篇推文将深入介绍backtrader回测系统评价指标和可视化。最后再强调一句，学习没有捷径，要想全面而深入地学习backtrader回测框架，最好的方法是研读其官方文档。

**参考资料：**backtrader官方文档

[https://www.backtrader.com/docu/](https://link.zhihu.com/?target=https%3A//www.backtrader.com/docu/)