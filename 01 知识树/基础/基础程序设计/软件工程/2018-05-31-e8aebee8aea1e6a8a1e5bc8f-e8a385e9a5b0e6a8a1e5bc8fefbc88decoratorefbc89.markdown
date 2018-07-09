---
author: evo
comments: true
date: 2018-05-31 23:40:28+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a3%85%e9%a5%b0%e6%a8%a1%e5%bc%8f%ef%bc%88decorator%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a3%85%e9%a5%b0%e6%a8%a1%e5%bc%8f%ef%bc%88decorator%ef%bc%89'
title: 设计模式 装饰模式（Decorator）
wordpress_id: 7169
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




# 装饰模式（Decorator）




在OO设计和开发过程，可能会经常遇到以下的情况：我们需要为一个己经定义好的类添加新的职责（操作），通常的情况我们会给定义一个新类继承自定义好的类，这样会带来一个问题（将在本模式的讨论中给出）。通过继承的方式解决这样的情况还带来了系统的复杂性，因为继承的深度会变得很深。

而Decorator提供了一种给类增加职责的方法，不是通过继承实现的，而是通过组合。







Decorator模式典型的结构图为:


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10fbb3b41b3.png)


在结构图中， ConcreteComponent 和 Decorator 需要有同样的接口，因此 ConcreteComponent和Decorator有着一个共同的父类。这里有人会问，让Decorator直接维 护一个指向ConcreteComponent引用（指针）不就可以达到同样的效果，答案是肯定并且是 否定的。肯定的是你可以通过这种方式实现，否定的是你不要用这种方式实现，因为通过这 种方式你就只能为这个特定的 ConcreteComponent 提供修饰操作了，当有了一个新的

ConcreteComponent你又要去新建一个Decorator来实现。但是通过结构图中的 ConcreteComponent和Decorator有一个公共基类，就可以利用OO中多态的思想来实现只要 是 Component 型别的对象都可以提供修饰操作的类，这种情况下你就算新建了 100 个 Component型别的类ConcreteComponent，也都可以由Decorator 一个类搞定。这也正是 Decorator模式的关键和威力所在了。

当然如果你只用给Component型别类添加一种修饰，则Decorator这个基类就不是很必 要了。


# 完整代码


decorator.h：

    
    #ifndef DESIGN_PATTERNS_DECORATOR_H
    #define DESIGN_PATTERNS_DECORATOR_H
    
    
    class Person {
    public:
      virtual void Show();
    };
    
    class Finery: public Person {
    public:
      Finery() {}
      Finery(Person*);
      void Show() {}
    
    protected:
      Person *component_;
    };
    
    class Tie: public Finery {
    public:
      Tie() {}
      Tie(Person*);
      void Show();
    };
    
    class Suit: public Finery {
    public:
      Suit() {}
      Suit(Person*);
      void Show();
    };
    
    class Shoes: public Finery {
    public:
      Shoes() {}
      Shoes(Person*);
      void Show();
    };
    
    
    #endif //DESIGN_PATTERNS_DECORATOR_H
    


decorator.cpp：

    
    #include "decorator.h"
    #include <iostream>
    
    void Person::Show() {
      std::cout << "person" << std::endl;
    }
    
    Finery::Finery(Person *component): component_(component) {}
    
    Tie::Tie(Person *component): Finery(component) {}
    
    void Tie::Show() {
      std::cout << "tie ";
      component_->Show();
    }
    
    Suit::Suit(Person *component): Finery(component) {}
    
    void Suit::Show() {
      std::cout << "suit ";
      component_->Show();
    }
    
    Shoes::Shoes(Person *component): Finery(component) {}
    
    void Shoes::Show() {
      std::cout << "shoes ";
      component_->Show();
    }


main.cpp：

    
    #include "decorator.h"
    
    
    
    int main() {
        Person* person_;
        Tie* tie_;
        Suit* suit_;
        Shoes* shoes_;
        person_ = new Person();
        tie_ = new Tie(person_);
        suit_ = new Suit(tie_);
        shoes_ = new Shoes(suit_);
        shoes_->Show();
        delete person_;
        delete shoes_;
        delete suit_;
        delete tie_;
        return 0;
    }


♦代码说明

Decorator 模式很简单，代码本身没有什么好说明的。运行示例代码可以看到 ConcreteDecorator 给 ConcreteComponent 类添加 了动作 AddedBehavior。

■讨论
Decorator模式和Composite模式有相似的结构图，其区别在Composite模式已经详细讨 论过了，请参看相应文档。另外GoF在《设计模式》中也讨论到Decorator和Proxy模式有 很大程度上的相似，初学设计模式可能实在看不出这之间的一个联系和相似，并且它们在结 构图上也很不相似。实际上，在本文档2.2节模式选择中分析到，让Decorator直接拥有一 个ConcreteComponent的引用（指针）也可以达到修饰的功能，大家再把这种方式的结构图 画出来，就和Proxy很相似了！

Decorator模式和Proxy模式的相似的地方在于它们都拥有一个指向其他对象的引用（指 针），即通过组合的方式来为对象提供更多操作（或者Decorator模式）间接性（Proxy模式）。 但是他们的区别是，Proxy模式会提供使用其作为代理的对象一样接口，使用代理类将其操 作都委托给Proxy直接进行。这里可以简单理解为组合和委托之间的微妙的区别了。

Decorator模式除了采用组合的方式取得了比采用继承方式更好的效果，Decorator模式 还给设计带来一种“即用即付”的方式来添加职责。在OO设计和分析经常有这样一种情况: 为了多态，通过父类指针指向其具体子类，但是这就带来另外一个问题，当具体子类要添加 新的职责，就必须向其父类添加一个这个职责的抽象接口，否则是通过父类指针是调用不到 这个方法了。这样处于高层的父类就承载了太多的特征（方法），并且继承自这个父类的所 有子类都不可避免继承了父类的这些接口，但是可能这并不是这个具体子类所需要的。而在 Decorator 模式提供了一种较好的解决方法，当需要添加一个操作的时候就可以通过 Decorator模式来解决，你可以一步步添加新的职责。



















* * *





# COMMENT





 	
  1. 装饰模式：动态的给一个对象添加一些额外的职能，把所需功能按顺序串联起来并进行控制。

 	
  2. 每个要装饰的功能放在单独的类中，并让这个类包装它所要修饰的对象。当需要执行特殊行为时，客户端就可以根据需要有选择的、有顺序的使用装饰功能包装对象了。

 	
  3. 装饰模式有效的把类的核心职能和装饰功能区分开了，并且可以去除相关类中重复的装饰逻辑。



