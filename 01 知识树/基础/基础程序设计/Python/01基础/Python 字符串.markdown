# TODO
- 要重新整理下。




## Python 字符串


字符串是 Python 中最常用的数据类型。我们可以使用引号来创建字符串。

创建字符串很简单，只要为变量分配一个值即可。例如：


    var1 = 'Hello World!'
    var2 = "Python w3cschool"






* * *





## Python访问字符串中的值


Python不支持单字符类型，单字符也在Python也是作为一个字符串使用。

Python访问子字符串，可以使用方括号来截取字符串，如下实例：


    #!/usr/bin/python

    var1 = 'Hello World!'
    var2 = "Python w3cschool"

    print "var1[0]: ", var1[0]
    print "var2[1:5]: ", var2[1:5]


以上实例执行结果：


    var1[0]:  H
    var2[1:5]:  ytho






* * *





## Python字符串更新


你可以对已存在的字符串进行修改，并赋值给另一个变量，如下实例：


    #!/usr/bin/python
    # -*- coding: UTF-8 -*-

    var1 = 'Hello World!'

    print "更新字符串 :- ", var1[:6] + 'w3cschool!'


以上实例执行结果


    更新字符串 :-  Hello w3cschool!





* * *





## Python转义字符


在需要在字符中使用特殊字符时，python用反斜杠(\)转义字符。如下表：
<table class="reference  " >

<tr >
转义字符
描述
</tr>

<tbody >
<tr >

<td >\(在行尾时)
</td>

<td >续行符
</td>
</tr>
<tr >

<td >\\
</td>

<td >反斜杠符号
</td>
</tr>
<tr >

<td >\'
</td>

<td >单引号
</td>
</tr>
<tr >

<td >\"
</td>

<td >双引号
</td>
</tr>
<tr >

<td >\a
</td>

<td >响铃
</td>
</tr>
<tr >

<td >\b
</td>

<td >退格(Backspace)
</td>
</tr>
<tr >

<td >\e
</td>

<td >转义
</td>
</tr>
<tr >

<td >00
</td>

<td >空
</td>
</tr>
<tr >

<td >\n
</td>

<td >换行
</td>
</tr>
<tr >

<td >\v
</td>

<td >纵向制表符
</td>
</tr>
<tr >

<td >\t
</td>

<td >横向制表符
</td>
</tr>
<tr >

<td >\r
</td>

<td >回车
</td>
</tr>
<tr >

<td >\f
</td>

<td >换页
</td>
</tr>
<tr >

<td >\oyy
</td>

<td >八进制数，yy代表的字符，例如：\o12代表换行
</td>
</tr>
<tr >

<td >\xyy
</td>

<td >十六进制数，yy代表的字符，例如：\x0a代表换行
</td>
</tr>
<tr >

<td >\other
</td>

<td >其它的字符以普通格式输出
</td>
</tr>
</tbody>
</table>



* * *





## Python字符串运算符


下表实例变量a值为字符串"Hello"，b变量值为"Python"：
<table class="reference  " >
<tbody >
<tr >
操作符
描述
实例
</tr>
<tr >

<td >+
</td>

<td >字符串连接
</td>

<td >a + b 输出结果： HelloPython
</td>
</tr>
<tr >

<td >*
</td>

<td >重复输出字符串
</td>

<td >a*2 输出结果：HelloHello
</td>
</tr>
<tr >

<td >[]
</td>

<td >通过索引获取字符串中字符
</td>

<td >a[1] 输出结果 **e**
</td>
</tr>
<tr >

<td >[ : ]
</td>

<td >截取字符串中的一部分
</td>

<td >a[1:4] 输出结果 **ell**
</td>
</tr>
<tr >

<td >in
</td>

<td >成员运算符 - 如果字符串中包含给定的字符返回 True
</td>

<td >**H in a** 输出结果 1
</td>
</tr>
<tr >

<td >not in
</td>

<td >成员运算符 - 如果字符串中不包含给定的字符返回 True
</td>

<td >**M not in a** 输出结果 1
</td>
</tr>
<tr >

<td >r/R
</td>

<td >原始字符串 - 原始字符串：所有的字符串都是直接按照字面的意思来使用，没有转义特殊或不能打印的字符。 原始字符串除在字符串的第一个引号前加上字母"r"（可以大小写）以外，与普通字符串有着几乎完全相同的语法。
</td>

<td >**print r'\n'** prints \n 和 **print R'\n'** prints \n
</td>
</tr>
<tr >

<td >%
</td>

<td >格式字符串
</td>

<td >请看下一章节
</td>
</tr>
</tbody>
</table>
实例如下：


    #!/usr/bin/python
    # -*- coding: UTF-8 -*-

    a = "Hello"
    b = "Python"

    print "a + b 输出结果：", a + b
    print "a * 2 输出结果：", a * 2
    print "a[1] 输出结果：", a[1]
    print "a[1:4] 输出结果：", a[1:4]

    if( "H" in a) :
        print "H 在变量 a 中"
    else :
    	print "H 不在变量 a 中"

    if( "M" not in a) :
        print "M 不在变量 a 中"
    else :
    	print "M 在变量 a 中"

    print r'\n'
    print R'\n'


以上程序执行结果为：


    a + b 输出结果： HelloPython
    a * 2 输出结果： HelloHello
    a[1] 输出结果： e
    a[1:4] 输出结果： ell
    H 在变量 a 中
    M 不在变量 a 中
    \n
    \n





* * *





## Python字符串格式化


Python 支持格式化字符串的输出 。尽管这样可能会用到非常复杂的表达式，但最基本的用法是将一个值插入到一个有字符串格式符 %s 的字符串中。

在 Python 中，字符串格式化使用与 C 中 sprintf 函数一样的语法。

如下实例：


    #!/usr/bin/python

    print "My name is %s and weight is %d kg!" % ('Zara', 21)



以上实例输出结果：


    My name is Zara and weight is 21 kg!



python字符串格式化符号:
<table class="reference  " >
<tbody >
<tr >
    符   号
描述
</tr>
<tr >

<td >      %c
</td>

<td > 格式化字符及其ASCII码
</td>
</tr>
<tr >

<td >      %s
</td>

<td > 格式化字符串
</td>
</tr>
<tr >

<td >      %d
</td>

<td > 格式化整数
</td>
</tr>
<tr >

<td >      %u
</td>

<td > 格式化无符号整型
</td>
</tr>
<tr >

<td >      %o
</td>

<td > 格式化无符号八进制数
</td>
</tr>
<tr >

<td >      %x
</td>

<td > 格式化无符号十六进制数
</td>
</tr>
<tr >

<td >      %X
</td>

<td > 格式化无符号十六进制数（大写）
</td>
</tr>
<tr >

<td >      %f
</td>

<td > 格式化浮点数字，可指定小数点后的精度
</td>
</tr>
<tr >

<td >      %e
</td>

<td > 用科学计数法格式化浮点数
</td>
</tr>
<tr >

<td >      %E
</td>

<td > 作用同%e，用科学计数法格式化浮点数
</td>
</tr>
<tr >

<td >      %g
</td>

<td > %f和%e的简写
</td>
</tr>
<tr >

<td >      %G
</td>

<td > %f 和 %E 的简写
</td>
</tr>
<tr >

<td >      %p
</td>

<td > 用十六进制数格式化变量的地址
</td>
</tr>
</tbody>
</table>
格式化操作符辅助指令:
<table class="reference  " >
<tbody >
<tr >
符号
功能
</tr>
<tr >

<td >*
</td>

<td >定义宽度或者小数点精度
</td>
</tr>
<tr >

<td >-
</td>

<td >用做左对齐
</td>
</tr>
<tr >

<td >+
</td>

<td >在正数前面显示加号( + )
</td>
</tr>
<tr >

<td ><sp>
</td>

<td >在正数前面显示空格
</td>
</tr>
<tr >

<td >#
</td>

<td >在八进制数前面显示零('0')，在十六进制前面显示'0x'或者'0X'(取决于用的是'x'还是'X')
</td>
</tr>
<tr >

<td >0
</td>

<td >显示的数字前面填充'0'而不是默认的空格
</td>
</tr>
<tr >

<td >%
</td>

<td >'%%'输出一个单一的'%'
</td>
</tr>
<tr >

<td >(var)
</td>

<td >映射变量(字典参数)
</td>
</tr>
<tr >

<td >m.n.
</td>

<td >m 是显示的最小总宽度,n 是小数点后的位数(如果可用的话)
</td>
</tr>
</tbody>
</table>



* * *





## Python三引号（triple quotes）


python中三引号可以将复杂的字符串进行复制:

python三引号允许一个字符串跨多行，字符串中可以包含换行符、制表符以及其他特殊字符。

三引号的语法是一对连续的单引号或者双引号（通常都是成对的用）。


     >>> hi = '''hi
    there'''
    >>> hi   # repr()
    'hi\nthere'
    >>> print hi  # str()
    hi
    there



三引号让程序员从引号和特殊字符串的泥潭里面解脱出来，自始至终保持一小块字符串的格式是所谓的WYSIWYG（所见即所得）格式的。

一个典型的用例是，当你需要一块HTML或者SQL时，这时用字符串组合，特殊字符串转义将会非常的繁琐。


     errHTML = '''
    <HTML><HEAD><TITLE>
    Friends CGI Demo</TITLE></HEAD>
    <BODY><H3>ERROR</H3>
    <B>%s</B><P>
    <FORM><INPUT TYPE=button VALUE=Back ONCLICK="window.history.back()"></FORM>
    </BODY></HTML>
    '''
    cursor.execute('''
    CREATE TABLE users (
    login VARCHAR(8),
    uid INTEGER,
    prid INTEGER)
    ''')






* * *





## Unicode 字符串


Python 中定义一个 Unicode 字符串和定义一个普通字符串一样简单：


    >>> u'Hello World !'
    u'Hello World !'



引号前小写的"u"表示这里创建的是一个 Unicode 字符串。如果你想加入一个特殊字符，可以使用 Python 的 Unicode-Escape 编码。如下例所示：


    >>> u'Hello\u0020World !'
    u'Hello World !'



被替换的 \u0020 标识表示在给定位置插入编码值为 0x0020 的 Unicode 字符（空格符）。



* * *





## python的字符串内建函数


字符串方法是从python1.6到2.0慢慢加进来的——它们也被加到了Jython中。

这些方法实现了string模块的大部分方法，如下表所示列出了目前字符串内建支持的方法，所有的方法都包含了对Unicode的支持，有一些甚至是专门用于Unicode的。
<table class="reference  " >
<tbody >
<tr >
**方法**
**描述**
</tr>
<tr >

<td >[string.capitalize()](https://www.w3cschool.cn/python/att-string-capitalize.html)
</td>

<td >把字符串的第一个字符大写
</td>
</tr>
<tr >

<td >[string.center(width)](https://www.w3cschool.cn/python/att-string-center.html)
</td>

<td >返回一个原字符串居中,并使用空格填充至长度 width 的新字符串
</td>
</tr>
<tr >

<td >**[string.count(str, beg=0, end=len(string))](https://www.w3cschool.cn/python/att-string-count.html)**
</td>

<td >返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
</td>
</tr>
<tr >

<td >[string.decode(encoding='UTF-8', errors='strict')](https://www.w3cschool.cn/python/att-string-decode.html)
</td>

<td >以 encoding 指定的编码格式解码 string，如果出错默认报一个 ValueError 的 异 常 ， 除 非 errors 指 定 的 是 'ignore' 或 者'replace'
</td>
</tr>
<tr >

<td >[string.encode(encoding='UTF-8', errors='strict')](https://www.w3cschool.cn/python/att-string-encode.html)
</td>

<td >以 encoding 指定的编码格式编码 string，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace'
</td>
</tr>
<tr >

<td >**[string.endswith(obj, beg=0, end=len(string))](https://www.w3cschool.cn/python/att-string-endswith.html)**
</td>

<td >检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False.
</td>
</tr>
<tr >

<td >[string.expandtabs(tabsize=8)](https://www.w3cschool.cn/python/att-string-expandtabs.html)
</td>

<td >把字符串 string 中的 tab 符号转为空格，默认的空格数 tabsize 是 8.
</td>
</tr>
<tr >

<td >**[string.find(str, beg=0, end=len(string))](https://www.w3cschool.cn/python/att-string-find.html)**
</td>

<td >检测 str 是否包含在 string 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1
</td>
</tr>
<tr >

<td >**[string.index(str, beg=0, end=len(string))](https://www.w3cschool.cn/python/att-string-index.html)**
</td>

<td >跟find()方法一样，只不过如果str不在 string中会报一个异常.
</td>
</tr>
<tr >

<td >[string.isalnum()](https://www.w3cschool.cn/python/att-string-isalnum.html)
</td>

<td >如果 string 至少有一个字符并且所有字符都是字母或数字则返

回 True,否则返回 False
</td>
</tr>
<tr >

<td >[string.isalpha()](https://www.w3cschool.cn/python/att-string-isalpha.html)
</td>

<td >如果 string 至少有一个字符并且所有字符都是字母则返回 True,

否则返回 False
</td>
</tr>
<tr >

<td >[string.isdecimal()](https://www.w3cschool.cn/python/att-string-isdecimal.html)
</td>

<td >如果 string 只包含十进制数字则返回 True 否则返回 False.
</td>
</tr>
<tr >

<td >[string.isdigit()](https://www.w3cschool.cn/python/att-string-isdigit.html)
</td>

<td >如果 string 只包含数字则返回 True 否则返回 False.
</td>
</tr>
<tr >

<td >[string.islower()](https://www.w3cschool.cn/python/att-string-islower.html)
</td>

<td >如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False
</td>
</tr>
<tr >

<td >[string.isnumeric()](https://www.w3cschool.cn/python/att-string-isnumeric.html)
</td>

<td >如果 string 中只包含数字字符，则返回 True，否则返回 False
</td>
</tr>
<tr >

<td >[string.isspace()](https://www.w3cschool.cn/python/att-string-isspace.html)
</td>

<td >如果 string 中只包含空格，则返回 True，否则返回 False.
</td>
</tr>
<tr >

<td >[string.istitle()](https://www.w3cschool.cn/python/att-string-istitle.html)
</td>

<td >如果 string 是标题化的(见 title())则返回 True，否则返回 False
</td>
</tr>
<tr >

<td >[string.isupper()](https://www.w3cschool.cn/python/att-string-isupper.html)
</td>

<td >如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False
</td>
</tr>
<tr >

<td >**[string.join(seq)](https://www.w3cschool.cn/python/att-string-join.html)**
</td>

<td >Merges (concatenates)以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
</td>
</tr>
<tr >

<td >[string.ljust(width)](https://www.w3cschool.cn/python/att-string-ljust.html)
</td>

<td >返回一个原字符串左对齐,并使用空格填充至长度 width 的新字符串
</td>
</tr>
<tr >

<td >[string.lower()](https://www.w3cschool.cn/python/att-string-lower.html)
</td>

<td >转换 string 中所有大写字符为小写.
</td>
</tr>
<tr >

<td >[string.lstrip()](https://www.w3cschool.cn/python/att-string-lstrip.html)
</td>

<td >截掉 string 左边的空格
</td>
</tr>
<tr >

<td >[string.maketrans(intab, outtab])](https://www.w3cschool.cn/python/att-string-maketrans.html)
</td>

<td >maketrans() 方法用于创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
</td>
</tr>
<tr >

<td >[max(str)](https://www.w3cschool.cn/python/att-string-max.html)
</td>

<td >返回字符串 _str_ 中最大的字母。
</td>
</tr>
<tr >

<td >[min(str)](https://www.w3cschool.cn/python/att-string-min.html)
</td>

<td >返回字符串 _str_ 中最小的字母。
</td>
</tr>
<tr >

<td >**[string.partition(str)](https://www.w3cschool.cn/python/att-string-partition.html)**
</td>

<td >有点像 find()和 split()的结合体,从 str 出现的第一个位置起,把 字 符 串 string 分 成 一 个 3 元 素 的 元 组 (string_pre_str,str,string_post_str),如果 string 中不包含str 则 string_pre_str == string.
</td>
</tr>
<tr >

<td >**[string.replace(str1, str2,  num=string.count(str1))](https://www.w3cschool.cn/python/att-string-replace.html)**
</td>

<td >把 string 中的 str1 替换成 str2,如果 num 指定，则替换不超过 num 次.
</td>
</tr>
<tr >

<td >[string.rfind(str, beg=0,end=len(string) )](https://www.w3cschool.cn/python/att-string-rfind.html)
</td>

<td >类似于 find()函数，不过是从右边开始查找.
</td>
</tr>
<tr >

<td >[string.rindex( str, beg=0,end=len(string))](https://www.w3cschool.cn/python/att-string-rindex.html)
</td>

<td >类似于 index()，不过是从右边开始.
</td>
</tr>
<tr >

<td >[string.rjust(width)](https://www.w3cschool.cn/python/att-string-rjust.html)
</td>

<td >返回一个原字符串右对齐,并使用空格填充至长度 width 的新字符串
</td>
</tr>
<tr >

<td >string.rpartition(str)
</td>

<td >类似于 partition()函数,不过是从右边开始查找.
</td>
</tr>
<tr >

<td >[string.rstrip()](https://www.w3cschool.cn/python/att-string-rstrip.html)
</td>

<td >删除 string 字符串末尾的空格.
</td>
</tr>
<tr >

<td >**[string.split(str="", num=string.count(str))](https://www.w3cschool.cn/python/att-string-split.html)**
</td>

<td >以 str 为分隔符切片 string，如果 num有指定值，则仅分隔 num 个子字符串
</td>
</tr>
<tr >

<td >[string.splitlines(num=string.count('\n'))](https://www.w3cschool.cn/python/att-string-splitlines.html)
</td>

<td >按照行分隔，返回一个包含各行作为元素的列表，如果 num 指定则仅切片 num 个行.
</td>
</tr>
<tr >

<td >[string.startswith(obj, beg=0,end=len(string))](https://www.w3cschool.cn/python/att-string-startswith.html)
</td>

<td >检查字符串是否是以 obj 开头，是则返回 True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查.
</td>
</tr>
<tr >

<td >**[string.strip([obj])](https://www.w3cschool.cn/python/att-string-strip.html)**
</td>

<td >在 string 上执行 lstrip()和 rstrip()
</td>
</tr>
<tr >

<td >[string.swapcase()](https://www.w3cschool.cn/python/att-string-swapcase.html)
</td>

<td >翻转 string 中的大小写
</td>
</tr>
<tr >

<td >[string.title()](https://www.w3cschool.cn/python/att-string-title.html)
</td>

<td >返回"标题化"的 string,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())
</td>
</tr>
<tr >

<td >**[string.translate(str, del="")](https://www.w3cschool.cn/python/att-string-translate.html)**
</td>

<td >根据 str 给出的表(包含 256 个字符)转换 string 的字符,

要过滤掉的字符放到 del 参数中
</td>
</tr>
<tr >

<td >[string.upper()](https://www.w3cschool.cn/python/att-string-upper.html)
</td>

<td >转换 string 中的小写字母为大写
</td>
</tr>
<tr >

<td >[string.zfill(width)](https://www.w3cschool.cn/python/att-string-zfill.html)
</td>

<td >返回长度为 width 的字符串，原字符串 string 右对齐，前面填充0
</td>
</tr>
<tr >

<td >[string.isdecimal()](https://www.w3cschool.cn/python/att-string-isdecimal.html)
</td>

<td >isdecimal()方法检查字符串是否只包含十进制字符。这种方法只存在于unicode对象。
</td>
</tr>
</tbody>
</table>





# 一个问题：三引号时候的换行
**在三引号的时候，自己的分行就对应双引号的 \n 因此在写代码的时候要注意。**

例子1：
```python
str='abcd'\
'efgh'
print(str)
str="Hello\nworld"
print(str)
str="""
Hello
world"""
print(str)
```
输出：

```text
abcdefgh
Hello
world

Hello
world
```



# REF
  1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)
  2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)
