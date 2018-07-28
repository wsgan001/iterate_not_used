---
author: evo
comments: true
date: 2018-05-05 07:09:53+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e6%95%b0%e7%bb%84/
slug: arduino-%e6%95%b0%e7%bb%84
title: Arduino 数组
wordpress_id: 5296
categories:
- 基础工具使用
tags:
- Arduino
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



数组是连续的一组相同类型的内存位置。要引用数组中的特定位置或元素，我们指定数组的名称和数组中特定元素的位置编号。

下图给出了一个名为C的整数数组，它包含11个元素。通过给出数组名称，后面跟特定元素的位置编号：方括号([])，你可以引用这些元素中的任何一个。位置编号更正式地称为下标或索引（该数字指定从数组开始的元素数）。第一个元素具有下标0（零），有时称为零元素。

因此，数组C的元素是C[0]，C[1]，C[2]等等。数组C中的最高下标是10，其比数组中的元素数少1。数组名遵循与其他变量名相同的约定。


Elements of Array

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/3Fcb0aDilc.png?imageslim)





下标必须是整数或整数表达式（使用任何整数类型）。如果程序使用表达式作为下标，则程序评估表达式以确定下标。例如，如果我们假设变量a等于5，变量b等于6，那么语句将数组元素C[11]加2。

下标数组名是一个左值，它可以在赋值的左侧使用，就像非数组变量名一样。

让我们更仔细地检查给定图中的数组C。整个数组的名称是C。它的11个元素被称为C[0]到C[10]。C[0]的值为-45，C[1]的值为6，C[2]的值为0，C[7]的值为62，C[10]的值为78。

要打印数组C的前三个元素中包含的值的总和，我们将写：


    Serial.print (C[ 0 ] + C[ 1 ] + C[ 2 ] );


要将C[6]的值除以2并将结果赋值给变量x，我们将写：


    x = C[ 6 ] / 2;




## 声明数组


数组占用内存中的空间。要指定元素的类型和数组所需的元素数量，请使用以下形式的声明：


    type arrayName [ arraySize ] ;


编译器保留适当的内存量（回想一下，保留内存的声明更恰当地被称为定义）。arraySize必须是大于零的整数常量。例如，要告诉编译器为整数数组C保留11个元素，请使用声明：


    int C[ 12 ]; // C is an array of 12 integers


数组可以声明为包含任何非引用数据类型的值。例如，可以使用字符串类型的数组来存储字符串。


## 使用数组的示例


本节提供了许多示例来演示如何声明，初始化以及操作数组。


### 示例1：声明数组并使用循环来初始化数组的元素


程序声明一个10元素的整数数组** n **。行a-b使用** For **语句将数组元素初始化为零。与其他自动变量一样，自动数组不会隐式初始化为零。第一个输出语句（行c）显示在后续for语句（行d-e）中打印的列的列标题，以表格格式打印数组。

**示例**


    int n[ 10 ] ; // n is an array of 10 integers

    void setup () {

    }

    void loop () {
       for ( int i = 0; i < 10; ++i ) // initialize elements of array n to 0 {
          n[ i ] = 0; // set element at location i to 0
          Serial.print (i) ;
          Serial.print (‘\r’) ;
       }
       for ( int j = 0; j < 10; ++j ) // output each array element's value {
          Serial.print (n[j]) ;
          Serial.print (‘\r’) ;
       }
    }


**结果** - 它会产生以下结果：
<table class="table table-bordered         " >
<tbody >
<tr >
元件
值
</tr>
<tr >

<td >


0




1




2




3




4




5




6




7




8




9



</td>

<td >


0




0




0




0




0




0




0




0




0




0



</td>
</tr>
</tbody>
</table>


###
示例2：使用初始化器列表在声明中初始化数组


数组元素也可以在数组声明中初始化，通过在数组名后面跟随等号和一个用大括号及逗号分隔的初始化器列表。程序使用初始化器列表来初始化一个具有10个值的整数数组（行a），并以表格格式（行b-c）打印数组。

**示例**


    // n is an array of 10 integers
    int n[ 10 ] = { 32, 27, 64, 18, 95, 14, 90, 70, 60, 37 } ;

    void setup () {

    }

    void loop () {
       for ( int i = 0; i < 10; ++i ) // initialize elements of array n to 0 {
          Serial.print (i) ;
          Serial.print (‘\r’) ;
       }
       for ( int j = 0; j < 10; ++j ) // output each array element's value {
          Serial.print (n[j]) ;
          Serial.print (‘\r’) ;
       }
    }


**结果** - 它会产生以下结果：
<table class="table table-bordered         " >
<tbody >
<tr >
元件
值
</tr>
<tr >

<td >


0




1




2




3




4




5




6




7




8




9



</td>

<td >


32




27




64




18




95




14




90




70




60




37



</td>
</tr>
</tbody>
</table>


###
示例3：对数组的元素求和


通常，数组的元素表示要在计算中使用的一系列值。例如，如果数组的元素表示考试成绩，教授可能希望将数组的元素进行加总，并使用该总和来计算班级考试的平均成绩。程序将包含在10元素整数数组** a **中的值进行求和。

**示例**


    const int arraySize = 10; // constant variable indicating size of array
    int a[ arraySize ] = { 87, 68, 94, 100, 83, 78, 85, 91, 76, 87 };
    int total = 0;

    void setup () {

    }
    void loop () {
       // sum contents of array a
       for ( int i = 0; i < arraySize; ++i )
          total += a[ i ];
       Serial.print (“Total of array elements : ") ;
       Serial.print(total) ;
    }


**结果** - 它会产生以下结果：


    Total of array elements: 849


数组对Arduino很重要，应该需要更多的关注。以下是学习Arduino应该清楚的与数组相关的重要概念：
<table class="table table-bordered         " >
<tbody >
<tr >
序号
概念和描述
</tr>
<tr >

<td >1
</td>

<td >[将数组传递给函数](https://www.w3cschool.cn/Arduino/arduino_passing_arrays_to_functions.html)要将数组参数传递给函数，请指定没有任何括号的数组的名称。
</td>
</tr>
<tr >

<td >2
</td>

<td >[多维数组](https://www.w3cschool.cn/Arduino/arduino_multi_dimensional_arrays.html)具有两个维度（即，下标）的数组通常表示由排列在行和列中的信息组成的值的表格。
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT
