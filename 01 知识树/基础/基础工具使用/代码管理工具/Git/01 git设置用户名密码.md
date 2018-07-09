

# git设置用户名密码

## 设置git用户名／邮箱

```
git config --global user.name xxxxxx
git config --global user.email xxxxxx
```

但是这个仅仅是设置用户名密码，如果你的git 源每次操作需要你输入用户名/密码验证，你依然需要每次设置，那么该如何办呢？

## git保存用户名密码

这里主要是配置一个config项

有两个方法，基本上原理都是一样，都是修改`.git/config`文件

1.使用如下命令，修改config文件即可保存

```
echo "[credential]" >> .git/config
echo "    helper = store" >> .git/config12
```

2.直接修改.git/config文件

在linux/mac下可以直接使用vim工具修改config文件

```
ubuntu@VM-7-212-ubuntu:~/kernel-code/kernel-netfilter-sample-code$ vim .git/config

##修改成如下

[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = https://github.com/Miss-you/kernel-netfilter-sample-code.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
##主要是后面这两行，如果不想保存，则删除即可
[credential]
    helper = store

##保存
```

这样就可以保存用户名密码，不用每次都输入了！

## git config查看配置

使用`git config --list`查看已设配置

```text
feiqianyousadeMacBook-Pro:xt_GTPU yousa$ git config --list
core.excludesfile=/Users/yousa/.gitignore_global
user.name=Miss-you
user.email=snowfly1993@gmail.com
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
remote.origin.url=https://github.com/Miss-you/xt_GTPU.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
```
