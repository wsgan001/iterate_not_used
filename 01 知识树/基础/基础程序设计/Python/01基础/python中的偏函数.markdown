---
author: evo
comments: true
date: 2018-03-22 11:10:13+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python-partial-function/
slug: python-partial-function
title: python中的偏函数
wordpress_id: 549
categories:
- 随想与反思
tags:
- python
---

<!-- more -->


## 缘由：


之前看书好像没有看到过这个，因此总结下


## 要点：


















Python的functools模块提供了很多有用的功能，其中一个就是偏函数（Partial function）。

偏函数又可以翻译成部分函数，大概意思就是说，只设置一部分参数。

代码如下：

    
    print(int('12345'))
    print(int('12345',base=8))
    print(int('12345',16))
    def int2(x,base=2):
        return int(x,base)
    print(int2('1000000'))
    
    # functools.partial就是帮助我们创建一个偏函数的，
    # 不需要我们自己定义int2()，可以直接使用下面的代码创建一个新的函数int2：
    import functools
    int2=functools.partial(int,base=2)#实际上固定了int()函数的关键字参数base。
    print(int2('1000000'))


输出：

    
    12345
    5349
    74565
    64
    64


所以，简单总结functools.partial的作用就是：把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。


















## COMMANT：


感觉这个偏函数应该会很少用到吧？
