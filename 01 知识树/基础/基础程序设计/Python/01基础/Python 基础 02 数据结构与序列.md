[TOC]

TODO
- 还是有一些知识点之前大概知道是怎么回事，现在才比较清楚。
- 还是要更多的例子来熟练应用。

# 1 Tuple（元组）

tuple是长度固定，不可改变的序列。创建元祖的方法是用逗号：==创建的时候不用加括号吗==

```
tup = 4, 5, 6
print(tup)
```
输出：
```
(4, 5, 6)
```

如果想要创建一个更复杂的tuple的话，还是要用括号，括号之间还是用逗号：


```
nested_tup = (4, 5, 6), (7, 8)
print(nested_tup)
```
输出：
```
((4, 5, 6), (7, 8))
```

把其他序列或迭代器转换为序列：
```
print(tuple([4, 0, 2]))
print(tuple('string'))
```
输出：
```
(4, 0, 2)
('s', 't', 'r', 'i', 'n', 'g')
```

存放在 tuple 中的 object 本身无法更改：

```
tup = tuple(['foo', [1, 2], True])
tup[2] = False
```
输出：
```
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-7-692985e84db1> in <module>()
      1 tup = tuple(['foo', [1, 2], True])
----> 2 tup[2] = False

TypeError: 'tuple' object does not support item assignment
```

但是如果tuple内部的object是可更改的，那么我们可以试着更改一下：==还可以这样？==

```
tup[1].append(3)
print(tup)
```
输出：

```
('foo', [1, 2, 3], True)
```

用 + 来合并多个tuple：==这个也没用过==


```
(4, None, 'for') + (6, 0) + ('bar', )
```

输出：

```
(4, None, 'for', 6, 0, 'bar')
```

`*` 相当于copy多份，也可以用在list上：


```
('foo', 'bar') * 4
```
输出：


```
('foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'bar')
```

## Unpacking tuples(取出元组)


```
tup = (4, 5, 6)
a, b, c = tup
print(b)
```
输出：
```
5
```

```
tup = 4, 5, (6, 7)
a, b, (c, d) = tup
print(d)
```
输出：

```
7
```

用下面的方法来交换变量的名字:


```
tmp = a
a = b 
b = tmp
```

但是在python里，交换能更简洁一些：==是的，这种方法，经常忘记==

```
b, a = a, b
```

这种 unpacking 通常用在迭代序列上：


```
seq = [(1, 2, 3), (4, 5, 6), (7, 8, 9)]
for a, b, c in seq:
    print('a={0}, b={1}, c={2}'.format(a, b, c))
```
输出：
```
a=1, b=2, c=3
a=4, b=5, c=6
a=7, b=8, c=9
```

另一种更高级的 unpacking 方法是用于只取出 tuple 中开头几个元素，剩下的元素直接赋给`*rest`：==没想到还可以这样，厉害了==

```
values = 1, 2, 3, 4, 5
a, b, *rest = values
print(a,b)
print(rest)
```
输出：
```
1 2
[3, 4, 5]
```

如果 rest 部分是你想要丢弃的，名字本身无所谓，通常用下划线来代替：==嗯，厉害，这个是经常看到的==

```
a, b, *_ = values
```

## Tuple methods(元组方法)

因为tuple的大小和内容都不能改变，所以方法也很少。`count`用来计算某个值出现的次数，list中也有这个方法：


```
a = (1, 2, 2, 2, 3, 4, 2)
a.count(2) # 用来计算每个值出现的次数
```
输出：
```
4
```

# 2 List (列表)

列表的灵活性就很强了，大小和内容都可以变：


```
a_list = [2, 3, 7, None]
tup = ('foo', 'bar', 'baz')
b_list = list(tup)
print(b_list)
b_list[1] = 'peekaboo'
b_list
```

输出：
```
['foo', 'bar', 'baz']
['foo', 'peekaboo', 'baz']
```

list 函数通常用来具现化迭代器或生成器：


```
gen = range(10)
print(gen) # 这是一个迭代器，所以无法看到里面的内容
list(gen) # 具现化后就可以看到了
```
输出：
```
range(0, 10)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 添加和移除元素


```
b_list.append('dwarf')
print(b_list)
```

输出：

```
['foo', 'peekaboo', 'baz', 'dwarf']
```

insert 可以把元素插入到特定的位置：
```
b_list.insert(1, 'red')
print(b_list)
```
输出：
```
['foo', 'red', 'peekaboo', 'baz', 'dwarf']
```

需要注意的是 insert 方法运算量比 append 大。所以如果想要在序列的开头和结尾添加元素的话，可以使用 collections.deque，这是一种双结尾的队列。==这个地方要注意==

insert 的反向操作较 pop, 能移除序列中特定位置的元素：

```
print(b_list.pop(2))
print(b_list)
```

输出：
```
'peekaboo'
['foo', 'red', 'baz', 'dwarf']
```

remove 可以通过值移除指定的element，如果同一个值在序列中多次出现，只移除第一个：==这个要注意，只会移除第一个==


```
b_list.append('foo')
print(b_list)
b_list.remove('foo')
print(b_list)
```
输出：
```
['foo', 'red', 'baz', 'dwarf', 'foo']
['red', 'baz', 'dwarf', 'foo']
```

检查一个值是否在 list 中，用 in：


```
print('dwarf' in b_list)
print('dwarf' not in b_list)
```
输出：
```
True
False
```

## 合并list

用 + 号：

```
[4, None, 'foo'] + [7, 8, (2, 3)]
```

输出：

```
[4, None, 'foo', 7, 8, (2, 3)]
```

通过entend方法，可以添加多个元素：


```
x = [4, None, 'foo']
x.extend([7, 8, (2, 3)])
print(x)
```
输出：

```
[4, None, 'foo', 7, 8, (2, 3)]
```

注意：用 + 法来做合并是一个运算量较大的操作，因为要创建一个新的 list 并复制。如果操作的是一个很大的list，用extend会更好一些：==这个不知道==


```
everything = []
for chunk in list_of_lists:
    everything.extend(chunk)
# 上面的代码要比下面的快
everything = []
for chunk in list_of_lists:
    everything = everything + chunk
```

这里总结一下，首先是 append 和 extend 的区别。==这个一定要注意区分，==
```
x = [4, None, 'foo']
x.extend([7, 8, (2, 3)])
print(x)
x.append([7, 8, (2, 3)])
print(x)
```
输出：
```
[4, None, 'foo', 7, 8, (2, 3)]
[4, None, 'foo', 7, 8, (2, 3), [7, 8, (2, 3)]]
```

- append 是把后面的 list 作为 元素添加到前一个 list 里
- extend 是把两个 list 的内容扩充到一起。

然后是 extend和 + 的区别

- `+`是创建了一个新的list并返回，运算量大
- extend 是在原本的 list上做了更改，运算量小

## 排序

用sort函数


```
a = [7, 2, 5, 1, 3]
a.sort()
print(a)
```
输出：
```
[1, 2, 3, 5, 7]
```

sort 函数有一些比较方便的选项。比如设置一个sort key，这个key也是一个函数（funciton）。比如我们想要按 string 的长度来排序：


```
b = ['saw', 'small', 'He', 'foxes', 'six']
b.sort(key=len)# 从小倒大排列的
print(b)
```
输出：
```
['He', 'saw', 'six', 'small', 'foxes']
```

## Binary search and maintaining a sorted list （二分搜索和维持一个排好序的list）

内建的 bisect 模块可以实现二分搜索。`bisect.bisect` 是用来寻找插入的位置，而`bisect.insort` 则实际插入元素到指定的位置：==没想到还有这种功能==


```
import bisect
c = [1, 2, 2, 2, 3, 4, 7]
print(bisect.bisect(c, 2))# 返回 2 可以插入的位置
print(bisect.bisect(c, 5))# 返回 5 可以插入的位置
bisect.insort(c, 6)
print(c)
```
输出：

```
4
6
[1, 2, 2, 2, 3, 4, 6, 7]
```

注意：bisect模块不会检查list是否是排好序的，所以用这个模块之前要先把list排序。==这个要注意==

## Slicing (切片)

[start:stop], 输出的结果包含开头，不包含结尾。所以输出的结果的数量是stop-start。==要注意==

```
seq = [7, 2, 3, 7, 5, 6, 0, 1]
seq[1:5] 
```
输出：
```
[2, 3, 7, 5]
```

可以赋值：
```
seq[3:4] = [6, 3]
seq # 把元素7变成了6, 3
```
输出：

```
[7, 2, 3, 6, 3, 5, 6, 0, 1]
```

可以不用写开头或结尾：

```
seq[:5]
seq[3:]
```
输出：
```
[7, 2, 3, 6, 3]
[6, 3, 5, 6, 0, 1]
```

负索引表示倒数开始多少个的意思：


```
seq[-4:]
seq[-6:-2]
```

输出：

```
[5, 6, 0, 1]
[6, 3, 5, 6]
```

两个冒号后面的数代表步长，就是隔几个元素取一次：

```
print(seq)
print(seq[::2])
```

输出：

```
[7, 2, 3, 6, 3, 5, 6, 0, 1]
[7, 3, 3, 6, 1]
```

用 -1 能反转一个 list 或 tuple：==这个用法还是很常见的==


```
seq[::-1]
```
输出：

```
[1, 0, 6, 5, 3, 6, 3, 2, 7]
```

切片方式：



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image//180615/h8Lf4eHgBH.png?imageslim)

# 3 Built-in Sequence Functions(内建的序列函数)

## enumerate（枚举）

这个通常用于迭代序列。一个比较直白的方法是：


```
i = 0
for value in collection:
    # do something with value
    i += 1
```
但enumerate能返回一个 (i, value) 的tuple：==这种用法要注意，的确是个好方法==
```
for i, value in enumerate(collection):
    # do something with value
```

enumerate通常用来把一个list中的位置和值映射到一个dcit字典里：

```
some_list = ['foo', 'bar', 'baz']
mapping = {}
for i, v in enumerate(some_list):
    mapping[v] = i
print(mapping)
```
输出：
```
{'bar': 1, 'baz': 2, 'foo': 0}
```

## sorted

sorted 函数返回一个新的排好序的序列，而之前提到的 .sort 方法是直接更改原有的序列，不产生新序列：==注意，这个要注意区==


```
sorted([7, 1, 2, 6, 0, 3, 2])
sorted('horse race')
```

输出：
```
[0, 1, 2, 2, 3, 6, 7]
[' ', 'a', 'c', 'e', 'e', 'h', 'o', 'r', 'r', 's']
```

## zip

用于"pairs"(成对)。把多个序列中每个对应的元素变成一对，最后返回一个含有tuple的list：==对应的元素变成一对==


```
seq1 = ['foo', 'bar', 'baz']
seq2 = ['one', 'two', 'three']
zipped = zip(seq1, seq2)
print(zipped)
list(zipped)
```
输出：

```
<zip object at 0x0000019B03D801C8>
[('foo', 'one'), ('bar', 'two'), ('baz', 'three')]
```

zip 可以接收任意长度的序列，最后返回的结果取决于最短的序列：

```
seq3 = [False, True]
list(zip(seq1, seq2, seq3))
```

输出：

```
[('foo', 'one', False), ('bar', 'two', True)]
```

zip 的一个常见用法是同时迭代多个序列，可以和 enumerate 搭配起来用：==哇塞，厉害的方法==

```
for i, (a, b) in enumerate(zip(seq1, seq2)):
    print('{0}: {1}, {2}'.format(i, a, b))
```
输出：

```
0: foo, one
1: bar, two
2: baz, three
```

如果给我们一个压缩过的序列，我们可以将其解压：==这也行？没看明白？==

```
pitchers = [('Nolan', 'Ryan'), 
            ('Roger', 'Clemens'), 
            ('Schilling', 'Curt')]
first_names, last_names = zip(*pitchers)
print(first_names)
print(last_names)
```
输出：
```
('Nolan', 'Roger', 'Schilling')
('Ryan', 'Clemens', 'Curt')
```

## reversed

reverse可以倒叙迭代序列：

```
list(reversed(range(10)))
```
输出：
```
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

注意，revered是一个generator（生成器，之后会详细讲），所以必须需要list来具现化。==这个revered 和 [::-1] 有区别吗？==

# 4 dict（字典）

字典也被叫做 hash map 或 associative array。结构就是 key-value pairs. 创建方式是用`{}`:

```
empty_dict = {}
d1 = {'a': 'some value', 'b': [1, 2, 3, 4]}
print(d1)
```
输出：

```
{'a': 'some value', 'b': [1, 2, 3, 4]}
```

dict像list一样可以插入：

```
d1[7] = 'an integer'
print(d1)
```
输出：
```
{'b': [1, 2, 3, 4], 7: 'an integer', 'a': 'some value'}
```

可以检查dict是否有某个key：


```
'b' in d1
```
输出：

```
True
```

可以用 del 或 pop 删除值：


```
d1[5] = 'some value'
print(d1)
del d1[5]
print(d1)
d1['dummy'] = 'another value'
print(d1)
ret = d1.pop('dummy')
print(ret)
print(d1)
```

输出：
```
{'b': [1, 2, 3, 4], 7: 'an integer', 5: 'some value', 'a': 'some value'}
{'b': [1, 2, 3, 4], 7: 'an integer', 'a': 'some value'}
{'b': [1, 2, 3, 4],
 'dummy': 'another value',
 7: 'an integer',
 'a': 'some value'}
'another value'
{'b': [1, 2, 3, 4], 7: 'an integer', 'a': 'some value'}
```

keys 和 values 方法能返回 dict 中 key-value 组合的迭代器，不过并不安什么顺序。如果想让keys和values 每次打印的顺序相同的话：


```
list(d1.keys())
list(d1.values())
```
输出：
```
['b', 7, 'a']
[[1, 2, 3, 4], 'an integer', 'some value']
```


可以用update来合并两个dict：==这个不知道，如果有相同的key 会怎么样？==


```
d1.update({'b': 'foo', 'c': 12})
print(d1)
```
输出：
```
{'b': 'foo', 'c': 12, 7: 'an integer', 'a': 'some value'}
```

这个update是更改了原有的dict，不会返回新的dict

## Creating dicts from sequences（从序列中生成dict）

假设我们想把两个序列按照 key-value 的方式生成一个 dict，我们可能会这样写：


```
mapping = []
for key, value in zip(key_list, value_list):
    mapping[key] = value
```

因为dict其实就是 2-tuple 的组合，所以dict函数能接受一组 2-tuple：


```
mapping = dict(zip(range(5), reversed(range(5))))
mapping
```
输出：
```
{0: 4, 1: 3, 2: 2, 3: 1, 4: 0}
```

## Default value(默认值)

如果 dict 中某个 key 存在的话，就返回该 value，否则的话，就返回一个默认值：


```
if key in some_dict:
    value = some_dict[key]
else:
    value = default_value
```

不过 dict 中的 get 和 pop 方法能设置默认值，即能把上面的代码简写为：

```
value = some_dict.get(key, default_value)
```

如果key不存在的话，get方法默认会返回 None，而 pop 则会引发一个错误。

通过设定值，一个常用的场景是一个dict中的value也是其他集合，比如list。举例说明，我们想要把一些单词按首字母归类：


```
words = ['apple', 'bat', 'bar', 'atom', 'book']
by_letter = {}
for word in words:
    letter = word[0]
    if letter not in by_letter:
        by_letter[letter] = [word]
    else:
        by_letter[letter].append(word)
print(by_letter)
```
输出：

```
{'a': ['apple', 'atom'], 'b': ['bat', 'bar', 'book']}
```

而 setdefault 方法则是专门为这个用途存在的，上面的循环可以写为：==这个不知道==

```
for word in words:
    letter = word[0]
    by_letter.setdefault(letter, []).append(word)
```

使用setdefault() 初始化字典键值. 使用字典的时候经常会遇到这样一种应用场景：动态更新字典，像如上面代码，如果 key 不在 dictionary 中那么就添加它并把它对应的值初始为空列表 [] ，然后把元素 append 到空列表中。

内建的 collections 模块有一个有用的 class，defaultdict，这个能让上述过程更简单。创建方法是传递一个type或是函数：==这个要注意==

```
from collections import defaultdict
by_letter = defaultdict(list)
for word in words:
    by_letter[word[0]].append(word)
```

## Valid dict key types (有效的key类型)

通常 key 的类型是不可更改的常量类型（int，float，string）或tuple。专业的叫法是 hashability。可以查看一个object是否是hashable，只要是hashable的，就可以当做 dict 中的 key 。这里用 hash函数查看：

In [102]:

```
print(hash('string'))
print(hash((1, 2, (2, 3))))
print(hash(1, 2, [2, 3])) # 失败，因为list是可变的
```

输出：
```
-522944812555367519
1097636502276347782
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-105-b1f78f4caeda> in <module>()
----> 1 hash(1, 2, [2, 3]) # 失败，因为list是可变的

TypeError: hash() takes exactly one argument (3 given)
```

要想把list当做key的话，可以把list转变为tuple：


```
d = {}
d[tuple([1, 2, 3])] = 5
print(d)
```
输出：

```
{(1, 2, 3): 5}
```

## 5 Set 集合

set是无序且元素不重复的。就像是key唯一，且没有value的字典。两种方式可以创建，一个是用set函数，一个是用花括号：


```
print(set([2, 3, 2, 1, 4, 4, 3]))
print({2, 3, 2, 1, 4, 4, 3})
```
输出：
```
{1, 2, 3, 4}
{1, 2, 3, 4}
```

集合的操作既然也支持，比如并集，交集，差集：


```
a = {1, 2, 3, 4, 5}
b = {3, 4, 5, 6, 7, 8}
# 并集
a.union(b)
a | b
# 交集
a.intersection(b)
a & b
```
输出：
```
{1, 2, 3, 4, 5, 6, 7, 8}
{1, 2, 3, 4, 5, 6, 7, 8}
{3, 4, 5}
{3, 4, 5}
```

一些集合操作

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180615/BmL8G1d58C.png?imageslim)

上面这些逻辑操作都是直接更改 set 本身。如果是一个很大的 set ，下面的操作会更有效率：


```
c = a.copy()
c |= b
print(c)
d = a.copy()
d &= b
print(d)
```

输出：

```
{1, 2, 3, 4, 5, 6, 7, 8}
{3, 4, 5}
```

set的元素必须是不可更改的。如果想要 list 一样的元素，只能变为 tuple：==嗯，要注意==


```
my_data = [1, 2, 3, 4]
my_set = {tuple(my_data)}
print(my_set)
```
输出：

```
{(1, 2, 3, 4)}
```

我们可以查看一个子集与父集的关系：
```
a_set = {1, 2, 3, 4, 5}
{1, 2, 3}.issubset(a_set)
a_set.issuperset({1, 2, 3})
```
输出：
```
True
True
```

# 6 List, Set, and Dict Comprehensions(推导式)

list comprehension(列表推导式）是python里最受喜爱的一个特色。我们能简洁地构造一个list：

```
[expr for val in collection if condiction]
```

相当于：

```
result = []
for val in collection:
    if condition:
        result.append(expr)
```

比如，给定一个list，里面有很多string，我们只要留下string长度超过2的，并将其转换为大写：


```
strings = ['a', 'as', 'bat', 'car', 'dove', 'python']
[x.upper() for x in strings if len(x) > 2]
```
输出：
```
['BAT', 'CAR', 'DOVE', 'PYTHON']
```

dict推导式：

`dict_comp = {key-expr: value-expr for value in collection if condition}`

set的推导式：

`set_comp = {expr for value in collection if condition}`

基于上面的例子，我们想要一个集合来保存string的长度：==嗯，原来还可以这样==


```
unique_length = {len(x) for x in strings}
unique_length
```

输出：
```
{1, 2, 3, 4, 6}
```

用map让表达更功能化一些：==是的，这样更明确==


```
set(map(len, strings))
```

输出：
```
{1, 2, 3, 4, 6}
```

一个简单而的字典表达式例子，string和其在list中对应的index：


```
loc_mapping = {val: index for index, val in enumerate(strings)}
loc_mapping
```

输出：
```
{'a': 0, 'as': 1, 'bat': 2, 'car': 3, 'dove': 4, 'python': 5}
```

## Nested list comprehensions（嵌套列表表达式）

假设我们有一个list，list中又有不同的list表示英语和西班牙语的姓名：


```
all_data = [['John', 'Emily', 'Michael', 'Mary', 'Steven'], 
            ['Maria', 'Juan', 'Javier', 'Natalia', 'Pilar']]
```

我们想要按语言来组织这些名字。可以用一个for loop：


```python
names_of_interest = []
for names in all_data:
    enough_es = [name for name in names if name.count('e') >= 2]
    names_of_interest.extend(enough_es)
names_of_interest
```

输出：
```
['Steven']
```

但是我们key用嵌套列表表达式写得更简洁一些：==没想到还可以这样！！从外层的循环到内层的循环==

```python
result = [name for names in all_data for name in names if name.count('e') >= 2]
result
```

输出：
```
['Steven']
```

for 部分是根据嵌套的顺序来写的，从外层的 loop 到内层的 loop 。这里一个例子是把tuple扁平化成一个整数列表：


```python
some_tuples = [(1, 2, 3), (4, 5, 6), (7, 8, 9)]
flattened = [x for tup in some_tuples for x in tup]
flattened
```
输出：
```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

一定要记住顺序是和我们写for loop一样的：

```python
flatteded = []
for tup in some_tuples:
    for x in tup:
        flattened.append(x)
```

列表表达式里再有一个列表表达式也是可以的，可以生成 a list of lists：==嗯，这个也行==


```python
[[x for x in tup] for tup in some_tuples]
```
输出：

```
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```