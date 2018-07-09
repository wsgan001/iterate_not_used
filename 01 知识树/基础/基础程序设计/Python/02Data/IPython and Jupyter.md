- 一直想知道，怎么把 ipython 格式的项目加入到 Jupyter notebook 里面去看，一直没弄明白。
- 而且，这个 Jupyter notebook 与Ipython 到底有什么区别和联系？
- 而且，正规的运行、调试、退出 的流程是什么样的？
- 而且，怎么在里面使用 tensorflow ？与正常使用有什么区别吗？


Jupyter 可以批量转换 ：jupyter nbconvert --to markdown 1.*.ipynb    将以1.开头的ipynb文件转化成了md格式。


- **IPython是Python科学计算标准工具集的组成部分**,它可以把很多东西联系到一起,有点类似一个增强版的Python shell.
- **目的是为了提高编程，测试和调试Python代码的速度**，好像很多国外的大学教授，还有Google大牛都很喜欢用IPython，确实很方便，至少我在分析数据的时候，也是用这个工具的，而且不用print,回车就能打印












本书的笔记就是使用Jupyter来制作的，详细内容可以查看下面的资料。

- [IPython 与 Jupyter 相关概念介绍](https://blog.windrunner.me/python/jupyter.html)
- [IPython和Jupyter Notebook](http://www.jianshu.com/p/9c4d3a7f3ca9)
- [Jupyter官网](http://jupyter.org/)
- [Jupyter Notebook Tutorial: Introduction, Setup, and Walkthrough(视频，需要翻墙)](https://www.youtube.com/watch?v=HW29067qVWk)

IPython是Python科学计算标准工具集的组成部分，它将其他所有的东西联系到了一起。 它为交互式和探索式计算提供了一个强健而高效的环境。它是一个增强的Python shell, 目的是提髙编写、测诚、调试Python代码的速度，它主要用于交互式数据处理和利用 matplotlib对数据进行可视化处理。我在用Python编程时，经常会用到IPy thon，包括运 行、调试和测试代码。$\color{red}\large \textbf{说实话，一直没明白为什么IPython都说好，感觉割裂了代码呀？}$ $\color{red}\large \textbf{确认下到底好在哪里。}$

除标准的基干终端的IPython shell外，该项目还提供了：

- 一个类似于Mathematica的HTML笔记本（通过Web浏览器连接IPython，稍后将对 此进行详细介绍）。
- 一个基于Qt框架的GUI控制台，其中含有绘图、多行编辑以及语法高亮显示等 功能。$\color{red}\large \textbf{有这个吗？}$
- 用于交互式并行和分布式计算的基础架构。$\color{red}\large \textbf{有这个分布式i计算架构吗？确认下，怎么用的？}$

我将在一章中专门讲解IPython，详细地介绍其大部分功能。强烈建议在阅读本书的过程中使用IPython。







# 我们先看看 Python解释器 是什么样的

 The Python Interpreter

Python是一门解释性语言。Python的解释器一次只能运行一个命令。标准的Python解释器环境可以用通过输入python进入（在 terminal 中输入python后，就能进入解释器）：

![img](../../../../%E7%9F%A5%E8%AF%86%E6%A0%91/%E5%9F%BA%E7%A1%80/%E5%9F%BA%E7%A1%80%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Python/assets/xua22.png)

`>>>`是提示符（prompt），告诉你可以输入指令。如果想要退出，可以输入`exit()`或者按Ctrl-D。

运行python程序也很简单，输入一个终端python+.py文件即可。假设我们的hello_world.py文件中有下面的内容

```
print('Hello world')
```

可以通过下面的命令来运行（我们需要先 cd 到 hello_world.py 所在的文件夹）：

![img](../../../../%E7%9F%A5%E8%AF%86%E6%A0%91/%E5%9F%BA%E7%A1%80/%E5%9F%BA%E7%A1%80%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Python/assets/x09fn.png)

# 我们再看看 IPython

在做科学计算和数据分析的时候，我们通常使用 IPython，这是一个强化版的python 解释器，当我们使用`%run`命令的时候，IPython会按执行代码的方式来执行文件，可以让我们直观地看到交互的结果：

![img](../../../../%E7%9F%A5%E8%AF%86%E6%A0%91/%E5%9F%BA%E7%A1%80/%E5%9F%BA%E7%A1%80%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Python/assets/gt33b.png)

默认的IPython提示符会显示数字，比如图片中的`In [2]:`，而不是普通的`>>>`提示符。





## IPython基础

### 1 Running the IPython Shell （运行IPython Shell）

可以通过命令行启动IPython，就像启动标准的Python解释器一样，直接在terminal 中键入 ipython，回车即可。当然，其实我们用的 Jupyter Notebook 默认其实就是ipython。

```t
Microsoft Windows [版本 10.0.14393]
(c) 2016 Microsoft Corporation。保留所有权利。

C:\Users\evo>ipython
Python 3.6.0 |Anaconda custom (64-bit)| (default, Dec 23 2016, 11:57:41) [MSC v.1900 64 bit (AMD64)]
Type "copyright", "credits" or "license" for more information.

IPython 5.1.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.
```

可以通过 Ctrl+D  或者输入 exit() 回车 退出。推荐使用 exit() 的方式退出。

OK，我们简单的输入一些：

```
In [4]: import numpy as np

In [5]: from numpy.random import randn

In [6]: data={i:randn() for i in range(7)}

In [7]: data
Out[7]:
{0: 0.09983882299644838,
 1: 0.23044291184198076,
 2: 0.27743454846003074,
 3: 0.5327938571118681,
 4: 0.9031292384369758,
 5: -0.1388919525681569,
 6: 0.004839159673685846}

In [8]:
```

因为我们用的是ipython，所以上面输出的字典对象可读性很好，每行一个key对应一个value。但如果是在标准的Python解释器里打印上面的字典的话，可读性就会变差了，输出会是：

```
>>> import numpy as np
>>> from numpy.random import randn
>>> data={i:randn() for i in range(7)}
>>> data
{0: -0.8148857639421023, 1: -2.4752398151601684, 2: 1.5964985996911942, 3: 0.0922868900780165, 4: 1.738419969472046, 5: 0.20134093856403773, 6: -0.6061635268108886}
```

这样的结果就很不方便查看。$\color{red}\large \textbf{怪不得使用 ipython，原来还有这个优点}$

# OK，我们再看看 Jupyter Notebook

Jupyter notebook 是一个基于网页的代码记事本，也是从 IPython 项目中开发出来的。

这个工具在数据分析方面真得非常好用，一定要好好学习一个这个工具。$\color{red}\large \textbf{后面的REF里面的教程要整合进来。}$

bilibili 上也可以搜索 'Jupyter notebook' 。



## Tab键自动补全功能

只要按下 tab 键，当前命名空间中任何与已输入的字符串相匹配的变量（对象，函数）就会被找出来：

像许多其它的IDE 一样，Jupyter notebook 也是有tab 键自动补全的。嗯不错。

主要可以用在这么几个地方：

```
an_apple=27
an_example=2
an<Tab>

b = [1, 2, 3]
an.<Tab>

import datetime
datetime.<Tab>

path = '../datasets/<Tab>'
```

注意：上面的<Tab>是指按下 Tab 键的意思。

这样的自动填充就非常的方便，可以显著减少敲击键盘的次数。

Tab自动补全还可以用于函数关键字参数（包括等号=）

## Introspection（内省）

在变量的前面或后面加上一个问号`?`，就可以讲有关该对象的一些通用信息显示出来：这个功能叫做对象内省（object introspection)。

```
b=[1,2,3]
b?
```

然后，点运行，它会以一种提示的方式告诉你关于 b 的一些信息：

```
Type:        list
String form: [1, 2, 3]
Length:      3
Docstring:
list() -> new empty list
list(iterable) -> new list initialized from iterable's items
```

不错不错，这个真的很方便，不知道pycharm 里面有没有类似的。$\color{red}\large \textbf{确认下。}$

如果该对象是一个函数或实例方法，则其 docstring 也会被显示出来：

```
def add_number(a, b):
    """
    Add two nummbers together

    Return
    ------
    the_sum: type of arguments
    """
    return a + b
add_number?
add_number??
```

提示为：

```
Signature: add_number(a, b)
Docstring:
Add two nummbers together

Return
------
the_sum: type of arguments
File:      c:\users\evo\desktop\pydata-notebook-94ab37630b0151293148d127c34b1190c6ace403\chapter-02\<ipython-input-8-5bcdfa534a08>
Type:      function
```

非常好！

还可以用两个问号来显示该函数的源码：

```
def add_number(a, b):
    """
    Add two nummbers together

    Return
    ------
    the_sum: type of arguments
    """
    return a + b
add_number??
```

提示为：

```
Signature: add_number(a, b)
Source:
def add_number(a, b):
    """
    Add two nummbers together

    Return
    ------
    the_sum: type of arguments
    """
    return a + b
File:      c:\users\evo\desktop\pydata-notebook-94ab37630b0151293148d127c34b1190c6ace403\chapter-02\<ipython-input-10-2d71a0ab467f>
Type:      function
```

不错的。

还有一个用法，配合通配符`*`，即可显示出所有与该通配符表达式相匹配的名称。

比如：

```
import numpy as np
np.*load*?
```

提示的信息为：

```
np.__loader__
np.load
np.loads
np.loadtxt
np.pkgload
```

很好！



## %run 命令

就是比如，我写了一个py 脚本：

```
def f(x, y, z):
    return (x + y) / z

a = 5
b = 6
c = 7.5

result = f(a, b, c)
```

然后放到了一个地方，我再我的 .ipynb 里面可以写下这句：

```
%run ipython_script_test.py  # 这里假设ipython_script_test.py在当前路径
```

然后运行，这时候  ipython_script_test.py 就被成功运行了，它里面的所有变量也都可以直接访问了。

如果上面这个  ipython_script_test.py 脚本需要用到命令行参数（通过sys.argv访问），那么可以将参数放到文件路径的后面，就像在命令行上执行那样。$\color{red}\large \textbf{对于这个我还是有些不确定，对于可变参数还是心里没底，要确认下。}$

OK，这个地方我们提一下怎么中断正在执行的代码：任何代码在执行时，只要按下“Ctrl-C”，就会应发一个KeyboardInterrupt。绝大部分情况下，python程序都将立即停止执行。 $\color{red}\large \textbf{什么是绝大部分情况下？}$

## 执行剪贴板中的代码

在IPython中执行代码最简单的方式是粘贴剪贴板中的代码。比如我们希望一段一段地执行脚本，以便查看各个阶段所加载的数据以及产生的结果。

多数情况下，我们能用“Ctrl-Shift-V”讲剪贴板中的代码片段粘贴出来。但这不是万能的。因为这种粘贴方式模拟的是在IPython中逐行输入代码，换行符会被处理成`<return>`，也就是说，如果代码中有所进，且有空行，IPython会认为缩进在空行哪里结束了。当执行到缩进块后面的代码时，会引发一个IndentationError。例如下面这段代码：

```
x = 5
y = 7
if x > 5:
    x += 1
    y = 8
```

直接粘贴是不行的。（具体的效果大家可以打开terminal，直接试一下。不过这里因为版本的缘故，不会出现书中的错误提示）==现在好像直接复制也是可以的，不过代码换行的地方显示的有些怪。确认下。==

```
In [1]: x = 5^M
   ...: y = 7^M
   ...: if x > 5:^M
   ...:     x += 1^M
   ...:     ^M
   ...:     y = 8
   ...:

In [2]: y
Out[2]: 7

In [3]: x
Out[3]: 5
```

但我们可以利用%paste和%cpaste这两个魔术函数。%paste可以承载剪贴板中的一切文本，并在shell中以整体形势执行：

```
In [2]: %paste
x = 5
y = 7
if x > 5:
    x += 1
    y = 8

## -- End pasted text --

In [3]: x
Out[3]: 5
```

嗯，不错的。

这里要注意一点，先把复制代码，然后在 terminal 中输入 %paste 回车。这个命令会自动执行剪贴板上复制的内容。

%cpaste和%paste差不多，只不过它多出一个用于粘贴代码的特殊提示符而已：

```
In [1]: %cpaste
Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
:
```

不知道为什么，按回车的时候，python 卡死掉了。$\color{red}\large \textbf{确认下原因。}$

建议一直使用 %cpaste，因为你可以自己决定是否执行代码，想粘贴多少粘贴多少。

==PyCharm 的Console 也是 IPython 吗？还是就是普通的Python Interpreter？==

## terminal 键盘快捷键

这个还没怎么用过。==在实际中真的会使用这个吗？应该是都是用 Jupyternotebook了吧？==

![快捷键](../../../../%E7%9F%A5%E8%AF%86%E6%A0%91/%E5%9F%BA%E7%A1%80/%E5%9F%BA%E7%A1%80%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Python/assets/006tKfTcgy1fkqy42b68sj30j50hgwil.jpg)



异常和跟踪

如果%run某段脚本或执行某条语句时发生了异常，IPython默认会输出整个调用栈跟踪（traceback），其中还会富商调用栈个点附近的几行代码作为上下文参考。

## 关于魔术命令

Magic Command，这些命令能提供便利。这些命令是以%为前缀的。例如，可以通过%timeit这个来检测任意 python 语句的执行时间：

```
a = np.random.randn(100, 100)
%timeit np.dot(a, a)
```

输出：

```
The slowest run took 5.60 times longer than the fastest. This could mean that an intermediate result is being cached.
10000 loops, best of 3: 60.1 µs per loop
```

嗯，每次的输出都是不一样的。

这些魔术命令也是可以通过 <?> 来得到它们的信息的：

```
%reset?
```

输出：

```
Docstring:
Resets the namespace by removing all names defined by the user, if
called without arguments, or by removing some types of objects, such
as everything currently in IPython's In[] and Out[] containers (see
the parameters for details).

Parameters
----------
.... 此处略去 ....

Notes
-----
Calling this magic from clients that do not implement standard input,
such as the ipython notebook interface, will reset the namespace
without confirmation.
File:      e:\11.programfiles\anaconda3\lib\site-packages\ipython\core\magics\namespace.py
```

嗯，不错。

实际上，魔术命令默认是可以不带百分号使用的，只要没有定义与其同名的变量即可。这个技术叫做 automagic，可以通过 %automagic 打开或关闭。

![IPython magic command](../../../../%E7%9F%A5%E8%AF%86%E6%A0%91/%E5%9F%BA%E7%A1%80/%E5%9F%BA%E7%A1%80%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Python/assets/006tKfTcgy1fkr1i9kbcij30jf0na0yr.jpg)

==真不知道什么时候会使用这些。==

## Matplotlib整合

其实上面我们也用到了，通过使用 `%matplotlib` 能够直接在jupyter中画图。

```
%matplotlib inline
import matplotlib.pyplot as plt
plt.plot(np.random.randn(50).cumsum())
```

输出：

```
[<matplotlib.lines.Line2D at 0x17798d86748>]
```

同时在block 内输出了一个图：

![1528970956542](assets/1528970956542.png)













# REF

* [Jupyter Notebook 快速入门（上）](http://www.codingpy.com/article/getting-started-with-jupyter-notebook-part-1/) 这个是翻译者推荐的Jupyter Notebook 的教程，说是非常好的，要把它总结进来。
