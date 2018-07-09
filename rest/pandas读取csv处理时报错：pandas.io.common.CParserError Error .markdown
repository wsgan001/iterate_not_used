---
author: evo
comments: true
date: 2018-05-06 01:11:55+00:00
layout: post
link: http://106.15.37.116/2018/05/06/pandas-read_csv-error/
slug: pandas-read_csv-error
title: 'tokenizing data. C error:
  Expected 1 fields in line 7, saw 2'
wordpress_id: 5312
categories:
- 随想与反思
tags:
- python
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1. [pandas读取csv处理时报错：ParserError: Error tokenizing data. C error: Expected 1 fields in line 29, saw 2](https://blog.csdn.net/yj928674542/article/details/75634197)




# TODO






  * aaa




# MOTIVE






  * 在read_csv的时候出现错误





* * *





# 错误出现的原因：


csv文件默认的是以逗号为分隔符，但是由于中文中逗号的使用率很高，所以有的时候使用pandas写入 csv时，会设置参数 sep，比如sep=’\t’ ，即以tab为分隔符写入，毕竟tab在中文习惯里用的很少。

而这个时候如果你读取的时候，没有设定sep，就会产生这种错误：

ParserError: Error tokenizing data. C error: Expected 1 fields in line 29, saw 2


# 解决的办法：


在读取 csv 进行数据处理时，要先看一下csv里面是以什么符号作为sep的，然后在读取的时候设定这个符号：比如sep="$"，那么：


    df=pd.read_csv('path',sep="$")

























* * *





# COMMENT



