---
author: evo
comments: true
date: 2018-05-31 10:20:24+00:00
layout: post
link: http://106.15.37.116/2018/05/31/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%bb%ba%e9%80%a0%e8%80%85%e6%a8%a1%e5%bc%8f%ef%bc%88builder%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%bb%ba%e9%80%a0%e8%80%85%e6%a8%a1%e5%bc%8f%ef%bc%88builder%ef%bc%89'
title: 设计模式 建造者模式（Builder）
wordpress_id: 7148
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




  1. 建造者模式：将复杂对象的创建与表示分开，使得相同的创建过程可以有不同的表示。用户只需制定需要建造的类型，不需要知道建造的过程和细节。


  2. 指挥者是建造者模式中重要的类，用于控制建造过程，也可以隔离用户与建造过程的关联。


  3. 建造者隐藏了产品的组装细节，若需要改变一个产品的内部表示，可以再定义一个具体的建造者。


  4. 建造者模式是在当前创造复杂对象的算法，独立于该对象的组成部分和装配方式时适用的模式。





# Builder 模式是什么样子的？


生活中有着很多的Builder的例子，比如说大学生活就是一个 Builder 模式的最好体验：

一般将大学教育过程分成 4 个学期进行，每个学期可以看作是作为一个完整大学教育的一部分的构建过程，每个人在经过这4个阶段的构建之后得到的结果是不一样的，因为四个阶段的构建中可能会引入很多的参数，比如机会、际遇等。


# Builder 模式是用来解决什么问题的？


当我们要创建的对象很复杂的时候（通常是由很多其他的对象组合而成），我们要把复杂对象的创建过程和这个对象的表示（展示）分离开来，这样做的好处就是通过一步步的进行复杂对象的构建，由于在每一步的构造过程中可以引入参数，使得经过相同的步骤创建最后得到的对象的展示不一样。**还是没明白，这一段讲的是什么？**

这就是 Builder 模式。


# Builder 模式介绍


Builder模式结构图为：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/HEFLeGjlJb.png?imageslim)

它的关键是其中的 Director 对象并不直接返回对象，而是通过Builder 一步步 （BuildPartA，BuildPartB，BuildPartC）的来创建对象。当然这里 Director 可以提供一个默认的返回对象的接口（即返回通用的复杂对象的创建，即不指定或者特定唯一指定BuildPart中的参数）。**什么意思？**




# 完整代码




## 代码如下


builder.h：


    #ifndef DESIGN_PATTERNS_BUILDER_H
    #define DESIGN_PATTERNS_BUILDER_H

    class Pen {
    };
    class Graphics {
    };





    class PersonBuilder {
    public:
      PersonBuilder() {
      };
      PersonBuilder(Pen*, Graphics*);
      virtual ~PersonBuilder() {
      };

      virtual void BuildHead() {
      };
      virtual void BuildBody() {
      };

    protected:
      Pen* pen_;
      Graphics* graphics_;
    };

    //两个不同的构建者
    class PersonThinBuilder: public PersonBuilder {
    public:
      PersonThinBuilder(Pen*, Graphics*);
      void BuildHead();
      void BuildBody();
    };
    class PersonFatBuilder: public PersonBuilder {
    public:
      PersonFatBuilder(Pen*, Graphics*);
      void BuildHead();
      void BuildBody();
    };


    class PersonDirector {
    public:
        PersonDirector(PersonBuilder*);
        void CreatePerson();
    private:
        PersonBuilder* person_builder_;
    };


    #endif //DESIGN_PATTERNS_BUILDER_H



builder.cpp：


    #include "builder.h"
    #include <iostream>


    PersonBuilder::PersonBuilder(Pen* pen, Graphics* graphics): pen_(pen), graphics_(graphics){
    }


    PersonThinBuilder::PersonThinBuilder(Pen* pen, Graphics* graphics): PersonBuilder(pen, graphics) {

    }
    void PersonThinBuilder::BuildHead() {
      std::cout<< "Build Thin Head"<<std::endl;
    }
    void PersonThinBuilder::BuildBody() {
      std::cout<< "Build Thin Body" << std::endl;
    }


    PersonFatBuilder::PersonFatBuilder(Pen* pen, Graphics* graphics): PersonBuilder(pen, graphics) {

    }
    void PersonFatBuilder::BuildHead() {
      std::cout<< "Build Fat Head" << std::endl;
    }
    void PersonFatBuilder::BuildBody() {
      std::cout<< "Build Fat Body" << std::endl;
    }


    //这个 Director 并不关心传进来的是胖构建者还是瘦构建者
    //它只知道这个传进来的是一个构建者就可以
    PersonDirector::PersonDirector(PersonBuilder* person_builder) : person_builder_(person_builder) {

    }
    void PersonDirector::CreatePerson() {
        person_builder_->BuildBody();
        person_builder_->BuildHead();
    }



main.cpp：


    #include "builder.h"


    //对于每一个 胖构建者和瘦构建者来说，它里面就可以放一些特殊的参数，但是它们作为构建者的本质没有变，
    //相当于 是不同的面包模板，可以生产不同的面包，但是面包的形状是存放在不同的面包模板里面的。
    int main() {

        Pen *pPen;
        Graphics *pGraphics;
        PersonBuilder* pPersonBuilder;
        PersonDirector* pPersonDirector;

        pPen = new Pen;
        pGraphics = new Graphics;

        // 创建一个瘦人
        pPersonBuilder = new PersonThinBuilder(pPen, pGraphics);
        pPersonDirector = new PersonDirector(pPersonBuilder);
        pPersonDirector->CreatePerson();

        // 创建一个胖人
        // 只需要把 PersonThinBuilder 替换成 PersonFatBuilder 即可
        pPersonBuilder = new PersonFatBuilder(pPen, pGraphics);
        pPersonDirector = new PersonDirector(pPersonBuilder);
        pPersonDirector->CreatePerson();

        delete pPen;
        delete pGraphics;
        delete pPersonBuilder;
        delete pPersonDirector;


        return 0;
    }




## 代码说明


对于每一个 胖构建者和瘦构建者来说，它里面就可以放一些特殊的参数，但是它们作为构建者的本质没有变。这样就可以得到不同的细微差别的复杂对象了。

相当于 是不同的面包模板，可以生产不同的面包，但是面包的形状是存放在不同的面包模板里面的。

**感觉这个例子不是特别贴切，找找有没有更贴切的例子。而且，builder 一定要这样用吗？有没有什么别的形式？**




# Builder 模式的优缺点


Builder 模式起到了 将一个复杂对象的构建与它的表示分离的作用，而且使得同样的构建过程可以创建不同的表示。**对象的构建与表示分离，到底是什么意思？**

Builder 模式与 AbstractFactory 有什么关联与区别吗？

Builder 模式和 AbstractFactory 模式在功能上是很相似的，因为都是用来创建大的复杂的对象的，它们的区别在于：




  * Builder 模式强调的是一步步创建对象，并通过相同的创建过程可以获得不同的结果对象，一般来说Builder 模式中对象不是直接返回的。


  * 而在 AbstractFactory 模式中对象是直接返回的，AbstractFactory 模式强调的是为创建多个相互依赖的对象提供一个同一的接口。


嗯，这个的确是区别的地方。









* * *





# COMMENT
