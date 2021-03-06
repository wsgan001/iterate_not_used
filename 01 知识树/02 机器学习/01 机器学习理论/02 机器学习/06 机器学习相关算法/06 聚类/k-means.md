# k-means

# ORIGINAL

* 对k-means 进行总结
* 掌握K-means聚类的思路和使用条件



# K-means 是一个最广泛的聚类方法




## K-means算法介绍


也是别的聚类的方法的基础。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1E9mB39AJm.png?imageslim)

**arg min 这种式子还是没怎么明白？每次都要遍历多有的样本点吗？簇中心变化率是什么？**


## 代码如下




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CIAFD0HDgL.png?imageslim)




## K-means过程


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2al4j6CFaD.png?imageslim)

K-means本质上做的是一个均方误差的梯度下降。可以这么理解。


## K-means是初值敏感的


因为它是贪心的，所以它的初值是敏感的：所以它做聚类结束的那个点，仅仅是局部最优，不是全局最优：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jkKmHemBlJ.png?imageslim)

所以有些时候，我们会重复很多次，随机选初始点进行聚类，最后把几次的结果比较一下，那个好选那个。**有什么更好的方法吗？**


## K-means 无法处理异常点


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jcflFhmd7H.png?imageslim)

**上面提到的 K 中值聚类经常用吗？**


## 使用二分 k均值聚类来避免初值敏感

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AD2d3IJc1j.png?imageslim)

我可以算一下各自簇的均方误差，如果我发现，又一个簇的均方误差特别大，总是将不了，那么我就强制把这个簇分成两类，然后再选择某两个簇中心最近的合成一类。这样就又合成了一个类，这样就可以得到第二幅图。**厉害了。**

所以这是一个一定程度避免均值敏感的一种思路，即做二分。**代码怎么实现呢？**


## K-Means适用范围


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/bck9fLgJdA.png?imageslim)

可见4类和6类，有时候真的无法判断出那个好。所以聚类里面有时候不太方便判断那个是好的，没有一个好的指标 。

而右下角的图，可以看出，虽然想让它分5类，但是这5个类别分得有些奇怪。

可见，类圆形的，凸的，类高斯分布的 就可以分得很不错，这种右下角的分叉的就不是很合理。**为什么是类似高斯分布的时候？**


## K-means聚类方法总结


优点：

  * 是解决聚类问题的一种经典算法，简单、快速
  * 对处理大数据集，该算法保持可伸缩性和高效率
  * 当簇近似为高斯分布时，它的效果较好。**是因为是 GMM 模型的一个特例，所以才对高斯分布情况支持好吗？**


缺点


  * 在簇的平均值可被定义的情况下才能使用，可能不适于某些应用。**是的，簇的平均值无法被定义的时候的确不能使用。**
  * 必须事先给出k(要生成的簇的数目)，而且对初值敏感，对于不同的初始值，可能会导致不同结果。
  * 不适合于发现非凸形状的簇或者大小差别很大的簇
  * 对躁声和孤立点数据敏感


因此，可以作为其他聚类方法的基础算法，如谱聚类

** 怎么判断簇是不是近似于高斯分布？如果不是高斯分布，那么用什么来聚类？**
