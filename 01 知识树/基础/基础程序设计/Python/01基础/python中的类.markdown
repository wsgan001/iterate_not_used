---
author: evo
comments: true
date: 2018-03-20 11:54:47+00:00
layout: post
link: http://106.15.37.116/2018/03/20/python-class/
slug: python-class
title: python中的类
wordpress_id: 476
categories:
- 随想与反思
tags:
- '@want_to_know'
- python
---

<!-- more -->缘由：

看书和看视频的时候经常想到一些问题，因此总结下：


## 问题：




### 1.class 中的 self是什么？


















这个 _self_ 就是个代指。代指了自己所在的class。_self_ 本身作为一个代词，并不一定要叫self。你也可以用个其他什么来代替。只不过，必须得是这个类的所有子方法的第一个参数。

比如：

    
    class NormalClass:
        def bar(juicy, aaa):
            return aaa
    
    n = NormalClass()
    n.bar('aaaa')


输出：

    
    'aaaa'


当调用 n.bar('aaaa') 的时候，Python默认将n传给self，所以其实我们做的是 n.bar(n) 。


### 2.python中的私有变量和共有变量到底是什么样的？在书中看到过，总结下：


如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线 ‘__’，在Python中，实例的变量名如果以 ‘__’ 开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问









### 3.到底要不要对每个变量都设置private然后用 Getter+Setter 呢？这个问题一直想知道。


**未解决**


### 4.类有多个父类的时候，继承关系是怎么样的？书上看到过，但是忘记了，因此记下：



    
    class D(object):
        def bar(self):
            print('D.bar')
    
    
    class C(D):
        def bar(self):
            print('C.bar')
    
    
    class B(D):
        pass
    
    
    class A(B, C):
        pass
    
    
    a = A()
    # 执行bar方法时
    # 首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去C类中找，如果C类中么有，则继续去D类中找，如果还是未找到，则报错
    # 所以，查找顺序：A --> B --> C --> D
    # 在上述查找bar方法的过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
    a.bar()<span id="mce_marker" data-mce-type="bookmark" data-mce-fragment="1">​</span>


输出如下：

    
    C.bar




### 5.刚看到说python里面没有多态的概念。之前没有注意到这个，因此记下：


















Pyhon不支持多态并且也用不到多态，多态的概念是应用于Java和C#这一类强类型语言中，而Python崇尚“鸭子类型（Duck Typing）”。

什么是鸭子类型？即在Python中，只要是能“不报错运行”的类型，都可以塞进参数中去。

这一点不同于强类型的语言，一个类型的obj只能一种事。

代码如下：

    
    class F1:
        pass
    
    
    class S1(F1):
        def show(self):
            print('S1.show')
    
    
    class S2:
        def show(self):
            print('S2.show')
    
    
    # 在Java或C#中定义函数参数时，必须指定参数的类型，也即是说，我们如果用
    # Java写下面的Func，需要告知，obj是F1类还是其他什么东西。
    # 如果限定了F1，那么S2是不可以被采纳的。
    # 然而，在Python中，一切都是Obj，它不care你到底是什么类，直接塞进去就可以
    
    def Func(obj):
        """Func函数需要接收一个F1类型或者F1子类的类型"""
        obj.show()
    
    
    s1_obj = S1()
    Func(s1_obj)  # 在Func函数中传入S1类的对象 s1_obj，执行 S1 的show方法，结果：S1.show
    
    s2_obj = S2()
    Func(s2_obj)  # 在Func函数中传入Ss类的对象 ss_obj，执行 S2 的show方法，结果：S2.show


输出：

    
    S1.show
    S2.show




### 6.一直没明白为什么setattr()可以设定一个不是通过self.来设定的变量。


关于getattr()、setattr()、hasattr()的用法如下：

    
    import traceback
    
    
    class MyObject():
        def __init__(self):
            self.x = 0
    
        def power(self):
            return self.x * self.x
    
    
    obj = MyObject()
    
    print(hasattr(obj, 'x'))
    setattr(obj, 'x', 10)
    print(obj.x)
    print(hasattr(obj, 'y'))
    try:
        print(obj.y)
    except:
        traceback.print_exc()
    
    setattr(obj, 'y', 19)  # 为什么可以设定一个不存在的属性？
    hasattr(obj, 'y')
    print(getattr(obj, 'y'))
    print(obj.y)
    
    print(getattr(obj, 'z', 404))  # 不存在的时候会返回默认值。
    
    hasattr(obj, 'power')
    print(getattr(obj, 'power'))
    fn = getattr(obj, 'power')  # 将这个属性赋值给变量fn
    print(fn)  # fn指向obj.power
    print(fn())<span id="mce_marker" data-mce-type="bookmark" data-mce-fragment="1">​</span>


输出如下：

    
    True
    10
    False
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/e4.py", line 19, in <module>
        print(obj.y)
    AttributeError: 'MyObject' object has no attribute 'y'
    19
    19
    404
    <bound method MyObject.power of <__main__.MyObject object at 0x000001B056A7A3C8>>
    <bound method MyObject.power of <__main__.MyObject object at 0x000001B056A7A3C8>>
    100<span id="mce_marker" data-mce-type="bookmark" data-mce-fragment="1">​</span>


备注：**为什么 setattr() 可以设定一个不是通过self.来设定的变量？ fn=getattr(obj,'power') 这种用法一般在什么时候用？感觉有些奇怪的用法。**


### 7.类的实例属性和类属性之间的关系。


类的实例属性：

    
    class Student(object):
        name = 'Student'
    
    
    s = Student()  # 创建实例s
    print(s.name)  # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
    print(Student.name)  # 打印类的name属性
    
    s.name = 'Michael'  # 给实例绑定name属性
    print(s.name)  # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
    print(Student.name)  # 但是类属性并未消失，用Student.name仍然可以访问
    
    del s.name  # 如果删除实例的name属性
    print(s.name)  # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
    


输出：

    
    Student
    Student
    Michael
    Student
    Student


















可见，在编写程序的时候，实例属性和类属性不要使用相同的名字，因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性。。


### 7.类的实例属性和类属性，对这个一直有一点不明白，实例属性一定要在init里面用self.来声明吗？如果不这样的话别人岂不是要整个类都要翻一遍才能找到？还有类属型一定要放在init的函数前面吗？


**需查找**


















### 8.一直没明白在一个类中，是较为高级的函数放在前面呢？还是较为低级的函数放在前面？函数的排放有什么规律吗？


**需查找**























