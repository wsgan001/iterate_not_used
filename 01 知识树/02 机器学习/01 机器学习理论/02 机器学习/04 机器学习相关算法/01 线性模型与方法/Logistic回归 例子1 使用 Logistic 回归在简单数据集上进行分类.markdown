
# TODO

* **alpha的动态设定有什么需要注意的吗？**
* **这个例子虽然简单，但是还是要好好理解的。**
* **每个weights的变化过程的图要画出来**
* **为什么第二个随机梯度下降方法中对于rand_index的变动能够减少周期性波动？**




# 项目概述


在一个简单的数据集上，采用梯度下降法找到 Logistic 回归分类器在此数据集上的最佳回归系数。


# 项目数据


[download id="5485"]

数据格式如下：


    -0.017612   14.053064   0
    -1.395634   4.662541    1
    -0.752157   6.538620    0
    -1.322371   7.152853    0
    0.423363    11.054677   0


使用Logistic回归的时候，由于需要进行距离计算，因此要求数据类型为数值型。另外，结构化数据格式则最佳。


完整代码




​
    import numpy as np
    import matplotlib.pyplot as plt


​
    # 加载数据集
    def load_data_set(file_name='testSet.txt'):
        datas = []
        labels = []
        fr = open(file_name)
        for line in fr.readlines():
            lineArr = line.strip().split()
            # 为什么前面要加上一个1.0? 应该是为了把 b 作为 w 的一部分，嗯
            datas.append([1.0, float(lineArr[0]), float(lineArr[1])])
            labels.append(int(lineArr[2]))
        return datas, labels


​
    def sigmoid(inX):
        return 1.0 / (1 + np.exp(-inX))


​
    # 再仔细看一下这个梯度下降
    def gradient_descent(datas, labels, alpha=0.001, max_cycles=500):
        # 转化为矩阵[[1,1,2],[1,1,2]....]
        data_mat = np.mat(datas)
        # 转化为矩阵[[0,1,0,1,0,1.....]]，并转制[[0],[1],[0].....]
        label_mat = np.mat(labels).transpose()  # convert to NumPy matrix
        row_num, column_num = np.shape(data_mat)
        # 生成一个长度和特征数相同的矩阵 -> [[1],[1],[1]]
        weights = np.ones((column_num, 1))

        for k in range(max_cycles):  # heavy on matrix operations
            # alpha = 1 / (10.0 +k) + 0.0001   把 alpha设定为动态的好像还不如固定为 0.1效果好，为什么呢？
            h = sigmoid(data_mat * weights)  # m*3 的矩阵 * 3*1 的单位矩阵 ＝ m*1的矩阵
            error = (label_mat - h)  # vector subtraction
            weights = weights + alpha * data_mat.transpose() * error  # matrix mult
            if k % 10 == 0:
                print(np.average(error))
        return weights


​
    # 这是一个随机梯度下降的方法
    def stoc_gradient_descent0(datas, labels, alpha=0.01, num_iter=150):
        data_mat = np.mat(datas)
        label_mat = np.mat(labels).transpose()
        row_num, column_num = np.shape(data_mat)
        weights = np.ones((column_num, 1))  # initialize to all ones
        for j in range(num_iter):
            for i in range(row_num):
                h = sigmoid(sum(data_mat[i] * weights))
                error = float(label_mat[i] - h)
                weights = weights + alpha * error * data_mat[i].transpose()
                if j % 10 == 0 and i == 0:
                    print(error)
        return weights


​
    # 这也是一个随机梯度下降的方法，不过alpha是变动的
    def stoc_gradient_descent1(datas, labels, num_iter=150):
        data_mat = np.mat(datas)
        label_mat = np.mat(labels).transpose()
        row_num, column_num = np.shape(data_mat)
        weights = np.ones((column_num, 1))  # initialize to all ones
        print(weights)
        for j in range(num_iter):
            data_index = list(range(row_num))
            for i in range(row_num):
                alpha = 4 / (1.0 + j + i) + 0.0001  # apha decreases with iteration, does not
                rand_index = int(np.random.uniform(0, len(data_index)))  # go to 0 because of the constant
                h = sigmoid(sum(data_mat[rand_index] * weights))
                error = float(label_mat[rand_index] - h)
                weights = weights + alpha * error * (data_mat[rand_index]).transpose()
                del (data_index[rand_index])
                if j % 10 == 0 and i == 0:
                    print(error)
        return weights


​
    def plot_graph(weights):
        datas, labels = load_data_set()
        data_array = np.array(datas)
        row_num = np.shape(data_array)[0]  # 行数
        # 将正样本和负样本的xy值整理一下 画出散点
        xcord1 = []
        ycord1 = []
        xcord2 = []
        ycord2 = []
        for i in range(row_num):
            if int(labels[i]) == 1:
                xcord1.append(data_array[i, 1])
                ycord1.append(data_array[i, 2])
            else:
                xcord2.append(data_array[i, 1])
                ycord2.append(data_array[i, 2])
        fig = plt.figure()
        ax = fig.add_subplot(111)
        ax.scatter(xcord1, ycord1, s=30, c='red', marker='s')
        ax.scatter(xcord2, ycord2, s=30, c='green')
        # 画出直线
        x = np.arange(-3.0, 3.0, 0.1)
        y = (-weights[0] - weights[1] * x) / weights[2]
        ax.plot(x, y)
        plt.xlabel('X1')
        plt.ylabel('X2')
        plt.show()


​
    if __name__ == "__main__":
        datas, labels = load_data_set()

        # 这三种方法都可以尝试下，对比下
        weights = gradient_descent(datas, labels, alpha=0.01, max_cycles=1000)
        # weights = stoc_gradient_descent0(datas, labels, alpha=0.001,num_iter=150)
        # weights=stoc_gradient_descent1(datas,labels,num_iter=150)
        print(weights)
        print(type(weights))
        # weights = [weights[0, 0], weights[1, 0], weights[2, 0]]
        w = np.mat(weights).transpose()
        weights = w.tolist()[0]
        print(w)
        print(weights)
        print(type(weights))
        plot_graph(weights)


输出：


    -0.364142533131
    0.388933945962
    ....中间略去
    0.000875800828433
    0.000869776249191
    0.00086380449892
    [[ 13.29983296]
     [  1.15405391]
     [ -1.80922946]]
    <class 'numpy.matrixlib.defmatrix.matrix'>
    [[ 13.29983296   1.15405391  -1.80922946]]
    [13.29983296444853, 1.1540539141963142, -1.8092294578016879]
    <class 'list'>


输出图像如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/AeAckL2EKj.png?imageslim)

上面的代码的一些疑问和总结：

**1.我将alpha设置为动态的时候，比如：alpha = 1 / (10.0 +k) + 0.0001 这个时候的效果好像还不如固定为 0.1效果好，为什么呢？alpha的动态设定有什么需要注意的吗？**

2.这个地方：datas.append([1.0, float(lineArr[0]), float(lineArr[1])]) 之所以加上一个1.0，应该是为了把 b 作为 w 的一部分，嗯。这样计算的时候，就可以统一计算了。

3.之前不知道


    [[ 13.29983296]
    [ 1.15405391]
    [ -1.80922946]]


这种形式的数据怎么转换成


    [13.29983296444853, 1.1540539141963142, -1.8092294578016879]


直接：np.mat(weights).transpose()[0] 这样是不行的，这个时候的 np.mat(weights).transpose() 还是 matrix 类型的，好像是不支持[0]的，因此先转化成 list 。**确认下matrix是不是不支持[0]的。**

4.关于梯度下降和随机梯度下降

梯度下降算法在每次更新回归系数时都需要遍历整个数据集，该方法在处理 100 个左右的数据集时尚可，但如果有数十亿样本和成千上万的特征，那么该方法的计算复杂度就太高了。

一种改进方法是一次仅用一个样本点来更新回归系数，该方法称为 随机梯度下降算法。由于可以在新样本到来时对分类器进行增量式更新，因而 **随机梯度上升算法是一个在线学习算法。**与 “在线学习” 相对应，一次处理所有数据被称作是 “批处理”。

下图展示了 stoc_gradient_descent0 算法在 200 次迭代过程中回归系数的变化情况。其中的系数2，也就是 X2 很快就稳定了，但系数 1 和 0 则需要更多次的迭代。如下图所示：**这个图哪里来的？随机梯度下降很容易震荡吗？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/EAeDCBbk6B.png?imageslim)

因此 stoc_gradient_descent1 做出了改进：

第一处改进为 alpha 的值。alpha 在每次迭代的时候都会调整，这回缓解上面波动图的数据波动或者高频波动。另外，虽然 alpha 会随着迭代次数不断减少，但永远不会减小到 0，因为我们在计算公式中添加了一个常数项。

第二处修改为 rand_index 更新，这里通过随机选取样本来更新回归系数。这种方法将减少周期性的波动。这种方法每次随机从列表中选出一个值，然后从列表中删掉该值（再进行下一次迭代）。**？为什么能够减少周期性波动？**

OK，这里我们把上面的 weight 的变化的曲线画一下：

```python
from numpy import *
import matplotlib
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle

from Ch05 import simple_logistic


​
def stoc_gradient_descent0(data_mat, label_mat):
    m, n = shape(data_mat)
    alpha = 0.5
    weights = ones(n)  # initialize to all ones
    weights_history = zeros((500 * m, n))
    for j in range(500):
        for i in range(m):
            h = simple_logistic.sigmoid(sum(data_mat[i] * weights))
            error = label_mat[i] - h
            weights = weights + alpha * error * data_mat[i]
            weights_history[j * m + i, :] = weights
    return weights_history


​
def stoc_gradient_descent1(data_mat, label_mat):
    m, n = shape(data_mat)
    alpha = 0.4
    weights = ones(n)  # initialize to all ones
    weights_history = zeros((40 * m, n))
    for j in range(40):
        dataIndex = list(range(m))
        for i in range(m):
            alpha = 4 / (1.0 + j + i) + 0.01
            randIndex = int(random.uniform(0, len(dataIndex)))
            h = simple_logistic.sigmoid(sum(data_mat[randIndex] * weights))
            error = label_mat[randIndex] - h
            # print error
            weights = weights + alpha * error * data_mat[randIndex]
            weights_history[j * m + i, :] = weights
            del (dataIndex[randIndex])
    print(weights)
    return weights_history


​
def plot_graph(data_arr, my_hist):
    n = shape(data_arr)[0]  # number of points to create
    xcord1 = []
    ycord1 = []
    xcord2 = []
    ycord2 = []
    markers = []
    colors = []

    fig = plt.figure()
    ax = fig.add_subplot(311)
    type1 = ax.plot(my_hist[:, 0])
    plt.ylabel('X0')
    ax = fig.add_subplot(312)
    type1 = ax.plot(my_hist[:, 1])
    plt.ylabel('X1')
    ax = fig.add_subplot(313)
    type1 = ax.plot(my_hist[:, 2])
    plt.xlabel('iteration')
    plt.ylabel('X2')
    plt.show()


​
if __name__ == "__main__":
    data_mat, label_mat = simple_logistic.load_data_set("../testSet.txt")
    data_arr = array(data_mat)
    my_hist = stoc_gradient_descent1(data_arr, label_mat)
    plot_graph(data_arr, my_hist)
```

输出：


```
[ 12.42965515   1.31174961  -1.95749652]
```

图像如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/0KJcAIf0Fj.png?imageslim)








* * *





# COMMENT
