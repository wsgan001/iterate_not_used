---
author: evo
comments: true
date: 2018-05-02 13:18:37+00:00
layout: post
link: http://106.15.37.116/2018/05/02/cpp-data-type/
slug: cpp-data-type
title: C++ 数据类型
wordpress_id: 4861
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [C++ 教程 w3cschool](https://www.w3cschool.cn/cpp/)

 	
  2. [C++ 教程 菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## C++ 数据类型






使用编程语言进行编程时，需要用到各种变量来存储各种信息。变量保留的是它所存储的值的内存位置。这意味着，当您创建一个变量时，就会在内存中保留一些空间。

您可能需要存储各种数据类型（比如字符型、宽字符型、整型、浮点型、双浮点型、布尔型等）的信息，操作系统会根据变量的数据类型，来分配内存和决定在保留内存中存储什么。






## 基本的内置类型


C++ 为程序员提供了种类丰富的内置数据类型和用户自定义的数据类型。下表列出了七种基本的 C++ 数据类型：
<table class="reference notranslate " >
<tbody >
<tr >
类型
关键字
</tr>
<tr >

<td >布尔型
</td>

<td >bool
</td>
</tr>
<tr >

<td >字符型
</td>

<td >char
</td>
</tr>
<tr >

<td >整型
</td>

<td >int
</td>
</tr>
<tr >

<td >浮点型
</td>

<td >float
</td>
</tr>
<tr >

<td >双浮点型
</td>

<td >double
</td>
</tr>
<tr >

<td >无类型
</td>

<td >void
</td>
</tr>
<tr >

<td >宽字符型
</td>

<td >wchar_t
</td>
</tr>
</tbody>
</table>
一些基本类型可以使用一个或多个类型修饰符进行修饰：



 	
  * signed

 	
  * unsigned

 	
  * short

 	
  * long


下表显示了各种变量类型在内存中存储值时需要占用的内存，以及该类型的变量所能存储的最大值和最小值。
<table cellpadding="5" border="1" class="src" >
<tbody >
<tr >
类型
位宽度
范围
</tr>
<tr >

<td >char
</td>

<td >1 个字节
</td>

<td >-127 到 127 或者 0 到 255
</td>
</tr>
<tr >

<td >unsigned char
</td>

<td >1 个字节
</td>

<td >0 到 255
</td>
</tr>
<tr >

<td >signed char
</td>

<td >1 个字节
</td>

<td >-127 到 127
</td>
</tr>
<tr >

<td >int
</td>

<td >4 个字节
</td>

<td >-2147483648 到 2147483647
</td>
</tr>
<tr >

<td >unsigned int
</td>

<td >4 个字节
</td>

<td >0 到 4294967295
</td>
</tr>
<tr >

<td >signed int
</td>

<td >4 个字节
</td>

<td >-2147483648 到 2147483647
</td>
</tr>
<tr >

<td >short int
</td>

<td >2 个字节
</td>

<td >-32768 到 32767
</td>
</tr>
<tr >

<td >unsigned short int
</td>

<td >Range
</td>

<td >0 到 65,535
</td>
</tr>
<tr >

<td >signed short int
</td>

<td >Range
</td>

<td >-32768 到 32767
</td>
</tr>
<tr >

<td >long int
</td>

<td >4 个字节
</td>

<td >-2,147,483,647 到 2,147,483,647
</td>
</tr>
<tr >

<td >signed long int
</td>

<td >4 个字节
</td>

<td >与 long int 相同
</td>
</tr>
<tr >

<td >unsigned long int
</td>

<td >4 个字节
</td>

<td >0 到 4,294,967,295
</td>
</tr>
<tr >

<td >float
</td>

<td >4 个字节
</td>

<td >+/- 3.4e +/- 38 (~7 个数字)
</td>
</tr>
<tr >

<td >double
</td>

<td >8 个字节
</td>

<td >+/- 1.7e +/- 308 (~15 个数字)
</td>
</tr>
<tr >

<td >long double
</td>

<td >8 个字节
</td>

<td >+/- 1.7e +/- 308 (~15 个数字)
</td>
</tr>
<tr >

<td >wchar_t
</td>

<td >2 或 4 个字节
</td>

<td >1 个宽字符
</td>
</tr>
</tbody>
</table>
从上表可得知，变量的大小会根据编译器和所使用的电脑而有所不同。

下面实例会输出您电脑上各种数据类型的大小。

    
    #include <iostream>
    using namespace std;
    
    int main()
    {
       cout << "Size of char : " << sizeof(char) << endl;
       cout << "Size of int : " << sizeof(int) << endl;
       cout << "Size of short int : " << sizeof(short int) << endl;
       cout << "Size of long int : " << sizeof(long int) << endl;
       cout << "Size of float : " << sizeof(float) << endl;
       cout << "Size of double : " << sizeof(double) << endl;
       cout << "Size of wchar_t : " << sizeof(wchar_t) << endl;
       return 0;
    }


本实例使用了 **endl**，这将在每一行后插入一个换行符，<< 运算符用于向屏幕传多个值。我们也使用 **sizeof()**函数来获取各种数据类型的大小。

当上面的代码被编译和执行时，它会产生以下的结果，结果会根据所使用的计算机而有所不同：

    
    Size of char : 1
    Size of int : 4
    Size of short int : 2
    Size of long int : 4
    Size of float : 4
    Size of double : 8
    Size of wchar_t : 4
    




## typedef 声明


您可以使用 **typedef** 为一个已有的类型取一个新的名字。下面是使用 typedef 定义一个新类型的语法：

    
    typedef type newname;


例如，下面的语句会告诉编译器，feet 是 int 的另一个名称：

    
    typedef int feet;


现在，下面的声明是完全合法的，它创建了一个整型变量 distance：

    
    feet distance;




## 枚举类型


枚举类型声明一个可选的类型名称和一组标识符，用来作为该类型的值。其带有零个或多个标识符可以被用来作为该类型的值。每个枚举数是一个枚举类型的常数。

创建枚举，需要使用关键字 **enum**。枚举类型的一般形式为：

    
    enum enum-name { list of names } var-list;


在这里，enum-name 是枚举类型的名称。名称列表 { list of names } 是用逗号分隔的。

例如，下面的代码定义了一个颜色枚举，变量 c 的类型为 color。最后，c 被赋值为 "blue"。

    
    enum color { red, green, blue } c;
    c = blue;


默认情况下，第一个名称的值为 0，第二个名称的值为 1，第三个名称的值为 2，以此类推。但是，您也可以给名称赋予一个特殊的值，只需要添加一个初始值即可。例如，在下面的枚举中，**green** 的值为 5。

    
    enum color { red, green=5, blue };
    


在这里，**blue** 的值为 6，因为默认情况下，每个名称都会比它前面一个名称大 1。























* * *





# COMMENT



