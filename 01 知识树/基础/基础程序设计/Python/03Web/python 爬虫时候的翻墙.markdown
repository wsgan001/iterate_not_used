---
author: evo
comments: true
date: 2018-05-13 09:04:20+00:00
layout: post
link: http://106.15.37.116/2018/05/13/python-cross-wall/
slug: python-cross-wall
title: python 爬虫时候的翻墙
wordpress_id: 5660
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [python-快速使用urllib爬取网页（6-代理服务器）](https://blog.csdn.net/qq_38262266/article/details/78883760)

 	
  2. [Python：爬虫如何翻墙并保存获取到的数据？](https://segmentfault.com/q/1010000008986220/a-1020000012102139) 其中的被采纳的答案




# TODO





 	
  * **实际上我还是有很多问题不是很清楚的：**

 	
  * **比如到底翻墙与代理IP有什么关系？翻墙的时候发生了什么？**

 	
  * **我开shadowsocks的时候用python怎么翻墙？我不开shadowsocks的时候python怎么用我的shadowsocks的服务器翻墙？**

 	
  * **shadowsocks里面是怎么实现的？**

 	
  * **这个要不断补充**




# MOTIVE





 	
  * 实际上，不只爬国外网站的时候，只要你想访问google等的api，就只能翻墙。这是必须的，因此非常像知道怎么用python翻墙





* * *






# 




# 翻墙是什么意思？什么是代理？


当我们的IP被网站服务器屏蔽的时候，我们就无法访问网站，就像我们无法访问国外的网站一样。

这个时候，我们就要用到代理服务器，也就是说我们不用自己的IP去访问网站，而是通过代理服务器IP去访问网站获取信息，我们再从代理服务器那里获取我们想要的信息。

可以从网上搜索免费代理IP，例如 http://www.xicidaili.com/


# 




代码如下：

    
    import json
    import re
    import urllib.request
    import requests
    
    #一些 header 这里没有使用，先放在这里
    user_agents = ['Mozilla/5.0 (Windows NT 6.1; WOW64; rv:23.0) Gecko/20130406 Firefox/23.0', \
                   'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:18.0) Gecko/20100101 Firefox/18.0', \
                   'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/533+ \
                   (KHTML, like Gecko) Element Browser 5.0', \
                   'IBM WebExplorer /v0.94', 'Galaxy/1.0 [en] (Mac OS X 10.5.6; U; en)', \
                   'Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/6.0)', \
                   'Opera/9.80 (Windows NT 6.0) Presto/2.12.388 Version/12.14', \
                   'Mozilla/5.0 (iPad; CPU OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) \
                   Version/6.0 Mobile/10A5355d Safari/8536.25', \
                   'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) \
                   Chrome/28.0.1468.0 Safari/537.36', \
                   'Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0; Trident/5.0; TheWorld)']
    
    
    
    
    if __name__ == '__main__':
        proxy_addrs = {
            "http": "220.168.237.187:8888",
            'https': 'https://127.0.0.1:1080',
            'http': 'http://127.0.0.1:1080'
        }
        headers = {
            'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36'
        }
    
        google_url = 'https://www.google.com'
    
        print('--------------使用urllib--------------')
        proxy_handler = urllib.request.ProxyHandler(proxy_addrs)  # 设置对应的代理服务器信息
        opener = urllib.request.build_opener(proxy_handler, urllib.request.HTTPHandler)  # 创建一个自定义的opener对象
        urllib.request.install_opener(opener)  # 创建全局默认的opener对象
        req = urllib.request.Request(google_url, headers=headers)
        response = urllib.request.urlopen(req)
        print(response.read().decode("utf-8"))
    
        print('--------------使用requests--------------')
        response = requests.get(google_url, proxies=proxy_addrs)
        print(response.text)




上面的代码在我开了 shadowsocks 并且代理端口设定为 1080 的时候是可用的。















* * *





# COMMENT



