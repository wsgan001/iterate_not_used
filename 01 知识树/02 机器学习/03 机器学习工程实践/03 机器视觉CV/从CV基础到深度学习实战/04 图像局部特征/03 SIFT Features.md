

TODO

- 基本没听懂，还是要先结合别的资料看下，然后再听，毕竟先知道大概是什么样的，再听的话会能理解些。
- 在第5节课的内容开始有17分钟这节课的内容回归，主要是针对没有听懂的说明，也是需要看下的。

我们前面讲的都是单一图像中的寻找简要的信息。

但是我们很多的任务是在多帧图像中找到对应的元素，比如说 match point ，这个就涉及到 图像匹配。

在图像匹配中 最常用的就是 SIFT

下面再提到角点、兴趣点、特征点 都表示的同一个意思，不进行区分了。

比如说我找到了一个图像上的特征点：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/HdIC6e84ik.png?imageslim)

可是在真实的情况下，我们可能想找到不同尺度下的对应

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/3e1FmabHFc.png?imageslim)

所以我们想定义一个函数，这个函数可以在同样一个区域在不同尺度下的响应应该是一致的。

我们为了实现这种多尺度下的图像匹配，我们使用了高斯滤波：

高斯卷积核是进行多尺度变换的唯一的线性核。
Gaussian Blur：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/ijj0B1EiBf.png?imageslim)

上面这个 G 就是尺度高斯函数，xy 是空间坐标， $\sigma$ 越大，说明模糊的程度越高，尺度越大，$\sigma$ 越小，模糊的程度越低，表示尺度越小，所以尺度是由我们的 $\sigma$ 决定的。

利用不同大小的高斯核来得到不同的尺度，换句话说就是模拟人眼来看物体

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/4ELgia0Ffc.png?imageslim)


在 SIFT 里面，他用到了 Difference of Gaussians 这个函数来检测某一尺度上对 DOG 的响应值。

正常使用 LOG，

我们看这个图像，为什么说是 Block detection ，因为我们用 LOG 或DOG 与图像做卷积实际就是一种相关运算，什么情况下响应最剧烈？两个区域相似的时候。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/J6L0hHa7gB.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/Lk0jklB8h9.png?imageslim)

DOG 是 LOG 的近似，画成 2D 的图像：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/h5CC9Dchm8.png?imageslim)

在 DOG 里面，我们构造这样一个尺度空间，在三维空间上寻找极值，Detect maxima and minima of difference-of-Gaussian in scale space

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/LBk5ecIiBj.png?imageslim)


我们看一下 SIFT 是怎么实现的：

对原图像进行高斯模糊：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/3lAdkE3I4B.png?imageslim)

然后 SIFT 里面构建了高斯金字塔，用不同的 $k\sigma$ 来模糊，模糊后做二倍的降采样，在模糊，在模糊，每一组叫做一个 octave。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180809/aKIcDf1508.png?imageslim)

我们不做下采样，只做高斯模糊就叫做 DOG 算子进行特征点检测。而 SIFT应用了 DOG，然后进行了下采样。

后面会有同学看到有的人没有构建金字塔，直接对原图像进行高斯滤波，得到一组图像之后就进行特征点检测了。

下面我们看一下尺度空间的构建：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/mK64Kd1gmF.png?imageslim)

Computation in Gaussian scale pyramid

我们直接切入正题，以 SIFT 中的多尺度表达来讲一下

首先，金字塔的组数，就是有多少个 octave 。我们先不看一个octave 里面有多少个图片，我们先看戏下 有多少个 octave，这个是由图像大小决定的，在opencv 里面，比如说，我们用的是 512*512 的图像，那么个数就是 $\frac{log(512)}{log(2)}-2$ 的，为什么要限制层数然后还要 减2 ？如果不退两层，最后就是 1*1 的，这个1*1 的做高斯模糊就没有意义，通常退到第六层 8*8 的，这个组数就是这么算出来的，

在 SIFT 里面默认的是 4个 octave ，每个 octave里面是5组图像，在检测极值点对原始图像进行高斯平滑，有可能会导致图像信息丢失

所以，SIFT 是 lowe，把原图像先扩展，比如原来是 216*216 的先扩展成 512 的，这样增多了特征点的个数，这个增大的这层定义成 -1 层，然后就是 0层等等。看到的时候不要觉得奇怪。

然后看金字塔：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/GLK46KI2aB.png?imageslim)

第一层 octave 在程序里面定义了一个变量，intvl $\frac{intvl+3}{3}$ 金字塔里面最基本的有 三个图像，然后额外生成三个图像，后面我们会说为什么额外要生成三个图像。

<span style="color:red;">这个地方真的没看懂</span>

在每个 octave 内部，$\sigma$ 都是相同的，$\sigma$ 会每次都在 octave 中计算。

比如说第0 层的第一个图像就是原始的 input ，然后第0层第2个图像就是 innput 做高斯卷积，卷积的参数就是 $\sigma_1$，然后第三个图像是用的第2个图像做得高斯卷积，每一个图都是使用的前一个图，而不是第一个图，

我们看到的 octave2 的第一个图像是 octave1 的倒数第三个图像进行下采样得到的，这就是我们这个地方为什么定义一个 3，我们是把第三个图像下采样得到的。<span style="color:red;">没明白？</span>

这样构建出了整个高斯金字塔。为什么每一层内还要有很多图像？而且不同层还要进行下采样？主要是为了保证尺度的连续性。

比如说，DOG空间是5个尺度，所以我在octave 里需要有 6个 高斯图像，因为第一幅最后一幅需要做差，所以 6-1=5 。

为什么是尺度连续的呢？假设 s=3,$k=2^{1/3}$ 然后在 DOG 的空间里面，第一个分别是 $\sigma$ $k\sigma$ 第二个octave 两项分别是 $2\sigma$ 和 $2k\sigma$ 由于这样没办法比较极值，所以我们需要在中间添加一些高斯像，比如 $\sigma$ $k\sigma$ $k^2\sigma$ 所以终极爱你想是 k k^2 k^3 这样左右才都有极值，我们可以在第一个 octave 的中间三个里面选极值，尺度是怎么算的呢？是由于它的下采样得到的。比如说 $\sigma$ 的下采样是 $2k\sigma$ $2k^2\sigma$ $2k^3\sigma$ 首先，$2k\sigma$ 就等于 $2^{4/3}$ 刚好与 $k^3\sigma=2^{3/3}$ 是连续的，所以这样尺度就是连续的了。

所以每次要在高斯的空间添三项，构建他每组塔有 3+3 的图像，对应的 DOG 空间有 3+2 的图像，这样我们的 DOG 空间才是连续的。

<span style="color:red;">基本没懂</span>

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/93jClCdKEi.png?imageslim)

得到这样的图像，这样的图像在啊立体的空间中取极值，取到了这个极值他是离散的，在 SIFT 里面还要进行差值运算，这里面就不说了，用到的是一个 hession矩阵。

把边界点去掉，我们会得到这样的一些图像上的特征点：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/I9dK21c9gd.png?imageslim)

他包括了 x y $sigma$

这个时候，我们拿到了特征点，是具有尺度不变性的。

接下来，我们看一下旋转不变性：

- How do we represent the patches around the interest points?
- How do we make sure that representation is invariant?

我们只是找到了角点，我们不但要找到方向，还要找到周围像素对它的贡献。

- Compute orientation histogram
- Select dominant orientation


Keypoint orientations

- Compute orientation histogram
- Select dominant orientation

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/4l75ak2f9h.png?imageslim)

采样是圆形的，说明一下，直方图是10度，总共 36个直方图，代表像素的梯度方向，柱子的长短代表梯度的幅值。



基本听不懂了，从上面就听不懂了，从一开始的SIFT 就没明白。
