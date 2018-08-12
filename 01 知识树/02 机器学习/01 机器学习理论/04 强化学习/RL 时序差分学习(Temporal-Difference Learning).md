# RL 时序差分学习(Temporal-Difference Learning)

## REF

# [强化学习读书笔记 - 06~07 - 时序差分学习(Temporal-Difference Learning)](http://www.cnblogs.com/steven-yang/p/6516818.html)







# TODO






  * aaa





* * *





# INTRODUCTION






  * aaa




学习笔记：

[Reinforcement Learning: An Introduction, Richard S. Sutton and Andrew G. Barto c 2014, 2015, 2016](https://webdocs.cs.ualberta.ca/~sutton/book/)




数学符号看不懂的，先看看这里：






  * [强化学习读书笔记 - 00 - 术语和数学符号](http://www.cnblogs.com/steven-yang/p/6481772.html)




## 时序差分学习简话




时序差分学习结合了动态规划和蒙特卡洛方法，是强化学习的核心思想。




<blockquote>

>
> 时序差分这个词不好理解。改为当时差分学习比较形象一些 - 表示通过当前的差分数据来学习。
>
>
</blockquote>




蒙特卡洛的方法是模拟（或者经历）一段情节，在情节结束后，根据情节上各个状态的价值，来估计状态价值。

时序差分学习是模拟（或者经历）一段情节，每行动一步（或者几步），根据新状态的价值，然后估计执行前的状态价值。

可以认为蒙特卡洛的方法是最大步数的时序差分学习。

本章只考虑单步的时序差分学习。多步的时序差分学习在下一章讲解。




**数学表示**

根据我们已经知道的知识：如果可以计算出策略价值（\(\pi\)状态价值\(v_{\pi}(s)\)，或者行动价值\(q_{\pi(s, a)}\)），就可以优化策略。

在蒙特卡洛方法中，计算策略的价值，需要完成一个情节(episode)，通过情节的目标价值\(G_t\)来计算状态的价值。其公式：

_Formula MonteCarlo_

\[
V(S_t) \gets V(S_t) + \alpha \delta_t \\
\delta_t = [G_t - V(S_t)] \\
where \\
\delta_t \text{ - Monte Carlo error} \\
\alpha \text{ - learning step size}
\]




时序差分的思想是通过下一个状态的价值计算状态的价值，形成一个迭代公式（又）：

_Formula TD(0)_

\[
V(S_t) \gets V(S_t) + \alpha \delta_t \\
\delta_t = [R_{t+1} + \gamma\ V(S_{t+1} - V(S_t)] \\
where \\
\delta_t \text{ - TD error} \\
\alpha \text{ - learning step size} \\
\gamma \text{ - reward discount rate}
\]




<blockquote>

>
> 注：书上提出TD error并不精确，而Monte Carlo error是精确地。需要了解，在此并不拗述。
>
>
</blockquote>




## 时序差分学习方法




本章介绍的是时序差分学习的单步学习方法。多步学习方法在下一章介绍。






  * 策略状态价值\(v_{\pi}\)的时序差分学习方法(单步\多步)


  * 策略行动价值\(q_{\pi}\)的on-policy时序差分学习方法: Sarsa(单步\多步)


  * 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法: Q-learning(单步)


  * Double Q-learning(单步)


  * 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法(带importance sampling): Sarsa(多步)


  * 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法(不带importance sampling): Tree Backup Algorithm(多步)


  * 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法: \(Q(\sigma)\)(多步)




## 策略状态价值\(v_{\pi}\)的时序差分学习方法




**单步时序差分学习方法TD(0)**






  * 流程图




Reinforcement Learning - TD0Reinforcement Learning - TD0sSs_1S's->s_1 A  RvV(S)s->vv_1V(S')s_1->v_1v->v_1R






  * 算法描述




<blockquote>

>
> Initialize \(V(s)\) arbitrarily \(\forall s \in \mathcal{S}^+\)

Repeat (for each episode):

  Initialize \(\mathcal{S}\)

  Repeat (for each step of episode):

   \(A \gets\) action given by \(\pi\) for \(S\)

   Take action \(A\), observe \(R, S'\)

   \(V(S) \gets V(S) + \alpha [R + \gamma V(S') - V(S)]\)

   \(S \gets S'\)

  Until S is terminal
>
>
</blockquote>





**多步时序差分学习方法**






  * 流程图




Reinforcement Learning - TD nReinforcement Learning - TD nsSs_1...s->s_1 A0  RkvV(S)s->vs_2Sns_1->s_2 An-1  Rnv_1V(...)s_1->v_1v_2V(Sn)s_2->v_2v->v_1Rkv_1->v_2Rn






  * 算法描述




<blockquote>

>
> Input: the policy \(\pi\) to be evaluated

Initialize \(V(s)\) arbitrarily \(\forall s \in \mathcal{S}\)

Parameters: step size \(\alpha \in (0, 1]\), a positive integer \(n\)

All store and access operations (for \(S_t\) and \(R_t\)) can take their index mod \(n\)
>
>

>
> Repeat (for each episode):

  Initialize and store \(S_0 \ne terminal\)

  \(T \gets \infty\)

  For \(t = 0,1,2,\cdots\):

   If \(t < T\), then:

    Take an action according to \(\pi(\dot \ | S_t)\)

    Observe and store the next reward as \(R_{t+1}\) and the next state as \(S_{t+1}\)

    If \(S_{t+1}\) is terminal, then \(T \gets t+1\)

   \(\tau \gets t - n + 1 \ \) (\(\tau\) is the time whose state's estimate is being updated)

   If \(\tau \ge 0\):

    \(G \gets \sum_{i = \tau + 1}^{min(\tau + n, T)} \gamma^{i-\tau-1}R_i\)

    if \(\tau + n \le T\) then: \(G \gets G + \gamma^{n}V(S_{\tau + n}) \qquad \qquad (G_{\tau}^{(n)})\)

    \(V(S_{\tau}) \gets V(S_{\tau}) + \alpha [G - V(S_{\tau})]\)

  Until \(\tau = T - 1\)
>
>
</blockquote>





这里要理解\(V(S_0)\)是由\(V(S_0), V(S_1), \dots, V(S_n)\)计算所得；\(V(S_1)\)是由\(V(S_1), V(S_1), \dots, V(S_{n+1})\)。




## 策略行动价值\(q_{\pi}\)的on-policy时序差分学习方法: Sarsa




**单步时序差分学习方法**






  * 流程图




Reinforcement Learning - TD SarsaReinforcement Learning - TD SarsasSs_1S's->s_1 A  RqQ(S, A)s->qq_1Q(S', A')s_1->q_1q->q_1R






  * 算法描述




<blockquote>

>
> Initialize \(Q(s, a), \forall s \in \mathcal{S}, a \in \mathcal{A}(s)\) arbitrarily, and \(Q(terminal, \dot \ ) = 0\)

Repeat (for each episode):

  Initialize \(\mathcal{S}\)

  Choose \(A\) from \(S\) using policy derived from \(Q\) (e.g. \(\epsilon-greedy\))

  Repeat (for each step of episode):

   Take action \(A\), observe \(R, S'\)

   Choose \(A'\) from \(S'\) using policy derived from \(Q\) (e.g. \(\epsilon-greedy\))

   \(Q(S, A) \gets Q(S, A) + \alpha [R + \gamma Q(S', A') - Q(S, A)]\)

   \(S \gets S'; A \gets A';\)

  Until S is terminal
>
>
</blockquote>





**多步时序差分学习方法**






  * 流程图




Reinforcement Learning - TD SarsaReinforcement Learning - TD SarsasSs_1...s->s_1 A  RkqQ(S, A)s->qs_2Sns_1->s_2An-1Rnq_1Q(...)s_1->q_1q_2Q(Sn, An)s_2->q_2q->q_1Rkq_1->q_2Rn






  * 算法描述




<blockquote>

>
> Initialize \(Q(s, a)\) arbitrarily \(\forall s \in \mathcal{S}^, \forall a in \mathcal{A}\)

Initialize \(\pi\) to be \(\epsilon\)-greedy with respect to Q, or to a fixed given policy

Parameters: step size \(\alpha \in (0, 1]\),

  small \(\epsilon > 0\)

  a positive integer \(n\)

All store and access operations (for \(S_t\) and \(R_t\)) can take their index mod \(n\)
>
>

>
> Repeat (for each episode):

  Initialize and store \(S_0 \ne terminal\)

  Select and store an action \(A_0 \sim \pi(\dot \ | S_0)\)

  \(T \gets \infty\)

  For \(t = 0,1,2,\cdots\):

   If \(t < T\), then:

    Take an action \(A_t\)

    Observe and store the next reward as \(R_{t+1}\) and the next state as \(S_{t+1}\)

    If \(S_{t+1}\) is terminal, then:

     \(T \gets t+1\)

    Else:

     Select and store an action \(A_{t+1} \sim \pi(\dot \ | S_{t+1})\)

   \(\tau \gets t - n + 1 \ \) (\(\tau\) is the time whose state's estimate is being updated)

   If \(\tau \ge 0\):

    \(G \gets \sum_{i = \tau + 1}^{min(\tau + n, T)} \gamma^{i-\tau-1}R_i\)

    if \(\tau + n \le T\) then: \(G \gets G + \gamma^{n} Q(S_{\tau + n}, A_{\tau + n}) \qquad \qquad (G_{\tau}^{(n)})\)

    \(Q(S_{\tau}, A_{\tau}) \gets Q(S_{\tau}, A_{\tau}) + \alpha [G - Q(S_{\tau}, A_{\tau})]\)

    If {\pi} is being learned, then ensure that \(\pi(\dot \ | S_{\tau})\) is \(\epsilon\)-greedy wrt Q

  Until \(\tau = T - 1\)
>
>
</blockquote>





## 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法: Q-learning




Q-learning 算法（Watkins, 1989）是一个突破性的算法。这里利用了这个公式进行off-policy学习。

\[
Q(S_t, A_t) \gets Q(S_t, A_t) + \alpha [R_{t+1} + \gamma \underset{a}{max} \ Q(S_{t+1}, a) - Q(S_t, A_t)]
\]




**单步时序差分学习方法**






  * 算法描述




<blockquote>

>
> Initialize \(Q(s, a), \forall s \in \mathcal{S}, a \in \mathcal{A}(s)\) arbitrarily, and \(Q(terminal, \dot \ ) = 0\)

Repeat (for each episode):

  Initialize \(\mathcal{S}\)

  Choose \(A\) from \(S\) using policy derived from \(Q\) (e.g. \(\epsilon-greedy\))

  Repeat (for each step of episode):

   Take action \(A\), observe \(R, S'\)

   \(Q(S, A) \gets Q(S, A) + \alpha [R + \gamma \underset{a}{max} \ Q(S‘, a) - Q(S, A)]\)

   \(S \gets S';\)

  Until S is terminal
>
>
</blockquote>



  * Q-learning使用了max，会引起一个最大化偏差(Maximization Bias)问题。

具体说明，请看书上的Example **6.**7。**

使用Double Q-learning可以消除这个问题。





## Double Q-learning




**单步时序差分学习方法**




<blockquote>

>
> Initialize \(Q_1(s, a)\) and \(Q_2(s, a), \forall s \in \mathcal{S}, a \in \mathcal{A}(s)\) arbitrarily

Initialize \(Q_1(terminal, \dot \ ) = Q_2(terminal, \dot \ ) = 0\)

Repeat (for each episode):

  Initialize \(\mathcal{S}\)

  Repeat (for each step of episode):

   Choose \(A\) from \(S\) using policy derived from \(Q_1\) and \(Q_2\) (e.g. \(\epsilon-greedy\))

   Take action \(A\), observe \(R, S'\)

   With 0.5 probability:

    \(Q_1(S, A) \gets Q_1(S, A) + \alpha [R + \gamma Q_2(S', \underset{a}{argmax} \ Q_1(S', a)) - Q_1(S, A)]\)

   Else:

    \(Q_2(S, A) \gets Q_2(S, A) + \alpha [R + \gamma Q_1(S', \underset{a}{argmax} \ Q_2(S', a)) - Q_2(S, A)]\)

   \(S \gets S';\)

  Until S is terminal
>
>
</blockquote>




## 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法(by importance sampling): Sarsa




考虑到重要样本，把\(\rho\)带入到Sarsa算法中，形成一个off-policy的方法。

\(\rho\) - 重要样本比率(importance sampling ratio)

\[
\rho \gets \prod_{i = \tau + 1}^{min(\tau + n - 1, T -1 )} \frac{\pi(A_t|S_t)}{\mu(A_t|S_t)} \qquad \qquad (\rho_{\tau+n}^{(\tau+1)})
\]




**多步时序差分学习方法**






  * 算法描述




<blockquote>

>
> Input: behavior policy \mu such that \(\mu(a|s) > 0，\forall s \in \mathcal{S}, a \in \mathcal{A}\)

Initialize \(Q(s，a)\) arbitrarily \(\forall s \in \mathcal{S}^, \forall a in \mathcal{A}\)

Initialize \(\pi\) to be \(\epsilon\)-greedy with respect to Q, or to a fixed given policy

Parameters: step size \(\alpha \in (0, 1]\),

  small \(\epsilon > 0\)

  a positive integer \(n\)

All store and access operations (for \(S_t\) and \(R_t\)) can take their index mod \(n\)
>
>

>
> Repeat (for each episode):

  Initialize and store \(S_0 \ne terminal\)

  Select and store an action \(A_0 \sim \mu(\dot \ | S_0)\)

  \(T \gets \infty\)

  For \(t = 0,1,2,\cdots\):

   If \(t < T\), then:

    Take an action \(A_t\)

    Observe and store the next reward as \(R_{t+1}\) and the next state as \(S_{t+1}\)

    If \(S_{t+1}\) is terminal, then:

     \(T \gets t+1\)

    Else:

     Select and store an action \(A_{t+1} \sim \pi(\dot \ | S_{t+1})\)

   \(\tau \gets t - n + 1 \ \) (\(\tau\) is the time whose state's estimate is being updated)

   If \(\tau \ge 0\):

    \(\rho \gets \prod_{i = \tau + 1}^{min(\tau + n - 1, T -1 )} \frac{\pi(A_t|S_t)}{\mu(A_t|S_t)} \qquad \qquad (\rho_{\tau+n}^{(\tau+1)})\)

    \(G \gets \sum_{i = \tau + 1}^{min(\tau + n, T)} \gamma^{i-\tau-1}R_i\)

    if \(\tau + n \le T\) then: \(G \gets G + \gamma^{n} Q(S_{\tau + n}, A_{\tau + n}) \qquad \qquad (G_{\tau}^{(n)})\)

    \(Q(S_{\tau}, A_{\tau}) \gets Q(S_{\tau}, A_{\tau}) + \alpha \rho [G - Q(S_{\tau}, A_{\tau})]\)

    If {\pi} is being learned, then ensure that \(\pi(\dot \ | S_{\tau})\) is \(\epsilon\)-greedy wrt Q

  Until \(\tau = T - 1\)
>
>
</blockquote>





### Expected Sarsa






  * 流程图




Reinforcement Learning - TD Expected SarsaReinforcement Learning - TD Expected SarsasSs_1...s->s_1 A  RkqQ(S, A)s->qs_2Sns_1->s_2An-1Rnq_1Q(...)s_1->q_1q_2sum(pi(a|Sn) * Q(Sn, a))s_2->q_2q->q_1Rkq_1->q_2Rn






  * 算法描述

略。




## 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法(不带importance sampling): Tree Backup Algorithm




Tree Backup Algorithm的思想是每步都求行动价值的期望值。

求行动价值的期望值意味着对所有可能的行动\(a\)都评估一次。




**多步时序差分学习方法**






  * 流程图




Reinforcement Learning - TD Tree BackupReinforcement Learning - TD Tree BackupsSs_1...s->s_1 A  RkqQ(S, A)s->qs_2Sns_1->s_2An-1Rnq_1sum(pi(a|...) * Q(..., a))s_1->q_1q_2sum(pi(a|Sn) * Q(Sn, a))s_2->q_2q->q_1Rkq_1->q_2Rn






  * 算法描述




<blockquote>

>
> Initialize \(Q(s，a)\) arbitrarily \(\forall s \in \mathcal{S}^, \forall a in \mathcal{A}\)

Initialize \(\pi\) to be \(\epsilon\)-greedy with respect to Q, or to a fixed given policy

Parameters: step size \(\alpha \in (0, 1]\),

  small \(\epsilon > 0\)

  a positive integer \(n\)

All store and access operations (for \(S_t\) and \(R_t\)) can take their index mod \(n\)
>
>

>
> Repeat (for each episode):

  Initialize and store \(S_0 \ne terminal\)

  Select and store an action \(A_0 \sim \pi(\dot \ | S_0)\)

  \(Q_0 \gets Q(S_0, A_0)\)

  \(T \gets \infty\)

  For \(t = 0,1,2,\cdots\):

   If \(t < T\), then:

    Take an action \(A_t\)

    Observe and store the next reward as \(R_{t+1}\) and the next state as \(S_{t+1}\)

    If \(S_{t+1}\) is terminal, then:

     \(T \gets t+1\)

     \(\delta_t \gets R - Q_t\)

    Else:

     \(\delta_t \gets R + \gamma \sum_a \pi(a|S_{t+1})Q(S_{t+1},a) - Q_t\)

     Select arbitrarily and store an action as \(A_{t+1}\)

     \(Q_{t+1} \gets Q(S_{t+1},A_{t+1})\)

     \(\pi_{t+1} \gets \pi(S_{t+1},A_{t+1})\)

   \(\tau \gets t - n + 1 \ \) (\(\tau\) is the time whose state's estimate is being updated)

   If \(\tau \ge 0\):

    \(E \gets 1\)

    \(G \gets Q_{\tau}\)

    For \(k=\tau, \dots, min(\tau + n - 1, T - 1):\)

     \(G \gets\ G + E \delta_k\)

     \(E \gets\ \gamma E \pi_{k+1}\)

    \(Q(S_{\tau}, A_{\tau}) \gets Q(S_{\tau}, A_{\tau}) + \alpha [G - Q(S_{\tau}, A_{\tau})]\)

    If {\pi} is being learned, then ensure that \(\pi(a | S_{\tau})\) is \(\epsilon\)-greedy wrt \(Q(S_{\tau},\dot \ )\)

  Until \(\tau = T - 1\)
>
>
</blockquote>





## 策略行动价值\(q_{\pi}\)的off-policy时序差分学习方法: \(Q(\sigma)\)




\(Q(\sigma)\)结合了Sarsa(importance sampling), Expected Sarsa, Tree Backup算法，并考虑了重要样本。

当\(\sigma = 1\)时，使用了重要样本的Sarsa算法。

当\(\sigma = 0\)时，使用了Tree Backup的行动期望值算法。




**多步时序差分学习方法**






  * 流程图




Reinforcement Learning - TD Q(sigma)Reinforcement Learning - TD Q(sigma)sSs_1...s->s_1 A  R.qQ(S, A)s->qs_2...s_1->s_2A.R.q_1Q(...)s_1->q_1sigma = 1s_3...s_2->s_3A.R.q_2sum(pi(a|...) * Q(...,a))s_2->q_2sigma = 0s_4Sns_3->s_4An-1Rnq_3Q(...)s_3->q_3sigma = 1q_4sum(pi(a|Sn) * Q(Sn,a))s_4->q_4sigma = 0q->q_1R.q_1->q_2R.q_2->q_3R.q_3->q_4Rn






  * 算法描述




<blockquote>

>
> Input: behavior policy \mu such that \(\mu(a|s) > 0，\forall s \in \mathcal{S}, a \in \mathcal{A}\)

Initialize \(Q(s，a)\) arbitrarily \forall s \in \mathcal{S}^, \forall a in \mathcal{A}$

Initialize \(\pi\) to be \(\epsilon\)-greedy with respect to Q, or to a fixed given policy

Parameters: step size \(\alpha \in (0, 1]\),

  small \(\epsilon > 0\)

  a positive integer \(n\)

All store and access operations (for \(S_t\) and \(R_t\)) can take their index mod \(n\)
>
>

>
> Repeat (for each episode):

  Initialize and store \(S_0 \ne terminal\)

  Select and store an action \(A_0 \sim \mu(\dot \ | S_0)\)

  \(Q_0 \gets Q(S_0, A_0)\)

  \(T \gets \infty\)

  For \(t = 0,1,2,\cdots\):

   If \(t < T\), then:

    Take an action \(A_t\)

    Observe and store the next reward as \(R_{t+1}\) and the next state as \(S_{t+1}\)

    If \(S_{t+1}\) is terminal, then:

     \(T \gets t+1\)

     \(\delta_t \gets R - Q_t\)

    Else:

     Select and store an action as \(A_{t+1} \sim \mu(\dot \ |S_{t+1})\)

     Select and store \(\sigma_{t+1})\)

     \(Q_{t+1} \gets Q(S_{t+1},A_{t+1})\)

     \(\delta_t \gets R + \gamma \sigma_{t+1} Q_{t+1} + \gamma (1 - \sigma_{t+1})\sum_a \pi(a|S_{t+1})Q(S_{t+1},a) - Q_t\)

     \(\pi_{t+1} \gets \pi(S_{t+1},A_{t+1})\)

     \(\rho_{t+1} \gets \frac{\pi(A_{t+1}|S_{t+1})}{\mu(A_{t+1}|S_{t+1})}\)

   \(\tau \gets t - n + 1 \ \) (\(\tau\) is the time whose state's estimate is being updated)

   If \(\tau \ge 0\):

    \(\rho \gets 1\)

    \(E \gets 1\)

    \(G \gets Q_{\tau}\)

    For \(k=\tau, \dots, min(\tau + n - 1, T - 1):\)

     \(G \gets\ G + E \delta_k\)

     \(E \gets\ \gamma E [(1 - \sigma_{k+1})\pi_{k+1} + \sigma_{k+1}]\)

     \(\rho \gets\ \rho(1 - \sigma_{k} + \sigma_{k}\tau_{k})\)

    \(Q(S_{\tau}, A_{\tau}) \gets Q(S_{\tau}, A_{\tau}) + \alpha \rho [G - Q(S_{\tau}, A_{\tau})]\)

    If \({\pi}\) is being learned, then ensure that \(\pi(a | S_{\tau})\) is \(\epsilon\)-greedy wrt \(Q(S_{\tau},\dot \ )\)

  Until \(\tau = T - 1\)
>
>
</blockquote>





## 总结




时序差分学习方法的限制：学习步数内，可获得奖赏信息。

比如，国际象棋的每一步，是否可以计算出一个奖赏信息？如果使用蒙特卡洛方法，模拟到游戏结束，肯定是可以获得一个奖赏结果的。




## 参照


























* * *





# COMMENT
