

Linux的登录



CentOS安装完之后，需要第一次配置并登录使用，Linux系统的登录方式有多种，本节

第2章跟我学CentOS的安装

X..    -    .    .......

主要介绍Linux的常见登录方式，如本地登录或通过相关软件远程连接等。

2.3.1首次配置与本地登录

在前面的章节中，主要介绍了如何使用不同的方法安装CentOS 7,本小节将简要介绍 CentOS 7的首次配置和本地登录等内容。

(l)CentOS7安装完成后重启即可使用，首次进入时还需要做一些简单的配置，如图2.30

所示。

![img](11 CentOS7fbdfa1060ed0f49e18-37.jpg)



本地化



许町倍班＜u



图 2.30



初始设置



CJSNTQSUNfUX?(O,W)



首次进入系统会要求用户确认许可信息，单击“许可信息”，接受CentOS的许可证进入 下一步设置。接下来会要求用户确认是杏启用Kdump,如图2.31所示。

![img](11 CentOS7fbdfa1060ed0f49e18-38.jpg)



Kdump



IMfomp条叫傭*瞻*机«, ft 概淋抑网.M , k

W予翼婊用户的,，



Wump (EJ? 为KrfunlF «曲的内S :



爆,6功(AI1    - •#«! (M)



Sir鄕的内押:，＜邮» s..    0



l-duwpffiS

'A t-4i^go(»i where to pu( |Fni (aiun.n ipfcc.fi/mcsr« W»»



Thu Hl« tcnlaint a



；# ken'ml ••.(■♦h Kai hepp».-ir j and tli* kdump ■i. thil fi!ft w» only 3pplfeabl«C0 th» fcdurnp initr^ni^ th« root nl»systyn*i *s nxiunc«rd and the rariTta* ini(



ptrfanri iin Mtlni) vdwn 9

'''ha« fc««n loaded Dirs';貌’ ，o «n»t( i( a prac»»s«d jH



；# Cuifwntly on!y cr<- nuirip tar<i»S ar«l p.ith mm be :onflaured '■« if !h» r'.i>fiqi,r8j aunip r«A{»'( Iwl* ih» dafairo #1 ti«> wnl fc* c the default action may1 ba

configured dufrio ta>*5«t it



iuiifj till. dno.lRi t»lcw. rfll箱



irtiiOre Wo 11 <J»vic9 nam#« f-x pi



'<rit»



Will moun' ft-sml <r.p> /pro<，Mnw5 ta



.na(H



图2.31设置Kdump

Kdump主要用来调试系统内核和相关软件，对用户和生产环境几乎没有任何帮助，启用

与否均无太大影响。设置完Kdump单击“前进”按钮，即可完成设置进入登录界面，如图2.32 所示。

麈期白 21：03    (j th *" O

user

图2.32登录窗口

在登录界面的右上角可以做一些辅助设置，例如语言设置、声音和开关机等。此时单击屏 幕中间的用户名后在弹出的窗口中输入密码，然后单击“登录”按钮，如果用户名、密码校验 通过则可顺利登录Linux系统。

(2)首次进入桌面环境，CentOS会弹出窗口要求用户进行一些使用习惯上的配置，如图 2.33所示。

欢迎

•------- ®a(N)-

!______:_____________________________

图2.33首次设置

从图中可以看到，系统会首先要求用户设置系统默认语言，接下来还会提示用户设置输入 源(即输入法)、云账号等内容，这些内容可按实际情况设置，此处不做赘述。

(3)如想切换到命令模式，可进入系统后在桌面单击右键选择“在终端中打开”，然后在

其中输入“init3”，即可完成运行级别的转变。Linux运行级别如表2.1所示。

表2.1 Linux运行级别

| 参数 | 说明                                                    |
| ---- | ------------------------------------------------------- |
| 0    | 停机                                                    |
| 1    | 单用户模式                                              |
| 2    | 多用户                                                  |
| 3    | 完全多用户模式，服务器一般运行在此级别                  |
| 4    | 一般不用，在一些特殊情况下使用                          |
| 5    | XII模式，一般发行版默认的运行级别，可以启动图形桌面系统 |
| 6    | 重新启动                                                |

2.3.2远程登录

远程登录是Linux系统中最常见的--种登录方式，多为运维工程师所用，远程登录可以使 用VNC图形界面、ssb等方法。其中以使用ssh登录为多，其原因是运维工程师管理和维护的 系统通常没有图形界面，且ssh使用的加密方案比较安全。本小节以ssh登录为例简要介绍如 何远程登录。

.(1)如果需要在虚拟机中使用远程登录，首先网络必须互通，如果虚拟机已使用了 Host-Only模式(仅主机模式)或桥接模式，则可以直接在宿主机登录。

本例中将采用仅主机模式演示登录过程，首先我们需要先查看Host-Only模式使用的IP 地址段。以Windows 7为例，在开始菜单中单击“控制面板”，然后在控制面板中找到并单击 “网络和Internet”下面的“查看网络状态和任务”。此时将进入“网络和共享中心”，在其界 面的左侧单击“更改适配器设置”，此时将进入“网络连接”界面。找至lj“VMware Network Adapter VMnet8”并右击，在弹出的菜单中选择“状态”，然后在状态对话框中单击“详细信息”，出 现如图2.34所示的界面。

图2.34网络连接的详细信息

如图2.34所示查看其IP地址，只有虚拟机Host-Only网卡的IP地址与此IP地址在同一

网段方可进行远程登录。

(2)在虚拟机中查看IP地址可以使用“ifconfig”命令，如图2.35所示。

文件(F)编辑(E)査看(V)搜索⑸终埔⑺帮助(H)

(usGT@localhost 車.面］$ ifconfig

HxtlP. Rf)ADCAST. RUNNING. Mill TTCAST、mtU 1500 linet 192:168.146.128! netmask 255.255.255.0 broadcast 192.168.146.255 inet6 fe80:: 20c: 29ff: feOb: 776 prefixlen 64 scopeid 0x20<link> ether 00:0c： 29: 0b： 07:76 txqueuelen 1000 (Ethernet)

RX packets 11251 bytes 13916342 (13.2 MiB)

RX errors 0 dropped 0 overruns 0 frame. 0 TX packets 2039 bytes 138205 (134.9 KiB)

TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

图2.35查看IP地址

可以看到网卡eno 16777736的IP地址与Windows中的IP地址属于同一网段，因此可以 使用远程连接。如果使用以上命令没有查看到此IP地址，就需要重启网络连接或对网络连接 进行配置。

(3)由于CentOS 7默认开启ssh,因此可直接通过PuTTY等工具连接，如图2.36所示。

图 2.36 PuTTY

在PuTTY中填入CentOS 7的IP地址，并选择“SSH”，单击“Open”，输入用户名和密 码即可远程登录到Linux系统中。
