### UNIX标准及实3

###### 2.1引言

人们在UNIX编程环境和C程序设计语言的标准化方面已经做了很多工作。虽然UNIX应用 程序在不同的UNIX操作系统版本之间进行移植相当容易，但是20世纪80年代UNDC版本种类 的剧增以及它们之间差别的扩大，导致很多大用户(如美国政府)呼吁对其进行标准化。

本章首先回顾过去近25年人们在UNIX标准化方面做出的种种努力，然后讨论这些UNIX 编程标准对本书所列举的各种UNIX操作系统实现的影响。所有标准化工作的一个重要部分是对 每种实现必须定义的各种限制进行说明，所以我们将说明这些限制以及确定它们值的各种方法。

##### 2.2 UNIX标准化

2.2.1 ISOC

1989年下半年，C程序设计语言的ANSI标准X3.159-1989得到批准。此标准被也采纳为国 际标谁 ISO/IEC 9899:1990。ANSI 是美国国家标准学会(American National Standards Institute)的 缩写，它是国际标准化组织(International Organization for Standardization，ISO)中代表美国的成 员。IEC 是国际电子技术委员会(International Electrotechnical Commission)的缩写。

ISOC标准现在由ISO/IEC的C程序设计语言国际标准工作组维护和开发，该工作组称为ISO/ IEC JTC1/SC22/WG14,简称WG14。ISO C标准的意图是提供C程序的可移植性，使其能适合于 大量不同的操作系统，而不只是适合UNIX系统。此标准不仅定义了 C程序设计语言的语法和语 义，还定义了其标淮库(参见ISO 1999第7章；Plauger[1992]i Kemighan和Ritchie[1988]中的附 录B)。因为所有现今的UNIX系统(如本书介绍的几个UNIX系统)都提供C标准中定义的库 函数，所以该标准库非常重要。

1999年，ISOC标准被更新，并被批准为1SO/IEC9899:1999,它显著改善了对进行数值处理 的应用软件的支持。除了对某些函数原型增加了关键字restrict外，这种改变并不影响本书中 描述的POSIX接口。restrict关键字告诉编译器，哪些指针引用是可以优化的，其方法是指出 指针引用的对象在函数中只通过该指针进行访问。

1999年以来，己经公布了 3个技术勘误来修正ISOC标准中的错误，分别在2001年、2004 年和2007年公布。如同大多数标准一样，在批准标准和修改软件使其符合标准两者之间有一段 时间延迟。随着供应商编译系统的不断演化，对最新ISOC标准的支持也就越来越多。

' gcc对ISO C标准1999版本符合程度的总结可参见http: //www.gnu.org/c99status .

1 html,虽然C标准已经在2011年更新，但由于其他标准还没有进行相应的更新，因此在本书中

我们还是沿用1999年的版本。

按照该标准定义的各个头文件（见图2-1）可将ISO C库好成24个区。POSIX.I标准包括选些 头文件以及另外一些头文件。从图2-1中可以看出，所有送些头文件在4种UNIX实现（FreeBSD 8.0、 Linux 3.2.0、Mac OS X 10.6.8和Solaris 10）中都支持。本章后面将对这4种UNIX实现进行说明。

ISO C头文件依赖于操作系统所配置的C编译器的版本。FreeBSD 8.0配置了 gcc 4.2.1版， Solaris 10 配置了 gcc 3.4.3 版（以及 Sun Studio 自带的 C 编译器），Ubuntu 12.04（ Linux 3.2.0）配

:置了 gcc4.6.3 版，MacOSX 10.6.8 配置了 gcc4.0.1 和4.2.1 版。

| 头文件       | FreeBSD 8.0 | Linux 3.2.0 | Mac OS XI 0.6.8 | Solaris 10 | 说明                     |
| ------------ | ----------- | ----------- | --------------- | ---------- | ------------------------ |
|              |             |             |                 |            |                          |
| <assert.h>   |             |             |                 |            | 验证程序断言             |
| <cotnplex.h> |             |             |                 |            | 复数算术运算支持         |
| <ctype.h>    |             |             |                 |            | 字符分类和映射支持       |
| <errno.h>    |             |             |                 |            | 出错码（1.7节）          |
| <fenv.h>     |             |             |                 |            | 浮点环境                 |
| <float.h>    |             |             |                 |            | 浮点常量及特性           |
| <inttypes.h> |             |             |                 |            | 整型格式变换             |
| <iso646.h>   |             |             |                 |            | 喊值、关系及一元操作符宏 |
| <limits.h>   |             |             |                 |            | 实现常量（2.5节）        |
| <locale.h>   |             |             |                 |            | 本地化类别及相关定义     |
| <math.h>     |             |             |                 |            | 数学函数、类型声明及常量 |
| <setjmp.h>   |             |             |                 |            | 非局部goto （7.10节）    |
| <signal.h>   |             |             |                 |            | 信号（第10章）           |
| <stdarg.h>   |             |             |                 |            | 可变长度参数表           |
| <stdbool.h>  |             |             |                 |            | 布尔类型和值             |
| <stddef.h>   |             |             |                 |            | 标准定义                 |
| <stdint.h>   |             |             |                 |            | 整型                     |
| <stdio.h>    |             |             |                 |            | 标准I/O库（第5章）       |
| <stdlib.h>   |             |             |                 |            | 实用函数                 |
| <string.h>   |             |             |                 |            | 字狩串操作               |
| <tgmath.h>   |             |             |                 |            | 通用类型数学宏           |
| <time.h>     |             |             |                 |            | 吋间和日期（6.10节）     |
| <wchar.h>    |             |             |                 |            | 扩充的多字节和宽字狩支持 |
| <wctype.h>   |             |             |                 |            | 宽字符分类和映射支持     |

ISO C标准定义的头文件

2.2.2 IEEE POSIX

POSIX 是一个最初由 IEEE （Institute of Electrical and Electronics Engineers，电气和电子工程 师学会）制订的标准族。POSIX指的是可棲植操作系统接口（Portable Operating System Interface）= 它原来指的只是IEEE标准1⑻3.1-1988 （操作系统接口），后来则扩展成包括很多标记为1003的 标准及标准草案，如shell和实用程序（1003.2）。

与本书相关的是1003.1操作系统接口标准，诙标准的目的是提升应用程序在各种UNIX系统 环境之间的可移植性。它定义了 “符合POSIX的”（POSIX compliant）操作系统必须提供的各

种服务，该标准己被很多计算机制造商采用。虽然1003.1标准是以UNIX操作系统为基础的，但 是它并不限于UNIX和UNIX类的系统。确实，有些提供专有操作系统的制造商也声称他们的系 统符合POSDC （同时还保留所有专有功能）。

由于1003.1标淮说明了一■个接口（interface）而不是一种实现（implementation），所以并不 区分系统调用和库函数。所有在标准中的例程都被称为函数。

标准是不断演进的，1003.1标淮也不例外。该标准的1988版，即IEEE标准1003.1-1988经 修改后递交给ISO,它没有增加新的接口或功能，但修订了文本。最终的文档作为IEEE标淮 1003.1-1990正式出版[IEEE 1990],这也就是国际标准ISO/IEC 9945-1:1990。该标准通常称为 POSIX.1,本书将使用此术语来表示不同版本的标准。

26

I

27



IEEE 1003.1工作组此后继续对这一标准做了更多修改。1996年，该标准的修订版发布，它 包括了 1003.1-1990、I003.1b-1993实时扩展标准以及被称为pthreads的多线程编程接口 （POSIX 线程），这就是国际标准ISO/IEC 9945-1:1996。1999年出版了 IEEE标准1003.1d-1999,其中增加 了更多实时接口。一■年后，出版了 IEEE标准1003.1j-2000和I003.1q-2000,前者包含了更多实时 接口，后者增加了标准在事件跟踪方面的扩展，

2001年的1003.1版本与以前各版本有较大的差别，它组合了多个1003.1的修正、1003.2标 准以及Single UNIX Specificaiton （SUS）第2版的若干部分（对于SUS,后面将进行更多说明）， 这形成了 IEEE标准1003.1-2001,它包括下列儿个标准

•    ISO/IEC 9945-1 （IEEE 标准 1003.1-1996）,包括

♦    IEEE 标准 1003.1-1990

♦    IEEE 标准 1003.1b-1993 （实时扩展）

♦    IEEE 标准 1003.1C-1995 （pthreads）

♦    IEEE标准1003.H-1995 （实时技术勘误表）

•    IEEEP1003.1a草案（系统接口修正）

•    IEEE标准1003.1d-I999 （髙级实时扩展）

•    IEEE标准1003.1j-2000 （更多髙级实时扩展）

•    IEEE 标淮 1003.1q-2000 （跟踪）

•部分IEEE标准1003.1g-2000 （协议无关接口）

•    ISO/IEC 9945-2 （IEEE 标准 1003.2-1993）

•    [EEEP1003.2b草案（shell及实用程序的修正）

•    IEEE标准1003.2d-1994 （批处理扩展）

•    Single UNIX Specification第2版基本说明，包括 ♦系统接口定义，第5发行版

♦命令和实用程序，第5发行版 ♦系统接口和头文件，第5发行版

•开放组技术标准，网络服务，5.2发行版

•    ISO/IEC 9899-1999, C 程序设计语言

2004年，POSIX.1说明随着技术勘误得到更新，2008年做了更多综合的改动并作为基本说明 的第7发行版发布，ISO在2008年底批准了这个版本并在2009年进行发布，即国际标准 ISO/IEC9945:2009。垓标准基于其他几个标准。

•    IEEE 标准 1003.1, 2004 年版。

•开放组织技术标准，2006,扩展API集，第1〜4部分。

• ISO/IEC 9899:1999,包含勘误表。

图2-2、图2-3以及图24总结了 POSIX.1指定的必需的和可选的头文件。因为POSIX.1包 含了 ISOC标准库函数，所以它还需要图2-1中列出的各个头文件。这4张图中的表也总结了本 书所讨论的4种UNIX系统实现所包含的头文件。

| 头文件          | FreeBSD8.0 | Linux3.2.0 | Mac OS X 10.6.8 | Solaris 10 | 说明                       |
| --------------- | ---------- | ---------- | --------------- | ---------- | -------------------------- |
| <aio.h>         |            |            |                 |            | 异步I/O                    |
| <cpio.h>        |            |            |                 |            | cpio归档值                 |
| <dirent.h>      |            |            |                 |            | 目录项（4.22节）           |
| <dlfcn.h>       |            |            |                 |            | 动态链接                   |
| <fcntl.h>       |            |            |                 |            | 文件控制（3.14节）         |
| <fnmatch.h>     |            |            |                 |            | 文件名匹配类型             |
| <glob.h>        |            |            |                 |            | 路径名摸式匹配与生成       |
| <grp.h>         |            |            |                 |            | 组文件（6.4节）            |
| <iconv.h>       |            |            |                 |            | 代码集变换实用程序         |
| <langinfo.h>    |            |            |                 |            | 语言信息常量               |
| <monetary.h>    |            |            |                 |            | 货币类型与函数             |
| <netdb.h>       |            |            |                 |            | 网络数据库操作             |
| <nl_types.h>    |            |            |                 |            | 消息类                     |
| <poll.h>        |            |            |                 |            | 投票函数（14.4+2节）       |
| <pthread.h>     |            |            |                 |            | 线程（第11章、第12章）     |
| <pwd.h>         |            |            |                 |            | 口令文件（6.2节）          |
| <regex.h>       |            |            |                 |            | 正则表达式                 |
| <sched.h>       |            |            |                 |            | 执行调度                   |
| 〈semaphore.h>  |            |            |                 |            | 信号量                     |
| Otrings. h>     |            |            |                 |            | 字符串操作                 |
| <tar.h>         |            |            |                 |            | tar归档值                  |
| <termios.h>     |            |            |                 |            | 终端1/0 （第18章）         |
| <unistd.h>      |            |            |                 |            | 符号常量                   |
| <wordexp.h>     |            |            |                 |            | 字扩充类型                 |
| <arpa/inet.h>   |            |            |                 |            | 因特网定义（第16章）       |
| <net/if.h>      |            |            |                 |            | 套接字本地接口（第16章）   |
| <netinet/in.h>  |            |            |                 |            | 因特网地址族（16.3节）     |
| <netinet/tcp.h> |            |            |                 |            | 传输控制协议定义           |
| <sys/mman.h>    |            |            |                 |            | 存储管理声明               |
| <sys/select.h>  |            |            |                 |            | select 函数（14.4.1 节）   |
| <sys/socket.h>  |            |            |                 |            | 套接字接口（第16章）       |
| <sys/stat.h>    |            |            |                 |            | 文件状态（第4章）          |
| <sys/statvfs.h> |            |            |                 |            | 文件系统信息               |
| <sys/times.h>   |            |            |                 |            | 进程时间（8.17节）         |
| <sys/types-h>   |            |            |                 |            | 基本系统数据类型（2.8节）  |
| <sys/un.h>      |            |            |                 |            | UNIX域套接字定义（17.2节） |
| <sys/utsname.h> |            |            |                 |            | 系统名（6.9节）            |
| <sys/wait.h>    |            |            |                 |            | 进程控制（8.6节）          |

图2-2 POSIX标准定义的必需的头文件

本书中描述了 POSIX.1 2008年版，其接口分成两部分：必需部分和可选部分。可选接口部分

按功能又进一步分成40个功能分区。图2-5按各自的选项码总结了包含未弃用的编程接口。选项 码是能够表述标准的2〜3个字母的缩写，用以标识属于各个功能分区的接口，其中的接口依赖 于特定选项的支持。很多选项处理实时扩展。

| 头文件           | FreeBSD8.0 | Linux3.2.0 | Mac OSX 10.6.8 | Solaris 10 | 说明                       |
| ---------------- | ---------- | ---------- | -------------- | ---------- | -------------------------- |
| <fmtmsg.h>       |            |            |                |            | 消息显示结构               |
| <ftw.h>          |            |            |                |            | 文件树漫游（4.22节）       |
| <libgen.h>       |            |            |                |            | 路径名管理函数             |
| <ndbm.h>         |            |            |                |            | 数据库操作                 |
| <search.h>       |            |            |                |            | 搜索表                     |
| <syslog.h>       |            |            |                |            | 系统出错曰志记录（13.4节） |
| <utmpx.h>        |            |            |                |            | 用户账户数据库             |
| <sys/ipc.h>      |            |            |                |            | IPC （15.6 节）            |
| <sys/msg.h>      |            |            |                |            | XSI消息队列（15.7节）      |
| <sys/resource.h> |            |            |                |            | 资源操作（7.11节）         |
| <sys/sem.h>      |            |            |                |            | XSI信号量（15.8节）        |
| <sys/shm.h>      |            |            |                |            | XSI共享存储（15.9节）      |
| <sys/time.h>     |            |            |                |            | 时间类型                   |
| <sys/uio.h>      |            |            |                |            | 矢量I/O操作（14.6节）      |

图2-3 POSIX标准定义的XS1可选头文件

| 头文件     | FreeBSD8,0 | Linux3.2.0 | Mac OSX 10.6.8 | Solaris 10 | 说明          |
| ---------- | ---------- | ---------- | -------------- | ---------- | ------------- |
| <mqueue.h> | •          |            |                |            | 消息队列      |
| <spawn.h>  | •          |            | •              | •          | 实时spawn接口 |

图24 POS1X标准定义的可选头文件

| 选项码 | SUS强制的 | 符号常量                          | 说明                           |
| ------ | --------- | --------------------------------- | ------------------------------ |
| ADV    |           | _POSIX_ADVISORY_INFO              | 建议性信息（实时）             |
| CPT    |           | _POSIX_CPUTIME                    | 进程CHJ时间时钟（实时）        |
| FSC    | •         | _POSIX_FSYNC                      | 文件同步                       |
| IP6    |           | _POSIX_IPV6                       | IPv6 接口                      |
| ML     |           | _POSIX_MEMLOCK                    | 进程存储区加锁（实时）         |
| MLR    |           | _POSIX_MEMLOCK_RANGE              | 存倚区域加锁（实时）           |
| MON    |           | _POSIX_MONOTONIC_CLOCK            | 笨调时钟（实时）               |
| MSG    |           | _POSIX_MESSAGE_PASSING            | 消息传送（实吋）               |
| MX     |           | _ _STDC_IEC_559_ _                | IEC 60559浮点选项              |
| PIO    |           | _POSIX_PRIORITIZED_IO             | 优先输入和输出                 |
| PS     |           | _POSIX_PRXORITIZED_SCHEDULING     | 进程调度（实吋）               |
| RPI    |           | _POSIX_THREAD_ROBUST„PRIO_INHERIT | 健壮的互斥量优先权继承（实时） |
| RPP    |           | _POSIX_THREAD_ROBUST_PRIO_PROTECT | 健壮的互斥量优先权保护（实时） |
| RS     |           | _POSIX_RAW_SOCKETS                | 原始套接宇                     |
| SHM    |           | _POSIX_SHARED_MEMORY_OBJECTS      | 共享存储对象（实时〉           |
| SIO    |           | _POSIX_SYNCHRONIZED_IO            | 同步输入和输出（实时）         |
| SPN    |           | _POSIX_SPAWN                      | 产生（实时）                   |
| ss     |           | „POSIX_SPORADIC_SERVER            | 进程阵发性服务器C实时〉        |
| TCT    |           | POSIX THREAD CPUTIME              | 线程CPU时间时钟（实时）        |

| TPI  |      | _POSIX_THREAD_PRIO_INHERIT        | 非键壮的互斥量优先权继承（实吋） |
| ---- | ---- | --------------------------------- | -------------------------------- |
| TPP  |      | _POSIX_THREAD_PRIO_PROTECT        | 非键壮的瓦斥量优先权保护（实吋） |
| TPS  |      | _POSIX_THREAD_PRIORITY_SCHEDULING | 线程执行调度（实时〉             |
| TSA  | •    | _POSIX_THREAD_ATTR_STACKADDR      | 线程栈地址属性                   |
| TSH  | •    | _POSIX_THREAD_PROCESS_SHARED      | 线程进程共享同步                 |
| TSP  |      | _POSIX_THREAD_SPORADIC_SERVER     | 线程阵发性服务器（实时）         |
| TSS  | •    | _POSXX_THREAD_ATTR_STACKSIZE      | 线程栈长度属性                   |
| TYM  |      | _POSIX_TYPED_MEMORY_OBJECTS       | 类型存储对象（实吋）             |
| XSI  | •    | XOPEN UNTX                        | X/Open扩充接口                   |

图2-5 P0SIX.1可选接口组和选项码

POSIX.1没有包括超级用户（superuser）这样的概念，代之以规定某咎操作要求“适当的优 先权”，POSIX.I将此术语的含义留由具体实现进行解释。某些符合美国国防部安全性指南要求的 UNIX系统具有很多不同的安全级，本书仍使用传统的UNIX术语，并指明要求超级用户特权的 操作。

经过20多年的工作，相关标准己经成熟稳定。POS1X.1标准现在由Austin Group开放工作组 （http://www.opengroup.org/austin）锥护。为了保证它们仍然有价值，仍需经常对送些 标准进行更新或再确认。

2.2.3 Single UNIX Specification

Single UNIX Specification （SUS，单一UNIX 规范）是POSIX.I 标准的一个超集，它定义了 一些附加接口扩展了 POSIX.1规范提供的功能。POSIX.1相当于Single UNIX Specification中的基 本规范部分。

POSIX.1 中的 X/Open 系统接口（X/Open System Interface, XSI）选项描述了可选的接口， 也定义了遵循XSI （XSI conforming）的实现必须支持POSIX.I的哪些可选部分。这些必须支 持的部分包括：文件同步、线程栈地址和长度属性、线程进程共享同歩以符 号常量（在图2-5中它们被加上“SUS强制的”的标记）。只有遵循XSI的实现才能称为UNIX

系统。

Open Group拥有UNIX商柄，他们使用Single UNIX Specification定义了 一系列接口。一个系 统要想称为UNIX系统，其实现必须支持这些接口。UNIX系统供应商必须以文件形式提供符合

■性声明，并通过验证符合性的测试，才能得到使用UNIX商标的许可证。

有些接口在遵循XSI的系统中是可选的，这些接口根据功能被分成若干选项组（option group）,具傳如下。

•加密：由符号常量_XOPEN_CRYPE标记。

•实时：由符号常量_XOPEN_REALTIME标记。

•髙级实时。

•实时线程：由符号常量_XOPEN JIEALTIME_THREADS标记。

•高级实时线程。

Single UNIX Specification是Open Group的出版物，而Open Group是由两个工业社团 X/Open和开放系统较件基金会（Open System Software Foundation, OSF）在1996年合并构成 的。X/Open过去出版了    （X/Open可穆植性指南），它采用了若干特定

标准，填补了其他标准缺失功能的空白。这些指南的目的是改善应用的可移植性，使其不仅仅 符合已发布的标准。

1~1    X/Open 在 1994 年发布了 Single UNIX Specification 第 1 版，因为它大约包含 了 1170 个接口，

因此也称为“Spec 1170”。定源自通用开放软件环境(Common Open Software Environment. COSE)的倡议，该倡议的目标是进一步改善应用程序在所有UNIX操作系统实现之间的可移植 性。COSE的成员包括Sun、IBM、HP、Novell/USL以及OSF等，他们的UNIX都包含了通用商 业化应用软件使用的接口，这较之仅仅赞同和支持标准前进了一大步。从这空应用软件的接口中 选出的1170个接口按包括在下列标推中：X/Open通用应用环境(Common Application Environment, CAE)第4发行版(也被称为XPG4,以表示它与其前身X/Open Portability Guide 的历史关系)、系统 V 接口定义(System V Interface Definition, SVID)第 3 版 Level 1 接口、OSF 应用环境规范(Application Environment Specification，AES) Full Use 接口。

1997年，Open Group发布了 Single UNIX Specification第2版。新版本增加了对线程、实时 接口、64位处理、大文件以及增强的多字节字符处理等功能的支持。

Single UNIX Specification 第 3 版(SUSv3)由 Open Group 在 2001 年发布。SUSv3 的基本规 范与IEEE标准1⑻3.1-2001相同，分成4个部分：基本定义、系统接口、shell和实用程序以及基 本理论。SUSv3还包括X/Open Curses第4发行版第2版，但该规范并不是POSEX.1的组成部分。

2002 年，ISO 将 IEEE 标准 1003.1-2001 批准为国际标准 ISO/IEC 9945:2002。Open Group 在 2003年再次更新了 1003J标准，包括了技术方面的更正。ISO将其批准为国际标准ISO/IEC 9945:2003。2004 年 4 月，Open Group 发布了 Single UNIX Specification 第 3 版 2004 年版，将更 多技术上的更正合并到标准的正文中。

2008年，Single UNIX Specification再次更新，包括了更正和新的接口、移除弃用的接口以及将 一些未来可能被删除的接口标记为弃用接口等。另外，有一些过去被认为可选的接口变成必选接口， 其中包括异步I/O、屏障、时钟选择、存储映像文件、内存保护、读写锁、实时信号、POSIX信号 量、旋转锁、线程安全函数、线程、超时机制以及时钟等，最终形成的标准就是基本规范的第7发 行版，也即POSIX.1-2008。Open Group把这个版本和X/OPEN Curses规范的更新版打包，并于2010 年作为Single UNIX Specification第4版发布。我们把这个规范称为SUSv4。

2.2.4 FIPS

FIPS代表的是联邦信息处理标准(Federal Information Processing Standard)，这一标准是由美 国政府发布的，并由美国政府用于计算机系统的采购。F1PS151-1 (1989年4月)基于IEEE标准 1003.1-1988 及 ANSI C 标准草案。此后是 FIPS 151-2( 1993 年 5 月)，它基于 IEEE标准 1003.1-1990。 在POS1X.1中列为可选的某些功能，在FIPS 151-2中是必需的。所有这些可选功能在POSIX.1-2001

r32~|中已成为强制性要求。

POSIX.1 FIPS的作用是，它要求任何希望向美国政府销售符合POSDC1标准的计算机系统的 厂商都应支持POSIX.1的某些可选功能《因为POSIX.1 FIPS己经撤回，所以在本书中我们不再 进一步考虑它。

##### 2.3 UNIX系统实现

上一节说明了 3个由各自独立的组织所制定的标准：ISOC, IEEEPOSIX以及Single UNIX

Specification。但是，标准只是接口的规范。这些标淮是如何与现实世界相关连的呢？这些标准 由厂商采用，然后转变成具体实现。本书中我们不仅对这些标准感兴趣，还对它们的具体实现 感兴趣。

在McKusick等［1996］的1.1节中给出了 UNIX系统家族树的详细历史。UNIX的各种版本和 变体都起源于在PDP-11系统上运行的UNIX分时系统第6版（1976年）和第7版（1979年）（通 常称为V6和V7）。这两个版本是在贝尔实验室以外首先得到广泛应用的UNIX系统。从这棵树 上演进出以下3个分支。

（1）    AT&T分支，从此引出了系统ID和系统V （被称为UNIX的商用版本）。

（2）    加州大学伯克利分校分支，从此引出4.XBSD实现。

（3）    由AT&T贝尔实验室的计算科学研究中心不断开发的UNIX研究版本，从此引出UNIX 分时系统第8版、第9版，终止于1990年的第10版。

2.3.1 SVR4

SVR4 （UNIX System V Release 4）是 AT&T 的 UNIX 系统实验室（UNIX System Laboratories,

USL，其前身是AT&T的UNIX Software Operation）的产品，它将下列系统的功能合并到了一个 一致的操作系统中：AT&T UNIX系统V 3.2版（SVR3.2）、Sun Microsystems公司的SunOS操作 系统、加州大学伯克利分校的4.3BSD以及微软的Xenix系统（Xenix是在V7的基础上开发的，

后来又采纳了很多系统V的功能）。其源代码于1989年后期发布，在1990年开始向终端用户提 供。SVR4 符合 POSIX 1003.1 标准和 X/Open XPG3 标准。

AT&T也出版了系统V接口定义（SV1D） ［AT&T 1989］。SVID第3版说明了 UNIX系统要 达到SVR4质量要求必须提供的功能。如同POSIX.1 —样，SVID定义了一个接口，而不是一种 实现。SVID并不区分系统调用和库函数。对于一个SVR4的具体实现，应查看其参考手册，以 了解系统调用和库函数的不同之处［AT&T 1990e］。    ［jE

2.3.2 4.4BSD

BSD （Berkeley Software Distribution）是由加州大学伯克利好校的计算机系统研究组（CSRG）研 究开发和分发的。4.2BSD于1983年问世，4.3BSD则于1986年发布。这两个版本都在VAX小型机 上运行。它们的下一个版本4.3BSD Tahoe于1988年发布，在一台称为Tahoe的小型机上运行（Leffler 等［1989］说明了 4.3BSD Tahoe版）。其后又有1990年的4.3BSDReno版，它支持很多POSDC1的功能。

最初的BSD系统包含了 AT&T专有的源代码，它们需要AT&T许可证。为了获得BSD系统的 源代码，首先需要持有AT&T的UNIX源代码许可证。这种情况正在改变，近几年.越来越多的 AT&T源代码被替换成非AT&T源代码，很多添加到BSD系统上的新功能也来自于非AT&T方面。

1989年，伯克利将4.3BSD Tahoe中很多非AT&T源代码包装成BSD网络软件1.0版，并使 其成为可公开获得的软件。1991年发布了 BSD网络软件2.0版，它是从4.3BSD Reno版派生出 来的，其目的是使大部分（如果不是全部的话）4.4BSD系统不再受AT&T许可证的限制，这样， 大家都可以得到源代码。

4.4BSD-Lite是CSRG计划开发的最后-•个发行版，由于与USL产生的法律纠纷，该版本曾 —度延迟推出。在纠纷解決后,4.4BSD-Lite立即于1994年发布，并且不再需要具有UNIX源代 码使用许可证就可以使用它。1995年CSRG发布丁修复丁 bug的版本。4.4BSI>Lite第2发行版 是CSRG的最后一个BSD版本（McKusick等［1996］描述了该BSD版本）。

在伯克利所进行的UNIX开发工作是从PDP-11开始的，然后转移到VAX小型机上，接着 又转移到工作站上。20世纪90年代早期，伯克利得到支持在广泛应用的80386个人计算机上 开发BSD版本，结果产生了 386BSD。这一工作是由Bill Jolitz完成的，其工作在1991年全年 的Dr    期刊上以每月一篇文章连载发表。其中很多代码出现在BSD网络软件2.0版中。

2.3.3    FreeBSD

FreeBSD基于4.4BSD-Lite操作系统。在加州大学伯克利分校的CSRG决定终止其在UNIX 操作系统的BSD版本的研发工作，而且386BSD项目被忽视很长时间之后，为了继续坚持BSD 系列，形成了 FreeBSD项目。

由FreeBSD项目产生的所有软件，包括其二进制代码和源代码，都是免费使用的。为了测试 书中的实例，本书选取了 4个操作系统，FreeBSD 8.0操作系统是其中之一。

有许多基于BSD的免费操作系统。NetBSD项目（http://www.netbsd.org）类似于 FreeBSE）项目，但是更注重不同硬件平台之间的可移植性。OpenBSD项目（<http://www.openbsd>.

5 org）也类似于FreeBSD项目，但更注重于安全性。

2.3.4    Linux

Linux是一种提供类似于UNIX的丰富编程环境的操作系统，在GNU公用许可证的指导下， Linux是免费使用的。Linux的普及是计算机产业中的一道亮丽风景线。Linux经常是支持较新硬 件的第一个操作系统，这一点使其引人注目。

Linux是由Linus Torvalds在1991年为替代MINIX而研发的。一位当时名不见经传人物的努 力掀起了澎湃巨浪，吸引了遍布全世界的很多软件开发者，在使用和不断增强Linux方面自愿贡 献出了他们大量的时间。

Ubuntu 12.04的Linux分发版本是用以测试本书实例的操作系统之一。孩系统使用了 Linux 操作系统3.2.0版内核。

2.3.5    Mac OS X

与其以前的版本相比，Mac OS X使用了完全不同的技术。其核心操作系统称为“Darwin”，它 基于Mach内核（Accetta等［1986］）、FreeBSD操作系统以及具有面向財象框架的驱动和其他内核 扩展的结合。Mac OS X 10.5的Intel部分已经被验证为是一个UNIX系统《 （关于UNIX验证的更 多信息，请参见 http: //[www.opengroup.org/certification/idx/UNIX.html](http://www.opengroup.org/certification/idx/UNIX.html%ef%bc%89%e3%80%82)[）。](http://www.opengroup.org/certification/idx/UNIX.html%ef%bc%89%e3%80%82)

Mac OS X 10.6.8 （Darwin 10.8.0）是用以测试本书实例的操作系统之一。

2.3.6 Solaris

Solaris是由Sun Microsystems （现为Oracle）开发的UNIX系统版本。基于SVR4,在超过 15年的时间里，Sun Microsystems的工程师对其功能不新増强。它是唯一在商业上取得成功的 SVR4后裔，并被正式验证为UNIX系统。

2005年，Sun Microsystems把Solaris操诈系统的大部分源代码开放给公众，作为OpenSolaris 开放源代码操作系统的一部分，试图建立围绕Solaris的外部开发人员社区。

Solaris 10 UNIX操作系统也是用以测试本书实例的操作系统之一。

2.3.7其他UNIX系统

已经通过验证的其他UNIX版本包括：

•    AIX，IBM 版的 UNIX 系统：

•    HP-UX, HP 版的 UNIX 系统：

•    IRIX, Silicon Graphics 版的 UNIX 系统；

•    UnixWare, SVR4派生的UNIX系统，现由SCO销售。    [35]



2.4标准和实现的关系

前面提到的各个标准定义了任一实际系统的子集，本书主要关注4种实际的UNIX系统：FreeBSD 8.0、Linux 3.2.0, Mac OS X 10.6.8 和 Solaris 10。在这 4 种系统中，虽然只有 MacOSX 和 Solaris 10 能 够称自己是一种UNIX系统，但是所有这4种系统都提供UNIX编程环境。因为所有这4种系统都在 不同程度上符合POSIX标准，所以我们也将重点关注POSIX.1标准所要求的功能，并指出这4种系 统具体实现与POSIX之间的差别。仅仅一个特定实现所具有的功能和例程会被清楚地标记出来。我 们还英注那些属于UNIX系统必需的，但却在符合POSIX标准的系统中是可选的功能。

应当看到，这些实现都提供了对它们早期版本（如SVR3.2和4.3BSD）功能的向后兼客性。

例如，Solaris对POSIX. 1规范中的非阻塞I/O （0„N0NBLOCK）以及传统的系统V中的方法 （O.NDELAY）都提供了支持。本书将只使用POSIX.1的功能，但是也会提及它所替换的是哪一种 非标准功能。与此相类似，SVR3.2和4.3BSD以某种方法提供了可靠的信号机制，这种方法也有别 于POSIX. 1标准。第10章将只说明POSIX. 1的信号机制。

###### 2.5限制

UNIX系统实现定义了很多幻数和常量，其中有很多已被硬编碍到程序中，或用特定的技术 确定。由于大量标准化工作的努力，已有若干种可移植的方法用以确定这些幻数和具体实现定义 的限制。送非常有助于改善UNIX环境下软件的可移植性。

以下两种类型的限制是必需的。

（1）    编译时限制（例如，短整型的最大值是什么？）

（2）    运行时限制（例如，文件名有多少个字符？）

编译时限制可在头文件中定义。程序在编译时可以包含这些头文件。但是，运行时限制则要 求进程调用一个函数获得限制值。

另外，某些限制在一个给定的实现中可能是固定的（因此可以静态地在一个头文件中定义），而在 另一个实现中则可能是变动的（需要有一个运行时函数调用）。这种类型限制的一个例子是文件名的最 大字符数。SVR4之前的系统V由于历史原因只允i午文件名最多包含14个字符，而源于BSD的系统则 将此增加为255。目前，大多数UNIX系统支持多文件系统类型，而每一种类型有它自己的限制。文件 名的最大长度依赖于该文件处于何种文件系统，例如，根文件系统中的交件茗长度限制可能是14个字 符，而在另一个文件系统中文件名长度限制可能是255个字符，这是运行时限制的一个例子。

为了解決这类问题，提供了以下3种限制。    [36]

（1）编译时限制（头文件）。

（2）    与文件或目录无关的运行时限制（sysconf函数）。

（3）    与文件或目录有关的运行时限制（pathconf和fpathconf函数）。

使事情变得更加复杂的是，如果一个特定的运行时限制在一个给定的系统上并不改变，则可 将其静态地定义在一个头文件中，但是，如果没有将其定义在头文件中，应用程序就必须调用3 个conf函数中的一个（我们很快就会对它们进行说明），以确定其运行时的值。

2.5.1 ISO C 限制

ISOC定义的所有编译时限制都列在头文件＜limitS.h＞m （见图2-6）。这些限制常量在一 个给定系统中并不会改变，表中第3列列出了 ISO C标准可接受的最小值。这用于16位整型的 系统，用1的补码表示。第4列列出了 32位整型Linux系统的值，用2的补码表示。注意，我们 没有列出无符号数据类型的最小值，这些值应该都为0。在64位系统中，其long整型的最大值 与表中long long整型的最大值相匹配。

| 名称       | 说明                               | 可接受的最小值           | 典型值                    |
| ---------- | ---------------------------------- | ------------------------ | ------------------------- |
| CHAR_BIT   | char的位数                         | 8                        | 8                         |
| CHAR.MAX   | char的最大值                       | （见后）                 | 127                       |
| CHAR_MIN   | char的最小值                       | （见后）                 | -128                      |
| SCHAR_MAX  | signed char的最大值                | 127                      | 127                       |
| SCHAR_MIN  | signed char的最小值                | -127                     | -128                      |
| UCHAR MAX  | unsigned char的最大值              | 255                      | 255                       |
| INT^MAX    | int的最大值                        | 32 767                   | 2 147 483 647             |
| INT_MIN    | int的最小值                        | -32 767                  | -2 147 483 648            |
| UINT MAX   | unsigned int的最大值               | 65 535                   | 4 294 967 295             |
| SHRT_MAX   | short的最大值                      | 32 767                   | 32 767                    |
| SHRT_MIN   | short的最小值                      | -32 767                  | —32 768                   |
| USHRT MAX  | unsigned short 的最大值            | 65 535                   | 65 535                    |
| LONG_MAX   | long的最大值                       | 2 147 483 647            | 2 147 483 647             |
| L0NG_MIN   | long的最小值                       | -2 147 483 647           | -2 147 483 648            |
| ULONG MAX  | unsigned long的最大值              | 4 294 967 295            | 4 294 967 295             |
| LLONGJ4AX  | long long的最大值                  | 9223 372036 854 775 807  | 9 223 372 036 854 775 807 |
| LLONG_MIN  | long long的最小值                  | -9223 372036 854775807   | -9223 372036 854775 808   |
| ULLONG MAX | unsigned long long 的最大值        | 18446 744 073 709551 615 | 18446 744 073 709 551 615 |
| MB LEN MAX | 在一个多字节字符常量中的最大字节数 | 1                        | 6                         |

图2-6 ＜limits.h＞中定义的整型值大小

我们将会遇到的一个区别是系统是否提供带符号或无符号的的字符值。从图2-6中的第4列可 [37]以看出，该特定系统使用带符号字狩。从图中可以看到CHAR_MIN等于SCHAR_MIN, CHAR_MAX 等于SCHAR_MAX。如果系统使用无符号字符，则CHAR_MIN等于0，CHAR_MAX等于UCHAR_MAX。

在头文件＜【1031:^＞中，对浮点数据类型也有类似的一组定义。如若读者在工作中涉及大量 浮点数据类型|则应仔细查看该文件。

虽然ISO C标准规定了整型数据类型可接受的最小值’但POSIXJ对C标准进行了扩充。为丁符 合POSIX.1标淮，具体实现必须支持INT_MAX的最小值为2 147 483 647, INT_MIN为2 147483 647, UINT_MAX为4 294 967 295。因为POS1X.1要求具体实现支持8位的char类型，所以CHAR_BIT必 须是 8，SCHAR_MIN 必须是-128, SCHAR_MAX 必须是 127，UCHAR_MAX 必须是 255。

我们会遇到的另一个ISO C常量是FOPEN_MAX,这是具体实现保证可同时打开的标准I/O流 的最小个数，该值在头文件＜3七＜^0.11＞中定义，其最小值是8。POSDU中的STREAM_MAX (若 定义的话)则应与FOPEN_MAX具有相同的值》

ISO C还在＜stdio.h＞中定义了常量TMP_MAX, *这是由tmpnam函数产生的唯一文件名的 最大个数。关于肐常量我们将在5.13节中进行更多说明。

虽然ISO C定义了常量FILENAME_MAX,但我们库避免使用该常量，因为POSIX.1提供了 更好的替代常量(NAMEJ4AX和PATHJWC)，我们很快就会介绍该常量。

在图2-7中，我们列出了本书所讨论4种平台上的FILENAME_MAX、FOPEN_MAX和TMP_MAX值。

| 限制                         | FreeBSD 8.0        | Linux 3.2.0    | Mac OS X 10.6.8    | Solaris 10    |
| ---------------------------- | ------------------ | -------------- | ------------------ | ------------- |
| FOPEN_MAXTMP_MAXFILENAME MAX | 20308 915 7761 024 | 16238 3284 096 | 20308 915 7761 024 | 2017 576! 024 |

图2-7在各种平台上ISO的限制

2.5.2 POSIX 限制

POSIX.1定义了很多涉及操作系统实现限制的常量，遗憾的是，这是POSIX.1中最令人迷惑 不解的部分之一。虽然POSIX.1定义了大量限制和常量，我们只关心与基本POSIX.1接口有关的 部分。这些限制和常量分成下列7类。

(1)    数值限制：LONG_BXT＞ SSIZE_MAX 和 WORD—B工T。

(2)    最小值：图2-8中的25个常量。

(3)    最大值：_POSIX_CLOCKRES_MIN。

(4)    垣行时可以增加的值：CHARCLASS_NAME_MAX, COLL_WE工GHTS_MAX、LINE_MAX、 NGROUE＞S_MAX 和 RE_DUP一MAX。

(5)    运行时不变值(可能不确定)：图2-9中的17个常量(加上12.2节中介绍的4个常量和 14.5节中介绍的3个常量

(6)    其他不变值：NL_ARGMAX＞ NL—MSGMAX、NL_SETMAX 和 NL_TEXTMAX。

(7)    路径名可变值：FILES工ZEBITS、LINK_MAX、MAX_CANON. MAX_INPUT＞ NAME_MAX＞ PATH_MAX、PIPE_BUF 和 SYML工NK_MAXO

在这些限制和常量中，某些可能定义在＜limitS.h＞中，其余的则按具体条件可定义、可不 定义。在2.5.4节中说明sysconf、pathconf和fpathconf圉数时，我们将描述可定义或可 不定义的限制和常量。在图2-8中，我们列出了 25个最小值。

这些最小值是不变的——它们并不随系统而改变。它们指定了这些特征最具约束性的值。一 个符合POSIX.1的实现应当提供至少这样大的值。这就是为什么将它们称为最小值，虽然它们的 名字都包含了 MAX。另外，为了保证可移植性，一个严格符合POSIX标淮的应用程序不应要求 更大的值。我们将在本书的适当章节说明每一个常量的含义。

一个严格符合(strictly conforming) POSIX的应用区别于一个刚刚符合POSIX (merely POSIX confirming)的应用。符合POSIX的应用只使用在IEEE 1003.1-2001中定义的接口。严格符合POS1X 的应用满足更多的限制，例如，不依赖于POSIX未定义的行为、不使用其任何已弃用的接口以及

!不要求所使用的常量值大于图2-8中所列出的最小值。

| 名称             | 说明：最小可接受值            | 值                                                           |        |
| ---------------- | ----------------------------- | ------------------------------------------------------------ | ------ |
| POSIX            | _ARG_MAX                      | exec函数的参数长度                                           | 4 096  |
| _POSIX_CHILD_MAX | 每个实际用户ID的子进程数      | 25                                                           |        |
| POSIX            | „DELAYTIMER_MAX               | 定吋器最大超限运行次数                                       | 32     |
| POSIX            | HOST NAME MAX                 | gethostname函数返回的主机名长度                              | 255    |
| POSIX            | •LINK_MAX                     | 至一个文件的链接数                                           | 8      |
| POSIX            | LOGIN NAME MAX                | 登录名的长度                                                 | 9      |
| POSIX            | ■MAX_CAN0N                    | 终端规范输入队列的字节数                                     | 255    |
| POSIX            | _MAX_INPUT                    | 终端输入队列的可用空间                                       | 255    |
| POSIX            | _HAME_MAX                     | 文件名中的字节数，不包括终止null字节                         | 14     |
| POSIX            | _NGROUPS_MAX                  | 每个进程同时添加的组ID数                                     | 8      |
| POSIX            | _OPEN_MAX                     | 每个进程的打开文件数                                         | 20     |
| POSIX            | PATHJ4AX                      | 路径名中的字节数，包括终止null字节                           | 256    |
| POSIX PIPE BUF   | 能原子地写到_个管道中的字节数 | 512                                                          |        |
| _POSIX.          | ■RE_DUP_MAX                   | 当使用间陽表示法\{m, n\}时，regexec和regcomp函数允许 的基本正则表达式重复发生次数 | 255    |
| POSIX            | _RTSIG_HAX                    | 为应用预留的实时信号编号个数                                 | 8      |
| POSIX            | SEM NSEMS MAX                 | 一个进程可以同时使用的信号量个数                             | 256    |
| POSIX            | SEM VALUE MAX                 | 信号量可持有的值                                             | 32 767 |
| POSIX            | _SIGQUEUE_MAX                 | —个进程可发送和挂起的排队信号的个数                          | 32     |
| POSIX            | ，SSIZE_MAX                   | 能存在ssize_t对象中的值                                      | 32 767 |
| _POSIX-          | _STREAM_MAX                   | 一个进程能同时打开的标准I/O流数                              | 8      |
| POSIX            | _SYMLINK_MAX                  | 符号链接中的字节数                                           | 255    |
| POSIX            | •SYMLOOP_MAX                  | 在解析路径名时，可遍历的符号链接数                           | 8      |
| POSIX            | _TIMER_MAX                    | 每个进程的定时器数目                                         | 32     |
| POSIX            | TTY NAME MAX                  | 终端设备名长度，包括终止null字节                             | 9      |
| POSIX.           | •TZNAME MAX                   | 吋区名字节数                                                 | 6      |

r39~|    图 2-8 <limits.h>中的 POSIXJ 最小值

遗憾的是，这些不变最小值中的某一些在实际应用中太小了。例如，目前在大多数UNIX系

统中，每个进程可同时打开的文件数远远超过20。另外，_POSIX_PATH_MAX的最小限制值为255, 这太小了，路径名可能会超过这一限制。这意味着在编译时不能使用_POSIX_OPEN_MAX和 _POSXX_PATH_MAX这两个常量作为数组长度。

图2-8中的25个不变最小值的每一个都有一个相关的实现值，其名字是将图2-8中的名字删 除前缀_POSIX_后构成的。没有_POSIX_前缀的名字用于给定具体实现支持的该不变最小值的实 际值25个i现值是本节开始部分所列出的1、4、5、7类：2个是运行时可以增加的值、15 个是运行时不变值、7个是路径名可变值，以及数值SSIZE_MAX）。问题是并不能确保所有这25 个实现值都在<limit.h>*文件中定义。

例如，某个特定值可能不在此头文件中定义，其理由是：一个给定进程的实际值可能依 赖于系统的存储总量。如果没有在头文件中定义它们，则不能在编译时使用它们作为数组边 界。所以，POSIX.1提供了 3个运行时函数以供调用，它们是：sysconf、pathconf以及 fpathconfc使用这3个函数可以在运行时得到实际的实现值。但是，还有一个问题，其中 某些值由POSIX.l定义为“可能不确定的”（逻辑上无限的），这就意味着该值没有实际上 限。例如，在Solaris中，进程结束时注册可运行atexit的函数个数仅受系统存储总量的限 制。所以在Solaris中，ATEXIT_MAX被认为是不确定的。2.5.5节还将讨论定行时限制不确

定的问题。

| 名称           | 说明                                    | 最小可接受值          |
| -------------- | --------------------------------------- | --------------------- |
| ARG.MAX        | exec函数族的参数最大长度                | POSIX ARG MAX         |
| ATEXIT_MAX     | 可用atexit函数登记的最大函数个数        | 32                    |
| CHILD_MAX      | 每个实际用户ID的子进程最大个数          | _POSIX_CHXLD_MAX      |
| DELAYTIMER_MAX | 定时器最大超限运行次数                  | _POSIX_DELAYTIMER_MAX |
| HOST_NAME_MAX  | gethostname返回的主机名长度             | _POSIX_HOST_NAME_MAX  |
| LOGIN_NAME_MAX | 登录名最大长度                          | _POSIX_LOGIN_NAME_MAX |
| OPEN_MAX       | 陚予新建文件描述符的最大值+1            | _POSIX_OPEN_MAX       |
| PAGESIZE       | 系统内存页大小(以字节为单位)            | 1                     |
| RTSIG_MAX      | 为应用程序预留的实时信号的最大个数      | POSIX RTSIG MAX       |
| SEM_NSEMS_MAX  | 一个进程可使用的信号量最大个数          | _POSIX_SEM_NSEMS_MAX  |
| SEM_VALUE_MAX  | 信号量的最大值                          | _POS IX_SEM_VALUE_MAX |
| SIGQUEUE_MAX   | 一个进程可排队信号的最大个数            | _POSIX_SIGQUEUE_MAX   |
| STREAM_MAX     | 一个进程一次可打开的禄准I/O流的最大个数 | _POSIX_STREAM_MAX     |
| SYMLOOP_MAX    | 路径解析过程中可访问的符号链接数        | _POSIX_SYMLOOP_MAX    |
| TIMER_MAX      | -个进程的定时器最大个数                 | POSIX TIMER MAX       |
| TTY_NAME_MAX   | 终端设备名长度.其中包括终止的mill字节   | _POSIX_TTY_NAME_MAX   |
| TZNAME MAX     | 时区名的字节数                          | POSIX TZNAME MAX      |

图2-9 〈limits.h＞中的POSDC1运行时不变值

2.5.3 XSI 限制

xsi定义了代表实现限制的几个常量。

(1)    最小值：图2-10中列出的5个常量。

(2)    运行时不变值(可能不确定)：IOV_MAX和PAGE_SIZE。

图2-10列出了最小值。最后两个常量值说明了 POSIX.1最小值太小的情况，根据推测这可 能是考虑到了嵌入式POSIX.1实现。为此，Single UNIX Specification为符合XSI的系统增加了具 有较大最小值的符号。

| 名称            | 说明                                   | 最小可接受值 | 典型值 |
| --------------- | -------------------------------------- | ------------ | ------ |
| NL_LANGMAX      | 在LANG环境变量中最大字节数             | 14           | 14     |
| NZERO           | 默认进程优先级                         | 20           | 20     |
| XOPEN IOV MAX   | readv或writev可使用的最多iovec结构个数 | 16           | 16     |
| XOPEN NAME MAX  | 文件名中的字节数                       | 255          | 255    |
| XOPEN PATH一MAX | 路径名中的字节数                       | 1 024        | 1 024  |

图 2-10〈limits.h＞中的XS1 最小值    40

I

2.5.4 函数 sysconf、pathconf 和 fpathconf    Lll

我们己列出了实现必须支持的各种最小值，但是怎样才能找到一个特定系统实际支持的限制 值呢？正如前面提到的，某些限制值在编译时是可用的，而另外一些则必须在运行时确定》我们 也曾提及某些限制值在一个给定的系统中可能是不会更改的，而其他限制值可能会更改，因为它 们与文件和目录相关联。运行时限制可调用下面3个函数之一获得。

♦include <unistd.h> long syaconf(int name)；

long pathconf (const char * pathname, int name); log fpathconf (int fd, int name);

所有函数返回值：若成功，返回相座值；若出错，返回-1 (见后) 后面两个函数的差别是：一个用路径名作为其参数，另一个则取文件描述符作为参数。

图2-11中列出了 sysconf函数所使用的緬参数，它用于标识统限制。&_SC_开始的常量用 作标识运行时限制的sysconf参数。图2-12列出了 pathconf和fpathconf函数为标识系统限制 所使用的name参数。&_PC_开始的常量用作标识运行时限制的pathconf或fpathconf参数。

我们需要更详细地讨论一下这3个函数不同的返回值。

(1)    如果恥me参数并不是一个合适的常量，这3个函数都返回-1，并把errno置为EINVAL。 图2-11和图2-12的第3列给出了我们在整本书中将要涉及的限制常量。

(2)    有些rnwne会返回一个变量值(返回值彡0)或者提示该值是不确定的。不确定的值通过 返回来体现，而不改变errno的值。

| 限制名           | 说明                                                         | name参数             |
| ---------------- | ------------------------------------------------------------ | -------------------- |
| ARG_MAX          | exec函数的参数最大长度(字节)                                 | _SC_ARG_MAX          |
| ATEXIT_MAX       | 可用atexit函数登记的最大函数个数                             | _SC_ATEXIT_MAX       |
| CHILD_MAX        | 每个实际用户1D的最大进程数                                   | _SC_CHILD_MAX        |
| 时钟滴答/秒      | 每秒时钟滴答数                                               | _SC_CLK_TCK          |
| COLL_WEIGHTS_MAX | 在本地定义文件中可以赋予LC_COLLATE顺序关 键字项的最大权重数  | _SC_COLL_WEIGHTS_MAX |
| DELAYTIMER_MAX   | 定时器最大超限运行次数                                       | SC DELAYTIMER_MAX    |
| HOST_NAME_MAX    | gethostname函数返回的主机名最大长度                          | _SC_HOST_NAME_MAX    |
| IOV_MAX          | readv或writev函数可以使用最多的iovec结 构的个数              | _SC_I0V__MAX         |
| LINE_MAX         | 实用程序输入行的最大长度                                     | _SC_LINE_MAX         |
| LOGIN_NAME_MAX   | 登录名的最大长度                                             | SC LOGIN NAME MAX    |
| NGROUPS_MAX      | 每个进程同时添加的最大进程组ID数                             | _SC_NGROUPS_MAX      |
| OPEN_MAX         | 每个进程最大打开文件数                                       | _SC_OPEN_MAX         |
| PAGESIZE         | 系统存储页长度(字节数)                                       | _SC_PAGESIZE         |
| PAGE_SIZE        | 系统存储页长度(字节数)                                       | _SC_PAGE_SI2E        |
| RE_DUP_MAX       | 当使用间隔表示法时，函数regexec和 regcomp允讲的基本正则表达式重复发生次数 | _SC_RE_DUP_MAX       |
| RTSIG_MAX        | 为应用程序预留的实时信号的最大个数                           | _SC_RTSIG_MAX        |
| SEM_NSEMS_MAX    | —个进程可使用的信号量最大个数                                | _SC_SEM_NSEMS_MAX    |
| SEM_VALUE_MAX    | 信号量的最大值                                               | _SC_SEM_VALUE_MAX    |
| SIGQUEUE_MAX     | 一个进程可排队信号的最大个数                                 | _SC_SIGQUEUE_MAX     |
| STREAM_MAX       | —SC STREAM MAX进程在任意给定时刻标准I/O 流的最大个数。如果定义，必须与TOPEN_MAX有相同值 | _SC_STREAM_MAX       |
| SYMLOOP_MAX      | 在解析路径名吋，可遍历的符号链接数                           | _SC_SYMLOOP_MAX      |
| TIMER_MAX        | 每个进程的最大定时器个数                                     | _SC_TIMER_MAX        |
| TTY„NAME_MAX     | 终端设备名长度，包括终止null字节                             | _SC_TTY_NAME_MAX     |
| T2NAME MAX       | 时区名中的最大字节数                                         | SC TZNAME MAX        |

图2-11对sysconf的限制及wwwe参数

| 限制名                      | 说明                                                         | name参数                 |
| --------------------------- | ------------------------------------------------------------ | ------------------------ |
| FILESIZEBXTS                | 以带符号整型值表示在指定目 录中允许的普通文件最大长度所 需的最小位(bit)数 | _PC_FILESI2EBITS         |
| LINK_MAX                    | 文件链接计数的最大值                                         | _PC_LINK_MAX             |
| MAX_CANON                   | 终端规繡入队列的最大字节数                                   | _PC_MAX_CANON            |
| MAX_INPUT                   | 终端输入队列可用空间的字节数                                 | _PC_MAX_INPUT            |
| NfiME_MAX                   | 文件名的最大字节数(不包括 终止null字节)                      | _PC_NAME_MAX             |
| PATH_MAX                    | 相对路径名的最大字节数，包 括终止null字节                    | _PC_PATH_MAX             |
| PIPE_BUF                    | 觀槐写到管道的駄字节数                                       | _PC_PIPE_BUF             |
| _POSIX_TIMESTAMP_RESOLUTION | 文件时间戳的纳秒精度                                         | _PC_TIMESTAMP RESOLUTION |
| SYMLINK MAX                 | 符号链接的字节数                                             | PC SYMLINK MAX           |

图2-12对pathconf和fpathconf的限制及name参數

(3) _SC_CLK_TCK的返回值是每秒的时钟滴答数，用于times函数的返回值(8.17节)。

对于pathconf的参数pcrrArtflme和fpathconf的参数＞5/有很多限制。如果不满足其中任 何一个限制，则结果是未定义的。

(1) _PC_MAX_CANON引用的文件必须是终端文件。

(2)    _PC_LINK_MAX和_?＜：_1[1](#bookmark2) [2](#bookmark3)1时£31[2](#bookmark3)泌1*_1^301^了10^1引用的文件可以是文件或目隶。如 果是目录，则返回值用于目彔本身，而不用于目录内的文件名项。

(3)    JCFILESIZEBITS和_PC_NAMELMAX引用的文件必须是目录，返回值用于该目录中 的文件名。

(4)    +PCjATH_MAX引用的文件必须是目录。当所指定的目录是工作目录时，返回值是相对 路径名的最大长度(遗憾的是，这不是我们想要知道的一个绝对路径名的实际最大长度，我们将 在2.5.5节中再次回到这一问题上来)。

(5)    _PC_PIPE_BUF引用的文件必须是管道、FIFO或目录。在管道或FIFO情况下，返回 值是対所引用的管道或FIFO的限制值。对于目录，返回值是対在该目录中创建的任一 FIFO的 限制值。

(6)    _PC_SYMLINK_MAX引用的文件必须是目录。返回值是垓目录中符号链接可包含字符串 的最大长度。

%■实例

图2-13中所示的awk(l)程序构建了一个C程序，它打印各pathconf和sysconf符号的值。

42

I

44



printf{"main{int argc, char *argv[])\n")

printf{"{Xn"}

printf(argc != 2)\n"J

printf("\t\terr_quit(X^usage: a.out <dirname>\");\n\n")

FS="\t+"

while (getline <"sysconf.sym" > 0) { printf("#ifdef %s\n", $1)

printf("\tprintf(\"%s defined to be %%ld\\n\", (long)%s+0>;\n", §1, $1) printf("#else\n")

printf("\tprintf(\"no symbol for %s\\n\");\n", $1) printf("#endif\n") printf("#ifdef %s\n", $2)

printf("\tpr_sysconf(\"%s =\", %s);\n", $1, $2) printf("#else\nH)

printf("\tprintf(\"no symbol for %s\\n\rt);\n", $2) printf C#endif\n")

}

close("sysconf.sym")

while (getline <"pathconf.sym" > 0) { printf("#ifdef %s\n”， 51)

printf("\tprintf(\"%s defined to be %%ld\\n\", (long)%s+0);\n", $1, $1) printf <"#else\n")

printf ("\tprintf (\°no symbol for %s\\n\")'• \n", $1) printf("#endif\n") printf("ftifdef %s\n”， S2)

printf("\tpr_pathconf(\"%s =\", argv[l], %s);\n", $1, $2) printf (*'#else\n")

printf ("\tprintf {\"no symbol for %s\\n\"); \n'*, $2) printf{"#endif\n")

close("pathconf.sym") exit

}

END {

printf(”\texit(0);\n") printf("IXnXn") printf{"static void\n")

printf("pr_sysconf{char *mesg, int name)\n")

printf("{\n"}

printf("\tlong val;\n\n"J

printf("\tfputs(mesg, stdout);\n")

printf("\terrno = 0;\n")

printf("\tif { {val = sysconf(name)) < 0) {\n"J printf ("\t\tif {errno ! = 0) {\n*'} printf("\t\t\tif (errno == EINVAL)\n")

printf("\t\t\t\tfputs(not supported)stdout);\n") printf("\t\t\telse\n")

printf(M\t\t\t\terr_sys(Vsysconf error\");\n") printf(else {\n"}

printf("\t\t\tfputs(\" (no limit)\\n\'\ stdout);\n") printf("\t\t)\n") printf("\t} else {\n")

printf("\t\tprintf(\” %%ld\\n\", val);\n") printf("\t)\n")

printf ("JXnXn'*) printf("static void\n")

printf(”pr_pathconf(char *mesg, char *path, int name)\n") printf

printf《"\tlong val;\n") printf

printf《"\tfputs(mesg, stdout);\n") printf("\terrno = 0;\n")

printf ("\tif Uval = pathconf (path, name)) < 0) {\n"} printf("\t\tif {errno != 0) {\n"} printf("\t\t\tif (errno == EINVAL)\n")

printf("\t\t\t\tfputs(\" (not supported)\\n\”， stdout);\n") printf("\t\t\telse\n")

printf("\t\t\t\terr_sys(\"pathconf error, path = %%s\", path);\n") printf(”\t\t) else (\n"}

printf("\t\t\tfputs(\" (no limit)\\n\", stdout);\n") printf("\t\t)\n") printf (•’\t) else l\n"}

printf("\t\tprintf(\" %%ld\\n\", val);\n") printf

printf("}\nM)

J

图2-13构建C程序以打印所有得到支持的系统配置限制

读awk程序读两个输入文件-pathconf. sym和sysconfig. sym,这两个文件中包含

了用制表符分隔的限制名和符号列表。并非每种平台都定义所有符号，所以围绕每个pathconf 和sysconf调用，awk程序都使用了必要的#ifdef语句。

例如，awk程序将输入文件中类似于下列形式的行：

NAME_MAX _PC_NAME_MAX

转换成下列C代码：

\#ifdef NAME_MAX

printf ("NAME_MAX is defined to be %d\n", NAME—MAX十0)'•

\#else

printf("no symbol for NRME_MAX\n");

\#endif

ttifdef _PC_NAME_MAX

pr_pathconf("NAME_MAX =", argv[l], _PC_NAME_MAX);

\#else

printf{"no symbol for _PC_NAME_MAX\n");

\#endif    '

由a«k产生的C程序如图2-14所示，它会打印所有这些限制，并处理未定义限制的情况。®

^include "apue.h" ♦include <errno.h> #include <limits,h>

static void pr_sysconf(char *, int);

static void pr_pathconf(char *, char *, int);

int

main(int argc, char *argv⑴

(

if (argc != 2)

err_quit("usage： a.out <dirname>");

\#ifdef ARG_MAX

printf{"ARG_MAX defined to be %ld\n", (long)ARG_MAX+0); #else

printf{"no symbol for ARG_MAX\n");

存endif

\#ifdef _SC_ARG_MAX

prsysconf{"ARG_MAX =_SC_ARG_MAX);

\#else

printf("no symbol for _SC_ARG_MAX\n");

\#endif

/* similar processing for all the rest of the sysconf symbols... */

\#ifdef MAX_CANON

printf("MAX_CANON defined to be    (long)MAX_CANON+0);

\#else

printf("no symbol for MAX_CANON\nu);

\#endif

\#ifdef _PC_MAX_CANON

pr_pathconf("MAX_CANON =", argv[l】， _PC_MAX_CANON»;

番 else

printf("no symbol for _PC_MAX_CANON\n");

轉endif

/* similar processing for all the rest of the pathconf symbols.

exit(0);

static void

pr_sysconf(char *mesg, int name) {

long val;

fputs(mesg, stdout); errno = 0;

if ((val = sysconf(name)) < 0) { if {errno != 0) {

if {errno == EINVAL)

fputs(" (not supported)\n", stdout);

| 47 |    else

err sys("sysconf error");

} else {

fputs (’’ (no limit) \n", stdout);

1

} else {

printf{"    val);

}

static void pr_pathconf(char *mesg, char *path, int name)

long val;

fputs(mesg, stdout); errno = 0;

if ((val = pathconf{path, name)) < 0) { if (errno != 0) {

if (errno == EINVAL)

fputs(" (not supported)\n", stdout);

else

err_sys("pathconf error, path = %s", path)；

(else {

fputs(" (no limit)\n", stdout);

}

} else {

printf(" ildXn", val)?

)

}

图2-14打印所有可能的sysconf和pathconf值 图2-15总结了在本书讨论的4种系统上图2-14所示程序的输出结果。“无符号”项表示该系

统没有提供相应_3(?或_?«3符号以查询相关常量值。因此，该限制是未定义的。与此对比，“不支 持”项表示孩符号由系统定义，但是未被sysconf和pathcon函数识别。“无限制”项表示该 系统将相关常量定义为无限制，但并不表示该限制值可以是无限的，它只表示该限制值不确定。

| 限制                | FreeBSD 8.0  | Linux 3.2.0  | MacOSX10.6.8  | Solaris 10 |           |
| ------------------- | ------------ | ------------ | ------------- | ---------- | --------- |
| UFS文件系统         | PCFS文件系统 |              |               |            |           |
|                     |              |              |               |            |           |
| ARG_MAX             | 262 144      | 2 097 152    | 262 144       | 2 096 640  | 2 096 640 |
| ATEXIT_MAX          | 32           | 2147 483 647 | 2 147 483 647 | 无限制     | 无限制    |
| CHARCLAS S_NRME_MAX | 无符号       | 2 048        | 14            | 14         | !4        |
| CHILD_MAX           | 1 760        | 47211        | 266           | 8 021      | 8 021     |
| 时钟滴答/秒         | 128          | 100          | 100           | 100        | 100       |
| COLL_WEIGHTS_MAX    | 0            | 255          | 2             | 10         | 10        |
| FILESIZEBITS        | 64           | 64           | 64            | 41         | 不支持    |
| HOST_NAME_MAX       | 255          | 64           | 255           | 255        | 255       |
| IOV_MAX             | 1 024        | 1 024        | 1 024         | 16         | 16        |
| LINE_MAX            | 2 048        | 2 048        | 2 048         | 2 048      | 2 048     |
| LINK_MAX            | 32 767       | 65 000       | 32 767        | 32 767     | 1         |
| LOGIN_NAME_MAX      | 17           | 256          | 255           | 9          | 9         |
| MAX_CANON           | 255          | 255          | 1 024         | 256        | 256       |
| MAX_INPUT           | 255          | 255          | 1 024         | 512        | 512       |
| NAME_MAX            | 255          | 255          | 255           | 255        | 8         |
| NGROUPS_MAX         | 1 023        | 65 536       | 16            | 16         | 16        |
| OPEN MAX            | 3 520        | 1 024        | 256           | 256        | 256       |
| PAGESIZE            | 4 096        | 4 096        | 4 096         | 8 192      | 8 192     |
| PAGE SIZE           | 4 096        | 4 096        | 4 096         | 8192       | 8 192     |
| PATH_MAX            | 1 024        | 4 096        | 1 024         | 1 024      | 1 024     |
| PIPE_BUF            | 512          | 4 096        | 512           | 5 120      | 5 120     |
| RE DUP_MAX          | 255          | 32 767       | 255           | 255        | 255       |
| STREAM MAX          | 3 520        | 16           | 20            | 256        | 256       |

| SYMLINK_MAX  | 1 024 | 无限制 | 255  | 1 024  | 1 024  |
| ------------ | ----- | ------ | ---- | ------ | ------ |
| SYMLOOP_MAX  | 32    | 无限制 | 32   | 20     | 20     |
| TTY_NAME_MAX | 255   | 32     | 255  | 128    | 128    |
| TZNAME MAX   | 255   | 6      | 255  | 无限制 | 无限制 |

图2-15配置限制的实例

J 注意，有些限制报告地并不正确。例如，在Linux中，SYMLOOP_MAX被报告成无限制，但是 J检查源代码后就会发现，实际上它在硬编码中有限制值，这一限制将猸环缺失的情况下遍历连续 '符号链接的教目限制为40 （参阅fs/namei.c中的follow_link函教）。

!    Linux中另一个潜在的不精确的来源是pathconf和fpathconf函教都是在C库函数中实

:现的，这些函数返回的配置限制依赖于底层的文件系统类型，因此如果你的文件系统不被C库熟 ;知的话，函数返困的是一个猜测值。

我们将在4.14节中看到，UFS是Berkeley快速文件系统的SVR4实现，PCFS是Solaris的 MS-DOS FAT文件系统的实现。

2.5.5不确定的运行时限制

前面已提及某些限制值可能是不确定的。我们遇到的问题是，如果这些限制值没有在头文件 <11!^1：3.&>中定义，那么在编译时也就不能使用它们。但是，如果它们的值是不确定的，那么 在运行时它们可能也是未定义的。让我们来观察两个特殊的情况，为一个路径名分配存储区，以 及确定文件描述符的数目。

[1](#footnote1)

\#!/usr/bin/awk -f

BEGIN {

printf {’’#include \"apue.h\"\n") printf{"#include <errno.h>\n") printf("#include <limits.h>\n") printf("\n")

printf ("static void pr_sysconf (char *, int)，- \n")

[2](#footnote2)

rintf("static void pr_pathconf{char *, char *, int);\n") printf ("\n")

printf("int\nn)

1-路径名

很多程序需要为路径名分配存储区，一般来说，在编译时就为其分配了存储匡，而且不同的 -4g-|程序使用各种不同的幻数（其中很少是正确的〉作为数组长度，如256、512、1 024或标准I/O常 I 量BUFSIZ。4.3BSD头文件<sys/param.h>m的常量MAXPATHLEN才是正确的值，但是很多

4.3BSD应用程序并未使用它。

POSIX.1试图用PATH_MAX值来帮助我们，但是如果此值是不确定的，那么仍是毫无帮助的。 图2-16程序是本书用来为路径名动态分配存储区的函数。

\#include "apue.h"

\#include <errno.h>

\#include <limits.h>

| #ifdefstatic#elsestatic#endif | PATH_MAXlong pathmax = PATH MAX; |                    |
| ----------------------------- | -------------------------------- | ------------------ |
| long                          | pathmax =                        | 0;                 |
| static                        | long                             | posix_version = 0; |
| static                        | long                             | xsi version = 0;   |

/* if PATH_MAX is indeterminate, no guarantee this is adequate */ #define PATH_MAX_GDESS 1024

char *

path_al1oc(size_t *sizep) " also return allocated size, if nonnull *7

char *ptr；

size_t size;

if (posix_version == 0}

posix_version = sysconf(_SC_VERSION);

if (xsi_version == 0)

xsi_version = sysconf{_SC_XOPEN_VERSION);

if (pathmax == 0) {    /* first time through */

errno =0;

if ((pathmax = pathconf_PC_PATH_MAX)) < 0) { if (errno == 0)

pathmax = PATH_MAX GUESS； /* it's indeterminate */

else

err_sys("pathconf error for _PC_PATH_MAX">;

} else {

pathmax++; /* add one since it's relative to root */

}

\*    Before POSIX.1-2001, we aren't guaranteed that PATH_MAX includes

\*    the terminating null byte. Same goes for XPG3.

*/

if ((posix_version < 200112L) && (xsi_version < 4)) size = pathmax + 1;

else

size = pathmax;

if ((ptr = malloc(size)) == NULL)

err_sys("malloc error for pathname");

if (sizep != NULL)

*sizep = size;

return (ptr);

J

图2-16为路径名动态地分配空间

如果＜limits.h＞中定义了常量PATH_MAX,那么就没有任何问题；如果未定义，则需调用 pathconf。因为pathconf的返回值是基于工作目录的相对路径名的最大长度，而工作目录是 其第一个参数，所以，指定根目录为第一个参数，并将得到的返回值加1作为结果值。如果 pathconf指明PATH.MAX是不确定的，那么我们就只能猜测某个值

对于PATE_MAX是否考虑到在路径名末尾有一个null字节这一点，2001年以前的POSIX.1 版本表述得并不清楚，出于安全方面的考虑，如果操作系统的实现符合某个先前版本的标准，但 并不符合Single UNIX Specification的任何版本（SUS明确要求在结尾处加一个终止null字节）， 则需要在为路径名分配的存储量上加1。

处理不确定结果情况的正确方法与如何使用分配的存储空间有关。例如，如果我们为getcwd 调用分配存储空间（返回当前工作目录的绝对路径名，见4.23节），但分配到的空间太小，则会 返固一个错误，并将errno设置为ERANGE。然后可调用realloc来增加分配的空间（见7.8 节和习题4.16)并重试。不断重复此操作，直到getcwd调用成功执行。

2.最大打开文件数

守护进程(daemon process,在后台运行且不与终端相连接的一种进程)中一个常见的代码序 列是关闭所有打开文件。某些程序中有下列形式的代码序列，这段程序假定在<373/932^!11上> 头文件中定义了常量NOFILE。

\#include <sys/param.h>;

for <i = 0; i < NOFILE; i++) close(i);

另外一些程序则使用某些<stdio.h>版本提供的作为上限的常量_NFILE。某些程序则直接将其 3ol上限值硬编码为20。但是，这些方法都不是可移植的。

I 我们希望用POSIX.1的0PEN.MAX确定此值以提高可移植性，但是如果此值是不确定的，则 仍然有问题，如果我们编写下列代码：

\#include <unistd.h>

for (i = 0; i < sysconf(_SC_OPEN_MAX); i++> close ⑴；

如果OPEN_MAX是不确定的，那么for循环根本不会执行，因为sysconf将返回_1。在这种情 况下，最好的选择就是关闭所有描述符直至某个限制值(如256)。如同上面的路径名实例一样， 虽然并不能保证在所有情况下都能正确工作，但这却是我们所能选择的最好方法。图2-17的程序 中使用了这种技术。

我们可以耐心地调用close,直至得到一个出错返回，但是从close (EBADF)出错返回 并不区分无效描述符和没有打开的描述符。如果使用此技术，而且描述符9未打开，描述符10 打开了，那么将停止在9上，而不会关闭10。dup函数(见3.12节)在超过了 OPEN_MAX时确 实会返回一个特定的出错值，但是用复制一个描述符两、三百次的方法来确定此值是一种非常极 端的方袪。

\#include "apue.h"

\#include <errno.h>

\#include    .h> #ifdef OPEN_MAX

static long openmax = OPEN_MAX;

\#else

static long openmax = 0;

\#endif

/*

\* If OPEN_MAX is indeterminate, this might be inadequate. */

ttdefine OPEN_MAX_GUESS 256 long

open_max(void)

if (openmax == 0) {    /* first time through */

errno



0;



if <(openmax = sysconf(_SC_OPEN_MAX)) < 0)    {

if (errno == 0)

openmax = OPEN_MAX_GUESS; /* it*s indeterminate */

else

err_sys("sysconf error for _SC_OPEN_MAX");

}

}

return(openmax);

图2-17确定文件描述符个数

某些实现返回LONG.MAX作为限制值，但这与不限制其值在效果上是相同的。Linux对 ATEXIT_MAX所取的限制值就属于此种情况（见图2-15），这将使程序的运行行为变得非常糟糕，

因此并不是一个好方法。    [52]

例如，我们可以使用Boume-again shell的内建命令ulimit来更改进程可同时打开文件 的最多个数。如果要将此限制值设置为在效果上是无限制的，那么通常要求具有特权（超级 用户）。但是，一旦将其值设置为无穷大，sysconf就会将LONG_MAX作为OPENJ4AX的限 制值报告。程序若将此值作为要关闭的文件描述符数的上限（如图2-17所示），那么为了试图 关闭2 147 483 647个文件描述符，就会浪费大量时间，实际上其中绝大多数文件描述符并未 得到使用。

支持 Single UNIX Specification 中 XSI 扩展的系统提供了 getrlimit（2）函数（见 7.11 节）。

它返回一个进程可以同时打开的描述符的最多个数。使用垓函数，我们能够检测出对于进程能够 打开的文件数实际上并没有设置上限，于是也就避开了这个问题。

! OPEN_MAX枝POSIX称为运行时不变值，这意味着在一个进程的生命周期中其值不应发生 !变化D但是在支持XSI扩展的系统上，可以调用setrlimit（2）函数（见7.11节）更改一个运

行进程的 OPEN_MAX 值（也可用 C shell 的 limit 或 Bourne shell、Bourne-昭aio shell、Debian j Almquist和Kom shell的ulimit命令更改这个值）。如果系统支持这种功能，则可以更改图2-17 ；中的函数，使得每次调用此函数时都会调用sysconf,而不只是在第一次调用此函数时调用 ! sysconfo

###### 2-6选项

图2-5列出了 POSIX.1的选项，并且2.2.3节讨论了 XSI的选项组。如果我们要编写可移植 的应用程序，而这些程序可能会依赖于这些可选的支持的功能，那么就需要一种可移植的方法来 判断实现是否支持一个给定的选项。

如同对限制的处理（见2.5节）一样，POSIX.1定义了 3种处理选项的方法。

（1）    编译时选项定义在＜111^3七|1.11＞中。

（2）    与文件或目录无关的运行时选项用sysconf函数来判断。

（3）    与文件或目录有英的运行时选项通进调用pathconf或fpathconf函数乘判断。

选项钮括了图2-5中第3列的符号以及图2-19和图2-18中的符号。如果符号常量未定义，则必

须使用sysconf、pathconf或fpathconf来判断是否支持垓选项。在这种情况下，这些函数的 参数前IPOSIX必须替换为_SClPC。対于以_XOPEN为前缀的常量，在构成name参数时

必须在其前放置+SC或-PC。例如，若常量_POSrX_RAWJTHREADS是未定义的，那么就可以将膽

参数设置为SC_RAW_THREADS,并以此调用sysconf来判断孩平台是否支持POSIX线程选项。如 若常量_XOPEN_UNIX是未定义的，那么就可以将参数设置为_SC_XOPEN_UNIX，并以此调用

\~53] sysconf来判断该平台是否支持XSI扩展。

对子每一个选项，有以下3种可能的平台支持状态。

(1)    如果符号常量没有定义或者定义值为-1,那么该平台在编译时并不支持相应选项。但是 有一种可能，即在已支持该选项的新系统上运行老的应用时，即使该选项在应用编译时未被支持， 但如今新系统运行时检查会显示该选项已被支持。

(2)    如果符号常量的定义值大于0,那么孩平台支持相应选项。

(3)    如果荷号常量的定义值为0，则必须调用sysconf、pathconf或fpathconf来判断 相座选项是否受到支持。

图2-18兑结了 pathconf和fpathconf使用的符号常量。除了图2-5中列出的选项之外， 图2-19总结了其他一些sysconf使用的未弃用的选项及它们的符号常量。注意，我们省略了与 实用命令相关的选项。

| 选项名                 | 说明                                | name参数             |
| ---------------------- | ----------------------------------- | -------------------- |
| POSIX CHQWN RESTRICTED | 使用chown是否是受限的               | _PC_CHOWN_RESTRICTED |
| _POSIX_NO_TRUNC        | 路径名长于NAME_MAX是否出错          | _PC_NO_TRUNC         |
| _POSIX_VDISABLE        | 若定义，可用此值禁用终端特殊字符    | _PC_VDISABLE         |
| _POSIX_ASYNC_IO        | 对相关联的文件是否可以使用异步I/O   | PC ASYNC 10          |
| _POSIX_PRIO_IO         | 对相关联的文件是否可以使用优先的I/O | _PC_PRIO_IO          |
| _POSIX_SYNC_TO         | 对相关联的文件是否可以使用同步I/O   | _PC_SYNC_IO          |
| POSIX2 SYMLINKS        | 目录中是否支持符号链接              | PC 2 SYMLINKS        |

图 2-18 pathconf 和 fpathconf 的选项及 name 参数

| 选项名                       | 说明                                            | name参数                  |
| ---------------------------- | ----------------------------------------------- | ------------------------- |
|                              |                                                 |                           |
| _POSIX_ASYNCHRONOUS_IO       | 此^^娥 POSIX 赫 I/O                             | _SC_ASYNCHRONOUS_IO       |
| _POSIX_BARRIERS              | 此实现是否支持屏障                              | _SC_BARRIERS              |
| _POSIX_CLOCK_SELECTION       | 此实现是否支持时钟选择                          | _SC_CLOCK_SELECTION       |
| _POSIX_JOB_CONTROL           | 此实现是否支持作业控制                          | _SC_J0B_C0NTR0L           |
| _POSIX_MAPPED_FILES          | 此实文件                                        | _SC_MAPPED_FILES          |
| _POSIX_MEMORY_PROTECTION     | 此实现是否支持存储保护                          | _SC_MEMORY_PROTECTION     |
| _POSIX_READEB_WRITER„LOCKS   | 此实现是否支蹄者-写者锁                         | _SC_READER_WRITER_LOCKS   |
| _POSIX_REALTIME_SIGNALS      | 此实现是否支持实时信号 此实现是否支持保存的设置 | _SC_REALTIME_SIGNALS      |
| _POSIX_SAVE D_IDS            | 用户ID和保存的设置组ID                          | _SC_SAVED_IDS             |
| _POSIX^SEMAPHORES            | 此实觀否碰POSDC信号量                           | _SC_SEMAPHORES            |
| _POSIX_SHELL                 | 雌现是否支持POSIX shell                         | _SC_SHELL                 |
| _POSIX_SPIN_LOCKS            | 此实现是否支持旋转锁                            | _SC_SPIN_LOCKS            |
| _POSIX_THREAD_SAFE_FUNCTIONS | 此实现是西支持线程安全函数                      | _SC_THREAD„SAFE_FUNCTIONS |
| _POSIX_THREADS               | 此实现是否支持线程 此实现是否支持基于超时的     | _SC_THREADS               |
| _POSIX_TIMEOUTS              | 变量选择函数                                    | _SC_TIMEOUTS              |
| „POSIX_TIMERS                | 此实现是否支持定时器                            | _SC_TIMERS                |
| _POSIX_VERSION               | POSIX. I 版本此实现是否支持XSI加密可            | ~SC_VERSI0N               |
| _XOPEN_CRYPT                 | 选组                                            | _SC_XOPEN_CRY PT          |

| _XOPEN_ | —REALTIME         | 此实现是否支持XSI实时选 项组     | _sc_ | _XOPEN_REALTIME         |
| ------- | ----------------- | -------------------------------- | ---- | ----------------------- |
| _XOPEN_ | .REALTIME_THREADS | 此实现是否支持实吋线程选 项组    | _sc. | _XOPEN_REALTXME_THREADS |
| _XOPEN, | ■SHM              | 此实现是否支持XSI共享存 储选项组 | _sc  | _XOPEN_SHM              |
| XOPEN.  | .VERSION          | XSI版本                          | sc   | XOPEN VERSION           |

图2-19 sysconf的选项及name参数

如同系统限制一样，芙于sysconf、pathconf和fpathconf如何处理选项，有如下几点 值得注意。

(1) _SC_VERSION的返回值表示标准发布的年(以4位数表示)、月(以2位数表示)。 该值可能是198808L、199009L、199506L或表示孩标准后续版本的其他值。与SUSv3 (POSIX.1 2001年版)相关连的值是200112L，与SUSv4 (POSIX.1 2008年版)相关连的值 是 200809L。

(2)    _SC_X0PEN_VERSI0N的返回值表示系统支持的XSI版本。与SUSv3相关联的值是600, 与SUSv4相关的值是700。

(3)    _SC_JOB_CONTROL、_SC_SAVED_IDS 以及_PC_VDISABLE 的值不再表示可选功能。 虽然XPG4和SUS早期版本要求支持选些选项，但从SUSv3起，不再需要这些功能，但这些符 号仍然被保留，以便向后兼容。

(4)    符合POSIX.1-2008的平台还要求支持下列选项：

•    _POSIX_ASYNCHRONOUS_IO

•    _POSIX_BARRIERS

•    _POSIX_CLOCK_SELECTION

•    _POSIX_MAPPED_FILES

•    _POSIX_MEMORY_PROTECTION

•    _POSIX_READER_WRITER_LOCKS

•    _POSIX_REALTIME_SIGNALS

•    _POSIX_SEMAPHORES

•    _POSIX_SPIN_LOCKS

•    _POSIX_THREAD_SAFE_FUNCTIONS

•    _POSIX_THREADS

•    _POSIX_TIMEOUTS

•    _POSIX_TIMERS

这常量定义成具有值200809L。相应的_SC符号同样是为了向后兼容而被保留下来的。

(5)    如果对指定的pathname或fd已不再支持此功能，那么_PCLCHOWN_RESTRICTED和 _PC_NO_TRUNC返回-1，而errno不变，往所有符合POSIX的系统中，返回值将大于0 (表示 垓选项被支持)；

(6)    _PC_CHOWN„RESTRICT引用的文件必须是一个文件或者是一个目录。如果是一个目录， 那么返回值指明该选项是否可应用于该目录中的各个文件。

(7)    _PC_NO_TRUNC和_卩0_2_5¥!^顶1<3引用的文件必须是一个目录。

(8)    PC NO TRUNC的返回值可用于目录中的各个文件名。

ITI (9) _PC_VDISABLE引用的文件必须是一个终端文件。

1    (10) _PC_ASYNC_IO, _?0_?1<10_10和_1<_5抓(：_10 引用的文件一定不能是一个目录。

图2-20列出了若干配置选项以及在本书所讨论的4个示例系统上的对应值。如果系统定 义了某个符号常量但它的值为-丨或0,但是相应的sysconf或pathconf调用返回的是-1， 就表示该项未被支持。可以看到，有些系统实现还没有跟上Single UNIX Specification的最新 版本。

| 限制                    | FreeBSD8.0   | Linux3.2.0 | Mac OS X 10.6.8 | Solaris 10 |        |
| ----------------------- | ------------ | ---------- | --------------- | ---------- | ------ |
| UFS文件系统             | PCFS文件系统 |            |                 |            |        |
|                         |              |            |                 |            |        |
| _POSIX_CHOWN_RESTRICTED | 1            | 1          | 200112          | I          | 1      |
| _POSIX_JOB_CONTROL      | 1            | 1          | 200112          | 1          | 1      |
| _POSIX_NO_TRUNC         | 1            | 1          | 200112          | I          | 不支持 |
| POSIX SAVED IDS         | 不支持       | 1          | 200112          | I          | 1      |
| _POSIX_THREADS          | 200112       | 200809     | 200112          | 200112     | 200112 |
| _POSIX_VDISABLE         | 255          | 0          | 255             | 0          | 0      |
| _POSIX_VERSION          | 200112       | 200809     | 200112          | 200112     | 200112 |
| _XOPEN_UNIX             | 不支持       | 1          | 1               | 1          | 1      |
| XOPEN VERSION           | 不支持       | 700        | 600             | 600        | 600    |

图2-20配置选项的实例

注意，当用于Solaris PCFS文件系统中的文件时，对于_PC_NO_TRUNC, pathconf返回-1。 PCFS文件系统支持DOS格式(软盘格式)，DOS文件名按DOS文件系统所要求8.3格式截断， 在进行此种操作时并无任何提示。

2.7功能测试宏

如前所述，头文件定义了很多POSIX.1和XSI符号。但是除了 POSIX.I和XSI定义外，大 多数实现在这些头文件中也加入了它们自己的定义。如果在编译一个程序时，希望它只与POSIX 的定义相关，而不与任何实现定义的常量冲突，那么就需要定义常量_POSIX_C_SOURCE。 —旦定义了 JPOSIX_C_SOURCE,所有POSIX.1头文件都使用此常量来排除任何实现专有的 定义。

! POS1X.1标准的早期版本定义7_P0SIX_S0URCE常量。在POSIX.1的2001版中，它被替换 ?为一P0SIX_C_S0URCEo

常量_POSIXJ：_SOURCE 及」(0£>£14_501} }^£ 被称为功能测试宏(feature test macro)。所有 [57]功能测试宏都以下划线开始。当要使用它们时，通常在cc命令行中以下列方式定义：

cc -D_POSIX_C_SOURCE=200809L file.c

这使得C程序在包括任何头文件之前，定义了功能测试宏。如果我们仅想使用POSIX.1定义，那 么也可将源文件的第一行设置为：

\#define_POSIX_C_SOURCE 200809L

为使SUSv4的XSI选项可由应用程序使用，需将常量_\0卯130[}1^£定义为700。除了让 XSI选项可用以外，就POS1X.1的功能而言，这与将_POSIX_C_SOURCE定义为200809L的作用

相同。

SUS将实用程序定义为C编译环境的接口。随之，就可以用如下方式编译文件:

C99 -D_X0PEN_S0URCE=700 file.c -o file

可以使用-std=c99选项在gcc的C编译器中启用1999 ISO C扩展，如下所示： gcc -D_XOPEN_SOURCE=700 -std=c99 file.c -o file

2.8基本系统数据类型

历史上，某些UNIX系统变量已与某些C数据类型联系在一起，例如，历史上主、次设备号 存放在一个16位的短整型中，8位表示主设备号，另外8位表示次设备号。但是，很多较大的系 统需要用多于256个值来表示其设备号，于是，就需要一种不同的技术。（实际上，Solaris用32 位表示设备号：14位用于主设备号，18位用于次设备号。）

头文件＜SyS/typeS.h＞中定义了某些与实现有关的数据类型，它们被称为基本系统数据类 型（primitive system data type）。述有很多这种数据类型定义在其他头文件中。在头文件中，这些 数据类型都是用C的typedef来定义的，它们绝大多数都以_t结尾。图2-21列出了本书将使 用的一些基本系统数据类型。

用这种方式定义了这些数据类型后，就不再需要考虑因系统不同而变化的程序实现细节。在 本书中涉及这些数据类型时，我们会说明为什么要使用它们。

| 类型         | 说明                                                 |
| ------------ | ---------------------------------------------------- |
| clock_t      | 时钟滴答计数器（进程时间）U.10节）                   |
| comp_t       | 压缩的时钟滴答（POSIX.1未定义；8.14节）              |
| dev_t        | 设备号（主和次）C4.24节）                            |
| fd_set       | 文件描述符集（14,4.1节）                             |
| fpos_t       | 文件位置（5.10节）                                   |
| gid_t        | 数值组ID                                             |
| ino_t        | i节点编号（4.14节）                                  |
| mode_t       | 文件类型，文件创建模式（4.5节）                      |
| nlink_t      | 目录项的链接计数（4.14节）                           |
| off_t        | 文件长度和偏移量（带符号的）（lseek, 3.6节〉         |
| pid_t        | 进程ID和进程组ID （带符号的）（8.2和9+4节）          |
| pthread_t    | 线程ID （11.3节）                                    |
| ptrdiff_t    | 两个指针相减的结果（带符号的）                       |
| rlim_t       | 资源限制（7.11节）                                   |
| sig_atomic_t | 能原子性地访问的数据类型（10.15节）                  |
| sigset_t     | 信号集（10.11节）                                    |
| size_t       | 対象（如字符串）长度（不带符号的）（3.7节）          |
| ssize_t      | 返回字节计数的函数（带符号的）（read、write, 3.7节〉 |
| time_t       | 日历时间的秒计数器（1.10节）                         |
| uid_t        | 数值用户U）                                          |
| wchar t      | 能表示所有不同的字符码                               |

图2-21 —些常用的基本系统数据类型

2.9标准之间的冲突

就整体而言，这些不同的标准之间配合得相当好。因为SUS基本说明和POSIX.1是同一个 东西，所以我们不对它们进行特别的说明，我们主要关注ISOC标准和POSIX.1之间的差别。它 们之间的冲突并非有意，但如果出现冲突，P0SDC1服从ISOC标准。然而它们之间还是存在着

—些差别的。

ISO C定义了 clock函数，它返回进程使用的CPU时间，返回值是clock„t类型值，但ISO C标准没有规定它的单位。为了将此值变换成以秒为单位，需要将其除以在＜1^1^.11＞头文件中 定义的CLOCKS_PER_SEC。P0SK.1定义了 times函数，它返回其调用者及其所有终止子进程 的CPU时间以及时钟时间，所有这些值都是clock_t类型值。sysconf函数用来获得每秒滴 答数，用于表示times函数的返回值。ISO C和POSIX.1用同一种数据类型（clock_t）来保

存对时间的测量，但定义了不同的单位。这种差别可以在Solaris中看到，其中clock返回微杪 数（CLOCK_PER_SEC是100万），而sysconf为每秒滴答数返回的值是100。因此，我们在使 用clock_t类型变量的时候，必须十分小心以免混淆不同的时间单位。

另一个可能产生冲突的地方是：在ISOC标准说明函数时，可能没有像POSIX.1那样严。在 POSIX环境下，有些函数可能要求有一个与C环境下不同的实现，因为POSIX环境中有多个进

58

I

59



程，而ISOC环境则很少考虑宿主操作系统。尽管如此，很多符合POSIX的系统为了兼容性 也会实现ISOC函数。signal函数就是一个例子。如果在不了解的情况下使用了 Solaris提 供的signal函数（希望编写可在ISO C环境和较早UNIX系统中运行的可兼容程序），那 么它提供了与POSIX. 1 sigaction函数不同的语义。第10章将对signal函数做更多说明。

##### 2.10小结

在过去25年多的时间里，UNIX编程环境的标准化己经取得了很大进展。本章对3个主要标 准——ISOC、POSIX和Single UNIX Specification进行了说明，也分析了这些标准对本书主要关 注的4个实现，即FreeBSD、Linux、Mac OS X和Solaris所产生的影响，这些标准都试图定义一 些可能随实现而更改的参数，但是我们已经看到这些限制并不完美。本书将涉及很多这些限制和 幻常量。

在本书最后的参考书目中，说明了如何获得这些标准的方法。

![img](UNIXaf83d8a7160b-5.png)



2.1    在2.8节中提到一些基本系统数据类型可以在多个头文件中定义，例如，在FreeBSD 8.0中， size_t在29个不同的头文件中都有定义。由于一个程序可能包含这29个不同的头文件， 但是ISOC却不允许対同一个名字进行多次typedef,那么如何编写这些头文件呢？

2.2    检查系统的头文件，列出实现基本系统数据类型所用到的实际数据类型。

2.3    改写图2-17中的程序，使其在sysconf为0PEN_MAX限制返回LONG_MAX时，避免进行 不必要的处理。
