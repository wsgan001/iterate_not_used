
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
