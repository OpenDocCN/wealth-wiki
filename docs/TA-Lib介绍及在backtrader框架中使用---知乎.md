<!--yml
category: 交易
date: 2023-09-17 20:13:08
-->

# TA-Lib介绍及在backtrader框架中使用 - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/496805737](https://zhuanlan.zhihu.com/p/496805737)

## **一、TA-Lib介绍**

*   **[TA-Lib](https://link.zhihu.com/?target=https%3A//ta-lib.org/)** -- 交易软件开发广泛使用的技术分析lib，包括了200多个技术指标，如MACD, RSI等
*   **[ta-lib for python](https://link.zhihu.com/?target=https%3A//mrjbq7.github.io/ta-lib/)** -- python封装的ta-lib。python封装的ta-lib，使用Cython和Numpy高效实现，比使用SWIG接口的原始版本快2-4倍

## **二、安装**

mac安装方法如下所示：

```
$ brew install ta-lib
$ pip install TA-Lib 
```

如果没用brew安装ta-lib，会报如下错误

```
func.c:256:28: fatal error: ta-lib/ta_libc.h: No such file or directory
compilation terminated.
If you get build errors like this, it typically means that it can't find the underlying TA-Lib library and needs to be installed: 
```

windows、linux参考：[https://mrjbq7.github.io/ta-lib/install.html](https://link.zhihu.com/?target=https%3A//mrjbq7.github.io/ta-lib/install.html)

进python使用`import talib`没有报错的话，表明安装成功。

## **三、常用指标使用**

1.  SMA：简单移动平均线(Simple Moving Average) talib.SMA() 要求数据是numpy.ndarray格式 talib.abstract.SMA() 要求数据是Numpy数组的字典格式

```
import numpy as np
import talib

close = np.random.random(100)
output = talib.SMA(close) # 默认是SMA30
output = talib.SMA(close, timeperiod=5) # SMA5

inputs = {
    'open': np.random.random(100),
    'high': np.random.random(100),
    'low': np.random.random(100),
    'close': np.random.random(100),
    'volume': np.random.random(100)
}
from talib.abstract import *
output = SMA(input_arrays, timeperiod=25) # 默认对close价格计算
output = SMA(input_arrays, timeperiod=25, price='open') # 对open价格计算 
```

2\. EMA：指数移动平均线(Exponential Moving Average)

```
talib.EMA(close)
talib.abstract.EMA(inputs) 
```

3\. MACD：平滑异同移动平均线(Moving Average Convergence / Divergence) 通过三种数据的信号（均线的组合 ）可以判断趋势的力量和方向并确定趋势的转折点

```
talib.MACD(close)
talib.abstract.MACD(inputs) 
```

4\. 支持的指标列表

*   均线指标，如EMA、SMA、WMA等
*   动量指标，如MACD、MOM、RSI等
*   成交量指标，如AD、OBV等
*   易变指标，如ATR、NATR等
*   价格变换，如AVGPRICE、MEDPRICE等
*   循环指标，如HT_DCPERIOD、HT_SINE等
*   模式识别，如CDL2CROWS、CDLHAMMER等
*   统计函数，如VAR,STDDEV, LINEARREG等
*   数学变换，如ACOS、ASIN、CEIL、COS、EXP、LN、SQRT等
*   数学操作，如ADD、DIV、MAX、MULT、SUM等

支持的功能及函数列表如下：

```
import talib
talib.get_functions()
print(len(talib.get_functions()))
talib.get_function_groups()
print(len(talib.get_function_groups())) 
```

## **四、在backtrader中调用TA-Lib库**

**欢迎关注**微信公众号查看&交流。

关注微信公众号：诸葛说talk，获取更多内容。同时还能获取邀请加入量化投资研讨微信群， 与众多量化从业者&爱好者交流、切磋，不错过最新行业发展&技术进展。

## **参考**