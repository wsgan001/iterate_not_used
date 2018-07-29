---
author: evo
comments: true
date: 2018-03-20 13:19:07+00:00
layout: post
link: http://106.15.37.116/2018/03/20/python-module-package/
slug: python-module-package
title: python中的模块与包
wordpress_id: 490
categories:
- 随想与反思
tags:
- '@want_to_know'
- python
---

<!-- more -->


## 缘由：


第一次看到python中的package的概念的时候感觉还是挺惊奇的，一些要点记下：


## 要点：




### 1.包package中的__init__.py文件是什么作用？


包必须至少含有一个__int__.py文件，该文件的内容可以为空。用于标识当前文件夹是一个包，这个__int__.py 是初始化模块，from-import语句导入子包时需要它，可以在里面做一些初始化工作，也可以是空文件。__init__.py定义的属性直接使用   顶层包.子包   的方式导入，如，在目录a的 __init__.py 文件中定义init_db()方法，调用如下：

    
    from app import a
    
    a.init_db()




### 2.python导入模块的时候的搜索顺序是什么样的？


导入模块时，是按照 sys.path 变量的值搜索模块， sys.path 的值是包含每一个独立路径的列表，包含当前目录、python安装目录、PYTHONPATH环境变量，搜索顺序按照路径在列表中的顺序（一般当前目录优先级最高）。（这里面的PYTONPATH环境变量是什么？）

想看自己的Python路径，大家可以

    
    import sys
    print(sys.path)


如果你发现你在某个地方写的文件（包）import 错误，你就可以看看这个sys.path是否囊括了你那批文件的根目录。


### 3.import 语句的先后顺序是什么样的？因为用C++的时候总是被.h头文件的次序感到不确定


推荐按照下面的顺序导入模块，并且一般在文件首部导入所有的模块



 	
  * python标准库

 	
  * 第三方模块

 	
  * 应用程序自定义模块



