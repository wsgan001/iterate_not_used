### TensorFlow实现卷积 神经网络



##### 5.1卷积神经网络简介

卷积神经网络(Convolutional Neural Network, CNN )最初是为解决图像识别等问 题设计的，当然其现在的应用不仅限于图像和视频，也可用于时间序列信号，比如音频信 号、文本数据等。在早期的图像识别研究中，最大的挑战是如何组织特征，因为图像数据 不像其他类型的数据那样可以通过人工理解来提取特征。在股票预测等模型中，我们可以 从原始数据中提取过往的交易价格波动、市盈率、市净率、盈利增长等金融因子胃，这即是 特征工程。但是在图像中，我们很难根据人为理解提取出有效而丰富的特征。在深度学习 出现之前，我们必须借助SIFT, HoG等算法提取具有良好区分性的特征，再集合SVM 等机器学习算法进行图像识别。如图5-1所示，SIFT对一定程度内的缩放、平移、旋转、 视角改变、亮度调整等畸变，都具有不变性，是当时最重要的图像特征提取方法之一。可 以说，在之前只能依靠SIFT等特征提取算法才能勉强进行可靠的图像识别。

eHnnaa^sAn.7^

eaaaan r y K■諷■輟 M •■ - +，



Spin image



![img](06TensorFlow9e18_c4875a088c74095baibbt-54.jpg)



RIFT



---------二-—



GLOH



Textons



图5-1 SIFT、HoG等图像特征提取方法

然而SIFT这类算法提取的特征还是有局限性的，在ImageNet ILSVRC比赛的最好 结果的错误率也有26%以上，而且常年难以产生突破。卷积神经网络提取的特征则可以 达到更好的效果，同时它不需要将特征提取和分类训练两个过程分开，它在训练时就自动 提取了最有效的特征。CNN作为一个深度学习架构被提出的最初诉求，是降低对图像数 据预处理的要求，以及避免复杂的特征工程。CNN可以直接使用图像的原始像素作为输 入，而不必先使用SIFT等算法提取特征，减轻了使用传统算法如SVM时必需要做的大 量重复、烦琐的数据预处理工作。和SIFT等算法类似，CNN训练的模型同样对缩放、平 移、旋转等畸变具有不变性，有着很强的泛化性。CNN的最大特点在于卷积的权值共享 结构，可以大幅减少神经网络的参数量，防止过拟合的同时又降低了神经网络模型的复杂 度。CNN的权值共享其实也很像早期的延时神经网络(TDNN),只不过后者是在时间这 一个维度上进行权值共享，降低了学习时间序列信号的复杂度。

卷积神经网络的概念最早出自19世纪60年代科学家提出的感受野(Receptive Field37)。当时科学家通过对猫的视觉皮层细胞研究发现，每一个视觉神经元只会处理一 小块区域的视觉图像，即感受野。到了 20世纪80年代，日本科学家提出神经认知机 (Neocognitron38)的概念，可以算作是卷积网络最初的实现原型。神经认知机中包含两类 神经元，用来抽取特征的S-cells,还有用来抗形变的C-cells,其中S-cells对应我们现在 主流卷积神经网络中的卷积核滤波操作，而C-cells则对应激活函数、最大池化 (Max-Pooling)等操作。同时，CNN也是首个成功地进行多层训练的网络结构，即前面 章节提到的LeCun的LeNet539,而全连接的网络因为参数过多及梯度弥散等问题，在早 期很难顺利地进行多层的训练。卷积神经网络可以利用空间结构关系减少需要学习的参数

TensorFlow 实战

量，从而提高反向传播算法的训练效率。在卷积神经网络中，第一个卷积层会直接接受图 像像素级的输入，每一个卷积操作只处理一小块图像,进行卷积变化后再传到后面的网络， 每一层卷积（也可以说是滤波器）都会提取数据中最有效的特征。这种方法可以提取到图 像中最基础的特征，比如不同方向的边或者拐角，而后再进行组合和抽象形成更高阶的特 征，因此CNN可以应对各种情况，理论上具有对图像缩放、平移和旋转的不变性。

一般的卷积神经网络由多个卷积层构成，每个卷积层中通常会进行如下几个操作。

（1）    图像通过多个不同的卷积核的滤波，并加偏置（bias）,提取出局部特征，毎一 个卷积核会映射出一个新的2D图像。

（2）    将前面卷积核的滤波输出结果，进行非线性的激活函数处理。目前最常见的是使 用ReLU函数，而以前Sigmoid函数用得比较多。

（3）    对激活函数的结果再进行池化操作（即降采样，比如将2x2的图片降为lxl的 图片），目前一般是使用最大池化，保留最显著的特征，并提升模型的畸变容忍能力。

这几个步骤就构成了最常见的卷积层，当然也可以再加上一个LRN40（ Local Response Normalization,局部响应归一化层）层，目前非常流行的Trick还有Batch Normalization 等。

一个卷积层中可以有多个不同的卷积核，而每一个卷积核都对应一个滤波后映射出的 新图像,同一个新图像中每一个像素都来自完全相同的卷积核，这就是卷积核的权值共享。 那我们为什么要共享卷积核的权值参数呢？答案很简单，降低模型复杂度，减轻过拟合并 降低计算量。举个例子，如图5-2所示，如果我们的图像尺寸是1000像素xlOOO像素， 并且假定是黑白图像，即只有一个颜色通道，那么一张图片就有100万个像素点，输入数 据的维度也是100万。接下来，如果连接一个相同大小的隐含层（100万个隐含节点）， 那么将产生100万xl00万=一万亿个连接。仅仅一个全连接息.Fully Connected' Layer）, 就有一万亿连接的权重要去训练，这已经超出了普通硬件的计算能力。我们必须减少需要 训练的权重数量，一是降低计算的复杂度，二是过多的连接会导致严重的过拟合，减少连 接数可以提升模型的泛化性。

图像在空间上是有组织结构的，每一个像素点在空间上和周围的像素点实际上是有紧 密联系的，但是和太遥远的像素点就不一定有什么关联了。这就是前面提到的人的视觉感 受野的概念，每一个感受野只接受一小块区域的信号。这一小块区域内的像素是互相关联

的，每一个神经元不需要接收全部像素点的信息，只需要接收局部的像素点作为输入，而 后将所有这些神经元收到的局部信息综合起来就可以得到全局的信息。这样就可以将之前 的全连接的模式修改为局部连接，之前隐含层的每一个隐含节点都和全部像素相连，现在 我们只需要将每一个隐含节点连接到局部的像素节点。假设局部感受野大小是10x10,即 每个隐含节点只与10x10个像素点相连，那么现在就只需要10x10x100万=1亿个连接， 相比之前的1万亿缩小了 10000倍。

fully connected neural net locally connected neural net

图5-2全连接（左）和局部连接（右）

上面我们通过局咅P连接（Locally Connect）的方法，将连接数从1万亿降低到1亿, 但仍然偏多，需要继续降低参数量。现在隐含层每一个节点都与10x10的像素相连，也 就是每一个隐含节点都拥有100个参数。假设我们的局部连接方式是卷积操作，即默认每 一个隐含节点的参数都完全一样,那我们的参数不再是1亿，而是100。不论图像有多大， 都是这10x10=100个参数，即卷积核的尺寸，这就是卷积对缩小参数量的贡献。我们不 需要再担心有多少隐含节点或者图片有多大，参数量只跟卷积核的大小有关，这也就是所 谓的权值共享。但是如果我们只有一个卷积核，我们就只能提取一种卷积核滤波的结果， 即只能提取一种图片特征，这不是我们期望的结果。好在图像中最基本的特征很少，我们 可以增加卷积核的数量来多提取一些特征。图像中的基本特征无非就是点和边，无论多么 复杂的图像都是点和边组合而成的。人眼识别物体的方式也是从点和边开始的，视觉神经 元接受光信号后，每一个神经元只接受一个区域的信号，并提取出点和边的特征，然后将 点和边的信号传递给后面一层的神经元，再接着组合成高阶特征，比如三角形、正方形、 直线、拐角等，再继续抽象组合，得到眼睛、鼻子和嘴等五官，最后再将五官组合成一张 脸，完成匹配识别。因此我们的问题就很好解决了，只要我们提供的卷积核数量足够多， 能提取出各种方向的边或各种形态的点，就可以让卷积层抽象出有效而丰富的高阶特征。 每一个卷积核滤波得到的图像就是一类特征的映射，即一个Feature Map。一般来说，我 们使用100个卷积核放在第一个卷积层就已经很充足了。那这样的话，如图5-3所示，我

TensorFlow 实战

们的参数量就是100x100=1万个，相比之前的1亿又缩小了 10000倍。因此，依靠卷积， 我们就可以高效地训练局部连接的神经网络了。卷积的好处是，不管图片尺寸如何，我们 需要训练的权值数量只跟卷积核大小、卷积核数量有关，我们可以使用非常少的参数量处 理任意大小的图片。每一个卷积层提取的特征，在后面的层中都会抽象组合成更高阶的特 征。而且多层抽象的卷积网络表达能力更强，效率更高，相比只使用一个隐含层提取全部 高阶特征，反而可以节省大量的参数。当然，我们需要注意的是，虽然需要训练的参数量 下降了’但是隐含节点的数量并没有下降，隐含节点的数量只跟卷积的步长有关。如果步 长为1,那么隐含节点的数量和输入的图像像素数量一致；如果步长为5,那么每5x5的 像素才需要一个隐含节点，我们隐含节点的数量就是输入像素数量的1/25。

LOCALLY CONNECTED NEURAL NET    CONVOLUTIONAL NET

图5-3局部连接(左)和卷积操作(右)

我们再总结一下，卷积神经网络的要点就是局部连接(Local Connection)、权值共享 (Weight Sharing)和池化层(Pooling)中的降采样(Down-Sampling)。其中，局部连接 和权值共享降低了参数量，使训练复杂度大大下降，并减轻了过拟合。同时权值共享还赋 予了卷积网络对平移的容忍性，而池化层降采样则进一步降低了输出参数量，并赋予模型 对轻度形变的容忍性，提高了模型的泛化能力。卷积神经网络相比传统的机器学习算法， 无须手工提取特征，也不需要使用诸如SIFT之类的特征提取算法，可以在训练中自动完 成特征的提取和抽象，并同时进行模式分类，大大降低了应用图像识别的难度；相比一般 的神经网络，CNN在结构上和图片的空间结构更为贴近，都是2D的有联系的结构，并 且CNN的卷积连接方式和人的视觉神经处理光信号的方式类似。

大名鼎鼎的LeNet5诞生于1994年，是最早的深层卷积神经网络之一，并且推动了 深度学习的发展。从1988年开始，在多次成功的迭代后，这项由Yam LeCim完成的开 拓性成果被命名为LeNet5o LeCun认为，可训练参数的卷积层是一种用少量参数在图像 的多个位置上提取相似特征的有效方式,这和直接把毎个像素作为多层神经网络的输入不

同。像素不应该被使用在输入层，因为图像具有很强的空间相关性，而使用图像中独立的 像素直接作为输入则利用不到这些相关性。

LeNet5当时的特性有如下几点。

•每个卷积层包含三个部分：卷积、池化和非线性激活函数 •使用卷积提取空间特征

•降采样(Subsample )的平均池化层(Average Pooling )

•双曲正切(Tanh )或S型(Sigmoid )的激活函数 • MLP作为最后的分类器

•层与层之间的稀疏连接减少计算复杂度

LeNet5中的诸多特性现在依然在state-of-the-art卷积神经网络中使用，可以说LeNet5 是奠定了现代卷积神经网络的基石之作。Lenet-5的结构如图5-4所示。它的输入图像为 32x32的灰度值图像，后面有三个卷积层，一个全连接层和一个高斯连接层。它的第一个 卷积层C1包含6个卷积核，卷积核尺寸为5x5,即总共(5。5+1) ><6=156个参数，括号 中的1代表1个bias,后面是一个2x2的平均池化层S2用来进行降采样，再之后是一个 Sigmoid激活函数用来进行非线性处理。而后是第二个卷积层C3，同样卷积核尺寸是5。5， 这里使用了 16个卷积核，对应16个Feature Map。需要注意的是，这里的16个Feature Map不是全部连接到前面的6个Feature Map的输出的，有些只连接了其中的几个Feature Map,这样增加了模型的多样性。下面的第二个池化层S4和第一个池化层S2—致，都是 2x2的降采样。接下来的第三个卷积层C5有120个卷积核，卷积大小同样为5x5,因为 输入图像的大小刚好也是5x5,因此构成了全连接，也可以算作全连接层。F6层是一个 全连接层，拥有84个隐含节点，激活函数为Sigmoid。LeNet-5最后一层由欧式径向基函 数(Euclidean Radial Basis Function )单元组成，它输出最后的分类结果。

C3 f maps IfrglOxlO

C1- (sature n 6^28x28

16@5x5

layer OUTPUT 10

Convolutions

Gaussian connections Fun connection

Subsampling Convolutions

图5-4 LeNet-5结构示意图



S2 f nuos

TensorFlow 实战

##### 5.2 TensorFlow实现简单的卷积网络

本节将讲解如何使用TensorFlow实现一个简单的卷积神经网络，使用的数据集依然 是MNIST,预期可以达到99.2%左右的准确率。本节将使用两个卷积层加一个全连接层 构建一个简单但是非常有代表性的卷积神经网络,读者应该能通过这个例子掌握设计卷积 神经网络的要点。

首先载入MNIST数据集，并创建默认的Interactive Session。本节代码主要来自 TensorFlow的开源实现41。

from tensorflow.examples.tutorials.mnist import input_data

import tensorflow as tf

mnist = input_data.read一data_sets("MNIST_data/", one_hot=True)

sess = tf.InteractiveSession()

接下来要实现的这个卷积神经网络会有很多的权重和偏置需要创建,因此我们先定义 好初始化函数以便重复使用。我们需要给权重制造一些随机的噪声来打破完全对称，比如 截断的正态分布噪声，标准差设为0.1。同时因为我们使用ReLU,也给偏置增加一些小 的正值(0.1 )用来避免死亡节点(dead neurons)。

def weight_variable(shape):

initial = tf.truncated一normal(shape_, stddev=0.1) return tf.Variable(initial)

def bias_variable(shape):

initial = tf.constant(0.1j shape=shape) return tf.Variable(initial)

卷积层、池化层也是接下来要重复使用的，因此也为他们分别定义创建函数。这里的 tf.nn.conv2d是TensorFlow中的2维卷积函数，参数中x是输入，W是卷积的参数，比如 [5,5,1,32]：前面两个数字代表卷积核的尺寸；第三个数字代表有多少个channel。因为我 们只有灰度单色，所以是1，如果是彩色的RGB图片，这里应该是3。最后一个数字代 表卷积核的数量，也就是这个卷积层会提取多少类的特征。Strides代表卷积模板移动的 步长，都是1代表会不遗漏地划过图片的每一个点。Padding代表边界的处理方式，这里 的SAME代表给边界加上Padding让卷积的输出和输入保持同样(SAME)的尺寸。

tf.nn.max_pool是TensorFlow中的最大池化函数，我们这里使用2><2的最大池化，即将一 个2x2的像素块降为1x1的像素。最大池化会保留原始像素块中灰度值最高的那一个像 素，即保留最显著的特征。因为希望整体上缩小图片尺寸，因此池化层的strides也设为 横竖两个方向以2为步长。如果步长还是1，那么我们会得到一个尺寸不变的图片。

def conv2d(x_, W):

return tf.nn.conv2d(x, W, strides=[l, 1) 1』1]』padding='SAME')

def max_pool_2x2(x):

return tf.nn.max_pool(x^ ksize=[lJ 2, 2, 1]? strides=[l_, 2』2_, 1], padding='SAME,)

在正式设计卷积神经网络的结构之前，先定义输入的placeholder, x是特征，y_是真 实的label。因为卷积神经网络会利用到空间结构信息，因此需要将1D的输入向量转为 2D的图片结构，即从1x784的形式转为原始的28x28的结构。同时因为只有一个颜色通 道，故最终尺寸为[-1，28,28,1],前面的-1代表样本数量不固定，最后的1代表颜色通道数 量。这里我们使用的tensor变形函数是tf.reshape。

x = tf.placeholder(tf.float32j [None) 784])

y_ = tf. placeholder(tf .float32j [None_, 10])

x_image = tf.reshape(x_, [-1j28j28j1])

接下来定义我们的第一个卷积层。我们先使用前面写好的函数进行参数初始化，包括 weights和bias，这里的[5,5，1，32]代表卷积核尺寸为5x5，1个颜色通道，32个不同的卷积 核。然后使用corned函数进行卷积操作，并加上偏置，接着再使用ReLU激活函数进行 非线性处理。最后，使用最大池化函数max」xx)l_2x2对卷积的输出结果进行池化操作。

W_convl = weight_variable([5^ 5j 13 32])

b_convl = bias_variable([32])

h_convl = tf. nn. relu(conv2d(x__image4 W_convl) + b_convl)

h_pooll = max_pool_2x2(h_convl)

现在定义第二个卷积层，这个卷积层基本和第一个卷积层一样，唯一的不同是，卷积 核的数量变成了 64,也就是说这一层的卷积会提取64种特征。

W_conv2 = weight_variable([5^ 5j 323 64])

b_conv2 = bias_variable([64])

h_conv2 = tf.nn.relu(conv2d(h_poollj W_conv2) + b_conv2)

h_pool2 = max_pool_2x2(h_conv2)

因为前面经历了两次步长为2x2的最大池化，所以边长已经只有1/4 了，图片尺寸由 28x28变成了 7x7。而第二个卷积层的卷积核数量为64,其输出的tensor尺寸即为7x7x64。 我们使用tf.reshape函数对第二个卷积层的输出tensoi•进行变形：将其转成1D的向量， 然后连接一个全连接层，隐含节点为1024,并使用ReLU激活函数。

W_fcl = weight_variable([7 * 7 * 64， 1024])

b_fcl = bias_variable([1024])

h_pool2_flat = tf.reshape(h_pool2j [-1， 7*7*64])

h_fcl = tf.nn.relu(tf.matmul(h_pool2_flat3 W_fcl) + b_fcl)

为了减轻过拟合，下面使用一个Dropout层，Dropout的用法第4章已经讲过，是通 过一个placeholder传入keepjwob比率来控制的。在训练时，我们随机丢弃一部分节点的 数据来减轻过拟合，预测时则保留全部数据来追求最好的预测性能。

keep_prob = tf.placeholder(tf.float32)

h_fcl_drop = tf .nn. dropout (h_f cl keep_prob)

最后我们将Dropout层的输出连接一个Softmax层，得到最后的概率输出。

W_fc2 = weight_variable([1024, 10])

b_fc2 = bias_variable([10])

y_conv=tf.nn.softmax(tf.matmul(h_fcl_drop^ W_fc2) + b_fc2)

我们定义损失函数为cross entropy,和之前一样，但是优化器使用Adam,并给予一 个比较小的学习速率le-4。

cross_entropy = tf.reduce_mean(-tf.reduce_sum(y_ * tf.log(y_conv),

reduction_indices=[l]))

train_step = tf.train.Adam0ptimizer(le-4).minimize(cross_entropy)

再继续定义评测准确率的操作，这里和第3章、第4章一样。

correct_prediction = tf.equal(tf.argmax(y_conVj 1)., tf.argmax(y_4l)) accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

下面开始训练过程。首先依然是初始化所有参数，设置训练时Dropout的keeP_pr0b 比率为0.5。然后使用大小为50的mini-batch,共进行20000次训练迭代，参与训练的样 本数量总共为100万。其中每100次训练，我们会对准确率进行一次评测(评测时keep_prOb 设为1 ),用以实时监测模型的性能。

tf.global_variables_initializer().run()

for i in range(20000):

batch = mnist.train.next_batch(50) if i%100 == 0:

train_accuracy = accuracy.eval(feed_dict={x:batch[0]y_: batch[l]?

keep_prob: 1.0})

pnint("step %d, training accuracy    train_accuracy))

train_step.run(feed_dict={x: batch[0], y_: batch[l]keep_prob: 0.5})

全部训练完成后，我们在最终的测试集上进行全面的测试，得到整体的分类准确率。

print("test accuracy %g"%accuracy.eval(feed_dict={

x: mnist.test.images4 y_: mnist.test.labelskeep_prob: 1.0}))

最后，这个CNN模型可以得到的准确率约为99.2%,基本可以满足对手写数字识别 准确率的要求。相比之前MLP的2%错误率，CNN的错误率下降了大约60%。这其中主 要的性能提升者睐自于更优秀的网络设计，即卷积网络对图像特征的提取和抽象能力。依 靠卷积核的权值共享，CNN的参数量并没有爆炸，降低计算量的同时也减轻了过拟合， 因此整个模型的性能有较大的提升。本节我们只实现了一个简单的卷积神经网络，没有复 杂的Trick。接下来，我们将实现一个稍微复杂一些的卷积网络，而简单的MNIST数据集 已经不适合用来评测其性能，我们将使用CIFAR-1042数据集进行训练，这也是深度学习 可以大幅领先其他模型的一个数据集。

83



##### 5.3 TensorFlow实现进阶的卷积网络

本节使用的数据集是CIFAR-10,这是一个经典的数据集，包含60000张32x32的彩 色图像，其中训练集50000张，测试集10000张。CIFAR-10如同其名字，一共标注为10 类，每一类图片 6000 张。这 10 类分别是 airplane、automobile、bird、cat、deer、dog、frog、 horse、ship和truck，其中没有任何重叠的情况，比如automobile只包括小型汽车，truck

—r TensorFlow 实战

只包括卡车，也不会在一张图片中同时出现两类物体。它还有一个兄弟版本CIFAR-100, 其中标注了 100类。这两个数据集是前面章节提到的深度学习之父Geoffrey Hinton和他 的两名学生 Alex Krizhevsky 和 Vinod Nair 收集的，图片来源于 80 million tiny images43 这个数据集，Hinton等人对其进行了筛选和标注。CIFAR-10数据集非常通用，经常出现 在各大会议的论文中用来进行性能对比，也曾出现在Kaggle竞赛而为大家所知。图5-5 所示为这个数据集的一些示例。

3.5%的错误率了，但是需要训练很久，即使在GPU上也需要十几个小时。CIFAR-10数

据集上详细的 Benchmark 和排名在 classification datasets results 上(<http://rodrigob.github>. io/are_we_there_yet/build/classification_datasets_results.html )□据深度学习三巨头之一 LeCun 说，现有的卷积神经网络已经可以对CIFAR-10进行很好的学习，这个数据集的问题已经 解决了。本节中实现的卷积神经网络没有那么复杂(根据Alex描述的cuda-convnet模型 做了些许修改得到)，在只使用3000个batch (每个batch包含128个样本)时，可以达 到73%左右的正确率。模型在GTX 1080单显卡上大概只需要几十秒的训练时间，如果 在CPU上训练则会慢很多。如果使用100k个batch,并结合学习速度的decay (即每隔一 段时间将学习速率下降一个比率)，正确率最高可以到86%左右。模型中需要训练的参数 约为100万个，而预测时需要进行的四则运算总量在2000万次左右。在这个卷积神经网 络模型中，我们使用了一些新的技巧。

(1 )对weights进行了 L2的正则化。

(2 )如图5-6所示，我们对图片进行了翻转、随机剪切等数据增强，制造了更多样本。

(3)在每个卷积-最大池化层后面使用了 LRN层，増强了模型的泛化能力。

Data Augmentation:

a. No augmentation' = 1 h'age)

b. Flip augmentation (= 2 smoges：

图5-6数据增强示例(水平翻转，随机裁切)

我们首先下载TensorFlow Models库，以便使用其中提供CIFAR-10数据的类。

git clone <https://github.com/tensorflow/models.git>

cd models/tutorials/image/cifarlO

TensorFlow 实跋

然后我们载入一些常用库，比如NumPy和time,并载入TensorFlow Models中自动 下载、读取CIFAR-10数据的类。本节代码主要来自TensorFlow的开源实现'

import cifarlO^ cifarl0_input

import tensorflow as tf

import numpy as np

import time

接着定义batch_size、训练轮数max_steps，以及下载CIFAR-10数据的默认路径。

max_steps = 3000

batch_size = 128

data_dir = '/tmp/cifarl0_data/cifap-10-batches-bin'

这里定义初始化weight的函数，和之前一样依然使用tf.truncated_normal截断的正态 分布来初始化权重。但是这里会给weight加一个L2的loss,相当于做了一个L2的正则 化处理。在机器学习中，不管是分类还是回归任务，都可能因特征过多而导致过拟合，一 般可以通过减少特征或者惩罚不重要特征的权重来缓解这个问题。但是通常我们并不知道 该惩罚哪些特征的权重，而正则化就是帮助我们惩罚特征权重的，即特征的权重也会成为 模型的损失函数的一部分。可以理解为，为了使用某个特征，我们需要付出loss的代价， 除非这个特征非常有效，否则就会被loss上的增加覆盖效果。这样我们就可以筛选出最 有效的特征，减少特征权重防止过拟合。这也即是奥卡姆剃刀法则，越简单的东西越有效。 一般来说，L1正则会制造稀疏的特征，大部分无用特征的权重会被置为0,而L2正则会 让特征的权重不过大，使得特征的权重比较平均。我们使用wl控制L2 loss的大小，使 用 tf.nn.l2_loss 函数计算 weight 的 L2 loss,再使用 tf.multiply 让L2 loss 乘以 wl,得到最 后的weight loss。接着,我们使用tf.add_to_collection把weight loss统一存到一个collection, 这个collection名为“losses”，它会在后面计算神经网络的总体loss时被用上。’

def variable_with_weight_loss(shape^ stddev? wl):

var = tf.Variable(tf.truncated_normal(shape? stddev=stddev)) if wl is not None:

weight_loss = tf .multiply (tf.nn.l2_loss (var )^1^3016='weight_loss') tf.add_to_collection('losses * weight_loss)

return var

下面使用cifarlO类下载数据集，并解压、展开到其默认位置。

cifarlO.maybe_download_and_extract()

再使用cifarlO_input类中的distorted_inputs函数产生训练需要使用的数据，包括特征 及其对应的label，这里返回的是已经封装好的tensor,每次执行者P会生成一个batch_size 的数量的样本。需要注意的是我们对数据进行了 Data Augmentation (数据增强)。具体的 实现细节，读者可以查看cifarlO_input.distorted_inputs函数，其中的数据增强操作包括随 机的水平翻转(tf.image.random_flip_left_right )、随机剪切一块24x24大小的图片 (tf.random_crop )、设置随机的亮度和对比度(tf.image.random_brightness、 tf.image.random_contrast),以及对数据进行标准化 tf.image.per_image_whitening (对数据 减去均值，除以方差，保证数据零均值，方差为1)。通过这些操作，我们可以获得更多 的样本(带噪声的)，原来的一张图片样本可以变为多张图片，相当于扩大样本量，对提 高准确率非常有帮助。需要注意的是，我们对图像进行数据增强的操作需要耗费大量CPU 时间，因此distorted_inputs使用了 16个独立的线程来加速任务，函数内部会产生线程池， 在需要使用时会通过TensorFlow queue进行调度。

images一train, labels_train = cifarl0_input.distorted一inputs(

data_dir=data_dir4 batch_size=batch_size)

我们再使用cifarlO_input.inputs函数生成测试数据，这里不需要进行太多处理，不需 要对图片进行翻转或修改亮度、对比度，不过需要裁剪图片正中间的24x24大小的区块， 并进行数据标准化操作。

images_testj labels_test = cifarl0_input.inputs(eval_data=True,

data_dir=data_dir4

batch_size=batch_size)

这里创建输入数据的placeholder,包括特征和label。在设定placeholder的数据尺寸 时需要注意，因为batCh_siZe在之后定义网络结构时被用到了，所以数据尺寸中的第一个 值即样本条数需要被预先设定，而不能像以前一样可以设为None。而数据尺寸中的图片 尺寸为24x24,即是裁剪后的大小，而颜色通道数则设为3,代表图片是彩色有RGB三 条通道。

image_holder = tf.placeholder(tf.float32_, [batch_size, 24_, 24j 3])

label_holder = tf.placeholder(tf.int32, [batch_size])

做好了准备工作，接下来开始创建第一个卷积层。先使用之前写好的

TensorFlow 实战

variable_with_weight_loss函数创建卷积核的参数并进行初始化。第一个卷积层使用5*5 的卷积核大小，3个颜色通道，64个卷积核,同时设置weight初始化函数的标准差为0.05。 我们不对第一个卷积层的weight进行L2的正则，因此wl ( weight loss )这一项设为0。 下面使用tf.nn.conv2d函数对输入数据image_holder进行卷稠7喿作，这里的步长stride均 设为1,padding模式为SAME。把这层的bias全部初始化为0,再将卷积的结果加上bias, 最后使用一个ReLU激活函数进行非线性化。在ReLU激活函数之后，我们使用一个尺寸 为3x3且步长为2。2的最大池化层处理数据，注意这里最大池化的尺寸和步长不一致， 这样可以增加数据的丰富性。再之后，我们使用tf.nn.lm函数，即LRN对结果进行处理。 LRN最早见于Alex那篇用CNN参加ImageNet比赛的论文，Alex在论文中解释LRN层 模仿了生物神经系统的“侧抑制”机制，对局部神经元的活动创建竞争环境，使得其中响 应比较大的值变得相对更大，并抑制其他反馈较小的神经元，增强了模型的泛化能力。 Alex在ImageNet数据集上的实验表明，使用LRN后CNN在Topi的错误率可以降低1.4%， 因此在其经典的AlexNet中使用了 LRN层。LRN对ReLU这种没有上限边界的激活函数 会比较有用，因为它会从附近的多个卷积核的响应(Response)中挑选比较大的反馈，但 不适合Sigmoid这种有固定边界并且能抑制过大值的激活函数。

weightl = variable_with_weight_loss(shape=[5j 5_, 3, 64], stddev=5e-2j wl=0.0)

kernell = tf.nn.conv2d(image_holder，weightl, [1，1，1, 1]，padding='SAME*) biasl = tf.Variable(tf.constant(0.0^ shape=[64])) convl = tf.nn.relu(tf.nn.bias_add(kernell4 biasl))

pooll = tf.nn.max_pool(convlksize=[lj 3}    1], strides=[lj 23 2, 1]_,

padding='SAME *)

norml = tf.nn.lrn(pooll, 4, bias=1.0) alpha=0.001 / 9.0) beta=0.75)

现在来创建第二个卷积层，这里的步骤和第一步很像，区别如下。上一层的卷积核数 量为64 (即输出64个通道)，所以本层卷积核尺寸的第三个维度即输入的通道数也需要 调整为64;还有一个需要注意的地方是这里的bias值全部初始化为0.1，而不是0。最后， 我们调换了最大池化层和LRN层的顺序，先进行LRN层处理，再使用最大池化层。

weight2 = variable_with_weight_loss(shape=[5j 5, 64, 64]j stddev=5e-2_, wl=0.0)

kernel2 = tf.nn.conv2d(norml, weight2) [1, 1, 1, 1], padding=*SAME') bias2 = tf.Variable(tf.constant(0.1shape=[64]))

conv2 = tf.nn.relu(tf.nn.bias_add(kernel2j bias2))

norm2 = tf.nn.lrn(conv2_, 4) bias=1.0) alpha=0.001 / 9.0) beta=0.75) pool2 = tf.nn.max_pool(norm2J ksize=[l> 3, 3, 1]) strides=[lJ 2} 2, 1]_,

padding='SAME1)

在两个卷积层之后，将使用一个全连接层，这里需要先把前面两个卷积层的输出结果 全部flatten,使用tf.reshape函数将每个样本都变成一维向量。我们使用get_shape函数， 获取数据扁平化之后的长度。接着使用variable_with_weight_loss函数对全连接层的weight 进行初始化，这里隐含节点数为384,正态分布的标准差设为0.04, bias的值也初始化为 0.1。需要注意的是我们希望这个全连接层不要过拟合，因此设了一个非零的weight loss 值0.04，让这一层的所有参数都被L2正则所约束。最后我们依然使用ReLU激活函数进 行非线性化。

reshape = tf.reshape(pool2_, [batch_sizej -1])

dim = reshape.get_shape()[1].value

weight3 = variable_with_weight_loss(shape=[dim, 384], stddev=0.04) wl=0.004) bias3 = tf.Variable(tf.constant(0.1shape=[384])) local3 = tf.nn.relu(tf.matmul(reshapej weights) + bias3)

接下来的这个全连接层和前一层很像，只不过其隐含节点数下降了一半，只有192 个，其他的超参数保持不变。

weight4 = variable_with_weight_loss(shape=[384j 192]stddev=0.04J wl=0.004) bias4 = tf.Variable(tf.constant(0.1j shape=[192])) local4 = tf.nn.relu(tf,matmul(local3J weight4) + bias4)

下面是最后一层，依然先创建这一层的weight,其正态分布标准差设为上一个曝含层 的节点数的倒数，并且不计入L2的正则。需要注意的是，这里不像之前那样使用softmax 输出最后结果，这是因为我们把softmax的操作放在了计算loss的部分。我们不需要对 inference的输出进行softmax处理就可以获得最终分类结果(直接比较inference输出的各 类的数值大小即可)，计算softmax主要是为了计算loss,因此softmax操作整合到后面是 比较合适的。

weight5 = variable_with_weight_loss(shape=[192, 10], stddev=l/192.0_, wl=0.0) bias5 = tf.Variable(tf.constant(0.0shape=[10])) logits = tf.add(tf.matmul(local4, weight5)bias5)

TensorFlow 实战

到这里就完成了整个网络inference的部分。梳理整个网络结构可以得到表5-1。从上 到下，依次是整个卷积神经网络从输入到输出的流程。可以观察到，其实设计CNN主要 就是安排卷积层、池化层、全连接层的分布和顺序，以及其中超参数的设置、Trick的使 用等。设计性能良好的CNN是有一定规律可循的，但是想要针对某个问题设计最合适的 网络结构，是需要大量实践摸索的。

表5-1卷积神经网络结构表

| Layer名称 | 描                      | 述   |
| --------- | ----------------------- | ---- |
| convl     | 卷积层和ReLU激活函数    |      |
| pooll     | 最大池化                |      |
| norml     | LRN                     |      |
| conv2     | 卷积层和ReLU激活函数    |      |
| norm2     | LRN                     |      |
| pool2     | 最大池化                |      |
| local3    | 全连接层和ReLU激活函数  |      |
| local4    | 全连接层和ReLU激活函数  |      |
| logits    | 模型Inference的输出结果 |      |

完成了模型inference部分的构建，接下来计算CNN的loss。这里依然使用cross entropy,需要注意的是我们把softmax的计算和cross entropy loss的计算合在了一起，即 tf.nn.sparse_softmax_cross_entropy_with_logits0 这里使用 tf.reduce_mean 对 cross entropy 计算均值，再使用 tf.add_to_collection 把 cross entropy 的 loss 添加到整体 losses 的 collection 中。最后，使用tf.add_n将整体losses的collection中的全部loss求和，得到最终的loss, 其中包括cross entropy loss,还有后两个全连接层中weight的L2 loss。

def loss(logits, labels):

labels = tf. cast (labels tf .int64)

cross_entropy = tf.nn.sparse_softmax_cross_entpopy_with_logits(

logits=logitSj labels=labelSj name='cross_entropy_per_example')

cross_entropy_mean = tf.peduce_mean(cross_entropyj

name='cross_entropy ’)

tf.add_to_collection('losses'cross_entropy_mean)

return tf.add_n(tf.get_collection('losses')name='total_loss')

接着将logits节点和labeljplaceholder传入loss函数获得最终的loss。 loss = loss(logitSj label_holder)

优化器依然选择Adam Optimizer,学习速率设为le-3。 train一op = tf.train.Adam0ptimizer(le-3).minimize(loss)

使用tf.nn.in_top_k函数求输出结果中top k的准确率，默认使用top 1，也就是输出 分数最高的那一类的准确率。

top_k_op = tf.nn.in_top_k(logits? label_holder\ 1)

使用tf.InteractiveSession创建默认的session,接着初始化全部模型参数。

sess = tf.InteractiveSession()

tf.global_variables_initializer().run()

这一步是启动前面提到的图片数据增强的线程队列，这里一共使用了 16个线程来进 行加速。注意，如果这里不启动线程，那么后续的inference及训练的操作都是无法开始 的。

tf.train.start一queue_runners()

现在正式开始训练。在每一个step的训练过程中，我们需要先使用session的run方 法执行images_train、labels_train的计算，获得一个batch的训练数据，再将这个batch的 数据传入train_op和loss的计算。我们记录每一个step花费的时间，毎隔10个step会计 算并展示当前的loss、每秒钟能训练的样本数量，以及训练一个batch数据所花费的时间， 这样就可以比较方便地监控整个训练过程。在GTX 1080上，每秒钟可以训练大约1800 个样本，如果batch_size为128,则每个batch大约需要0.066s。损失loss在一开始大约 为4.6,在经过了 3000步训练后会下降到1.0附近。

for step in range(max_steps): start_time = time.time()

image_batchjlabel_batch = sess.run([images_train,labelsjtrain]) loss_value = sess.run([train_op, loss]

feed_dict={image_holder: image_batch, label_holder:label_batch}) duration = time.time() - start_time

—厂 TensorFlow 实战

if step % 10 == 0：

examples_per_sec = batch_size / duration sec_per_batch = float(duration)

format_str=(1 step %d,loss=%.2f (%.lf examples/sec; %.3f sec/batch)') print(format_str % (step,loss_value_,examples_per_sec_,sec_per_batch))

接下来评测模型在测试集上的准确率。测试集一共有10000个样本，但是需要注意的 是，我们依然要像训练时那样使用固定的batch_size,然后一个batch 一个batch地输入测 试数据。我们先计算一共要多少个batch才能将全部样本评测完。同时，在每一个step中 使用 session 的 run 方法获取 images_test、labels_test 的 batch,再执行 top_k_op 计算模型 在这个batch的top 1上预测正确的样本数。最后汇总所有预测正确的结果，求得全部测 试样本中预测正确的数量。

num_examples = 10000

import math

num_iter = int(math.ceil(num_examples / batch_size))

true_count =0

total_sample_count = num_iter * batch_size

step = 0

while step < num_iter:

image_batchjlabel_batch = sess.run([images_test_, labels一test]) predictions = sess.run([top_k_op]^feed_dict={image_holder: image_batch^

label_holder:label_batch})

true_count += np.sum(predictions) step += 1

最后将准确率的评测结果计算并打印出来。

precision = true_count / total_sample_count

print('precision @ 1 = %.3f' % precision)

最终，在CIFAR-10数据集上，通过一个短时间小迭代次数的训练，可以达到大致73% 的准确率。持续增加max_steps,可以期望准确率逐渐増加。如果max_StepS比较大，则 推荐使用学习速率衰减(decay )的SGD进行训练，这样训练过程中能达到的准确率峰值

会比较高，大致接近86%。而其中L2正则及LRN层的使用都对模型准确率有提升作用， 他们都可以从某些方面提升模型的泛化性。

数据增强(Data Augmentation )在我们的训练中作用很大，它可以给单幅图增加多 个副本，提高图片的利用率，防止对某一张图片结构的学习过拟合。这刚好是利用了图片 数据本身的性质，图片的冗余信息量比较大，因此可以制造不同的噪声并让图片依然可以 被识别出来。如果神经网络可以克服这些噪声并准确识别，那么它的泛化性必然会很好。 数据增强大大增加了样本量，而数据量的大小恰恰是深度学习最看重的，深度学习可以在 图像识别上领先其他算法的一大因素就是它对海量数据的利用效率非常高。用其他算法, 可能在数据量大到一定程度时，准确率就不再上升了，而深度学习只要提供足够多的样本, 准确率基本可以持续提升，所以说它是最适合大数据的算法。如图5-6所示，传统的机器 学习算法在获取了一定量的数据后，准确率上升曲线就接近瓶颈，而神经网络则可以持续 上升到更高的准确率才接近瓶颈。规模越大越复杂的神经网络模型，可以达到的准确率水 平越高，但是也相应地需要更多的数据才能训练好，在数据量小时反而容易过拟合。我们 可以看到Large NN在数据量小的时候，并不比常规算法好，直到数据量持续扩大才慢慢 超越了常规算法、Small NN和Medium NN,并在最后达到了一个非常高的准确率。根 据Alex在cuda-convnet上的测试结果，如果不对CIFAR-10数据使用数据增强，那么错 误率最低可以下降到17%；使用数据增强后，错误率可以下降到11%左右，模型性能的 提升非常显著O

TensorFlow 实战

从本章的例子中可以发现，卷积层一般需要和一个池化层连接，卷积加池化的组合目 前已经是做图像识别时的一个标准组件了。卷积网络最后的几个全连接层的作用是输出分 类结果，前面的卷积层主要做特征提取的工作，直到最后的全连接层才开始对特征进行组 合匹配，并进行分类。卷积层的训练相对于全连接层更复杂，训练全连接层基本是进行一 些矩阵乘法运算，而目前卷积层的训练基本依赖于cuDNN的实现（另有nervana公司的 neon也占有一席之地）。其中的算法相对复杂，有些方法（比如Facebook开源的算法） 还会涉及傅里叶变换。同时，卷积层的使用有很多Trick,除了本章提到的方法，实际上 有很多方法可以防止CNN过拟合，力卩快收敛速度或者提高泛化性，这些会在后续章节中 讲解。
