开篇将介绍一些Python的背景知识，包括什么是Python、Python的起源和它的一些关健 特性。一旦你来了兴致，我们就会向你介绍怎样获得Python以及如何在你的系统上安装并运 行它。本章最后的练习将会帮助你非常自如地使用Python，包括使用交互式解释器以及创建1 并运行脚本程序。

### 1.1什么是Python

Python是一门优雅而健壮的编程语言，它继承了传统编译语言的强大性和通用性，同时也

借鉴了简单脚本和解释语言的易用性。它可以帮你完成工作，而且一段时间以后，你还能看明

白自己写的这段代码。你会对自己如此快地学会它和它强大的功能感到十分的惊讶，更不用提

你已经完成的工作了！只有你想不到，没有Python做不到

### 1.2 起源

贵铎•范•罗萨姆(Guido van Rossum)于1989年底始创了 Python，那时，他还在荷兰 的CWI(Centrum voor Wiskunde en Informatica，国家数学和计算机科学研宄院)。1991年 初，Python发布了第一个公开发行版。这一切宄竟是如何开始的呢？像C、C++、Lisp、Java 和Perl —样，Python来自于某个研宄项目，项目中的那些程序员利用手边现有的工具辛苦的

工作着，他们设想并开发出了更好的解决办法。

那时范•罗萨姆是一位研宄人员，对解释型语言ABC有着丰富的设计经验，这个语言同样 也是在CWI开发的。但是他不满足其有限的开发能力。已经使用并参与开发了像ABC这样的高 级语言后，再退回到C语言显然是不可能的。他所期望的工具有一些是用于完成日常系统管理 任务的，而且它还希望能够访问Amoeba分布式操作系统的系统调用。尽管范•罗萨姆也曾想过 为Amoeba开发专用语言，但是创造一种通用的程序设计语言显然更加明智，于是在1989年末， Python的种子被播下了。

### 1.3 特点

尽管Python已经流行了超过15年，但是一些人仍旧认为相对于通用软件开发产业而言，

它还是个新丁。我们应当谨慎地使用“相对”这个词，因为“网络时代”的程序开发，几年看

上去就像几十年。

当人们询问：“什么是Python?”的时候，很难用任何一个具象来描述它。人们更倾向于 一 口气不加思索地说出他们对Python的所有感觉，Python是___ （请填写）_，这些特点宄竟

又是什么呢？为了让你能知其所以然，我们下面会对这些特点进行逐一地阐释。

### 1.3.1 高级

伴随着每一代编程语言的产生，我们会达到一个新的高度。汇编语言是上帝献给那些挣扎

在机器代码中的人的礼物，后来有了 FORTRAN、C和Pascal语言，它们将计算提升到了崭新 的高度，并且开创了软件开发行业。伴随着C语言诞生了更多的像C++、Java这样的现代编译 语言。我们没有止步于此，于是有了强大的、可以进行系统调用的解释型脚本语言，例如Tcl、 Perl 和 Python。

这些语言都有高级的数据结构，这样就减少了以前“框架”开发需要的时间。像Python中 的列表（大小可变的数组）和字典（哈希表）就是内建于语言本身的。在核心语言中提供这些 重要的构建单元，可以鼓励人们使用它们，缩短开发时间与代码量，产生出可读性更好的代码。

在C语言中，对于混杂数组（Python中的列表）和哈希表（Python中的字典）还没有相

应的标准库，所以它们经常被重复实现，并被复制到每个新项目中去。这个过程混乱而且容易

产生错误。C++使用标准模版库改进了这种情况，但是标准模版库是很难与Python内建的列表 和字典的简洁和易读相提并论的。

### 1.3.2 面向对象

建议：面向对象编程为数据和逻辑相分离的结构化和过程化编程添加了新的活力。面向对 象 编程支持将特定的行为、特性以及和/或功能与它们要处理或所代表的数据结合在一起。

Python的面向对象的特性是与生俱来的。然而，Python绝不想Java或Ruby仅仅是一门面向对 象语言，事实上它融汇了多种编程风格。例如，它甚至借鉴了一些像Lisp和Haskell这样的函

数语言的特性。

### 1.3.3 可升级

大家常常将Python与批处理或Unix系统下的shell相提并论。简单的shell脚本可以用 来处理简单的任务，就算它们可以在长度上（无限度的）增长，但是功能总会有所穷尽。Shell

脚本的代码重用度很低，因此，你只能止步于小项目。实际上，即使一些小项目也可能导致脚

本又臭又长。Python却不是这样，你可以不断地在各个项目中完善你的代码，添加额外的新的 或者现存的Python元素，也可以重用您脑海中的代码。Python提倡简洁的代码设计、高级的 数据结构和模块化的组件，这些特点可以让你在提升项目的范围和规模的同时，确保灵活性、 一致性并缩短必要的调试时间。

“可升级”这个术语最经常用于衡量硬件的负载，通常指为系统添加了新的硬件后带来 的性能提升。我们乐于在这里对这个引述概念加以区分，我们试图用“可升级”来传达一种观 念，这就是：Python提供了基本的开发模块，你可以在它上面开发你的软件，而且当这些需要 扩展和增长时，Python的可插入性和模块化架构则能使你的项目生机盎然和易于管理。

### 1.3.4 可扩展

就算你的项目中有大量的Python代码，你也依旧可以有条不紊地通过将其分离为多个文件 或模块加以组织管理。而且你可以从一个模块中选取代码，而从另一个模块中读取属性。更棒 的是，对于所有模块，Python的访问语法都是相同的。不管这个模块是Python标准库中的还 是你一分钟之前创造的，哪怕是你用其他语言写的扩展都没问题！借助这些特点，你会感觉自

己根据需要“扩展”了这门语言，而且你已经这么做了。

代码中的瓶颈，可能是在性能分析中总排在前面的那些热门或者一些特别强调性能的地方，

可以作为Python扩展用C重写。。需要重申的是，这些接口和纯Python模块的接口是一模 一样的，乃至代码和对象的访问方法也是如出一辙的。唯一不同的是，这些代码为性能带来了 显著的提升。自然，这全部取决你的应用程序以及它对资源的需求情况。很多时候，使用编译

程序设计语言中的这种可扩展性使得工程师能够灵活附加或定制工具，缩短开发周期。虽

然像C、C++乃至Java等主流第三代语言（3GL）都拥有该特性，但是这么容易地使用C编写

扩展确实是 Python 的优势。此外，还有像 PyRex 这样的工具，允许 C 和 Python 混合编程， 使编写扩展更加轻而易举，因为它会把所有的代码都转换成 C 语言代码。

因为Python的标准实现是使用C语言完成的（也就是CPython），所以要使用C和C++ 编写Python扩展。Python的Java实现被称作Jython，要使用Java编写其扩展。最后， 还有IronPython，这是针对.NET或Mono平台的C#实现。你可以使用C#或者VB.Net扩

展 IronPython。

### 1.3.5 可移植性

在各种不同的系统上可以看到Python的身影，这是由于在今天的计算机领域，Python取 得了持续快速的成长。因为Python是用C写的，又由于C的可移植性，使得Python可以运行 在任何带有ANSI C编译器的平台上。尽管有一些针对不同平台开发的特有模块，但是在任何一 个平台上用Python开发的通用软件都可以稍事修改或者原封不动的在其他平台上运行。这种 可移植性既适用于不同的架构，也适用于不同的操作系统。

### 1.3.6易学

Python关键字少、结构简单、语法清晰。这样就使得学习者可以在相对更短的时间内轻松 上手。对初学者而言，可能感觉比较新鲜的东西可能就是Python的面向对象特点了。那些还未 能全部精通00P（0bject Oriented Programming,面向对象的程序设计）的人对径直使用Python 还是有所顾忌的，但是OOP并非必须或者强制的。入门也是很简单的，你可以先稍加涉猎，等 到有所准备之后才开始使用。

### 1.3.7 易读

Python与其他语言显著的差异是，它没有其他语言通常用来访问变量、定义代码块和进行 模式匹配的命令式符号。通常这些符号包括：美元符号（$）、分号（;）、波浪号（~）等等。

没有这些分神的家伙，Python代码变得更加定义清晰和易于阅读。让很多程序员沮丧（或者欣 慰）的是，不像其他语言，Python没有给你多少机会使你能够写出晦涩难懂的代码，而是让其 他人很快就能理解你写的代码，反之亦然。如前所述，一门语言的可读性让它更易于学习。我 们甚至敢冒昧的声称，即使对那些之前连一行 Python 代码都没看过的人来说，那些代码也是 相当容易理解的。看看下一章节——“起步”中的例子，然后告诉我们你的进展是多么神速。

### 1.3.8    易维护

源代码维护是软件开发生命周期的组成部分。只要不被其他软件取代或者被放弃使用，你

的软件通常会保持继续的再开发。这通常可比一个程序员在一家公司的在职时间要长得多了。

Python项目的成功很大程度上要归功于其源代码的易于维护，当然这也要视代码长度和复杂度 而定。然而，得出这个结论并不难，因为Python本身就是易于学习和阅读的。Python另外一

个激动人心的优势就是，当你在阅读自己六个月之前写的脚本程序的时候，不会把自己搞得一

头雾水，也不需要借助参考手册才能读懂自己的软件。

### 1.3.9    健壮性

没有什么能够比允许程序员在错误发生的时候根据出错条件提供处理机制更有效的了。针 对错误，Python提供了 “安全合理”的退出机制，让程序员能掌控局面。一旦你的Python由

于错误崩溃，解释程序就会转出一个“堆栈跟踪”，那里面有可用到的全部信息，包括你程序

崩溃的原因以及是那段代码（文件名、行数、行数调用等等）出错了。这些错误被称为异常。 如果在运行时发生这样的错误，Python使你能够监控这些错误并进行处理。

这些异常处理可以采取相应的措施，例如解决问题、重定向程序流、执行清除或维护步骤、

正常关闭应用程序、亦或干脆忽略掉。无论如何，这都可以有效的缩减开发周期中的调试环节。

Python的健壮性对软件设计师和用户而言都是大有助益的。一旦某些错误处理不当，Python也 还能提供一些信息，作为某个错误结果而产生的堆栈追踪不仅可以描述错误的类型和位置，还 能指出代码所在模块。

### 1.3.10    高效的快速原型开发工具

我们之前已经提到了 Python是多么的易学易读。但是，你或许要问了，BASIC也是如此啊， Python有什么出类拔萃的呢？与那些封闭僵化的语言不同，Python有许多面向其他系统的接口， 它的功能足够强大，本身也足够强壮，所以完全可以使用 Python 开发整个系统的原型。显然， 传统的编译型语言也能实现同样的系统建模，但是Python工程方面的简洁性让我们可以在同样 的时间内游刃有余的完成相同的工作。此外，大家已经为Python开发了为数众多的扩展库，所 以无论你打算开发什么样的应用程序，都可能找到先行的前辈。你所要做的全部事情，就是来 个“即插即用”（当然，也要自行配置一番）！只要你能想得出来，Python模块和包就能帮你 实现。Python标准库是很完备的，如果你在其中找不到所需，那么第三方模块或包就会为你完 成工作提供可能。

### 1.3.11    内存管理器

C或者C++最大的弊病在于内存管理是由开发者负责的。所以哪怕是对于一个很少访问、修 改和管理内存的应用程序，程序员也必须在执行了基本任务之外履行这些职责。这些加诸在开 发者身上的没有必要的负担和责任常常会分散精力。

在Python中，由于内存管理是由Python解释器负责的，所以开发人员就可以从内存事务 中解放出来，全神贯注于最直接的目标，仅仅致力于开发计划中首要的应用程序。这会使错误 更少、程序更健壮、开发周期更短。

### 1.3.12    解释性和（字节）编译性

Python 是一种解释型语言，这意味着开发过程中没有了编译这个环节。一般来说，由于不 是以本地机器码运行，纯粹的解释型语言通常比编译型语言运行的慢。然而，类似于Java,Python 实际上是字节编译的，其结果就是可以生成一种近似机器语言的中间形式。这不仅改善了Python 的性能，还同时使它保持了解释型语言的优点。

#### 核心笔记：文件扩展名

Python源文件通常用.py扩展名。当源文件被解释器加载或者显式地进行字节码编译的时1 候会被编译成字节码。由于调用解释器的方式不同，源文件会被编译成带有.pyc或.pyo扩展

名的文件，你可以在第12章“模块”学到更多的关于扩展名的知识。

### 1.4下载和安装Python

得到所有Python相关软件最直接的方法就是去访问它的网站（http://python.org）。为 了方便读者，你也可以访问本书的网站（http://corepython.com）并点击左侧的“Download Python”链接一一我们在表格中罗列了当前针对大多数平台的Python版本，当然，这还是主要 集中在“三巨头”身上：Unix，Win32和MacOS X。

正如我们在前面1.3.5小节中提到的，Python的可应用平台非常广泛。我们可以将其划分 成如下的几大类和可用平台：

•所有 Unix 衍生系统（Linux，MacOS X，Solaris，FreeBSD 等等）

• Win32 家族（Windows NT，2000，XP 等等）

•早期平台：MacOS 8/9，Windows 3.x，DOS，OS/2，AIX

•掌上平台（掌上电脑/移动电话）：Nokia Series 60/SymbianOS，Windows CE/Pocket

•游戏控制台：Sony PS2，PSP，Nintendo GameCube

•实时平台：VxWorks，QNX

•其他实现版本：Jython，IronPython，stackless

•其他

Python大部分的最近版本都只是针对“三巨头”的。实际上，最新的Linux和MacOS X版 本都已经安装好了 Python——你只需查看一下是哪个版本。尽管其他平台只能找到相对较早的

2.x对应版本，但是就1.5版而言这些版本也有了显著的改进。一些平台有其对应二进制版本， 可以直接安装，另外一些则需要在安装前手工编译。

Unix 衍生系统(Linux，MacOS X，Solaris，FreeBSD 等等)

正如前文所述，基于Unix的系统可能已经安装了 Python。最好的检查方法就是通过命令 行运行Python，查看它是否在搜索路径中而且运行正常。只需输入：

myMac:~ wesley$ python

Python 2.4 (#4, Mar 19 2005, 03:25:10)

[GCC 3.3 20030304 (Apple Computer, Inc. build 1671)] on darwin

Type "help", "copyright", "credits" or "license" for more information.

">>>"

If starting Python fails, it doesn't mean it's not installed, just that it's not in your path. Hunt around for it, and if you're unsuccessful, try building it manually, which isn't very difficult （see “Build It Yourself” on the next page）. If you're using certain versions of Linux, you can get the binary or source RPMs.

### Windows/DOS 系统

首先从前文提到的 python.org 或是 corepython.com 网站下载 msi 文件（例如， python-2.5.msi），之后执行该文件安装Python。如果你打算开发Win32程序，例如使用COM 或MFC，或者需要Win32库，强烈建议下载并安装Python的Windows扩展。之后你就可以 通过DOS命令行窗口或者IDLE和Pythonwin中的一个来运行Python 了，IDLE是Python缺 省的 IDE （Integrated Development Environment，集成开发环境），而 Pythonwin 则来自 Windows 扩展模块。

![img](07Python核心编程_files/07Python38c3160b-35.jpg)



### 自己动手编译Python

对绝大多数其它平台 ， 下载 .tgz 文件， 解压缩这些文件， 然后执行以下操作以编译 Python:

\1.    ./configure

\2.    make

\3.    make install

Python通常被安装在固定的位置，所以你很容易就能找到。如今，在系统上安装多种版本 的Python已经是司空见惯的事情了。虽然容易找到二进制执行文件，你还是要设置好库文件的 安装位置。

![img](07Python核心编程_files/07Python38c3160b-36.jpg)



在Unix中，可执行文件通常会将Python安装到/usr/local/bin子目录下，而库文件则通 常安装在/usr/local/lib/python2.x子目录下，其中的2.x是你正在使用的版本号。MacOS X 系统中，Python则安装在/sw/bin以及/或者/usr/local/bin子目录下。而库文件则在 /sw/lib,/usr/local/lib,以及/或者 /Library/Frameworks/Python.framework/Versions 子

![img](07Python核心编程_files/07Python38c3160b-37.jpg)



目录下。

在Windows中，默认的安装地址是C:\Python2x。请避免将其安装在C:\Program Files 目录下。是的，我们知道这是通常安装程序的文件夹。但是DOS是不支持“Program Files”

这样的长文件名的，它通常会被用“Progra~1”这个别名代替。这有可能给程序运行带来一些 麻烦，所以最好尽量避免。所以，听我的，将Python安装在C:\Python目录下，这样标准库文 件就会被安装在C:\Python\Lib目录下。

### 1.5 运行 Python

有三种不同的办法来启动Python。最简单的方式就是交互式的启动解释器，每次输入一行 Python代码来执行。另外一种启动Python的方法是运行Python脚本。这样会调用相关的脚本 解释器。最后一种办法就是用集成开发环境中的图形用户界面运行Python。集成开发环境通常 整合了其他的工具，例如集成的调试器、文本编辑器，而且支持各种像 CVS 这样的源代码版本 控制工具。

1.5.1 命令行上的交互式解释器

在命令行上启动解释器，你马上就可以开始编写Python代码。在Unix, DOS或其它提供命 令行解释器或shell窗口的系统中，都可以这么做。学习Python的最好方法就是在交互式解



### Unix 衍生系统（Linux,MacOS X,Solaris，FreeBSD 等等）

要访问Python，除非你已经将Python所在路径添加到系统搜索路径之中，否则就必须 输入Python的完整路径名才可以启动PythonQPython —般安装在/usr/bin或/usr/local/bin 子目录中。

我们建议读者把Python（python执行文件，或Jython执行文件--如果你想使用Java版

的解释器的话）添加到你的系统搜索路径之中， 这样你只需要输入解释器的名字就可以启动 Python解释器了，而不必每次都输入完整路径。

要将Python添加到搜索路径中，只需要检查你的登录启动脚本，找到以set path或 PATH= 指令开始，后面跟着一串目录的那行， 然后添加解释器的完整路径。所有事情都做完之 后，更新一下shell路径变量。现在在Unix提示符（根据shell的不同可能是’°%’或’$’） 处键入python（或jython）就可以启动解释器了，如下所示：

$ python

Python启动成功之后，你会看到解释器启动信息，表明Python的版本号及平台信息， 最后显示解释器提示符">>>〃等待你输入Python命令。

### Windoes/DOS 环境

为了把 Python 添加到搜索路径中，你需要编辑 C:\autoexec.bat 文件并将完整的 Python安装路径添加其中。这通常是C:\Python或C:\Program Files \Python （或是 “Program Files” 在 DOS 下的简写名字 C:\Progra~1\Python）

要想在DOS中将Python添加到搜索路径中去，需要编辑C:\autoexec.bat文件，把Python 的安装目录添加上去。一般是C:\Python或C:\Program Files\Python（或者它在DOS中的简写 名字C:\Progra~1\Python）.在一个DOS窗口中（它可以是纯DOS环境或是在Windows中的启动 的一个DOS窗口）启动Python的命令与Unix操作系统是一样的都是“python”：它们唯一 的区别在于提示符不同，DOS中是C:\>如下图所示：

图1-1在一个UNIX（MacOS X）环境中启动Python时的屏幕画面。

![img](07Python核心编程_files/07Python38c3160b-41.jpg)



C:\> python

命令行选项

当从命令行启动Python的时候，可以给解释器一些选项。这里有部分选项可供选择:

-d 提供调试输出

-O 生成优化的字节码（生成 .pyo 文件）

-S 不导入site模块以在启动时查找Python路径 -v 冗余输出（导入语句详细追踪）

-m mod 将一个模块以脚本形式运行

-Q opt 除法选项（参阅文档）

-c cmd 运行以命令行字符串形式提交的 Python 脚本 file 从给定的文件运行Python脚本（参阅后文）

图1 — 2在一个DOS/命令行窗口启动Python

![img](07Python核心编程_files/07Python38c3160b-42.jpg)



![img](07Python核心编程_files/07Python38c3160b-43.jpg)



![img](07Python核心编程_files/07Python38c3160b-44.jpg)



![img](07Python核心编程_files/07Python38c3160b-45.jpg)



![img](07Python核心编程_files/07Python38c3160b-46.jpg)



![img](07Python核心编程_files/07Python38c3160b-47.jpg)



![img](07Python核心编程_files/07Python38c3160b-48.jpg)



Figure 1-2 Starting Python in a DOS/command window



1.5.2 从命令行启动脚本

Unix 衍生系统(Linux，MacOS X，Solaris，FreeBSD 等等)

不管哪种Unix平台，Python脚本都可以象下面这样，在命令行上通过解释器执行:

$ python script.py

Python脚本使用扩展名.py，上面的例子也说明了这一点。Unix平台还可以在不明确指 定Python解释器的情况下，自动执行Python解释器。如果你使用的是类Unix平台，你可以 在你的脚本的第一行使用shell魔术字符串(“sh-bang”)：

\#!/usr/local/bin/python

在#!之后写上Python解释器的完整路径，我们前面曾经提到，Python解释器通常安装 在/usr/local/bin或/usr/bin目录下.如果Python没有安装到那里，你就必须确认你的 Python解释器确实位于你指定的路径。错误的路径将导致出现类似于”找不到命令“的错误信

息

有一个更好的方案，许多Unix系统有一个命令叫env，位于/bin或/usr/bin中。它 会帮你在系统搜索路径中找到 python 解释器。 如果你的系统拥有 env, 你的启动行就可以改 为下面这样：

![img](07Python核心编程_files/07Python38c3160b-50.jpg)



或者， 如果你的 env 位于 /bin 的话，

\#!/bin/env python

当你不能确定Python的具体路径或者Python的路径经常变化时（但不能挪到系统搜索路 径之外）， env 就非常有用。当你在你的脚本首行书写了合适的启动指令之后， 这个脚本就 能够直接执行。当调用脚本时，会先载入Python解释器，然后运行你的脚本。我们刚才提到， 这样就不必显式的调用 Python 解释器了， 而你只需要键入脚本的文件名：

$ script.py

注意， 在键入文件名之前， 必须先将这个文件的属性设置为可以执行。在文件列表中， 你的文件应该将它设置为自己拥有rwx权限。如果在确定Python安装路径，或者改变文件权 限，或使用chmod命令时遇到困难，请和系统管理员一道检查一下。

### Windows/DOS 环境

DOS命令窗口不支持自动执行机制，不过至少在WinXP当中，它能象在Windows中一样 做到通过输入文件名执行脚本：这就是“文件类型”接口。这个接口允许Windows根据文件扩 展名识别文件类型， 从而调用相应的程序来处理这个文件。举例来说， 如果你安装了带有 PythonWin的Python，双击一个带有.py扩展名的Python脚本就会自动调用Python或 PythonWin IDE（如果你安装了的话）来执行你的脚本。运行以下命令就和双击它的效果一样：

C:\> script.py

这样无论是基于 Unix 操作系统还是 Win32 操作系统都可以无需在命令行指定 Python 解释器的情况下运行脚本，但是如果调用脚本时，得到类似“命令无法识别”之类的错误提示 信息，你也总能正确处理。

1.5.3 集成开发环境

你也可以从图形用户界面环境运行Python，你所需要的是支持Python的GUI程序。如 果你已经找到了一个，很有可能它恰好也是集成开发环境。集成开发环境不仅仅是图形接口， 通常会带有源代码编辑器、追踪和排错工具。

### Unix 衍生系统（Linux,MacOS X,Solaris,FreeBSD 等等）

IDLE可以说是Unix平台下Python的第一个集成开发环境（IDE）。最初版本的IDLE也是 贵铎•范•罗萨姆开发的，在Python1.5.2中，它首次露面。IDLE代表的就是IDE，只不过

多了一个“L”。我猜测，IDLE是借用了“蒙提•派森”一个成员的名字[译注1]...嗯......

IDLE基于Tkinter,要运行它的话你的系统中必须先安装Tcl/Tk .目前的Python发行版都带 有一个迷你版的 Tcl/Tk 库， 因此就不再需要 Tcl/Tk 的完整安装了。

如果你已经在系统中安装好了 Python，或者你有一个Python RPM包，可是它并没有包 含IDLE或Tkinter，那在你尝试IDLE之前，必须先将这两样东西安装好。（如果你需要， 确实有一个独立的Tkinter RPM包可以供你下载，以便和Python 一起工作）如果你是自己编

译的Python,而且有Tk库可用，那Tkinter会自动编译进Python,而且Tkinter和IDLE 也会随Python的安装而安装。

如果你打算运行 IDLE,就必须找到你的标准库安装位置： /usr/local/lib/python2.x/idlelib/idle.py. 如果你是自己编译 Python, 你会在 /usr/local/bin目录中发现一个名为idle的脚本，这样你就可以在shell命令行中直接运行 idle.图1 — 3是类Unix系统下的IDLE界面。MacOS X是一个非常类似Unix （基于mach内核， BSD服务）的操作系统。在MacOS X下，Python可以用传统的Unix编译工具编译。MacOS X 发行版自带一个编译好的Python解释器，不过并没有任何一个面向Mac的特殊工具。（比如 GNU readline, IDE 等等）。当然也没有 Tkinter 和 IDLE。

你可能会打算自己下载并编译一个出来，不过要小心一点，有时你新安装的Python会与 Apple预装的版本混淆在一起。认真一点没有坏处。你也可以通过Fink/Finkcommander和 DarwinPorts 得到 MacOS X 版的 Python：

<http://fink.sourceforge.net/>

<http://darwinports.org>

图1-3在Unix中启动IDLE



![img](07Python核心编程_files/07Python38c3160b-56.jpg)



![img](07Python核心编程_files/07Python38c3160b-57.jpg)



![img](07Python核心编程_files/07Python38c3160b-58.jpg)



另一个选择是从Python站点下载MacOS X的通用二进制包。这个磁盘映像文件(DMG)要求 操作系统版本至少为10.3.9，它适用于基于PowerPC和Intel硬件的Mac机器。

### Windows 环境

PythonWin 是 Python 的第一个 Windows 接口，并且还是个带有图形用户界面的集成开发 环境。PythonWin发行版本中包含Windows API和COM扩展。PythonWin本身是针对MFC库编写 的，它可以作为开发环境来开发你自己的Windows应用程序。你可以从下面给出的网页中下载 并安装它。

PythonWin通常被安装在和Python相同的目录中，在它自己的安装目录 C:\Python2x\Lib\site-packages\pythonwin 中有可执行的启动文件 pythonwin. exe 。 PythonWin拥有一个带有颜色显示的编辑器、一个新的增强版排错器、交互shell窗口、COM扩



![img](07Python核心编程_files/07Python38c3160b-60.jpg)



![img](07Python核心编程_files/07Python38c3160b-61.jpg)



File Edit View Tools Wndow Help

PythonWin 1.6a2 (#O,Apr 6 2000, 11:45:12) [MSC 32 bit (Intel)] on Win32 Copyright 1991-1995 Stichting Mathematisch Centrum, Amsterdam Portions Copyright 1994-2000 Mark Hammond ([MHammond@skippinet.com.au](mailto:MHammond@skippinet.com.au))

;»>

usr/bin/env python

shcui-Fcrn? if

showForm()

presents blank or data-filled fo]|

00001 |001

PythonWin

其 ®la

l鉍I

□ &

-ini x

Interactive WindoiM

兮|friends.py

-|a|x|

urajort. ccfi

iroin urllib xnmort quote plus

from string rn^ort capwords

clef snowForm who, ho職any):

for i in [U,    1U,    5U, 1UU]:



![img](07Python核心编程_files/07Python38c3160b-63.jpg)



图1-4 Windows环境中的PythonWin

你可以在下面由马克•哈蒙德(Mark Hammond)维护的网站中找到更多的关于PythonWin 和Python针对Windowns的扩展(也被称作“win32all”)：

<http://starship.python.net/crew/mhammond/win32/>

<http://sourceforge.net/projects/pywin32/>

<http://starship.python.net/crew/mhammond/win32/>

<http://sourceforge.net/projects/pywin32/>

IDLE也有Windows平台版本，这是由Tcl/Tk和Python/ Tkinter的跨平台性特点决定的， 它看上去很像Unix平台下的版本，如图1-5所示。

![img](07Python核心编程_files/07Python38c3160b-64.jpg)



在Windows平台下，IDLE可以在Python编译器通常所在的目录C:\Python2x下的子目录 Lib\idlelib中找到。从DOS命令行窗口中启动IDLE，请调用idle.py。你也可以从Windows 环境中调用idle.py，但是会启动一个不必要的DOS窗口。取而代之的方法是双击idle.pyw， 以.pyw作为扩展名的文件不会通过打开DOS命令行窗口来运行脚本。事实上你可以在桌面上创 建一个到C:\Python2x\Lib\idlelib\idle.pyw的快捷方式，然后双击启动就可以了，简单吧!

Figure 1-5 Starting IDLE in Windows

### 1.5.4 其它的集成开发环境和执行环境

很多的软件开发专家事实上会选择在他们喜欢的文本编辑器中编写代码，比如vi(m)或者 emacs。除了这些和上面提到到的集成开发环境，还有大量的开源和商业的集成开发环境，下面 是个简短的列表：

#### 开源

• IDLE (在Python发行版中自带)

![img](07Python38c3160b-68.jpg)



•    PythonWin + Win32 Extensions <http://starship.python.net/crew/skippy/win32>

•    IPython （增强的交互式Python） <http://ipython.scipy.org>

•    IDE Studio （IDLE 以及更多） <http://starship.python.net/crew/mike/Idle>

•    Eclipse <http://pydev.sf.net>

<http://eclipse.org/>

#### 商业

• WingWare开发的WingIDE Python集成开发环境 <http://wingware.com/>

![img](07Python38c3160b-69.jpg)



• ActiveState 开 发 的 Komodo <http://activestate.com/Products/Komodo>



集成开发环境



![img](07Python38c3160b-70.jpg)



#### 通用集成开发环境列表



<http://wiki.python.org/moin/IntegratedDevelopmentEnvironments>



核心提示：运行本书中的代码实例

在本书中，你会发现很多的Python脚本样例，可以从本书的网站上下载。但是当你运行它 们的时候，请记住这些代码是投计用来从命令行(DOS命令行窗口或Unix shell)或者集成开 发环境执行的。如果你在使用Win32系统，双击Python程序会打开DOS窗口，但是在脚本执行 完毕后就会关闭，所以你可能看不到输出结果。如果你遇到了这种情况，就直接打开DOS窗口， 从命令行中运行相关的脚本，或者在集成开发环境中执行脚本。另外一种办法，就是在脚本的 最后一行后面添加raw_input()语句，这样就可以保持窗口开着，直到你按下回车键才关闭。



### 1.6 Python 文档



![img](07Python38c3160b-71.jpg)



Python文档可以在很多地方找到。最便捷的方式就是从Python网站查看在线文档。如果 你没上网，并且使用的是Win32系统，那么在C:\Python2x\Doc\目录下会找到一个名为 Python2x.chm的离线帮助文档。它使用IE接口，所以你实际上是使用网页浏览器来查看文档。 其他的离线文档包括PDF和PostScript （PS）文件。最后，如果你下载了 Python发行版，你会 得到 LaTeX 格式的源文件。

在本书的网站中，我们创建了一个包括绝大多数Python版本的文档，只要访问 http://corepython.com，单击左侧的 “Documentation” 就可以了。

### 1.7比较Python（Python与其他语言的比较）

Python已经和很多语言比较过了。一个原因就是Python提供了很多其他语言拥有的特性。 另外一个原因就是Python本身也是由诸多其他语言发展而来的，这包括ABC、Modula-3、C、 C++、Algol-68、SmallTalk、Unix shell和其他的脚本语言等等。Python就是”浓缩的精华

“：范•罗萨姆研宄过很多语言，从中吸收了许多觉得不错的特性，并将它们溶于一炉。

然而，往往因为Python是一门解释型语言，你会发现大多数的比较是在Perl、Java、Tcl， 还有JavaScript之间进行的。Perl是另外一种脚本语言，远远超越了标准的shell脚本。像 Python—样，Perl赋予了你所有编程语言的功能特性，还有系统调用能力。

Perl最大的优势在于它的字符串模式匹配能力，其提供了一个十分强大的正则表达式匹配 引擎。这使得Perl实际上成为了一种用于过滤、识别和抽取字符串文本的语言，而且它一直是 开发Web服务器端CGI（common gateway interface,通用网关接口）网络程序的最流行的语言。 Python的正则表达式引擎很大程度上是基于Perl的。

然而，Perl语言的晦涩和对符号语法的过度使用，让解读变得很困难。这些语法令初学者 不得精要，为他们的学习带来了不小的阻碍。Perl的这些额外的“特色”使得完成同一个任务 会有多个方法，进而引起了开发者之间的分歧和内讧。最后，通常当你想阅读几个月前写的Perl 脚本的时候都不得不求助参考书。

Python也经常被拿来和Java作对比，因为他们都有类似的面向对象的特性和语法。Java 的语法尽管比C++简单的多，但是依旧有些繁琐，尤其是当你想完成一个小任务的时候。Python 的简洁比纯粹的使用Java提供了更加快速的开发环境。在Python和Java的关系上，一个非常 重大的革命就是Jython的开发。Jython是一个完全用Java开发的Python解释器，现在可以

在只有Java虚拟机的环境中运行Python程序。我们会在后面的章节中简单讲述Jython的更多 优点，但是现在就可以告诉你：在Jython的脚本环境中，你可以熟练地处理Java对象，Java 可以和Python对象进行交互，你可以访问自己的Java标准类库，就如同Java —直是Python 环境的一部分一样。

现在，由于Rails项目的流行，Python也经常被拿来和Ruby进行比较。就像前面我们提 到的，Python是多种编程范式的混合，它不像Ruby那样完全的面向对象，也没有像Smalltalk 那样的块，而这正是Ruby最引人注目的特性。Python有一个字节码解释器，而Ruby没有。Python 更加易读，而Ruby事实上可以看作是面向对象的Perl。相对于Rails，Python有几个自己的 Web应用框架，比如Django和Turbogears这两个项目。

Tcl是另一种可以与Python相提并论的脚本语言。Tcl是最易于使用的脚本语言之一，程 序员很容易像访问系统调用一样对Tcl语言进行扩展。Tcl直到今天仍然很流行，与Python 相比，它或许有更多局限性(主要是因为它有限的几种数据类型)，不过它也拥有和Python 一样的通过扩展超越其原始设计的能力。更重要的是，Tcl通常总是和它的图形工具包Tk 一 起工作，一起协同开发图形用户界面应用程序。因为它非常流行，所以Tk已经被移植到 _ Perl(Perl/Tk)和Python(Tkinter)中.同样有一个有争议的观点，那就是与Tcl相比，因为

Python有类，模块及包的机制，所以写起大程序来更加得心应手。

Python有一点点函数化编程(functional programming ，FP)结构，这使得它有点类似 List或Scheme语言。尽管Python不是传统的函数化编程语言，但它持续的从Lisp和haskell 语言中借用一些有价值的特性。举例来说，列表解析就是一个广受欢迎的来自Haskell世界的 特性，而Lisp程序员在遇到lambda, map, filter和reduce时也会感到异常亲切。

JavaScript是另外一种非常类似Python的面向对象脚本语言。优秀的JavaScript程序员 学起Python来易如反掌。聪慧的读者会注意到JavaScript是基于原型系统的，而Python则 遵循传统的面向对象系统， 这使得二者的类和对象有一些差异。

下面列出了有关Python与其它语言进行比较的网页：

#### Perl

<http://www2.linuxjournal.com/article/3882>

<http://llama.med.harvard.edu/~fgibbons/PerlPythonPhrasebook.html>

![img](07Python38c3160b-76.jpg)



<http://aplawrence.com/Unixart/pythonvsperl.html>

<http://pleac.sf.net/pleac_python>

<http://www.garshol.priv.no/download/text/perl.html>

#### Java

<http://dirtsimple.org/2004/12/python-is-not-java.html>

<http://twistedmatrix.com/users/glyph/rant/python-vs-java.html>

<http://netpub.cstudies.ubc.ca/oleary/python/python_java_comparison.php>

#### Lisp

<http://strout.net/python/pythonvslisp.htmlhttp://norvig.com/python-lisp.html>

#### Ruby

<http://blog.ianbicking.org/ruby-python-power.html>

<http://www.rexx.com/~oinkoink/Ruby_v_Python.html>

<http://dev.rubycentral.com/faq/rubyfaq-2.html>

#### Perl, C++

<http://strombergers.com/python/>

#### Perl, Java, C++

<http://furryland.org/~mikec/bench/>

#### C++, Java, Ruby

<http://dmh2000.com/cjpr>

#### Perl, Java, PHP, Tcl

<http://www-128.ibm.com/developerworks/linux/library/l-python101.html>

<http://www-128.ibm.com/developerworks/linux/library/l-script-survey/>

C, C++, Java, Perl, Rexx, Tcl

你可以在下面的网址中看到更多Python与其他的语言的比较： <http://www.python.org/doc/Comparisons.html>

### 1.8 其它实现

标准版本的Python是用C来编译的，又叫CPython.除此之外，还有一些其它的Python 实现。我们将在下面讲述些实现， 除了本书中提到的这些实现以外， 下面的网址还有更多的 实现版本：

<http://python.org/dev/implementations.html>

#### Java

我们在上一节中曾经提到，还有一个可以用的Python解释器是完全由Java写成的，名 为Jython。    尽管两种解释器之间存在一些细微的差别，但是它们非常接近，而且启动环

一境也完全相同。那Jython又有哪些优势呢？ Jython...

•只要有Java虚拟机，就能运行Jython

•拥有访问Java包与类库的能力

•为Java开发环境提供了脚本引擎

•能够很容易的测试Java类库

•提供访问Java原生异常处理的能力

•继承了 JavaBeans特性和内省能力

•鼓励Python到Java的开发（反之亦然）

•⑶I开发人员可以访问Java的AWT/Swing库 •利用了 Java原生垃圾收集器（CPython未实现此功能）

对Jython进行详细论述，超出了本文的范围。不过网上有非常多的Jython信息。Jython 目前仍然在不断开发之中，不时会增加新的特性。你可以通过访问Jython的网站得到更多有 用的信息：

#### .NET/Mono

现在已经有一个名为IronPython的Python实现.它是用C#语言完成的.它适用的环 境是 .NET 和 Mono. 你可以在一个 .NET 应用程序中整合 IronPython 解释器来访问 .NET 对象.IronPython的扩展可以用C#或VB.NET语言编写.除此之外，还有一种名为Boo 的 .NET/Mono 语言. 你可以在下面的网址获得更多关于 IronPython 和 Boo 语言的信息.

<http://codeplex.com/Wiki/View.aspx?ProjectName=IronPython>

<http://boo.codehaus.org/>

#### Stackless

CPython的一个局限就是每个Python函数调用都会产生一个C函数调用.(从计算机科学的角度来说，我 们在讨论栈帧).这意味着同时产生的函数调用是有限制的，因此Python难以实现用户级的线程库和复杂递 归应用.一旦超越这个限制，程序就会崩溃.你可以通过使用一个“stackless”的Python实现来突破 这个限制，一个C栈帧可以拥有任意数量的Python栈帧.这样你就能够拥有几乎无穷的函数调用，并能

支持巨大数量的线程.这个Python实现的名字就叫…….Stackless(嘿嘿，很惊讶吗？!)

Stackless唯一的问题就是它要对现有的CPython解释器做重大修改.所以它几乎是一 个独立的分支.另一个名为Greenlets的项目也支持微线程，它是一个标准的C扩展，因此

不需要对标准Python解释器做任何修改.通过以下网址你能了解更多信息：

<http://stackless.com> <http://codespeak.net/py/current/doc/greenlet.html>

1.9 练习

1-1. 安装Python。请检查Python是否已经安装到你的系统上，如果没有，请下载并 安装它！

1-2. 执行Python。有多少种运行Python的不同方法？你喜欢哪一种？为什么？

1-3. Python 标准库。

(a)    请找到系统中Python执行程序的安装位置和标准库模块的安装位置

(b)    看看标准库里的一些文件，比如string.py。这会帮助你适应阅读Python脚本。

1-4.    I交互执行。启动你的Python交互解释器。你可以通过输入完整的路径名来启动

它。当然，如果你已经在搜索路径中设置了它的位置，那么只输入它的名字(python或者 python.exe)就行了。(你可以任选最适合你的的Python实现方式，例如：命令行、图形用户

接口/集成开发环境、Jython、IronPython或者Stackless）启动界面看上去就像本章描述的一 样，一旦你看到＞＞＞提示符，就意味着解释器准备好要接受你的Python命令了。



试着输入命令print 'Hello World!'（然后按回车键），完成著名的Hello World!程序， 然后退出解释器。在Unix系统中，按下Ctrl+D会发送EOF信号来中止Python解释器，在DOS 系统中，使用的组合键是Ctrl+Z。如果要从Macintosh、PythonWin、以及Windows或Unix中 的IDLE这样的图形用户环境中退出，只要简单的关闭相关窗口就可以了。

1-5. 编写脚本。作为练习1-4的延续，创建“Hello World!”的Python脚本其实和 上面的交互性练习并不是一回事。如果你在使用Unix系统，尝试建立自动运行代码行，这样你 就可以在没有调用Pyton解释器的情况下运行程序了。

1-6.    编写脚本。使用print语句编写脚本在屏幕上显示你名字、年龄、最喜欢的颜色

和与你相关的一些事情（背景、兴趣、爱好等等）。

译注1:蒙提•派森:Monty Python,也称“蒙地蟒蛇”。是英国的一^个六人喜剧团 体，其七十年代的电视剧和八十年代的电影作品红极一时。贵铎•范•罗萨姆就是该团体的忠

![img](07Python38c3160b-83.jpg)



—实影剧迷,故而将本语言命名为Python。这里的IDLE指的是其成员艾瑞克•艾多（Eric Idle ）

![img](07Python38c3160b-84.jpg)



ss>sq>Ag*ipUJ



![img](07Python38c3160b-86.jpg)



![img](07Python38c3160b-87.jpg)



囊•

邾•

籁茵*

聪>*

e><*

\#蛾邶篡•

银擬*

割邾 uosAd •

靼e<*

難劫*

霧/Y渥•

•

蹯州餅传



Ss>sq>>g*;p3
