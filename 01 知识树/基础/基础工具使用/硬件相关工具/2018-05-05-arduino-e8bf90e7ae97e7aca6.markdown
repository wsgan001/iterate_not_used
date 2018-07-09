---
author: evo
comments: true
date: 2018-05-05 06:57:42+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e8%bf%90%e7%ae%97%e7%ac%a6/
slug: arduino-%e8%bf%90%e7%ae%97%e7%ac%a6
title: Arduino 运算符
wordpress_id: 5265
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



运算符是一个符号，它告诉编译器执行特定的数学或逻辑函数。C语言具有丰富的内置运算符，并提供以下类型的运算符:



 	
  * Arithmetic Operators 算术运算符

 	
  * Comparison Operators 比较运算符

 	
  * Boolean Operators 布尔运算符

 	
  * Bitwise Operators 位运算符

 	
  * Compound Operators 复合运算符




## 算术运算符


假设变量A为10，变量B为20，则：

[示例](https://www.w3cschool.cn/arduino/arduino_arithmetic_operators.html)
<table class="table table-bordered         " >
<tbody >
<tr >
运算符名称
运算符简写
描述
例子
</tr>
<tr >

<td >赋值运算符
</td>

<td >=
</td>

<td >将等号右侧的值存储在等号左边的变量中。
</td>

<td >A = B
</td>
</tr>
<tr >

<td >加号
</td>

<td >+
</td>

<td >两个操作数相加
</td>

<td >A + B将得出30
</td>
</tr>
<tr >

<td >减号
</td>

<td >-
</td>

<td >从第一个操作数中减去第二个操作数
</td>

<td >A - B将得出-10
</td>
</tr>
<tr >

<td >乘号
</td>

<td >*
</td>

<td >将两个操作数相乘
</td>

<td >A * B将得出200
</td>
</tr>
<tr >

<td >除号
</td>

<td >/
</td>

<td >用分母除分子
</td>

<td >B / A将得出2
</td>
</tr>
<tr >

<td >模数
</td>

<td >%
</td>

<td >模数运算符和整数除后的余数
</td>

<td >B % A将得出0
</td>
</tr>
</tbody>
</table>


## 
比较运算符


假设变量A为10，变量B为20，则：

[示例](https://www.w3cschool.cn/arduino/arduino_comparison_operators.html)
<table class="table table-bordered         " >
<tbody >
<tr >
运算符名称
运算符简写
描述
例子
</tr>
<tr >

<td >等于
</td>

<td >==
</td>

<td >检查两个操作数的值是否相等，如果相等，则条件为真（true）。
</td>

<td >(A == B)不为真
</td>
</tr>
<tr >

<td >不等于
</td>

<td >!=
</td>

<td >检查两个操作数的值是否相等，如果值不相等，则条件为真。
</td>

<td >(A != B)为真
</td>
</tr>
<tr >

<td >小于
</td>

<td ><
</td>

<td >检查左操作数的值是否小于右操作数的值，如果是，则条件为真。
</td>

<td >(A < B)为真
</td>
</tr>
<tr >

<td >大于
</td>

<td >>
</td>

<td >检查左操作数的值是否大于右操作数的值，如果是，则条件为真。
</td>

<td >(A > B)不为真
</td>
</tr>
<tr >

<td >小于或等于
</td>

<td ><=
</td>

<td >检查左操作数的值是否小于或等于右操作数的值，如果是，则条件为真。
</td>

<td >(A <= B)为真
</td>
</tr>
<tr >

<td >大于或等于
</td>

<td >>=
</td>

<td >检查左操作数的值是否大于或等于右操作数的值，如果是，则条件为真。
</td>

<td >(A >= B)不为真
</td>
</tr>
</tbody>
</table>


## 
布尔运算符


假设变量A为10，变量B为20，则：

[示例](https://www.w3cschool.cn/Arduino/arduino_boolean_operators.html)
<table class="table table-bordered         " >
<tbody >
<tr >
运算符名称
运算符简写
描述
例子
</tr>
<tr >

<td >and（与）
</td>

<td >&&
</td>

<td >称为逻辑运算符与。如果两个操作数都是非零，那么条件为真。
</td>

<td >(A && B)为真
</td>
</tr>
<tr >

<td >or（或）
</td>

<td >||
</td>

<td >称为逻辑运算符或。如果两个操作数中的任何一个是非零，则条件为真。
</td>

<td >(A || B)为真
</td>
</tr>
<tr >

<td >not（非）
</td>

<td >!
</td>

<td >称为逻辑运算符非。用于反转其操作数的逻辑状态。如果条件为真，则逻辑运算符非将为假。
</td>

<td >!(A && B)为假
</td>
</tr>
</tbody>
</table>


## 
位运算符


假设变量A为60，变量B为13，则：

[示例](https://www.w3cschool.cn/Arduino/arduino_bitwise_operators.html)
<table class="table table-bordered         " >
<tbody >
<tr >
运算符名称
运算符简写
描述
例子
</tr>
<tr >

<td >and（与）
</td>

<td >&
</td>

<td >如果同时存在于两个操作数中，二进制AND运算符复制一位到结果中。
</td>

<td >(A & B)将得出12，即0000 1100
</td>
</tr>
<tr >

<td >or（或）
</td>

<td >|
</td>

<td >如果存在于任一操作数中，二进制OR运算符复制一位到结果中。
</td>

<td >(A | B)将得出61，即0011 1101
</td>
</tr>
<tr >

<td >xor（异或）
</td>

<td >^
</td>

<td >如果存在于其中一个操作数中但不同时存在于两个操作数中，二进制XOR运算符复制一位到结果中。
</td>

<td >(A ^ B)将得出49，即0011 0001
</td>
</tr>
<tr >

<td >not（非）
</td>

<td >~
</td>

<td >二进制NOT运算符是一元运算符，具有"翻转"位效果。
</td>

<td >(〜A)将得出-60，即1100 0011
</td>
</tr>
<tr >

<td >shift left（左移）
</td>

<td ><<
</td>

<td >二进制左移运算符。左操作数的值向左移动右操作数指定的位数。
</td>

<td >A<< 2将得出240，即1111 0000
</td>
</tr>
<tr >

<td >shift right（右移）
</td>

<td >>>
</td>

<td >二进制右移运算符。左操作数的值向右移动右操作数指定的位数。
</td>

<td >A>> 2将得出15，即0000 1111
</td>
</tr>
</tbody>
</table>


## 
复合运算符


假设变量A为10，变量B为20，则：

[示例](https://www.w3cschool.cn/Arduino/arduino_compound_operators.html)
<table class="table table-bordered         " >
<tbody >
<tr >
运算符名称
运算符简写
描述
例子
</tr>
<tr >

<td >自增
</td>

<td >++
</td>

<td >自增运算符，将整数值增加1
</td>

<td >A++ 将得出11
</td>
</tr>
<tr >

<td >自减
</td>

<td >--
</td>

<td >自减运算符，将整数值减1
</td>

<td >A-- 将得出9
</td>
</tr>
<tr >

<td >复合加
</td>

<td >+=
</td>

<td >加且赋值运算符。把右边操作数加上左边操作数的结果赋值给左边操作数。
</td>

<td >B += A 等效于 B = B + A
</td>
</tr>
<tr >

<td >复合减
</td>

<td >-=
</td>

<td >减且赋值运算符。把左边操作数减去右边操作数的结果赋值给左边操作数。
</td>

<td >B -= A等效于B = B - A
</td>
</tr>
<tr >

<td >复合乘
</td>

<td >*=
</td>

<td >乘且赋值运算符。把右边操作数乘以左边操作数的结果赋值给左边操作数。
</td>

<td >B *= A等价于B = B * A
</td>
</tr>
<tr >

<td >复合除
</td>

<td >/=
</td>

<td >除且赋值运算符。把左边操作数除以右边操作数的结果赋值给左边操作数。
</td>

<td >B /= A等效于B = B / A
</td>
</tr>
<tr >

<td >复合模数
</td>

<td >%=
</td>

<td >求模且赋值运算符。 求两个操作数的模赋值给左边操作数
</td>

<td >B %= A等效于B = B % A
</td>
</tr>
<tr >

<td >复合按位或
</td>

<td >|=
</td>

<td >按位或且赋值运算符
</td>

<td >A |= 2与A = A | 2相同
</td>
</tr>
<tr >

<td >复合按位与
</td>

<td >&=
</td>

<td >按位与且赋值运算符
</td>

<td >A &= 2与A = A & 2相同
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT



