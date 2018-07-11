

### 2.7 list.sort方法和内置函数sorted

list.sort 方法会就地排序列表，也就是说不会把原列表复制一份。这也是这个方法的 返回值是 None 的原因，提醒你本方法不会新建一个列表。在这种情况下返回 None 其实 是 Python 的一个惯例：如果一个函数或者方法对对象进行的是就地改动，那它就应该返 回None，好让调用者知道传入的参数发生了变动，而且并未产生新的对象。例 如， random.shuffle 函数也遵守了这个惯例。

用返回None来表示就地改动这个惯例有个弊端，那就是调用者无法将其串联 起来。而返回一个新对象的方法(比如说 str 里的所有方法)则正好相反，它们可 以串联起来调用，从而形成连贯接口 (fluent interface)。详情参见维基百科中有关连 贯接口的讨论(<https://en.wikipedia.org/wiki/Fluent_interface>)。

与list.sort相反的是内置函数sorted，它会新建一个列表作为返回值。这个方法可以 接受任何形式的可迭代对象作为参数，甚至包括不可变序列或生成器(见第 14 章)。而 不管 sorted 接受的是怎样的参数，它最后都会返回一个列表。

不管是 list.sort 方法还是 sorted 函数，都有两个可选的关键字参数。

reverse

如果被设定为True，被排序的序列里的元素会以降序输出(也就是说把最大值当作 最小值来排序)。这个参数的默认值是 False。

key

一个只有一个参数的函数，这个函数会被用在序列里的每一个元素上，所产生的结果 将是排序算法依赖的对比关键字。比如说，在对一些字符串排序时，可以用 key=str.lower 来实现忽略大小写的排序，或者是用 key=len 进行基于字符串长度的排 序。这个参数的默认值是恒等函数(identity function)，也就是默认用元素自己的值来排 序。

可选参数key还可以在内置函数min()和max()中起作用。另外，还有些标 准库里的函数也接受这个参数，像 itertools.groupby() 和 heapq.nlargest()

等。

下面通过几个小例子来看看这两个函数和它们的关键字参数： 7

7这几个例子还说明了 Python的排序算法——Timsort——是稳定的，意思是就算两个元素比不出大小，在每次排序的 结果里它们的相对位置是固定的。Timsort在本章结尾的“杂谈”里会有进一步的讨论。

['apple', 'banana', 'grape', 'raspberry'] O

\>>> fruits

['grape', 'raspberry', 'apple', 'banana'] ©

\>>> sorted(fruits, reverse=True)

['raspberry', 'grape', 'banana', 'apple'] ©

\>>> sorted(fruits, key=len)

['grape', 'apple', 'banana', 'raspberry'] ©

\>>> sorted(fruits, key=len, reverse=True)

['raspberry', 'banana', 'grape', 'apple']❺

\>>> fruits

['grape', 'raspberry', 'apple', 'banana'] ©

\>>> fruits.sort()    &

\>>> fruits

['apple', 'banana', 'grape', 'raspberry']❻

#### ❶ 新建了一个按照字母排序的字符串列表。

#### ❷ 原列表并没有变化。

#### ❸ 按照字母降序排序。

❹ 新建一个按照长度排序的字符串列表。因为这个排序算法是稳定的， grape 和 apple 的 长度都是 5，它们的相对位置跟在原来的列表里是一样的。

#### ❺ 按照长度降序排序的结果。结果并不是上面那个结果的完全翻转，因为用到的排序算

法是稳定的，也就是说在长度一样时， grape 和 apple 的相对位置不会改变。

❻ 直到这一步，原列表 fruits 都没有任何变化。

❼ 对原列表就地排序，返回值 None 会被控制台忽略。

❽ 此时 fruits 本身被排序。

已排序的序列可以用来进行快速搜索，而标准库的 bisect 模块给我们提供了二分查找算 法。下一节会详细讲这个函数，顺便还会看看 bisect.insort 如何让已排序的序列保持

有序。
