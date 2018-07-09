---
author: evo
comments: true
date: 2018-05-27 00:21:21+00:00
layout: post
link: http://106.15.37.116/2018/05/27/c-stl-istream_iterator%e3%80%81ostream_iterator/
slug: c-stl-istream_iterator%e3%80%81ostream_iterator
title: C++ STL istream_iterator、ostream_iterator
wordpress_id: 6794
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





 	
  1. [STL中istream_iterator和ostream_iterator的基本用法](https://www.cnblogs.com/VIPler/p/4367308.html)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





# 缘由


今天看一道算法题里面有这么一句：

    
    	int arr[] = { 7, 5, 6, 4 };
    	vector<int> vec(arr, arr + 4);
    	copy(vec.begin(), vec.end(), ostream_iterator<int>(cout, " "));


以前好像没有看到过 ostream_iterator 的使用，因此要总结下。



**待总结**





















* * *





# COMMENT



