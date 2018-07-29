# Linux CentOS yum

# 报错 Another app is currently holding the yum lock

## 问题

yum -y update 之后，程序走到最后一个cleaning的时候不走了，然后我 Ctrl+Z 了，但是之后发现 yum 命令被占用了，报错：Another app is currently holding the yum lock。因此查了下，解决如下：


## 解决

可以通过强制关掉yum进程：

```
rm -f /var/run/yum.pid
```

然后就可以使用yum了。OK，很简单。


# REF

1. [centos在yum install报错：Another app is currently holding the yum lock解决方法](https://www.cnblogs.com/jincon/p/3371471.html)
