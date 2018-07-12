

Linux高级网络配置工具



目前很多Linux在使用之前的arp、ifconfig和route命令。虽然这些工具能够工作，但它 们在Linux 2.2和更高版本的内核上显得有一些落伍。无论对于Linux开发者还是Linux系统 管理员，网络程序调试时数据包的采集和分析是不可少的。tcpdump是Linux中强大的数据包 采集分析工具之一。本节主要介绍iproute2和tcpdump的相关知识。

3.5.1高级网络管理工具iproute2

相对于系统提供的arp、ifconfig和route等旧版本的命令，iproute2工具包提供了更丰富 的功能，除了提供了网络参数设置，路由设置，带宽控制等功能，最新的GRE隧道也可以通 过此工具进行配置。

现在大多数Linux发行版本都安装了 iproute2软件包,如没有安装可以使用yum工具进行 安装，应该注意的是yum工具需要联网才能使用。iproute2工具包中主要管理工具为ip命令。 下面将介绍iprOUte2工具包的安装与使用。安装过程如【示例3-21】所示。

【示例3-21】

[root@CentOS Packages]# yum install -y iproute #安装过程省略

![img](11 CentOS7fbdfa1060ed0f49e18-62.jpg)



[root@CentOS Packages}# rpm -qa!grep iproute iproute-3.10.0-13.el7.x86_64 #检查安装情况

[root@CentOS Packages]# ip ~V

ip utility, iproute2-ssl30716

ip命令的语法如【示例3-22]所示。

【75例3-22】

[root@CentOS 叫# iP help

Usage: ip [ OPTIONS ] OBJECT { COMMAND I help }

ip [ -force 3 -batch filename

where OBJECT :- { link | addr I addrlabel I route I rule I neigh I ntable { tunnel J tuntap ( maddr | mroute I mrule | monitor J xfrm | netns 1 12tp J tcp一 metrics 丨 token }

OPTIONS := { -V[ersion] | -s[tatistics] I -d[etails] I -r[esolve] I -f[amily] { inet J inet6 1 ipx | dnet | bridge I link } |

-4 | -6 j -I j —D \ -B | -0 }

-1[oops] { maximum-addr-flush-attempts } I -o[neline] I ~ttimestamp] | ~b[atch] [filename] }

-rc[vbuf] [size]}

1.使用ip命令来查看网络配置

ip命令是iproute2软件的命令工具，可以替代ifconfig、route等命令，查看网络配置的用 法如【3-23】所示。

【示例3-23】

\#显示当前网卡参数，同ipconfig [root@CentOS # ip addr list

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 inet 127.0.0.1/8 scope host lo

valid一1ft forever preferred_lft forever inet6 ::1/128 scope host

valid一1ft forever preferred 一1ft forever

2: enol6777736:〈BROADCAST,MULT工CAST,UP,LOWERJJP> mtu 1500 qdisc pfifo一fast state UP qlen 1000

link/ether 00:0c:29:0b:07:76 brd

inet 192.168.128.133/24 brd 192.168.128.255 scope global dynamic enol6777736 valid_lft 1149sec preferred 一1ft 1149sec

inet6 fe80::20c:29ff:feOb:776/64 scope link valid_lft forever preferred 一Ift forever

3: eno33554984: <BROADCASTZMULTICAST,UP,LOWER_UP> mtQ 1500 qdisc pfifo_fast state UP qlen 1000

link/ether 00:0c:29:0b:07:80 brd

inet 192.168.146.150/24 brd 192.168.146.255 scope global eno33554984 valid 一1ft forever preferred一1ft forever

inet6 fe80;:20c;29ff:feOb:780/64 scope link valid一1ft forever preferred_lft forever

\#添加新的网络 i址

[root^CentOS -]# ip addr add 192.168.128.140/24 dev enol6777736 Croot@CentOS 〜]# ip addr list #部分结果省略

4: enol6777736: 〈BROADCAST,MULTICAST,UPrLOWERJJP〉 mtu 1500 qdisc pfifo__fast state UP qlen 1000

link/ether 00:0c:29:0b:07:76 brd' ff:ff:ff:ff;ff:ff

inet 192.168.128.133/24 brd 192.168.128.255 scope global dynamic enol6777736 valid_lft 1776sec preferred一Ift 1776sec

inet 192.168.128.140/24 scope global secondary enol6777736 valid_lft forever preferred_lft forever

inet6 fe80::20c:29ff:fe0b:776/64 scope link

valid_lft forever preferred_lft forever #删除网络地址"

[root@CentOS # ip addr del 192.168,3.123/24 dev ethO

上面的命令显示了机器上所有的地址，以及这些地址属于哪些网络接口。“inet”表示 Internet (IPv4)。ethO的IP地址与192.168.3.88/24相关联，“/24”指IP地址表示网络地址的位 数，“lo”则为本地回路信息。

2.显示路由信息

如需查看路由信息，可以使用“ip route list”命令，如【示例3-24】所示。

【示例3-24]

\#査看路由情况

[root@CentOS 〜]眷 ip route list

default via 192.168.146.2 dev eno33554984 proto static metric 1024 192.168.128.0/24 dev enol6777736 proto kernel scope link src 192.168.128.133 192.168.146.0/24 dev eno3 3554 984 proto kernel scope link src 192.168.146.150 [root@CentOS 〜1# route -n

Kernel IP routing table

| Destination   | Gateway       | Genmask       | Flags | Metric | Ref  | Use Iface |
| ------------- | ------------- | ------------- | ----- | ------ | ---- | --------- |
| 0.0.0.0       | 192.168.146.2 | 0.0,0.0       | UG    | 1024   | 0    | 0         |
| eno33554984   |               |               |       |        |      |           |
| 192.168.128.0 | 0.0.0.0       | 255.255.255.0 | U     | 0      | 0    | 0         |
| enol6777736   |               |               |       |        |      |           |
| 192.168,146.0 | 0.0.0，0      | 255.255.255.0 | U     | 0      | 0    | 0         |

eno33554984 #添加路由

[rootSCentOS # ip route add 192.168.3.1 dev eno33554984

上述示例首先查看系统中当前的路由情况，其功能和route命令类似。 以上只是初步介绍了 iproute2的用法，更多信息请查看系统帮助。

3.5.2网络数据采集与分析工具tcpdump

tcpdump即dump traffic on a network,根据使用者的定义对网络上的数据包进行截获的包 分析工具。无论对于网络开发者还是系统管理员，数据包的获取与分析是最重要的技术之一。 对于系统管理员来说，在网络性能急剧下降的时候，可以通过tcpdump工具分析原因，找出造 成网络阻塞的来源。对于程序开发者来说，可以通过tcpdump工具来调试程序。tcpdump支持 针对网络层、协议、主机、网络或端口的过滤，并提供and, or, not等逻辑语句过滤不必要 的信息。

![img](11 CentOS7fbdfa1060ed0f49e18-63.jpg)



Linux系统下tcpdump普通用户是不能正常执行，一般通过root用户执行。

tcpdump采用命令行方式，命令格式如下，参数说明如表3.12所示。

tcpdump [ -adefInNOpqStvx ]    [ -c 数量][-F 文件名]

[-i 网络接口] [ -r 文件名][-s snaplen j f -T类型][-w文件名][表达式]

表3.12 tcpdump命令参数含义说明

| 参数  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| -A    | 以ASCII码方式显示每一个数据包，在程序调试时可方便查看数据    |
| -a    | 将网络地址和广播地址转变成名字                               |
| -C    | tcpdump将在接收到指定数目的数据包后退出                      |
| -d    | 将匹配信息包的代码以人们能够理解的汇编格式给出               |
| -dd   | 将匹配信息包的代码以C语言程序段的格式给出                    |
| -ddd  | 将匹配信息包的代码以十进制的形式给出                         |
| -e    | 在输出行打印出数据链路层的头部信息                           |
| -f    | 将外部的Internet地址以数字的形式打印出来                     |
| -F    | 使用文件作为过滤条件表达式的输入，此时命令行上的输入将被忽略 |
| -i    | 指定监听的网络接口                                           |
| -1    | 使标准输出变为缓冲行形式                                     |
| -n    | 不把网络地址转换成名字                                       |
| -N    | 不打印出host的域名部分                                       |
| -q    | 打印很少的协议相关信息，从而输出行都比较简短                 |
| -r    | 从文件file中读取包数据                                       |
| -s    | 设置tcpdump的数据包抓取长度，如果不设置默认为68字节          |
| -t    | 在输出的每一行不打印时间戳                                   |
| -tt   | 不对每行输出的时间进行格式处理                               |
| -ttt  | tcpdump输出时，每两行打印之间会延迟一个时间段，以ms为单位    |
| -tttt | 在每行打印的时间戳之前添加日期的打印                         |
| -V    | 输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息 |
| -w    | 输出详细的报文信息                                           |
| -vw   | 产生比-W更详细的输出                                         |
| -X    | 当分析和打印时，tcpdump会打印每个包的头部数据，同时会以十六进制打印出每个包的数据，但不包括连接层的头部 |
| -XX   | tcpdump会打印每个包的头部数据，同时会以十六进制打印出每个包的数据，其中包括数据链路 层的头部 |
| -X    | tcpdump会打印每个包的头部数据，同时会以十六进制和ASCII码形式打印出每个包的数据，但 不包括连接层的头部 |
| -XX   | tcpdump会打印每个包的头部数据，同时会以十六进制和ASCII码形式打印出每个包的数据，其 中包括数据链路层的头部 |

首先确认本机tcpdump是否安装，如没有安装，可以使用【示例3-25】中的方法安装。

【示例3-25】

\# 安装 tcpdump

[rootQCentOS Packages]# yum install -y tcpdump #安装过程省略

tcpdump最简单的使用方法如【示例3-26】所示。 【示例3-26】

[root@CentOS Packages]# tcpdump -i any

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode listening on any, link-type LINUX一SLL (Linux cooked), capture size 65535 bytes 15:47:05.143823 IP 192.168.146.150.ssh > 192.168.146.1.52161: Flags {P.), seq

1017381117:1017381313, ack 1398930582, win 140, length 196

15:47:05.144050 IP 192.168.146.1.52161 > 192.168.146.150.ssh: Flags [.], ack

196, win 16169, length 0

15:47:06.148824 IP 192.168.146.150.56971 > ns.sc.cninfo.net.domain: 29605+PTR? 1.146.168.192.in-addr.arpa. (44)

15:47:06.158878 IP ns.sc.cninfo.net.domain > 192.168.146.150.56971: 29605 NXDomain 0/0/0 (44)

\#部分结果省略，按下Ctrl+C中止输出

以上示例演示了 tcpdump最简单的使用方式，如不跟任何参数，tcpdump会从系统接口列 表中搜寻编号最小的已配置好的接口，不包括loopback接口，—旦找到第1个符合条件的接 口，搜寻马上结束，并将获取的数据包打印出来。

tcpdump利用表达式作为过滤数据包的条件，表达式可以是正则表达式。如果数据包符合 表达式，则数据包被截获；如果没有给出任何条件，则接口上所有的信息包将会被截获。

表达式中一般有如下几种关键字：

(1)    第1种是关于类型的关键字，如host、net和port。例如host 192.168.16.150指明 192.168.16.150 为一台主机，而 net 192.168.16.150 则表示 192.168.16.150 为一个网络地址。如 果没有指定类型，默认的类型是host。

(2)    第2种是确定数据包传输方向的关键字，包含src、dst、dst or src和dst and src,这 些关键字指明了数据包的传输方向。例如src 192.168.16.150指明数据包中的源地址是 192.168.16.150,而dst 192.168.16.150则指明数据包中的目的地址是192.168.16.150。如果没有 指明方向关键字，则默认是src or dst关键字。

(3)    第3种是协议的关键字，如指明是TCP还是UDP协议。

除了这3种类型的关键字之外，还有3种逻辑运算，取非运算是“not”或“!”，与运算 是“and”或“&&”，或运算是“or”或“||”。通过这些关键字的组合可以实现复杂强大的 条件。接下来看一个综合【示例3-27】所示。

【示例3-27】

[root@CentOS ~3 # tcpdump ~i any tcp and dst host 192.168.19.101 and dst port 3306 -slOO -XX -n

tcpdump: verbose output suppressed, use -v or ~vv for full protocol decode listening on any, link-type LINUX_SLL (Linux cooked), capture size 100 bytes 16:08:05.539893 IP 192.168.19.101.49702 > 192.168.19.101.mysql: Flags [P.seq

79:108, ack 158, win 1024, options [nop,nop,TS val 17107592 ecr 17107591], length 29

| 0x0000: | 0000 | 0304 | 0006  | 0000 | 0000 | 0000 | 0000 | 0800 | • • • • » ». • ■ •«.             |
| ------- | ---- | ---- | ----- | ---- | ---- | ---- | ---- | ---- | -------------------------------- |
| 0x0010: | 4508 | 0051 | f fe8 | 4000 | 4006 | 929b | c0a8 | 1365 | E. .Q.    @......e               |
| 0x0020: | c0a8 | 1365 | c226  | Ocea | 32aa | f5e0 | c46e | c925 | .• . 6« & • • 2 • ■ • • n. %     |
| 0x0030: | 8018 | 0400 | a85e  | 0000 | 0101 | 080a | 0105 | 0a88 | • • • • • : • • • • •+ • • • • » |
| 0x0040: | 0105 | 0a87 | 1900  | 0000 | 0373 | 656c | 6563 | 7420 | .........select.                 |
| 0x0050: | 2a20 | 6672 | 6f6d  | 206d | 7973 | 7I6c |      | ■ .* | .from.mysql                      |

以上tcpdump表示抓取发往本机3306端口的请求。“-i any”表示截获本机所有网络接口 的数据报“tcp”表示TCP协议“dst host”表示数据包地址为192.168.19.101, “dst port” 表示目的地址为3306,    “-XX”表示同时会以十六进制和ASCII码形式打印出每个包的数

搌” -si00"表示设置tcpdump的数据包抓取长度为100个字节，如果不设置默认为68字节， “-n”表示不对地址如主机地址或端口号进行数字表示到名字表示的转换。输出部分 “16:08:05”表示时间，然后是发起请求的源IP端口和目的IP和端口，“Flags[P.]”是TCP 包中的标志信息：S是SYN标志，F表示FIN, P表示PUSH，R表示RST,    则表示没有

标记，详细说明可进一步参考TCP各种状态之间的转换规则=

70
