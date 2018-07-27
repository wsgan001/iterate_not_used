---
author: evo
comments: true
date: 2018-05-05 07:08:36+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e5%ad%97%e7%ac%a6%e4%b8%b2%e5%af%b9%e8%b1%a1/
slug: arduino-%e5%ad%97%e7%ac%a6%e4%b8%b2%e5%af%b9%e8%b1%a1
title: Arduino 字符串对象
wordpress_id: 5266
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



在Arduino编程中使用的第二种类型的字符串是字符串对象。


## 什么是对象？


对象是一个包含数据和函数的构造。字符串对象可以像变量一样被创建并分配一个值或字符串。字符串对象包含函数（在面向对象编程（OOP）中称为“方法”），它们对字符串对象中包含的字符串数据进行操作。

下面的草图和解释将清楚说明对象是什么，以及如何使用字符串对象。


### 例子



    
    void setup() { 
       String my_str = "This is my string.";
       Serial.begin(9600);
    
       // (1) print the string
       Serial.println(my_str);
    
       // (2) change the string to upper-case
       my_str.toUpperCase();
       Serial.println(my_str);
    
       // (3) overwrite the string
       my_str = "My new string.";
       Serial.println(my_str);
    
       // (4) replace a word in the string
       my_str.replace("string", "Arduino sketch");
       Serial.println(my_str);
    
       // (5) get the length of the string
       Serial.print("String length is: ");
       Serial.println(my_str.length());
    }
    
    void loop() { 
    
    }




### 结果



    
    This is my string.
    THIS IS MY STRING.
    My new string.
    My new Arduino sketch.
    String length is: 22


创建字符串对象，并在草图顶部分配一个值（或字符串）。

    
    String my_str = "This is my string." ;


这将创建一个名为** my_str **的String对象，并为其赋值“This is my string.”。

这可以与创建变量并为其分配一个值（如整数）相比较：

    
    int my_var = 102;


以上草图以下列方式工作。


### （1）打印字符串


字符串可以像字符数组字符串一样打印到串口监视器窗口。


### （2）将字符串转换为大写


创建的字符串对象my_str，有多个可以在其上操作的函数或方法。这些方法通过使用对象名称后跟点运算符(.)，然后使用函数的名称来调用的。

    
    my_str.toUpperCase();
    


**toUpperCase()**函数对包含在类型为String的** my_str **对象中的字符串进行操作，并将对象包含的字符串数据（或文本）转换为大写字符。String类包含的函数列表可以在Arduino字符串参考中找到。从技术上讲，String被称为一个类，用于创建String对象。


### （3）覆盖字符串


赋值运算符用于将新字符串分配给** my_str **对象以替换旧字符串。

    
    my_str = "My new string." ;
    


赋值运算符不能用于字符数组字符串，仅适用于String对象。


### （4）替换字符串中的单词


replace()函数用于将传递给它的第二个字符串替换传递给它的第一个字符串。replace()是构建在String类中的另一个函数，因此可以在String对象my_str上使用。


### （5）获取字符串的长度


通过使用length()可以很容易地获取字符串的长度。在示例草图中，由length()返回的结果直接传递到Serial.println()，而不使用中间变量。


## 何时使用字符串对象


字符串对象比字符串字符数组更容易使用。该对象具有内置函数，可以对字符串执行多个操作。

使用String对象的主要缺点是，它使用了大量的内存，可能会很快耗尽Arduino的RAM内存，这可能会导致Arduino挂起，崩溃或行为意外。如果Arduino上的草图很小并限制了对象的使用，那么应该没有问题。

字符数组字符串更难使用，你可能需要编写自己的函数来操作这些类型的字符串。其优点是，你可以控制字符串数组的大小，因此你可以保持数组很小来节省内存。

你需要确保不要超出字符串数组边界的范围，而String对象没有这个问题，只要有足够的内存供它操作，就会照顾到你的字符串边界。在内存不足时，String对象可以尝试在不存在的内存中写入，但绝不会在超出其操作的字符串末尾的地方写入。


### 在哪里使用字符串


在本章中，我们学习了字符串，它们在内存中的行为及其操作。

字符串的用法将在课程的下一部分进行介绍，届时我们将学习如何从串口监视器窗口获取用户输入并将输入保存为字符串。























* * *





# COMMENT



