---
author: evo
comments: true
date: 2018-05-04 13:43:21+00:00
layout: post
link: http://106.15.37.116/2018/05/04/sql-%e7%ae%80%e4%bb%8b/
slug: sql-%e7%ae%80%e4%bb%8b
title: SQL 简介
wordpress_id: 5190
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





## 手册简介:


SQL 是用于访问和处理数据库的标准的计算机语言。 在本教程中，您将学到如何使用 SQL 访问和处理数据系统中的数据，这类数据库包括：Oracle, Sybase, SQL Server, DB2, Access 等等。





https://www.w3cschool.cn/sql



# SQL教程




## 多端阅读《SQL教程》:






  * **在PC/MAC上查看：**下载w3cschool客户端，进入客户端后通过搜索当前教程手册的名称并下载，就可以查看当前离线教程文档。[下载SQL教程离线版客户端](https://www.w3cschool.cn/download/sql.html)


  * **在手机APP上查看：**请从各大安卓应用商店、苹果App Store搜索并下载w3cschool手机客户端，在App中搜索当前教程手册的名称查看。[下载w3cschool手机App端](https://www.w3cschool.cn/download/)


  * **在手机上查看文档：**[https://m.w3cschool.cn/sql/](https://m.w3cschool.cn/sql/)







## 手册简介:


SQL 是用于访问和处理数据库的标准的计算机语言。 在本教程中，您将学到如何使用 SQL 访问和处理数据系统中的数据，这类数据库包括：Oracle, Sybase, SQL Server, DB2, Access 等等。









## __手册说明:







## **SQL数据库是什么?**


结构化查询语言(Structured Query Language)简称SQL(发音：/ˈes kjuː ˈel/ "S-Q-L")，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统；同时也是数据库脚本文件的扩展名。

结构化查询语言是高级的非过程化编程语言，允许用户在高层数据结构上工作。它不要求用户指定对数据的存放方法，也不需要用户了解具体的数据存放方式，所以具有完全不同底层结构的不同数据库系统, 可以使用相同的结构化查询语言作为数据输入与管理的接口。结构化查询语言语句可以嵌套，这使它具有极大的灵活性和强大的功能。

如果您想要更快、更系统地学会SQL，您最好采用边学边练（[**SQL微课**](https://www.w3cschool.cn/minicourse/play/sqlcourse)）的学习模式。

学完本教程，推荐您进行实战练习：[**点击进入实战**](https://www.w3cschool.cn/codecamp/list?pename=coding_interview_data_structure_questions_camp)




## **SQL语句结构**


结构化查询语言包含6个部分：

**一：数据查询语言（DQL:Data Query Language）：**
其语句，也称为“数据检索语句”，用以从表中获得数据，确定数据怎样在应用程序给出。保留字SELECT是DQL（也是所有SQL）用得最多的动词，其他DQL常用的保留字有WHERE，ORDER BY，GROUP BY和HAVING。这些DQL保留字常与其他类型的SQL语句一起使用。

**二：数据操作语言（DML：Data Manipulation Language）：**
其语句包括动词INSERT，UPDATE和DELETE。它们分别用于添加，修改和删除表中的行。也称为动作查询语言。

**三：事务处理语言（TPL）：**
它的语句能确保被DML语句影响的表的所有行及时得以更新。TPL语句包括BEGIN TRANSACTION，COMMIT和ROLLBACK。

**四：数据控制语言（DCL）：**
它的语句通过GRANT或REVOKE获得许可，确定单个用户和用户组对数据库对象的访问。某些RDBMS可用GRANT或REVOKE控制对表单个列的访问。

**五：数据定义语言（DDL）：**
其语句包括动词CREATE和DROP。在数据库中创建新表或删除表（CREAT TABLE 或 DROP TABLE）；为表加入索引等。DDL包括许多与人数据库目录中获得数据有关的保留字。它也是动作查询的一部分。

**六：指针控制语言（CCL）：**
它的语句，像DECLARE CURSOR，FETCH INTO和UPDATE WHERE CURRENT用于对一个或多个表单独行的操作。


## **SQL数据类型**


简要描述一下结构化查询语言中的五种数据类型：字符型，文本型，数值型，逻辑型和日期型。

**字符型(VARCHARVS CHAR)**
VARCHAR型和CHAR型数据的这个差别是细微的，但是非常重要。他们都是用来储存字符串长度小于255的字符。

假如你向一个长度为四十个字符的VARCHAR型字段中输入数据Bill Gates。当你以后从这个字段中取出此数据时，你取出的数据其长度为十个字符——字符串Bill Gates的长度。假如你把字符串输入一个长度为四十个字符的CHAR型字段中，那么当你取出数据时，所取出的数据长度将是四十个字符。字符串的后面会被附加多余的空格。

当你建立自己的站点时，你会发现使用VARCHAR型字段要比CHAR型字段方便的多。使用VARCHAR型字段时，你不需要为剪掉你数据中多余的空格而操心。

VARCHAR型字段的另一个突出的好处是它可以比CHAR型字段占用更少的内存和硬盘空间。当你的数据库很大时，这种内存和磁盘空间的节省会变得非常重要。

**文本型(TEXT)**
使用文本型数据，你可以存放超过二十亿个字符的字符串。当你需要存储大串的字符时，应该使用文本型数据。

注意文本型数据没有长度，而上一节中所讲的字符型数据是有长度的。一个文本型字段中的数据通常要么为空，要么很大。

当你从HTML FORM的多行文本编辑框（TEXTAREA）中收集数据时，你应该把收集的信息存储于文本型字段中。但是，无论何时，只要你能避免使用文本型字段，你就应该不使用它。文本型字段既大且慢，滥用文本型字段会使服务器速度变慢。文本型字段还会吃掉大量的磁盘空间。

一旦你向文本型字段中输入了任何数据（甚至是空值），就会有2K的空间被自动分配给该数据。除非删除该记录，否则你无法收回这部分存储空间。

**数值型(整数INT 、小数NUMERIC、钱数MONEY)**
INT 对比 SMALLINT 对比 TINYINT
通常，为了节省空间，应该尽可能的使用最小的整型数据。一个TINYINT型数据只占用一个字节；一个INT型数据占用四个字节。这看起来似乎差别不大，但是在比较大的表中，字节数的增长是很快的。另一方面，一旦你已经创建了一个字段，要修改它是很困难的。因此，为安全起见，你应该预测一下，一个字段所需要存储的数值最大有可能是多大，然后选择适当的数据类型。

**NUMERIC**
为了能对字段所存放的数据有更多的控制，你可以使用NUMERIC型数据来同时表示一个数的整数部分和小数部分。NUMERIC型数据使你能表示非常大的数——比INT型数据要大得多。一个NUMERIC型字段可以存储从-10^38到10^38范围内的数。NUMERIC型数据还使你能表示有小数部分的数。例如，你可以在NUMERIC型字段中存储小数3.14。

**MONEY 对比 SMALLMONEY**
你可以使用 INT型或NUMERIC型数据来存储钱数。但是，专门有另外两种数据类型用于此目的。如果你希望你的网点能挣很多钱，你可以使用MONEY型数据。如果你的野心不大，你可以使用SMALLMONEY型数据。MONEY型数据可以存储从-922,337,203,685,477.5808到922,337,203,685,477.5807的钱数。如果你需要存储比这还大的金额，你可以使用NUMERIC型数据。

SMALLMONEY型数据只能存储从-214,748.3648到214,748.3647 的钱数。同样，如果可以的话，你应该用SMALLMONEY型来代替MONEY型数据，以节省空间。

**逻辑型(BIT)**
如果你使用复选框（CHECKBOX）从网页中搜集信息，你可以把此信息存储在BIT型字段中。BIT型字段只能取两个值：0或1。

**注意**:在你创建好一个表之后，你不能向表中添加 BIT型字段。如果你打算在一个表中包含BIT型字段，你必须在创建表时完成。
**日期型(DATETIME 对比 SMALLDATETIME)**

一个 DATETIME型的字段可以存储的日期范围是从1753年1月1日第一毫秒到9999年12月31日最后一毫秒。

如果你不需要覆盖这么大范围的日期和时间，你可以使用SMALLDATETIME型数据。它与DATETIME型数据同样使用，只不过它能表示的日期和时间范围比DATETIME型数据小，而且不如DATETIME型数据精确。一个SMALLDATETIME型的字段能够存储从1900年1月1日到2079年6月6日的日期，它只能精确到秒。

DATETIME型字段在你输入日期和时间之前并不包含实际的数据，认识这一点是重要的。


## **附加资料**


SQL API文档:[http://www.w3cschool.cn/sql/82rg1ozi.html](https://www.w3cschool.cn/sql/82rg1ozi.html)SQL 教程:[http://www.w3cschool.cn/sql/y93wmfol.html](https://www.w3cschool.cn/sql/y93wmfol.html)










SQL（结构化查询语言）是用于访问和操作数据库中的数据的标准数据库编程语言。




为了处理数据库和数据库相关的编程，程序员需要有一些介质，或者可以说接口来详细说明一组命令或代码来处理数据库或访问数据库的数据。在本章中，将简要介绍在学习SQL的过程中您将学习的术语。






* * *





## 你会从SQL中学到什么？


SQL为结构化查询语言提供了独特的学习和数据库处理技术，并将帮助您更好地控制SQL查询并有效处理这些代码。由于SQL帮助您包括数据库创建，数据库或表删除，获取行数据和修改这些数据等，并行SQL使得事情自动和平滑，最终用户可以轻松访问和处理该应用程序的数据。



* * *





## SQL 是什么？






  * SQL 发音为“sequel”。


  * SQL 指结构化查询语言，全称是 Structured Query Language（是最初由IBM开发）。


  * SQL 是关系数据库系统的标准语言。


  * SQL 是一种 ANSI（American National Standards Institute 美国国家标准化组织）标准的计算机语言。





* * *





## SQL 能做什么？






  * SQL可以创建新的数据库及其对象（表，索引，视图，存储过程，函数和触发器）。


  * SQL可以修改现有数据库的结构。


  * SQL可以从数据库中删除（删除）对象。


  * SQL可以TRUNCATE（删除）表中的所有记录。


  * SQL可以对数据字典进行COMMENT。


  * SQL可以RENAME一个对象。


  * SQL可以从数据库中选择（检索）数据。


  * SQL可以将数据插入到表中。


  * SQL可以更新表中的现有数据。


  * SQL可以从数据库表中删除记录。


  * SQL可以在数据库中设置用户的GRANT和REVOKE权限。





* * *





## SQL 的历史






  * SQL由IBM的Donald D. Chamberlin和Raymond F. Boyce于1970年开发。


  * 首先，开发版本最初被称为SEQUEL（结构化英语查询语言）。


  * 关系软件于1979年发布了第一个叫做System / R的商业产品。


  * 由于商标冲突问题，SEQUEL首字母缩略词后来更改为SQL。


  * 后来IBM基于System / R的原型开始在SQL上开发商业产品。





* * *





## SQL 是一种标准 - 但是...


虽然 SQL 是一门 ANSI（American National Standards Institute 美国国家标准化组织）标准的计算机语言，但是仍然存在着多种不同版本的 SQL 语言。

然而，为了与 ANSI 标准相兼容，它们必须以相似的方式共同地来支持一些主要的命令（比如 SELECT、UPDATE、DELETE、INSERT、WHERE 等等）。

**注释：**除SQL标准之外，大多数SQL数据库程序还具有自己的专有扩展名！





## 在您的网站中使用 SQL


要创建一个显示数据库中数据的网站，您需要：




  * 一个RDBMS数据库程序（即MS Access，SQL Server，MySQL）。


  * 使用服务器端脚本语言，如PHP或ASP。


  * 使用SQL来获取所需的数据。


  * 使用HTML / CSS来设置页面的样式





* * *





## RDBMS


RDBMS 指关系型数据库管理系统，全称 Relational Database Management System。

RDBMS 是 SQL 的基础，同样也是所有现代数据库系统的基础，比如 MS SQL Server、IBM DB2、Oracle、MySQL 以及 Microsoft Access。

RDBMS 中的数据存储在被称为表的数据库对象中。

表是相关的数据项的集合，它由列和行组成。

**代码示例：**


    SELECT * FROM Customers;




每个表都被分解成称为字段的更小的实体。Customers表中的字段由CustomerID，CustomerName，ContactName，Address，City，PostalCode和Country组成。字段是表中的一列，用于维护表中每条记录的特定信息。

记录（也称为行）是表中存在的每个单独条目。例如，在上面的Customers表中有91条记录。记录是表中的横向实体。

列是表中的垂直实体，其包含与表中的特定字段相关联的所有信息。

















* * *





# COMMENT
