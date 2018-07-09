# Git 基本操作

OK，本章我们介绍一些 git 的基本操作，这些操作基本是很常用的。



## 初始化和克隆 git 仓库

### git init

用 `git init` 在目录中创建新的 Git 仓库。 你可以在任何时候、任何目录中这么做，完全是本地化的。

在目录中执行 git init，就可以创建一个 Git 仓库了。比如说我们创建一个 w3cschoolcc 项目：

```
$ mkdir w3cschoolcc
$ cd w3cschoolcc
$ git init
Initialized empty Git repository in /www/w3cschoolcc/.git/
# 在 /www/w3cschoolcc/.git/ 目录初始化空 Git 仓库完毕。
```

现在你可以看到在你的项目目录中有个 .git 的子目录。 这就是你的 Git 仓库了，所有有关你的此项目的快照数据都存放在这里。

```
ls -a
.    ..  .git
```

### git clone


使用 git clone 拷贝一个 Git 仓库到本地，让自己能够查看该项目，或者进行修改。

如果你需要与他人合作一个项目，或者想要复制一个项目，看看代码，你就可以克隆那个项目。 执行命令：

```
git clone [url]
```

[url] 为你想要复制的项目，就可以了。

例如我们克隆 Github 上的项目：

```
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
```

上述操作将复制该项目的全部记录。

```
$ ls -a
.        ..       .git     README   Rakefile lib
$ cd .git
$ ls
HEAD        description info        packed-refs
branches    hooks       logs        refs
config      index       objects
```

默认情况下，Git 会按照你提供的 URL 所指示的项目的名称创建你的本地项目目录。 通常就是该 URL 最后一个 / 之后的项目名称。如果你想要一个不一样的名字， 你可以在该命令后加上你想要的名称。





## 将快照提交到暂存区


### git add


git add 命令可将该文件添加到缓存，也就是暂存区。

比如我们先添加以下两个文件：

```
$ touch README
$ touch hello.php
$ ls
README      hello.php
$ git status -s
?? README
?? hello.php
$
```

`git status` 命令用于查看项目的当前状态，可见，当前添加的两个文件还都是 ?? 。

然后执行 `git add` 命令来把刚刚添加的两个文件添加到暂存区：

```
$ git add README hello.php
```

现在我们再执行 git status，就可以看到这两个文件已经加到暂存区了。

```
$ git status -s
A  README
A  hello.php
$
```


在新项目中，添加所有文件很普遍，可以在当前工作目录执行命令：`git add .` 。<span style="color:red;">是不是什么情况下都可以使用这个 git add . ?我好像不管是添加还是深处都是用的这个 git add .好像也没有问题？确认下。</span>

OK，现在我们修改个文件，再执行一下 git status：


```
$ vim README
$ git status -s
AM README
A  hello.php
```
可见，README 状态变成 AM 了，"AM" 状态的意思是，这个文件在我们将它添加到缓存之后又有改动。

这个时候，我们可以再次执行 git add 命令将它添加到暂存区：

```
$ git add .
$ git status -s
A  README
A  hello.php
```

也就是说，当你要将你的修改包含在即将提交的快照里的时候，需要执行 git add 先把修改提交到暂存区。


### git status


`git status` 可以以查看在你上次提交之后是否有修改。

加 -s 参数，以获得简短的结果输出。如果没加该参数会详细输出内容：


```
$ git status
On branch master

Initial commit

Changes to be committed:
(use "git rm --cached <file>..." to unstage)

new file:   README
new file:   hello.php
```





### git diff

执行 `git diff` 来查看执行 git status 的结果的详细信息。

git diff 命令会显示：暂存区 与 被修改了但是尚未写入暂存区 的改动之间的区别。

git diff 有两个主要的应用场景。

```
* 尚未缓存的改动：git diff
* 查看已缓存的改动： git diff --cached
* 查看已缓存的与未缓存的所有改动：git diff HEAD
* 显示摘要而非整个 diff：git diff --stat
```

在 hello.php 文件中输入以下内容：

```
<?php echo 'www.w3cschool.cn'; ?>
```


```
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
```

<span style="color:red;">感觉上面这个 git diff 的内容还是有些不是很清楚，再确认下。</span>

git status 显示你上次提交更新至后所更改或者写入缓存的改动， 而 git diff 一行一行地显示这些改动具体是啥。

接下来我们来查看下 `git diff --cached` 的执行效果：<span style="color:red;">没看明白？这个 cached 为什么好像是 暂存区与版本库之间的区别 ？</span>

```
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
```





### git commit

使用 `git add` 命令将想要快照的内容写入了缓存， 而执行 `git commit` 把缓存区的快照提交到版本库。

Git 为你的每一个提交都记录你的名字与电子邮箱地址，所以第一步需要配置用户名和邮箱地址。


```
$ git config --global user.name 'w3cschool'
$ git config --global user.email w3c@w3cschool.cn
```

接下来我们写入缓存，并提交对 hello.php 的所有改动。在首个例子中，我们使用 -m 选项以在命令行中提供提交注释。


```
$ git add hello.php
$ git status -s
A  README
A  hello.php
$ git commit -m 'test comment from w3cschool.cn'
[master (root-commit) 85fc7e7] test comment from w3cschool.cn
2 files changed, 4 insertions(+)
create mode 100644 README
create mode 100644 hello.php
```


现在我们已经记录了快照。如果我们再执行 git status:


```
$ git status
# On branch master
nothing to commit (working directory clean)
```



以上输出说明我们在最近一次提交之后，没有做任何改动，是一个"干净的工作目录"。

如果你没有设置 -m 选项，Git 会尝试为你打开一个编辑器以填写提交信息。 如果 Git 在你对它的配置中找不到相关信息，默认会打开 vim。屏幕会像这样：

```
# Please enter the commit message for your changes. Lines starting # with '#' will be ignored, and an empty message aborts the commit. # On branch master # Changes to be committed: # (use "git reset HEAD ..." to unstage) # # modified: hello.php # ~ ~ ".git/COMMIT_EDITMSG" 9L, 257C
```

如果你觉得 git add 提交缓存的流程太过繁琐，Git 也允许你用 -a 选项跳过这一步。命令格式如下：

```
git commit -a
```

如：

```
$ git commit -am 'changes to hello file'
[master 78b2670] changes to hello file
 1 files changed, 2 insertions(+), 1 deletions(-)
```

<span style="color:red;">竟然是-am ，怪不得，我之前使用的是 -a 和 -m 好像是不行的。好的。</span>


### git reset HEAD

git reset HEAD 命令用于取消缓存已缓存的内容。<span style="color:red;">原来是这个作用，之前在 讲暂存区的那一张我看了这个 reset HEAD 还在想什么时候会使用到呢，原来是这样，那么应该还是会经常使用到的。</span>

这里我们有两个最近提交之后又有所改动的文件。我们将两个都缓存，并取消缓存其中一个。

```
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
```

<span style="color:red;">竟然还可以指定某个文件来reset，不错。</span>

<span style="color:red;">可以看到 reset 之后，hello.php 变成红色的 M 。</span>

现在你执行 `git commit` 将只记录 README 文件的改动，并不含现在并不在缓存中的 hello.php。<span style="color:red;">嗯。这个功能还是不错的。比如说 add 错了一个文件，还可以把他撤销了再修改下。</span>

### git rm

`git rm` 可以把文件从缓存区中移除。<span style="color:red;">是这样吗？为什么我试验的时候，必须已经 commit 的才能删除？这个时候不是从版本库里面删除的吗？到底缓存区是什么？与之前将的暂存区有区别吗？</span>

如我们删除 hello.php 文件：

```
$ git rm hello.php
rm 'hello.php'
$ ls
README
```


默认情况下，`git rm file` 会将文件从缓存区和你的硬盘中（工作目录）删除。 如果要在工作目录中留着该文件，可以使用命令：

```
git rm --cached
```


### git mv

`git mv` 命令做得所有事情就是 `git rm --cached`， 重命名磁盘上的文件，然后再执行 `git add` 把新文件添加到缓存区。因此，虽然有 `git mv` 命令，但它有点多余 。好吧，还有这样的。



# REF

- [Git教程](https://www.w3cschool.cn/git/)
