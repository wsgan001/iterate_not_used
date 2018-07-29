---
author: evo
comments: true
date: 2018-06-05 11:34:34+00:00
layout: post
link: http://106.15.37.116/2018/06/05/django-no-changes-detected-when-makemigrations/
slug: django-no-changes-detected-when-makemigrations
title: Django. No changes detected when "makemigrations"
wordpress_id: 7425
categories:
- 基础工具使用
tags:
- Django
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [Django. No changes detected when "makemigrations"](https://blog.csdn.net/stephen_wong/article/details/46351505)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




# 缘由


如果你没有 makemigrations migrate 就直接 runserver了，那么当你想起来再 makemigrations 和migrate 的时候，发现不管用了，这时候怎么办呢？


# 问题解答


在修改了models.py后，有些用户会喜欢用python manage.py makemigrations生成对应的py代码。

但有时执行python manage.py makemigrations命令，会提示"No changes detected." 可能有用的解决方式如下：

1. 直接使用python manage.py migrate.

可能会先生成对应数据库的py代码，再自动执行这段代码，创建数据库表格 （我没有仔细去读文档 不清楚这条命令的逻辑）

2. 来自：https://docs.djangoproject.com/en/1.8/topics/migrations/

先 python manage.py makemigrations --empty yourappname 生成一个空的initial.py

再 python manage.py makemigrations 生成原先的model对应的migration file

3. 待续





















* * *





# COMMENT



