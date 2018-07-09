---
author: evo
comments: true
date: 2018-05-05 06:56:55+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e6%95%b0%e6%8d%ae%e7%b1%bb%e5%9e%8b/
slug: arduino-%e6%95%b0%e6%8d%ae%e7%b1%bb%e5%9e%8b
title: Arduino 数据类型
wordpress_id: 5263
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



C中的数据类型是指用于声明不同类型的变量或函数的扩展系统。变量的类型确定它在存储器中占用多少空间以及如何解释存储的位模式。

下表提供了你将在Arduino编程期间使用的所有数据类型。
<table class="table table-bordered     " >
<tbody >
<tr >

<td >void
</td>

<td >Boolean
</td>

<td >char
</td>

<td >Unsigned char
</td>

<td >byte
</td>

<td >int
</td>

<td >Unsigned int
</td>

<td >word
</td>
</tr>
<tr >

<td >long
</td>

<td >Unsigned long
</td>

<td >short
</td>

<td >float
</td>

<td >double
</td>

<td >array
</td>

<td >String-char array
</td>

<td >String-object
</td>
</tr>
</tbody>
</table>


## 
void


void关键字仅用于函数声明。它表示该函数预计不会向调用它的函数返回任何信息。

**例子**

    
    Void Loop ( ) {
       // rest of the code
    }




## 




## Boolean


布尔值保存两个值之一，true或false。每个布尔变量占用一个字节的内存。

**例子**

    
    boolean val = false ; // declaration of variable with type boolean and initialize it with false
    boolean state = true ; // declaration of variable with type boolean and initialize it with false




## Char


一种数据类型，占用一个字节的内存，存储一个字符值。字符文字用单引号写成：'A'，对于多个字符，字符串使用双引号："ABC"。

但是，字符是存储为数字。你可以在[ASCII图表](https://www.arduino.cc/en/Reference/ASCIIchart)中查看特定编码。这意味着可以对使用ASCII值的字符进行算术运算。例如，'A'+1的值为66，因为大写字母A的ASCII值为65。

**例子**

    
    Char chr_a = ‘a’ ;//declaration of variable with type char and initialize it with character a
    Char chr_c = 97 ;//declaration of variable with type char and initialize it with character 97




![ASCII Char Table](https://img.w3cschool.cn/attachments/tuploads/arduino/ascii_char_table.jpg)




## 




## unsigned char


**unsigned char**是一种无符号数据类型，占用一个字节的内存。unsigned char数据类型编码数字为0到255。

**例子**

    
    Unsigned Char chr_y = 121 ; // declaration of variable with type Unsigned char and initialize it with character y




## 




## byte


一个字节存储一个8位无符号数，从0到255。

**例子**

    
    byte m = 25 ;//declaration of variable with type byte and initialize it with 25




## int


整数（int）是数字存储的主要数据类型。int存储16位（2字节）值。这产生-32768至32767的范围（最小值为-2^15，最大值为（2^15）-1）。

**int**的大小因板而异。例如，在Arduino Due中，**int**存储32位（4字节）值。这产生-2147483648至2147483647的范围（最小值-2^31和最大值（2^31）-1）。

**例子**

    
    int counter = 32 ;// declaration of variable with type int and initialize it with 32




## 




## Unsigned int


unsigned int（无符号整数）与int相同，存储2字节。然而，它们只存储正值，产生0到65535（2^16）-1的有效范围。Due存储4字节（32位）值，范围从0到4294967295（2^32-1）。

**例子**

    
    Unsigned int counter = 60 ; // declaration of variable with 
       type unsigned int and initialize it with 60




## 




## Word


在Uno和其他基于ATMEGA的板上，一个word存储一个16位无符号数。在Due和Zero上，它存储一个32位无符号数。

**例子**

    
    word w = 1000 ;//declaration of variable with type word and initialize it with 1000




## 




## Long


Long变量是用于数字存储的扩展大小变量，存储32位（4字节），从-2147483648到2147483647。

**例子**

    
    Long velocity = 102346 ;//declaration of variable with type Long and initialize it with 102346




## unsigned long


unsigned long变量是用于数字存储的扩展大小变量，并存储32位（4字节）。与标准的long不同，unsigned long不会存储负数，它们的范围为0到4294967295（2^32-1）。

**例子**

    
    Unsigned Long velocity = 101006 ;// declaration of variable with 
       type Unsigned Long and initialize it with 101006




## 




## short


short是16位数据类型。在所有Arduinos（基于ATMega和ARM）上，一个short存储一个16位（2字节）值。这产生-32768至32767的范围（最小值为-2^15，最大值为（2^15）-1）。

**例子**

    
    short val = 13 ;//declaration of variable with type short and initialize it with 13




## 




## float


浮点数的数据类型是具有小数点的数字。浮点数通常用于近似模拟值和连续值，因为它们的分辨率高于整数。

浮点数可以大到3.4028235E+38，也可以低到-3.4028235E+38。它们被存储为32位（4字节）信息。

**例子**

    
    float num = 1.352;//declaration of variable with type float and initialize it with 1.352




## 




## double


在Uno和其他基于ATMEGA的板上，双精度浮点数占用四个字节。也就是说，double实现与float完全相同，精度没有增益。在Arduino Due上，double具有8字节（64位）精度。

**例子**

    
    double num = 45.352 ;// declaration of variable with type double and initialize it with 45.352
























* * *





# COMMENT



