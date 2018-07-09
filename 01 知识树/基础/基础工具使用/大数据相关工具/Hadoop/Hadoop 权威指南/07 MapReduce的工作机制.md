### MapReduce的工作机制

在本章中，我们将深入学习Hadoop中的MapReduce工作机制。这些知识将为我 们随后两章学习写MapReduce高级编程奠定基础。

##### 7.1剖析MapReduce作业运行机制

可以通过一个简单的方法调用来运行MapReduce作业：］ob对象的submit()方 法。注意，也可以调用waitForCompletion(),它用干提交以前没有提交过的作 业，并等待它的完成。®submit()方法调用封装了大量的处理细节。本小节将揭 示Hadoop运行作业时所采取的措施。

整个过程描述如图7-1所示。在最高层，有以下5个独立的实体®。

客户端，提交MapReduce作业。

YARN资源管理器，负责协调集群上计算机资源的分配。

YARN节点管理器，负责启动和监视集群中机器上的计算容器

(container) 0

![img](Hadoop43010757_2cdb48_2d8748-101.jpg)



MapReduce 的 application master，负责协调运行 MapReduce 作业 的任务。它和MapReduce任务在容器中运行，这些容器由资源管理器分 配并由节点管理器进行管理。

①    老版本 MapReduce API 中，调用］obClient.submit］ob(conf)或］obClient.run］ob(conf )0

②    本节中没有涉及作业历史服务端守护进程(负责维护作业历史数据)和shuffle处理器辅助眼务 (负责将map输出传送给reduce任务)的讨论。

•分布式文件系统(一般为HDFS，参见第3章)，用来与其他实体间共享 作业文件。

###### 7.1.1作业的提交

]ob的submit()方法创建一个内部的ZJobSummiter实例，并且调用其 submitJoblnternalO方法(参见图7-1的步骤1)。提交作业后， waitForCompletion()每秒轮询作业的进度，如果发现自上次报告后有改变，便 把进度报告到控制台。作业完成后，如果成功，就显示作业计数器：如果失败， 则导致作业失败的错误被记录到控制台。

;:Job

client node

5a: start container

4: submit application



| MapReduceC    it /y'' ' ■* •::、•'••••••’••    A、'， | 1: run job       |
| ----------------------------------------------------- | ---------------- |
| program                                               | ..............RJ |

client JVM



resource manager node



3: copy job resources



•:難海籌鑛

NodeManager

’頓醸釀觀.



/ 8: allocate resources



■.職t、



5b: launch



6: initialize ,

mJ.

tart

liner



node manager node 9b: launch:

7: retrieve



input splits



參

耋

task JVM

1 Filesystem | r (e.g., HOFS)

10: retrieve job resources



Md

德醐趣紀滿證i



11:run:

node manager node

7-1. Hadoop运行MapReduce作业的工作原理

JobSummiter所实现的作业提交过程如下所述。

•向资源管理器请求一个新应用ID，用于MapReduce作业ID。请参见 步骤2。

检查作业的输出说明。例如，如果没有指定输出目录或输出目录已经存 在，作业就不提交，错误抛回给MapReduce程序。

计算作业的输入分片。如果分片无法计算，比如因为输入路径不存在， 作业就不提交，错误返回给MapReduce程序。

将运行作业所需要的资源（包括作业JAR文件、配置文件和计算所得的 输入分片）复制到一个以作业ID命名的目录下的共享文件系统中（请参见 步骤 3）。作业 JAR 的复本较多（由 mapreduce.client.submit.file, replication属性控制，默认值为10），因此在运行作业的任务时，集群 中有很多个复本可供节点管理器访问。

通过调用资源管理器的submitApplication（）方法提交作业。请参见 步骤4。

###### 7.1.2作业的初始化

资源管理器收到调用它的submitApplication（）消息后，便将请求传递给YARN 调度器（scheduler）。调度器分配一个容器，然后资源管理器在节点管理器的管理下 在容器中启动application master的进程（步骤5a和5b）。

MapReduce作业的application master是一个Java应用程序，它的主类是 MRAppMaster 0由于将接受来自任务的进度和完成报告（步骤6），因此 application master对作业的初始化是通过创建多个簿记对象以保持对作业进 度的跟踪来完成的。接下来，它接受来自共享文件系统的、在客户端计算的输入 分片（步骤7）。然后对每一个分片创建一个map任务对象以及由 mapreduce.job.reduces 属性（通过作业的 setNumReduceTasks（）方法设置）确 定的多个reduce任务对象。任务ID在此时分配。

application master必须决定如何运行构成MapReduce作业的各个任务。如果作业 很小，就选择和自己在同一个JVM上运行任务。与在一个节点上顺序运行这些任 务相比，当application master判断在新的容器中分配和运行任务的开销大于并行

运行它们的开销时，就会发生这一情况。这样的作业称为uberized，或者作为 任务运行。

哪些作业是小作业？默认情况下，小作业就是少于10个mapper且只有1个reducer且输 人大小小于一个HDFS块的作业（通过设置mapreduce.job.ubertask.maxmaps , mapreduce.job.ubertask.maxreduces 和 mapreduce.job.ubertask.maxbytes 可以改

变这几个值）。必须明确启用Uber任务（对于单个作业，或者是对整个集群），具体 方法是将 mapreduce. job. ubertask .enable 设置为 true。

最后，在任何任务运行之前，application master调用setup]ob（）方法设置 OutputCommittero FileOutputCommitten为默认值，表示将建立作业的最终输出目录 及任务输出的临时工作空间。提交协议（commit protocol）将在7.4.3节介绍。

###### 7.1.3任务的分配

如果作业不适合作为uber任务运行，那么application master就会为该作业中的所 有map任务和reduce任务向资源管理器请求容器（步骤8）。首先为Map任务发出 请求，该请求优先级要高于reduce任务的请求，这是因为所有的map任务必须在 reduce的排序阶段能够启动前完成（详见7.3节）。直到有5%的map任务已经完成 时，为reduce任务的请求才会发出（详见10.3.5节）。

reduce任务能够在集群中任意位置运行，但是map任务的请求有着数据本地化局 限，这也是调度器所关注的（详见4.1.1节）。在理想的情况下，任务是数据本地化 （data的，意味着任务在分片驻留的同一节点上运行。可选的情况是，任务 可能是机架本地化（md /^aZ）的，即和分片在同一机架而非同一节点上运行。有 一些任务既不是数据本地化，也不是机架本地化，它们会从别的机架，而不是运 行所在的机架上获取自己的数据。对于一个特定的作业运行，可以通过査看作业 的计数器来确定在每个本地化层次上运行的任务的数量（参见表9-6）。

请求也为任务指定了内存需求和CPU数。在默认情况下，每个map任务和reduce 任务都分配到1024 MB的内存和一个虚拟的内核，这些值可以在每个作业的基础 上进行配置（遵从于10.3.3节描述的最大值和最小值），分别通过4个属性来设置

mapreduce.map.memory.mb、mapreduce.reduce.memory.mb、mapreduce.map.cpu• vcores 和 mapreduce. reduce, cpu. vcoresp. memory. mb0

###### 7.1.4任务的执行

一旦资源管理器的调度器为任务分配了一个特定节点上的容器，application master 就通过与节点管理器通信来启动容器（步骤9a和9b）。该任务由主类为YarnChild 的一个Java应用程序执行。在它运行任务之前，首先将任务需要的资源本地化， 包括作业的配置、JAR文件和所有来自分布式缓存的文件（步骤10,参见9.4.2 节）。最后，运行map任务或reduce任务（步骤11）。

YarnChild在指定的JVM中运行，因此用户定义的map或reduce函数（甚至是 YarnChild）中的任何缺陷不会影响到节点管理器，例如导致其崩溃或挂起。

每个任务都能够执行搭建（setup）和提交（commit）动作，它们和任务本身在同一个 JVM中运行，并由作业的OutputCommitter确定（参见7.1.4节）。对于基于文件

的作业，提交动作将任务输出由临时位置搬移到最终位置。提交协议确保当推测 执行（speculative execution）被启用时（参见7.4.2节），只有一个任务副本被提交，其 他的都被取消。

Streaming

Streaming运行特殊的map任务和reduce任务，目的是运行用户提供的可执行程 序，并与之通信（参见图7-2）。

Streaming

launch

task JVM

##### ill

run

input

key/values

stdin

\* output !key/valu«

stdout

launch

7-2.可执行的Streaming与节点管理器及任务容器的关系

Streaming任务使用标准输入和输出流与进程（可以用任何语言写）进行通信。在任 务执行过程中，Java进程都会把输入键-值对传给外部的进程，后者通过用户定义 的map函数和reduce函数来执行它并把输出键-值对传回Java进程。从节点管理 器的角度看，就像其子进程自己在运行map或reduce代码一样。

###### 7.1.5进度和状态的更新

MapReduce作业是长时间运行的批量作业，运行时间范围从数秒到数小时。这可 能是一个很长的时间段，所以对于用户而言，能够得知关于作业进展的一些反馈 是很重要的。一个作业和它的每个任务都有一个状态（status）,包括：作业或任务 的状态（比如，运行中，成功完成，失败）、map和reduce的进度、作业计数器的 值、状态消息或描述（可以由用户代码来设置）。这些状态信息在作业期间不断改 变，它们是如何与客户端通信的呢？ 任务在运行时，对其进度（progress,即任务完成百分比）保持追踪。对map任务， 任务进度是已处理输入所占的比例，对reduce任务，情况稍微有点复杂，但系统 仍然会估计已处理reduce输入的比例。整个过程分成三部分，与shuffle的三个阶 段相对应（详情参见7.3节）。比如，如果任务已经执行reducer 一半的输入，那么 任务的进度便是5/6,这是因为已经完成复制和排序阶段（每个占1/3），并且已经 完成reduce阶段的一半（1/6）。

MapReduce中进度的组成

进度并不总是可测量的，但是虽然如此，它能告诉Hadoop有个任务正在做一 些事情。比如，正在写输出记录的任务是有进度的，即使此时这个进度不能用 需要写的总量的百分比来表示（因为即便是产生这些输出的任务，也可能不知 道需要写的总量）。

进度报告很重要。构成进度的所有操作如下：

•    读入一条输入记录（在mapper或reducer中）

•    写入一条输出记录（在mapper或reducer中）

•    设置状态描述（通过Reporter或TaskAttemptContext的 setStatusO 方法）

•    增加计数器的值（使用Reporter的incrCounter（）方法或Counter的 increment（）方法）

•调用 Reporter'或 TaskAttemptContext 的 progress（）方法

任务也有一组计数器，负责对任务运行过程中各个事件进行计数（详情参见2.3.2 节），这些计数器要么内置于框架中，例如已写入的map输出记录数，要么由用户 自己定义。

当map任务或reduce任务运行时，子进程和自己的父application master通过 umbilical接口通信。每隔3秒钟，任务通过这个umbilical接口向自己的 application master报告进度和状态（包括计数器），application master会形成一个作 业的汇聚视图（aggregate view）。

资源管理器的界面显示了所有运行中的应用程序，并且分别有链接指向这些应用 各自的application master的界面，这些界面展示了 MapReduce作业的更多细节，

包括其进度。

在作业期间，客户端每秒钟轮询一次application master以接收最新状态（轮询间隔 通过 mapreduce.client.progressmonitor.pollinterval 设置）。客户端也可 以使用］ob的getStatus（）方法得到一个：JobStatus的实例，后者包含作业的 所有状态信息。

7-3对上述过程进行了图解。

client node



managernode



getJobStatus \



NodeManager



A ■

| 1     |          | ',<*• /.J. 1k    • 1 |
| ----- | -------- | -------------------- |
| MRA： | 叩MastBi | 幽ef 1」             |

<•

node manager node



（排 ws）



NodeManager

[progress or counter updated statusUpdate]

node manager node



7-3.状态更新在MapReduce系统中的传递流程

###### 7.1.6作业的完成

当application master收到作业最后一个任务已完成的通知后，便把作业的状态设 置为“成功”。然后，在]ob轮询状态时，便知道任务已成功完成，于是〕ob打印一 条消息告知用户，然后从waitForCompletion（）方法返回。Job的统计信息和计 数值也在这个时候输出到控制台。

如果application master有相应的设置，也会发送一个HTTP作业通知。希望收3回 调指令的客户端可以通过mapreduce.job.end-notification.url属性来进行这项

设置。

最后，作业完成时，application master和任务容器清理其工作状态（这样中间输出 将被删除），OutputCommitter的commit]ob（）方法会被调用。作业信息由作业 历史服务器存档，以便日后用户需要时可以查询。

##### 7.2失败

在现实情况中，用户代码错误不断，进程崩溃，机器故障，如此种种。使用 Hadoop最主要的好处之一是它能处理此类故障并让你能够成功完成作业。我们需 要考虑以下实体的失败：任务、application master,节点管理器和资源管理器。

###### 7.2.1任务运行失败

首先考虑任务失败的情况。最常见的情况是map任务或reduce任务中的用户代码 抛出运行异常。如果发生这种情况，任务JVM会在退出之前向其父application master发送错误报告。错误报告最后被记入用户日志。application master将此次任 务任务尝试标记为为//eJ（失败），并释放容器以便资源可以为其他任务使用。

对于Streaming任务，如果Streaming进程以非零退出代码退出，则被标记为失 败。这种行为由stream.non.zero.exit.is.failure属性（默认值为true）来

控制。

另一种失败模式是任务JVM突然退出，可能由于JVM软件缺陷而导致MapReduce 用户代码由于某些特殊原因造成JVM退出。在这种情况下，节点管理器会注意到 进程已经退出，并通知application master将此次任务尝试标记为失败。

任务挂起的处理方式则有不同。一旦application master注意到已经有一段时间没

有收到进度的更新，便会将任务标记为失败。在此之后，任务JVM进程将被自动 杀死。®任务被认为失败的超时间隔通常为10分钟，可以以作业为基础（或以集群 为基础）进行设置，对应的属性为mapreduce.task.timeout，单位为毫秒。

超时（timeout）设置为0将关闭超时判定，所以长时间运行的任务永远不会被标记 为失败。在这种情况下，被挂起的任务永远不会释放它的容器并随着时间的推移 最终降低整个集群的效率。因此，尽量避免这种设置，同时充分确保每个任务能够 定期汇报其进度。参见7.1.5节的补充材料“MapReduce中进度的组成”。

application master被告知一个任务尝试失败后，将重新调度该任务的执行。 application master会试图避免在以前失败过的节点管理器上重新调度该任务。此 外，如果一个任务失败过4次，将不会再重试。这个值是可以设置的：对于map任 务，运行任务的最多尝试次数由mapreduce.map.maxattempts属性控制；而对于 reduce任务，则由mapreduce.reduce.maxattempts属性控制。在默认情况 下，如果任何任务失败次数大于4（或最多尝试次数被配置为4），整个作业都会 失败。

对于一些应用程序，我们不希望一旦有少数几个任务失败就中止运行整个作业， 因为即使有任务失败，作业的一些结果可能还是可用的。在这种情况下，可以为作 业设置在不触发作业失败的情况下允许任务失败的最大百分比。针对map任务和

reduce 任务的设置可以通过 mapreduce. map.failures.maxpercent 和 mapreduce. reduce, failures, maxpercent 这两个属 14^ 完成。

任务尝试（task attempt）也是可以中止的（killed），这与失败不同。任务尝试可以被中

止是因为它是一个推测副本（相关详情可以参见7.4.2节）或因为它所处的节点管理 器失败，导致application master将它上面运行的所有任务尝试标记为killed。被中 止的任务尝试不会被计入任务运行尝试次数（由mapreduce.map.maxattempts和 mapreduce.reduce.maxattempts设置），因为尝试被中止并不是任务的过错。

用户也可以使用Web UI或命令行方式（输入mapped job査看相应的选项）来中止

①如果一个Streaming进程挂起，节点管理器在下面这种情况中将会终止它（与启动它的JVM — 起）：yarn.nodemanager.container-executor.class 被设置为 org.apache.hadoop. yarn.server.nodemanager. LinuxContainerExecutor 或者默认的容器执行器正被使用并 且系统上可以使用setid命令（这样，任务JVM和它启动的所有进程都在同一个进程群中）。 对于其他情况，孤立的Streaming进程将堆积在系统上，随着时间的推移，这会影响到利 用率。

或取消任务尝试。也可以采用相同的机制来中止作业。

###### 7.2.2 application master 运行失败

YARN中的应用程序在运行失败的时候有几次尝试机会，就像MapReduce任务在 遇到硬件或网络故障时要进行几次尝试一样。运行M叩Reduce application master 的最多尝试次数由mapreduce.am.max-attempts属性控制。默认值是2,即如 果MapReduce application master失败两次，便不会再进行尝试，作业将失败。

YARN对集群上运行的YARN application master的最大尝试次数加以了限制，单个 的应用程序不可以超过这个限制。该限制由yarn.resourcemanager. am.max-attempts 属性设置，默认值是2,这样如果你想增加MapReduce application master的尝试次数，你也必须增加集群上YARN的设置。

恢复的过程如下。application master向资源管理器发送周期性的心跳，当 application master失败时，资源管理器将检测到该失败并在一个新的容器（由节点 管理器管理）中开始一个新的master实例。对于Mapreduce application master,它;

将使用作业历史来恢复失败的应用程序所运行任务的状态，使其不必重新运行。 默认情况下恢复功能是开启的，但可以通过设置yarn.app.mapreduce.am.job. recovery.enable为false来关闭这个功能。

Mapreduce客户端向application master轮询进度报告，但是如果它的application

master运行失败，客户端就需要定位新的实例。在作业初始化期间，客户端向资 源管理器询问并缓存application master的地址，使其每次需要向application master 査询时不必重载资源管理器。但是，如果application master运行失败，客户端就

会在发出状态更新请求时经历超时，这时客户端会折回向资源管理器请求新的 application master的地址。这个过程对用户是透明的。

###### 7.2.3节点管理器运行失败

如果节点管理器由于崩溃或运行非常缓慢而失败，就会停止向资源管理器发送心 跳信息（或发送频率很低）。如果10分钟内（可以通过属性yarn.resourcemanager. nm.liveness-monitor.expiry-interval-ms设置，以毫秒为单位）没有收到一条心 跳信息，资源管理器将会通知停止发送心跳信息的节点管理器，并且将其从自己的节 点池中移除以调度启用容器。

在失败的节点管理器上运行的所有任务或application master都用前两节描述的机

制进行恢复。另外，对于那些曾经在失败的节点管理器上运行且成功完成的map 任务，如果属于未完成的作业，那么application master会安排它们重新运行。这 是由于这些任务的中间输出驻留在失败的节点管理器的本地文件系统中，可能无 法被reduce任务访问的缘故。

如果应用程序的运行失败次数过高，那么节点管理器可能会被拉黑，即使节点管 理自己并没有失败过。由application master管理黑名单，对干MapReduce，如果 一个节点管理器上有超过三个任务失败，application master就会尽量将任务调度到 不同的节点上。用户可以通过作业属性mapreduce.job.maxtaskfailures. per.tracker设置该阈值。

注意，在本书写作时，资源管理器不会执行对应用程序的拉黑操作，因此新作

业中的任务可能被调度到故障节点上，即使这些故障节点已经被运行早期作业 的 application master 拉黑。

###### 7.2.4资源管理器运行失败

资源管理器失败是非常严重的问题，没有资源管理器，作业和任务容器将无法启 动。在默认的配置中，资源管理器是个单点故障，这是由于在机器失败的情况下 （尽管不太可能发生），所有运行的作业都失败且不能被恢复。

为获得高可用性（HA），在双机热备配置下，运行一对资源管理器是必要的。如果 主资源管理器失败了，那么备份资源管理器能够接替，且客户端不会感到明显的 中断。

关于所有运行中的应用程序的信息存储在一个高可用的状态存储区中（由 ZooKeeper或HDFS备份），这样备机可以恢复出失败的主资源管理器的关键状 态。节点管理器信息没有存储在状态存储区中，因为当节点管理器发送它们的第 一个心跳信息时，节点管理器的信息能以相当快的速度被新的资源管理器重构。 （同样要注意，由于任务是由application master管理的，因此任务不是资源管理器 的状态的一部分。这样，要存储的状态量比MapReduce 1中jobtracker要存储的状 态量更好管理。）

当新资源管理器启动后，它从状态存储区中读取应用程序的信息，然后为集群中 运行的所有应用程序重启application master。这个行为不被计为失败的应用程序 尝试（所以不会计入yarn.resourcemanager.am.max-attempts）,这是因为应用

程序并不是因为程序代码错误而失败，而是被系统强行中止的。实际情况中， application master重启不是MapReduce应用程序的问题，因为它们是恢复已完成 的任务的工作（详见7.2.2节）。

资源管理器从备机到主机的切换是由故障转移控制器（failover controller）处理的。 默认的故障转移控制器是自动工作的，使用ZooKeeper的leader选举机制（leader election）以确保同一时刻只有一个主资源管理器。不同于HDFS高可用性（详见 3.2.5节）的实现，故障转移控制器不必是一个独立的进程，为配置方便，默认情况 下嵌人在资源管理器中。故障转移也可以配置为手动处理，但不建议这样。

为应对资源管理器的故障转移，必须对客户和节点管理器进行配置，因为他们可 能是在和两个资源管理器打交道。客户和节点管理器以轮询（roimd-mbin）方式试图 连接每一个资源管理器，直到找到主资源管理器。如果主资源管理器故障，他们 将再次尝试直到备份资源管理器变成主机。

##### 7.3 shuffle 和排序

MapReduce确保每个reducer的输入都是按键排序的。系统执行排序、将map输 出作为输人传给reducer的过程称为0在此，我们将学习shuffle是如何工 作的，因为它有助于我们理解工作机制（如果需要优化MapReduce程序）。shuffle

属于不断被优化和改进的代码库的一部分，因此下面的描述有必要隐藏一些细节 （也可能随时间而改变，目前是0.20版本）。从许多方面来看，shuffle是MapReduce的 “心脏”，是奇迹发生的地方。

###### 7.3.1 map 端

map函数开始产生输出时，并不是简单地将它写到磁盘。这个过程更复杂，它利 用缓冲的方式写到内存并出于效率的考虑进行预排序。图7-4展示了这个过程。

每个map任务都有一个环形内存缓冲区用于存储任务输出。在默认情况下，缓冲区 盼大小为100MB，这个值可以通过改变mapreduce. task .io .sort .mb属性来调整。一旦缓冲 内容达到阈值（mapreduce.map.sort.spill.percent，默认为 0.80，或 80%）,—

①事实上，shuffle这个说法并不准确。因为在某些语境中，它只代表reduce任务获取map输出 的这部分过程。在这一小节，将其理解为从map产生输出到reduce消化输人的整个过程。

个后台线程便开始把内容溢出(spill)到磁盘。在溢出写到磁盘过程中，map输出继 续写到缓冲区，但如果在此期间缓冲区被填满，map会被阻塞直到写磁盘过程完 成。溢出写过程按轮询方式将缓冲区中的内容写到mapreduce.cluster, local.dir属性在作业特定子目录下指定的目录中。

Copy    "Sort”    Reduce

phase    phase    phase

reduce task

map task

fetch

buffer in memory

merge

output

mixture of in-memory and on-disk data

Other reduces

Other maps

reduce



![img](Hadoop43010757_2cdb48_2d8748-110.jpg)



7-4. MapReduce 的 shuffle 和排序

在写磁盘之前，线程首先根据数据最终要传的reducer把数据划分成相应的分区 (partition)。在每个分区中，后台线程按键进行内存中排序，如果有一个combiner 函数，它就在排序后的输出上运行。运行combiner函数使得map输出结果更紧 凑，因此减少写到磁盘的数据和传递给reducer的数据。

每次内存缓冲区达到溢出阈值，就会新建一个溢出文件(spill file)，因此在map任

务写完其最后一个输出记录之后，会有几个溢出文件。在任务完成之前，溢出文 件被合并成一个已分区且已排序的输出文件。配置属性mapreduce.task.io. sort.factor控制着一次最多能合并多少流，默认值是10。

如果至少存在3个溢出文件(通过mapreduce.map.combine.minspills属性设 置)时，则combiner就会在输出文件写到磁盘之前再次运行。前面曾讲过， combiner可以在输入上反复运行，但并不影响最终结果。如果只有1或2个溢出 文件，那么由于map输出规模减少，因而不值得调用combiner带来的开销，因此 不会为该map输出再次运行combiner。

在将压缩map输出写到磁盘的过程中对它进行压缩往往是个很好的主意， 样会写磁盘的速度更快，节约磁盘空间，并且减少传给reducer的数据量。 情况下，输出是不压缩的，但只要将mapreduce.map.output, compress

为这



在默认

设置为



true ,就可以轻松启用此功能。使用的压缩库由mapreduce.map.output, compress.codec指定，要想进一步了解压缩格式，请参见5.2节。

reducer通过HTTP得到输出文件的分区。用于文件分区的工作线程的数量由任务 的mapreduce.shuffle.max.threads属性控制，此设置针对的是每一个节点管 理器，而不是针对每个map任务。默认值0将最大线程数设置为机器中处理器数 量的两倍。

###### 7.3.2 reduce 端

现在转到处理过程的reduce部分。map输出文件位于运行map任务的tasktracker 的本地磁盘（注意，尽管map输出经常写到map tasktracker的本地磁盘，但reduce 输出并不这样），现在，tasktracker需要为分区文件运行reduce任务。并且， reduce任务需要集群上若干个map任务的map输出作为其特殊的分区文件。每个 map任务的完成时间可能不同，因此在每个任务完成时，reduce任务就开始复制 其输出。这就是reduce任务的复制阶段。reduce任务有少量复制线程，因此能够 并行取得map输出。默认值是5个线程，但这个默认值可以修改设置mapreduce. reduce.shuffle, parallelcopies 属性即可。

![img](Hadoop43010757_2cdb48_2d8748-112.jpg)



reducer如何知道要从哪台机器取得map输出呢？

map任务成功完成后，它们会使用心跳机制通知它们的application master。因 此，对于指定作业，application master知道map输出和主机位置之间的映射关 系。reducer中的一个线程定期询问master以便获取map输出主机的位置，直 到获得所有输出位置。

由于第一个reducer可能失败，因此主机并没有在第一个reducer检索到map输 出时就立即从磁盘上删除它们。相反，主机会等待，直到application master告知它 删除map输出，这是作业完成后执行的。

如果map输出相当小，会被复制到reduce任务JVM的内存（缓冲区大小由 mapreduce. reduce, shuffle, input .buffer, percent 属性控制，指定用于此 用途的堆空间的百分比），否则，map输出被复制到磁盘。一旦内存缓冲区达到阈

值大小(由 mapreduce.reduce.shuffle.merge.percent 决定)或达到 map 输出

阈值（由 mapreduce.reduce.merge.inmem.threshold 控制），则合并后溢出写 到磁盘中。如果指定combinei*，则在合并期间运行它以降低写人硬盘的数据量。

随着磁盘上副本增多，后台线程会将它们合并为更大的、排好序的文件。这会为 后面的合并节省一些时间。注意，为了合并，压缩的map输出（通过map任务）都

必须在内存中被解压缩。

复制完所有map输出后，reduce任务进入排序阶段（更恰当的说法是合并阶段，因 为排序是在map端进行的），这个阶段将合并map输出，维持其顺序排序。这是 循环进行的。比如，如果有50个map输出，而合并因子是10（10为默认设置，由 mapreduce.task.io.sort.factor属性设置，与map的合并类似），合并将进行5趟。每 趟将10个文件合并成一个文件，因此最后有5个中间文件。

在最后阶段，即reduce阶段，直接把数据输入reduce函数，从而省略了一次磁盘 往返行程，并没有将这5个文件合并成一个已排序的文件作为最后一趟。最后的 合并可以来自内存和磁盘片段。

![img](Hadoop43010757_2cdb48_2d8748-113.jpg)



每趟合并的文件数实际上比示例中展示有所不同。目标是合并最小数量的文件 以便满足最后一趟的合并系数。因此如果有40个文件，我们不会在四趟中每 趟合并10个文件从而得到4个文件。相反，第一趟只合并4个文件，随后的 三趟合并完整的10个文件。在最后一趟中，4个已合并的文件和余下的6个 （未合并的）文件合计10个文件。该过程如图7-5所述。

round 1

round 2

—H

势辦綱s務

round 5

round 3

round 4

图7-5.通过合并因子10有效地合并40个文件片段



注意，这并没有改变合并次数，它只是一个优化措施，目的是尽量减少写到磁 盘的数据量，因为最后一趟总是直接合并到reduce。

在reduce阶段，对已排序输出中的每个键调用reduce函数。此阶段的输出直接写 到输出文件系统，一般为HDFS。如果采用HDFS，由于节点管理器也运行数据节 点，所以第一个块复本将被写到本地磁盘。

###### 7.3.3配置调优

现在我们已经有比较好的基础来理解如何调优shuffle过程来提高MapReduce性 能。表7-1和表7-2总结了相关设置和默认值，这些设置以作业为单位（除非有特 别说明），默认值适用于常规作业。

表7-1. map端的调优属性

属性名称    类型    默认值    说明

mapreduce.task.io.    int    100    排序 map 输出时所使用

S°rt-mb    的内存缓冲区的大小，

以兆字节为单位

| mapreduce.map.sort. spill.percent                            | float      | 0.80                                          | map输出内存缓冲和用 来开始磁盘溢出写过程 的记录边界索引，这两 者使用比例的阈值 |
| ------------------------------------------------------------ | ---------- | --------------------------------------------- | ------------------------------------------------------------ |
| mapreduce.task.io. sort.factor                               | int        | 10                                            | 排序文件时，一次最多 合并的流数。这个属性 也在reduce中使用。将 此值增加到100是很常 见的 |
| mapreduce.map.combine, minspills                             | int        | 3                                             | 运行combiner所需的最少溢出文件数（如果已指 定 combiner）     |
| mapreduce.map.output, compress                               | Boolean    | false                                         | 是否乐缩map输出                                              |
| mapreduce.map.output, compress.codec                         | Class name | org.apache, hadoop.io. compress. DefaultCodec | 用于map输出的压缩编 解码器    ，                             |
| mapreduce.shuffle.max• threads                               | int        | 0                                             | 每个节点管理器的工作 线程数，用于将map输 出至reducer。这是集痺 范围的设置，不能由单 个作业设置。0表示使 用Netty默认值，即两 倍于可用的处理器数 |
| 总的原则是给shuffle过程尽量多提供内存空间。然而，有一个平衡问题，也就是 要确保map函数和reduce函数能得到足够的内存来运行。这就是为什么写map函 数和reduce函数时尽量少用内存的原因，它们不应该无限使用内存（例如，应避免 |            |                                               |                                                              |

在map中堆积数据）。

运行map任务和reduce任务的JVM，其内存大小由mapred.child. java.opts 属性设置。任务节点上的内存应该尽可能设置的大些，10.3.3节讨论YARN和 MapReduce中的内存设置时要讲到需要考虑哪些约束条件。

在map端，可以通过避免多次溢出写磁盘来获得最佳性能；一次是最佳的情况。 如果能估算map输出大小，就可以合理地设置mapreduce.task. io.sort.*属

性来尽可能减少溢出写的次数。具体而言，如果可以，就要增加

mapreduce.task.io.sort.mb 的值。MapReduce 计数器（"SPILLED RECORDS",

参见9.1节“计数器”）计算在作业运行整个阶段中溢出写磁盘的记录数，这对于 调优很有帮助。注意，这个计数器包括map和reduce两端的溢出写。

在reduce端，中间数据全部驻留在内存时，就能获得最佳性能。在默认情况下， 这是不可能发生的，因为所有内存一般都预留给reduce函数。但如果reduce函数 的内存需求不大，把 mapreduce.reduce.merge, inmem.threshold 设置为 0，把 mapreduce. reduce, input .buffer .percent 设置为 1.0（或一个更低的值，详见表 7-2）就可以提升性能。

表7-2. reduce端的调优属性

| 属性名称mapreduce.reduce, shuffle.parallelcopies | 类型int | 默认值5 | 描述用于把map输出复制到reducer的线程数                       |
| ------------------------------------------------ | ------- | ------- | ------------------------------------------------------------ |
| mapreduce.reduce.shuffle.maxfetchfailure s       | int     | 10      | 在声明失败之前，reducer获取一个map输 出所花的最大时间        |
| mapreduce.task.io.sort.factor                    | int     | 10      | 排序文件时一次最多合并的流的数量。这个 属性也在map端使用     |
| mapreduce.reduce, shuffle.input.buffer, percent  | float   | 0.70    | 在shuffle的复制阶段，分配给map输出的 缓冲区占堆空间的百分比  |
| mapreduce.reduce, shuffle.merge, percent         | float   | 0.66    | map 输出缓冲区（由 mapred. job. shuffle. input.buffer.percent定义）的阈值使用 比例，用于启动合并输出和磁盘溢出写的过程 |
| mapreduce.reduce, merge.in mem.threshold         | int     | 1000    | 启动合并输出和磁盘溢出写过程的map输出 的阈值数。0或更小的数意味着没有阈值限 |

制，溢出写行为由mapreduce.reduce, shuffle.merge.percent 单独控制

续表



mapreduce.reduce.in put.buffer.percent



float



0.0



在reduce过程中，在内存中保存map输出的



空间占整个堆空间的比例。reduce阶段开始 时，内存中的map输出大小不能大于这个值。 默认情况下，在reduce任务开始之前，所有 map输出都合并到磁盘上，以便为reducer提 供尽可能多的内存。然而，如果reducer需要 的内存较少，可以增加此值来最小化访问磁



盘的次数

2008年4月，Hadoop在通用TB字节排序基准测试中获胜（详见1.6节的介绍）， 它使用了一个优化方法，即将中间数据保存在reduce端的内存中。

更常见的情况是，Hadoop使用默认为4 KB的缓冲区，这是很低的，因此应该在集 群中增加这个值（通过设置io.Hle.tn^fer.size，详见10.3.5节）。

##### 7.4任务的执行

在7.1节介绍剖析MapRedue作业运行机制时，我们结合整个作业的背景知道了 MapReduce系统是如何执行任务的。在本小节，我们将了解MapReduce用户对任 务执行的更多的控制。

###### 7.4.1任务执行环境

Hadoop为map任务或reduce任务提供运行环境相关信息。例如，map任务可以 知道它处理的文件的名称（参见8.2.1节），map任务或reduce任务可以得知任务的

尝试次数。表7-3中的属性可以从作业的配置信息中获得，在老版本的 MapReduce API中通过为Mapper或Reducer提供一个configure（）方法实现（其 中，配置信息作为参数进行传递），便可获得这一信息。在新版本的API中，这些 属性可以从传递给Mapper或Reducer的所有方法的相关对象中获取。

表7-3.任务执行环境的属性

| 翼性名称                 | IS型    | 1明                  | £例                                   |
| ------------------------ | ------- | -------------------- | ------------------------------------- |
| mapreduce.job.id         | string  | 作业ID①              | job 200811201130.0004                 |
| mapreduce.task.id        | string  | 任务ID               | task一200811201130一 00O4jn—000003    |
| mapreduce.task.attemp.id | string  | 任务尝试ID           | attempt_2008112011300004jn _000003一0 |
| mapreduce.task.partition | int     | 作业中任务 的索引    | 3                                     |
| mapreduce.task.ismap     | boolean | 此任务是否是 map任务 | true                                  |

Streaming环境变量

Hadoop设置作业配置参数作为Streaming程序的环境变量。但它用下划线来代替 非字母数字的符号，以确保名称的合法性。下面这个Python表达式解释了如何用 Python Streaming 脚本来检索 mapreduce. job. id 属性的值。

os.environ[Hmapreduce job id"]

也可以应用Streaming启动程序的-cmdenv选项，来设置MapReduce所启动的

Streaming进程的环境变量（一次设置一个变量）。比如，下面的语句设置了

##### MAGIC_PARAMETER 环境变量：

-cmdenv MAGIC PARAMETER=abracadabra

###### 7.4.2推测执行

MapReduce模型将作业分解成任务，然后并行地运行任务以使作业的整体执行时 间少于各个任务顺序执行的时间。这使作业执行时间对运行缓慢的任务很敏感， 因为只运行一个缓慢的任务会使整个作业所用的时间远远长于执行其他任务的时 间。当一个作业由几百或几千个任务组成时，可能出现少数“拖后腿”的任务，这 是很常见的。

任务执行缓慢可能有多种原因，包括硬件老化或软件配置错误，但是，检测具体 原因很困难，因为任务总能够成功完成，尽管比预计执行时间长。Hadoop不会尝 试诊断或修复执行慢的任务，相反，在一个任务运行比预期慢的时候，它会尽量

①格式描述参见6.5.2节的补充内容“作业、任务和任务任务尝试ID”。

检测，并启动另一个相同的任务作为备份。这就是所谓的任务的“推测执行 (speculative execution)。

必须认识到一点：如果同时启动两个重复的任务，它们会互相竞争，导致推测执 行无法工作。这对集群资源是一种浪费。相反，调度器跟踪作业中所有相同类型 (map和reduce)任务的进度，并且仅仅启动运行速度明显低于平均水平的那一小部 分任务的推测副本。一个任务成功完成后，任何正在运行的重复任务都将被中 止，因为已经不再需要它们了。因此，如果原任务在推测任务前完成，推测任务 就会被终止：同样，如果推测任务先完成，那么原任务就会被中止。

推测执行是一种优化措施，它并不能使作业的运行更可靠。如果有一些软件缺陷 会造成任务挂起或运行速度减慢，依靠推测执行来避免这些问题显然是不明智 的，并且不能可靠地运行，因为相同的软件缺陷可能会影响推测式任务。应该修 复软件缺陷，使任务不会挂起或运行速度减慢。

在默认情况下，推测执行是启用的。可以基于集群或基于每个作业，单独为map 任务和reduce任务启用或禁用该功能。相关的属性如表7-4所示。

表7-4.推测执行的属性

| 属性名称mapreduce.map.speculative                            | 类型boolean | 默认值true                                                   | 描述如果任务运行变慢，该属性 决定着是否要启动map任务 的另外一个实例 |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| mapreduce.reduce, speculative                                | boolean     | true娜                                                       | 如果任务运行变慢，该属性 决定着是否要启动reduce任 务的另一个实例 |
| Yarn.app.mapreduce. am.job.speculator, class                 | Class       | Org.apache.hadoop. map reduce.v2.app.specu late.DefaultSpeculator | Speculator类实现推测执 行策略(只针对MapReduce2)              |
| Yarn.app.mapreduce. am.job.estimator, class                  | Class       | Org.apache.hadoop.map reduce. v2. app. specxil ate.LegacyTaskRuntimeEstimator | Speculator实例使用的 TaskRuntimeEstimator 的实现，提供任务运行时间的估 计值(只针对MapReduce 2 ) |
| 为什么会想到关闭推测执行？推测执行的目的是减少作业执行时间，但这是以集 群效率为代价的。在一个繁忙的集群中，推测执行会减少整体的吞吐量，因为冗 |             |                                                              |                                                              |

余任务的执行时会减少作业的执行时间。因此，一些集群管理员倾向于在集群上 关闭此选项，而让用户根据个别作业需要而开启该功能。Hadoop老版本尤其如 此，因为在调度推测任务时，会过度使用推测执行方式。

对于reduce任务，关闭推测执行是有益的，因为任意重复的reduce任务都必须将 取得map输出作为最先的任务，这可能会大幅度地增加集群上的网络传输。

关闭推测执行的另一种情况是为了非幂等(mmidempotent)任务。然而在很多情况 下，将任务写成幂等的并使用OutputCommitter来提升任务成功时输出到最后位置的 速度，这是可行的。详情将在下一节介绍。

###### 7.4.3 关于 OutputCommitters

Hadoop MapReduce使用一个提交协议来确保作业和任务都完全成功或失败。这个 行为通过对作业使用OutputCommitter来实现，在老版本MapReduce API中通 过调用］obConf 的 setOut put Committer ()或配置中的 mapred. output, committer, class 来设置。在新版本的 MapReduce API 中，OutputCommitter 由 OutputFormat 通过它的getOutputCommitter()方法确定。默认值为FileOutputCommitter,这对基 于文件的MapReduce是适合的。可以定制已有的OutputCommitter,或者在需要时 还可以写一个新的实现以完成对作业或任务的特别设置或清理。

OutputCommitter的API如下所示(在新旧版本中的MapReduce API中):

public abstract class OutputCommitter {

public abstract void setup3ob(DobContext jobContext) throws IOException; public void commitDob(JobContext jobContext) throws IOException { } public void abortDob(JobContext jobContext， DobStatus.State state)

throws IOException { }

public abstract void setupTask(TaskAttemptContext taskContext) throws IOException;

public abstract boolean needsTaskCommit(TaskAttemptContext taskContext) throws IOException;

public abstract void commitTask(TaskAttemptContext taskContext) throws IOException;

public abstract void abortTask(TaskAttemptContext taskContext) throws IOException;

}

}

setup]ob()方法在作业运行前调用，通常用来执行初始化操作。当OutputCommitter 设为FileOutputCommitter时，该方法创建最终的输出目录${mapreduce.output.

204 第7章

fileoutputformat.outputdir},并且为任务输出创建一个临时的工作空间

一temporary，作为最终输出目录的子目录。

如果作业成功，就调用canmit]Ob()方法，在默认的基于文件的实现中，它用于删 除临时的工作空间并在输出目录中创建一个名为^SUCCESS的隐藏的标志文件，以 此告知文件系统的客户端该作业成功完成了。如果作业不成功，就通过状态对象 调用abort]ob()，意味着该作业是否失败或终止(例如由用户终止)。在默认的实现 中，将删除作业的临时工作空间。

在任务级别上的操作与此类似。在任务执行之前先调用setupTask()方法，默认的 实现不做任何事情，因为针对任务输出命名的临时目录在写任务输出的时候被 创建。

任务的提交阶段是可选的，并通过从needsTaskCommitO返回的false值关闭它。 这使得执行框架不必为任务运行分布提交协议，也不需要commitTask()或者 abortTask()。当一个任务没有写任何输出时，FileOutputCommitter将跳过提交

阶段。

如果任务成功，就调用canmitTaskO,在默认的实现中它将临时的任务输出目录

(它的名字中有任务尝试的ID，以此避免任务尝试间的冲突)移动到最后的输出路径

${mapreduce.output.fil eoutputformat.outputdir}。否则，执行框架调用 abortTask()，它负责删除临时的任务输出目录。

执行框架保证特定任务在有多次任务尝试的情况下，只有一个任务会被提交，其 他的则被取消。这种情况是可能出现的，因为第一次尝试出于某个原因而失败(这 种情况下将被取消)，提交的是稍后成功的尝试。另一种情况是如果两个任务尝试 作为推测副本同时运行，则提交先完成的，而另一个被取消。

任务附属文件

对于map任务和reduce任务的输出，常用的写方式是通过OutputCollector来收集键 值对。有一些应用需要比单个键-值对模式更灵活的方式，因此直接将m叩苗 reduce任务的输出文件写到分布式文件系统中，如HDFS。还有其他方法用于产4 多个输出，详情参见8.3.3节。

注意，要确保同一个任务的多个实例不向同一个文件进行写操作。如前所述， OutputCommitter协议解决了该问题。如果应用程序将附属文件导入其任务的工作

目录中，那么成功完成的这些任务就会将其附属文件自动推送到输出目录，而失 败的任务，其附属文件则被删除。

任务通过从作业配置文件中査询mapreduce.task.out put.dir属性值找到其工作目 录。另一种方法，使用Java API的MapReduce程序可以调用FileOtrt put Format上的 getWorkOutputPath()静态方法获取描述工作目录的Path对象。执行框架在执行任 务之前首先创建工作目录，因此不需要我们创建。

举一个简单的例子，假设有一个程序用来转换图像文件的格式。一种实现方法是 用一个只有map任务的作业，其中每个map指定一组要转换的图像(可以使用 NlinelnputFormat,详情参见8.2.2节)。如果map任务把转换后的图像写到其 工作目录，那么在任务成功完成之后，这些图像会被传到输出目录。
