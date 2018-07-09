[TOC]

# 3.2 Functions (函数)

Functions是python中很重要的概念。可以用def来定义：


```python
def my_function(x, y, z=1.5):
    if z > 1:
        return z * (x + y)
    else:
        return z / (x + y)
```

上面可以return多个结果，如果没有return语句的话，默认会返回None。==注意：每个函数都是必定有返回值的==

每个function有positional arguments（位置参数） and keyword arguments（关键字参数）。keyword argument通常用来指定默认的值或可选参数。在上面的函数里，x和y是位置参数，而z是一个关键字参数。我们可以通过下面的方式调用：


```python
my_function(5, 6, z=0.7)
my_function(3.14, 7, 3.5)
my_function(10, 20)
```

输出：
```
0.06363636363636363
35.49
45.0
```

一个需要强制遵守的规则是，函数的参数顺序为，位置参数在前，关键字参数在后。==这个要注意==而多个关键字参数的位置是可以自己指定的。比如下面的x，y，z都是关键字参数，调用函数的时候没有按顺序也可以：


```python
my_function(x=5, y=6, z=7)
my_function(y=6, x=5, z=7)
```

# 1 Namespaces, Scope, and Local Functions（命名空间，作用范围，局部函数）

scope 分两种，global and local （全局和局部）。namespace 用来描述变量的作用范围。当调用一个函数的时候，会自动创建一个局部命名空间，用来存放函数的参数，一旦函数结束，局部命名空间会被自动废弃掉。考虑下面的例子：


```python
def func():
    a = []
    for i in range(5):
        a.append(i)
```

当func()被调用，会创建一个空list a，然后5个元素赋给a。函数结束后，a也会被废弃。假设有下面的定义：

```python
a = []
def func():
    for i in range(5):
        a.append(i)
```

给函数范围外的变量赋值是可行的，但是这些变量必须通过global关键字来声明：


```python
a = None
b=None
def bind_a_variable():
    a = []
    global b
    b=[]
bind_a_variable()
print(a)
print(b)
```
输出：
```
None
[]
```
下面这个关于list 的例子还是有点不清楚：==为什么 append 可以？而 直接赋值不行？可能是因为直接赋值指向的是一个新的地方，而这个地方只有在函数内部才有效，而 append 改动的是它原来的地方。==
```python
a = []
b=[]
c=[]
c=[3]
def func():
    a.append(1)
    b=[2]
func()
print(a)
print(b)
print(c)
```
输出：
```
[1]
[]
[3]
```

这里我们不推荐使用global关键字。因为这个全局变量通常用于存储系统状态（state in a system)，==什么意思？==如果你用了很多全局变量，或许你该考虑使用class。==什么意思？用class 统一存放变量吗？然后把这个class 设定为全局的？==

# 2 Returning Multiple Values（返回多个值）


```python
def f():
    a = 5
    b = 6
    c = 7
    return a, b, c
a, b, c = f()
```

原理：其实函数还是返回了一个object，即tuple，然后这个tuple被解压给了result variables. 比如：

```
return_value = f()
```

这样的话，return_value就是一个 3-tuple。

# 3 Functions Are Objects（函数是对象）

因为函数是对象，所以很多构造能轻易表达出来。比如我们要对下面的string做一些数据清洗：


```
states = [' Alabama ', 'Georgia!', 'Georgia', 'georgia', 
          'FlOrIda', 'south carolina##', 'West virginia?']
```

要想让这些string统一，我们要做几件事：去除空格，删去标点符号，标准化大小写。一种做法是利用内建函数和re模块（正则表达式）：


```python
import re
def clean_string(strings):
    result = []
    for value in strings:
        value = value.strip()
        value = re.sub('[!#?]', '', value)
        value = value.title()
        result.append(value)
    return result
clean_string(states)
```

输出：
```
['Alabama',
 'Georgia',
 'Georgia',
 'Georgia',
 'Florida',
 'South Carolina',
 'West Virginia']
```

还有一种做法，把一系列操作放在一个list里：==嗯，我以前看到过这个，有一些理解，现在再次看到才比较理解==


```python
def remove_punctuation(value):
    return re.sub('[!#?]', '', value)
clean_ops = [str.strip, remove_punctuation, str.title]
def clean_strings(strings, ops):
    result = []
    for value in strings:
        for function in ops:
            value = function(value)
        result.append(value)
    return result
clean_strings(states, clean_ops)
```

输出：
```
['Alabama',
 'Georgia',
 'Georgia',
 'Georgia',
 'Florida',
 'South Carolina',
 'West Virginia']
```

一个更函数化的方式能让你方便得在一个高等级上转变 string 。可以把函数当做其他函数的参数，比如用内建的 map 函数，这个 map 函数能把一个函数用于一个序列上：==对于map 函数一直理解的不够，主要是因为看到 map 函数经常会想到 hashmap==


```python
for x in map(remove_punctuation, states):
    print(x)
```
输出：
```
 Alabama 
Georgia
Georgia
georgia
FlOrIda
south carolina
West virginia
```

# 4 Anonymous (Lambda) Functions(匿名函数，lambda函数)

这种函数只有一行，结果能返回值。下面两个函数是一样的效果：

```python
def short_function(x):
    return x * 2
equiv_anon = lambda x: x * 2
```

之后我们只称其为lambda函数。这种函数在数据分析方面非常有用，就因为方便。比如下面的例子：


```python
def apply_to_list(some_list, f):
    return [f(x) for x in some_list]
ints = [4, 0, 1, 5, 6]
apply_to_list(ints, lambda x: x * 2)
```

输出：
```
[8, 0, 2, 10, 12]
```

假设你想按不同字母的数量给一组string排序：


```python
strings = ['foo', 'card', 'bar', 'aaaa', 'abab']
strings.sort(key=lambda x: len(set(list(x))))#厉害，不同够得字母的数量，先转化成list 在转化成 set ，再求 len 。
strings
```
输出：
```
['aaaa', 'foo', 'abab', 'bar', 'card']
```

# 5 Currying: Partial Argument Application(柯里化：局部参数应用)

在计算机科学中，柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数(最初函数的第一个参数)的函数，并且返回接受余下的参数且返回结果的新函数的技术。

[函数加里化是一种实现多参数函数的方法。](http://www.vaikan.com/currying-partial-application/)

简单的说，通过局部参数应用，在一个原有函数的基础上，构造一个新的函数。比如，我们想要一个加法的函数：


```
def add_number(x, y):
    return x + y
```

通过上面这个函数，我们可以衍生出一个只需要一个参数的新方程，add_five，即把5加到参数上：==看到这个我就知道之前看到过，之前看的例子是把进制转换分别命名==


```
add_five = lambda y: add_number(5, y)
```

其中第二个参数y叫做被柯里化了。这其实没什么特别的，我们做的其实就是用一个已有的函数定义了一个新函数。而内建的functools模块里的partial函数能简化这个操作：==嗯==


```
from functools import partial
add_five = partial(add_number, 5)
```

# 6 Generators(生成器)

这个东西在python里很重要，能用来迭代序列。比如，迭代一个字典：


```
some_dict = {'a': 1, 'b': 2, 'c': 3}
for key in some_dict:
    print(key)
```
输出：
```
a
b
c
```

当我们写 `for key in some_dict` 的时候，python解释器就新建了一个iterator：==是这样吗？==


```
dict_iterator = iter(some_dict)
dict_iterator
```

输出：
```
<dict_keyiterator at 0x104c92ef8>
```

一个生成器能产生 object 给 python 解释器，当遇到for loop这样的情景时。大部分方法，除了 list 之类的object，都能接受迭代器。比如内建的函数min, max, sum,或是类型构造器 list, tuple:
==嗯，我大概知道，生成器是一个用来提供元素的东西，而且每次返回一个==

```
list(dict_iterator)
```
输出：
```
['a', 'b', 'c']
```

生成器是用于构造迭代对象的简洁方式。不像其他函数一口气执行完，返回一个结果，生成器是多次返回一个序列，每请求一次，才会返回一个。用yield可以构建一个生成器：

```
def squares(n=10):
    print('Generating squares from 1 to {0}'.format(n**2))
    for i in range(1, n+1):
        yield i ** 2
```

当你实际调用一个生成器的时候，不会有代码立刻执行：


```
gen = squares()
gen
```
输出：

```
<generator object squares at 0x104c95bf8>
```

知道我们发出请求，生成器才会执行代码：==关于这个发出请求，什么是发出请求？这个地方的 for 怎么发出请求了？==


```
for x in gen:
    print(x, end=' ')
```
输出：
```
Generating squares from 1 to 100
1 4 9 16 25 36 49 64 81 100 
```

## Generator expresssions (生成器表达式)

另一个构造生成器的方式是利用生成器表达式。写法就像列表表达式一样，只不过使用括号：==这里注意一下与列表推导式的区别==


```python
a = (x ** 2 for x in range(10))# 这个是生成器
b=[x**2 for x in range(10)]# 后面这两个是列表推导式
c={x**2 for x in range(10)}

print(a)
print(b)
print(c)
```

输出：
```
<generator object <genexpr> at 0x0000019B03DF5BF8>
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
{0, 1, 64, 4, 36, 9, 16, 49, 81, 25}
```

上面的代码和下面冗长的代码是等效的：


```python
def _make_gen():
    for x in range(100):
        yield x ** 2
gen = _make_gen()
```

生成器表达式能作为函数的参数，而列表表达式不能作为函数的参数：==嗯==


```python
sum( x ** 2 for x in range(100))
```

输出：
```
328350
```


```python
dict((i, i**2) for i in range(5))
```
输出：


```
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

## itertools module

itertools模块有很多常用算法的生成器。比如 groupby 能取任意序列当做函数：==没明白？==


```python
import itertools 
first_letter = lambda x: x[0]
names = ['Alan', 'Adam', 'Wes', 'Will', 'Albert', 'Steven']
for letter, group in itertools.groupby(names, first_letter):
    print(letter, list(group)) # names is a generator
```
输出：
```
A ['Alan', 'Adam']
W ['Wes', 'Will']
A ['Albert']
S ['Steven']
```
这个地方的groupby 说明一下：The operation of groupby() is similar to the uniq filter in Unix. It generates a break or new group every time the value of the key function changes (which is why it is usually necessary to have sorted the data using the same key function).

就是说：这里的 groupby 可以理解为根据 key 把相邻的重复元素挑出来放在一起成为一个小组，然后对这一小组的东西返回一个 迭代器，这样每个小组都有每个小组的迭代器，上面的函数中的 group 就是一个迭代器。([参考廖雪峰groupby](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143200162233153835cfdd1a541a18ddc15059e3ddeec000))


所以如果想要让 Albert 与其他两个A开头的名字分到一组的话，可以先对 names 排序，让三个A开头的名字相邻，然后再用groupby：


```python
first_letter = lambda x: x[0]
names = ['Alan', 'Adam', 'Wes', 'Will', 'Albert', 'Steven']
names = sorted(names, key=first_letter)# 先进行排序
for letter, group in itertools.groupby(names, first_letter):#然后再按照对应的keyfunc 进行groupby
    print(letter, list(group))# 这个地方的 group 其实是一个迭代器，也就是说，对于每个 k 都有一个迭代器。
```
输出：
```
A ['Alan', 'Adam', 'Albert']
S ['Steven']
W ['Wes', 'Will']
```

一些迭代工具函数：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180615/g24jae5ALg.png?imageslim)

# 7 Errors and Exception Handling（错误和异常处理）

在数据分析应用中，许多函数只能用于特定的输入。比如float能把string变为浮点数，但如果有不正确的输入的话会报错：


```python
float('1.2345')
float('something')
```

输出：
```
1.2345
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-47-439904410854> in <module>()
----> 1 float('something')

ValueError: could not convert string to float: 'something'
```

假设我们想要这个 float 失败的优雅一些，返回输入的参数。我们可以用try/except：

这个except的部分只有当 float(x) 引发异常的时候才会执行：

```python
def attempt_float(x):
    try:
        return float(x)
    except:
        return x
attempt_float('1.2345')
attempt_float('something')
```

输出：
```
1.2345
'something'
```

当然，float也可能引发除了 ValueError 之外的异常：


```
float((1, 2))
```
输出：

```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-53-842079ebb635> in <module>()
----> 1 float((1, 2))

TypeError: float() argument must be a string or a number, not 'tuple'
```

你可能只想控制ValueError，因为如果是TypeError的话，错误提示对你debug是有帮助的：


```python
def attempt_float(x):
    try:
        return float(x)
    except ValueError:
        return x
attempt_float((1, 2))
```
输出：

```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-55-9bdfd730cead> in <module>()
----> 1 attempt_float((1, 2))

<ipython-input-54-84efde0a7059> in attempt_float(x)
      1 def attempt_float(x):
      2     try:
----> 3         return float(x)
      4     except ValueError:
      5         return x

TypeError: float() argument must be a string or a number, not 'tuple'
```

当然，你也可以捕捉多个不同的异常：

```python
def attempt_float(x):
    try:
        return float(x)
    except (TypeError, ValueError):
        return x
```

在某些情况下，你不想抑制任何异常，但你想希望执行某些代码，不论 try 里的代码成功执行与否。这样的话，需要用的 finally :


```python
f = open(path, 'w')
try:
    write_to_file(y)
finally:
    f.close()
```

这样的处理会始终会让 f 关闭。同样的，你可以在try里的代码成功执行后，让某些代码执行：


```python
f = open(path, 'w')
try:
    write_to_file(f)
except:
    print('Failed')
else:
    print('Succeeded')
finally:
    f.close()
```

## Exceptions in IPython (IPython中的异常)

当使用%run执行代码片段，引发异常后，IPython中默认打印出所有的调用信息（traceback）


```python
%run example/ipython_bug.py
ERROR:root:File `'example/ipython_bug.py'` not found.
```




# REF

* [函数加里化(Currying)和偏函数应用(Partial Application)的比较](http://www.vaikan.com/currying-partial-application/)

