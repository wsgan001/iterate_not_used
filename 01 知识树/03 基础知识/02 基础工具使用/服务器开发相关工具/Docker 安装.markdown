---
author: evo
comments: true
date: 2018-05-04 16:09:41+00:00
layout: post
link: http://106.15.37.116/2018/05/05/docker-%e5%ae%89%e8%a3%85/
slug: docker-%e5%ae%89%e8%a3%85
title:
wordpress_id: 5234
categories:
- 基础工具使用
tags:
- Docker
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1. [Docker简单使用指南](https://www.w3cschool.cn/use_docker/)




# TODO






  * aaa




# MOTIVE






  * aaa





* * *



照着官方文档 [http://docs.docker.com/installation/ubuntulinux/](http://docs.docker.com/installation/ubuntulinux/) 做吧.

一般就是:




  * 更新工具:




    $ sudo apt-get update
    $ sudo apt-get install apt-transport-https ca-certificates







  * 添加软件源的 KEY:




    $ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 \
                       --recv-keys 58118E89F3A912897C070ADBF76221572C52609D







  * 添加软件源:


<table >
<tbody >
<tr >
Ubuntu version
Repository
</tr>
<tr >

<td >Precise 12.04 (LTS)
</td>

<td >`deb https://apt.dockerproject.org/repo ubuntu-precise main`
</td>
</tr>
<tr >

<td >Trusty 14.04 (LTS)
</td>

<td >`deb https://apt.dockerproject.org/repo ubuntu-trusty main`
</td>
</tr>
<tr >

<td >Xenial 16.04 (LTS)
</td>

<td >`deb https://apt.dockerproject.org/repo ubuntu-xenial main`
</td>
</tr>
</tbody>
</table>




  * 更新源列表并安装:




    $ sudo apt-get update
    $ sudo apt-get install docker-engine



安装完成之后, 有一个 `docker` 命令可供使用. 同时, `docker` 的服务默认监听在一个 **sock** 文件上(这样除了命令行工具, 各语言的 API 都很容易实现了).

权限方面, `docker` 的功能限制于 root 用户, docker 用户组. 所以, 你要么带着 `sudo` 用, 要么把当前用户加入到 docker 组:


    $ sudo groupadd docker
    $ sudo gpasswd -a zys docker



重启一下系统吧.

最后, 先安装一个可用的"镜像":


    docker pull ubuntu:14.04



这可能需要一点时间, 也可能因为 GFW 的影响而不容易安装成功.

然后作一个 `Hello World` :


    docker run ubuntu:14.04 echo "Hello World"



安装成功.























* * *





# COMMENT
