---
author: evo
comments: true
date: 2018-05-30 11:50:34+00:00
layout: post
link: http://106.15.37.116/2018/05/30/linux-centos-yum/
slug: linux-centos-yum
title: Linux CentOS yum
wordpress_id: 7046
categories:
- 基础工具使用
tags:
- linux
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. 


[centos在yum install报错：Another app is currently holding the yum lock解决方法](https://www.cnblogs.com/jincon/p/3371471.html)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 报错 Another app is currently holding the yum lock




## 问题


yum -y update 之后，程序走到最后一个cleaning的时候不走了，然后我 Ctrl+Z 了，但是之后发现 yum 命令被占用了，报错：Another app is currently holding the yum lock。因此查了下，解决如下：


## 解决


可以通过强制关掉yum进程：

    
    rm -f /var/run/yum.pid


然后就可以使用yum了。OK，很简单。





















* * *





# COMMENT



