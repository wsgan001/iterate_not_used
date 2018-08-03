---
author: evo
comments: true
date: 2018-05-16 16:56:10+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-on-policy%e6%8e%a7%e5%88%b6%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95/
slug: rl-on-policy%e6%8e%a7%e5%88%b6%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95
title: RL on-policy控制的近似方法
wordpress_id: 5909
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


# [强化学习读书笔记 - 10 - on-policy控制的近似方法](http://www.cnblogs.com/steven-yang/p/6536471.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa










## on-policy控制的近似方法


近似控制方法(Control Methods)是求策略的行动状态价值\(q_{\pi}(s, a)\)的近似值\(\hat{q}(s, a, \theta)\)。


### 半梯度递减的控制Sarsa方法 (Episodic Semi-gradient Sarsa for Control)




<blockquote>Input: a differentiable function \(\hat{q} : \mathcal{S} \times \mathcal{A} \times \mathbb{R}^n \to \mathbb{R}\)

Initialize value-function weights \(\theta \in \mathbb{R}^n\) arbitrarily (e.g., \(\theta = 0\))
Repeat (for each episode):
\(S, A \gets\) initial state and action of episode (e.g., "\(\epsilon\)-greedy)
Repeat (for each step of episode):
Take action \(A\), observe \(R, S'\)
If \(S'\) is terminal:
\(\theta \gets \theta + \alpha [R - \hat{q}(S, A, \theta)] \nabla \hat{q}(S, A, \theta)\)
Go to next episode
Choose \(A'\) as a function of \(\hat{q}(S', \dot \ , \theta)\) (e.g., \(\epsilon\)-greedy)
\(\theta \gets \theta + \alpha [R + \gamma \hat{q}(S', A', \theta) - \hat{q}(S, A, \theta)] \nabla \hat{q}(S, A, \theta)\)
\(S \gets S'\)
\(A \gets A'\)</blockquote>




### 多步半梯度递减的控制Sarsa方法 (n-step Semi-gradient Sarsa for Control)


请看原书，不做拗述。


### （连续性任务的）平均奖赏


由于打折率(\(\gamma\), the discounting rate)在近似计算中存在一些问题（说是下一章说明问题是什么）。
因此，在连续性任务中引进了平均奖赏(Average Reward)\(\eta(\pi)\):
\[
\begin{align}
\eta(\pi)
& \doteq \lim_{T \to \infty} \frac{1}{T} \sum_{t=1}{T} \mathbb{E} [R_t | A_{0:t-1} \sim \pi] \\
& = \lim_{t \to \infty} \mathbb{E} [R_t | A_{0:t-1} \sim \pi] \\
& = \sum_s d_{\pi}(s) \sum_a \pi(a|s) \sum_{s',r} p(s,r'|s,a)r
\end{align}
\]



 	
  * 目标回报（= 原奖赏 - 平均奖赏）
\[
G_t \doteq R_{t+1} - \eta(\pi) + R_{t+2} - \eta(\pi) + \cdots
\]

 	
  * 策略价值
\[
v_{\pi}(s) = \sum_{a} \pi(a|s) \sum_{r,s'} p(s',r|s,a)[r - \eta(\pi) + v_{\pi}(s')] \\
q_{\pi}(s,a) = \sum_{r,s'} p(s',r|s,a)[r - \eta(\pi) + \sum_{a'} \pi(a'|s') q_{\pi}(s',a')] \\
\]

 	
  * 策略最优价值
\[
v_{*}(s) = \underset{a}{max} \sum_{r,s'} p(s',r|s,a)[r - \eta(\pi) + v_{*}(s')] \\
q_{*}(s,a) = \sum_{r,s'} p(s',r|s,a)[r - \eta(\pi) + \underset{a'}{max} \ q_{*}(s',a')] \\
\]

 	
  * 时序差分误差
\[
\delta_t \doteq R_{t+1} - \bar{R} + \hat{v}(S_{t+1},\theta) - \hat{v}(S_{t},\theta) \\
\delta_t \doteq R_{t+1} - \bar{R} + \hat{q}(S_{t+1},A_t,\theta) - \hat{q}(S_{t},A_t,\theta) \\
where \\
\bar{R} \text{ - is an estimate of the average reward } \eta(\pi)
\]

 	
  * 半梯度递减Sarsa的平均奖赏版
\[
\theta_{t+1} \doteq \theta_t + \alpha \delta_t \nabla \hat{q}(S_{t},A_t,\theta)
\]




### 半梯度递减Sarsa的平均奖赏版(for continuing tasks)




<blockquote>Input: a differentiable function \(\hat{q} : \mathcal{S} \times \mathcal{A} \times \mathbb{R}^n \to \mathbb{R}\)
Parameters: step sizes \(\alpha, \beta > 0\)

Initialize value-function weights \(\theta \in \mathbb{R}^n\) arbitrarily (e.g., \(\theta = 0\))
Initialize average reward estimate \(\bar{R}\) arbitrarily (e.g., \(\bar{R} = 0\))
Initialize state \(S\), and action \(A\)

Repeat (for each step):
Take action \(A\), observe \(R, S'\)
Choose \(A'\) as a function of \(\hat{q}(S', \dot \ , \theta)\) (e.g., \(\epsilon\)-greedy)
\(\delta \gets R - \bar{R} + \hat{q}(S', A', \theta) - \hat{q}(S, A, \theta)\)
\(\bar{R} \gets \bar{R} + \beta \delta\)
\(\theta \gets \theta + \alpha \delta \nabla \hat{q}(S, A, \theta)\)
\(S \gets S'\)
\(A \gets A'\)</blockquote>




### 多步半梯度递减的控制Sarsa方法 - 平均奖赏版(for continuing tasks)


请看原书，不做拗述。

















* * *





# COMMENT



