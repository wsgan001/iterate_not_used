

# 使 CBIR 更快

直接用神经网络去学习分桶。

OK，我们回到我们的 PPT：

我们现在已经大概知道怎么搭建 CBIR 系统了，那么有没有什么办法使得你的 CBIR 系统更快？

有个 CVPR 2015 的 paper，他在想，如果你在外面用 LSH hash 去做一个空间的划分，或者预先分桶的操作，那么这一步可不可以让神经网络去学习呢？

他们搭建了一个神经网络，然后让神经网络学会对原本的图像进行分桶，让他在训练的过程中就学会这个图应该在那个桶里，哪些相近的图应该在那个桶里面。

那么去做检索的时候，直接把图片送到神经网络中，就拿到了一个分桶的编号，然后直接把桶里的图片取出来就行。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/6BciKklJg8.png?imageslim)

我们看看他是怎训练的，他用的是 Alexnet ，alexnet 维度是 4096*1000 的全连接，然后他为了学习这个分桶，他插了一层进去，这个隐层是 128 个神经元，而这 128 个神经元外面套了 sigmoid，然后，与 0.5 做比较，这样就把128个连续值->128 个 0~1 的值-> 128 个 0 或 1 的数字串。

<span style="color:red;">讲到这里的时候，视频声音混乱了，有大概3分钟的话完全没听到。</span>


项目对应的应该就是这个项目：[caffe-cvprw15](https://github.com/kevinlin311tw/caffe-cvprw15)
他里面是有 examples 的。


可以同时参考这两个：
- [《基于deep learning的快速图像检索系统》](http://blog.csdn.net/han_xiaoyang/article/details/50856583)
- https://github.com/HanXiaoyang/image_retrieval



<span style="color:red;">还是不错的，要自己走一遍。</span>



图像的数据可以在这里：

利⽤以下数据集构建图像检索系统
-  http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html

这个是香港中文大学的paper 里面带的数据集，是百度云的。
