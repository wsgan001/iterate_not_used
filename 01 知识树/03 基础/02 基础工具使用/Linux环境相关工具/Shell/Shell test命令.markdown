---
author: evo
comments: true
date: 2018-05-04 11:42:41+00:00
layout: post
link: http://106.15.37.116/2018/05/04/shell-test%e5%91%bd%e4%bb%a4/
slug: shell-test%e5%91%bd%e4%bb%a4
title: Shell test命令
wordpress_id: 5126
categories:
- 基础工具使用
tags:
- shell
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [Linux教程](https://www.w3cschool.cn/linux/)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## Shell test命令


Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。



* * *





## 数值测试


<table class="reference " >
<tbody >
<tr >
参数
说明
</tr>
<tr >

<td >-eq
</td>

<td >等于则为真
</td>
</tr>
<tr >

<td >-ne
</td>

<td >不等于则为真
</td>
</tr>
<tr >

<td >-gt
</td>

<td >大于则为真
</td>
</tr>
<tr >

<td >-ge
</td>

<td >大于等于则为真
</td>
</tr>
<tr >

<td >-lt
</td>

<td >小于则为真
</td>
</tr>
<tr >

<td >-le
</td>

<td >小于等于则为真
</td>
</tr>
</tbody>
</table>
实例演示：

    
    num1=100
    num2=100
    if test $[num1] -eq $[num2]
    then
        echo '两个数相等！'
    else
        echo '两个数不相等！'
    fi
    


输出结果：

    
    两个数相等！
    





* * *





## 字符串测试


<table class="reference" >
<tbody >
<tr >
参数
说明
</tr>
<tr >

<td >=
</td>

<td >等于则为真
</td>
</tr>
<tr >

<td >!=
</td>

<td >不相等则为真
</td>
</tr>
<tr >

<td >-z 字符串
</td>

<td >字符串长度为零则为真
</td>
</tr>
<tr >

<td >-n 字符串
</td>

<td >字符串长度不为零则为真
</td>
</tr>
</tbody>
</table>
实例演示：

    
    num1="W3Cschool"
    num2="W3Cschool"
    if test num1=num2
    then
        echo '两个字符串相等!'
    else
        echo '两个字符串不相等!'
    fi
    


输出结果：

    
    两个字符串相等!
    





* * *





## 文件测试


<table class="reference " >
<tbody >
<tr >
参数
说明
</tr>
<tr >

<td >-e 文件名
</td>

<td >如果文件存在则为真
</td>
</tr>
<tr >

<td >-r 文件名
</td>

<td >如果文件存在且可读则为真
</td>
</tr>
<tr >

<td >-w 文件名
</td>

<td >如果文件存在且可写则为真
</td>
</tr>
<tr >

<td >-x 文件名
</td>

<td >如果文件存在且可执行则为真
</td>
</tr>
<tr >

<td >-s 文件名
</td>

<td >如果文件存在且至少有一个字符则为真
</td>
</tr>
<tr >

<td >-d 文件名
</td>

<td >如果文件存在且为目录则为真
</td>
</tr>
<tr >

<td >-f 文件名
</td>

<td >如果文件存在且为普通文件则为真
</td>
</tr>
<tr >

<td >-c 文件名
</td>

<td >如果文件存在且为字符型特殊文件则为真
</td>
</tr>
<tr >

<td >-b 文件名
</td>

<td >如果文件存在且为块特殊文件则为真
</td>
</tr>
</tbody>
</table>
实例演示：

    
    cd /bin
    if test -e ./bash
    then
        echo '文件已存在!'
    else
        echo '文件不存在!'
    fi
    


输出结果：

    
    文件已存在!
    


另外，Shell还提供了与( -a )、或( -o )、非( ! )三个逻辑操作符用于将测试条件连接起来，其优先级为："!"最高，"-a"次之，"-o"最低。例如：

    
    cd /bin
    if test -e ./notFile -o -e ./bash
    then
        echo '有一个文件存在!'
    else
        echo '两个文件都不存在'
    fi
    


输出结果：

    
    有一个文件存在!
























* * *





# COMMENT



