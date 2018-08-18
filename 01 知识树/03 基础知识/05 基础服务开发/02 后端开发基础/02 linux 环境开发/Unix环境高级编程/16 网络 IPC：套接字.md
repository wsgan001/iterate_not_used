### 第16章

网络IPC:套鮮

##### 16.1弓I言

上一章我们考察了各种UNIX系统所提供的经典进程间通信机制（IPC）:管道、FIFO、消息 队列、信号量以及共享存储。这些机制允许在同一台计算机上运行的进程可以相互通信。本章将 考察不同计算机（通过网络相连）上的进程相互通信的机制：网络进程间通信（network IPC）O

在本章中，我们将描述套接字网络进程间通信接口，进程用该接口能够和其他进程通信，无 论它们是在同一台计算机上还是在不同的计算机上，实际上，这正是套接字接口的设计目标之一： 同样的接口既可以用于计算机间通信，也可以用于计算机内通信。尽管套接字接口可以采用许多 不同的网络协议进行通信，但本章的讨论限制在因特网事实上的通信标准：TCP/IP协议桟。

POSIX.1中指定的套接字API是基于4.4 BSD套接字接口的。尽管这些年套接字接口有些细 微的变化，但是当前的套接字接口与20世纪80年代早期4.2BSD所引入的接口很类似。

本章仅是一个套接字API的概述。Stevens、Fenner和Rudoff[2004】在有关UNIX系统网络编 [589]程的权威性文献中详细讨论了套接字接口。

16.2套接字描述符

套接字是通信端点的抽象。正如使用文件描述符访问文件，应用程序用套接字描述符访问套 接字。套接字描述符在UNIX系统中被当作是一种文件描述符。事实上，许多处理文件描述符的 函数（如read和write）可以用于处理套接字描述符。

为创建一个套接字，调用socket函数。

\#include <sys/socket.h>

int socket （int domain, int type, int protocol};

返回值：若成功，返回文件（套接字〉描述符：若出错，返回

魏domain （域）确定通信的特性，包括地址格式（在下一节详细描述）。图16-1总结了由 POSDC1指定的各个域。各个域都有自己表示地址的格式，而表示各个域的常数都以AF_开头， 意指地址族（address family）。

我们将在17.2节讨论UNIX域。大多数系统还定义了 AF_LOCAL域，这是AF_UNIX的别名= AF.UNSPEC域可以代表“任何”域。历史上，有些平台支持其他网络协议，如AF.IPX域代表 的NetWare协议族，但这些协议的域常数没有被POSIX.1标准定义。

| 域        | 描述         |
| --------- | ------------ |
| AF_INET   | IPv4因特网域 |
| AF_INET6  | IPv6因特网域 |
| AF_UNIX   | UNIX 域      |
| AF UPSPEC | 未指定       |

图164套接字通信域

参数妙e确定套接字的类型，进一步确定通信特征。图16-2总结了由POSIX.1定义的套接 字类型，但在实现中可以自由增加其他类型的支持。

| 类型                                        | 描述                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| SOCK_DGRAMSOCK_RAWSOCK_SEQPACKETSOCK STREAM | 固定长度的、无连接的、不可靠的报文传递IP协议的数据报接口(在POSDC.1中为可选)固定长度的、有序的、可靠的、面向连接的报文传递 有序的、可靠的、双向的、面向连接的字节流 |

图16-2套接字类型

参数pTOtocoZ通常是0,表示为给定的域和套接字类型选择默认协议。当对同一域和套接字 类型支持多个协议时，可以使用protocol选择一个特定协议。在AF_INET通信域中，套接字类型闹 SOCK_STREAM 的默认协议是传输接制协议(Transmission Control Protocol, TCP)。在 AF_INET 通信 域中，套接字类型SOCK_DGRAM的默认协议是UDP。图16-3列出了为因特网域套接字定义的协议。

| 协议                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| IPPROTO_IPIPPROTO_IPV6IPPROTO_ICMPIPPROTO_RAWIPPR0T0_TCPIPPROTOJJDP | IPv4网际协议IPv6网际协议(在POSX.1中为可选)因特网控制根文协议(IntemetControl Message Protocol) 原始IP数据包协议(在POSX.1中为可选)传输控制协议用户数据报协议(User Datagram Protocol) |

图16-3为因特网域套接字定义的协议

对于数据报(SOCK_DGRAM)接口，两个財等进程之间通信时不需要逻辑连接。只需要向对 等进程所使用的套接字送出一个报文。

因此数据报提供了一个无连接的服务。另一方面，字节流(SOCK_STREAM)要求在交换数 据之前，在本地套接字和通信的对等进程的套接字之间建立一个逻辑连接。

数据报是自包含报文。发送数据报近似于给某人邮寄信件。你能邮寄很多信，但你不能保证 传递的狄序，并且可能有些倩件会丢失在路上。每封信件包含接收者地址，使这封信件独立于所 有其他信件。每封信件可能送适不同的接收者。

相反，使用面向连接的协谈通信就像与対方打电话。首先，需要通过电话建立一个连接，连 接建立好之后，彼此能双向地通信。每个连接是端到端的通信链路。对话中不包含地址信息，就 像呼叫两端存在一个点对点虚拟连接，并且连接本身暗示特定的源和目的地。

SOCK_STREAM套接字提供字节流服务，所以应用程序分辨不出报文的界限。这意味着从 SOCK„STREAM套接字读数据时，它也许不会返回所有由发送进程所写的字节数。最终可以获得 发送过来的所有数据，但也许要通过若干次函数调用才能得到。

SOCK.SEQPACKET套接字和SOCK_STREAM套接字很类似，只是从该套接字得到的是基于 报文的服务而不是字节流服务。这意味着从SOCK„SEQPACKET套接字接收的数据量与对方所发 送的一致。流授制传输协议（Stream Control Transmission Protocol, SCTP）提供了因特网域上的 顺序数据包服务。

SOCK_RAW套接字提供一个数据报接口，用于直接访问下面的网络层（即因特网域中的IP __层）。使用这个接口时，应用程序负责构造自己的协议头部，这是因为传输协议（如TCP和UDP） 被绕过了。当创建一个原始套接字时，需要有超级用户特权，这样可以防止恶意应用程序绕过内

建安全机制来创建报文。

调用socket与调用open相类似。在两种情况下，均可获得用于I/O的文件描述符当不再需 要该文件描述符时，调用close来关闭肘文件或套接字的访问，并且释放孩描述符以便重新使用。

虽然套接字描述符本质上是一个文件描述符，但不是所有参数为文件描述符的函数都可以接 受套接字描述符。图164总结了到目前为止所讨论的大多数以文件描述符为参数的函数使用套接 字描述符时的行为。未指定和由实现定义的行为通常意味着该函数对套接字描述符无效。例如， lseek不能以套接字描述符为参数，因为套接字不支持文件偏移量的概念。

| 函数                                       | 使用套接字时的行为                                           |
| ------------------------------------------ | ------------------------------------------------------------ |
| close （见 3.5 节〉                        | 释放套接字                                                   |
| Dup 和 dup2 （见 3.12 节）                 | 和一般文件描述符一样复制                                     |
| fchdir （见 4.23 节）                      | 失败，并且将errno设置为ENOTDIR                               |
| fchomod （见 4.9 节'）                     | 未指定                                                       |
| fchown （见 4.11 节）                      | 由实现定义                                                   |
| fcntl （见 3.14 节）                       | 支持一些命令，包括 F_DUPFD、F_DUPFD_CLOEXEC、F_GETFD、 F_GETFL、F_GETOWN、FLSETFD、F_SETFL 和 F_SETOWN |
| Fdatasync 和 fsync （见 3.13 节）          | 由实现定义                                                   |
| f stat （见 4.2 节）                       | 支持一些stat结构成员，但如何支持由实现定义                   |
| f truncate （见 4.13 节）                  | 未指定                                                       |
| ioctl （见 3+15 节）                       | 支持部分命令.依献于底层设备驱动                              |
| lseek （见 3.6 节）                        | 由实现定义（通常失败时会将errno设为ESPIPE）                  |
| mmap （见 14.8 节）                        | 未指定                                                       |
| poll （见 14.<2 节）                       | 正常工作                                                     |
| Pread 和 pwrite （见 3.11 节）             | 失败时会将errno设为ESPIPE                                    |
| read （见 3.7 节）和 readv （见 14.6 节）  | 与没有任何标志位的recv （见16,5节）等价                      |
| select （见 14+4.1 节>                     | 正常工作                                                     |
| write （见 3.8市）和 writev （见 14.6 节） | 与没有任何标志位的send （见16.5节）等价                      |

图164文件描述符函数使用套接字时的行为 套接字通信是双向的。可以采用shutdown函数来禁止一个套接字的I/O。

\#include <sys/socket.h>

int shutdown （int sockfd, int how）；

返回值，若成功，返回0:若出错，返回-1

如果few是SHUT_RD （关闭读端），那么无法从套接字读取数据。如果/是SHOT_WR （关闭写 [592]端），那么无法使用套接字发送数据。如果tow是SHUT_RDWR,则既无法读取数据，又无法发送数据。

能够关闭（close） 一个套接字，为何还使用shutdown呢？这里有若干理由。首先，只有 最后一个活动引用关闭时，close才释放网络端点-这意味着如果复制一个套接字（如采用dup）,

要直到关闭了最后一个引用它的文件描述符才会释放这个套接字。而shutdown允许使一个套接 字处于不活动状态，和引用它的文件描述符数目无关。其次，有时可以很方便地关闭套接字双向 传输中的一个方向。例如，如果想让所通信的进程能够确定数据传输何时结束，可以关闭该套接 字的写端，然而通过该套接字读端仍可以继续接收数据。

##### 16.3寻址

上一节学习了如何创建和销毁一个套接字。在学习用套接字做一些有意义的事情之前，需要 知道如何标识一个目标通信进程，进程标识由两部分组成。一部分是计算机的网络地址，它可以 帮助标识网络上我们想与之通信的计算机：另一部分是该计算机上用蟪口号表示的服务，它可以 帮助标识特定的进程。

16.3.1字节序

与同一台计算机上的进程进行通信时，一般不用考虑字节序。字节序是一个处理器架构特性，

用于指示像整数这样的大数据类型内部的字节如何排序。图16-5显示了一个32位整数中的字节

是如何排序的。

大纗

| n    | n+1  | n+2  | n+3  |
| ---- | ---- | ---- | ---- |
| MSB  | 小纗 | LSB  |      |
| n+3  | n+2  | n+1  | n    |

MSB    LSB



如果处理器架构支持大端(big-endian)字节序，那么最大 字节地址出现在最低有效字节(Least Significant Byte, LSB)上。 小端(little-endian)字节序则相反：最低有效字节包含最小字节 地址。注意，不管字节如何排序，最髙有效字节(Most Significant Byte, MSB)总是在左边，最低有效字节总是在右边。因此，如 果想给一个32位整数赋值0x04030201，不管字节序如何，最

髙有效字节都将包含4,最低有效字节都将包含1。如果接下来想

将一个字符指针(cp)强制转换到这个整数地址，就会看到字节图I6-5 —个32位整数的字节序 序带来的不同。在小端字节序的处理器上，cp[0]指向最低有效字节因而包含1，cp[3]指向最高

有效字节因而包含4。相比较而言，在大端字节序的处理器上，CP[0]指向最髙有效字节因而包

含4, cp [3]指向最低有效字节因而包含1。图16-6总结了本文所讨论的4种平台的字节序。

| 操作系统       | 处理器架构       | 字节序 |
| -------------- | ---------------- | ------ |
| FreeBSD 8.0    | Intel Pentium    | 小端   |
| Linux 3.2.0    | Intel Core i5    | 小端   |
| Mac OSX 10.6.8 | Intel Core 2 Duo | 小端   |
| Solaris 10     | Sun SPARC        | 大端   |

图16-6测试平台的字节序

]    有些处理器可以配置成大端，也可以配篁成小端，因而使问题变得更让人困惑。

网络协议指定了字节序，因此异构计算机系统能够交换协议信息而不会被字节序所混淆。 TCP/IP协议栈使用大端字节序。应用程序交换格式化数据时，字节序问题就会出现。对于TCP/IP， 地址用网络字节序来表示，所以应用程序有时需要在处理器的字节序与网络字节序之间转换它 们。例如，以一种易读的形式打印一个地址时，这种转换很常见。

对于TCP/IP应用程序，有4个用来在处理器字节序和网络字节序之间实施转换的函数。

ttinclude <arpa/inet.h> uint32_t htonl<uint32_t

hostint32);

hostintI6)；

netint32);

netin(16);



返回值：以网络字节序表示的32位整数

返回值：以网络字节序表示的16位整数

返回值：以主机字节序表示的32位整数

返回值：以主机字节序表示的16位整数



uint!6_t htons(uintl6_t

uint32_t ntohl(uint32_t

uintl6_t ntohs(uintl6_t

h表示“主机”字节序，n表示“网络”字节序。1表示“长”（即4字节〉整数，s表示“短” （即4字节）整数。虽然在使用这些函数时包含的是＜arpa/inet.h＞*文件，但系统实现经常是 在其他头文件中声明这些函数的，只是这些头文件都包含在＜arpa/iriet.h＞中。对于系统来说，

®把这些函数实现为宏也是很常见的。

16.3.2地址格式

一个地址标识一个特定通信域的套接字端点，地址格式与这个特定的通信域相关。为使不同 格式地址能够传入到套接字函数，地址会被强制转换成一个通用的地址结构sockaddr：

struct sockaddr { sa_family_t char



sa_family; /* address family */

sa_data[];    /* variable-length address */

套接字实现可以自由地添加额外的成员并且定义sa_data成员的大小，例如，在Linux中，该 结构定义如下：

struct sockaddr {

sa_family_t    sa_family; /* address family */

char    sa_data[14] ； /* variable-length address */

}；

但是在FreeBSD中，该结构定义如下：

struct sockaddr { unsigned char sa_family_t char



sa_len； sa_family; sa_data[14];



/* total length */

/* address family */

/* variable-length address */

因特网地址定义在<netinet/in.h>头文件中。在IPv4因特网域(AF_INET)中，套接字 地址用结构sockaddr_in表示：

struct in_addr {

in_addr_t    s_ addr;    /* IPv4 address */

}；

struct sockaddr_in { sa_family_t in_port_t

sin_family; /* address family */ sin_port; /* port number */



struct in_addr sin_addr;    /* IPv4 address */

}i

数据类型in_port_t定义成uintl6_t。数据类型in_addr_t定义成uint32_t。这些整数类 型在＜Stdint.h＞中定义并指定了相应的位数，

与AF_INET域相比较，IPv6因特网域（AF_INET6）套接字地址用结构sockaddr_in6表示：

s 6_addr[16];



/* IPv6 address



struct_in6_addr { uint8_t

In

struct sockaddr_in6 {

| sa_family_t                                   | sin6_family;                     | /* address family */ |           |      |
| --------------------------------------------- | -------------------------------- | -------------------- | --------- | ---- |
| in_port_t                                     | sin6_port;                       | /* port number */    |           |      |
| uint32_t                                      | sin6_flowinfo;                   | /* traffic class and | flow info | */   |
| struct in6_addr                               | sin6_addr;                       | /* IPv6 address*/    |           |      |
| uint32_t1;                                    | sin6_s c ope_id;                 | /* set of interfaces | for scope | */   |
| 这些都是Single UNIX Specification要求的定义。 | 每个实现可以自由添加更多的字段。 | 例如，               |           |      |
| Linux 中，sockaddr_in 定义如下：              |                                  |                      |           |      |
| struct sockaddr_in {                          |                                  |                      |           |      |
| sa_family_t                                   | sin_family;    I                 | '* address family */ |           |      |
| in_port_t                                     | sin_port;    !                   | '★ port number */    |           |      |
| struct in6_addr                               | sin6_addr;    i                  | '* IPv4 address */   |           |      |
| unsigned char                                 | sin_zero[8];    I                | '* filler */         |           |      |

其中成员sin.zero为填充字段，应该全部被置为0。

注意，疼管sockaddr_in与sockaddr„in6结构相差比较大，但它们均被强制转换成

sockaddr结构输入到套接字例程中。在17.2节，将会看到UNIX域套接字地址的结构与上述两 个因特网域套接字地址格式的不同。

有时，需要打印出能被人理解而不是计算机所理解的地址格式。BSD网络软件包含函数

inet_addr和inet_ntoa,用于二进制地址格式与点分十进制字符表示（a.b.c.d）之间的相互 转换D但是这些函数仅适用于IPv4地址。有两个新函数inet_ntop和inet_pton具有相似的 功能，而且同时支持IPv4地址和IPv6地址，

^include <arpa/inet.h>

const char *inet_ntop{int domain, const void ^restrict addr, char * restrict str, socklen_t size);

返回值：若成功，返回地址字符串指针；若出错，返回NULL

int inet_pton (int domain, const char * restrict str, void * restrict addr}，-

返回值：若成功，返回1;若格式无效.返回0;若出错，返回-I

函数inet.ntop将网络字节序的二进制地址转换成文本字符串格式。inet_ptOn将文本字 符串格式转换成网络字节序的二进制地址。参数也Wflfn仅支持两个值：AF_INET和AF_INET6。

对于inet^ntop,参数论e指定了保存文本字符串的缓冲匡（价）的大小。两个常数用于 简化工作：工NET_ADDRSTRLEN定义了足够大的空间来存放一个表示IPv4地址的文本字符串； INET6 ADDRSTRLEN定义了足够大的空间来存放一个表示IPv6地址的文本字符串。对于

inet_pton,如果domain是AF_INET,则缓冲区addr需要足够大的空间来存放一个32位地址， ®如果cfomaZrt是AFJCNET6，则需要足够大的空间来存放一个128位地址。

16.3.3地址查询

理想情况下，应用程序不需要了解一个套接字地址的内部结构。如果一个程序简单地传递一 个类似于sockaddr结构的套接字地址，并且不依赖于任何协议相关的特性，那么可以与提供相 同类型服务的许多不同协议协作。

历史上，BSD网络软件提供了访问各种网络配置信息的接口。6.7节简要讨论了网络数据 文件和用来访问这些文件的函数。本节将更详细地讨论一些细节，并且引入新的函数来査询寻 址信息。

这些函数返回的网络配置信息被存放在许多地方。这个信息可以存放在静态文件(如 /etc/hosts和/etc/services)中，也可以由名字服务管理，如域名系统(Domain Name System. DNS)或者网络信息服务(NetworkInformation Service, NIS)。无论这个信息放在何处， 都可以用同样的函数访问它=

通过调用gethostent,可以找到给定计算机系统的主机信息。

\#include <netdb.h>

struct hostent *gethostent(void);

返回值：若成功，返回指针：若出错，返回NULL

void sethostent (int stayopen); void endhostent{void};

如果主机数据库文件没有打开，gethostent会打开它。画数gethostent返回文件中的下一 个条目。函数sethostent会打开文件，如果文件已经被打开，那么将其回绕。当做例湖参数设 置成非0值时，调用gethostent之后，文件将依然是打开的。函数endhostent可以关闭文件。

当gethostent返回时，会得到一个指向hostent结构的指针，该结构可能包含一个静态 的数据缓冲区，每次调用gethostent,缓冲区都会被覆盖。hostent结构至少包含以下成员：

struct hostent{

| char | *h_name;       | /* name of host */                          |
| ---- | -------------- | ------------------------------------------- |
| char | **h_aliases;   | /* pointer to alternate host name array */  |
| int  | h_addrtype;    | /* address type */                          |
| int  | h_length;      | /* length in bytes of address */            |
| char | **h_addr_list; | /* pointer to array of network addresses *. |

返回的地址采用网络字节序。

另外两个函数gethostbyname和gethostbyaddr，原来包含在hostent函数中，现在 [5?7]则被认为是过时的。SUSV4已经删除了它们。马上将会看到它们的替代函数。

能够采用一套相似的接口来获得网络名字和网络编号=

\#include <netdb.h>

struct netent *getnetbyaddr (uint32_t net, int type}; struct netent *getnetbyname(const char *name);

struct netent *getnetent(void);

3个函数的返回值：若成功，返回指针；若出错，返回NULL

void setnetent (int stayopen); void endnetent(void);

netent结构至少包含以下字段：

struct netent { char *n_name; char **n_aliases; int    n_addrtype;

uint32_t n_net;



/* network name */

/* alternate network name array pointer */ /* address type */

/* network number */

｝；

网络编号按照网络字节序返回。地址类型是地址族常量之一(如AF_INET)。 我们可以用以下函数在协议名字和协议编号之间进行映射。

\#include <netdb.h>

struct protoent *getprotobyname(const char *name); struct protoent *getprotobynumber (int proto}; struct protoent *getprotoent(void);

3个函数的返回值：若成功，返回指针；若出错，返回NULL

void setprotoent (int stayopen}; void endprotoent(void);

POSIX.1定义的protoent结构至少包含以下成员:

struct protoent { char *p_name; char **p_ aliases; int p_proto;



/* protocol name *Z

/* pointer to altername protocol name array */ /* protocol number */

I；

服务是由地址的端口号部分表示的。每个服务由一个唯一的众所周知的端口号来支持。可以 使用函数getservbynaine将一个服务名映射到一个端口号，使用函数getservbyport将一|5卯1 个端口号映射到一个服务名，使用函数getservent顺序扫描服务数据库。

\#include <netdb.h>

struct servent *getservbyname (const char *name, const char * proto); struct servent *getserbyport (int port, const char * proto、； struct servent *getservent(void);

3个函数的返回值，若成功，返回指针，若出错，返回NULL

void setservent (int stayopen)； void endservent(void};

servent结构至少包含以下成员:

struct servent{ char *s_name;



/* service name */



char **s_aliases; int s_port; char *s_proto;



/* pointer to alternate service name array */ /* port number */

/* name of protocol */

POSIX.1定义了若干新的函数，允许一个应用程序将一个主机名和一个服务名映射到一个地 址，或者反之。这些函数代替了较老的圉数gethostbyname和gethostbyaddr。

getaddrinfo函数允评将一个主机名和一个服务名映射到一个地址。

\#include <sys/socket.h>

\#include <netdb.h>

int getaddrinfo （const char *restrict host,

const char * restrict service,

const struct addrinfo * restrict hint,

struct addrinfo “restrict rej）;

返回值：若成功，返回0:若出镑，返回非0错误码

void freeaddrinfo （struct addrinfo *ai）;

需要提供主机名、服务名，或者两者都提供。如果仅仅提供一个名字，另外一个必须是一个 空指针。主机名可以是一个节点名或点好格式的主机地址。

getaddrinfo函数返回一个链表结构addrinfo。可以用freeaddrinfo来释放一个或 ®多个这种结构，这取决于用ai_next字段链接起来的结构有多少。

addrinfo结构的定义至少包含以下成员：

struct addrinfo { int int int int

socklen_t struct sockaddr char

struct addrinfo



ai_flags;

ai_family;

ai_socktype;

ai_protocol;

ai_addrlen;

*ai_addr；

*ai_canonname;

*ai_next;



/* customize behavior */

/* address family */

/* socket type */

/* protocol */

/* length in bytes of address */ /* address */

/* canonical name of host */

/* next in list *Z

};

可以提供一个可选的W时来选择符合特定条件的地址。A/扣是一个用于过墟地址的模板，包 括ai_family、ai_flags、ai_protocol和ai_socktype字段。剩余的整数字段必须设置 为0,指针字段必须为空。图16-7总结了 ai_flags字段中的标志，可以用这些标志来自定义如 何处理地址和名字。

| 标志                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| AI_ADDRCONFIGAI_ALLAI_CANONNAMEAI_NUMERICHOSTAI_NUMERICSERVAI_PASSIVEAI V4MAPPED | 査询配置的地址类型（IPv4或IPv6）査找IPv4和IPv6地址（仅用于AI_V4MAPPED）需要一个规范的名字（与别名相对）以数字格式指定主机地址，不翻译 将服务指定为数字端口号，不翱译 套接字地址用于监听绑定如没有找到IPv6地址，返回映射到IPv6格式的IPv4地址 |

图16-7 addrinfo结构的标志

如果getaddrinfo先败，不能使用perror或strerror乘生成错误消息，而是要调用 gai_strerror将返回的错误码转换成错误消息。

\#include <netdb.h>

const char *gai_strerror （int error};

返回值：指向描述错误的字符串的指针

getnameinfo函数将一个地址转换成~个主机名和一个服务名。

\#include <sys/socket.h>

\#include <netdb.h>

int getnameinfo {const struct sockaddr * restrict addr, socklen_t alert, char *restrict host, socklen_t hostlen, char * restrict service, socklen_t servlen, int flags);

|600|



返回值：若成功，返回0:若出错，返回非0值

套接字地址被翻译成一个主机名和一个服务名。如果如对非空，则指向一个长度为 tarfert字节■的缓冲区用于存放返回的主机名。同样，如果ww'ce非空，则指向一个长度为jerv/en 字节的缓冲区用于存放返回的主机名。

yZfl识参数提供了一些控制翻译的方式。图16-8总结了支持的标志。

| 标志                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| NI_DGRAMNI_NAMEREQDNI_NOFQDNNI_NUMERICHOSTNI_NUMERICSCOPENI NUMERICSERV | 服务基于数据报而非基于流 如果找不到主机名，将其作为一个错误对待 对于本地主机，仅返回全限定域名的节点名部分 返回主机地址的数字形式，而非主机名 对于IPv6,返回范围1D的数字形式，而非名字 返回服务地址的数字形式（即端口号），而非名字 |

图16-8 getnameinfo函数的标志

实例

图16-9说明了 getaddrinfo函数的使用方法。

^include "apue.h"

\#if defined(SOLARIS) ♦include <netinet/in.h> #endif

♦include <netdb.h> #include <arpa/inet.h> #if defined(BSD) #include <sys/socket.h> #include <netinet/in.h> #endif

void

print_family(struct addrinfo *aip) {

printf(" family "); switch (aip->ai_family) f case AF INET:

printf finef); break;

case AF„INET6:

printf("inet6"); break;

case AF_UNIX:

printf("unix"); break;

画 case AF_UNSPEC:

printf("unspecified"); break;

default:

printf("unknown")；

void

print_type(struct addrinfo *aip)

{

printf (" type ;

switch (aip->ai_socktype) {

case SOCK_STREAM:

printf("stream"); break;

case SOCK_DGRAM:

printf("datagram"); break;

case SOCK_SEQPACKET:

printf C'seqpacket"); break;

case SOCK_RAW:

printf("raM-); break;

default:

printf("unknown (%d)", aip->ai_socktype);

void

print_protocol{struct addrinfo *aip)

printf C protocol "); switch (aip->ai_protocol) { case 0:

printf ("default'” ； break;

case IPPROTO_TCP: printf("TCP"); break;

case IPPROTO_UDP: printf("UDP"); break;

case IPPROTO_RAW: printf{"raw"); break;

default:

printf{"unknown (%d)", aip->ai_protocol};

}    1    画

void

print_flags(struct addrinfo *aip)

{

printf{"flags"); if (aip->ai_flags == 0) {

printf(" 0");

} else {

if (aip->ai_flags & AI_PASSIVE> printf(" passive")；

if (aip->ai_flags & AI_CANONNAME) printf(" canon");

if {aip->ai_flags & AI_NUMERICHOST) printf(” numhost")；

if (aip->ai_flags & AI_NUMERICSERV) printf(" numserv")；

if (aip->ai_flags & AI_V4MAPPED) printf(" v4mapped");

if (aip->ai_flags & AI_ALL) printf{" all");

1

int

main(int argc, char *argv[])

| struct addrinfo    | *ailist, *aip;         |
| ------------------ | ---------------------- |
| struct addrinfo    | hint/                  |
| struct sockaddr_in | *sinp；                |
| const char         | *addr;                 |
| int                | err；                  |
| char               | abuf[INET_ADDRSTRLEN]; |
| if (argc != 3)     |                        |



err_quit("usage: is nodename service", argv[0]}； hint.ai_flags = AI^CANONNAME; hint.ai_family = 0;

hint.ai_socktype = 0;

hint.ai_protocol = 0；

hint.ai_addrlen = 0;

hint.ai_canonname = NULL;

hint.ai_addr = NULL;

hint.ai_next = NULL;

if ((err = getaddrinfo(argv[l], argv[2], fihint, &ailist)) != 0) err_quit{"getaddrinfo error: %s”， gai_strerror(err));

for (aip = ailist; aip != NULL; aip = aip->ai_next) { print—flags(aip); print_family(aip); print_type{aip}; print_protocol (aip) '•

printf ("\n\thost %s’’，aip->ai_canonname?aip->ai_canonname:

画    if (aip->ai_family == AF_INET)(

sinp = (struct sockaddr_in *)aip->ai_addr； addr = inet_ntop(AF_INET, &sinp->sin_addr, abuf,

XNET_ADDRSTRLEN);

printf(" address %s", addr?addr:"unknown"); printf(" port %d", ntohs(sinp->sin_port));

}

printf{M\nM);

}

exit (0)；

图16-9打印主机和服务信息

这个程序说明了 getaddrinfo函数的使用方法。如果有多个协议为指定的主机提供给定的服 务，程序会打印出多条信息。本实例仅打印了与IPv4—起工作的那些协议（ai_family为AF_INET） 的地址信息。如果想将输出限制在AF_INET协议族，可以在提示中设置ai_family字段0

在一个测试系统上运行这个程序时，得到了以下输出：

$ ./a.out harry n£a

flags canon family inet type stream protocol TCP host harry address 192.168.1.99 port 2049

flags canon family inet type datagram protocol UDP

host harry address 192.168.1.99 port 2049    ■_

16.3.4将套接字与地址鍊

将一个客户端的套接字关联上一个地址没有多少新意，可以让系统选一个默认的地址。然而， 对于服务器，需要给一个接收客户端请求的服务器套接字关联上一个众所周知的地址。客户端应 有一种方法来发现连接服务器所需要的地址，最简单的方法就是服务器保留一个地址并且注册在 /etc/services或者某个名字服务中。

使用bind函数来关联地址和套接字。

\#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr, socklen_t ien);

返回值：若成功，返回0;若出错，返回-I

对于使用的地址有以下一些限制。

•在进程正在运行的计算机上，指定的地址必须有效；不能指定一个其他机器的地址，

[604]    •地址必须和创建套接字时的地址族所支持的格式相匹配。

•地址中的端口号必须不小于1024,除非该进程具有相应的特权（即超级用户）。

• 一般只能将一个套接字端点绑定到一个给定地址上，尽管有些协议允许多重绑定。

对于因特网域，如果指定IP地址为INADDR_ANY （＜netinet/in.h＞中定义的〉，套接字端

点可以被绑定到所有的系统网络接口上，这意味着可以接收这个系统所安装的任何一个网卡的数 据包。在下一节中可以看到，如果调用connect或listen，但没有将地址绑定到套接字上， 系统会选一个地址绑定到套接字上。

可以调用getsockname函数来发现绑定到套接字上的地址。

\#include <sys/socket.h>

int getsockname (int socirfd, struct sockaddr ^restrict addr, socklen_t *restrict alenp};

返回值：若成勒，返回0:若出错，返回-1

调用getsockname之前，将alenp设置为一个指向整数的指针，该整数指定缓冲医 sockaddr的长度。返回时，该整数会被设置成返回地址的大小，如果地址和提供的缓冲区长 度不匹配，地址会被自动截断而不报错。如果当前没有地址绑定到该套接字，则其结果是未定 义的。

如果套接字已经和对等方连接，可以调用getpeername函数来找到对方的地址。

\#include <sys/socket.h>

int getpeername （int sock'd, struct sockaddr * restrict addr, socklen_t * restrict alenp};

返回值：若成功，返回0;若出镑，返回-1

除了退回対等方的地址，函数getpeername和getsockname —样。

###### 16.4建立连接

如果要处理一个面向连接的网络服务（SOCK_STREAM或SOCK_SEQPACKET）,那么在开始 交换数据以前，需要在请求服务的进程套接字（客户端）和提供服务的进程套接字（服务器）之 间建立一个连接。使用connect函数来建立連接。

\#±nclude <sys/socket.h>

int connect (int socfrfd, const struct sockaddr *addr, socklen_t len);

返回值：若成功，返回0:若出错，返回-1

在connect中指定的地扯是我们想与之通信的服务器地址。如果如c蜘没有绑定到一个地 址，connect会给调用者绑定一个默认地址。

当尝试连接服务器时，出于一些原因，连接可能会失败。要想一个连接请隶成功，要连接的 计算机必须是开启的，并且正在运行，服务器必须绑定到一个想与之連接的地址上，并且服务器 的等待连接队列要有足够的空间（后面会有更详细的介绍）。因此，应用程序必须能够处理 connect返回的错误，这些错误可能是由一些瞬时条件引起的。

I实例

图16-10显示了一种如何处理瞬时connect错误的方注。如果一个服务器运行在一个负载 很重的系统上，就很有可能发生这些错误。

\#include "apue.h"

\#include <sys/socket.h>

^define MAXSLEEP 128

int

connect_retry(int sockfd, const struct sockaddr *addr7 socklen_t alen>

int numsec;

\* Try to connect with exponential backoff.

*/

for (numsec =1; numsec <= MAXSLEEP; numsec «= 1) { if (connect{sockfd, addr, alen) = 0) {

/[1](#bookmark22) [2](#bookmark23)

\* Connection accepted.

*/

return(0);

J

★ Delay before trying again. */

if (numsec <= MAXSLEEP/2) sleep(numsec);

J

return(-1);

图16-10支持重试的connect

这个函数展示了指数补楼(exponential backoff)算法。如果调用connect失败，进程会休 眠一小段时间，然后进入下次搪环再次尝试，每次循环休眠时间会以指数级増加，直到最大延迟

[606]为2分钟左右。

然而图16-10中的代码存在一个问题：代码是不可移植的。它在Linux和Solaris上可以工作， 但是在FreeBSD和Mac OS X上却不能按预期工作。在基于BSD的套接字实现中，如果第一次连 接尝试失败，那么在TCP中继续使用同一个套接字描述符，接下来仍旧会失败。这就是一个协议 相关的行为从(协议无关的)套接字接口中显露出来变得应用程序可见的例子，这些都是历史原 因，因此Single UNIX Specification瞀告，如果connect失败，套接字的状态会变成未定义的。

因此，如果connect失败，可迁移的应用程序需要关闭套接字。如果想重试，必须打开一 个新的套接字。这种更易于迁移的技术如图16-11所示。

if (connect(fd, addr, alen) == 0) { /*

\* Connection accepted.

*/

return (fd) ,• f

close (fd);

\* Delay before trying again.

*/

if (numsec <= MAXSLEEP/2) sleep{numsec);

}

return(-1);

图1641可迁移的支持重试的连接代码

需要注意的是，因为可能要建立一个新的套接字，给COnneCt_retry函数传递一个套接字 描述符参数是没有意义。我们现在返回一个已连接的套接字描述符给调用者，而并非返回一个表 示调用成功的值。    ,®

如果套接字描述符处于非阻塞模式(该模式将在16.8节中进一步讨论)，那么在连接不能马 上建立时，connect将会返回-1并且将errno设置为特殊的错误码EINPROGRESS。应用程序 可以使用poll或者select来判断文件描述符何时可写。如果可写，连接完成。

connect函数述可以用于无连接的网络服务(SOCK_DGRAM)n这看起来有点矛盾，实际上却是一 个不错的选择。如果用SOCK_DGRAM套接字调用connect，传送的报文的目标地址会设置成connect 调用中所指定的地址，这样每次传送报文时就不需要再提供地址。另外，仅能接收来自指定地址的报文。

服务器调用listen函数来宣告它愿意接受连接请求。

\#include <sys/socket.h>

int listen(int sock'd, int backlog);

返回值：若成功•返回0:若出镑•返回-1

参数ZwcWog提供了一个提示，提示系统该进程所要入队的未完成连接请求数量。其实际值 由系统决定，但上限S＜sys/socket .h＞中的SOMAXCONN指定。

Solaris系统忽略了＜sys/socket.h＞中的SOMAXCONN。具体的最大值取决于每个协议的实 j现。对于TCP,其默认值为128。

一旦队列满，系统就会拒绝多余的连接请求，所以backlog的值应该基于服务器期望负载和 处理量来选择，其中处理量是指接受连接请求与启动服务的数量。

一旦服务器调用了 listen，所用的套接字就能接收连接请求。使用accept函数获得连接 请求并建立连接，

\#include <sys/socket.h>

int accept (int sock'd, struct sockaddr * restrict addr, socklen_t *restrict len、；

返回值：若成功，返回文件(套接字)描述符：若出错> 返回-1

函数accept所返回的文件描述符是套接字描述符，该描述符连接到调用connect的客户 端。这个新的套接字描述符和原始套接字（舰够/）具有相同的套接字类型和地址族。传给accept 的原始套接字没有关联到这个连接，而是继续保持可用状态井接收其他连接请求。

如果不关心客户端标识，可以将参数flt/咖和设为NULL。否则，在调用accept之前， r—n将《也>•参数设为足够大的缓冲区来存放地址，并且将⑻指向的整数设为这个缓冲区的字节大小，

返回时，accept会在缓冲区填充客户端的地址，并且更新指向知《的整数来反映该地址的大小。 如果没有连接请求在等待，accept会阻塞直到一个请求到来。如果如c坊/处于非阻塞模式，

accept 会返回-1，并将 errno 设置为 EAGAIN 或 EWOULDBLOCK。 j    本文中讨论的所有平台都将EAGAIN定义为EWOULDBLOCK。

如果服务器调用accept,并且当前没有连接请求，服务器会阻塞直到一个请求到来D另外， 服务器可以使用poll或select来等待一个请求的到来。在这种情况下，一个带有等待连接请 求的套接字会以可读的方式出现。

实例

图16>12显示了一个函数，可以用来分配和初始化套接字供服务器进程使用。

\#include "apue.h"

\#include <errno.h>

番include <sys/socket.h>

int

initserver(int type, const struct sockaddr *addr, socklen_t alen, int qlen)

{

int fd; int err = 0;

if {(fd = socket(addr->sa_family, type, 0)J < 0) return(-1);

if (bindffd, addr, alen) < 0) goto errout;

if (type == SOCK_STREAM || type == SOCK_SEQPACKET) { if (listen(fd, qlen} < 0)

goto errout;

}

return(fd);

errout:

err = errno; close(fd)； errno = err; return(-1);

}

图16-12初始化一个套接字端点供服务器进程使用 可以看到，TCP有一些奇怪的地址复用规则，这使得这个例子不完备。图16-22显示了有关

[609]这个函数的另一个版本，可以绕过这些规则，解决此版本的主要缺陷。

[1](#footnote1)

差include "apue.h"

^include <sys/socket.h>

\#define MAXSLEEP 128

int

connect_retry(int domain, int type, int protocol,

const struct sockaddr [2](#bookmark23)addr, socklen_t alen)

t

int numsec, fd;

/[2](#bookmark23)

[2](#footnote2)

Try to connect with exponential backoff.

*/

for (numsec = 1; numsec <= MAXSLEEP; numsec «= 1) { if ((fd = socket(domain, type, protocol)) < 0)

return(-1);


###### 16.5数据传输

既然一个套接字端点表示为一个文件描述符，那么只要建立连接，就可以使用read和write 来通过套接字通信。回忆前面所讲，通过在connect函数里面设置默认对等地址，数据报套接 字也可以被“连接”。在套接字描述符上使用read和write是非常有意义的，因为这意味着可 以将套接字描述符传速给那些原先为处理本地文件而设计的函数。而且还可以安排将套接字描述 符传递给子进程，而该子进程执行的程序并不了解套接字。

尽管可以通过read和write交换数据，但这就是这两个函数所能做的一切。如果想指定选 项，从多个客户端接收数据包，或者发送带外数据，就需要使用6个为数据传递而设计的套接字 函数中的一个。

3个函数用来发送数据，3个用于接收数据。首先，考查用于发送数据的函数。

最简单的是send,它和write很像，但是可以指定标志来改变处理传输数据的方式。

\#include <sys/socket.h>

ssize_t send (int sockfd, const void *buf, size_t nbytes, int flags);

返回值：若成功，返回发送的字节数：若出错，返回-1

类似write,使用send时套接字必须己经连接、参数buf和nbytes的含义与write中的 一致。

然而，与write不同的是，send支持第4个参数yZogs。3个标志是由Single UNIX Specification 定义的，但是具体系统实现支持其他标志的情况也是很常见的。图16-13总结了这些标志。

| 标志                                                         | 描述                                                         | POSK.1 | FreeBSD8.0 | Linux3.2.0 | Mac OSX 10.6.8 | Solaris10 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------ | ---------- | ---------- | -------------- | --------- |
| MSG_CONFIRMMSG_DONTROUTEMSG_DONTWAITMSG_EOFMSG_EORMSG_MOREMSG_NOSIGNALMSG_OOB | 提供链路层反馈以保持地址映 射有效勿将数据包路由出本地网络 允许非阻塞操作（等价于使用 O_NONBLOCK）发送数据后关闭套接字的发送端 如果协议支持.标记记录结束 允许写更多数据 在写无连接的套接字时不产生 SIGPIPE 信号如果协议支持，发送带外数据 （见16.7节） | •      |            | •          | •              | •         |

图16-13 send套接字调用标志

即使send成功返回，也并不表示连接的另一端的进程就一定接收了数据。我们所能保证的 只是当send成功返回时，数据已经被无错误地发送到网络驱动程序上。

对于支持报文边界的协议，如果尝试发送的单个报文的长度超过协议所支持的最大长度，那 么send会失败，并将errrw设为EMSGSIZE，对于字节流协议，send会阻塞直到整个数据传 输完成。函数sendto和send很类似。区别在于sendto可以在无连接的套接字上指定一个目 标地址。

♦include <sys/socket,h>

ssize_t sendto(int sockfd, const void *byf, size_t nbytes, int flags, const struct sockaddr *destaddr, socklen_t destlen);

返回值：若成功，返回发送的字节数：若出错，返回-I

对于面向连接的套接字，目标地址是被忽略的，因为连接中隐含了目标地址。对于无连接的套接字， 除非先调用connect设置了目标地址，否则不能使用send。sendto提供了发送报文的另一种方式。

通过套接字发送数据时，还有一个选择。可以调用带有msghdr结构的sendmsg来指定多 重缓冲区传输数据，这和writev函数很相似（见14.6节）-

^include <sys/socket.h>

ssize_t sendmsg (int sock'd, const struct msghdr *msg, int flags');

返回值：若成功，返回发送的字节数；若出错，返回-1

POSIX.1定义了 msghdr结构，它至少有以下成员:

struct msghdr {

void    *msg_name;

socklen_t    msg_namelen;

struct iovec *msg_iov; int    msg_iovlen;

void    *msg_control;

socklen_t    msg_controllen;

int    msg^flags;



/* optional address */

/* address size in bytes */

/* array of I/O buffers */

/* number of elements in array */ /* ancillary data */

/* number of ancillary bytes */

/* flags for received message */



610

611



在14.6节中可以看到iovec结构。在17.4节中可以看到辅助数据的使用。 函数recv和read相假，但是recv可以指定标志来控制如何接收数据。

\#include <sys/socket.h>

ssize_t recv （int sodtfd, void *buf, size_t nbytes, int flags、；

返回值：返回数据的字节长度：若无可用数据或对等方已经按序结束，返回0;若出错，返回-1

图16«14总结了这些标志。仅有3个标志是Single UNIX Specification定义的。

| 板志                                     | 描述                                                         | POSK.1 | FreeBSD8.0 | Linux3.2.0 | Mac OSX 10.6.8 | Solaris10 |
| ---------------------------------------- | ------------------------------------------------------------ | ------ | ---------- | ---------- | -------------- | --------- |
| MSG_CMSG_CLOEXECMSG_DONTWAITMSG_ERRQUEUE | 为UNIX域套接字上接收的 文件描述符设置执行时关闭标 志（见17.4节）启用非阻塞操作（相当于使 用 O_NONBLOCK）接收错误信息作为辅助数据 |        | •          | •          |                | •         |
| MSG_OOB                                  | 如果协议支持，获取带外数 据（见16.7节）                      | •      | •          | •          | •              | •         |
| MSG_PEEKMSG_TRUNC                        | 返回数据包内容而不真正取 走数据包即使数据包被截断’也返回 数据包的实际长度 |        |            | •          |                |           |
| MSG_WAITALL                              | 等待直到所有的数据可用（仅 SOCK STREAM）                     | •      |            |            |                |           |

图】6~14 recv套接字调用标志

当指定MSG_PEEK标志时，可以查看下一个要读取的数据但不真正取走它D当再狄调用read 或其中一个recv函数时，会返回刚才查看的数据。

対于SOCK_STREAM套接字，接收的数据可以比预期的少。MSG_WAITALL标志会阻止这种 行为，直到所请求的数据全部返回，recv函数才会返回。对于SOCK_DGRAM和SOCK_SEQPACKET 套接字，MSG_WAITALL标志没有改变什么行为，因为这些基于报文的套接字类型一次读取就返 回整个报文。

如果发送者已经调用shutdown （见16.2节）来结束传输，或者网络协议支持按默认的顺序 关闭并且发送端已经关闭，那么当所有的数据接收完毕后，recv会返回0。    闹

如果有兴趣定位发送者，可以使用recvfrom来得到数据发送者的源地址。

\#include <sys/socket.h>

ssize_t recvf rom (int sockfd, void * restrict buf, size_t len, int flags, struct sockaddr * restrict addr, socklen_t *restrict addrlen、；

返回值：返回数据的字节长度；若无可用数据或对等方已经按序结束，返回0;若出错，返回-1

如果非空，它将包含数据发送者的套接字端点地址。当调用recvfrom时，需要设置 参数指向一个整数，孩整数包含咖所指向的套接字缓冲区的字节长度。返回时，垓整

数设为垓地址的实际字节长度。

因为可以获得发送者的地址，recvf rom通常用于无连接的套接字。否则，recvf rom等同 于 recv0

为了将接收到的数据送入多个缓冲区，类似于readv （见14.6节），或者想接收辅助数据 （见17.4节），可以使用recvmsg。

\#include <sys/socket.h>

ssize_t recvrasg （int soc^fd, struct msghdr *msg, int flags、；

返回值：返回数据的字节长度：若无可用数据或对等方已经按序结束，返回0;若出错，返回-1

recvmsg用msghdr结构（在sendmsg中见到过）指定接收数据的输入缓冲区。可以设置 参数来改变recvmsg的默认行为。返回时，msghdr结构中的msg_f lags字段被设为所 接收数据的各种特征。（进入recvmsg时msg_flags被您略。）recvmsg中返回的各种可能值 总结在图16-15中。我们将在第17章看到使用recvmsg的实例。

| 标志         | 描述                     | POSIX.1 | FreeBSD8.0 | Linux 3.2.0 | Mac OSX 10.6.8 | Solans10 |
| ------------ | ------------------------ | ------- | ---------- | ----------- | -------------- | -------- |
| MSG_CTRUNC   | 控制数据被截断           | -       | •          |             | •              | •        |
| MSG_EOR      | 接收记录结束符           |         | •          |             | •              | •        |
| MSG_ERRQUEUE | 接收错误信息作为辅助数据 |         |            |             |                |          |
| MSG_OOB      | 接收带外数据             | •       | •          |             | •              | •        |
| MSG TRUNC    | 一般数据被截断           | •       | •          |             | •              | •        |

图 16-15 从 recvmsg 中返回的 msg_f lags 标志    I.d31

实例：面向连接的客户端

图16-16显示了一个与服务器通信的客户端从系统的uptime命令获得输出。我们把这个服 务称为“选程正常运行时间”（remoteuptime）（简写为“ruptime”）。

\#include #include #include #include



"apue.b" <netdb.h> <errno.h> <sys/socket.h>

\#define BUFLEN    128

extern int connect_retry(int, int, int, const struct sockaddr *, socklen_t);

void

print_uptime{int sockfd)

int    n;

char    buf[BUFLEN];

while ((n = recv(sockfd, buf, BUFLEN, 0)) > 0) write(STDOUT_FILENO, buf, n);

if (n < 0>

err_sys{"recv error");

int

main(int argc, char *argv[])

struct addrinfo struct addrinfo int



★ailist, *aip; hint;

sockfd, err;



if {argc != 2,

err_quit("usage： ruptime hostname"); memset(fihint, 0, sizeof(hint)); hint.ai_socktype = SOCK_STREAM; hint.ai_canonname = NULL; hint.ai_addr = NULL;

hint.ai_next = NULL；

if ({err = getaddrinfo(argv[l], "ruptime", &hint, sailist)) != 0) err_quit("getaddrinfo error: %s", gai_strerror(err));

for {aip = ailist; aip != NULL； aip = aip->ai_next) {

if ((sockfd = connect_retry(aip->ai_family, SOCK_STREAM, 0,

aip->ai_addr, aip->ai_addrlen)) < 0) { err = errno;

} else {

print_uptime(sockfd); exit{0) ?

画 }    *

err_exit(err, "can't connect to %s", argv[l]);

图16-16用于从服务器获取正常运行时间的客户端命令 这个程序连接服务器，读取服务器发送过来的字符串并将其打印到标准输出。因为使用的是

SOCK_STREAM套接字，所以不能保证调用一次recv就会读取整个字符串，因此需要重复调用 直到它返回0。

如栗服务器支持多重网络接口或多重网络协议，函数getaddrinfo可能会返回多个候选地 址供使用。翰流尝试每个地址，当找到一个允许连接到服务的地址时便可停止。使用图16-11中 的connect_retry函数来与服务器建立一个连接。

实例：面向连接的服务器

图16-17展示了服务器程序，用来提供uptime命令的输出到图16-16所示的客户端程序。

\#include "apue.h" ♦include <netdb.h> #include <errno.h> #include <syslog.h> #include <sys/socket.h> #define BUFLEN 128 #define QLEN 10

\#ifndef HOST_NAME_MAX #define HOST_NAME_MAX 256 #endif

extern int initserver(int, const struct sockaddr *, socklen_t, int);

void serve(int sockfd)

int    clfd;

FILE    *fp;

char    buf[BUFLEN];

set_cloexec(sockfd);

for (;;) {

if ((clfd = accept(sockfd, NULL, NULL)) < 0) {

syslog{LOG_ERR, "ruptimed: accept error: %s",

strerror(errno)); exit(l);

}

set_cloexec(clfd);    _

if ((fp = popen("/usr/bin/uptime", "r")) == NULL) {    |615|

sprintf(buf, "error： %s\n", strerror(errno)}; send {clfd, buf, strlen(buf), 0);

} else {

while (fgets(buf, BUFLEN, fp) != NULL) send (clfd, buf, strlen (buf), 0);

pclose(fp);

}

close(clfd);

}

int

main(int argc, char *argv[])

{

struct addrinfo *ailistz *aip;

struct addrinfo

int

char



hint;

sockfd, err, n; *host;



if (argc != 1)

err_quit("usage: ruptimed"); if { {n = sysconf (_SC_HOST_NAME_MAX) ) < 0)

n = HOST_NAME_MAX; /* best guess */ if ( (host = malloc(n)) == NULL)

err_sys("malloc error"); if (gethostname(host, n) < 0)

err_sys("gethostname error")； daemonize("ruptimed"); memset(Shint, 0, sizeof(hint)); hint.ai_flags = AI_CANONNAME; hint.ai_socktype = SOCK_STREAM; hint.ai_canonname = NULL; hint.ai_addr = NULL; hint.ai_next = NULL;

if ((err = getaddrinfo(host, "ruptime", shint, fiailist)) != 0) { syslog(LOG_ERR, "ruptimed: getaddrinfo error: %s",

gai_strerror(err)); exit(l);

f

for (aip = ailist; aip != NULL; aip = aip->ai_next) { if {(sockfd = initserver(SOCK_STREfiM, aip->ai_addr,

aip->ai_addrlen, QLEN)) >= 0)    {

serve(sockfd); exit(0);

exit(l);

[616]



图16-17提供系统正常运行时间的服务器程序



为了找到它的地址，服务器需要获得其运行时的主机名。如果主机名的最大长度不确定，可 以使用HOST_NAME_MAX代替。如果系统没定义HOST_NAME_MAX，可以自己定义。POSIX.1要 求主机名的最大长度至少为255字节，不包括终止null字符，因此定义HOST_NAME_MAX为256 来包括终止null字符，

服务器调用gethostname获得主机名，査看远程正常运行时间服务的地址。可能会有多个 地址返回，但我们简单地选择第一个来建立被动套接字端点（即一个只用于监听连接请求的地 址）。处理多个地址作为习题留给读者。

使用图16-12的initserver函数来初始化套接字端点，在这个端点上等待到来的连接请求。 （实际上，使用的是图16^22的版本；在16.6节中讨论套接字选项时，可以了解其中的原因。）

、实例：另一4^面向连接的服务器

前面说过，采用文件描述符来访问套接字是非常有意义的，因为它允许程序对联网环境的网 络访问一无所知。图16-18中所示的服务器程序版本说明了这一点。服务器没有从uptime命令 中读取输出并发送到客户端，而是将uptime命令的标准输出和标准错误安排成为连接到客户端

的套接字端点。

\#include ttinclude #include #include #include



"apue.h"

<netdb.h>

<errno.h>

<syslog.h>

<fcntl.h>

\#include <sys/socket.h> #include <sys/wait.h>

\#define QLEN 10

ttifndef HOST_NAME_MAX #define HOST_NAME_MAX 256 #endif

extern int initserver(int, const struct sockaddr *, socklen_t, int);

void serve(int sockfd)

int    clfd, status;

pid_t    pid;

set_cloexec(sockfd);

for (;;) {

if ((clfd = accept(sockfd, NULL, NULL)) < 0) {

syslog(LOG_ERR, "ruptimed: accept error: %s",

strerror(errno)); exit (1);

if ( (pid = fork()》< 0) {

syslog(LOG_ERR, "ruptimed: fork error： %s",    _

strerror(errno));    |6I7|

exit ⑴；

} else if (pid == 0) ( Z* child */

/*

\*    The parent called daemonize (Figure 13.1), so

\*    STDIN_FILENO, STDOUT_FILENO, and STDERR_FILENO

\*    are already open to /dev/null. Thus, the call to

\*    close doesn't need to be protected by checks that

\*    clfd isn’t already equal to one of these values.

*/

if (dup2(clfd, STDOUT.FILENO) != STDOUT.FILENO || dup2(Clfd, STDERR_FILENO) != STDERR_FILENO) {

syslog(LOG_ERR, "ruptimed: unexpected error"); exit ⑴；

}

close(clfd);

execl("/usr/bin/uptime", "uptime", (char *)0);

syslog(LOG_ERR, "ruptimed: unexpected return from exec: %s",

strerror(errno));

} else {    /* parent */

close(clfd)i

waitpid(pid, fistatus, 0);

int



main (int argc, char

struct addrinfo struct addrinfo int

char



*argv[])

*ailist, *aip; hint;

sockfd, err, n; *host;



if (argc != 1)

err_quit("usage: ruptimed"); if ( (n = sysconf USC_HOST_NAME_MAX) ) < 0)

n = HOST_NAME_MAX; /* best guess */ if ((host = malloc(nJ) == NULL)

err_sys("malloc error"}; if (gethostname(host, n) < 0)

err_sys("gethostname error"); daemonize("ruptimed"); memset(&hint, 0, sizeof(hint)); hint.ai_flags = AI_CANONNAME; hint.ai_socktype = SOCK_STREAM; hint.ai_canonname = NULL; hint.ai_addr = NULL； hint.ai_next = NULL;

!= 0) {



if ((err = getaddrinfo(host, "ruptime", Shint, &ailist)> syslog (LOG_ERR, "ruptimed: getaddrinfo error: %s**,

gai_strerror(err)); exit(l);

for (aip = ailist; aip != NOLL; aip = aip->ai_next> { if ((sockfd = initserver{SOCK_STREAM, aip->ai_addr,

aip->ai_addrlen, QLEN)) >= 0) { serve(sockfd); exit(0);

}

J

exit(l)，-

图16-18用于说明命令直接写到套接字的服务器程序 我们没有采用popen来运行uptime命令，并从连接到命令标准输出的管道读取输出，而是

采用fork创建了一个子进程，然后使用dup2使STDIN_FILENO的子进程副本对/dev/null开 放，使STDOUT_FILENO和STDERR_FILENO的子进程副本対套接字端点开放。当执行uptime 时，命令将结果写到它的标准输出，该标准输出是连接到套接字的，所以数据被送到ruptime客 户端命令。

父进程可以安全地关闭连接到客户端的文件描述符，因为子进程仍旧让它打开着。父进程会 等待子进程处理完毕再继续，所以子进程不会变成僵死进程。由于运行uptime命令不会花费太 长的时间，所以父进程在接受下一个连接请求之前，可以等待子进程退出。然而，如果子进程运 行的时间比较长的话，这种策略就未必适合了。    ■fc

前面的实例采用的都是面向连接的套接字。但如何选择合适的套接字类型呢？何时采用面 向连接的套接字，何时采用无连接的套接字呢？答案取决于我们要做的工作量和能够容忍的出 错程度。

对于无连接的套接字，数据包到达时可能已经没有次序，因此如果不能将所有的数据放在一 个数据包里，则在应用程序中就必须关心数据包的次序。数据包的最大尺寸是通信协议的特征。

另外，对于无连接的套接字，数据包可能会丢失。如果应用程序不能容忍这种丢失，必须使用面 向连接的套接字。

容忍数据包丢失意味着两种选择。一种选择是，如果想和对等方可靠通信，就必须对数据包 编号，并且在发现数据包丢失时，请求对等应用程序重传，还必须标识重复数据包并丢弃它们，

因为数据包可能会延迟或疑似丢失，可能请求重传之后，它们又出现了。    |619]

另一种选择是，通过让用户再次尝试那个命令来处理错误，对于简单的应用程序，这可能就 足够了，但对于复杂的应用程序，这种选择通常不可行。因此，一般在这种情况下使用面向连接 的套接字比较好。

面向连接的套接字的缺陷在于需要更多的时间和工作来建立一个连接，并且每个连接都需要 消耗较多的操作系统资源。

■•实例：无连接的客户竭

图16-19中的程序是采用数据报套接字接口的uptime客户端命令版本。

番include "apue.h"

♦include <netdb.h>

番include <errno.h>

番include <sys/socket.h>

\#define BUFLEN    128

\#define TIMEOUT    20

void

sigalrm(int signo)

}

void

print_uptirae(int sockfd, struct addrinfo *aip)

{

int    n;

char    buf[BUFLEN];

buf[0] = 0;

if (sendto(sockfd, buf, 1, 0, aip->ai_addr, aip->ai_addr1en) < 0) err_sys("sendto error");

alarm(TIMEOUT);

if (<n = recvfrom{sockfd, buf, BUFLEN, 0, NULL, NULL)) < 0> i if (errno != EINTR)

alarm(0);

err_sys("recv error");

}

alarm(0);

write(STDOUT_FILENO, buf, n);

int

main(int argc, char *argv[])

|~62d|



struct addrinfo struct addrinfo int

struct sigaction



★ailist, *aip; hint;

sockfd, err; sa;



if (argc != 2)

err_quit{"usage: ruptime hostname"); sa.sa_handler = sigalrm; sa.sa_flags = 0; sigemptyset(&sa.sa_mask); if (sigaction(SIGALRM, &sa, NULL) < 0)

err^sys("sigaction error"); memset0, sizeof(hint)); hint.ai_socktype = SOCK_DGRAM; hint.ai_canonname = NULL; hint.ai_addr = NULL;

hint.ai_next = NULL;

0)



if ({err = getaddrinfo(argv[1], "ruptime", &hint, &ailist)) err_quit{"getaddrinfo error: %s", gai_strerror(err));

for (aip = ailist; aip != NULL; aip = aip->ai_next) t

if ((sockfd = socket(aip->ai_family, SOCK_DGRAM, 0)) < 0>    {

err = errno;

} else {

print_uptime{sockfd, aip); exit(0>;

fprintf(stderr, "can't contact %s: %s\n", argv[l], strerror(err)); exit ⑴；

图16-19采用数据报服务的客户端命令

除了增加安装一个SIGALRM的信号处理程序以外，基于数据报的客户端中的main函数和 面向连接的客户端中的类似。使用alarm函数来避免调用recvfrom时的无限期阻塞。

对于面向连接的协议，需要在交换数据之前连接到服务器。对于服务器来说，到来的连 接请求已经足够判断出所需提供给客户端的服务。但是財于基于数据报的协议，需要有一种 方法通知服务器来执行服务。本例中，只是简单地向服务器发送了 1字节的数据。服务器将 接收它，从数据包中得到地址，并使用这个地址来传送它的响应。如果服务器提供多个服务， 可以使用这个请求数据来表示需要的服务，但由于服务器只做一件事情，1字节数据的内容是 无关紧要的。

如果服务器不在运行状态，客户端调用recvfrom便会无限期阻塞。对于这个面向连接的实 例，如果服务器不运行，connect调用会失败。为了避免无限期阻塞，可以在调用recvfrom

岡之前设置警告时钟。    ■

I实例：无连接的服务器

图16-20所示的程序是uptime服务器的数据报版本。

\#include 持include #include #include ♦include



"apue.h" <netdb.h> <errno.h> <syslog.h> <sys/socket.h>

\#define BUFLEN    128

\#define MAXADDRLEN 256

tifndef HOST_NAME_MAX #define HOST_NAME_MAX 256 ttendif

extern int initserver(int, const struct sockaddr ★, socklen_t, int);

void

serve(int sockfd)

{

int

socklen_t

FILE

char

char

struct sockaddr



n；

alen;

*fp；

buf[BUFLEN]; abuf[MAXADDRLEN];

*addr = (struct sockaddr *)abuf;

set_cloexec(sockfd);

for (;;> 1

alen = MAXADDRLEN;

if ((n = recvfrom(sockfd, buf, BUFLEN, 0, addr, &alen)) < 0> { syslog(LOG_ERR, "ruptiraed: recvfrom error: %s",

strerror(errno)); exit ⑴；

if ((fp = popen("/usr/bin/uptime", "r")) == NULL) { sprintf(buf, "error: %s\n", strerror(errno)); sendto(sockfd, buf, strlen(buf>, 0, addr, alen};

} else {

if (fgets(buf, BUFLEN, fp) != NULL)

sendto(sockfd, buf, strlen(buf), 0, addr, alen);

pclose(fp>:

int

main (int argc, char *argv[],

struct addrinfo struct addrinfo int



[ailist, *aip; hint;

sockfd, err, n;



16221



char    *host;

if <argc != 1)

err_quit<"usage: ruptimed"); if ((n = sysconf (_SC_HOST_NAME_MAX)) < 0)

n = HOST_NAME_MAX; /* best guess */ if "host = malloc(n)) == NULL)

err_sys("malloc error"); if (gethostname(host, n) < 0)

err_sys("gethostname error")； daemon!ze(” rupt imed"); memset(&hint, 0, sizeof(hint)); hint.ai_flags = AI_CANONNAME; hint.ai_socktype = SOCK_DGRAM; hint.ai_canonname = NULL; hint.ai_addr = NULL; hint.ai_next = NULL;

if <(err = getaddrinfo(host, "ruptime", fihint, Sailist)) != 0) f syslog{LOG_ERR, "ruptimed: getaddrinfo error: %s",

gai_strerror(err)); exit ⑴；

}

for (aip = ailist; aip != NULL; aip = aip->ai_next) { if ((sockfd = initserver(SOCK_DGRAM, aip->ai_addr,

aip->ai_addrlen, 0)) >= 0) { serve(sockfd)； exit(0);

I

exit (1);

图16-20基于数据报提供系统正常运行时间的服务器

服务器在recvfrom阻塞等待服务请隶。当一个清求到达时，保存请求者地址并使用popen来运 行uptime命令。使用sendto函数将输出发送到客户端，将目标地址设置成刚才的请求者她址。,

16.6套接字选项

套接字机制提供了两个套接字选项接口来控制套接字行为。一个接口用来设置选项，另一个 接口可以査询选项的状态。可以获取或设置以下3种选项。

(1)    通用选项，工作在所有套接字类型上。

(2)    在套接字层次管理的选项，但是依赖于下层协议的支持。

(3)    特定于某协议的选项，每个协议独有的，

[6231    Single UNIX Specification定义了套接字层的选项(上述选项中的前两个选项类型)《

可以使用setsockopt函数来设置套接字选项。

\#include <sys/socket.h>

int setsockopt (int sockfd, int level, int option, const void *val, socklen_t len);

返回值：若成功，返回0;若出错.返回-1

参数level标识了选项应用的协议。如果选项是通用的套接字层次选项，则level设置成 SOL_SOCKETo否则，/eve/设置成捺制这个选项的协I烟号。对于TCP逸项，/evef是IPPROTOJTCP， 对于 IP，/eve/是 IPPROTO_IP。图 16-21 总结了 Single UNIX Specification 中定义的通用套接字 层次选项。

| 选项          | 参数vaZ的类型  | 描述                                             |
| ------------- | -------------- | ------------------------------------------------ |
| SO_ACCEPTCONN | int            | 返回信息指示该套接字是否能被监听（仅getsockopt） |
| SO_BROADCAST  | int            | 如果*val非0,广播数据报                           |
| SO_DEBUG      | int            | 如果*val非0,启用网络驱动调试功能                 |
| SO_DONTROUTE  | int            | 如果*val非0,绕过通常路由                         |
| SO_ERROR      | int            | 返回挂起的套接字错误并濟除（仅getsockopt）       |
| SO_KEEPALIVE  | int            | 如果*val非0»启用周期性keep-alive根文             |
| SOJLINGER     | struct linger  | 当还有未发报文而套接字已关闭时，延迟时间         |
| SO_OOBINLINE  | int            | 如果*val非0,将带外数据放在普通数据中             |
| SO_RCVBUF     | int            | 接收缓冲区的字节长度                             |
| SO_RCVLOWAT   | int            | 接收调用中返回的最小数据字节数                   |
| SO_RCVTIMEO   | struct timeval | 套接字接收调用的超时值                           |
| SO_REUSEADDR  | int            | 如果*val非0,重用bind中的地址                     |
| SO_SNDBUF     | int            | 发送缓冲区的字节长度                             |
| SO^SNDLOWAT   | int            | 发送调用中传送的最小数据字节数                   |
| SO_SNDTIMEO   | struct timeval | 套接字发送调用的超时值                           |
| SO TYPE       | int            | 标识套接字类型（仅getsockopt）                   |

图16-21套接字选项

参数根据选项的不同指向一个数据结构或者一个整数。一些选项是on/off开关。如果整 数非0,则启用选项。如果整数为0,则禁止选项。参数指定了 vfl/指向的对象的大小、

可以使用getsockopt函数来查看选项的当前值。

\#include <sys/socket.h>

int getsockopt (int socifd, int level, int option, void * restrict val, socklen_t * restrict lenp);

返回值：若成功，返回0:若出错，返回-1

参数是一个指向整数的指针。在调用getsockopt之前，设置该整数为复制选项缓冲 医的长度。如果选项的实际长度大于此值，则选项会被截断。如果实际长度正好小于此值，那么 返回时将此值更新为实际长度。

鍵■实例

当服务器终止并尝试立即重启时，图16-12中的函数将无法正常工作。通常情况下，除非超 时（超时时间一般是几分钟），否则TCP的实现不允许绑定同一个地址。幸运的是，套接字选项 SO_REUSEADDR可以绕过这个限制，如图16-22所示。

\#include "apue.h"

\#include <errno.h>

\#include <sys/socket.h>

int

initserver(int type, const struct sockaddr *addr, socklen_t alen.

int qlen)

int fd, err; int reuse = 1;

if ((fd = socket (addr->sa_family, type, 0" < 0) return (-1)；

if (setsockopt(fd, SOL_SOCKET, SO_REUSEADDR, &reuse, sizeof(int)) < 0)

goto errout;

if (bind(fd, addr, alen) < 0) goto errout;

if {type == SOCK_STREAM || type == SOCK_SEQPACKET) if (listen(fd, qlen) < 0)

goto errout; return(fd);

errout:

err = errno; close(fd); errno = err; return(-1);

}

图16-22采用地址复用初始化套接字端点供服务器使用 为了启用SO_REUSEADDR选项，设置了一个非0值的整数，并把这个整数地址作为vo/参数

[625]传递给了 setsockopto将細参数设置成了一个整数大小来表明做/所指的对象的大小。,

###### 16.7带外数据

带外数据(out-of-banddata)是一些通信协议所支持的可选功能，与普通数据相比，它允 许更高优先级的数据传输。带外数据先行传输，即使传输队列已经有数据。TCP支持带外数 据.但是UDP不支持套接字接口对带外数据的支持很大程度上受TCP带外数据具体实现的 影响。

TCP将带外数据称为紧急数据(urgentdata)。TCP仅支持一个字节的紧急数据，但是允许紧 急数据在普通数据传递机制数据流之外传输。为了产生紧急数据，可以在3个send函数中的任 何一个里指定MSG_OOB标志。如果带MSG_OOB标志发送的字节数超过一个时，最后一个字节将 被视为紧急数据字节。

如果通过套接字安排了信号的产生，那么紧急数据被接收时，会发送SIGURG信号。在3.14 节和14.5.2节中可以看到，在fcntl中使用F_SETOWN命令来设置一个套接字的所有权。如果 fcntl中的第三个参数为正值，那么它指定的就是进程辽)。如果为非-1的负值，那么它代表的 就是进程组ID。因此，可以通过调用以下函数安排进程接收套接字的信号：

fcntl(sockfd, F_SETOWN, pid);

F_GET0WN命令可以用来获得当前套接字所有权。对于F.SETOWN命令，负值代表进程组ID, 正值代表进程ID。因此，调用

owner = fcntl(sockfd, F_GETOWN, 0};

将返回owner,如果owner•为正值，则等于配置为接收套接字信号的进程的ID。如果owner为 负值，其绝对值为接收套接字信号的进程组的ID。

TCP支持紧急标记(urgent mark)的概念，即在普通数据流中紧急数据所在的位置。如果采 用套接字选项SO_OOBINLINE，那么可以在普通数据中接收紧急数据。为帮助判断是否已经到达 緊急标记，可以使用函数sockatmark。

\#include <sys/socket.h> int sockatmark (int soclrfd);

返回值：若在标记处，返回1:若没在标记处，返回0:若出错，返回-1

当下一个要读取的字节在紧急标志处时，socfcatmark返回1。

当带外数据出现在套接字读取队列时，select函数(见14.4.1节)会返回一个文件描述 符并且有一个待处理的异常条件D可以在普通数据流上接收紧急数据，也可以在其中一个 recv函数中采用MSG_OOB标志在其他队列数据之前接收紧急数据，TCP队列仅用一个字节的 紧急数据。如果在接收当前的紧急数据字节之前又有新的紧急数据到来，那么已有的字节会被[6?6] 丢弃。

##### 16.8非阻塞和异步I/O

通常，recv函数没有数据可用时会阻塞等待。同样地，当套接字输出队列没有足够空间来 发送消息时，send函数会阻塞。在套接字非阻塞模式下，行为会改变。在这种情况下，这些函 数不会阻塞而是会失败，将errno设置为EWOULDBLOCK或者EAGAIN。当这种情况发生时，可 以使用poll或select来判断能否接收或者传输数据。

Single UNIX Specification包含通用异步I/O机制(见14.5节)的支持。套接字机制有其自己 的处理异步I/O的方式，但是这在Single UNIX Specification中没有标准化。一些文献把经典的基 于套接字的异步I/O机制称为“基于信号的I/O”，区别于Single UNIX Specification中的通用异 步I/O机制。

在基于套接字的异步I/O中，当从套接字中读取数据时，或者当套接字写队列中空间变得可 用时，可以安排要发送的信号SIGIO。启用异步I/O是一个两步骤的过程。

(1)    建立套接字所有权，这样信号可以被传递到合适的进程。

(2)    通知套接字当I/O操作不会阻塞时发信号。

可以使用3种方式来完成第一个步骤。

(1)    在fcntl中使用F_SETOWN命令。

(2)    在ioctl中使用FIOSETOWN命令。

(3)    在ioctl中使用SIOCSPGRP命令。

要完成第二个步骤，有两个选择。

(1)    在fcntl中使用F_SETFL命令并且启用文件标志O_ASYNC。

(2)    在ioctl中使用FIOASYNC命令。

虽然有多种选项，但它们没有得到普遍支持。图16-23总结了本文讨论的平台支持这些选项 的情况。

| 机制                                                         | POSIX.l | FreeB SD 8.0 | Linux 3.2.0 | Mac OS X 10.6.8 | Solaris10 |
| ------------------------------------------------------------ | ------- | ------------ | ----------- | --------------- | --------- |
| fcntl(fd, F_SET0WN, pid) ioctl(fd, FIOSETOWN, pid) ioctl(fd, SIOCSPGRP, pid) |         |              |             |                 | -         |
| fcntl(fd, F_SETFL, flags\|O_ASYNC) ioctl(fd, FIOASYNC, &n);  |         |              |             |                 | •         |

[627]    图16-23套接字异步I/O管理命令

##### 16.9小结

本章考察了 IPC机制，这些机制允许进程与不同计算机上的以及同一计算机上的其他进程通 信。我们讨论了套接字端点如何命名，在连接服务器时，如何发现所用的地址。

我们给出了采用无连接的（即基于数据报的）套接字和面向连接的套接字的客户端和服务器 的实例，还简要讨论了异步和非阻塞的套接字1/0,以及用于管理套接字选项的接口。

下一章将会考察一些高级IPC主题，包括在同一台计算机上如何使用套接字在两个进程之间 传送文件描述符。

习题

16.1写一个程序判断所使用系统的字节序。

16.2写一个程序，在至少两种不同的平台上打印出所支持套接字的stat结构成员，并且描述这 些结果的不同之处。

16.3图16-17的程序只在一个端点上提供了服务。修改这个程序，同时支持多个端点（每个端点 具有一个不同的地址）上的服务。

16.4写一个客户端程序和服务端程序，返回指定主机上当前运行的进程数量。

16.5在图16-18的程序中，服务器等待子进程执行uptime，子进程完成后退出，服务器才接受

下一个连接请求。重新设计服务器，使得处理一个请求时并不拖延处理到来的连接请求。 16.6写两个库例程：一个在套接字上允许异步1/0, 一个在套接字上不允许异步I/O。使用图16-23

函    来保证函数能够在所有平台上运行，并且支持尽可能多的套接字类型。
