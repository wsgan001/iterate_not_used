# sphinx 转 markdown


sphinx 是 rst 后缀的文件

sphinx等工具更适合于有多个章节、比较大的文档的编写，而markdown则比较适合于单篇、比较短的文章的编写，基本就是latex中的book和chapter的区别，sphinx比较适合写book，而markdown比较适合写chapter。markdown近来比较多地被应用于博客的编写中。

那么如果我们对某领域进行了系统学习，并用sphinx进行了的学习记录，如何发表成博客呢？我们可以按章节把sphinx文档进行拆分，每个章节发一篇博客，最终组成一个系列的博客。达成这一目标，最简单的方式是，把每个章节的rst格式的文件转成markdown语法的文件，并发表在支持markdown语法的博客上(wordpress也有markdown的插件)。

目前，网上似乎没有合适的工具可以把rst转为markdown。不过，我们可以自己动手，使用python和正则表达式来进行转换，python正则的使用，见本人博客[ [ python入门系列(3) – python语言基础语法 \]。](http://blog.csdn.net/weishantc/article/details/45647665)

这里，我们只对一些比较常用的sphinx语法进行处理，sphinx的使用，见本人的另一篇博客[ [ Sphinx-doc编写文档 \]](http://blog.csdn.net/weishantc/article/details/46729103)

下面针对比较常用的几个标记进行分别讨论：

- 行内标记
- 列表
- 标题
- 标注
- 代码段
- 代码包含
- 图片
- 链接

其中行内标记、列表在sphinx和markdown的语法一样，不做处理。图片标记因为主要用于博客，图片要手动单独上传，所以图片的也不处理。代码包含因为博客上无法上传源码文件，所以也不处理。链接处理比较麻烦，所以也不处理。所以我们仅处理以下内容：

- 标题
- *标注
- 代码段

### **标题**

对于标题，我们只处理一级标题，二级标题，三级标题。在sphinx中，三种标题形如:

```
一级标题
======

二级标题
------

三级标题3
++++++
123456789
```

我们要把它转换为sphinx中的标题:

```
# **一级标题**
## **二级标题**
### **三级标题**123
```

为了效果，我们把每级标题的字体都用`**`加粗。同时，每个二级标题前面都加一条分割线。

#### **一级标题**

sphinx的一级标题的正则如下：

```python
t0Pattern = re.compile(u'''(^.*?)\n=+''', re.MULTILINE)1
```

正则中的sub置换方法为:

```python
def t0Repl(matched):
    rs = matched.groups()[0]
    rs = "# **%s**" % rs.strip()
    return rs1234
```

调用方法：

```python
tt = re.sub(t0Pattern, t0Repl, tt)1
```

其中tt为文件内容

#### **二级标题**

sphinx的二级标题的正则如下：

```python
t1Pattern = re.compile(u'''(^.*?)\n-+''', re.MULTILINE)1
```

正则中的sub置换方法为:

```python
def t1Repl(matched):
    rs = matched.groups()[0]
    rs = "\n---\n## **%s**" % rs.strip()
    return rs1234
```

调用方法：

```python
tt = re.sub(t1Pattern, t1Repl, tt)1
```

其中tt为文件内容

#### **三级标题**

sphinx的三级标题的正则如下：

```python
t2Pattern = re.compile(u'''(^.*?)\n\++''', re.MULTILINE)1
```

正则中的sub置换方法为:

```python
def t2Repl(matched):
    rs = matched.groups()[0]
    rs = "### **%s**" % rs.strip()
    return rs1234
```

调用方法：

```python
tt = re.sub(t2Pattern, t2Repl, tt)1
```

其中tt为文件内容

### **标注**

把sphinx中形如如下的格式：

```
.. note::

    python的字符串是不可修改的。如修改一个字符，应使用replace，或使用左边字串+新字符+右边字串拼接而成123
```

改成markdown中，如下的形式:

```
> python的字符串是不可修改的。如修改一个字符，应使用replace，或使用左边字串+新字符+右边字串拼接而成1
```

为了处理标注和代码段中的段落结构，即标注的内容或代码都是按4个空格缩进的，而在markdown中却不需要缩进。所以，我们先定义缩进的正则表达式：

```python
spacePattern = re.compile(u'''^(\s{4})(?=\s*\S)''', re.S|re.MULTILINE)1
```

sphinx中note使用如下正则：

```python
notePattern = re.compile(u'''..\s*?note\s*?::\s*?$\n\s*?\n(.*?)\n(?=^\S)''', re.S|re.MULTILINE)1
```

把标注的内容提取出来，并去掉每行的缩进，最后前面加`>`，sub置换函数为：

```python
def noteRepl(matched):
    rs = matched.groups()[0]
    rs = re.sub(spacePattern, "", rs)
    rs = "> %s\n\n" % rs.strip()
    return rs    12345
```

调用代码如下：

```python
tt = re.sub(notePattern, noteRepl, tt)1
```

其中tt为文件内容。

### **代码段**

显式的代码段比较复杂，我们只处理简单的代码段，即把sphinx中形如:

```
python使用如下语法定义list，list的元素类型可以不一样::

    >>> a = ['spam', 'eggs', 100, 1234]
    >>> a
    ['spam', 'eggs', 100, 1234]12345
```

的格式，转换为markdown中，如下的形式：

```
python使用如下语法定义list，list的元素类型可以不一样:
​```
>>> a = ['spam', 'eggs', 100, 1234]
>>> a
['spam', 'eggs', 100, 1234]
​```
1234567
```

code的识别，使用如下正则：

```python
codePattern = re.compile(u'''::\s*?$\n\s*?\n(.*?)(?=^\S)''', re.S|re.MULTILINE)1
```

提出取代码后，还是去掉每行的4个缩进，并在代码前后加上` ` `号，置换sub如下：

```python
def codeRepl(matched):
    rs = matched.groups()[0]
    rs = re.sub(spacePattern, "", rs)
    # 去掉末尾换行
    rs = rs.rstrip()+"\n"
    rs = "\n``` \n%s```\n\n" % rs
    return rs1234567
```

调用过程如下：

```python
tt = re.sub(codePattern, codeRepl, tt)1
```

tt仍为文件内容

### **完整代码**

```python
# encoding:utf8
'''
Created on 2015-5-6

@author: vincent
'''

#1. 序列一样，不用修改
#2. note -> 引用
#3. 代码引用 -> ```
#4. ---转为##
#5. ==转为###
#6. 图片引用、代码块引用 转换
#7.

import re
import sys,os
import shutil
from optparse import OptionParser

MSG_USAGE = "rst2md [-f <filename>][-d <dirpath>]"

t0Pattern = re.compile(u'''(^.*?)\n=+''', re.MULTILINE)
t1Pattern = re.compile(u'''(^.*?)\n-+''', re.MULTILINE)
t2Pattern = re.compile(u'''(^.*?)\n\++''', re.MULTILINE)
codePattern = re.compile(u'''::\s*?$\n\s*?\n(.*?)(?=^\S)''', re.S|re.MULTILINE)
spacePattern = re.compile(u'''^(\s{4})(?=\s*\S)''', re.S|re.MULTILINE)
codePattern2 = re.compile(u'''\s*?\d\d/\d\d/\d\d''', re.S|re.MULTILINE)
notePattern = re.compile(u'''..\s*?note\s*?::\s*?$\n\s*?\n(.*?)\n(?=^\S)''', re.S|re.MULTILINE)

includePattern = re.compile(u'''\s*?\d\d/\d\d/\d\d''', re.S|re.MULTILINE)
# 有序
listPattern1 = re.compile(u'''\s*?\d\d/\d\d/\d\d''', re.S|re.MULTILINE)
# 无序
listPattern2 = re.compile(u'''\s*?\d\d/\d\d/\d\d''', re.S|re.MULTILINE)

test1 = r'''如果一下子
类、对象
-------------------------

类的定义和实例化
+++++++++++++++++++++++

一个简单的例子如下::

    >>> class MyClass:
        """A simple class."""           #__doc__
        i=10000
        def __init__(self, p1, p2):     # 构造函数
            self.r = p1
            self.i = p2
        def f(self):
            print self.r+self.i

    >>> x = MyClass(3.0, -4.5)          # 实例化
    >>> x.r, x.i
    (3.0, -4.5)
    >>> x.f()
    -1.5

需要注意的是，定义一个类时，python会产生一个类对象。类对象中属性、函数可以修改::

    >>> del MyClass.i
    >>> del MyClass.f
    >>> MyClass.j=10000

> index如果没找到会抛出异常，find没找到返回-1

类对象中可以使用 __doc__打印类定义中的帮助字符串'''

def noteRepl(matched):
    rs = matched.groups()[0]
    rs = re.sub(spacePattern, "", rs)
    rs = "> %s\n\n" % rs.strip()
    return rs

def codeRepl(matched):
    rs = matched.groups()[0]
    rs = re.sub(spacePattern, "", rs)
    # 去掉末尾换行
    rs = rs.rstrip()+"\n"
    rs = "\n``` \n%s```\n\n" % rs
    return rs

def t0Repl(matched):
    rs = matched.groups()[0]
    rs = "# **%s**" % rs.strip()
    return rs

def t1Repl(matched):
    rs = matched.groups()[0]
    rs = "\n---\n## **%s**" % rs.strip()
    return rs

def t2Repl(matched):
    rs = matched.groups()[0]
    rs = "### **%s**" % rs.strip()
    return rs

def dealFile(f):
    fd = open(f,'r')
    ct = fd.read()
    fd.close()

    tt = ct
    tt = re.sub(notePattern, noteRepl, tt)
    tt = re.sub(t0Pattern, t0Repl, tt)
    tt = re.sub(t1Pattern, t1Repl, tt)
    tt = re.sub(t2Pattern, t2Repl, tt)
    tt = re.sub(codePattern, codeRepl, tt)

    ofd = open(f.replace(".rst", ".md"), 'w')
    ofd.write(tt)
    ofd.close()

    pass

def dealDir(d):
    for root, dirs, files in os.walk(d):
#        for nd in [root+os.sep+d for d in dirs]:
#            dealDir(nd)
        for nf in [root+os.sep+f for f in files if f.find('.rst')>0]:
            dealFile(nf)


def work():
    optParser = OptionParser(MSG_USAGE)
    optParser.add_option("-f","--file",action = "store",type="string",dest = "filename")
    optParser.add_option("-d","--dir",action = "store",type="string",dest = "dir")

    options, args = optParser.parse_args()
    day = None

    if len(sys.argv)<=1:
        optParser.print_help()
        exit()
    if options.filename:
        dealFile(options.filename)
    elif options.dir:
        dealDir(options.dir)
    pass

def test():
#     tt = test2
#     tt = re.sub(notePattern, noteRepl, tt)
#     tt = re.sub(t0Pattern, t0Repl, tt)
#     tt = re.sub(t1Pattern, t1Repl, tt)
#     tt = re.sub(t2Pattern, t2Repl, tt)
#     tt = re.sub(codePattern, codeRepl, tt)
#     print tt
    f = r'''D:\Learn\python\source\chapter4.rst'''
    dealFile(f)
    pass

if __name__ == '__main__':
#     test()
    work()
    pass
```
