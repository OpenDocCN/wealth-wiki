<!--yml
category: 交易
-->

# 【Quant102】 经典技术指标 Pandas 实现（第一部分）

## 双均线策略

> 假设你是个高级程序员和量化研究员，编写函数实现双均线策略。函数接受数据帧`df`，较短均线的列名称`short_col`和较长均线的列名称`long_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。


```py
def dual_moving_average_strategy(df, short_col='sma_short', long_col='sma_long', inplace=False):
    """
    实现双均线策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame。
    short_col : str
        短周期均线的列名。
    long_col : str
        长周期均线的列名。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    if not inplace:
        df = df.copy()

    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：短周期均线由下向上穿越长周期均线
    df.loc[(df[short_col] > df[long_col]) & (df[short_col].shift(1) <= df[long_col].shift(1)), 'signal'] = 1
    
    # 产生卖出信号：短周期均线由上向下穿越长周期均线
    df.loc[(df[short_col] < df[long_col]) & (df[short_col].shift(1) >= df[long_col].shift(1)), 'signal'] = -1
    
    return df
```

## 布林带指标

> 假设你是个高级程序员和量化研究员，编写函数实现布林带指标。函数接受数据帧`df`，中布林带的列名称`mid_col`，上布林带列名称`upper_col`，下布林带列名称`lower_col`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def bollinger_bands(
	df, mid_col='mid_band', 
	upper_col='upper_band', 
	lower_col='lower_band', 
	window=20, num_std=2, 
	inplace=False
):
    """
    计算布林带指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    window : int, optional
        计算简单移动平均线SMA的窗口大小，默认为20。
    num_std : int, optional
        计算布林带通道时使用的标准差倍数，默认为2。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含布林带指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算中轨（简单移动平均线SMA）
    df['mid_band'] = df['close'].rolling(window=window).mean()
    
    # 计算价格的标准差
    std = df['close'].rolling(window=window).std()
    
    # 计算布林带上轨
    df['upper_band'] = df['mid_band'] + (std * num_std)
    
    # 计算布林带下轨
    df['lower_band'] = df['mid_band'] - (std * num_std)
    
    return df

```

## 布林带策略

> 假设你是个高级程序员和量化研究员，编写函数实现布林带策略。函数接受数据帧`df`，中轨的列名称`mid_col`，上轨列名称`upper_col`，下轨列名称`lower_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def bollinger_bands_strategy(df, mid_col='mid_band', upper_col='upper_band', lower_col='lower_band', inplace=False):
    """
    实现布林带策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'mid_col', 'upper_col', 'lower_col'列。
    mid_col : str
        中轨（通常是简单移动平均线SMA）的列名。
    upper_col : str
        上轨的列名。
    lower_col : str
        下轨的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：价格跌破下轨
    df.loc[df['close'] < df[lower_col], 'signal'] = 1
    
    # 产生卖出信号：价格突破上轨
    df.loc[df['close'] > df[upper_col], 'signal'] = -1
    
    return df
```

## MACD 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 MACD 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def macd(df, dea_col='dea', dif_col='dif', hist_col='macd_hist', fast_window=12, slow_window=26, signal_window=9, inplace=False):
    """
    计算MACD指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    fast_window : int, optional
        快速EMA的窗口大小，默认为12。
    slow_window : int, optional
        慢速EMA的窗口大小，默认为26。
    signal_window : int, optional
        信号线的窗口大小，默认为9。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含MACD指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算快速EMA
    fast_ema = df['close'].ewm(span=fast_window, adjust=False).mean()
    
    # 计算慢速EMA
    slow_ema = df['close'].ewm(span=slow_window, adjust=False).mean()
    
    # 计算MACD线
    df[dea_col] = fast_ema - slow_ema
    
    # 计算信号线
    df[dif_col] = df[dea_col].ewm(span=signal_window, adjust=False).mean()
    
    # 计算MACD柱
    df[hist_col] = df[dea_col] - df[dif_col]
    
    return df
```

## MACD 策略

> 假设你是个高级程序员和量化研究员，编写函数实现MACD策略。函数接受数据帧`df`，DEA列名称`dea_col`，DIF列名称`dif_col`，柱状图列名称`hist_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def macd_strategy(df, dea_col='dea', dif_col='dif', hist_col='macd_hist', inplace=False):
    """
    实现MACD策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含MACD指标数据的DataFrame，必须包含'dea_col', 'dif_col', 'hist_col'列。
    dea_col : str
        DEA（信号线）的列名。
    dif_col : str
        DIF（MACD线）的列名。
    hist_col : str
        MACD柱状图的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：DIF上穿DEA
    df.loc[(df[dif_col] > df[dea_col]) & (df[dif_col].shift(1) <= df[dea_col].shift(1)), 'signal'] = 1
    
    # 产生卖出信号：DIF下穿DEA
    df.loc[(df[dif_col] < df[dea_col]) & (df[dif_col].shift(1) >= df[dea_col].shift(1)), 'signal'] = -1
    
    return df
```

## RSI 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 RSI 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def rsi(df, rsi_col='rsi', window=14, inplace=False):
    """
    计算RSI指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    window : int, optional
        RSI指标的窗口大小，默认为14。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含RSI指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算价格变动
    chg = df['close'].diff()
    
    # 计算上涨和下跌的平均值
    up_avg = chg.where(chg > 0).rolling(window=window).mean()
    down_avg = -chg.where(chg < 0).rolling(window=window).mean()
    
    # 防止除以零
    up_avg.fillna(0, inplace=True)
    down_avg.fillna(0, inplace=True)
    
    # 计算RSI
    df[rsi_col] = 100 - (100 / (1 + (up_avg / down_avg)))
    
    return df
```

## RSI 策略

> 假设你是个高级程序员和量化研究员，编写函数实现RSI策略。函数接受数据帧`df`，RSI列名称`rsi_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def rsi_strategy(df, rsi_col='rsi', inplace=False):
    """
    实现RSI策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含RSI指标数据的DataFrame，必须包含'rsi_col'列。
    rsi_col : str
        RSI指标的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：RSI低于30
    df.loc[df[rsi_col] < 30, 'signal'] = 1
    
    # 产生卖出信号：RSI高于70
    df.loc[df[rsi_col] > 70, 'signal'] = -1
    
    return df
```

## KDJ 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 KDJ 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def kdj(df, k_col='K', d_col='D', j_col='J', n=9, m1=3, m2=3, inplace=False):
    """
    计算KDJ指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    n : int, optional
        计算KDJ指标的时间窗口，默认为9。
    m1 : int, optional
        计算D线的时间窗口，默认为3。
    m2 : int, optional
        计算J线的参数，默认为3。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含KDJ指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算最小值和最大值
    low_min = df['low'].rolling(window=n).min()
    high_max = df['high'].rolling(window=n).max()
    
    # 计算K值
    df[k_col] = (df['close'] - low_min) / (high_max - low_min) * 100
    
    # 计算D值
    df[d_col] = df[k_col].rolling(window=m1).mean()
    
    # 计算J值
    df[j_col] = m2 * df[d_col] - df[k_col].rolling(window=m2).mean()
    
    return df
```

## KDJ 策略

> 假设你是个高级程序员和量化研究员，编写函数实现KDJ策略。函数接受数据帧`df`，K/D/J列名称`k_col`、`d_col`、`j_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def kdj_strategy(df, k_col='K', d_col='D', j_col='J', inplace=False):
    """
    实现KDJ策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含KDJ指标数据的DataFrame，必须包含'k_col', 'd_col', 'j_col'列。
    k_col : str
        K值的列名。
    d_col : str
        D值的列名。
    j_col : str
        J值的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：K值上穿D值
    df.loc[(df[k_col] > df[d_col]) & (df[k_col].shift(1) <= df[d_col].shift(1)), 'signal'] = 1
    
    # 产生卖出信号：K值下穿D值
    df.loc[(df[k_col] < df[d_col]) & (df[k_col].shift(1) >= df[d_col].shift(1)), 'signal'] = -1
    
    return df
```

## CCI 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 CCI 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def cci(df, cci_col='cci', n=20, inplace=False):
    """
    计算CCI指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    n : int, optional
        CCI指标的时间窗口，默认为20。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含CCI指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算移动平均值
    sma = df['close'].rolling(window=n).mean()
    
    # 计算标准差
    std = df['close'].rolling(window=n).std()
    
    # 计算CCI
    df[cci_col] = (df['close'] - sma) / (0.015 * std)
    
    return df
```

## CCI 策略

> 假设你是个高级程序员和量化研究员，编写函数实现CCI策略。函数接受数据帧`df`，CCI列名称`cci_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def cci_strategy(df, cci_col='cci', inplace=False):
    """
    实现CCI策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含CCI指标数据的DataFrame，必须包含'cci_col'列。
    cci_col : str
        CCI指标的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：CCI低于-100
    df.loc[df[cci_col] < -100, 'signal'] = 1
    
    # 产生卖出信号：CCI高于+100
    df.loc[df[cci_col] > 100, 'signal'] = -1
    
    return df
```

## OBV 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 OBV 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def obv(df, obv_col='obv', inplace=False):
    """
    计算OBV指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含OBV指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
	# 计算收盘价差异的符号
    chg = df['close'].diff()
	sgn = np.sign(chg)
	sgn.iloc[0] = 0
	# 计算OBV
	df[obv_col] = (df['volume'] * sgn).cumsum()
    
    return df
```

## OBV 策略

> 假设你是个高级程序员和量化研究员，编写函数实现OBV策略。函数接受数据帧`df`，OBV列名称`obv_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def obv_strategy(df, obv_col, inplace=False):
    """
    实现OBV策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含OBV指标数据的DataFrame，必须包含'obv_col'列。
    obv_col : str
        OBV指标的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：OBV连续上升
    df.loc[df[obv_col].rolling(window=3).sum() > 0, 'signal'] = 1
    
    # 产生卖出信号：OBV连续下降
    df.loc[df[obv_col].rolling(window=3).sum() < 0, 'signal'] = -1
    
    return df
```

## ADX 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 ADX 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def adx(df, window=14, inplace=False):
    """
    计算ADX指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close'列。
    window : int, optional
        ADX指标的时间窗口，默认为14。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含ADX指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算最高价和最低价之间的差异
    df['range'] = df['high'] - df['low']
    
    # 计算动量（Momentum）
    plus_dm = np.where(df['close'] - df['open'] > 0, df['range'], 0)
    minus_dm = np.where(df['open'] - df['close'] > 0, -df['range'], 0)
    
    # 计算ADM和ADN
    plus_dm_mean = plus_dm.rolling(window=window).mean()
    minus_dm_mean = minus_dm.rolling(window=window).mean()
    
    # 计算DX
    dx = (plus_dm_mean - minus_dm_mean) / (plus_dm_mean + minus_dm_mean)
    dx_mean = dx.rolling(window=window).mean()
    
    # 计算ADX
    df['adx'] = (100 - 100 / (1 + np.sqrt(dx_mean)))
    
    return df
```

## ADX 策略

> 假设你是个高级程序员和量化研究员，编写函数实现ADX策略。函数接受数据帧`df`，ADX列名称`adx_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def adx_strategy(df, adx_col='adx', inplace=False):
    """
    实现ADX策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含ADX指标数据的DataFrame，必须包含'adx_col'列。
    adx_col : str
        ADX指标的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：ADX超过25
    df.loc[df[adx_col] > 25, 'signal'] = 1
    
    # 产生卖出信号：ADX低于25
    df.loc[df[adx_col] < 25, 'signal'] = -1
    
    return df
```

## VWAP 指标

> 假设你是个高级程序员和量化研究员，编写函数实现 VWAP 指标。函数接受数据帧`df`，`inplace`参数控制是否原地更新`df`。`df`包含四个列：`open`开盘价、`high`最高价、`low`最低价和`close`收盘价。所有指标都应当保存到`df`中，最后返回`df`。

```py
def vwap(df, vwap_col='vwap', inplace=False):
    """
    计算VWAP指标，并更新DataFrame。
    
    参数:
    df : DataFrame
        包含价格数据的DataFrame，必须包含'open', 'high', 'low', 'close', 'volume'列。
    window : int, optional
        滚动窗口的大小，默认为1。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        包含VWAP指标的新DataFrame（如果inplace为False）。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 计算VWAP
    df['vwap'] = (df['open'] + df['high'] + df['low'] + df['close']) / 4 * df['volume']
        
    return df
```


## VWAP 策略

> 假设你是个高级程序员和量化研究员，编写函数实现VWAP策略。函数接受数据帧`df`，VWAP列名称`vwap_col`，`inplace`参数控制是否原地更新`df`。买卖信号应保存在`signal`列中。最后返回`df`。

```py
def vwap_strategy(df, vwap_col='vwap', inplace=False):
    """
    实现VWAP策略，生成买卖信号。
    
    参数:
    df : DataFrame
        包含VWAP指标数据的DataFrame，必须包含'vwap_col'列。
    vwap_col : str
        VWAP指标的列名。
    inplace : bool, optional
        是否在原地更新DataFrame，默认为False。
    
    返回:
    df : DataFrame
        原始DataFrame，增加了一个名为'signal'的列，包含买卖信号。
    """
    
    # 复制DataFrame以避免修改原始数据
    if not inplace:
        df = df.copy()
    
    # 初始化信号列，默认为无操作
    df['signal'] = 0
    
    # 产生买入信号：价格突破VWAP
    df.loc[df['close'] > df[vwap_col], 'signal'] = 1
    
    # 产生卖出信号：价格跌破VWAP
    df.loc[df['close'] < df[vwap_col], 'signal'] = -1
    
    return df
```

