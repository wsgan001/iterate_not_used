# python 中怎么测量程序运行的时间？

# ORIGIN


在看视频的时候，视频中想说明python中的不同的写法对应的使用的时间是不同的，因此举了一个例子，使用了timeit来测量程序运行的时间，以前用 c# 的时候也用过，但是不记下来，现在已经忘记当时怎么做的了，因此记下。

代码如下：


```python
# 使用三种方法来计算加法，每种加法使用的数据格式也是不同的，计算并打印出不同的方法与不同的格式结合的计算时间

import timeit

sum_by_for = """
for d in data:
    s+=d
"""
sum_by_sum = """
sum(data)
"""
sum_by_numpy_sum = """
import numpy
numpy.sum(data)
"""
```


​    
    # WARNING 这个"""里面的内容一定要是贴边的，不然是报错的，看来对于”“”的使用还是有些注意点不知道
    

```python
def timeit_using_list(n, loops):
        list_setup = """
    data=[1]*{}
    s=0
    """.format(n)
        print('list result:')
        print('sum_by_for:', timeit.timeit(sum_by_for, list_setup, number=loops))
        print('sum_by_sum:', timeit.timeit(sum_by_sum, list_setup, number=loops))
        print('sum_by_numpy_sum:', timeit.timeit(sum_by_numpy_sum, list_setup, number=loops))

    

    def timeit_using_array(n, loops):
        array_setup = """
    import array
    data=array.array('L',[1]*{})
    s=0
    """.format(n)
        print('array result:')
        print('sum_by_for:', timeit.timeit(sum_by_for, array_setup, number=loops))
        print('sum_by_sum:', timeit.timeit(sum_by_sum, array_setup, number=loops))
        print('sum_by_numpy_sum:', timeit.timeit(sum_by_numpy_sum, array_setup, number=loops))

    def timeit_using_numpy(n, loops):
        numpy_setup = """

    import numpy

    data=numpy.array([1]*{})

    s=0

    """.format(n)

        print('numpy result:')
        print('sum_by_for:', timeit.timeit(sum_by_for, numpy_setup, number=loops))
        print('sum_by_sum:', timeit.timeit(sum_by_sum, numpy_setup, number=loops))
        print('sum_by_numpy_sum:', timeit.timeit(sum_by_numpy_sum, numpy_setup, number=loops))

    

    if name == 'main':
        timeit_using_list(30000, 500)
        timeit_using_array(30000, 500)
        timeit_using_numpy(30000, 500)

```


**输出：**


    list result:
    sum_by_for: 1.1140673746680532
    sum_by_sum: 0.1717248488461791
    sum_by_numpy_sum: 1.6290331389249817
    array result:
    sum_by_for: 1.268921818715539
    sum_by_sum: 0.24743374149226405
    sum_by_numpy_sum: 0.018491738593206186
    numpy result:
    sum_by_for: 2.7695402011136574
    sum_by_sum: 2.3518965623172443
    sum_by_numpy_sum: 0.01615360459480364




## 疑问：


上面的三个例子分别是使用不同的三种方法来计算30000个1的和，并且计算500次。看到这个程序，有几个不清楚的地方：为什么timeit 用的是字符串作为运行的程序？format是什么，怎么用的？为什么numpy.array 在for和sum的时候用的时间这么长呢？比普通的list的for的时间都长。为什么对list的sum这么快？为什么list的numpy.sum比array和numpy.array的都要慢？timeit是怎么使用的？


## 解答：



1.format是怎么使用的？为什么不使用普通的方法？


format格式函数：[http://www.runoob.com/python/att-string-format.html](http://www.runoob.com/python/att-string-format.html)

2.为什么numpy.array 在for和sum的时候用的时间这么长呢？比普通的list的for的时间都长。为什么对list的sum这么快？为什么list的numpy.sum比array和numpy.array的都要慢？


**不知道**

3.timeit 的用法是怎样的？


官方文档：[https://docs.python.org/3/library/timeit.html](https://docs.python.org/3/library/timeit.html)

COMMENT:


官方文档还是要仔细看下的

format 的例子再补充下，


