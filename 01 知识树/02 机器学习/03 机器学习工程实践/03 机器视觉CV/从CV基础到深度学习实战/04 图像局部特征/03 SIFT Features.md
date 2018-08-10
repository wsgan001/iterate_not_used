
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

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180810/EH87F4I88F.png?imageslim)
