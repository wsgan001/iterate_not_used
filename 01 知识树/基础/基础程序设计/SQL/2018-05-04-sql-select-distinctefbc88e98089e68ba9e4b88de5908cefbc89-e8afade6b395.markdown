---
author: evo
comments: true
date: 2018-05-04 13:52:57+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-select-distinct%ef%bc%88%e9%80%89%e6%8b%a9%e4%b8%8d%e5%90%8c%ef%bc%89-%e8%af%ad%e6%b3%95/
slug: sql-select-distinct%ef%bc%88%e9%80%89%e6%8b%a9%e4%b8%8d%e5%90%8c%ef%bc%89-%e8%af%ad%e6%b3%95
title: SQL SELECT DISTINCT（选择不同） 语法
wordpress_id: 5207
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



SELECT DISTINCT语法用于仅返回不同的（different）值。

在一张表内，一列通常包含许多重复的值; 有时你只想列出不同的（different）值。

SELECT DISTINCT语句用于仅返回不同的（different）值。



* * *





## SQL SELECT DISTINCT 语法



    
    <code class="html hljs xml">SELECT DISTINCT column1, column2, ...
    FROM table_name;</code>






* * *





## 演示数据库


在本教程中，我们将使用著名的的 Northwind 样本数据库。

下面是罗斯文示例数据库中 "Customers" 表的数据：
<table class="reference notranslate  " >
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





## SELECT实例


以下SQL语句从“Customers”表中的“Country”列中选择所有（和重复）值：


**代码示例：**



    
    SELECT Country FROM Customers;


现在，让我们在上面的SELECT语法中使用DISTINCT关键字并查看结果。


## SELECT DISTINCT 实例


以下SQL语句仅从"Customers" 表中的 "Country" 列中选择DISTINCT值：





## 实例




SELECT DISTINCT Country FROM Customers;





以下SQL语句列出了不同（distinct）客户国家的数量：

**代码示例：**

    
    SELECT COUNT(DISTINCT Country) FROM Customers;


**注意：上述示例在Firefox和Microsoft Edge中不起作用！**

由于在Microsoft Access数据库中不支持COUNT(DISTINCT _column_name_)。在我们的示例中Firefox和Microsoft Edge使用Microsoft Access。























* * *





# COMMENT



