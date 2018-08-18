
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

有一点要注意：这个 RNN 虽然在不断的迭代，但是每次迭代的函数和参数都是相同的。注意，这个地方说的相同指的是已经训练好的RNN在使用的时候参数是相同的。

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

输出层是这样的：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/F0jhLghcAb.png?imageslim)

上面这个是最基本的 RNN，从这个出发，我们想一下


Preview of fancier architectures

RNN attends spatially to different parts of images while generating each word of the sentence


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/H8DGkfEl9m.png?imageslim)

这个论文就是 Show Attend and Tell .

那么有没有什么更好的 RNN？

我们正常的多层 RNN 就是这样的：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/a8FL6Jj4kG.png?imageslim)

但是 LSTM 是这样的：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/bhJBJbLI8l.png?imageslim)

绿色的后面又加了一些黄色的东西，这些就是 gate。

OK，我们说一下这个 LSTM

LSTM 是上世纪 90 年代的 97 年的

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/bFgK7Gmh92.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/lG5g9Ki9K0.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/19g3Lc4Hdb.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/672e4Ikf17.png?imageslim)


Long Short Term Memory

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/aKf5caLi1d.png?imageslim)

4n*2n 中的 4 是因为有 4 个 gate，即 i,f,o,g 。那么2 是因为有 x 和 h。

<span style="color:red;">上面这个图没有看明白</span>

第一步：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/lJlA6j74FG.png?imageslim)

前一个时刻的 cell 的状态就是 $c_{t-1}^l$ 。

这个 x 就是我们之前说的 image 的feature ，假如说是 4096 。 这个 ![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/6ihcIG5h2C.png?imageslim) 是点运算。

<span style="color:red;">为什么是 c 乘以 f 呢？</span>

第二步：

i 是前一个过程，g 也是前一个过程。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/KaDdFiBBBf.png?imageslim)

得到的这个 $c_t^l$ 就是当前的 cell state。

第三步：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/4ibElA3kbk.png?imageslim)

当前的 cell state 过一个 tanh 之后，在经过一个 o 的 gate。

第四步：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/7i1Lk80gLk.png?imageslim)

h 是下一个 higher layer ，也可以是 prediction，也就是说，如果这个 单元是 多层中的一层，那么输出就是到更高的一层，如果是最后一层，那么输出就是预测。

为什么要做的这么复杂？有点模仿人的神经元之类的吧。

每一个 gate 都有一个说法，具体的看一下 之前推荐的第二篇的 blog。


OK，我们看一下相邻的两个时间戳的时候的状态的转化：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/Df18fdjL3b.png?imageslim)

<span style="color:red;">上面这个图没有写出 h 传入的饿时候到哪里的，还是说传入的 h 并没有被用到，只是作为输出了？嗯，用到了，在第一步的第一个公式里，用来更新这四个门。哈哈，终于知道这个门是怎么更新的了，一直奇怪这门是怎么更新的。嗯，还想知道更多关于这个门的，公式写的带简略了。</span>

<span style="color:red;">老师没想明白一个问题：为什么RNN 和 LSTM 里面不用 ReLU 呢？而是使用  sigmoid 呢？不知道这个有没有人解答过。</span>

OK，对比一下 LSTM 和 RNN ：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/FbG4bkD4DA.png?imageslim)


那么大家想一下 ResNet 与 LSTM 有什么区别？

ResNet 对于平常的网络来说，相当于 LSTM 对于 RNN：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/JaBB2mJA5f.png?imageslim)


了解动态梯度变化：

这个老师没有讲。

Cute backprop signal video: http://imgur.com/gallery/vaNahKE

上面这个连接是 RNN 和LSTM 的动态的梯度变化。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/lB73d23b5L.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/gLj2hB3C19.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/38aib40d59.png?imageslim)

LSTM 的一些变种：LSTM  variants and friends：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180816/DLBCIlbcEI.png?imageslim)

这个GRU 是一个基于 LSTM 的一个变种，是图上的蓝色的线表示的，老师只用过LSTM，这个 GRU 不知道现在应用的怎么样，应该没有很火。


OK，我们总结一下：


- RNNs	allow	a	lot	of	flexibility	in	architecture	design
- 传统的RNN 很简单，但是效果不是很好
- Common	to	use	LSTM	or	GRU:	their	additive	interactions	improve	gradient	flow
- Backward	flow	of	gradients	in	RNN	can	explode	or	vanish.
Exploding	is	controlled	with	gradient	clipping.
Vanishing	is	controlled	with	additive	interactions	(LSTM)
- Better/simpler	architectures	are	a	hot	topic	of	current	research
- Better	understanding	(both	theoretical	and	empirical)	is	needed.

为什么LSTM好呢？

一个是对于梯度的把我比较好，exploding 和 vanishing 的问题都能控制住，如果梯度太小了，他的 gate 可以控制。

有同学问 BNN 对梯度消失有影响吗？老师说：有的，batch norm 让样本尽量的不一致，每一个 batch 的样本尽量跟前面的样本不重叠，如果不重叠，相当于在network 里加了很强的 disturbance，那么你学出来的 loss 会很大，那么你 loss 大，间接的，你的 gradient 就会很大，这时候你的梯度就不会容易消失了。所以大家都在用 BNN 。<span style="color:red;">怎么突然有同学问了这个 BNN 的问题？什么是 BNN？大家都在用 BNN 是什么意思？</span>

老师又说 LSTM 用不用 BNN 不清楚。可能也用。<span style="color:red;">到底什么是 BNN？有没有使用？什么情况下使用？</span>



OK，上面我们就把 RNN 和 LSTM 简单的说了下，下面我们看下基于 RNN 和LSTM 的 image captioning 的具体做法。




## REF

- 七月在线 opencv计算机视觉
