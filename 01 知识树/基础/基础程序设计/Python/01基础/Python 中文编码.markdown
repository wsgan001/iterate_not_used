---
author: evo
comments: true
date: 2018-05-03 01:51:46+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-chinese/
slug: python-chinese
title: Python 中文编码
wordpress_id: 4960
categories:
- 基础程序设计
tags:
- Pycharm
- python
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)

 	
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)

 	
  3. 



# TODO





 	
  * a





* * *





# INTRODUCTION





 	
  * a











# Python2 中的中文编码




## 在 py 文件开头指定编码


Python2 中如果py文件中未指定编码，在涉及中文字符的时候，执行会报错：

    
    #!/usr/bin/python
    print "你好，世界";
    


输出：

    
      File "test.py", line 2
    SyntaxError: Non-ASCII character '\xe4' in file test.py on line 2, but no encoding declared; see http://www.python.org/peps/pep-0263.html for details
    


以上出错信息显示了我们未指定编码。

解决方法为：在文件开头加入下面任意一个即可：



 	
  * # -*- coding: UTF-8 -*-

 	
  * # coding=utf-8


例子如下：

    
    #!/usr/bin/python 
    # coding=utf-8
    
    print "你好，世界";


输出：

    
    你好，世界
    











## 使用 Pycharm 的时候的中文对应


如果你使用编辑器，同时需要设置好编辑器的编码，如 Pycharm 设置步骤：



 	
  * 进入 **file > Settings**，在输入框搜索 **encoding**。

 	
  * 找到 **Editor > File encodings**，将 **IDE Encoding** 和 **Project Encoding** 设置为 utf-8。


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5aea6b1362ac1.png)







# Python3 中的中文编码






Python3.X 的源码文件默认使用 utf-8 编码，所以可以正常解析中文，无需指定 UTF-8 编码。






















































* * *





# COMMENT



