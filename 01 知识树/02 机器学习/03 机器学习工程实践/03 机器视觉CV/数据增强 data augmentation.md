# 数据增强 data augmentation

TODO

- 这篇文章还么看，要仔细总结下。
- 数据增强的方法还是要整理的，最好能系统化，比如从哪些方面进行数据增强，然后再分具体的方法。
- 实现的方法也要补充进来
- python 的和 C++ 的都要总结下。
- 除了 CV 领域之外的也会有数据增强吗？比如 NLP ？确认下。


收集数据准备微调深度学习模型时，经常会遇到某些分类数据严重不足的情况，另外数据集过小容易造成模型的过拟合。



## 数据增强常用方法

<span style="color:red;">要补充各种方法的实现，而且要是工业等级实际使用的方式。如果只是理论上可行要明确。</span>

- Color Jittering：对颜色的数据增强：图像亮度、饱和度、对比度变化（此处对色彩抖动的理解不知是否得当）；
- PCA  Jittering：首先按照RGB三个颜色通道计算均值和标准差，再在整个训练集上计算协方差矩阵，进行特征分解，得到特征向量和特征值，用来做PCA Jittering；
- Random Scale：尺度变换；
- Random Crop：采用随机图像差值方式，对图像进行裁剪、缩放；包括Scale Jittering方法（VGG及ResNet模型使用）或者尺度和长宽比增强变换；
- Horizontal/Vertical Flip：水平/垂直翻转；
- Shift：平移变换；
- Rotation/Reflection：旋转/仿射变换；
- Noise：高斯噪声、模糊处理；
- Label shuffle：类别不平衡数据的增广，参见海康威视ILSVRC2016的report；另外，文中提出了一种Supervised Data Augmentation方法，有兴趣的朋友的可以动手实验下。

参考：

- [海康威视研究院ImageNet2016竞赛](https://zhuanlan.zhihu.com/p/23249000)使用的数据增强方法；
- [知乎小白在闭关](https://www.zhihu.com/people/yan-zhang-xi/answers)对数据增强的理解；
- [深度学习之图像的数据增强；](http://www.cnblogs.com/gongxijun/p/6117588.html)





## REF

- [data augmentation 数据增强方法总结](https://blog.csdn.net/u010555688/article/details/60757932)
