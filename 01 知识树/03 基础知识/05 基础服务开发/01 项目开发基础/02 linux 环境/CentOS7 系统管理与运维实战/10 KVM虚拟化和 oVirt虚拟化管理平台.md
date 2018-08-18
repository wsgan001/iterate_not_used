###### 55 10 S

###### >■ a* ■ W

KVM繡化翮oViri醐七冒理率台

虚拟化是最近几年来兴起的一个比较实用的技术，从各企业的使用来看虚拟化带来了许多 好处，例如：细化资源管理、低成本投入等。CentOS是较早支持虚拟化的Linux发行版之一。 早期的CentOS支持Xen虚拟化，但随着Red Hat公司收购KVM虚拟化，CentOS主要的虚拟 化软件由Xen过渡为KVM。本章将简要介绍KVM虚拟化及oVirt虚拟化管理平台的使用。

本章主要涉及的知识点有：

•    KVM虚拟化简介

•    CentOS中KVM虚拟化使用方法

•    oVirt虚拟化管理平台简介

•    oVirt虚拟化管理平台的安装和使用

10.1    KVM虚拟化

KVM (Kernel-based Virtual Machine)是一个基于内核的系统虚拟化模块，从Linux内核版本 2.6.20开始，各大Linux发行版就已经将其集成于发行版中。与Xen等虚拟化相比，KVM是需要 硬件支持的完全虚拟化(Xen的早期产品是基于软件的半虚拟化产品)。KVM由内核加载，并使 用Linux系统的调试器进行管理，因此KVM对资源的管理效率相对较高。在基于Linux操作系 统的虚拟化产品中占有较大份额，本节将简要介绍CentOS 7中KVM的安装和使用。

10.1.1    安装KVM虚拟化

同之前的CentOS 6—样，CentOS 7也将KVM作为虚拟化的基础部件之一，因此可以直 接通过yum工具安装。本小节简要介绍如何使用yum工具安装KVM虚拟化。

(1)环境配置

由于KVM使用的是基于硬件支持的虚拟化，因此CPU必须包含了相关的指令集。可以 在Linux系统中查看CPU是否包含了相关指令集，如【示例10-1】所示。

【示例10-1】

[rootQlocalhost # egrep *(vmx|svm)' /proc/cpuinfo

flags    : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge me a emov

pat pse36 clflush dts acpi 腿x fxsr sse sse2 ss ht tm pbe syscall nx rdtsep lm constant一tsc arch一perfmon pebs bts rep_good nopl xtopology nonstop一tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds__cpl vmx est tm2 ssse3 cx!6 xtpr pdem pcid sse4一 1 sse4一2 popent tsc_deadline_timer xsave avx lahf一lm arat epb xsaveopt pin pts dtherm tpr一shadow vnmi flexpriority ept vpid

查看CPU支持之后，还需要修改SELinux设置，将文件/etc/sysconfig/selinux中的 “SELINUX=enforcing” 修改为 “SELINUX=disabled” 并重启系统。

![img](11 CentOS7fbdfa1060ed0f49e18-196.jpg)



如果系统中查看CPU支持没有相应标志，可能需要修改BIOS相关设置以获得支持。关 于修改BIOS设置，读者可自行阅读相关文档修改。

(2)安装KVM

由于使用yum工具安装，因此必须正确设置IP地址、DNS等信息，确保网络畅通。安装 过程如【示例10-2］所示。

【示例10-2】

\#安装KVM相关软件包

[root@localhost -3 # yum -y install qemu-kvm libvirt virt-install bridge-utils Loaded plugins: fastestmirror, langpacks Loading mirror speeds from cached hostfile

\*    base: centos.ustc.edu.cn

\*    extras: mirrors.sina.cn

\*    updates: mirrors.sina.cn

Package 10:qemu-kvm-1.5.3-86.el7_l.2.x86__64 already installed and latest

version

Package bridge-utils-1.5-9.el7.x86_64 already installed and latest version

Resolving Dependencies

--> Running transaction check

---> Package libvirt.x86__64 0:1.2.8-16,el7_l.3 will be installed

-一> Processing Dependency: libvirt-daemon-driver-lxc = 1.2.8-16.el7_l.3 for

package: libvirt-1.2.8-16 .el7_l. 3 ,x86__64

--> Processing Dependency: libvirt-daemon-config-nwfilter = 1.2.8-16.el7_l.3

for package: libvirt-1.2.8-16. el7_l. 3. x86__64 ••••••

\#检查kvm模块是否加载

[rootGlocalhost    # lsmod | grep kvm

kvm 一intel    148081 0

kvm    461126    1 kvm_intel

(3)    开启服务

安装完成后还需要开启libvirtd，以开启相关支持：

[root@localhost ~]# systemctl start libvirtd [root@localhost # systemctl enable libvirtd

(4)    桥接网络

在虚拟机的网络连接中，使用的最多的莫过于桥接网络，所谓桥接网络是指将物理网络连 接到虚拟机中。新安装的KVM还没有桥接网络，需要手动添加。添加过程如【示例10-3】所

ZJ'* O

【示例10-3]

\#本例中物理网卡名为enp5s0    :兔'

[root@localhost network-scripts]# cat ifcfg-enp5s0

TYPE=nEthernetn

DEVICE=Menp5sOM

ONBOOT=wyesn

BRIDGE=.，brO，.

\#新建一个名为brO的桥接网卡并设置IP地址等信息

[root@localhost network-scripts]# cat ifcfg-brO

TYPE=nBridge"

BOOTPROTO=Hnone"

IPADDR=M172.16.45.35”

PREFIXES4

GATEWAY-n172.16.45.1M

DEVICE=brO

ONBOOT-nyesH

\#重启网络服务

[root@localhost ~]# systemctl restart network

[root@localhost    ifconfig

brO: flags-4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500

inet 172.16.45.35 netmask 255.255.255.0 broadcast 172.16.45.255 inet6 fe80::eea8;6bff:fea4:49fa prefixlen 64 scopeid 0x20<link> ether ec:a8:6b:a4:49:fa txqueuelen 0 (Ethernet)

RX packets 131 bytes 8307 (8.1 KiB)

RX errors 0 dropped 0 overruns 0 frame 0 TX packets 22 bytes 1823 (1.7 KiB)

TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

enp5s0: flags-4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500

inet6 fe80::eea8:6bff:fea4:49fa prefixlen 64 scopeid 0x20<link> ether ec:a8:6b:a4:49:fa txqueuelen 1000 (Ethernet)

RX packets 17350 bytes 3777406 (3,6 MiB)

RX errors 0 dropped 8 overruns 0 frame 0 TX packets 2830 bytes 325846 (318.2 KiB)

TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

桥接网络设置完后，KVM就已经安装完成了。

10.1.2 KVM虚拟机的管理方法

安装完Linux系统后，就可以管理KVM虚拟机了。管理KVM虚拟机通常可以使用两种 方法，其一是使用Linux系统图形界面下的虚拟系统管理器，其二是使用命令的方式，本小节 将分别介绍这两种方式。

1.虚拟系统龍器

虚拟系统管理器由软件包virt-manager提供，可以使用yum工具安装“yum install virt-manager”。安装完后可以通过单击桌面右上角的“应用程序”，然后依次单击“系统工具” 和“虚拟系统管理器”，弹出虚拟系统管理器界面，如图10.1所示。

图10.1虚拟系统管理器

由于没有新建任何KVM虚拟机，因此在虚拟系统管理器中没有看到任何虚拟机。虚拟系 统管理器可以用来创建、删除虚拟机，还可以管理虚拟机电源、编辑硬件等，几乎所有功能都 可以在虚拟系统管理器中实现。

2.命令M

除了图形界面工具虚拟系统管理器之外，KVM还提供了一些命令工具，使用这些命令工 具也可以达到管理虚拟机的目的，常见的命令形式如表10.1所示。

表10.1管理虚拟机的常见命令形式

| 命令形式            | 作用                                       |
| ------------------- | ------------------------------------------ |
| virt-install        | 用于创建虚拟机，具体选项可参考其手册页了解 |
| virsh list —all     | 查看所有虚拟机                             |
| virsh start name    | 启动名为name的虚拟机                       |
| virsh destroy name  | 停止名为name的虚拟机                       |
| virsh undefine name | 删除名为name的虚拟机                       |
| virsh console name  | 连接到名为name的虚拟机的控制台             |

除以上列举的命令形式之外，还有许多其他形式的用途各异的命令读者可自行阅读virsh 的手册页了解，此处不再赘述。

10.1.3使用图形工具创建虚拟机

安装完KVM虚拟机之后，就可以创建虚拟机并安装操作系统了。创建虚拟机可以使用图 形界面中的虚拟系统管理工具，也可以使用virt-install命令，本节将以虚拟系统管理工具创建 Linux虚拟机为例介绍如何创建虚拟机。

打开虚拟系统管理器，确保主界面中的“lOcalhOSt(QEMU)”处于连接状态，并在之上单 击鼠标右键，在弹出的菜单中选择“新建(N)”，将弹出新建虚拟机向导，如图10.2所示。

图10.2新建虚拟机向导

新建虚拟机向导要求选择安装介质的位置，从图10.2中可以看到KVM支持本地安装、 网络安装、PXE引导安装(需要操作系统支持)及使用现在硬盘文件几种方式。此处使用ISO 光盘映像文件安装，因此选择“本地安装介质”，单击“前进”按钮，向导要求输入ISO光盘 映像文件路径或使用光驱，如图10.3所示。

图10.3选择光盘映像和操作系统类型

此时选择“使用ISO映像”并单击后面的“浏览”按钮，将弹出“定位ISO介质卷”界 面，如图10.4所示。

|                             | 定位ISO介质卷            |
| --------------------------- | ------------------------ |
| :Storage Pools              |                          |
| ■    oerauit■    文件紐目录 | 名称大小    格式    用于 |
| !                           |                          |
| 本地浏览（旦）              | 取消（£）    滤卷（V:    |

图10.4 “定位ISO介质卷”界面

单击左下角的“本地浏览”按钮，在弹出的界面正确选择Linux安装光盘ISO映像所在位 置，并返回选择光盘映像和操作系统类型界面。软件可能不能正确识别ISO映像的操作系统， 因此可以取消选择“根据安装介质自动侦测操作系统类型”选项，并在之后的操作“系统类型" 和“版本”中正确选择。在本例中将安装CentOS 6.6，由于CentOS是RHEL的重编译版，二 者之间的区别很小，因此此处可选择“Linux”及“Red Hat Enterprise Linux 6.6”。然后单击前 进按钮，进入内存和CPU设置界面，如图10.5所示。

在内存和CPU设置界面需要对虚拟机的内存容量及CPU数量进行设置，通常情况下内存 容量越大、CPU数量越多表示虚拟机性能越好。此处可以按需要进行设置，如果没有特殊需 求，也可以保持默认设置。设置完成后，单击“前进”按钮，进入存储设置界面，如图10.6 所示。

图10.6设置存储界面

在存储设置界面需要为虚拟机设置合适的磁盘空间，此处按需要进行设置即可。需要注意 的是如果需要使用迁移功能，此时需要将硬盘映像的存储位置选择到远程存储上，而不是本地 磁盘中。选择合适的磁盘容量后，单击“前进”按钮，接下来向导要求用户确认配置，如图 10.7所示。

图10.7确认设置

在确认设置界面，向导会将之前的设置罗列出来，并自动为虚拟机命名(由于之前的虚拟 机类型中选择为RHEL6.6,此处向导自动命名为RHEL6.6,可更改为CentOS6.6,可保持默认)、 添加网卡。在尚级选项中可以定义虚拟机网络I此处选择之前设置的桥接网络brO，向导还会 自动为虚拟机设置一个MAC地址，MAC地址是网卡工作的必要条件，此项设置一般无须修 改。确认所有设置都正确后，单击“完成”按钮即可完成虚拟机的创建工作。

在向导完成虚拟机创建之后，虚拟系统管理器会立即打开虚拟机电源，并显示虚拟机的控 制台，如图10.8所示。

文件(F)鹿拟机(M)查看(V)发送技键(K)

■ .... v

Uelcome to CentOS 6.6!

jjistalI or upgrade an existing system Install system uitli basic video driver Rescue installed system

Boot from local drive Memory test

Press [Tab] to edit options

fiutonwtit： boot in 35 sw^onds -.

CentOS 6

Community €NTerpr(se Operating System

图10.8虚拟机控制台

由于新的虚拟机还没有安装操作系统，因此虚拟机使用了之前指定的ISO光盘映像引导， 此时只需要将操作系统正确安装就可以使用虚拟机了。

10.1.4使用virt-install创建虚拟机

使用图形界面创建虚拟机只适合能接触到系统桌面的情况，无论是直接在物理机上操作还 是通过VNC远程操作均可。但有许多计算机可能并没有安装桌面，用户可能更希望通过远程 的方式访问虚拟机，就像VMware的ESX那样通过客户端远程操作虚拟机。这时可行的方法 通常有两种：其一是使用VNC，其二是使用SPICE协议。本小节将以不使用图形界面为例介 绍如何通过上述方法创建和访问虚拟机。

\1. VNC远程访问

由于virt-install的选项和参数众多，因此在使用virt-install创建虚拟机之前建议先阅读其 手册页详细了解其参数和选项的使用方法。此处仍以CentOS 6.6作示例，其创建命令如【示 例10-4】所示。

【示例10-4】

[root@localhost 〜]# virt-install -n centos6.6-2 -r 1024 \

\>    --disk /var/lib/libvirt/iraages/rhel6.6-2.imgr size=10 \

\>    --network bridge=br0 --os-type=linux    os~variant=rhel6.6 \

\>    --cdrom /iso/CentOS-6.6-x86__64 . iso \

\>    --graphics vnc,port=5910,listen=,0.0.0.0* ^password^*redhat1

Starting install...

Allocating *rhel6.6-2.img*    1    10 GB 00:00

Creating domain...    } 0B 00:00

Cannot open display:

Run 'virt-viewer help' to see a full list of available command line options Domain installation still in progress. You can reconnect to the console to complete the installation process.

[root@localhost 〜]# netstat -tunlp | grep 5910

tcp    0    0 0.0.0.0:5910    0.0,0.0:*    LISTEN

7435/qemu~kvm

【示例10-4】创建虚拟机时使用的选项及参数如下：

•    常规设置：选项n和r分别指定了虚拟机的名称和内存容量。

•    磁盘设置：选项disk用于设置磁盘参数。参数/var/lib/libvirt/images/rhel6.6-2.img表示 磁盘文件名及存放路径，size参数则设置磁盘容量。

•    网络选项：选项network用于设置虚拟机的网络。参数bridge=brO表示使用桥接网络 brO。

•    操作系统类型：选项os-type用于设置操作系统类型，os-variant表示操作系统的版本，

•    光驱设置：选项cdrom在此示例中用于设定ISO光盘映像的路径。

•    图形选项：选项graphics用于设置图形、监视器等。参数 vnipor^SgiOJister^'O.O.O.O^passworddredhat'表示使用 VNC作为监视器，端口为 5910 （对应的桌面号为10），访问密码为redhat, listen='0.0.0.0’表示在物理机的所有接口 上监听。

![img](11 CentOS7fbdfa1060ed0f49e18-204.jpg)



以上均为较简单的设置，使用其他的参数还可以做更为复杂的设置，例如设置硬件的厂商 和类型等，读者可自行参考相关文档，此处不再赘述。

从【示例10-4】的两条命令输出中可以看到，虚拟机己经建立并在5910端口监听。此时 可以在远程的Windows计算机上打开VNC Viewer访问，如图10.9所示。

图 10.9 VNC Viewer



在VNC Viewer中输入服务器的IP地址和桌面号（注意不是端口号），单击“确定”按钮, 并输入建立虚拟机时设置的密码就可以访问到建立的虚拟机了，如图10.10所示。

图10.10使用VNC Viewer访问虚拟机



使用VNC Viewer访问虚拟机时，VNC Viewer也支持向虚拟机发送按键指令。发送按键 指令时在窗口上方的标题栏中单击右键，即可弹出指令菜单，如图10.11所示。

趣麵(R)    (

移动(M)

大＜HS)

-爆小化(N) a飲化(X)

x 关闻(Q    Att*F4

±^{F)

运动們

Ctrl

Alt

发送F8

糙 Ctrl-Alt-Del 卜 賺願(H)

颜(0)川

图10.1］指令菜单

从图10J1中可以看到，指令菜单中有一些VNC Viewer无法捕获的快捷键(使用这些快 捷键会被Windows或其他软件捕获)，鼠标单击相应的菜单项就可以向虚拟机发送快捷键。

\2. SPICE远程访问

与VNC远程访问相比，SPICE访问更加优秀，除了完全实现VNC的功能，SPICE还可 以支持视频播放GPU加速、音频传输、连接加密、多桌面及USB设备远程传输等。但SPICE 的缺点也比较明显，SPICE的配置相对比较复杂。

(1)安装软件

在使用SPICE之前必须确保系统中已经安装有spice-server等软件包，如果没有安装可参 考【示例10-5］所示安装。

【示例10-5】

[rootQlocalhost    yum install -y spice~gtk3 spice-server spice-protocol

Loaded plugins: fastestmirror, langpacks Loading mirror speeds from cached hostfile

\*    base: mirrors.sina.cn

\*    extras: centos•ustc.edu.cn

\*    updates: mirrors.sina.cn

示例所示的软件包中spice-gtk3是一个SPICE客户端，spice-server和spice-protocol用于 实现SPICE服务器。

(2)生成证书

由于SPICE协议是可以加密的，因此必须要为其生成证书才能使用，生成证书如【示例 10-6】所示。

Centos 7系统管理与运维实战

___________________________________J

【示例10-6】

\#生成证书的过程相对比较麻烦，此处仅为简单示例 •    #创建证书目录

[root^localhost 〜]# mkdir /etc/pki/libvirt-spice [root@localhost 〜]# cd /etc/pki/libvirt-spice/

\#创建CA并生成证书

[root@localhost libvirt-spice]# umask 077

[root@localhost libvirt-spice]# openssl genrsa 2048 > ca-key.pem Generating RSA private key, 2048 bit long modulus

4.4.4.

• • •

e is 65537 (0x10001)

[rootSlocalhost libvirt-spice]# openssl req -new -x509 -nodes -days 1095 \ > -key ca-key.pern -out ca-cert.pem

You are about to be asked to enter information that will be incorporated into your certificate request.

What you are about to enter is what is called a Distinguished Name or a DN. There are quite a few fields but you can leave some blank

For some fields there will be a default value, If you enter *.S the field will be left blank.

Country Name (2 letter code) [XX]:CN

State or Province Name (full name) []:Sichuan

Locality Name (eg, city) [Default City]:Chengdu

Organization Name (eg, company) [Default Company Ltd]:Example, Inc. Organizational Unit Name (eg, section)[]:

Common Name (eg, your name or your server’s hostname) []:vt.example•com Email Address []:

[root^localhost libvirt-spice]# openssl req -newkey rsa:2048 -days 1095 \ > -nodes -keyout server-key.pem -out server-req.pem Generating a 2048 bit RSA private key

• •■•••••••■••••••••■••••■••••a + + +

writing new private key to 1 server-key.pem'

You are about to be asked to enter information that will be incorporated

into your certificate

What you are about to There are quite a few For some fields there If you enter 1.*, the



request.

enter is what is called a Distinguished Name or a DN. fields but you can leave some blank will be a default value,

field will be left blank.

Country Name (2 letter code) [XX]:CN

State or Province Name {full name) []:Sichuan

Locality Name (eg, city) [Default City]:Chengdu

Organization Name (eg, company) [Default Company Ltd]:Exanqple, Inc. Organizational Unit Name (eg, section)[]:

Common Name (eg, your name or your server’s hostname) [3:vt.©xan^le.com Email Address []:

Please enter the following 1 extra' attributes to be sent with your certificate request A challenge password []:

An optional company name []:

[root@localhost libvirt-spice]# openssi rsa -in server-key.pem -out server-key.pem

writing RSA key

[rootSlocalhost libvirt-spice]# openssl x509 -req -in server-req.pem \

\>    -days 1095 -CA ca-cert.pem \

\>    -CAkey ca-key,pem -set一serial 01 \

\>    -out server-cert.pem Signature ok

subject=/C=CN/ST=Sichuan/L=Chengdu/O=Example, Inc./CN=vt.example.com Getting CA Private Key

![img](11 CentOS7fbdfa1060ed0f49e18-207.jpg)



本小节中的证书用过程仅为参考，并不具备在生产环境使用的条件。由 并不讨论安全问题，因此关于证书的安全性、证书的使用等问题并不涉及，读者可自行参 考相关资料了解。

(3)修改配置文件

接下来需要修改配置文件qemu.conf,启用SPICE的加密功能，修改过程如【示例10-7】 所示。

【示例10-7】

\#以下文件内容开始f文件的第112行

[root@localhost 〜]# cat /etc/libvirt/qemu.conf

\#    Enable use of TLS encryption on the SPICE server.

\#

\#    It is necessary to setup CA and issue a server certificate

\#    before enabling this.

\#

spice_tls = 1    #将此行的注释取消

\#以下为关于证书文件名称的相关说明 #如果需要使用证书必须确保文件名相同

\#

\#

\#

\#

\#



Use of TLS requires that x509 certificates be issued. The default it to keep them in /etc/pki/libvirt-spice. This directory must contain



![img](11 CentOS7fbdfa1060ed0f49e18-208.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-209.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-210.jpg)



ca-cert.pem - the CA master certificate

server-cert.pem - the server certificate signed with ca-cert.pem server-key.pem - the server private key



![img](11 CentOS7fbdfa1060ed0f49e18-211.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-212.jpg)



\#

\#

This option allows the certificate directory to be changed.



\#在执行以下命令之前最好确保虚报机都已经关机 #重启libvirtd服务让配置文件生效

[root@localhost # systemctl restart libvirtd

(4)创建虚拟机

使用SPICE协议时，创建虚拟机过程与VNC几乎相同，不同的是此处需要指定控制台为 SPICE，如【示例10-8】所示。

【示例10-8]

[root@localhost    virt-install -n centos6.6-3 -r 1024 \

\>    disk /var/lib/libvirt/images/rhel6.6-3.imgf size=10 -一network bridge^brO \

\>    ~~os~type=linux --os~variant=rhel6.6 --cdrom /iso/CentOS-6.6-x86一64.iso \

\>    --graphics spice,port=5931rlisten-* 0.0.0.01,password=,redhat1

Starting install...

Creating domain...    I 0 B 00:00

Cannot open display:

Run ’virt—viewer --help1 to see a full list of available command line options Domain installation still in progress. You can reconnect to the console to complete the installation process.

[root@localhost # netstat -tunlp J grep 5931

tcp    0    0 0.0.0.0:5931    0.0.0.0:*    LISTEN

9944/qemu-kvm

![img](11 CentOS7fbdfa1060ed0f49e18-214.jpg)



由于在本例中使用的是自建证书，因此在创建虚拟机的命令选项中，并没有使用加密的 SPICE。创建完虚拟机之后，就可以使用SPICE客户端访问虚拟机了。

(5) SPICE客户端访问

SPICE为Windows用户开发了相应的客户端程序，读者可以从其官方网上下载。

客户端下载地址：<http://www.spice-space.org/download.html>

官方网站提供两种访问方法，其是SPICE的客户端，另一个是Windows版的Virt Viewer, 安装过程都比较简单，根据官方网站上的说明下载安装即可。

使用SPICE客户端访问时，直接输入IP地址、端口及密码就可以直接访问，如图10.12 所示。

图10.12 SPICE客户端访问虚拟机

使用Virt Viewer访问时，会要求输入链接地址，链接地址形如：spice://ipaddress:port，例 如本例中应输入spice://172.16.45.35:5931。输入链接地址后就可以连接到远程虚拟机，如图 10.13所示。

图10.13 Virt Viewer远程连接

无论使用SnCE客户端还是VirtViewer连接，都可以传输虚拟机的音频、使用本地USB 设备等，与VMware公司的ESX客户端相同。

读者可以阅读相关文档了解SPICE客户端及Virt Viewer的更多使用方法，此处不再赘述。

1临2 oVirt虚拟化管理平台

oVirt 是 Red Hat 公司下的 RHEV （Red Hat Enterprise virtualization,红帽企业虚拟化）的 开源版本，主要用来管理和部署虚拟化主机。oVirt由两部分组成，客户端称为oVirt Node, 与VMware公司的ESXi类似，主要用来实现主机的虚拟化；另一部分称为oVirt-engine，类 似于VMware vCenter，主要用来管理虚拟化主机。本节将介绍如何在CentOS 7中安装和使用 oVirt虚拟化管理平台。

10.2.1 oVirt-engine虚拟化管理平台概述

时至今日，KVM虚拟化可以说已经深入人心，包括IBM、Ubuntu、Red Hat在内的许多 Linux发行版都将其作为默认的kypervisor。而oVirt虚拟化管理平台正是Red Hat公司下的 RHEV的开源版本，可以说是为小型企业应用环境量身定制。oVirt提供了一个Web管理工具， 利用Web管理工具可以实现许多功能：

•    与vCenter类似，oVirt也可以完成虚拟机的基本管理，包括创建虚拟机、快照功能、 虚拟机模板克隆等。

•    高可用的在线或离线迁移虚拟机（需要存储支持）。

•    查看、统计虚拟机、宿主机的性能。

•    多样化的网格连接。

oVirt虚拟化管理平台的功能还有许多，此处不再一一列举，读者可自行参考相关资料了 解。尽管oVirt还有许多缺点，例如不能精细地调节系统资源等，但由于其成本低，使用方便 深受小型企业用户喜爱。

10.2.2 oVirt管理平台的安装

oVirt管理平台的安装过程十分简便，其官方网站上对其有十分详尽的说明。 oVirt 官方网站：http://www.ovirt.org/Home

oVirt管理平台目前的最新版本为3.5,本小节将以3.5为例介绍其在CentOS 7上的安装过程。 在开始安装之前还需要安装一些额外的部件，主要包括DNS域名服务器、iSCSI存储和

NFS存储。DNS域名服务器的安装过程可参考第3章中的相关章节，目标是能解析 oVirt-engine、Node及NFS存储等。iSCSI、NFS存储可用来虚拟相关数据，NFS存储还需要

用来存储ISO光盘映像。本节将采用一个最简单的结构简单介绍oVirt平台的使用，其主要的 主机、IP地址等信息如表10.2所示。

表10.2 oVirt平台示例主机信息

| 域名             | IP地址       | 说明                   |
| ---------------- | ------------ | ---------------------- |
| ma.example.com   | 172.16.45.35 | 用于安装oVirt-engine   |
| node.example.com | 172.16.45.39 | 用于安装oVirt Node     |
| 无               | 172.16.45.42 | iSCSI存储              |
| ma.example.com   | 172.16.45.35 | NFS存储，用于建立ISO域 |

在本示例中，NFS存储、DNS域名服务器、oVirt管理平台为同一台主机，但为了减少延 迟，在实际使用过程中NFS存储直接使用IP地址而不是域名。与VMware的ESXi相同，oVirt 也支持包括Vian内的多种网络，但在本示例中并不会涉及。

(1)    安装软件仓库

由于oVirt在RHEL及CentOS中推荐使用yum的方式安装，因此第一步首先要安装yum 仓库包：

[root@localhost 〜yum install <http://resources.ovirt.org/pub/yum-repo/ovirt-release35.rpm>

以上命令将从官方网站上直接下载包含有仓库配置文件、Key等文件的安装包，并进行安 装，安装完成后可以从目录/etC/yum.repOS.d中查看到软件仓库配置文件。

(2)    安装 oVirt-engine

在管理机上安装oVirt-engine之前，确保已经设置好IP地址、系统软件已全部为最新版本 等信息，网络接口上最好使用静态IP地址。接下来就可以安装oVirt-engine 了，安装过程如【示 例10-9】所示。

【示例10-9】

[root^localhost -]# yum install -y ovirt-engine Loaded plugins: fastestmirror, langpacks Loading mirror speeds from cached hostfile

*    base: mirrors.btte.net

\*    extras: mirrors.sina.cn

\*    ovirt-3.5: mirror.linux.duke.edu

\*    ovirt-3 >S'-epel: [ftp.cuhk.edu.hk](ftp://ftp.cuhk.edu.hk)

\*    updates: mirrors.sina.cn Resolving Dependencies

--> Running transaction check

---> Package ovirt-engine.noarch 0:3.5.2.1-1.el7.centos will be installed

由于【示例10-9】所示命令需要从oVirt官方网站上下载近900MB的数据，因此整个安

装过程可能要持续约1小时，需耐心等待。

(3)初始化 oVirt-engine

安装完成后，还需要对oVirt-engine进行初始化，这个过程主要是用来配置密码、防火墙、 数据库、ISO域等信息。如【示例10-10】所示。

【示例10-10】

\#初始化过程需要用户确认配置信息，按实际情况输入即可 [root@ma -]# engine-setup [INFO ] Stage: Initializing [INFO ] Stage: Environment setup

Configuration files:

['/etc/ovirt-engine-setup.conf.d/10-packaging-jboss.conf *,

’/etc/ovirt-engine-setup.conf.d/10-packaging.conf1]

Log file:

/var/log/ovirt-engine/setup/ovirt~engine~setup~20150610195044-8gpxck.log Version: otopi~l.3.1 (otopi-1,3.1~1,e!7)

| [INFO | ]Stage: | Environment packages setup |
| ----- | ------- | -------------------------- |
| [INFO | ]Stage: | Programs detection         |
| [INFO | ]Stage: | Environment setup          |
| [INFO | ]Stage: | Environment customization  |

\#在设置过程中会询问用户设置选项 # “门”中的设置为默认设置 #选项及更新检查

=PRODUCT OPTIONS

Configure Engine on this host (Yes, No) [Yes]:

Configure WebSocket Proxy on this host (Yes, No) [Yes]:

―PACKAGES ===—

[INFO 3 Checking for product updates...

[INFO ] No product updates found

ALL IN ONE CONFIGURATION ~--

\#网络设置    •    •

.#已关闭防火墙因此此处跳过防火墙设置

—===== NETWORK CONFIGURATION

Setup can automatically configure the firewall on this system.

Note: automatic configuration of the firewall may overwrite current

settings.

Do you want Setup to configure the firewall? (Yes, No) [Yes]: No Host fully qualified DNS name of this server [ma.example.com]:

\#数据库选项通常保持默认

![img](11 CentOS7fbdfa1060ed0f49e18-217.jpg)



一s DATABASE CONFIGURATION ——

Where is the Engine database located? (Local, Remote) [Local]:

Setup can configure the local postgresql server automatically for the

engine to run. This may conflict with existing applications.

Would you like Setup to automatically configure postgresql and create

Engine database, or prefer to perform that manually? (Automatic, Manual) [Automatic]:

\#设置访问密码

—==OVIRT ENGINE CONFIGURATION

Engine admin password:

Confirm engine admin password:

Application mode (Virt, Glusterf Both) [Both]:

--==PKI CONFIGURATION ==--

Organization name for certificate [example.com]:

弁设置httpd服务

--=« APACHE CONFIGURATION ==--

Setup can configure the default page of the web server to present the application home page. This may conflict with existing applications.

Do you wish to set the application as the default page of the web server?

(Yes, No) [Yes]:

Setup can configure apache to use SSL using a certificate issued from the internal CA.

Do you wish Setup to configure that, or prefer to perform that manually? (Automatic, Manual) [Automatic]:

\#系统配置项

一—=SYSTEM CONFIGURATION -#此处跳过ISO域配置 #将在后续小节中介紹此设置

Configure an NFS share on this server to be used as an ISO Domain? (Yes, No) [Yes]: No

—MI SC CONFIGURATION ==—

END OF CONFIGURATION ==——

[INFO ] Stage: Setup validation

[WARNING] Less than 16384MB of memory is available #安装清单

CONFIGURATION PREVIEW

'Application mode

both

False

ma.example.com :engine :False :localhost :engine :False :5432 :True example.com :True :True :True :True

ma.example.com



Update Firewall    :

Host FQDN    :

Engine database name

Engine database secured connection

Engine database host

Engine database user name

Engine database host name validation

Engine database port

Engine installation

PK工 organization

Configure local Engine database

Set application as default page

Configure Apache SSL

Configure WebSocket Proxy

Engine Host FQDN    ;

[OK]:



[INFO [INFO [INFO [INFO [INFO [INFO [INFO [INFO [INFO [INFO



Please confirm installation settings (OK, Cancel) ]Stage: Transaction setup ]Stopping engine service

]Stopping ovirt-fence-kdump-listener service ]Stopping websocket-proxy service ]Stage: Misc configuration ]Stage: Package installation ]Stage: Misc configuration ]Creating PostgreSQL * engine1 database ]Configuring PostgreSQL

} Creating/refreshing Engine database schema

[INFO ] Creating CA

[INFO 3 Configuring WebSocket Proxy

[INFO ] Generating post install configuration file

[INFO ] Stage: Transaction conunit [INFO ] Stage: Closing up #以下为安装结束时显示的有关访问信息



SUMMARY ==

[WARNING] Less than 16384MB of memory is available

SSH fingerprint: B6:F6:E8:93:80:95:A1:3D:OF:6D:37:F4:B5:2F:2C:D4 Internal CA

15:Cl:AD:Al:12:04:44:C2:18:13:4F:34:6E:4F:21:52:CD:D4:93:C5 Web access is enabled at:

http://ma,example.com:80/ovirt-enginehttps://ma,example.com:443/ovirt-engine Please use the user ,'admin,T and password specified in order to login In order to configure firewalld, copy the files from

/etc/ovirt-engine/firewalld to /etc/firewalld/services

| and execute the following commands:                          |      |
| ------------------------------------------------------------ | ---- |
| firewall-cmd -service ovirt-postgres                         |      |
| firewall-cmd -service ovirt-https firewall-cmd -service ovirt-fence-kdump-listener firewall-cmd -service ovirt-websocket-proxy firewall-cmd -service ovirt-nfsfirewall-cmd -service ovirt-httpThe following network ports should be opened:tcp:111 tcp:2049 tcp:32803 tcp:443 tcp:5432 tcp:6100 tcp:662 tcp:80 tcp:875 tcp:892 udp:111 udp:32769 udp:662 udp:7410 udp:875 udp:8 92An example of the required configuration for iptables can be found at: /etc/ovirt-engine/iptables.example--==END OF SUMMARY =====--[INFO ] Starting engine service [INFO ] Restarting httpd (INFO ] Stage: Clean upLog file is located at |      |
| /var/log/ovirt~eng.ine/setup/ovirt-engine-setup-20150610195044-8gpxck.log [INFO ] Generating answer file*/var/lib/ovirt-engine/setup/answers/20150610195255-setup.conf1 [INFO ] Stage: Pre-termination [INFO ] Stage: Termination[INFO ] Execution of setup completed successfully |      |

安装过程中的许多设置保持默认即可。需要注意的是，以上初始化过程根据不同的计算机 情况，可能会稍有不同。

羽 I 1/7，口 o



在初始化oVirt管理平台的过程中可能会失败，此时可以继续使用engine-setup命令尝试

^Sr -in /P

安装完成后即可在浏览器中输入地址http7/ipaddreSS/访问管理平台，如图10.14所示。

| Ovirt-Engine - MoziUa Firefox     |       |
| --------------------------------- | ----- |
| :/ O Ovirt-Cncme    » . ♦         |       |
| ♦ •$172 16 45 35                  |       |
| OVirt OPEN VIRTUALIZATION MANAGER | oVirt |
| 欢迎使用开放式游拟化靑瑚器    VT  |       |
| 门产    下钱8腿】騄個             |       |

图10.14 oVirt管理平台

管理平台分为三个功能门户区，功能分别是用户管理、平台管理及实时报表，可以使用初 始化过程中设置的admin用户登录管理。

10.2.3 oVirt Node 安装

oVirt Node的作用与VMware的ESXi相似，也是一个基于Linux的操作系统，主要用来 实现虚拟机运行和基本管理。oVirt Node的下载地址如下所示。

oVirt Node 官方下载地址：http://resources01.phx.ovirt.org/releases/stable/iso/

在官方提供的下载地址中，有多个版本的oVirt-node提供下载，本例中将采用

ovirt-node-iso-3.0.4-1.0.2014012912O4.el6 作为示例安装使用。

下载oVirt Node光盘映像并刻录为光盘后，就可以使用光盘引导系统。引导系统后，其引

导菜单如图10.15所示。

oUir! Node Hypervisor 3.B.4 (1.0.201401291204.elG)

Install or Upgrade Bool (Basic Uideo)

Install (Basic UideoJ

Install or Upgrade with serial console

Reinstall

Reinstall (Basic Uideo)

Reinstall uith serial console Uninstall

Boot fron local driue

Press fTobl to edit opt ions

图10.15 oVift-node引导菜单

oVirt Node的引导菜单中提供了比较丰富的功能选项，需要注意的是，如果选择重新安装 必须要输入原系统的密码方可进行。在本例中选中“Install or Upgrade”，并按Enter键进入下 一步安装=接下来安装程序会检测系统中是否已安装有oVirt Node,并要求选择进一步的操作, 如图10.16所示。

图10.16安装选择界面

如果系统中已安装有oVirt Node,会在安装选择界面中显示，井提示用户选择是否重新安 装或升级原有系统。

由于oVirt Node底层仍然采用的是KVM虚拟化，因此安装程序将检测系统是否支持硬件 虚拟化。如图10.16所示，安装程序提示没有在当前系统中检测到硬件虚拟化支持，此时可参 考相关资料，在BIOS中打开硬件虚拟化支持选项。确认没有任何问题，可按Enter键继续安 装。接下来安装程序会要求用户选择键盘布局，如图10.17所示。

oUirt Node Hyperuisoi* 3.0.1 1.8.201401291204 .elb

图10.17选择键盘布局

键盘布局需要按实际情况进行选择，本示例中将保持默认，即选择“U.S. English”，然后 将光标移动到“Continue”上按Enter键继续。接下来安装程序将要求用户选择安装的磁盘， 如图10.18所示。

图10.18磁盘选择

如果系统中存在多个磁盘，需要在磁盘选择界面中选择合适的磁盘安装。由于oVirtNode 系统占用磁盘空间并不大，并且现在的服务器大部分都支持U盘启动系统，因此在实际应用 环境中，通常将oVirtNode安装到U盘中，以节省磁盘空间。在本例中将采用默认存储设备， 在默认的磁盘上按Enter键选中磁盘，然后将光标移动到“Continue”上按Enter键继续。接下 来安装程序会提供一个默认的分区方案，如图10.19所示。

图10.19确认分区方案

如图10.19所示，安装程序提供了一个默认的分区方案要求用户确认。通常不需要修改此 默认分区方案，将光标移动至“Next”上按Enter键。接下来安装程序会要求用户设置用户admin 的密码，如图10.20所示。

| oUirt Node Hypervisor 3 .R .4-1.0-.28H01231204 .c 16         |      |        |                          |
| ------------------------------------------------------------ | ---- | ------ | ------------------------ |
| Require a password for the admin user?                       |      |        | 騙醐i9s職嘯賊,i'1 { / ”( |
| Password r                                                   |      |        | 匕7勞曝'7                |
| Conf iriu Password :                                         |      | 2一    | --                       |
|                                                              |      | .- --' |                          |
|                                                              |      |        |                          |
|                                                              |      |        |                          |
| ;讀賴紀;s仰補g赛藝奔喊:，警鐘< Quit >    < Back >    < Install > |      |        |                          |
| —■-    ■■    ■-    1    z ' •>    '    S    : ' : _    -    :    ■    - |      |        |                          |
| Press esc to quit.                                           |      |        |                          |

图10.20设置用户密码

与Linux系统不同的是，oVirt Node管理员不再是root,而是admin。此处设置好admin 的用户密码之后，就可以将光标移动到Install上按Enter键开始安装了。

整个安装过程视计算机性能不同而不等，大约需要3~10分钟。安装完成后，安装程序会 要求用户重新启动计算机，如图10.21所示。

oUirt Wade Hypervisor 3.0.4-1.0.281401291281.e16

Installing oUirt Mode Hypervisor

Start ing ...

(lz5) Writing configuration file (Done)

(2/5) Partitioning and Creating File Systems on 1 r/dev/mapper/lftTAJ|EMU__HABinnSK__QM00的 1* r (Done) (3/5) Setting Admin Password (Done)

(4/5) Installing Image and Bootloader Configuration to */dcv/«apper/lATA_QEMU_HARDDISK_Qtffl0001* (Done)

(5/5) Setting keyboard layout to * us* (Done)

< Reboot >

Press esc to quit.

图10.21安装完成

此吋按Enter键即可重新启动系统，系统重启后界面如图10.22所示。

oUirt Mode Hypervisor release 3.8.4 (1.8.elb) Kernel 2.6.32-431.3.1 .e 16. xfif»_64 on x86_64 (ttyl)

Uirttta I izat inn bardunre is    i lab le .

(Mo virtna1izAtion hardware was detected on this system)

Please login as * adwin* to conf igure the node local host login:

图10.22 oVirt Node启动界面

此时输入安装时设置的用户admin及其密码即可进入oVirt Node»

10.2.4 oVirt Node 设置

oVirt Node安装完成后，还需要对其进行设置，主要包括IP地址、DNS域名、管理密码 等。进入oVirt Node系统，选择主界面左侧的“Network”，此时右侧界面将显示网络配置界面， 如图10.23所示。

图10.23网络配置界面

在网络配置界面中的“Hostname”后面输入主机名，“DNS Server 1 ”后面输入域名服务 器地址。最后将光标移动到“Available System NICs”下面的网络接口卡ethO上，按Enter键, 将弹出网络接口配置界面，如图10.24所示。

| NIC Details： elh8                          |                                                              |                                                              |
| ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Driver：    8139cpLink Status: Disconnected | Vendor：    Kealtek Semiconductor CoMAC Address: 52:54：08：3a：ba :9b |                                                              |
| IPv4 Settings Bootprotocol:IP Address:      | < )Disabled 172.16.45.33                                     | ()DHCP    (X) StaticMetmask:    255.255.255.0                |
| Gateway：                                   | 172.16.45.1                                                  |                                                              |
| IPv6 Settings Bootprotocol:IP Address:      | (X) Disabled    (                                            | Auto    ( ) DHCP    C ) Static„"... Prefix Length:    _ ........................................................ |
| Gateway:                                    |                                                              |                                                              |
| ULAM ID.                                    |                                                              |                                                              |
| Use Bridge:                                 |                                                              | E 3                                                          |
| < Flash Lights                              | to Identify >                                                | *                                                            |
| < Save >    <                               | Close >                                                      |                                                              |

图10.24网络接口配置界面

选中“IPv4 Settings”后面的“Static”，然后在后面分别设置IP地址、子网掩码及默认网

关，最后保存退出即可生效。

除了网络设置外，还需要对服务器，即oVirt Engine进行配置，通过键盘的上下方向键选 中左侧的oVirt Engine，此时右侧将显示oVirt Enging配置，如图10.25所示。

图 10.25 oVirt Engine 配置界面

在oVirt Engine配置界面，需要配置Management Server的IP地址并获取证书，然后还需 要为SSH守护进程设置密码。SSH守护进程密码主要是为了保证oVirt Engine能正确连接到 oVirt Node,设置好以上两项之后将光标移动到“Save & Register”并按Enter键即可生效。



10.2.5 oVirt虚拟化管理平台设置

完成前面几个小节的内容之后，就可以将相关的资源添加到oVirt Engine管理平台，并使 用这些资源建立虚拟机了，本小节将简单介绍如何添加资源及管理平台设置等内容。

![img](11 CentOS7fbdfa1060ed0f49e18-228.jpg)



oVirt Engine管理平台可以在Linux或Windows下的IE、Firefox等浏览器访问，但各版本 安装的软件却并不相同，可通过单击管理平台主界面•中的“控制台客户资源'了解，此处 不再赘述。

(1)创建数据中心

在oVirt中，一台或几台版本相同的oVirt Node组成一个集群。在同一集群内，位于不同 oVirt Node之上的虚拟机可以互为冗余，即其中一台Node宕机，之上的虚拟机会自动在别的 Node上运行。多个集群又可以组成数据中心，数据中心内的集群将共用中心的资源，如存储、 ISO域等均可共用，因此oVirt虚拟化平台管理的第一步是创建数据中心。

本节将采用一个单节点Node作为示例，介绍如何使用oVirt平台。首先登录oVirt虚拟化 管理平台，并单击主界面左侧的“数据中心”，然后选择右侧的“新建”，此时将弹出“新建数 据中心”窗口，如图10.26所示。

图10.26新建数据中心窗口

在名称后面输入数据中心名，类型选择本地，兼容版本需要选择oVirt Node的版本，在本 例中选择3.4,其他设置保持默认即可，单击“确定”按钮就可以完成数据中心的创建。

(2)创建集群

完成数据中心的创建后，管理平台会弹出向导窗口，要求用户配置一个新的集群，单击配 置集群就可以弹出创建集群界面。如果没有弹出向导窗口，可以在左侧单击新建的数据中心名， 然后在右侧选择集群选项卡，并单击选项卡中的新建即可弹出“新建集群”窗口，如图10.27 所示。

图10.27新建集群窗口

在“新建集群”窗口的常规设置中，选择数据中心(系统己自动选择)，输入集群名，并

选择Node使用的CPU类型、版本信息，然后在优化设置中为虚拟机设置合适的优化策略。 由于本示例中采用单节点Node,因此集群策略可略过，单击"确定”按钮就可以完成集群创 建。

(3)为集群添加主机

oVirt Node在管理平台中称为主机，创建完集群后管理平台将弹出向导提示用户添加或选 择主机。如果没有弹出向导，可在左侧窗口中依次选择新建的数据中心、集群名称，在右侧的 主机选项卡中选择新建，即可弹出“新建主机”窗口，如图10.28所示。

图10.28 “新建主机”窗口

在新建主机窗口中选择主机所属的数据中心和集群，然后在名称、地址、SSH端口中正 确输入oVirt Node的相应信息，最后在验证中输入主机的root用户密码。如果需要验证输入 是否正确，可在高级参数中的SSH指印下单击获取，如果此时显示SSH指印则说明主机信息 输入正确，否则就需要检查上述输入。由于本示例中采用单节点，因此电源管理等选项可略过 (电源管理选项主要用于多节点集群中)，单击“确定”按钮就可以完成主机添加。

主机添加完成后，管理平台会立即要求主机初始化并安装启动相关服务，因此在添加完成 后的一段时间内，主机将处于不可用状态，直到上述步骤完成。当主机完成初始化、安装等步 骤后，会立即将其状态更新为Up,如图10.29所示。

測I主机I网銘I雜I虚姐机|地|翻

脉理编格妒1    維护选为SPM ?'：.彳岣I:,: 、分4i标类职解能力

名»    主机名/p    負霧    的件心 狄态    ▼虚报« 内存 CPU    m格    SPM

' -    • - 'i5    ?-M，'•    F\ - ?<?.

图10.29主机状态

在主机状态一栏中显示为Up,表明主机已初始化完成并可用。如果主机状态不可用，此 时将在主窗口下面的事件中显示日志信息，也可以在右侧窗口的事件选项卡中查看完整的日志 事件。

![img](11 CentOS7fbdfa1060ed0f49e18-232.jpg)



在创建数据中心和集群时，一定要注意oVirtNode的版本及硬件类型，错误的设置将会因j

主机与数据中心和集群信息不匹配，从而导致主机添加失败。

S'-w’-ww-w”.................................................................... ............................ ...........----------------

10.2.6配置资源

经过前面几个小节的配置，oVirt平台已经可以正常使用，但虚拟平台的最终目标是建立 虚拟机，还需要存储等资源，本小节将简要介绍如何配置资源。

(1)使用oVirt Node本地存储

oVirt可供使用的存储方案有多种，例如Node本地存储、NFS、iSCSI等。本地存储虽然 有诸多限制(例如不能使用故障迁移)，但其配置简单，特别适合单节点使用。配置本地存储 可在管理界面左侧单击系统，然后在右侧选择存储并单击新建域，将弹出“新建域”窗口，如 图10.30所示。

图10.30新建本地存储域



选择正确的数据中心，并在域功能/存储类型中选择“Data/LocalonHost”，系统会自动在 使用主机和路径中填入相应的参数，最后单击“确定”按钮即可添加完成。需要注意的是，仅 当安装oVirtNode时添加了数据分区时，本地存储选项才可用，否则将无法使用本地存储。

(2)建立ISO域

ISO域是由所有数据中心共享使用的存储资源，其作用是为虚拟机提供安装光盘映像。在 10.2.2小节中安装过程中，跳过了 ISO域的配置，因此必须手动建立ISO,建立过程如【示例

10-11】所示。

【示例10-11】

\#此处略过NFS安装过程

[root@ma *■] # mkdir -p /export/iso

\#此配置并没有考虑安全等因素，读者可参考NFS安全相关文档了解

[root@ma -]# cat /etc/exports

/export/iso *(rwz sync,no_subtree一check,all_squash,anonuid=36,anongid=36) [root@ma -]# chmod -R 777 /export/iso [root@ma    systemctl start nfs

\#确认配置

[root@ma    showmount -e ma.example.com

Export list for ma.example.com:

/export/iso *

建立NFS共享之后就可以在管理平台上添加ISO域了，单击平台左侧的系统，然后在右 侧依次选择存储、新建域，将弹出新建域窗口，如图10.31所示。

图10.31添加ISO域

在新建域窗口中填入名称，选择当前的数据中心，并在域功能/存储类型中选择 “ISO/NFS”，最后在导出路径中输入NFS共享路径，单击“确定”按钮完成添加。添加完成 后，ISO域还需要初始化，因此需要等待一段时间之后才可用。

添加完ISO域之后，还需要在ISO域中添加光盘映像才能使用，添加过程需要在oVirt 管理平台上完成，其过程如【示例10-12】所示。

【示例10-12】

\#此操作在管理平台上用命令进行 #先査看iso域的名称

\#以下两个操诈都需要输入管理平台用户admin的密码 [roottoa # engine-iso-uploader list

Please provide the REST API password for the admin@internal oVirt Engine user (CTRL+D to abort):

ISO Storage Domain Name 丨 Datacenter    丨 ISO Domain Status

ISOs    I MyDC    I active

\#向ISOs域添加光盘映像

[root@ma 〜]# engine-iso-uploader upload -i ISOs CentOS-5.5-i386-bin-DVD,iso

Please provide the REST API password for the admin@internal oVirt Engine user (CTRL+D to abort):

Uploading, please wait…

INFO: Start uploading CentOS-5.5-i386-bin-DVD.iso

INFO: CentOS-5.5-i386-bin-DVD.iso uploaded successfully

完成上述步骤后，就可以在管理平台的存储中添加ISO域，并在之下的映像选项卡中查 看到上传的光盘映像。

也可以使i    过程与添域类似，此处不=再赘述。

------------------------------------- -------------------------1—— ---------------------------------------------------------------------------------------------------------j

(3) iSCSI 存储

iSCSI无疑是应用最广泛的存储解决方案之一，oVirt也支持iSCSI作为其数据存储。添加 iSCSI存储首先选中左侧的系统，然后在右侧的存储选项中单击新建域，将弹出新建域窗口， 如图10.32所示。

新建域

名称    ^CSI__42    _ _ _    _____ J 描述

使用主机    j node example.com____



荃0,由1 < S§1



[■、发现目标-

地址    n用户验证：

端口    网---------------------j    O4AP用户1:    CHAP密挺

唇J

| 舀解名称                               | 機    端口 |      |       |                                   |      |
| -------------------------------------- | ---------- | ---- | ----- | --------------------------------- | ---- |
| © iqn.2015-06.dev.iscsl-target.sdbLOW® | 祕*小      |      | 鑛酶© | 172.16 45 42    3260产伽    ‘辨贈 | 争   |
| 1IET_OOO1OOO1                          | 149GB      | 1    | IET   | VIRTUAL DISK SlET VIRTUAL-DP      |      |



确定取消

图10.32添加iSCSI存储

在名称中输入iSCSI名称，数据中心选择“(none)”，域功能/存储类型选择“Data/iSCSI”， 此时窗口将自动显示发现目标按钮。单击发现目标并输入地址和端口号，然后单击“发现”， 窗口将自动显示发现结果。此时需要单击右侧的“登录全部”按钮，并单击发现的LUN之前 的“+”，显示全部磁盘信息，选中相应磁盘并单击“确定”按钮就可以添加完毕。

![img](11 CentOS7fbdfa1060ed0f49e18-236.jpg)



iSCSI存储通常是全局性的，只有附加到某个数据中心上才能使用。附加时需要注意只有 当数据中心的类型为共享，并且拥有活动主机的情况下才能附加。’

10.2.7建立虚拟机

在前面几个小节中介绍了如何建立一个最基本的oVirt单节点平台，在确认所有资源都可 用的情况下就可以建立虚拟机了。在左侧窗口中选择数据中心，然后在右侧的虚拟机选项卡中 选择新建虚拟机，将弹出新建虚拟机窗口，如图10.33所示。

图10.33新建虚拟机



在新建虚拟机窗口中选择合适的操作系统，并在名称中为操作系统命名，然后在nicl后 面为虚拟机添加网卡。由于本例中并没有添加网络，因此可以选择“ovirtmgmt”使用管理网 络。

完成常规设置后，需要单击系统选项为虚拟机设置合适的内存大小、CPU数量及时区。 由于是第一次配置，还需要在引导选项中为新系统添加安装光盘映像，如图10.34所示。

在引导序列中勾选附加CD选项，并在之后的选择框中选择合适的光盘映像，完成所有步 骤之后，即可单击“确定”按钮完成创建。接下来会弹出引导窗口，要求用户为虚拟机添加虚 拟磁盘，如图10.35所示。

图10.35添加虚拟磁盘

在添加虚拟磁盘窗口中，直接输入磁盘大小并选择相应的存储域即可完成添加，也可勾选 “外部（直接LUN）”将iSCSI存储作为虚拟磁盘使用。

添加完虚拟磁盘后，虚拟机就已经添加完成了。可以选择虚拟机，然后单击右键，在弹出 的菜单中选择“运行”，即可打开虚拟机电源。打开虚拟机电源后，可以再次单击右键，在弹 出的菜单中选择“控制台”，即可打开虚拟机的控制台，如图10.36所示。

C«rrtOS5.S:l -按 SHIFT+F12 释放光标-Remote Viewer

:文件（f> 查管On 发送按键（s） 帮助（h）_

複 CentOS

图10.36虚拟机控制台

关于虚拟机控制台程序的安装说明，可参考oVirt主界面链接“控制台客户资源”中的相 关内容，此处不再赘述。

307

#### 10.3小结

当今的互联网以云计算和虚拟化技术为主体，CentOS 7在发布之初就已经吸收整合了 RHEL7的虚拟化技术。本章以KVM虚拟化为起点，介绍了 CentOS 7中的KVM虚拟化解决 方案，及当前最新的oVirt管理平台。虽然在大型企业中，这些平台应用较少，但在经费紧张 的小型企业中却应用非常广泛。
