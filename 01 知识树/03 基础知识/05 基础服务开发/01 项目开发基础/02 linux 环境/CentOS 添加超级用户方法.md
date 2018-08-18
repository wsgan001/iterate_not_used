# CentOS 添加超级用户方法


TODO

* **需要再补充全面一下。**



# 添加一个普通用户

* #adduser junguoguo//添加一个名为junguoguo的用户
* #passwd junguoguo //修改密码


# 赋予 root 权限


修改 /etc/sudoers 文件，找到下面一行，在root下面添加一行，如下所示：

```
## Allow root to run any commands anywhere
root ALL=(ALL) ALL
junguoguo ALL=(ALL) ALL
```

修改完毕，现在可以用 junguoguo 帐号登录，然后用命令 **su –** ，即可获得root权限进行操作。


## REF

1. [CentOS添加Root权限(超级用户)用户方法|su,sudo命令详解（转）](https://my.oschina.net/u/559845/blog/78293)
