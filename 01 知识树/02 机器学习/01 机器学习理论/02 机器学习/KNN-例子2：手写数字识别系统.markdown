---
author: evo
comments: true
date: 2018-05-07 13:55:27+00:00
layout: post
link: http://106.15.37.116/2018/05/07/knn-sample2/
slug: knn-sample2
title:
wordpress_id: 5400
categories:
- 随想与反思
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






    1. [第2章 k-近邻算法](http://ml.apachecn.org/mlia/knn/)

  2. 《机器学习实战》




# TODO






  * aaa




# MOTIVE






  * aaa





* * *





# 项目要求


构造一个能识别数字 0 到 9 的基于 KNN 分类器的手写数字识别系统。

要识别的数字是存储在文本文件中的具有相同的色彩和大小：宽高是 32 像素 * 32 像素的黑白图像。


# 项目数据


[download id="5402"]

其中：




  * 目录 trainingDigits 中包含了大约 2000 个样本，也即每个数字大约有 200 个样本；

  * 目录 testDigits 中包含了大约 900 个样本。


每个样本都是以txt文档的形式存放的，类似如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/AG80GlFkGE.png?imageslim)




# 整体代码如下




```python
import numpy as np
import operator
from os import listdir


​
# 读取一张照片作为一个 1行 1024列的向量
def get_img_data(filename):
    img_data = np.zeros((1, 1024))
    fr = open(filename)
    for i in range(32):
        line_str = fr.readline()
        for j in range(32):
            img_data[0, 32 * i + j] = int(line_str[j])  # 因为是11101110 这样的，所以可以这样分
    return img_data


​
# 开始进行分类
def knn_classify(inX, data_set, labels, k):
    # 求出这个inX这个样本于data_set中的样本之间的距离
    row_num = data_set.shape[0]  # 行数
    diff_mat = np.tile(inX, (row_num, 1)) - data_set  # 差值
    sq_diff_mat = diff_mat ** 2  # 平方
    sq_distances = sq_diff_mat.sum(axis=1)  # 每一行的差值平方之和
    distances = sq_distances ** 0.5  # 开方

    # 排序
    sorted_dist_indicies = distances.argsort()  # 返回的是数组里面从小到大的索引值
    class_count = {}  # 字典用来存放每个类别的投票个数
    # 只看距离最近的k个样本的标签
    for i in range(k):
        vote_label = labels[sorted_dist_indicies[i]]
        class_count[vote_label] = class_count.get(vote_label, 0) + 1
    # 投票结束 开始排序
    sorted_label_count = sorted(class_count.items(),  # 没有怎么明白？
                                key=operator.itemgetter(1),
                                reverse=True)
    # 返回投票个数最多的标签
    return sorted_label_count[0][0]


​
#用测试集看看训练集的knn效果
def try_hand_writing_knn():
    train_labels = []
    # 获取所有的训练数据地址
    training_file_name_list = listdir('digits/trainingDigits')  # load the training set
    num_train = len(training_file_name_list)
    train_data = np.zeros((num_train, 1024))
    for i in range(num_train):
        file_full_name = training_file_name_list[i]
        # 获取训练数据
        train_data[i, :] = get_img_data('digits/trainingDigits/%s' % file_full_name)
        # 获取训练标签
        file_name = file_full_name.split('.')[0]
        file_label = int(file_name.split('_')[0])
        train_labels.append(file_label)

    test_file_list = listdir('digits/testDigits')  # iterate through the test set
    error_count = 0.0
    test_num = len(test_file_list)
    for i in range(test_num):
        file_full_name = test_file_list[i]
        # 获得这张图片的数据
        file_data = get_img_data('digits/testDigits/%s' % file_full_name)
        # 获取这张图片的标签
        file_name = file_full_name.split('.')[0]  # 去掉后缀.txt
        file_label = int(file_name.split('_')[0])
        # 进行分类
        voted_label = knn_classify(file_data,
                                   train_data,
                                   train_labels,
                                   k=3)
        # print ("the classifier came back with: %d, the real answer is: %d" % (voted_label, file_label))
        if (voted_label != file_label):
            error_count += 1.0
    return error_count, test_num


​
if __name__ == "__main__":
    error_count, num_test = try_hand_writing_knn()
    print("the total number of errors is: %d" % error_count)
    print("the total error rate is: %f" % (error_count / float(num_test)))
```

这个运算还是需要一会的，结果如下：


```
the total number of errors is: 10
the total error rate is: 0.010571
```


可见，knn的聚类还是很厉害的，错误率很低。**不知道对于复杂的任务来说什么效果？**
