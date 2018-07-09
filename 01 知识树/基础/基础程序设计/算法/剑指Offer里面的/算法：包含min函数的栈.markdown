---
author: evo
comments: true
date: 2018-05-18 04:01:55+00:00
layout: post
link: http://106.15.37.116/2018/05/18/%e7%ae%97%e6%b3%95%ef%bc%9a%e5%8c%85%e5%90%abmin%e5%87%bd%e6%95%b0%e7%9a%84%e6%a0%88/
slug: '%e7%ae%97%e6%b3%95%ef%bc%9a%e5%8c%85%e5%90%abmin%e5%87%bd%e6%95%b0%e7%9a%84%e6%a0%88'
title: 算法：包含min函数的栈
wordpress_id: 5971
categories:
- 随想与反思
tags:
- ordinary algorithm
---

<!-- more -->

[mathjax]

**注：非原创，只是按照自己的思路做了整合，修改。推荐直接看 ORIGINAL 中所列的原文。**


# ORIGINAL





 	
  1. 


[CodingInterviews](https://github.com/gatieme/CodingInterviews)







# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# 题目




## 题目描述


自己定义一个栈的数据结构，并在该类型中实现一个能够得到栈最小元素的min函数（可以使用已经有的 stack）。


## 





# 题目解答




## 使用两个栈来实现


我们可以维持两个栈：



 	
  * 数据栈 data，存储栈的数据用于常规的栈操作。

 	
  * 最小栈 min，保存每次 push 和 pop 时候，data 栈中的最小值。


流程如下：

 	
  * 在 push 的时候，将新的数据压入 data ，然后，把新数据与 min 中的 top 数据进行对比，把其中小的那个压入 min 。

 	
  * 在 pop 的时候，将 data 和 min 的 top 数据弹出。


这样，min 中就一直存着当前现场的最小值，并且随着数据栈的更新而更新

嗯，感觉用文字还是说的不是很清楚，可以看下代码：

代码如下：

    
    #include <iostream>
    #include <vector>
    #include <stack>
    #include <assert.h>
    
    using namespace std;
    
    
    class Solution {
    public:
    	void push(int value) {
    
    		//正常的压入栈中
    		this->m_data.push(value);
    
    		//查看是否是比m_min中top里的最小值小，如果小就压栈，不然还是压top的最小值
    		if (this->m_min.size() == 0 || value < this->m_min.top()) {
    			this->m_min.push(value);
    		}
    		else {
    			this->m_min.push(this->m_min.top());
    		}
    	}
    
    	//同时pop
    	void pop() {
    		assert(this->m_data.size() > 0 && this->m_min.size() > 0);
    		this->m_data.pop();
    		this->m_min.pop();
    	}
    
    	int top() {
    		assert(this->m_data.size() > 0 && this->m_min.size() > 0);
    		return this->m_data.top();
    	}
    
    	int min() {
    		if (this->m_data.empty() == true) {
    			return 0;
    		}
    		return this->m_min.top();
    	}
    protected:
    	stack<int>  m_data;     // 作为正常的数据存储栈
    	stack<int>  m_min;      // 用来存储m_data中的每次操作之后的最小值
    };
    
    int main() {
    	Solution solu;
    	solu.push(2);
    	cout << solu.min() << endl;
    	solu.push(4);
    	cout << solu.min() << endl;
    	solu.push(1);
    	cout << solu.min() << endl;
    	solu.push(5);
    	cout << solu.min() << endl;
    	solu.pop();
    	cout << solu.min() << endl;
    	solu.pop();
    	cout << solu.min() << endl;
    	solu.pop();
    	cout << solu.min() << endl;
    	solu.pop();
    
    	return 0;
    }


输出：

    
    2
    2
    1
    1
    1
    2
    2




















* * *





# COMMENT



