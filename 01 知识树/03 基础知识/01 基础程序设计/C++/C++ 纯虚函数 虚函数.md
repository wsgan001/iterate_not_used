文章还没有看


首先：强调一个概念定义一个函数为虚函数，不代表函数为不被实现的函数。定义他为虚函数是为了允许用基类的指针来调用子类的这个函数。定义一个函数为纯虚函数，才代表函数没有被实现。定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数。

1、简介假设我们有下面的类层次：

```cpp
**class A**
**{**
**public:**
​    **virtual void foo()**
​    **{**
​        **cout<<"A::foo() is called"<<endl;**
    **}**
**};**
**class B:public A**
 **{**
 **public:**
 ​    **void foo()**
 ​    **{**
 ​        **cout<<"B::foo() is called"<<endl;**
 ​    **}**
 **};**
 **int main(void)**
 **{**
 ​    **A \*a = new B();**
 ​    **a->foo();   // 在这里，a虽然是指向A的指针，但是被调用的函数(foo)却是B的!**
 ​    **return 0;**
 **}**
```

​     **这个例子是虚函数的一个典型应用，通过这个例子，也许你就对虚函数有了一些概念。它虚就虚在所谓“推迟联编”或者“动态联编”上，一个类函数的调用并不是在编译时刻被确定的，而是在运行时刻被确定的。由于编写代码的时候并不能确定被调用的是基类的函数还是哪个派生类的函数，所以被成为“虚”函数。    虚函数只能借助于指针或者引用来达到多态的效果。C++纯虚函数一、定义　纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加“=0”　virtual void funtion1()=0二、引入原因　　1、为了方便使用多态特性，我们常常需要在基类中定义虚拟函数。　　2、在很多情况下，基类本身生成对象是不合情理的。例如，动物作为一个基类可以派生出老虎、孔雀等子类，但动物本身生成对象明显不合常理。　　为了解决上述问题，引入了纯虚函数的概念，将函数定义为纯虚函数（方法：virtual ReturnType Function()= 0;），则编译器要求在派生类中必须予以重写以实现多态性。同时含有纯虚拟函数的类称为抽象类，它不能生成对象。这样就很好地解决了上述两个问题。声明了纯虚函数的类是一个抽象类。所以，用户不能创建类的实例，只能创建它的派生类的实例。纯虚函数最显著的特征是：它们必须在继承类中重新声明函数（不要后面的＝0，否则该派生类也不能实例化），而且它们在抽象类中往往没有定义。定义纯虚函数的目的在于，使派生类仅仅只是继承函数的接口。纯虚函数的意义，让所有的类对象（主要是派生类对象）都可以执行纯虚函数的动作，但类无法为纯虚函数提供一个合理的缺省实现。所以类纯虚函数的声明就是在告诉子类的设计者，“你必须提供一个纯虚函数的实现，但我不知道你会怎样实现它”。抽象类的介绍抽象类是一种特殊的类，它是为了抽象和设计的目的为建立的，它处于继承层次结构的较上层。（1）抽象类的定义：  称带有纯虚函数的类为抽象类。（2）抽象类的作用：抽象类的主要作用是将有关的操作作为结果接口组织在一个继承层次结构中，由它来为派生类提供一个公共的根，派生类将具体实现在其基类中作为接口的操作。所以派生类实际上刻画了一组子类的操作接口的通用语义，这些语义也传给子类，子类可以具体实现这些语义，也可以再将这些语义传给自己的子类。（3）使用抽象类时注意：•   抽象类只能作为基类来使用，其纯虚函数的实现由派生类给出。如果派生类中没有重新定义纯虚函数，而只是继承基类的纯虚函数，则这个派生类仍然还是一个抽象类。如果派生类中给出了基类纯虚函数的实现，则该派生类就不再是抽象类了，它是一个可以建立对象的具体的类。•   抽象类是不能定义对象的。总结：1、纯虚函数声明如下： virtual void funtion1()=0; 纯虚函数一定没有定义，纯虚函数用来规范派生类的行为，即接口。包含纯虚函数的类是抽象类，抽象类不能定义实例，但可以声明指向实现该抽象类的具体类的指针或引用。2、虚函数声明如下：virtual ReturnType FunctionName(Parameter)；虚函数必须实现，如果不实现，编译器将报错，错误提示为：error LNK\****: unresolved external symbol "public: virtual void __thiscall ClassName::virtualFunctionName(void)"3、对于虚函数来说，父类和子类都有各自的版本。由多态方式调用的时候动态绑定。4、实现了纯虚函数的子类，该纯虚函数在子类中就编程了虚函数，子类的子类即孙子类可以覆盖该虚函数，由多态方式调用的时候动态绑定。5、虚函数是C++中用于实现多态(polymorphism)的机制。核心理念就是通过基类访问派生类定义的函数。6、在有动态分配堆上内存的时候，析构函数必须是虚函数，但没有必要是纯虚的。7、友元不是成员函数，只有成员函数才可以是虚拟的，因此友元不能是虚拟函数。但可以通过让友元函数调用虚拟成员函数来解决友元的虚拟问题。8、析构函数应当是虚函数，将调用相应对象类型的析构函数，因此，如果指针指向的是子类对象，将调用子类的析构函数，然后自动调用基类的析构函数。有纯虚函数的类是抽象类，不能生成对象，只能派生。他派生的类的纯虚函数没有被改写，那么，它的派生类还是个抽象类。定义纯虚函数就是为了让基类不可实例化化因为实例化这样的抽象数据结构本身并没有意义。或者给出实现也没有意义实际上我个人认为纯虚函数的引入，是出于两个目的1、为了安全，因为避免任何需要明确但是因为不小心而导致的未知的结果，提醒子类去做应做的实现。**

**2、为了效率，不是程序执行的效率，而是为了编码的效率。**

在C++中的一种函数申明被称之为：纯虚函数(pure virtual function).它的申明格式如下：

class CShape

{

public:

​    virtual void Show()=0;

};



类的一个成员定位虚函数的实际意义在于让C++知道该函数并无意义，它的作用只是为了让派生类进行函数重载保留位置。

注意红色部分，在普通的虚函数后面加上"=0"这样就声明了一个pure virtual function.



废话不说先看例子：

![复制代码](http://common.cnblogs.com/images/copycode.gif)

```
 1 #include <iostream>
 2 #include <cstdlib>
 3 #include <cstdio>
 4
 5 using namespace std;
 6
 7
 8 class abstractcls
 9 {
10 public:
11     abstractcls(float speed,int total)   //构造函数
12     {
13         this->speed = speed;
14         this->total = total;
15     }
16
17     virtual void showmember()= 0;    //纯虚函数的定义
18 protected:
19     float speed;
20     int total;
21 };
22
23 class car : public abstractcls
24 {
25 public:
26     car(int aird,float speed,int total):abstractcls(speed,total)
27     {
28         this->aird = aird;
29     }
30
31     virtual void showmember()
32     {
33         cout << speed <<"--------" <<total <<"-----------"<<aird<<endl;
34     }
35 protected:
36     int aird;
37 };
38 int main()
39 {
40     car b(250,150,4);
41     b.showmember();
42     return 0;
43 }
```

![复制代码](http://common.cnblogs.com/images/copycode.gif)

运行结果想必大家都知道！！就不写了！！

总结：什么时候需要用纯虚函数

1,当想要在基类中抽象出一个方法，且该类被继承类而不能被实例化时。
2,基类的方法必须在派生类中被实现时。
3,多个对象具有公共的抽象属性，但却有不同的实现要求时。

下面我们看一道某公司的面试的笔试题（含金量到底有多少？？）

![复制代码](http://common.cnblogs.com/images/copycode.gif)

```
#include <iostream>
#include <cstdio>

using namespace std;

class A
{
public:
    void foo()
    {
        printf("1\n");
    }
    virtual void fuu()
    {
        printf("2\n");
    }
};

class B:public A
{
public :
    void foo()
    {
        printf("3\n");
    }
    void fuu()
    {
        printf("4\n");
    }
};

int main()
{
    A a;
    B b;

    A *p = &a;
    cout<< "p->foo()---" ; p->foo() ;
    cout<<"p->fuu()---";p->fuu();

    cout <<"-------向上转型-----------"<<endl;
    p=&b;
    cout<<"p->foo()---";p->foo();
    cout<<"p->fuu()---";p->fuu();

    cout <<"--------向下转型----------"<<endl;

    B *ptr =(B *)&a;
    cout<<"ptr->foo()----";ptr->foo();
    cout<<"ptr->fuu()-----";ptr->fuu();
    return 0;
}
```

![复制代码](http://common.cnblogs.com/images/copycode.gif)

先不要看答案，看自己能否作对？？







运行结果：![img](http://images.cnitblog.com/blog/490879/201301/23103449-c1ccbee4e5f54fc79c16e2ff4688fbc9.png)

*下面进行详细分析一下为什么结果是这样的？？你全做对了没？？*

​      第一个p->foo()和p->fuu()都很好理解，本身是基类指针，指向的又是基类对象，调用的都是基类本身的函数，因此输出结果就是1、2。
　　第二个输出结果就是1、4。p->foo()和p->fuu()则是基类指针指向子类对象，正式体现多态的用法，p->foo()由于指针是个基类指针，指向是一个固定偏移量的函数，因此此时指向的就只能是基类的foo()函数的代码了，因此输出的结果还是1。而p->fuu()指针是基类指针，指向的fuu是一个虚函数，由于每个虚函数都有一个虚函数列表，此时p调用fuu()并不是直接调用函数，而是通过虚函数列表找到相应的函数的地址，因此根据指向的对象不同，函数地址也将不同，这里将找到对应的子类的fuu()函数的地址，因此输出的结果也会是子类的结果4.



　　第三个并不是很理解这种用法，从原理上来解释，由于B是子类指针，虽然被赋予了基类对象地址，但是ptr->foo()在调用的时候，由于地址偏移量固定，偏移量是子类对象的偏移量，于是即使在指向了一个基类对象的情况下，还是调用到了子类的函数，虽然可能从始到终都没有子类对象的实例化出现。
　　第四个：而ptr->fuu()的调用，可能还是因为C++多态性的原因，由于指向的是一个基类对象，通过虚函数列表的引用，找到了基类中foo()函数的地址，因此调用了基类的函数。由此可见多态性的强大，可以适应各种变化，不论指针是基类的还是子类的，都能找到正确的实现方法。



小结：1.有virtual才可能发生多态现象2.不发生多态（无virtual）调用就按原类型调用



![复制代码](http://common.cnblogs.com/images/copycode.gif)

```
#include <iostream>

using namespace std;

class Base
{
public:
    virtual void f(float x)
    {
        cout <<"Base::f(float)"<<x <<endl;
    }
    void g(float x)
    {
        cout <<"Base::g(float)"<<x<<endl;
    }
    void h(float x)
    {
        cout <<"Base::h(float)"<<x<<endl;
    }
};

class Derived:public Base
{
public:
    virtual void f(float x)
    {
        cout <<"Derived::f(float)"<<x<<endl;
    }

    void g(int x)
    {
        cout <<"Derived::g(int)"<<x <<endl;
    }

    void h(float x)
    {
        cout << "Derived::h(float)"<<x <<endl;
    }
};

int main()
{
    Derived d;
    Base *pb = &d;
    Derived *pd = &d;

    pb->f(3.14f);
    pd->f(3.14f);

    pb->g(3.14f);
    pd->g(3.14f);

    pb->h(3.14f);
    pd->h(3.14f);
    system("pause");

}
```

![复制代码](http://common.cnblogs.com/images/copycode.gif)





运行结果：![img](http://images.cnitblog.com/blog/490879/201301/23110330-7c871efdea134846a97c38b9b52f4e79.png)

虚函数和纯虚函数
在面向对象的C++语言中，虚函数（virtual function）是一个非常重要的概念。因为它充分体现了面向对象思想中的继承和多态性这两大特性，在C++语言里应用极广。比如在微软的MFC类库中，你会发现很多函数都有virtual关键字，也就是说，它们都是虚函数。难怪有人甚至称虚函数是C++语言的精髓。
那么，什么是虚函数呢，我们先来看看微软的解释：
虚函数是指一个类中你希望重载的成员函数，当你用一个基类指针或引用指向一个继承类对象的时候，你调用一个虚函数，实际调用的是继承类的版本。

——摘自MSDN
这个定义说得不是很明白。MSDN中还给出了一个例子，但是它的例子也并不能很好的说明问题。我们自己编写这样一个例子：



![复制代码](http://common.cnblogs.com/images/copycode.gif)

```
#include <iostream>
#include <conio.h>
using namespace std;

class Parent
{
public:
    char data[20];
    void Function1();
    virtual void Function2();
}parent;

void Parent::Function1()
{
    printf("This is parent,function1\n");
}

void Parent::Function2()
{
    printf("This is parent,function2\n");
}

class Child:public Parent
{
    void Function1();
    void Function2();
}child;

void Child::Function1()
{
    printf("This is child,function1\n");
}

void Child::Function2()
{
    printf("This is child,function2\n");
}


int main()
{
    Parent *p;
    if(_getch()=='c')
        p =&child;
    else
        p=&parent;

    p->Function1();

    p->Function2();

    return 0 ;


}
```

![复制代码](http://common.cnblogs.com/images/copycode.gif)

输入非c字符：运行结果：![img](http://images.cnitblog.com/blog/490879/201301/23115020-4ee03a8a64414dda80625e46ba3bd93f.png)



输入c字符：运行结果：![img](http://images.cnitblog.com/blog/490879/201301/23115110-ec901556fa7d44e592b53ad0f1e58924.png)



在这里就不过多解释以上程序，紧接着讲一下C++中虚函数和纯虚函数的虚别

首先：强调一个概念
定义一个函数为虚函数，不代表函数为不被实现的函数
定义他为虚函数是为了允许用基类的指针来调用子类的这个函数

定义一个函数为纯虚函数，才代表函数没有被实现
定义他是为了实现一个接口，起到一个规范的作用，规范继承这个
类的程序员必须实现这个函数。
对继承的影响：
普通的类（没有虚函数，纯虚函数）就可以被继承，而且工作的相当好
关于这个问题有以下疑问：
纯虚函数难道就是为了实现接口？接口存在的意义？
我实在弄不懂，我干嘛要预先定义好？未来的事情本难料
就等有一天我的类中需要使用某个函数，在添加一个函数



但是虚函数是不能重载的！！！！







## REF

- [C++ Primer--虚函数与纯虚函数的区别](https://blog.csdn.net/yusiguyuan/article/details/12676177)
