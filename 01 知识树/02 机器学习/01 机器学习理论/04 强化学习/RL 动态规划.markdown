---
author: evo
comments: true
date: 2018-05-16 16:29:04+00:00
layout: post
link: http://106.15.37.116/2018/05/17/5880/
slug: '5880'
title: RL 动态规划
wordpress_id: 5880
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


# [强化学习读书笔记 - 04 - 动态规划](http://www.cnblogs.com/steven-yang/p/6493328.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




**动态规划(Dynamic Programming)** - 计算最优策略的一组算法。


## 策略


强化学习的一个主要目的是：找到最优策略。
我们先要明白什么是策略？
策略告诉主体(agent)在当前的状态下，应该选择哪个行动。
我们稍微数据化上面的说法，变成：
**策略告诉主体(agent)在每个状态\(s\)下，选择行动\(a\)的可能性。**

脑补一下：想象一个矩阵：
每一行代表一个state，
每一列代表一个action，
单元的值是一个取值区间为\([0, 1]\)的小数，代表对应状态-行动的选择概率。


## 最优策略(Optimal Policy)


最优策略是可以取得最大的长期奖赏的策略。
**长期奖赏就是\(G_t\)**
因此，我们需要对策略进行价值计算。计算的方法在[强化学习读书笔记 - 03 - 有限马尔科夫决策过程](http://www.cnblogs.com/steven-yang/p/6480666.html)讲了。
有两个计算公式：一个是策略的状态价值公式，一个是策略的行动价值公式。
**策略的状态价值公式**有利于发现哪个状态的价值高。也就是找到最优状态。
**策略的行动价值公式**有利于发现（在特定状态下）哪个行动的价值高。也就是找到最优行动。


## 通用策略迭代(Generalized Policy Iteration)


动态规划的基本思想 - 通用策略迭代是：



 	
  1. 先从一个策略\(\pi_0\)开始，

 	
  2. 策略评估(Policy Evaluation) - 得到策略\(\pi_0\)的价值\(v_{\pi_0}\)

 	
  3. 策略改善(Policy Improvement) - 根据价值\(v_{\pi_0}\)，优化策略为\(\pi_0\)。

 	
  4. 迭代上面的步骤2和3，直到找到最优价值\(v_*\)，因此可以得到最优策略\(\pi_*\)（终止条件：得到了稳定的策略\(\pi\)和策略价值\(v_{pi}\)）。


这个被称为通用策略迭代(Generalized Policy Iteration)。
数学表示如下：
\[
\pi_0 \xrightarrow{E} v_{\pi_0} \xrightarrow{I} \pi_1 \xrightarrow{E} v_{\pi_1} \xrightarrow{I} \pi_2 \xrightarrow{E} \cdots \xrightarrow{I} \pi_* \xrightarrow{E} v_*
\]

因此，我们需要关心两个问题：如何计算策略的价值，以及如何根据策略价值获得一个优化的策略。


## 策略迭代(Policy Iteration)的实现步骤


步骤如下：请参照书上的图4.1。



 	
  1. 初始化 - 所有状态的价值（比如：都设为0）。
所有的状态\(\mathcal{S} = \{ s_0, s_1,...,s_n\}\)是一个集合。
数学表示：\(\vec{V_0(s)} = [0, \dots, 0]\)

 	
  2. 初始化 - 一个等概率随机策略\(\pi_0\) (the equiprobable random policy)
**等概率随机策略** - 意味着每个行动的概率相同。
数学表示：
\[
\pi = \begin{bmatrix}
\dots & \dots & \dots \\
\dots & \pi(s, a) & \dots \\
\dots & \dots & \dots \\
\end{bmatrix} \\
where \\
\pi \text{ - a matrix for each state s and action a} \\
\pi(s, a) =
\begin{cases}
\frac{1}{N_a}, \text{a is selected under state s by } \pi \\
0, otherwise \\
\end{cases} \\
N_a \text{ - the count of actions selected under state s by } \pi
\]
矩阵\(\pi\)就是我们的策略，我们反过来看，如果一个单元的值不是0，说明该策略选择了这个行动，如果为0，说明该策略不选择这个行动。
初始的时候：一个状态\(s\)对应的所有可能行动\(a\)，都是有值的。
**关键理解： 找到最优策略的过程就是优化矩阵\(\pi\) - 减少每个状态\(s\)选的行动\(a\)**。

 	
  3. 策略迭代 - 策略评估过程
根据\(\pi\)计算状态价值\(\vec{V_{k+1}(s)}\)
迭代策略评估公式 - iterative policy evaluation - Bellman update rule
\[
\begin{align}
v_{k+1}(s)
& = \mathbb{E}_{\pi} \left [ R_{t+1} + \gamma v_k(S_{t+1}) \ | \ S_t = s \right ] \\
& = \sum_{a} \pi(a|s) \sum_{s',r} p(s',r|s,a) \left [ r + \gamma v_{k}(s') \right], \ \forall s \in \mathcal{S}
\end{align}
\]

 	
  4. 策略迭代 - 策略优化过程
根据状态价值\(\vec{V_{k+1}(s)}\)，优化策略\(\pi\)。
**关键： 优化方法 - 对于每个状态\(s\)，只保留可达到最大状态价值的行动**。
举例说明：
你是一个初级程序员(5)，你有4个选择：成为A: 架构师(10)，B: 项目经理(10)，C: 测试(8)，D: 运营(8)。
括号里的是状态价值。由于架构师(10)，项目经理(10)的价值最大。
所以，只保留行动A和B。


数学表示：
\[
\begin{align}
\pi'(s)
& = \underset{a}{argmax} \ q_{\pi}(s, a) \\
& = \underset{a}{argmax} \ \sum_{s', r} p(s',r|s,a) \left [ r + \gamma v_{\pi}(s') \right ] \\
\end{align} \\
\because q_{\pi}(s, \pi'(s)) \ge v(\pi), \ \forall s \in \mathcal{S} \\
\therefore v_{\pi}'(s) \ge v_{\pi}(s) \\
v_{\pi}(s) = v_{\pi}'(s) \\
where \\
\pi'(s) \text{ - action(s) selected under the state s by policy } \pi'
\]
注意：这是一个贪恋的策略(greedy policy)，因为只做了**一步**价值计算。



 	
  1. 迭代结束条件 - 得到了稳定的策略\(\pi\)和策略价值\(v_{pi}\)
策略\(\pi\)稳定 - 即\(\pi_{k+1} = \pi_k\)。




## 策略评估公式说明


下面这个是第三章讲的策略状态价值公式：
\[
\begin{align}
v_{\pi}(s)
& \doteq \mathbb{E}_{\pi} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} | S_t = s \right ] \\
& = \sum_{a} \pi(a|s) \sum_{s',r} p(s',r|s,a) \left [ r + \gamma v_{\pi}(s') \right], \ \forall s \in \mathcal{S}
\end{align}
\]
可以看出状态\(s\)在策略\(v_pi\)上的价值是由其它状态\(s'\)在策略\(v_pi\)的价值决定的。
简单地想一想，就会发现这个公式难以（不能）被实现。

因此：我们使用了一个迭代的公式：
迭代策略评估公式 - iterative policy evaluation - Bellman update rule
\[
\begin{align}
v_{k+1}(s)
& = \mathbb{E}_{\pi} \left [ R_{t+1} + \gamma v_k(S_{t+1}) \ | \ S_t = s \right ] \\
& = \sum_{a} \pi(a|s) \sum_{s',r} p(s',r|s,a) \left [ r + \gamma v_{k}(s') \right], \ \forall s \in \mathcal{S}
\end{align}
\]
这个公式和策略状态价值公式很像。
仔细比较一下，就会发现这个公式的\(v_{k+1}(s)\)是由\(v_{k}(s')\)计算得到的。
这就有了可行性。为什么呢？因为我们可以定义\(v_0(s) = 0, \ \forall s \in \mathcal{S}\)。
这样就可以计算\(v_1(s), \ \forall s \in \mathcal{S}\)，以此类推，经过多次迭代(\(k \to \infty\))， \(v_k \cong v_{\pi}\)。


## 价值迭代(Value Iteration)


价值迭代方法是对上面所描述的方法的一种简化：
在策略评估过程中，对于每个状态\(s\)，只找最优(价值是最大的)行动\(a\)。这样可以减少空间的使用。



 	
  1. 初始化 - 所有状态的价值（比如：都设为0）。

 	
  2. 初始化 - 一个等概率随机策略\(\pi_0\) (the equiprobable random policy)

 	
  3. 策略评估
对于每个状态\(s\)，只找最优(价值是最大的)行动\(a\)。
数学表示：
简化策略评估迭代公式
\[
\begin{align}
v_{k+1}(s)
& \doteq \underset{a}{max} \ \mathbb{E} \left [ R_{t+1} + \gamma v_k(S_{t+1}) \ | \ S_t = s , A_t = a\right ] \\
& = \underset{a}{max} \ \sum_{s',r} p(s',r|s,a) \left [ r + \gamma v_{k}(s') \right]
\end{align} \\
where \\
\underset{a}{max}(.) \text{ - get the max value } \forall a \in \mathcal{A(s)}
\]

 	
  4. 策略优化
没有变化。




## 总结


通用策略迭代(DPI)是一个强化学习的核心思想，影响了几乎所有的强化学习方法。
通用策略迭代(DPI)的通用思想是：两个循环交互的过程，迭代价值方法（value function）和迭代优化策略方法。

动态规划(DP)对复杂的问题来说，可能不具有可行性。主要原因是问题状态的数量很大，导致计算代价太大。


## 参照
























* * *





# COMMENT



