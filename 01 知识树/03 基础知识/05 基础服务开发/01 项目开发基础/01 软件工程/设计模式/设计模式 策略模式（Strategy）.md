---
author: evo
comments: true
date: 2018-05-31 23:44:44+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%ad%96%e7%95%a5%e6%a8%a1%e5%bc%8f%ef%bc%88strategy%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%ad%96%e7%95%a5%e6%a8%a1%e5%bc%8f%ef%bc%88strategy%ef%bc%89'
title: 设计模式 策略模式（Strategy）
wordpress_id: 7180
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





# 策略模式（Strategy）






  1. 面向对象中并非类越多越好，类的划分是为了封装，但分类的基础是抽象，具有相同属性和功能的对象的抽象集合才是类。


  2. 策略模式：定义算法家族并分别封装，他们完成的工作相同，只是实现不同，可以互相替换。继承有助于析取这些算法的公共功能。此模式让算法的变化不会影响到使用算法的用户。


  3. 策略与工厂模式结合，使客户端需要认识的类减少，耦合度更加降低。


  4. 策略模式可以简化单元测试，因为每个算法可以通过自己的接口单独测试。


  5. 只要在不同时间内应用不同的业务规则，就可以考虑用策略模式来处理这种变化的可能性。






Strategy 模式
■问题
Strategy模式和Template模式要解决的问题是相同（类似）的，都是为了给业务逻辑（算 法）具体实现和抽象接口之间的解耦。Strategy模式将逻辑（算法）封装到一个类（Context） 里面，通过组合的方式将具体算法的实现在组合对象中实现，再通过委托的方式将抽象接口 的实现委托给组合对象实现。State模式也有类似的功能，他们之间的区别将在讨论中给出。 ■模式选择

Strategy 模式典型的结构图为:


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/K2EIbCaAkj.png?imageslim)

图 2-1: Strategy Pattern 结构图

这里的关键就是将算法的逻辑抽象接口(DoAction)封装到一个类中(Context)，再 通过委托的方式将具体的算法实现委托给具体的Strategy类来实现(ConcreteStrategeA 类)。

-实现
♦完整代码示例(code)

♦代码说明

strategy.h


    #ifndef DESIGN_PATTERNS_STRATEGY_H
    #define DESIGN_PATTERNS_STRATEGY_H

    #include <string>

    class CashSuper {
    public:
      virtual ~CashSuper() {}
      virtual double AcceptCash(double) = 0;
    };

    class CashNormal: public CashSuper {
    public:
      double AcceptCash(double);
    };

    class CashRebate: public CashSuper {
    public:
      CashRebate() {}
      CashRebate(double);
      double AcceptCash(double);

    private:
      double money_rebate_;
    };

    class CashReturn: public CashSuper {
    public:
      CashReturn() {}
      CashReturn(double, double);
      double AcceptCash(double);

    private:
      double money_condition_;
      double money_return_;
    };

    class CashContext {
    public:
      CashContext() {}
      CashContext(std::string, std::string);
      ~CashContext();
      double GetResult(double);

    private:
      CashSuper *cash_;
    };


    #endif //DESIGN_PATTERNS_STRATEGY_H



strategy.cpp


    #include "strategy.h"
    #include <iostream>
    #include <sstream>

    double CashNormal::AcceptCash(double money) {
      return money;
    }

    CashRebate::CashRebate(double money_rebate): money_rebate_(money_rebate) {}

    double CashRebate::AcceptCash(double money) {
      return money * money_rebate_;
    }

    CashReturn::CashReturn(double money_condition, double money_return):
        money_condition_(money_condition), money_return_(money_return) {}

    double CashReturn::AcceptCash(double money) {
      return money - (int)(money / money_condition_) * money_return_;
    }

    CashContext::CashContext(std::string type, std::string number) {
      if(type == "normal") {
        cash_ = new CashNormal();
      } else if (type == "rebate") {
        std::stringstream ss;
        double money_rebate;
        ss << number;
        ss >> money_rebate;
        cash_ = new CashRebate(money_rebate);
      } else if (type == "return") {
        std::stringstream ss;
        double money_condition, money_return;
        ss << number;
        ss >> money_condition >> money_return;
        cash_ = new CashReturn(money_condition, money_return);
      }
    }

    CashContext::~CashContext() {
      delete cash_;
    }

    double CashContext::GetResult(double money) {
      double result = cash_->AcceptCash(money);
      std::cout << result << std::endl;
      return result;
    }




main.cpp


    #include "strategy.h"
    #include <iostream>


    int main() {
        CashContext *cash_context_;
        cash_context_ = new CashContext("rebate", "0.8");
        cash_context_->GetResult(1000);

        cash_context_ = new CashContext("return", "300 100");
        cash_context_->GetResult(1000);
        delete cash_context_;

        return 0;
    }




Strategy模式的代码很直观，关键是将算法的逻辑封装到一个类中。

■讨论
可以看到Strategy模式和Template模式解决了类似的问题，也正如在Template模式中 分析的，Strategy模式和Template模式实际是实现一个抽象接口的两种方式：继承和组合之 间的区别。要实现一个抽象接口，继承是一种方式：我们将抽象接口声明在基类中，将具体 的实现放在具体子类中。组合(委托)是另外一种方式:我们将接口的实现放在被组合对象 中，将抽象接口放在组合类中。这两种方式各有优缺点，先列出来:

1) 继承:

■ 优点

1)易于修改和扩展那些被复用的实现。

■ 缺点

1) 破坏了封装性，继承中父类的实现细节暴露给子类了；

2) “白盒”复用，原因在1)中；

3） 当父类的实现更改时，其所有子类将不得不随之改变

4） 从父类继承而来的实现在运行期间不能改变（编译期间就已经确定了）。

2） 组合

■ 优点




  1. 1） “黑盒”复用，因为被包含对象的内部细节对外是不可见的；


  2. 2） 封装性好，原因为1）；


  3. 3） 实现和抽象的依赖性很小（组合对象和被组合对象之间的依赖性小）；


  4. 4） 可以在运行期间动态定义实现（通过一个指向相同类型的指针，典型的是抽象


基类的指针）。

■ 缺点

1）系统中对象过多。

从上面对比中我们可以看出，组合相比继承可以取得更好的效果，因此在面向对象

的设计中的有一条很重要的原则就是：优先使用（对象）组合，而非（类）继承（Favor Composition Over Inheritance。

实际上，继承是一种强制性很强的方式，因此也使得基类和具体子类之间的耦合

性很强。例如在Template模式中在ConcreteClassl中定义的原语操作别的类是不能够直 接复用（除非你继承自AbstractClass，具体分析请参看Template模式文档）。而组合（委 托）的方式则有很小的耦合性，实现（具体实现）和接口（抽象接口）之间的依赖性很 小，例如在本实现中，ConcreteStrategyA的具体实现操作很容易被别的类复用，例如我 们要定义另一个Context类AnotherContext，只要组合一个指向Strategy的指针就可以 很容易地复用ConcreteStrategyA的实现了。

我们在Bridge模式的问题和Bridge模式的分析中，正是说明了继承和组合之间的 区别。请参看相应模式解析。

另外Strategy模式很State模式也有相似之处，但是State模式注重的对象在不同的 状态下不同的操作。两者之间的区别就是State模式中具体实现类中有一个指向Context 的引用，而Strategy模式则没有。具体分析请参看相应的State模式分析中。










* * *





# COMMENT
