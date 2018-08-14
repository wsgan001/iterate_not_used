
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

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/38F4cA7j2g.png?imageslim)

one to one 就是传统的CNN
one to many 就是 image captioning
many to one 就是文本处理的，给了一堆话，
