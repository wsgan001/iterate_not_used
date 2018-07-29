## 第 15 章 上下文管理器和 else 块

最终，上下文管理器可能几乎与子程序(subroutine)本身一样重要。目前，我们只 了解了上下文管理器的皮毛+ + + + + +Basic语言有with语句，而且很多语言都有。但

是，在各种语言中 with 语句的作用不同，而且做的都是简单的事，虽然可以避免不 断使用点号查找属性，但是不会做事前准备和事后清理。不要觉得名字一样，就意味

着作用也一样。 with 语句是非常了不起的特性。 1

—Raymond Hettinger 雄辩的 Python 布道者

I1 节选自 PyCon US 2013 主题演讲tWhat Makes Python Awesome” (<http://pyvideo.org/video/1669/keynote-3>);关于 with 的 部分从23:00开始，到26:15结束。

本章讨论其他语言中不常见的一些流程控制特性，正因如此， Python 用户往往会忽视或没 有充分使用这些特性。下面要讨论的特性有：

• with语句和上下文管理器

• for、while和try语句的else子句

with 语句会设置一个临时的上下文，交给上下文管理器对象控制，并且负责清理上下

文。这么做能避免错误并减少样板代码，因此 API 更安全，而且更易于使用。除了自动

关闭文件之外， with 块还有很多用途。

else 子句与 with 语句完全没有关系。可是己经写到第五部分了，我找不到其他地方介 绍else，又不能单写只有一页内容的一章，因此就在这一章讨论了。

下面从这个较小的话题开始，进入本章的实质内容。

### 15.1先做这个，再做那个：f语句之外的else块

这个语言特性不是什么秘密，但却没有得到重视： else 子句不仅能在 if 语句中使用， 还能在 for、while 和 try 语句中使用。

for/else、while/else 和 try/else 的语义关系紧密，不过与 if/else 差别很大。起

初， else 这个单词的意思阻碍了我对这些特性的理解，但是最终我习惯了。

else 子句的行为如下。

for

仅当 for 循环运行完毕时（即 for 循环没有被 break 语句中止）才运行 else 块。

while

仅当 while 循环因为条件为假值而退出时（即 while 循环没有被 break 语句中止）

才运行 else 块。

try

仅当 try 块中没有异常抛出时才运行 else 块。官方文档 （[https://docs+python+org/3/reference/compound_stmts+html](https://docs.python.org/3/reference/compound_stmts.html)）还指出：“else 子句抛出的异常

不会由前面的 except 子句处理。 ”

在所有情况下，如果异常或者 return、break 或 continue 语句导致控制权跳到了复合 语句的主块之外， else 子句也会被跳过。

我觉得除了 if语句之外，其他语句选择使用else关键字是个错误。else蕴

含着“排他性”这层意思，例如“要么运行这个循环，要么做那件事”。可是，在循环

中， else 的语义恰好相反： “运行这个循环，然后做那件事。 ”因此，使用 then 关 键字更好。 then 在 try 语句的上下文中也说得通： “尝试运行这个，然后做那件 事。 ”可是，添加新关键字属于语言的重大变化，而 Guido 唯恐避之不及。

在这些语句中使用 else 子句通常能让代码更易于阅读，而且能省去一些麻烦，不用设置 控制标志或者添加额外的 if 语句。

在循环中使用 else 子句的方式如下述代码片段所示：

for item in my_list:

if item.flavor == 'banana': break

else:

raise ValueError('No banana flavor found!')

一开始，你可能觉得没必要在 try/except 块中使用 else 子句。毕竟，在下述代码片段 中，只有 dangerous_call() 不抛出异常， after_call() 才会执行，对吧？

try:

dangerous_call()

after_call()

except OSError:

log('OSError...')

然而， after_call() 不应该放在 try 块中。为了清晰和准确， try 块中应该只抛出预期 异常的语句。因此，像下面这样写更好：

try:

dangerous_call()

except OSError:

log('OSError...')

else:

after_call()

现在很明确， try 块防守的是 dangerous_call() 可能出现的错误，而不是

after_call()。而且很明显，只有try块不抛出异常，才会执行after_call()。

在 Python 中， try/except 不仅用于处理错误，还常用于控制流程。为此， Python 官方词 汇表([https://docs+python+org/3/glossary+html#term-eafp](https://docs.python.org/3/glossary.html%23term-eafp))还定义了一个缩略词(口号)。

EAFP

取得原谅比获得许可容易( easier to ask for forgiveness than permission) 。这是一 种常见的 Python 编程风格，先假定存在有效的键或属性，如果假定不成立，那么捕 获异常。这种风格简单明快，特点是代码中有很多 try 和 except 语句。与其他很多 语言一样(如 C 语言)，这种风格的对立面是 LBYL 风格。

接下来，词汇表定义了 LBYL。

LBYL

三思而后行(look before you leap)。这种编程风格在调用函数或查找属性或键 之前显式测试前提条件。与 EAFP 风格相反，这种风格的特点是代码中有很多 if 语 句。在多线程环境中， LBYL 风格可能会在“检查”和“行事”的空当引入条件竞争。例 如，对 if key in mapping: return mapping[key] 这段代码来说，如果在测试 之后，但在查找之前，另一个线程从映射中删除了那个键，那么这段代码就会失败。 这个问题可以使用锁或者 EAFP 风格解决。

如果选择使用 EAFP 风格，那就要更深入地了解 else 子句，并在 try/except 语句中合 理使用。

下面探讨本章的主要话题：强大的 with 语句。

### 15.2上下文管理器和with块

上下文管理器对象存在的目的是管理 with 语句，就像迭代器的存在是为了管理 for 语句 一样。

with 语句的目的是简化 try/finally 模式。这种模式用于保证一段代码运行完毕后执行 某项操作，即便那段代码由于异常、 return 语句或 sys.exit() 调用而中止，也会执行 指定的操作。 finally 子句中的代码通常用于释放重要的资源，或者还原临时变更的状

态。

上下文管理器协议包含 __enter__ 和 __exit__ 两个方法。 with 语句开始运行时，会在

上下文管理器对象上调用 __enter__ 方法。 with 语句运行结束后，会在上下文管理器对

象上调用 __exit__ 方法，以此扮演 finally 子句的角色。

最常见的例子是确保关闭文件对象。使用 with 语句关闭文件的详细说明参见示例 15-1。 示例 15-1 演示把文件对象当成上下文管理器使用

\>>> with open('mirror.py') as fp: # O … src = fp.read(60) # ©

\>>> len(src)

60

\>>> fp # ©

<_io.TextIOWrapper name='mirror.py' mode='r' encoding='UTF-8'> >>> fp.closed, fp.encoding # ©

(True, 'UTF-8')

\>>> fp.read(60) # ©

Traceback (most recent call last):

File "<stdin>", line 1, in <module>

ValueError: I/O operation on closed file.

❶ fp 绑定到打开的文件上，因为文件的 __enter__ 方法返回 self。

❷ 从 fp 中读取一些数据。

❸ fp 变量仍然可用。 2 2与函数和模块不同，with块没有定义新的作用域。

❹ 可以读取 fp 对象的属性。

❺ 但是不能在 fp 上执行 I/O 操作，因为在 with 块的末尾，调用

TextIOWrapper.__exit__ 方法把文件关闭了。

示例15-1中标注❶的那行代码道出了不易察觉但很重要的一点：执行with后面的表达 式得到的结果是上下文管理器对象，不过，把值绑定到目标变量上(as子句)是在上下

文管理器对象上调用 __enter__ 方法的结果。

碰巧，示例 15-1 中的 open() 函数返回 TextIOWrapper 类的实例，而该实例的 __enter__方法返回self。不过，__enter__方法除了返回上下文管理器之外，还可能

返回其他对象。

不管控制流程以哪种方式退出 with 块，都会在上下文管理器对象上调用 __exit__ 方 法，而不是在 __enter__ 方法返回的对象上调用。

with 语句的 as 子句是可选的。对 open 函数来说，必须加上 as 子句，以便获取文件的 引用。不过，有些上下文管理器会返回None，因为没什么有用的对象能提供给用户。

示例 15-2 使用一个精心制作的上下文管理器执行操作，以此强调上下文管理器与 __enter__ 方法返回的对象之间的区别。

示例 15-2 测试 LookingGlass 上下文管理器类

\>>> from mirror import LookingGlass

\>>> with LookingGlass() as what: O

...    print('Alice, Kitty and Snowdrop') ©

...    print(what)

pordwonS dna yttiK ,ecilA ©

YKCOWREBBAJ

\>>> what &

'JABBERWOCKY'

\>>> print('Back to normal.') ❺

Back to normal.

❶上下文管理器是LookingGlass类的实例；Python在上下文管理器上调用__enter__

方法，把返回结果绑定到 what 上。

❷ 打印一个字符串，然后打印 what 变量的值。

❸ 打印出的内容是反向的。

❹ 现在， with 块已经执行完毕。可以看出， __enter__ 方法返回的值——即存储在 what 变量中的值——是字符串 'JABBERWOCKY'。

❺ 输出不再是反向的了。

示例 15-3 是 LookingGlass 类的实现。

示例 15-3 mirror+py： LookingGlass 上下文管理器类的代码

return 'JABBERWOCKY' ©

def reverse_write(self, text):❺ self.original_write(text[::-1])

def __exit__(self, exc_type, exc_value, traceback): © import sys &

sys.stdout.write = self.original_write ❻ if exc_type is ZeroDivisionError: ©

print('Please DO NOT divide by zero!') return True ©

❶ 除了 self 之外， Python 调用 __enter__ 方法时不传入其他参数。

❷ 把原来的 sys.stdout.write 方法保存在一个实例属性中，供后面使用。

❸ 为 sys.stdout.write 打猴子补丁，替换成自己编写的方法。

❹ 返回 'JABBERWOCKY' 字符串，这样才有内容存入目标变量 what。

❺ 这是用于取代 sys.stdout.write 的方法，把 text 参数的内容反转，然后调用原来

的实现。

❻如果一切正常，Python调用__exit__方法时传入的参数是None, None, None;如

果抛出了异常，这三个参数是异常数据，如下所述。

❼ 重复导入模块不会消耗很多资源，因为 Python 会缓存导入的模块。

❽ 还原成原来的 sys.stdout.write 方法。

❾ 如果有异常，而且是 ZeroDivisionError 类型，打印一个消息++

❿+ + + + + +然后返回True，告诉解释器，异常己经处理了。

®如果__exit__方法返回None，或者True之外的值，with块中的任何异常都会向上

冒泡。

在实际使用中，如果应用程序接管了标准输出，可能会暂时把sys.stdout换

成类似文件的其他对象，然后再切换成原来的版

本。 contextlib.redirect_stdout 上下文管理器 ([https://docs+python+org/3/library/contextlib+html#contextlib+redirect_stdout](https://docs.python.org/3/library/contextlib.html%23contextlib.redirect_stdout))就是这么做 的：只需传入类似文件的对象，用于替代 sys.stdout。

解释器调用 __enter__ 方法时，除了隐式的 self 之外，不会传入任何参数。传给 __exit__ 方法的三个参数列举如下。

exc_type

异常类(例如 ZeroDivisionError)。

exc_value

异常实例。有时会有参数传给异常构造方法，例如错误消息，这些参数可以使用 exc_value.args 获取。

traceback

traceback 对象。 3

3在 try/finally 语句的 finally 块中调用 sys.exc_info()([https://docs.python.org/3/library/sys.html#sys.exc_info](https://docs.python.org/3/library/sys.html%23sys.exc_info))，

得到的就是 __exit__ 接收的这三个参数。鉴于 with 语句是为了取代大多数 try/finally 语句，而且通常需要调用 sys.exc_info() 来判断做什么清理操作，这种行为是合理的。

上下文管理器的具体工作方式参见示例 15-4。在这个示例中，我们在 with 块之外使用 LookingGlass 类，因此可以手动调用 __enter__ 和 __exit__ 方法。

示例 15-4 在 with 块之外使用 LookingGlass 类

\>>> from mirror import LookingGlass >>> manager = LookingGlass() O >>> manager

<mirror.LookingGlass object at 0x2a578ac> >>> monster = manager.__enter__() ©

\>>> monster == 'JABBERWOCKY' ©

eurT

\>>> monster

'YKCOWREBBAJ'

\>>> manager

\>ca875a2x0 ta tcejbo ssalGgnikooL.rorrim< >>> manager.__exit__(None, None, None) © >>> monster

'JABBERWOCKY'

❶ 实例化并审查 manager 实例。

❷ 在上下文管理器上调用 __enter__() 方法，把结果存储在 monster 中。

❸monster的值是字符串JABBERWOCKY'。打印出的True标识符是反向的，因为 stdout 的所有输出都经过 __enter__ 方法中打补丁的 write 方法处理。

❹ 调用 manager.__exit__，还原成之前的 stdout.write。

上下文管理器是相当新颖的特性， Python 社区肯定还在不断寻找新的创意用法。标准库中 有一些示例。

•在 sqlite3 模块中用于管理事务，参见“12+6+7+3+ Using the connection as a context [manager”](https://docs.python.org/3/library/sqlite3.html%23using-the-connection-as-a-context-manager)[( https://docs.python.org/3/library/sqlite3.html#using-the-connection-as-a-context-](https://docs.python.org/3/library/sqlite3.html%23using-the-connection-as-a-context-manager)manager)。 4

•在threading模块中用于维护锁、条件和信号，参见“17+1 + 10+ Using locks, conditions, and semaphores in the with

[statement”](https://docs.python.org/3/library/threading.html%23using-locks-conditions-and-semaphores-in-the-with-statement)[(https://docs+python+org/3/library/threading+html#using-locks-conditions-and-](https://docs.python.org/3/library/threading.html%23using-locks-conditions-and-semaphores-in-the-with-statement)

semaphores-in-the-with-statement)。

•为Decimal对象的算术运算设置环境，参见decimal.localcontext函数的文档 ([https://docs+python+org/3/library/decimal+html#decimal+localcontext](https://docs.python.org/3/library/decimal.html%23decimal.localcontext))。

•为了测试临时给对象打补丁，参见unittest.mock.patch函数的文档 ([https://docs+python+ org/3/library/unittest+mock+html#patch](https://docs.python.org/3/library/unittest.mock.html%23patch))。

| 4在Python 3+5文档中是“12+6+8+3”。——编者注

标准库中还有个 contextlib 模块，提供一些实用工具，参见下一节。

### 15.3 contextlib模块中的实用工具

自己定义上下文管理器类之前，先看一下 Python 标准库文档中的“29+6 contextlib — Utilities for with-statement contexts” ([ ](https://docs.python.org/3/library/contextlib.html)[http s://docs+python+ org/ 3 /library/contextlib+html](https://docs.python.org/3/library/contextlib.html))。除了 前面提到的 redirect_stdout 函数， contextlib 模块中还有一些类和其他函数，使用 范围更广。

closing

如果对象提供了 close() 方法，但没有实现 __enter__/__exit__ 协议，那么可以 使用这个函数构建上下文管理器。

suppress

构建临时忽略指定异常的上下文管理器。

@contextmanager

这个装饰器把简单的生成器函数变成上下文管理器，这样就不用创建类去实现管理器

协议了。

ContextDecorator

这是个基类，用于定义基于类的上下文管理器。这种上下文管理器也能用于装饰函

数，在受管理的上下文中运行整个函数。

ExitStack

这个上下文管理器能进入多个上下文管理器。 with 块结束时， ExitStack 按照后进 先出的顺序调用栈中各个上下文管理器的 __exit__ 方法。如果事先不知道 with 块要进 入多少个上下文管理器，可以使用这个类。例如，同时打开任意一个文件列表中的所有文

件。

显然，在这些实用工具中，使用最广泛的是 @contextmanager 装饰器，因此要格外留

心。这个装饰器也有迷惑人的一面，因为它与迭代无关，却要使用 yield 语句。由此可 以引出协程，这是下一章的主题。

### 15.4 使用 @contextmanager

@contextmanager 装饰器能减少创建上下文管理器的样板代码量，因为不用编写一个完 整的类，定义 __enter__ 和 __exit__ 方法，而只需实现有一个 yield 语句的生成器， 生成想让 __enter__ 方法返回的值。

在使用 @contextmanager 装饰的生成器中， yield 语句的作用是把函数的定义体分成两 部分： yield 语句前面的所有代码在 with 块开始时（即解释器调用 __enter__ 方法 时）执行， yield 语句后面的代码在 with 块结束时（即调用 __exit__ 方法时）执行。

下面举个例子。示例 15-5 使用一个生成器函数代替示例 15-3 中定义的 LookingGlass 类。

示例15-5 mirror_gen.py：使用生成器实现的上下文管理器

import contextlib

@contextlib.contextmanager O def looking_glass(): import sys

original_write = sys.stdout.write ©

def reverse_write(text): © original_write(text[::-1])

sys.stdout.write = reverse_write ©

yield 'JABBERWOCKY' ©

sys.stdout.write = original_write ©

❶ 应用 contextmanager 装饰器。

❷ 贮存原来的 sys.stdout.write 方法。

❸ 定义自定义的 reverse_write 函数；在闭包中可以访问 original_write。

❹ 把 sys.stdout.write 替换成 reverse_write。

❺ 产出一个值，这个值会绑定到 with 语句中 as 子句的目标变量上。执行 with 块中的

代码时，这个函数会在这一点暂停。

❻ 控制权一旦跳出 with 块，继续执行 yield 语句之后的代码；这里是恢复成原来的 sys. stdout.write 方法。

示例 15-6 是使用 looking_glass 函数的例子。

示例 15-6 测试 looking_glass 上下文管理器函数

\>>> from mirror_gen import looking_glass >>> with looking_glass() as what: O ...    print('Alice, Kitty and Snowdrop')

...    print(what)

pordwonS dna yttiK ,ecilA

YKCOWREBBAJ

\>>> what

'JABBERWOCKY'

O与示例15-2唯一的区别是上下文管理器的名字：LookingGlass变成了

looking_glass。

其实， contextlib.contextmanager 装饰器会把函数包装成实现 __enter__ 和 __exit__ 方法的类。 5

I 5类的名称是_GeneratorContextManager。如果想了解具体的工作方式，可以阅读Python 3+4发行版中 Lib/contextlib+py 文件里的源码([https://hg+python+ org/cpython/file/3+4/Lib/c ontextlib+py#l34](https://hg.python.org/cpython/file/3.4/Lib/contextlib.py%23l34) )。

这个类的 __enter__ 方法有如下作用。

(1) 调用生成器函数，保存生成器对象(这里把它称为 gen)。

(2)调用next(gen)，执行到yield关键字所在的位置。

(3) 返回 next(gen) 产出的值，以便把产出的值绑定到 with/as 语句中的目标变量上。 with 块终止时， __exit__ 方法会做以下几件事。

⑴检查有没有把异常传给exc_type;如果有，调用gen.throw(exception)，在生成 器函数定义体中包含 yield 关键字的那一行抛出异常。

(2)否则，调用next(gen)，继续执行生成器函数定义体中yield语句之后的代码。

示例 15-5 有一个严重的错误：如果在 with 块中抛出了异常， Python 解释器会将其捕获， 然后在 looking_glass 函数的 yield 表达式里再次抛出。但是，那里没有处理错误的代 码，因此 looking_glass 函数会中止，永远无法恢复成原来的 sys.stdout.write 方

法，导致系统处于无效状态。

示例 15-7 添加了一些代码，特别用于处理 ZeroDivisionError 异常；这样，在功能上 它就与示例 15-3 中基于类的实现等效了。

示例15-7 mirror_gen_exc+py:基于生成器的上下文管理器，而且实现了异常处理 ——从外部看，行为与示例 15-3 一样

import sys

original_write = sys.stdout.write

def reverse_write(text): original_write(text[::-1])

sys.stdout.write = reverse_write

msg = '' O    _

try:

yield 'JABBERWOCKY'

except ZeroDivisionError: &

msg = 'Please DO NOT divide by zero!'

finally:

sys.stdout.write = original_write © if msg:

print(msg) ©

import csv

with inplace(csvfilename, 'r', newline='') as (infh, outfh): reader = csv.reader(infh) writer = csv.writer(outfh)

for row in reader:

row += ['new', 'columns']

writer.writerow(row)

inplace 函数是个上下文管理器，为同一个文件提供了两个句柄（这个示例中的 infh 和 outfh），以便同时读写同一个文件。这比标准库中的fileinput.input函数

（[https://docs+pyhon+org/3/library/fileinput+html#filei](https://docs.python.org/3/library/fileinput.html%23fileinput.input)[叩ut+i叩ut](https://docs.python.org/3/library/fileinput.html%23fileinput.input);顺便说一下，这个函数也提

供了一个上下文管理器）易于使用。

如果想学习 Martijn 实现 inplace 的源码（列在这篇文章

中：[http://www+zopatista+com/python/2013/11/26/inplace-file-rewriting/](http://www.zopatista.com/python/2013/11/26/inplace-file-rewriting/)），找至ij yield 关键

字，在此之前的所有代码都用于设置上下文：先创建备份文件，然后打开并产出

__enter__ 方法返回的可读和可写文件句柄的引用。 yield 关键字之后的 __exit__ 处

理过程把文件句柄关闭；如果什么地方出错了，那么从备份中恢复文件。

注意，在 @contextmanager 装饰器装饰的生成器中， yield 与迭代没有任何关系。在本

节所举的示例中，生成器函数的作用更像是协程：执行至某一点时暂停，让客户代码运 行，直至客户让协程继续做事。第 16 章会全面讨论协程。

### 15.5 本章小结

本章从简单的话题入手，先讨论了 for、while 和 try 语句的 else 子句。当你习惯 else 子句在这些语句中的奇怪意思之后，我相信 else 能阐明你的意图。

然后，本章讨论了上下文管理器和 with 语句的作用。很快我们就知道，除了自动关闭打 开的文件之外， with 语句还有很多用途。我们自己动手实现了一个上下文管理器——含 有 __enter__/__exit__ 方法的 LookingGlass 类，说明了如何在 __exit__ 方法中处 理异常。 Raymond Hettinger 在 PyCon US 2013 上所做的主题演讲传达了一个重要的观 点： with 不仅能管理资源，还能用于去掉常规的设置和清理代码，或者在另一个过程前 后执行的操作(“What Makes Python Awesome?”，第21张幻灯

[片， ](https://speakerdeck.com/pyconslides/pycon-keynote-python-is-awesome-by-raymond-hettinger?slide=21)[https : //speakerdeck+com/pyconslides/pycon-keynote-python-i s-awesome-by-raymond-](https://speakerdeck.com/pyconslides/pycon-keynote-python-is-awesome-by-raymond-hettinger?slide=21)hettinger?slide=21) 。

最后，我们分析了标准库中 contextlib 模块里的函数。其中， @contextmanager 装饰 器能把包含一个 yield 语句的简单生成器变成上下文管理器——这比定义一个至少包含 两个方法的类要更简洁。我们使用 looking_glass 生成器函数实现了 LookingGlass 类，还讨论了使用 @contextmanager 时如何处理异常。

@contextmanager 装饰器优雅且实用，把三个不同的 Python 特性结合到了一起：函数装 饰器、生成器和 with 语句。

### 15.6 延伸阅读

Python 语言参考手册中的“8+ Compound statements” 一章 ([https://docs+python+org/3/reference/compound_stmts+html](https://docs.python.org/3/reference/compound_stmts.html))全面说明了 if、for、while 和

try 语句的 else 子句。关于 try/except 语句(有 else 子句，或者没有)是否符合 Python 风格，Raymond Hettinger 在 Stack Overflow 中对“Is it a good practice to use try-except-else in Python?”这一问题(http://stackoverflow+com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python)做了精影的回答。在[ ](http://stackoverflow.com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python)[Alex Martelli 写的《Python 技术手册](http://stackoverflow.com/questions/16138232/is-it-a-good-practice-to-use-try-except-else-in-python)

(第 2 版)》一书中，有一章是关于异常的，那一章极好地讨论了 EAFP 风格。 Alex 认 为“取得原谅比获得许可容易”是由计算领域的先驱 Grace Hopper 首先提出的。

在Python标准库文档中，“4+ Built-in Types”一章中有一节专门说明了上下文管理器的类型 ([https://docs+python+org/3/library/stdtypes+html#typecontextmanager](https://docs.python.org/3/library/stdtypes.html%23typecontextmanager)) 。 Python 语言参考手册

中还有 __enter__/__exit__ 两个特殊方法的文档，在“3+3+8+ With Statement Context [Managers”](https://docs.python.org/3/reference/datamodel.html%23with-statement-context-managers)[一节中(https://docs+python+org/3/reference/datamodel+html#with-statement-context-](https://docs.python.org/3/reference/datamodel.html%23with-statement-context-managers)managers)。上下文管理器在“PEP 343—

The‘with'Statement” ([https://www+python+org/dev/peps/pep-0343/](https://www.python.org/dev/peps/pep-0343/))中引入。这份 PEP 不易读

懂，因为大量篇幅都在讲极端情况，以及反对其他提案。这就是 PEP 的特点。

在 PyCon US 2013 的主题演讲中， Raymond Hettinger 强调， with 语句是“这门语言的一项 迷人特性”。在这次大会上的“Transforming Code into Beautiful, Idiomatic Pyhon”演讲中

(https://speakerdeck+com/pyconslides/transforming-code-into-beautiful-idiomatic-python-by-raymond-hettinger-1?slide=34[) ，他还展示了上下文管理器的几个有趣应用。](https://speakerdeck.com/pyconslides/transforming-code-into-beautiful-idiomatic-python-by-raymond-hettinger-1?slide=34)

Jeff Preshing 写的一篇博客文章很有趣，题为“The Python with Statement by

Example” ([http://preshing+com/20110920/the-python-with-statement-by-example/](http://preshing.com/20110920/the-python-with-statement-by-example/)) ，他举例说 明了 pycairo 图形库中的上下文管理器。

Beazley与Jones在他们的《Python Cookbook (第3版)中文版》一书中，发明了上下文 管理器的独特用途。 “8+3 让对象支持上下文管理协议”一节实现了一个 LazyConnection 类，它的实例是上下文管理器，在 with 块中能自动打开和关闭网络连接。 “9+22 以简单 的方式定义上下文管理器”一节编写了一个用于统计代码运行时间的上下文管理器，还编 写了一个使用事务修改 list 对象的上下文管理器：在 with 块中创建 list 实例的副 本，所有改动都针对那个副本；仅当 with 块没有抛出异常，正常执行完毕之后，才用副 本替代原来的列表。这样做简单又巧妙。

杂谈

取出面包

在 PyCon US 2013 的主题演讲“What Makes Python Awesome” 中 ([http://pyvideo+org/video/1669/keynote-3](http://pyvideo.org/video/1669/keynote-3)) ， Raymond Hettinger 说他第一次看至 with

语句的提案时，觉得“有点晦涩难懂”。这和我一开始的反应类似。 PEP 通常难以阅

读， PEP 343 尤其如此。

然后， Hettinger 告诉我们，他认识到在计算机语言的发展历程中，子程序是最重要的 发明。如果有一系列操作，如A-B-C和P-B-Q，那么可以把B拿出来，变成子程

序。这就好比把三明治的馅儿取出来，这样我们就能使用金枪鱼搭配不同的面包。可

是，如果我们想把面包取出来，使用小麦面包夹不同的馅儿呢？这就是 with 语句实 现的功能。 with 语句是子程序的补充。 Hettinger 接着说道：

with 语句是非常了不起的特性。我建议你在实践中深挖这个特性的用途。使用 with 语句或许能做意义深远的事情。 with 语句最好的用法还未被发掘出来。我 预料，如果有好的用法，其他语言以及未来的语言会借鉴这个特性。或许，你正 在参与的事情几乎与子程序的发明一样意义深远。

Hettinger 承认，他夸大了 with 语句的作用。尽管如此， with 语句仍是一个十分有 用的特性。他用三明治类比，道出 with 语句是子程序的补充；那一刻，我的脑海中

浮现了许多可能性。

如果你想让任何人信服 Python 是出色的语言，一定要观看 Hettinger 的主题演讲。关 于上下文管理器的部分从 23:00 开始，到 26:15 结束。不过，整个主题演讲都很精 彩。
