### TensorFlow实现深度 强化学习

##### 8.1深度强化学习简介

强化学习(Reinforcement Learning )是机器学习的一个重要分支，主要用来解决连 续决策的问题。强化学习可以在复杂的、不确定的环境中学习如何实现我们设定的目标。 强化学习的应用场景非常广，几乎包括了所有需要做一系列决策的问题，比如控制机器人 的电机让它执行特定任务，给商品定价或者库存管理、玩视频游戏或祺牌游戏等。强化学 习也可以应用到有序列输出的问题中，因为它可以针对一系列变化的环境状态，输出一系 列对应的行动，举个简单的例子，围棋(乃至全部棋牌类游戏)可以归结为一个强化学习 问题，我们需要学习在各种局势下如何走出最好的招法。

一个强化学习问题包含三个主要概念，即环境状态(Environment State )、行动(Action) 和奖励(Reward),而强化学习的目标就是获得最多的累计奖励。在围祺中，环境状态就 是我们已经下出来的某个局势，行动是指我们在某个位置落子，奖励则是当前这步棋获得 的目数(围棋中存在不确定性，在结束对弈后计算的目数是准确的，祺局中获得的目数是 估计的)，而最终目标就是在结束对弈时总目数超过对手，赢得胜利。我们要让强化学习

TensorFlow 实战

模型根据环境状态、行动和奖励，学习出最佳的策略，并以最终结果为目标，不能只看某 个行动当下带来的利益（比如围祺中通过某一手棋获得的实地），还要看到这个行动未来 能带来的价值（比如围棋中外势可以带来的潜在价值）。我们回顾一下，AutoEncoder属 于无监督学习，而MLP、CNN和RNN都属于监督学习，但强化学习跟这两种都不同。 它不像无监督学习那样完全没有学习目标，也不像监督学习那样有非常明确的目标（即 label）,强化学习的目标一般是变化的、不明确的，甚至可能不存在绝对正确的标签。

强化学习已经有几十年的历史，但是直到最近几年深度学习技术的突破，强化学习才 有了比较大的进展。Google DeepMind结合强化学习与深度学习，提出DQN61 （Deep Q-Network,深度Q网络），它可以自动玩Atari 2600系列的游戏，并取得了超过人类的 水平。而DeepMind的AlphaGo62结合了策略网络（Policy Network）、估值网络（Value Network,也即DQN）与蒙特卡洛捜索树（Monte Carlo Tree Search ）,实现了具有超高 水平的围棋对战程序，并战胜了世界冠军李世石。DeepMind使用的这些深度强化学习模 型（Deep Reinforcement Learning ）本质上也是神经网络，主要分为策略网络和估值网络 两种。深度强化学习模型对环境没有特别强的限制，可以很好地推广到其他环境，因此对 强化学习的研究和发展具有非常重大的意义。下面我们来看看深度强化学习的一些实际应 用例子。

无人驾驶是一个非常复杂、非常困难的强化学习任务，在深度学习出现之前，几乎不 可能实现。如图8-1所示，无人驾驶汽车通过摄像头、雷达、激光测距仪、传感器等对环 境进行观测，获取到许多丰富的环境信息，然后通过深度强化学习模型中的CNN、RNN 等对环境信息进行处理、抽象和转化，再结合强化学习算法框架预测出最应该执行的动作 （加速、减速、转换方向等），来实现自动驾驶。无人驾驶汽车每次执行的动作，都会让它 到目的地的路程更短，这就是每次行动的奖励。当然，其最终目标是安全地顺利地到达目 的地，这样可以获得最多的奖励。

图8-1自动驾驶包含了对环境物体的识别及对汽车移动的连续控制

深度强化学习的另一个重要应用是操控复杂的机械装置。一般情况下，我们需要给机 械装置编写逻辑非常复杂的控制代码来让它们执行具体的操作，比如控制机械臂拾取小零 件。如果要拾取某个特定形状的小零件，需要单独设计一套逻辑，来控制电机进行一系列 运转，进而驱动机械臂各个关节转动，最终拾取物体。但是这种做法拾取物体的成功率并 不高，而且如果换了一个形状的零件，或者零件的位置发生比较大的变化，那就需要重新 设计逻辑。利用深度强化学习算法，我们可以让机器自己学习如何拾取物体，如图8-2所 示，省去了大量的编程工作。深度强化学习模型中前几层可使用卷积网络，然后可以使用 卷积网络对摄像头捕获的图像进行处理和分析，让模型能“看见”环境并识别出物体位置， 再通过强化学习框架，学习如何通过一系列动作来最高效地拾取物体。另外，当有新零件 出现时，只需要再让机器学习一段时间，就可以掌握抓取新零件的方法，并且这个学习过 程可以自动完成，无须人工干预。事实上，通过深度强化学习我们甚至可以让模型学会自 动驾驶直升机，这是Andrew Ng在讲解强化学习时提到的例子。

图8-2使用深度强化学习模型控制机械臂拾取小零件

同时，我们也可以使用深度强化学习自动玩游戏，如图8-3.所示，用DQN可学习自 动玩Flappy    前几层通常也是卷积层,因此具有了对游戏图像像素(raw pixels )

直接进行学习的能力。前几层卷积可理解和识别游戏图像中的物体，后层的神经网络则对 Action的期望价值进行学习，结合这两个部分，可以得到能根据游戏像素自动玩Happy

的强化学习策略。而且，不仅是这类简单的游戏，连非常复杂的包含大量战术策略的 《星际争霸2》也可以被深度强化学习模型掌握。目前，DeepMind就在探索如何通过深度 强化学习训练一个可以战胜《星际争霸2》世界冠军的人工智能，这之后的进展让我们拭 目以待。

图8-3使用深度强化学习自动玩

深度强化学习最具有代表性的一个里程碑自然是AlphaGo。在2016年，Google DeepMind的AlphaGo以4:1的比分战胜了人类的世界冠军李世石，如图8-4所示。围祺 可以说是棋类游戏中最为复杂的，19x19的棋盘给它带来了 3361种状态，除去其中非法的 违反游戏规则的状态，也有远超整个宇宙中原子数目的状态数。因此，计算机是无法通过 像深蓝那样的暴力搜索来战胜人类的，要在围棋这个项目上战胜人类，就必须给计算机抽 象思维的能力，而AlphaGo做到了这一点。

图8-4 AlphaGo代表了深度强化学习技术的巅峰

在AlphaGo中使用了快速走子(Fast Rollout)、策略网络、估值网络和蒙特卡洛搜索 树等技术。图8-5所示为AlphaGo的几种技术单独使用时的表现，横坐标为步数，纵坐标 为预测的误差(可以理解为误差越低模型效果越好)，其中简单的快速走子策略虽然效果 比较一般，但是已经远胜随机策略。估值网络和策略网络的效果都非常好，相对来说，策

略网络的性能更胜一筹。AlphaGo融合了所有这些策略，取得了比单一策略更好的性能: 在实战中表现出了惊人的水平。

b

s3Lueo)p<uQ.x3C0 J0JJ9 PQJroncrw uraes

图8-5 AlphaGo中随机策略、快速走子、估值网络和策略网络（SL和RL两种）的性能表现

Policy-Based （或者 Policy Gradients ）和 Value-Based （或者 Q-Learning ）是强化学习 中最重要的两类方法，其主要区别在于Policy-Based的方法直接预测在某个环境状态下应 该采取的Action,而Value Based的方法则预测某个环境状态下所有Action的期望价值（Q 值），之后可以通过选择Q值最高的Action执行策略。这两种方法的出发点和训练方式都 有不同，一般来说，Value Based方法适合仅有少量离散取值的Action的环境，而 Policy-Based方法则更通用，适合Action种类非常多或者有连续取值的Action的环境。 而结合深度学习后，Policy-Based的方法就成了 Policy Network,而Value-Based的方法则 成了 Value Network。

图8-6所示为AlphaGo中的策略网络预测出的当前局势下应该采取的Action,图中标 注的数值为策略网络输出的应该执行某个Action的概率，即我们应该在某个位置落子的 概率。

图8-7所示为AlphaGo中估值网络预测出的当前局势下每个Action的期望价值。估 值网络不直接输出策略，而是输出Action对应的Q值，即在某个位置落子可以获得的期 望价值。随后,我们可以直接选择期望价值最大的位置落子，或者选择其他位置进行探索。

###### d    Policy network

###### a    Value network

图8-7 AlphaGo中的估值网络，输出在某个位置落子的期望价值

在强化学习中，我们也可以建立额外的model对环境状态的变化进行预测。普通的强 化学习直接根据环境状态预测出行动策略，或行动的期望价值。如果根据环境状态和采取

的行动预测接下来的环境状态，并利用这个信息训练强化学习模型，那就是model-based RL。对于复杂的环境状态，比如视频游戏的图像像素，要预测这么大量且复杂的环境信 息是非常困难的。如果环境状态是数量不大的一些离散值（m），并且可采取的行动也是 数量较小的一些离散值（n）’那么环境model只是一个简单的mxn的转换矩阵。对于一个 普通的视频游戏环境，假设图像像素为64x64x3,可选行动有18种，那么我们光存储这 个转换矩阵就需要大的难以想象的内存空间（ 25664xMx3x18）。对于更复杂的环境，我们 就更难使用model预测接下来的环境状态。而model-free类型的强化学习则不需要对环境 状态进行任何预测,也不考虑行动将如何影响环境Dmodel-free RL直接对策略或者Action 的期望价值进行预测，因此计算效率非常高。当然，如果有一个良好的model可以高效、 准确地对环境进行预测，会对训练RL带来益处；但是一个不那么精准的model反而会严 重干扰RL的训练。因此，对大多数复杂环境，我们主要使用model-free RL,同时供给 更多的样本给RL训练，用来弥补没有model预测环境状态的问题。

##### 8.2 TensorFlow实现策略网络

前面提到了强化学习中非常重要的3个要素是Environment State、Action和Reward。 在环境中，强化学习模型的载体是Agent,它负责执行模型给出的行动。环境是Agent无 法控制的，但是可以进行观察；根据观察的结果，模型给出行动，交由Agent来执行；而 Reward是在某个环境状态下执行了某个Action而获得的，是模型要争取的目标。在很多 任务中，Reward是延迟获取的（Delayed ），即某个Action除了可以即时获得Reward,也 可能跟未来获得的Reward有很大关系。

'所谓策略网络，即建立一个神经网络模型，它可以通过观察环境状态，直接预测出目 前最应该执行的策略（Policy ）,执行这个策略可以获得最大的期望收益（包括现在的和 未来的Reward）。与普通的监督学习不同，在强化学习中，可能没有绝对正确的学习目标， 样本的feature不再和label 一一对应。对某一个特定的环境状态，我们并不知道它对应的 最好的Action是什么,只知道当前Action获得的Reward还有试验后获得的未来的Reward。 我们需要让强化学习模型通过试验样本自己学习什么才是某个环境状态下比较好的 Action,而不是告诉模型什么才是比较好的Action,因为我们也不知道正确的答案（即样 本没有绝对正确的label,只有估算出的label）。我们的学习目标是期望价值，即当前获得 的Reward,加上未来潜在的可获取的reward。为了更好地让策略网络理解未来的、潜在 的Reward,策略网络不只是使用当前的Reward作为label,而是使用Discounted Future

Reward,即把所有未来奖励依次乘以衰减系数y。这里的衰减系数一般是一个略小于但接 近1的数，防止没有损耗地积累导致Reward目标发散，同时也代表了对未来奖励的不确 定性的估计。

r = r1+yr2 + y2r3 H-----H yn_1rn

我们使用被称为Policy Gradients的方法来训练策略网络。Policy Gradients指的是模 型通过学习Action在Environment中获得的反馈，使用梯度更新模型参数的过程。在训练 过程中，模型会接触到好Action及它们带来的高期望价值，和差Action及它们带来的低 期望价值，因此通过对这些样本的学习，我们的模型会逐渐增加选择好Action的概率， 并降低选择坏Action的概率，这样就逐渐完成了我们对策略的学习。和Q-Leaming或估 值网络不同，策略网络学习的不是某个Action对应的期望价值Q,而是直接学习在当前 环境应该釆取的策略，比如选择每个Action的概率（如果是有限个可选Action,好的Action 应该对应较大概率，反之亦然），或者输出某个Action的具体数值（如果Action不是离散 值，而是连续值）。因此策略网络是一种End-to-End （端对端）的方法，可以直接产生最 终的策略。

Policy Based的方法相比于Value-Based,有更好的收敛性（通常可以保证收敛到局 部最优，且不会发散），同时对高维或者连续值的Action非常高效（训练和输出结果都更 高效），同时能学习出带有随机性的策略。例如，在石头剪刀布的游戏中，任何有规律的 策略都会被别人学习到并且被针对，因此完全随机的策略反而可以立于不败之地（起码不 会输给别的策略）。在这种情况下，可以利用策略网络学到随机出剪刀、石头、布的策略 （三个Action的概率相等）。

我们需要使用Gym63辅助我们进行策略网络的训练。Gym是OpenAI推出的开源的 强化学习的环境生成工具。OpenAI是Tesla和Space X的老板马斯克发起的非营利性的 人工智能研究机构。其主要任务是研究安全、开放的人工智能技术，并且确保人工智能技 术可以被广泛地、公平地普及，并服务社会。Gym是OpenAI贡献出来的非常重要的开源 项目，它的主要作用是为研究者和开发者提供一个方便的强化学习任务环境，例如文字游 戏、棋类游戏、视频图像游戏等，并且让用户可以和其他人的强化学习算法进行效率、性 能上的比较。

对于强化学习的研究，之前主要受制于两个因素。其一是缺乏高质量的Benchmark, 对于图像识别、监督学习等问题，我们有ImageNet这样的经过标注的超大规模数据集， 可以让各种算法在上面进行测试。在强化学习中同样需要大量的、丰富的任务环境，而目 前任务环境不仅稀缺，而且设置一个环境的过程也非常烦琐；其二是我们没有一个通用的 环境标准，强化学习的相关论文很难进行横向比较，不同任务使用的环境定义、reward 的函数、可用的Action都会有区别，而且不同任务的难度可能差异非常大，比如围棋就 比国际象祺难很多。Gym则非常好地解决了这两个问题，提供了大量的标准化的环境， 可以用来公平地横向对比强化学习模型的性能。Gym的用户可以上传模型效果和训练日 志到OpenAI Gym Service的接口，随后可以参与某个任务的排名，和其他研究者比较模 型的效果，并分享算法的思路给其他研究者。

OpenAI Gym对用户开发模型的方式没有任何限制，它跟其他机器学习库，例如 TensorFlow和Theano,都完全兼容。用户可以使用Python语言和任何Python的Library 编写强化学习模型的Agent,比如可以创建一些简单的经验规则，或者使用State-Action 一一对应的策略表，当然也可以使用深度神经网络模型来做训练模型。

在Gym中，有两个核心的概念，一个是Environment,指我们的任务或者问题，另一 个就是Agent,即我们编写的策略或算法。Agent会将执行的Action传给Environment, Environment接受某个Action后，再将结果Observation （即环境状态）和Reward返回给 Agent。Gym中提供了完整的Environment的接口，而Agent则是完全由用户编写。目前， Gym 一共包含了几个大类的环境，分别是Algorithmic （算法）、Atari游戏（使用了 Arcade Learning Environment）、Board Games （祺牌类游戏，其中围棋包含了 9x9和19x19两种 规模，目前使用的对抗程序为Pachi ）、Box2D （二维的物理引擎）、Classic Control （经典 的控制类问题）、MuJoCo （另一个高效的物理引擎，可以实现非常细节的物理模拟，包括 碰撞，可以用来控制2D或者3D的机器人执行一些任务操作）,以及Toy Text（文本类型） 的任务。其中某些任务环境需要额外安装一些依赖库或者程序，我们可以执行foil install 来安装全部环境的依赖程序。

Gym中环境的接口是Env类，其中有几个重要的方法。使用env=gym.make（'Copy-vO'） 创建某个任务的环境；使用env.reset（）初始化环境，并返回初始的observation,即state； 使用 env.step（action）在当前状态下执行一步 Action,并返回 observation、reward、done （完 成标记）、info （调试信息，但一般不应让Agent使用该信息）；使用env.render（）方法可以 渲染出一巾贞的任务图像，很多任务的observation就是一桢图像，此时Agent直接从图像 像素中学习信息和策略。

下面我们就以Gym中的CartPole环境作为具体例子。CartPole任务最早由论文

Neuronlike Adaptive Elements That Can Solve Difficult Learning Control Problem 提出， 是一个经典的可用强化学习来解决的控制问题。如图8-8所示，CartPole的环境中有一辆 小车，在一个一维的无阻力轨道上行动，在车上绑着一个连接不太结实的杆，这个杆会左 右摇晃。我们的环境信息observation并不是图像像素，而只是一个有4个值的数组，包 含了环境中的各种信息，比如小车位置、速度、杆的角度、速度等。我们并不需要知道每 个数值对应的具体物理含义，因为我们不是要根据这些数值自己编写逻辑控制小车，而是 设计一个策略网络让它自己从这些数值中学习到环境信息，并制定最佳策略。我们可以采 取的Action非常简单，给小车施加一个正向的力或者负向的力。我们有一个Action Space 的概念，即Action的离散数值空间，比如在CartPole里Action Space就是Discrete（2）, 即只有0或1，其他复杂一点的游戏可能有更多可以选择的值。我们并不需要知道这里的 数值会具体对应哪个Action,只要模型可以学习到采取这个Action之后将会带来的影响 就可以，因此Action都只是一个编码。CartPole的任务目标很简单，就是尽可能地保持杆 竖直不倾倒，当小车偏离中心超过2.4个单位的距离，或者杆的倾角超过15度时，我们 的任务宣告失败，并自动结束。在每坚持一步后，我们会获得+1的reward,我们只需要 坚持尽量长的时间不导致任务失败即可。任务的Reward恒定，对任何Action，只要不导 致任务结束，都可以获得+1的Reward。但是我们的模型必须有远见，要可以考虑到长远 的利益，而不只是学习到当前的Reward。

图8-8 CartPole环境中包含一个可以控制移动方向的小车和不稳的杆

当我们使用env.reset（）方法后，就可以初始化环境,并获取到环境的第一个Observation□ 此后，根据Observation预测出应该采取的Action,并使用env.step（action）在环境中执行 Action,这时会返回Observation （在CartPole中是4维的抽象的特征，在其他任务中可能 是图像像素）、reward （当前这步Action获得的即时奖励）、done （任务是否结束的标记， 在CartPole中是杆倾倒或者小车偏离中心太远，其他游戏中可能是被敌人击中。如果为 True,应该reset任务）和info （额外的诊断信息，比如标识了游戏中一些随机事件的概 率，但是不应该用来训练Agent）。这样我们就进入Action-Observation的循环，执行Action, 获得Observation,再执行Action,如此往复直到任务结束，并期望在结束时获得尽可倉旨 高的奖励。我们可执行的Action在CartPole中是离散的数值空间，即有限的几种可能， 在别的任务中可能是连续的数值，例如在赛车游戏任务中，我们执行的动作是朝某个方向 移动，这样我们就有了 0-360度的连续数值空间可以选择。同时，我们的环境名称后面 都带有版本号，比如V0、VI等。当环境发生更新或者变化时，我们不会修改之前的环境， 而是创建新的版本，这样可以让Agent的性能被公平的比较。同时，我们可以调用 env.monitor方法，对模型的训练过程进行监控和记录，这样之后我们就可以方便地使用 gym.upload将训练日志上传到gym service进行展示，并与他人的算法进行比较。一般来 说,对比较简单的问题，我们的评测标准是需要多少步训练就可以稳定地达到理想的分数, 并希望需要的训练步数越少越好;对于比较复杂的问题,我们并不知道理想的分数是多少, 因此一般是希望获得的分数越高越好。用户可以上传算法到gym并让同行审议，其中如 果提出非常有效的新算法、新技巧，并且能被其他研究者复现，那对相关领域的研究会有 很大价值。

下面就使用TensorFlow创建一个基于策略网络的Agent来解决CartPole问题。我们 先安装OpenAI Gym。本节代码主要来自DeepRL-Agents64的开源实现。 pip install gym

接着，载入 NumPy、TensorFlow 和 gym。这里用 gym.make('CartPole-vO')创建 CartPole 问题的环境envo import numpy as np

import tensorflow as tf    .

import gym

env = gym.make('CartPole-v0')

先测试在CartPole环境中使用随机Action的表现，作为接下来对比的baseline。首先， 我们使用env.reset()初始化环境，然后进行10次随机试验，这里调用env.render()将CartPole 问题的图像渲染出来。使用np.random.randint(0,2)产生随机的Action,然后用env.step() 执行随机的Action,并获取返回的observation、reward和done。如果done标记为True, 则代表这次试验结束，即倾角超过15度或者偏离中心过远导致任务失败。在一次试验结 束后，我们展示这次试验累计的奖励reward_sum并重启环境。

env.reset()

random_episodes =0

reward_sum = 0

while random_episodes <10:

env.render()

observation，reward, done，_ = env.step(np.random.randint(0j2)) reward_sum += reward if done:

random_episodes += 1

print("Reward for this episode wasreward一sum) reward_sum = 0 env.reset()

可以看到随机策略获得的奖励总值差不多在10~40之间，均值应该在20~30,这将作 为接下来用来对比的基准。我们将任务完成的目标设定为拿到200的Reward,并希望通 过尽量少次数的试验来完成这个目标。

Reward for this episode was: 12.0

Reward for this episode was: 17.0

Reward for this episode was: 20.0

Reward for this episode was: 44.0

Reward for this episode was: 28.0

Reward for this episode was: 19.0

Reward for this episode was: 13.0

Reward for this episode was: 30.0

Reward for this episode was: 20.0

Reward for this episode was: 26.0

我们的策略网络使用简单的带有一个隐含层的MLP。先设置网络的各个超参数，这 里隐含节点数H设为50, batch_size设为25,学习速率leaming_rate为0.1,环境信息 observation 的维度 D 为 4，gamma 即 Reward 的 discount 比例设为 0.99。在估算 Action 的 期望价值（即估算样本的学习目标）时会考虑Delayed Reward,会将某个Action之后获 得的所有Reward做discount并累加起来，这样可以让模型学习到未来可能出现的潜在 Reward。注意，一般discount比例要小于1，防止Reward被无损耗地不断累加导致发散， 这样也可以区分当前Reward和未来Reward的价值（当前Action直接带来的Reward不 需要discount,而未来的Reward因存在不确定性所以需要discount ）。

H = 50

batch_size = 25

learning_rate = le-1

D = 4

gamma = 0.99

下面定义策略网络的具体结构。这个网络将接受observations作为输入信息，最后输 出一个概率值用以选择Action (我们只有两个Action,向左施加力或者向右施加力，因此 可以通过一个概率值决定)。我们创建输入信息observations的placeholder,其维度为D。 然后使用tf.contrib.layers.xavier_initializer初始化算法创建隐含层的权重W1，其维度为[D, H]。接着用tf.matmul将环境信息observation乘上W1再使用ReLU激活函数处理得到隐 含层输出layer 1,这里注意我们并不需要加偏置。同样用xavier_initializer算法创建最后 Sigmoid输出层的权重W2,将隐含层输出layerl乘以W2后，使用Sigmoid激活函数处 理得到最后的输出概率。

observations = tf.placeholder(tf.float32j [None_,D] name="input _x")

W1 = tf.get_variable("Wlnshape=[Dj H]j

initializer=tf.contrib.layers.xavier_initializer()) layerl = tf.nn.relu(tf.matmul(observationsjWl))

W2 = tf.get_variable(”W2", shape=[H， 1])

initializep=tf.contrib.layers.xavier_initializer()) score = tf.matmul(layerlW2) probability = tf.nn.sigmoid(score)

这里模型的优化器使用Adam算法。我们分别设置两层神经网络参数的梯度的

placeholder-WIGrad 和 W2Grad,并使用 adam.apply_gradients 定义我们更新模型参数

的操作updateGrads。之后计算参数的梯度，当积累到一定样本量的梯度，就传入WIGrad 和W2Grad，并执行updateGrads更新模型参数。这里注意，深度强化学习的训练和其他 神经网络一样，也使用batch training的方式。我们不逐个样本地更新参数，而是累计一 个batch_siZe的样本的梯度再更新参数，防止单一样本随机扰动的噪声对模型带来不良影 响。

adam = tf.train.AdamOptimizer(learning_rate=learning_rate)

WIGrad = tf. placeholder (tf. f loat32? name=" batch_gradl,*)

W2Grad = tf.placeholder(tf.float32,name="batch_grad2")

batchGrad = [WlGrad)W2Grad]

updateGrads = adam.apply_gradients(zip(batchGradjtvars))

下面定义函数discount_rewards,用来估算每一个Action对应的潜在价值discountero 因为CartPole问题中每次获得的Reward都和前面的Action有关，属于delayed reward。 因此需要比较精准地衡量每一个Action实际带来的价值时，不能只看当前这一步的 Reward,而要考虑后面的Delayed Reward。那些能让Pole长时间保持在空中竖直的Action, 应该拥有较大的期望价值，而那些最终导致Pole倾倒的Action,则应该拥有较小的期望 价值。我们判断越靠后的Action的期望价值越小，因为它们更可能是导致Pole倾倒的原 因，并且判断越靠前的Action的期望价值越大，因为它们长时间保持了 Pole的竖直，和 倾倒的关系没有那么大。我们倒推整个过程，从最后一个Action开始计算所有Action应 该对应的期望价值。输入数据r为每一个Action实际获得的Reward,在CartPole问题中， 除了最后结束时的Action为0,其余均为1。下面介绍具体的计算方法，我们定义每个 Action除直接获得的Reward外的潜在价值为running_add, running_add是从后向前累计 的，并且需要经过discount衰减。而每一个Action的潜在价值，即为后一个Action的潜 在价值乘以衰减系数gamma再加上它直接获得的reward,即running_add*gamma+r[t]□ 这样从最后一个Action开始不断向前累计计算，即可得到全部Action的潜在价值。这种 对潜在价值的估算方法符合我们的期望，越靠前的Action潜在价值越大。

def discount_rewards(r):

discounted_r = np.zeros_like(r) running_add = 0

for t in reversed(range(r.size)):

running_add = running_add * gamma + r[t] discounted_r[t] = running_add

return discounted一r

我们定义人工设置的虚拟label (下文会讲解其生成原理，其取值为0或1 )的

placeholder-input_y,以及每个 Action 的潜在价值的 placeholder-advangtages。这里

loglik的定义略显复杂，我们来看一下loglik到底代表什么。Action取值为1的概率为 probability (即策略网络输出的概率)，Action取值为0的概率为1-probability, label取值 与 Action 相反，即 label: 1-Action。当 Action 为 1 时，label 为 0，此时 loglik=tf.log(probability), Action 取值为 1 的概率的对数;当 Action 为 0 时，label 为 1，此时 loglik=tf.log(l-probability), 即Action取值为0的概率的对数。所以，loglik其实就是当前Action对应的概率的对数， 我们将loglik与潜在价值advantages相乘，并取负数作为损失，即优化目标。我们使用优 化器优化时，会让能获得较多advantages的Action的概率变大，并让能获得较少advantages

的Action的概率变小，这样能让损失变小。通过不断的训练，我们便能持续加大能获得 较多advantages的Action的概率，即学习到一个能获得更多潜在价值的策略。最后，使 用tf.trainable_variables()获取策略网络中全部可训练的参数tvars,并使用tf.gradients求角军 模型参数关于loss的梯度。

input_y = tf .placeholder(tf.floatBZj [Nonejl] j name=',input_y,')

advantages = tf .placeholder(tf .floatSZjnameyr'eward—signal")

loglik = tf.log(input_y*(input_y - probability) + \

(1 - input_y)*(input_y + probability))

loss = -tf.reduce_mean(loglik * advantages) tvars = tf,trainable_variables()

newGrads = tf.gradients(losstvars)

在正式进入训练过程前，我们先定义一些参数，xs为环境信息observation的列表， ys为我们定义的label的列表，drs为我们记录的每一个Action的Reward。我们定义累计 的 Reward 为 reward_sum,总试验次数 total_episodes 为 10000,直到达到获取 200 的 Reward 才停止训练。

xs.ys.drs =[],[],[]

reward_sum = 0

episode_number = 1

total_episodes = 10000

我们创建默认的Session,初始化全部参数，并在一开始将render的标志关闭。因为 render会带来比较大的延迟，所以一开始不太成熟的模型还没必要去观察。先初始化 CartPole的环境并获得初始状态。然后使用sess.run执行tvars获取所有模型参数，•用来创 建储存参数梯度的缓冲器gradBuffer,并把gardBuffer全部初始化为零。接下来的每次试 验中，我们将收集参数的梯度存储到gradBuffer中，直到完成了一个batch_size的试验， 再将汇总的梯度更新到模型参数。

with tf.Session() as sess: rendering = False

init = tf.global_variables_initializer() sess.run(init)

—「TensorFlow 实战

observation = env.reset()

gradBuffer = sess.run(tvars)

for iXjgrad in enumerate(gradBuffer):

gradBufferfix] = grad * 0

下面进入试验的循环，最大循环次数即为total_episodes。当某个batch的平均Reward 达到100以上时，即Agent表现良好时，调用env.render()对试验环境进彳亍展示。先使用 tf.reshape将observation变形为策略网络输入的格式，然后传入网络中，使用sess .run执 行probability获得网络输出的概率tfi)rOb，即Action取值为1的概率。接下来我们在(0， 1)间随机抽样，若随机值小于曲rob，则令Action取值为1,否则令Action取值为0， 即代表Action取值为1的概率为tf^rob。

while episode一number <= total_episodes:

if reward_sum/batch_size > 100 or rendering == True : env.render() rendering = True

x = np.reshape(observationj[l^D])

tfprob = sess.run(probability^feed_dict={observations: x}) action = 1 if np.random.uniform() < tfprob else 0

然后将输入的环境信息observation添加到列表xs中。这里我们制造虚拟的label-

y，它取值与Action相反，即y=l-action,并将其添加到列表ys中。然后使用env.step执 行一次 Action,获取 observation、reward、done 和 info,并将 reward 累加到 reward_sum, 同时将reward添加到列表drs中。

xs.append(x) y = 1 - action ys.append(y) observationj rewardj done, info = env.step(action) reward_sum += reward

drs.append(reward)

当done为True,即一次试验结束时，将episode_numer加1。同时使用np.vstack将 几个列表xs、ys、drs中的元素级向堆叠起来，得到epx、epy和epr,并将xs、ys、drs 清空以备下次试验使用。这里注意，epx、epy、drs即为一次试验中获得的所有observation、 label、reward的列表。我们使用前面定义好的discount_rewards函数计算每一步Action的 潜在价值，并进行标准化(减去均值再除以标准差)，得到一个零均值标准差为1的分布。 这么做是因为discount_reward会参与到模型损失的计算，而分布稳定的discount_rewad 有利于训练的稳定。

if done:

episode_number += 1 epx = np.vstack(xs) epy = np.vstack(ys) epr = np.vstack(drs) xs.ys.drs =[]“],[]

discounted_epr = discount_rewards(epr) discounted_epr -= np.mean(discounted_epr) discounted_epr /= np.std(discounted_epr)

我们将epx、epy和discounted_epr输入神经网络，并使用操作newGrads求解梯度。 再将获得的梯度累加到gradBuffer中去。

tGrad = sess.run(newGradSjfeed_dict={observations: epx^

input_y: epy, advantages: discounted_epr})    .

for iXjgrad in enumerate(tGrad): gradBuffer[ix] += grad

当进行试验的次数达到batch_size的整倍数时,gradBuffer中就累计了足够多的梯度， 因此使用updateGrads操作将gradBuffer中的梯度更新到策略网络的模型参数中，并清空 gradBuffer,为计算下一个batch的梯度做准备。这里注意，我们是使用一个batch的梯度 更新参数，但是每一个梯度是使用一次试验中全部样本(一个Action对应一个样本)计 算出来的，因此一个batch中的样本数实际上是25 ( batch_size )次试验的样本数之和。

TensorFlow 实战

同时，我们展示当前的试验次数episode_number，和batch内每次试验平均获得的reward。 当我们batch内每次试验的平均reward大于200时，我们的策略网络就成功完成了任务， 并将终止循环。如果没有达到目标，则清空reward_sum,重新累计下一个batch的总reward。 同时，在每次试验结束后，将任务环境uw重置，方便下一次试验。

if episode一number % batch_size == 0:

sess.run(updateGradsJfeed_dict={WlGrad: gradBuffer[0],

W2Grad:gradBuffer[l]}) for iXjgrad in enumerate(gradBuffer):

gradBuffer[ix] = grad * 0

print('Average reward for episode %d :    ' % \

(episode_ numberjreward_sum/batch_size))

if reward_sum/batch_size > 200:

print("Task solved in1、episode_numberj 'episodes!1) break

reward_sum = 0

observation = env.reset()

下面是我们模型的训练日志，可以看到策略网络在仅经历了 200次试验，即8个batch 的训练和参数更新后，就实现了我们的目标，达到了 batch内平均230的reward,顺利完 成预设的目标。有兴趣的读者可以尝试修改策略网络的结构、隐含节点数、batch_size, 学习速率等参数来尝试优化策略网络的训练，加快其学习到好策略的速度。

Average reward for episode 25 : 19.200000.

Average reward for episode 50 : 30.680000.

Average reward for episode 75 : 41.360000.

Average reward    for    episode    100    :    52.160000.

Average reward    for    episode    125    :    70.680000.

Average reward    for    episode    150    :    84.520000.

Average reward    for    episode    175    :    153.320000.

Average reward for episode 200 : 230.400000. Task solved in 200 episodes!

##### 8.3 TensorFlow实现估值网络

在强化学习中，除了 Policy Based直接选择Action的方法，还有一种学习Action对 应的期望价值(Expected Utility )的方法，称为Q-Leaming65。Q-Leaming最早于1989年 由Watkins提出，其收敛性于1992年由Watkins和Dayan共同证明。Q-Leaming学习中 的期望价值指从当前的这一步到所有后续步骤，总共可以期望获取的最大价值(即Q值， 也可称为Value )。有了这个ActiorwQ的函数，我们的最佳策略就是在每一个state下， 选择Q值最高的Action。和Policy Based方法一样，Q-Leaming不依赖环境模型。在有 限马尔科夫决策过程(Markov Decision Process)中，Q-Leaming被证明最终可以找到最 优的策略。

Q-Leaming的目标是求解函数即根据当前环境状态，估算Action的期望价 值。Q-Leaming训练模型的基本思路也非常简单，它以(状态、行为、奖励、下一个状态) 构成的元组(st,a£,rt+1,st+1)为样本进行训练，其中■?,为当前的状态，a为当前状态下执行 的Action, rm为在执行Action后获得的奖励，s,+1为下一个状态。其中特征是(s,，a,),而 学习目标(即期望价值)则是rt+1 + y ■ maxa Q(st+1, a),这个学习目标即是当前Action 获得的Reward加上下一步可获得的最大期望价值。学习目标中包含了 Q-Leaming的函数 本身，所以这其中使用了递归求解的思想。下一步可获得的最大期望价值被乘以一个y, 即衰减系数discount factor,这个参数决定了未来奖励在学习中的重要性。如果discount factor为0,那么模型将学习不到任何未来奖励的信息，将会变得短视，只关注当前的利 益；如果discount factor大于等于1，那算法很可能无法收敛，期望价值将被不断累加并 且没有衰减(即discount)，这样期望价值很可能会发散。因此，discount factor —般会被 设为一个比1稍小的值。我们可以把整个Q-Leaming学习的过程写成下面这个式子：

Qnewfe, at) (1 - a) • Qoid(st, at) + a • (rt+1 + y - max Q(st+1, a))

简单描述这个公式是，将旧的Q-Leaming函数QcW(st，at),向着学习目标(当前获得 的Reward加上下一步可获得的最大期望价值)按一个较小的学习速率《学习，得到新的 Q-Leaming函数Qnew(st，at)。其中学习速率决定了我们使用新获取的样本信息覆盖之前掌 握到的信息的比率，通常设为一个li较小的值，可以保证学习过程的稳定，同时确保最后

TensorFlow 头战

的收敛性。同时/ Q-Leaming需要一个初始值(?(,，而比较高的初始值可以鼓励模型多进行 探索。

我们用来学习Q-Leammg的模型可以是神经网络，这样得到的模型即是估值网络。 如果其中的神经网络比较深，那就是DQN。DQN这一说法，是由Google DeepMind发 表于 Nature 的论文    control through deep reinforcement Zearn/ng 提出的，在

这篇论文中DeepMind使用DQN创建了达到人类专家水平的可以玩Atari 2600系列游戏 的Agent。相比于早期Q-Leaming使用的简单模型，DeepMind的DQN有了很多方面的 改进。下面我们将逐一介招目前state of the art的DQN中的一些Trick。

第1个Trick,我们需要在DQN中引入卷积层。我们不再是输入一些数值类的特征 让模型学习，而是直接让模型通过Atari这类游戏的视频图像了解环境信息并学习策略。 这样就必须让DQN能理解它所接收到的图像，即具有一定的图像识别能力，因此我们就 需要用到前几章提到的卷积神经网络。卷积神经网络的具体原理前面几章讲解过，它利用 可提取空间结构信息的卷积层来抽取特征。卷积层可以提取图像中重要目标的特征并传给 后面的层来做分类或者回归，比如第6章中的VGG Net和Inception Net。但DQN不同， 它使用卷积层不是用来对图像做分类，而是进行强化学习的训练，其目标是根据环境图像 输出决策。通常在设计DQN时，如果输入是图像，那么最前面几层一般都会设置成卷积 层，如图8-9所示。本节将要实现的DQN的前4层也都是卷积层。

Convolution

Convolution



Fully connected Fully connected



图8-9 Deep Q-Network中的多层卷积结构

第2个Trick是Experience Replay。因为深度学习需要大量的样本，所以传统的 Q-Leaming的online update的方法（逐一对新样本学习的方式）可能不太适合DQN。因 此，我们需要增大样本量，并且像VGGNet或Inception Net那样进行多个epoch的训练，

对图像进行反复利用。我们引入一种被称为Experience Replay的技术，它的主要思想就 是储存Agent的Experience （即样本），并且每次训练时随机抽取一部分样本供给网络学 习。这样我们能比较稳定地完成学习任务，避免只短视地学习到最新接触到的样本，而是 综合地、反复地利用过往的大量样本进行学习。我们会创建一个用来储存Experience的缓 存buffer,它里面可以储存一定量的比较新的样本。当容量满了以后，会用新样本替换最 旧的样本，这可以保证大部分样本有相近的概率被抽到，如果不替换旧的，那么从一开始 就获得的旧样本，在整个训练过程中被抽到的概率会比新样本高很多。每次需要训练样本 时，就直接从buffer中随机抽取一定量的样本给DQN训练，这样可以保持对样本较高的 利用率，同时可以让模型学习到比较新的一批样本。

第3个Trick,我们可以再使用第二个DQN网络来辅助训练，这个辅助网络一般称 为target DQN ,它的意义是辅助我们计算目标Q值，即提供学习目标公式里的 maxaQ（st+1,a）o我们之所以要拆分为两个网络，一个用来制造学习目标，一个用来进行 实际训练，原因很简单，是为了让Q-Leaming训练的目标保持平稳。强化学习及Q-Leaming 不像普通的监督学习，它的学习目标每次都是变化的，因为学习目标的一部分是模型本身 输出的。每次更新模型参数都会导致我们的学习目标发生变化，如果更新很频繁、幅度很 大，我们的训练过程就会非常不稳定并且失控。这样DQN的训练就会陷入目标Q值与预 测Q值的反馈循环中（陷入震荡发散，难以收敛）。为了降低这种影响，需要让目标Q值 尽量平稳，因此需要一个比较稳定的target DQN辅助网络计算目标Q值。我们让target DQN进行低频率或者缓慢的学习，这样它输出的目标Q值的波动也会比较小，可以减小 对训练过程的影响。

第4个Trick,如果在分拆出target DQN的方法上更进一步，那就是Double DQN。

DeepMind 的研究者在论文 Dee/? Reinforcement Learning with Double    中发现，

传统的DQN通常会高估Action的Q值。如果这种高估不是均匀的，可能会导致本来次

优的某个Action总是被高估而超过了最优的Action,那将给训练和选择Action带来很大

的麻烦，我们可能永远都发现了不了最优的Action。因此，在DeepMind这篇论文中提出

了可以在DQN中也使用Double Q-Leaming的方法。我们之前是让target DQN完全负责

生成目标Q值，即先产生QOt+1,a）,再通过max选择最大的Q值。Double DQN则是修 a

—「TensorFlow 实战

改了第二步，不是直接选择target DQN上最大的Q值，而是在我们的主DQN上通过其 最大Q值选择Action,再去获取这个Action在target DQN上的Q值。这样我们的主网 络负责选择Action,而这个被选定的Action的Q值则由target DQN生成。被选择的Q 值，不一定总是最大的Q值，这样就避免了被高估的次优Action总是超过最优的Action, 导致我们发现不了真正最好的Action。我们的学习目标因此可以写成下面的式子。

Target = rt+1 + y - Qtarget （st+1, argmaxa^QmainCst+1, a）））

第5个Trick是Dueling DQN,也是DQN的一个重大改进，在Google的论文Dueling Network Architectures for Deep Reinforcement Learning 中被首次提出。Dueling DQN 将 Q值的函数（10£,叫）拆分为两部分，一部分是静态的环境状态本身具有的价值，称为 Value ；另一部分是动态的通过选择某个Action额外带来的价值24（at）,称为Advantage□ 我们的Q值将由这两部分组合而成，可以写成下面这个公式。

Q（st,at） = V（st） + X（at）

Dueling的目标就是让网络可以分别计算环境本身的Value和选择Action带来的 Advantage,这里的Advantage是某个Action与其他Action的比较，因此我们将它设计为 零均值的。如图8-10所示，上面那部分是传统的DQN网络，下面的就是Dueling DQN 了，在网络的最后部分，不再是直接输出Action数量（假定为《 ）的Q值，而是输出一 个Value值及《个Advantage值，然后将V值分别加到每一个Advanatge值上，得到最后 的结果。这样做的目的是让DQN的学习目标更明确，如果当前的期望价值主要是由环境 状态决定的，那么Value值很大’而所有Advantage的波动都不大；如果期望价值主要由 Action决定，那么Value值很小，而Advantage波动会很大，分解这两个部分会让我们的 学习目标更稳定、更精确，让DQN对环境状态的估计能力更强。

下面我们就实现带有前面几个Trick的DQN。使用的任务环境是叫作GridWorld的导 航类游戏，如图8-11所示。GridWorld中包含一个hero （实际为蓝色，这里以白色显示） 4个goal （实际为绿色，这里以浅灰表示）和2个fce（实际为红色，这里以深灰色表示）。 我们的目标就是控制hero移动，每次向上、下、左、右等方向移动一步，尽可能多地触 碰goal （奖励值为1 ），同时避开fire （奖励值为-1 ）。游戏的目标是在限定步数内拿到最 多的分数。我们的Agent将直接通过GridWorld的图像学习控制hero移动的最优策略。

图8-11 GridWorld游戏环境示例

下面开始创建GridWorld任务的环境。首先是载入各种依赖的库，这次需要载入的库 相对较多，其中itertools可以方便地进行迭代操作，scipy.misc和matplotlib.pyplot可以绘 图。同时因为训练时间较长，我们也载入OS用来定期储存模型文件。本节代码主要来自 DeepRL-Agents 的开源实现 66。

import numpy as np

import random

import itertools

import scipy.misc

import matplotlib.pyplot as pit import tensorflow as tf

「TensorFlow 实战

import os

%matplotlib inline

先是创建环境内物体对象的class，环境物体包括以下几个属性：coordinates ( x，y坐 标)、size (尺寸)、intensity (亮度值)、channel ( RGB 颜色通道)、reward (奖励值)，以 及name (名称)。

class gameOb():

def _init_(self, coordinatessize^intensity^ channel, rewardname): self.x = coordinates[0] self.y = coordinates[l] self.size = size self.intensity = intensity self.channel = channel

self.reward = reward self.name = name

然后创建GridWorld环境的class,其初始化方法只需要传入一个参数，即环境的size。 我们将环境的长和宽都设为输入的size,同时将环境的Action Space设为4，并初始化环 境的物体对象的列表。调用self.reset()方法重置整个环境，得到初始的observation (即 GridWorld的图像)，并使用plt.imshow将observation展示出来。

class gameEnv():

def_init_(self, size):

self.sizeX = size self.sizeY = size

self-actions = 4 self.objects =[] a = self.reset()

pit. imshow(a_, interpolation? nearest")

接下来定义环境的reset方法。我们将创建所有GridWorld中的物体，包括1个hero (用户控制的对象)、4个goal ( reward为1 )、2个fire ( reward为-1 )，并把他们添加到物 体对象的列表self.objects。创建物体的位置时使用self.newPosition()，该方法会随机选择 一个没有被占用的新位置。所有物体的size和intensity均为1,其中hero的channel为2

(蓝色)，goal的channel为1(绿色),fire的channel为0(红色)。最后我们使用self.renderEnv() 将GridWorld的图像绘制出来，即state。

def reset(self):

self.objects =[]

hero = gameOb(self .newPositionOjljljZjNone^ 'hero*) self.objects.append(hero)

goal = gameOb(self,newPosition()'goal') self.objects.append(goal)

hole = gameOb(self.newPosition(),1)1)0)-1)'fire') self.objects.append(hole)

goal2 = gameOb(self.newPosition()'goal') self.objects.append(goal2)

hole2 = gameOb(self.newPosition()-lj 'fire*) self.objects.append(hole2)

goal3 = gameOb(self .newPositionOjljljljlj 'goal*) self.objects.append(goal3)

goal4 = gameOb(self.newPosition()'goal*) self.objects.append(goal4) state = self.renderEnv() self.state = state

return state

这里我们实现移动英雄角色的方法，我们传入的值为0、1、2、3这四个数字，分别 代表上、下、左、右。函数根据输入来操作英雄的移动，但如果移动该方向会导致英雄出 界，则不会进行任何移动。，

def moveChar(selfjdirection): hero = self.objects[0] heroX = hero.x heroY = hero.y

if direction == 0 and hero.y >= 1: hero.y -= 1

if direction == 1 and hero.y <= self.sizeY-2: hero.y += 1

TensorFlow 实战

if direction == 2 and hero.x >= 1:

hero.x -= 1

if direction == 3 and hero.x <= self.sizeX-2: hero.x += 1

self.objects[0] = hero

然后定义刚才提到的newPosition方法，它可以选择一个跟现有物体不)中突的位置。 itertools.product方法可以得到几个变量的所有组合,使用这个方法创建环境Size允许的所 有位置的集合points,并获取目前所有物体位置的集合currentPositions,再从points中去 掉currentPositions,剩下的就是可用的位置。最后使用np.random.choice随机抽取一个可 用位置并返回。

def newPosition(self):

iterables = [ range(self.sizeX), range(self.sizeY)] points =[]

for t in itertools.product(*iterables): points.append⑴

currentPositions =[]

for objectA in self.objects:

if (objectA.x4objectA.y) not in currentPositions: currentPositions.append((objectA.xobjectA.y))

for pos in currentPositions: points.remove(pos)

location = np.random.choice(range(len(points))replace=False) return points[location]

下面定义checkGoal函数，用来检查hero是否触碰了 goal或者fire。我们先从objects 中获耳hero:并将其他物体对象放到others列表中。然后遍历others列表，如果有物体和 坐标与hero完全一致，那么可判定为触碰。接下来根据触碰到的是什么物体，我们销毁 该物体，并调用self.newPosition()方法在随机位置重新生成一个该物体，并返回这个物体 的 reward 值(goal 为 1，fire 为-1 )o

def checkGoal(self): others =[]

for obj in self.objects:

if obj.name == 'hero*: hero = obj

else:

others.append(obj) for other in others:

if hero.x == other.x and hero.y == other.y: self.objects.remove(other) if other.reward == 1:

self, objects, append (gameOb(self. newPosition () _, 1 1_, 1』1 'goal1))

else:

self.objects.append(gameOb(self.newPosition()ljljOj-lj 'fire*))

return other.rewardj False return 0.0^False

先创建一个长宽为size+2,颜色通道数为3的图片，初始值全部为1,代表全为白色。 然后把最外边一圈内部的像素的颜色值全部赋为0,代表黑色。遍历物体对象的列表 self.objects,并设置这些物体的亮度值。同时，使用scipy.misc.imresize将图像从原始大小 resize为84x84x3的尺寸，即一个正常的游戏图像尺寸。

def renderEnv(self):

a = np.ones([self.sizeY+2^ self.sizeX+2^ 3])

：] = 0

hero = None

for item in self .objects:..

a [ item, y+1: item, y+item. size+1^ item, x+1: item, x+item. size+l_, item.channel] = item.intensity

b = scipy.misc.imresize(a[:4:,0],[84^84^1],interp='nearest') c = scipy.misc.imresize(a[:,:>1],[84^84,l]Jinterp='nearest') d = scipy.misc.imresize(a[:2]^[84^84,l]>interp=,nearest') a = np.stack([b4c3d]?axis=2)

return a

—TensorFlow 实战

最后定义在GridWorld环境中执彳?一步Action的方法。输入的参数为Action,先使 用self.moveChar(action)移动hero的位置，再使用self.checkGoal()检测hero是否有触碰物 体，并得到reward和done标记。然后使用self.renderEnv获取环境的图像state,最后返 回 state、reward 和1 done。

def step(self,action): self.moveChar(action) rewarddone = self.checkGoal() state = self.renderEnv() return state, rewarddone

接下来调用刚才写好的gameEnv类的初始化方法，并设置size为5,创建一个5x5 大小的GridWorld环境，每一次创建的GridWorld环境都是随机生成的。读者可以尝试使 用不同尺寸的GridWorld,小尺寸的环境会相对容易学习，大尺寸的则较难，训练时间也 更长。

env = gameEnv(size=5)

下面便是我们创建好的5x5的GridWorld环境图像，如图8-12所示，因为黑白印刷 的原因，其中白色代表hero,浅灰色代表goal (reward为1 )，深灰色代表fire ( reward 为-1 )。我们的任务目标是在指定步数(每一步可以选择向上、下、左、右移动)内获得 尽可能多的分数，我们每触碰一个物体，将会销毁该物体并在其他位置重建。因此，Agent 的目标就是避开fire，同时多触碰goal。我们还需要规划最优路线，在有限步数内收集尽 可能多的goal。当然，这些策略都是DQN需要自己通过试验来学习的。

图8-12 5x5的GridWorld环境，白色为hero,浅灰色为goal,深灰色为fire

下面我们就开始设计DQN (Deep Q-Network)网络，相对上一节的简单例子，本节 的网络更复杂一些，并且使用了卷积层，可以直接从环境的原始像素中学习策略。输入 scalarlnput是被扁平化的长为84x84x3=21168的向量，需要先将其恢复成[-1，84, 84, 3] 尺寸的图片Imageln。我们使用tf.contrib.layers.convolution2d创建第1个卷积层，卷积梭 尺寸为8x8,步长为4x4,输出通道数(filter的数量)为32, padding模式为VALID (以 下所有层padding模式均为VALID ),bias初始化器为空。因为使用了 4x4的步长和VALID 模式的padding,所以第一层卷积的输出维度为20x20x32。第2个卷积层尺寸为4x4，步 长为2x2,输出通道数为64,这一层的输出维度为9x9x64。第3层卷积层尺寸为3x3， 步长为1x1，输出通道数为64，这一层输出维度为7x7x64。第4层卷积尺寸为7x7,步 长为1x1,输出通道数一下涨到了 512,这一层的空间尺寸只允许在一个位置进行卷积， 因此最后的输出维度变为1x1x512。

class Qnetwork():

def _init_(self h_size):

self.scalarlnput = tf.placeholder(shape=[Nonej21168] dtype=tf.float32)

self.imageln = tf.reshape(self.scalarlnput,shape=[-l>84^84^3]) self.convl = tf.contrib.layers.convolution2d(

inputs=self. imagelrij num_outputs=32j kernel_size=[8>8]>stride=[4>4], padding=*VALID', biases_initializer=None)

self.conv2 = tf.contrib.layers.convolution2d(

inputs=self.convl,num_outputs=64j kernel_size=[4>4]/stride=[2>2] padding=,VALID,biases_initializer=None)

self.conv3 = tf.contrib.layers.convolution2d(

inputs=self. conv2^ num_outputs=64j kernel_size=[3,3] stride=[l_, 1], padding=,VALID,biases_initializer=None)

self.conv4 = tf.contrib.layers.convolution2d( inputs=self.conv3,num_outputs=512J kernel_size=[7j7]stride=[ljl]? padding=*VALID', biases_initializer=None)

接下来，使用tf.splito将第4个卷积层的输出conv4平均拆分成两段，streamAC和

TensorFlow 实战

streamVC，即 Dueling DQN 中的 Advantage Function ( Action 带来的价值)和 Value Function (环境本身的价值)。这里注意tf.split函数的第2个参数代表要拆分成几段，第3 个参数代表要拆分的是第几个维度。然后分别使用tf.contrib.layers.flatten将streamAC和 streamVC转为扁平的steamA和streamV。下面创建streamA和streamV的线性全连接层 参数AW和VW，我们直接使用tf.random_normal初始化它们的权重，再使用tf.matmul 做全连接层的矩阵乘法，得到self.Advantage和self.Value。因为Advantage是针对Action 的，因此输出数量为Action的数量，而Value则是针对环境统一的，输出数量为1。我们 的Q值则由Value和Advantage复合而成，即Value加上减去均值的Advantage。Advantage 减去均值的操作使用的是tf.subtract,均值计算使用的是tf.reduce_mean函数 (reduce_indices为1,即代表Action数量的维度)。最后输出的Action即为Q值最大的 Action,这里使用tf.argmax求出这个Actiono

self.streamAC,self.streamVC = tf.split(self.conv4j 2^3)

self.streamA = tf.contrib.layers.flatten(self.streamAC)

self.streamV = tf.contrib.layers.flatten(self.streamVC)

self.AW = tf.Variable(tf.random_normal([h_size//2jenv.actions]))

self.VW = tf.Variable(tf.random_normal([h_size//2,1]))

self.Advantage = tf.matmul(self.streamA^ self.AW)

self.Value = tf.matmul(self.streamV^ self.VW)

self.Qout = self.Value + tf.subtract(self.Advantagejtf.reduce_mean( self.Advantage^reduction_indices=l,keep_dims=True))

self.predict = tf.argmax(self.Qout^1)

我们定义Double DQN中的目标Q值targetQ的输入placeholder,以及Agent的动作 actions的输入placeholder。在计算目标Q值时，action由主DQN选择，Q值则由辅助的 target DQN生成。在计算预测Q值时，我们将scalar形式的actions转为onehot编码的形 式，然后将主DQN生成的Qout乘以actions_onehot,得到预测Q值(Qout和actions都 来自主DQN)。

self.targetQ = tf. placeholder (shape= [None] _,dtype=tf.float32) self-actions = tf .placeholder(shape=[None] _,dtype=tf .int32) self.actions_onehot = tf.one_hot(self.actions^env.actions,

dtype=tf.float32)

self.Q = tf.reduce_sum(tf.multiply(self.Qoutself.actions_onehot), reduction_indices=l)

接下来定义loss，使用tf.square和tf.reduce_mean计算targetQ和Q的均方误差，并 使用学习速率为le-4的Adam优化器优化预测Q值和目标Q值的偏差。

self.td_error = tf.square(self.targetQ - self.Q) self.loss = tf.reduce_mean(self.td_error)

self.trainer = tf.train.AdamOptimizer(learning_rate= 0.0001) self.updateModel = self.trainer.minimize(self.loss)

接下来实现前面提到的Experience Replay策略。我们定义experience_buffer的class, 其初始化需要定义buffer_SiZe即存储样本的最大容量，并创建buffer的列表。然后定义向 buffer中添加元素的方法，如果超过了 buffer的最大容量，就清空前面最早的一些样本， 并在列表末尾添加新元素。然后在定义对样本进行抽样的方法，这里直接使用 random.sample()函数随机抽取一定数量的样本。

class experience_buffer():

def _init_(selfbuffer_size = 50000):

self.buffer =[]

self.buffer_size = buffer_size

def add(self,experience):

if len(self.buffer) + len(experience) >= self.buffer_size: self.buffer[0:(len(experience)+len(self.buffer)) - \

self.buffer_size]=[] self.buffer.extend(experience)

def sample(selfjSize):

return np.reshape(np.array(random.sample(self.buffer\size))

[size.5])

下面定义将84x84x3的states扁平化为1维向量的函数processState,这样做的主要 目的是后面堆叠样本时会比较方便。

def processState(states):

TensorFlow 实践

return np. reshape(states_, [21168])

这里的updateTargetGraph函数是更新target DQN模型参数的方法(主DQN则是直 接使用DQN class中的self.updateModel方法更新模型参数)。我们的输入变量tfVars是 TensorFlow Graph中的全部参数，tau是target DQN向主DQN学习的速率。函数 updateTargetGraph会取tfVars中前一半参数，即主DQN的模型参数，再令辅助的target DQN的参数朝向主DQN的参数前进一个很小的比例(即tau，一般设为0.001 )，这样做 是让target DQN缓慢地学习主DQN。我们在训练时，目标Q值不能在几次迭代间波动 太大，否则训练会非常不稳定并且失控，陷入目标Q值和预测Q值之间的反馈循环中。 因此，需要使用稳定的目标Q值训练主网络，所以我们使用一个缓慢学习的target DQN 网络输出目标Q值，并让主网络来优化目标Q值和预测Q值间的loss,再让target DQN 跟随主DQN并缓慢学习。函数updateTargetGraph会创建更新target DQN模型参数的操 作，而函数updateTarget则直接执行这些操作o

def updateTargetGraph(tfVarSjtau): total_vars = len(tfVars) op_holder =[]

for idXjVar in enumerate(tfVars[0:total_vars//2]):

op_holder.append(tfVars[idx+total_vars//2].assign((var.value() * \

tau) + ((l-tau)*tfVars[idx+total_vars//2].value()))) return op_holder

def updateTarget(op_holderjSess): fop op in op_holder:

sess.run(op)

下面是DQN网络及其训练过程的一些参数。batch_size即每次从experience buffer 中获取多少样本，设为32;更新频率Update_freq,即每隔多少step执行一次模型参数更 穿斤，设为4; Q值的衰减系数(discount factor )y设为0.99； startE为起始的执行随机Action 的概率；endE为最终的执行随机Action的概率(在训练时，我们始终需要一些随机Action 进行探索，实际预测时则没有必要)；annelingjteps是从初始随机概率降到最终随机概率 所需要的步数；num_episodes指总共进行多少次GridWorld环境的试验；pre_train_steps 代表正式使用DQN选择Action前进行多少步随机Action的测试；max_epLength是每个 episode进行多少步Action; load_model代表是否读取之前训练的模型；path是模型储存

的路径；h_size是DQN网络最后的全连接层的隐含节点数；tau是target DQN向主DQN 学习的速率。

batch_size = 32

update_freq = 4

y = .99

startE = 1

endE = 0.1

anneling_steps = 10000.

num_episodes = 10000

pre_train_steps = 10000

max_epLength = 50

load一model = False

path = " ./dqn"

h__size = 512

tau = 0.001

我们使用前面写好的Qnetwork类初始化mainQN和辅助的targetQN,并初始化所有 模型参数。同时，使用trainables获耳又所有可训练的参数，并使用updateTargetGraph创建 更新target DQN模型参数的操作。

mainQN = Qnetwork(h_size)

targetQN = Qnetwork(h_size)

init = tf,global_variables_initializer{)

trainables = tf.trainable_variables()

targetOps = updateTargetGraph(trainables^tau)    .

我们使用前面定义的experience_buffer创建experience replay的class,设置当前随机 Action的概率e，并计算e在每一步应该衰减的值stepDrop。接着初始化储存每一个episode 的reward的列表rList,总步数为total_steps0然后创建模型训急的保存器(Saver),并检 查保存目录是否存在。

myBuffer = experience_buffer()

e = startE

TensorFlow 实战

stepDrop = (startE - endE)/anneling_steps

nList =[]

total_steps = 0

saver = tf.train.Saver()

if not os.path.exists(path): os.makedirs(path)

接下来创建默认的Session，如果load_model标志为True，那么检查模型文件路径的 checkpoint,读取并载入之前已保存的模型。接着，我们执行参数初始化的操作，并执行 更新targetQN模型参数的操作。然后创建进行GridWorld试验的循环，并创建每个episode 内部的experience_buffer，这些内部的buffer不会参与当前迭代的训练，训练只会使用之 前episode的样本。同时，初始化环境得到第一个环境信息s,并使用processState()函数 将其扁平化。我们初始化默认的done标记d、episode内总reward值rAll,以及episode 内的步数j。

L 228

[www.aibbt.com](http://www.aibbt.com) 让未来触手可及

with tf.Session() as sess: if load_model == True:

print(* Loading Model...*)

ckpt = tf.train.get_checkpoint_state(path)

saver. restore(sess_,ckpt.model_checkpoint_path)

sess.run(init)

updateTarget(targetOpSjsess) for i in range(num_episodes+l):

episodeBuffer = experience_buffer() s = env.reset() s = processState(s) d = False rAll = 0 j = 0

接着创建一个内层循环，每一次迭代执行一次Action。当总步数小于pre_train_steps 时，强制使用随机Action,相当于只从随机Action学习，但不去强化其过程。达到

pre_train_steps后，我们会保留一个较小的概率去随机选择Action。若不随机选择Action, 则传入当前状态s给主DQN,预测得到应该执行的Actiono然后使用env.step()执行一步 Action,并得到接下来的状态si、reward和done标记。我们使用processState对si进行 扁平化处理，然后将s、a、r、si、d等结果传入episodeBuffer中存储。

while j < max_epLength: j+=l

if np.random.rand(l) < e or total_steps < pre一train_steps: a = np.random.randint(0^4)

else:

a = sess.runCmainQN.predictj

feed_dict={mainQN.scalarlnput:[s]})[0]

sl^Tjd = env.step(a) si = processState(sl) total_steps += 1

episodeBuffer.add(np.reshape(np.array([s^a^r^sljd])[1,5]))

当总步数超过pre_train_steps时，我们持繹降低随机选择Action的概率e,直到达到 其最低值endE。并且每当总步数达到uPdate_freq的整数倍时，我们进行一次训练，即模 型参数的更新。首先是从myBuffer中sample出一个batch_size的样本，然后将训练样本 中第3列信息，即下一个状态si,传入mainQN并执行main.predict,得到主模型选择的 Action。再将si传入辅助的targetQN,并得到si状态下所有Action的Q值。接下来，使 用mainQN的输出Action,选择targetQN输出的Q,得到doubleQ。这里使用两个DQN 网络把选择Action和输出Q值两个操作分隔开来的做法，正是Double DQN的方法。然 后使用训练样本的第2列信息，即当前的reward,加上doubleQ乘以衰减系数y,得到我 们的学习目标targetQ。接着，传入当前的状态s，学习目标targetQ和这一步实际釆取的 Action,执行updateModel操作更新一次主模型mainQN的参数(即执行一次训练操作)。 同时也调用updateTarget函数，执行一次targetQN模型参数的更新(缓慢地向mainQN学 习)，这样就完整地完成了一次训练过程。同时，在每个step结束时，累计当前这步获取 的reward，并更新当前状态为下一步试验做准备。如果done标记为True，我们直接中断 这个episode的试验。

if total_steps > pre_train_steps: if e > endE:

—厂 Tenso「Flow 实战

e -= stepDrop

if total_steps % (update_freq) == 0:

trainBatch = myBuffer.sample(batch_size)

A = sess.run(mainQN.predict,feed_dict={

mainQN.scalarlnput:np.vstack(trainBatch[:^3])})

Q = sess.run(targetQN.QoutJfeed_dict={

targetQN.scalarInput:np.vstack(trainBatch[:3])}) doubleQ = Q[range(batch_size)^A] targetQ = trainBatch[:^2] + y*doubleQ _ = sess.run(mainQN.updateModelJfeed_dict={

mainQN.scalarlnput:np.vstack(trainBatch[:,0]),

mainQN.targetQ:targetQ,

mainQN.actions:trainBatch[:^1]})

updateTarget(targetOps^ sess) rAll += r

s = si

if d == True:

break

我们将episode内部的episodeBufTer添加到myBuffer中，用作以后训练抽样的数据 集，并将当前episode的reward添加到rList中。然后，每25个episode京尤展75—次它们 平均的reward值，同时每1000个episode或全部训练完成后，保存当前模型。

myBuffer.add(episodeBuffer.buffer) rList.append(rAll) if i>0 and i % 25 == 0:

print(1 episode、average reward of last 25 episode、 np.mean(rList[-25:]))

if i>0 and i % 1000 == 0:

saver.save(sess,path+'/model-'+str(i)+'.cptk') print("Saved Model")

saver.save(sessJpath+,/model-'+str(i)+'.cptk’)

在初始的200个episode内，即完全随机Action的前10000步内，平均可以获得reward 在2附近，这是基础的baseline。

计算每100个episodes的平均reward,并使用plt.plot展示reward变化的趋势。

rMat = np.resize(np.array(rList)[len(rList)//100j100])

rMean = np.average(rMatjl)

pit.plot(rMean)

如图8-13所示，我们可以看到从第1000个episode开始，reward快速提升，到第4000 个episode时基本达到了高峰，后面进入平台期，没有太大提升。

圏8-13训练过程中reward的变化趋势

本节中讲述了 DQN的基本原理，和使用DQN的几个非常重要的Trick。目前DQN 的研究仍在快速发展中，已经有越来越多新的技术被应用到DQN中。DQN首次被提出 了，在Atari 2600游戏中展示出了惊人的表现，并直接引发了深度强化学习的热潮。相 信在未来，DQN或Value Network会继续在更多地方发挥出强大的作用。
