
下面，我们介绍下 RNN 和LSTM 的一些背景知识：

这块很重要。


首先，我们要能检测出图上的物体：Object Detection

因此需要一个 CNN：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/9512HG0b66.png?imageslim)

然后，我们怎么把图像和描述结合起来训练呢？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/G8BIkAIIkA.png?imageslim)


我们可以看到，这个地方也有一个 CNN，这个 CNN 把上面的 object detection 的 head 也就是 softmax 不要了。然后，提出来的 feature 作为START ，然后，开始了 RNN，输出的label 是man ，然后，这个man 作为下一个的输入，等等，然后输入 frisbee，输出一个状态 END。<span style="color:red;">嗯，感觉还是非常平常的，不过</span>

这个是相关的参考文献：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/EkKml44CIG.png?imageslim)



那么推荐大家几个比较好 RNN 的 tutorial：

- http://www.wildml.com/2015/09/recurrent-neural-networkstutorial-part-1-introduction-to-rnns/
-  http://colah.github.io/posts/2015-08-Understanding-LSTMs/
(highly	recommended)
-  http://deeplearning.net/tutorial/lstm.html

第二个是极力推荐的，讲的非常非常好。老师的研究组里面介绍 LSTM 都是拿第二个来讲。<span style="color:red;">嗯，要看下。</span>

RNN 提供了很多的灵活性，很多应用都建立在RNN 基础上。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/c76kgbjai9.png?imageslim)

- one to one 就是传统的CNN，就是做 cv 任务的
- one to many 就是 image captioning
- many to one Sentiment Classification sequence of words-> sentiment  就是文本处理的，给了一堆话，比如说 Q&A，最近google 有一篇文章是给一个文章然后做阅读理解
- many to many Machine Translation 就是比如从西班牙文翻译成中文。
- many to many Video classification on frame level 就是一个分颜色的是一个 image，



我们看下 tutorial：

RNN：

什么是 RNN

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/i3A2GlG7aj.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/8ee7GIBBaG.png?imageslim)

前一帧的输出作为这一帧的输入：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/i9cH8ACgCG.png?imageslim)

可以看到，是 state 和 input 的一个组合通过一个函数之后输出一个 state。

有一点要注意：这个 RNN 虽然在不断的迭代，但是每次迭代的函数和参数都是相同的。

传统的 RNN 是做什么的呢？

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/jEHmkj7mjE.png?imageslim)

传统的 RNN ，类似 马尔科夫 或者 CRF，有一个隐变量在里面。

<span style="color:red;">为什么要通过一个 tanh呢？</span>

这些 W 就是要进行迭代的。

下面举个例子：

Character-level language model example

假设我们的词库里面就有 helo 这四个字母，然后我想说一个 hello：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/7e2abeif5K.png?imageslim)

因此，我们的输入就是类似这样：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/jc2GdA4ekc.png?imageslim)

然后 隐含层就是这样：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180815/2BjjbCcmdd.png?imageslim)


W_xh 就是从 x 到h 的权重， W_hh 就是从 h 到 h 的权重。

所以，根据上面这个公式，来迭代每次的隐含层。
