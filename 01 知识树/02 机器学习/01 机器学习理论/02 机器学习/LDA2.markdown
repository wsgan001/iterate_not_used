# LDA

## REF

1. 七月在线 机器学习



# MOTIVE

* 把LDA总结一下，注意这个LDA是 Linear Discriminant Analysis



## 知识前提

* 熵的定义式等概念。
* 决策树学习的生成算法。
* [实对称阵不同特征值的特征向量正交](http://106.15.37.116/2018/03/31/ai-linear-algebra-matrix-transformation-and-eigenvalues/#i-8)
* 伪逆





# LDA介绍


Linear Discriminant Analysis

LDA 线性判别分析，这个是一个中看不中用，但是又必须说的




# LDA


线性判别分析 Linear Discriminant Analysis


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/EAEd9Ad6Bl.png?imageslim)

今天我们讲的是线性判别准则 LDA，而后面在主题模型那里还有一个LDA，那个是一个生成模型，这个是判别模型。要区分开。

**刚看到上面的SVM的时候，我突然想到了怪不得之前说SVM是hingeloss，而softmax是MaxEnt。这个是之前在深度学习中提到的，在学过SVM之后，我才知道为什么SVM叫做hingeloss**


## LDA的思路


假定两类数据线性可分，即：存在一个超平面，将两类数据分开。则：存在某旋转向量，将两类数据投影到1维，并且可分。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ICDAFh5k1b.png?imageslim)

嗯，如果是完全线性可分的，那么在一维上也是可以线性可分的。

现在的问题就是，如何样本旋转之后，能够保证在低维上线性可分呢？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AaKKaH8G5F.png?imageslim)

我们就来算一下投影的方向： w是一个列向量，所以y是个一维的数。

如果能够得到 y 之后，能够方便进行分类，那么就OK了，所以关键是怎么找到这么一个 w


## 我们先来看一下类内均值和方差


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0Kmk1hg2GF.png?imageslim)

松散度其实跟方差是差不多的，就差一个系数。离散度除以n就是样本的方差。

第一个式子是投影之前的类内均值。

第二个式子是投影之后的类内均值，m1和m2是两个值。

第三个式子是松散度，但是yi是什么？是这个 ![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/85llIAKh0L.png?imageslim)

那么现在我们需要做什么呢？我们想使它投影之后两堆的散度尽可能的小，但是呢，投影之后的类内均值尽可能的大。

那么我们就得到了一个目标函数：我们想让这个目标函数越大越好


## Fisher判别准则




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/JDif72FelA.png?imageslim)

解释一下上面的：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kHcGHIdC5A.png?imageslim)是一个列向量乘以一个行向量得到一个矩阵。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/j48GE74db0.png?imageslim)

这个之所以能成立，是因为括号里面都只是数而已。


这样的转化就把w暴露出来了


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fLB6LDH61i.png?imageslim)这个最后的这个转置多了，打错了的。


之所以![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8h8ckca0f9.png?imageslim)这个的上面是平方是因为想让他量纲一样。

这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ib5JBbIK3G.png?imageslim)的推导没有写

最后得到如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/269Bhk6hFH.png?imageslim)

这里面的 $S_w$ , $S_b$ 都是可以计算出来的。

其实：




  * \(S_b\)是类内的散列值 Within-class scatter matrix。
  * \(S_w\)是类间的散列值 Between-class scatter。

## 现在开始求目标函数取最大值的时候的w值


先求偏导：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/h3bGeKJlBk.png?imageslim)

之所以![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/LcGDEIf3eJ.png?imageslim) 等于![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/c7llL0bK12.png?imageslim)是因为之前学习过xAx对x求偏导是2Ax。再确认下。应该是矩阵那块的。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ikl416CmKf.png?imageslim)因为括号里面的是两个数，所以，括号外面的是同方向的。


## Fisher 判别投影向量公式


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/cBi0G9EG1l.png?imageslim)

这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/dfA1dLbLCI.png?imageslim)之所以成立是因为![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9I9G718eE0.png?imageslim)是一个数。

这时候我们就得到了w的方向。即投影的方向


## LDA与分类

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/i167elEl3j.png?imageslim)

所以这个就是LDA 线性判别准则带来的很漂亮的结论。


## 代码如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/caJjeLE0h4.png?imageslim)

分类step1：极大似然估计：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/82KHCHHe7D.png?imageslim)

分类step2：误判率准则


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/gl63mKg7JJ.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/b9jF5kK8hh.png?imageslim)

使用LDA将样本投影到平面上：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jgKA6B5eD4.png?imageslim)




## LDA特点：


LDA 是一个很好的结论，但是对于现在的分类问题而言没有这么强的实践意义。因为由LDA的计算公式看出，LDA是强依赖均值的。如果类别之间的均值相差不大或者需要方差等高阶矩来分类，效果一般。

若均值无法有效代表概率分布，LDA效果一般。LDA适用于类别是高斯分布的分类。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5ggCm1EbI7.png?imageslim)

有各种各样的情况不太适合于分类

另外一个中看不中用的原因是：

LDA与线性回归的关系：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kEj4c919Hh.png?imageslim)

什么叫![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/GBIHl2gbD2.png?imageslim)？

但是线性回归是不太适合做分类的，logistic回归是没问题的，但是线性回归是不合适的。所以这套LDA知道就可以。那么为什么要介绍呢？因为历史上出现过。好多时候会提到，比如面试什么的。并且主题模型的LDA在实践中还是有用的。**主体模型的LDA与这个地方的LDA有什么关系吗？**

更重要的是，如果我们的数据没有给定标记的时候，能不能仍然进行分类呢？






# LDA与PCA

* LDA：分类性能最好的方向
* PCA：样本点投影具有最大方差的方向




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/d4CH9ikjH6.png?imageslim)




# COMMENT
