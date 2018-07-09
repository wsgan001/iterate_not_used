### 管理Hadoop

第10章介绍如何搭建Hadoop集群。本章将关注如何保障集群的平稳运行。

##### 11.1 HDFS

###### 11.1.1永久性数据结构

作为管理员，深人了解namenode、辅助namenode和datanode等HDFS组件如何 在磁盘上组织永久性数据非常重要。洞悉各文件的用法有助于进行故障诊断和故 障检出。

\1. namenode的目录结构

运行中的namenode有如下所示的目录结构:

${dfs.namenode.name.dir}/

|- current

|    |- VERSION

|    |- editS一0000000000000000001-0000000000000000019

|    |- edits_inprogress_0000000000000000020

|    |- fsimage—0000000000000000000

|    |- fsimage_0000000000000000000•md5

|    |- fs image_0000000000000000019

|    |- fsimage_0000000000000000019.md5

I    1- seen一txid

1- in use.lock

如第10章所示，dfs.namenode.name.dir属性描述了一组目录，各个目录存储

着镜像内容。该机制使系统具备了一定的复原能力，特别是当其中一个目录是 NFS的一个挂载时（推荐配置）。

文件是一个Java属性文件，其中包含正在运行的HDFS的版本信息。该 文件一般包含以下内容：

\#Mon Sep 29 09:54:36 BST 2014 namespaceID=1342387246

clusterID=CID-01b5c398-959c-4ea8-aae6-le0d9bd8bl42

cTime=0

storageType=NAME_NODE

blockpoolID=BP-526805057-127.0.0.1-1411980876842 layoutVersion=-57

属性layoutVersion是一个负整数，描述HDFS持久性数据结构（也称布局）的版 本，但是该版本号与Hadoop发布包的版本号无关。只要布局变更，版本号便会递 减（例如，版本号-57之后是-58），此时，HDFS也需要升级。否则，磁盘仍然使用 旧版本的布局，新版本的namenode（或datanode）就无法正常工作。要想知道如何 升级HDFS，请参见11.3.3节。

属性namespacelD是文件系统命名空间的唯一标识符，是在namenode首次格式 化时创建的。clusterlD是将HDFS集群作为一个整体赋予的唯一标识符，对于 联邦HDFS非常重要（见3.2.4节），这里一个集群由多个命名空间组成，且每个命 名空间由一个namenode管理。blockpoolID是数据块池的唯一标识符，数据块 池中包含了由一个namenode管理的命名空间中的所有文件。

dime属性标记了 namenode存储系统的创建时间。对于刚刚格式化的存储系统， 这个属性值为0;但是在文件系统升级之后，该值会更新到新的时间戳。

storageType属性说明该存储目录包含的是namenode的数据结构

in_use.lock文件是一个锁文件，namenode使用该文件为存储目录加锁。可以避免 其他namenode实例同时使用（可能会破坏）同一个存储目录的情况。

namenode的存储目录中还包含eJ/Zs、/k/wage和seeA/jx/t/等二进制文件。只有深 入学习namenode的工作机理，才能够理解这些文件的用途。

2.文件系统映像和编辑日志

文件系统客户端执行写操作时（例如创建或移动文件），这些事务首先被记录到编辑 日志中。namenode在内存中维护文件系统的元数据；当编辑日志被修改时，相关 元数据信息也同步更新。内存中的元数据可支持客户端的读请求。

编辑日志在概念上是单个实体，但是它体现为磁盘上的多个文件。每个文件称为 一个“段” （segment），名称由前缀edits及后缀组成，后缀指示出该文件所包含的 事务ID。任一时刻只有一个文件处于打开可写状态（前述例子中为 edits_inprogress_0000000000000000020y在每个事务完成之后，且在向客户端发 送成功代码之前，文件都需要更新和同步。当namenode向多个目录写数据时，只 有在所有写操作更新并同步到每个复本之后方可返回成功代码，以确保任何事务 都不会因为机器故障而丢失。

每个fsimage文件都是文件系统元数据的一个完整的永久性检査点。（前缀表示映 像文件中的最后一个事务。）并非每一个写操作都会更新该文件，因为Towage是 一个大型文件（甚至可高达几个GB），如果频繁地执行写操作，会使系统运行极为 缓慢。但这个特性根本不会降低系统的恢复能力，因为如果namenode发生故障， 最近的方/wage文件将被载入到内存以重构元数据的最近状态，再从相关点开始向 前执行编辑日志中记录的每个事务。事实上，namenode在启动阶段正是这样做的 （参见11.1.2节对安全模式的讨论）。

E~~每个文件包含文件系统中的所有目录和文件inode的序列化信息。每个 ■k inode是一个文件或目录的元数据的内部描述方式。对于文件来说，包含的信

息有“复本级别” （replication level）、修改时间和访问时间、访问许可、块大 小、组成一个文件的块等：对于目录来说，包含的信息有修改时间、访问许可 和配额元数据等信息。

数据块存储在datanode中，但Towage文件并不描述datanode。取而代之的 是，namenode将这种块映射关系放在内存中。当datanode加入集群时， namenode向datanode索取块列表以建立块映射关系！ namenode还将定期征询 datanode以确保它拥有最新的块映射。

如前所述，编辑日志会无限增长（即使物理上它是分布在多个edits文件中）。尽管 这种情况对于namenode的运行没有影响，但由于需要恢复（非常长的）编辑日志中 的各项事务，namenode的重启操作会比较慢。在这段时间内，文件系统将处于离 线状态，这会有违用户的期望。

解决方案是运行辅助namenode,为主namenode内存中的文件系统元数据创建检 查点。1创建检査点的步骤如下所示（图11-1中也概略展现了前述的编辑日志和映 像文件）。

（1）    辅助namenode请求主namenode停止使用正在进行中的文件，这样 新的编辑操作记录到一个新文件中。主namenode还会更新所有存储目录 中的seen_txid文件。

（2）    辅助namenode从主namenode获取最近的fsimage ft edits文件（采用 HTTP GET）。

（3）    辅助namenode将力/wtzge文件载入内存，逐一执行etZ/h文件中的事务， 创建新的合并后的力/wtzge文件。

（4）    辅助namenode将新的fsimage文件发送回主namenode（使用HTTP PUT）,主namenode将其保存为临时的.drpf文件。

（5）    主namenode重新命名临时的文件，便于日后使用。

最终，主namenode拥有最新的方/wage文件和一个更小的正在进行中的edits文件 （edits文件可能非空，因为在创建检査点过程中主namenode还可能收到一些编辑 请求）。当namenode处在安全模式时，管理员也可调用hdfs dfsadmin -saveNamespace命令来创建检查点。

这个过程清晰解释了辅助namenode和主namenode拥有相近内存需求的原因（因为 辅助namenode也把力/wage文件载入内存）。因此，在大型集群中，辅助 namenode需要运行在一台专用机器上。

创建检査点的触发条件受两个配置参数控制。通常情况下，辅助namenode每隔一 小时（由dfs.namenode.checkpoint.period属性设置，以秒为单位）创建检查

点；此外，如果从上一个检查点开始编辑日志的大小已经达到100万个事务（由 dfs.namenode.checkpoint.txns属性设置）时，那么即使不到一小时，也会创 建检查点，检查频率为每分钟一次（由dfs.namenode.checkpoint, check.period属性设置，以秒为单位）。

①实用户可以使用-checkpoint选项来启动namenode,它将运行一个检查点过程以应对另 -个（主）namenode。在功能上，这等价于运行一个辅助namenode,但直到本书写就之际，这

个技术并未体现出强于辅助n amen ode的能力（实际上，辅助namenode仍然是目前最常!Ah的用 法）。当在一个高有效的环境之中运行时（参见3.2.5节对HTTP高可用性的讨论），备用节点执 行检查点功能。

Primary Namenode



Secondary Namenode



I.Roll edits



edits|inprogress JO



| 糸：-.<.<•* | a'r,• • |
| ----------- | ------- |
| edit...     | 5J-19   |

fsimage 0

w# ***** v ,,



fsimage J 9.ckpt



\4. Transfer checkpoint to primary



fs}mage_19.ckpt



\5. Rename fsimage.ckpt



fs’f'



\2. Retrieve fsimage and edits from primary



![img](Hadoop43010757_2cdb48_2d8748-150.jpg)



图11-1.创建检查点的过程

3.辅助namenode的目录结构

辅助namenode的检查点目录(dfs.namenode.checkpoint .dir)的布局和主 namenode的检查点目录的布局相同。这种设计方案的好处是，在主namenode发 生故障时(假设没有及时备份，甚至在NFS上也没有)，可以从辅助namenode恢复 数据。有两种实现方法。方法一是将相关存储目录复制到新的namenode中；方法 二是使用-importCheckpoint选项启动namenode守护进程，从而将辅助 namenode用作新的主namenode。借助该选项，仅当dfs.namenode.name.dir属 性定义的目录中没有元数据时，辅助namenode会从dfs.namenode.

checkpoint.dir属性定义的目录载入最新的检查点namenode元数据， 必担心这个操作会覆盖现有的元数据。

此，不



\4. datanode的目录结构

和namenode不同的是，datanode的存储目录是初始阶段自动创建的，不需要额外 格式化。datanode的关键文件和目录如下所示：

${dfs.datanode.data.dir}/

H current

|    |— BP-526805057-127.0.0.1-1411980876842

|    |    1— current

|    |    |—    VERSION

I    |    |—    finalized

|    |    |    |- blkJL073741825

|    |    |    |- blk 1073741825    1001.meta

|    |    |    |- blk_1073741826

|    |    |    1- blk 1073741826    1002.meta

|    |    1—    rbw

|    1— VERSION

L infuse.lock

hdfs数据块存储在以为前缀名的文件中，文件名包含了该文件存储的块的 原始字节数。每个块有一个相关联的带有iem后缀的元数据文件。元数据文件包 括头部（含版本和类型信息）和该块各区段的一系列的校验和。

每个块属于一个数据块池，每个数据块池都有自己的存储目录，目录根据数据块 池1D形成（和namenode的VERSION文件中的数据块池ID相同）。

当目录中数据块的数量增加到一定规模时，datanode会创建一个子目录来存放新 的数据块及其元数据信息。如果当前目录已经存储了 64个（通过 dfs.datanode.numblocks属性设置）数据块时，就创建一个子目录。终极目标是 设计一棵高扇出的目录树，即使文件系统中的块数量非常多，目录树的层数也不 多。通过这种方式，datanode可以有效管理各个目录中的文件，避免大多数操作 系统遇到的管理难题，即很多（成千上万个）文件放在同一个目录之中。

如果dfs.datanode.data.dir属性指定了不同磁盘上的多个目录，那么数据块 会以轮转（round-robin）的方式写到各个目录中。注意，同一个datanode上的每个 磁盘上的块不会重复，只有不同datanode之间的块才有可能重复。

namenode启动时，首先将映像文件（A/wage）载入内存，并执行编辑日志（ecf/As）中 的各项编辑操作。一旦在内存中成功建立文件系统元数据的映像，则创建一个新 的文件（该操作不需要借助辅助namenode）和一个空的编辑日志。在这个过 程中，namenode运行在安全模式，意味着namenode的文件系统对于客户端来说 是只读的。

![img](Hadoop43010757_2cdb48_2d8748-151.jpg)



严格来说，在安全模式下，只有那些访问文件系统元数据的文件系统操作是肯 定成功执行的，例如显示目录列表等。对于读文件操作来说，只有集群中当前 datanode上的块可用时，才能够工作。但文件修改操作（包括写、删除或重命名）

均会失败。

需要强调的是，系统中数据块的位置并不是由namenode维护的，而是以块列表的 形式存储在datanode中（每个datanode存储的块组成的列表）。在系统的正常操作 期间，namenode会在内存中保留所有块位置的映射信息。在安全模式下，各个 datanode会向namenode发送最新的块列表信息，namenode 了解到足够多的块位 置信息之后，即可高效运行文件系统。如果namenode认为向其发送更新信息的 datanode节点过少，则它会启动块复制进程，以将数据块复制到新的datanode节 点。然而，在大多数情况下上述操作都是不必要的（因为实际上namenode只需继 续等待更多datanode发送更新信息即可），并浪费了集群的资源。实际上，在安全 模式下namenode并不向datanode发出任何块复制或块删除的指令。

如果满足“最小复本条件” （minimal replication condition）, namenode会在30秒钟

之后就退出安全模式。所谓的最小复本条件指的是在整个文件系统中有99.9%的 块满足最小复本级别（默认值是1，由dfs.namenode.replication.min属性设 置，参见表11-1）。

在启动一个刚刚格式化的HDFS集群时，因为系统中还没有任何块，所以 namenode不会进入安全模式。

进入和离开安全模式

要想查看namenode是否处于安全模式，可以像下面这样用dfsadmin命令：

% hdfs dfsadmin -safemode get

Safe mode is ON

HDFS的网页界面也能够显示namenode是否处于安全模式。

表1V1.安全模式的属性

属性名称    类型默认值说明

dfs. namenode. replication, min int 1    成功执行写操作所需要创建的最少复本数目

（也称为最小复本级别）

dfs.namenode• safemode.    float 0.999 在 namenode 退出安全模式之前，系统中

threshold-pet    满足最小复本级别（由dfs• namenode.

replication.min定义）的块的比例。将 这项值设为0或更小会令namenode无法启 动安全模式：设为髙于1则永远不会退出

安全模式

dfs.namenode. safemode.extension    int 30000 在满足最小复本条件（由 dfs.namenode.

safemode. threshold -pet 定义）之后， namenode还需要处于安全模式的时间（以毫 秒为单位）。对于小型集群（几十个节点）来 说，这项值可以设为0

有时，用户期望在执行某条命令之前namenode先退出安全模式，特别是在脚本 中。使用wait选项能够达到这个目的：

%hdfs dfsadmin -safemode wait

\# command to read or write a file

管理员随时可以让namenode进入或离开安全模式。这项功能在维护和升级集群时 非常关键，因为需要确保数据在指定时段内是只读的。使用以下指令进入安全模式：

% hdfs dfsadmin -safemode enter

Safe mode is ON

前面提到过，namenode在启动阶段会处于安全模式。在此期间也可使用这条命 令，从而确保namenode在启动完毕之后不离开安全模式。另一种使namenode永 远处于安全模式的方法是将属性dfs.namenode. safemode. threshold-pet的值设为大

干1。

运行以下指令即可使得namenode离开安全模式:

% hdfs dfsadmin -safemode leave

Safe mode is OFF

###### 11.1.3日志审计

HDFS的日志能够记录所有文件系统访问请求，有些组织需要这项特性来进行审 计。对日志进行审计是log4j在INFO级别实现的。在默认配置下，此项特性并未启 用，但是通过在文件hadoop~env.sk中增加以下这行命令，很容易启动该日志审计 特性：

export HDFS_AUDIT_LOGGER="INFO,RFAAUDIT"

每个HDFS事件均在审计日志中生成一行日志记录。下例说明如何 对/uyer/tozw目录执行list status命令（列出指定目录下的文件/目录的状态）：

2014-09-30 21:35:30,484 INFO FSNamesystem.audit: allowed=true ugi=tom （auth:SIMPLE） ip=/127.0.0.1 cmd=listStatus src=/user/tom dst=null perm=null proto=rpc

###### 11.1.4



![img](Hadoop43010757_2cdb48_2d8748-152.jpg)



\1. dfsadmin 工具

dfsadmin工具用途较广，既可以查找HDFS状态信息，又可在HDFS上执行管理 操作。以hdfs dfsadmin形式调用，且需要超级用户权限。

表11-2列举了部分命令。要想进一步了解详情，可以用-help命令。

表 11-2. dfsadmin 命令

| 人人麵.麵命々  | 的说明，..嫌RO觀:.                                           |
| -------------- | ------------------------------------------------------------ |
| -help          | 显示指定命令的帮助，如果未指明命令，则显示所有命令的帮助     |
| -report        | 显示文件系统的统计信息（类似于在网页界面上显示的内容），以及所连接 的各个datanode的信息 |
| -metasave      | 将某些信息存储到Hadoop日志目录中的一个文件中，包括正在被复制或 删除的块的信息以及已连接的datanode列表 |
| -safemode      | 改变或査询安全模式，参见11.1.2节对安全模式的讨论             |
| -saveNamespace | 将内存中的文件系统映像保存为一个新的fsimage文件，重置edits文 件。该操作仅在安全模式下执行 |
| -fetchlmage    | 从namenode获取最新的文件，并保存为本地文件                   |
| -refreshNodes  | 更新允许连接到namenode的datanode列表，参见113.2节对委任和解除 节点的讨论 |
|                |                                                              |

| 命令               | 续表说明                                                     |
| ------------------ | ------------------------------------------------------------ |
| -upgradeProgress   | 获取有关HDFS升级的进度信息或强制升级。参见11.3.3对升级的讨论 |
| -finalizellpgrade  | 移除datanode和namenode的存储目录上的旧版本数据。这个操作一般在 升级完成而且集群在新版本下运行正常的情况下执行。参见1133节对 升级的讨论 |
| -setQuota          | 设置目录的配额，即设置以该目录为根的整个目录树最多包含多少个文 件和目录。这项配置能有效阻止用户创建大量小文件，从而保护 namenode的内存（文件系统中的所有文件、目录和块的各项信息均存储在 内存中） |
| -clrQuota          | 清理指定目录的配额                                           |
| -setSpaceQuota     | 设置目录的空间配额，以限制存储在目录树中的所有文件的总规模。分 别为各用户指定有限的存储空间很有必要 |
| -clrSpaceQuota     | 清理指定的空间配额                                           |
| -refreshServiceAcl | 刷新namenode的服务级授权策略文件                             |
| -allowSnapshot     | 允许为指定的目录创建快照                                     |
| -disallowSnapshot  | 禁止为指定的目录创建快照                                     |

2.文件系统检查fsck工具

Hadoop提供工具来检査HDFS中文件的健康状况。该工具会查找那些在所有 datanode中均缺失的块以及过少或过多复本的块。下例演示如何检查某个小型集 群的整个文件系统：

% hdfs fsck /

......................Status： HEALTHY

Total size:    511799225 B

Total dirs:    10

Total files:    22

Total blocks (validated):    22 (avg. block size 23263601 B)

Minimally replicated blocks:    22 (100.0 %)

Over-replicated blocks:    0 (0.0 %)

Under-replicated blocks:    0 (0.0 %)

Mis-replicated blocks:    0 (0.0 %)

The filesystem under path */’ is HEALTHY

fsck工具从给定路径(本例是文件系统的根目录)开始循环遍历文件系统的命名空

间，并检查它所找到的所有文件。对于检查过的每个文件，都会打印一个点 。在此过程中，该工具获取文件数据块的元数据并找出问题或检査它们是否

一致。注意，工具只是从namenode获取信息，并不与任何datanode进行交 互，因此并不真正获取块数据。

力d输出文件的大部分内容都容易理解，以下仅说明部分信息。

过多复制的块指复本数超出最小复本级别的块。严格意义上讲，这并 非一个大问题，HDFS会自动删除多余复本。

仍需复制的块指复本数目低于最小复本级别的块。HDFS会自动为这 些块创建新的复本，直到达到最小复本级别。可以调用hdfs dfsadmin -metasave指令了解正在复制的（或等待复制的）块的信息。

•错误复制的块是指违反块复本放置策略的块（参见3.6.2节“复本的放 置”相关内容）。例如，在最小复本级别为3的多机架集群中，如果一个 块的三个复本都存储在一个机架中，则可认定该块的复本故置错误，因 为一个块的复本要分散在至少两个机架中，以提高可靠性。

•损坏的块指所有复本均已损坏的块。如果虽然部分复本损坏，但至少 还有一个复本完好，则该块就未损坏；namenode将创建新的复本，直到达到 最小复本级别。

•缺失的复本指在集群中没有任何复本的块。

损坏的块和缺失的块是最需要考虑的，因为这意味着数据已经丢失了。默认情况 下，力d不会对这类块进行任何操作，但也可以让力A执行如下某一项操作。

•移动使用-move选项将受影响的文件移到HDFS饱/lost+found目录。 这些受影响的文件会分裂成连续的块链表，可以帮助用户挽回损失。

•删除使用-delete选项删除受影响的文件。记住，在删除之后，这些 文件无法恢复。

査找一个文件的数据块方d工具能够帮助用户轻松找到属于特定文件的数据 块。例如：

% hdfs fsck /user/tom/part-00007 -files -blocks -racks

/user/tom/part-00007 25582428 bytes, 1 block(s): OK

\0. blk二3724870485760122836JL035 len=25582428 repl=3 [/default-rack/10.251.43•2:50010,

/default-rack/10.251.27.178:50010, /default-rack/10.251.123.163:50010]

输出内容表示文件/wser/Zow/poTV-卯卵7包含一个块，该块的三个复本存储在不同 datanode。力dr所使用的三个选项的含义如下。

•    -files选项显示第一行信息，包括文件名称、大小、块数量和健康状 况（是否有缺失的块）

•    -blocks选项描述文件中各个块的信息，每个块一行

•    -racks选项显示各个块的机架位置和datanode的地址

如果不指定任何参



运行不带参数的hdfs fsck命令会显示完整的使用说明。

\3. datanode块扫描器

各个datanode运行一个块扫描器，定期检测本节点上的所有块，从而在客户端读 到坏块之前及时地检测和修复坏块。可以依靠扫描器所维护的块列表依次扫描 块，查看是否有校验和错误。扫描器还使用节流机制，来维持datanode的磁盘带 宽（换句话说，块扫描器工作时仅占用一小部分磁盘带宽）。

在默认情况下，块扫描器每隔三周就会检测块，以应对可能的磁盘故障，该周期

由dfs.datanode.scan.period.hours属性设置，默认值是504小时。损坏的 块被报给namenode，并被及时修复。

用户可以访问datanode的网页（A即.•//6/67/<7«0必:5⑽来获取该 datanode的块检测报告。以下是一份报告的范例，很容易理解：

Progress this period    :    109%

Time left in cur period    : 53.08%

通过指定 listblocks 参数，http: //datanode:50075/blockScannerReport? List blocks

会在报告中列出该datanode上所有的块及其最新验证状态。下面节选部分内容（由 于页面宽度限制，报告中的每行内容被显示成两行）：

blk_6035596358209321442    : status : ok type : none scan time

0    not yet verified

blk_3065580480714947643 : status : ok type : remote scan time : 1215755306400    2008-07-11 05:48:26,400

blk_8729669677359108508 : status : ok type : local scan time : 1215755727345    2008-07-11 05:55:27,345

第一列是块ID,接下来是一些键-值对。块的状态（status）要么failed（损坏的）， 要么ok（良好的），由最近一次块扫描是否检测到校验和来决定。扫描类型（type）可 以是local（本地的）、remote（远程的）或none（没有）。如果扫描操作由后台线程 执行，则是local;如果扫描操作由客户端或其他datanode执行，则是remote; 如果针对该块的扫描尚未执行，则是none。最后一项信息是扫描时间，从1970 年1月1号午夜开始到扫描时间为止的毫秒数，另外也提供更易读的形式。

4.均衡器

随着时间推移，各个datanode上的块分布会越来越不均衡。不均衡的集群会降低 MapReduce的本地性，导致部分datanode相对更加繁忙。应避免出现这种情况。

均衡器（balancer）程序是一个Hadoop守护进程，它将块从忙碌的datanode移到相 对空闲的datanode,从而重新分配块。同时坚持块复本放置策略，将复本分散到 不同机架，以降低数据损坏率（参见3.6.2节）。它不断移动块，直到集群达到均 衡，即每个datanode的使用率（该节点上已使用的空间与空间容量之间的比率）和 集群的使用率（集群中已使用的空间与集群的空间容量之间的比率）非常接近，差距 不超过给定的阀值。可调用下面指令启动均衡器：

% start-balancer.sh

-threshold参数指定阈值（百分比格式），以判定集群是否均衡。该标记是可选 的；若省略，默认阈值是10%。任何时刻，集群中都只运行一个均衡器。

均衡器会一直运行，直到集群变得均衡为止，此时，均衡器不能移动任何块，或 失去与nameiwde的联络。均衡器在标准日志目录中创建一个日志文件，记录它所 执行的每轮重新分配过程（每轮次输出一行）。以下是针对一个小集群的日志输出 （为适应页面显示要求稍微调整了格式）:

Time Stamp    Iteration# Bytes Already Moved.left To Move... Being Moved

Mar 18, 2009 5:23:42 PM 0    0 KB    219.21 MB    150.29 MB

Mar 18, 2009 5:27:14 PM 1    195.24 MB    22.45 MB    150.29 MB

The cluster is balanced. Exiting…

Balancing took 6.072933333333333 minutes

为了降低集群负荷、避免干扰其他用户，均衡器被设计为在后台运行。在不同节 点之间复制数据的带宽也是受限的。默认值是很小的1 MB/s,可以通过hdfs-5z7e.xw/文件中的 dfs.datanode. balance. bandwidthPerSec 属性重新设定（单 位是字节）。

##### 11.2监控

监控是系统管理的重要内容。在本小节中，我们概述Hadoop的监控工具，看看它 们如何与外部监控系统相结合。

监控的目标在于检测集群在何时未提供所期望的服务。主守护进程是最需要监控 的，包括主namenode、辅助namenode和资源管理器。我们可以预期少数 datanode和节点管理器会出现故障，特别是在大型集群中。因此，需要为集群预 留额外的容量，即使有一小部分节点宏机，也不会影响整个系统的运作。

除了以下即将介绍的工具之外，管理员还可以定期运行一些测试作业来检查集群 的健康状况。

所有Hadoop守护进程都会产生日志文件，这些文件非常有助于查明系统中已发生 的事件。10.3.2节在讨论系统日志文件时解释了如何配置这些文件。

1.设置日志级别

在故障排查过程中，若能够临时变更特定组件的日志的级别的话，将非常有益 可以通过Hadoop守护进程的网页（在守护进程的网页的目录下）来改变任 何log4j日志名称的日志级别。一般来说，Hadoop中的日志名称对应着执行相关日 志操作的类名称。此外，也有例夕H青况，因此最好从源代码中查找日志名称。

也可以为所有以给定前缀开始的类包启用日志。例如，为了启用资源管理器相关 的所有类的日志调试特性，可以访问它的网页http:"resource-manager-host:8088/logLevel , 并将 日 志名 org. apache, hadoop. yarn, server. resourcemanager 设置为 DEBUG 级别。

也可以通过以下命令实现上述目标：

% hadoop daemonlog -setlevel resource -manager-ho s t: 8088 \ org.apache•hadoop.yarn.server.resourcemanager DEBUG

按照上述方式修改的日志级别会在守护进程重启时被复位，通常这也符合用户预 期。如果想永久性地变更日志级别，只需在配置目录下的log4j.properties文柃中 添加如下这行代码：

log4 ]•. logger, org. apache, hadoop .yarn, server. resourcemanager=DEBUG

2.获取堆栈跟踪

Hadoop守护进程提供一个网页（网页界面的目录）对正在守护进程的JVM中 运行着的线程执行线程转储（thread dump）。例如，可通过[http://resource-manager-host:8088/stacks](http://resource-manager-host:8088/stacks%e8%8e%b7%e5%be%97%e8%b5%84%e6%ba%90%e7%ae%a1%e7%90%86%e5%99%a8%e7%9a%84%e7%ba%bf%e7%a8%8b%e8%bd%ac%e5%82%a8%e3%80%82)[获得资源管理器的线程转储。](http://resource-manager-host:8088/stacks%e8%8e%b7%e5%be%97%e8%b5%84%e6%ba%90%e7%ae%a1%e7%90%86%e5%99%a8%e7%9a%84%e7%ba%bf%e7%a8%8b%e8%bd%ac%e5%82%a8%e3%80%82)

###### 11.2.2度量和JMX（Java管理扩展）

Hadoop守护进程收集事件和度量相关的信息，这些信息统称为“度量”（metric）。 例如，各个datanode会收集以下度量（还有更多）：写入的字节数、块的复本数和 客户端发起的读操作请求数（包括本地的和远程的）。

有时用metrics2指代Hadoop 2及后续版本的度量系统，以区别早期版本 Hadoop的旧度量系统（现在已经不支持）。

度量从属于特定的上下文（context）。目前，Hadoop使用“ dfs ”    “ mapred ”

“yarn”和“rpc”四个上下文。Hadoop守护进程通常在多个上下文中收集度量。 例如，datanode会分别为“di's”和“rpc”上下文收集度量。

度量和计数器的差别在哪里?

t要区别是应用范围不同：度量由Hadoop守护进程收集，而计数器（参见9.1 芦对计数器的讨论）先针对MapReduce任务进行采集，再针对整个作业进行汇

总。此外，用户群也不同，从广义上讲，度量为管理员服务，而计数器主要为 MapReduce用户服务<>

![img](Hadoop43010757_2cdb48_2d8748-153.jpg)



二者的数据采集和聚集过程也不相同。计数器是MapReduce的特性， MapReduce系统确保计数器值由任务JVM产生，再传回application master，最 终传回运行MapReduce作业的客户端。（计数器是通过RPC的心跳［heartbeat］传 播的，详情可以参见7.1.5节。）在整个过程中，任务进程和application master 都会执行汇总操作



![img](Hadoop43010757_2cdb48_2d8748-154.jpg)



![img](Hadoop43010757_2cdb48_2d8748-155.jpg)



![img](Hadoop43010757_2cdb48_2d8748-156.jpg)



![img](Hadoop43010757_2cdb48_2d8748-157.jpg)



釀醸



*•.冰权於慷2聯辦浓■    S'

1、擬微處撼龍满■滅|备•纖繡:



度量的收集机制独立于接收更新的组件。有多种输出度量的方式，包括本地文 件、Ganglia和JMX。守护进程收集度量，并在输出之前执行汇总操作。



所有的Hadoop度量都发布给JMX（Java management Extensions）,可以使用标准的 JMX工具，如JConsole（JDK自带），来査看这些度量。对于远程监控，必须将 JMX系统属性com.sun.management.jmxremote.port（及其他一些用于安全的 属性）设置为允许访问。为了在namenode实现这些，需要在/za而文件中 设置以下语句：

HADOOP_NAMENODE_OPTS="-Dcom.sun.management.jmxremote.port=8004"

也可以通过特定Hadoop守护进程的网页査看该守护进程收集的JMX度量 （JSON格式），这为调试带来了便利。例如，可以在网页<http://namenode-host:50070/jmx> 査看 namenode 的度量。

Hadoop自带大量的度量通道用于向外部系统发布度量，例如本地文件或Ganglia 监控系统。通道在hadoop-metrics2.properties文件中配置，可以参考该文件，了解 如何进行配置设置。

##### 11.3维护

###### 11.3.1日常管理过程

1.元数据备份

如果namenode的永久性元数据丢失或损坏，则整个文件系统无法使用。因此，元 数据备份非常关键。可以在系统中分别保存若干份不同时间的备份（例如，1小时 前、1天前、1周前或1个月前），以保护元数据。方法一是直接保存这些元数据

文件的复本；方法二是整合到namenode上正在使用的文件屮。

最直接的元数据备份方法是使用dfsadmin命令下载namenode最新的方/wage文 件的复本：

% hdfs dfsadmin -fetchlmage fsimage.backup

可以写一个脚本从准备存储fsimage存档文件的异地站点运行该命令。该脚本还需 测试复本的完整性。测试方法很简单，只要启动一个本地namenode守护进程，查 看它是否能够将方/wflge和etZ/Av文件载入内存（例如，通过扫描namenode日志以 获得操作成功信息）。®

2.数据备份

尽管HDFS已经充分考虑了如何可靠地存储数据，但是正如任何存储系统一样， 仍旧无法避免数据丢失。因此，备份机制就很关键。Hadoop中存储着海量数据， 判断哪些数据需要备份以及在哪里备份就极具挑战性。关键在于为数据划分不同 优先级。那些无法重新生成的数据的优先级最高，这些数据对业务非常关键。同 理，可再生数据和一次性数据商业价值有限，所以优先级最低，无需备份。

![img](Hadoop43010757_2cdb48_2d8748-158.jpg)



不要误以为HDFS的复本技术足以胜任数据备份任务。HDFS的程序纰漏、硬 件故障都可能导致复本丟失。尽管Hadoop的设计方案可确保硬件故障极不讨 能导致数据丢失，但是这种可能性无法完全排除，特别是软件bug和人工误操 作情况在所难免。

再比较HDFS的备份技术和RAID。RAID可以确保在某一个RAID盘片发生故 障时数据不受损坏。.但是，如果发生RAID控制器故障、软件纰漏（岈能重写部 分数据）或整个磁盘阵列故障，数据肯定会丢失。

通常情况下，HDFS的用户目录还会附加若干策略，例如目录容量限制和夜间备 份等。用户需要熟悉相关策略，才可以预料执行结果。

distcp是一个理想的备份工具，其并行的文件复制功能可以将备份文件存储到其他 HDFS集群（最好软件版本不同，以防Hadoop软件纰漏而丢失数据）或其他Hadoop 文件系统（例如S3）。此外，还可以用3.4节提到的方法将数据从HDFS导出到完 全不同的存储系统中。

① Hadoop 自带的 Offline Image Viewer 和 Offline Edits Viewer 工具能检测.加则fge 和 edits 文件的 一致性。这两种工具均支持旧版文件。因此，用户可以用这些工具来诊断Hadoop的早期发布 版本中存在的问题。可以键入hdfs oiv和hdfs oev来调用这些工具。

HDFS允许管理者和用户对文件系统进行快照。快照是对文件系统子树在给定时 刻的一个只读复本。由于并不真正复制数据，因此快照非常高效，它们简单地记 录每个文件的元数据和块列表，这对于重构快照时刻的文件系统内容已经足 够了。

快照不是数据备份的替代品，但是对于恢复用户误删文件在特定时间点的数据而 言，它们是一个有用的工具。可以制定一个周期性快照的策略，根据年份将快照 保存一段特定的时间。例如，可以对前一天的数据进行每小时一次的快照，对前 一个月的数据进行每天一次的快照。

\3.    文件系统检查（fsck）

建议定期地在整个文件系统上运行HDFS的力d（文件系统检查）工具（例如，每天 执行），主动查找丢失的或损坏的块。参见11.1.4节对文件系统检查的详细介绍。

\4.    文件系统均衡器

定期运行均衡器工具（参见H.1.4节对均衡器的详细介绍），保持文件系统的各个 datanode比较均衡。

###### 11.3.2委任和解除节点

Hadoop集群的管理员经常需要向集群中添加节点，或从集群中移除节点。例如， 为了扩大存储容量，需要委任节点。相反的，如果想要缩小集群规模，则需解除 节点。如果某些节点表现反常，例如故障率过高或性能过于低下，则需要解除该

通常情况下，节点同时运行datanode和节点管理器， 解除。

而两者一般同时被委任或



1.委任新节点

委任一个新节点非常简单。首先，配置hdfs-site.xml文件，指向namenode；其次，配 置yarn-site.xml文件，指向资源管理器；最后，启动datanode和资源管理器守护 进程。然而，预先指定一些经过审核的节点以从中挑选新节点仍不失为一种好的 方法。

随便允许一台机器以datanode身份连接到namenode是不安全的，因为该机器很可 能会访问未授权的数据。此外，这种机器并非真正的datanode,不在集群的控制 之下，随时可能停止，导致潜在的数据丢失。(想象一下，如果有多台这类机器连 接到集群，而且某一个块的全部复本恰巧只存储在这类机器上，安全性如何？)由 于错误配置的可能性，即使这些机器都在本机构的防火墙之内，这种做法的风险 也很髙。因此所有工作集群上的datanode(以及节点管理器)都应该被明确管理。

允许连接到namenode的所有datanode放在一个文件中，文件名称由dfs.hosts 属性指定。该文件放在namenode的本地文件系统中，每行对应一个datanode的网 络地址(由datanode报告一可以通过namenode的网页査看)。如果需要为一个 datanode指定多个网络地址，可将多个网络地址放在一行，由空格隔开。

类似的，可能连接到资源管理器的各个节点管理器也在同一个文件中指定(该文件

的名称由 yarn.resourcemanager.nodes.include-path 属性指定。在通常情 况下，由于集群中的节点同时运行datanode和节点管理器守护进程，dfs.hosts 和 yarn.resourcemanager.nodes.include-path 会同时指向一个文件，即

include 文件。

dfs.hosts 属性和 yarn, resourcemanager. nodes, include-path 属性指定 的(一个或多个)文件不同于slaves文件。前者供namenode和资源管理器使用，

用于决定可以连接哪些工作节点。Hadoop控制脚本使用■v&v從文件执行面向整 个集群范围的操作，例如重启集群等。Hadoop守护进程从不使用■y/flvey文件。

向集群添加新节点的步骤如下。

(1)    将新节点的网络地址添加到include文件中。

(2)    运行以下指令，将审核过的一系列datanode集合更新至namenode信息：

% hdfs dfsadmin -refreshNodes

(3)    运行以下指令，将审核过的一系列节点管理器信息更新至资源管理器：

% yarn rmadmin -refreshNodes

(4)    以新节点更新slaves文件。这样的话，Hadoop控制脚本会将新节点包括 在未来操作之中。

(5)    启动新的datanode和节点管理器。

(6)检査新的datanode和节点管理器是否都出现在网页界面中。

HDFS不会自动将块从旧的datanode移到新的datanode以平衡集群。用户需要自 行运行均衡器，详情参考11.1.4节对均衡器的讨论。

2.解除旧节点

HDFS能够容忍datanode故障，但这并不意味着允许随意终止datanode0以三复 本策略为例，如果同时关闭不同机架上的三个datanode,则数据丢失的概率会非 常高。正确的方法是，用户将拟退出的若干datanode告知namenode, Hadoop系统就 可©±些datanode停＜1±前将块复制到其他datanode 0

有了节点管理器的支持，Hadoop对故障的容忍度更高。如果关闭一个正在运行 MapReduce任务的节点管理器，application master会检测到故障，并在其他节点上 重新调度任务。

解除节点的过程由exclude文件控制。对于HDFS来说，文件由dfs.hosts, exclude 属性设置；对于 YARN 来说，文件由 yarn.resourcemanager. nodes, exclude-path属性设置。这些文件列出若干未被允许连接到集群的节点。通 常，这两个属性指向同一个文件。

判断一个节点管理器能否连接到资源管理器非常简单。仅当节点管理器出现在 include文件且不出现在exclude文件中时，才能够连接到资源管理器。注意，如 果未指定include文件，或include文件为空，则意味着所有节点都包含在include 文件中。

HDFS的规则稍有不同。如果一个datanode同时出现在zTw/wt/e和exclude文件 中，则该节点可以连接，但是很快会被解除委任。表11-3总结了 datanode的不同 组合方式。与节点管理器类似，如果未指定include文件或include文件为空，都 意味着包含所有节点。

表 11 -3. HDFS 的 include 文件和 exclude 文件

| 节点是否出现在include文件中否 | 节点是否出现在exclude文件中否 | 解释节点无法连接     |
| ----------------------------- | ----------------------------- | -------------------- |
| 否                            | 是                            | 节点无法连接         |
| 是                            | 否                            | 节点可连接           |
| 是                            | 是                            | 节点可连接，将被解除 |

从集群中移除节点的步骤如下。

(1)    将待解除节点的网络地址添加到exclude文件中，不更新include文件。

(2)    执行以下指令，使用一组新的审核过的datanode来更新namenode设置:

% hdfs dfsadmin -refreshNodes

(3)    使用一组新的审核过的节点管理器来更新资源管理器设置：

% yarn rmadmin -refreshNodes

(4)    转到网页界面，查看待解除datanode的管理状态是否已经变为“正在解 除”(Decommission】n Progress),因为此时相关的datanode正在被解除过 程之中。这些datanode会把它们的块复制到其他datanode中。

(5)    当所有datanode的状态变为“解除完毕”(Decommissioned)时，表明所有 块都已经复制完毕。关闭已经解除的节点。

(6)    从Zwc/wt/e文件中移除这些节点，并运行以下命令：

% hdfs dfsadmin -refreshNodes % yarn rmadmin -refreshNodes

(7)    从文件中移除节点。

###### 11.3.3升级

升级Hadoop集群需要细致的规划，特别是HDFS的升级。如果文件系统的布局的 版本发生变化，升级操作会自动将文件系统数据和元数据迁移到兼容新版本的格 式。与其他涉及数据迁移的过程相似，升级操作暗藏数据丢失的风险，因此需要 确保数据和元数据都已经备份完毕。参见11.3.1节对日常管理过程的讨论。

规划过程最好包括在一个小型测试集群上的测试过程，以评估是否能够承担(可能 的)数据丢失的损失。测试过程使用户更加熟悉升级过程、了解如何配置本集群和 工具集，从而为在产品集群上进行升级工作消除技术障碍。此外，一个测试集群 也有助于测试客户端的升级过程。用户可以阅读以下补充内容中对客户端兼容性 的讨论。

兼容性

将Hadoop版本升级成另外一个版本时，需要仔细考虑需要升级步骤。同时还 要考虑几个方面：API兼容性、数据兼容性和连接兼容性。

API兼容性重点考虑用户代码和发行的Hadoop API之间的对比，例如Java MapReduce API。主发行版本（例如从l.x.y到2.0.0）是允许破坏API兼容性的， 因此，用户的程序要修改并重新编译。次重点发行版本（例如从到1.0.x到 1.1.0）和单点发行版本（例如从1.0.1到1.0.2）不应该破坏兼容性。

Hadoop针对API函数使用分类模式来表征其稳定性。按照先前的命名规 则，API兼容性包括标记为InterfaceStability.Stable。公开发行的 Hadoop API 中包含有部分函数，标记为 InterfaceStability.Evolving 或 者 InterfaceStability.Unstable（上述标注包含在 org.apache.hadoop. classification软件包中），这意味允许它们分别在次重点发行版本和单点发 行版本中破坏兼容性。

数据兼容性主要考虑持久数据和元数据的格式，例如在HDFS namenode中用 于存储持久数据的格式。这些格式允许在主版本和次重点版本之间修改，但是 这类修改对用户透明，因为系统升级时数据会自动迁移。系统升级路径有一些 限制，这些限制包含在发行须知中。例如，在系统升级过程中可能需要通过某 个中间发行版本依次升级，而非一步直接升级到最新版本。

连接兼容性主要考虑通过利用RPC和HTTP这样的连接协议来实现客户端和 服务器之间的互操作性。连接兼容性的规则是，客户端与服务器必须有相同的 主版本号，但次版本号或单点发行版本号可以不同（例如，客户端2.0.2版可以 和服务器2.0.1版或2.1.0版一起工作，但是与服务器3.0.0版不能一起工作）。

【    这条连接兼容性规则和Hadoop早期版本中要求的不同。早期版本中，内

部客户端（例如datanode）必须和服务端一起加锁升级。目前这种客户端和服 务端的版本可以不同的事实使得Hadoop 2能够支持滚动升级。

在<http://bit.ly/hadoop> compatibility可以查阅到Hadoop遵从的全部兼容性 规则。

如果文件系统的布局并未改变，升级集群就非常容易：在集群上安装新版本的 Hadoop（客户端也同步安装），关闭旧的守护进程，升级配置文件，启动新的守护

进程，令客户端使用新的库。整个过程是可逆的，换言之，也可以方便地还原到 旧版本。

成功升级版本之后，还需要执行两个清理步骤。

(1)    从集群中移除旧的安装和配置文件。

(2)    在代码和配置文件中针对“被弃用”(deprecation)警告信息进行修复。

升级功能是Hadoop集群管理工具如Cloudera Manager和Apache Ambari的一个亮 点。它们简化了升级过程，且使得滚动升级变得容易。节点以批量方式升级(或对 于主节点，一次升级一个)，这样客户端不会感受到服务中断。

336 第11章

HDFS的数据和元数据升级

如果采用前述方法来升级HDFS，且新旧HDFS的文件系统布局恰巧不同，则 namenode无法正常工作，在其日志文件中产生如下信息：

File system image contains an old layout version -16. An upgrade to version -18 is required.

Please restart NameNode with -upgrade option.

k可靠的判定文件系统升级是否必要的方法是在一个测试集群做实验

升级HDFS会保留前一版本的元数据和数据的复本，但这并不意味着需要两倍的 存储开销，因为datanode使用硬链接保存指向同一块的两个应用(分别为当前版本 和前一版本)，从而能够在需要时方便地回滚到前一版本。需要强调的是，系统回滚 到旧版本之后，原先的升级改动都将被取消。

用户可以保留前一个版本的文件系统，但无法回滚多个版本。为了执行HDFS数

据和元数据上的另一次升级任务，需要删除前一版本，该过程被称为“定妥升 级” (finalizing the upgrade)0 一旦执行该操作，就无法再回滚到前一个版本。

一般来说，升级过程可以忽略中间版本。但在某些情况下还是需要先升级到中间 版本，这种情况会在发布说明文件中明确指出。

仅当文件系统健康时，才可升级，因此有必要在升级之前调用方d工具全面检査 文件系统的状态(参见11.1.4节对方d工具的讨论)。此外，最好保留力d的输出报 告，该报告列举了所有文件和块信息；在升级之后，再次运行力d新建一份输出 报告并比较两份报告的内容。

在升级之前最好清空临时文件，包括HDFS的MapReduce系统目录和本地的临时 文件等。

综上所述，如果升级集群会导致文件系统的布局变化，则需要采用下述步骤进行 升级。

(1)    在执行升级任务之前，确保前一升级已经定妥。

(2)    关闭YARN和MapReduce守护进程。

(3)    关闭HDFS，并备份namenode目录。

(4)    在集群和客户端安装新版本的Hadoop。

(5)    使用-upgrade选项启动HDFS。

(6)    等待，直到升级完成。

(7)    检验HDFS是否运行正常。

(8)    启动YARN和MapReduce守护进程。

(9)    回滚或定妥升级任务(可选的)。

运行升级任务时，最好移除PATH环境变量下的Hadoop脚本，这样的话，用户就

不会混淆针对不同版本的脚本。通常可以为新的安装目录定义两个环境变量。在后

续指令中，我们定义了 OLD_HADOOP_HOME和NEW_HADOOP_ HOME两个环境变量。

启动升级为了执行升级，可运行以下命令(即前述的步骤5):

% $NEW_HADOOP_HOME/bin/start-dfs.sh -upgrade

该命令的结果是让namenode升级元数据，将前一版本放在dfs.namenode. name.dir下的名为prev/ows的新目录中。类似地，datanode升级存储目录，保留 原先的复本，将其存放在目录中。

等待，直到升级完成升级过程并非一蹴即就，可以用查看升级进度， 升级事件同时也出现在守护进程的日志文件中，步骤(6):

% $NEW_HADOOP_HOME/bin/hdfs dfsadmin -upgradeProgress status

Upgrade for version -18 has been completed.

Upgrade is not finalized.

查验升级情况显示升级完毕。在本阶段中，用户可以检查文件系统的状态，例

如使用一个基本的文件操作）检验文件和块 最好让HDFS进入安全模式（所有数据只读）， 11.1.2节安全模式的有关内容。

参见步骤（7）。检验系统状态时， 以防止其他用户修改数据。详见



回滚升级（可选的）如果新版本无法正确工作，可以回滚到前一版本，参见步骤 （9），前提是尚未定妥更新。

回滚操作会将文件系统的状态转回到升级之前的状态，同期所做的任何改变都 会丢失。换句话说，将回滚到文件系统的前一状态，而非将当前的文件系统降 级到前-版本。

首先，关闭新的守护进程：

% $NEW_HADOOP_HOIIE/bin/stop-dfs.sh

其次，使用-rollback选项启动旧版本的HDFS:

% $OLD_HADOOP_HOME/bin/start-dfs.sh -rollback

该命令会让namenode和datanode使用升级前的复本替换当前的存储目录。文件系 统返回之前的状态。

定妥升级（可选）如果用户满意于新版本的HDFS，可以定妥升级，参见步骤（9），以移除升级前的存储目录。

就再也无法回滚到前一版本



在执行新的升级任务之前，必须执行这一步:

% $NEW_HADOOP_HOME/bin/hdfs dfsadmin -finalizeUpgrade % $NEW_HADOOP_HOME/bin/hdfs dfsadmin •叩gradeProgress status

There are no upgrades in progress.

现在，HDFS已经完全升级到新版本了。
