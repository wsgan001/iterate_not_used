# Kaggle竞赛：房价预测


## 题目

依据⼀个房⼦的全⽅位信息，包括⾯积，地段，环境，等等。预测出房⼦的价格

- X: [房源信息]
- y: 房价


## 文件描述

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180720/i29DDkG0D6.png?imageslim)


## 特征描述

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180720/A301hG4k9L.png?imageslim)

SalePrice 就是 Y







# 房价预测案例

## Step 1: 检视源数据集


```python
import numpy as np
import pandas as pd
```

#### 读入数据

* 一般来说源数据的 `index` 那一栏没什么用，我们可以用来作为我们 pandas dataframe 的 `index` 。这样之后要是检索起来也省事儿。

* Kaggle上默认把数据放在 *input* 文件夹下。所以我们没事儿写个教程什么的，也可以依据这个 **convention** 来，显得自己很有逼格。。


```python
train_df = pd.read_csv('../input/train.csv', index_col=0)
test_df = pd.read_csv('../input/test.csv', index_col=0)
```

#### 检视源数据


```python
train_df.head()
```


| MSSubClass | MSZoning | LotFrontage | LotArea | Street | Alley | LotShape | LandContour | Utilities | LotConfig | ...    | PoolArea | PoolQC | Fence | MiscFeature | MiscVal | MoSold | YrSold | SaleType | SaleCondition | SalePrice |        |
| ---------- | -------- | ----------- | ------- | ------ | ----- | -------- | ----------- | --------- | --------- | ------ | -------- | ------ | ----- | ----------- | ------- | ------ | ------ | -------- | ------------- | --------- | ------ |
| Id         |          |             |         |        |       |          |             |           |           |        |          |        |       |             |         |        |        |          |               |           |        |
| 1          | 60       | RL          | 65.0    | 8450   | Pave  | NaN      | Reg         | Lvl       | AllPub    | Inside | ...      | 0      | NaN   | NaN         | NaN     | 0      | 2      | 2008     | WD            | Normal    | 208500 |
| 2          | 20       | RL          | 80.0    | 9600   | Pave  | NaN      | Reg         | Lvl       | AllPub    | FR2    | ...      | 0      | NaN   | NaN         | NaN     | 0      | 5      | 2007     | WD            | Normal    | 181500 |
| 3          | 60       | RL          | 68.0    | 11250  | Pave  | NaN      | IR1         | Lvl       | AllPub    | Inside | ...      | 0      | NaN   | NaN         | NaN     | 0      | 9      | 2008     | WD            | Normal    | 223500 |
| 4          | 70       | RL          | 60.0    | 9550   | Pave  | NaN      | IR1         | Lvl       | AllPub    | Corner | ...      | 0      | NaN   | NaN         | NaN     | 0      | 2      | 2006     | WD            | Abnorml   | 140000 |
| 5          | 60       | RL          | 84.0    | 14260  | Pave  | NaN      | IR1         | Lvl       | AllPub    | FR2    | ...      | 0      | NaN   | NaN         | NaN     | 0      | 12     | 2008     | WD            | Normal    | 250000 |

5 rows × 80 columns



这时候大概心里可以有数，哪些地方需要人为的处理一下，以做到源数据更加好被 process 。

## Step 2: 合并数据

<span style="color:red;">这个地方要注意：在现实中，我们该怎么做还是怎么做，然后把对训练数据的处理写成一个 function ，然后把测试集的数据上应用这个 function 。但是对于竞赛而言，由于已经拿到了训练街和测试集，因此可以合并后同意处理。</span>

这么做主要是为了用 DataFrame 进行数据预处理的时候更加方便。等所有的需要的预处理进行完之后，我们再把他们分隔开。<span style="color:red;">嗯。</span>

首先，`SalePrice`作为我们的训练目标，只会出现在训练集中，不会在测试集中（要不然你测试什么？）。所以，我们先把 `SalePrice` 这一列给拿出来，不让它碍事儿。

我们先看一下 `SalePrice` 是什么样子：

<span style="color:red;">可见，在认识数据这一步，也要看看数据是不是均匀分布的。然后在这里才会对数据进行这种 `log1p` 的处理。还是没有理解数据要满足什么样子？</span>

```python
%matplotlib inline
prices = pd.DataFrame({"price":train_df["SalePrice"], "log(price + 1)":np.log1p(train_df["SalePrice"])})
prices.hist()
```

```
array([[<matplotlib.axes._subplots.AxesSubplot object at 0x10864a5f8>,
        <matplotlib.axes._subplots.AxesSubplot object at 0x1092429b0>]], dtype=object)
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/l5HH5dj85D.png?imageslim)

从上图可以看出，上面的 price  不是类正态的一个分部。由于我的数据集是偏着的，我的出来的结果也会偏着。<span style="color:red;">没明白为什么这样处理之后结果就不偏了？</span>

可见，label 本身并不平滑。为了我们分类器的学习更加准确，我们会首先把 label 给“平滑化”（正态化）。<span style="color:red;">什么叫不平滑？还有哪些可以使数据平滑的方式？</span>

<span style="color:red;">老师说，只有对这种回归问题才要做这个平滑性的处理，对于二分类或多分类问题就不用这么做。</span>

这一步大部分同学会 miss 掉，导致自己的结果总是达不到一定标准。

这里我们使用最有逼格的`log1p`, 也就是 `log(x+1)`，避免了复值的问题。

记住哟，如果我们这里把数据都给平滑化了，那么最后算结果的时候，要记得把预测到的平滑数据给变回去。

按照“怎么来的怎么去”原则，`log1p()`就需要`expm1()`; 同理，`log()`就需要`exp()`, ... etc。


```python
y_train = np.log1p(train_df.pop('SalePrice'))
```

然后我们把剩下的部分合并起来


```python
all_df = pd.concat((train_df, test_df), axis=0)
```

此刻，我们可以看到`all_df`就是我们合在一起的 DataFrame


```python
all_df.shape
```

```
(2919, 79)
```


而 `y_train` 则是 `SalePrice` 那一列


```python
y_train.head()
```




```
Id
1    12.247699
2    12.109016
3    12.317171
4    11.849405
5    12.429220
Name: SalePrice, dtype: float64
```



## Step 3: 变量转化

其实就是特征工程的一部分，把数据中的乱七八糟的东西统一成用数字表现的形式。

这一步没有什么标准化的方式，你要看这个数据到底是什么样子的。然后你要思考到底要怎么才能用数据化的方式表达出来。

#### 正确化变量属性

首先，我们注意到，`MSSubClass` 的值其实应该是一个 category ，怎么知道的呢？这个就是 data_description.txt 里面会说的。因此数据说明的文档一定要读，不然你根本没法处理好数据。

由于 `MSSubClass` 的值实际上表达的是这个房子的等级，比如 60 就是 60 级的房子。因此这个等级的值之间其实我们是不关心他们相对大小的。而这个事情 Pandas 是不会自动知道的，在我们使用 DataFrame 的时候，这类数字符号只会被默认记成数字。

因此这种情况的数字就很有误导性，我们需要把它变回成 `string`。


```python
all_df['MSSubClass'].dtypes
```

输出：

```
dtype('int64')
```

我们把它变成 `str` 格式：

```python
all_df['MSSubClass'] = all_df['MSSubClass'].astype(str)
```

变成 `str` 以后，我们统计一下这些层级各有多少房子：

```python
all_df['MSSubClass'].value_counts()
```


```
20     1079
60      575
50      287
120     182
30      139
70      128
160     128
80      118
90      109
190      61
85       48
75       23
45       18
180      17
40        6
150       1
Name: MSSubClass, dtype: int64
```



#### 把 category 的变量转变成 numerical 表达形式


上面，我们把类别中的层级数字转化成 `str` 了，但是这些是不能被 pandas 处理的，因此，我们要再把他们处理成 numerical 的形式。

当我们用 numerical 来表达 categorical 的时候，要注意，数字本身有大小的含义，所以乱用数字会给之后的模型学习带来麻烦。于是我们可以用 One-Hot的方法来表达 `category`。

pandas自带的`get_dummies`方法，可以帮你一键做到 One-Hot。


```python
pd.get_dummies(all_df['MSSubClass'], prefix='MSSubClass').head()
```

<span style="color:red;">如果 `MSSubClass` 里面有 `NaN` ，那么这个 get_dummies 会有什么结果？</span>


| MSSubClass_120 | MSSubClass_150 | MSSubClass_160 | MSSubClass_180 | MSSubClass_190 | MSSubClass_20 | MSSubClass_30 | MSSubClass_40 | MSSubClass_45 | MSSubClass_50 | MSSubClass_60 | MSSubClass_70 | MSSubClass_75 | MSSubClass_80 | MSSubClass_85 | MSSubClass_90 |      |
| -------------- | -------------- | -------------- | -------------- | -------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ---- |
| Id             |                |                |                |                |               |               |               |               |               |               |               |               |               |               |               |      |
| 1              | 0.0            | 0.0            | 0.0            | 0.0            | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 1.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0  |
| 2              | 0.0            | 0.0            | 0.0            | 0.0            | 0.0           | 1.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0  |
| 3              | 0.0            | 0.0            | 0.0            | 0.0            | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 1.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0  |
| 4              | 0.0            | 0.0            | 0.0            | 0.0            | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 1.0           | 0.0           | 0.0           | 0.0           | 0.0  |
| 5              | 0.0            | 0.0            | 0.0            | 0.0            | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0           | 1.0           | 0.0           | 0.0           | 0.0           | 0.0           | 0.0  |





此刻 *MSSubClass* 被我们分成了12个 column，每一个代表一个 category 。是就是1，不是就是0。

同理，我们把所有的 category 数据，都给 One-Hot 了


```python
all_dummy_df = pd.get_dummies(all_df)
all_dummy_df.head()
```

<span style="color:red;">这样处理，里面的分类的值为 NaN 的怎么办？</span>


| LotFrontage | LotArea | OverallQual | OverallCond | YearBuilt | YearRemodAdd | MasVnrArea | BsmtFinSF1 | BsmtFinSF2 | BsmtUnfSF | ...   | SaleType_ConLw | SaleType_New | SaleType_Oth | SaleType_WD | SaleCondition_Abnorml | SaleCondition_AdjLand | SaleCondition_Alloca | SaleCondition_Family | SaleCondition_Normal | SaleCondition_Partial |      |
| ----------- | ------- | ----------- | ----------- | --------- | ------------ | ---------- | ---------- | ---------- | --------- | ----- | -------------- | ------------ | ------------ | ----------- | --------------------- | --------------------- | -------------------- | -------------------- | -------------------- | --------------------- | ---- |
| Id          |         |             |             |           |              |            |            |            |           |       |                |              |              |             |                       |                       |                      |                      |                      |                       |      |
| 1           | 65.0    | 8450        | 7           | 5         | 2003         | 2003       | 196.0      | 706.0      | 0.0       | 150.0 | ...            | 0.0          | 0.0          | 0.0         | 1.0                   | 0.0                   | 0.0                  | 0.0                  | 0.0                  | 1.0                   | 0.0  |
| 2           | 80.0    | 9600        | 6           | 8         | 1976         | 1976       | 0.0        | 978.0      | 0.0       | 284.0 | ...            | 0.0          | 0.0          | 0.0         | 1.0                   | 0.0                   | 0.0                  | 0.0                  | 0.0                  | 1.0                   | 0.0  |
| 3           | 68.0    | 11250       | 7           | 5         | 2001         | 2002       | 162.0      | 486.0      | 0.0       | 434.0 | ...            | 0.0          | 0.0          | 0.0         | 1.0                   | 0.0                   | 0.0                  | 0.0                  | 0.0                  | 1.0                   | 0.0  |
| 4           | 60.0    | 9550        | 7           | 5         | 1915         | 1970       | 0.0        | 216.0      | 0.0       | 540.0 | ...            | 0.0          | 0.0          | 0.0         | 1.0                   | 1.0                   | 0.0                  | 0.0                  | 0.0                  | 0.0                   | 0.0  |
| 5           | 84.0    | 14260       | 8           | 5         | 2000         | 2000       | 350.0      | 655.0      | 0.0       | 490.0 | ...            | 0.0          | 0.0          | 0.0         | 1.0                   | 0.0                   | 0.0                  | 0.0                  | 0.0                  | 1.0                   | 0.0  |

5 rows × 303 columns



#### 处理好numerical变量


就算是 numerical 的变量，也还会有一些小问题。

比如，有一些数据是缺失的：<span style="color:red;">对于缺失值得处理还是要看一下 data_description.txt 的介绍</span>


```python
all_dummy_df.isnull().sum().sort_values(ascending=False).head(10)
```



```
LotFrontage     486
GarageYrBlt     159
MasVnrArea       23
BsmtHalfBath      2
BsmtFullBath      2
BsmtFinSF2        1
GarageCars        1
TotalBsmtSF       1
BsmtUnfSF         1
GarageArea        1
dtype: int64
```


可以看到，缺失最多的 column 是 `LotFrontage`

处理这些缺失的信息，得靠好好审题。一般来说，数据集的描述里会写的很清楚，这些缺失都代表着什么。当然，如果实在没有的话，也只能靠自己的『想当然』。。

在这里，我们用平均值来填满这些空缺。


```python
mean_cols = all_dummy_df.mean()
mean_cols.head(10)
```




```
LotFrontage        69.305795
LotArea         10168.114080
OverallQual         6.089072
OverallCond         5.564577
YearBuilt        1971.312778
YearRemodAdd     1984.264474
MasVnrArea        102.201312
BsmtFinSF1        441.423235
BsmtFinSF2         49.582248
BsmtUnfSF         560.772104
dtype: float64
```

<span style="color:red;">这样直接使用 `fillna` 是不是太粗暴了？ 想要详细的填充要怎么办？</span>


```python
all_dummy_df = all_dummy_df.fillna(mean_cols)
```

看看是不是没有空缺了？


```python
all_dummy_df.isnull().sum().sum()
```

```
0
```



#### 标准化 numerical 数据

这一步并不是必要，但是得看你想要用的分类器是什么。一般来说，regression 的分类器都比较傲娇，最好是把源数据给放在一个标准分布内。不要让数据间的差距太大。<span style="color:red;">为什么呢？什么是一个标准分布？</span>

这里，我们当然不需要把 One-Hot 的那些 0/1 数据给标准化。我们的目标应该是那些本来就是 numerical 的数据：<span style="color:red;">嗯。</span>

先来看看哪些是 numerical 的：


```python
numeric_cols = all_df.columns[all_df.dtypes != 'object']
numeric_cols
```




```
Index(['LotFrontage', 'LotArea', 'OverallQual', 'OverallCond', 'YearBuilt',
       'YearRemodAdd', 'MasVnrArea', 'BsmtFinSF1', 'BsmtFinSF2', 'BsmtUnfSF',
       'TotalBsmtSF', '1stFlrSF', '2ndFlrSF', 'LowQualFinSF', 'GrLivArea',
       'BsmtFullBath', 'BsmtHalfBath', 'FullBath', 'HalfBath', 'BedroomAbvGr',
       'KitchenAbvGr', 'TotRmsAbvGrd', 'Fireplaces', 'GarageYrBlt',
       'GarageCars', 'GarageArea', 'WoodDeckSF', 'OpenPorchSF',
       'EnclosedPorch', '3SsnPorch', 'ScreenPorch', 'PoolArea', 'MiscVal',
       'MoSold', 'YrSold'],
      dtype='object')
```



计算标准分布： $(X-X')/s$

让我们的数据点更平滑，更便于计算。

注意：我们这里也是可以继续使用`Log`的，我只是给大家展示一下多种“使数据平滑”的办法。


```python
numeric_col_means = all_dummy_df.loc[:, numeric_cols].mean()
numeric_col_std = all_dummy_df.loc[:, numeric_cols].std()
all_dummy_df.loc[:, numeric_cols] = (all_dummy_df.loc[:, numeric_cols] - numeric_col_means) / numeric_col_std
```

<span style="color:red;">上面这个例子没有特别掌握。要仔细看下。</span>

## Step 4: 建立模型

#### 把数据集分回 训练/测试集


```python
dummy_train_df = all_dummy_df.loc[train_df.index]
dummy_test_df = all_dummy_df.loc[test_df.index]
```


```python
dummy_train_df.shape, dummy_test_df.shape
```

输出：


```
((1460, 303), (1459, 303))
```



#### Ridge Regression

用 Ridge Regression 模型来跑一遍看看。（对于多因子的数据集，这种模型可以方便的把所有的 var 都无脑的放进去） <span style="color:red;">Ridge Regression 是回归模型的一种</span>


```python
from sklearn.linear_model import Ridge
from sklearn.model_selection import cross_val_score
```

这一步不是很必要，只是把 DataFrame 转化成 Numpy Array，这跟Sklearn更加配：<span style="color:red;">嗯。</span>

```python
X_train = dummy_train_df.values
X_test = dummy_test_df.values
```

用 Sklearn 自带的 cross validation 交叉验证方法来测试模型：


```python
alphas = np.logspace(-3, 2, 50)
test_scores = []
for alpha in alphas:
    clf = Ridge(alpha)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```

<span style="color:red;">这个 test_score 为什么是这个式子？表示什么意思？</span>

test_scores 把每次交叉验证的得分记录下来。这个参数的调参方法是直接用循环来便利搜索，其实有更好的方法是 gridsearch 。

存下所有的 CV 值，看看哪个alpha值更好（这就是调参数的过程）


```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(alphas, test_scores)
plt.title("Alpha vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/CHj7De3B84.png?imageslim)

可见，大概 alpha=10~20 的时候，可以把 score 达到 0.135 左右。这时候就比较低了。这时候还可以吧范围设定到 10~20 ，然后继续搜索。


#### Random Forest


```python
from sklearn.ensemble import RandomForestRegressor
```


```python
max_features = [.1, .3, .5, .7, .9, .99]
test_scores = []
for max_feat in max_features:
    clf = RandomForestRegressor(n_estimators=200, max_features=max_feat)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=5, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```

`max_features` 树最多使用百分之多少的特征。


```python
plt.plot(max_features, test_scores)
plt.title("Max Features vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/7j0AHCae4k.png?imageslim)


也就是说每棵树使用 30% 的特征的时候错误率最低，为 0.137


## Step 5: Ensemble

上面我们已经找到了一个有最优超参的 Ridge 模型和一个 有最优超参的随机森林模型。

因此这时候，我们就可以结合着两个最优的模型：这里我们用一个 Stacking 的思维来汲取两种或者多种模型的优点

首先，我们把最好的 parameter 拿出来，做成我们最终的 model


```python
ridge = Ridge(alpha=15)
rf = RandomForestRegressor(n_estimators=500, max_features=.3)
```

我们使用这两个模型分别 fit 训练集：<span style="color:red;"> fit 是什么意思？是在固定超参数的情况下把里面的参数训练出来吗？</span>

```python
ridge.fit(X_train, y_train)
rf.fit(X_train, y_train)
```


```
RandomForestRegressor(bootstrap=True, criterion='mse', max_depth=None,
           max_features=0.3, max_leaf_nodes=None, min_impurity_split=1e-07,
           min_samples_leaf=1, min_samples_split=2,
           min_weight_fraction_leaf=0.0, n_estimators=500, n_jobs=1,
           oob_score=False, random_state=None, verbose=0, warm_start=False)
```



上面提到了，因为最前面我们给`label`做了个`log(1+x)`, 于是这里我们需要把predit的值给`exp`回去，并且减掉那个"1"

所以就是我们的`expm1()`函数。exp minus 1。


```python
y_ridge = np.expm1(ridge.predict(X_test))
y_rf = np.expm1(rf.predict(X_test))
```

一个正经的Ensemble是把这群model的预测结果作为新的input，再做一次预测。这里我们简单的方法，就是直接『平均化』。相当于一个投票的方式。


```python
y_final = (y_ridge + y_rf) / 2
```


<span style="color:red;">上面生成的 Ridge 和 RandomForest 算是弱优化器吗？他们两个的 ensamble 没有问题吗？</span>

## Step 6: 提交结果

要看一下提交的格式要求是什么，然后创建对应的 DataFrame。

```python
submission_df = pd.DataFrame(data= {'Id' : test_df.index, 'SalePrice': y_final})
```

我们的 `submission` 大概长这样：


```python
submission_df.head(10)
```



|    |  Id  |         SalePrice      |
| ---- | --------- | ------------- |
| 0    | 1461      | 119595.627405 |
| 1    | 1462      | 152127.359971 |
| 2    | 1463      | 174472.484621 |
| 3    | 1464      | 189936.942219 |
| 4    | 1465      | 193934.290197 |
| 5    | 1466      | 175889.222850 |
| 6    | 1467      | 177835.726832 |
| 7    | 1468      | 169239.114752 |
| 8    | 1469      | 184864.220939 |
| 9    | 1470      | 123773.699896 |




到这里，一个简单的标准版过程就完成了。但是结果并没有特别的优。OK，advance 会讲到底怎么把结果提高一些。





## REF

- https://www.kaggle.com/c/house-prices-advanced-regression-techniques


## 数据下载地址

- 链接：https://pan.baidu.com/s/1aSG9ytuZfC4MTdLcpNBtzw 密码：staq
