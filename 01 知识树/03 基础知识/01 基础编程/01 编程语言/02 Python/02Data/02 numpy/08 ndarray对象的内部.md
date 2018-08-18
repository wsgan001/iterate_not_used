
# APPENDIX A Advanced NumPy（附录A：高级NumPy用法）


# A.1 ndarray Object Internals（ndarray对象的内部）


Numpy的ndarray可以让我们把同类数据（homogeneous data），相邻的或相隔的（contiguous or strided），作为一个多维的数组对象（array object）。数据类型，即dtype，会决定一个数据以何种类型被读取，比如浮点，整数，布尔，或其他一些数据类型。

> 这里strided作为形容词，我译为相隔的，名词的情况下，我译为跨度

ndarray之所以很灵活，是因为每一个数组对象是一块数据（a block of data）的间隔视图（strided view）。我们可能会感到奇怪，为什么一个数组的视图（array view） `arr[::2, ::-1]`不会复制任何数据。其原因是ndarray不仅是一大块内存和一种数据类型，它还有跨度信息（striding information），能让数组以不同的跨度（step size）在内存中进行移动。更确切一点说，ndarray内部包含以下内容：

- 一个数据指针（a pointer to data），在RAM中的一块数据或在内存映射文件中的数据。
- 数据类型（data type or dtype），用来描述数组中固定大小的单元（fixed-size value cells）。
- 一个用来描述数组形状（shape）的元组（tuple）。
- 一个保存跨度（strides）的元组，整数表明每一步的字节大小，好让元素沿着一个方向推进。

看下图显示了一个ndarray对象的内在构成：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/kHekAHmg8e.png?imageslim)

比如，一个10x5的数组，形状是(10, 5):



```python
import numpy as np
```


```python
np.ones((10, 5)).shape
```




    (10, 5)



一个典型的3x4x5数组，类型为float64(8-byte)，跨度为(160, 40, 8)（对于跨度的使用要小心，通常情况下，在某一维度上的跨度越大，运算消耗越大）：

> 一个值是8字节，所以一个数字之间的跨度是8，在第一个维度上，有三个4x5的矩阵，所以每个之间是4x5x8=160个字节的跨度。


```python
np.ones((3, 4, 5), dtype=np.float64).strides
```




    (160, 40, 8)




```python
np.ones((3, 4, 5))
```




    array([[[ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.]],

           [[ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.]],

           [[ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.],
            [ 1.,  1.,  1.,  1.,  1.]]])



尽管很少有numpy用户对数组跨度感兴趣，但跨度对于构建zero-copy array views是很重要的。

# 1 NumPy dtype Hierarchy（NumPy dtype层级）

我们有时可能会需要查看一个数组里是否包含整数类型，浮点数类型的数字，又或者是字符串。而且浮点数又有很多类型（float16,float128），一个个检查是很累人的。不过dtypes有一个超类（superclass），其中有np.integer和np.floating，可以用来与np.issubdtype函数进行连接：


```python
ints = np.ones(10, dtype=np.uint16)
floats = np.ones(10, dtype=np.float32)
```


```python
np.issubdtype(ints.dtype, np.integer)
```




    True




```python
np.issubdtype(floats.dtype, np.floating)
```




    True



可以通过mro方法，来查看一个dtype的所有父类：


```python
np.float64.mro()
```




    [numpy.float64,
     numpy.floating,
     numpy.inexact,
     numpy.number,
     numpy.generic,
     float,
     object]



因此，我们可以得到：


```python
np.issubdtype(ints.dtype, np.number)
```




    True



大部分numpy用户不用非得记住这些，但是了解一下有时候会很方便。下面是一副关于dtype层级和父子关系的示意图：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/mkA90mL8D2.png?imageslim)
