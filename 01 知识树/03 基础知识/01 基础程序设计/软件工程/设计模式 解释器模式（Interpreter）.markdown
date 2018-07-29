---
author: evo
comments: true
date: 2018-05-31 23:48:50+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a7%a3%e9%87%8a%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88interpreter%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e8%a7%a3%e9%87%8a%e5%99%a8%e6%a8%a1%e5%bc%8f%ef%bc%88interpreter%ef%bc%89'
title: 设计模式 解释器模式（Interpreter）
wordpress_id: 7196
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







# 解释器模式（Interpreter）






  1. 解释器模式：给定一种语言，定义它文法的一种表示，再定义一个解释器，使用该表示来解释语言中的句子。


  2. 如果一种特定类型发生的频率足够高，就可以将其实例表达为一个句子，构建解释器来解析。


  3. 解释器模式就是用“迷你语言”来表现程序要解决的问题，将句子抽象为语法树。由于各个节电的类大体相同，便于修改、扩展和实现。


  4. 解释器为文法中的每条规则定义了一个类，当文法过多时将难以维护，建议使用其他技术如语法分析程序或编译器生成器处理。




Interpreter 模式
■问题
一些应用提供了内建（Build-In）的脚本或者宏语言来让用户可以定义他们能够在系统 中进行的操作。Interpreter模式的目的就是使用一个解释器为用户提供一个一门定义语言的 语法表示的解释器，然后通过这个解释器来解释语言中的句子。

Interpreter 模式提供了这样的一个实现语法解释器的框架，笔者曾经也正在构建一个编 译系统Visual CMCS，现在己经发布了 Visual CMCS1.0 （Beta），请大家访问Visual CMCS网 站获取详细信息。

■模式选择
Interpreter模式典型的结构图为:


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/EGHh6J9e1D.png?imageslim)

图 2-1: Interpreter Pattern 结构图

Interpreter 模式中，提供了 TerminalExpression 和 NonterminalExpression 两种表达式的角军 释方式，Context类用于为解释过程提供一些附加的信息（例如全局的信息）。

-实现
♦完整代码示例(code)

interpreter.h


    #ifndef DESIGN_PATTERNS_INTERPRETER_H
    #define DESIGN_PATTERNS_INTERPRETER_H

    #include <string>

    class Context {
    public:
      void SetText(std::string);
      std::string GetText();

    private:
      std::string text_;
    };

    class Expression {
    public:
      virtual ~Expression() {}
      void Interprete(Context*);

    protected:
      virtual void Excute(std::string, double) = 0;
    };

    class Scale: public Expression {
    private:
      void Excute(std::string, double);
    };

    class Note: public Expression {
    private:
      void Excute(std::string, double);
    };

    class ExpressionFactory {
    public:
      Expression* CreateExpression(Context*);
    };

    #endif //DESIGN_PATTERNS_INTERPRETER_H



interpreter.cpp


    #include "interpreter.h"
    #include <iostream>
    #include <sstream>

    std::string Context::GetText() {
      return text_;
    }

    void Context::SetText(std::string text) {
      text_ = text;
    }

    void Expression::Interprete(Context *context) {
      std::stringstream ss;
      std::string key;
      double value;
      std::string remain_text = context->GetText();
      ss << remain_text;
      ss >> key >> value;
      remain_text = remain_text.substr(remain_text.find(" ")+1);
      remain_text = remain_text.substr(remain_text.find(" ")+1);
      if(remain_text.length() < 3){
        remain_text = "";
      }
      context->SetText(remain_text);
      Excute(key, value);
    }

    void Scale::Excute(std::string key, double value) {
      switch ((int)value){
        case 1:
          std::cout << "bass " << std::endl;
          break;
        case 2:
          std::cout << "alto " << std::endl;
          break;
        case 3:
          std::cout << "treble " << std::endl;
          break;
        default:
          break;
      }
    }

    void Note::Excute(std::string key, double value) {
      std::cout << key[0] << std::endl;
    }

    Expression* ExpressionFactory::CreateExpression(Context *context) {
      char key = context->GetText()[0];
      if(key == 'O') {
        return new Scale();
      } else {
        return new Note();
      }
    }


main.cpp


    #include "interpreter.h"
    #include <iostream>


    int main() {
        Context *context_;
        ExpressionFactory *expression_factory_;
        Expression *expression_;

        expression_factory_ = new ExpressionFactory();
        context_ = new Context();
        context_->SetText("O 2 E 0.5 G 0.5 A 3");
        while (context_->GetText().length()) {
            expression_ = expression_factory_->CreateExpression(context_);
            expression_->Interprete(context_);
            delete expression_;
        }
        delete context_;
        delete expression_factory_;


        return 0;
    }


♦代码说明

Interpreter 模式的示例代码很简单，只是为了说明模式的组织和使用，实际的解释 Interpret逻辑没有实际提供。

■讨论
XML 格式的数据解析是一个在应用开发中很常见并且有时候是很难处理的事情，虽然 目前很多的开发平台、语言都提供了对XML格式数据的解析，但是例如到了移动终端设备 上，由于处理速度、计算能力、存储容量的原因解析XML格式的数据却是很复杂的一件事 情，最近也提出了很多的移动设备的XML格式解析器，但是总体上在项目开发时候还是需 要自己去设计和实现这一个过程（笔者就有过这个方面的痛苦经历）。

Interpreter模式则提供了一种很好的组织和设计这种解析器的架构。

Interpreter 模式中使用类来表示文法规则，因此可以很容易实现文法的扩展。另外对于 终结符我们可以使用Flyweight模式来实现终结符的共享。















* * *





# COMMENT
