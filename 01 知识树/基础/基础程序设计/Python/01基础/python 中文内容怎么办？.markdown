---
author: evo
comments: true
date: 2018-05-06 07:40:45+00:00
layout: post
link: http://106.15.37.116/2018/05/06/python-chinese-content/
slug: python-chinese-content
title: python 中文内容怎么办？
wordpress_id: 5321
categories:
- 随想与反思
tags:
- '@NULL'
- python
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  * 


[【整理】Python中遇到"UnicodeDecodeError: ‘gbk’ codec can’t decode bytes in position 2-3: illegal multibyte sequence"之类的编码或解码的错误时如何处理](https://www.crifan.com/summary_python_unicodedecode_error_possible_reasons_and_solutions/)







# TODO





 	
  * **中文的编码和读写还是要好好总结下的。**




# MOTIVE





 	
  * aaa





* * *



实际上在读取文本的时候，还是经常会有编码错误的，可能是编码的确制定错了，也可能是txt文档的内容本身就有一些编码错误，比如说，从pdf上复制下来的文本，其实里面可能会有一些乱码，但是这些乱码看起来与字母很像，因此读取错误的时候就很难查了。比如说下面这个：

    
    If you抮e just looking


存在文本中的时候是utf-8的，但是却有这个乱码汉字。





















* * *





# COMMENT



