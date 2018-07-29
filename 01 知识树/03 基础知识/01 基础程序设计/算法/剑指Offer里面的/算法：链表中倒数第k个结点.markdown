---
author: evo
comments: true
date: 2018-05-16 01:10:49+00:00
layout: post
link: http://106.15.37.116/2018/05/16/%e7%ae%97%e6%b3%95%ef%bc%9a%e9%93%be%e8%a1%a8%e4%b8%ad%e5%80%92%e6%95%b0%e7%ac%ack%e4%b8%aa%e7%bb%93%e7%82%b9/
slug: '%e7%ae%97%e6%b3%95%ef%bc%9a%e9%93%be%e8%a1%a8%e4%b8%ad%e5%80%92%e6%95%b0%e7%ac%ack%e4%b8%aa%e7%bb%93%e7%82%b9'
title: 算法：链表中倒数第k个结点
wordpress_id: 5813
categories:
- 随想与反思
tags:
- NOT_ADD
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




## **题目描述**


输入一个链表，输出该链表中倒数第k个结点。




# 题目解答




## 两趟遍历


看到这个题目，最简单的就是：两趟遍历，第一趟先求出 list 的长度 length ，然后进而 length - k 得到倒数第 k 个节点的位置。第二次遍历就得到了这个结点。


## 双指针法


当然，之前看过类似的题目会知道有另外一个更加高效的方法：**双指针法** ：

就是有两个指针 right 和 left，指针 right 先向前走K步，然后 left 和 right 一起走，此时两个指针差别K步，那么当 right 走到链表尾部的时候，left指向的就是倒数第 K 个节点。

当然，这中间还是要注意几点的：



 	
  * 链表可能为 NULL

 	
  * 链表长度可能没有 K 个



    
    #include <iostream>
    #include <vector>
    
    using namespace std;
    
    
    struct ListNode {
    	int val;
    	struct ListNode *next;
    
    };
    
    class Solution {
    public:
    	ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
    		//判断是不是NULL
    		if (pListHead == NULL) {
    			return NULL;
    		}
    
    
    		unsigned int i = 0;
    		ListNode *right = pListHead;
    
    		//  right指针先向前走K步
    		while (i < k && right != NULL) {
    			cout << "index  = " << i << ", value = " << right->val << endl;
    			right = right->next;
    			i++;
    		}
    		//判断这时right是不是NULL
    		if (right == NULL && i < k) {
    			cout << "the list length = " << i << " < " << k << endl;
    			return NULL;
    		}
    
    		//两个指针一起走
    		ListNode *left = pListHead;
    		while (right != NULL) {
    			cout << "index  = " << i++ << ", value = " << right->val << endl;
    			left = left->next;
    			right = right->next;
    		}
    		return left;
    	}
    };
    
    
    int main() {
    	//初始化listNode
    	ListNode list[4];
    	list[0].val = 1;
    	list[0].next = &list[1];
    	list[1].val = 2;
    	list[1].next = &list[2];
    	list[2].val = 3;
    	list[2].next = &list[3];
    	list[3].val = 4;
    	list[3].next = NULL;
    
    	Solution solu;
    	ListNode* node_k = solu.FindKthToTail(list, 3);
    	cout << node_k->val << endl;
    	return 0;
    }


输出：

    
    index  = 0, value = 1
    index  = 1, value = 2
    index  = 2, value = 3
    index  = 3, value = 4
    2


当然，类似的，也可以第一个指针 right 先向前走 K-1 步，然后 left 和 right 一起走，此时两个指针差别 K-1 步，那么当 right 走到链表尾部的前一个结点时候的，left指向的就是倒数第K个节点。

    
    #include <iostream>
    #include <vector>
    
    using namespace std;
    
    
    struct ListNode {
    	int val;
    	struct ListNode *next;
    
    };
    class Solution {
    public:
    	ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
    		//判断是不是NULL
    		if (pListHead == NULL) {
    			return NULL;
    		}
    
    
    		unsigned int i = 0;
    		ListNode *right = pListHead;
    
    		//  right指针先向前走K-1步
    		while (i < k - 1 && right != NULL) {
    			cout << "index  = " << i << ", value = " << right->val << endl;
    			right = right->next;
    			i++;
    		}
    		//判断这时right是不是NULL
    		if (right == NULL) {
    			cout << "the list length = " << i << " < " << k << endl;
    			return NULL;
    		}
    
    		//两个指针一起走
    		ListNode *left = pListHead;
    		while (right->next != NULL) {
    			cout << "index  = " << i++ << ", value = " << right->val << endl;
    			left = left->next;
    			right = right->next;
    		}
    		return left;
    	}
    };
    
    
    int main() {
    	//初始化listNode
    	ListNode list[4];
    	list[0].val = 1;
    	list[0].next = &list[1];
    	list[1].val = 2;
    	list[1].next = &list[2];
    	list[2].val = 3;
    	list[2].next = &list[3];
    	list[3].val = 4;
    	list[3].next = NULL;
    
    	Solution solu;
    	ListNode* node_k = solu.FindKthToTail(list, 3);
    	cout << node_k->val << endl;
    	return 0;
    }


输出：

    
    index  = 0, value = 1
    index  = 1, value = 2
    index  = 2, value = 3
    2


这两个其实是类似的，只是 right 指针走的位置不同。













* * *





# COMMENT



