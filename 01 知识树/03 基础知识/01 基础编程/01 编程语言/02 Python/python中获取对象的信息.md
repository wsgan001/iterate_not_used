---
author: evo
comments: true
date: 2018-03-19 13:57:16+00:00
layout: post
link: http://106.15.37.116/2018/03/19/type-get-information/
slug: type-get-information
title: python中获取对象的信息
wordpress_id: 422
categories:
- 随想与反思
tags:
- python
---

<!-- more -->


## 缘由：


在遇到一个不熟悉的类库的时候，怎么获取关于这个类库的信息？不然的话都不知道怎么使用。


## 要点：




### 可以使用 type() ：


代码如下：

    
    # 字符串处理一定要非常熟悉，常见的字符串处理一定要背下来，这样工作效率会很高
    # python里面函数也会作为对象来进行操作
    
    # 基本的变量类型
    # 怎么查看类型很重要，因为第三方库有些文档讲的不是很清楚，这时要自己查看
    
    
    # python里面没有简单数据类型，都是class类型 都是集成自object的
    # 因此int和string都有自己的方法
    
    print(type(1234))
    print(type(123.45))
    print(type(123.))
    print(type('abc'))
    
    print()
    print(type(None))
    print(type([1,2,3,'a','b']))
    print(type((1,'abc')))
    print(type(set(['a','b',3])))
    print(type({'a':1,'b':3}))
    
    
    # 在python里面函数也是一个对象 是一个function对象
    # 函数跟普通变量是一样的
    def func(a,b,c):
        print(a,b,c)
    print(type(func))
    
    a=func
    print(type(a))
    
    #对于模块来说也有类型
    import string
    print(type(string))
    
    
    # 对于你自己定义的类 它是一个type类型
    # 对于你实例出来的对象之后，它的实例就是你所对应的类型
    # type就是一个类型，在讲到元编程的时候， 所有的类背后也是一个type类来描述这个类
    # 因此在python里面所有对象都是个类，类本身也是一个类。
    class Myclass(object):
        pass
    print(type(Myclass))
    my_class=Myclass()
    print(type(my_class))


输出如下：

    
    <class 'int'>
    <class 'float'>
    <class 'float'>
    <class 'str'>
    
    <class 'NoneType'>
    <class 'list'>
    <class 'tuple'>
    <class 'set'>
    <class 'dict'>
    <class 'function'>
    <class 'function'>
    <class 'module'>
    <class 'type'>
    <class '__main__.Myclass'>


可见，python中很多都有type，而不仅仅是class，连function都有type，且class的类型也有type。。


### 可以使用isinstance()：


















isinstance()可以告诉我们，一个对象是否是某种类型（包括继承关系）。

















    
    # isinstance
    class A(object):
        pass
    
    
    class B(A):
        pass
    
    
    class C(B):
        pass
    
    
    k = A()
    g = B()
    y = C()
    print(isinstance(y, C))
    print(isinstance(y, B))
    print(isinstance('a', str))


输出：

    
    True
    True
    True




### 使用 dir() ：


如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：

    
    print(dir('ABC'))


输出：

    
    ['__add__',
     '__class__',
     '__contains__',
     '__delattr__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__getitem__',
     '__getnewargs__',
     '__gt__',
     '__hash__',
     '__init__',
     '__init_subclass__',
     '__iter__',
     '__le__',
     '__len__',
     '__lt__',
     '__mod__',
     '__mul__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__rmod__',
     '__rmul__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     'capitalize',
     'casefold',
     'center',
     'count',
     'encode',
     'endswith',
     'expandtabs',
     'find',
     'format',
     'format_map',
     'index',
     'isalnum',
     'isalpha',
     'isdecimal',
     'isdigit',
     'isidentifier',
     'islower',
     'isnumeric',
     'isprintable',
     'isspace',
     'istitle',
     'isupper',
     'join',
     'ljust',
     'lower',
     'lstrip',
     'maketrans',
     'partition',
     'replace',
     'rfind',
     'rindex',
     'rjust',
     'rpartition',
     'rsplit',
     'rstrip',
     'split',
     'splitlines',
     'startswith',
     'strip',
     'swapcase',
     'title',
     'translate',
     'upper',
     'zfill']
    


注意：类似__xxx__的属性和方法在Python中都是有特殊用途的，比如__len__方法返回长度。在Python中，如果你调用len()函数试图获取一个对象的长度，实际上，在len()函数内部，它自动去调用该对象的__len__()方法。








