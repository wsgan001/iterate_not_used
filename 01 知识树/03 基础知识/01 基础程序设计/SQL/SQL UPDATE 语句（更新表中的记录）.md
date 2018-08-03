---
author: evo
comments: true
date: 2018-05-04 14:14:22+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-update-%e8%af%ad%e5%8f%a5%ef%bc%88%e6%9b%b4%e6%96%b0%e8%a1%a8%e4%b8%ad%e7%9a%84%e8%ae%b0%e5%bd%95%ef%bc%89/
slug: sql-update-%e8%af%ad%e5%8f%a5%ef%bc%88%e6%9b%b4%e6%96%b0%e8%a1%a8%e4%b8%ad%e7%9a%84%e8%ae%b0%e5%bd%95%ef%bc%89
title: SQL UPDATE 语句（更新表中的记录）
wordpress_id: 5220
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











UPDATE 语句用于更新表中的现有记录。






* * *





## SQL UPDATE 语句


UPDATE 语句用于更新表中已存在的记录。


### SQL UPDATE 语法




    UPDATE table_name
    SET column1 = value1, column2 = value2, ...
    WHERE condition;


**请注意
****更新表中的记录时要小心！****
要注意SQL UPDATE 语句中的 WHERE 子句！**
WHERE子句指定哪些记录需要更新。如果省略WHERE子句，所有记录都将更新！



* * *





## 演示数据库


在本教程中，我们将使用著名的Northwind示例数据库。

以下是 "Customers" 表中的数据：
<table class="reference notranslate" >
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





## SQL UPDATE 实例


以下SQL语句为第一个客户（CustomerID = 1）更新了“CustomerName”和“City”：





## 实例




UPDATE Customers
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
WHERE CustomerID = 1;





现在，选自 "Customers" 表的数据如下所示：
<table >
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

<td >Alfred Schmidt
</td>

<td >Obere Str. 57
</td>

<td >Frankfurt
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





## 更新多个记录


WHERE子句决定了将要更新的记录数量。

以下SQL语句将把国家/地区为"Mexico"的所有记录的联系人姓名更新为“Juan”：


    UPDATE Customers
    SET ContactName='Juan'
    WHERE Country='Mexico';


“Customers”表中的选择现在看起来像这样：
<table >
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

<td >Alfred Schmidt
</td>

<td >Obere Str. 57
</td>

<td >Frankfurt
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

<td >Juan
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

<td >Juan
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


## Update 警告！


更新记录时要小心。如果您省略WHERE子句，所有记录将被更新！


    UPDATE Customers
    SET ContactName='Juan';


"Customers" 表将如下所示：
<table >
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

<td >Juan
</td>

<td >Obere Str. 57
</td>

<td >Frankfurt
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

<td >Juan
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

<td >Juan
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

<td >Juan
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

<td >Juan
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





# COMMENT
