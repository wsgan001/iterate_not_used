---
author: evo
comments: true
date: 2018-05-10 13:12:55+00:00
layout: post
link: http://106.15.37.116/2018/05/10/svm-sample1/
slug: svm-sample1
title:
wordpress_id: 5513
categories:
- 随想与反思
tags:
- '@todo'
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1.


[第6章 支持向量机](http://ml.apachecn.org/mlia/svm/)







# TODO






  * **SMO的过程之前看的课程里面没有，这个地方的算法用的就是SMO的算法，很多地方没有明白？**

  * **而且没有仔细看**

  * **对照《机器学习实战》书看一下，仔细理解一下。**




# MOTIVE






  * aaa





* * *





# 项目要求


对小规模数据点进行分类


# 项目数据


[download id="5517"]

数据格式：


    3.542485    1.977398    -1
    3.018896    2.556416    -1
    7.551510    -1.580030   1
    2.114999    -0.004466   -1
    8.127113    1.274372    1




# 完整代码


在看完整代码之前，我们先看一下 testRBF.txt 文档中的数据：


    from numpy import *
    import matplotlib
    import matplotlib.pyplot as plt


​
    def generate_data(file_name):
        fw = open(file_name, 'w')
        xcord0 = [];
        ycord0 = [];
        xcord1 = [];
        ycord1 = []
        for i in range(100):
            [x, y] = random.uniform(0, 1, 2)
            xpt = x * cos(2.0 * pi * y);
            ypt = x * sin(2.0 * pi * y)
            if (x > 0.5):
                xcord0.append(xpt);
                ycord0.append(ypt)
                label = -1.0
            else:
                xcord1.append(xpt);
                ycord1.append(ypt)
                label = 1.0
            fw.write('%f\t%f\t%f\n' % (xpt, ypt, label))
        fw.close()


​
    def load_data_set(file_name):
        xcord0 = []
        ycord0 = []
        xcord1 = []
        ycord1 = []

        datas = []
        labels = []
        fr = open(file_name)
        for line in fr.readlines():
            line_arr = line.strip().split('\t')
            datas.append([float(line_arr[0]), float(line_arr[1])])
            labels.append(float(line_arr[2]))
        fr.close()
        for i in range(len(datas)):
            if labels[i] == -1:
                xcord0.append(datas[i][0])
                ycord0.append(datas[i][1])
            else:
                xcord1.append(datas[i][0])
                ycord1.append(datas[i][1])
        return xcord0, ycord0, xcord1, ycord1


​
    def plot_graph(xcord0, ycord0, xcord1, ycord1):
        fig = plt.figure()
        ax = fig.add_subplot(111)

        ax.scatter(xcord0, ycord0, marker='s', s=90)
        ax.scatter(xcord1, ycord1, marker='o', s=50, c='red')
        plt.title('Non-linearly Separable Data for Kernel Method')
        plt.show()


​
    if __name__ == "__main__":
        # generate_data("testSetRBF.txt")
        xcord0, ycord0, xcord1, ycord1 = load_data_set("testRBF.txt")
        plot_graph(xcord0, ycord0, xcord1, ycord1)


输出图像：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Idhc43mf57.png?imageslim)

OK，下面看完整的代码：


    import numpy as np


​
    # 加载数据集
    def load_data_set(file_name):
        datas = []
        labels = []
        fr = open(file_name)
        for line in fr.readlines():
            line_arr = line.strip().split('\t')
            datas.append([float(line_arr[0]), float(line_arr[1])])
            labels.append(float(line_arr[2]))
        return datas, labels


​
    def clip_alpha(aj, H, L):
        if aj > H:
            aj = H
        if L > aj:
            aj = L
        return aj


​
    def select_j_rand(i, m):
        j = i  # we want to select any J not equal to i
        while (j == i):
            j = int(np.random.uniform(0, m))
        return j


​
    # C 松弛变量(常量值)，允许有些数据点可以处于分隔面的错误一侧。
    # 控制最大化间隔和保证大部分的函数间隔小于1.0这两个目标的权重。
    # 可以通过调节该参数达到不同的结果。
    # toler  容错率（是指在某个体系中能减小一些因素或选择对某个系统产生不稳定的概率。）
    def smo_simple(datas, labels, C, toler, max_iter):
        data_mat = np.mat(datas)
        label_mat = np.mat(labels).transpose()
        row_num, column_num = np.shape(data_mat)
        alphas = np.mat(np.zeros((row_num, 1)))  # 拉格朗日乘子  有点类似权重值
        b = 0  # 模型的常量值

        iter = 0
        while (iter < max_iter):
            alpha_pairs_changed = 0  # 记录alpha是否已经进行优化，每次循环时设为0，然后再对整个集合顺序遍历
            for i in range(row_num):
                # 我们预测的类别 y[i] = w^Tx[i]+b; 其中因为 w = Σ(1~n) a[n]*lable[n]*x[n]
                fXi = float(np.multiply(alphas, label_mat).T * (data_mat * data_mat[i, :].T)) + b
                # 预测结果与真实结果比对，计算误差Ei
                Ei = fXi - float(label_mat[i])
                # 0<=alphas[i]<=C，但由于0和C是边界值，我们无法进行优化，因为需要增加一个alphas和降低一个alphas。
                # 表示发生错误的概率：labelMat[i]*Ei 如果超出了 toler， 才需要优化。至于正负号，我们考虑绝对值就对了。
                # 检验样本(xi, yi)是否满足KKT条件
                if ((label_mat[i] * Ei < -toler) and (alphas[i] < C)) \
                        or ((label_mat[i] * Ei > toler) and (alphas[i] > 0)):
                    # 如果满足优化的条件，我们就随机选取非i的一个点，进行优化比较
                    j = select_j_rand(i, row_num)
                    # 预测j的结果
                    fXj = float(np.multiply(alphas, label_mat).T * (data_mat * data_mat[j, :].T)) + b
                    Ej = fXj - float(label_mat[j])
                    alpha_iold = alphas[i].copy()
                    alpha_jold = alphas[j].copy()
                    # L 和 H 用于将 alphas[j] 调整到 0-C 之间。如果 L==H ，就不做任何改变，直接执行continue语句
                    # labelMat[i]!= labelMat[j] 表示异侧，就相减，否则是同侧，就相加。
                    if (label_mat[i] != label_mat[j]):
                        L = max(0, alphas[j] - alphas[i])
                        H = min(C, C + alphas[j] - alphas[i])
                    else:
                        L = max(0, alphas[j] + alphas[i] - C)
                        H = min(C, alphas[j] + alphas[i])
                    # 如果相同，就没法优化了
                    if L == H:
                        print("L==H")
                        continue
                    # eta 是 alphas[j] 的最优修改量，如果 eta==0 ，需要退出for循环的当前迭代过程
                    # 参考《统计学习方法》李航-P125~P128<序列最小最优化算法>
                    eta = 2.0 * data_mat[i, :] * data_mat[j, :].T \
                          - data_mat[i, :] * data_mat[i, :].T \
                          - data_mat[j, :] * data_mat[j, :].T
                    if eta >= 0:
                        print("eta>=0")
                        continue
                    # 计算出一个新的alphas[j]值
                    alphas[j] -= label_mat[j] * (Ei - Ej) / eta
                    # 并使用辅助函数，以及L和H对其进行调整
                    alphas[j] = clip_alpha(alphas[j], H, L)
                    # 检查alpha[j]是否只是轻微的改变，如果是的话，就退出for循环。
                    if (abs(alphas[j] - alpha_jold) < 0.00001):
                        print("j not moving enough")
                        continue
                    # 然后alphas[i]和alphas[j]同样进行改变，虽然改变的大小一样，但是改变的方向正好相反
                    alphas[i] += label_mat[j] * label_mat[i] * (alpha_jold - alphas[j])
                    # 在对alpha[i], alpha[j] 进行优化之后，给这两个alpha值设置一个常数b。
                    # w= Σ[1~n] ai*yi*xi => b = yj- Σ[1~n] ai*yi(xi*xj)
                    # 所以：  b1 - b = (y1-y) - Σ[1~n] yi*(a1-a)*(xi*x1)
                    # 为什么减 2 遍？ 因为是 减去 Σ[1~n]，正好2个变量i和j，所以减2遍
                    b1 = b - Ei - label_mat[i] * (alphas[i] - alpha_iold) * data_mat[i, :] * data_mat[i, :].T \
                         - label_mat[j] * (alphas[j] - alpha_jold) * data_mat[i, :] * data_mat[j, :].T
                    b2 = b - Ej - label_mat[i] * (alphas[i] - alpha_iold) * data_mat[i, :] * data_mat[j, :].T \
                         - label_mat[j] * (alphas[j] - alpha_jold) * data_mat[j, :] * data_mat[j, :].T
                    if (0 < alphas[i]) and (C > alphas[i]):
                        b = b1
                    elif (0 < alphas[j]) and (C > alphas[j]):
                        b = b2
                    else:
                        b = (b1 + b2) / 2.0
                    alpha_pairs_changed += 1
                    print("iter: %d i:%d, pairs changed %d" % (iter, i, alpha_pairs_changed))
            # 在for循环外，检查alpha值是否做了更新，如果在更新则将iter设为0后继续运行程序
            # 知道更新完毕后，iter次循环无变化，才推出循环。
            if (alpha_pairs_changed == 0):
                iter += 1
            else:
                iter = 0
            print("iteration number: %d" % iter)
        return b, alphas


​
    class opt_struct:
        def __init__(self, data_mat_in, class_labels, C, toler, kTup):  # Initialize the structure with the parameters
            self.X = data_mat_in
            self.labelMat = class_labels
            self.C = C
            self.tol = toler
            self.m = np.shape(data_mat_in)[0]
            self.alphas = np.mat(np.zeros((self.m, 1)))
            self.b = 0
            self.eCache = np.mat(np.zeros((self.m, 2)))  # first column is valid flag
            self.K = np.mat(np.zeros((self.m, self.m)))
            for i in range(self.m):
                self.K[:, i] = kernel_trans(self.X, self.X[i, :], kTup)


​
    # data_mat 数据集
    # row_mat data_mat数据集的第i行的数据
    # kTup  核函数的信息
    def kernel_trans(data_mat, row_mat, kTup):  # calc the kernel or transform data to a higher dimensional space
        row_num, column_num = np.shape(data_mat)
        K = np.mat(np.zeros((row_num, 1)))
        if kTup[0] == 'lin':
            K = data_mat * row_mat.T  # linear kernel
        elif kTup[0] == 'rbf':
            for j in range(row_num):
                delta_row = data_mat[j, :] - row_mat
                K[j] = delta_row * delta_row.T
            # 径向基函数的高斯版本
            K = np.exp(K / (-1 * kTup[1] ** 2))  # divide in NumPy is element-wise not matrix like Matlab
        else:
            raise NameError('Kernel is not recognized')
        return K


​
    def calc_Ek(oS, k):
        fXk = float(np.multiply(oS.alphas, oS.labelMat).T * oS.K[:, k] + oS.b)
        Ek = fXk - float(oS.labelMat[k])
        return Ek


​
    def selectJ(i, oS, Ei):  # this is the second choice -heurstic, and calcs Ej
        max_k = -1;
        max_delta_e = 0;
        Ej = 0
        oS.eCache[i] = [1, Ei]  # set valid #choose the alpha that gives the maximum delta E
        valid_ecache_list = np.nonzero(oS.eCache[:, 0].A)[0]
        if (len(valid_ecache_list)) > 1:
            for k in valid_ecache_list:  # loop through valid Ecache values and find the one that maximizes delta E
                if k == i: continue  # don't calc for i, waste of time
                Ek = calc_Ek(oS, k)
                delta_e = abs(Ei - Ek)
                if (delta_e > max_delta_e):
                    max_k = k;
                    max_delta_e = delta_e;
                    Ej = Ek
            return max_k, Ej
        else:  # in this case (first time around) we don't have any valid eCache values
            j = select_j_rand(i, oS.m)
            Ej = calc_Ek(oS, j)
        return j, Ej


​
    def update_Ek(oS, k):  # after any alpha has changed update the new value in the cache
        Ek = calc_Ek(oS, k)
        oS.eCache[k] = [1, Ek]


​
    def innerL(i, oS):
        Ei = calc_Ek(oS, i)
        if ((oS.labelMat[i] * Ei < -oS.tol) and (oS.alphas[i] < oS.C)) \
                or ((oS.labelMat[i] * Ei > oS.tol) and (oS.alphas[i] > 0)):
            j, Ej = selectJ(i, oS, Ei)  # this has been changed from selectJrand
            alpha_iold = oS.alphas[i].copy();
            alpha_jold = oS.alphas[j].copy();
            if (oS.labelMat[i] != oS.labelMat[j]):
                L = max(0, oS.alphas[j] - oS.alphas[i])
                H = min(oS.C, oS.C + oS.alphas[j] - oS.alphas[i])
            else:
                L = max(0, oS.alphas[j] + oS.alphas[i] - oS.C)
                H = min(oS.C, oS.alphas[j] + oS.alphas[i])
            if L == H:
                print("L==H")
                return 0
            eta = 2.0 * oS.K[i, j] - oS.K[i, i] - oS.K[j, j]  # changed for kernel
            if eta >= 0:
                print("eta>=0")
                return 0
            oS.alphas[j] -= oS.labelMat[j] * (Ei - Ej) / eta
            oS.alphas[j] = clip_alpha(oS.alphas[j], H, L)
            update_Ek(oS, j)  # added this for the Ecache
            if (abs(oS.alphas[j] - alpha_jold) < 0.00001):
                print("j not moving enough")
                return 0
            oS.alphas[i] += oS.labelMat[j] * oS.labelMat[i] * (
                alpha_jold - oS.alphas[j])  # update i by the same amount as j
            update_Ek(oS, i)  # added this for the Ecache                    #the update is in the oppostie direction
            b1 = oS.b - Ei - oS.labelMat[i] * (oS.alphas[i] - alpha_iold) * oS.K[i, i] \
                 - oS.labelMat[j] * (oS.alphas[j] - alpha_jold) * oS.K[i, j]
            b2 = oS.b - Ej - oS.labelMat[i] * (oS.alphas[i] - alpha_iold) * oS.K[i, j] \
                 - oS.labelMat[j] * (oS.alphas[j] - alpha_jold) * oS.K[j, j]
            if (0 < oS.alphas[i]) and (oS.C > oS.alphas[i]):
                oS.b = b1
            elif (0 < oS.alphas[j]) and (oS.C > oS.alphas[j]):
                oS.b = b2
            else:
                oS.b = (b1 + b2) / 2.0
            return 1
        else:
            return 0


​
    # 完整SMO算法外循环，与smoSimple有些类似，但这里的循环退出条件更多一些
    def smoP(data_mat_in, class_labels, C, toler, max_iter, kTup=('lin', 0)):  # full Platt SMO
        oS = opt_struct(np.mat(data_mat_in),
                        np.mat(class_labels).transpose(),
                        C, toler, kTup)
        iter = 0
        entire_set = True;
        alpha_pairs_changed = 0
        # 循环遍历：循环maxIter次 并且 （alphaPairsChanged存在可以改变 or 所有行遍历一遍）
        while (iter < max_iter) and ((alpha_pairs_changed > 0) or (entire_set)):
            alpha_pairs_changed = 0
            #  当 entireSet=true or 非边界alpha对没有了；就开始寻找 alpha对，然后决定是否要进行else。
            if entire_set:
                # 在数据集上遍历所有可能的alpha
                for i in range(oS.m):
                    # 是否存在alpha对，存在就+1
                    alpha_pairs_changed += innerL(i, oS)
                    print("fullSet, iter: %d i:%d, pairs changed %d" % (iter, i, alpha_pairs_changed))
                iter += 1
            # 对已存在 alpha对，选出非边界的alpha值，进行优化。
            else:
                # 遍历所有的非边界alpha值，也就是不在边界0或C上的值。
                non_bound_is = np.nonzero((oS.alphas.A > 0) * (oS.alphas.A < C))[0]
                for i in non_bound_is:
                    alpha_pairs_changed += innerL(i, oS)
                    print("non-bound, iter: %d i:%d, pairs changed %d" % (iter, i, alpha_pairs_changed))
                iter += 1
            # 如果找到alpha对，就优化非边界alpha值，否则，就重新进行寻找，如果寻找一遍 遍历所有的行还是没找到，就退出循环。
            if entire_set:
                entire_set = False  # toggle entire set loop
            elif (alpha_pairs_changed == 0):
                entire_set = True
            print("iteration number: %d" % iter)
        return oS.b, oS.alphas


​
    def try_RBF(k1=1.3):
        datas, labels = load_data_set('trainRBF.txt')
        b, alphas = smoP(datas, labels, 200, 0.0001, 10000, ('rbf', k1))  # C=200 important
        dat_mat = np.mat(datas);
        label_mat = np.mat(labels).transpose()
        sv_ind = np.nonzero(alphas.A > 0)[0]
        sVs = dat_mat[sv_ind]  # get matrix of only support vectors
        labelSV = label_mat[sv_ind];
        print("there are %d Support Vectors" % np.shape(sVs)[0])
        m, n = np.shape(dat_mat)
        error_count = 0
        for i in range(m):
            kernel_eval = kernel_trans(sVs, dat_mat[i, :], ('rbf', k1))
            predict = kernel_eval.T * np.multiply(labelSV, alphas[sv_ind]) + b
            if np.sign(predict) != np.sign(labels[i]):
                error_count += 1
        print("the training error rate is: %f" % (float(error_count) / m))
        datas, labels = load_data_set('testRBF.txt')
        error_count = 0
        dat_mat = np.mat(datas)
        label_mat = np.mat(labels).transpose()
        m, n = np.shape(dat_mat)
        for i in range(m):
            kernel_eval = kernel_trans(sVs, dat_mat[i, :], ('rbf', k1))
            predict = kernel_eval.T * np.multiply(labelSV, alphas[sv_ind]) + b
            if np.sign(predict) != np.sign(labels[i]):
                error_count += 1
        print("the test error rate is: %f" % (float(error_count) / m))


​
    if __name__ == "__main__":
        try_RBF()


输出：


    L==H
    fullSet, iter: 0 i:0, pairs changed 0
    fullSet, iter: 0 i:1, pairs changed 1
    fullSet, iter: 0 i:2, pairs changed 2
    fullSet, iter: 0 i:3, pairs changed 3
    ....略去
    fullSet, iter: 4 i:97, pairs changed 0
    fullSet, iter: 4 i:98, pairs changed 0
    fullSet, iter: 4 i:99, pairs changed 0
    iteration number: 5
    there are 30 Support Vectors
    the training error rate is: 0.130000
    the test error rate is: 0.150000


**SMO的过程之前看的课程里面没有，这个地方的算法用的就是SMO的算法，很多地方没有明白？而且没有仔细看。**

**看过SMO算法之后这个地方再重新修正一下。**













* * *





# COMMENT
