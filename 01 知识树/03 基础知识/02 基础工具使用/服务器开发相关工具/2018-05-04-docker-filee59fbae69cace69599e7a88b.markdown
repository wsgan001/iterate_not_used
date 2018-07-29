---
author: evo
comments: true
date: 2018-05-04 16:12:37+00:00
layout: post
link: http://106.15.37.116/2018/05/05/docker-file%e5%9f%ba%e6%9c%ac%e6%95%99%e7%a8%8b/
slug: docker-file%e5%9f%ba%e6%9c%ac%e6%95%99%e7%a8%8b
title: Docker file基本教程
wordpress_id: 5236
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



**Dockerfile** 是记录了镜像是如何被构建出来的配置文件, 可以被 `docker` 直接执行以创建一个镜像. 它的样子:

    
    FROM ubuntu:14.04
    MAINTAINER YS.Zou <>
    
    ADD run /root/run
    ADD sources.list /etc/apt/sources.list
    ADD id_rsa.pub /tmp/pubkey
    ADD requirements /root/requirements
    
    RUN mkdir -p /root/.ssh && \
        cat /tmp/pubkey >> /root/.ssh/authorized_keys && \
        rm -rf /tmp/pubkey
    ...
    
    CMD ["bash", "/root/run"]
    


把文件命名为 `Dockerfile` , 进入文件所在目录, 输入:

    
    docker build .
    


就可以开始构建过程, 并且得到一个新的镜像了.

**Dockerfile** 支持一些很简单的命令:



 	FROM

 	    以哪个镜像为基础开始构建.

 	MAINTAINER

 	    作者信息.

 	RUN

 	    运行一条命令.

 	CMD

 	    `docker run IMAGE_ID cmd` 这里的默认命令.

 	ENTRYPOINT

 	    `docker run IMAGE_ID cmd` 这里的默认命令的前面部分, `run` 中 `cmd` 可以作为后续参数.

 	EXPOSE

 	    声明会用到的端口.

 	ENV

 	    设置环境变量

 	ADD

 	    从当前目录复制文件到容器. 会自动处理目录, 压缩包等情况.

 	COPY

 	    从当前目录复制文件到容器. 只是单纯地复制文件.

 	VOLUME

 	    声明一个数据卷, 可用于挂载.

 	USER

 	    **RUN** 命令执行时的用户.

 	WORKDIR

 	    **RUN**, **CMD**, **ENTRYPOINT** 这些命令执行时的当前目录.

 	ONBUILD

 	    前缀命令, 放在上面这些命令前面, 表示生成的镜像再次作为"基础镜像"被用于构建时, 要执行的命令.


`build` 的过程, 会依次执行上面的命令, 实际上, docker 做的事, 也就是从基础镜像启一个容器, 然后执行一条命令, 修改之后提交此容器为新镜像. 以此类推, 直到所有命令都执行完. 所以在得到最终构建的镜像时, 会生成很多"中间镜像". 而如果 **Dockerfile** 中某条命令有错, 也是在当前中止, 过程中的"中间镜像"及"当前构建用的容器"仍然存在的.























* * *





# COMMENT



