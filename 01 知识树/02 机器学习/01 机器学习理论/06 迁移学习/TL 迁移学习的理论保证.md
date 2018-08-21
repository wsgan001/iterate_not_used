---
author: evo
comments: true
date: 2018-05-20 15:03:00+00:00
layout: post
link: http://106.15.37.116/2018/05/20/tl-%e8%bf%81%e7%a7%bb%e5%ad%a6%e4%b9%a0%e7%9a%84%e7%90%86%e8%ae%ba%e4%bf%9d%e8%af%81/
slug: tl-%e8%bf%81%e7%a7%bb%e5%ad%a6%e4%b9%a0%e7%9a%84%e7%90%86%e8%ae%ba%e4%bf%9d%e8%af%81
title: TL 迁移学习的理论保证
wordpress_id: 6118
categories:
- 人工智能学习
tags:
- '@todo'
- Transfer Learning
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)




# TODO





 	
  * **要把涉及到的几篇论文都总结进来。要完全的弄清楚为什么迁移学习是科学的。**





* * *





# INTRODUCTION





 	
  * aaa




**看到这篇的，可以以后再看。**




## 迁移学习的理论保证


\textit{
本部分的标题中带有*号，有一些难度，为可看可不看的内容。此部分最常见的形式是当自己提出的算法需要理论证明时，可以借鉴。}

在第一章里我们介绍了两个重要的概念：迁移学习是什么，以及为什么需要迁移学习。但是，还有一个重要的问题没有得到解答：为什么可以进行迁移?也就是说，迁移学习的可行性还没有探讨。

值得注意的是，就目前的研究成果来说，迁移学习领域的理论工作非常匮乏。我们在这里仅回答一个问题：为什么数据分布不同的两个领域之间，知识可以进行迁移？或者说，到底达到什么样的误差范围，我们才认为知识可以进行迁移？

加拿大滑铁卢大学的Ben-David等人从2007年开始，连续发表了三篇文章~\cite{ben2007analysis,blitzer2008learning,ben2010theory}对迁移学习的理论进行探讨。在文中，作者将此称之为“Learning from different domains”。在三篇文章也成为了迁移学习理论方面的经典文章。文章主要回答的问题就是：在怎样的误差范围内，从不同领域进行学习是可行的？

学习误差： 给定两个领域 \(\mathcal{D}_s,\mathcal{D}_t\) ， \(X\) 是定义在它们之上的数据，一个假设类 \(\mathcal{H}\) 。则两个领域 \(\mathcal{D}_s,\mathcal{D}_t\) 之间的 \(\mathcal{H}\) -divergence被定义为

\begin{equation}
\hat{d}_{\mathcal{H} }(\mathcal{D}_s,\mathcal{D}_t) = 2 \sup_{\eta \in \mathcal{H} } \left|\underset{\mathbf{x} \in \mathcal{D}_s}{P}[\eta(\mathbf{x}) = 1] - \underset{\mathbf{x} \in \mathcal{D}_t}{P}[\eta(\mathbf{x}) = 1] \right|
\end{equation}

因此，这个 \(\mathcal{H}\) -divergence依赖于假设 \(\mathcal{H}\) 来判别数据是来自于 \(\mathcal{D}_s\) 还是 \(\mathcal{D}_t\) 。作者证明了，对于一个对称的 \(\mathcal{H}\) ，我们可以通过如下的方式进行计算

\begin{equation}
d_\mathcal{H} (\mathcal{D}_s,\mathcal{D}_t) = 2 \left(1 - \min_{\eta \in \mathcal{H} } \left[\frac{1}{n_1} \sum_{i=1}^{n_1} I[\eta(\mathbf{x}_i)=0] + \frac{1}{n_2} \sum_{i=1}^{n_2} I[\eta(\mathbf{x}_i)=0]\right] \right)
\end{equation}
其中 \(I[a]\) 为指示函数：当 \(a\) 成立时其值为1,否则其值为0。

在目标领域的泛化界：

假设 \(\mathcal{H}\) 为一个具有 \(d\) 个VC维的假设类，则对于任意的 \(\eta \in \mathcal{H}\) ，下面的不等式有 \(1 - \delta\) 的概率成立：

\begin{equation}
R_{\mathcal{D}_t}(\eta) \le R_s(\eta) + \sqrt{\frac{4}{n}(d \log \frac{2en}{d} + \log \frac{4}{\delta})} + \hat{d}_{\mathcal{H} }(\mathcal{D}_s,\mathcal{D}_t) + 4 \sqrt{\frac{4}{n}(d \log \frac{2n}{d} + \log \frac{4}{\delta})} + \beta
\end{equation}
其中
\begin{equation}
\beta \ge \inf_{\eta^\star \in \mathcal{H} } [R_{\mathcal{D}_s}(\eta^\star) + R_{\mathcal{D}_t}(\eta^\star)]
\end{equation}
并且
\begin{equation}
R_{s}(\eta) = \frac{1}{n} \sum_{i=1}^{m} I[\eta(\mathbf{x}_i) \ne y_i]
\end{equation}

具体的理论证明细节，请参照上述提到的三篇文章。

在自己的研究中，如果需要进行相关的证明，可以参考一些已经发表的文章的写法，例如~\cite{long2014adaptation}等。

另外，英国的Gretton等人也在进行一些学习理论方面的研究，有兴趣的读者可以关注他的个人主页：\url{http://www.gatsby.ucl.ac.uk/~gretton/}。























* * *





# COMMENT



