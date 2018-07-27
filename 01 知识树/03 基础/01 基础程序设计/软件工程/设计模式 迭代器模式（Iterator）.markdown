---
author: evo
comments: true
date: 2018-05-31 23:46:07+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%bf%ad%e4%bb%a3%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88iterator%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%bf%ad%e4%bb%a3%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88iterator%ef%bc%89'
title: 设计模式 迭代器模式（Iterator）
wordpress_id: 7183
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





# 迭代器模式（Iterator）






  1. 迭代器模式：提供一种方法顺序遍历一个聚集对象，为不同的聚集结构提供遍历所需接口，而不暴露对象内部的表示。


  2. 在高级编程语言如c#、c++、java等，都已经把迭代器模式设计进语言的一部分。


  3. 迭代器模式分离了对象的遍历行为，既不暴露内部结构又可以让外部代码透明的访问集合内部的数据。








3.10 Iterator 模式
-问题
Iterator 模式应该是最为熟悉的模式了，最简单的证明就是我在实现 Composite 模式、 Flyweight模式、Observer模式中就直接用到了 STL提供的Iterator来遍历Vector或者List

数据结构。

Iterator 模式也正是用来解决对一个聚合对象的遍历问题，将对聚合的遍历封装到一个

类中进行，这样就避免了暴露这个聚合对象的内部表示的可能。

■模式选择
Iterator模式典型的结构图为:


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/jK5G7k7ic1.png?imageslim)

图 2-1： Iterator Pattern 结构图

Iterator 模式中定义的对外接口可以视客户成员的便捷定义，但是基本的接口在图中的 Iterator中已经给出了（参考STL的Iterator就知道了）。

-实现
♦完整代码示例（code）

iterator.h


    #ifndef DESIGN_PATTERNS_AGGREGATE_H
    #define DESIGN_PATTERNS_AGGREGATE_H

    #include <vector>

    class Iterator;

    class Aggregate {
    public:
      virtual ~Aggregate() {}
      virtual Iterator* CreateIterator() = 0;
    };

    class List: public Aggregate {
    public:
      Iterator* CreateIterator();
      int Count();
      int operator[] (int) const;
      void Insert(int);

    private:
      std::vector <int> items_;
    };

    class Iterator {
    public:
      virtual int First() = 0;
      virtual int Next() = 0;
      virtual bool IsDone() = 0;
      virtual int CurrentItem() = 0;
    };

    class ListIterator: public Iterator {
    public:
      ListIterator() {}
      ListIterator(List*);
      int First();
      int Next();
      bool IsDone();
      int CurrentItem();

    private:
      int current_;
      List *aggregate_;
    };


    #endif //DESIGN_PATTERNS_AGGREGATE_H



iterator.cpp


    #include "iterator_.h"

    Iterator* List::CreateIterator() {
      return new ListIterator(this);
    }

    int List::Count() {
      return (int)items_.size();
    }

    int List::operator[] (int index) const {
      return items_[index];
    }

    void List::Insert(int value) {
      items_.push_back(value);
    }

    ListIterator::ListIterator(List *aggregate): aggregate_(aggregate), current_(0) {}

    int ListIterator::First() {
      return (*aggregate_)[0];
    }

    int ListIterator::Next() {
      int next = -1;
      if(++current_ < aggregate_->Count())
        next =  (*aggregate_)[current_];
      return next;
    }

    bool ListIterator::IsDone() {
      return current_ >= aggregate_->Count();
    }

    int ListIterator::CurrentItem() {
      return (*aggregate_)[current_];
    }



main.cpp


    #include "iterator_.h"
    #include <iostream>


    int main() {
        List *list_;
        Iterator *list_iterator_;
        list_ = new List();
        list_->Insert(1);
        list_->Insert(2);
        list_->Insert(3);
        list_iterator_ = list_->CreateIterator();
        std::cout << list_iterator_->CurrentItem() << std::endl;
        std::cout << list_iterator_->First() << std::endl;
        std::cout << list_iterator_->Next() << std::endl;
        std::cout << list_iterator_->IsDone() << std::endl;
        std::cout << list_iterator_->Next() << std::endl;
        std::cout << list_iterator_->Next() << std::endl;
        std::cout << list_iterator_->IsDone() << std::endl;
        delete list_;
        delete list_iterator_;



        return 0;
    }


♦代码说明

Iterator模式的实现代码很简单，实际上为了更好地保护Aggregate的状态，我们可以尽

量减小Aggregate的public接口，而通过将Iterator对象声明位Aggregate的友元来给予Iterator 一些特权，获得访问Aggregate私有数据和方法的机会。

■讨论
Iterator模式的应用很常见，我们在开发中就经常会用到STL中预定义好的Iterator来对 STL类进行遍历（Vector、Set等）。













* * *





# COMMENT
