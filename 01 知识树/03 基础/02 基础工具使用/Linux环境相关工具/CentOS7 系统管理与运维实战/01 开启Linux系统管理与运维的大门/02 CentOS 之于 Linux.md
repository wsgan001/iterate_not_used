
## 2 CentOS 之于 Linux

CentOS (Community Enterprise Operating System，社区企业操作系统) <span style="color:red;">这个 CentOS 竟然是 Community Enterprise Operating System 的缩写。</span>最初是由一个社区主导的操作系统，其来源于 Linux 的另一个最重要的发行版 Red Hat Enterprise Linux (后面简称为RHEL)。由于CentOS并不向用户收取任何费用，因此得到了大量技术实力较高的运维人员的青睐而发展壮大。<span style="color:red;">嗯。</span>

## 2.1 CentOS 简介

说到 CentOS 必然需要先说明 RHEL ，而说到 RHEL 又不得不说 RHEL 的运作模式。RHEL 的发行公司通常被称为红帽子公司，其发行的 RHEL 与 Windows 这类闭源操作系统的发行模 式截然不同。由于 RHEL 采用了 GNU 计划中的大部分软件，因此红帽子公司在发行 RHEL 时， 通常需要使用两种形式发行同一个版本。第一种称为二进制版，用户可以直接利用这个版本安装并使用；另一种形式则为遵循 GNU 计划规定的源码形式。获得和安装 RHEL 都无须付费，但升级和技术支持需要付费，因此一些经费紧张的小型企业无法使用这种昂贵而又十分优秀的操作系统，在这种形式下 CentOS 应运而生。

CentOS 根据 RHEL 释出的源代码进行二次编译，并去掉 RHEL 相关的图标等具有商业版权的信息后形成与 RHEL 版本相对应的 CentOS 发行版。虽然 CentOS 是根据 RHEL 源代码编 译而成，但 CentOS 与 RHEL 仍有许多不同之处：

1. RHEL 中包含了红帽自行开发的闭源软件(如红帽集群套件等)，这些软件并未开放源代码，因此也就未包含在CentOS发行版中。
2. CentOS 发行版通常会修改 RHEL 中存在的一些 BUG ，并提供了一个 yum 源以便用户可以随时更新操作系统。
3. 与 RHEL 提供商业技术支持不同，CentOS 并不提供任何形式的技术支持，用户遇到的问题需要用户自行解决，因此 CentOS 对技术人员的要求更高。

RHEL 与 CentOS 还有许多不同之处，此处不一一列举，感兴趣的读者可以参考相关资料了解。值得注意的是 2014 年初，CentOS与 Red Hat 同时宣布，CentOS 将加入 Red Hat，共同打造 CentOS，业界普遍希望此举能让 CentOS 操作系统更加强大。

虽然 CentOS 的技术门槛更高，但其稳定、安全、高效等特点吸引了一大批经验丰富的 IT 管理人员加入，从近些年来的使用情况来看，其发展非常迅猛。许多 IT 企业都在使用 CentOS, 其中不乏像淘宝、网易这样的 IT 巨头。<span style="color:red;">到底哪个使用的最多？对于机器学习 server 来说呢？</span>

## 2.2 CentOS7 的最新改进

CentOS 每一次新版本的发布都会提供许多新的功能，并对已经存在的软件进行了大量的优化。例如 CentOS5 发布后，用户惊奇地发现yum包管理器更具人性化了，而 CentOS6 对虚拟化进行了大量的修改。CentOS7 也不例外，其改进主要有：

1. 更新内核版本为3.10.0:新版本的内核将对 swap 内存空间进行压缩，这将显著提高 I/O 性能；优化 KVM 虚拟化支持；开启固态硬盘和机械硬盘框架，同时使用这两种硬盘的系统将会提速；更新和改进了图形、音频声音驱动等。<span style="color:red;">KVM 虚拟化支持是什么？怎么对swap 进行压缩的？</span>

2. 文件系统方面：默认支持 XFS 文件系统，并更新了 KVM，使其可以支持 ext4 和 XFS快照。

3. 网络方面：支持 Firewalld (动态防火墙)，防火墙现在可以支持区域和网络信任，配置防火墙之后也不需要重新启动防火墙就可以应用配置了；更新了高性能网络驱动等。<span style="color:red;">什么是动态防火墙？到底防火墙有什么用？怎么使用？</span>

4. 支持 Linux 容器：Linux 容器能提供轻量化的虚拟化，以便隔离进程和资源，这将提高资源的使用效率。<span style="color:red;">什么叫支持 Linux 容器？指的是 docker 吗？</span>

5. 用 Systemd 替换 SysVinit ：更好的服务管理框架能使存在依赖的服务之间更好地并行化。<span style="color:red;">这个 Systemd 是什么？好像之前用过，好像 nginx 启动和停止的时候是用的这个指令。</span>

CentOS7 有许多改进，此处不再一一列举，感兴趣的读者可以阅读相关文档了解。对于运维人员而言，CentOS 新版本无疑会在功能、操作便捷性和性能等方面带来巨大改变，甚至一些操作方式(例如防火墙、系统服务管理)也会发生改变，这些改变需要运维人员一一适应， 以高效地管理你的系统。
