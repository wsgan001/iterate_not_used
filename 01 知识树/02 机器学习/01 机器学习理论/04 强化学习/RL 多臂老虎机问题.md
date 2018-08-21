---
author: evo
comments: true
date: 2018-05-16 16:19:21+00:00
layout: post
link: http://106.15.37.116/2018/05/17/5872/
slug: '5872'
title: RL 多臂老虎机问题
wordpress_id: 5872
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


[强化学习读书笔记 - 02 - 多臂老O虎O机问题](http://www.cnblogs.com/steven-yang/p/6476034.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





## 数学符号的含义





 	
  * 通用
\(a\) - 行动(action)。
\(A_t\) - 第t次的行动(select action)。通常指求解的问题。

 	
  * 在老O虎O机问题中
\(q_*(a)\) - 行动 a 的真实奖赏(true value)。这个是（实际中）不可知的。期望计算的结果收敛(converge)与它。
\(N_t(a)\) - 在第t次之前，行动a被选择的次数。
\(R_t\) - 第t步的实际奖赏(actual reward)。
\(Q_t(a)\) - 行动 a 在第t次前（不包括第t次）的实际平均奖赏。
\[
Q_t(a) = \frac{\sum_{i=1}^{t-1} R_i \times 1_{A_i=a} }{N_t(a)}
\]
\(H_t(a)\) - 对于行动a的学习到的倾向。
\(\epsilon\) - 在ε-贪婪策略中，采用随机行动的概率\([0, 1)\)。




## 多臂老O虎O机问题


一般的老O虎O机只有一个臂（杆）。你塞10个硬币，拉一下杆，老O虎O机可能会吐出来一两个硬币，或者100个硬币。
多臂老O虎O机有多个杆（象征着多个行动(action)，每个杆有自己特有的吐钱逻辑）。
注意：每个杆的吐钱概率可能是固定的(stationary)，也可能是不固定的(non-stationary)。不固定在现实中更常见。
多臂老O虎O机问题就是在许多次尝试后，**找到一个有效收益的策略**。
多臂老O虎O机问题是统计学、工程、心理学的一个经典问题。不要小看了这个问题，很多权威都研究过。
在强化学习方面，我们通过这个问题，可以了解强化学习的基本概念和算法的思路。其中包括：



 	
  * 探索(exploration)和采用(exploitation)的权衡

 	
  * 贪婪(greedy)

 	
  * 收敛(convergence)

 	
  * 参数初始化的重要性

 	
  * 梯度递减(gradient descent)
（注：梯度递增和梯度递减的意思一样，只是看问题的方向不一样。）
等等。




### 如何判断算法的好坏


在讨论算法前，我们先考虑判断算法好坏的标准。



 	
  * 建立模型
建立一个10臂老O虎O机。
每个臂的真实行动价值\(q_*(a), a = 1, \dots, 10\)是一个符合（平均值=0, 方差=1）的正态分布。
每个臂的每次行动的估值\(R_t(a)\)是一个符合（平均值=\(q_*(a)\), 方差=1）的正态分布。

 	
  * 测试标准

 	
    * 平均奖赏 - 奖赏越多越好

 	
    * 最优行动 - 和实际最优行动一致的比率越大越好







## 解决方法




### 行动-价值方法 (action-value method)


在决定第t次的行动\(A_t\)时，使用下面的算法。
\[
A_t = \underset{a}{argmax} Q_t(a) \\
where \\
1_{A_i=a} =
\begin{cases}
1, if A_i = a \\
0, otherwise
\end{cases}
\]



 	
  * 贪婪方法(greedy method)
总是选择当前取得最大收益的行动。
特点：**最大化采用(exploitation)。**
算法的过程如下：


<blockquote>初始： \(Q_0(a), a = 1, \cdots, 10\) 都为0；
每个杆（action）都拉一下。 \(Q_0(a), a = 1, \cdots, 10\) 有了新值。
根据当前平均收益最大的杆，当做本次选择的杆。</blockquote>




 	
  * ε - 贪婪方法(ε-greedy method)
ε - 读作epsilon。有弹性的意思。
一般情况下选择当前取得最大收益的行动，但是有一定比例ε的行动是随机选择的。
特点：**增强了探索(exploration)性。**
算法的过程如下：


<blockquote>初始： \(Q_0(a), a = 1, \cdots, 10\) 都为0；
每个杆（action）都拉一下。 \(Q_0(a), a = 1, \cdots, 10\) 有了新值。
根据当前平均收益最大的杆，当做本次选择的杆。
同时根据\(ε\)的值，随机选择一个杆。（比如：\(ε=0.1\)，每十次随机选择一个杆）</blockquote>







### 增值实现(incremental implementation)


如何计算\(Q_t\)。
**算法**
\[
\begin{array} \\
Q_t
& = \frac{1}{t-1} \sum_{i=1}^{t-1} R_i \text{ : this method need memory all } R_i. \\
& = Q_{t-1} + \frac{1}{t-1} \left [ R_{t-1} - Q_{t-1} \right ] \text{this method is better, just need memory last } R_{t-1}, Q_{t-1}.
\end{array}
\]


### 带权值步长的增值实现(incremental implementation with weighted step size)


一个替代算法。用步长\(\alpha\) 代替$ \frac{1}{t-1}$。
**算法**
\[
Q_t = Q_{t-1} + \alpha \left [ R_{t-1} - Q_{t-1} \right ]
\]

**解释**
这个算法利于解决非稳定(non-stationary)问题。
非稳定(non-stationary)问题意味着\(q_*(a)\)是会发生变化的。因此，最近几次的奖赏更具代表性。
\(\alpha\)越大，意味着最近奖赏的权重越大。
这里也可以看到梯度计算的影子。


### 优化初始值(Optimistic initial values)


优化初始值\(Q_1(a)\)，如果赋值越大，会鼓励探索。
初始值为0时，ε - 贪婪方法(ε=0.1) 好于 ε - 贪婪方法(ε=0.01) 好于 贪婪方法。
看来冒一定风险还是有好处的。
初始值为5的贪婪方法 好于 ε - 贪婪方法(ε=0.1)。
有钱人更容易成功。


### 置信上界选择算法 (Upper-Confidence-Bound action selection)


可理解为求每个行动的**最大可信值**，选择**最大可信值**最大的行动。

**算法**
\[
A_t = \underset{a}{argmax} \left [ Q_t(a) + c \sqrt{\frac{\log{t} }{N_t(a)} } \right ] \\
where \\
c \text{ : > 0, controls the degree of exploration. bigger c means more exploration.} \\
\text{if } N_t(a) = 0 \text{, then a is considered to be a maximizing action.}
\]

**算法理解**
这个算法在计算：**第t次的行动应该是什么？**


<blockquote>我们没有说：“第t次的**最优**行动应该是什么？”。为什么不说**最优**呢？
因为，强化学习的目的是总体最优，不是局部最优，因此“第t次的**最优**行动”不是强化学习最求的目标。</blockquote>


\(c\)是一个可调的参数。我们在理解中不用太关心它，当它是\(1\)好了。


<blockquote>在机器学习中，算法一般都有几个参数可以调节。不同环境中，调节参数最优化可以很大的提高算法的质量。
\(Q_t(a)\) - 行动a当前的奖赏。
\(t\) - 第t次。
\(\log{t}\) - 求t的指数。随着t变大，\(\log{t}\)变大的速度变慢。
\(N_t(a)\) - 行动a被选择的次数。
\(\left [ \sqrt{\frac{\log{t} }{N_t(a)} } \right ]\) - 由于\(\frac{\log{t} }{N_t(a)} < 1 \text{， when x > 7 }\)， 求平方根，反而是起了一个放缓、放大的作用。
在没有奖赏的情况下：\(Q_t(a)\) 不变。\(\log{t}\)比\(N_t(a)\)变化的慢，因此总结果会变小。</blockquote>





 	
  * 梯度老O虎O机算法 (Gradient Bandit Algorithms)
之前的算法，主要是通过发生的事件，根据**行动的估计奖赏**，来决定选择哪个行动。
梯度算法是：通过发生的事件，根据**行动的倾向**\(H_t(a)\)，来决定选择哪个行动。
（个人没看出有什么不同）。
\[
Pr\{A_t = a\} = \pi_t(a) = softmax(H_t(a)) = \frac{e^{H_t(a)} }{ \sum_{i=1}^k e^{H_t(a)} } \\
A_t = \underset{a}{argmax} (\pi_t(a)) \\
\pi_t(a) \text{ for the probability of taking action a at time t.}
\]


<blockquote>softmax是一个激活函数。通常用于输出的概率计算，就是现在看到的例子。</blockquote>





\[
H_1(a) = 0, \forall a \\
\text{After action } A_t \text{ and receiving the reward } R_t, \\
H_{t+1}(A_t) = H_t(A_t) + \alpha(R_t - \bar{R}_t)(1- \pi_t(A_t)) \text{, and} \\
H_{t+1}(a) = H_t(a) - \alpha(R_t - \bar{R}_t)\pi_t(a) , \forall a \ne A_t \\
\bar{R}_t = \frac{\sum R_t}{t} \\
where \\
\alpha \text{ - step size parameter.} \\
\bar{R}_t \text{ - the average of all the rewards up through and including time t.}
\]


## 参照
























* * *





# COMMENT



