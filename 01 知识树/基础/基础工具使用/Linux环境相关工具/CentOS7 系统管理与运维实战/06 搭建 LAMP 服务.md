###### Z aec

‘ n n

wMIr

H 10

，搭蓮LAMP明异►

4'• X*.-'' .v * ■^'¥^-''^'''f .'«'-'，,J，^( «'■ <'s<-    ' '’iV :' "■. ' ''' .'' ,'\ '. ' ' ： .•'

使用LAMP (Linux + Apache + MySQL + PHP)来搭建Web应用尤其是电子商务已经是一 种流行的方式，因为全部是开源和免费的软件，所以成本非常低廉。本章主要介绍平台的搭建， 在搭建平台时，也可以直接使用RPM包来安装，但是由于使用RPM包依赖特定的平台，可 以使用更通用的方法直接从源代码来安装。

本章首先介绍LAMP密切相关的HTTP协议，然后介绍Apache服务的安装与配置和PHP 的安装与配置，最后给出了 MySQL的一些日常维护方法。

本章主要涉及的知识点有：

•    Apache的安装与配置

•    PHP的安装与配置

•    LAMP应用

6.1    Apache HTTP服务安装与配置

Apache是世界上应用最广泛的Web服务器之一，尤其是现在，使用LAMP (Linux+Apache+MySQL+PHP)来搭建Web应用已经是一种流行的方式，因此，掌握Apache 的配置是系统工程师必备的技能之一。本节主要介绍Apache的安装与配置。

6.1.1    HTTP协议简介

超文本传送协议(Hypertext Transfer Protocol, HTTP)是因特网(World Wide Web, WWW，也 简称为Web)的基础。HTTP服务器与HTTP客户机(通常为网页浏览器)之间的会话如图6.1所示。

图6.1 HTTP服务端与HTTP客户端交互过程

下面对这一交互过程进行详细分析。

1.客户贴服务器建立连接

首先客户端与服务器建立连接，就是SOCKET连接，因此要指定机器名称、资源名称和 端口号，可以通过URL来提供这些信息。URL的格式如【示例6-1】所示。

【示例6-1】

HTTP://＜IP地址＞/[端口号｝/[路径1 [ ＜其他信息＞]

<http://dev.mysql.com/get/Downloads/MySQL-5.l/mysql~5.1.49.tar.gz>

\2.    客户向服务器提出请求

请求信息包括希望返回的文件名和客户机信息。客户机信息以请求头发送给服务器，请求 头包括HTTP方法和头字段。

HTTP方法常用的有GET、HEAD、POST,头字段主要包含以下字段。

•    DATE:请求发送的日期和时间。

•    PARGMA:用于向服务器传输与实现无关的信息。这个字段还用于告诉代理服务器， 要从实际服务器而不是从高速缓存获取资源。

•    FORWARDED:可以用来追踪机器之间，而不是客户机和服务器的消息。这个字段 可以用来追踪在代理服务器之间的传递路由。

•    MESSAGE_ID:用于唯一地标识消息。

•    ACCEPT:通知服务器客户所能接受的数据类型和尺寸。

•    FROM:当客户应用程序希望服务器提供有关电子邮件地址时使用。

•    IF-MODEFIED-SINCE:如果所请求的文档自从所指定的日期以来没有发生变化，则 服务器应不发送该对象。如果所发送的日期格式不合法，或晚于服务器的日期，服务 器会忽略该字段。

•    BEFERRER:向服务器进行资源请求用到的对象。

•    MIME-VERTION:用于处理不同类型文件的MIME协议版本号。

•    USER-AGENT:有关发出请求的客户信息。

\3.    服务器对请求做出应答

服务器收到一个请求，就会立刻解释请求中所用到的方法，并开始处理应答。服务器的应 答消息也包含头字段形式的报文信息。状态码是个3位数字码，主要分为4类。

•    以2开头，表示请求被成功处理

•    以3开头，表示请求被重定向

•    以4’开头，表示客户的请求有错

•    以5开头，表示服务器不能满足请求

响应报文除了返回状态行，还向客户返回几个头字段，如以下字段：

•    DATE:服务器的时间

•    LAST-MODIFIED:网页最后被修改的时间

•    SERVER:服务器信息

.CONTENT TYPE:数据类型

•    RETRY AFTER:服务器太忙时返回这个字段

4.关闭客户与服务器之间的连接

此步主要关闭客户端与服务器的连接，详细过程请参考TCP/IP协议的关闭过程。

6.1.2 Apache服务的安装、配置与启动

Apache由于其跨平台和安全性被广泛使用，Apache的特点是简单、速度快、性能稳定， 并可做代理服务器来使用。可以支持SSL技术，并且支持多个虚拟主机，是作为Web服务的 优先选择。

1.编译安装

本书主要以httpd-2.4.12.tar.gz源码安装Apache HTTP服务为例说明其安装过程。如果系 统需要使用https协议来进行访问，需要Apache支持SSL,因此，在开始安装Apache软件之 前，首先要安装OpenSSL，其源码可以在http://www.叩enssl.org/下载。安装OpenSSL的步骤 如【示例6-2】所示。

【示例6-2】

林在进行编译安装之前先安装编译环境＞■■■■■■■■■■■■■■ [root@CentOS soft]# yum install -y gcc #下载源码包

[rootQCentOS soft]# wget

[http://www.openssl.org/source/openssl-l](http://www.openssl.org/source/openssl-l%ef%bc%8c0.2-latest.tar.gz)[，0.2-latest.tar.gz](http://www.openssl.org/source/openssl-l%ef%bc%8c0.2-latest.tar.gz) #解压源码包

[root@CentOS soft]# tar xvf openssl-1.0»2-latest.tar.gz [rootQCentOS soft]# cd openssl-1.0.2a #配置编译选项

[root@CentOS openssl-1.0.2a]# ./config —prefix^/usr/local/ssl —shared #编译

[root@CentOS openssl-1.0.2a)# make [root^CentOS openssl-1,0.2a]# make install #将动态库路径加入系统路径中

[root@CentOS openssl-1.0.2a]# echo /usr/local/ssl/lib/ »/etc/Id.so.conf #加载动态库以便系统共享

[root@CentOS openssl-1.0.2a]# ldconfig

在安装完OpenSSL后，接下来就可以安装Apache 了，安装Apache的步骤如【示例6-3】 所示。

【示例6-3]

\#安装依赖软件包

[root@CentOS soft]# yum install -y apr apr~util pcre apr-devel apr-util-devel pcre-devel

\#下载并解压源码包

[root@CentOS soft]#

[rootGCentOS soft}# tar xvf httpd~2.4.12.tar.gz

[root@CentOS soft]# cd httpd-2.4.12/

\#配置编译选项

[root@CentOShttpd-2.4.12]# ./configure --prefix=/usr/local/apache2 --enable-so --enable-rewrite ——enable-ssl ——with-ssl=/usr/local/ssl ~with-mpm=prefork

\#编译

[root@CentOS httpd-2.4.12]# make

[root@CentOS httpd-2.4.12]# make install

Apache是模块化的服务器，核心服务器中只包含了功能最常用的模块，而扩展功能由其 他模块提供。设置过程中，可以指定包含哪些模块。Apache有两种使用模块的方法：

(1)    一是静态编译至二进制文件。如果操作系统支持动态共享对象(DSO),而且能为 autoconf所检测，则模块可以使用动态编译。DSO模块的存储是独立于核心的，可以被核心使 用由mod_So模块提供的运行时刻配置指令包含或排除。如果编译中包含有任何动态模块，则 mod_so模块会被自动包含进核心。如果希望核心能够装载DSO,而不实际编译任何动态模块， 需要明确指定--enable-so。在当前的失利中，核心模块功能我们全部启用。

(2)    二是需要启用SSL加密和mod_rewrite,并且采用动态编译模式以便后续可以动态 添加模块而不重新编译Apache,因此需要启用mod_so。

在上面的示例中，还有一个重要的选项with-mpm，这个选项用来指定httpd的工作模式。 常见的httpd工作模式有两种prefork和worker：

•    prefork:这是之前2.2版中默认的工作模式。这种工作模式下会有许多子进程，每个 子进程只有一个线程，同一时间每个进程都只处理一个请求。这种工作模式一般用来 避免线程兼容性问题，Unix系统中多采用此种方式。这种工作模式的优点是处理效 率高，稳定性好，但内存使用量比较大。

•    worker: worker工作模式与prefork不同，worker也会有许多子进程，但每个子进程 有多个线程，同一时间每个线程只处理一个请求。这种工作模式的优点是内存使用量 小，由于一个线程崩溃会导致整个进程崩溃，因此其稳定性相对不足。

除了以上两种常见的工作模式之外，还有一种名为Event的工作模式，这种模式可以用来

处理更高的负载，但使用这种工作模式的网站较少，此处不作讨论。

基于上面的分析，配置编译选项时，推荐使用以下选项，如【示例6-4】所示。

【示例6-4】

[root@CentOS httpd-2.4.12]# ./configure 一prefix=/usr/local/apache2 --enable-so --enable-rewrite --enable-ssl --with-ssl^Zusr/local/ssl --with-mpm=prefork

[root@CentOS httpd-2.4.12]# make

[root包CentOS httpd-2.4.12]# make install

由于每个项目及网站的情况不同，如果还需要支持其他的模块，可以在编译时使用相应的 选项。

2.主要目录

经过上面的过程Apache已经安装完毕，安装目录位于/usr/local/apache2目录下。主要的 目录说明如表6.1所示。

表6.1 Apache目录说明

| 参数                       | 说明               |
| -------------------------- | ------------------ |
| /usr/local/apache2Zbin     | Apache bin文件位置 |
| /usr/local/apache2/modules | Apache需要的模块   |
| /usr/local/apache2/logs    | Apache log文件位置 |
| /usr/local/apache2/htdocs  | Apache资源位置     |
| /usr/local/apache2/conf    | Apache配置文件     |

3.配置文件

Apache主配置文件位于conf目录中，名为httpd.conf。httpd.conf包含丰富的选项配置供 用户选择，下面是一些主要配置项的含义说明。

【示例6-5】

\#设置服务器的基础罔录，默认为Apache安装目录 ServerRoot n/usr/local/apache2 H #设置脤务器监听的IP和端口 Listen 80

\#设置管理员邮件地址 ServerAdmin [root@test.com](mailto:root@test.com) #设置服务器用于辨识自己的主机名和端口号 ServerName [www.test.com:80](http://www.test.com:80)

.#设置动态加载的DSO模块 #不同版本可能此处模块有所不同 #认证核心模块

LoadModule authn_core一 module modules/mod一authn一core•s

\#基于主机的认证（通常是ip地址或域名）

\#形式认证

\#LoadModule auth一form 一 module modules/mod一auth一form.so #如需在未正确配置认证模块b情况下简单拒绝一切认ii信息@启用此模块 LoadModule authn_default_module modules/niod__authn_default. so M七模块提供基于主机3、IP地i、请求特征的访问控制，illowfDeny指令需要，推荐加载。 LoadModule authz一host一 module modules/mod_authz_host.so #如需使用纯文本文件三组提石授权支持则启用此模块~

LoadModule authz_groupfile_module modules/mod_authz_groupfile.so #如需提供基于每个用 > 的授权支持启用此模块

LoadModule authz__user__module modules/mod一authz一user. so #如需使用DBM文件为组提供权支持则启用此模块

LoadModule authz dbm module modules/mod authz dbm.so — —•    —■    ~

\#如需基于文件的所有者进行授权则启用此模块

LoadModule authz_owner_module modules/mod_authz_owner.so

\#如需提供基本的http认证则启用此模块，此模块至少i要同时ira载一个认证支持模块和一个授权支持

模块

LoadModule auth basic module modules/mod auth basic.so

\#如需提供HTTPMDS摘要认证则启用此模块，此模块至少需要同时加载-个认证支持模块和一个授权支 持模块

LoadModule auth_digest一module modules Zmod__auth_digest. so #此模块可用于限制i单提交£或

\#LoadModule allowmethods module modules/mod allowmethods.so 一 —

\#共享对象缓存，这是一个HTTP缓存过滤器的基础

\#LoadModule cache_socache_module modulesZmod_cache__socache.so #下面这几个是提供不的共享对i缓存的模块

\#LoadModule socache shmcb nodule modules/mod socache shmcb,so

\#LoadModule socache dbm module modules/mod socache dbm.so —-    -*• ■    ■—

\#LoadModule socache_memcache_module modules/mod 一socache一memcache.so #httpd运行时的配置宏£件支持

\#LoadModule macro__module modules/mod一macro. so #此模块提供文件描述#缓存支持，从而提髙Apache性能，推荐加载，但请小心使用 LoadModule file一cache一module modules/mod一file一cache.so #此模块提供基于URI键的内容动态缓存从而提高Apache性jg,必须与

mod_disk_cache/mod__mem_cache 同时使用，推荐加载

LoadModule cache一 module modules/mod一cache.so

\#此模块为mod_csche提供基于磁盘的缓存管理，推荐加载

LoadModule disk一cache_module modules/mod_cache_disk.so

\#此模块为mo^cache提供i于内存的缓存管理，推i加载

LoadModule mem cache module modules/mod mem cache.so —— —

\#如需管理SQL数据库连接，为需要数据库功能的模块提供支持则启用此模块（推荐）

\#LoadModule ratelimit_module modules/mod__ratelimit.so #用于设置请求超时和最小数速度

LoadModule reqtimeout一 module modules/mod_reqtimeout.so #用来处理HTTP请求

\#LoadModule request一module modules/mod 一request.so #用来执行搜索和替换操作h模块    _

\#LoadModule substitute_module modules/mod_substitute.so

\#使用sed来过滤清求和响应模块    _    -

♦LoadModule sed_module modules/mod_sed.so

\#此模块将所有I/O &作转储到错误日志中，导致在日志中写入及其海量的数据，只建议在发现问蟬 并进行调试时使用

LoadModule dumpio_modu1e modules/mod_dumpio.so

\#如需使用外部程序作£过滤器，加载此模块（不i荐），否则注释掉    ’

LoadModule ext_filter_module modules/mod_ext_fliter.so

莽如需实现服务端包i文档（SSI）处理，加载此模块（~不推^）,否则注释掉

LoadModule include一 module modules/mod一include.so

\#如需根据上下文实际情i对输出过滤器进行动态既f置则启用此模块

LoadModule filter一 module modules/mod 一filter.so

\#如需服务器在将输出容发送到客户端以前进行i缩以节约带宽，加载此模块（推荐），否则注释掉 #LoadModule deflate一module modules/mod 一deflate.so #如需记录日志和定制日志i件格式，加载此模块（i荐），否则注释掉 #LoadModule log一config一module modules/mod一log一config.so #如需对每个请求的i入/输出&节数以及HTTP头进行tF志记则启用此模块 LoadModule logio一module modules/mod一logio.so

\#如果允许Apache修&或清除传送到CGI脚本SSI页面的坏境变量则启用此模块

LoadModule env module modules/mod env.so 一 一

\#如果允许通过配置文件控制http的I’Expires:"和”Cache-Control:"头内容，加载此模块（推荐）, 否则注释掉

LoadModule expires_module modules/mod_expires.so #如果允许通过配置文件&制任意的HTTP请求和应答头信息则启用此模块 LoadModule headers_module modules/mod_headers.so #如需实现RFC1U3规定^ident查找，加载此块（不推荐），否则注释掉 LoadModule ident_module modules/mod_ident.so

\#如需根据客户端请求字段设置环境变量则启€此模块

::囊I:翼變



1M



LoadModule setenvif module modules/mod setenvif.so

\#提供代粧持    "    "

\#LoadModule proxy一module modules/mod_proxy.so #下面几个是代理模块Hiod_prOxy的支持模块

\#LoadModule proxy_f tp_modu 1 e modules/mod_proxy_f tp, so #LoadModule proxy__http__module modules/mod_proxy_http. so #LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so #LoadModule proxy一scgi一 module modules/mod__proxy_scgi.so #此模块是mod proxy的扩展，提供Apache JServ Protocol支持，H在必要时加载 LoadModule proxy一ajp一module modules/mod一proxy一ajp«so #此模块是mocLproxy的扩展，提供负载均衡支持，只在必要时加载 LoadModule proxy一balancer_modul.e modules/mod_proxy_balancer.so 祐提供安全套接字层和&输层安全G议支持

\#LoadModule ssl一module modules/mod__ssl. so

\#如需根据文件扩展i决定应答的行为（处理器/过滤器）和内容（MIME类型/语言/字符集/编码）则扃 用此模块

LoadModule mime_module modules/mod_mime.so #如果允许Apache g供DAV协议支持则启用模块 LoadModule dav_module modules/mod_dav.so

此模块生成描述服i器状态的Web页面，只建议在追踪服务器性能和问题时加载 LoadModule status_module modules/mod_status.so #如需自动对目录中的&容生成列表则加载此模块，否则注释掉 LoadModule autoindex_module modules/mod_autoindex,so #如需服务器发送自己包含HTTP头内容的文件则启用it模块 LoadModule asis_module modules/mod_asis.so

\#如需生成Apache 置情况的Web页面，加此模块（会带来安全问题，不推荐），否则注释掉 LoadModule info_module modules/mod_info.so #如需在非线程型（prefork）上提供对CGI脚本执行的支持则启用此模块 LoadModule cgi_module modules/mod_cgi.so

此模块在线程型MP& （worker）上用一■个外部CGI守护进程执行CGI脚本，如果正在多线程模式下使 用CGI程序，推荐替换mOd_cgi加载，否则注释掉

LoacJModule cgi d module modules /mod cgid.so

此模块为mOd_dav访问服务器上的文件系统提供支持，如果加载rnod_dav,则也应加载此模块，否则 注释掉

LoadModule dav fs module modules/mod dav fs.so #如需提供大批量虚拟主机的动态配置支持则启用此模块 LoadModule vhost_alias_module modules/mod_vhost_alias.so

\#如需提供内容协商支g （从几+有效文档中选择一个最&配客户^要求的文档）.

加载此模块（推荐）



否则注释掉

'雲醸麵讀團鋒義議禱囊盡誦麵麵處襲塞顯灣議薰醸墓釀議翻，種.

\#如需指定目录索引文件以及为_目录提供"尾斜杠"重定向?加载此模块（推荐），

否则注释掉



LoadModule dir_mpdule rtodules /mod_di r .-So #知需处理服务器端像ffife射则启用此模块 S蠶|陶議麵鐘讀■讓覇糧蠢難讓續議覇麵續鐘醸讓晏義麵善誦麵簿酵，讀 #如需针对特定的媒体类型Is请求方法执行CGI脚本启用此模块

LoadModule actions一 module modules/mod 一actions.so

\#如果希望服务器自动纠i URL中的拼写错误，加i此模块（推荐），否则注释掉 LoadModule speling_module modules/爪od一speling.so

\#如果允许在URL中通过”/-username”形式从用户自己的主目录中提供页面则启用此模块

LoadModule userdir一 module modules/mod^userdir.so

拌此模块提供从文件系统不同部分到文档树的映射了和URL重定向，推荐加载

LoadModule alias一module modules/mod_alias.so

\#如需基于•一定规则实时重写URL请求，加载此&块（推荐），否则注释掉

LoadModule rewrite_module modules/mod_rewrite.so

\#仅当加载unixd模块才启用下面的设置项

<IfModule unixd__module>

\#设置子进程的用户^组•

User apache

Group apache    •

</IfModule>

\#设置Web文档根目录的默认属性

〈Directory />

AllowOverride None Require all denied

</Directory>

\#设置默认Web文档根目录

DocumentRoot n/usr/local/apache2/htdocsn

\#设置DocumentRoot指定目录的属性

〈Directory M/usr/local/apache2/htdocsn> Options Indexes FollowSymLinks AllowOverride None

Require all granted

</Directory>

\#设置默认目录资源列表文件

<IfModule dir_module>

DirectoryXndex index.html

</IfModule>

\#拒绝对.ht开头文件的访问，以保护.htaccess文件 〈Files n.htM>

Require all denied

</Files>

\#指定错误日志文件

ErrorLog "logs/error一log"

\#指定记录到错误日志的消i级别

LogLevel warn

\#当加载了 log_config模块时生效

![img](11 CentOS7fbdfa1060ed0f49e18-90.jpg)



<IfModule log_config_module>

\#定义访问日志的i式    -

LogFormat "%h %1 %t \"%r\" %>s %b \n%{Referer}i\" \n%{User-Agent}i\°H

combined

LogFormat n%h %1 %u %t \"%r\" %>s %bH common

<IfModule JLogio一module〉

LogFormat "%h %1 %u %t \"%r\" %>s %b \M%{Referer}i\n \*'%{User-Agent}i\n %I %0^ combinedio

</IfModule>

CustomLog °logs/access_logn common

</IfModule〉

\#设定默认CGI脚本目录及别名 <IfModule alias_module>

ScriptAlias /cgi-bin/ ,T/usr/local/apache2/cgi-bin/"

</IfModule>

\#设定默认CGI脚本目录的属性

〈Directory "/usr/local/apache2/cgi-bin"〉

AllowOverride None Options None Require all granted

〈/Directory〉

\#设定默认mime内容类型 DefaultType text/plain <IfModule mime一 module〉

\#WEB指定MIME类型映射文件

TypesConfig conf/mime.types

\#WEB增加.Z .tgz的类型映射

AddType application/x-compress .2 AddType application/x-gzip .gz .tgz

</IfModule〉

\#启用内存映射 EnableMMAP on

\#使用操作系统内核的sendfile支持来将文件发送到客户端 EnableSendfile on

\#指定多路处理模块(MPM)配置文件并将其附加到主配置文件

Include conf/extra/httpd-mpm.conf

\#指定多语言错误应答配置文件并将其附加到主配置文件

Include conf/extra/httpd-multilang-errordoc.conf

\#指定目录列表配置文件并将其附加到主配置文件

Include conf/extra/httpd-autoindex.conf

\#指定语言配置文件并将其附加到主配置文件

Include conf/extra/httpd-languages.conf

\#指定用户主目录配置文件并将其附加到主配置文件

Include conf/extra/httpd-userdir.conf

\#指定用于服务器信息和状态显示的配置文件并将其附加到主配置文件

Include conf/extra/httpd-info.conf

\#指定加载虚拟主机的配置文件

Include conf/extra/httpd-vhosts.conf

\#指定提供Apache文档访问的配置文件并将其附加到配置文件 Include conf/extra/httpd-manual.conf #指定DAV配置文件并将其附加到主配置文件 Include conf/extra/httpd-dav.conf 弁指定与Apache服务自身相关的配置文件并将其附加到主配置文件 Include conf/extra/httpd-default.conf #如果加载了 proxy^html相关模块，则将其配置文件附加到主配置文件 <IfModule proxy_html__rnodule>

Include conf/extra/proxy-html.conf </IfModule〉

\#SSL默认配置

<IfModule ssl module〉

—

SSLRandomSeed startup builtin SSLRandornSeed connect builtin

以上是配置文件httpd.conf中最主要的配置项及其说明，其中模块部分并未完全列举。要 查询各个模块的详细用法及说明，可以参考[http://httpd.apache.Org/docs/2.4/mod/](http://httpd.apache.Org/docs/2.4/mod/%e4%b8%ad%e7%9a%84%e7%9b%b8%e5%85%b3%e6%96%87%e6%a1%a3)[中的相关文档](http://httpd.apache.Org/docs/2.4/mod/%e4%b8%ad%e7%9a%84%e7%9b%b8%e5%85%b3%e6%96%87%e6%a1%a3) 了解。

修改加载的相关设置。

前面介绍到httpd的两种常见模式，在本例中还没有为工作模式相关的模块设置参数。在 配置文件httpd.conf中加入相关参数，设置prefork模块相关参数如下，这里重点说明各配置 项的意义。一个典型的profork模块参数如下所示：

<IfModule mpm prefork module〉

MaxRequestsPerChild 0

</IfModule〉

指令说明：

•    StartServers:设置服务器启动时建立的子进程数量。因为子进程数量动态地取决于负 载的轻重，所有一般没有必要调整这个参数。

•    MinSpareServers:设置空闲子进程的最小数量。所谓空闲子进程是指没有正在处理请 求的子进程。如果当前空闲子进程数少于MinSpareServers ,那么Apache将以最大 每秒一个的速度产生新的子进程.只有在非常繁忙的机器上才需要调整这个参数，'通 常不建议将此参数的值设置的太大，除非你的机器非常繁忙。

•    MaxSpareServers:设置空闲子进程的最大数量。如果当前有超过MaxSpareServers数 量的空闲子进程，那幺父进程将杀死多余的子进枚只有在非常繁忙的机器上才需要 调整这个参数，通常不建议将此参数设置的太大，除非你的机器非常繁忙。如果将该 指令的值设置为比MinSpareServers小，Apache将会自动将其修改成

“MinSpareServers+1”。

•    ServerLimit服务器允许配置的进程数上限，只有在你需要将MaxClients设置成高于 默认值256时才需要使用，要将此指令的值保持和MaxClients 一样。修改此指令的 值必须完全停止服务后再启动才能生效，以restart方式重启动将不会生效。

•    MaxClients:用于伺服客户端请求的最大请求数量（最大子进程数），任何超过 + MaxClients限制的请求都将进入等候队列。默认值是256,如果要提高这个值必须同

时提高ServerLimit的值笔者建议将初始值设为（以MB为单位的最大物理内存/2 ）, 然后根据负载情况进行动态调整。比如一台4GB内存的机器，那么初始值就是 4000/2=2000。

•    MaxRequestsPerChild;设置每个子进穩在其生存期内允许伺服的最大请求数量。到达 MaxRequestsPerChild 的限制后，子进程将会结束。如果 MaxRequestsPerChild 为"0"， 子进程将永远不会结束。将MaxRequestsPerChild设置成非零值有两个好处：可以防 止（偶然的）内存泄漏无限进行而耗尽内存；给进擇一个有限寿命，从而有助于当服 务器负载减轻时减少活动进程的数量。

目前大多数服务器都使用了 prefork模式，如果需要采用worker模式，其典型的参数如下 所示：

<IfModule mpm 一worker一module〉

| StartServers          | 5    |
| --------------------- | ---- |
| ServerLimit           | 20   |
| ThreadLimit           | 200  |
| MaxClients            | 4000 |
| MinSpareThreads       | 25   |
| MaxSpareThreads       | 250  |
| ThreadsPerChild       | 200  |
| MaxRequestsPerChild 0 |      |

</IfModule>



![img](11 CentOS7fbdfa1060ed0f49e18-92.jpg)



指令说明：

•    StartServers:设置服务器启动时建立的子进程数量。因为子进程数量动态地取决于负 载的轻重，所有一般没有必要调整这个参数。

•    ServerLimit:服务器允许配置的进程数上限。只有在你需要将MaxClients和 ThreadsPerChild设置成需要超过默认值16个子进程时才需要使用这个指令。不要将 该指令的值设置的比MaxClients和ThreadsPerChild需要的子进程数量高。修改此指 令的值必须完全停止服务后再启动才能生效，以restart方式重新启动将不会生效。

•    ThreadLimit:设置每个子进程可配置的线程数ThreadsPerChild上限，该指令的值应 当和ThreadsPerChild可能达到的最大值保持一致。修改此指令的值必须完全停止服 务后再启动才能生效，以restart方式重新启动将不会生效。

•    MaxClients:用于伺服客户端请求的最大接入请求数量(最大线程数)。任何超过 MaxClients限制的请求都将进入等候队列。默认值是400, 16 ( ServerLimit)乘以25 (ThreadsPerChild)的结果。因此要增加MaxClients时，你必须同时增加ServerLimit 的值。笔者建议将初始值设为以MB为单位的最大物理内存/2,然后根据负载情况进 行动态调整。比如一台4GB内存的机器，那么初始值就是4000/2=2000。

•    MinSpareThreads:最小空闲线程数，默认值是"75”。这个MPM将基于整个服务器 监视空闲线程数。如果服务器中总的空闲线程数太少，子进程将产生新的空闲线程。

•    MaxSpareThreads:设置最大空闲线程数。默认值是“250”。这个MPM将基于整个 服务器监视空闲线程故如果服务器中总的空闲线程数太多，子进程将杀死多余的空 闲线程。

MaxSpareThreads的取值范围是有限制的。Apache将按照如下限制自动修正你设置的值： worker 要求其大于等于 MinSpareThreads 加上 ThreadsPerChild 的和。

•    ThreadsPerChild:每个子进程建立的线程数。默认值是25。子进程在启动时建立这 些线程后就不再建立新的线程了。每个子进程所拥有的所有线程的总数要足够大，以 便可以处理可能的请求高峰。

•    MaxRequestsPerChild:设置每个子进程在其生存期内允许伺服的最大请求数量。

需要特别注意的是，配置文件中并没有关于prefork和worker的相关配置项，以上两段内 容需要手动添加，并且要按实际情况对以上参数进行调整。

4.判断使用何种工作模式

对于自己安装的httpd,我们可以通过参考编译时的参数判断使用的是何种工作模式，但 如果是别人编译安装的httpd可能就无法判断。这时可以使用以下命令判别：

【示例6-6]

[root@CentOS # /usr/local/apache2/bin/httpd -1 Compiled in modules: core.c

mod一so.c http_core.c prefork.c

从以上命令的输出可以判断出当前使用的是prefork工作模式。

1306.1.3 Apache基于IP的虚拟主机配置

Apache配置虚拟主机支持3种方式:基于IP的虚拟主机配置，基于端口的虚拟主机配置, 基于域名的虚拟主机配置。本节主要介绍基于IP的虚拟主机配置。

如果同一台服务器有多个IP,可以使用基于IP的虚拟主机配置，将不同的服务绑定在不 同的IP上。

(1)假设服务器有个IP地址为192.168.146.150,首先使用ifconfig在同一个网络接口上 绑定其他3个IP,如【示例6-7】所示。

【示例6-7】

[rootgCentOS -]# ifconfig eno33554984:1 192.168.146,151/24 up

[rootJCentOS -}# ifconfig eno33554984:2 192.168.146.152/24 up

[root@CentOS 〜ifconfig eno33554984:3 192.168.146.153/24 up

[rootSCentOS # ifconfig

eno33554984: flags=4163<UP,BROADCAST,RUNNING,MULTICAST mtu 1500

inet 192.168.146.150 netmask 255.255.255.0 broadcast 192.168.146.255 inet6 fe80::20c:29ff:feOb:780 prefixlen 64 scopeid 0x20<link> ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

RX packets 31507 bytes 15697744 (14.9 MiB)

RX errors 0 dropped 0 overruns 0 frame 0 TX packets 22513 bytes 4024816 (3.8 MiB)

TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eno33554984:l: flags=4163<UP,BROADCAST,RUNNING,MULTICAST〉 mtu 1500

inet 192.168.146.151 netmask 255.255.255.0 broadcast 192.168.146.255 ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

崎我採M•‘分，：    ■    vf? ■•    -

eno33554984:2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500

inet 192.168.146.152 netmask 255.255.255.0 broadcast 192.168.146,255 ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

eno33554984:3: flagsM163<UP, BROADCAST, RUNNING, MULTICAST〉mtu 1500

inet 192.168.146.153 netmask 255.255.255.0 broadcast 192.168.146.255

ether 00:0c:29:0b:07:80 txqueuelen 1000 (Ethernet)

. '. ：■ ■ ■ ■■ ■ ■■

Io: flags«73<UPfLOOPBACK,RUNNING> mtu 65536 inet 127.0.0.1 netmask 255.0.0.0 inet6 : : 1 prefixlen 128 scopeid 0xl0<host> loop txqueuelen 0 (Local Loopback)

RX packets 758 bytes 245409 (239.6 KiB)

RX errors 0 dropped 0 overruns 0 frame 0 TX packets 758 bytes 245409 (239.6 KiB)

TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

(2) 3个IP对应的域名如下，配置主机的host文件便于测试。

【示例6-8]

[root@CentOS conf]# cat /etc/hosts

127.0.0.1 CentOS localhost

192.168.146.151    [www.testl51.com](http://www.testl51.com)

192.168.146.152    [www.testl52.com](http://www.testl52.com)

192.168.146.153    [www.testl53.com](http://www.testl53.com)    ?    \

(3)建立虚拟主机存放网页的根目录，并创建首页文件index.html。

【示例6-9】

CrootQCentOS

[rootGCentOS

[root@CentOS

[rootQCentOS

[root@CentOS

[root@CentOS

[root@CentOS

[root@CentOS



-3 # mkdir /data/www

〜cd /data/www

www]# mkdir 151

www]# mkdir 152

www]# mkdir 153

www)# echo "192,168.146.151.. >151/index.html www]# echo ”192.168.146.152“ >152/index.html www)# echo "192.168.146.153° >153/index.html

(4)修改httpd.conf在文件末尾加入以下配置。

【示例6-10]

Listen 192.168.146.151:80 Listen 192.168.146.152:80 Listen 192.168.146.153:80

Include conf/vhost/*.conf

(5)编辑每个IP的配置文件。

【示例6-11】

[root@CentOS conf]# mkdir -p vhost [rootGCentOS conf]# cd vhost/

![img](11 CentOS7fbdfa1060ed0f49e18-93.jpg)



(root@Cent0S vhost)# cat [www.testl51.conf](http://www.testl51.conf) <VirtualHost 192.168.146.151:80>

ServerName [www.testl51.com](http://www.testl51.com) DocumentRoot /data/www/151 〈Directory M/data/www/151/n>

Options Indexes FollowSymLinks AllowOverride None Require all granted

〈/Directory〉

</VirtualHost>

[root@CentOS vhost]# cat [www.testl52.conf](http://www.testl52.conf) <VirtualHost 192.168.146.152.*80>

ServerName [www.testl52.com](http://www.testl52.com) DocumentRoot /data/www/152 〈Directory "/data/www/152/"〉

Options Indexes FollowSymLinks AllowOverride None Require all granted </Directory>

</VirtualHost>

[root@CentOS vhost]# cat [www.testl53.conf](http://www.testl53.conf) <VirtualHost 192.168.146.153:80>

ServerName [www.testl53.com](http://www.testl53.com) DocumentRoot /data/www/153 〈Directory "/data/www/153/"〉

Options Indexes FollowSymLinks AllowOverride None Require all granted

</Directory>

</VirtualHost>

[root@CentOS vhost]# cat /data/www/151/index.html

192.168.3.101

[root@CentOS vhost]# cat /data/www/152/index.html

192.168.3.102

[rootOCentOS vhost]# cat /data/www/153/index.html

192.168.3.103

/戀:::：:

■蠶鑿If醐戀

誦画国

議議誦議醐

謬讓■

霧

llililllillll

纖释灘辦 ，：:'d

霸鑿謹議攤議儀誦 :薄纖S驟讎;囊.遷曇s

:斤聽

麵議議圓藝誦變

.■ ■ - ■. - --



難,

震画



(6)配置完以后可以启动Apache服务并进行测试。

【示例6-12]

\#检査配置文件是否正确

[root@CentOS conf]# /usr/local/apache2/bin/apachectl -1 Syntax OK #启动httpd

[rootQCentOS conf】# /usr/local/apache2/foin/apachectl start #检查虚拟主机是否已经运行

[root@CentOS conf]# curl <http://www.testl51.com>

192.168.146.151

[rootGCentOS conf]# curl <http://www.testl52.com>

192.168.146.152

[rootQCentOS conf 3 # curl <http://www.testl53.com>

192.168.146.153

6.1.4 Apache基于端口的虚拟主机配置

如一台服务器只有一个ip或需要通过不同的端口访问不同的虚拟主机，可以使用基于端 口的虚拟主机配置。

(1)假设服务器有个IP地址为192.168.146.154,如【示例6-13】所示。

【示例6-13]

[root@CentOS conf]# ifconfig eno33554984:4 192.168.146.154/24 up [root@CentOS conf]# ifconfig eno33554984:4

eno33554984:4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500

inet 192.168.146.154 netmask 255.255.255.0 broadcast 192.168.146.255 ether 00:0c:29:Ob:07:80 txqueuelen 1000 (Ethernet)

(2)需要配置的虚拟主机分别为7081、8081和9081，配置主机的host文件便于测试。

【示例6-14】

[root@CentOS conf]# cat /etc/hostsIgrep 192.168.146.154

192.168.146.154 [www.testl54.com](http://www.testl54.com)

(3)建立虚拟主机存放网页的根目录，并创建首页文件index.html。

【示例6-15]

[root@CentOS [root@CentOS [rootQCentOS [root@CentOS [root@CentOS [root@CentOS [root轻CentOS (root@CentOS [root@CentOS [root@CentOS



conf]# cd /data/www/

www]# mkdir port

www]# cd port/

port]# Is

port]# mkdir 7081

port]# mkdir 8081

port]# mkdir 9081

\>7081/index.html >8081/index.html >9081/index.html



![img](11 CentOS7fbdfa1060ed0f49e18-94.jpg)



port3 # echo "port 7081"

port]# echo "port 8081”

port]# echo "port 9081”

(4)修改httpd.conf在文件末尾加入以下配置。 【示例6-16】

Listen 192.168.146.154:7081 Listen 192.168.146.154:8081 Listen 192.168.146.154:9081 #仍然需要保持以下配置项的存在 Include conf/vhost/*.conf

(5)编辑每个IP的配置文件。 【示例6-17]

[root@CentOS vhost]# cat [www.testl54.7081.conf](http://www.testl54.7081.conf)

![img](11 CentOS7fbdfa1060ed0f49e18-95.jpg)



<VirtualHost 192.168.146.154:7081>

ServerName [www.testl54.com](http://www.testl54.com) DocumentRoot /data/www/port/7081 〈Directory n/data/www/port/7081/M>

Options Indexes FollowSymLinks AllowOverride None Require all granted

</Directory>

</VirtualHost>

[root@CentOS vhost]# cat [www.test154.8081.conf](http://www.test154.8081.conf) <VirtualHost 192.168.146.154:8081>

ServerName [www.testl54.com](http://www.testl54.com) DocumentRoot /data/www/port/8081 <Directory ’’/data/www/port/ 8081/">

Options Indexes FollowSymLinks AllowOverride None Require all granted

</Directory>

</VirtualHost>

[root@CentOS vhost]# cat [www.test154.9081.conf](http://www.test154.9081.conf) <VirtualHost 192.168.146.154:9081>

ServerName [www.test154.com](http://www.test154.com) DocumentRoot /data/www/port/9081

〈Directory n/data/www/port/9081/*'>

Options Indexes FollowSymLinks AllowOverride None

Require all granted 〈/Directory〉

</VirtualHost>

![img](11 CentOS7fbdfa1060ed0f49e18-96.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-97.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-98.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-99.jpg)



(6)配置完以后可以启动Apache服务并进行测试。

【示例6-18】

\#检査配置文件格式是否正确

[root@CentOS vhost]# /usr/local/apache2/bin/apachectl -t

Syntax OK

\#启动httpd并验证结果 [rootGCentOS vhost]# [root@CentOS vhost]# port 7081

[root@CentOS vhost]# port 8081

[root@CentOS vhost)# port 9081



/usr/local/apache?/bin/apachectl start curl <http://www.test154.com:7081>

curl http://www.testl54»com:8081

curl <http://www.test154.com:9081>

![img](11 CentOS7fbdfa1060ed0f49e18-100.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-101.jpg)



6.1.5 Apache基于域名的虚拟主机配置

使用基于域名的虚拟主机配置是比较流行的方式，可以在同一个ip上配置多个域名并且 都通过80端口访问。

(1)假设服务器有个IP地址为192.168.3.105，如【示例6-19】所示。

【示例6-19】

[root@CentOS -]# ifconfig eno33554984:5 192.168.146.155/24 up

[root@CentOS # ifconfig eno33554984:5

eno33554984:5: flags=4163<UPZ BROADCAST,RUNNING,MULTICAST> mtu 1500

inet 192.168.146.155 netmask 255.255.255.0 broadcast 192.168.146.255 ether 00:0c:29:Ob:07:80 txqueuelen 1000 (Ethernet)

(2) 192.168.3.105对应的域名如下，配置主机的host文件便于测试。

【示例6-20】

[root@CentOS conf]# cat /etc/hostsIgrep 192.168.146.155

192.168.146.155 [www.oa.com](http://www.oa.com)

192.168.146.155 [www.bbs.com](http://www.bbs.com)

192.168.146.155 [www.test.com](http://www.test.com)

(3)建立虚拟主机存放网页的根目录，并创建首页文件index.html。

| 【示例6-21】 |         |
| ------------ | ------- |
| frootQCentOS | ~] # c< |
| [root@CentOS | WWW ] # |
| [root@CentOS | WWW) #  |
| [rootQCentOS | www] #  |
| [root@CentOS | WWW] #  |
| [root@CentOS | WWW] #  |
| (root@CentOS | WWW] #  |



[www.test.com](http://www.test.com)



(4)修改httpd.conf在文件末尾加入以下配置。

【示例6-22】

Listen 192.168.3.105:80

\#由于每个域名的配置文件通过以下语句加栽，因此保留以下配置项 Include conf/vhost/*.conf

(5)编辑每个域名的配置文件。

【示例6-23]

[root@CentQS vhost]# cat [www.oa.com.conf](http://www.oa.com.conf) <VirtualHost 192.168.146.155:80>

ServerName [www.oa.com](http://www.oa.com)

DocumentRoot [/data/www/www.oa.com](file:///data/www/www.oa.com) 〈Directory ’’/data/www/www. oa. com/ "〉

Options Indexes FollowSymLinks AllowOverride None Require all granted

〈/Directory〉

</VirtualHost>

[root@CentOS vhost]# cat [www.bbs.com.conf](http://www.bbs.com.conf)

<VirtualHost 192.168.146.155:80>

ServerName [www.bbs.com](http://www.bbs.com) DocumentRoot [/data/www/www.bbs.com](file:///data/www/www.bbs.com) 〈Directory n/data/www/www.bbs.com/">

Options Indexes FollowSymLinks AllowOverride None Require all granted

</Directory>

'mV::



![img](11 CentOS7fbdfa1060ed0f49e18-102.jpg)



..■ • > -

覇霧



</VirtualHost>

[root@CentOS vhost]# cat [www.test.com.conf](http://www.test.com.conf)

<VirtualHost 192.168.146.155:80〉

ServerName [www.test.com](http://www.test.com) DocumentRoot [/data/www/www.test.com](file:///data/www/www.test.com) 〈Directory "[/data/www/www.test.com/n](file:///data/www/www.test.com/n)>

Options Indexes FollowSymLinks AllowOverride None Require all granted

</Directory>

</VirtualHost>

[root@CentOS vhost]# cat /data/www/www•oa.com/index.html [www.oa.com](http://www.oa.com)

[root@CentOS vhost]# cat /data/www/[www.test.com/index.htmlwww.test.com](http://www.test.com/index.htmlwww.test.com)

[rootQCentOS vhost]# cat /data/www/[www.bbs.com/index.htmlwww.bbs.com](http://www.bbs.com/index.htmlwww.bbs.com)

(6)配置完以后可以启动Apache服务并进行测试。在浏览器测试是同样的效果。

【示例6-24】

\#检査配置文件格式是否正确

[root@CentOS vhost]番 /usr/local/apache2/bin/apachectl ~t Syntax OK #启动httpd

[root@CentOS vhost]# /usr/local/apache2/bin/apachectl -k start [root@CentOS vhost3 # curl [www.oa.com](http://www.oa.com) www,oa.com

[root@CentOS vhost]# curl [www.bbs.com](http://www.bbs.com)

[www.bbs.com](http://www.bbs.com)

[rootQCentOS vhost]# curl [www.test.com](http://www.test.com)

[www.test.com](http://www.test.com)

如果需要在现有的Web服务器上增加虚拟主机，通常建议像上面那样单独将虚拟主机的 配置文件写在一个专门的虚拟主机配置文件中，然后在httpd.conf中加载，以免将httpd.conf 弄的杂乱无章。在虚拟主机配置文件中，必须为现存的主机建造一个＜VirtualHost＞定义块。在 ＜VirtualHoSt＞指令后面，即可以使用一个固定的IP地址，也可以使用“*”号代表所有监听地 址。之后需要配置虚拟主机使用的域名，主目录位置等信息。

至此3种虚拟主机配置方法介绍完毕，有关配置文件的其他选项可以参考相关资料或 Apache的帮助手册。

6.1.6 Apache安全控制与认证

Apache提供了多种安全控制手段，包括设置Web访问控制、用户登录密码认证及.htaccess 文件等。通过这些技术手段，可以进一步提升Apache服务器的安全级别，减少服务器受攻击 或数据被窃取的风险。

\1. Apache安全控制

要进行Apache的访问控制首先要了解Apache的虚拟目录。虚拟目录可以用指定的指令 设置，设置虚拟目录的好处在于便于访问之外，还可以增强安全性，类似软链接的概念，客户 端并不知道文件的实际路径。虚拟目录的格式如【示例6-25】所示。

【示例6-25】

〈Diretory目录的路径〉

目录相关的配置参数和指令

＜/Diretory＞

每个Diretory段以＜Diretory＞开始，&＜/Diretory＞结束，段作用于＜Diretory＞中指定的目录 及其里面的所有文件和子目录。在段中可以设置与目录相关的参数和指令，包括访问控制和认 证。.2.4版的Apache在访问控制方面与之前的2.2版有较大改变，2.4版中的控制指令主要使 用Require,控制方法主要有基于ip地址、域名、http方法、用户等。

(1)允许、拒绝所有访问指令 允许、拒绝所有访问：

\#允许所有访问

Require all granted

\#拒绝所有访问

Require all denied

(2)基于IP地址或网络 基于IP地址或网络访问：

\#仅允诗192,168.146.13访问 require ip 192.168.146.13

\#仅允许网络192.168.146.0/24访问 require ip 192.168.146.0/24 #仅允许网络192.168.146.0/24访问 require ip 192.168.146 # 禁止 192.168.146.2 访问 require not ip 192.168.146.2

![img](11 CentOS7fbdfa1060ed0f49e18-103.jpg)



(3)基于域名

通常不建议使用基于域名的访问控制，这主要是因为解析域名可能会导致访问速度变慢:

\#禁止 www. example. com 访问 Require not host www.example,com #允许 www.example.com访问 Require host [www.example.com](http://www.example.com)

【示例6-26】

\#综合示例，只允许192.1S8.146.134主机访问，拒绝其他所有主机访问 Require ip 192.168.146.134

当访问没有权限的地址时，会出现以下提示信息:

Forbidden

You don^ have permission to access /dir on this server

现在，我们使用6.1.3小节中虚拟IP虚拟主机的例子来模拟，其中主要的配置文件与之前 设置相同。

首先配置对应虚拟主机的配置文件，本例中仅使用配置文件www.testl51.conf，如【示例 6-27］所示。

【示例6-27】

| <VirtualHost 192.168.146.151:80>ServerName [www.testl51.com](http://www.testl51.com)DocumentRoot /data/www/151 〈Directory "/data/www/151/">Options Indexes FollowSymLinksAllowOverride NoneRequire ip 192.168.146.134 </Directory></VirtualHost> |                      |
| ------------------------------------------------------------ | -------------------- |
| 保存后重启Apache服务。在IP地址为192.168.146.134的机器上编辑/etc/hosts,加入以下内容： |                      |
| 192.168.146.151 [www.testl51.com](http://www.testl51.com)    |                      |
| 之后可以直接打开浏览器访问http://www.testl51.com进行测试，   | 可以看到只有指定的客 |

户端可以访问，访问控制的目的已经达到。

\2. Apache 认证

除了可以使用以上介绍的指令控制特定的目录访问之外，如服务器中有敏感信息需要授权 的用户才能访问，所以Apache提供了认证与授权机制，当用户访问使用此机制控制的目录时， 会提示用户输入用户名密码，只有输入正确用户名和密码的主机才可以正常访问该资源。

Apache的认证类型分为两种：基本（Basic）认证和摘要（Digest）认证两种。摘要认证 比基本认证更加安全，但是并非所有的浏览器都支持摘要认证，所以本节只针对基本认证进行 介绍。基本认证方式其实相当简单，当Web浏览器请求经此认证模式保护的URL时，将会出 现一个对话框，要求用户输入用户名和口令。用户输入后，传给Web服务器，Web服务器验 证它的正确性。如果正确，则返回页面；否则将返回401错误。

要使用用户认证，首先要创建保存用户名和口令的认证口令文件。在Apache中提供了 htpasswd命令用于创建和修改认证口令文件，该命令在＜八卩3（:1^安装目录＞/1^11目录下。关于 该命令完整的选项和参数说明可以通过直接运行htpasswd获取。

要在/usr/local/apache2/conf目录下创建一个名为users的认证口令文件，并在口令文件中 添加一个名为admin的用户，命令如下所示。

[rootOCentOS bin]# ./htpasswd -c /usr/local/apache2/conf/users.list admin

New password:

Re-type new password:

Adding password for user admin

命令运行后会提示用户输入admin用户的口令并再次确认。

【示例6-28】

认证口令文件创建后，如果还要再向文件里添加一个名为used的用户，可以执行如下命令。

【示例6-29】

[root@CentOS bin]# ./htpasswd /usr/local/apache2/conf/users.list userl New password:

Re-type new password:

Adding password for user userl

[root@CentOS bin]# cat /usr/local/apache2/conf/users.list

admin:$aprl$gQXd5FH8$7PVa6Envs4vDElYOcICTo.

userl:$apr1$d/Eyq.lQ$uoJ4 81VlQt zEoYGTBBkYGl

与/etc/shadow文件类似，认证口令文件中的每一行为一个用户记录，每条记录包含用户 名和加密后的口令。

![img](11 CentOS7fbdfa1060ed0f49e18-104.jpg)



htpasswd命令没有提供删除用户的选项，如果要删除用户，直接通过文本编辑器打开认证 口令文件把指定的用户删除即可。

创建完认证口令文件后，还要对配置文件进行修改，用户认证是在httpd.conf配置文件中 的＜0^邮0^＞段中进行设置的，其配置涉及的主要指令如下：

(1) AuthName 指令

AuthName指令设置了使用认证的域，此域会出现在显示给用户的密码提问对话框中，其 次也帮助客户端程序确定应该发送哪个密码。其指令格式如下：

AuthName 域名称

域名称没有特别限制，用户可以根据自己的喜欢进行设置。

(2) AuthType 指令

AuthType指令主要用于选择一个目录的用户认证类型，目前只有两种认证方式可以选择， Basic和Digest分别代表基本认证和摘要认证，该指令格式如下：

AuthType Basic/Digest



![img](11 CentOS7fbdfa1060ed0f49e18-105.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-106.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-107.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-108.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-109.jpg)



(3) AuthUserFile 指令

AuthUserFile指令用于设定一个纯文本文件的名称，其中包含用于认证的用户名和密码的 列表，该指令格式如下：

AuthUserFile 文件名

(4) Require 指令

Require指令用于设置哪些认证用户允许访问指定的资源。这些限制由授权支持模块实现, 其格式有下面两种：

•    用户名：认证口令文件中的用户，可以指定一个或多个用户，设置后只有指定的用户 才能有权限进行访问。

•    valid-user:授权给认证口令文件中的所有用户。

现在假设网站管理员希望对bm目录做进一步地控制配置该目录只有经过验证的admin 用户能够访问，用户口令存放在users.list 口令认证文件中。要实现这样的效果，需要把 www.testl51.conf配置文件中的配置信息替换为下面的内容，如【示例6-30】所示。

【示例6-30】

\#配置虚拟主机

<VirtualHost 192.168.146.151:80>

\#指定虚拟主机使用的域名

ServerName [www.testl51.com](http://www.testl51.com) ♦指定虚拟主机的主目录

DocumentRoot /data/www/151

〈Directory ,r/data/www/151/M>

Options Indexes FollowSymLinks AllowOverride None

\#使用AuthType指令设置认证类型，此处为基本认证方式 AuthType Basic

\#使用AuthName指令设置，此处设置的域名称会显示在提示输入密码的对话框中 AuthName ’’auth”

\#使用AuthUserFile指令设置认证口令文件的位置 AuthUserFile /usr/local/apache2/conf/users«list #指定允许访问的用户

Require user admin

</Directory>

</VirtualHost>

重启Apache服务后i在客户端使用浏览器访问[http://www.testl51.com/](http://www.testl51.com/%e8%bf%9b%e8%a1%8c%e6%b5%8b%e8%af%95%ef%bc%8c%e5%a6%82%e5%9b%be6.2)[进行测试，如图6.2](http://www.testl51.com/%e8%bf%9b%e8%a1%8c%e6%b5%8b%e8%af%95%ef%bc%8c%e5%a6%82%e5%9b%be6.2) 所示。输入用户名和密码，单击【确定】按钮。

验证成功后将进入如图6.3所示的页面；否则将会要求重新输入。如果单击【取消】按钮 将会返回如图6.4所示的错误页面。

<http://www.testl51.com> 消求用 户名和密妈•信息为：auth

用户名】|1 密妈：I    |



图6.2认证窗口



^3    ▼    http //www tesQ51.com/

圖访间最多，® CentOS □ Support ▼

192.168.146.151

图6.3访问PHP成功页面

401 Unauthorized • Mozdla Ftrefox    - n x

文件ID嘛械t£)敬她历史(5J杉签(B) XAQ)帮助

♦I    »    僅^ P^ ' http 7/www testI51 com/ ; j

趣汸间嚴多 *    CentOS E5 Support ▼

Unauthorized

This server could not verify that you are authorized to access the document requested. Either you supplied the wrong credentials (e g., bad password), or your browser doesn't understand how to supply the credentials required.

賓班

图6.4认证错误页面

\3. .htaccess 设置

.htaccess文件又称为分布式配置文件，该文件可以覆盖httpd.conf文件中的配置，但是它 只能设置对目录的访问控制和用户认证。.htaccess文件可以有多个，每个.htaccess文件的作用 范围仅限于该文件所存放的目录以及该目录下的所有子目录。虽然.htaccess能实现的功能在 〈Directory〉段中都能够实现，但是因为在.htaccess修改配置后并不需要重启Apache服务就能

生效，所以在一些对停机时间要求较高的系统中可以使用。

启用.htaccess文件需要做以下设置：

(1)打开配置文件www.testl51.conf,将目录的配置信息替换为下面的内容，如【示例 6-31】所示。

【示例6-31】

\#以下为Diretory段的配置 〈Directory n/data/www/151/M>

Options Indexes FollowSymLinks A1lowOverride All Require all granted

<ZDirectory>

修改主要包括两个方面：

删除原有的关于访问控制和用户认证的参数和指令，因为这些指令将会被写到.htaccess 文件中去。

添加AllowOveiride All参数，允许.htaccess文件覆盖httpd.conf文件中关于虚拟主机目录 的配置。如果不做这项设置，.htaccess文件中的配置将不能生效。

(2)重启Apache服务，在/data/www/151/目录中创建一个文件.htaccess，如【示例6-32】

所示。

【示例6-32】

AuthType Basic

M吏用AuthName指令设置 AuthName MauthM

\#使用AuthUserFile指令设置认证口令文件的位置 AuthUserFile /usr/local/apache2/conf/users.list #使用require指令设置admin用户可以访问

require user admin

其他测试过程与上一节类似，此处不再赘述。

5e2    MySQL服务的安装与配置

MySQL可以支持多种平台，如Windows、UNIX、FreeBSD或其他Linux系统。MySQL 如何安装，MySQL如何配置，MySQL又有哪些启动方式，MySQL服务如何停止。要了解这 些知识，就要阅读本节的内容。

![img](11 CentOS7fbdfa1060ed0f49e18-110.jpg)



由于MySQL被收购，现在大量的公司将原来MySQL的解决方案改为MariaDB, MariaDB 是MySQL的一个分支，与其完全兼容。

6.2.1 MySQL的版本选择

安装MySQL首先确定使用哪个版本。MySQL的开发有几个发布系列，可以选择最适合 要求的一个版本。MySQL的每个版本提供了二进制版本和源码，开发者可以自由选择安装。 在最新的5.6版本中，数据库的可扩展性、集成度以及查询性能都得到提升。新增功能包括实 现全文搜索，开发者可以通过InnoDB存储引擎列表进行索引和搜索基于文本的信息；InnoDB 重写日志文件容量也增至2TB,能够提升写密集型应用程序的负载性能；加速MySQL复制； 提供新的编程接口，使用户可以将MySQL与新的和原有的应用程序以及数据存储无缝集成。 MySQL5.1是当前稳定并且使用广泛的发布系列。只针对漏洞修复重新发布；没有增加会影响 稳定性的新功能。MySQL4.x是旧的稳定发布系列。目前只有少量用户使用。

本章将以MySQL5.1.71版本为例说明MySQL的安装和使用。安装之前有必要了解下 MySQL的版本命名机制。

6.2.2 MySQL的版本命名机制

MySQL的版本命名机制使用由数字和一个后缀组成的版本号。如mysql-5.1.71版本号这 样解释。

第1个数字5是主版本号，相同主版本号具有相同的文件格式。

第2个数字1是发行级别。主版本号和发行级别组合到一起便构成了发行序列号。

第3个数字71是在此发行系列的版本号，随每个新分发版本递增。

同时版本号可能包含后缀，如alpha、beta和rc。

alpha表明发行包含大量未被彻底测试的新代码，包含新功能，一般作为新功能体验使用。 beta意味着该版本功能是完整的，并且所有的新代码被测试，没有增加重要的新特征，没有已 知的缺陷。rc是发布版本，表示一个发行了一段时间的beta版本，运行正常，只增加了很小 的修复。如果没有后缀，如mysql-5.1.71 -Iinux-i686-icc-glibc23.tar,这意味着该版本已经在很 多地方运行一段时间了，而且没有非平台特定的缺陷报告，可以认为是稳定版。

6.2.3 MySQL rpm 包安装

MySQL的安装可以通过源码或rpm包安装，如要避免编译源代码的复杂配置，可以使用 rpm包安装。但在CentOS 7光盘中没有MySQL安装包，所以无法通过光盘进行安装，这里 将使用yum安装源的方式进行安装，如【示例6-33］所示。

【示例6-33】

\#下载安装源

[rooteCentOS -]# wget <http://dev.mysql.com/get/mysql-community~release-el7-5.noarch.rpm>

-2015-04-09 10:17:50-

<http://dev.mysql.com/get/mysql-community-release~el7-5.noarch.rpm> Resolving dev.mysql.com (dev.mysql.com)... 137.254.60.11

Connecting to dev.mysql.com (dev.mysql.com)|137.254.60.111:80... connected. HTTP request sent, awaiting response... 302 Found

Location: <http://repo.mysql.com/mysql-community-release-el7~5.noarch.rpm> [following]

井安装源

[root@CentOS -]# rpm ~ivh mysql-community~release-el7-5.noarch.rpm Preparing...    ################################# [100%]

Updating / installing...

1:mysql-community-release-el7-5 #################################

[100%]

\#通过yum工具安装mysql

[root@CentOS -]# yum install —y mysql-community-server Loaded plugins: fastestmirror, langpacks Loading mirror speeds from cached hostfile

\*    base: mirrors.pubyun.com

\*    extras: centos.ustc.edu.cn

\*    updates: centos.ustc.edu,cn Resolving Dependencies

--> Running transaction check

---> Package mysql-community-server.x86_64 0:5.6.24-3.el7 will be installed

-一> Processing Dependency: mysql-community-common(x86-64) « 5.6.24-3.el7 for

package: mysql-community-server-5.6.24-3.el7.x86_64

--> Processing Dependency: mysql-community-client(x86-64) = 5.6.24~3,el7 for

package: mysql-community--server-5.6.24-3.e!7 .x86一64

—> Processing Dependency: perl(Data::Dumper) for package:

mysql-community-server-5.6.24-3.el7.x86 64

\> Processing Dependency: perl(DBI) for package:

mysql-community-server-5.6.24-3.el7.x86_64

\#査看安装后的文件路径

(rootQCentOS Packages]# which mysql mysqld 一safe mysqlbinlog mysqldump

/usr/bin/mysql

/usr/bin/mysqld_safe

/usr/bin/mysqlbinlog

/usr/bin/mysqldump

如需查看每个安装包包含的详细文件列表，可以使用“rpm-ql软件名”查看，该命令列 出了当前rpm包的文件列表及安装位置，如【示例6-34】所示。

【示例6-34】

(root@CentOS *•] # rpm ~ql mysql-community-server /etc/logrotate.d/mysql /etc/my.cnf

/etc/my.cnf.d

/usr/bin/innochecksum

/usr/bin/my_print_defaults

/usr/bin/myisam 一ftdump

/usr/bin/myisamchk

携寿翁顧醸瓣磯麟環瓣辑耗滅璃■觀猶纖灘鑛繡MB嫁觀镜毫鑛

/usr/bin/resolveip

/usr/lib/systemd/system/mysqld.service /usr/lib/tmpfiles.d/raysql.conf /usr/lxb64/mysql/plugin /usr/lib64/mysql/plugin/adt_null.so /usr/lib64/mysql/plugin/auth.so

从上面的命令输出中可以看到软件文件中包含mysqld.service,此文件就是MySQL的启 动停止控制单元。

6.2.4 MySQL源码安装

用户可以从[http://dev.mysql.com/Downloads/](http://dev.mysql.com/Downloads/%e4%b8%8b%e8%bd%bd%e6%9c%80%e6%96%b0%e7%a8%b3%e5%ae%9a%e7%89%88%e7%9a%84%e6%ba%90%e4%bb%a3%e7%a0%81%ef%bc%8c%e6%9c%ac%e7%ab%a0%e4%bb%a55.6.24%e4%b8%ba%e4%be%8b)[下载最新稳定版的源代码，本章以5.6.24为例](http://dev.mysql.com/Downloads/%e4%b8%8b%e8%bd%bd%e6%9c%80%e6%96%b0%e7%a8%b3%e5%ae%9a%e7%89%88%e7%9a%84%e6%ba%90%e4%bb%a3%e7%a0%81%ef%bc%8c%e6%9c%ac%e7%ab%a0%e4%bb%a55.6.24%e4%b8%ba%e4%be%8b) 说明MySQL的安装过程，其他版本的安装过程类似，如【示例6-35】所示。

【示例6-35】

\#下载源码

[rootSCentOS soft]# wget

http://dev.mysq!.com/get/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz -2015-04-09 21:57:30 —

<http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz> Resolving dev.mysql.com (dev.mysql.com)... 137.254.60.11

Connecting to dev.mysql.com (dev.mysql.com)1137.254.60.Ill:80... connected. HTTP request sent, awaiting response... 302 Found

Location: [http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.24.tar.](http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz)[gz](http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.24.tar.gz) [following]

-2015-04-09 21:57:33—

<http://cdn.mysql.com/Downloads/MySQL-5.6/mysql~5-6.24.tar.gz>

\#恢复源码包

[rootQCentOS soft]# tar xvf mysql-5.6.24.tar.gz [root@CentOS soft]# cd mysql-5.6.24 #安装编译所需的软件包

[root@CentOS mysql-5.6.24]# yum install -y make gcc-c++ cmake bison-devel ncurses-devel gcc autoconf automake zlib* fiex* libxml*

libmcrypt* libtool-ltdl-devel*

3.6 kB 00:00:00 3.4 kB 00:00:00 J 2.5 kB 00:00:00 I 2.5 kB 00:00:00 J 2.5 kB 00:00:00 3.4 kB 00:00:00



Loading mirror speeds from cached hostfile

\*    base: mirrors.pubyun.com

\*    extras: centos.ustc.edu.cn

\*    updates: centos.ustc.edu.cn

择安装完软件包后，需进行配置

\#这个过程将费时3~5分钟

[root@CentOS mysql-5.6.24 J # cmake \

-DCMAKE_lNSTALL_PREFIX=/usr/local/mysql \

-DMYSQL_DATADIR-/data/mysql/data \

-DSYSCON顧R=/etc \

~DWXTH_MYISAM_STORAGE_ENGINE=1 \

-DWITH_XNNOBASE_STORAGE_ENGINE-1 \

-DMYSQL_UNIX_ADDR-/tmp/mysql/mysql.sock \

-DMYSQL_TCP_PORT=3 306 \    *

~DENABLED LOCAL INFILE—1 \

一 一

-DWITH~PART XTION一STORAGE_ENGINE=1 \

-DEXTRA_CHARSETS=all \

-DDEFAULT_CHARSET-utf8 \

-DDEFAULT COLLATION=utf8 general ci #编译并且安

\#编译过程大约需要30-50分钟 [root@CentOS mysql-5.6.24]# make [rootGCentOS mysql-5.6.24]# make install #设置权限

[rootSCentOS mysql-5.6.24]# groupadd mysql [root@CentOS mysql-5.6.24]# useradd -r -g mysql mysql (root@CentOS mysql-5.6.24]# cd /usr/local/mysql/

\#设置权限以便mysql能修改文件

(root@CentOS mysql]# chown ~R mysql.mysql ./

[root@CentOS mysql]# chown -R mysql.mysql /data/mysql/data #初始化数据库

\#需要注意的是此处设置的数据fit录应该与之前MYSQL_DATADIR指定的目录相同 [root@CentOS mysql]# scripts/mysql_install_db user=mysql

-ldata~/data/mysql/data

\#恢复权限设置，并修改相应目录的权限以便niysql修改 [root@CentOS raysql]# chown -R root ./ [root@CentOS mysql]# chown -R mysql data

上述示例表示将MySQL软件安装到/usr/local/mysql目录下，本示例中使用的参数及其含 义如下：

•    CMAKE_INSTALL_PREFIX:表示将MySQL安装到何处，此例中将安装到 /usr/local/mysql 目录中。

•    MYSQL DATADIR:表示MySQL的数据文件存放目录。

•    SYSCONFDIR:配置文件所在目录。

•    WITH_MYISAM_STORAGE_ENGINE:将 MylSAM 存储引擎编译到服务中。

•    WITH_INNOBASE_STORAGE_ENGINE:将 InnoDB 存储引擎编译到服务中。

•    ENABLED LOCAL INFILE:指定是否允许本地执行 LOAD DATA rNFILE。

•    MYSQL_TCP_PORT:默认使用的端口。

•    WITH PARTITION STORAGE ENGINE:将分区引擎编译到服务中。

•    EXTRA_CHARSETS:让服务支持所有扩展字符集=

•    DEFAULT CHARSET:服务使用的默认字符集，此处设置为utfB。

•    DEFAULT COLLATION:默认的排序规则。

编译安装MySQL时有许多参数，这些参数的详细含义和说明可以在官方网站中查找： <http://dev.mysqI.eom/doc/refman/5.6/en/source-configuration-options.htmlo>

完成上述安装步骤还不够，还需要为MySQL添加配置选项、启动停止脚本等，一个简单 的示例如【6-36】所示。

【示例6-36】

[root@CentOS # cd /usr/local/mysql/

\#去掉配置文件中的注释行仅显示有效行 [root@CentOS mysql] # grep -v n/s#n my .cnf [mysqld]

\#指定默认端口为3306 port = 3306 #指定MySQL监听的端口 bind-address=l92.168.146.150 #指定MySQL的主目录 basedir=/usr/local/mysql #指定数据目录

datadir=/data/mysql/data socket=/var/lib/mysql/mysql.sock

\#启动时使用的用户 user=mysql

\#指定时区与系统一致

![img](11 CentOS7fbdfa1060ed0f49e18-111.jpg)



:縫



default-time-zone=system #指定使用的存储引擎为InnoDB default-storage-engine-InnoDB #指定错误日志位置

log-error^/var/log/mysqld.log

\#默认配置的MySQL模式    ，，：，    ，.，

sql_mode-N0_ENGINE_SUBSTI7UTI0N,STRICT_TRANS_TABLES

\#将€动脚本放到/etc/init.d目录中

[root@CentOS mysql]# cp support-files/mysql.server /etc/init.d/mysqld #将1^391(1添加为系统服务

[root@CentOS mysql]# chkconfig --add mysqld

[root^CentOS mysql}# service mysqld start

Starting MySQL.. SUCCESS»

\#此时MySQL的root用户还没有密码，应该为其设置密码

[root@CentOS mysql]# /usr/local/mysql/bin/mysql ~u root -h 192.168.146.150 -p #由于还没有设置密码因此直接按下Enter键即可 Enter password:

Welcome to the MySQL monitor. Commands end with ; or \g.

Your MySQL connection id is 2

Server version: 5.6.24 Source distribution

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its

affiliates. Other names may be trademarks of their respective

owners.

Type *help; 1 or 1 \h* for help. Type ' \c * to clear the current input statement.

\#设置root用户的密码为123456

mysql> set password == password(11234561);

Query OK, 0 rows affected <0.00 sec)

\#设置完成后输入quit退出

mysql> quit

Bye

![img](11 CentOS7fbdfa1060ed0f49e18-112.jpg)



本小节仅简单介绍配置文件等内容，在后继小节中将详细介绍这些内容。

6.2.5 MySQL程序介绍

MySQL版本中提供了儿种类型的命令行运用程序，主要有以下几类:

(1)    MySQL服务器和服务器启动脚本

•    mysqld是MySQL服务器主程序；

•    mysqld safe、mysql.server 和 mysqld multi 是服务器启动脚本；

•    mysql install db是初始化数据目录和初始数据库。

(2)    访问服务器的客户程序

•    mysql是一个命令行客户程序，用于交互式或以批处理模式执行SQL语句；

•    mysqladmin是用于管理功能的客户程序；

•    mysqlcheck执行表维护操作；

•    mysqldump和mysqlhotcopy负责数据库备份；

•    mysqlimport导入数据文件；

•    mysqlshow显示信息数据库和表的相关信息；

•    mysqldumpslow分析慢查询日志的工具。

(3)    独立于服务器操作的工具程序

•    myisamchk执行表维护操作；

•    myisampack产生压缩、只读的表；

•    mysqlbinlog查看二进制日志文件的实用工具；

•    perror显示错误代码的含义。-

除了上面介绍的这些随MySQL—起发布的命令行工具外，另外有一些GUI工具，需单 独下载使用。

6.2.6 MySQL配置文件介绍

如使用rpm包安装，MySQL的配置文件位于/etc/my.cnf, MySQL配置文件的搜索顺序可 以使用以下命令查看，如【示例6-37】所示。

【示例6-37]

[root@CentOS Packages]# /usr/libexec/mysqld --help --verbose Igrep -BI -i "roy.cnfM

Default options are read from the following files in the given order: Zetc/mysql/my.cnf /etc/my.cnf    .my.cni

上述示例结果表示该版本的MySQL搜索配置文件的路径依次为/etc/mysql/my.cnf /etc/my.cnf〜/.my.cnf。即先查找/etc/mysql/my.cnf，如果找到则使用此配置文件，否则继续查找 /etc/my.cnf,直到找到有效的配置文件为止。为便于管理，在只有一个MySQL实例的情况下 一般将配置文件部署在/etc/my.cnf.中。

MySQL配置文件常用选项(mysqld选项段)说明如表6.2所示。

表6.2 MySQL配置文件常用参数说明

| 参数                          | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| bind-address                  | MySQL实例启动后绑定的IP                                      |
| port                          | MySQL实例启动后监听的端口                                    |
| socket                        | 本地socket方式登录MySQL时socket文件路径                      |
| datadir                       | MySQL数据库相关的数据文件主目录                              |
| tmpdir                        | MySQL保存临时文件的路径                                      |
| skip-external-locking         | 跳过外部锁定                                                 |
| back log                      | 在MySQL的连接请求等待队列中允许存放的最大连接数              |
| character-set-server          | MySQL默认字符集                                              |
| key buffer size               | 索引缓冲区，决定了 myisam数据库索引处理的速度                |
| max connections               | MySQL允许的最太连接数                                        |
| max connect errors            | 客户端连接指定次数后，服务器将屏蔽该主机的连接               |
| table cache                   | 设置表高速缓存的数量                                         |
| max allowed packet            | 网络传输中，一次消息传输量的最大值                           |
| binlog cache size             | 在事务过程中容纳二进制日志SQL语句的缓存大小                  |
| sort buffer size              | 用来完成排序操作的线程使用的缓冲区大小                       |
| join buffer size              | 将为两个表之间的每个完全连接分配连接缓冲区                   |
| thread cache size             | 线程缓冲区所能容纳的最大线程个数                             |
| thread concurrency            | 限制了一次有多少线程能进入内核                               |
| query cache size              | 为缓存查询结果分配的内存的数量                               |
| query cache limit             | 如查询结果超过此参数设置的大小将不进行缓存                   |
| ft min word len               | 加入索引的词的最小长度                                       |
| thread stack                  | 每个连接创建时分配的内存                                     |
| transaction isolation         | MySQL数据库事务隔离级别                                      |
| tmp table size                | 临时表的最大大小                                             |
| net buffer length             | 服务器和客户之间通信使用的缓冲区长度                         |
| read buffer size              | 对数据表作顺序读取时分配的MySQL读入缓冲区大小                |
| read md buffer size           | 是MySQL随机读缓冲区大小                                      |
| max heap table size           | HEAP表允许的最大值                                           |
| default-storage-engine        | MySQL创建表时默认的字符集                                    |
| log-bin                       | MySQL二进制文件binlog的路径和文件名                          |
| server-id                     | 主从同步时标识唯一的MySQL实例                                |
| slow query log                | 是否开启慢查询，为1表示开启                                  |
| long query time               | 超过此值则认为是慢查询，记录到慢查询曰志                     |
| log-queries-not-using-indexes | 如SQL语句没有使用索引，则将SQL语句记录到慢査询日志中         |
| expire-logs-days              | MySQL二进制文件binlog保留的最长时间                          |
| replicate wild ignore table   | MySQL主从同步时忽略的表                                      |
| replicate wild do table       | 与replicate wild ignore table相反，指定MySQL主从同步时需要同步的表 |
| innodb data home dir          | InnoDB数据文件的目录                                         |

（续表）

| 参数                             | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| innodb file per table            | 启用独立表空间                                               |
| innodb data file path            | Innodb数据文件位置                                           |
| innodb log group home dir        | 用来存放IrrnoDB日志文件的目录路径                            |
| innodb_additional_mem_ pool size | IrnioDB存储的数据目录信息和其他内部数据结构的内存池大小      |
| innodb buffer pool size          | InnoDB存储引擎的表数据和索引数据的最大内存缓冲区大小         |
| innodb file io threads           | I/O操作的最大线程个数                                        |
| innodb thread concurrency        | Innodb并发线程数                                             |
| innodb_flush_log_at_trx_commit   | Innodb日志提交方式                                           |
| innodb log buffer size           | InnoDB日志缓冲区大小                                         |
| innodb log file size             | InnoDB日志文件大小                                           |
| innodb log files in group        | Innodb日志个数                                               |
| innodb_max_dirty_pages_pct       | 当内存中的脏页量达到innodb_buffer_pool大小的该比例（％）时，刷新脏 页到磁盘 |
| innodb lock wait timeout         | InnoDB行锁导致的死锁等待时间                                 |
| slave compressed protocol        | 主从同步时是否采用压缩传输binlog                             |
| skip-name-resolve                | 跳过域名解析                                                 |

![img](11 CentOS7fbdfa1060ed0f49e18-113.jpg)



不同版本的配置文件参数及使用方法略有不同，具体可参考官方网站帮助文档。如果选项 名称配置错误，MySQL将不能启动。

1526.2.7 MySQL启动与停止

MySQL服务可以通过多种方式启动，常见的是利用MySQL提供的系统服务脚本启动， 另外一种是通过命令行mysqld_safe启动。

1.通过系统服务启动与停止

如使用yum工具安装，rpm包会自动将MySQL设置为系统服务，同时可以利用“service mysqld start”启动，查看MySQL是否为系统服务可以使用下面的命令，如示例6-38所示。

【示例6-38】

[root@CentOS mysql]# systemctl list-unit-files } grep mysqld ffiysqld.service    disabled

[root@CentOS mysql]# systemctl enable mysqld.service In -s */usr/lib/systemd/system/mysqld.service1

1/etc/systemd/system/Tnysql,Service1

In -s 1/usr/lifo/systemd/system/mysqld.service'

*/etc/systemd/system/multi-user.target.wants/mysqld.service1

\#査看MySQL启停控制单元

首先利用systemctl list-unit-files查看系统服务，显示结果为disable,表示MySQL并没有 设置为开机自动启动模式。可以通过systemctl enable mysqld.service将mysqld系统服务设置为 开机自动启动。

经过上述步骤，MySQL成为系统服务并且开机自动启动，如需启动或停止MySQL,可 以使用【示例6-39】中的命令。

【示例6-39]

\#安装完成后提供的默认配置文件 [rootGCentOS 〜cat -n /etc/my.cnf

1    [mysqld]

2    datadir=/var/lib/mysql

3    socket=/var/lib/mysql/mysql.sock

4

5    symbolic-links=0

6

7    sql_mode=NO_ENGINE一SUBSTITUTION,STRICT_TRANS一TABLES

8

9 [rnysqld_safe]

10    log-error=/va.r/log/mysqld.log

11    pid-f ile==/var/run/mysqid/mysqld.picl #启动MySQL服务

[root@CentOS # systemctl start mysqld.service #査看MySQL启动状态

[rootGCentOS # service mysqld status mysqld.service - MySQL Community Server

Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled)

Active: active (running) since Tue 2015-04-14 15:42:38 CST; 38s ago Process: 2473 ExecStartFost=/usr/bin/mysql-systemd-start post (code=exited, status=O/SUCCESS)

Process: 2462 ExecStartPre^/usr/bin/mysql-systemd-start pre (code=exited, status^O/SUCCESS)'

Main PID: 2472 (mysqld一safe)

CGroup: /system.slice/mysqld.service

\-2472 /bin/sh /usr/bin/mysqld_safe

'-2623 /usr/sbin/mysqld basedir=/usr --datadir^/var/lib/raysql

\#利用ps命令查看MySQL服务相关进程 [root@CentOS # ps -efIgrep mysql

mysql 2472    1    0 15:42 ?    00:00:00 /bin/sh /usr/bin/mysqld一safe

mysql 2623 2472    0 15:42 ?    00:00:00 /usr/sbin/mysqld --basedir=/usr

--datadir=-/var/lib/mysql -一plugin-dir=/usr/lib64/mysql/plugin

―log-error=/var/log/mysqld.log    pid~file=/var/run/mysqld/mysqld.pid

--socket^/var/lib/mysql/mysql.sock

root 2668    1482    0 15:44 pts/0 00:00:00 grep --color=auto mysql

\#MySQL启动后默认的数据目录

[rootGCentOS ']# Is -lh /var/lib/mysql

total 109M

—r 一 ■XTW""*""

:~x+w 一 i?w——一

-rw^rw----



1 mysql mysql 1 mysql mysql 1 mysql mysql

1 mysql mysql



56 Apr 14 15:25 auto.cnf 48M Apr 14 15:42 ib^logfileO 48M Apr 14 15:25 ib^logfilel 12M Apr 14 15:42 ibdatal

drwx------ 2 mysql mysql 4.OK Apr 14 15:25 mysql

srwxrwxrwx 1 mysql mysql 0 Apr 14 15:42 mysql.sock

drwx------2 mysql mysql 4.OK Apr 14 15:25 performance_schema

\#登录测试

[rootGCentOS # mysql -uroot    •

Welcome to the MySQL monitor. Commands end with ; or \g.

Your MySQL connection id is 2

Server version: 5.6.24 MySQL Community Server (GPL)

. •

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

’'V    .    ■    -    —    -    -

Oracle is a registered trademark of Oracle Corporation and/or its affiliates. Other names may be trademarks of their respective

owners.

Type，help; • or * \h' for help. Type 1 \c1 to clear the current input statement.

mysql> SELECT version。；

I version() I

I 5.6.24 I

1 row in set (0.00 sec)

mysql> quit

Bye

\#通过系统服务停止MySQL服务

[rootQCentOS    # systemctl stop mysqld.service

查看了通过rpm包安装后的配置文件内容，分别指定了 d,atadir、socket和启动后以什么用 户运行，然后利用系统服务启动MySQL，命令为“systemctl start mysqld.service”，启动后利 用利用“systemctl status mysqld.service”或ps命令查看MySQL服务状态。同时ps命令显示 了更多的信息。

如果MySQL服务后查看相关的数据目录和文件，除通过配置文件外，可以通过ps命令 查看，如上述示例中的datadir位于/var/lib/mysql目录下。

MySQL成功启动后可以进行正常的操作了，初始化用户名为“root”，密码为空。使用“mysql -u root”可以成功登录mysql。

如需停止 MySQL，可以通过 “systemctl stop mysqld.service” 的方式停止 MySQL。

2.利用mysqld_safe程序启动和停止MySQL服务

如同一系统中存在多个MySQL实例，使用MySQL提供的系统服务已经不能满足要求， 这时可以通过MySQL安装程序提供的mysqld_safe程序启动和停止MySQL服务。

由于/var/lib/mysql为MySQL服务的默认数据目录，同时可以通过配置指定其他数据目录。 假设MySQL数据文件目录位于/data/mysql_data_3307，端口设置为3307,【示例6-40】演示了 设置启动和停止过程。

【示例6-40]

[root@CentOS 〜mkdir -p /data/xnysql_data_3307 [root@CentOS *•] # chown 一 R mysql.mysql / dat a /my sql_da t a_3 307 / [rootQCentOS ~]# mysql一install一db --datadir=/data/mysql_data_3307/ ——user=mysql

\#部分结果省略

Installing MySQL system tables...

OK

Filling help tables...

OK

\#部分结果省略 #査看系统表相关数据库

[root@CentOS # Is -lh /data/mysql_data__3307/ total 109M

| -rw-rw----  | 1    | mysql | mysql |
| ----------- | ---- | ----- | ----- |
| -rw-rw----  | 1    | mysql | mysql |
| -rw-rw----  | 1    | mysql | mysql |
| dr w x ~~—  | 2    | mysql | mysql |
| drwx------- | ..2  | mysql | mysql |



| 48M   | Apr  | 14   | 19:25 |
| ----- | ---- | ---- | ----- |
| 48M   | Apr  | 14   | 19:25 |
| 12M   | Apr  | 14   | 19:25 |
| 4. OK | Apr  | 14   | 19:25 |
| 4. OK | Apr  | 14   | 19:25 |

ib一logfileO ib一logfilel ibdatal rnysql

performance一schema



[root@CentOS # mysqld_safe 一一datadir-/data/mysql_data_3307 一-socket=/data/mysql一deitd一3307/mysql.sock 一—port=3307 一一user=mysql

—bind-address=192.168.146.150 &

[1] 7329

[root@CentOS -] # 150414 19:32:27 mysqld一safe Logging to '/var/log/mysqld. log * . 150414 19:32:27 mysqld_safe Starting mysqld daemon with databases from /data/mysql一data_3307

[root@GentOS 〜]#

[root@CentOS # ps -ef J grep mysqld__safe

root 7329    1482    0 19:32 pts/0 00:00:00 /bin/sh /usr/bin/mysqld一safe

--datadir=/data/mysql_data__3307 --socket=/data/mysql_data_3307/mysql.sock --port—3307 一-user=mysql --bind~address=192.168.146.150

root 7576    1482    0 19:33 pts/0 00:00:00 grep --color«auto mysqld_safe

[rootQCentOS # netstat -pint J grep 3307

tcp    0    0 192.168.146.150:3307    0.0.0,0:*    LISTEN

7545/mysqld

[root@CentOS 〜]# mysql -S /data/mysql一data一3307/mysql.sock -u root 一 一

Welcome to the MySQL monitor. Commands end with ; or \g.

Your MySQL connection id is 1

Server version: 5.6.24 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

■ ■ ■

Oracle is a registered trademark of Oracle Corporation and/or its affiliates. Other names may be trademarks of their respective owners.

Type fhelp; * or 1 \h» for help. Type '\c，to clear the current input statement.

mysql> \s

M 一 w— 一 — * 一 MB* M    M —

mysql Ver 14.14 Distrib 5.6.24, for Linux (x86一64) using EditLine wrapper

Connection id: Current database:

Current user:

■ ■    .. .

SSL:

Current pager:

Using outfile:

■

Using delimiter: Server version: Protocol version: Connection:

Server characterset: Db characterset: Client characterset: Conn, characterset: UNIX socket:

Uptime:



1

.- * ■

root@localhost Not in use

stdout

5.6.24 MySQL Community Server (GPL) 10

Localhost via UNIX socket latinl latinl latinl latinl

/data/mvsql data 3307/mysql.sock — ~

3 min 21 sec

Threads: 1 Questions: 5 Slow queries: 0 Opens: 67 Flush tables: 1 Open tables: 60 Queries per second avg: 0.024

上述示例首先创建了启动MySQL服务需要的数据目录/data/mysql_data_3307，创建完成 后通过chown将目录权限赋给mysql用户和mysql用户组。

mysql_install_db程序用于初始化MySQL系统表，比如权限管理相关的mysql.user表等等， 初始化完成以后利用mySqld_Safe程序启动，由于此示例并没有使用配置文件，需要设置的参 数通过命令行参数指定，没有设置的参数则为默认值。

系统启动完成后可以通过本地socket方式登录，另外一种登录方式为TCP方式登录，这 点将在下一节介绍，登录命令为 “mysql-S/data/mysql_data_3307/mysql.sock-u root”。登录完 成后第1行为欢迎信息，第2行显示了 MySQL服务给当前连接分配的连接ID，ID用于标识 唯一的连接。接着显示的为MySQL版本信息，然后是版权声明。同时给出了查看系统帮助的 方法。“\s”命令显示了 MySQL服务的基本信息，如字符集，启动时间，查询数量，打开表的 数量等等，更多的信息可以查阅MySQL帮助文档。

以上示例演示了如何通过mysqld_safe命令启动MySQL服务，如需停止，可以使用【示 例641】中的方法。

【示例6-41】

[rootQCentOS # mysqladmin -S /data/mysql一data/mysql•sock -u root shutdown 130806 22:56:11 mysqld一safe mysqld from pid file /data/mysql一data/CentOS.pid ended

[1] + Done mysqld_safe --datadir=/data/mysql_data ——socket=/data/raysql_data/mysql.sock ——port-3307 —user=mysql

通过命令mysqladmin可以方便地控制MySQL服务的停止。同时mysqladmin支持更多的 参数，比如查看系统变量信息，查看当前服务的连接等，更多信息可以通过 “mysqladmin-help” 命令查看。

除通过本地socket程序可以停止MySQL服务外，还可以通过远程TCP停止MySQL服务， 前提为该账号具有shutdown权限，如【示例6-42】所示。

【示例6-42】

[root@CentOS # mysql -S /data/mysql_data/mysql.sock -u root mysql> grant all on * . * to admin@ *192.168.146.1501 identified by "passl23"; Query OK, 0 rows affected (0.00 sec)

[root0CentOS # mysqladmin -uadmin -ppass!23 -hl92.168.146.150 -P3307 shutdown [root@CentOS 番 130806 23:01:22 mysqld_safe mysqld from pid file /data/mysql__data/CentOS. pid ended

[1]+ Done    mysqld_safe ——datadir=/data/mysql_data

--socket=/data/mysql__data/mysql.sock --port=3307 --user=mysql

[root@CentOS 〜]# mysql -S /data/mysql_data/mysql,sock -u root ERROR 2002 (HY000) : Can*t connect to local MySQL server through socket •/data/mysql_data/mysql.sock ’    (2)

由于具有shutdown等权限的用户可以远程停止MySQL服务，因此日常应用中应该避免 分配具有此权限的账户。

6e3 PHP安装与配置

PHP的安装同样需要经过环境检查、编译和安装3个步骤，本节采用的PHP版本为 php-5.4J6.tar.gz，安装过程如【示例6-43】所示。

【示例6-43】

\#解压源码包

[root@CentOS soft]# tar xvf php-5.4.16.tar.gz [root^CentOS soft]# cd php-5.4.16 #检査系统环境

[root@CentOS php~5.4.16]# ./configure --prefix=/usr/local/php —with~raysql=/usr/local/

mysql    enable-fastcgi

\#另外一种集成方式编译命令

[root@CentOS php-5.4.16]#    ./configure 一prefix*/usr/local/php

--with-mysql=/usr/local/mysql --enable-fpm

\#编译源码

[root@CentOS php-5.4.16]# make 共安装

[root@GentOS php-5.4.16]# make install

“--enable-fastcgi”含义为开启PHP的FastCGI支持，另外一种开启FastCGI支持的方式 为指定“--enable-fym”参数。

LAMP集成安装、配置与测试实战

上面章节已经分别介绍了 MySQL、Apache的安装与设置。本节主要介绍Linux环境下利 用源码Apache、MySQL、PHP的集成环境的安装过程。

PHP为“Professional Hypertext Preprocessor”的缩写，最新发布版本为5.6.7,此版本包含 了大量的新功能和bug修复，特别注意的一点是不再支持Windows XP和2003系统。PHP具 有非常强大的功能，所有的CGI的功能PHP都能实现，支持几乎所有流行的数据库以及操作 系统。和其他技术相比，PHP本身免费且是开源代码。因为PHP可以被嵌入于HTML语言， 它相对于其他语言，编辑简单，实用性强，更适合初学者。PHP运行在服务器端，可以部署

在UNIX、Linux、Windows、Mac OS下。另外PHP支持面向对象编程。本节主要以php5.4.16 源码安装为例说明PHP的安装过程，因不同版本之间有特定差别，需要根据业务特性选择合 适的版本。

从源代码安装Apache、MySQL、PHP, PHP用户可以从http://www.php.net下载最新稳定 版的源代码，php可以支持很多扩展，本节软件安装涉及的软件包列表如【示例6-44】所示。

【示例6-44】

![img](11 CentOS7fbdfa1060ed0f49e18-114.jpg)



mysql~5.1.49.tar.gz apache-2.2.24.tar.gz libxml2-2,7.7.tar .gz curl-7.15.1-tar.gz zlib-1.2.3.tar.gz freetype~2.1.10.tar.gz libpng-1.2.8~config.tar.gz jpegsrc.v6b.tar.gz gd-2.0.33.tar.gz openssl-1.0.0c.tar.gz php-5.4.16.tar.gz

安装过程如实例[6-451所示。

【示例6-45]

\# 安装 MySQL 胃

\#安装环境

[root@CentOS soft]# yum install ~y gcc gcc-c-n- make cmake ncurses-devel bison-devel automake autoconf libtool libXpm-devel 1 ibXpm-deve1.i686 libvpx-devel gmp-devel

[root@CentOS soft]# tar xvf mysql-5.6.24.tar.gz

[root@CentOS soft]# cd mysql~5.6.24

(rootGCentOS raysql~5.6.24]# groupadd mysql

[root@CentOS mysql-5.6.24]# useradd -r -g mysql mysql

[root@CentOS mysql-5.6.24]# mkdir -p /data/mysql/data

[rootGCentOS mysql-5.6.24]# chown -R mysql.mysql /data/mysql/data [root@CentOS mysql-5.6.24}# cmake \

-DCMAKE一INSTALL一PREFIX=/usr/local/mysql \ -DMYSQL_DATADIR=/data/mysql/data \

-DSYSCONFDXR-/etc \

-DWITH_MY X SAM_STORAGE_ENGINE-1 \ -DWITH__INNOBASE_STORAGE_ENGINE=1 \ -DMYSQL_UNIX_ADDR=/tmp/mysql/mysql.sock \ -DMYSQL一TCP_PORT=3306 \

-DENABLED_LOCAL_INFILE=1 \



-DWITH_PARTITION_STORAGE_ENGINE=1 \ -DEXTRA_CHARSETS=al1 \ -DDEFAULT_CHARSET=utf8 \

一DDEFAULT_COLLATION=utf8_general_c i

CentOS 7系统管理与运维实战

PA趟權::

[root径CentOS mysql-5.6.24]# make [root^CentOS mysql-5.6.24]# make install #安装SSL

\#解压源码包

[root^CentOS soft]# tar xvf openssl-l,0.2-latest.tar.gz

[root@CentOS soft]# cd openssl-1.0.2a #配置编译选项

[rootQCentOS openssl-1.0.2a]# ./config --prefix=/usr/local/ssl --shared #編译

[root^CentOS openssl-1,0.2a]# make [rootGCentOS openssl-1.0.2a]# make install #将动态库路径加入系统路径中

[root@CentOS openssl-1.0.2a]# echo /usr/local/ssl/lib/ »/etc/ld.so.conf #加载动态库以便系统共享

[rootQCentOS openssl-1.0.2a]# ldconfig #安装curl，以便可以在PHP中使用curl相关的功能 [rootGCentOS soft]# tar xvf curl-7.41.0.tar.gz [rootQCentOS soft]# cd curl~7.41.0 [root@CentOS curl-7.41.0]# chmod -R a+x .

[root@CentOS curl-7.41.0]# ,/configure prefix«/usr/local/curl

-enable-shared

[root@CentOS curl-7.41.0]# make [root@CentOS curl~7.41.0]# make install #安装 libxml

[root@CentOS soft]# wget

■ ■ . ■

<http://down1.chinaunix.net/distfiles/libxml2-2.7.8.tar.gz> [root径CentOS soft]# tar xvf libxml2-2-7.8.tar.gz [root@CentOS soft]# cd libxml2-2.7.8

[root@CentOS libxml2-2.7.8]# chmod -R a+x •

[root@CentOS libxml2-2.7.8]# ./configure --prefix^/usr/local/libxml2

--enable-shared [root@CentOS [root@CentOS #安装zlib

libxml2~2. libxml2-2,



7.8]# make 7.8]# make install



[root@CentOS soft]# wget

http://downl.chinaunix,net/distfiles/zlib-1.2.7.tar.gz [rootQCentOS soft]# tar xvf zlib-1.2.7.tar.gz [root@CentOS soft]弁 cd zlib-1.2.7/

[root足CentOS zlib-1.2.7]#    ./configure ~~prefix=/usr/local/zlib

--enable-shared.

[rootGCentOS [root^CentOS

zlib-1.2.7]# make zlib-1.2.7]# make install



\#安装 f reetype [rootQCentOS soft]# wget

[http://jaist.dl.sourceforge.net/project/freetype/freetype2/2.5.5/freetype~2.5•](http://jaist.dl.sourceforge.net/project/freetype/freetype2/2.5.5/freetype~2.5%e2%80%a2) 5. tar.gz

[root@CentOS soft]# tar xvf freetype~2.5.5.tar,gz [root@CentOS soft]# cd freetype-2.5.5/

[root@CentOS freetype-2.5.5]# ./configure    prefix==/usr/local/freetype

第6章搭建LAMP服务

--enable-shared

[rootGCentOS freetype-2.5,5]# make [rootQCentOS freetype-2.5.5]# make install #安装 libpng

[root@CentOS soft]# wget

<http://ncu.dl.sourceforge.net/project/libpng/libpngl6/!.6.17/libpng-l.6.17.tar> .gz

[root@CentOS soft]# tar xvf libpng-1.6.17.tar.gz [root@CentOS soft]# cd libpng-1.6.17/

[root@CentOS soft] # export LDFLAGS-**~L/usr/local/zlib/libn [root@CentOS soft]# export CPPFLAGS=H-I /usr/local/zlib/include11 (root@CentOS libpng-1.6.17]# ./configure --prefix=/usr/local/libpng

--enable-shared

[root@CentOS libpng-1.6.17]# make [rootQCentOS libpng-1.6.17]# make install #安装jpeg支持

[root@CentOS soft]# wget

<http://downl.chinaunix.net/distfiles/jpegsrc.v7.tar.gz> [root@CentOS soft]# tar xvf jpegsrc.v7.tar.gz [rootecentOS soft]# cd jpeg-7/

[root@CentOS jpeg-7]# cp /usr/bin/libtool <

[rootQCentOS jpeg-7]# ./configure --prefix=/usr/local/jpeg    enable-shared

[root@CentOS jpeg-7]# make [root@CentOS jpeg-7]# make install #安装gd库支持

[root@CentOS soft]# wget

<http://downl.chinaunix.net/distfiles/gd-2.0.33.tar.gz> [root@CentOS soft]# tar xvf gd-2.0.33.tar.gz cd gd-2.0.33/

[rootQCentOS soft]# [root@CentOS soft]# [rootGCentOS soft]# [root@CentOS gd-2.0.



In -s /usr/local/zlib/include/zlib.h /usr/include In -s /usr/local/zlib/include/zconf.h /usr/include/ .33]#    ./configure -prefix=/usr/local/gd -with-jpeg

-with-png -with-zlib=/usr/local/zlib -with-freetype=/usr/local/freetype [root@CentOS gd-2.0.33]# make

[root@CentOS gd-2.0.33]# make install #安装PHP

[root^CentOS soft]# tar xvf php~5.4.16.tar.gz [root@CentOS soft]# cd php~5.4.16

[root@CentOS php-5.4.16]# In -s /usr/local/freetype/include/freetype2

/usr/local/freetype/include/freetype2/freetype

[rootGCentOS php-5.4.16]# ./configure --prefix=/usr/local/php \ --with-config-file-scan-dir=/etc/php.d \ --with-apxs2=/usr/local/apache2/bin/apxs \

--with-mysql=/usr/local/mysql \

--enable-mbstring --enable-sockets \

一enable-soap --enable-ftp    enable-xml \

--with-iconv --with-curl --with-openssl \

--with~gd«yes    with-freetype~dir=/usr/local/freetype \

with-jpeg-dir«/usr/local/jpeg \

--with-png~dir=/usr/local/libpng \

--with-zlib^yes --enable-pcntl --enable-cgi \

一-with-gmp --with-libxml-dir~/usr/local/libxml2 \ --with-curl=/usr/local/crul \

--with-xpm-dir=/usr/local/freetype/include [root@CentOS php-5,4.16]# make [root@CentOS php-5.4.16]# make install #安装APC

[root@CentOS soft]# tar xvf tar xvf APC-3.1.13.tgz [root®CentOS soft]# cd APC-3.1.13



![img](11 CentOS7fbdfa1060ed0f49e18-115.jpg)



[root@CentOS APC-3.1.13]# /usr/local/php/bin/phpize [rootSCentOS APC-3.1.13]# ./configure

with-apxs^/usr/local/apache2/bin/apxs -一enable-apc    enable—shared

一-with-php~config=/usr/local/php/bin/php-config



[root^CentOS APC-3.1.13]# make



[root@CentOS APC-3.1.13]# make install



![img](11 CentOS7fbdfa1060ed0f49e18-116.jpg)



\#设置环境变量

[root@CentOS soft]# echo ’’export PATH=/usr/local/php/bin:\$PATH:. »/etc/prof ile



经过以上的步骤，Apache、MySQL和PHP环境需要的软件已经安装完毕，如需Apache 支持PHP,还需做以下设置。修改httpd.conf加入以下配置，如【示例6-46】所示。

【示例6-46]

\#以下语句加入httpd. conf

Include conf/php.conf

Listen 192.168.146.151

\#编辑 php.conf

[root@CentOS soft]# cat /usr/local/apache2/conf/php.conf AddType php5-script .php

然后像6.1.3小节中那样配置/etc/hosts设置域名解析和虚拟主机文件，配置虚拟主机文件 如【示例6-47】所示。

【示例6-47]

[root@CentOS soft]# cat

/usr/local/apache2/conf/vhost/[www.testdomain.com.conf](http://www.testdomain.com.conf) <VirtualHost 192.168.146.151:80>

ServerAdmin [pettersong@tencent.com](mailto:pettersong@tencent.com) DocumentRoot [/data/www.testdomain.com](file:///data/www.testdomain.com) ServerName [www.testdomain.com](http://www.testdomain.com)

〈Directory n/data/[www.testdomain.comn](http://www.testdomain.comn)> AllowOverride None Options None Require all granted

</Directory>

</VirtualHost>

重启Apache服务，然后编辑测试脚本，如【示例6-481所示。 【示例6-48】

| [root@CentOS www. testdoinain. com] # cat test .php |      |
| --------------------------------------------------- | ---- |
| <?php                                               |      |
| phpinfoO ;                                          |      |
| ?>                                                  |      |

然后可以进行浏览器的测试了，输入[http://www.testdomain.com/test.php](http://www.testdomain.com/test.php%e8%ae%bf%e9%97%ae%ef%bc%8c%e6%b5%8f%e8%a7%88%e5%99%a8%e4%b8%ad)[访问，浏览器中](http://www.testdomain.com/test.php%e8%ae%bf%e9%97%ae%ef%bc%8c%e6%b5%8f%e8%a7%88%e5%99%a8%e4%b8%ad) 显示如图6.5所示，说明PHP已经安装成功了。

| System                                | Linux localhosfc.toceldomain 3.10,0-229.1,2.el7,x86_64 #1 SMP Fri Mar 27 03:04:26 UTG2015 x86_64 |
| ------------------------------------- | ------------------------------------------------------------ |
| Build Date                            | Apr 16 2015 16:43:41                                         |
| ConfigureCommand                      | '/configure • ' —prefbf/usr/locol/php' ' — with-conflg-fite-scan-cflP1/etc/ php -tl'    wfth-apxsa^/usrZtocal/apache^bin/apxs' '--with-mysqh/usr/locol/mysql' enabte-mbstring''-enable-sockets' ’一enabte-soop' '—enable-ftp'    enobte-xnM' --with-toonv'*—wfth-curl'    with-openssf' with-gd=yes' wnth-freetype-dir^Zusr/local/freetype'with-peg-dir3/ usr/local/ jpeg' '~vwth-png-dir»/usr/locolZlibpng' '--wrth-zlib^yes' '-enable-pcntl'    enable-cgi'    with-gmp' 1 —wfth-libxmHdir®/usr/local/Iibxml2'    wrtth-cun=>Zusr/locol/crul, wlth'xpm-dir^Zusr/tocal/freetype/lnclude' |
| Server API                            | Apache 2.0 Handler                                           |
| VirtualDirectorySupport               | disabled                                                     |
| ConfigurationFile (php.ini) Path      | /usr/toce^php/Bb                                             |
| LoadedConfigurationFile               | (none)                                                       |
| Scan this dirfor additional.ini files | /efcc/php.d                                                  |

图6.5 PHP测试页面

6^5 MySQL曰常维护

搭建好LAMP后，还要注意MySQL的日常维护，包含权限管理、日志管理、备份与恢 复和复制等，本节主要介绍这方面的知识。

6.5.1 MySQL权限管理

MySQL权限管理基于主机名、用户名和数据库表，可以根据不同的主机名、用户名和数 据库表分配不同的权限。当用户连接至MySQL服务器后，权限即被确定，用户只能做权限内 的操作。

MySQL 账户权限信息被存储在 MySQL 数据库的 user、db、host、tables_priv、columns_priv 和procs_priv表中，在MySQL启动时服务器将这些数据库表内容读入内存。要修改一个用户 的权限，可以直接修改上面的几个表，也可以使用GRANT和REVOKE语句，推荐使用后者。 如需添加新账号，可以使用GRANT语句，MySQL的常见权限说明如表6.3所示。

表6.3 MySQL权限说明

| 参数                    | 说明                     |
| ----------------------- | ------------------------ |
| CREATE                  | 创建数据库、表           |
| DROP                    | 删除数据库、表           |
| GRANT OPTION            | 可以对用户授权的权限     |
| REFERENCES              | 可以创建外键             |
| ALTER                   | 修改数据库、表的属性     |
| DELETE                  | 在表中删除数据           |
| INDEX                   | 创建和刪除索引           |
| INSERT                  | 向表中添加数据           |
| SELECT                  | 从表中查询数据           |
| UPDATE                  | 修改表的数据             |
| CREATE VIEW             | 创建视图                 |
| SHOW VIEW               | 显示视图的定义           |
| ALTER ROUTINE           | 修改存储过程             |
| CREATE ROUTINE          | 创建存储过程             |
| EXECUTE                 | 执行存储过程             |
| FILE                    | 读、写服务器上的文件     |
| CREATE TEMPORARY TABLES | 创建临时表               |
| LOCK TABLES             | 锁定表格                 |
| CREATE USER             | 创建用户                 |
| PROCESS                 | 管理服务器和客户连接进程 |
| RELOAD                  | 重载服务                 |
| REPLICATION CLIENT      | 用于复制                 |
| REPLICATION SLAVE       | 用于复制                 |
| SHOW DATABASES          | 显示数据库               |
| SHUTDOWN                | 关闭服务器               |
| SUPER                   | 超级用户                 |

1.分配账号

如主机192.168.1.12需要远程访问MySQL服务器的account.users表，权限为SELECT和 UPDATE,则可以使用以下命令分配，操作过程如【示例649】所示。

【示例6-49】

\#分配用户名、密码和对应权限

mysql> grant select, update ON account .users TO [userl@192.168.1.12](mailto:userl@192.168.1.12) IDENTIFIED by fpassl23456*;

Query OK, 0 rows affected (0.00 sec) mysql>flush privileges;



\#账户创建成功后査看mysql数据库表的变化

mysql> select ★ from user where user=* userl1\G

i irow ★★*★*★***★**★★*★★**★***★★★*

Host: 192.168.1.12 User: userl



Password: *AB48367D337F60962B15F2DD7A6D005CE2793115



Select_priv: N Insert_priv: N Update一priv: N Delete一 priv: N Create_priv: N

Drop一 priv: N Reload_priv: N

Shutdown一priv: N



![img](11 CentOS7fbdfa1060ed0f49e18-118.jpg)



Process_priv: N



File_priv: N Grant一priv: N

References_priv: N Index一 priv: N Alter一priv: N

S ho w_db_p r i v: N Super一priv: N

Create一tmp一table一 priv: N



![img](11 CentOS7fbdfa1060ed0f49e18-119.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-120.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-121.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-122.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-123.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-124.jpg)



Lock_tables_priv: N Executejpriv: N

Repl_slave一 priv: N Repl_client_priv: N Create一view一 priv: N Show一view一priv: N

Create_routine一priv: N Alter_routine_priv: N Create_user_priv: N

Event_priv: N Trigger_priv; N



![img](11 CentOS7fbdfa1060ed0f49e18-125.jpg)



ssi一type: ssl一cipher: x509一issuer:

x509_subject: max一questions: 0

max_updates: 0



![img](11 CentOS7fbdfa1060ed0f49e18-126.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-127.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-128.jpg)



max_connections: 0 max__user_connections: 0 1 row in set <0.00 sec)

mysql> select * from db where user='userl*\G Empty set (0.00 sec)

mysql> select * from tables一priv where user:1userl*\G *****r********************** row *************************** Host: 192.168.1.12

Db: account User: userl Table_name: users

Grantor: root@localhost Timestamp: 2015-06-18 23:38:03 Table__priv: Select, Update Column_priv:

1 row in set (0.00 sec)

上述示例为MySQL服务器给远程主机192.168.1.12分配了访问表accout.users的查询和更 新权限。当用户登录时，首先检查user表，发现对应记录，但由于各个权限都为“N‘‘，因此 继续寻找db表中的记录，如没有则继续寻找tables^priv表中的记录，通过对比发现当前连接 的账户具有accountusers表的SELECT和UPDATE权限，权限验证通过，用户成功登录。

MySQL权限按照user^db-^tables_priv->columns_priv检查的顺序，如果user表中对应的 权限为“Y”，则不会检查后面表中的权限。

2.查看或修改账户权限

如需查看当前用户的权限，可以使用SHOW GRANTS FOR命令，如【示例6-50】所示。

【示例6-50】

raysql> show grants for [userl@192.168.1.12](mailto:userl@192.168.1.12) \G

***************************** i row ★**★-* *-**■********★**★***★■** ★

Grants for [userl@192.168.1.12](mailto:userl@192.168.1.12): GRANT USAGE ON *.* TO ，userl•@»192.168.1•12• IDENTIFIED BY PASSWORD 1*AB48367D337F60962B15F2DD7A6D005CE27931151

★    2 row * * * * * *******★*****'*********

Grants for [userl@192.168.1.12](mailto:userl@192.168.1.12): GRANT SELECT, UPDATE ON 'bbs'.* TO

，userU@ .192.168.1.121

★    ★★★★★★★★★★★★★★★★*★★★★★★★*★    row    ★★女女

Grants for [userl@192.168.1.12](mailto:userl@192.168.1.12): GRANT SELECT, UPDATE ON 'account、'users' TO •userl,@,192.168.1.12,

3 rows in set (0.00 sec)

上述示例通过查看指定账户和主机的权限，userl@192.168.1.12具有的权限为三条记录的 综合。密码为经过MD5算法加密后的结果。USAGE权限表示当前用户只具有连接数据库的 权限，但不能操作数据库表，其他记录表示该账户具有表“bbs.*”和表account.users的查询

和更新权限。

![img](11 CentOS7fbdfa1060ed0f49e18-129.jpg)



![img](11 CentOS7fbdfa1060ed0f49e18-130.jpg)



MySQL用户登录成功后权限加载到内存中，此时如果在另一会话中更改该账户的权限并 不会影响之前会话中用户的权限，如需使用最新的权限，用户需要重新登录。

3.回收账户权限

如需回收账户的权限，MySQL提供了 REVOKE命令，可以对应账户的部分或全部权限， 注意此权限操作的账户需具有GRANT权限。使用方法如【示例6-51】所示。

【示例6-51】

mysql〉revoke insert on *.* from test3@ * %1/ Query OK, 0 rows affected (0.00 sec) mysql> revoke ALL on * . * from test3@ * % *; Query OK, 0 rows affected (0.00 sec)

账户所有权限回收后用户仍然可以连接该MySQL服务器，如需彻底删除用户，可以使用 DROP USER命令，如【示例6-52】所示。

【示例6-52】

| mysql> show grants for test3W                                | r       |
| ------------------------------------------------------------ | ------- |
| +—~‘一—一一一一——-------—------------——                      | -----j- |
| I Grants for test3@%                                         | 1       |
| +—-----------------------------------------                  |         |
| I GRANT USAGE ON *.* TO 'tesWI                               | IV \|   |
| 1 row in set (0,00 sec)                                      |         |
| mysql> drop user test3@1 % 1;                                |         |
| Query OK, 0 rows affected (0.00                              | sec)    |
| mysql> show grants for test3@1 %                             | r       |
| ERROR 1141 (42000) : There is no such grant defined for user ftest3* on host |         |

6.5.2 MySQL日志管理

MySQL服务提供了多种日志用于记录数据库的各种操作，通过日志可以追踪MySQL服 务器的运行状态，及时发现服务运行中的各种问题。MySQL服务支持的日志有二进制日志、 错误日志、访问日志和慢查询日志。

1.二进制日志

二进制日志也通常被称为bintog,,记录了数据库表的所有DDL和DML操作，但并不包 括数据查询语句。

如需启用二进制日志，可以通过在配置文件中添加“--log-bin=[file-name]”选项指定二进 制文件存放的位置，位置可以为相对路径或是绝对路径。

由于binlog以二进制方式存储，如需查看其内容需要通过MySQL提供的工具mysqlbinlog 查看，如【示例6-53】所示。

【示例6-53]

[root@MySQL_l92_168_130 binlog]# mysqlbinlog mysql-bin.000005\cat -n

1    /*!40019 SET @@session.max一insert_delayed 一threads=0*/;

2    /*!50003 SET    ~

(aOLD__COMPLETION_TYPE=@@COMPLETION_TYPE, COMPLETXON_TYPE-0*/ ;

3    DELIMITER /*!*/;

4    # at 4

5    #130809 18:20:51 server id 1 end一logjpos 106 Start: binlog v 4f server v 5.1.71-log created 130809 18:20:51

6    # Warning: this binlog is either in use or was not closed properly.

7    BINLOG »

8

g8 IECJg8BAAAAZgAAAGoAAAABAAQANS4xI, j Y2LWxvZw7VAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA 9 AAAAAAAAAAAAAAAAAAAATVAAAEzgNAAgAEgAEBAQEEgT^kUwAEGggTkAAAICAgC

10 »/*!*/；

11    # at 106

12    #130809 18:21:25 server id 1 end 一log一 pos 228 Query thread一id«3 exec_time=0 error_code~0

1:3 use testDB_l/* ! */;

14    SET TIMESTAMP=1376043685/*t*/;

15    SET @@session.pseudo一thread_id=3/*!*/;

16    SET @@session.foreign一key一checks=l, @@session.sql一auto一is一null=l, @@session,unique_checks-l, @0session.autocommit~l/*!*/;

17    SET @@session.sql^mode^O/*!*/;

18    SET @@session.auto一increment一incrementel,

卵session, aijto一increment一of fset=l/* ! */;

19    /*!\C latinl *7/*!*/；

20    SET

@@session.character一set一client5^,@@session.collation一connection=8, @@session.co llation_server-8/*1*/;

21    SET @@session.lc一time一names=0/*!*/;

22    SET @@session.collation_database=5DEFAULT/*!*/;

23    update users_myisam set name="xxx" where name=*petter1

24    /*!*/;    ~

25    # at 228

26    #130809 18:21:32 server id 1 end_log_pos 350 Query thread_id®3

exec^time^O    error一code=0

27    SET TIMESTAMP=1376043692/*!*/;

28    update users_myisam set name=nxxxH where name-[1](#bookmark28) [2](#bookmark29) [3](#bookmark30) [4](#bookmark31) [5](#bookmark32)myisam *

29    /★!*/;

30    DELIMITER ?

31    # End of log file

32    ROLLBACK /* added by mysqlbinlog */;

33    /* J50003 SET COMPLETION_TYPE-@OLD_COMPLETION_TYPE*Z;

第5行记录了当前MySQL服务的server-id、偏移量、binlog版本、MySQL版本等信息， 第26-28行则记录了执行的SQL及时间。

如需删除binlog，可以使用“purge binary logs”命令，该命令可以指定删除的binlog序号 或删除指定时间之前的日志，如【示例6-54】所示。

【示例6-54】

\#删除指定序号之前的二进制曰志

PURGE BINARY LOGS TO 'mysql-bin.010';

\#删除指定时间之前的二进制曰志

PURGE BINARY LOGS BEFORE '2015-04-02 22:46:26';

除通过以上方法外，可以在配置文件中指定“expire_logs_days=#”参数设置二进制文件 的保留天数，此参数也可以通过MySQL变量设置，如需删除7天之前的binlog,可以使用【示 例6-55】的命令。

【示例6-55】

mysql> set global expire_iogs一; Query OK, 0 rows affected (0.01 sec)

此参数设置了 binlog日志的过期天数，此时MySQL可以自动清理指定天数之前的二进制 日志文件。

2.操作错误日志

MySQL的操作错误日志记录了 MySQL启动、运行至停止过程中的相关异常信息，在 MySQL故障定位方面有重要的作用。

可以通过在配置文件中设置“--log-erroi^file-name]”指定错误日志存放的位置，如没有 设置，则错误日志默认位于MySQL服务的datadir目录下。

一段错误日志如示例【示例6-56】所示。

【示例6-56】

5

6

7

8 9

10

created

11

12

13

created

14

15

16

17

18 19



130810    0:00:09 InnoDB: Completed initialization of buffer pool

InnoDB； The first specified data file ./ibdatal did not exist:

InnoDB: a new database to be created!

130810 0:00:09 InnoDB: Setting file ./ibdatal size to 10 MB InnoDB: Database physically writes the file full: wait...

130810    0:00:09 InnoDB: Log file . /ib_logfileO did not exist: new to be

InnoDB: Setting log file ./ib_logfileO size to 5 MB

InnoDB: Database physically writes the file full: wait...

130810    0:00:10 InnoDB: Log file . /ib__logfilel did not exist: new to be

InnoDB: Setting log file . /ib__logf ilel size to 5 MB

InnoDB: Database physically writes the file full: wait...

InnoDB: Doublewrite buffer not found: creating new

InnoDB: Doublewrite buffer created

InnoDB: Creating foreign key constraint system tables

InnoDB: Foreign key constraint system tables created



20    130810    0:00:10

21    130810    0:00:10

already in use

22    130810 0:00:10



InnoDB: Started; log sequence number 0 0 [ERROR] Can * t start server: Bind on TCP/IP port: Address



[ERROR] Do you already have another mysqld server running



on port:3306 ?



| 23   | 130810 | 0:00:10  |
| ---- | ------ | -------- |
| 24   | 130810 | 0:00:10  |
| 2+5  | 1308X0 | 0:00:15  |
| 26   | 130810 | 0:00:15  |
| 27   | 130810 | 00:00:15 |



/data/master/dhdata/CentOS.pid ended



[ERROR] Aborting

InnoDB: Starting shutdown...

InnoDB: Shutdown completed; log sequence number 0 44233 [Note] /usr/libexec/mysqlci: Shutdown complete mysqld_safe mysqld from pid file



以上日志信息记录了第1次运行MySQL时的错误信息，其中第2~3行的错误信息说明在 启动MySQL之前并没有初始化MySQL系统表，错误码13对应的错误提示可以使用命令 “perrOr13”查看。第21~23行则说明系统中已经启动了同样端口的实例，当前启动的MySQL 实例将自动退出。

3.访问日志

此曰志记录了所有关于客户端发起的连接、查询和更新语句，由于其记录了所有操作，在 相对繁忙的系统中建议将此设置关闭。

该日志可以通过在配置文件中设置指定访问日志存放的位置，另外 一种方法可以在登录MySQL实例后通过设置变量启用此日志，如【示例6-57】所示。

【示例6-57】

\#启用该日志

mysql> set global general_log=on;

Query OK, 0 rows affected (0.01 sec)

\#査询日志位置

mysql> show variables like * %general_log% *;

171

[1](#footnote1)

[root@CentOS tmp]# cat /data/master/dbdata/CentOS.err

[2](#footnote2)

   130810 00:00:09 mysqld_safe Starting mysqld daemon with databases from Zdata/master/dbdata

[3](#footnote3)

   /usr/libexec/mysqld: Can[2](#bookmark29)1 find file: './mysql/plugin.frm" (errno: 13)

[4](#footnote4)

   130810    0:00:09 [ERROR] Can * t open the mysql.plugin table. Please run

mysql一upgrade to create it.

[5](#footnote5)

   130810 0:00:09 InnoDB: Initializing buffer pool, size - 8.0M



如果没有指定[file-name],贝U默认为主机名(hostname)做为文件名，默认存放在数据目录 中。文件记录内容如【示例6-58】所示。

【示例6-58】

| [root@MySQL_l92_168_19_23         | 0    # cat                                  | :—n                                         |
| --------------------------------- | ------------------------------------------- | ------------------------------------------- |
| /data/slave/dbdata/MySQL_l92      | 168 19 230.log                              |                                             |
| 1    /usr/libexec/mysqld, Version | :5.1.71-log (Source distribution) . started |                                             |
| with:                             |                                             |                                             |
| 2 Tcp port: 3306 Unix socket:     | /data/slave/dbdata/mysql.sock               |                                             |
| 3 Time                            | Id Command    Argument                      |                                             |
| 4    130809 18:43:20              | 5 Query                                     | show variables like * %general_log% *       |
| 5    130809 18:44:24              | 5 Query                                     | update users一my is am set name-'^xx" where |
| name=1petter *                    |                                             |                                             |
| 6 130809 18:44:31                 | 5 Query                                     | SELECT DATABASE()                           |
| 7    5                            | Init DB                                     | testDB 1 —                                  |
| 8    5                            | Query                                       | show databases                              |
| 9    5                            | Query                                       | show tables                                 |
| 10    5                           | Field List    users_myisam                  |                                             |
| 11    130809 18:44:32             | 5 Query                                     | update users_myisam set name=°xxxn where    |
| name= *petter'                    |                                             |                                             |
| 12    130809 18:44:33             | 5 Quit                                      |                                             |
| 13    130809 18:45:00             | 6 Connect root@localhost on                 |                                             |
| 14    6                           | Query                                       | select (Aversion一comment limit 1           |
| 15    130809 18:45:05             | 6 Query                                     | set global general一log=off                 |

上述日志记录了所有客户端的操作，系统管理员可根据此日志发现异常信息以便及时处理。

4.慢查询日志

慢查询日志是记录了执行时间超过参数lOng_qUery_time (单位是秒)所设定值的SQL语 句日志，对于SQL审核和开发者发现性能问题及时进行应用程序的优化具有重要意义。

如需启用该日志可以在配置文件中设置<<slow_query_logw用来指定是否开启慢查询。如 果没有指定文件名，默认hostname-slow.log作为文件名，并存放在数据目录中。示例配置如 下所示。

【示例6-59】

[root@MySQL__192__168_19_101 -]# cat /etc/master.cnf [mysqld]

slow__query_log = 1 long_query_time = 1

log-slow-queries = /usr/local/mysql/data/slow.log log-queries-not-using-indexes

i    J    〜    卜    df. / S 二乂办戸曾 *

;'•'    ；•'. 厂.    '' '■    ■ '    / ■ ' ' .

[root@MySQL_l92__168_19__101 -]# cat /data/mas ter/dbdata/MySQL_l 92__168_19_101-slow.log

/usr/libexec/mysqld, Version: 5.1.71-log (Source distribution). started with: Tcp port: 3306 Unix socket: /data/master/dbdata/mysql.sock Time    Id Command Argument

\#    Time: 130811    0:11:41

\#    User@Host: root[root] @ localhost [】

\#    Query一time: 1.016963 Lock_time: 0.000000 Rows_sent: 1 Rows一examined: 0 SET timestamp^lSVGlSHOl;

select sleep(1);

说明：

long_query_time = 1 #定义超过1秒的查询计数到变量Slow_querieso log-slow-queries = /usr/local/my sql/data/slow. log #定义慢查询日志路径。 log-queries-not-using-indexes #未使用索引的查询也被记录到慢查询日志中(可选)。 MySQL提供了慢查询日志分析的工具mysqldumpslow,可以按时间或出现次数统计慢查

询的情况，常用参数如表6.4所示。

表 6.4 mysqldumpslow 参数说明

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -S   | 排序参数，可选的有： al:平均锁定时间 ar:平均返回记录数 at:平均査询时间 |
| -t   | 只显示指定的行数                                             |

用此工具就可以分析系统中哪些SQL是性能的瓶颈，以便进行优化，比如加索引、优化 应用程序等等。

6.5.3 MySQL备份与，咴复

为防止数据库数据丢失或被非法篡改时恢复数据，数据库的备份是非常重要的。MySQL 的备份方式可以通过直接备份数据文件或使用mysqldump命令将数据库数据导出到文本文件。 直接备份数据库文件适用于MylSAM和InnoDB存储引擎，由于备份时数据库表正在读写， 备份出的文件可能损坏无法使用，不推荐直接使用此方法。另外一种可以实时备份的开源工具

为xtrabackup，本节主要介绍这两种备份工具的使用。

1.使用mysqldump进行MySQL备份与恢复

mysqldump是MySQL提供的数据导出工具，适用于大多数需要备份数据的场景。表数据 可以导出成SQL语句或文本文件，常用的使用方法如【示例6-60】所示。

【示例6-60】

\#导出整个数据库

[root@CentOS 〜]# mysqldump 一u root test>test.sql #导出一个表

[root@CentOS mysqldump -u root test TBL_2 >test.TBL_2.sql #只导出数据库表结构

[root@CentOS 〜]# mysqldump 一u root -d --add~drop-table test>test.sql -d没有数据--add-drop-table在每个create语句之前増加一个drop table #恢复数据库

[root@CentOS # mysql -uroot test<test.sql #恢复数据的另外一种方法 [root@CentOS # mysql -uroot test mysql> source /root/test.sql

mysqldump支持丰富的选项，mysqldump部分选项说明如表6.5所示。

表6.5 mysqldump部分选项说明

| 参数                   | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| -A                     | 等同于-all-databases,导出全部数据库                          |
| -add-drop-database     | 每个数据库创建之前添加drop语句                               |
| -add-drop-table        | 每个数据表创建之前添加drop表语句，默认为启用状态             |
| -add-locks             | 在每个表导出之前增加LOCK TABLES并且之后UNLOCK TABLE,默认为启 用状态 |
| -c                     | 等同于-complete-insert，导出时使用完整的insert语句           |
| -B                     | 等同于-databases，导出多个数据库                             |
| -default-character-set | 设置默认字符集                                               |
| -X                     | 等同于-lock-all-tables,提交请求锁定所有数据库中的所有表，以保证数据的一致性 |
| -1                     | 等同于-lock-tables,开始导出前，锁定所有表                    |
| -n                     | 等同于~no-create-db,只导出数据，而不添加CREATE DATABASE语句  |
| -t                     | 等同于-no-create-info，只导出数据，而不添加CREATE TABLE语句  |
| -d                     | 等同于-nodata，不导出任何数据，只导出数据库表结构            |
| —tables                | 此参数会覆盖-databases (-B)参数，指定需要导出的表名          |
| -w                     | 等同于一WHERE,只导出给定的WHERE条件选择的记录                |

以上给出了 mysqldump常用参数说明，更多的参数含义说明可参考系统帮助“man mysqldump” 0

2.使用Xtrabackup在线备份

使用mysqldump进行数据库或表的备份非常方便，操作简单使用灵活，在小数据量时备 份和恢复时间可以接受，如果数据量较大，mysqldump恢复的时间会很长而难以接受。 xtrabackup是一款高效的备份工具，备份时并不会影响原数据库的正常更新，最新的版本可以 在 [http://www.percona.com/downloads/](http://www.percona.com/downloads/%e4%b8%8b%e8%bd%bd%e3%80%82Xtrabackup)[下载。Xtrabackup](http://www.percona.com/downloads/%e4%b8%8b%e8%bd%bd%e3%80%82Xtrabackup) 提供了 Linux 下常见的安装方式，包括 RPM安装，源码编译方式，以及二进制版本安装，本节以源码安装percona-xtrabackup-2.0.7 为例说明Xtrabackup的使用方法，如【示例6-61】所示。

【示例6-61】

[root@CentOS soft]# tar xvf percona-xtrabackup-2.0.7.tar.gz

\#査看编译帮助信息

[rootGCentOS percona-xtrabackup-2.0.7]# ./utils/build.sh Build an xtrabackup binary against the specified InnoDB flavor.

Usage: build.sh CODEBASE

where CODEBASE can be one of the following values or aliases:

innodb50    I 5.0

build against innodb 5.1 builtin, but

build against built-in 工nnoDB in MySQL

build agsinst InnoDB plugin in MySQL 5.1 build against InnoDB in MySQL 5.5 build against InnoDB in MySQL 5.6

build against Percona Server with



should be compatible with MySQL 5.0

innodb51 builtin | 5.1

5.1

} plugin ]5.5

I 5.6Z xtradb56, mariadblOO J xtradb,mariadb51

mariadb52,mariadb53 I galera55,mariacib55



innodb51

innodt)55

innodb56

xtr.adb51 XtraDB 5.1

build against Percona Server with



xtradb55

XtraDB 5.5

\#编译针对MySQL 5.1版本的二进制文件

innodb51 builtin



[root@CentOS percona-xtrabackup-2.0.7]# ./utils/build.sh    卿

\#编译完成后二进制文件位于percona-xtrabackup-2.0.7/src目录下 [root@CentOS 5.1]# cd /data/xtraback.up/5.1

[root@CentOS 5.1]# cp

/data/soft/percona-xtrabackup~2.0.7/src/xtrabackup_51 .

[root@CentOS 5.1]# cp /data/soft/percona-xtrabackup~2.0.7/innobackupex [root@CentOS 5.1]# export PATH='pwd':$PATH:.

[rootSCentOS 5.1]#./innobackupex--defaults-file=/etc/my.cnf

--sockete/data/mysql一data/m

ysql.sock ~user-root --password-123456 --slave-info /data/backup/

\#部分结果省略

130822 12:13:10 innobackupex: Starting mysql with options: defaults一file=’/etc/my，cnf• 一一password^xxxxxxxx --user-1 root1

--socket^ * /data/mysql__data/fnysql, sock * 一-unbuffered

130822 12:13:10 innobackupex: Connected to database with mysql child process (pid=41953)

130822 12:13:16 innobackupex: Connection to database server closed

IMPORTANT: Please check that the backup run completes successfully.

At the end of a successful backup run innobackupex prints ncompleted OK!".

'

.,■ •    ' ■    :..    ,    • ：;    V-

innobackupex: Using raysql Ver 14.14 Distrib 5.1.49, for unknown-linux-gnu (x86_64) using EditLine wrapper

innobackupex: Using mysql server version Copyright (c) 2000, 2010, Oracle and/or its affiliates. All rights reserved.

innobackupex: Created backup directory /data/backup/2015-08~22_12-13~16 130822 12:13:16 innobackupex: Starting mysql with options:

--defaults-file—1/etc/my.cnf *    password=xxxxxxxx 一-user^1 root1

--socket-1/data/mysql_data/mysql.sock1 --unbuffered --

130822 12:13:16' innobackupex: Connected to database with mysql child process (pid-42688)

130822 12:13:18 innobackupex: Connection to database server closed

130822 12:13:18 innobackupex: Starting ibbackup with command: xtrabackup一51 --defaults-file-M/etc/ray.cnfn --defaults-groupsnmysqldn --backup --suspend-at-end --target-dir=/data/backup/2015-08-22_12-13-16 --tmpdir~/tmp

innobackupex: Waiting for ibbackup (pid-42935) to suspend innobackupex: Suspend file

1 /data/backup/2015-08~22__12-13-16/xtrabaGkup__suspended*

xtrabackup_51 version 2.0.7 for MySQL server 5.1.59 unknown-linux-gnu <x86一64) (revision id: undefined)

xtrabackup: uses posix_fadvise(). xtrabackup: cd to /data/mysql_data

xtrabackup: Target instance is assumed as followings. xtrabackup:    innodfc>__data__hQme__ciir - ./

xtrabackup:    innodb一data一file一path = ibdatal:10M:autoextend

xtrabackup:    innodb一log一group_home_dir = . /

xtrabackup:    innodb_log_files_in_group = 2

xtrabackup:    innodb_log_file_size = 5242880

» log scanned up to (0 1089752)

[01] Copying ./ibdatal to /data/backup/2015-08-22_12-13-16/ibdatal

[01]    " .done

130822 12:13:19 innobackupex: Continuing after ibbackup has suspended

130822 12:13:19 innobackupex:Starting mysql with options:

--defaults-file-1/etc/my. cnf * -~password=xxxxxxxx --user-1 root * --socket^ */data/mysql_data/mysql.sock1 --unbuffered --

130822 12:13:19 innobackupex: Connected to database with mysql child process

(pid=43055)

130822 12:13:21 innobackupex: Starting to lock all tables...

130822 12:13:32 innobackupex: All tables locked and flushed to disk

130822 12:13:34 innobackupex: Starting to backup non-InnoDB tables and files innobackupex: in subdirectories of 1/data/mysql__data1 innobackupex: Backing up file 1/data/mysql_data/test/test_l.frm* innobackupex: Backing up files

•/data/mysql_data/mysql/*.{frm,ARM,ARZ,CSM,CSV ,opt,par}1 (69 files)

130822 12:13:34 innobackupex: Finished backing up non-InnoDB tables and files

130822 12:13:34 innobackupex: Waiting for log copying to finish

•»

xtrabackup: The latest check point (for incremental): '0:1164431 xtrabackup: Stopping log copying thread.

.» log scanned up to (0 1164431) xtrabackup: Transaction log of Isn (0 1059943) to (0 1164431) was copied. 130822 12:13:37 innobackupex: All tables unlocked

130822 12:13:37 innobackupex: Connection to database server closed

innobackupex: Backup created in directory */data/backup/2015-08~22_12-13-16* innobackupex: MySQL binlog position: filename 1mysql-bin.000001 *, position 144432

innobackupex: MySQL slave binlog position: master host " , filename 1 *, position 130822 12:13:37 innobackupex: completed OK!

首先解压源码包，然后使用提供的./utils/build.sh工具进行编译安装，编译时需要指定版本 的MySQL源码，比如mysql-5.1.59.tar.gz，源码可以从MySQL官方网站下载，然后复制到指 定目录，执行编译，编译时可以指定MySQL 5.1和MySQL 5.5，编译完成后二进制文件位于 src目录下，复制到指定位置。

通过设置环境变量PATH指定了二进制文件的寻找路径，然后执行innobackupex脚本备 份文件，脚本执行时指定了 MySQL实例的配置文件和登录方式，如该备份程序从库上运行， 可以指定-slave-info参数，用于记录备份完成时同步的位置。当出现“innobackupex: completed OK!”时说明备份成功。文件的位置位于/data/backup/2015-08-22_12-13-16目录下。

恢复过程如【示例6-62】所示。

【示例6-62】

[rootQCentOS 2015-08-22_12-13-16]# cat -n /etc/new.cnf 1 [mysqld]

2    port    - 3308

3    socket    = /data/mysql__new/mysql. sock

4    datadir*/data/mysql一new

5    log~bin-/data/mysql_new/mysql~bin [root@CentOS 2015-08-22一12-13-16]# xtrabackup_51 prepare

-一target'-dir=se/data/backup/2015-08-22_12~

2015-08-22_12-02~ll/ 2015~08-22__12-07-20/ 2015-08-22一12-08-19/

2015-08-22_12-10~22/ 2015-08-22一12-13-16/

[root0CentOS 2015-08-22_12-13~16]# xtrabackup_51 ~~prepare

--target-dir=/data/backup/2015-08-22_i2-10-22/

xtrabackup一51 version 2.0.7 for MySQL server 5.1.59 unknown-1inux-gnu (x86_64)

(revision id: undefined)

xtrabackup: cd to /data/backup/2015-08~22_12-10-22/ xtrabackup: This target seems to be not prepared yet.

xtrabackup: xtrabackup一logfile detected: size-2097152, start_lsn=(0 703883) xtrabackup: Temporary instance for recovery is set as followings, xtrabackup:    innodb一data一home一dir = ./

xtrabackup:    innodb_data_file_path - ibdatal:10M:autoextend

xtrabackup:    innodb_log 一group一 home一dir ~ /

xtrabackup:    innodb一log_files_in一group = 1

xtrabackup:    innodb一log_file_size = 2097152

xtrabackup: Temporary instance for recovery is set as followings, xtrabackup:    innodb data home dir = ，/

xtrabackup:    innodb_data_file_path « ibdatal:10M:autoextend

xtrabackup:    innodb_log_group_home__dir = ./

xtrabackup:    innodb一log_files一in 一group = 1

xtrabackup:    innodb_log__f ile_size = 2097152

xtrabackup: Starting InnoDB instance for recovery.

xtrabackup: Using 104857600 bytes for buffer pool (set by --use-memory parameter) InnoDB: The InnoDB memory heap is disabled

130822 13:28:19 InnoDB: Initializing buffer pool, size = 100.0M 130822 13:28:19 InnoDB: Completed initialization of buffer pool InnoDB: Log scan progressed past the checkpoint Isn 0 703883 130822 13:28:19 InnoDB: Database was not shut down normally!

InnoDB: Starting crash recovery.

InnoDB: Reading tablespace information from the .ibd files...

InnoDB: Doing recovery: scanned up to log sequence number 0 765235 (3 %) 130822 13:28:19 InnoDB: Starting an apply batch of log records to the database... InnoDB: Progress in percents: 54 55 56 57 58 ,59 60 61 62 63 64 65 66 67 68 69

70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96; 97 98 99

InnoDB: Apply batch completed

130822 13:28:20 InnoDB: Started; log sequence number 0 765235

[notice (again)}

If you use binary log and don't use any hack of group commit, the binary log position seems to be:

xtrabackup: starting shutdown with innodt>__fast一shutdown = 1 130822 13:28:20 InnoDB: Starting shutdown...

130822 13:28:25 InnoDB: Shutdown completed; log sequence number 0 765235 [root@CentOS 2015-08-22一12-13-16]# xtrabackup_51 --prepare

--targette-dir=/data/backup/2015-08-22__12~10-22/

xtrabackup_51 version 2.0.7 for MySQL server 5.1.59 unknown-linux-gnu (x86_64)

(revision id: undefined)

xtrabackup: cd to /data/backup/2015-08-22_12-l0-22/ xtrabackup: This target seems to be already prepared.

xtrabackup: notice: xtrabackup__logfile was already used to '--prepare 1. xtrabackup: Temporary instance for recovery is set as followings. xtrabackup:    innodb_data_home^dir « ./

xtrabackup:    innodb_data_file_path - ibdatal:10M:autoextend

xtrabackup:    innodb一log_group一 home一dir = ./

xtrabackup:    innodb_log_files_in_group = 2

xtrabackup:    innodb_log_file_size = 5242880

xtrabackup: Temporary instance for recovery is set as followings. xtrabackup:    innodb__data_home__dir = . /

xtrabackup:    innodb一data一file一 path = ibdatal:10M;autoextend

xtrabackup:    innodb_log__group__home__dir = . /

xtrabackup:    innodb_log_files_in_group = 2

xtrabackup:    irmodb_log一file一size = 5242880

xtrabackup: Starting InnoDB instance for recovery.

xtrabackup: Using 104857600 bytes for buffer pool (set by use-memory parameter) InnoDB: The InnoDB memory heap is disabled

130822 13:28:26 InnoDB: Initializing buffer pool, size - 100.0M 130822 13:28:26 InnoDB: Completed initialization of buffer pool 130822 13:28:26 InnoDB: Log file ./ib_logfileO did not exist: new to be created InnoDB: Setting log file ./ib_logfileO size to 5 MB

InnoDB: Database physically writes the file full: wait...

130822 13:28:26 InnoDB: Log file ./ib一logfilel did not exist: new to be created InnoDB: Setting log file ./ib_logfilel size to 5 MB InnoDB: Database physically writes the file full: wait...

InnoDB: The log sequence number in ibdata files does not match InnoDB: the log sequence number in the ib_logfiles!

130822 13:28:26 InnoDB: Database was not shut down normally!

InnoDB; Starting crash recovery.

InnoDB: Reading tablespace information from the .ibd files...

130822 13:28:26 InnoDB: Started; log sequence number 0 765452

[notice (again)]

If you use binary log and don’t use any hack of group commit, the binary log position seems to be:

xtrabackup: starting shutdown with innodb_fast_shutdown - 1 130822 13:28:26 InnoDB: Starting shutdown."

130822 13:28:32 InnoDB: Shutdown completed; log sequence number 0 765452-[root@CentOS ~3 # mkdir ~p /data/mysql一new [rootGCentOS 〜H chown ~R mysql.mysql /data/mysql_new

/data/backup/2015-08~22_12-13-16/

[root@CentOS ~]# mv /data/backup/2015-08~22_12~13-16/* /data/mysql一new [root@CentOS 〜mysqld_safe -~defaults-file~/etc/new.cnf ~~user=mysql & [root@CentOS -]# mysql -S /data/mysql__new/mysql.sock -plZ3456 mysql> select * from test，test一1 limit 3;

I a lb. {

I 123450 J 123450 f I 27175 I 22781 \

I    8802 I    2618 !

3 rows in set (0.00 sec)

6.5.4 MySQL 复芾ij

借助MySQL提供的复制功能，应用者可以经济高效地提高应用程序的性能、扩展力和高 可用性。全球许多流量最大的网站都通过MySQL复制来支持数以亿计、呈指数级增长的用户 群，其中不乏eBay、Facebook、Tumblr、Twitter和YouTube等互联网巨头。MySQL复制，既 支持简单的主从拓扑，也可实现复杂、极具可伸缩性的链式集群。

![img](11 CentOS7fbdfa1060ed0f49e18-132.jpg)



当使用MySQL复制时，所有对复制中的表的更新必须在主服务器上进行。否则可能引起 主服务器上的表进行的更新与对从服务器上的表所进行的更新产生冲突。

利用MySQL的复制有以下好处:

(1)    增加MySQL服务健壮性

数据库复制功能实现了主服务器与从服务器之间数据的同步，增加了数据库系统的可用 性。当主服务器出现问题时，数据库管理员可以马上让从服务器作为主服务器以便接管服务。 之后有充足的时间检查主服务器的故障。

(2)    实现负载均衡

通过在主服务器和从服务器之间实现读写分离，可以更快地响应客户端的请求。如主服务 器上只实现数据的更新操作，包括数据记录的更新、删除、插入等操作，而不关心数据的查询 请求。数据库管理员将数据的查询请求全部转发到从服务器中。同时通过设置多台从服务器处

理用户的查询请求。

通过将数据更新与查询分别放在不同的服务器上进行，既可以提高数据的安全性，同时也 缩短应用程序的响应时间、提高系统的性能。用户可根据数据库服务的负载情况灵活、弹性地 添加或删除实例，以便动态按需调整容量。

(3)实现数据备份

首先通过MySQL的实时复制数据从主服务器上复制到从服务器上，从服务器可以设置在 本地也可以设置在异地，从而增加了容灾的健壮性，为避免异地传输速度过慢，MySQL服务 器可以通过设置参数slave_compressed_protocol启用binlog压缩传输，数据传输效率大大提高， 通过异地备份增加了数据的安全性。

当使用mysqldump导出数据进行备份时如果作用于主服务器可能会影响主服务器的服务， 而在从服务器进行数据的导出操作不但能达到数据备份的目的而且不会影响主服务器上的客 户请求。

MySQL使用3个线程来执行复制功能，其中1个在主服务器上，另2个在从服务器上。 当执行START SLAVE时，主服务器创建一线程负责发送二进制日志。从服务器创建一个I/O 线程，负责读取主服务器上的二进制日志，然后将该数据保存到从服务器数据目录中的中继曰 志文件中。从服务器的SQL线程负责读取中继日志并重做日志中包含的更新，从而达到主从 数据库数据的一致性。整个过程如【示例6-63】所示。

【示例6-63】

\#主服务器上/ SHOr PROCESSLIST的输出

mysql> show processlist \G    •

******************** ******* I， row ***************************

.Id: 2

User •‘ rep

Host: 192.168.19.102:43986 db: NULL

Command: Binlog Dump Time: 100

State: Has sent all binlog to slave; waiting for binlog to be updated Info: NULL

\#在从服务器上，SHOW PROCESSLIST的输出 mysql> show processlist \G ★ ★*★■*★★*★***★ *'*-'**★★*★**•**★★ j row Id: 5

User: system user Host: db: NULL Command: Connect Time: 46

State: Waiting for master to send event

Info: NULL

**★■**★*★ *****★****•*••**★★★** 2 2?OW ^**************************

Id: 6

User: system user Host:

db： NULL Command: Connect Time: 125

State: Has read all relay log; waiting for the slave I/O thread to update it Info: NULL

2 rows in set <0.00 sec)

这里，线程2是一个连接从服务器的复制线程。该信息表示所有主要更新已经被发送到从 服务器，主服务器正等待更多的更新出现。

该信息表示线程5是同主服务器通信的I/O线程，线程6是处理保存在中继日志中的更新 的SQL线程。SHOWPROCESSLIST运行时，两个线程均空闲，等待其他更新。

![img](11 CentOS7fbdfa1060ed0f49e18-133.jpg)



Time列的值可以显示从服务器比主服务器滞后多长时间(

6.5.5 MySQL复制搭建过程

本节示例涉及的主从数据库信息为：主MySQL服务器192.168.19.101:3306，从MySQL 服务器为192.168.19.102:3306。为便于演示主从复制的部署过程，以上两个实例都为新部署的 实例。

(1)    确认主从服务器上安装了相同版本的数据库，本节以MySQL 5.1.71为例。

(2)    确认主从服务器已经启动并正常提供服务，主从服务器的关键配置如下：

【示例6-64】

| [root@CentOS 〜】#              | cat             | ~n /etc/master.cnf            |
| ------------------------------- | --------------- | ----------------------------- |
| 1 [mysqld]                      |                 |                               |
| 2 bind-address =                | =192.168.19.101 |                               |
| 3 port                          | =               | 3306                          |
| 4 log-bin                       | -               | Zdata/master/binlog/mysql-bin |
| 5 server-id                     | —               | 1                             |
| 6 datadir                       |                 | /data/master/dbdata           |
| [rootGCentOS 〜                 | .cat:           | /etc/slave.cnf                |
| 1 [mysqld]                      |                 |                               |
| 2 bind-address = 192.168.19.102 |                 |                               |
| 3 port                          | =               | 3306                          |
| 4 log-bin                       | =               | /data/slave/binlog/mysql-bin  |
| 5 server-id                     |                 | 2                             |
| 6 datadir                       | =               | /data/slave/dbdata            |

(3)在MySQL主服务器上，分配一个复制使用的账户给MySQL从服务器，并授予 replication slave 权 P艮。

【示例6-65】

mysql> grant replication slave on *.* to [rep@192.168.19.102](mailto:rep@192.168.19.102); Query OK, 0 rows affected (0.00 sec)

(4)登录主服务器得到当前binlog的文件名和偏移量。

【示例6-66】

mysql> show master logs;

+•------------------+-----------+

| Log__name    I File_size |



5 rows in set (0.01 sec)

(5)登录从服务器设置主备关系

对从数据库服务器做相应的设置，指定复制使用的用户、主服务器的IP、端口，开始执 行复制的文件和偏移量等。

【示例6-67】

mysql> change master to

| ->        | master一host='192.168.19.1011,         |
| --------- | -------------------------------------- |
| ->        | master_port=3306z                      |
|           | master一user= ’ rep1,                  |
| ->        | masterjpassword:",                     |
|           | master一log一file='mysql-bin.000005 *, |
| ->        | master_log_pos=233;                    |
| Query OK, | 0 rows affected (0.05 sec)             |

(6)登录从服务器上启动slave线程并检查同步状态

【示例6-68】

mysql〉 start slave;

Query OK, 0 rows affected (0.01 sec)

mysql> show slave status \G

*★■*•***************★*★■*****    row. ■**★****■*******■*******★****★

Slave_IO一State: Waiting for master to send event Master__Host: 192.168,19.101

Master一User: rep

Master_Port: 3306 Connect一Retry: 60

Master_Log__File: mysql-bin. 000006 Read_Master_Log__Pos: 106

Relay_Log_File: CentOS-relay~bin.000004 Relay_Log_Pos: 251



Relay__Master__Log__File: mysql-bin Slave_JO一Running: Yes Slave_SQL_Running: Yes

Replicate__Do_DB:

Replicate一Ignore一DB:

Replicate一Do一Table: Replicate_Ignore_Table:

Replicate_Wild_Do_Table: Replicate_Wild_Ignore__Table:

Last一Errno: 0 Last一Error:

Skip一Counter: 0 Exec__Master__Log_Pos: 106

Relay一Log一Space: 679 Until一Condition: None Until一Log_File:

Until一Log_Pos: 0 Master一SSL一 Allowed: No Master__SSL_CA__File: Master_SSL__CA_Path:

Master__SSL_Cert: Master__SSL__Cipher:

Master一SSL一Key:

Seconds一Behind一Master: 0

Master_SSL_Verify_Server_Cert: No

Last__IO_Errno: 0

Last__IO_Error:

Last一SQL_Errno: 0 I»ast_SQL 一 Error:

1 row in set (0.01 sec)

![img](11 CentOS7fbdfa1060ed0f49e18-135.jpg)



如Slave_IO_Running和Slave_SQL_Running都为YES说明主从已经正常工作了。如其中 一个为NQ则需要根据Last_IO_Errno和Last_IO_Error显示的信息定位主从同步失败的原因,

(7)主从同步测试

【示例6-69】

\#登录主服务器执行

[root@CentOS -3 # mysql -S /data/master/dbdata/mysql.sock

mysql> create database ms;

Query OK, 1 row affected (0.03 sec) mysql> show master logs;

十一 •一 <**    — -w —•*«*•—-*■ — -^***—* — ■ ■*** • *■* -*• +

i Log_name    i File一size I

\+ 一 一    一 — ■一 w    一一一 十-*• •** 一 •*» — * ** •*•«*•*!•

I

| mysql-bin.000001 }  | 125 J  |
| ------------------- | ------ |
| mysql~bin.000002 J  | 106 \| |
| mysql-bin.000003 J  | 106 f  |
| mysql-bin.000004 \| | 106 r  |
| mysql-bin.000005 1  | 360 I  |
| mysql-bin.000006 J  | 185 \| |



6 rows in set (0.00 sec)

\#登录从服务器执行 mysql> show databases;

十-

i



|                        |           |
| ---------------------- | --------- |
| Database               | I         |
| information^           | —schema 1 |
| ms                     | 1         |
| mysql                  | 1         |
| test-一 一 —•—一—»——， | 1         |

[rows in set (0.00 sec)



mysql> show slave status \G ★*★**★**★***********★*★****

Slave一I。一State: Master一Host: Master一User: Master_Port::

Connect一Retry:



■i. row *************************** ■ ■ ■ .

Waiting for master to send event

192.168.19.101

rep

3306

60



Master_Log_File: mysql-bm. 000006 Read_Master_Lo^_Pos: 185

Relay_Log_File: CentOS一relay-bin.000004 Relay_l»og_Pos: 330

Relay__Master一Log一File: mysql-bin. 000006 Slave一10一 Running: Yes.

Slave一SQL一Running: Yes Repl icate__Do_DB:

Replicate_Ignore__DB:

Replicate_Do_Table:

Replicate一Ignore一Table: Replicate_Wild__Do_Table:

Replicate_Wild_Ignore_Table:

Last一Errno: 0 Last一Error: Skip—Counter: 0 Exec_Master_Log_Pos: 185 Relay_Log_Space: 758 Until_Condition: None Unti1一Log_File: Until_Log_Pos: 0 Master一SSL一 Allowed: No Master一SSL一CA一File: Master_SSL_CA_Path: Master_SSL一Cert:

Master一SSL一Cipher: Master一SSL一Key: Seconds一Behind一Master: 0 Master_SSL一Verify一Server一Cert: No Last一10一Errno: 0 Last_IO一Error: Last_SQL__Errno: 0 Last_SQL_Error:

1 row in set (0.00 sec)

首先登录主数据库，然后创建了表，同时此语句会写入到主数据库的binlog日志中，从 数据库的IO线程读取到该日志写入到本地的中继日志，从数据库的SQL线程重新执行该语 句，从而实现主从数据库数据一致。

###### 6.6小结

本章首先介绍了 HTTP协议，通过此协议，读者可以了解HTTP的原理及其常见返回码代 表的含义，返回码在日常程序调试中具有重要的作用。通过介绍Apache服务安装与配置，使 读者了解了 Apache服务常见的3种虚拟主机配置方法，其中基于域名的虚拟主机配置是使用 比较广泛的一种，需重点掌握。通过PHP的安装与配置介绍了 PHP如何与Apache服务集成, 集成后就可以通过Apache访问PHP文件了，最后对MySQL的日常维护给出了操作示例。
