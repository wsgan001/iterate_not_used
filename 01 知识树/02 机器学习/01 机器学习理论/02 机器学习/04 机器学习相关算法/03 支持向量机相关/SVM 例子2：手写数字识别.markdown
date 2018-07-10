---
author: evo
comments: true
date: 2018-05-10 13:16:01+00:00
layout: post
link: http://106.15.37.116/2018/05/10/svm-sample2/
slug: svm-sample2
title: 
wordpress_id: 5514
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






    1. [第6章 支持向量机](http://ml.apachecn.org/mlia/svm/)

  2. 



# TODO






  * **同样也是SMO的，没有怎么明白，而且关于RBF的使用，理论还是要弄明白的。**

  * **对照《机器学习实战》书看一下，仔细理解一下。**




# MOTIVE






  * aaa





* * *





# 项目要求


之前我们使用 knn 的方法，可以根据已经有的一些digits数据来判断一个新的手写数字 digits 到底是多少，其实精度还是很高的，但是呢，有个缺点：knn需要保留所有的向量，因此它的模型占用的内存很大，如果给别人用的话，别人下载就不是很方便。

而如果用SVM来做这件事情，那么我们最后的模型只需要保存SVM的支持向量就行。


# 项目数据


仍然是之前的digits项目的数据 ： [download id="5402"]

里面每个txt文档都是32*32 的，内容类似：


    00000000000000001111000000000000
    00000000000000011111111000000000
    00000000000000011111111100000000
    00000000000000011111111110000000
    00000000000000011111111110000000
    00000000000000111111111100000000
    00000000000000111111111100000000
    00000000000001111111111100000000
    00000000000000111111111100000000
    00000000000000111111111100000000
    00000000000000111111111000000000
    00000000000001111111111000000000
    00000000000011111111111000000000
    00000000000111111111110000000000
    00000000001111111111111000000000
    00000001111111111111111000000000
    00000011111111111111110000000000
    00000111111111111111110000000000
    00000111111111111111110000000000
    00000001111111111111110000000000
    00000001111111011111110000000000
    00000000111100011111110000000000
    00000000000000011111110000000000
    00000000000000011111100000000000
    00000000000000111111110000000000
    00000000000000011111110000000000
    00000000000000011111110000000000
    00000000000000011111111000000000
    00000000000000011111111000000000
    00000000000000011111111000000000
    00000000000000000111111110000000
    00000000000000000111111100000000




# 完整代码




    import numpy as np
    from time import sleep


​    
    def calc_Ws(alphas, data_arr, class_labels):
        X = np.mat(data_arr);
        labelMat = np.mat(class_labels).transpose()
        m, n = np.shape(X)
        w = np.zeros((n, 1))
        for i in range(m):
            w += np.multiply(alphas[i] * labelMat[i], X[i, :].T)
        return w


​    
    def get_img_data(file_name):
        img_data = np.zeros((1, 1024))
        fr = open(file_name)
        for i in range(32):
            lineStr = fr.readline()
            for j in range(32):
                img_data[0, 32 * i + j] = int(lineStr[j])
        return img_data


​    
    def load_images(dir_name):
        from os import listdir
        file_list = listdir(dir_name)  # load the training set
        file_num = len(file_list)
        data_mats = np.zeros((file_num, 1024))
        labels = []
        for i in range(file_num):
            file_full_name = file_list[i]
            file_name = file_full_name.split('.')[0]  # take off .txt
            label = int(file_name.split('_')[0])
            if label == 9:
                labels.append(-1)
            else:
                labels.append(1)
            data_mats[i, :] = get_img_data('%s/%s' % (dir_name, file_full_name))
        return data_mats, labels


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
    def select_j_rand(i, m):
        j = i  # we want to select any J not equal to i
        while (j == i):
            j = int(np.random.uniform(0, m))
        return j


​    
    def clip_alpha(aj, H, L):
        if aj > H:
            aj = H
        if L > aj:
            aj = L
        return aj


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
    def try_digits(kTup=('rbf', 10)):
        data_arr, label_arr = load_images('digits/trainingDigits')
        b, alphas = smoP(data_arr, label_arr, 200, 0.0001, 10000, kTup)
        dat_mat = np.mat(data_arr);
        label_mat = np.mat(label_arr).transpose()
        sv_ind = np.nonzero(alphas.A > 0)[0]
        sVs = dat_mat[sv_ind]
        label_s_v = label_mat[sv_ind];
        print("there are %d Support Vectors" % np.shape(sVs)[0])
        m, n = np.shape(dat_mat)
        error_count = 0
        for i in range(m):
            kernel_eval = kernel_trans(sVs, dat_mat[i, :], kTup)
            predict = kernel_eval.T * np.multiply(label_s_v, alphas[sv_ind]) + b
            if np.sign(predict) != np.sign(label_arr[i]):
                error_count += 1
        print("the training error rate is: %f" % (float(error_count) / m))
        data_arr, label_arr = load_images('digits/testDigits')
        error_count = 0
        dat_mat = np.mat(data_arr)
        label_mat = np.mat(label_arr).transpose()
        m, n = np.shape(dat_mat)
        for i in range(m):
            kernel_eval = kernel_trans(sVs, dat_mat[i, :], kTup)
            # 1*m * m*1 = 1*1 单个预测结果
            predict = kernel_eval.T * np.multiply(label_s_v, alphas[sv_ind]) + b
            if np.sign(predict) != np.sign(label_arr[i]):
                error_count += 1
        print("the test error rate is: %f" % (float(error_count) / m))


​    
    if __name__ == "__main__":
        try_digits()


输出如下：


    L==H
    fullSet, iter: 0 i:0, pairs changed 0
    fullSet, iter: 0 i:1, pairs changed 1
    fullSet, iter: 0 i:2, pairs changed 2
    fullSet, iter: 0 i:3, pairs changed 3
    fullSet, iter: 0 i:4, pairs changed 4
    fullSet, iter: 0 i:5, pairs changed 5
    ...略去
    fullSet, iter: 3 i:396, pairs changed 0
    fullSet, iter: 3 i:397, pairs changed 0
    L==H
    fullSet, iter: 3 i:398, pairs changed 0
    fullSet, iter: 3 i:399, pairs changed 0
    fullSet, iter: 3 i:400, pairs changed 0
    L==H
    fullSet, iter: 3 i:401, pairs changed 0
    iteration number: 4
    there are 112 Support Vectors
    the training error rate is: 0.000000
    the test error rate is: 0.010753


**同样也是SMO的，没有怎么明白，而且关于RBF的使用，理论还是要弄明白的。**















* * *





# COMMENT



