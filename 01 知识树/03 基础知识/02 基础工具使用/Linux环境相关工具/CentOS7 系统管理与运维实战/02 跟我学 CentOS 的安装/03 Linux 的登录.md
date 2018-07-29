
## Linux的登录

CentOS安装完之后，需要第一次配置并登录使用，Linux系统的登录方式有多种，本节主要介绍Linux的常见登录方式，如本地登录或通过相关软件远程连接等。


### 3.1 首次配置与本地登录

在前面的章节中，主要介绍了如何使用不同的方法安装 CentOS 7，本小节将简要介绍 CentOS 7 的首次配置和本地登录等内容。

**(l)** CentOS7安装完成后重启即可使用，首次进入时还需要做一些简单的配置，如图 2.30 所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/7djGme7KHL.png?imageslim)

首次进入系统会要求用户确认许可信息，单击“许可信息”，接受CentOS的许可证进入下一步设置。接下来会要求用户确认是否启用 Kdump，如图2.31所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/0CHJ7JaDg3.png?imageslim)

Kdump主要用来调试系统内核和相关软件，对用户和生产环境几乎没有任何帮助，启用与否均无太大影响。<span style="color:red;">好吧。</span>设置完Kdump单击“前进”按钮，即可完成设置进入登录界面，如图2.32 所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/gDF4cjh1ei.png?imageslim)

在登录界面的右上角可以做一些辅助设置，例如语言设置、声音和开关机等。此时单击屏幕中间的用户名后在弹出的窗口中输入密码，然后单击“登录”按钮，如果用户名、密码校验 通过则可顺利登录Linux系统。

**(2)** 首次进入桌面环境，CentOS会弹出窗口要求用户进行一些使用习惯上的配置，如图 2.33所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/EcCBdIGFDE.png?imageslim)

从图中可以看到，系统会首先要求用户设置系统默认语言，接下来还会提示用户设置输入源(即输入法)、云账号等内容，这些内容可按实际情况设置，此处不做赘述。

**(3)** 如想切换到命令模式，可进入系统后在桌面单击右键选择“在终端中打开”，然后在其中输入“init3”，即可完成运行级别的转变。Linux运行级别如表2.1所示。<span style="color:red;">一定要改运行级别吗？为什么会有运行级别这种设置？</span>

表2.1 Linux运行级别

| 参数 | 说明                                                    |
| ---- | ------------------------------------------------------- |
| 0    | 停机                                                    |
| 1    | 单用户模式                                              |
| 2    | 多用户                                                  |
| 3    | 完全多用户模式，服务器一般运行在此级别                  |
| 4    | 一般不用，在一些特殊情况下使用                          |
| 5    | X11 模式，一般发行版默认的运行级别，可以启动图形桌面系统 |
| 6    | 重新启动                                             |

## 3.2远程登录

远程登录是Linux系统中最常见的一种登录方式，多为运维工程师所用，远程登录可以使用 VNC 图形界面、ssh 等方法。其中以使用 ssh 登录为多，其原因是运维工程师管理和维护的系统通常没有图形界面，且 ssh 使用的加密方案比较安全。本小节以 ssh 登录为例简要介绍如何远程登录。

**(1)** 如果需要在虚拟机中使用远程登录，首先网络必须互通，如果虚拟机已使用了 Host-Only 模式(仅主机模式)或桥接模式，则可以直接在宿主机登录。<span style="color:red;">嗯。</span>

本例中将采用仅主机模式演示登录过程，首先我们需要先查看 Host-Only 模式使用的IP 地址段。以 Windows 7为例，在开始菜单中单击“控制面板”，然后在控制面板中找到并单击 “网络和Internet” 下面的 “查看网络状态和任务”。此时将进入 “网络和共享中心” ，在其界面的左侧单击 “更改适配器设置” ，此时将进入 “网络连接” 界面。找到 “VMware Network Adapter VMnet8” 并右击，在弹出的菜单中选择“状态”，然后在状态对话框中单击“详细信息”，出 现如图2.34所示的界面。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/AGJa3FHjE2.png?imageslim)

如图2.34所示查看其IP地址，只有虚拟机 Host-Only 网卡的IP地址与此IP地址在同一网段方可进行远程登录。

**(2)** 在虚拟机中查看IP地址可以使用 `ifconfig` 命令，如图2.35所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/bGmAeaiK7m.png?imageslim)

可以看到网卡eno 16777736的 IP 地址与 Windows 中的 IP 地址属于同一网段，因此可以使用远程连接。如果使用以上命令没有查看到此 IP 地址，就需要重启网络连接或对网络连接进行配置。<span style="color:red;">没看懂。关于IP一直不是很清楚。</span>

**(3)** 由于CentOS7 默认开启 ssh ，因此可直接通过 PuTTY 等工具连接，如图2.36所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/ehHDmCjJeL.png?imageslim)

在PuTTY中填入CentOS7 的 IP 地址，并选择 “SSH”，单击 “Open” ，输入用户名和密码即可远程登录到Linux系统中。<span style="color:red;">关于PuTTY 和 Xshell 还是需要介绍下的，比如要怎么使用，哪个比较好。有哪些快捷键等。 </span>
