# LDA

对主题模型进行整理总结


## 基础知识

* 贝叶斯公式


## 什么是LDA？


隐含狄利克雷分布（英语：Latent Dirichlet allocation，简称LDA），是一种主题模型，它可以将文档集中每篇文档的主题按照概率分布的形式给出。同时它是一种无监督学习算法，在训练时不需要手工标注的训练集，需要的仅仅是文档集以及指定主题的数量k即可。此外LDA的另一个优点则是，对于每一个主题均可找出一些词语来描述它。


# LDA涉及的主要问题：


- 共轭先验分布
- Dirichlet 分布
- LDA 模型
    - Gibbs 采样算法学习参数








# 先说一下共轭先验分布




## 频率学派和贝叶斯学派


**这个放在这里合适吗？**

给定某系统的若干样本，求该系统的参数。怎么办呢？

频率学派的方法：




  * 思路：假定参数是某些未知的定值，然后求参数的过程，最终是求某个目标函数的极大极小。


  * 方法：矩估计/MLE/MaxEnt/EM等：


贝叶斯学派的思路和方法：


  * 思路：假定参数本身是变化的，而且参数本身还服从一个分布，然后在这种情况下求目标函数的极大极小值


  * 方法：贝叶斯模型


二者无高低好坏之分，只是认识自然的手段。只是在当前人们掌握的数学工具和需解决的实践问题中，贝叶斯学派的理论体系往往能够比较好的解释目标函数、分析相互关系等。

前面章节的内容，大多是频率学派的思想；下面的推理，使用贝叶斯学派的观点

大数据是频率学派对于贝叶斯学派的一次强有力的逆袭，大数据是跟频率学派有关的。而贝叶斯学派会说它们会用小样本做一些事情。


## 共轭先验分布：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ceehb3b1fL.png?imageslim)

不同的参数\theta的时候都是除以p(x)的，所以p(x)本身跟我是没有关系的，只是样本存在的证据，它本身无法对我的\theta 提供更多信息

所以要想\(P(\theta \mid x)\) 最大，本质上就是想让\(P(\theta \mid x)P(\theta)\) 最大，

在极大似然估计 MLE中，我们是假定\(P(\theta)\) 都是相等的，即均匀分布，所以求出最大的\(P(\theta \mid x)\) 我们就认为是最大的\(P(\theta \mid x)\) 。**是这样吗？要验证下。**

而如果\(P(\theta)\) 不是均匀分布的，那么我们就需要考虑\(P(\theta \mid x)P(\theta)\) 这个整体，就是MAP，即极大后验概率。

这一点可以认为：频率学派是贝叶斯学派的一种特殊情况，即\(P(\theta)\) 为均匀分布。

正常而言\(P(\theta \mid x)\) 这个后验概率的分布，与\(P(\theta)\) 这个先验概率分布是不同的分布，如果选择了某一个合适的\(P(\theta \mid x)\) ，是的二者是同样的分布率，那么这种时候先验和后验就是互为共轭分布，而这个时候的这个先验函数\(P(\theta)\) 就是似然函数\(P(\theta \mid x)\) 的共轭先验分布。

** 为什么要使先验概率和后验概率是同一种分布呢？ **


##


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/B04kEfJ5fe.png?imageslim)



## 到底为什么要提出共轭先验分布？ 在实践层面如何起作用？


共轭先验分步的实践意义


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/l38Kba4G7j.png?imageslim)

其实我们已经接触过了：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/L0FmmCKHcJ.png?imageslim)

$\lambda$ 是乘法因子。

为什么敢加这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/e3h2I37cIe.png?imageslim)？本质上平方和损失就是假定参数\theta 服从高斯分布的贝叶斯学派的思想。没明白？

$f(x)=a*e^(b*x^2)$  这里面的a和b都是系数，如果能得到这个，这里面的x一定是高斯分布的，只不过这里面的均值是0。好像有这么一回事。再看下。

上面这个其实就是贝叶斯学派的一个应用。再看一下之前这部分的文章。



OK，继续复习：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/b19CcldlcC.png?imageslim)


这个东西可以在一定程度上解释：频率的极限是概率。

这个与我们的大数定理是不矛盾的


那么我们现在给出一个问题：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0FI6D99EGA.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8e2m4gaH9f.png?imageslim)不能，因为我们的样本太少了。


如果对公式进行修正，感觉就合理了一些了。那么这个修正的值是什么？为什么可以加上这个修正值？这个加几这个背后有什么理论吗？

我们就造一个理论出来：

上述过程的理论解释：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5ccmAC36Al.png?imageslim)

**我还是没有明白为什么要用共轭先验？普通的先验为什么不行？为什么后验与先验一定是同分布的？**

Beta分布就是二项分布的共轭分布。

先验概率和后验概率的关系：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/KCmK1Hd8cg.png?imageslim)

到![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/higL6m7mjk.png?imageslim)这一步，是把归化因子去掉了。

可见，上面的二项分布，乘以一个Beta分布，结果仍然服从一个Beta分布。

所以，先验后验都是Beta分布，所以它们互为共轭的。

其实先验分布的\alpha 和\beta 与x进行了加和，而x是硬币扔完之后朝上的次数。

我们把\alpha 和\beta 叫做伪计数。




## 伪计数


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AjGh5JEbiF.png?imageslim)

而上面的例子中的5和10就对应 $\alpha$ 和 $\beta$ ，可以看到，这个5和10在样本数量很少的时候，可以极大的调节这个结论使更合理，但是如果在样本很大的时候，5和10 就不起作用了，

这个其实就是用贝叶斯这套机制的重要内容：能够在小样本的时候，使它在一定程度上是合理的。我们发现，如果不加上5和10，那么结果显然过拟合了，而有了这个先验的条件就可以避免过拟合。

实际上之前我们讲的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/KIc0c3IF8L.png?imageslim)也就是为了防止过拟合。

感觉上面的这还是很厉害的，但是只支持二项分布吗？

实际上是可以推广出来的：

## 共轭先验的直接推广


从2到K：

* 二项分布 -> 多项分布
* Beta分布 -> Dirichlet分布


**厉害呀。看到多项分布，立马就想到了文档的主题分布和主题的词分布 都是多项分布。**

这里我么加一个引子：一个Gamma函数

# Gamma函数


Γ函数是阶乘在实数上的推广

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fb0hkfdEe9.png?imageslim)

所以，当我们看到\(\Gamma(n)\)的时候，一定要知道这个是\((n-1)!\)

**厉害了，这个函数是怎么发现的？**

OK，我们继续看Dirichlet分布：


# Dirichlet 分步


参照Beta分步的定义：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/d3EkcDfaCI.png?imageslim)

## Dirichlet分步的定义：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Fhc6Fl9kID.png?imageslim)

解释一下：

刚才我们的Beta分布 里面的\alpha 和\beta 是超参数

把\theta 变成k个，组成一个向量\(overrightarrow{p}\)

把\alpha 写成k个，组成一个向量\(overrightarrow{\alpha}\)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ihldJDkClc.png?imageslim)就是对应的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3iL52b41Cf.png?imageslim)




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mHgiekgkjD.png?imageslim)就是对应的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/a3aaHBfb2k.png?imageslim)


比如说$\alpha_1=2$，$\alpha_2=3$，$\alpha_3=4$ 那么：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jJL85d19G4.png?imageslim)就是： $9!/(1!2!3!)$




我们假定![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/55Ei454I9C.png?imageslim)记作：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/f55Fbcd3d7.png?imageslim)也就是说：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/FI9gCbaeI5.png?imageslim)

我们说一下 $\alpha$ 对我们的分布的影响：


## Dirichlet 分步分析


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AbakBGhC3g.png?imageslim)

参数决定的是 $p1,p2...pk$  的概率。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1cm8lD07Fk.png?imageslim)所以它的自由度是k-1维的，所以，我们的Dirichlet分布是用k个变量来约束k-1个变量。


比如说，抛硬币那个例子中的\theta 朝上的概率，实际上只有这一个参数，但是到了Beta分布里面，就有了\alpha 和\beta 两个参数 来决定一个\theta 。

虽然说\alpha 有k个，但是我们实践中取\alpha_1=\alpha_2=...=\alpha_k 等于某一个数。k个维度都取相同的。

这个时候就是对称的dirichlet分布。

因为正常而言，我们无法假定这p_1，p_2，p_k那个维度是更重要的。因此根据最大熵模型的特点，什么都不知道的时候，取均匀分布。

Symmetric Dirichlet distribution

A very common special case is the symmetric Dirichlet distribution, where all of the elements making up the parameter vector have the same value. Symmetric Dirichlet distributions are often used when a Dirichlet prior is called for, since there typically is no prior knowledge favoring one component over another.Since all elements of the parameter vector have the same value, the distribution alternatively can be parametrized by a single scalar value α, called the concentration parameter(聚集参数).

所以，这个时候\alpha 全部相等，此时，就从一个向量退化称一个标量


## 对称Dirichlet分布：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9lhgcbLF21.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5AACLcFfmh.png?imageslim)




## 对称Dirichlet分布的参数分析：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HEHC73d2Bg.png?imageslim)

\alpha 等于1 时候是平的

\alpha 大于1的时候是中间高的。

\alpha 小于1    在某一个维度上，p1=p2=0的时候，p3值能取最大， 取某一个维度的时候概率是最高的，取三个都相等的时候概率是最低的。 **什么意思？**

这个图的意思是：假如说x1，x2,x3是三个概率，三个维度上取加和为1，那么，我只需要研究x1，x2就行，对于任何的x1取某个值，x2取某个值，等一定会有某个概率值，那个值我们记作p(x1,x2)，由于是画的p(x1,x2)的lnp(x1,x2)，因此有正有负。

**还是没明白？到底这个图的纵轴是什么？为什么与\alpha 有关？x是什么？p是什么？**



比如我有![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HF7eh92fDk.png?imageslim)这几个点的分布，取第一个值的概率是0.2，取第二个是0.3.。。，如果我以概率来从这几个数中取，可能有30%的情况取到第二个数，虽然第三个数取到的概率是0.03，但是仍然是可能被取到的，如果做了1000次实验，正常而言，可能有30次被取到。

而上图：比如说这个点![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/EA9690BH1E.png?imageslim)有0.2的概率被取到而![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/572Ihkg00B.png?imageslim)可能有0.1的概率被取到。

**上面这个图是从wiki上下载的，最好到wiki上再看一下，因为还没怎么理解它的意思。**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/gCC09b4B4C.png?imageslim)




## 参数 α 对Dirichlet分布的影响




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/L6ffAkJ1EA.png?imageslim)

\(x_1,x_2,p(x_1,x_2)\)这张图更重要，x1是一个轴，x2是一个轴，纵轴是p(x1,x2) 是一个概率值，所以

\(\{a_k\}=0.1\) 时候，会极大的归属到某个轴上，这样就利于做收敛，因为我的词就老是会几种到某个主题上去，经过若干次迭代就能得到我们的结论。而\alpha 越小，就表示主题越鲜明。

而\alpha 为1的时候，意味着各个主题被取到的概率是一样的，也就是说这时候的主题是最不鲜明的。

当\alpha 很大的时候，相当于主题偏向于都几种在最中庸的那种状态。即这篇文章只要是社会上出现的主题，都会涉及 。越是主题相等越不鲜明。



当\alpha=1的时候，这个


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/62LDaFab1I.png?imageslim)

式子里面的 \(p_k^{\alpha-1}\) 就是1 ，其实就是均匀分布。



注意，当我们的语料的数量足够大的时候，\alpha 的值其实是没有影响的，因为这个时候\alpha 先验的额影响已经很小了。



参数选择对对称Dirichlet分布的影响

When α=1, the symmetric Dirichlet distribution is
equivalent to a uniform distribution over the open
standard (K−1)-simplex, i.e. it is uniform over all
points in its support. Values of the concentration
parameter above 1 prefer variants that are dense,
evenly distributed distributions, i.e. all the valu es
within a single sample are similar to each other. Values
of the concentration parameter below 1 prefer sparse
distributions, i.e. most of the values within a single
sample will be close to 0, and the vast majority of the
mass will be concentrated in a few of the values.




## 多项分布的共轭分布是Dirichlet分布




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ckd0fii7DC.png?imageslim)



说实话上面的还没怎么理解。

现在开始说明LDA“

这个LDA一定要自己解释清楚，并且根据


# LDA的解释


共有m篇文章，一共涉及了K个主题；

每篇文章(长度为N m )都有各自的主题分布，主题分布是多项分布，该多项分布的参数服从Dirichlet分布，该Dirichlet分布的参数为α；

每个主题都有各自的词分布，词分布为多项分布，该多项分布的参数服从Dirichlet分布，该Dirichlet分布的参数为 β ；

对于某篇文章中的第n个词，首先从该文章的主题分布中采样一个主题，然后在这个主题对应的词分布中采样一个词。不断重复这个随机生成过程，直到m篇文章全部完成上述过程。



m篇文档是样本给定的，K是我们自己设的。

主题分布是一个k点分布，因此这个主题分布是多项的，只不过在某些主题上概率大些而已。这个主题的参数服从的是Dirichlet分布，这个Dirichlet分布的参数我们记作\alpha .

任何一个主题在词典中的所有词上都有一个概率值，只不过有的概率值是极小的而已，


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/iHkdmG7mBE.png?imageslim)

这个w表示的是文档的第n个词，是一个可以观测的变量。

首先，\alpha 是dirichlet分布的参数，根据这个参数就可以决定一个主题分布 \(\overrightarrow{\vartheta}_m\)，我们从主题分布里面可以采样出一个主题出来： \(z_{m,n}\)，（**什么叫就可以采样出一个主题？**）这个就是第m篇文档的第n个词应该属于那个主题。

然后，如果有了\beta 这个超参数，我们就可以利用这个超参数所决定的dirichlet分布去采样出一个相应的词分布出来\(\overrightarrow{\varphi}_k\)，假设有K个主题，那么我的词分布就有K个，比如，我的主题的数目是K，词的数目是V，那么对于一号主题而言，一号主题的词分布是V个值，二号主题的词分布也是V个值，等等。所以这个\(\varphi\) 是有K个的。那这样子有K个主题，我从主题分布\(\overrightarrow{\vartheta}_m\)里面采样出某一个主题 \(z_{m,n}\)，因此，我就用这个主题 \(z_{m,n}\)与我的K个主题分布\(\overrightarrow{\varphi}_k\)一结合，我就选中了第z个主题，然后。。。。 由于前面我们已经有一个主题 \(z_{m,n}\)，那么这个主题对应的词分布是多少，这个就共同决定了一个词\(w_{m,n}\)。

上面就是这个模型的物理意义。** 没明白？**

方框的意思是一对多



这个是本章的最关键的一页。


## 详细解释




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/b2IElhdKEA.png?imageslim)

一般用term和token来表示词汇或词项，也就是字典里面的词，这个是不可重复的，

而word是可以重复的某一个词，是文章中的某个词。

其实感觉上面这一页还是比较好理解的。

图中K为主题个数，M为文档总数，Nm是第m个文档的单词总数。β是每个Topic下词的多项分布的Dirichlet先验参数，α是每个文档下Topic的多项分布的Dirichlet先验参数。zmn是第m个文档中第n个词的主题，wmn是m个文档中的第n个词。两个隐含变量θ和φ分别表示第m个文档下的Topic分布和第k个Topic下词的分布，前者是k维(k为Topic总数)向量，后者是v维向量(v为词典中term总数)



我们来说一下参数的学习


# 参数的学习




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ci8fcmK536.png?imageslim)

上面这个式子根据的是贝叶斯网络

但是为什么这个地方这么写？\(\overrightarrow{\varphi}_{z_{m,n} }\)


因为z可以看成是一个int值，它采样出来某一个z的时候，我就用这个主题编号去选第几个主题分布就行。因此，虽然这么写，但是本质上是两个变量。


注意，这个地方是一个词袋模型 ，比如说一把一篇文档打乱了之后，在LDA中仍然认为这个是同一片文章







那么在给定了主题和给定了词分布的情况下，看到词的概率有多大呢？




## 似然概率




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/KBf22BD114.png?imageslim)

\(W_{mn}\) 指的是第m篇文档的第n个词。这个词是 t 的概率就是：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0G9577hHfH.png?imageslim)

比如p(Wmn="好")


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9ClF1B2HHa.png?imageslim)这个就是整个看到的整个语料的联合概率。厉害了


实际上由于每个


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/IHfhc3L709.png?imageslim)

非常小，也就是说这个


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/f0KECBFhBd.png?imageslim)

会非常非常小，而这个时候计算机里面就要取对数，不然实在太小。这个问题在HMM中也会介绍







现在说一下Gibbs 采样：




# Gibbs 采样




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8L1faH5h4e.png?imageslim)

给定了\alpha 和 \beta  我们想推测一下这个词与某个主题出现的联合概率。

因为假如我拿到了这个联合概率，那么所有的信息都能拿到。这个之前说过，比如我有p(x,y)那么要求p(x)就直接把y求积分积掉就行。而如果还要求p(x|y)那么就是p(x,y)/p(y)  就行

联合分布：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7la33312jj.png?imageslim)

\(p(\overrightarrow{w}\mid \overrightarrow{z},\overrightarrow{\beta})\) 相当于是给定了某个主题之下的采样的某个词的概率。

\(p(overrightarrow{z}\mid \overrightarrow{\alpha})\) 这个是给定了\alpha 之后采样的某个主题的概率是多大。

虽然这两个式子看起来不同，但是实际上在网络上是类似的：

\(p(overrightarrow{z}\mid \overrightarrow{\alpha})\) 对应![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/6D8eJ78dD3.png?imageslim)

\(p(\overrightarrow{w}\mid \overrightarrow{z},\overrightarrow{\beta})\) 对应![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/F2IE7c96F3.png?imageslim)

\(n_m^{(k)}\)  即第m个文档里面主题k出现的次数。



先计算第一个因子：


## 计算因子


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5ke41dbj0j.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/89Gakhk3I0.png?imageslim)

解释：

\(p(\overrightarrow{w}\mid \overrightarrow{z},\overrightarrow{\beta})\) 是\beta 到w ，所以要对\Phi 进行积分，所以说我们给定了超参数\beta 的时候，采样到了某一个词分布\(\underline{\Phi}\) ，然后在给定了词分布和第z号主题的时候，我们采样到了这个词\(p(\overrightarrow{w}\mid \overrightarrow{z},\underline{\Phi})\)  。最后我们把所有的主题都做一个积分。

再说细一点：比如我的\beta 会采样到很多的词分布，这次采样到了某个词分布，能够得到这个词word，然后有一次采样到了某个分布，又能够得到这个词word，每一个都相当于是从\beta 到w的路径。我们把所有的词分布都采样完，再把每一个词分布得到这个词的概率都加起来，就得到了这个式子。

我的理解：就是不管你过来的是什么主题，我只想算出在这一套\beta的情况下 ，某个为t的词出现的概率是多少。就是这个积分的意思。






由于![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/69a6KDF3jb.png?imageslim)是一个dirichlet分布，而![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/dl6AfLE4kl.png?imageslim)所以，我们可以把这个带入到![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CIc4Hi1hcc.png?imageslim)




里面。




我们看前面这个：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/636k5DGEcD.png?imageslim)，这个是给定了某个主题的词分布




和某个主题之后，词的概率，这是一个普通的多项分布。由于term有V个，所以这是一个V项分布。那么当我的w想取到t的时候，有多少个是属于z这个分布的，就是![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/gJKjg0H8HL.png?imageslim)







**![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5F5mjh1F4A.png?imageslim)这个式子没明白？为什么![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2Gi04JgaJ0.png?imageslim)这个放在![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fLE56JF5HH.png?imageslim)前面？为什么是![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2Bc9KD1DGi.png?imageslim)这个？里面的乘法是那里来的？而且为什么要先对 t 求prod？这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/c8dCKJDJ6d.png?imageslim)是数出来的吗？为什么要相乘？而且，多项分布的时候，求其中某一个出现的概率是怎么算的？多项分布再看下。**














那么拿到了这个积分![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/J1h8dckDA3.png?imageslim)怎么求呢？首先我们看到了：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/6D993hIhHB.png?imageslim)这个式子的左右两边同时对p求积分，左边对p积分是1，那么我正好把右边的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2fEjGmg1ad.png?imageslim)乘到左边去，然后得到![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mf7b1KkkEm.png?imageslim)而由于![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/m9Fm8DfGeC.png?imageslim)



这部分不含![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mic9Ed2jeh.png?imageslim)，因此把它直接放到积分外面，而![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/94jh113gEE.png?imageslim)
的积分根绝上面的式子可以写成![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4CEcCF9Ebk.png?imageslim)。也就是说：得到：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/E0LJ8B39I6.png?imageslim)





## 类似的，可以计算第二个因子：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/i8flD50maJ.png?imageslim)



上面的两个可以看成：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/15kJAIKi0G.png?imageslim)与![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/g1Fd9hl2J5.png?imageslim)成正比，![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4jgegF9FhK.png?imageslim)与![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5JG8JkhF5a.png?imageslim)成正比。而所以，后面我就不管分母的问题，只管分子，**为什么可以不管分母？难道，不管是什么\alpha 的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/j1iD83mKCF.png?imageslim)都一样？**

所以，我们就可以往后面写了：

我要想知道这个词属于某个主题的概率：就看一下除了我这个词之外，其它的词的主题是什么。

Gibbs采样蕴含这一个思想：我想看看我所属的类别，我就看看别的人属于什么类别。**没明白？到底怎么真切理解？**




## Gibbs updating rule




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kHile891Bi.png?imageslim)

这里的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/I3fLl50BbJ.png?imageslim)是指除了i以外其它的主题。分子之所以是![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/glhfIehgJ1.png?imageslim)是因为i和除i以外的写在一起。这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/l81e6fHBb0.png?imageslim)就等于![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/B849E3c8cE.png?imageslim)其中p(wi)就是\(p(w_i|\overrightarrow{z}_{\neg i})\)，因为i与非i是独立的，因此直接写成p(wi)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/eDch2DGeLA.png?imageslim)是把之前的两个式子的结果带入进来，而之所以\(p(\w_i)\)没有了，是因为这个是一个词在样本给定之后的概率分布，在给定样本之后就是一个确认的值，因此直接扔掉了。因为这个\(\propto\)是要求成比例就行。


带入之后。![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/I1lhaHf924.png?imageslim)就是![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/bAb6FGki46.png?imageslim) 没明白？**为什么![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4BGc1hfd2k.png?imageslim)比![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Dd0jEGJEg4.png?imageslim)小1？**

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/LJkj42A2JK.png?imageslim)这个指的是某一个词t term可能隶属于第k号主题 出现了几次，比如说，在所有主题的第1，2个主题中出现了，那么值就是2，


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AeKadCC98F.png?imageslim)的意思是除去这篇这篇文档中第i个词以外的时候，词t在第k号主题中出现了多少次。**是不是这个意思？还是没明白？**


而这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/gGH8hF74FH.png?imageslim)是第m个文档中第k个主题出现的次数，这个是一个常数项，这个与我们要求的东西无关，因此直接扔掉。就得到了最后的式子。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ClcE8Hg3aH.png?imageslim)![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/LgFCKKK3gg.png?imageslim)这个词t隶属于第k号主题的个数有多少个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/26F9m15mI2.png?imageslim)是m文档里面第k个主题出现多少次。所以这两个都可以通过样本数出来，而\alpha 和\beta 是已知的，所以这个数是可以算的。所以这个都是可以通过编程实现的。



注意：最初的时候所有词的主题都是随机给的。




OK 刚才我们推出了重要的结论。




但是我们现在只是得到了第i号词主题是第k号的概率是什么，




我们项知道这篇文档的主题分布是什么？




我们也想知道每个主题的词分布是什么？


词分布和主题分布


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/03aehaLcA0.png?imageslim)

在已知了z这个隐变量的时候，我们就可以这么写了![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5iE8b0AFih.png?imageslim)，因为根据马尔科夫毯，我们只要把它的父亲孩子和亲家拿出来，那么它就相对独立了。所以我们现在研究![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2AdJfbg7Ah.png?imageslim)就可以这样研究了。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/41g112J6K2.png?imageslim)是归一化因子。![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ie6A6JmDjC.png?imageslim)是给定一个\alpha 的时候它的主题分布，是一个dirichlet分布。![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/d88B4ij4lm.png?imageslim)是给定一个\theta的时候采样的某一个主题，这个就是一个普通的k项分布。那么k项分布乘以它的共轭分布就得到了一个dirichlet分布。而这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/951CGeLiIg.png?imageslim)的期望就是![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/20L346mjLH.png?imageslim)。

**还是有些不清楚？**




## Gibbs采样算法：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/h5DiG4C1F1.png?imageslim)




## 代码实现：


数目：




  * 文档数目：M


  * 词数目：V(非重复的，“term”)


  * 主题数目：K


记号：


  * 用d表述第几个文档，k表示主题，w表示词汇(term)，n表示词(word)


三个矩阵和三个向量


  * z[d][w]：第d篇文档的第w个词来自哪个主题。M行，X列，X为相应文档长度：即词(可重复)的数目。


  * nw[w][t]：第w个词是第t个主题的次数。word-topic矩阵，列向量nw[][t]表示主题t的词频数分布；V行K列


  * nd[d][t]：第d篇文档中第t个主题出现的次数，doc-topic矩阵，行向量nd[d]表示文档d的主题频数分布。M行，K列。


  * 辅助向量：


    * ntSum[t]：第t个主题在所有语料出现的次数，K维


    * ndSum[d]：第d篇文档中词的数目(可重复)，M维；


    * P[t]：对于当前计算的某词属于主题t的概率，K维。





nw 和 nd 就是这![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0fAA85hHca.png?imageslim)里面的![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/j4L4acjF2j.png?imageslim)和![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5HagcGla4i.png?imageslim)

## Code




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/80fFEAiKa4.png?imageslim)

因为我们的文档里卖弄会有一些停止词，所以 stop_words 里面就是停止词库，然后把停止词过滤掉


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/D63Hd7a4HH.png?imageslim)

把第m篇文档中的第i个词在词典中是第几号，然后做gibbs采样。然后计算主题分布和词分布。

gibbs采样用的是：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/djAHe6Bfm7.png?imageslim)这个公式，

计算主题分布用的是：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jB655b4jiH.png?imageslim)这个公式

计算词分布用的是：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5m0m9JJ5Cb.png?imageslim)这个公式


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/j6bJmgc8iB.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/bBAI972ACh.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/aLLdF3ga4f.png?imageslim)

有人提到了MCMC采样，第一个MC是马尔科夫链，第二个MC是蒙特卡洛，到底什么是MCMC采样？

来一篇新文档的时候，每个主题对应的词分布还是可以使用的，也就是说每个词对应的主题概率也是定的，那么来一个新文档还是可以利用主题的词分布来进行计算的，就不用更多的gibbs采样了。到底怎么做的？

下面说一下超参数的确定：


# 超参数的确定






  * 交叉验证


  * α表达了不同文档间主题是否鲜明，β度量了有多少近义词能够属于同一个类别。


  * 给定主题数目K，可以使用：


    * α=50/K


    * β=0.01


    * 注：不一定普遍适用







## 一种迭代求超参数的方法：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/b17f9IkD3g.png?imageslim)

如果我们有了一个\alpha 值，那么带到![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/jacc4jiAGE.png?imageslim)
这个公式中，总是可以求出新的\alpha 值的。因此，我们可以迭代出来一个\alpha 值，这个就可以作为我的\alpha



实践中有各种各样求超参数的形式，**要自己总结下**



号，现在对LDA总结一下。


# LDA总结：


由于在词和文档之间加入的主题的概念，可以较好的解决一词多义和多词一义的问题。

在实践中发现，LDA用于短文档往往效果不明显——这是可以解释的：因为一个词被分配给某个主题的次数和一个主题包括的词数目尚未敛。往往需要通过其他方案“连接”成长文档。比如：用户评论/Twitter/微博

LDA可以和其他算法相结合。首先使用LDA将长度为Ni的文档降维到K维(主题的数目)，同时给出每个主题的概率(主题分布)，从而可以使用if-idf继续分析或者直接作为文档的特征进入聚类或者标签传播算法——用于社区发现等问题。



LDA对于短文档是效果不明显的。

把这个用户所有的文档连起来或者做其它操作，总之，做成一个比较长的文档，再做LDA

利害，相当于降维。

**这个主题的概率怎么用if-idf来分析？**





LDA 除了可以做文本，也可以做图像，因为图像也可以认为它有主题这种隐藏信息。找下有没有。

这个时候词可能对应图像上的超像素或者可以做特征提取SIFT Harr，然后特征提取的东西再当作词

LDA在各个场合都有人说它不行，比如说采样时间太多，聚类时间太长。

LDA运行的时间还是很长的，比如决策树来处理一万个样本，可能2s就出结果了，SVM可能需要10s，而LDA可能需要分钟，甚至小时才能得出结果









## REF：

1. 七月在线 机器学习
2. [隐含狄利克雷分布 wiki](https://zh.wikipedia.org/wiki/%E9%9A%90%E5%90%AB%E7%8B%84%E5%88%A9%E5%85%8B%E9%9B%B7%E5%88%86%E5%B8%83)
3. [通俗理解LDA主题模型](https://blog.csdn.net/v_july_v/article/details/41209515)
