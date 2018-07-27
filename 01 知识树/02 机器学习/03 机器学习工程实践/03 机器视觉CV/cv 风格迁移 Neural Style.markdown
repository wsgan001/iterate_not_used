
# REF：

1. 七月在线 深度学习




# 缘由：


总结一下风格迁移中的知识


# 要点：




## 简单介绍


让机器模仿绘画风格：

在艺术领域，艺术家可以通过风格和内容的相互交融来创造不同的化作。深度神经网络将画作中的风格和内容本身进行分离，并将风格做迁移，引入到另外一个图片内容中，最总达到一个风格迁移的工作。这个很像Photoshop中的滤镜，然而滤镜是人为设定好的一系列动作处理集合，只能针对特定的风格的图片，对于不同特征的图片，需要选择不同的滤镜，因此比较机械。

模仿包括：




  * 图像内容的重建   **这个怎么重建？**


  * 绘画风格的重建


content_image + style_image=> output image


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/GHg4ILl86F.png?imageslim)

文章地址：https://arxiv.org/abs/1508.06576

代码地址：https://github.com/jcjohnson/neural-style


## 怎么做的？




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/3I0E5Eak1f.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/aAjG9Gl1cF.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/eCg5BGEBga.png?imageslim)




## 网络模型和结构


网络模型：VGG 16-layers

网络结构：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Jj6jgJb6bI.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/1iJ0al7meJ.png?imageslim)


在本文中，只使用了前5层的网络 'conv1_1'(a)，'conv2_1'(b)，'conv3_1'(c)，'conv4_1'(d)，'conv5_1'(e)。VGG网络主要用来做内容识别，在实践中作者发现，使用前三层1，2，3已经能达到比较好的内容重建工作，4、5两层保留了一些比较高层的特征，丢失了一些细节。




## 实验流程图：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/90c4G755aF.png?imageslim)



* a:conv1-1
* b:conv1-1 and conv2-1
* c:conv1-1,conv2-1 and conv3-1
* d:conv1-1,conv2-1,conv3-1 and conv4-1
* e:conv1-1 conv2-1,conv3-1,conv4-1 and conv5-1




## 损失函数=内容重建+风格重建


* 内容重建损失函数：


\[L_{content}(\overrightarrow{p},\overrightarrow{x},l)=\frac{1}{2}\sum_{i,j}^{ }(F_{ij}^l-P_{ij}^l)^2\]


利用梯度下降，content loss对F求导：


\[\frac{\partial L_{content}}{\partial F_{ij}^{l}}=\begin{cases}(F^l-P^l)_{ij} & \text{ if } F_{ij}^l>0 \\ 0 & \text{ if } F_{ij}^l<0\end{cases}\]

F is the activation of the i-th filter at position j in layer I.

P is the feature representation from the original image.




  * 风格重建损失函数：


\[E_l=\frac{1}{4N_l^2M_l^2}\sum_{i,j}^{ }(G_{ij}^l-A_{ij}^l)^2\]

\[L_{style}(\overrightarrow{a},\overrightarrow{x})=\sum_{l=0}^{L}w_lE_l\]

a and x be the original image and the image that is generated and A and G their respective style representations in layer /.

We define feature correlations by Gram matrix G,where G is the inner product between the vectorized feature map i and j in layer I：

\[G_{ij}^l=\sum_{k}^{ }F_{ik}^{l}F_{jk}^l\]


  * 最终损失函数：


To generate the images that mix the content of a photograph withe the style of a painting, we jointly minimise the distance of a white noise image from the content representation of the photograph in one layer of the network and the style representation of the painting in a number of layers of the CNN,So let p be the photograph and a be the artwork.

\[L_{total}(\overrightarrow{p},\overrightarrow{a},\overrightarrow{x})=\alpha L_{content}(\overrightarrow{a},\overrightarrow{x})\]


## 实验结果


1.对于同一张content对象，给style风格图片不同时，输出的图像不同。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/IhbD3lGifb.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/CaGgE9ab35.png?imageslim)

2.Total loss 中的\(\alpha /\beta \)不同。

* 从上到下表示的时不同conv层的feature进行style，conv1->conv5是一个从整体到局部的过程。

* 从左导游表示的时不同的\(\alpha /\beta \)的比例，\(10^{-5}->10^{-2}\)是指更注重style还是更强调content。**厉害**




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/mkDiab7gba.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/fL40DD470K.png?imageslim)






# COMMENT：




**基本没明白，也没怎么细讲，要找个详细的自己做下来。**

Neural Style
(Caffe + Python) https://github.com/fzliu/style-transfer
