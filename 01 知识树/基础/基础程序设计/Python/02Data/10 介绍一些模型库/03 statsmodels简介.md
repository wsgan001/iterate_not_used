
# 13.3 Introduction to statsmodels（statsmodels简介）

[statsmodels](http://www.statsmodels.org/stable/index.html)是一个有很多统计模型的python库，能完成很多统计测试，数据探索以及可视化。它也包含一些经典的统计方法，比如贝叶斯方法和一个机器学习的模型。

statsmodels中的模型包括：

- 线性模型（linear models），广义线性模型（generalized linear models），鲁棒线性模型（robust linear models）
- 线性混合效应模型（Linear mixed effects models）
- 方差分析(ANOVA)方法（Analysis of variance (ANOVA) methods）
- 时间序列处理（Time series processes）和状态空间模型（state space models）
- 广义矩估计方法（Generalized method of moments）

接下来我们用一些statsmodels中的工具，并了解如何使用Patsy公式和pandas DataFrame进行建模。

# 1 Estimating Linear Models（估计线性模型）

statsmodels中的线性模型大致分为两种：基于数组的（array-based），和基于公式的（formula-based）。调用的模块为：

  


```python
import statsmodels.api as sm 
import statsmodels.formula.api as smf
```

为了演示如何使用，我们对一些随机数据生成一个线性模型：


```python
import numpy as np
import pandas as pd
```


```python
def dnorm(mean, variance, size=1):
    if isinstance(size, int):
        size = size
    return mean + np.sqrt(variance) * np.random.randn(size)
    
# For reproducibility 
np.random.seed(12345)

N = 100 
X = np.c_[dnorm(0, 0.4, size=N), 
          dnorm(0, 0.6, size=N), 
          dnorm(0, 0.2, size=N)] 

eps = dnorm(0, 0.1, size=N) 
beta = [0.1, 0.3, 0.5]

y = np.dot(X, beta) + eps
```


```python
print(X.shape)
print(eps.shape)
```

    (100, 3)
    (100,)
    

真正的模型用的参数是beta，dnorm的功能是产生指定平均值和方差的随机离散数据，得到：


```python
X[:5]
```




    array([[-0.12946849, -1.21275292,  0.50422488],
           [ 0.30291036, -0.43574176, -0.25417986],
           [-0.32852189, -0.02530153,  0.13835097],
           [-0.35147471, -0.71960511, -0.25821463],
           [ 1.2432688 , -0.37379916, -0.52262905]])




```python
y[:5]
```




    array([ 0.42786349, -0.67348041, -0.09087764, -0.48949442, -0.12894109])



一个线性模型通常会有一个截距，这里我们用sm.add_constant函数添加一个截距列给X：


```python
X_model = sm.add_constant(X)
X_model[:5]
```




    array([[ 1.        , -0.12946849, -1.21275292,  0.50422488],
           [ 1.        ,  0.30291036, -0.43574176, -0.25417986],
           [ 1.        , -0.32852189, -0.02530153,  0.13835097],
           [ 1.        , -0.35147471, -0.71960511, -0.25821463],
           [ 1.        ,  1.2432688 , -0.37379916, -0.52262905]])



sm.OLS可以拟合（fit）普通最小二乘线性回归：


```python
model = sm.OLS(y, X)
```

fit方法返回的是一个回顾结果对象，包含预测模型的参数和其他一些诊断数据：


```python
results = model.fit()
results.params
```




    array([ 0.17826108,  0.22303962,  0.50095093])



在results上调用summary方法，可能得到一些详细的诊断数据：


```python
results.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>y</td>        <th>  R-squared:         </th> <td>   0.430</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.413</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   24.42</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 11 Dec 2017</td> <th>  Prob (F-statistic):</th> <td>7.44e-12</td>
</tr>
<tr>
  <th>Time:</th>                 <td>00:01:30</td>     <th>  Log-Likelihood:    </th> <td> -34.305</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   100</td>      <th>  AIC:               </th> <td>   74.61</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    97</td>      <th>  BIC:               </th> <td>   82.42</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
   <td></td>     <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>x1</th> <td>    0.1783</td> <td>    0.053</td> <td>    3.364</td> <td> 0.001</td> <td>    0.073</td> <td>    0.283</td>
</tr>
<tr>
  <th>x2</th> <td>    0.2230</td> <td>    0.046</td> <td>    4.818</td> <td> 0.000</td> <td>    0.131</td> <td>    0.315</td>
</tr>
<tr>
  <th>x3</th> <td>    0.5010</td> <td>    0.080</td> <td>    6.237</td> <td> 0.000</td> <td>    0.342</td> <td>    0.660</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 4.662</td> <th>  Durbin-Watson:     </th> <td>   2.201</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.097</td> <th>  Jarque-Bera (JB):  </th> <td>   4.098</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.481</td> <th>  Prob(JB):          </th> <td>   0.129</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.243</td> <th>  Cond. No.          </th> <td>    1.74</td>
</tr>
</table>



参数的名字通常为x1, x2，以此类推。假设所有的模型参数都在一个DataFrame里：


```python
data = pd.DataFrame(X, columns=['col0', 'col1', 'col2'])
data['y'] = y
```


```python
data.head()
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
      <th>col0</th>
      <th>col1</th>
      <th>col2</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.129468</td>
      <td>-1.212753</td>
      <td>0.504225</td>
      <td>0.427863</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.302910</td>
      <td>-0.435742</td>
      <td>-0.254180</td>
      <td>-0.673480</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.328522</td>
      <td>-0.025302</td>
      <td>0.138351</td>
      <td>-0.090878</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.351475</td>
      <td>-0.719605</td>
      <td>-0.258215</td>
      <td>-0.489494</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.243269</td>
      <td>-0.373799</td>
      <td>-0.522629</td>
      <td>-0.128941</td>
    </tr>
  </tbody>
</table>
</div>



现在我们可以使用statsmodels formula API（公式API）和Patsy的公式字符串：


```python
results = smf.ols('y ~ col0 + col1 + col2', data=data).fit()
results.params
```




    Intercept    0.033559
    col0         0.176149
    col1         0.224826
    col2         0.514808
    dtype: float64




```python
results.tvalues
```




    Intercept    0.952188
    col0         3.319754
    col1         4.850730
    col2         6.303971
    dtype: float64



可以看到statsmodel返回的结果是Series，而Series的索引部分是DataFrame的列名。当我们使用公式和pandas对象的时候，不需要使用add_constant。

如果得到新的数据，我们可以用预测模型的参数来进行预测：


```python
results.predict(data[:5])
```




    0   -0.002327
    1   -0.141904
    2    0.041226
    3   -0.323070
    4   -0.100535
    dtype: float64



其他一些分析、诊断、可视化工具可以自己多尝试。

# 2 Estimating Time Series Processes（预测时序过程）

statsmodels中的另一个类是用于时间序列分析的，其中有自动回归处理（autoregressive processes）， 卡尔曼滤波（Kalman filtering），状态空间模型（state space models），多元回归模型（multivariate autoregressive models）。

让我们用自动回归结果和噪音模拟一个时间序列数据：




```python
init_x = 4

import random
values = [init_x, init_x]
N = 1000

b0 = 0.8
b1 = -0.4
noise = dnorm(0, 0.1, N)
for i in range(N):
    new_x = values[-1] * b0 + values[-2] * b1 + noise[i]
    values.append(new_x)
```


```python
values[:6]
```




    [4,
     4,
     1.8977509636904242,
     0.086865262206104243,
     -0.57694691325353353,
     -0.49950238023089472]



这种数据有AR(2)结构（two lags，延迟两期），延迟参数是0.8和-0.4。当我们拟合一个AR模型，我们可能不知道延迟的期间是多少，所以可以在拟合时设一个比较大的延迟数字：


```python
MAXLAGS = 5
model = sm.tsa.AR(values)
results = model.fit(MAXLAGS)
```

结果里的预测参数，第一个是解决，之后是两个延迟（lags）：


```python
results.params
```




    array([-0.00616093,  0.78446347, -0.40847891, -0.01364148,  0.01496872,
            0.01429462])



关于模型的更多细节，可以查看文档。
