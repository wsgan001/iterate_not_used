---
author: evo
comments: true
date: 2018-05-03 01:47:06+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-introduction/
slug: python-introduction
title: Python 简介
wordpress_id: 4953
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE






  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)


  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)




# TODO






  * aaa




# MOTIVE






  * aaa





* * *





## **Python是什么?**


Python（英国发音：/ˈpaɪθən/ 美国发音：/ˈpaɪθɑːn/）, 是一种面向对象的解释型计算机程序设计语言，由荷兰人Guido van Rossum于1989年发明，第一个公开发行版发行于1991年。

如果您想要更快、更系统地学会Python，您最好采用边学边练（[**Python微课**](https://www.w3cschool.cn/minicourse/play/python3course?cp=380)）的学习模式。

Python是纯粹的自由软件， 源代码和解释器CPython遵循 GPL(GNU General Public License)协议 。Python语法简洁清晰，特色之一是强制用空白符(white space)作为语句缩进。Python具有丰富和强大的库。它常被昵称为胶水语言，能够把用其他语言制作的各种模块（尤其是C/C++）很轻松地联结在一起。常见的一种应用情形是，使用Python快速生成程序的原型（有时甚至是程序的最终界面），然后对其中有特别要求的部分，用更合适的语言改写，比如3D游戏中的图形渲染模块，性能要求特别高，就可以用C/C++重写，而后封装为Python可以调用的扩展类库。需要注意的是在您使用扩展类库时可能需要考虑平台问题，某些可能不提供跨平台的实现。



## **Python编码风格**


Python在设计上坚持了清晰划一的风格，这使得Python成为一门易读、易维护，并且被大量用户所欢迎的、用途广泛的语言。

设计者开发时总的指导思想是，对于一个特定的问题，只要有一种最好的方法来解决就好了。这在由Tim Peters写的Python格言（称为The Zen of Python）里面表述为：There should be one-- and preferably only one --obvious way to do it. 这正好和Perl语言（另一种功能类似的高级动态语言）的中心思想TMTOWTDI（There's More Than One Way To Do It）完全相反。

Python的作者有意的设计限制性很强的语法，使得不好的编程习惯（例如if语句的下一行不向右缩进）都不能通过编译。其中很重要的一项就是Python的缩进规则。

一个和其他大多数语言（如C）的区别就是，一个模块的界限，完全是由每行的首字符在这一行的位置来决定的（而C语言是用一对花括号{}来明确的定出模块的边界的，与字符的位置毫无关系）。这一点曾经引起过争议。因为自从C这类的语言诞生后，语言的语法含义与字符的排列方式分离开来，曾经被认为是一种程序语言的进步。不过不可否认的是，通过强制程序员们缩进（包括if，for和函数定义等所有需要使用模块的地方），Python确实使得程序更加清晰和美观。


## **设计定位**


Python的设计哲学是“优雅”、“明确”、“简单”。因此，Perl语言中“总是有多种方法来做同一件事”的理念在Python开发者中通常是难以忍受的。Python开发者的哲学是“用一种方法，最好是只有一种方法来做一件事”。在设计Python语言时，如果面临多种选择，Python开发者一般会拒绝花俏的语法，而选择明确的没有或者很少有歧义的语法。由于这种设计观念的差异，Python源代码通常被认为比Perl具备更好的可读性，并且能够支撑大规模的软件开发。这些准则被称为Python格言。在Python解释器内运行import this可以获得完整的列表。

Python开发人员尽量避开不成熟或者不重要的优化。一些针对非重要部位的加快运行速度的补丁通常不会被合并到Python内。所以很多人认为Python很慢。不过，根据二八定律，大多数程序对速度要求不高。在某些对运行速度要求很高的情况，Python设计师倾向于使用JIT技术，或者用使用C/C++语言改写这部分程序。可用的JIT技术是PyPy。

Python是完全面向对象的语言。函数、模块、数字、字符串都是对象。并且完全支持继承、重载、派生、多继承，有益于增强源代码的复用性。Python支持重载运算符和动态类型。相对于Lisp这种传统的函数式编程语言，Python对函数式设计只提供了有限的支持。有两个标准库(functools, itertools)提供了Haskell和Standard ML中久经考验的函数式程序设计工具。

虽然Python可能被粗略地分类为“脚本语言”（script language），但实际上一些大规模软件开发计划例如Zope、Mnet及BitTorrent，Google也广泛地使用它。Python的支持者较喜欢称它为一种高级动态编程语言，原因是“脚本语言”泛指仅作简单程序设计任务的语言，如shellscript、VBScript等只能处理简单任务的编程语言，并不能与Python相提并论。

Python本身被设计为可扩充的。并非所有的特性和功能都集成到语言核心。Python提供了丰富的API和工具，以便程序员能够轻松地使用C语言、C++、Cython来编写扩充模块。Python编译器本身也可以被集成到其它需要脚本语言的程序内。因此，很多人还把Python作为一种“胶水语言”（glue language）使用。使用Python将其他语言编写的程序进行集成和封装。在Google内部的很多项目，例如Google Engine使用C++编写性能要求极高的部分，然后用Python或Java/Go调用相应的模块。《Python技术手册》的作者马特利（Alex Martelli）说：“这很难讲，不过，2004 年，Python 已在Google 内部使用，Google 召募许多 Python 高手，但在这之前就已决定使用Python，他们的目的是 Python where we can, C++ where we must，在操控硬件的场合使用 C++，在快速开发时候使用 Python。”


## **执行**


Python在执行时，首先会将.py文件中的源代码编译成Python的byte code（字节码），然后再由Python Virtual Machine（Python虚拟机）来执行这些编译好的byte code。这种机制的基本思想跟Java，.NET是一致的。然而，Python Virtual Machine与Java或.NET的Virtual Machine不同的是，Python的Virtual Machine是一种更高级的Virtual Machine。这里的高级并不是通常意义上的高级，不是说Python的Virtual Machine比Java或.NET的功能更强大，而是说和Java 或.NET相比，Python的Virtual Machine距离真实机器的距离更远。或者可以这么说，Python的Virtual Machine是一种抽象层次更高的Virtual Machine。

基于C的Python编译出的字节码文件，通常是.pyc格式。

除此之外，Python还可以以交互模式运行，比如主流操作系统Unix/Linux、Mac、Windows都可以直接在命令模式下直接运行Python交互环境。直接下达操作指令即可实现交互操作。


## **Python优点**


1.简单：Python是一种代表简单主义思想的语言。阅读一个良好的Python程序就感觉像是在读英语一样。它使你能够专注于解决问题而不是去搞明白语言本身。

2.易学：Python极其容易上手，因为Python有极其简单的说明文档[5]  。

3.速度快：Python 的底层是用 C 语言写的，很多标准库和第三方库也都是用 C 写的，运行速度非常快。

4.免费、开源：Python是FLOSS（自由/开放源码软件）之一。使用者可以自由地发布这个软件的拷贝、阅读它的源代码、对它做改动、把它的一部分用于新的自由软件中。FLOSS是基于一个团体分享知识的概念。

5.高层语言：用Python语言编写程序的时候无需考虑诸如如何管理你的程序使用的内存一类的底层细节。

6.可移植性：由于它的开源本质，Python已经被移植在许多平台上（经过改动使它能够工作在不同平台上）。这些平台包括Linux、Windows、FreeBSD、Macintosh、Solaris、OS/2、Amiga、AROS、AS/400、BeOS、OS/390、z/OS、Palm OS、QNX、VMS、Psion、Acom RISC OS、VxWorks、PlayStation、Sharp Zaurus、Windows CE、PocketPC、Symbian以及Google基于linux开发的android平台。

7.解释性：一个用编译性语言比如C或C++写的程序可以从源文件（即C或C++语言）转换到一个你的计算机使用的语言（二进制代码，即0和1）。这个过程通过编译器和不同的标记、选项完成。运行程序的时候，连接/转载器软件把你的程序从硬盘复制到内存中并且运行。而Python语言写的程序不需要编译成二进制代码。你可以直接从源代码运行 程序。在计算机内部，Python解释器把源代码转换成称为字节码的中间形式，然后再把它翻译成计算机使用的机器语言并运行。这使得使用Python更加简单。也使得Python程序更加易于移植。

8.面向对象：Python既支持面向过程的编程也支持面向对象的编程。在“面向过程”的语言中，程序是由过程或仅仅是可重用代码的函数构建起来的。在“面向对象”的语言中，程序是由数据和功能组合而成的对象构建起来的。

9.可扩展性：如果需要一段关键代码运行得更快或者希望某些算法不公开，可以部分程序用C或C++编写，然后在Python程序中使用它们。

10.可嵌入性：可以把Python嵌入C/C++程序，从而向程序用户提供脚本功能。

11.丰富的库：Python标准库确实很庞大。它可以帮助处理各种工作，包括正则表达式、文档生成、单元测试、线程、数据库、网页浏览器、CGI、FTP、电子邮件、XML、XML-RPC、HTML、WAV文件、密码系统、GUI（图形用户界面）、Tk和其他与系统有关的操作。这被称作Python的“功能齐全”理念。除了标准库以外，还有许多其他高质量的库，如wxPython、Twisted和Python图像库等等。

12.规范的代码：Python采用强制缩进的方式使得代码具有较好可读性。而Python语言写的程序不需要编译成二进制代码。


## **Python缺点**


1.单行语句和命令行输出问题：很多时候不能将程序连写成一行，如import sys;for i in sys.path:print i。而perl和awk就无此限制，可以较为方便的在shell下完成简单程序，不需要如Python一样，必须将程序写入一个.py文件。

2.独特的语法:这也许不应该被称为局限，但是它用缩进来区分语句关系的方式还是给很多初学者带来了困惑。即便是很有经验的Python程序员，也可能陷入陷阱当中。最常见的情况是tab和空格的混用会导致错误，而这是用肉眼无法分别的。

3.运行速度慢：这里是指与C和C++相比。


## **Python 工具**


1.Tkinter:Python默认的图形界面接口。Tkinter是一个和Tk接口的Python模块，Tkinter库提供了对Tk API的接口，它属于Tcl/Tk的GUI工具组。

2.PyGTK:用于python GUI程序开发的GTK+库。GTK就是用来实现GIMP和Gnome的库。

3.PyQt:用于python的Qt开发库。QT就是实现了KDE环境的那个库，由一系列的模块组成，有qt, qtcanvas, qtgl, qtnetwork, qtsql, qttable, qtui and qtxml，包含有300个类和超过5750个的函数和方法。PyQt还支持一个叫qtext的模块，它包含一个QScintilla库。该库是Scintillar编辑器类的Qt接口。

4.wxPython:GUI编程框架，熟悉MFC的人会非常喜欢，简直是同一架构（对于初学者或者对设计要求不高的用户来说，使用Boa Constructor可以方便迅速的进行wxPython的开发）

5.PIL:python提供强大的图形处理的能力，并提供广泛的图形文件格式支持，该库能进行图形格式的转换、打印和显示。还能进行一些图形效果的处理，如图形的放大、缩小和旋转等。是Python用户进行图象处理的强有力工具。

6.Psyco:一个Python代码加速度器，可使Python代码的执行速度提高到与编译语言一样的水平。

7.xmpppy:Jabber服务器采用开发的XMPP协议，Google Talk也是采用XMPP协议的IM系统。在Python中有一个xmpppy模块支持该协议。也就是说，我们可以通过该模块与Jabber服务器通信，是不是很Cool。

8.PyMedia:用于多媒体操作的python模块。它提供了丰富而简单的接口用于多媒体处理(wav, mp3, ogg, avi, divx, dvd, cdda etc)。可在Windows和Linux平台下使用。

9.Pmw:Python megawidgets，Python超级GUI组件集，一个在python中利用Tkinter模块构建的高级GUI组件，每个Pmw都合并了一个或多个Tkinter组件，以实现更有用和更复杂的功能。

10.PyXML:用Python解析和处理XML文档的工具包，包中的4DOM是完全相容于W3C DOM规范的。它包含以下内容：


## **Python 标准库**


Python拥有一个强大的标准库。Python语言的核心只包含数字、字符串、列表、字典、文件等常见类型和函数，而由Python标准库提供了系统管理、网络通信、文本处理、数据库接口、图形系统、XML处理等额外的功能。Python标准库命名接口清晰、文档良好，很容易学习和使用。

Python社区提供了大量的第三方模块，使用方式与标准库类似。它们的功能无所不包，覆盖科学计算、Web开发、数据库接口、图形系统多个领域，并且大多成熟而稳定。第三方模块可以使用Python或者C语言编写。SWIG,SIP常用于将C语言编写的程序库转化为Python模块。Boost C++ Libraries包含了一组库，Boost.Python，使得以 Python 或 C++ 编写的程序能互相调用。借助于拥有基于标准库的大量工具、能够使用低级语言如C和可以作为其他库接口的C++，Python已成为一种强大的应用于其他语言与工具之间的胶水语言。



Python标准库的主要功能有：

1.文本处理，包含文本格式化、正则表达式匹配、文本差异计算与合并、Unicode支持，二进制数据处理等功能

2.文件处理，包含文件操作、创建临时文件、文件压缩与归档、操作配置文件等功能

3.操作系统功能，包含线程与进程支持、IO复用、日期与时间处理、调用系统函数、写日记(logging)等功能

4.网络通信，包含网络套接字，SSL加密通信、异步网络通信等功能

5.网络协议，支持HTTP，FTP，SMTP，POP，IMAP，NNTP，XMLRPC等多种网络协议，并提供了编写网络服务器的框架W3C格式支持，包含HTML，SGML，XML的处理。

6.其它功能，包括国际化支持、数学运算、HASH、Tkinter等


## **Python 开发环境**






  * IDLE：Python内置IDE (随python安装包提供)


  * PyCharm  ：由著名的JetBrains公司开发，带有一整套可以帮助用户在使用Python语言开发时提高其效率的工 具，比如调试、语法高亮、Project管理、代码跳转、智能提示、自动完成、单元测试、版本控制。此外，该IDE提供了一些高级功能，以用于支持Django框架下的专业Web开发。


  * Komodo和Komodo Edit：后者是前者的免费精简版


  * PythonWin：ActivePython或pywin32均提供该IDE，仅适用于Windows


  * SPE（Stani's Python Editor）：功能较多的自由软件，基于wxPython


  * Ulipad：功能较全的自由软件，基于wxPython；作者是中国Python高手limodou


  * WingIDE：可能是功能最全的IDE，但不是自由软件(教育用户和开源用户可以申请免费key)


  * Eric：基于PyQt的自由软件，功能强大。全名是：The Eric Python IDE


  * DrPython


  * PyScripter：使用Delphi开发的轻量级的开源Python IDE， 支持Python2.6和3.0。


  * PyPE：一个开源的跨平台的PythonIDE。


  * bpython： 类Unix操作系统下使用curses库开发的轻量级的Python解释器。语法提示功能。


  * eclipse + pydev插件：方便调试程序


  * emacs：自带python支持，自动补全、refactor等功能需要插件支持


  * Vim: 最新7.3版编译时可以加入python支持，提供python代码自动提示支持


  * Visual Studio 2003 + VisualPython：仅适用Windows，已停止维护，功能较差


  * SlickEdit


  * Visual Studio 2010 + Python Tools for Visual Studio


  * TextMate


  * Netbeans IDE


  * Sublime




## **Python 著名应用**






  * Pylons-Web应用框架


  * Zope- 应用服务器


  * Plone- 内容管理系统


  * Django- 鼓励快速开发的Web应用框架


  * Uliweb- 国人开发的轻量级Web框架


  * TurboGears- 另一个Web应用快速开发框架


  * Twisted--Python的网络应用程序框架


  * Python Wikipedia Robot Framework- MediaWiki的机器人程序


  * MoinMoinWiki- Python写成的Wiki程序


  * flask- Python 微Web框架


  * tornado- 非阻塞式服务器


  * Webpy- Python 微Web框架


  * Bottle- Python 微Web框架


  * EVE- 网络游戏EVE大量使用Python进行开发


  * Reddit - 社交分享网站


  * Dropbox - 文件分享服务


  * Pylons - Web应用框架


  * TurboGears - 另一个Web应用快速开发框架


  * Fabric - 用于管理成百上千台Linux主机的程序库


  * Trac - 使用Python编写的BUG管理系统


  * Mailman - 使用Python编写的邮件列表软件


  * Mezzanine - 基于Django编写的内容管理系统


  * Blender - 以C与Python开发的开源3D绘图软件




## **附加资料**


官网:[https://www.python.org/](https://www.python.org/)

API文档:[https://docs.python.org/3/](https://docs.python.org/3/)

下载:[https://www.python.org/downloads/](https://www.python.org/downloads/)

教程:[http://www.w3cschool.cn/python/python-tutorial.html](https://www.w3cschool.cn/python/python-tutorial.html)






## Python 简介






Python是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。

Python的设计具有很强的可读性，相比其他语言经常使用英文关键字，其他语言的一些标点符号，它具有比其他语言更有特色语法结构。








  * **Python 是一种解释型语言：** 这意味着开发过程中没有了编译这个环节。类似于PHP和Perl语言。


  * **Python 是交互式语言：** 这意味着，您可以在一个Python提示符，直接互动执行写你的程序。


  * **Python 是面向对象语言:** 这意味着Python支持面向对象的风格或代码封装在对象的编程技术。


  * **Python是初学者的语言：**Python 对初级程序员而言，是一种伟大的语言，它支持广泛的应用程序开发，从简单的文字处理到 WWW 浏览器再到游戏。






* * *





## Python发展历史


Python是由Guido van Rossum在八十年代末和九十年代初，在荷兰国家数学和计算机科学研究所设计出来的。

Python 本身也是由诸多其他语言发展而来的,这包括 ABC、Modula-3、C、C++、Algol-68、SmallTalk、Unix shell 和其他的脚本语言等等。

像Perl语言一样, Python 源代码同样遵循 GPL(GNU General Public License)协议。

现在Python是由一个核心开发团队在维护，Guido van Rossum 仍然占据着至关重要的作用，指导其进展。





* * *





## Python特点







  * **1.易于学习：**Python有相对较少的关键字，结构简单，和一个明确定义的语法，学习起来更加简单。


  * **2.易于阅读：**Python代码定义的更清晰。


  * **3.易于维护：**Python的成功在于它的源代码是相当容易维护的。


  * **4.一个广泛的标准库：**Python的最大的优势之一是丰富的库，跨平台的，在UNIX，Windows和Macintosh兼容很好。


  * **5.互动模式：**互动模式的支持，您可以从终端输入并获得结果的语言，互动的测试和调试代码片断。


  * **6.便携式：**Python可以运行在多种硬件平台和所有平台上都具有相同的接口。


  * **7.可扩展：**可以添加低层次的模块到Python解释器。这些模块使程序员可以添加或定制自己的工具，更有效。


  * **8.数据库：**Python提供所有主要的商业数据库的接口。


  * **9.GUI编程：**Python支持GUI可以创建和移植到许多系统调用。


  * **10.可扩展性：**相比 shell 脚本，Python 提供了一个更好的结构，且支持大型程序。


















* * *





# COMMENT
