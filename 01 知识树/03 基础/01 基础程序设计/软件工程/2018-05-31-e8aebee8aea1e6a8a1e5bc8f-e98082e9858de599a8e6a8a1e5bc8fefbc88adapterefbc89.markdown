---
author: evo
comments: true
date: 2018-05-31 23:39:27+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e9%80%82%e9%85%8d%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88adapter%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e9%80%82%e9%85%8d%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88adapter%ef%bc%89'
title: 设计模式 适配器模式（Adapter）
wordpress_id: 7163
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







# 适配器模式（Adapter）是用来解决什么问题的？


Adapter模式解决的问题在生活中经常会遇到，比如说：

假设我们有一个 Team 为外界提供 S 类服务，但是我们 Team 里面没有能够完成此项人物的 member，然后我们得知 A 可以完成这项服务（他把这项任务重新取了个名字叫 S' ，并且他不对外公布他的具体实现）。为了保证我们对外的服务类别的一致性（提供S服务），这时我们有两种方式解决这个问题：



 	
  1. 把 A 直接招安到我们Team为我们工作，提供 S 服务的时候让 A 去办就是了。

 	
  2. A 可能在别的地方有工作，并且不准备接受我们的招安，于是我们 Team 可以想这样一种方式解决问题：

 	
    * 我们安排 B 去完成这项任务，然后，让 B 工作的时候可以向 A 请教，因此 B 就是一个复合体，它提供 S 服务，但是他是 B 的继承弟子。





上面生活中问题的解决方式也就正好对应了  Adapter 的两种模式：类模式和对象模式。

实际上，在软件系统设计和开发中，这种问题也会经常遇到：

比如我们为了完成某项工作购买 了一个第三方的库来加快开发。然后发现，这个第三方提供的接口与我们在应用程序中已经设计好的接口不一致，怎么办呢？

为了使得这些接口不兼容的类（不能在一起工作）可以在一起工作，Adapter模式提供了将一个类（第三方库）的接口转化为客户（购买使用者）希望的接口。**什么意思？不通顺吧**


# Adapter 模式介绍


Adapter Pattern（类模式）的结构图为：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10f6949e4c3.png)


Adapter Pattern（对象模式）的结构图为：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10f6c247995.png)


可见：

类模式的 Adapter 采用继承的方式复用 Adaptee 的接口。

对象模式的 Adapter 中我们则用组合的方式实现 Adaptee 的复用。


# 完整代码




## 代码如下


adapter.h：

    
    #ifndef DESIGN_PATTERNS_ADAPTER_H
    #define DESIGN_PATTERNS_ADAPTER_H
    
    #include <string>
    
    class Player {
    public:
      Player() {}
      Player(std::string);
      virtual void Attack() {}
      virtual void Defense() {}
    
    protected:
      std::string name_;
    };
    
    class Forward: public Player {
    public:
      Forward() {}
      Forward(std::string);
      void Attack();
      void Defense();
    };
    
    class Center: public Player {
    public:
      Center() {}
      Center(std::string);
      void Attack();
      void Defense();
    };
    
    class ForeignCenter {
    public:
      ForeignCenter() {}
      ForeignCenter(std::string);
      void Gong();
      void Shou();
    
    private:
      std::string name_;
    };
    
    class Translator: public Player {
    public:
      Translator() {}
      Translator(std::string);
      ~Translator();
      void Attack();
      void Defense();
    
    private:
      ForeignCenter *foreign_center_;
    };
    
    
    #endif //DESIGN_PATTERNS_ADAPTER_H
    


adapter.cpp：

    
    #include "adapter.h"
    #include <iostream>
    
    Player::Player(std::string name): name_(name) {}
    
    Forward::Forward(std::string name): Player(name) {}
    
    void Forward::Attack() {
      std::cout << "forward " << name_ << " attack" << std::endl;
    }
    
    void Forward::Defense() {
      std::cout << "forward " << name_ << " defense" << std::endl;
    }
    
    Center::Center(std::string name): Player(name) {}
    
    void Center::Attack() {
      std::cout << "center " << name_ << " attack" << std::endl;
    }
    
    void Center::Defense() {
      std::cout << "center " << name_ << " defense" << std::endl;
    }
    
    ForeignCenter::ForeignCenter(std::string name): name_(name) {}
    
    void ForeignCenter::Gong() {
      std::cout << "foreign center " << name_ << " attack" << std::endl;
    }
    
    void ForeignCenter::Shou() {
      std::cout << "foreign center " << name_ << " defense" << std::endl;
    }
    
    Translator::Translator(std::string name): Player(name) {
      foreign_center_ = new ForeignCenter(name);
    }
    
    Translator::~Translator() {
      delete foreign_center_;
    }
    
    void Translator::Attack() {
      foreign_center_->Gong();
    }
    
    void Translator::Defense() {
      foreign_center_->Shou();
    }
    
    
    
    


main.cpp：

    
    #include "adapter.h"
    
    
    
    int main() {
    
        Forward *forward_;
        Center *center_;
        Translator *translator_;
    
        forward_ = new Forward("Battier");
        forward_->Attack();
        forward_->Defense();
    
        center_ = new Center("Russell");
        center_->Attack();
        center_->Defense();
    
        translator_ = new Translator("YaoMing");
        translator_->Attack();
        translator_->Defense();
        delete forward_;
        delete center_;
        delete translator_;
    
    
    
        return 0;
    }






♦代码说明

Adapter模式实现上比较简单，要说明的是在类模式Adapter中，我们通过private继承 Adaptee获得实现继承的效果，而通过public继承Target获得接口继承的效果(有关实现继 承和接口继承参见讨论部分)。

■讨论
在Adapter模式的两种模式中，有一个很重要的概念就是接口继承和实现继承的区别和 联系。接口继承和实现继承是面向对象领域的两个重要的概念，接口继承指的是通过继承， 子类获得了父类的接口，而实现继承指的是通过继承子类获得了父类的实现(并不统共接 口)。在C++中的public继承既是接口继承又是实现继承，因为子类在继承了父类后既可以 对外提供父类中的接口操作，又可以获得父类的接口实现。当然我们可以通过一定的方式和 技术模拟单独的接口继承和实现继承，例如我们可以通过 private 继承获得实现继承的效果 (private继承后，父类中的接口都变为private，当然只能是实现继承了。)通过纯抽象基 类模拟接口继承的效果，但是在C++中pure virtual function也可以提供默认实现，因此这是 不纯正的接口继承，但是在Java中我们可以interface来获得真正的接口继承了。















* * *





# COMMENT





 	
  1. 适配器模式：当系统数据和行为都一致，只有接口不符合时，将一个类的接口转化为客户端期望的另一个接口。

 	
  2. 适配器模式用于服用一些现存的类，常用在第三方接口或软件开发后期双方都不易修改的时候。

 	
  3. 在.Net中DataAdapter是用于DataSet和数据源间的适配器，Fill更改DataSet适配数据源，Update更改数据源适配DataSet。


