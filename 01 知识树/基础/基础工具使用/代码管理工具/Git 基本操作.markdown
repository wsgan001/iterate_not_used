# Git 基本操作


# REF

- [Git教程](https://www.w3cschool.cn/git/)





## Git 基本操作


Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。本章将对有关创建与提交你的项目的快照的命令作介绍。



## 获取与创建项目命令




### git init


用 git init 在目录中创建新的 Git 仓库。 你可以在任何时候、任何目录中这么做，完全是本地化的。

在目录中执行 git init，就可以创建一个 Git 仓库了。比如我们创建 w3cschoolcc 项目：


    $ mkdir w3cschoolcc
    $ cd w3cschoolcc
    $ git init
    Initialized empty Git repository in /www/w3cschoolcc/.git/
    # 在 /www/w3cschoolcc/.git/ 目录初始化空 Git 仓库完毕。



现在你可以看到在你的项目目录中有个 .git 的子目录。 这就是你的 Git 仓库了，所有有关你的此项目的快照数据都存放在这里。

```
ls -a
.    ..  .git
```



### git clone


使用 git clone 拷贝一个 Git 仓库到本地，让自己能够查看该项目，或者进行修改。

如果你需要与他人合作一个项目，或者想要复制一个项目，看看代码，你就可以克隆那个项目。 执行命令：


     git clone [url]


[url] 为你想要复制的项目，就可以了。

例如我们克隆 Github 上的项目：


    $ git clone git://github.com/schacon/simplegit.git
    Initialized empty Git repository in /private/tmp/simplegit/.git/
    remote: Counting objects: 100, done.
    remote: Compressing objects: 100% (86/86), done.
    remote: Total 100 (delta 35), reused 0 (delta 0)
    Receiving objects: 100% (100/100), 9.51 KiB, done.
    Resolving deltas: 100% (35/35), done.
    $ cd simplegit/
    $ ls
    README   Rakefile lib



上述操作将复制该项目的全部记录。


    $ ls -a
    .        ..       .git     README   Rakefile lib
    $ cd .git
    $ ls
    HEAD        description info        packed-refs
    branches    hooks       logs        refs
    config      index       objects



默认情况下，Git 会按照你提供的 URL 所指示的项目的名称创建你的本地项目目录。 通常就是该 URL 最后一个 / 之后的项目名称。如果你想要一个不一样的名字， 你可以在该命令后加上你想要的名称。



* * *





## 基本快照


Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。本章将对有关创建与提交你的项目的快照的命令作介绍。


### git add


git add 命令可将该文件添加到缓存，如我们添加以下两个文件：


    $ touch README
    $ touch hello.php
    $ ls
    README      hello.php
    $ git status -s
    ?? README
    ?? hello.php
    $



git status 命令用于查看项目的当前状态。

接下来我们执行 git add 命令来添加文件：


    $ git add README hello.php



现在我们再执行 git status，就可以看到这两个文件已经加上去了。


    $ git status -s
    A  README
    A  hello.php
    $



新项目中，添加所有文件很普遍，可以在当前工作目录执行命令：git add .。

现在我们改个文件，再执行一下 git status：


    $ vim README
    $ git status -s
    AM README
    A  hello.php



"AM" 状态的意思是，这个文件在我们将它添加到缓存之后又有改动。改动后我们在执行 git add 命令将其添加到缓存中：


    $ git add .
    $ git status -s
    A  README
    A  hello.php



当你要将你的修改包含在即将提交的快照里的时候，需要执行 git add。


### git status


git status 以查看在你上次提交之后是否有修改。

我演示该命令的时候加了 -s 参数，以获得简短的结果输出。如果没加该参数会详细输出内容：


    $ git status
    On branch master

    Initial commit

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)

      new file:   README
        new file:   hello.php





### git diff


执行 git diff 来查看执行 git status 的结果的详细信息。

git diff 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别。git diff 有两个主要的应用场景。




  * 尚未缓存的改动：git diff


  * 查看已缓存的改动： git diff --cached


  * 查看已缓存的与未缓存的所有改动：git diff HEAD


  * 显示摘要而非整个 diff：git diff --stat


在 hello.php 文件中输入以下内容：


    <?php echo 'www.w3cschool.cn'; ?>





    $ git status -s
    A  README
    AM hello.php
    $ git diff
    diff --git a/hello.php b/hello.php
    index e69de29..d1a9166 100644
    --- a/hello.php
    +++ b/hello.php
    @@ -0,0 +1,3 @@
    +<?php +echo 'www.w3cschool.cn'; +?>



git status显示你上次提交更新至后所更改或者写入缓存的改动， 而 git diff 一行一行地显示这些改动具体是啥。

接下来我们来查看下 git diff --cached 的执行效果：


    $ git add hello.php
    $ git status -s
    A  README
    A  hello.php
    $ git diff --cached
    diff --git a/README b/README
    new file mode 100644
    index 0000000..704cce7
    --- /dev/null
    +++ b/README
    @@ -0,0 +1 @@
    +w3cschool.cn
    diff --git a/hello.php b/hello.php
    new file mode 100644
    index 0000000..d1a9166
    --- /dev/null
    +++ b/hello.php
    @@ -0,0 +1,3 @@
    +<?php +echo 'www.w3cschool.cn'; +?>





### git commit


使用 git add 命令将想要快照的内容写入了缓存， 而执行 git commit 记录缓存区的快照。

Git 为你的每一个提交都记录你的名字与电子邮箱地址，所以第一步需要配置用户名和邮箱地址。


    $ git config --global user.name 'w3cschool'
    $ git config --global user.email w3c@w3cschool.cn



接下来我们写入缓存，并提交对 hello.php 的所有改动。在首个例子中，我们使用 -m 选项以在命令行中提供提交注释。


    $ git add hello.php
    $ git status -s
    A  README
    A  hello.php
    $ git commit -m 'test comment from w3cschool.cn'
    [master (root-commit) 85fc7e7] test comment from w3cschool.cn
     2 files changed, 4 insertions(+)
     create mode 100644 README
     create mode 100644 hello.php



现在我们已经记录了快照。如果我们再执行 git status:


     $ git status
    # On branch master
    nothing to commit (working directory clean)



以上输出说明我们在最近一次提交之后，没有做任何改动，是一个"干净的工作目录"。

如果你没有设置 -m 选项，Git 会尝试为你打开一个编辑器以填写提交信息。 如果 Git 在你对它的配置中找不到相关信息，默认会打开 vim。屏幕会像这样：

# Please enter the commit message for your changes. Lines starting # with '#' will be ignored, and an empty message aborts the commit. # On branch master # Changes to be committed: # (use "git reset HEAD ..." to unstage) # # modified: hello.php # ~ ~ ".git/COMMIT_EDITMSG" 9L, 257C

如果你觉得 git add 提交缓存的流程太过繁琐，Git 也允许你用 -a 选项跳过这一步。命令格式如下：


    git commit -a



如：


    $ git commit -am 'changes to hello file'
    [master 78b2670] changes to hello file
     1 files changed, 2 insertions(+), 1 deletions(-)





### git reset HEAD


git reset HEAD 命令用于取消缓存已缓存的内容。

这里我们有两个最近提交之后又有所改动的文件。我们将两个都缓存，并取消缓存其中一个。


    $ git status -s
     M README
     M hello.php
    $ git add .
    $ git status -s
    M  README
    M  hello.pp
    $ git reset HEAD -- hello.php
    Unstaged changes after reset:
    M hello.php
    $ git status -s
    M  README
     M hello.php



现在你执行 git commit 将只记录 README 文件的改动，并不含现在并不在缓存中的 hello.rb。


### git rm


git rm 将文件从缓存区中移除。

如我们删除 hello.php文件：


     $ git rm hello.php
    rm 'hello.php'
    $ ls
    README



默认情况下，git rm file 会将文件从缓存区和你的硬盘中（工作目录）删除。 如果要在工作目录中留着该文件，可以使用命令：


     git rm --cached。




### git mv


git mv 命令做得所有事情就是 git rm --cached， 重命名磁盘上的文件，然后再执行 git add 把新文件添加到缓存区。因此，虽然有 git mv 命令，但它有点多余 。
