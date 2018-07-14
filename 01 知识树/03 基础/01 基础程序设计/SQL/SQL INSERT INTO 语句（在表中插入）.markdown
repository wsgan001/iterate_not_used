---
author: evo
comments: true
date: 2018-05-04 14:10:24+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-insert-into-%e8%af%ad%e5%8f%a5%ef%bc%88%e5%9c%a8%e8%a1%a8%e4%b8%ad%e6%8f%92%e5%85%a5%ef%bc%89/
slug: sql-insert-into-%e8%af%ad%e5%8f%a5%ef%bc%88%e5%9c%a8%e8%a1%a8%e4%b8%ad%e6%8f%92%e5%85%a5%ef%bc%89
title: SQL INSERT INTO 语句（在表中插入）
wordpress_id: 5218
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




INSERT INTO 语句用于向表中插入新记录。






* * *





## SQL INSERT INTO 语句


INSERT INTO 语句用于向表中插入新记录。


### SQL INSERT INTO 语法


INSERT INTO 语句可以用两种形式编写。
第一个表单没有指定要插入数据的列的名称，只提供要插入的值：

    
    INSERT INTO table_name (column1, column2, column3, ...)
    VALUES (value1, value2, value3, ...);


如果要为表中的所有列添加值，则不需要在SQL查询中指定列名称。但是，请确保值的顺序与表中的列顺序相同。INSERT INTO语法如下所示：

    
    INSERT INTO table_name
    VALUES (value1, value2, value3, ...);






* * *





## 演示数据库


在本教程中，我们将使用著名的 Northwind 示例数据库。

以下是"Customers" 表中的数据：
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

<td >87
</td>

<td >Wartian Herkku
</td>

<td >Pirkko Koskitalo
</td>

<td >Torikatu 38
</td>

<td >Oulu
</td>

<td >90110
</td>

<td >Finland
</td>
</tr>
<tr >

<td >88
</td>

<td >Wellington Importadora
</td>

<td >Paula Parente
</td>

<td >Rua do Mercado, 12
</td>

<td >Resende
</td>

<td >08737-363
</td>

<td >Brazil
</td>
</tr>
<tr >

<td >89
</td>

<td >White Clover Markets
</td>

<td >Karl Jablonski
</td>

<td >305 - 14th Ave. S. Suite 3B
</td>

<td >Seattle
</td>

<td >98128
</td>

<td >USA
</td>
</tr>
<tr >

<td >90
</td>

<td >Wilman Kala
</td>

<td >Matti Karttunen
</td>

<td >Keskuskatu 45
</td>

<td >Helsinki
</td>

<td >21240
</td>

<td >Finland
</td>
</tr>
<tr >

<td >91
</td>

<td >Wolski
</td>

<td >Zbyszek
</td>

<td >ul. Filtrowa 68
</td>

<td >Walla
</td>

<td >01-012
</td>

<td >Poland
</td>
</tr>
</tbody>
</table>




* * *





## INSERT INTO 实例


假设我们想在"Customers"表中插入一个新行。

我们可以使用以下SQL语句：





## 实例




INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal','Tom B. Erichsen','Skagen 21','Stavanger','4006','Norway');





现在，选自 "Customers" 表的数据如下所示：
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

<td >87
</td>

<td >Wartian Herkku
</td>

<td >Pirkko Koskitalo
</td>

<td >Torikatu 38
</td>

<td >Oulu
</td>

<td >90110
</td>

<td >Finland
</td>
</tr>
<tr >

<td >88
</td>

<td >Wellington Importadora
</td>

<td >Paula Parente
</td>

<td >Rua do Mercado, 12
</td>

<td >Resende
</td>

<td >08737-363
</td>

<td >Brazil
</td>
</tr>
<tr >

<td >89
</td>

<td >White Clover Markets
</td>

<td >Karl Jablonski
</td>

<td >305 - 14th Ave. S. Suite 3B
</td>

<td >Seattle
</td>

<td >98128
</td>

<td >USA
</td>
</tr>
<tr >

<td >90
</td>

<td >Wilman Kala
</td>

<td >Matti Karttunen
</td>

<td >Keskuskatu 45
</td>

<td >Helsinki
</td>

<td >21240
</td>

<td >Finland
</td>
</tr>
<tr >

<td >91
</td>

<td >Wolski
</td>

<td >Zbyszek
</td>

<td >ul. Filtrowa 68
</td>

<td >Walla
</td>

<td >01-012
</td>

<td >Poland
</td>
</tr>
<tr >

<td >92
</td>

<td >Cardinal
</td>

<td >Tom B. Erichsen
</td>

<td >Skagen 21
</td>

<td >Stavanger
</td>

<td >4006
</td>

<td >Norway
</td>
</tr>
</tbody>
</table>

<table class="lamp" >
<tbody >
<tr >
![lamp](https://img.w3cschool.cn/attachments/image/20150510/lamp.jpg)

<td >**注意到了吗？****我们没有将任何号码插入 CustomerID 字段。**
CustomerID列是一个[自动递增](https://www.w3cschool.cn/sql/2phntfpq.html)字段，在将新记录插入到表中时自动生成。
</td>
</tr>
</tbody>
</table>




* * *





## 仅在指定的列中插入数据


我们还可以只在指定的列中插入数据。

以下SQL语句插入一个新行，但只在“CustomerName”、“City”和“Countryn”列中插入数据（CustomerID字段将自动更新）：





## 实例




INSERT INTO Customers (CustomerName, City, Country)
VALUES ('Cardinal', 'Stavanger', 'Norway');





现在，选自 "Customers" 表的数据如下所示：
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

<td >87
</td>

<td >Wartian Herkku
</td>

<td >Pirkko Koskitalo
</td>

<td >Torikatu 38
</td>

<td >Oulu
</td>

<td >90110
</td>

<td >Finland
</td>
</tr>
<tr >

<td >88
</td>

<td >Wellington Importadora
</td>

<td >Paula Parente
</td>

<td >Rua do Mercado, 12
</td>

<td >Resende
</td>

<td >08737-363
</td>

<td >Brazil
</td>
</tr>
<tr >

<td >89
</td>

<td >White Clover Markets
</td>

<td >Karl Jablonski
</td>

<td >305 - 14th Ave. S. Suite 3B
</td>

<td >Seattle
</td>

<td >98128
</td>

<td >USA
</td>
</tr>
<tr >

<td >90
</td>

<td >Wilman Kala
</td>

<td >Matti Karttunen
</td>

<td >Keskuskatu 45
</td>

<td >Helsinki
</td>

<td >21240
</td>

<td >Finland
</td>
</tr>
<tr >

<td >91
</td>

<td >Wolski
</td>

<td >Zbyszek
</td>

<td >ul. Filtrowa 68
</td>

<td >Walla
</td>

<td >01-012
</td>

<td >Poland
</td>
</tr>
<tr >

<td >92
</td>

<td >Cardinal
</td>

<td >null
</td>

<td >null
</td>

<td >Stavanger
</td>

<td >null
</td>

<td >Norway
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT



