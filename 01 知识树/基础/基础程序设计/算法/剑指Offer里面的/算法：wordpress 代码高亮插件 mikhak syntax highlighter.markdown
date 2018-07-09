---
author: evo
comments: true
date: 2018-03-19 15:03:45+00:00
layout: post
link: http://106.15.37.116/2018/03/19/wordpress-mikhak-syntax-highlighter/
slug: wordpress-mikhak-syntax-highlighter
title: wordpress 代码高亮插件 mikhak syntax highlighter
wordpress_id: 433
categories:
- 非核心点
tags:
- wordpress
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [Google Code Prettify – 程式碼上色工具](http://blog.shihshih.com/google-code-prettify/)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





# wordpress 的代码高亮插件


实际上，wordpress的代码高亮插件还是很多的，但是要么就是不好配置，要么就是要切换到文本里面来添加代码，还要加一些code的标记。

因此试了很多，还是用这个 mikhak syntax highlighter


# 高亮是怎么实现的？


看了下大部分的代码高亮插件里面其实用的都是 google code prettify。因此从本质上来说都是类似的。


# mikhak syntax highlighter 插件存在的问题




## 在插件的 General 界面设置的 default language 没有作用，并没有切换过去，每次都要手动


**未解决**


## 代码的高亮效果并不像想象中的那么利害？


实际上 google code prettify 的效果应该是很好的，但是不知道是这个插件用的 google code prettify 版本太旧了还是有些东西没有正确加载进来，反正效果不是很好。

比如 Simple Code Highlighter 这个插件的效果：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5af7b72142f12.png)


效果非常好。但是没有什么自定义的选项。

但是  mikhak syntax highlighter 的效果就不是很好了：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5af7b7fba9725.png)


但是可以定制，选择一些style，虽然这些style都不是很好。

而且在手机上看的时候，不把代码自动换行。

因此还是使用这个，但是不知道怎么把效果弄好一些，**未解决。**




## 有的主题，使用这个插件会多出来两行


比如这样：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5af7b4e0b569e.png)


这个问题可以在 Appearance 下面的Custom CSS Code里面写入：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5af7b55f56bdb.png)




    
    pre.prettyprint {
        padding: 2px;
    }


来解决：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5af7b5732c747.png)




















* * *





# COMMENT



