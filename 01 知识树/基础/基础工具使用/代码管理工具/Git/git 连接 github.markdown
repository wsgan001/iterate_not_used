



# 缘由


我直接从我的 github 上把项目clone 下来，然后在这个基础上 修改提交，但是每次 push 都要输一遍密码，这样还是有些麻烦的，怎么才能直接提交，不用重复输入密码呢？


# 问题解答




## 原因


每次都需要输入用户名和密码是因为你采用的是 https 方式提交代码， 如果采用的是 ssh 方式只需要在版本库中添加用户的 sha 的key就可以实现提交时无需输入用户名和密码。


##
步骤 1
如果你的版本库已经用 https 方式创建好了，那么就需要在 git 上先删除原来的提交方式：
  * git remote rm origin
然后执行：
  * git remote add origin git@github.com:(用户名)/版本库名


注：上面这个 git@github.com:(用户名)/版本库名 是在github 界面上，点击Clone or download 的时候，弹出的界面右上角有 个 SSH ，点击，会切换到 SSH的地址，就是这个地址。




## 步骤 2


然后这个时候你使用下面指令提交代码：




  * git push -u origin master


系统会提示：


    The authenticity of host 'github.com (192.30.252.131)' can't be established.
    RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'github.com,192.30.252.131' (RSA) to the list of known h                             osts.
    Permission denied (publickey).
    fatal: Could not read from remote repository.

    Please make sure you have the correct access rights


说明你权限不够。所以这时你需要在本地创建自己的 RSA 的 key。在当前的git 窗口运行：




  * ssh-keygen -t rsa -C "用户名"


然后系统会问你保存路径等东西，直接 enter 默认就行：


    Generating public/private rsa key pair.
    Enter file in which to save the key (/c/Users/AlexYi/.ssh/id_rsa):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:


然后系统会生成一些东西：


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


最主要的是告诉你，你的可以在：


    Your public key has been saved in /c/Users/AlexYi/.ssh/id_rsa.pub

**注：CentOS上生成的默认位置是：/root/.ssh/id_rsa.pub**

找到这个文件，然后用记事本打开，文本的内容就是你的 key ：


    ssh-rsa AAAAB3NzaC1yc2EAAAADA...




## 步骤 3


然后将生成的 rsa 的 key 添加到版本库中即可，方法：

打开自己的版本库，点击右边的 Settings 进入配置页。 然后点击左边导航栏的： Deploy keys 进入添加key页面 ，然后点击： Add deploy keys ，将自己的内容输入进去就可以了。 这样就完成了。

最后继续提交更改的代码，使用：




  * git push -u origin master


可以提交成功。


# 补充


如果要使用 git push简短提交代码：

需要配置 :




  * git config --global push.default simple


或者：


  * git config --global push.default matching


区别在于，前者只提交你当前所在的分支，而后者会提交本地所有的分支。







* * *





# COMMENT
