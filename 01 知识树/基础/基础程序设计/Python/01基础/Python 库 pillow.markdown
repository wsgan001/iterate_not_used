# TODO
- 关于 pillow 还是要总结下的。


# REF
安装一个别人开发的django 博客的时候，需要用到 pillow，因此记录一下 pillow 在安装之前需要用到的一些 c 库的安装。




# pillow 安装
## 安装环境
  * CentOS 7
## 指令
```text
yum install python-devel  **#注意：根据版本不同是python34-devel  、python36.devel 等。**
yum install zlib-devel
yum install libjpeg-turbo-devel
yum install libpng-devel
pip install pillow  **# 注意，这个pip 也与 python 版本有关 最好在 virtualenv 里面。**
```

应该没有安装全，完整的看 [External Libraries](https://pillow.readthedocs.io/en/3.1.x/installation.html#building-on-linux) ，但是现在安装的也是可以用的。







# 问题 PIL: DLL load failed: specified procedure could not be found
在 运行 `from PIL import Image` 的时候，提示说有个问题：PIL: DLL load failed: specified procedure could not be found 。这个问题之前好像遇到过，现在又遇到了，之前好像是不知道怎么样就OK了，这次有搜了下：
有人说他是 Python 3.6 的时候，遇到的问题，然后他把 pillow 4.1.0 卸载了重新安装了 4.0.0 就可以了。
我也是 Python3.6 也安装了 pillow4.1.0，因此我卸了重装了下：

```python
pip uninstall pillow
pip install pillow==4.0.0
```

这样就OK了。



# REF
  1. [CentOS7，Python3.6安装pillow](https://blog.csdn.net/chenlou123/article/details/53403952)
  2. [centos安装Pillow](https://blog.csdn.net/bwlab/article/details/51281390)
  3. [External Libraries](https://pillow.readthedocs.io/en/3.1.x/installation.html#building-on-linux)
  4. [PIL: DLL load failed: specified procedure could not be found](https://stackoverflow.com/questions/43264773/pil-dll-load-failed-specified-procedure-could-not-be-found)
