---
author: evo
comments: true
date: 2018-05-31 23:47:38+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%a8%a1%e6%9d%bf%e6%96%b9%e6%b3%95%e6%a8%a1%e5%bc%8f%ef%bc%88template-method%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%a8%a1%e6%9d%bf%e6%96%b9%e6%b3%95%e6%a8%a1%e5%bc%8f%ef%bc%88template-method%ef%bc%89'
title: 设计模式 模板方法模式（Template Method）
wordpress_id: 7193
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




# 模板方法模式（Template Method）






  1. 模板方法模式：定义一个操作中的算法框架，将一些步骤延迟到子类中。子类在不改变框架的前提下就可以重新定义某些特定步骤。


  2. 当不变和可变的行为在子类中混到一起时，可以通过把重复的行为移到同一地方，帮助子类摆脱重复不变行为的纠缠。






Template 模式
■问题
在面向对象系统的分析与设计过程中经常会遇到这样一种情况:对于某一个业务逻辑

（算法实现）在不同的对象中有不同的细节实现，但是逻辑（算法）的框架（或通用的应用

算法）是相同的。Template提供了这种情况的一个实现框架。

Template模式是采用继承的方式实现这一点：将逻辑（算法）框架放在抽象基类中，并

定义好细节的接口，子类中实现细节 【注释1】

【注释1 : Strategy模式解决的是和Template模式类似的问题，但是Strategy模式是将逻辑 （算法）封装到一个类中，并采取组合（委托）的方式解决这个问题。

■模式选择
解决2.1中问题可以采取两种模式来解决，一是Template模式，二是Strategy模式。本 文当给出的是Template模式。一个通用的Template模式的结构图为：

图2-1: Template 模式结构图


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/ja28mA5giB.png?imageslim)

Template模式实际上就是利用面向对象中多态的概念实现算法实现细节和高层接口的 松耦合。可以看到Template模式采取的是继承方式实现这一点的，由于继承是一种强约束 性的条件，因此也给 Template 模式带来一些许多不方便的地方（有关这一点将在讨论中展

开)。

-实现
♦完整代码示例(code)

template_method.h


    #ifndef DESIGN_PATTERNS_TEMPLATE_METHOD_H
    #define DESIGN_PATTERNS_TEMPLATE_METHOD_H

    #include <string>

    class TestPaper {
    public:
      void Question1();
      void Question2();
      void Question3();

    protected:
      virtual std::string Answer1() = 0;
      virtual std::string Answer2() = 0;
      virtual std::string Answer3() = 0;
    };

    class TestPaperA: public TestPaper {
      std::string Answer1();
      std::string Answer2();
      std::string Answer3();
    };

    class TestPaperB: public TestPaper {
      std::string Answer1();
      std::string Answer2();
      std::string Answer3();
    };


    #endif //DESIGN_PATTERNS_TEMPLATE_METHOD_H



template_method.cpp


    #include "template_method.h"
    #include <iostream>

    void TestPaper::Question1() {
      std::cout << "question 1: " << Answer1() << std::endl;
    }

    void TestPaper::Question2() {
      std::cout << "question 2: " << Answer2() << std::endl;
    }

    void TestPaper::Question3() {
      std::cout << "question 3: " << Answer3() << std::endl;
    }

    std::string TestPaperA::Answer1() {
      return "a";
    }

    std::string TestPaperA::Answer2() {
      return "a";
    }

    std::string TestPaperA::Answer3() {
      return "a";
    }

    std::string TestPaperB::Answer1() {
      return "b";
    }

    std::string TestPaperB::Answer2() {
      return "b";
    }

    std::string TestPaperB::Answer3() {
      return "b";
    }




main.cpp


    #include "template_method.h"
    #include <iostream>


    int main() {
        TestPaper *test_paper_a_;
        TestPaper *test_paper_b_;
        test_paper_a_ = new TestPaperA();
        test_paper_a_->Question1();
        test_paper_a_->Question2();
        test_paper_a_->Question3();

        test_paper_b_ = new TestPaperB();
        test_paper_b_->Question1();
        test_paper_b_->Question2();
        test_paper_b_->Question3();

        return 0;
    }


♦代码说明

由于 Template 模式的实现代码很简单，因此解释是多余的。其关键是将通用算法(逻 辑)封装起来，而将算法细节让子类实现(多态)。

唯一注意的是我们将原语操作(细节算法)定义未保护(Protected)成员，只供模板方 法调用(子类可以)。

■讨论
Template 模式是很简单模式，但是也应用很广的模式。如上面的分析和实现中阐明的 Template是采用继承的方式实现算法的异构，其关键点就是将通用算法封装在抽象基类中，

并将不同的算法细节放到子类中实现。

Template模式获得一种反向控制结构效果，这也是面向对象系统的分析和设计中一个原 则DIP （依赖倒置：Dependency Inversion Principles）。其含义就是父类调用子类的操作（高

层模块调用低层模块的操作），低层模块实现高层模块声明的接口。这样控制权在父类（高

层模块），低层模块反而要依赖高层模块。

继承的强制性约束关系也让 Template 模式有不足的地方，我们可以看到对于 ConcreteClass类中的实现的原语方法Primitive1（），是不能被别的类复用。假设我们要创建 一个AbstractClass的变体AnotherAbstractClass，并且两者只是通用算法不一样，其原语操 作想复用AbstractClass的子类的实现。但是这是不可能实现的，因为ConcreteClass继承自 AbstractClass，也就继承了 AbstractClass 的通用算法，AnotherAbstractClass 是复用不了 ConcreteClass的实现，因为后者不是继承自前者。

Template模式暴露的问题也正是继承所固有的问题，Strategy模式则通过组合（委托） 来达到和Template模式类似的效果，其代价就是空间和时间上的代价，关于Strategy模式的 详细讨论请参考Strategy模式解析。

















* * *





# COMMENT
