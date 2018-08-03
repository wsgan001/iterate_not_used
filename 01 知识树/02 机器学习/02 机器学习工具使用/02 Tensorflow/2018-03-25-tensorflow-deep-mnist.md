


在 [tensorflow MNIST 入门](http://106.15.37.116/2018/03/25/tensorflow-mnist-easy/) 的基础上，使用神经网络来提高准确率

手册上的关于使用tensorflow实现深度神经网络的教程。


# 要点：




## 1.介绍：


TensorFlow 是一个善于大规模数值计算的强大库件。它的一个强项就是训练并实现深度神经网络 (deep neural networks) 。在本小节中，我们将会学习 TensorFlow 模型构建的基本方法，并以此构建一个深度卷积 MNIST 分类器。

在 [tensorflow MNIST 入门](http://106.15.37.116/2018/03/25/tensorflow-mnist-easy/) 的基础上构建多层卷积神经网络模型。

在 MNIST 上只有 91% 正确率，实在太糟糕。在这个小节里，我们用一个稍微复杂
的模型：卷积神经网络来改善效果。这会达到大概 99.2% 的准确率。虽然不是最高，但
是还是比较让人满意。


## 2.权重初始化 Weight Initialization


在创建模型之前，我们先来创建权重和偏置。一般来说，初始化时应加入轻微噪声，来打破对称性，防止零梯度的问题。因为我们用的是 ReLU ，所以用稍大于 0 的值来初始化偏置能够避免节点输出恒为 0 的问题（ dead neurons ）。为了不在建立模型的时候反复做初始化操作，我们定义两个函数用于初始化。（**对称性和零梯度和ReLU之间的关系不知道？为什么稍大于0就可以避免输出恒为零的问题？**）


    def weight_variable(shape):
        initial = tf.truncated_normal(shape, stddev=0.1)
        return tf.Variable(initial)


    def bias_variable(shape):
        initial = tf.constant(0.1, shape=shape)
        return tf.Variable(initial)


**为什么 weight 使用的是 truncated_normal ？ 为什么bias是常量？**


## 3.卷积和池化 Convolution and Pooling


TensorFlow 在卷积和池化上有很强的灵活性。我们怎么处理边界？步长应该设多大？在这个实例里，我们会一直使用 vanilla 版本。我们的卷积使用 1 步长（ stride size ），0 边距（ padding size ）的模板，保证输出和输入是同一个大小。我们的池化用简单传统的 2×2 大小的模板做 max pooling 。为了代码更简洁，我们把这部分抽象成一个函数。（**什么是vanilla版本？步长是什么？一般设置为多大？边距一般是多大？，池化这个词还是有些奇怪，到底什么是池化？为什么叫池化？**）


    def conv2d(x, W):
        return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')


    def max_pool_2x2(x):
        return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, ], padding='SAME')


**padding是什么？ksize的几个参数分别代表什么？strides的参数分别代表什么？**


## 4.第一层卷积 First Convolutional Layer


现在我们可以开始实现第一层了。它由一个卷积接一个 max pooling 完成。卷积在每个 5×5 的 patch 中算出 32 个特征。权重是一个 [5, 5, 1, 32] 的张量，前两个维度是patch 的大小，接着是输入的通道数目，最后是输出的通道数目。输出对应一个同样大小的偏置向量。（**5*5的patch是怎么算出32个特征的？图片的大小不是28*28吗？为什么输入的通道是1？输出是32？**）


    W_conv1 = weight_variable([5, 5, 1, 32])
    b_conv1 = bias_variable([32])


为了用这一层，我们把 x 变成一个 4d 向量，第 2 、 3 维对应图片的宽高，最后一维代表颜色通道。（**为什么第一个是-1？x是784吗？为什么是4d的向量？不是3d的？**）


    x_image = tf.reshape(x, [ − 1,28,28,1])


我们把 x_image 和权值向量进行卷积相乘，加上偏置，使用 ReLU 激活函数，最后 max pooling 。


    h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
    h_pool1 = max_pool_2x2(h_conv1)




## 5.第二层卷积 Second Convolutional Layer


为了构建一个更深的网络，我们会把几个类似的层堆叠起来。第二层中，每个 5x5 的 patch 会得到 64 个特征。（**怎么得到64个特征的？**）


    W_conv2 = weight_variable([5, 5, 32, 64])
    b_conv2 = bias_variable([64])

    h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
    h_pool2 = max_pool_2x2(h_conv2)




## 6.密集连接层 Densely Connected Layer


现在，图片降维到 7×7 ，我们加入一个有 1024 个神经元的全连接层，用于处理整个图片。我们把池化层输出的张量 reshape 成一些向量，乘上权重矩阵，加上偏置，使用 ReLU 激活。（**为什么这个时候图片被降维到7*7？64哪里来的？**）


    W_fc1 = weight_variable([7 * 7 * 64, 1024])
    b_fc1 = bias_variable([1024])
    h_pool2_flat = tf.reshape(h_pool2, [−1,7*7*64])
    h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)


**上面这个h_pool2是什么？h_pool2_flat是什么？是接在什么地方的？**


## 7.Dropout


为了减少过拟合，我们在输出层之前加入 dropout 。我们用一个 placeholder 来代表一个神经元在 dropout 中被保留的概率。这样我们可以在训练过程中启用 dropout ，在测试过程中关闭 dropout 。 TensorFlow 的 tf.nn.dropout 操作会自动处理神经元输出值的scale 。所以用 dropout 的时候可以不用考虑 scale 。 （**什么scale？drop的scale吗？**）


    keep_prob = tf.placeholder("float")
    h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob)


**这个地方是keep的比率还是drop的比率？drop安排在哪里？全链接层之后吗？还是什么？**


## 8.输出层 Readout Layer


最后，我们添加一个 softmax 层，就像前面的单层 softmax regression 一样。（**为什么这个地方还有一层softmax？两层全链接层？那么之前的relu是起到什么作用？**）


    W_fc2 = weight_variable([1024, 10])
    b_fc2 = bias_variable([10])

    y_conv=tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2) + b_fc2)




## 9.训练和评估模型 Train and Evaluate the Model


这次效果又有多好呢？我们用前面几乎一样的代码来测测看。只是我们会用更加复杂的 ADAM 优化器来做梯度最速下降，在 feed_dict 中加入额外的参数 keep_prob 来控制 dropout 比例。然后每 100 次迭代输出一次日志。（**为什么是ADAM优化器？**）


    # 用来打印预测值
    correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_, 1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))

    # 用来训练
    cross_entropy = -tf.reduce_sum(y_ * tf.log(y_conv))
    train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)


    sess.run(tf.global_variables_initializer())
    for i in range(10000):
        train_batch = mnist.train.next_batch(50)
        test_batch = mnist.test.next_batch(50)
        train_step.run(feed_dict={x: train_batch[0], y_: train_batch[1], keep_prob: 0.5})

        if i % 100 == 0:
            train_accuracy = accuracy.eval(feed_dict={x: train_batch[0], y_: train_batch[1], keep_prob: 1.0})
            test_accuracy = accuracy.eval(feed_dict={x: test_batch[0], y_: test_batch[1], keep_prob: 1.0})
            print('step %d'%i)
            print('training accuracy %g' % train_accuracy)
            print('test accuracy %g' % test_accuracy)


以上代码，在最终测试集上的准确率大概是 99.2% 。

目前为止，我们已经学会了用 TensorFlow 来快速和简易地搭建、训练和评估一个
复杂一点儿的深度学习模型。


# 完整代码如下：




    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data

    mnist = input_data.read_data_sets('MNIST_data', one_hot=True)
    sess = tf.InteractiveSession()

    x = tf.placeholder(tf.float32, shape=[None, 784])
    y_ = tf.placeholder(tf.float32, shape=[None, 10])

    W = tf.Variable(tf.zeros([784, 10]))
    b = tf.Variable(tf.zeros([10]))


    def weight_variable(shape):
        initial = tf.truncated_normal(shape, stddev=0.1)
        return tf.Variable(initial)


    def bias_variable(shape):
        initial = tf.constant(0.1, shape=shape)
        return tf.Variable(initial)


    def conv2d(x, W):
        return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')


    def max_pool_2x2(x):
        return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')



    x_image = tf.reshape(x, [-1, 28, 28, 1])

    W_conv1 = weight_variable([5, 5, 1, 32])
    b_conv1 = bias_variable([32])
    h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
    h_pool1 = max_pool_2x2(h_conv1)

    W_conv2 = weight_variable([5, 5, 32, 64])
    b_conv2 = bias_variable([64])
    h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
    h_pool2 = max_pool_2x2(h_conv2)

    h_pool2_flat = tf.reshape(h_pool2, [-1, 7 * 7 * 64])
    W_fc1 = weight_variable([7 * 7 * 64, 1024])
    b_fc1 = bias_variable([1024])
    h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)

    # 从fc1的输出里面drop一些
    keep_prob = tf.placeholder(dtype=tf.float32)
    h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob)

    W_fc2 = weight_variable([1024, 10])
    b_fc2 = bias_variable([10])
    y_conv = tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2) + b_fc2)

    # 用来打印预测值
    correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_, 1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))

    # 用来训练
    cross_entropy = -tf.reduce_sum(y_ * tf.log(y_conv))
    train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)


    sess.run(tf.global_variables_initializer())
    for i in range(10000):
        train_batch = mnist.train.next_batch(50)
        test_batch = mnist.test.next_batch(50)
        train_step.run(feed_dict={x: train_batch[0], y_: train_batch[1], keep_prob: 0.5})

        if i % 100 == 0:
            train_accuracy = accuracy.eval(feed_dict={x: train_batch[0], y_: train_batch[1], keep_prob: 1.0})
            test_accuracy = accuracy.eval(feed_dict={x: test_batch[0], y_: test_batch[1], keep_prob: 1.0})
            print('step %d'%i)
            print('training accuracy %g' % train_accuracy)
            print('test accuracy %g' % test_accuracy)





# COMMENT：


**还是有很多的问题没有解决，要弄明白**


# REF：
