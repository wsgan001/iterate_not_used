# Python 正则表达式 regex


# REF

1. [python基础教程 w3cschool](https://www.w3cschool.cn/python/)
2. [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)
3. [Python正则表达式匹配反斜杠“\”](https://blog.csdn.net/jinixin/article/details/56705284)




# TODO






  * **要把    [python中的正则表达式](http://106.15.37.116/2018/03/23/python-re/)  合并进来**


  * **讲的还是不够详细，很多特殊的点都没有触及到，在使用的时候还是会经常卡住。**




# MOTIVE





* * *





## Python正则表达式


正则表达式是一个特殊的字符序列，它能帮助你方便的检查一个字符串是否与某种模式匹配。Python 自1.5版本起增加了re 模块，它提供 Perl 风格的正则表达式模式。

re 模块使 Python 语言拥有全部的正则表达式功能。

compile 函数根据一个模式字符串和可选的标志参数生成一个正则表达式对象。该对象拥有一系列方法用于正则表达式匹配和替换。

re 模块也提供了与这些方法功能完全一致的函数，这些函数使用一个模式字符串做为它们的第一个参数。

本章节主要介绍Python中常用的正则表达式处理函数。



---





## re.match函数


re.match 尝试从字符串的起始位置匹配一个模式，如果不是起始位置匹配成功的话，match()就返回none。

**函数语法**：


    re.match(pattern, string, flags=0)



函数参数说明：
<table class="reference" >
<tbody >
<tr >
参数
描述
</tr>
<tr >

<td >pattern
</td>

<td >匹配的正则表达式
</td>
</tr>
<tr >

<td >string
</td>

<td >要匹配的字符串。
</td>
</tr>
<tr >

<td >flags
</td>

<td >标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。
</td>
</tr>
</tbody>
</table>
匹配成功re.match方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。
<table class="reference" >
<tbody >
<tr >
匹配对象方法
描述
</tr>
<tr >

<td >group(num=0)
</td>

<td >匹配的整个表达式的字符串，group() 可以一次输入多个组号，在这种情况下它将返回一个包含那些组所对应值的元组。
</td>
</tr>
<tr >

<td >groups()
</td>

<td >返回一个包含所有小组字符串的元组，从 1 到 所含的小组号。
</td>
</tr>
</tbody>
</table>
实例：


    #!/usr/bin/python
    import re

    line = "Cats are smarter than dogs"

    matchObj = re.match( r'(.*) are (.*?) .*', line, re.M|re.I)

    if matchObj:
       print "matchObj.group() : ", matchObj.group()
       print "matchObj.group(1) : ", matchObj.group(1)
       print "matchObj.group(2) : ", matchObj.group(2)
    else:
       print "No match!!"



以上实例执行结果如下：


    matchObj.group() :  Cats are smarter than dogs
    matchObj.group(1) :  Cats
    matchObj.group(2) :  smarter






* * *





## re.search方法


re.search 会在字符串内查找模式匹配，直到找到第一个匹配。

函数语法：


    re.search(pattern, string, flags=0)



函数参数说明：
<table class="reference" >
<tbody >
<tr >
参数
描述
</tr>
<tr >

<td >pattern
</td>

<td >匹配的正则表达式
</td>
</tr>
<tr >

<td >string
</td>

<td >要匹配的字符串。
</td>
</tr>
<tr >

<td >flags
</td>

<td >标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。
</td>
</tr>
</tbody>
</table>
匹配成功re.search方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。
<table class="reference" >
<tbody >
<tr >
匹配对象方法
描述
</tr>
<tr >

<td >group(num=0)
</td>

<td >匹配的整个表达式的字符串，group() 可以一次输入多个组号，在这种情况下它将返回一个包含那些组所对应值的元组。
</td>
</tr>
<tr >

<td >groups()
</td>

<td >返回一个包含所有小组字符串的元组，从 1 到 所含的小组号。
</td>
</tr>
</tbody>
</table>
实例：


    #!/usr/bin/python
    import re

    line = "Cats are smarter than dogs";

    matchObj = re.search( r'(.*) are (.*?) .*', line, re.M|re.I)

    if matchObj:
       print "searchObj.group() : ", searchObj.group()
       print "searchObj.group(1) : ", searchObj.group(1)
       print "searchObj.group(2) : ", searchObj.group(2)
    else:
       print "Nothing found!!"



以上实例执行结果如下：


    searchObj.group() :  Cats are smarter than dogs
    searchObj.group(1) :  Cats
    searchObj.group(2) :  smarter






* * *





## re.match与re.search的区别


re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。

实例：


    #!/usr/bin/python
    import re

    line = "Cats are smarter than dogs";

    matchObj = re.match( r'dogs', line, re.M|re.I)
    if matchObj:
       print "match --> matchObj.group() : ", matchObj.group()
    else:
       print "No match!!"

    matchObj = re.search( r'dogs', line, re.M|re.I)
    if matchObj:
       print "search --> matchObj.group() : ", matchObj.group()
    else:
       print "No match!!"



以上实例运行结果如下：


    No match!!
    search --> matchObj.group() :  dogs






* * *





## 检索和替换


Python 的 re 模块提供了 re.sub 用于替换字符串中的匹配项。

语法：


    re.sub(pattern, repl, string, max=0)



返回的字符串是在字符串中用 RE 最左边不重复的匹配来替换。如果模式没有发现，字符将被没有改变地返回。

可选参数 count 是模式匹配后替换的最大次数；count 必须是非负整数。缺省值是 0 表示替换所有的匹配。

实例：


    #!/usr/bin/python
    import re

    phone = "2004-959-559 # 这是一个电话号码"

    # 删除注释
    num = re.sub(r'#.*$', "", phone)
    print "电话号码 : ", num

    # 移除非数字的内容
    num = re.sub(r'\D', "", phone)
    print "电话号码 : ", num



以上实例执行结果如下：


    电话号码 :  2004-959-559
    电话号码 :  2004959559





##


OK，上面的这个sub例子还是比较简单的，之前我遇到一个问题，把文档中的：


    '\(\[ P=\left[\begin{matrix}  \]\)'


替换为：


    '\[ P=\left[\begin{matrix}  \]'


这个看起来没有什么麻烦的，但是，它关键在于 \ 和 ( 这两个符号的处理，因为再正则表达式中，这些都是有特含义的，之前总是写不对，后来看了网上，说，**这种特殊字符在匹配的时候，要把这些特殊字符前面都要加上反斜杠**，这才可以：


    aaa='\(\[ P=\left[\begin{matrix}  \]\)'
    re.sub(r'\\\(\\\[(.*?)\\\]\\\)',r'\[ \1 \]',aaa)




## 输出：




    '\\[  P=\\left[\x08egin{matrix}   \\]'


这样才是正确的。**不过有一点还是很奇怪，为什么 sub 中的 pattern 中要把反斜线也要加上反斜杠，但是 repl 中只需要把中括号添加反斜杠？**




## 正则表达式修饰符 - 可选标志


正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志：
<table class="reference" >
<tbody >
<tr >
修饰符
描述
</tr>
<tr >

<td >re.I
</td>

<td >使匹配对大小写不敏感
</td>
</tr>
<tr >

<td >re.L
</td>

<td >做本地化识别（locale-aware）匹配
</td>
</tr>
<tr >

<td >re.M
</td>

<td >多行匹配，影响 ^ 和 $
</td>
</tr>
<tr >

<td >re.S
</td>

<td >使 . 匹配包括换行在内的所有字符
</td>
</tr>
<tr >

<td >re.U
</td>

<td >根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.
</td>
</tr>
<tr >

<td >re.X
</td>

<td >该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。
</td>
</tr>
</tbody>
</table>



* * *





## 正则表达式模式


模式字符串使用特殊的语法来表示一个正则表达式：

字母和数字表示他们自身。一个正则表达式模式中的字母和数字匹配同样的字符串。

多数字母和数字前加一个反斜杠时会拥有不同的含义。

标点符号只有被转义时才匹配自身，否则它们表示特殊的含义。

反斜杠本身需要使用反斜杠转义。

由于正则表达式通常都包含反斜杠，所以你最好使用原始字符串来表示它们。模式元素(如 r'/t'，等价于'//t')匹配相应的特殊字符。

下表列出了正则表达式模式语法中的特殊元素。如果你使用模式的同时提供了可选的标志参数，某些模式元素的含义会改变。
<table class="reference" >
<tbody >
<tr >
模式
描述
</tr>
<tr >

<td >^
</td>

<td >匹配字符串的开头
</td>
</tr>
<tr >

<td >$
</td>

<td >匹配字符串的末尾。
</td>
</tr>
<tr >

<td >.
</td>

<td >匹配任意字符，除了换行符，当re.DOTALL标记被指定时，则可以匹配包括换行符的任意字符。
</td>
</tr>
<tr >

<td >[...]
</td>

<td >用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k'
</td>
</tr>
<tr >

<td >[^...]
</td>

<td >不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符。
</td>
</tr>
<tr >

<td >re*
</td>

<td >匹配0个或多个的表达式。
</td>
</tr>
<tr >

<td >re+
</td>

<td >匹配1个或多个的表达式。
</td>
</tr>
<tr >

<td >re?
</td>

<td >匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式
</td>
</tr>
<tr >

<td >re{ n}
</td>

<td >
</td>
</tr>
<tr >

<td >re{ n,}
</td>

<td >精确匹配n个前面表达式。
</td>
</tr>
<tr >

<td >re{ n, m}
</td>

<td >匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式
</td>
</tr>
<tr >

<td >a| b
</td>

<td >匹配a或b
</td>
</tr>
<tr >

<td >(re)
</td>

<td >G匹配括号内的表达式，也表示一个组
</td>
</tr>
<tr >

<td >(?imx)
</td>

<td >正则表达式包含三种可选标志：i, m, 或 x 。只影响括号中的区域。
</td>
</tr>
<tr >

<td >(?-imx)
</td>

<td >正则表达式关闭 i, m, 或 x 可选标志。只影响括号中的区域。
</td>
</tr>
<tr >

<td >(?: re)
</td>

<td >类似 (...), 但是不表示一个组
</td>
</tr>
<tr >

<td >(?imx: re)
</td>

<td >在括号中使用i, m, 或 x 可选标志
</td>
</tr>
<tr >

<td >(?-imx: re)
</td>

<td >在括号中不使用i, m, 或 x 可选标志
</td>
</tr>
<tr >

<td >(?#...)
</td>

<td >注释.
</td>
</tr>
<tr >

<td >(?= re)
</td>

<td >前向肯定界定符。如果所含正则表达式，以 ... 表示，在当前位置成功匹配时成功，否则失败。但一旦所含表达式已经尝试，匹配引擎根本没有提高；模式的剩余部分还要尝试界定符的右边。
</td>
</tr>
<tr >

<td >(?! re)
</td>

<td >前向否定界定符。与肯定界定符相反；当所含表达式不能在字符串当前位置匹配时成功
</td>
</tr>
<tr >

<td >(?> re)
</td>

<td >匹配的独立模式，省去回溯。
</td>
</tr>
<tr >

<td >\w
</td>

<td >匹配字母数字
</td>
</tr>
<tr >

<td >\W
</td>

<td >匹配非字母数字
</td>
</tr>
<tr >

<td >\s
</td>

<td >匹配任意空白字符，等价于 [\t\n\r\f].
</td>
</tr>
<tr >

<td >\S
</td>

<td >匹配任意非空字符
</td>
</tr>
<tr >

<td >\d
</td>

<td >匹配任意数字，等价于 [0-9].
</td>
</tr>
<tr >

<td >\D
</td>

<td >匹配任意非数字
</td>
</tr>
<tr >

<td >\A
</td>

<td >匹配字符串开始
</td>
</tr>
<tr >

<td >\Z
</td>

<td >匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串。c
</td>
</tr>
<tr >

<td >\z
</td>

<td >匹配字符串结束
</td>
</tr>
<tr >

<td >\G
</td>

<td >匹配最后匹配完成的位置。
</td>
</tr>
<tr >

<td >\b
</td>

<td >匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。
</td>
</tr>
<tr >

<td >\B
</td>

<td >匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。
</td>
</tr>
<tr >

<td >\n, \t, 等.
</td>

<td >匹配一个换行符。匹配一个制表符。等
</td>
</tr>
<tr >

<td >\1...\9
</td>

<td >匹配第n个分组的子表达式。
</td>
</tr>
<tr >

<td >\10
</td>

<td >匹配第n个分组的子表达式，如果它经匹配。否则指的是八进制字符码的表达式。
</td>
</tr>
</tbody>
</table>



* * *





## 正则表达式实例




#### 字符匹配


<table class="reference" >
<tbody >
<tr >
实例
描述
</tr>
<tr >

<td >python
</td>

<td >匹配 "python".
</td>
</tr>
</tbody>
</table>


#### 字符类


<table class="reference" >
<tbody >
<tr >
实例
描述
</tr>
<tr >

<td >[Pp]ython
</td>

<td >匹配 "Python" 或 "python"
</td>
</tr>
<tr >

<td >rub[ye]
</td>

<td >匹配 "ruby" 或 "rube"
</td>
</tr>
<tr >

<td >[aeiou]
</td>

<td >匹配中括号内的任意一个字母
</td>
</tr>
<tr >

<td >[0-9]
</td>

<td >匹配任何数字。类似于 [0123456789]
</td>
</tr>
<tr >

<td >[a-z]
</td>

<td >匹配任何小写字母
</td>
</tr>
<tr >

<td >[A-Z]
</td>

<td >匹配任何大写字母
</td>
</tr>
<tr >

<td >[a-zA-Z0-9]
</td>

<td >匹配任何字母及数字
</td>
</tr>
<tr >

<td >[^aeiou]
</td>

<td >除了aeiou字母以外的所有字符
</td>
</tr>
<tr >

<td >[^0-9]
</td>

<td >匹配除了数字外的字符
</td>
</tr>
</tbody>
</table>


#### 特殊字符类


<table class="reference" >
<tbody >
<tr >
实例
描述
</tr>
<tr >

<td >.
</td>

<td >匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用象 '[.\n]' 的模式。
</td>
</tr>
<tr >

<td >\d
</td>

<td >匹配一个数字字符。等价于 [0-9]。
</td>
</tr>
<tr >

<td >\D
</td>

<td >匹配一个非数字字符。等价于 [^0-9]。
</td>
</tr>
<tr >

<td >\s
</td>

<td >匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。
</td>
</tr>
<tr >

<td >\S
</td>

<td >匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。
</td>
</tr>
<tr >

<td >\w
</td>

<td >匹配包括下划线的任何单词字符。等价于'[A-Za-z0-9_]'。
</td>
</tr>
<tr >

<td >\W
</td>

<td >匹配任何非单词字符。等价于 '[^A-Za-z0-9_]'。
</td>
</tr>
</tbody>
</table>






















* * *





# COMMENT
