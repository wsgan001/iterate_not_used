---
author: evo
comments: true
date: 2018-05-31 10:19:46+00:00
layout: post
link: http://106.15.37.116/2018/05/31/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%8d%95%e4%be%8b%e6%a8%a1%e5%bc%8f%ef%bc%88singleton%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%8d%95%e4%be%8b%e6%a8%a1%e5%bc%8f%ef%bc%88singleton%ef%bc%89'
title: 设计模式 单例模式（Singleton）
wordpress_id: 7147
categories:
- 基础程序设计
tags:
- Design Patterns
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL






  1. [design-patterns-cpp](https://github.com/yogykwan/design-patterns-cpp)  作者： [Jennica](http://jennica.space/)  厉害的


  2. 《设计模式精解 - GoF 23种设计模式解析》


  3. 《大话设计模式》作者 程杰




# TODO






  * aaa





* * *





# INTRODUCTION






  * aaa





# 什么时候使用单例模式？


Singleton 模式应该是最常用到的了。

它解决的问题十分常见：怎么创建一个唯一的变量(对象)？

实际上，在基于过程的设计中我们是可以通过创建一个全局变量（对象）来实现的，在面向对象和面向过程结合的设计范式（如C++）中，我们也还是可以通过一个全局变量实现这一点。

但是，当我们到了纯粹的面向对象的范式中的时候，就只能通过 Singleton 模式来实现了。**为什么这个时候我不能还是设置一个全局变量？**


# Singleton 模式介绍


Singleton模式结构图如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/gk9emjI3C9.png?imageslim)

可以看到，我们是通过维护一个 static 的成员变量来记录这个唯一的对象实例的。然后通过提供一个 staitc 的接口 instance 来获得这个唯一的实例。


# 完整代码




## 代码如下


singleton.h：


    #ifndef DESIGN_PATTERNS_SINGLETON_H
    #define DESIGN_PATTERNS_SINGLETON_H


    #include <thread>
    #include <mutex>

    class Singleton {
    private:
      Singleton() {};
    public:
      static Singleton* GetInstance();

    private:
      static Singleton* instance;
      static std::mutex mtx;
    };


    #endif //DESIGN_PATTERNS_SINGLETON_H



singleton.cpp：


    #include "singleton.h"

    // 初始化 static 变量
    Singleton* Singleton::instance = NULL;
    std::mutex Singleton::mtx;

    //这个锁是不是有问题的，确认下
    Singleton* Singleton::GetInstance() {
        if (instance == NULL) {
            mtx.lock();
            if (instance == NULL) {
                instance = new Singleton();
            }
            mtx.unlock();
        }
        return instance;
    }


main.cpp：


    #include "singleton.h"


    int main() {

        Singleton* instance1;
        Singleton* instance2;

        instance1 = Singleton::GetInstance();
        instance2 = Singleton::GetInstance();

        delete instance1;
        if (instance1 != instance2) {
            delete instance2;
        }

        return 0;
    }


**确认下，这个锁写的是不是OK的。**


## 代码说明


有一点需要注意：由于 Singleton 不能被实例化，因此 我们将其构造函数声明为 protected 或者直接声明为 private 。




# Singleton 的优缺点


Singleton 模式在开发中经常用到，比如：




  * 因为有些变量必须是唯一的，比如说打印机的实例等。


  * 比如某个线程要是唯一的。


  * 有时候，某个 Factory（AbstractFactory）也要是唯一的，比如说连接数据库的时候，factory 可能对应了 sql 和access，但是我们同一个时刻只会对应一个 ，这时候也会用到 singleton 。


**感觉对 singleton 的理解还是不够，要再不从下，比如有什么特殊的东西或者例子，也要整合进来。**








前几天因为某些事情，和在ATC工作的一位朋友S聊了些技术方面的问题。S在学校 的时候就是很，那种传说中的N人，S在Review我之前写的Visual CMCS的一些代码，就 问到其中一个类为什么要使用 Singleton 模式？代码是几个月前写的，写的时候正是自己对 设计模式有一些学习积累和思考的时候，因此里面用到了不少的设计模式。当时简单的考虑 就是因为那是一个工厂类，我在程序生命周期内仅需要一个该工厂类的对象就可以了，因此 很直观地使用了 Singleton模式。当然我当时的回答就是因为仅需要提供一个对象就可以，S 马上就问那为什么不用一个全局变量来实现？我知道，在纯面向对象支持的编程语言（例如

C#Java）中，这个问题可以很简单地理解为没有全局变量这样一个概念支持，但是对于向 C++等这种支持面向过程的编程语言中这个回答就不是很合适了（至少像在找借口）。

实际上这个问题的答案并不重要（虽然是这个文档的主题），更加重要的是我们在作设 计的时候实际上要好好综合考虑，为什么要使用这种设计模式，也就是要防止滥用设计模式 的情况。Singleton模式可能可以说是最简单的设计模式了，其应用的场景和示例实在是没 有太多可以再重复，但是以下的两个问题还是需要我们进一步的审视：

1）    Singleton模式VS全局变量。很多情况下，我们使用Singleton模式达到的效果和 全局变量达到的效果类似。但是，全局变量不能防止实例化多个对象。GoF在《设计模式》 中给出了 Singleton 模式的意图“保证一个类仅有一个对象，并提供一个访问它的全局访问 点”，因此全局变量可以达到后面半句的效果，但是却不能保证仅有一个对象被实例化。另 外，使用全局变量将使得对象在无论是否用到都要被创建，而 Singleton 模式则没有这个瑕 疵。

2）    Singleton的子类化问题。一般来说Singleton的子类并不是Singleton，因此在保证 Singleton的正确子类化，在实现上要注意以下几点（C++实现）：

①    父类Singleton的构造函数为Protected，目的是为了要让Singleton子类访问，而

不让Client程序访问（防止被其他方式实例化类）;Singleton子类构造函数声明为private

或者protected，并且将父类Singleton声明为子类Singleton的友元，目的是在父类

Singleton中可以实例化子类Singleton，而Client程序不可访问（防止被其他方式实例

化类）。

②    我们必须改写父类Singleton中的Instance方法（获得唯一实例方法）。因为Instance

是static的成眼函数，不能以多态的方式实现之。因此我们必须在父类Singleton中就是

提供真正实例化Singleton子类的信息。我们可以通过到某一个专门的地方获取Singleton

子类的信息，例如提供一个获取函数，在 Instance 实例化 Singleton 子类之前获得这个

信息，再根据这个信息去实例化具体的 Singleton 子类。我这里提供的示例程序中是，

提供一个全局的GetSingletionType （），返回应该实例化的Singleton具体子类。具体的

实现则是是通过随机数来确定的方式，详细请参看代码。以下就将整个代码给出：





* * *





# COMMENT






  1. 单例模式：让类自身保证它只有一个实例，并提供一个全局访问点。


  2. 多线程下单例模式可能失效，需要采取双重锁定的的方式，确保被锁定的代码同一时刻只被一个进程访问。


  3. 饿汉式单例：即静态初始化方式，在类初始化时产生私有单例对象，会提前占用资源；渴汉式单例：在第一次被引用时将自己初始化，会产生多线程访问安全问题，需要添加双重锁定。
