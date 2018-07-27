---
author: evo
comments: true
date: 2018-05-31 10:19:52+00:00
layout: post
link: http://106.15.37.116/2018/05/31/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%8a%bd%e8%b1%a1%e5%b7%a5%e5%8e%82%e6%a8%a1%e5%bc%8f%ef%bc%88abstract-factory%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e6%8a%bd%e8%b1%a1%e5%b7%a5%e5%8e%82%e6%a8%a1%e5%bc%8f%ef%bc%88abstract-factory%ef%bc%89'
title: 设计模式 抽象工厂模式（Abstract Factory）
wordpress_id: 7145
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





# 抽象工厂模式是为了解决什么问题的？


比如说，我们要开发一款游戏，为了吸引更多的人玩，游戏难度就不能太大，但是也不能太简单OK，我们就可以给游戏设立等级：初级、中级、高级。

OK，假设现在是一个过关游戏，每个关卡都有一些怪物守着，玩家要把这些怪物干掉才可以过关。那么，一般作为开发者，我们就可以创建怪物类，然后初级怪物、中级怪物等都继承自这个怪物类（当然不同种类的则需要另创建类，但是模式相同）。在每个关卡，我们都要创建怪物的实例，例如初级就创建初级怪物（有很多种类）、中级创建中级怪物等。

可以想象在这个系统中，将会有成千上万的怪物实例要创建，问题是还要保证创建的时候不会出错：初级不能创建高级的怪物，反之也不可以。

**没明白？普通的方法不行吗？什么叫创建的时候不会出错？**

AbstractFactory 模式就是用来解决这类问题的：要创建一组相关或者相互依赖的对象。




# AbstractFactory 模式介绍




![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10c61ed71e8.png)


上面这个就是 AbstractFactory 模式的典型结构图，这个模式的关键就是将这一组对象的创建封装到一个用于创建对象的具体类  ConcreteFactory 中。这样，维护这样一个创建类就比维护很多相关对象的创建过程要简单的多。

**还需要再解释下。**


# 完整代码




## 代码如下


abstract_factory.h：

    
    #ifndef DESIGN_PATTERNS_ABSTRACT_FACTORY_H
    #define DESIGN_PATTERNS_ABSTRACT_FACTORY_H
    
    
    //两种数据类型 User 和 Department
    //由于对于相同的 User 数据，存放到 sql 和 access 中的时候，使用的具体的方法是不同的，
    //因此又分成了 SqlServerUser 和 AccessUser。也是用的factory。
    //这样我存放的时候 我只需要把User数据传给 IUser，它就自己把数据按照具体的子类对应进行处理了。
    class User {
    };
    class IUser {
    public:
        virtual void InsertUser(User*) = 0;
        virtual User GetUser(int) = 0;
    };
    class SqlserverUser: public IUser {
    public:
        void InsertUser(User* user);
        User GetUser(int id);
    };
    class AccessUser: public IUser {
    public:
        void InsertUser(User* user);
        User GetUser(int id);
    };
    
    
    
    class Department {
    };
    class IDepartment {
    public:
        virtual void InsertDepartment(Department*) = 0;
        virtual Department GetDepartment(int) = 0;
    };
    class SqlserverDepartment: public IDepartment {
    public:
        void InsertDepartment(Department* department);
        Department GetDepartment(int id);
    };
    class AccessDepartment: public IDepartment {
    public:
        void InsertDepartment(Department* department);
        Department GetDepartment(int id);
    };
    
    
    //这个工厂用来实现对数据库的操作，可能是 sql 数据库，也可能是 access
    class IFactory {
    public:
        virtual IUser* CreateUser() = 0;
        virtual IDepartment* CreateDepartment() = 0;
    };
    class SqlserverFactory: public IFactory {
    public:
        IUser* CreateUser();
        IDepartment* CreateDepartment();
    };
    class AccessFactory: public IFactory {
    public:
        IUser* CreateUser();
        IDepartment* CreateDepartment();
    };
    
    
    #endif //DESIGN_PATTERNS_ABSTRACT_FACTORY_H
    


abstract_factory.cpp：

    
    #include "abstract_factory.h"
    #include <iostream>
    
    void SqlserverUser::InsertUser(User* user) {
      std::cout << "Insert User into Sqlserver" << std::endl;
    }
    User SqlserverUser::GetUser(int id) {
      std::cout << "Get User from Sqlserver" << std::endl;
    }
    
    
    void AccessUser::InsertUser(User* user) {
      std::cout << "Insert User into Access" << std::endl;
    }
    User AccessUser::GetUser(int id) {
      std::cout << "Get User from Access" << std::endl;
    }
    
    
    void SqlserverDepartment::InsertDepartment(Department* department) {
      std::cout << "Insert Department into Sqlserver" << std::endl;
    }
    Department SqlserverDepartment::GetDepartment(int id) {
      std::cout << "Get Department from Sqlserver" << std::endl;
    }
    
    
    void AccessDepartment::InsertDepartment(Department* department) {
      std::cout << "Insert Department into Access" << std::endl;
    }
    Department AccessDepartment::GetDepartment(int id) {
      std::cout << "Get Department from Access" << std::endl;
    }
    
    
    
    //两个数据库对两种数据类型的操作
    IUser* SqlserverFactory::CreateUser() {
      IUser* i_user =  new SqlserverUser();
      return i_user;
    }
    IDepartment* SqlserverFactory::CreateDepartment() {
      IDepartment* i_Department =  new SqlserverDepartment();
      return i_Department;
    }
    IUser* AccessFactory::CreateUser() {
      IUser* i_user =  new AccessUser();
      return i_user;
    }
    IDepartment* AccessFactory::CreateDepartment() {
      IDepartment* i_Department =  new AccessDepartment();
      return i_Department;
    }


main.cpp：

    
    #include "abstract_factory.h"
    
    
    int main() {
    
        IFactory* pIFactory;
        IUser* pIUser;
        IDepartment* pIDepartment;
    
        //使用 sqlserver 来添加 User 和 Department 数据
        pIFactory = new SqlserverFactory();
        pIUser = pIFactory->CreateUser();
        pIUser->InsertUser(new User());
        pIUser->GetUser(0);
        pIDepartment = pIFactory->CreateDepartment();
        pIDepartment->InsertDepartment(new Department());
        pIDepartment->GetDepartment(0);
    
        //使用 access 来添加 User 和 Department 数据
        //可见，与上面的对比，只改变了这个地方。
        pIFactory = new AccessFactory();
        pIUser = pIFactory->CreateUser();
        pIUser->InsertUser(new User());
        pIUser->GetUser(0);
        pIDepartment = pIFactory->CreateDepartment();
        pIDepartment->InsertDepartment(new Department());
        pIDepartment->GetDepartment(0);
    
    
        delete pIFactory;
        delete pIUser;
        delete pIDepartment;
    
        return 0;
    }


嗯，这个的确是工作中会用到的，当需要支持几种数据库的时候，就要用这个，或者说，上下位机之间需要支持几种传输协议的时候也会用这个。有些 nice。

把实现的逻辑放到对应的每个子工厂里面，这样，以后业务逻辑相关的代码在改动的时候，怎么改都可以，当想要切换不同的数据库的时候，直接把开始的这个 factory 更换一下就行，而不用再改什么别的代码。厉害。也就是说，只用维护这个Factory就行。

**再添加一下具体场景的代码**




# AbstractFactory 的优缺点


首先，我们看一下 AbstractFactory 与 Factory 模式有什么区别和联系？

实际上，AbstractFactor y模式是为创建一组 (有多类) 相关的l或依赖的对象提供创建接口，而 Factory 模式是为一类对象提供创建接口或延迟对象的创建到子类中实现。**这句怎么感觉不通顺呢？确认下。**

并且可以看到，AbstractFactory 模式通常都是使用 Factory 模式实现的。

**没有缺点吗？确认下。之前 Factory 的缺点，它有没有？**









* * *





# COMMENT





 	
  1. 抽象工厂模式：提供一个创建一系列相关或互相依赖对象的接口，只需要知道对象的系列，无需知道具体的对象。

 	
  2. 在客户端中，具体工厂类只在初始化时出现一次，更改产品系列即可使用不同产品配置。

 	
  3. 利用简单工厂类替换抽象工厂类及其子类，可以使客户端不再受不同系列的影响。

 	
  4. 结合反射机制，Assembly.Load(“程序集名称”).CreateInstance(“命名空间”.“类名”)，可以直接通过字符串创建对应类的实例。所有在简单工厂中，都可以通过反射去除switch或if，解除分支判断带来的耦合。

 	
  5. 反射中使用的字符串可以通过配置文件传入，避免更改代码。


