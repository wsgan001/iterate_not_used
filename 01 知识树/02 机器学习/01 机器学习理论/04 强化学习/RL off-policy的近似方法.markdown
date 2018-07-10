---
author: evo
comments: true
date: 2018-05-16 16:41:16+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-off-policy%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95/
slug: rl-off-policy%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95
title: RL off-policy的近似方法
wordpress_id: 5891
categories:
- 人工智能学习
tags:
- NOT_ADD
- Reinforcement Learning
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. 


# [强化学习读书笔记 - 11 - off-policy的近似方法](http://www.cnblogs.com/steven-yang/p/6536742.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa







## off-policy的近似方法




尽管可以使用第6,7章的方法，修改成为off-policy的近似方法，但是效果不好。  

主要原因是：行为策略的分布和目标策略的分布不一致。




off-policy的近似方法的研究现在处于领域的前沿。主要有两个方向：






  * 使用重要样本的方法，扭曲样本的分布成为目标策略的分布。这样就可以使用半梯度递减方法收敛。


  * 开发一个真正的梯度递减方法，这个方法不依赖于任何分布。




**原书这章还远远没有写完！**  

这章先停在这里了。
























* * *





# COMMENT



