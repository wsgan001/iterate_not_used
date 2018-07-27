---
author: evo
comments: true
date: 2018-05-31 23:45:08+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%8a%b6%e6%80%81%e6%a8%a1%e5%bc%8f%ef%bc%88state%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e7%8a%b6%e6%80%81%e6%a8%a1%e5%bc%8f%ef%bc%88state%ef%bc%89'
title: 设计模式 状态模式（State）
wordpress_id: 7181
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





# 状态模式（State）





 	
  1. 拥有过多分支的过长方法违背了单一职责原则，而且当需求变化时修改代码往往会违背开放-封闭原则，应该将分支变成一不同小类，将状态的判断逻辑转移到小类中。

 	
  2. 状态模式：一个对象可能拥有多种状态，当内在状态改变时允许改变行为。

 	
  3. 状态模式的好处是将与特定状态有关的行为局部化，并将不同状态的行为分隔开。






State 模式
-问题
每个人、事物在不同的状态下会有不同表现（动作），而一个状态又会在不同的表现下 转移到下一个不同的状态（State）。最简单的一个生活中的例子就是：地铁入口处，如果你 放入正确的地铁票，门就会打开让你通过。在出口处也是验票，如果正确你就可以ok，否 则就不让你通过（如果你动作野蛮，或许会有报警（Alarm），））。

有限状态自动机（FSM）也是一个典型的状态不同，对输入有不同的响应（状态转移）。 通常我们在实现这类系统会使用到很多的Switch/Case语句，Case某种状态，发生什么动作， Case另外一种状态，则发生另外一种状态。但是这种实现方式至少有以下两个问题：

1） 当状态数目不是很多的时候，Switch/Case可能可以搞定。但是当状态数目很多的时 候（实际系统中也正是如此），维护一大组的Switch/Case语句将是一件异常困难并且容易出 错的事情。

2） 状态逻辑和动作实现没有分离。在很多的系统实现中，动作的实现代码直接写在状 态的逻辑当中。这带来的后果就是系统的扩展性和维护得不到保证。

■模式选择
State模式就是被用来解决上面列出的两个问题的，在State模式中我们将状态逻辑和动 作实现进行分离。当一个操作中要维护大量的 case 分支语句，并且这些分支依赖于对象的 状态。State模式将每一个分支都封装到独立的类中。State模式典型的结构图为：

图 2-1: State Pattern 结构图


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b1101944368a.png)


-实现
♦完整代码示例

state.h

    
    #ifndef DESIGN_PATTERNS_STATE_H
    #define DESIGN_PATTERNS_STATE_H
    
    class State;
    
    class Work {
    public:
      Work();
      ~Work();
      void SetState(State*);
      void WriteProgram();
    
    public:
      bool finished_;
      int hour_;
    
    private:
      State* state_;
    };
    
    class State {
    public:
      virtual ~State() {}
      virtual void WriteProgram(Work*) = 0;
    };
    
    class WorkingState: public State {
      void WriteProgram(Work* work);
    };
    
    class OvertimeState: public State {
      void WriteProgram(Work* work);
    };
    
    class RestState: public State {
      void WriteProgram(Work* work);
    };
    
    class SleepingState: public State {
      void WriteProgram(Work* work);
    };
    
    
    #endif //DESIGN_PATTERNS_STATE_H
    


state.cpp

    
    #include "state.h"
    #include <iostream>
    
    Work::Work() {
      state_ = new WorkingState();
    }
    
    Work::~Work() {
      delete state_;
    }
    
    void Work::SetState(State *state) {
      delete state_;
      state_ = state;
    }
    
    void Work::WriteProgram() {
      state_->WriteProgram(this);
    }
    
    void WorkingState::WriteProgram(Work *work) {
      if(work->hour_ < 17) {
        std::cout << work->hour_ << " : working" << std::endl;
      } else {
        work->SetState(new OvertimeState());
        work->WriteProgram();
      }
    }
    
    void OvertimeState::WriteProgram(Work *work) {
      if(work->finished_) {
        work->SetState(new RestState());
        work->WriteProgram();
      } else if (work->hour_ < 21) {
        std::cout << work->hour_ << " : overtime" << std::endl;
      } else {
        work->SetState(new SleepingState());
        work->WriteProgram();
      }
    }
    
    void RestState::WriteProgram(Work *work) {
      std::cout << work->hour_ << " : return to rest" << std::endl;
    }
    
    void SleepingState::WriteProgram(Work *work) {
      std::cout << work->hour_ << " : sleeping" << std::endl;
    }


main.cpp

    
    #include "state.h"
    #include <iostream>
    
    
    int main() {
        Work *work_;
        work_ = new Work();
    
        work_->hour_ = 15;
        work_->WriteProgram();
    
        work_->hour_ = 20;
        work_->finished_ = false;
        work_->WriteProgram();
    
        work_->hour_ = 22;
        work_->WriteProgram();
    
        delete work_;
        work_ = new Work();
        work_->hour_ = 20;
        work_->finished_ = true;
        work_->WriteProgram();
        delete work_;
    
    
        return 0;
    }


♦代码说明

State 模式在实现中，有两个关键点:

1) 将State声明为Context的友元类(friend class)，其作用是让State模式访问Context 的 protected 接口 ChangeSate ()。

2) State及其子类中的操作都将Context*传入作为参数，其主要目的是State类可以通 过这个指针调用Context中的方法(在本示例代码中没有体现)。这也是State模式和Strategy 模式的最大区别所在。

运行了示例代码后可以获得以下的结果：连续3次调用了 Context的OprationInterface () 因为每次调用后状态都会改变(A_B_A)，因此该动作随着Context的状态的转变而获得了不同的结果。

■讨论
State模式的应用也非常广泛，从最高层逻辑用户接口 GUI到最底层的通讯协议（例如 GoF在《设计模式》中就利用State模式模拟实现一个TCP连接的类。）都有其用武之地。

State模式和Strategy模式又很大程度上的相似：它们都有一个Context类，都是通过委 托（组合）给一个具有多个派生类的多态基类实现Context的算法逻辑。两者最大的差别就 是State模式中派生类持有指向Context对象的引用，并通过这个引用调用Context中的方法， 但在 Strategy 模式中就没有这种情况。因此可以说一个 State 实例同样是 Strategy 模式的一 个实例，反之却不成立。实际上State模式和Strategy模式的区别还在于它们所关注的点不 尽相同：State模式主要是要适应对象对于状态改变时的不同处理策略的实现，而Strategy 则主要是具体算法和实现接口的解耦（coupling），Strategy模式中并没有状态的概念（虽然 很多时候有可以被看作是状态的概念），并且更加不关心状态的改变了。

State模式很好地实现了对象的状态逻辑和动作实现的分离，状态逻辑分布在State的派 生类中实现，而动作实现则可以放在Context类中实现（这也是为什么State派生类需要拥 有一个指向Context的指针）。这使得两者的变化相互独立，改变State的状态逻辑可以很容 易复用Context的动作，也可以在不影响State派生类的前提下创建Context的子类来更改或 替换动作实现。

State模式问题主要是逻辑分散化，状态逻辑分布到了很多的State的子类中，很难看到 整个的状态逻辑图，这也带来了代码的维护问题。
对于State模式，很多情况下和Strategy模式看起来极为相似。实际上它 们都是为了解决具体子类实现抽象接口的实现异构问题而存在的（封装变化），

但是它们的侧重各不相同。而针对算法的异构问题，Template模式通过继承的 方式来改变一部分算法实现（原子操作在不同具体子类中可以有不同实现）， Strategy模式则通过组合的方式来改变整个算法（可动态替换），而State模 式则强调的是针对不同的状态对象可以有不同的响应。因此State模式实际上强 调的状态的概念，并且强调对状态转换的逻辑封装，即对象可能处于不同的状态 下，而各个状态在响应了该状态的实现后可能会动态转到另一个状态，而这个转 变我们不希望Context的参与（Context不必维护这个转换）。状态机在编译原 理的DFA/NDFA中很常见，针对一个输入字符和已有串，DFA/NDFA可能会转换到 另外一个状态。

因此对于State模式有以下几个关键点：

1） State模式会处理算法的不同，但是更加关注的是状态的改变。并且对 于状态的转变逻辑一般会放在State子类中实现。而对于不同状态的处 理则可以放在Context类中，State子类保存一个指向Context的引用（实 际上往往传递一个指向Context的指针即可，而不必在State子类真正 保存一个引用），以调用这些实现。当然放在State子类中实现也无可 厚非，不过为了突出重点，使用前一种方式实现更能说明问题。当然在 实际开发中，完全可以不受这个制约。

2） 在具体实现过程中，对状态的改变我们会在Context类中实现（因为 Context才有State的概念），而在State子类中的状态转变逻辑实现则

通过调用这个实现来达到目的。当然为了不让这个改变状态的接口暴露

给普通客户程序员，我们将Context中这个接口声明为private，而在将 State类声明为Context的friend类，并且将State子类中状态改变逻 辑实现声明为Protected，不让普通客户程序员调用。具体请参考示例代 码部分。







* * *





# COMMENT



