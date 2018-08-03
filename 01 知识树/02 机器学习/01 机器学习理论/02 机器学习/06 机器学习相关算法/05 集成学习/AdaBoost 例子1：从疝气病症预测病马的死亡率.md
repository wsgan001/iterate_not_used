---
author: evo
comments: true
date: 2018-05-12 07:32:40+00:00
layout: post
link: http://106.15.37.116/2018/05/12/adaboost-sample1/
slug: adaboost-sample1
title:
wordpress_id: 5564
categories:
- 随想与反思
tags:
- '@want_to_know'
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1.


[第7章 集成方法 ensemble method](http://ml.apachecn.org/mlia/ensemble-random-tree-adaboost/#_3)







# TODO






  * **还是需要仔细看下的，有很多细节不清楚**

  * **具体的一些问题下面都有写**




# MOTIVE






  * aaa





* * *





# 项目要求


疝气病是描述马胃肠痛的术语，现在我们准备预测患有疝气病的马的存活问题。也可以构建一个 Web 网站，让驯马师输入马的症状然后预测马是否会死去


# 项目数据


链接：https://pan.baidu.com/s/1_poe5mYSPGENdMHaEyAkfg 密码：rlci

病马的训练数据已经给出来了，如下形式存储在文本文件中:


    1.000000    1.000000    39.200000   88.000000   20.000000   0.000000    0.000000    4.000000    1.000000    3.000000    4.000000    2.000000    0.000000    0.000000    0.000000    4.000000    2.000000    50.000000   85.000000   2.000000    2.000000    0.000000
    2.000000    1.000000    38.300000   40.000000   24.000000   1.000000    1.000000    3.000000    1.000000    3.000000    3.000000    1.000000    0.000000    0.000000    0.000000    1.000000    1.000000    33.000000   6.700000    0.000000    0.000000    1.000000


这里的数据包括368个样本和28个特征，该数据还存在一个问题，数据集中有30%的值是缺失的。

注意：类别标签是+1和-1，而非1和0




# 完整代码


我们这里用的弱分类器是单层决策树（decision stump, 也称决策树桩）。


    import numpy as np


​
    # 加载数据集
    def load_data_set(file_name):
        num_in_row = len(open(file_name).readline().strip().split('\t'))
        data_arr = []
        label_arr = []

        fr = open(file_name)
        for line in fr.readlines():
            line_arr = []
            cur_line = line.strip().split('\t')
            for i in range(num_in_row - 1):
                line_arr.append(float(cur_line[i]))
            data_arr.append(line_arr)
            label_arr.append(float(cur_line[-1]))
        return data_arr, label_arr


​
    # 将数据集，按照feature列的value进行 二分法切分比较来赋值分类
    def stump_classify(data_mat, column_index, thresh_value, inequal_flag):
        row_num, column_num = np.shape(data_mat)
        label_pred = np.ones((row_num, 1))  # 先初始化为1
        if inequal_flag == 'lt':  # lower than
            # 将split_result里面的小于这个值的都设定为-1 为什么？
            # 得到了根据这个 value 划分的样本的结果，小于这个值的样本都为-1，大于这个值的样本都为1
            label_pred[data_mat[:, column_index] <= thresh_value] = -1.0
        else:
            label_pred[data_mat[:, column_index] > thresh_value] = -1.0
        return label_pred


​
    # weights_mat 每个样本的权重值
    # 建立决策树桩，这个比较重要
    def build_tree_stump(data_mat, label_mat, weights_mat):
        row_num, column_num = np.shape(data_mat)
        num_steps = 10.0  # 这个是什么？
        min_error = np.inf  # 错误率  无穷大
        best_stump = {}  # 最优的决策树桩
        best_label_pred = np.mat(np.zeros((row_num, 1)))  # 预测的最优结果

        for column_index in range(column_num):
            # 找到这个特征的最小值和最大值
            min_feature = data_mat[:, column_index].min()
            max_feature = data_mat[:, column_index].max()
            step_size = (max_feature - min_feature) / num_steps  # 分成很多等份
            for j in range(-1, int(num_steps) + 1):
                for inequal_flag in ['lt', 'gt']:
                    thresh_value = (min_feature + float(j) * step_size)
                    label_pred = stump_classify(data_mat, column_index, thresh_value, inequal_flag)  # 进行根据这个特征的这个值进行预测
                    # 预测正确的为0，预测错误的为1
                    error_arr = np.mat(np.zeros((row_num, 1)))

                    error_arr[label_pred != label_mat] = 1
                    # 得到权重错误值，即 整体结果的错误率
                    # 为什么这个地方可以这么计算？
                    weights_error_value = weights_mat.T * error_arr
                    if weights_error_value < min_error:
                        min_error = weights_error_value
                        best_label_pred = label_pred.copy()
                        best_stump['column_index'] = column_index
                        best_stump['thresh_value'] = thresh_value
                        best_stump['inequal_flag'] = inequal_flag
        # best_stump 表示分类器的结果，在第几个列上，用大于／小于比较，阈值是多少 (单个弱分类器)
        return best_stump, min_error, best_label_pred


​
    # 进行训练
    # classifier_num_limit: 分类器的个数极限，中间有可能因为error_rate已经是0了，就不用这么多
    def adaboost_train(data_mat, label_mat, classifier_num_limit=40):
        weak_classifier_arr = []  # 弱分类器的集合
        row_num = np.shape(data_mat)[0]
        weights_mat = np.mat(np.ones((row_num, 1)) / row_num)  # 每个样本的权重值
        predict_mat = np.mat(np.zeros((row_num, 1)))  # 预测的分类结果值
        for i in range(classifier_num_limit):
            # 建立一个决策树桩
            tree_stump, error, label_pred = build_tree_stump(data_mat, label_mat, weights_mat)
            # 根据这个分类器的错误率来计算它的权重值 这个权重与树的个数没有关系吗？
            alpha = float(0.5 * np.log((1.0 - error) / max(error, 1e-16)))
            tree_stump['alpha'] = alpha
            # 添加到我的弱分类器集合里面
            weak_classifier_arr.append(tree_stump)
            # 如果某个样本分类正确，那么结果就是负数，如果某个样本分类错误，那么结果就是正数
            expon = np.multiply(-1 * alpha * label_mat, label_pred)
            # 之前为正数的，也就是分类错误的，这次权重变大；之前分类正确的，这次权重变小 确认一下公式？
            weights_mat = np.multiply(weights_mat, np.exp(expon))
            # OK，重新归一化样本的权重
            weights_mat = weights_mat / weights_mat.sum()

            # 更新整体的predict_mat，将本次的预测结果按权重加进来
            predict_mat += alpha * label_pred
            # 每次都会看看这时候的error_rate怎么样了
            # sign 判断正为 1， 0为 0， 负为 -1。因为是 !=,那么结果就是0 正, 1 负
            # 为什么这个地方要乘以1？ 乘以1 不是还是1吗？
            # error_flag_mat 是一列值，其中预测正确的就是0，预测错误的就是1，
            error_flag_mat = np.multiply(np.sign(predict_mat) != label_mat, np.ones((row_num, 1)))
            error_rate = error_flag_mat.sum() / row_num
            print('current error_rate: ', error_rate)
            if error_rate == 0.0:
                break
        # 之所以返回predict_mat 是因为创建了40个树桩之后，error_rate 还没到0，也就是说还有分错的
        return weak_classifier_arr, predict_mat, error_rate


​
    # 打印ROC曲线，并计算AUC的面积大小
    # 这个没有怎么看 要弄清楚？
    def plot_roc(predict_mat, label_mat):
        import matplotlib.pyplot as plt
        # variable to calculate AUC
        y_sum = 0.0

        num_positive = np.sum(np.array(label_mat) == 1.0)  # 对正样本的进行求和
        y_step = 1 / float(num_positive)  # 正样本的权重
        x_step = 1 / float(len(label_mat) - num_positive)  # 负样本的权重
        # 这个地方如果没有tanspose的话返回的都是0
        sorted_pred_index = predict_mat.transpose().argsort()  # 返回的是数组值从小到大的索引值
        # 测试结果是否是从小到大排列
        # 可以选择打印看一下
        # 开始创建模版对象
        fig = plt.figure()
        fig.clf()
        ax = plt.subplot(111)
        # cursor光标值
        cur = (1.0, 1.0)
        # loop through all the values, drawing a line segment at each point
        for index in sorted_pred_index.tolist()[0]:
            if label_mat[index] == 1.0:
                del_x = 0
                del_y = y_step
            else:
                del_x = x_step
                del_y = 0
                y_sum += cur[1]
            # draw line from cur to (cur[0]-delX, cur[1]-delY)
            # 画点连线 (x1, x2, y1, y2)
            # print cur[0], cur[0]-delX, cur[1], cur[1]-delY
            ax.plot([cur[0], cur[0] - del_x], [cur[1], cur[1] - del_y], c='b')
            cur = (cur[0] - del_x, cur[1] - del_y)
        # 画对角的虚线线
        ax.plot([0, 1], [0, 1], 'b--')
        plt.xlabel('False positive rate')
        plt.ylabel('True positive rate')
        plt.title('ROC curve for AdaBoost horse colic detection system')
        # 设置画图的范围区间 (x1, x2, y1, y2)
        ax.axis([0, 1, 0, 1])
        plt.show()
        '''
        参考说明：http://blog.csdn.net/wenyusuran/article/details/39056013
        为了计算 AUC ，我们需要对多个小矩形的面积进行累加。
        这些小矩形的宽度是x_step，因此可以先对所有矩形的高度进行累加，最后再乘以x_step得到其总面积。
        所有高度的和(y_sum)随着x轴的每次移动而渐次增加。
        '''
        print("the Area Under the Curve is: ", y_sum * x_step)


​
    # 使用adaboost的弱分类器进行分类
    def adaboost_classify(data_mat, weak_class_arr):
        row_num = np.shape(data_mat)[0]
        pred_mat = np.mat(np.zeros((row_num, 1)))
        for i in range(len(weak_class_arr)):
            label_pred = stump_classify(
                data_mat,
                weak_class_arr[i]['column_index'],
                weak_class_arr[i]['thresh_value'],
                weak_class_arr[i]['inequal_flag']
            )
            pred_mat += weak_class_arr[i]['alpha'] * label_pred
        return np.sign(pred_mat)  # 统一到 -1，0，1 上


​
    if __name__ == '__main__':
        data_arr, label_arr = load_data_set('horseColicTraining.txt')
        data_mat = np.mat(data_arr)
        label_mat = np.mat(label_arr).T
        print(data_mat.shape, label_mat.shape)
        # 进行训练
        weak_class_arr, predict_mat, error_rate = adaboost_train(data_mat, label_mat, 20)
        print("训练得到的弱分类器集合为：\n", weak_class_arr)
        print("此时的分类结果为： \n", predict_mat.T)
        print("此时的训练集错误率为： ", error_rate)

        # 打印ROC曲线
        plot_roc(predict_mat, label_mat)

        # 准备测试集上的数据
        data_arr_test, label_arr_test = load_data_set("horseColicTest.txt")
        data_mat_test = np.mat(data_arr_test)
        label_mat_test = np.mat(label_arr_test).T
        # 在测试集上进行测试
        pred_mat_test = adaboost_classify(data_mat_test, weak_class_arr)
        num_test_row = np.shape(data_mat_test)[0]
        error_arr = np.mat(np.ones((num_test_row, 1)))
        # 测试：计算总样本数，错误样本数，错误率
        print("训练集样本总数：", num_test_row,
              "分类错误的样本个数：", error_arr[pred_mat_test != label_mat_test].sum(),
              "分类错误率：", error_arr[pred_mat_test != label_mat_test].sum() / num_test_row)


输出如下：


    (299, 21) (299, 1)
    current error_rate:  0.284280936455
    current error_rate:  0.284280936455
    current error_rate:  0.247491638796
    current error_rate:  0.247491638796
    current error_rate:  0.254180602007
    current error_rate:  0.240802675585
    current error_rate:  0.240802675585
    current error_rate:  0.220735785953
    current error_rate:  0.247491638796
    current error_rate:  0.230769230769
    current error_rate:  0.240802675585
    current error_rate:  0.214046822742
    current error_rate:  0.227424749164
    current error_rate:  0.217391304348
    current error_rate:  0.220735785953
    current error_rate:  0.217391304348
    current error_rate:  0.224080267559
    current error_rate:  0.224080267559
    current error_rate:  0.230769230769
    current error_rate:  0.224080267559
    训练得到的弱分类器集合为：
     [{'column_index': 9, 'thresh_value': 3.0, 'inequal_flag': 'gt', 'alpha': 0.4616623792657674}, {'column_index': 17, 'thresh_value': 52.5, 'inequal_flag': 'gt', 'alpha': 0.31248245042467104}, {'column_index': 3, 'thresh_value': 55.199999999999996, 'inequal_flag': 'gt', 'alpha': 0.2868097320169577}, {'column_index': 18, 'thresh_value': 62.300000000000004, 'inequal_flag': 'lt', 'alpha': 0.23297004638939506}, {'column_index': 10, 'thresh_value': 0.0, 'inequal_flag': 'lt', 'alpha': 0.19803846151213741}, {'column_index': 5, 'thresh_value': 2.0, 'inequal_flag': 'gt', 'alpha': 0.18847887349020634}, {'column_index': 12, 'thresh_value': 1.2, 'inequal_flag': 'lt', 'alpha': 0.15227368997476778}, {'column_index': 7, 'thresh_value': 1.2, 'inequal_flag': 'gt', 'alpha': 0.15510870821690512}, {'column_index': 5, 'thresh_value': 0.0, 'inequal_flag': 'lt', 'alpha': 0.13536197353359405}, {'column_index': 4, 'thresh_value': 28.799999999999997, 'inequal_flag': 'lt', 'alpha': 0.12521587326132078}, {'column_index': 11, 'thresh_value': 2.0, 'inequal_flag': 'gt', 'alpha': 0.1334764812820767}, {'column_index': 9, 'thresh_value': 4.0, 'inequal_flag': 'lt', 'alpha': 0.1418224325377107}, {'column_index': 14, 'thresh_value': 0.0, 'inequal_flag': 'gt', 'alpha': 0.10264268449708028}, {'column_index': 0, 'thresh_value': 1.0, 'inequal_flag': 'lt', 'alpha': 0.11883732872109484}, {'column_index': 4, 'thresh_value': 19.199999999999999, 'inequal_flag': 'gt', 'alpha': 0.09879216527106625}, {'column_index': 2, 'thresh_value': 36.719999999999999, 'inequal_flag': 'lt', 'alpha': 0.12029960885056867}, {'column_index': 3, 'thresh_value': 92.0, 'inequal_flag': 'lt', 'alpha': 0.10846927663989175}, {'column_index': 15, 'thresh_value': 0.0, 'inequal_flag': 'lt', 'alpha': 0.09652967982091411}, {'column_index': 3, 'thresh_value': 73.599999999999994, 'inequal_flag': 'gt', 'alpha': 0.08958515309272022}, {'column_index': 18, 'thresh_value': 8.9000000000000004, 'inequal_flag': 'lt', 'alpha': 0.09210361961272426}]
    此时的分类结果为：
     [[ -4.09543656e-01   5.26407952e-01   1.13744899e+00  -2.34841212e-01
       -6.32795606e-01   1.39898149e+00   1.14062312e+00   5.20947035e-01
       -8.68196335e-01   5.57328458e-01   2.46043468e-01   8.14261277e-01
        1.44517050e+00   4.83921235e-01   1.03493895e+00  -5.69973725e-01
       -1.51389734e+00   5.87806181e-01   3.26154873e-01  -6.09905446e-01
       -2.54988564e-02   1.74451275e+00   1.15020608e+00   1.11093631e+00
        4.36178477e-01   2.64618464e-01   2.37223290e-01   1.03061834e+00
        3.07116637e-01   2.10233871e+00  -9.57263969e-01   2.16938992e-01
        8.71551037e-01   6.36372290e-01  -6.05287574e-01  -2.02426117e+00
       -6.06781215e-02  -5.12647893e-02   1.64525074e+00   6.47120534e-02
       -1.80458582e+00   3.02386711e-01   3.84456114e-01  -7.89239263e-01
       -1.04028815e+00  -1.34997261e+00  -1.08026892e+00   1.64525074e+00
       -7.72197286e-01  -1.29015644e+00   1.32618120e+00   3.97416313e-01
        1.09443411e+00   5.87806181e-01  -7.36665882e-03   2.45597906e-02
        3.07116637e-01  -4.59157434e-01  -9.44833004e-01  -1.34997261e+00
        1.38788074e+00   1.37617866e+00  -5.69509877e-01  -1.26055298e-02
       -5.65649632e-03   6.09176987e-01   5.81310923e-01   1.75221379e+00
       -2.04050651e-02   1.64525074e+00  -6.66786746e-02   1.51453913e+00
       -2.59036096e-02  -5.49253311e-01   3.07116637e-01   1.75697583e-01
        1.48318134e+00   2.09781374e+00  -1.22283773e-01  -1.57254799e-01
       -9.26526856e-01   1.44139952e+00   2.37397120e-01  -1.59791745e+00
        1.04348288e-01   1.49936876e+00  -8.22226127e-01   5.44791294e-01
        1.11880866e+00   1.63639862e+00   7.98346934e-01  -7.36665882e-03
        1.37525604e+00   1.21060921e-01   1.01092299e+00   1.31679214e+00
        4.28315186e-01  -8.92537427e-01   1.57208798e+00  -4.16846086e-01
        1.68076567e+00  -9.31081985e-01   8.63363951e-01   9.24572283e-01
        7.57115896e-02   6.40534782e-01   4.83405214e-01   1.38788074e+00
       -9.65466417e-01  -4.39553756e-01  -4.14235282e-01   1.02176622e+00
        1.15812475e+00  -2.23487940e-01   5.70878446e-01   1.53762917e+00
        2.09531783e+00   4.85837247e-01   2.84442591e-01   1.02091750e+00
        1.15020608e+00  -1.23739005e+00   1.63187365e+00  -5.59575468e-01
        7.98085665e-01   1.38801313e+00  -1.12338332e-01   4.64688375e-01
       -3.99544463e-01   8.52263234e-01   5.47715854e-01   1.70005912e-01
        1.25460705e+00  -1.01614693e+00  -5.69973725e-01  -1.22283773e-01
        1.44517050e+00   1.41156119e+00   9.31419334e-01   1.03061834e+00
       -3.37543607e-01  -1.58764727e+00   1.51453913e+00  -1.22049954e-01
        7.05275756e-01   1.27526767e+00   8.62794994e-01  -2.34919386e-01
        1.82708980e+00   1.72538097e+00  -1.03317411e+00  -3.91617394e-01
        1.37525604e+00   1.88256313e+00   6.66693899e-01   1.44766641e+00
        6.15551894e-01   1.12808105e+00   1.39898149e+00  -4.32000883e-01
        3.50131524e-01   1.18342815e+00  -1.29317781e-01   6.49116277e-01
        1.01860388e-01   1.40757608e+00   1.31855521e+00  -1.04407383e-01
       -1.06617263e+00   1.13744899e+00  -8.79691415e-01  -4.02718140e-01
       -9.47967377e-01  -1.06193764e+00   3.07116637e-01   1.53128133e+00
       -1.70533037e+00  -2.97455869e-01  -4.63505980e-01  -1.07055714e+00
        7.73164502e-01  -1.59791745e+00   1.71610857e+00   1.76895599e+00
       -5.80627141e-01   6.93744676e-01   3.68270400e-01   8.25104500e-01
        5.56260884e-01   8.26634721e-01  -1.67703561e-03   1.79077045e+00
       -2.04927233e+00   5.68866464e-01   1.91844033e+00   2.43412010e-01
        1.60324293e-01   1.52419428e+00   6.21510769e-01   1.13495309e+00
       -5.41161444e-01   7.84770510e-01   1.94979812e+00  -2.89020148e-01
       -4.63505980e-01   3.78771858e-01   3.07116637e-01   8.32354150e-02
        1.75704761e+00   5.54309261e-02  -4.96926143e-01   2.53604555e-01
        1.02494830e+00   1.42902607e+00   1.71212346e+00   5.44791294e-01
       -6.45831577e-01   9.57146724e-01   3.19811790e-01  -5.02063270e-01
        7.13870352e-01  -5.81752404e-01   8.28286584e-01   1.59178332e+00
        1.75221379e+00  -1.09801185e+00   3.07116637e-01  -1.02238457e+00
        1.14260296e+00  -8.35722545e-01  -1.25694332e+00   5.47715854e-01
        2.37223290e-01   1.01092299e+00  -5.49253311e-01   1.44766641e+00
        1.37902862e+00   7.61746106e-02   1.83056421e-01  -1.51113919e+00
        1.09735867e+00  -9.08233387e-02   1.90249021e-01   1.54342545e-01
       -1.34997261e+00  -8.91386837e-01  -8.32058656e-01  -2.71375474e-01
        1.75673876e+00  -6.01705270e-01   5.24417001e-01  -2.95558428e-02
       -6.55639080e-01  -8.13118456e-01  -2.65705438e-01   1.48410395e+00
       -8.52137017e-01   2.13400536e+00  -3.63059144e-03   9.03765037e-01
        1.36066184e+00  -1.64983589e-01  -6.97370381e-01   5.60411008e-01
        8.11469109e-01   5.63829531e-01   5.59730850e-01   1.12347505e+00
        6.81518667e-01   6.09108300e-01  -1.83314124e-01  -6.05287574e-01
       -1.73769174e+00  -3.16549173e-01  -2.18384386e-01   8.56702922e-01
        1.29408339e+00   1.64525074e+00   3.86770635e-01   1.59394011e+00
       -4.19679093e-01  -4.14235282e-01  -2.59036096e-02   1.94979812e+00
        9.40919670e-01  -1.26391078e+00   8.54481576e-02   3.07116637e-01
        1.18969955e+00   1.51924056e+00  -7.72197286e-01  -8.64122848e-01
       -1.74877138e-01   2.29539807e+00  -1.71079026e-01  -6.98858558e-01
       -1.31106637e+00  -2.16039427e-01   7.27398123e-01]]
    此时的训练集错误率为：  0.224080267559
    the Area Under the Curve is:  0.8740830160646289
    训练集样本总数： 67 分类错误的样本个数： 15.0 分类错误率： 0.223880597015


对于这个程序本身的逻辑还是比较理解的，但是还是有些细节不清楚的：


## 好像错误率还是很高？为什么？


**看起来好像分类错误率还是很高的，不应该呀？是什么地方错了吗？而且我把弱分类器个数设为300的时候，错误率还是只能到16% 左右，基本不怎么降了。是什么地方不对吗？**


## alpha 的公式里面的参数哪里来的？


**需要补充下，这个图要重新整理到 AdaBoost 文章里去，然后在这里引用**

AdaBoost算法权重计算公式

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/bFmfIlcLAl.png?imageslim)





## plot_roc 这个函数没有怎么看


**要仔细看下**


## 在训练的时候 expon 的计算是根据什么公式？


**原理要附在这里。**


## 要尝试一下决策树作为弱分类器，这里面用的是决策树桩（1层的）


**待尝试**


## 要尝试一下别的分类器


**待尝试**


## 要看下有没有现成的adaboost可以使用？sklearn 里面好像是有的，使用它重新求解一下


**待补充**


## 之前Logistic 的例子也是做的同样的事情，要对比下这两种算法的时间和效果


**待补充**





* * *





# COMMENT
