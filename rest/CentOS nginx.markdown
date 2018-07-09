---
author: evo
comments: true
date: 2018-06-04 00:04:24+00:00
layout: post
link: http://106.15.37.116/2018/06/04/centos-nginx/
slug: centos-nginx
title: CentOS nginx
wordpress_id: 7385
categories:
- 基础工具使用
tags:
- linux
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [nginx的sites-available目录找不到/丢失](https://www.centos.bz/question/nginx-sites-available-not-found/)

 	
  2. [centos 下 nginx 没有 sites-enabled 文件夹](https://www.jianshu.com/p/10814151f071)

 	
  3. [使用 Nginx 和 Gunicorn 部署 Django 博客](https://www.zmrenwu.com/post/20/)




# TODO





 	
  * **现在还是与 Django 比较相关的，后续全面总结一下。**





* * *





# INTRODUCTION





 	
  * aaa









# 安装





 	
  * yun install nginx




## 配置


转到 nginx 路径下：



 	
  * cd /etc/nginx


nginx 在 centos 上安装是没有 sites-availables 和 sites-enabled 两个文件夹的，因此需要创建：

 	
  * mkdir sites-availables

 	
  * mkdir sites-enabled


然后把 sites-enabled 这个文件夹添加到 nginx.conf 里面：

 	
  * cd /etc/nginx

 	
  * vim nginx.conf

 	
  * 在 http block 中添加：

 	
    * include /etc/nginx/sites-enabled/*;  # 注意，是有分号的





OK，现在才开始写配置文件：

 	
  * cd /etc/nginx/sites-availables

 	
  * vim demo.zmrenwu.com #文件名我一般就设置为域名

 	
  * 里面写上：


![](http://106.15.37.116/wp-content/uploads/2018/06/img_5b147fa9e18aa.png)





说明：

 	
  1. 服务的域名为 demo.zmrenwu.com。

 	
  2. 所有URL 带有 /static 的请求均由 Nginx 处理，alias 指明了静态文件的存放目录。

 	
  3. 其它请求转发给 Django 处理。proxy_pass 后面使用了 unix 套接字，其作用是防止端口冲突，这里就不再详述。


这样，设置文件就写好了，接下来需要创建一个符号链接，把这个配置文件加入到启用的网站列表中去，被启用网站的目录在 /etc/nginx/sites-enabled/，你可以理解为从 sites-available/ 目录下发送了一个配置文件的快捷方式到 sites-enabled/ 目录。具体命令如下：

 	
  * sudo ln -s /etc/nginx/sites-available/demo.zmrenwu.com /etc/nginx/sites-enabled/demo.zmrenwu.com


这样，配置就写好了。


# 使用





 	
  * sudo service nginx start

 	
  * sudo service nginx stop

 	
  * sudo service nginx restart

 	
  * systemctl status nginx.service # 查看 nginx 当前状态










* * *





# COMMENT



