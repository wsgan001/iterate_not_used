---
author: evo
comments: true
date: 2018-06-07 03:10:52+00:00
layout: post
link: http://106.15.37.116/2018/06/07/hashlib%e4%bd%bf%e7%94%a8%e6%97%b6%e5%87%ba%e7%8e%b0-unicode-objects-must-be-encoded-before-hashing/
slug: hashlib%e4%bd%bf%e7%94%a8%e6%97%b6%e5%87%ba%e7%8e%b0-unicode-objects-must-be-encoded-before-hashing
title: Python hashlib
wordpress_id: 7437
categories:
- 基础程序设计
tags:
- '@NULL'
- python
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL





 	
  1. [hashlib使用时出现: Unicode-objects must be encoded before hashing](http://www.cnblogs.com/everfight/p/python_hashlib.html)




# TODO





 	
  * aaa





* * *





# INTRODUCTION





 	
  * aaa





# hashlib 模块介绍


用于加密相关的操作，代替了md5 模块和sha模块，主要提供SHA1，SHA224，SHA256，SHA384，SHA512，MD5算法。

在python3中已经废弃了md5和sha模块，简单说明下md5和sha的使用。


# 什么是摘要算法呢？


摘要算法又称为哈希算法，散列算法。它通过一个函数，把任意长度的数据转换为一个长度固定的数据串（通常用16进制的字符串表示）用于加密相关的操作。

md5加密
1 hash = hashlib.md5()
2 hash.update('admin'.encode('utf-8'))
3 print(hash.hexdigest())
4 21232f297a57a5a743894a0e4a801fc3
sha1加密
1 hash = hashlib.sha1()
2 hash.update('admin'.encode('utf-8'))
3 print(hash.hexdigest())
4 d033e22ae348aeb5660fc2140aec35850c4da997
sha256加密
1 hash = hashlib.sha256()
2 hash.update('admin'.encode('utf-8'))
3 print(hash.hexdigest())
4 8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
sha384加密

1 hash = hashlib.sha384()
2 hash.update('admin'.encode('utf-8'))
3 print(hash.hexdigest())
4 9ca694a90285c034432c9550421b7b9dbd5c0f4b6673f05f6dbce58052ba20e4248041956ee8c9a2ec9f10290cdc0782

sha512加密

1 hash = hashlib.sha512()
2 hash.update('admin'.encode('utf-8'))
3 print(hash.hexdigest())
4 c7ad44cbad762a5da0a452f9e854fdc1e0e7a52a38015f23f3eab1d80b931dd472634dfac71cd34ebc35d16ab7fb8a90c81f975113d6c7538dc69dd8de9077ec

‘加盐’加密
以上加密算法虽然很厉害，但仍然存在缺陷，通过撞库可以反解。所以必要对加密算法中添加自定义key再来做加密。

1 ###### md5 加密 ############
2 hash = hashlib.md5('python'.encode('utf-8'))
3 hash.update('admin'.encode('utf-8'))
4 print(hash.hexdigest())
5 75b431c498b55557591f834af7856b9f
hmac加密
hmac内部对我们创建的key和内容进行处理后在加密

1 import hmac
2 h = hmac.new('python'.encode('utf-8'))
3 h.update('helloworld'.encode('utf-8'))
4 print(h.hexdigest())
5 b3b867248bb4cace835b59562c39fd55
获取文件的MD5
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
import hashlib
def md5sum(filename):
"""
用于获取文件的md5值
:param filename: 文件名
:return: MD5码
"""
if not os.path.isfile(filename):  # 如果校验md5的文件不是文件，返回空
return
myhash = hashlib.md5()
f = open(filename, 'rb')
while True:
b = f.read(8096)
if not b:
break
myhash.update(b)
f.close()
return myhash.hexdigest()














# 几个问题




## hashlib 使用时出现： Unicode-objects must be encoded before hashing



    
    # hashlib.md5(data)函数中，data参数的类型应该是bytes
    # hash前必须把数据转换成bytes类型
    >>> from hashlib import md5
    File "<stdin>", line 1, in <module>
    >>> c = md5("helloworld")
    TypeError: Unicode-objects must be encoded before hashing
    >>> c = md5("helloworld".encode("utf-8"))
    >>> print(c.hexdigest())
    fc5e038d38a57032085441e7fe7010b0












* * *





# COMMENT



