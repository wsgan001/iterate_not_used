

# Git 远程仓库

## Git 远程仓库介绍


Git 并不像 SVN 那样有个中心服务器。<span style="color:red;">SVN 是需要中心服务器吗？嗯，那么看来的确不一样。</span>

目前我们使用到的 Git 命令都是在本地执行，如果你想通过 Git 分享你的代码或者与其他开发人员合作。 你就需要将数据放到一台其他开发人员能够连接的服务器上。

本例使用了 Github 作为远程仓库。






## 添加远程库


要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，命令格式如下：

```
git remote add [shortname] [url]
```

本例以Github为例作为远程仓库，如果你没有Github可以在官网[https://github.com/](https://github.com/)注册。

由于你的本地Git仓库和GitHub仓库之间的传输是通过 SSH 加密的，所以我们需要配置验证信息：

使用以下命令生成SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```


后面的 your_email@youremail.com 改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开 id_rsa.pub，复制里面的key。

回到 github 上，进入 Account Settings（账户配置），左边选择 SSH Keys，Add SSH Key，title 随便填，粘贴在你电脑上生成的key。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180710/gDeHfbjHEc.png?imageslim)

为了验证是否成功，输入以下命令：


```
$ ssh -T git@github.com
Hi WongJay! You've successfully authenticated, but GitHub does not provide shell access.
```



以下命令说明我们已成功连上 Github。

之后登录后点击" New repository " 如下图所示：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180710/kbhaEgLl41.png?imageslim)


之后在在Repository name 填入 w3cschool.cn(远程仓库名) ，其他保持默认设置，点击"Create repository"按钮，就成功地创建了一个新的Git仓库：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180710/Gml8l4cb9c.png?imageslim)


创建成功后，显示如下信息：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180710/IE6efAE57L.png?imageslim)


以上信息告诉我们可以从这个仓库克隆出新的仓库，也可以把本地仓库的内容推送到GitHub仓库。

现在，我们根据GitHub的提示，在本地的仓库下运行命令：

```
$ ls
README
W3Cschool教程测试.txt
test.txt
$ git remote add origin git@github.com:WongJay/w3cschool.cn.git
$ git push -u origin master
Counting objects: 21, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (21/21), 1.73 KiB | 0 bytes/s, done.
Total 21 (delta 4), reused 0 (delta 0)
To git@github.com:WongJay/w3cschool.cn.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```


以下命令请根据你在Github成功创建新仓库的地方复制，而不是根据我提供的命令，因为我们的Github用户名不一样，仓库名也不一样。

接下来我们返回 Github 创建的仓库，就可以看到文件已上传到Github上：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180710/406g3CFh68.png?imageslim)





## 查看当前的远程库


要查看当前配置有哪些远程仓库，可以用命令 `git remote`：<span style="color:red;">还可以这样！</span>

```
$ git remote
origin
$ git remote -v
origin  git@github.com:WongJay/w3cschool.cn.git (fetch)
origin  git@github.com:WongJay/w3cschool.cn.git (push)
```

执行时加上 `-v` 参数，你还可以看到每个别名的实际链接地址。





## 提取远程仓库

Git 有两个命令用来提取远程仓库的更新。

1、从远程仓库下载新分支与数据 `git fetch`，该命令执行完后需要执行 `git merge` 远程分支到你所在的分支。<span style="color:red;">哦，原来单独的git fetch 是不行的。</span>

2、从远端仓库提取数据并尝试合并到当前分支：`git pull`。该命令就是在执行 git fetch 之后紧接着执行 git merge 远程分支到你所在的任意分支。<span style="color:red;">嗯。</span>

假设你配置好了一个远程仓库，并且你想要提取更新的数据，你可以首先执行 **git fetch [alias]** 告诉 Git 去获取它有你没有的数据，然后你可以执行 **git merge [alias]/[branch]** 以将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支。

接下来我们在 Github 上点击"w3cschoolW3Cschool教程测试.txt" 并在线修改它。之后我们在本地更新修改。

```
$ git fetch origin
Warning: Permanently added the RSA host key for IP address '192.30.252.128' to the list of known hosts.
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:WongJay/w3cschool.cn
   7d2081c..f5f3dd5  master     -> origin/master
```



以上信息"7d2081c..f5f3dd5 master -> origin/master" 说明 master 分支已被更新，我们可以使用以下命令将更新同步到本地：<span style="color:red;">origin 到底是什么？</span>

```
$ git merge origin/master
Updating 7d2081c..f5f3dd5
Fast-forward
 "w3cschool\350\217\234\351\270\237\346\225\231\347\250\213\346\265\213\350\257\225.txt" | 1 +
 1 file changed, 1 insertion(+)
```



## 推送到远程仓库


推送你的新分支与数据到某个远端仓库命令:

```
git push [alias] [branch]
```



以上命令将你的 [branch] 分支推送成为 [alias] 远程仓库上的 [branch] 分支，实例如下。<span style="color:red;">origin 到底是什么？</span>

```
$ git merge origin/master
Updating 7d2081c..f5f3dd5
Fast-forward
 "w3cschool\350\217\234\351\270\237\346\225\231\347\250\213\346\265\213\350\257\225.txt" | 1 +
 1 file changed, 1 insertion(+)
bogon:w3cschoolcc WongJay$ vim w3cschoolW3Cschool教程测试.txt
bogon:w3cschoolcc WongJay$ git push origin master
Everything up-to-date
```



## 删除远程仓库


删除远程仓库你可以使用命令：

```
git remote rm [别名]
```


```
$ git remote -v
origin  git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)
$ git remote add origin2 git@github.com:WongJay/w3cschool.cn.git
$ git remote -v
origin   git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)
origin2   git@github.com:WongJay/w3cschool.cn.git (fetch)
origin2  git@github.com:WongJay/w3cschool.cn.git (push)
$ git remote rm origin2
$ git remote -v
origin  git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)
```

<span style="color:red;">可以有两个 origin 吗？</span>


# REF

- [Git教程](https://www.w3cschool.cn/git/)
