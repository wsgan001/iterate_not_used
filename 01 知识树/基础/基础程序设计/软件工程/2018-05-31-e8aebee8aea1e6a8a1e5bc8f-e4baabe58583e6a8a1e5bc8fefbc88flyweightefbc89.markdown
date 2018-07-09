---
author: evo
comments: true
date: 2018-05-31 23:42:03+00:00
layout: post
link: http://106.15.37.116/2018/06/01/%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e4%ba%ab%e5%85%83%e6%a8%a1%e5%bc%8f%ef%bc%88flyweight%ef%bc%89/
slug: '%e8%ae%be%e8%ae%a1%e6%a8%a1%e5%bc%8f-%e4%ba%ab%e5%85%83%e6%a8%a1%e5%bc%8f%ef%bc%88flyweight%ef%bc%89'
title: 设计模式 享元模式（Flyweight）
wordpress_id: 7171
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





# 享元模式（Flyweight）





 	
  1. 享元模式：运用共享技术有效支持大量细粒度对象。

 	
  2. 在享元模式对象内部不随环境改变的共享部分是内部状态，不可共享需要通过调用传递进来的参数是外部状态。

 	
  3. 使用享元模式的场景包括，一个应用程序产生了大量的实例对象，占用了大量内存开销；或对象的大多数状态为外部状态，删除内部状态后可以用较少的共享对象来取代组对象。

 	
  4. 应用场景有正则表达式、浏览器、机器人指令集等。








2.5 Flyweight 模式
■问题
在面向对象系统的设计何实现中，创建对象是最为常见的操作。这里面就有一个问题: 如果一个应用程序使用了太多的对象，就会造成很大的存储开销。特别是对于大量轻量级（细 粒度）的对象，比如在文档编辑器的设计过程中，我们如果为没有字母创建一个对象的话， 系统可能会因为大量的对象而造成存储开销的浪费。例如一个字母“a”在文档中出现了 10⑻00次，而实际上我们可以让这一万个字母“a”共享一个对象，当然因为在不同的位置 可能字母“a”有不同的显示效果（例如字体和大小等设置不同），在这种情况我们可以为将 对象的状态分为“外部状态”和“内部状态”，将可以被共享（不会变化）的状态作为内部 状态存储在对象中，而外部对象（例如上面提到的字体、大小等）我们可以在适当的时候将 外部对象最为参数传递给对象（例如在显示的时候，将字体、大小等信息传递给对象）。

■模式选择
上面解决问题的方式被称作Flyweight模式解决上面的问题，其典型的结构图为：

图 2-1: Flyweight Pattern 结构图


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b10fd7c3c243.png)


可以从图2-1中看出，Flyweight模式中有一个类似Factory模式的对象构造工厂 FlyweightFactory，当客户程序员（Client）需要一个对象时候就会向FlyweightFactory发出 请求对象的消息GetFlyweight （）消息，FlyweightFactory拥有一个管理、存储对象的“仓 库”（或者叫对象池，vector实现），GetFlyweight （）消息会遍历对象池中的对象，如果已 经存在则直接返回给Client，否则创建一个新的对象返回给Client。当然可能也有不想被共 享的对象（例如结构图中的UnshareConcreteFlyweight），但不在本模式的讲解范围，故在实 现中不给出。

-实现
♦完整代码示例（code）

flyweight.h：

    
    #ifndef DESIGN_PATTERNS_FLYWEIGHT_H
    #define DESIGN_PATTERNS_FLYWEIGHT_H
    
    #include <map>
    #include <string>
    
    class User {
    public:
      User() {}
      User(std::string);
      std::string GetName();
    
    private:
      std::string name_;
    };
    
    class Website {
    public:
      virtual void Use(User *) = 0;
    };
    
    class ConcreteWebsite: public Website {
    public:
      ConcreteWebsite() {}
      ConcreteWebsite(std::string);
      void Use(User *);
    private:
      std::string website_name_;
    };
    
    class WebsiteFactory {
    public:
      ~WebsiteFactory();
      Website* GetWebsiteCategory(std::string);
      int GetWebsiteCount();
    
    private:
      std::map <std::string, Website*> flyweights_;
    };
    
    #endif //DESIGN_PATTERNS_FLYWEIGHT_H
    


flyweight.cpp：

    
    #include "flyweight.h"
    #include <iostream>
    
    User::User(std::string name): name_(name) {}
    
    std::string User::GetName() {
      return name_;
    }
    
    ConcreteWebsite::ConcreteWebsite(std::string website_name): website_name_(website_name) {}
    
    void ConcreteWebsite::Use(User *user) {
      std::cout << user->GetName() << " use " << website_name_ << std::endl;
    }
    
    WebsiteFactory::~WebsiteFactory() {
      std::map <std::string, Website*> ::iterator it;
      for(it = flyweights_.begin(); it != flyweights_.end(); it++) {
        delete it->second;
      }
    }
    
    Website* WebsiteFactory::GetWebsiteCategory(std::string website_name) {
      if(flyweights_.find(website_name) == flyweights_.end()) {
        Website *website = new ConcreteWebsite(website_name);
        flyweights_[website_name] = website;
      }
      return flyweights_[website_name];
    }
    
    int WebsiteFactory::GetWebsiteCount() {
      int cnt = (int)flyweights_.size();
      std::cout << cnt << std::endl;
      return cnt;
    }
    
    


main.cpp：

    
    #include "flyweight.h"
    #include <iostream>
    
    
    int main() {
        WebsiteFactory *website_factory_;
        Website *website_;
        website_factory_ = new WebsiteFactory();
        website_ = website_factory_->GetWebsiteCategory("bbs");
        website_->Use(new User("Bob"));
        website_->Use(new User("Alice"));
        website_factory_->GetWebsiteCount();
    
        website_ = website_factory_->GetWebsiteCategory("blog");
        website_->Use(new User("Bob"));
        website_->Use(new User("Alice"));
        website_factory_->GetWebsiteCount();
    
        website_ = website_factory_->GetWebsiteCategory("bbs");
        website_->Use(new User("Bob"));
        website_->Use(new User("Alice"));
        website_factory_->GetWebsiteCount();
    
        delete website_factory_;
        return 0;
    }


Flyweight模式完整的实现代码（所有代码采用C++实现，并在VC 6.0下测试运行）。 代码片断 1: Flyweight.h

//

♦代码说明

Flyweight模式在实现过程中主要是要为共享对象提供一个存放的“仓库”(对象池)， 这里是通过C++ STL中Vector容器，当然就牵涉到STL编程的一些问题(Iterator使用等)。 另外应该注意的就是对对象“仓库”(对象池)的管理策略(查找、插入等)，这里是通过直 接的顺序遍历实现的，当然我们可以使用其他更加有效的索引策略，例如Hash表的管理策 略，当时这些细节己经不是Flyweight模式本身要处理的了。

■讨论
我们在 State 模式和 Strategy 模式中会产生很多的对象，因此我们可以通过 Flyweight 模式来解决这个问题。













* * *





# COMMENT



