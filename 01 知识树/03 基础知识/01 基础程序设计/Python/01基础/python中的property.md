---
author: evo
comments: true
date: 2018-03-22 11:40:53+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python%e4%b8%ad%e7%9a%84property/
slug: python%e4%b8%ad%e7%9a%84property
title: 
wordpress_id: 557
categories:
- 随想与反思
tags:
- '@want_to_know'
- python
---

<!-- more -->


## 缘由：


之前就想，python中的属性到底是怎么做成类似private的？还是直接暴露的？还是通过c#类似的getset？还是只是通过名字前面加上'__'来实现的？。。现在才知道是使用的property


## 要点：




### 1.如何使用property和setter？




    import traceback


​    
    # 想对传进来的值限定为数字或者字符串
    # get set的话虽然验证了，但是代码可读性不够好，不够美观
    # 因此python使用 @property简化get/set方法
    
    class Student:
        # 这里用property装饰的时候实际上生成了一个新的对象，名字叫score
        # 同时这个方法也作为get
        @property
        def score(self):
            return self._score
    
        # 这个地方score.setter setter实际上是一个函数调用是把后面的这个score作为一个函数传进去，就告诉他，set的话就使用这个方法
        @score.setter
        def score(self, value):
            if not isinstance(value, int):
                raise ValueError('not int')
    
            elif (value < 0) or (value > 100):
                raise ValueError('not between 0 ~ 100')
    
            self._score = value
    
        @property
        def double_score(self):
            return self._score * 2


​    
    s = Student()
    s.score = 75
    print(s.score)
    
    try:
        s.score = 'abc'
    except ValueError:
        traceback.print_exc()
    
    try:
        s.score = 101
    except:
        traceback.print_exc()
    
    print(s.double_score)
    try:
        s.double_score = 150  # 这个是无法赋值的，因为@property只对应set，get的话需要单独写
    except AttributeError:
        traceback.print_exc()


输出：


    75
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_property.py", line 36, in <module>
        s.score = 'abc'
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_property.py", line 19, in score
        raise ValueError('not int')
    ValueError: not int
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_property.py", line 41, in <module>
        s.score = 101
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_property.py", line 22, in score
        raise ValueError('not between 0 ~ 100')
    ValueError: not between 0 ~ 100
    150
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_property.py", line 47, in <module>
        s.double_score = 150  # 这个是无法赋值的，因为@property只对应set，get的话需要单独写
    AttributeError: can't set attribute


注：可见这个还是比较好用的


### 2.自己实现property




    # 按照描述器的规范必须实现 get,set,delete 这三个方法。
    # 类的定义是在执行的过程中完成的
    class MyProperty:
        def __init__(self, fget=None, fset=None, fdel=None):
            print('__init__', fget)  # 看一下传进来的参数是什么
            self.fget = fget
            self.fset = fset
            self.fdel = fdel
    
        def __get__(self, instance, cls):
            if self.fget:
                print('__get__')
                return self.fget(instance)
    
        def __set__(self, instance, value):
            if self.fset:
                print('__set__')
                return self.fset(instance, value)
    
        def __delete__(self, instance):
            if self.fdel:
                print('__delete__')
                return self.fdel(instance)
    
        def getter(self, fn):  # 这个有点多余，因为初始化的时候已经有了
            self.fget = fn
    
        def setter(self, fn):
            print('setter', fn)
            self.fset = fn
    
        def deler(self, fn):  # 这个是什么时候调用的？
            self.fdel = fn


​    
    class Student:
        # MyProperty是一个类，这里就相当于在Student里面实例化了一个MyProperty类，然后把score作为fget参数传进去
        # 即生成了一个score对象？但是为什么这个对象叫做score？没有哪里说过这个MyProperty的实例叫做score吧？
        @MyProperty
        def score(self):
            return self._score
    
        # 这里就调用了刚刚创建的score对象的setter方法，然后将set_score作为参数传进去，初始化score里面的fset函数。
        # 但是这个MyPorperty的实例怎么就叫做score呢？
        @score.setter
        def set_score(self, value):  # 如果是标准的描述器的话读写都叫score
            print(value)
            self._score = value


​    
    s = Student()
    s.score = 95  # 为什么这个会调用到score的set呢？难道说对一个描述器进行赋值的时候就会调用__set__函数？
    print(s.score)


输出：


    __init__ <function Student.score at 0x000001F37F967D90>
    setter <function Student.set_score at 0x000001F37F967E18>
    __set__
    95
    __get__
    95


注：**这里对描述器还是有些不清楚，而且，为什么生成的这个MyProperty的对象叫做score？没有说吧？而且为什么 s.score=95 就会直接调用到score的set函数？**


## COMMENT：



