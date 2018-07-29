## 第9章符合Python风格的对象

绝对不要使用两个前导下划线，这是很烦人的自私行为。 1

——Ian Bicking pip、virtualenv 和 Paste 等项目的创建者

I1摘自 Paste 的风格指南([http://pythonpaste.org/StyleGUde.html](http://pythonpaste.org/StyleGuide.html))。

得益于 Python 数据模型，自定义类型的行为可以像内置类型那样自然。实现如此自然的

行为，靠的不是继承，而是鸭子类型(duck typing):我们只需按照预定行为实现对象所 需的方法即可。

前一章分析了很多内置对象的结构和行为，这一章则自己定义类，而且让类的行为跟真正

的 Python 对象一样。

这一章接续第 1 章，说明如何实现在很多 Python 类型中常见的特殊方法。

本章包含以下话题：

•支持用于生成对象其他表示形式的内置函数(如repr()、bytes()，等等)

•使用一个类方法实现备选构造方法

•扩展内置的format()函数和str.format()方法使用的格式微语言

•实现只读属性

•把对象变为可散列的，以便在集合中及作为diet的键使用 •利用__slots__节省内存

我们将开发一个简单的二维欧几里得向量类型，在这个过程中涵盖上述全部话题。

在实现这个类型的中间阶段，我们会讨论两个概念：

•如何以及何时使用@classmethod和@staticmethod装饰器

• Python的私有属性和受保护属性的用法、约定和局限 我们从对象表示形式函数开始。

### 9.1 对象表示形式

每门面向对象的语言至少都有一种获取对象的字符串表示形式的标准方式。 Python 提供了

两种方式。

repr()

以便于开发者理解的方式返回对象的字符串表示形式。

str()

以便于用户理解的方式返回对象的字符串表示形式。

正如你所知，我们要实现 __repr__ 和 __str__ 特殊方法，为 repr() 和 str() 提供支 持。

为了给对象提供其他的表示形式，还会用到另外两个特殊方法： __bytes__ 和 __format__。 __bytes__ 方法与 __str__ 方法类似： bytes() 函数调用它获取对象的 字节序列表示形式。而 __format__ 方法会被内置的 format() 函数和 str.format() 方 法调用，使用特殊的格式代码显示对象的字符串表示形式。我们将在下一个示例中讨论 __bytes__ 方法，随后再讨论 __format__ 方法。

如果你是从Python 2转过来的，记住，在Python 3中，__repr__、__str__ 和__format__都必须返回Unicode字符串(str类型)。只有__bytes__方法应 该返回字节序列(bytes类型)。

### 9.2 再谈向量类

为了说明用于生成对象表示形式的众多方法，我们将使用一个 Vector2d 类，它与第 1 章

中的类似。这一节和接下来的几节会不断实现这个类。我们期望 Vector2d 实例具有的基

本行为如示例 9-1 所示。

示例 9-1 Vector2d 实例有多种表示形式

\>>> v1 = Vector2d(3, 4)

\>>> print(v1.x, vl.y) O

3.0 4.0

\>>> x, y = v1 ©

\>>> x, y (3.0, 4.0)

\>>> v1 ©

Vector2d(3.0, 4.0)

\>>> v1_clone = eval(repr(v1))    ©

\>>> v1 == v1_clone True

\>>> print(v1) ©

(3.0, 4.0)

\>>> octets = bytes(vl) &

\>>> octets

b'd\\x00\\x00\\x00\\x00\\x00\\x00\\x08@\\x00\\x00\\x00\\x00\\x00\\x00\\x10@'

\>>> abs(v1)

5.0

\>>> bool(v1), bool(Vector2d(0, 0))    ©

❶ Vector2d 实例的分量可以直接通过属性访问（无需调用读值方法）。

❷ Vector2d 实例可以拆包成变量元组。

❸ repr 函数调用 Vector2d 实例，得到的结果类似于构建实例的源码。

❹ 这里使用 eval 函数，表明 repr 函数调用 Vector2d 实例得到的是对构造方法的准确 表述。 2

2这里使用eval函数克隆对象是为了说明repr方法。使用copy.copy函数克隆实例更安全也更快速。

❺ Vector2d 实例支持使用 == 比较；这样便于测试。

❻ print 函数会调用 str 函数，对 Vector2d 来说，输出的是一个有序对。

❼ bytes 函数会调用 __bytes__ 方法，生成实例的二进制表示形式。

❽ abs 函数会调用 __abs__ 方法，返回 Vector2d 实例的模。

❾bool函数会调用_bool__方法，如果Vector2d实例的模为零，返回False，否则

返回 True。

示例 9-1 中的 Vector2d 类在 vector2d_v0.py 文件中实现（见示例 9-2）。这段代码基于 示例 1-2，除了 == 之外（在测试中用得到），其他中缀运算符将在第 13 章实现。现 在， Vector2d 用到了几个特殊方法，这些方法提供的操作是 Python 高手期待设计良好的 对象所提供的。

#### 示例9-2 vector2d_v0.py：目前定义的都是特殊方法

| from array import array |                                                          |                                                              |          |
| ----------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| import m                | ath                                                      |                                                              |          |
| class Vector2d:         |                                                          |                                                              |          |
| typecode =              | 'd' O                                                    |                                                              |          |
| def                     | __init_                                                  | _(self、 X、 y):                                             |          |
|                         | self.X                                                   | =float(x) &                                                  |          |
|                         | self.y                                                   | = float(y)                                                   |          |
| def                     | __iter_                                                  | _(self):                                                     |          |
|                         | return                                                   | (i for i in (self.x、 self.y)) ©                             |          |
| def                     | __repr_                                                  | _(self):                                                     |          |
|                         | class_name = type(self).__name__                         |                                                              |          |
|                         | return                                                   | '{}({!r}、 {!r})'.format(class_name、                        | *self) © |
| def                     | __str__                                                  | (self):                                                      |          |
|                         | return str(tuple(self))❺                                 |                                                              |          |
| def                     | __bytes                                                  | __(self):                                                    |          |
|                         | return                                                   | (bytes([ord(self.typecode)]) +    ©bytes(array(self.typecode、 self))) | &        |
| def                     | __eq__(self、 other):return tuple(self) == tuple(other)❻ |                                                              |          |
| def                     | __abs__                                                  | (self):                                                      |          |
|                         | return math.hypot(self.x、 self.y) ©                     |                                                              |          |
| def                     | __bool_                                                  | _(self):                                                     |          |
|                         | return                                                   | bool(abs(self)) ©                                            |          |

#### ❶ typecode 是类属性，在 Vector2d 实例和字节序列之间转换时使用。

❷ 在 __init__ 方法中把 x 和 y 转换成浮点数，尽早捕获错误，以防调用 Vector2d 函 数时传入不当参数。

❸ 定义 __iter__ 方法，把 Vector2d 实例变成可迭代的对象，这样才能拆包（例 如，X、y = my vector）。这个方法的实现方式很简单，直接调用生成器表达式一个接

一个产出分量。 3

I 3这一行也可以写成yield self.x; yield.self.y。第14章会进一步讨论__iter__特殊方法、生成器表达式和 yield关键字。

❹ __repr__ 方法使用 {!r} 获取各个分量的表示形式，然后插值，构成一个字符串；因 为 Vector2d 实例是可迭代的对象，所以 *self 会把 x 和 y 分量提供给 format 函数。

❺ 从可迭代的 Vector2d 实例中可以轻松地得到一个元组，显示为一个有序对。

❻为了生成字节序列，我们把typecode转换成字节序列，然后......

❼ ……迭代 Vector2d 实例，得到一个数组，再把数组转换成字节序列。

❽ 为了快速比较所有分量，在操作数中构建元组。对 Vector2d 实例来说，可以这样

做，不过仍有问题。参见下面的警告。

❾ 模是 x 和 y 分量构成的直角三角形的斜边长。

❿ __bool__ 方法使用 abs(self) 计算模，然后把结果转换成布尔值，因此， 0.0 是 False，非零值是True。

示例9-2中的__eq__方法，在两个操作数都是Vector2d实例时可用，不 过拿Vector2d实例与其他具有相同数值的可迭代对象相比，结果也是True (如 Vector(3, 4) == [3, 4])。这个行为可以视作特性，也可以视作缺陷。第 13 章

讲到运算符重载时才能进一步讨论。

我们己经定义了很多基本方法，但是显然少了一个操作：使用 bytes() 函数生成的二进 制表示形式重建 Vector2d 实例。

### 9.3 备选构造方法

我们可以把 Vector2d 实例转换成字节序列了；同理，也应该能从字节序列转换成

Vector2d 实例。在标准库中探索一番之后，我们发现 array.array 有个类方法 .frombytes （2+9+1节介绍过）正好符合需求。下面在vector2d_v1+py （见示例9-3）中为 Vector2d 定义一个同名类方法。

示例 9-3 vector2d_v1+py 的一部分：这段代码只列出了 frombytes 类方法，要添加 到vector2d_v0+py （见示例9-2）中定义的Vector2d类中

@classmethod O def frombytes(cls, octets): © typecode = chr(octets[0]) © memv = memoryview(octets[1:]).cast(typecode) © return cls(*memv)❺

❶ 类方法使用 classmethod 装饰器修饰。

❷ 不用传入 self 参数；相反，要通过 cls 传入类本身。

❸ 从第一个字节中读取 typecode。

❹使用传入的octets字节序列创建一个memoryview，然后使用typecode转换。4 42+9+2节简单介绍过memoryview，说明了它的.cast方法。

❺拆包转换后的memoryview，得到构造方法所需的一对参数。

我们用的 classmethod 装饰器是 Python 专用的，下面讲解一下。

### 9.4 classmethod与staticmethod

Python教程没有提到classmethod装饰器，也没有提到staticmethod。学过Java面向 对象编程的人可能觉得奇怪，为什么 Python 提供两个这样的装饰器，而不是只提供一 个？

先来看classmethod。示例9-3展示了它的用法：定义操作类，而不是操作实例的方 法。 classmethod 改变了调用方法的方式，因此类方法的第一个参数是类本身，而不是 实例。 classmethod 最常见的用途是定义备选构造方法，例如示例 9-3 中的 frombytes。注意，frombytes的最后一行使用cls参数构建了一个新实例，即 cls(*memv)。按照约定，类方法的第一个参数名为cls (但是Python不介意具体怎么命 名)。

staticmethod 装饰器也会改变方法的调用方式，但是第一个参数不是特殊的值。其实， 静态方法就是普通的函数，只是碰巧在类的定义体中，而不是在模块层定义。示例 9-4 对 classmethod 和 staticmethod 的行为做了对比。

示例 9-4 比较 classmethod 和 staticmethod 的行为

\>>> class Demo:

...    @classmethod

...    def klassmeth(*args):

…    return args # O

...    @staticmethod

...    def statmeth(*args):

…    return args # ©

\>>> Demo.klassmeth() # ©

(<class '__main__.Demo'>,)

\>>> Demo.klassmeth('spam')

(<class '__main__.Demo'>, 'spam') >>> Demo.statmeth() # ©

()

\>>> Demo.statmeth('spam') ('spam',)

❶ klassmeth 返回全部位置参数。

❷ statmeth 也是。

❸不管怎样调用Demo.klassmeth，它的第一个参数始终是Demo类。 ❹ Demo.statmeth 的行为与普通的函数相似。

classmethod装饰器非常有用，但是我从未见过不得不用staticmethod的

情况。如果想定义不需要与类交互的函数，那么在模块中定义就好了。有时，函数虽

然从不处理类，但是函数的功能与类紧密相关，因此想把它放在近处。即便如此，在

#### 同一模块中的类前面或后面定义函数也就行了。 5

5本书的技术审校之一 Leonardo Rochael 不同意我对 staticmethod 的见解，作为反驳，他推荐阅读 Julien Danjou 写的

一篇博客文章，题为“The Definitive Guide on How to Use Static, Class or Abstract Methods in

Python”(<https://julien.danjou.info/blog/2013/guide-python-static-class-abstract-methods>) 。 Danjou 的这篇文章写得很好，我推 荐阅读。但是，我对 staticmethod 的观点依然不变。请读者自辨。

#### 现在，我们对 classmethod 的作用已经有所了解(而且知道 staticmethod 不是特别有 用)，下面继续讨论对象的表示形式，说明如何支持格式化输出。

### 9.5 格式化显示

内置的 format() 函数和 str.format() 方法把各个类型的格式化方式委托给相应的 .__format__(format_spec) 方法。 format_spec 是格式说明符，它是：

•    format(my_obj, format_spec)的第二个参数，或者

•    str.format()方法的格式字符串，{}里代换字段中冒号后面的部分 例如：

\>>> brl = 1/2.43    # BRL到USD的货币兑换比价

\>>> brl

0.4115226337448559

\>>> format(brl, '0.4f')    # O

'0.4115'

\>>> '1 BRL = {rate:0.2f} USD'.format(rate=brl) # © '1 BRL = 0.41 USD'

❶ 格式说明符是 '0.4f'。

❷格式说明符是’0.2f'。代换字段中的'rate'子串是字段名称，与格式说明符无关， 但是它决定把 .format() 的哪个参数传给代换字段。

第2条标注指出了一个重要知识点：’{0.mass:5.3e}'这样的格式字符串其实包含两部 分，冒号左边的 '0.mass' 在代换字段句法中是字段名，冒号后面的 '5.3e' 是格式说明

符。格式说明符使用的表示法叫格式规范微语言(“Format Specification Mini-Language”， [https://docs+python+org/3/library/string+html#formatspec](https://docs.python.org/3/library/string.html%23formatspec)) 。

如果你对format()和str.format()都感到陌生，根据我的教学经验，最好

先学 format() 函数，因为它只使用格式规范微语言。学会这些表示法之后，再阅读

格式字符串句法(“Format String

Syntax”， [https://docs+python+org/3/library/string+html#formatspec](https://docs.python.org/3/library/string.html%23formatspec)) ，学习 str.format() 方法使用的 {:} 代换字段表示法(包含转换标志 !s、!r 和 !a)。

格式规范微语言为一些内置类型提供了专用的表示代码。比如， b 和 x 分别表示二进制和 十六进制的 int 类型， f 表示小数形式的 float 类型，而 % 表示百分数形式：

\>>> format(42, 'b') '101010'

\>>> format(2/3, '.1%')

'66.7%'

格式规范微语言是可扩展的，因为各个类可以自行决定如何解释 format_spec 参数。例 如， datetime 模块中的类，它们的 __format__ 方法使用的格式代码与 strftime() 函

#### 数一样。下面是内置的 format() 函数和 str.format() 方法的几个示例：

\>>> from datetime import datetime >>> now = datetime.now()

\>>> format(now、 '%H:%M:%S')

'18:49:05'

\>>> "It's now {:%I:%M %p}".format(now) "It's now 06:49 PM"

#### 如果类没有定义__format__方法，从object继承的方法会返回str(my_object)。我

们为 Vector2d 类定义了 __str__ 方法，因此可以这样做：

\>>> v1 = Vector2d(3、 4)

\>>> format(v1)

'(3.0、 4.0)'

#### 然而，如果传入格式说明符， object.__format__ 方法会抛出 TypeError：

\>>> format(v1、 '.3f')

Traceback (most recent call last):

TypeError: non-empty format string passed to object.format

#### 我们将实现自己的微语言来解决这个问题。首先，假设用户提供的格式说明符是用于格式

化向量中各个浮点数分量的。我们想达到的效果是：

\>>> v1 = Vector2d(3、 4)

\>>> format(v1)

'(3.0、 4.0)'

\>>> format(v1、 '.2f') '(3.00、 4.00)'

\>>> format(v1、 '.3e') '(3.000e+00、 4.000e+00)

#### 实现这种输出的 __format__ 方法如示例 9-5 所示。 示例 9-5 Vector2d.__format__ 方法，第 1 版

\#在Vector2d类中定义

def __format__(self、 fmt_spec=''):

components = (format(c、 fmt_spec) for c in self) # O return '({}、 {})'.format(*components) # ©

#### ❶ 使用内置的 format 函数把 fmt_spec 应用到向量的各个分量上，构建一个可迭代的格 式化字符串。

❷ 把格式化字符串代入公式 '(X、 y)' 中。

下面要在微语言中添加一个自定义的格式代码：如果格式说明符以 'p' 结尾，那么在极

坐标中显示向量，即＜r, 0 ＞，其中r是模，0 (西塔)是弧度；其他部分(’p'之前的 部分)像往常那样解释。

为自定义的格式代码选择字母时，我会避免使用其他类型用过的字母。在格式 规范微语言([https://docs.python+org/3/library/string+html#formatspec](https://docs.python.org/3/library/string.html%23formatspec))中我们看到，整

数使用的代码有’bcdoxXn '，浮点数使用的代码有'eEfFgGn%'，字符串使用的代码 有’s'。因此，我为极坐标选的代码是’p'。各个类使用自己的方式解释格式代码， 在自定义的格式代码中重复使用代码字母不会出错，但是可能会让用户困惑。

对极坐标来说，我们已经定义了计算模的 __abs__ 方法，因此还要定义一个简单的 angle 方法，使用 math.atan2() 函数计算角度。 angle 方法的代码如下：

\#在Vector2d类中定义 def angle(self):

return math.atan2(self.y, self.x)

这样便可以增强 __format__ 方法，计算极坐标，如示例 9-6 所示。

示例 9-6 Vector2d.__format__ 方法，第 2 版，现在能计算极坐标了

def __format__(self, fmt_spec=''): if fmt_spec.endswith('p'): O

fmt_spec = fmt_spec[:-1] ©

coords = (abs(self), self.angle()) ©

outer_fmt = '<{}, {}>' ©

else:

coords = self ❺ outer_fmt = '({}, {})'    ©

components = (format(c, fmt_spec) for c in coords) & return outer_fmt.format(*components)❻

❶ 如果格式代码以 'p' 结尾，使用极坐标。

❷ 从 fmt_spec 中删除 'p' 后缀。

❸构建一个元组，表示极坐标：(magnitude, angle)。

❹ 把外层格式设为一对尖括号。

❺ 如果不以 'p' 结尾，使用 self 的 x 和 y 分量构建直角坐标 ❻ 把外层格式设为一对圆括号。

❼ 使用各个分量生成可迭代的对象，构成格式化字符串。

❽ 把格式化字符串代入外层格式。

#### 示例 9-6 中的代码得到的结果如下：

\>>> format(Vector2d(1, 1), 'p') '<1.4142135623730951, 0.7853981633974483>' >>> format(Vector2d(1, 1), '.3ep') '<1.414e+00, 7.854e-01>'

\>>> format(Vector2d(1, 1), '0.5fp') '<1.41421, 0.78540>'

#### 如本节所示，为用户自定义的类型扩展格式规范微语言并不难。

#### 下面换个话题，它不仅事关对象的外观：我们将把 Vector2d 变成可散列的，这样便可以

构建向量集合，或者把向量当作 dict 的键使用。不过在此之前，必须让向量不可变。详

情参见下一节。

### 9.6 可散列的Vector2d

#### 按照定义，目前Vector2d实例是不可散列的，因此不能放入集合（set）中:

| >>> v1 = Vector2d(3、 4)>>> hash(v1)Traceback (most recent call last): |            |
| ------------------------------------------------------------ | ---------- |
| TypeError: unhashable type:                                  | 'Vector2d' |
| >>> set([v1])                                                |            |
| Traceback (most recent call                                  | last):     |
| TypeError: unhashable type:                                  | 'Vector2d' |

#### 为了把 Vector2d 实例变成可散列的，必须使用 __hash__ 方法（还需要 __eq__ 方法，

前面已经实现了）。此外，还要让向量不可变，详情参见第 3 章的附注栏“什么是可散列 的数据类型”。

目前，我们可以为分量赋新值，如 v1.x = 7， Vector2d 类的代码并不阻止这么做。我 们想要的行为是这样的：

\>>> v1.x、 v1.y (3.0、 4.0)

\>>> v1.x = 7

Traceback (most recent call last): AttributeError: can't set attribute

#### 为此，我们要把 x 和 y 分量设为只读特性，如示例 9-7 所示。

#### 示例9-7 vector2d_v3.py：这里只给出了让Vector2d不可变的代码，完整的代码清

单在示例 9-9 中

class Vector2d: typecode = 'd'

def __init__(self、 x、 y): self.__x = float(x) O self.__y = float(y)

@property © def x(self): ©

return self.__x o

@property ❺ def y(self):

return self.__y

def __iter__(self):

return (i for i in (self.x、 self.y)) ©

\# 下面是其他方法（排版需要，省略了）

❶ 使用两个前导下划线（尾部没有下划线，或者有一个下划线），把属性标记为私有

的。 6

| 6根据本章开头引用的那句话，这不符合Ian Bicking的建议。私有属性的优缺点参见后面的9.7节。

❷ @property 装饰器把读值方法标记为特性。

❸ 读值方法与公开属性同名，都是 x。

❹ 直接返回 self.__x。

❺ 以同样的方式处理 y 特性。

❻ 需要读取 x 和 y 分量的方法可以保持不变，通过 self.x 和 self.y 读取公开特性，而 不必读取私有属性，因此上述代码清单省略了这个类的其他代码。

Vector.x和Vector.y是只读特性。读写特性在第19章讨论，届时会深入说 明 @property 装饰器。

注意，我们让这些向量不可变是有原因的，因为这样才能实现 __hash__ 方法。这个方法 应该返回一个整数，理想情况下还要考虑对象属性的散列值（__eq__ 方法也要使用）， 因为相等的对象应该具有相同的散列值。根据特殊方法 __hash__ 的文档

<https://docs.python.org/3/reference/datamodel.html>），最好使用位运算符异或（A）混合各

分量的散列值——我们会这么做。 Vector2d.__hash__ 方法的代码十分简单，如示例 98 所示。

示例 9-8 vector2d_v3.py:实现 __hash__ 方法

\#在Vector2d类中定义

def __hash__(self):

return hash(self.x) A hash(self.y)

添加 __hash__ 方法之后，向量变成可散列的了：

\>>> v1 = Vector2d(3, 4)

\>>> v2 = Vector2d(3.1, 4.2)

\>>> hash(v1), hash(v2)

(7, 384307168202284039)

\>>> set([v1, v2])

{Vector2d(3.1, 4.2), Vector2d(3.0, 4.0)}

![img](08414584Python-44.jpg)



要想创建可散列的类型，不一定要实现特性，也不一定要保护实例属性。只需 正确地实现 __hash__ 和 __eq__ 方法即可。但是，实例的散列值绝不应该变化，因 此我们借机提到了只读特性。

如果定义的类型有标量数值，可能还要实现 __int__ 和 __float__ 方法(分别被 int() 和 float() 构造函数调用)，以便在某些情况下用于强制转换类型。此外，还有用于支 持内置的 complex() 构造函数的 __complex__ 方法。 Vector2d 或许应该提供 __complex__ 方法，不过我把它留作练习给读者。

我们一直在定义 Vector2d 类，也列出了很多代码片段，示例 9-9 是整理后的完整代码清

单，保存在 vector2d_v3.py 文件中，包含开发时我编写的全部 doctest。

#### 示例 9-9 vector2d_v3+py：完整版

A two-dimensional vector class

\>>> v1 = Vector2d(3, 4)

\>>> print(v1.x, v1.y)

3.0 4.0 >>> x, y = v1

\>>> x, y (3.0, 4.0)

\>>> v1

Vector2d(3.0, 4.0)

\>>> v1_clone = eval(repr(v1))

\>>> v1 == v1_clone True

\>>> print(v1)

(3.0, 4.0)

\>>> octets = bytes(v1)

\>>> octets

b'd\\x00\\x00\\x00\\x00\\x00\\x00\\x08@\\x00\\x00\\x00\\x00\\x00\\x00\\x10@'

\>>> abs(v1)

5.0

\>>> bool(v1), bool(Vector2d(0, 0))

(True, False)

Test of ''.frombytes()'' class method:

\>>> v1_clone = Vector2d.frombytes(bytes(v1)) >>> v1_clone Vector2d(3.0, 4.0)

\>>> v1 == v1_clone True

Tests of ''format()'' with Cartesian coordinates:

\>>> format(v1)

'(3.0, 4.0)'

\>>> format(v1, '.2f')

'(3.00, 4.00)'

\>>> format(v1、 '.3e')

'(3.000e+00、 4.000e+00)

Tests of the ''angle'' method::

\>>> Vector2d(0、 0).angle()

0.0

\>>> Vector2d(1、 0).angle()

0.0

\>>> epsilon = 10**-8

\>>> abs(Vector2d(0、 1).angle() - math.pi/2) < epsilon True

\>>> abs(Vector2d(1、 1).angle() - math.pi/4) < epsilon True

Tests of ''format()'' with polar coordinates:

\>>> format(Vector2d(1、 1)、 'p') # doctest:+ELLIPSIS

'<1.414213...、 0.785398...>'

\>>> format(Vector2d(1、 1)、 '.3ep')

'<1.414e+00、 7.854e-01>'

\>>> format(Vector2d(1、 1)、 '0.5fp')

'<1.41421、 0.78540>'

Tests of 'x' and 'y' read-only properties:

\>>> v1.x、 v1.y (3.0、 4.0)

\>>> v1.x = 123

Traceback (most recent call last): AttributeError: can't set attribute

Tests of hashing:

\>>> v1 = Vector2d(3、 4)

\>>> v2 = Vector2d(3.1、 4.2) >>> hash(v1)、 hash(v2)

(7、 384307168202284039)

\>>> len(set([v1、 v2]))

2

from array import array import math

class Vector2d: typecode = 'd'

def __init__(self、 x、 y): self.__x = float(x) self.__y = float(y)

@property def x(self):

return self.__x

@property def y(self):

return self.__y

def __iter__(self):

return (i for i in (self.x, self.y))

def __repr__(self):

class_name = type(self).__name__

return '{}({!r}, {!r})'.format(class_name, *self)

def __str__(self):

return str(tuple(self))

def __bytes__(self):

return (bytes([ord(self.typecode)]) +

bytes(array(self.typecode, self)))

def __eq__(self, other):

return tuple(self) == tuple(other)

def __hash__(self):

return hash(self.x) A hash(self.y)

def __abs__(self):

return math.hypot(self.x, self.y)

def __bool__(self):

return bool(abs(self))

def angle(self):

return math.atan2(self.y, self.x)

def __format__(self, fmt_spec=''): if fmt_spec.endswith('p'):

fmt_spec = fmt_spec[:-1]

coords = (abs(self), self.angle())

outer_fmt = '<{}, {}>' else:

coords = self

outer_fmt = '({}, {})'

components = (format(c, fmt_spec) for c in coords) return outer_fmt.format(*components)

@classmethod

def frombytes(cls, octets): typecode = chr(octets[0]) memv = memoryview(octets[1:]).cast(typecode) return cls(*memv)

#### 小结一下，前两节说明了一些特殊方法，要想得到功能完善的对象，这些方法可能是必备

的。当然，如果你的应用用不到，就没必要全部实现这些方法。客户并不关心你的对象是

否符合 Python 风格。

示例 9-9 中定义的 Vector2d 类只是为了教学，我们为它定义了许多与对象表示形式有关

的特殊方法。不是每个用户自定义的类都要这样做。

下一节暂时不继续定义 Vector2d 类了，我们将讨论 Python 对私有属性（带两个下划线

前缀的属性，如self.__x）的设计方式及其缺点。

### 9.7 Python的私有属性和“受保护的”属性

Python 不能像 Java 那样使用 private 修饰符创建私有属性，但是 Python 有个简单的机 制，能避免子类意外覆盖“私有”属性。

举个例子。有人编写了一个名为 Dog 的类，这个类的内部用到了 mood 实例属性，但是没

有将其开放。现在，你创建了 Dog类的子类：Beagle。如果你在毫不知情的情况下又创 建了名为 mood 的实例属性，那么在继承的方法中就会把 Dog 类的 mood 属性覆盖掉。这

是个难以调试的问题。

为了避免这种情况，如果以 __mood 的形式（两个前导下划线，尾部没有或最多有一个下 划线）命名实例属性， Python 会把属性名存入实例的 __dict__ 属性中，而且会在前面加

上一个下划线和类名。因此，对Dog类来说，__mood会变成_Dog__mood;对Beagle 类来说，会变成 _Bea gle__mood 。这个语言特性叫名称改写（ name mangling） 。

示例 9-10 以示例 9-7 中定义的 Vector2d 类为例来说明名称改写。

示例 9-10 私有属性的名称会被“改写”，在前面加上下划线和类名

\>>> v1 = Vector2d（3、 4）

\>>> v1.__dict__

{'_Vector2d__y': 4.0、 '_Vector2d__x': 3.0} >>> v1._Vector2d__x

3.0

名称改写是一种安全措施，不能保证万无一失：它的目的是避免意外访问，不能防止故意 做错事（图 9-1 也是一种保护装置）。

图 9-1：把手上的盖子是种保护装置，而不是安全装置：它能避免意外触动把手，但 是不能防止有意转动 如示例 9-10 中的最后一行所示，只要知道改写私有属性名的机制，任何人都能直接读取 私有属性——这对调试和序列化倒是有用。此外，只要编写 v1._Vector__x = 7 这样的 代码，就能轻松地为 Vector2d 实例的私有分量直接赋值。如果真在生产环境中这么做 了，出问题时可别抱怨。

不是所有 Python 程序员都喜欢名称改写功能，也不是所有人都喜欢 self.__x 这种不对 称的名称。有些人不喜欢这种句法，他们约定使用一个下划线前缀编写“受保护”的属性

(如self._x)。批评使用两个下划线这种改写机制的人认为，应该使用命名约定来避 免意外覆盖属性。本章开头引用了多产的 Ian Bicking 的一句话，那句话的完整表述如下：

绝对不要使用两个前导下划线，这是很烦人的自私行为。如果担心名称冲突，应该明 确使用一种名称改写方式(如_MyThing_blahblah)。这其实与使用双下划线一

样，不过自己定的规则比双下划线易于理解。 7

|7摘自 Paste 的风格指南(<http://pythonpaste.org/StyleGuide.html>)。

Python 解释器不会对使用单个下划线的属性名做特殊处理，不过这是很多 Python 程序员

严格遵守的约定，他们不会在类外部访问这种属性。 8 遵守使用一个下划线标记对象的私 有属性很容易，就像遵守使用全大写字母编写常量那样容易。

8不过在模块中，顶层名称使用一个前导下划线的话，的确会有影响：对 from mymod import * 来说， mymod 中前缀 为下划线的名称不会被导入。然而，依旧可以使用 from mymod import _privatefunc 将其导入。 Python 教程的 6.1 节“More on Modules” ([https://docs.python.org/3/tutorial/modules.html#more-on-modules](https://docs.python.org/3/tutorial/modules.html%23more-on-modules))说明了这一点。

Python 文档的某些角落把使用一个下划线前缀标记的属性称为“受保护的”属性。 9 使用 self._x 这种形式保护属性的做法很常见，但是很少有人把这种属性叫作“受保护的”属 性。有些人甚至将其称为“私有”属性。

| 9gettext 模块中就有一个例子([https://docs.python.org/3/library/gettext.html#gettext.NuflTranslations](https://docs.python.org/3/library/gettext.html%23gettext.NullTranslations))。

总之， Vector2d 的分量都是“私有的”，而且 Vector2d 实例都是“不可变的”。我用了两

对引号，这是因为并不能真正实现私有和不可变。 10

10如果这个说法让你感到沮丧，而且让你觉得在这方面Python应该向Java看齐的话，那么别去读本章的“杂谈”，我在 其中对Java的private修饰符的相对强度进行了探讨。

下面继续定义 Vector2d 类。在最后一节中，我们将讨论一个特殊的属性(不是方法)，

它会影响对象的内部存储，对内存用量可能也有重大影响，不过对对象的公开接口没什么

影响。这个属性是 __slots__。

### 9.8 使用 __slots__ 类属性节省空间

默认情况下， Python 在各个实例中名为 __dict__ 的字典里存储实例属性。如 3.9.3 节所 述，为了使用底层的散列表提升访问速度，字典会消耗大量内存。如果要处理数百万个属 性不多的实例，通过 __slots__ 类属性，能节省大量内存，方法是让解释器在元组中存 储实例属性，而不用字典。

继承自超类的_sl0ts__属性没有效果。Python只会使用各个类中定义的 __slots__ 属性。

定义 __slots__ 的方式是，创建一个类属性，使用 __slots__ 这个名字，并把它的值设 为一个字符串构成的可迭代对象，其中各个元素表示各个实例属性。我喜欢使用元组，因 为这样定义的 __slots__ 中所含的信息不会变化，如示例 9-11 所示。

示例 9-11 vector2d_v3_slots.py:只在 Vector2d 类中添加了 __slots__ 属性

class Vector2d:

__slots__ = ('__x', '__y') typecode = 'd'

\# 下面是各个方法(因排版需要而省略了)

在类中定义 __slots__ 属性的目的是告诉解释器： “这个类中的所有实例属性都在这儿 了！”这样， Python 会在各个实例中使用类似元组的结构存储实例变量，从而避免使用消

耗内存的 __dict__ 属性。如果有数百万个实例同时活动，这样做能节省大量内存。

如果要处理数百万个数值对象，应该使用NumPy数组（参见2.9.3节）。

NumPy 数组能高效使用内存，而且提供了高度优化的数值处理函数，其中很多都一

次操作整个数组。我定义 Vector2d 类的目的是讨论特殊方法，因为我不太想随便举

些例子。

在示例 9-12 中，我们运行了两个构建列表的脚本，这两个脚本都使用列表推导创建 10

000 000 个 Vector2d 实例。 mem_test.py 脚本的命令行参数是一个模块的名字，模块中定 义了不同版本的 Vector2d 类。第一次运行使用的是 vector2d_v3.Vector2d 类（在示 例 9-7 中定义），第二次运行使用的是定义了 __slots__ 的

vector2d_v3_slots.Vector2d 类。

示例9-12 mem_test.py使用指定模块（如vector2d_v3.py）中定义的Vector2d类创

建 10 000 000 个实例

Selected Vector2d type: vector2d_v3.Vector2d Creating 10,000,000 Vector2d instances Initial RAM usage:    5,623,808

Final RAM usage:    1,558,482,944

real 0m16.721s

user 0m15.568s

sys 0m1.149s

$ time python3 mem_test.py vector2d_v3_slots.py Selected Vector2d type: vector2d_v3_slots.Vector2d

Creating 10,000,000 Vector2d instances

Initial RAM usage:    5,718,016

Final RAM usage:    655,466,496

real 0m13.605s

user 0m13.163s

sys 0m0.434s

如示例 9-12 所示，在 10 000 000 个 Vector2d 实例中使用 __dict__ 属性时， RAM 用量

高达1.5GB;而在Vector2d类中定义__slots__属性之后，RAM用量降到了 655MB。

此外，定义了 __slots__ 属性的版本运行速度也更快。这个测试中使用的 mem_test.py 脚 本其实只用于加载一个模块、检查内存用量和格式化结果，所用的代码与本章没有太大关

联，因此放入附录 A 中的示例 A-4 里。

在类中定义_slots__属性之后，实例不能再有__slots__中所列名称之 外的其他属性。这只是一个副作用，不是 __slots__ 存在的真正原因。不要使用 __slots__ 属性禁止类的用户新增实例属性。 __slots__ 是用于优化的，不是为了

约束程序员。

然而， “节省的内存也可能被再次吃掉”：如果把 '__dict__' 这个名称添加到

__slots__ 中，实例会在元组中保存各个实例的属性，此外还支持动态创建属性，这些 属性存储在常规的 __dict__ 中。当然，把 '__dict__' 添加到 __slots__ 中可能完全

违背了初衷，这取决于各个实例的静态属性和动态属性的数量及其用法。粗心的优化甚至

比提早优化还糟糕。

此外，还有一个实例属性可能需要注意，即 __weakref__ 属性，为了让对象支持弱引用 (参见 8.6 节)，必须有这个属性。用户定义的类中默认就有 __weakref__ 属性。可 是，如果类中定义了 __slots__ 属性，而且想把实例作为弱引用的目标，那么要把 '__weakref__' 添加到 __slots__ 中。

综上， __slots__ 属性有些需要注意的地方，而且不能滥用，不能使用它限制用户能赋 值的属性。处理列表数据时 __slots__ 属性最有用，例如模式固定的数据库记录，以及

特大型数据集。然而，如果你经常处理大量数据，一定要了解一下

NumPy (<http://www.numpy.org>)；此夕卜，数据分析库 pandas (<http://pandas.pydata.org>)也

值得了解，这个库可以处理非数值数据，而且能导入 / 导出很多不同的列表数据格式。

__slots__ 的问题

总之，如果使用得当， __slots__ 能显著节省内存，不过有几点要注意。

•每个子类都要定义__slots__属性，因为解释器会忽略继承的__slots__属性。

•实例只能拥有__slots__中列出的属性，除非把’__diet__'加入__slots__中

（这样做就失去了节省内存的功效）。

•如果不把’__weakref__'加入__slots__，实例就不能作为弱引用的目标。

如果你的程序不用处理数百万个实例，或许不值得费劲去创建不寻常的类，那就禁止它创

建动态属性或者不支持弱引用。与其他优化措施一样，仅当权衡当下的需求并仔细搜集资

料后证明确实有必要时，才应该使用 __slots__ 属性。

本章最后一个话题讨论如何在实例和子类中覆盖类属性。

### 9.9 覆盖类属性

Python 有个很独特的特性：类属性可用于为实例属性提供默认值。 Vector2d 中有个 typecode 类属性， __bytes__ 方法两次用到了它，而且都故意使用 self.typecode 读 取它的值。因为 Vector2d 实例本身没有 typecode 属性，所以 self.typecode 默认获 取的是 Vector2d.typecode 类属性的值。

但是，如果为不存在的实例属性赋值，会新建实例属性。假如我们为 typecode 实例属性 赋值，那么同名类属性不受影响。然而，自此之后，实例读取的 self.typecode 是实例

属性typecode，也就是把同名类属性遮盖了。借助这一特性，可以为各个实例的 typecode 属性定制不同的值。

Vector2d.typecode属性的默认值是’d'，即转换成字节序列时使用8字节双精度浮点 数表示向量的各个分量。如果在转换之前把 Vector2d 实例的 typecode 属性设为 'f'，

那么使用 4 字节单精度浮点数表示各个分量，如示例 9-13 所示。

我们在讨论如何添加自定义的实例属性，因此示例9-13使用的是示例9-9中 不带 __slots__ 属性的 Vector2d 类。

示例 9-13 设定从类中继承的 typecode 属性，自定义一个实例属性

\>>> from vector2d_v3 import Vector2d >>> v1 = Vector2d(1.1, 2.2)

\>>> dumpd = bytes(v1)

\>>> dumpd

b'd\x9a\x99\x99\x99\x99\x99\xf1?\x9a\x99\x99\x99\x99\x99\x01@'

\>>> len(dumpd) # O

17

\>>> vl.typecode = 'f'    # ©

\>>> dumpf = bytes(v1)

\>>> dumpf

b'f\xcd\xcc\x8c?\xcd\xcc\x0c@'

\>>> len(dumpf) # ©

9

\>>> Vector2d.typecode # ©

'd'

❶ 默认的字节序列长度为 17 个字节。

❷ 把 v1 实例的 typecode 属性设为 'f'。

❸ 现在得到的字节序列是 9 个字节长。

❹ Vector2d.typecode 属性的值不变，只有 v1 实例的 typecode 属性使用 'f'。

现在你应该知道为什么要在得到的字节序列前面加上 typecode 的值了：为了支持不同的

格式。

如果想修改类属性的值，必须直接在类上修改，不能通过实例修改。如果想修改所有实例 (没有 typecode 实例变量)的 typecode 属性的默认值，可以这么做：

\>>> Vector2d.typecode = 'f

然而，有种修改方法更符合 Python 风格，而且效果持久，也更有针对性。类属性是公开

的，因此会被子类继承，于是经常会创建一个子类，只用于定制类的数据属性。 Django

基于类的视图就大量使用了这个技术。具体做法如示例 9-14 所示。

示例 9-14 ShortVector2d 是 Vector2d 的子类，只用于覆盖 typecode 的默认值

\>>> from vector2d_v3 import Vector2d >>> class ShortVector2d(Vector2d): # o ... typecode = 'f'

\>>> sv = ShortVector2d(1/11, 1/27)    # ©

\>>> sv

ShortVector2d(0.09090909090909091, 0.037037037037037035)    # ©

\>>> len(bytes(sv)) # ©

9

❶ 把 ShortVector2d 定义为 Vector2d 的子类，只用于覆盖 typecode 类属性。

❷ 为了演示，创建一个 ShortVector2d 实例，即 sv。

❸ 查看 sv 的 repr 表示形式。

❹ 确认得到的字节序列长度为 9 字节，而不是之前的 17 字节。

这也说明了我在 Vecto2d.__repr__ 方法中为什么没有硬编码 class_name 的值，而是 使用 type(self).__name__ 获取，如下所示：

\# 在 Vector2d 类中定义

def __repr__(self):

class_name = type(self).__name__

return '{}({!r}, {!r})'.format(class_name, *self)

如果硬编码class_name的值，那么Vector2d的子类(如ShortVector2d)要覆盖 __repr__ 方法，只是为了修改 class_name 的值。从实例的类型中读取类 名， __repr__ 方法就可以放心继承。

至此，我们通过一个简单的类说明了如何利用数据模型处理 Python 的其他功能：提供不

同的对象表示形式、实现自定义的格式代码、公开只读属性，以及通过 hash() 函数支持 集合和映射。



### 9.10 本章小结

本章的目的是说明，如何使用特殊方法和约定的结构，定义行为良好且符合 Python 风格

的类。

vector2d_v3.py （示例 9-9）比 vector2d_v0.py （示例 9-2）更符合 Python 风格吗？ vector2d_v3.py 中的 Vector2d 类用到的 Python 功能肯定要多，但是 Vector2d 类的第一 版和最后一版相比哪个更符合风格，要看使用的上下文。Tim Peter写的“Python之禅”说

道：

简洁胜于复杂。

符合 Python 风格的对象应该正好符合所需，而不是堆砌语言特性。

我不断改写 Vector2d 类是为了提供上下文，以便讨论 Python 的特殊方法和编程约定。

回看表 1-1，你会发现本章的几个代码清单说明了下述特殊方法。

•所有用于获取字符串和字节序列表示形式的方

法：__repr__、__str__、__format__ 和 __bytes__。

•把对象转换成数字的几个方法：__abs__、__bool__和__hash__。

•用于测试字节序列转换和支持散列（连同_hash_方法）的_eq_运算符。

为了转换成字节序列，我们还实现了一个备选构造方法，即 Vector2d.frombytes（）， 顺便又讨论了 @classmethod （十分有用）和@staticmethod （不太有用，使用模块层 函数更简单）两个装饰器。 frombytes 方法的实现方式借鉴了 array.array 类中的同名 方法。

我们了解到，格式规范微语言（[https://docs.python+org/3/library/string+html#formatspec](https://docs.python.org/3/library/string.html%23formatspec)）是 可扩展的，方法是实现 __format__ 方法，对提供给内置函数 format（obj, format_spec）的 format_spec，或者提供给 str.format 方法的 '{:«format_spec»}' 位于代换字段中的 «format_spec» 做简单的解析。

为了把 Vector2d 实例变成可散列的，我们先让它们不可变，至少要把 x 和 y 设为私有属 性，再以只读特性公开，以防意夕修改它们。随后，我们实现了 __hash__ 方法，使用推 荐的异或运算符计算实例属性的散列值。

接着，我们讨论了如何使用 __slots__ 属性节省内存，以及这么做要注意的问 题。 __slots__ 属性有点棘手，因此仅当处理特别多的实例（数百万个，而不是几千 个）时才建议使用。

最后，我们说明了如何通过访问实例属性（如self.typecode）覆盖类属性。我们先创 建一个实例属性，然后创建子类，在类中覆盖类属性。

本章多次提到，我编写代码的方式是为了举例说明如何编写标准Python对象的API。如果

用一句话总结本章的内容，那就是：

要构建符合 Python 风格的对象，就要观察真正的 Python 对象的行为。

——古老的中国谚语

### 9.11 延伸阅读

本章介绍了数据模型的几个特殊方法，因此主要参考资料与第 1 章一样，阅读那些资料能 对这个话题有个整体了解。方便起见，我再次给出之前推荐的四个资料，同时再多加几

个。

Python语言参考手册中的“Data Model”一章

(<https://docs.python.org/3/reference/datamodel.html>)

本章用到的方法大部分见于“3.3.1. Basic

customization”([https://docs.python.org/3/reference/datamodel.html#basic-customization](https://docs.python.org/3/reference/datamodel.html%23basic-customization)) 。

《Python 技术手册(第 2 版)》， Alex Martelli 著

虽然这本书只涵盖 Python 2.5(第 2 版)，但是对数据模型做了深入说明。基本的概 念都是一样的，而且自 Python 2.2 起(这一版的内置类型和用户定义的类兼容性变得更 好)，数据模型的大多数 API 完全没变。

《Python Cookbook (第 3 版)中文版》，David Beazley 和 Brian K. Jones 著

通过诀窍来演示现代化的编程实践。尤其是第 8 章“类与对象”，其中有好几个方案与 本章讨论的话题有关。

《Python 参考手册(第 4 版)》， David Beazley 著

详细说明了 Python 2.6 和 Python 3 的数据模型。

本章涵盖了与对象表示形式有关的全部特殊方法，唯有 __index__ 除外。这个方法的作 用是强制把对象转换成整数索引，在特定的序列切片场景中使用，以及满足 NumPy 的一

个需求。在实际编程中，你我都不用实现 __index__ 方法，除非决定新建一种数值类

型，并想把它作为参数传给 __getitem__ 方法。如果好奇的话，可以阅读 A.M.Kuchling 写的 “What's New in Python 2.5”( <https://docs.python.org/2.5/whatsnew/pep-357.html>) ，这篇

文章做了简要说明；此外，还可以阅读“PEP 357—Allowing Any Object to be Used for

Slicing” (<https://www.python.org/dev/peps/pep-0357/>) ，这份 PEP 从 C 语言扩展的实现者 和 NumPy 的作者 Travi s Oliphant 的角度详述了对 __index__ 方法的需求。

意识到应该区分字符串表示形式的早期语言是 Smalltalk。 1996 年， Bobby Woolf 写了一篇 题为“How to Display an Object as a String: printString and displayString” 的文章 ( <http://esug.org/data/HistoricalDocuments/TheSmalltalkReport/ST07/04wo.pdf>) ，他在这篇 文章中讨论了 Smalltalk 对 printString 和 displayString 方法的实现。在 9.1 节说明 repr() 和 str() 的作用时，我从这篇文章中借用了言简意赅的表述，即“便于开发者理 解的方式”和“便于用户理解的方式”。

杂谈

特性有助于减少前期投入

在 Vector2d 类的第一版中， x 和 y 属性是公开的；默认情况下， Python 的所有实例 属性和类属性都是公开的。这对向量来说是合理的，因为我们要能访问分量。虽然这

些向量是可迭代的对象，而且可以拆包成一对变量，但是还要能够通过

my_vector.x 和 my_vector.y 获取各个分量。

如果觉得应该避免意外更新 x 和 y 属性，可以实现特性，但是代码的其他部分没有

变化， Vector2d 的公开接口也不受影响，这一点从 doctest 中可以得知。我们依然能 够访问 my_vector.x 和 my_vector.y。

这表明我们可以先以最简单的方式定义类，也就是使用公开属性，因为如果以后需要

对读值方法和设值方法增加控制，那就可以实现特性，这样做对一开始通过公开属性

的名称（如X和y）与对象交互的代码没有影响。

Java语言采用的方式则截然相反：Java程序员不能先定义简单的公开属性，然后在 需要时再实现特性，因为 Java 语言没有特性。因此，在 Java 中编写读值方法和设值 方法是常态，就算这些方法没做什么有用的事情也得这么做，因为 API 不能从简单 的公开属性变成读值方法和设值方法，同时又不影响使用那些属性的代码。

此外，本书的技术审校 Alex Martelli 指出，到处都使用读值方法和设值方法是愚蠢的

行为。如果想编写下面的代码：

\>>> my_object.set_foo(my_object.get_foo() + 1)

这样做就行了：

\>>> my_object.foo += 1

维基的发明人和极限编程先驱 Ward Cunningham 建议问这个问题： “做这件事最简单 的方法是什么？”意即，我们应该把焦点放在目标上。 11 提前实现设值方法和读值方 法偏离了目标。在 Python 中，我们可以先使用公开属性，然后等需要时再变成特 性。

私有属性的安全性和保障性

Perl 不会强制你保护隐私。你应该待在客厅外，因为你没收到邀请，而不是因为

里面有把枪。

——Larry Wall Perl 之父

Python 和 Perl 在很多方面的做法是截然相反的，但是 Larry 和 Guido 似乎都同意要保

护对象的隐私。

这些年我教过许多Java程序员学习Python，我发现很多人都对Java提供的隐私保障 推崇备至。可事实是， Java 的 private 和 protected 修饰符往往只是为了防止意夕

（即一种安全措施）。只有使用安全管理器部署应用时才能保障绝对安全，防止恶意

访问；但是，实际上很少有人这么做，即便在企业中也少见。

下面通过一个 Java 类证明这一点（见示例 9-15）。

示例9-15 Confidential .java： 一个Java类，定义了一个私有字段，名为secret

public class Confidential { private String secret = "";

public Confidential(String text) {

secret = text.toUpperCase();

}

}

在示例 9-15 中，我把 text 转换成大写后存入 secret 字段。转换成大写是为了表明 secret 字段中的值全部是大写的。

我们要使用Jython运行expose.py脚本才能真正说明问题。那个脚本使用内省（Java 称之为“反射”）获取私有字段的值。 expose.py 脚本的代码在示例 9-16 中。

示例9-16 expose.py： 一段Jython代码，从另一个类中读取一个私有字段

import Confidential

message = Confidential('top secret text') secret_field = Confidential.getDeclaredField('secret')

secret_field.setAccessible(True) # 攻破防线

print 'message.secret =', secret_field.get(message)

运行示例 9-16 得到的结果如下：

$ jython expose.py message.secret = TOP SECRET TEXT

字符串 'TOP SECRET TEXT' 从 Confidential 类的私有字段 secret 中读取。

这里没有什么黑魔法： expose.py 脚本使用 Java 反射 API 获取私有字段 'secret' 的

引用，然后调用 'secret_field.setAccessible（True）' 把它设为可读的。显

然，使用 Java 代码也能做到这一点（不过所需的代码行数是这里的三倍多，参见本 书代码仓库里的 Expose.java 文件， <https://github.com/fluentpython/example-code>） 。

如果这个 Jython 脚本或 Java 主程序( 如 EXpose.class) 在

SecurityManager ( [http://docs.oracle.com/javase/tutorial/essential/environment/security.html ](http://docs.oracle.com/javase/tutorial/essential/environment/security.html)) 的监管下运行， .setAccessible(True) 这个关键的调用就会失败。但是现实中， 很少有人部署 Java 应用时会使用 SecurityManager， Java applet 除外(还记得这个

吗？)。

我的观点是， Java 中的访问控制修饰符基本上也是安全措施，不能保证万无一失 ——至少实践中是如此。因此，安心享用 Python 提供的强大功能吧，放心去用吧！

11 参见“Simplest Thing that Could Possibly Work: A Conversation with Ward Cunningham, Part

V”(<http://www.artima.com/intv/simplest3.html>)。
