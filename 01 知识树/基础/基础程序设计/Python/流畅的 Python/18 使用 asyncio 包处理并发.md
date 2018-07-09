## 第 18 章 使用 asyncio 包处理并发

并发是指一次处理多件事。

并行是指一次做多件事。

二者不同，但是有联系。

一个关于结构，一个关于执行。

并发用于制定方案，用来解决可能(但未必)并行的问题。 1

——Rob Pike Go 语言的创造者之一

I1摘自“Concurrency Is Not Parallelism (It's Better)” ([http://concur.rspace.googlecode.eom/hg/talk/concur.html#slide-5](http://concur.rspace.googlecode.com/hg/talk/concur.html%23slide-5))演讲的

第5张幻灯片。

Imre Simon 教授 2 说过，科学界有两个重要过错：使用不同的词表示相同的事物，以及使 用同一个词表示不同的事物。如果你研究过并发编程或并行编程，会发现“并发，和“并 行，有不同的定义。我将采用上述引文中 Rob Pike 的非正式定义。

2Imre Simon (1943—2009)是巴西的计算机科学先驱，对自动机理论(Automata Theory)有杰出的贡献，开创了热带 数学(Tropical Mathematics)这一领域。他还是自由软件和自由文化的拥护者。我有幸曾与他一起学习、工作和相

处。

真正的并行需要多个核心。现代的笔记本电脑有4个CPU核心，但是通常不经意间就有 超过 100 个进程同时运行。因此，实际上大多数过程都是并发处理的，而不是并行处理。 计算机始终运行着 100 多个进程，确保每个进程都有机会取得进展，不过 CPU 本身同时 做的事情不能超过四件。十年前使用的设备也能并发处理 100 个进程，不过都在同一个核

心里。鉴于此，Rob Pike才把那次演讲取名为“Concurrency Is Not Parallelism (It's Better)”[“并发不是并行(并发更好)”]。

本章介绍 asyncio 包，这个包使用事件循环驱动的协程实现并发。这是 Python 中最大也 是最具雄心壮志的库之一。 Guido van Rossum 在 Python 仓库之外开发 asyncio 包，把这 个项目的代号命名为“Tulip”(郁金香)。因此，在网上搜索这方面的资料时，会经常看到 这种花的名称。例如，这个项目的主要讨论组仍叫 python-

tulip ( [https://groups.google.com/forum/#!forum/python-tulip ](https://groups.google.com/forum/%23!forum/python-tulip)) 。

Python 3.4把Tulip添加到标准库中时，把它重命名为asyncio。这个包也兼容Python

3.3，在 PyPI 中可以通过新的官方名称找到

(<https://pypi.python.org/pypi/asyncio>)。 asyncio 大量使用 yield from 表达式，因此与 Python 旧版不兼容。

Trollius 项目(也以花名命名，http://trollius.readthedocs.org/)移植了 asyncio，把yield from替换成yield和精巧的回调(From和Return)，以便 支持 Python 2.6 及以上版本。 yield from ... 表达式变成了 yield From(...)； 如果协程需要返回结果，那么要把 return result 替换成 raise

Return(result)。 Trollius 由 Victor Stinner 主导，他也是 asyncio 包的核心开发

者。 Victor 人很好，在本书付梓之前同意审核本章。

本章讨论以下话题：

•对比一个简单的多线程程序和对应的asyncio版，说明多线程和异步任务之间的关

系

• asyncio.Future 类与 concurrent.futures .Future 类之间的区另ij

•第17章中下载国旗那些示例的异步版 •摒弃线程或进程，如何使用异步编程管理网络应用中的高并发 •在异步编程中，与回调相比，协程显著提升性能的方式 •如何把阻塞的操作交给线程池处理，从而避免阻塞事件循环 •使用asyncio编写服务器，重新审视Web应用对高并发的处理方式 •为什么asyncio己经准备好对Python生态系统产生重大影响

首先，本章通过简单的示例来对比 threading 模块和 asyncio 包。

### 18.1 线程与协程对比

有一次讨论线程和GIL时，Michele Simionato发布了一个简单但有趣的示例 （[https://mail+python+org/pipermail/python-list/2009-February/525280+html](https://mail.python.org/pipermail/python-list/2009-February/525280.html)） ：在长时间计算的 过程中，使用 multiprocessing 包在控制台中显示一个由 ASCII 字符 "|/-\" 构成的动 画旋转指针。

我改写了 Simionato 的示例，一个借由 threading 模块使用线程实现，一个借由 asyncio 包使用协程实现。我这么做是为了让你对比两种实现，理解如何不使用线程来 实现并发行为。

示例 18-1 和示例 18-2 的输出是动态的，因此你一定要运行这两个脚本，看看结果如何。 如果你在坐地铁（或者在某个没有 Wi-Fi 连接的地方），可以看图 18-1，想象单

词“thinking”之前的\线是旋转的。

0 O O    2. Python

(.venv34) lontra:17-concurrency lucianoS python3 spinner_thread.py spinner object: <Thread(Thread-1,

Answer: 42

(.venv34) lontra:17-concurrency luciano$ python3 spinner_asyncio.py spinner object: <Task pending coro-<spinC) running at spinner_asyncio.py:12» \ thinking!|

图 18-1：spinner_thread.py 和 spinner_asyncio.py 两个脚本的输出类似：旋转指针对象

的字符串表示形式和文本“Answer: 42”。在这个截图中，spinner_asyncio.py脚本仍在

运行中，旋转指针显示的是“\ thinking!”消息；脚本运行结束后，那一行会替换 成 “Answer: 42”

首先，分析 spinner_thread+py 脚本（见示例 18-1）。

示例18-1 spinner_thread+py：通过线程以动画形式显示文本式旋转指针

def spin(msg, signal): ©

write, flush = sys.stdout.write, sys.stdout.flush for char in itertools.cycle('|/-\\'): ©

status = char + ' ' + msg

write(status)

flush()

write('\x08' * len(status)) ©

time.sleep(.1)

if not signal.go:❺

break

write(' ' * len(status) + '\x08' * len(status)) ©

def slow_function(): O #假装等待I/O—段时间 time.sleep⑶❻ return 42

def supervisor():    ©

signal = Signal()

spinner = threading.Thread(target=spin,

args=('thinking!', signal))

print('spinner object:', spinner) ©

spinner.start() ®

result = slow_function() ®

signal.go = False ©

spinner.join() ©

return result

def main():

result = supervisor() © print('Answer:', result)

if __name__ == '__main__': main()

#### ❶ 这个类定义一个简单的可变对象；其中有个 go 属性，用于从外部控制线程。

❷ 这个函数会在单独的线程中运行。 signal 参数是前面定义的 Signal 类的实例。

❸ 这其实是个无限循环，因为 itertools.cycle 函数会从指定的序列中反复不断地生成

元素。

#### ❹这是显示文本式动画的诀窍所在：使用退格符(\x08)把光标移回来。

❺ 如果 go 属性的值不是 True 了，那就退出循环。

#### ❻ 使用空格清除状态消息，把光标移回开头。

#### ❼ 假设这是耗时的计算。

❽调用sleep函数会阻塞主线程，不过一定要这么做，以便释放GIL，创建从属线程。 ❾ 这个函数设置从属线程，显示线程对象，运行耗时的计算，最后杀死线程。

❿ 显示从属线程对象。输出类似于 <Thread(Thread-1, initial)>。

®启动从属线程。

©运行slow_function函数，阻塞主线程。同时，从属线程以动画形式显示旋转指针。 ®改变signal的状态；这会终止spin函数中的那个for循环。

©等待spinner线程结束。

©运行 supervisor 函数。

注意，Python没有提供终止线程的API，这是有意为之的。若想关闭线程，必须给线程发 送消息。这里，我使用的是 signal.go 属性：在主线程中把它设为 False 后， spinner 线程最终会注意到，然后干净地退出。

下面来看如何使用 @asyncio.coroutine 装饰器替代线程，实现相同的行为。

第16章的小结说过，asyncio包使用的“协程”是较严格的定义。适合 asyncio API的协程在定义体中必须使用yield from，而不能使用yield。此外， 适合 asyncio 的协程要由调用方驱动，并由调用方通过 yield from 调用；或者把 协程传给 asyncio 包中的某个函数，例如 asyncio.async(...) 和本章要介绍的其 他函数，从而驱动协程。最后， @asyncio.coroutine 装饰器应该应用在协程上， 如下述示例所示。

我们来分析示例 18-2。

示例18-2 spinner_asyncio.py：通过协程以动画形式显示文本式旋转指针

import asyncio

import itertools

import sys

@asyncio.coroutine O

def spin(msg): ©

write, flush = sys.stdout.write, sys.stdout.flush for char in itertools.cycle('|/-\\'):

status = char + ' ' + msg

write(status)

flush()

write('\x08' * len(status)) try:

yield from asyncio.sleep(.l) © except asyncio.CancelledError: ©

break

@asyncio.coroutine

def slow_function(): ❺

\#假装等待I/O—段时间

yield from asyncio.sleep(3) ©

return 42

@asyncio.coroutine

def supervisor(): O

spinner = asyncio.async(spin('thinking!')) ❻ print('spinner object:', spinner) © result = yield from slow_function() © spinner.cancel() ©

return result

def main():

loop = asyncio.get_event_loop() ®

result = loop.run_until_complete(supervisor()) ©

loop.close()

print('Answer:', result)

if __name__ == '__main__': main()

❶ 打算交给 asyncio 处理的协程要使用 @asyncio.coroutine 装饰。这不是强制要求，

但是强烈建议这么做。原因在本列表后面。

❷ 这里不需要示例 18-1 中 spin 函数中用来关闭线程的 signal 参数。

❸使用 yield from asyncio.sleep( .1)代替 time.sleep( .1)，这样的休眠不会阻

塞事件循环。

❹ 如果 spin 函数苏醒后抛出 asyncio.CancelledError 异常，其原因是发出了取消请

求，因此退出循环。

❺ 现在， slow_function 函数是协程，在用休眠假装进行 I/O 操作时，使用 yield from 继续执行事件循环。

❻ yield from asyncio.sleep(3) 表达式把控制权交给主循环，在休眠结束后恢复这

个协程。

❼ 现在， supervisor 函数也是协程，因此可以使用 yield from 驱动 slow_function

函数。

❽ asyncio.async(...) 函数排定 spin 协程的运行时间，使用一个 Task 对象包装 spin 协程，并立即返回。

❾ 显示 Task 对象。输出类似于 <Task pending coro=<spin() running at spinner_ asyncio.py:12>>。

❿ 驱动 slow_function() 函数。结束后，获取返回值。同时，事件循环继续运行，因为 slow_function 函数最后使用 yield from asyncio.sleep(3) 表达式把控制权交回给

了主循环。

® Task对象可以取消；取消后会在协程当前暂停的yield处抛出

asyncio.CancelledError 异常。协程可以捕获这个异常，也可以延迟取消，甚至拒绝

取消。

©获取事件循环的引用。

®驱动supervisor协程，让它运行完毕；这个协程的返回值是这次调用的返回值。

除非想阻塞主线程，从而冻结事件循环或整个应用，否则不要在asyncio协 程中使用 time.sleep(...)。如果协程需要在一段时间内什么也不做，应该使用

yield from asyncio.sleep(DELAY)。

使用 @asyncio.coroutine 装饰器不是强制要求，但是强烈建议这么做，因为这样能在

一众普通的函数中把协程凸显出来，也有助于调试：如果还没从中产出值，协程就被垃圾

回收了(意味着有操作未完成，因此有可能是个缺陷)，那就可以发出警告。这个装饰器

不会预激协程。

注意， spinner_thread+py 和 spinner_asyncio+py 两个脚本的代码行数差不多。 supervisor

函数是这两个示例的核心。下面详细对比二者。示例 18-3 只列出了线程版示例中的

supervisor 函数。

示例 18-3 spinner_thread+py：线程版 supervisor 函数

def supervisor():

signal = Signal()

spinner = threading.Thread(target=spin,

args=('thinking!', signal))

print('spinner object:', spinner)

spinner.start()

result = slow_function()

signal.go = False

spinner.join() return result

为了对比，示例 18-4 列出了 supervisor 协程。

示例 18-4 spinner_asyncio+py：异步片反 supervisor 协程

spinner = asyncio.async(spin('thinking!')) print('spinner object:', spinner) result = yield from slow_function() spinner.cancel()

return result

这两种 supervisor 实现之间的主要区别概述如下。

•    asyncio.Task 对象差不多与 threading.Thread 对象等效。Victor Stinner (本章的 特约技术审校)指出，“Task对象像是实现协作式多任务的库(例如gevent)中的 绿色线程(green thread) ”。

•    Task对象用于驱动协程，Thread对象用于调用可调用的对象。

•    Task对象不由自己动手实例化，而是通过把协程传给asyncio.async(...)函数或 loop.create_task(...) 方法获取。

•获取的Task对象己经排定了运行时间(例如，由asyncio.async函数排 定)； Thread 实例则必须调用 start 方法，明确告知让它运行。

•在线程版supervisor函数中，slow_function函数是普通的函数，直接由线程调 用。在异步版 supervisor 函数中， slow_function 函数是协程，由 yield from 驱动。

•没有API能从外部终止线程，因为线程随时可能被中断，导致系统处于无效状态。 如果想终止任务，可以使用 Task.cancel() 实例方法，在协程内部抛出 CancelledError 异常。协程可以在暂停的 yield 处捕获这个异常，处理终止请 求。

•    supervisor协程必须在main函数中由loop.run_until_complete方法执行。

上述比较应该能帮助你理解，与更熟悉的 threading 模型相比， asyncio 是如何编排并 发作业的。

线程与协程之间的比较还有最后一点要说明：如果使用线程做过重要的编程，你就知道写

出程序有多么困难，因为调度程序任何时候都能中断线程。必须记住保留锁，去保护程序

中的重要部分，防止多步操作在执行的过程中中断，防止数据处于无效状态。

而协程默认会做好全方位保护，以防止中断。我们必须显式产出才能让程序的余下部分运 行。对协程来说，无需保留锁，在多个线程之间同步操作，协程自身就会同步，因为在任 意时刻只有一个协程运行。想交出控制权时，可以使用 yield 或 yield from 把控制权 交还调度程序。这就是能够安全地取消协程的原因：按照定义，协程只能在暂停的 yield 处取消，因此可以处理 CancelledError 异常，执行清理操作。

下面说明 asyncio.Future 类与第 17 章所用的 concurrent.futures.Future 类之间的

区别。

18.1.1 asyncio.Future:故意不阻塞

asyncio.Future 类与 concurrent.futures.Future 类的接口基本一致，不过实现方

式不同，不可以互换。 “PEP 3156—Asynchronous IO Support Rebooted:

the‘asyncio'Module” (<https://www.python.org/dev/peps/pep-3156/>)对这个不幸状况是这样说 的:

未来可能会统一 asyncio.Future 和 concurrent.futures.Future 类实现的期物 (例如，为后者添加兼容 yield from 的 __iter__ 方法)。

如 17.1.3 节所述，期物只是调度执行某物的结果。在 asyncio 包

中， BaseEventLoop.create_task(...) 方法接收一个协程，排定它的运行时间，然后 返回一个 asyncio.Task 实例——也是 asyncio.Future 类的实例，因为 Task 是 Future 的子类，用于包装协程。这与调用 Executor.submit(...) 方法创建 concurrent.futures.Future 实例是一个道理。

与 concurrent.futures.Future 类似， asyncio.Future 类也提供了

.done()、.add_done_callback(...) 和 .result() 等方法。前两个方法的用法与 17.1.3 节所述的一样，不过 .result() 方法差别很大。

asyncio.Future 类的 .result() 方法没有参数，因此不能指定超时时间。此外，如果

调用 .result() 方法时期物还没运行完毕，那么 .result() 方法不会阻塞去等待结果， 而是抛出 asyncio.InvalidStateError 异常。

然而，获取asyncio.Future对象的结果通常使用yield from，从中产出结果，如示例

18-8 所示。

使用 yield from 处理期物，等待期物运行完毕这一步无需我们关心，而且不会阻塞事件

循环，因为在 asyncio 包中， yield from 的作用是把控制权还给事件循环。

注意，使用 yield from 处理期物与使用 add_done_callback 方法处理协程的作用一

样:延迟的操作结束后，事件循环不会触发回调对象，而是设置期物的返回值；而 yield from 表达式则在暂停的协程中生成返回值，恢复执行协程。

总之，因为 asyncio.Future 类的目的是与 yield from 一起使用，所以通常不需要使

用以下方法。

•无需调用my_future.add_done_callback(...)，因为可以直接把想在期物运行结 束后执行的操作放在协程中 yield from my_future 表达式的后面。这是协程的一

大优势:协程是可以暂停和恢复的函数。

•无需调用my_future.result()，因为yield from从期物中产出的值就是结果 (例如， result = yield from my_future)。

当然，有时也需要使用 .done()、.add_done_callback(...) 和 .result() 方法。但 是一般情况下， asyncio.Future 对象由 yield from 驱动，而不是靠调用这些方法驱

动。

下面分析 yield from 和 asyncio 包的 API 如何拉近期物、任务和协程的关系。

18.1.2 从期物、任务和协程中产出

在 asyncio 包中，期物和协程关系紧密，因为可以使用 yield from 从

asyncio.Future 对象中产出结果。这意味着，如果 foo 是协程函数(调用后返回协程 对象)，抑或是返回 Future 或 Task 实例的普通函数，那么可以这样写： res = yield from foo()。这是asyncio包的API中很多地方可以互换协程与期物的原因之一。

为了执行这些操作，必须排定协程的运行时间，然后使用 asyncio.Task 对象包装协 程。对协程来说，获取 Task 对象有两种主要方式。 asyncio.async(coro_or_future, *, loop=None)

这个函数统一了协程和期物：第一个参数可以是二者中的任何一个。如果是 Future 或 Task 对象，那就原封不动地返回。如果是协程，那么 async 函数会调用 loop.create_task(...) 方法创建 Task 对象。 loop= 关键字参数是可选的，用于传入 事件循环；如果没有传入，那么 async 函数会通过调用 asyncio.get_event_loop() 函 数获取循环对象。

BaseEventLoop.create_task(coro)

这个方法排定协程的执行时间，返回一个 asyncio.Task 对象。如果在自定义的 BaseEventLoop子类上调用，返回的对象可能是外部库(如Tornado)中与Task类兼容

的某个类的实例。

BaseEventLoop.create_task(...)方法只在 Python 3+4+2 及以上版本中可 用。如果是 Python 3+3 或 Python 3+4 的旧版，要使用 asyncio.async(...) 函数，或 者从 PyPI 中安装较新的 asyncio 版本( [https://pypi +python+org/pypi/asyncio](https://pypi.python.org/pypi/asyncio) ) 。

asyncio 包中有多个函数会自动(内部使用的是 asyncio.async 函数)把参数指定的协 程包装在 asyncio.Task 对象中，例如 BaseEventLoop.run_until_complete(...) 方 法。

如果想在 Python 控制台或者小型测试脚本中试验期物和协程，可以使用下述代码片段： 3

I[3](https://mail.python.org/pipermail/python-ideas/2014-September/029294.html)[摘自 Petr Viktorin 于 2014 年 9 月 11 日在 Python-ideas 邮件列表中发布的消息(https://mai!python+org/pipermail/python-](https://mail.python.org/pipermail/python-ideas/2014-September/029294.html)ideas/2014-September/029294+html)。

\>>> import asyncio

\>>> def run_sync(coro_or_future):

...    loop = asyncio.get_event_loop()

...    return loop.run_until_complete(coro_or_future)

\>>> a = run sync(some coroutine())

在 asyncio 包的文档中，“18.5.3. Tasks and coroutines”一节 (<https://docs.python.org/3/library/asyncio-task.html>)说明了协程、期物和任务之间的关系。

其中有个注解说道：

这份文档把一些方法说成是协程，即使它们其实是返回 Future 对象的普通 Python 函 数。这是故意的，为的是给以后修改这些函数的实现留下余地。

掌握这些基础知识后，接下来要分析异步下载国旗的 flags_asyncio.py 脚本。这个脚本的 用法在示例 17-1(第 17 章)中与依序下载版和线程池版一同演示过。

### 18.2 使用asyncio和aiohttp包下载

从Python 3.4起，asyncio包只直接支持TCP和UDP。如果想使用HTTP或其他协议， 那么要借助第三方包。当下，使用 asyncio 实现 HTTP 客户端和服务器时，使用的似乎 都是 aiohttp 包。

示例 18-5 是下载国旗的 flags_asyncio.py 脚本的完整代码清单。运作方式简述如下。

(1)    首先，在 download_many 函数中获取一个事件循环，处理调用 download_one 函数 生成的几个协程对象。

(2)    asyncio 事件循环依次激活各个协程。

(3)    客户代码中的协程(如get_flag)使用yield from把职责委托给库里的协程(如 aiohttp.request)时，控制权交还事件循环，执行之前排定的协程。

(4)    事件循环通过基于回调的低层API，在阻塞的操作执行完毕后获得通知。

(5)    获得通知后，主循环把结果发给暂停的协程。

(6)    协程向前执行到下一个 yield from 表达式，例如 get_flag 函数中的 yield from resp.read()。事件循环再次得到控制权，重复第4〜6步，直到事件循环终止。

这与 16.9.2 节所见的示例类似。在那个示例中，主循环依次启动多个出租车进程；各个出 租车进程产出结果后，主循环调度各个出租车的下一个事件(未来发生的事)，然后激活

队列中的下一个出租车进程。那个出租车仿真简单得多，主循环易于理解。不过，在

asyncio 中，基本的流程是一样的:在一个单线程程序中使用主循环依次激活队列里的

协程。各个协程向前执行几步，然后把控制权让给主循环，主循环再激活队列里的下一个

协程。

下面详细分析示例 18-5。

示例18-5 flags_asyncio.py：使用asyncio和aiohttp包实现的异步下载脚本

import asyncio

import aiohttp O

from flags import BASE_URL, save_flag, show, main ©

@asyncio.coroutine ©

def get_flag(cc):

url = '{}/{cc}/{cc}.gif'.format(BASE_URL, cc=cc.lower()) resp = yield from aiohttp.request('GET', url) © image = yield from resp.read()❺

return image

@asyncio.coroutine

def download_one(cc): ©

image = yield from get_flag(cc) & show(cc)

save_flag(image, cc.lower() + '.gif') return cc

def download_many(cc_list):

loop = asyncio.get_event_loop()❻

to_do = [download_one(cc) for cc in sorted(cc_list)] © wait_coro = asyncio.wait(to_do) © res, _ = loop.run_until_complete(wait_coro) ® loop.close() ®

return len(res)

if __name__ == '__main__': main(download_many)

❶ 必须安装 aiohttp 包，它不在标准库中。 4

| 4可以使用pip install aiohttp命令安装aiohttp包。-编者注

❷ 重用 flags 模块（见示例 17-2）中的一些函数。

❸ 协程应该使用 @asyncio.coroutine 装饰。

❹ 阻塞的操作通过协程实现，客户代码通过 yield from 把职责委托给协程，以便异步 运行协程。

❺ 读取响应内容是一项单独的异步操作。

❻ download_one 函数也必须是协程，因为用到了 yield from。

❼与依序下载版download_one函数唯一的区别是这一行中的yield from；函数定义

体中的其他代码与之前完全一样。

❽ 获取事件循环底层实现的引用。

❾ 调用 download_one 函数获取各个国旗，然后构建一个生成器对象列表。

❿虽然函数的名称是wait，但它不是阻塞型函数。wait是一个协程，等传给它的所有 协程运行完毕后结束（这是 wait 函数的默认行为；参见这个示例后面的说明）。

©执行事件循环，直到wait_coro运行结束；事件循环运行的过程中，这个脚本会在这 里阻塞。我们忽略 run_until_complete 方法返回的第二个元素。下文说明原因。

©关闭事件循环。

如果事件循环是上下文管理器就好了，这样我们就可以使用with块确保循环 会被关闭。然而，实际情况是复杂的，客户代码绝不会直接创建事件循环，而是调用

asyncio.get_event_loop() 函数，获取事件循环的引用。而且有时我们的代码 不“拥有”事件循环，因此关闭事件循环会出错。例如，使用

Quamash ([https://pypi+python+org/pypi/Quamash/](https://pypi.python.org/pypi/Quamash/))这种包实现的外部 GUI 事件循环时，

Qt 库负责在退出应用时关闭事件循环。

asyncio.wait(...)协程的参数是一个由期物或协程构成的可迭代对象；wait会分别 把各个协程包装进一个 Task 对象。最终的结果是， wait 处理的所有对象都通过某种方 式变成 Future 类的实例。 wait 是协程函数，因此返回的是一个协程或生成器对 象； wait_coro 变量中存储的正是这种对象。为了驱动协程，我们把协程传给 loop.run_until_complete(...) 方法。

loop.run_until_complete 方法的参数是一个期物或协程。如果是协

程， run_until_complete 方法与 wait 函数一样，把协程包装进一个 Task 对象中。协 程、期物和任务都能由 yield from 驱动，这正是 run_until_complete 方法对 wait 函数返回的 wait_coro 对象所做的事。 wait_coro 运行结束后返回一个元组，第一个元 素是一系列结束的期物，第二个元素是一系列未结束的期物。在示例 18-5 中，第二个元 素始终为空，因此我们把它赋值给 _，将其忽略。但是 wait 函数有两个关键字参数，如 果设定了可能会返回未结束的期物；这两个参数是timeout和return_when。详情参见 [asyncio.wait ](https://docs.python.org/3/library/asyncio-task.html%23asyncio.wait)[函数的文档( https ://docs+ python+ org/3/library/asyncio-](https://docs.python.org/3/library/asyncio-task.html%23asyncio.wait)

task+html#asyncio+wait) 。

注意，在示例 18-5 中不能重用 flags+py 脚本(见示例 17-2)中的 get_flag 函数，因为那 个函数用到了 requests 库，执行的是阻塞型 I/O 操作。为了使用 asyncio 包，我们必 须把每个访问网络的函数改成异步版，使用 yield from 处理网络操作，这样才能把控制 权交还给事件循环。在get_flag函数中使用yield from，意味着它必须像协程那样驱 动。

因此，不能重用 flags_threadpool+py 脚本(见示例 17-3)中的 download_one 函数。示例 18-5 中的代码使用 yield from 驱动 get_flag 函数，因此 download_one 函数本身也 得是协程。每次请求时， download_many 函数会创建一个 download_one 协程对象；这 些协程对象先使用 asyncio.wait 协程包装，然后由 loop.run_until_complete 方法 驱动。

asyncio 包中有很多新概念要掌握，不过，如果你采用 Guido van Rossum 建议的一个技

巧，就能轻松地理解示例 18-5 的总体逻辑：眯着眼，假装没有 yield from 关键字。这 样做之后，你会发现示例 18-5 中的代码与纯粹依序下载的代码一样易于阅读。 比如说，以这个协程为例：

return image

我们可以假设它与下述函数的作用相同，只不过协程版从不阻塞：

def get_flag(cc):

url = '{}/{cc}/{cc}.gif'.format(BASE_URL, cc=cc.lower()) resp = aiohttp.request('GET', url) image = resp.read()

return image

yield from foo 句法能防止阻塞，是因为当前协程（即包含 yield from 代码的委派生 成器）暂停后，控制权回到事件循环手中，再去驱动其他协程；foo期物或协程运行完毕 后，把结果返回给暂停的协程，将其恢复。

在 16.7 节的末尾，我对 yield from 的用法做了两点陈述，摘要如下。

•使用yield from链接的多个协程最终必须由不是协程的调用方驱动，调用方显式或 隐式（例如，在 for 循环中）在最外层委派生成器上调用 next（...） 函数或 .send（...） 方法。

•链条中最内层的子生成器必须是简单的生成器（只使用yield）或可迭代的对象。

在 asyncio 包的 API 中使用 yield from 时，这两点都成立，不过要注意下述细节。

•我们编写的协程链条始终通过把最外层委派生成器传给asyncio包API中的某个函 数（如 loop.run_until_complete（...））驱动。

也就是说，使用 asyncio 包时，我们编写的代码不通过调用 next（...） 函数或 .send（...） 方法驱动协程——这一点由 asyncio 包实现的事件循环去做。

•我们编写的协程链条最终通过yield from把职责委托给asyncio包中的某个协程 函数或协程方法（例如示例 18-2 中的 yield from asyncio.sleep（...）），或者 其他库中实现高层协议的协程（例如示例 18-5 中 get_flag 协程里的 resp = yield from aiohttp. request（'GET', url））。

也就是说，最内层的子生成器是库中真正执行 I/O 操作的函数，而不是我们自己编写 的函数。

概括起来就是：使用 asyncio 包时，我们编写的异步代码中包含由 asyncio 本身驱动的 协程（即委派生成器），而生成器最终把职责委托给 asyncio 包或第三方库（如 aiohttp）中的协程。这种处理方式相当于架起了管道，让asyncio事件循环（通过我 们编写的协程）驱动执行低层异步 I/O 操作的库函数。

现在可以回答第 17 章提出的那个问题了。

• flags_asyncio.py脚本和flags.py脚本都在单个线程中运行，前者怎么会比后者快5

倍？



### 18.3 避免阻塞型调用

Ryan Dahl（Node.js 的发明者）在介绍他的项目背后的哲学时说：“我们处理 I/O 的方式彻 底错了。 ”5 他把执行硬盘或网络 I/O 操作的函数定义为阻塞型函数，主张不能像对待非 阻塞型函数那样对待阻塞型函数。为了说明原因，他展示了表 18-1 中的前两列。

| ^“Introduction to Node.js” （https://www.youtube.com/watch?v=M-sc73Y-zQA）视频 4:55 处。

表18-1：使用现代的电脑从不同的存储介质中读取数据的延迟情况；第三栏按比例换

算成具体的时间，便于人类理解

| 存储介质 | CPU周期     | 按比例换算成“人类时间" |
| -------- | ----------- | ---------------------- |
| L1缓存   | 3           | 3秒                    |
| L2缓存   | 14          | 14 秒                  |
| RAM      | 250         | 250 秒                 |
| 硬盘     | 41 000 000  | 1.3 年                 |
| 网络     | 240 000 000 | 7.6 年                 |

为了理解表 18-1，请记住一点：现代的 CPU 拥有 GHz 数量级的时钟频率，每秒钟能运行 几十亿个周期。假设 CPU 每秒正好运行十亿个周期，那么 CPU 可以在一秒钟内读取 L1 缓存 333 333 333 次，读取网络 4 次（只有 4 次）。表 18-1 中的第三栏是拿第二栏中的各 个值乘以固定的因子得到的。因此，在另一个世界中，如果读取 L1 缓存要用 3 秒，那么 读取网络要用 7.6 年！

有两种方法能避免阻塞型调用中止整个应用程序的进程：

•在单独的线程中运行各个阻塞型操作 •把每个阻塞型操作转换成非阻塞的异步调用使用

多个线程是可以的，但是各个操作系统线程（Python使用的是这种线程）消耗的内存达兆

字节（具体的量取决于操作系统种类）。如果要处理几千个连接，而每个连接都使用一个

线程的话，我们负担不起。

为了降低内存的消耗，通常使用回调来实现异步调用。这是一种低层概念，类似于所有并

发机制中最古老、最原始的那种——硬件中断。使用回调时，我们不等待响应，而是注册 一个函数，在发生某件事时调用。这样，所有调用都是非阻塞的。因为回调简单，而且消 耗低，所以 Ryan Dahl 拥护这种方式。

当然，只有异步应用程序底层的事件循环能依靠基础设置的中断、线程、轮询和后台进程

等，确保多个并发请求能取得进展并最终完成，这样才能使用回调。 6 事件循环获得响应 后，会回过头来调用我们指定的回调。不过，如果做法正确，事件循环和应用代码共用的

主线程绝不会阻塞。

6其实，虽然 Node+js 不支持使用 JavaScript 编写的用户级线程，但是在背后却借助 libeio 库使用 C 语言实现了线程 池，以此提供基于回调的文件API——因为从2014年起，大多数操作系统都不提供稳定且便携的异步文件处理API 了。

把生成器当作协程使用是异步编程的另一种方式。对事件循环来说，调用回调与在暂停的 协程上调用 .send() 方法效果差不多。各个暂停的协程是要消耗内存，但是比线程消耗

的内存数量级小。而且，协程能避免可怕的“回调地狱”；这一点会在 18+5 节讨论。

现在你应该能理解为什么flags_asyncio+py脚本的性能比flags+py脚本高5倍了： flags+py 脚本依序下载，而每次下载都要用几十亿个 CPU 周期等待结果。其实， CPU 同时做了很 多事，只是没有运行你的程序。与此相比，在 flags_asyncio+py 脚本中，在

download_many 函数中调用 loop.run_until_complete 方法时，事件循环驱动各个 download_one 协程，运行到第一个 yield from 表达式处，那个表达式又驱动各个 get_flag 协程，运行到第一个 yield from 表达式处，调用 aiohttp.request(...) 函数。这些调用都不会阻塞，因此在零点几秒内所有请求全部开始。

asyncio 的基础设施获得第一个响应后，事件循环把响应发给等待结果的 get_flag 协 程。得到响应后， get_flag 向前执行到下一个 yield from 表达式处，调用 resp.read() 方法，然后把控制权还给主循环。其他响应会陆续返回(因为请求几乎同 时发出)。所有 get_ flag 协程都获得结果后，委派生成器 download_one 恢复，保存 图像文件。

为了尽量提高性能，save_flag函数应该执行异步操作，可是asyncio包目 前没有提供异步文件系统 API(Node 有)。如果这是应用的瓶颈，可以使用

loop. run_in_executor 方法(https://docs+python+org/3/library/asyncio-eventloop+html#asyncio+BaseEventLoop+run_in_executor[) ，在线程池中运行](https://docs.python.org/3/library/asyncio-eventloop.html%23asyncio.BaseEventLoop.run_in_executor) save_flag

函数。示例 18-9 会说明做法。

因为异步操作是交叉执行的，所以并发下载多张图像所需的总时间比依序下载少得多。我 使用 asyncio 包发起了 600 个 HTTP 请求，获得所有结果的时间比依序下载快 70 倍。

现在回到那个 HTTP 客户端示例，看看如何显示动态的进度条，并且恰当地处理错误。

### 18.4    改进asyncio下载脚本

17.5    节说过， flags2 系列示例的命令行接口相同。本节要分析这个系列中的 flags2_asyncio.py脚本。例如，示例18-6展示如何使用100个并发请求(-m 100)从 ERROR服务器中下载100面国旗(-al 100)。

示例 18-6 运行 flags2_asyncio.py 脚本

$ python3 flags2_asyncio.py -s ERROR -al 100 -m 100 ERROR site: <http://localhost:8003/flags> Searching for 100 flags: from AD to LK 100 concurrent connections will be used.

73 flags downloaded. 27 errors.

Elapsed time: 0.64s

![img](08414584Python-84.jpg)



测试并发客户端要谨慎

尽管线程版和 asyncio 版 HTTP 客户端的下载总时间相差无几，但是 asyncio 版发 送请求的速度更快，因此很有可能对服务器发起 DoS 攻击。为了全速测试这些并发 客户端，应该在本地搭建 HTTP 服务器，详情参见本书代码仓库

(<https://github.com/fluentpython/example-code>)中 17-fUtures/countries/ 目录里的 [README.rst ](https://github.com/fluentpython/example-code/blob/master/17-futures/countries/README.rst)[文件(https://github .com/fluentpython/ example-code/blob/master/17-](https://github.com/fluentpython/example-code/blob/master/17-futures/countries/README.rst)futures/countries/README.rst)。

下面分析 flags2_asyncio.py 脚本的实现方式。

18.4.1 使用 asyncio.as_completed 函数

在示例 18-5 中，我把一个协程列表传给 asyncio.wait 函数，经由

loop.run_until_complete 方法驱动，全部协程运行完毕后，这个函数会返回所有下载 结果。可是，为了更新进度条，各个协程运行结束后就要立即获取结果。在线程池版示例 中(见示例 17-14)，为了集成进度条，我们使用的是 as_completed 生成器函数；幸 好， asyncio 包提供了这个生成器函数的相应版本。

为了使用 asyncio 包实现 flags2 示例，我们要重写几个函数；重写后的函数可以供 concurrent.future 版重用。之所以要重写，是因为在使用 asyncio 包的程序中只有一

个主线程，而在这个线程中不能有阻塞型调用，因为事件循环也在这个线程中运行。所 以，我要重写 get_flag 函数，使用 yield from 访问网络。现在，由于 get_flag 是协 程， download_one 函数必须使用 yield from 驱动它，因此 download_one 自己也要 变成协程。之前，在示例 18-5 中， download_one 由 download_many 驱

动： download_one 函数由 asyncio. wait 函数调用，然后传给

loop.run_until_complete 方法。现在，为了报告进度并处理错误，我们要更精确地控

制，所以我把 download_many 函数中的大多数逻辑移到一个新的协程

downloader_coro 中，只在 download_many 函数中设置事件循环，以及调度 downloader_coro 协程。

示例 18-7 展示的是 flags2_asyncio.py 脚本的前半部分，定义 get_flag 和 download_one 协程。示例 18-8 列出余下的源码，定义 downloader_coro 协程和 download_many 函

数。

示例18-7 flags2_asyncio.py：脚本的前半部分；余下的代码在示例18-8中

import asyncio

import collections

import aiohttp

from aiohttp import web

import tqdm

from flags2_common import main, HTTPStatus, Result, save_flag

\#    默认设为较小的值，防止远程网站出错

\#    例如503 - Service Temporarily Unavailable

DEFAULT_CONCUR_REQ = 5

MAX_CONCUR_REQ = 1000

class FetchError(Exception): O

def __init__(self, country_code):

self.country_code = country_code

@asyncio.coroutine

def get_flag(base_url, cc): ©

url = '{}/{cc}/{cc}.gif'.format(base_url, cc=cc.lower()) resp = yield from aiohttp.request('GET', url)

if resp.status == 200:

image = yield from resp.read() return image

elif resp.status == 404:

raise web.HTTPNotFound()

else:

raise aiohttp.HttpProcessingError(

code=resp.status, message=resp.reason, headers=resp.headers)

@asyncio.coroutine

def download_one(cc, base_url, semaphore, verbose): © try:

with (yield from semaphore): ©

image = yield from get_flag(base_url, cc)❺

except web.HTTPNotFound: ©

status = HTTPStatus.not_found msg = 'not found'

except Exception as exc:

raise FetchError(cc) from exc O

else:

save_flag(image, cc.lower() + '.gif')❻

status = HTTPStatus.ok

msg = 'OK'

if verbose and msg: print(cc, msg)

return Result(status, cc)

❶这个自定义的异常用于包装其他HTTP或网络异常，并获取country_code，以便报告

错误。

❷get_flag协程有三种返回结果：返回下载得到的图像；HTTP响应码为404时，抛出 web.HTTPNotFound 异常；返回其他 HTTP 状态码时，抛出 aio[http.HttpProcessingError](http://http.HttpProcessingError) 异常。

[❸ ](https://docs.python.org/3/library/asyncio-sync.html%23asyncio.Semaphore)[semaphore 参数是 asyncio.Semaphore 类(https://docs+python+org/3/library/asyncio-](https://docs.python.org/3/library/asyncio-sync.html%23asyncio.Semaphore)sync+html#asyncio+ Semaphore)的实例。Semaphore类是同步装置，用于限制并发请求数

量。

❹ 在 yield from 表达式中把 semaphore 当成上下文管理器使用，防止阻塞整个系统： 如果 semaphore 计数器的值是所允许的最大值，只有这个协程会阻塞。

❺ 退出这个 with 语句后， semaphore 计数器的值会递减，解除阻塞可能在等待同一个 semaphore 对象的其他协程实例。

❻ 如果没找到国旗，相应地设置 Result 的状态。

❼其他异常当作FetchError抛出，传入国家代码，并使用“PEP 3134 — Exception Chaining and Embedded Tracebacks” ([https://www+python+ org/dev/peps/pep-3134/](https://www.python.org/dev/peps/pep-3134/))引入的 raise X from Y 句法链接原来的异常。

❽ 这个函数的作用是把国旗文件保存到硬盘中。

可以看出，与依序下载版相比，示例 18-7 中的 get_flag 和 download_one 函数改动幅 度很大，因为现在这两个函数是协程，要使用 yield from 做异步调用。

对于我们分析的这种网络客户端代码来说，一定要使用某种限流机制，防止向服务器发起

太多并发请求，因为如果服务器过载，那么系统的整体性能可能会下降。

flags2_threadpool+py 脚本(见示例 17-14)限流的方法是，在 download_many 函数中实例 化ThreadPoolExecutor类时把max_workers参数的值设为concur_req，只在线程池 中启动 concur_req 个线程。在 flags2_asyncio+py 脚本中我的做法是，在

downloader_coro 函数中创建一个 asyncio.Semaphore 实例(在后面的示例 18-8 中)，然后把它传给示例 18-7 中 download_one 函数的 semaphore 参数。 7 7感谢Guto Maia指出本书的草稿没有说明Semaphore类。

Semaphore 对象维护着一个内部计数器，若在对象上调用 .acquire() 协程方法，计数 器则递减；若在对象上调用 .release() 协程方法，计数器则递增。计数器的初始值在实

例化 Semaphore 时设定，如 downloader_coro 函数中的这一行所示：

semaphore = asyncio.Semaphore(concur_req)

如果计数器大于零，那么调用 .acquire() 方法不会阻塞；可是，如果计数器为零，那么 .acquire() 方法会阻塞调用这个方法的协程，直到其他协程在同一个 Semaphore 对象 上调用 .release() 方法，让计数器递增。在示例 18-7 中，我没有调用 .acquire() 或 .release() 方法，而是在 download_one 函数中的下述代码块中把 semaphore 当作上 下文管理器使用：

with (yield from semaphore):

image = yield from get_flag(base_url, cc)

这段代码保证，任何时候都不会有超过 concur_req 个 get_flag 协程启动。

现在来分析示例 18-8 中这个脚本余下的代码。注意， download_many 函数中以前的大多 数功能现在都在 downloader_coro 协程中。我们必须这么做，因为必须使用 yield from 获取 asyncio.as_completed 函数产出的期物的结果，所以 as_completed 函数 必须在协程中调用。可是，我不能直接把 download_many 函数改成协程，因为必须在脚 本的最后一行把 download_many 函数传给 flags2_common 模块中定义的 main 函数，可 main 函数的参数不是协程，而是一个普通的函数。因此，我定义了 downloader_coro 协程，让它运行 as_completed 循环。现在， download_many 函数只用于设置事件循 环，并把 downloader_coro 协程传给 loop.run_until_complete 方法，调度 downloader_coro。

示例 18-8 flags2_asyncio.py：接续示例 18-7

@asyncio.coroutine

def downloader_coro(cc_list, base_url, verbose, concur_req): O counter = collections.Counter() semaphore = asyncio.Semaphore(concur_req) © to_do = [download_one(cc, base_url, semaphore, verbose)

for cc in sorted(cc_list)] ©

to_do_iter = asyncio.as_completed(to_do) © if not verbose:

to_do_iter = tqdm.tqdm(to_do_iter, total=len(cc_list))❺ for future in to_do_iter: ©

try:

res = yield from future & except FetchError as exc:❻

country_code = exc.country_code © try:

error_msg = exc._cause_.args[0] © except IndexError:

error_msg = exc._cause_._class_._name_ ® if verbose and error_msg:

msg = '*** Error for {}: {}' print(msg.format(country_code, error_msg))

status = HTTPStatus.error else:

status = res.status counter[status] += 1 ®

return counter ©

def download_many(cc_list, base_url, verbose, concur_req): loop = asyncio.get_event_loop()

coro = downloader_coro(cc_list, base_url, verbose, concur_req) counts = loop.run_until_complete(coro) © loop.close() ©

return counts

if __name__ == '__main__':

main(download_many, DEFAULT_CONCUR_REQ, MAX_CONCUR_REQ)

❶ 这个协程的参数与 download_many 函数一样，但是不能直接调用，因为它是协程函 数，而不是像 download_many 那样的普通函数。

❷ 创建一个 asyncio.Semaphore 实例，最多允许激活 concur_req 个使用这个计数器 的协程。

❸ 多次调用 download_one 协程，创建一个协程对象列表。

❹ 获取一个迭代器，这个迭代器会在期物运行结束后返回期物。

❺ 把迭代器传给 tqdm 函数，显示进度。

❻ 迭代运行结束的期物；这个循环与示例 17-14 中 download_many 函数里的那个十分相 似；不同的部分主要是异常处理，因为两个HTTP库(requests和aiohttp)之间有差 异。

❼获取asyncio.Future对象的结果，最简单的方法是使用yield from，而不是调用 future.result() 方法。

❽ download_one 函数抛出的各个异常都包装在 FetchError 对象里，并且链接原来的 异常。

❾ 从 FetchError 异常中获取错误发生时的国家代码。

❿ 尝试从原来的异常(__cause__)中获取错误消息。

®如果在原来的异常中找不到错误消息，使用所链接异常的类名作为错误消息。

©记录结果。

®与其他脚本一样，返回计数器。

© download_many函数只是实例化downloader_coro协程，然后通过

run_until_complete 方法把它传给事件循环。

©所有工作做完后，关闭事件循环，返回counts。

在示例 18-8 中不能像示例 17-14 那样把期物映射到国家代码上，因为

asyncio.as_completed 函数返回的期物与传给 as_completed 函数的期物可能不同。

在 asyncio 包内部，我们提供的期物会被替换成生成相同结果的期物。 8

8关于这一点的详细讨论，可以阅读我在python-tulip讨论组中发起的话题，题为“Which other futures my come out of asyncio.as_completed?”（https://groups.google.com/forum/#!msg/python-tulip/PdAEtwpaJHs/7fqb-Qj2zJoJ） 。 Guido 回复了， 而且深入分析了 as_completed 函数的实现，还说明了 asyncio 包中期物与协程之间的紧密关系。

因为失败时不能以期物为键从字典中获取国家代码，所以我实现了自定义的 FetchError 异常（如示例 18-7 所示）。 FetchError 包装网络异常，并关联相应的国家代码，因此 在详细模式中报告错误时能显示国家代码。如果没有错误，那么国家代码是 for 循环顶

部那个 yield from future 表达式的结果。

我们使用 asyncio 包实现的这个示例与前面的 flags2_threadpool.py 脚本具有相同的功 能，这一话题到此结束。接下来，我们要改进 flags2_asyncio.py 脚本，进一步探索

asyncio 包。

在分析示例 18-7 的过程中，我发现 save_flag 函数会执行硬盘 I/O 操作，而这应该异步 执行。下一节说明做法。

18.4.2使用Executor对象，防止阻塞事件循环

Python 社区往往会忽略一个事实——访问本地文件系统会阻塞，想当然地认为这种操作不 会受网络访问的高延迟影响（这也极难预料）。与之相比， Node.js 程序员则始终谨记，

所有文件系统函数都会阻塞，因为这些函数的签名中指明了要有回调。表 18-1 已经指

出，硬盘 I/O 阻塞会浪费几百万个 CPU 周期，而这可能会对应用程序的性能产生重大影

响。

在示例18-7中，阻塞型函数是save_flag。在这个脚本的线程版中（见示例17-14）， save_flag 函数会阻塞运行 download_one 函数的线程，但是阻塞的只是众多工 作线程中的一个。阻塞型I/O调用在背后会释放GIL，因此另一个线程可以继续。但是在 flags2_asyncio.py 脚本中， save_flag 函数阻塞了客户代码与 asyncio 事件循环共用的唯 一线程，因此保存文件时，整个应用程序都会冻结。这个问题的解决方法是，使用事件循 环对象的 run_in_executor 方法。

asyncio 的事件循环在背后维护着一个 ThreadPoolExecutor 对象，我们可以调用 run_in_executor 方法，把可调用的对象发给它执行。若想在这个示例中使用这个功 能， download_one 协程只有几行代码需要改动，如示例 18-9 所示。

示例 18-9 flags2_asyncio_executor.py：使用默认的 ThreadPoolExecutor 对象运行



#### save_flag 函数

@asyncio.coroutine

def download_one(cc, base_url, semaphore, verbose): try:

with (yield from semaphore):

image = yield from get_flag(base_url, cc) except web.HTTPNotFound:

status = HTTPStatus.not_found msg = 'not found' except Exception as exc:

raise FetchError(cc) from exc else:

loop = asyncio.get_event_loop() O loop.run_in_executor(None, ©

save_flag, image, cc.lower() + '.gif')    ©

status = HTTPStatus.ok msg = 'OK'

if verbose and msg: print(cc, msg)

return Result(status, cc)

#### ❶ 获取事件循环对象的引用。

❷run_in_executor方法的第一个参数是Executor实例；如果设为None，使用事件 循环的默认 ThreadPoolExecutor 实例。

#### ❸ 余下的参数是可调用的对象，以及可调用对象的位置参数。

![img](08414584Python-85.jpg)



#### 我测试示例18-9时，没有发现改用run_in_executor方法保存图像文件后性

能有明显变化，因为图像都不大(平均13KB)。不过，如果编辑flags2_common.py 脚本中的 save_flag 函数，把各个文件保存的字节数变成原来的 10 倍(只需把 fp.write(img)改成fp.write(img*10))，此时便会看到效果。下载的平均字节 数变成 130KB 后，使用 run_in_executor 方法的好处就体现出来了。如果下载包 含百万像素的图像，速度提升更明显。

#### 如果需要协调异步请求，而不只是发起完全独立的请求，协程较之回调的好处会变得显而

易见。下一节说明回调的问题，并给出解决方法。

### 18.5 从回调到期物和协程

使用协程做面向事件编程，需要下一番功夫才能掌握，因此最好知道，与经典的回调式编

程相比，协程有哪些改进。这就是本节的话题。

只要对回调式面向事件编程有一定的经验，就知道“回调地狱”这个术语：如果一个操作需 要依赖之前操作的结果，那就得嵌套回调。如果要连续做 3 次异步调用，那就需要嵌套 3

层回调。示例 18-10 是一个使用 JavaScript 编写的例子。

示例 18-10 JavaScript 中的回调地狱：嵌套匿名函数，也称为灾难金字塔

api_call1(request1, function (response1) {

// 第一步

var request2 = step1(response1);

api_call2(request2, function (response2) {

// 第二步

var request3 = step2(response2);

api_call3(request3, function (response3) {

// 第三步

step3(response3);

});

});

});

在示例 18-10 中， api_call1、api_call2 和 api_call3 是库函数，用于异步获取结 果。例如， api_call1 从数据库中获取结果， api_call2 从 Web 服务器中获取结果。这 3 个函数都有回调。在 JavaScript 中，回调通常使用匿名函数实现（在下述 Python 示例中 分别把这3个回调命名为stagel、stage2和stage3）。这里的stepl、step2和 step3 是应用程序中的常规函数，用于处理回调接收到的响应。

示例 18-11 展示 Python 中的回调地狱是什么样子。

示例 18-11 Python 中的回调地狱：链式回调

def stage1(response1):

request2 = step1(response1) api_call2(request2, stage2)

def stage2(response2):

request3 = step2(response2) api_call3(request3, stage3)

def stage3(response3): step3(response3)

api_call1(request1, stage1)

虽然示例 18-11 中的代码与示例 18-10 的排布方式差异很大，但是作用却完全相同。前述 JavaScript 示例也能改写成这种排布方式(但是这段 Python 代码不能改写成 JavaScript 那 种风格，因为 lambda 表达式句法上有限制)。

示例 18-10 和示例 18-11 组织代码的方式导致代码难以阅读，也更难编写：每个函数做一 部分工作，设置下一个回调，然后返回，让事件循环继续运行。这样，所有本地的上下文 都会丢失。执行下一个回调时(例如stage2)，就无法获取request2的值。如果需要

那个值，那就必须依靠闭包，或者把它存储在外部数据结构中，以便在处理过程的不同阶

段使用。

在这个问题上，协程能发挥很大的作用。在协程中，如果要连续执行 3 个异步操作，只需 使用 yield3 次，让事件循环继续运行。准备好结果后，调用 .send() 方法，激活协 程。对事件循环来说，这种做法与调用回调类似。但是对使用协程式异步 API 的用户来 说，情况就大为不同了：3 次操作都在同一个函数定义体中，像是顺序代码，能在处理过 程中使用局部变量保留整个任务的上下文。请看示例 18-12。

示例 18-12 使用协程和 yield from 结构做异步编程，无需使用回调

@asyncio.coroutine

def three_stages(request1):

response1 = yield from api_call1(request1)

\#    第一步

request2 = step1(response1)

response2 = yield from api_call2(request2)

\#    第二步

request3 = step2(response2)

response3 = yield from api_call3(request3)

\#    第三步

step3(response3)

loop.create_task(three_stages(request1)) # 必须显式调度执行

与前面的 JavaScript 和 Python 示例相比，示例 18-12 容易理解多了：操作的 3 个步骤依次

写在同一个函数中。这样，后续处理便于使用前一步的结果；而且提供了上下文，能通过

异常来报告错误。

假设在示例18-11中处理api_call2(request2, stage2)调用(stagel函数最后一 行)时抛出了 I/O 异常，这个异常无法在 stage1 函数中捕获，因为 api_call2 是异步 调用，还未执行任何 I/O 操作就会立即返回。在基于回调的 API 中，这个问题的解决方法 是为每个异步调用注册两个回调，一个用于处理操作成功时返回的结果，另一个用于处理

错误。一旦涉及错误处理，回调地狱的危害程度就会迅速增大。

与此相比，在示例 18-12 中，那个三步操作的所有异步调用都在同一个函数中

(three_stages)，如果异步调用 api_call1、api_call2 和 api_call3 会抛出异

常，那么可以把相应的 yield from 表达式放在 try/except 块中处理异常。

这么做比陷入回调地狱好多了，但是我不会把这种方式称为协程天堂，毕竟我们还要付出

代价。我们不能使用常规的函数，必须使用协程，而且要习惯yield from-这是第一

个障碍。只要函数中有yield from，函数就会变成协程，而协程不能直接调用，即不能 像示例 18-11 中那样调用 api_call1(request1, stage1) 来启动回调链。我们必须使 用事件循环显式排定协程的执行时间，或者在其他排定了执行时间的协程中使用 yield from 表达式把它激活。如果示例 18-12 没有最后一行

(loop.create_task(three_stages(request1)))，那么什么也不会发生。 下面举个例子来实践这个理论。

每次下载发起多次请求

假设保存每面国旗时，我们不仅想在文件名中使用国家代码，还想加上国家名称。那么， 下载每面国旗时要发起两个请求：一个请求用于获取国旗，另一个请求用于获取图像所在 目录里的 metadata.json 文件(记录着国家名称)。

在同一个任务中发起多个请求，这对线程版脚本来说很容易：只需接连发起两次请求，阻

塞线程两次，把国家代码和国家名称保存在局部变量中，在保存文件时使用。如果想在异

步脚本中使用回调做到这一点，你会闻到回调地狱中飘来的硫磺味道：国家代码和名称要

放在闭包中传来传去，或者保存在某个地方，在保存文件时使用，这么做是因为各个回调 在不同的局部上下文中运行。协程和 yield from 结构能缓解这个问题。解决方法虽然没

有使用多个线程那么简单，但是比链式或嵌套回调易于管理。

示例 18-13 是使用 asyncio 包下载国旗脚本的第 3 版，这一次国旗的文件名中有国家名 称。 flags2_asyncio.py 脚本(示例 18-7 和示例 18-8)中的 download_many 函数和 downloader_coro 协程没变，有变化的是下面的内容。

download_one

现在，这个协程使用 yield from 把职责委托给 get_flag 协程和新添的 get_country 协程。 get_flag

这个协程的大多数代码移到新添的 http_get 协程中了，以便也能在 get_country 协程中使用。

get_country

这个协程获取国家代码相应的 metadata.json 文件，从文件中读取国家名称。

http_get

从 Web 获取文件的通用代码。

示例18-13    flags3_asyncio.py：再定义几个协程，把职责委托出去，每次下载国旗

时发起两次请求

@asyncio.coroutine def http_get(url):

res = yield from aiohttp.request('GET', url) if res.status == 200:

ctype = res.headers.get('Content-type', '').lower() if 'json' in ctype or url.endswith('json'):

data = yield from res.json() O else:

data = yield from res.read() © return data

elif res.status == 404:

raise web.HTTPNotFound()

else:

raise aiohttp.errors.HttpProcessingError( code=res.status, message=res.reason, headers=res.headers)

@asyncio.coroutine

def get_country(base_url, cc):

url = '{}/{cc}/metadata.json'.format(base_url, cc=cc.lower()) metadata = yield from http_get(url) © return metadata['country']

@asyncio.coroutine

def get_flag(base_url, cc):

url = '{}/{cc}/{cc}.gif'.format(base_url, cc=cc.lower()) return (yield from http_get(url)) ©

@asyncio.coroutine

def download_one(cc, base_url, semaphore, verbose): try:

with (yield from semaphore):❺

image = yield from get_flag(base_url, cc)

with (yield from semaphore):

country = yield from get_country(base_url, cc) except web.HTTPNotFound:

status = HTTPStatus.not_found msg = 'not found' except Exception as exc:

raise FetchError(cc) from exc else:

country = country.replace(' ', '_') filename = '{}-{}.gif'.format(country, cc) loop = asyncio.get_event_loop()

loop.run_in_executor(None, save_flag, image, filename) status = HTTPStatus.ok msg = 'OK'

if verbose and msg: print(cc, msg)

return Result(status, cc)

❶如果内容类型中包含'json '，或者url以.json结尾，那么在响应上调用.json()

方法，解析响应，返回一个 Python 数据结构——在这里是一个字典。

❷ 否则，使用 .read() 方法读取原始字节。

❸ metadata 变量的值是一个由 JSON 内容构建的 Python 字典。

❹ 这里必须在外层加上括号，如果直接写 return yield from， Python 解析器会不明所 以，报告句法错误。

❺我分别在semaphore控制的两个with块中调用get_flag和get_country，因为我

想尽量缩减下载时间。

在示例 18-13 中， yield from 句法出现了 9 次。现在，你应该已经熟知如何在协程中使

用这个结构把职责委托给另一个协程，而不阻塞事件循环。

问题的关键是，知道何时该使用yield from，何时不该使用。基本原则很简单，yield from 只能用于协程和 asyncio.Future 实例(包括 Task 实例)。可是有些 API 很棘 手，肆意混淆协程和普通的函数，例如下一节实现某个服务器时使用的 StreamWriter 类。

示例 18-13 是本书最后一次讨论 flags2 系列示例。我建议你自己运行那些示例，有助于

对 HTTP 并发客户端的运作方式建立直观认识。你可以使用 -a、 -e 和 -l 这三个命令行

选项控制下载的国旗数量，还可以使用 -m 选项设置并发下载数。此外，还可以分别使用

LOCAL、 REMOTE、 DELAY 和 ERROR 服务器测试，找出能最大限度地利用各个服务器的吞 吐量的并发下载数。如果想去掉错误或延迟，可以修改 vaurien_error_delay.sh 脚本

[( ](https://github.com/fluentpython/example-code/blob/master/17-futures/countries/vaurien_error_delay.sh)[https://github.com/fluentpython/example-code/blob/master/17-](https://github.com/fluentpython/example-code/blob/master/17-futures/countries/vaurien_error_delay.sh)

futures/countries/vaurien_error_delay.sh)中的设置。

客户端脚本到此结束，接下来使用 asyncio 包编写服务器。

### 18.6使用asyncio包编写服务器

演示 TCP 服务器时通常使用回显服务器。我们要构建更好玩一点的示例服务器，用于查 找 Unicode 字符，分别使用简单的 TCP 协议和 HTTP 协议实现。这两个服务器的作用是， 让客户端使用 4.8 节讨论过的 unicodedata 模块，通过规范名称查找 Unicode 字符。图 18-2 展示了在一个 Telnet 会话中访问 TCP 版字符查找服务器所做的两次查询，一次查询

国际象棋棋子字符，一次查询名称中包含“sun”的字符。

| © O O                                            | 4. bash                     |      |
| ------------------------------------------------ | --------------------------- | ---- |
| lontra:charfinder lucianoS telnet localhost 2323 |                             |      |
| Trying 127.0.0.1…                                |                             |      |
| Connected                                        | to localhost.               |      |
| Escape character is 丨A]*.                       |                             |      |
| ?> chess block                                   |                             |      |
| U+265A ★                                         | BLACK CHESS KING            |      |
| U+265B w                                         | BLACK CHESS QUEEN           |      |
| U+265C s                                         | BLACK CHESS ROOK            |      |
| U+2G5D ±                                         | BLACK CHESS BISHOP          |      |
| U+265E    «                                      | BLACK CHESS KNIGHT          |      |
| U+265F i                                         | BLACK CHESS PAWN            |      |
| 6 matches                                        | for fchess black*           |      |
| ?> sun                                           |                             |      |
| U+2600 來                                        | BLACK SUN WITH RAYS         |      |
| U+2609 o                                         | SUN                         |      |
| U+263C                                           | WHITE SUN WITH RAYS         |      |
| U十26CS                                          | SUN BEHIND CLOUD            |      |
| U+2E9C -                                         | OK RADICAL SUN              |      |
| U+2F47 曰                                        | KANGXI RADICAL SUN          |      |
| U+3230 0                                         | PARENTHESIZED IDEOGRAPH SUN |      |
| U+3290 ®                                         | CIRCLED IDEOGRAPH SUN       |      |
| U+C21C                                           | HANGUL SYLLABLE SUN         |      |
| U+1F31E ■，                                      | SUN WITH FACE               |      |
| 10 matches                                       | for 'sunf                   |      |
| ?> ac                                            |                             |      |
| Connection                                       | closed by foreign host.     |      |
| lontra:charfinder lucianoS \|                    |                             |      |

图18-2：在一个Telnet会话中访问tcp_charfinder.py服务器-查询“chess

black”和 “sun”

#### 接下来讨论实现方式。

18.6.1使用asyncio包编写TCP服务器

下面几个示例的大多数逻辑在 charfinder.py 模块中，这个模块没有任何并发。你可以在命 令行中使用 charfinder.py 脚本查找字符，不过这个脚本更为重要的作用是为使用 asyncio 包编写的服务器提供支持。 charfinder.py 脚本的代码在本书的代码仓库中

(<https://github.com/fluentpython/example-code>) 。

charfinder 模块读取 Python 内建的 Unicode 数据库，为每个字符名称中的每个单词建立 索引，然后倒排索引，存进一个字典。例如，在倒排索引中， 'SUN' 键对应的条目是一个

集合(set)，里面是名称中包含'SUN'这个词的10个Unicode字符。9倒排索引保存在 本地一个名为 charfinder_index.pickle 的文件中。如果查询多个单词， charfinder 会计算 从索引中所得集合的交集。

9在 Python3.5 中，新增了 4 个名称中包含’SUN'的 Unicode 字符：U+1F323 (WHITE SUN)、U+1F324 (WHITE SUN WITH SMALL CLOUD)、U+1F325 (WHITE SUN BEHIND CLOUD)和 U+1F326 (WHITE SUN BEHIND CLOUD WITH RAIN)。 ——编者注

下面我们把注意力集中在响应图 18-2 中那两个查询的 tcp_charfinder.py 脚本上。我要对这 个脚本中的代码做大量说明，因此把它分为两部分，分别在示例 18-14 和示例 18-15 中列 出。

示例 18-14 tcp_charfinder.py：使用 asyncio.start_server 函数实现的简易 TCP

服务器；这个模块余下的代码在示例 18-15 中

import sys import asyncio

from charfinder import UnicodeNameIndex O

CRLF = b'\r\n'

PROMPT = b'?> '

index = UnicodeNameIndex() ©

@asyncio.coroutine

def handle_queries(reader, writer): © while True: ©

writer. write (PROMPT)    # 不能使用yield from!❺

yield from writer.drain() # 必须使用yield from!    ©

data = yield from reader.readline() O

try:

query = data.decode().strip() except UnicodeDecodeError:❻

query = '\x00'

if lines:

writer.writelines(line.encode() + CRLF for line in lines)❽ writer.write(index.status(query, len(lines)).encode() + CRLF) ©

yield from writer.drain() ©

print('Sent {} results'.format(len(lines))) ©

print('Close the client socket')    ®

writer.close() ©

❶ UnicodeNameIndex 类用于构建名称索引，提供查询方法。

❷ 实例化 UnicodeNameIndex 类时，它会使用 charfinder_index.pickle 文件（如果有的

话），或者构建这个文件，因此第一次运行时可能要等几秒钟服务器才能启动。 10

10Leonardo Rochael 指出，可以在示例 18-15 中的 main 函数里使用 loop.run_with_executor（） 方法，在另一个线程 中构建 Unicode 名称索引，这样索引构建好之后，服务器能立即开始接收请求。他说得对，不过这个应用的唯一用途 是查询索引，因此那样做没有多大好处。不过， Leo 建议的做法是个不错的练习，有兴趣的话你可以去做。

❸ 这个协程要传给 asyncio.start_server 函数，接收的两个参数是

asyncio.StreamReader 对象和 asyncio.StreamWriter 对象。

❹ 这个循环处理会话，直到从客户端收到控制字符后退出。

❺ StreamWriter.write 方法不是协程，只是普通的函数；这一行代码发送 ?> 提示符。

❻ StreamWriter.drain 方法刷新 writer 缓冲；因为它是协程，所以必须使用 yield from 调用。

❼ StreamReader.readline 方法是协程，返回一个 bytes 对象。

❽ Telnet 客户端发送控制字符时，可能会抛出 UnicodeDecodeError 异常；遇到这种情 况时，为了简单起见，假装发送的是空字符。

❾ 返回与套接字连接的远程地址。

❿ 在服务器的控制台中记录查询。

®如果收到控制字符或者空字符，退出循环。

©返回一个生成器，产出包含Unicode码位、真正的字符和字符名称的字符串（例如， U+0039\t9\tDIGIT NINE）；为了简单起见，我从中构建了一个列表。

®使用默认的UTF-8编码把lines转换成bytes对象，并在每一行末尾添加回车符和换 行符；注意，参数是一个生成器表达式。

© 输出状态，例如 627 matches for 'digit'。11

| 11 在 Python 3.5 中，是 755 matches for 'digit'。-编者注

©刷新输出缓冲。

©在服务器的控制台中记录响应。

®在服务器的控制台中记录会话结束。

© 关闭 StreamWriter 流。

handle_queries 协程的名称是复数，因为它启动交互式会话后能处理各个客户端发来的 多次请求。

注意，示例 18-14 中所有的 I/O 操作都使用 bytes 格式。因此，我们要解码从网络中收到

的字符串，还要编码发出的字符串。Python 3默认使用的编码是UTF-8，这里就隐式使用 了这个编码。

注意一点，有些 I/O 方法是协程，必须由 yield from 驱动，而另一些则是普通的函数。

例如， StreamWriter.write 是普通的函数，我们假定它大多数时候都不会阻塞，因为 它把数据写入缓冲；而刷新缓冲并真正执行 I/O 操作的 StreamWriter.drain 是协 程， StreamReader.readline 也是协程。写作本书时， asyncio 包的 API 文档有重大的

改进，明确标识出了哪些方法是协程。

示例 18-15 接续示例 18-14，列出这个模块的 main 函数。

示例18-15 tcp_charfinder.py （接续示例18-14） : main函数创建并销毁事件循环和

套接字服务器

def main(address='127.0.0.1', port=2323): O port = int(port) loop = asyncio.get_event_loop()

server_coro = asyncio.start_server(handle_queries, address, port, loop=loop) ©

server = loop.run_until_complete(server_coro) ©

host = server.sockets[0].getsockname() ©

print('Serving on {}. Hit CTRL-C to stop.'.format(host)) ❺

try:

loop.run_forever() © except KeyboardInterrupt: # 按CTRL-C键

pass

print('Server shutting down.') server.close() O

loop.run_until_complete(server.wait_closed()) ❻ loop.close() ©

if __name__ == '__main__': main(*sys.argv[1:]) ©

❶ 调用 main 函数时可以不传入参数。

❷ asyncio.start_server 协程运行结束后，返回的协程对象返回一个

asyncio.Server 实例，即一个 TCP 套接字服务器。

❸驱动server_coro协程，启动服务器（server）。

❹获取这个服务器的第一个套接字的地址和端口，然后+ + + + + +

❺+ + + + + +在服务器的控制台中显示出来。这是这个脚本在服务器的控制台中显示的第一个 输出。

❻ 运行事件循环； main 函数在这里阻塞，直到在服务器的控制台中按 CTRL-C 键才会关 闭。

❼ 关闭服务器。

❽ server.wait_closed（） 方法返回一个期物；调用 loop.run_until_complete 方

法，运行期物。

❾ 终止事件循环。

❿这是处理可选的命令行参数的简便方式：展开sys.argv[1:]，传给main函数，未指 定的参数使用相应的默认值。

注意，run_until_complete方法的参数是一个协程（start_server方法返回的结果） 或一个Future对象（server.wait_closed方法返回的结果）。如果传给 run_until_complete 方法的参数是协程，会把协程包装在 Task 对象中。

仔细查看 tcp_charfinder.py 脚本在服务器控制台中生成的输出（如示例 18-16），更易于理 解脚本中控制权的流动。

示例18-16 tcp_charfinder.py：这是图18-2所示会话在服务器端的输出

$ python3 tcp_charfinder.py

Serving on ('127.0.0.1', 2323). Hit CTRL-C to stop. O

| Received from ('127.0.0.1', | 62910): | 'chess | black' © |
| --------------------------- | ------- | ------ | -------- |
| Sent 6 results              |         |        |          |
| Received from ('127.0.0.1', | 62910): | 'sun'  | ©        |
| Sent 10 results             |         |        |          |
| Received from ('127.0.0.1', | 62910): | '\x00' |          |

Close the client socket ❺

❶ 这是 main 函数的输出。

❷ handle_queries 协程中那个 while 循环第一次迭代的输出。

❸ 那个 while 循环第二次迭代的输出。 12

| 12在Python 3.5中是Sent 14 results。参见本小节开头的编者注。 编者注

❹ 用户按下 CTRL-C 键；服务器收到控制字符，关闭会话。

❺ 客户端套接字关闭了，但是服务器仍在运行，准备为其他客户端提供服务。

注意， main 函数几乎会立即显示 Serving on... 消息，然后在调用

loop.run_forever（） 方法时阻塞。在那一点，控制权流动到事件循环中，而且一直待 在那里，不过偶尔会回到 handle_queries 协程，这个协程需要等待网络发送或接收数

据时，控制权又交还事件循环。在事件循环运行期间，只要有新客户端连接服务器就会启

动一个 handle_queries 协程实例。因此，这个简单的服务器可以并发处理多个客户 端。出现 KeyboardInterrupt 异常，或者操作系统把进程杀死，服务器会关闭。 tcp_charfinder.py 脚本利用 asyncio 包提供的高层流

API (<https://docs.python.org/3/library/asyncio-stream.html>) ，有现成的服务器可用，所以我 们只需实现一个处理程序(普通的回调或协程)。此外， asyncio 包受 Twisted 框架中抽

象的传送和协议启发，还提供了低层传送和协议API。详情请参见asyncio包的文档 ( [https://docs.python.org/3/library/asyncio-protocol .html ](https://docs.python.org/3/library/asyncio-protocol.html)) ，里面有一个使用低层 API 实现的

TCP 回显服务器。

下一节实现 HTTP 版字符查找服务器。

18.6.2使用aiohttp包编写Web服务器

asyncio版国旗下载示例使用的aiohttp库也支持服务器端HTTP，我就使用这个库实 现了 http_charfinder.py脚本。图18-3是这个简易服务器的Web界面，显示搜索“cat face”表情符号得到的结果。

U+1F431 0 CAT FACE

U+1F638 GRINNING CAT FACE WITH SMILING EYES

U+1F639 CAT FACE WITH TEARS OF JOY

U+1F63A SMILING CAT FACE WITH OPEN MOUTH

U+1F63B SMILING CAT FACE WITH HEART-SHAPED EYES

U+1F63C CAT FACE WITH WRY SMILE

U+1F63D KISSING CAT FACE WITH CLOSED EYES

U+1F63E POUTING CAT FACE

U+1F63F V CRYING CAT FACE

U+1F640 WEARY CAT FACE

图18-3:浏览器窗口中显示在http_charfinder.py服务器中搜索“cat face”得到的结果

有些浏览器显示Unicode字符的效果比其他浏览器好。图18-3中的截图在 OS X 版 Firefox 浏览器中截取，我在 Safari 中也得到了相同的结果。但是，运行在同 一台设备中的最新版 Chrome 和 Opera 却不能显示猫脸等表情符号。不过其他搜索结 果(例如“chess”)正常，因此这可能是OS X版Chrome和Opera的字体问题。

我们先分析 http_charfinder.py 脚本中最重要的后半部分：启动和关闭事件循环与 HTTP 服 务器。参见示例 18-17。

#### 示例 18-17 http_charfinder.py： main 和 init 函数

@asyncio.coroutine

def init(loop, address, port): O

app = web.Application(loop=loop) © app.router.add_route('GET', '/', home) © handler = app.make_handler() © server = yield from loop.create_server(handler,

address, port) ❺

return server.sockets[0].getsockname() ©

def main(address="127.0.0.1", port=8888): port = int(port) loop = asyncio.get_event_loop()

host = loop.run_until_complete(init(loop, address, port)) O print('Serving on {}. Hit CTRL-C to stop.'.format(host)) try:

loop.run_forever() ❻ except KeyboardInterrupt: # 按CTRL-C键

pass

print('Server shutting down.') loop.close() ©

if __name__ == '__main__': main(*sys.argv[1:])

#### ❶ init 协程产出一个服务器，交给事件循环驱动。

❷ aio[http.web.Application](http://http.web.Application) 类表示 Web 应用++

❸ ++ 通过路由把 URL 模式映射到处理函数上；这里，把 GET / 路由映射到 home 函数

上（参见示例 18-18）。

❹ app.make_handler 方法返回一个 aio[http.web.RequestHandler](http://http.web.RequestHandler) 实例，根据 app

对象设置的路由处理 HTTP 请求。

❺ create_server 方法创建服务器，以 handler 为协议处理程序，并把服务器绑定在指 定的地址（address）和端口（port）上。

#### ❻ 返回第一个服务器套接字的地址和端口。

#### ❼ 运行 init 函数，启动服务器，获取服务器的地址和端口。

#### ❽ 运行事件循环；控制权在事件循环手上时， main 函数会在这里阻塞。

#### ❾ 关闭事件循环。

我们己经熟悉了 asyncio包的API，现在可以对比一下示例18-17与前面的TCP示例 （见示例 18-15），看它们创建服务器的方式有何不同。

#### 在前面的 TCP 示例中，服务器通过 main 函数中的下面两行代码创建并排定运行时间：

server_coro = asyncio.start_server(handle_queries, address, port, loop=loop)

server = loop.run_until_complete(server_coro)

#### 在这个 HTTP 示例中， init 函数通过下述方式创建服务器：

server = yield from loop.create_server(handler,

address, port)

#### 但是 init 是协程，驱动它运行的是 main 函数中的这一行：

host = loop.run_until_complete(init(loop, address, port))

asyncio.start_server 函数和 loop.create_server 方法都是协程，返回的结果都是 asyncio.Server 对象。为了启动服务器并返回服务器的引用，这两个协程都要由他人驱 动，完成运行。在 TCP 示例中，做法是调用

loop.run_until_complete(server_coro)，其中 server_coro 是

asyncio.start_server 函数返回的结果。在 HTTP 示例中， create_server 方法在 init 协程中的一个 yield from 表达式里调用，而 init 协程则由 main 函数中的 loop.run_until_complete(init(...)) 调用驱动。

#### 我提到这一点是为了强调之前讨论过的一个基本事实：只有驱动协程，协程才能做事，而

驱动asyncio.coroutine装饰的协程有两种方法，要么使用yield from，要么传给 asyncio 包中某个参数为协程或期物的函数，例如 run_until_complete。

示例 18-18 列出 home 函数。根据这个 HTTP 服务器的配置， home 函数用于处理 /(根) URL。

示例 18-18 http_charfinder.py： home 函数

def home(request): O

query = request.GET.get('query', '').strip() © print('Query: {!r}'.format(query)) © if query: ©

descriptions = list(index.find_descriptions(query)) res = '\n'.join(ROW_TPL.format(**vars(descr))

for descr in descriptions) msg = index.status(query, len(descriptions))

else:

descriptions = [] res = ''

msg = 'Enter words describing characters.'

html = template.format(query=query, result=res,❺ message=msg)

print('Sending {} results'.format(len(descriptions)))    ©

return web.Response(content_type=CONTENT_TYPE, text=html) ©

❶ 一个路由处理函数，参数是一个 aio[http.web.Request](http://http.web.Request) 实例。

❷ 获取查询字符串，去掉首尾的空白。

❸ 在服务器的控制台中记录查询。

❹如果有查询字符串，从索引(index)中找到结果，使用HTML表格中的行渲染结

果，把结果赋值给 res 变量，再把状态消息赋值给 msg 变量。

❺ 渲染 HTML 页面。

❻ 在服务器的控制台中记录响应。

❼ 构建 Response 对象，将其返回。

注意， home 不是协程，既然定义体中没有 yield from 表达式，也没必要是协程。在 aiohttp 包的文档中， add_route 方法的条目

( [http://aiohttp.readthedocs.org/en/v0. 1 4.4/web_reference.html#aiohttp.web.UrlDispatcher.add_ro](http://aiohttp.readthedocs.org/en/v0.14.4/web_reference.html%23aiohttp.web.UrlDispatcher.add_route)

下面说道， “如果处理程序是普通的函数，在内部会将其转换成协程”。

示例 18-18 中的 home 函数虽然简单，却有一个缺点。 home 是普通的函数，而不是协程， 这一事实预示着一个更大的问题：我们需要重新思考如何实现 Web 应用，以获得高并 发。下面来分析这个问题。

18.6.3 更好地支持并发的智能客户端

示例 18-18 中的 home 函数很像是 Django 或 Flask 中的视图函数，实现方式完全没有考虑 异步：获取请求，从数据库中读取数据，然后构建响应，渲染完整的 HTML 页面。在这 个示例中，存储在内存中的 UnicodeNameIndex 对象是“数据库”。但是，对真正的数据 库来说，应该异步访问，否则在等待数据库查询结果的过程中，事件循环会阻塞。例

如，aiopg 包(<https://aiopg.readthedocs.org/en/stable/>)提供了一个异步 PostgreSQL 驱动，

与 asyncio 包兼容；这个包支持使用 yield from 发送查询和获取结果，因此视图函数 的表现与真正的协程一样。

除了防止阻塞调用之外，高并发的系统还必须把复杂的工作分成多步，以保持敏捷。

http_charfinder.py服务器表明了这一点：如果搜索“cjk”，得到的结果是75 821个中文、日 文和韩文象形文字。 13 此时， home 函数会返回一个 5.3MB 的 HTML 文档，显示一个有 75 821 行的表格。

13这正是CJK表示的意思：不断增加的中文、日文和韩文字符。以后的Python版本支持的CJK象形文字数量可能会 比 Python 3.4 多。

我在自己的设备中使用命令行 HTTP 客户端 curl 访问架设在本地的 http_charfinder.py 服 务器，查询“cjk”，2秒钟后获得响应。浏览器要布局包含这么大一个表格的页面，用的时 间会更长。当然，大多数查询返回的响应要小得多：查询“braille”返回256行结果，页面 大小为19KB，在我的设备中用时0.017秒。可是，如果服务器要用2秒钟处理“cjk”查 询，那么其他所有客户端都至少要等 2 秒——这是不可接受的。

避免响应时间太长的方法是实现分页：首次至多返回(比如说)200 行，用户点击链接或 滚动页面时再获取更多结果。如果查看本书代码仓库

(<https://github.com/fluentpython/example-code>)中的 charfinder.py 模块，你会发现 UnicodeNameIndex.find_descriptions 方法有两个可选的参数-start 和 stop，

这是偏移值，用于支持分页。因此，我们可以返回前 200 个结果，当用户想查看更多结果 时，再使用 AJAX 或 WebSockets 发送下一批结果。

实现分批发送结果所需的大多数代码都在浏览器这一端，因此 Google 和所有大型互联网

公司都大量依赖客户端代码构建服务：智能的异步客户端能更好地使用服务器资源。

虽然智能的客户端甚至对老式 Django 应用也有帮助，但是要想真正为这种客户端服务， 我们需要全方位支持异步编程的框架，从处理 HTTP 请求和响应到访问数据库，全都支持 异步。如果想实现实时服务，例如游戏和以 WebSockets 支持的媒体流，那就尤其应该这

么做。 14

14在“杂谈”中我会进一步说明这个趋势。

这里留一个练习给读者：改进 http_charfinder.py 脚本，添加下载进度条。此外还有一个附 加题：实现 Twitter 那样的“无限滚动”。做完这个练习后，我们对如何使用 asyncio 包做 异步编程的讨论就结束了。

### 18.7 本章小结

本章介绍了在Python中做并发编程的一种全新方式，这种方式使用yield from、协程、 期物和 asyncio 事件循环。首先，我们分析了两个简单的示例——两个旋转指针脚本， 仔细对比了使用 threading 模块和 asyncio 包处理并发的异同。

然后，本章讨论了 asyncio.Future 类的细节，重点讲述它对 yield from 的支持，以 及与协程和 asyncio.Task 类的关系。接下来分析了 asyncio 版国旗下载脚本。

然后，本章分析了 Ryan Dahl 对 I/O 延迟所做的统计数据，还说明了阻塞调用的影响。尽

管有些函数必然会阻塞，但是为了让程序持续运行，有两种解决方案可用：使用多个线

程，或者异步调用——后者以回调或协程的形式实现。

其实，异步库依赖于低层线程（直至内核级线程），但是这些库的用户无需创建线程，也

无需知道用到了基础设施中的低层线程。在应用中，我们只需确保没有阻塞的代码，事件

循环会在背后处理并发。异步系统能避免用户级线程的开销，这是它能比多线程系统管理

更多并发连接的主要原因。

之后，我们又回到下载国旗的脚本，添加进度条并处理错误。这需要大幅度重构，特别是

要把 asyncio.wait 函数换成 asyncio.as_completed 函数，因此不得不把 download_many 函数的大多数功能移到新添的 downloader_coro 协程中，这样我们才 能使用 yield from 从 asyncio.as_completed 函数生成的多个期物中逐个获得结果。

然后，本章说明了如何使用 loop.run_in_executor 方法把阻塞的作业（例如保存文

件）委托给线程池做。

接着，本章讨论了如何使用协程解决回调的主要问题：执行分成多步的异步任务时丢失上

下文，以及缺少处理错误所需的上下文。

然后又举了一个例子，在下载国旗图像的同时获取国家名称，以此说明如何结合协程和

yield from 避免所谓的回调地狱。如果忽略 yield from 关键字，使用 yield from 结

构实现异步调用的多步过程看起来类似于顺序执行的代码。

本章最后两个示例是使用 asyncio 包实现的 TCP 和 HTTP 服务器，用于按名称搜索

Unicode 字符。在分析 HTTP 服务器的最后，我们讨论了客户端 JavaScript 对服务器端提 供高并发支持的重要性。使用JavaScript，客户端可以按需发起小型请求，而不用下载较

大的 HTML 页面。

### 18.8 延伸阅读

Python 核心开发者 Nick Coghlan 在 2013 年 1 月对“PEP 3156—Asynchronous IO Support Rebooted: the‘asyncio'Module” ( [https://www.python.org/dev/peps/pep-3 1 56/](https://www.python.org/dev/peps/pep-3156/))草案评论如

下：

在这个 PEP 的开头部分应该言简意赅地说明等待异步期物返回结果的两个 API：

(1)    f.add_done_callback(...)

(2)    协程中的yield from f (期物运行结束后恢复协程，期物要么返回结果，要么

抛出合适的异常)

此刻，这两个 API 深埋在众多的 API 中，而它们是理解核心事件循环层之上各种事

物交互方式的关键。 15

I[15](https://mail.python.org/pipermail/python-ideas/2013-January/018791.html)[摘自 2013 年 1 月 20 日发布在 python-ideas 邮件列表中的一个消息(https://maiIpython.org/pipermail/python-ideas/2013-](https://mail.python.org/pipermail/python-ideas/2013-January/018791.html)January/018791.html)，在这个消息中，Coghlan对PEP 3156做出了上述评论。

PEP 3156 的作者 Guido van Rossum 没有采纳 Coghlan 的建议。实现 PEP 3156 的初 期， asyncio 包的文档虽然十分详细，但对用户并不友好。 asyncio 包的文档有 9 个 .rst 文件，128KB，将近71页。在标准库文档中，只有“Built-in Types”一章

(<https://docs.python.org/3/library/stdtypes.html>)有这么长，而那一章内容众多，涵盖了数

字类型、序列类型、生成器、映射、集合、bool、上下文管理器，等等。

asyncio包的文档大部分是在讲概念和API，其中夹杂着有用的示意图和示例，不过特别 实用的一节是“18.5.11. Develop with asyncio” ([https://docs.python.org/3/library/asyncio-dev.html](https://docs.python.org/3/library/asyncio-dev.html)%ef%bc%8c)[)，](https://docs.python.org/3/library/asyncio-dev.html)%ef%bc%8c)[ 16 其中说明了极为重要的使用模式。 asyncio 包的文档需要用更多的内](https://docs.python.org/3/library/asyncio-dev.html)容来 说明如何使用 asyncio。

| 16 目前是：18.5.9. Develop with asyncio。-编者注

asyncio 包很新，已出版的书中少有涉及。我发现只有 Jan Palach 写的 Parallel Programming with Python (Packt 出版社，2014 年)一书中有一章讲到了 asyncio，可惜

那一章很短。

不过，有很多关于 asyncio 的精彩演讲。我觉得最棒的是 Brett Slatkin 在蒙特利尔 PyCon 2014 大会上发表的演讲，题为“Fan-In and Fan-Out: The Crucial Components of Concurrency”([https://speakerdeck.com/pycon2014/fan-in-and-fan-out-the-crucial-components-of-concurrency-by-brett-slatkin](https://speakerdeck.com/pycon2014/fan-in-and-fan-out-the-crucial-components-of-concurrency-by-brett-slatkin)%ef%bc%8c%e5%89%af%e6%a0%87%e9%a2%98%e6%98%af%e2%80%9cWhy)[)，副标题是“Why](https://speakerdeck.com/pycon2014/fan-in-and-fan-out-the-crucial-components-of-concurrency-by-brett-slatkin)%ef%bc%8c%e5%89%af%e6%a0%87%e9%a2%98%e6%98%af%e2%80%9cWhy)[ do we need Tulip? (a.k.a., PEP 3156—](https://speakerdeck.com/pycon2014/fan-in-and-fan-out-the-crucial-components-of-concurrency-by-brett-slatkin) asyncio)” (视频：[https://www+youtube.com/watch?v=CWmq-jtkemY](https://www.youtube.com/watch?v=CWmq-jtkemY))。在 30 分钟内，

Slatkin 实现了一个简单的 Web 爬虫示例，强调了 asyncio 包的正确用法。身为观众的 Guido van Rossum 提到，为了引荐 asyncio 包，他也写了一个 Web 爬虫。 Guido 写的代码 不依赖 aiohttp 包，只用到了标准库。 Slatkin 还写了一篇见解深刻的文章，题 为“Python's asyncio Is for Composition, Not Raw

Performance”([http://www.onebigfluke.com/2015/02/asynci o-is-for-composition.html](http://www.onebigfluke.com/2015/02/asyncio-is-for-composition.html)) 。

Guido van Rossum 自己的几个演讲也是必看的，包括在 PyCon US 2013 上所做的主题演讲 (<http://pyvideo.org/video/1667/keynote-1>)，以及在 LinkedIn 公司 ([https://www+youtube.com/watch?v=aurOB4qYuFM](https://www.youtube.com/watch?v=aurOB4qYuFM))和 Twitter 大学

(<https://www.youtube.com/watch?v=1coLC-MUCJc>)所做的演讲。此外，还推荐 Saul Ibarra

Corretg^的演讲-“A Deep Dive into PEP-3156 and the New asyncio Module”[(幻灯片

[(](https://www.youtube.com/watch?v=MS1L2RGKYyY)[http://www+slideshare+net/saghul/asyncio)，视步页(https://www+youtube+com/watch?](https://www.youtube.com/watch?v=MS1L2RGKYyY)

v=MS1L2RGKYyY) ]。

在 PyCon US 2013 大会上，Dino Viehland 做了一场演讲，题为“Using futures for async GUI programming in Python 3+3” (http://pyvideo+org/video/1762/using-futures-for-async-gui-programming-in-python)，说明如何把[ ](http://pyvideo.org/video/1762/using-futures-for-async-gui-programming-in-python)[asyncio 包集成到 Tkinter 事件循环中。Vie](http://pyvideo.org/video/1762/using-futures-for-async-gui-programming-in-python)hland 展 示了在另一个事件循环之上实现 asyncio.AbstractEventLoop 接口的重要部分是多么 容易。他的代码使用 Tulip 编写，这是 asyncio 包添加到标准库中之前的名称。我修改了 他的代码，以便支持 Python 3+4 中的 asyncio 包。我重构后的新版在 GitHub 中

([https://github+com/fluentpython/asyncio-tkinter](https://github.com/fluentpython/asyncio-tkinter)) 。

Victor Stinner [asyncio 包的核心贡献者， asyncio 包的移植版

Trollius ([http://trollius+readthedocs+org](http://trollius.readthedocs.org))的作者]经常更新相关资源的链接列表-“The

new Python asyncio module aka‘tulip'” ([http://haypo-notes+readthedocs+org/asyncio+html](http://haypo-notes.readthedocs.org/asyncio.html)) 。此 外，收集 asyncio 资源的还有 Asyncio+org 网站([http://asyncio+org/](http://asyncio.org/))和 GitHub 中的 aio-libs 组织([https://github+com/aio-libs](https://github.com/aio-libs))，在这两个网站中能找到 PostgreSQL、MySQL 和多 种 NoSQL 数据库的异步驱动。我没有测试过这些驱动，不过写作本书时，这些项目好像 十分活跃。

Web 服务将成为 asyncio 包的重要使用场景。你的代码有可能要依赖 Andrew Svetlov 领 衔开发的aiohttp库([http://aiohttp+readthedocs+org/en/](http://aiohttp.readthedocs.org/en/))。你可能还想架设环境，测试错 误处理代码，在这方面，Alexis M^taireau和Tarek Ziad^开发的Vaurien (“混沌TCP代 理”，[http://vaurien+readthedocs+org/en/1+8/](http://vaurien.readthedocs.org/en/1.8/))极其有用。Vaurien 是为 Mozilla Services 项目

([https://mozilla-services+github+io/](https://mozilla-services.github.io/))开发的，用于在程序与后端服务器(例如，数据库和

Web 服务提供方)之间的 TCP 流量中引入延迟和随机错误。

杂谈

至尊循环

有很长一段时间，大多数 Python 高手开发网络应用时喜欢使用异步编程，但是总会 遇到一个问题——挑选的库之间不兼容。 Ryan Dahl 提到， Twisted 是 Node+js 的灵感 来源之一；而在 Python 中， Tornado 拥护使用协程做面向事件编程。

在 JavaScript 社区里还有争论，有些人推崇使用简单的回调，而有些人提倡使用与回 调处于竞争地位的各种高层抽象方式。 Node+js 早期版本的 API 使用的是 Promise 对 象(类似于 Python 中的期物)，但是后来 Ryan Dahl 决定统一只用回调。 James Coglan 认为， Node+js 在这一点上错过了大好良机

[(](https://blog.jcoglan.com/2013/03/30/callbacksare-imperative-promises-are-functional-nodes-biggest-missed-opportunity/)[https://blog+jcoglan+com/2013/03/30/callbacksare-imperative-promises-are-functional-](https://blog.jcoglan.com/2013/03/30/callbacksare-imperative-promises-are-functional-nodes-biggest-missed-opportunity/)

nodes-biggest-missed-opportunity/)。

Python 社区的争论己经结束： asyncio 包添加到标准库中之后，协程和期物被确定为 符合 Python 风格的异步代码编写方式。此外， asyncio 包为异步期物和事件循环定

义了标准接口，为二者提供了实现参考。

正如“Python之禅”所说：

肯定有一种——通常也是唯一一种——最佳的解决方案

不过这并不容易找到，因为你不是 Python 之父

或许变成荷兰人才能理解 yield from 吧。 17 对我这个巴西人来说，一开始并不易 于理解，不过一段时间之后我理解了。

更重要的是，设计 asyncio 包时考虑到了使用外部包替换自身的事件循环，因此才 有 asyncio.get_event_loop 和 set_event_loop 函数——二者是抽象的事件循环 [策略 ](https://docs.python.org/3/library/asyncio-eventloops.html%23event-loop-policies-and-the-default-policy)[API(https://docs.python.org/3/library/asyncio-eventloops.html#event-loop-policies-](https://docs.python.org/3/library/asyncio-eventloops.html%23event-loop-policies-and-the-default-policy)and-the-default-policy)的一部分。

Tornado 己经有实现 asyncio.AbstractEventLoop 接口的类

——AsyncIOMainLoop(<http://tornado.readthedocs.org/en/latest/asyncio.html>) ，因此在 同一个事件循环中可以使用这两个库运行异步代码。此外， Quamash 项目

(<https://pypi.python.org/pypi/Quamash/>)也很有趣，它把 asyncio 包集成到 Qt 事件循 环中，以便使用 PyQt 或 PySide 开发 GUI 应用。我只是举两个例子，说明 asyncio 包能把面向事件的包集成在一起。

智能的HTTP客户端，例如单页Web应用(如Gmail)或智能手机应用，需要快速、 轻量级的响应和推送更新。鉴于这样的需求，服务器端最好使用异步框架，不要使用 传统的Web框架(如Django)。传统框架的目的是渲染完整的HTML网页，而且不

支持异步访问数据库。

WebSockets 协议的作用是为始终连接的客户端(例如游戏和流式应用)提供实时更 新，因此，高并发的异步服务器要不间断地与成百上千个客户端交互。 asyncio 包 的架构能很好地支持Web Sockets，而且至少有两个库己经在asyncio包的基础上实 现了 WebSockets 协议：Autobahn|Python (<http://autobahn.ws/python/>)和

WebSockets (<http://aaugustin.github.io/websockets/>) 。

“实时Web”的整体发展趋势迅猛，这是Node.js需求量不断攀升的主要因素，也是 Python 生态系统积极向 asyncio 靠拢的重要原因。不过，要做的事还有很多。为了 便于入门，我们要在标准库中提供异步HTTP服务器和客户端API，异步数据库API 3 .0 ( <https://www.python.org/dev/peps/pep-0249/>) ， 18 以及使用 asyncio 包构建的新 数据库驱动。

与 Node.js 相比，含有 asyncio 包的 Python 3.4 最大的优势是 Python 本身： Python 语 言设计良好，使用协程和 yield from 结构编写的异步代码比 JavaScript 采用的古老 回调易于维护。而我们最大的劣势是库， Python 自带了很多库，但是那些库不支持异 步编程。 Node.js 库的生态系统丰富，完全建构在异步调用之上。但是， Python 和 Node. js 都有一个问题，而 Go 和 Erlang 从一开始就解决了这个问题：我们编写的代

码无法轻松地利用所有可用的 CPU 核心。

Python 标准化了事件循环接口，还提供了一个异步库，这是一大进步，而且只有我们 仁慈的独裁者能在众多深入人心且高质量的替代方案中选择这种方式。具体实现时，

他咨询了多个重要的 Python 异步框架的作者，其中受 Glyph Lefkowitz (Twisted 的主 要开发者)的影响最深。如果你想知道为什么 asyncio.Future 类与 Twisted 中的 Deferred 类不同，一定要阅读 Guido 在 Python-tulip 讨论组中发布的一篇文章，题 为“Deconstructing Deferred”( [https://groups.google.com/forum/#!msg/python-tulip/ut4vTG-08k8/PWZzUXX9HYIJ](https://groups.google.com/forum/%23!msg/python-tulip/ut4vTG-08k8/PWZzUXX9HYIJ)[) 。 Guido 对 Twisted 这个最古老也是最大的 Python 异步框架充](https://groups.google.com/forum/%23!msg/python-tulip/ut4vTG-08k8/PWZzUXX9HYIJ) 满敬意，在python-twisted讨论组中讨论设计方案时，他甚至说，“What Would Twisted Do( WWTD) ”。 19

幸好有 Guido van Rossum 打头阵，让 Python 以更好的姿态应对当前的并发挑战。若 想精通 asyncio 包，一定要下一番功夫。可是，如果你计划使用 Python 编写并发网 络应用，那就去寻求至尊循环(the One Loop):

至尊循环驭众生，至尊循环寻众生，

至尊循环引众生，普照众生欣欣荣。

| 17Python之父Guido van Rossum是荷兰人。-译者注

| 18应该是：PEP 249—Python Database API Specification v2.0。-编者注

[19](https://groups.google.com/forum/%23!msg/python-tulip/pPMwts-CvUcw/eIoX_n8FSPwJ)[ ](https://groups.google.com/forum/%23!msg/python-tulip/pPMwts-CvUcw/eIoX_n8FSPwJ)[出自 Guido 于 2015 年 1 月 29 日发布的消息(https://groups. google.c om/forum/#! msg/python-tulip/pPMwts-](https://groups.google.com/forum/%23!msg/python-tulip/pPMwts-CvUcw/eIoX_n8FSPwJ)CvUcw/eIoX_n8FSPwJ) ，然后 Glyph 立即回复了这一消息。

第六部分 元编程
