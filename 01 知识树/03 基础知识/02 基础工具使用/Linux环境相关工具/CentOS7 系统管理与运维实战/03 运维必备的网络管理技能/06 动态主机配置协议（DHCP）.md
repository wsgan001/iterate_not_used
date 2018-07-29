
3.6动态主机配置协议（DHCP ）

使用动态主机配置协议（Dynamic Host Configuration Protocol, DHCP）可以避免网络参数 变化后一些烦琐的配置，客户端可以从DHCP服务端检索相关信息并完成相关网络配置，在 系统重启后依然可以工作。DHCP基于C/S模式，主要用于大型网络。DHCP提供一种动态指 定IP地址和相关网络配置参数的机制。本节主要介绍DHCP的工作原理及DHCP服务端与 DHCP客户端的部署过程。

3.6.1 DHCP的工作原理

动态主机配置协议（DHCP）是用来自动给客户端分配TCP/IP信息的网络协议，如IP地 址、网关、子网掩码等信息。每个DHCP客户端通过广播连接到区域内的DHCP服务器，该 服务器会相应请求返回包括IP地址、网关和其他网络配置信息。DHCP的请求过程如图3.4 所示。

DHCPDISCOVER

图3.4 DHCP请求过程



客户端请求IP地址和配置参数的过程有以下几个步骤:

客户端需要寻求网络IP地址和其他网络参数，然后向网络中广播，客户端发出的请 求名称叫DHCPDISCOVER。如广播网络中有可以分配IP地址的服务器，服务器会 返回相应应答，告诉客户端可以分配，服务器返回包的名称叫DHCPOFFER,包内 包含可用的IP地址和参数。

如果客户在发出DHCPOFFER包后一段时间内没有接收到响应，会重新发送请求， 如广播区域内有多于一台的DHCP服务器，由客户端决定使用哪个。

[03.



【04



■步:骤



当客户端选定了某个目标服务器后，会广播DHCPREQUEST包，用以通知选定的 DHCP服务器和未选定的DHCP服务器。.

服务端收到DHCPREQUEST后会检查收到的包，如果包内的地址和所提供的地址一 致，证明现在客户端接收的是自己提供的地址；如果不是，则说明自己提供的地址 未被采纳。如被选定的服务器在接收到DHCPREQUEST包以后，因为某些原因可能 不能向客户端提供这个IP地址或参数，可以向客户端发送DHCPNAK包。

客户端在收到包后，检查内部的IP地址和租用时间，如发现有问题，则发包拒绝这 个地址，然后重新发送DHCPDISCOVER包。如无问题，就接受这个配置参数。

3.6.2配置DHCP服务器

本节主要介绍DHCP服务器的配置过程，包含安装，配置文件设置，服务器启动等步骤。

1.软件安装

DHCP服务依赖的软件可以从rpm包安装或从源码进行安装，本节以yum工具为例说明 DHCP服务的安装过程，如【示例3-28】所示。

【示例3-28】

\#确认当前系统是否安装相应软件包

[root@CentOS 〜]# rpm -qa|grep dhcp #如以上命令无输出说明没有安装dhcp #如使用rpm安装，使用如下命令

[root@CentOS Packages]# yum install -y dhcp Loaded plugins: fastestmirror, langpacks

| base                                                         | 1 3.6 kB | 00:00 |
| ------------------------------------------------------------ | -------- | ----- |
| extras                                                       | 1 3.4 kB | 00:00 |
| updatesLoading mirror speeds from cached hostfile*    base: mirrors.yun-idc.com*    extras: mirrors.pubyun.com*    updates: mirrors.yun-idc.com•••••• | I 3.4 kB | 00:00 |

经过上面的设置，DHCP服务已经安装完毕，主要的文件如下:

/etc/dhcp/dhcpd.conf 为 DHCP 主酉己置文件。

:

/usr/lib/systemd/system/dhcpd.service DHCP 服务单元o

2.编辑酉己置文件/etc//dhcpd.conf

要配置DHCP服务器，需修改配置文件/etc/dhcp/dhcpd.conf。如果不存在则创建该文件。 本示例实现的功能为当前网络内的服务器分配指定IP段的IP地址，并设置过期时间为2天。 配置文件如【示例3-29】所示。

【示例3-29】

[root@CentOS Packages]# cat -n /etc/dhcp/dhcpd.conf

\#指定接收DHCP请求的网卡的子网地址，注意不是本机的IP地址。netmask为子网掩码

1    subnet 192.168.19.0 netmask 255.255.255.0{

\#指定默认网关

2    option routers 192.168.19.1;

\#指定默认子网掩码

3    option subnet-mask 255.255.255.0;

\#指定最大租用周期

4    max-lease-time 172800 ;

\#此DHCP服务分配的IP地址范围

5    range 192.168.19.230 192.168.19.240;

6    }

以上示例文件列出了一个子网的声明，包括routers默认网关、subnet-mask默认子网掩码 和max-lease-time最大租用周期，单位是秒。需要特别说明的是，在本地须有一个网络接口的 IP地址为192.168.19.0网络，DHCP服务才能启动。

配置文件的更多选项可以使用命令“man dhcpd.conf”获取更多帮助信息。

【示例3-30]

[root@CentOS Packages]# systemctl start dhcpd.service

如启动失败可以参考屏幕输出定位错误内容，或查看/var/log/messages的内容，然后参考 dhcpd.conf的帮助文档。

3.6.3配置DHCP客户端

当服务端启动成功后，客户端需要与服务端网络联通，然后做以下配置以便自动获取ip 地址。客户端网卡配置如【示例3-31】所示。

【示例3-31】

[root@CentOS # cat /etc/sysconfig/network-scripts/ifcfg-ethl DEVICE^ethl

HWADDR^O0:0c:29:be:db:d5

TYPE^Ethernet

UUID=363f47a9-dfb8-4c5a-bedf-3f060cf99eab

ONBOOT~yes

NM一CONTROLLED=yes

BOOTPROTO^dhcp

如需使用DHCP服务，BOOTPROTO=dhcp表示将当前主机的网络IP地址设置为自动获 取方式。需要说明的是DHCP客户端无须使用CentOS 7，使用其他版本的Linux或Windows 操作系统均可，在本例中使用的是CentOS 6作为客户端。测试过程如【示例3-32】所示。

【示例3-32】

[root@CentOS -]# service network restart

\#启动成功后确认成功获取到指定IP段的IP地址。

[rootQCentOS ~]# ifconfig

ethl Link encap:Ethernet HWaddr 00:0C:29:BE:DB:D5

inet addr:192.168.19.230 Beast:192.168.19.255 Mask:255.255.255.0 inet6 addr: fe80::20c:29ff:febe:dbd5/64 Scope:Link

UP BROADCAST RUNNING MULTICAST MTU:1500 Metrical RX packets:573 errors:0 dropped:0 overruns:0 frame:0 TX packets:482 errors:0 dropped:0 overruns:0 carrier:0 collisions:0 txqueuelen:1000

RX bytes:59482 (58.0 KiB) TX bytes:67044 (65.4 KiB)

客户端配置为自动获取IP地址，然后重启网络接口，启动成功后使用ifconfig查看成功 获取到IP地址。

![img](11 CentOS7fbdfa1060ed0f49e18-65.jpg)



本节介绍了 DHCP的基本功能，如需了解DHCP其他更多的功能，可参考DHCP的帮助 文档或其他资料。
