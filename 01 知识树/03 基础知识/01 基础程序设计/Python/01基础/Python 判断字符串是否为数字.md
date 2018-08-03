---
author: evo
comments: true
date: 2018-05-12 08:04:11+00:00
layout: post
link: http://106.15.37.116/2018/05/12/python-%e5%88%a4%e6%96%ad%e5%ad%97%e7%ac%a6%e4%b8%b2%e6%98%af%e5%90%a6%e4%b8%ba%e6%95%b0%e5%ad%97/
slug: python-%e5%88%a4%e6%96%ad%e5%ad%97%e7%ac%a6%e4%b8%b2%e6%98%af%e5%90%a6%e4%b8%ba%e6%95%b0%e5%ad%97
title: Python 判断字符串是否为数字
wordpress_id: 5574
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [Python 判断字符串是否为数字](http://www.runoob.com/python3/python3-check-is-number.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





# 缘由


在从文本中读取数据的时候，有时候会要判断读进来的str是不是数字。而：



 	
  * Python isdigit() 方法检测字符串是否只由数字组成。 但是对于0.02 这种就会认为是False

 	
  * Python isnumeric() 方法检测字符串是否只由数字组成。这种方法是只针对unicode对象。




# 代码


因此可以自己进行判断，代码如下：

    
    def is_number(s):
        try:
            float(s)
            return True
        except ValueError:
            pass
     
        try:
            import unicodedata
            unicodedata.numeric(s)
            return True
        except (TypeError, ValueError):
            pass
     
        return False
     
    # 测试字符串和数字
    print(is_number('foo'))   # False
    print(is_number('1'))     # True
    print(is_number('1.3'))   # True
    print(is_number('-1.37')) # True
    print(is_number('1e3'))   # True
     
    # 测试 Unicode
    # 阿拉伯语 5
    print(is_number('٥'))  # True
    # 泰语 2
    print(is_number('๒'))  # True
    # 中文数字
    print(is_number('四')) # True
    # 版权号
    print(is_number('©'))  # False


输出：

    
    False
    True
    True
    True
    True
    True
    True
    True
    False




















* * *





# COMMENT



