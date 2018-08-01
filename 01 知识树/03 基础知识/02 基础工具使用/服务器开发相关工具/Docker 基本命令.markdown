---
author: evo
comments: true
date: 2018-05-04 16:12:05+00:00
layout: post
link: http://106.15.37.116/2018/05/05/docker-%e5%9f%ba%e6%9c%ac%e5%91%bd%e4%bb%a4/
slug: docker-%e5%9f%ba%e6%9c%ac%e5%91%bd%e4%bb%a4
title: Docker 基本命令
wordpress_id: 5235
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



`docker images`显示镜像列表

`docker ps`显示容器列表

`docker run IMAGE_ID`指定镜像, 运行一个容器

`docker start/stop/pause/unpause/kill/restart CONTAINER_ID`操作容器状态

`docker tag IMAGE_ID [REGISTRYHOST/][USERNAME/]NAME[:TAG]`给指定镜像命名

`docker pull/push NAME:TAG`下载, 推送镜像到 **Docker registry server** , NAME 部分包括了服务地址

`docker rm/rmi CONTAINER_ID/IMAGE_ID`删除容器, 镜像

`docker inspect CONTAINER_ID/IMAGE_ID`查看细节信息

`docker top CONTAINER_ID`查看指定的运行容器的进程情况

`docker info`查看系统配置信息

`docker save/load`保存, 恢复镜像信息

`docker commit CONTAINER_ID`从容器创建镜像

`docker export > xxx.tar`保存一个容器

`docker import - < xxx.tar`恢复一个容器

`docker cp CONTAINER_ID:PATH HOSTPATH`从镜像复制文件到实体机

`docker diff CONTAINER_ID`查看容器相对于镜像的文件变化

`docker logs CONTAINER_ID`查看容器日志

`docker build`从 `Dockerfile` 构建镜像

`docker history IMAGE_ID`查看镜像的构建历史























* * *





# COMMENT



