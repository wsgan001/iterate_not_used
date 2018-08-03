[TOC]

# TODO

- ==需要进行拆分，==
- ==要补全，最好把 python 手册全部放过来，不然根本没办法全面掌握。==
- ==要理解python 的跟本设计理念，设计思想。为什么这样设计。==

# 1 语言语义（Language Semantics）

## 缩进，而不是括号

Python使用空格（tabs or spaces)来组织代码结构，而不是像R，C++，Java那样用括号。

建议使用四个空格来作为默认的缩进，设置tab键为四个空格。==PyCharm 上面怎么设置的？要吧 pycharm 的说明标注这个地方==

另外可以用分号隔开多个语句：

```
a = 5; b = 6; c = 7
```

## 所有事物都是对象（object）

在python中，number，string，data structure，function，class，module都有自己的“box”，即可以理解为Python object（对象）。下面所有的对象直接用object来指代。

## 调用函数和对象的方法

用圆括号

```
result = f(x, y, z)
```

## 动态参考，强类型

不像C++，Java之类的语言，python中object reference是没有自带类型的。但是可以通过type来查看类型：

```
a = 5
type(a)
```

输出：

```
int
```

类型信息存储在这个对象本身。==怎么存储的？==

而python可以看做是强类型，即每一个object都有一个明确的类型。所以下面的运算不会成立。但是Visual Basic会把'5'变为整数（int），而JavaScript会把5变为字符串（string）

```
'5' + 5
```
输出：

```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-5-f9dbf5f0b234> in <module>()
----> 1 '5' + 5

TypeError: Can't convert 'int' object to str implicitly
```

不过像是int与float之间倒是会隐式转换：

```
a = 4.5
b = 2
print('a is {0}, b is {1}'.format(type(a), type(b)))
a / b
```

输出：

```
a is <class 'float'>, b is <class 'int'>
2.25
```

因为知道每个Object的类型很重要，我们可以用 `isinstance` 函数来查看object的类型

```
a = 5
isinstance(a, int)
```

输出：

```
True
```

查看 a、b是否是 int 或 float 类型：==这也可以！！==

```
a = 5; b = 4.5
isinstance(a, (int, float))
isinstance(b, (int, float))
```

输出：

```
True
True
```

## 属性和方法

属性（Attributes）指在当前这个object里，还有一些其他的python object。方法（method）指当前这个object自带的一些函数，这些函数可以访问object里的内部数据。

通过`obj.attribute_name`可以查看：

```
a = 'foo'
a.<Press Tab>
```

可以通过`getattr`函数来访问属性和方法：

```
getattr(a, 'split')
```

输出：

```
<function str.split>
```

## Duck typing

在程序设计中，鸭子类型（英语：duck typing）是动态类型的一种风格。在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由"当前方法和属性的集合"决定。这个概念的名字来源于由James Whitcomb Riley提出的鸭子测试（见下面的“历史”章节），“鸭子测试”可以这样表述：

> “当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。”

在鸭子类型中，关注的不是对象的类型本身，而是它是如何使用的。==对于这个鸭子类型还是不是很理解==

比如，如果一个object能够实现迭代原则，那么这个object就是可迭代的。我们可以看这个object是否有`__iter__`这个magic method。或者自己写一个iter function来检测：


```
def isiterable(obj):
    try:
        iter(obj)
        return True
    except TypeError: # not iterable
        return False
isiterable('a string')
isiterable([1, 2, 3])
isiterable(5)
```
输出：
```
True
True
False
```

这个功能多用于写一些能接受多种类型的函数。比如我们写一个函数，用接收任何序列（list, tuple, ndarray) 甚至一个迭代器。如果接收的不是一个list，那么我们就人为将其转变为一个list：

```
if not isinstance(x, list) and isiterable(x): # 如果x不是list，且x可迭代
    x = list(x) # 转变x为list
```

## Import（导入）

比如我创建了一个some_module.py的文件，里面写着：

In [22]:

```
# some_module.py
PI = 3.14159
def f(x):
    return x + 2
def g(a, b):
    return a + b
```

那么在别的文件里，有多重导入方式：

```
# 1
import some_module
result = some_module.f(5)
pi = some_module.PI
# 2
from some_module import f, g, PI
result = g(5, PI)
# 3
import some_module as sm
from some_module import PI as pi, g as gf
r1 = sm.f(pi)
r2 = gf(6, pi)
```


## 运算符（Binary operators）

用is,和is not, 检查两个引用（references）是否指同一个object，

```
a = [1, 2, 3]
b = a
c = list(a)
print(a is b)
print(a is not c)
```

输出

```
True
True
```

因为`c = list(a)`中的list函数创建了一个新的list，所以c是一个新的list，不指向原来的a。==这个地方要注意==

另一个 is 的常用法是用来检查一个instance是不是none：===None不能判断吗？==

```
print(a = None)
print(a is None)
```

输出：

```
True
```

另外像是，+， - ，==， <=, &, | 等都也算是运算符，这个就不详细说了，可以直接看这个[链接](http://www.runoob.com/python/python-operators.html)

## 可更改和不可更改对象(Mutable and immutable objects)

在python的object中，lists, dicts, NumPy arrays, 以及用户自定义的类型(classes), 都是可以更改的。

而string和tuple是不可以更改的：

# 2 标量类型（scalar types）

这种类型指的是None，str, bytes, float, bool, int

## 数值型

```
ival = 123554
ival ** 6
```

输出：

```
3557466836753811461234217695296
```

```
fval = 7.234
fval2 = 5.43e-5
```

```
print(5/2)
print(5//2)  # 取商
5 % 2 # 取余数
```

输出：

```
2.5
2
1
```

## 字符串

In [41]:

```
a = 'one way of writing a string'
b = "another way"
c = """
    This is a longer string that
    spans multiple lines
"""
print(c.count('\n')) # 有三个回车符
```

输出：

```
3
```

字符串类型是不可变的：

```
a[10] = 'f'
```
输出：

```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-45-5ca625d1e504> in <module>()
----> 1 a[10] = 'f'

TypeError: 'str' object does not support item assignment
```

把其他类型转换为字符串：

In [46]:

```
a = 5.6
s = str(a)
s
```
输出：

```
'5.6'
```

因为字符串是一连串Unicode字符，所以可以当序列来处理，像list和tuple一样：


```
s = 'python'
print(list(s))
print(s[:3])
```
输出：

```
['p', 'y', 't', 'h', 'o', 'n']
'pyt'
```


反斜线用来制定特别的字符，比如回车符 \n


```
s = '12\\34'
print(s)
```
输出：

```
12\34
```

可以用前缀r来直接写出想要的字符串格式，而不用输入很多反斜杠：==什么时候都可以吗？


```
s = r'this\has\no\special\characters'
print(s)
```
输出

```
'this\\has\\no\\special\\characters'
```


```
# 加法用于连接两个字符串
s + s
```
输出：
```
'this\\has\\no\\special\\charactersthis\\has\\no\\special\\characters'
```

字符串的模板，或叫做格式化，是一个很重要的课题。string ojecjt 有一个format的方法：==format==还是要好好总结下的。其实我觉得，python 手册都要整理进来。==



```
template = '{0:.2f} {1:s} are worth US${2:d}'
template
template.format(4.5560, 'Argentine Pesos', 1)
```
输出：
```
'{0:.2f} {1:s} are worth US${2:d}'
'4.56 Argentine Pesos are worth US$1'
```

在这个string中：

- `{0:.2f}` : 第一个参数为float类型，去小数点后两位
- `{1:s}`: 把第二个参数变为string类型
- `{2:d}`: 把第三个参数变为一个精确的整数


## Bytes and Unicode

可以使用不同的编码方式：==没明白，为什么需要这么多编码方式？对于编码方式还是有点惧怕的，比如说，到底程序中存放的.py 文件的编码方式对程序中的字符串有没有影响？我怎么知道有些文字是什么编码？还是只能一个一个试？有些是先编成这个再编成那个，这种情况是不是有？要怎么对应？==


```
val = "español"
print(val)
val_utf8 = val.encode('utf-8')
print(val_utf8)
print(type(val_utf8))
print(val_utf8.decode('utf-8'))
print(val.encode('latin1'))
print(val.encode('utf-16'))
print(val.encode('utf-16le'))
```
输出：
```
'español'
b'espa\xc3\xb1ol'
bytes
'español'
b'espa\xf1ol'
b'\xff\xfee\x00s\x00p\x00a\x00\xf1\x00o\x00l\x00'
b'e\x00s\x00p\x00a\x00\xf1\x00o\x00l\x00'
```

通过加一个b前缀，变为byte文字：==这个没明白？这里 b 的作用相当于 encode('utf8') 吗？如果是，那么是一定对应 utf8 吗？还是说与 .py 文件的编码有关？==

```
bytes_val = b'this is bytes'
print(bytes_val)
decoded = bytes_val.decode('utf8')
print(decoded) # this is str (Unicode) now
```
输出：==为什么输出的是字符串前面加 b 的？而不是 \xff 类似这样的？==
```
b'this is bytes'
'this is bytes'
```

## 类型塑造（Type casting）


```
s = '3.14159'
fval = float(s)
print(type(fval))
print(int(fval))
print(bool(fval))
print(bool(0))
```
输出：
```
float
3
True
False
```

## 日期和时间

python内建的datetime模块提供了三种类型，datatime, date and time types：


```
from datetime import datetime, date, time
dt = datetime(2011, 10, 29, 20, 30, 21)
dt.day
dt.minute
```
输出：
```
29
30
```

用方法（method）提取日期或时间：


```
dt.date()
dt.time()
```

输出：

```
datetime.date(2011, 10, 29)
datetime.time(20, 30, 21)
```

输出string:

```
dt.strftime('%m/%d/%Y %H:%M')
```

输出：
```
'10/29/2011 20:30'
```

把string变为datetime object:

```
datetime.strptime('20091031', '%Y%m%d')
```
输出：
```
datetime.datetime(2009, 10, 31, 0, 0)
```

如果我们处理时间序列数据的话，把time部分换掉会比较有用，比如把minute和second变为0：==什么意思？==


```
dt.replace(minute=0, second=0)
```

输出：

```
datetime.datetime(2011, 10, 29, 20, 0)
```

因为datetime.datetime是不可变的，所以上面的命令是新创建了一个object。

两个不同的datetime object能产生一个datetime.timedelta类型：

```
dt2 = datetime(2011, 11, 15, 22, 30)
delta = dt2 - dt
print(delta)
print(type(delta))
print(dt)
print(dt + delta)
```
输出：
```
datetime.timedelta(17, 7179)
datetime.timedelta
datetime.datetime(2011, 10, 29, 20, 30, 21)
datetime.datetime(2011, 11, 15, 22, 30)
```

这个datetime.timedelta(17, 7179)表明两个时间差17天，7179秒


还有其他一些datetime格式![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/ah24ILB3e3.jpg?imageslim)


# 3 控制流程 (Control Flow)

## if, elif and else

## for loops

## while loops

上面这三个比较基础的就不介绍了，如果不懂的话可以看这个教程：[控制流](http://wiki.jikexueyuan.com/project/simple-python-course/control-flow.html)

## pass

表示不进行任何行动，当个占位的东西


```
if x < 0:
    print('negative!')
elif x == 0:
    # TODO: put something smart here
    pass
else:
    print('positive!')
```

## range

range函数返回一个能产生序列的迭代器，==还是不清楚 迭代器到底是什么？我想知道关于它的全部。==


```
range(10)
list(range(10))
list(range(0, 20, 2))
list(range(5, 0, -1))
```
输出：
```
range(0, 10)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
[5, 4, 3, 2, 1]
```

range的一个常用法是用来通过index迭代序列：

```
seq = [1, 2, 3, 4]
for i in range(len(seq)):
    val = seq[i]
```

## 三元表达式 Ternary expressions

`value = true-expr if condition else false-expr`


```
x = 5
print('Non-negative' if x >= 0 else 'Negative')
```
输出：
```
'Non-negative'
```

这个三元表达式通常用来简化代码，不过相对的是损失一部分可读性。
