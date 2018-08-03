---
author: evo
comments: true
date: 2018-05-16 16:31:19+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e8%92%99%e7%89%b9%e5%8d%a1%e6%b4%9b%e6%96%b9%e6%b3%95monte-carlo-methods/
slug: rl-%e8%92%99%e7%89%b9%e5%8d%a1%e6%b4%9b%e6%96%b9%e6%b3%95monte-carlo-methods
title: RL 蒙特卡洛方法(Monte Carlo Methods)
wordpress_id: 5884
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


# [强化学习读书笔记 - 05 - 蒙特卡洛方法(Monte Carlo Methods)](http://www.cnblogs.com/steven-yang/p/6507015.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




学习笔记：  

[Reinforcement Learning: An Introduction, Richard S. Sutton and Andrew G. Barto c 2014, 2015, 2016](http://incompleteideas.net/sutton/book/bookdraft2017june19.pdf/)




数学符号看不懂的，先看看这里：






  * [强化学习读书笔记 - 00 - 术语和数学符号](http://www.cnblogs.com/steven-yang/p/6481772.html)




## 蒙特卡洛方法简话




**蒙特卡洛**是一个赌城的名字。冯·诺依曼给这方法起了这个名字，增加其神秘性。  

蒙特卡洛方法是一个计算方法，被广泛的用于许多领域，用于求值。  

相对于确定性的算法，蒙特卡洛方法是基于抽样数据来计算结果。




## 蒙特卡洛方法的基本思路




蒙特卡洛方法的整体思路是：**模拟 -> 抽样 -> 估值**。




**示例：**  

比如：如何求\(\pi\)的值。一个使用蒙特卡洛方法的经典例子如下：  

我们知道一个直径为1的圆的面积为\(\pi\)。  

把这个圆放到一个边长为2的正方形（面积为4）中，圆的面积和正方形的面积比是：\(\frac{\pi}{4}\)。  

如果可以测量出这个比值\(c\)，那么\(\pi=c \times 4\)。  

如何测量比值\(c\)呢？用飞镖去扎这个正方形。扎了许多次后，用圆内含的小孔数除以正方形含的小孔数可以近似的计算比值\(c\)。




**说明：**  

模拟 - 用飞镖去扎这个正方形为一次模拟。  

抽样 - 数圆内含的小孔数和正方形含的小孔数。  

估值 - 比值\(c\) = 圆内含的小孔数 / 正方形含的小孔数




## 蒙特卡洛方法的使用条件






  * 环境是可模拟的  

在实际的应用中，模拟容易实现。相对的，了解环境的完整知识反而比较困难。  

由于环境可模拟，我们就可以抽样。



  * 只适合情节性任务(episodic tasks)  

因为，需要抽样完成的结果，只适合有限步骤的情节性任务。





## 蒙特卡洛方法在强化学习中的用例




只要满足蒙特卡洛方法的使用条件，就可以使用蒙特卡洛方法。  

比如：游戏类都适合：完全信息博弈游戏，像围棋、国际象棋。非完全信息博弈游戏：21点、麻将等等。




## 蒙特卡洛方法在强化学习中的基本思路




蒙特卡洛方法的整体思路是：**模拟 -> 抽样 -> 估值**。




如何应用到强化学习中呢？  

强化学习的目的是得到最优策略。  

得到最优策略的一个方法是求\(v_{pi}(s), \ q_{pi}{s, a}\)。 - 这就是一个**求值问题**。




结合通用策略迭代(GPI)的思想。  

下面是蒙特卡洛方法的一个迭代过程：






  1. 策略评估迭代  

1. 探索 - 选择一个状态(s, a)。  

1. 模拟 - 使用当前策略\(\pi\)，进行一次模拟，从当前状态(s, a)到结束，随机产生一段情节(episode)。  

1. 抽样 - 获得这段情节上的每个状态(s, a)的回报\(G(s, a)\)，记录\(G(s, a)\)到集合\(Returns(s, a)\)。  

1. 估值 - q(s, a) = Returns(s, a)的平均值。  

（因为状态(s, a)可能会被多次选择，所以状态(s, a)有一组回报值。）


  2. 策略优化 - 使用新的行动价值\(q(s, a)\)优化策略\(\pi(s)\)。




**解释**






  * 上述的策略评估迭代步骤，一般会针对所有的状态-行动，或者一个起始(\(s_0, a_0\))下的所有状态-行动。  

这也说明**持续探索（continual exploration）是蒙特卡洛方法的主题**。


  * 模拟过程 - 会模拟到结束。是前进式的，随机选择下一个行动，一直前进到结束为止。  

因此可以看出蒙特卡洛方法需要大量的迭代，才能正确的找到最优策略。


  * 策略评估是计算行动价值(\(q(s, a)\))。  

(也可以是状态价值，则\(\pi(s)\)为状态\(s\)到其下一个最大价值状态\(s‘\)的任意行动。)  

计算方法：  

\[
q(s, a) = average(Returns(s, a))
\]




## 一些概念






  * Exploring Starts 假设 - 指有一个探索起点的环境。  

比如：围棋的当前状态就是一个探索起点。自动驾驶的汽车也许是一个没有起点的例子。



  * first-visit - 在一段情节中，一个状态只会出现一次，或者只需计算第一次的价值。


  * every-visit - 在一段情节中，一个状态可能会被访问多次，需要计算每一次的价值。


  * on-policy method - 评估和优化的策略和模拟的策略是同一个。



  * off-policy method - 评估和优化的策略和模拟的策略是不同的两个。  

有时候，模拟数据来源于其它处，比如：已有的数据，或者人工模拟等等。



  * target policy - 目标策略。off policy method中，需要优化的策略。


  * behavior policy - 行为策略。off policy method中，模拟数据来源的策略。





根据上面的不同情境，在强化学习中，提供了不同的蒙特卡洛方法。






  * 蒙特卡洛（起始点（Exploring Starts））方法


  * On-policy first visit 蒙特卡洛方法（for \(\epsilon\)-soft policies）


  * Off-policy every-visit 蒙特卡洛方法




## 蒙特卡洛（起始点（Exploring Starts））方法




<blockquote>

> 
> Initialize, for all \(s \in \mathcal{S}, \ a \in \mathcal{A}(s)\):  

 \(Q(s,a) \gets\) arbitrary  

 \(\pi(s) \gets\) arbitrary  

 \(Returns(s, a) \gets\) empty list
> 
> 

> 
> Repeat forever:  

 Choose \(S_0 \in \mathcal{S}\) and \(A_0 \in \mathcal{A}(S_0)\) s.t. all pairs have probability > 0  

 Generate an episode starting from \(S_0, A_0\), following \(\pi\)  

 For each pair \(s,a\) appearing in the episode:  

   $G \gets $ return following the first occurrence of s,a  

   Append \(G\) to \(Returns(s, a)\)  

   \(Q(s, a) \gets average(Returns(s, a))\)  

 For each s in the episode:  

   \(\pi(s) \gets \underset{a}{argmax} Q(s,a)\)
> 
> 
</blockquote>




## On-policy first visit 蒙特卡洛方法（for \(\epsilon\)-soft policies）




<blockquote>

> 
> Initialize, for all \(s \in \mathcal{S}, \ a \in \mathcal{A}(s)\):  

  \(Q(s,a) \gets\) arbitrary  

  \(\pi(a|s) \gets\) an arbitrary \(\epsilon\)-soft policy  

  \(Returns(s, a) \gets\) empty list
> 
> 

> 
> Repeat forever:  

  (a) Generate an episode using \(\pi\)  

  (b) For each pair \(s,a\) appearing in the episode:  

   $G \gets $ return following the first occurrence of s,a  

   Append \(G\) to \(Returns(s, a)\)  

   \(Q(s, a) \gets average(Returns(s, a))\)  

  (c) For each s in the episode:  

   \(A^* \gets \underset{a}{argmax} \ Q(s,a)\)  

   For all \(a \in \mathcal{A}(s)\):  

    if \(a = A^*\)  

     \(\pi(a|s) \gets 1 - \epsilon + \frac{\epsilon}{|\mathcal{A}(s)|}\)  

    if \(a \ne A^*\)  

     \(\pi(a|s) \gets \frac{\epsilon}{|\mathcal{A}(s)|}\)
> 
> 
</blockquote>




## Off-policy every-visit 蒙特卡洛方法




<blockquote>

> 
> Initialize, for all \(s \in \mathcal{S}, \ a \in \mathcal{A}(s)\):  

  \(Q(s,a) \gets\) arbitrary  

  \(C(s,a) \gets\) 0  

  \(\mu(a|s) \gets\) an arbitrary soft behavior policy  

  \(\pi(a|s) \gets\) a deterministic policy that is greedy with respect to Q
> 
> 

> 
> Repeat forever:  

  Generate an episode using \(\mu\):  

   \(S_0,A_0,R_1,\cdots,S_{T-1},A_{T-1},R_T,S_T\)  

  \(G \gets 0\)  

  \(W \gets 1\)  

  For t = T - 1 downto 0:  

   \(G \gets \gamma G + R_{t+1}\)  

   \(C(S_t, A_t) \gets C(S_t, A_t) + W\)  

   \(Q(S_t, A_t) \gets Q(S_t, A_t) + \frac{W}{C(S_t, A_t)} |G - Q(S_t, A_t)|\)  

   \(\pi(S_t) \gets \underset{a}{argmax} \ Q(S_t, a)\) (with ties broken consistently)  

   If \(A_t \ne \pi(S_t)\) then ExitForLoop  

   \(W \gets W \frac{1}{\mu(A_t|S_t)}\)
> 
> 
</blockquote>




## 总结




### 蒙特卡洛方法和动态规划的区别






  1. 动态规划是基于模型的，而蒙特卡洛方法是无模型的。




<blockquote>

> 
> 注：基于模型(model-base)还是无模型(model-free)是看(状态或者行动)价值(\(G, v(s), q(s,a)\))是如何得到的？  

如果是已知的、根据已知的数据计算出来的，就是基于模型的。  

如果是取样得到的、试验得到的，就是无模型的。
> 
> 
</blockquote>



  2. 动态规划的计算的，而蒙特卡洛方法的计算是取样性的(sampling)。




<blockquote>

> 
> 注：引导性的(bootstrapping)还是取样性的(sampling)是看(状态或者行动)价值(\(G, v(s), q(s,a)\))是如何计算的？  

如果是根据其它的价值计算的，就是引导性的。  

如果是通过在实际环境中模拟的、取样的，就是取样性的。  

引导性和取样性并不是对立的。可以是取样的，并且是引导的。  

如果价值是根据其它的价值计算的，但是有部分值（比如：奖赏）是取样得到的，就是无模型、取样的、引导性的。
> 
> 
</blockquote>





**解释：**  

上面两个区别，可以从计算状态价值\(v_{\pi}(s), q_{\pi}(s, a)\)的过程来看：  

动态规划是从初始状态开始，一次计算一步可能发生的所有状态价值，然后迭代计算下一步的所有状态价值。这就是引导性。  

蒙特卡洛方法是从初始状态开始，通过在实际环境中模拟，得到一段情节（从头到结束）。  

比如，如果结束是失败了，这段情节上的状态节点，本次价值都为0,；如果成功了，本次价值都为1。




下面的比喻（虽然不太恰当，但是比较形象）  

想象一棵树，动态规划是先算第一层的所有节点价值，然后算第二层的所有节点价值。  

蒙特卡洛方法，随便找一个从根到叶子的路径。根据叶子的值，计算路径上每个节点价值。  

可以看出蒙特卡洛方法比较方便。




### 蒙特卡洛方法的优势






  * 蒙特卡洛方法可以从交互中直接学习优化的策略，而不需要一个环境的动态模型。  

环境的动态模型 - 似乎表示环境的状态变化是可以完全推导的。表明了解环境的所有知识。  

说白了，就是可以计算\(v(s), q(s, a)\)这意味着必须了解所有状态变化的可能性。  

蒙特卡洛方法只需要一些（可能是大量的）取样就可以。



  * 蒙特卡洛方法可以用于模拟（样本）模型。


  * 蒙特卡洛方法可以只考虑一个小的状态子集。


  * 蒙特卡洛方法的每个状态价值计算是独立的。不会影响其他的状态价值。





### 　蒙特卡洛方法的劣势






  * 需要大量的探索（模拟）。


  * 基于概率的，不是确定性的。




## 参照


























* * *





# COMMENT



