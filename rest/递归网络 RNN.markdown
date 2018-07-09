---
author: evo
comments: true
date: 2018-03-30 14:04:31+00:00
layout: post
link: http://106.15.37.116/2018/03/30/rnn-2/
slug: rnn-2
title: 递归网络 RNN
wordpress_id: 2000
categories:
- 随想与反思
tags:
- '@todo'
- '@want_to_know'
---

<!-- more -->

[mathjax]



**这部分的视频没有怎么看，也要整合到RNN那篇文章里面去。**




# 递归网络（RNN）





## 为什么要用RNN


我们之前的大部分算法处理的都是IID的数据，即独立同分布的数据，这类数据可以处理成分类问题，回归问题，特征表达问题。

但是我们很多的数据是不满足IID的要求的，特别是序列数据：



 	
  * 序列分析（Tagging，Annotation)

 	
  * 序列⽣生成，如语⾔言翻译，⾃自动⽂文本⽣生成

 	
  * 内容提取（Content Extraction），如图像描述


这些问题很难用前面的这些只有单向的神经网络来分析，比如前向全连接，或者卷积网络这种前向部分连接的。

处理序列，只有前向是不够的。


## 序列样本的几种形态




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe3ef9b08bc.png)






 	
  * one to one 分类 **没明白？**

 	
  * one to many 信号的生成

 	
  * many to one 序列的分类 输入一串序列，得到一个向量

 	
  * many to many 有时延的多对多

 	
  * many to many 没有时延的多对多


RNN不仅能够处理序列输出，也能得到序列输出，这里序列指的是向量的序列。

RNN学习出来的是程序，不是函数。**什么意思？ 神经网络前向的可以认为是一个函数，相同的输入一定会得到相同的输出，但是RNN学出来的可以认为是一个程序，它是有状态的，不同时候的相同输入可能会得到不同的结果。这种行为很像程序。利害的。**




## 序列预测：


模型输入的是时间变化向量序列： \(x_{t-2} , x_{t-1} , x_t , x_{t+1} , x_{t+2}\) ，然后在 t 时刻通过模型来估计 \(x_{t+1}=f(x_t,...,x_{t-\tau })\)。

但是存在这两个问题：



 	
  * 真实的这个 f 是非常难以建模和观察的，因为它有内部状态，而我只有输入的向量。

 	
  * 对长时间范围的场景 (context) 也是难以建模和观察


那么我们怎么来设计一个 f' 来逼近这个真实的 f 呢？我们可以引⼊入内部隐含状态变量：**嗯，感觉与HMM有些类似，仔细感受下。为什么这个是可行的？**

即假设 \(x_{t+1 }\)  除了与之前的 x 有关，还与之前的状态变量 z 有关：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5adf07fc3ef73.png)


而 z 本身也是与之前的 x 和隐含状态有关的：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5adf082747991.png)




那么这两行就已经形成了一个递归的方式。





## 序列预测模型


我们知道之前的模型是关于t的，但是我们可以按照时间为横坐标展开成一个固定的网络结构。


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe3ff8e404a.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe400d58e2a.png)


h_t里面的维度对应到全连接网络里面的hidden-unit number ，因此这个h的维度是与模型的复杂度直接相关的。


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe401ae2483.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe40266352a.png)





## RNN训练：


前向计算，相同的W矩阵需要乘以多次

多步之前的输入x，会影响当前的输出

在后向计算的时候，同样相同的矩阵也会乘以多次


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe404724c07.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe404b9fe70.png)





### BPTT算法-BackProp Through Time


RNN前向计算


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe406e4d912.png)


计算W的偏导，需要把所有Time Step加起来


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe407695841.png)


应⽤用链式规则


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe407e64564.png)





### BPTT算法：计算实现




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe4194a84d6.png)


BPTT算法：梯度 vanishing/exploding 现象分析


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe41a7af5ef.png)


BPTT算法：解决⽅方案


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe41b8115aa.png)


Long Term Memory?


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe41c075f5e.png)





### LSTM（ Long Short Term Memory)


应⽤用最为广泛、成功的RNN


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe41de0f9cf.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe41e5882f1.png)





## LSTM：cell state


可以长期保存某个状态，cell state值通过forget gate控制实现保留多少“老”的状态

Layer 把 输入维度x变成输出维度h


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe420e57364.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe4218470c4.png)


×处为gate

σ处为sigmoid Layer with weights and bias


## LSTM: forget / input unit




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe425f6c969.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe42637cba6.png)


LSTM: update cell


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe426bccc8c.png)![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe42804f61d.png)


LSTM: output


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe427324944.png)![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe429ad42e5.png)


LSTM


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe42a696143.png)![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe42b7ee911.png)





## LSTM其他变形




### Gated Recurrent Unit




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe42c83b176.png)





## LSTM的训练


不需记忆BP公式，使用层次关系，可以简单开发出BPTT算法

LSTM 具备一定抑制梯度 vanishing/exploding 特性


## 使⽤用LSTM





 	
  * 将多个LSTM单元组合为层

 	
  * 网络中有多层

 	
  * 复杂的结构能够处理更大范围的动态性








![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe433c9519a.png)





## RNN算法应用





 	
  * 手写文字输出：

 	
    * http://www.cs.toronto.edu/~graves/handwriting.html




 	
  * 文本生成

 	
  * 机器翻译

 	
  * 非常好的介绍文章：http://karpathy.github.io/2015/05/21/rnn-effectiveness/




## RNN实验1：基于Torch构建RNN网络





 	
  * 使⽤用nngraph模块可以构造复杂的有向图

 	
  * BPTT算法依旧基于nn模块提供的BP算法

 	
  * 各种optim算法依旧适用RNN




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abe438ce9132.png)





## RNN实验2：char_cnn分析





 	
  * 基于字符的⽂文本⽣生成⼯工具

 	
    * https://github.com/karpathy/char-rnn

 	
    * 生成汪峰⻛风格的歌词: https://github.com/phunterlau/wangfeng-rnn

 	
    * 莫奈⻛风格绘画⽣生成：http://blog.manugarri.com/teaching-recurrent-neural-networks-about-monet/








# COMMENT：


**关于RNN的部分没有看，要看完同时配合书或者文章梳理一下。**

**很多东西需要补充和拆分。**
