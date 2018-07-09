---
author: evo
comments: true
date: 2018-05-03 04:58:03+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-for-while/
slug: python-for-while
title: Python 循环语句
wordpress_id: 4975
categories:
- 随想与反思
---

<!-- more -->

[mathjax]


# REFERENCE





 	
  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)

 	
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)




# TODO





 	
  * aaa




# MOTIVE





 	
  * aaa





* * *





## Python 循环语句


本章节将向大家介绍Python的循环语句，程序在一般情况下是按顺序执行的。

编程语言提供了各种控制结构，允许更复杂的执行路径。

循环语句允许我们执行一个语句或语句组多次，下面是在大多数编程语言中的循环语句的一般形式：

![loop_architecture](https://www.w3cschool.cn/attachments/uploads/2013/11/loop_architecture.jpg)

Python提供了for循环和while循环（在Python中没有do..while循环）:
<table class="reference" >
<tbody >
<tr >
循环类型
描述
</tr>
<tr >

<td >[while 循环](https://www.w3cschool.cn/python/python-while-loop.html)
</td>

<td >在给定的判断条件为 true 时执行循环体，否则退出循环体。
</td>
</tr>
<tr >

<td >[for 循环](https://www.w3cschool.cn/python/python-for-loop.html)
</td>

<td >重复执行语句
</td>
</tr>
<tr >

<td >[嵌套循环](https://www.w3cschool.cn/python/python-nested-loops.html)
</td>

<td >你可以在while循环体中嵌套for循环
</td>
</tr>
</tbody>
</table>




* * *





## 循环控制语句


循环控制语句可以更改语句执行的顺序。Python支持以下循环控制语句：
<table class="reference" >
<tbody >
<tr >
控制语句
描述
</tr>
<tr >

<td >[break 语句](https://www.w3cschool.cn/python/python-break-statement.html)
</td>

<td >在语句块执行过程中终止循环，并且跳出整个循环
</td>
</tr>
<tr >

<td >[continue 语句](https://www.w3cschool.cn/python/python-continue-statement.html)
</td>

<td >在语句块执行过程中终止当前循环，跳出该次循环，执行下一次循环。
</td>
</tr>
<tr >

<td >[pass 语句](https://www.w3cschool.cn/python/python-pass-statement.html)
</td>

<td >pass是空语句，是为了保持程序结构的完整性。
</td>
</tr>
</tbody>
</table>



## Python While循环语句


Python 编程中 while 语句用于循环执行程序，即在某条件下，循环执行某段程序，以处理需要重复处理的相同任务。其基本形式为：

    
    while 判断条件：
        执行语句……
    


执行语句可以是单个语句或语句块。判断条件可以是任何表达式，任何非零、或非空（null）的值均为true。

当判断条件假false时，循环结束。

执行流程图如下：


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5aea971f143d0.png)




实例：

    
    #!/usr/bin/python
    
    count = 0
    while (count < 9):    print 'The count is:', count    count = count + 1  print "Good bye!"


以上代码执行输出结果:

    
    The count is: 0
    The count is: 1
    The count is: 2
    The count is: 3
    The count is: 4
    The count is: 5
    The count is: 6
    The count is: 7
    The count is: 8
    Good bye!
    


while 语句时还有另外两个重要的命令 continue，break 来跳过循环，continue 用于跳过该次循环，break 则是用于退出循环，此外"判断条件"还可以是个常值，表示循环必定成立，具体用法如下：

    
    # continue 和 break 用法
    
    i = 1
    while i < 10:        i += 1     if i%2 > 0:     # 非双数时跳过输出
            continue
        print i         # 输出双数2、4、6、8、10
    
    i = 1
    while 1:            # 循环条件为1必定成立
        print i         # 输出1~10
        i += 1
        if i > 10:     # 当i大于10时跳出循环
            break






* * *





## 无限循环


如果条件判断语句永远为 true，循环将会无限的执行下去，如下实例：

    
    #coding=utf-8
    #!/usr/bin/python
    
    var = 1
    while var == 1 :  # 该条件永远为true，循环将无限执行下去
       num = raw_input("Enter a number  :")
       print "You entered: ", num
    
    print "Good bye!"
    


以上实例输出结果：

    
    Enter a number  :20
    You entered:  20
    Enter a number  :29
    You entered:  29
    Enter a number  :3
    You entered:  3
    Enter a number between :Traceback (most recent call last):
      File "test.py", line 5, in <module>
        num = raw_input("Enter a number :")
    KeyboardInterrupt
    


**注意：**以上的无限循环你可以使用 CTRL+C 来中断循环。





* * *





## 循环使用 else 语句


在 python 中，for … else 表示这样的意思，for 中的语句和普通的没有区别，else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。

    
    #!/usr/bin/python
    
    count = 0
    while count < 5:
       print count, " is  less than 5"
       count = count + 1
    else:
       print count, " is not less than 5"


以上实例输出结果为：

    
    0 is less than 5
    1 is less than 5
    2 is less than 5
    3 is less than 5
    4 is less than 5
    5 is not less than 5
    






* * *





## 简单语句组


类似if语句的语法，如果你的while循环体中只有一条语句，你可以将该语句与while写在同一行中， 如下所示：

    
    #!/usr/bin/python
    
    flag = 1
    
    while (flag): print 'Given flag is really true!'
    
    print "Good bye!"
    


**注意：**以上的无限循环你可以使用 CTRL+C 来中断循环。


## Python for 循环语句


Python for循环可以遍历任何序列的项目，如一个列表或者一个字符串。

**语法：**

for循环的语法格式如下：

    
    for iterating_var in sequence: 
       statements(s)
    


**实例：**

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    for letter in 'Python':     # 第一个实例
       print '当前字母 :', letter
    
    fruits = ['banana', 'apple',  'mango']
    for fruit in fruits:        # 第二个实例
       print '当前字母 :', fruit
    
    print "Good bye!"
    




以上实例输出结果:

    
    Current Letter : P
    Current Letter : y
    Current Letter : t
    Current Letter : h
    Current Letter : o
    Current Letter : n
    Current fruit : banana
    Current fruit : apple
    Current fruit : mango
    Good bye!
    






* * *





## 通过序列索引迭代


另外一种执行循环的遍历方式是通过索引，如下实例：

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    fruits = ['banana', 'apple',  'mango']
    for index in range(len(fruits)):
       print '当前水果 :', fruits[index]
    
    print "Good bye!"
    


以上实例输出结果：

    
    当前水果 : banana
    当前水果 : apple
    当前水果 : mango
    Good bye!
    


以上实例我们使用了内置函数 len() 和 range(),函数 len() 返回列表的长度，即元素的个数。 range返回一个序列的数。





* * *





## 循环使用 else 语句


在 python 中，for … else 表示这样的意思，for 中的语句和普通的没有区别，else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。

如下实例：

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    for num in range(10,20):  # 迭代 10 到 20 之间的数字
       for i in range(2,num): # 根据因子迭代
          if num%i == 0:      # 确定第一个因子
             j=num/i          # 计算第二个因子
             print '%d 等于 %d * %d' % (num,i,j)
             break            # 跳出当前循环
       else:                  # 循环的 else 部分
          print num, '是一个质数'
    


以上实例输出结果：

    
    10 等于 2 * 5
    11 是一个质数
    12 等于 2 * 6
    13 是一个质数
    14 等于 2 * 7
    15 等于 3 * 5
    16 等于 2 * 8
    17 是一个质数
    18 等于 2 * 9
    19 是一个质数





## Python 循环嵌套


Python 语言允许在一个循环体里面嵌入另一个循环。

**Python for 循环嵌套语法：**

    
    for iterating_var in sequence:
       for iterating_var in sequence:
          statements(s)
       statements(s)




**Python while 循环嵌套语法：**

    
    while expression:
       while expression:
          statement(s)
       statement(s)
    


你可以在循环体内嵌入其他的循环体，如在while循环中可以嵌入for循环， 反之，你可以在for循环中嵌入while循环。

**实例：**

以下实例使用了嵌套循环输出2~100之间的素数：

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    i = 2
    while(i < 100):
       j = 2
       while(j <= (i/j)):
          if not(i%j): break
          j = j + 1
       if (j > i/j) : print i, " 是素数"
       i = i + 1
    
    print "Good bye!"


以上实例输出结果:

    
    2 是素数
    3 是素数
    5 是素数
    7 是素数
    11 是素数
    13 是素数
    17 是素数
    19 是素数
    23 是素数
    29 是素数
    31 是素数
    37 是素数
    41 是素数
    43 是素数
    47 是素数
    53 是素数
    59 是素数
    61 是素数
    67 是素数
    71 是素数
    73 是素数
    79 是素数
    83 是素数
    89 是素数
    97 是素数
    Good bye!





## Python break 语句


Python break语句，就像在C语言中，打破了最小封闭for或while循环。

break语句用来终止循环语句，即循环条件没有False条件或者序列还没被完全递归完，也会停止执行循环语句。

break语句用在while和for循环中。

如果您使用嵌套循环，break语句将停止执行最深层的循环，并开始执行下一行代码。

**Python语言 break 语句语法：**

    
    break






**实例：**

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
     
    for letter in 'Python':     # 第一个实例
       if letter == 'h':
          break
       print '当期字母 :', letter
      
    var = 10                    # 第二个实例
    while var > 0:              
       print '当期变量值 :', var
       var = var -1
       if var == 5:   # 当变量 var 等于 5 时退出循环
          break
     
    print "Good bye!"




以上实例执行结果：

    
    当期字母 : P
    当期字母 : y
    当期字母 : t
    当期变量值 : 10
    当期变量值 : 9
    当期变量值 : 8
    当期变量值 : 7
    当期变量值 : 6
    Good bye!




## Python continue 语句


Python continue 语句跳出本次循环，而break跳出整个循环。

continue 语句用来告诉Python跳过当前循环的剩余语句，然后继续进行下一轮循环。

continue语句用在while和for循环中。

**实例：**

    
    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    for letter in 'Python':     # 第一个实例
       if letter == 'h':
          continue
       print '当前字母 :', letter
    
    var = 10                    # 第二个实例
    while var > 0:              
       var = var -1
       if var == 5:
          continue
       print '当前变量值 :', var
    print "Good bye!"




以上实例执行结果：

    
    当前字母 : P
    当前字母 : y
    当前字母 : t
    当前字母 : o
    当前字母 : n
    当前变量值 : 9
    当前变量值 : 8
    当前变量值 : 7
    当前变量值 : 6
    当前变量值 : 4
    当前变量值 : 3
    当前变量值 : 2
    当前变量值 : 1
    当前变量值 : 0
    Good bye!




## Python pass 语句


Python pass是空语句，是为了保持程序结构的完整性。

pass 不做任何事情，一般用做占位语句。



**实例：**

    
    # -*- coding: UTF-8 -*- 
    #!/usr/bin/python
    
    # 输出 Python 的每个字母
    for letter in 'Python':
       if letter == 'h':
          pass
          print '这是 pass 块'
       print '当前字母 :', letter
    
    print "Good bye!"


以上实例执行结果：

    
    当前字母 : P
    当前字母 : y
    当前字母 : t
    这是 pass 块
    当前字母 : h
    当前字母 : o
    当前字母 : n
    Good bye!






* * *





# COMMENT



