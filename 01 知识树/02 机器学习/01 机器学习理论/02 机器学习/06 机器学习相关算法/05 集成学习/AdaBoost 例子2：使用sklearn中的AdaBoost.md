---
author: evo
comments: true
date: 2018-05-12 10:24:37+00:00
layout: post
link: http://106.15.37.116/2018/05/12/adaboost-sample2/
slug: adaboost-sample2
title:
wordpress_id: 5587
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1.


[第7章 集成方法 ensemble method](http://ml.apachecn.org/mlia/ensemble-random-tree-adaboost/)







# TODO






  * **这个没怎么看，看完后补充到sample1里面**




# MOTIVE






  * aaa





* * *






# 完整代码



```python
import matplotlib.pyplot as plt
# importing necessary libraries
import numpy as np
from sklearn import metrics
from sklearn.ensemble import AdaBoostRegressor
from sklearn.tree import DecisionTreeRegressor

print(__doc__)

# Create the dataset
rng = np.random.RandomState(1)
X = np.linspace(0, 6, 100)[:, np.newaxis]
y = np.sin(X).ravel() + np.sin(6 * X).ravel() + rng.normal(0, 0.1, X.shape[0])
# dataArr, labelArr = loadDataSet("input/7.AdaBoost/horseColicTraining2.txt")


​
# Fit regression model
regr_1 = DecisionTreeRegressor(max_depth=4)
regr_2 = AdaBoostRegressor(DecisionTreeRegressor(max_depth=4),
                           n_estimators=300,
                           random_state=rng)

regr_1.fit(X, y)
regr_2.fit(X, y)

# Predict
y_1 = regr_1.predict(X)
y_2 = regr_2.predict(X)

# Plot the results
plt.figure()
plt.scatter(X, y, c="k", label="training samples")
plt.plot(X, y_1, c="g", label="n_estimators=1", linewidth=2)
plt.plot(X, y_2, c="r", label="n_estimators=300", linewidth=2)
plt.xlabel("data")
plt.ylabel("target")
plt.title("Boosted Decision Tree Regression")
plt.legend()
plt.show()

print('y---', type(y[0]), len(y), y[:4])
print('y_1---', type(y_1[0]), len(y_1), y_1[:4])
print('y_2---', type(y_2[0]), len(y_2), y_2[:4])

# 适合2分类
y_true = np.array([0, 0, 1, 1])
y_scores = np.array([0.1, 0.4, 0.35, 0.8])
print('y_scores---', type(y_scores[0]), len(y_scores), y_scores)
print(metrics.roc_auc_score(y_true, y_scores))

# print "-" * 100
# print metrics.roc_auc_score(y[:1], y_2[:1])
```

输出：


```
None
y--- <class 'numpy.float64'> 100 [ 0.16243454  0.35506848  0.73293321  0.96056821]
y_1--- <class 'numpy.float64'> 100 [ 0.8381376  0.8381376  0.8381376  0.8381376]
y_2--- <class 'numpy.float64'> 100 [ 0.44598854  0.44598854  0.49302021  0.80710121]
y_scores--- <class 'numpy.float64'> 4 [ 0.1   0.4   0.35  0.8 ]
0.75
```



输出图像：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/LBaaCKALba.png?imageslim)
