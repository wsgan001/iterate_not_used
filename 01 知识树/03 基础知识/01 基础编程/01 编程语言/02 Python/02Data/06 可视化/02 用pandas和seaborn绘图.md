
# 9.2 Plotting with pandas and seaborn（用pandas和seaborn绘图）

matplotlib是一个相对底层的工具。pandas自身有内建的可视化工具。另一个库seaborn则是用来做一些统计图形。

> 导入seaborn会改变matlotlib默认的颜色和绘图样式，提高可读性和美感。即使不适用seaborn的API，也可以利用seaborn来提高可视化的效果。

# 1 Line Plots（线图）

Series和DataFrame各自都有plot属性，用来做一些比较基本的绘图类型。默认，plot()会绘制线图：


```python
%matplotlib inline
# 如果不添加这句，是无法直接在jupyter里看到图的
```


```python
import numpy as np
import pandas as pd
```


```python
s = pd.Series(np.random.randn(10).cumsum(), index=np.arange(0, 100, 10))
```


```python
s.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x112e7f4a8>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/f8dHcICFBH.png?imageslim)

Series对象的index（索引），被matplotlib用来当做x轴，当然，我们也可以自己设定不这么做，use_index=False。x轴的ticks（标记）和limits（范围）能通过xticks和xlim选项来设定，而y轴的可以用yticks和ylim来设定。下面是关于plot的一些选项。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/BJ71dfCCcK.png?imageslim)

大部分的pandas绘图方法接受一个ax参数，可以作为一个matplotlib subplot对象。这给我们更强的灵活性在gird layout（网格样式）中放置subplot。

DataFrame的plot方法，会把每一列画出一条线，所有的线会画在同一个subplot（子图）上，而且可以添加legend（图例）：


```python
df = pd.DataFrame(np.random.randn(10, 4).cumsum(0),
                  columns=['A', 'B', 'C', 'D'],
                  index=np.arange(0, 100, 10))
```


```python
df.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x113025b38>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/C1Ek1kfDL8.png?imageslim)

plot属性中，包含一组方法，来画出不同类型的绘图。例如，df.plot()等同于df.plot.line()。

> 一些额外的关键字可以在plot里设定，并会被matplotlib函数执行，所以我们可以学习matplotlib API来定制化想要的绘图。

DataFrame有一些选项在处理列的时候提供了灵活性；例如，是否把所有列都画在一个子图中，或者把不同列画在不同的子图中。下图有更多的设定：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/H8aij3LjGA.png?imageslim)

# 2 Bar Plots（条形图）

plot.bar()和plot.barh()分别绘制垂直和水平的条形图。这种情况下，series或DataFrame的index会被用来作为x(bar)或y(barh)的ticks（标记）：


```python
import matplotlib.pyplot as plt
```


```python
fig, axes = plt.subplots(2, 1)
data = pd.Series(np.random.rand(16), index=list('abcdefghijklmnop'))
data.plot.bar(ax=axes[0], color='k', alpha=0.7)
data.plot.barh(ax=axes[1], color='k', alpha=0.7)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1146a6748>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/A9Lfl1I1fe.png?imageslim)

color='k'设置颜色为黑，而alpha=0.7则设置局部透明度（靠近1越明显，靠近0则虚化）。

对于DataFrame，条形图绘图会把每一行作为一个组画出来：


```python
df = pd.DataFrame(np.random.rand(6, 4),
                  index=['one', 'two', 'three', 'four', 'five', 'six'],
                  columns=pd.Index(['A', 'B', 'C', 'D'], name='Genus'))
```


```python
df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Genus</th>
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>one</th>
      <td>0.857118</td>
      <td>0.055367</td>
      <td>0.232816</td>
      <td>0.007077</td>
    </tr>
    <tr>
      <th>two</th>
      <td>0.554345</td>
      <td>0.491691</td>
      <td>0.515788</td>
      <td>0.217826</td>
    </tr>
    <tr>
      <th>three</th>
      <td>0.843414</td>
      <td>0.240387</td>
      <td>0.070781</td>
      <td>0.074844</td>
    </tr>
    <tr>
      <th>four</th>
      <td>0.180180</td>
      <td>0.976074</td>
      <td>0.135649</td>
      <td>0.471574</td>
    </tr>
    <tr>
      <th>five</th>
      <td>0.670680</td>
      <td>0.122028</td>
      <td>0.307744</td>
      <td>0.440098</td>
    </tr>
    <tr>
      <th>six</th>
      <td>0.546968</td>
      <td>0.800105</td>
      <td>0.421170</td>
      <td>0.821478</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.plot.bar()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1146f67f0>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/m9Fdihj1IL.png?imageslim)


```python
import seaborn
```


```python
df.plot.bar() # 可以看到导入seaborn后画出来的图漂亮多了
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1173d7860>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/4adGgbhcfH.png?imageslim)

注意这里DataFrame列名的"Genus"，被作为图例。

我们可以设定stacked=True，令条形图堆叠起来，能让每一行的所有值都被堆起来：


```python
df.plot.barh(stacked=True, alpha=0.5)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11755bb38>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/F5KgEFmBiL.png?imageslim)

> 一个小窍门，在画series的值出现的频率的条形图时，可以使用value_counts: `s.value_counts().plot.bar()`

之前我们用到过tipping(小费)数据集，假设我们想做一个堆叠的条形图，来表示在每一天，每一个大小不同的组（party）中，数据点的百分比。用read_csv导入数据，并按天数（day）和组大小（party size）做一个交叉报表（cross-tabulation）：


```python
tips = pd.read_csv('../examples/tips.csv')
```


```python
tips.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_bill</th>
      <th>tip</th>
      <th>smoker</th>
      <th>day</th>
      <th>time</th>
      <th>size</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>16.99</td>
      <td>1.01</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10.34</td>
      <td>1.66</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>21.01</td>
      <td>3.50</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>23.68</td>
      <td>3.31</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24.59</td>
      <td>3.61</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




```python
party_counts = pd.crosstab(tips['day'], tips['size'])
```


```python
party_counts
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>size</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
    </tr>
    <tr>
      <th>day</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Fri</th>
      <td>1</td>
      <td>16</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Sat</th>
      <td>2</td>
      <td>53</td>
      <td>18</td>
      <td>13</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Sun</th>
      <td>0</td>
      <td>39</td>
      <td>15</td>
      <td>18</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Thur</th>
      <td>1</td>
      <td>48</td>
      <td>4</td>
      <td>5</td>
      <td>1</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 大于1人小于6人
party_counts = party_counts.loc[:, 2:5]
party_counts
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>size</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
    </tr>
    <tr>
      <th>day</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Fri</th>
      <td>16</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Sat</th>
      <td>53</td>
      <td>18</td>
      <td>13</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Sun</th>
      <td>39</td>
      <td>15</td>
      <td>18</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Thur</th>
      <td>48</td>
      <td>4</td>
      <td>5</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



标准化一下，让每一行的和变为1，然后绘图：


```python
# Normalize to sum to 1
party_pcts = party_counts.div(party_counts.sum(1), axis=0)
party_pcts
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>size</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
    </tr>
    <tr>
      <th>day</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Fri</th>
      <td>0.888889</td>
      <td>0.055556</td>
      <td>0.055556</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>Sat</th>
      <td>0.623529</td>
      <td>0.211765</td>
      <td>0.152941</td>
      <td>0.011765</td>
    </tr>
    <tr>
      <th>Sun</th>
      <td>0.520000</td>
      <td>0.200000</td>
      <td>0.240000</td>
      <td>0.040000</td>
    </tr>
    <tr>
      <th>Thur</th>
      <td>0.827586</td>
      <td>0.068966</td>
      <td>0.086207</td>
      <td>0.017241</td>
    </tr>
  </tbody>
</table>
</div>




```python
party_pcts.plot.bar()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11807af28>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/10L5DFHH72.png?imageslim)

这样我们可以看出来，在周末的时候组大小（party size）是增大的。

对于需要汇总的数据，使用seaborn能方便很多。让我们试一下用seaborn，按day来查看tipping percentage(小费百分比)：


```python
import seaborn as sns
```


```python
tips['tip_pct'] = tips['tip'] / (tips['total_bill'] - tips['tip'])
```


```python
tips.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_bill</th>
      <th>tip</th>
      <th>smoker</th>
      <th>day</th>
      <th>time</th>
      <th>size</th>
      <th>tip_pct</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>16.99</td>
      <td>1.01</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
      <td>0.063204</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10.34</td>
      <td>1.66</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
      <td>0.191244</td>
    </tr>
    <tr>
      <th>2</th>
      <td>21.01</td>
      <td>3.50</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
      <td>0.199886</td>
    </tr>
    <tr>
      <th>3</th>
      <td>23.68</td>
      <td>3.31</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
      <td>0.162494</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24.59</td>
      <td>3.61</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>4</td>
      <td>0.172069</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.barplot(x='tip_pct', y='day', data=tips, orient='h')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1182b3cf8>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/g2Id52kfji.png?imageslim)

seaborn的绘图函数中有一个data参数，这里可以导入pandas的DataFrame。其他参数指的是列名。因为每一天（比如一个固定的周六）可能会有多个不同的值，所以条形图表示的是tip_pct的平均值。条形图上的黑线表示95%的置信区间（confidence interval）（这个可以通过可选参数进行更改）。

seaborn.barplot有一个hue选项，这个能让我们通过一个额外的类别值把数据分开：


```python
sns.set_style("ticks") # 我们可以换一个样式
# sns.set_style("white")
```


```python
sns.barplot(x='tip_pct', y='day', hue='time', data=tips, orient='h')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x118e143c8>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/hHba9376cD.png?imageslim)

注意seaborn会自动更改绘图的外观：默认的调色板，绘图背景，网格颜色。我们可以自己设定不同的绘图外观，通过seaborn.set:


```python
sns.set(style='whitegrid')
```

# 3 Histograms and Density Plots（柱状图和密度图）

柱状图是一种条形图，不过值的频率是分割式的。数据点被分割为，离散的甚至是隔开的bin（BIN是储存箱、存放箱、垃圾箱的意思，中文实在是不好翻译，我把它理解为一个小柱子，之后就直接用bin了），而且每个bin中的数据点的数量会被画出来。用上面的tipping数据集，我们可以用plot.hist做一个柱状图来表示小费（tip）占总费用（total bill）的比例：


```python
tips['tip_pct'].plot.hist(bins=50)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1191d05f8>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/2gFDamceIE.png?imageslim)

另一种相关的绘图类型是density plot（密度图），这个是用来计算观测数据中，连续概率分布的推测值。通常的步骤是用一组混合的“kernels”（核）来近似这个分布————核指的是，像正态分布一样的简单分布。因此，概率图也经常被叫做kernel density estimate(KDE, 核密度估计)图。用plot.kde，通过conventional mixture-of-normals estimate（常规混合估计
）制作一个密度图：


```python
tips['tip_pct'].plot.density()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11945a748>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/bG62cCH6h9.png?imageslim)

seaborn能更方便地绘制柱状图和概率图，通过distplot方法，这个方法可以同时绘制一个柱状图和a continuous density estimate（一个连续密度估计）。举个例子，考虑一个bimodal distribution（双峰分布，二项分布），它由连个不同的标准正态分布组成：


```python
comp1 = np.random.normal(0, 1, size=200)
comp2 = np.random.normal(10, 2, size=200)
values = pd.Series(np.concatenate([comp1, comp2]))
```


```python
values[:10]
```




    0    0.006016
    1   -0.481471
    2   -0.861770
    3   -0.544488
    4    0.712472
    5    0.371618
    6   -1.289412
    7    1.201699
    8    1.433649
    9   -0.076823
    dtype: float64




```python
sns.distplot(values, bins=100, color='k')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x114fc67f0>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/3g0kJ2lK9b.png?imageslim)

（我在运行上面的代码时，报错提示为ypeError: slice indices must be integers or None or have an __index__ method。通过更新statsmodels这个包解决了问题，原先的版本是0.6，更新到0.8后就没问题了。可以直接输入`conda install -c statsmodels statsmodels=0.8.0
`）

# 4 Scatter or Point Plots（散点图或点图）
散点图对于检查二维数据之间的关系是非常有用的。例如，我们导入macrodata数据集，选一些参数，然后计算log differences（对数差分）：


```python
macro = pd.read_csv('../examples/macrodata.csv')
macro.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>quarter</th>
      <th>realgdp</th>
      <th>realcons</th>
      <th>realinv</th>
      <th>realgovt</th>
      <th>realdpi</th>
      <th>cpi</th>
      <th>m1</th>
      <th>tbilrate</th>
      <th>unemp</th>
      <th>pop</th>
      <th>infl</th>
      <th>realint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1959.0</td>
      <td>1.0</td>
      <td>2710.349</td>
      <td>1707.4</td>
      <td>286.898</td>
      <td>470.045</td>
      <td>1886.9</td>
      <td>28.98</td>
      <td>139.7</td>
      <td>2.82</td>
      <td>5.8</td>
      <td>177.146</td>
      <td>0.00</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1959.0</td>
      <td>2.0</td>
      <td>2778.801</td>
      <td>1733.7</td>
      <td>310.859</td>
      <td>481.301</td>
      <td>1919.7</td>
      <td>29.15</td>
      <td>141.7</td>
      <td>3.08</td>
      <td>5.1</td>
      <td>177.830</td>
      <td>2.34</td>
      <td>0.74</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1959.0</td>
      <td>3.0</td>
      <td>2775.488</td>
      <td>1751.8</td>
      <td>289.226</td>
      <td>491.260</td>
      <td>1916.4</td>
      <td>29.35</td>
      <td>140.5</td>
      <td>3.82</td>
      <td>5.3</td>
      <td>178.657</td>
      <td>2.74</td>
      <td>1.09</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1959.0</td>
      <td>4.0</td>
      <td>2785.204</td>
      <td>1753.7</td>
      <td>299.356</td>
      <td>484.052</td>
      <td>1931.3</td>
      <td>29.37</td>
      <td>140.0</td>
      <td>4.33</td>
      <td>5.6</td>
      <td>179.386</td>
      <td>0.27</td>
      <td>4.06</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1960.0</td>
      <td>1.0</td>
      <td>2847.699</td>
      <td>1770.5</td>
      <td>331.722</td>
      <td>462.199</td>
      <td>1955.5</td>
      <td>29.54</td>
      <td>139.6</td>
      <td>3.50</td>
      <td>5.2</td>
      <td>180.007</td>
      <td>2.31</td>
      <td>1.19</td>
    </tr>
  </tbody>
</table>
</div>




```python
data = macro[['cpi', 'm1', 'tbilrate', 'unemp']]
data.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cpi</th>
      <th>m1</th>
      <th>tbilrate</th>
      <th>unemp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>28.98</td>
      <td>139.7</td>
      <td>2.82</td>
      <td>5.8</td>
    </tr>
    <tr>
      <th>1</th>
      <td>29.15</td>
      <td>141.7</td>
      <td>3.08</td>
      <td>5.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>29.35</td>
      <td>140.5</td>
      <td>3.82</td>
      <td>5.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>29.37</td>
      <td>140.0</td>
      <td>4.33</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29.54</td>
      <td>139.6</td>
      <td>3.50</td>
      <td>5.2</td>
    </tr>
  </tbody>
</table>
</div>




```python
trans_data = np.log(data).diff().dropna()
trans_data[-5:]
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cpi</th>
      <th>m1</th>
      <th>tbilrate</th>
      <th>unemp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>198</th>
      <td>-0.007904</td>
      <td>0.045361</td>
      <td>-0.396881</td>
      <td>0.105361</td>
    </tr>
    <tr>
      <th>199</th>
      <td>-0.021979</td>
      <td>0.066753</td>
      <td>-2.277267</td>
      <td>0.139762</td>
    </tr>
    <tr>
      <th>200</th>
      <td>0.002340</td>
      <td>0.010286</td>
      <td>0.606136</td>
      <td>0.160343</td>
    </tr>
    <tr>
      <th>201</th>
      <td>0.008419</td>
      <td>0.037461</td>
      <td>-0.200671</td>
      <td>0.127339</td>
    </tr>
    <tr>
      <th>202</th>
      <td>0.008894</td>
      <td>0.012202</td>
      <td>-0.405465</td>
      <td>0.042560</td>
    </tr>
  </tbody>
</table>
</div>



关于对数差分译者找到的一些资料：

1
1. 取对数(log)：缩小差距，减少异方差性
2. 差分：非平缓数据变平稳的技能

2 是原始序列的对数增长率，而且这么处理后序列会更平稳

3
对于不平稳的时间序列，我们可以通过差分的方法使它平稳，但是差分之后的问题是有的经济意义就无法直观解释了，所以我们又有了构建协整关系这一方法。


然后我们可以利用seaborn的regplot方法，它可以产生一个散点图并拟合一条回归线：


```python
sns.set_style("whitegrid")
```


```python
sns.regplot('m1', 'unemp', data=trans_data)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x11556f898>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/GH0g7aBjIG.png?imageslim)

在数据探索阶段，散点图能把一组变量之间所有的散点图都画出来，这种图被称之为pairs plot（多变量图）或scatter plot matrix（散点图矩阵）。画这样的图很麻烦，所以seaborn有一个非常方便的pairplot函数，这个函数可以把每一个参数的柱状图或密度估计画在对角线上：


```python
sns.pairplot(trans_data, diag_kind='kde', plot_kws={'alpha': 0.2})
```




    <seaborn.axisgrid.PairGrid at 0x116370390>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/7f59D40Dc1.png?imageslim)

这里要注意一下plot_kws关键字。这个让我们能导入设置选项，用来控制非对角线上的绘图。查看seaborn.pairplot的字符串文档查看更多的设定选项。


```python
sns.pairplot?
```

# 5 Facet Grids and Categorical Data（多面网格和类别数据）

如果遇到一些数据集，需要额外分组的维度，该怎么办？一个方法是使用类别变量来把数据可视化，利用facet grid（多面网格）。seaborn有一个有用的内建函数factorplot，能简化制作各种多面图的过程：


```python
sns.factorplot(x='day', y='tip_pct', hue='time', col='smoker',
               kind='bar', data=tips[tips.tip_pct < 1])
```




    <seaborn.axisgrid.FacetGrid at 0x116b28748>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/iC3JbiF5IG.png?imageslim)

在一个facet（面）内，不是通过time和不同的柱状颜色来分组，我们也能通过添加给每一个time值添加一行的方式来扩展多面网格：


```python
tips[tips.tip_pct < 1].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_bill</th>
      <th>tip</th>
      <th>smoker</th>
      <th>day</th>
      <th>time</th>
      <th>size</th>
      <th>tip_pct</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>16.99</td>
      <td>1.01</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
      <td>0.063204</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10.34</td>
      <td>1.66</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
      <td>0.191244</td>
    </tr>
    <tr>
      <th>2</th>
      <td>21.01</td>
      <td>3.50</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
      <td>0.199886</td>
    </tr>
    <tr>
      <th>3</th>
      <td>23.68</td>
      <td>3.31</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
      <td>0.162494</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24.59</td>
      <td>3.61</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>4</td>
      <td>0.172069</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.factorplot(x='day', y='tip_pct', row='time',
               col='smoker', kind='bar', data=tips[tips.tip_pct])
```




    <seaborn.axisgrid.FacetGrid at 0x118d4fe48>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/DcDecGHFa1.png?imageslim)

factorplot支持其他一些绘图类型，是否有用取决于我们想要如何展示。例如，box plots（箱线图，可以展示中位数，分位数和利群店）可能是一种有效的视觉类型：


```python
sns.factorplot(x='tip_pct', y='day', kind='box',
               data=tips[tips.tip_pct < 0.5])
```




    <seaborn.axisgrid.FacetGrid at 0x1151a8eb8>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/kl2bk5fL01.png?imageslim)

通过seaborn.FacetGrid，我们可以创建自己的多面网格图。更多信息请查看seaborn的文档。
