---
author: evo
comments: true
date: 2018-05-04 16:14:08+00:00
layout: post
link: http://106.15.37.116/2018/05/05/docker-%e7%9a%84%e9%95%9c%e5%83%8f%e6%9c%8d%e5%8a%a1%e5%99%a8/
slug: docker-%e7%9a%84%e9%95%9c%e5%83%8f%e6%9c%8d%e5%8a%a1%e5%99%a8
title: Docker 的镜像服务器
wordpress_id: 5246
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



docker 的镜像服务器 **docker-registry** 是 docker 项目的组成部分. 前面在谈 docker 的命令时, 它的 `pull/push` 命令就是和镜像服务器打交道. 并且, docker 的设计之中, 服务器地址不是单独配置的, 而是作为镜像名称的一部分.

镜像的完整名称是:

    
    127.0.0.1:5000/zephyr/common:latest
    


各部分的意思:



 	
  * `127.0.0.1:5000` 就是服务器地址

 	
  * `zephyr` 是名字空间

 	
  * `common` 是镜像名

 	
  * `latest` 是版本


**docker-registry** 的实现也是开源的, 在 github [https://github.com/dotcloud/docker-registry](https://github.com/dotcloud/docker-registry) 上拿下源码就可以跑起来.

拿下源码之后, 项目中有一个 Dockerfile 文件, 我们可以开始构建镜像了. build 之前, 因为 GFW 的原因, 我们可以先把 Dockerfile 调整一下, 包括两部分:



 	
  * 把 ubuntu 的软件源改成国内的.

 	
  * 把 pip 的源改成国内的.


然后开始构建:

    
    docker build -rm -t registry .
    


完成之后, 你可以得到一个名为 registry 的镜像, 直接运行即可:

    
    docker run -p 5000:5000 registry
    


访问 [http://localhost:5000](http://localhost:5000/) 能得到响应, 一个 **docker-registry** 服务就起来了.

现在你可以把镜像提交到上面去:

    
    docker tag xxx 127.0.0.1:5000/zephyr/common
    docker push 127.0.0.1:5000/zephyr/common
    


完成之后, 在浏览器中访问 [http://localhost:5000/v1/search](http://localhost:5000/v1/search) 可以看到列表.

获取镜像:

    
    docker pull 127.0.0.1:5000/zephyr/common
    


**docker-registry** 本身是设计成一套 Web API 的, 具体文档在 [http://docs.docker.com/reference/api/registry_api/](http://docs.docker.com/reference/api/registry_api/) .

**docker** 本身的服务, 也是有一套基于网络的 API 可供使用的, 文档在 [http://docs.docker.com/reference/api/docker_remote_api/](http://docs.docker.com/reference/api/docker_remote_api/) .























* * *





# COMMENT



