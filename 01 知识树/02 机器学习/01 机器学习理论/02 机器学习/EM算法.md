# EM算法



## 缘由：

- 对 EM 算法进行总结，而且之前在视频中有提到说 EM 算法非常强大，
- EM 算法怎么将拧在一起的数据怎么分开？
- 怎么做前景后景分开，图像分割等，想知道什么是 EM 算法，到底怎么实现的？
- EM算法和聚类很相似，都是解决无标记样本的分类问题的。是这样吗？


# EM算法主要内容


Expection Maximium 期望最大化算法

  * 通过实例直观求解高斯混合模型GMM
    * 适合快速掌握GMM，及编程实现
  * 通过极大似然估计详细推导EM算法
    * 适合理论层面的深入理解
    * 用坐标上升理解EM的过程
  * 推导GMM的参数φ、μ、σ
    * 复习多元高斯模型
    * 复习拉格朗日乘子法


# 复习一些基础知识




## Jensen不等式


对于任意一个凸函数而言：

\[f(Ex)\leq Ef(x)\]


## K-means算法


再复习一下K-means算法的计算过程。K-means算法可以对样本进行聚类，但是没办法给出某个样本属于该簇的后验概率。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/46LHE5H9Ih.png?imageslim)

比如：这个黄色的点被标记成蓝色有多大的可靠性呢？是80%的概率被标记成蓝色还是什么？实际上极大似然估计就可以。
老师说实际上极大似然估计与k-means算法非常像。**那里像？**



而且有没有其他方法可否处理未标记样本呢？


## 极大似然估计


对极大似然估计的知识进行复习，之前我们已经得到如下结论：

\[\mu=\frac{1}{n}\sum_{i} x_i\]

\[\sigma^2=\frac{1}{n}\sum_{i} (x_i-\mu)^2\]

即可以通过极大似然估计对正态分布的参数进行估计：用样本的均值作为这个高斯分布的均值，样本的伪方差作为高斯分布的方差。



OK 到这里，我们本章需要的基础知识就已经完备了。

那么我们从一个例子开始讲起：


# 高斯混合模型




## 一个例子


问题：随机变量无法直接(完全)观察到


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/04d0jimlFD.png?imageslim)

也就是说：现在我们手头上只拿到了样本的高度，没有拿到样本的性别。这种情况就是不完全观测的。


## GMM 要研究的问题

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/L6dL49d09h.png?imageslim)

这个就是高斯混合模型要研究的内容，手段就是EM算法。


## 首先，我们建立一个目标函数


对数似然函数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/87IGidhbBk.png?imageslim)

注意，这个地方强调一下：似然函数只有在指数族分布的情况下才能得到全局最优，而GMM并不是，他是有多个极值点的。



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/2H5Ihf22gB.png?imageslim)就是那个正态分布。这个是第k个高斯分布




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/fEF1hfJ3l4.png?imageslim)这就是一个混合的高斯分布。




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/gmca9de20C.png?imageslim)对这样的混合高斯分布求极大似然估计，就可以先乘起来，然后取对数，就相当于先取对数再求和




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/mDB7kbCkeE.png?imageslim)由于这是一个对数似然，所以是小写的l


那么怎么求上面这个式子的最大值时的各个参数呢？


## 怎么求这个目标函数为最大值时候的参数呢？


由于在对数函数里面又有加和，我们没法直接用求导解方程的办法直接求得极大值。为了解决这个问题，我们分成两步。


### 第一步：估算数据来自哪个组份


**这个地方的公式为最核心的东西。**

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/5ifJi7I5bK.png?imageslim)

对上面的公式进行解释：

$N(x_i|\mu_k,\Sigma _k)$的意思是：对于第 $k$ 个高斯分布来说，如果我们已经知道了 $\mu$ ，$\Sigma$，那么我带入 $x$ ，总是能够求出这个高斯分布在 $x$ 处的概率密度。

\(\pi\)指的是，比如，我有2000个学生，其中1200个男生，800个女生，男生和女生的身高都满足各自的高斯分布。那么这个男生的占比\(\pi\)男。

注意这个占比与一个学生是男生的概率是不同的。比如：我随便拿出一个学生，这个学生是男生的概率\(\pi\)就是60%。但是，如果我拿出一个学生，而且知道这个学生的身高是2.2m，那么也许就不能认为他是男生的概率是60%了，因为男生和女生的身高都满足各自的高斯分布，有可能这个身高是男生的概率是90%。

所以，这个\(\pi\)只是我字面上的男生的数量占比，而并不能作为是男生的概率。  当然了，因为我事先并不知道学生中男生有多少，女生有多少，所以这个\(\pi\)还是一个先验概率，在现在是不知道的。

那么\(\pi_kN(x_i|\mu_k,\Sigma _k)\)乘起来是什么意思呢？就是随便拿出一个学生，男生的占比是\(\pi\)，而且他在男生里面这个身高出现的概率是\(N(x_i|\mu_k,\Sigma _k)\)，所以，这个\(\pi_kN(x_i|\mu_k,\Sigma _k)\)就是这个学生是男生的概率。

\(\Sigma_{j=1}^{K}\pi_jN(x_i\mid \mu_j,\Sigma_j)\)这个只是为了做归一化。因为对所有的\(\pi\)加起来是1，对所有的\(\pi_kN(x_i\mid \mu_k,\Sigma_k)\)加起来就不一定是1了，所以做一个归一化。


从上面的公式可以知道，假定我知道\(\pi\)，\(\mu\)，\(\Sigma\)，那么我是可以算出\(\gamma(i,k)\)的，但是现在我都不知道，那么我可以吧所有的k套的参数都猜出来。这样就能吧\(\gamma(i,k)\)给算出来。


算出来之后呢？




### 第二步：估计每个组份的参数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/EBjflFiIb7.png?imageslim)

厉害了，这样\(\gamma(i,k)x_i\)乘出来的点的确是满足第k组的高斯分布的。那么高斯根部如何取进行参数估计呢？就是我们刚才极大似然估计得到的式子，而讲这些高斯分布的样本带入到这两个式子就得到了这个高斯分布的均值和方差，也就是这个高斯分布的参数。

为什么除以的是\(N_k\)呢？而不是N呢？\(N_k=\sum_{i=1}^{N}\gamma(i,k)\)？比如1.9这个样本，有0.8个男，1.5这个样本有0.2个男，那么所有的样本我都知道有几个男，那么N_k就是这男的个数。还是没有很理解。

\(\pi_k=\frac{N_k}{N}=\frac{1}{N}\sum_{i=1}^{N}\gamma(i,k) 这个地方，是因为一共由\(N_k\)个男的，N是总体，所以得到了\(\pi_k\)



所以这里我就得到了k组的三个值，那么这时候，我把这些值代入到：\(\gamma(i,k)=\frac{\pi_kN(x_k\mid \mu_k,\Sigma_k)}{\sum_{j=1}^{K}\pi_jN(x_i\mid \mu_k,\Sigma_k)}\)

里面，就可以对之前由随便猜的值算出来的\(\gamma(I,k)进行更新。


## 那么有几个问题：




### 初值很极端的情况下，会收敛吗？


要注意：如果给的是一个很差的初值，那么就只能得到一个很差的结论，也就是说EM算法只是局部最优，不是全局最优。顺便提一下。K-means模型也是初值敏感的。


### 那么合适的初值怎么选呢？




### 为什么这种迭代更新是有道理的？


**注意，上面的GMM中先验分布全是高斯分布。**




# EM算法的提出




## EM算法对应的问题


OK，GMM到这里应该是讲的很详细了，那么EM算法到底怎么去做呢？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/EJj8AHEk1d.png?imageslim)

像得出样本的概率是p(x)，但是有隐变量z。所以我们希望得到带隐变量的概率分布。

比如对于上面举的身高的例子而言，x是身高，z是性别


## 同样的，通过极大似然估计建立目标函数


对数似然函数为：

\[\begin{align*}l(\theta)&=\sum_{i=1}^{m}log\,p(x;\theta)\\&=\sum_{i=1}^{m}log\sum_{z}p(x,z;\theta)\end{align*}\]

\(\sum_{z}^{ } p(x,z;\theta)\) 这个就把 \(z\) 强制暴露了出来，用 x 和 z的联合分布对 z 求积分。现在我们就开始想办法得到这个函数的极大值。


## 怎么才能求得这个函数的极大值呢


对于上面这个式子而言，z是隐随机变量，直接找到参数的估计是很困难的。那么怎么办呢？

如果 \(l(\theta\) 严格大于某个函数，并且我们方便求出那个函数的极大值，那么就很好了，我们就可以用那个函数的极大值代替 \(l(\theta\) 这个函数的极大值。


## 那么我们怎么去找那个比它小的函数呢？


我们首先假设 \(z\) 的分布是 \(Q_i\)。那么\(Q_i\geq 0\)。那么：

\[\begin{align*}\sum_{i} log\,p(x^{(i)};\theta)&=\sum_{i}log\sum_{z^{(i)}}p(x^{(i)},z^{(i)};\theta)\\&=\sum_{i}log\sum_{z^{(i)}} Q_i(z^{(i)})\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\\&\geq \sum_{i}\sum_{z^{(i)}} Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\end{align*}\]

对上面的式子说明一下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/7gfJj1e6g3.png?imageslim)

这个\(log\sum_{z^{(i)}} Q_i(z^{(i)})\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\)其实可以看成是一个log函数，里面是Ex，那么x是什么呢？就是\(\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\)。也就是说这个式子可以看成是\(f(Ex)\)，那么由Jensen公式，我们自然可以想到它对应的\(Ef(x)\)，写出来是：\(\sum_{z^{(i)}} Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\)，正是上面的最后一个式子。而这个时候要注意了，Jensen不等式的前提条件是\(f(x)\)是一个凸函数，而这个地方的\(log\)却是一个凹函数。所以上面的最后一个就不是小于等于，而是大于等于。



那么这个时候我们就已经得到了一个比它小的函数。而我们之前想做的是什么呢？是相求Lagrange函数的极大值，那么现在由一个函数小于等于这个Lagrange函数。所以我们不仅仅想求这个函数的极大值，我们还想让这个函数尽可能的大，最好是与原来的Lagrange函数相等。这样我求得的这个函数的极大值就与原函数基本差不多了。

也就是说：


## 我们需要使这个下界函数尽可能的大


下界函数最大能多大呢？大于等于符号取等于的时候最大。那么这个地方取等于是什么情况？

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/6e3AbJjIB9.png?imageslim)

我们看一下右侧的图：\(f(Ex)\leq Ef(x)\) 在什么情况下取等于？

* 或者是线段在无穷远处。
* 或者是线段退化成一个点。


那么当线段蜕化成一个点的时候意味着什么呢？意味着x，也就是\(\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\)一直不变，是一个常数。

也就是：其中 c 为常数。

\[\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}=c\]

即两个函数成正比：

\[Q_i(z^{(i)})\propto p(x^{(i)},z^{(i)};\theta)\]

而，对于\(Q_i\)这个隐变量的概率分布来说：

\[\sum_{z} Q_i(z^{(i)})=1\]

那么也就是说：我只要对\(p(x^{(i)},z^{(i)};\theta)\)进行归一化，那么它就等于\(Q_i(z^{(i)})\)：

\[\begin{align*} Q_i(z^{(i)})&=\frac{p(x^{(i)},z^{(i)};\theta)}{\sum_{z}p(x^{(i)},z;\theta)}\\&=\frac{p(x^{(i)},z^{(i)};\theta)}{p(x^{(i)};\theta)}\\&=p(z^{(i)}\mid x^{(i)};\theta)\end{align*}\]

最后的结果是 \(x\) 给定的时候 \(z\) 的条件概率。所以，当我的 \(Q_i(z^{(i)})\) 为这么一个概率的时候，我的式子就可以为等号。

所以， \(Q_i(z^{(i)})\) 就是样本给定的时候隐变量的条件分布。

而上面说的这个用给定样本的条件分布对样本值求期望，这个 就是E的做法，也就是EM算法的第一步。**什么叫：用给定样本的条件分布对样本值求期望？这句话不知道从哪里来的，估计听视频听来的。**


## 剩下要做的事情


OK，剩下的就是把刚才求得的 \(Q_i(z^{(i)})\) 带入到\(\sum_{i}\sum_{z^{(i)}} Q_i(z^{(i)})log\frac{p(x^{(i)},z^{(i)};\theta)}{Q_i(z^{(i)})}\)来使它最大，并求出最大时候对应的\(\theta\)。

OK，这一步就是EM算法的M。也就是第二步。

我们总结一下：


## EM算法整体框架


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/DdIJlIDAk5.png?imageslim)

即：先写出给定样本的时候隐变量的条件分布，然后将 \(Q\) 带入这个式子中求出他的极大值。不管用任何一种求极值的方法。 当你求出了 \(\theta\) 的时候，你吧 \(\theta\) 待会到第一步就能求出这个 \(Q\) ，新的 \(Q\) 得到了，再带入第二步。不断循环。

最终会收敛，这个最终的 就可以算出来。




## 可以把这个一个过程看作使坐标上升




坐标上升


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/DHfDd2FAaL.png?imageslim)

从理论公式推导GMM


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/e07kB0mBFE.png?imageslim)

E-step


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/b4LBE36Kfe.png?imageslim)

M-step

将多项分布和高斯分布的参数带入：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/e16ci6ALbE.png?imageslim)

对均值求偏导


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/iaJ8k3AmIJ.png?imageslim)

高斯分布的均值

令上式等于0，解的均值：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/FECa04DD19.png?imageslim)

高斯分布的方差：求偏导，等于0


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/eiB98gFcKB.png?imageslim)

### 多项分布的参数


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/IlJHECHKEm.png?imageslim)

拉格朗日乘子法


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/14Bd1LdlbC.png?imageslim)

求偏导，等于0


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/degmddE8K4.png?imageslim)

总结


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/j85fGJG7gi.png?imageslim)




### EM 代码如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/9dBE0EgKFD.png?imageslim)

### GMM与图像：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/12iDhl02J7.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HmKgh3e1a4.png?imageslim)




### 朴素贝叶斯的分析：


可以胜任许多文本分类问题。无法解决语料中一词多义和多词一义的问题——它更像是词法分析，而非语义分析。如果使用词向量作为文档的特征，一词多义和多词一义会造成计算文档间相似度的不准确性。

可以通过增加“主题”的方式，一定程度的解决上述问题：




  * 一个词可能被映射到多个主题中：一词多义


  * 多个词可能被映射到某个主题的概率很高：多词一义




### pLSA模型：


基于概率统计的pLSA模型(probabilistic Latent Semantic Analysis，概率隐语义分析)，增加了主题模型，形成简单的贝叶斯网络，可以使用EM算法学习模型参数。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/BD5ghJKB8K.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/cfg6Ac0fLI.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/5D10D4gj34.png?imageslim)

极大似然估计：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/ja4C235HG5.png?imageslim)

目标函数分析：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1Klfb1e1k3.png?imageslim)

求隐含变量主题\(z_k\)的后验概率


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/abaHId1DdH.png?imageslim)

分析似然函数期望：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Cab0C11DF1.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/I9CFHb8k1K.png?imageslim)

完成目标函数的建立：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/IBhJclC1dl.png?imageslim)

目标函数的求解


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/FcFLc43Flg.png?imageslim)

分析第一个等式


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/55cGLmmC3g.png?imageslim)

同时分析第二个等式：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/aAAEHIDA3l.png?imageslim)

pLSA的总结：

pLSA应用于信息检索、过滤、自然语言处理等领域，pLSA考虑到词分布和主题分布，使用EM算法来学习参数。

 虽然推导略显复杂，但最终公式简洁清晰，很符合直观理解，需用心琢磨；此外，推导过程使用了EM算法，也是学习EM算法的重要素材。






### pLSA进一步思考


相对于“简单”的链状贝叶斯网络，可否给出“词”“主题”“文档”更细致的网络拓扑，形成更具一般性的模型？

pLSA不需要先验信息即可完成自学习——这是它的优势。如果在特定的要求下，需要有先验知识的影响呢？

答：LDA模型；

* 三层结构的贝叶斯模型
* 需要超参数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/LEFEcaAJ18.png?imageslim)





## REF

1. 七月在线 机器学习
2. [从最大似然到EM算法浅解](https://blog.csdn.net/zouxy09/article/details/8537620)
