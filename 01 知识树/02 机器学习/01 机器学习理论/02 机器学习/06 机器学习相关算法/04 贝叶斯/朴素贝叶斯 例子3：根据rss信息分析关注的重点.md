# 朴素贝叶斯 例子3：根据rss信息分析关注的重点



TODO

* **这个没有很理解到底是做什么的，要对照书看一下**
* **运行的时候报错说：ModuleNotFoundError: No module named 'rfc822' 。尚没有解决。**
* **而且这两个rss源好像也是没有entries这个属性的。这两个问题需要解决。**






# 项目场景


广告商往往想知道关于一个人的一些特定统计信息，以便能更好地定向推销广告。


# 项目要求


我们将分别从美国的两个城市中选取一些人，通过分析这些人发布的信息，来看看他们的关注点是不是都是相同的，如果是不同的，那么想了解下他们到底关注些什么。

我们会从两个 RSS 源收集内容，然后进行解析，来显示最常用的公共词。


# 项目数据


使用的是RSS数据，因此需要先安装 feedparse 库。


# 完整代码




    import numpy as np
    import feedparser
    import operator


    # 将文本内容切分成 word list
    def text_parse(content):
        import re
        list_of_words = re.split(r'\W*', content)
        return [tok.lower() for tok in list_of_words if len(tok) > 2]


    # 根据样本中的词汇创建词汇表
    def create_vocabulary_list(data_set):
        vocabulary_set = set([])  # create empty set
        for document in data_set:
            vocabulary_set = vocabulary_set | set(document)  # union of the two sets
        return list(vocabulary_set)


    # 获得前30个最常用到的words
    def calculate_most_freq_words(vocabulary_list, full_words):
        import operator
        freq_dict = {}
        for token in vocabulary_list:
            freq_dict[token] = full_words.count(token)
        sorted_freq = sorted(freq_dict.iteritems(),
                             key=operator.itemgetter(1),
                             reverse=True)
        return sorted_freq[:30]


    # 将文本转化成向量
    def convert_doc_to_vec(vocabulary_list, doc):
        doc_vec = [0] * len(vocabulary_list)
        for word in doc:
            if word in vocabulary_list:
                doc_vec[vocabulary_list.index(word)] += 1
        return doc_vec


    # 计算出概率值
    def calculate_probility(vec_data, label):
        doc_num = len(vec_data)
        word_num = len(vec_data[0])
        p_1 = sum(label) / float(doc_num)  # 正样本出现概率，sum(label)即其中所有的 1 的个数，
        vec_0 = np.ones(word_num)  # 构造单词出现次数列表 之所以不是zeros 是因为为了应对有生词出现的情况
        vec_1 = np.ones(word_num)
        word_num_0 = 2.0  # 整个数据集单词出现总数   为什么不是0？
        word_num_1 = 2.0

        for i in range(doc_num):
            if label[i] == 1:
                vec_1 += vec_data[i]  # 如果是正样本，对正样本的向量进行加和
                word_num_1 += sum(vec_data[i])  # 计算所有正样本中出现的单词总数   为什么要算这个？为了后面normalize吗？
            else:
                vec_0 += vec_data[i]
                word_num_0 += sum(vec_data[i])

        p_vec_0 = np.log(vec_0 / word_num_0)
        p_vec_1 = np.log(vec_1 / word_num_1)  # 即在正样本类别下，每个单词出现的概率   为什么使用log()？
        return p_vec_0, p_vec_1, p_1

    def classify_NB(test_vec, p_vec_not_abusive, p_vec_abusive, p_abusive):
        p1 = sum(test_vec * p_vec_abusive) + np.log(p_abusive)  # element-wise mult
        p0 = sum(test_vec * p_vec_not_abusive) + np.log(1.0 - p_abusive)
        if p1 > p0:
            return 1
        else:
            return 0


    def localWords(feed0, feed1):
        # 将文本提取出来
        doc_list = []
        class_list = []
        full_words = []
        min_len = min(len(feed1['entries']), len(feed0['entries']))
        for i in range(min_len):
            wordList = text_parse(feed1['entries'][i]['summary'])
            doc_list.append(wordList)
            full_words.extend(wordList)
            class_list.append(1)  # NY is class 1
            wordList = text_parse(feed0['entries'][i]['summary'])
            doc_list.append(wordList)
            full_words.extend(wordList)
            class_list.append(0)
        vocabulary_list = create_vocabulary_list(doc_list)  # create vocabulary
        top30_words = calculate_most_freq_words(vocabulary_list, full_words)  # remove top 30 words

        # 从字典中将这30个词移除
        for pair_w in top30_words:
            if pair_w[0] in vocabulary_list:
                vocabulary_list.remove(pair_w[0])
        # 创建出训练集和测试集
        train_set = list(range(2 * min_len))
        test_set = []  # create test set
        for i in range(20):
            rand_index = int(np.random.uniform(0, len(train_set)))
            test_set.append(train_set[rand_index])
            del (train_set[rand_index])
        # 获得训练集的数据向量和label
        train_mat = []
        train_label = []
        for docIndex in train_set:  # train the classifier (get probs) trainNB0
            train_mat.append(convert_doc_to_vec(vocabulary_list, doc_list[docIndex]))
            train_label.append(class_list[docIndex])
        # 计算后面要用到的概率
        p_vec_0, p_vec_1, p_1 = calculate_probility(np.array(train_mat), np.array(train_label))

        # 进行测试
        errorCount = 0
        for docIndex in test_set:  # classify the remaining items
            wordVector = convert_doc_to_vec(vocabulary_list, doc_list[docIndex])
            if classify_NB(np.array(wordVector), p_vec_0, p_vec_1, p_1) != class_list[docIndex]:
                errorCount += 1
        print('the error rate is: ', float(errorCount) / len(test_set))
        return vocabulary_list, p_vec_0, p_vec_1

    #最具表征性的词汇显示函数
    def getTopWords(ny, sf):
        vocabList, p_vec_0, p_vec_1 = localWords(ny, sf)
        topNY = []
        topSF = []
        for i in range(len(p_vec_0)):
            if p_vec_0[i] > -6.0:
                topSF.append((vocabList[i], p_vec_0[i]))
            if p_vec_1[i] > -6.0:
                topNY.append((vocabList[i], p_vec_1[i]))
        sortedSF = sorted(topSF,
                          key=lambda pair: pair[1],
                          reverse=True)
        print("SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**SF**")
        for item in sortedSF:
            print(item[0])
        sortedNY = sorted(topNY,
                          key=lambda pair: pair[1],
                          reverse=True)
        print("NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**NY**")
        for item in sortedNY:
            print(item[0])


    if __name__=="__main__":
        ny = feedparser.parse('http://newyork.craigslist.org/stp/index.rss')
        sf = feedparser.parse('http://sfbay.craigslist.org/stp/index.rss')
        #接下来，我们要分析一下数据，显示地域相关的用词
        getTopWords(ny, sf)


**运行的时候报错说：ModuleNotFoundError: No module named 'rfc822' 。尚没有解决。**

**而且这两个rss源好像也是没有entries这个属性的。这两个问题需要解决。**







# REF

1. [第4章 基于概率论的分类方法：朴素贝叶斯](http://ml.apachecn.org/mlia/naive-bayes/)
