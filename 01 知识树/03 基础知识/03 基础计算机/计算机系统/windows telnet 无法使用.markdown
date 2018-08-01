# windows telnet 无法使用

# 缘由：




想试一下 telnet api.dropboxapi.com 443 但是：

'telnet' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

然后发现这个telnet的功能需要打开。之前有遇到过，这次总结下


# 要点：


进入控制面板，点击“打开或关闭Windows功能”，找到"Telnet客户端"，勾选它，然后确定即可：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/J94IJLfkaD.png?imageslim)

安装完成后，然后进入cmd，telnet可以使用了。




# COMMENT：
