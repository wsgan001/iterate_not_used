---
author: evo
comments: true
date: 2018-03-20 11:14:51+00:00
layout: post
link: http://106.15.37.116/2018/03/20/python-set/
slug: python-set
title: python中的set
wordpress_id: 459
categories:
- 随想与反思
tags:
- python
---

<!-- more -->


## 缘由：


set与dict和list有些不同，作为集合，有一些特有的运算


## 要点：




### 集合的初始化：



    
    s_a = set([1, 2, 2, 3, 4, 5, 6])  # 这里在使用list初始化set的时候 重复的元素自动只保留一个
    s_b = set([4, 5, 6, 7])
    print(s_a)
    print(s_b)


输出：

    
    {1, 2, 3, 4, 5, 6}
    {4, 5, 6, 7}




### 集合的运算：



    
    s_a = set([1, 2, 2, 3, 4, 5, 6])  # 这里在使用list初始化set的时候 重复的元素自动只保留一个
    s_b = set([4, 5, 6, 7])
    # 并集
    print(s_a | s_b)
    print(s_a.union(s_b))
    # 交集
    print(s_a & s_b)
    print(s_a.intersection(s_b))  # 通过intersection生成一个新的set
    # 差集 a-a&b
    print(s_a - s_b)
    print(s_a.difference(s_b))
    # 对称差 （A|B）-(A&B) 把两个集合相同的部分去除  这应该就是异或吧
    print(s_a ^ s_b)
    print(s_a.symmetric_difference(s_b))


输出：

    
    {1, 2, 3, 4, 5, 6, 7}
    {1, 2, 3, 4, 5, 6, 7}
    {4, 5, 6}
    {4, 5, 6}
    {1, 2, 3}
    {1, 2, 3}
    {1, 2, 3, 7}
    {1, 2, 3, 7}


备注：要补充下


### 修改集合中的元素：



    
    s_a = set([1, 2, 2, 3, 4, 5, 6])
    s_a.add('x')
    s_a.update([4, 5, 6, 9])
    print(s_a)


输出：

    
    {1, 2, 3, 4, 5, 6, 'x', 9}


删除集合中的元素：

    
    # 必须知道这个元素的值，不然的话无法删除
    s_a = set([1, 2, 2, 'x', 4, 5, 6])
    s_a.remove('x')
    s_a.remove(88)


输出：

    
    Traceback (most recent call last):
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2881, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
      File "<ipython-input-5-4a1d9e03e895>", line 4, in <module>
        s_a.remove(88)
    KeyError: 88


注意：必须知道这个元素的值，不然只能用try catch


