---
author: evo
comments: true
date: 2018-05-03 05:24:27+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-dictionary/
slug: python-dictionary
title: Python 字典(Dictionary)
wordpress_id: 4984
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)

 	
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## Python 字典(Dictionary)


字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值(key=>value)对用冒号(**:**)分割，每个对之间用逗号(**,**)分割，整个字典包括在花括号(**{})**中 ,格式如下所示：

    
    d = {key1 : value1, key2 : value2 }
    


键必须是唯一的，但值则不必。

值可以取任何数据类型，但键必须是不可变的，如字符串，数字或元组。

一个简单的字典实例：

    
    dict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
    


也可如此创建字典：

    
    dict1 = { 'abc': 456 };
    dict2 = { 'abc': 123, 98.6: 37 };





* * *





## 访问字典里的值


把相应的键放入熟悉的方括弧，如下实例:

    
    #!/usr/bin/python
     
    dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
     
    print "dict['Name']: ", dict['Name'];
    print "dict['Age']: ", dict['Age'];
    


以上实例输出结果：

    
    dict['Name']:  Zara
    dict['Age']:  7
    


如果用字典里没有的键访问数据，会输出错误如下：

    
    #!/usr/bin/python
     
    dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
     
    print "dict['Alice']: ", dict['Alice'];
    


以上实例输出结果：

    
    dict['Zara']:
    Traceback (most recent call last):
      File "test.py", line 4, in <module>
        print "dict['Alice']: ", dict['Alice'];
    KeyError: 'Alice'
    






* * *





## 修改字典


向字典添加新内容的方法是增加新的键/值对，修改或删除已有键/值对如下实例:

    
    #!/usr/bin/python
     
    dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
     
    dict['Age'] = 8; # update existing entry
    dict['School'] = "DPS School"; # Add new entry
     
     
    print "dict['Age']: ", dict['Age'];
    print "dict['School']: ", dict['School'];
    


以上实例输出结果：

    
    dict['Age']:  8
    dict['School']:  DPS School
    






* * *





## 删除字典元素


能删单一的元素也能清空字典，清空只需一项操作。

显示删除一个字典用del命令，如下实例：

    
    #coding=utf-8
    #!/usr/bin/python
     
    dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
     
    del dict['Name']; # 删除键是'Name'的条目
    dict.clear();     # 清空词典所有条目
    del dict ;        # 删除词典
     
    print "dict['Age']: ", dict['Age'];
    print "dict['School']: ", dict['School'];
    


但这会引发一个异常，因为用del后字典不再存在：

    
    dict['Age']:
    Traceback (most recent call last):
      File "test.py", line 8, in <module>
        print "dict['Age']: ", dict['Age'];
    TypeError: 'type' object is unsubscriptable
    


**注：**del()方法后面也会讨论。

**字典键的特性**

字典值可以没有限制地取任何python对象，既可以是标准的对象，也可以是用户定义的，但键不行。

两个重要的点需要记住：

1）不允许同一个键出现两次。创建时如果同一个键被赋值两次，后一个值会被记住，如下实例：

    
    #!/usr/bin/python
     
    dict = {'Name': 'Zara', 'Age': 7, 'Name': 'Manni'};
     
    print "dict['Name']: ", dict['Name'];
    


以上实例输出结果：

    
    dict['Name']:  Manni
    


2）键必须不可变，所以可以用数，字符串或元组充当，所以用列表就不行，如下实例：

    
    #!/usr/bin/python
     
    dict = {['Name']: 'Zara', 'Age': 7};
     
    print "dict['Name']: ", dict['Name'];
    


以上实例输出结果：

    
    Traceback (most recent call last):
      File "test.py", line 3, in <module>
        dict = {['Name']: 'Zara', 'Age': 7};
    TypeError: list objects are unhashable
    






* * *





## 字典内置函数&方法


Python字典包含了以下内置函数：
<table class="reference  " >
<tbody >
<tr >
序号
函数及描述
</tr>
<tr >

<td >1
</td>

<td >[cmp(dict1, dict2)](https://www.w3cschool.cn/python/att-dictionary-cmp.html)
比较两个字典元素。
</td>
</tr>
<tr >

<td >2
</td>

<td >[len(dict)](https://www.w3cschool.cn/python/att-dictionary-len.html)
计算字典元素个数，即键的总数。
</td>
</tr>
<tr >

<td >3
</td>

<td >[str(dict)](https://www.w3cschool.cn/python/att-dictionary-str.html)
输出字典可打印的字符串表示。
</td>
</tr>
<tr >

<td >4
</td>

<td >[type(variable)](https://www.w3cschool.cn/python/att-dictionary-type.html)
返回输入的变量类型，如果变量是字典就返回字典类型。
</td>
</tr>
</tbody>
</table>
Python字典包含了以下内置函数：
<table class="reference  " >
<tbody >
<tr >
序号
函数及描述
</tr>
<tr >

<td >1
</td>

<td >[radiansdict.clear()](https://www.w3cschool.cn/python/att-dictionary-clear.html)
删除字典内所有元素
</td>
</tr>
<tr >

<td >2
</td>

<td >[radiansdict.copy()](https://www.w3cschool.cn/python/att-dictionary-copy.html)
返回一个字典的浅复制
</td>
</tr>
<tr >

<td >3
</td>

<td >[radiansdict.fromkeys()](https://www.w3cschool.cn/python/att-dictionary-fromkeys.html)
创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值
</td>
</tr>
<tr >

<td >4
</td>

<td >[radiansdict.get(key, default=None)](https://www.w3cschool.cn/python/att-dictionary-get.html)
返回指定键的值，如果值不在字典中返回default值
</td>
</tr>
<tr >

<td >5
</td>

<td >[radiansdict.has_key(key)](https://www.w3cschool.cn/python/att-dictionary-has_key.html)
如果键在字典dict里返回true，否则返回false
</td>
</tr>
<tr >

<td >6
</td>

<td >[radiansdict.items()](https://www.w3cschool.cn/python/att-dictionary-items.html)
以列表返回可遍历的(键, 值) 元组数组
</td>
</tr>
<tr >

<td >7
</td>

<td >[radiansdict.keys()](https://www.w3cschool.cn/python/att-dictionary-keys.html)
以列表返回一个字典所有的键
</td>
</tr>
<tr >

<td >8
</td>

<td >[radiansdict.setdefault(key, default=None)](https://www.w3cschool.cn/python/att-dictionary-setdefault.html)
和get()类似, 但如果键不已经存在于字典中，将会添加键并将值设为default
</td>
</tr>
<tr >

<td >9
</td>

<td >[radiansdict.update(dict2)](https://www.w3cschool.cn/python/att-dictionary-update.html)
把字典dict2的键/值对更新到dict里
</td>
</tr>
<tr >

<td >10
</td>

<td >[radiansdict.values()](https://www.w3cschool.cn/python/att-dictionary-values.html)
以列表返回字典中的所有值
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT



