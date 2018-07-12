
## 4 从 CentOS 6.5 升级到 CentOS 7

对于一些已安装 CentOS 旧版本的计算机来说，通常更希望能从旧版本直接升级到 CentOS7，因为这样能够避免数据迁移的问题。由于 CentOS7 与之前的版本差异较大(事实上每一个新版本与老版本差异都较大)，不一定能百分之百成功。本节将简要介绍如何从CentOS 6.5升级到 CentOS7。<span style="color:red;">为什么不能保证百分百成功呢？</span>

### 4.1 升级风险

在个人使用的计算机或虚拟机中升级 CentOS 即使失败，可能也未必会有多大损失，但拥有宝贵数据的生产环境则不同。此处仅讨论生产环境中升级可能带来的影响，其主要可归结为以下几点：

1. 系统升级后，系统中的软件也会同时升级。这些软件可能会因为兼容性等原因与之前版本的数据、配置文件等产生冲突，导致不可用或部分功能丧失。<span style="color:red;">嗯。是的</span>
2. 由于CentOS 7属于较新的系统，其稳定性、性能尚不稳定，可能会危及业务系统的可用性。
3. 新系统使用时间尚短，可能会有许多没有被发现的 Bug，这也是为何运维工程师通常会采用较低版本的原因。<span style="color:red;">嗯。</span>

从之前红帽子的更新习惯来看，新版本通常有非常巨大的改动，也必然存在较多 Bug 且系统也尚未得到时间的考验，因此生产环境更新可稍作等待。待更新两三次，系统中大部分 Bug 被发现、修复，且性能趋于稳定时再更新。

### 4.2 使用升级工具

与之前的版本不同，新版本的 CentOS 提供了一个升级工具 preupg ，用户可以使用此工具将 CentOS 6.5 升级到 7 。**但这个工具并没有得到非常严格的测试，因此生产环境中不建议使用本小节中介绍的升级工具**。<span style="color:red;">好吧。</span>

**(1)** 由于升级工具并没有包含在 CentOS 6.5 的软件源中，因此需要添加新的软件源才能使用。使用 vim 在目录/etc/yum.repos.d中建立一个名为upgrade.repo的文件，内容如下：

```
[root@localhost -]# cat /etc/yum.repos.d/upgrade.repo
[upgrade]
name=upgrade
baseurl=http://dev.centos.org/centos/6/upg/x86_64/
enable=l
gpgcheck=O
```

完成上述设置后，请确保计算机能正常连接网络，然后执行以下命令安装更新工具：

```
yum -y install preupgrade-assistant-contents redhat-upgrade-tool preupgrade-assistant
```

上面这条命令将安装包括测试工具在内的升级工具。<span style="color:red;">嗯。</span>

**(2)** 在正式开始升级之前，建议使用测试工具进行测试：

```
[root@localhost ~]# preupg
```

需要注意的是，preupg工具仅作一些常规测试，其结果仅能做参考。

**(3)** 接下来就可以导入 CentOS7 的 key 并开始更新了:

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/K65H0Af1eG.png?imageslim)

先使用 rpm 导入 Key,然后使用 redhat-upgrade-tool-cli 工具下载更新中需要使用的软件包。从以上执行结果可以看出共计下载了 1468 个软件包(视系统中安装的软件不同，需要的软件包也不同)，由于此步需要从国外服务器中下载软件包，因此可能需要花费大量时间。

**(4)** 完成上述步骤之后，重新启动系统时系统就会自动更新至CentOS 7，如图2.37所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180712/eJmg8A9jLI.png?imageslim)

从图中可以看到，系统已经开始更新系统中的软件，视计算机配置不同这个过程将持续 10~20 分钟。在升级过程中可能还会出现一些错误及部分软件升级失败的情况，通常系统会自动处理这些情况，以保证升级过程顺利完成。

<span style="color:red;">嗯，一般来说要不要升级呢？还需要什么准备工作吗？比如说是否需要备份什么？</span>
