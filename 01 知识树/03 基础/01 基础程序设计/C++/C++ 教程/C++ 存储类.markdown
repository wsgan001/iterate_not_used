---
author: evo
comments: true
date: 2018-05-02 13:34:01+00:00
layout: post
link: http://106.15.37.116/2018/05/02/cpp-storage-class/
slug: cpp-storage-class
title: C++ 存储类
wordpress_id: 4874
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [C++ 教程 w3cschool](https://www.w3cschool.cn/cpp/)

 	
  2. [C++ 教程 菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)




# TODO





 	
  * **存储类这个名字好像第一次见 以前也叫存储类吗？**

 	
  * 



# MOTIVE





 	
  * 




* * *





## C++ 存储类






存储类定义 C++ 程序中变量/函数的范围（可见性）和生命周期。这些说明符放置在它们所修饰的类型之前。下面列出 C++ 程序中可用的存储类：



 	
  * auto

 	
  * register

 	
  * static

 	
  * extern

 	
  * mutable







## auto 存储类


**auto** 存储类是所有局部变量默认的存储类。

    
    {
       int mount;
       auto int month;
    }
    


上面的实例定义了两个带有相同存储类的变量，auto 只能用在函数内，即 auto 只能修饰局部变量。


## register 存储类


**register** 存储类用于定义存储在寄存器中而不是 RAM 中的局部变量。这意味着变量的最大尺寸等于寄存器的大小（通常是一个词），且不能对它应用一元的 '&' 运算符（因为它没有内存位置）。

    
    {
       register int  miles;
    }
    


寄存器只用于需要快速访问的变量，比如计数器。还应注意的是，定义 'register' 并不意味着变量将被存储在寄存器中，它意味着变量可能存储在寄存器中，这取决于硬件和实现的限制。


## static 存储类


**static** 存储类指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。因此，使用 static 修饰局部变量可以在函数调用之间保持局部变量的值。

static 修饰符也可以应用于全局变量。当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。

在 C++ 中，当 static 用在类数据成员上时，会导致仅有一个该成员的副本被类的所有对象共享。

    
    #include <iostream>
     
    // 函数声明 
    void func(void);
     
    static int count = 10; /* 全局变量 */
     
    int main()
    {
        while(count--)
        {
           func();
        }
        return 0;
    }
    // 函数定义
    void func( void )
    {
        static int i = 5; // 局部静态变量
        i++;
        std::cout << "变量 i 为 " << i ;
        std::cout << " , 变量 count 为 " << count << std::endl;
    }


当上面的代码被编译和执行时，它会产生下列结果：

    
    变量 i 为 6 , 变量 count 为 9
    变量 i 为 7 , 变量 count 为 8
    变量 i 为 8 , 变量 count 为 7
    变量 i 为 9 , 变量 count 为 6
    变量 i 为 10 , 变量 count 为 5
    变量 i 为 11 , 变量 count 为 4
    变量 i 为 12 , 变量 count 为 3
    变量 i 为 13 , 变量 count 为 2
    变量 i 为 14 , 变量 count 为 1
    变量 i 为 15 , 变量 count 为 0




## extern 存储类


**extern** 存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。当您使用 'extern' 时，对于无法初始化的变量，会把变量名指向一个之前定义过的存储位置。

当您有多个文件且定义了一个可以在其他文件中使用的全局变量或函数时，可以在其他文件中使用 _extern_ 来得到已定义的变量或函数的引用。可以这么理解，_extern_ 是用来在另一个文件中声明一个全局变量或函数。

extern 修饰符通常用于当有两个或多个文件共享相同的全局变量或函数的时候，如下所示：

第一个文件：main.cpp

    
    #include <iostream>
     
    int count ;
    extern void write_extern();
     
    main()
    {
       count = 5;
       write_extern();
    }


第二个文件：support.cpp

    
    #include <iostream>
     
    extern int count;
     
    void write_extern(void)
    {
       std::cout << "Count is " << count << std::endl; 
    }


在这里，第二个文件中的 _extern_ 关键字用于声明已经在第一个文件 main.cpp 中定义的 count。现在 ，编译这两个文件，如下所示：

    
    $g++ main.cpp support.cpp -o write
    


这会产生 **write** 可执行程序，尝试执行 **write**，它会产生下列结果：

    
    $ ./write
    Count is 5




## mutable 存储类


**mutable** 说明符仅适用于类的对象，这将在本教程的最后进行讲解。它允许对象的成员替代常量。也就是说，mutable 成员可以通过 const 成员函数修改。























* * *





# COMMENT



