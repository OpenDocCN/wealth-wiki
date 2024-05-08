<!--yml
category: 交易
date: 2023-09-17 19:51:47
-->

# 量化回测框架Backtrader【4】-中枢模块Cerebro

> 来源：[https://blog.csdn.net/xmy_2002/article/details/116309364](https://blog.csdn.net/xmy_2002/article/details/116309364)

**目录**

[一, 获取，创建回测所需的各个组件](#%E8%8E%B7%E5%8F%96%EF%BC%8C%E5%88%9B%E5%BB%BA%E5%9B%9E%E6%B5%8B%E6%89%80%E9%9C%80%E7%9A%84%E5%90%84%E4%B8%AA%E7%BB%84%E4%BB%B6)

[二, 执行回测](#%E6%89%A7%E8%A1%8C%E5%9B%9E%E6%B5%8B)

[三, 返回结果](#%E8%BF%94%E5%9B%9E%E7%BB%93%E6%9E%9C)

[四, 调用绘图模块进行绘图](#%E8%B0%83%E7%94%A8%E7%BB%98%E5%9B%BE%E6%A8%A1%E5%9D%97%E8%BF%9B%E8%A1%8C%E7%BB%98%E5%9B%BE)

[五, 回测逻辑](#%E5%9B%9E%E6%B5%8B%E9%80%BB%E8%BE%91)

[结语](#%E7%BB%93%E8%AF%AD)

* * *

这讲会涉及到很多之前没讲过的概念，这些坑暂时先留着，后面的文章中都会慢慢填上。

已经看完前几讲的细心的小伙伴应该能看到，每个完整的例子里都定义了Cerebro这个对象，而且是几乎从头运行到尾。不难看出，这个Cererbro是一个非常重要和关键的模块。

Cerebro这个单词本身的意思是大脑，其实跟这个模块的作用很相符，就是在整个框架中起到统领各方的功能。他会参与到回测从创建到收尾的几乎所有的部分，这其中包括了：

*   创建并管理所有组件，包括原始数据（data feed），策略，观察器，分析器和记录器
*   执行回测
*   返回结果
*   调用绘图模块进行绘图

以下就按这个顺序依次进行说明。

# 一, 获取，创建回测所需的各个组件

**创建Cerebro**

```
cerebro = bt.Cerebro(**kwargs)
```

可以通过参数**kwargs改变运行方式，主要有如下三种运行方式：

预加载（默认值：True）

最通用的模式，预加载数据测试策略

runonce（默认值：True）

并行运行指标，以加快运行速度。 此时策略和观察器是基于事件运行的

实时（默认：False）

实时模式，设置这个模式时，前面两个参数自动设为False

**添加Data feed**

```
cerebro.adddata(data)
```

这里的data就是data feed对象，第二讲已经讲了如何创建它们。也可以添加重采样数据或回放数据

```
cerebro.resampledata(data, timeframe=bt.TimeFrame.Days)
```

或

```
cerebro.replaydatadata(data, timeframe=bt.TimeFrame.Days)
```

数据可以任意添加，也可以三种数据混合添加，数据会以时间进行对齐

**添加策略**

跟添加data feed不同，data feed是实例化的对象，而策略是以类作为参数添加的，它的实例化是在回测执行时发生的。这么设计的原因是为了便于优化策略参数。被添加策略的参数也是以参数的形式添加的。这句话有点拗口，看个例子就明白了

```
cerebro.addstrategy(MyStrategy, myparam1=value1, myparam2=value2)
```

后面的‘myparam1’和‘myparam2’是策略‘MyStrategy’需要的参数，最终会在运行时传过去。

这是不需要优化策略参数时的形式，当需要对策略参数调优时，是下面的形式

```
cerebro.optstrategy(MyStrategy, myparam1=range(10, 20))
```

这条语句将使用myparam1值从10到19运行MyStrategy 10次

**添加其他组件**

这些组件不是必须的，但可以起到额外辅助的作用。

*   addwriter（添加记录器）
*   addanalyzer（添加分线器）
*   addobserver (或 addobservermulti)（添加观察器）

**改变代理**

Cerebro将在backtrader中使用默认代理，但它也可以被覆盖：

```
broker = MyBroker()
cerebro.broker = broker
```

**接收通知**

如果Data feed和/或代理发送通知（或创建通知的存储管理器），则将通过Cerebro.notify_store的方法接收通知。 有三种处理这些通知的方法。

方法一，通过 *addnotifycallback（callback）*将回调添加到Cerebro实例。 回调函数形式是这样的：

```
callback(msg, *args, **kwargs)
```

方法二，重写添加到Cerebro实例的策略子类中的*notify_store*方法：

```
notify_store(self, msg, *args, **kwargs)
```

方法三，不推荐但可行的方式，创建Cerebro子类并覆盖notify_store方法

# 二, 执行回测

只需一条简单的指令

```
result = cerebro.run(**kwargs)
```

参数kwarg都会影响实例化Cerebro时的参数的值。

如果cerebro没有添加任何数据，就会立即退出。

**标准观察器**

执行回测时cerebro自动实例化三个标准观察器

跟踪现金和价值的代理观察器（投资组合）

交易观察器，应显示每笔交易的有效性

买/卖观察器，应在执行操作时记录下来

使用stdstats = False可以禁用它们，这样最后的绘图可以干净一些

# 三, 返回结果

前面说过，策略是在回测执行期间实例化的，Cerebro会返回其在回测期间创建的策略的实例。 因为策略中的所有元素都是可访问的，所以这可以分析他们在回测期间的行为。结果是回测命令直接返回的

```
result = cerebro.run(**kwargs)
```

运行结果返回的结果格式将根据是否使用优化功能而有所不同（一种策略已添加到光学策略中）：

*   每个通过*addstrategy*添加的策略都生成一个列表
*   添加了一个以上策略的*optstrategy*，会生成一个列表的列表。每个子列表会包含一轮策略运行的输出

如果希望将全部的策略作为返回值，请将参数optreturn设置为False。

# 四, 调用绘图模块进行绘图

前提是已经安装了matplotlib模块，运行的命令是

```
cerebro.plot()
```

# 五, 回测逻辑

简单流程如下：

1，转发所有存储通知

2，要求Data feed提供下一条数据，但在版本1.9.0.99中有了新的逻辑：通过对比日期时间将所有的Data feed进行同步，可用的Data feed接下来将提供该日期时间的数据。在新时点没有数据的Data feed仍然提供旧的数据，而有新数据可用的Data feed提供新数据（以及指标的计算）

如果想使用旧的逻辑（将oldsync = True在实例化cerebro时作为参数传入）

插入到系统中的第一个数据的Data feed是datamaster，系统将等待它传递心跳

其他Data feed是datamaster的从属，并且：

> *如果此轮要发送的数据（在日期时间上来说）比由`datamaster`要发送的数据更新，那么这条数据将不会发送
> *由于多种原因，可能会在没有提供新数据的情况下返回

该逻辑是为了可以轻松同步多个Data feed以及具有不同时间范围的Data feed

3，通知有关订单，交易和现金/价值的排队代理通知的策略

4，告诉代理接受排队的订单并使用新数据执行挂单

5，调用该策略的‘*next*’方法，以使该策略运行下一条数据（以及发布可能在经纪人中排队的订单），根据阶段的不同，在满足策略或指标的最短期限要求之前会运行‘*prenext*’或‘*nextstart*’，在内部，这些策略还将调用观察器，指标，分析器和其他活跃的组件

6，告诉记录器将数据写入其目标

**重要：当前回合发出的交易订单将以下一个时刻的价格执行**

# **结语**

Cerero的介绍就到这里，这篇文章基本上是翻译的官方文档，因为很多概念我也还没涉及过，没法自由展开，等全学完了可能会再回来添加一些自己的认识吧。