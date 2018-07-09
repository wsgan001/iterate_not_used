---
author: evo
comments: true
date: 2018-05-30 12:08:17+00:00
layout: post
link: http://106.15.37.116/2018/05/30/linux-centos-%e7%b3%bb%e7%bb%9f%e6%93%8d%e4%bd%9c/
slug: linux-centos-%e7%b3%bb%e7%bb%9f%e6%93%8d%e4%bd%9c
title: Linux CentOS 系统操作
wordpress_id: 7050
categories:
- 基础工具使用
tags:
- linux
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [CentOS重启与关机](http://www.cnblogs.com/booth/p/3227428.html)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa




# 缘由


没想到，我首先想查的就是 reboot， 因为之前我把yum -y update 的安装过程 Ctrl+Z 掉了，然后，因为yum 占用了，因此我又把 yum 的占用停掉了，但是 想使用 yum groupinstall -y development 的时候，他说sqlite 数据库占用了，好吧，只能重启了。 **但是我还是有个问题，yum 占用之后要不要停掉yum呢？而且这个数据库占用是不是因为我停掉了yum产生的呢？还是因为之前的 yum -y update 的时候产生的呢？**






# Linux centos 重启命令：





 	
  * reboot

 	
  * shutdown -r now 立刻重启(root用户使用)

 	
  * shutdown -r 10 过10分钟自动重启(root用户使用)

 	
  * shutdown -r 20:35 在时间为20:35时候重启(root用户使用)


如果是通过shutdown命令设置重启的话，可以用shutdown -c命令取消重启


# Linux centos关机命令：





 	
  * halt 立刻关机

 	
  * poweroff 立刻关机

 	
  * shutdown -h now 立刻关机(root用户使用)

 	
  * shutdown -h 10 10分钟后自动关机


如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启





















* * *





# COMMENT



