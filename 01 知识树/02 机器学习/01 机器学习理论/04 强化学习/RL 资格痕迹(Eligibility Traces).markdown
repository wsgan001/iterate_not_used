---
author: evo
comments: true
date: 2018-05-16 16:42:59+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e8%b5%84%e6%a0%bc%e7%97%95%e8%bf%b9eligibility-traces/
slug: rl-%e8%b5%84%e6%a0%bc%e7%97%95%e8%bf%b9eligibility-traces
title: RL 资格痕迹(Eligibility Traces)
wordpress_id: 5898
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


# [强化学习读书笔记 - 12 - 资格痕迹(Eligibility Traces)](http://www.cnblogs.com/steven-yang/p/6617134.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa











## 资格迹(Eligibility Traces)




### 如何理解资格迹




资格迹是一个向量，称为eligibility trace vector。  

强化学习是找最优策略\(\pi_*\)。  

最优策略\(\pi_*\)等价于最优行动\(\pi_*(s)\)。  

最优行动\(\pi_*(s)\)可以由最优状态价值\(v_*(s)\)(或者最优行动价值\(q_*(s, a)\))决定。  

如果把\(v_*(s)\)(或者\(q_*(s, a)\))看成一个函数，因此：强化学习变成了求这个函数。




在近似方法中\(v_*(s)\)(或者\(q_*(s, a)\))表示为近似预测函数\(\hat{v}(s, \theta)\)(或者近似控制函数\(\hat{q}(s, a, \theta)\))。  

以近似预测函数\(\hat{v}(s, \theta)\)为例：  

\[
\hat{v} \doteq \theta^T \phi(s)
\]  

\(\phi(s)\)可以认为是固定的。它是将状态变成一个计算向量的方法。  

因此，求近似预测函数\(\hat{v}(s, \theta)\)，就是求解权重向量\(\theta\)。  

求权重向量\(\theta\)是通过梯度下降的方法。比如：  

\[
\delta_t = G_t - \hat{v}(S_t, \theta_t) \\
\theta_{t+1} = \theta_t + \alpha \delta_t \nabla \hat{v}(S_t, \theta_t)
\]  

这里面，有三个元素：\(\alpha, G_t, \nabla \hat{v}(S_t, \theta_t)\)。每个都有自己的优化方法。






  * \(\alpha\)是学习步长  

要控制步长的大小。一般情况下步长是变化的。比如：如果误差\(\delta_t\)变大了，步长要变小。


  * \(G_t\)的计算  

可以通过本章的\(\lambda\) - return方法。


  * \(\nabla \hat{v}(S_t, \theta_t)\)  

可以通过资格迹来优化。资格迹就是优化后的函数微分。  

为什么要优化，原因是在TD算法中\(\hat{v}(S_t, \theta_t)\)是不精确的。  

\(G_t\)也是不精确的。




### \(\lambda\) - return




\(\lambda\) - return 提供了一个新的方式来估算\(G_t\)，这个新的估值为\(G_t^{\lambda}\)。  

它是由它后面的所有\(G_t^{(n)}\)的加权平均值。  

从下面的公式可以看出，这个方法可以用于连续性任务和情节性任务。




\[
G_t^{(n)} \doteq R_{t+1} + \gamma R_{t+2} + \dots + \gamma^{n-1} R_{t+n} + \gamma^n \hat{v}(S_{t+n}, \theta_{t+n-1}) , \ 0 \le t \le T-n \\
\text{Continuing tasks: } \\
G_t^{\lambda} \doteq (1 - \lambda) \sum_{n=1}^{\infty} \lambda^{n-1}G_t^{(n)} \\
\text{Episodic tasks: } \\
G_t^{\lambda} \doteq (1 - \lambda) \sum_{n=1}^{T-t-1} \lambda^{n-1}G_t^{(n)} + \lambda^{T-t-1}G_t \\
where \\
\lambda \in [0, 1] \\
(1 - \lambda) \sum_{n=1}^{\infty}\lambda^{n-1} = 1 \\
(1 - \lambda) \sum_{n=1}^{T-t-1} \lambda^{n-1} + \lambda^{T-t-1} = 1 \\
\]






  * 算法描述




<blockquote>

> 
> Input: the policy \(\pi\) to be evaluated  

Input: a differentiable function \(\hat{v} : \mathcal{S} \times \mathbb{R^n} \to \mathbb{R}\)
> 
> 

> 
> Initialize value-function weights \(\theta\) arbitrarily (e.g. \(\theta = 0\))  

Repeat (for each episode):  

  Generate an episode \(S_0, A_0, R_1 ,S_1 ,A_1, \cdots ,R_t ,S_t\) using \(\pi\)  

  For \(t = 0, 1, \cdots, T - 1\)  

   \(\theta \gets \theta + \alpha [\color{Red}{G_t^{\lambda}} -\hat{v}(S_t, \theta)] \nabla \hat{v}(S_t, \theta)\)
> 
> 
</blockquote>





比较下面这个算法（第9章的蒙特卡罗方法），红色是不同之处。






  * 算法描述




<blockquote>

> 
> Input: the policy \(\pi\) to be evaluated  

Input: a differentiable function \(\hat{v} : \mathcal{S} \times \mathbb{R^n} \to \mathbb{R}\)
> 
> 

> 
> Initialize value-function weights \(\theta\) arbitrarily (e.g. \(\theta = 0\))  

Repeat (for each episode):  

  Generate an episode \(S_0, A_0, R_1 ,S_1 ,A_1, \cdots ,R_t ,S_t\) using \(\pi\)  

  For \(t = 0, 1, \cdots, T - 1\)  

   \(\theta \gets \theta + \alpha [\color{Red}{G_t} -\hat{v}(S_t, \theta)] \nabla \hat{v}(S_t, \theta)\)
> 
> 
</blockquote>





可以看出当\(\lambda=1\)的时候，\(\lambda\) - return算法就是蒙特卡罗算法。所以说**\(\lambda\) - return算法是蒙特卡罗算法的通用化算法**。




\(\lambda\)和\(\gamma\)一起控制了n步回报\(G_t^{(n)}\)的权重。




## TD(\(\lambda\))




\(e_t\) - 第t步资格迹向量(eligibility trace rate)。  

资格迹向量是近似价值函数的优化微分值。  

其优化的技术称为(backward view)。仔细观察公式可以发现\(e_t\)的算法中包含了以前的微分值。






  * 数学公式  

\[
e_0 \doteq 0 \\
e_t \doteq \nabla \hat{v}(S_t, \theta_t) + \gamma \lambda e_{t-1} \\
\delta_t \doteq R_{t+1} + \gamma \hat{v}(S_{t+1}, \theta_t) - \hat{v}(S_{t}, \theta_t) \\
\theta_{t+1} \doteq \theta_t + \alpha \delta_t e_t \\
where \\
e_t \text{ - eligibility accumulating traces, the estimation differential of } \nabla \hat{v}(S_t, \theta) \\
\delta_t \text{ - the TD error} \\
\theta_t \text{ - the weighted vector in the approximation value function } \hat{v}(S, \theta) \\
\]



  * 算法描述(Semi-gradient TD(\(\lambda\)) for estimating \(\hat{v} \approx v_{\pi}\))  

请参考原书。





## On-line Forward View




On-line和off-line的一个区别是off-line的数据是完整的，比如拥有一个情节的所有Return（G)。  

这个导致off-line算法不适合on-line的情景，就是说在完成一个情节前，学习不到任何东西。  

这个章节要开发一个on-line的算法，首先引入一个概念h。  

h（horizon）- 水平线h表示on-line当时可以模拟的数据步骤。\(t < h \le T\)  

没有步骤h之后的数据。






  * h-truncated \(\lambda\)-return  

\[
G_t^{\lambda | h} \doteq (1 - \lambda) \sum_{n=1}^{h-t-1} \lambda^{n-1} G_t^{(n)} + \lambda^{h-t-1} G_t^{(h-t)}, \ 0 \le t < h \le T \\
\theta_{t+1}^h \doteq \theta_{t}^h \alpha \left [ 
G_t^{\lambda | h} - \hat{v}(S_t, \theta_t^h) \right ] \nabla \hat{v}(S_t, \theta_t^h) , \ 0 \le t < h \le T \\
\theta_t \doteq \ \theta_t^t \\
where \\
h \text{ - the horizon, we have the n-step returns up to the the horizon, but beyond the horizon there is no data}
\]




## True on-line TD(\(\lambda\))




\[
e_0 \doteq 0 \\
e_t \doteq \gamma \lambda e_{t-1} + (1 - \alpha \gamma \lambda e_{t-1}^T \phi_t) \phi_t \\
\delta_t \doteq R_{t+1} + \gamma \hat{v}(S_{t+1}, \theta_t) - \hat{v}(S_{t}, \theta_t) \\
\theta_{t+1} \doteq \theta_t + \alpha \delta_t e_t + \alpha \left ( \theta_t^T \phi_t - \theta_{t-1}^T \phi_t \right ) (e_t - \phi_t) \\
where \\
e_t \text{ - eligibility dutch trace, the estimation differential of } \nabla \hat{v}(S_t, \theta) \\
\delta_t \text{ - the TD error} \\
\theta_t \text{ - the weighted vector in the approximation value function } \hat{v}(s, \theta) \\
\hat{v}(s, \theta) = \theta^T \phi(s)  \\
\]






  * 算法描述(True Online TD(\(\lambda\)) for estimating \(\theta^T \phi \approx v_{\pi}\))  

请参考原书。




**原书还没有完成，这章先停在这里**




















* * *





# COMMENT



