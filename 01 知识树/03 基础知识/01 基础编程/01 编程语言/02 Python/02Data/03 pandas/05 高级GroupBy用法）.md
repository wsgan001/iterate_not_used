
# 12.2 Advanced GroupBy Use（高级GroupBy用法）

我们已经在第十章讨论了groupby的一些用法，这里还有一些技巧可能会用得到。

# 1 Group Transforms and “Unwrapped” GroupBys（组变换和无包装的GroupBy）

在第十章里，使用apply方法在组上进行转换操作的。还有一个内建的方法叫transform，和apply相同，但是在一些函数的用法上有一些限制：

- 可以产生一个标量，将数据广播（broadcast）到与组一样的形状（这里的broadcast可以理解为改变数据形状的方法，感兴趣的可以直接搜索 numpy broadcast）
- 可以产生一个和输入的组一样形状的对象
- 不能对输入进行改变

举个例子：


```python
import numpy as np
import pandas as pd
```


```python
df = pd.DataFrame({'key': ['a', 'b', 'c'] * 4,
                   'value': np.arange(12.)})
df
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
      <th>key</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>c</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>a</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>b</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>c</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>a</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>b</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>c</td>
      <td>11.0</td>
    </tr>
  </tbody>
</table>
</div>



通过key来计算组的平均值：


```python
g = df.groupby('key').value
g.mean()
```




    key
    a    4.5
    b    5.5
    c    6.5
    Name: value, dtype: float64



假设我们想要产生一个和df['value']一样大小的Series，不过要用key分组后的平均值来替换。我们可以把函数lambda x: x.mean()给transform：


```python
g.transform(lambda x: x.mean())
```




    0     4.5
    1     5.5
    2     6.5
    3     4.5
    4     5.5
    5     6.5
    6     4.5
    7     5.5
    8     6.5
    9     4.5
    10    5.5
    11    6.5
    Name: value, dtype: float64



对于内建的聚合函数，我们可以传入一个字符串别名，就像使用groupby agg方法的时候一样：


```python
g.transform('mean')
```




    0     4.5
    1     5.5
    2     6.5
    3     4.5
    4     5.5
    5     6.5
    6     4.5
    7     5.5
    8     6.5
    9     4.5
    10    5.5
    11    6.5
    Name: value, dtype: float64



就像apply，transform能用那些返回Series的函数，但是结果的大小和输入的必须一样。例如，我们通过一个lambda函数令每个小组都乘2：


```python
g.transform(lambda x: x * 2)
```




    0      0.0
    1      2.0
    2      4.0
    3      6.0
    4      8.0
    5     10.0
    6     12.0
    7     14.0
    8     16.0
    9     18.0
    10    20.0
    11    22.0
    Name: value, dtype: float64



一个更复杂的例子，我们可以按降序来计算每一个组：


```python
g.transform(lambda x: x.rank(ascending=False))
```




    0     4.0
    1     4.0
    2     4.0
    3     3.0
    4     3.0
    5     3.0
    6     2.0
    7     2.0
    8     2.0
    9     1.0
    10    1.0
    11    1.0
    Name: value, dtype: float64



考虑一个包含简单聚合的分组转换函数：


```python
def normalize(x):
    return (x - x.mean()) / x.std()
```

使用transform或apply，都能得到一样的结果：


```python
g.transform(normalize)
```




    0    -1.161895
    1    -1.161895
    2    -1.161895
    3    -0.387298
    4    -0.387298
    5    -0.387298
    6     0.387298
    7     0.387298
    8     0.387298
    9     1.161895
    10    1.161895
    11    1.161895
    Name: value, dtype: float64




```python
g.apply(normalize)
```




    0    -1.161895
    1    -1.161895
    2    -1.161895
    3    -0.387298
    4    -0.387298
    5    -0.387298
    6     0.387298
    7     0.387298
    8     0.387298
    9     1.161895
    10    1.161895
    11    1.161895
    Name: value, dtype: float64



内建的聚合函数，比如mean, sum经常比一般的apply函数要快。而是用transform的话，会更快一些。这就需要我们使用无包装的组操作（upwrapped group operation）：


```python
g.transform('mean')
```




    0     4.5
    1     5.5
    2     6.5
    3     4.5
    4     5.5
    5     6.5
    6     4.5
    7     5.5
    8     6.5
    9     4.5
    10    5.5
    11    6.5
    Name: value, dtype: float64




```python
normalized = (df['value'] - g.transform('mean')) / g.transform('std')
normalized
```




    0    -1.161895
    1    -1.161895
    2    -1.161895
    3    -0.387298
    4    -0.387298
    5    -0.387298
    6     0.387298
    7     0.387298
    8     0.387298
    9     1.161895
    10    1.161895
    11    1.161895
    Name: value, dtype: float64



一个无包装的组操作可能会涉及多个组聚合操作，不过向量化操作会胜过这种操作。

# 2 Grouped Time Resampling（分组时间重采样）

对于时间序列数据，resample方法是一个基于时间的组操作。这里有一个样本表格： 


```python
N = 15
times = pd.date_range('2017-05-20 00:00', freq='1min', periods=N)
df = pd.DataFrame({'time': times, 'value': np.arange(N)})
df
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
      <th>time</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2017-05-20 00:00:00</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-05-20 00:01:00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017-05-20 00:02:00</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-05-20 00:03:00</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017-05-20 00:04:00</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2017-05-20 00:05:00</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2017-05-20 00:06:00</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2017-05-20 00:07:00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2017-05-20 00:08:00</td>
      <td>8</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2017-05-20 00:09:00</td>
      <td>9</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2017-05-20 00:10:00</td>
      <td>10</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2017-05-20 00:11:00</td>
      <td>11</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2017-05-20 00:12:00</td>
      <td>12</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2017-05-20 00:13:00</td>
      <td>13</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2017-05-20 00:14:00</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
</div>



我们用time索引，然后重采样：


```python
df.set_index('time').resample('5min').count()
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
      <th>value</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-05-20 00:00:00</th>
      <td>5</td>
    </tr>
    <tr>
      <th>2017-05-20 00:05:00</th>
      <td>5</td>
    </tr>
    <tr>
      <th>2017-05-20 00:10:00</th>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



假设一个DataFrame包含多个时间序列，用多一个key列来表示：


```python
df2 = pd.DataFrame({'time': times.repeat(3),
                    'key': np.tile(['a', 'b', 'c'], N), 
                    'value': np.arange(N * 3.)})
df2[:7]
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
      <th>key</th>
      <th>time</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2017-05-20 00:00:00</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>2017-05-20 00:00:00</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>c</td>
      <td>2017-05-20 00:00:00</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>2017-05-20 00:01:00</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>2017-05-20 00:01:00</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>2017-05-20 00:01:00</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>a</td>
      <td>2017-05-20 00:02:00</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>



想要对key列的值做重采样，我们引入pandas.TimeGrouper对象：


```python
time_key = pd.TimeGrouper('5min')
```

然后设置time为索引，对key和time_key做分组，然后聚合：


```python
resampled = (df2.set_index('time')
             .groupby(['key', time_key])
             .sum())
resampled
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
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>key</th>
      <th>time</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">a</th>
      <th>2017-05-20 00:00:00</th>
      <td>30.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:05:00</th>
      <td>105.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:10:00</th>
      <td>180.0</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">b</th>
      <th>2017-05-20 00:00:00</th>
      <td>35.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:05:00</th>
      <td>110.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:10:00</th>
      <td>185.0</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">c</th>
      <th>2017-05-20 00:00:00</th>
      <td>40.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:05:00</th>
      <td>115.0</td>
    </tr>
    <tr>
      <th>2017-05-20 00:10:00</th>
      <td>190.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
resampled.reset_index()
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
      <th>key</th>
      <th>time</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2017-05-20 00:00:00</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>a</td>
      <td>2017-05-20 00:05:00</td>
      <td>105.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>a</td>
      <td>2017-05-20 00:10:00</td>
      <td>180.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>b</td>
      <td>2017-05-20 00:00:00</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>2017-05-20 00:05:00</td>
      <td>110.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>b</td>
      <td>2017-05-20 00:10:00</td>
      <td>185.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>c</td>
      <td>2017-05-20 00:00:00</td>
      <td>40.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>c</td>
      <td>2017-05-20 00:05:00</td>
      <td>115.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>c</td>
      <td>2017-05-20 00:10:00</td>
      <td>190.0</td>
    </tr>
  </tbody>
</table>
</div>



使用TimeGrouper的一个限制是时间必须是Series或DataFrame的索引才行。
