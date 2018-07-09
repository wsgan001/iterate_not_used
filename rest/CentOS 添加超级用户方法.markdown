---
author: evo
comments: true
date: 2018-06-03 12:27:31+00:00
layout: post
link: http://106.15.37.116/2018/06/03/centos-%e6%b7%bb%e5%8a%a0%e8%b6%85%e7%ba%a7%e7%94%a8%e6%88%b7%e6%96%b9%e6%b3%95/
slug: centos-%e6%b7%bb%e5%8a%a0%e8%b6%85%e7%ba%a7%e7%94%a8%e6%88%b7%e6%96%b9%e6%b3%95
title: CentOS 添加超级用户方法
wordpress_id: 7373
categories:
- 基础工具使用
tags:
- linux
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [CentOS添加Root权限(超级用户)用户方法|su,sudo命令详解（转）](https://my.oschina.net/u/559845/blog/78293)




# TODO





 	
  * **需要再补充全面一下。**





* * *





# INTRODUCTION





 	
  * aaa




# 添加一个普通用户





 	
  * #adduser junguoguo//添加一个名为junguoguo的用户

 	
  * #passwd junguoguo //修改密码





# **赋予****root****权限**


修改 /etc/sudoers 文件，找到下面一行，在root下面添加一行，如下所示：

    
    ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL
    junguoguo ALL=(ALL) ALL


修改完毕，现在可以用 junguoguo 帐号登录，然后用命令 **su –** ，即可获得root权限进行操作。

















* * *





# COMMENT



