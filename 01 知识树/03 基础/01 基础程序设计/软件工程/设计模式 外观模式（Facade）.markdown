---
author: evo
comments: true
date: 2018-05-31 23:40:06+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%a4%96%e8%a7%82%e6%a8%a1%e5%bc%8f%ef%bc%88facade%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%a4%96%e8%a7%82%e6%a8%a1%e5%bc%8f%ef%bc%88facade%ef%bc%89'
title: 设计模式 外观模式（Facade）
wordpress_id: 7164
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

 	
  2. 《大话设计模式》作者 程杰




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 外观模式（Facade）





 	
  1. 外观模式：为子系统中一组接口提供一个一致的界面，即定义一个高层接口，增加子系统的易用性。

 	
  2. 外观模式完美体现了依赖倒转原则和迪米特法则。

 	
  3. 设计初期阶段，在MVC三层架构中，任意两层间建立外观Facade。

 	
  4. 子系统会因不断演化变得复杂，增加外观Facade提供简单简单接口减少依赖。

 	
  5. 在维护一个大的遗留系统时，新的开发又必须依赖其部分功能。此时，开发一个外观Facade类，从老系统中抽象出比较清晰的简单接口。让新系统只与Facade交互，而Facade与遗留代码交互所有的工作。






2.6 Facade 模式
-问题
举一个生活中的小例子，大凡开过学或者毕过业的都会体会到这样一种郁闷：你要去n 个地方办理n个手续（现在大学合并后就更加麻烦，因为可能那n个地方都隔的比较远）。 但是实际上我们需要的就是一个最后一道手续的证明而已，对于前面的手续是怎么办的、到 什么地方去办理我们都不感兴趣。

实际上在软件系统开发中也经常回会遇到这样的情况，可能你实现了一些接口（模块）， 而这些接口（模块）都分布在几个类中（比如A和B、C、D）: A中实现了一些接口，B中 实现一些接口（或者A代表一个独立模块，B、C、D代表另一些独立模块）。然后你的客户 程序员（使用你设计的开发人员）只有很少的要知道你的不同接口到底是在那个类中实现的， 绝大多数只是想简单的组合你的A_D的类的接口，他并不想知道这些接口在哪里实现的。

这里的客户程序员就是上面生活中想办理手续的郁闷的人！在现实生活中我们可能可以 很快想到找一个人代理所有的事情就可以解决你的问题（你只要维护和他的简单的一个接口 而己了 ！），在软件系统设计开发中我们可以通过一个叫做Fa5ade的模式来解决上面的问题。

■模式选择
我们通过Facade模式解决上面的问题，其典型的结构图为：

图 2-1: Facade Pattern 结构图


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10fe1b3f1e8.png)


Fa5ade模式的想法、思路和实现都非常简单，但是其思想却是非常有意义的。并且Fa5ade 设计模式在实际的开发设计中也是应用最广、最多的模式之一。

一个简单的例子就是，我在开发Visual CMCS项目【注释1】时候，在Visual CMCS中

我们将允许用户独立访问我们的编译子系统（词法、语法、语义、代码生成模块），这些都

是通过特定的类实现的，我们通过使用Fa5ade模式给用户提供一个高层的接口，供用户在 不想了解编译器实现的情况下去使用或重用我们的设计和实现。我们将提供一个 Compile 类作为Facade对象。

【注释1】：Visual CMCS是笔者主要设计和完成的一个C_minus语言（C语言的一个子集） 的编译系统，该系统可以生成源C-minus程序的汇编代码（并且可以获得编译中间阶段的 各个输出，如：词法、语法、语义中间代码等。），并可执行。Visual CMCS将作为一个对 教学、学习、研究开源的项目，它更加重要的特性是提供了一个框架（framework），感兴 趣的开发人员可以实现、测试自己感兴趣的模块，而无需实现整个的编译系统。 Visual CMCS采用VC++ 6.0的界面风格，更多内容请参见Visual CMCS网站。

-实现
♦完整代码示例（code）

facade.h：

    
    #ifndef DESIGN_PATTERNS_FACADE_H
    #define DESIGN_PATTERNS_FACADE_H
    
    
    class Stock1 {
    public:
      void Buy();
      void Sell();
    };
    
    class Stock2 {
    public:
      void Buy();
      void Sell();
    };
    
    class Reality1 {
    public:
      void Buy();
      void Sell();
    };
    
    class Fund {
    public:
      Fund();
      ~Fund();
      void BuyFund();
      void SellFund();
    
    private:
      Stock1 *stock1_;
      Stock2 *stock2_;
      Reality1 *reality1_;
    };
    
    
    #endif //DESIGN_PATTERNS_FACADE_H
    


facade.cpp：

    
    #include "facade.h"
    #include <iostream>
    
    void Stock1::Buy() {
      std::cout << "buy stock1" << std::endl;
    }
    
    void Stock1::Sell() {
      std::cout << "sell stock1" << std::endl;
    }
    
    void Stock2::Buy() {
      std::cout << "buy stock2" << std::endl;
    }
    
    void Stock2::Sell() {
      std::cout << "sell stock2" << std::endl;
    }
    
    void Reality1::Buy() {
      std::cout << "buy reality1" << std::endl;
    }
    
    void Reality1::Sell() {
      std::cout << "sell reality1" << std::endl;
    }
    
    Fund::Fund() {
      stock1_ = new Stock1;
      stock2_ = new Stock2;
      reality1_ = new Reality1;
    } 
    
    Fund::~Fund() {
      delete stock1_;
      delete stock2_;
      delete reality1_;
    }
    
    void Fund::BuyFund() {
      stock1_->Buy();
      stock2_->Buy();
      reality1_->Buy();
    }
    
    void Fund::SellFund() {
      stock1_->Sell();
      stock2_->Sell();
      reality1_->Sell();
    }
    


main.cpp：

    
    #include "facade.h"
    #include <iostream>
    
    
    int main() {
        Fund *fund_;
        fund_ = new Fund;
        fund_->BuyFund();
        fund_->SellFund();
        delete fund_;
    
        return 0;
    }


♦代码说明

Fa5ade模式的实现很简单，多余的解释完全是没有必要。

■讨论
Fa5ade模式在高层提供了一个统一的接口，解耦了系统。设计模式中还有另一种模式 Mediator也和Facade有类似的地方。但是Mediator主要目的是对象间的访问的解耦(通讯 时候的协议)，具体请参见Mediator文档。















* * *





# COMMENT



