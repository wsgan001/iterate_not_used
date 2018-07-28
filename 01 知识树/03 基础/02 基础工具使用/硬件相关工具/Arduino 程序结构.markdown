---
author: evo
comments: true
date: 2018-05-05 06:56:17+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e7%a8%8b%e5%ba%8f%e7%bb%93%e6%9e%84/
slug: arduino-%e7%a8%8b%e5%ba%8f%e7%bb%93%e6%9e%84
title: Arduino 程序结构
wordpress_id: 5262
categories:
- 基础工具使用
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1. [Arduino教程](https://www.w3cschool.cn/arduino/)




# TODO






  * aaa




# MOTIVE






  * aaa





* * *



在本章中，我们将深入研究Arduino程序结构，并将学习更多Arduino世界中使用的新术语。Arduino软件是开源的。Java环境的源代码在GPL下发布，C/C++微控制器库在LGPL下。

**Sketch（草图）** - 第一个新的术语是名为“**sketch**”的Arduino程序。


## 结构


Arduino程序可以分为三个主要部分：**结构，值**（变量和常量）和**函数**。在本教程中，我们将逐步了解Arduino软件程序，以及如何编写程序而不会出现任何语法或编译错误。

让我们从**结构**开始。软件结构包括两个主要函数：

  * Setup()函数
  * Loop()函数


结构

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/A2DLGHfc52.png?imageslim)






```
Void setup ( ) {

}
```






  * **PURPOSE**- 草图启动时会调用** setup()**函数。使用它来初始化变量，引脚模式，启用库等。setup函数只能在Arduino板的每次上电或复位后运行一次。


  * **INPUT **- -


  * **OUTPUT **- -


  * **RETURN**- -




    Void Loop ( ) {

    }







  * **PURPOSE**- 在创建了用于初始化并设置初始值的**setup()**函数后，**loop()** 函数，正如它的名称所指，允许你的程序连续循环的更改和响应。可以使用它来主动控制Arduino板。


  * **INPUT **- -


  * **OUTPUT **- -


  * **RETURN**- -
























* * *





# COMMENT
