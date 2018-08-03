---
author: evo
comments: true
date: 2018-05-16 16:34:09+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e8%a7%84%e5%88%92%e5%bc%8f%e6%96%b9%e6%b3%95%e5%92%8c%e5%ad%a6%e4%b9%a0%e5%bc%8f%e6%96%b9%e6%b3%95/
slug: rl-%e8%a7%84%e5%88%92%e5%bc%8f%e6%96%b9%e6%b3%95%e5%92%8c%e5%ad%a6%e4%b9%a0%e5%bc%8f%e6%96%b9%e6%b3%95
title: RL 规划式方法和学习式方法
wordpress_id: 5889
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


# [强化学习读书笔记 - 08 - 规划式方法和学习式方法](http://www.cnblogs.com/steven-yang/p/6525889.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




## 什么是模型(model)




环境的模型，本体可以通过模型来预测行为的反应。  

对于随机的环境，有两种不同的模型：






  * distribution model - 分布式模型，返回行为的各种可能和其概率。


  * sample model - 样本式模型，根据概率，返回行为的一种可能。




**样本式模型的数学表达**  

\[
(R, S') = model(S, A)
\]




## 规划型方法和学习型方法（Planning and Learning with Tabular Methods）






  * planning methods - 规划型方法。通过模型来获得价值信息（行动状态转换，奖赏等）。  

比如：动态规划（dynamic programming）和启发式查询（heuristic search）。  

模型planning相当于模型模拟(model simulation)。



  * learning methods - 学习型方法。通过体验（experience）来获得价值信息。  

比如：蒙特卡洛方法(Mento Carlo method)和时序差分方法(temporal different method)。





<blockquote>

> 
> 蒙特卡洛树方法是一个规划型方法，需要一个样本式模型。而蒙特卡洛方法是一个学习型方法。  

这并不矛盾，只是意味着学习型方法的体验是可以用模型来执行，而获得一个模拟的经验(simulated experience)。
> 
> 
</blockquote>






  * 规划型方法和学习型方法的相似性  

规划型方法和学习型方法都是通过计算策略价值来优化策略。因此，可以融合到一起。  

见书中例子：Random-sample on-step tabular Q-planning.




### 规划型方法




规划就是通过模型来学习 - 优化策略，有两种：






  * state-place planning - 状态空间规划  

这也是本书中所讲的。



  * plan-place planning - 规划空间规划  

本书不讲。





## Dyna - 结合模型学习和直接强化学习






  * model learning - 模型学习，通过体验来优化模型的过程。


  * directly reinforcement learning - 直接强化学习，通过体验来优化策略的过程。




这里的思想是：通过体验来直接优化策略和优化模型（再优化策略）。见图：




Reinforcement Learning - DynaReinforcement Learning - Dynapolicyvalue/policyexperienceexperiencepolicy->experienceactingexperience->policydirect reinforcement learningmodelmodelmodel->policyplanningmodel->experiencemodel learning




### Tabular Dyna-Q




<blockquote>

> 
> Initialize \(Q(s, a)\) and \(Model(s, a) \forall s \in S \ and \ a \in A(s)\)  

Do forever(for each episode):  

  (a) $S \gets $ current (nonterminal) state  

  (b) \(A \gets \epsilon-greedy(S, Q)\)  

  (c) Execute action \(A\); observe resultant reward, \(R\), and state, \(S'\)  

  (d) \(Q(S, A) \gets Q(S, A) + \alpha [R + \gamma \underset{a}{max} \ Q(S', a) - Q(S, A)]\)   

  (e) \(Model(S, A) \gets R, S'\) (assuming deterministic environment)  

  (f) Repeat n times:  

   $S \gets $ random previously observed state  

   $A \gets $ random action previously taken in \(S\)  

   \(R, S' \gets Model(S, A)\)  

   \(Q(S, A) \gets Q(S, A) + \alpha [R + \gamma \underset{a}{max} \ Q(S', a) - Q(S, A)]\)   

  

**理解**  

上面的算法，如果\(n=0\)，就是Q-learning算法。Dyna-Q的算法的优势在于性能上的提高。  

我想主要原因是通过建立模型，减少了操作(c)，模型学习到了\(Model(S, A) \gets R, S'\)。
> 
> 
</blockquote>




### 优化的交换（Prioritized Sweeping）




下面的算法，提供了一种性能的优化，只评估那些误差大于一定值\(\theta\)的策略价值。




<blockquote>

> 
> Initialize \(Q(s, a)\), \(Model(s, a), \ \forall s, \forall a\) and PQueue to empty  

Do forever(for each episode):  

  (a) $S \gets $ current (nonterminal) state  

  (b) \(A \gets policy(S, Q)\)  

  (c) Execute action A; observe resultant reward, R, and state, \(S'\)  

  (d) \(Model(S, A) \gets R, S'\)  

  (e) \(P \gets |R + \gamma \underset{a}{max} \ Q(S', a) - Q(S, A)|\)  

  (f) if \(P > \theta\), then insert \(S, A\) into \(PQueue\) with priority \(P\)  

  (g) Repeat \(n\) times, while \(PQueue\) is not empty:  

   \(S, A \gets first(PQueue)\) (will remove the first also)  

   \(R, S' \gets Model(S, A)\)  

   \(Q(S, A) \gets Q(S, A) + \alpha [R + \gamma \underset{a}{max} \ Q(S', a) - Q(S, A)]\)  

   Repeat, for all \(S,A\) predicted to lead to \(S\):  

    $\overline{P} \gets $ predicted reward for \(\overline{S}, \overline{A}, S\)  

    \(P \gets |\overline{R} + \gamma \underset{a}{max} \ Q(S', a) - Q(\overline{S}, \overline{A})|\)  

    if \(P > \theta\), then insert \(\overline{S}, \overline{A}\) into \(PQueue\) with priority \(P\)
> 
> 
</blockquote>




## 蒙特卡洛树搜索




我有另外一个博文介绍了这个算法。  

[蒙特卡洛树搜索算法（UCT）: 一个程序猿进化的故事](http://www.cnblogs.com/steven-yang/p/5993205.html)




## 参照


























* * *





# COMMENT



