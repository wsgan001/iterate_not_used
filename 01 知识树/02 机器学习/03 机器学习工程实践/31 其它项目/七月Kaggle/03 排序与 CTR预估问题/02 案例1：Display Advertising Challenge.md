# Display Advertising Challenge

Predict click-through rates on display ads

## 项目地址

https://www.kaggle.com/c/criteo-display-ad-challenge/

## 关于数据

- Label - Target variable that indicates if an ad was clicked (1) or not (0).
- I1-I13 - A total of 13 columns of integer features (mostly count features).
- C1-C26 - A total of 26 columns of categorical features. The values of these features have been hashed onto 32 bits for anonymization purposes.

比赛的时候这些数据特征的意义是不知道的，因为要脱敏，而且分类的特征也是不知道的。

我们准备用 LR 来做。

这个样本集解压后是 5个G 的文本文件，由于特征只有上面这么多，说明这个文件的条目还是很多的。

## 这时候单机无法训练，怎么办呢？

- 我把量级降到我可以训练，我可以做一个下采样。因为我数据里 0 的数量远大于 1 的数量，大概大了几倍。不被点击的次数要比被点击的次数要多很多的。这时候带来样本不均衡的问题，实际上工业界也会做下采样，对0 的样本的保留率低一些，然后与1 的尽量均衡一些，然后放到单机上跑。
-








这里面我们给了一个案例，他使用的是 pandas 来 load 数据的，但是实际上工业界的人不是这么做的，尽管 pandas 非常好用。因为命名只有200M 的文件，使用pandas load 之后内存占用可能会是上G，因为为了方便使用，pandas 对这个数据进行了一些处理，所以有很多额外的数据结构在。

那么实际的工业界会怎么做呢？他们会使用 LIBLINER 来做，这是台大开源的用于 LR 的一个库。大部分情况下，大家会把数据处理成 Libsvm 这种形式。

为什么这样好呢？因为我们的很多的category 需要one-hot 编码转化为数字，这时候，特征就会很多，维度可能会到几千维，也可能是几万维。但是大部分的值都是0，这时候，使用 Libsvm 来保存这个数据就很好，它的第一个位置是标签，后面是 index:value 对。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180722/mdjl8ALLB8.png?imageslim)

上面就是  libsvm 的数据格式，实际的公司里面在做训练的时候都会处理成这样的形式。因为它是省内存的。<span style="color:red;">但是不通过 pandas 把原始数据装载进来的话，怎么转换成 libsvm 格式？</span>

如果你已经整理成这个libsvm 的数据了，这时候你想跑 LR 的话，就直接用 liblinear ，它有一个可执行文件叫做 train

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180722/i0L22bH669.png?imageslim)

可见，使用起来还是很简单的。

真实的工业界如果用 LR 和 GBDT ，它实际上都是转成 libsvm 格式，然后用 liblinear 或者 xgboost 来跑。
