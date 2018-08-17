

下面我们使用 pandas 对一小部分抽取出来的数据进行处理

这个是下采样之后可以载入到单机里面进行处理的形式：



# CTR Prediction

	https://www.kaggle.com/c/avazu-ctr-prediction/data

## File descriptions
**train** - Training set. 10 days of click-through data, ordered chronologically. Non-clicks and clicks are subsampled according to different strategies.

	https://www.kaggle.com/c/avazu-ctr-prediction/download/train.gz

**test** - Test set. 1 day of ads to for testing your model predictions.

	https://www.kaggle.com/c/avazu-ctr-prediction/download/test.gz

**sampleSubmission.csv** - Sample submission file in the correct format, corresponds to the All-0.5 Benchmark.

	https://www.kaggle.com/c/avazu-ctr-prediction/download/sampleSubmission.gz

## Data fields
id: ad identifier
click: 0/1 for non-click/click
hour: format is YYMMDDHH, so 14091123 means 23:00 on Sept. 11, 2014 UTC.

C1 -- anonymized categorical variable, banner_pos, site_id, site_domain, site_category, app_id, app_domain, app_category, device_id, device_ip, device_model, device_type, device_conn_type, C14-C21 -- anonymized categorical variables

# Load Data


```python
import pandas as pd


# Initial setup
train_filename = "train_small.csv"
test_filename = "test.csv"
submission_filename = "submit.csv"

training_set = pd.read_csv(train_filename)
```

```
/Library/Python/2.7/site-packages/IPython/core/interactiveshell.py:2723: DtypeWarning: Columns (0) have mixed types. Specify dtype option on import or set low_memory=False.
  interactivity=interactivity, compiler=compiler, result=result)
```


# Explore Data

首先看一下你的数据：

```python
training_set.head(10)
```



|      | id                   | click | hour     | C1   | banner_pos | site_id  | site_domain | site_category | app_id   | app_domain | ...  | device_type | device_conn_type | C14   | C15  | C16  | C17  | C18  | C19  | C20    | C21  |
| ---- | -------------------- | ----- | -------- | ---- | ---------- | -------- | ----------- | ------------- | -------- | ---------- | ---- | ----------- | ---------------- | ----- | ---- | ---- | ---- | ---- | ---- | ------ | ---- |
| 0    | 1000009418151094273  | 0     | 14102100 | 1005 | 0          | 1fbe01fe | f3845767    | 28905ebd      | ecad2386 | 7801e8d9   | ...  | 1           | 2                | 15706 | 320  | 50   | 1722 | 0    | 35   | -1     | 79   |
| 1    | 10000169349117863715 | 0     | 14102100 | 1005 | 0          | 1fbe01fe | f3845767    | 28905ebd      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 15704 | 320  | 50   | 1722 | 0    | 35   | 100084 | 79   |
| 2    | 10000371904215119486 | 0     | 14102100 | 1005 | 0          | 1fbe01fe | f3845767    | 28905ebd      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 15704 | 320  | 50   | 1722 | 0    | 35   | 100084 | 79   |
| 3    | 10000640724480838376 | 0     | 14102100 | 1005 | 0          | 1fbe01fe | f3845767    | 28905ebd      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 15706 | 320  | 50   | 1722 | 0    | 35   | 100084 | 79   |
| 4    | 10000679056417042096 | 0     | 14102100 | 1005 | 1          | fe8cc448 | 9166c161    | 0569f928      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 18993 | 320  | 50   | 2161 | 0    | 35   | -1     | 157  |
| 5    | 10000720757801103869 | 0     | 14102100 | 1005 | 0          | d6137915 | bb1ef334    | f028772b      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 16920 | 320  | 50   | 1899 | 0    | 431  | 100077 | 117  |
| 6    | 10000724729988544911 | 0     | 14102100 | 1005 | 0          | 8fda644b | 25d4cfcd    | f028772b      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 20362 | 320  | 50   | 2333 | 0    | 39   | -1     | 157  |
| 7    | 10000918755742328737 | 0     | 14102100 | 1005 | 1          | e151e245 | 7e091613    | f028772b      | ecad2386 | 7801e8d9   | ...  | 1           | 0                | 20632 | 320  | 50   | 2374 | 3    | 39   | -1     | 23   |
| 8    | 10000949271186029916 | 1     | 14102100 | 1005 | 0          | 1fbe01fe | f3845767    | 28905ebd      | ecad2386 | 7801e8d9   | ...  | 1           | 2                | 15707 | 320  | 50   | 1722 | 0    | 35   | -1     | 79   |
| 9    | 10001264480619467364 | 0     | 14102100 | 1002 | 0          | 84c7ba46 | c4e18dd6    | 50e219e0      | ecad2386 | 7801e8d9   | ...  | 0           | 0                | 21689 | 320  | 50   | 2496 | 3    | 167  | 100191 | 23   |


10 rows × 24 columns

id 类的特征也是有一些处理的方法的。可以看到，前10条，只有1条发生了点击的。banner_pos 就是广告展示的位置，不同的位置价格也是不一样的。

可见，上面的数据都已经做过 hash 处理，是看不到明文的。

pandas 有很好的函数，比如 describe ，这样就可以对数据进行描述：

```python
training_set.describe()
```



|       | click        | hour       | C1           | banner_pos   | device_type  | device_conn_type | C14          | C15          | C16          | C17          | C18          | C19          | C20           | C21          |
| ----- | ------------ | ---------- | ------------ | ------------ | ------------ | ---------------- | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------- | ------------ |
| count | 99999.000000 | 99999.0    | 99999.000000 | 99999.000000 | 99999.000000 | 99999.000000     | 99999.000000 | 99999.000000 | 99999.000000 | 99999.000000 | 99999.000000 | 99999.000000 | 99999.000000  | 99999.000000 |
| mean  | 0.174902     | 14102100.0 | 1005.034440  | 0.198302     | 1.055741     | 0.199272         | 17682.106071 | 318.333943   | 56.818988    | 1964.029090  | 0.789328     | 131.735447   | 37874.606366  | 88.555386    |
| std   | 0.379885     | 0.0        | 1.088705     | 0.402641     | 0.583986     | 0.635271         | 3237.726956  | 11.931998    | 36.924283    | 394.961129   | 1.223747     | 244.077816   | 48546.369299  | 45.482979    |
| min   | 0.000000     | 14102100.0 | 1001.000000  | 0.000000     | 0.000000     | 0.000000         | 375.000000   | 120.000000   | 20.000000    | 112.000000   | 0.000000     | 33.000000    | -1.000000     | 13.000000    |
| 25%   | 0.000000     | 14102100.0 | 1005.000000  | 0.000000     | 1.000000     | 0.000000         | 15704.000000 | 320.000000   | 50.000000    | 1722.000000  | 0.000000     | 35.000000    | -1.000000     | 61.000000    |
| 50%   | 0.000000     | 14102100.0 | 1005.000000  | 0.000000     | 1.000000     | 0.000000         | 17654.000000 | 320.000000   | 50.000000    | 1993.000000  | 0.000000     | 35.000000    | -1.000000     | 79.000000    |
| 75%   | 0.000000     | 14102100.0 | 1005.000000  | 0.000000     | 1.000000     | 0.000000         | 20362.000000 | 320.000000   | 50.000000    | 2306.000000  | 2.000000     | 39.000000    | 100083.000000 | 156.000000   |
| max   | 1.000000     | 14102100.0 | 1010.000000  | 5.000000     | 5.000000     | 5.000000         | 21705.000000 | 728.000000   | 480.000000   | 2497.000000  | 3.000000     | 1835.000000  | 100248.000000 | 157.000000   |


click 的 mean 是0.17 可见是每展示 100 条，有 17 条被点击。click 的时候的 std 是 0.37988 ，说明还是有些抖动的。

下面我们开始建模：

```python
# id: ad identifier
# click: 0/1 for non-click/click
# hour: format is YYMMDDHH, so 14091123 means 23:00 on Sept. 11, 2014 UTC.
# C1 -- anonymized categorical variable
# banner_pos
# site_id
# site_domain
# site_category
# app_id
# app_domain
# app_category
# device_id
# device_ip
# device_model
# device_type
# device_conn_type
# C14-C21 -- anonymized categorical variables
from sklearn.externals import joblib
from sklearn.cross_validation import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn import metrics

from utils import load_df
```

在 CTR 和排序这个场景下，最常用到的评估方式就是 AUC，而不是准确率、召回率、 F1 什么的。但是实际的比赛中还是要注意看题目的要求。


```
/Library/Python/2.7/site-packages/sklearn/cross_validation.py:44: DeprecationWarning: This module was deprecated in version 0.18 in favor of the model_selection module into which all the refactored classes and functions are moved. Also note that the interface of the new CV iterators are different from that of this module. This module will be removed in 0.20.
  "This module will be removed in 0.20.", DeprecationWarning)
```

上面提到的 utils 是自己编写的用来载入数据的，内容如下：

```python
import pandas as pd

def clean_df(df, training=True):
    df = df.drop(['site_id', 'app_id', 'device_id', 'device_ip', 'site_domain',
                  'site_category', 'app_domain', 'app_category', 'device_model'], axis=1)
    if training:
        # id column is not required for training purposes
        df = df.drop(['id'], axis=1)
    return df

def load_df(filename, training=True, **csv_options):
    df = pd.read_csv(filename, header=0, **csv_options)
    df = clean_df(df, training=training)
    return df
```

之所以在 load 的时候 drop 了一些特征，主要是因为我们在 04 的分析过程中知道了，哪些特征是重要的。



下面这三个函数是用来判断
```python
# 结果衡量
def print_metrics(true_values, predicted_values):
    print "Accuracy: ", metrics.accuracy_score(true_values, predicted_values)
    print "AUC: ", metrics.roc_auc_score(true_values, predicted_values)
    print "Confusion Matrix: ", + metrics.confusion_matrix(true_values, predicted_values)
    print metrics.classification_report(true_values, predicted_values)

# 拟合分类器 其实就是 fit
def classify(classifier_class, train_input, train_targets):
    classifier_object = classifier_class()
    classifier_object.fit(train_input, train_targets)
    return classifier_object

# 模型存储
def save_model(clf):
    joblib.dump(clf, 'classifier.pkl')
```

<span style="color:red;">为什么生成的文件要是 .pkl 格式的？而且 joblib 之前没有怎么使用过。</span>


```python
train_data = load_df('train_small.csv').values
```

```
/Library/Python/2.7/site-packages/IPython/core/interactiveshell.py:2825: DtypeWarning: Columns (0) have mixed types. Specify dtype option on import or set low_memory=False.
  if self.run_code(code, result):
```



```python
train_data[:,:]
```


```
array([[       0, 14102100,     1005, ...,       35,       -1,       79],
       [       0, 14102100,     1005, ...,       35,   100084,       79],
       [       0, 14102100,     1005, ...,       35,   100084,       79],
       ...,
       [       0, 14102100,     1005, ...,       35,       -1,       79],
       [       1, 14102100,     1005, ...,       35,       -1,       79],
       [       0, 14102100,     1005, ...,       35,       -1,       79]])
```




```python
# 训练和存储模型
X_train, X_test, y_train, y_test = train_test_split(train_data[0::, 1::],
    train_data[0::, 0],
    test_size=0.3,
    random_state=0)

classifier = classify(LogisticRegression, X_train, y_train)
predictions = classifier.predict(X_test)
print_metrics(y_test, predictions)
save_model(classifier)
```

```
Accuracy:  0.8233
AUC:  0.5
Confusion Matrix:
[[24699     0]
 [ 5301     0]]
             precision    recall  f1-score   support

          0       0.82      1.00      0.90     24699
          1       0.00      0.00      0.00      5301

avg / total       0.68      0.82      0.74     30000


/usr/local/lib/python2.7/site-packages/numpy/core/fromnumeric.py:2652: VisibleDeprecationWarning: `rank` is deprecated; use the `ndim` attribute or function instead. To find the rank of a matrix see `numpy.linalg.matrix_rank`.
  VisibleDeprecationWarning)
/Library/Python/2.7/site-packages/sklearn/metrics/classification.py:1113: UndefinedMetricWarning: Precision and F-score are ill-defined and being set to 0.0 in labels with no predicted samples.
  'precision', 'predicted', average, warn_for)
```


上面的 AUC 非常的低，0.5 意味着基本上是随便猜的。之所以是 0.5 是因为我们这里使用的样本集非常的小。


```python
# 按照指定的格式生成结果
def create_submission(ids, predictions, filename='submission.csv'):
    submissions = np.concatenate((ids.reshape(len(ids), 1),
        predictions.reshape(len(predictions), 1)),
        axis=1)
    df = DataFrame(submissions)
    df.to_csv(filename, header=['id', 'click'], index=False)
```

<span style="color:red;">上面这段代码中的 np.concatenate 没有很明白。</span>


```python
import numpy as np
from pandas import DataFrame

classifier = joblib.load('classifier.pkl')
test_data_df = load_df('test.csv', training=False)
ids = test_data_df.values[0:, 0]
predictions = classifier.predict(test_data_df.values[0:, 1:])
create_submission(ids, predictions)
```



可见，对于要使用 LR 进行处理的，可以先看下数据的那些特征比较重要，那些特征中的值是需要在One-hot之后进行合并的，这些分析的过程都在 04 .md 里面了，要把这两个 md 合并起来。


下采样的方式主要就是这样。

那么如果想用海量数据怎么样呢？因为这下采样毕竟丢失了部分信息。
