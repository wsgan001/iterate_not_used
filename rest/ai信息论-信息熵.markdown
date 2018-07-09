---
author: evo
comments: true
date: 2018-04-05 07:21:15+00:00
layout: post
link: http://106.15.37.116/2018/04/05/information-entropy/
slug: information-entropy
title: ai信息论-信息熵
wordpress_id: 3360
categories:
- 随想与反思
tags:
- '@NULL'
---

<!-- more -->

[mathjax]


# REF





 	
  1. 《机器学习 实践应用》




# TODO





 	
  * **如果小狗生了一条小狗，那么信息熵比较小，如果小狗生了一匹小马，那么信息熵比较大？**




# MOTIVE





 	
  * 到底什么是信息熵？





* * *






# 信息熵的定义


在信源重考虑的不是某一单个符号发生的不确定性，而是要考虑这个信源缩有可能发生情况的平均不确定性。若信源符号有n种可能 \(U_1\)， \(U_2\)， \(U_3\)， \(U_4\cdots \)每种可能对应的概率分别为\(P_1\)， \(P_2\)， \(P_3\)， \(P_4\cdots \) ，且各种符号的出现彼此独立。信源的平均不确定性应当为单个符号不确定性\(-log\,P_i\)的统计平均值 E，即\(H(U)=E[-log\,P_i]=-\sum_{i1}^{n}P_ilog\,P_i\)。

OK，举个例子来说明什么是信息熵：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ae5515a46082.png)


OK，我们先来求特征列的信息熵，特征列功3种颜色，有5个样本，则，最终的特征列信息熵计算如下：

\[H(U)=-[\frac{2}{5}\times log(\frac{2}{5})+\frac{2}{5}\times log(\frac{2}{5})+\frac{1}{5}\times log(\frac{1}{5})]\]



















* * *





# COMMENT





