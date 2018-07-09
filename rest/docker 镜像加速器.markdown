---
author: evo
comments: true
date: 2018-06-01 15:10:47+00:00
layout: post
link: http://106.15.37.116/2018/06/01/docker-%e9%95%9c%e5%83%8f%e5%8a%a0%e9%80%9f%e5%99%a8/
slug: docker-%e9%95%9c%e5%83%8f%e5%8a%a0%e9%80%9f%e5%99%a8
title: docker 镜像加速器
wordpress_id: 7326
categories:
- 基础工具使用
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [CentOS7安装Docker](https://www.jianshu.com/p/3a4cd73e3272)

 	
  2. [docker怎么修改拉取源从指定的国内仓库拉取镜像？](https://www.zhihu.com/question/55135855)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa


Docker支持以下的CentOS版本：
CentOS 7 (64-bit)
CentOS 6.5 (64-bit) 或更高的版本

前提条件
目前，CentOS 仅发行版本中的内核支持 Docker。
Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。
Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。

Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

    
    #查看你当前的内核版本
    uname -r
    
    #安装 Docker
    yum -y install docker
    
    #启动 Docker 后台服务
    service docker start
    
    #测试运行 hello-world,由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。
    docker run hello-world
    
    作者：wiseap
    链接：https://www.jianshu.com/p/3a4cd73e3272
    來源：简书
    著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


不过，在运行最后一句的时候有问题：

    
    Unable to find image 'hello-world:latest' locally
    Trying to pull repository docker.io/library/hello-world ... 
    latest: Pulling from docker.io/library/hello-world
    /usr/bin/docker-current: Get https://registry-1.docker.io/v2/library/hello-world/manifests/sha256:d5c74e6f8efc7bdf42a5e22bd764400692cf82360d86b8c587a7584b03f51520: dial tcp: lookup registry-1.docker.io: no such host.
    See '/usr/bin/docker-current run --help'.


查了下，是因为访问的镜像是国外的。

因为在阿里云上，因此可以使用阿里云自带的镜像加速器：

登录阿里云，访问 [https://cr.console.aliyun.com/#/accelerator](https://link.zhihu.com/?target=https%3A//cr.console.aliyun.com/%23/accelerator) 获取专属Docker加速器地址：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b1161be9c52b.png)


直接把他给你的代码运行一下，就可以了。























* * *





# COMMENT



