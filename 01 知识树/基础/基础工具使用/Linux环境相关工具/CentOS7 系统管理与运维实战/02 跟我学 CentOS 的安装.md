囲我学CentOS的安装》

学习Linux首先要了解Linux的安装。安装Linux有多种方法，可以直接将Linux安装到某台 机器上，也可以采用虚拟机安装。本章首先介绍虚拟机的相关知识，演示如何在虚拟机上安装 Linux,然后介绍Linux的其他安装方式。通过本章，读者可以掌握Linux的系统安装过程。

本章主要涉及的知识点有：

•    认识虚拟机

•    如何安装Linux

•    安装后如何进行配置

•    旧版本如何升级

2^1    安装CentOS必须知道的基础知识

作为一个企业使用的专业操作系统，直接使用CentOS对初学者有较大难度。这是因为 CentOS中有许多非常专业的概念和软件结构，在正式开始之前，本节将介绍安装CentOS必 须知道的一些基础知识。

2.1.1磁盘分区

安装一个全新的CentOS如同安装全新的Windows 一样，都需要先对磁盘进行分区。对于个人 学习用户而言，推荐读者使用一个比较合理的静态分区方案。一方面静态分区方案不太复杂，另一 方面手动进行分区方案(而不是由安装程序自行分区)可以认识Linux系统中各目录的作用。

在Windows系统中，分区类型是一个已经被淡化的概念，但在Linux系统分区时，这些 概念依然存在。因此首先介绍一下分区类型：

(1)    主分区：主分区可以直接用来存放数据，但在一个硬盘上主分区最多只能有4个， 因此如果想在一个硬盘上创建4个以上分区，光主分区是不够的。

(2)    扩展分区：扩展分区也是一种主分区，但扩展分区不能用来存放数据，但可以在扩 展分区之上再划分可以存放数据的逻辑分区。

(3)    逻辑分区：逻辑分区是在扩展分区的基础上建立的，可以用来存放数据。

从上面的介绍中可以看出，如果需要划分4个以上分区，就必须使用扩展分区，然后在扩 展分区的基础上划分多个逻辑分区。

明白了分区类型的概念之后，安装CentOS时还需要制订一个分区方案。在制订分区方案 之前首先需要明确一个概念，在Windows系统中，不同的分区被使用C、D、E等盘符替代， 只要进入这些盘符就进入了相应的分区。但在Linux系统中没有盘符的概念，不同的分区被挂 在不同的目录下面，这个过程称为挂载，目录称为挂载点。只要进入挂载点目录就进入了相应 的分区，这样做的好处是用户可以按自己的需要为某个目录单独扩展空间。

制订分区方案首先需要了解自己的需求,生产环境中的系统与以学习为目的的分区方案肯 定不同。对于以学习为目的的初学者而言，一个最简单的分区方案应该包括以下内容：

（1）    /boot：创建一个约3OOMB-5OOMB的分区挂载到/boot下面，这个分区主要用来存 放系统引导时使用的文件，通常我们称为引导分区。

（2）    swap分区：这个分区没有挂载点，大小通常为内存的2倍。系统运行时，当物理内 存不足时，系统会将内存中不常用的数据存放到swap中，即swap此时被当作了虚拟内存。

（3）    根分区“/”：根分区的挂载点是“/”，这个目录是系统的起点，可以将剩余的空间 都分到这个分区中。此时该分区中包含了用户家目录、配置文件、数据文件等内容，初学者系 统中的这些数据都不会太多，因此推荐将他们都放在一起。

以上就是一个最简单的分区方案，初学者也可以尝试将再多几个分区，将其他目录也挂载 到分区中，例如分一个500MB的分区挂载到用户家目录/home下面。如果是生产环境就需要根 据具体业务来决定分区方案，生产环境分区方案一般奉行系统、软件与数据分开的原则。即操 作系统和应用软件放在本地硬盘上，数据单独存放于存储或单独的分区中，这种方案一方面分 类清晰，读写速度相对更快；另一方面即使存放系统和软件的硬盘损坏，数据也不会有所损失。

分区类型在安装操作系统时不会有具体体现，但在操作系统安装完成后使用fdisk等工具 添加新硬盘分区时会用到。

2.1.2静态分区的缺点及逻辑卷管理简介

对于普通用户而言，直接对硬盘分区然后挂载这种使用静态分区的方法几乎没有什么问 题。但对于某些特定的生产环境而言，这种方法弊大于利。例如要求不间断运行的数据库中心, 这类服务会随时间增加逐渐占用大量硬盘空间。如果使用静态分区方案，这类服务会在硬盘空 间耗尽后自动停止，即使运维工程师及早发现，也会在更换硬盘时停止服务。因此这类要求不 间断运行的服务，最好不要使用静态分区方案。

为了防止需要不间断运行服务因硬盘空间耗尽而停止，此时应该采用更加先进的逻辑卷管 理（Logical Volume Manager,逻辑卷管理常简写为LVM）方案。LVM先将硬盘分区转化为物 理卷（PV），然后将物理卷组成卷组（VG），然后在卷组的基础上再划分逻辑卷（LV），最后 就可以使用逻辑卷存放数据了=使用逻辑卷有以下优点：

(1)    可以解决硬盘空间不足，需要停止服务迁移数据的问题。虽然在划分逻辑卷时指定 了大小，但只要卷组中还有剩余空间，就可为逻辑卷扩容，扩容过程是在线进行的，这意味着 无须停止服务就可以进行。即使卷组中没有剩余空间，也可以向卷组添加新物理卷为卷组扩容。

(2)    当硬盘空间不足时，可以添加更大的硬盘，从而将卷组中那些容量较小的硬盘移出 卷组，这个过程也可以在线进行，无须关闭服务。

(3)    可以为逻辑卷添加快照卷，利用这一功能可以实现数据备份等操作，而无须担心数 据的一致性受到影响。

逻辑卷管理还有许多其他可能，例如减小逻辑卷空间等，此处不再一一介绍，感兴趣的读 者可以自行阅读相关文档了解。虽然逻辑卷有诸多好处，但依然建议初学者在安装系统时使用 静态分区，待系统安装好之后再学习逻辑卷操作。

2.1.3 虚拟化和 VMware Workstation 简介

虚拟化技术是指在一台计算机上同时运行多个逻辑计算机，这些逻辑计算机可以运行不同的 操作系统，拥有相互独立的CPU、内存等硬件，运行时互相不影响。虚拟化技术的好处是将CPU、 内存等硬件资源实现动态分配、灵活高度使用，从而提高资源的利用效率。如今虚拟化厂商和相 关的虚拟化软件有许多，但对于初学者普遍推荐使用VMware公司的Workstation。

VMware公司是最早从事虚拟化技术的公司之一，也是虚拟化技术的领导厂商，公司针对 不同的客户需求开发了许多虚拟化产品。例如针对个人桌面的Workstation,用于企业环境的 VMware vSphere 等。本书中多使用 VMware Workstation 作演示，VMware Workstation 运行时 界面如图2.1所示。

图 2.1 VMware Workstation 运行界面

Workstation可以虚拟许多操作系统，例如Windows 7、各种发行版的Linux和Unix、Solaris、 Novell NetWare等。为了读者能更好地使用Workstation,现将一些常见使用技巧列举如下：

(1)虚拟机的监视器：打开某个虚拟机的电源之后，Workstation会自动显示虚拟机监视

器。将鼠标移动到监视器内并单击，Workstation会自动将鼠标和键盘的控制权交给正在运行 的虚拟机。如果要让宿主计算机重新获得鼠标和键盘的控制权，可以使用Alt+Ctrl快捷键。

(2)    当虚拟机获得鼠标和键盘的控制权后，可以进行任何输入和控制，但使用组合键 Alt+Ctrl+Del将被宿主计算机获得，此时可以使用Alt+Ctrl+Insert替代或在菜单栏的虚拟机下 面单击发送Alt+Ctrl+Delo

(3)    Workstation为虚拟机提供了多种网络：如果要让虚拟机使用宿主机的网络可以使用 桥接模式(bridged);如果仅想让虚拟机连接网络可以选择NAT模式；如果只想让宿主机与虚 拟机通信可以使用仅主机模式(Host-Only);如果宿主机有多个网络，可以在虚拟网络编辑器 中设置。

(4)    Workstation附带有快照功能，使用快照功能将虚拟机保持在某一刻，使用一段时间 后返回作快照的时刻。

Workstation是一个功能十分强大的虚拟化软件，其使用方法和技巧有很多，此处不再一 一介绍，感兴趣的读者可以自行阅读相关文档了解。

VMware Workstation是一个收费软件，读者完全可以选择Oracle VM VirtualBox这类免费 软件来替代。

2.1.4下载最新的CentOS安装版本

目前最新的版本为CentOS 7,要安装首先需要从其官方网站上下载，其官方网站为 [http://www.centos.org/](http://www.centos.org/%e3%80%82%e5%8f%af%e4%bb%a5%e7%9b%b4%e6%8e%a5%e5%9c%a8%e6%b5%8f%e8%a7%88%e5%99%a8%e4%b8%ad%e8%be%93%e5%85%a5%e7%bd%91%e5%9d%80%e8%ae%bf%e9%97%ae%ef%bc%8c%e4%b9%9f%e5%8f%af%e4%bb%a5%e5%9c%a8%e6%90%9c%e7%b4%a2%e5%bc%95%e6%93%8e%e4%b8%ad%e8%be%93%e5%85%a5CentOS)[。可以直接在浏览器中输入网址访问，也可以在搜索引擎中输入CentOS](http://www.centos.org/%e3%80%82%e5%8f%af%e4%bb%a5%e7%9b%b4%e6%8e%a5%e5%9c%a8%e6%b5%8f%e8%a7%88%e5%99%a8%e4%b8%ad%e8%be%93%e5%85%a5%e7%bd%91%e5%9d%80%e8%ae%bf%e9%97%ae%ef%bc%8c%e4%b9%9f%e5%8f%af%e4%bb%a5%e5%9c%a8%e6%90%9c%e7%b4%a2%e5%bc%95%e6%93%8e%e4%b8%ad%e8%be%93%e5%85%a5CentOS), 然后在搜索结果中选择其官方网站访问，如图2.2所示。

C€ntOS

CentOS 7 is here

1 r»e: • • t*    t i-, plejsecl    ihe irnnjedijjte availability o» CentOS i for x66_64, tnukioing

'ot    ,占rxl    fner* are many fundamental    in    •:steaf今-somprjfSdTp

. ■:'    NoTS*)^ rht' inclusion ot sysUjmd, Gno(nr； 5r a    hk»Ayit«n)oi xTS. Fc r>wc

in'. 'tn    : ：?hnul u/h.if    C<?”tQS 7 SiefKj ouL    CtX

?/iih Lhk    W »vi!l be to foCi'J>Ofi getting yAe Coninjurf/V/.Builtononline, s'nd

fa’cn’T?乂四;few    several    :什⑶ bavafeiwn csnhold ckirio^d伸您｛ft怒印sfe

\- tiUth .r- < .W.f'Sf jf.k, 'Xc-M; Gli.aiei',    Gan rtow rtdfltorili7t.Gcnr.OS ( IhL，kii他'■做々科

Get CentOS 7 Now

图2.2 CentOS官方网站

在其官方网站中单击左上方的“GET CENTOS”或单击下方的“Get CentOS 7 Now”按钮， 在下载页面中选择安装镜像类型。此处选择“DVD ISO”标准安装镜像即可，接下来的页面 将选择从哪个镜像站点下载，国内通常可以使用网易、中国科技大学(网址开头为

http://centos.ustc.edu.cn）等速度不错的站点。

除了 DVD标准安装镜像之外，还有Everything版（完整版安装光盘）、Netlnstall版（网 络安装映像）等，这些安装映像都可以从 [http://mirrors.163.eom/centos/7.0.1406/isos/x86_64/](http://mirrors.163.eom/centos/7.0.1406/isos/x86_64/%e5%a4%84)[处](http://mirrors.163.eom/centos/7.0.1406/isos/x86_64/%e5%a4%84) 下载。

![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-6.jpg)



下载完成后是一个扩展名为iso的光盘映像文件，可以使用软碟通等软件将光盘映像文件 刻录为光盘使用，虚拟机也可以直接使用映像文件作为光盘使用。

安装 CentOS

完成之前的知识积累和软件下载后，就可以开始安装CentOS 了。读者可能会遇到不同的 环境，因此本节将模拟不同的环境，使用不同的方法安装CentOS。

2.2.1创建虚拟机

在虚拟中单击菜单栏中的“文件”，在弹出的菜单中选择“新建虚拟机”，之后将弹出新建 虚拟机向导，如图2.3所示。

图2.3新建虚拟机向导



首先需要选择采用什么类型新建虚拟机，如果使用自定义，向导将会要求用户选择虚拟机 的兼容版本，SCSI控制器类型等，此处选择“典型”，然后单击“下一步”。接下来向导会要 求用户选择安装来源，如图2.4所示。

通常不建议大家在此处选择安装光盘，此处建议选中“稍后安装操作系统”，并单击“下 一步”。接下来向导会提示用户选择操作系统类型，如图2.5所示。

图2.4选择安装来源



图2.5选择操作系统类型



此处选择“客户机操作系统"为“Linux”，然后在“版本”列表中选择“CentOS 64位” （截至本书编写时CentOS 7还没有发布32位版，因此此处选择64位版），然后单击“下一步”。 接下来向导会提示用户命名虚拟机，如图2.6所示。

在虚拟机名称中输入虚拟机名，然后单击位置后面的“浏览”按钮，选择虚拟机文件保存 的目录，最后单击“下一步”进入指定磁盘容量页面，如图2.7所示。

图2.6命名虚拟机



图2.7指定磁盘容量



如果仅需要安装基本版的CentOS 7,磁盘空间10GB已经足够使用了；如果要存放其他 文件则按文件大小调整硬盘空间大小。之后将选择虚拟磁盘存储为单个文件还是多个文件，这 是由存放虚拟机文件的分区类型决定的。如果文件类型为FAT32,必须选择存储为多个文件， 这是因为FAT32不支持4GB以上的单个文件。用户可以在对应的盘符上单击右键选择“属性”， 在弹出的常规页面中查看文件系统类型。选择好磁盘选项后，单击“下一步”就会弹出完成创 建页面，如图2.8所示。

|                       |              |              |      | 翩   |
| --------------------- | ------------ | ------------ | ---- | ---- |
| 已：（《好«蠹遢拟机   |              |              |      |      |
| 单击”完成"创蠭由｝«机 | 然后司以安装 | CantOS 64 位 |      |      |

»使用■FWSHmSK 机:

| 名称：       | CentOS 7                        |      |
| ------------ | ------------------------------- | ---- |
| 位S:         | E:\Virtual Machines\CentOS 7    |      |
| 版本：       | Workstation 10.0                | j    |
| 播作系统：   | CentOS 64 fe                    |      |
| 硬盘：       | 20 GB                           |      |
| 内存：       | 1024 MB                         |      |
| 网络适织器： | MAT                             | i    |
| 其他设蓊：   | OJ/DVO, USS控剌器，打印机，声卡 |      |

_

图2.8完成创建页面

在完成创建页面中，可以单击“自定义硬件”按钮弹出修改虚拟机的硬件设备页面。在自 定义硬件设备页面中，需要检查内存容量应该大于512MB,否则CentOS将无法启动安装程序; 如果要使用U盘、移动硬盘等设备，硬件中必须包含USB控制器；安装时需要使用光盘，因 此CD/DVD设备也必不可少。确认以上信息之后就可以单击“完成”按钮，完成虚拟机创建。

2.2.2光盘安装CentOS 7

Linux的安装方法有很多种，本小节主要以光盘安装为例介绍Linux的安装过程及相关的 参数设置。详细步骤如下：

步孅



打开创建的虚拟机，单击“编辑座拟机设置”，或在主窗体右侧库中找到新建的虚拟机 右键单击，并在弹出的菜单中选择“设置”，调出虚拟机设置窗口，如图2.9所示。

图2.9虚拟机设置窗口



在虚拟机设置窗口中单击硬件选项中的“CD/DVD (IDE)”，然后在右侧选择“使用物理 驱动器”(即使用宿主机的光驱)或“使用ISO映像文件”。读者可根据实际情况进行选择， 此例中选择“使用ISO映像文件”并单击浏览选择下载的光盘文件，完成后单击“确定”保 存设置即可。

通过以上步骤完成虚拟机的参数设置，下一步是启动虚拟机，单击“开启虚拟机” 选项或单击其中的绿色箭头即可启动虚拟机，如图2.10所示。

a殲我的培脑

徽BT    翁

趣 LFS6.3 趨 CentOS 織 OraclelOg 趣 CentOS?



&颖?

Lp Cen

*幵自过 hf’織藎



图2.10启动虚拟机

€03'启动后耐心等待安装程序引导完毕，即进入Linux的安装界面。Linux的安装和 Windows的安装类似，如图2.11所示。

Cmvtns： 7

Insh-i 11 CentOS ?

Test this nedia & install CentOS 7

TroJi h 1 esiioot iny

Automatic boot in 59 seconds…

图2.11 Linux引导选择画面

画面中给出 了三个选项，“Install CentOS 7”、“Test this media & install CentOS 7” 和 “Troubleshooting”。第一个选项表示直接安装CentOS7,第二个选项表示先测试光盘有无错误 然后再安装，第三个选项主要用来测试内存和启动救援模式修复已存在的CentOS。此示例中 选择第一项，直接安装CentOS 7，如果对安装光盘表示怀疑也可选择第二项。

选中第一项“Install CentOS 7”并按Enter键，等待数秒后会提示用户按Enter键启动安装 程序，此时可以按Enter键或等待数秒待系统自动启动安装程序。

^4 安装程序启动后，首先会提示用户在安装过程中使用的语言，如图2.12所示。

![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-15.jpg)



CentOS



CENTOS 7 INSTALLATION

頤u«

WELCOME TO CENTOS 7.

What language would you tike to use during the installation process?

| English           | £n<^ish    |
| ----------------- | ---------- |
| Afrikaans         | Afrikaans  |
|                   | AmtMrk     |
|                   | Arabic     |
|                   | Assamese   |
| Asturianu         | Asturian   |
| Ee/iapycKas       | Belarusian |
| BwirapcKM         | Bulgarian  |
|                   | Bengali    |
| ftfps    Ur srmft |            |



English' States)



J: English (United Kingdom)    ；

；'English (India)

\ English (Australia)

/；: English (Canada)

: English (Denmark)

+::p English (Ireland)

\ \ English (New Zealand) p English (Niger⑷

' English (Hong Kong SAR China) *;



Quit ； Continue

图2.12安装程序询问安装过程中使用的语言

此处可以保持默认，也可以在左侧选项中选择“中文”，右侧中选择“简体中文(中国)”。 需要注意的是此处选择的语言仅为安装过程中使用的语言，并不影响系统的最终语言。本例中 左侧选择“中文”，右侧选择“简体中文(中国)”，单击Continue完成设置。

接下来安装程序将显示安装信息摘要界面，如图2.13所示。

安装催雇摘嬰



![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-16.jpg)



CENTOS 7 安装

因cn

C€ntOS



本地化



曰期和时间(T)

亚洲/上海崛

进苗支持(L)

简体中文(中鼴}



软件



安装灘⑴

本地介质



软件选择(S)

截小安装



系统



SS



o3



网格和主机名＜N)

未连接

退邮)

•.細細w '俊微獅紳殘舰糊to.



紐眶脉細綱岍下-*‘



體:戀戮



图2.13安装信息摘要界面

安装程序将需要用户设置的信息分为三个部分：本地化、软件和系统，完成这些设置后方 可继续安装。

^)6



如图2.13所示，在本地化中系统已经按之前设置的语言预设了时区、键盘类型和语 言支持三个选项，如需要修改直接鼠标单击对应的图标即可。首先日期和时间中的 时区已经被设置为“亚洲/上海”，单击“日期和时间”弹出日期和时间设置窗口，

如图2.14所示。

| 曰期&时间   |              |      | CENTOS 7 安装 |
| ----------- | ------------ | ---- | ------------- |
|             |              |      |               |
| 堆K(R):亚洲 | *城市(q:上脣 | -q   | 网關測.闭炎   |

图2.14日期和时间设置窗口

在日期和时间设置窗口中，可以在地区和城市中选择需要使用的时区，也可以在下方地图 中单击对应的区域来设置时区。网络时间设置是用来设置是否让操作系统自动从时间服务器同 步时间，设置此项需要在系统设置中先设置网络。如果已经设置网络，可以拖动网络时间后面 的滑块至开启位置即可，而如需对时间服务器进行设置，可以单击滑块右侧的齿轮按钮添加或 删除时间服务器。

在窗口的下方可以设置当前日期和时间及时间显示的制式等，读者按需要进行设置即可， 完成设置后单击左上方的“完成”按钮返回安装信息摘要界面。

本地化中的键盘选项已被设置为“汉语”，而语言支持选项中也已自动选择添加简体中文 支持，通常都无须在做设置。

![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-20.jpg)



若选择英语作为系统的默认语言，也应该在语言支持中选择安装简体中文相关的软件包， 否则某些中文命名的文件可能无法正常显示。

安装信息摘要界面中的软件部分，主要是用来定制需要安装的软件包及软件包的来 源。其中安装源表示安装时软件包的来源，此时安装程序已自动将来源设置为本地 介质即光盘，无须修改。软件选择选项表示安装操作系统时需要一并安装的软件， 默认设置为最小安装即只安装系统最基本的组件，单击软件选择进入软件定制界

面，如图2.15所示。

软件选择



![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-21.jpg)



CENTOS 7 安萩



驀本环壤



e繼小安鎪 軀本功i



sats饞■髡H

用子播格s舞的《«a.

文停及打印■傳B

用子企*的文汽.rrui及存《臞舞a.

基本nns务b

癦供财态及••力态豆联网内番的塵《H.

慮«化主机

鳗小虚拟比主机，

播GUI的曲»B

带W用子嫌托网扭暮硪说施脚典GUI的》勞器，

GNOME A面

GNOME S —个衮篥廬题S用户友好的累SP?境.

KDE Ptasma Workspaces

KDE Ptacma Workspaces是一个邋連可C邐田形用戸 矜s.霣中e话s*橐s s«ffi标以a麋翯向写« «多功sa大的KDE s用程序，

开发及生成工作K

用子《w. Jilt. »形s«内容幵发的工作玷.



已选环遭的附加选顶

雇容性糧序庫

用子圧«囑企®曲Lmw的之《«事中拘BWS用程序幻

开沒I*

基車孖发坏廣，

■軀+支》♦

支待使用B能千»证，



图2.15软件定制界面

软件定制界面左侧是系统预定义的基本环境，按用途不同可以分为基础设施服务器、文件 及打印服务器、基本网页服务器和虚拟化主机等。右侧则为每个基本环境中附加软件选项，此 处可以根据自身需求选择基本环境，需要注意的是某些基本环境默认没有安装图形界面，对于 初学者此处可选择“GNOME桌面”。选择完成后单击左上角的“完成”按钮返回安装信息摘 要界面，返回后安装程序将重新计算软件依赖关系，此过程中软件部分的选项将显示为灰色。

步霽



系统部分要求用户设置硬盘分区方案和网络连接，此时安装程序已自动将硬盘分区 方案设置为自动分区，但要求用户确认，如图2.13所示。单击“安装位置”进入“安 装目标位置”选择界面，如图2.16所示。

| 安装目标位置                                               | 、    CENTOS 7 安弒              |
| ---------------------------------------------------------- | -------------------------------- |
| 设备遘揮逯择您想»安8的设番，在您点击“幵始安装"本壜番惠磁耋 | 按招之的.选择的设番并不会破襍作- |
| 10.24 G8                                                   |                                  |
| -CVMware, VMware Virtual Ssda t 10.24 GB 空闲              |                                  |
| KM的及nttMtt                                               | 此处禾选撵的磁曲獬不会镶操作,    |
| 添加碾番(A)...                                             |                                  |
| 篥它存備遘項分区O自功C置分区＜Uh    我褰M分区＜IL          | ttfi來連掃的f8番格不会9進作,     |

我想让《外空间5J用(M),

加窗

充S磁盘摘》以及引锌找序(FL.



已®» 1个磁fih料癱W24GB; 10Z4GB空闲

图2.16    “安装目标位置”界面

在安装目标位置界面中，首先需要用户确认安装的磁盘，此处已选择了一个本地磁盘sda， 容量为10.24GB,空闲空间为10.24GB。本地标准磁盘下面是存储添加区域，如果需要使用额 外的存储，可以在设置网络之后单击“添加硬盘”选项添加额外的存储。最后的“其他存储选 项”中，可以选择手动分区和系统是否加密（通常不选择加密选项）。选择“我要配置分区”, 然后单击左上角的“完成”按钮进入分区界面，如图2.17所示。

手幼分区

，斷CentOS 7安襞

«迁没有为CentOS，的安利创建任词技ffi可

:tt：

:-妞盟鱼楚堡遽上.

•遇过点击*****按扭剖礁顧珪 •或奮向下方速鐸的巳列分区分e析栓n点.

CENTOS 7 安装



在您为CentOS 7安装创钂技W讲后.怒可在这鬣浏览它们的谇纗《



全部里设



BJ用空1»1

10.23 GB

S5»l

10.24 GB



图2.17手动分区界面

在"手动分区”界面中可以看到，这是一个新的CentOS 7安装，分区方案可以有多种选 择，本例中将选择标准分区。然后单击下方的“+”添加分区，此时将弹出添加分区窗口，如 图2.18所示。

添加新挂n点

在下方创建挂載点后 有更多自定义选项可用.

«載点（P）:「

期B番量（⑺：

取，削 G 3iSSif«）a（A）

图2.18分区窗口

添加分区窗口主要有两项需要用户设置，第一项为挂载点即系统目录，第二项为期望容量， 此处填入分区大小，默认单位为MB,但也可以使用如2GB、100MB等形式。本例将使用最 基本的分区方案，依次添加挂载点为“/boot”的引导分区，空间大小为500MB,挂载点为“swap” 的交换分区，大小为2GB，挂载点为根分区的“/”，期望容量不填（不填将默认使用剩余所有 空间）。分区完成后将显示分区方案，如图2.19所示

| 手幼分区 | CENTOS |
| -------- | ------ |
| 猶商     |        |

卜:



M■番麄＜0):    7 739GB

设曇供am:殍詹分区

加細



文停系鏞作)xfs

its!    •上的"开助―檫祖之K” .

雄在車霱華内所做的更改不会9S用.

!L+_

969.23 kD I 10 24 GO

已蠹g i ff a设bis>

图2.19分区方案



在分区方案的右侧，还可以对分区进行一些调整，此处最主要的调整是调整文件系统类型。 在本例中保持默认，单击左上角的“完成”按钮，将提示是否需要将分区方案保存到硬盘，单 击“接受更改”即可保存并返回安装信息摘要界面。

CS9 完成分区方案设置之后，接下来需要设置网络和主机名，单击“网络和主机名”弹 出设置窗口，如图2.20所示。

| 网铒和主机€ | CENTQS 7 安« |
| ----------- | ------------ |
|             |              |

d etnernst (eno 16?7?7365

!nr*i C«w^M«|'(< ■PiiWi (WO    l»ort A^«ai«« .；

Ethernet (enol6777736) 已《歼3播

键件堆祉 O&OC.2958 58 i$a iooor*iw«

子网 niw 127.0.0,1

CW;

图2.20网络和主机名设置

从界面左侧可以看到，安装程序发现了一张网卡命名为enol6777736,并且网卡默认处于 关闭状态。在界面左下侧安装程序已经设置了一个主机名，用户可以在此处输入新的主机名。

拖动界面右侧的滑块将网卡启用，如果使用的网络还需要设置IP地址等信息，可以单击 “配置”按钮，在弹出的窗口中选中“IPv4设置”，如图2.21所示。

图2.21网卡设置



可以看到系统默认使用DHCP的方式来获得IP地址等信息，如需设置IP地址可以在方法 中选择“手动”，然后在地址中添加相应的IP地址、子网掩码和网关。DNS服务器地址应该 填写在“附加DNS服务器”选项中，如果有多个DNS地址则使用逗号作分隔。如果虚拟机 的网络设置为Host-Only和NAT,此处应该设置为通过DHCP方式获得IP地址等信息。

完成上述设置后，单击“保存”按钮返回网络和主机名设置界面，再单击左上角的“完成” 按钮返回安装信息摘要界面。

gTTio 完成前面几步的设置之后，请再次确认每一项设置是否合适，特别是磁盘分区，因 为到此时为止安装程序还没有修改磁盘中的数据。确认没有问题之后，单击右下角 的“开始安装”，安装程序会使用之前的设置开始系统的安装工作，如图2.22所示。

配靈



![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-26.jpg)



CENTOS 7 安装

困《



C€ntOS



用户设置



ROOTftB

Root密码未设邇



«醒用户（u>

不会铟鏞任钶用户



惠动軟件E安解进W

?ntO5 Core 5IG

iduccsthe CentOS Linux OistnbuKon.

■    i…    '

![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-27.jpg)



必價先JW灌戰此囲样杨记的内8W8行T-伊，



图2.22开始安装

从图2.22中可以看到安装程序已经开始安装工作，但还需要对用户进行设置。其中“ROOT 密码”选项将设置root用户的密码，root用户通常也称为根用户，在Linux系统中拥有至高无

上的权限（相当于Windows系统中的Administrator用户），因此如果在生产环境中，应当设置 一个强度较高的密码。

由于root用户的权限太大，若用户登录并使用根用户误操作可能会带来一些不必要的麻 烦，例如输错一个字母删除系统中重要的数据等。因此系统强制要求创建一个普通用户，并使 用普通用户登录系统，必要时向root用户“申请”权利。

单击“创建用户”选项即可弹出创建用户窗口，填入全名、用户名、密码之后，单击左上 角的“完成”按钮即可返回安装界面。

如果在生产环境中设置密码，通常需要注意两点，其一是密码必须要有一定的长度，通常 建议设置为8~16位。其二是密码要具备一定的复杂性，复杂性通常用"四分之三原则”来 衡量，即密码要包含构成密码的4种字符（大写字母、小写字母、数字和字符）中的3种。

ggTil 设置完密码之后，等待安装程序安装完成（视计算机配置不同，此步可能需要20-40 分钟），安装结束时如图2.23所示。

配量    CENTOS 7安装

![img](11 CentOS7系统管理与运维实战_files/11 CentOS7fbdfa1060ed0f49e18-28.jpg)



C€ntOS



因cn

用户边霣

ROOTS碑    9    倒廳枏户00

设奮-imi鼉&    H    格ft（電用户嫩

充_

CentOS 功安强利您的竹障机并珂以«用！ ■麻然偽使用吧！

篇躺

a. an* 产

图2.23安装完成

此时只需要单击.“重启”按钮，系统就会将最后的修改写入硬盘并重新启动系统，至此 CentOS 7就已经安装完成了。

2.2.3 U 盘安装 CentOS 7

由于光盘使用不是非常方便，目前在计算机中安装操作系统多使用U盘，CentOS 7也可 以使用U盘安装。本小节将简单介绍如何使用U盘安装CentOS 7。

首先需要下载一个名为USBWriter的软件，将U盘插入计算机的USB接口，确保系统能 正常识别U盘，且U盘足够大（建议容量8GB）。然后打开USBWriter,如图2.24所示。

图 2.24 USBWriter 软件



单击Source后面的“Browse”按钮，在弹出的对话框中选择CentOS 7的光盘映像文件， 然后在Target后面选择要使用的U盘。由于写入光盘映像会清空U盘中的所有数据，此步骤 请慎重。确定使用的U盘中没有任何有用数据之后，单击“Write”按钮开始写入映像文件。

待USBWriter写入完成，将U盘插入需要安装CentOS 7的计算机上，然后使用U盘启动 安装。之后的安装步骤与2.2.1中介绍的相同，此处不再赘述。

![img](11 CentOS7fbdfa1060ed0f49e18-30.jpg)



并不是所有的U盘都可以写入光盘映像，某些U盘制作启动盘时，可能会失败。

2.2.4 Windows 7+CentOS 7 双系统安装

由于虚拟机安装CentOS 7时会占用大量内存，对计算机要求相对较高，低配置的计算机 运行可能会不太流畅，因此许多人将Windows和CentOS都安装到同一个计算机中。本小节将 以Windows 7为例，介绍如何在一台计算上同时安装Windows和Linux,此处介绍的方法也适 合其他版本的Windows和Linux。

(1)安装双系统应该先安装Windows,因为Windows的引导装载程序无法引导Linux系 统，但Linux系统的引导程序Grub则可以引导Windows。因此应该先安装Windows,然后再 安装Linux,在Linux的引导程序中添加Windows引导选项。

如果计算机中还没有安装Windows 7,即硬盘中还没有分区，在Windows 7安装时需要为 CentOS 7预留足够的空间。这些空间可以不用分区，以空闲空间的形式存在即可，如图2.25所示。

图2.25安装Windows 7时预留空间



从图中可以看到磁盘0的容量约为80GB,其中分区1是由Windows 7安装程序自动分配 的系统保留空间，容量为48.7GB的分区2作为Windows 7的系统分区，即C盘。分区3是分 配给Windows 7使用的第二个分区，大小为19.5GB,而最后还有一个未分配空间，容量为 11.6GB,这个未分配空间就是留给CentOS 7的空间。

安装Windows 7时，也可以只添加系统盘，待系统安装完成后，再使用磁盘管理器进行 分区并预留空间。由于需要将Windows 7安装在分区2中，因此只需选中分区2并单击“下 一步”继续安装即可。

![img](11 CentOS7fbdfa1060ed0f49e18-32.jpg)



由于本书的重点并非Windows的安装，故此略过Windows安装一节，Windows 7和其他 版本Windows的安装方法，读者可自行阅读相关文档或搜索相关视频了解。

(2)如果计算机中已安装了 Windows 7且磁盘中已没有未分配的空闲空间，则可以使用 魔术分区大师等软件重新调整分区，将分区中的未使用空间调整为未分配空间。如果磁盘中还 有未使用的空间，则可在桌面上的“计算机”图标上单击右键，在弹出的菜单中选择“管理”, 然后在弹出计算机管理界面左侧依次选择“存储”和“磁盘管理”，如图2.26所示。

图2.26计算机管理



如果系统中还有多余没有分配的空闲空间，此时可以在未分配空间上单击右键，添加分区。 最后剩余一部分空间作为CentOS 7的预留空间。如图2.26所示，己经为CentOS 7预留了一 个10.66GB的未分配空间。

(3)完成上述步骤后，重新启动系统，修改BIOS设置并使用安装光盘或U盘启动系统, 然后安装CentOS 7,安装过程与2.2.1节中介绍的相同，此处不再赘述。安装完成后登录系统, 在桌面上单击右键，选择“在终端中打开”，此时将弹出终端窗口。接下来相修改CentOS 7 引导装载程序的配置文件，并添加Windows 7的引导选项。'

在终端窗口中输入命令“su-root”，然后输入root用户密码切换到root用卢，需要注 意的是输入密码时终端内不会有任何显示，输入完成后按Enter键即可。切换成root用户 后，命令提示符将变为 “[root@localhost 〜]#”，输入命令 “gedit /boot/grub2/grub.cfg” 按 Enter键，此命令表示使用gedit编辑器打开Grub的配置文件/boot/grub2/grub.cfg。待gedit 打开后，依次单击菜单中的“搜索”和“跳转到行”，在弹出的对话框中输入70,此时光 标将跳转到第70行。

如图2.27所示，在原有的菜单条目“menuentry”之后加上Windows 7的引导选项。引导 项中“Windows 7”表示启动时，Grub显示的菜单名称，“set root=（hdO，l）”表示Windows引 导的设备为第0块磁盘的第1个分区（此处需要按实际情况设置），而其后的“chainloader+1” 表示加载Windows的引导程序。

g«dit



grub.cfg (/boot/grub2)

文件（F）



査看(V)搜索(S)工具(T)文栏(D)

；'Ll grub.tfg

:terminaX_output console !if【feature_timeout_style = xy ] ; then set timeout_style=menu

set timeout=5

:# Fallback normal, timeout code in case ths timeout—style feature is :# unevollable.

•elsa

set timeout=5

END /d/flrub.d^SJieader menuentry 'Windows 1' { set root=(hdO,l) chainloader +1

\#ff BElilM /ofc/qnjb.d/'lO 'linux ###

•menuentry 'CentOS Linux, with Linux 3.16.0-123.917.x86_64* --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option -'gnulinux-3.1Q.G-i23.aX7.x86_64-advanced-166fe31f-3c35-4520-b604-95blsed686ae• { load_vldeo

set gfkpaylosd=keep

insmod gzio lnsmod part_msdos insmod xfs ~

i-----___；______________ ______________翼

统文本*    制表符贳度：8 *    行TO•列1 播入



图 2.27 使用 gedit 编辑 grub.cfg

完成以上操作后，保存退出并重启系统，待系统重启后可以发现CentOS 7的引导菜单中 多了 Windows 7的引导选项，如图2.28所示。

UindoHK 7

Efjittlts I.i n<iX. Ml! h I. i nux B-rescue ZBM'ibbH^USn'/^HyVah'MJSlcttOreliSlo

Use « I»h T and i hoys to change the sol»v.t ion.

Press a" to <«111 «hr seloct«il i tew. or' 'c' for rt cn«Mdnd pruMpt Th»f sol«ct«rt ont ry Mill be stflrled «utnH<«t ic« I (y in 5 s,.

图2.28 CentOS 7引导菜单

从图2.28中可以看出，引导菜单中已有Windows 7选项，此时只需要使用向上方向键选 中该选项并按Enter键即可引导进入Windows 7。

2.2.5网络安装

网络安装分为两种模式，一种是使用Netlnstall版（网络安装版），使用这个版本安装需要 通过网络下载安装所需的软件包；另一种方式是使用PXE网络启动的方式安装，这种方式适 用于大规模自动化安装，例如一次性安装100台计算机，井且这些计算机的分区方案都相同。 由于PXE网络启动安装方式比较复杂且实用性不太多，此处不作介绍，读者可自行阅读相关 文档了解，本小节将介绍如何使用网络安装版的使用方法。

网络安装通常适用于不能使用DVD和U盘的环境，读者仍可以使用虚拟机模拟安装。首 先需要下载Netlnstall版的光盘映像文件，接下来需要让虚拟机能正常访问网络，这时网络连 接方式一般选择桥接或NAT,视宿主机网络环境而定。接下来使用Netlnstall版光盘映像启动 虚拟机，之后的操作与2.2.1中介绍的相同，直到进入安装信息摘要界面。

在Netlnstall版的安装信息摘要界面中，会要求用户设置安装源。首先需要设置网络，可 以单击“网络和主机名”进行设置，请参考2.2.1节中的介绍，此处不再赘述。完成网络设置 后，单击“安装源”进行设置，如图2.29所示。

Mtp:// w mirrors 163 «om/Mntos/7/o47*a6.64/    代

| 麵外《件愈靡 | » URL摘朐一个银像列《＜^1） |                |
| ------------ | --------------------------- | -------------- |
| ‘已硒网名摘  |                             | 咨觯iw’n    \| |
|              |                             | * !'■用庐SiMh  |
| 卜'          |                             | 窗             |

图2.29安装源设置



![img](11 CentOS7fbdfa1060ed0f49e18-35.jpg)



| 安装源 | CENTOS 7 安装 |
| ------ | ------------- |
| '韻bi  |               |

您觀使m馨个安装S7

«在W格上（NJ:

如图2.29所示在安装源中输入地址即可，此处使用的是网易的安装源，读者也可以从 CentOS官网上查询并使用其他安装源。设置完安装源之后，其他设置和安装方法与2.2.1小节 中介绍的相同，此处不再赘述。

.鹼



也可以通过解压DVD安装光盘映像的方式自建安装源，但由于光盘文件名长度限制，光 盘中解压出的目录repodata和Packages中有许多文件名有误，需要修正才能使用。

![img](11 CentOS7fbdfa1060ed0f49e18-36.jpg)



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

2.4 从 CentOS 6.5 升级到 CentOS 7

对于一些已安装CentOS旧版本的计算机来说，通常更希望能从旧版本直接升级到CentOS 7,因为这样能够避免数据迁移的问题。由于CentOS 7与之前的版本差异较大(事实上每一个 新版本与老版本差异都较大)，不一定能百分之百成功。本节将简要介绍如何从CentOS 6.5升

级到 CentOS 7»

2.4.1升级风险

在个人使用的计算机或虚拟机中升级CentOS即使失败，可能也未必会有多大损失，但拥 有宝贵数据的生产环境则不同。此处仅讨论生产环境中升级可能带来的影响，其主要可归结为 以下几点：

(1)    系统升级后，系统中的软件也会同时升级。这些软件可能会因为兼容性等原因与之 前版本的数据、配置文件等产生冲突，导致不可用或部分功能丧失。

(2)    由于CentOS 7属于较新的系统，其稳定性、性能尚不稳定，可能会危及业务系统的 可用性。

(3)    新系统使用时间尚短，可能会有许多没有被发现的Bug,这也是为何运维工程师通 常会采用较低版本的原因。

从之前红帽子的更新习惯来看，新版本通常有非常巨大的改动，也必然存在较多Bug且 系统也尚未得到时间的考验，因此生产环境更新可稍作等待。待更新两三次，系统中大部分 Bug被发现、修复，且性能趋于稳定时再更新。

2.4.2使用升级工具

与之前的版本不同，新版本的CentOS提供了一个升级工具preupg，用户可以使用此工具 将CentOS 6.5升级到7。但这个工具并没有得到非常严格的测试，因此生产环境中不建议使用 本小节中介绍的升级工具。

(1)由于升级工具并没有包含在CentOS 6.5的软件源中，因此需要添加新的软件源才能 使用。使用vim在目录/etc/yum.repos.d中建立一个名为upgrade.repo的文件，内容如下：

[root@localhost -]# cat /etc/yum.repos.d/upgrade.repo

[upgrade]

name^upgrade

baseurl^<http://dev.centos.org/centos/6/upg/x86_64/>

enable=l

gpgcheck=O

完成上述设置后，请确保计算机能正常连接网络，然后执行以下命令安装更新工具：

yum -y install preupgrade-assistant-contents redhat-upgrade-tool preupgrade-assistant

上面这条命令将安装包括测试工具在内的升级工具。

(2)在正式开始升级之前，建议使用测试工具进行测试：

[root@localhost 〜preupg

Preupg tool doesn't do the actual upgrade.

Please ensure you have backed up your system and/or data in the event of a failed

upgrade

that would require a full re-install of the system from installation media. Do you want to continue? y/n #此处需要输入 y 并按 Enter•键

Gathering logs used by All installed packages All changed files Changed config files All us台rs    :



preupgrade assistant:

:01/11 “.finished (time 00:01s) :02/11 ."finished (time 15:29s)

:03/11 ...finished (time 00:00s) 04/11 ...finished (time 00:00s)

All groups Service statuses



05/11 ...finished (time 00:00s)

:06/11 •，.finished (time 00:01s)

需要注意的是，preupg工具仅作一些常规测试，其结果仅能做参考。

(3)接下来就可以导入CentOS 7的key并开始更新了:

[root@localhost # rpm. --import

http: //centos-^.excellmedia.net/7.0.1406/os/x86_64/RPM-GPG-KEY-CentOS-7 #此命令用 于导入CentOS 7的key

[root@localhost -]# redhat-upgrade-tool~cli 一-force --network 7 instrepo=[http://mirror.centos.org/centos/7/os/x86](http://mirror.centos.org/centos/7/os/x86%e4%b8%8064)[一64](http://mirror.centos.org/centos/7/os/x86%e4%b8%8064)

setting up repos...

.treeinfo

1.1 kB



00:00



4.7 MB 01:38 J 32 MB 10.28



getting boot images...

vmlinuz-redhat-upgrade-tool

initramfs-redhat-upgrade-tool.img

49 kB 81 kB



00:00

00:0.0



(1467/1468): zlib-devel-1.2.7-13.el7.x86_64.rpm J (1468/1468): zziplib-0.13.62-5.el7,x86_64.rpm { rpm transaction 100%

rpm install 100%

setting up repos...

base extras updates



No upgrade available for the following repos: getting boot images... setting up update... testing upgrade transaction

setting up system for upgrade    '

Finished. Reboot to start upgrade.

先使用rpm导入Key,然后使用redhat-upgrade-tool-cli工具下载更新中需要使用的软件 包。从以上执行结果可以看出共计下载了 1468个软件包(视系统中安装的软件不同，需要的

软件包也不同)，由于此步需要从国外服务器中下载软件包，因此可能需要花费大量时间.

(4)完成上述步骤之后，重新启动系统时系统就会自动更新至CentOS 7,如图2.37所示。

li)»ir<vh-I IS?.! I '-.del 1S7；I I ltV，i|r..,,ft W!l irartel 15731 ipgradrll!.?：!! ipyi'adttl 15731 ipyiMdel 1* iV.il up«vfl4etJ5?；ll ipgrdde I I'.h' U i.-a.l.-! V./ H

Ii((yrn4et 15731 Wrttdel 15731 ipyrartel 1S?31 a4ell573l n<tr;tlft?；U (idel 15731 15711

IS? II 1573 J ujjyradfU 15?:" 聊嫩k4i5?'"+

■I1' ' ' I »pgra4ellljV33 ipyradel 1*>?3J •pyrortel 1573 J 咖f15731

Iil^rrtdert5?：n ipyiMdell57；n xpqrddd 15731 »(<tjr.«lrl 15731 1；,,' I! ii|itir<n,(cl IS? ! J ipyrudel 157:11 tpi(rndpll*jZ3J •pyrartel IS7：J1 .|)flra(1«US?3! ip(ir..df：f IS?3 I ，.山 i r.'/ i i 11,5731 ipijpixlfil 15731 '.,(1(； ( 1S71I m 山? r 15V3J

*pt(ra,|pL15Zi) -adeUSZn •«a«l 15731



15/14WH tbXJ SDStnllhwi fontpacfcagcs-f f !esi*stem-l 14 B.el?... 16/1468) Ihy.i inst«|ling Iiherotiw»t fo»iIs-cinanon J.0?.Z 14.b17.. (Vzt'lbfll (6x) instolling cfintrol-c.Knter -l ile«yst»tw-：|.n-6-15.Bl?.. (6zl instfl11 iim xkeybuarrt-uoitf lg 2.4.t;I?...

Uix, histfllliny de,jrtwu font?, comsdh ?.. .13-6 ,e 17 -..

110,-14601 Uiz) lost-rtl 111,(j    r e I «;(iS(i-7 H. 1el?, centos Z . 3,.

I IP 14601 OiZ) Hicbil I iiiij si；l,up Z.H.?i *1 el?..

Metroin()■ zetcz|(r<»up cr««t«()tis zetc/^roup rpnrnew

^etc/tfshartow er’fiaVcd a：s /etc/gsiiartwu. rjinmew

/etc^i>ass;ui«i fi'Brtted «s zetc>pi«5ii'wil. fpmnew

/fiW-xjshrtdnw ci'R4tcd «s zcti./slwitlnu. r|«miew

/fctczslicl In created as /Klc/sliel I,.. rptmiew al?..



Mtruitiy w«rni«sf un riling warvtlwj llZzl蜘 1 (fix) insUHing



(13，M63J (fix) ImsbUllny IwiRieByKteM-ltl.0 ?,eIV.centos tl4zl4C'Bl t6zi <nst«l 1 tng i»,ilcaj,-Z. 1.41-2.el?-

!IS.-1-168) (6'z) inst^Hing 1 ihrepyrt-f ilesy'BtBM-Z. 1.11- 1.0.e!?.t；e"t«s..,

[lfcft伽 1 (fex) instftlliny IsfdaU-Z0t4b -l .el?..

117/1468} ((»/> litstrtllitwj (rtn>plc»u d«U -0.4,b-3,el?.,

rifizMfeffll (6zi installing f(»onwtic-dh-riles!<stew 1,0-4«.28l38911.el7...

119/HfaltJ (bxl histi，11 in(/ antixorr-KJi-l• 1.1 Z 3.el?.

HjX)    suzil l(vf ilftsysvUMU-ll-el?--.

L2VM6SI «3X» instdllinn «t«Ittdirr f；n-4-.1.4.2-3.e)7... r22/K»»I (7h) nisUlliuy »l?n dt> l .&.4 <J eJ7... f?.3zl-16Sl (，M installhwj tzdflU ->ua-2B14b 1 el?. •,

[Z4/14601 CM install it>g Mnd-litowe    -.

123/14681 (?z) instillt Intj lelepdfclKj-fiIit&ystttM-8.8,E-6.el?.i [2fa/ t1t(B) C?X) 4»islrtH ing kri«eros-f«nt,si i：tt»tt»}>)5-0*1? ,r17 ,,.

I2?zHb(M (7k) Install tn(/ thai-stcAlnblc?-fOnt.s-c«wmiQn-H.5.8 7.».tl7...

F20zM»ifll (7x, Inslnllbwj sh«:-fOnts-c(wwm)»> h 8 7.t;17.

I2fl/Mb81 (7z) inslal li»«j centos-booknMi,'ks- ? lei?,..

<?z>    l iiig 1 iJ»Xtl -cown»u-1.6，B Z. l-isl?.,.

131/1,4(il) 1 f?z) instd 11 in,j kerne 1-headers-3.18.B-123.e 17..,

L32'’14WU A?Z> iustaJlinff yeli；t-xSl -3.fl. I 2.el?..

l -CI/HfitH (?X) lnstdlliiHf li»ftix firn«u»re 20M8ZI3 M.3.git4164eZ3.el?...

(7z) instill) int) J lhwiM：o«i-il«ta-8.0-3,«l?...

135/.11MI] IZz. > Installing vim f i Jest/stenn 7,4.168 1 ,el7... t36z J40lt| (?z, ItisVil 11ny orftwil-n-nursew' tliKmc 3.0 l-is.el?,,,

(3?zl46ai CM hlsUllloq khd-misi: 1.15.5 10.el?...

I38/M6H I <7z) htstfll 11 tig mobi lc-hr(M.lbrtnd prnMldBr Info t .Z812B6H-1 el?.-r39/146F) I (7z)    kihs,is： extrascl?.,.

1側/1 秘ttJ <7x) h»strtHii»a ij，wtrt -i»l£-4.«lM ,e|7 ..

l-ll/Mfttt 1 (?x) insirt 11J fig ncurses-base 5-9 -13 .；^0130S11 .el?,.,

112/MftflI t7zJ iiist.’U‘叫 ytihc-cowmoli-?..l?-55.el?.,,

zusrAsbinzbuild locale «rc»)(Me： Iibr..so.6： 'version ’GLIB€_Z.:H’ not fomttl (rcquirwJ fey zusrzsslf(vtzb«tId h»v<sle archi) zwsl'/sbiuzbiiil(l loc«l> nmliiw；: /ItbfoVllbr.so.b' uKrslmi '6E.IBC 2.14' noi foiwtd (i-Ki(uirB<( by /ttsP/sbiM^buJl4-loc«le-aiPGhSvB) t始,W68J (0-z> lnctflHi»u uss -s(»f lokn freeb 1 -3.15.4 2.c 1?.

I44/14MH (Hz) Instil 11 iny glibt 21?-SS,f;17..

图2.37升级到CentOS 7

从图中可以看到，系统已经开始更新系统中的软件，视计算机配置不同这个过程将持续 10〜20分钟。在升级过程中可能还会出现一些错误及部分软件升级失败的情况，通常系统会自 动处理这些情况，以保证升级过程顺利完成。

2e5小白安装过程中的问题

本节主要介绍初学者在安装Linux过程中的一些常见问题，如Windows系统是否能够和 Linux系统并存，如何安装多个Linux发行版等。    ,

2.5.1    Linux分区会覆盖原有Windows系统吗

如在已安装Window系统的计算机中安装Linux和Windows双系统引导，安装完Windows 后，需要在硬盘上预留一定的空间安装Linux,安装过程中Linux的安装程序会检测已有的 Windows分区，安装后的Linux和Windows分别使用了硬盘中不同分区，安装的Linux系统 并不会覆盖已安装的Windows系统。

2.5.2如何安装多个Linux发行版

(1)    采用虚拟机安装的方式可以安装多个Linux系统。可以创建多个虚拟机，虚拟机创 建完毕后采用2.2.2节介绍的安装方法安装Linux系统。

(2)    在同一 PC上安装多个Linux操作系统，首先要为安装的每个Linux系统预留硬盘 空间，并且在每个Linux的安装过程中采用手动分区模式做好分区设置。如图2.38所示，单 击“创建自定义布局”，然后依次创建Linux所需的根分区和Swap分区，如有需要，可以创 建其他挂载点，分区设置如图2.39所示，图中剩余的空间可以预留给其他要安装的操作系统， 分区创建完毕即可进行Linux的安装。

图2.38 Linux分区选择方式

| 设奋        | 大小拯載点/ -uanj Mr-4-<MB) RAIDfl*    .格式 |         |
| ----------- | -------------------------------------------- | ------- |
| ▽硬盘驱动榭 |                                              |         |
| ▽ sda       |                                              |         |
| sdal        | 4000 I                                       | ext4 ■/ |
| sda2        | 1000                                         | swap Y  |
| 空闲        | 15479                                        |         |

图2.39 Linux分区参考值

2.5.3如何删除双系统中的Linux

如果系统中只安装有Linux，可以采用分区管理工具(如PartitionMagic)把Linux的分区 全部删除=也可以利用Windows的引导光盘进入纯DOS或Win PE模式，然后进入命令提示 窗口，执行“fdisk/mbr”清除分区信息。

如果计算机中Windows和Linux并存，可直接进入Windows系统操作，删除步骤如下:

步鼷



Lot



歩骤



在桌面上右击【我的电脑】图标，在弹出的菜单中选择“管理”命令，进入计算机 管理界面。

单击【磁盘管理】菜单，如图2.40所示，如有Linux分区，选择分区后删除即可。



| 曳文件⑧iwt（A）査看®口如帮助on;-了商8窗SWW |                |
| ------------------------------------------ | -------------- |
| 十篡机官s体地）                            | 卷    r        |
| e弧系妖工具                                | s» «:)         |
| 亊件眘卷器                                 | ㉔a)           |
| «    共莩文件夹                            | ^CD175A1 0 )   |
| !±! ®本地用尸和组                          | ODOVHIOAD CD ) |
| ® @性能日志和警报                          | ^OFFICE 12 «:) |
| 恚设备首理器                               | @cod* 2d (F:)  |
| B自存                                      |                |
| 磁盘碎片整理程序 孬艦管理                  | so£t_2* &:)    |
| a    服务和应用程序                        |                |

图2.40 Windows磁盘管理

如果是使用虚拟机安装的Linux,删除步骤如下：

^1;启动VMware,单击安装的虚拟机。

单击菜单【VM】-所示。

【Manager】一＞【Delete from Disk】进行虚拟机的删除，如图2.41



![img](11 CentOS7fbdfa1060ed0f49e18-42.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-43.jpg)



图2.41 使用VM菜单

以上两步完成后虚拟机中安装的Linux即删除完毕。

2.5.4主机连不上虚拟机安装的Linux系统

使用虚拟机安装完Linux系统后，如果采用SecureCRT不能登录安装的Linux,可以从以 下几个方面排查。

(1)检查安装的Linux服务器sshd服务是否启动，如下所示：

\1.    #检査sshd服务是否启动    ，

[root@CentOS # ps ~efIgrep sshd

root 1147    1    0 10:58 ?    00:00:00 /usr/sbin/sshd

\2.    #检查端口是否正常

[root@CentOS H# telnet 192.168.19.101 22

Trying 192.168.19.101...

Connected to 192.168.19.101. Escape character is 'A]*. SSH-2.0-OpenSSH_5.3

test

Protocol mismatch.

Connection closed by foreign host

\3.    #清除防火墙

(rootdCentOS 〜]# iptables -F

(2)选择合适的虚拟机网络连接方式

经过上面代码中的3步，确认服务器sshd服务正常，服务器防火墙没有开启，如果还是 不能连接，可检查虚拟机网络配置，单击桌面上的虚拟机图标，单击【VM】一【Setting】菜 单，结果如图2.42所示。

Hardware | options |

| Device               | Surwnary    ‘1:                   |
| -------------------- | --------------------------------- |
| HI Memory            | 512 MB                            |
| O Processors         | 1                                 |
| iSHard Di汝(SCSI)    | 20 GB                             |
| 3 Hard Disk (IDE)    | 10®                               |
| ^^CD/DVD (SCSI)      | Us>ng file G: VQQDownload^entO... |
| i4f>CD/DVD (IDE)     | Using ffle G: V^Downtoad^CentO... |
| 0 Floppy             | Autodetect                        |
| liO'letwofi' Adapter | NAT    'tr-'                      |
| USB Controller       | Present                           |
| Sound Card           | Auto detect    -                  |
| SPrriter             | Present                           |
| 戀 Dismay            | Auto detect                       |

Device status ----

f? Connected P Connect at power on

■ Wetwori( connectiw,    - - ■

广 Ridged: Connected {fe-ectfy to 也e phyacat network

:爹儀讓圖瞻醸灣靡，議輯醺醸■鎖，:人'

(• HAT； Used to share the hosfsP address 广 fckwt-orty: A private network ^ared with the host

C Custom: Specific virtuaJ network

Add... | BemoveJ



'C segment



![img](11 CentOS7fbdfa1060ed0f49e18-45.jpg)



图2.42虚拟机网络配置

虚拟机与宿主主机通信有3种方式，别为：

•    Bridged模式：在桥接模式下，VMware虚招出来的操作系统就类似局域网中的一独 立主机，可以访问网络内任何一台机器。如选择此种模式，需要手工为虚拟系统配置 IP地址、子网掩码，并且和宿主机器处于同一网段，这样虚拟系统才能和宿主机器 进行通信。此时需要设置Linux和宿主主机同网段IP即可。

•    NAT网络地址转换模式：使用NAT模式使虚拟系统借助NAT功能，通过宿主机器 所在的网络来访问互联网。此时Linux的IP地址和VMnet8虚拟网络处于同一网段。

•    Host-only主机模式：在Host-only模式中，所有的盧拟系统可以相互通信，但虚拟系

统和真实的网络是被隔离开的。虚拟系统的TCP/IP配置信息（如IP地址、网关地址、 DNS服务器等）由VMnetl（Host-only）虚拟网络的DHCP服务器来动态分配。

X ft Linux的目录结构

Linux与Windows最大的不同之处在于Linux目录结构的设计，在开始后面的内容前，我 们先来介绍Linux典型的目录结构。

登录Windows以后，打开C盘，会发现一些常见的文件夹，而登录Linux以后，执行ls-1 /会发现在“/”下包含很多的目录，比如etc、usr、var、bin等目录，进入其中一个目录后，看 到的还是很多文件和目录。Linux的目录类似树形结构，如图2.43所示。

认识Linux的目录结构首先必须认识Linux目录结构的最顶端“/”，任何目录、文件和设 备等都在“/”之下》Linux的文件路径与Windows不同，Linux的文件路径类似“/data/myfde.txt”， 没有Windows中盘符的概念。初学者开始对Linux的目录结构可能不是很习惯，可以把 当作Windows的盘符（如C盘）。表2.2对Linux中主要的目录进行说明。

表2.2 Linux常见目录说明

| 参数  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| /     | 根目录。文件的最顶端，/etc、/bin、/dev、/lib、/sbin应该和根目录放置在一个分区中，而 类似/usr/local可以单独位于另一个分区 |
| /bin  | 存放系统所需要的重要命令，比如文件或目录操作的命令ls、cp、mkdir等。另外/usr/bin也 存放了一些系统命令，这些命令对应的文件都是可执行的，普通用户可以使用大部分的命令 |
| /boot | 这是存放Linux启动时内核及引导系统程序所需要的核心文件，内核文件和grub系统引导 管理器都位于此目录 |
| /dev  | 存放Linux系统F的设备文件，如光驱、磁盘等。访问该目录下某个文件相当于访问某个硬 件设备，常用的是挂载光驱 |
| /etc  | 一般存放系统的配置文件，作为一些软件启动时默认配置文件读取的目录，如/etc/fstab存放 系统分区信息 |

（续表）

| 参数        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| /home       | 系统默认的用户主目录。如果添加用户时不指定用户的主目录，默认在/home下创建与用户 名同名的文件夹。代码中可以用HOME环境变量表示当前用户的主目录 |
| /lib        | 64位系统有/lib64文件夹，主要存放动态链接库。类似的目录有/usr/lib、/usr/local/lib等 |
| /lost+found | 存放一些当系统意外崩溃或机器意外关机时产生的文件碎片         |
| /mnt        | 用于存放挂载储存设备的挂载目录，如光驱等                     |
| /proc       | 存放操作系统运行时的运行信息，如进程信息、内核信息、网络信息等。此目录的内容存在 于内存中，实际不占用磁盘空间。如/etc/cpuinfo存放CPU的相关信息 |
| /root       | Linux超级权限用户root的主目录                                |
| /sbin       | 存放一些系统管理的命令，一般只能由超级权限用户root执行。大多数命令普通用户一般 无权限执行，类似/sbin/ifconfig，普通用户使用绝对路径也可执行，用于查看当前系统的网 络配置。类似的目录有/usr/sbin;/usr/local/sbin |
| /tmp        | 临时文件目录，任何人都可以访问。系统软件或用户运行程序（如MySQL）时产生的临时 文件存放到这里=此目录数据需要定期清除。重要数据不可放置在此目录下，此目录空间不 宜过小 |
| /usr：      | 应用程序存放目录，如命令、帮助文件等。安装Linux软件包时默认安装到/usr/local目录下。 比如/usr/share/fonts存放系统字体，/usr/share/man存放帮助文档，/usr/include存放软件的头 文件等。/usr/local目录建议单独分区并设置较大的磁盘空间 |
| /var        | 这个目录的内容是经常变动的，/var/log用于存放系统日志，/var/lib存放系统库文件等 |
| /sys        | 目录与/proc类似，是一个虚拟的文件系统，主要记录与系统核心相关的信息，如系统当前 己经载入的模块信息等，这个目录实际不占硬盘容量 |

樓芥

各个发行版是由不同的公司开发，所以各个发行版之间的目录可能会有所不同。Linux各 发行版本之间目录的差距比较小，不同的地方主要是提供的图形界面及操作习惯等。



2,7小结

与之前的版本相比，CentOS 7的系统结构和安装过程有较大改变。本章主要介绍了与 CentOS安装相关的知识，如分区、LVM、虚拟机的使用等。还介绍了几种常见的安装方法， 如光盘安装、U盘安装、升级CentOS等内容。
