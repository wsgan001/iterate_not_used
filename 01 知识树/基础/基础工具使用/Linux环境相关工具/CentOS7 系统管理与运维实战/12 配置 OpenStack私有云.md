###### 配置OpenStack翮有云

OpenStack既是一个社区，也是一个项目和一个开源软件，它提供了一个部署云的操作平 台或工具集。其宗旨在于帮助组织和运行为虚拟计算或存储服务的云，为公有云、私有云，也 为大云、小云提供可扩展的、灵活的云计算。

本章主要涉及的知识点有：

•    OpenStack 概况

•    OpenStack系统架构

•    OpenStack主要部署工具

•通过 RDO 部署 OpenStack

參管理OpenStack

12.1 OpenStack 概况

OpenStack是一个免费的开放源代码的云计算平台，用户可以将其部署成为一个基础设施 即服务（Iaas）的解决方案。OpenStack不是一个单一的项目，而是由多个相关的项目组成， 包括Nova、Swift、Glance、Keystone以及Horizon等。这些项目分别实现不同的功能，例如 弹性计算服务、对象存储服务、虚拟机磁盘镜像服务、安全统一认证服务以及管理平台等。 OpenStack 以 Apache 许可授权。

OpenStack最早开始于2010年，作为美国国家航空航天局和Rackspace合作研发的云端运 算软件项目，目前，OpenStack由OpenStack基金会管理，该基金会是一个非营利组织，创立 于2012年。现在已经有超过200家公司参与了该项目，包括Arista Networks、AT&T、AMD、 Cisco、Delk EMC、HP、IBM、Intel、NEC、NetApp 以及 Red Hat 等大型公司。

OpenStack发展非常迅速，已经发布了 11个版本，每个版本都有代号，分别为Austin、 Bexar、Cactus、Diablo、Essex、Folsom、Grizzly、Havana、Icehouse、Juno 以及最新的 Kilo。

除了 OpenStack之外，还有其他的一些云计算平台，例如Eucalyptus>AbiCloud>OpenNebula 等，这些云计算平台都有自己的特点，关于它们之间具体的区别，请读者参考相关书籍，此处 不再详细说明。

1 2el' OpenStack 系统架构

由于OpenStack由多个组件组成，所以其系统架构相对比较复杂。但是，只有了解 OpenStack的系统架构，才能够成功地部署和管理OpenStack。本节将对OpenStack的整体系 统架构进行介绍。

12.2.1 OpenStack 体系架构

OpenStack由多个服务模块构成，表12.1-12.4列出了这些服务模块。

表12.1基本模块

| 项目名称 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| Horizon  | 提供了基于Web的控制台，以此来展示OpenStack的功能             |
| Nova     | OpenStack云计算架构的基础项目，是基础架构即服务(IaaS)中的核心模块。它负责 管理在多种Hypervisor上的虚拟机的生命周期 |
| Neutron  | 提供云计算环境下的虚拟网络功能                               |

表12.2存储模块

| 项目名称 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| Swift    | 提供了弾性可伸缩、髙可用的分布式对象存储服务，适合存储大规模非结构 化数据 |
| Cinder   | 提供块存储服务                                               |

表12.3共享服务

| 名称       | 说明                                 |
| ---------- | ------------------------------------ |
| Keystone   | 为其他的模块提供认证和授权           |
| Glance     | 存储和访问虚拟机磁盘镜像文件         |
| Ceilometer | 为计费和监控以及其他服务提供数据支撑 |

表12.4其他的服务

| 名称  | 说明                   |
| ----- | ---------------------- |
| Heat  | 实现弹性扩展，自动部署 |
| Trove | 提供数据库即服务功能   |

图12.1描述了 OpenStack中各子项目及其功能之间的关系，

第12章配置OpenStack私有云

图12.1各子项目与功能

图12.2描述了 OpenStack各功能模块之间的关系。

12.2.2 OpenStack 部署方式

针对不同的计算、网络和存储环境，用户可以非常灵活地配置OpenStack来满足自己的需

求。图12.3显示了含有3个节点的OpenStack的部署方案。

网络节点 （虚拟网络〉



计算节点 （计算节点1）



控制节点 （控制器）

| 支撑服务         |      |                   |      |
| ---------------- | ---- | ----------------- | ---- |
| 数据库服务 MySQL |      | 消息代理 RabbitMQ |      |

身份认证 Keystone

可选服务

块存储管理 Cinder



基本服务



基本服务



镜像文件 Glance



对象存慵 Swift



计算服务 Nova



数据库服务 Trove



网络服务 Neutron



自动化部署 Heat



控制台

Horizon



Telemetry

Ceilometer



网络接口 10.0.0.11/24



ML2插件 二层代理（OVS）

三层代理 DHCP代理



计算

KVM/QEMU



网络 ML2插件

二层代理（OVS）



可选服务



Telemetry Ceilometer 代理



1.管理：10.0.0.21/24 | 2.实例通道：10.0.1.21/2^~|



3.外部网络接口



网络接口



1.管理：10.0.0.31/24



2.实例通道：10.0.1.31/24



图12.3含有3个节点的OpenStack部署方案

在图12.3中，使用Neutron作为虚拟网络的管理模块，包含控制节点、网络节点和计算 节点，这3个节点的功能分别描述如下：

1.控制节点

基本控制节点运行身份认证服务、镜像文件服务、计算节点和网络接口的管理服务、虚拟 网络插件以及控制台等。另外，还运行一些基础服务，例如OpenStack数据库、消息代理以及 网络时间NTP服务等。

控制节点还可以运行某些可选服务，例如部分的块存储管理、对象存储管理、数据库服务、 自动部署（Orchestration）以及 Telemetry （Ceilometer）。

2.网络节点

网络节点运行虚拟网络插件、二层网络代理以及三层网络代理。其中，二层网络服务包括 虚拟网络和隧道技术，三层网络服务包括路由、网络地址转换（NAT）以及DHCP等。此外， 网络节点还负责虚拟机与外部网络的连接。

3.计算节点

计算节点运行虚拟化监控程序（Hypervisor）,管理虚拟机或者实例。默认情况下，计算节 点采用KVM作为虚拟化平台。除此之外，计算节点还可以运行网络插件以及二层网络代理。 通常情况下，计算节点会有多个。

12.2.3计算模块Nova

Nova是OpenStack系统的核心模块，其主要功能是负责虚拟机实例的生命周期管理、网 络管理、存储卷管理、用户管理以及其他的相关云平台管理功能。从能力上讲，Nova类似于 Amazon EC2。Nova逻辑结构中的大部分组件可以划分为以下两种自定义的Python守护进程:

(1)    接收与处理API调用请求的Web服务器网关接口(Python Web Server Gateway Interface, WSGI),例如 Nova-API 和 Glance-API 等。

(2)    执行部署任务的Worker守护进程，例如Nova-Compute> Nova-Network以及 Nova-Schedule 等。

消息队列（Queue）与数据库（Database）作为Nova的架构中的两个重要的组成部分，虽 然不属于WSGI或者Worker进程，但是两者通过系统内消息传递和信息共享的方式实现任务 之间、模块之间以及接口之间的异步部署，在系统层面大大简化了复杂任务的调度流程与模 式，是Nova的核心模块。

由于Nova采用无共享和基于消息的灵活架构，所以Nova的7个组件有多种部署方式。 用户可以将每个组件单独部署到一台服务器上，也可以根据实际情况，将多个组件部署到一台 服务器上。

下面给出了几种常见的部署方式。

\1.    单节点

在这种方式下，所有的Nova服务都集中在一台服务器上，同时也包含虚拟机实例。由于 这种方式的性能不高，所以不适合生产环境，但是部署起来相对比较简单，所以非常适合初学 者练习或者相关开发。

\2.    双3f?点

这种部署方式由两台服务器构成，其中一台作为控制节点，另外一台作为计算节点。控制 节点运行除Nova-Compute服务之外的所有的其他服务，计算节点运行Nova-Compute服务。 双节点部署方式适合规模较小的生产环境或者开发环境。

\3.    多节点

这种部署方式由用户根据业务性能需求，实现多个功能模块的灵活安装，包括控制节点的 层次化部署和计算节点规模的扩大。多节点部署方式适合各种对于性能要求较高的生产环境。

12.2.4分布式对象存储模块Swift

Swift是OpenStack系统中的对象存储模块，其目标是使用标准化的服务器来创建冗余的、 可扩展且存储空间达到PB级的对象存储系统。简单地讲，Swift非常类似于AWS的S3服务。 它并不是传统意义上的文件系统或者实时数据存储系统，而是长期静态数据存储系统。

Swift主要由以下3种服务组成：

(1)    代理服务：提供数据定位功能，充当对象存储系统中的元数据服务器的角色，维护 账户、容器以及对象在环(Ring)中的位置信息，并且向外提供API,处理用户访问请求。

(2)    对象存储：作为对象存储设备，实现用户对象数据的存储功能。

(3)    身份认证：提供用户身份鉴定认证功能。

OpenStack中的对象由存储实体和元数据组成，相当于文件的概念。当向Swift对象存储 系统上传文件的时候，文件并不经过压缩或者加密，而是和文件存放的容器名、对象名以及文 件的元数据组成对象，存储在服务器上。

12.2.5虚拟机镜像管理模块Glance

Glance项目主要提供虚拟机镜像服务，其功能包括虚拟机镜像、存储和获取关于虚拟机 镜像的元数据、将虚拟机镜像从一种格式转换为另外一种格式。

Glance主要包括两个组成部分，分别是Glance API和Glance Registry。Glance API主要提 供接口，处理来自Nova的各种请求。Glance Registry用来和MySQL数据库进行交互，存储 或者获取镜像的元数据。这个模块本身不存储大量的数据，需要挂载后台存储Swift来存放实 际的镜像数据。

12.2.6身份认证模块Keystone

Keystone是OpenStack中负责身份验证和授权的功能模块。Keystone类似一个服务总线， 或者说是整个OpenStack框架的注册表，其他服务通过keystone来注册其服务的端点 (Endpoint),任何服务之间相互的调用，都需要经过Keystone的身份验证，来获得目标服务 的端点来找到目标服务。

Keystone包含以下基本概念：

\1.    用户(User)

用户代表可以通过Keystone进行访问的人或程序。用户通过认证信息如密码、API Keys 等进行验证。

\2.    租户(Tenant)

租户是各个服务中的一些可以访问的资源集合。例如，在Nova中一个租户可以是一些机 器，在Swift和Glance中一个租户可以是一些镜像存储，在Quantum中一个租户可以是一些 网络资源。默认情况下，用户总是绑定到某些租户上面。

3.角色(Role)

角色代表一组用户可以访问的资源权限，例如Nova中的虚拟机、Glance中的镜像。用户 可以被添加到任意一个全局的或租户内的角色中。在全局的角色中，用户的角色权限作用于所 有的租户，即可以对所有的租户执行角色规定的权限；在租户内的角色中，用户仅能在当前租 户内执行角色规定的权限。

\4.    服务（Service ）

OpenStack中包含许多服务，如Nova、Glance、Swift。根据前三个概念，即用户、租户 和角色，一个服务可以确认当前用户是否具有访问其资源的权限。但是当一个用户尝试着访问 其租户内的服务时，该用户必须知道这个服务是否存在以及如何访问这个服务，这里通常使用 一些不同的名称表示不同的服务。

\5.    端点（Endpoint）

所谓端点，是指某个服务的URL。如果需要访问一个服务，则必须知道该服务的端点。 因此，在Keystone中包含一个端点模板，这个模板提供了所有存在的服务的端点信息。一个 端点模版包含一个URL列表，列表中的每个URL都对应一个服务实例的访问地址，并且具有 public、private和admin这三种权限。其中public类型的端点可以被全局访问，私有URL只 能被局域网访问，admin类型的URL被从常规的访问中分离。

12.2.7 控制台 Horizon    、

Horizon为用户提供了一个管理OpenStack的控制面板，使得用户可以通过浏览器，以图 形界面的方式就可以进行相应的管理任务，避免去记忆烦琐、复杂的命令。Horizon几乎提供 了所有的操作功能，包括Nova虚拟机实例的管理和Swift存储管理等。图12.4显示了 Horizon 的主界面，关于Horzon的详细功能，将在后面的内容中介绍。

admin    ;



管理员

I

測机簟職

云谈&

云主机类型



项目名称

admn

3示1个彔目



Q    - OpenStack I

4-    G \ Cl 58.64.138.219/dashboarrt/adm5n/

Q openstack

使用情况摘要

选择的一段吋间来査询其用量：

从 2014-05-01 a. 20105-24    日期采是懶式。

活衽的云主机1活妖的内存：512MB S_时期的的VCPU-小时教：3 63这一时期的GB.小时教：3.63 用量    ▲石载CSV摘要

虚拟内核

内存

512MB

途似内核小时教

3 63



磁盘

1



图12.4 Horizon主界面

12.3 Openstack主要部署工具

前面已经介绍过，OpenStack的体系架构比较复杂，对于初学者来说，逐个使用命令来安 装各个组件是一项非常困难的事情。幸运的是，为了简化OpenStack的安装操作，许多部署工 具已经被开发出来。通过这些工具，用户可以快速地搭建出一个OpenStack的学习环境。本节 将对主要的OpenStack部署工具进行介绍。

12.3.1 Fuel

Fuel是一个端到端一键部署OpenStack设计的工具，主要包括裸机部署、配置管理、 OpenStack组件以及图形界面等几个部分，下面分别进行简单介绍。

1.裸机部署

Fuel支持裸机部署，该项功能由HP的Cobbler提供。Cobbler是一个快速网络安装Linux 的服务，该工具使用python开发，小巧轻便，使用简单的命令即可完成PXE网络安装环境的

配置管理采用P叩pet实现。Puppet是一个非常有名的云环境自动化配置管理工具，采用 XML语言定义配置。Puppet提供了一个强大的框架，简化了常见的系统管理任务，大量细节 交给Puppet去完成，管理员只要集中精力在业务配置上。系统管理员使用Puppet的描述语言 来配置，这些配置便于共享。Puppet伸缩性强，可以管理成千上万台机器。

\3.    OpenStack 组件

除了可灵活选择安装OpenStack核心组件以外，还可以安装Monitoring和HA组件。Fuel 还支持心跳检查。

\4.    图形界面

Fuel提供了基于Web的管理界面Fuel Web,可以使用户非常方便地部署和管理OpenStack 的各个组件。

12.3.2 TripleO

TripleO 是另外_套 OpenStack 部署工具，TripleO 又称为 OpenStack 的 OpenStack (OpenStack OverOpenStack)。通过使用OpenStack运行在裸机上的自有设施作为该平台的基

础，这个项目可以实现OpenStack的安装、升级和操作流程的自动化。

在使用TripleO的时候，需要先准备一个OpenStack控制器的镜像，然后用这个镜像通过

OpenStack的Ironic功能再去部署裸机，再通过HEAT在裸机上部署OpenStack。

12.3.3    RDO

RDO （Red Hat Distribution of OpenStack）是由红帽公司推出的部署 OpenStack 集群的一 个基于Puppet的部署工具，可以很快地通过RDO部署一套复杂的OpenStack环境。如果用户 想在REHL上面部署OpenStack,最便捷的方式就是RDO。在本书中，就是采用RDO来介绍 OpenStack 的安装。

12.3.4    DevStack

DevSteck实际上是个Shell脚本，可以用来快速搭建OpenStack的运行和开发环境，特 别适合OpenStack开发者下载最新的OpenStack代码后迅速在自己的笔记本上搭建一个开发环 境。正如DevStack官方所强调的，devstack不适合用在生产环境。

12.4 通过RDO部署OpenStack

尽管OpenStack已经拥有了许多部署工具，但是在RHEL或者CentOS等操作系统上部署 OpenStack, RDO仍然是首选的方案。尤其对于初学者来说，使用RDO可以大大降低部署的 难度。本节将对使用RDO部署OpenStack进行详细介绍。

12.4.1部署前的准备

OpenStack对于软硬件环境都有一定的要求，其中RHEL是官方推荐的版本，另外，用户 也可以选择其他的基于RHEL的发行版，例如CentOS 6.5及之后的版本（包括CentOS 7）、 Scientific Linux 6.5或者Fedora 20以上。为了避免Packstack域名解析出现问题，需要把主机 名设置为完整的域名，来代替短主机名（注意如果不使用自建的DNS服务器，同时也要修改 /etc/hosts）。

硬件方面，OpenStack至少需要2GB的内存，CPU也需要支持硬件虚拟化，此外，至少 有一块网卡。

12.4.2配置安装源

为了保证当前系统的所有的软件包都是最新的，需要使用yum命令进行更新操作，命令 如下：

[rootQlocalhost 〜3# yum -y update

执行以上命令之后，yum软件包管理器会查询安装源，以验证当前系统中的软件包是否 有更新；如果存在更新，则会自动进行安装。由于系统中的软件包通常会非常多，所以上面的 更新操作可能会花费较长的时间。

接下来是配置OpenStack安装源，目前RDO的最新版本为IceHouse, RedH提供了一个 RPM软件包来帮助用户设置RDO安装源，其URL为：

<http://rdo.fedorapeople.org/openstack-icehouseZrdo-release-icehouse.rpm>

用户只要安装以上软件包即可，命令如下：

[rooteiocalhost -]# yum install -y

<http://rdo.fedorapeople.org/openstack-icehouse/rdo-release-icehouse.rpm>

执行以上命令之后，会为当前系统添加Foreman、Puppet Labs和RDO安装源，命令如下:

[root@localhost -]# 11 /etc/yum.repos.d/

total 32

![img](11 CentOS7fbdfa1060ed0f49e18-262.jpg)



| -rw-r——r——.1    | root | root | 707  | May  | 24 14:38     | foreman.repo |
| --------------- | ---- | ---- | ---- | ---- | ------------ | ------------ |
| - rw-r——r——.1   | root | root |      | 1220 | May 24 14:38 |              |
| puppetlabs.repo |      |      |      |      |              |              |
| -rw-r--r. 1     | root | root | 248  | May  | 24 14:38     | rdo-release. |

在正式开始安装之前，还需要妥善处理SELinux和防火墙等，以免安装过程中出现问题或 导致安装完成后无法访问。

12.4.3 安装 Packstack

在使用RDO安装OpenStack过程中，需要Packstack来部署OpenStack,所以，必须提前 安装Packstack软件包。Packstack的底层也是基于Puppet,通过Puppet部署OpenStack各组件。 Packstack的安装命令如下：

[root@localhost # yum -y install openstack-packstack

12.4.4 安装 OpenStack

Packstack提供了多种方式来部署OpenStack,包括单节点和多节点等，其中单节点部署最 简单。单节点部署方式中，OpenStack所有的组件都被安装在同一台服务器上面。用户还可以 选择控制器加多个计算节点的方式或者是其他的部署方式。为了简化操作，本节将选择单节点 部署方式。

Packstack提供了一个名称为packstack的命令来执行部署操作。该命令支持非常多的选项， 用户可以通过以下命令来查看这些选项及其含义：

[root@localhost 〜packstack --help

从大的方面来说，packstack命令的选项主要分为全局选项、vCenter选项、MySQL选项、 AMQP 选项、Keystone 选项、Glance 选项、Cinder 选项、Nova 选项、Neutron 选项、Horizon 选项、Swift选项、Heat选项、Ceilometer选项以及Nagios选项等。可以看出packstack命令 非常灵活，几乎为所有的OpenStack都提供了相应的选项。下面对常用的选项进行介绍。

\1.    -gen-answer-file

该选项用来创建一个应答文件(answer file),应答文件是一个普通的纯文本文件，包含了 packstack部署OpenStack所需的各种选项。

\2.    -answer-file

该选项用来指定一个已经存在的应答文件，packstack命令将从该文件中读取各选项的值。

\3.    -install-hosts

该选项用来指定一批主机，主机之间用逗号隔开。列表中的第1台主机将被部署为控制节 点，其余的部署为计算节点。如果只提供了一台主机，则所有的组件都将被部署在该主机上面。

\4.    -allinone

该选项用来执行单节点部署。

\5.    -os-mysql-install

该选项的值为y或者n,用来指定是否安装MySQL服务器。

\6.    -os-glance-install

该选项的值为y或者n,用来指定是否安装Glance组件。

\7.    -os-cinder-install

该选项的值为y或者n,用来指定是否安装Cinder组件。

\8.    -os-nova-install

该选项的值为y或者n,用来指定是否安装Nova组件。

\9.    -os-neutron-install

该选项的值为y或者n，用来指定是否安装Neutron组件。

\10. -os-horizon-install

该选项的值为y或者n，用来指定是否安装Horizon组件。

\11. -os-swift-install

该选项的值为y或者n，用来指定是否安装Swift组件。

\12. -os-ceilometer-install

该组件的值为y或者n，用来指定是否安装Ceilometer组件。

除了以上选项之外，对于每个具体的组件，packstack也提供了许多选项，不再详细介绍。 如果用户想在一个节点上面快速部署OpenStack,可以使用-allinone选项，命令如下：

(rootfilocalhost # packstack --allinone

如果想要单独指定其中的某个选项，例如下面的命令将采用单节点部署，并且虚拟网络 采用 Neutron：

[root@localhost # packstack allinone os-neutron-install-y

由于packstack的选项非常多，为了便于使用，packstack命令还支持将选项及其值写入一 个应答文件(Answer file)中。用户可以通过-gen-answer-file选项来创建应答文件，如下所示：

[root@localhost packstack ^^gen^answer-file openstack.txt

应答文件为一个普通的纯文本文件，' 包含了 packstack部署OpenStack所需的各种选项， 如下所示：

[rootSlocalhost 叫# cat openstack.txt J more [general]    '

\+ ^

\#    Path to a Public key to install on servers. If a usable key has not

\#    been installed on the remote servers the user will be prompted for a

\#    password and this key will be installed so the password will not be

\#    required again CONFIG_SSH_KEY=

\#    Set to *y1 if you would like Packstack to install MySQL CONF 工 G 一MYSQL_JNSTAL3>y

\#    Set to ’y1 if you would like Packstack to install OpenStack Image

\#    Service (Glance)    々4 :

CONFlG_GLANCE__INSTALL=y

\#    Set to * y1 if you would like Packstack to install OpenStack Block

\#    Storage (Cinder)

CONFIG_CINDER_INSTALL-y

用户可以根据自己的需要来修改生成的应答文件，以确定某个组件是否需要安装，以及 相应的安装选项。修改完成之后，使用以下命令进行安装部署：

[root@loealhost 〜H packstack answer-file openstack.txt

如果没有设置SSH密钥，在部署之前，packstack会询问参与部署的各主机的root用户的

密码，用户输入相应的密码即可。下面的代码是部分安装过程:

| [root@localhost    # packstack    answer-file   | openstack‘             | txt    |      |
| ----------------------------------------------- | ---------------------- | ------ | ---- |
| Welcome to Installer setup utility              |                        |        |      |
| Packstack changed given value to required value | /root/.ssh/id一rsa.pub |        |      |
| Installing:                                     |                        |        |      |
| Clean Up                                        | (                      | DONE   | ]    |
| root@58.64.138,2191s password:                  |                        |        |      |
| Setting up ssh keys                             | (                      | DONE   | 1    |
| Discovering hosts1 details                      | (                      | DONE   | ]    |
| Adding pre install manifest entries             | C                      | DONE   | ]    |
| Adding MySQL manifest entries                   | [                      | DONE   | ]    |
| Adding AMQP manifest entries                    | (                      | DONE： | 1    |
| Adding Keystone manifest entries                | fcS餐                  | DONE   | ]    |
| Adding Glance Keystone manifest entries         | [                      | DONE   | )    |
| Adding Glance manifest entries                  | [                      | DONE   | ]    |
| Installing dependencies for Cinder......        | [                      | DONE   | ]    |

![img](11 CentOS7fbdfa1060ed0f49e18-263.jpg)



整个安装过程需费较长的0^,与用户选择的组件、网gpi机的硬＜牛配置情况密q

相关，一般为20-50分钟。如果在安装的过程中，由于网络原因导致安装失败，可以再次 执行以上命令重新安装部署。

当出现以下信息时，表示安装完成:

Finalizing    [ DONE ]

■ / . . 『. .    . . . . ■ . . • . . . - • ~ ■ ■ ■ ■'

**** Installation completed successfully ******

Additional information:

\*    Time synchronization installation was skipped. Please note that

unsynchronized time on server instances might be problem for some Open$tack components,    ’    ：，

\*    File /root/keystonerc一admin has been created on OpenStack client host 58.64.138.219. To use the command line tools you need to source the file.

\*    To access the OpenStack Dashboard browse to http: Z/58.64.138.219/dashboard . Please, find your login credentials stored in the keys toner e 一 admin in your home

directory.

\*    To use Nagios, browse to <http://58.64.138.219/nagios> username : nagiosadmin, password : bcb0bc9462fd4blto

\*    Because of the kernel update the host 58.64»138;219 requires reboot.

\*    The installation log file is available at: /var/tmp/packstack/20140524-153355~0XmeUf/openstack-setup.log

\* The generated manifests are available at: /var/tmp/packstack/20140524-153355-0ImeUf/manifests

在上面的信息中，除了告诉用户已经安装部署完成之外，还有其他的一些附加信息，这些 信息包括提醒用户当前主机上面没有安装NTP服务，因此，时间同步的相关配置被跳过去了； 脚本文件/root/keystonerc_admin已经被创建了，如果用户需要使用命令行工具来配置 OpenStack，则应该首先使用source命令读取并且执行其中的命令；用户可以通过 [http://58.64.138.219/dashboard](http://58.64.138.219/dashboard%e6%9d%a5%e8%ae%bf%e9%97%aeDashboard,%e5%8d%b3%e6%8e%a7%e5%88%b6%e5%8f%b0%ef%bc%8c%e7%99%bb%e5%bd%95%e4%bf%a1%e6%81%af%e5%ad%98%e5%82%a8%e5%9c%a8%e7%94%a8%e6%88%b7%e4%b8%bb%e7%9b%ae%e5%bd%95%e4%b8%ad%e7%9a%84)[来访问Dashboard,即控制台，登录信息存储在用户主目录中的](http://58.64.138.219/dashboard%e6%9d%a5%e8%ae%bf%e9%97%aeDashboard,%e5%8d%b3%e6%8e%a7%e5%88%b6%e5%8f%b0%ef%bc%8c%e7%99%bb%e5%bd%95%e4%bf%a1%e6%81%af%e5%ad%98%e5%82%a8%e5%9c%a8%e7%94%a8%e6%88%b7%e4%b8%bb%e7%9b%ae%e5%bd%95%e4%b8%ad%e7%9a%84) keystonerc admin 文件里面；用户可以通过 <http://58.64.138.219/nagios> 来访问 Nagios，并给出 了用户名和密码。此外还有一些安装日志文件的位置信息。

由于CentOS 7使用yum源的关系，安装某些组件时可能会失败，例如mariaDB，此时只 需手动将其安装好并设置其访问权限继续安装即可。具体细节可参考mariaDB相关文档了解,

此处不再赘述。

![img](11 CentOS7fbdfa1060ed0f49e18-264.jpg)



每次使用--allinone选项来安装OpenStack都会自动创建一个应答文件。因此如果在安 装过程中出现了问题，重新执行单节点安装时，应该使用-answer-file指定自动创建的 应答文件。

#### 12.5



理 OpenStack



OpenStack提供了许多命令行的工具来管理配置各项功能，但是这需要记忆大量的命令和 选项，对于初学者来说，其难度非常大。通过Horizon控制台，则可以非常方便地管理OpenStack 的各项功能，对于初学者来说，是一个便捷的途径。本节主要介绍通过控制台管理OpenStack。

12.5.1登录控制台

安装成功之后，用户就可以通过浏览器来访问控制台，其地址为主机的ip地址加上 dashboard,例如，在本例中，主机的IP地址为58.64.138.219,所以其默认的控制台网址为：

<http://58.64.138.219/dashboatd>    -

控制台登录界面如图12.5所示。

♦- C ，58 64.n8719/ddshtxbv<l    ‘t 瘙 R S

.-.............:.....•........-vu.4.ai5ffl1a»aaa«fc41aiasi&«saiKxi4ajt/iii,»iiii*-\___-.v.-.s-.-jc.-.

|                          |           |      |
| ------------------------ | --------- | ---- |
| j                        | a         |      |
|                          | openstack |      |
| 登录                     |           |      |
| 囲户私i! :.............. |           |      |

图12.5控制台登录界面



在上面的一节中，当OpenStack部署的最后，告诉用户控制台的登录信息位于用户主目录 的keystonerc admin文件中，所以可以使用以下命令查看该文件的内容：

[root@localhost # more keystonerc一admin

export OS_USERNAME=admin

export OS一TENANT—NAME^admin

export OS_PASSWORDs=191ae4adl2da48cie

export OS__AUTH_URL=http://58.64,138.219:5000/v2.0/

exP°rt PSl='[\u@\h \W(keystone_adInin)3\$ '

在上面的代码中，OS_USERNAME就是控制台的用户名，而OS_PASSWORD则是控制 台的登录密码，这个命名由Packstack自动生成，所以比较复杂。

登录成功之后，会出现控制台主界面，如图12.6所示。左侧为导航栏，分为“项目”和 “管理员”两大菜单项。如果使用普通用户登录，则只出现“项目”菜单项。

图12.6控制台主界面



“项目”菜单项中包含了用户安装的各组件，二级菜单根据用户选择的组件有所变化。在 本例中，包含了计算、网络和对象存储3个菜单项。其中“计算”菜单项中包含了与计算节点 有关的功能，例如实例、云硬盘、镜像，以及访问和安全等。“网络”则包含了网络拓扑、虚 拟网络以及路由等。“对象”主要包含容器的管理。

“管理员”菜单项包含与系统管理有关的操作，主要有“系统面板”和“认证面板”两个菜 单项，“系统面板”包含了 “虚拟机管理器”、“主机集合”、“实例”以及“云磁盘”等菜单项。其 中，用户可以通过“系统信息”菜单项来查看当前安装的服务及其主机，如图12.7所示。

，’« O*< * 'B«»* •    »    2 fi|c« « ' |fc« >



令 C „ «.M.l38219/：i.:sJuvwid/admin/info/ ««    ! 系统饴.S.

番



曜P

眼务

««

mutron

tfarc.

n<fc*_»c2

AM*

K*yste«a



w人？I    rj»5*



■n

com<iul«

natwoik

*oknn»i?

sJ

•nag.

•cj

JtortHV ("Mi'* (MU)



生龍

SIM 1.W719

68WJMJW

S«C4 13«21S

13G2I9

S8M：3«21«

S9 61

M«T»7n

MS4 13Sa)9

S8 6*136 219



■s

Emelrtf

En*W*il

EnaiM

EixWM

En«M»<

&ISOM

EraUfd

FnaWtd

EnMMd



图12.7所安装的OpenStack服务及其主机

“认证面板”主要与用户认证有关，包含“项目”和“用户”两个菜单项，其中项目实际 上指的就是租户，而用户指的是系统用户。

12.5.2用户设置

单击主界面右上角的用户名对应的下拉菜单，选择“设置”命令，打开“用户设置”窗口， 如图12.8所示。

O*甲"H 1■- ■廣氏，a    * Cr,f* M '    »■» » S* 幽■嚷:    _

s

| Q openstack | admin    'r    |      |           | «a； |
| ----------- | -------------- | ---- | --------- | ---- |
| !           | •    户设置    |      |           |      |
| 汲员        |                |      |           |      |
| ;：         | 用户设罝       |      |           |      |
| j isn       | -              |      |           |      |
|             | a翥.           |      | 说明，    |      |
| \|\|用户佘z | 灣体屮文lzh<nj | •    | 勺m户tf改 |      |
| tf疔        | MS             |      |           |      |
|             | UTC            |      |           |      |
|             | 百教20         |      |           |      |

用户可以设置“语言”和“时区”等选项。单击左侧的“修改密码”菜单项，打开“修改 密码”窗口，输入当前的密码，就可以修改用户密码，如图12.9所示。

![img](11 CentOS7fbdfa1060ed0f49e18-266.jpg)



不过目前来说，用户设置里的语言和时区的设置，只是保存在Cookie里面，并没有存储 在数据库里。默认语言是根据浏览器的语言来决定的，用户的个性化的设置，都是无法保存。 因为目前Keystone无法存放这些数据，所以用户也无法修改邮箱，也就导致无法实现取回密 码功能。

12.5.3管理用户

在“管理员”菜单中，选择“用户”菜单项，窗口的右侧列出了当前系统的各个用户，如 图12.10所示。

Qist x 0 m ■»

. C ： 58.64.138.219/cla'4>bo.«<J/jdiTi»>/useis/



w.nY

openstack



系快曲K 认证曲K

项a



odmln

用户

用户



□    用户名    麯嚷

E3 admin    coni

neutron    neution@)localhos>

t?    swift    iwft@,ccaAwst

glance    gianc«@ioca8w$t

IS    tfeme

jS    cinder    ciml«»@loca!h<ist

Lj    aSt_demo



:纖睡，:：

么蜱B



0,    *>»    + 触 mr*    EE3E9

通

爾戸10    S    动作

6c321»a16d«66»41W7fiS328c3«b    True    B 多

?5b1«0^f ,(M530be451fi62(Jbe411be    Tn»    !««    B 乡

94c2«22(D37al1a29c19238CJOaJ4000 True    !«組更多



单击右侧的“编辑”菜单，可以修改当前的用户。选择某个用户左侧的复选框，然后单击 “删除用户”按钮，可以将选中的用户删除。单击“创建用户”按钮，可以打开“创建用户" 对话框，如图12.11所示。在“用户名”、“邮箱”、“密码”以及“确认密码”等文本框中输入 相应的信息，选择“主项目”和“角色”之后，单击“创建用户”按钮即可完成用户的创建。

O' 9-    'H=    H-    'V s-    釅，“.…广:一一

图I2.ll创建用户

12.5.4管理镜像

用户可以管理当前OpenStack中的镜像文件。前面已经介绍过，Glance支持很多格式，但 是对于企业来说，其实用不了那么多格式。用户可以自己制作镜像文件，也可以从网络上面下 载已经制作好的镜像文件。以下网址列出了常用的操作系统的镜像文件：

http: //openstack. redhat.com/Image_resources

下面以CentOS 6.5为例，说明如何创建一个镜像。

(1)    进入“管理员”一 “系统面板”，选择“镜像”菜单项，右侧列出了当前系统中的镜 像，如图12.12所示。

(2)    单击右上侧的“创建镜像”按钮，打开“创建一个镜像”窗口，如图12.13所示。

在“名称”文本框中输入镜像的名称，例如CentOS 6.5,在“描述”文本框中输入相应的 描述信息，在“镜像源”下拉菜单中选择“镜像地址”选项，在“镜像地址”文本框中输入 CentOS 6.5镜像文件的地址为：

http://repos•fedorapeople.org/repos/openstack/guest-images/centos-6.5-20140 117.0.x86_64.qcow2

“格式化”下拉菜单选择相应的文件格式在本例中选择“QCOW2-QEMU模拟器”选 项。选中“公有”复选框，如果不是生产环境，其他的选项可以保留默认值。

图12.12镜像列表

图12.13创建镜像

(3)    单击"创建镜像”按钮，关闭窗口。在镜像列表中列出了刚才创建的镜像，其状态 为 Saving o

(4)    由于需要把整个镜像文件下载下来，所以需要较长的时间。到镜像的状态变成Active 时，表示镜像已经创建成功，处于可用状态，如图12.14所示。

图12.14镜像创建成功

对于其他的镜像文件，用户可以采用类似的步骤来完成创建操作。

如果用户想要修改某个镜像的信息，可以单击相应行的右侧的“编辑”按钮，打开“上传

镜像”对话框，如图12.15所示。

图12.15修改镜像信息

修改完成之后，单击右下角的“上传镜像文件”按钮关闭对话框。

如果用户不再需要某个镜像文件，可以单击右侧的“更多”按钮，选择“删除镜像”命令， 即可将该镜像文件删除。

12.5.5管理云主机类型

云主机类型(Flavors)实际上对云主机的硬件配置进行了限定。进入“管理员”菜单里面 的“系统面板”，单击“云主机类型”菜单项，窗口的右侧列出了当前已经预定义好的主机类 型，如图12.16所示，从图中可以得知，系统默认已经内置了 5个云主机类型，分别是ml.tiny、 ml.small、ml.medium, ml.large和ml.xlarge。从表格中可以看出，这5个内置的类型的硬件 配置是从低到高的，主要体现在CPU的个数、内存以及根磁盘这3个方面。

图12.16云主机类型

这5个类型已经基本满足用户的需求。如果用户需要其他配置的主机类型，则可以创建新 的主机类型。下面介绍创建新的主机类型的步骤。

(1)    单击图12.16中右上角的“创建云主机类型”按钮，打开“创建云主机类型”窗口。 在“名称”文本框中输入主机类型的名称，如ml.lg, ID文本框保留原来的auto,表示自动 生成ID。虚拟内核实际上指的是云主机CPU的个数，在本例中输入2。内存以MB为单位， 在本例中输入1024,根磁盘的容量以GB为单位，在本例中输入10。临时磁盘和交换盘空间 都为0，如图12.17所示。

(2)    单击窗口上面的“云主机类型访问”，切换到“云主机类型访问”选项卡。在窗口的 左侧列出了当前系统中所有的租户，右侧则列出了可以访问该主机类型的租户。单击某个租户 右侧的O按钮，将该租户添加到右侧，赋予该租户使用该类型的权限，如图12.18所示。

(3)设置完成之后，单击右下角的“创建云主机类型”按钮，完成主机类型的创建。

除了添加主机类型之外，用户还可以修改主机类型的信息、修改使用权以及删除主机类型。

这些操作都比较简单，不再详细说明。

OpenStac> K

创建云主机类型

云主倾2?值恩

fl;可以&这里6®题的云主机类型来组织.买例资S ■

©

曲M内検

内存M8

祺秘ftGR

10

磁盘

番空HMB

:o

创建丢i极类2?

图12.17创建主机类型

图12.18指定云主机类型的访问权限

346

12.5.6管理网络

Neutron是OpenStack核心项目之一，提供云计算环境下的虚拟网络功能。Neutron的功能 日益强大，并在Horizon面板中已经集成该模块。为了能够使得读者更好地掌握网络的管理， 下面首先介绍一下Neutron的几个基本概念。

\1.    网络

在普通人的眼里，网络就是网线和供网线插入的端口，一个盒子会提供这些端口。对于网 络工程师来说，网络的盒子指的是交换机和路由器。所以在物理世界中，网络可以简单地被认 为包括网线、交换机和路由器。当然，除了物理设备，还有软件方面的组成部分，例如IP地 址、交换机和路由器的配置和管理软件以及各种网络协议。要管理好一个物理网络需要非常多 的网络专业知识和经验。

Neutron网络目的是划分物理网络，在多租户环境下提供给每个租户独立的网络环境。另 外，Neutron提供API来实现这种目标。Neutron中“网络”是一个可以被用户创建的对象， 如果要和物理环境下的概念映射的话，这个对象相当于一个巨大的交换机，可以拥有无限多个 动态可创建和销毁的虚拟端口。

\2.    端口

在物理网络环境中，端口是用于连接设备进入网络的地方。Neutron中的端口起着类似的 功能，它是路由器和虚拟机挂接网络的着附点。

\3.    路由器

和物理环境下的路由器类似，Neutron中的路由器也是一个路由选择和转发部件。只不过 在Neutron中，它是可以创建和销毁的软部件。

\4.    子网

简单地说，子网是由一组IP地址组成的地址池。不同子网间的通信需要路由器的支持， 这个Neutron和物理网络下是一致的。Neutron中子网隶属于网络。图12.19描述了一个典型 的Neutron网络结构。

在图12.19中，存在一个和互联网连接的Neutron外部网络。这个外部网络是租户虚拟机 访问互联网或者互联网访问虚拟机的途径。外部网络有一个子网A，它是一组在互联网上可寻 址的IP地址。一般情况下，外部网络只有一个，且由管理员创建和管理。租户网络可由租户 任意创建。当一个租户的网络上的虚拟机需要和外部网络以及互联网通信时，这个租户就需要 一个路由器。路由器有两种臂，一种是网关（gateway）臂，另一种是网络接口臂。网关臂只 有一个，连接外部网。接口臂可以有多个，连接租户网络的子网。

图12.19典型的Neutron网络结构

对于图12.19所示的网络结构，用户可以通过以下的步骤来实施：

(1)    首先管理员拿到一组可以在互联网上寻址的IP地址，并且创建一个外部网络和子网。

(2)    租户创建一个网络和子网。

(3)    租户创建一个路由器并且连接租户子网和外部网络。

(4)    租户创建虚拟机。

接下来介绍如何在控制台中实现以上网络。管理员登录控制台，选择“管理员”面板，单 击“网络”菜单项后显示当前网络列表，如图12.20所示。

图12.20网络列表

从图12.20中可以得知，OpenStack已经默认创建了一个名称为public的外部网络，并且 己经拥有了一个名称为public_subnet,网络地址为172.24.4.224/28的子网。

单击右上角的“创建网络”按钮，可以打开“创建网络”窗口，创建新的外部网络，如图 12.21所示。

图12.21创建网络

尽管Neutron支持多个外部网络，但是在多个外部网络存在的情况下，其配置会非常复杂， 所以不再介绍创建新的外部网络的步骤，而是直接使用已有的名称为public的外部网络。在网 络列表窗口中，单击网络名称就可以查看相应网络的详细信息，如图12.22所示。

图12.22 public的网络详情

可以看到，网络详情主要包含3个部分，分别是网络概况、子网和端口。网络概况部分描 述了外部网络的重要属性，例如名称、ID、项目ID以及状态等。子网部分列出了该网络划分 的子网，包含子网名称、网络地址以及网关等信息。用户可以添加或者删除子网。端口部分列 出了网络中的网络接口，包括名称、固定IP、连接设备以及状态等信息。管理员可以修改端 口的名称，但是不能删除端口。

前面已经介绍过，除了外部网络之外，还有租户网络。租户网络主要包括子网、路由器等， 租户可以创建、删除属于自己的网络、子网以及路由器等。下面介绍如何管理租户网络。

(1)以普通用户demo登录控制台，在左侧的菜单中选择“网络”一“网络”，页面右侧 列出了当前系统中可用的网络列表，如图12.23所示。

图12.23 demo用户可用的网络

(2)单去“创建网络”按钮，打开“创建网络”窗口，如图12.24所示。在“网络名称” 文本框中输入网络的名称，例如Private3,单击“下一步”按钮，进入下一个界面。

图12.24设置网络名称

(3)如果需要创建子网，则选中“创建子网”复选框，在“子网名称”文本框中输入子 网的名称，例如private_subnet2,在“网络地址”文本框中输入子网的ID,例如192.168.21.0/24, 在“IP版本”下拉菜单中选择“IPv4”选项，在“网关IP”文本框中输入子网网关的IP地址, 例如192.168.21.1，如图12.25所示。单击“下一步”按钮，进入下一个界面。

图12.25设置子网

(4)选中“激活DHCP”复选框，在“分配地址池”文本框中输入DHCP地址池的范围， 例如192.168.21.2~192.168.21.128,在“DNS域名解析服务”文本框中输入DNS服务器的IP 地址，如图12.26所示。单击“已创建”按钮，完成网络的创建。

图12.26设置DHCP服务

通过上面的操作，租户己经创建了一个新的网络，但是这个网络还不能与外部网络连通。 为了连通外部网络，租户还需要创建和设置路由器。下面介绍如何通过设置路由器将新创建的 网络连接到外部网络。

(1)以demo用户登录控制台，选择“网络"一 “路由”菜单，窗口右侧列出当前租户 可用的路由器，如图12.27所示。

图12.27租户路由器列表



在图12.27中列出了一个名称为router 1的路由器，该路由器为安装OpenStack时自动创 建的路由器。从图中可以得知，该路由器已经连接到名称为public的外部网络。

(2)单击路由器名称，打开“路由详情”窗口，如图12.28所示。该窗口主要包括路由 概览和接口两个部分，路由概览部分列出了路由器的名称、ID、状态和外部网关等信息。接 口部分列出了该路由器所拥有的连接到内部网络的接口。

4-    C ，': S8.&4.138.219 J.-.,-: ■    : 0 彐

Q openstack    .    較 4

| 哦目    - |      |
| --------- | ---- |
| Compute   | » 1  |
| :啪       | ，\  |
| 哪拓朴    |      |

I触

进象存tt



路由详情 路由概贫:rouuri

名称

routeri

ID

8477t)94a-998l-485f-&883-68t11026ca98

扶态 ACTIVE

外薄网蛑哦

puWc

揸口

接口    > miiE) gRSEa

|                 | 酷IP       |        | 紐      | 货■员贿 | 动作 |
| --------------- | ---------- | ------ | ------- | ------- | ---- |
| @    (351S3944) | 192168.1.1 | ACTIVE | 内部相□ | UP      |      |
| ®    (55455ae8» | 10.0.0.1   | ACTIVE | 內鵬□   | UP      |      |

a示2个帝目

图12.28路由详情页面

(3)单击“增加接口”按钮，打开“增加接口”对话框，如图12.29所示。在“子网” 下拉菜单中选择刚刚创建的网络private3的子网private_subnet2，“IP地址”文本框中输入接 口的IP地址，例如192.168.21.1,单击“增加接口”按钮，关闭对话框。

图12.29增加接口



现在这个租户的路由器已经连接了外网和租户的子网，接下来这个租户可以创建虚拟机， 这个虚拟机借助路由器就可以访问外部网络甚至互联网。选择“网络”一“网络拓扑”菜单， 可以查看当前租户的网络拓扑结构，如图12.30所示。



♦ G . S8.64.138.219/ :.    :    「：.'•:、-

Oop^stack

*a    • R籍拓1b

■小■正*



a三

der»*- JWi



Coavwfe



抽9itS 田



,R*8W!

供由

P 163 1

ia o.q

图12.30 demo租户的网络拓扑结构

从图12.30可以得知，demo租户拥有3个网络，其名称分别为private、private2和private3, 其网络地址分别为10.0.0.0/24、192.168.1.0/24以及192.168.21.0/24,每个子网中都有几台虚拟 机。这3个网络分别连接到路由器routerl的3个接口上面，接口的IP地址分别为10.0.0.1、 192.168.1.1和192.168.21.1。实际上，这3个网络接口分别充当3个网络的网关。路由器routerl 的另外一个接口连接到外部网络public。

12.5.7管理实例

所谓实例(instance),实际上指的就是虚拟机。之所以称为实例，是因为在OpenStack中， 虚拟机总是从一个镜像创建而来的。下面介绍如何管理实例。

以demo用户登录控制台，进入“Compute” 一 “实例”菜单，窗口右侧列出当前租户所 拥有的实例，如图12.31所示。

图12.31实例列表

单击右上角的“启动云主机”按钮，打开“启动云主机”对话框，如图12.32所示。在“云 主机名称”文本框中输入主机名称，例如webserver。在“云主机类型’叩拉菜单中选择“ ml .small” 选项，创建一个CPU、20GB的硬盘以及2GB内存的虚拟机。“云主机数量”文本框中输入1， 即只创建一个虚拟机。“云主机启动源”下拉菜单选择“从镜像启动”选项，“镜像名称”下拉 菜单选择“cirros(12.5MB)”选项。

图12.32创建云主机

切换到“访问&安全”选项卡，如图12.33所示。在“值对”下拉菜单中选择一个密钥 对作为访问虚拟机的方式。选中“安全组”中的“default”选项。



图12.33选择密钥对



如果目前还没有密钥对，则可以单击右侧的+按钮，打开“导入密钥对”对话框，如图 12.34所示。

![img](11 CentOS7fbdfa1060ed0f49e18-288.jpg)



在“密钥对名称”文本框中输入密钥对的标识，例如key2。然后在终端窗口中执行以下 命令：

[root@localhost # ssh-keygen -t rsa -f cloud.key

![img](11 CentOS7fbdfa1060ed0f49e18-289.jpg)



以上命令会创建一个名称为cloud.key的私钥文件以及名称为cloud.key.pub的公钥文件。 然后使用以下命令打开公钥文件：

[root@localhost # cat cloud,key.pub ssh-rsa

AAAAB3NzaClyc2E72LAAABIwAAAQEAxopk8A7 9Tp01ds2ySL63kiw/6t45F7ZRG10LLBjZXNQtleke4Y XnF/D/jvzMoYRG7Gj4gtvFxwFtqtYel9o00dQoN0tKrfTD4ajqUqFm+lqWNVkB7h0rtz0eiqHrv8Pd H5bRd4ifPtJn3nfPDd7hTbHGqoJnuppITnTQYKA20XRDwGQM/Ra3/+fJj6EkwgVwLQgOvbHLoXafEk TNlGHARlLZUwqy/i8eC53Tmgh+1310pnjXB5WAr4XLuCyhfnZ6ICXOp501rDTqU/FclGXEnqhc5wma 9Cjgi30hiXADNFJQlSBtWiS4J10nZBKkWzqXfOJSqX84pz6Znpc+tjphpQ==

root@localhost.localdomain

将其内容粘贴到图12.34中的“公钥”文本框中。单击“导入密钥对”按钮，完成密钥对 的创建。

切换到“网络”选项卡，可以看到所有的网络列表，如图12.35所示。

图12.35可用网络列表



单击要使用的网络右下角的O按钮，选中该网络，完成之后如图12.36所示。单击“运行” 按钮，完成虚拟机的创建。

图12.36选择网络

此时，冈棚创建的实例webserver已经出现在实例列表中，并且已经为其分配了一个地址 192.168.21.3。单击实例名称，打开云主机详情窗口。切换到“控制台”选项卡，可以看到该 虚拟机已经启动，如图12.37所示。

云主机控制台

識械栈破城盘奶栅:.她缺L.盘醜妓郎鎌&. 滅ig.%*薄am.

图12.37实例控制台

尽管实例已经成功创建，但是此时仍然不能通过SSH访问虚拟机，也无法ping通该虚拟

机。这主要是因为安全组规则所限，所以需要修改其中的规则。

选择Compute — “访问&安全”菜单，窗口右侧列出了所有的安全组，如图12.38所示。

图12.38安全组列表

由于前面在创建实例时使用了 default安全组，所以单击对应行中的“管理规则”按钮， 打开“安全组规则”窗口，如图12.39所示。

OS' f：i->    r    乂、 □    -    ®    :______

4-    C ? 58.64.138.219 i ' 1    ' ； .■    ■彐

| Q openstack | i <tono    ' ji. | oemoX"    看出 |          |                   |                      |      |
| ----------- | ---------------- | -------------- | -------- | ----------------- | -------------------- | ---- |
| 棚          | .管理安金组规则: | default        |          |                   |                      |      |
| Compute     |                  |                |          |                   |                      |      |
| 安全组规则  |                  |                | 4!糾納 I |                   |                      |      |
|             |                  | 方ft           | 曲入炱9  | IP格设            | 嫡口范困 《8    动作 |      |
|             |                  | XO             | IPv4     | ICMP              | 00 00/0 (QDRJ \|     |      |
|             |                  | 出口           | IPv6     | 任河              | /0 iCIOR)    \|      |      |
| J访间&尹g   |                  | 入口           | IPv6     | 任间              | defaoN               |      |
|             | 入□              | IPv4           | 任间     | default    t      |                      |      |
|             |                  |                |          |                   |                      |      |
| ^27 □       | 出口             | IPv4           | «钶      | 00 0.0/0 (CJCW) 1 |                      |      |
| 砌存餹      | »B示计条目       |                |          |                   |                      |      |

图12.39 default安全组规则

单击“添加规则”按钮，打开“添加规则”对话框，如图12.40所示。在“规则”下拉菜 单中选择“ALLICMP”选项，单击“添加”按钮将该项规则添加到列表里面。再通过相同的 步骤，将SSH规则添加进去。前者使得用户可以ping通虚拟机，后者可以使得用户通过SSH 客户端连接虚拟机。

图12.40添加规则



为了能够使得外部网络中的主机可以访问虚拟机，还需要为虚拟机绑定浮动IP。在实例 列表中，单击webserver虚拟机所在行的最右边的“更多”按钮，选择“绑定浮动IP”命令， 打开“管理浮动IP的关联”对话框，在“IP地址”下拉菜单中选择一个外部网络的IP地址, 如图12.41所示。单击“关联”按钮，完成IP的绑定。

图12.41绑定浮动IP



![img](11 CentOS7fbdfa1060ed0f49e18-298.jpg)



如果IP地址下拉菜单中没有选项，则可以单击右侧的+按钮，添加浮动IP。

对于已经绑定浮动IP的虚拟机来说，其IP地址会有两个，分别为租户网络的IP地址和 外部网络地址，在本例中，虚拟机webserver的IP地址分别为192.168.21.3和172.24.4.229。 然后在终端窗口中输入ping命令，以验证是否可以访问虚拟机，如下所示：

(rootQlocalhost    # ping 172.24.4.229

of data. time=5.32 ms time=0.499 ms time^O.637 ms



PING 172.24.4.229 (172.24.4.229) 56(84) bytes 64 bytes from 172.24.4.229: icmp一seq=l ttl=63 64 bytes from 172.24.4.229: icmp_seq«2 64 bytes from 172.24.4.229: icmp一seq=3 ttl=63

从上面的命令可以得知，外部网络中的主机已经可以访问虚拟机。接下来使用SSH命令 配合密钥来访问虚拟机，如下所示：

[root^localhost # ssh -i cloud.key [cirros@172.24.4.229](mailto:cirros@172.24.4.229)

可以发现，上面的命令己经成功登录虚拟机，并且出现了虚拟机的命令提示符$符号。下 面验证虚拟机能否访问互联网，输入以下命令：

$ ping [www.google.com](http://www.google.com)

可以发现，虚拟机已经可以访问互联网上的资源。

如果用户想要重新启动某台虚拟机，则可以单击对应行的右侧的“更多”按钮，选择“软 重启云主机”或者“硬重启云主机”命令，来实现虚拟机的重新启动。

此外，用户还可以删除虚拟机、创建快照以及关闭虚拟机。这些操作都比较简单，不再详 细说明。

##### 12®6小结

本章详细介绍了在CentOS 7上面安装部署OpenStack的方法。主要内容包括OpenStack 的基础知识，OpenStack的体系架构，OpenStack的部署工具，使用RDO部署OpenStack以及 管理OpenStack等。重点在于掌握好OpenStack的体系架构，使用RDO部署OpenStack的方 法以及镜像、虚拟网络和实例的管理。

###### 第13蠢





# REF
- [OpenStack构架知识梳理](http://www.cnblogs.com/kevingrace/p/5733508.html)
