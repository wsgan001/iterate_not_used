
# A.7 Writing Fast NumPy Functions with Numba（利用Numba构建快速的NumPy函数）

numba是一个开源项目，对于类似于numpy的数据，numba创建的函数，能利用CPU、GPU或其他一些硬件进行快速计算。它利用[LLVM Project](http://llvm.org/)项目，把python代码编译为机器代码。

我们先写一个纯python的例子，用for循环计算(x-y).mean():


```python
import numpy as np
```


```python
def mean_distance(x, y):
    nx = len(x)
    result = 0.0
    count = 0
    for i in range(nx):
        result += x[i] - y[i]
        count += 1
    return result / count
```

上面的函数是很慢的：


```python
x = np.random.randn(10000000)
y = np.random.randn(10000000)
```


```python
%timeit mean_distance(x, y)
```

    1 loop, best of 3: 4.36 s per loop
    


```python
%timeit (x - y).mean()
```

    10 loops, best of 3: 45.2 ms per loop
    

numpy版本快100倍。我们使用numba.jit把这个函数变为numba函数：


```python
import numba as nb
```


```python
numba_mean_distance = nb.jit(mean_distance)
```

我们也可以写成装饰器（decorator）：

    @nb.jit
    def mean_distance(x, y):
        nx = len(x)
        result = 0.0
        count = 0
        for i in range(nx):
            result += x[i] - y[i]
            count += 1
        return result / count
        
结果会比向量化的numpy版本还要快：


```python
%timeit numba_mean_distance(x, y)
```

    The slowest run took 30.87 times longer than the fastest. This could mean that an intermediate result is being cached.
    1 loop, best of 3: 14.2 ms per loop
    

numba的jit函数有一个选项，nopyhton=True，能强制通过LLVM对代码进行编译，而不调用任何Python C API。jit(nopython=True)有一个简写，numba.njit.

上面的例子可以写成：


```python
from numba import float64, njit

@njit(float64(float64[:], float64[:]))
def mean_distance(x, y):
    return (x - y).mean()
```

# 1 Creating Custom numpy.ufunc Objects with Numba（利用Numba创建自定义的numpy.ufunc对象）

numba.vectorize函数能创建编译过的numpy ufuncs，效果就像是内建的built-in ufuncs一样。比如我们实现一个numpy.add：


```python
from numba import vectorize

@vectorize
def nb_add(x, y):
    return x + y
```


```python
x = np.arange(10)
```


```python
nb_add(x, x)
```




    array([ 0,  2,  4,  6,  8, 10, 12, 14, 16, 18])




```python
nb_add.accumulate(x, 0)
```




    array([ 0,  1,  3,  6, 10, 15, 21, 28, 36, 45])


