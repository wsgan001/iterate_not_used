---
author: evo
comments: true
date: 2018-05-16 16:38:15+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-on-policy-%e9%a2%84%e6%b5%8b%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95/
slug: rl-on-policy-%e9%a2%84%e6%b5%8b%e7%9a%84%e8%bf%91%e4%bc%bc%e6%96%b9%e6%b3%95
title: RL on-policy 预测的近似方法
wordpress_id: 5890
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


# [强化学习读书笔记 - 09 - on-policy预测的近似方法](http://www.cnblogs.com/steven-yang/p/6535418.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa


这一章开始了第二部门 - **近似解决方案**


## 近似方法的重要性


我们先看看传统方法中存在的问题：



 	
  * 不适用复杂的环境。主要原因是状态和行动太多，策略需要大量空间来记忆策略价值。

 	
  * 环境可能是不稳定的，过去的经验不能适用于未来的情况。需要一个通用性的方法来更新策略价值。

 	
  * 策略价值是一个数值，缺乏通用性。期望有一个通用的方法来计算策略价值。


所以对近似预测方法的理解是，找到一个通用的方法\(\hat{v}(s, \theta)\)。
**数学表示**
\[
\hat{v}(s, \theta) \approx v_{\pi}(s) \\
where \\
\theta \text{ - a weight vector} \\
\theta \doteq (\theta_1, \theta_2, ..., \theta_n)^T
\]

**解释**
近似预测方法是指求策略的状态价值的近似值。
求策略的行动状态价值的近似值叫做近似控制方法(Control Methods)(下一章的内容)。


## 近似预测方法的目标


首先，我们需要找到一个判断近似预测方法质量的计算公式。

**价值均方误差（Mean Squared Value Error）**
\[
MSVE(\theta) = \sum_{s \in \mathcal{S}} d(s) [v_{\pi} - \hat{v}(s, \theta)]^2 \\
where \\
d(s) \text{ - on-policy distribution, the fraction of time spent in s under the target policy } \pi \\
\]



 	
  * 在情节性任务中
\[
\eta(s) = h(s) + \sum_{\bar{s}} \eta(\bar{s}) \sum_{a} \pi(a|\bar{s})p(s|\bar{s}, a), \ \forall s \in \mathcal{S} \\
d(s) = \frac{\eta(s)}{\sum_{s'} \eta(s')} \\
where \\
\eta(s) \text{ - the number of time steps spent in state s in a single episode} \\
h(s) \text{ - time spent in a state s if episodes start in it}
\]

 	
  * 在连续性任务中
\[
d(s) = \text{ the stationary distribution under } \pi \\
\]


**解释：**
\(\eta(s) = h(s) + \sum_{\bar{s}} \eta(\bar{s}) \sum_{a} \pi(a|\bar{s})p(s|\bar{s}, a), \ \forall s \in \mathcal{S}\)
状态s的发生时间（次数） = 在情节中状态s发生在开始的时间（次数） + 状态s发生在其它的时间（次数）


## 随机梯度递减方法（Stochastic gradient descend method）


那么如何求\(\theta\)呢？一个常见的方法是通过梯度递减的方法，迭代的求解\(\theta\)。


### 随机梯度递减算法


**Stochastic gradient descend**
\[
\begin{align}
\theta_{t+1}
& \doteq \theta_{t} - \frac{1}{2} \alpha \nabla [v_{\pi}(S_t) - \hat{v}(S_t, \theta_t)]^2 \\
& = \theta_{t} + \alpha [v_{\pi}(S_t) - \hat{v}(S_t, \theta_t)] \nabla \hat{v}(S_t, \theta_t) \\
\end{align} \\
where \\
\nabla f(\theta) \doteq \left ( \frac{\partial f(\theta)}{\partial \theta_1}, \frac{\partial f(\theta)}{\partial \theta_2}, \cdots, \frac{\partial f(\theta)}{\partial \theta_n} \right )^T \\
\alpha \text{ - the step size, learning rate}
\]

**解释**
这个方法可以在多次迭代后，让\(\theta\)最优。
\(v_{\pi}(S_t)\)是实际值。
\(\hat{v}(S_t, \theta_t)\)是当前计算值。
随机梯度递减方法通过误差（实际值 - 当前计算值）接近最优值的方法。
比较麻烦的是：如何求\(\nabla \hat{v}(S_t, \theta_t)\)。
传统的方法是求\(v_{\pi}(s), q_{\pi}(s, a)\)，在近似方法中变成了求\(\theta, \hat{v}(s, \theta), \hat{q}(s, a,\theta)\)。


### 蒙特卡洛





 	
  * 算法描述


<blockquote>Input: the policy \(\pi\) to be evaluated
Input: a differentiable function \(\hat{v} : \mathcal{S} \times \mathbb{R^n} \to \mathbb{R}\)

Initialize value-function weights \(\theta\) arbitrarily (e.g. \(\theta = 0\))
Repeat (for each episode):
Generate an episode \(S_0, A_0, R_1 ,S_1 ,A_1, \cdots ,R_t ,S_t\) using \(\pi\)
For \(t = 0, 1, \cdots, T - 1\)
\(\theta \gets \theta + \alpha [G_t -\hat{v}(S_t, \theta)] \nabla \hat{v}(S_t, \theta)\)</blockquote>







## 半梯度递减方法（Semi-gradient method）


之所以叫**半梯度递减**的原因是TD(0)和n-steps TD计算价值的公式不是精确的（而蒙特卡罗方法是精确的）。


### 半梯度下降(Semi-gradient TD(0))





 	
  * 算法描述


<blockquote>Input: the policy \(\pi\) to be evaluated
Input: a differentiable function \(\hat{v} : S^+ \times \mathbb{R^n} \to \mathbb{R}\) such that \(\hat{v}(terminal, \dot \ ) = 0\)

Initialize value-function weights \(\theta\) arbitrarily (e.g. \(\theta = 0\))
Repeat (for each episode):
Initialize \(\mathcal{S}\)
Repeat (for each step of episode):
Choose $A \sim \pi(\dot |S) $
Take action \(A\), observe \(R, S'\)
\(\theta \gets \theta + \alpha [R + \gamma \hat{v}(S', \theta) -\hat{v}(S', \theta)] \nabla \hat{v}(S, \theta)\)
\(S \gets S'\)
Until \(S'\) is terminal</blockquote>







### n-steps TD


请看原书，不做拗述。


## 特征选择




## 线性方程的定义


\[
\phi(s) \doteq (\phi_1(s), \phi_2(s), \dots, \phi_n(s))^T \\
\hat{v} \doteq \theta^T \phi(s) \doteq \sum_{i=1}^n \theta_i \phi_i(s)
\]
\(\phi(s)\) 为**特征函数**。
这里讨论特征函数的通用化定义方法。


### 多项式基(polynomials basis)


\(s\)的每一个维度都可以看成一个特征。多项式基的方法是使用\(s\)的高维多项式作为新的特征。
比如：二维的\(s = (s_1, s_2)\)，可以选择多项式为\((1, s_1, s_2, s_1s_2)\)或者\((1, s_1, s_2, s_1s_2, s_1^2, s_2^2, s_1s_2^2, s_1^2s_2, s_1^2s_2^2)\)

**多项式基方法的通用数学表达：**
\[
\phi_i(s) = \prod_{j=1}^d s_j^{C_{i,j}} \\
where \\
s = (s_1,s_2,\cdots,s_d)^T \\
\phi_i(s) \text{ - polynomials basis function}
\]


### 傅里叶基(Fourier basis)


**傅里叶基方法的通用数学表达：**
\[
\phi_i(s) = \cos(\pi c^i \dot s), \ s \in [0,1)] \\
where \\
c^i = (x_1^i, c_2^i, \cdots, c_d^i)^T, \ with \ c_j^i \in \{0, \cdots, N\} \ for \ j = 1, \cdots, d \ and \ i = 0, \cdots, (N + 1)^d
\]


### 径向基(Radial Basis)


**径向基方法的通用数学表达：**
\[
\phi_i(s) \doteq exp \left ( - \frac{\lVert s-c_i \rVert ^2 }{2 \sigma_i^2} \right )
\]


## 最小二乘法TD(Least-Squares TD)




<blockquote>Input: feature representation \(\phi(s) \in \mathbb{R}^n, \forall s \in \mathcal{S}, \phi(terminal) \doteq 0\)

$\hat{A^{-1}} \gets \epsilon^{-1} I \qquad \text{An } n \times n matrix $
\(\hat{b} \gets 0\)
Repeat (for each episode):
Initialize S; obtain corresponding \(\phi\)
Repeat (for each step of episode):
Choose \(A \sim \pi(\dot \ | S)\)
Take action \(A\), observer \(R, S'\); obtain corresponding \(\phi'\)
\(v \gets \hat{A^{-1}}^T (\phi - \gamma \phi')\)
\(\hat{A^{-1}} \gets \hat{A^{-1}} - (\hat{A^{-1}}\phi) v^T / (1+v^T\phi)\)
\(\hat{b} \gets \hat{b} + R \phi\)
\(\theta \gets \hat{A^{-1}} \hat{b}\)
\(S \gets S'; \phi \gets \phi'\)
until S' is terminal</blockquote>



























* * *





# COMMENT



