<!--yml
category: 交易
date: 2023-09-17 19:51:57
-->

# 量化回测框架Backtrader【3】-核心概念Lines

> 来源：[https://blog.csdn.net/xmy_2002/article/details/116309351](https://blog.csdn.net/xmy_2002/article/details/116309351)

**目录**

[一，什么是Lines](#1%EF%BC%8C%E4%BB%80%E4%B9%88%E6%98%AFLines)

[二，Lines的声明](#2%EF%BC%8CLines%E7%9A%84%E5%A3%B0%E6%98%8E)

[三，Lines的访问](#3%EF%BC%8CLines%E7%9A%84%E8%AE%BF%E9%97%AE)

[四，Lines的长度](#4%EF%BC%8CLines%E7%9A%84%E9%95%BF%E5%BA%A6)

[五，Lines和参数的继承机制](#5%EF%BC%8CLines%E5%92%8C%E5%8F%82%E6%95%B0%E7%9A%84%E7%BB%A7%E6%89%BF%E6%9C%BA%E5%88%B6)

[题外话](#%E9%A2%98%E5%A4%96%E8%AF%9D)

* * *

这讲介绍BackTrader的一个贯穿框架始终的核心概念-lines。

## 一，什么是Lines

我们知道股票，期货这些金融概念都跟一个属性息息相关，那就是时间。而BackTrader的基本功能回测其实说白了就是将一个算法（策略）运行在一个一时间为基本维度的数据（股票，期货等）上进行迭代。时间这种顺序性极强的概念跟现实世界中的直线的概念是很相似的，于是BackTrader将框架中绝大部分的与时间概念相关的类中都包含了*lines*这个成员。

上节讲的数据导入中，导入的每一个数据类别都会生成一条lines，包括时间数据，还是拿之前的代码作为示例：

```
from datetime import datetime
import backtrader as bt
import backtrader.feeds as btfeeds

class SmaCross(bt.SignalStrategy):
    def __init__(self):
        sma1, sma2 = bt.ind.SMA(period=10), bt.ind.SMA(period=30)
        crossover = bt.ind.CrossOver(sma1, sma2)
        self.signal_add(bt.SIGNAL_LONG, crossover)

cerebro = bt.Cerebro()
cerebro.addstrategy(SmaCross)

data = btfeeds.GenericCSVData(
    dataname='stock_data.csv',
    fromdate=datetime(2011, 1, 1),
    todate=datetime(2012, 12, 31),
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=2,
    high=3,
    low=4,    
    close=5,
    volume=9,
    openinterest=-1
)

cerebro.adddata(data)

cerebro.run()
cerebro.plot(iplot=False)
```

在数据导入（代码中的GenericCSVData）时，生成了‘datetime’，‘open’，‘high’，‘low’，‘close’，‘volume’，这六条lines，另外在类SmaCross里面也生成了‘sma1’，‘sma2’以及‘crossover’三条lines。‘sma1’，‘sma2’，‘crossover’是策略用的指标，指标会在之后的章节详细讲解，这边只要了解就好。

## 二，Lines的声明

当你需要开发一个新的指标或者导入新的数据类别（上一讲最后部分）的时候，则必须在该指标中声明一条line来保存产生的指标值或数据类别。

就像添加参数一样，以元组作为类属性进行声明。 不支持字典，因为它们不按照插入顺序存储内容。摘抄上讲最后的例子说明：

```
class GenericCSV_extend(GenericCSVData):

    # 添加change数据
    lines = ('change',)
```

> 注意：如果您将一个字符串当成元组的一个成员，则在字符串后面需要添加逗号，否则，字符串中的每个字母都将被解释为元组的成员。这是Python本身的语法特性

## 三，Lines的访问

在策略或指标模块中需要访问lines来获取需要处理的数据，尽管两者的目的有些不同，策略是使用数据来做出开仓，平仓等操作决策，而指标则是使用数据来生成新的数据供策略使用。

对lines的访问有几种形式：

*   数组下标的方式lines[X]
*   line后面跟X，比如lineX
*   line跟X中间用一个下划线连接，比如line_X

X代表一个数字，以上三种方式都能访问到同一条line，另外还有一种line后面什么都没有就相当于line0

示例如下：

```
class TestStrategy(bt.SignalStrategy):
    def __init__(self):
        print(self.data.line)
        print(self.data.line0)
        print(self.data.line_0)
        print(self.data.lines[0])

    def next(self):
        pass

cerebro = bt.Cerebro()
cerebro.addstrategy(TestStrategy)

data = btfeeds.GenericCSVData(
    dataname='stock_data.csv',
    fromdate=datetime(2011, 1, 1),
    todate=datetime(2012, 12, 31),
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=2,
    high=3,
    low=4,    
    close=5,
    volume=9,
    openinterest=-1
)

cerebro.adddata(data)
cerebro.run()
```

运行之后的输出如下：

> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC57FAC8>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC57FAC8>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC57FAC8>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC57FAC8>

可见四种访问方式都指向同一个LineBuffer对象

还有一种简便的省略可以line访问方式，

```
self.dataX_Y
```

相当于

```
self.datas[X].lines[Y]
```

在上面的例子中，我们都是用数字序号的方式来访问对应的line，这有一个弊端就是不好阅读，data在导入的时候都是带名字，‘close’，‘open’等，那么我们也可以用名字的方式来访问，这样代码阅读起来更好懂一点

```
class TestStrategy(bt.SignalStrategy):
    def __init__(self):
        print(self.data.line)
        print(self.data.close)
        print(self.data.lines.close)
        print(self.data.line1)
        print(self.data.low)
        print(self.data.lines.low)

    def next(self):
        pass
```

将上例中的class TestStrategy(bt.SignalStrategy)替换为这个，运行的输出是这样

> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC6502B0>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC6502B0>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC6502B0>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC650320>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC650320>
> <backtrader.linebuffer.LineBuffer object at 0x000002A5EC650320>

前面三个和后面三个分别指向同一个对象，可见line0就是close，而line1是low。虽然他们的作用一样，一般情况下能用名字尽量用名字，可读性的重要性不言而喻。但有一个例外就是在指标函数的内部，由于指标函数会生成新的line输出，为了避免可能跟新的line同名，可以使用数字索引。

## 四，Lines的长度

就像我开头说的，运行回测相对于将策略从头到尾遍历一遍数据。当回测正在运行时，会有两个长度的概念，一个是过去数据的长度（已经运行过的数据），另一个是总的数据长度。做个比喻就像是爬台阶，每一个台阶就是一个数据，过去的数据就是已经爬完的台阶，脚下的台阶就是当前正在处理的数据，数据总长度就是全部的台阶数。已处理数据长度（爬过的台阶）是动态增长的，而数据总长（总台阶数）是固定的。

这两种长度在运行的过程中都可以获取，

*   python的len方法可以获取运行时的已处理数据长度
*   buflen可以获取数据总长度

示例如下：

```
class TestStrategy(bt.SignalStrategy):
    def __init__(self):
        pass

    def next(self):
        print("已经处理了%d个数据, 总共有%d个数据" % (len(self),self.data.buflen()))

cerebro = bt.Cerebro()
cerebro.addstrategy(TestStrategy)

data = btfeeds.GenericCSVData(
    dataname='stock_data.csv',
    fromdate=datetime(2011, 1, 1),
    todate=datetime(2012, 12, 31),
    nullvalue=0.0,
    dtformat=('%Y%m%d'),
    datetime=1,
    open=2,
    high=3,
    low=4,    
    close=5,
    volume=9,
    openinterest=-1
)

cerebro.adddata(data)
cerebro.run()
```

只复制了前7行输出：

> 已经处理了1个数据, 总共有468个数据
> 已经处理了2个数据, 总共有468个数据
> 已经处理了3个数据, 总共有468个数据
> 已经处理了4个数据, 总共有468个数据
> 已经处理了5个数据, 总共有468个数据
> 已经处理了6个数据, 总共有468个数据
> 已经处理了7个数据, 总共有468个数据

## 五，Lines和参数的继承机制

BackTrader提供了一种元语言来支持参数和lines的声明并且已努力使其与标准Python继承规则兼容。

*参数的继承*

*   支持多重继承
*   基类的参数被继承
*   如果多个基类定义相同的参数，则使用继承列表中最后一个类的默认值
*   如果在子类中重新定义了相同的参数，则新的默认值将取代基类的默认值

*lines的继承*

*   支持多重继承
*   所有基类的lines均被继承。 line被命名的情况下，如果在基类中被多次命名同一名称，则该line只会有一个版本

## 题外话

看过官方文档的小伙伴应该发现了，我的文章有的跟官方文档一致，有的又不一样。这算是我自己的风格吧，这个系列文章我是一边学习一边写的，是我学习的记录，所以我会把自己当时的理解写出来，包括例子也可能会用自己做的例子。官方文档里比较直白，好理解的，我可能会直接翻译引用一下。顺序和内容覆盖范围跟官方文档也不会一样，毕竟不是原文翻译，这样的方式可以比较自由轻松一点，但缺点就是可能错误也会多一些，所以欢迎大家就没看明白或有错误的地方跟我探讨！