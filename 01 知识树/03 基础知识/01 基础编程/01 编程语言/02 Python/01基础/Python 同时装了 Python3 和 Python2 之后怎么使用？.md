---
author: evo
comments: true
date: 2018-06-01 23:55:02+00:00
layout: post
link: http://106.15.37.116/2018/06/02/python-%e5%90%8c%e6%97%b6%e8%a3%85%e4%ba%86-python3-%e5%92%8c-python2-%e4%b9%8b%e5%90%8e%e6%80%8e%e4%b9%88%e4%bd%bf%e7%94%a8%ef%bc%9f/
slug: python-%e5%90%8c%e6%97%b6%e8%a3%85%e4%ba%86-python3-%e5%92%8c-python2-%e4%b9%8b%e5%90%8e%e6%80%8e%e4%b9%88%e4%bd%bf%e7%94%a8%ef%bc%9f
title: Python 同时装了 Python3 和 Python2 之后怎么使用？
wordpress_id: 7338
categories:
- 基础程序设计
tags:
- python
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. 


[同时装了Python3和Python2，怎么用pip？](https://www.zhihu.com/question/21653286)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 同时装了 Python3 和 Python2 后，怎么运行 python 程序？




## 使用 py






这个问题几年以前 Python 社区就给出了官方解决方案，只不过国内一直没有注意到罢了。

我们在安装Python3（>=3.3）时，Python的安装包实际上在系统中安装了一个启动器py.exe，默认放置在文件夹 C:\Windows\ 下面。这个启动器允许我们指定使用 Python2 还是 Python3 来运行代码（当然前提是你已经成功安装了 Python2 和 Python3）。

如果你有一个 Python 文件叫 hello.py，那么你可以这样用 Python2 运行它



 	
  * py -2 hello.py


类似的，如果你想用Python3运行它，就这样

 	
  * py -3 hello.py




## **去掉参数 -2/-3**


每次运行都要加入参数-2/-3还是比较麻烦，所以py.exe这个启动器允许你在代码中加入说明，表明这个文件应该是由python2解释运行，还是由python3解释运行。说明的方法是在代码文件的最开始加入一行：



 	
  * #! python2


或者

 	
  * #! python3


分别表示该代码文件使用Python2或者Python3解释运行。这样，运行的时候你的命令就可以简化为

 	
  * py hello.py





# 同时装了 Python3 和 Python2 后的 pip 使用


当 Python2 和 Python3 同时存在于windows上时，它们对应的 pip 都叫 pip.exe，所以不能够直接使用 pip install 命令来安装软件包。而是要使用启动器 py.exe 来指定 pip 的版本。命令如下：



 	
  * py -2 -m pip install XXXX


-2 还是表示使用 Python2，-m pip 表示运行 pip 模块，也就是运行pip命令了。如果是为Python3安装软件，那么命令类似的变成

 	
  * py -3 -m pip install XXXX





即：

    
    ## 对于 Linux ##
    sudo pip install sth
    # 或者明确版本
    sudo pip2 install sth
    sudo pip3 install sth
    sudo python2 -m pip install sth
    sudo /path/to/python -m pip install sth
    
    ## 对于 Windows NT ##
    
    # 如果仅安装 python2
    pip install sth
    # 如果安装有 python3, 则需要明确 pip 版本
    py -2 -m pip install sth
    py -3 -m pip install sth
    
    作者：Johnny Wong
    链接：https://www.zhihu.com/question/21653286/answer/96834584
    来源：知乎
    著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。






















* * *





# COMMENT



