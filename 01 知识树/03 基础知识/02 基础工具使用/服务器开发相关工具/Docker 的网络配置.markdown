---
author: evo
comments: true
date: 2018-05-04 16:13:00+00:00
layout: post
link: http://106.15.37.116/2018/05/05/docker-%e7%9a%84%e7%bd%91%e7%bb%9c%e9%85%8d%e7%bd%ae/
slug: docker-%e7%9a%84%e7%bd%91%e7%bb%9c%e9%85%8d%e7%bd%ae
title: Docker 的网络配置
wordpress_id: 5237
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



docker 安装后, 会自动在系统做一个网桥配置 **docker0** . 其容器都会分配到此网桥配置下的独立, 私有 IP 地址.

如果你要自己配置桥接, 也可以把 **docker0 **删除掉. `docker run` 的时候使用参数 `-b` 指定你自己配置的网桥.

docker 容器的网络, 是相对于实体机的私有网络. 在网桥配置下, 只要知道 IP 地址, 各容器, 及实体机本身都可以自由通信.

但是在实体机的网卡网络下, docker 容器就不可见了. 要让容器被外界访问到, 最简单的一个方法就是直接做端口映射, 把容器的端口和实体机端口成对连通. `docker run` 的时候使用 `-p` 参数就可以指定一对端口映射:

    
    docker run -d -p 5000:22 -p 18888:8888 zys:common
    


上面的命令, 在启动容器时, 指定的端口映射表示实体机 5000 端口映射到容器 22 端口, 同时 18888 端口映射到容器 8888 端口. 这样做之后, 就可以通过实体机的 5000 端口 ssh 登录到容器了:

    
    ssh root@localhost -p5000
    ssh root@realip -p5000
    


`docker run` 时还有其它参数可用到控制容器的网络配置:



 	`-p`

 	    端口映射

 	`-h`

 	    设置主机名, 这个主机名是仅容器自己可见的.

 	`--link=CONTAINER_NAME:ALIAS`

 	    把另一个容器的地址配置成一个 `ALIAS` 主机名.

 	`--dns`

 	    配置 DNS 服务器.
























* * *





# COMMENT



