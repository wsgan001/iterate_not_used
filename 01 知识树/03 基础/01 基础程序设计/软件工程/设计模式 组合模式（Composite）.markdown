---
author: evo
comments: true
date: 2018-05-31 23:42:43+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%bb%84%e5%90%88%e6%a8%a1%e5%bc%8f%ef%bc%88composite%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%bb%84%e5%90%88%e6%a8%a1%e5%bc%8f%ef%bc%88composite%ef%bc%89'
title: 设计模式 组合模式（Composite）
wordpress_id: 7172
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





# 组合模式（Composite）






在开发中，我们经常可能要递归构建树状的组合结构，Composite模式则提供了很好的 解决方案。



■模式选择
Composite模式的典型结构图为：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10fc9a95c15.png)


-实现
♦完整代码示例(code)

composite.h：

    
    #ifndef DESIGN_PATTERNS_COMPOSITE_H
    #define DESIGN_PATTERNS_COMPOSITE_H
    
    #include <string>
    #include <vector>
    
    class Company {
    public:
      Company() {}
      Company(std::string);
      virtual void Add(Company*) = 0;
      virtual void Display(int) = 0;
      virtual void LineOfDuty() = 0;
    
    protected:
      std::string name_;
    };
    
    class HrDepartment: public Company {
    public:
      HrDepartment() {}
      HrDepartment(std::string);
      void Add(Company*) {}
      void Display(int);
      void LineOfDuty();
    };
    
    class FinanceDepartment: public Company {
    public:
      FinanceDepartment() {}
      FinanceDepartment(std::string);
      void Add(Company*) {}
      void Display(int);
      void LineOfDuty();
    };
    
    class ConcreteCompany: public Company {
    public:
      ConcreteCompany() {}
      ConcreteCompany(std::string);
      void Add(Company*);
      void Display(int);
      void LineOfDuty();
    
    private:
      std::vector <Company*> companies_;
    };
    
    
    #endif //DESIGN_PATTERNS_COMPOSITE_H
    


composite.cpp：

    
    #include "composite.h"
    #include <iostream>
    
    Company::Company(std::string name): name_(name) {}
    
    HrDepartment::HrDepartment(std::string name): Company(name) {}
    
    void HrDepartment::Display(int depth) {
      for(int i = 0; i < depth; ++i)
        std::cout << "--";
      std::cout << name_  << std::endl;
    }
    
    void HrDepartment::LineOfDuty() {
      std::cout << name_ << " : human resources" << std::endl;
    }
    
    FinanceDepartment::FinanceDepartment(std::string name): Company(name) {}
    
    void FinanceDepartment::Display(int depth) {
      for(int i = 0; i < depth; ++i)
        std::cout << "--";
      std::cout << name_  << std::endl;
    }
    
    void FinanceDepartment::LineOfDuty() {
      std::cout << name_ << " : finance analysis" << std::endl;
    }
    
    ConcreteCompany::ConcreteCompany(std::string name): Company(name) {}
    
    void ConcreteCompany::Add(Company *company) {
      companies_.push_back(company);
    }
    
    void ConcreteCompany::Display(int depth) {
      for(int i = 0; i < depth; ++i)
        std::cout << "--";
      std::cout << name_  << std::endl;
      for(std::vector <Company*> ::iterator it = companies_.begin(); it != companies_.end(); ++it) {
        (*it)->Display(depth + 1);
      }
    }
    
    void ConcreteCompany::LineOfDuty() {
      for(std::vector <Company*> ::iterator it = companies_.begin(); it != companies_.end(); ++it) {
        (*it)->LineOfDuty();
      }
    }


main.cpp：

    
    #include "composite.h"
    #include <iostream>
    
    
    int main() {
        ConcreteCompany *beijing_head_office_;
        ConcreteCompany *huadong_branch_office_;
        ConcreteCompany *nanjing_office_;
        ConcreteCompany *hangzhou_office_;
        beijing_head_office_ = new ConcreteCompany("Beijing Head Office");
        beijing_head_office_->Add(new HrDepartment("Beijing HR Department"));
        beijing_head_office_->Add(new FinanceDepartment("Beijing Finance Department"));
    
        huadong_branch_office_ = new ConcreteCompany("Huadong Branch Office");
        huadong_branch_office_->Add(new HrDepartment("Huadong HR Department"));
        huadong_branch_office_->Add(new FinanceDepartment("Huadong Finance Department"));
        beijing_head_office_->Add(huadong_branch_office_);
    
        nanjing_office_ = new ConcreteCompany("Nangjing Office");
        nanjing_office_->Add(new HrDepartment("Nanjing HR Department"));
        nanjing_office_->Add(new FinanceDepartment("Nanjing Finance Department"));
        huadong_branch_office_->Add(nanjing_office_);
    
        hangzhou_office_ = new ConcreteCompany("Nangjing Office");
        hangzhou_office_->Add(new HrDepartment("Hangzhou HR Department"));
        hangzhou_office_->Add(new FinanceDepartment("Hangzhou Finance Department"));
        huadong_branch_office_->Add(hangzhou_office_);
    
        std::cout << "Structure Tree:" << std::endl;
        beijing_head_office_->Display(0);
    
        std::cout << "Duty Lines:" << std::endl;
        beijing_head_office_->LineOfDuty();
        delete beijing_head_office_;
        delete huadong_branch_office_;
        delete nanjing_office_;
        delete hangzhou_office_;
    
        return 0;
    }


♦代码说明

Composite模式在实现中有一个问题就是要提供对于子节点（Leaf）的管理策略，这里 使用的是STL中的vector，可以提供其他的实现方式，如数组、链表、Hash表等。

■讨论
Composite模式通过和Decorator模式有着类似的结构图，但是Composite模式旨在构造 类，而Decorator模式重在不生成子类即可给对象添加职责。Decorator模式重在修饰，而 Composite模式重在表示。













* * *





# COMMENT





 	
  1. 组合模式：将对象的组合以树形的层次结构表示，对单个对象和组合结构的操作具有一致性。

 	
  2. 透明方法：叶子和分枝对外接口无差别；安全方法：分枝具有添加删除叶子的接口，低层抽象接口和叶子没有。

 	
  3. 基本对象组合成组合，组合又可以被组合，不断递归下去，在任何用到基本对象的地方都可以使用组合对象。



