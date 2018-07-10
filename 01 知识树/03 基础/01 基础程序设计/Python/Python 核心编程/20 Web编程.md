Web编程

![img](07Python38c3160b-2740.jpg)



![img](07Python38c3160b-2741.jpg)



![img](07Python38c3160b-2742.jpg)



本章主题

*引言

•    Python的Web应用：简单的Web客户端

•    urlparse 和 urllib 模块

•髙级的Web客户端

•网络爬虫/蜘蛛/机器人

•    CGI:帮助Web服务器处理客户端数据 •创建CGI应用程序

•在CGI中使用Unicode

•髙级CGI

•创建Web服务器

•相关模块

![img](07Python38c3160b-2743.jpg)



![img](07Python38c3160b-2744.jpg)



![img](07Python38c3160b-2745.jpg)



![img](07Python38c3160b-2746.jpg)



![img](07Python38c3160b-2747.jpg)



![img](07Python38c3160b-2748.jpg)



![img](07Python38c3160b-2749.jpg)



![img](07Python38c3160b-2750.png)



### 20.1 介绍

本章是有关Web编程的介绍，可以帮助你对出Python在因特网上的各种基础应用有个概要了 解，例如通过Web页面建立用户反馈表单，通过CGI动态生成输出页面

### 20.1.1 Web 应用：客户端/服务器计算

Web应用遵循我们反复提到的客户端/服务器架构。这里，Web的客户端是浏览器，应用程序允 许用户在万维网上查询文档。另外Web服务器端，进程运行在信息提供商的主机上。这些服务器等 待客户和文档请求，进行相应的处理，返回相关的数据。正如大多数客户端/服务器的服务器端一样， Web服务器端被设置为“永远”运行。图20-1列举了 Web应用的体验。这里，一个用户执行一个像 浏览器的这类客户端程序与Web服务器取得连接，就可以在因特网上任何地方获得数据。

![img](07Python38c3160b-2751.jpg)



图20-1因特网上的Web客户端和Web服务器。在因特网上客户端向服务器端发送一个请求，然 后服务器端响应这个请求并将相应的数据返回给客户端。

![img](07Python38c3160b-2752.jpg)



![img](07Python38c3160b-2753.jpg)



![img](07Python38c3160b-2754.jpg)



![img](07Python38c3160b-2755.jpg)



![img](07Python38c3160b-2756.jpg)



![img](07Python38c3160b-2757.jpg)



客户端可能向服务器端发出各种请求。这些请求可能包括获得一个网页视图或者提交一个包含

数据的表单。这个请求经过服务器端的处理，然后会以特定的格式（HTML等等）返回给客户端浏览。

Web客户端和服务器端交互使用的“语言”，Web交互的标准协议是HTTP （超文本传输协议、。HTTP 协议是TCP/IP协议的上层协议，这意味着HTTP协议依靠TCP/IP协议来进行低层的交流工作。它的 职责不是路由或者传递消息（TCP/IP协议处理这些、，而是通过发送、接受HTTP消息来处理客户端 的请求。

HTTP 协议属于无状态协议，它不跟踪从一个客户端到另一个客户端的的请求信息，这点和我们 现今使用的客户端/服务器端架构很像。服务器端持续运行，但是客户端的活动是按照这种结构独立 进行的：一旦一个客户的请求完成后，活动将被终止。可以随时发送新的请求，但是他们会被处理 成独立的服务请求。由于每个请求缺乏上下文背景，你可以注意到有些URL会有很长的变量和值作 为请求的一部分，以便提供一些状态信息。另外一个选项是“cookie” --保存在客户端的客户状态 信息。在本章的后面部分，我们将会看到如何使用URL和cookie来保存状态信息。

### 20.1.2 因特网

因特网是一个连接全球客户端和服务器端的变幻莫测的“迷雾”。客户端最终连接到服务器的 H通路，实际包含了不定节点的连通。作为一个客户端用户，所有这些实现细节都会被隐藏起来。抽 象成为了从客户端到所访问的服务器端的直接连接。被隐藏起来的HTTP，TCP/IP协议将会处理所

![img](07Python38c3160b-2758.jpg)



有的繁重工作。中间的环节信息用户并不关心，所以将这些执行过程隐藏起来是有好处的。图 20-2 展示了因特网的扩展视图。

![img](07Python38c3160b-2759.jpg)



![img](07Python38c3160b-2760.jpg)



![img](07Python38c3160b-2761.jpg)



![img](07Python38c3160b-2762.jpg)



![img](07Python38c3160b-2763.jpg)



Home User



![img](07Python38c3160b-2765.jpg)



Modem

ISP Network

Server

ISP Network

External Server

n Intranet

Internet Core

Colocated .com Servers

Server

Hoitie

C lent Modem

The Internet

hueriKi Server

C ern

厂i 丁

Corporate Local Area Network

Corporate Web Site (Network)



![img](07Python38c3160b-2767.jpg)



图20-2因特网的统览。左侧指明了在哪里你可以找到Web客户端，而右侧则暗示了 Web服务

器的具体位置。

如图所示：因特网是由多种工作在一定规则下的（也许非连贯的）相互连接的网络组成的。图

表左侧的焦点是Web客户端，在家上网的用户通过拨号连接到ISP （因特网供应商）上，上班族使用 的则是公司的局域网。

图表的右半部分关注的是Web服务器端及位置所在。具有大型Web站点的公司会将他们全部的 “Web服务器”放在ISP那里。这种物理安放被称为“整合”，这意味着你的服务器和其它客户的服 务器一同放在ISP处被“集中管理”。这些服务器或许为客户提供了不同的数据或者有一部分为应付 重负荷（高数量用户群）而设计成了可以存储重复数据的系统。小公司的Web站点或许不需要这么 大的硬盘或者网络设备，也许仅有一个或者几个“整合”服务器安放在他们的ISP处就可以了。

在任何一种情况下，大多数“整合”服务器被部署在大型ISP提供的骨干网上，这意味着他们 具有更髙的“带宽”，如果你愿意，可以更接近因特网的核心点，从而可以更快的与因特网取得连接。



这就允许客户端可以绕过许多网络直接快速的访问服务器，从而在指定的时间内可以使得更多的客

户获得服务。

有一点需要记清楚，Web应用是网络应用的一种最普遍的形式，但不是唯一的也不是最古老的一 种形式。因特网的出现早于Web近三十年。在Web出现之前，因特网主要用于教学和科研目的。因 特网上的大多数系统都是运行在Unix平台上的一一个多用户操作系统，许多最初的因特网协议至今 仍被沿用。

这些协议包括telnet （允许用户在因特网上登录到远程的主机上，至今仍用），FTP协议（文本 传输协议，用户通过上传和下载文件可以共享文件和数据，至今仍用），Gopher（Web搜索引擎的雏 形一一个在互联网上爬动的小软件“gopher”可以自动寻找你感兴趣的数据），SMTP或者叫做简单邮 件传输协议（这个协议用于最古老的也是应用最广泛的电子邮件），NNTP （新闻对新闻传输协议）。

由于Python的最初偏重就是因特网编程，除了其他一些东西外你还可以找到上边提及的所有协 议。可以这样区分“因特网编程”和“Web编程”后者仅包括针对Web的应用程序开发，也就是说 Web客户端和服务器是本章的焦点。

因特网编程涵盖更多范围的应用程序：包括我们之前提及的一些因特网协议，例如：FTP，SMTP 等，同时也包括我们前一章提到的网络编程和套接字编程。

### 20.2使用Python进行Web应用：创建一个简单的Web客户端

有一点需要记清楚，浏览器只是Web客户端的一种。任何一个通过向服务器端发送请求来获得 数据的应用程序都被认为是“客户端”。当然，也可以建立其他的客户端从而在因特网上检索出文档 和数据。这样做的一个重要原因就是浏览器的能力有限，也就是说，它主要用于查看并同其他Web 站点交互。另一方面，一个客户端程序，有能力做得更多—它不仅可以下载数据，同时也可以存储、 操作数据，甚或可以将其传送到另外一个地方或者传给另外一个应用。

一个使用urllib模块下载或者访问Web上的信息的应用程序［使用urllib.urlopen（）或者 urllib.urlre- trieveO］可以被认为是简单的Web客户端。你所要做的就是提供一个有效的Web地 址。

### 20.2.1 统一资源定位符

简单的Web应用包扩使用被称为URL （统一资源定位器）的Web地址。这个地址用来在Web上定 位一个文档，或者调用一个CGI程序来为你的客户端产生一个文档。URL是大型标识符URI（统一资 源标识）的一部分。这个超集是建立在已有的命名惯例基础上的。一个URL是一个简单的URI，使用 已存在的协议或规划（也就是http，ftp等）作为地址的一部分。为了进一步描绘这些，我们将会

引入non-URL的URI，有时这些被成为URN （统一资源名称、，但是在今天我们唯一使用的一种URI 是URL，至于URI和URN你也许没有听到太多，这或许已被保存成XML标识符了。



如街道地址一样，Web地址也有一些结构。美国的街道地址通常是这种格式“号码街道名称”， 例如123主大街。这个和其他国家不同，他们有自己的规则。URL使用这种格式：

prot_sch://net_loc/path;params?query#frag

| Table 20.1 | Web Address Components                    |
| ---------- | ----------------------------------------- |
| URL部件    | 描述                                      |
| prot_sch   | 网络协议或者下载规划                      |
| net_loc    | 服务器位置（或许也有用户信息）            |
| path       | 斜杠（/ ）限定文件或者CGI应用程序的路径。 |
| Params     | 可选参数                                  |
| query      | 连接符（ & ）连接键值对                   |
| frag       | 拆分文档中的特殊锚                        |

表20.1描述了各个部件

![img](07Python38c3160b-2772.jpg)



net_loc可以进一步拆分成多个部件，有些是必备的，其他的是可选部件，net_loc字符串如

![img](07Python38c3160b-2773.jpg)



![img](07Python38c3160b-2774.jpg)



下：

user:passwd@host:port

表20.2中分别描述了这些部件。

在这四个当中，host主机名是最重要的。端口号只有在Web服务器运行其他非默认端口上时才 会被使用。（如果你不确定所使用的端口号，可以参到第十六章、。

用户名和密码部分只有在使用FTP连接时候才有可能用到，因为即使是使用FTP，大多数的连接 都是使用匿名这时是不需要用户名和密码的。

表20.2网络定位部件

net_loc

部件    描述

user    登录名

password 用户的密码

host    Web服务器运行的机器名或地址（必须字段、

port    端口号（默认80、

![img](07Python38c3160b-2775.jpg)



Python支持两种不同的模块，分别以不同的功能和兼容性来处理URL。一种是urlparse，一种 是urllib。这里我们将会简单的介绍下它们的功能。

20.2.2 urlparse 模块

urlpasrse模块提供了操作URL字符串的基本功能。这些功能包括urlparse(), urlunparse() 和 urljoin().

urlparseO将URL字符串拆分成如上所描述的一些主要部件。语法结构如下：

urlparse(urlstr, defProtSch=None, allowFrag=None)

urlparse()将 urlstr 角军析成一个 6-元组(prot_sch, net_loc,path, params, query, frag).这里的每个部件在上边已经描述过了。如果urlstr中没有提供默认的网络协议或下载规划时 可以使用defProtSch。allowFrag标识一个URL是否允许使用零部件。下边是一个给定URL经 urlparse() 后的输出：

〉〉〉urlparse. urlparseChttp://www. python. org/doc/FAQ. html，)

('http', ’www.python.org' ’/doc/FAQ.html’， ', ', ')

#### urlparse.urlunparse()

urlunparseO 的功能与 urlpaseO 完全相反一它拼合一个 6-元组(prot_sch, net_loc, path, params, query, frag) - urltup,它可能是一个URL经urlparse()后的输出返回值。于是，我们可 以用如下方式表示：

urlunparse(urlparse(urlstr)) = urlstr

你或许已经猜到了 urlunpase()的语法：

urlunparse(urltup)

#### urlparse.urljoin()

在需要多个相关的URL时我们就需要使用urljoin()的功能了，如，在一个Web页中生成的一系 列页面的URLaUrljoin()的语法是：

urljoin(baseurl, newurl, allowFrag=None)

Table 20.3 Core urlparse Module Functions

![img](07Python38c3160b-2778.jpg)



![img](07Python38c3160b-2779.jpg)



![img](07Python38c3160b-2780.jpg)



![img](07Python38c3160b-2781.jpg)



urlparse 功能 urlparse(urlstr， defProtSch=None， allowFrag=None)

描述



urlunparse(urltup) urljoin(baseurl， newurl， allowFrag =None)



将urlstr解析成各个部件，如果在rulstr中没有给定协议 或者规划将使用defProtSch;llowFrag决定是否允许有URL 零部件。

将URL数据(urltup)的一个元组反解析成一个URL字符串。

将URL的基部件baseurl和newurl拼合成一个完整的URL； allowFrag的作用和urlpase()中相同。

urljoin()取得baseurl，并将其基路径(net_loc附加一个完整的路径，但是不包括终端的文件) 与newurl连接起来。例如：

\>>> urlparse.urljoin('[http://www.python.org/doc/FAQ.html'，](http://www.python.org/doc/FAQ.html'%ef%bc%8c) \

... 'current/lib/lib.htm')

'<http://www.python.org/doc/current/lib/lib.html'>

在表20.3中可以找到urlparse的功能概述。

### 20.2.3 urllib 模块

#### 核心模块：urllib

urllib模块提供了所有你需要的功能，除非你计划写一个更加低层的网络客户端。urllib提供 了 了一个高级的Web交流库，支持Web协议，HTTP，FTP和Gopher协议，同时也支持对本地文件的 访问。urllib模块的特殊功能是利用上述协议下载数据(从因特网、局域网、主机上下载)。使用这 个模块可以避免使用httplib，ftplib和gopherlib这些模块，除非你想用更低层的功能。在那些 情况下这些模块都是可选择的(注意：大多数以*lib命名的模块用于客户端相关协议开发。并不是所 有情况都是这样的，或许urllib应该被命名为“internetlib”或者其他什么相似的名字)。

Urllib模块提供了在给定的URL地址下载数据的功能，同时也可以通过字符串的编码、解码来 确保它们是有效URL字符串的一部分。我们接下来要谈的功能包括urlopen()， urlretrieve()， quote()，unquote()， quote_plus()， unquote_plus()，和 urlencode ()。我们可以使用 urlopenO方法返回文件类型对象。你会觉得这些方法不陌生，因为在第九章我们已经涉及到了文件 方面的内容。

#### urllib.urlopen()

urlopenO打开一个给定URL字符串与Web连接，并返回了文件类的对象。语法结构如下：

![img](07Python38c3160b-2783.jpg)



urlopen(urlstr, postQueryData=None)

urlopenO打开urlstr所指向的URL。如果没有给定协议或者下载规划，或者文件规划早已传入，



urlopenO则会打开一个本地的文件。

对于所有的HTTP请求，常见的请求类型是“GET”。在这些情况中，向Web服务器发送的请求字 符串(编码键值或引用，如urlencodeO函数的字符串输出［如下］)应该是urlstr的一部分。

如果要求使用“POST”方法，请求的字符串(编码的)应该被放到postQueryData变量中。(要 了解更多关于“GET”和“POST”方法的信息，请查看CGI应用编程部分的普通文档或者文本，这些 我们在下边也会讨论)。GET和POST请求是向Web服务器上传数据的两种方法。

一旦连接成功，urlopenO将会返回一个文件类型对象，就像在目标路径下打开了一个可读文 件。例如，如果我们的文件对象是f，那么我们的“句柄”将会支持可读方法如：f.readO, f.readline(), f.readlines(), f.close(),和 f.fileno().

此外，f.infoO方法可以返回 MIME (Multipurpose Internet Mail Extension，多目标因特 网邮件扩展)头文件。这个头文件通知浏览器返回的文件类型可以用哪类应用程序打开。例如，浏 览器本身可以查看HTML(HyperText Markup Language，超文本标记语言)，纯文本文件，生成(指

由数据显示图像--译者注)PNG (Portable Network Graohics)文件，JPEG (Joint Photographic

![img](07Python38c3160b-2785.jpg)



Experts Group)或者GIF(Graphics Interchange Format)文件。其他的如多媒体文件，特殊类

H型文件需要通过扩展的应用程序才能打开。

Table 20.4 urllib.urlopen() File-like Object Methods

urlopen() 对象方法 描述



f.read([bytes])

f.readline()

f.readlines()

f.close()

f.fileno()

f.info()

f.geturl()



从f中读出所有或bytes个字节

从f中读出一行

从f中读出所有行并返回一个列表 关闭f的URL的连接 返回f的文件句柄 获得f的MIME头文件 返回f所打开的真正的URL

最后，geturlO方法在考虑了所有可能发生的间接导向后，从最终打开的文件中获得真实的URL， 这些文件类型对象的方法在表20.4中有描述。

如果你打算访问更加复杂的URL或者想要处理更复杂的情况如基于数字的权限验证，重定位，

coockie等问题，我们建议你使用urllib2模块，这个在1.6版本中有介绍(多数是试验模块)。它



同时还有一个urlopenO函数，但也提供了其他的可以打开各种URL的函数和类。关于urllib2的更 多信息，将会在本章的下一部分介绍。

#### urllib.urlretrieve()

如果你对整个URL文档的工作感兴趣，urlretrieveO可以帮你快速的处理一些繁重的工作。下 边是urlretrieveO的语法：

urlretrieve(urlstr， localfile=None， downloadStatusHook=None)

除了像urlopenO这样从URL中读取内容，urlretrieveO可以方便地将urlstr定位到的整个 HTML文件下载到你本地的硬盘上。你可以将下载后的数据存成一个本地文件或者一个临时文件。如 果该文件已经被复制到本地或者已经是一个本地文件，后续的下载动作将不会发生。

如果可能，downloadStatusHook这个函数将会在每块数据下载或传输完成后被调用。调用时使 用下边三个参数：目前读入的块数，块的字节数和文件的总字节数。如果你正在用文本的或图表的 视图向用户演示“下载状态”信息，这个函数将会是非常有用的。

urlretrieve()返回一个2-元组，(filename，mime_hdrs).filename是包含下载数据的本地文 件名，mime_hdrs是对Web服务器响应后返回的一系列MIME文件头。要获得更多的信息，可以看 mimetools的Message类。对本地文件来说mime_hdrs是空的。

关于urlretrieveO的简单应用，可以看11.4(grabweb.py)中的例子。在本章的20.2中将会 介绍urlretrieveO更深层的应用。

urllib.quote() and urllib.quote_plus()

quote*()函数获取URL数据，并将其编码，从而适用于URL字符串中。尤其是一些不能被打印 的或者不被Web服务器作为有效URL接收的特殊字符串必须被转换。这就是quote*()函数的功能。 quote*()函数的语法如下：

quote(urldata， safe='/')

逗号，下划线，句号，斜线和字母数字这类符号是不需要转化。其他的则均需要转换。另外， 那些不被允许的字符前边会被加上百分号(%)同时转换成16进制，例如：“％xx”，“xx”代表这个字母 的ASCII码的十六进制值。当调用quote*()时，urldata字符串被转换成了一个可在URL字符串中 使用的等价值。safe字符串可以包含一系列的不能被转换的字符。默认的是斜线(/).

quote_plus()与quote()很像，另外它还可以将空格编码成+号。下边是一个使用quote()和 quote_plus()的例子:

\>>> name = 'joe mama

〉〉〉 number = 6

〉〉〉 base = ’[http://www/~foo/cgi-bin/s.py’](http://www/~foo/cgi-bin/s.py%e2%80%99)

〉〉〉 final = ’%s?name=%s&num=%d’ % (base, name, number)

〉〉〉 final

’<http://www/~foo/cgi-bin/s.py?name=joe> mama&num=6’

〉〉〉

〉〉〉 urllib.quote(final)

’http:%3a//www/%7efoo/cgi-bin/s.py%3fname%3djoe%20mama%26num%3d6’

〉〉〉

〉〉〉 urllib.quote_plus(final)

’http%3a//www/%7efoo/cgi-bin/

s.py%3fname%3djoe+mama%26num%3d6’

#### urllib.unquote() 和 urllib.unquote_plus()

也许和你猜到的一样，unquote*()函数与quote*()函数的功能安全相反一它将所有编码为“°%xx” 式的字母都转换成它们的ASCII码值。Unquote*()的语法如下：

unquote*(urldata)

调用unquote()函数将会把urldata中所有的URL-编码字母都解码，并返回字符串。 Unquote_plus()函数会将加号转换成空格符。

#### urllib.urlencode()

在1.5.2版的Python中，urlopen()函数接收字典的键-值对，并将其编译成CGI请求的URL字 符串的一部分。键值对的格式是“键=值”,以连接符(&)划分。更进一步，键和它们的值被传到 quote_plus()函数中进行适当的编码。下边是urlencode()输出的一个例子：

〉〉〉 aDict = { ’name’: ’Georgina Garcia’, ’hmdir’: ’~ggarcia’ }

〉〉〉 urllib.urlencode(aDict)

’name=Georgina+Garcia&hmdir=%7eggarcia’

urllib和urlparse还有一些其他的功能，在这里我们就不一一概述了。阅读相关文档可以获得

更多信息。

#### 安全套接字层支持

在1.6版中urllib模块通过安全套接字层(SSL)支持开放的HTTP连接.socket模块的核心变化 是增加并实现了 SSL。随后，urllib和httplib模块被上传用于支持URL在“https”连接规划中 的应用。除了那两个模块以外，其他的含有SSL的模块还有：imaplib, poplib和smtplib。

![img](07Python38c3160b-2791.jpg)



![img](07Python38c3160b-2792.jpg)



![img](07Python38c3160b-2793.jpg)



![img](07Python38c3160b-2794.jpg)



在表20.5中可以看到关于本节讨论的urllib函数的概要总结。



Table 20.5 Core urllib Module Functions urllib 函数    描述



urlopen(urlstr,

postQuery- Data二None)    打开 URL urlstr,如果必要则通过 postQueryData 发送请求。



urlretrieve(urlstr, local- tusHook二None)



quote(urldata, safe二'/')

quote_plus(urldata,

safe二'/')

unquote(urldata)

unquote_plus(urldata)

urlencode(dict)



![img](07Python38c3160b-2796.jpg)



将URL urlstr定位的文件下载到localfile或临时文件中(当 localfile没有给定时)；如果文件已经存在downloaStatusHook 将会获得下载的统计信息。

将urldata的无效的URL字符编码；在safe列的则不必编码。

将空格编译成加(+)号(并非%20)外，其他功能与quoteO相似。

将urldata中编码后的字母解码 除了将加好转换成空格后其他功能与unquote()相似。

将字典键-值对编译成有效的CGI请求字符串，用quote_plus() 对键和值字符串分别编码。



### 20.2.4 urllib2 模块



![img](07Python38c3160b-2797.jpg)



正如前面所提到的，urllib2可以处理更复杂URL的打开问题。一个例子就是有基本认证(登录 名和密码)需求的Web站点。最简单的“获得已验证参数”的方法是使用前边章节中描述的URL部 件net_loc，也就是说：http://user:passwd@www.python.org.这种解决方案的问题是不具有可编程 性。然而使用urllib2,我们可以通过两种不同的方式来解决这个问题。



我们可以建立一个基础认证处理器(urllib2.HTTPBasicAuthHandler)，同时在基本URL或域上注 册一个登录密码，这就意味着我们在Web站点上定义了个安全区域。(关于域的更多信息可以查看 RFC2617[HTTP认证：基本数字认证])。一旦完成这些，你可以安装URL打开器，通过这个处理器打 开所有的URL。

另一个可选的办法就是当浏览器提示的时候，输入用户名和密码 ，这样就发送了一个带有适当 用户请求的认证头。在20.1的例子中，我们可以很容易的区分出这两种方法。

逐行解释



1-7行



普通的初始化过程，外加几个为后续脚本使用的常量。



#### 9-15 行

代码的“handler”版本分配了一个前面提到的基本处理器类，并添加了认证信息。之后该处理 器被用于建立一个URL-opener，并安装它以便所有已打开的URL能用到这些认证信息。这段代码和 urllib2模块的Python官方文档是兼容的。

Example 20.1 HTTP Auth Client (urlopenAuth.py)

This script uses both techniques described above for basic authentication.

1    #!/usr/bin/env python

2

3    import urllib2

4

5    LOGIN = 'wesc'

6    PASSWD = "you'llNeverGuess"

7    URL = 'http://localhost'

8

9    def handler_version(url):

![img](07Python38c3160b-2800.jpg)



10    from urlparse import urlparse as up

11    hdlr = urllib2.HTTPBasicAuthHandler()

12    hdlr.add_password('Archives', up(url)[1], LOGIN, PASSWD)

13    opener = urllib2.build_opener(hdlr)

14    urllib2.install_opener(opener)

15    return url

16

17    def request_version(url):

18    from base64 import encodestring

19    req = urllib2.Request(url)

20    b64str = encodestring('%s:%s' % (LOGIN, PASSWD))[:-1]

21    req.add_header("Authorization", "Basic %s" % b64str)

22    return req

23

24    for funcType in ('handler', 'request'):

25    print '*** Using %s:' % funcType.upper()

26    url = eval('%s_version')(URL)

27    f = urllib2.urlopen(url)

28    print f.readline()

29    f.close()

![img](07Python38c3160b-2801.jpg)



![img](07Python38c3160b-2802.jpg)



![img](07Python38c3160b-2803.jpg)



![img](07Python38c3160b-2804.jpg)



![img](07Python38c3160b-2805.jpg)



![img](07Python38c3160b-2806.jpg)



#### 17-22行

这段代码的“request”版本创建了一个Request对象，并在HTTP请求中添加了基本的base64 编码认证头信息。返回“main”后（译者注：指for循环）调用urlopen（）时，该请求被用来替换其 中的URL字符串。注意原始URL内建在Requst对象中，正因为如此在随后的urllib2.urlopen（）中 调用中替换URL字符串才不会产生问题。这段代码的灵感来自于Mike Foord和Lee Harr在Python Cookbook上的回复，具体位置在：

<http://aspn.activestate.com/ASPN/Cookbook/Python/Recipe/305288>

<http://aspn.activestate.com/ASPN/Cookbook/Python/Recipe/267197>

如果能直接用Harr的HTTPRealmFinder类就更好了，那样我们就没必要在例子里使用硬编码了。

#### 24-29行

这个脚本的剩余部分只是用两种技术分别打开了给定的URL，并显示服务器返回的HTML页面第 一行（舍弃了其他行），当然前提是要通过认证。注意如果认证信息无效的话会返回一个HTTP错误 （并且不会有HTML）。

![img](07Python38c3160b-2807.jpg)



程序的输出应当如下所示：



![img](07Python38c3160b-2808.jpg)



$ python urlopen-auth.py Using handler:

<html>

Using request:

<html>

还有一个很有用的文档可以在

<http://www.voidspace.org.uk/python/articles/urllib2.shtml> 找到，你可以把它作为 Python 官方文档的补充。

### 20.3高级Web客户端

Web浏览器是基本的Web客户端。主要用来在Web上查询或者下载文件。而Web的高级客户端并 不只是从因特网上下载文档。

高级Web客户端的一个例子就是网络爬虫（aka蜘蛛和机器人）。这些程序可以基于不同目的在 因特网上探索和下载页面，其中包括：

![img](07Python38c3160b-2809.jpg)



•为Google和Yahoo这类大型的搜索引擎建索引

•脱机浏览一将文档下载到本地，重新设定超链接，为本地浏览器创建镜像。

•下载并保存历史记录或框架 • Web页的缓存，节省再次访问Web站点的下载时间。

我们下边介绍网络爬虫:crawl.py,抓取Web的开始页面地址(URL)，下载该页面和其它后续链 接页面，但是仅限于那些与开始页面有着相同域名的页面。如果没有这个限制的话，你的硬盘将会 被耗尽！ crwal.py的代码在例子20.2中展示。

逐行解释(一个类一个类的)

#### 1-11行

该脚本的开始部分包括Python在Unix上标准的初始化行以及一些模块属性的导入，它们都会 在本应用程序中用到。

#### 13-49行

Retriever类的责任是从Web下载页面，解析每个文档中的链接并在必要的时候把它们加入 “to-do”队列。我们为每个从网上下载的页面都创建一个Retriever类的实例。Retriever中的方 法展现了它的功能：构造器(__init__())、filename()、download()、和 parseAndGetLinksO。

filenameO方法使用给定的URL找出安全、有效的相关文件名并存储在本地。大体上说，它会 去掉URL的“http://”前缀，使用剩余的部分作为文件名，并创建必要的文件夹路径。那些没有 文件名前缀的URL则会被赋予一个默认的文件名“index.htm”。(可以在调用filename()时重新指定 这个名字。)

构造器实例化了一个Retriever对象，并把URL和通过filename()获得的相应文件名都作为本 地属性保存起来。

Example 20.2 Advanced Web Client: a Web Crawler (crawl.py)

这个爬虫程序包括两个类，一个管理真个crawling进程(Crawler),—个检索并解析每一个下 载的 Web 页面(Retriever)。

1    #!/usr/bin/env python

2

3    from sys import argv

4    from os import makedirs, unlink, sep

5    from os.path import dirname, exists, isdir, splitext

6    from string import replace, find, lower

7    from htmllib import HTMLParser

8    from urllib import urlretrieve

9    from urlparse import urlparse, urljoin



10

11

12

13

14

15

16 17 18

19

20 21 22 23 24



from formatter import DumbWriter, AbstractFormatter from cStringIO import StringIO

class Retriever(object):# download Web pages

def __init__(self, url):

self.url = url

self.file = self.filename(url)

def filename(self, url, deffile='index.htm'): parsedurl = urlparse(url, 'http:', 0) ## parse path path = parsedurl[1] + parsedurl[2] ext = splitext(path)

if ext[1] == '': # no file, use default if path[-1] == '/':

![img](07Python38c3160b-2813.jpg)



25    path += deffile

26    else:

27    path += '/' + deffile

28    ldir = dirname(path) # local directory

29    if sep != '/': # os-indep. path separator

30    ldir = replace(ldir, '/', sep)

31    if not isdir(ldir): # create archive dir if nec.

32    if exists(ldir): unlink(ldir)

33    makedirs(ldir)

34    return path

35

36    def download(self): # download Web page

37    try:

38    retval = urlretrieve(self.url, self.file)

39    except IOError:

40    retval = ('*** ERROR: invalid URL "%s"' %\

41    self.url,)

42    return retval

43

44    def parseAndGetLinks(self):# parse HTML, save links

45    self.parser = HTMLParser(AbstractFormatter(\

46    DumbWriter(StringIO())))

![img](07Python38c3160b-2814.jpg)



![img](07Python38c3160b-2815.jpg)



![img](07Python38c3160b-2816.jpg)



47    self.parser.feed(open(self.file).read())

48    self.parser.close()

49    return self.parser.anchorlist

50

51    class Crawler(object):# manage entire crawling process

52

53    count = 0 # static downloaded page counter

54

55    def __init__(self， url):

56    self.q = [url]

57    self.seen = []

58    self.dom = urlparse(url)[1]

59

60    def getPage(self， url):

61    r = Retriever(url)

62    retval = r.download()

63    if retval[0] == '*': # error situation， do not parse

64    print retval， '... skipping parse'

![img](07Python38c3160b-2817.jpg)



65    return

![img](07Python38c3160b-2818.jpg)



66    Crawler.count += 1

67    print '\n('， Crawler.count， ')'

68    print 'URL:'， url

69    print 'FILE:'， retval[0]

70    self.seen.append(url)

71

72    links = r.parseAndGetLinks() # get and process links

73    for eachLink in links:

74    if eachLink[:4] != 'http' and \

75    find(eachLink， '://') == -1:

76    eachLink = urljoin(url， eachLink)

77    print '* '， eachLink，

78

79    if find(lower(eachLink)， 'mailto:') != -1:

80    print '... discarded， mailto link'

81    continue

82

83    if eachLink not in self.seen:

84    if find(eachLink， self.dom) == -1:

85    print '... discarded， not in domain'

86    else:

![img](07Python38c3160b-2819.jpg)



![img](07Python38c3160b-2820.jpg)



87    if eachLink not in self.q:

88    self.q.append(eachLink)

89    print '... new, added to Q'

90    else:

91    print '... discarded, already in Q'

92    else:

93    print '... discarded, already processed'

94

95    def go(self):# process links in queue

96    while self.q:

97    url = self.q.pop()

98    self.getPage(url)

99

100    def main():

101    if len(argv) > 1:

102    url = argv[1]

103 else:

104    try:

![img](07Python38c3160b-2821.jpg)



105    url = raw_input('Enter starting URL: ')

106    except (KeyboardInterrupt, EOFError):

107    url = ''

108

109    if not url: return

110    robot = Crawler(url)

111    robot.go()

112

113    if __name__ == '__main__':

114    main()

正如你想象的，downloadO方法实际会连上网络去下载给定链接的页面。它使用URL调用 urllib.urlretrieveO函数并把结果保存在filename中（该值由filenameO返回）。如果下载成功， parseO方法会被调用来解析刚从网络拷贝下来的页面；否则会返回一个错误字符串。

如果Crawler判定没有错误发生，它会调用parseAndGetLinksO方法来解析新下载的页面并决 定该页面中每个链接的后续动作。

51-98行

Crawler类是这次演示中的“明星”，掌管在一个Web站点上的整个抓爬过程。如果我们为应用



程序添加线程，就可以为每个待抓爬的站点分别创建实例。Crawler的构造器在初始化过程中存储了 3样东西，第一个是q，一个待下载链接的队列。这个队列在运行过程中会有涨落，有页面处理完毕 它就变短，在下载的页面中发现新的链接则会让它变长。

Crawler包含的另两个数值是seen，一个所有“我们已看过”(已下载)的链接的列表，和dom， 我们把主链接的域名存储在这里，并用这个值来判定后续链接是否是该域的一部分。

Crawler还有一个静态数据成员count。这个计数器只是用来保存我们已经从网上下载的对象数 目。每有一个页面成功下载它就会增加。

除了构造器Crawler还有其他两个方法，getPageO和go(Kgo()只是简单的启动Crawler，它 在代码的主体部分被调用。go()中有一个循环，只有队列中还有待下载的新链接它就会不停的执行。 然而这个的真正工作者，却是getPageO方法。

getPageO初始化了一个Retriever对象，并把第一个链接赋给它然后让它执行。如果页面下载 成功，计数器会增加并且链接会被加到“已看”列表。它会反复地检查每个已下载页面中的所有链 接并判决是否有链接要被加入待下载队列。go()中的主循环会不停的推进处理过程直到队列为空， 这时便大功告成。

属于其他域的链接、已经下载过的链接、已在队列中待处理的链接、以及“mailto:”类型的 链接在扩充队列时都会被忽略掉。

#### 100-114行

main()是程序运行的起点，它在该脚本被直接调用时执行。其他导入crawl.py的模块则需要调 用main()来启动处理过程。main()需要一个URL来启动处理，如果在命令行指定了一个(例如这个 脚本被直接调用时)，它就会使用这个指定的。否则，脚本进入交互模式，提示用户输入起始URL。 一旦有了起始链接，Crawler就会被实例化并启动开来。

一个调用crawl.py的例子如下所示:

% crawl.py

Enter starting URL: <http://www.null.com/home/index.html>

( 1 )

URL: <http://www.null.com/home/index.html>

FILE: [www.null.com/home/index.html](http://www.null.com/home/index.html)

\*    <http://www.null.com/home/overview.html> ... new, added to Q

\*    <http://www.null.com/home/synopsis.html> ... new, added to Q

\*    <http://www.null.com/home/order.html> ... new, added to Q

![img](07Python38c3160b-2825.jpg)



\*    <mailto:postmaster@null.com> ... discarded， mailto link

\*    <http://www.null.com/home/overview.html> ... discarded， already in Q

\*    <http://www.null.com/home/synopsis.html> ... discarded， already in Q

\*    <http://www.null.com/home/order.html> ... discarded， already in Q

\*    <mailto:postmaster@null.com> ... discarded， mailto link

\*    <http://bogus.com/index.html> ... discarded， not in domain

( 2 )

URL: <http://www.null.com/home/order.html>

FILE: [www.null.com/home/order.html](http://www.null.com/home/order.html)

\*    <mailto:postmaster@null.com> ... discarded， mailto link

\*    <http://www.null.com/home/index.html> ... discarded， already processed

\*    <http://www.null.com/home/synopsis.html> ... discarded， already in Q

\*    <http://www.null.com/home/overview.html> ... discarded， already in Q

( 3 )

URL: <http://www.null.com/home/synopsis.html>

FILE: [www.null.com/home/synopsis.html](http://www.null.com/home/synopsis.html)

![img](07Python38c3160b-2826.jpg)



\*    <http://www.null.com/home/index.html> ... discarded， already processed

![img](07Python38c3160b-2827.jpg)



\*    <http://www.null.com/home/order.html> ... discarded， already processed

\*    <http://www.null.com/home/overview.html> ... discarded， already in Q

( 4 )

URL: <http://www.null.com/home/overview.html>

FILE: [www.null.com/home/overview.html](http://www.null.com/home/overview.html)

\*    <http://www.null.com/home/synopsis.html> ... discarded， already processed

\*    <http://www.null.com/home/index.html> ... discarded， already processed

\*    <http://www.null.com/home/synopsis.html> ... discarded， already processed

\*    <http://www.null.com/home/order.html> ... discarded， already processed

执行后，在本地的系统文件中将会在创建一个名为www.null.com的目录，及分目录。左右的HTML 文件都会显示在主目录下。

### 20.4 CGI:帮助Web服务器处理客户端数据

### 20.4.1 CGI 介绍

Web开发的最初目的是在全球范围内对文档进行存储和归档（大多是教学和科研目的的）。这些



零碎的信息通常产生于静态的文本或者HTML.



HTML是一个文本格式而算不上是一种语言，它包括改变字体的类型、大小、风格。HTML的主要

特性在于它对超文本的兼容性，文本以一种或者是高亮的形式指向另外一个相关文档。可以通过鼠

标点击或者其他用户的选择机制来访问这类文档。这些静态的HTML文档在Web服务器上，当有请求

时，将被送到客户端。

随着因特网和Web服务器的形成，产生了处理用户输入的需求。在线零售商需要能够单独订货， 网上银行和搜索引擎需要为用户分别建立帐号。因此发明了这种执行模式，并成为了 Web 站点可以 从用户那里获得特苏信息的唯一形式（在Java applets出现之前）。反过来，在客户提交了特定数 据后，就要求立即生成HTML页面。

现在Web服务器仅有一点做的很不错，获取用户对文件的请求，并将这个文件（也就是说HTML 文件。返回给客户端。它们现在还不具有处理字段类特殊数据的机制。将这些请求送到可以生成动 态HTML页面的扩展应用程序中并返回给客户端，这些还没有成为Web服务器的职责。

这整个过程开始于Web服务器从客户端接到了请求（GET或者POST。，并调用合适的程序。然后 开始等待HTML页面一与此同时，客户端也在等待。一旦程序完成，会将生成的动态HTML页面返回 到服务器端，然后服务器端再将这个最终结果返回给用户。服务器接到表单反馈，与外部应用程序 一交互，收到并返回新生成的HTML页面都发生在一个叫做Web服务器CGI （Common Gateway Interface。 的接口上.图20-3描述了 CGI的工作原理，逐步展示了一个用户从提交表单到返回最终结果Web页

![img](07Python38c3160b-2830.jpg)



面的整个执行过程和数据流。

客户端输入给Web服务器端的表单可能包括处理过程和一些存储在后台数据库中的表单。需要 记住的是，在任何时候都可能有任何一个用户去填写这个字段，或者点击提交按钮或者图片，这更 像激活了某种CGI活动。

创建HTML的CGI应用程序通常是用高级编程语言来实现的，可以接受、处理数据，向服务器端 返回HTML页面。目前使用的编程语言有Perl，PHP，C/C++，或者Python。

![img](07Python38c3160b-2831.jpg)



图20-3 CGI工作概要图。CGI代表了在一个Web服务器和能够处理用户表单、生成并返回动态 HTML页的应用程序间的交互。

在我们研宄CGI之前，我们必须告诉你典型的Web应用产品已经不再使用CGI 了。

由于它词义的局限性和允许Web服务器处理大量模拟客户端数据能力的局限性，CGI几乎绝迹。 Web服务的关键使命依赖于遵循像C/C++这样语言的规范。如今的Web服务器典型的部件有Aphache 和集成的数据库部件（MySQL或者PostgreSQL），Java（Tomcat），PHP和各种Perl模块，Python模 块，以及SSL/security。然而，如果你工作在私人小型的或者小组织的Web网站上的话就没有必要

使用这种强大而复杂的Web服务器，CGI是一个适用于小型Web网站开发的工具。

更进一步来说，有很多Web应用程序开发框架和内容管理系统，这些都弥补了过去CGI的不足。 然而，在这些浓缩和升华下，它们仍旧遵循这CGI最初提供的模式，可以允许用户输入，根据输入

执行拷贝，并提供了一个有效的HTML做为最终的客户端输出。因此，为了开发更加高效的Web服务 有必要理解CGI实现的基本原理。

在下一部分中，我们将会关注在cgi模块的协助下如何在Python中建立一个CGI应用程序。

### 20.4.2 CGI应用程序

CGI应用程序和典型的应用程序有些不同。主要的区别在于输入、输出以及用户和计算机交互方 面。当一个CGI脚本开始执行时，它需要检索用户-支持表单，但这些数据必须要从Web的客户端才 可以获得，而不是从服务器或者硬盘上获得。

这些不同于标准输出的输出将会返回到连接的Web客户端，而不是返回到屏幕、⑶I窗口或者硬 盘上。这些返回来的数据必须是具有一系列有效头文件的HTML。否则，如果浏览器是Web的客户端， 由于浏览器只能识别有效的HTTP数据（也就是MIME都问价和HTML），那么返回的也只能是个错误消 息（具体的就是因特网服务器错误）。

最后，可能和你想象的一样，用户不能与脚本进行交互。所有的交互都将发生在Web客户端（用

户的行为），Web服务器端和CGI应用程序间。

### 20.4.2 cgi 模块

在cgi模块中有个主要类：FieldStorage类，它完成了所有的工作。在Python CGI脚本开始时 这个类将会被实例化，它会从Web客户端（具有Web服务器）读出有关的用户信息。一旦这个对象 被实例化，它将会包含一个类似字典的对象，具有一系列的键-值对，键就是通过表单传入的表单条 目的名字，而值则包含相应的数据。

这些值本身可以是以下三种对象之一。它们既可以是FieldStorage对象（实例）也可以是另一 个类似的名为MiniFieldStorage类的实例，后者用在没有文件上传或mulitple-part格式数据的情 况。MiniFieldStorage实例只包含名字和数据的键-值对。最后，它们还可以是这些对象的列表。这 发生在表单中的某个域有多个输入值的情况下。

对于简单的Web表单，你将会经常发现所有的MiniFieldStorage实例。下边包含的所有的例子 都仅针对这种情况。

### 20.5建立CGI应用程序 20.5.1建立Web服务器

为了可以用Python进行CGI开发，你首先需要安装一个Web服务器，将其配置成可以处理Python CGI请求的模式，然后让你的Web服务器访问CGI脚本。其中有些操作你也许需要获得系统管理员的 帮助。

如果你需要一个真正的Web服务器，可以下载并安装Aphache。Aphache的插件或模块可以处理 Python CGI，但这在我们的例子里并不是必要的。如果你准备把自己的服务〃带入真实世界〃，也许会 想安装这些软件。尽管它们似乎过于强大。

为了学习的目的或者是建立小型的Web站点，使用Python自身带的Web服务器就已经足够了。 在第20.8节，你将会实际的学习如何建立和配置简单的基于Python的Web服务器。如果你想在本 阶段获得更多知识，你也可以现在提前阅读那部分。然而，这并不是本章的焦点。

如果你只是想建立一个基于Web的服务器，你可以直接执行下边的Python语句。

$ python -m CGIHTTPServer

-m选项是在2.4中新引进的，如果你使用的是比这旧的Python版本，或者想看下它执行的不同

方式，请看14.4.3. 无论如何，最终它需要工作起来...

这将会在当前机器的当前目录下建立一个端口号为8000的Web服务器。然后可以在启动这个服 务器的目录下建立一个Cgi-bin，将Python CGI脚本放到那里。将一些HTML文件放到那个目录下， 或许有些.py CGI脚本在Cgi-bin中，然后就可以在地址栏中输入这些地址来访问Web站点啦。

<http://localhost:8000/friends.htmhttp://localhost:8000/cgi-bin/friends2.py>

### 20.5.2 建立表单页

在例20.3中，我们写了一个简单的Web表单，friends.html.

正如你可以在代码中看到的一样，这个表单包括两个输入变量：person和howmany，这两个值 将会被传到我们的CGI脚本friendsl.py中。

你会注意到在例子中我们将CGI脚本初始化到主机默认的cgi-bin目录下（“Action”连接）。（如 果这个信息与你开发环境不一样的话，在测试Web页面和CGI之前请更新你的表单事件）。同时由于

表单事件中缺少METHOD子标签，所有的请求将会采用默认的GET方法。选择GET方法是因为我们 的表单没有太多的字段，同时我们希望我们的请求字段可以在“位置” （aka“Address”，“Go To”

H条中显示，以便你可以看到被送到服务器端的URL。

Example 20.3 Static Form Web Page (friends.htm)

这个HTML文件展示给用户一个空文档，含有用户名，和一系列可供用户选择的单选按钮。

1    <HTML><HEAD><TITLE>

2    Friends CGI Demo (static screen)

3    </TITLE></HEAD>

4    <BODY><H3>Friends list for: <I>NEW USER</I></H3>

5    <FORM ACTION="/cgi-bin/friends1.py">

6    <B>Enter your Name:</B>

7    <INPUT TYPE=text NAME=person VALUE="NEW USER" SIZE=15>

8    <P><B>How many friends do you have?</B>

9    <INPUT TYPE=radio NAME=howmany VALUE="0" CHECKED> 0

10    <INPUT TYPE=radio NAME=howmany VALUE="10"> 10

11    <INPUT TYPE=radio NAME=howmany VALUE="25"> 25

12    <INPUT TYPE=radio NAME=howmany VALUE="50"> 50

13    <INPUT TYPE=radio NAME=howmany VALUE="100"> 100

14 <P><INPUT TYPE=submit></FORM></BODY></HTML>



图 20-4



图20-4，Friends表单页面在MacOS X操作系统Safari浏览器上的显示(friends.htm)

![img](07Python38c3160b-2840.jpg)



让我们看看friends.htm提交后在客户端屏幕上的显示(图20-4 Safari，MacOS和图20-5 IE6)

![img](07Python38c3160b-2841.jpg)



图 20-5 图20-5 friends表单页面在Win32操作系统IE6浏览器上的显示(friends.htm)

![img](07Python38c3160b-2842.jpg)



通过本章，我们将会展示来自与不同Web浏览器和操作系统的屏幕截图。

### 20.5.3 生成结果页

这些输入是由用户完成的，然后按下了 “Submit”按钮（可选的，用户也可以在该文本字段中 按下回车键获得相同的效果。）当这些发生后，在例20.4中的脚本，friendsl.py将会随CGI—起 被执行。

这个脚本包含了所有的编程功能，读出并处理表单的输入，同时向用户返回结果HTML页面。所 有的这些“实际”的工作仅是通过四行Python代码来实现的（14-17行）。

表单的变量是FieldStorage的实例，包含person和howmanyh字段的值。我们把这些值本分别 存入Python的who和howmany变量中。变量reshtml包含需要返回的HTML文本的正文，还有一些 动态填好的字段，这些数据都是从表单中读入的。

Example 20.4 Results Screen CGI code (friends1.py)

CGI脚本在表单上抓取person和howmany字段，并用这些数据生成动态的结果示图。

1    #!/usr/bin/env python

2

3    import cgi

4

5    reshtml = '''Content-Type: text/html\n

6    <HTML><HEAD><TITLE>

7    Friends CGI Demo (dynamic screen)

8    </TITLE></HEAD>

9    <BODY><H3>Friends list for: <I>%s</I></H3>

10    Your name is: <B>%s</B><P>

11    You have <B>%s</B> friends.

12    </BODY></HTML>'''

13

14    form = cgi.FieldStorage()

15    who = form['person'].value

16    howmany = form['howmany'].value

17    print reshtml % (who, who, howmany)

核心提示:HTML头文件是从HTML中分离出来的。

#### 有一点需要向CGI初学者指明的是，在向CGI脚本返回结果时，须先返回一个适当的HTTP头文 件后才会返回结果HTML页面。进一步说，为了区分这些头文件和结果HTML页面，需要在friendsl.py

的第五行中插入几个换行符。在本章的后边的代码中也是这样的处理。

图20-6是可能出现的屏幕，假设用户输入的名字为“erick allen”，单击“10 friends”单选 按钮。这次的屏幕镜像图展示的是在Windows环境下IE3浏览器的效果。

如果你是一个Web站点的生产商，你也许会想，“如果这个人忘记了的话，我能自动的将这个人 的名字大写，会不会更好些？ ”这个通过Python的CGI可以很容易就实现（我们很快就会进行试验！）

注意GET请求是如何将表单中的变量和值加载在URL地址条中的。你是否观察到了 friends.htm 页面的标题有个“static”，

而friends.py脚本输出到屏幕上的则是“dynamic”？我们这样做的一个原因就是：指明 friends.htm文件是一个静态的文本，而结果页面却是动态生成的。换句话说，结果页面的HTML不 是以文本文件的形式存在硬盘上的，而是由我们的 CGI 脚本生成的，并且将其以本地文件的形式返 回。

![img](07Python38c3160b-2846.jpg)



![img](07Python38c3160b-2847.jpg)



![img](07Python38c3160b-2848.jpg)



图 20-6

图20-6 Friends的结果页面在Win32操作系统IE3浏览器上的显示

在下边的例子中，我们将会更新我们的CGI脚本，使其变得更灵活些，从而完全绕过静态文件。

### 20.5.4 生成表单和结果页面

我们删除fiends.html文件并将其合并到friends2.py中。这个脚本现在将会同时生成表单页



和结果页面。但是我们如何控制生成哪个页面呢？好吧，如果有表单数据被发送，那就意味着我们

需要建立一个结果页面。如果我们没有获得任何的信息，这就说明我们需要生成一个用户可以输入

数据的表单页面。

例子20.5展示的就是我们的新脚本friends2.py

那么我们改变了哪些脚本呢？让我们一起看下这个脚本的代码块。

逐行解释

#### 1-5行

除了通常的起始、和模块导入行，我们还把HTTP M頂I头从后面的HTML正文部分分离出来，放 在了这里。因为我们将在返回的两种页面(表单页面和结果页面)中都使用它，而又不想重复写文 本。当需要输出时，我们将把这个头字串加在相应的HTML正文中。

#### 7-29行

所有这些代码都是为了整合CGI脚本里的friends.htm表单页面。我们对表单页面的文本使用 一个变量formhtml，还有一个用来创建单选按钮的字符串变量fradio。我们从friends.htm复制了 这个单选按钮HTML文本，但我们意在展示如何使用Python来生成更多的动态输出一见22-27行的 for循环。    _

showFormO函数负责对用户输入生成表单页。它为单选按钮创建了一个文字集，并把这些HTML 文本行合并到了 formhtml主体中，然后给表单加上头信息，最后通过把整个字符串输出到标准输出 的方式给客户端返回了整块数据。

例20.5生成表单和结果页面(friends2.py)

将friends.html和friendsl.py合并成friends2.py。得到的脚本可以同时显示表单和动态生 成的HTML结果页面，同时可以巧妙的知道应该输出哪个页面。

1    #!/usr/bin/env python

2

3    import cgi

4

5    header = 'Content-Type: text/html\n\n'

6

7    formhtml = '''<HTML><HEAD><TITLE>

8    Friends CGI Demo</TITLE></HEAD>

9    <BODY><H3>Friends list for: <I>NEW USER</I></H3>

10    <FORM ACTION="/cgi-bin/friends2.py">

11    <B>Enter your Name:</B>

![img](07Python38c3160b-2852.jpg)



12    <INPUT TYPE=hidden NAME=action VALUE=edit>

13    <INPUT TYPE=text NAME=person VALUE="NEW USER" SIZE=15>

14    <P><B>How many friends do you have?</B>

15    %s

16    <P><INPUT TYPE=submit></FORM></BODY></HTML>'''

17

18    fradio = '<INPUT TYPE=radio NAME=howmany VALUE="%s" %s> %s\n'

19

20    def showForm():

21    friends = ''

22    for i in [0, 10, 25, 50, 100]:

23    checked = ''

24    if i == 0:

25    checked = 'CHECKED'

26    friends = friends + fradio % \

27    (str(i), checked, str(i))

28

29 print header + formhtml % (friends)

![img](07Python38c3160b-2853.jpg)



30

![img](07Python38c3160b-2854.jpg)



31    reshtml = '''<HTML><HEAD><TITLE>

32    Friends CGI Demo</TITLE></HEAD>

33    <BODY><H3>Friends list for: <I>%s</I></H3>

34    Your name is: <B>%s</B><P>

35    You have <B>%s</B> friends.

36    </BODY></HTML>'''

37

38    def doResults(who, howmany):

39    print header + reshtml % (who, who, howmany)

40

41    def process():

42    form = cgi.FieldStorage()

43    if form.has_key('person'):

44    who = form['person'].value

45    else:

46    who = 'NEW USER'

47

48    if form.has_key('howmany'):

49    howmany = form['howmany'].value

50    else:

51    howmany = 0

![img](07Python38c3160b-2855.jpg)



52

53    if form.has_key('action'):

54    doResults(who， howmany)

55    else:

56    showForm()

57

58    if __name__ == '__main__':

59    process()



这段代码中有两件有趣的事值得注意。第一点是表单中12行action处的“hidden”变量，这 里的值为“edit”。我们决定显示哪个页面（表单页面或是结果页面）的唯一途径是通过这个字段。 我们将在第53-56行看到这个字段如何起作用。

还有，请注意我们在生成所有按钮的循环里把单选按钮 0设置为默认按钮。这表明我们可以在 一行代码里（第18行。更新单选按钮的布局和/或它们的值，而不用再写多行文字。这也同时提供 了更多的灵活性，可以用逻辑来判断哪个单选按钮被选中—见我们脚本的下一个升级版，后面的 friends3.py。

现在你或许会想“既然我也可以选择person或howmany是否出现，那为什么我们要用一个action 变量呢？”这是一个很好的问题，因为在这种情况下你当然可以只用person或hwomany。

然而，action变量代表了一种更明显的出现，不光是它的名字还有它的作用一其代码很容易理 解。person和howmany变量都是对其值起作用，而action变量则被用作一个标志。

![img](07Python38c3160b-2857.jpg)



创立action的另一个原因是我们将会再一次使用它来帮助我们决定生成哪一页。具体来说， 我们需要在person变量出现时会显示一个表单（而不是生成结果页面）--如果在这里仅依赖person

变量，你的代码运行将失败。

#### 31-39行

显示结果页的代码实际上和friendsl.py中的一样。

![img](07Python38c3160b-2858.jpg)



![img](07Python38c3160b-2859.jpg)



图 20-7



图20-7 Friends表单页面在Win32操作系统Firefox 1.x浏览器上的显示（friends2.py）

#### 41-56行

因为这个脚本可以产生出不同的页面，所以我们创建了一个包括一切的processO函数来获得表 单数据并决定采用何种动作。看起来processO的主体部分也和friendsl.py中主体部分的代码相似。 然而它们有两个主要的不同。

因为这个脚本也许可以、也许不能取得所期待的字段（例如，第一次运行脚本时生成一个表单 页，这样的话就不会给服务器传递任何字段），我们需要用if语句把从表单项取得的值“括起来”， 并检查它们此时是否有效。还有我们上面提到的action字段，它可以帮助我们判定应生成哪一个页 面。第53-56行作了这种判定。

在图20-7和图20-8中，你会先看到脚本生成的表单页面（已经输入了一个名字并选择了一个

单选按钮），然后是结果页面，也是这个脚本生成的。

如果看一下位置或“转到”栏，你将不会看到一个对friends.htm静态文件的URL，而在图20-4 或图20-5中都有。

### 20.5.5全面交互的Web站点

我们最后一个例子将会完成这个循环。如在前面中，用户在表单页中输入他/她的信息，然后我 们处理这些数据，并输出一个结果页面。现在我们将会在结果页面上加个链接允许返回到表单页面 ， 但是我们返回的是含有用户输入信息的页面而不是一个空白页面。我们页加上了一些错误处理程序， 来展示它是如何实现的。

HOB



![img](07Python38c3160b-2861.jpg)



\> Friends CGI Demo - Mozilla FireFoK

File Edit View Go Bookmarks Tools Help

o ▼    @    @    ® F hl±p: ■l■■l■l。c■3lh。sl：: i3O[ii]/cgi-bi「i,iTrie nds2. py?acl ▼ |

Filenrts list foi1： joint(h>e

Your riEune is: iolui doe

Done



图20-8

图20-8 Friends结果页面在Win32操作系统Firefox浏览器上的显示(friends2.py)

现在在例子20.6中我们展示我们最后的更新，friends3.py。

friends3.py和friends2.py没有太大的不同。我们请读者比较不同处；这里我们简要的介绍了 主要的不同点。

简略的逐行解释

#### 第8行

我们把URL从表单中抽出来是因为现在有2个地方需要它，结果页面是它的新顾客。

#### 10-19， 69-71， 75-82行

所有这些行都用来处理新特性—错误页面。如果用户没有选择单选按钮，指明朋友数量，那么 howmany字段就不会传送给服务器，在这种情况下，showErrorO函数会返回一个错误页面给客户。

错误页面的显示使用了 JavaScript的“后退”按钮。因为按钮都是输入类型的，所以需要一个 表单，但不需要有动作因为我们只是简单的后退到浏览器历史中的上一个页面。尽管我们的脚本目 前只支持(或者说探测、测试)一种类型的错误，但我们仍然使用了一个通用的error变量，这是

H为了以后还可以继续开发这个脚本，给它增加更多的错误检测。

例20.6全用于交互和错误处理(friends3.py)

通过加上返回输入信息的表单页面的连接，我们实现了整个循环，给了用户一次完整的Web应 用体验。我们的应用程序现在也进行了一些简单的错误验证，在用户没有选择任何单选按钮时，可 以通知用户。

1    #!/usr/bin/env python

2

3    import cgi

4    from urllib import quote_plus

5    from string import capwords

6

7    header = 'Content-Type: text/html\n\n'

8    url = '/cgi-bin/friends3.py'

9

10    errhtml = '''<HTML><HEAD><TITLE>

11    Friends CGI Demo</TITLE></HEAD>

12    <BODY><H3>ERROR</H3>

13    <B>%s</B><P>

![img](07Python38c3160b-2865.jpg)



14    <FORM><INPUT TYPE=button VALUE=Back

15    ONCLICK="window.history.back()"></FORM>

16    </BODY></HTML>'''

17

18    def showError(error_str):

19    print header + errhtml % (error_str)

20

21    formhtml = '''<HTML><HEAD><TITLE>

22    Friends CGI Demo</TITLE></HEAD>

23    <BODY><H3>Friends list for: <I>%s</I></H3>

24    <FORM ACTION="%s">

25    <B>Your Name:</B>

26    <INPUT TYPE=hidden NAME=action VALUE=edit>

27    <INPUT TYPE=text NAME=person VALUE="%s" SIZE=15>

28    <P><B>How many friends do you have?</B>

29    %s

30    <P><INPUT TYPE=submit></FORM></BODY></HTML>'''

31

![img](07Python38c3160b-2866.jpg)



32    fradio = '<INPUT TYPE=radio NAME=howmany VALUE="%s" %s> %s\n'

![img](07Python38c3160b-2867.jpg)



33

34    def showForm(who， howmany):

35    friends = ''

36    for i in [0， 10， 25， 50， 100]:

37    checked = ''

38    if str(i) == howmany:

39    checked = 'CHECKED'

40    friends = friends + fradio % \

41    (str(i)， checked， str(i))

42    print header + formhtml % (who， url， who， friends)

43

44    reshtml = '''<HTML><HEAD><TITLE>

45    Friends CGI Demo</TITLE></HEAD>

46    <BODY><H3>Friends list for: <I>%s</I></H3>

47    Your name is: <B>%s</B><P>

48    You have <B>%s</B> friends.

49    <P>Click <A HREF="%s">here</A> to edit your data again.

50    </BODY></HTML>'''

51

52 def doResults(who， howmany):

![img](07Python38c3160b-2868.jpg)



![img](07Python38c3160b-2869.jpg)



53    newurl = url + '?action=reedit&person=%s&howmany=%s'%\

54    (quote_plus(who), howmany)

55    print header + reshtml % (who, who, howmany, newurl)

56

57    def process():

58    error = ''

59    form = cgi.FieldStorage()

60

61    if form.has_key('person'):

62    who = capwords(form['person'].value)

63    else:

64    who = 'NEW USER'

65

66    if form.has_key('howmany'):

67    howmany = form['howmany'].value

68    else:

69    if form.has_key('action') and \

70    form['action'].value == 'edit':

![img](07Python38c3160b-2870.jpg)



71    error = 'Please select number of friends.

![img](07Python38c3160b-2871.jpg)



72    else:

73    howmany = 0

74

75    if not error:

76    if form.has_key('action') and \

77    form['action'].value != 'reedit':

78    doResults(who, howmany)

79    else:

80    showForm(who, howmany)

81    else:

82    showError(error)

83

84    if __name__ == '__main__':

85    process()

#### 27，38-41，49，52-55行

这个脚本的一个目的是创建一个有意义的链接，以便从结果页面返回表单页面。当有错误发生

时，用户可以使用这个链接返回表单页面去更新他/她填写的数据。新的表单页面只有当它包含了用

户先前输入的信息时才有意义。（如果让用户重复输入这些信息会很令人沮丧！）

为了实现这一点，我们需要把当前值嵌入到更新过的表单中。在第27行，我们给name新增了



![img](07Python38c3160b-2873.jpg)



一个值。这个值如果给出的话，会被插入到name字段。显然地，在初始表单页面上它将是空值。第 38-41行，我们根据当前选定的朋友数目设置了单选按钮。最后，通过第49行和52-55行更新了的 doResultsO函数，我们创建了这个包含已有信息的链接，它会让用户“返回”到我们更改后的表单 页面。

#### 62行

最后我们从美学角度上加了一个简单的特性。在friendsl.py和friends2.py的截屏中，可以 看到返回结果和用户的输入一字不差。在上述的截屏中，如果用户的名字没有大写这将影响返回的 页面。我们加了一个对string.capwordsO函数的调用从而自动的将用户名置成大写。capwords() 函数可以将传进来的每个单词的第一个字母置成大写的。这也许是或许不是必要的特性，但是我们 还是愿意一起分享它，以便你知道这个功能的存在。

下边我们将会展示四个截屏，表明用户和CGI表单及脚本的交互过程。

在第一个截屏图20 — 9中，我们调用friends3.py生成了一个熟悉的新表单页面。输入“fool bar， 同时故意忘记检查单选按钮。单击Submit按钮后将会返回错误页面，请看第二个截屏图20-10.

![img](07Python38c3160b-2874.jpg)



| ©OO       | Friends CGI Demo                             | O    |
| --------- | -------------------------------------------- | ---- |
| @ @ ◎     | O <http://localhost:8000/cgi-bin/fnends3,py> |      |
| Bookmarks |                                              |      |

Friends list for: NEW USER



![img](07Python38c3160b-2875.jpg)



Your Name: foo har

How many friends do you have? 3 0 0 10 O 25 0 50 O 100

r SubmitQuery s

Document: Done

图 20-9

图20-9friends的初始表单页面在MacOS X操作系统Camino浏览器上的显示(friends3.py)

![img](07Python38c3160b-2876.jpg)



![img](07Python38c3160b-2877.jpg)



![img](07Python38c3160b-2878.jpg)



![img](07Python38c3160b-2879.jpg)



![img](07Python38c3160b-2880.jpg)



![img](07Python38c3160b-2881.jpg)



![img](07Python38c3160b-2882.jpg)



![img](07Python38c3160b-2883.jpg)



图 20-10



图20-10 Friends的错误页面(无效的用户输入)在Camino浏览器上的显示(Friends3.py)

我们单击“后退”按钮，选择“50”单选按钮，重新提交表单。结果页面如图20-11，也非常像，

但是现在在页面底部有个额外的连接。这个连接将会把我们带到表单页面。新表单页面和最初的页

面的唯一区别是所有用户输入的数据都被设置成了“默认值”，这意味着这些值在表单中已经存在了。

我们可以看图20-12.

这时用户可以更改任何一个字段或者重新提交表单。

毫无疑问你会开始注意到我们的表单和数据已经变得复杂多了，生成的HTML页面是这样，结果 页面更是复杂。如果你有HTML文本和应用程序的接入点的话，你可能会考虑与Python的HTMLgen 模块的连接，HTMLgen是Python的一个扩展模块，专用于生成HTML页面。

图20-11



图20-11带有当前信息的更新后的friends表单页面



![img](07Python38c3160b-2887.jpg)



![img](07Python38c3160b-2888.jpg)



![img](07Python38c3160b-2889.jpg)



![img](07Python38c3160b-2890.jpg)



![img](07Python38c3160b-2891.jpg)



图20-12



图20-12 friends结果页面(无敦输入)(friends3.py)

Edit By Vheavens

### 20.6在CGI中使用Unicode编码

在第六章“序列”中，我们介绍了 Unicode字符串的使用。在6.8.5部分，我们给了个简单的 H例子脚本:取得Unicode字符串，写入一个文件，并重新读出来。在这里，我们将演示一个具有Unicode

输出的简单CGI脚本，并给浏览器足够的提示，从而可以正确的生成这些字符。。

唯一的要求是你的计算机必须装有对应的东亚字体以便浏览器可以显示它们。

为了看到Unicode的作用，我们将会用CGI脚本生成一个多语言功能的Web页面。首先我们用 Unicode字符串定义一些消息。我们假设你的编辑器只能输入ASCII编码。因此，非ASCII编码的字 符使用\u转义符输入。实际上从文件或数据库中也能读取这些消息。

\#    Greeting in English, Spanish,

\#    Chinese and Japanese. UNICODE_HELLO = u""" Hello!

\u00A1Hola!

\u4F60\u597D!

\u3053\u3093\u306B\u3061\u306F!

CGI产生的第一个头信息指出内容类型(content-type)是HTTP。此处还声明了消息是以UTF-8 编码进行传输的，这点很重要，这样浏览器才可以正确的翻译它。

print 'Content-type: text/html; charset=UTF-8\r' print '\r'



![img](07Python38c3160b-2894.jpg)



例 20.7 简单 Unicode CGI 示例(uniCGI.py)

这个脚本输出到你Web浏览器端的是Unicode字符串。

1 #!/usr/bin/env python

2

3 CODEC



'UTF-8'



4 UNICODE_HELLO = u'

5    Hello!

6    \u00A1Hola!

7    \u4F60\u597D!

8    \u3053\u3093\u306B\u3061\u306F!

9    '''

10

11    print 'Content-Type: text/html; charset=%s\r' % CODEC

12    print '\r'

13    print '<HTML><HEAD><TITLE>Unicode CGI Demo</TITLE></HEAD>'

14    print '<BODY>'

![img](07Python38c3160b-2895.jpg)



15    print UNICODE_HELLO.encode(CODEC)

![img](07Python38c3160b-2896.jpg)



16    print '</BODY></HTML>'

然后输出真正的消息。事先用string类的encodeO方法先将这个字符串转换成UTF-8序列(

例20.7中显示了完整的程序。

如果你在你的浏览器中运行这个CGI，你将会获得如图20-13所示的输出。

图 20-13



![img](07Python38c3160b-2898.jpg)



图20-13简单的CGI Unicode编码在Firefox上的输出（uniCGI.py）

### 20.7 高级 CGI

现在我们来看看CGI编程的高级方面。这包括cookie的使用（保存在客户端的缓存数据），同 一个CGI字段的多重值，和用multipart表单实现的文件上传。为了节省空间，我们将会在同一个 程序中向你展示这三个特性。首先让我们看下多次提交问题。

### 20.7.1 Mulitipart表单提交和文件的上传

目前，CGI特别指出只允许两种表单编码，“ application/x-www-form-urlencoded ”和 “multipart/form-dat”。由于前者是默认的，就没有必要像下边那样在FORM标签里声明编码方式。

<FORM enctype="application/x-www-form-urlencoded" ...>

但是对于multipart表单，你需要像这样明确给出编码：

<FORM enctype="multipart/form-data" ...>

在表单提交时你可以使用任一种编码，但在目前上传的文件仅能表现为multipart编码。 Multipart编码是由网景在早期开发的，但是已经被微软（开始于IE4版）和其他的浏览器采用。

通过使用输入文件类型完成文件上传：

<INPUT type=file name=...>

这个指令表现为一个空的文本字段，同时旁边有个按钮，可以让你浏览文件目录系统，找到 要上传的文件。在使用multipart编码时，你客户端提交到服务器端的表单看起来会很像带有附件 的email。同时还需要有一个单独的编码，因为它还没有聪明到“通过URL编码”的程度，尤其是对 一个二进制文件。这些信息仍然会到达服务器，只是以一种不同的“封装”形式而已。

不论你使用的是默认编码还是multipart编码，cgi模块都会以同样的方式来处理它们，在表单 提交时提供键和相应的值。你还可以像以前那样通过FieldStorage实例来访问数据。

### 20.7.2多值字段

除了上传文件，我们将会展示如何处理具有多值的字段。最常见的情况就是你有一系列的复选

框允许用户有多个选择。每个复选框都会标上相同的字段名，但是为了区分它们，会有不同的值与

特定的复选框关联。



正如你所知道的，在表单提交时，数据从用户端以键-值对形式发送到服务器端。当提交不止一

个复选框时，就会有多个值对应同一个键。在这种情况下，cgi模块将会建立一个这类实例的列表， 你可以遍历获得所有的值，而不是为你的数据指定一个MiniFielStorage实例。总的来说不是很痛 苦。

### 20.7.3 cookie

最后，我们会在例子中使用cookie。如果你对cookie还不太熟悉的话，可以把它们看成是Web 站点服务器要求保存在客户端（例如浏览器）上的二进制数据。

由于HTTP是一个“无状态信息”的协议，如你在本章最开始看到的截图一样，是通过GET请 求中的键值对来完成信息从一个页面到另一个页面的传递。实现这个功能的另外一种方法如我们以 前看到的一样，是使用隐藏的表单字段，如在后期friends.py脚本中对action变量的处理。这些 信息必须被嵌入新生成的页面中并返回给客户端，所以这些变量和值由服务器来管理。

![img](07Python38c3160b-2902.jpg)



还有一种可以保持对多个页面浏览连续性的方法就是在客户端保存这些数据。这就是引进 H cookie的原因。服务器可以向客户端发送一个请求来保存cookie，而不必用在返回的Web页面中 嵌入数据的方法来保持数据。Cookie连接到最初的服务器的主域上（这样一个服务器就不能设置或

者覆盖其他服务器上的cookie）,并且有一定的生存期限（因此你的浏览器不会堆满cookie）。

这两个属性是通过有关数据条目的键-值对和cookie联系在一起的。cookie还有一些其他的属 性，如域子路径，cookie安全传输请求。

有了 coockies，我们不再需要为了跟踪用户而将数据从一页传到另一页了。虽然这在隐私问题 上也引发了大量的争论，多数Web站点还是合理地使用了 cookie。为了准备代码，在客户端获得请 求文件前，Web服务器向客户端发送“SetCookie”头文件要求客户端存储cookie

一旦在客户端建立了 cookie，HTTP_COOKIE环境变量会将那些cookie自动放到请求中发送给服 务器。cookie是以分号分隔的键值对存在的。要访问这些数据，你的应用程序就要多次拆分这些字 符串（也就是说，使用str.splitO或者手动解析）。cookie以分号（；分隔，每个键-值对中间都 由等号（=）分开。

和multipart编码一样，cookie同样起源于网景，他们实现了 cookie并制定出第一个规范并沿 用至今，在下边的Web站点中你可以接触这些文档：

<http://www.netscape.com/newsref/std/cookie_spec.html>

![img](07Python38c3160b-2903.jpg)



一旦cookie标准化以后，这些文档最终都被废除了，你可以从评论请求文档（RFCs）中获得更 多现在的信息。现今发布的最新的cookie的文件是RFC2109.

### 20.7.4使用高级CGI

现在我们来展示CGI应用程序，advcgi.py,它的代码号功能和本章前部分讲到的friends3.py 的差别不是很大。默认的第一页是用户填写的表单，它由四个主要部分组成：用户设置cookie字符 串，姓名字段，编程语言复选框列表，文件提交框。在图20-14中可以看到示图。

图20-15是在另一个浏览器看到的表单效果图，在这个表单中，我们可以输入自己的信息，如 图20-16中给的样式。注意查找文件的按钮在不同的浏览器中显示的文字是不同的，如，“Browse...” “Choose”, “...”等。

这些数据以mutipart编码提交到服务器端，在服务器端以同样的方式用FieldStorage实例获 取。唯一不同的就是对上传文件的检索。在我们的应用程序中，我们选择的是逐行读取，遍历文件。 如果你不介意文件的大小的话，也可以一次读入整个文件。

由于这是服务器端第一次接到数据，这时，当我们向客户端返回结果页面时，我们使用 — “SetCookie:”头文件来捕获浏览器端的cookie。    一

![img](07Python38c3160b-2906.jpg)



![img](07Python38c3160b-2907.jpg)



![img](07Python38c3160b-2908.jpg)



![img](07Python38c3160b-2909.jpg)



i© Advanced CCi Demo



![img](07Python38c3160b-2911.jpg)



Eack For^a-rd

Refresh Hone

AjtoFill Print

■■■■■■■■ I■:c:a Ihost :80C0 /'eg i-b in .■''••ddveg

Advanced CGI Demo Form

My Cookie Setting

Enter cookie value

(optional)

Enter your name

(required)

What languages can you program in? (at least one squired}

Python □ PERL □ Java 匚 C++ □ PHP □ C □ JavaScript

Enter file to upload

Birowse

:a m-achine zone

图20-14



![img](07Python38c3160b-2913.jpg)



图20-14上传及多值表单页IE5浏览器，MacOS X系统

在图 20-17中，你可以看到数据提交后的结果展示。用户输入的所有数据都可以在页面中显示 出来。在最后对话框中指定的文件也被上传到了服务器端，并显示出来。

你也会注意到在结果页面下方的那个链接，它使用相同的CGI脚本，可以帮我们返回表单页。

如果我们单击下方的那个链接，没有任何表单数据提交给我们的脚本，因此会显示一个表单页 面。然而，如你在图 20-17中看到的一样，所有的东西都可以显示出来，并非是一个空的表单！我 们前边输入的信息都被显示出来了！在没有表单数据的情况下我们是怎样做到这一点的呢（将其隐 藏或者作为URL中的请求参数）？实际上秘密是这些数据都被保存在客户端的cookie中了。

用户的cookie将用户输入表单中的值都保存了起来，用户名，使用的语言，上传文件的信息都



会存储在cookie中。



当脚本检测到表单没有数据时，它会返回一个表单页面，但是在表单页面建立前，它们从客户 端的cookie中抓取了数据（当用户在单击了那个链接的时候将会自动传入）并且相应的将其填入表 单中。因此当表单最终显示出来时，先前的输入便会魔术般的显示在用户面前（图20-18）。

![img](07Python38c3160b-2916.jpg)



![img](07Python38c3160b-2917.jpg)



图 20-15

图20-15同一个高级CGI在Netscape4浏览器，Linux系统

我们相信你现在已经迫不及待的想看下这个程序了，详见例子20.8.

advcgi.py和我们本章前部分提到的CGI脚本friends3.py相当的像。它有表单页、结果页、 错误页可以返回。新的脚本中除了有所有的高级CGI特性外，我们还在脚本中增加了更多的面向对 象特征：用类和方法代替了一系列的函数。我们页面的HTML文本对我们的类来说都是静态的了，这

![img](07Python38c3160b-2918.jpg)



![img](07Python38c3160b-2919.jpg)



就意味着它们在实例中都是以常量出现的—虽然我们这里仅有一个实例。



图20-16



![img](07Python38c3160b-2922.jpg)



图20-16高级CGI提交演示Opera8 Win32系统

逐行解释(以块划分)

#### 1-7行

普通的起始、和模块导入行出现在这里。唯一你可能不太熟悉的模块是cStringlO,我们曾在第 10章简单讲解过它并在例20.1中用过。cStringIO.StingIO()会在字符串上创建一个类似文件的对 象，所以访问这个字符串与打开一个文件并使用文件句柄去访问数据很相似。

![img](07Python38c3160b-2923.jpg)



| I Opera 4.01 Unregistered version - [Advanced CGI Demo]      |          |      |
| ------------------------------------------------------------ | -------- | ---- |
| \|\|] File Edit View Navigation Bookmarks E-mail News Window Help | lffl x\| |      |
| 幻苎as乇n囯                                                  | a        |      |

![img](07Python38c3160b-2924.jpg)



![img](07Python38c3160b-2925.jpg)



![img](07Python38c3160b-2926.jpg)



![img](07Python38c3160b-2927.jpg)



![img](07Python38c3160b-2928.jpg)



Your Uploaded Data

Your cookie value is: Oh look mom, a cookie!

Your name is: Steven Alistair Kii'k

You can program in the following languages:

•    Fython

•    Java

•    C

Your uploaded file...

Name: Comments^txt C ontents:

COHHEWTS

Like most, of its Unix shell and script, language brethren^ uses the pound sign/hash inarfc ( # ) to indicate t-he beginr a line of cointient.. You can also start, a coinnent. in the trii the line. Basically, anyt-hing after t.he 1 #1 through the e the line will J：ip igiiorecl.

![img](07Python38c3160b-2929.jpg)



Click kere to ret-um to form.

IZ1 (Bl 1^ |卜邮施cslho:帅碑 dvcg i .py    二 1 uu% 二

图 20-17

图 20-17 Results page generated and returned by the Web server in Opera4 on Win32

#### 9-12行

在声明AdvCGI类之后，header和url（静态）变量被创建出来，在显示所有不同页面的方法中 会用到这些变量。

#### 14-80行

所有这个块中的代码都是用来创建、显示表单页面的。那些数据属性都是不言自明的。 getCPPcookieO取得Web客户端发来的cookie信息，而showFormO校对所有这些信息并把表单页面 返回给客户端。

![img](07Python38c3160b-2930.jpg)



![img](07Python38c3160b-2931.jpg)



![img](07Python38c3160b-2932.jpg)



![img](07Python38c3160b-2933.jpg)



![img](07Python38c3160b-2934.jpg)



![img](07Python38c3160b-2935.jpg)



![img](07Python38c3160b-2936.jpg)



![img](07Python38c3160b-2937.jpg)



![img](07Python38c3160b-2938.jpg)



Advanced CGI Demo Form

My Cookie Setting

• CPPuser = Oh look mom, a. cookie ■

Enter cookie value

|i:〕h look rnom.. a cookie!

Enter your name

|Steven Alistair Kirk (required)

![img](07Python38c3160b-2939.jpg)



What languages can you program in? (of.least.one. required)

E Python □ PERL E JavaC C-H-O PHP E !'?□ Jav^ctipt



![img](07Python38c3160b-2941.jpg)



图 20-18

图 20-18 Form page with data loaded from the Client cookie

#### 82-91行

这个代码块负责错误页面。

#### 93-144行

结果页面的生成使用了本块代码。setCPPcookieO方法要求客户端为我们的应用程序存储 cookie，而doResultsO方法聚集所有数据并把输出发回客户端。

Example 20.8 Advanced CGI Application (advcgi.py)

这个脚本有一个处理所有事情的主函数，AdvCGI，它有方法显示表单、错误或结果页面，同时

也可以从客户端(Web浏览器)读写cookie。



![img](07Python38c3160b-2943.jpg)



1    #!/usr/bin/env python

2

3    from cgi import FieldStorage

4    from os import environ

5    from cStringIO import StringIO

6    from urllib import quote, unquote

7    from string import capwords, strip, split, join

8

9    class AdvCGI(object):

10

11    header = 'Content-Type: text/html\n\n'

12    url = '/py/advcgi.py'

13

14    formhtml = '''<HTML><HEAD><TITLE>

15    Advanced CGI Demo</TITLE></HEAD>

16    <BODY><H2>Advanced CGI Demo Form</H2>

17    <FORM METHOD=post ACTION="%s" ENCTYPE="multipart/form-data">

18    <H3>My Cookie Setting</H3>

![img](07Python38c3160b-2944.jpg)



19    <LI> <CODE><B>CPPuser = %s</B></CODE>

20    <H3>Enter cookie value<BR>

21    <INPUT NAME=cookie value="%s"> (<I>optional</I>)</H3>

22    <H3>Enter your name<BR>

23    <INPUT NAME=person VALUE="%s"> (<I>required</I>)</H3>

24    <H3>What languages can you program in?

25    (<I>at least one required</I>)</H3>

26    %s

27    <H3>Enter file to upload</H3>

28    <INPUT TYPE=file NAME=upfile VALUE="%s" SIZE=45>

29    <P><INPUT TYPE=submit>

30    </FORM></BODY></HTML>'''

31

32    langSet = ('Python', 'PERL', 'Java', 'C++', 'PHP',

33    'C', 'JavaScript')

34    langItem = \

35    '<INPUT TYPE=checkbox NAME=lang VALUE="%s"%s> %s\n'

36

37    def getCPPCookies(self): # read cookies from client

38    if environ.has_key('HTTP_COOKIE'):

39    for eachCookie in map(strip, \

![img](07Python38c3160b-2945.jpg)



![img](07Python38c3160b-2946.jpg)



40    split(environ['HTTP_COOKIE'], ';')):

41    if len(eachCookie) > 6 and \

42    eachCookie[:3] == 'CPP':

43    tag = eachCookie[3:7]

44    try:

45    self.cookies[tag] = \

46    eval(unquote(eachCookie[8:]))

47    except (NameError, SyntaxError):

48    self.cookies[tag] = \

49    unquote(eachCookie[8:])

50    else:

51    self.cookies['info'] = self.cookies['user'] = ''

52

53    if self.cookies['info'] != '':

54    self.who, langStr, self.fn = \

55    split(self.cookies['info'], ':')

56    self.langs = split(langStr, ',')

57    else:

![img](07Python38c3160b-2947.jpg)



58    self.who = self.fn = ' '

![img](07Python38c3160b-2948.jpg)



59    self.langs = ['Python']

60

61 def showForm(self): # show fill-out form

62    self.getCPPCookies()

63    langStr = ''

64    for eachLang in AdvCGI.langSet:

65    if eachLang in self.langs:

66    langStr += AdvCGI.langItem % \

67    (eachLang, ' CHECKED', eachLang)

68    else:

69    langStr += AdvCGI.langItem % \

70    (eachLang, '', eachLang)

71

72    if not self.cookies.has_key('user') or \

73    self.cookies['user'] == '':

74    cookStatus = '<I>(cookie has not been set yet)</I>'

75    userCook = ''

76    else:

77    userCook = cookStatus = self.cookies['user']

78

79    print AdvCGI.header + AdvCGI.formhtml % (AdvCGI.url,

![img](07Python38c3160b-2949.jpg)



![img](07Python38c3160b-2950.jpg)



80    cookStatus, userCook, self.who, langStr, self.fn)

81

82    errhtml = '''<HTML><HEAD><TITLE>

83    Advanced CGI Demo</TITLE></HEAD>

84    <BODY><H3>ERROR</H3>

85    <B>%s</B><P>

86    <FORM><INPUT TYPE=button VALUE=Back

87    ONCLICK="window.history.back()"></FORM>

88    </BODY></HTML>'''

89

90    def showError(self):

91    print AdvCGI.header + AdvCGI.errhtml % (self.error)

92

93    reshtml = '''<HTML><HEAD><TITLE>

94    Advanced CGI Demo</TITLE></HEAD>

95    <BODY><H2>Your Uploaded Data</H2>

96    <H3>Your cookie value is: <B>%s</B></H3>

97    <H3>Your name is: <B>%s</B></H3>

![img](07Python38c3160b-2951.jpg)



98    <H3>You can program in the following languages:</H3>

![img](07Python38c3160b-2952.jpg)



99    <UL>%s</UL>

100    <H3>Your uploaded file...<BR>

101    Name: <I>%s</I><BR>

102    Contents:</H3>

103    <PRE>%s</PRE>

104    Click <A HREF="%s"><B>here</B></A> to return to form.

105    </BODY></HTML>'''

106

107    def setCPPCookies(self):# tell client to store cookies

108    for eachCookie in self.cookies.keys():

109    print 'Set-Cookie: CPP%s=%s; path=/' % \

110    (eachCookie, quote(self.cookies[eachCookie]))

111

112    def doResults(self):# display results page

113    MAXBYTES = 1024

114    langlist = ''

115    for eachLang in self.langs:

116    langlist = langlist + '<LI>%s<BR>' % eachLang

117

118    filedata = ''

119    while len(filedata) < MAXBYTES:# read file chunks

![img](07Python38c3160b-2953.jpg)



![img](07Python38c3160b-2954.jpg)



120    data = self.fp.readline()

121    if data == '': break

122    filedata += data

123    else: # truncate if too long

124    filedata += \

125    '... <B><I>(file truncated due to size)</I></B>'

126    self.fp.close()

127    if filedata == '':

128    filedata = \

129    <B><I>(file upload error or file not given)</I></B>

130    filename = self.fn

131

132    if not self.cookies.has_key('user') or \

133    self.cookies['user'] == '':

134    cookStatus = '<I>(cookie has not been set yet)</I>'

135    userCook = ''

136    else:

137    userCook = cookStatus = self.cookies['user']

![img](07Python38c3160b-2955.jpg)



138

![img](07Python38c3160b-2956.jpg)



139    self.cookies['info'] = join([self.who, \

140    join(self.langs, ','), filename], ':')

141    self.setCPPCookies()

142    print AdvCGI.header + AdvCGI.reshtml % \

143    (cookStatus, self.who, langlist,

144    filename, filedata, AdvCGI.url)

145

146    def go(self): # determine which page to return

147    self.cookies = {}

148    self.error = ''

149    form = FieldStorage()

150    if form.keys() == []:

151    self.showForm()

152    return

153

154    if form.has_key('person'):

155    self.who = capwords(strip(form['person'].value))

156    if self.who == '':

157    self.error = 'Your name is required. (blank)'

158    else:

159    self.error = 'Your name is required. (missing)'

![img](07Python38c3160b-2957.jpg)



![img](07Python38c3160b-2958.jpg)



![img](07Python38c3160b-2959.jpg)



160

161    if form.has_key('cookie'):

162    self.cookies['user'] = unquote(strip(\

163    form['cookie'].value))

164    else:

165    self.cookies['user'] = ''

166

167    self.langs = []

168    if form.has_key('lang'):

169    langdata = form['lang']

170    if type(langdata) == type([]):

171    for eachLang in langdata:

172    self.langs.append(eachLang.value)

173    else:

174    self.langs.append(langdata.value)

175    else:

176    self.error = 'At least one language required.

177

178    if form.has_key('upfile'):

![img](07Python38c3160b-2960.jpg)



179    upfile = form["upfile"]

180    self.fn = upfile.filename or ''

181    if upfile.file:

182    self.fp = upfile.file

183    else:

184    self.fp = StringIO('(no data)')

185    else:

186    self.fp = StringIO('(no file)')

187    self.fn = ''

188

189    if not self.error:

190    self.doResults()

191    else:

192    self.showError()

193

194    if __name__ == '__main__':

195    page = AdvCGI()

196    page.go()

doResultsO方法收集所有数据并把输出发回客户端。



146-196行

脚本一开始就实例化了一个AdvCGI页面对象，然后调用它的go（）方法让一切运转起来，这和严

格的基于过程编写的程序不同。go（）方法中包含读取所有新到的数据并决定显示哪个页面的逻辑。

如果没有给出名字或选定语言，错误页面将会被显示。如果没有收到任何输入数据，将调用 showFormO方法来输出表单，否则将调用doResultsO方法来显示结果页面。通过设置self.error 变量可以创建错误页面，这样做有两个目的。它不但可以让你把错误原因设置在字符串里，并且可 以作为一个标记表明有错误发生。如果该变量不为空，用户将会被导向到错误页面。

处理person字段（第154-159行）的方法和我们先前看到的一样，一个键-值对；然而，在收 集语言信息时却需要一点技巧，原因是我们必须检查一个（Mini）FieldStorage对象或一个该对象 的列表。我们将使用熟悉的type（）内建函数来达到目的。最终，我们会有一个单独或多个语言名的 列表，具体依赖于用户的选择情况。

使用cookie（第161-165行）来保管数据展示了如何利用它们来避免使用任何类型的CGI字段。 你一定注意到了代码里包含这些数据的地方没有调用CGI处理，这意味着数据并非来自FieldStorage 对象。这些数据是由Web客户端通过每一次请求和从cookie取得的值（包括用户的选择结果和用来 填充后续表单的已有信息）传给我们的。

![img](07Python38c3160b-2963.jpg)



![img](07Python38c3160b-2964.jpg)



![img](07Python38c3160b-2965.jpg)



因为showResultsO方法从客户那里取得了新的收入值，所以它负责设置cookie，通过调用 setCPPcookieO。而showFormO必须读出cookie中的值才能用表单页显示用户的当前选项。这通 过它对getCPPcookieO的调用实现。

最后，我们看看文件上传处理（第178-187行）。不论一个文件是否已经上传，FieldStorage都 会从file属性中获得一个文件句柄。在第180行，如果没有指明文件名，那么我们只须把它设成空 字符串。如果访问过value属性，那么文件的整个内容都会被放到value里。还有一个更好的做法， 你可以去访问文件指针一一file属性一一并且可以每次只读一行或者其他更慢一些的处理方法。

在我们的例子里，文件上传只是用户提交过程的一部分，所以我们可以简单的把文件指针传给 doResultsO函数，从文件中抽取数据。由于空间限制doResultsO将只显示文件的最前1K内容，这 也表明显示一个4M的二进制文件是不需要（或未必有效/有用）的。

Edit By Vheavens

### 20.8 Web(HTTP)服务器

到现在为止，我们已经讨论了如何使用Python建立Web客户端并用CGI请求处理帮助Web服务 器执行了一些工作。我们通过第20.2和20.3的学习知道了 Python可以用来建立简单和复杂的Web 客户端。而对复杂的CGI请求没有说明。

然而，我们在这章的焦点是探索建立Web服务器。如果说Firefox，Mozilla， IE，Opera，



Netscape, AOL, Safari, Camino, Epiphany, Galeon 和 Lynx 浏览器是最流行的一些 Web 客户 端，那么什么是最常用的Web服务器呢？它们就是Apache, Netscape IIS, thttpd, Zeus，和Zope。 由于这些服务器都远远超过了你的应用程序要求，这里我们使用Python建立简单但有用的Web服务 器。



### 20.8.1用Python建立Web服务器

由于已经打算建立这样的一个应用程序,你很自然的就需要创建个人素材,但是你将要用到的 所有的基础代码都在Python的标准库中。要建立一个Web服务，一个基本的服务器和一个“处理器” 是必备的。

基础的(Web)服务器是一个必备的模具。它的角色是在客户端和服务器端完成必要HTTP交互。 在BaseHTTPServer模块中你可以找到一个名叫HTTPServer的服务器基本类。

处理器是一些处理主要“Web服务”的简单软件。它们处理客户端的请求，并返回适当的文件， 静态的文本或者由CGI生成的动态文件。处理器的复杂性决定了你的Web服务器的复杂程度。Python 标准库提供了三种不同的处理器。

![img](07Python38c3160b-2968.jpg)



最基本，最普通的是vanilla处理器，被命名BaseHTTPResquestHandler,这个可以在基本 Web服务器的BaseHTTPServer模块中找到。除了获得客户端的请求外，不再执行其他的处理工作， 因此你必须自己完成它们，这样就导致了出现了 myhttpd.py服务的出现。

![img](07Python38c3160b-2969.jpg)



![img](07Python38c3160b-2970.jpg)



用 于 SimpleHTTPServer 模 块 中 的 SimpleHTTPRequestHandler ， 建 立 在 BaseHTTPResquestHandler基础上，直接执行标准的GET和HEAD请求。这虽然还不算完美，但已经 可以完成一些简单的功能啦。

最后，我们来看下用于CGIHTTPServer模块中的CGIHTTPRequestHandler处理器，它可以获取 SimpleHTTPRequestHandler并为POST请求提供支持。它可以调用CGI脚本完成请求处理过程，也可 以将生成的HTML脚本返回给客户端。

这三个模块和他们的类在表20.6中有描述。

为了能理解在SimpleHTTPServer和CGIHTTPServer模块中的其他高级处理器如何工作的，我们 将对BaseHTTPRequestHandler实现简单的GET处理功能。

![img](07Python38c3160b-2971.jpg)



Table 20.6 Web Server Modules and Classes



模块

BaseHTTPServer

SimpleHTTPServer

CGIHTTPServer



描述

提供基本的Web服务和处理器类，分别是HTTPServer和 BaseHTTPRequestHandler

包含执行 GET 和 HEAD 请求的 SimpleHTTPRequestHandler 类

包含处理POST请求和执行CGICGIHTTPRequestHandler类

在例子20.9中，我们展示了一个Web服务器的全部工作代码，myhttpd.py.

这个服务的子类BaseHTTPRequestHandler只包含do_GET()方法在基础服务器接到GET请求时被 调用。

尝试打开客户端传来的路径，如果实现了，将会返回“0K”状态(200)，并转发下载的Web页 面，否则将会返回404状态。

main()函数只是简单的将Web服务器类实例化，然后启动它进入永不停息的服务循环，如果 遇到了~C中断或者类似的键输入则会将其关闭。如果你可以访问并运行这个服务器，你就会发现它 会显示出一些类似这样的登录输出：

\# myhttpd.py

Welcome to the machine... Press ’C once or twice to quit

localhost - - [26/Aug/2000 03:01:35] "GET /index.html HTTP/1.0" 200 -localhost - - [26/Aug/2000 03:01:29] code 404, message File Not Found: /x.html localhost

\- - [26/Aug/2000 03:01:29] "GET /dummy.html HTTP/1.0" 404 -

localhost - - [26/Aug/2000 03:02:03] "GET /hotlist.htm HTTP/1.0" 200 -

当然，我们的小Web服务器是太简单了，它甚至不能处理普通的文本文件。我们将这部分给读 者，这部分可以在本章最后的练习题中找到。

正如你所看到的一样，建立一个Web服务器并在纯Python脚本中运行并不会花太多时间。为你 的特定应用程序定制改进处理器将需要做更多事情。请查看本部分的相关库来获得更多模块及其类 的信息。

Example 20.9 Simple Web Server (myhttpd.py)

这个简单的Web服务器可以读取GET请求，获取Web页面(.html文件)并将其返回给客户端。 它通过使用BaseHTTPServer的BaseHTTPRequestHandler处理器执行do_GET()方法来处理GET请求。

![img](07Python38c3160b-2973.jpg)



![img](07Python38c3160b-2974.jpg)



![img](07Python38c3160b-2975.jpg)



![img](07Python38c3160b-2976.jpg)



![img](07Python38c3160b-2977.jpg)



1    #!/usr/bin/env python

2

3    from os import curdir, sep

4    from BaseHTTPServer import \

5    BaseHTTPRequestHandler, HTTPServer

6

7    class MyHandler(BaseHTTPRequestHandler):

8

9    def do_GET(self):

10    try:

11    f = open(curdir + sep + self.path)

12    self.send_response(200)

13    self.send_header('Content-type',

14    'text/html')

15    self.end_headers()

16    self.wfile.write(f.read())

17    f.close()

18    except IOError:

![img](07Python38c3160b-2978.jpg)



19    self.send_error(404,

![img](07Python38c3160b-2979.jpg)



20    'File Not Found: %s' % self.path)

21

22    def main():

23    try:

24    server = HTTPServer(('', 80), MyHandler)

25    print 'Welcome to the machine...',

26    print ’Press C once or twice to quit.’

27    server.serve_forever()

28    except KeyboardInterrupt:

29    print ”C received, shutting down server’

30    server.socket.close()

31

32    if __name__ == ’__main__’:

33    main()

### 20．9 相关模块

在表20.7中，我们列出了对Web开发有用的模块。也许你会想看下第十七章的因特网客户端编 程，还有第二十三章的Web服务部分的模块，这些对Web应用都是有用的。

![img](07Python38c3160b-2980.jpg)



Table 20.7 Web编程相关模块



模块/包    描述

Web应用程序

cgi    从标准网关接口（CGI）获取数据

cgitbc    处理CGI返回数据

htmllib 解析HTML文件时用的旧HTML解析器；HTMLParser类扩展自sgmllib.SGMLParser HTMLparserc 新的非基于SGML的HTML、XHTML解析器 htmlentitydefs HTML普通实体定义

用于HTTP状态管理的服务器端cookie HTTP客户端的cookie处理类 控制器：向浏览器加载Web文档 解析简单的SGML文件 解析robots.txt文件作URL的“可获得性’ 用来创建HTTP客户端



Cookie cookielibe webbrowser sgmllib

robotparsera    解析robots.txt文件作URL的“可获得性”分析

httpliba XML解析 xmllib

原始的简单XML解析器（已过时/不推荐使用）

包含许多不同XML特点的解析器（见下文）

简单的适用于SAX2的XML（SAX）解析器 文本对象模型（D0M）的XML解析器 树型的XML解析器，基于Elemnt flexible container对象



xmlb

![img](07Python38c3160b-2982.jpg)



xml.saxb xml.domb xml.etreef

![img](07Python38c3160b-2983.jpg)



xml.parsers.expatb    非验证型Expat XML解析器的接口

xmlrpclibc 通过HTTP提供XML远程过程调用（RPC）客户端

Table 20.7 Web编程相关模块（续） 模块/包    描述

XML解析



SimpleXMLRPCServerc

DocXMLRPCServerd



Python XML-RPC服务器的基本框架 自描述XML-RPC服务器的框架



Web服务器 BaseHTTPServer SimpleHTTPServer CGIHTTPServer



用来开发Web服务器的抽象类 处理最简单的HTTP请求（HEAD和GET）

不但能像SimpleHTTPServers —样处理Web文件，还能处理CGI请求 （HTTP POST）



wsgiref    Web服务器和Python Web应用程序间的标准接口

第三方开发包（非标准库）

HTMLgen    协助CGI把Python对象转换成可用的HTML

<http://starship.python.net/crew/friedrich/HTMLgen/> html/main.html

BeautifulSoup    HTML、XML 解析器及转换器 <http://crummy.com/software/BeautifulSoup>

邮件客户端协议

![img](07Python38c3160b-2984.jpg)



![img](07Python38c3160b-2985.jpg)



poplib    用来创建POP3客户端

imaplib    用来创建IMAP4客户端

邮件、M頂E处理及数据编码格式

emailc    管理e-mail消息的工具包，包括MIME和其它基于RFC2822的消息

mailbox    e-mail消息的信箱类

mailcap    解析mailcap文件，从中获得MIME应用授权

Table 20.7 Web编程相关模块（续）

模块/包    描述

邮件、M頂E处理及数据编码格式

mimetools    提供封装MIME编码信息的功能

mimetypes    提供和MIME类型相关的功能

MimeWriter生成MIME编码的多种文件

multipart    可以解析多种MIME编码文件

quopri    编解码使用quoted-printable规范的数据

rfc822    解析符合RFC822标准的e-mail头信息

smtplib    用来创建SMTP （简单邮件传输协议）客户端

base64    编解码使用base64标准的数据

![img](07Python38c3160b-2986.jpg)



binascii编解码使用base64、binhex、uu （模块）格式的数据 binhex    编解码使用binhex4标准的数据

![img](07Python38c3160b-2987.jpg)



uu    编解码使用uuencode格式的数据

因特网协议

httpliba    用来创建HTTP客户端

ftplib    用来创建 FTP（File Transfer Protocol）客户端

gopherlib 用来创建Gopher客户端 telnetlib 用来创建Telnet客户端

nntplib 用来创建NNTP （网络新闻传输协议［Usenet］）客户端

a.    Python    1.6中新增。

b.    Python    2.0中新增。

c.    Python    2.2中新增。

d.    Python    2.2中新增。

e.    Python    2.4中新增。

f.    Python    2.5中新增。

### 20.10 练习

20-1.    urllib模块及文件。

请修改friends3.py脚本，把名字和相应的朋友数量存储在一个两列的磁盘文本文件中，以后



每次运行脚本都添加名字。附加题：增加一些代码把这种文件的内容转储到Web浏览器里（以HTML 格式）。附加题：增加一个链接，用以清空文件中的所有名字。



20-2. urllib模块。编写一个程序，它接收一个用户输入的URL （可以是一个Web页面或一 个 FTP 文件，例如，http:// python.org 或 [ftp://ftp.python.org/pub/python/README），然后下](ftp://ftp.python.org/pub/python/README%ef%bc%89%ef%bc%8c%e7%84%b6%e5%90%8e%e4%b8%8b) 载它并以相同的文件名（如果你的系统不支持也可以把它改成和原文件相似的名字）存储到电脑上。 Web页面（HTTP）应保存成.htm或.html文件，而FTP文件应保持其扩展名。

20-3. urllib模块。重写例11.4的grabWeb.py脚本，它会下载一个Web页面，并显示生成 的HTML文件的第一个和最后一个非空白行，你应使用urlopenO来代替urlretrieveO来直接处理 数据（这样就不必先下载所有文件再处理它了）。

20-4. URL和正则表达式。你的浏览器也许会保存你最喜欢的Web站点的URL,以“书签” 式的HTML文件（Mozilla发行品的浏览器就是如此）或者以“收藏夹”里一组.URL文件（IE既是如 此）的形式保存。查看你的浏览器记录“热门链接”的办法，并定位其所在和存储方式。不去更改 任何文件，剔除对应Web站点（如果给定了的话）的URL和名字，生成一个以名字和链接作为输出 的双列列表，并把这些数据保存到硬盘文件中。截取站点名和URL,确保每一行的输出不超过80个 字符。

![img](07Python38c3160b-2990.jpg)



![img](07Python38c3160b-2991.jpg)



![img](07Python38c3160b-2992.jpg)



20-5. URL、urllib模块、异常、已编码正则表达式。作为对上一个问题的延伸，给你的脚 本增加代码来测试你所喜欢的链接。记录下无效链接（及其名字），包括无效的Web站点和已经被 删除的Web页面。只输出并在磁盘中保存依然有效的链接。

20-6. 错误检测。friends3.py脚本在没有选择任意一个单选按钮指定好友的数目时会返回 一个错误提示。在更新CGI脚本是如果没有输入名字（例如空字符或空白）也会返回一个错误。附 加题：目前为止我们探讨的仅是服务器端的错误检测。探索JavaScript编程，并通过创建JavaScript 代码来同时检测错误，以确保这些错误在到达服务器前被终止，这样便实现了客户端错误检测。

下面的问题20-7到20-10涉及Web服务器的访问日志文件和正则表达式。Web服务器（及其管 理员）通常需要保存访问日志文件（一般是主Web的server文件夹里的logs/access_log）来跟踪 文件请求。一段时间之后，这些逐渐变大的文件需要被保存或删节。为什么不能仅保存有用的信息 而删除这些文件来节省磁盘空间呢？通过下面的习题，你会练习正则表达式和如何使用它们进行归 档及分析Web服务器数据。

20-7.计算日志文件中有多少种请求（GET vs POST）。

20-8.    计算成功下载的页面/数据：显示所有返回值为200 （OK［没有错误发生］）的链接，以

及每个链接被访问的次数。

20-9. 计算错误：显示所有产生错误的链接（返回值为400或500）以及每个链接被访问的次



数。

20-10. 跟踪IP地址：对每个IP地址，输出每个页面/数据下载情况的列表，以及这些链接 被访问的次数。

20-11. 简单CGI。为Web站点创建“评论”或“反馈”页面。由表单获得用户反馈，在脚本 中处理数据，最后返回一个“thank you”页面。

20-12. 简单CGI。创建一个Web客户薄。接受用户输入的名字、e-mail地址、日志，并将 其保存到文件中(自定义格式)。类似上一个题，返回一个“thanks for filling out a guestbooks entry”页面。同时再给用户提供一个查看客户薄的链接。

20-13. Web浏览器Cookie和Web站点注册。更改你对习题20-4的答案。你现在可以使用用 户名-密码信息来注册Web站点，而不必只用简单的基于文本的菜单系统。附加题：想办法让自己熟 悉Web浏览器cookie，并在最后登录成功后将会话保持4个小时。

20-14. Web客户端。移植例20.1的Web爬虫脚本crawler.py，使用HTMLParser模块或 BeautifulSoup 解析系统。

20-15. 错误处理。当一个CGI脚本崩溃时会发生什么？如何用cgitb模块提供帮助？

20-16. CGI、文件升级、及Zip文件。创建一个不仅能保存文件到服务器磁盘，而且能智 能解压Zip文件(或其它压缩档)到同名子文件夹的CGI应用程序。

20-17. Zope、Plone、TurboGears、及Django。研宄每一个复杂的Web开发平台并分别创建 一个简单的应用程序。

20-18.    Web数据库应用程序。思考对你Web数据库应用程序支持的数据库构架。对于多用户

的应用程序，你需要支持每个用户对数据库的全部内容的访问，但每个人可能分别输入。一个例子 就是你家人及亲属的“地址簿”。每个成员成功登录后，显示出来的页面应该有几个选项 add an entry, view my entry, update my entry, remove or delete my entry,及 view all entries(整个数据 库)。

20-19. 电子商务引擎。使用你在习题13-11中建立的类，增加一些产品清单建立一个电子商 务Web站点。确保你的应用程序支持多个用户，机器每个用户的注册功能。

20 - 20. 字典及cgi模块相关。正如你所知道的，cgi.FieldStorageO方法返回一个字典类 对象，包括提交的CGI变量的键值对。你可以使用这个对象的keys()和has_key()方法。在Python1.5 中，get()方法被添加到字典中，用它可以返回给定键的值，当键不存在时返回一个默认值。 FieldStorage对象却没有这个方法。让我们依照用户手册的形式：

form = cgi.FieldStorage()



为cgi.py中类的定义添加一个类似的get()方法(你可以把它重命名为mycgi.py或其他你喜欢 的名字)，以便能像下面这样操作：

if form.has_key('who'):

who = form['who'].value

else:

who = '(no name submitted)'

. . . 也可以用一行实现，这样就更像字典的形式了： howmany = form.get('who', '(no name submitted)')

20-21.高级Web客户端。在20.7中的myhttpd.py代码只能读取HTML文件并将其返回到客 户端。添加对以“.txt”结束的普通的文本的支持。确保返回正确的“text/plain”de M頂E类。附 加题：添加对以“.jpg”及“.jpeg”结束的JPEG文件的支持，并返回“image/jpeg”的MIME类型。

20 - 22. 高级Web客户端。作为crawl.py的输入的URL必须是以“http://”协议指示符开 头，高层的URL必须包含一个反斜线，例如：http:// [www.prenhallprofessional.com/.加强](http://www.prenhallprofessional.com/.%e5%8a%a0%e5%bc%ba) crawl.py的功能，允许用户只输入主机名(没有协议部分[确保是HTTP])，反斜线是可选的。例如： [www.prenhallprofessional.com](http://www.prenhallprofessional.com) 应该是可接受的输入形式。

![img](07Python38c3160b-2997.jpg)



![img](07Python38c3160b-2998.jpg)



![img](07Python38c3160b-2999.jpg)



20 - 23. 高级Web客户端。更改20.3小节中的crawl.py脚本，让它也下载“ftp:”型的链 接。所有的“mailto:”都会被crawl.py忽略。增加代码确保它也忽略“telnet:”、“news:”、“gopher:”、 和“about:”型的链接。

20 - 24.高级Web客户端。20.3小节中的crawl.py脚本仅从相同站点内的Web页面中找到链 接，下载了.html文件，却不处理/保存图片这类对页面同样有意义的“文件”。对于那些允许URL缺 少末端斜线(/)的服务器，这个脚本也不能处理。给crawl.py增添两个类来解决这些问题。

一个是urllib.FancyURLOpener类的子类My404UrlOpener，它仅包含一个方法， http_error_404()，用该方法来判断收到的404错误中是不是包含缺少末端斜线的URL。如果有，它 就添加斜线并从新请求(仅一次)。如果仍然失败，才返回一个真正的404错误。你必须用该类的一 个实例来设置urllib._urlopener，这样urllib才能使用它。

创建另一个类LinkImageParser，它派生自htmllib.HTMLParser。这个类应有一个构造器用来 调用基类的构造器，并且初始化一个列表用来保存从Web页面中解析出的图片文件。应重写 handle_image()方法，把图片文件名添加到图片列表中(这样就不会像现在的基类方法那样丢弃它 们了)。

![img](07Python38c3160b-3000.jpg)



 
