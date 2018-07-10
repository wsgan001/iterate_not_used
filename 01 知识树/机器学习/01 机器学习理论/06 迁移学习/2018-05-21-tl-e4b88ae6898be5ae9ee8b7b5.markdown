---
author: evo
comments: true
date: 2018-05-21 13:18:27+00:00
layout: post
link: http://106.15.37.116/2018/05/21/tl-%e4%b8%8a%e6%89%8b%e5%ae%9e%e8%b7%b5/
slug: tl-%e4%b8%8a%e6%89%8b%e5%ae%9e%e8%b7%b5
title: TL JDA例子1：
wordpress_id: 6194
categories:
- 人工智能学习
tags:
- '@NULL'
- Transfer Learning
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [迁移学习简明手册](https://github.com/jindongwang/transferlearning-tutorial)  [王晋东](https://zhuanlan.zhihu.com/p/35352154)

 	
  2. [迁移学习代码实现 总结 王晋东](https://github.com/jindongwang/transferlearning/tree/master/code)




# TODO





 	
  * 没有写，之前的册子上是Matlab代码的，





* * *





# INTRODUCTION





 	
  * aaa







# 上手实践


在本部分，我们以迁移学习中最为流行的图像分类为实验对象，在流行的Office+Caltech10数据集上完成。

在众多的非深度迁移学习方法中，我们选择发表于ICCV-13的 JDA  方法进行实践。实验平台为普通机器上的Matlab软件。

1. 数据获取

由于我们要测试非深度方法，因此，选择 SURF 特征文件作为算法的输入。SURF 特征文件可以[这里](https://pan.baidu.com/s/1bp4g7Av)下载。

下载到的文件主要包含4个.mat文件：Caltech.mat, amazon.mat, webcam.mat, dslr.mat。它们恰巧对应4个不同的领域。彼此之间两两一组，就是一个迁移学习任务。

每个数据文件包含两个部分：fts 为800维的特征，labels 为对应的标注。在测试中，我们选择由Caltech.mat作为源域，由amazon.mat作为目标域。

Office+Caltech10  数据集的介绍可以在本手册的第~\ref{sec-dataset}部分找到。

我们对数据进行加载并做简单的归一化，将最后的数据存入 \(X_s,Y_s,X_t,Y_t\) 这四个变量中。这四个变量分别对应源域的特征和标注、以及目标域的特征和标注。











* * *





# COMMENT



