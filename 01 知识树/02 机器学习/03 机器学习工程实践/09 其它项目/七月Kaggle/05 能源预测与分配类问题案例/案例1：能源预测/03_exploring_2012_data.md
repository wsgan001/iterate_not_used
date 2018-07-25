

计算是非常耗时间和资源的，所以你不要以为说，我所有东西都有了，我先跑一个 baseline model 这个事情不是那么合适的。

一个比较合适的事情是：你先保证你的流程能跑通，保证你的当前的探索的方法是有意义的，是值得做的。

## 2012数据探索

<span style="color:red;">工作中，模型做好了之后，一定要先在小数据集上先跑一下。看看情况。</span>

本 ipython notebook 使用2种方法(Gradient Boosting regression 和 OLS回归)在2012数据上小试验一把。<span style="color:red;">OLS 回归是什么？</span>


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```

### 1.导入2012子数据集


```python
loads = pd.read_csv('load2012.csv')
weather = pd.read_csv('weather2012.csv')
```

### 格式化时间列


```python
weather['date'] = weather.dateutc.apply(lambda x: pd.to_datetime(x).date())
weather['timeest'] = weather.timeest.apply(lambda x: pd.to_datetime(x).time())
foo = weather[['date', 'timeest']].astype(str)
weather['timestamp'] = pd.to_datetime(foo['date'] + ' ' + foo['timeest'])
loads['timestamp'] = loads.timestamp.apply(lambda x: pd.to_datetime(x))
```

上面这段的操作不是特别清楚。要自己跑一下。

之所以要进行上面的操作是因为要把 weather 和 loads 合并起来，在工业界，实际上是大家不会给你一个完整的数据的，不会帮你做很多工作的，这些合并和处理都要自己做。<span style="color:red;">这种合并和处理数据的方法和具体的 python 使用都要整理，并熟练掌握。</span>

在实际的工作中，数据库也会分不同的表来存贮，文件也会分不同的表来存储，数据库可能会有第几范式来节省空间。<span style="color:red;">什么是数据库的第几范式？</span>

## 2. 补充缺失的天气信息

<span style="color:red;">永远我对我拿到手的数据保持警惕，数据里面可能会有缺失的。</span>

天气信息的频度是小时级别的，我们载入的 2012 数据是每 5 分钟的间隔。下面这个函数实际上就是使用 KNN 去补全 5 分钟级别数据里的天气信息。当然，详细的天气数据是可以从气象局购买的，不过需要钱。<span style="color:red;">还有这样的。</span>

<span style="color:red;">没想到还可以使用 KNN 进行填充。</span>


```python
from sklearn.neighbors import NearestNeighbors

def find_nearest(group, match, groupname):
    nbrs = NearestNeighbors(1).fit(match['timestamp'].values[:, None])
    dist, ind = nbrs.kneighbors(group['timestamp'].values[:, None])

    group['nearesttime'] = match['timestamp'].values[ind.ravel()]
    return group

loads = find_nearest(loads,weather,'timestamp')
```

<span style="color:red;">厉害了，这个 NearestNeighbors 竟然用 1 作为参数，嗯，好想法。</span>

<span style="color:red;">这个 None 是什么？</span>

嗯，上面这段程序，把 loads 的时间戳最近的完整的小时的时间放到 nearesttime 里面。这样这个 nearesttime 就可以与天气的时间对应了。这时候就可以把 weather 和 loads merge 在一起了：

```python
full = loads.merge(weather, left_on='nearesttime', right_on='timestamp')

#去除冗余列，重命名部分列
full = full[['timestamp_x', 'load', 'nearesttime', 'temperaturef', \
            'dewpointf', 'humidity', 'sealevelpressurein', 'winddirection', 'windspeedkmh', \
            'precipitationmm']].rename(columns={'timestamp_x': 'timestamp', 'nearesttime':'weathertime'})
```

部分的列明进行了重命名。

上面这些都是很耗费时间，但是必须要做的数据处理的事情。

### *导出完整数据到csv文件中*


```python
full.to_csv('full2012.csv', index=False)
```





## 3. 构造特征


现在，我们已经把天气填充到 loads 里面了。由于我们没有气象方面的专业背景，所以不知道要对天气特征进行什么处理才对这个场景更有效，另外一方面，天气信息里面已经包含了一些气温等的数字信息，这些再给平方或开放什么的，意义不是很大。

因此，我们接下来准备挖掘一些时间的特征。

这是一个时间序列上的回归问题，需要在时间上做一些特征，可参照论文[Barta et al. 2015](http://arxiv.org/pdf/1506.06972.pdf)提到的方式，去构造细粒度的时间特征，上面那篇论文的应用场景也是用概率模型预测电价。构造的特征如下：

- `dow`: day of the week (integer 0-6)
- `doy`: day of the year (integer 0-365)
- `day`: day of the month (integer 1-31)
- `woy`: week of the year (integer 1-52)
- `month`: month of the year (integer 1-12)
- `hour`: hour of the day (integer 0-23)
- `minute`: minute of the day (integer 0-1339) <span style="color:red;">好吧，这个也有。</span>

- `t_m24`: load value from 24 hours earlier
- `t_m48`: load value from 48 hours earlier
- `tdif`: difference between load and t_m24 ：每隔 24 小时的 diff。

论文中告诉了我们这些特征是有用的。


我们先构造一个函数获取 n 天前的相同时刻的电力需求：

```python
#取出 n 天前相同时刻的电力需求
pday = pd.Timedelta('1 day')

def get_prev_days(x, n_days):
    '''Take a datetime (x) in the 'full' dataframe, and outputs the load value n_days before that datetime'''
    try:
        lo = full[full.timestamp == x - n_days*pday].load.values[0]
    except:
        lo = full[full.timestamp == x].load.values[0]
    return lo
```

真的，看厉害的人写的代码真的像手术刀一样精准。上面这个 `pd.Timedelta('1 day')` 简直，做梦都没想到还可以这样写。

开始构造特征：

```python
full['dow'] = full.timestamp.apply(lambda x: x.dayofweek)
full['doy'] = full.timestamp.apply(lambda x: x.dayofyear)
full['day'] = full.timestamp.apply(lambda x: x.day)
full['month'] = full.timestamp.apply(lambda x: x.month)
full['hour'] = full.timestamp.apply(lambda x: x.hour)
full['minute'] = full.timestamp.apply(lambda x: x.hour*60 + x.minute)

full['t_m24'] = full.timestamp.apply(get_prev_days, args=(1,))
full['t_m48'] = full.timestamp.apply(get_prev_days, args=(2,))
full['tdif'] = full['load'] - full['t_m24']
```

<span style="color:red;">震惊了，没想到 pandas 里面是这样构造特征的，简直太方便了。怪不得要求 pandas 里面关于时间的操作最起码要知道有这些操作，因为知道之后可能直接一句话就行，如果不知道可能需要自己来手工处理。</span>

<span style="color:red;">`full.timestamp.apply(get_prev_days, args=(1,))` 这种写法也很厉害，以前不知道还可以这样写。</span>

<span style="color:red;">如果想把过去24小时使用的时间总和作为特征怎么写？</span>

把这个时间特征也构造好的数据存放起来：


```python
full.to_csv('full2012_features.csv', index=False)
```

到目前为止，我们做好了数据的采集和数据的清洗和特征的构建。


## 4. Gradient Boosting Regression


这里使用的是 GradientBoostingRegressor ，但是他比较慢，所以很多人都使用 XGBoost。

```python
from sklearn.ensemble import GradientBoostingRegressor
from sklearn.cross_validation import train_test_split
```


```python
full.columns
```


```
Index([u'timestamp', u'load', u'weathertime', u'temperaturef', u'dewpointf',
       u'humidity', u'sealevelpressurein', u'winddirection', u'windspeedmph',
       u'precipitationin', u'dow', u'doy', u'day', u'month', u'hour',
       u'minute', u't_m24', u't_m48', u'tdif'],
      dtype='object')
```




```python
X = full[[\
          'temperaturef',\
          'dewpointf', \
          'humidity', \
          'sealevelpressurein', \
          'windspeedkmh', \
          'precipitationmm',\
          'dow',\
          'doy', \
          'month',\
          'hour',\
          'minute',\
          't_m24', \
          't_m48', \
          'tdif'\
         ]]
y = full['load']
```


```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)
```

直接使用默认的值跑一下：

```python
gbr = GradientBoostingRegressor(loss='ls', verbose=1, warm_start=True)
```


```python
gbr_fitted = gbr.fit(X_train, y_train)
```

```
      Iter       Train Loss   Remaining Time
         1       54422.8242            9.84s
         2       46096.6971           11.07s
         3       39293.9887            9.98s
         4       33773.5271            8.73s
         5       29246.6397            8.50s
         6       25568.6157            8.23s
         7       22550.6621            7.98s
         8       20025.1744            7.74s
         9       17956.5337            7.52s
        10       16236.3410            7.46s
        20        9033.7561            5.55s
        30        7353.5608            4.25s
        40        6528.5173            3.39s
        50        5909.5739            2.73s
        60        5598.7196            2.08s
        70        5323.0064            1.52s
        80        5056.9857            1.08s
        90        4865.8749            0.53s
       100        4670.0520            0.00s
```



```python
gbr.score(X_test, y_test)
```




```
0.92513322840797652
```


```python
gbr.score(X_train, y_train)
```


```
0.92779521041729107
```

<span style="color:red;">这种是分越高越好的吗？看来很不错呀，没想到。</span>

## 5. Ordinary Least Squares Regression

使用传统的 LSR 来做：

```python
import statsmodels.api as sm

model = sm.OLS(y,X)
results = model.fit()
results.summary()
```

| Dep. Variable:    | load             | R-squared:          | 0.996       |
| ----------------- | ---------------- | ------------------- | ----------- |
| Model:            | OLS              | Adj. R-squared:     | 0.996       |
| Method:           | Least Squares    | F-statistic:        | 1.856e+06   |
| Date:             | Wed, 16 Mar 2016 | Prob (F-statistic): | 0.00        |
| Time:             | 16:19:54         | Log-Likelihood:     | -6.3477e+05 |
| No. Observations: | 107000           | AIC:                | 1.270e+06   |
| Df Residuals:     | 106987           | BIC:                | 1.270e+06   |
| Df Model:         | 13               |                     |             |
| Covariance Type:  | nonrobust        |                     |             |

|                    | coef     | std err | t        | P>\|t\| | [95.0% Conf. Int.] |
| ------------------ | -------- | ------- | -------- | ------- | ------------------ |
| temperaturef       | -1.3140  | 0.172   | -7.626   | 0.000   | -1.652 -0.976      |
| dewpointf          | 1.9076   | 0.183   | 10.447   | 0.000   | 1.550 2.265        |
| humidity           | -1.0322  | 0.080   | -12.865  | 0.000   | -1.189 -0.875      |
| sealevelpressurein | 8.2132   | 0.246   | 33.328   | 0.000   | 7.730 8.696        |
| windspeedmph       | 0.4577   | 0.054   | 8.522    | 0.000   | 0.352 0.563        |
| precipitationin    | -51.7532 | 11.751  | -4.404   | 0.000   | -74.785 -28.721    |
| dow                | -16.3734 | 0.145   | -113.264 | 0.000   | -16.657 -16.090    |
| doy                | 0.0893   | 0.032   | 2.824    | 0.005   | 0.027 0.151        |
| month              | -2.4457  | 0.970   | -2.522   | 0.012   | -4.346 -0.545      |
| hour               | 1.7808   | 0.970   | 1.835    | 0.066   | -0.121 3.683       |
| minute             | -0.0007  | 0.016   | -0.042   | 0.967   | -0.032 0.031       |
| t_m24              | 0.8563   | 0.003   | 290.160  | 0.000   | 0.851 0.862        |
| t_m48              | 0.0259   | 0.003   | 8.694    | 0.000   | 0.020 0.032        |

| Omnibus:       | 11294.270 | Durbin-Watson:    | 0.074     |
| -------------- | --------- | ----------------- | --------- |
| Prob(Omnibus): | 0.000     | Jarque-Bera (JB): | 40839.219 |
| Skew:          | 0.509     | Prob(JB):         | 0.00      |
| Kurtosis:      | 5.850     | Cond. No.         | 8.81e+04  |





```python
from sklearn.metrics import mean_squared_error
from sklearn.linear_model import Ridge

avg_MSE = []
alphas = np.linspace(-2, 8, 20, endpoint=False)
alphas
for alpha in alphas:
    MSE = []
    for i in range(20):
#     X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=20)
#     model = sm.OLS(X_train, y_train)
        model = Ridge(alpha=alpha)
        model.fit(X_test, y_test)
        test_error = mean_squared_error(y_test, model.predict(X_test))
        MSE.append(test_error)
    avg_MSE.append(np.mean(MSE))

plt.figure(figsize=(6,2))
plt.xlabel('alpha', fontsize=14)
plt.ylabel('Cross Validation MSE', fontsize=11)
plt.title('alpha vs. Cross Validation MSE', fontsize=11)
plt.plot(alphas, avg_MSE)
```




```
[<matplotlib.lines.Line2D at 0x12646d190>]
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180725/53KgdkaGd4.png?imageslim)




这个只是在小数据集上跑了全部的流程，并没有确定现在我们要用什么参数。因为在小数据集上表现好的参数并不一定在全局的时候表现好。

参数其实我们不担心，我们只需要把 grid-search 和 cross-validation 用上之后，给计算机跑就行。我们真正要关注的是前面的数据如何去产出我们最合适的特征，产出我们对目标场景有帮助的特征。

其实，关于模型的选择，和关于超参的选择，是我们比较不担心的事情，因为大部分都是写好之后给计算机跑就行。可供选择的模型和超参不是那么的多。

<span style="color:red;">其实我对交叉验证这个词的概念还有一些不确定。到底什么样叫做交叉验证？有哪些参数？</span>
