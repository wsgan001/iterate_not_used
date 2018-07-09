---
author: evo
comments: true
date: 2018-03-22 06:17:46+00:00
layout: post
link: http://106.15.37.116/2018/03/22/python-decorator/
slug: python-decorator
title: python中的装饰器
wordpress_id: 535
categories:
- 随想与反思
tags:
- '@want_to_know'
- python
---

<!-- more -->


## 缘由：


之前在学Django的时候经常能看到函数上面写了比如 @.... 这样的东西，之前知道是装饰器，但是具体是怎么实现的现在才知道，而且看了一下之后觉得确实好，因此总结一下：


## 要点：




### 1.装饰器的原理及用法：


装饰器，顾名思义，它就是要对被装饰的对象进行一些修改，通过再包装来达到不一样的效果，它被发明出来是帮助我们更高效更简洁的编写代码，对其他方法或类进行装饰。例如对接收数据的检校，我们只要写好一个校验方法，便可以在其他许多方法前作为装饰器使用。

最简单的装饰器：

    
    def decorator(func):
        func()
        print('this is decorator')
    
    @decorator
    def target():
        print('this is target')
    
    target


输出：

    
    this is target
    this is decorator


如果单凭理解，会觉得，大概是target被传到decorator里面，然后执行decorator，大概是这个过程。

但是上面的有个问题：**为什么是target？不是target() ？**

如果写成target() 的话输出：

    
    this is target
    this is decorator
    Traceback (most recent call last):
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2881, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
      File "<ipython-input-41-f7e848e813c8>", line 9, in <module>
        target()
    TypeError: 'NoneType' object is not callable


为什么会这样？target为什么是NoneType ?不是函数吗？为什么不能调用了？

这个就是装饰器的比较核心的一个点：

target和target()，也就是说真正的差别在于()这个括号。当()被附加在方法或者类后面时，表示调用，或者称为运行及实例化，无论称呼怎样，本质意义没有不同，都是调用给出的对象，当对象不具备调用性的时候，就会报错：'某个类型' object is not callable。当一个方法被调用后，即target()，是否能被再次执行，取决于它是否会return一个对象，并且该对象可以被调用。也许你会有点迷糊，对比一下代码会比较容易理解我想表达的意思：

    
    def for_return():
        print('this is for_return')
    
    
    def func():
        print('this is func')
        return for_return
    
    
    print(func)
    print()
    print(func())
    print()
    print(func()())
    print()
    print(func()()())


输出：

    
    <function func at 0x000002D288CE67B8>
    
    this is func
    <function for_return at 0x000002D288723E18>
    
    this is func
    this is for_return
    None
    
    this is func
    this is for_return
    Traceback (most recent call last):
      File "E:/01.Learn/01.Python/01.PythonBasic/e5.py", line 13, in <module>
        print(func()()())
    TypeError: 'NoneType' object is not callable


可见：



 	
  * func输出的是func这个函数在内存中的位置。

 	
  * func()输出的是对这个函数执行的结果，同时print出返回的for_return函数在内存中的地址

 	
  * func()()输出的是func这个函数的实行结果，同时对for_return 函数进行了执行

 	
  * func()()() 在上面的基础上还想进行执行，但是现在已经没有什么可以执行了，因此报出TypeError


即，每层()对应的都是对一个函数进行执行。

当你调用被装饰方法target时，其实首先被执行的是作为装饰器的decorator函数，然后会把target方法作为参数传进去，即：

 	
  1. 首先调用decorator方法：decorator()

 	
  2. 因为decorator方法含1个参数，因此将target传入:decorator(target)

 	
  3. 运行代码“func()”，根据传入的参数，实际执行target()，结果打印出：this is target

 	
  4. 运行代码"print('this is decorator')"，结果打印出：this is decorator


而 target() 等同于： target()==decorator(target)()  ， 开始时执行与上面的运行情况完全相同，接下来便是执行target没有的()，也就是执行调用命令。而由于decorator(target)没有返回一个可以被调用的对象，因此报错：'NoneType' object is not callable

因此这就是写成target而不是target()的原因。但这样的写法会带来一些困惑，这个困惑就是通过我们编写的decorator装饰器对target进行装饰后，将target变成了一个永远不能被调用的方法，或者说变成了一个调用就报错的方法。这跟我们的使用习惯以及对方法的认识是很不协调的，所以为了满足我们对方法的定义，最好将作为装饰器的方法写成一个可以返回具有被调用能力的对象的方法。是的：

    
    def decorator(func):
        def restructure():
            func()
            print('this is decorator')
    
        return restructure  # 注意这个地方是想返回一个可以被添加() 来进行执行的东西，因此这里不能加()
    
    
    @decorator
    def target():
        print('this is target')
    
    
    target()


输出：

    
    this is target
    this is decorator


从最外层讲，以上代码其实只有两个方法，decorator和target，即装饰和被装饰方法。但在decorator内部内嵌了一个方法restructure，这个内嵌的方法才是真正改造target的东西，而decorator其实只是负责将target传入。

当decorator完成初始化，应该return一个可调用对象，也就是restructure方法，这个方法就是替代target的克隆人，在restructure中你可以对target进行重写，或其他代码来包装target。因此你只是想初始化target的话（实际就是对restructure初始化），就应将你要初始化的代码写入到restructure内部去。

OK，普通的装饰器基本上OK了，那么怎么装饰带有参数的函数呢？

代码如下：

    
    def decorator(func):
        #print(x)  #这个地方可以确认是不是在初始化decorator的时候就传进了x
        def restructure(x):
            func(x)
            print('this is decorator %s' % x)
    
        return restructure
    
    
    @decorator
    def target(x):
        print('this is target %s' % x)
    
    
    target('!')


输出：

    
    this is target !
    this is decorator !


执行这种被装饰的带有参数的函数的时候，先初始化decorator，这时只有target方法被传入，其参数x='!'并没有传入。

即等同于：  target(x) == decorator(target)(x) == restructure(x)

所以，可以很清楚地了解到，作为target的参数，其实不是传给decorator，而是传给初始化完decorator之后return的restructure。正因为如此，你必须保证初始化decorator之后返回的对象restructure方法形参与被装饰的target方法形参相匹配，即：

如果定义为：def target(x)
则装饰器中：def restructure(x)

如果定义为：def target(x,y)
则装饰器中：def restructure(x,y)

如果想支持不定数量的参数怎么办？

    
    def decorator(func):
        def restructure(*x):
            func(*x)
            print('this is decorator')
    
        return restructure
    
    
    @decorator
    def target(x):
        print('this is target %s' % x)
    
    
    @decorator
    def newtarget(x, y):
        print('this is target %s%s' % (x, y))
    
    
    target('!')
    newtarget('!', '?')


输出：

    
    this is target !
    this is decorator
    this is target !?
    this is decorator


利用python的带星号参数语法(*arg)，你便可以传入任意数量的参数，你也可以设置带双星号的形参(**arg)，便可以传入字典形式的参数，单星形参和双星形参可以同时使用，如：def restructure(*arg, **arg)。

OK，带参数的函数进行装饰的方法OK了，那么带参数的装饰器怎么写呢？

    
    def decorator_with_para(i):
        def decorator(func):
            def restructure(x):
                func(x)
                print('this is decorator %s%s' % (i, x))
    
            return restructure
    
        return decorator
    
    
    @decorator_with_para('?')
    def target(x):
        print('this is target %s' % x)
    
    
    target('!')


输出：

    
    this is target !
    this is decorator ?!


当执行target('!')的时候等同于：

target('!')==decorator_with_para('?')**(target)**('!')==decorator(target)('!')==resturature('!')

即，**不管什么时候，先执行@后面的，然后 (target) 然后(x) 。**

这个地方要注意的一点：**由于装饰器带参数，因此是三层的，因为最外一层要把装饰器的参数传进去，如果装饰器没有参数，就只有两层，最外面的参数就是func。**

OK，到这里，普通的函数的装饰器，带参数的函数的装饰器，带参数的装饰器 这三种都OK了，**但是现在想知道这三种装饰器分别用在什么情况下？**

两个decorate的时候是什么样的？

    
    def decorate1(func):
        def wrapper():
            func()
            print("this is decorate1")
        return wrapper
    def decorate2(func):
        def wrapper():
            func()
            print("this is decorate2")
        return wrapper
    
    @decorate1
    @decorate2
    def hello():
        print('hello')
    
    hello()


输出：

    
    hello
    this is decorate2
    this is decorate1


即：hello()==decorate1(decorate2(hello))，同样是这样依次写下来即可。


### 2.一个网页编程的例子：



    
    # 注意，这个args和kwds是对应的装饰器的参数，
    def makeHtmlTag(tag, *args, **kwds):
        def real_decorator(fn):
            # 这个args_w和kwds_w是对应的hello传过来的参数，而这个参数后续还是要回到fn里面去的
            # 这个与上面的args和kwds不要弄混了。
            def wrapped(*args_w, **kwds_w):
                # 在wrapped里面仍然可以使用makeHemlTag传进来的args和kwds的参数
                css_class = " class='{0}'".format(kwds["css_class"]) if "css_class" in kwds else ""
                return "<" + tag + css_class + ">" + fn(*args_w, **kwds_w) + "</" + tag + ">"
    
            return wrapped
    
        return real_decorator
    
    
    @makeHtmlTag(tag="b", css_class="bold_css")
    @makeHtmlTag(tag="i", css_class="italic_css")
    def hello(name):
        return "hello world " + name
    
    
    print(hello("Herry"))


输出：

    
    <b class='bold_css'><i class='italic_css'>hello world Herry</i></b>


为什么里面要又一个read_decorater？不是一个wrapped就可以吗？因为最外层实际上是用来处理装饰器的参数的，里面两层才是decorator和wrapper。
















在上面这个例子中，我们可以看到：makeHtmlTag有两个参数。所以，为了让 hello = makeHtmlTag(arg1, arg2)(hello) 成功，makeHtmlTag 必需返回一个decorator（这就是为什么我们在makeHtmlTag中加入了real_decorator()的原因），因为只有decorator才是用来把func作为参数传进去的。


















### 3.decorate 写成一个class


一个简单的例子：

    
    class MyDecorator(object):
        def __init__(self, fn):
            print("inside MyDecorator.__init__()")
            self.fn = fn
    
        def __call__(self):
            print("inside MyDecorator.__call__()")
            self.fn()#而在call里面才是调用的fn
    
    
    
    @MyDecorator# 这个地方就构造了MyDecorator的一个类，因此执行了__init__
    def func():
        print("inside func()")
    
    
    print("Finished decorating func()")
    
    func()# 执行的时候实际上是执行了__call__


输出：

    
    inside MyDecorator.__init__()
    Finished decorating func()
    inside MyDecorator.__call__()
    inside func()


将之前的网页的例子改为class：

    
    class makeHtmlTagClass(object):
        def __init__(self, tag, css_class=""):
            print('makeHemlTagClass __init__ ', tag, css_class)
            self._tag = tag
            self._css_class = " class='{0}'".format(css_class) if css_class != "" else ""
    
        def __call__(self, fn):
            print('makeHemlTagClass __call__ ', self._tag, self._css_class)
            # args和kwargs是hello传进来的参数
            def wrapped(*args, **kwargs):
                print('wrapped ', self._tag, self._css_class)
                return "<" + self._tag + self._css_class + ">" \
                       + fn(*args, **kwargs) \
                       + "</" + self._tag + ">"
    
            return wrapped
    
    
    @makeHtmlTagClass(tag="b", css_class="bold_css")
    @makeHtmlTagClass(tag="i", css_class="italic_css")
    def hello(name):
        return "Hello, {}".format(name)
    
    
    print(hello("Herry"))  # 先调用到__call__


输出：

    
    makeHemlTagClass __init__  b bold_css
    makeHemlTagClass __init__  i italic_css
    makeHemlTagClass __call__  i  class='italic_css'
    makeHemlTagClass __call__  b  class='bold_css'
    wrapped  b  class='bold_css'
    wrapped  i  class='italic_css'
    <b class='bold_css'><i class='italic_css'>Hello, Herry</i></b>


注：**这个地方的顺序还有些不是很清楚也没有很理解**


### 4.装饰器的副作用及对应


因为decorator的因素，我们原本的函数其实已经变成了一个叫wrapper函数。比如，你再调用__name__的时候，他会告诉你，这是 wrapper, 而不是 foo 或者 hello。

所以，Python的functool包中提供了一个叫wrap的decorator来消除这样的副作用：

    
    from functools import wraps
    
    
    def decorator(fn):
        @wraps(fn)
        def wrapper():
            print("hello, %s" % fn.__name__)
            fn()
            print("goodby, %s" % fn.__name__)
    
        return wrapper
    
    
    @decorator
    def foo():
        '''foo help doc'''
        print("i am foo")
    
    
    foo()
    print(foo.__name__)
    print(foo.__doc__)


输出如下：

    
    hello, foo
    i am foo
    goodby, foo
    foo
    foo help doc




### 5.装饰器的一个经典的例子：


斐波那契数列：[斐波那契](http://106.15.37.116/2018/03/19/fib/)

    
    from functools import wraps
    
    
    def memo(fn):
        # cache这个字典用来存放之前已经计算出来的东西
    
        cache = {}
        print('in memo ', cache)
    
        @wraps(fn)
        def wrapper(*args):
            print('in wrapper ', *args)
            # None为我们设置的默认值
            result = cache.get(args, None)
            if result is None:
                # 这个时候调用的fn会触发memo吗？为什么不会？
                # 如果是不会触发的话，那么的确达到效果了，因为结果被存放到memo的cache里面了
                result = fn(*args)
                cache[args] = result
            # 如果存在就直接返回查到的结果
            return result
    
        return wrapper
    
    
    @memo
    def fib(n):
        if n < 2:
            return n
        return fib(n - 1) + fib(n - 2)
    
    
    # 可见memo里面的初始化只会被触发一次，所以memo里面的cache的初始化只会执行一次，这样的话只会有这一个cache，
    # 所有的计算结果都保存到了cache里面，
    # 后续再进行计算的时候，前面的计算过的内容也是有用的，这样就大大节省了时间。
    # memo里面的初始化虽然只会调用一次，但是wrapper还是每次都会被调用的，这个感觉有些神奇。
    print(fib(20))
    print(fib(10))
    print(fib(30))


输出：

    
    in memo  {}
    in wrapper  20
    in wrapper  19
    in wrapper  18
    in wrapper  17
    in wrapper  16
    in wrapper  15
    in wrapper  14
    in wrapper  13
    in wrapper  12
    in wrapper  11
    in wrapper  10
    in wrapper  9
    in wrapper  8
    in wrapper  7
    in wrapper  6
    in wrapper  5
    in wrapper  4
    in wrapper  3
    in wrapper  2
    in wrapper  1
    in wrapper  0
    in wrapper  1
    in wrapper  2
    in wrapper  3
    in wrapper  4
    in wrapper  5
    in wrapper  6
    in wrapper  7
    in wrapper  8
    in wrapper  9
    in wrapper  10
    in wrapper  11
    in wrapper  12
    in wrapper  13
    in wrapper  14
    in wrapper  15
    in wrapper  16
    in wrapper  17
    in wrapper  18
    6765
    in wrapper  10
    55
    in wrapper  30
    in wrapper  29
    in wrapper  28
    in wrapper  27
    in wrapper  26
    in wrapper  25
    in wrapper  24
    in wrapper  23
    in wrapper  22
    in wrapper  21
    in wrapper  20
    in wrapper  19
    in wrapper  20
    in wrapper  21
    in wrapper  22
    in wrapper  23
    in wrapper  24
    in wrapper  25
    in wrapper  26
    in wrapper  27
    in wrapper  28
    832040


如果使用普通的方法，我们知道，这个递归是相当没有效率的，因为会重复调用。比如：我们要计算fib(5)，于是其分解成fib(4) + fib(3)，而fib(4)分解成fib(3)+fib(2)，fib(3)又分解成fib(2)+fib(1)…… 你可看到，基本上来说，fib(3), fib(2), fib(1)在整个递归过程中被调用了两次。

**而我们用decorator，在调用函数前查询一下缓存，如果没有才调用了，有了就从缓存中返回值。一下子，这个递归从二叉树式的递归成了线性的递归。厉害，但是为什么decorator的初始化只会被执行一次，但是wrapper却是每次都会被调用到呢？**


## 




## COMMENT：


**关于这个装饰器还是有些没有弄明白的，要弄清楚**


## REF：


1.[[Python] 对 Python 装饰器的理解心得](http://www.cnblogs.com/ifantastic/archive/2012/12/09/2809325.html)
