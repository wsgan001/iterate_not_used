
# TODO
  * **没有总结，暂时放在这里，需要总结。**
  * 之前在视频中看到的时候，lda是与pca等降维一起讲的，没想到这本书里会放在线性模型里面。



# 前置知识
  * 拉格朗日乘子法
  * 奇异值分解





# 线性判别分析 LDA 介绍


线性判别分析 (Linear Discriminant Analysis，简称 LDA) 是一种经典的线性学习方法， 在二分类问题上因为最早由 [Fisher ， 1936] 提出 ， 亦称 "Fisher 判别分析"。严格说来 LDA 与 Fisher 判别分析稍有不同，前者假设了各类样本的协方差矩阵相同且满秩。<span style="color:red;">什么是协方差矩阵相同且满秩？</span>

LDA 的思想非常朴素：给定训练样例集，设法将样例投影到一条直线上，使得同类样例的投影点尽可能接近、 异类样例的投影点尽可能远离。

示意图如下： LDA 的二维示意图"+"、 "-"分别代表正例和反例，椭圆表示数据簇的外轮廓，虚线表示投影， 红色实心园和实心三角形分别表示两类样本投影后的中心点.


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/ih4CF1e14C.png?imageslim)


在对新样本进行分类时，将其投影到同样的这条直线上，再根据投影点的位置来确定新样本的类别。





给定数据集 $D=\{(x_i,y_i)\}_{i=1}^{m},y_i\in\{0,1\}$ ，令 $X_i$ 、$\mu_i$ 、$\Sigma_i$ 分别表示第 $i\in \{0,1\}$ 类示例的集合、均值向量、协方差矩阵。

如果若将数据投影到直线 $w$ 上， 则两类样本的中心在直线上的投影分别为 $w^T\mu_0$ 和 $w^T\mu_1$ ；若将所有样本点都投影到直线上，则两类样本的协方差分别为 $w^T\Sigma_0w$ 和 $w^T\Sigma_1w$ 。由于直线是 一维空间，因此 $w^T\mu_0$ 、$w^T\mu_1$ 、$w^T\Sigma_0w$ 和 $w^T\Sigma_1w$  均为实数。

欲使同类样例的投影点尽可能接近，我们可以让同类样例投影点的协方差尽可 能小，即 $w^T\Sigma_0w+w^T\Sigma_1w$ 尽可能小；而欲使异类样例的投影点尽可能远离, 可以让类中心之间的距离尽可能大，即 $||w^T\mu_0-w^T\mu_1||_2^2$ 尽可能大。同时考虑二者，则可得到欲最大化的目标：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/f854k2KdHG.png?imageslim)


我们定义 “类内散度矩阵” (within-class scatter matrix)：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/4KAfCk12jl.png?imageslim)


以及 “类间散度矩阵”  (between-class scatter matrix)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/K7j1al2FgI.png?imageslim)


则上面的最大化的目标 J 可以重写为：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/E93ig0Jaf5.png?imageslim)


这就是 LDA 欲最大化的目标，即 $S_b$ 与 $S_w$ 的 “广义瑞利商” (generalized Rayleigh quotient)。

如何确定 $w$ 呢？注意到这个广义瑞利商的分子和分母都是关于 $w$ 的二次项，因此它的解与 $w$ 的长度无关，只与其方向有关。（若 $w$ 是一个解，则对于任意常数 a, $aw$ 也是解）

不失一般性，令 $w^TS_ww=1$ ，则上面的式子等价于：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/ji8GGa7627.png?imageslim)


由拉格朗日乘子法，上式等价于：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/md3blfaAF3.png?imageslim)


其中 $\lambda$ 是拉格朗日乘子，我们注意到 $S_bw$ 的方向恒为 $\mu_0-\mu_1$ ，我们不妨令


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/eLHjFcKgal.png?imageslim)


代入上面的式子得：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/BgGka8Df96.png?imageslim)



考虑到数值解的稳定性，在实践中通常是对 $S_w$ 进行奇异值分解，即 $S_w=U\Sigma V^T$ ，这里 $\Sigma$ 是一个实对角矩阵，其对角线上的元素是 $S_w$ 的奇异值，然后再由 $S_w^{-1}=V\Sigma ^{-1}U^T$ 得到 $S_w^{-1}$ 。

值得一提的是，LDA 可从贝叶斯决策理论的角度来阐释，并可证明，当两类数据同先验、满足高斯分布且协方差相等时，LDA 可达到最优分类。

可以将 LDA 推广到多分类任务中。假定存在 N 个类，且第 i 类示例数为 $m_i$ 。我们先定义“全局散度矩阵”：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/HDcAd9LcFJ.png?imageslim)



其中 \(\mu\) 是所有示例的均值向量。将类内散度矩阵 \(S_w\) 重定义为每个类别的散度矩阵之和，即


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/3G5KD4FfkC.png?imageslim)


其中


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/lchkJh4mHh.png?imageslim)


得到：



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/86D5mKLHHB.png?imageslim)


显然，多分类 LDA 可以有多种实现方法：使用 $S_b$ 、$S_w$ 、$S_t$ 三者中的任何两个即可。常见的一种实现是采用优化目标：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/AdIgELbkml.png?imageslim)


其中 $W\in \mathbb{R}^{d\times (N-1)}$ ，$tr(\cdot )$ 表示矩阵的迹(trace)，上式可以通过如下广义特征值问题求解：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180626/GgkHkF0ab9.png?imageslim)


W 的闭式解则是 $S_w^{-1}S_b$ 的 N-1 个最大广义特征值所对应的特征向量组成的矩阵.

若将 W 视为一个投影矩阵，则多分类 LDA 将样本投影到 N - 1 维空间， N- 1通常远小于数据原有的属性数。于是，可通过这个投影来减小样本点的维数，且投影过程中使用了类别信息，因此 LDA 也常被视为一种经典的监督降维技术。




# REF
  1. 《机器学习》周志华
