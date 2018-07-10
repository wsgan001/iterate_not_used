# 第1章 Python 数据模型

Guido 对语言设计美学的深入理解让人震惊。我认识不少很不错的编程语言设计者，他们设计出来的东西确实很精彩，但是从来都不会有用户。<span style="color:red;">好吧。</span>Guido知道如何在理论上做出一定妥协，设计出来的语言让使用者觉得如沐春风，这真是不可多得。


Python 最好的品质之一是一致性。当你使用 Python 工作一会儿后，就会开始理解 Python 语言，并能正确猜测出对你来说全新的语言特征。<span style="color:red;">是这样吗？</span>

然而，如果你带着来自其他面向对象语言的经验进入 Python 的世界，会对 len(colleciton) 而不是 collection.len() 写法觉得不适。<span style="color:red;">对呀，到底为什么是使用 len 而不是 collection.len ？</span>当你进一步理解这种不适感背后的原因之后，会发现这个原因，和它所代表的庞大的设计思想，是形成我们通常说的 “Python风格”(Pythonic) 的关键。这种设计思想完全体现在 Python 的数据模型上，而数据模型所描述的 API，为使用最地道的语言特性来构建你自己的对象提供了工具。<span style="color:red;">这句话不是很明白，数据模型所描述的 API 是什么？</span>

数据模型其实是对 Python 框架的描述，它规范了这门语言自身构建模块的接口，这些模块包括但不限于序列、迭代器、函数、类和上下文管理器。<span style="color:red;">数据模型是什么？</span>

不管在哪种框架下写程序，都会花费大量时间去实现那些会被框架本身调用的方法，Python 也不例外。 Python 解释器碰到特殊的句法时，会使用特殊方法去激活一些基本的对象操作，这些特殊方法的名字以两个下划线开头，以两个下划线结尾(例如`__getitem__`)。比如 `obj[key]` 的背后就是 `__getitem__` 方法，为了能求得 `my_collection[key]` 的值，解释器实际上会调用 `my_collection.__getitem__(key)`。<span style="color:red;">嗯。</span>

这些特殊方法名能让你自己的对象实现和支持以下的语言构架，并与之交互：

- 迭代  <span style="color:red;">一直想知道这个是怎么实现的</span>
- 集合类 <span style="color:red;">这个也想知道</span>
- 属性访问
- 运算符重载
- 函数和方法的调用
- 对象的创建和销毁
- 字符串表示形式和格式化
- 管理上下文(即with块) <span style="color:red;">这个也能实现吗？</span>

<span style="color:red;">不错，挺好的。</span>



> magic 和 dunder
>
>
> 魔术方法(magic method)是特殊方法的昵称。<span style="color:red;">嗯，这个还是要明确下的，因为有的时候忘了，不知道他说的魔术方法到底是什么。</span>有些Python开发者在提到 `__getitem__` 这个特殊方法的时候，会用诸如“下划线－下划线－ getitem” 这种说法，但是显然这种说法会引起歧义，因为像 `__x` 这种命名在 Python 里还有其他含义，但是如果完整地说出 “下划线－下划线－getitem－下划线－下划线” ，又会很麻烦。于是我跟着Steve Holden，一位技术书作者和老师，学会了 “双下一getitem” (dunder-getitem) 这种说法。于是乎，特殊方法也叫双下方法(dunder method)。 <span style="color:red;">好吧，这一段说的是魔术方法的英文的念法。</span>


##  1 一摞Python风格的纸牌

接下来我会用一个非常简单的例子来展示如何实现 `__getitme__` 和 `__len__` 这两个特殊 方法，通过这个例子我们也能见识到特殊方法的强大。

示例 1-1 里的代码建立了一个纸牌类。

示例 1-1 一摞有序的纸牌

```python
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```

<span style="color:red;">这个 namedtuple 是什么？为什么可以这么使用？看到这段代码，感觉震惊了，还可以这么创建 扑克 `[Card(rank, suit) for suit in self.suits for rank in self.ranks]`，厉害。而且，他创建 ranks 和 suits 的过程也挺厉害，简直八仙过海各显神通。</span>

首先，我们用 collections.namedtuple 构建了一个简单的类来表示一张纸牌。<span style="color:red;">嗯，原来是创建一个简单的类，</span>自 Python 2.6 开始， namedtuple 就加入到 Python 里，用以构建只有少数属性但是没有方法的对象，比如数据库条目。<span style="color:red;">嗯，的确，可以用来创建一些对应数据库的类。不错，不过感觉还是用类比较好，可以随时添加方法。</span>如下面这个控制台会话所示，利用 namedtuple，我们可以很轻松地得到一个纸牌对象：

```python
>>> beer_card = Card('7', 'diamonds')
>>> beer_card
Card(rank='7', suit='diamonds')
```

当然，我们这个例子主要还是关注 FrenchDeck 这个类，它既短小又精悍。<span style="color:red;">是的，真的挺精悍的。</span>首先，它跟任何标准 Python 集合类型一样，可以用 `len()` 函数来查看一叠牌有多少张：

```python
>>> deck = FrenchDeck()
>>> len(deck)
52
```

从一叠牌中抽取特定的一张纸牌，比如说第一张或最后一张，是很容易的： deck[0] 或deck[-1]。这都是由 `__getitem__` 方法提供的：

```python
>>> deck[0]
Card(rank='2',suit='spades')
>>> deck[-1]
Card(rank='A',suit='hearts')
```

我们需要单独写一个方法用来随机抽取一张纸牌吗？没必要， Python 已经内置了从一个序列中随机选出一个元素的函数random.choice，我们直接把它用在这一摞纸牌实例上就好：<span style="color:red;">好吧，这也行，OK，`choice` 到底怎么使用的也要总结下。</span>

```python
>>> from random import choice
>>> choice(deck)
Card(rank='3', suit='hearts')
>>> choice(deck)
Card(rank='K', suit='spades')
>>> choice(deck)
Card(rank='2', suit='clubs')
```

现在已经可以体会到通过实现特殊方法来利用 Python 数据模型的两个好处。

- 作为你的类的用户，他们不必去记住标准操作的各式名称(“怎么得到元素的总数？ 是 .size() 还是 .length() 还是别的什么？”)。<span style="color:red;">嗯，是的。不过还是没有解答为什么是len,而不是 collection.len </span>

- 可以更加方便地利用 Python 的标准库，比如 `random.choice` 函数，从而不用重新发明轮子。<span style="color:red;">这个真是很 nice。</span>

而且好戏还在后面。

因为 `__getitem__` 方法把 `[]` 操作交给了 `self._cards` 列表，所以我们的 deck 类自动支持切片 (slicing) 操作。下面列出了查看一摞牌最上面 3 张和只看牌面是A的牌的操作。其中第二种操作的具体方法是，先抽出索引是 12 的那张牌，然后每隔 13 张牌拿 1 张：

```python
>>> deck[:3]
[Card(rank='2', suit='spades'), Card(rank='3', suit='spades'), Card(rank='4', suit='spades')]
>>> deck[12::13]
[Card(rank='A', suit='spades'), Card(rank='A', suit='diamonds'), Card(rank='A', suit='clubs'), Card(rank='A', suit='hearts')]
```

<span style="color:red;">上面这个 `deck[12::13]` 的切片是什么意思？。。基础不扎实。</span>


另外，仅仅实现了 `__getitem__` 方法，这一摞牌就变成可迭代的了：

```python
>>> for card in deck: # doctest: +ELLIPSIS
...     print(card)
Card(rank='2', suit='spades')
Card(rank='3', suit='spades')
Card(rank='4', suit='spades')
...
```

反向迭代也没关系：

```python
>>> for card in reversed(deck):  # doctest: +ELLIPSIS
...   print(card)
Card(rank='A', suit='hearts')
Card(rank='K', suit='hearts')
Card(rank='Q', suit='hearts')
...
```

> doctest 中的省略
>
> 为了尽可能保证书中的 Python 控制台会话内容的正确性，这些内容都是直接从 doctest 里摘录的。在测试中，如果可能的输出过长的话，那么过长的内容就会被如 上面例子的最后一行的省略号（...）所替代。此时就需要 #doctest: +ELLIPSIS 这个指令来保证 doctest 能够通过。要是你自己照着书中例子在控制台中敲代码，可以略过这一指令。<span style="color:red;">这个 doctest 到底怎么使用的？要补充下。</span>

迭代通常是隐式的，譬如说一个集合类型没有实现 `__contains__` 方法，那么 `in` 运算符 就会按顺序做一次迭代搜索。于是，in 运算符可以用在我们的 FrenchDeck 类上，因为它是可迭代的：<span style="color:red;">只要实现了 `__getitem__` 就可以迭代了吗？</span>

```python
>>> Card('Q', 'hearts') in deck True
True
>>> Card('7', 'beasts') in deck False
False
```

那么排序呢？我们按照常规，用点数来判定扑克牌的大小，2 最小、A 最大；同时还要加上对花色的判定，黑桃最大、红桃次之、方块再次、梅花最小。下面就是按照这个规则来给扑克牌排序的函数，梅花 2 的大小是 0，黑桃 A 是 51：

```python
>>> suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)
>>> def spades_high(card):
...     rank_value = FrenchDeck.ranks.index(card.rank)
...     return rank_value * len(suit_values) + suit_values[card.suit]
```

<span style="color:red;">震惊了，这个 spades_high 简直神乎其技，传进来一个牌，然后，返回这个牌的序号。这里面的 FrenchDeck.ranks 是静态变量，因此可以直接拿过来用， 不过这个返回值是不是应该是这样：`len(FrenchDeck.ranks)*suit_values[card.suit]+rank_value` ？ 感觉他写的返回值并不是真正的 index，还是说我理解错了？确认下。</span>

有了 spades_high 函数，就能对这摞牌进行升序排序了：


```python
>>> spades_high(Card('2', 'clubs'))
0
>>> spades_high(Card('A', 'spades'))
51

>>> for card in sorted(deck, key=spades_high):  # doctest: +ELLIPSIS
...      print(card)
Card(rank='2', suit='clubs')
Card(rank='2', suit='diamonds')
Card(rank='2', suit='hearts')
...
Card(rank='A', suit='diamonds')
Card(rank='A', suit='hearts')
Card(rank='A', suit='spades')
```

虽然 FrenchDeck 隐式地继承了 object 类，但功能却不是继承而来的。我们通过数据模型和一些合成来实现这些功能。通过实现 `__len__` 和 `__getitem__` 这两个特殊方 法， FrenchDeck 就跟一个 Python 自有的序列数据类型一样，可以体现出 Python 的核心语言特性（例如迭代和切片）。同时这个类还可以用于标准库中诸如 `random.choice`、 `reversed` 和 `sorted` 这些函数。<span style="color:red;">是的，很厉害。</span>另外，对合成的运用使得 `__len__` 和 `__getitem__` 的具体实现可以代理给 `self._cards` 这个 Python 列表（即 list 对象）。<span style="color:red;">对合成的应用？什么是合成？有使用吗？</span>

按照目前的设计， FrenchDeck 是不能洗牌的，因为这摞牌是不可变的(immutable):卡牌和它们的位置都是固定的，除非我们破坏这个类的封装性，直接对 `_cards` 进行操作。<span style="color:red;">嗯，我现在才知道什么是pythonic ，要是我之前用，肯定就对 `_cards` 直接写方法进行操作了。这个的确不错。不知道有没有什么限制。</span>第 11 章会讲到，其实只需要一行代码来实现 `__setitem__` 方法，洗牌功能就不是问题了。<span style="color:red;">是这样吗？怎么实现的？</span>
