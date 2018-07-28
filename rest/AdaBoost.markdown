
# TODO

* **没有怎么看，这部分，还是看书比较好，视频讲的云里雾里。**
* **这个人的教程还是要自己学习相关资料之后，大概理解之后再来听，把遗漏的地方不上，不然，直接听基本没明白。**
* **adaboost要拆出来，而且顺便看下3月份的视频讲adaboost的部分。**

# MOTIVE
* 对集成学习进行总结：



# AdaBoost算法介绍




## 什么是AdaBoost 算法？


是自适应提升算法的一种。


## 到底怎么理解AdaBoost算法？

为了比较好地理解AdaBoost，先来看一下这个问题：

对于左边的这张图，如何划分红球和篮球？

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Al6kdc2LBK.png?imageslim)

显然这个问题用一个线性分类器的话很难取得最好的效果。

那么有没有办法通过组合一系列和正方形平行的线（每条线都相当于一个线性分类器）来获得一个比较好的分类效果呢？OK，我们可以这么做：




  * 首先，我们选择一条平行于四边且最不坏的线段来进行划分，OK，我们找到了，第一排中间的小图里，直线把图分为左边和右边两类，被分错的点只有3个。


  * 看起来好像还不错，这似乎是能得到的最好的结果了。接下来我们想去再找一个线性分类器来对总体的样本进行切分。但是这时候，如果还是基于这些点的话，那么说不定还是只能得到和之前那条差不多的线段，那么这样的话，这个新的线性分类器跟之前也没多少区别，那么我们想只是用多个不同的线性分类器进行划分就没有意义了，所以我们这个时候稍微调整了一下某些点在分类结果里的重要性，提升了他们的权重。OK，这里，我们提升了那三个被错分的蓝点的权重。


  * 由于我们提升了那三个被分错的蓝点的重要性，这时候我们再找线性分类器的时候，这个线段就会尽可能多地把蓝点归在一起，OK，就得到了 t=2 时候的图，这个时候我们分错的是右边那4个红点。


  * 这个时候我们想再建立一个线性分类器，同上面一样，我们把所有点的权重恢复正常，然后放大了上次分错的4个红点，提升他们的权重。


  * OK，像这样，不断重复。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/B0mec4d1Gk.png?imageslim)



最终我们得到了很多个线性分类器，这时候，我们把这些线性分类器的结果做一个线性组合，就得到了整个集成模型的结果。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/ldfhdbFh9a.png?imageslim)



而每个线性分类器的结果的系数（权重）就取决于它们之前的表现，表现越好，权重越高。比如第一条线段的分类错误就优于第二条线段，那么它获得的权重也就会更大。

OK，这个过程基本就是AdaBoost算法的过程。


## 为什么叫 AdaBoost？


在训练的过程中，每个新的模型都会基于前一个模型的表现结果进行调整，这个过程很像是一个自适应（adaptive）的过程，所以叫做 AdaBoost。**确认下**


## AdaBoost 算法在实际中使用的情况怎么样？


集成模型的效果非常好。**需补充**

# AdaBoost 算法流程如下：


**自己写一下**

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/cFa0AKmbAa.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/d8GDK5HfeI.png?imageslim)


# Adaptive Boosting (AdaBoost)

## AdaBoost介绍

* 对样本赋予权重，采用迭代方式构造
* 线性加权得到最后的结果


这种算法既要学出函数，又要学出权重。


## 利用权重构造不同的函数


对同样的算法，相同的训练集，如果样本的权重不一样，能够得到不同的函数。**什么叫样本的权重不一样？权重是什么意思？为什么能够得到不同的函数？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/c0C51DLLfD.png?imageslim)

\([g_t(x_i)\neq y_i]\)  这个的输出是 0 或 1 。

反过来，已知一个函数，可以通过设置权重，使得这个函数看起来像随机的。**为什么要使它看起来是随机的？为什么可以设置为1/2？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/gBA0K4I2iC.png?imageslim)

权重->函数，函数->权重，因此构造出一个迭代的方式，通过这个迭代的方法可以制造出一系列的g(x)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/LfHeF2l95D.png?imageslim)

**完全没明白怎么就可以构造出来了？**

即在已有的权重下，迭代构造新的权重（对应新的判别函数）：把分错样本的权重放大，分队样本的权重缩小即可


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/KAfE4GAF7F.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/BedhDKeCaK.png?imageslim)

## 基于权重构造系列函数的方法：


  * u1=[1/N,...1/N];


  * for t=1...T


    * 在ut权重下选择gt，使得权重分类误差最小


    * 根据gt的结果，重新生产ut+1





  * 最后得到一组gt(x)函数（实际上可以认为g0(x)为随机猜测函数）


得到了g1(x)...gt(x)一系列函数，那么如何构造G(x)呢？

AdaBoost 是一个在迭代中直接构造权重alpha的算法：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/AhkB8BJm7C.png?imageslim)

当加权错误率接近0的时候，a变得非常大

当加权错误率接近1/2的时候，a接近0


## 完整的AdaBoost算法：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/8B7EJ127mK.png?imageslim)

**基本每怎么听明白，看书的话倒是比较快的就明白了。虽然看书也有一些疑问。还是看书吧。**



下面来解释这个


## AdaBoost 函数也可以看作是前向分步算法的一种实现






  * 集成模型为加法模型


  * 随时函数为指数函数




### 什么是前向分布算法呢？


前向分步算法：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/DibhHC4c66.png?imageslim)

**什么是基函数。为什么这个是前向分布算法？**

前向分步算法的含义：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/I4Ch42f96c.png?imageslim)

### 前向分步算法的算法框架：



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/ajelE1JgiL.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/jAklc799L2.png?imageslim)




## 前向分步算法与AdaBoost




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/dmgBg1c4KB.png?imageslim)

证明：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Afd8m9bHFg.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/K3H2441f5F.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/FfleDGf9hF.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/FIa4dcmgLA.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/04cE71Feai.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/66E6e8LIK0.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/h9lfJD93Lk.png?imageslim)

图示：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Kf87fhm1bg.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/KBIA6HdCK0.png?imageslim)




# 著名案例：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/hJmBidG303.png?imageslim)


# AdaBoost 开发过程中要注意的


AdaBoost 算法中的弱分类器要很弱效果才好，比如单层的决策树，单层的决策树可以处理任何数据类型。 当然也可以使用任意分类器作为弱分类器，比如KNN、Decision Tree、Naive Bayes、SVM、LogisticRegression 等。** 都要尝试一下。**

AdaBoost 的大部分时间都用在训练上，分类器将多次在同一数据集上训练弱分类器。

同 SVM 一样，AdaBoost 预测两个类别中的一个。如果想把它应用到多个类别的场景，那么就要像多类 SVM 中的做法一样对 AdaBoost 进行修改。**嗯，到底要怎么修改？**


# AdaBoost 算法特点


优点：

  * 泛化（由具体的、个别的扩大为一般的）错误率低，
  * 易编码  **什么是易编码？**
  * 可以应用在大部分分类器上，
  * 无参数调节。**没有参数调节吗？**


缺点：

  * 对离群点敏感。**为什么对离群点敏感？**

适用数据类型：

  * 数值型和标称型数据。







# COMMENT：




AdaBoost确实采用的是指数损失，基分类器最常见的是决策树（在很多情况下是决策树桩，深度为1的决策树）。在每一轮提升相应错分类点的权重可以被理解为调整错分类点的observation probability。**没明白？**



# REF

* 七月在线 机器学习
* [机器学习算法中GBDT与Adaboost的区别与联系是什么？](https://www.zhihu.com/question/54626685)
