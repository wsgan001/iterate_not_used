---
author: evo
comments: true
date: 2018-05-04 13:57:26+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-order-by-keyword%ef%bc%88%e6%8c%89%e5%85%b3%e9%94%ae%e5%ad%97%e6%8e%92%e5%ba%8f%ef%bc%89/
slug: sql-order-by-keyword%ef%bc%88%e6%8c%89%e5%85%b3%e9%94%ae%e5%ad%97%e6%8e%92%e5%ba%8f%ef%bc%89
title: SQL ORDER BY Keyword（按关键字排序）
wordpress_id: 5210
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










ORDER BY 关键字用于对结果集进行排序。



* * *





## SQL ORDER BY 关键字


ORDER BY 关键字用于按升序或降序对结果集进行排序。

ORDER BY 关键字默认情况下按升序排序记录。

如果需要按降序对记录进行排序，可以使用DESC关键字。


### SQL ORDER BY 语法



    
    SELECT column1, column2, ...
    FROM table_name
    ORDER BY column1, column2, ... ASC|DESC;





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





## ORDER BY 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列排序：





## 实例




SELECT * FROM Customers
ORDER BY Country;








* * *





## ORDER BY DESC 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列降序排序：





## 实例




SELECT * FROM Customers
ORDER BY Country DESC;








* * *





## ORDER BY 多列 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 和 "CustomerName" 列排序：





## 实例




SELECT * FROM Customers
ORDER BY Country, CustomerName;













## ORDER BY 多列 实例2


以下SQL语句从"Customers" 表中选择所有客户，按 "Country" 升序排列，并按 "CustomerName" 列降序排列：

    
    SELECT * FROM Customers
    ORDER BY Country ASC, CustomerName DESC;
























* * *





# COMMENT



