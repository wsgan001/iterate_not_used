# TODO
* **还是要再补充一下的，包括分析的过程**
* **图像要重新看下怎么画，将 12，23，13 三个图都画出来**



# 项目概述


海伦使用约会网站寻找约会对象。经过一段时间之后，她发现曾交往过三种类型的人:

  * 不喜欢的人
  * 魅力一般的人
  * 极具魅力的人


而且，她已经收集到了一些之前约会过的人的数据信息：
  * 每年获得的飞行常客里程数
  * 玩视频游戏所耗时间百分比
  * 每周消费的冰淇淋公升数


这时，她希望：在认识一个新人的时候，可以根据这个人的这三项数据信息快速知道他是属于哪一类人。


# 使用的数据


链接：https://pan.baidu.com/s/1MJOyE-shGCcie5KpD-6f0Q 密码：s4ew


#





# 场景解析


这个是看一个人是属于三类中的哪一类，因此是分类问题。OK

**实际上这个地方还是要看书上，为什么要使用knn来做？还是有一些疑问的。**










# 整体代码




    import numpy as np
    import operator   # 这个operator 是什么？为什么要使用这个operator？
    import matplotlib
    import matplotlib.pyplot as plt


​
    # 从文件中提取数据，并分成features和label
    def get_data(file_name):
        fr = open(file_name, 'r')
        number_of_lines = len(fr.readlines())

        data_features = np.zeros((number_of_lines, 3))  # 矩阵 用来存放训练的数据
        data_label = []  # 用来存放训练的标签
        fr = open(file_name, 'r')
        index = 0
        for line in fr.readlines():
            line = line.strip()
            list_from_line = line.split('\t')
            data_features[index, :] = list_from_line[0:3]
            data_label.append(int(list_from_line[-1]))
            index += 1
        return data_features, data_label


​
    # 对获得的特征数据进行归一化
    def norm_data(data_features):
        min_vals = data_features.min(0)  # .min(0)是求出每一列的最小值，并组成一个数组
        max_vals = data_features.max(0)
        ranges = max_vals - min_vals

        norm_data_set = np.zeros(np.shape(data_features))
        m = data_features.shape[0]
        norm_data_set = data_features - np.tile(min_vals, (m, 1))  # np.tile 是类似把 min_vals 看成一个整体，然后生成类似m行1列的。
        norm_data_set = norm_data_set / np.tile(ranges, (m, 1))  # 可见使用矩阵的话，这种除法还是很方便的。
        # 为什么 ranges 和 min_vals还要返回出去？这两个在预测的时候会用到，对预测的数据进行归一化
        return norm_data_set, ranges, min_vals


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
​
​
    #将前两个特征画出来 需要修改，没有达到效果
    def plt_data(data_features,data_label):
        fig = plt.figure()
        ax = fig.add_subplot(111)
        ax.scatter(data_features[:, 1],
                   data_features[:, 2],
                   15.0*np.array(data_label),
                   15.0*np.array(data_label))
        plt.show()


​
    #从数据中抽取10% 的数据作为测试集，将剩余的数据作为训练集，计算测试集在训练集上的knn效果
    #说实话，不知道得到这样的效果有什么用？
    def try_dating_knn(norm_features, data_labels, hold_out_ratio=0.1, k=3):
        row_num = norm_features.shape[0]  # 行数
        test_number = int(row_num * hold_out_ratio)  # 取出这么多作为测试
        error_count = 0.0
        for i in range(test_number):
            voted_label = knn_classify(norm_features[i, :],  # 取第i行
                                       norm_features[test_number:row_num, :],  # 取从某一点开始的后面所有行
                                       data_labels[test_number:row_num],  # 取从某一点开始的后面所有的标签
                                       k)  # k值 为什么说 k 值要取奇数？
            print("the classifier came back with: %d, the real answer is: %d" % (voted_label, data_labels[i]))
            # 统计 knn方法预测错误的标签个数
            if (voted_label != data_labels[i]):
                error_count += 1.0
        # 打印出错误率，以及在测试个数为test_num的情况下，错误的个数
        print("the total error rate is: %f" % (error_count / float(test_number)))
        print(error_count)


​
    #percentage of time spent playing video games ?
    #frequent filer miles earned per year?
    #liters of ice cream consumed per year?
    def classify_person(feature1, feature2, feature3):
        person = np.array([feature1, feature2, feature3])
        data_features, data_labels = get_data('datingTestSet2.txt')  # load data setfrom file
        norm_features, ranges, min_vals = norm_data(data_features)
        voted_label = knn_classify((person-min_vals)/ranges,
                                        norm_features,
                                        data_labels,
                                        k=3)
        result_list = ['not at all', 'in small doses', 'in large doses']
        return result_list[voted_label - 1]


​
​
    if __name__ == "__main__":
        data_features, data_labels = get_data('datingTestSet2.txt')  # load data setfrom file
        norm_features, ranges, min_vals = norm_data(data_features)
        # 将数据画出来
        plt_data(data_features,data_labels)
        #将一部分数据作为测试集，一部分数据作为训练集，看看 knn 效果怎么样
        try_dating_knn(norm_features,
                       data_labels,
                       hold_out_ratio=0.10,
                       k=5)
        #将整体数据作为训练集，给一个新人的数据，看看分出来的结果
        res=classify_person(42666,	12,	0.3)
        print ("You will probably like this person: ", res)



画出的图形如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/FfmAjmEG42.png?imageslim)

输出的结果如下：


    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 3
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 1, the real answer is: 1
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 3, the real answer is: 3
    the classifier came back with: 2, the real answer is: 2
    the classifier came back with: 2, the real answer is: 1
    the classifier came back with: 1, the real answer is: 1
    the total error rate is: 0.050000
    5.0
    You will probably like this person:  in large doses











# REF

1. [第2章 k-近邻算法](http://ml.apachecn.org/mlia/knn/)
