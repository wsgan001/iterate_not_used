

# Ensemble Learning-模型融合-Python实现

2017年07月16日 19:08:36

阅读数：8657

- Ensemble Learning-模型融合
  - [1 Voting](https://blog.csdn.net/shine19930820/article/details/75209021#11-voting)
  - [2 Averaging](https://blog.csdn.net/shine19930820/article/details/75209021#12-averaging)
  - [3 Ranking](https://blog.csdn.net/shine19930820/article/details/75209021#13-ranking)
  - [4 Binning](https://blog.csdn.net/shine19930820/article/details/75209021#14-binning)
  - [5 Bagging](https://blog.csdn.net/shine19930820/article/details/75209021#15-bagging)
  - [6 Boosting](https://blog.csdn.net/shine19930820/article/details/75209021#16-boosting)
  - [7 Stacking](https://blog.csdn.net/shine19930820/article/details/75209021#17-stacking)
  - [8 Blending](https://blog.csdn.net/shine19930820/article/details/75209021#18-blending)
- [融合的条件](https://blog.csdn.net/shine19930820/article/details/75209021#2-%E8%9E%8D%E5%90%88%E7%9A%84%E6%9D%A1%E4%BB%B6)
- Python实现
  - [1 Stacking](https://blog.csdn.net/shine19930820/article/details/75209021#31-stacking)
  - [2 Blending](https://blog.csdn.net/shine19930820/article/details/75209021#32-blending)
- [Reference](https://blog.csdn.net/shine19930820/article/details/75209021#reference)

> **Wisdom of the crowds == ensemble machine learning**

# 1 Ensemble Learning-模型融合

通过对多个单模型融合以提升整体性能。

## 1.1 Voting

投票制即为，投票多者为最终的结果。例如一个分类问题，多个模型投票（当然可以设置权重）。最终投票数最多的类为最终被预测的类。

## 1.2 Averaging

Averaging即所有预测器的结果平均。

- 回归问题，直接取平均值作为最终的预测值。（也可以使用加权平均）
- 分类问题，直接将模型的预测概率做平均。（or 加权）

加权平均，其公式如下：

∑i=1nWeighti∗Pi∑i=1nWeighti∗Pi

其中nn表示模型的个数， WeightiWeighti表示该模型权重，PiPi表示模型i的预测概率值。

例如两个分类器，XGBoost（权重0.4）和LightGBM（权重0.6），其预测概率分别为：0.75、0.5，那么最终的预测概率，(0.4 * 0.75+0.6 * 0.5)/(0.4+0.6)=0.6

**模型权重也可以通过机器学习模型学习得到**

## 1.3 Ranking

Rank的思想其实和Averaging一致，但Rank是把排名做平均，对于AUC指标比较有效。

个人认为其实就是Learning to rank的思想，可以来优化搜索排名。具体公式如下：

∑i=1nWeightiRanki∑i=1nWeightiRanki

其中nn表示模型的个数， WeightiWeighti表示该模型权重，所有权重相同表示平均融合。RankiRanki表示样本在第i个模型中的升序排名。它可以较快的利用排名融合多个模型之间的差异，而不需要加权融合概率。

## 1.4 Binning

将单个模型的输出放到一个桶中。参考[pdf paper](http://cseweb.ucsd.edu/~elkan/254spring01/jdrishrep.pdf) ， [Guocong Song](http://www.kaggle.com/users/41275/guocong-song) ，

## 1.5 Bagging

使用训练数据的不同随机子集来训练每个 Base Model，最后每个 Base Model 权重相同，分类问题进行投票，回归问题平均。

随机森林就用到了Bagging，并且具有天然的并行性。

## 1.6 Boosting

Boosting是一种迭代的方法，每一次训练会更关心上一次被分错的样本，比如改变被错分的样本的权重的Adaboost方法。还有许多都是基于这种思想，比如Gradient Boosting等。

**经典问题：随机森林、Adaboost、GBDT、XGBoost的区别是什么？**（面试常常被问）

## 1.7 Stacking

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/1c7K5i4iA2.png?imageslim)

图片来自[Wille的博客](https://dnc1994.com/2016/04/rank-10-percent-in-first-kaggle-competition/)。

从上图可以看出，类似交叉验证。

1. 将数据集分为K个部分，共有n个模型。

2. for i in xrange(n):

    for i in xrange(k):

    用第i个部分作为预测，剩余的部分来训练模型，获得其预测的输出作为第i部分的新特征。

    对于测试集，直接用这k个模型的预测值均值作为新的特征。

3. 这样k次下来，整个数据集都获得了这个模型构建的New Feature。n个模型训练下来，这个模型就有n个New Features。

4. 把New Features和label作为新的分类器的输入进行训练。然后输入测试集的New Features输入模型获得最终的预测结果。

## 1.8 Blending

Blending直接用不相交的数据集用于不同层的训练。

以两层的Blending为例，训练集划分为两部分（d1，d2），测试集为test。

1. 第一层：用d1训练多个模型，讲其对d2和test的预测结果作为第二层的New Features。
2. 第二层：用d2的New Features和标签训练新的分类器，然后把test的New Features输入作为最终的预测值。

# 2 融合的条件

- **Base Model 之间的相关性要尽可能的小。**这就是为什么非 Tree-based Model 往往表现不是最好但还是要将它们包括在 Ensemble 里面的原因。Ensemble 的 Diversity 越大，最终 Model 的 Bias 就越低。
- **Base Model 之间的性能表现不能差距太大。**这其实是一个 **Trade-off**，在实际中很有可能表现相近的 Model 只有寥寥几个而且它们之间相关性还不低。但是实践告诉我们即使在这种情况下 Ensemble 还是能大幅提高成绩。

# 3 Python实现

下面只实现了一些常用的融合方法，其他的类推。

## 3.1 Stacking

```
'''5折stacking'''
n_folds = 5
skf = list(StratifiedKFold(y, n_folds))
for j, clf in enumerate(clfs):
    '''依次训练各个单模型'''
    dataset_blend_test_j = np.zeros((X_predict.shape[0], len(skf)))
    for i, (train, test) in enumerate(skf):
        '''使用第i个部分作为预测，剩余的部分来训练模型，获得其预测的输出作为第i部分的新特征。'''
        X_train, y_train, X_test, y_test = X[train], y[train], X[test], y[test]
        clf.fit(X_train, y_train)
        y_submission = clf.predict_proba(X_test)[:, 1]
        dataset_blend_train[test, j] = y_submission
        dataset_blend_test_j[:, i] = clf.predict_proba(X_predict)[:, 1]
    '''对于测试集，直接用这k个模型的预测值均值作为新的特征。'''
    dataset_blend_test[:, j] = dataset_blend_test_j.mean(1)

'''融合使用的模型'''
clf = GradientBoostingClassifier(learning_rate=0.02, subsample=0.5, max_depth=6, n_estimators=30)
clf.fit(dataset_blend_train, y)
y_submission = clf.predict_proba(dataset_blend_test)[:, 1]1234567891011121314151617181920
```

完整代码见：[GitHub_ensemble_stacking](https://github.com/InsaneLife/MyPicture/blob/master/ensemble_stacking.py)

## 3.2 Blending

下面是一个两层的Blending的实现

```
'''切分训练数据集为d1,d2两部分'''
X_d1, X_d2, y_d1, y_d2 = train_test_split(X, y, test_size=0.5, random_state=2017)
dataset_blend_train = np.zeros((X_d2.shape[0], len(clfs)))
dataset_blend_test = np.zeros((X_predict.shape[0], len(clfs)))

for j, clf in enumerate(clfs):
    '''依次训练各个单模型'''
    # print(j, clf)
    '''使用第1个部分作为预测，第2部分来训练模型，获得其预测的输出作为第2部分的新特征。'''
    # X_train, y_train, X_test, y_test = X[train], y[train], X[test], y[test]
    clf.fit(X_train, y_train)
    y_submission = clf.predict_proba(X_test)[:, 1]
    dataset_blend_train[:, j] = y_submission
    '''对于测试集，直接用这k个模型的预测值作为新的特征。'''
    dataset_blend_test[:, j] = clf.predict_proba(X_predict)[:, 1]
    print("val auc Score: %f" % roc_auc_score(y_predict, dataset_blend_test[:, j]))

'''融合使用的模型'''
# clf = LogisticRegression()
clf = GradientBoostingClassifier(learning_rate=0.02, subsample=0.5, max_depth=6, n_estimators=30)
clf.fit(dataset_blend_train, y_test)
y_submission = clf.predict_proba(dataset_blend_test)[:, 1]12345678910111213141516171819202122
```

完整代码见：[GitHub_ensemble_blending](https://github.com/InsaneLife/MyPicture/blob/master/ensemble_blending.py)

# Reference

1. [HUMAN ENSEMBLE LEARNING](http://link.zhihu.com/?target=https%3A//mlwave.com/human-ensemble-learning/)
2. <https://mlwave.com/kaggle-ensembling-guide/>
3. <https://dnc1994.com/2016/04/rank-10-percent-in-first-kaggle-competition/>
4. <https://zhuanlan.zhihu.com/p/25836678>



# REF

- [shine19930820](https://blog.csdn.net/shine19930820/article/details/75209021)
