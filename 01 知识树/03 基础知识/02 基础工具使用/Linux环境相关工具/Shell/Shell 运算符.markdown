---
author: evo
comments: true
date: 2018-05-04 11:41:04+00:00
layout: post
link: http://106.15.37.116/2018/05/04/shell-%e8%bf%90%e7%ae%97%e7%ac%a6/
slug: shell-%e8%bf%90%e7%ae%97%e7%ac%a6
title: Shell 运算符
wordpress_id: 5117
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





## Shell 基本运算符


Shell 和其他编程语言一样，支持多种运算符，包括：



 	
  * 算数运算符

 	
  * 关系运算符

 	
  * 布尔运算符

 	
  * 字符串运算符

 	
  * 文件测试运算符


expr 是一款表达式计算工具，使用它能完成表达式的求值操作。

例如，两个数相加(注意使用的是反引号 ` 而不是单引号 ')：

    
    #!/bin/bash
    
    val=`expr 2 + 2`
    echo "两数之和为 : $val"


[运行实例 »](https://www.w3cschool.cn/tryrun/showcode/add2data&type=bash)

执行脚本，输出结果如下所示：

    
    两数之和为 : 4


两点注意：



 	
  * 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。

 	
  * 完整的表达式要被 ` ` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。




## 算术运算符


下表列出了常用的算术运算符，假定变量 a 为 10，变量 b 为 20：
<table class="reference    " >
<tbody >
<tr >
运算符
说明
举例
</tr>
<tr >

<td >+
</td>

<td >加法
</td>

<td >`expr $a + $b` 结果为 30。
</td>
</tr>
<tr >

<td >-
</td>

<td >减法
</td>

<td >`expr $a - $b` 结果为 -10。
</td>
</tr>
<tr >

<td >*
</td>

<td >乘法
</td>

<td >`expr $a \* $b` 结果为  200。
</td>
</tr>
<tr >

<td >/
</td>

<td >除法
</td>

<td >`expr $b / $a` 结果为 2。
</td>
</tr>
<tr >

<td >%
</td>

<td >取余
</td>

<td >`expr $b % $a` 结果为 0。
</td>
</tr>
<tr >

<td >=
</td>

<td >赋值
</td>

<td >a=$b 将把变量 b 的值赋给 a。
</td>
</tr>
<tr >

<td >==
</td>

<td >相等。用于比较两个数字，相同则返回 true。
</td>

<td >[ $a == $b ] 返回 false。
</td>
</tr>
<tr >

<td >!=
</td>

<td >不相等。用于比较两个数字，不相同则返回 true。
</td>

<td >[ $a != $b ] 返回 true。
</td>
</tr>
</tbody>
</table>
注意：条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]。


### 实例


算术运算符实例如下：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    a=10
    b=20
    
    val=`expr $a + $b`
    echo "a + b : $val"
    
    val=`expr $a - $b`
    echo "a - b : $val"
    
    val=`expr $a \* $b`
    echo "a * b : $val"
    
    val=`expr $b / $a`
    echo "b / a : $val"
    
    val=`expr $b % $a`
    echo "b % a : $val"
    
    if [ $a == $b ]
    then
       echo "a 等于 b"
    fi
    if [ $a != $b ]
    then
       echo "a 不等于 b"
    fi


执行脚本，输出结果如下所示：

    
    a + b : 30
    a - b : -10
    a * b : 200
    b / a : 2
    b % a : 0
    a 不等于 b




<blockquote>注意：乘号(*)前边必须加反斜杠(\)才能实现乘法运算；if...then...fi 是条件语句，后续将会讲解。在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。</blockquote>




## 关系运算符


关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

下表列出了常用的关系运算符，假定变量 a 为 10，变量 b 为 20：
<table class="reference    " >
<tbody >
<tr >
运算符
说明
举例
</tr>
<tr >

<td >-eq
</td>

<td >检测两个数是否相等，相等返回 true。
</td>

<td >[ $a -eq $b ] 返回 false。
</td>
</tr>
<tr >

<td >-ne
</td>

<td >检测两个数是否相等，不相等返回 true。
</td>

<td >[ $a -ne $b ] 返回 true。
</td>
</tr>
<tr >

<td >-gt
</td>

<td >检测左边的数是否大于右边的，如果是，则返回 true。
</td>

<td >[ $a -gt $b ] 返回 false。
</td>
</tr>
<tr >

<td >-lt
</td>

<td >检测左边的数是否小于右边的，如果是，则返回 true。
</td>

<td >[ $a -lt $b ] 返回 true。
</td>
</tr>
<tr >

<td >-ge
</td>

<td >检测左边的数是否大于等于右边的，如果是，则返回 true。
</td>

<td >[ $a -ge $b ] 返回 false。
</td>
</tr>
<tr >

<td >-le
</td>

<td >检测左边的数是否小于等于右边的，如果是，则返回 true。
</td>

<td >[ $a -le $b ] 返回 true。
</td>
</tr>
</tbody>
</table>


### 实例


关系运算符实例如下：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    a=10
    b=20
    
    if [ $a -eq $b ]
    then
       echo "$a -eq $b : a 等于 b"
    else
       echo "$a -eq $b: a 不等于 b"
    fi
    if [ $a -ne $b ]
    then
       echo "$a -ne $b: a 不等于 b"
    else
       echo "$a -ne $b : a 等于 b"
    fi
    if [ $a -gt $b ]
    then
       echo "$a -gt $b: a 大于 b"
    else
       echo "$a -gt $b: a 不大于 b"
    fi
    if [ $a -lt $b ]
    then
       echo "$a -lt $b: a 小于 b"
    else
       echo "$a -lt $b: a 不小于 b"
    fi
    if [ $a -ge $b ]
    then
       echo "$a -ge $b: a 大于或等于 b"
    else
       echo "$a -ge $b: a 小于 b"
    fi
    if [ $a -le $b ]
    then
       echo "$a -le $b: a 小于或等于 b"
    else
       echo "$a -le $b: a 大于 b"
    fi


执行脚本，输出结果如下所示：

    
    10 -eq 20: a 不等于 b
    10 -ne 20: a 不等于 b
    10 -gt 20: a 不大于 b
    10 -lt 20: a 小于 b
    10 -ge 20: a 小于 b
    10 -le 20: a 小于或等于 b




## 布尔运算符


下表列出了常用的布尔运算符，假定变量 a 为 10，变量 b 为 20：
<table class="reference    " >
<tbody >
<tr >
运算符
说明
举例
</tr>
<tr >

<td >!
</td>

<td >非运算，表达式为 true 则返回 false，否则返回 true。
</td>

<td >[ ! false ] 返回 true。
</td>
</tr>
<tr >

<td >-o
</td>

<td >或运算，有一个表达式为 true 则返回 true。
</td>

<td >[ $a -lt 20 -o $b -gt 100 ] 返回 true。
</td>
</tr>
<tr >

<td >-a
</td>

<td >与运算，两个表达式都为 true 才返回 true。
</td>

<td >[ $a -lt 20 -a $b -gt 100 ] 返回 false。
</td>
</tr>
</tbody>
</table>


### 实例


布尔运算符实例如下：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    a=10
    b=20
    
    if [ $a != $b ]
    then
       echo "$a != $b : a 不等于 b"
    else
       echo "$a != $b: a 等于 b"
    fi
    if [ $a -lt 100 -a $b -gt 15 ]
    then
       echo "$a -lt 100 -a $b -gt 15 : 返回 true"
    else
       echo "$a -lt 100 -a $b -gt 15 : 返回 false"
    fi
    if [ $a -lt 100 -o $b -gt 100 ]
    then
       echo "$a -lt 100 -o $b -gt 100 : 返回 true"
    else
       echo "$a -lt 100 -o $b -gt 100 : 返回 false"
    fi
    if [ $a -lt 5 -o $b -gt 100 ]
    then
       echo "$a -lt 100 -o $b -gt 100 : 返回 true"
    else
       echo "$a -lt 100 -o $b -gt 100 : 返回 false"
    fi


执行脚本，输出结果如下所示：

    
    10 != 20 : a 不等于 b
    10 -lt 100 -a 20 -gt 15 : 返回 true
    10 -lt 100 -o 20 -gt 100 : 返回 true
    10 -lt 100 -o 20 -gt 100 : 返回 false




## 逻辑运算符


以下介绍 Shell 的逻辑运算符，假定变量 a 为 10，变量 b 为 20:
<table class="reference    " >
<tbody >
<tr >
运算符
说明
举例
</tr>
<tr >

<td >&&
</td>

<td >逻辑的 AND
</td>

<td >[[ $a -lt 100 && $b -gt 100 ]] 返回 false
</td>
</tr>
<tr >

<td >||
</td>

<td >逻辑的 OR
</td>

<td >[[ $a -lt 100 || $b -gt 100 ]] 返回 true
</td>
</tr>
</tbody>
</table>


### 实例


逻辑运算符实例如下：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    a=10
    b=20
    
    if [[ $a -lt 100 && $b -gt 100 ]]
    then
       echo "返回 true"
    else
       echo "返回 false"
    fi
    
    if [[ $a -lt 100 || $b -gt 100 ]]
    then
       echo "返回 true"
    else
       echo "返回 false"
    fi


执行脚本，输出结果如下所示：

    
    返回 false
    返回 true




## 字符串运算符


下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：
<table class="reference    " >
<tbody >
<tr >
运算符
说明
举例
</tr>
<tr >

<td >=
</td>

<td >检测两个字符串是否相等，相等返回 true。
</td>

<td >[ $a = $b ] 返回 false。
</td>
</tr>
<tr >

<td >!=
</td>

<td >检测两个字符串是否相等，不相等返回 true。
</td>

<td >[ $a != $b ] 返回 true。
</td>
</tr>
<tr >

<td >-z
</td>

<td >检测字符串长度是否为0，为0返回 true。
</td>

<td >[ -z $a ] 返回 false。
</td>
</tr>
<tr >

<td >-n
</td>

<td >检测字符串长度是否为0，不为0返回 true。
</td>

<td >[ -n $a ] 返回 true。
</td>
</tr>
<tr >

<td >str
</td>

<td >检测字符串是否为空，不为空返回 true。
</td>

<td >[ $a ] 返回 true。
</td>
</tr>
</tbody>
</table>


### 实例


字符串运算符实例如下：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    a="abc"
    b="efg"
    
    if [ $a = $b ]
    then
       echo "$a = $b : a 等于 b"
    else
       echo "$a = $b: a 不等于 b"
    fi
    if [ $a != $b ]
    then
       echo "$a != $b : a 不等于 b"
    else
       echo "$a != $b: a 等于 b"
    fi
    if [ -z $a ]
    then
       echo "-z $a : 字符串长度为 0"
    else
       echo "-z $a : 字符串长度不为 0"
    fi
    if [ -n $a ]
    then
       echo "-n $a : 字符串长度不为 0"
    else
       echo "-n $a : 字符串长度为 0"
    fi
    if [ $a ]
    then
       echo "$a : 字符串不为空"
    else
       echo "$a : 字符串为空"
    fi


执行脚本，输出结果如下所示：

    
    abc = efg: a 不等于 b
    abc != efg : a 不等于 b
    -z abc : 字符串长度不为 0
    -n abc : 字符串长度不为 0
    abc : 字符串不为空




## 文件测试运算符


文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：
<table class="reference    " >
<tbody >
<tr >
操作符
说明
举例
</tr>
<tr >

<td >-b file
</td>

<td >检测文件是否是块设备文件，如果是，则返回 true。
</td>

<td >[ -b $file ] 返回 false。
</td>
</tr>
<tr >

<td >-c file
</td>

<td >检测文件是否是字符设备文件，如果是，则返回 true。
</td>

<td >[ -c $file ] 返回 false。
</td>
</tr>
<tr >

<td >-d file
</td>

<td >检测文件是否是目录，如果是，则返回 true。
</td>

<td >[ -d $file ] 返回 false。
</td>
</tr>
<tr >

<td >-f file
</td>

<td >检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。
</td>

<td >[ -f $file ] 返回 true。
</td>
</tr>
<tr >

<td >-g file
</td>

<td >检测文件是否设置了 SGID 位，如果是，则返回 true。
</td>

<td >[ -g $file ] 返回 false。
</td>
</tr>
<tr >

<td >-k file
</td>

<td >检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。
</td>

<td >[ -k $file ] 返回 false。
</td>
</tr>
<tr >

<td >-p file
</td>

<td >检测文件是否是有名管道，如果是，则返回 true。
</td>

<td >[ -p $file ] 返回 false。
</td>
</tr>
<tr >

<td >-u file
</td>

<td >检测文件是否设置了 SUID 位，如果是，则返回 true。
</td>

<td >[ -u $file ] 返回 false。
</td>
</tr>
<tr >

<td >-r file
</td>

<td >检测文件是否可读，如果是，则返回 true。
</td>

<td >[ -r $file ] 返回 true。
</td>
</tr>
<tr >

<td >-w file
</td>

<td >检测文件是否可写，如果是，则返回 true。
</td>

<td >[ -w $file ] 返回 true。
</td>
</tr>
<tr >

<td >-x file
</td>

<td >检测文件是否可执行，如果是，则返回 true。
</td>

<td >[ -x $file ] 返回 true。
</td>
</tr>
<tr >

<td >-s file
</td>

<td >检测文件是否为空（文件大小是否大于0），不为空返回 true。
</td>

<td >[ -s $file ] 返回 true。
</td>
</tr>
<tr >

<td >-e file
</td>

<td >检测文件（包括目录）是否存在，如果是，则返回 true。
</td>

<td >[ -e $file ] 返回 true。
</td>
</tr>
</tbody>
</table>


### 实例


变量 file 表示文件"/var/www/w3cschool/test.sh"，它的大小为100字节，具有 rwx 权限。下面的代码，将检测该文件的各种属性：

    
    #!/bin/bash
    # author:W3Cschool教程
    # url:www.w3cschool.cn
    
    file="/var/www/w3cschool/test.sh"
    if [ -r $file ]
    then
       echo "文件可读"
    else
       echo "文件不可读"
    fi
    if [ -w $file ]
    then
       echo "文件可写"
    else
       echo "文件不可写"
    fi
    if [ -x $file ]
    then
       echo "文件可执行"
    else
       echo "文件不可执行"
    fi
    if [ -f $file ]
    then
       echo "文件为普通文件"
    else
       echo "文件为特殊文件"
    fi
    if [ -d $file ]
    then
       echo "文件是个目录"
    else
       echo "文件不是个目录"
    fi
    if [ -s $file ]
    then
       echo "文件不为空"
    else
       echo "文件为空"
    fi
    if [ -e $file ]
    then
       echo "文件存在"
    else
       echo "文件不存在"
    fi


执行脚本，输出结果如下所示：

    
    <span>文件可读</span><span>
    </span><span>文件可写</span><span>
    </span><span>文件可执行</span><span>
    </span><span>文件为普通文件</span><span>
    </span><span>文件不是个目录</span><span>
    </span><span>文件不为空</span><span>
    </span><span>文件存在</span>
























* * *





# COMMENT



