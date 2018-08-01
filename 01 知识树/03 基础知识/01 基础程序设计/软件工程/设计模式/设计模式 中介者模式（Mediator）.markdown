
# REF

1. [design-patterns-cpp](https://github.com/yogykwan/design-patterns-cpp)  作者： [Jennica](http://jennica.space/)  厉害的

2. 《设计模式精解 - GoF 23种设计模式解析》

3. 《大话设计模式》作者 程杰




# TODO






  * aaa





* * *





# INTRODUCTION






  * aaa




  1. 中介者模式：用一个中介对象来封装一系列对象间的交互。


  2. 中介者模式在系统中易用也容易被误用，当系统中出现了多对多的交互复杂的对象群时，更应考虑设计的问题。


  3. 由于控制集中化，中介者模式将交互复杂性变成了中介者的复杂性，中介者类会比任何一个同事类都复杂。


  4. 中介者模式应用的场合有，一组对象以定义良好但复杂的方式进行通信，以及想定制一个分布在多个类中的行为却不想产生太多子类。






Mediator 模式
■问题
在面向对象系统的设计和开发过程中，对象之间的交互和通信是最为常见的情况，因为 对象间的交互本身就是一种通信。在系统比较小的时候，可能对象间的通信不是很多、对象 也比较少，我们可以直接硬编码到各个对象的方法中。但是当系统规模变大，对象的量变引 起系统复杂度的急剧增加，对象间的通信也变得越来越复杂，这时候我们就要提供一个专门 处理对象间交互和通信的类，这个中介者就是Mediator模式。Mediator模式提供将对象间 的交互和通讯封装在一个类中，各个对象间的通信不必显势去声明和引用，大大降低了系统 的复杂性能（了解一个对象总比深入熟悉n个对象要好）。另外Mediator模式还带来了系统 对象间的松耦合，这些将在讨论中详细给出。

■模式选择
Mediator模式典型的结构图为：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/8ekD4baGDF.png?imageslim)

图 2-1: Mediator Pattern 结构图

Mediator 模式中，每个 Colleague 维护一个 Mediator ，当要进行交互，例如图中 ConcreteColleagueA 和 ConcreteColleagueB 之间的交互就可以通过 ConcreteMediator 提供的 DoActionFromAtoB 来处理，ConcreteColleagueA 和 ConcreteColleagueB 不必维护对各自的引 用，甚至它们也不知道各个的存在。Mediator通过这种方式将多对多的通信简化为了一 (Mediator)对多(Colleague)的通信。

-实现
♦完整代码示例(code)

mediator.h


    #ifndef DESIGN_PATTERNS_MEDIATOR_H
    #define DESIGN_PATTERNS_MEDIATOR_H

    #include <string>

    class Country;

    class UnitedNations {
    public:
      virtual void Declare(std::string, Country*) = 0;
    };

    class UnitedNationsSecurityCouncil: public UnitedNations {
    public:
      UnitedNationsSecurityCouncil() {}
      void SetUsa(Country*);
      void SetIraq(Country*);
      void Declare(std::string, Country*);

    private:
      Country *usa_;
      Country *iraq_;
    };

    class Country {
    public:
      Country() {}
      Country(UnitedNations*);
      virtual void Declare(std::string) = 0;
      virtual void GetMessage(std::string) = 0;

    protected:
      UnitedNations *mediator_;
    };

    class Usa: public Country {
    public:
      Usa(UnitedNations*);
      void Declare(std::string);
      void GetMessage(std::string);
    };

    class Iraq: public Country {
    public:
      Iraq(UnitedNations*);
      void Declare(std::string);
      void GetMessage(std::string);
    };


    #endif //DESIGN_PATTERNS_MEDIATOR_H



mediator.cpp


    #include "mediator.h"
    #include <iostream>

    void UnitedNationsSecurityCouncil::SetUsa(Country *usa) {
      usa_ = usa;
    }

    void UnitedNationsSecurityCouncil::SetIraq(Country *iraq) {
      iraq_ = iraq;
    }

    void UnitedNationsSecurityCouncil::Declare(std::string message, Country * country) {
      if(country == usa_) {
        iraq_->GetMessage(message);
      } else if(country == iraq_){
        usa_->GetMessage(message);
      }
    }

    Country::Country(UnitedNations *mediator): mediator_(mediator) {}

    Usa::Usa(UnitedNations *mediator): Country(mediator) {}

    void Usa::Declare(std::string message) {
      mediator_->Declare(message, this);
    }

    void Usa::GetMessage(std::string message) {
      std::cout << "USA gets: \"" << message << "\"" << std::endl;
    }

    Iraq::Iraq(UnitedNations *mediator): Country(mediator) {}

    void Iraq::Declare(std::string message) {
      mediator_->Declare(message, this);
    }

    void Iraq::GetMessage(std::string message) {
      std::cout << "Iraq gets: \"" << message << "\"" << std::endl;
    }




main.cpp


    #include "mediator.h"
    #include <iostream>


    int main() {
        UnitedNationsSecurityCouncil *unsc_;
        Country *usa_;
        Country *iraq_;
        unsc_ = new UnitedNationsSecurityCouncil();
        usa_ = new Usa(unsc_);
        iraq_ = new Iraq(unsc_);
        unsc_->SetUsa(usa_);
        unsc_->SetIraq(iraq_);
        usa_->Declare("Stop nuclear weapons");
        iraq_->Declare("No nuclear here");
        delete unsc_;
        delete usa_;
        delete iraq_;

        return 0;
    }


♦代码说明

Mediator模式的实现关键就是将对象Colleague之间的通信封装到一个类种单独处理， 为了模拟Mediator模式的功能，这里给每个Colleague对象一个string型别以记录其状态， 并通过状态改变来演示对象之间的交互和通信。这里主要就 Mediator 的示例运行结果给出 分析：

1） 将 ConcreteColleageA 对象设置状态 “old” ConcreteColleageB 也设置状态 “old”

2） ConcreteColleageA 对象改变状态，并在 Action 中和 ConcreteColleageB 对象进行通信，并改变

ConcreteColleageB 对象的状态为 “new”

3） ConcreteColleageB 对象改变状态，并在 Action 中和 ConcreteColleageA 对象进行通信，并改变 ConcreteColleageA 对象的状态为 “new”

注意到，两个 Colleague 对象并不知道它交互的对象，并且也不是显示地处理交互过程，这一切都是

通过Mediator对象完成的，示例程序运行的结果也正是证明了这一点。

■讨论
Mediator 模式是一种很有用并且很常用的模式，它通过将对象间的通信封装到一个类 中，将多对多的通信转化为一对多的通信，降低了系统的复杂性。Mediator还获得系统解耦 的特性，通过Mediator，各个Colleague就不必维护各自通信的对象和通信协议，降低了系 统的耦合性，Mediator和各个Colleague就可以相互独立地修改了。

Mediator模式还有一个很显著额特点就是将控制集中，集中的优点就是便于管理，也正 式符合了 OO设计中的每个类的职责要单一和集中的原则。

















* * *





# COMMENT
