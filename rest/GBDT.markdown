# GBDT



## GBDT介绍




### GBDT是怎么来的？


在 AdaBoost 发表后不久，Breiman 等人发表了 Formulate AdaBoost as gradient descent with a special loss function。随后 Friedman 等人发表了 Generalize AdaBoost to Gradient Boosting in order to handle a variety of loss functions。可以说 AdaBoost 是 Gradient Boosting 的一个特例或者 Gradient Boosting 是对 AdaBoost 进行推广。


### GBDT 算法到底是什么？

Gradient Boosting 和其它 Boosting 算法一样，通过将表现一般的数个模型（通常是深度固定的决策树）组合在一起来集成一个表现较好的模型。抽象地说，模型的训练过程是对一任意可导目标函数的优化过程。通过反复地选择一个指向负梯度方向的函数，该算法可被看做在函数空间里对目标函数进行优化。因此可以说 Gradient Boosting = Gradient Descent + Boosting。


### GBDT 与 AdaBoost 有什么区别？

和 AdaBoost 一样，Gradient Boosting 也是重复选择一个表现一般的模型并且每次基于先前模型的表现进行调整。不同的是，AdaBoost 是通过提升错分数据点的权重来定位模型的不足而 Gradient Boosting 是通过算梯度（gradient）来定位模型的不足。因此相比 AdaBoost, Gradient Boosting 可以使用更多种类的目标函数。

Gradient boosting 算法


### 提升树


* GBRT（gradient boosted regression tree）
* GBDT（gradient boosted decision tree）
* LambdaMART
* etc  （**还有什么吗？**）


### 树集成模型：


模型：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mh50CbghLj.png?imageslim)

每一个回归书，就是把样本映射到一个score，使用加性模型

参数：

每棵树的结构，每个非叶节点对应一个feature，一个split value

目标：

学习出函数的集合，每个函数实际构成又是一个单一的树。

假设我们拥有k个树：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Kl68i9FL5L.png?imageslim)

目标函数：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/edmelk0B91.png?imageslim)

如何涉及复杂度函数？损失函数？


#### CART树回顾：






  * 信息增益->用gini指数作为损失函数


  * 树的剪枝操作->用节点数目作为正则函数 （什么是正则函数？）


  * 最大深度->树空间的约束


  * 回归树叶节点score L2 Norm->正则函数




#### 模型可以应用到回归，分类等多种问题


尽管我们使用回归树作为基函数，只需要通过涉及合适的目标函数，可以应用到分类问题

回归问题：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CDJj91011i.png?imageslim)

二分类问题：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ie96Aa49E3.png?imageslim)


#### 如何实现函数的学习？


我们寻找的对象是一棵棵的回归树，不再是向量空间，因此无法使用SGD算法。（SGD是什么算法？）

采用加性提升训练方法：Additive Training

从最简单起点开始，一步步增加


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/170DE3Djf9.png?imageslim)

通过最优化目标函数，来确定选择什么样的\(f_t(x)\)假如到模型中


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CbiHcgbh6C.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/gAj2e0FBKb.png?imageslim)




#### 采用L2损失函数




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ich1ID7Eek.png?imageslim)

上图红圈部分是前一次迭代的残差

对目标函数进行Talay展开，保留两阶：

目标函数：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lfldH7fIGA.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fjBgJjiAHg.png?imageslim)

其中


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fh2HjIcCec.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/IibAKg5Gm0.png?imageslim)

如采用L2损失函数：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/a910HKEHi3.png?imageslim)

去掉常数项目，观察新的目标函数


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/I76LLHHLD7.png?imageslim)

很明显\(g_i\)，\(h_i\)都可以计算，已知的

接下来仔细考察\(f_t(x)\)，即一棵独立的回归树


#### 考察\(f_t(x)\)：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/5KmCHieEem.png?imageslim)






  * 每个叶节点输出标量，所有叶子节点输出构成T维向量W


  * q维样本到叶节点的映射




#### 指定一个复杂度函数




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ff886c429J.png?imageslim)

注意这不是唯一的方法？



对某一个\(f_t(x)\)，遍历所有叶节点\(I_j=\{i|q(x_i)=j\}\)，首先定义叶节点训练样本集合


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/d4CbL04LDf.png?imageslim)

T个变量的二次函数

树的评价计算：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/k61aACGE1I.png?imageslim)

知道如何评价树，如何搜索出最佳树？

  * 枚举所有树结构
    * 计算树的评价函数![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/cb2EEImDgJ.png?imageslim)
  * 选择最优树，计算对应的W![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ICLFGHK1aE.png?imageslim)


**但是，树结构太多了。**

不是搜索所有树结构，而是从哪个根节点开始，一层层长出一颗最佳树


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/eHci3KDegL.png?imageslim)

注意不再计算gini指数




#### 如何发现最佳的分支点：


遍历所有特征维度

  * 在选中的特征维度上，按样本值排序，选中对应的split value
  * 计算目标函数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8gFlK5Jm4c.png?imageslim)

完整的算法：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/l6hHjGe16c.png?imageslim)

随机森林 vs AdaBoost vs Gradient boosting

随机 vs 权重 vs 梯度

梯度提升算法实现包，刷Kaggle数据的神器

https://github.com/dmlc/xgboost







## REF

1. 七月在线 机器学习
2. [A Gentle Introduction to Gradient ](http://www.chengli.io/tutorials/gradient_boosting.pdf)  **这个很好 需要看下**
3. [机器学习算法中GBDT与Adaboost的区别与联系是什么？](https://www.zhihu.com/question/54626685) **这个也需要看下**
