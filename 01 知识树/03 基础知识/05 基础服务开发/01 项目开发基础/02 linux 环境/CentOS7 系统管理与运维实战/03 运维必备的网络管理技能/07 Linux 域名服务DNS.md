
Linux域名服务DNS

如今互联网应用越来越丰富，如仅仅用ip地址标识网络上的计算机是不可能完成任务的，

而且也没有必要，于是产生了域名系统。域名系统通过一系列有意义的名称标识网络上的计算 机，用户按域名请求某个网络服务时域名系统负责将其解析为对应的IP地址，这便是DNS。 本节将详细介绍有关DNS的一些知识。

3.7.1    DNS 简介

目前提供网络服务的应用使用唯一的32位的IP地址来标识，但由于数字比较复杂、难以 记忆。因此产生了域名系统。通过域名系统，可以使用易于理解和形象的字符串名称来标识网 络应用。访问互联网应用可以域名，也可以通过IP地址直接访问该应用。在使用域名访问网 络应用时，DNS负责将其解析为IP地址。

DNS是一个分布式数据库系统，扩充性好，由于是分布式的存储，数据量的增长并不会 影响其性能。新加入的网络应用可以由DNS负责将新主机的信息传播到网络中的其他部分。

域名查询有两种常用的方式：递归查询和迭代査询。

递归查询由最初的域名服务器代替客户端进行域名查询。如该域名服务器不能直接回答, 则会在域中的各分支的上下进行递归查询，最终将返回查询结果给客户端，在域名服务器查询 期间，客户端将完全处于等待状态。

迭代查询则每次由客户端发起请求，如请求的域名服务器能提供需要查询的信息则返回主 机地址信息。如不能提供，则引导客户端到其他域名服务器查询。

以上两种方式类似需要寻找东西的过程，一种是找个人替自己寻找，另外一种是自己完成， 首先到一个地方寻找，如没有则向另外一个地方寻找。

DNS域名服务器的分类有高速缓存服务器、主DNS服务器和辅助DNS服务器。高速缓 存服务器将每次域名查询的结果缓存到本机，主DNS服务器则提供特定域的权威信息，是可 信赖的，辅助DNS服务器信息则来源于主DNS服务器。

3.7.2    DNS服务器配置

目前网络上的域名服务系统使用最多的为BIND (Berkeley Internet Name Domain)软件， 该软件实现了 DNS协议。本节主要介绍DNS服务器的配置过程，包含安装、配置文件设置、 服务器启动等步骤。

1.软件安装

DNS服务依赖的软件可以从rpm包安装或从源码进行安装，本节以rpm包为例说明DNS 服务的安装过程，如【示例3-33】所示。

【示例3-33】

H角汄系统中相关的软件是否已经安装

[root@CentOS Packages]# yum install -y bind bind-utils

Loaded plugins: faatestmirror, langpacks

base    | 3.6 kB 00:00

^第3章运维必备的网络管理技能

……■ ■ L. :— - -

extras

J 3.4 kB    00:00

} 3.4 kB 00:00



![img](11 CentOS7fbdfa1060ed0f49e18-66.jpg)



updates

Loading mirror speeds from cached hostfile

\*    base: mirrors.yun-idc.com

\*    extras: mirrors.sina.cn

\*    updates: mirrors.sina.cn

Package 32:bind-utils-9.9.4-14.el7_0.1.x86_64 already installed and latest

version

经过上面的设置，DNS服务己经安装完毕，主要的文件如下:

/etc/named.conf为DNS主配置文件

/usr/lib/systemd/system/named. service DNS 月艮务控讳！J单元

2.编辑配置文件/etc/named.conf

要配置DNS服务器，需修改配置文件/etc/namedxonf。如果不存在则创建该文件。

本示例实现的功能是搭建一域名服务器ns.oa.com，位于192.168.19.101，其他主机可以通

过该域名服务器解析已经注册的以“oa.com”结尾的域名。配置文件如【示例3-34】所示，如 需添加注释，可以以“#”、“//”、“；”开头的行或使用“/**/”包含。

【示例3-34】

[root@CentOS named]# cat -n /etc/named.conf #此处列出的配置文件已将注释等内容略去 options {

listen-on port 53 { any; }; listen-on-v6 port 53 { ::1; };

directory    n/var/namedM;

dump-file    H/var/named/data/cache_dump.dbn;

statistics-file "/var/named/data/named_stats.txt"; memstatistics-file n/var/named/data/named_mem_stats.txtn; allow-query { any; };

recursion yes;

dnssec-enable yes; dnssec-validation yes; dnssec-lookaside auto;

/* Path to ISC DLV key */ bindkeys-file n/etc/named.iscdlv.key";

' ■ < -    ■-    ....    ;•，圓    ■ '    .    - ■ - '； „■ ... \ . ■ ■，…了    '

managed-keys-directory n / va r/named/dynamic1*;

pid-file u/run/named/named.pidM;

session-keyfile "/run/named/session.key";

}；

logging {

channel default一debug {

file ndata/named.runn; severity dynamic;

}；

zone n." IN {

type hint; file "named.caw;

\#以下为添加的配置项 zone noa.com" IN { type master; file "oa.com.zone0? allow-update { none;};

}；

include 'Vetc/named.rfc!912.zones"; include "/etc/named.root.key";

Name.conf配置文件中的配置项非常多，以下为最主要的配置项说明：

•    options:是全局服务器的配置选项，即在叩tions中指定的参数，对配置中的任何域 都有效，如在服务器上要配置多个域如testl.com和test2.com，在option中指定的选 项对这些域都生效。

•    listen-on port: DNS服务实际是一监听在本机53端口的TCP服务程序。该选项用于 指定域名服务监听的网络接口。如监听在本机IP上或127.0.0.1。此处“any”表示接 收所有主机的连接。

•    directory:指定named从/var/named目录下读取DNS数据文件，这个目录用户可自 行指定并创建，指定后所有的DNS数据文件都存放在此目录下，注意此目录下的文 件所属的组应为named,否则域名服务无法读取数据文件。

•    dump-file:当执行夺出命令时将DNS服务器的缓存数据存储到指定的文件中。

•    statistics-file:指定named服务的统计文件。当执行统计命令时，会将内存中的统计 信息追加到该文件中。

•    allow-query:允许那些客户端可以访问DNS服务，此处“any”表示任意主机。

•    zone:每一个zone就是定义一个域的相关信息及指定了 named服务从哪些文件中获 得DNS各个域名的数据文件，

3.编辑 DNS 数据文件/var/named/oa.com.zone

该文件为DNS数据文件，可以配置每个域名指向的实际IP,此文件可通过复制目录 /var/named中的named.localhost获得模板。文件配置内容如【示例3-35】所示。

【示例3-35]

[root@CentOS named]# cat -n oa.com.zone

| 1    | $TTL | 3600                     |      |          |
| ---- | ---- | ------------------------ | ---- | -------- |
| 2    |      | IN SOA ns.oa.com. root ( |      |          |
| 3    |      |                          | 2015 | ;serial  |
| 4    |      |                          | ID   | ;refresh |
| 5    |      |                          | 1H   | ；retry  |
| 6    |      |                          | 1W   | ;expire  |
| 7    |      |                          | 3H ) | ;minimum |
| 8    |      | NS    ns                 |      |          |
| 9    | ns   | A 192.168.19.1           |      |          |
| 10   | test | A 192.168,19.101         |      |          |
| 11   | bbs  | A 192.168.19.102         |      |          |

下面说明各个参数的含义：

•    TTL:表示域名缓存周期字段，指定该资源文件中的信息存放在DNS缓存服务器的 时间，此处设置为3600秒，表示超过3600秒则DNS缓存服务器重新获取该域名的 信息。

•    @:表示本域，SOA描述了一个授权区域，如有oa.com的域名请求将到ns.oa.com 域查找。root表示接收信息的邮箱，此处为本地的root用户。

•    serial:表示该区域文件的版本号。当区域文件中的数据改变时，这个数值将要改变。从 服务器在一定时间以后请求主服务器的SOA记录，并将该序列号值与缓存中的SOA记 录的序列号相比较，如果数值改变了，从服务器将重新拉取主服务器的数据信息。

•    refresh:指定了从域名服务器将要检查主域名服务器的SOA记录的时间间隔，单位 为秒。

'• retry:指定了从域名服务器的一个请求或一个区域刷新失败后，从服务器重新与主服 务器联系的时间间隔，单位是秒。

•    expire:指在指定的时间内，如果从服务器还不能联系到主服务器，从服务器将丢去 所有的区域数据。

•    Minimum:如果没有明确指定TTL的值，则minimum表示域名默认的缓存周期。

•    A:表示主机记录，用于将一个主机名与一个或一组IP地址相对应。

•    NS: 一条NS记录指向一个给定区域的主域名服务器，以及包含该服务器主机名的 资源记录。

•    CNAME:用来将一个域名和该域名的别名相关联，访问域名的别名和访问域名的原 始名字将解析到同样的主机地址。

第9〜11行分别定义了相关域名指向的IP地址。

![img](11 CentOS7fbdfa1060ed0f49e18-67.jpg)



默认权限可能会阻止bind访问oa.com.zone文件 oa.com.zone修改文件所属的用户组。

因由root用户使用命令chgrp named



4.启动域名服务

启动域名服务可以使用BIND软件提供的/etc/init.d/named脚本，如【示例3-36】所示。

【示例3-36】

[root@CentOS Packages]# systemctl start named.service

如启动失败可以参考屏幕输出定位错误内容，或查看/var/log/messages的内容，更多信息 参考系统帮助“man named.conf” o

3.7.3 DNS服务测试

经过上一节的步骤，DNS服务端已经部署完毕，客户端需要做一定设置才能访问域名服 务器，操作步骤如下。

沙隳



包1    配置/etc/resol v.conf。



如需正确地解析域名，客户端需要设置DNS服务器地址。DNS服务器地址修改如【示例 3-37]所示。

【示例3-37】

[root@CentOS # cat /etc/resolv.conf nameserver 192.168.19.1

1^? 域名测试。

域名测试可以使用ping、nslookup或dig命令。

【示例3-38]

[root@CentOS # nslookup bbs.oa.com #先使用server命令确认是否使用本机作为解析DNS

\>    server

Default server: 192.168.19.1 Address: 192.168.19.1#53

\>    bbs.oa.com

Server:    192.168.19.1

Address:    192.168.19.1#53

Name:    bbs.oa•com

Address: 192.168.19.102

上述示例说明了 bbs.oa.com成功解析到192.168.19.102。

经过以上的部署和测试演示了 DNS域名系统的初步功能，要了解更进一步的信息可参考 系统帮助或其他资料。
