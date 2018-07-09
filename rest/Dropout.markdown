---
author: evo
comments: true
date: 2018-04-28 10:09:31+00:00
layout: post
link: http://106.15.37.116/2018/04/28/dropout/
slug: dropout
title: Dropout
wordpress_id: 4478
categories:
- 随想与反思
tags:
- '@todo'
---

<!-- more -->

[mathjax]


# REF





 	
  1. aaa




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *






# 4.Dropout（随机失活）




## Dropout ( 随机失活) ： 别一次开启所有学习单元




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac1e4f8cd72b.png)





### 代码如下：




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac1e55a2fe5f.png)


H1是一个ReLU层，U1生成H1相同shape的随机数，并且随机数小于0.5时为False，大于0.5时为True。然后H1*=U1就得到了过滤后的H1。

但是predict的时候，这个信息过不过去的数学期望就是这个值乘以p。**为什么？为什么这个地方讲数学期望了？**






## 实际实现 ：


上面的方式存在一个问题，用户只关心predict的速度，不关心你训练用了多长时间。

所以，可以把预测阶段的概率p转移到训练阶段。注意，是以数学期望的形式转移的。**还是有点不明白，而且，一个Ture False的矩阵除以p是什么意思？**


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac1e5adb9cee.png)





## Dropout理解：


防止过拟合 的第1种理解方式：



 	
  * 别让你的神经⽹网络记住那么多东西( ( 虽然 CNN 记忆⼒力好? )?

 	
  * 就是⼀一只猫而已 ，要有一些泛化能力




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac1e606222c7.png)


从上面的图可看出，有部分信息是冗余的。

防止过拟合的第2 种理解方式 ：



 	
  * 每次都关掉一部分感知器 ， 就可以看作一个新模型 ，那么多个模型对最后的结果做预测会降低overfitting的风险。




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac1e695ed4a0.png)








* * *





# COMMENT


对 Dropout 想要有更细致的了解 ， 参见：



 	
  * 2014, Hinton, etc《 Dropout: A Simple Way to Prevent Neural Networks from Overfitting》

 	
  * 2013, Stefan Wager, etc 《Dropout Training as Adaptive Regularization》


**将参考文献的内容补充进来，这个讲的还是不明确。**
