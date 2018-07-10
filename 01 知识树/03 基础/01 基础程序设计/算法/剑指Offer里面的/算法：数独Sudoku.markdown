---
author: evo
comments: true
date: 2018-04-04 23:25:08+00:00
layout: post
link: http://106.15.37.116/2018/04/05/sudoku/
slug: sudoku
title: 数独Sudoku
wordpress_id: 3119
categories:
- 随想与反思
tags:
- '@todo'
---

<!-- more -->

数独问题：

解数独问题，初始化时的空位用‘.’表示。
 每行、每列、每个九宫内，都是1-9这9个数字。


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac55e242a3e6.png)




数独Sudoku分析
 若当前位置是空格，则尝试从1到9的所有
数；如果对于1到9的某些数字，当前是合法
的，则继续尝试下一个位置——调用自身即
可。


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac55e4dac1c0.png)


代码如下：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac55e5d6a908.png)


非递归数独Sudoku


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac55ebc4118c.png)


COMMENT：

REF：



 	
  1. 七月在线 算法



