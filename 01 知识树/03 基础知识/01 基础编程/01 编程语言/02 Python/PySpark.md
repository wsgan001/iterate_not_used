---
author: evo
comments: true
date: 2018-03-23 08:35:04+00:00
layout: post
link: http://106.15.37.116/2018/03/23/pyspark/
slug: pyspark
title: PySpark
wordpress_id: 672
categories:
- 随想与反思
tags:
- '@NULL'
- '@todo'
- python
- Spark
---

<!-- more -->


## 缘由：


简单的记一下，暂时没有深入


## 要点：


代码如下：

    
    import sys
    from operator import add
    from pyspark import SparkContext
    sc = SparkContext()
    
    
    lines = sc.textFile("stormofswords.csv")
    counts = lines.flatMap(lambda x: x.split(',')) \
                  .map(lambda x: (x, 1)) \
                  .reduceByKey(add)
    output = counts.collect()
    output = filter(lambda x:not x[0].isnumeric(), sorted(output, key=lambda x:x[1], reverse = True))
    for (word, count) in output[:10]:
        print "%s: %i" % (word, count)
    
    sc.stop()


**没有自己执行过，要学习下，这里只是简单的记录下视频中的代码**

输出应该是：

    
    Tyrion: 36
    Jon: 26
    Sansa: 26
    Robb: 25
    Jaime: 24
    Tywin: 22
    Cersei: 20
    Arya: 19
    Robert: 18
    Joffrey: 18




## COMMENT：
