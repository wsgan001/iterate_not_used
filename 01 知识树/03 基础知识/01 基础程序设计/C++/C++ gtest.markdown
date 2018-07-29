---
author: evo
comments: true
date: 2018-06-01 10:06:56+00:00
layout: post
link: http://106.15.37.116/2018/06/01/c-gtest/
slug: c-gtest
title: C++ gtest
wordpress_id: 7309
categories:
- 基础程序设计
tags:
- '@NULL'
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL






  1.


[玩转Google开源C++单元测试框架Google Test系列(gtest)(总)](https://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)







# TODO






  * **要仔细总结一下 gtest 到底是怎么使用的**


  * **除了在 make 中使用 ，在 VS 中可以使用吗？**





* * *





# INTRODUCTION






  * aaa




# 缘由


一直想看一下C++的单元测试怎么写，但是不知道真的用什么，刚在看别人的设计模式的例子代码的时候，看到他用了 gtest，test文件是这么写的：


    #include "gtest/gtest.h"
    #include "command.h"

    class CommandFixture: public ::testing::Test {
    protected:
      virtual void TearDown() {};
      virtual void SetUp() {};

    public:
      CommandFixture(): Test() {
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
      }

      virtual ~CommandFixture() {
        delete barbecuer_;
        delete bake_mutton_command1_;
        delete bake_mutton_command2_;
        delete bake_chicken_command_;
        delete waiter_;
      }

      Barbecuer *barbecuer_;
      Command *bake_mutton_command1_;
      Command *bake_mutton_command2_;
      Command *bake_chicken_command_;
      Waiter *waiter_;
    };

    TEST_F(CommandFixture, command_test) {
    }


然后他的路径是这样的：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/7h21C6mc6c.png?imageslim)

他用的是make 不是 vs ，看来 make 还是一定要掌握的，不然连一些东西都不好用。



















* * *





# COMMENT
