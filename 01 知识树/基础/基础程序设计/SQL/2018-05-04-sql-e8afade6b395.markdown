---
author: evo
comments: true
date: 2018-05-04 13:46:02+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-%e8%af%ad%e6%b3%95/
slug: sql-%e8%af%ad%e6%b3%95
title: SQL 语法
wordpress_id: 5191
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





## SQ 语法规则





 	
  * SQL语句总是以关键字开始。

 	
  * SQL语句以分号结尾。

 	
  * SQL不区分大小写，意味着更新与UPDATE相同





* * *





## 数据库表


数据库通常包含一个或多个表。每个表都用一个名称标识（例如，"Customers"或"Orders"）。该表包含带有数据（行）的记录。
在本教程中，我们将使用著名的Northwind示例数据库（包括MSAccess和MSSQLServer）。

下面是选自 "Customers" 表的数据：
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
上面的表包含五条记录（每一条对应一个客户）和七个列（CustomerID、CustomerName、ContactName、Address、City、PostalCode 和 Country）。



* * *





## SQL 语句


您需要在数据库上执行的大部分操作都是使用SQL语句完成的。

以下SQL语句选择“Customers”表中的所有记录：





## 实例




SELECT * FROM Customers;





在本教程中，我们将向您解释各种不同的SQL语句。



* * *





## 请记住...





 	
  * SQL 对大小写不敏感：SELECT 与 select 是相同的。

 	
  * 在本教程中，我们将以大写形式编写所有SQL关键字。





* * *





## SQL 语句后面的分号？





 	
  * 一些数据库系统需要在每个SQL语句的末尾使用分号。

 	
  * 分号是分离数据库系统中每个SQL语句的标准方法，这样您就可以在对服务器的同一请求中执行多个SQL语句。

 	
  * 在本教程中，我们将在每个SQL语句的末尾使用分号。





* * *





## 一些最重要的 SQL 命令





 	
  * **SELECT** - 从数据库中提取数据

 	
  * **UPDATE** - 更新数据库中的数据

 	
  * **DELETE** - 从数据库中删除数据

 	
  * **INSERT INTO** - 向数据库中插入新数据

 	
  * **CREATE DATABASE** - 创建新数据库

 	
  * **ALTER DATABASE** - 修改数据库

 	
  * **CREATE TABLE** - 创建新表

 	
  * **ALTER TABLE** - 变更（改变）数据库表

 	
  * **DROP TABLE** - 删除表

 	
  * **CREATE INDEX** - 创建索引（搜索键）

 	
  * **DROP INDEX** - 删除索引


**SELECT语句**

句法：

    
    <code class="html hljs xml">SELECT column_name(s) FROM table_name</code>




### SELECT语句和WHERE子句


句法：

    
    <code class="html hljs xml">SELECT [*] FROM [TableName] WHERE [condition1]</code>




### SELECT语句与WHERE和/或子句


句法：

    
    <code class="html hljs xml">SELECT [*] FROM [TableName] WHERE [condition1] [AND [OR]] [condition2]...</code>




### SELECT语句与ORDER BY


句法：

    
    <code class="html hljs xml">SELECT column_name()
    FROM table_name
    ORDER BY column_name() ASC or DESC</code>




### INSERT INTO语句


句法：

    
    <code class="html hljs xml">INSERT INTO table_name (column, column1, column2, column3, ...)
    VALUES (value, value1, value2, value3 ...)</code>




### 更新声明


句法：

    
    <code class="html hljs xml">UPDATE table_name
    SET column=value, column1=value1,...
    WHERE someColumn=someValue</code>




### DELETE语句


句法：

    
    <code class="html hljs xml">DELETE FROM tableName
    WHERE someColumn = someValue</code>
























* * *





# COMMENT



