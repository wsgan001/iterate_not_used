---
author: evo
comments: true
date: 2018-05-30 01:50:00+00:00
layout: post
link: http://106.15.37.116/2018/05/30/cpp-string%e3%80%81char/
slug: cpp-string%e3%80%81char
title: C++ string、char*
wordpress_id: 7015
categories:
- 基础程序设计
tags:
- C++
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [CString与string、char*的区别和转换](https://blog.csdn.net/luoweifu/article/details/20232379)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




# 缘由


其实我一直想知道，string 和 char * 到底有关系吗？有哪些转换的方法？为什么要弄两个类似的东西？尤其是里面的字符的交换、结尾 什么的。都要总结下。


# 介绍一下 char *




char*是一个指向字符的指针，是一个内置类型。可以指向一个字符，也可以表示字符数组的首地址（首字符的地址）。




我们更多的时候是用的它的第二的功能，来表示一个字符串，功能与字符串数组 char ch[n] 一样，表示字符串时，最后有一个 '\0' 结束符作为字符串的结束标志。


代码如下：

    
    #include <iostream>  
    using namespace std;
    void testCharArray() {
        char ch1[12] = "Hello Wrold"; //这里只能ch1[12]，ch1[11]编译不通过,提示 array bounds overflow  
        char *pch1, *pch2 = "string";
        char *pch3, *pch4;
        pch3 = &ch1[2]; //ch1[2]的地址赋给pch3  
        char ch = 'c';
        pch4 = &ch;
        pch1 = ch1;
    
        cout << ch1 << endl;    //输出ch1[0]到\0之前的所有字符  
        cout << pch1 << endl;   //输出ch1[0]到\0之前的所有字符  
        cout << pch2 << endl;   //输出ch1[0]到\0之前的所有字符  
        cout << pch3 << endl;   //输出ch1[2]到\0之前的所有字符  
        cout << *pch3 << endl;  //解引用pch3输出pch3指向的字符  
        cout << *pch4 << endl;  //解引用pch4输出pch4指向的字符  
    }
    int main() {
        testCharArray();
        return EXIT_SUCCESS;
    }


输出：

    
    Hello Wrold
    Hello Wrold
    string
    llo Wrold
    l
    c





# 介绍一下 string




string 是 C++ 标准库 (STL) 中的类型，它是定义的一个类，定义在 <string> 头文件中。里面包含了对字符串的各种常用操作，它较 char* 的优势是内容可以动态拓展，以及对字符串操作的方便快捷，用 + 号进行字符串的连接是最常用的操作。**什么是可以动态扩展？**


代码如下：

    
    #include <iostream>  
    #include <string>  
    using namespace std;
    
    void testString() {
        string s1 = "this";
        string s2 = string(" is");
        string s3, s4;
        s3 = string(" a").append("string.");
        s4 = s1 + s2 + s3;
    
        cout << s1 << endl;
        cout << s2 << endl;
        cout << s3 << endl;
        cout << s4 << endl;
        cout << s4.size() << endl;
        s4.insert(s4.end() - 7, 1, ' ');
        cout << s4 << endl;
    }
    
    int main() {
        testString();
        return EXIT_SUCCESS;
    }


输出：

    
    this
     is
     astring.
    this is astring.
    16
    this is a string.





# char* 与 string 的转换


代码如下：

    
    #include <iostream>  
    #include <string>  
    using namespace std;
    
    void pCharToString() {
    
        //from char* to string  
        char * ch = "hello world";
        string s1 = ch; //直接初始化或赋值  
        string s2(ch), s3;
        s3 = string(ch);
        cout << s1 << endl;
        cout << s2 << endl;
        cout << s3 << endl;
    
        //from string to char*  
        string str = string("string is commonly used.");
        /*************************************************************************
        其实没有很大的必要将string转换成char*,因为string可以直接当成字符数组来使用，
        即通过下标来访问字符元素，如str[1]表示第1个字符't'
        **************************************************************************/
        const char *ch1 = str.c_str();
        cout << ch1 << endl;
    }
    
    int main() {
        pCharToString();
        return EXIT_SUCCESS;
    }


输出：

    
    hello world
    hello world
    hello world
    string is commonly used.








* * *





# COMMENT



