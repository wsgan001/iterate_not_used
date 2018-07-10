---
author: evo
comments: true
date: 2018-05-04 14:15:31+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-delete-%e8%af%ad%e5%8f%a5%ef%bc%88%e5%88%a0%e9%99%a4%e8%a1%a8%e4%b8%ad%e7%9a%84%e8%ae%b0%e5%bd%95%ef%bc%89/
slug: sql-delete-%e8%af%ad%e5%8f%a5%ef%bc%88%e5%88%a0%e9%99%a4%e8%a1%a8%e4%b8%ad%e7%9a%84%e8%ae%b0%e5%bd%95%ef%bc%89
title: SQL Delete 语句（删除表中的记录）
wordpress_id: 5221
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




DELETE语句用于删除表中现有记录。






* * *





## SQL DELETE 语句


DELETE 语句用于删除表中的行。


### SQL DELETE 语法



    
    DELETE FROM table_name
    WHERE condition;



<table class="lamp" >
<tbody >
<tr >
![lamp](https://img.w3cschool.cn/attachments/image/20150510/lamp.jpg)

<td >**请注意
****删除表格中的记录时要小心！****
注意SQL DELETE 语句中的 WHERE 子句！**
WHERE子句指定需要删除哪些记录。如果省略了WHERE子句，表中所有记录都将被删除！
</td>
</tr>
</tbody>
</table>




* * *





## 演示数据库


在本教程中，我们将使用著名的Northwind示例数据库。

以下是 "Customers" 表中的数据：
<table class="reference notranslate   " >
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





## SQL DELETE 实例


假设我们想从"Customers" 表中删除客户“Alfreds Futterkiste”。

我们使用以下SQL语句：





## 实例




DELETE FROM Customers
WHERE CustomerName='Alfreds Futterkiste';





现在，"Customers" 表如下所示：
<table class="reference notranslate   " >
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





## 删除所有数据


您可以删除表中的所有行，而不需要删除该表。这意味着表的结构、属性和索引将保持不变：

    
    DELETE FROM table_name;


**或者**

    
    DELETE * FROM table_name;


**注意：**在没有备份的情况下，删除记录要格外小心！因为你删除了不能重复！























* * *





# COMMENT



