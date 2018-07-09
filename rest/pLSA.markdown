---
author: evo
comments: true
date: 2018-04-27 08:23:21+00:00
layout: post
link: http://106.15.37.116/2018/04/27/plsa/
slug: plsa
title: pLSA
wordpress_id: 4132
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REF





 	
  1. 七月在线 机器学习


********************************************************************************


# TODO




# MOTIVE


对pLSA做总结，看下是如何把EM算法应用在pLSA上面的。

********************************************************************************


# 为什么要引入隐变量？


朴素贝叶斯的分析


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc786a4f84.png)


因此加入主题这个隐变量。

增加这个隐变量可以一定程度解决问题。

文档和主题：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc792c751f.png)


不管是pLSA还是LDA，具体的做法都非常简单，但是困难的是中间的推理过程。

为什么这个理论是有道理的。

先讲一些pLSA模型。






# pLSA模型


基于概率统计的pLSA模型(probabilistic Latent Semantic Analysis，概率隐语义分析)，增加了主题模型，形成简单的贝叶斯网络，可以使用EM算法学习模型参数。


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7a0b963c.png)


这个就是增加主题的基本的网络模型。这么简单的一个贝叶斯网络就是pLSA模型，分析一下这个样的网络发生什么事情：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7b29b532.png)


每个文档在所有主题上服从多项分布，的意思是：

比如![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2d8b17035d.png)第一篇文档除了是小说，还是关于民国的。

每个主题在所有词项上服从多项分布：

假如词典有V个词，那么主题在所有的词上就服从v项分布。

假设主题有t个，那么每个文档在所有的主题上服从t项分布

**整个文档的生成过程是什么意思？ **




## 现在我们来看一下如何进行这个主题模型的建立




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7b937fdd.png)


对于![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2d901b5553.png)这个解释一下：

由全概率公式得：\(p(w_j)=\sum_{k=1}^{K}p(w_j\mid z_k)*p(z_k)\) 而，这里都加了d而已，由于给定z得时候，d和w是head-to-tail 得关系，也就是说给定z的时候，d和w是独立的即![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2d906a9115.png)，因此![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2d90a5f1ea.png)这个里面的d就省略的。



所以可见有了贝叶斯网络的基本的东西之后

正常而言，还是用极大似然估计


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7c14acac.png)


这个地方解释下，主题有K个，词有M个，文档有N个

可见，这是一个关于w z d 的部分可观测的目标函数，因此使用的最正的办法就是EM算法：

目标函数分析：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7cd400a0.png)


OK，我们先做第一步：写隐变量主题\(z_k\) 的后验概率:


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7e324c52.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2d941d8907.png)这个式子，把d去掉就能看明白了，然后在把d加上就可以理解了。，所以上面。这个就是给定样本之下的隐变量的后验概率





## 然后，我们看看这个怎么求期望的极大值：




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7ee82e7d.png)


看看怎么求的：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc7f5d03a9.png)


写开之后，后面的部分 \(P(d_i)\) 是文档的概率，\(n(d_i,w_j)\) 是联合起来的数出的个数，也就是说后面的部分是常数，因此只看前面的部分。l_{new}

对\(l_{new}\)求期望之后，\(\sum_{k=1}^{K}P(z_k\mid d_i,w_j)\) 就是在给定d w 的情况下的主题的条件概率，就是推EM算法的时候，里面的Qi。没明白？

这么一个式子看着很长，其实是一个带有约束的求极值问题。


## 完成目标函数的建立：




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc8076e7e7.png)


进行求解：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc81853518.png)


注意，我们这里求导的时候只关心第k个主题，第j个词，所以很多的求和符号就都没有了，因为i是文档的，所以要保留

分析第一个等式得到：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc85b2e79b.png)


这个地方的![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da0caecb1.png)带入到上面的![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da0fdecb2.png)里面，就得到了：![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da1369d35.png)。

注意这里的![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da1ba5b8f.png)下面不是m而是j。

利用这个等于0得到主题给定的情况下，词的分布。

同理分析第二个等式


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc8642152b.png)


上面这两个就是M过程。

而![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da492cbe5.png)
就是刚刚在![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da4e7315c.png)
这里算出来的，所以带入即可。

![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da521f6b2.png)![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da56ce244.png)

这个是给定的样本之下z_k这个主题出现的概率。


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da5a597c5.png)这个就是看一下d_i w_j出现的次数。




上面就是pLSA 的全部的推导过程，还是要自己推一下。再理解一下。




# pLSA的总结


pLSA应用于信息检索、过滤、自然语言处理等领域，pLSA考虑到词分布和主题分布，使用EM算法来学习参数。

虽然推导略显复杂，但最终公式简洁清晰，很符合直观理解，需用心琢磨；此外，推导过程使用了EM算法，也是学习EM算法的重要素材。

实际上pLSA是10年前的产物了，而现在已经全部转向LDA了。



OK，对于pLSA进一步思索：


# pLSA进一步思考：




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abdc88487db0.png)


是的，是不需要先验信息，只需要数出


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae2da8da389e.png)


就行了，其它的全都是假设的，然后迭代出来的。

这个是它的优势，但是也是它的劣势，因为没办法人工干预他。



而对它的进行的改进，就是这个著名的LDA。






# COMMENT



