
# REF

- [Git教程](https://www.w3cschool.cn/git/)




## Git 创建仓库

本章节我们将为大家介绍如何创建一个远程的 Git 仓库。您可以使用一个已经存在的目录作为Git仓库或创建一个空目录。

使用您当前目录作为Git仓库，我们只需使它初始化。

```
git init
```


使用我们指定目录作为Git仓库。


    git init newrepo



初始化后，在当前目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：


    $ git add *.c
    $ git add README
    $ git commit -m 'initial project version'






* * *





## 从现有仓库克隆


克隆仓库的命令格式为：


    git clone [url]


比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：


    $ git clone git://github.com/schacon/grit.git



执行该命令后，会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：


    $ git clone git://github.com/schacon/grit.git mygrit
























* * *





# COMMENT
