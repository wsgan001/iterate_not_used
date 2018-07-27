---
author: evo
comments: true
date: 2018-05-31 23:46:31+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%a4%87%e5%bf%98%e5%bd%95%e6%a8%a1%e5%bc%8f%ef%bc%88memento%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e5%a4%87%e5%bf%98%e5%bd%95%e6%a8%a1%e5%bc%8f%ef%bc%88memento%ef%bc%89'
title: 设计模式 备忘录模式（Memento）
wordpress_id: 7184
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







# 备忘录模式（Memento）






  1. 备忘录模式：不破坏封装，获取对象内部状态并在其之外保存该对象，以便其未来恢复到当前状态。


  2. Orginator负责创建Memento，Memento封装Originator状态细节，Caretaker负责保管和交付Memento。


  3. 备忘录模式适用于需要维护历史状态的对象，或只需要保存原类属性中的小部分。








Memento 模式
■问题
没有人想犯错误，但是没有人能够不犯错误。犯了错误一般只能改过，却很难改正（恢 复）。世界上没有后悔药，但是我们在进行软件系统的设计时候是要给用户后悔的权利（实 际上可能也是用户要求的权利：）），我们对一些关键性的操作肯定需要提供诸如撤销（Undo）

的操作。那这个后悔药就是Memento模式提供的。

■模式选择
Memento模式的关键就是要在不破坏封装行的前提下，捕获并保存一个类的内部 状态，这样就可以利用该保存的状态实施恢复操作。为了达到这个目标，可以在后面的实现 中看到我们采取了一定语言支持的技术。Memento模式的典型结构图为：

图 2-1: Memento Pattern 结构图


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/4g71H88Jhg.png?imageslim)

-实现
♦完整代码示例(code)

memento.h


    #ifndef DESIGN_PATTERNS_MEMENTO_H
    #define DESIGN_PATTERNS_MEMENTO_H

    class StateMemento {
    public:
      StateMemento() {}
      StateMemento(int, int);
      int GetHp();
      int GetMp();

    private:
      int hp_;
      int mp_;
    };

    class GameRole {
    public:
      GameRole();
      StateMemento* CreateMemento();
      void StateDisplay();
      void Fight();
      void RecoveryState(StateMemento*);

    private:
      int hp_;
      int mp_;
    };

    class StateCaretaker {
    public:
      StateCaretaker() {}
      StateCaretaker(StateMemento*);
      ~StateCaretaker();
      StateMemento* GetMemento();
    private:
      StateMemento* memento_;
    };


    #endif //DESIGN_PATTERNS_MEMENTO_H



memento.cpp


    #include "memento.h"
    #include <iostream>

    StateMemento::StateMemento(int hp, int mp): hp_(hp), mp_(mp) {}

    int StateMemento::GetHp() {
      return hp_;
    }

    int StateMemento::GetMp() {
      return mp_;
    }

    GameRole::GameRole(): hp_(100), mp_(100) {}

    StateMemento* GameRole::CreateMemento() {
      return new StateMemento(hp_, mp_);
    }

    void GameRole::StateDisplay() {
      std::cout << hp_ << " " << mp_ << std::endl;
    }

    void GameRole::Fight() {
      hp_ = 0;
      mp_ = 0;
    }

    void GameRole::RecoveryState(StateMemento *memento) {
      hp_ = memento->GetHp();
      mp_ = memento->GetMp();
    }

    StateCaretaker::StateCaretaker(StateMemento *memento): memento_(memento) {}

    StateCaretaker::~StateCaretaker() {
      delete memento_;
    }

    StateMemento* StateCaretaker::GetMemento() {
      return memento_;
    }





main.cpp


    #include "memento.h"
    #include <iostream>


    int main() {
        GameRole* game_role_;
        StateCaretaker* state_caretaker_;
        game_role_ = new GameRole();
        state_caretaker_ = new StateCaretaker(game_role_->CreateMemento());
        game_role_->StateDisplay();

        game_role_->Fight();
        game_role_->StateDisplay();

        game_role_->RecoveryState(state_caretaker_->GetMemento());
        game_role_->StateDisplay();
        delete game_role_;
        delete state_caretaker_;



        return 0;
    }


♦代码说明

Memento模式的关键就是friend class Originator;我们可以看到，Memento的接口都声明 为private，而将Originator声明为Memento的友元类。我们将Originator的状态保存在 Memento类中，而将Memento接口 private起来，也就达到了封装的功效。

在Originator类中我们提供了方法让用户后悔：RestoreToMemento（Memento* mt）;我们可以 通过这个接口让用户后悔。在测试程序中，我们演示了这一点：Originator的状态由old变为new最 后又回到了 old。

■讨论
在Command模式中，Memento模式经常被用来维护可以撤销（Undo）操作的状态。这 一点将在Command模式具体说明。











* * *





# COMMENT
