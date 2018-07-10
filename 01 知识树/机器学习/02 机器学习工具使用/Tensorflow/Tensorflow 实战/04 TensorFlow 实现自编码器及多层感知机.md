#### TensorFlow实现自编码 器及多层感知机

##### 4.1自编码器简介

传统机器学习任务很大程度上依赖于好的特征工程，比如对数值型、日期时间型、种 类型等特征的提取。特征工程往往是非常耗时耗力的，在图像、语音和视频中提取到有效 的特征就更难了，工程师必须在这些领域有非常深入的理解，并且使用专业算法提取这些 数据的特征。深度学习则可以解决人工难以提取有效特征的问题，它可以大大缓解机器学 习模型对特征工程的依赖。深度学习在早期一度被认为是一种无监督的特征学习 (Unsupervised Feature Learning ),模仿了人脑的对特征逐层抽象提取的过程。这其中有 两点很重要:一是无监督学习，即我们不需要标注数据就可以对数据进行一定程度的学习， 这种学习是对数据内容的组织形式的学习，提取的是频繁出现的特征；二是逐层抽象，特 征是需要不断抽象的，就像人总是从简单基础的概念开始学习，再到复杂的概念。学生们 要从加减乘除开始学起，再到简单函数，然后到微积分，深度学习也是一样，它从简单的 微观的特征开始，不断抽象特征的层级，逐渐往复杂的宏观特征转变。

例如在图像识别问题中，假定我们有许多汽车的图片，要如何判定这些图片是汽车呢?

如果我们从像素级特征开始进行训练分类器，那么绝大多数算法很难有效地工作。如果我 们提取出高阶的特征，比如汽车的车轮、汽车的车窗、汽车的车身，那么使用这些高阶特 征便可以非常准确地对图片进行分类，这就是高阶特征的效果。不过任何高阶特征都是由 底层特征组合而成的，比如车轮由橡胶轮胎、车轴、轮辐等组成。而其中每一个组件都是 由更小单位的特征组合而成的，比如橡胶轮胎由许多黑色的同心圆组成，而这些同心圆也 都由许多圆弧曲线组成，圆弧曲线都由像素组成。我们将前面的过程逆过来，将一张图片 的原始像素慢慢抽象，从像素组成点、线，再将点、线组合成小零件，再将小零件组成车 轮、车窗、车身等高阶特别，这便是深度学习在训练过程中所做的特征学习。

早年由学者们研究稀疏编码(Sparse Coding)时，他们收集了大量黑白风景照，并 从中提取了许多16像素。16像素的图像碎片。他们发现几乎所有的图像碎片都可以由64 种正交的边组合得到，如图4-1所示，并且组合出一张图像碎片需要的边的数量是很少的， 即稀疏的。学者同时发现声音也存在这种情况，他们从大量的未标注音频中发现了 20种 基本结构，绝大多数声音可以由这些基本结构线性组合得到。这其实就是特征的稀疏表达， 使用少量的基本特征组合拼装得到更高层抽象的特征。通常我们也需要多层的神经网络, 对每一层神经网络来说，前一层的输出都是未加工的像素，而这一层则是对像素进行加工 组织成更高阶的特征(即前面提到的将边组合成图像碎片)。

[al7aM] = [o, o, o, 0.8, o,o, 0.3, o,o, 0.5, o] (feature representation)

图4-1图像碎片可由少量的基本结构稀疏表达

我们来看一下实际的例子。假如我们有许多基本结构，比如指向各个方向的边、白块、 黑块等，如图4-2所示，我们可以通过不同方式组合出不同的高阶特征，并最终拼出不同 的目标物体。这些基本结构就是basis,在人脸识别任务中，我们可以使用它们拼出人脸

的不同器官，比如鼻子、嘴、眼睛、眉毛、脸颊等，这些器官又可以向上一层拼出不同样 式的人脸，最后模型通过在图片中匹配这些不同样式的人脸(即高阶特征)来进行识别。 同样，basis可以拼出汽车上不同的组件，最终拼出各式各样的车型；也可以拼出大象身 体的不同部位，最后组成各种尺寸、品种、颜色的大象；还可以拼出椅子的凳、座、靠背 等，最后组成不同款式的椅子。特征是可以不断抽象转为高一级的特征的，那我们如何找 到这些基本结构，然后如何抽象呢？如果我们有很多标注的数据，则可以训练一个深层的 神经网络。如果没有标注的数据呢？这种情况下，我们依然可以使用无监督的自编码器来 提取特征。自编码器(AutoEncoder),顾名思义，即可以使用自身的高阶特征编码自己。 自编码器其实也是一种神经网络，它的输入和输出是一致的，它借助稀疏编码的思想，目 标是使用稀疏的一些高阶特征重新组合来重构自己。因此，它的特点非常明显：第一，期 望输入/输出一致；第二，希望使用高阶特征来重构自己，而不只是复制像素点。

Features learned from training on different object classes.

Elephants

m.二，/*'

鼸•、》.Tt* IrZ U* '    - /

j东------



i

，：亂    \；：^

i *?<

L-.，、、冰



X>\ f



\jf

X I y \\ ‘I

图4-2由基本结构不断抽象为高阶特征

Hinton 教授在 Science 发表文章 Reducing the dimensionality of data with neural networks'讲解了使用自编码器对数据进行降维的方法。Hinton还提出了基于深度信念 网络(Deep Belief Networks24, DBN,由多层RBM堆叠而成)可使用无监督的逐层训练 的贪心算法,为训练很深的网络提供了一个可行方案:我们可能很难直接训练极深的网络， 但是可以用无监督的逐层训练提取特征，将网络的权重初始化到一个比较好的位置，辅助 后面的监督训练。无监督的逐层训练，其思想和自编码器(AutoEncoder)非常相似。后 者的目标是让神经网络的输出能和原始输入一致，相当于学习一个恒等式7 = X，如图4-3 所示。自编码器的输入节点和输出节点的数量是一致的，但如果只是单纯地逐个复制输入 节点则没有意义，像前面提到的，自编码器通常希望使用少量稀疏的高阶特征来重构输入， 所以我们可以加入几种限制。

(1)如果限制中间隐含层节点的数量，比如让中间隐含层节点的数量小于输入/输出 节点的数量，就相当于一个降维的过程。此时已经不可能出现复制所有节点的情况，因为 中间节点数小于输入节点数，那只能学习数据中最重要的特征复原，将可能不太相关的内 容去除。此时，如果再给中间隐含层的权重加一个L1的正则，则可以根据惩罚系数控制 隐含节点的稀疏程度，惩罚系数越大，学到的特征组合越稀疏，实际使用(非零权重)的 特征数量越少。

(2 )如果给数据加入噪声，那么就是Denoising AutoEncoder (去噪自编码器)，我们 将从噪声中学习出数据的特征。同样，我们也不可能完全复制节点，完全复制并不能去除 我们添加的噪声，无法完全复原数据。所以唯有学习数据频繁出现的模式和结构，将无规 律的噪声略去，才可以复原数据。

去噪自编码器中最常使用的噪声是加性高斯噪声(Additive Gaussian Noise, AGN)， 其结构如图4-3所示。当然也可以使用Masking Noise,即有随机遮挡的噪声，这种情况 下，图像中的一部分像素被置为0,模型需要从其他像素的结构推测出这些被遮挡的像素 是什么，因此模型依然需要学习图像中抽象的高阶特征。

Layer Lj

图4-3自编码器结构图，学习目标是使用少量高阶特征重构输入 如果自编码器的隐含层只有一层，那么其原理类似于主成分分析(PCA)。Hinton提

出的DBN模型有多个隐含层，每个隐含层都是限制性玻尔兹曼机RBM ( Restricted Boltzman Machine, 一种具有特殊连接分布的神经网络)。DBN训练时，需要先对每两层 间进行无监督的预训练(pre-training),这个过程其实就相当于一个多层的自编码器，可 以将整个网络的权重初始化到一个理想的分布。最后，通过反向传播算法调整模型权重， 这个步骤会使用经过标注的信息来做监督性的分类训练。当年DBN给训练深层的神经网 络提供了可能性，它能解决网络过深带来的梯度弥散(Gradient Vanishment)问题，让 训练变得容易。简单地说，Hinton的思路就是先用自编码器的方法进行无监督的预训练， 提取特征并初始化权重，然后使用标注信息进行监督式的训练。当然自编码器的作用不仅 局限于给监督训练做预训练，直接使用自编码器进行特征提取和分析也是可以的。现实中 数据最多的还是未标注的数据，因此自编码器拥有许多用武之地。

##### 4.2 TensorFlow实现自编码器

下面我们就开始实现最具代表性的去噪自编码器。去噪自编码器的使用范围最广也最 通用。而其他几种自编码器，读者可以对代码加以修改自行实现，其中无噪声的自编码器 只需要去掉噪声，并保证隐含层节点小于输入层节点；Masking Noise的自编码器只需要 将高斯噪声改为随机遮挡噪声；Variational AutoEncoder ( VAE )则相对复杂，VAE对中 间节点的分布有强假设，拥有额外的损失项，且会使用特殊的SGVB ( Stochastic Gradient Variational Bayes)算法进行训练。目前VAE还在生成模型中发挥了很大的作用。

这里我们依然是先导入常用库NumPy，还有Scikit-leam中的preprocessing模块，这 是一个对数据进行预处理的常用模块，之后我们会使用其中的数据标准化功能。同时本节 依然使用MNIST数据集，因此也导入TensorFlow中MNIST数据的加载模块。本节代码 主要来自TensorFlow的开源实现25。

import numpy as np

import sklearn.preprocessing as prep

import tensorflow as tf

from tensorflow.examples.tutorials.mnist import input_data

我们的自编码器中会使用到一种参数初始化方法xavier initialization26,需要先定义好 它。Xavier初始化器在Caffe的早期版本中被频繁使用，它的特点是会根据某一层网络的 输入、输出节点数量自动调整最合适的分布。Xaiver Glorot和深度学习三巨头之一的

Yoshua Bengio在一篇论文中指出，如果深度学习模型的权重初始化得太小，那信号将在 每层间传递时逐渐缩小而难以产生作用，但如果权重初始化得太大，那信号将在每层间传 递时逐渐放大并导致发散和失效。而Xaiver初始化器做的事情就是让权重被初始化得不 大不小，正好合适。从数学的角度分析，Xavier就是让权重满足0均值，同时方差为

nin+nout

分布可以用均匀分布或者高斯分布。如下代码所示，我们通过tf.random_uniform创建了 一个(-I一5一)范围内的均匀分布，而它的方差根据公式D(x) = (max-

\ nin+nout aJ nin+noutJ

m;n)2/12刚好等于。因此，这里实现的就是标准的均匀分布的Xaiver初始化器,

nin~^nout

其中fan_in是输入节点的数量，fan_out是输出节点的数量。

def xavier_init(fan_in, fan_out? constant = 1):

low = -constant * np.sqrt(6.0 / (fan_in + fan_out)) high = constant * np.sqrt(6.0 / (fan_in + fan_out)) return tf.random_uniform((fan_irb fan_out)j

minval = low, maxval = high, dtype = tf.float32)

下面我们就开始定义一个去噪自编码的class,方便以后使用。这个类会包含一个构 建函数_init_()，还有一些常用的成员函数，因此会比较长，下面会分为几个代码段讲解， 我们先来看构建函数。

_init_函数包含这样几个输入：n_input (输入变量数)、n_hidden (隐含层节点数)、 transfer_function (隐含层激活函数，默认为softplus )、optimizer (优化器，默认为Adam )、 scale (高斯噪声系数，默认为0.1 )。其中，class内的scale参数做成了一•个placeholder, 参数初始化则使用了接下来定义的_initialize_weights函数。这里需要注意的是，我们只使 用了一个隐含层，有需要的读者可以自行尝试多添加几个隐含层。

class AdditiveGaussianNoiseAutoencoder(object):

def _init_(self? n_inputj n_hidden_, transfer_function=tf.nn.softplus

optimizer = tf.train.AdamOptimizer()? scale=0.1): self.n_input = n一input self.n_hidden = n_hidden self.transfer = transfer_function self.scale = tf.placeholder(tf.float32) self,training_scale = scale

network_weights = self._initialize_weights() self.weights = network_weights

接下来开始定义网络结构，我们为输入x创建一个维度为n_input的placeholder。然 后建立一个能提取特征的隐含层，我们先将输入X加上噪声，即self.x +scale* tf.random_normal((n_i叩ut,))，然后用tf.matmul将加了噪声的输入与隐含层的权重wl 相乘，并使用tf.add加上隐含层的偏置bl，最后使用self.transfer对结果进行激活函数处 理。经过隐含层后’我们需要在输出层进行数据复原、重建操作(即建立reconstruction 层)，这里我们就不需要激活函数了，直接将隐含层的输出self.hidden乘上输出层的权重 w2,再加上输出层的偏置b2即可。

self.x = tf.placeholder(tf.float32j [None』 self.n_input]) self.hidden = self.transfer(tf.add(tf.matmul(

self.x + scale * tf.random_normal((n_input^))j self.weights['wl*])j self.weights[1bl']))

self.reconstruction = tf.add(tf.matmul(self.hidden^

self.weights['w2'])3 self.weights['b2'])

接下来定义自编码器的损失函数,这里直接使用平方误差(Squared Error)作为cost, 即用tf.subtract计算输出(self.reconstruction )与输入(self.x )之差，再使用tf.pow求差 的平方，最后使用tf.reduce_sum求和即可得到平方误差。再定义训练操作为优化器 self.optimizer对损失self.cost进行优化。最后创建Session,并初始化自编码器的全部模型 参数。

self.cost = 0.5 * tf.reduce_sum(tf.pow(tf.subtract(

self.reconstruction, self.x), 2.0))

self.optimizer = optimizer.minimize(self.cost)

init = tf.global_variables_initializer() self.sess = tf.Session() self.sess.run(init)

下面来看一下参数初始化函linitialize_weights,先创建一个名为all_weights的字典 diet,然后将wl、bl、w2、b2全部存入其中，最后返回all_weights。其中wl需要使用 前面定义的xavier_init函数初始化，我们直接传入输入节点数和隐含层节点数，然后xavier 即可返回一个比较适合于softplus等激活函数的权重初始分布，而偏置bl只需要使用 tf.zeros全部置为0即可。对于输出层self.reconstruction，因为没有使用激活函数，这里将 w2、b2全部初始化为0即可。

def _initialize_weights(self): all_weights = dict()

all_weights['wl*] = tf.Variable(xavier_init(self.n_inputj

self .n__hidden))

all一weights[*bl1] = tf.Variable(tf.zeros([self.n_hidden]j

dtype = tf.float32))

all_weights['w2*] = tf.Variable(tf.zeros([self.n_hidden?

self.n_input]dtype = tf,float32))

all_weights['b2'] = tf.Variable(tf.zeros([self.n_input]j

dtype = tf.float32))

return all_weights

我们定义计算损失cost及执行一步训练的函数partial_fit。函数里只需让Session执行 两个计算图的节点：分别是损失cost和训练过程optimizer,输入的feed_dict包括输入数 据X，以及噪声的系数scale。函数partial_fit做的就是用一个batch数据进行训练并返回 当前的损失cost。

def partial_fit(selfX):

cost, opt = self.sess.run((self.cost? self.optimizer)

feed_dict = {self.x: X, self.scale: self.training_scale})

return cost

我们也需要一个只求损失cost的函数calc_total_cost,这里就只让Session执行一个计 算图节点self.cost,传入的参数和前面的partial_fit 一致。这个函数是在自编码器训练完毕 后，在测试集上对模型性能进行评测时会用到的，它不会像partial_fit那样触发训练操作。

def calc_total_cost(selfX):

return self.sess.run(self.costfeed_dict = {self.x: X)

self.scale: self.training_scale

})

我们还定义了 transform函数，它返回自编码器隐含层的输出结果。它的目的是提供

一个接口来获取抽象后的特征，自编码器的隐含层的最主要功能就是学习出数据中的高阶 特征。

def transform(selfX):

return self.sess.run(self.hiddenj feed_dict = {self.x: X, self.scale: self.training_scale

})

我们再定义generate函数，它将隐含层的输出结果作为输入，通过之后的重建层将提 取到的高阶特征复原为原始数据。这个接口和前面的transform正好将整个自编码器拆分 为两部分，这里的generate接口是后半部分，将高阶特征复原为原始数据的步骤。

def generate(self4 hidden = None): if hidden is None:

hidden = np.random.normal(size = self.weights["bl"]) return self.sess.run(self.reconstruction,

feed_dict = {self.hidden: hidden})

接下来定义reconstruct函数，它整体运行一遍复原过程，包括提取高阶特征和通过高 阶特征复原数据，即包括transform和generate两块。输入数据是原数据，输出数据是复 原后的数据。

def reconstruct(selfX):

return self.sess.run(self.reconstruction^ feed_dict = {self.x: X_, self.scale: self.training_scale

})

这里的getWeights函数作用是获取隐含犀的权重wl。

def getWeights(self):

return self.sess.run(self.weights['wl'])

而getBiases函数则是获取隐含层的偏置系数bl。 def getBiases(self):

return self.sess.run(self.weights[1bl'])

至此，去噪自编码器的class就全部定义完了，包括神经网络的设计、权重的初始化，

—「TensorFlow 实战

以及几个常用的成员函数(transform、generate等，他们属于计算图中的子图)。接下来 使用定义好的AGN自编码器在MNIST数据集上进行一些简单的性能测试，看看模型对 数据的复原效果究竟如何。

接下来依然使用TensorFlow提供的读取示例数据的函数载入MNIST数据集。 mnist = input_data. read一data_sets('MNIST—data'one_hot = True)

先定义一个对训练、测试数据进行标准化处理的函数。标准化即让数据变成0均值， 且标准差为1的分布。方法就是先减去均值，再除以标准差。我们直接使用 skleam.preprossing的StandardScaler这个类，先在训练集上进行fit,再将这个Scaler用到 训练数据和测试数据上。这里需要注意的是，必须保证训练、测试数据都使用完全相同的 Scaler,这样才能保证后面模型处理数据时的一致性，这也就是为什么先在训练数据上fit 出一个共用的Scaler的原因。

def standard__scale(X_train, X_test):

preprocessor = prep.StandardScaler().fit(X_train)

X_train = preprocessor.transform(X_train)

X_test = preprocessor.transform(X_test) return X_trainj X_test

再定义一个获取随机block数据的函数：取一个从0到len(data) - batch_size之间的 随机整数,再以这个随机数作为block的起始位置，然后顺序取到一个batch size的数据。 需要注意的是，这属于不放回抽样，可以提高数据的利用效率。

def get_random_block_from_data(databatch_size):

start_index = np.random.randint(0j len(data) - batch_size) return data[start_index:(start_index + batch一size)]

使用之前定义的standard_scale函数对训练集、测试集进行标准化变换。

X_train^ X_test = standard一scale(mnist.train.images, mnist.test.images)

接下来定义几个常用参数，总训练样本数，最大训练的轮数(epoch殷为20,batch_size 设为128，并设置每隔一轮(epoch)就显不一次损失cost。

n 一 samples = int(mnist.train.num_examples)

training_epochs = 20

batch一size = 128

display_step = 1

创建一个AGN自编码器的实例，定义模型输入节点数njnput为784,自编码器的隐 含层节点数n_hidden为200,隐含层的激活函数transfer_function为softplus,优化器 optimizer为Adam且学习速率为0.001,同时将噪声的系数scale设为0.01 o

autoencoder = AdditiveGaussianNoiseAutoencoder(n_input = 784, n_hidden = 200,

transfer一function = tf.nn.softplus,

optimizer = tf.train.AdamOptimizer(learning_rate = 0.001)^ scale = 0.01)

下面开始训练过程，在每一轮(epoch)循环开始时，我们将平均损失avg_C0St设为 0,并计算总共需要的batch数(通过样本总数除以batch大小)，注意这里使用的是不放 回抽样，所以并不能保证每个样本都被抽到并参与训练。然后在每一个batch的循环中， 先使用get_random_block_from_data函数随机抽取一个block的数据，然后使用成员函数 partial_fit训练这个batch的数据并计算当前的cost,最后将当前的cost整合到avg_cost 中。在每一轮迭代后，显示当前的迭代数和这一轮迭代的平均cost。我们在第一轮迭代时， cost大约为19000,在最后一轮迭代时，cost大约为7000,再接着训练cost也很难继续降 低了。读者如果感兴趣，可以通过调整batch_size, epoch数、优化器、自编码器的隐含 层数、隐含节点数等，来尝试获得更低的cost。

for epoch in range(training_epochs): avg_cost = 0.

total_batch = int(n一samples / batch一size) for i in range(total_batch):

batch_xs = get_random_block_from_data(X_trainJ batch_size)

cost = autoencoder.partial_fit(batch一xs) avg_cost += cost / n一samples * batch^size

if epoch % display_step == 0:

print("Epoch:'%04d' % (epoch + 1), "cost:'、

"{9f}".format(avg_cost))

TensorFlow 实战

最后对训练完的模型进行性能测试，这里使用之前定义的成员函数Cal_total_COSt对 测试集Xjest进行测试，评价指标依然是平方误差，如果使用示例中的参数，损失值约 为60万。

print("Total cost: " + str(autoencoder.calc_total_cost(X_test)))

至此，去噪自编码器的TensorFlow实现就全部结束了。读者可以发现，实现自编码 器和实现一个单隐含层的神经网络差不多，只不过是在数据输入时做了标准化，并加上了 一个高斯噪声，同时我们的输出结果不是数字分类结果，而是复原的数据，因此不需要用 标注过的数据进行监督训练。自编码器作为一种无监督学习的方法，它与其他无监督学习 的主要不同在于,它不是对数据进行聚类,而是提取其中最有用、最频繁出现的高阶特征， 根据这些高阶特征重构数据。在深度学习发展早期非常流行的DBN,也是依靠这种思想， 先对数据进行无监督的学习，提取到一些有用的特征，将神经网络权重初始化到一个较好 的分布，然后再使用有标注的数据进行监督训练，即对权重进行fme-tune。

现在，无监督式预训练的使用场景比以前少了许多，训练全连接的MLP或者CNN、 RNN时，我们者P不需要先使用无监督训练提取特征。但是无监督学习乃至AutoEncoder 依然是非常有用的。现实生活中，大部分的数据都是没有标注信息的，但人脑就很擅长处 理这些数据，我们会提取其中的高阶抽象特征，并使用在其他地方。自编码器作为深度学 习在无监督领域的尝试是非常成功的，同时无监督学习也将是深度学习接下来的一个重要 发展方向。

##### 4.3多层感知机简介

在第3章中我们使用TensorFlow实现了一个简单的Softmax Regression模型，这个 线性模型最大的特点就是简单易用，但是拟合能力不强。Softmax Regression可以算是多 分类问题logistic regression,它和传统意义上的神经网络的最大区别是没有隐含层。隐含 层是神经网络的一个重要概念，它是指除输入、输出层外，中间的那些层。输入层和输出 层是对外可见的，因此也被称作可视层，而中间层不直接暴露出来，是模型的黑箱部分, 通常也比较难具有可解释性，所以一般被称作隐含层。有了隐含层，神经网络就具有了一 些特殊的属性，比如引入非线性的隐含层后，理论上只要隐含节点足够多，即俾只有一个 隐含层的神经网络也可以拟合任意函数。同时隐含层越多，越容易拟合复杂函数。有理论 研究表明，为了拟合复杂函数需要的隐含节点的数目，基本上随着隐含层的数量增多呈指

数下降趋势。也就是说层数越多，神经网络所需要的隐含节点可以越少。这也是深度学习 的特点之一，层数越深，概念越抽象，需要背诵的知识点（神经网络隐含节点）就越少。 不过实际使用中，使用层数较深的神经网络会遇到许多困难，比如容易过拟合、参数难以 调试、梯度弥散，等等。对这些问题我们需要很多Trick来解决，在最近几年的研究中， 越来越多的方法，比如Dropout27、Adagrad28、ReLU29等，逐渐帮助我们解决了一部分问 题。

过拟合是机器学习中一个常见的问题，它是指模型预测准确率在训练集上升高，但是 在测试集上反而下降了，这通常意味着泛化性不好，模型只是记忆了当前数据的特征，不 具备推广能力。尤其在神经网络中，因为参数众多，经常出现参数比数据还要多的情况， 这就非常容易出现只是记忆了训练集特征的情况。为了解决这个问题，Hinton教授团队 提出了一个思路简单但是非常有效的方法，Dropout。在使用复杂的卷积神经网络训练图 像数据时尤其有效，它的大致思路是在训练时，将神经网络某一层的输出节点数据随机丢 弃一部分。我们可以理解成随机把一张图片50%的点删除掉（即随机将50%的点变成黑 点），此时人还是很可能识别出这张图片的类别，当时机器也是可以的。这种做法实质上 等于创造出了很多新的随机样本，通过增大样本量、减少特征数量来防止过拟合。Dropout 其实也算是一种bagging方法，我们可以理解成每次丢弃节点数据是对特征的一种采样。 相当于我们训练了一个ensemble的神经网络模型，对每个样本都做特征采样，只不过没 有训练多个神经网络模型，只有一个融合的神经网络。

参数难以调试是神经网络的另一大痛点，尤其是SGD的参数，对SGD设置不同的 学习速率，最后得到的结果可能差异巨大。神经网络通常不是一个凸优化的问题，它处处 充满了局部最优。SGD本身也不是一个比较稳定的算法,结果可能会在最优解附近波动， 而不同的学习速率可能导致神经网络落入截然不同的局部最优之中。不过，通常我们也并 不指望能达到全局最优，有理论表示，神经网络可能有很多个局部最优解都可以达到比较 好的分类效果，而全局最优反而容易是过拟合的解。我们也可以从人来类推，不同的人有 各自迥异的脑神经连接，没有两个人的神经连接方式能完全一致，就像没有两个人的见解 能完全相同，但是毎个人的脑神经网络（局部最优解）对识别图片中物体类别都有很不错 的效果。对SGD,-开始我们可能希望学习速率大一些，可以加速收敛，但是训练的后 期又希望学习速率可以小一些，这样可以比较稳定地落入一个局部最优解。不同的机器学 习问题所需要的学习速率也不太好设置，需要反复调试，因此就有像Adagrad、Adam' Adadelta31等自适应的方法可以减轻调试参数的负担。对于这些优化算法，通常我们使用

—「TensorFlow 实跋

它默认的参数设置就可以取得一个比较好的效果。而SGD则需要对学习速率、 Momentum32, Nesterov33等参数进彳亍比较复杂的调试，当调试的参数较为适合问题时，才 能达到比较好的效果。

梯度弥散(Gradient Vanishment)是另一个影响深层神经网络训练的问题，在ReLU 激活函数出现之前，神经网络训练全部都是用Sigmoid作为激活函数。这可能是因为 Sigmoid函数具有限制性，输出数值在0~1，最符合概率输出的定义。非线性的Sigmoid 函数在信号的特征空间映射上，对中央区的信号增益较大，对两侧区的信号增益小。从生 物神经科学的角度来看，中央区酷似神经元的兴奋态，两侧区酷似神经元的抑制态。因而 在神经网络训练时，可以将重要特征置于中央区，将非重要特征置于两侧区。可以说， Sigmoid比最初期的线性激活函数y = X，阶梯激活函数y =    ==匕00))好

了不少。但是当神经网络层数较多时，Sigmoid函数在反向传播中梯度值会逐渐减小，经 过多层的传递后会呈指数级急剧减小，因此梯度值在传递到前面几层时就变得非常小了。 这种情况下，根据训练数据的反馈来更新神经网络的参数将会非常缓慢，基本起不到训练 的作用。直到ReLU的出现，才比较完美地解决了梯度弥散的问题。ReLU是一个简单的 非线性函数y = max(0,x),它在坐标轴上是一条折线(如图4-4(B)所示)，当x《0时，y = 0; 当x〉0时，y = x,非常类似于人脑的阈值响应机制(如图4-4(A)所示)。信号在超过某 个阈值时，神经元才会进入兴奋和激活的状态，平时则处于抑制状态。ReLU可以很好地 传递梯度，经过多层的反向传播，梯度依旧不会大幅缩小，因此非常适合训练很深的神经 网络。ReLU从正面解决了梯度弥散的问题，而不需要通过无监督的逐层训练初始化权重 来绕行。ReLU对比Sigmoid的主要变化有如下3点。

(1 )单侧抑制。

(2)    相对宽阔的兴奋边界。

(3)    稀疏激活性。

神经科学家在进彳亍大脑能量消耗的研究中发现，神经元编码的工作方式具有稀疏性, 推测大脑同时被激活的神经元只有1% ~ 4%。神经元只会对输入信号有少部分的选择性响 应，大量不相关的信号被屏蔽’这样可以更高效地提取重要特征。传统的Sigmoid函数则 有接近一半的神经元被激活，不符合神经科学的研究。Softplus虽然有单侧抑制，却没有 稀疏激活性，因而ReLU函数maX(0，x)成了最符合实际神经元的模型。目前，ReLU及其 变种(ElU34, PReLU35, RReLU36)已经成为了最主流的激活函数。实践中大部分情况下

(包括MLP和CNN, RNN内部主要还是使用Sigmoid、Tanh、Hard Sigmoid )将隐含层 的激活函数从Sigmoid替换为ReLU都可以带来训练速度及模型准确率的提升。当然神经 网络的输出层一般都还是Sigmoid函数，因为它最接近概率输出分布。

o

o o O 15105 (ZJeols SUUIL

2    4    6

Inpul currenl (A)

8 10 -9

X 10



o



-1



2 10

SJ

-3-2-10123

图4-4(B) ReLU激活函数及Softplus激活函数



图4-4(A)神经科学家提出的神经元激活模型



上面三段分别提到了可以解决多层神经网络问题的Dropout、Adagrad、ReLU等，那 么多层神经网络到底有什么显著的能力值得大家探索呢？或者说神经网络的隐含层到底 有什么用呢？隐含层的一个代表性的功能是可以解决XOR问题。在早期神经网络的研究 中，有学者提出一个尖锐的问题，当时(没有隐含层)的神经网络无法解决X0R的问题。 如图4-5所示，假设我们有两个维度的特征，并且有两类样本，(0, 0k(l, 1)是灰色， (0, 1X(1, 0)是黑色，在这个特征空间中这两类样本是线性不可分的，也就是说’我 们无法用一条直线把灰、黑两类分开。没有隐含层的神经网络是线性的，所以不可能对这 两类样本进行正确地区分。这是早期神经网络的致命缺点，也直接导致了当时神经网络研 究的低谷。当弓I入了隐含层并使用非线性的激活函数(如Sigmoid、ReLU) 我们可 以使用曲线划分两类样本，可以轻松解决XOR异或函数的分类问题。神经网络的隐含层 越多，就可以对原有特征进行越抽象的变换，模型的拟合能力就越强。这就是多层神经网 络(或多层感知机，Multi-Layer Perceptron, MLP )的功能所在。

接下来，我们通过例子展示在仅加入一个隐含层的情况下，神经网络对MNIST数据 集的分类性能就有显著提升，可以达到98%的准确率。当然，其中使用了 Dropout.Adagrad, ReLU等辅助性组件。

TensorFlow 实战

##### 4.4    TensorFlow实现多层感知机

在第3章中我们讲角¥了使用TensorFlow实现一个完整的Softmax Regression（无隐含 层），并在MNIST数据集上取得了大约92%的正确率。现在，我们要给神经网络加上隐 含层，并使用4.3节提到的减轻过拟合的Dropout、自适应学习速率的Adagrad,以及可 以解决梯度弥散的激活函数ReLU。在TensorFlow中实现这些都是非常方便的，通常只需 要调用相应的类或者函数即可。

首先，载入TensorFlow并加载MNIST数据集，创建一个TensorFlow默认的Interactive Session,这样后面执行各项操作就无须指定Session 了。

from tensorflow.examples.tutorials.mnist import input_data

import tensorflow as tf

mnist = input_data.read_data_sets("MNIST_data/"one_hot=True)

sess = tf.InteractiveSession()

接下来我们要给隐含层的参数设置Variable并进行初始化，这里in_units是输入节点 数，hl_units即隐含层的输出节点数设为300 （在此模型中隐含节点数设在200-1000范围 内的结果区别都不大）。Wl、bl是隐含层的权重和偏置，我们将偏置全部赋值为0,并将 权重初始化为截断的正态分布，其标准差为0.1,这一步可以通过tf.truncated_normal方便 地实现。因为模型使用的激活函数是ReLU,所以需要使用正态分布给参数加一点噪声, 来打破完全对称并且避免0梯度。在其他一些模型中，有时还需要给偏置赋上一些小的非 零值来避免dead neuron （死亡神经元），不过在这里作用不太明显。而对最后输出层的

Softmax,直接将权重W2和偏置b2全部初始化为0即可(上面提到过，对于Sigmoid， 在0附近最敏感、梯度最大)。

in_units = 784

hl_units = 300

W1 = tf.Variable(tf.truncated_normal([in_unitsj hl_units]j stddev=0.1)) bl = tf.Variable(tf.zeros([hl_units]))

W2 = tf.Variable(tf.zeros([hl_units, 10])) b2 = tf.Variable(tf.zeros([10]))

接下来定义输入x的placeholder。另外因为在训练和预测时，Dropout的比率keepjprob (即保留节点的概率)是不一样的，通常在训练时小于1,而预测时则等于1，所以也把 Dropout的比率作为计算图的输入，并定义成一个placeholder。

x = tf.placeholder(tf.floatSZ^ [None, in_units])

keep_prob = tf.placeholder(tf.float32)

下面定义模型结构。首先需要一个隐含层，命名为hiddenl ,可以通过 tf.nn.reluOf.matmuKxU+Z?!)实现一个激活函数为ReLU的隐含层，这个隐含层的计算公 式就是y = relu^W^ + hx)o接下来，调用tf.nn.dropout实现Dropout的功能，即随机将 —部分节点置为0，这里的keep_prob参数即为保留数据而不置为0的比例，在训练时应 该是小于1的，用以制造随机性，防止过拟合；在预测时应该等于1，即使用全部特征来 预测样本的类别。最后是输出层，也就是第3章介绍的Softmax，这一行代码的功能和之 前是一致的。

hiddenl = tf.nn.relu(tf.matmul(x^ Wl) + bl)

hiddenl_drop = tf.nn.dropout(hiddenl^ keep_prob)

y = tf.nn.softmax(tf.matmul(hiddenl_dropj W2) + b2)

第3章提到的使用TensorFlow训练神经网络的4个步骤，到目前为止，我们已经完 成了 4步中的第1步：定义算法公式，即神经网络forward时的计算。接下来继续第2步， 定义损失函数和选择优化器来优化loss,这里的损失函数继续使用交叉信息嫡，和之前一 致，但是优化器选择自适应的优化器Adagrad,并把学习速率设为0.3,这里我们直接使 用tf.train.AdagradOptimizer就可以了。类似地，还有Adadelta及Adam等优化器，读者可 以自行尝试，不过学习速率可能需要调整。

T㊀nsorFlow实战

y_ = tf.placeholder(tf.float32j [None, 10])

cross一entropy = tf.reduce_mean(-tf.reduce_sum(y_ * tf.log(y)

reduction_indices=[l]))

train_step = tf.train.AdagradOptimizer(0.3).minimize(cross_entropy)

然后进行第3步，训练步骤，这里有一点和之前不同，我们加入了 keep_prob作为计 算图的输入，并且在训练时设为0.75,即保留75%的节点，其余的25%置为0。一般来说， 对越复杂越大规模的神经网络，Dropout的效果越显著。另外，因为加入了隐含层，我们 需要更多的训练迭代来优化模型参数以达到一个比较好的效果。所以一共采用了 3000个 bacth,每个batch包含100条样本，一共30万的样本，相当于是对全数据集进行了 5轮 (epoch)迭代。读者也可以尝试增大循环次数，准确率会略有提高。

tf.global_variables_initializer().run()

for i in range(3000):

batch一xs_, batches = mnist.train.next_batch( 100) train_step.run({x: batch_xs> y_: batch_ys_, keep_prob: 0.75})

最后我们进彳亍第4步，对模型进行准确率评测，这里的代码和第3章讲解的评测代码 基本一致，但还是需要加入一个keepjrob作为输入。因为是预测部分，所以我们直接令 keepjrob等于1即可，这样可以达到模型最好的预测效果。

correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y一， 1))

accuracy = tf.reduce_mean(tf.cast(correct_predictiorb tf.float32))

print(accuracy.eval({x: mnist.test.imagesy一： mnist.test.labels^

一    i

keep_prob: 1.0}))

最终，我们在测试集上可以达到98%的准确率。相比之前的Softmax，我们的误差率 由8%下降到2%,对识别银行账单这种精确度要求很高的场景，可以说是飞跃性的提高。 而这个提升仅靠增加一个隐含层就实现了，可见多层神经网络的效果有多显著。当然，其 中我们也使用了一些Trick进行辅助，比如Dropout、Adagrad、ReLU等，但是起决定性 作用的还是隐含层本身，它能对特征进行抽象和转化。

没有隐含层的Softmax Regression只能直接从图像的像素点推断是哪个数字，而没有 特征抽象的过程。多层神经网络依靠隐含层，则可以组合出高阶特征，比如横线、竖线、 圆圈等,之后可以将这些高阶特征或者说组件再组合成数字,就能实现精准的匹配和分类。 隐含层输出的高阶特征(组件)经常是可以复用的，所以每一类的判别、概率输出都共享

这些高阶特征，而不是各自连接独立的高阶特征。

同时我们可以发现，新加了一个隐含层，并使用了 Dropout、Adagrad和ReLU,而代 码没有增加很多，这就是TensorFlow的优势之一。它的代码非常简洁，没有太多的冗余， 可以方便地将有用的模块拼装在一起。

总结一下，本节我们介绍了如何实现包含一个隐含层的MLP,对于有更多个隐含层 的MLP,读者可以如法炮制。我们讲解了 Dropout、Adagrad、ReLU的原理和作用，以 及如何在TensorFlow中使用它们。

不过，使用全连接神经网络（Fully Connected Network, FCN, MLP的另一种说法） 也是有局限的，即使我们使用很深的网络、很多的隐藏节点、很大的迭代轮数，也很难在 MNIST数据集上达到99%以上的准确率。因此第5章我们将介绍卷积神经网络，以及如 何在MNIST数据集上使用CNN达到99%以上的准确率，真正满足识别银行支票这种高 精度系统的需求。
