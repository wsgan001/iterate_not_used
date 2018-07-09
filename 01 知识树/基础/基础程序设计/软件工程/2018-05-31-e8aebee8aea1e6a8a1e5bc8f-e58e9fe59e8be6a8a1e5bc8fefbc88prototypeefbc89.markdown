---
author: evo
comments: true
date: 2018-05-31 23:34:06+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%8e%9f%e5%9e%8b%e6%a8%a1%e5%bc%8f%ef%bc%88prototype%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%8e%9f%e5%9e%8b%e6%a8%a1%e5%bc%8f%ef%bc%88prototype%ef%bc%89'
title: 设计模式 原型模式（Prototype）
wordpress_id: 7158
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







# 原型模式 （Prototype）是用来解决什么问题的？


在西游记中，孙悟空在发飙的时候可以复制出来成千上万的孙悟空，来打小怪。

这正是 Prototype 做的事情，它提供了自我复制的功能，也就是说，新对象的创建可以通过已有的对象来进行创建。

在 C++ 中拷贝构造函数 (Copy Constructor) 曾经是很对程序员的噩梦，浅层拷贝和深层拷贝的魔魇也是很多程序员在面试时候的快餐和系统崩溃时候的根源之一。**哈哈，是的。各种数据格式的 copy、deepcopy 到底支不支持，以及数据格式之间嵌套的时候对于拷贝的支持情况。等等。**

**但是单从上面这个解释我还是没看出这个 Prototype 到底与这个拷贝有什么区别？好像也没有解决什么特殊的问题。**




# Prototype 模式介绍


Prototype模式的结构图为：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10e3c20409f.png)


Prototype 模式提供了一个通过已存在对象进行新对象创建的接口 Clone() ，Clone () 的实现和具体的语言相关，在 C++ 中我们将通过拷贝构造函数来进行实现。

**好吧，那么还是拷贝功能而已吧？确认下。**


# 完整代码




## 代码如下


prototypr.h：

    
    #ifndef DESIGN_PATTERNS_PROTOTYPE_H
    #define DESIGN_PATTERNS_PROTOTYPE_H
    
    #include <string>
    
    
    //看起来好像与普通的拷贝功能没有什么区别吧？确认下
    class WorkExperience {
    public:
      void SetCompany(std::string);
      void SetTimeArea(std::string);
      std::string GetCompany();
      std::string GetTimeArea();
      WorkExperience* Clone();
    
    private:
      std::string company_;
      std::string time_area_;
    };
    
    class Resume {
    public:
      Resume() {};
      Resume(std::string);
      ~Resume();
      void SetPersonalInfo(std::string, std::string);
      void SetWorkExperience(std::string, std::string);
      Resume* Clone();
      void PrintResume();
    private:
      std::string name_;
      std::string sex_;
      std::string age_;
      WorkExperience *work_experience_;
    };
    
    
    #endif //DESIGN_PATTERNS_PROTOTYPE_H
    


prototype.cpp：

    
    #include "prototype.h"
    #include <iostream>
    
    
    void WorkExperience::SetCompany(std::string company) {
      company_ = company;
    }
    void WorkExperience::SetTimeArea(std::string time_area) {
      time_area_ = time_area;
    }
    std::string WorkExperience::GetCompany() {
      return company_;
    }
    std::string WorkExperience::GetTimeArea() {
      return time_area_;
    }
    WorkExperience* WorkExperience::Clone() {
      WorkExperience* new_work_experience = new WorkExperience();
      new_work_experience->SetCompany(company_);
      new_work_experience->SetTimeArea(time_area_);
      return new_work_experience;
    }
    
    
    Resume::Resume(std::string name) {
      name_ = name;
      work_experience_ = new WorkExperience();
    }
    Resume::~Resume() {
      delete work_experience_;
    }
    void Resume::SetPersonalInfo(std::string sex, std::string age){
      sex_ = sex;
      age_ = age;
    }
    void Resume::SetWorkExperience(std::string company, std::string time_area) {
      work_experience_->SetCompany(company);
      work_experience_->SetTimeArea(time_area);
    }
    Resume* Resume::Clone() {
      Resume* new_resume = new Resume(name_);
      new_resume->SetPersonalInfo(sex_, age_);
      new_resume->work_experience_ = work_experience_->Clone();//也是继续 clone 的。
      return new_resume;
    }
    void Resume::PrintResume() {
      std::cout<< name_ << ", " << sex_ << ", " << age_ << ", "
               << work_experience_->GetCompany() << " : " << work_experience_->GetTimeArea() << std::endl;
    }


main.cpp：

    
    #include "prototype.h"
    
    
    int main() {
        Resume *resume1_;
        Resume *resume2_;
    
        resume1_ = new Resume("Bob");
        resume1_->SetPersonalInfo("M", "24");
        resume1_->SetWorkExperience("Google", "2015~2017");
    
        resume2_ = resume1_->Clone();
    
        resume1_->PrintResume();
        resume2_->PrintResume();
    
        resume2_->SetPersonalInfo("F", "23");
        resume1_->PrintResume();
        resume2_->PrintResume();
    
        resume2_->SetWorkExperience("Twitter", "2016~2017");
        resume1_->PrintResume();
        resume2_->PrintResume();
    
        resume1_->SetWorkExperience("Amazon", "2015-2017");
        resume1_->PrintResume();
        resume2_->PrintResume();
    
        delete resume1_;
        delete resume2_;
    
        return 0;
    }


**看起来好像与普通的拷贝功能相同，确认下。如果是的话，确认一下这个拷贝功能的一些写法，还是说这样写是最OK的。**


## 代码说明


Prototype模式的结构和实现都很简单，其关键就是拷贝构造函数的实现方式，这也是C++实现技术层面上的事情。




# Prototype 讨论


Prototype 模式通过复制原型 (Prototype) 而获得新对象创建的功能，这里 Prototype 本身就是 “对象工厂” (因为能够生产对象)。

实际上 Prototype 模式和 Builder 模式、 AbstractFactory 模式都是通过一个类 (对象实例) 来专门负责对象的创建工作 (工厂对象)， 它们之间的区别是：



 	
  * Builder模式重在复杂对象的一步步创建(并不直接返回对象)，

 	
  * AbstractFactory 模式重在产生多个相互依赖类的对象。

 	
  * Prototype 模式重在从自身复制自 己创建新类。


**的确是有相似的地方的。**







* * *





# COMMENT






 	
  1. 原型模式：用原型实例指定创建对象的种类，并通过拷贝这些原型创建对象。本质是从一个对象再创建另一个可定制的对象，并且不需要知道创建细节。

 	
  2. 原型抽象类的关键是有一个Clone()方法，原型具体类中复写Clone()创建当前对象的浅表副本。

 	
  3. 对.Net而言，由于拷贝太常用原型抽象类并不需要，在System命名空间中提供了ICloneable接口，其中唯一的方法就是Clone()，只要实现这个接口就可以完成原型模式。

 	
  4. 原型拷贝无需重新初始化对象，动态获取对象的运行状态。既隐藏了对象创建的细节，又提升性能。

 	
  5. 在具体原型类中，MemberwiseClone()方法是浅拷贝，对值类型字段诸位拷贝，对引用类型只复制引用但不会把具体的对象值拷贝过来。

 	
  6. 比起浅拷贝，深拷贝把引用对象的变量指向新对象，而不是原被引用的对象。对于需要深拷贝的每一层，都需要实现ICloneable原型模式。

 	
  7. 数据集对象DataSet，Clone()是浅拷贝，Copy()是深拷贝。


