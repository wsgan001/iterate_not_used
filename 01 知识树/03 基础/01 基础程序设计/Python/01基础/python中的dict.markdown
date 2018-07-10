---
author: evo
comments: true
date: 2018-03-20 04:34:15+00:00
layout: post
link: http://106.15.37.116/2018/03/20/python-dict/
slug: python-dict
title: python中的dict
wordpress_id: 455
categories:
- 随想与反思
tags:
- '@todo'
- '@want_to_know'
- python
---

<!-- more -->


## 缘由：


python中的dict感觉与别的dict还是差不多的。想要记录的就是key与value和item的遍历


## 要点：



    
    d={'a':1,'b':2,'c':3,1:'one',2:'two','m':[1,3,3,3]}
    for key in d:# 注意这两种不同的遍历方法
        print(key,d[key])
    for key,value in d.items():
        print(key,value)
    
    keys=d.keys()# dict_keys　格式
    print(type(keys))
    print(keys)


输出：

    
    a 1
    b 2
    c 3
    1 one
    2 two
    m [1, 3, 3, 3]
    a 1
    b 2
    c 3
    1 one
    2 two
    m [1, 3, 3, 3]
    <class 'dict_keys'>
    dict_keys(['a', 'b', 'c', 1, 2, 'm'])




## COMMENT：


**字典的初始化到底是什么样自得？为什么能看到几种不同的初始化？为什么有的时候还不是字符串的？**



REF：

1.[Python3 字典](http://www.runoob.com/python3/python3-dictionary.html)
