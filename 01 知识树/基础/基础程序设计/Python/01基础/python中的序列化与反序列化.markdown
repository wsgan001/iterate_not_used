---
author: evo
comments: true
date: 2018-03-22 04:37:00+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python-pickling-unpickling/
slug: python-pickling-unpickling
title: python中的序列化与反序列化
wordpress_id: 523
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


之前看一个人的视频中在教pandas的时候用到了pickle的文件格式，之前没见到过，知道我这次在序列化与反序列化中看到了pickle的文件格式，因此对于序列化与反序列化的方法还是要记录下，毕竟与直接的文本的读写还是有些不同的，有很多存取的格式，且像pickle这样的占用的空间比较小。经常用的话是csv格式的。


## 要点：




### 1.什么是序列化和反序列化？之前知道，这里再明确下，因为有点不是很清楚了：


















程序运行的过程中，所有变量都是在内存中操作的，当程序一旦执行完毕，结束退出后，变量占有的内存就被操作系统回收了。 因此我们需要将某些数据持久化存储到磁盘中，下次运行的时候从磁盘中读取相关数据。

我们将变量从内存中变成可以存储或传输的过程称之为序列化，在Python中叫做pickling，在其它语言中也称之为 serialization、marshaling、flattening等等，说的都是一个意思。 反之，则为反序列化，称之为unpickling，把变量内容从序列化的对象重新读取到内存中。


















### 2.使用pickle库进行序列化与反序列化


序列化：

    
    import pickle
    
    d = dict(name='aaa', age=29, score=80)
    str = pickle.dumps(d)#进行序列化处理
    print(str)
    
    with open('dump.txt', 'wb') as f:
        pickle.dump(d, f)


输出：

    
    b'\x80\x03}q\x00(X\x04\x00\x00\x00nameq\x01X\x03\x00\x00\x00aaaq\x02X\x03\x00\x00\x00ageq\x03K\x1dX\x05\x00\x00\x00scoreq\x04KPu.'


反序列化：

    
    import pickle
    
    with open('dump.txt', 'rb') as f:
        d = pickle.load(f)  # 反序列化处理
    print(d)


输出：

    
    {'name': 'aaa', 'age': 29, 'score': 80}


注意：Py2与Py3中的pickle不一样

为了保证2，3的和谐，可以使用这个方法：

    
    try:
        import cPickle as pickle
    except ImportError:
        import pickle




### 3.使用Json实现序列化和反序列化


Json还是经常使用的

代码如下：

    
    import json
    
    d1 = dict(name='aaa', age=20, score=80)
    print(d1)
    s = json.dumps(d1)  # 序列化
    print(s)
    
    d2 = json.loads(s)  # 反序列化
    print(d2)




### 4.使用pandas来进行序列化，（**推荐使用这个，支持的格式非常多**）


**这个后续进行补充**


## COMMENT：



