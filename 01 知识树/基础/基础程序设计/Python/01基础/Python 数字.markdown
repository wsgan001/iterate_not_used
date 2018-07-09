---
author: evo
comments: true
date: 2018-05-03 05:05:30+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-num/
slug: python-num
title: Python 数字
wordpress_id: 4977
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)

 	
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## Python 数字


Python 数字数据类型用于存储数值。

数据类型是不允许改变的,这就意味着如果改变数字数据类型得值，将重新分配内存空间。

以下实例在变量赋值时数字对象将被创建：

    
    var1 = 1
    var2 = 10
    


您也可以使用del语句删除一些数字对象引用。

del语句的语法是：

    
    del var1[,var2[,var3[....,varN]]]]
    


您可以通过使用del语句删除单个或多个对象，例如：

    
    del var
    del var_a, var_b
    


Python 支持四种不同的数值类型：



 	
  * **整型(Int)** - 通常被称为是整型或整数，是正或负整数，不带小数点。

 	
  * **长整型(long integers)** - 无限大小的整数，整数最后是一个大写或小写的L。

 	
  * **浮点型(floating point real values)** - 浮点型由整数部分与小数部分组成，浮点型也可以使用科学计数法表示（2.5e2 = 2.5 x 102 = 250）

 	
  * **复数( (complex numbers))** - 复数的虚部以字母J 或 j结尾 。如：2+3i


<table class="reference  " >
<tbody >
<tr >
int
long
float
complex
</tr>
<tr >

<td >10
</td>

<td >51924361L
</td>

<td >0.0
</td>

<td >3.14j
</td>
</tr>
<tr >

<td >100
</td>

<td >-0x19323L
</td>

<td >15.20
</td>

<td >45.j
</td>
</tr>
<tr >

<td >-786
</td>

<td >0122L
</td>

<td >-21.9
</td>

<td >9.322e-36j
</td>
</tr>
<tr >

<td >080
</td>

<td >0xDEFABCECBDAECBFBAEl
</td>

<td >32.3+e18
</td>

<td >.876j
</td>
</tr>
<tr >

<td >-0490
</td>

<td >535633629843L
</td>

<td >-90.
</td>

<td >-.6545+0J
</td>
</tr>
<tr >

<td >-0x260
</td>

<td >-052318172735L
</td>

<td >-32.54e100
</td>

<td >3e+26J
</td>
</tr>
<tr >

<td >0x69
</td>

<td >-4721885298529L
</td>

<td >70.2-E12
</td>

<td >4.53e-7j
</td>
</tr>
</tbody>
</table>



 	
  * 长整型也可以使用小写"L"，但是还是建议您使用大写"L"，避免与数字"1"混淆。Python使用"L"来显示长整型。

 	
  * Python还支持复数，复数由实数部分和虚数部分构成，可以用a + bj,或者complex(a,b)表示， 复数的实部a和虚部b都是浮点型






* * *





## Python数字类型转换



    
    int(x [,base ])         将x转换为一个整数  
    long(x [,base ])        将x转换为一个长整数  
    float(x )               将x转换到一个浮点数  
    complex(real [,imag ])  创建一个复数  
    str(x )                 将对象 x 转换为字符串  
    repr(x )                将对象 x 转换为表达式字符串  
    eval(str )              用来计算在字符串中的有效Python表达式,并返回一个对象  
    tuple(s )               将序列 s 转换为一个元组  
    list(s )                将序列 s 转换为一个列表  
    chr(x )                 将一个整数转换为一个字符  
    unichr(x )              将一个整数转换为Unicode字符  
    ord(x )                 将一个字符转换为它的整数值  
    hex(x )                 将一个整数转换为一个十六进制字符串  
    oct(x )                 将一个整数转换为一个八进制字符串  
    






* * *





## Python数学函数


<table class="reference  " >
<tbody >
<tr >
函数
返回值 ( 描述 )
</tr>
<tr >

<td >[abs(x)](https://www.w3cschool.cn/python/func-number-abs.html)
</td>

<td >返回数字的绝对值，如abs(-10) 返回 10
</td>
</tr>
<tr >

<td >[ceil(x)](https://www.w3cschool.cn/python/func-number-ceil.html)
</td>

<td >返回数字的上入整数，如math.ceil(4.1) 返回 5
</td>
</tr>
<tr >

<td >[cmp(x, y)](https://www.w3cschool.cn/python/func-number-cmp.html)
</td>

<td >如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1
</td>
</tr>
<tr >

<td >[exp(x)](https://www.w3cschool.cn/python/func-number-exp.html)
</td>

<td >返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045
</td>
</tr>
<tr >

<td >[fabs(x)](https://www.w3cschool.cn/python/func-number-fabs.html)
</td>

<td >返回数字的绝对值，如math.fabs(-10) 返回10.0
</td>
</tr>
<tr >

<td >[floor(x)](https://www.w3cschool.cn/python/func-number-floor.html)
</td>

<td >返回数字的下舍整数，如math.floor(4.9)返回 4
</td>
</tr>
<tr >

<td >[log(x)](https://www.w3cschool.cn/python/func-number-log10.html)
</td>

<td >如math.log(math.e)返回1.0,math.log(100,10)返回2.0
</td>
</tr>
<tr >

<td >[log10(x)](https://www.w3cschool.cn/python3/python3-func-number-log10.html)
</td>

<td >返回以10为基数的x的对数，如math.log10(100)返回 2.0
</td>
</tr>
<tr >

<td >[max(x1, x2,...)](https://www.w3cschool.cn/python/func-number-max.html)
</td>

<td >返回给定参数的最大值，参数可以为序列。
</td>
</tr>
<tr >

<td >[min(x1, x2,...)](https://www.w3cschool.cn/python/func-number-min.html)
</td>

<td >返回给定参数的最小值，参数可以为序列。
</td>
</tr>
<tr >

<td >[modf(x)](https://www.w3cschool.cn/python/func-number-modf.html)
</td>

<td >返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示。
</td>
</tr>
<tr >

<td >[pow(x, y)](https://www.w3cschool.cn/python/func-number-pow.html)
</td>

<td >x**y 运算后的值。
</td>
</tr>
<tr >

<td >[round(x [,n])](https://www.w3cschool.cn/python/func-number-round.html)
</td>

<td >返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。
</td>
</tr>
<tr >

<td >[sqrt(x)](https://www.w3cschool.cn/python/func-number-sqrt.html)
</td>

<td >返回数字x的平方根，数字可以为负数，返回类型为实数，如math.sqrt(4)返回 2+0j
</td>
</tr>
</tbody>
</table>




* * *





## Python随机数函数


随机数可以用于数学，游戏，安全等领域中，还经常被嵌入到算法中，用以提高算法效率，并提高程序的安全性。

Python包含以下常用随机数函数：
<table class="reference  " >
<tbody >
<tr >
函数
描述
</tr>
<tr >

<td >[choice(seq)](https://www.w3cschool.cn/python/func-number-choice.html)
</td>

<td >从序列的元素中随机挑选一个元素，比如random.choice(range(10))，从0到9中随机挑选一个整数。
</td>
</tr>
<tr >

<td >[randrange ([start,] stop [,step])](https://www.w3cschool.cn/python/func-number-randrange.html)
</td>

<td >从指定范围内，按指定基数递增的集合中获取一个随机数，基数缺省值为1
</td>
</tr>
<tr >

<td >[random()](https://www.w3cschool.cn/python/func-number-random.html)
</td>

<td >随机生成下一个实数，它在[0,1)范围内。
</td>
</tr>
<tr >

<td >[seed([x])](https://www.w3cschool.cn/python/func-number-seed.html)
</td>

<td >改变随机数生成器的种子seed。如果你不了解其原理，你不必特别去设定seed，Python会帮你选择seed。
</td>
</tr>
<tr >

<td >[shuffle(lst)](https://www.w3cschool.cn/python/func-number-shuffle.html)
</td>

<td >将序列的所有元素随机排序
</td>
</tr>
<tr >

<td >[uniform(x, y)](https://www.w3cschool.cn/python/func-number-uniform.html)
</td>

<td >随机生成下一个实数，它在[x,y]范围内。
</td>
</tr>
</tbody>
</table>




* * *





## Python三角函数


Python包括以下三角函数：
<table class="reference  " >
<tbody >
<tr >
函数
描述
</tr>
<tr >

<td >[acos(x)](https://www.w3cschool.cn/python/func-number-acos-2.html)
</td>

<td >返回x的反余弦弧度值。
</td>
</tr>
<tr >

<td >[asin(x)](https://www.w3cschool.cn/python/func-number-asin.html)
</td>

<td >返回x的反正弦弧度值。
</td>

<td >
</td>
</tr>
<tr >

<td >[atan(x)](https://www.w3cschool.cn/python/func-number-atan.html)
</td>

<td >返回x的反正切弧度值。
</td>
</tr>
<tr >

<td >[atan2(y, x)](https://www.w3cschool.cn/python/func-number-atan2.html)
</td>

<td >返回给定的 X 及 Y 坐标值的反正切值。
</td>
</tr>
<tr >

<td >[cos(x)](https://www.w3cschool.cn/python/func-number-cos.html)
</td>

<td >返回x的弧度的余弦值。
</td>
</tr>
<tr >

<td >[hypot(x, y)](https://www.w3cschool.cn/python/func-number-hypot.html)
</td>

<td >返回欧几里德范数 sqrt(x*x + y*y)。
</td>
</tr>
<tr >

<td >[sin(x)](https://www.w3cschool.cn/python/func-number-sin.html)
</td>

<td >返回的x弧度的正弦值。
</td>
</tr>
<tr >

<td >[tan(x)](https://www.w3cschool.cn/python/func-number-tan.html)
</td>

<td >返回x弧度的正切值。
</td>
</tr>
<tr >

<td >[degrees(x)](https://www.w3cschool.cn/python/func-number-degrees.html)
</td>

<td >将弧度转换为角度,如degrees(math.pi/2) ， 返回90.0
</td>
</tr>
<tr >

<td >[radians(x)](https://www.w3cschool.cn/python/func-number-radians.html)
</td>

<td >将角度转换为弧度
</td>
</tr>
</tbody>
</table>




* * *





## Python数学常量


<table class="reference  " >
<tbody >
<tr >
常量
描述
</tr>
<tr >

<td >pi
</td>

<td >数学常量 pi（圆周率，一般以π来表示）
</td>
</tr>
<tr >

<td >e
</td>

<td >数学常量 e，e即自然常数（自然常数）。
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT



