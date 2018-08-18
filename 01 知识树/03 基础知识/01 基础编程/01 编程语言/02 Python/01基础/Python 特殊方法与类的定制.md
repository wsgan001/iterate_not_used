# TODO

 * **这些还是不是很清楚，要弄清楚**
 * 



## 


总结一下，有些与C++和C#里面相似的功能，但是实现的话还是有区别的，使用的是__str__、__iter__、__getitem__等，这些或多或少的会修改类的行为


## 

# MAIN POINT


### 1.使用 __str__ 控制打印类的时候的输出


代码如下：


    class MyClass1:
        def __init__(self, name):
            self.name = name


​    
    class MyClass2:
        def __init__(self, name):
            self.name = name
    
        # 通过实现这个，可以控制这个类在print的时候显示的内容，这个相当于C#里面的接口
        def __str__(self):
            print('print will call __str__ first.')
            return 'Hello ' + self.name + '!'


​    
    print(MyClass1('Tom'))
    print(MyClass2('Tom'))


输出：


    <__main__.MyClass1 object at 0x0000017ADE1C8630>
    print will call __str__ first.
    Hello Tom!




### 2.使用 __iter__ 做一个迭代器




    # 使用iter和next可以支持for循环
    class Fib100:
        def __init__(self):
            self._1, self._2 = 0, 1
    
        def __iter__(self):
            return self  # 这个只需要返回
    
        def __next__(self):
            self._1, self._2 = self._2, self._1 + self._2
            if self._1 > 100:
                raise StopIteration()  # 循环结束只需要raise一个StopIteration()的异常，系统会知道循环结束了
            return self._1


​    
    for i in Fib100():
        print(i)


输出：


    1
    1
    2
    3
    5
    8
    13
    21
    34
    55
    89




### 3.通过 __getitem__ 来给类提供下标的支持




    class Fib(object):
        def __init__(self):
            self.limit = 1000
    
        def __getitem__(self, key):
            if isinstance(key, slice):
                # Get the start, stop, and step from the slice
                return [self.get_fib(ii) for ii in range(*key.indices(1000))]
            elif isinstance(key, int):
                if key > 1000:
                    raise IndexError("The index (%d) is out of range." % key)
                return self.get_fib(key)
            else:
                raise TypeError("Invalid argument type.")
    
        def get_fib(self, n):
            a, b = 1, 1
            for i in range(n):
                a, b = b, a + b
            return a


​    
    f = Fib()
    print(f[1])
    print(f[5])
    print(f[10])
    print()
    print(f[1:10:2])


输出：


    1
    8
    89
    
    [1, 3, 8, 21, 55]


注：对于slice类型还是第一次见，之前不知道slice也是一个类型。


### 4.使用__call__将class变成callable的




    # 为什么要将class变成callable的呢？
    # 在框架代码中经常看到？
    # 链式调用的时候用到？为什么什么样子的？
    
    class MyClass:
        def __call__(self):
            print('You can call cls() directly.')


​    
    cls = MyClass()
    cls()  # 没有__call__的话会报错：TypeError: 'MyClass' object is not callable
    
    print(callable(cls))
    print(callable(max))
    
    # 不可调用的
    print(callable([1, 2, 3]))
    print(callable(None))
    print(callable('str'))


输出：


    You can call cls() directly.
    True
    True
    False
    False
    False


注：**为什么要将class变成callable的？在框架中经常看到的话是什么样子的？链式调用的时候为什么会用到？**


## COMMENT：



