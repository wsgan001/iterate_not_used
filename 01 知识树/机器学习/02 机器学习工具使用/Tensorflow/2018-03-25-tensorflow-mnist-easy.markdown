---
author: evo
comments: true
date: 2018-03-25 05:02:21+00:00
layout: post
link: http://106.15.37.116/2018/03/25/tensorflow-mnist-easy/
slug: tensorflow-mnist-easy
title: tensorflow MNIST 入门
wordpress_id: 818
categories:
- 机器学习相关工具
tags:
- '@want_to_know'
- Tensorflow
---

<!-- more -->


# 缘由：


看了下tensorflow手册，感觉对于MNIST这个例子讲的非常透彻，而且各种变化，因此对这个进行总结标记下。


# 要点：




## 1.一些说明


MNIST 是一个入门级的计算机视觉数据集，它包含各种手写数字图片：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab729bd3cf3c.png)


它也包含每一张图片对应的标签，告诉我们这个是数字几．比如，上面这四张图片的标签分别是 5,0,4,1

这次我们将训练一个机器学习模型用于预测图片里面的数字．主要是介绍下如何使用 TensorFlow 。

先从一个简单的数学模型开始，它叫做 Softmax Regression ．

MNIST 数据集的官网是 [Yann LeCun’s website](http://yann.lecun.com/exdb/mnist/) ．在这里，我们提供了一份 python 源
代码用于自动下载和安装这个数据集．你可以下载这段代码，然后用下面的代码导入到
你的项目里面，也可以直接复制粘贴到你的代码文件里面．

    
    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data
    
    # 如果没有数据包，这一行会从网上下载对应的数据包
    mnist = input_data.read_data_sets('MNIST_data/', one_hot=True)
    print(type(mnist))
    print(type(mnist.train))
    print(type(mnist.test))
    print(type(mnist.validation))


输出：

    
    Extracting MNIST_data/train-images-idx3-ubyte.gz
    Extracting MNIST_data/train-labels-idx1-ubyte.gz
    Extracting MNIST_data/t10k-images-idx3-ubyte.gz
    Extracting MNIST_data/t10k-labels-idx1-ubyte.gz
    <class 'tensorflow.contrib.learn.python.learn.datasets.base.Datasets'>
    <class 'tensorflow.contrib.learn.python.learn.datasets.mnist.DataSet'>
    <class 'tensorflow.contrib.learn.python.learn.datasets.mnist.DataSet'>
    <class 'tensorflow.contrib.learn.python.learn.datasets.mnist.DataSet'>


上面的代码会检查MNIST_data文件夹里面有没有需要的数据，如过没有，会自动将需要用到的数据下载到MNIST_data文件夹里面，然后读入到mnist里面，所以下载下来的包不用你手动解压。

下载下来的数据集可被分为三部分：



 	
  * 55000 行训练用点数据集（ mnist.train ）

 	
  * 10000行测试数据集 ( mnist.test )

 	
  * 5000 行验证数据集（ mnist.validation ）**为什么有了测试之后还要有验证呢？**


这样的切分很重要：在机器学习模型设计时必须有一个单独的测试数据集不用于训练而是用来评
估这个模型的性能，从而更加容易把设计的模型推广到其他数据集上（泛化）。

正如前面提到的一样，每一个 MNIST 数据单元有两部分组成：一张包含手写数字的图片和一个对应的标签．我们把这些图片设为“ xs ”，把这些标签设为“ ys ”．训练数据集和测试数据集都包含 xs 和 ys ，比如训练数据集的图片是 mnist.train.images ，训练数据集的标签是 mnist.train.labels ．

每一张图片包含 28×28 像素．我们可以用一个数字数组来表示这张图片：

![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab72c899beb3.png)

我们把这个数组展开成一个向量，长度是 28×28=784 ．如何展开这个数组（数字间的顺序）不重要，只要保持各个图片采用相同的方式展开．从这个角度来看， MNIST数据集的图片就是在 784 维向量空间里面的点 , 并且拥有比较复杂的结构 ( 注意 : 此类数据的可视化是计算密集型的 ) ．（**什么意思？什么是可视化是计算密集型的？**）

展平图片的数字数组会丢失图片的二维结构信息．这显然是不理想的，最优秀的计算机视觉方法会挖掘并利用这些结构信息，我们会在后续教程中介绍．但是在这个教程中我们忽略这些结构，所介绍的简单数学模型， softmax 回归 (softmax regression) ，不会利用这些结构信息．（**优秀的计算机视觉方法？CNN吗？ 还有别的吗？softmax回归到底是什么？**）

因此，在 MNIST 训练数据集中， mnist.train.images 是一个形状为 [55000, 784] 的张量，第一个维度数字用来索引图片，第二个维度数字用来索引每张图片中的像素点．在此张量里的每一个元素，都表示某张图片里的某个像素的强度值，值介于 0 和 1 之间．（**之所以是55000行784列，而不是反过来，是因为每一列都是对应一个特征，每一行都是一个样本**）


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab72d4b9b395.png)


相对应的 MNIST 数据集的标签是介于 0 到 9 的数字，用来描述给定图片里表示的数字．为了用于这个教程，我们使标签数据是 "one-hot vectors" ．**一个 one-hot 向量除了某一位的数字是 1 以外其余各维度数字都是 0** ．所以在此教程中，数字 n 将表示成一个只有在第 n 维度（从 0 开始）数字为 1 的 10 维向量．比如，标签 0 将表示成( [1,0,0,0,0,0,0,0,0,0,0] ) ．因此， mnist.train.labels 是一个[55000, 10] 的数字矩阵．（**原来这个就是one-hot，之前看到过，但是又忘记了，然后对这个一直有疑问，是的，labels是一个55000*10的矩阵**）


## 2.Softmax 回归 介绍


我们知道 MNIST 数据集的每一张图片都表示一个 (0 到 9 的 ) 数字．那么，**如果模型能看到一张图就能知道它属于各个数字的对应概率就好了。**比如，我们的模型可能看到一张数字 "9" 的图片，就判断出它是数字 "9" 的概率为 80% ，而有 5% 的概率属于数字 "8" （因为 8 和 9 都有上半部分的小圆），同时给予其他数字对应的小概率（因为该图像代表它们的可能性微乎其微）．

这是能够体现 softmax 回归自然简约的一个典型案例． **softmax 模型可以用来给不同的对象分配概率．**在后文，我们训练更加复杂的模型时，最后一步也往往需要用softmax 来分配概率．

softmax 回归（ softmax regression ）分两步：首先对输入被分类对象属于某个类的**“证据”相加求和**，然后将这个**“证据”的和转化为概率。**（**什么叫证据的和转化为概率？**）

我们使用加权的方法来累积计算一张图片是否属于某类的“证据”。如果图片的像素强有力的体现该图不属于某个类，则权重为负数，相反如果这个像素拥有有利的证据支持这张图片属于这个类，那么权值为正．

下面的图片显示了一个模型学习到的图片上每个像素对于特定数字类的权值．红色代表负权值，蓝色代表正权值．（**没想到这个都可以弄出来，怎么做到的？**）


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab7512eb4919.png)


我们也需要引入额外的“证据”，可称之为偏置量 (bias ）。总的来说，我们希望它代表了与所输入向无关的判断证据．因此对于给定的输入图片 x 代表某数字 i 的总体证据可以表示为：
[mathjax]
\[evidence_{i}=\sum_{j}^{ }W_{i,j}x_j+b_i\]

其中， \(W_i\)代表权重， \(b_i\)代表第 i 类的偏置量， j 代表给定图片 x 的像素索引用于像素求
和．然后用 softmax 函数可以把这些证据转换成概率 y:
\[y=softmax(evidence)\]

这里的 softmax 可以看成是一个激励（ activation ）函数或是链接（ link ）函数，把我们定
义的线性函数的输出转换成我们想要的格式，也就是关于 10 个数字类的概率分布．因
此，给定一张图片，它对于每一个数字的吻合度可以被 softmax 函数转换成为一个概率
值． softmax 函数可以定义为：（**为什么这个是这样的？exp(x)？**）
\[softmax(x)=normalize(exp(x))\]

展开等式右边的子式，可以得到：（**为什么展开会得到这个？**）
\[softmax(x)_i=\frac{exp(x_i)}{\sum_{j}^{ } exp(x_j)}\]

但是更多的时候把 softmax 模型函数定义为第一种形式：把输入值当成幂指数求值，再正则化这些结果值．这个幂运算表示，更大的证据对应更大的假设模型（ hypothesis ）里面的乘数权重值．反之，拥有更少的证据意味着在假设模型里面拥有更小的乘数系数．假设模型里的权值不可以是 0 值或者负值． Softmax 然后会正则化这些权重值，使它们的总和等于 1 ，以此构造一个有效的概率分布．（更多的关于 Softmax 函数的信息，可以参考 Michael Nieslen 的书里面的[这个部分](http://neuralnetworksanddeeplearning.com/chap3.html#softmax)，其中有关于softmax 的可交互式的可视化解释．）（**还是没明白这段？**）

对于 softmax 回归模型可以用下面的图解释，对于输入的 xs 加权求和，再分别加上一个
偏置量，最后再输入到 softmax 函数中：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab758afb3673.png)


如果把它写成一个方程，可以得到：（为什么得到的是这样的形式？\(W_{1,1}\)是什么？）


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab758e053cbc.png)


我们也可以用向量表示这个计算过程：用矩阵乘法和向量相加．这有助于提高计算效率（也是一种更有效的思考方式）．


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab758faaa532.png)


更进一步，可以写成更加紧凑的方式：
\[y=softmax(W_{k}+b)\]


## 3.回归模型的实现


为了在 python 中高效的进行数值计算，我们通常会调用（如 NumPy ）外部函数库，把类似矩阵乘法这样的复杂运算使用其他外部语言实现．不幸的是，从外部计算切换回Python 的每一个操作，仍然是一个很大的开销．如果你用 GPU 来进行外部计算，这样的开销会更大．用分布式的计算方式，也会花费更多的资源用来传输数据．

TensorFlow 也把复杂的计算放在 python 之外完成，但是为了避免前面说的那些开销，它做了进一步完善． TensorFlow 不单独地运行单一的复杂计算，而是让我们可以先用图描述一系列可交互的计算操作，然后全部一起在 Python 之外运行．（这样类似的运行方式，可以在不少的机器学习库中看到．）（**先用图描述一系列可交互的计算操作，然后全部一起放在python之外运行，底层是怎么实现的？**）

我们通过操作符号变量来描述这些可交互的操作单元，可以用下面的方式创建一个：


_x = tf.placeholder("float", [None, 784])_


x 不是一个特定的值，而是一个占位符 placeholder ，我们在 TensorFlow 运行计算时输入这个值．我们希望能够输入任意数量的 MNIST 图像，每一张图展平成 784 维的向量．我们用 2 维的浮点数张量来表示这些图，这个张量的形状是 [None ， 784] ．（**这里的 None 表示此张量的第一个维度可以是任何长度的**．**虽然 placeholder 的 shape 参数是可选的，但有了它， TensorFlow 能够自动捕捉因数据维度不一致导致的错误。**）

我们的模型也需要权重值和偏置量，当然我们可以把它们当做是另外的输入（使用占位符），但 TensorFlow 有一个更好的方法来表示它们： Variable ．**一个 Variable 代表一个可修改的张量，存在在 TensorFlow 的用于描述交互性操作的图中．它们可以用于计算输入值，也可以在计算中被修改．**对于各种机器学习应用，一般都会有模型参数，可以用 Variable 表示．（**一个Variable代表一个可修改的张量吗？**）


_W = tf.Variable(tf.zeros([784,10]))_
_b = tf.Variable(tf.zeros([10]))_


我们赋予 tf.Variable 不同的初值来创建不同的 Variable ：在这里，我们都用全为零的张量来初始化 W 和 b ．因为我们要学习 W 和 b 的值，它们的初值可以随意设置。

**注意， W 的维度是 [784，10] ，因为我们想要用 784 维的图片向量乘以它以得到一个10 维的证据值向量，每一位对应不同数字类。b 的形状是 [10] ，所以我们可以直接把它加到输出上面。是的。**

现在，可以实现我们的模型了，只需以下一行代码：


_ y = tf.nn.softmax(tf.matmul(x,W) + b)_


首先，我们用 tf.matmul(X，W) 表示 x 乘以 W ，对应之前等式里面的\(W_x\)，这里 x 是
一个 2 维张量拥有多个输入．然后再加上 b ，把和输入到 tf.nn.softmax 函数里面。

至此，我们先用了几行简短的代码来设置变量，然后只用了一行代码来定义我们的模型． TensorFlow 不仅仅可以使 softmax 回归模型计算变得特别简单，它也用这种非常灵活的方式来描述其他各种数值计算，从机器学习模型对物理学模拟仿真模型．一旦被定义好之后，我们的模型就可以在不同的设备上运行：计算机的 CPU ， GPU ，甚至是手机！

4.训练模型

为了训练我们的模型，我们首先需要定义一个指标来评估这个模型是好的．其实，在机器学习，我们通常定义指标来表示一个模型是坏的，这个指标称为成本（ cost ）或损失（ loss ），然后尽量最小化这个指标．但是，这两种方式是相同的。

一个非常常见的，非常漂亮的成本函数是“交叉熵” (cross-entropy) ．交叉熵产生于信息论里面的信息压缩编码技术，但是它后来演变成为从博弈论到机器学习等其他领域里的重要技术手段．它的定义如下：（**为什么是这样的？**）

\[
H_{y'}(y)=-\sum_{i}^{ } y_i'log(y_i)
\]
y 是我们预测的概率分布 ,y ′ 是实际的分布（我们输入的 one-hot vector) ．比较粗糙的理解是，交叉熵是用来衡量我们的预测用于描述真相的低效性．更详细的关于交叉熵的解释超出本教程的范畴，但是你很有必要好好理解它．（**怎么理解交叉熵？需要学习下**）

为了计算交叉熵，我们首先需要添加一个新的占位符用于输入正确值：


_y=tf.placeholder(tf.float32,[None,10])_


然后我们可以使用\(-\sum_{ }^{ }y'log(y)\)来计算交叉熵：（**为什么这么计算？**）


_cross_entropy=-tf.reduce_sum(y_*tf.log(y))_


首先，用 tf.log 计算 y 的每个元素的对数．接下来，我们把 y_ 的每一个元素和 tf.log(y_) 的对应元素相乘．最后，用 tf.reduce_sum 计算张量的所有元素的总和．

**注****意， tf.reduce_sum 把 minibatch 里的每张图片的交叉熵值都加起来了。我们计算的交叉熵是指整个 minibatch 的。**（**为什么是reduce_sum？与之前python里面的reduce又什么关联？**）

值得注意的是，这里的交叉熵不仅仅用来衡量单一的一对预测和真实值，而是所有100 幅图片的交叉熵的总和．对于 100 个数据点的预测表现比单一数据点的表现能更好地描述我们的模型的性能．

现在我们知道我们需要我们的模型做什么啦，用 TensorFlow 来训练它是非常容易的．因为 TensorFlow 拥有一张描述你各个计算单元的图，它可以自动地使用反向传播算法 (backpropagation algorithm) 来有效地确定你的变量是如何影响你想要最小化的那个成本值的．然后， TensorFlow 会用你选择的优化算法来不断地修改变量以降低成本．（**反向传播算法用代码是怎么实现的？**）


_train_step=tf.train.GradientDescentOptimizer(0.01).minimize(cross_entropy)_


在这里，我们要求 TensorFlow 用梯度下降算法（ gradient descent algorithm ）以 0.01的学习速率最小化交叉熵．梯度下降算法（ gradient descent algorithm ）是一个简单的学习过程， TensorFlow 只需将每个变量一点点地往使成本不断降低的方向移动．当然TensorFlow 也提供了其他许多优化算法：只要简单地调整一行代码就可以使用其他的算法．（**学习率是什么？0.01是大是小？**）

TensorFlow 在这里实际上所做的是，它会在后台给描述你的计算的那张图里面增加一系列新的计算操作单元用于实现反向传播算法和梯度下降算法．然后，它返回给你的只是一个单一的操作，当运行这个操作时，它用梯度下降算法训练你的模型，微调你的变量，不断减少成本．

现在，我们已经设置好了我们的模型．在运行计算之前，我们需要添加一个操作来
初始化我们创建的变量：


_init = tf.initialize_all_variables()_


现在我们可以在一个 Session 里面启动我们的模型，并且初始化变量：

_sess = tf.Session()_
_sess.run(init)_

然后开始训练模型，这里我们让模型循环训练 1000 次！

_ for i in range(1000):_
_    batch_xs, batch_ys = mnist.train.next_batch(100)_
_    sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})_

该循环的每个步骤中，我们都会随机抓取训练数据中的 100 个批处理数据点，然后
我们用这些数据点作为参数替换之前的占位符来运行 train_step ．(**嗯，不错，批量训练还是厉害的**）

使用一小部分的随机数据来进行训练被称为随机训练 (stochastic training)--- 在这里更确切的说是随机梯度下降训练．理想情况下，我们希望用我们所有的数据来进行每一步的训练，因为这能给我们更好的训练结果，但显然这需要很大的计算开销．所以，每一次训练我们可以使用不同的数据子集，这样做既可以减少计算开销，又可以最大化地学习到数据集的总体特性．


## 5.评估模型 Evaluating Our Model


那么我们的模型性能如何呢？

首先让我们找出那些预测正确的标签． tf.argmax() 是一个非常有用的函数，它能给你在一个张量里沿着某条轴的最高条目的索引值．比如， tf.argmax(y,1) 是模型认为每个输入最有可能对应的那些标签，而 tf.argmax(y_,1) 代表正确的标签．我们可以用 tf.equal来检测我们的预测是否真实标签匹配。（**厉害，原来tf.argmax是用在这个地方的，的确用的好**）

_correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))_

这行代码会给我们一组布尔值．为了确定正确预测项的比例，我们可以把布尔值转换成浮点数，然后取平均值．例如， [True, False, True, True] 会变成 [1,0,1,1] ，取平均值后得到 0.75 .

_accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))_

最后，我们计算所学习到的模型在测试数据集上面的正确率．

_print sess.run(accuracy, feed_dict={x: mnist.test.images, y_: mnist.test.labels})_

最终结果值应该大约是 91%

这个结果好吗？嗯，并不太好．事实上，这个结果是很差的．这是因为我们仅仅使用了一个非常简单的模型．不过，做一些小小的改进，我们就可以得到 97% 的正确率．最好的模型甚至可以获得超过 99.7% 的准确率！[这里有统计](http://rodrigob.github.io/are_we_there_yet/build/classification_datasets_results.html)。（**99.7是怎么做的？**）

比结果更重要的是，我们从这个模型中学习到的设计思想．不过，如果你仍然对这里的结果有点失望，可以查看下个教程，在那里你将学到如何用 FensorFlow 构建更加复杂的模型以获得更好的性能！


# 完整代码如下：


**后续补充**


# COMMENT：


**还是又很多东西不明白，不明确的，要补充下**


# REF：





 	
  1. tensorflow中文手册


