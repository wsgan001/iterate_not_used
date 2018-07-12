
九 Linux网络酉己置

Linux系统在服务器占用较大份额，使用计算机首先要了解网络配置，本节主要介绍Linux 系统的网络配置。

3.3.1 Linux网络相关配置文件

Linux网络配置相关的文件根据不同的发行版目录名称有所不同，但大同小异，主要有以 下目录或文件。

(1)    /etc/hostname：主要功能在于修改主机名称。

(2)    /etc/sysconfig/network-scrips/ifcfg-enoN：是设置网卡参数的文件，比如 IP 地址、子 网掩码、广播地址、网关等，N为一串数字。

(3)    /etc/resolv.conf：此文件设置了 DNS相关的信息，用于将域名解析到IP。

(4)    /etc/hosts：计算机的IP对应的主机名称或域名对应的IP地址，通过设置 /etc/nsswitch.conf中的选项可以选择是DNS解析优先还是本地设置优先。

(5)    /etc/nsswitch.conf (name service switch configuration,名字服务切换配置)：规定通过 哪些途径，以及按照什么顺序通过这些途径来查找特定类型的信息。

3.3.2配置Linux系统的IP地址

要设置主机的ip地址，可以直接通过终端命令设置，如想设置在系统重启后依然生效, 可以通过设置对应的网络接口文件，如【示例3-11】所示。•

【示例3-11】

主要字段的含义如表3.7所示。

表3.7网卡设置参数说明

| 参数         | 说明                                  |
| ------------ | ------------------------------------- |
| TYPE         | 设备连接类型，此处为以太网            |
| BOOTPROTO    | 使用动态IP还是静态IP                  |
| IPADDRO      | 第一 IP地址                           |
| PREFIXO      | 第一 IP地址对应的子网掩码长度         |
| GATEWAYO     | 第一 IP地址对应的网关                 |
| DNSl 和 DNS2 | DNS服务器地址                         |
| DEFROUTE     | 是否为默认路由                        |
| ONBOOT       | 系统启动时是否设置此网络接口          |
| NAME         | 设备名，此处对应网络接口为eno33554984 |

设置完ifcfg-ethO文件后，需要重启网络服务才能生效，重启后使用ifconfig查看设置是 否生效：

[root@CentOS network-scripts]# service network restart

同一个网络接口可以使用子接口的方式设置多个IP地址，如【示例3-12】所示。

【示例3-12]

up



[root@CentOS -]# ifconfig eno33554984:2 192.168.146.152 netmask 255.255.255.0



[root@CentOS network-scripts]# ifconfig

eno33554984: flags=4163<UP,BROADCAST,RUNNING,MULTICAST〉 mtu 1500

inet 192.168.146.150 netmask 255.255.255.0 broadcast 192.168.146.255 inet6 fe80::20c:29ff:fe0b:780 prefixlen 64 scopeid 0x20<link> ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

RX packets 6453 bytes 6525511 (6.2 MiB)

RX errors 0 dropped 0 overruns 0 frame 0 TX packets 2023 bytes 167541 (163.6 KiB)



TX errors 0



dropped 0



overruns 0



carrier 0



collisions 0



eno33554984:2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST〉 mtu 1500

inet 192.168.146.152 netmask 255.255.255.0 broadcast 192.168.146.255 ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

当服务器重启或网络服务重启后，子接口配置将消失，如需重启后依然生效，可以将配置 子接口命令加入/etc/rc.local文件中。

3.3.3设置主机名

主机名是识别某个计算机在网络中的标识，设置主机名可以使用hostname命令即可。在 单机情况下主机名可任意设置，如以下命令，重新登录后发现主机名已经改变。

[root@CentOS network-scripts]# hostname [www.example.com](http://www.example.com)

如要修改重启后依然生效，可以将主机名写入文件/etc/hostname中。如【示例3-13】所示。

【示例3-13]

[root@www 〜]# hostname [www.example.com](http://www.example.com)

3.3.4设置默认网关

设置好IP地址以后，如果要访问其他的子网或Internet,用户还需要设置路由，在此不做 介绍，这里采用设置默认网关的方法。在Linux中，设置默汄网关有两种方法：

(1)第1种方法就是直接使用route命令，在设置默认网关之前，先用route - n命令查 看路由表。执行如下命令设置网关。

[root@CenOS /]# route add default gw 192.168.1.254

如果不想每次开机都执行route命令，则应该把要执行的命令写入/etc/rc.d/rc.local文件中。

(2)第2种方法是在/etc/sysconfig/network-scripts/ifcfg-接口文件中添加如下字段:

GATEWAY=192.168.10.254

同样，只要是更改了脚本文件，必须重启网络服务来使设置生效，可执行下面的命令:

[root@CentOS /]# service network restart

■ ■

使用service命令时需要注意，由于CentOS 7中使用的是systemd，因此开启和停止服务 通常使用systemctl代替，但也可以使用service。

使用命令方式配置默认路由通常适用于临时测试。

3.3.5设置DNS服务器

设置DNS服务器需修改/etc/resolv.conf文件即可。下面是一个resolv.conf文件的示例。

【示例3-14】

[root@CentOS -]# cat /etc/resolv.conf nameserver 192.168.3.1 nameserver 192.168.3.2 options rotate

options timeout:1 attempts:2

其中192.168.3.1为第一名字服务器，192.168.3.2为第二名字服务器，option rotate选项指 在这2个dns server之间轮询，option timeout: 1表示解析超时时间Is （默认为5s），attempts 表示解析域名尝试的次数。如需添加DNS服务器，可直接修改此文件，需要注意的是使用 nameserver指定的DNS服务器只有前三条生效。
