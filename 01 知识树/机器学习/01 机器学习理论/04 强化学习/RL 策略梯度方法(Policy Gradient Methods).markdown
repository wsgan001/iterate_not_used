---
author: evo
comments: true
date: 2018-05-16 16:44:19+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e7%ad%96%e7%95%a5%e6%a2%af%e5%ba%a6%e6%96%b9%e6%b3%95policy-gradient-methods/
slug: rl-%e7%ad%96%e7%95%a5%e6%a2%af%e5%ba%a6%e6%96%b9%e6%b3%95policy-gradient-methods
title: RL 策略梯度方法(Policy Gradient Methods)
wordpress_id: 5897
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


# [强化学习读书笔记 - 13 - 策略梯度方法(Policy Gradient Methods)](http://www.cnblogs.com/steven-yang/p/6624253.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa











## 策略梯度方法(Policy Gradient Methods)




### 基于价值函数的思路




\[
\text{Reinforcement Learning} \doteq \pi_* \\
\quad \updownarrow \\
\pi_* \doteq \{ \pi(s) \}, \ s \in \mathcal{S} \\
\quad \updownarrow \\
\begin{cases}
\pi(s) = \underset{a}{argmax} \ v_{\pi}(s' | s, a), \ s' \in S(s), \quad \text{or} \\
\pi(s) = \underset{a}{argmax} \ q_{\pi}(s, a) \\
\end{cases} \\
\quad \updownarrow \\
\begin{cases}
v_*(s), \quad \text{or} \\
q_*(s, a) \\
\end{cases} \\
\quad \updownarrow \\
\text{approximation cases:} \\
\begin{cases}
\hat{v}(s, \theta) \doteq \theta^T \phi(s), \quad \text{state value function} \\
\hat{q}(s, a, \theta) \doteq \theta^T \phi(s, a), \quad \text{action value function} \\
\end{cases} \\
where \\
\theta \text{ - value function's weight vector} \\
\]




### 策略梯度方法的新思路(Policy Gradient Methods)




\[
\text{Reinforcement Learning} \doteq \pi_* \\
\quad \updownarrow \\
\pi_* \doteq \{ \pi(s) \}, \ s \in \mathcal{S} \\
\quad \updownarrow \\
\pi(s) = \underset{a}{argmax} \ \pi(a|s, \theta) \\
where \\
\pi(a|s, \theta) \in [0, 1] \\
s \in \mathcal{S}, \ a \in \mathcal{A} \\
\quad \updownarrow \\
\pi(a|s, \theta) \doteq \frac{exp(h(s,a,\theta))}{\sum_b exp(h(s,b,\theta))} \\
\quad \updownarrow \\
exp(h(s,a,\theta)) \doteq \theta^T \phi(s,a) \\
where \\
\theta \text{ - policy weight vector} \\
\]




## 策略梯度定理（The policy gradient theorem）




### 情节性任务




如何计算策略的价值\(\eta\)  

\[
\eta(\theta) \doteq v_{\pi_\theta}(s_0) \\
where \\
\eta \text{ - the performance measure} \\
v_{\pi_\theta} \text{ - the true value function for } \pi_\theta \text{, the policy determined by } \theta \\
s_0 \text{ - some particular state} \\
\]






  * 策略梯度定理  

\[
\nabla \eta(\theta) = \sum_s d_{\pi}(s) \sum_{a} q_{\pi}(s,a) \nabla_\theta \pi(a|s, \theta) \\
where \\
d(s) \text{ - on-policy distribution, the fraction of time spent in s under the target policy } \pi \\
\sum_s d(s) = 1 \\
\]




## 蒙特卡洛策略梯度强化算法(ERINFORCE: Monte Carlo Policy Gradient)






  * 策略价值计算公式  

\[
\begin{align}
\nabla \eta(\theta) 
& = \sum_s d_{\pi}(s) \sum_{a} q_{\pi}(s,a) \nabla_\theta \pi(a|s, \theta) \\
& = \mathbb{E}_\pi \left [ \gamma^t \sum_a q_\pi(S_t,a) \nabla_\theta \pi(a|s, \theta) \right ] \\
& = \mathbb{E}_\pi \left [ \gamma^t G_t \frac{\nabla_\theta \pi(A_t|S_t, \theta)}{\pi(A_t|S_t, \theta)} \right ]
\end{align}
\]



  * Update Rule公式  

\[
\begin{align}
\theta_{t+1} 
& \doteq \theta_t + \alpha \gamma^t G_t \frac{\nabla_\theta \pi(A_t|S_t, \theta)}{\pi(A_t|S_t, \theta)} \\
& = \theta_t + \alpha \gamma^t G_t \nabla_\theta \log \pi(A_t|S_t, \theta) \\
\end{align}
\]



  * 算法描述(ERINFORCE: A Monte Carlo Policy Gradient Method (episodic))  

请看原书，在此不做拗述。





## 带基数的蒙特卡洛策略梯度强化算法(ERINFORCE with baseline)






  * 策略价值计算公式  

\[
\begin{align}
\nabla \eta(\theta) 
& = \sum_s d_{\pi}(s) \sum_{a} q_{\pi}(s,a) \nabla_\theta \pi(a|s, \theta) \\
& = \sum_s d_{\pi}(s) \sum_{a} \left ( q_{\pi}(s,a) - b(s)\right ) \nabla_\theta \pi(a|s, \theta) \\
\end{align} \\
\because \\
\sum_{a} b(s) \nabla_\theta \pi(a|s, \theta) \\
\quad = b(s) \nabla_\theta \sum_{a} \pi(a|s, \theta) \\
\quad = b(s) \nabla_\theta 1 \\
\quad = 0 \\
where \\
b(s) \text{ - an arbitrary baseline function, e.g. } b(s) = \hat{v}(s, w) \\
\]



  * Update Rule公式  

\[
\delta = G_t - \hat{v}(s, w) \\
w_{t+1} = w_{t} + \beta \delta \nabla_w \hat{v}(s, w) \\
\theta_{t+1} = \theta_t + \alpha \gamma^t \delta \nabla_\theta \log \pi(A_t|S_t, \theta) \\
\]



  * 算法描述  

请看原书，在此不做拗述。





## 角色评论算法(Actor-Critic Methods)




这个算法实际上是：






  1. **带基数的蒙特卡洛策略梯度强化算法**的TD通用化。


  2. 加上资格迹(eligibility traces)




<blockquote>

> 
> 注：蒙特卡洛方法要求必须完成当前的情节。这样才能计算正确的回报\(G_t\)。  

TD避免了这个条件（从而提高了效率），可以通过临时差分计算一个近似的回报\(G_t^{(0)} \approx G_t\)（当然也产生了不精确性）。  

资格迹(eligibility traces)优化了(计算权重变量的)价值函数的微分值，\(e_t \doteq \nabla \hat{v}(S_t, \theta_t) + \gamma \lambda \ e_{t-1}\)。
> 
> 
</blockquote>






  * Update Rule公式  

\[
\delta = G_t^{(1)} - \hat{v}(S_t, w) \\
\quad = R_{t+1} + \gamma \hat{v}(S_{t+1}, w) - \hat{v}(S_t, w) \\
w_{t+1} = w_{t} + \beta \delta \nabla_w \hat{v}(s, w) \\
\theta_{t+1} = \theta_t + \alpha \gamma^t \delta \nabla_\theta \log \pi(A_t|S_t, \theta) \\
\]



  * Update Rule with eligibility traces公式  

\[
\delta = R + \gamma \hat{v}(s', w) - \hat{v}(s', w) \\
e^w = \lambda^w e^w + \gamma^t \nabla_w \hat{v}(s, w) \\
w_{t+1} = w_{t} + \beta \delta e_w \\
e^{\theta} = \lambda^{\theta} e^{\theta} + \gamma^t \nabla_\theta \log \pi(A_t|S_t, \theta)  \\
\theta_{t+1} = \theta_t + \alpha \delta e^{\theta} \\
where \\
R + \gamma \hat{v}(s', w) = G_t^{(0)} \\
\delta \text{ - TD error} \\
e^w \text{ - eligibility trace of state value function} \\
e^{\theta} \text{ - eligibility trace of policy value function} \\
\]



  * 算法描述  

请看原书，在此不做拗述。





## 针对连续性任务的策略梯度算法(Policy Gradient for Continuing Problems(Average Reward Rate))






  * 策略价值计算公式  

对于连续性任务的策略价值是**每个步骤的平均奖赏**。  

\[
\begin{align}
\eta(\theta) \doteq r(\theta)
& \doteq \lim_{n \to \infty} \frac{1}{n} \sum_{t=1}^n \mathbb{E} [R_t|\theta_0=\theta_1=\dots=\theta_{t-1}=\theta] \\
& = \lim_{t \to \infty} \mathbb{E} [R_t|\theta_0=\theta_1=\dots=\theta_{t-1}=\theta] \\
\end{align}
\]



  * Update Rule公式  

\[
\delta = G_t^{(1)} - \hat{v}(S_t, w) \\
\quad = R_{t+1} + \gamma \hat{v}(S_{t+1}, w) - \hat{v}(S_t, w) \\
w_{t+1} = w_{t} + \beta \delta \nabla_w \hat{v}(s, w) \\
\theta_{t+1} = \theta_t + \alpha \gamma^t \delta \nabla_\theta \log \pi(A_t|S_t, \theta) \\
\]



  * Update Rule Actor-Critic with eligibility traces (continuing) 公式  

\[
\delta = R - \bar{R} + \gamma \hat{v}(s', w) - \hat{v}(s', w) \\
\bar{R} = \bar{R} + \eta \delta  \\
e^w = \lambda^w e^w + \gamma^t \nabla_w \hat{v}(s, w) \\
w_{t+1} = w_{t} + \beta \delta e_w \\
e^{\theta} = \lambda^{\theta} e^{\theta} + \gamma^t \nabla_\theta \log \pi(A_t|S_t, \theta)  \\
\theta_{t+1} = \theta_t + \alpha \delta e^{\theta} \\
where \\
R + \gamma \hat{v}(s', w) = G_t^{(0)} \\
\delta \text{ - TD error} \\
e^w \text{ - eligibility trace of state value function} \\
e^{\theta} \text{ - eligibility trace of policy value function} \\
\]



  * 算法描述(Actor-Critic with eligibility traces (continuing))  

请看原书，在此不做拗述。  

**原书还没有完成，这章先停在这里**




















* * *





# COMMENT



