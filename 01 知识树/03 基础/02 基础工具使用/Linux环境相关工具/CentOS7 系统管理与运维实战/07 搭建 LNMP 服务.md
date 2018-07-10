###### SS 7音

###### Tn < sjps

###### 搭蓮LNMP明务

Web服务除了常见的LAMP (Linux+Apache+MySQL+PHP)架构外，另外一种应用比较 广泛的架构即为LNMP (Linux+Nginx+MySQL+PHP)„ Nginx是一款轻量级的Web服务软件， 同时支持负载均衡和反向代理。因为Nginx并发能力很强，所以国内很多大型公司都使用Nginx 作为Web服务器。

本章首先介绍LNMP (Linux+Nginx+MySQL+PHP)涉及的相关软件的安装与管理，然后 介绍Nginx的负载均衡和反向代理，接着介绍Nginx和PHP的两种集成方式。最后通过PHP 操作MySQL的实战案例，使读者了解如何通过PHP实现MySQL数据库表的增、删、改、査 功能。

本章主要涉及的知识点有：

•    LNMP服务安装与管理

•    Nginx负载均衡与反向代理

•    掌握Nginx与PHP集成的两种方式

•    掌握如何通过PHP操作MySQL数据库

###### 7e1    LNMP服务安装与管理

本节主要介绍常见的LNMP (Linux+Nginx+MySQL+PHP)服务的安装与管理。与Apache 相比，Nginx的安装包更轻量级。

7.1.1 Nginx的安装与管理

Nginx软件的安装主要经过3个步骤：检查系统软件环境、编译源码和安装。Nginx的最 新版本可以从[http://nginx.org/](http://nginx.org/%e4%b8%8b%e8%bd%bd%ef%bc%8c%e7%9b%ae%e5%89%8d%e6%9c%80%e6%96%b0%e7%89%88%e6%9c%ac%e4%b8%ba1.6.3%e5%92%8c1.7.12,%e5%85%b6%e4%b8%ad1.6.3%e4%b8%ba%e7%a8%b3%e5%ae%9a%e7%89%88%ef%bc%8c1.7.12)[下载，目前最新版本为1.6.3和1.7.12,其中1.6.3为稳定版，1.7.12](http://nginx.org/%e4%b8%8b%e8%bd%bd%ef%bc%8c%e7%9b%ae%e5%89%8d%e6%9c%80%e6%96%b0%e7%89%88%e6%9c%ac%e4%b8%ba1.6.3%e5%92%8c1.7.12,%e5%85%b6%e4%b8%ad1.6.3%e4%b8%ba%e7%a8%b3%e5%ae%9a%e7%89%88%ef%bc%8c1.7.12) 为开发版。本节以Nginx的1.6.3版本为例说明Nginx的安装过程。

\1. Nginx 安装

安装 Nginx 之前，首先需要安装 PCRE (Perl Compatible Regular Expressions), PCRE 为

Perl语言兼容正则表达式，主要用C语言编写，被很多开源软件所采用。详细的安装过程如 【示例7-1】所示。

【示例7-1】

\#下载解压源码包 [root@CentOS soft]# wget

<ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.35.tar.gz> [root@CentOS soft]# tar jxvf pcre-8.35.tar.gz [root@CentOS soft】# cd pcre-8.35

\#配置并检査系统环境

[root@CentOS pcre-8.35]# ./configure #编译源码

[root@CentOS pcre-8.35]# make #安装

[root@CentOS pcre-8.35]# make install #解压源码包

[root@CentOS soft]# wget <http://nginx.org/download/nginx-1.6.3.tar.gz> [root@CentOS soft}# tar xvf nginx~l.6.3.tar.gz [rootQCentOS soft]# cd nginx-1.6.3

\#检査系统环境

[root@CentOS nginx-1.6.3]# ./configure --prefix-/usr/local/nginx #编译

[root@CentOS nginx-1.6.3]# make #安装

[root@CentOS nginx-1.6.3]# make install

通过以上步骤完成了 PCRE和Nginx软件的安装，两者的安装与普通软件安装类似，并 不需要特殊设置。Nginx安装时依赖zlib和zlib-devel,因此需要提前安装这两个软件。

Nginx安装完后位于/usr/local/nginx目录下，目录结构如【示例7-2】所示。

【示例7-2】

[root@CentOS nginx]# Is conf html logs sbin [root^CentOS nginx]# find .

\#部分文件省略

./sbin

./sbin/nginx

./conf

./conf/koi-win

./conf/koi~utf

./conf/win-utf

，/conf/mime.types

./conf/mime.types * default ./conf/fastcgi_params

./conf/fastcgi_params.default ./conf/fastcgi.conf ./conf/fastcgi.conf.default

Nginx服务的主要文件为sbin/nginx,此程序为Nginx主程序。Nginx的主要配置文件 为./conf/nginx.conf，此文件类似Apache服务的配置文件httpd.conf。

\2. Nginx虚拟主机设置

同Apache类似，Nginx支持多种虚拟主机配置方式，如基于端口的虚拟主机配置、基于 IP的虚拟主机配置和基于域名的虚拟主机配置。本节主要以基于域名的虚拟主机配置为例说 明如何在Nginx下完成基于域名的虚拟主机配置。详细的设置过程与配置文件如【示例7-3】 所示。

【示例7-3】

[root@CentOS sbin]# cd /usr/local/nginx/conf/

[rootQCentOS conf]# mkdir vhost [rootQCentOS conf3 # cd vhost/

\#创建虚拟主机配置文件

[root@CentOS vhost]# cat ~n [www.test.com.conf](http://www.test.com.conf) ，' 1 server {    …

2    listen    192.168.19.101:80;

3    server name [www.test.com](http://www.test.com);

4

5    access一log /data/logs/[www.test.com.log](http://www.test.com.log) main;

6    error一log /data/logs/[www.test.com.error.log](http://www.test.com.error.log);

7

8    location / {

9    root [/data/www.test.com](file:///data/www.test.com);

10    index index.html index.htm;

11    } ,, ’

12 }

\#将虚拟主机配置文件包含进主文件 [root@CentOS vhost]# tail ../nginx.conf #部分内容省略

\#在http段中找到以下内容并删除每行前面的“#”

log一format main 1$remote_addr - $remote一user    一local] n$request0 1

1 $status $bodv bytes sent "$http referer，’ 丨 1n$http一user一agent" "$http一x一forwarded一for"，；

\#配置文件结尾的最后一个“ }”加又以下句，如下所示 ~ ~    ~

include vhost/*.conf;

1

\#创建日志文件，否则无法启动Nginx

[root径CentOS vhost]# mkdir -p /data/logs

[root@CentOS vhost]# touch /data/logs/[www.test.com.log](http://www.test.com.log)

\-

[root^CentOS vhost]# touch /data/logs/[www.test.com.error.log](http://www.test.com.error.log) #先测试配置文件然后再启动Nginx [rootGCentOS vhost]# cd ../../sbin/

[root@CentOS sbin]# ./nginx -t

nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful [root@CentOS sbin]# ./nginx

\#配置host用于测试

[rootQCentOS sbin]# tail /etc/hosts

192.168.19.101 [www.test.com](http://www.test.com)

\#创建虚拟主机目录并创建测试文件index.html

[root@GentOS vhost] mkdir ~p [/data/www.test.com](file:///data/www.test.com)

[root@CentOS vhost] echo nwww.test.com.indexn>/data/[www.test.com/index.html](http://www.test.com/index.html) #测试文件

[root@CentOS sbin]# curl [www.test.com](http://www.test.com) [www.test.com.index](http://www.test.com.index)

189该示例中首先创建了虚拟主机配置文件www.test.com.conf,在此文件中采用了基于域名 的虚拟主机配置。每行的主要含义如下：

第1行为虚拟主机配置标识，此标识类似Apache服务中的VirtualHost。

第2行指定了该虚拟主机监听的IP和端口。

第3行为虚拟主机对应的域名，如配置多个域名，可以用空格分开。

第5~6行指定了 Nginx的日志配置。

第8~11行指定了虚拟主机的主目录和默认文件。

7.1.2 PHP 安装

PHP的安装同样需要经过环境检查、编译和安装3个步骤，这在第6章中已经介绍过， 本节采用php-5.4.16.tar.gz作简单示例如【示例7-4】所示。

【示例7-4】

\#解压源码包

[root@CentOS soft]# tar xvf php-5.4.16•tar.gz [root@CentOS soft}# cd php-5.4.16 #检査系统环境

![img](11 CentOS7fbdfa1060ed0f49e18-136.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-137.jpg)



[root@CentOS php-5.4,16]# ./configure --prefix=/usr/local/php --with-mysql=/usr/local/

mysql --enable-fastcgi #另外一种集成方式编译命令

[root@CentOS php-5.4.16]#    ./configure --prefix=/usr/local/php

--with-mysql=/usr/local/

mysql ——enable-fpm

\#编译源码

[root@CentOS php-5.4.16]# make 職

[root@CentOS php-5.4.16]# make install

“--enable-fastcgi”含义为开启PHP的FastCGI支持，另外一种开启FastCGI支持的方式 为指定w-enable-ipmw参数。在Apache将PHP作为一个模块进行加载，而Nginx通常则是 将PHP请求发送给FastCGI进程处理，因此安装时需要使用上述参数。更多参数及更详细的 定制方法可以参考第6章中的相关章节。

![img](11 CentOS7fbdfa1060ed0f49e18-138.jpg)



MySQL安装方法与第6章中介绍的方法相同，此处不再赘述。

7.2 Nginx负载均衡与反向代理

Nginx是一款优秀的Web软件，同时支持负载均衡和反向代理功能，本节主要介绍Nginx 的负载均衡和反向代理相关的设置。

7.2.1 Nginx负载均衡设置

Nginx除作为Web服务器外，另外支持多种负载均衡算法。常见的算法如轮询、权重、IP 哈希等。

(1)轮询算法：每次将请求顺序分配到不同的服务器，通过此算法可以实现请求在多台 机器之间的轮询转发。轮询算法的负载均衡配置如【示例7-5】所示。

【示例7-5】

[root@CentOS vhost]# cat ~n [www.test.com.conf](http://www.test.com.conf)

1    upstream test一svr

2    {

| 3    | server       | 192.168.19.78:8080;                                          |
| ---- | ------------ | ------------------------------------------------------------ |
| 4    | server       | 192.168.19.79:8080;                                          |
| 5    | server       | 192.168.19.80:8080;                                          |
| ,6   | }            |                                                              |
| 7    | server {     |                                                              |
| 8    | listen       | 192.168.19.101:80;                                           |
| 9    | server       | name [www.test.com](http://www.test.com);                    |
| 10   |              |                                                              |
| 11   | access       | 」og /data/logs/[www.test.com.log](http://www.test.com.log) main; |
| 12   | error—       | log /data/logs/[www.test.com.error.log](http://www.test.com.error.log); |
| 13   |              |                                                              |
| 14   | locatior / { |                                                              |



![img](11 CentOS7fbdfa1060ed0f49e18-139.jpg)



| 15   | proxy_ | pass    http://test一svr;                        |      |          |      |
| ---- | ------ | ------------------------------------------------ | ---- | -------- | ---- |
| 16   | root   | [/data/www.test.com](file:///data/www.test.com); |      |          |      |
| 17   | index  | index.html index.htm;                            |      | t:'效隸1 |      |
| 18   | }      |                                                  |      |          |      |
| 19 } |        |                                                  |      |          |      |

在nginx.conf配置文件中，用upstream指令定义一组负载均衡后端服务器池。

(2)权重算法：通过将不同的后端服务器设置不同的权重以便实现请求的按比例分配， 当后端服务器故障时可以自动剔除该服务器，此算法配置方法如【示例7-6】所示。

【示例7-6】

1    upstream test一svr

2    {

3    server    192.168.19.78:8080 weight=2    max_fails=l    fail^timeout^lOs;

4    server    192.168.19*79:8080 weight=2    max_fails=l    fail_timeout-10s;

5    server    192.168.19.80:8080 weight=6    max^fails^l    fail_timeout=10s;

6    }

其中，test_svr为服务器组名。weight设置服务器的权重，默认值是1，权重值越大，表 示该服务器可以接收更多的请求。max_fails和fail_timeout表示如果某台服务器在fail timeout 时间内出现了 max_fails次连接失败，那么Nginx就会认为该服务器已经故障，从而剔除该服 务器。

(3) IP哈希算法：此算法根据用户的客户端IP将请求分配给后端的服务器，由于源IP 相同的客户端经过IP哈希算法后的值相同，因此同一客户端的请求可以分配到后端的同一台 服务器上。IP哈希负载均衡主要通过指令ip_hash指定，如【示例7-7】所示。

【示例7-7】

1    upstream test_svr

2    {

3    ip一 hash;

4    server    192.168.19.78:8080;

5    server    192.168.19.79:8080;

6    server    192.168.19.80:8080;

7    )

7.2.2 Nginx反向代理配置

反向代理方式与普通的代理方式有所不同，使用反向代理服务器可以根据指定的负载均衡 算法将请求转发给后端的真实Web服务器，可以将负载均衡和代理服务器的高速缓存技术结 合在一起，从而提升静态网页的访问速度，因此可以实现较好的负载均衡。如需设置反向代理， 需要在conf目录中建立文件proxy.conf并修改www.test.com.conf，如【示例7-8】所示。

【示例7-8】

[rootQCentOS conf]# cat -n proxy.conf

1    proxy_redirect off;

2    proxy set header Host $host;

3    proxy一set_header X-Forwarded-For $remote_addr;

4    client一max一body_size 10m;

5    client一 body一 buffer一size 128k;

6    proxy_connect_timeout 3;

7    proxy_send_timeout 3;

8    proxy_read_timeout 3;

9    proxy_buffer_size 32k;

10    proxy__buffers 4 32k;

11    proxy busy buffers size 64k;

12    proxy temp file write size 64k;

13    proxy_next_upstream error timeout http__500 http一502 http_503 http_504; [root@CentOS conf3 # cat -n vhost/[www.test.com.conf](http://www.test.com.conf)

1 upstream test svr

| 3          | server 192.168.19.78:8080 weight=2                           | max_                  | _fails=l | fail_timeout=10s |      |
| ---------- | ------------------------------------------------------------ | --------------------- | -------- | ---------------- | ---- |
| 4          | server 192.168.19.79:8080 weight=2                           | max_                  | fails-l  | fail_timeout=    | :10s |
| 56         | server 192.168.19.80:8080 weight-2}                          | max 一                | _fails=l | fai1一timeout=   | :10s |
| 78         | server {                                                     |                       |          |                  |      |
| 9          | listen 192.168.19.101:80;                                    |                       |          |                  |      |
| 10         | server一name [www.test.com](http://www.test.com);            |                       |          |                  |      |
| 11         |                                                              |                       |          |                  |      |
| 12         | location /                                                   |                       |          |                  |      |
| 13         | {                                                            |                       |          |                  |      |
| 14         | proxy_pass http: "test一svr;                                 |                       |          |                  |      |
| 15         | include proxy.conf;                                          |                       |          |                  |      |
| 16         | }                                                            |                       |          |                  |      |
| 17         | location 〜★ A.+\.(jsI css J icoI jpg\|jpeg I gif{pngIswf(rarI zip)$ |                       |          |                  |      |
| 18         | {                                                            |                       |          |                  |      |
| 19         | root [/data/www.test.com/htdocs](file:///data/www.test.com/htdocs); |                       |          |                  |      |
| 20         | expires 2d;                                                  |                       |          |                  |      |
| 21         | access一log /data/logs/[www.test](http://www.test).          | com-access一log main; |          |                  |      |
| 22         | }                                                            |                       |          |                  |      |
| 23         |                                                              |                       |          |                  |      |
| 24         | location - \.php$                                            |                       |          |                  |      |
| 25         | {                                                            |                       |          |                  |      |
| 26         | fastcgi一pass 127.0,0.1:9000;                                |                       |          |                  |      |
| 27         | fastcgi_index index.php;                                     |                       |          |                  |      |
| 28         | fastcgi一param SCRIPT一FILENAME                              |                       |          |                  |      |
| $document_ | _root$fastcgi_script_name;                                   |                       |          |                  |      |

29    include fastcgi^arams;

30    }

31

32    access_log /data/logs/www.test.com-access_log main;

33    error一log /data/logs/www.test.com~error_log;

34    }

其中，第14行“proxy_pass http://test_svr”用于指定反向代理的服务器池。

第17行表示请求的文件如果为指定的扩展名，则直接从指定目录读取。

第24行表示如果是以php为扩展名的文件，则转给本地的FastCGI处理。proxy.conf文件 中第3行表示将客户端真实的IP传送给后端服务器，如后端服务器需要获取客户端的真实IP， 则可以从变量X-Forwarded-For中获取。

如需了解更多参数的相关介绍，可以参考Nginx的帮助手册。

1937.3 集成 Nginx 与 PHP

Nginx与PHP的常见集成方式有两种：一种是通过spawn-fcgi方式，另外一种是通过 php-frra方式。两种集成方式类似，并无太大区别，本节主要介绍如何通过这两种方式集成 Nginx 和 PHP。

7.3.1 spawn-fcgi 集成方式

使用spawn-fcgi与PHP集成首先要安装相应的软件，这里的版本为spawn-fcgi-1.6.4.tar.gz, 软件安装与设置主要经过以下几个步骤。

\1. spawn-fcgi软件安装

安装过程如【示例7-9】所示。

【示例7-9】

\#下载解压源码包 [rootQCentOS soft]# wget

<http://download.lighttpd.net/spawn-fcgi/releases-1.6.x/spawn-fcgi-1.6.4.tar.gz> [rootGCentOS soft]# tar xvf spawn-fcgi-1.6.4.tar.gz [root@CentOS soft]# cd spawn~fcgi~l.6.4

\#检査系统环境

[root@CentOS spawn-fcgi-1.6.4]# ./configure --prefix^/usr/local/spawn-fcgi #编译源码

[root@CentOS spawn-fcgi~l.6.4]# make #安装

[root@CentOS spawn-fcgi-1.6.4]# make install

经过上面的步骤需要的软件spawn-fcgi已经安装完成，位于/usr/local/spawn-fcgi目录下。 spawn-fcgi安装完成后，需要安装PHP，安装命令可参考示例7-4。

spawn-fcgi 集成方式需要 PHP 的 FastCGI 程序位于/usr/local/php/bin/php-cgi，在编译 PHP 时需要使用选项“--enable-fastcgi”。

2.虚拟主机设置

本节主要进行虚拟主机的相关配置。www.test.com对应的虚拟主机配置可参考7.1.1小节。 本节需要的配置文件如【示例7-10】所示。

【示例7-10】

[root@CentOS vhost]# cat ~n [www.test.com.conf](http://www.test.com.conf)

1    server {

2    listen 192.168.3.88:80;

3    server一name [www.test.com](http://www.test.com);

4    root [/data/www.test.com](file:///data/www.test.com);

5

6    access一log /data/logs/[www.test.coni.access.log](http://www.test.coni.access.log) main;

7    error__log    /data/logs/[www.test.com.error.log](http://www.test.com.error.log);

8    location ~    \.php$

9    {

| 10         | fastcgi一 pass    127.0.0.1:9000; |
| ---------- | --------------------------------- |
| 11         | fastcgi一index index.php;         |
| 12 :       | fastcgi_param SCRXPT_FILENAME     |
| $document^ | •root$fastcgi一script一name;      |
| 13 :       | include fastcgi一 params;         |
| 14         | }                                 |
| 15         |                                   |
| 16         | location /                        |
| 17         |                                   |
| 18         | index index.php index.html index. |
| 19         |                                   |
| 20         | }                                 |

以上设置为虚拟主机www.test.com支持PHP的设置。

第1行指定虚拟主机配置的开始。

第3行为指定虚拟主机对应的域名，如有多个域名可以使用空格分隔。

第4行指定了虚拟主机对应的主目录。

第8〜14行为PHP与spawn-fcgi集成的关键配置，表示如果访问的文件以“.php”扩展名 结尾，则将请求转到本机127.0.0.1的9000端口处理。

第11行指定了默认的首页文件。

第12行指定了 PHP对应的处理CGI。

第 13 行表示包含/usr/local/nginx/conf 目录下的 fastcgi_params 文件。

\3.    启动 spawn-fcgi

经过上面的设置相关配置已经完成，然后进行spawn-fcgi的启动，启动命名如【示例7-11】 所示。

【示例7-11】

\#庖动 spawn-fcgi

[root^CentOS 并 cd /usr/local/spawn-fcgi/bin/

[root@CentOS bin}# ./spawn-fcgi ~a 127.0.0.1 -p 9000 -f

/usr/local/php/bin/php-cgi #检査spawn-fcgi是否启动成功 [root@CentOS # netstat -pint Igrep 9000

tcp    0    0 127.0.0.1:9000    0.0.0.0:*    LISTEN

5015/php-cgi

经过上面的步骤spawn-fcgi已经启动。“-a”参数表示服务启动时绑定的IP, “-p”表示 服务启动时监听的端口，“-f”指定了 php-Cgi文件所在的位置。

\4.    编辑测试文件

编辑测试文件index.php并启动Nginx，文件内容及启动命令如【示例7-12】所示。

【示例7-12]

\#编辑测试文件

[root@CentOS # cat -n /data/[www.test.com/index.php](http://www.test.com/index.php)

1    <?php

2    phpinfoO;

3    ?>

[root@CentOS # cd /usr/local/nginx/sbin/

\#治动Nginx

[root@CentOS sbin]# ./nginx    .

\#检查是否启动成功

[root@CentOS sbin}# netstat -pint Igrep 80

tcp    0    0 0.0.0.0:80    0.0.0.0:*    LISTEN

5125/nginx

\5.    鉞结果测试

Nginx启动成功后，可以进行访问测试，测试结果如图7.1所示。

々 0-    众☆自 + 舍

| System                                 | Linux localhost.tocaldomain 3.10.0-229,1.2.el7,x86_64 #1 » 035426 UTC 2015 x86.64 | »» Fri Mar 27 |
| -------------------------------------- | ------------------------------------------------------------ | ------------- |
| Build Date                             | Apr 17 2015 223HO5                                           |               |
| Configure Command                      | ’ ./configure' '—prefix-/usr/local/php•    with-mysql®/usr/localZmysql,■-enoble-fostcgi' |               |
| Server API                             | CGl/FestCGI                                                  |               |
| Virtual Directory Support              | dfsabled                                                     |               |
| Configuration FBe (php.ini) Poth       | /usr/local/(相/lib                                           |               |
| Loaded Configuration FHe               | (none)                                                       |               |
| Scan this dir for additional.Ini files | (none)                                                       |               |
| Additional .ini files parsed           | (none)                                                       |               |
| PHP API                                | 20100412                                                     |               |
| PHP Extension                          | 20100525                                                     |               |

图7.1 spawn-fcgi集成方式测试

如正常出现上述输出“Server API CGI/FastCGI”，则表示Nginx通过spawn-fcgi与PHP集 成，已经成功运行，然后可以进行PHP程序的开发了。

7.3.2 php-fpm集成方式

php-fjim同spawn-fcgi类似，是FastCGI进程管理器，最新的PHP版本已经集成php-fpm 的源码，安装时只需开启“--enable-fj)m”参数即可。相对于spawn-fcgi，php-fj)m处理方式更 高效，为推荐的集成方式。本节主要介绍php-fj)m集成过程与测试，主要经过以下几个步骤。

1.编译安装PHP

PHP编译安装过程如【示例7-13】所示。

【示例7-13】

[root@CentOS php-5.4.16]#    ./configure --prefix-/usr/local/php

--with-mysql=/usr/local/mysql --enable-fpm

froot@CentOS php~5.4.163# make [root@CentOS php-5.4.16]# make install

经过上面的步骤，PHP软件已经安装完成，关键文件位于/usr/local/php/sbin/php-fjDm。

\2.    虚拟主机配置

本节主要进行虚拟主机的相关设置，www.test.com对应的虚拟主机配置同7.3.1小节的内容。

\3.    启动程序php-fpm

配置完成后，然后可以进行PHP-FPM的启动，启动命令如【示例7-14】所示。

【示例7-14】

| [root@CentOS                                                 | -# cd /usr/local/php/etc                    |      |
| ------------------------------------------------------------ | ------------------------------------------- | ---- |
| #此文件为php-fpm程序需要的配置文件                           |                                             |      |
| (root@CentOS:                                                | etc] # mv php-fpm,conf.default php-fpm.conf |      |
| [root@CentOS                                                 | 〜killall -9 php~cgi                        |      |
| #启动相关进程                                                |                                             |      |
| [root@CentOS#检査启动结果                                    | # /usr/local/php/sbin/php-fpm               |      |
| [root^CentOS                                                 | 〜]# netstat -pint i grep 9000              |      |
| tcp    0    0 127.0.0.1:9000    0.0.0.0:*37815/php-fpm: mast | LISTEN                                      |      |

经过上面的步骤程序/usr/local/php/sbin/php-fpm已经启动，启动时需要的配置采用了 php-fpm.conf文件中的默认设置。

4.测试

编辑测试文件index.php并启动Nginx,文件内容及启动命令可参考7.3.1节的对应内容。 Nginx启动成功后，测试结果如图7.2所不。

图7.2 PHP-FPM集成方式测试

如正常出现上述输出“Server API FPM/FastCGI”，则表示Nginx通过PHP-FPM与PHP 集成，已经成功运行，然后可以进行PHP程序的开发了。

1.4 LNMP实战

PHP提供了高级语言中的流程控制、循环、函数、类等功能，本节以一个简单的入门程 序为例，说明PHP程序的编写过程，然后介绍了如何利用PHP实现MySQL表的查询、添加、 修改和删除。

7.4.1 第1个PHP程序

本节的示例比较简单，功能为在网页上显示字符串“Hello World”。详细代码如【示例7-15] 所示。

【示例7-15】

[root@CentOS BBS] # cat -n hello.php    篇讀^

1    <html>

2    <head>

3    <meta http-equiv=nContent-Typen content-"text/html; charset==UTF-8" />

4    <title>first PHP program</title>

5    <body>

6    <?php

7    echo "Hello World!";

8    ?>

9 </body>

第1~4行为HTML代码，PHP代码以“<?php”标记开始，以“?>”标记结束，中间为 PHP代码部分，如第7行作用是使用echo命令显示一字符串“Hello Word”。

7.4.2数据库连接

PHP提供了一系列函数用来操作MySQL数据库，本节主要介绍如何使用PHP程序连接 MySQL,主要代码如【示例7-16】所示。

【示例7-16】

| [root@CentOS BBS]# cat -n connect.php |                            |
| ------------------------------------- | -------------------------- |
| 1                                     | <?php                      |
| 2                                     | #数据库IP地址              |
| 3                                     | $host - "192.168.19.101”； |
| 4                                     | #连接数据库的用户名        |
| 5                                     | $db user = nbbs*';         |
| 6                                     | #连€数据库的密码           |
| 7                                     | $db_pass = "bbs..com";     |
| 8                                     |                            |

第7章搭建LNMP服务

勤薇燃魅:避必鑑



9

10

11

12

13

14

15

16

17

18

19

20 21 22

23

24

25

26

27

28

29

30

31

32



$db_name = "BBS”；

\#指定时区

$ time zone = ” Asia/Shanghai’•； #使用mysql_connect连接数据库 $link = mysql一connect($host, #判断是否连接成3

if($link!=null)

{

echo "数据库连接成功％•

}

else

{

echo "数据库连接失败！"；

exit ();

}



$db一user, $db_pass);



![img](11 CentOS7fbdfa1060ed0f49e18-142.jpg)



mysql_select__db ($db_namer $link);

mysql_query("SET names UTF8");

\#设置i面编码

header("Content-Type: text/html; charset=utf-8n); #设置默认时区

date default timezone set($timezone);



?>



上述示例首先设置了数据库的IP地址、用户名、密码和连接的数据库，然后使用 mysql_connect进行数据库连接，并通过返回值判断连接是否成功。mysql_select_db函数用于 选择数据库，mysql_query函数设置了默认字符集编码，date_default_timezone_set函数设置了 默认时区。



7.4.3记录查询

上一节介绍了如何使用PHP连接MySQL,本节主要介绍如何使用PHP查询数据库中的 记录。本示例涉及的数据库和表的创建语句如【示例7-17】所示。



【示例7-17】

mysql> CREATE DATABASE IF NOT EXISTS BBS; Query OK, 1 row affected (0.00 sec)

mysql> USE BBS

Database changed

mysql> CREATE TABLE IF NOT EXISTS "users' ( -> 'id' int(11) NOT NULL AUTO一INCREMENT, ->    'uname' varchar(20) DEFAULT NULL,



![img](11 CentOS7fbdfa1060ed0f49e18-143.jpg)



-> 'address' varchar(200) DEFAULT NULL/ -> PRIMARY KEY ('id')

-> )ENGINE-InnoDB DEFAULT CHARSET-utf8;



![img](11 CentOS7fbdfa1060ed0f49e18-144.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-145.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-146.jpg)



Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO users(uname,address) VALUES(1 alien *,fBeiJing*); Query OK, 1 row affected (0.00 sec)



![img](11 CentOS7fbdfa1060ed0f49e18-147.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-148.jpg)



mysql> INSERT INTO users(uname,address) VALUES(f cron\ 丨ShangHai»); Query OK, 1 row affected (0.00 sec)

以上创建了数据库BBS,并创建了表users,它包含字段id, INT类型，该表的主键，自 增；字段uname表示用户名；字段address表示地址。INSERT语句添加了测试数据。

查询表中的记录首先需要连接数据库，然后使用SELECT语句查询出需要的记录，通过 遍历将记录取出并显示到页面上。详细代码如【示例7-18】所示。

【示例7-18】

| [rootGCentOS BBS# cat -n users.php |                                                              |
| ---------------------------------- | ------------------------------------------------------------ |
| 1                                  | <?php                                                        |
| 2                                  | include 一 once (’’connect. php">;                           |
| 34                                 | ?>                                                           |
|                                    | <html>                                                       |
| 6                                  | <head>                                                       |
|                                    | <meta http-equiv=HContent-Typen content=Mtext/html; charset-UTF-8n /> |
|                                    | ＜title＞用户信息查询＜/title〉                              |
|                                    | <link rel=nstylesheet" type="text/css" href-ncss/main.cssn /> |
| 10                                 | <script language=’.javascript"〉                             |
|                                    | function check(form){                                        |
| 12                                 | if (form，txt一keyword.value55^’"’){                         |
| 13                                 | alert （"^询关键字不能为空！"）；                            |
| ；14                               | form.txt_keyword.focus();                                    |
| 15                                 | return false;                                                |
| 16                                 | }    、i    窪::::籍纖瀑                                     |
| 17                                 | form.submit();                                               |
| 18                                 | }    戀窮霞議霧義，                                          |
| 19                                 | </script>                                                    |
| 20                                 | </head>                                                      |
| 21                                 |                                                              |
| 22                                 | <body>                                                       |
| 23                                 | <table>                                                      |
| 24                                 | <tr>                                                         |
| 25                                 | <td heights"30” aligrv=’’centerM>                            |
| 26                                 | 〈form name-nformln method-nget" action:’"’〉                |
| 27                                 | 査询关键字                                                   |
| 28                                 | 〈input name-ntxt_keywordn type^^texf* id~ntxt_keywordn      |

size=M40">

29

onClick=°return check(form)M

<input type="submit” name="Submit" value®"搜索"



a:':::::. / ':雞■



30

■31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52



〈/form〉

</td>

</tr>

</table>

pHVit0y-(1,    v ' s

<table>

<thead>

<tr>

<td colspan~H60n><span class="open"></span>用户信息査询</td>

</tr>

</thead>

<tbody>

<<•+- r-'>

、UJ- z

<td>用户 ID</td>

<td>用户名</td>

<td> 地址 </td>

</tr>

<?php

if ($txt_keyword=s~null)

$txt keyword=$_GET[ntxt_keywordM];

$rs^mysql__query("select id,uname,address from BBS.users a where a.uname like '%$txt_keyword% * n);

53    $count = mysql一num一rows ( $rs);

54    $i=l;    \ :

55    if ($count-==0)

56    (

57    echo ”<tr><td colspan=100 align^centerxfont color=red size=3>没有查询到符合条件的记录！ </td></tr〉"；

| 58   | exit;                                      |
| ---- | ------------------------------------------ |
| 59   | }                                          |
| 60   | while ( $row = mysql__fetch_row ( $rs ))   |
| .61  | {■                                         |
| 62   | $num=：0;                                  |
| 63   | 9>                                         |
| 64   | <tr>                                       |
| 65   | <tdx?php echo $row[$num++]; ?></td>        |
| 66   | <tdx?php echo $row f $num++-] ； ?></td>： |
| 67   | <tdx?php echo $row[$num++] ; ?></td>       |
| 68   | </tr>                                      |
| 69   | <?php                                      |

70    }

71    ?>

72    </tbody>

73    〈/table〉

第2行使用include指令包含了文件connect.php。

第10-19行为判断用户页面输入的参数，不允许输入的参数为空。

第22行开始为网页正文。

第25-33行为显示输入框，可以在页面上输入参数，单击【搜索】按钮后将参数传递给 MySQL语句进行查询。

第34行指定了接下来显示一个表格。

第41-45行为表格表头说明文字。

第47-50行判断当输入的参数为空时如何处理，如不输入任何参数，则显示表中的所有 符合条件的记录。

第52行将输入的关键词作为MySQL查询语句的参数，然后通过循环遍历结果集，并以 表格的形式显示在页面上。

此示例的执行结果如图7.3所示。

i 毗.t <http://www.test.com/BBSZusers.php>

x i

x査找|    ♦下一个奮上4    壹找结臬:第o个,

数据库连接成功

査询关键字I

搜索|

| 用户信息查询 |        |          |
| ------------ | ------ | -------- |
| 用户ID       | 用户名 | 舰       |
| 1            | aQen   | Beiling  |
| 2            | cron   | ShangHai |

图7.3用户信息查询结果

7.4.4增加分页

如果表中记录过多，所有结果放在一页中会影响页面性能并影响浏览效果，通过分页可以 优化显示效果。分页的方法有很多选择，本节介绍一种简单的分页方法，査询时通过指定 MySQL的LIMIT来实现指定范围记录的查询与显示。具体代码如【示例7-19】所示。

【示例7-19】

[rootQCentOS BBS# cat -n users一 page，php 1 <?php

2

3

4

5

6

7

8 9

10

11



include一once(° connect.php n);

?>

y    -rl"'1 P/I ipi I JI I'1/ li ' V ii i'i    V / /I i    ! mjjU!    ■%?/ ?；,    f /, '    J " / / / / /' . .//< '

； ... : 1 。i

:' .... ■ ^ . . . ^ . ■ ^ ^ 、 .‘ ■ . .. ■ .. J .

<html>

<head>

<meta http-equiv=nContent-Typen content^0text/html; charset=UTT-8" /> <title>用户信息査询，带分页〈/title〉

<Iink rel=nstylesheetn type="text/css” href=ncss/main.cssu />

<script language:”javascript”> function check(form){

12    if(form.txt_keyword.value«=MM){

13    alert    询关键字不能为空！")；

14    form.txt一keyword.focus();

15    return false;

16    }

17    form.submit();

18    I

19    </script>

20    </head>

21

22    <body>

23    <table>

24    <tr>

![img](11 CentOS7fbdfa1060ed0f49e18-149.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-150.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-151.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-152.jpg)



25

26

27

28

id-,ftxt_keywordH

29



<td height=n30n align="centern>

<form name="forml” methoci~ngetn action=H°>

査询关键字&nbsp;

.■. -

〈input name="txt_keyword" type="text"

size-n40n>

<input type='’submit" name="Submit" value=”搜索"

onClick=Mreturn check(form)n>

•* -

30    </form>

31    </td>

32    </tr>

33    </table>

34    <table algin二”center">

35    <thead>

36    <tr>

37    <td    colspan=K60Hxspan    class=Hopenn></span>^/±1^^#-iflj</td>

38    </tr>

39    </thead>

40    <tbody>

41    <tr>

42    <td>用户 XD</td>

43    <td> 用户名 </td>

44    <td〉地址 </td>

| 45                               | </tr>                                                        |
| -------------------------------- | ------------------------------------------------------------ |
| 46                               | <?php                                                        |
| 47                               | if($ txt_keyword~-nul1)                                      |
| 48                               | {                                                            |
| 49                               | $txt_keyword=$_GET[ntxt_keyword°];                           |
| 50                               | }                                                            |
| 51                               | $page=$_GET["page"];                                         |
| 52                               |                                                              |
| 53-                              | if <$page==，"’)                                             |
| 54                               | ( -{                                                         |
| 55                               | $page-l;                                                     |
| 56                               | }                                                            |
| 57                               | $page_size=3;                                                |
| 58                               | $query=nselect count{*) as c from BBS.users a where          |
| a.uname like * %$txt_keyword%1H; |                                                              |
| 5 9                              | $rs_count=mysql_query($query);                               |
| 60                               | $total一count=mysql一result <$rs一count,0,nc");              |
| 61                               | $page_total=ceil($total_count/$page_size);                   |
| 62                               | $offset=i ($page~l) *$page_size;                             |
| 63                               |                                                              |
| 64                               | $rs=mysql一query("select id,uname,address from BBS.users     |
| a where a                        | .uname like ' %$txt keyword%1 limit $offset, $page sizeH);—■    —■ |
| 65                               | $count = mysql_num_rows ( $rs);                              |
| 66                               | $j=$offset+1;                                                |
| 67                               | $i=l；                                                       |
| 68                               | if($count==0)                                                |
| 69                               | {                                                            |
| 7 0                              | echo "<trxtd colspan=100 align=center><font                  |
| color-red                        | size==3>没有查询到符合条件的记录！ </td></tr>";              |
| 71                               | exit;                                                        |
| 72                               |                                                              |
| 73                               | while ( $row = mysql_fetch_row ( $rs ))                      |
| 74                               | {                                                            |
| 75                               | $num-0;                                                      |
| 76                               | ?>                                                           |
| 77                               | <tr>                                                         |
| 78                               | <tdx?php echo $j++ ?></td>                                   |
| 79                               | <tdx?php echo $row[l] ; ?></td>                              |
| 80                               | <td><?php echo $row[2]; ?></td>                              |
| 81                               | </tr>                                                        |
| 82                               | <?php                                                        |
| 83                               | J                                                            |
| 84                               | ?> + +                                                       |
| 85                               | </tbody>                                                     |
| 86                               | </table>                                                     |

87

88

89

90



<table>

<tr>



<td>当前第<?php echo $page;?项，总共<?php echo $page_total; ?>



页，总记录<?php echo $total~count;?>条</td>

91

92

93

94

95



<td>

<?php

if($page!-l)

echo ”<a

href-users_page.php?page-l&txt_keyword=$txt_keyword>'^*页</a>&nbsp; n;

96    echo "<a

href=users_page.php?page忽".($page-l).H&txt_keyword=$txt_keyword>_h—M </a>&nbsp;n;

href^users^page.phpTpage-1'. ($page+l) . M&txt_keyword=$txt_keyword>T~*M </a>&nbsp;

101    echo "<a

href=users_page.php?page=n.$page_total."&txt_keyword=$txt_keyword> 尾页 </a>&nbsp;n;

102    }

103    ?>

104    </tr>

105    〈/table〉

本示例与不带分页的示例的区别在于第53〜62行。

第53~56行获取当前的页码编号，如果为空，则用户首次浏览时显示第1页内容。

第57行指定了每页可以显示记录的条数。

第58-61行主要执行查询并得到符合条件的记录的总数。

第62行用于计算查询时需要的偏移量。

第64行根据计算的偏移量和每页显示的记录条数执行LIMIT查询。

第88〜105行主要显示首页、下一页、上一页和尾页超链接，并将查询关键字和页码编号

传给指定的页码。

本例运行效果如图7.4所示。

地址：磨」http：Z/www.test.conVusers_page.php?p^e=2&txt_k.eyword=

x查找|    I下一个资上一个    查找结果:第o个，共叶▼

数据库连接成功



查询关键字|    ；    搜索|

用户信息查询

| 用户ID | 用户名    | 地址         |
| ------ | --------- | ------------ |
|        | user22494 | addressl980  |
|        | user23403 | address3329  |
|        | oscr24312 | adkiress4678 |

当前第2页，总共W6页，总记录317条.首页上一页下一页尾页 图7.4用户信息分页查询

7.4.5添加记录

上一节介绍了如何通过关键字查询符合条件的记录及如何分页，本节介绍如何使用PHP 添加MySQL记录。

添加记录的代码如【示例7-20】所示。

【示例7-20]

[root@CentOS BBS)# cat -n users一add.php



1

2

3

4

5    ；

6

7

8 9

10

11

12

13

14

15

16    :

17

18

19

20



<ht爪1〉

<head>

<meta http-equiv="Content-Type’’ content=ntext/html; charset二UTF-8" /> 〈title〉用户信息添加</title>

<link rel=MstylesheetM type=M text/cssn href^*’1 css/main .css" />

<script language-**javascriptM> function check(form){

if (form.uname, value=s=,,n)

{

alert (”用户名不能为空！")； form.uname.focus (); return false;

}

form.submit();

J?' a    人'

</script>

</head>

<body>

<form name^^users^ method~MGETM action-Musers add do.phpM>

21    <table algin=ncentern>

22    <thead>

18



23    <tr>

![img](11 CentOS7fbdfa1060ed0f49e18-154.jpg)



24    <td colspan 口 2> 用户信息添加 </td>

25    </tr>

![img](11 CentOS7fbdfa1060ed0f49e18-155.jpg)



26    </thead>

27    <tbody>

28    <tr〉

29    <比>用户名</td><td><input name="unamen type二"text” id=Munamen size-M50n></td>

30    </tr>

31    <tr>

32    <td>地址</td><td><input name~naddress0 type~ntextM id=”address" size=M50wx/td>

33    </tr>

34    <tr>

35    <td></td><td><input type=Hsubmitn name。”submit" value二” 添加"onC 1 ick«"return check (form) n> </td>

36    </tr>

37    </tbody>

38    </table>

39    </form>

运行效果如图7.5所示。

~t_________________________________________

X查找|    一    ＜下一个舍上一个    查找结果:第0个,共0个，

用户信息添加 用户名    (unarnel

地址    jaddressl

添加j

图7.5用户信息添加

输入用户信息单击【添加】按钮后需要相应的处理程序，将输入的信息添加到数据库中的 代码如【示例7-21】所示。

【示例7-21】

[root@CentOS BBS] # cat -n users_add__do.php

1    <?php

2    include_once("connect.php”)；

3    echo "<br>";

4    $uname-$_GET["uname"];

5    if($uname«=nn)

6    {

7    echo ”用户名不能为空！ ”；

8    :

9 else 10 {

11    $address«$__GET [ "address"];

12    $query=mysql_query(ninsert into BBS.users{uname,address)

values (' $uname *, 1 $address ”    ；

if($query)

{

echo n记录添加成功"；

}

else

{

echo ”记录添加失败"；

}

}

include <’’users_page.php"); ?>



13

14

15

16

17

18

19

20 21 22 23

运行效果如图7.6所示。

数据库连接成功 记录添加成功

查询关键字!    H

用户信息查询

序号    用户名    地址

1    李四    上梅

2    李勇    北京

3    赵亮    天律

当前第1页，总共4页，总记录11 ‘    下一页尾页

图7.6添加数据库记录

208

7.4.6修改记录

如需修改数据库相关记录，首先需要根据该表的主键查询对应的记录，然后显示到修改页 面，提交后保存到数据库中。

本节介绍的修改功能对应的代码是在users_page.php基础上修改的，主要是添加修改记录 需要的超链接，超链接应该将当前记录的主键传到更新页面users update.php, users_page.php 所做修改如【示例7-22］所示。

【示例7-22】

| [root@CentOS BBS] # cat -n users__page.php |                                              |              |      |
| ------------------------------------------ | -------------------------------------------- | ------------ | ---- |
| 1                                          | #其余代码同7.4 • 4章节中的user_page.php      |              |      |
| 2                                          | <tbody>                                      |              |      |
| 3                                          | <tr>                                         | ■ . ■ "      |      |
| 4                                          | <td〉序号 </td〉                             |              |      |
| 5                                          | <td〉用户名</td>                             |              |      |
| 6                                          |                                              |              |      |
| 7                                          | <td> 操作 </td>                              |              |      |
| 8Q                                         | </tr>                                        |              |      |
| 10                                         | #其余代码同7.4.4章带中的user__page.php       |              |      |
| 11                                         | while ( $row = mysql_fetch_                  | row ( $rs )) |      |
| 12                                         | {                                            |              |      |
| 13                                         | $num=0;                                      |              |      |
| 14                                         | ?>                                           |              |      |
| 15                                         | <tr>                                         |              |      |
| 16                                         | <tdx?php echo $j++ ?></td>                   |              |      |
| 17                                         | <tdx?php echo $row [1]；                     | ?></td>      |      |
| 18                                         | <tdx?php echo $row[2];                       | ?></td>      |      |
| 19                                         | <td><a href==users_update .php?id=<?php echo |              |      |
| $row[0];                                   | ?>> 修改 </a></td> .                         |              |      |
| 20                                         | </tr>                                        |              |      |
| 21                                         | <?php                                        |              |      |
| 22                                         | }                                            |              |      |
| 23                                         | ?>                                           |              |      |
| 24                                         | </tbody>                                     |              |      |
| 25                                         | </table>                                     |              |      |
| 26                                         | .#其余代码同7 • 4 • 4章节中的user_page.pbp   |              |      |

以上代码运行效果如图7.7所示。

用户信息查询

| 序号 | 用户名 | 地址   | 揀作 |
| ---- | ------ | ------ | ---- |
| 4    | 李洋   | 北京   | 修改 |
| 5    | 梅霞   | 天律   | 修改 |
| 6    | 小崔   | 内蒙古 | 修改 |

当前第2页，总共3页，总记录7条    首页上一页下一页尾页

http://www. test.com/BBS/users upda te.php?id=6

图7.7增加修改超链接

图7.7显示了修改需要的超链接，并将主键作为参数加入到超链接中，以便更新页面可以 通过指定的主键ID查找对应记录。

users_update.php负责查询指定的记录并显示相关信息，详细代码如【示例7-23】所示。 【示例7-23】

[root@CentOS BBS]# cat -n users_update.php 1 <?php

include_once connect.php");

?>

<html>

<head>

<meta http-equiv=°Content-Typen contentext/html; charset二UTF-8" />

住:咏i如用户信息修改或:::

<link re stylesheet*' type二"text/css” href-Mcss/main.cssM /> 〈script language-**javascriptH> function check(form){

2

3

4 5：

6

7

8 9

10

11

12

13

14

15

16

17

18

19

20 21 22

23

24

25

a.id=$id")

26

27

28 29



if(form.unarae.va1ue== un) {

alert (”用户名不能为空！ form.uname.focus (); return false;

}

form.submit();

}

</script>    ’

</head> <body>

<?php

$id=$_GET["id»];

$rs=mysql_query("select id,uname,address from BBS.users a where

$count = mysql一num一rows ( $rs); if($count~0)

. .■ •' ； ■ „ 、•'. :' .■ . , ■ ■ : ； •. ..

echo "<tr><td colspan=100 align=center><font color-red size~3>

没有査询到符合条件的记录！ </tdx/tr>t*;

30

31

32

33

34

35

36

37

38

39



exit;

}

$row



mysql一fetch一row ( $rs )



?>

<form name二’’users" method="GET,' action®’’users一update一do.php"> <table algin=°centerH>

<thead>

<tr>

<td colsparv=2> 用户信息修改 </td>

〈input name=’’id" type=Mhiddenn id=nid,r value二<?php echo

$row[0]; ?> size="50”>

藝麵醸讀



40

41

42

43

44



</tr>

</thead>

<tbody>

<tr>

<td〉用户名</td><td〉<input name="uname” type=ntext" id=nuname**

value=n<?php echo $row[1]; ?>" size="50"></td〉

45    </tr>    ■:

46    <tr>

47    <td>地址</td><td〉<input narae="address’1 type="text" address" value="<?php echo $ row [23; ?〉" size=M50nx/td>

48    </tr>

49    <tr>

50    <tdx/td><tdxinput type二”submit" name-nsubmit11 value-** 修改n onClick.=Hreturn check (form) H> </td>

51    </tr>

52    </tbody>

53    </fcable>

54 </form>

上述示例第24行得到记录的主键，第25行从数据库中查找对应的记录，第32〜54行将 查找到的记录以表单形式展现出来，显示效果如图7.8所示。

数据库连接成功

用户信息修改 用户名 R1

地址 poi    ~

條改j

图7.8修改记录页面

在记录被修改后，可以通过单击“修改”按钮将更改后的数据传送给更新处理页面 users_update_do.php,该页面主要负责获取更新后的数据并更新到数据库中，详细代码如【示 例7-24】所示。

【示例7-24】

| (root@CentOS BBS]# cat -n users一update一do.php |                                |
| ----------------------------------------------- | ------------------------------ |
| 1                                               | <?php                          |
| 2                                               | include一once("connect.php”)； |
| 3                                               | echo "<br>”；                  |
| 4                                               | //获取当前记录的ID             |
| 5                                               | $id-$_GET[HidM];               |
| 6                                               | //获取 uname                   |
| 7                                               | $uname=$_GET["unameH 3；       |
| 8                                               | if <$uname==’,n)               |

| 9                                           | {                                        |                        |                                                    |
| ------------------------------------------- | ---------------------------------------- | ---------------------- | -------------------------------------------------- |
| 10                                          | echo n用户名不能为空！"；                |                        |                                                    |
| 11                                          | }                                        |                        |                                                    |
| 12 :                                        | else                                     |                        |                                                    |
| 13 :                                        | ,{                                       | 曝纏擊l'馨誦:y:';, i • | 卜:"V、    ' 1 -i    i« *                          |
| 14                                          | //获取地址信息                           |                        |                                                    |
| 15                                          | $addresss=;$_GET [ "address'*            | i；                    |                                                    |
| 16                                          | $query=mysql_query(Mupdate BBS.users set |                        |                                                    |
| uname^* $uname *,address^* $address * where | 1(1^$ idM);                              |                        |                                                    |
| 17                                          | if($query)                               |                        | /» -'                                              |
| 18                                          | {                                        |                        |                                                    |
| 19                                          | echo ”记彔修改成功”；                    |                        |                                                    |
| 20                                          | }                                        |                        | ■ ■' ■.:，-二...，么-a.    石权、-人 vi'    ■ s! - |
| 21                                          | else                                     |                        |                                                    |
| 22                                          |                                          |                        |                                                    |
| 23                                          | echo "记录修改失败"；                    |                        |                                                    |
| 24                                          | }                                        |                        | ；J' "，：+:八''                                   |
| 25                                          | i'r-                                     |                        |                                                    |
| 26                                          | include (’’users一page .php”)；          |                        | 麵襲"i:，' "    K                                  |
| 27                                          | ?>                                       |                        |                                                    |

此页面如果更新记录成功，则显示“记录修改成功”；如果修改失败，则显示“记录修改 失败”，可根据此信息判断修改结果。

7.4.7删除记录

如果要删除数据库中的记录，首先获取当前记录的主键，然后在数据库中查找并删除。本 节代码在7.4.4节中的USerS_page.php基础上进行修改，主要是添加删除记录需要的超链接，超 链接应该将当前记录的主键传到更新页面users_delete.php。完整的代码如【示例7-25】所示。

【示例7-25】

| (rootGCentOS BBS]# cat -n users一page.php |                                                              |
| ----------------------------------------- | ------------------------------------------------------------ |
| 少:丄                                     | <?php                                                        |
| 2                                         | include一once("connect.php”；                                |
| 34                                        |                                                              |
| 5                                         | <html>    ,    ?    •                                        |
| 6                                         | -<head>                                                      |
| 7                                         | <meta http-equiv~MContent-Typev content-**text/html; charset^UTF-81' /> |
| 8                                         | ＜七丄1：16〉用户信息查询＜/1：；11：16：＞                  |
| 9                                         | clink rel=n stylesheet0 type 二”text/cssn bre f~,f css/main .css M /> |
| 10                                        | <script language=njavascript°>                               |
| 11                                        | function check(form){    ,                                   |
| 12                                        | if (form.txt_keyword.value—M,*) {                            |
| 變籌:    13                               | alert 査询关键字不能为空！ ”〉；                             |

| 1415                              | form.txt keyword.focus (); return false;     |                                 |                                  |
| --------------------------------- | -------------------------------------------- | ------------------------------- | -------------------------------- |
| 1617                              | • - - ' .' . . ■ ■ ■form.submit();           | •. /■ ■■ '■/                    |                                  |
| 18                                | I                                            |                                 |                                  |
| 19                                | function deleteCheck(){                      |                                 | ■    '    I    、    .鱗鄕fX物機 |
| 20                                | if《confirmC•真的要删除当前记录吗？    }     | y    A    i .                   |                                  |
| 21                                | {                                            |                                 |                                  |
| 22                                | return true;                                 | &■, -    "Mi                    |                                  |
| 23                                | 1                                            |                                 | ■X♦究，以                        |
| 24                                | else                                         |                                 |                                  |
| 25                                | {                                            |                                 |                                  |
| 26                                | return false;                                |                                 |                                  |
| 27                                | }                                            |                                 |                                  |
| 28                                |                                              |                                 |                                  |
| 29                                | )                                            |                                 |                                  |
| 30                                | 〈/script〉                                  |                                 |                                  |
| 31                                | </head>                                      |                                 |                                  |
| 32                                |                                              |                                 |                                  |
| 33                                | <body>                                       |                                 |                                  |
| 34                                | <table>                                      |                                 |                                  |
| 35                                | <tr>                                         |                                 |                                  |
| 36                                | <td height=*,30n align=ncenterH>             |                                 | ■艮婚尋说赚祕?WW                 |
| 37                                | <form name=Hformln method^’iget" action®*'n> |                                 |                                  |
| 38                                | 査洵关键字&nfc)sp;                           |                                 |                                  |
| 39                                | <input namewtxt一keyword’                    | type二,’text”                   |                                  |
| id=Mtxt一keyword" size="40">      |                                              |                                 |                                  |
| 40                                | <input type="submit" name-nSubmitH value-    | "搜索"                          |                                  |
| onClick=nreturn check (form)，，> |                                              |                                 |                                  |
| 41                                | </form>                                      |                                 |                                  |
| 42                                | </td>                                        | . -■ . . ■ - ； ■               |                                  |
| 43                                | </tr>                                        |                                 |                                  |
| 44                                | </table>                                     |                                 |                                  |
| 45                                | <table algin="center’’>                      |                                 |                                  |
| 46                                | <thead>                                      |                                 |                                  |
| 47                                | <tr>                                         | -                               |                                  |
| 48                                | <td colspan=n60,'xspan class="open’          | •></span〉用户信息査询</td〉    |                                  |
| 49                                | </tr>                                        | ,.IVnS-i' '    Tt^SlJhi    Jil' |                                  |
| 50                                | </thead>                                     |                                 |                                  |
| 51                                | <tbody>                                      |                                 |                                  |
| 52                                | <tr>                                         |                                 |                                  |
| 53                                | <td>用户 ID</td>                             |                                 |                                  |
| 54                                | <td>用户名</td>                              |                                 |                                  |
| 55                                | <td> 地址 </td>                              |                                 |                                  |
| 56                                | <td colspan=2>操作</td>                      |                                 |                                  |

| 57          | </tr>                                         |
| ----------- | --------------------------------------------- |
| 58          | <?php                                         |
| 59          | if($ txt_keyword-=nul1)                       |
| 60          | {                                             |
| 61          | $txt__keyword-$_GET [ ntxt__keywordH ];       |
| 62          | }                                             |
| 63          | $page=$一GET["page"];                         |
| 64          |                                               |
| 65          | if ($page==nn)                                |
| 66          | {                                             |
| 67          | $page=l;                                      |
| 68          |                                               |
| 69          | $page__size=s3;                               |
| 70          | $query~**select count (*) as c from BBS.users |
| .uname like | '%$txt_keyword% * n;                          |
| 71          | $rs_count-mysql_query($query);                |
| 72          | $total一count=mysql_result($rs一count,0,"c"); |
| 73          | $page_total=ceil ($total_count/$page__size);  |
| 74          | $offset=($page-l)*$page_size;                 |
| 75          |                                               |

a where



![img](11 CentOS7fbdfa1060ed0f49e18-156.jpg)



76    $rs=mysql_query(nselect id,unameraddress from BBS.users a where a.uname like 1 %$txt_keyword% * order by id asc limit $of fset, $page_size");

77    $count = mysql_num_rows ( $rs);

78    $ j=$of f set+1'•

79    $i=l;

80    if($count==0)

81 {

82    echo "<tr><td colspan=100 align=center><font color=red size==3》S有查询到符合条件的记录！ </td></tr>";

83    exit;

84    }

85

86

87

88

89

90

91

92



while ( $row - mysql__fetch_row ( $rs )) {

$num=0;

?>

<tr>

<tdx?php

<tdx?php

<td><?php



echo $j++ ?></td> echo $row[1]; ?></td> echo $row[2]; ?></td>

93    <td><a href=users_update.php?page=<?php echo

$page; ?>&id=<?php echo $row [0}; ?>>修改</a>&nbsp;

94    <a href=users_delete,php?page=<?php echo

$page;?>&id=<?php echo $row[0]; ?> onClick=Mreturn deleteCheck()

95    </td>

96    </tr>

| 97                                            | <?php                                                        | 麵義醸議戀麵麵灘醸sBO醸讎霧00獅繼繼 |
| --------------------------------------------- | ------------------------------------------------------------ | ----------------------------------- |
| 98                                            | }                                                            |                                     |
| 99                                            | ?>                                                           |                                     |
| 100                                           | </tbody>                                                     |                                     |
| 101                                           | </table〉                                                    |                                     |
| 102                                           |                                                              |                                     |
| 103                                           | <table>                                                      |                                     |
| 104                                           | <tr>                                                         |                                     |
| 105                                           | <td>当前第<?php echo $page;?〉页，总共<?php echo $page_total; ?> |                                     |
| 页，总记录<?php echo $total_count; ?〉条</td> |                                                              |                                     |
| 106                                           | <td>                                                         |                                     |
| 107                                           | <?php                                                        |                                     |
| 108                                           | if($page!=1)                                                 |                                     |
| 109                                           | {                                                            |                                     |
| 110                                           | echo *’<a                                                    |                                     |
| href=users                                    | •page.php?page~l&txt_keyword=$txt                            | _keyword>首页</a>&nbsp; ;           |
| 111                                           | echo "<a                                                     |                                     |
| href=users                                    | _page.php?page='*. ($page-l)                                 | keyword=$txt_key word〉上一页       |
| </a>&nbsp;                                    | r .                                                          |                                     |
| 112                                           | }                                                            |                                     |
| 113                                           | if($page<$page_total)                                        |                                     |
| 114                                           | {                                                            |                                     |
| 115                                           | echo ’’<a                                                    |                                     |
| href=users_page.php?page=n.($page+l)."&txt一  | keyword~$txt_keyword>"fr—                                    |                                     |
| </a>&nbsp;                                    | H .                                                          |                                     |
| 116                                           | echo **<a                                                    |                                     |
| href^users                                    | _page .php?page=H . $page_total. " &txt_keyword!=$txt_keyword>^M |                                     |
| </a>&nbsp;                                    | ii * r                                                       |                                     |
| 117                                           | }                                                            |                                     |
| 118                                           | ?>                                                           |                                     |
| 119                                           | </tr>                                                        |                                     |
| 120                                           | </table>                                                     |                                     |

运行效果如图7.9所示，地址栏显示了超链接的内容。

用户信息查询

| 序号    用户名       | 地址       | 揀作                 |
| -------------------- | ---------- | -------------------- |
| 4    李泮            | 北京       | 修改删除             |
| 5    梅霞            | 深圳       | 修改删除             |
| 6    小崔            | 内蒙古     | 修改删除             |
| 当前第2页，总共4页， | 总记录11条 | 首页上一页下一页尾页 |

hfclp: /細w. test. com/BBS/users d^ete. p^p?id =8

图7.9添加“删除超链接”

经过以上步骤删除记录需要的主键ID己经加入到超链接中，点击对应的超链接后，会弹 出“确认”对话框要用户确认是否真的删除，如图7.10所示。

![img](11 CentOS7fbdfa1060ed0f49e18-157.jpg)



图7.10记录删除确认对话框

当单击“取消”按钮时，将不进行记录删除操作，如单击“确定”按钮，则进行记录的删 除操作，删除相关的代码如【示例7-26】所示。

【示例7-26】

[rootSCentOS BBS]# cat -n users_delete,php <?php

include一once("connect.php"}; echo ”<br〉’’；

//获取当前记录的ID $id-$_GET[»idnj;

/ /获取 uname

$uname=$__GET [ "uname” ]; if ( $id —"")



1

2

3    ;

4

5

6

7    \

8 9

10 11 12 13 :14 :

15    :

16 n 18

19

20 21 22

23

24

25



echo ”ID不能为空！ ”；



else



,Mr ' r. , -    '    1    'l5'*    f；-.'

\- ,,■ ‘



$query=mysql_query(ndelete from BBS.users where id=$idH); if($query)

s

I



echo "记录删除成功";



}

else : {



echo "记录删除失败";



include(Musers_page.phpn); ?>



###### 7.5小结

LNMP (Linux+Nginx+MySQL+PHP)是一种应用比较广泛的Web服务架构。本章首先介 绍LNMP涉及的相关软件的安装与管理，然后介绍了 Nginx的虚拟主机配置，接着介绍Nginx 和PHP的两种集成方式，最后通过实现数据库表的增、删、改、查功能，介绍了 Nginx+PHP+MySQL 的应用。
