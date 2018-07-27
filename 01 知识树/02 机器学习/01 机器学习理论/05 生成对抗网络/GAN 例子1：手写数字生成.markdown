# GAN 例子1：手写数字生成

# ORIGINAL

1. [GAN入门教程｜从0开始，手把手教你学会最火的神经网络](https://zhuanlan.zhihu.com/p/27440393)




# TODO






  * **文章只看了一小半，代码也没有怎么看，要接着看**





* * *





# INTRODUCTION






  * aaa





# 项目要求


学习构建分析手写数字图像的 GAN，并且从零开始学如何让它学会生成新图像。


# 模型架构


GAN中包含两个模型：生成模型（Generative Model）和判别模型（Discriminative Model）。


![](http://106.15.37.116/wp-content/uploads/2018/05/img_5b03e995cb9b1.png)


判别模型是一个分类器，它判断给定的图片到底是来自数据集的真实图像，还是人工创建的假图像。这基本上就是一个表现为卷积神经网络（CNN）形式的二元分类器。

生成模型通过反卷积神经网络将随机输入值转化为图像。

在数次训练迭代的历程中，判别器和生成器的的权重和偏差都是通过反向传播训练的。判别器学习从一堆生成器生成的假数字图像中，找出真正的数字图像。与此同时，生成器通过判别器的反馈学习如何生成具有欺骗性的图片，防止被判别器识别出来。


# 判别网络


判别器是一个卷积神经网络，接收图片大小为28×28×1的输入图像，之后返还一个单一标量值来描述输入图像的真伪——判断到底是来自MNIST图像集还是生成器。




![](http://106.15.37.116/wp-content/uploads/2018/05/img_5b03e9f2ee7ea.png)


判别器的结构与TensorFlow的样例CNN分类模型密切相关。它有两层特征为5×5像素特征的卷积层，还有两个全连接层按图像中每个像素计算增加权重的层。

创建了神经网络后，通常需要将权重和偏差初始化，这项任务可以在tf.get_variable中完成。权重在截断正态分布中被初始化，偏差在0处被初始化。

tf.nn.conv2d()是TensorFlow中的标准卷积函数，它包含四个参数：首个参数就是输入图像（input volume）,也就是本示例中的28×28像素的图片；第二个参数是滤波器/权矩阵，最终你也可以改变卷积的“步幅”和“填充”。这两个参数控制着输出图像的尺寸大小。

其实上面这些就是一个普通简单的二进制分类器，如果你不是初次接触CNN，应该对此并不陌生。




![](http://106.15.37.116/wp-content/uploads/2018/05/img_5b03ea0a45a8b.png)


定义了判别器之后，我们需要回头看看生成模型。我们将以Tim O’Shea编写的简单生成器代码为基础构建模型的整体结构。

Code链接：

[osh/KerasGAN](https://link.zhihu.com/?target=https%3A//github.com/osh/KerasGAN)

其实你可以把生成器想象成反向卷积神经网络的一种。判别器就是一个典型的CNN，它能将二维或三维的像素值矩阵（matrix of pixel values）转化成一个概率。然而作为一个生成器，需要d-维度向量 d-dimensional vector ，并需要将其变为28*28的图像。ReLU和批量标准化（batch normalization）也经常用于稳定每一层的输出。

在这个神经网络中，我们用了三个卷积层和插值，直到形成28*28像素的图像。

我们在输出层添加了一个tf.sigmoid() 激活函数，它将挤压灰色呈现白色或黑色相，从而产生一个更清晰的图像。


# 生成样本图像


定义完生成器和判别函数，我们现在看看没有训练过的生成器会生成怎样的样例。

首先打开TensorFlow，为我们的生成器创建一个占位符。占位符的形式是None x z_dimensions，关键字None意味着可以在运行会话时确定它的值。我们通常用None作为我们第一个维度，所以我们的批处理大小是可变的。有了关键词None,所以不需要指定batch_size。

代码如下：


    import tensorflow as tf
    import numpy as np
    import datetime
    import matplotlib.pyplot as plt

    from tensorflow.examples.tutorials.mnist import input_data

    def discriminator(images, reuse_variables=None):
        with tf.variable_scope(tf.get_variable_scope(), reuse=reuse_variables) as scope:
            # First convolutional and pool layers
            # This finds 32 different 5 x 5 pixel features
            d_w1 = tf.get_variable('d_w1', [5, 5, 1, 32], initializer=tf.truncated_normal_initializer(stddev=0.02))
            d_b1 = tf.get_variable('d_b1', [32], initializer=tf.constant_initializer(0))
            d1 = tf.nn.conv2d(input=images, filter=d_w1, strides=[1, 1, 1, 1], padding='SAME')
            d1 = d1 + d_b1
            d1 = tf.nn.relu(d1)
            d1 = tf.nn.avg_pool(d1, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')

            # Second convolutional and pool layers
            # This finds 64 different 5 x 5 pixel features
            d_w2 = tf.get_variable('d_w2', [5, 5, 32, 64], initializer=tf.truncated_normal_initializer(stddev=0.02))
            d_b2 = tf.get_variable('d_b2', [64], initializer=tf.constant_initializer(0))
            d2 = tf.nn.conv2d(input=d1, filter=d_w2, strides=[1, 1, 1, 1], padding='SAME')
            d2 = d2 + d_b2
            d2 = tf.nn.relu(d2)
            d2 = tf.nn.avg_pool(d2, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')

            # First fully connected layer
            d_w3 = tf.get_variable('d_w3', [7 * 7 * 64, 1024], initializer=tf.truncated_normal_initializer(stddev=0.02))
            d_b3 = tf.get_variable('d_b3', [1024], initializer=tf.constant_initializer(0))
            d3 = tf.reshape(d2, [-1, 7 * 7 * 64])
            d3 = tf.matmul(d3, d_w3)
            d3 = d3 + d_b3
            d3 = tf.nn.relu(d3)

            # Second fully connected layer
            d_w4 = tf.get_variable('d_w4', [1024, 1], initializer=tf.truncated_normal_initializer(stddev=0.02))
            d_b4 = tf.get_variable('d_b4', [1], initializer=tf.constant_initializer(0))
            d4 = tf.matmul(d3, d_w4) + d_b4

            # d4 contains unscaled values
            return d4


    def generator(z, batch_size, z_dim):
        g_w1 = tf.get_variable('g_w1', [z_dim, 3136], dtype=tf.float32,
                               initializer=tf.truncated_normal_initializer(stddev=0.02))
        g_b1 = tf.get_variable('g_b1', [3136], initializer=tf.truncated_normal_initializer(stddev=0.02))
        g1 = tf.matmul(z, g_w1) + g_b1
        g1 = tf.reshape(g1, [-1, 56, 56, 1])
        g1 = tf.contrib.layers.batch_norm(g1, epsilon=1e-5, scope='g_b1')
        g1 = tf.nn.relu(g1)

        # Generate 50 features
        g_w2 = tf.get_variable('g_w2', [3, 3, 1, z_dim / 2], dtype=tf.float32,
                               initializer=tf.truncated_normal_initializer(stddev=0.02))
        g_b2 = tf.get_variable('g_b2', [z_dim / 2], initializer=tf.truncated_normal_initializer(stddev=0.02))
        g2 = tf.nn.conv2d(g1, g_w2, strides=[1, 2, 2, 1], padding='SAME')
        g2 = g2 + g_b2
        g2 = tf.contrib.layers.batch_norm(g2, epsilon=1e-5, scope='g_b2')
        g2 = tf.nn.relu(g2)
        g2 = tf.image.resize_images(g2, [56, 56])

        # Generate 25 features
        g_w3 = tf.get_variable('g_w3', [3, 3, z_dim / 2, z_dim / 4], dtype=tf.float32,
                               initializer=tf.truncated_normal_initializer(stddev=0.02))
        g_b3 = tf.get_variable('g_b3', [z_dim / 4], initializer=tf.truncated_normal_initializer(stddev=0.02))
        g3 = tf.nn.conv2d(g2, g_w3, strides=[1, 2, 2, 1], padding='SAME')
        g3 = g3 + g_b3
        g3 = tf.contrib.layers.batch_norm(g3, epsilon=1e-5, scope='g_b3')
        g3 = tf.nn.relu(g3)
        g3 = tf.image.resize_images(g3, [56, 56])

        # Final convolution with one output channel
        g_w4 = tf.get_variable('g_w4', [1, 1, z_dim / 4, 1], dtype=tf.float32,
                               initializer=tf.truncated_normal_initializer(stddev=0.02))
        g_b4 = tf.get_variable('g_b4', [1], initializer=tf.truncated_normal_initializer(stddev=0.02))
        g4 = tf.nn.conv2d(g3, g_w4, strides=[1, 2, 2, 1], padding='SAME')
        g4 = g4 + g_b4
        g4 = tf.sigmoid(g4)

        # Dimensions of g4: batch_size x 28 x 28 x 1
        return g4



    # 加载图像
    mnist = input_data.read_data_sets("MNIST_data/")
    sample_image = mnist.train.next_batch(1)[0]
    print(sample_image.shape)
    sample_image = sample_image.reshape([28, 28])
    plt.imshow(sample_image, cmap='Greys')


    #随便生成一个图像看一下
    #创建能够保存生成器输出的变量
    z_dimensions = 100
    z_placeholder = tf.placeholder(tf.float32, [None, z_dimensions])
    generated_image_output = generator(z_placeholder, 1, z_dimensions)
    #将输入的随机噪声向量初始化
    z_batch = np.random.normal(0, 1, [1, z_dimensions])
    # 生成一个图像看看  像噪音
    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        generated_image = sess.run(generated_image_output,
                                    feed_dict={z_placeholder: z_batch})
        generated_image = generated_image.reshape([28, 28])
        plt.imshow(generated_image, cmap='Greys')


    # 重置
    tf.reset_default_graph()


    #下面开始正式进行
    # 准备一些参数
    batch_size = 50
    # z_placeholder 用来向生成器中输入噪音
    z_placeholder = tf.placeholder(tf.float32, [None, z_dimensions], name='z_placeholder')
    # x_placeholder 用来向判别器中输入图片
    x_placeholder = tf.placeholder(tf.float32, shape = [None,28,28,1], name='x_placeholder')
    # Gz 用来存放生成的图片
    Gz = generator(z_placeholder, batch_size, z_dimensions)
    #Dx用来存放判别器根据一张真实图片预测出的可能性
    Dx = discriminator(x_placeholder)
    #Dg 用来存放判别器根据一张生成图片预测出的可能性
    Dg = discriminator(Gz, reuse_variables=True)


    #定义损失函数
    #判别器的损失函数，函数计算 Dx和 0与 Dg 和 1之间的交叉熵损失 的各分量的平均值
    d_loss_real = tf.reduce_mean(
        tf.nn.sigmoid_cross_entropy_with_logits(logits = Dx, labels = tf.ones_like(Dx)))
    d_loss_fake = tf.reduce_mean(
        tf.nn.sigmoid_cross_entropy_with_logits(logits = Dg, labels = tf.zeros_like(Dg)))
    # 生成器的损失函数
    g_loss = tf.reduce_mean(
        tf.nn.sigmoid_cross_entropy_with_logits(logits = Dg, labels = tf.ones_like(Dg)))


    #开始定义优化程序
    tvars = tf.trainable_variables()
    # 创建两个变量列表，一个是判别器的权重和偏差，另一个是生成器的权重和偏差。
    #这就是当给TensorFlow变量取名字需要深思熟虑的原因。
    d_vars = [var for var in tvars if 'd_' in var.name]#判别器的权重和偏差
    g_vars = [var for var in tvars if 'g_' in var.name]#生成器的权重和偏差
    print([v.name for v in d_vars])
    print([v.name for v in g_vars])


    #制定两个优化器，我们一般选择Adam优化算法
    # 用来训练判别器
    d_trainer_fake = tf.train.AdamOptimizer(0.0003).minimize(d_loss_fake, var_list=d_vars)
    d_trainer_real = tf.train.AdamOptimizer(0.0003).minimize(d_loss_real, var_list=d_vars)
    #用来训练生成器
    g_trainer = tf.train.AdamOptimizer(0.0001).minimize(g_loss, var_list=g_vars)

    # From this point forward, reuse variables
    tf.get_variable_scope().reuse_variables()

    # tensorboard上需要用到的 tensorboard怎么用？
    tf.summary.scalar('Generator_loss', g_loss)
    tf.summary.scalar('Discriminator_loss_real', d_loss_real)
    tf.summary.scalar('Discriminator_loss_fake', d_loss_fake)
    images_for_tensorboard = generator(z_placeholder, batch_size, z_dimensions)
    tf.summary.image('Generated_images', images_for_tensorboard, 5)
    merged = tf.summary.merge_all()
    logdir = "tensorboard/" + datetime.datetime.now().strftime("%Y%m%d-%H%M%S") + "/"
    writer = tf.summary.FileWriter(logdir, sess.graph)



    sess = tf.Session()
    sess.run(tf.global_variables_initializer())

    # 先给判别器几个简单的原始训练进行迭代，这种方法有助于形成对生成器有用的梯度。
    for i in range(300):
        z_batch = np.random.normal(0, 1, size=[batch_size, z_dimensions])
        real_image_batch = mnist.train.next_batch(batch_size)[0].reshape([batch_size, 28, 28, 1])
        _, __, dLossReal, dLossFake = sess.run([d_trainer_real, d_trainer_fake, d_loss_real, d_loss_fake],
                                               {x_placeholder: real_image_batch, z_placeholder: z_batch})
        if(i % 100 == 0):
            print("dLossReal:", dLossReal, "dLossFake:", dLossFake)


    #进行主要的训练循环，训练生成器和判别器   GPU>3h   CPU>30h
    for i in range(100000):
        real_image_batch = mnist.train.next_batch(batch_size)[0].reshape([batch_size, 28, 28, 1])
        z_batch = np.random.normal(0, 1, size=[batch_size, z_dimensions])

        # Train discriminator on both real and fake images
        _, __, dLossReal, dLossFake = sess.run([d_trainer_real, d_trainer_fake, d_loss_real, d_loss_fake],
                                               {x_placeholder: real_image_batch, z_placeholder: z_batch})

        # Train generator
        z_batch = np.random.normal(0, 1, size=[batch_size, z_dimensions])
        _ = sess.run(g_trainer, feed_dict={z_placeholder: z_batch})

        if i % 10 == 0:
            # Update TensorBoard with summary statistics
            z_batch = np.random.normal(0, 1, size=[batch_size, z_dimensions])
            summary = sess.run(merged, {z_placeholder: z_batch, x_placeholder: real_image_batch})
            writer.add_summary(summary, i)

        if i % 100 == 0:
            # Every 100 iterations, show a generated image
            print("Iteration:", i, "at", datetime.datetime.now())
            z_batch = np.random.normal(0, 1, size=[1, z_dimensions])
            generated_images = generator(z_placeholder, 1, z_dimensions)
            images = sess.run(generated_images, {z_placeholder: z_batch})
            plt.imshow(images[0].reshape([28, 28]), cmap='Greys')
            plt.show()

            # Show discriminator's estimate
            im = images[0].reshape([1, 28, 28, 1])
            result = discriminator(x_placeholder)
            estimate = sess.run(result, {x_placeholder: im})
            print("Estimate:", estimate)


    #加载了一个我们在高速GPU机器上训练了10小时的模型，你可以试验下训练过的GAN
    '''
    saver = tf.train.Saver()
    with tf.Session() as sess:
        saver.restore(sess, 'pretrained-model/pretrained_gan.ckpt')
        z_batch = np.random.normal(0, 1, size=[10, z_dimensions])
        z_placeholder = tf.placeholder(tf.float32, [None, z_dimensions], name='z_placeholder')
        generated_images = generator(z_placeholder, 10, z_dimensions)
        images = sess.run(generated_images, {z_placeholder: z_batch})
        for i in range(10):
            plt.imshow(images[i].reshape([28, 28]), cmap='Greys')
            plt.show()
    '''





# 完整项目代码下载


下载地址：[百度网盘](https://pan.baidu.com/s/1NAwroGfCcTouhO6GQmitUw)  密码：epxh





* * *





# COMMENT
