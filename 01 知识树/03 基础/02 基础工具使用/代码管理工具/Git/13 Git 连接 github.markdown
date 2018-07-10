TODO:

- 后续应该没有什么需要添加的了。

---


# Git 连接 github


## 首先，进行 Git 初识的设置

设置 git 用户名/邮箱

```
git config --global user.name xxxxxx
git config --global user.email xxxxxx
```

对于与 github 连接的项目来说，这个地方就可以写自己的 github 账号和注册 github 时候用的邮箱。到时候 push 上去的时候，github上回根据这个用户名来对应不同的账户头像。

注意：这个地方要注意：连接 github 的时候并不需要把 github 的密码保存在设置中，这个是比较特殊的，他是使用一个 key 来核对你是否有 push 的权限的。 这个在后面会说。

补充：可以使用 `vim .git/config` 修改config文件，使用 `git config --list` 查看配置

## 然后，初始化 git

```
cd testfolder
git init
```
这样就在那个文件夹里初始化好了 git 。


## 然后，开始连接远处的 github 账户

```
git remote add origin git@github.com:(用户名)/版本库名
```
说明：上面这个 `git@github.com:(用户名)/版本库名` 是在github 界面上，点击 Clone or download 的时候，弹出的界面右上角有个 **SSH** ，点击，会切换到 **SSH** 的地址，就是这个地址。**注意，不是 http 的地址。**

比如，我的是：`git@github.com:iterateself/iterate.git`

OK，那么如果之前已经是使用的 git clone http地址 ，那么怎么办呢？可以使用下面的语句删除之前的提交方式：

```
git remote rm origin
```

推荐使用 SSH 地址还有一个原因：直接使用 `git clone https://github.com/iterateself/iterate.git` 的话，每次push都要输入一遍密码，**即使是使用了 key **。如果采用的是 SSH 方式，只需要在版本库中添加用户的 sha 的 key 就可以实现提交时无需输入用户名和密码。


## 然后，生成 key


我们先尝试使用下面指令提交代码：

```
git push -u origin master
```


系统会提示：

```
The authenticity of host 'github.com (192.30.252.131)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.252.131' (RSA) to the list of known h                             osts.
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
```


说明你权限不够。所以这时你需要在本地创建自己的 RSA 的 key。在当前的git 窗口运行：

```
ssh-keygen -t rsa -C "用户名"
```

说明：这个用户名就是之前的 user.name 的名字。

然后系统会问你保存路径等东西，直接 enter 默认就行：

```
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/AlexYi/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```


然后系统会生成一些东西：

```
Your identification has been saved in /c/Users/AlexYi/.ssh/id_rsa.
Your public key has been saved in /c/Users/AlexYi/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:rxfK05d7oZWpDvQ5dRQM0Na7...
The key's randomart image is:
+---[RSA 2048]----+
|           .o.+. |
|             o o.|
|            .   o|
|               o |
...
```


最主要的是告诉你，你的可以在：

```
Your public key has been saved in /c/Users/AlexYi/.ssh/id_rsa.pub
```

**注：CentOS上生成的默认位置是：/root/.ssh/id_rsa.pub**

找到这个文件，然后用记事本打开，文本的内容就是你的 key ：


```
ssh-rsa AAAAB3NzaC1yc2EAAAADA...
```




## 把生成的 key 放到 github 上


然后将生成的 rsa 的 key 添加到版本库中即可，方法：

打开自己的版本库，点击右边的 Settings 进入配置页。 然后点击左边导航栏的： Deploy keys 进入添加key页面 ，然后点击： Add deploy keys ，将自己的内容输入进去就可以了。 这样就完成了。

最后继续提交更改的代码，使用：

```
git push -u origin master
```


可以提交成功。


## 补充


如果要使用 git push简短提交代码：

需要配置 :

```
git config --global push.default simple
```

或者：

```
git config --global push.default matching
```


区别在于，前者只提交你当前所在的分支，而后者会提交本地所有的分支。
