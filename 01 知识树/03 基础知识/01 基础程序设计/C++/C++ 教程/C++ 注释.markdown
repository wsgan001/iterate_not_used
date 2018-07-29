---
author: evo
comments: true
date: 2018-05-02 13:15:27+00:00
layout: post
link: http://106.15.37.116/2018/05/02/cpp-comment/
slug: cpp-comment
title: C++ 注释
wordpress_id: 4858
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. aaa




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## C++ 注释


程序的注释是解释性语句，您可以在 C++ 代码中包含注释，这将提高源代码的可读性。所有的编程语言都允许某种形式的注释。

C++ 支持单行注释和多行注释。注释中的所有字符会被 C++ 编译器忽略。

C++ 注释以 /* 开始，以 */ 终止。例如：

    
    /* 这是注释 */
    
    /* C++ 注释也可以
     * 跨行
     */
    


注释也能以 // 开始，直到行末为止。例如：

    
    #include 
    using namespace std;
    
    main()
    {
       cout << "Hello World"; // 输出 Hello World     return 0; 
    }


当上面的代码被编译时，编译器会忽略 **// prints Hello World**，最后会产生以下结果：

    
    Hello World
    


在 /* 和 */ 注释内部，// 字符没有特殊的含义。在 // 注释内，/* 和 */ 字符也没有特殊的含义。因此，您可以在一种注释内嵌套另一种注释。例如：

    
    /* 用于输出 Hello World 的注释
    
    cout << "Hello World"; // 输出 Hello World  */
























* * *





# COMMENT



