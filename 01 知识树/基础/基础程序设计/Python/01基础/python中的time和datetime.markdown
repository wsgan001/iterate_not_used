---
author: evo
comments: true
date: 2018-03-23 11:26:19+00:00
layout: post
link: http://106.15.37.116/2018/03/23/python-time-datetime/
slug: python-time-datetime
title: python中的time和datetime
wordpress_id: 683
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


这个在编程的时候还是会经常用到的，比如时间与str的转化，时间的日期的格式，对程序运行时间进行计时。等


## 要点：




### 1.time的使用



    
    import time
    print(time.time())
    print(time.localtime())
    for i in range(3):
        time.sleep(0.5)
        print('Tick !')


输出：

    
    1521804179.0451643
    time.struct_time(tm_year=2018, tm_mon=3, tm_mday=23, tm_hour=19, tm_min=22, tm_sec=59, tm_wday=4, tm_yday=82, tm_isdst=0)
    Tick !
    Tick !
    Tick !




### 2.datetime的使用



    
    import datetime
    
    print('Today is :', datetime.date.today())
    print('Now is :', datetime.datetime.now())
    print(datetime.date(2011, 1, 1))
    print(datetime.time(14, 0, 0))
    
    today = datetime.date.today()
    yesterday = today - datetime.timedelta(days=1)
    tomorrow = today + datetime.timedelta(days=1)
    print(yesterday, today, tomorrow)


输出：

    
    Today is : 2018-03-23
    Now is : 2018-03-23 19:24:54.797304
    2011-01-01
    14:00:00
    2018-03-22 2018-03-23 2018-03-24




## COMMENT：


**时间与str的转化，时间的日期的格式，对程序运行时间进行计时。这些都没有，要补充下**
