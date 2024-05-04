<!--yml
category: 交易
date: 2023-09-17 19:50:30
-->

# 学习backtrader笔记(1): _pickle.PicklingError: Can‘t pickle attribute lookup

> 来源：[https://blog.csdn.net/qq_41578115/article/details/122333011](https://blog.csdn.net/qq_41578115/article/details/122333011)

2022年1月5日晚,在学习backtrader时,参考的是CSDN下载的资料<Backtrader官方文档中文翻译.PDF>.

本人开发IDE软件用的是pycharm.

当学习到参数优化这一节时用的资料的代码,在运行时报错.报错的提示是:_pickle.PicklingError: Can't pickle <class '__main__.TestStrategy'>: attribute lookup TestStrategy on __main__ failed

然后就开始找原因,前前后后折腾了一天,最终经过对比发现,该中文翻译资料里面的代码与官方的代码不一样的地方找到了.该中文翻译资料里面的代码是

```
cerebro.run()
```

而官方代码

```
cerebro.run(maxcpus=1)
```

maxcpus=1的意思是最大使用一个CPU

最终解决了问题