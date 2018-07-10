---
author: evo
comments: true
date: 2018-05-04 13:53:47+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-where-clause%ef%bc%88%e6%9f%a5%e8%af%a2%e5%ad%90%e5%8f%a5%ef%bc%89/
slug: sql-where-clause%ef%bc%88%e6%9f%a5%e8%af%a2%e5%ad%90%e5%8f%a5%ef%bc%89
title: SQL WHERE Clause（查询子句）
wordpress_id: 5208
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











WHERE 子句用于过滤记录。






* * *





## SQL WHERE 子句


WHERE子句用于提取满足指定标准的记录。


### SQL WHERE 语法



    
    SELECT column1, column2, ...
    FROM table_name
    WHERE condition;


**注意：** WHERE子句不仅用于SELECT语法，还用于UPDATE，DELETE语法等！



* * *





## 演示数据库


在本教程中，我们将使用著名的Northwind示例数据库。
以下是 "Customers" 表中的数据：
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





## WHERE 子句实例


以下SQL语句从"Customers"表中选择其国家为"Mexico"的所有客户：





## 实例




SELECT * FROM Customers
WHERE Country='Mexico';








* * *





## 文本字段与数值字段


SQL在文本值周围使用单引号（大多数数据库系统也接受双引号）。

如果是数值字段，则不要使用引号。





## 实例




SELECT * FROM Customers
WHERE CustomerID=1;








* * *





## WHERE 子句中的运算符


WHERE子句中可以使用以下运算符：
<table class="reference notranslate" >
<tbody >
<tr >
运算符
描述
</tr>
<tr >

<td >=
</td>

<td >等于
</td>
</tr>
<tr >

<td ><>
</td>

<td >不等于。 **注意**：在某些版本的SQL中，这个操作符可能写成！=
</td>
</tr>
<tr >

<td >>
</td>

<td >大于
</td>
</tr>
<tr >

<td ><
</td>

<td >小于
</td>
</tr>
<tr >

<td >>=
</td>

<td >大于等于
</td>
</tr>
<tr >

<td ><=
</td>

<td >小于等于
</td>
</tr>
<tr >

<td >BETWEEN
</td>

<td >在某个范围内
</td>
</tr>
<tr >

<td >LIKE
</td>

<td >搜索某种模式
</td>
</tr>
<tr >

<td >IN
</td>

<td >为列指定多个可能的值
</td>
</tr>
</tbody>
</table>




























* * *





# COMMENT



