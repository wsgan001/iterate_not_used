---
author: evo
comments: true
date: 2018-05-18 09:45:13+00:00
layout: post
link: http://106.15.37.116/2018/05/18/cpp-%e4%b8%8d%e5%ad%98%e5%9c%a8%e9%bb%98%e8%ae%a4%e6%9e%84%e9%80%a0%e5%87%bd%e6%95%b0/
slug: cpp-%e4%b8%8d%e5%ad%98%e5%9c%a8%e9%bb%98%e8%ae%a4%e6%9e%84%e9%80%a0%e5%87%bd%e6%95%b0
title: C++ 不存在默认构造函数
wordpress_id: 6001
categories:
- 随想与反思
tags:
- C++
- NOT_ADD
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. [关于”类‘customer’中不存在默认构造函数“的问题](https://blog.csdn.net/sun980984305/article/details/52958275)




# TODO





 	
  * **这个问题暴露了自己在类的构造函数那个地方还是有很多的问题的。详细总结之后把问题的根源更新到这里。**





* * *





# INTRODUCTION





 	
  * aaa




# 问题缘由


这样写：

    
    struct RandomListNode {
    	int label;
    	struct RandomListNode *next;
    	struct RandomListNode *random;
    };
    int main() {
    	RandomListNode node_list[5];
    }


没有问题，但是，写成这样就有问题了：

    
    struct RandomListNode {
    	int label;
    	struct RandomListNode *next;
    	struct RandomListNode *random;
    	RandomListNode(int x) :
    		label(x), next(NULL), random(NULL) {
    	}
    };
    int main() {
    	RandomListNode node_list[5];
    }


在node_list下面有一条红线：**类"RandomListNode” 不存在默认构造函数**。

这个问题很久以前就遇到过。没有进行总结，这里总结一下：






# 问题解决


两个解决方法：



 	
  * 方法一：添加一个不带参数的构造函数即可。

 	
  * 方法二：给带参的构造函数里面的参数赋上初值。


























* * *





# COMMENT



