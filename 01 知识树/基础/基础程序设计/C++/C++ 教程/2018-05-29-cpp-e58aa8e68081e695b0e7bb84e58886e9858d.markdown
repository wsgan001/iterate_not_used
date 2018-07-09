---
author: evo
comments: true
date: 2018-05-29 07:19:49+00:00
layout: post
link: http://106.15.37.116/2018/05/29/cpp-%e5%8a%a8%e6%80%81%e6%95%b0%e7%bb%84%e5%88%86%e9%85%8d/
slug: cpp-%e5%8a%a8%e6%80%81%e6%95%b0%e7%bb%84%e5%88%86%e9%85%8d
title: C++ 动态数组分配
wordpress_id: 6983
categories:
- 基础程序设计
tags:
- C++
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [探索二维数组的动态分配](https://blog.csdn.net/GAMEloft9/article/details/48947085)

 	
  2. [二维数组的动态分配(new)、初始化(memset)和撤销(delete)](https://blog.csdn.net/maverick1990/article/details/22829135)




# TODO





 	
  * **要看一下权威的资料上是怎么处理的，现在是不是有什么更好的方法？**





* * *





# INTRODUCTION





 	
  * aaa




普通的数组用这样就可以分配：

    
    int ls[10];
    memset(ls, 0, sizeof(ls));


或者这样：

    
    int ls[8] = { 0, 1, 2, 3, 2, 6, 7, 4 };


但是我不确定数组长度的时候怎么办呢？

只有动态分配：

    
    int *ls = new int[length];
    memset(ls, 0, sizeof(int)*length);
    delete[] ls;


或者这样：**这个要确认下。这个好像还是没有初始化为0，因此还是优先使用上面的方法，后续补充下。**

    
    int *ls = (int*)malloc(sizeof(int)*length);
    delete[] ls;














* * *





# COMMENT



