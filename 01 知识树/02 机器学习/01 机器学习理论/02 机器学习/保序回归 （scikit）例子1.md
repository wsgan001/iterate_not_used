




# REF

1. [scikit学习心得——Isotonic Regression](https://blog.csdn.net/qq_14905099/article/details/49908089)




# TODO

* **保序回归是什么？**
* **没怎么看**




这个应该是 scikit 例子中的源代码




# 完整代码



```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.collections import LineCollection #加载画图所需要的函数

from sklearn.linear_model import LinearRegression#加载线性拟合函数
from sklearn.isotonic import IsotonicRegression #加载保序拟合函数
from sklearn.utils import check_random_state #加载随机生成函数

n = 100
x = np.arange(n)#生成一个0到99的一维矩阵
rs = check_random_state(0)#设置随机种子
y = rs.randint(-50, 50, size=(n,)) + 50. * np.log(1 + np.arange(n))#生成一个随机数加上对数函数的一维矩阵

###############################################################################
# Fit IsotonicRegression and LinearRegression models

ir = IsotonicRegression()#生成保序函数的对象

y_ = ir.fit_transform(x, y)#生成保序的y

lr = LinearRegression()#生成线性拟合对象
lr.fit(x[:, np.newaxis], y)  # x needs to be 2d for LinearRegression x需要二维的，生成拟合成功的模型

###############################################################################
# plot result

segments = [[[i, y[i]], [i, y_[i]]] for i in range(n)] #画连线时使用
lc = LineCollection(segments, zorder=0)
lc.set_array(np.ones(len(y)))
lc.set_linewidths(0.5 * np.ones(n))

fig = plt.figure()
plt.plot(x, y, 'r.', markersize=12)
plt.plot(x, y_, 'g.-', markersize=12)
plt.plot(x, lr.predict(x[:, np.newaxis]), 'b-')#根据x和线性拟合拟合出的模型画出图
plt.gca().add_collection(lc)#增加连线
plt.legend(('Data', 'Isotonic Fit', 'Linear Fit'), loc='lower right')#标签
plt.title('Isotonic regression')
plt.show()
```


输出：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180713/6HJ7Jf0glJ.png?imageslim)
