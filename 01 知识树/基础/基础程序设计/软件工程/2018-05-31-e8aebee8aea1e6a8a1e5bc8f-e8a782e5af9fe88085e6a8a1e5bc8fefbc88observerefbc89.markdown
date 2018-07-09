---
author: evo
comments: true
date: 2018-05-31 23:45:40+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a7%82%e5%af%9f%e8%80%85%e6%a8%a1%e5%bc%8f%ef%bc%88observer%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a7%82%e5%af%9f%e8%80%85%e6%a8%a1%e5%bc%8f%ef%bc%88observer%ef%bc%89'
title: 设计模式 观察者模式（Observer）
wordpress_id: 7182
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





# 观察者模式（Observer）





 	
  1. 观察者模式：多个观察者对象同时监听某一主题（通知者）对象，当该主题对象状态变化时会通知所有观察者对象，使它们能更新自己。

 	
  2. 具体观察者保存一个指向具体主题对象的引用，抽象主题保存一个抽象观察者的引用集合，提供一个可以添加或删除观察者的接口。

 	
  3. 抽象模式中有两方面，一方面依赖另一方面，使用观察者模式可将两者独立封装，解除耦合。

 	
  4. 观察者模式让主题和观察者双方都依赖于抽象接口，而不依赖于具体。

 	
  5. 委托就是一种引用方法类型。委托可看作函数的类，委托的实例代表具体函数。在主题对象内声明委托，不再依赖抽象观察者。

 	
  6. 一个委托可以搭载多个相同原形和形式（参数和返回值）的方法，这些方法不需要属于一个类，且被依次唤醒。










Observer 模式
■问题
Observer 模式应该可以说是应用最多、影响最广的模式之一，因为 Observer 的一个实 例Model/View/Control （MVC）结构在系统开发架构设计中有着很重要的地位和意义，MVC 实现了业务逻辑和表示层的解耦。个人也认为Observer模式是软件开发过程中必须要掌握 和使用的模式之一。在MFC中，Doc/View （文档视图结构）提供了实现MVC的框架结构 （有一个从设计模式（Observer模式）的角度分析分析Doc/View的文章正在进一步的撰写 当中，遗憾的是时间：））。在Java阵容中，Struts则提供和MFC中Doc/View结构类似的实 现MVC的框架。另外Java语言本身就提供了 Observer模式的实现接口，这将在讨论中给 出。

当然，MVC只是Observer模式的一个实例。Observer模式要解决的问题为：建立一个 一（Subject）对多（Observer）的依赖关系，并且做到当“一”变化的时候，依赖这个“一” 的多也能够同步改变。最常见的一个例子就是:对同一组数据进行统计分析时候，我们希望

能够提供多种形式的表示（例如以表格进行统计显示、柱状图统计显示、百分比统计显示等）。

这些表示都依赖于同一组数据，我们当然需要当数据改变的时候，所有的统计的显示都能够 同时改变。Observer模式就是解决了这一个问题。

■模式选择
Observer模式典型的结构图为:![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b110242d7efd.png)

图 2-1: Observer Pattern 结构图

这里的目标Subject提供依赖于它的观察者Observer的注册（Attach）和注销（Detach） 操作，并且提供了使得依赖于它的所有观察者同步的操作（Notify）。观察者Observer则提 供一个Update操作，注意这里的Observer的Update操作并不在Observer改变了 Subject目 标状态的时候就对自己进行更新，这个更新操作要延迟到Subject对象发出Notify通知所有 Observer进行修改（调用Update）。

-实现
♦完整代码示例

observer.h

    
    #ifndef DESIGN_PATTERNS_OBSERVER_H
    #define DESIGN_PATTERNS_OBSERVER_H
    
    #include <string>
    #include <vector>
    
    class Notifier;
    
    class Observer {
    public:
      Observer() {}
      Observer(std::string);
      virtual ~Observer() {}
      void SetNotifier(Notifier *);
      virtual void Update() = 0;
    
    protected:
      std::string name_;
      Notifier *notifier_;
    };
    
    class StockObserver: public Observer {
    public:
      StockObserver() {}
      StockObserver(std::string);
      void Update();
    };
    
    class NbaObserver: public Observer {
    public:
      NbaObserver() {}
      NbaObserver(std::string);
      void Update();
    };
    
    class Notifier {
    public:
      virtual ~Notifier() {}
      void Attach(Observer *);
      void Detach(Observer *);
      void SetState(std::string);
      std::string GetState();
      void Notify();
    
    protected:
      std::vector <Observer*> observers_;
      std::string state_;
    };
    
    class Secretary: public Notifier {
    };
    
    class Boss: public Notifier {
    };
    
    
    #endif //DESIGN_PATTERNS_OBSERVER_H
    


observer.cpp

    
    #include "observer.h"
    #include <iostream>
    
    Observer::Observer(std::string name): name_(name) {}
    
    void Observer::SetNotifier(Notifier *notifier) {
      notifier_ = notifier;
    }
    
    void Notifier::Attach(Observer * observer) {
      observers_.push_back(observer);
    }
    
    void Notifier::Detach(Observer * observer) {
      for(std::vector <Observer*> ::iterator it = observers_.begin(); it != observers_.end(); ++it) {
        if(*it == observer) {
          observers_.erase(it);
          return;
        }
      }
    }
    
    void Notifier::SetState(std::string state) {
      state_ = state;
    }
    
    std::string Notifier::GetState() {
      return state_;
    }
    
    void Notifier::Notify() {
      for(std::vector <Observer*> ::iterator it = observers_.begin(); it != observers_.end(); ++it) {
        (*it)->Update();
      }
    }
    
    StockObserver::StockObserver(std::string name): Observer(name) {}
    
    void StockObserver::Update() {
      std::cout << name_ << ", " << notifier_->GetState() << ", close stock" << std::endl;
    }
    
    NbaObserver::NbaObserver(std::string name): Observer(name) {}
    
    void NbaObserver::Update() {
      std::cout << name_ << ", " << notifier_->GetState() << ", close NBA" << std::endl;
    }


main.cpp

    
    #include "observer.h"
    #include <iostream>
    
    
    int main() {
        Boss *boss_;
        StockObserver *stock_observer_;
        NbaObserver *nba_observer_;
        boss_ = new Boss();
        stock_observer_ = new StockObserver("Alice");
        nba_observer_ = new NbaObserver("Bob");
        boss_->SetState("boss is back himself");
    
        stock_observer_->SetNotifier(boss_);
        nba_observer_->SetNotifier(boss_);
        boss_->Attach(stock_observer_);
        boss_->Attach(nba_observer_);
        boss_->Notify();
    
        boss_->Detach(nba_observer_);
        boss_->Notify();
        delete boss_;
        delete stock_observer_;
        delete nba_observer_;
    
    
        return 0;
    }


♦代码说明

在Observer模式的实现中，Subject维护一个list作为存储其所有观察者的容器。每当 调用Notify操作就遍历list中的Observer对象，并广播通知改变状态（调用Observer的Update 操作）。目标的状态state可以由Subject自己改变（示例），也可以由Observer的某个操作引 起state的改变（可调用Subject的SetState操作）。Notify操作可以由Subject目标主动广播 （示例），也可以由Observer观察者来调用（因为Observer维护一个指向Subject的指针）。

运行示例程序，可以看到当Subject处于状态“old”时候，依赖于它的两个观察者都显 示“old”，当目标状态改变为“new”的时候，依赖于它的两个观察者也都改变为“new”。

■讨论
Observer是影响极为深远的模式之一，也是在大型系统开发过程中要用到的模式之一。 除了 MFC>Struts提供了 MVC的实现框架，在Java语言中还提供了专门的接口实现Observer 模式：通过专门的类Observable及Observer接口来实现MVC编程模式，其UML图可以表 示为:

Java中实现MVC的UML图。

这里的Observer就是观察者，Observable则充当目标Subject的角色。

Observer模式也称为发布一订阅（publish-subscribe），目标就是通知的发布者，观察者

则是通知的订阅者（接受通知）。









* * *





# COMMENT



