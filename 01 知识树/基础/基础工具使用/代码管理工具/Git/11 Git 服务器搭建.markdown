TODO:

- 感觉后续再补充一下。

---


# Git 服务器搭建

## Git 服务器搭建


上一章节中我们远程仓库使用了 Github，Github 公开的项目是免费的，但是如果你不想让其他人看到你的项目就需要收费。

这时我们就需要自己搭建一台 Git 服务器作为私有仓库使用。

接下来我们将以 Centos 为例搭建 Git 服务器。


### 1、安装Git

```
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
$ yum install git
```



接下来我们 创建一个 git 用户组和用户，用来运行 git 服务：<span style="color:red;">为什么要添加这么一个组和用户呢？</span>

```
$ groupadd git
$ adduser git -g git
```


### 2、创建证书登录


收集所有需要登录的用户的公钥，公钥位于 id_rsa.pub 文件中，把我们的公钥导入到 /home/git/.ssh/authorized_keys 文件里，一行一个。

如果没有该文件创建它：

```
$ cd /home/git/
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
```




### 3、初始化Git仓库


首先我们选定一个目录作为Git仓库，假定是 /home/gitrepo/w3cschoolcn.git ，在 /home/gitrepo 目录下输入命令：

```
$ cd /home
$ mkdir gitrepo
$ chown git:git gitrepo/
$ cd gitrepo

$ git init --bare w3cschoolcn.git
Initialized empty Git repository in /home/gitrepo/w3cschoolcn.git/
```


以上命令Git创建一个空仓库，服务器上的Git仓库通常都以.git结尾。然后，把仓库所属用户改为git：<span style="color:red;">为什么要这么改？</span>

```
$ chown -R git:git w3cschoolcn.git
```




### 4、克隆仓库



```
$ git clone git@192.168.45.4:/home/gitrepo/w3cschoolcn.git
Cloning into 'w3cschoolcn'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
```


192.168.45.4 为 Git 所在服务器 ip ，你需要将其修改为你自己的 Git 服务 ip。

这样我们的 Git 服务器安装就完成了，接下来我们可以禁用 git 用户通过shell登录，可以通过编辑 /etc/passwd 文件完成。找到类似下面的一行：

```
git:x:503:503::/home/git:/bin/bash
```


改为：

```
git:x:503:503::/home/git:/sbin/nologin
```







# REF

- [Git教程](https://www.w3cschool.cn/git/)
