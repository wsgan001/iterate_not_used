# TODO

- 还要重新整理下，最好整理成之前的一块一块的这种。不要太多的解释文字穿插到中间，只需要再代码中解释一下就行。

# Numpy 库的介绍

这个库太重要了，Python之所以能在科学计算上独领风骚很大程度上就是因为这个库。它还影响了另一个很有名的深度学习库，PyTorch。

NumPy 是Python科学计算的基础包。本书大部分内容都基于 NumPy 以及构建于其上的库。它提供了以下功能（不限于此）：

- 快速高效的多维数组对象ndarray。
- 用于对数组执行元素级计算以及直接对数组执行数学运算的函数。
- 用于读写硬盘上基干数组的数据集的工具。
- 线性代数运算、傅里叶变换，以及随机数生成。
- 用于将C、C++、Fortran 代码集成到 Python 的工具。

除了为 Python 提供快速的数组处理能力，NumPy 在数据分析方面还有另外一个主要作用，即作为在算法之间传递数据的容器。

对干数值型数据，NumPy 数组在存储和处理数据时要比内置的Python数据结构髙效得多。此外，由低级语言（比如C和Fortran）编写的库可以直接操作NumPy数组中的数据，无需进行任何数据复制工作。$\color{red}\large \textbf{怎么直接操作的？这个也要总结一下。}$






### 1.np.array的基础属性和初始化



```
# np.array的一些基础属性
import numpy as np

# 普通的list 对于一个list来说是没有几行的属性的？是吗？
lis = [[1, 2, 3],
[2, 3, 4]]
print(lis)

# 普通的array
ar = np.array([[1, 2, 3],  # 2行3列 每一个被逗号隔开的小团体都是1行
[2, 3, 4]], dtype=np.float64)  # 注意 dtype是np.形式的
print(ar)
print("number of dim:", ar.ndim)  # 是一维的还是二维的？二维的
print("type :", ar.dtype)
print("shape:", ar.shape)  # 矩阵的尺寸 指的是长宽
print("size:", ar.size)  # 总共由多少元素

# np.array的初始化

ar = np.array([[1, 2, 3],
[2, 3, 4]])
print(ar)

# 在使用reshape的时候我设置了shape作为keyword，但是有error报出：
# https://stackoverflow.com/questions/46508340/passing-new-shape-to-np-reshape
# 'shape' is an invalid keyword argument for this function
# g=np.arange(12).reshape(shape=(3,4))
ar = np.arange(10, 34, 2).reshape((3, 4))
print(ar)

# 生成一个从1到20 共20个数的矩阵
ar1 = np.linspace(1, 10, 20)  # 生成一行矩阵
ar2 = np.linspace(1, 10, 20).reshape((5, 4))  # eshape成5行4列
print(ar1)
print(ar2)

a = np.random.random((2, 4))
print(a)

c = np.zeros(shape=(3, 4))  # 3行4列的0矩阵
print(c)
d = np.ones(shape=(3, 4), dtype=np.float32)
print(d)
e = np.empty(shape=(3, 4))  # 生成的是一个几乎接近0的矩阵
print(e)
```

输出：

```
[[1, 2, 3], [2, 3, 4]]
[[ 1.  2.  3.]
[ 2.  3.  4.]]
number of dim: 2
type : float64
shape: (2, 3)
size: 6
[[1 2 3]
[2 3 4]]
[[10 12 14 16]
[18 20 22 24]
[26 28 30 32]]
[  1.           1.47368421   1.94736842   2.42105263   2.89473684
3.36842105   3.84210526   4.31578947   4.78947368   5.26315789
5.73684211   6.21052632   6.68421053   7.15789474   7.63157895
8.10526316   8.57894737   9.05263158   9.52631579  10.        ]
[[  1.           1.47368421   1.94736842   2.42105263]
[  2.89473684   3.36842105   3.84210526   4.31578947]
[  4.78947368   5.26315789   5.73684211   6.21052632]
[  6.68421053   7.15789474   7.63157895   8.10526316]
[  8.57894737   9.05263158   9.52631579  10.        ]]
[[ 0.13723176  0.51171931  0.17402743  0.02751977]
[ 0.47662603  0.33134621  0.43879793  0.12383868]]
[[ 0.  0.  0.  0.]
[ 0.  0.  0.  0.]
[ 0.  0.  0.  0.]]
[[ 1.  1.  1.  1.]
[ 1.  1.  1.  1.]
[ 1.  1.  1.  1.]]
[[  0.00000000e+000   4.94065646e-324   9.88131292e-324   1.48219694e-323]
[  1.97626258e-323   2.47032823e-323   2.96439388e-323   3.45845952e-323]
[  3.95252517e-323   4.44659081e-323   4.94065646e-323   5.43472210e-323]]
```



### 2.对矩阵中元素的选择及迭代



```
# 对矩阵中元素的选择及迭代
import numpy as np

A = np.arange(3, 15)
print(A)
print(A[3])
A = np.arange(3, 15).reshape((3, 4))
print(A)
print(A[2])  # 现在对应的是行数
print(A[1][1])
print(A[1, 1])  # 注意这个是与上面不同的表达方式，但是是相同的

print(A[2, :])
print(A[:, 2])
print(A[1, 1:2])  # 注意这个返回的是一个1*1的矩阵 但是上面的A[1,1]是一个数字

# 迭代每一行
for row in A:
print(row)
# 迭代每一列 这个是一个tricky 先反转然后列就变成行了，就可以进行迭代了。
for column in A.T:
print(column)

print(A.flatten())  # 返回的是一个一行的矩阵
for item in A.flat:  # A.flat 是一个迭代器
print(item)
```

输出：

```
[ 3  4  5  6  7  8  9 10 11 12 13 14]
6
[[ 3  4  5  6]
[ 7  8  9 10]
[11 12 13 14]]
[11 12 13 14]
8
8
[11 12 13 14]
[ 5  9 13]
[8]
[3 4 5 6]
[ 7  8  9 10]
[11 12 13 14]
[ 3  7 11]
[ 4  8 12]
[ 5  9 13]
[ 6 10 14]
[ 3  4  5  6  7  8  9 10 11 12 13 14]
3
4
5
6
7
8
9
10
11
12
13
14
```



### 3.np.array的常规计算

```
#np.array的常规的计算
# 加减乘和求次方

import numpy as np

a = np.array([1, 2, 3, 4])
b = np.arange(4)
print(a)
print(b,'\n')
c = a - b
print(c,'\n')
c = b ^ 2  # 这个是什么计算？
d = b ** 2
print(c)
print(d,'\n')

e = 10 * np.sin(a)
print(e,'\n')

f = b < 3
g = b == 3
print(f)
print(g,'\n')
```

输出：

```
[1 2 3 4]
[0 1 2 3]

[1 1 1 1]

[2 3 0 1]
[0 1 4 9]

[ 8.41470985  9.09297427  1.41120008 -7.56802495]

[ True  True  True False]
[False False False  True]
```



### 4.作为矩阵而具有的操作：



```
import numpy as np
# 作为矩阵而具有的操作
a = np.array([[1, 2],
[2, 3]])
b = np.arange(4).reshape((2, 2))
print(a)
print(b,'\n')
c = a * b  # 对应位置上相乘
print(c,'\n')
c_dot = np.dot(a, b)  # 矩阵的乘法
c_dot2 = a.dot(b)
print(c_dot)
print(c_dot2,'\n')


# 矩阵的转制 会经常用到这个，因为矩阵的乘法的时候会用到
print(np.transpose(a))
print(a.T)
print((a.T).dot(a))
```

输出：

```
[[1 2]
[2 3]]
[[0 1]
[2 3]]

[[0 2]
[4 9]]

[[ 4  7]
[ 6 11]]
[[ 4  7]
[ 6 11]]

[[1 2]
[2 3]]
[[1 2]
[2 3]]
[[ 5  8]
[ 8 13]]
```



### 5.求最大值，最小值，平均值，求和，中位数


```

import numpy as np

# 求最大值，求最小值，求和，求平均值，求中位数
# 随机生成的0~1的随机数
a = np.random.random((2, 4))
print(a)
b = np.max(a)
c = np.min(a)
d = np.sum(a)
print(b)
print(c)
print(d)
# WARINING 这个是要值得注意的，因为0和1 并不是对应的行和列 而是对应的列和行？为什么呢？
# 0是对每一列的操作 1是对每一行的操作
e = np.max(a, axis=0)  # 这个求出的是每一列的最大值
f = np.sum(a, axis=1)  # 这个求出的是每一行的和
print(e)
print(f)

# 平均值
print(np.mean(a))
print(a.mean())
print(np.average(a))
# print(A.average())# 这种是不行的

# 中位数
print(np.median())
```

输出：

```
[[ 0.0394221   0.68637003  0.93676758  0.38890581]
[ 0.40286786  0.54253569  0.50098785  0.93919709]]
0.939197092585
0.0394221003771
4.43705402253
[ 0.40286786  0.68637003  0.93676758  0.93919709]
[ 2.05146553  2.3855885 ]
0.554631752816
0.554631752816
0.554631752816
0.521761770871

```


### 6.一些比较特殊的操作，如sort，argmax，clip，nonezero，diff，cumsum 等



```
# 求一些满足条件的值的索引
A = np.arange(2, 14).reshape((3, 4))
print(A)
print(np.argmin(A))  # 打印出它的最小值的索引 这个索引是全局的索引，而不是一个二维坐标
print(np.argmax(A))
print(np.argmin(A, axis=0))
print(np.argmax(A, axis=1))



# 每一个都是前面的所有相加的值
print(np.cumsum(A))  # 累加的过程，逐步加上去
print(np.diff(A))  # 当前的数减前面的数 会比原来的矩阵少一列

# 输出的是两个行矩阵，一个是非零值的横坐标，一个是非零值的纵坐标
print(np.nonzero(A))  # 这个nonzero 什么时候使用？而且返回的值比较奇怪

# 排序
C = np.array([[1, 7],
[4, 3]])
print(C)
# 排序默认的都是从小到大排序
print(np.sort(C))  # 注意这个是将每一行里面的内容进行排序，而不是全体的进行排序
print(np.sort(C, axis=1))  # 与上面是相同的
print(np.sort(C, axis=0))  # 这个是对每一列里面的内容进行排序

# 所有小于5的都等于5  所有大于9的都等于9
print(np.clip(A, 5, 9))
```

输出：

```
[[ 2  3  4  5]
[ 6  7  8  9]
[10 11 12 13]]
0
11
[0 0 0 0]
[3 3 3]
[ 2  5  9 14 20 27 35 44 54 65 77 90]
[[1 1 1]
[1 1 1]
[1 1 1]]
(array([0, 0, 0, 0, 1, 1, 1, 1, 2, 2, 2, 2], dtype=int64), array([0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3], dtype=int64))
[[1 7]
[4 3]]
[[1 7]
[3 4]]
[[1 7]
[3 4]]
[[1 3]
[4 7]]
[[5 5 5 5]
[6 7 8 9]
[9 9 9 9]]
```



### 7.np.array的拼接与合并


```

#矩阵的拼接与合并

import numpy as np

A = np.array([1, 1, 1])
B = np.array([2, 2, 2])
print(A.shape)  # A 只是一个序列，因此是 (3,)
C = np.vstack((A, B))  # vertical stack
print(C)
print(C.shape)
D = np.hstack((A, B))  # horizontal stack
print(D)
print(D.shape)

print(A)
print(A.T)  # 可见不能用一个一维横向量变成一个1列的向量
E = A[np.newaxis, :]  # 对A在行上加了一个维度
print(E)
print(E.shape)
F = A[:, np.newaxis]  # 这样就可以变成纵向的1列的向量
print(F)
print(F.shape)

A = np.array([1, 1, 1])[:, np.newaxis]
B = np.array([2, 2, 2])[:, np.newaxis]
print(np.hstack((A, B, B)))
C = np.concatenate((A, B, A, B, B), axis=0)  # 0是对列上进行拼接 等于vstack
D = np.concatenate((A, B, A, B, B), axis=1)  # 这时是对行上进行拼接，等于hstack
print(C)
print(D)
```

输出：
```

(3,)
[[1 1 1]
[2 2 2]]
(2, 3)
[1 1 1 2 2 2]
(6,)
[1 1 1]
[1 1 1]
[[1 1 1]]
(1, 3)
[[1]
[1]
[1]]
(3, 1)
[[1 2 2]
[1 2 2]
[1 2 2]]
[[1]
[1]
[1]
[2]
[2]
[2]
[1]
[1]
[1]
[2]
[2]
[2]
[2]
[2]
[2]]
[[1 2 1 2 2]
[1 2 1 2 2]
[1 2 1 2 2]]
```



### 8.np.array的分割



```
#矩阵的分割

import numpy as np

A = np.arange(12).reshape((3, 4))
print(A)
print(np.split(A, 3, axis=0))  # 不能进行每一项分割出来的不等的分割
print(np.split(A, 2, axis=1))  # 将列分为两块
print(np.vsplit(A, 3))
print(np.hsplit(A, 2))

# 不等量分割：
# 机器学习的时候经常会用到不等量的分割
# QUESTION 但是这个不等量分割的规律是什么？
print(np.array_split(A, 3, axis=1))
```

输出：

```
[[ 0  1  2  3]
[ 4  5  6  7]
[ 8  9 10 11]]
[array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
[array([[0, 1],
[4, 5],
[8, 9]]), array([[ 2,  3],
[ 6,  7],
[10, 11]])]
[array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
[array([[0, 1],
[4, 5],
[8, 9]]), array([[ 2,  3],
[ 6,  7],
[10, 11]])]
[array([[0, 1],
[4, 5],
[8, 9]]), array([[ 2],
[ 6],
[10]]), array([[ 3],
[ 7],
[11]])]
```



### 9.矩阵的拷贝


```

# 矩阵的拷贝

import numpy as np

A = np.arange(4)
print(A)
B = A
C = A
D = B

# 改变之后会看出ABCD实际上是同一个东西，改变任何一个都会改变其他的
A[0] = 1.3  # 注意 默认是正数，因此这个会变成1 因此arange之后一半在tensorflow中使用的画要注意标识np.type
print(A)
print(B)
print(C)
print(D)
print(B is A and D is A)

# 赋值的时候又不想关联起来，则可以用copy
B = A.copy()  # deep copy 就是将A的值赋值给B 但是没有将他们指向同一个东西
A[1:3] = [22, 33]
print(A)
print(B)
print(C)
```

输出：
```
[0 1 2 3]
[1 1 2 3]
[1 1 2 3]
[1 1 2 3]
[1 1 2 3]
True
[ 1 22 33  3]
[1 1 2 3]
[ 1 22 33  3]
```



## COMMENT：


**肯定有不充分的地方，后续迭代补充，而且对这些方法的使用还不熟练**


## REF：
*










# CHAPTER 4

# NumPy Basics: Arrays and Vectorized Computation

在数值计算领域，Numpy 是 python 最重要的包也不为过。在numpy中有下面这些东西：

- ndarray, 一个有效的多维数组，能提供以数组为导向的快速数值计算和灵活的广播功能（broadcasting）==什么是广播功能？==
- 便利的数学函数
- 用于读取/写入(reading/writing)==numpy 有读写的功能吗==
- 线性代数，随机数生成，傅里叶变换能力
- 可以用C API来写C，C++，或FORTRAN ==怎么写？==

通过学习理解 numpy 中数组和数组导向计算，能帮我们理解 pandas 之类的工具。

# 4.1 The NumPy ndarray: A Multidimensional Array Object（ndarray: 多维数组对象）

N-dimensional array object（n维数组对象）, or ndarray，这是numpy的关键特征。先来尝试一下，生成一个随机数组：


```
import numpy as np
data = np.random.randn(2, 3)# Generate some random data
print(data)
```
输出：
```
array([[-0.35512366, -0.63779545,  0.14137933],
       [ 0.36642056,  0.30898139, -0.87040292]])
```

进行一些数学运算：


```
print(data * 10)
print(data + data)
```

输出：

```
array([[-3.55123655, -6.37795453,  1.41379333],
       [ 3.66420556,  3.0898139 , -8.70402916]])
array([[-0.71024731, -1.27559091,  0.28275867],
       [ 0.73284111,  0.61796278, -1.74080583]])
```

每一个数组都有一个shape，来表示维度大小。而 dtype，用来表示 data type：


```
print(data.shape)
print(data.dtype)
```
输出：
```
(2, 3)
dtype('float64')
```

# 1 Greating ndarrays (创建n维数组)

最简单的方法使用array函数，输入一个序列即可，比如list：


```
data1 = [6, 7.5, 8, 0, 1]
arr1 = np.array(data1)
print(arr1)
```

输出：

```
array([ 6. ,  7.5,  8. ,  0. ,  1. ])
```

嵌套序列能被转换为多维数组：


```
data2 = [[1, 2, 3, 4], [5, 6, 7, 8]]
arr2 = np.array(data2)
print(arr2)
```

输出：

```
array([[1, 2, 3, 4],
       [5, 6, 7, 8]])
```

因为 data2 是一个 list of lists , 所以arr2维度为2。我们能用ndim和shape属性来确认一下：


```
print(arr2.ndim)
print(arr2.shape)
```

输出：

```
2
(2, 4)
```

除非主动声明，否则np.array会自动给data搭配适合的类型，并保存在dtype里：


```
print(arr1.dtype)
print(arr2.dtype)
```
输出：

```
dtype('float64')
dtype('int64')
```

除了 np.array，还有一些其他函数能创建数组。比如zeros,ones，另外还可以在一个tuple里指定shape：



```
print(np.zeros(10))
print(np.zeros((3, 6)))
print(np.empty((2, 3, 2)))
```
输出：

```
array([ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.])
array([[ 0.,  0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.,  0.]])
array([[[  0.00000000e+000,   0.00000000e+000],
        [  2.16538378e-314,   2.16514681e-314],
        [  2.16511832e-314,   2.16072529e-314]],

       [[  0.00000000e+000,   0.00000000e+000],
        [  2.14037397e-314,   6.36598737e-311],
        [  0.00000000e+000,   0.00000000e+000]]])
```

np.empty 并不能保证返回所有是0的数组，某些情况下，会返回为初始化的垃圾数值，比如上面。

arange 是一个数组版的 python range 函数：

```
np.arange(15)
```

输出：

```
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])
```

这里是一些创建数组的函数：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/3DLbKa5fCG.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/2kAi8ehi7H.png?imageslim)



# 2 Data Types for ndarrays

dtype 保存数据的类型：



```
arr1 = np.array([1, 2, 3], dtype=np.float64)
arr2 = np.array([1, 2, 3], dtype=np.int32)
print(arr1.dtype)
print(arr2.dtype)
```

输出：

```
dtype('float64')
dtype('int32')
```

dtype才是numpy能灵活处理其他外界数据的原因。

类型表格：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/l8H3E5AlFB.png?imageslim)
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/4i4gEd9FjB.png?imageslim)

可以用astype来转换类型：



```
arr = np.array([1, 2, 3, 4, 5])
print(arr.dtype)
float_arr = arr.astype(np.float64)
print(float_arr.dtype)
```
输出：

```
dtype('int64')
dtype('float64')
```

上面是把int变为float。如果是把float变为int，小数点后的部分会被丢弃：



```
arr = np.array([3.7, -1.2, -2.6, 0.5, 12.9, 10.1])
print(arr)
arr.astype(np.int32)
print(arr)
```
输出：

```
array([  3.7,  -1.2,  -2.6,   0.5,  12.9,  10.1])
array([ 3, -1, -2,  0, 12, 10], dtype=int32)
```

还可以用astype把string里的数字变为实际的数字：==没想到还可以这样，但是如果里面有不可以转化的，那么会出错吗？怎么处理？嗯，下面说了，cast失败会有 error==

```
numeric_strings = np.array(['1.25', '-9.6', '42'], dtype=np.string_)
print(numeric_strings)
numeric_strings.astype(float)
print(numeric_strings)
```
输出：

```
array([b'1.25', b'-9.6', b'42'], dtype='|S4')
array([  1.25,  -9.6 ,  42.  ])
```

要十分注意`numpy.string_`类型，这种类型的长度是固定的，所以可能会直接截取部分输入而不给警告。==什么叫长度是固定的？指的是溢出不会提示吗？==

如果转换（casting）失败的话，会给出一个ValueError提示。

可以用其他数组的dtype直接来制定类型：


```
int_array = np.arange(10)
calibers = np.array([.22, .270, .357, .380, .44, .50], dtype=np.float64)
int_array.astype(calibers.dtype)
print(int_array)
```
输出：

```
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.,  9.])
```

还可以利用类型的缩写，比如 u4 就代表 unit32 ：==还是用全称比较好==


```
empty_unit32 = np.empty(8, dtype='u4')
print(empty_unit32)
```

输出：

```
array([0, 0, 0, 0, 0, 0, 0, 0], dtype=uint32)
```

记住，astype总是会返回一个新的数组。==这个的确是要注意的，即使是与原来的相同的type，也是会是一个新的数组吗？==

# 3 Arithmetic with NumPy Arrays（数组计算）

数组之所以重要，是因为不用写 for 循环就能表达很多操作，这种特性叫做 vectorization(向量化)。任何两个大小相等的数组之间的运算，都是 element-wise（点对点）：



```
arr = np.array([[1., 2., 3.], [4., 5., 6.]])
print(arr)
print(arr * arr)
arr - arr
```

输出：

```
array([[ 1.,  2.,  3.],
       [ 4.,  5.,  6.]])
array([[  1.,   4.,   9.],
       [ 16.,  25.,  36.]])
array([[ 0.,  0.,  0.],
       [ 0.,  0.,  0.]])
```

element-wise 我翻译为点对点，就是指两个数组的运算，在同一位置的元素间才会进行运算。

这种算数操作如果涉及标量（scalar）的话，会涉及到数组的每一个元素：==这个要注意，之间的区别==



```
1 / arr
arr ** 0.5
```
输出：

```
array([[ 1.        ,  0.5       ,  0.33333333],
       [ 0.25      ,  0.2       ,  0.16666667]])
array([[ 1.        ,  1.41421356,  1.73205081],
       [ 2.        ,  2.23606798,  2.44948974]])
```

两个数组的比较会产生布尔数组：


```
arr2 = np.array([[0., 4., 1.], [7., 2., 12.]])
print(arr2)
print(arr2 > arr)
```
输出：
```
array([[  0.,   4.,   1.],
       [  7.,   2.,  12.]])
array([[False,  True, False],
       [ True, False,  True]], dtype=bool)
```

# 4 Basic Indexing and Slicing（基本的索引和切片）

一维的我们之前已经在list部分用过了，没什么不同：



```
arr = np.arange(10)
print(arr)
print(arr[5])
print(arr[5:8])
arr[5:8] = 12
print(arr)
```

输出：

```
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
5
array([5, 6, 7])
array([ 0,  1,  2,  3,  4, 12, 12, 12,  8,  9])
```

这里把12赋给`arr[5:8]`，其实用到了broadcasted（我觉得应该翻译为广式转变）。这里有一个比较重要的概念需要区分，python 内建的 list 与 numpy 的 array 有个明显的区别，这里 array 的切片后的结果只是一个views（视图），用来代表原有array对应的元素，而不是创建了一个新的 array 。但 list 里的切片是产生了一个新的 list：==什么叫只是一个 views ？意思是说，我对这个views 的操作实际上就是对原始数据的操作吗？==


比如：下面如果我们改变arr_slice的值，会反映在原始的数组arr上：==OK，我知道 views 是什么意思了，的确是与python 自带的 list 的切片是不一样的。==

```
arr_slice = arr[5:8]
print(arr_slice)
arr_slice[1] = 12345
print(arr)
```

输出：

```
array([12, 12, 12])
array([    0,     1,     2,     3,     4,    12, 12345,    12,     8,     9])
```

`[:]` 这个赋值给所有元素：



```
arr_slice[:] = 64
print(	arr)
```
输出：

```
array([ 0,  1,  2,  3,  4, 64, 64, 64,  8,  9])
```

之所以这样设计是出于性能和内存的考虑，毕竟如果总是复制数据的话，会很影响运算时间。当然如果想要复制，可以使用copy()方法，比如 `arr[5:8].copy()` ==嗯，这种copy 方法。

在一个二维数组里，单一的索引指代的是一维的数组：



```
arr2d = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
arr2d[2]
```
输出：

```
array([7, 8, 9])
```

有两种方式可以访问单一元素：==这两种方法这里再强调下，有时候会有些迷惑==

```
arr2d[0][2]
arr2d[0, 2]
```
输出：

```
3
3
```

我们可以把下图中的axis0看做row（行），把axis1看做column（列）：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/Dc587HleLb.png?imageslim)

对于多维数组，如果省略后面的索引，返回的将是一个低纬度的多维数组。比如下面一个2 x 2 x 3数组：



```
arr3d = np.array([[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]])
print(arr3d)
```
输出：

```
array([[[ 1,  2,  3],
        [ 4,  5,  6]],

       [[ 7,  8,  9],
        [10, 11, 12]]])
```

arr3d[0]是一个2x3数组：


```
arr3d[0]
```

输出：

```
array([[1, 2, 3],
       [4, 5, 6]])
```

标量和数组都能赋给arr3d[0]:


```
old_values = arr3d[0].copy()
arr3d[0] = 42
print(	arr3d)
arr3d[0] = old_values
print(	arr3d)
```

输出：

```
array([[[42, 42, 42],
        [42, 42, 42]],

       [[ 7,  8,  9],
        [10, 11, 12]]])
array([[[ 1,  2,  3],
        [ 4,  5,  6]],

       [[ 7,  8,  9],
        [10, 11, 12]]])
```

`arr3d[1, 0]` 会给你一个(1, 0)的一维数组：



```
print(	arr3d[1, 0])
x = arr3d[1]
print(	x)
print(	x[0])
```

输出：

```
array([7, 8, 9])
array([[ 7,  8,  9],
       [10, 11, 12]])
array([7, 8, 9])
```

一定要牢记这些切片后返回的数组都是 views。==嗯==

## Indexing with slices（用切片索引）

一维的话和 python 里的 list 没什么差别：



```
print(arr)
print(arr[1:6])
```

输出：

```
array([ 0,  1,  2,  3,  4, 64, 64, 64,  8,  9])
array([ 1,  2,  3,  4, 64])
```

二维的话，数组的切片有点不同：



```
import numpy as np
list2=[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
arr2d = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(list2[:2])
print(arr2d[:2])
```

输出：

```
[[1, 2, 3], [4, 5, 6]]
[[1 2 3]
 [4 5 6]]
```

可以看到，切片是沿着axis 0（行）来处理的。所以，数组中的切片，是要沿着设置的axis来处理的。我们可以把arr2d[:2]理解为“选中arr2d的前两行”。==好像没有看出有什么区别？确认下==

当然，给定多个索引后，也可以使用复数切片：



```
import numpy as np
arr2d = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(arr2d)
print(arr2d[:2, 1:]) # 前两行，第二列之后
print(arr2d[0, 2])
```

输出：

```
[[1 2 3]
 [4 5 6]
 [7 8 9]]
[[2 3]
 [5 6]]
```

记住，选中的是array view。
通过混合整数和切片，能做低维切片。比如，我们选中第二行的前两列：



```
arr2d[1, :2]
```

输出：

```
array([4, 5])
```

选中第三列的前两行：


```
arr2d[:2, 2]
```

输出：

```
array([3, 6])
```

冒号表示提取整个axis（轴）：



```
arr2d[:, :1]
```

输出：

```
array([[1],
       [4],
       [7]])
```

看图示有助于理解：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/3efm8HfhJB.png?imageslim)

赋值也很方便：


```
arr2d[:2, 1:] = 0
print(	arr2d)
```

输出：

```
array([[1, 0, 0],
       [4, 0, 0],
       [7, 8, 9]])
```

# 5 Boolean Indexing (布尔索引)

假设我们的数组数据里有一些重复。这里我们用 numpy.random 里的 randn 函数来随机生成一些离散数据：



```
names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
print(	names)
```

输出：

```
array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'], dtype='<U4')
```



```
data = np.random.randn(7, 4)
print(	data)
```

输出：

```
array([[ 0.02584271, -1.53529621,  0.73143988, -0.34086189],
       [ 0.40864782,  0.53476799,  1.09620596,  0.4846564 ],
       [ 1.95024076, -0.37291038, -0.40424703,  0.30297059],
       [-0.48632936,  0.63817756, -0.40792716, -1.48037389],
       [-0.81976335, -1.10162466, -0.59823212, -0.10926744],
       [-0.5212113 ,  0.29449179,  2.0568032 ,  2.00515735],
       [-2.36066876, -0.3294302 , -0.24464646, -0.81432884]])
```

假设每一个name对应data数组中的一行，我们想要选中name为 'Bob' 的所有行。就像四则运算，用比较运算符（==）建立一个布尔数组，然后用这个布尔数组当做索引：==很好的用法==

```
names == 'Bob'
data[names == 'Bob']
```

输出：

```
array([ True, False, False,  True, False, False, False], dtype=bool)
array([[ 0.02584271, -1.53529621,  0.73143988, -0.34086189],
       [-0.48632936,  0.63817756, -0.40792716, -1.48037389]])
```

注意：布尔数组和data数组的长度要一样。==如果比 data 的长度短会怎么样？==

我们可以选中names=='Bob'的行，然后索引列：


```
data[names == 'Bob', 2:]
data[names == 'Bob', 3]
```

输出：

```
array([[ 0.73143988, -0.34086189],
       [-0.40792716, -1.48037389]])
array([-0.34086189, -1.48037389])
```

选中除了 'Bob' 外的所有行，可以用`!=`或者`~`：


```
names != 'Bob'
data[~(names == 'Bob')]
```

输出：

```
array([False,  True,  True, False,  True,  True,  True], dtype=bool)
array([[ 0.40864782,  0.53476799,  1.09620596,  0.4846564 ],
       [ 1.95024076, -0.37291038, -0.40424703,  0.30297059],
       [-0.81976335, -1.10162466, -0.59823212, -0.10926744],
       [-0.5212113 ,  0.29449179,  2.0568032 ,  2.00515735],
       [-2.36066876, -0.3294302 , -0.24464646, -0.81432884]])
```

当想要反转一个条件时，用`~`操作符很方便：==这个不知道，感觉还是很有用的==



```
cond = names == 'Bob'
data[~cond]
```

输出：

```
array([[ 0.40864782,  0.53476799,  1.09620596,  0.4846564 ],
       [ 1.95024076, -0.37291038, -0.40424703,  0.30297059],
       [-0.81976335, -1.10162466, -0.59823212, -0.10926744],
       [-0.5212113 ,  0.29449179,  2.0568032 ,  2.00515735],
       [-2.36066876, -0.3294302 , -0.24464646, -0.81432884]])
```

选中2个或3个名字，组合多个布尔条件，用布尔运算符&，|，另外python中的关键词 and 和 or 不管用：==这个地方不知道，之前没用过==



```
names
mask = (names == 'Bob') | (names == 'Will')
mask
data[mask]
```

输出：

```
array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'], dtype='<U4')
array([ True, False,  True,  True,  True, False, False], dtype=bool)
array([[ 0.02584271, -1.53529621,  0.73143988, -0.34086189],
       [ 1.95024076, -0.37291038, -0.40424703,  0.30297059],
       [-0.48632936,  0.63817756, -0.40792716, -1.48037389],
       [-0.81976335, -1.10162466, -0.59823212, -0.10926744]])
```

用布尔索引总是会返回一份新创建的数据，原本的数据不会被改变。==这一点是要注意的，是返回一份新的数据，与刚才讲的 arry 的切片返回 view 是不一样的。==

更改值的方式也很直觉。比如我们想让所有负数变为0：



```
data[data < 0] = 0
print(	data)
```

输出：

```
array([[ 0.02584271,  0.        ,  0.73143988,  0.        ],
       [ 0.40864782,  0.53476799,  1.09620596,  0.4846564 ],
       [ 1.95024076,  0.        ,  0.        ,  0.30297059],
       [ 0.        ,  0.63817756,  0.        ,  0.        ],
       [ 0.        ,  0.        ,  0.        ,  0.        ],
       [ 0.        ,  0.29449179,  2.0568032 ,  2.00515735],
       [ 0.        ,  0.        ,  0.        ,  0.        ]])
```

用一维的布尔数组也能更改所有行或列：

```
print(names)
data[names != 'Joe'] = 7 #看到这个感觉就好像 null 的就应该这么处理不知道可不可行
print(data)
```

输出：

```
array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'], dtype='<U4')
array([[ 7.        ,  7.        ,  7.        ,  7.        ],
       [ 0.40864782,  0.53476799,  1.09620596,  0.4846564 ],
       [ 7.        ,  7.        ,  7.        ,  7.        ],
       [ 7.        ,  7.        ,  7.        ,  7.        ],
       [ 7.        ,  7.        ,  7.        ,  7.        ],
       [ 0.        ,  0.29449179,  2.0568032 ,  2.00515735],
       [ 0.        ,  0.        ,  0.        ,  0.        ]])
```

# 6 Fancy Indexing(花式索引)==这一节不是很理解，而且这里还说 fancy indexing 与之前的切片是不同的，返回的是一个新的array，想知道，为什么要弄得与 切片不同？==

通过整数数组来索引。假设我们有一个8 x 4的数组：



```
arr = np.empty((8, 4))
for i in range(8):
    arr[i] = i
print(	arr)
```

输出：

```
array([[ 0.,  0.,  0.,  0.],
       [ 1.,  1.,  1.,  1.],
       [ 2.,  2.,  2.,  2.],
       [ 3.,  3.,  3.,  3.],
       [ 4.,  4.,  4.,  4.],
       [ 5.,  5.,  5.,  5.],
       [ 6.,  6.,  6.,  6.],
       [ 7.,  7.,  7.,  7.]])
```

想要按一定顺序选出几行，可以用一个整数 list 或整数 ndarray 来指定顺序：==这也可以！！OK，好吧==



```
arr[[4, 3, 0, 6]]
```

输出：

```
array([[ 4.,  4.,  4.,  4.],
       [ 3.,  3.,  3.,  3.],
       [ 0.,  0.,  0.,  0.],
       [ 6.,  6.,  6.,  6.]])
```

用符号来从后选择 row：


```
arr[[-3, -5, -7]]
```

输出：

```
array([[ 5.,  5.,  5.,  5.],
       [ 3.,  3.,  3.,  3.],
       [ 1.,  1.,  1.,  1.]])
```

用多维索引数组，能选出由一维数组中的元素，通过在每个 tuple 中指定索引：


```
arr = np.arange(32).reshape((8, 4))
print(arr)
print(arr[[1, 5, 7, 2], [0, 3, 1, 2]])
```

输出：

```
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]
 [12 13 14 15]
 [16 17 18 19]
 [20 21 22 23]
 [24 25 26 27]
 [28 29 30 31]]
[ 4 23 29 10]
```

可以看到 `[ 4, 23, 29, 10]` 分别对应`(1, 0), (5, 3), (7, 1), (2, 2)`。不论数组有多少维，fancy indexing的结果总是一维。==还是不够理解，再找一些例子和说明。==

对于长方形区域，有下面的方法来截取：==没明白==

==既然说 这个fancy indexing 与切片是不同的，返回的是一个新的array 。那么它们的这种混用是返回的什么？==

```
arr[[1, 5, 7, 2]][:, [0, 3, 1, 2]]
```

输出：

```
array([[ 4,  7,  5,  6],
       [20, 23, 21, 22],
       [28, 31, 29, 30],
       [ 8, 11,  9, 10]])
```

上面的意思是，先从arr中选出[1, 5, 7, 2]这四行：

```
array([[ 4,  5,  6,  7],
       [20, 21, 22, 23],
       [28, 29, 30, 31],
       [ 8,  9, 10, 11]])
```

然后[:, [0, 3, 1, 2]]表示选中所有行，但是列的顺序要按0,3,1,2来排。于是得到：==为什么这么理解？与 fancy indexing 的理解是怎么区分开的？==

```
array([[ 4,  7,  5,  6],
       [20, 23, 21, 22],
       [28, 31, 29, 30],
       [ 8, 11,  9, 10]])
```

要记住，fancy indexing 和切片不同，得到的是一个新的 array。

# 7 Transposing Arrays and Swapping Axes（数组转置和轴交换）

转置也是返回一个view，而不是新建一个数组。有两种方式，一个是transpose方法，一个是 T 属性：==震惊了，转置也是返回的一个 view ？我一直以为是返回的一个新的数组，确认下。==



```
arr = np.arange(15).reshape((3, 5))
print(arr)
print(arr.T)
```

输出：

```
array([[ 0,  1,  2,  3,  4],
       [ 5,  6,  7,  8,  9],
       [10, 11, 12, 13, 14]])
array([[ 0,  5, 10],
       [ 1,  6, 11],
       [ 2,  7, 12],
       [ 3,  8, 13],
       [ 4,  9, 14]])
```

做矩阵计算的时候，这个功能很常用，计算矩阵乘法的时候，用np.dot：==注意，矩阵的乘法用的是 np.dot==



```
arr = np.arange(8).reshape((4, 2))
print(arr.T)
print(arr)
print(np.dot(arr.T, arr))
```
输出：

```
[[0 2 4 6]
 [1 3 5 7]]
[[0 1]
 [2 3]
 [4 5]
 [6 7]]
[[56, 68],
 [68, 84]]
```

上面的例子是 (2x4) x (4x2) = (2x2)。得到的结果是2x2维，就是普通的矩阵乘法。

对于多维数组，transpose 会接受由轴数字组成的 tuple，来交换轴：



```
arr = np.arange(16).reshape((2, 2, 4))
print(arr)
print(arr.transpose((1, 0, 2)))
```

输出：

```
array([[[ 0,  1,  2,  3],
        [ 4,  5,  6,  7]],

       [[ 8,  9, 10, 11],
        [12, 13, 14, 15]]])
array([[[ 0,  1,  2,  3],
        [ 8,  9, 10, 11]],

       [[ 4,  5,  6,  7],
        [12, 13, 14, 15]]])
```

这里，secode axis(1) 被设为第一个，first axis(0) 第二个，最后的axis没边。==没有怎么看懂，确认下==

使用 `.T` 来转置 swapping axes(交换轴) 的一个特殊情况。ndarray 有方法叫做 swapaxes , 这个方法取两个 axis 值，并交换这两个轴：==这个地方没看懂，确认下==



```
print(arr)
print(arr.swapaxes(1, 2))# 直交换 second axis 和 last axis
```

输出：

```
[[[ 0,  1,  2,  3],
    [ 4,  5,  6,  7]],

    [[ 8,  9, 10, 11],
    [12, 13, 14, 15]]]
[[[ 0,  4],
    [ 1,  5],
    [ 2,  6],
    [ 3,  7]],

    [[ 8, 12],
    [ 9, 13],
    [10, 14],
    [11, 15]]]
```
swapaxes也是返回view，不生成新的data。==嗯==

```

```







# 4.2 Universal Functions: Fast Element-Wise Array Functions（通用函数：快速点对点数组函数）

universal function, 或 ufunc, 是用来在 ndarray 中实现 element-wise 操作的。==对了，我突然想到 ndarray 还有类似的martix 吗？也要对比总结下==
==而且，自己可以编写这种函数吗？==

可以认为这个 ufunc 可以把一些简单的函数做快速的向量化封装，输入是一个以上的标量，输出也是一个以上的标量。

很多 ufuncs 都是点对点的变换，像 sqrt 或 exp：



```
import numpy as np
arr = np.arange(10)
print(arr)
print(np.sqrt(arr))
print(np.exp(arr))
```

输出：

```
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
array([ 0.        ,  1.        ,  1.41421356,  1.73205081,  2.        ,
        2.23606798,  2.44948974,  2.64575131,  2.82842712,  3.        ])
array([  1.00000000e+00,   2.71828183e+00,   7.38905610e+00,
         2.00855369e+01,   5.45981500e+01,   1.48413159e+02,
         4.03428793e+02,   1.09663316e+03,   2.98095799e+03,
         8.10308393e+03])
```

这些函数叫做一元通用函数（unary ufuncs）。其他一些函数，比如add或maximum，需要两个数组（binary ufuncs）,并返回一个数组作为结果：



```
x = np.random.randn(8)
y = np.random.randn(8)
print(x)
print(y)
print(np.maximum(x, y))

```

输出：

```
array([ 0.18373557, -1.82728347, -0.11149882, -1.34286776, -1.09016986,
        1.63308   ,  1.05205535, -0.32746706])
array([-0.42410809,  1.89603273, -1.13649816, -0.98559379, -0.16827718,
        0.52828569,  1.57543351,  1.50045399])
array([ 0.18373557,  1.89603273, -0.11149882, -0.98559379, -0.16827718,
        1.63308   ,  1.57543351,  1.50045399])
```

这里 mamimum 点对点的比较 x 和 y 中的元素。

尽管不常见，但ufunc也能返回多个数组。例如 modf，这是一个向量版的 divmod（python内建函数），modf 会返回小数部分和整数部分：

> 本函数是实现 a 除以b，然后返回商与余数的元组。如果两个参数a,b都是整数，那么会采用整数除法，结果相当于（a//b, a % b)。如果a或b是浮点数，相当于（math.floor(a/b), a%b)。



```
arr = np.random.randn(7) * 5
print(arr)
remainder, whole_part = np.modf(arr)
print(remainder)
print(whole_part)
```

输出：

```
array([ 1.51538382, -0.75054846,  0.02863286,  8.74026861, -3.44529124,
       -9.18401768, -0.68469611])
array([ 0.51538382, -0.75054846,  0.02863286,  0.74026861, -0.44529124,
       -0.18401768, -0.68469611])
array([ 1., -0.,  0.,  8., -3., -9., -0.])
```

ufunc 能接受一个可选参数作为输出，这样可以直接更改原有的数组：==还可以这样！==

```
arr = np.random.randn(7) * 5
print(arr)
print(np.sqrt(arr)) # 没有改变原有的 arr
print(arr)
print(np.sqrt(arr, arr)) # 改变了原有的arr
print(arr)
```

输出：

```
[ 1.51374335 -1.43642492 -3.15505828  1.24763007 -7.30662272  1.04522963
 -1.81342858]
[ 1.23034278         nan         nan  1.11697362         nan  1.02236472
         nan]
[ 1.51374335 -1.43642492 -3.15505828  1.24763007 -7.30662272  1.04522963
 -1.81342858]
[ 1.23034278         nan         nan  1.11697362         nan  1.02236472
         nan]
[ 1.23034278         nan         nan  1.11697362         nan  1.02236472
         nan]
```

一些一元通用函数：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/89dFg5A64a.png?imageslim)

一些二元通用函数：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/BcGGHm8EAk.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/dbggl7lbcJ.png?imageslim)



# 3 Array-Oriented Programming with Arrays（数组导向编程）



向量化的数组运算比纯 python 同等程度的运算要快很多。

一个简单的例子，假设我们想要评价函数`sqrt(x^2 + y^2)`。`np.meshgrid`函数取两个1维的数组，产生一个2维的矩阵，对应于所有两个数组中(x, y)的组合：


```
import numpy as np

```

在进行书中的内容之前，先举个例子说明meshgrid的效果。meshgrid函数用两个坐标轴上的点在平面上画网格。用法：==mesh 是什么的缩写？==

- `[X,Y]=meshgrid(x,y)`
- `[X,Y]=meshgrid(x)` 与 `[X,Y]=meshgrid(x,x)` 是等同的
- `[X,Y,Z]=meshgrid(x,y,z)` 生成三维数组，可用来计算三变量的函数和绘制三维立体图

这里，主要以`[X,Y]=meshgrid(x,y)`为例，来对该函数进行介绍。

`[X,Y] = meshgrid(x,y)` 将向量 x 和 y 定义的区域转换成矩阵 X 和 Y，其中矩阵 X 的行向量是向量 x 的简单复制，而矩阵 Y 的列向量是向量 y 的简单复制 (注：下面代码中X和Y均是数组，在文中统一称为矩阵了)。

假设 x 是长度为 m 的向量，y 是长度为 n 的向量，则最终生成的矩阵 X 和 Y 的维度都是 nm （注意不是mn）。


```
m, n = (5, 3)
x = np.linspace(0, 1, m)
y = np.linspace(0, 1, n)
X, Y = np.meshgrid(x, y)
print(x)
print(y)
print(X)
print(Y)
```
输出：
```
[ 0.    0.25  0.5   0.75  1.  ]
[ 0.   0.5  1. ]
[[ 0.    0.25  0.5   0.75  1.  ]
 [ 0.    0.25  0.5   0.75  1.  ]
 [ 0.    0.25  0.5   0.75  1.  ]]
[[ 0.   0.   0.   0.   0. ]
 [ 0.5  0.5  0.5  0.5  0.5]
 [ 1.   1.   1.   1.   1. ]]

```

可以看到X和Y的shape都是3x5，用图的话更好理解：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/BCKE93iaDi.png?imageslim)

把 X 和 Y 画出来后，就可以看到网格了：

==没明白，为什么是可以把 X，Y 放到 plot 的参数里面？==

```
import matplotlib.pyplot as plt
%matplotlib inline
plt.style.use('ggplot')
plt.plot(X, Y, marker='.', color='blue', linestyle='none')
```

输出：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/Abe6fjCJd1.png?imageslim)



可以用 zip 得到网格平面上坐标点的数据：==这个有什么作用吗？==


```
z = [i for i in zip(X.flat, Y.flat)]
print(Z)
```

输出：

```
[(0.0, 0.0),
 (0.25, 0.0),
 (0.5, 0.0),
 (0.75, 0.0),
 (1.0, 0.0),
 (0.0, 0.5),
 (0.25, 0.5),
 (0.5, 0.5),
 (0.75, 0.5),
 (1.0, 0.5),
 (0.0, 1.0),
 (0.25, 1.0),
 (0.5, 1.0),
 (0.75, 1.0),
 (1.0, 1.0)]

```

好了，下面继续进入书中的内容



```
points = np.arange(-5, 5, 0.01) # 1000 equally spaced points
xs, ys = np.meshgrid(points, points) # xs和ys是一样的
print(xs)
print(ys)
```

输出：

```
[[-5.  , -5.  , -5.  , ..., -5.  , -5.  , -5.  ],
    [-4.99, -4.99, -4.99, ..., -4.99, -4.99, -4.99],
    [-4.98, -4.98, -4.98, ..., -4.98, -4.98, -4.98],
    ...,
    [ 4.97,  4.97,  4.97, ...,  4.97,  4.97,  4.97],
    [ 4.98,  4.98,  4.98, ...,  4.98,  4.98,  4.98],
    [ 4.99,  4.99,  4.99, ...,  4.99,  4.99,  4.99]]
[[-5.  , -5.  , -5.  , ..., -5.  , -5.  , -5.  ],
    [-4.99, -4.99, -4.99, ..., -4.99, -4.99, -4.99],
    [-4.98, -4.98, -4.98, ..., -4.98, -4.98, -4.98],
    ...,
    [ 4.97,  4.97,  4.97, ...,  4.97,  4.97,  4.97],
    [ 4.98,  4.98,  4.98, ...,  4.98,  4.98,  4.98],
    [ 4.99,  4.99,  4.99, ...,  4.99,  4.99,  4.99]]

```



```
z = np.sqrt(xs ** 2 + ys ** 2)
print(z)
```

输出：

```
[[ 7.07106781,  7.06400028,  7.05693985, ...,  7.04988652,
        7.05693985,  7.06400028],
    [ 7.06400028,  7.05692568,  7.04985815, ...,  7.04279774,
        7.04985815,  7.05692568],
    [ 7.05693985,  7.04985815,  7.04278354, ...,  7.03571603,
        7.04278354,  7.04985815],
    ...,
    [ 7.04988652,  7.04279774,  7.03571603, ...,  7.0286414 ,
        7.03571603,  7.04279774],
    [ 7.05693985,  7.04985815,  7.04278354, ...,  7.03571603,
        7.04278354,  7.04985815],
    [ 7.06400028,  7.05692568,  7.04985815, ...,  7.04279774,
        7.04985815,  7.05692568]]

```

这里我们用matplotlib把图画出来：==关于 plot 的用法还是要总结一下的==



```
plt.imshow(z, cmap=plt.cm.gray)
plt.colorbar()
plt.title("Image plot of $\sqrt{x^2 + y^2}$ for a grid of values")
```

输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/Gj3C5E0hAL.png?imageslim)


# 1 Expressing Conditional Logic as Array Operations (像数组操作一样表示逻辑条件)

`numpy.where` 函数是一个向量版的三相表达式，`x if condition else y`。假设我们有一个布尔数组和两个数组：



```
xarr = np.array([1.1, 1.2, 1.3, 1.4, 1.5])
yarr = np.array([2.1, 2.2, 2.3, 2.4, 2.5])
cond = np.array([True, False, True, True, False])

```

假设如果cond中为true，我们取 xarr 中对应的值，否则就取 yarr中的值。列表表达式的话会这么写：

```
result = [(x if c else y)
          for x, y, c in zip(xarr, yarr, cond)]# 嗯，这样也不错
result
```
输出：

```
[1.1000000000000001, 2.2000000000000002, 1.3, 1.3999999999999999, 2.5]
```

这么做的话会有很多问题：
1. 首先，对于很大的数组，会比较慢。
2. 对于多维数组不起作用。这个的确。
    但 `np.where` 能让我们写得更简洁：

```
result = np.where(cond, xarr, yarr)# 这也行！
result
```

输出：

```
array([ 1.1,  2.2,  1.3,  1.4,  2.5])
```

`np.where` 中第二个和第三个参数不用必须是数组。where 在数据分析中一个典型的用法是基于一个数组，产生一个新的数组值。假设我们有一个随机数字生成的矩阵，我们想要把所有的正数变为 2，所有的负数变为 -2。用 where 的话会非常简单：

```
arr = np.random.randn(4, 4)
print(arr)
print(arr>0)
print(np.where(arr>0,2,-2))
```
输出：
```
[[ 2.18194474,  0.15001978, -0.77191684,  0.18716397],
    [ 1.2083149 , -0.22911585,  1.30880201,  0.14197253],
    [ 0.65639111, -1.28394185,  0.65706167,  1.14277598],
    [-0.32639966, -0.26880881, -0.10225964,  0.4739671 ]]
[[ True,  True, False,  True],
    [ True, False,  True,  True],
    [ True, False,  True,  True],
    [False, False, False,  True]], dtype=bool
[[ 2,  2, -2,  2],
[ 2, -2,  2,  2],
[ 2, -2,  2,  2],
[-2, -2, -2,  2]]
```

我们可以结合标量和数组。比如只把整数变为2，其他仍未原来的数字：==这也行，厉害！应该是个常用的好方法==



```
np.where(arr > 0, 2, arr) # set only positive value to 2
```

输出：
```
array([[ 2.        ,  2.        , -0.77191684,  2.        ],
       [ 2.        , -0.22911585,  2.        ,  2.        ],
       [ 2.        , -1.28394185,  2.        ,  2.        ],
       [-0.32639966, -0.26880881, -0.10225964,  2.        ]])

```

# 2 Mathematical and Statistical Methods (数学和统计方法)

一些能计算统计值的数学函数能基于整个数组，或者沿着一个axis（轴）。可以使用 aggregations (often called reductions，汇总，或被叫做降维)，比如sum, mean, and std(标准差).

下面是一些aggregate statistics（汇总统计）：



```
arr = np.random.randn(5, 4)
print(arr)
print(arr.mean())
print(np.mean(arr))
print(arr.sum())
```

输出：

```
array([[-1.53575656, -1.39268394, -1.02284353, -1.03165049],
       [ 0.53301867,  0.50258973, -0.49389656,  0.24610963],
       [ 0.95377174, -1.57268184,  0.42969986,  1.22912566],
       [ 0.73686692, -2.82328155,  0.48018497, -1.38046692],
       [ 0.94164808,  0.19599722, -0.88779738, -0.87556277]])
-0.33838045197794597
-0.33838045197794597
-6.767609039558919

```

mean, sum这样的函数能接受 axis 作为参数来计算统计数字，返回的结果维度更少：==关于多维的瘦的 axis 到底指的是那个还是有些不清楚。==
```
arr.mean(axis=1)
arr.sum(axis=0)
```

输出：

```
array([-1.24573363,  0.19695537,  0.25997886, -0.74667415, -0.15642871])
array([ 1.62954886, -5.09006038, -1.49465263, -1.81244489])
```

这里 `arr.mean(1)` 表示，compute mean acros the columns(计算各列之间的平均值)。`arr.sum(0)` 表示，compute sum down the rows(计算各行总和)。

其他一些方法，像 cumsum 和 cumprod 不做汇总，而是产生一个中间结果的数组：==嗯，这个之前也见到过的==

```
arr = np.array([0, 1, 2, 3, 4, 5, 6, 7])
arr.cumsum()
```

输出：

```
array([ 0,  1,  3,  6, 10, 15, 21, 28])

```

上面的计算是一个累加的结果，`0+1=1，1+2=3，3+3=6` 以此类推。



对于多维数组，accumulation functions（累积函数）比如 cumsum，返回的是同样大小的数组，但是部分聚合会沿着指示的轴向较低维度进行切片：


==这个地方的 axis=0 和 axis =1 有点不理解，这个与之前的对于数组的一些操作的 axis 的说法冲突吗？最好总结一下==
```
arr = np.array([[0, 1, 2], [3, 4, 5], [6, 7, 8]])
print(arr)
print(arr.cumsum(axis=0)) # 沿着行加法
print(arr.cumprod(axis=1)) # 沿着列乘法

```

输出：

```
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
array([[ 0,  1,  2],
       [ 3,  5,  7],
       [ 9, 12, 15]])
array([[  0,   0,   0],
       [  3,  12,  60],
       [  6,  42, 336]])
```

这里有一些基本的统计计算方法：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/lADlhDfmAe.png?imageslim)

# 3 Methods for Boolean Arrays（布尔数组的方法）

sum 是用来计算布尔数组中有多少个 true 的：



```
arr = np.random.randn(100)
(arr > 0).sum() # Number of positive values

```

输出：

```
46

```

有两个其他方法，any 和 all，对于布尔数组特别有用。any 检测数组中只要有一个 ture 返回就是true，而 all 检测数组中都是 true 才会返回 true。



```
bools = np.array([False, False, True, False])
print(bools.any())
print(bools.all())
```
输出：

```
True
False
```

# 4 Sorting（排序）

numpy 中也有 sort 方法：

```
arr = np.random.randn(6)# 返回符合正态分布的数值
print(arr)
arr.sort()
print(arr)
```
输出：

```
array([ 1.93663555, -1.29810982,  0.83366006,  0.51674613,  2.32879117,
        1.07342758])
array([-1.29810982,  0.51674613,  0.83366006,  1.07342758,  1.93663555,
        2.32879117])
```

如果是多维数组，还可以按 axis 来排序：

```
arr = np.random.randn(5, 3)
print(arr)
arr.sort(1)
print(arr)
```
输出：

```
array([[-0.76658562, -1.00222899,  0.39039437],
       [ 0.23100317, -1.0581081 ,  1.69177329],
       [ 1.0239365 ,  0.84698669, -0.97911915],
       [ 0.76255951,  0.27828523,  0.41807172],
       [ 0.40792019, -1.19514714, -1.41666804]])
array([[-1.00222899, -0.76658562,  0.39039437],
       [-1.0581081 ,  0.23100317,  1.69177329],
       [-0.97911915,  0.84698669,  1.0239365 ],
       [ 0.27828523,  0.41807172,  0.76255951],
       [-1.41666804, -1.19514714,  0.40792019]])

```

上面是直接调用数组的sort方法，会改变原有数组的顺序。但如果使用 `np.sort()` 函数的话，会生成一个新的排序后的结果。==嗯，这个等同于对于列表使用 sort 会排序，但是 sorted 会生成一个新的 列表==

一个计算分位数的快捷方法是先给数组排序，然后选择某个排名的值：==什么是计算分位数？==



```
large_arr = np.random.randn(1000)
large_arr.sort()
large_arr[int(0.05 * len(large_arr))] # 5% quantile
```

输出：

```
-1.6908607973872243
```

# 5 Unique and Other Set Logic (单一性和其他集合逻辑)

Numpy也有一些基本的集合操作用于一维数组。`np.unique`，能返回排好序且不重复的值：



```
names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
print(np.unique(names))
ints = np.array([3, 3, 3, 2, 2, 1, 1, 4, 4])
print(np.unique(ints))
```

输出：
```
array(['Bob', 'Joe', 'Will'], dtype='<U4')
array([1, 2, 3, 4])

```

如果用纯python代码来实现的话，要这么写：
```
sorted(set(names))
```

输出：

```
['Bob', 'Joe', 'Will']

```

`np.in1d` ,  测试一个数组的值是否在另一个数组里，返回一个布尔数组：==有些6 的用法，什么场景下会使用这个呢？==

```
values = np.array([6, 0, 0, 3, 2, 5, 6])
np.in1d(values, [2, 3, 6])
```

输出：

```
array([ True, False, False,  True,  True, False,  True], dtype=bool)
```

这里是一些数组的集合操作：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/kjD8FCG0L9.png?imageslim)











# 4 File Input and Output with Arrays（通过数组来进行文件的输入和输出）



Numpy 能从磁盘直接存储和加载数据，不论是文本格式还是二进制模式。这里我们只考虑Numpy的二进制模式，因为大多数用户更喜欢用pandas或其他工具来加载 text 或 tabular 数据。==什么是 tabular 数据?表格类型的?不过的确,好像很少看到 用 numpy 加载数据的。==

np.save 和 np.load。数组会以未压缩的原始二进制模式被保存，后缀为.npy，即使保存的时候没有加后缀，也会被自动加上。==.npy 格式竟然是 numpy 的二进制模式保存的数据==

```
import numpy as np
arr = np.arange(10)
np.save('../examples/some_array', arr)
```

可以用np.load来加载数组：

```
np.load('../examples/some_array.npy')
```

输出：

```
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

用 np.savez 能保存多个数组，还可以指定数组对应的关键字，不过是未压缩的 npz 格式：



```
np.savez('../examples/array_archive.npz', a=arr, b=arr)
```

加载.npz文件的时候，得到一个dict object：

```
arch = np.load('../examples/array_archive.npz')
arch['b']
```

输出：

```
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```

可以用 np.savez_compressed 来压缩文件：

```
np.savez_compressed('../examples/array_compressed.npz', a=arr, b=arr)
```

== 这个地方是不是缺少了一些？==





# 5 线性代数



在MATLAB里，`*` 代表矩阵乘法。但是在numpy里，`*` 表示 element-wise prodct。要想做到矩阵乘法，要用多函数dot:


```
import numpy as np
x = np.array([[1., 2., 3.], [4., 5., 6.]])
y = np.array([[6., 23.], [-1, 7], [8, 9]])
print(x)
print(y)
print(x.dot(y))
```

输出：

```
array([[ 1.,  2.,  3.],
       [ 4.,  5.,  6.]])
array([[  6.,  23.],
       [ -1.,   7.],
       [  8.,   9.]])
array([[  28.,   64.],
       [  67.,  181.]])

```

`x.dot(y)` 等同于 `np.dot(x, y)`:

```
np.dot(x, y)
```

输出：

```
array([[  28.,   64.],
       [  67.,  181.]])
```

一个二维数组和一个一维数组的矩阵乘法，得到一个一维数组：

```
np.dot(x, np.ones(3))
```

输出：

```
array([  6.,  15.])
```

`@` 作为一个中缀计算符（鬼知道这是什么东西），也能实现矩阵乘法：==这个 中缀计算符是什么？==



```
x @ np.ones(3)
```

输出：

```
array([  6.,  15.])
```

`np.linalg` 能用来做矩阵分解，以及比如转置和求秩之类的事情：==没有很理解==

```
from numpy.linalg import inv, qr
# X = np.round(np.random.randn(5, 5), 3) # 这里我们用np.round控制小数点后的位数，看起来更舒服一些
X = np.random.randn(5, 5)
print(X)
mat = X.T.dot(X)  #X.T.dot(X) 计算的是X和X的转置的矩阵乘法。
print(np.round(mat, 2))
print(np.round(inv(mat), 2))
print(np.round(mat.dot(inv(mat)), 2)) # 这个mat.dot 是什么？
q, r = qr(mat)
np.round(r, 2)
```

输出：

```
[[ 0.42973283 -1.00692483  0.67366885 -0.67621757  1.02193322]
 [-0.81907848 -0.32441427  1.16429389 -0.71295303 -0.0890279 ]
 [-0.90356671 -0.74007223 -1.11302966 -1.05284735 -1.03173077]
 [-0.64995677  1.76047177 -1.20316653 -1.03918089  0.06206442]
 [-0.64331742  0.71141435  1.6526956   0.59369371  2.07610345]]
[[ 2.51 -1.1   0.06  1.54  0.07]
 [-1.1   5.27 -1.17  0.28  1.35]
 [ 0.06 -1.17  7.23  2.12  5.09]
 [ 1.54  0.28  2.12  3.51  1.63]
 [ 0.07  1.35  5.09  1.63  6.43]]
[[ 0.89  0.37  0.4  -0.54 -0.27]
 [ 0.37  0.46  0.39 -0.28 -0.34]
 [ 0.4   0.39  0.68 -0.37 -0.53]
 [-0.54 -0.28 -0.37  0.68  0.19]
 [-0.27 -0.34 -0.53  0.19  0.6 ]]
[[ 1.  0. -0.  0.  0.]
 [-0.  1. -0.  0. -0.]
 [ 0. -0.  1.  0. -0.]
 [ 0. -0.  0.  1. -0.]
 [-0. -0. -0.  0.  1.]]

```



一些常用的numpy.linalg函数：==都需要总结下==

- ![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/8ah15e1HEA.png?imageslim)
- ![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/7k77LE83Ig.png?imageslim)



# 4.6 Pseudorandom Number Generation（伪随机数生成）

numpy.random模块提供了很多生成随机数的函数，可以选择生成符合某种概率分布的随机数。比如我们可以用normal得到一个4 x 4的，符合标准正态分布的数组：


```
import numpy as np
samples = np.random.normal(size=(4, 4))
print(samples)
```
输出：
```
array([[ 0.5382462 , -0.79452471, -0.07993797,  0.72243531],
       [ 0.87180676,  1.61663011, -0.62169955,  1.73880636],
       [ 1.88294218,  0.07432438,  1.63474848,  0.23519213],
       [ 0.92847885, -0.45791646,  0.63965317, -0.23654448]])
```

相对的，python 内建的 random 模块一次只能生成一个样本。在生成大量样本方法，numpy.random 是非常快的：==没想到 np 的random 模块还有这个好处==

```
import numpy as np
from random import normalvariate
N = 1000000
%timeit sample = [normalvariate(0, 1) for _ in range(N)]
%timeit np.random.normal(size=N)
```
输出：==可见这的快好多==
```
1 loop, best of 3: 1.6 s per loop
10 loops, best of 3: 54.9 ms per loop
```

之所以称之为伪随机数，是因为随机数生成算法是根据seed来生成的。也就是说，只要 seed 设置一样，每次生成的随机数是相同的：


```
np.random.seed(1234)

```

当然，这个seed是全局的，如果想要避免全局状态，可以用 numpy.random.RandomState 来创建一个独立的生成器：==还有这个函数，之前不知道==

```
rng = np.random.RandomState(1234)
rng.randn(10)
```
输出：
```
array([ 0.47143516, -1.19097569,  1.43270697, -0.3126519 , -0.72058873,
        0.88716294,  0.85958841, -0.6365235 ,  0.01569637, -2.24268495])
```

下面是是写numpy.random里的函数：==还是要全面总结下的，现在这个还是不行的==

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/JCHcjAcig3.png?imageslim)



# 一个例子：随机漫步

这个例子让我了解一个在实际任务中如何利用数组操作。首先一个最简单的随机漫步：从 0 开始，步幅为 1 和 -1，以相同的概率出现。

下面是纯 python的实现方法，1000步：


```python
import random
position = 0
walk = [0,]
steps = 1000
for i in range(steps):
    step = 1 if random.randint(0, 1) else -1
    position += step
    walk.append(position)
import matplotlib.pyplot as plt
%matplotlib inline
walk[:5]
plt.plot(walk[:100])
```
输出：
```
[0, -1, -2, -3, -2]
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180616/ibe9cadaCm.png?imageslim)

随机漫步其实就是一个简单的累加。而用 np.random 能更快：


```python
import numpy as np
nsteps = 1000
draws = np.random.randint(0, 2, size=nsteps)
steps = np.where(draws > 0, 1, -1)
walk = steps.cumsum()# 这样就得到了随机游走
```

我们能直接从中得到一些统计数据，比如最大值和最小值：

```python
walk.min()
walk.max()
```
输出：
```text
-57
7
```

一个更复杂的统计值是在哪一步 random walk 到达了一个指定值。我们想知道从 0 走出 10 步用了多久，不论是正方向还是负方向。`np.abs(walk) >= 10`给我们一个布尔数组告诉我们是否超过10，但我们想要第一次出现的10或-10。因此，我们利用argmax来计算，这个会返回布尔数组中最大值的索引(Ture是最大值)：==嗯，对 argmax 前面没有怎么讲==


```
(np.abs(walk) >= 10).argmax()
```
输出：
```
71
```

注意，使用argmax并不总是效率的，因为它总会搜索整个数组。==什么意思？==在这里例子里，一旦True被找到了，我们就返回为最大值。

## Simulating Many Random Walks at Once（一次模拟多个随机漫步）

假设我们一次要模拟 5000 个随机漫步。传入一个 2-tuple，np.random 会生成一个二维数组，然后我们沿着每行来计算累加，这样就能一次模拟5000个：


```python
nwalks = 5000
nsteps = 1000
draws = np.random.randint(0, 2, size=(nwalks, nsteps)) # 0 or 1 竟然还可以这样
steps = np.where(draws > 0, 1, -1)
walks = steps.cumsum(1)
print(walks)
```

输出：
```
array([[ -1,  -2,  -3, ..., -24, -25, -26],
       [ -1,  -2,  -1, ..., -10,  -9,  -8],
       [  1,   0,   1, ...,  -4,  -3,  -4],
       ...,
       [  1,   0,   1, ...,  52,  51,  52],
       [ -1,   0,   1, ..., -26, -25, -26],
       [ -1,   0,  -1, ..., -30, -29, -30]])
```

找到所有漫步中的最大值和最小值：


```
walks.max()
walks.min()
```

输出：
```
115
-129

```

在这些漫步模拟中，我们想找到30步以上的。用 any 方法：==这个地方的any 是什么意思？==

```
hits30 = (np.abs(walks) >= 30).any(1)# 这个any 是什么意思？
print(hits30)
print(hits30.sum())
```
输出：
```
array([ True, False, False, ...,  True,  True,  True], dtype=bool)
3423
```

上面的step只是像翻硬币一样二选一，我们也可以用一个概率函数来生成：

```
steps = np.random.normal(loc=0, scale=0.25, size=(nwalks, nsteps))# 什么意思？
```





# REF
- [数据 Numpy & Pandas](https://morvanzhou.github.io/tutorials/data-manipulation/np-pd/)  **（推荐入门看这个人的莫烦python）**
- [Python Numpy Tutorial](http://cs231n.github.io/python-numpy-tutorial/) （这个没有补充进去，暂时记在这里）
- Wiki：[NumPy](https://www.wikiwand.com/zh-hans/NumPy)
- Baidu：[NumPy](https://baike.baidu.com/item/numpy/5678437)
- [使用 Python 进行科学计算：NumPy入门](http://codingpy.com/article/an-introduction-to-numpy/)
- [NumPy用户指南](http://usyiyi.cn/translate/NumPy_v111/user/index.html)
