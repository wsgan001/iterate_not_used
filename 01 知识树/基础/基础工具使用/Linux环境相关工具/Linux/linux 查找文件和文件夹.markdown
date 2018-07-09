---
author: evo
comments: true
date: 2018-03-31 04:07:22+00:00
layout: post
link: http://106.15.37.116/2018/03/31/linux-find-file-or-folder/
slug: linux-find-file-or-folder
title: linux 查找文件和文件夹
wordpress_id: 2143
categories:
- 基础计算机
tags:
- linux
---

<!-- more -->


# 缘由：


linux里面经常会用到查找文件和文件夹，因为要修改的一些配置文件，只知道名字，不知道位置，因此必须要查找，还有这次我想创建一个twentytwelve_child的子主题，因此也必须要找到这个wp-content的文件夹和theme文件夹在哪里。


# 要点：




## **1.使用 find**


find是最常见和最强大的查找命令，你可以用它找到任何你想找的文件。

find的使用格式如下：

**$ find <指定目录> <指定条件> <指定动作>**



 	
  * <指定目录>： 所要搜索的目录及其所有子目录。默认为当前目录。

 	
  * <指定条件>： 所要搜索的文件的特征。

 	
  * <指定动作>： 对搜索结果进行特定的处理。


如果什么参数也不加，find默认搜索当前目录及其子目录，并且不过滤任何结果（也就是返回所有文件），将它们全都显示在屏幕上。

find的使用实例：

**$ find . -name 'my*'**

搜索当前目录（含子目录，以下同）中，所有文件名以my开头的文件。

**$ find . -name 'my*' -ls**

搜索当前目录中，所有文件名以my开头的文件，并显示它们的详细信息。

**$ find . -type f -mmin -10**

搜索当前目录中，所有过去10分钟中更新过的普通文件。如果不加-type f参数，则搜索普通文件+特殊文件+目录。

find / -name wp-content -print




# COMMENT：




# REF：





 	
  1. 


[Linux的五个查找命令](http://www.ruanyifeng.com/blog/2009/10/5_ways_to_search_for_files_using_the_terminal.html)






