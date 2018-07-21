
非常不建议在XGBoost 里面用他的缺省值处理方法
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180718/C2DHI1elbg.png?imageslim)
还是要一个feature，一个feature 的手工做处理比较好。



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180718/dhlD6kHgHc.png?imageslim)
这个可以知道每个特征的重要性。

看起来 XGBoost 还是很好用的。
最简单的例子：guide-python/basic_walkthrough.py 里面有讲一些基本的操作。
然后guide-python 里面还有一些例子。比如custom_objective.py 这个是教你怎么自定义loss function 的。
注意：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180718/D710cdeHhD.png?imageslim)
如果你要自己制定loss function 的话，你要保证你的loss function 是可导的，而且，你要告诉他一阶导数 grad 和二阶倒数 hess。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180718/3klEbE2jHc.png?imageslim)
这个是交叉验证的使用案例。

sklearn_parallel.py 这个是讲怎么用sklearn 并行的调用xgboost

guide-python 里面基本覆盖了你一般会用到的一些功能



课程文档里面的XGBoost model tuning 可以直接参考这个来进行调参。



XGBoost 可以布置分布式的，基本上都在用，用到GBDT 的地方基本上都用它。大的公司会布置分布式的版本，在大的数据上训练是很快的。

DL 基本上只在图像上有很多的应用，在别的东西上应用没有那么多。

lightGBM 确实有一定程度的提升，但是没有它说的那么大的提升。现在用得少是因为没有python 的版本。

目前boosting 的算法，除了 XGBoost 和lightGBM ，如果在非常大的数据集上进行训练，百度有 一个基于RF 的算法，比XGBoost 的效果要好
