---
author: evo
comments: true
date: 2018-05-16 16:45:54+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e5%bc%ba%e5%8c%96%e5%ad%a6%e4%b9%a0%e8%af%bb%e4%b9%a6%e7%ac%94%e8%ae%b0-14-%e5%bf%83%e7%90%86%e5%ad%a6/
slug: rl-%e5%bc%ba%e5%8c%96%e5%ad%a6%e4%b9%a0%e8%af%bb%e4%b9%a6%e7%ac%94%e8%ae%b0-14-%e5%bf%83%e7%90%86%e5%ad%a6
title: RL 心理学
wordpress_id: 5899
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


# [强化学习读书笔记 - 14 - 心理学](http://www.cnblogs.com/steven-yang/p/6636358.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa









## 停在这里了


从这一章开始叫做Looking Deeper。
讲的有心理学(Psychology)，神经科学(Neuroscience) 和强化学习的联系，
还有强化学习的应用和案例(Applications and case studies)和前沿(Frontiers)。

基本上需要大量的翻译。这不是我的特长。
**所以我的笔记先停在这里了。**


## 心理学(Psychology)




## 术语





 	
  * reinforcement
在心理学中，指动物接收到一个刺激（或者经历一个刺激的消失），行为和另一个刺激（或者反应）的关联模式得到了（强度或者频率上的）加强。

 	
  * reinforcer - 强化刺激

 	
  * reward - 奖赏
让动物认知好行为的事物或者事件。

 	
  * penalty - 惩罚
让动物认知坏行为的事物或者事件。

 	
  * reinforcement signal - 加强信号
加强信号的一个例子：TD error。

 	
  * action

 	
  * control
在强化学习中，控制是指本体影响它的环境，带来期望的状态或者事件。

 	
  * stimulus-response learning - 刺激-反应学习

 	
  * prediction algorithm

 	
  * control algorithm
Policy improvement algorithms

 	
  * unconditioned responses

 	
  * unconditioned stimulus

 	
  * conditioned responses

 	
  * conditioned stimulus

 	
  * classical conditioning - 条件反射




# 算法列表


2
A simple bandit algorithm
4
Iterative policy evaluation
Policy iteration (using iterative policy evaluation)
Value iteration
5
First-visit MC policy evaluation (returns V  v)
Monte Carlo ES (Exploring Starts)
On-policy rst-visit MC control (for "-soft policies)
Incremental o-policy every-visit MC policy evaluation
O-policy every-visit MC control (returns   )
6
Tabular TD(0) for estimating v
Sarsa: An on-policy TD control algorithm
Q-learning: An o-policy TD control algorithm
Double Q-learning
7
n-step TD for estimating V  v
n-step Sarsa for estimating Q  q, or Q  q for a given 
O-policy n-step Sarsa for estimating Q  q, or Q  q for a given 
n-step Tree Backup for estimating Q  q, or Q  q for a given 
O-policy n-step Q() for estimating Q  q, or Q  q for a given 
8
Random-sample one-step tabular Q-planning
Tabular Dyna-Q
Prioritized sweeping for a deterministic environment
9
Gradient Monte Carlo Algorithm for Approximating ^v  v
Semi-gradient TD(0) for estimating ^v  v
n-step semi-gradient TD for estimating ^v  v
LSTD for estimating ^v  v (O(n2) version)
10
Episodic Semi-gradient Sarsa for Control
Episodic semi-gradient n-step Sarsa for estimating ^q  q, or ^q  q
Dierential Semi-gradient Sarsa for Control
Dierential semi-gradient n-step Sarsa for estimating ^q  q, or ^q  q
12
Semi-gradient TD() for estimating ^v  v
True Online TD() for estimating >  v
13
REINFORCE, A Monte-Carlo Policy-Gradient Method (episodic)
REINFORCE with Baseline (episodic)
One-step Actor-Critic (episodic)
Actor-Critic with Eligibility Traces (episodic)
Actor-Critic with Eligibility Traces (continuing)



















* * *





# COMMENT



