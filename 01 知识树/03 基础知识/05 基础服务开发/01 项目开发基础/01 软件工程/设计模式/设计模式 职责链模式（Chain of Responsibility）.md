---
author: evo
comments: true
date: 2018-05-31 23:44:20+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%81%8c%e8%b4%a3%e9%93%be%e6%a8%a1%e5%bc%8f%ef%bc%88chain-of-responsibility%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%81%8c%e8%b4%a3%e9%93%be%e6%a8%a1%e5%bc%8f%ef%bc%88chain-of-responsibility%ef%bc%89'
title: 设计模式 职责链模式（Chain of Responsibility）
wordpress_id: 7179
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





# 职责链模式（Chain of Responsibility）






  1. 职责链模式：使多个对象都有机会处理请求，解除请求发送者和接收者的耦合。将对象连成一条链，并沿这条链传递请求直到请求被解决。


  2. 请求交付给最小接受者，职责链中每一环保存后继的引用，使得请求有序沿链传递。


  3. 通过合理设置后继以及分支关系，避免一个请求到了链末端依旧无法被处理，或因配置错误得不到处理的情况。




3.9 Chain of Responsibility 模式
-问题
熟悉VC/MFC的都知道，VC是“基于消息，事件驱动”消息在VC开发中起着举足 轻重的作用。在 MFC 中，消息是通过一个向上递交的方式进行处理，例如一个 WM_COMMAND消息的处理流程可能为：

1) MDI 主窗口( CMDIFrameWnd)收到命令消息 WM_COMMAND，其 ID 位 ID_

XXX；

2) MDI主窗口将消息传给当前活动的MDI子窗口(CMDIChildWnd);

3) MDI子窗口给自己的子窗口(View) —个处理机会，将消息交给View;

4) View 检查自己 Message Map;

5) 如果View没有发现处理该消息的程序，则将该消息传给其对应的Document对 象；否则View处理，消息流程结束。

6) Document检查自己Message Map，如果没有该消息的处理程序，则将该消息传 给其对象的DocumentTemplate处理；否则自己处理，消息流程结束；

7) 如果在6)中消息没有得到处理，则将消息返回给View；

8) View再传回给MDI子窗口；

9) MDI子窗口将该消息传给CwinApp对象，CwinApp为所有无主的消息提供了 处理。

注明：有关MFC消息处理更加详细信息，请参考候捷先生的《深入浅出MFC》。

MFC提供了消息的处理的链式处理策略，处理消息的请求将沿着预先定义好的路径依 次进行处理。消息的发送者并不知道该消息最后是由那个具体对象处理的，当然它也无须也 不想知道，但是结构是该消息被某个对象处理了，或者一直到一个终极的对象进行处理了。

Chain of Responsibility 模式描述其实就是这样一类问题将可能处理一个请求的对象链 接成一个链，并将请求在这个链上传递，直到有对象处理该请求(可能需要提供一个默认处 理所有请求的类，例如MFC中的CwinApp类)。

■模式选择
Chain of Responsibility模式典型的结构图为：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Gg7HKFk8J8.png?imageslim)

图 2-1： Chain of Responsibility Pattern 结构图

Chain of Responsibility模式中ConcreteHandler将自己的后继对象(向下传递消息的对 象)记录在自己的后继表中，当一个请求到来时，ConcreteHandler会先检查看自己有没有 匹配的处理程序，如果有就自己处理，否则传递给它的后继。当然这里示例程序中为了简化， ConcreteHandler 只是简单的检查看自己有没有后继，有的话将请求传递给后继进行处理， 没有的话就自己处理。

-实现
♦完整代码示例(code)

chain_of_responsibility.h


    #ifndef DESIGN_PATTERNS_CHAIN_OF_RESPONSIBILITY_H
    #define DESIGN_PATTERNS_CHAIN_OF_RESPONSIBILITY_H

    #include <string>

    class Request {
    public:
      Request() {}
      Request(std::string, int);
      std::string GetType();
      int GetNumber();

    private:
      std::string type_;
      int number_;
    };

    class Manager {
    public:
      Manager() {}
      Manager(std::string);
      void SetSuperior(Manager *);
      virtual void RequestApplications(Request *) = 0;

    protected:
      Manager *superior_;
      std::string name_;
    };

    class CommonManager: public Manager {
    public:
      CommonManager(std::string);
      void RequestApplications(Request *);
    };

    class Majordomo: public Manager {
    public:
      Majordomo(std::string);
      void RequestApplications(Request *);
    };

    class GeneralManager: public Manager {
    public:
      GeneralManager(std::string);
      void RequestApplications(Request *);
    };


    #endif //DESIGN_PATTERNS_CHAIN_OF_RESPONSIBILITY_H



chain_of_responsibility.cpp


    #include "chain_of_responsibility.h"
    #include <iostream>

    Request::Request(std::string type, int number): type_(type), number_(number) {}

    int Request::GetNumber() {
      return number_;
    }

    std::string Request::GetType() {
      return type_;
    }

    Manager::Manager(std::string name): name_(name) {}

    void Manager::SetSuperior(Manager *superior) {
      superior_ = superior;
    }

    CommonManager::CommonManager(std::string name): Manager(name) {}

    void CommonManager::RequestApplications(Request *request) {
      if(request->GetType() == "leave application" && request->GetNumber() <= 2){
        std::cout << name_ << " : approve" << std::endl;
      } else {
        superior_->RequestApplications(request);
      }
    }

    Majordomo::Majordomo(std::string name): Manager(name) {}

    void Majordomo::RequestApplications(Request *request) {
      if(request->GetType() == "leave application" && request->GetNumber() <= 5){
        std::cout << name_ << " : approve" << std::endl;
      } else {
        superior_->RequestApplications(request);
      }
    }

    GeneralManager::GeneralManager(std::string name): Manager(name) {}

    void GeneralManager::RequestApplications(Request *request) {
      if(request->GetType() == "leave application"){
        std::cout << name_ << " : approve" << std::endl;
      } else if(request->GetType() == "salary increase" && request->GetNumber() <= 500){
        std::cout << name_ << " : approve" << std::endl;
      } else {
        std::cout << name_ << " : not approve" << std::endl;
      }
    }



main.cpp


    #include "chain_of_responsibility.h"
    #include <iostream>


    int main() {
        Request *request1_, *request2_;
        CommonManager *common_manager_;
        Majordomo *majordomo_;
        GeneralManager *general_manager_;
        common_manager_ = new CommonManager("JingLi");
        majordomo_ = new Majordomo("ZongJian");
        general_manager_ = new GeneralManager("ZongJingLi");
        common_manager_->SetSuperior(majordomo_);
        majordomo_->SetSuperior(general_manager_);

        request1_ = new Request("leave application", 4);
        common_manager_->RequestApplications(request1_);

        request2_ = new Request("salary increase", 1000);
        common_manager_->RequestApplications(request2_);
        delete request1_;
        delete request2_;
        delete common_manager_;
        delete majordomo_;
        delete general_manager_;



        return 0;
    }


♦代码说明

Chain of Responsibility模式的示例代码实现很简单，这里就其测试结果给出说明： ConcreteHandleA的对象和hl拥有一个后继ConcreteHandleB的对象h2,当一个请求到来时 候，h1检查看自己有后继，于是h1直接将请求传递给其后继h2进行处理，h2因为没有后 继，当请求到来时候，就只有自己提供响应了。于是程序的输出为:

1) ConcreteHandleA 我把处理权给后继节点.....；

2) ConcreteHandleB 没有后继了，我必须自己处理....。

■讨论
Chain of Responsibility模式的最大的一个有点就是给累统降低了賴合性，请求的发送者 完全不必知道该请求会被哪个应答对象处理，极大地降低了系统的耦合性。

















* * *





# COMMENT
