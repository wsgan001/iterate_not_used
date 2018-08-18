
# Neural Style


TODO

- 还是要好好看论文的，感觉现在的这种课程也只能讲到这了。


这个是 15年提出的，现在已经是基本比较平常了。

最早的论文：![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/gFGH1aH3Lk.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/ac3fbed46f.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/jAA4IJl5cj.png?imageslim)

怎么做的呢？

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/6gGd8GEljb.png?imageslim)

他开始的时候没有想做成这个文艺的东西，他在想，我们用图像的距离比对的时候一直用的是 L2 loss，他在想有没有更好的 loss function ，然后就做了一堆的试验。

然后他发现了一个 loss ：style loss。居然能够定义两幅图风格维度的差异度。

L2 loss 是评价两幅图在内容上的接近程度，style loss 是定义在抽象的风格上的差异度。

既然我能定义风格上的差异度，有能定义内容上的差异度，那我把这两个东西合一下呢。

一般的 CNN 的分类任务都是为了学习 这个 w，但是这个地方，他是把 VGG 或 AlexNet 拿过来，然后固定住这个 w 然后调这个 x，让损失最小。<span style="color:red;">惊了，脑洞啊。</span>

调 x 是为了满足我的内容与原始的 content 输入最接近，我的风格与 style 输入最接近。

所以，我定义了两个loss。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/K9F3D7lH2a.png?imageslim)

说明一下这个公式：

这个 loss 是分为 content 的 loss 和 style 的 loss。这个content loss 还是比较简单的，就看他的输出与输入的内容 的l2 loss。

这个 style loss，我们都知道从卷积层出来之后就是很多个 feature map，假如说是 64*64*16 的矩阵，说明有 16个 64*64  的feature map，然后把每两个feature map之间做了一个点乘，这样就有了 16*16 个点乘的结果，这 16*16 个矩阵叫做 Gram matirx，语法矩阵，然后，我用两个语法矩阵去做相同的差值。<span style="color:red;">这个还是有些不清楚。</span>

这个 \alpha 和 \beta 是分别是内容更像一点还是 风格更像一点。

调节权重:偏风格or偏内容

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180812/F575IDhKBh.png?imageslim)


老师还是推荐去看原始的论文。这个 neural style 没有多困难。






## REF

- 七月在线 opencv计算机视觉
