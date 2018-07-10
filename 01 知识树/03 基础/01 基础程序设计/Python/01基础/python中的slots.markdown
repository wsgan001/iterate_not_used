---
author: evo
comments: true
date: 2018-03-22 11:24:40+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python-slots/
slug: python-slots
title: python中的slots
wordpress_id: 551
categories:
- 随想与反思
---

<!-- more -->


## 缘由：


之前没有见到过，总结下


## 要点：


pyton的类的属性是可以动态添加的，__slot__限定了class实例能添加的属性，__slot__仅的ui当前类实例起作用，对继承的子类是不起作用的。

代码如下：

    
    import traceback
    
    from types import MethodType
    
    
    class MyClass(object):
        __slots__ = ['name', 'set_name']  # 只能添加这两个名字的属性或者方法
    
    
    def set_name(self, name):
        self.name = name
    
    
    cls = MyClass()
    cls.name = 'Tom'
    cls.set_name = MethodType(set_name, cls)  # 动态的添加方法 MethodType 即方法类型
    cls.set_name('Jerry')
    print(cls.name)
    
    try:
        cls.age = 30
    except AttributeError:
        traceback.print_exc()  # 打印异常信息最好使用这个
    
    
    class ExtMyClass(MyClass):
        pass
    
    
    ext_cls = ExtMyClass()
    ext_cls.age = 30  # 可见子类并不会被两个名字所限制住
    print(ext_cls.age)


输出如下：

    
    Jerry
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/lesson_06_slots.py", line 21, in <module>
        cls.age = 30
    AttributeError: 'MyClass' object has no attribute 'age'
    30




## COMMENT：


是的，如果使用开源代码，发现不能添加一些属性或方法。。有可能就是代码已经限定只能用那些属性，这时你把它继承过来之后就可以了，通过子类绕过 slots 的限制
