#### 目录

TensorFlow 实战

目录

##### 1.1 TensorFlow 概要

Google第一代分布式机器学习框架DistBelief',在内部大规模使用后并没有选择开 源。而后第二代分布式机器学习系统TensorFlow2终于选择于2015年11月在GitHub上 开源，且在2016年4月补充了分布式版本，并于2017年1月发布了 1.0版本的预览，API 接口趋于稳定。目前TensorFlow仍处于快速开发迭代中，有大量新功能及性能优化在持 续研发。TensorFlow最早由Google Brain的研究员和工程师开发，设计初衷是加速机器 学习的研究，并快速地将研究原型转化为产品。Google选择开源TensorFlow的原因也非 常简单：第一是希望通过社区的力量，让大家一起完善TensorFlow。之前Google内部 DistBelief及TensorFlow的用户就贡献了非常多的意见和反馈，使得产品质量得到了快速 提升；第二是回馈社区，Google希望让这个优秀的工具得到更多的应用，从整体上提高 学术界乃至工业界使用深度学习的效率。除了 TensorFlow, Google也开源过大量成功的 项目，包括大名鼎鼎的移动操作系统Android、浏览器Chromium、编程语言Go、JavaScript 引擎V8、数据交换框架Protobuf、编译工具Bazel、OCR工具Tesseract等共计数百个高 质量的项目。

TensorFlow 白勺官方网址：www.tensorflow.org

GitHub 网址：github.com/tensorflow/tensorflow

模型仓库网址：github.com/tensorflow/modek

TensorFlow既是一个实现机器学习算法的接口，同时也是执行机器学习算法的框架。 它前端支持Python、C++、Go、Java等多种开发语言，后端使用C++、CUDA等写成。 TensorFlow实现的算法可以在众多异构的系统上方便地移植，比如Android手机、iPhone、 普通的CPU服务器，乃至大规模GPU集群，如图1-1所示。除了执行深度学习算法， TensorFlow还可以用来实现很多其他算法，包括线性回归、逻辑回归、随机森林等。 TensorFlow建立的大规模深度学习模型的应用场景也非常广，包括语音识别、自然语言 处理、计算机视觉、机器人控制、信息抽取、药物研发、分子活动预测等，使用TensorFlow 开发的模型也在这些领域获得了最前沿的成果。

图1-1 TensorFlow基础架构

为了研究超大规模的深度神经网络，Google在2011年启动了 Google Brain项目，同 时开发了第一代的分布式机器学习框架DistBeliefo有超过50个Google的团队在他们的 产品中使用了 DistBelief,比如Google Search中的搜索结果排序、Google Photos中的图 片标注、Google Translate中的自然语言处理等,都依赖于DistBelief建立的深度学习模型。 Google基于使用DistBelief时的经验及训练大规模分布式神经网络的需求，.开发了 TensorFlow——第二代分布式机器学习算法实现框架和部署系统。Google将著名的 Inception Net从DistBelief移植到TensorFlow后，获得了 6倍的训练速度提升。目前，在 Google内部使用TensorFlow的项目呈爆炸性的增长趋势，在2016年已经有超过2000个 项目使用了 TensorFlow建立的深度学习模型，而且这个数字还在高速增长中，如图1-2 所示。

\#of directories containing model description files

3000

sapoluvJIP luorojd 8nbluz>

Time

图1-2 TensorFlow在Google的使用趋势

TensorFlow使用数据流式图来规划计算流程，它可以将计算映射到不同的硬件和操 作系统平台。凭借着统一的架构，TensorFlow可以方便地部署到各种平台，大大简化了 真实场景中应用机器学习的难度。使用TensorFlow我们不需要给大规模的模型训练和小 规模的应用部署开发两套不同的系统，避免了同时维护两套程序的成本，TensorFlow给 训练和预测的共同部分提供了一个恰当的抽象。TensorFlow的计算可以表示为有状态的 数据流式图，对于大规模的神经网络训练，TensorFlow可以让用户简单地实现并行计算， 同时使用不同的硬件资源进行训练，同步或异步地更新全局共享的模型参数和状态。将一 个串行的TensorFlow算法改造成并行的成本也是非常低的，通常只需要对小部分代码进 行改写。相比于DistBelief, TensorFlow的计算模型更简洁灵活，计算性能显著提升，同 时支持更多的异构计算系统。大量Google内部的DistBelief用户转向了 TensorFlow,他 们使用TensorFlow进行各种研究和产品开发，包括在手机上跑计算机视觉模型，或是训 练有数百亿参数、数千亿数据的神经网络模型。虽然绝大多数的TensorFlow应用都在机 器学习及深度学习领域，但TensorFlow抽象出的数据流式图也可以应用在通用数值计算 和符号计算上，比如分形图计算或者偏微分方程数值求解。表1-1所示为TensorFlow的 主要技术特性。

| 表1-1 TensorFlow的主要技术特性 |                                                              |
| ------------------------------ | ------------------------------------------------------------ |
| 编程模型                       | Dataflow-like model （数据流模型）                           |
| 语言                           | Python、C++、Go、Rust、Haskell、Java （还有非官方的 Julia、JavaScript、R的支持） |
| 部署                           | Code once, run everywhere （一次编写，各处运行）             |

续表

| 计算资源 | CPU （ Linux、Mac、Windows、Android、iOS ）GPU （ Linux、Mac、Windows ）TPU （ Tensor Processing Unit,张量计算单元，主要用作推断） |
| -------- | ------------------------------------------------------------ |
| 实现方式 | Local Implementation （单机实现）Distributed Implementation （分布式实现） |
| 平台支持 | Google Cloud Platform （谷歌云平台）Hadoop File System （ Hadoop 分布式文件系统） |
| 数学表达 | Math Graph Expression （数学计算图表达）Auto Differentiation （自动微分） |
| 优化     | Common Subexpression Elimination （共同子图消除）Asynchronous Kernel Optimization （异步核优化）Communication Optimization （通信优化）Model Parallelism （模型并行）Data Parallelism （数据并行）Pipeline （流水线） |

##### 1.2 TensorFlow编程模型简介

###### 1.2.1核心概念

TensorFlow中的计算可以表示为一个有向图(directed graph ),或称计算图 (computation graph ),其中每一个运算操作(operation )将作为一个节点(node ),节点与 节点之间的连接称为边(edge)。这个计算图描述了数据的计算流程，它也负责维护和更 新状态，用户可以对计算图的分支进行条件控制或循环操作。用户可以使用Python,C++, Go, Java等几种语言设计这个数据计算的有向图。计算图中每一个节点可以有任意多个 输入和任意多个输出，每一个节点描述了一种运算操作，节点可以算是运算操作的实例化 (instance )。在计算图的边中流动(flow)的数据被称为张量(tensor ),故得名TensorFlow。 .而tensoi■的数据类型，可以是事先定义的，也可以根据计算图的结构推断得到。有一类特 殊的边中没有数据流动，这种边是依赖控制(control dependencies ),作用是让它的起始 节点执行完之后再执行目标节点，用户可以使用这样的边进行灵活的条件控制，比如限制 内存使用的最高峰值。下面是用Python设计并执行计算图的示例。计算图示例如图1-3 所示。

import terrsorflow as tf

b=tf .Variable(tf. zeros([100]))    # 生成 100 维的向量，初始化为 0

W=tf. Variable (tf. random_uniform( [784^ 100] -lj 1)) # 生成 784x100 的随机矩阵 W x=tf .placeholder(name=,,xn) # 输入的 Placeholder relu=tf.nn..matmul(W_, x)+b)    # ReLU(Wx+b)

C=[...]    #根据ReLU函数的结果计算Cost

s=tf.Session()

for step in range(0_, 10):

input:" .construct 100-D input array... # 为输入创建一个 100 维的向量 result=s.run(C, feed_dict={x: input}) # 获取 Cost,供给输入 x print(stepj result)

图1-3计算图示例

一个运算操作代表了一种类型的抽象运算，比如矩阵乘法或者向量加法。运算操作可 以有自己的属性，但是所有属性必须被预先设置，或者能在创建计算图时被推断出来。通 过设置运算操作的属性可以用来支持不同的tensor元素类型,比如让向量加法支持浮点数 （float）或者整数（int ）。运算核（kernel）是一个运算操作在某个具体硬件（比如在CPU 或者GPU中）的实现。在TensorFlow中，可以通过注册机制加入新的运算操作或者运算 核。表1-2所示为部分TensorFlow内建的运算操作。

表1-2 TensorFlow内建的运算操作

| 类       | 型                                                 | 示    例 |
| -------- | -------------------------------------------------- | -------- |
| 标量运算 | Add、Sub、Mul、Div、Exp、Log、Greater、Less、Equal |          |

续表

| 类             | 型                                                   | 示    例 |
| -------------- | ---------------------------------------------------- | -------- |
| 向量运算       | Concat、Slice、Split、Constant、Rank、Shape、Shuffle |          |
| 矩阵运算       | MatMul、Matrixlnverse、MatrixDeterminant             |          |
| 带状态的运算   | Variables Assign、AssfgnAdd                          |          |
| 神经网络组件   | SoftMax、Sigmoid、ReLU、Convolution2D、MaxPooling    |          |
| 储存、恢复     | Save、Restore                                        |          |
| 队列及同步运算 | Enqueue、Dequeue、MutexAcquire、MutexRelease         |          |
| 控制流         | Merge、Switch、Enter、Leave、Nextiteration           |          |

Session是用户使用TensorFlow时的交互式接口。用户可以通过Session的Extend方 法添加新的节点和边，用以创建计算图,然后就可以通过Session的Run方法执行计算图： 用户给出需要计算的节点，同时提供输入数据，TensorFlow就会自动寻找所有需要计算 的节点并按依赖顺序执行它们。对绝大多数的用户来说，他们只会创建一次计算图，然后 反复地执行整个计算图或是其中的一部分子图(sub-graph )o

在大多数运算中,计算图会被反复执行多次,而数据也就是tensor并不会被持续保留， 只是在计算图中过一遍。Variable是一类特殊的运算操作,它可以将一些需要保留的tensor 储存在内存或显存中，比如神经网络模型中的系数。每一次执行计算图后，Variable中的 数据tensor将会被保存，同时在计算过程中这些tensor也可以被更新，比如神经网络每一 次mini-batch训练时，神经网络的系数将会被更新并保存。使用Variable,可以在计算图 中实现一些特殊的操作，比如Assign、AssignAdd (+=)或AssignMul (*=)。

###### 1.2.2实现原理

TensorFlow有一个重要组件client,顾名思义，就是客户端，它通过Session的接口 与master及多个worker相连。其中每一个worker可以与多个硬件设备(device )相连， 比如CPU或GPU,并负责管理这些硬件。而master则负责指导所有worker按流程执行 计算图。TensorFlow有单机模式和分布式模式两种实现，其中单机指client、master、worker 全咅P在一台机器上的同一个进程中；分布式的版本允许client、master、worker在不同机 器的不同进程中，同时由集群调度系统统一管理各项任务。图1-4所示为单机版和分布式 版本的示例图。

![img](06TensorFlow实战_黄文坚（完整）[aibbt_files/06TensorFlow9e18_c4875a088c74095baibbt-7.jpg)



![img](06TensorFlow实战_黄文坚（完整）[aibbt_files/06TensorFlow9e18_c4875a088c74095baibbt-8.jpg)



图1-4 TensorFlow单机版本和分布式版本的示例图

TensorFlow中每一个worker可以管理多个设备，每一个设备的name包含硬件类别、 编号、任务号（单机版本没有），示例如下。

单机模式：/j ob: localhost/device: cpu: 0

分布式模式：/job:worker/task: 17/device:gpu:3

TensorFlow为CPU和GPU提供了管理设备的对象接口，每一个对象负责分配、释放 设备的内存，以及执行节点的运算核。TensorFlow中的tensor是多维数组，数据类型支持 8位至64位的int,以及IEEE标准的float、double和复数型，同时还支持任意字符串。 每一个设备有单独的allocator负责储存各种数据类型的tensor,同时tensor的引用次数也 会被记录，当引用数为0时，内存将被释放。如图1-5所示，TensorFlow支持的设备包括 x86 架构 CPU、手机上的 ARM CPU、GPU、TPU （ Tensor Processing Unit, Google 专门 为大规模深度学习计算定制的芯片，但目前还没有公开发布的计划），例如AlphaGo在 与李世石比赛时就大量使用了 TPU集群的计算资源。

手机



GPU



![img](06TensorFlow实战_黄文坚（完整）[aibbt_files/06TensorFlow9e18_c4875a088c74095baibbt-11.jpg)



图1-5 TensorFlow支持的设备

TPU



tpu集群



图1-5 TensorFlow支持的设备(续)

在只有一个硬件设备的情况下，计算图会按依赖关系被顺序执行。当一个节点的所有 上游依赖者P被执行完时(依赖数为0 )，这个节点就会被加入ready queue以等待执行。同 时，它下游所有节点的依赖数减1，实际上这就是标准的计算拓扑序的方式。当有多个设 备时，情况就变得复杂了，难点有二：

(1 )每一个节点该让什么硬件设备执行。

(2)如何管理节点间的数据通信。

对第1个问题，TensorFlow设计了一套为节点分配设备的策略。这个策略首先需要 计算一个代价模型，这个代价模型估算每一个节点的输入、输出tensor的大小，以及所需 要的计算时间。代价模型一部分由人工经验制定的启发式规则得到，另一部分则是由对一 小部分数据进行实际运算而测量得到的。接下来，分配策略会模拟执行整个计算图，首先 会从起点开始，按拓扑序执行。在模拟执行一个节点时，会把每一个能执行这个节点的设 备都测试一遍，这个测试会考虑代价模型对这个节点的计算时间的估算，加上数据传到这 个设备上所需要的通信时间,最后选择一个综合时间最短的设备作为这个节点的运算设备。 可以看到这个策略是一个简单的贪婪策略，它不能确保找到全局最优解，但是可以用较快 的速度找到一个不错的节点运算分配方案。同时除了运行时间，内存的最高使用峰值也会 被考虑进来。目前TensorFlow的节点分配策略仍在不断研发、优化，将来可能使用一个 强化学习(Reinforcement Learning )的神经网络来辅助决策。此外，TensorFlow还允许 用户对节点的分配设置限制条件，比如“只给这个节点分配GPU类型的设备”，“只给这 个节点分配/job:worker/task: 17上的设备”,“这个节点分配的设备必须和variable 13 一致”。 对于这些限制条件，TensorFlow会先计算每个节点可以使用的设备，再使用并查集

(union-fmd)算法找到必须使用同一个设备的节点。

我们再来看第2个问题，当给节点分配设备的方案被确定，整个计算图就会被划分为 许多子图，使用同一个设备并且相邻的节点会被划分到同一个子图。然后计算图中从x 到的边,会被取代为一个发送端的发送节点(send node )、一个接收端的接收节点(receive node),以及从发送节点到接收节点的边，如图1-6所示。

Device B    「Device B

Device A    [Device A

图1-6 TensorFlow的通信机制

这样就把数据通信的问题转变为发送节点和接收节点的实现问题，用户不需要为不同 的硬件环境实现通信方法。同时两个子图之间可能会有多个接收节点，如果这些接收节点 接收的都是同一个tensor,那么所有这些接收节点会被自动合并为一个，避免了数据的反 复传输或者重复占用设备内存。总结一下，TensorFlow的通信机制很优秀，发送节点和 接收节点的设计简化了底层的通信模式，用户无须设计节点之间的通信流程，可以让同一 套代码，自动扩展到不同硬件环境并处理复杂的通信流程。图1-7所示为CPU与GPU之 间通信的流程。

图1-7 CPU与GPU的通信过程

同时，从单机单设备的版本改造为单机多设备的版本也非常容易，下面的代码只添加 了加粗的这一行，就实现了从一块GPU训练到多块GPU训练的改造。

for i in range(8):

for d in range(4):

with tf.device('7gpu:%dn % d):

input = x[i] if d is 0 else m[d-l]

m[d]j c[d] = LSTMCell(input, mprev[d]_, cprev[d])    .

mprev[d] = m[d] cprev[d] = c[d]

TensorFlow分布式执行时的通信和单机设备间的通信很像，只不过是对发送节点和 接收节点的实现不同：比如从单机的CPU到GPU的通信，变为不同机器之间使用TCP 或者RDMA传输数据。同时，容错性也是分布式TensorFlow的一个特点。故障会在两种 情况下被检测出来，一种是信息从发送节点传输到接收节点失败时，另一种是周期性的 worker心跳检测失败时。当一个故障被检测到时，整个计算图会被终止并重启。其中 Variable node可以被持久化，TensorFlow支持检查点(checkpoint)的保存和恢复，每一 个Variable node都会链接一个Save node,每隔几轮迭代就会保存一次数据到持久化的储 存系统，比如一个分布式文件系统。同样，每一个Variable node都会连接一个Restore node, 在每次重启时会被调用并恢复数据。这样在发生故障并重启后，模型的参数将得以保留， 训练将从上一个checkpoint恢复而不需要完全从头再来。

图1-8所示为使用TensorFlow GPU集群进行分布式训练的性能对比图，在GPU 数量小于16时，基本没有性能损耗。直到50块GPU时，依然可以获得80%的效率，也 就是40倍于单GPU的提速。在100块GPU时，最终可以获得56倍的提速，也就是56% 的使用效率，可以看到TensorFlow在大规模分布式系统上有相当高的并行效率。

ndoauo snsJ9>dnpaa>dc^



0 5 0 5 6 4 3 1

Number of GPUs

图1-8 TensorFlow分布式训练性育旨



###### 1.2.3拓展功能

在深度学习乃至机器学习中，计算cost function的梯度都是最基本的需求，因此 TensorFlow也原生支持自动求导。比如一个fe胃r C在计算图中有一组依赖的Ze/n {Xk}， 那么在TensorFlow中可以自动求出以C/~。这个求解梯度的过程也是通过在计算图拓展 节点的方式实现的，只不过求梯度的节点对用户是透明的。

如图1-9所示，当TensorFlow计算一个tensor C关于tensor I的梯度时，会先寻找 从/到C的正向路径，然后从C回溯到/，对这条回溯路径上的每一个节点增加一个对应 求解梯度的节点，并根据链式法则(chain rule)计算总的梯度，这就是大名鼎鼎的反向 传播(back propagation, BP )算法。这些新增的节点会计算梯度函数(gradient function ), 比如[db，dW，dx] = tf.gradients(C，[b，W，x])o

自动求导虽然对用户很方便，但伴随而来的是TensorFlow对计算的优化(比如为节 点分配设备的策略)变得很麻烦，尤其是内存使用的问题。在正向执行计算图，也就是进 行推断(inference)时，因为确定了执行顺序，使用经验的规则是比较容易取得好效泉的， tensoi•在产生后会迅速地被后续节点使用掉，不会持续占用内存。然而在进行反向传播计 算梯度时，经常需要用到计算图开头的tensor,这些tensor可能会占用大量的GPU显存， 也限制了模型的规模。目前TensorFlow仍在持续改进这些问题，包括使用更好的优化方 法；重新计算tensor,而不是保存tensor;将tensor从GPU显存移到CPU控制的主内存 中。

TensorFlow还支持单独执行子图，用户可以选择计算图的任意子图，并沿某些边输 入数据，同时从另一些边获取输出结果。TensorFlow用节点名加port的形式指定数据， 例如bar:0表示名为bar的节点的第1个输出。在调用Session的Run方法执行子图时， 用户可以选择一组输入数据的映射，比如name:port -〉tensor；同时用户必须指定一组输 出数据，比如name[:port],来选择执行哪些节点，如果port也被选择，那么这些port输 出的数据将会作为Run函数调用的结果返回。然后整个计算图会根据输入和输出进行调 整，输入数据的节点会连接一个feed node,输出数据的节点会连接一个fetch node。 TensorFlow会根据输出数据自动推导出哪些节点需要被执行。如图1-10所示，我们选择 用一些数据替换掉b,那么feed node会替代b连接到c。同时，我们只需要获取f:0的结 果，所以一个fetch node便会连接到f，这样d和e就不会被执行，所以最终需要执行的 节点便只有a、c和f。

| fetch |

图1-10 TensorFlow子图的执行示例

TensorFlow支持计算图的控制流，比如if-condition和while-loop,因为大部分机器学 习算法需要反复迭代，所以这个功能非常重要。TensorFlow提供Switch和Merge两种 operator,可以根据某个布尔值跳过某段子图，然后把两段子图的结果合并，实现if-else 的功能。同时还提供了 Enter、Leave和Nextlteration用来实现循环和迭代。在使用高阶语 言（比如Python）的if-else、while、for控制计算流程时，这些控制流会被自动编译为上 述那些operator,方便了用户。Loop中的每一次循环会有唯一的tag，它的执行结果会输 出成frame,这样用户可以方便地查询结果日志。同时，TensorFlow的控制流支持分布式， 每一轮循环中的节点可能分布在不同机器的不同设备上。分布式控制流的实现方式也是依 靠计算图的改写,循环内的节点会被划分到不同的小的子图，每一个子图会连接控制节点， 实现自己的循环，同时将循环终止等信号发送到其他子图。同时，控制流也提供了对计算 图中隐含的梯度计算节点的支持，TensorFlow会将控制流中的自动求导功能隐藏到底层， 用户不需要考虑这部分功能的逻辑设计。

TensorFlow的数据输入除了通过feed node,也有特殊的input node可以让用户直接 输入文件系统的路径，例如一个Google Cloud Platform的文件路径。如果从feed node

输入数据，那么数据必须从client读取，并通过网络传到分布式系统的其他节点，这样会 有较大的网络开销，直接使用文件路径，可以让worker节点读取本地的文件，提高效率。

队列（queue）也是TensorFlow任务调度的一个重要特性，这个特性可以让计算图的 不同节点异步地执行。使用队列的一个目的是当一个batch的数据运算时，提前从磁盘读 取下一个batch的数据，减少磁盘I/O阻塞时间。同时还可以异步地计算许多梯度，再组 合成一个更复杂的整体梯度。除了传统的先进先出（FIFO）队列，TensorFlow还实现了 洗牌队列（shuffling queue）,用以满足某些机器学习算法对随机性的要求，这对于损失函 数优化或者模型收敛会有帮助。

容器（Container）是TensorFlow中一种特殊的管理长期变量的机制，例如Variable 对象就储存在容器中。每一个进程会有一个默认的容器一直存在，直到进程结束。使用容 器甚至可以允许不同计算图的不同Session之间共享一些状态值。

###### 1.2.4性能优化

在TensorFlow中有很多高度抽象的运算操作，这些运算操作可能是由很多层复杂的 计算组合而成的，当有多个高阶运算操作同时存在时，它们的前几层可能是完全一致的重 复计算（输入及运算内容均一致）。这时，TensorFlow会自动识别这些重复计算，同时改 写计算图，只执行一次重复的计算，然后把这几个高阶运算操作后续的计算连接到这些共 有的计算上，避免冗余计算。

同时，巧妙地安排运算的顺序也可以极大地改善数据传输和内存占用的问题。比如适 当调整顺序以错开某些数据同时存在于内存的时间，对于显存容量比较小的GPU来说至 关重要。TensorFlow也会精细地安排接收节点的执行时间，如果接收节点过早地接收数 据，那么数据会堆积在设备的内存中，所以TensorFlow设计了策略让接收节点在刚好需 要数据来计算时才开始接收数据。

TensorFlow也提供异步计算的支持，这样线程执行时就无须一直等待某个节点计算 完成。有一些节点，比如receive、enqueue、dequeue就是异步的实现，这些节点不必因 等待I/O而阻塞一个线程继续执行其他任务。

TensorFlow同时支持几种高度优化的第三方计算库。

线性代数计算库：

• Eigen3

矩阵乘法计算库:

,BLAS4

• cuBLAS (CUDA BLAS)5 深度学习计算库：

« cuda-convnet6

• cuDNN7

很多机器学习算法在数字精度较低时依然可以正常工作，TensorFlow也支持对数据 进行压缩。比如将32-bit浮点数有损地压缩为16-bit浮点数，这样做可以降低在不同机器、 不同设备之间传输数据时的网络开销，提高数据通信效率。

TensorFlow提供了三种不同的加速神经网络训练的并行计算模式。

(1)数据并行：通过将一个mini-batch的数据放在不同设备上计算，实现梯度计算的 并行化。例如，将有1000条样本的mini-batch拆分成10份100条样本的数据并行计算， 完成后将10份梯度数据合并得到最终梯度并更新到共享的参数服务器(parameter server)。 这样的操作会产生许多完全一样的子图的副本，在client上可以用一个线程同步控制这些 副本运算的循环。TensorFlow中的数据并行如图1-11所示。

Synchronous Data Parallelism

图1 -11 TensorFlow中的数据并行

上述这个操作还可以改成异步的，使用多个线程控制梯度计算，每一个线程计算完后 异步地更新模型参数。同步的方式相当于用了一个较大的mini-batch (当然其中的批标准 化(batch normalization )还是独立的)，其优点是没有梯度干扰，缺点是容错性差，一台 机器出现问题后可能需要重跑。异步的方式的优点是有一定的容错性，但是因为梯度干扰 的问题，导致每一组梯度的利用效率都下降了。另外一种就是混合式，比如两组异步的， 其中每组有50份同步的训练。从图1-12中可以看到，一般来说，同步训练的模型精度较 好。

sync5O+2

0.770    •

60    70    80    90    100    110

hours

图1-12数据并行中同步、异步训练的性能对比

图1-13所示为使用10块GPU和50块GPU训练Inception时的对比图，要达到相同 的精度，50块GPU需要的时间只是10块的1/4左右。

50 replicas 10 replicas

Hours

图1-13    10快GPU和50块GPU的训练效率对比

相比于模型并行，数据并行的计算性能损耗非常小，尤其是对于sparse的model。因 为不同mini-batch之间干扰的概率很小，所以经常可以同时进行很多份(replicas )数据并 行，甚至可高达上千份。表1-3所示为Google的各个项目中使用的数据并行的份数或者 GPU数目。

表1 -3 Google使用数据并行的项目

| RankBrain                            | 500份数据并行                   |
| ------------------------------------ | ------------------------------- |
| ImageNet Inception Model             | 50块GPU，40倍提速               |
| SmartReply                           | 16份数据并行，每一份包含多块GPU |
| Language model on “One Billion Word” | 32 块 GPU                       |

(2)模型并行：将计算图的不同部分放在不同的设备上运算，可以实现简单的模型并 行，其目标在于减少每一轮训练迭代的时间，不同于数据并行同时进行多份数据的训练。 模型并行需要模型本身有大量可以并行，且互相不依赖或者依赖程度不高的子图。在不同 的硬件环境上性能损耗不同，比如在单核的CPU上使用SIMD是没有额外开销的，在多 核CPU上使用多线程也基本上没有额外开销，在多GPU上的限制主要在PCIe的带宽， 在多机之间的限制则主要在网络开鞘。TensorFlow中的模型并行如图1-14所示。

| I Clie--3==** | nt 1- |      |      |      |
| ------------- | ----- | ---- | ---- | ---- |
| Device 3 广   |       |      |      |      |
|               |       |      |      |      |
| id            |       |      |      |      |
|               |       |      |      |      |
|               |       |      |      |      |

图1-14 TensorFlow中的模型并行

(3)流水线并行：和异步的数据并行很像，只不过是在同一个硬件设备上实现并行。 大致思路是将计算做成流水线，在一个设备上连续地并行执行，提高设备的利用率，如图 1-15所示。

| Client |

图1 -15 TensorFlow中的流水线并行

未来，TensorFlow会支持把任意子图独立出来，封装成一个函数，并让不同的前端 语言（比如Python或者C++）来调用。这样将设计好的子图发布在开源社区中，大家的 工作就可以方便地被分享了。TensorFlow也计划推出优化计算图执行的just-in-time编译 器（目前在TensorFlow 1.0.0-rc0中已有试验性的XLA组件，可提供JIT及AOT编译优 化），期望可以自动推断出tensor的类型、大小，并自动生成一条高度优化过的流水线。 同时，TensorFlow还会持续优化为运算节点分配硬件设备的策略，以及节点执行排序的 策略。
