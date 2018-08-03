TODO:

- 感觉这一章这个远程仓库的远程感觉不对吧？叫做本地仓库差不多？而且，这个.git 文件夹里面的内容到底与 github 上有什么关系？怎么实现的push到真正的远程仓库上面的？

---


# Git 创建仓库


## Git 创建仓库

本章节将介绍如何创建一个远程的 Git 仓库。比如说，

- 在一个 linux 服务器上放一个远程的 git 仓库，这样大家都可以访问这个仓库。
- 或者，其实 github 就是一个远程的仓库。
- 或者，可以在 linux 上放一个 git 仓库，然后你提交代码上去的时候，通过hook可以自动完成一些事情。

OK，我们可以使用一个已经存在的目录作为 Git 仓库或创建一个空目录。<span style="color:red;">这个地方指的远程仓库实际上指的是本地的 .git 文件夹对应的仓库是吧？</span>

如果使用您当前目录作为 Git 仓库，我们只需把它初始化。

```
git init
```

我们也可以指定某个目录作为 Git 仓库。

```
git init newrepo
```

初始化后，这个目录下会出现一个名为 .git 的文件夹，所有 Git 需要的数据和资源都会放在这个文件夹中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

```
$ git add *.c
$ git add README
$ git commit -m 'initial project version'
```




## 从现有仓库克隆


克隆仓库的命令格式为：

```
git clone [url]
```

比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：

```
$ git clone git://github.com/schacon/grit.git
```

执行该命令后，会在当前目录下创建一个名为 grit 的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：<span style="color:red;">这个经常用吗？这样会弄得比较混乱吧？</span>

```
$ git clone git://github.com/schacon/grit.git mygrit
```


# REF

- [Git教程](https://www.w3cschool.cn/git/)
