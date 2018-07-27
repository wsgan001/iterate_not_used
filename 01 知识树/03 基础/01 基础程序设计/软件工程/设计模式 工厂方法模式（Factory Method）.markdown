---
author: evo
comments: true
date: 2018-05-31 10:08:19+00:00
layout: post
link: http://106.15.37.116/2018/05/31/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%b7%a5%e5%8e%82%e6%96%b9%e6%b3%95%e6%a8%a1%e5%bc%8f%ef%bc%88factory-method%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%b7%a5%e5%8e%82%e6%96%b9%e6%b3%95%e6%a8%a1%e5%bc%8f%ef%bc%88factory-method%ef%bc%89'
title: 设计模式 工厂方法模式（Factory Method）
wordpress_id: 7140
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






  * **很多地方都没明白，话需要一些例子，**


  * **而且，到底具体是在什么样的场景下使用？还是没有很清楚。**





* * *





# INTRODUCTION






  * aaa





  1.




# 工厂方法模式是用来解决什么问题的？


主要是用来解决下面这两种问题的：


## 把类的公共接口抽象成基类和接口的时候出现的问题


为了提高程序的内聚（Cohesion）性，和降低程序的耦合（Coupling）的程度，我们经常会抽象出一些类的公共接口以形成抽象基类或者接口。**一般****到底用的是抽象基类还是接口比较好？** 这样我们就可以通过声明一个指向基类的指针来指向实际的子类实现，也就是所谓的多态。

这个本身还是很有用的，比如做一些界面程序的时候，从一个通用界面继承出几个界面。

但是，这里很容易出现一个问题：

由于很多的子类继承自抽象基类，我们不得不在每次要用到某个子类的时候就编写如 new XXX; 这样的代码。这实际上带来了两个问题：




  1. 别人使用你的程序的接口的时候，它必须知道实际的子类的名称，而当系统复杂后，这些子类的命名是比较麻烦的，有时候为了处理可能的名字冲突，命名可能是很奇怪的。**难道使用工厂模式就不用知道实际的子类的名字了吗？**


  2. 程序的扩展性和维护变得越来越困难。**为什么这么说？好像是这么回事，不过也要确认下，把普通的写出来与工厂模式对比下，工厂模式扩展和维护很方便吗？**


**嗯，这个的确是编程中经常遇到的问题，所以对基类和接口的时候还是很抵触的。到底有什么好的方法吗？感觉 Effect C++ 要好好总结下。**


## 父类中并不知道具体要实例化哪一个具体的子类


还有一种情况就是在父类中并不知道具体要实例化哪一个具体的子类。

这里的意思是：

假设我们在类 A 中要使用到类 B，B 是一个抽象基类，在 A 中并不知道具体要实例化 B 的那一个子类，但是在类 A 的子类 D 中是可以知道的。在 A 中我们没有办法直接使用类似于 new XXX 的语句，因为根本就不知道 XXX 是什么。

**什么意思？没有很明白，要写一下。**

我们通常使用 Factory 模式来解决上面给出的两个问题。


# 工厂方法模式的功能


上面的两个问题就引出了 Factory 模式的两个最重要的功能：




  1. 定义创建对象的接口，封装了对象的创建。**什么叫封装了对象的创建？**


  2. 使得具体化类的工作延迟到了子类中。**为什么？怎么做到的？**





# 到底怎么解决的？




## 对于第一个问题


我们经常就是声明一个创建对象的接口，并封装了对象的创建过程。Factory 在这里就类似于一个真正意义上的工厂（生产对象）。**封装了对象的创建过程？什么意思？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/22dmKLFJ59.png?imageslim)

上图所示的 Factory 模式经常在系统开发中用到，但是这个

上面这个并不是 Factory 模式的最大威力所在（因为这可以通过其他方式解决这个问题）。它更重要的是延迟了子类的实例化（第二个问题）：


## 对于第二个问题


我们不仅提供了一个对象创建对象的接口，还在子类中提供其具体实现（因为只有在子类中可以决定到底实例化哪一个类）。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/3aKKICLm1h.png?imageslim)

如上图，Factory 中只是提供了对象创建的接口，对象创建的实现被放在 Factory 的子类  ConcreteFactory 中进行。这个与之前的图是不同的。




# 完整代码




## 代码如下


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/G8K11d6fiK.png?imageslim)


factory_method.h：


    #ifndef DESIGN_PATTERNS_FACTORY_METHOD_H
    #define DESIGN_PATTERNS_FACTORY_METHOD_H

    //基类
    class Worker {
    public:
      virtual void Wash();
      virtual void Sweep();
      virtual void BuyRice();
    };


    //两个子类
    class TemporaryWorker: public Worker {

    };
    class FormalWorker: public Worker {

    };



    //工厂 为什么工厂的命名要以 I 为开头？
    class IFactory {
    public:
      IFactory() {
      };
      virtual ~IFactory() {//虚析构函数的原因是什么？确认下
      };

      virtual Worker* CreateWorker() = 0;//这个是什么意思？忘记了
    };



    //两个继承的工厂，实现了对上面两个Product 子类的实例化
    class TemporaryWorkerFactory: public IFactory {
    public:
      Worker* CreateWorker();
    };
    class FormalWorkerFactory: public IFactory {
    public:
      Worker* CreateWorker();
    };

    #endif //DESIGN_PATTERNS_FACTORY_METHOD_H


factory_method.cc：


    #include "factory_method.h"
    #include <iostream>

    void Worker::Wash() {
      std::cout << "Wash" << std::endl;
    }
    void Worker::Sweep() {
      std::cout << "Sweep" << std::endl;
    }
    void Worker::BuyRice() {
      std::cout << "BuyRice" << std::endl;
    }



    Worker* TemporaryWorkerFactory::CreateWorker() {
      Worker* worker = new TemporaryWorker();
      return worker;
    }
    Worker* FormalWorkerFactory::CreateWorker() {
      Worker* worker = new FormalWorker();
      return worker;
    }


main.cpp：


    #include "factory_method.h"


    int main() {

        IFactory* pIFactory;
        Worker* pWorker;

        //产生一个临时工来做事情
        pIFactory = new TemporaryWorkerFactory();
        pWorker = pIFactory->CreateWorker();
        pWorker->Wash();

        // 产生一个正式工来做事情
        pIFactory = new FormalWorkerFactory();
        pWorker = pIFactory->CreateWorker();
        pWorker->Wash();

        delete pIFactory;
        delete pWorker;

        return 0;
    }


**还是没明白为什么要这么做？**


## 代码说明


示例代码中给出的是 Factory 模式解决父类中并不知道具体要实例化哪一个具体的子类 的问题，至于为创建对象提供接口问题，可以由 Factory 中附加相应的创建操作例如 Create***Product()  即可。




# Factory 模式的优劣




## Factory 模式的优点


Factory 模式在实际开发中应用非常广泛。

面向对象的系统经常面临着对象创建问题: 要创建的类实在是太多了。而 Factory 提供的创建对象的接口封装（第一个功能），以及其将类的实例化推迟到子类（第二个功能）都部分地解决了实际问题。**还是没明白？**

一个简单的例子就是语义分析过程中，由于要为文法中的每个非终结符构造一个类处理，因此这个过程中对象的创建非常多，采用 Factory 模式后系统可读性性和维护都变得优雅了许多。


## Factory 模式存在的问题


Factory模式也带来至少以下两个问题：




  1. 如果为每一个具体的 ConcreteProduct 类的实例化提供一个函数体，那么我们可能不得不在系统中添加了一个方法来处理这个新建的 ConcreteProduct ，这样 Factory 的接口永远就不可能封闭（Close）。当然我们可以通过创建一个 Factory 的子类来通过多态实现这一点， 但是这也是以新建一个类作为代价的。**没明白**


  2. 在实现中我们可以通过参数化工厂方法，即给 FactoryMethod() 传递一个参数用以决定是创建具体哪一个具体的Product 。当然也可以通过模板化避免 1 中的子类创建子类，其方法就是将具体Product 类作为模板参数，实现起来也很简单。**没明白**


可以看出，Factory模式对于对象的创建给予开发人员提供了很好的实现策略，但是 Factory 模式仅仅局限于一类类（就是说 Product 是一类，有一个共同的基类），如果我们要为不同类的类提供一个对象创建的接口，那就要用 AbstractFactory 了。









* * *





# COMMENT






  1. 工厂方法模式：定义一个用于创建对象的接口，让子类决定实例化哪个类。


  2. 工厂方法把简单工厂的内部判断逻辑移到了客户端代码，本来需要修改工厂类，现在是修改客户端。


  3. 简单工厂模式违背了开放-封闭原则，工厂方法模式借助多态，克服了该缺点，却保持了封装对象创建过程的优点。
