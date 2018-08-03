---
author: evo
comments: true
date: 2018-05-31 23:38:51+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e4%bb%a3%e7%90%86%e6%a8%a1%e5%bc%8f%ef%bc%88proxy%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e4%bb%a3%e7%90%86%e6%a8%a1%e5%bc%8f%ef%bc%88proxy%ef%bc%89'
title: 设计模式 代理模式（Proxy）
wordpress_id: 7162
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





  1. 代理模式：为其他对象提供一种代理以控制对这个对象的访问。实际上是在访问对象时引入一定程度的间接性。


  2. 远程代理：为一个对象在不同地址空间提供局部代表，隐藏一个对象存在于不同空间的事实。如.Net加入Web引用，引入WebService，此时项目会生成WebReference的文件夹，就是代理。


  3. 虚拟代理：根据需要创建开销很大的对象，通过它存放实例化需很长时间的真实对象。HTML中的多图，就是通过虚拟代理代替了真实图片，存储路径和尺寸。


  4. 安全代理：控制真实对象的访问权限，用于对象应该拥有不同的访问权限时。


  5. 智能指引：当调用真实对象时，代理处理一些另外的事情。通过代理在访问对象时增加一些内务处理。








2.7 Proxy 模式
-问题
至少在以下集中情况下可以用Proxy模式解决问题：

1） 创建开销大的对象时候，比如显示一幅大的图片，我们将这个创建的过程交给代理 去完成，GoF称之为虚代理（Virtual Proxy）;

2） 为网络上的对象创建一个局部的本地代理，比如要操作一个网络上的一个对象（网 络性能不好的时候，问题尤其突出），我们将这个操纵的过程交给一个代理去完成，GoF称 之为远程代理（Remote Proxy）;

3） 对对象进行控制访问的时候，比如在Jive论坛中不同权限的用户（如管理员、普通 用户等）将获得不同层次的操作权限，我们将这个工作交给一个代理去完成，GoF称之为保

护代理（Protection Proxy）。

4） 智能指针（Smart Pointer），关于这个方面的内容，建议参看Andrew Koenig的《C++

沉思录》中的第5章。

■模式选择
Proxy模式典型的结构图为:


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/5Ah11e5CfA.png?imageslim)

图 2-1: Proxy Pattern 结构图 实际上，Proxy模式的想法非常简单，

-实现
♦完整代码示例（code）

proxy.h


    #ifndef DESIGN_PATTERNS_PROXY_H
    #define DESIGN_PATTERNS_PROXY_H

    #include <iostream>
    #include <string>

    class SchoolGirl {
    public:
      SchoolGirl() {}
      SchoolGirl(std::string);
      std::string GetName();
    private:
      std::string name_;
    };

    class GiveGift {
    public:
      virtual void GiveFlowers() = 0;
      virtual void GiveDolls() = 0;
    };

    class Pursuit: public GiveGift{
    public:
      Pursuit() {}
      Pursuit(SchoolGirl *);
      void GiveFlowers();
      void GiveDolls();

    private:
      SchoolGirl *school_girl_;
    };

    class Proxy: public GiveGift{
    public:
      Proxy() {}
      Proxy(SchoolGirl *);
      ~Proxy();
      void GiveFlowers();
      void GiveDolls();

    private:
      Pursuit *pursuit_;
    };


    #endif //DESIGN_PATTERNS_PROXY_H



proxy.cpp


    #include "proxy.h"

    SchoolGirl::SchoolGirl(std::string name): name_(name) {
    }

    std::string SchoolGirl::GetName() {
      return name_;
    }

    Pursuit::Pursuit(SchoolGirl *school_girl): school_girl_(school_girl){
    }

    void Pursuit::GiveFlowers() {
      std::cout << "Give flowers to " << school_girl_->GetName() << std::endl;
    }

    void Pursuit::GiveDolls() {
      std::cout << "Give dolls to " << school_girl_->GetName() << std::endl;
    }

    Proxy::Proxy(SchoolGirl *school_girl) {
      pursuit_ = new Pursuit(school_girl);
    }

    Proxy::~Proxy() {
      delete pursuit_;
    }

    void Proxy::GiveFlowers() {
      pursuit_->GiveFlowers();
    }

    void Proxy::GiveDolls() {
      pursuit_->GiveDolls();
    }


main.cpp


    #include "proxy.h"
    #include <iostream>


    int main() {
        SchoolGirl *school_girl_;
        Proxy *proxy_;
        school_girl_ = new SchoolGirl("Alice");
        proxy_ = new Proxy(school_girl_);
        proxy_->GiveFlowers();
        proxy_->GiveDolls();
        delete school_girl_;
        delete proxy_;


        return 0;
    }






♦代码说明

Proxy模式的实现很简单，这里不做多余解释。

可以看到，示例代码运行后，p的Request请求实际上是交给了 sub来实际执行。

■讨论
Proxy模式最大的好处就是实现了逻辑和实现的彻底解耦。








* * *





# COMMENT
