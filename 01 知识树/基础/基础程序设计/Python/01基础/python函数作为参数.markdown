# python函数作为参数



# TODO

 * **还需要再总结下**
* **而且想知道这种传函数再什么场景下会使用？而且之前看到过一种把类里面的函数赋值给一个变量的，然后调用这个变量就相当于调用类里面这个函数，这种形式什么时候会用到？**



# ORIGIN


一直对python里面将函数作为参数不是特别清楚，之前在用c#的时候也遇到过将排序的函数作为参数传递到给别的函数，一直不是很清楚。


## 要点：




可以把别的函数作为参数传入的函数叫高阶函数。由于python里变量和函数都是object，那么就是说，比如 abs 这个函数可以直接复制给另外一个变量。而且由于函数本身可以作为一个变量，而我们的变量是可以作为另一个函数的参数的，那么一个函数也可以作为另一个函数的参数。

代码如下：


```python
# 函数是可以作为参数的
p = print
p(1, 2, 3)
```


​    
    def my_sum(x, y, p=None):
        s = x + y
        if p:
            p(s)
        return s


​    
    my_sum(100, 200)
    my_sum(100, 200, print)


​    
    # 可以定义一个比较函数
    def cmp(x, y, cp=None):
        if not cp:  # 注意这个地方堆cp的判断
            if x > y:
                return 1
            elif x < y:
                return -1
            else:
                return 0
        else:
            return cp(x, y)


​    
    def my_cp(x, y):
        if x < y:
            return 1
        elif x == y:
            return 0
        else:
            return -1


​    
    print(cmp(100, 200))
    print(cmp(100, 200, my_cp))


​    
    # python 里面函数可以作为参数传递
    def do_sum(data, method):
        return method(data)


​    
    print(do_sum([1, 2, 3], sum))



输出：


    1 2 3
    300
    -1
    1
    6
