---
author: evo
comments: true
date: 2018-03-22 15:30:37+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python-exception/
slug: python-exception
title: python异常与错误处理
wordpress_id: 579
categories:
- 随想与反思
tags:
- python
---

<!-- more -->


## 缘由：


python里面异常到底怎么处理好？最好能整理出系统的，完善的方法


## 要点：




### 1.关于except的使用


代码如下：

    
    import traceback
    
    try:
        r = 10 / 0
    except ZeroDivisionError as e:
        print(e)
        r = 1
    else:
        print('没有异常')
    finally:
        print('不管有没有异常都执行')
    print(r)


输出：

    
    division by zero
    不管有没有异常都执行
    1




### 2.traceback的使用


**需要补充**


### 3.logging的使用与配置


**需要补充**


## COMMENT：



