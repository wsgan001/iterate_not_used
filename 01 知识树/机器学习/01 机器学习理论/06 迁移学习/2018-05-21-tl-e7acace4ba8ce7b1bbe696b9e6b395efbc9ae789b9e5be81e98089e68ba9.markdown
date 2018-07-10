---
author: evo
comments: true
date: 2018-05-21 12:02:04+00:00
layout: post
link: http://106.15.37.116/2018/05/21/tl-%e7%ac%ac%e4%ba%8c%e7%b1%bb%e6%96%b9%e6%b3%95%ef%bc%9a%e7%89%b9%e5%be%81%e9%80%89%e6%8b%a9/
slug: tl-%e7%ac%ac%e4%ba%8c%e7%b1%bb%e6%96%b9%e6%b3%95%ef%bc%9a%e7%89%b9%e5%be%81%e9%80%89%e6%8b%a9
title: TL 第二类方法：特征选择
wordpress_id: 6152
categories:
- 人工智能学习
tags:
- Transfer Learning
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)




# TODO





 	
  * **没明白，需要好好总结下。**





* * *





# INTRODUCTION





 	
  * aaa






\newpage


# 第二类方法：特征选择


特征选择法的基本假设是：源域和目标域中均含有一部分公共的特征，在这部分公共的特征上，源领域和目标领域的数据分布是一致的。因此，此类方法的目标就是，通过机器学习方法，选择出这部分共享的特征，即可依据这些特征构建模型。**为什么可以根据这些特征构建模型？**

特征选择法示意图：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5b02b598a8634.png)





## 核心方法


这个领域比较经典的一个方法是发表在2006年的 ECML-PKDD 会议上，作者提出了一个叫做 SCL 的方法(Structural Correspondence Learning)~\cite{blitzer2006domain}。这个方法的目标就是我们说的，找到两个领域公共的那些特征。作者将这些公共的特征叫做Pivot feature。找出来这些Pivot feature，就完成了迁移学习的任务。

特征选择法中的Pivot feature示意图：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5b02b5d079114.png)


上图展示了Pivot feature的含义。Pivot feature指的是在文本分类中，在不同领域中出现频次较高的那些词。


## 扩展


SCL方法是特征选择方面的经典研究工作。基于SCL，也出现了一些扩展工作。



 	
  * Joint feature selection and subspace learning~\cite{gu2011joint}：特征选择+子空间学习

 	
  * TJM (Transfer Joint Matching)~\cite{long2014transfer}: 在优化目标中同时进行边缘分布自适应和源域样本选择

 	
  *  FSSL (Feature Selection and Structure Preservation)~\cite{li2016joint}: 特征选择+信息不变性





## 小结





 	
  * 特征选择法从源域和目标域中选择提取共享的特征，建立统一模型

 	
  * 通常与分布自适应方法进行结合

 	
  * 通常采用稀疏表示 \(||\mathbf{A}||_{2,1}\) 实现特征选择






















* * *





# COMMENT



