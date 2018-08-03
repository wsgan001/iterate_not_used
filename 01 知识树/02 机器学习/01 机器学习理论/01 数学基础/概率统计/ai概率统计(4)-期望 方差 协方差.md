---
author: evo
comments: true
date: 2018-04-06 00:04:49+00:00
layout: post
link: http://106.15.37.116/2018/04/06/ai-probability-statistics-expected-variance-covariance/
slug: ai-probability-statistics-expected-variance-covariance
title: ai概率统计(4)-期望 方差 协方差
wordpress_id: 3414
categories:
- 随想与反思
tags:
- '@todo'
- '@want_to_know'
---

<!-- more -->

[mathjax]


# 缘由：


对期望，方差，协方差进行总结。


# 期望：




## 期望的概念：






  * 离散值：\(E(x)=\sum_{i} x_ip_i\)


  * 连续型：\(E(X)=\int_{-\infty}^{\infty}xf(x)dx\)


即：概率加权下的"平均值"。E 即 expection


## 期望的性质：


无条件成立：




  * \(E(kX)=kE(x)\)


  * \(E(X+Y)=E(x)+E(Y)\) 注意，这个是无条件成立的，没有独立或者不相关的要求。


若X和Y相互 **独立**：




  * \(E(XY)=E(X)E(Y)\) **一直没明白XY的乘积是什么？是对应点进行乘积吗？乘积的期望是什么？**


反之不成立，实际上如果\(E(XY)=E(X)E(Y)\)，只能说明X和Y不相关

**不相关与独立的区别是什么？非常想知道？**


## 一道题目：


从1,2,3,......,98,99,2015这100个数中任意选择若干个数(可能为0个数)求异或，试求异或的期望值。

解答：

首先，对于任何一个二进制位：取奇数个1异或后会得到1，取偶数个1异或后会得到0，可见异或值与取0的个数无关，只与1的个数有关。

给定的最大数\(2015=(11111011111)_2\) ，共11位

针对每一位分别计算，考虑第 i 位\(X_i\)，假定给定的100个数中第 i 位一共有 N 个 1，M 个 0，某次采样取到的 1 的个数为 k 。则有：

\[E(P\{X_i=1\})=\frac{2^m\cdot \sum_{k\in odd} C_n^k}{2^{m+n}}=\frac{\sum_{k\in odd} C_n^k}{2^n}=\frac{1}{2}\]

**这个式子的前半部分没大明白？**

**为什么这个地方是1/2？奇数和偶数对应的和是1/2？为什么？后半段的式子之所以等于1/2是因为之前在讲二项式展开定理的时候，奇数的和偶数的 \(C_n^k\) 是相等的，而且加起来等于 \(2^n\) 。**

**取到的1的个数是k，那么总个数不用管吗？为什么这个地方是这样的？ 求和是对谁求和？\(2^m\)是什么？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/IC3KB2ALeA.png?imageslim)

**这里使用E(x+y)=E(x)+E(y) 来将累加和提取到外面**

**这个例子没大看明白，真的是这样的吗？要自己动手试下。**

模拟如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/caEe1f4Fid.png?imageslim)




# 2.方差




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/GfC41FdL9d.png?imageslim)




# 3.协方差




## 协方差的定义与性质




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/i6HI53BhKm.png?imageslim)




## 协方差和独立、不相关


X和Y独立时\( E(XY)=E(X)E(Y)\)，而 \(Cov(X,Y)=E(XY)-E(X)E(Y)\)，因此，当X和Y独立时，\(Cov(X,Y)=0\)

但X和Y独立这个前提太强，因此我们定义：若Cov(X,Y)=0，称X和Y不相关。**注意，不相关的定时是从协方差这个地方来的。**

**独立与不相关的定义区别：独立是联合概率的期望等于每个的期望的乘积，相关是二者的协方差为0。**


## 协方差的意义：


协方差是两个随机变量具有相同方向变化趋势的度量：




  * 若Cov(X,Y)>0，它们的变化趋势相同


  * 若Cov(X,Y)<0，它们的变化趋势相反


  * 若Cov(X,Y)=0，称X和Y不相关。




## 协方差的上界


两个随机变量的协方差，是否有上界？




  * \(若\,Var(X)=\sigma_1^2\; Var(Y)=\sigma_2^2\)


  * \(则\,\mid Cov(X,Y)\mid \leq \sigma_1\sigma_2\)


注意，上述当且仅当X和Y之间有线性关系时，等号成立。这个时候时最相关的。


## 再谈独立与不相关


因为上述定理的保证，使得“不相关”事实上即“线性独立”。即：若X与Y不相关，说明X与Y之间没有线性关系(但有可能存在其他函数关系)，不能保证X和Y相互独立。**不相关不代表没有关系，只是没有线性关系，这个说法对吗？比如一个二次函数与x是不是相关的？还想跟多了解下独立与不相关**

注意，有个特殊的，对于二维正态随机变量，X与Y不相关等价于X与Y相互独立。**在了解下这个是怎么互推的。**


## 相关系数：


定义：

\[\rho_{XY}=\frac{Cov(X,Y)}{\sqrt{Var(X)Var(Y)}}\]

由协方差上界定理可知 \(\mid \rho\mid\leq 1\)。而且当且仅当X与Y有线性关系时，等号成立

容易看到，相关系数相当于是标准尺度下的协方差。上面关于协方差与XY相互关系的结论，完全适用于相关系数和XY的相互关系。


## 协方差矩阵


对于n维随机向量\((X_1,X_2,\cdots X_n)\)，任意两个随机变量\(X_i\)和\(X_j\)都可以得到一个协方差，从而形成一个\(n*n\)的矩阵，显然，协方差矩阵是对称阵。**对称阵会带来很多的好处，比如对称阵可以求出不同的特征值和特征向量，对称阵的特征向量是正交的。**

\[c_{ij}=E\{[X_i=E(X_i)]\left\lfloor X_j-E(X_j) \right\rfloor\}=Cov(X_i,X_j)\]

\[C= \begin{bmatrix} c_{11}& c_{12} & \cdots & c_{1n}\\ c_{21}& c_{22} & \cdots & c_{2n}\\ \vdots & \vdots &\ddots &\vdots \\ c_{n1}& c_{n2} &\cdots & c_{nn} \end{bmatrix}\]

协方差矩阵在降维处理和主成分分析中会用到，**怎么用的？**






# COMMENT：


**还需要再整理下。**


# REF：






  1. 七月在线 机器学习
