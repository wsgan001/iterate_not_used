---
author: evo
comments: true
date: 2018-05-10 01:46:24+00:00
layout: post
link: http://106.15.37.116/2018/05/10/logistic-sample2/
slug: logistic-sample2
title: ''
wordpress_id: 5499
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1. 


[第5章 Logistic回归](http://ml.apachecn.org/mlia/logistic-regress/)







# TODO






  * aaa




# MOTIVE






  * aaa





* * *





# 项目要求


使用 Logistic 回归来预测患有疝病的马的存活问题。

疝病是描述马胃肠痛的术语。然而，这种病不一定源自马的胃肠问题，其他问题也可能引发马疝病。


# 项目数据


[download id="5502"]

病马的训练数据已经给出来了，如下形式存储在文本文件中:


    1.000000    1.000000    39.200000   88.000000   20.000000   0.000000    0.000000    4.000000    1.000000    3.000000    4.000000    2.000000    0.000000    0.000000    0.000000    4.000000    2.000000    50.000000   85.000000   2.000000    2.000000    0.000000
    2.000000    1.000000    38.300000   40.000000   24.000000   1.000000    1.000000    3.000000    1.000000    3.000000    3.000000    1.000000    0.000000    0.000000    0.000000    1.000000    1.000000    33.000000   6.700000    0.000000    0.000000    1.000000





# 完整代码




    import matplotlib.pyplot as plt
    import numpy as np


​    
​    
    def get_data(file_name):
        fs = open(file_name);
        # 获取训练数据
        data = []
        labels = []
        for line in fs.readlines():
            curr_line = line.strip().split('\t')
            line_arr = []
            for i in range(21):
                line_arr.append(float(curr_line[i]))
            data.append(line_arr)
            labels.append(float(curr_line[21]))
        return data,labels


​    
​    
​    
    def sigmoid(inX):
        return 1.0 / (1 + np.exp(-inX))


​    
    # 这也是一个随机梯度下降的方法，不过alpha是变动的
    def stoc_gradient_descent1(datas, labels, num_iter=150):
        data_mat = np.mat(datas)
        label_mat = np.mat(labels).transpose()
        row_num, column_num = np.shape(data_mat)
        weights = np.ones((column_num, 1))  # initialize to all ones
        for j in range(num_iter):
            data_index = list(range(row_num))
            for i in range(row_num):
                alpha = 4 / (1.0 + j*row_num + i) + 0.0001  # apha decreases with iteration, does not
                rand_index = int(np.random.uniform(0, len(data_index)))  # go to 0 because of the constant
                h = sigmoid(sum(data_mat[rand_index] * weights))
                error = float(label_mat[rand_index] - h)
                weights = weights + alpha * error * (data_mat[rand_index]).transpose()
                del (data_index[rand_index])
                if j % 10 == 0 and i == 0:
                    print(error)
        return weights
    
    def classify_vec(inX, weights):
        data = np.array(inX)
        prob = sigmoid(sum(data * weights))
        if prob > 0.5:
            return 1.0
        else:
            return 0.0


​    
    def try_colic():
        train_data, train_labels=get_data('horseColicTraining.txt')
        test_data, test_labels=get_data('horseColicTest.txt')
    
        # 进行训练
        weights = stoc_gradient_descent1(train_data, train_labels, 1000)
        print(weights)
        # 开始测试
        error_count = 0
        row_column=len(test_data)
        for i in range(row_column):
            if int(classify_vec(test_data[i], weights)) != int(test_labels[i]):
                error_count += 1
        error_rate = (float(error_count) / row_column)
        print("the error rate of this test is: %f" % error_rate)
        return error_rate


​    
    def multi_try_colic(num_tests = 10):
        error_sum = 0.0
        for k in range(num_tests):
            error_sum += try_colic()
        print("after %d iterations the average error rate is: %f" % (num_tests, error_sum / float(num_tests)))
        return error_sum
    
    if __name__=="__main__":
        multi_try_colic()


输出：


    .....略去
    after 10 iterations the average error rate is: 0.361194


**不明白，为什么这个错误率会这么高？不是应该会降低吗？而且，我尝试了下，增加num_iter 或者变更alpha，但是这个错误率仍然还是30% 为什么呢？**









* * *





# COMMENT



