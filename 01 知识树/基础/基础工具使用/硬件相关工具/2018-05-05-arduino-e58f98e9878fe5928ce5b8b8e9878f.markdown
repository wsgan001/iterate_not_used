---
author: evo
comments: true
date: 2018-05-05 06:57:14+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e5%8f%98%e9%87%8f%e5%92%8c%e5%b8%b8%e9%87%8f/
slug: arduino-%e5%8f%98%e9%87%8f%e5%92%8c%e5%b8%b8%e9%87%8f
title: Arduino 变量和常量
wordpress_id: 5264
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



在我们开始解释变量类型之前，我们需要确定一个非常重要的主题，称为**变量范围**。


## 什么是变量范围？


Arduino使用的C语言中的变量具有名为scope（范围）的属性。scope是程序的一个区域，有三个地方可以声明变量。它们是：



 	
  * 在函数或代码块内部，称为**局部变量**。

 	
  * 在函数参数的定义中，称为**形式参数**。

 	
  * 在所有函数之外，称为**全局变量**。




### 局部变量


在函数或代码块中声明的变量是局部变量。它们只能由该函数或代码块中的语句使用。局部变量不能在它们自己之外运行。以下是使用局部变量的示例：

    
    Void setup () {
    
    }
    
    Void loop () {
       int x , y ;
       int z ; Local variable declaration
       x = 0;
       y = 0; actual initialization
       z = 10;
    }




### 全局变量


全局变量在所有函数之外定义，通常位于程序的顶部。全局变量将在程序的整个生命周期中保持其价值。

全局变量可以被任何函数访问。也就是说，一个全局变量可以在整个程序中声明后使用。

以下示例使用全局变量和局部变量：

    
    Int T , S ;
    float c = 0 ; Global variable declaration
    
    Void setup () {
    
    }
    
    Void loop () {
       int x , y ;
       int z ; Local variable declaration
       x = 0;
       y = 0; actual initialization
       z = 10;
    }
























* * *





# COMMENT



