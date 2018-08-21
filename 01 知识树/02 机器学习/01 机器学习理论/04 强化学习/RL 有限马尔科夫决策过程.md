---
author: evo
comments: true
date: 2018-05-16 16:26:26+00:00
layout: post
link: http://106.15.37.116/2018/05/17/rl-%e6%9c%89%e9%99%90%e9%a9%ac%e5%b0%94%e7%a7%91%e5%a4%ab%e5%86%b3%e7%ad%96%e8%bf%87%e7%a8%8b/
slug: rl-%e6%9c%89%e9%99%90%e9%a9%ac%e5%b0%94%e7%a7%91%e5%a4%ab%e5%86%b3%e7%ad%96%e8%bf%87%e7%a8%8b
title: RL 有限马尔科夫决策过程
wordpress_id: 5878
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


# [强化学习读书笔记 - 03 - 有限马尔科夫决策过程](http://www.cnblogs.com/steven-yang/p/6480666.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




## 代理-环境接口(The agent-environment interface)




代理(agent) - 学习者或者决策者  

环境(environment) - 代理外部的一切，代理与之交互。




## 情节性任务(Episodic Tasks)和连续任务(Continuing Tasks)




情节性任务(Episodic Tasks)，所有的任务可以被可以分解成一系列情节。逻辑上，可以看作为有限步骤的任务。  

连续任务(Continuing Tasks) ，所有的任务不能分解。可以看作为无限步骤任务。




## 马尔科夫属性(The Markov property)




state - 马尔科夫属性，表示当前环境的状态。  

举个例子：一个国际象棋的state可能包含：棋盘上所有棋子的位置，上一步的玩家，上一步的走法。




看看下面的公式：  

这个公式在计算下一步（状态是\(s'\)、奖赏是\(r\)）的概率。  

并说明这个概率是由至今为止所有的状态\(S*\)，行动\(A*\)和奖赏\(R*\)决定的。  

\[
Pr\{s_{t+1} = s', R_{t+1} = r | S_0, A_0, R_1, S_1, A_1, \dots, R_t, S_t, A_t \} \\
\]




如果，我们有马尔科夫属性state，有了现在环境的所有状态，那么上面的公式可以简化为：  

这个公式的含义是下一步（状态是\(s'\)、奖赏是\(r\)）的概率是**由马尔科夫属性\(s\)和行动\(a\)决定的**。  

\[
p(s', r | s, a) = Pr \{S_{t+1} = s', R_{t+1} = r | S_t = s, A_t = a \}
\]




## 马尔科夫决策过程 - 数学模型




马尔科夫决策过程是一个强化学习问题的数学描述模型。  

这个数学模型可以从几个视图来学习。






  * 状态(state)-行动(action)-奖赏(reward)视图


  * 目标(goal)-奖赏(reward)视图


  * 决策过程视图


  * 策略(policy)视图




## 状态(state)-行动(action)-奖赏(reward)视图




Markov Decision Processes - TermsMarkov Decision Processes - Termsstates  (state)state1s' (state)state->state1 r  (reward) a  (action)




这是一个马尔科夫抉择过程的基本视图。  

描述agent在状态\(s\)下，选择了行动\(a\)，状态变为\(s'\)，获得了奖赏\(r\)。  

这个很容易理解，说明奖赏是行动引起状态转变后得到的。  

举个特殊例子：天上掉馅饼的过程：行动是等待；新状态是获得馅饼。




## 目标(goal)-奖赏(reward)视图




Markov Decision Processes - GoalMarkov Decision Processes - GoalS2...S_tS_tS2->S_tS_t_1S_t+1S_t_2...S_t_1->S_t_2 R_t+2A_t+1S0S0S1S1S0->S1R1A0S1->S2R2A1S_t->S_t_1 R_t+1A_t




奖赏假设(reward hypothesis) - 目标就是：最大化长期累计奖赏的期望值。  

注：不是立即得到的奖赏。  

回报\(G_t\)：  

\[
G_t \doteq \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} \\
where \\
\gamma \text{ - is a parameter, discount rate, } 0 \leqslant \gamma \leqslant 1 
\]  

\(\gamma\)折扣率决定了未来奖赏的当前价值：  

在k步之后的一个奖赏，如果换算成当前奖赏，需要乘以它的\(\gamma^{k-1}\)倍。




**情节性任务(episodic tasks)的回报计算**  

\[
G_t \doteq \sum_{k=0}^{T-t-1} \gamma^k R_{t+k+1} \quad (T = \infty \text{ or } \gamma = 1 \text{ (but not both)}) \\
where \\
T \ne \infty \text{ - case of episodic tasks} \\
T = \infty \text{ - case of continuing tasks}
\]




## 决策过程视图




Reinforcement Learning - Markov Decision ProcessesReinforcement Learning - Markov Decision Processesssas->a a  a_2s->a_2 a_2  s_1s's_2s_2'ra->rp(s'|s,a)r_3a->r_3p(s_2'|s,a)r_4a_2->r_4 r->s_1p(s',r|s,a),rr->s_1p(s',r'|s,a),r'r'r_3->s_2 r_4->s_1




上图说明了：






  1. 状态\(s\)下，采取行动\(a\)，转变成新状态\(s'\)，是由概率\(p(s' | s, a)\)决定的。


  2. 状态\(s\)下，采取行动\(a\)，转变成新状态\(s'\)，获得的奖赏\(r\)，是由概率\(p(s', r | s, a)\)决定的。


  3. 引起状态\(s\)到状态\(s'\)的转变行动，不一定是唯一的。




### 相应的数学定义和公式




在状态\(s\)下，执行行动\(a\)，转变为状态\(s'\)并获得奖赏\(r\)的可能性：  

\[
p(s', r | s, a) \doteq Pr \{S_{t+1} = s', R_{t+1} = r | S_t = s, A_t = a \}
\]




在状态\(s\)下，执行行动\(a\)的期望奖赏：  

\[
r(s,a) \doteq \mathbb{E}[R_{t+1} | S_t = s, A_t = a] = \sum_{r \in \mathcal{R} } r \sum_{s' \in \mathcal{S} } p(s', r|s,a)
\]




在状态\(s\)下，执行行动\(a\)，转变为状态\(s'\)的可能性：  

\[
p(s' | s,a) \doteq Pr \{S_{t+1} = s' | S_t=s, A_t=a \} = \sum_{r \in \mathcal{R} } p(s',r | s,a)
\]




在状态\(s\)下，执行行动\(a\)，转变为状态\(s'\)的期望奖赏：  

\[
r(s,a,s') \doteq \mathbb{E}[R_{t+1} | S_t = s, A_t = a, S_{t+1} = s'] = \frac{\sum_{r \in \mathcal{R} } r  p(s',r|s,a)}{p(s'|s,a)}
\]




## 策略视图




强化学习的目标是找到（可以获得长期最优回报）的最佳策略。




\(\pi\) - 策略(policy)。  

\(\pi\) - 策略(policy)。强化学习的目标：**找到最优策略**。  

策略规定了状态\(s\)时，应该选择的行动\(a\)。  

\[
\pi = [\pi(s_1), \cdots, \pi(s_n)]
\]  

\(\pi(s)\) - 策略\(\pi\)在状态\(s\)下，选择的行动。  

\(\pi_*\) - 最优策略(optimal policy)。  

\(\pi(a | s)\) - **随机策略**\(\pi\)在状态\(s\)下，选择的行动\(a\)的概率。




### 价值方法(Value Functions)




**使用策略\(\pi\)，状态价值方法 - state-value function**  

\[
v_{\pi}(s) \doteq \mathbb{E}[G_t | S_t = s] = \mathbb{E}_{\pi} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} | S_t = s \right ] \\
where \\
\pi \text{ - polity} \\
\mathbb{E}_{\pi}[\cdot] \text{ - the expected value of a value follows policy } \pi
\]




**使用策略\(\pi\)，行动价值方法 - action-value function**  

\[
q_{\pi}(s,a) \doteq \mathbb{E}[G_t | S_t = s, A_t = a] = \mathbb{E}_{\pi} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} | S_t = s, A_t = a \right ] \\
\]




**使用策略\(\pi\)，迭代状态价值方法 - iterative state-value function**  

a.k.a Bellman equation for \(v_{\pi}\)




\[
\begin{align}
v_{\pi}(s) 
    & \doteq \mathbb{E}[G_t | S_t = s] \\
    & = \mathbb{E}_{\pi} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} | S_t = s \right ] \\
    & = \mathbb{E}_{\pi} \left [ R_{t+1} + \gamma\sum_{k=0}^{\infty} \gamma^k R_{t+k+2} | S_t = s \right ] \\
    & = \sum_{a} \pi(a|s) \sum_{s'} \sum_{r} p(s',r|s,a) \left [ r + \gamma\mathbb{E}_{\pi} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+2} | S_{t+1} = s' \right ] \right ] \\
    & = \sum_{a} \pi(a|s) \sum_{s',r} p(s',r|s,a) \left [ r + \gamma v_{\pi}(s') \right ], \ \forall s \in \mathcal{S}
\end{align}
\]




### 最优价值方法(Optimal Value Functions)




**最优状态价值方法 - optimal state-value function**  

\[
v_*(s) \doteq \underset{\pi}{max} \ v_{\pi}(s), \forall s \in \mathcal{S}
\]




**最优行动价值方法 - optimal action-value function**  

\[
q_*(s, a) \doteq \underset{\pi}{max} \ q_{\pi}(s, a), \ \forall s \in \mathcal{S} \ and \ a \in \mathcal{A}(s)
\]




最优的行为价值等于最优的状态价值下的最大期望：  

\[
q_*(s,a) = \mathbb{E}[R_{t+1} + \gamma v_* (S_{t+1}) \ | \ S_t = s, A_t = a]
\]




**最优状态价值迭代方法 - interval optimal state-value function**  

\[
\begin{align}
v_*(s)
    & = \underset{a \in \mathcal{A}(s)}{max} \ q_{\pi_*}(s, a) \\
    & = \underset{a}{max} \ \mathbb{E}_{\pi*} [G_t \ | \ S_t=s, A_t=a] \\
    & = \underset{a}{max} \ \mathbb{E}_{\pi*} \left [ \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} \ | \ S_t=s, A_t=a \right ] \\
    & = \underset{a}{max} \ \mathbb{E}_{\pi*} \left [ R_{t+1} + \gamma\sum_{k=0}^{\infty} \gamma^k R_{t+k+2} \ | \ S_t=s, A_t=a \right ] \\
    & = \underset{a}{max} \ \mathbb{E}[R_{t+1} + \gamma v_*(S_{t+1}) \ | \ S_t=s, A_t=a ] \\
    & = \underset{a \in \mathcal{A}(s)}{max} \sum_{s',r} p(s',r|s,a)[r + \gamma v_*(s')]  \\
\end{align}
\]


























* * *





# COMMENT



