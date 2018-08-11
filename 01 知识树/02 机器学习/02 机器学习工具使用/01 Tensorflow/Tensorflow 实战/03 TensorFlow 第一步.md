### TensorFlow 第一步



前两章我们讲了 TensorFlow的核心概念和编程模型，又谈了 TensorFlow和其他深度 学习框架的异同。本章将直奔主题，我们将学会安装TensorFlow,然后使用TensorFlow （1.0.0-rc0）训练一个手写数字识别（MNIST）的模型。

##### 3.1 TensorFlow的编译及安装

TensorFlow的安装方式没有Theano那么直接，因为它并不是全部由Python写成的库, 底层有很多C++乃至CUDA的代码，因此某些情况下可能需要编译安装（比如你的gcc 版本比较新，硬件环境比较特殊，或者你使用的CUDA版本不是realease版预编译的）。 通常安装TensorFlow分为两种情况，一种是只使用CPU’安装相对容易；另一种是使用 GPU,这种情况还需要安装CUDA和cuDNN,情况相对复杂。然而不管哪种情况，我们 都推荐使用Anaconda20作为Python环境，因为可以避免大量的兼容性问题。另外，本书 默认使用Python 3.5作为Python的基础版本，相比Python 2.7,它更代表了 Python未来 的趋势发展。TensorFlow目前支持得比较完善的是Linux和Mac （对Windows的支持还 不太全面）。因为Mac系统主要使用CPU版本（Mac系统很少有使用NVIDIA显卡的， 而目前TensorFlow对CUDA支持得比较好，对OpenCL的支持还属于实验性质），安装 方式和Linux的CPU版基本一致，而Mac —般没有NVIDIA的显卡，所以不适合使用

—「TensorFlow 实战

GPU版本。本章将主要讲解在Linux下安装TensorFlow的过程。另外，本书基于2017 年1月发布的TensorFlow 1.0.0-rc0版，旧版本在运行本书的代码时可能会有不兼容的情 况，所以建议读者都安装这个版本或更新版本的TensorFlow。此外，本书推荐有条件的 读者使用GPU版本，因为在训练大型网络或者大规模数据时，CPU版本的速度可能会很 慢。

###### 3.1.1 安装 Anaconda

Anaconda是Python的一个科学计算发行版，内置了数百个Python经常会使用的库， 也包括许多我们做机器学习或数据撩掘的库，包括Scikit-leam、NumPy、SciPy和Pandas 等，其中可能有一些还是TensorFlow的依赖库。我们在安装这些库时，通常都需要花费 不少时间编译，而且经常容易出现兼容性问题，Anaconda提供了一个编译好的环境可以 直接安装。同时Anaconda自动集成了最新版的MKL (Math Kernel Libaray)库，这是 Intel推出的底层数值计算库，功能上包含了 BLAS ( Basic Linear Algebra Software )等 矩阵运算库的功能，可以作为NumPy、SciPy、Scikit-leam、NumExpr等库的底层依赖， 加速这些库的矩阵运算和线性代数运算。简单来说，Anaconda是目前最好的科学计算的 Python环境，方便了安装，也提高了性能。本书强烈推荐安装Anaconda,接下来的章节 也将默认读者使用Anaconda作为TensorFlow的Python环境。

(1 )我们在 Anaconda 的官网上([www.continuum.io/downloads](http://www.continuum.io/downloads) )下载 Anaconda3 4.2.0 版，请读者根据自己的操作系统下载对应版本的64位的Python 3.5版。

(2)    我们在Anaconda的下载目录执行以下命令(请根据下载的文件替换对应的文件

名)。

bash Anaconda3-4.2.0-Linux-x86_64.sh

(3)    接下来我们会看到安装提示，直接按回车键确认进入下一步。然后我们会进入 Anaconda的License文档，这里直接按q键跳过，然后输入yes确认。下面的这一步会让 我们输入anaconda的安装路径，没有特殊情况的话，我们可以按回车键使用默认路径， 然后安装就自动开始了。

(4)    安装完成后，程序提示我们是否把anaconda3的binary路径加入到.bashrc，读者 可以根据自己的情况考虑，建议添加，这样以后python和ipython命令就会自动使用 Anaconda Python3.5 的环境了。

###### 3.1.2 TensorFlow CPU 版本的安装

TensorFlow的CPU版本相对容易安装，一般分为两种情况：第一种情况，安装编译 好的release版本，推荐大部分用户安装这种版本；第二种情况，使用l.O.O-rcO分支源码 编译安装，当用户的系统比较特殊，比如gcc版本比较新(gcc 6以上)，或者不支持使 用编译好的release版本，才推荐这样安装。

第一种情况，安装编译好的release版本，我们可以简单地执行下面这个命令。python 的默认包管理器是pip，直接使用pip来安装TensorFlow。对于Mac或Windows系统，可 在TensorFlow的GitHub仓库上的Download and Setup页面查看编译好的程序的地趾。

export TF_BINARY_URL=<https://storage.googleapis.com/tensorflow/linux/cpu/ten>

sorflow-1.0.0rc0-cp35-cp35m-linux_x86_64.whl

pip install --upgrade $TF_BINARY_URL

第二种情况，使用l.O.O-rcO分支的源码编译安装。

此时，确保系统安装了 gcc (版本最好介于4.8〜5.4之间)，如果没有安装，请根据 自己的系统情况先安装gcc，本节不再赘述。此外，为了编译TensorFlow,我们还需要有 Google自家的编译工具bazel ( github.com/bazelbuild/bazel ),根据其安装教程 ([www.bazel.io/versions/master/docs/install.html)](http://www.bazel.io/versions/master/docs/install.html)%e7%9b%b4%e6%8e%a5%e5%ae%89%e8%a3%85%e5%ae%83%e7%9a%84)[直接安装它的](http://www.bazel.io/versions/master/docs/install.html)%e7%9b%b4%e6%8e%a5%e5%ae%89%e8%a3%85%e5%ae%83%e7%9a%84) v0.43 release 版本即可，不 需要使用最新的dev版本的功能。

在正确地安装完gcc和bazel之后，接下来我们正式开始编译安装TensorFlow,首先 先下载 TensorFlow 1.0.O-rcO 的源码：

wget <https://github.com/tensorflow/tensorflow/archive/vl.0.0-rc0.tar.gz> tar -xzvf vl.0.0-rc0.tar.gz

完成下载之后，进入TensorFlow代码仓库的目录，然后执行下面的命令进行配置:

cd tensorflow-1.0.0-rc0

./configure

接下来的输出要选择python路径，确保是anaconda的python路径即可：

Please specify the location of python. [Default is /home/wenjian/anaconda3/b in/python]:

这里选择CPU编译优化选项，默认的-march=native将选择本地CPU能支持的最佳配 置，比如SSE4.2、AVX等。建议选择默认值。

Please specify optimization flags to use during compilation [Default is -m arch=native]:

选择是否使用jemalloc作为默认的malloc实现（仅限Linux ），建议选择默认设置。

Do you wish to use jemalloc as the malloc implementation? (Linux only) [Y/ n]

然后它会让我们选择是否开启对Google Cloud Platform的支持，这个在国内一般是 访问不到的，有需要的用户可以选择支持，通常选N即可：

Do you wish to build TensorFlow with Google Cloud Platform support? [y/N]

它会询问是否需要支持Hadoop File System,如果有读取HDFS数据的需求，请选y 选项，否则就选默认的N即可：

Do you wish to build TensorFlow with Hadoop File System support? [y/N]

选择是否开启XLA JIT编译编译功能支持。这里XLA是TensorFlow目前实验性的 JIT ( Just in Time)、AOT (Ahead of Time)编译优化功能，还不太成熟，有探索欲望的 读者可以尝拭开启。

Do you wish to build TensorFlow with the XLA just-in-time compiler (experi mental)? [y/N]

然后它会让我们选择python的library路径，这里依然选择anaconda的路径：

Please input the desired Python library path to use. Default is

[/home/wenjian/anaconda3/lib/python3.5/site-packages]

接着选择不需要使用GPU,即OpenCL和CUDA全部选N:

Do you wish to build TensorFlow with OpenCL support? [y/N]

Do you wish to build TensorFlow with CUDA support? [y/N]

之后可能需要下载一些依赖库的文件，完成后configure就顺利结束了，接下来使用 编译命令执行编译：

bazel build --copt=-march=native -c opt //tensorflow/tools/pip_package:build _pip_package

编译结束后，使用下面的命令生成pip安装包：

bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

最后，使用pip命令安装TensorFlow：

pip install /tmp/tensorflow_pkg/tensorflow-1.0.0rc0-cp35-cp35m-linux_x86_64. whl

###### 3.1.3 TensorFlow GPU 版本的安装

TensorFlow的GPU版本安装相对复杂。目前TensorFlow仅对CUDA支持较好，因 此我们首先需要一块NVIDIA显卡，AMD的显卡只能使用实验性支持的OpenCL,效果 不是很好。接下来，我们需要安装显卡驱动、CUDA和cuDNNo

CUDA是NVIDIA推出的使用GPU资源进行通用计算(Genral Purpose GHJ)的SDK， CUDA的安装包里一般集成了显卡驱动，我们直接去官网下载NVIDIA CUDA (<https://developer.nvidia.com/cuda-toolkit> )o

在安装前，我们需要暂停当前NVIDIA驱动的X server,如果是远程连接的Linux机 器，可以使用下面这个命令关闭X server：

sudo init 3

之后，我们将CUDA的安装包权限设置成可执行的，并执行安装程序：

chmod u+x cuda_8.0.44_linux.run

sudo ./cuda_8.0.44_linux.run    •

接下来我们正式进入CUDA的安装过程，先按q键跳过开头的license说明，接着输 入accept接收协议，然后按y键选择安装驱动程序：

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 367.48? (y)es/(n)o/(q)uit:

按y键选择安装CUDA并确认安装路径’ 一般可直接使用默认地址：

Install the CUDA 8.0 Toolkit?

(y)es/(n)o/(q)uit:

Enter Toolkit Location

[default is /usr/local/cuda-8.0 ]:

按n键不选择安装CUDA samples (我们只是通过TensorFlow调用CUD A,不直接 写CUDA代码)：

Install the CUDA 8.0 Samples?

(y)es/(n)o/(q)uit:

最后等待安装程序完成。

接下来安装cuDNN,cuDNN是NVIDIA推出的深度学习中CNN和RNN的高度优化 的实现。因为底层使用了很多先进技术和接口(没有对外开源)，因此比其他GPU上的 神经网络库性能要高不少，目前绝大多数的深度学习框架都使用cuDNN来驱动GPU计 算。我们先从官网下载 cuDNN ( [https://developer.nvidia.com/rdp/cudnn-download),](https://developer.nvidia.com/rdp/cudnn-download),%e8%bf%99%e4%b8%80%e6%ad%a5)[这一步](https://developer.nvidia.com/rdp/cudnn-download),%e8%bf%99%e4%b8%80%e6%ad%a5) 可能需要先注册NVIDIA的账号并等待审核(需要一段时间)。

接下来再安装cuDNN，我们到cuda的安装目录执行解压命令： cd /usr/local

sudo tar -xzvf ^/downloads/cudnn-8.0-linux-x64-v5.1.tgz

这样就完成了 cuDNN的安装，但我们可能还需要在系统环境里设置CUDA的路径： vim -/.bashrc

export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:/usr/local/cuda-8.0/extras/

CUPTI/lib64:$LD_LIBRARY_PATH

export CUDA_HOME=/usr/local/cuda-8.0

export PATH=/usr/local/cuda-8.0/bin:$PATH

source 〜/.bashrc

接下来，我们开始安装TensorFlow。对GPU版的TensorFlow，官网也提供了预编译 的包，但是这个预编译版对本地的各种依赖环境支持可能不是最佳的，如果读者试用过没 有任何兼容性问题，可以直接安装预编译版的TensorFlow：

export TF_BINARY_URL=<https://storage.googleapis.com/tensorflow/linux/gpu/ten> sorflow_gpu-1.0.0rc0-cp35-cp35m-linux_x86_64.whl

pip install --upgrade $TF_BINARY_URL

如果预编译的版本不支持当前的CUDA、cuDNN版本，或者存在其他兼容性问题， 可以进行编译安装，和前面提到的CPU版本的编译安装类似，我们需要先安装gcc和bazel, 接下来下载TensorFlow 1.0.0-rc0的代码，然后使用配置程序(./configure )进行编译配置， 前面几步和CPU版本的安装完全一致，直到选择是否支持CUDA这一步：

Do you wish to build TensorFlow with CUDA support? [y/N]

我们按y键选择支持GPU，接下来选择指定的gcc编译器，一般选默认设置就好。

Please specify which gcc should be used by nvcc as the host compiler. [Defau It is /usr/bin/gcc]:

接下来选择要使用的CUDA版本、CUDA安装路径、cuDNN版本和cuDNN的安装 路径，这里使用的是CUDA 8.0版本，所以CUDA SDK Version设置为8.0,路径设置为 /usr/local/cuda-8.0, cuDNN Version 设置为 5.1，cuDNN 路径也设置为/usr/local/cuda-8.0:

Please specify the Cuda SDK version you want to use_, e.g. 7.0. [Leave empty to use system default]:

Please specify the location where CUDA toolkit is installed. Refer to README. md for more details. [Default is /usr/local/cuda]:

Please specify the Cudnn version you want to use. [Leave empty to use system default]:

Please specify the location where cuDNN library is installed. Refer to READM E.md for more details. [Default is /usr/local/cuda]:

最后将选择GPU的compute capability （ CUDA的计算兼容性），不同的GPU可能有 不同的compute capability,我们可以在官网查到具体数值，比如GTX 1080和新titan X 是 6.1,而 GTX 980 和旧版的 GTX Titan X 是 5.2。

Please note that each additional compute capability significantly increases your build time and binary size.

[Default is: "3.5,5.2"]:

至此，配置完成，配置程序可能会开始下载对应的需要其他库的代码仓库，我们耐心 等待一会儿就好。

接下来，开始编译GPU版本的TensorFlow,执行下面这个命令，注意和CPU版本的 编译相比，这里多了一个-config=cuda:

bazel build --copt=-march=native -c opt --config=cuda //tensorflow/tools/pip —package:build_pip_package

编译大概需要花费一■段时间，之后执行命令生成pip安装包并进行安装：

bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg pip install /tmp/tensorflow_pkg/tensorflow-1.0.0rc0-cp35-cp35m-linux_x86_64. whl

##### 3.2 TensorFlow 实现 Softmax Regression 识别手写数字

3.1节介绍了安装TensorFlow,接下来我们京尤以一个机器学习领域的Hello World任 务——MNIST 手写数字识别来探索 TensorFlow。MNIST21 ( Mixed National Institute of Standards and Technology database)是一个非常简单的机器视觉数据集，如图3-1所示， 它由几万张28像素x28像素的手写数字坦成，这些图片只包含灰度值信息。我们的任务 就是对这些手写数字的图片进行分类，转成0 ~ 9 —共10类。

图3-1 MNIST手写数字图片示例

首先对MNIST数据进行加载，TensorFlow为我们提供了一个方便的封装，可以直接 加载MNIST数据成我们期望的格式，在ipython命令行或者spyder中直接运行下面的代 码。本节代碍主要来自TensorFlow的开源实现220

from tensorflow.examples.tutorials.mnist import input_data

mnist = input_data.read_data_sets("MNIST_data/n, one_hot=True)

然后查看mnist这个数据集的情况，可以看到训练集有55000个样本，测试集有10000 个样本，同时验证集有5000个样本。每一个样本都有它对应的标注信息，即label。我们 将在训练集上训练模型，在验证集上检验效果并决定何时完成训练，最后我们在测试集评

测模型的效果(可通过准确率、召回率、Fl-score等评测)。

print(mnist.train.images.shape, mnist.train.labels.shape)

print(mnist.test.images.shape』 mnist.test.labels.shape)

print(mnist.validation.images.shape, mnist.validation.labels.shape)

前面提到我们的图像是28像素。28像素大小的灰度图片，如图3-2所示。空白部分 全部为0,有笔迹的地方根据颜色深浅有0到1之间的取值。同时，我们可以发现毎个样 本有784维的特征，也就是28x28个点的展开成1维的结果(28x28=784 )。因此，这里 丢弃了图片的二维结构方面的信息，只是把一张图片变成一个很长的1维向量。读者可能 会问，图片的空间结构信息不是很有价值吗，为什么我们要丢弃呢？因为这个数据集的分 类任务比较简单，同时也是我们使用TensorFlow的第一次尝试，我们不需要建立一个太 复杂的模型，所以简化了问题，丢弃空间结构的信息。后面的章节将使用卷积神经网络对 空间结构信息进行利用，并取得更高的准确率。我们将图片展开成1维向量时，顺序并不 重要，只要每一张图片都是用同样的顺序进行展开的就可以。

![img](06TensorFlow9e18_c4875a088c74095baibbt-38.jpg)



图3-2手写数字灰度信息示例



匯

##### ■

题

##### ■

BE



•連

我们的训练数据的特征是一个55000x784的Tensor,第一个维度是图片的编号，第 二个维度是图片中像素点的编号，如图3-3所示。同时训练的数据Label是一个55000x10 的Tensor,如图3-4所示，这里是对10个种类进行了 one-hot编码，Label是一个10维的 向量，只有1个值为1，其余为0。比如数字0,对应的Label就是［1，0,0,0,0,0,0,0,0,0］，数 字5对应的Label就是［0,0,0,0,0，1，0,0,0,0］,数字zz就代表对应位置的值为1。

图3-3 MNIST训练数据的特征

55000

图3-4 MNIST训练数据的Label

准备好数据后，接下来就要设计算法了，这里使用一个叫作Softmax Regression的算 法训练手写数字识别的分类模型。我们的数字都是0 ~ 9之间的，所以一共有10个类别， 当我们的模型对一张图片进行预测时,Softmax Regression会对每一种类别估算一个概率： 比如预测是数字3的概率为80%,是数字5的概率为5%,最后取概率最大的那个数字作 为模型的输出结果。

当我们处理多分类任务时，通常需要使用Softmax Regression模型。即使后面章节的 卷积神经网络或者循环神经网络，如果是分类模型，最后一层也同样是Softmax Regression。 它的工作原理很简单，将可以判定为某类的特征相加，然后将这些特征转化为判定是这一 类的概率。上述特征可以通过一些简单的方法得到’比如对所有像素求一个加权和，而权 重是模型根据数据自动学习、训练出来的。比如某个像素的灰度值大代表很可能是数字n 时，这个像素的权重就很大；反之，如果某个像素的灰度值大代表不太可能是数字n时， 这个像素的权重就可能是负的。图3-5所示为这样的一些特征，其中明亮区域代表负的权 重，灰暗区域代表正的权重。

0    12    3    4

5    6    7    8    9

图3-5不同数字可能对应的特征权重

我们可以将这些特征写成如下公式：f代表第/类，代表一张图片的第J个像素。仏 是bias,顾名思义就是这个数据本身的一些倾向，比如大部分数字都是0,那么0的特征 对应的bias就会很大。

feature£ = Wtj'Xj +

接下来对所有特征计算softmax,结果如下。简单说就是都计算一个exp函数，然后 再进行标准化(让所有类别输出的概率值和为I )。

softmax(x) = normalize(exp(x))

其中判定为第f类的概率就可由下面的公式得到。

softmax(x)£=^^

Zjexp(xj

我们先对各个类的特征求exp函数，然后对它们标准化，使得和为1，特征的值越大 的类，最后输出的概率也越大；反之，特征的值越小的类，输出的概率也越小。最后的标 准化操作保证所有的概率没有为0或者为负数的，同时它们的和为1,也满足了概率的分 布。如果将整个计算过程可视化，结果如图3-6所示。

softmQJx

图 3-6 Softmax Regression 的流程



一®



接着，如果将图3-6中的连线变成公式，结果如图3-7所示，最后将元素相乘变成矩 阵乘法，结果如图3-8所示。

| 2/1  |          | + Wlt2X2 + 叭，3工3 +                |
| ---- | -------- | ------------------------------------ |
| V2   | =softmax | W2,iXi + W2,2 工2 + ^2,3^3 + b2      |
| 2/3  |          | 灰3，1 工1 + 灰3,2 工2 + ^3,3^3 + ^3 |

图3-7 Softmax Regression元素乘法示例

| yi   |          |      |      |       |       |      |      |      | 乂   |      |      |
| ---- | -------- | ---- | ---- | ----- | ----- | ---- | ---- | ---- | ---- | ---- | ---- |
| V2   | =softmax |      | 恥,1 | 1^2,2 | W2,3  |      | ^2   | +    | ^2   |      |      |
| V3   |          |      |      | IV3,2 | 1^3,3 |      | 工3  |      | 石3  |      |      |

图3-8 Softmax Regression矩阵乘法示例

上述鉅阵运算表达写成公式的话，可以用下面这样简洁的一行表达。

y — softmax(l4/x + h)

接下来京尤使用TensorFlow实现一个Softmax Regression。其实在Python中，当还没 有TensorFlow时，通常使用NumPy做密集的运算操作。因为NumPy是使用C和一部分 fortran语言编写的，并且调用openblas、mkl等矩阵运算库，因此效率很高。其中每一个 运算操作的结果都要返回到Python中，但不同语言之间传输数据可能会带来比较大的延 迟0TensorFlow同样也把密集的复杂运算搬到Python外执行，不过做得更彻底。TensorFlow 通过定义一个计算图将所有的运算操作全部运行在Python外面，比如通过C++运行在 CPU上或者通过CUDA运行在GPU上，而不需要每次把运算完的数据传回Python。

首先载入TensorFlow库，并创建一个新的InteractiveSession，使用这个命令会将这个 session注册为默认的session,之后的运算也默认跑在这个session里，不同session之间 的数据和运算应该都是相互独立的。接下来创建一个Placeholder,即输入数据的地方o Placeholder的第一个参数是数据类型，第二个参数［None，784］代表tensor的shape,也就 是数据的尺寸，这里None代表不限条数的输入，784代表每条输入是一个784维的向量。

import tensorflow as tf

sess = tf.InteractiveSession()

x = tf.placeholder(tf.float32［None) 784］)

接下来要给Softmax Regression模型中的weights和biases创建Variable对象，第1 章中提到Variable是用来存储模型参数的。不同于存储数据的tensor 一旦使用掉就会消失， Variable在模型训练迭代中是持久化的(比如一直存放在显存中)，它可以长期存在并且 在每轮迭代中被更新。我们把weights和biases全部初始化为0,因为模型训练时会自动 学习合适的值，所以对这个简单模型来说初始值不太重要。不过对复杂的卷积网络、循环 网络或者比较深的全连接网络，初始化的方法就比较重要，甚至可以说至关重要。注意这 里W的shape是［784，10］，784是特征的维数，而后面的10代表有10类，因为Label在 one-hot编码后是10维的向量。

W = tf.Variable(tf.zeros([784j 10]))

b = tf.Variable(tf.zeros([10]))

接下来就要实现Softmax Regression算法，我们回忆一下上面提到的公式： y = softmax(lVx + b)。改写成TensorFlow的语言就是下面这行代码。 y = tf.nn.softmax(tf.matmul(x, W) + b)

Softmax是tf.nn下面的一个函数，而tf.nn则包含了大量神经网络的组件，tf.matmul 是TensorFlow中的矩阵乘法函数。我们使用一行简单的代码就定义了 Softmax Regression, 语法和直接写数学公式很像。然而TensorFlow最厉害的地方还不是定义公式，而是将 forward和backward的内容都自动实现(无论CPU或是GKJ上)，只要接下来定义好loss, 训练时将会自动求导并进行悌度下降，完成对Softmax Regression模型参数的自动学习。

为了训练模型，我们需要定义一个loss function来描述模型对问题的分类精度。Loss 越小，代表模型的分类结果与真实值的偏差越小，也就是说模型越精确。我们一开始给模 型填充了全零的参数，这样模型会有一个初始的loss,而训练的目的是不断将这个loss 减小，直到达到一个全局最优或者局部最优解。对多分类问题，通常使用cross-entropy 作为 loss function。Cross-entropy 最早出自信息论(Information Theory )中的信息嫡(与 压缩比率等有关)，然后被用到很多地方，包括通信、纠错码、博弈论、机器学习等。 Cross-entropy的定义如下，其中y是预测的概率分布，乂是真实的概率分布(即Label的 one-hot编码)，通常可以用它来判断模型对真实概率分布估计的准确程度。

"y,(y) =

在TensorFlow中定义cross-entropy也很容易，代码如下。

y_ = tf.placeholder(tf.float32j [None/ 10])

cross_entropy = tf.reduce_mean(-tf.reduce_sum(y_ * tf.log(y),

reduction_indices=[l]))

先定义一个placeholder,输入是真实的label,用来计算cross-entropy。这里的 y_ * tf.log(y)也就是前面公式中的y\log(y£)，tf.reduce_sum也就是求和的Z，而 tf.reduce_mean则用来对每个batch数据结果求均值。

现在我们有了算法Softmax Regression的定义，又有了损失函数cross-entropy的定义，

只需要再定义一个优化算法即可开始训练。我们釆用常见的随机梯度下降SGD( Stochastic Gradient Descent )。定义好优化算法后，TensorFlow就可以根据我们定义的整个计算图(我 们前面定义的各个公式已经自动构成了计算图)自动求导，并根据反向传播(Back Propagation )算法进行训练，在每一轮迭代时更新参数来减小loss。在后台TensorFlow会 自动添加许多运算操作(Operation)来实现刚才提到的反向传播和梯度下降，而给我们提 供的就是一个封装好的优化器，只需要每轮迭代时feed数据给它就好。我们直接调用 tf.train.GradientDescentOptimizer,并设置学习速率为 0.5，优化目标设定为 cross-entropy, 得到进行训练的操作train_step。当然，TensorFlow中也有很多其他的优化器，使用起来 也非常方便，只需要修改函数名即可。

train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)

下一步使用TensorFlow的全局参数初始化器tf.global_variables_initializer,并直接执 行它的run方法。

tf.global一variables_initializer().run()

最后一步，我们开始迭代地执行训练操作train_SteP。这里每次都随机从训练集中抽 取100条样本构成一个mini-batch，并feed给placeholder,然后调用train_step对这些样 本进行训练。使用一小部分样本进行训练称为随机梯度下降，与每次使用全部样本的传统 的梯度下降对应。如果每次训练都使用全部样本，计算量太大，有时也不容易跳出局部最 优。因此，对于大部分机器学习问题，我们都只使用一小部分数据进行随机梯度下降，这 种做法绝大多数时候会比全样本训练的收敛速度快很多。

for i in range(1000):

batch_xsj batch_ys = mnist.train.next_batch(100)

train_step.run({x: batch_xSj y_:, batch_ys})    .    .

现在我们已经完成了训练，接下来就可以对模型的准确率进行验证。下面代码中的 tf.argmax是从一个tensor中寻找最大值的序号，tf.argmax(y, 1)就是求各个预测的数字中 概率最大的那一个，而tf.argmax(y_，1)则是找样本的真实数字类别。而tf.equal方法则用 来判断预测的数字类别是否就是正确的类别，最后返回计算分类是否正确的操作 correct_predition o

correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_J 1))

我们统计全部样本预测的accuracy,这里需要先用tf.cast将之前correct_prediction输 出的bool值转换为float32,再求平均。

accuracy = tf.reduce_mean(tf.castCcorrec^prediction^ tf.float32))

我们将测试数据的特征和Label输入评测流程accuracy,计算模型在测试集上的准确 率，再将结果打印出来。使用Softmax Regression对MNIST数据进行分类识别，在测试 集上平均准确率可达92%左右。

print(accuracy.eval({x: moist.test.images, y_: mnist.test.labels}))

通过上面的这个简单例子，我们使用TensorFlow实现了一个简单的机器学习算法 Softmax Regression,这可以算作是一个没有隐含层的最浅的神经网络。我们来回忆一下 整个流程，我们做的事情可以分为4个部分。

(1 )定义算法公式，也就是神经网络forward时的计算。

(2)    定义loss,选定优化器，并指定优化器优化loss。

(3)    迭代地对数据进行训练。

(4 )在测试集或验证集上对准确率进行评测。

这几个步骤是我们使用TensorFlow进行算法设计、训练的核心步骤，也将会贯穿之 后其他类型神经网络的章节。需要注意的是，TensorFlow和Spark类似，我们定义的各个 公式其实只是Computation Graph,在执行这行代码时，计算还没有实际发生，只有等调 用run方法，并feed数据时计算才真正执行。比如cross_entropy、train_step、accuracy等 都是计算图中的节点，而并不是数据结果，我可以通过调用nm方法执行这些节点或者说 运算操作来获取结果。

我们再来看看Softmax Regression达到的效果，准确率为92%,虽然是一个还不错的 数字，但是还达不到实用的程度。手写数字的识别的主要应用场景是识别银行支票，如果 准确率不够高，可能会引起严重的后果。后面我们将讲解使用多层感知机和卷积网络，来 解决MNIST手写数字识别问题的方法。事实上，MNIST数字识别也算是卷积神经网络的 首个经典应用，LeCun的LeNet5在20世纪90年代就已经提出，而且可以达到99%的准 确率，可以说是领先时代的重大突破。可惜后面因为计算能力制约，卷积神经网络的研究 一直没有太大突破，神经网络也一度被SVM等超越而陷入低谷。在20世纪初的很多年 里，神经网络几乎被大家遗忘,相关研究一直不受重视,这一段是深度学习的一次冰期（神 经网络的研究一共有三次大起大落）。2006年，Hinton等人提出逐层预训练来初始化权重 的方法及利用多层RBM堆叠的神经网络DBN,神经网络才逐渐重回大家视野。Hinton 揭示了神经网络的最大价值在于对特征的自动提取和抽象，它免去了人工提取特征的烦琐， 可以自动找出复杂且有效的高阶特征。这一点类似人的学习过程，先理解简单概念，再逐 渐递进到复杂概念，神经网络每加深一层，可以提取的特征就更抽象。随着2012年Hinton 学生的研究成果AlexNet以巨大优势摘得了当年ImageNet ILSVRC比赛的第一名，深度 学习的热潮被再次点燃。ImageNet是一个非常著名的图片数据集，大致有几百万张图片 和1000类（大部分是动物，约有几百类的动物）。官方会每年举办一次大型的比赛，有图 片分类、目标位置检测、视频检测、图像分割等任务。在此之前，参赛读物都是做特征工 程，然后使用SVM等模型进行分类。而AlexNet夺冠后，每一年ImageNet ILSVRC的 冠军都是依靠深度学习、卷积神经网络，而且趋势是层数越深，效果越好。2015年，微 软研究院提出的ResNet甚至达到惊人的152层深，并在分类准确率上有了突破性的进展。 至此，深度学习在复杂机器学习任务上的巨大优势正式确立，现在基本在任何问题上，仔 细设计的神经网络都可以取得比其他算法更好的准确率和泛化性,前提是有足够多的数据。

接下来的章节，我们会继续使用其他算法在MNIST数据集上进行训练，事实上，现 在的Softmax Regression加入隐含层变成一个正统的神经网络后,再结合Dropout、Adagrad、 ReLU等技术准确率就可以达到98%。引入卷积层、池化层后，也可以达到99%的正确率。 而目前基于卷积神经网络的state-of-the-art的方法已经可以达到99.8%的正确率。
