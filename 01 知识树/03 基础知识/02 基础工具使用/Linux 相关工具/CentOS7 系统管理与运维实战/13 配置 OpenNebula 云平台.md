###### ◄配置OpenNebula云罕台►

OpenNebula是一个非常成熟的云平台，十分简单但功能却又十分丰富。它提供了十分灵 活的解决方案，让用户能建立并管理企业云和虚拟的数据中心。OpenNebula的设计目标是简 单、轻便、灵活且功能强大，也正因为如此其赢得了不少用户。本章将简要介绍OpenNebula 云平台及其使用方法。

本章主要涉及的知识点有：

•    云简介

•    OpenNebula 概述

•    OpenNebula安装与管理

13.1 OpenNebula 概述

OpenNebula是云计算软件中的代表之一，其轻便、简单、灵活的特点为其贏得了不少客 户，但在国内仍少有人使用。本节将简要介绍云计算与OpenNebula等知识。

13.1.1云计算概述

云计算是近年来兴起的新技术之一，关于云计算还没有一个准确的定义，有许多种关于云 计算的解释。但广为人们接受的是美国国家标准与技术研究院（National Institute of Standards and Technology, NIST）的定义：云计算是一种按使用量付费的模式，这种模式提供便捷、可 用和按需求的网络访问，进入可配置的计算资源共享池（资源包括网络、服务器、存储应用软 件及服务），这些资源能够被快速提供，只需投入很少的管理工作，或与服务供应商进行很少 的交互。.

美国国家标准与技术研究院关于云计算定义的翻译来源于网络，译者不详。

云计算有许多种应用实例和模型本书并不涉及,本书中介绍的云计算模型均是以虚拟化为 核心、以计算机网络技术为基础的计算模式。此类模式为企业提供了更加经济、便捷的管理模 式，广泛应用于各种大中小型企业中。

云计算是将原来较为分散的计算、存储、服务器等资源，通过计算机网络和云计算软件有 效地整合起来，从而形成一个便于管理、分配的资源库。当新客户到来或有新的需求时，管理 员仅需要从资源库中选择合乎要求的各类资源，并进行重新组装即可供新客户使用。同时在原 有基础上还实现了资源细化及按需配置。

简单来说就是将原有的服务器计算资源、网络（通过Vian的形式）、存储等资源通过虚拟 化的方式，重新组装成新的虚拟计算机，从而实现对资源的精确分配。由此可以说云计算是传 统的分布式计算、网络存储、并行计算、虚拟化、负载均衡、效用计算等技术与网络技术互相 融合的产物。

13.1.2 OpenNebula 概述

OpenNebula是专门为云计算打造的开源系统，用户可以使用Xen、KVM甚至是VMware 等虚拟化软件一起打造企业云。利用OpenNebula可以轻松地构建私有云、混合云及公开云， OpenNebula提供的接口如图13.1所示。

图13.1 OpenNebula提供的接口

OpenNebula提供的接口比较丰富，如管理员提供了包括类似于Unix命令行的工具集CLI 及功能强大的GUI界面；可扩展的底层接口提供了 XML-RPC、Ruby、Java等AH供用户整 合使用等。

OpenNebula还提供了许多资源管理和预配置目录，使用这些目录中的资源，可以快速、 安全地构建富有弹性的云平台，资源目录如图13.2所示。

NETWORK CATALOG

•    Private Dev Net

•    Public Net

•    HPC InfiniBand

•    Private production



TEMPLATE CATALOG

•    Web Server Front-end

•    Database component

•    Web server workar

•    Load balancer

图13.2 OpenNebula提供的资源目录

映像目录主要包含的是磁盘映像；网络目录可以使用有组织的网络，也可以使用虚拟网络 或混合网络，既能使用IPv4也能使用IPv6； VM模板目录同VMware虚拟化中的模板相似， 可以被实例化为虚拟机；除此之外还有虚拟资源控制及监测（主要用于虚拟机迁移、停止、恢 复等）等。

OpenNebula的工作机制相对比较简单，其使用共享的存储设备来为虚拟机提供各种存储 服务，以便于所有虚拟机都能访问到相同的资源。同时OpenNebula还使用SSH作为传输方式, 将虚拟化管理命令传输至各节点，这样做的好处是无须安装额外的服务或软件，降低f软件的 复杂性。

![img](11 CentOS7fbdfa1060ed0f49e18-302.jpg)



本小节中仅介绍了与配置OpenNebula相关的内容，其他相关内容可通过查看其官方网站 上的说明了解，此处不再赘述。

13.2 OpenNebula 安装

OpenNebula目前的最新版本为4.12,本节将以4.12版在CentOS 7上安装为示例，简要介 绍其安装过程。

13.2.1控制端环境配置

环境配置包括IP地址、DNS地址、主机名及hosts文件等网络设置，关于此方面设置可 参考本书的第3章中的相关章节，此处不再赘述。

(1) SELinux 配置

SELinux为一项重要的配置，OpenNebula官方建议关闭SELinux,以免出现不必要的错误。 关闭SELinux需要修改文件/etc/sysconfig/selinux,如【示例13-1】所示。

【示例13-1】

[root@mal cat /etc/sysconfig/selinux

\#    This file controls the state of SELinux on the system.

\#    SELINUX= can take one of these three values:

\#    enforcing - SELinux security policy is enforced.

\#    permissive - SELinux prints warnings instead of enforcing.

\#    disabled - No SELinux policy is loaded.

\#修改以下值为disabled

SELINUX=disabled

\#    SELINUXTYPE= can take one of these two values:

\#    targeted - Targeted processes are protected,

\#    minimum - Modification of targeted policy. Only selected processes are protected.

\#    mis - Multi Level Security protection.

SELINUXTYPE=targeted

(2)防火墙配置

为了能使OpenNebula正常工作，还必须配置系统防火墙开放相关端口。在本例中将采取 关闭防火墙的方法，如【示例13-2】所示。

【示例13-2】

■■■

trootemal •>•] # '■ systertctl. disable 'ExrewalXd [root@mal # systemctl stop firewalld

(3)软件源配置

OpenNebula官方提供了软件源方便安装，直接在系统上添加软件源，然后使用yum工具 安装即可。新建一个名为opennebula.repo的文件，如【示例13-3】所示。

【示例13-3】

[root@mal -*] # cat /etc/yum. repos.d/opennebula. repo

[opennebula]

name=opennebula

baseurl=<http://downloads.opennebula.org/repo/4.12/CentOS/7/x86_64>

enabled=l

gpgcheck=O

到此环境配置就已经完成了，接下来就可以重新启动CentOS 7让所有配置生效。

13.2.2控制端安装

环境配置完成后就可以开始软件安装过程了，在开始安装之前还需要安装EPEL源，EPEL 源将提供一些额外的软件包。安装过程如【示例13-4】所示。

【示例13-4]

.• .... -

[root@mal # yum install -y epel-release Loaded plugins: fastestmirror, langpacks base

I 3.6 kB I 3.4 kB 1 2.9 kB I 3.4 kB 1    19



00:00 00:00 00:00 00:00 kB 00:00



extras

opennebula

updates

opennebula/primary_db

Loading mirror speeds from cached hostfile * base: mirrors.btte.net

★    extras: mirrors.yun-idc.com

\*    updates: mirrors.yun-idc.com Resolving Dependencies

--> Running transaction check

---> Package epel-release.noarch 0:7-5 will be installed

--> Finished Dependency Resolution

确认以上环境和软件都已经安装完成后，还需要安装依赖软件包，如【示例13-5】所示。

【示例13-5】

[root@mal 〜yum install -y gcc~c++ gcc sqlite-devel curl-devel mysql-devel ruby-devel make

Loaded plugins: fastestmirror, langpacks Loading mirror speeds from cached hostfile

\*    base: mirrors.skyshe.cn

*    epel: [ftp.kddilabs.jp](ftp://ftp.kddilabs.jp)

\*    extras: mirrors,sina,cn

\*    updates: mirrors,sina.cn

Package 1:make-3.82-21.el7.x86__64 already installed and latest version

Resolving Dependencies

--> Running transaction check

---> Package gcc.x86_64 0:4.8.3-9.e!7 will be installed

安装完成后，就可以开始装OpenNebula,如【示例13-6】所示。

【示例13-6】

[root@mal # yum clean all

Loaded plugins: fastestmirror, langpacks

Cleaning repos: base epel extras opennebula updates Cleaning up everything    -

Cleaning up list of fastest mirrors

[root@mal # yum install ~y opennebula-server opennebula-sunstone opennebula-ruby

Loaded plugins: fastestmirror, langpacks Determining fastest mirrors

\*    base: mirrors.btte.net

\*    epel: [ftp.kddilabs.jp](ftp://ftp.kddilabs.jp)

\*    extras: mirrors.yun-idc.com

\*    updates: mirrors.yun~idc.com Resolving Dependencies

--> Running transaction check

---> Package opennebula-ruby.x86__64 0:4.12.1-1 will be installed

--> Processing Dependency: rubygems for package:

opennebula-ruby-4.12..x86一64

--> Processing Dependency: rubygem-nokogiri for package:

opennebula-ruby-4.12.1-1,x86_64

--> Processing Dependency: rubygem-json for package:

opennebula-ruby-4.12.1-1.x86_64

安装完成后还需要安装Ruby库才能使用，OpenNebula提供了一个集成化的脚本，运行此 脚本即可安装，如【示例13-7】所示。

【示例13-7】

[root@mal # /usr/share/one/install__gems

Isb一release command not found. If you are using a RedHat based distribution install redhat-lsb

Select your distribution or press enter to continue without installing dependencies.

\0. Ubuntu/Debian 1. CentOS/RedHat/Scientific #此处需要选择操作系统类型 1

Distribution ’’redhat" detected.

About to install these dependencies:

\*    gcc-c++

\*    gcc

\*    sqlite-devel

\*    curl-devel

\*    mysqi-devel

\*    ruby-devel

\* make

![img](11 CentOS7fbdfa1060ed0f49e18-303.jpg)



\#需要安装依赖软件按Enter键即可 Press enter to continue...

\#后面还会提示安装相关软件，按Enter键即可

由于许多源都位于国外，执行上述命令安装时有可能会因连接超时而导致整个安装失败， 此时可以添加国内的'淘宝源，然后再执行上述命令。添加淘宝源命令如【示例13-8】所示。

【示例13-8】

[root@mal gem sources -a <http://ruby.taobao.org/> <http://ruby.taobao.org/> added to sources

胃中，可能会有许多警告信息，无须担心，忽略即可。

匕:,包错误导致失败，可继续运行上述命令重新安荦直到安装结束。

13.2.3客户端安装

OpenNebula可以使用多种虚拟化技术客户端，如KVM、Xen甚至是VMware,在本例中 将采用在CentOS 7中安装KVM作为客户端。CentOS 7中安装KVM的方法可参考本书的第 10章，此处不再赘述。

安装完KVM之后就可以开始安装OpenNebula的客户端程序了，客户端程序依然采用yum 工具安装，因此需要按13.2.1小节中的方法先配置yum源。安装方法如【示例13-9】所示。

【示例13-9】

鑿#设置好源之后最好先清除缓存再安装    ■: •'乂

[root@nodel # yum clean all

Loaded plugins: fastestmirror, langpacks

Cleaning repos: base extras opennebula updates

Cleaning up everything

Cleaning up list of fastest mirrors

[rootQnodel -]# yum install ~y opennebula-node-kvm

Loaded pluginsfastestmirror, langpacks

| base                                                         | 1 3.6 kB | 00:00 |       |
| ------------------------------------------------------------ | -------- | ----- | ----- |
| extras                                                       | 1 3.4 kB | 00:00 |       |
| opennebula                                                   | 1 2.9 kB | 00:00 |       |
| updates                                                      | 1 3.4 kB | 00:00 |       |
| (1/5): extras/7/x8^_64/primary_db                            | ! 62     | kB    | 00:00 |
| (2/5): toase/7/x86_64/group_gz                               | 1 154    | kB    | 00:00 |
| (3/5): updates/7/x86_64/priraary_db                          | 1 2.5    | MB    | 00:01 |
| (4/5): base/7/x86_64/primary_db                              | 1 5.1    | MB    | 00:01 |
| {5/5): opennebula/primary_dbDetermining fastest mirrors*    base: mirrors.sina.cn*    extras: mirrors.sina.cn*    updates: mirror.bit.edu.cn | I 19 :   | k.B   | 00:03 |

Resolving Dependencies

--> Running transaction check

![img](11 CentOS7fbdfa1060ed0f49e18-304.jpg)



如果使用Xen虚拟化，除以上安装的软件包外，客户端还需要安装一个名为

openebula-common 的软件包。

13.2.4配置控制端和客户端

所有软件安装完成后还不能立即使用，还需要做一些配置，包括密码、SSH验证等方面。 本小节将简要介绍如何配置控制端和客户端。

1.控制端主守护进程配置

控制端有两个守护进程需要配置，其一是oned，这是OpenNebula的主要进程，所有主要 功能都通过此进程完成；另一个称为sunstone,这是一个图形化的用户接口。启动OpenNebula 需要启动这两个进程，首先需要配置的是主守护进程。

安装完控制端后，OpenNebula会向系统添加一个名为oneadmin的用户，OpenNebula将 以此用户的身份管理整个软件。首先需要添加系统认证的密码，如【示例13-10】所示。

【示例13-10】

\#切换到用户oneadmin

[rootSmal ~J# su - oneadmin

\#添加初始化密码并修改认证文件的权限

[oneadmin@mal •*] $ mkdir ~/.one

\#以下设置必须在第一次启动之前做

\#此处演示密码为password

[oneadmin@mal $ echo "oneadmin:password" ＞〜/-Qne/one 一auth

[oneadmin@mal chmod 600 〜/ .one/one一auth

\#以下仅为测试，可选步骤

\#建议在【示例13-11】之后做

\#启动OpenNebula守护进程

\#使用查看虚拟机列表的方式验证是否成功启动

[oneadmin@mal 〜]$ one start

[oneadmin@mal 〜]$ onevm list

ID USER GROUP NAME    STAT UCPU UMEM HOST    TIME

在上面的示例中需要使用密码替换password字符串，此处设置的密码为第一次启动的密码。

2.图形化用户接口配置

图形化用户接口进程为sunstone,默认情况下该进程只在本地环回接口（接口名为lo, IP 地址为127.0.0.1）侦听，其他计算机均无法访问。为了能使其他计算机都能访问，需要修改侦 听地址，如【示例13-11 ]所示。

【示例13-11】

\#修改sunstone服务的配置文件

[root@mal # cat /etc/one/sunstone-server.conf #

--------------------------------------------------------------------------#

\#    Copyright 2002-2015, OpenNebula Project (OpenNebula.org), C12G Labs    #

\#    #

\#    Licensed under the Apache License, Version 2.0 (the ’’License’’)； you may #

\#    not use this file except in compliance with the License. You may obtain #

\#    a copy of the License at #部分内容省略

\#    Server Configuration

\#

\#修改侦听地址127.0.0.1为0.0.0.0 #该和位于第31行 :host: 0.0.0.0 :port: 9869

\#    Place where to store sessions, this value can be memory or memcache

\#    Use memcache when starting multiple server processes, for example,

\#    with passenger

\#完成上述设置后需要开启开关服务

[root@mal # systemctl enable opennebula

In 一s '/usr/lib/systemd/system/opennebula.service *

•/etc/systemd/system/multi-user.target.wants/opennebula.service1 [root@mal # systemctl start opennebula [root@mal ~]# systemctl enable opennebula-sunstone In -s */usr/lib/systemd/system/opennebula-sunstone.service *

1/etc/systemd/system

[root@mal 〜]# systemctl start opennebula-sunstone

完成上述步骤后就可以通过网页打开Sunstone 了，如图13.3所示。

| A OpenNebul* Sunstone x     |      |
| --------------------------- | ---- |
| «    C    172.16.45.22 'S'' |      |
| OpenNebulaSunstone          |      |
| Usernamef    ]              |      |
| I    JPassword1    1        |      |
| 。Login                     |      |
|                             |      |

图 13.3 Sunstone 界面

访问Sunstone时需要注意，不建议使用IE内核的浏览器，建议使用Mozilla Hrefox或 Google Chrome等非IE内核浏览器；另一个问题是控制端与访问计算机的时间相差不能太大, 否则会导致失败。

3.配置NFS

如果使用多节点的OpenNebula，需要在控制端上配置NFS （控制端与客户端位于同一服 务器时无须此配置），如【示例13-12】所示。

【示例13-12】

\#设置NFS将目录八ar/lib/one共享 [root@mal # cat /etc/exports

/var/lib/one/ *(rwz syncz no_sufatree_check,roOt_squash) [root@mal # systemctl jstart nfs

当控制端配置了 NFS之后，客户端还需要配置NFS挂载（NFS共享的目录相当于存储， 关于此问题可参考官方网站关于存储的说明）。挂载应该写入文件/etc/fstab,写入内容如下所 示：

\#将下面这行内容添加到/etc/f stab文件最后

172.16.45.22:Zvar/lib/one/ /var/lib/one/ nfs soft,intr,rsi2e=8192rwsize=8192,noauto

\#验证配置

[rootSnodel 〜J# mount -a

{root0nodel # df -h | grep Zvar/lib/one

172,16,45*22:/var/lib/one 458G    18G    440G    4% /var/lib/one

4.配置SSH公钥

OpenNebula使用SSH远程登录到Node上，然后执行各种管理命令，因此必须要配置SSH 服务，让管理端的oneadmin用户能够自动登录，而不需要密码。控制端配置如【示例13-13】 所示。

【示例13-13】

\#以oneadmin登录并设置ssh登录方式 [.ropt@mal ,    # su oneadmin

[oneadmin@mal 〜J$ cat -/.ssh/config Hosj: *

St rictHostKeyChec king no UserKnownHostsFile /dev/null {oneadmin@ma1    $ chmod 600 -/.ssh/config

\#生成公钥和私钥

\#以下命令需要按3次Enter键 [oneadmin@raal ~ 3 $ ssh-keygen

Generating public/private rsa key pair.

Enter file in which to save the key (/var/lib/one/.ssh/id_rsa Enter passphrase (empty for no passphrase):

Enter same passphrase again:



Your identification has been saved in /var/lib/one/.ssh/id rsa.

Your public key has been saved in /var/lib/one/.ssh/id__rsa.pub.

The key fingerprint is:

ef:e6:lb:70:f2:cc:35:8b:45:65:6f:25:90:d8:lb:e6 [oneadmin@mal.example.com](mailto:oneadmin@mal.example.com) The key's randomart image is:

![img](11 CentOS7fbdfa1060ed0f49e18-305.jpg)



\#修改生成文件的权限

[oneadmin@mal $ chmod 600 .ssh/*

并将公钥传递给nodel上 #执行以下命令吋需要输入nodel的root密码

[oneadminQmal $ scp .ssh/id一rsa.pub rootQnodel:/var/lib/one

Warning: Permanently added *nodel,172.16.45.23* (ECDSA) to the list of known

hosts.

root@nodel1s password:

id一 rsa.pub    100% 406    0.4KB/S    00:00

送文件成功后需要在nodel上继续操作

\#注意以下步骤在nodel上执行 #先修改传送过来的文件仅限

[root@nodel # chown oneadmin.oneadrain /var/lib/one/id_rsa.pub

\#切换到oneadmin用户执行后继操作

[root@nodel 〜H su 一 oneadmin

Last login: Fri Jul 3 09:56:46 CST 2015 on pts/0 #创建目录并导入公钥

[oneadmin@nodel [oneadmin@nodel [oneadmin@nodel

\#修改权限

[oneadmin@nodel [oneadmin®nodel



$ mkdir .ssh

mv id一rsa.pub 〜/.ssh/

~]$ cat 〜/.ssh/id一rsa.pub >> -7 .ssh/authorized一keys

$ chmod 600 .ssh/authorized_keys ~3 $ chmod 700 .ssh/

\#测试效果

\#此操作在控制端进行

![img](11 CentOS7fbdfa1060ed0f49e18-306.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-307.jpg)



\#第一次登录时需要输入yes [root@mal # su ~ oneadmin

Last login: Fri Jul 3 09:49:13 CST 2015 on pts/0 {oneadmin@raal ~3 $ ssh nodel

Warning: Permanently added 'nodel,172.16.45.23* (ECDSA) to the list of known hosts.

Last login: Fri Jul 3 10:06:21 2015 from mal

![img](11 CentOS7fbdfa1060ed0f49e18-308.jpg)



无论哪种方案都需要配置SSH,即使控制端与客户端在同一服务器上，同时建议将控制 端也做成一个客户端，以便配置和安装镜像。

........................... ............. ...............................................................................................v J

5.客户端KVM配置

在客户端上安装KVM并设置桥接等内容可参考第10章的相关内容，此处不再赞述。此 处还需要对KVM做一些配置，如【示例13-14】所示。

【示例13-14】

\#设置用户和组

{root@mal # cat /etc/libvirt/qemu.conf

user = "oneadmin”

group - "oneadmin”

dynamic^ownership = 0

security_driver = ttnone"

security—default二confined = 0

\#配置libvirtd服务侦听

IrootSmal ~,J# .'Cat /etc/libvirtZlibvirtd.conf

\#部分设置省略 ••••••

\#配置项为第22行和第33行 #分别取消这两行的注释，如下所示

\#    It is necessary to setup a CA and issue server certificates before

\#    using this capability.

\#

\#    This is enabled by default, uncomment this to disable it listen tls = 0

\#    Listen for unencrypted TCP connections on the public TCP/XP port.

\#    NB, must pass the --listen flag to the libvirtd process for this to

\#    have any effect.

\#    Using the TCP socket requires SASL authentication by default. Only

\#    SASL mechanisms which support data encryption are allowed. This is

\#    DIGEST MD5 and GSSAPX (KerberosS)

\# This is disabled by default, uncomment this to enable it. listen一tcp = 1

\#开启服务监听选项

[roois@mal cat /etc/sysconfig/libvirtd

\#    Override the default config file

\#    NOTE: This setting is no longer honoured if using

\#    systemd. Set *-一config /etc/libvirt/libvirtd.conf *

\#    in LIBVIRTD_ARGS instead. #LIBVIRTD_CONFIG=s/etc/libvirt/libvirtd. conf

\#    Listen for TCP/IP connections

\#    NB. must setup TLS/SSL keys prior to using this #取消下面的注释

LIBVIRTD一 ARGS:"--listen"



\#重启服务并检查设置是否生效

[root@mal systemctl restart libvirtd [root@mal # netstat -tunlp I grep libvirtd



![img](11 CentOS7fbdfa1060ed0f49e18-309.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-310.jpg)



tcp    0    0 0.0.0.0:16509

24179/libvirtd

tcp6    0    0 :::16509

24179/libvirtd



0.0.0,0:*



![img](11 CentOS7fbdfa1060ed0f49e18-311.jpg)



LISTEN

LISTEN



至此服务端和客户端都已经配置完成了。

373

13.3 OpenNebula配置与应用

学习了 OpenNebula的安装之后，接下就可以配置OpenNebula 了，内容包括:配置Sunstone、 VDC和集群，设置映像、模板管理、虚拟机管理等。与第10章中介绍的oVirt相比，OpenNebula 还有大量的工作需要做，这些工作主要来自映像、模板和虚拟机管理。本节将简要介绍如何将 安装好的OpenNebula组装为一个可用的集群，并添加一些映像、模板，最后实例化为虚拟机。

13.3.1配置VDC和集群

首次登录Sunstone之后，可以发现其默认语言为英语，可以修改为简体中文。修改的方 法为先单击右上角的当前登录的用户名，然后在菜单中选择“Settings”。在弹出的配置界面右 上角单击“Conf”，如图13.4所示。

Configuration

| O    0Info    Conf             | QuoUs | Accounung    Sfwwbdck |
| ------------------------------ | ----- | --------------------- |
| Language.                      |       |                       |
| Simplified Chinese (zh_CN)     |       | *                     |
| \ACWS：                        |       |                       |
| admin                          |       | 督                    |
| Default Table order:descending |       | •                     |

Update config

图13.4 Sunstone语言配置

在 “Language” 下拉列表框中选择 “Simplified Chinese(zh CN)",然后单击 “Update config” 按钮即可将默认语言修改为简体中文。

VDC (Virtual Data Centers,虚拟数据中心)与oVirt中的数据中心概念相似，表示一组或 多组功能集群的集合。但在OpenNebula中数据中心和集群的概念相对较弱，几乎没有过多的 约束设置，只有在做故障迁移等设置时，这些设置才起作用。如果没有故障迁移等方面的需求， 也可跳过虚拟数据中心和集群设置。

添加VDC可以在Sunstone界面左侧的索统设置中选择“VDCs”，此时右侧将显示已存在 的虚拟数据中心。单击虚拟数据中心列表上方的加号，将弹出添加VDC界面，如图13.5所示。

|            |                            | <\| OpenNebula |                                        |              |
| ---------- | -------------------------- | -------------- | -------------------------------------- | ------------ |
| OpenNebula | Create Virtual Data Center |                |                                        |              |
| 曲&控制台  |                            | ts             |                                        | 向导    《^6 |
| as系统设s  | as                         | it             | a                                      |              |
|            |                            |                |                                        |              |
| 解娜珲     |                            |                |                                        |              |
| I VOCS     | 名称：&                    |                |                                        |              |
| 仍1*^•苗   |                            |                |                                        |              |
| .戲臟      | 掮述：                     |                |                                        |              |
| £础设施    |                            |                |                                        |              |
| ■R fWT5«   |                            |                |                                        |              |
| & Often®*  | 自定乂霣忮                 |                |                                        |              |
| ◎ Support  |                            |                |                                        |              |
| comcttcd   | 名件                       |                | 值                                     |              |
|            |                            |                | OoJrtNebiM <    «*f OpcIMb!** ' fS^rni |              |

图13.5添力口 VDC

在创建VDC界面的“常规”选项中输入数据中心的名称、描述信息，然后在“资源”中 为数据中心添加己存在的集群、主机、网络和数据仓库，最后单击上面的“创建”按钮即可= 需要注意的是，数据仓库已经在安装时自动创建，此处可以直接选择所有数据仓库将其一并添 加到数据中心中。

添加完VDC后，接下来需要创建集群。单击左侧基础设施中的集群管理，界面右侧将显示 当前系统中的集群列表。单击集群列表上方的加号将弹出“创建集群”界面，如图13.6所示。

创建集群

[malnClustcrJ



O Gr

主机管®    由拟网络    教堳仓厍

已分配CPU    已分配内存

172.16.45.22



100/40G    S12MB/3.7C6    ■

(3TO)    fW)    測

1育从列*中迭择一个或者多个主机

图13.6创建集群

在“名称”中输入集群名称，然后在“主机管理”中选中主机、“虚拟网络”中选择添加 的网络，然后选择“数据仓库”，最后单击“创建”按钮即可。

添加完集群和数据中心后,可以在数据中心界面中的数据中心列表中单击创建的数据中心 查看数据中心详情。在数据中心详情界面右上角单击更新，然后在资源选项的集群管理中为数 据中心添加集群。也可以更新数据仓库等设置，集群也可使用同样的方法更新设置。

![img](11 CentOS7fbdfa1060ed0f49e18-312.jpg)



OpenNebula还预设了各种角色和用户，同时还提供了计费等功能，本书中并不涉及，读j

者可自行参考相关资料了解。

is-------------------------------------------------------------------------

13.3.2添加KVM主机

主机是云计算中的计算节点，通俗地讲主机主要是将存储资源、网络资源集中起来，并使 用自身的计算资源以虚拟机的方式汇集各种资源为客户提供服务。OpenNebula中可以添加的 主机有Xen、KVM、VMware及vCenter,由于红帽公司主导使用KVM虚拟化，因此本书中 主要介绍KVM主机的使用方法，其他主机并不涉及，如需使用可以参考OpenNebula的官方 文档了解。

添加KVM主机有两种方法，其一是使用Sunstone提供的图形化接口；其二是使用CLI 命令方式添加。在添加主机之前，需要确保主机的SELinux、防火墙、SSH、KVM、NFS等 均已正确设置，具体设置细节可参考13.2节中的相关内容了解，此处不再赘述。

1 .在Sunstone中添加主机

在Sunstone界面的左侧基础设施中选择主机管理，此时右侧将显示主机列表。单击主机 列表上方的加号，将弹出创建主机界面，如图13.7所示。

| 创建主机     |      |                 |      |
| ------------ | ---- | --------------- | ---- |
|              |      | 集群            |      |
| KVM          | ▼    | 100: ma<nduscer | •    |
| 主机名       |      |                 |      |
| 172.16.45.23 |      | 缺省值(dummy)   | •    |

图13.7创建主机界面

在创建主机界面中选择正确的类型，选择主机所属的集群和网络(此处网络设置选择默 认)，并在主机名中填入主机的IP地址或主机名，最后单击创建即可。需要注意的是，如果使 用主机名，一定要确保能正确解析，否则添加主机可能会失败。

主机添加完成后，就可以在主机列表中看到该主机，如图13.8所示。

|            | 主机管理            |             |                         |      |                                 |      |
| ---------- | ------------------- | ----------- | ----------------------- | ---- | ------------------------------- | ---- |
|            |                     |             |                         |      | AftftR    «用    源用           |      |
| 齒 IO«名称 | 集群                | 运行VM数量  | 已分紀CPU    已分配内存 | 状态 |                                 |      |
| © 1        | 172.16.45.23        | malnClusfor | 0                       |      | 0/必    0*6?-                   |      |
| 镜 0       | 172J6.45.22         |             | \                       |      | 100 /400125%)    512MB/3 7CB 门 | 幵机 |
| showing    | •. to 2 0( ? dwries |             |                         |      | PtCSrtObs    1    Nr-O          | 10 - |
|            |                     | 1           | 1                       | 0:   | 0 f2;fr                         |      |

图13.8主机列表

可以看到新添加的主机状态为“初始化”，当主机初始化完成后，状态将变为“开机”，此 时主机可用，否则主机将不可用，此时就需要查看日志排错。

在主机列表中单击任意一台主机，将显示主机信息，如图13.9所示。

| O    I舶            | a                                       |           |      |      |                |
| ------------------- | --------------------------------------- | --------- | ---- | ---- | -------------- |
| aw.                 | V'M棚                                   |           |      |      |                |
|                     |                                         | 容璽      |      |      |                |
|                     | 0                                       |           |      |      | Sl2M8/i7C6sUHI |
|                     | 172,16.45.22                            |           |      |      |                |
|                     |                                         |           |      |      |                |
|                     | MONITORED                               | 娜哺      |      |      | (MB }7G&rj46i  |
| •岛!JO 间 ct        | kvm                                     | 卿        |      |      | 0 »-W010H)     |
| 伽lnJ揀中间件触堪件 | kvmdurnmy                               | sGVMfJ 簷 |      |      | 1              |
| 厲性                |                                         |           |      |      |                |
| AMCM                | »se_&4                                  |           | rjt  | «9r  |                |
| CPUSPFFD            | 2524                                    |           | C?   | Sr   |                |
|                     | ma' cxamplc.com                         |           | GT   | s    |                |
| HVPtKVJSOR          | kvm                                     |           | or   | s    |                |
| MODELS AME          | (nteJ(R) CwtXTM) *3-2130 CPU 砂 3.40&H» |           | 5    |      |                |
| NF7RX               | 0                                       |           | <3   | a    |                |
| Nenx                | 0                                       |           | {?   | e>   |                |
| RtSt*VH5_CRJ        |                                         |           | uT   | 苗   |                |
|                     |                                         |           |      | 5    |                |
| VFRSION             | A. 12.1                                 |           |      |      |                |
|                     |                                         |           |      |      |                |

图13.9主机信息

![img](11 CentOS7fbdfa1060ed0f49e18-313.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-314.jpg)



在主机信息界面的信息选项中可以查看到当前主机的主要信息，如已分配CPU、内存、 CPU型号等。在图表信息中将显示过去一段时间内CPU和内存的使用情况，vm数量将显示 当前主机运行的主机列表。

2.使用CLI拭添加

使用CLI方式添加主机与图形界面所需参数相同，添加过程如【示例13-15］所示。

【示例13-15】

\#此命令需要在控制端执行 #需要以用户oneadmin身份执行 [rootQmal # su - oneadndn #参数参考图形界面中的参数设置

[oneadmin@mal ~]$ onehost create 172.16.45.22 ——im kvm --vm kvm --net dummy ID: 0

\#査看添加主机情况

\#刚添加时主机状态处于init初始化状态 #初始化完成后状态将变为on [oneadmin^mal $ onehost list ID NAME    CLUSTER RVM    ALLOCATED一CPU

ALLOCATED一 MEM STAT -init



0 172.16.45.22    0    -

13.3.3建立映像

OpenNebula安装完成后建立虚拟时，需要使用操作系统模板，模板可以快速转换为虚拟 机，而不再需要安装操作系统。建立系统模板需要使用磁盘映像，磁盘映像就是虚拟磁盘文件。

OpenNebula提供了两种方法建立映像，其一是使用官方提供映像和模板；其二是用户自己建 立磁盘文件安装系统制作映像。

1.使用官方映像和模板

使用官方提供的映像和模板可以在Sunstone界面的左侧选择应用市场，此时右侧将显示 官方提供的映像列表，如图13.10所示。

|                                 | OpenNebula 市杨                         |                     |            |          |      |
| ------------------------------- | --------------------------------------- | ------------------- | ---------- | -------- | ---- |
|                                 |                                         |                     |            |          | 累入 |
| ▲                               | 名称                                    | 发布者              | Hypervisor | 处理器渊 | 格式 |
|                                 | SJaok rcwplotc - wdc                    | vOC-Srorc           | KW         | None     | raw  |
|                                 | Ubuntu Servo 13.10 *2 - vdc             | vOC-Sto»c           | KVM        | 功6-64   | raw  |
|                                 | Peppc^niint 4 Desktop - vdi             | VDC-Stort           | KVM        | m86.M    | raw  |
| 适                              | IXJ Bridge Direct Cloud SJavc Appliance | MTA SZTAKI LPOS     | KVM        | *«6.M    | raw  |
|                                 | Ky!!nuxwrno                             | □pcnNebuia.«g       | KVM        | x86_fc4  | r^w  |
|                                 | OpenNebub vlrwat Router                 | Opcnhiebuia.ofg     | di         | *£6.W    | raw  |
| ©                               | CentOS 6-5 - KVM                        | OpenNebuU Systems   | nVM        | X86.6i   | *4W  |
| &                               | CWtOS 7 KVM                             | Oper.Nebu»a SyMcnis | dil        | X86.&1   | raw  |
| 饴                              | Ubuntu 14.W- xvm                        | OponNcbtiU Systtm   | KVM        | X86.64   | raw  |
| 12                              | D«Jidn 7 - KVM                          | OpenNebula Systems  | KVM        | <«6.W    | raw  |
| SffsfMtig ；> <n^0a> 33 enfrw'S | Previous                                | 2    3 A            | NeXT       | 10 *     |      |

图 13.10 OpenNebula 市场

映像列表中详细列举了系统名称和版本、发布人、客户端类型、处理器架构及虚拟磁盘映 像使用的格式等。如果需要查看某个映像的详细信息只需要单击映像，将显示映像的详细描述。 在映像列表中勾选需要使用的映像，然后单击右上角的导入按钮，将弹出导入应用界面，如图 13.11所示。

X

导入应用

以下映像捋在系统中创連如巢你0修汐磁盘映像的费尠，随后可以在磁盘映像选质卡 中修改

迭择用于招盘睐像数据仓库 1： default    •

土 0映像名称    106B

CcniOS-6.5-ono-4.8

T®的模板将埔紉連在Opc州cbuU，以前的睐像将在SS中引用如累你想诿改横板 的費豹，随后可以在榍板选项卡中倦改

@禊板名打 CentOS 6.5 - KVM

导入

图13.11导入应用

在“导入应用”窗口中选择数据仓库，填入映像名称、模板名称，然后单击“导入”按钮 即可。然后在左侧的虚拟资源中的映像管理和模板管理中，可以看到导入的应用，但在下载完 成前映像和模板将无法使用。

使用导入应用的方式创建模板十分方便、快捷，但如果网络不通畅（下载地址为国外地址） 导致超时将会添加失败。

2.自制映像

磁盘映像有多种格式，如raw、qcow2、qed、vmdk、vdi等，这些格式都拥有不同的特性， 读者可阅读相关文档了解这些格式的特点。在本例中将采用KVM默认使用的qcow2作为映像 格式，建立映像过程如【示例13-16】所示。

【示例13-16】

\#此操作在控制端进行 [root@mal # cd /data/

\#创建一个虚拟磁盘，空间大小为

(root@mal data]# qemu-img create -f qcow2 CentOS6.5-x86_64-Desktop.qcow2 15G Formatting *CentOS6.5-x86_64-Desktop.qcow21, fmt~qcow2 size=l6106127360

encryption=off cluster一size=65536 lazy^refcounts^off

[root@mal data]# qemu-img info CentOS6.5~x86_64~Desktop.qcow2 image: CentOS6.5-x86__64~Desktop.qcow2

file format: qcow2

virtual size: 15G (16106127360 bytes) disk size: 196K cluster一size: 65536 Format specific information:

compat: 1.1

lazy refcounts: false

\#磁盘创建好之后就可以创建一个虚拟机 #将操作系统安装到创建的虚拟磁盘上 #创建虚拟机并为虚拟机指定磁盘和光驱 #参数m指定创建内存为1024M #参数-boot d表示使用光驱引导

\#参数-nographic -vnc : 0表示使用VNC远程访问控制台 #网卡参数也可不设

[rootSmal data]# /usr/libexec/qemu-kvm -m 1024 \    -

\>    -cdrom /data/CentOS-6.5-x86_64-bin-DVDl.isq \

\>    -drive file=/data/CentOS6.5-x86_64-Desktop.qcow2,if=virtio \

\>    -net nic,model=virtio -net tap, script=no -boot d -nographic ■-vnc 0

执行上述命令后，使用VNC Viewer在服务器地址中输入172.16.45.22: 5900，远程连接 到虚拟机，如图13.12所示。

图13.12 VNC远程连接虚拟机控制台

在虚拟机的控制台中将系统安装完成并作相应的设置后，在控制台中关闭系统，这样就得 到了一个安装了系统的虚拟磁盘。

接下来就需要将虚拟磁盘导入OpenNebula，可以使用两种方法导入映像，其一是使用CLI 命令方式；其二是在Sunstone中导入映像。无论使用哪种方式导入映像，都需要保证oneadmin 用户能读取映像文件，否则导入将失败。使用CLI命令方式导入过程如【示例13-17】所示。

【示例13-17】

\#以下命令在控制端执行

\#首行査看权限

[root0mal data]# 11 CentOS6<5-x86_64-Desktop.qcow2

-rw-r--r-- 1 root root 4236247040 Jul 7 11:02 CentOS6.5-x86_64~Desktop.qcow2 #切换用户到oneadmin并査看系统中的映像列表 [root@mal data]# su - oneadmin [oneadmin@mal oneimage list

ID USER    GROUP NAME    DATASTORE SIZE TYPE PER STAT RVMS

0 oneadmin oneadmin CentOS6.5~x8 default    15G OS No used 1

\#编辑导入文件，内容如下所示    ~

[oneadmin@mal ，3$ cat centos.one

NAME    -    HCentOS6,5-x86_64~Desktopn

PATH    -    w/data/CentOS6.5~x86_64-Desktop.qcow2n

TYPE    =    OS

DESCRIPTION = "centos 6.5 desktop"

DRIVER    =    qcow2

\#査看数据仓库

[oneadirtin@mal ~] $ onedatastore list

ID NAME    SIZE AVAIL CLUSTER    IMAGES TYPE DS    TM STA

![img](11 CentOS7fbdfa1060ed0f49e18-316.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-317.jpg)



| 0    | system  | 457.8G | 96%  | raainCluster | 0    | sys  | -    | shared | on   |
| ---- | ------- | ------ | ---- | ------------ | ---- | ---- | ---- | ------ | ---- |
| 1    | default | 457.8G | 96%  | mainCluster  | 1    | img  | fs   | shared | on   |
| 2    | files   | 457.8G | 96%  | mainCluster  | 0    | fil  | fs   | ssh    | on   |



\#将映像导入到default中

[oneadmin@mal $ oneimage create centos.one --datastore default ID: 1

释命令执行后添加的映像状态为lock 弈命令输出的映像名称不完全，但在Sunstone中显示正常 [oneadmin@mal -]$ oneimage list

ID USER    GROUP    NAME    DATASTORE

SIZE TYPE PER STAT RVMS 15G OS    No used    1

15G OS    No lock    0



0 oneadmin oneadmin CentOS6.5-x86_6 default 1 oneadmin oneadmin CentOS6.5-x86一6 default 毋导入后映像的状态将变为rdy

[oneadmin@mal $ oneimage list

SIZE TYPE PER STAT RVMS 15G OS    No used    1

15G OS    No rdv    0



ID USER    GROUP    NAME    DATASTORE

0 oneadmin oneadmin CentOS6.5~x86_6 default 1 oneadmin oneadmin CentOS6.5-x86_6 default

在Sunstone中添加映像需要在左侧选中虚拟资源中的映像管理，右侧窗口将显示当前映 像列表，单击列表上方的加号弹出创建映像窗口，如图13.13所示。

| X                                                           |                      |      |
| ----------------------------------------------------------- | -------------------- | ---- |
| 创建磁盘映像                                                | 向导    高级横式     |      |
| 名称oCcntO56.5- x^6_64-scn/cr                               |                      |      |
| 描述oserver                                                 |                      |      |
| 类型O                                                       | 鯉仓库O              |      |
| OS                                                          | V. default□歉性©     |      |
| 磁盘映倌位置：                                              |                      |      |
| 遞提供一卜路径e路径o/dato/Con tOS6.5-xS6>_64-scrvcr .qct»w2 | 上传    芏白Dawblock |      |
| •高级舰                                                     |                      |      |
| 重置                                                        |                      |      |
| 图 13.13                                                    | 创建映像窗口         |      |

在创建硬盘映像窗口中输入名称、描述，选择数据仓库并在路径中输入映像位置，在高级 中的驱动程序中输入qcow2,最后单击“创建”按钮即可添加新映像。同使用CLI方式相同， 新添加的映像在列表中的状态为锁定，当导入成功后状态将变为就绪。

13.3.4添加虚拟网络和模板

当映像导入成功之后，还需要创建虚拟网络才能添加模板，最后在模板的基础上创建 虚拟机。

1.添加虚拟网络

在Sunstone左侧的基础设施中选择虚拟网络，此时右侧将显示虚拟网络列表，单击列表 上方的加号，将显示创建虚拟网络页面，如图13.14所示。

创建虚拟网络

| 09                      | 遒5  |      |          |      |
| ----------------------- | ---- | ---- | -------- | ---- |
|                         | 0    | 芸   |          | 鼬   |
| 茉飙名称：0vncio描述：0 | BcS  | 舰   | Setortiy |      |

图13.14创建虚拟网络

在“常规”选项中输入虚拟网络的名称，然后在“配置”选项中输入网桥和网络模式，在 本例中输入网桥为brO，即桥接网络，网络模式保持默认。OpenNebula也支持802.1q协议的 多Vian中继，因此此处需要按实际情况选择。接下来需要在“地址”选项中输入IP起始地址 和大小（即地址数量），最后单击“创建”按钮，即可创建虚拟网络。

2.创建模板

在Sunstone界面的左侧选择虚拟资源中的模板管理，此时右侧将显示当前己存在的模板 列表，单击列表上方的加号将显示创建模板页面，如图13.15所示。

| Create Template                                |                                  |          |          |                  |
| ---------------------------------------------- | -------------------------------- | -------- | -------- | ---------------- |
|                                                | 向导    織蠡    —Hpxiit    籩 Ki |          |          |                  |
| E    S    ©    0常«    A•抽    转              |                                  | 鶄C^ww>- |          |                  |
| 名称©                                          |                                  | Hypcrvtw |          |                  |
|                                                |                                  | > KVM    | Vfciwarc | ® Xcn @ vC enter |
| 描述®                                          |                                  | Lago ©   |          | ••               |
|                                                |                                  |          | cost it  |                  |
|                                                | Si2                              | MB •     |          |                  |
| CPU ©                                          |                                  |          | Cost ©   |                  |
| vcpu &                                         |                                  |          |          |                  |
| 'ii Do net allow to change MpacHy ©            |                                  |          |          |                  |
| 诏 Oo n« jtiew co modify occwork configuros/on |                                  |          |          |                  |

图13.15创建模板页面

在“常规”选项的名称中输入模板名称，Hypervisor中选择节点类型，此处选择KVM, 在Logo中可以为模板选择一个图标，并选择合适的内存和CPU数量；在“存储”选项中为 模板选择磁盘映像；在“网络”选项中为模板选择虚拟网络；在“输入输出”中选择控制台设 备；最后在“调度”中选择运行模板的主机或集群，单击“创建”按钮即可。在本例中图形界 面选择VNC，监听IP中输入0.0.0.0，并设置访问密码。

13.3.5创建并访问虚拟机

创建虚拟机模板之后就可以将模板实例化为虚拟机，并运行虚拟机。创建虚拟机可以在 Sunstone界面左侧的虚拟资源中选择虚机管理，此时右侧将显示虚拟机列表。在列表上方单击 加号，将弹出创建虚拟机页面，如图13.16所示。

| 创建虚拟机                          | «■            |                                                 |                                                              |      |
| ----------------------------------- | ------------- | ----------------------------------------------- | ------------------------------------------------------------ | ---- |
| m—.制笛一个名称，以电军w羚重        | 祕*待e        |                                                 |                                                              |      |
| W名构崧                             | 5C阀的勤黴：* |                                                 |                                                              |      |
| 步#二.楔物                          |               |                                                 |                                                              |      |
| 10 T締者1    oneadmin0    oncaUnMi* | 群组QfieodRMn | 名称CentOSS,    Dirsklopfenios65-friiniOts*i oo | 'r檢注册时间1Ri5：?907/07/J015i&:26;2tO& 切氓 ISi*r"J4(SA 1    Nr |      |
| 纘从务廉中ttfl-个镌析               |               |                                                 | I                                                            | B    |

图13.16创建虚拟机页面

在步骤一中的VM名称中输入虚拟机名，然后在步骤二中选择一个模板，然后单击“创 建”按钮，就完成虚拟机创建了。创建完虚拟机之后，可以在虚拟机管理中查看到刚创建的虚 拟机。刚创建的虚拟机还不能立即访问，还需要等待系统分配资源，当资源分配完成后可以在 状态中看到虚拟机处于运行状态，此时就可以访问了。如果状态为错误或其他非正常的状态， 可以在列表中单击虚拟机，在虚拟机详细信息中选择日志，查看错误原因并排除错误。

当虚拟机处于运行状态时，可通过VNC客户端进行访问，访问端口可通过虚拟机详细信 息中的模板中查看（GRAPHICS信息）。另一个访问虚拟机的方法是在虚拟机列表中，运行的 虚拟机后有一个显示器图标，单击此图标将在网页中显示控制台，如图13.17所示。

图13.17网页访问虚拟机控制台

OpenNebula中虚拟机还有许多操作，读者可通过阅读其官方网站的相关说明了解详情并 进一步使用。

13*4小结

OpenNebula是一个功能十分强大而又简单的开源云计算平台，虽然目前国内使用的人较 少，但可以预见在不久的将来，将会有大量的用户使用。本章介绍了 OpenNebula的基本情况， 还介绍了 OpenNebula在CentOS 7中的安装和配置等内容。
