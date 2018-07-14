---
author: evo
comments: true
date: 2018-05-04 13:56:23+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-and-or-and-not%ef%bc%88%e4%b8%8e%ef%bc%8c%e6%88%96%e4%b8%8d%e6%98%af%e8%bf%90%e7%ae%97%e7%ac%a6%ef%bc%89/
slug: sql-and-or-and-not%ef%bc%88%e4%b8%8e%ef%bc%8c%e6%88%96%e4%b8%8d%e6%98%af%e8%bf%90%e7%ae%97%e7%ac%a6%ef%bc%89
title: SQL AND, OR and NOT（与，或不是运算符）
wordpress_id: 5209
categories:
- 基础工具使用
tags:
- SQL
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [SQL教程](https://www.w3cschool.cn/sql/)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *




AND&OR运算符用于根据一个以上的条件过滤记录。






* * *





## SQL AND & OR 运算符


WHERE子句可以与AND，OR和NOT运算符结合使用。

AND和OR运算符用于根据多个条件筛选记录：



 	
  * 如果由AND分隔的所有条件为TRUE，则AND运算符显示记录。

 	
  * 如果由OR分隔的任何条件为真，则OR运算符显示记录。


如果条件不为真，则NOT运算符显示记录。


### AND语法



    
    SELECT column1, column2, ...
    FROM table_name
    WHERE condition1 AND condition2 AND condition3 ...;




### OR语法



    
    SELECT column1, column2, ...
    FROM table_name
    WHERE condition1 OR condition2 OR condition3 ...;




### NOT语法



    
    SELECT column1, column2, ...
    FROM table_name
    WHERE NOT condition;





* * *





## 演示数据库


在本教程中，我们将使用著名的Northwind示例数据库。

以下是"Customers"表中的数据：
<table class="reference notranslate " >
<tbody >
<tr >
CustomerID
CustomerName
ContactName
Address
City
PostalCode
Country
</tr>
<tr >

<td >1
</td>

<td >Alfreds Futterkiste
</td>

<td >Maria Anders
</td>

<td >Obere Str. 57
</td>

<td >Berlin
</td>

<td >12209
</td>

<td >Germany
</td>
</tr>
<tr >

<td >2
</td>

<td >Ana Trujillo Emparedados y helados
</td>

<td >Ana Trujillo
</td>

<td >Avda. de la Constitución 2222
</td>

<td >México D.F.
</td>

<td >05021
</td>

<td >Mexico
</td>
</tr>
<tr >

<td >3
</td>

<td >Antonio Moreno Taquería
</td>

<td >Antonio Moreno
</td>

<td >Mataderos 2312
</td>

<td >México D.F.
</td>

<td >05023
</td>

<td >Mexico
</td>
</tr>
<tr >

<td >4
</td>

<td >Around the Horn
</td>

<td >Thomas Hardy
</td>

<td >120 Hanover Sq.
</td>

<td >London
</td>

<td >WA1 1DP
</td>

<td >UK
</td>
</tr>
<tr >

<td >5
</td>

<td >Berglunds snabbköp
</td>

<td >Christina Berglund
</td>

<td >Berguvsvägen 8
</td>

<td >Luleå
</td>

<td >S-958 22
</td>

<td >Sweden
</td>
</tr>
</tbody>
</table>




* * *





## AND 运算符实例


以下SQL语句从 "Customers" 表中选择其国家为 "Germany" 、其城市为"Berlin" 的所有客户：





## 实例






    
    SELECT * FROM Customers 
    WHERE Country='Germany' 
    AND City='Berlin';














* * *





## OR 运算符实例


以下SQL语句选择城市为“Berlin”或“München”的“Customers”的所有字段：





## 实例




SELECT * FROM Customers
WHERE City='Berlin' OR City='München';







## NOT 运算符实例


以下SQL语句选择国家不是 "Germany"的"Customers"的所有字段：

    
    SELECT * FROM Customers
    WHERE NOT Country='Germany';





* * *





## 结合 AND & OR


您还可以组合AND和OR（使用括号来组成成复杂的表达式）。

以下SQL语句从国家 "Germany" 且城市为"Berlin" 或"München"的"Customers" 表中选择所有客户：





## 实例




SELECT * FROM Customers
WHERE Country='Germany'
AND (City='Berlin' OR City='München');







## 结合AND，OR和NOT


你也可以结合AND，OR和NOT运算符。

以下SQL语句选择国家是“德国”的“客户”的所有字段，城市必须是“柏林”或“慕尼黑”（用括号形成复杂表达式）：

**代码示例：**

    
    SELECT * FROM Customers
    WHERE Country='Germany' AND (City='Berlin' OR City='München');


以下SQL语句选择来自"Customers" 的国家不是 "Germany" 且不是 "USA"的所有字段：

**代码示例：**

    
    SELECT * FROM Customers
    WHERE NOT Country='Germany' AND NOT Country='USA';






















* * *





# COMMENT



