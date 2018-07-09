---
author: evo
comments: true
date: 2018-05-19 15:24:08+00:00
layout: post
link: http://106.15.37.116/2018/05/19/cpp-%e5%b8%b8%e8%a7%81%e9%a2%84%e5%ae%9a%e4%b9%89%e5%ae%8f%e7%9a%84%e4%bd%bf%e7%94%a8/
slug: cpp-%e5%b8%b8%e8%a7%81%e9%a2%84%e5%ae%9a%e4%b9%89%e5%ae%8f%e7%9a%84%e4%bd%bf%e7%94%a8
title: C++ 常见预定义宏的使用
wordpress_id: 6043
categories:
- 基础程序设计
tags:
- '@NULL'
- C++
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [C++ 中常见预定义宏的使用](https://blog.csdn.net/hgl868/article/details/7058906)  **尚未整理**




# TODO





 	
  * **还没有整理，需要整理，而且顺便看下与Python中有什么区别？而且这些是在什么情况下使用的？**

 	
  * **而且，这个在C++ Primer中讲到过吗？需要确认下，属于哪一章的内容。**





* * *





# INTRODUCTION


今天在看一些算法代码的时候，发现他有这么一句：

    
    cout << endl << " line " << __LINE__ << "in function : " << __FUNCTION__ << endl << endl;


它输出的是：

    
    line 70 in function : Solution::LeastKNumbers_BySort


这个  __LINE__ 和 __FUNC__ 到底是干什么用的？看起来应该是打 log 用的。还是很厉害的。之前只在 Python 中看到过。**因此要总结一下。**





















* * *





# COMMENT



