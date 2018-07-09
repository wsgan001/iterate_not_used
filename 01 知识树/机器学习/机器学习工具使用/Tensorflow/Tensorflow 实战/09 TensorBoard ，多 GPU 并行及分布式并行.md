![img](06TensorFlow9e18_c4875a088c74095baibbt-113.jpg)



多GPU



#### 并行及分布式并行

##### 9.1    Tensor 巳 oard

TensorBoard是TensorFlow官方推出的可视化工具，如图9-1所示，它可以将模型训 练过程中的各种汇总数据展示出来，包括标量（Scalars ）、图片（Images ）、音频（Audio \ 计算图（Graphs ）、数据分布（Distributions ）、直方陳 Histograms ）和嵌入向量（Embeddings ）。 我们在使用TensorFlow训练大型深度学习神经网络时，中间的计算过程可能非常复杂， 因此为了理解、调试和优化我们设计的网络，可以使用TensorBoard观察训练过程中的各 种可视化数据。如果要使用TensorBoard展示数据，我们需要在执行TensorFlow计'算图的 过程中，将各种类型的数据汇总并记录到日志文件中。然后使用TensorBoard读取这些日 志文件，解析数据并生成数据可视化的Web页面，让我们可以在浏览器中观察各种汇总 数据。下面我们将通过一个简单的MNIST手写数字识别的例子，讲解各种类型数据的汇 总和展示的方法。

Write a regex to creaR a tag group    X

accuracy—，

cross_entropy_1



[| Spirt on underscores



I j Data download links

Toohip sorting method: default



Smoothing



Horizontal Axis



cross_entropy_l

ax

2^

20；

CCCO 2CC.C -<33.0 6000 320 0 ：.0Xk



dropout

图9-1 TensorBoard-基于Web的TensorFlow数据可视化工具

我们首先载入TensorFlow，并设置训练的最大步数为1000,学习速率为0.001, dropout 的保留比率为0.9。同时，设置MNIST数据的下载地址data_dir和汇总数据的日志存放路 径log_dirQ这里的日志路径log_dir非常重要，会存放所有汇总数据供TensorBoard展示。 本节代码主要来自TensorFlow的开源实现67。

import tensorflow as tf

from tensorflow.examples.tutorials.mnist import input_data

max_steps=1000

learning_rate=0.001

dropout=0.9

data_dip=*/tmp/tensor千low/mnist/input_data1

log_dir='/tmp/tensorflow/mnist/logs/mnist_with_summaries1

我们使用input_data.read_data_sets下载MNIST数据，并创建TensorFlow的默认 Session。

mnist = input_data.read_data_sets(data_dirjOne_hot=True)

sess = tf.InteractiveSession()

为了在TensorBoard中展示节点名称，我们设计网络时会经常使用with tf.name_scope 限定命名空间，在这个with下的所有节点都会被自动命名为input/xxx这样的格式。下面 定义输入x和y的placeholder,并将输入的一维数据变形为28x28的图片储存到另一个

tensor,这样就可以使用tf.summary.image将图片数据汇总给TensorBoard展示了。 with tf.name一scope(1 input’)：

x = tf.placeholder(tf.float32? [None, 784]j name='x-input') y_ = tf .placeholder (tf.float32 [None, 10] namely-input')

with tf.name_scope('input_reshape'):

image_shaped_input = tf.reshape(x_, [-1} 2S} 28} 1]) tf .summary, image ('input1 image_shaped_input^ 10)

同时，定义神经网络模型参数的初始化方法，权重依然使用我们常用的 truncated_normal进行初始化，偏置则赋值为0.10

def weight_variable(shape):

initial = tf.truncated_normal(shapestddev=0.1) return tf.Variable(initial)

def bias_variable(shape):

initial = tf.constant(0.1, shape=shape) return tf.Variable(initial)

再定义对Variable变量的数据汇总函数，我们计算出Variable的mean、stddev、max 和min ,对这些标量数据使用tf.summary.scalar进行记录和汇总。同时，使用 tf.summary.histogram直接记录变量var的直方图数据。

def variable_summaries(var):

with tf.name_scope(1 summaries'):

mean = tf.reduce_mean(var) tf.summary.scalar('mean*mean) with tf.name_scope(1stddev*):

stddev = tf.sqrt(tf.reduce_mean(tf.square(var - mean))) tf. summary .scalar (* stddev、stddev) tf. summary, scalar (’max、tf. reduce_max(var)) tf.summary.scalar('min、 tf.reduce_min(var)) tf. summary. histogram(' histogram、var)

然后我们设计一个MLP多层神经网络来训练数据，在每一层中都会对模型参数进行 数据汇总。因此，我们定义创建一层神经网络并进行数据汇总的函数nnjayer。这个函数 的输入参数有输入数据input_tensor、输入的维度input_dim、输出的维度output_dim和层 名称layer_name,激活函数act则默认使用ReLU。在函数内，先是初始化这层神经网络 的权重和偏重，并使用前面定义的variable_summaries对variable进行数据汇总。然后对 输入做矩阵乘法并加偏置，再将未进行激活的结果使用tf.summary.histogram统计直方图。 同时，在使用激活函数后，再使用tf.summary.histogram统计一次。

def nn_layer(input_tensorj input_dim_, output_dimj layer_name, act=tf.nn.relu):

with tf.name_scope(layer_name): with tf.name_scope(* weights1):

weights = weight_variable([input一dim, output_dim]) variable_summaries(weights)

with tf.name_scope(* biases *):

biases = bias_variable([output_dim]) variable_summaries(biases)

with tf.name_scope(*Wx_plus_b'):

preactivate = tf.matmul(input_tensorj weights) + biases tf.summary.histogram(1pre_activations'preactivate)

activations = act(preactivate^ name='activation’) tf.summary.histogram('activations'activations) return activations

我们使用刚刚定义好的nnjayer创建一层神经网络，输入维度是图片的尺寸 ( 784=28x28 )，输出的维度是隐藏节点数500。再创建一个Dropout层，•并使用 tf.summary.scalar记录keep_prob。然后再使用nnjayer定义神经网络的输出层，其输入维 度为上一层的隐含节点数500,输出维度为类别数10,同时激活函数为全等映射identity, 即暂不使用Softmax,在后面会处理。

hiddenl = nn_layer(x, 784, 500, ’layerl’)

with tf.name_scope('dropout'):

keep_prob = tf.placeholder(tf.float32)

tf.summary.scalar(*dropout_keep_probability'7 keep_prob) dropped = tf.nn.dropout(hiddenl, keep_prob)

y = nn_layer(dropped500^ 10, 'layer2'j act=tf.identity)

这里使用 tf.nn.softmax_cross_entropy_with_logits()对前面输出层的结果进行 Softmax 处理并计算交叉熵损失cross_entropy。我们计算平均的损失，并使用tf.summary.scalar进 行统计汇总。

with tf.name_scope(* cross_entropy*):

diff = tf.nn.softmax_cross_entropy_with_logits(logits=ylabels=y_) with tf.name_scope('total'):

cross_entropy = tf.reduce_mean(diff) tf.summary.scalar(*cross_entropy*』cross_entropy)

下面使用Adma优化器对损失进行优化，同时统计预测正确的样本数并计算正确率 accuray,再使用 tf.summary.scalar 对 accuracy 进行统计汇总。 with tf.name_scope('train'):

train_step = tf.train.AdamOptimizer(learning_rate).minimize(cross_entropy) with tf.name_scope('accuracy'):

with tf.name_scope('correct_prediction'):

correct_prediction = tf.equal(tf.argmax(y, 1)tf.argmax(y_, 1))

with tf.name_scope('accuracy*):

accuracy = tf.reduce_mean(tf.cast(correct_prediction^ tf.float32)) tf. summary. scalar( 'accuracy、accuracy)

因为我们之前定义了非常多的tf.summary的汇总操作，逐一执行这些操作太麻烦， 所以这里使用tf.summary.merger_all()直接获取所有汇总操作，以便后面执行。然后，定义 两个tf.summary.FileWriter (文件记录器)在不同的子目录，分别用来存放训练和测试的 日志数据。同时，将Session的计算图sess.graph加入训练过程的记录器，这样在TensorBoard 的GRAPHS窗口中就能展示整个计算图的可视化效果。最后使用 tf.global_variables_initializer().run()初始化全部变量。

merged = tf.summary.merge_all()    -

train_writer = tf.summary.FileWriter(log_dir + '/train'sess.graph) test_writer = tf.summary.FileWriter(log_dir + '/test')

tf.global_variables_initializer().run()

接下来定义feed_dict的损失函数。该函数先判断训练标记，如果训练标记为True, 则从mnist.train中获取一个batch的样本，并设置dropout值；如果训练标记为False,则 获取测试数据，并设置keep」)rOb为1，即等于没有dropout效果。

def feed_dict(train): if train:

xsj ys = mnist.train.next_batch(100) k = dropout

else:

xSj ys = mnist.test.imagesmnist.test.labels k = 1.0

return {x: xs_, y_:    keep_prob: k}

最后一步，实际执行具体的训练、测试及日志记录的操作。首先使用tf.train.Saver() 创建模型的保存器。然后进入训练的循环中，每隔10步执行一次merged (数据汇总)、 accuracy (求测试集上的预测准确率)操作，并使用test_writer.add_sumamry将汇总结果 summary和循环步数i写入日志文件;同时每隔100步,使用tf.RunOptions定义TensorFlow 运行选项，其中设置 tracejevel 为 FULL_TRACE,并使用 tf.RunMetadata()定义 TensorFlow 运行的元信息，这样可以记录训练时运算时间和内存占用等方面的信息。再执行merged 数据汇总操作和train_step训练操作，将汇总结果summary和训练元信息run_metadata添 加到train_writer。平时，则只执行merged操作和train_step操作，并添加summary到 train_writer。所有训练全部结束后，关闭train_writer和test_writer。

saver = tf.train.Saver()    .    .    .

for i in range(max_steps):

if i % 10 == 0:

summary^ acc = sess.run([merged^ accuracy], feed_dict=feed_dict(False))

test_writer.add_summary(summaryJ i)

print("Accuracy at step %s: %s* % (ij acc))

else:

if i % 100 == 99:

run_options = tf.RunOptions(trace_level=tf.RunOptions.FULL_TRACE)

pun_metadata = tf.RunMetadata()

summary, _ = sess.run([mergedj train一step], feed_dict=feed_dict(True), options=run_optionSj run_metadata=run_metadata)

train_writer.add_run_metadata(run_metadataJ 'step%03d' % i) train_writer.add_summary(summaryj i) saver.save(sessj log_dir+"/model.ckpt"J i) print( 'Adding run metadata for、i)

else:

summaryj 一 = sess.run([merged, train_step]j feed_dict=feed_dict(True)) train_writer.add_summary(summary? i)

train_writer.close()

test_writer.close()

之后切换到Linux命令行下，.执行TensorBoard程序，并通过-logdir指定TensorFlow 日志路径，然后TensorBoard就可以自动生成所有汇总数据可视化的结果了。 tensorboard --logdir=/tmp/tensorflow/mnist/logs/mnist_with_summaries

执行上面的命令后，出现一条提示信息，复制其中的网址到浏览器，就可以看到数据 可视化的图表了。

Starting TensorBoard b'39* on port 6006

(You can navigate to <http://192.168.233.101:6006>)

首先打开标量SCALARS的窗口，并单击打开accuracy的图表，如图9-2所示。其中 可以看到两条曲线，分别是train和test中accuray随训练士数变化的趋势。我们可以调整 Smoothing参数，控制对曲线的平滑处理，数值越小越接近实际值，但波动较大；数值越 大则曲线越平缓。单击图表左下方的按钮，’可以放大这个图片，单击它右边的按鈕则可以 调整坐标轴的范围，以便更清楚地展示。

切换到图像IMAGES窗口，如图9-3所示，可以看到MNIST数据集中的图片。不只 是原始数据，所有在tf.sumamry.imageO中汇总的图片数据都可以在这里看到，包括进行 了各种光学畸变后的图片，或是神经网络的中间节点的输出。

C ① 192.168.233.101:6S<?G

Write a regex to create u tag group X

i~~) Split on underscores

门 Data download links

Tooltip sorting method: default -

accuracy」

accuracy.!



Smoothing

-• 0.6

Horizontal Axis

200.G ftC0.0 6CC.C SCC-G : COCK



Runs



Vff.ie & reoex io fiiic* roni



cross_entropy_1

dropout

layerl

Iayer2



图9-2 TensorBoard SCALARS变量展示效果



图9-3 TensorBoard IMAGES图片展示效果

进入计算图GRAPHS窗口，可以看到整个TensorFlow计算图的结构，如图9-4所示。 这里展示了网络forward的inference的流程，以及backward训练更新参数的流程。我们 在代码中创建的只有forward正向过程：input -> layerl dropout -> layer2 cross_entropy、accuracy的，而训练中backward的求解梯度、更新参数等操作是TensorFlow 帮我们自动创建的。图中实线代表数据上的依赖关系，虚线代表控制条件上的依赖关系。 单击某个节点的窗口，可以查看它的属性、输入及输出，并且可以看到输出tensor的尺寸。

我们也可以单击节点右上角的“+”号按钮，展开这个node的内部细节。例如，单击layer2 可以看到内部的weights、biases,矩阵乘法操作、向量加法操作，以及激活函数计算的操 作，这些操作都归属于tf.name_scope('layer2')这个命名空间(name scope)。所有在一个 命名空间中的节点都会被折叠在一起，在设计网络时，我们要尽可能精细地使用命名空间 对节点名称进彳亍规范，这样会展示出更清晰的结构。同时，在TensorBoard中，我们可以 右键单击一个节点并选择删除它，这不会真的在计算图中中删除它，但是可以简化我们的 视图，以便更好地观察网络结构。我们也可以切换配色风格，一种是基于结构的，相同的 结构的节点有一样的颜色；另一种是基于运算硬件的，在同一个运算硬件上的节点有一样 的颜色。同时，我们可以单击左边面板的Session runs,选择我们之前记录过run_metadata 的训练元信息，这样可以查看某轮迭代计算的时间消耗、内存占用等情况。

Main Graph



![img](06TensorFlow9e18_c4875a088c74095baibbt-120.jpg)



Fit lo screen

Download PNG

Run train    ■

⑴

Session _

runs(10)

Upload

Trace inputs S>

Color ® Stiuctyre O Device

coiws same sobsvucuxe (    ) iKwou* sobstnxtixe

pcsvffA



i inpuUesh...；

K

Graph r pxpa；xj»b»f) Nanwtpac**

OfiNod*

UnconrMjcted tvtts* Canmacwi tvrtts*

| Q    CoflM»nt                                      | 1 input 厂                       |
| -------------------------------------------------- | -------------------------------- |
| B5    SutrmaryDataflow «dQ«Cont'ol dependency edge |                                  |
| 图9-4                                              | TensorBoard GRAPHS计算图展示效果 |

切换到DISTRIBUTIONS窗口，如图9-5所示，可以查看之前记录的各个神经网络层 输出的分布，包括在激活函数前的结果及在激活函数后的结果。这样能观察到神经网络节 点的输出是否有效，会不会存在过多的被屏蔽的节点(dead neurons)。

![img](06TensorFlow9e18_c4875a088c74095baibbt-122.jpg)



也可以将DISTRIBUTIONS的图示结构转为直方图的形式。单击HISTOGRAMS窗 口，如图9-6所示，可以将每一步训练后的神经网络层的输出的分布以直方图的形式展示 出来。

Vi.-iic a rcgtx io crcste a tog group    X

门 Split on underscores



layer1/Wx_piusJj/pre_»ctivailonB



Histogram Mode



OVERLAY



Offs« Time Axis

Runs



a    U> fillet f 曲s

S C W*"



layer 1 /Wx_plus_b/pfe_activaiions



A

\- -t-.

-H 4    ?    ? t



layer Vbieses/summaries/hisiogram



图9-6 TensorBoard HISTOGRAMS直方图的展示效果

单击EMBEDDINGS窗口，如图9-7所示，可以看到降维后的嵌入向量的可视化效果， 这是TensorBoard中的Embedding Projector功能。虽然在MNIST数据的训练中是没有嵌 入向量的，但是只要我们使用tf.save.Saver保存了整个模型，就可以让TensorBoard自动

对模型中所有二维的Variable进行可视化（TensorFlow中只有Variable可以被保存，而 Tensor不可以，因此我们需要把想可视化的Tensor转为Variable ）。我们可以选择T-SNE 或者PCA等算法对数据的列（特征）进行降维，并在3D或者2D的坐标中进行可视化展 示。如果我们的模型是Word2Vec计算或Language Model,那么TensorBoard的 EMEBEDDINGS可视化功能会变得非常有用。

DATA

•iO 》B ■ Points: 784 Dimension: 500

A

test



layerl/weighis/VariaWc

JJ3 Spbvieizcdala ©

Occipunt    ;t6o4i.-eT>nn.

with    model

:PCA



Component »1    - ComiXKwn''

Component #3    , D

PCA is epprox-fnasc. &

![img](06TensorFlow9e18_c4875a088c74095baibbt-130.jpg)



![img](06TensorFlow9e18_c4875a088c74095baibbt-131.jpg)



图9-7 TensorBoard EMBEDDINGS向量嵌入展示效果

##### 9.2多GPU并行

TensorFlow中的并行主要分为模型并行和数据并行。模型并行需要根据不同模型设 计不同的并行方式，其主要原理是将模型中不同计算节点放在不同硬件资源上运算。比较 通用的且能简便地实现大规模并行的方式是数据并行，其思路我们在第1章讲解过，是同 时使用多个硬件资源来计算不同batch的数据的梯度，然后汇总梯度进行全局的参数更新。

数据并行几乎适用于所有深度学习模型，我们总是可以利用多块GPU同时训练多个 batch数据，运行在每块GPU上的模型都基于同一个神经网络，网络结构完全一样，并且 共享模型参数。本节我们主要讲解同步的数据并行，即等待所有GPU都计算完一个batch 数据的梯度后，再统一将多个梯度合在一起，并更新共享的模型参数，这种方法类似于使 用了一个较大的batch。使用数据并行时，GPU的型号、速度最好一致，这样效率最高。

而异步的数据并行，则不等待所有GPU都完成一次训练，而是哪个GPU完成了训练， 就立即将梯度更新到共享的模型参数中。通常来说，同步的数据并行比异步的模式收敛速 度更快，模型的精度更高。

下面就讲解使用多GPU的同步数据并彳于来训练卷积神经网络的例子，使用的数据集 为CIFAR-10。首先载入各种依赖的库，其中包括TensorFlow Models中cifarlO的类（我 们在第5章下载了这个库，现在只要确保Python执行路径在models/tutorials/image/cifar 10 下即可），它可以下载CIFAR-10数据并进行一些数据预处理。本节我们不再重头设计一 个CNN,而是直接使用一个现成的CNN,并侧重于讲解如何使用数据并行训练这个CNN。 本节代码主要来自TensorFlow的开源实现6S。

import os.path

import re

import time

import numpy as np

import tensorflow as tf

import cifarl0

我们设置batch大小为128,最大步数为100万步（中间可以随时停止，模型定期保 存），使用的GPU数量为4 （取决于当前机器上有多少可用显卡）。

batch_size=128

max_steps=1000000

num_gpus=4

然后定义计算损失的函数tower_loss。我们先使用cifarl0.distorted_inputs产生数据增 强后的images和labels,并调用cifarlO.inference生成卷积网络（注意，我们需要为每个 GPU生成单独的网络，这些网络的结构完全一致，并且共享模型参数）。通过 cifarlO.inference生成的卷积网络和5.3节中的卷积网络一致，读者若想了解网络结构的具 体细节，可参考5.3节中的内容。然后，根据卷积网络和labels,调用cifarlO.loss计算損 失函数（这里不直接返回loss，而是储存到collection中），并用tf.get_collection（'losses',scope） 获取当前这个GPU上的loss （通过scope限定了范围），再使用tf.add_n将所有损失叠加 到一起得到total_loss。最后返回totaljoss作为函数结果。

def towep_loss（scope）:

images, labels = cifarlO.distorted_inputs（）

logits = cifarl0.inference(images)

_ = cifarl0.loss(logitslabels) losses = tf.get_collection('losses'j scope) total_loss = tf .addjXlosseSj name='total_loss *) return total_loss

下面定义函数average_gradients,它负责将不同GPU计算出的梯度进行合成。函数 的输入参数tower_grads是梯度的双层列表，外层列表是不同GPU计算得到的梯度，内层 列表是某个GPU内计算的不同Variable对应的梯度，最内层元素为(grads, variable)，即 tower_grads的基本兀素为二元组(梯度，变量)。其具体形式为［［(gradO_gpuO，varO_gpuO), (gradl_gpuO, varl_gpuO)，...］，［ (gradO_gpul, varO_gpul), (gradl_gpul, varl一gpul)”..］，...］ o 我们先创建平均梯度的列表aVerage_gradS，它负责将梯度在不同GI>U间进行平均。然后 使用 zip(*tower_grads)将这个双层列表转置，变成［［(gradO_gpuO，var0_gpu0)3 (gradO_gpul, varO_gpul)，…］，［(gradl_gpuO, varl_gpuO), (gradl_gpul, varl_gpul)，…］，…］的形式，然后使 用循环遍历其元素。每个循环中获取的元素grad_and_vars,是同一个Variable的梯度在不 同 GPU 上的计算结果，即［(gradO_gpuO, varO_gpuO), (gradO_gpul, varO_gpul)，...］。对同一 个Variable的梯度在不同GRU计算出的副本，需要计算其梯度的均值，如果这个梯度是 一个7V维的向量，需要在每个维度上都进行平均。我们先使用tf.expand_dims给这些梯度 添加一个冗余的维度0,然后把这些梯度放到列表grad中，接着使用tf.concat将它们在维 度0上合并，最后使用tf.reduce_mean针对维度0上求平均，即将其他维度全部平均。最 后将平均后的梯度跟Variable组合得到原有的二元组(梯度，变量)格式，并添加到列表 average_grads中。当所有梯度都求完均值后，我们返回average_grads。

def average_gradients(tower_grads): average_grads =［］

for grad_and_vars in zip(*tower_grads): grads =［］

for gj _ in grad_and_vars:

expanded_g = tf,expand_dims(gJ 0) grads.append(expanded_g)

grad = tf,concat(grads^ 0) grad = tf.reduce_mean(grad^ 0)

v = grad_and_vars[0][1] grad_and_var = (grad^ v) average_grads.append(grad_and_var)

return average_grads

下面定义训练的函数。先设置默认的计算设备为CPU,用来进行一些简单的计算。 然后使用global_step记录全局训练的步数，并计算一个epoch对应的batch数，以及学习 速率衰减需要的步数decay_stepso我们使用tf.train.exponential_decay创建随训练步数衰 减的学习速率，这里第1个参数为初始学习速率，第2个参数为全局训练的步数，第3 个参数为每次衰减需要的步数，第4个参数为衰减率，staircase设为True代表是阶梯式 的衰减。然后设置优化算法为GradientDescent,并传入随步数衰减的学习速率。 def train():

with tf.Graph().as_default()^ tf.device('/cpu:0'): global_step = tf .get_variable( *global_step', []_,

initializer=tf.constant_initializer(0) trainable=False)

num_batches_per_epoch = cifarlO.NUM_EXAMPLES_PER_EPOCH_FOR__TRAIN / \ batch_size

decay一steps = int(num_batches_per一epoch * cifarl0.NUM_EPOCHS_PER_DECAY)

lr = tf.train.exponential_decay(cifarl0.INITIAL__LEARNIN6_RATEj global_stepj

decay一steps    .

cifarlO.LEARNING_RATE_DECAY_FACTORJ staircase=True)

opt = tf.train.GradientDescentOptimizer(lr)

我们定义储存各GPU计算结果的列表towerjads。然后创建一个循环，循环次数为 GPU数量，在每一个循环内，使用tf.device限定使用第几个GPU，如gpuO、gpul,然后 使用tf.name_scope将命名空间定义为tower_0、tower_l的形式。对每一个GPU，使用前

面定义好的函数 tower_loss 获取其损失，然后调用 tf.get_variable_scope().reuse_variables() 重用参数，让所有GPU共用一个模型及完全相同的参数。再使用opt.compute_gradients(loss) 计算单个GPU的梯度，并将求得的梯度添加到梯度列表towei^grads。最后使用前面写好 的函数average_gradients计算平均梯度，并使用opt.apply_gradients更新模型参数。这样 就完成了多GPU的同步训练和参数更新。

tower_grads =[]

for i in range(num_gpus):

with tf.device(1/gpu:%d* % i):

with tf.name_scope('%s_%d* % (cifarl0.TOWER_NAMEi)) as scope: loss = tower_loss(scope) tf.get_variable_scope().reuse_variables() grads = opt.compute_gradients(loss) tower_grads.append(grads)

grads = average_gradients(tower_grads)

apply_gradient_op = opt.apply_gradients(grads., global__step=global_step)

我们创建模型的保存器saver,将Session的allow_softjplacement参数设置为True(有 些操作只能在CPU进行，不使用soft_placement可能导致运行出错)，初始化全部参数， 并调用tf.train.start_queue_runners()准备好大量的数据增强后的训练样本，防止后面的训练 被阻塞在生成样本上。

saver = tf.train.Saver(tf.all_variables()) init = tf.global_variables_initializer()

sess = tf.Session(config=tf.ConfigProto(allow_soft_placement=True)) sess.run(init)

tf.train.start_queue_runners(sess=sess)

下面进入训练的循环，最大迭代次数为maX_stepS。在每一步中执行一次更新梯度的 操作apply_gradient_op (即一次训练操作)和计算损失的操作loss,同时使用time.time() 记录耗时。每隔10步，展示一次当前batch的loss,以及每秒钟可训练的样本数和每个 batch训练所需要花费的时间。每隔1000步，使用Saver保存整个模型文件。

for step in range(max_steps):

start_time = time.time()

loss_value = sess.run([apply_gradient_op?

loss])



duration = time.time() - start_time if step % 10 == 0:

num一examples_per_step = batch_size * num_gpus examples_per_sec = num_examples_per_step / duration sec_per_batch = duration / num_gpus

format_str = ('step %d， loss = %.2f (%.lf examples/sec; %.3f ’

'sec/batch)1)

print (format_str % (step^ loss_value3 examples_per_sec4 sec_per_batch))

if step % 1000 == 0 or (step + 1) == max_steps:

saver.save(sess' /tmp/cifarl0_train/model.ckpt*, global一step=step)

我们将主函数后全部定义完后，使用cifarl0.maybe_download_and_extract()下载完整 的CIFAR-10数据，并调用trainO函数开始训练。

cifarl0,maybe_download_and_extract()

train()

下面展示的结果即为训练过程中显示的日志，loss从最开始的4点几，到第70万步 时，大致降到了 0.07。我们的训练速度很快，平均每个batch的耗时仅为0.021s,平均每 秒可以训练6000个样本，差不多正好是单GPU的4倍。因此在单机多GPU的情况下, 使用TensorFlow实现的数据并行效率是非常高的。

step 729470, loss = 0.07 (6043.4 step 729480, loss = 0.07 (6200.1 step 729490) loss = 0.08 (6055.5 step 729500, loss = 0.09 (5986.7 step 729510, loss =0.07 (6075.3 step 729520, loss = 0.06 (6630-1 step 729530, loss = 0.09 (6788.4



examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.021 sec/batch) examples/sec; 0.019 sec/batch) examples/sec; 0*019 sec/batch)



step 729540, step 729550, step 729560, step 729570, step 729580,



loss = 0.08

loss = 0.06

loss = 0.08

loss = 0.08

loss = 0.07



(6464.4 examples/sec; (6548.5 examples/sec; (6900.3 examples/sec; (6381.3 examples/sec; (6101.0 examples/sec;



0.020 sec/batch) 0.020 sec/batch) 0.019 sec/batch) 0.020 sec/batch) 0.021 sec/batch)



##### 9.3分布式并行

TensorFlow的分布式并行基于gRPC通信框架，其中包括一个master负责创建Session, 还有多个worker负责执行计算图中的任务。我们需要先创建一个TensorFlow Cluster对象， 它包含了一组task (每个task —般是一台单独的机器)用来分布式地执行TensorFlow的 计算图。一个Cluster可以切分为多个job，一个job是指一类特定的任务，比如parameter server (ps)、worker,每一个job里可以包含多个task。我们需要为每一个task创建一个 server,然后连接到Cluster上，通常每个task会执行在不同的机器上，当然也可以一台机 器上执行多个task (控制不同的GPU )。Cluster对象通过tf.train.ClusterSpec来初始化， 初始化信息是一个 Python 的 diet,例如 tf.train.ClusterSpec({HpsH: ["192.168.233.201:2222"]， ,'worker,,:[n192.168.233.202:2222","192.168.233.203:2222n]}),这代表设置了一个parameter server和两个worker,分别在三台不同机器上。对每个task,我们需要给它定义自己的身 份，比如对这个 ps 我们将设置 server = tf.train. Server(cluster, job_name=Hps", task_index=0),将这台机器的job定义为ps,并且是ps中的第0台机器。此外，通过在 程序中使用诸如with tf.device("/job:worker/task:7"),可以限定Variable存放在哪个task或 哪台机器上。

TensorFlow的分布式有几种模式，比如In-graph replication模型并行，将模型的计算 图的不同部分放在不同机器上执行；而Befween-graph replication则是数据并行，每台机 器使用完全相同的计算图，但是计算不同的batch数据。此外，我们还有异步并行和同步 并行，异步并行指每机器独立计算梯度，一旦计算完就更新到parameter server中，不等 其他机器；同步并行指等所有机器都完成对梯度的计算后，将多个梯度合成并统一更新模 型参数。一般来说，同步并行训练时，loss下降的速度更快，可达到的最大精度更高，但 是同步并行有木桶效应，速度取决于最慢的SP个机器，所以当设备速度一致时，效率比较 局O

下面我们就用TensorFlow实现包含1个paramter server和2个worker的分布式并行 训练程序，并以MNIST手写数据识别任务作为示例。这里需要写一个完整的Python文件， 并在不同机器上以不同的task执行。首先载入TensorFlow和所有依赖库。本节代码主要 来自TensorFlow的开源实现69。

import math

import tempfile

import time

import tensorflow as tf

from tensorflow.examples.tutorials.mnist import input_data

这里使用tf.app.flags定义标记，用以在命令行执彳亍TensorFlow程序时设置参数。在 命令行中指定的参数会被TensorFlow读取，并直接转为flags。设定数据储存目录data_dir 默认为/tmp/mnist-data,隐藏节点数默认为100，训练最大步数train_steps默认为1000000， batch size默认为100,学习速率为默认0.01。

flags = tf.app.flags

flags.DEFINE_string(ndata_dirnj "/tmp/mnist-data"j

"Directory for storing mnist data")

flags .DEFINE_integer(Mhidden_unitsn_, 100

"Number of units in the hidden layer of the NN")

flags. DEFINE_integer(,'train_steps,,J 1000000,

nNumber of (global) training steps to perform")

flags.DEFINE_integer(,'batch_size"^ 100) "Training batch size")

flags.DEFINE_float(Hlearning_raten0.01, "Learning rate")

然后设定是否使用同步并行的标记sync_replicas默认为False,在命令行执行时可以 设为True开启同步并行。同时，设定需要累计多少个梯度来更新模型的值默认为None， 这个参数代表进行同步并行时，一共积攒多少个batch的梯度才进行一次参数更新，设为 None则使用worker的数量，即所有worker都完成一个batch的训练后再更新模型参数。

flags.DEFINE_boolean(.’sync_replicas"_, False,

"Use the sync一replicas (synchronized replicas) mode," "wherein the parameter updates from workers are *' "aggregated before applied to avoid stale gradients")

flags.DEFINE_integer("replicas_to_aggregate"j Nonej

"Number of replicas to aggregate before parameter " "update is applied (For sync_replicas mode only;" "default: num_workers)")

再定义ps的地址，这里默认为192.168.233.201:2222,读者应该根据集群的实际情况 配置，下同。将 worker 的地址设置为 192.168.233.202:2222 和 192.168.233.203:2222。同 时，设置job_name和task_index的FLAG，这样在命令行执行时，可以输入这两个参数。

flags.DEFINE_string(,,ps_hosts,,J "192.168.233.201:2222\

"Comma-separated list of hostname:port pairs")

flags.DEFINE_string(nworker_hosts"?

"192.168.233.202：2222,192.168.233.203:2222n,

"Comma-separated list of hostname:port pairs")

flags.DEFINE_string("job_name"j None,"job name: worker or ps")

flags,DEFINE_integer("task_index", None,

"Worker task index_, should be >= 0. task_index=0 is "

•’the master worker task the performs the variable " "initialization ")

将flags.FLAGS直接命名为FLAGS,简化使用。同时，设置图片尺寸IMAGE_PIXELS 为28。

FLAGS = flags.FLAGS

IMAGE_PIXELS = 28

接下来编写程序的主函数main,首先使用input_data.read_data_sets下载并读取MNIST 数据集，并设置为one_hot编码格式。同时，检测命令行输入的参数，确保有job_name 和task_index这两个必备的参数。显示出job_name和task_index,并将ps和worker的所 有地址解析成列表ps_spec和worker_spec0

def mai门(unused_argv):

mnist = input__data.read_data_sets(FLAGS.data_dirj one_hot=True)

if FLAGS.job_name is None or FLAGS.job一name ==

raise ValueError("Must specify an explicit 'job_name'")

if FLAGS.task_index is None or FLAGS.task_index

raise ValueError("Must specify an explicit 'task_index'")

print("job name = %s" % FLAGS.job_name) print(”task index = %d" % FLAGS.task_index)

ps_spec = FLAGS.ps_hosts.split(n/') worker_spec = FLAGS. worker_hosts. split

先计算总共的worker数量，然后使用tf.train.ClusterSpec生成一个TensorFlow Cluster 的对象，传入的参数是ps的地址信息和worker的地址信息。再使用tf.train.Server创建当 前机器的server,用以连接到Cluster。如果当前节点是parameter server,则不再进行后 续的操作，而是使用server.join等待worker工作。

num一workers = len(worker_spec)

cluster = tf.train.ClusterSpec({"ps": ps_spec^ "worker": worker_spec}) server = tf.train.Server(

clustery job_name=FLAGS.job_namej task_index=FLAGS.task_index) if FLAGS.job_name == "ps":

server.join()

这里判断当前机器是否为主节点，即taskjndex是否为0。然后定义当前机器的 worker_device,格式为"job:worker/task:0/gpu:0"。我们假定有两台机器，并且每台机器有 1块GPU，则总共需要两个worker。如果一台机器有多块GPU,可以通过一个task管理 多个GPU或者使用多个task分别管理。下面使用tf.train.replica_device_setter()设置worker 的资源，其中worker_device为计算资源，ps_device为存储模型参数的资源。我们通过 replica_device_setter将模型参数部署在独立的ps服务器“/job:ps/cpu:0”，并将训练操作部 署在”/job:worker/task:0/gpu:0n,即本机的GPU。最后再创建记录全局训练步数的变量 global_stepo

is_chief = (FLAGS.task_index == 0)

worker_device = "/job:worker/task:%d/gpu:0" % FLAGS.task_index with tf.device(

tf.train.replica_device_setter( worker_device=worker_device_,

ps_device="/job:ps/cpu:0"j cluster=cluster)):

global_step = tf.VariableCOj name="global_step"trainable= False)

接下来，定义神经网络模型，本节的神经网络和4.4节的MLP全连接网络基本一致。

下面使用tf.truncated_normal初始化权重，使用tf.zeros初始化偏置，创建输入的placeholder, 并使用tf.nn.xwjplus_b对输入x进行矩阵乘法和加偏置操作，再用ReLU激活函数处理， 得到第一个隐层的输出hid。然后使用tf.nn.xw_plus_b和tf.nn.softmax对第一层的输出hid 进行处理，得到网络的最终输出y。最后计算损失cross_entropy,并定义优化器为Adam。

hid_w = tf.Variable(

tf.truncated_normal([IMAGE_PIXELS*IMAGE_PIXELSJ FLAGS.hidden_units], stddev=1.0 / IMA6E_PIXELS)? name="hid_w")

hid_b = tf.Variable(tf.zeros([FLAGS.hidden_units])name= ”hid_b")

sm_w = tf.Variable(

tf. truncated一normal ([FLAGS. hidden_units, 10]

stddev=1.0 / math.sqrt(FLAGS.hidden_units))name="sm_wn)

sm_b = tf.Variable(tf.zeros([10]name="sm_b")

x = tf.placeholder(tf.float32, [None, IMAGE_PIXELS * IMAGE_PIXELS]) y_ = tf.placeholder(tf.float32j [None, 10])

hid_lin = tf.nn.xw_plus_b(x, hid_wJ hid_b) hid = tf.nn.relu(hid_lin)

y = tf.nn.softmax(tf.nn.xw_plus_b(hid^ sm_w> sm_b))

cross_entropy = -tf.reduce_sum(y_ * tf.log(tf.clip_by_value(y^ le-10?

1.0)))

opt = tf.train.AdamOptimizer(FLAGS.learning_rate)

我们判断是否设置了同步训练模式symLjeplicas，如果是同步模式，则先获取同步更 新模型参数所需要的副本数replicas_to_aggregate;如果没有单独设置，则使用worker数

作为默认值。然后使用tf.train.SyncReplicasOptimizer创建同步训练的优化器，它实质上是 对原有优化器的一个扩展，我们传入原有优化器及其他参数(replicas_to_aggregate、 total_num_replicas、replica_id等)，它就会将原有优化器改造为同步的分布式训练版本。 最后，使用普通的(即异步的)或同步的优化器对损失crossjntropy进行优化。

if FLAGS.sync_replicas:

if FLAGS.replicas_to一aggregate is None:

replicas_to_aggregate = num_workers else:

replicas_to_aggregate = FLAGS.replicas_to_aggregate

opt = tf.train.SyncReplicasOptimizer( opt,

replicas_to_aggregate=replicas__to_aggregatej total_num_replicas=num__workersj replica_id=FLAGS.task_index^ name=,'mnist_sync_replicas")

train一step = opt.minimize(cross_entropy4 global_step=global_step)

如果是同步训练模式，并且为主节点，则使用opt.get_chief_queue_runner创建队列执 行器，并使用opt.get_init_tokens_op创建全局参数初始化器。

if FLAGS.sync_replicas and is_chief:

chief一queue_runner = opt.get_chief_queue一runner() init_tokens_op = opt. get_init_tokens__op ()

下面生成本地的参数初始化操作init_op ,创建临时的训练目录，并使用 tf.train_Supervisor创建分布式训练的监督器，传入的参数包括is_chief、train_dir、init_op 等。这个Supervisor会管理我们的task参与到分布式训练。

init_op = tf.global_variables_initializer() train_dir = tempfile.mkdtemp() sv = tf.train.Supervisor(is_chief=is_chief

logdir=train_dirj init_op=init_opJ

recovery_wait_secs=l? global_step=global_step)

然后设置Session的参数，其中allow_soft_placement设为True代表当某个操作在指 定的device不能执行时，可以转到其他device执行。

sess_config = tf.ConfigProto( a 11 o w_s oft_p 1 a c em e n t=T r u e log_device_placement=Falsej device_filters=["/job:ps '

"/job:worker/task:%d" % FLAGS.task_index])

如果为主节点，则显示初始化Session,其他节点则显示等待主节点的初始化操作。 然后执行sv.prepate_or_wait_for_session(),若为主节点则会创建Session,若为分支节点则 会等待。

if is_chief:

print("Worker %d: Initializing session..." % FLAGS.task_index) else:

print("Worker %d: Waiting for session to be initialized…,’ %

FLAGS.task_index)

sess = sv.prepare_o「_wait_for_session(server.targetconfig=sess__config)

print("Worker %d: Session initialization complete." % FLAGS.task_index)

接着，如果处于同步模式并且是主节点，则调用sv.start_queue_runners执行队列化执 行器chief_queue_runner,并执行全局的参数初始化器init_tokens_opD

if FLAGS.sync_replicas and is_chief:

print(HStarting chief queue runner and running init_tokens_op") sv.start_queue_runners(sess\, [chief_queue一runner])

sess.run(init_tokens_op)

下面就正式到了训练过程。我们记录worker执行训练的启动时间，初始化本地训练 的步数local_step，然后进入训练循环。在每一步训练中，我们从mnist.train.next_batch读 取一个batch的数据，并生成feed_dict，再调用train_step执行一次训练。当全局训练步

数达到我们预设的最大值后，停止训练。

time_begin = time.time() print("Training begins @    " % time_begin)

local一step = 0 while True:

batch_xSj batch_ys = mnist.train.next_batch(FLAGS.batch_size) train_feed = {x: batch_xs_, y_: batch_ys}

step = sess.run( [train_stepj global_step], feed_dict=train_feed) local_step += 1

now = time.time()

print("%f: Worker %d: training step %d done (global step: %d)" % (nowFLAGS.task_index_, local_step, step))

if step >= FLAGS.train_steps: break

训练结束后，我们展示总训练时间，并在验证数据上计算预测结果的损失 cross_entropy,并展示出来。至此，我们的主函数main全部结束。

time_end = time.time() print("Training ends @ %fn % time_end) training_time = time_end - time_begin

print("Training elapsed time: %f s" % training_time)    -

val_feed = {x: mnist.validation.imagesy一： mnist.validation.labels}

val_xent = sess.run(cross_entropyJ feed_dict=val_feed)

print("After %d training step(s), validation cross entropy = %g" %

(FLAGS.train_stepSj val_xent))

这是代码的最后一部分，在主程序中执行tf.app.runO并启动main()函数，我们将全部 代码保存为文件distributed.py。我们需要在3台不同的机器上分别执行distributed.py启动

3个task,在每次执行distributed.py时我们需要传入job_name和task_index指定worker 的身份。

if _name_ == "_main_": tf.app.run()

我们分别在三台机器 192.168.233.201、192.168.233.202 和 192.168.233.203 上执行下 面三行代码。第一台机器执行第一行代码，第二台机器执行第二行代码，下同。这样我们 Z就在三台机器上分别启动了一个parameter server及两个worker。

python distributed.py --job_name=ps --task_index=0

python distributed.py --job_name=worker --task_index=0

python distributed.py --job_name=worker --task_index=l

如果我们想使用同步模式，只需要将上面的代码加上-sync_replicas=True，就可以自 动开启同步训练。注意，此时global_step和异步不同，异步时，全局步数是所有worker 训练步数之和，同步时则是指有多少轮并行训练。

python distributed.py --job_name=ps --task一index=0 --sync_replicas=True python distributed.py --job_name=worker --task_index=0 --sync_replicas=True python distributed.py --job_name=worker --task_index=l --sync_replicas=True

下面是我们在parameter server上显示出的日志。我们在192.168.233.201:2222上顺利 开启了 PS的服务。

I tensorflow/core/distributed_runtime/rpc/grpc_channel.cc:197] Initialize Gr pcChannelCache for job ps -> {0 -> localhost:2222}

I tensorflow/core/distributed_runtime/rpc/grpc_channel.cc:197] Initialize Gr pcChannelCache for job worker -> {0 -> 192.168.233.202:2223, 1 -> 192.168.23 3.203:2224}

I tensorflow/core/distributed_runtime/rpc/grpc_server_lib.cc:206] Started se rver with target: grpc://localhost:2222

下面是workerO在192.168.233.202上的训练日志。

1484195706.167773:    Worker    0:    training    step    5657    done    (global    step:    10285)

1484195706.178822:    Worker    0:    training    step    5658    done    (global    step:    10287)

1484195706.189648:    Worker    0:    training    step    5659    done    (global    step:    10289)

1484195706.200894: Worker 0: training step 5660 done (global step: 10291) 1484195706.212560: Worker 0: training step 5661 done (global step: 10293) 1484195706.224736: Worker 0: training step 5662 done (global step: 10295) 1484195706.237565: Worker 0: training step 5663 done (global step: 10297) 1484195706.252718: Worker 0: training step 5664 done (global step: 10299)

下面是workerl在192.168.233.203上的训练日志。

1484195714.332566: Worker 1: training step 5269 done (global step: 11569) 1484195714.345961: Worker 1: training step 5270 done (global step: 11571) 1484195714.359124: Worker 1: training step 5271 done (global step: 11573) 1484195714.372848: Worker 1: training step 5272 done (global step: 11575) 1484195714.386048: Worker 1: training step 5273 done (global step: 11577) 1484195714.398567: Worker 1: training step 5274 done (global step: 11579) 1484195714.411631: Worker 1: training step 5275 done (global step: 11581) 1484195714.424619: Worker 1: training step 5276 done (global step: 11583)

至此，我们在三台机器上的数据并行模式的分布式训练的示例就结束了，读者可以看 到用TensorFlow实现分布式训练非常简单。我们可以复用单机版本的网络结构，只是在不 同机器上训练不同batch的数据，并使用parameter server统一管理模型参数。另夕卜，分布 式TensorFlow的运行效率也非常高，在16台机器上可以获得15倍于单机的速度，非常 适合大规模神经网络的训练。
