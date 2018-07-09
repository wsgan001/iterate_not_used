---
author: evo
comments: true
date: 2018-05-31 23:40:57+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%a1%a5%e6%8e%a5%e6%a8%a1%e5%bc%8f%ef%bc%88bridge%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%a1%a5%e6%8e%a5%e6%a8%a1%e5%bc%8f%ef%bc%88bridge%ef%bc%89'
title: 设计模式 桥接模式（Bridge）
wordpress_id: 7170
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

 	
  3. [一天学习一个设计模式之桥接模式](https://www.cnblogs.com/gousheng107/p/8005692.html)

 	
  4. 《大话设计模式》作者 程杰




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa







# 桥接模式（Bridge）是用来解决什么问题的？


面向对象系统设计的时候很注意：



 	
  * 尽可能地提高系统模块内部的内聚（Cohesion）

 	
  * 尽可能降低模块间的耦合（Coupling）。


但是这两点还是很难把握的，比如，我们经常会：

 	
  1. 客户给了你一个需求，于是使用一个类来实现 A ;

 	
  2. OK，现在客户需求变化了，想要添加一个算法，于是我们改变设计，抽象出一个基类 A0，然后再定义两个具体类 A01 和 A02 来实现不同的算法;

 	
  3. OK，现在客户又说，要可以对应不同的操作系统，于是我们再抽象一个层次，作为一个抽象基类 A，再分别为每个操作系统派生具体类 A0和 A1 来实现不同的操作系统上的客户需求，然后再 A0、A1 对应的操作系统上再分别实现算法 A00、A01、A10、A11。

 	
  4. OK，现在客户又要添加一种新的算法 ........

 	
  5. 这样我们陷入了一个需求变化的郁闷当中，也因此带来了类的迅速膨胀。


Bridge模式则正是解决了这类问题。**是这样吗？怎么解决的？**


# Bridge 模式介绍


Bridge 模式的结构图为：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10e8dfcd49b.png)


从结构图我们可以看到，系统被分为两个相对独立的部分，左边是抽象部分，右边是实现部分，这两个部分可以互相独立地进行修改。

例如，在上面的问题中：**没明白这一段？**



 	
  * 当客户的需求变化的时候，比如用户需要从 Abstraction 派生一个具体子类时候，并不需要像上面通过继承的方式实现的时候需要添加子类 A01 和 A02 了。

 	
  * 另外当算法添加的时候也只需要改变右边的实现，即添加一个具体化子类，而左边不用在变化，也不用添加具体子类了。




# 完整代码


比如说，我现在想开发一些手机软件，我们可以按手机品牌来实现：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10f1439979f.png)


也可以按软件分类来实现：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10f18c683f5.png)


由于实现的方式有多种，桥接模式的核心意图就是把这些实现独立出来，让它们各自地变化。这就可以使每种实现的变化不会影响其他实现，从而达到应对变化的目的：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10f1b133b1e.png)





## 代码如下


bridge.h：

    
    #ifndef DESIGN_PATTERNS_BRIDGE_H
    #define DESIGN_PATTERNS_BRIDGE_H
    
    
    //手机软件
    class HandsetSoft {
    public:
      virtual void run() {}
    };
    //手机游戏
    class HandsetGame: public HandsetSoft {
    public:
      void run();
    };
    //手机通讯录
    class HandsetAddressList: public HandsetSoft {
    public:
      void run();
    };
    
    
    
    //手机牌子
    class HandsetBrand {
    public:
      HandsetBrand() {}
      HandsetBrand(HandsetSoft *);
      virtual ~HandsetBrand() {}
      virtual void run() {}
    protected:
      HandsetSoft *handset_soft_;
    };
    class HandsetBrandM: public HandsetBrand {
    public:
      HandsetBrandM() {}
      HandsetBrandM(HandsetSoft *);
      ~HandsetBrandM();
      //这里虽然是调用的手机软件的run，但是这个环境是M牌手机的，
      //也就是说，这个类给这个游戏提供了一个运行的环境。
      //这样，游戏就不用考虑它是用在什么环境下的，对于环境的支持在这个地方实现了
      void run();
    };
    class HandsetBrandN: public HandsetBrand {
    public:
      HandsetBrandN() {}
      HandsetBrandN(HandsetSoft *);
      ~HandsetBrandN();
      void run();
    };
    
    
    #endif //DESIGN_PATTERNS_BRIDGE_H
    


bridge.cpp：

    
    #include "bridge.h"
    #include <iostream>
    
    void HandsetGame::run() {
      std::cout << "run game" << std::endl;
    }
    void HandsetAddressList::run() {
      std::cout << "run address list" << std::endl;
    }
    
    
    
    
    HandsetBrand::HandsetBrand(HandsetSoft *handset_soft): handset_soft_(handset_soft) {}
    
    HandsetBrandM::HandsetBrandM(HandsetSoft *handset_soft): HandsetBrand(handset_soft) {}
    HandsetBrandM::~HandsetBrandM() {
      delete handset_soft_;
    }
    void HandsetBrandM::run() {
      std::cout << "handset brand M: ";
      handset_soft_->run();
    }
    
    HandsetBrandN::HandsetBrandN(HandsetSoft *handset_soft): HandsetBrand(handset_soft) {}
    HandsetBrandN::~HandsetBrandN() {
      delete handset_soft_;
    }
    void HandsetBrandN::run() {
      std::cout << "handset brand N: ";
      handset_soft_->run();
    }


main.cpp：

    
    #include "bridge.h"
    
    
    
    //可见，当开发一个新的手机软件的时候，也不用考虑它是用在哪种手机上的，因为环境已经都在Brand里了。
    //当想要支持新的手机的时候，也只需要提供一个这个手机的环境，这时手机软件都可以移植过来了。
    //可见，这样就吧手机软件和软件运行的环境区分开了
    int main() {
        HandsetBrand *handset_brand_;
    
        //给牌子M开发的两个软件
        handset_brand_ = new HandsetBrandM(new HandsetGame);
        handset_brand_->run();
        handset_brand_ = new HandsetBrandM(new HandsetAddressList);
        handset_brand_->run();
    
        //给牌子N开发的两个软件
        handset_brand_ = new HandsetBrandN(new HandsetGame);
        handset_brand_->run();
        handset_brand_ = new HandsetBrandN(new HandsetAddressList);
        handset_brand_->run();
    
    
        delete handset_brand_;
    
    
    
        return 0;
    }


可见：



 	
  * 当开发一个新的手机软件的时候，也不用考虑它是用在哪种手机上的，因为环境已经都在 Brand 里了。

 	
  * 当想要支持新的手机的时候，也只需要提供一个这个手机的环境，这时手机软件都可以移植过来了。


这样就吧手机软件和软件运行的环境区分开了




# Bridge 桥接模式讨论


桥接是一个接口，它与一方应该是绑定的，也就是解耦的双方中的一方必然是继承这个接口的，这一方就是实现方，而另一方正是要与这一方解耦的抽象方。

如果不采用桥接模式，一般我们的处理方式是直接使用继承来实现，这样双方之间处于强链接，类之间关联性极强，如要进行扩展，必然导致类结构急剧膨胀。

采用桥接模式，正是为了避免这一情况的发生，将一方与桥绑定，即实现桥接口，另一方在抽象类中调用桥接口（指向的实现类），这样桥方可以通过实现桥接口进行单方面扩展，而另一方可以继承抽象类而单方面扩展，而之间的调用就从桥接口来作为突破口，不会受到双方扩展的任何影响。**厉害的。**















* * *





# COMMENT





 	
  1. 对象的继承关系编译时已确定，所以无法在运行时修改从父类继承的实现。由于紧耦合，父类中任何的改变必然会导致子类发生变化。当需要复用子类，但继承下来的方法不合适时，必须重写父类或用其他类替代。这种依赖性限制了灵活性和复用性。

 	
  2. 合成／聚合复用原则：尽量使用合成和聚合而不是继承。可以保证每个类封装集中在单个任务上，不会出现规模太大的类及继承结构。

 	
  3. 桥接模式：抽象类和其派生类分离，各自实现自己的对象。若系统可以从多角度分类，且每种分类都可能变化，则把多角度分离独立出来，降低耦合。


