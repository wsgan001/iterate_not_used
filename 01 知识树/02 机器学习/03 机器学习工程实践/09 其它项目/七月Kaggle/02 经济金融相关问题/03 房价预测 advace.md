
# 房价预测案例（进阶版）

在基础版，我们已经知道这些数据是怎么处理，可以使用什么模型，然后怎么提交结果。

但是基础版的精度并不是特别高，现在的比赛都是想方设法提高精度，因此，看看有什么可以提高的。

这个是进阶版的notebook。主要是为了比较几种模型框架。所以前面的特征工程部分内容，我也并没有做任何改动，重点都在后面的模型建造section

下面会讲在所有的竞赛题上的最好的做法是什么样的。

主要的不同就是 ensemble 的方式不同：



#### 做一点高级的 Ensemble

一般来说，单个分类器的效果真的是很有限。我们会倾向于把 N 多的分类器合在一起，做一个“综合分类器”以达到最好的效果。

从基础版本我们已经知道：Ridge 里面 alpha=15 时效果最好，那么我们先创建一个 ridge ：


```python
from sklearn.linear_model import Ridge
ridge = Ridge(15)
```

我们准备使用 Bagging 。

#### Bagging

Bagging 把很多的小分类器放在一起，每个 train 随机的一部分数据，然后把它们的最终结果综合起来（多数投票制）。

Sklearn 已经直接提供了这套构架，我们直接调用就行：<span style="color:red;">sklearn 里面所有的方法都有两套，如果是处理回归问题，那么他的方法名里面有 Regressor，如果是处理分类问题，它的方法名里面有 Classifier 。</span>


```python
from sklearn.ensemble import BaggingRegressor
from sklearn.model_selection import cross_val_score
```

在这里，我们用 CV 结果来测试不同的分类器个数对最后结果的影响。

注意，我们在部署 Bagging 的时候，要把它的函数 base_estimator 里填上你的小分类器（这里我们使用这个我们认为已经被调到最优的若分类器 ridge）<span style="color:red;">嗯。如果不放的话，就会默认用一个树。</span>


```python
params = [1, 10, 15, 20, 25, 30, 40]
test_scores = []
for param in params:
    clf = BaggingRegressor(n_estimators=param, base_estimator=ridge)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```

<span style="color:red;">params 里面就是我们想尝试的小分类器的个数</span>

```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(params, test_scores)
plt.title("n_estimator vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/bC3agm49EF.png?imageslim)

可见，前一个版本中，ridge 最优结果也就是 0.135 ；而这里，我们使用 25个小 ridge 分类器的 bagging ，达到了低于 0.132 的结果。<span style="color:red;">可见，用 bagging 比不用还是好一些的。</span>

当然了，你如果并没有提前测试过 ridge 模型，你也可以用 Bagging 自带的 DecisionTree 模型：

代码是一样的，把 base_estimator 给删去即可


```python
params = [10, 15, 20, 25, 30, 40, 50, 60, 70, 100]
test_scores = []
for param in params:
    clf = BaggingRegressor(n_estimators=param)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```


```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(params, test_scores)
plt.title("n_estimator vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/jiK1d3274I.png?imageslim)

可见，单纯的使用 Decision Tree 来 bagging 效果还是不是很好的，最好的结果也就 0.140 。当然，这个地方使用的 decision tree 是没有经过调参的。

#### Boosting

Boosting 比 Bagging 理论上更高级点，它也是揽来一把的分类器。但是把他们线性排列。下一个分类器把上一个分类器分类得不好的地方加上更高的权重，这样下一个分类器就能在这个部分学得更加“深刻”。

还是类似的使用方法：

```python
from sklearn.ensemble import AdaBoostRegressor
```


```python
params = [10, 15, 20, 25, 30, 35, 40, 45, 50]
test_scores = []
for param in params:
    clf = AdaBoostRegressor(n_estimators=param, base_estimator=ridge)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```


```python
plt.plot(params, test_scores)
plt.title("n_estimator vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/0EAeDd3IKk.png?imageslim)

可见，Adaboost+Ridge 在 25 个小分类器的情况下，也是达到了接近 0.132的效果。

但是我们可以看到这个误差曲线不是很稳定。那么怎么办呢？我们可以在 15~45 的范围内用更多的参数来调一遍，或者我们使用更多的小分类器 或者更少一些的 cv 。现在的 cv=10 ，那么每个 cv 里面的值可能太少了，可以调成 3，或者 5 。最好能使我们的曲线稳定一些。

同理，这里，你也可以不必输入 Base_estimator，使用 Adaboost 自带的 DT：


```python
params = [10, 15, 20, 25, 30, 35, 40, 45, 50]
test_scores = []
for param in params:
    clf = BaggingRegressor(n_estimators=param)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```


```python
plt.plot(params, test_scores)
plt.title("n_estimator vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/8CbI11AAkK.png?imageslim)

还是大于 0.14 ，看来我们也许要先对要用的决策树模型进行调参，然后再进行 boost 。

#### XGBoost

最后，我们来看看巨牛逼的XGBoost，外号：Kaggle 神器，中国人做的。这依旧是一款Boosting框架的模型，但是却做了很多的改进。在很多使用场景上：比赛、科学界、工业界，XGBoost 的效果都是非常好的。


```python
from xgboost import XGBRegressor
```

用Sklearn自带的cross validation方法来测试模型


```python
params = [1,2,3,4,5,6]
test_scores = []
for param in params:
    clf = XGBRegressor(max_depth=param)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=10, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```

`max_depth` 就是每棵树的最深可以有多深。

存下所有的CV值，看看哪个alpha值更好（也就是『调参数』）


```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(params, test_scores)
plt.title("max_depth vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180721/i6IL42cHl4.png?imageslim)

惊了，深度为5的时候，错误率缩小到 0.127 。<span style="color:red;">震惊了。比我们用的调过参的 ridge 的 bagging 还要好。</span>

这就是为什么，浮躁的竞赛圈，人人都在用XGBoost :)


<span style="color:red;">不知道现在有没有更厉害的出现。</span>
