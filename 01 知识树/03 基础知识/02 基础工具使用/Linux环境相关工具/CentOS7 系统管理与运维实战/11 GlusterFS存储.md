###### SS It afie 索11車

###### ◄ GlusterFS存猫►

GlusterFS是近年来兴起的一个开源分布式文件系统，其在开源社区活跃度很高，互联网 通常称其与MooseFS、CEPH、丄ustre为四大开源分布式文件系统。国外有众多互联网从业者 在研究、测试并使用GlusterFS，而国内目前正处于起步阶段，本章将简要介绍GlusterFS的部 署与应用。

本章主要涉及的内容有：

•    GlusterFS存储结构简介

•    GlusterFS部署与应用

1 I •! GlusterFS概述

GlusterFS最早由Cluster公司开发，其目标是开发出一个能为客户提供全局命名空间、分 布式前端及高达数百PB级别扩展性的开源分布式文件系统。相比其他分布式文件系统， GlusterFS具有高扩展性、高可用性、高性能、可横向扩展等特点，并且其没有元数据服务器 的设计，让整个服务没有单点故障的隐患。正是由于GlusterFS拥有众多优秀的特点，红帽公 司于2011年收购Gluster公司，并将GlusterFS作为其大数据解决方案的一部分。本节将简单 介绍分布式文件系统及GlusterFS =

11.1.1分布式文件系统

分布式文件系统(Distributed File System)是指文件系统管理的物理存储资源并不直接与 本地节点相联(即非直联存储)，而是分布于计算机网络中的一个或多个节点计算机上。目前 意义上的分布式文件系统大多都是由多个节点计算机构成的，结构上是典型的客户机/服务器 模式。流行的模式是当客户机需要存储数据时，服务器指引其将数据分散地存储到多个存储节 点上，以提供更快的速度、更大的容量及更好冗余特性。

目前流行的分布式文件系统有许多，如MooseFS、OpenAFS、GoogleFS等，下面将简要 介绍一些最常见的分布式文件系统。

\1. MooseFS

MooseFS主要由管理服务器(master)、元日志服务器(Metalogger)、数据存储服务器 (chunkservers)构成，管理服务器主要作用是管理数据存储服务器，文件读写控制、空间管 理及节点间的数据拷贝等；元日志服务器主要用来备份管理服务器的变化日志，以便管理服务 器出问题时能恢复工作；数据存储服务器主要工作是听从管理服务器调度，提供存储空间，接 收或传输客户数据等。MooseFS的读过程如图11.1所示。

![img](11 CentOS7fbdfa1060ed0f49e18-241.jpg)





00.0



CLIENTS



2 Th«<teu>t onXdiunh



CHUNK SERVERS



MASTER SERVER

图11.1 MooseFS读数据过程

如图11.1中的读取数据过程，客户首先向master询问数据存放在哪些数据存储服务器上， 然后再向数据存储服务器请求并获得数据。其写过程与读过程正好相反，如图11.2所示。

5 SynrtwoM<2e





2a Create new chunks on X



O O'O



3 S»««the4Ma on X ctwjnk

2b Success.

CHUNKSERVERS



CLIENTS



MASTER SERVER

图11.2 MooseFS写数据过程

写数据时，客户先向master发出请求，master查询剩余空间后将存储位置返回给客户， 由客户将数据分散地存放在数据存储服务器上，最后向master发出写入结束信号。

MooseFS结构简单，最适合初学者理解分布式文件系统的工作过程。但也存在较大问题， MooseFS具有单点故障隐患，一旦master无法工作，整个分布式文件系统都将停止工作。

\2. Lustre

Lustre是一个比较典型的高性能面向对象的文件系统，其结构相对比较复杂，如图11.3 所示。

Management    Metadata

Servers (MGSs) Servers (MDSs)

eee<



Object Storage Object Storage Servers (OSSs) Targets (OSTs)

oss 7

图 11.3 Lustre 结构



Lustre 由元数据服务器(Metadata Servers，MDSs)、对象存储服务器(Object Storage Servers, OSSs)和管理服务器(Management Servers，MGSs)组成。与MooseFS类似，当客户端读取 数据时，主要的操作集中在MDSs和OSSs间；写入数据时就需要MGSs、MDSs及OSSs共 同参与操作。

Lustre主要面对是的海量级的数据存储，支持多达10000个节点、PB级的数据存储、 100Gbit/s以上传输速度。在气象、石油等领域应用十分广泛，是目前比较成熟的解决方案之

\3. Ceph

Ceph的目标是建立一个容量可扩展至PB级、高可靠性，并且支持多种工作负载的高性 能分布式文件系统。其结构如图11.4所示。

CephFS Kernel Object |    |    CephFS FUSE

Ceph FS Library (libcephfs)

Ceph Storage Cluster Protocol (librados)

OSDs



MDSs



Monitors



图11.4 Ceph结构

Ceph主要由元数据服务器（MDSs）、对象存储集群（OSDs）和集群监视器组成，元数据 服务器主要用来缓存和同步分布式元数据；对象存储集群用来存储数据和元数据；监视器则用 来监视整个集群。Ceph在文件一致性、容错性、高性能、扩展性等方面都有显著的优势，特 别适合于云计算。

本小节简单介绍了最具代表性的几个分布式文件系统，但目前成熟的分布式文件系统还有 许多，例如GridFS、mogileFS、TFS、FastDFS等。读者可自行参考相关资料了解，此处不 再赘述。

11.1.2 GlusterFS 概述

GlusterFS与其他分布式文件系统相比，在扩展性、高性能、维护性等方面都具有独特优 势。本小节将简要介绍GlusterFS存储的特点。

1.无元数据设计

元数据是用来描述一个文件或给定区块在分布式文件系统中所在的位置，简而言之就是某 个文件或某个区块存储的位置。传统分布式文件系统大都会设置元数据服务器或功能相近的管 理服务器，主要作用就是用来管理文件与数据区块之间的存储位置关系。相较其他分布式文件 系统而言，GlusterFS并没有集中或分布式的元数据的概念，取而代之的是弹性哈希算法。集 群中的任何服务器、客户端都可利用哈希算法、路径及文件名进行计算，就可以对数据进行定 位，并执行读写访问操作。

这种设计带来的好处是极大地提高了扩展性，同时也提高了系统的性能和可靠性；另一显 著的特点是如果给定确定的文件名，查找文件位置会非常快。但如果需要列出文件或目录，性 能会大幅下降，因为列出文件或目录时，需要查询所在节点并对各节点中的信息进行聚合。此 时有元数据服务的分布式文件系统的查询效率反而会高许多。

2.服务器间的部署

在之前的版本中服务器间的关系是对等的，也就是说每个节点服务器都掌握了集群的配置 信息。这样做的好处是每个节点都拥有节点的配置信息，高度自治，所有信息都可以在本地查 询。每个节点的信息更新都会向其他节点通告，保证节点间信息的一致性。但如果集群规模较 大，节点众多时，信息同步的效率就会下降，节点间信息的非一致性概率就会大大提高。因此 GlusterFS未来的版本有向集中式管理变化的趋势。

GlusterFS还支持多种集群模式，组成诸如磁盘阵列状的结构，让用户在数据可靠性、冗 余程度等方面自行取舍。

3.客户端访问

当客户端访问GlusterFS存储时，其流程如图11.5所示。

图11.5客户端访问流程

首先程序通过访问挂载点的形式读写数据,对于用户和程序而言，集群文件系统是透明的， 用户和程序根本感觉不到文件系统是本地还是在远端服务器上。读写操作将会被交给VFS （Virtual File System,虚拟文件系统）来处理，VFS会将请求交给FUSE内核模块，而FUSE 又会通过设备/dev/fhse将数据交给GlusterFS Client。最后经过GlusterFS Client的计算，并最 终经过网络将请求或数据发送到GlusterFS Server上。

4.可性

GlusterFS在提供了一套基于Web GUI的基础上，还提供了一套基于分布式体系协同合作 的命令行工具，二者相结合就可以完成GlusterFS的管理工作。由于整套系统都是基于Linux 系统，在懂得Linux管理知识的基础之上，再加上2~3小时的学习就可以完成GlusterFS的日 常管理工作。这对一套分布式文件系统而言，GlusterFS的管理工作无疑是非常简便的。

作为一款获得红帽青睐的开源分布式文件系统，GlusterFS无疑有许多值得关注的地方。 本小节将介绍了其中一部分，其他方面的特点还有许多，此处不再赘述，读者可自行参阅相关 文档了解。

11.1.3 GlusterFS集群的模式

GlusterFS集群的模式是指数据在集群中的存放结构，类似于磁盘阵列中的级别。GlusterFS 支持多种集群模式，本小节将简要介绍几种常见的模式。

1.分布式GlusterFS卷

分布式GlusterFS卷(Distributed Glusterfs Volume)是一种比较常见的松散式结构，如图 11.6所示。

图11.6分布式GlusterFS卷

分布式GlusterFS卷的结构相对比较简单，存放文件时并没有特别的规则，仅仅是将文件 存放到组成分布式卷的所有服务器上。创建分布式卷时，如果没有特别的指定，将默认使用分 布式GlusterFSo这种卷的好处是非常便于扩展，且组成卷的服务器容量可以不必相同，缺点 是没有任何冗余功能，任何一个节点失败都会导致数据丢失。分布式GlusterFS卷需要在底层 硬件上做数据冗余，例如磁盘阵列RAID等。

2.复制 GlusterFS 卷

复制GlusterFS卷(Replicated Glusterfs Volume)同RAID 1类似，所有组成卷的服务器中 存放的内容都完成相同，其结构如图11.7所示。

图 11+7 复制 GlusterFS 卷"

复制GlusterFS卷的原理是将文件复制到所有组成分布式卷的服务器上。在创建分布式卷 时需要指定复制的副本数量，通常是2或者3,但副本数量一定要小于或等于组成卷的服务器 数量。由于复制GlusterFS卷会在不同的服务器上保存数据的副本，当其中一台服务器失效后， 可以从另一台服务器读取数据，因此复制GlusterFS卷提高了数据可靠性的同时，还提供了数 据冗余功能。

3.分布式复制GlusterFS卷

分布式复制 GlusterFS 卷(Distributed Replicated Glusterfs Volume)结合了分布式和复制 Gluster卷的特点，其结构如图11.8所示。

图11.8分布式复制GIusterFS卷

分布式复制GlusterFS卷的结构看起来类似RAID 10,但其实不同，RAID 10其实质是条 带化，但分布式复制GIusterFS卷则没有。这种卷实际上是针对数据冗余和可靠性要求都非常 高的环境而开发的。

4.条带化GIusterFS卷

条带化GIusterFS卷(Striped Glusterfs Volume)是专门针对大文件，多客户端而设置的， 如图11.9所示。

图11.9条带化GIusterFS卷

当GlusterFS被用来存储一些较大的文件时，如果仅保存在某个服务器上，当客户端较多 时，性能就会急剧下降。此时使用条带化的GlusterFS就可以解决这个问题，条带化Cluster 允许将体型较大的文件分拆并存放到多台服务器上，当客户端进行访问时就能分散压力，效果 如同负载均衡。条带化GlusterFS卷的缺点是不能提供数据冗余功能。

5.分布式条带化GlusterFS卷

分布式条带化GlusterFS卷(Distributed Striped Glusterfs Volume)被用来处理体型十分巨 大的文件，其结构如图11.10所示。

![img](11 CentOS7fbdfa1060ed0f49e18-253.jpg)





图11.10分布式条带化GlusterFS卷



当单个文件的体型十分巨大，客户端数量更多时，条带化GlusterFS卷已无法满足需要， 此时将分布式与条带化结合起来是一个比较好的选择。需要注意的是，无论是条带化GlusterFS 还是分布式条带化GlusterFS,其性能都与服务器数量有关。

![img](11 CentOS7fbdfa1060ed0f49e18-255.jpg)



由于本书并没有涉及大文件方面的内容，读者可阅读其官方网站的相关说明了解更多关于 条带化GlusterFS卷和分布式条带化GlusterFS卷，此处不再赘述。

1 1 «2 GlusterFS部署和应用

GlusterFS有扩展性强、高可靠性、高性能等诸多优点，同时又有红帽公司的大力支持， 使得当下许多Linux发行版和软件都已经包含并支持GlusterFS。本节将简要介绍GlusterFS在 CentOS 7中的部署和应用。

11.2.1 GlusterFS 安装

在开始GlusterFS安装之前，建议将系统升级至最新，最新的软件可以减少软件Bug、提

升软件的兼容性。由于GlusterFS需要使用网络，因此还必须事先根据环境设置防火墙规则、 SELinux规则等，本示例将不涉及这些设置，但在生产环境中应该特别注意。

在本例中将采用3台服务器作为示例，演示如何在CentOS 7中安装GlusterFS, 3台服务 器的信息如表11.1所示。

表11.1示例服务器信息

| 服务器  | IP地址       | 域名                  |
| ------- | ------------ | --------------------- |
| serverl | 172.16.45.43 | serverl .example.com  |
| server2 | 172.16.45.44 | server2. example, com |
| server3 | 172.16.45.45 | server3.example.com   |

由于此处仅为演示，因此硬件等方面几乎没有特殊要求，但在生产环境中使用时，应该尽 量选用性能相近的硬件配置，以避免个别服务器性能较差引发的短板效应。

1.环境设置

由于GlusterFS并没有服务器与元数据等概念，因此所有服务器的设置都相同。此处仅以 一台服务器的设置作为示例，其他服务器仅作IP地址、域名方面的修改即可。

首先需要作域名方面的设置，使用DNS作为解析手段有一定的延时，这在集群环境中可 能会带来一些问题，因此推荐使用hosts文件解析。服务器名设置及hosts文件解析如【示例 11-1】所示。

【示例11-1】

[root@serverl ~]# cat /etc/hostname

serverl.example.com

[root@serverl # cat /etc/hosts

127,0,0.1 localhost localhost.localdomain localhost4 localhost4.Iocaldomain4

::1    localhost localhost.localdomain localhost6 localhost6.Iocaldomain6

172.16.45.43    serverl server1.example.com

172.16.45.44    server2 server2.example.com

172.16.45.45    server3 server3.example.com

2.时钟同步

另一个问题是集群内部的时间非常重要，如果服务器间的时间有误差，可能会给集群间的 通信带来麻烦，进而导致集群失效。如果服务器能连接到互联网或内部的授时服务器，可以使 用网络同步时钟的方法。网络同步时钟使用命令ntpdate,如【示例11-2】所示。

【示例11-2】

\#参数time.windows.com是微软的授时服务器 [root@serverl # ntpdate time.windows.com

22 Jun 15:22:52 ntpdate[ 1989] : adjust time server 23.101.187.68 offset 0.069146

sec

手动使用命令同步比较麻烦，可以使用cron自动任务调度的方法。自动任务调度时，执 行命令“crontab-e”，会打开Vi编辑器，在其中输入【示例11-3】所示内容，保存退出即可。

【示例11-3】’

\#其含义为每天早上8点整执行后面的命令同步系统时间和硬件时钟 [root@serverl 〜]# crontab -1

08*** /usr/sbin/ntpdate time.windows.com &> /dev/null; /usr/sbin/clock -w

3.建立yum仓库

在GlusterFS的官方网站上（地址为：http://www.gluster.org/）,介绍了如何安装GlusterFS 的详尽过程，读者可以参考。由于GlusterFS提供了 yum源，因此可以在CentOS 7中使用yum 的方式安装，本例将釆用这种方法安装，安装过程如【示例11-4】所示。

【示例11-4]

\#下载源到目录/etc/yum. repos.d/

[root@serverl # wget -P /etc/yum.repos.d/ \

\>

<http://download.gluster.org/pub/gluster/glusterfs/LATEST/CentOS/glusterfs-epel> .repo

-2015-06-22 15:43:08--

<http://download.gluster.org/pub/gluster/glusterfs/LATEST/CentOS/glusterfs-epel> • repo

Resolving download.gluster.org (download.gluster.org)... 50.57.69,89 Connecting to download.gluster.org (download.gluster.org)150.57.69.89(:80... connected.

HTTP request sent, awaiting response" . 200 OK Length: 1055 (1.OK) [text/plain]

Saving to: r/etc/yum.repos.d/glusterfs-epel.repo1

100%[=======================================>] 1r 055    —.-K/s in Os

2015-06-22 15:43:16 (112 MB/s) - 1 /etc/yum.repos.d/glusterfs-epel.repo * saved [1055/1055]

\#安装支持软件包

[rootQserverl # rpm -ivh \

\> <http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm> Retrieving

<http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm> warning: /var/tmp/rpm-tmp.13qxVt: Header V3 RSA/SHA256 Signature, key ID

0608b895: NOKEY

Preparing...    ################################# [100%]

Updating / installing...

l:epel-release-6~8    ################################# [100%]

4.安装 GlusterFS

完成之前的环境设置、设置源等步骤后，就可以开始安装GlusterFS•了，其安装过程如【示 例11-5】所示。

【示例11-5】

\#部分安装过程省略

(root^serverl # yum install -y glusterfs glusterfs-fuse glusterfs—server

Loaded plugins: fastestmirror

Loading mirror speeds from cached hostfile

*    base: mirrors.btte.net

\*    epel: mirrors.neusoft.eciu.cn

\*    extras: mirrors.btte.net

\*    updates: mirtors^sina.cn Resolving Dependencies

\> Running transaction check

---〉Package glusterfs.x86_64 0:3.7.1-1.el7 will be installed

一一> Processing Dependency: glusterfs~libs - 3.7.1-1,el7 for package:

glusterfs-3    .7,1-1.el7.x86_64    •

一-> Processing Dependency: libglusterfs.so.0 () (64bit) for package:

glusterfs-3.7    * 1-1*el7.x86_64

到此，GlusterFS的安装就己经完成了，需要说明的是安装过程需要在每台服务器上都进 行一次。

11.2.2配置服务和集群

安装完成GlusterFS之后，还不能立即使用，还需要对服务进行配置。本小节将简单介绍 如何配置GlusterFS,

首先需要在3台服务器上分别启动相应的服务，如【示例11-6】所示。

【示例11-6】

[root@serverl ~3 # systemctl enable glusterd In -s */usr/lib/systemd/systemZglusterd.service1

4/etc/systemd/system/multi-user.target.wants/glusterd.service• [root@serverl # systemctl start glusterd

在两台服务器上启动服务后，就可以开始配置集群了。在配置集群之前，最好使用命令 ping各服务器的主机名，以确保域名与IP都已正确设置。配置集群过程如【示例11-7】所示。

【示例11-7】

\#将节点serverl和server2加入集群 [root@serverl -]# gluster peer probe serverl peer probe: success. Probe on localhost not needed [root@serverl # gluster peer probe server2 peer probe: success.

(rootQserverl # gluster peer probe server3 peer probe: success.

\#査看集群状态

[rootQserverl -]# gluster peer status Number of Peers: 2    一' +

Hostname: server2

Uuid: 26f40e95-935a-4445-b6e7-ea9e3fh34e2d State: Peer in Cluster (Connected)

Hostname: server3

Uuid: 85c9bdcc-68a4-4a43-bd4c-al6d95d02c76 Peer Cluster

从上面的示例输出中，可以看到服务和集群都已经配置完成。

11.2.3添加磁盘到集群

接下来就需要为集群添加磁盘了，需要注意的是各集群节点上的磁盘容量应该尽量相同。 添加磁盘到集群首先需要对磁盘分区、创建文件系统，如【示例11-8】所示。

【示例11-8】

\#对sda分区    ‘

\#此处仅以server2为例

\#其他节点也应做相同操诈'    '

[root@server2 〜]# fdisk /dev/sda

Welcome to fdisk (util~linux 2.23.2).

Changes will remain in memory only, until you decide to write them Be careful before using the write command.

\#分区方案为sdal容量为50GB    - _

Command (m for help): n

Partition type:

p primary (0 primary, 0 extended, 4 free) e extended

Select (default p): p    "

Partition number (1-4r default 1): 1

First sector (2048-209715199, default 2048):

Using default value 2048

Last sector, +sectors or +size{K,M,G} (2048—209715199, default 209715199) : +50G Partition 1 of type Linux and of size 50 GiB is set #建立第二个分区，剩余容量都分配给sda2

Command (m for help): n

Partition type:

p primary (1 primary, 0 extended, 3 free) e extended

Select (default p): p

Partition number (2-4, default 2):2

First sector (104859648-209715199, default 104859648):

Using default value 104859648

Last sector, ^sectors or+size{K,M,G} (104859648-209715199, default 209715199) Using default value 209715199

Partition 2 of type Linux and of size 50 GiB is set

\#写入分区方案

Command (m for help): w

The partition table has been altered!

Calling ioctl() to re-read Syncing disks.

\#创建文件系统

[root@server2 # mkfs.xfs m6ta-data=/dev/sdal



data =

=

naming =version 2 log ^internal log

realtime -none

[root@server2 〜]# mkfs.xfs meta-data=/dev/sda2



partition table.



/dev/sdal

isize=256 agcount=4, agsize=3276800 blks sectsz=512 attr=2z projid32bit=l crc=0    finobt=0

bsize=4096 blocks=l3107200, imaxpct=25 sunit=0 swidth=0 blks

bsize=4096 ascii-ci=0 ftype=0 bsize=4096 block琴=6400, version=2

sectsz=512 sunit=0 blks, lazy-count^l extsz-4096 blocks=0r rtextents~0

/dev/sda2

isiz6=256 agcount=4, agsize=3276736 blks sectsz«512 attr^, projid32bit=l crc=0    finobt-0

\#创建挂载点文件夹

[root@server2 # mkdir ~p /data/myDC

[root@server2 〜3 # #fc©sdal和 sda2 [root@server2 # [root@server2    #

\#创建集群挂载点

mkdir -p /data/mainDC

mount /dev/sdal /data/myDC/ mount /dev/sda2 /data/mainDC/



\#由于不能使用磁盘的挂载点，因此此处选择在磁盘挂载点下面新建挂戴点

[root@server2 〜]# mkdir ~p /data/myDC/brickl

[root@server2 # mkdir -p /data/mainDC/brick2

以上步骤需要在3台服务器上进行操作，磁盘可以不同，但建议分区方案挂载点名称等都 应该相同。

在本例中并没有将挂载信息写入/etc/fstab中，实际应用中应该写入配置文件，以便重新启 动后可用。

创建完磁盘之后，就可以为集群添加磁盘了，添加过程如【示例11-9】所示。

【示例11-9】

\#存serve,rl„t：添加磁盘

\#此处创建了一个名为myDC_clisk的GlusterFS■卷 [root@serverl *•] # gluster volume create myDC_disk \

\>    serverl:/data/myDC/brickO \

\>    server2:/data/myDC/brickO \

\>    server3:/data/myDC/brickO

volume create: myDC__disk: success: please start the volume to access data 1查看新建卷的情况

[root@serverl    # gluster volume info

Volume Name: myDC一 disk Type: Distribute

Volume ID: eb747bf6-f923--4bl5-b6a7-2cbd79e33666 Status: Created Number of Bricks: 3 '

Transport-type: tcp Bricks:

Brickl: serverl:/data/myDC/brickO Brick2: server2:/data/myDC/brickO Brick3: server3:/data/myDC/brickO Options Reconfigured:

performance. readdir-ahead: on ,

\#启动.磁盘

[root@serverl '~]# gluster volume start myDC_disk volume start: myDC_disk: success #为磁盘访问设置权限

\#允许172.16.45.0、24网络访问

[root@serverl •*]# gluster volume set myDC一disk auth.allow 172.16.45.* volume set: success

11.2.4添加不同模式的GlusterFS磁盘

为了应用于不同的环境,GlusterFS定义了多种模式，在11.1.3小节中简要介绍了 GlusterFS 的各种模式及应用环境。在本小节中将简单介绍如何创建各种模式的GlusterFS卷。

(1)分布式GlusterFS卷

创建GlusterFS卷时，如果不加任何参数，则默认创建分布式GlusterFS卷，如【示例11-9】 所示。

(2)复制 GlusterFS 卷

创建复制GlusterFS卷时，需要指定一个replica参数，即指定每个文件在GlusterFS卷中 复制的份数。由于此数值将决定文件在不同服务器中存放的份数，因此不能大于组成卷的服务 器数量。创建复制GlusterFS卷如【示例11-101所示。

【示例11-10】

\#指定文件在服务器中存放的份数为3

[root@serverl gluster volume create myDC一disk replica 3 \

\>    serverl:/data/myDC/brickO \

\>    server2:/data/myDC/brickO \

\>    server3:/data/rayDC/brickO

由于指定的份数为3且服务器数量为因此可以预见3台服务器中存放的文件是相同的。

(3)分布式复制GlusterFS卷

创建分布式复制GlusterFS卷同样也需要指定replica参数，由11.1.3小节中可以看出，当 replica参数为2时，需要的服务器数量至少为4台。当replica参数增加时，服务器数量也需 要相应地增加。创建命令如【示例11-11】所示。

【示例11-11】

\* [root@serverl ~3 # gluster volume create myDC一disk replica 2 transport tcp \

\>    serverl;/data/myDC/brickO \

\>    server2:/data/myDC/brickO \    .

\>    server3:/data/myDC/brickO \

\>    server4:/data/myDC/brickO

(4)条带化GlusterFS卷

创建条带化GlusterFS卷时，需要指定条带化参数stripe，与磁盘阵列中的条带化不同， 此处指定的是将文件分成几份存放，而不是每份大小。创建条带化GlusterFS卷至少需要2台 服斧器，创建命令如【示例11-12】所示。

【示例11-12】

[root@serverl 〜gluster volume create myDC一disk stripe 2 transport tcp \ > serverl:/data/myDC/brickO \

\> server2:/data/myDC/brickO \



![img](11 CentOS7fbdfa1060ed0f49e18-256.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-257.jpg)



(5)分布式条带化GlusterFS卷

分布式条带化GlusterFS卷同分布式复制GlusterFS卷类似，stripe值为2时，至少需要4 台服务器组成。创建命令如【示例11-13】所示。

【示例11-13】

[root@serverl -J # gluster volume create myDC_disk stripe 2 transport tcp \

\>    serverl:/data/myDC/brickO \

\>    servers:/data/myDC/brickO \

\>    server3:/data/myDC/brickO \

\>    server4:/data/myDC/brickO

11.2.5 在 Linux 中使用 GlusterFS 存储

在Linux系统中使用GlusterFS存储时，需要安装GlusterFS相关软件包。CentOS 7和 RHEL7之前的版本可以直接安装，CentOS 7和之前的版本还可以通过官方源安装，其他Linux 系统可通过编译安装的方式安装相关软件包。具体安装方法可参考GlusterFS的官方网站。

1.安装软件包

此处采用CentOS 7安装作为示例，其安装过程如【示例11-14】所示。

【示例11-141

\#下载软件源及安装支持的软件包

[root@server4 -]# wget -P /etc/yum.repos.dZ \

\>

<http://download.gluster.org/pub/gluster/glusterfs/LATEST/CentOS/glusterfs-epel> .repo

[root@server4 〜]# rpm -ivh \

<http://dl.fedoraproject.Org/pub/epel/6/x86_64/epel-release~6-8.noarch.rpm> #安装 glusterf s 及 glusterfs-fuse

[root@server4 # yum install ~y glusterfs glusterfs~fuse Loaded plugins: fastestmirror

epel/x86_64/metalink    ( 2.7 kB 00:00

epel ~    | 4.4 kB 00:00

glusterfs-epel    | 2.5 kB 00:00

glusterfs-noarch-epel    \ 2.9 kB 00:00

(1/4) : glusterfs-epel/7/x86_64/primary_cib (2/4): glusterfs-noarch~epel/7/primary一db (3/4): epel/x86_64/group_gz



I 13 kB 00:00 J 2.5 kB 00:00

I 149 kB 00:13

2.挂载远程存储

安装完相关的软件包之后，就可以挂载远程存储到本地了，挂载过程如【示例11-15】所示。

【示例11-15】

祐创建挂载点    ::MM—H

[root@server4 -]# mkdir -p /mnt/data #挂载远程存储

[root@server4 〜]# mount -t glusterfs server1:myDC一disk /mnt/data #想看挂载情况

| [root@server4                  | -]# df -h |      |       |                 |                |
| ------------------------------ | --------- | ---- | ----- | --------------- | -------------- |
| Filesystem                     | Size      | Used | Avail | Use% Mounted on |                |
| /dev/mapper/centos-root 13G 1. | 1G 12G    | 9% / |       |                 |                |
| devtmpfs                       | 488M      | 0    | 488M  | 0%              | /dev           |
| tmpfs                          | 497M      | 0    | 497M  | 0%              | /dev/shm       |
| tmpfs                          | 497M      | 6.6M | 491M  | 2%              | /run           |
| tmpfs                          | 4 97M     | 0    | 497M  | 0%              | /sys/fs/cgroup |
| /dev/vdal                      | 497M      | 139M | 35 9M | 28% /boot       |                |

server 1 :myDC一disk    150G    97M 150G 1% /mnt/data

3.榭圭载信息写入文件

【示例11-15】所示挂载将在系统重新启动后消失，如需让挂载继续生效可将挂载信息写 入文件/ etc/fstab：

播载信息    •希

[root@server4 ~]# cat /etc/fstab #

\#    /etc/fstab

\#    Created by anaconda on Thu Jun 25 OS;34:58 2015

server1:myDC_disk    /mnt/data    glusterfs    defaults    0

0

\#按文件/etc/fstab重新挂载    ••

[root®server4 *]# mount -a #查看挂载是否成功

[root@server4 # df ~h.

Filesystem    Size Used Avail Use% Mounted on

server 1:myDC_disk    150G 97M 150(3 1% /mnt/data

由于挂载时使用的是域名，因此需要在server4的/etc/hosts中写入其他GlusterFS服务器的 相关信息，.或者使用能解析的DNS。



##### 11 •:S小结

GlusterFS拥有很高的扩展性的同时，还兼具高可靠性、高性能等优势，是中小型企业在 分布式存储方面的又一选择。本章介绍了分布式存储及其特点，GlusterFS及特点，并着重介 绍了 GlusterFS的部署和应用。
