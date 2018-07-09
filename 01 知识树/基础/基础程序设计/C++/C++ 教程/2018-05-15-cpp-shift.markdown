---
author: evo
comments: true
date: 2018-05-15 09:57:02+00:00
layout: post
link: http://106.15.37.116/2018/05/15/cpp-shift/
slug: cpp-shift
title: C++ 左移>
wordpress_id: 5798
categories:
- 随想与反思
tags:
- NOT_ADD
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [左移和右移运算符（>> 和 <<）](https://msdn.microsoft.com/zh-cn/library/336xbhcz.aspx#Anchor_5)

 	
  2. [左移 右移 逻辑右移 算术右移](https://blog.csdn.net/yzf279533105/article/details/58642142)

 	
  3. [逻辑右移和算术右移](https://blog.csdn.net/tandesir/article/details/7385955)




# TODO





 	
  * **需要补充**

 	
  * 要对各种情况及结果进行总结。





* * *





# INTRODUCTION





 	
  * 今天在看一个算法问题的程序：计算数字为二进制时候1的个数 的时候，看到了 n >>= 1; 这一句，然后，它说：右移，对于负数来说是有问题的，因此还是要对左移右移弄清楚，要对逻辑左移和逻辑右移也要弄清楚。





# 最关键的性质


左移只有一种：



 	
  * 规则：丢弃最高位，往左移位，右边空出来的位置补0


右移有两种：

 	
  * 1. 逻辑右移：丢弃最低位，向右移位，左边空出来的位置补0

 	
  * 2. 算术右移：丢弃最低位，向右移位，左边空出来的位置补原来的符号位（即补最高位）




# 而对于 C 来说，>> 这个符号





 	
  * 对无符号数右移时执行的是逻辑右移

 	
  * 对有符号数右移时执行的取决于编译器，一般是算术右移 **要确认下**


**OK，上面这两点是比较核心的东西，其他的后续进行补充。**





在嵌入式程序开发的时候，通常采用交叉编译开发，如果定义为有符号的，就无法保证右移操作能跨平台使用，因此嵌入式的程序中，常会将数据定义为unsigned int，而不是int。

























* * *





# COMMENT



