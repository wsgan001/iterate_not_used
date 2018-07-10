

# 缘由：


对强化学习总结一下，实际上感觉强化学习还是很多的。


# 1.强化学习成就






  * Learned the world’s best player of Backgammon (Tesauro 1995)


  * Learned acrobatic helicopter autopilots (Ng, Abbeel, Coates et al2006+)


  * Widely used in the placement and selection of advertisements on the web (e.g. A-B tests)


  * Used to make strategic decisions in Jeopardy! (IBM’s Watson 2011)


  * Achieved human-level performance on Atari games from pixel-level visual input, in conjunction with deep learning (GoogleDeepmind 2015)


  * In all these cases, performance was better than could be obtained by any other method, and was obtained without human instruction




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac35e5d2c796.png)


Human-level control through deep reinforcement learning 2015 Nature


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac35fb95f27e.png)





# 强化学习+深度学习




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac35fef2fc33.png)





# 强化学习要点大纲






  * 强化学习的难点？


    * credit assignment problem


    * the exploration-exploitation dilemma





  * 怎么定义强化学习？


    * 马尔可夫决策过程





  * 怎么把“眼光”放长远？


    * discounted future reward





  * 怎么预估“未来收益”？


    * table-based Q-learning 算法





  * 状态空间太大怎么办？


    * 使用深度神经网络





  * 如何实际应用


    * “重演”策略







# 强化学习问题：




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac360429b3f6.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3604a84747.png)


David Silver reinforcement leanring Lecture 1


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac360599524d.png)


Atari Breakout游戏




  * 三种动作：向左，向右以及开火（把球射出）


  * 状态：所处的位置，屏幕上方状况等


  * 奖励：得分增减


传统有监督？无监督？强化学习：有稀疏并延时的标签：奖励



信用分配问题 (credit assignment problem)


  * 击中砖块并且得分和前一时刻如何移动横杆没有直接关系


  * 前面某一时刻的横杆移动有作用


探索-利用困境（exploration-exploitation dilemma）


  * 游戏开始，只发射球，不移动横杆，你总是能得10分的


  * 满足于得分还是去探索更多的可能性？(可能更好或者更坏)




# 马尔可夫决策过程(MDP)






  * 操作者或智能体（agent）


  * 状态（state）（比如横杆的位置，球的位置，球的方向，当前环境中的砖块等等）


  * 动作（actions）（比如向左或向右移动横杆）


  * 奖励（reward）（比如分数的增加）


  * 策略（policy）：在state下采取行动的原则




## 强化学习的难点？




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac360fa0b85f.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36101c11cc.png)





## 打折的未来奖励 Discounted Future Reward






  * 一个马尔科夫决策过程，它对应的奖励总和：


\[R=r_1+r_2+r_3+\cdots +r_n\]


  * t时刻的未来奖励：


\[R_t=r_t+r_{t+1}+r_{t+2}+\cdots +r_n\]


  * 无法确定在两次采取相同动作，agent能够获得相同的奖励，未来有不确定性，计算“ 打折的未来奖励”


\[R_t=r_t+\gamma r_{t+1}+\gamma^2r_{t+2}+\cdots +\gamma^{n-t}r_n\]


  * gamma是一个0到1的值，使得我们更少地考虑哪些更长远未来的奖励


  * \(R_t\)可以用\(R_{t+1}\)来表示，写成递推式


\[R_t=r_t+\gamma (r_{t+1}+\gamma (r_{t+2}+\cdots ))=r_t+\gamma R_{t+1}\]




# Q-learning




## Q-learning


Q(s, a)函数，用来表示智能体在s状态下采用a动作并在之后采取最优动作条件下的打折的未来奖励。

\[Q(s_t,a_t)=maxR_{t+1}\]

假设得到了Q-函数，你只要选取Q-函数值最大的动作(policy明确)

\[\pi (s)=argmax_aQ(s,a)\]

我们可以采用与打折的未来奖励相同的方式定义这一状态下的Q函数

\[Q(s,a)=r+\gamma max_{a'}Q(s',a')\]

贝尔曼公式实际非常合理。对于某个状态来讲，最大化未来奖励相当于最大化即刻奖励与下一状态最大未来奖励之和。


## Q-learning 核心思想


Q-learning的核心思想是：我们能够通过贝尔曼公式迭代地近似Q-函数。

最简单的情况下，我们可以采用一种填表的方式学习Q-函数。这个表包含状态空间大小的行，以及动作个数大小的列。填表的算法伪码如下所示：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac362f5e79e9.png)


其中α是在更新Q[s, a]时，调节旧Q[s, a]与新Q[s, a]比例的学习速率。如果α=1，Q[s, a]就被消掉，而更新方式就完全与贝尔曼公式相同。


## Q-learning 经典例子：




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36318ecadf.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3631d71629.png)


http://fromwiz.com/share/s/1CGZRH2S1Aro2gtjMB0TJPbh2WMt0I1fPkJq26Z6cI3pS8GI


# Deep Q Network




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac363cce8057.png)







  * 能不能设计一种通用的表示游戏状态的方法呢？ => 屏幕像素


  * 采用连续两个游戏屏幕的像素，球速和方向等各种信息也可以表示出来


  * 屏幕大小84*84 ，每个像素点有 256 个灰度值，总共 256^(84 * 84*4)~10^67970种可能的状态


  * Q-table有10^67970行，且非常稀疏（有很多状态遇不到！！）


  * 用一个神经网络对Q-函数进行建模


神经网络接收一个状态(连续四步的屏幕)和一个动作，然后输出对应的Q-函数的值

改造一下：只接受一个状态作为输入，然后输出所有动作的分数（具体来讲是动作个数大小的向量），这样一次前向运算可以得到所有动作的得分


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac364133844b.png)


Deep Q Network

DeepMind论文中使用优化的Q网络 DeepMind在论文中使用的网络结构


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3642a22ccf.png)


没有池化层！！

池化层带来位置不变性，会使我们的网络对于物体的位置不敏感，从而无法有效地识别游戏中球的位置

回归问题，loss function如下


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3643ce3dd9.png)





# Experience Replay 经验回放






  * 我们可以用Q-learning的算法估计未来奖励，并能够用一个卷积神经网络近似Q-函数。


  * 但使用Q 值近似非线性的Q-函数可能非常不稳定。即使使用很多小技巧使得这个函数收敛。在单GPU上也可能需要一个星期的时间训练模型。


最重要的技巧是经验回放（experience replay）


  * 在玩游戏的过程中，所有经历的都被记录起来。


  * 训练神经网络时，我们从这些记录的中随机选取一些mini-batch作为训练数据训练，而不是按照时序地选取一些连续的。


  * 按时序选取，训练实例之间相似性较大，网络很容易收敛到局部最小值。




# Exploration-Exploitation


Q-learning算法尝试解决信用分配问题




  * 通过Q-learning，奖励被回馈到关键的决策时刻


依旧存在的是探索-利用困境


  * 在游戏开始阶段，Q-table或Q-network是随机初始化的。它给出的Q-值最高的动作是完全随机的，智能体表现出的是随机的“探索”。


  * 当Q-函数收敛时，随机“探索”的情况减少。所以，Q-learning中包含“探索”的成分。但是这种探索是“贪心”的，它只会探索当前模型认为的最好的策略 。


一种简单修正技巧：


  * \epsilon-贪心探索


  * 以\epsilon的概率选取随机的动作做为下一步动作，1-\epsilon的概率选取分数最高的动作


  * DeepMind的系统中，\epsilon随着时间从1减少到0.1。这意味着开始时，系统完全随机地探索状态空间，最后以固定的概率探索。




# Deep Q-learning Algorithm




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3650ab7a20.png)


DeepMind还使用了一系列其他的技巧，比如：**目标网络、误差截断、回馈截断  要学习**

新的论文进展包括Double Q-learning, Prioritized Experience Replay, Dueling Network Architecture, extension to continuous action space

有兴趣的同学可以关注NIPS 2015 deep reinforcement learning workshop以及ICLR 2016


# Flappy-bird




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac365453ed88.png)


https://github.com/yenchenlin/DeepLearningFlappyBird

Deep Q Network algorithm


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3655477d83.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36595b3b17.png)





#




# COMMENT：


**有很多东西没有总结进来，而且很多东西讲的只是随便讲讲。要自己动手实践总结。**


# REF：






  1. 七月在线 深度学习
