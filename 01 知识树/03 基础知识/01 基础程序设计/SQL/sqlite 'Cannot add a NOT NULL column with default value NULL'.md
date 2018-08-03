---
author: evo
comments: true
date: 2018-06-09 08:22:20+00:00
layout: post
link: http://106.15.37.116/2018/06/09/sqlite-cannot-add-a-not-null-column-with-default-value-null/
slug: sqlite-cannot-add-a-not-null-column-with-default-value-null
title: sqlite 'Cannot add a NOT NULL column with default value NULL'
wordpress_id: 7447
categories:
- 基础工具使用
tags:
- '@NULL'
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [Laravel migration with sqllite 'Cannot add a NOT NULL column with default value NULL'](https://stackoverflow.com/questions/20822159/laravel-migration-with-sqllite-cannot-add-a-not-null-column-with-default-value)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 缘由


一直觉得，sqlite 这么方便，速度又快，为什么要用 mysql 呢？

后来虽然知道 sqlite 同时读写的时候不是很好，但是还觉得没什么。

今天遇到一个问题：

在使用 migrate 的向一个table 里面添加一个column 的时候，报错了，说：

'Cannot add a NOT NULL column with default value NULL'

它意思是，它添加这一列的时候，必须是NULL，但是我要求这列 nullable=False ，因此它报错了

看了下，好像没有什么好的方法，

暂时没有解决



















* * *





# COMMENT



