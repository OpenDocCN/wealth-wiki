<!--yml
category: 交易
date: 2023-09-17 19:51:28
-->

# 量化回测框架BackTrader【6】-开发策略

> 来源：[https://blog.csdn.net/xmy_2002/article/details/116355048](https://blog.csdn.net/xmy_2002/article/details/116355048)

**目录**

[0，序](#0%EF%BC%8C%E5%BA%8F)

[1，定义一个策略](#1%EF%BC%8C%E5%AE%9A%E4%B9%89%E4%B8%80%E4%B8%AA%E7%AD%96%E7%95%A5)

[2，策略运行时序](#2%EF%BC%8C%E7%AD%96%E7%95%A5%E8%BF%90%E8%A1%8C%E6%97%B6%E5%BA%8F)

[start()](#start%28%29)

[prenext()](#prenext%28%29)

[nextstart()](#nextstart%28%29)

[next()](#next%28%29)

[stop()](#stop%28%29)

[3，操作交易](#3%EF%BC%8C%E6%93%8D%E4%BD%9C%E4%BA%A4%E6%98%93)

[基本交易](#%E5%9F%BA%E6%9C%AC%E4%BA%A4%E6%98%93)

[扩展交易](#%E6%89%A9%E5%B1%95%E4%BA%A4%E6%98%93)

[4，通知回调](#4%EF%BC%8C%E9%80%9A%E7%9F%A5%E5%9B%9E%E8%B0%83)

[5，示例](#5%EF%BC%8C%E7%A4%BA%E4%BE%8B)

* * *

# 0，序

众所周知，对于量化投资来说最核心的部分就是策略。而量化回测框架用来回测的也正是策略的性能。所以对于策略开发的功能支持度，开发的简易程度，是检验一个量化回测框架非常重要的指标。BackTrader对于策略开发的支持还是很完整的，这一讲从如下几个方面来介绍BackTrader的策略开发。

# 1，定义一个策略

BackTrader定义了一个策略的基类-Strategy，所有的策略可以从继承这个基类开始，下面展示一个基本的策略示例

```
class MyStrategy(bt.Strategy):
    params = (('period', 30),)

    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(period=self.p.period)

    def next(self):
        if self.sma > self.data.close:
            pass

        elif self.sma < self.data.close:
            pass
```

该例中的策略继承了Strategy基类，并在__init__(self)中定义了一个移动平均值的指标，定义了一个参数用来设置这个指标。重写了next(self)函数，这个函数很关键，基本都是要重写的，下文会讲到。

# 2，策略运行时序

BackTrader内部运行回测时有一个心跳机制，每个data feed的最小时间间隔（不同的data feed可能会有不同的时间间隔）就是一次心跳，也可以说是一个时间点，所有时间点串在一起就是整个回测周期。

Strategy将整个周期分为了几个时间段，并对每个时间段相应地定义了成员函数，如果需要在该时间段的时间点做一些逻辑处理的话就需要重写它们。接下来分别介绍这些时间段：

### start()

策略开始运行之前，即第一个bar之前

### prenext()

策略准备阶段，主要取决于指标，在start之后，在所有指标能够输出之前。以上面的例子为例，移动均值指标的时长是30个bar，也就是说在前29个bar该指标是不会有输出的，此时策略无法正常运行，所以定义为准备阶段。

### nextstart()

准备阶段结束后的第一个时点，也就是策略正常运行的第一个时点。

### next()

策略的主要阶段，这个阶段，所有的指标都能正常输出，所有的交易也会发生在这个阶段，可以说是策略的主战场。

### stop()

结束阶段，即最后一个bar的之后，可以做些收尾或重置的处理。

通过一个示例可以直观地了解这个时序。

```
import backtrader as bt
import inspect

def show_yourself(self):    
    print("当前是第{}个bar，所处阶段 {}".format(len(self), inspect.stack()[1][3]))

class MyStrategy(bt.Strategy):
    params = (('period', 30),)

    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(period=self.p.period)

    def next(self):
        show_yourself(self)
        if self.sma > self.data.close:
            pass

        elif self.sma < self.data.close:
            pass

    def nextstart(self):
        show_yourself(self)
        pass

    def prenext(self):
        show_yourself(self)
        pass

    def start(self):
        show_yourself(self)
        pass

    def stop(self):
        show_yourself(self)
        pass

cerebro = bt.Cerebro()
data = bt.feeds.GenericCSVData(
    dataname='CU1811.csv',
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=4,
    high=5,
    low=6,    
    close=7,
    volume=11,
    openinterest=-1
)

cerebro.adddata(data)
#cerebro.broker.set_cash(1000000)
cerebro.addstrategy(MyStrategy, period=30)

cerebro.run()
#cerebro.plot(style='bar',iplot=False)
```

所用的数据是上一讲从Tushare下载的期货日线数据，定义了一个基本策略并重写了上面介绍的成员函数，这些函数都不做实际的处理，只是打印自己所在时点以及自己的函数名。输出如下

> ```
> 当前是第0个bar，所处阶段 start
> 当前是第1个bar，所处阶段 prenext
> ```
> 
> * * *
> 
> ```
> 当前是第29个bar，所处阶段 prenext
> 当前是第30个bar，所处阶段 nextstart
> 当前是第31个bar，所处阶段 next
> ```
> 
> * * *
> 
> ```
> 当前是第242个bar，所处阶段 next
> 当前是第242个bar，所处阶段 stop 
> ```

省去了中间的重复部分。可以看到输出完全吻合之前的描述。

# 3，操作交易

BackTrader支持两个基本的交易操作买（Buy）和卖（Sell），以及由这两个基本交易扩展的一系列其他 交易。

### 基本交易

Buy和Sell其实就是两个相反的操作，它们的参数是一样。下面介绍主要参数

*   data：由哪个data feed创建的订单。如果没有，则将使用系统中的默认data feed，self.datas [0]或self.data0（也称为self.data）
*   size：交易的数量（正数）。如果没有，则将通过getsizer()检索到的sizer实例用于确定数量大小
*   price：交易的价格，当交易类型是“market”或“close”，可以接受“none”由市场决定价格，当交易类型是“Limit”, “Stop”和“StopLimit”时，价格此时只是作为触发值
*   plimit：仅适用于“StopLimit”交易类型。一旦触发了止损（市场价格触及price参数），以这个值设定限价订单的价格
*   exectype：交易类型，“market”（默认值）：市场订单将以下一个可用价格执行；在回测中，它将是下一个bar的开盘价。“Limit”：限价交易，只能以给定价格或更优的价格执行的订单。“Stop”：止损单，以价格触发并像“market”一样执行的订单。“StopLimit”：止损限价交易，以“Price”触发并以“plimit”作为限价执行的订单。
*   valid：有效时间。“None”：生成的订单不会过期（也称为“有效至取消”），一直保留在市场中直到成功交易或被取消；“`datetime.datetime`” 或 “`datetime.date`”：有效至某个特点时间；“Order.DAY” 或 “0” or “timedelta()”：日间订单，只在当天有效；数值：假定为与matplotlib编码中的日期时间相对应的值（由backtrader使用的日期时间），并将用于生成在该时间之前有效的订单（有效截止日期）
*   tradeid：这是BackTrader的内部值，用于跟踪同一资产上的重叠交易。通知订单状态更改时，此Tradeid将发送回给策略
*   **kwargs：其他broker所需的参数。 BackTrader将把kwargs传递给被创建的order对象

### 扩展交易

*   Close：平仓，清空所有的多单或空单
*   order_target_size：平衡仓位至目标数量，如果现数量超过目标数量就执行卖，反之则买
*   order_target_value：平衡仓位至目标价值
*   order_target_percent：平衡仓位至目标价值百分比
*   buy_bracket：止盈止损组合多单，会同时生成三个order，一个低价的止损卖单，一个中间价的限价买单以及一个高价的止盈限价卖单。相比基本交易的Buy和Sell，参数多了两组参数【stopprice，stopexec，stopargs】和【limitprice，limitexec，limitargs】（官方文档以及源码注释中把“limitexec”误写为“stopexec”了），分别用来配置止损订单和止盈订单。如果不想生成止损单或止盈单也可以通过设置参数stopexec=none或limitexec=none。返回的三个order顺序是[买单, 止损单, 止盈单]
*   sell_bracket：止盈止损组合空单，参数跟止盈止损组合多单一样
*   cancel(self, order)：取消还未被处理的order

# 4，通知回调

BackTrader对一些状态改变的通知是以回调的方式实现的，需要重写对回调函数的实现。目前支持以下通知：

*   notify_order(order)：每次订单状态改变会触发回调
*   notify_trade(trade)：任何开仓/更新/平仓交易的通知
*   notify_cashvalue(cash, value) ：通知当前现金和投资组合
*   notify_store(msg, *args, **kwargs)：关于存储的通知
*   notify_data(self, data, status, *args, **kwargs):：关于数据的通知
*   notify_timer(self, timer, when, *args, **kwargs)：定时器通知，定时器可以通过成员函数add_timer（）添加

# 5，示例

例1，在前面的例子的基础上我们添加基本的买卖操作以及订单，交易和资产的通知

```
import backtrader as bt
import inspect

class MyStrategy(bt.Strategy):
    params = (('period', 30),)

    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(period=self.p.period)

    def notify_order(self, order):
        print("\033[31mbar序：{}，订单通知 size:{},price:{},pricelimit:{},exectype{},tradeid:{},status:{}\033[0m"
              .format(len(self),order.size,order.price,order.pricelimit,order.exectype,order.tradeid,order.Status[order.status]))

    def notify_trade(self, trade):
        print("\033[32mbar序：{}，交易通知 size:{},price:{},value:{},tradeid:{},status:{}\033[0m"
              .format(len(self),trade.size,trade.price,trade.value,trade.tradeid,trade.status_names[trade.status]))

    def notify_cashvalue(self, cash, value):
        print("\033[33mbar序：{}，资产通知 cash:{},value:{}\033[0m"
              .format(len(self),cash,value))

    def next(self):
        print("bar序：{}，next sma={},close={}".format(len(self),self.sma[0],self.data.close[0]))
        if self.sma > self.data.close:
            print("It's time to buy!")
            self.buy()

        elif self.sma < self.data.close:
            print("It's time to close!")
            self.close()

cerebro = bt.Cerebro()
data = bt.feeds.GenericCSVData(
    dataname='CU1811.csv',
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=4,
    high=5,
    low=6,    
    close=7,
    volume=11,
    openinterest=-1
)

cerebro.adddata(data)
cerebro.broker.set_cash(1000000)
cerebro.addstrategy(MyStrategy, period=30)

cerebro.run()
#cerebro.plot(style='bar',iplot=False)
```

该例中，策略非常简单，当移动平均值大于close值时执行买入操作，参数都是默认，当移动平均值小于close值时执行平仓操作。输出如下：

> ```
> bar序：1，资产通知 cash:1000000.0,value:1000000.0
> bar序：2，资产通知 cash:1000000.0,value:1000000.0
> bar序：3，资产通知 cash:1000000.0,value:1000000.0
> ```
> 
> * * *
> 
> ```
> bar序：30，资产通知 cash:1000000.0,value:1000000.0
> bar序：30，next sma=54738.0,close=56340.0
> It's time to close!
> bar序：31，资产通知 cash:1000000.0,value:1000000.0
> bar序：31，next sma=54823.0,close=57230.0
> It's time to close! 
> ```
> 
> * * *
> 
> ```
> bar序：44，next sma=55286.666666666664,close=54600.0
> It's time to buy!
> bar序：45，订单通知 size:1,price:None,pricelimit:None,exectype0,tradeid:0,status:Submitted
> bar序：45，订单通知 size:1,price:None,pricelimit:None,exectype0,tradeid:0,status:Accepted
> bar序：45，订单通知 size:1,price:None,pricelimit:None,exectype0,tradeid:0,status:Completed
> bar序：45，交易通知 size:1,price:54890.0,value:54890.0,tradeid:0,status:Open
> bar序：45，资产通知 cash:945110.0,value:1000040.0
> bar序：45，next sma=55357.666666666664,close=54930.0
> ```
> 
> * * *
> 
> ```
> bar序：62，next sma=55060.666666666664,close=53050.0
> It's time to buy!
> bar序：63，订单通知 size:1,price:None,pricelimit:None,exectype0,tradeid:0,status:Submitted
> bar序：63，订单通知 size:1,price:None,pricelimit:None,exectype0,tradeid:0,status:Margin
> bar序：63，资产通知 cash:20430.0,value:983610.0
> bar序：63，next sma=54957.333333333336,close=53510.0
> ```
> 
> * * *
> 
> ```
> bar序：66，next sma=54765.333333333336,close=55000.0
> It's time to close!
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype0,tradeid:0,status:Submitted
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype0,tradeid:0,status:Accepted
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype0,tradeid:0,status:Completed
> bar序：67，交易通知 size:0,price:54420.555555555555,value:0.0,tradeid:0,status:Closed
> bar序：67，资产通知 cash:1007010.0,value:1007010.0
> bar序：67，next sma=54736.333333333336,close=55040.0
> ```

这是部分输出，可以得出，1，notify_cashvalue会在每个bar被调到；2，从第3段可以看出订单的发出发生在满足条件的后面一个bar，因为采用了默认的“market”交易类型；3，在同一个bar时点内，顺序是订单通知》交易通知》资产通知》next函数；4，从第4段可以看出当现金不够是，order会进入Margin状态；5，在已经持有多单时再追加多单不会有交易通知

例2，一次只买一手太少了，我们试一次另一个控制仓位比例的函数order_target_percent，并添加一些控制

```
import backtrader as bt
import inspect

class MyStrategy(bt.Strategy):
    params = (('period', 30),)

    def __init__(self):
        self.sma = bt.indicators.SimpleMovingAverage(period=self.p.period)

    def notify_order(self, order):
        print("\033[31mbar序：{}，订单通知 size:{},price:{},pricelimit:{},exectype:{},tradeid:{},status:{}\033[0m"
              .format(len(self),order.size,order.price,order.pricelimit,order.ExecTypes[order.exectype],order.tradeid,order.Status[order.status]))

        if not order.alive():
            self.order = None

    def notify_trade(self, trade):
        print("\033[32mbar序：{}，交易通知 size:{},price:{},value:{},tradeid:{},status:{}\033[0m"
              .format(len(self),trade.size,trade.price,trade.value,trade.tradeid,trade.status_names[trade.status]))

    '''def notify_cashvalue(self, cash, value):
        print("\033[33mbar序：{}，资产通知 cash:{},value:{}\033[0m"
              .format(len(self),cash,value))'''

    def start(self):
        self.order = None

    def next(self):
        #print("bar序：{}，next sma={},close={}".format(len(self),self.sma[0],self.data.close[0]))
        if self.order:
            return

        if not self.position:
            if self.sma > self.data.close:
                self.order = self.order_target_percent(target=1.0, price=self.data.close[0]*0.99,exectype=bt.Order.Limit)
                #self.order = self.buy(price=self.data.close[0]*0.99,exectype=bt.Order.Limit)
        else:
            if self.sma < self.data.close:
                self.close()

cerebro = bt.Cerebro()
data = bt.feeds.GenericCSVData(
    dataname='CU1811.csv',
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=4,
    high=5,
    low=6,    
    close=7,
    volume=11,
    openinterest=-1
)

cerebro.adddata(data)
cerebro.broker.set_cash(1000000)
cerebro.addstrategy(MyStrategy, period=30)

cerebro.run()
```

添加了一个order的变量，保存order状态，当有order被挂起时不发起新的order；添加了在“self.position”的判断，当已经开了仓不会再重复开仓；用“order_target_percent”替换了“buy”，并且满仓买入，订单类型也改为限价单；去掉了多余的打印信息。以下是输出

> ```
> bar序：45，订单通知 size:18,price:54054.0,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：45，订单通知 size:18,price:54054.0,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：49，订单通知 size:18,price:54054.0,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：49，交易通知 size:18,price:54054.0,value:972972.0,tradeid:0,status:Open
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Submitted
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Accepted
> bar序：67，订单通知 size:-18,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Completed
> bar序：67，交易通知 size:0,price:54054.0,value:0.0,tradeid:0,status:Closed
> bar序：70，订单通知 size:19,price:53341.2,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：70，订单通知 size:19,price:53341.2,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：71，订单通知 size:19,price:53341.2,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：71，交易通知 size:19,price:53341.2,value:1013482.7999999999,tradeid:0,status:Open
> bar序：104，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Submitted
> bar序：104，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Accepted
> bar序：104，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Completed
> bar序：104，交易通知 size:0,price:53341.2,value:0.0,tradeid:0,status:Closed
> bar序：116，订单通知 size:19,price:51153.3,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：116，订单通知 size:19,price:51153.3,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：154，订单通知 size:19,price:51153.3,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：154，交易通知 size:19,price:51153.3,value:971912.7000000001,tradeid:0,status:Open
> bar序：181，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Submitted
> bar序：181，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Accepted
> bar序：181，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Completed
> bar序：181，交易通知 size:0,price:51153.3,value:0.0,tradeid:0,status:Closed
> bar序：182，订单通知 size:19,price:49113.9,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：182，订单通知 size:19,price:49113.9,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：184，订单通知 size:19,price:49113.9,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：184，交易通知 size:19,price:49113.9,value:933164.1,tradeid:0,status:Open
> bar序：206，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Submitted
> bar序：206，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Accepted
> bar序：206，订单通知 size:-19,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Completed
> bar序：206，交易通知 size:0,price:49113.9,value:0.0,tradeid:0,status:Closed
> bar序：207，订单通知 size:20,price:47935.8,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：207，订单通知 size:20,price:47935.8,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：207，订单通知 size:20,price:47935.8,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：207，交易通知 size:20,price:47935.8,value:958716.0,tradeid:0,status:Open
> bar序：209，订单通知 size:-20,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Submitted
> bar序：209，订单通知 size:-20,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Accepted
> bar序：209，订单通知 size:-20,price:None,pricelimit:None,exectype:Market,tradeid:0,status:Completed
> bar序：209，交易通知 size:0,price:47935.8,value:0.0,tradeid:0,status:Closed
> bar序：233，订单通知 size:20,price:49153.5,pricelimit:None,exectype:Limit,tradeid:0,status:Submitted
> bar序：233，订单通知 size:20,price:49153.5,pricelimit:None,exectype:Limit,tradeid:0,status:Accepted
> bar序：233，订单通知 size:20,price:49153.5,pricelimit:None,exectype:Limit,tradeid:0,status:Completed
> bar序：233，交易通知 size:20,price:49153.5,value:983070.0,tradeid:0,status:Open
> ```

可以看出订单不会马上成交，会等到设置的价格出现时才会成交，不会像上个例子一样频繁发出订单，更加合理