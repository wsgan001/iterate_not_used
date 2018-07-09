---
author: evo
comments: true
date: 2018-05-02 13:53:14+00:00
layout: post
link: http://106.15.37.116/2018/05/02/cpp-datetime/
slug: cpp-datetime
title: C++ 日期 & 时间
wordpress_id: 4899
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





## C++ 日期 & 时间


C++ 标准库没有提供所谓的日期类型。C++ 继承了 C 语言用于日期和时间操作的结构和函数。为了使用日期和时间相关的函数和结构，需要在 C++ 程序中引用 <ctime> 头文件。

有四个与时间相关的类型：**clock_t、time_t、size_t** 和 **tm**。类型 clock_t、size_t 和 time_t 能够把系统时间和日期表示为某种整数。

结构类型 **tm** 把日期和时间以 C 结构的形式保存，tm 结构的定义如下：

    
    struct tm {
      int tm_sec;   // 秒，正常范围从 0 到 59，但允许至 61
      int tm_min;   // 分，范围从 0 到 59
      int tm_hour;  // 小时，范围从 0 到 23
      int tm_mday;  // 一月中的第几天，范围从 1 到 31
      int tm_mon;   // 月，范围从 0 到 11
      int tm_year;  // 自 1900 年起的年数
      int tm_wday;  // 一周中的第几天，范围从 0 到 6，从星期日算起
      int tm_yday;  // 一年中的第几天，范围从 0 到 365，从 1 月 1 日算起
      int tm_isdst; // 夏令时
    }
    


下面是 C/C++ 中关于日期和时间的重要函数。所有这些函数都是 C/C++ 标准库的组成部分，您可以在 C++ 标准库中查看一下各个函数的细节。
<table class="reference notranslate   " >
<tbody >
<tr >
序号
函数 & 描述
</tr>
<tr >

<td >1
</td>

<td >[**time_t time(time_t *time);**](https://www.w3cschool.cn/c/c-function-time.html)
该函数返回系统的当前日历时间，自 1970 年 1 月 1 日以来经过的秒数。如果系统没有时间，则返回 .1。
</td>
</tr>
<tr >

<td >2
</td>

<td >[**char *ctime(const time_t *time);**](https://www.w3cschool.cn/c/c-function-ctime.html)
该返回一个表示当地时间的字符串指针，字符串形式 _day month year hours:minutes:seconds year\n_。
</td>
</tr>
<tr >

<td >3
</td>

<td >[**struct tm *localtime(const time_t *time);**](https://www.w3cschool.cn/c/c-function-localtime.html)
该函数返回一个指向表示本地时间的 **tm** 结构的指针。
</td>
</tr>
<tr >

<td >4
</td>

<td >[**clock_t clock(void);**](https://www.w3cschool.cn/c/c-function-clock.html)
该函数返回程序执行起（一般为程序的开头），处理器时钟所使用的时间。如果时间不可用，则返回 .1。
</td>
</tr>
<tr >

<td >5
</td>

<td >[**char * asctime ( const struct tm * time );**](https://www.w3cschool.cn/c/c-function-asctime.html)
该函数返回一个指向字符串的指针，字符串包含了 time 所指向结构中存储的信息，返回形式为：day month date hours:minutes:seconds year\n。
</td>
</tr>
<tr >

<td >6
</td>

<td >[**struct tm *gmtime(const time_t *time);**](https://www.w3cschool.cn/c/c-function-gmtime.html)
该函数返回一个指向 time 的指针，time 为 tm 结构，用协调世界时（UTC）也被称为格林尼治标准时间（GMT）表示。
</td>
</tr>
<tr >

<td >7
</td>

<td >[**time_t mktime(struct tm *time);**](https://www.w3cschool.cn/c/c-function-mktime.html)
该函数返回日历时间，相当于 time 所指向结构中存储的时间。
</td>
</tr>
<tr >

<td >8
</td>

<td >[**double difftime ( time_t time2, time_t time1 );**](https://www.w3cschool.cn/c/c-function-difftime.html)
该函数返回 time1 和 time2 之间相差的秒数。
</td>
</tr>
<tr >

<td >9
</td>

<td >[**size_t strftime();**](https://www.w3cschool.cn/c/c-function-strftime.html)
该函数可用于格式化日期和时间为指定的格式。
</td>
</tr>
</tbody>
</table>


## 当前日期和时间


下面的实例获取当前系统的日期和时间，包括本地时间和协调世界时（UTC）。

    
    #include <iostream>
    #include <ctime>
    
    using namespace std;
    
    int main( )
    {
       // 基于当前系统的当前日期/时间
       time_t now = time(0);
       
       // 把 now 转换为字符串形式
       char* dt = ctime(&now);
    
       cout << "本地日期和时间：" << dt << endl;
    
       // 把 now 转换为 tm 结构
       tm *gmtm = gmtime(&now);
       dt = asctime(gmtm);
       cout << "UTC 日期和时间："<< dt << endl;
    }


当上面的代码被编译和执行时，它会产生下列结果：

    
    本地日期和时间：Sat Jan  8 20:07:41 2011
    
    UTC 日期和时间：Sun Jan  9 03:07:41 2011
    




## 使用结构 tm 格式化时间


**tm** 结构在 C/C++ 中处理日期和时间相关的操作时，显得尤为重要。tm 结构以 C 结构的形式保存日期和时间。大多数与时间相关的函数都使用了 tm 结构。下面的实例使用了 tm 结构和各种与日期和时间相关的函数。

在练习使用结构之前，需要对 C 结构有基本的了解，并懂得如何使用箭头 -> 运算符来访问结构成员。

    
    #include <iostream>
    #include <ctime>
    
    using namespace std;
    
    int main( )
    {
       // 基于当前系统的当前日期/时间
       time_t now = time(0);
    
       cout << "1970年1月1日到目前经过的秒数:" << now << endl;
    
       tm *ltm = localtime(&now);
    
       // 输出 tm 结构的各个组成部分
       cout << "年: "<< 1900 + ltm->tm_year << endl;
       cout << "月: "<< 1 + ltm->tm_mon<< endl;
       cout << "日: "<<  ltm->tm_mday << endl;
       cout << "时间: "<< 1 + ltm->tm_hour << ":";
       cout << 1 + ltm->tm_min << ":";
       cout << 1 + ltm->tm_sec << endl;
    }


当上面的代码被编译和执行时，它会产生下列结果：

    
    1970年1月1日到目前经过的秒数:1524456057
    年: 2018
    月: 4
    日: 23
    时间: 5:1:58
    
























* * *





# COMMENT



