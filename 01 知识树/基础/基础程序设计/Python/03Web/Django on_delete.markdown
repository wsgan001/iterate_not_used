---
author: evo
comments: true
date: 2018-06-02 23:06:16+00:00
layout: post
link: http://106.15.37.116/2018/06/03/django-on_delete/
slug: django-on_delete
title: Django on_delete
wordpress_id: 7367
categories:
- 基础程序设计
tags:
- Django
- python
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [django数据模型中关于on_delete的使用](https://blog.csdn.net/kuangshp128/article/details/78946316)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# on_delete 一般用在：


post = models.ForeignKey('blog.Post',models.CASCADE)#级联删除

一、外键的删除
关于on_delete的总结
1、常见的使用方式(设置为null)

    
    class BookModel(models.Model):
    """
    书籍表
    """
    book_name = models.CharField(max_length=100, verbose_name='书名')
    # 表示外键关联到作者表,当作者表删除了该条数据,图书表中不删除,仅仅是把外键置空
    author = models.ForeignKey(AuthModel, null=True, blank=True, on_delete=models.SET_NULL)
    price = models.FloatField(verbose_name='价格')
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='添加时间')


2、关于别的属性的介绍



 	
  * CASCADE:这就是默认的选项，级联删除，你无需显性指定它。

 	
  * PROTECT: 保护模式，如果采用该选项，删除的时候，会抛出ProtectedError错误。

 	
  * SET_NULL: 置空模式，删除的时候，外键字段被设置为空，前提就是blank=True, null=True,定义该字段的时候，允许为空。

 	
  * SET_DEFAULT: 置默认值，删除的时候，外键字段设置为默认值，所以定义外键的时候注意加上一个默认值。

 	
  * SET(): 自定义一个值，该值当然只能是对应的实体了


3、补充说明:关于SET()的使用

    
    **官方案例**
    def get_sentinel_user():
    return get_user_model().objects.get_or_create(username='deleted')[0]
    
    class MyModel(models.Model):
    user = models.ForeignKey(
    settings.AUTH_USER_MODEL,
    on_delete=models.SET(get_sentinel_user),
    )






















* * *





# COMMENT



