---
author: evo
comments: true
date: 2018-05-03 05:08:33+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-lists/
slug: python-lists
title: Python 列表(Lists)
wordpress_id: 4982
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





## Python 列表(Lists)


序列是Python中最基本的数据结构。序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。

Python有6个序列的内置类型，但最常见的是列表和元组。

序列都可以进行的操作包括索引，切片，加，乘，检查成员。

此外，Python已经内置确定序列的长度以及确定最大和最小的元素的方法。

列表是最常用的Python数据类型，它可以作为一个方括号内的逗号分隔值出现。

列表的数据项不需要具有相同的类型

创建一个列表，只要把逗号分隔的不同的数据项使用方括号括起来即可。如下所示：

    
    list1 = ['physics', 'chemistry', 1997, 2000];
    list2 = [1, 2, 3, 4, 5 ];
    list3 = ["a", "b", "c", "d"];
    


与字符串的索引一样，列表索引从0开始。列表可以进行截取、组合等。



* * *





## 访问列表中的值


使用下标索引来访问列表中的值，同样你也可以使用方括号的形式截取字符，如下所示：

    
    #!/usr/bin/python
    
    list1 = ['physics', 'chemistry', 1997, 2000];
    list2 = [1, 2, 3, 4, 5, 6, 7 ];
    
    print "list1[0]: ", list1[0]
    print "list2[1:5]: ", list2[1:5]
    


以上实例输出结果：

    
    list1[0]:  physics
    list2[1:5]:  [2, 3, 4, 5]
    





* * *





## 更新列表


你可以对列表的数据项进行修改或更新，你也可以使用append()方法来添加列表项，如下所示：

    
    #!/usr/bin/python
    
    list = ['physics', 'chemistry', 1997, 2000];
    
    print "Value available at index 2 : "
    print list[2];
    list[2] = 2001;
    print "New value available at index 2 : "
    print list[2];
    


**注意：**我们会在接下来的章节讨论append()方法的使用

以上实例输出结果：

    
    Value available at index 2 :
    1997
    New value available at index 2 :
    2001
    





* * *





## 删除列表元素


可以使用 del 语句来删除列表的的元素，如下实例：

    
    #!/usr/bin/python
    
    list1 = ['physics', 'chemistry', 1997, 2000];
    
    print list1;
    del list1[2];
    print "After deleting value at index 2 : "
    print list1;
    


以上实例输出结果：

    
    ['physics', 'chemistry', 1997, 2000]
    After deleting value at index 2 :
    ['physics', 'chemistry', 2000]
    


**注意：**我们会在接下来的章节讨论remove()方法的使用



* * *





## Python列表脚本操作符


列表对 + 和 * 的操作符与字符串相似。+ 号用于组合列表，* 号用于重复列表。

如下所示：
<table class="reference" >
<tbody >
<tr >
Python 表达式
结果
描述
</tr>
<tr >

<td >len([1, 2, 3])
</td>

<td >3
</td>

<td >长度
</td>
</tr>
<tr >

<td >[1, 2, 3] + [4, 5, 6]
</td>

<td >[1, 2, 3, 4, 5, 6]
</td>

<td >组合
</td>
</tr>
<tr >

<td >['Hi!'] * 4
</td>

<td >['Hi!', 'Hi!', 'Hi!', 'Hi!']
</td>

<td >重复
</td>
</tr>
<tr >

<td >3 in [1, 2, 3]
</td>

<td >True
</td>

<td >元素是否存在于列表中
</td>
</tr>
<tr >

<td >for x in [1, 2, 3]: print x,
</td>

<td >1 2 3
</td>

<td >迭代
</td>
</tr>
</tbody>
</table>



* * *





## Python列表截取


Python的列表截取与字符串操作类型，如下所示：

    
    L = ['spam', 'Spam', 'SPAM!']
    


操作：
<table class="reference" >
<tbody >
<tr >
Python 表达式
结果
描述
</tr>
<tr >

<td >L[2]
</td>

<td >'SPAM!'
</td>

<td >读取列表中第三个元素
</td>
</tr>
<tr >

<td >L[-2]
</td>

<td >'Spam'
</td>

<td >读取列表中倒数第二个元素
</td>
</tr>
<tr >

<td >L[1:]
</td>

<td >['Spam', 'SPAM!']
</td>

<td >从第二个元素开始截取列表
</td>
</tr>
</tbody>
</table>



* * *





## Python列表函数&方法


Python包含以下函数:
<table class="reference" >
<tbody >
<tr >
序号
函数
</tr>
<tr >

<td >1
</td>

<td >[cmp(list1, list2)](https://www.w3cschool.cn/python/att-list-cmp.html)
比较两个列表的元素
</td>
</tr>
<tr >

<td >2
</td>

<td >[len(list)](https://www.w3cschool.cn/python/att-list-len.html)
列表元素个数
</td>
</tr>
<tr >

<td >3
</td>

<td >[max(list)](https://www.w3cschool.cn/python/att-list-max.html)
返回列表元素最大值
</td>
</tr>
<tr >

<td >4
</td>

<td >[min(list)](https://www.w3cschool.cn/python/att-list-min.html)
返回列表元素最小值
</td>
</tr>
<tr >

<td >5
</td>

<td >[list(seq)](https://www.w3cschool.cn/python/att-list-list.html)
将元组转换为列表
</td>
</tr>
</tbody>
</table>
Python包含以下方法:
<table class="reference" >
<tbody >
<tr >
序号
方法
</tr>
<tr >

<td >1
</td>

<td >[list.append(obj)](https://www.w3cschool.cn/python/att-list-append.html)
在列表末尾添加新的对象
</td>
</tr>
<tr >

<td >2
</td>

<td >[list.count(obj)](https://www.w3cschool.cn/python/att-list-count.html)
统计某个元素在列表中出现的次数
</td>
</tr>
<tr >

<td >3
</td>

<td >[list.extend(seq)](https://www.w3cschool.cn/python/att-list-extend.html)
在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
</td>
</tr>
<tr >

<td >4
</td>

<td >[list.index(obj)](https://www.w3cschool.cn/python/att-list-index.html)
从列表中找出某个值第一个匹配项的索引位置
</td>
</tr>
<tr >

<td >5
</td>

<td >[list.insert(index, obj)](https://www.w3cschool.cn/python/att-list-insert.html)
将对象插入列表
</td>
</tr>
<tr >

<td >6
</td>

<td >[list.pop(obj=list[-1])](https://www.w3cschool.cn/python/att-list-pop.html)
移除列表中的一个元素（默认最后一个元素），并且返回该元素的值
</td>
</tr>
<tr >

<td >7
</td>

<td >[list.remove(obj)](https://www.w3cschool.cn/python/att-list-remove.html)
移除列表中某个值的第一个匹配项
</td>
</tr>
<tr >

<td >8
</td>

<td >[list.reverse()](https://www.w3cschool.cn/python/att-list-reverse.html)
反向列表中元素
</td>
</tr>
<tr >

<td >9
</td>

<td >[list.sort([func])](https://www.w3cschool.cn/python/att-list-sort.html)
对原列表进行排序
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT



