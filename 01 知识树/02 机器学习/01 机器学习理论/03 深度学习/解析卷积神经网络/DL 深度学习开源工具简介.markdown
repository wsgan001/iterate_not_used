# REF
1. 《解析卷积神经网络》魏秀参




# TODO






  * aaa



* * *




# INTRODUCTION






  * aaa
















深度学习开

自 2006

点燃了最

ImageNe

深度学习

与此同时


































































在Science上发表的深度学习论文[38] [星之火”，接着，2012年Alex-Net在 习在人工智能领域的“燎原之势”。当下

自然语言学习等众多人工智能应用领域。

驾齐驱的还有层出不穷的诸多深度学习 [■比9个目前使用较多的深度学习开源框




架对比


开发语言”、“支持平台”、“支持接口”、是否支持“自动求导”、 训练模型”、是否支持“单机多卡并行”运算等10个方面，对包 、MatConvNet、TensorFlow、Theano 和 Torch 在内的 9 个目前最常

度学习开源框架进行了对比。


h—I hU h—I

表14.1:不同深度学习开发框架对比。

开发框架

开发者

开发语言

支持平台

Caffe

BVLC

C++

Linux, Mac OS X，Windows

Deeplearning4j

Skymind engineering team

Java

Linux, Mac OS X，Windows, Android

Keras

Francois Chollet

Python

Linux, Mac OS X，Windows

MXNet

Distributed (Deep) Machine Learning Community

C++

Linux, Mac OS X，Windows, AWS, Android, iOS, JavaScript

MatConvNet

Oxford University

MATLAB，C++

Linux, Mac OS X，Windows

TensorFlow

Google Brain

C++, Python

Linux, Mac OS X，Windows

Theano

Universite de Montreal

Python

Cross-plat form

Torch

Ronan Collobert, Koray Kavukcuoglu，Clement Farabet

C, Lua

Linux, Mac OS X，Windows, Android, iOS

PyTorch

Facebook

Python

Linux, Mac OS X

支持接口

自动求导

预训练模型

CNN开发

RNNa开发

单机多卡并行

Python, MATLAB

不支持

提供6

支持

支持

支持

Java, Scala, Clojure, Python

支持

提供c

支持

支持

支持

Python

支持

提供d

支持

支持

不支持e

Cd--h, Python, Julia, MATLAB, JavaScript, Go, R, Scala, Perl

不支持

提供

支持

支持

支持

MATLAB

不支持

提供5

支持

不支持

支持

Python, C/C++, Java, Go

支持

提供h

支持

支持

支持

Python

支持

不提供

支持

支持

不支持

Lua, LuaJIT, C, utility library for C++/OpenCL

不支持

提供

支持

支持

支持

Python

支持

提W

支持

支持

支持

a递归神经网络(RNN)是两种人工神经网络的总称。一种是时间递归神经网络(recurrent neural network),另一种是结构递归神经网络(recursive neural network) o时间递归神经网络的神经元间连接构成有向图，而结构递归神经网络利用相似的神经网络结构递归构造更为复杂的深度网络。RNN 一般 指代时间递归神经网络。

^https://github.com/BVLC/caffe/wiki/Model-Zoo chttps://deeplearning4j.org/model-zoo ^https://keras.io/applications/

eTheano作为后端时不支持单机多卡；TensorFlow作为后端时可支持。

'https ://github. com/dmlc/mxnet-model-gallery

5http://www.vlfeat.org/matconvnet/pretrained/

^https://github.com/tensorflow/models/tree/master/siim#Pretrained

^ttps://github.com/torch/torch7/wiki/ModelZoo

^https://github.com/pytorch/vision

14.2.常用框架的各自特点

14.2常用框架的各自特点
14.2.1    Caffe

Caffe是一个广为人知、广泛应用侧重计算机视觉方面的深度学习库，由加州大 学伯克利分校BVLC组开发，总结来说，Caffe有以下优缺点：

✓    适合前馈网络和图像处理；

✓    适合微调已有的网络模型；

✓    训练已有网络模型无需编写任何代码；

✓提供方便的Python和MATLAB接口；

X可单机多卡，但不支持多机多卡；

X需要用C++ / CUDA编写新的GPU层；

X 不适合循环网络；

X用于大型网络（如，GoogLeNet、ResNet）时过于繁琐；

X 扩展性稍差,代码有些不够精简；

X 不提供商业支持；

X 框架更新缓慢,可能之后不再更新。

14.2.2    Deeplearning4j

Deeplearning4j简称DL4J,是基于JVM、聚焦行业应用且提供商业支持的分 布式深度学习框架,其宗旨是在合理的时间内解决各类涉及大量数据的问题。 它与Hadoop和Spark集成，可使用任意数量的GPU或CPU运行。DL4J 是一种适用于各类平台的便携式学习库。开发语言为Java,可通过调整JVM 的堆空间、垃圾回收算法、内存管理以及DL4J的ETL数据加工管道来优化 DL4J的性能。其优缺点为：

✓    适用于分布式集群,可高效处理海量数据；

✓    在多种芯片上的运行已经被优化；

✓    可跨平台运行,有多种语言接口；

✓    支持单机多卡和多机多卡；

✓    支持自动求导,方便编写新的网络层；

✓    提供商业支持；

X 提供的预训练模型有限；

X 框架速度不够快。

14.2.3 Keras

Keras由谷歌软件工程师Francois Chollet开发，是一个基于Theano和 TensorFlow的深度学习库，具有一个受Torch启发、较为直观的API。其优缺 点如下：

✓受Torch启发的直观API;

✓可使用 Theano、TensorFlow 和 Deeplearning4j 后端；

✓ 支持自动求导；


与多种API的机器学习框架，主要面向R、Python和Julia 马逊云服务采用。其优缺点为：


✓ 支持多种语言接口； X 不支持自动求导。

14.2.常用框架的各自特点

14.2.5    MatConvNet

MatConvNet由英国牛津大学著名计算机视觉和机器学习研究组VGG负责开 发，是主要基于MATLAB的深度学习工具包。其优缺点为：

✓基于MATLAB，便于进行图像处理和深度特征后处理；

✓    提供了丰富的预训练模型；

✓    提供了充足的文档及教程；

X不支持自动求导；

X跨平台能力差。

14.2.6    TensorFlow

TensorFlow是Google负责开发的用Python API编写，通过C/C++引擎加

速的深度学习框架，是目前受关注最多的深度学习框架。它使用数据流图集成

深度学习中最常见的单元，并支持许多最新的CNN网络结构以及不同设置的 RNN。其优缺点为：

✓    具备不局限于深度学习的多种用途，还有支持强化学习和其他算法的工 具；

✓    跨平台运行能力强；

✓    支持自动求导；

X 运行明显比其他框架慢；

X 不提供商业支持。

14.2.7    Theano

Theano是深度学习框架中的元老，用Python编写，可与其他学习库配合使用， 非常适合学术研究中的模型开发。现在已有大量基于Theano的开源深度学习

库，包括Keras、Lasagne和Blocks。这些学习库试着在 的接口之上添加一层便于使用的API。关于Theano,:

✓支持 Python 和 Numpy;

✓    支持自动求导；

✓    RNN与计算图匹配良好；

✓高级的包装（Keras、Lasagne）可减少使用时的麻

X 编译困难,错误信息可能没有帮助；

X 运行模型前需编译计算图,大型模型的编译时间较 X 仅支持单机单卡；

X 对预训练模型的支持不够完善。

14.2.8 Torch

Torch是用Lua编写带API的科学计算框架，支持机器学习算法。Facebook 和Twitter等大型科技公司使用Torch的某些版本，由内部团队专门负责定制 自己的深度学习平台。其优缺点如下：

✓ 大量模块化组件,容易组合；







支持丰富的预训练模型；

PyTorch为Torch提供了更便利的接口；

使用Lua语言需要学习成本；

X 文档质量参差不齐；

X 一般需要自己编写训练代码（即插即用相对较少）。



















* * *




# COMMENT
