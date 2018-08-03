---
author: evo
comments: true
date: 2018-05-08 04:40:25+00:00
layout: post
link: http://106.15.37.116/2018/05/08/python-typeerror-dict_keys-object-does-not-support-indexing/
slug: python-typeerror-dict_keys-object-does-not-support-indexing
title: 'python TypeError: ''dict_keys'' object does not support indexing'
wordpress_id: 5431
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [TypeError: 'dict_keys' object does not support indexing](https://blog.csdn.net/qq_18433441/article/details/54782459)




# TODO





 	
  * aaa




# MOTIVE





* * *





# 问题出现情景


在学习决策树的时候，有这么一句：first_str = tree.keys()[0] ，报错了：TypeError: 'dict_keys' object does not support indexing


# 问题原因和解决


这是由于 python3 改变了 dict.keys ，返回的是 dict_keys 对象，支持 iterable 但不支持 indexable，我们可以将其明确的转化成 list：

    
    a = {0:'左左',1:'右右'}
    k = list(a.keys())
    print(k[0])#输出0






















* * *





# COMMENT



