TODO

* 对于 Anaconda 的使用也要总结一下，之前一直没有系统的全面的额使用 Anaconda。
* 到底使用 pip 还是用 Anaconda？什么时候使用什么？
* Linux 上Anaconda 怎么用？



# Anaconda 的使用





# 环境的安装和设置

使用 Anaconda ，Python 3.6 版



译者：针对不同的操作系统，选择相应的下载方式。本书中关于安装过程的描述还是比较简单的，推荐大家直接看下面我给出的几篇文章。

- [Anaconda使用总结](http://www.jianshu.com/p/2f3be7781451)
- [Windows下Anaconda的安装和简单使用](http://blog.csdn.net/DQ_DM/article/details/47065323)
- [初学Python者自学anaconda的正确姿势是什么](https://www.zhihu.com/question/58033789)





# 下载和更新Python库

可以使用conda和pip两种工具进行库的下载和更新：

```
conda install package_name
```

但有时候一些库不在Anaconda的服务器上，上面的命令会失败。这个时候我们可以使用pip（pip是一个python的包管理工具）：

```
pip install package_name
```

conda更新：

```
conda update package_name
```

pip更新：

```
pip install --upgrade package_name
```

这两个下载方式都可以用，不会冲突的。不过不要使用pip来更新用conda下载的包，这会导致库之间的依赖出现问题。所以在使用Anaconda的时候，最好先尝试使用conda来更新，不行的话再使用pip。

