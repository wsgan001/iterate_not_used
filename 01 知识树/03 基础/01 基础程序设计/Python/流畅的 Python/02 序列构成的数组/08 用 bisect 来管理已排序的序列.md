
### 2.8用bisect来管理已排序的序列

bisect模块包含两个主要函数，bisect和insort，两个函数都利用二分查找算法来在

有序序列中查找或插入元素。

2.8.1用bisect来搜索

bisect(haystack, needle)在 haystack (干草操)里搜索 needle (针)的位置，该 位置满足的条件是，把 needle 插入这个位置之后， haystack 还能保持升序。也就是在 说这个函数返回的位置前面的值，都小于或等于 needle 的值。其中 haystack 必须是一 个有序的序列。你可以先用bisect(haystack, needle)查找位置index，再用 haystack.insert(index, needle) 来插入新值。但你也可用 insort 来一步到位，并 且后者的速度更快一些。

Python的高产贡献者Raymond Hettinger写了 一个排序集合模块、

(<http://code.activestate.com/recipes/577197-sortedcollection/>) ，模块里集成了 bisect

功能，但是比独立的 bisect 更易用。

向我们展示了 bisect 返回的不同位置值。这段



示例 2-17 利用几个精心挑选的 needle， 代码的输出结果显示在图 2-4 中。

示例 2-17 在有序序列中用 bisect 查找某个元素的插入位置

import bisect import sys

HAYSTACK = [1, 4, 5, 6, 8, 12, 15, 20, 21, 23, 23, 26, 29, 30] NEEDLES = [0, 1, 2, 5, 8, 10, 22, 23, 29, 30, 31]

ROW_FMT = '{0:2d} @ {1:2d}    {2}{0:<2d}'

def demo(bisect_fn):

for needle in reversed(NEEDLES):

position = bisect_fn(HAYSTACK, needle) O offset = position * '    |'    ©

print(ROW_FMT.format(needle, position, offset)) ©

if __name__ == '__main__':

if sys.argv[-1] == 'left': © bisect_fn = bisect.bisect_left

else:

bisect_fn = bisect.bisect print('DEMO:', bisect_fn.__name__) ❺

print('haystack ->', ' '.join('%2d' % n for n in HAYSTACK)) demo(bisect_fn)

❶ 用特定的 bisect 函数来计算元素应该出现的位置。

❷利用该位置来算出需要几个分隔符号。

❸ 把元素和其应该出现的位置打印出来。

❹ 根据命令上最后一个参数来选用 bisect 函数。

❺ 把选定的函数在抬头打印出来

图 2-4：用 bisect 函数时示例 2-17 的输出。每一行以 needle @ position (元素及 其应该插入的位置)开始，然后展示了该元素在原序列中的物理位置

bisect 的表现可以从两个方面来调教。

首先可以用它的两个可选参数一lo和hi——来缩小搜寻的范围。lo的默认值是0，hi 的默认值是序列的长度，即 len() 作用于该序列的返回值。

其次， bisect 函数其实是 bisect_right 函数的别名，后者还有个姊妹函数叫 bisect_left。它们的区别在于，bisect_left返回的插入位置是原序列中跟被插入元 素相等的元素的位置，也就是新元素会被放置于它相等的元素的前面，而 bisect_right 返回的则是跟它相等的元素之后的位置。这个细微的差别可能对于整数序列来讲没什么 用，但是对于那些值相等但是形式不同的数据类型来讲，结果就不一样了。比如说虽然 1 == 1.0 的返回值是 True， 1 和 1.0 其实是两个不同的元素。图 2-5 显示的是用 bisect_left 来运行上述示例的结果。

02-array-seq/ $ python3 bi.sect_demo.py left DEMO: bisect_left

| haystack ->                                                  | 1    | 4    | 5    | 6    | 8 12 15 20 21 23 23 26 29 30                     |
| ------------------------------------------------------------ | ---- | ---- | ---- | ---- | ------------------------------------------------ |
| 31 @ 14                                                      |      |      |      |      | 1    1    1    1    1    1    1    1    1    131 |
| 30 @ 13                                                      |      |      |      |      | 1    1    1    1    1    1    1    1    130      |
| 29 @ 12                                                      |      |      |      |      | 1    1    1    1    1    1    1    129           |
| 23 @ 9                                                       |      |      |      |      | 1    1    1    1    123                          |
| 22 @ 9                                                       |      |      |      |      | 1 1 1 1 122                                      |
| 10 @ 5                                                       |      |      |      |      | 110                                              |
| 8 @ 4                                                        |      |      |      | 18   |                                                  |
| 5 @    2                                                     |      | 15   |      |      |                                                  |
| 2 @ 1                                                        | 12   |      |      |      |                                                  |
| 1 @ 0 1                                                      |      |      |      |      |                                                  |
| 0 @ 0 0                                                      |      |      |      |      |                                                  |
| 2-5：用 bisect_left 运行示例 2-17 得到的结果(跟图 2-4 对比可以发现，值 |      |      |      |      |                                                  |

1、8、23、29 和 30 的插入位置变成了原序列中这些值的前面)

bisect 可以用来建立一个用数字作为索引的查询表格，比如说把分数和成绩 8 对应起 来，见示例 2-18。

8成绩指的是在美国大学中普遍使用的A~F字母成绩，A表示优秀，F表示不及格。一译者注 示例 2-18 根据一个分数，找到它所对应的成绩

\>>> def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'): ...    i = bisect.bisect(breakpoints, score)

...    return grades[i]

\>>> [grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]

['F', 'A', 'C', 'C', 'B', 'A', 'A']

示例 2-18 里的代码来自 bisect 模块的文档 ([https://docs.python+org/3/library/bisect+html](https://docs.python.org/3/library/bisect.html))。文档里列举了一些利用 bisect 的函数，

它们可以在很长的有序序列中作为 index 的替代，用来更快地查找一个元素的位置。

这些函数不但可以用于查找，还可以用来向序列中插入新元素，下面就来看看它们的用

法。

2.8.2 用bisect.insort插入新元素

排序很耗时，因此在得到一个有序序列之后，我们最好能够保持它的有

序。 bisect.insort 就是为了这个而存在的。

insort(seq, item) 把变量 item 插入到序列 seq 中，并能保持 seq 的升序顺序。详见 示例 2-19 和它在图 2-6 里的输出。

示例 2-19 insort 可以保持有序序列的顺序

import bisect import random SIZE=7

random.seed(1729)

my_list = []

for i in range(SIZE):

new_item = random.randrange(SIZE*2) bisect.insort(my_list, new_item) print('%2d ->' % new_item, my_list)

02-array-seq/ $ python3 bisect_insort.py

| 10   | ->   | [10] |      |      |      |      |          |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | -------- |
| 0    | ->   | [0,  | 10]  |      |      |      |          |
| 6    | ->   | [0,  | 6，  | 10]  |      |      |          |
| 8    | ->   | [0,  | 6，  | 8,   | 10]  |      |          |
| 7    | ◊    | [0， | 6，  | 7,   | 8,   | 10]  |          |
| 2    | ->   | E0,  | 2,   | 6，  | 7,   | 8,   | 10]      |
| 10   | ->   | [0,  | 2,   | 6，  | 乙   | 8,   | 10， 10] |

图 2-6：示例 2-19 的输出

insort 跟 bisect 一样，有 lo 和 hi 两个可选参数用来控制查找的范围。它也有个变体 叫insort_left，这个变体在背后用的是bisect_left。

目前所提到的内容都不仅仅是对列表或者元组有效，还可以应用于几乎所有的序列类型 上。有时候因为列表实在是太方便了，所以 Python 程序员可能会过度使用它，反正我知 道我犯过这个毛病。而如果你只需要处理数字列表的话，数组可能是个更好的选择。下面 就来讨论一些可以替换列表的数据结构。
