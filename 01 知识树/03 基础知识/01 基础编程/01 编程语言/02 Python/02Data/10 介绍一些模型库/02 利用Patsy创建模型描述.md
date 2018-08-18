
# 13.2 Creating Model Descriptions with Patsy（利用Patsy创建模型描述）

Patsy是一个python库，用于描述统计模型（尤其是线性模型），方法是通过一个叫做公式语法（formula syntax）的字符串来描述。这种公式语法的灵感来源于R和S语言中的公式语法。

Patsy的公式是有特殊格式的字符串，像下面这样：

    y ~ x0 + x1
    
这种a + b的语法并不代表将a和b相加，而是代表为模型创建的设计矩阵的术语（terms in the design matrix）。patsy.dmatrices函数，取一个公式字符串和一个数据集（可以使DataFrame或dict），然后为线性模型产生设计矩阵：


```python
import numpy as np
import pandas as pd
```


```python
data = pd.DataFrame({'x0': [1, 2, 3, 4, 5],
                     'x1': [0.01, -0.01, 0.25, -4.1, 0.], 
                     'y': [-1.5, 0., 3.6, 1.3, -2.]})
```


```python
data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x1</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.01</td>
      <td>-1.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>-0.01</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>0.25</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>-4.10</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.00</td>
      <td>-2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
import patsy
```


```python
y, X = patsy.dmatrices('y ~ x0 + x1', data)
```

我们得到：


```python
y
```




    DesignMatrix with shape (5, 1)
         y
      -1.5
       0.0
       3.6
       1.3
      -2.0
      Terms:
        'y' (column 0)




```python
X
```




    DesignMatrix with shape (5, 3)
      Intercept  x0     x1
              1   1   0.01
              1   2  -0.01
              1   3   0.25
              1   4  -4.10
              1   5   0.00
      Terms:
        'Intercept' (column 0)
        'x0' (column 1)
        'x1' (column 2)



这些Patsy DesignMatrix实例是Numpy的ndarrays，附有额外的元数据（metadata）:


```python
np.asarray(y)
```




    array([[-1.5],
           [ 0. ],
           [ 3.6],
           [ 1.3],
           [-2. ]])




```python
np.asarray(X)
```




    array([[ 1.  ,  1.  ,  0.01],
           [ 1.  ,  2.  , -0.01],
           [ 1.  ,  3.  ,  0.25],
           [ 1.  ,  4.  , -4.1 ],
           [ 1.  ,  5.  ,  0.  ]])



我们可能奇怪X中的Intercept是从哪里来的。这其实是线性模型的一个惯例，比如普通最小二乘回归法（ordinary least squares regression）。我们可以去掉这个截距（intercept），通过加添术语`+0`给模型：


```python
patsy.dmatrices('y ~ x0 + x1 + 0', data)[1]
```




    DesignMatrix with shape (5, 2)
      x0     x1
       1   0.01
       2  -0.01
       3   0.25
       4  -4.10
       5   0.00
      Terms:
        'x0' (column 0)
        'x1' (column 1)



这种Patsy对象可以直接传入一个算法，比如numpy.linalg.lstsq，来进行普通最小二乘回归的计算：


```python
coef, resid, _, _ = np.linalg.lstsq(X, y)
```


```python
coef
```




    array([[ 0.31290976],
           [-0.07910564],
           [-0.26546384]])




```python
coef = pd.Series(coef.squeeze(), index=X.design_info.column_names)
coef
```




    Intercept    0.312910
    x0          -0.079106
    x1          -0.265464
    dtype: float64



# 1 Data Transformations in Patsy Formulas（Patsy公式的数据变换）

我们可以把python和Patsy公式混合起来。当评估公式的时候，库会尝试找到封闭域中的公式：


```python
y, X = patsy.dmatrices('y ~ x0 + np.log(np.abs(x1) + 1)', data)
```


```python
X
```




    DesignMatrix with shape (5, 3)
      Intercept  x0  np.log(np.abs(x1) + 1)
              1   1                 0.00995
              1   2                 0.00995
              1   3                 0.22314
              1   4                 1.62924
              1   5                 0.00000
      Terms:
        'Intercept' (column 0)
        'x0' (column 1)
        'np.log(np.abs(x1) + 1)' (column 2)



一些常用的变量变换，包括标准化（standardizing (平均值0，方差1）和中心化（减去平均值）。Patsy有内建的函数可以做到这些：


```python
y, X = patsy.dmatrices('y ~ standardize(x0) + center(x1)', data)
```


```python
X
```




    DesignMatrix with shape (5, 3)
      Intercept  standardize(x0)  center(x1)
              1         -1.41421        0.78
              1         -0.70711        0.76
              1          0.00000        1.02
              1          0.70711       -3.33
              1          1.41421        0.77
      Terms:
        'Intercept' (column 0)
        'standardize(x0)' (column 1)
        'center(x1)' (column 2)



作为建模的一部分，我们可能会在一个数据及上训练模型，然后在另一个数据及上评价模型。当使用中心化或标准化这样的转换时，我们必须注意，必须用模型在新数据集上做预测。这叫做状态变换（stateful transformations）。因为我们必须用原本在训练集上得到的平均值和标准差，用在新的数据集上。

通过保存原先数据集中的信息，patsy.build_design_matrices函数能把变换用在新的数据集上：


```python
new_data = pd.DataFrame({
        'x0': [6, 7, 8, 9], 
        'x1': [3.1, -0.5, 0, 2.3],
        'y': [1, 2, 3, 4]})
```


```python
new_X = patsy.build_design_matrices([X.design_info], new_data)
new_X
```




    [DesignMatrix with shape (4, 3)
       Intercept  standardize(x0)  center(x1)
               1          2.12132        3.87
               1          2.82843        0.27
               1          3.53553        0.77
               1          4.24264        3.07
       Terms:
         'Intercept' (column 0)
         'standardize(x0)' (column 1)
         'center(x1)' (column 2)]



因为加号在Patsy公式中不代表加法，如果想要把两个列通过名字相加，必须把他们用I函数包起来：


```python
y, X = patsy.dmatrices('y ~ I(x0 + x1)', data)
X
```




    DesignMatrix with shape (5, 2)
      Intercept  I(x0 + x1)
              1        1.01
              1        1.99
              1        3.25
              1       -0.10
              1        5.00
      Terms:
        'Intercept' (column 0)
        'I(x0 + x1)' (column 1)



Patsy有一些其他的内建转换，得来patsy.builtins模块里。更多的信息请参考文档。

Categorical数据有特殊的类用于变换，下面进行介绍。

# 2 Categorical Data and Patsy（Categorical数据和Patsy）

非数值型数据可以通过很多种方式变为一个模型设计矩阵。这个话题很大，这里只做简单介绍。

当我们在Patsy公式中使用非数值术语时，这些类型数据默认会被转换为哑变量。如果有截距，一个层级上的截距会被舍弃，防止出现共线性：


```python
data = pd.DataFrame({'key1': ['a', 'a', 'b', 'b', 'a', 'b', 'a', 'b'], 
                     'key2': [0, 1, 0, 1, 0, 1, 0, 0], 
                     'v1': [1, 2, 3, 4, 5, 6, 7, 8],
                     'v2': [-1, 0, 2.5, -0.5, 4.0, -1.2, 0.2, -1.7] })
```


```python
y, X = patsy.dmatrices('v2 ~ key1', data)
X
```




    DesignMatrix with shape (8, 2)
      Intercept  key1[T.b]
              1          0
              1          0
              1          1
              1          1
              1          0
              1          1
              1          0
              1          1
      Terms:
        'Intercept' (column 0)
        'key1' (column 1)



如果从模型中舍弃截距，每个类型的列会被包含在模型设计矩阵中：


```python
y, X = patsy.dmatrices('v2 ~ key1 + 0', data)
X
```




    DesignMatrix with shape (8, 2)
      key1[a]  key1[b]
            1        0
            1        0
            0        1
            0        1
            1        0
            0        1
            1        0
            0        1
      Terms:
        'key1' (columns 0:2)



数值型列可以通过C函数，变为类型列：


```python
y, X = patsy.dmatrices('v2 ~ C(key2)', data)
X
```




    DesignMatrix with shape (8, 2)
      Intercept  C(key2)[T.1]
              1             0
              1             1
              1             0
              1             1
              1             0
              1             1
              1             0
              1             0
      Terms:
        'Intercept' (column 0)
        'C(key2)' (column 1)



当我们在一个模型中使用多个类型术语时，会变得更复杂一些，之前用`key1:key2`的形式来包含有交集的术语，这种方法可以用于使用多个术语，例如，一个方法分析模型（analysis of variance (ANOVA) models）：


```python
data['key2'] = data['key2'].map({0: 'zero', 1: 'one'})
data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key1</th>
      <th>key2</th>
      <th>v1</th>
      <th>v2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>zero</td>
      <td>1</td>
      <td>-1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>a</td>
      <td>one</td>
      <td>2</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>b</td>
      <td>zero</td>
      <td>3</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>b</td>
      <td>one</td>
      <td>4</td>
      <td>-0.5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>a</td>
      <td>zero</td>
      <td>5</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>b</td>
      <td>one</td>
      <td>6</td>
      <td>-1.2</td>
    </tr>
    <tr>
      <th>6</th>
      <td>a</td>
      <td>zero</td>
      <td>7</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>b</td>
      <td>zero</td>
      <td>8</td>
      <td>-1.7</td>
    </tr>
  </tbody>
</table>
</div>




```python
y, X = patsy.dmatrices('v2 ~ key1 + key2', data)
X
```




    DesignMatrix with shape (8, 3)
      Intercept  key1[T.b]  key2[T.zero]
              1          0             1
              1          0             0
              1          1             1
              1          1             0
              1          0             1
              1          1             0
              1          0             1
              1          1             1
      Terms:
        'Intercept' (column 0)
        'key1' (column 1)
        'key2' (column 2)




```python
y, X = patsy.dmatrices('v2 ~ key1 + key2 + key1:key2', data)
X
```




    DesignMatrix with shape (8, 4)
      Intercept  key1[T.b]  key2[T.zero]  key1[T.b]:key2[T.zero]
              1          0             1                       0
              1          0             0                       0
              1          1             1                       1
              1          1             0                       0
              1          0             1                       0
              1          1             0                       0
              1          0             1                       0
              1          1             1                       1
      Terms:
        'Intercept' (column 0)
        'key1' (column 1)
        'key2' (column 2)
        'key1:key2' (column 3)



Patsy还提供一些其他转换类型数据的方案，包括按特定顺序来变换。具体的可以查看文档。
