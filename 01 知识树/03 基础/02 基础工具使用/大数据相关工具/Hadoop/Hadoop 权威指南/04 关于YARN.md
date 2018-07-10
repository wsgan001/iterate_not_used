### 关于YARN

Apache YARN(Yet Another Resource Negotiator 的缩写)是 Hadoop 的集群资源管理 系统。YARN被引入Hadoop 2，最初是为了改善MapReduce的实现，但它具有足 够的通用性，同样可以支持其他的分布式计算模式。

YARN提供请求和使用集群资源的API,但这些API很少直接用于用户代码。相 反，用户代码中用的是分布式计算框架提供的更高层API，这些API建立在 YARN之上且向用户隐藏了资源管理细节。图4-1对此进行了描述，一些分布式计 算框架(MapReduce, Spark等等)作为YARN应用运行在集群计算层(YARN)和集群 存储层(HDFS和HBase)上。

Application

Compute

Storage

YARN

HDFSandHBase

图4-1. YARN应用

还有一层应用是建立在图4-1所示的框架之上。如Pig, Hive和Crunch都是运行 在MapReduce, Spark或Tez(或三个都可)之上的处理框架，它们不和YARN直接 打交道。

本章将介绍YARN的特性，为理解后续第IV部分关于Hadoop分布式处理框架的 相关内容提供基础。

##### 4.1剖析丫ARN应用运行机制

yarn通过两类长期运行的守护进程提供自己的核心服务：管理集群上资源使用 的资源管理器（rewwrce manager）、运行在集群中所有节点上且能够启动和监控容 器（cC/«er）的节点管理器⑽必manager）0容器用于执行特定应用程序的进程， 每个容器都有资源限制（内存、CPU等）。一个容器可以是一个Unix进程，也可以 是一个Linux cgroup,取决于YARN的配置（详见10.3.3节）。图4-2描述了 YARN 是如何运行一个应用的。

client node



ResourceManaqer



1: submit I YARN [

application I resource manager node

2a: start container

r—

2b: launch



3: allocate resources (heartbeat)

Container I

Application

process



4a: start container



node manager node



4b: launch



J Container

node manager node



图4-2. YARN应用的运行机制

为了在YARN上运行一个应用，首先，客户端联系资源管理器，要求它运行一个 application wtmer进程（图4-2中的步骤1）。然后，资源管理器找到一个能够在容

器中启动application master的节点管理器（步骤2a和2b）y。准确地说，application master 一旦运行起来后能做些什么依赖于应用本身。有可能是在所处的容器中简 单地运行一个计算，并将结果返回给客户端；或是向资源管理器请求更多的容器 （步骤3），以用于运行一个分布式计算（步骤4a和4b）。后者是MapReduce YARN 应用所做的事情，将在7.1节中详细介绍。

注意，从图4-2可看出，YARN本身不会为应用的各部分（客户端、master和进程） 彼此间通信提供任何手段。大多数重要的YARN应用使用某种形式的远程通信机 制（例如Hadoop的RPC层）来向客户端传递状态更新和返回结果，但是这些通信机 制都是专属于各应用的。

###### 4.1.1资源请求

YARN有一个灵活的资源请求模型。当请求多个容器时，可以指定每个容器需要 的计算机资源数量（内存和CPU）,还可以指定对容器的本地限制要求。

本地化对于确保分布式数据处理算法高效使用集群带宽®非常重要，因此，yarn 允许一个应用为所申请的容器指定本地限制。本地限制可用于申请位于指定节点 或机架，或集群中任何位置（机架外）的容器。

有时本地限制无法被满足，这种情况下要么不分配资源，或者可选择放松限制。 例如，一个节点由于已经运行了别的容器而无法再启动新的容器，这时如果有应 用请求该节点，则YARN将尝试在同一机架中的其他节点上启动一个容器，如果 还不行，则会尝试集群中的任何一个节点。

通常情况下，当启动一个容器用于处理HDFS数据块（为了在MapReduce中运行一 个map任务）时，应用将会向这样的节点申请容器：存储该数据块三个复本的节 点，或是存储这些复本的机架中的一个节点。如果都申请失败，则申请集群中的 任意节点。

YARN应用可以在运行中的任意时刻提出资源申请。例如，可以在最开始提出所 有的请求，或者为了满足不断变化的应用需要，采取更为动态的方式在需要更多 资源时提出请求。

①    客户端也有可能启动application master,可能在集群外，或在与客户端相同的JVM中。这被称 作“非托管的 application master” （unmanagedapplication master、。

②    关于该议题的详细讨论，可以参见2.4节和3.6节。

Spark采用了上述第一种方式，在集群上启动固定数量的执行器（详见19.6节）。另 一方面，MapReduce则分两步走，在最开始时申请map任务容器，reduce任务容 器的启用则放在后期。同样，如果任何任务出现失败，将会另外申请容器以重新 运行失败的任务。

###### 4.1.2应用生命期

YARN应用的生命期差异性很大:有几秒的短期应用，也有连续运行几天甚至几个 月的长期应用。与其关注应用运行多长时间，不如按照应用到用户运行的作业之 间的映射关系对应用进行分类更有意义。最简单的模型是一个用户作业对应一个 应用，这也是MapReduce采取的方式。

第二种模型是，作业的每个工作流或每个用户对话（可能并无关联性）对应一个应 用。这种方法要比第一种情况效率更高，因为容器可以在作业之间重用，并且有 可能缓存作业之间的中间数据。Spark采取的是这种模型。

第三种模型是，多个用户共享一个长期运行的应用。这种应用通常是作为一种协 调者的角色在运行。例如，Apache Slider（网址为

有一个长期运行的application master，主要用于启动集群上的其他应用。 Impala（详见17.4.3节）也使用这种模型提供了一个代理应用，Impala守护进程通过 该代理请求集群资源。由于避免了启动新application master带来的开销，一个总 是开启（always on）的application master意味着用户将获得非常低延迟的查询响

应。①

###### 4.1.3构建丫ARN应用

从无到有编写一个yarn应用是一件相当复杂的事，但在很多情况下不必这样。 有很多现成的应用，在符合要求的情况下通常可以直接使用。例如，如果你有兴 趣运行一个作业的有向无环图，那么Spark或Tez就很合适；如果对流处理有兴 趣，Spark、Samza或Storm能提供帮助。⑦

许多项目都简化了构建YARN应用的过程。先前提到的Apache Slider,使得在 YARN上运行现有的分布式应用成为可能。对于一个应用（如HBase）来说，用户可

①    低延迟 application master 代码可以参见 Llama 项目，网址为 <http://cloudera>. github.io/llatna/。

②    所有这些项目都是Apache Software基金会支持的。

以独立干其他用户在集群上运行自己的实例，则意味着不同的用户能够运行同一 应用的不同版本。Slidei•提供了控制手段，可以修改应用运行所在的节点的数量， 也可以暂停和恢复应用的运行。

Apache    incubator.apache.org/）与 Slider 类似，但额外提供了一个简

单的编程模型，用于开发YARN上的分布式应用。Twill允许将集群进程定义为 Java Runnable的扩展，然后在集群上的YARN容器中运行它们。Twill同样为 实时日志（来自于runnable类的日志事件以流的方式返回客户端）和命令消息提供支 持。

当遇到以上方法都不适用的情况时，例如一个应用有复杂的调度需求，那么作为 YARN项目自身一部分的distributed shell应用为如何写YARN应用做了一个示 范。该应用演示了如何使用YARN客户端API来处理客户端或application master 与YARN守护进程之间的通信。

##### 4.2 丫ARN 与 MapReduce 1 相比

有时用“MapReduce 1”来指代Hadoop初始版本（版本1及更早期版本）中的 MapReduce分布式执行框架，以区别于使用了 YARN（Hadoop 2及以后的版本）的 MapReduce 20

參

■jZ一新旧版本MapReduce API之间的区别不同于MapReduce 1执行框架和 MapReduce 2执行框架的区别。API具有面向用户的、客户端的特性，并且决 定着用户怎样写MapReduce程序（见附录D）;而执行机制只是运行

'B MapReduce程序的不同途径而已。新旧版本API在MapReduce 1和2运行的四 种组合都是支持的。

MapReduce 1中，有两类守护进程控制着作业执行过程：一个jobtracker及一个歲 多个tasktracker0 jobtracker通过调度tasktracker上运行的任务来协调所有运行在 系统上的作业。tasktracker在运行任务的同时将运行进度报告发送给jobtracker, jobtracker由此记录每项作业任务的整体进度情况。如果其中一个任务失败， jobtracker可以在另一个tasktracker节点上重新调度该任务。

MapReduce 1中，jobtracker同时负责作业调度（将任务与tasktracker匹配）和任务 进度监控（跟踪任务、重启失败或迟缓的任务：记录任务流水，如维护计数器的计 数）。相比之下，YARN中，这些职责是由不同的实体担负的:它们分别是资源管理

器和application master（每个MapReduce作业一个）。jobtracker也负责存储已完成

作业的作业历史，但是也可以运行一个作业历史服务器作为一个独立的守护进程 来取代jobtracker。在YARN中，与之等价的角色是时间轴服务器（timeline server）,它主要用于存储应用历史。®

YARN中与tasktrackei*等价的角色是节点管理器。表4-1中对两者之间的映射关系 进行了总结。    ；*

| 表4-1. MapReduce 1和Yarn在组成上的比较 |                                              |
| -------------------------------------- | -------------------------------------------- |
| MapReduce 1                            | YARN                                         |
| Jobtracker                             | 资源管理器、application master、时间轴服务器 |
| Tasktracker•                           | 节点管理器                                   |
| Slot                                   | 容器                                         |

YARN的很多设计是为了解决MapReduce 1的局限性。使用YARN的好处包括以 下几方面。

可扩展性(Scalability) 与MapReduce 1相比，YARN可以在更大规模的集群上运行。当节点数达到 4000,任务数达到40000®时，MapReduce 1会遇到可扩展性瓶颈，瓶颈源自于 jobtracker必须同时管理作业和任务这样一个事实。YARN利用其资源管理器和 application master分离的架构优点克服了这个局限性，可以扩展到面向将近10000 个节点和100000个任务。

与jobtracker相比，一个应用的每个实例（这里指一个MapReduce作业）都对应一个 专门的application master，该管理进程（master）在应用的持续期间运行。这个模型 实际上与初始的Google MapReduce论文中所描述的更接近。论文中描述了如何 启动一个管理（master）进程以协调运行在一系列工作（worker）上的map和reduce 任务。

①    Hadoop 2.5.1中，YARN时间轴服务器依旧不存储MapReduce作业历史，因此仍需要 MapReduce作业历史服务器守护进程（详见10.2节）。

②    参见Arun C. Murthy于2011年2月14日发表的“下一代Apache Hadoop MaReduce”，网址 为 http://bit」y/next_gen mapreduce。

可用性（Availability）

当服务守护进程失败时，通过为另一个守护进程复制接管工作所需的状态以便其 继续提供服务，从而可以获得高可用性（HA, High availability）。然而，jobtracker

内存中大量快速变化的复杂状态（例如，每个任务状态每几秒会更新一次）使得改进 jobtracker服务获得高可用性非常困难。

由于YARN中jobtracker在资源管理器和application master之间进行了职责划分，

高可用的服务随之成为一个分而治之问题：先为资源管理器提供高可用性，再为 YARN应用（针对每个应用）提供高可用性。实际上，对于资源管理器和application master, Hadoop 2都支持MapReduce作业的高可用性。YARN中的失败恢复将在 7.2节中详细讨论。

利用率(Utilization)

MapReduce 1中，每个tasktracker都配置有若干固定长度的slot,这些slot是静态 分配的，在配置的时候就被划分为map slot和reduce slot。一个map slot仅能用于 运行一个map任务，同样，一个reduce slot仅能用于运行一个reduce任务。

YARN中，一个节点管理器管理一个资源池，而不是指定的固定数目的slot。 YARN上运行的MapReduce不会出现由于集群中仅有map slot可用导致reduce任 务必须等待的情况，而MapReduce 1则会有这样的问题。如果能够获得运行任务 的资源，那么应用就会正常进行。

更进一步，YARN中的资源是精细化管理的，这样一个应用能够按需请求资源， 而不是请求一个不可分割的、对于特定的任务而言可能会太大（浪费资源）或太小 （可能会导致失败）的slot。

多租户(Multitenancy) 在某种程度上，可以说YARN的最大优点在于向MapReduce以外的其他类型的分 布式应用开放了 Hadoop。MapReduce仅仅是许多YARN应用中的一个。

用户甚至可以在同一个YARN集群上运行不同版本的MapReduce,这使得升级 MapReduce的过程更好管理。（注意，MapReduce的一些部件，例如作业历史服务 器和shuffle句柄，和YARN自身一样，仍然需要在集群范围内升级。） 由于Hadoop 2应用广泛且是最新的稳定版本，本书余下的章节中，除非特意声

明，MapReduce 一般指 MapReduce 2。第 7 章详细探讨了 YARN 中 MapReduce 的 工作机制。

##### 4.3 丫ARN中的调度

理想情况下，yarn应用发出的资源请求应该立刻给予满足。然而现实中资源是 有限的，在一个繁忙的集群上，一个应用经常需要等待才能得到所需的资源。 yarn调度器的工作就是根据既定策略为应用分配资源。调度通常是一个难题， 并且没有一个所谓“最好”的策略，这也是为什么YARN提供了多种调度器和可 配置策略供我们选择的原因。接下来我们将探讨这个问题。

###### 4.3.1 •调度选项

YARN中有三种调度器可用：FIFO调度器(FIFO Scheduler),容量调度器(Capacity Scheduler)和公平调度器(Fair Scheduler)o FIFO调度器将应用放置在一个队列中， 然后按照提交的顺序(先进先出)运行应用。首先为队列中第一个应用的请求分配资 源，第一个应用的请求被满足后再依次为队列中下一个应用服务。

FIFO调度器的优点是，简单易懂，不需要任何配置，但是不适合共享集群。大的 应用会占用集群中的所有资源，所以每个应用必须等待直到轮到自己运行。在一 个共享集群中，更适合使用容量调度器或公平调度器。这两种调度器都允许长时 间运行的作业能及时完成，同时也允许正在进行较小临时查询的用户能够在合理 时间内得到返回结果。

图4-3描述了调度器之间的差异性，由图中可以看出，当使用FIFO调度器⑴时， 小作业一直被阻塞，直至大作业完成。

使用容量调度器时(图4-3中的ii)，一个独立的专门队列保证小作业一提交就可以 启动，由于队列容量是为那个队列中的作业所保留的，因此这种策略是以整个 集群的利用率为代价的。这意味着与使用FIFO调度器相比，大作业执行的时间

要长。

i. FIFO Scheduler

utilization



time



![img](Hadoop43010757_2cdb48_2d8748-60.jpg)



job 1 job 2 submitted submitted



ii. Capacity Scheduler



submitted submitted



lit Fair Scheduler



utilization



![img](Hadoop43010757_2cdb48_2d8748-62.jpg)



![img](Hadoop43010757_2cdb48_2d8748-63.jpg)



fair share pool/queue



► time



job 1 job 2 submitted submitted

4-3.用FIFO调度器⑴、容量调度器(ii)和公平调度器㈣运行大小作业时集群的利用率

使用公平调度器时（图4-3中的iii），不需要预留一定量的资源，因为调度器会在所 有运行的作业之间动态平衡资源。第一个（大）作业启动时，它也是唯一运行的作 业，因而获得集群中所有的资源。当第二个（小）作业启动时，它被分配到集群的一 半资源，这样每个作业都能公平共享资源。

注意，从第二个作业的启动到获得公平共享资源之间会有时间滞后，因为它必须 等待第一个作业使用的容器用完并释放出资源。当小作业结束且不再申请资源 后，大作业将回去再次使用全部的集群资源。最终的效果是：既得到了较高的集 群利用率，又能保证小作业能及时完成。

图4-3对比了三种调度器的基本操作。在接下来的两个小节中，我们将讨论容量 调度器和公平调度器的高级配置选项。

###### 4.3.2容量调度器配置

容量调度器允许多个组织共享一个Hadoop集群，每个组织可以分配到全部集群资 源的一部分。每个组织被配置一个专门的队列，每个队列被配置为可以使用一定 的集群资源。队列可以进一步按层次划分，这样每个组织内的不同用户能够共享 该组织队列所分配的资源。在一个队列内，使用FIFO调度策略对应用进行调度。

正如图4-3所示，单个作业使用的资源不会超过其队列容量。然而，如果队列中 有多个作业，并且队列资源不够用了呢？这时如果仍有可用的空闲资源，那么容 量调度器可能会将空余的资源分配给队列中的作业，哪怕这会超出队列容量。$这 称为“弹性队列” （queue elasticity）。

正常操作时，容量调度器不会通过强行中止来抢占容器（container）®。因此，如果 一个队列一开始资源够用，然后随着需求增长，资源开始不够用时，那么这个队 列就只能等着其他队列释放容器资源。缓解这种情况的方法是，为队列设置一个 最大容量限制，这样这个队列就不会过多侵占其他队列的容量了。当然，这样 做是以牺牲队列弹性为代价的，因此需要在不断尝试和失败中找到一个合理的 折中。

①    如果属性 yarn.scheduler.capacity.<queue-path>.user-limit-factor 设置为大于

1（默认值），那么一个作业可以使用超过其队列容量的资源。

②    然而，容量调度器可以执行“工作保留”式的抢占，此时资源管理器会要求应用返回容器以平 衡容量资源。

假设一个队列的层次结构如下:

root

|—prod 1—dev

I— eng 1— science

范例4-1是一个基于上述队列层次的容量调度器配置文件，文件名为capacity-scheduler.xml。在root队列下定义两个队列：prod和dev,分别占40%和60%的

容量。需要注意的是，对特定队列进行配置时，是通过以下形式的配置属性

yarn .scheduler* capacity. <gt/et/e-p£7th>.<st//?-property〉进行设置的，其 中，<qweue-path>表示队列的层次路径（用圆点隔开），例如root.prod。

范例4-1.容量调度器的基本配置文件

<?xml version=,,1.0,,?>

〈configuration〉

<property>

<name>yarn.scheduler.capacity•root.queues</name> <value>prodJdev</value>

</property>

<property>

<name>yarn.scheduler.capacity.root.dev•queues</name> <value>engJscience</value>

</property>

<property>

<name>yarn.scheduler.capacity.root.prod.capacity</name> <value>40</value>

</property>

<property>

<name>yarn.scheduler.capacity.root.dev.capacity</name> <value>60</value>

</property>

<property>

<name>yarn.scheduler.capacity.root.dev.maximum-capacity</name> <value>75</value>

</property>

<property> •

<name>yarn.scheduler.capacity.root.dev.eng.capacity</name> <value>50</value>

</property>

<property>

<name>yarn.scheduler.capacity.root.dev.science.capacity</name> <value>50</value>

</property>

〈/configuration〉

可以看到，dev队列进一步被划分成eng和science两个容量相等的队列。由于 dev队列的最大容量被设置为75%，因此即使prod队列空闲，dev队列也不会占

用全部集群资源。换而言之，prod队列能即刻使用的可用资源比例总是能达到 25%。由于没有对其他队列设置最大容量限制，eng或science中的作业可能会 占用dev队列的所有容量（将近75%的集群资源），而prod队列实际则可能会占用 全部集群资源。

除了可以配置队列层次和容量，还有些设置是用来控制单个用户或应用能被分配 到的最大资源数量、同时运行的应用数量及队列的ACL认证等。关于容量调度器 配置的更多内容，请参见 http://bit_ly/capacity schedularo 队列放置 将应用放置在哪个队列中，取决于应用本身。例如，在MapReduce中，可以通过 设置属性mapreduce.job.queuename来指定要用的队列。如果队列不存在，则

在提交时会发送错误。如果不指定队列，那么应用将被放在一个名为 “default”的默认队列中。

![img](Hadoop43010757_2cdb48_2d8748-64.jpg)



对于容量调度器，队列名应该是队列层次名的最后一部分，完整的队列层次名 是不会被识别的。例如，对干上述配置范例，prod和eng是合法的队列名， 但root.dev.eng和dev.eng作为队列名是无效的。

###### 4.3.3公平调度器配置

公平调度器旨在为所有运行的应用公平分配资源。图4-3展示了同一个队列中的 应用是如何实现资源公平共享的。然而公平共享实际也可以在多个队列间工作， 后续会对此进行分析。

![img](Hadoop43010757_2cdb48_2d8748-65.jpg)



术语gwewe和pool在公平调度器的上下文中会交替使用

接下来解释资源是如何在队列之间公平共享的。想象两个用户J和A分别拥有 自己的队列（参见图4-4）。J启动一个作业，在公没有需求时J会分配到全部可用 资源；当j的作业仍在运行时S启动一个作业，一段时间后，按照我们先前看到 的方式，每个作业都用到了一半的集群资源。这时，如果S启动第二个作业且其 他作业仍在运行，那么第二个作业将和S的其他作业（这里是第一个）共享资源， 因此B的每个作业将占用四分之一的集群资源，而仍继续占用一半的集群资 源。最终的结果就是资源在用户之间实现了公平共享。

utilization

queue A (fair share)

► time

job 1 ；    job 3

submitted i    submitted

job 2

submitted

图4-4.用户队列间的公平共享

1.启用公平调度器

公平调度器的使用由属性yarn.resourcemanager.scheduler.dass的设置所决定。默 认是使用容量调度器（尽管在一些Hadoop分布式项目，如CDH中是默认使用公 平调度器），如果要使用公平调度器，需要将yarn-site.xml文件中的yarn, resourcemanager.scheduler.class 设置为公平调度器的完全限定名：org.apache, hadoop.yarn.server.resourcemanager.scheduler.fair.FairSchedulero

2.队列配置

通过一个名为fair-scheduler.xrnl的分配文件对公平调度器进行配置，该文件位于类 路径下。（可以通过设置属性yarn, scheduler, fair .allocation, file来修改文件名）。当 没有该分配文件时，公平调度器的工作策略同先前所描述的一样：每个应用放置 在一个以用户名命名的队列中，队列是在用户提交第一个应用时动态创建的。

通过分配文件可以为每个队列进行配置。这样可以对容量调度器支持的层次队列 进行配置。例如，可以像为容量调度器所做的那样，使用范例4-2所示的分配文 件定义prod和dev。

范例4-2.公平调度器的分配文件

<?xml version="1.0"?>

〈allocations〉

<defaultQueueSchedulingPolicy>fair</defaultQueueSchedulingPolicy>

〈queue name="prod">

〈weight>40</weight>

<schedulingPolicy>fifo</schedulingPolicy>

</queue>

<queue name=,,dev,,>

〈weight>60</weight〉

〈queue name="eng" />

〈queue name="science" />

</queue> <queuePlacementPolicy>

<rule name=.’specified’’ create=,,false" /> <rule name="primaryGroup" create=,,falseH/> <rule name=ndefault•’ queue="dev.eng" />

</queuePlacementPolicy>

〈/allocations〉

队列的层次使用嵌套queue元素来定义。所有的队列都是root队列的孩子，即 使实际上并没有嵌套进root queue元素里。这里把dev队列又划分成eng和

science两个队列。

队列中有权重元素，用于公平共享计算。在这个例子中，当集群资源按照40:60 的比例分配给prod和dev时，集群分配被认为是公平的。eng和science队列 没有指定权重，因此它们会被平均分配。权重并不是百分百，例子中是为了简单 起见使用了相加之和为100的两个数。也可以为prod和dev队列分别指定2和3 的权重，在效果上是一样的。

I 当设置权重时，记住要考虑默认队列和动态创建的队列(例如以用户名命名的队 列)。虽然没有在分配文件中为它们指定权重，但它们仍有值为1的权重。

每个队列可以有不同的调度策略。队列的默认调度策略可以通过顶层元素 defaultQueueSchedulingPolicy进行设置，如果省略，默认使用公平调度。尽

管名称是“公平”，公平调度器也支持队列级别的FIFO(fifo)策略，以及 Dominant Resource Fairness(drf)策略，本章稍后会对此策略进行解释。

队列的调度策略可以被该队列的schedulingPolicy元素指定的策略覆盖。在上 述例子中，由于我们希望每个生产性作业能够顺序运行且在最短可能的时间内结 束，所以prod队列使用了 FIFO调度策略。值得注意的是，在prod和dev队列 之间、eng和science队列之间及内部划分资源仍然使用了公平调度。

尽管上述的分配文件中没有展示，每个队列仍可配置最大和最小资源数量，及最大可运 行的应用的数量(更多细节可以访问http://bit_ly/fair_schedular、。最小资源数量不是一 个硬性的限制，但是调度器常用它对资源分配进行优先排序。如果两个队列的资 源都低于它们的公平共享额度，那么远低于最小资源数量的那个队列优先被分配 资源。最小资源数量也会用于接下来将介绍的抢占行为。

3.队列放置

公平调度器使用一个基于规则的系统来确定应用应该放到哪个队列。在范例4-2 中，queuePlacementPolicy元素包含了一个规则列表，每条规则会被依次尝试 直到匹配成功。第一条规则，specified,表示把应用放进所指明的队列中，如 果没有指明，或如果所指明的队列不存在，则规则不匹配，继续尝试下一条规 则。primaryGroup规则会试着把应用放在以用户的主Unix组名命名的队列中， 如果没有这样的队列，则继续尝试下一条规则而不是创建队列。Default规则是 一条兜底规则，当前述规则都不匹配时，将启用该条规则，把应用放进dev.eng 队列中。

当然，可以完全省略queuePlacementPolicy元素，此时队列放置默认遵从如下 规则：

〈queuePlacementPolicy>

<rule name=nspecified" />

<rule name="user" />

</queuePlacementPolicy>

换而言之，除非明确定义队列，否则必要时会以用户名为队列名创建队列。

另一个简单的队列放置策略是，将所有的应用放进同一个队列(default)中。这样可 以在应用之间公平共享资源，而不是在用户之间共享。策略定义等价于以下规 则：

〈queuePlacementPolicy>

<rule name="defaultH />

〈/queuePlacementPolicy〉

不使用分配文件也可以设置以上策略，通过将属性yarn.scheduler.fair.user-as-default-queue设为false,应用就会被放入default队列，而不是各个用户的队列。 另外，将属性 yam.scheduler.fair.allow-undeclared-pools 设置为 false,用户便不能 随意创建队列了。

4.抢占

在一个繁忙的集群中，当作业被提交给一个空队列时，作业不会立刻启动，直到 集群上已经运行的作业释放了资源。为了使作业从提交到执行所需的时间可预 测，公平调度器支持“抢占” (preemptions^o 所谓抢占，就是允许调度器终止那些占用资源超过了其公平共享份额的队列的容 器，这些容器资源释放后可以分配给资源数量低于应得份额的队列。注意，抢占 会降低整个集群的效率，因为被终止的containers需要重新执行。

通过将yarn.scheduler.fair.preemption设置为true,可以全面启用抢占功 能。有两个相关的抢占超时设置：一个用于最小共享share preemption timeout),另一个用于公平共享(An’r share preemption timeout),两者设定时间均为 秒级。默认情况下，两个超时参数均不设置。所以为了允许抢占容器，需要至少 设置其中一个超时参数。

如果队列在minimum share preemption timeout指定的时间内未获得被承诺的最小

共享资源，调度器就会抢占其他容器。可以通过分配文件中的顶层元素

defaultMinSharePreemptionTimeout为所有队列设置默认的超时时间，还可 以通过设置每个队列的minSharePreemptionTimeout元素来为单个队列指定超 时时间。

类似，如果队列在tor share preemption timeout指定的时间内获得的资源仍然低于

其公平共享份额的一半，那么调度器就会抢占其他容器。可以通过分配文件中的

顶层元素defaultFairSharePreemptionTimeout为所有队列设置默认的超时时 间，还可以通过设置每个队列的fairSharePreemptionTimeout元素来为单个队 列指定超时时间。通过设置defaultFairSharePreemptionThreshold和 fairSharePreemptionThreshold (针对每个队列)可以修改超时阈值，默认值是

0.5。

###### 4.3.5延迟调度

所有的YARN调度器都试图以本地请求为重。在一个繁忙的集群上，如果一个应 用请求某个节点，那么极有可能此时有其他容器正在该节点上运行。显而易见的 处理是，立刻放宽本地性需求，在同一机架中分配一个容器。然而，通过实践发 现，此时如果等待一小段时间(不超过几秒)，能够戏剧性的增加在所请求的节点上 分配到一个容器的机会，从而可以提高集群的效率。这个特性称之为延迟调度 {delay scheduling)。容量调度器和公平调度器都支持延迟调度。

YARN中的每个节点管理器周期性的(默认每秒一次)向资源管理器发送心跳清

求。心跳中携带了节点管理器中正运行的容器、新容器可用的资源等信息，这样

对于一个计划运行一个容器的应用而言，每个心跳就是一个潜在的调度机会 (scheduling opportunity) 0 当使用延迟调度时，调度器不会简单的使用它收到的第一个调度机会，而是等待 设定的最大数目的调度机会发生，然后才放松本地性限制并接收下一个调度机会。

对于容量调度器，可以通过设置yam.scheduler.capacity.node-locality-delay来配置 延迟调度。设置为正整数，表示调度器在放松节点限制、改为匹配同一机架上的 其他节点前，准备错过的调度机会的数量。

公平调度器也使用调度机会的数量来决定延迟时间，尽管是使用集群规模的比例

来表示这个值。例如将 yarn.scheduler.fair.locality, threshold.node 设

置为0.5,表示调度器在接受同一机架中的其他节点之间，将一直等待直到集群中 的一半节点都已经给过调度机会。还有个相关的属性yarn.scheduler, fair.locality.threshold.rack,表示在接受另一个机架替代所申请的机架之 前需要等待的时长阈值。

###### 4.3.5主导资源公平性

对于单一类型资源，如内存的调度，容量或公平性的概念很容易确定。例如两个 用户正在运行应用，可以通过度量每个应用使用的内存来比较两个应用。然而， 当有多种资源类型需要调度时，事情就会变得复杂。例如，如果一个用户的应用对 CPU的需求量很大，但对内存的需求量很少；而另一个用户需要很少的CPU，但对内存需 求量很大，那么如何比较这两个应用呢？

YARN中调度器解决这个问题的思路是，观察每个用户的主导资源，并将其作为 对集群资源使用的一个度量。这个方法称为“主导资源公平性”(Dominant Resource Fairness,DRF)®O这个思想用一个简单的例子就可以很好的给予解释。

①对DRF的详细介绍，可以参见Ghodsi在2011年发表的论文，标题为“Dominant Resource Fairness: Fair Allocation of Multiple Resource Types” ，网址力 [http://bitJy/fairjjllocation](http://bitJy/fairjjllocation%e3%80%82)[。](http://bitJy/fairjjllocation%e3%80%82)

想象一个总共有100个CPU和10TB的集群。应用A请求的每份容器资源为2 个CPU和300GB内存，应用B请求的每份容器资源为6个CPU和100GB内存。

A请求的资源在集群资源中占比分别为2%和3%，由于内存占比(3%)大于CPU占 比(2%)，所以内存是A的主导资源。B请求的资源在集群资源中占比分别为6%和 1%，所以CPU是B的主导资源。由于B申请的资源是A的两倍(6% vs3%)，所以在 公平调度下，B将分到一半的容器数。

默认情况下不用DRF,因此在资源计算期间，只需要考虑内存，不必考虑CPU。 对容量调度器进行配置后，可以使用DRF，将capacity-scheduler.xml文件中的 org.apache.hadoop.yarn.util.resource.DominantResourceCalculator 设为 yarn, scheduler. capacity. resource-calculator 即可0

公平调度器若要使用DRF，通过将分配文件中的顶层元素defaultQueue SchedulingPolicy 设为 drf 即可。

##### 4.4延伸阅读

本章简要介绍了 YARN。如果希望了解更多细节，请参见Arun C. Murthy等人所 片的 Apache Hadoop YARN 一书，网址为 [http://yarn-book.com/](http://yarn-book.com/%e3%80%82)[。](http://yarn-book.com/%e3%80%82)
