---
author: evo
comments: true
date: 2018-06-03 13:36:46+00:00
layout: post
link: http://106.15.37.116/2018/06/03/django-disallowedhost-at-invalid-http_host-header/
slug: django-disallowedhost-at-invalid-http_host-header
title: Django DisallowedHost at / Invalid HTTP_HOST header
wordpress_id: 7376
categories:
- 基础工具使用
tags:
- '@NULL'
- Django
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. 


[Django运行访问项目出现的问题:DisallowedHost at / Invalid HTTP_HOST header](https://blog.csdn.net/will5451/article/details/53861092)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 缘由


在刚把django 博客放到服务器上运行的时候，它报错说：DisallowedHost at xx.xx.xx.xx Invalid HTTP_HOST header，查了下，解决了。


# 解决如下


修改一下 setting.py 文件中的 ALLOWED_HOSTS：

原来是：



 	
  * ALLOWED_HOSTS = []


改为：

 	
  * ALLOWED_HOSTS = ['*']  ＃在这里请求的host添加了＊


**具体原因没有追究**





















* * *





# COMMENT



