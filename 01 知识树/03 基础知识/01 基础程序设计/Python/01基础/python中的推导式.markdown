---
author: evo
comments: true
date: 2018-03-20 11:19:28+00:00
layout: post
link: http://106.15.37.116/2018/03/20/python-comprehensions/
slug: python-comprehensions
title: python中的推导式
wordpress_id: 461
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


推导式感觉很酷，要掌握下：


## 简单的例子：



    
    # set与dict在python2里面是不支持的
    li = [i * 2 for i in range(10)]
    print(li)
    s = {x for x in range(10) if x % 2 == 0}
    print(s)
    d = {x: x % 2 == 0 for x in range(10)}
    print(d)


输出：

    
    [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
    {0, 2, 4, 6, 8}
    {0: True, 1: False, 2: True, 3: False, 4: True, 5: False, 6: True, 7: False, 8: True, 9: False}


COMMENT：

**要更加系统的深入的了解**
