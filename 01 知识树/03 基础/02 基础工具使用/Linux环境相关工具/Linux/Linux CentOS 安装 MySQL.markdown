TODO

* **还是有很多疑问的**

---


# Linux CentOS 安装 MySQL


# 在安装之前

## 首先检查 MySQL 是否已安装

  * yum list installed | grep mysql

## 如果有的话 就全部卸载






  * yum -y remove +数据库名称





# 安装 MySQL




## MySQL 依赖 libaio，所以先要安装 libaio


**是这样吗？为什么有的教程不用装这个？**




  * yum search libaio # 检索相关信息


  * yum install libaio # 安装依赖包




## 准备好 Repository


下载 MySQL Yum Repository：**这个 noarch.rpm 是什么文件？一般来说我到哪里找这个地址？如果我想装 sqlite，那么地址应该怎么找？到网上找吗？还是说linux里面可以直接有命令查找到 对应的一些 repository 地址？**




  * wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm


添加 MySQL Yum Repository 到你的系统 repository 列表中：


  * yum localinstall mysql-community-release-el7-5.noarch.rpm


如果提示-bash: wget: 未找到命令，请先执行 yum install wget 安装 wget

验证下是否添加成功：**grep 的用法还是要总结下的。**




  * yum repolist enabled | grep "mysql.*-community.*"





## 查看 MySQL 版本






  * yum repolist all | grep mysql


可以看到 5.5、 5.7 版本是默认禁用的，因为现在最新的稳定版是 5.6


  * yum repolist enabled | grep mysql





## 安装 MySQL






  * yum install mysql-community-server


Yum 会自动处理 MySQL 与其他组件的依赖关系。遇到提示，输入 y 继续，执行完成会提示“完毕！”。

此时MySQL 安装完成，它包含了 mysql-community-server、mysql-community-client、mysql-community-common、mysql-community-libs 四个包。


## 查看安装好的 MySQL 信息






  * rpm -qi mysql-community-server.x86_64 0:5.6.24-3.el7


  * whereis mysql


可以看到 MySQL 的安装目录是 /usr/bin/


## 尝试启动和关闭 MySQL


启动 MySQL Server：




  * systemctl start mysqld


查看 MySQL Server 状态：


  * systemctl status mysqld


测试是否安装成功：


  * mysql


可以进入 mysql 命令行界面，输入 exit 然后回车，就会退出这个 mysql 命令行界面。

关闭 MySQL Server：


  * systemctl stop mysqld





# 一些设置




## 防火墙设置


**没明白？防火墙没有开，要打开吗？防火墙的作用是什么？**

远程访问 MySQL， 需开放默认端口号 3306：




  * firewall-cmd --permanent --zone=public --add-port=3306/tcp


  * firewall-cmd --permanent --zone=public --add-port=3306/udp


这样就开放了相应的端口。

重新加载防火墙：


  * firewall-cmd --reload




## MySQL 安全设置


服务器启动后，可以执行：




  * mysql_secure_installation;


此时输入 root 原始密码（初始化安装的话为空），接下来，为了安全，MySQL 会提示你重置数据库的原始密码，移除其他用户账号，禁用 root 远程登录，移除 test 数据库，重新加载 privilege 表格等，你只需输入 y 继续执行即可。

**至此，整个 MySQL 安装完成。**




# 设置外部可访问 MySQL


现在只能本机访问 mysql，如果要外部可以访问 mysql，那么可以这样：**什么叫外部可访问 mysql ？什么是外部？通过 Xshell 远程登陆到linux上是外部吗？还是说 在程序中远程登陆连接到 mysql 是外部？嗯，应该是程序中远程登陆上来是外部。确认下。**

首先，重启 mysql 服务，再次进入到mysql：




  * systemctl start mysqld


  * mysql -u root -p   **#****为什么这么写？意思是什么？**


现在已经在mysql 里面了，用查询语句查看 user 表：


  * show databases;


  * use mysql;


  * select host,user,password from user;


创建一个用户并授予最高的权限：**没看懂这一段，为什么要这么设置？不设置会怎样？句子中每个是什么意思？我到时候要怎么远程连接过来？**




  * grant all privileges on *.* to 'root'@'%' identified by 'lam7' with grant option;


  * flush privileges;     #让刚刚修改的权限生效


给root账户设置密码为 lam7 且 host 为 %，即外部任何主机均可访问。*.*这个本意是数据库名.表名，我们这里没有写任何表名也没有写数据库名，就是意思所有的数据库都可以用这个root账户访问。

再看一下 users：


  * select host,user,password from user;


这时候可以看到 多了一个user。

退出 mysql：按 Ctrl+C

关闭 数据库：


  * systemctl stop mysqld




如果需要使用外部工具链接 mysql，还需要关闭防火墙：**什么意思？之前不是开了端口了吗？为什么还要关闭防火墙？**




  * systemctl stop firewalld


然后查看状态：


  * systemctl status firewalld
























* * *





# COMMENT


**下面这个有用吗？要补充进来**


    # 使用root用户登录后创建新用户
    mysql> CREATE USER 'demouser'@'localhost' IDENTIFIED BY 'demopassword';

    # 授权
    mysql> GRANT ALL PRIVILEGES ON demodb.* to demouser@localhost;
    mysql> FLUSH PRIVILEGES;

    # 使用新创建的用户登录后创建数据库
    mysql> CREATE DATABASE demodb;



# REF

1. [CentOS 7 安装 MySQL](https://blog.csdn.net/SmallTankPy/article/details/75451645)
