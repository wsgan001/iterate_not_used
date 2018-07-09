---
author: evo
comments: true
date: 2018-05-02 14:51:27+00:00
layout: post
link: http://106.15.37.116/2018/05/02/cpp-stl/
slug: cpp-stl
title: C++ STL 教程
wordpress_id: 4937
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [C++ 教程 w3cschool](https://www.w3cschool.cn/cpp/)

 	
  2. [C++ 教程 菜鸟教程](http://www.runoob.com/cplusplus/cpp-tutorial.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## C++ STL 教程


在前面的章节中，我们已经学习了 C++ 模板的概念。C++ STL（标准模板库）是一套功能强大的 C++ 模板类，提供了通用的模板类和函数，这些模板类和函数可以实现多种流行和常用的算法和数据结构，如向量、链表、队列、栈。

C++ 标准模板库的核心包括以下三个组件：
<table class="reference notranslate " >
<tbody >
<tr >
组件
描述
</tr>
<tr >

<td >容器（Containers）
</td>

<td >容器是用来管理某一类对象的集合。C++ 提供了各种不同类型的容器，比如 deque、list、vector、map 等。
</td>
</tr>
<tr >

<td >算法（Algorithms）
</td>

<td >算法作用于容器。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。
</td>
</tr>
<tr >

<td >迭代器（terators）
</td>

<td >迭代器用于遍历对象集合的元素。这些集合可能是容器，也可能是容器的子集。
</td>
</tr>
</tbody>
</table>
这三个组件都带有丰富的预定义函数，帮助我们通过简单的方式处理复杂的任务。

下面的程序演示了向量容器（一个 C++ 标准的模板），它与数组十分相似，唯一不同的是，向量在需要扩展大小的时候，会自动处理它自己的存储需求：

    
    #include <iostream>
    #include <vector>
    using namespace std;
     
    int main()
    {
       // 创建一个向量存储 int
       vector<int> vec; 
       int i;
    
       // 显示 vec 的原始大小
       cout << "vector size = " << vec.size() << endl;
    
       // 推入 5 个值到向量中
       for(i = 0; i < 5; i++){
          vec.push_back(i);
       }
    
       // 显示 vec 扩展后的大小
       cout << "extended vector size = " << vec.size() << endl;
    
       // 访问向量中的 5 个值
       for(i = 0; i < 5; i++){
          cout << "value of vec [" << i << "] = " << vec[i] << endl;
       }
    
       // 使用迭代器 iterator 访问值
       vector<int>::iterator v = vec.begin();
       while( v != vec.end()) {
          cout << "value of v = " << *v << endl;
          v++;
       }
    
       return 0;
    }


当上面的代码被编译和执行时，它会产生下列结果：

    
    vector size = 0
    extended vector size = 5
    value of vec [0] = 0
    value of vec [1] = 1
    value of vec [2] = 2
    value of vec [3] = 3
    value of vec [4] = 4
    value of v = 0
    value of v = 1
    value of v = 2
    value of v = 3
    value of v = 4
    


关于上面实例中所使用的各种函数，有几点要注意：



 	
  * push_back( ) 成员函数在向量的末尾插入值，如果有必要会扩展向量的大小。

 	
  * size( ) 函数显示向量的大小。

 	
  * begin( ) 函数返回一个指向向量开头的迭代器。

 	
  * end( ) 函数返回一个指向向量末尾的迭代器。
























* * *





# COMMENT



