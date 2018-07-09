---
author: evo
comments: true
date: 2018-05-04 13:46:36+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-select%ef%bc%88%e9%80%89%e6%8b%a9%ef%bc%89-%e8%af%ad%e6%b3%95/
slug: sql-select%ef%bc%88%e9%80%89%e6%8b%a9%ef%bc%89-%e8%af%ad%e6%b3%95
title: SQL Select（选择） 语法
wordpress_id: 5206
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




SELECT 语法用于从数据库中选择数据。




返回的数据存储在结果表中，称为结果集。






* * *





## SQL SELECT 语法






    
    <code class="html hljs xml">SELECT column1, column2, ...
    FROM table_name;</code>





这里，column1，column2，...是要从中选择数据的表的字段名称。如果要选择表中可用的所有字段，请使用以下语法：

    
    <code class="html hljs xml">SELECT * FROM table_name;</code>






* * *





## 演示数据库


在本教程中，我们将使用众所周知的 Northwind 样本数据库。

下面是罗斯文示例数据库中“Customers”表的一个选择：
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





## SELECT Column 实例


下面的 SQL 语句从 "Customers" 表中选取 "CustomerName" 和 "City" 列：





## 实例




SELECT CustomerName, City FROM Customers;









* * *





## SELECT * 实例


下面的 SQL 语句从 "Customers" 表中选取所有列：





## 实例




SELECT * FROM Customers;









* * *





## 结果集中的导航


大多数数据库软件系统都允许使用编程函数在结果集中进行导航，例如：Move-To-First-Record、Get-Record-Content、Move-To-Next-Record 等等。

本教程中不包括与这些编程函数类似的功能。要了解如何通过函数调用访问数据，请访问我们的 [ADO 教程](https://www.w3cschool.cn/ado/ado-tutorial.html) 或者 [PHP 教程](https://www.w3cschool.cn/php/php-tutorial.html)。























* * *





# COMMENT



