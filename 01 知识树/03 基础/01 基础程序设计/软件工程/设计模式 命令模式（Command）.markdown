---
author: evo
comments: true
date: 2018-05-31 23:47:15+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%91%bd%e4%bb%a4%e6%a8%a1%e5%bc%8f%ef%bc%88command%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%91%bd%e4%bb%a4%e6%a8%a1%e5%bc%8f%ef%bc%88command%ef%bc%89'
title: 设计模式 命令模式（Command）
wordpress_id: 7192
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





# 命令模式（Command）






  1. 命令模式：将请求分装为对象，将请求和执行分开，可以用不同的请求对客户参数化。可以对请求排队、通过或否决、记录日志、撤销或重做。


  2. 基于敏捷开发原则，不要给代码添加基于猜测而实际不需要的功能，在需要的时候通过重构实现。






7 Command 模式
■问题
Command模式通过将请求封装到一个对象（Command）中，并将请求的接受者存放到

具体的ConcreteCommand类中（Receiver）中，从而实现调用操作的对象和操作的具体实现 者之间的解耦。

■模式选择
Command模式的典型结构图为:


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/0B9G8i6H64.png?imageslim)

图 2-1： Command Pattern 结构图

Command 模式结构图中，将请求的接收者（处理者）放到 Command 的具体子类 ConcreteCommand中，当请求到来时（Invoker发出Invoke消息激活Command对象）， ConcreteCommand将处理请求交给Receiver对象进行处理。

-实现
♦完整代码示例

command.h


    #ifndef DESIGN_PATTERNS_COMMAND_H
    #define DESIGN_PATTERNS_COMMAND_H

    #include <vector>

    class Barbecuer {
    public:
      void BakeMutton();
      void BakeChicken();
    };

    class Command {
    public:
      Command() {}
      Command(Barbecuer*);
      virtual ~Command() {}
      virtual void ExecuteCommand() = 0;

    protected:
      Barbecuer* barbecuer_;
    };

    class BakeMuttonCommand: public Command {
    public:
      BakeMuttonCommand() {}
      BakeMuttonCommand(Barbecuer*);
      void ExecuteCommand();
    };

    class BakeChickenCommand: public Command {
    public:
      BakeChickenCommand() {}
      BakeChickenCommand(Barbecuer*);
      void ExecuteCommand();
    };

    class Waiter {
    public:
      void SetOrder(Command*);
      void CancelOrder(Command*);
      void Notify();

    private:
      std::vector <Command*> commands_;
    };


    #endif //DESIGN_PATTERNS_COMMAND_H



command.cpp


    #include "command.h"
    #include <string>
    #include <iostream>
    #include <typeinfo>

    void Barbecuer::BakeMutton() {
      std::cout << "bake mutton" << std::endl;
    }

    void Barbecuer::BakeChicken() {
      std::cout << "bake chicken" << std::endl;
    }

    Command::Command(Barbecuer *barbecuer): barbecuer_(barbecuer) {}

    BakeMuttonCommand::BakeMuttonCommand(Barbecuer *barbecuer): Command(barbecuer) {}

    void BakeMuttonCommand::ExecuteCommand() {
      barbecuer_->BakeMutton();
    }

    BakeChickenCommand::BakeChickenCommand(Barbecuer *barbecuer): Command(barbecuer) {}

    void BakeChickenCommand::ExecuteCommand() {
      barbecuer_->BakeChicken();
    }

    void Waiter::SetOrder(Command *command) {
      if(dynamic_cast<BakeChickenCommand*>(command)){
        std::cout << "chicken sold out" << std::endl;
      } else {
        commands_.push_back(command);
        std::cout << "add: " << std::string(typeid(*command).name()).substr(2) << std::endl;
      }
    }

    void Waiter::CancelOrder(Command *command) {
      for(std::vector <Command*> ::iterator it = commands_.begin(); it != commands_.end(); ++it) {
        if(*it == command) {
          commands_.erase(it);
          std::cout << "cancel: " << std::string(typeid(*command).name()).substr(2) << std::endl;
          return;
        }
      }
    }

    void Waiter::Notify() {
      for (std::vector<Command *>::iterator it = commands_.begin(); it != commands_.end(); ++it) {
        (*it)->ExecuteCommand();
      }
    }



main.cpp


    #include "command.h"
    #include <iostream>


    int main() {
        Barbecuer *barbecuer_;
        Command *bake_mutton_command1_;
        Command *bake_mutton_command2_;
        Command *bake_chicken_command_;
        Waiter *waiter_;
        barbecuer_ = new Barbecuer();
        bake_mutton_command1_ = new BakeMuttonCommand(barbecuer_);
        bake_mutton_command2_ = new BakeMuttonCommand(barbecuer_);
        bake_chicken_command_ = new BakeChickenCommand(barbecuer_);
        waiter_ = new Waiter();
        waiter_->SetOrder(bake_mutton_command1_);
        waiter_->SetOrder(bake_mutton_command2_);
        waiter_->SetOrder(bake_chicken_command_);
        waiter_->CancelOrder(bake_mutton_command2_);
        waiter_->Notify();
        delete barbecuer_;
        delete bake_mutton_command1_;
        delete bake_mutton_command2_;
        delete bake_chicken_command_;
        delete waiter_;


        return 0;
    }


注意到上面通过模板的方式来参数化请求的接收者，当然是为了简单演示。在复杂的情 况下我们会提供一个抽象Command对象，然后创建Command的子类以支持更复杂的处理。

■讨论
Command 模式的思想非常简单，但是 Command 模式也十分常见，并且威力不小。实 际上，Command模式关键就是提供一个抽象的Command类，并将执行操作封装到Command 类接口中，Command类中一般就是只是一些接口的集合，并不包含任何的数据属性(当然 在示例代码中，我们的Command类有一个处理操作的Receiver类的引用，但是其作用也仅 仅就是为了实现这个Command的Excute接口)。这种方式在是纯正的面向对象设计者最为 鄙视的设计方式，就像OO设计新手做系统设计的时候，仅仅将Class作为一个关键字，将 C种的全局函数找一个类封装起来就以为是完成了面向对象的设计。

但是世界上的事情不是绝对的，上面提到的方式在OO设计种绝大部分的时候可能是一 个不成熟的体现，但是在Command模式中却是起到了很好的效果。主要体现在：

1) Command 模式将调用操作的对象和知道如何实现该操作的对象解耦。在上面 Command的结构图中，Invoker对象根本就不知道具体的是那个对象在处理Excute 操作(当然要知道是Command类别的对象，也仅此而已)。

2) 在 Command 要增加新的处理操作对象很容易，我们可以通过创建新的继承自 Command 的子类来实现这一点。

3) Command模式可以和Memento模式结合起来，支持取消的操作。










* * *





# COMMENT
