---
author: evo
comments: true
date: 2018-05-11 13:16:46+00:00
layout: post
link: http://106.15.37.116/2018/05/11/python-sum/
slug: python-sum
title: python sum
wordpress_id: 5542
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. aaa




# TODO





 	
  * **再将sum总结一下，这里没有进行总结。**




# MOTIVE





 	
  * aaa





* * *





# 缘由


今天在看代码的时候，看到了 train_set=sum(folds, []) 这样的写法，意思应该是 将采样生成的几个folds合并成一个train_set。没想到sum还有这种方式。


# 示例代码



    
    l1 = [[1, 2, 'a'], [11, 22, 'b']]
    l2 = [[3, 4, 'c'], [33, 44, 'd']]
    l = []
    l.append(l1)
    l.append(l2)
    print(l)
    print(sum(l, []))


输出：

    
    [[[1, 2, 'a'], [11, 22, 'b']], [[3, 4, 'c'], [33, 44, 'd']]]
    [[1, 2, 'a'], [11, 22, 'b'], [3, 4, 'c'], [33, 44, 'd']]


看到这个，我想起了之前遇到的一个问题：

将这种权重值：列向量 w=[[1],[2],[3]]  转化成行向量。之前不知道怎么转，只能一个一个遍历然后重新放到一个list。现在就可以这样：

    
    a = [[1], [2], [3]]
    b = sum(a, [])
    print(b)


输出：

    
    [1, 2, 3]


当然，如果是np.mat类型的矩阵，直接用 transpose() 或者 .T 就可以转换。













* * *





# COMMENT



