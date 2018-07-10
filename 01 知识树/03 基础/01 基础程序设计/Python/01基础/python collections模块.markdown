---
author: evo
comments: true
date: 2018-03-23 11:53:31+00:00
layout: post
link: http://106.15.37.116/2018/03/23/python-collections/
slug: python-collections
title: python collections模块
wordpress_id: 690
categories:
- 随想与反思
tags:
- python
---

<!-- more -->


## 缘由：


collections是Python内建的一个集合模块，里面提供了除了python原生的之外的一些集合结构，还是很有用的


## 要点：




### 1.namedtuple



    
    # 感觉这个namedtyple就像创建了一个类一样
    # 比如这个就像创建了一个Point的类，然后实例化为p
    from collections import namedtuple
    
    Point = namedtuple('Point', ['x', 'y'])
    print(Point)
    p = Point(1, 2)
    print(p.x, p.y)


输出：

    
    <class '__main__.Point'>
    1 2


这个namedtuple一般用在什么地方？


### 2.deque



    
    from collections import deque
    
    # deque 是为了高效实现插入和删除操作的双向列表，适合用于队列和栈
    q = deque(['a', 'b', 'c'])
    q.append('x')
    q.appendleft('y')
    print(q)


输出：

    
    deque(['y', 'a', 'b', 'c', 'x'])


这个应该用的很多


### 3.defaultdict 与Ordereddict



    
    from collections import defaultdict
    import traceback
    
    d = dict({'key1': 1})
    print(d['key1'])
    try:
        print(d['key2'])# 普通的dict如果没有这个key会报KeyError
    except:
        traceback.print_exc()
    
    dd = defaultdict(lambda: 'N/A')  # 为什么这个地方是一个lambda表达式？
    dd['key1'] = 'abc'
    print(dd['key1'])  # key1存在
    print(dd['key2'])  # key2不存在，返回默认值
    
    
    
    
    
    from collections import OrderedDict
    
    d = dict([('a', 1), ('b', 2), ('c', 3)])
    print(d)  # dict的Key是无序的，因此结果是不一定的 {'a': 1, 'c': 3, 'b': 2}
    od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
    print(od)  # OrderedDict的Key是有序的，OrderedDict([('a', 1), ('b', 2), ('c', 3)])
    


输出：

    
    1
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/c7.py", line 6, in <module>
        print(d['key2'])
    KeyError: 'key2'
    abc
    N/A
    {'a': 1, 'b': 2, 'c': 3}
    OrderedDict([('a', 1), ('b', 2), ('c', 3)])


注：为什么default的初始化用的是一个lambda表达式？而且为什么是 N/A？


### 4.Counter



    
    from collections import Counter
    
    g = {}
    c = Counter()
    for ch in 'programming':
        c[ch] = c[ch] + 1
        if not g.__contains__(ch):
            g[ch] = 1
        else:
            g[ch] = g[ch] + 1
    print(g)
    print(c)  # Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})


输出：

    
    Counter({'r': 2, 'g': 2, 'm': 2, 'p': 1, 'o': 1, 'a': 1, 'i': 1, 'n': 1})
    {'p': 1, 'r': 2, 'o': 1, 'g': 2, 'a': 1, 'm': 2, 'i': 1, 'n': 1}


注：感觉这个Counter就类似一个dict一样，不过用起来稍微方便些。counter的确是dict的一个子类


## COMMENT：


感觉deque用的应该会很多，namedtuple、defaultdict、Ordereddict和Counter感觉都用的比较少
