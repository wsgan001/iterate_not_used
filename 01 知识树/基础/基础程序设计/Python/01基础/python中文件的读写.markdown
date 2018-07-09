---
author: evo
comments: true
date: 2018-03-21 11:07:10+00:00
layout: post
link: http://106.15.37.116/2018/03/21/python-file-read-write/
slug: python-file-read-write
title: python中文件的读写
wordpress_id: 498
categories:
- 随想与反思
tags:
- '@want_to_know'
- python
---

<!-- more -->


## 缘由：


机器学习中经常涉及到文件的读写和处理，因此有必要总结下文件的读写的方法。


## 要点：




### 1.直接读取和写入：



    
    # 注意如果文件不存在就会报错，FileNotFoundError: [Errno 2] No such file or directory: 'c3_two_sum1.py'
    file1 = open('c3_two_sum1.py')
    # 如果不标识 w 就会报错：io.UnsupportedOperation: not writable
    file2 = open('output.txt', 'w')
    while True:
        line = file1.readline()
        file2.write('"' + line[:] + '"' + ",")
        if not line:
            break
    file1.close()  # 文件处理完之后记得关闭
    file2.close()


读文件有3种方法：



 	
  * read() 将文本文件所有行读到一个字符串中。

 	
  * readline() 是一行一行的读，优点是：可以在读行过程中跳过特定行。

 	
  * readlines() 是将文本文件中所有行读到一个list中，文本文件每一行是list的一个元素。


备注：看来对file和directory的操作还是要整理一下，比如经常要用到对文件是否存在进行判断，经常要输出一些文件，这个时候的地址的相关拼接操作就必须熟练。


### 2.使用文件迭代器，用for循环的方法



    
    file2=open('output.txt','w')
    for line in open('test.txt'):
        file2.write('"'+line[:]+'"'+',')


这个方法看起来是个好方法


### 3.使用文件上下文管理器，即with...open...



    
    # 读取文件
    with open('file.txt', 'r') as f:
        data = f.read()
    with open('file.txt', 'r') as f:
        for line in f:
            # ...
            pass
    
    # 写入文件
    text1 = "qqqq"
    with open('file.txt', 'w') as f:
        f.write(text1)
    # 将要打印的line写入文件中
    # QUESTION 这个没明白，还可以这样做？？可以
    with open('file.txt', 'w') as f:
        print(text1, file=f)


注：这个 print(text1,file=f) 之前没有看到过这样使用，看来print的用法还是很多样的


### 4.读取二进制文件怎么读？比如把图片作为二进制文件读进来？



    
    f=open('test.png','rb')
    print(f.read())


任何非标准的文本文件（对于Py2来说，标准是ASCII，对于Py3来说，标准是unicode），你就需要用二进制读入这个文件，然后再用 .decode('...')的方法来解码这个二进制文件，即假如文件使用的是一种somecode 这种编码格式写的 ，就可以先读进来二进制文件，然后进行解密，f.read().decode('somecode') **这个地方需要确认下，并补充例子**
