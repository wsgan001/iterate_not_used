# Linux CentOS 安装Django



# 目的

准备安装 django，并且是安装在 virtualenv 创建的虚拟环境里面的，这样最方便，安装完成后会尝试运行看一下。

# 一些需要安装的

## 安装 python

* 直接根据 [CentOS 安装python 3](http://106.15.37.116/2018/05/30/linux-centos-%e5%ae%89%e8%a3%85python-3/) 进行安装。

# 在虚拟环境里安装 django




## 创建项目所需的虚拟环境 venv






  1. cd ~


  2. mkdir ~/dj


  3. cd ~/dj


  4. virtualenv newenv --python=`which python3.6`


  5. source newenv/bin/activate


说明：


  * 1，2，3 我们先创建了一个新的文件夹 dj，这个文件夹，就是我们的总的 django 项目路径。


  * 4  在里面创建了一个 python 的虚拟环境 newenv，并在这里面安装了独立版本的 Python，以及 pip 。


  * 5  要想后续实用 python3.6 和安装包的时候是用的这个虚拟环境 newenv 里面的python3.6 而不是电脑上的 python3.6 ，那么我们就必须执行 5 把它激活。要想离开虚拟环境，可以从系统的任何位置发出命令：deactivate


在激活之后，你的提示应该已经发生了改变，以反映您现在处于虚拟环境中。它看起来像：  (newenv)username@hostname:~/newproject$


## 安装 django






  1. cd ~/dj


  2. pip install django


  3. django-admin --version


说明：


  * 2  由于是在虚拟环境里面进行安装的，因此不需要使用 sudo 。


  * 3  验证安装的版本




## 脱离与重新激活虚拟环境的指令






  1. deactivate


  2. cd ~/newproject


  3. source newenv/bin/activate


说明：


  * 1  要想离开虚拟环境，可以在系统的任何位置执行 deactivate，这是提示应恢复为传统显示。


  * 2，3 当希望再次处理项目时，可以回到项目目录并激活




# 一个示例项目




## 创建一个小项目






  * cd ~/dj


  * django-admin startproject testproject


  * cd testproject


说明：


  * 1，2，3 这将在 dj 中创建一个 testproject 文件夹。这个文件夹里面有一个 manage.py 的脚本 和一个也叫 testproject 文件夹，文件夹里才是真正的 code 。




## 进行一些配置


使用数据库（默认是 SQLite）：




  1. python manage.py migrate


创建一个管理员：


  1. python manage.py createsuperuser




## 可以跑起来了






  1. python manage.py runserver 0.0.0.0:8000 #可以通过 Ctrl+C 停止 server


在你的浏览器里输入地址： server_ip_address:8000  来访问。

注意：


  * server_ip_address 是你的服务器的外网地址


  * 如果网页显示：无法访问此网站 ，查看一下 服务器的防火墙有没有开放 8000 端口，


    * 如果是阿里云的服务器，可以直接在管理控制台找到你的服务器，然后，找到防火墙，然后，添加规则，应用类型：自定义，协议：TCP，端口范围：8000。设置好之后重新启动 server 即可。





  * 如果网页显示：DisallowedHost at Invalid HTTP_HOST header: 'xx.xx.xx.xx:8000'. You may need to add 'xx.xx.xx.xx' to ALLOWED_HOSTS. 那么直接：


    1. cd ~/dj/testproject/testproject


    2. vim setting.py  #修改了： ALLOWED_HOSTS = ['*']  即添加了＊


    3. 重新启动 server。





这时候，就能成功的访问到了Django的项目了，页面会显示：The install worked successfully! Congratulations!


## 登陆进去看一下


现在，追加 /admin 到你的网址末尾以进入管理员登录页面：




  1. server_ip_address:8000/admin


说明：


  * 1  输入刚刚创建的管理员用户名和密码，它应该会转到网站的管理员部分。


















* * *





# COMMENT


支持 unicode 全字符：不知道会不会用上：


    # 修改/etc/my.conf,修改或者添加以下配置,支持unicode全字符(即支持emoji)
    [client]
    default-character-set = utf8mb4
    [mysqld]
    character-set-server = utf8mb4
    collation-server = utf8mb4_unicode_ci
    default-storage-engine = INNODB




# REF

1. [How To Install the Django Web Framework on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-the-django-web-framework-on-centos-7)  非常好
2. [快速安装指南](https://docs.djangoproject.com/zh-hans/2.0/intro/install/)
3. [Django运行访问项目出现的问题:DisallowedHost at / Invalid HTTP_HOST header](https://blog.csdn.net/will5451/article/details/53861092)
