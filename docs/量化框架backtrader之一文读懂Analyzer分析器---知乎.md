<!--yml
category: 交易
date: 2023-09-17 20:12:39
-->

# 量化框架backtrader之一文读懂Analyzer分析器 - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/527564729](https://zhuanlan.zhihu.com/p/527564729)

诺贝尔奖获得者威廉夏普

## **简介**

策略绩效评价是量化交易很重要的一环，投资不仅需要了解策略的收益率， 也需要了解策略的风险。backtrader提供多种analyzer分析器，可以输出多种绩效指标，用来分析策略回测的效果。

## **使用方法**

```
cerebro.addanalyzer(bt.analyzers.AnnualReturn, _name='AnnualReturn')
cerebro.addanalyzer(bt.analyzers.SharpeRatio, riskfreerate=0.003, annualize=True, _name='SharpeRatio')
cerebro.addanalyzer(bt.analyzers.DrawDown, _name='DrawDown')

strats = cerebro.run()

# 第一个策略
strat = strats[0]

print("--------------- AnnualReturn -----------------")
print(strat.analyzers.AnnualReturn.get_analysis())
print("--------------- SharpeRatio -----------------")
print(strat.analyzers.SharpeRatio.get_analysis())
print("--------------- DrawDown -----------------")
print(strat.analyzers.DrawDown.get_analysis()) 
```

AnnualReturn代表年化收益率分析器**，** SharpeRatio代表夏普比率分析器， DrawDown代表回测分析器。各个分析器的结果通常以 OrderedDict 字典的形式返回，如下所示，可以通过 keys 取需要的 values， 如strat.analyzers.SharpeRatio.get_analysis()['sharperatio'] 。

```
--------------- AnnualReturn -----------------
OrderedDict([(2010, 0.0055023006637382466), (2011, 0.0015355496623354892), (2012, 0.004218277850025043), (2013, 0.09220659214070048), (2014, 0.05213380413050861), (2015, 0.012115724348371826), (2016, -0.017901621371985033), (2017, 0.06763449420829182), (2018, 0.01391412496311406), (2019, 0.05472002239157425), (2020, 1.8864865026259587), (2021, 0.36892175167157526), (2022, -0.2711252801992251)])
--------------- SharpeRatio -----------------
OrderedDict([('sharperatio', 0.3360518820606975)])
--------------- DrawDown -----------------
AutoOrderedDict([('len', 145), ('drawdown', 35.66222565509548), ('moneydown', 21054.40185546875), ('max', AutoOrderedDict([('len', 648), ('drawdown', 40.770090001923634), ('moneydown', 24070.00244140625)]))]) 
```

可以看出来， Analyzer分析器输出的结果都是一些数值，并没有绘图。以`SharpeRatio` 为例，夏普比率计算后是一个数值0.3360518820606975。实际上，`Analyzers` 不包含lines对象，这也意味着analyzer对象对内存非常友好，不会占用很多的内存。

*   进一步说明：一个analyzer分析器分析的是一个策略的绩效表现而不是整个系统的表现。 通过addanalyzer函数添加的分析器类，在`cerebro.run` 运行时，每个分析器实例会被链接到各个策略， 如果回测包含3个策略，那么同一个分析器将有3个实例被创建，每个实例会被链接到不同的策略
*   一些`Analyzer` 分析器对象会使用其他analyzers分析器来完成任务，比如`SharpeRatio` 使用了`TimeReturn` 分析器的输出来进一步计算，但这些依赖用户并不可见

## **backtrader自带的Analyzers分析器**

*   **AnnualReturn：**年化收益率
*   **Calmar：** 卡玛比率
*   **DrawDown：**回撤
*   **TimeDrawDown：** 指定时间粒度下的回撤
*   **GrossLeverage：** 总杠杆
*   **PositionsValue：** 持仓价值
*   **PyFolio：** 生成数据兼容pyfolio
*   **LogReturnsRolling：** 滚动log收益率
*   **PeriodStats：** 给定时段下的基本统计信息
*   **Returns：** 用对数法计算总、平均、复合、年化收益率
*   **SharpeRatio：** 夏普比率
*   **SharpeRatio_A：** 年化夏普比率
*   **SQN：**系统质量数System Quality Number
*   **TimeReturn：**不同时段的收益率
*   **TradeAnalyzer：**交易统计信息，如获胜、失败次数等
*   **Transactions：** 每笔交易的标的、价格、数量等信息
*   **VWR：** variability weighted return，波动率加权的收益率

常用的Analyzer分析器有：AnnualReturn， DrawDown， PyFolio， SharpeRatio， TimeReturn。

## **新建Analyzer分析器**

如果backtrader自带的分析器无法满足需求，可以自建Analyzer分析器。以`SharpeRatio` 为例，参考代码如下。夏普比例是最常用的绩效评价指标之一，其反映的是单位波动贡献的超额收益率，越大越好。夏普比例等于策略相对无风险收益率的超额收益率除以收益率标准差。

```
import backtrader as bt

from backtrader.utils.py3 import map
from backtrader.mathsupport import average, standarddev

class SharpeRatio(bt.Analyzer):
    params = (
           ('timeframe', bt.TimeFrame.Years), 
           ('riskfreerate', 0.01),
    )

    def __init__(self):
        super(SharpeRatio, self).__init__()
        self.anret = bt.analyzers.AnnualReturn()

    # analyzer与策略一样，都是从第0根bar开始运行
    def start(self):
        pass

    def prenext(self):
        pass

    def nextstart(self):
        pass

    def next(self):
        pass

    # 一般对策略整体的评价指标是在策略结束后开始计算的
    def stop(self):
        retfree = [self.p.riskfreerate] * len(self.anret.rets)
        retavg = average(list(map(operator.sub, self.anret.rets, retfree)))
        retdev = standarddev(self.anret.rets)

        self.ratio = retavg / retdev

    # Analyzer类特有方法，返回包含分析结果的类dict对象
    def get_analysis(self):
        return dict(sharperatio=self.ratio)

    # 支持与策略一样的信息打印函数
    def notify_order(self, order):
        pass

    def notify_trade(self, trade):
        pass

    def notify_cashvalue(self, cash, value):
        pass

    def notify_fund(self, cash, value, fundvalue, shares):
        pass 
```

从上述代码可以看出，尽管`Analyzer` 对象没有Lines对象且并不需要在lines上迭代，但它们跟策略一样遵守相同的运行模式，当然也有`get_analysis` 这种特有的方法用来返回分析结果。

## **在backtrader中使用PyFolio**

```
cerebro.addanalyzer(bt.analyzers.PyFolio)
strats = cerebro.run()
# retrieve the 1st strategy
strat0 = strats[0]
pyfolio = strats.analyzers.getbyname('pyfolio')
returns, positions, transactions, gross_lev = pyfoliozer.get_pf_items()

import pyfolio as pf
pf.create_full_tear_sheet(returns) 
```

`pyfolio` 在*Jupyter Notebook外也可以运行，但在Jupyter里面运行支持的最好。*

## **跟基线benchmark对比**

```
import backtrader as bt

cerebro = bt.Cerebro()

spypd = bt.feeds.PandasData(dataname=spydata, name="spy")
cerebro.adddata(spypd)

cerebro.addanalyzer(bt.analyzers.TimeReturn, timeframe=bt.TimeFrame.Years, _name='TimeReturn')
cerebro.addanalyzer(bt.analyzers.TimeReturn, timeframe=bt.TimeFrame.Years, data=spypd, _name='BenchTimeReturn')

strats = cerebro.run()

time_return = strat.analyzers.getbyname('TimeReturn').get_analysis()
bench_time_return = strat.analyzers.getbyname('BenchTimeReturn').get_analysis()

print("--------------- TimeReturn -----------------")
print(time_return)
print("--------------- BenchTimeReturn -----------------")
print(bench_time_return) 
```

输出日志：

```
--------------- TimeReturn -----------------
OrderedDict([(datetime.date(2009, 12, 31), 0.0), (datetime.date(2010, 12, 31), 0.0055023006637382466), (datetime.date(2011, 12, 31), 0.0015355496623354892), (datetime.date(2012, 12, 31), 0.004218277850025043), (datetime.date(2013, 12, 31), 0.09220659214070048), (datetime.date(2014, 12, 31), 0.05213380413050861), (datetime.date(2015, 12, 31), 0.012115724348371826), (datetime.date(2016, 12, 31), -0.017901621371985033), (datetime.date(2017, 12, 31), 0.06763449420829182), (datetime.date(2018, 12, 31), 0.01391412496311406), (datetime.date(2019, 12, 31), 0.05472002239157425), (datetime.date(2020, 12, 31), 1.8864865026259587), (datetime.date(2021, 12, 31), 0.36892175167157526), (datetime.date(2022, 12, 31), -0.2711252801992251)])
--------------- SpyTimeReturn -----------------
OrderedDict([(datetime.date(2009, 12, 31), -0.011793865755532318), (datetime.date(2010, 12, 31), 0.12840988195524994), (datetime.date(2011, 12, 31), -0.001988071570576566), (datetime.date(2012, 12, 31), 0.134741065036728), (datetime.date(2013, 12, 31), 0.2968892471880906), (datetime.date(2014, 12, 31), 0.11289182180470925), (datetime.date(2015, 12, 31), -0.008124930541476227), (datetime.date(2016, 12, 31), 0.09643402233275422), (datetime.date(2017, 12, 31), 0.19384416771302204), (datetime.date(2018, 12, 31), -0.06347893319524989), (datetime.date(2019, 12, 31), 0.28785206349908), (datetime.date(2020, 12, 31), 0.16162313396749006), (datetime.date(2021, 12, 31), 0.2703540848726258), (datetime.date(2022, 12, 31), -0.13563244077211734)]) 
```

注意不是所有Analyzers分析器都支持Benchmark对比。

## **结论 & 交流**

关注微信公众号：诸葛说talk，获取更多内容。同时还能获取邀请加入投资交流群、量化投资研讨群， 与众多投资爱好者、量化从业者、技术大牛一起交流、切磋，快速提升自己的投资水平。

写文章不易，觉得本文对你有帮助的话，帮忙点个赞吧。

## **参考**