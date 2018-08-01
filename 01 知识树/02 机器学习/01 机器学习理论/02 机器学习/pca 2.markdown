# pca 2


TODO

1. **要把  [http://106.15.37.116/2018/03/31/pca/](http://106.15.37.116/2018/03/31/pca/) 合并进来，或者把这篇文章里的PCA拆分出去**
2. **实际上感觉他讲的东西还是很多的，需要好好消化。尤其是例子。**
3. **降维会用作主要的算法吗？还是只有再预处理的时候用到？**
4. **把这个归在预处理里面是不是更好？**




# MOTIVE


对降维相关的进行总结。

\(\propto\) 这个符号是正比于的意思。




# 需要的数学知识






  * 熵的定义式等概念。


  * 决策树学习的生成算法。


  * [实对称阵不同特征值的特征向量正交](http://106.15.37.116/2018/03/31/ai-linear-algebra-matrix-transformation-and-eigenvalues/#i-8)


  * 伪逆













# PCA




## 为什么要用PCA？


实际问题往往需要研究多个特征，而这些特征存在一定的相关性。




  * 数据量增加了问题的复杂性。


将多个特征综合为少数几个代表性特征：


  * 既能够代表原始特征的绝大多数信息，


  * 组合后的特征又互不相关，降低相关性。


  * 主成分


即主成分分析。


## 那么我们怎么去求出这种主成分呢？


考察降维后的样本方差


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ih2iaGEBje.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/H36lH5B2l6.png?imageslim)




## 计算投影样本点的方差




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ejm8aL35jc.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/El7amiG0kf.png?imageslim)就是做投影的意义吗？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lCDCbbFDFh.png?imageslim)就是投影到那条直线上的m个数。这m个数总可以进行求方差。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3EI3Jkhd4H.png?imageslim)为什么是这样求方差的？




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/c90hK44CAD.png?imageslim)这个目标函数是关于u的。现在想去求u。这个1/2是要去掉的，笔误。



## 目标函数




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lAIfG3Akch.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7AiiBfcjBl.png?imageslim)即u的模是1。


给一个目标函数，求约束条件下的目标函数的极值，那么就可以用Lagrange

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4E7K53E365.png?imageslim)这个式子能高速我们什么信息呢？一个方阵乘以一个方向等于一个常数乘以这个方向。因此这个u就是去![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CAC7bjE1be.png?imageslim)的特征向量，而且对应的特征值就是这个Lagrange乘子 \(\lambda\)

这就是PCA的推导的核心过程

即什么是主方向？就是保证样本投到我这个方向上来保证方差最大，那个方向就是主方向。


## 方差核特征值


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/aI1GCDEk8K.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/iGCLEH3hac.png?imageslim)给定方差的时候那种分布的熵最大？正态分布。



所以想弄到方差最大的方向上去，其实PCA适合的数据的特征其中一点就是比较适合高斯分布的情况。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2jKGAD7E0i.png?imageslim)用最小二乘拟合的直线也是这个直线



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mckfHfL1Jb.png?imageslim)上面三条都没有很明白


现在说一下降维体现在那里？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fF9l0jKEAg.png?imageslim)我现在可以对![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1E23EDF0hK.png?imageslim)这个方阵求出特征值最大的一个u出来，然后把它列成一列，然后把它特征值次大的一个u拿出来裂成第二列。以此类推，这样，原来的n*n的一个矩阵，通过特征值写成一个新的n*n的矩阵，我只取前面的前k个。那么我就只用了k列，而没有用到n列

由于![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/IlijJffkim.png?imageslim)是对称阵，那么它的不同的特征值对应的特征向量一定是正交的，也就是说第二个方向一定是垂直于第一个的主方向的。所以球出来的维度都是相互垂直的。



PCA的两个特征向量

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3k3gKI6amE.png?imageslim)

OK 到这里PCA基本上介绍好了，那么PCA有什么用呢？


## PCA的重要应用

* OBB树：
    * Oriented Bounding Box **OBB是什么？**
    * GIS中的空间索引
* 特征提取
* 数据压缩
    * 降维
    * 对原始观测数据A在λ值前k大的特征向量u上投影后，获得一个A(mxn)Q(nxk)的序列，再加上特征向量矩阵Q，即将A原来的mxn个数据压缩到mxk+kxn个数据




对于现在的计算机，数据压缩现在基本不是一个重要的事情。关键是特征提取

**这三方面的例子需要补充下**


### PCA的重要应用-去噪


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/a9FL6L9Ebg.png?imageslim)
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/amgBkBFJhh.png?imageslim)
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/b55ffDA94D.png?imageslim)

怎么去噪呢？

先找到主方向，然后把它的次方向和次次方向全部清为0，这样就可以达到去噪的目的。

这个不是PCA的主要作用，但是PCA是可以用来去噪的。


### PCA的重要应用-降维




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4iH5Lg7H1D.png?imageslim)


主要是降维，x，y就发生变化了，也就是说降维后的特征已经不一样了，这个是PCA的恶一个限制。这个时候，特征是什么已经有一点怪异了。就有点混在一起了。 **是的呀。**

但是就因为说不清，所以我们就可以造个词语：主题，来解释。**什么是主题？**


## PCA总结


实对称阵的特征值一定是实数，不同特征值对应的特征向量一定正交，重数为r的特征值一定有r个线性无关的特征向量；

样本矩阵的协方差矩阵必然一定是对称阵，协方差矩阵的元素即各个特征间相关性的度量；




  * 具体实践中考虑是否去均值化；


将协方差矩阵C的特征向量组成矩阵P，可以将C合同为对角矩阵D，对角阵D的对角元素即为A的特征值。


  * \(P^TCP=D\)


  * 协方差矩阵的特征向量，往往单位化，即特征向量的模为1，从而，P是标准正交阵：\(P^TP=I\)。


  * 即将特征空间线性加权，使得加权后的特征组合间是不相关的。选择若干最大的特征值对应的特征向量(即新的特征组合)，即完成了PCA的过程。




**但是一直想问：对于所有的特征类型都可以使用PCA吗？**

注意：

正常去求协方差矩阵，应该是去均值的。去均值的作用是什么？

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0J7L6i9Kj2.png?imageslim)

正常而言我的均值的方向就是我的绿色的箭头的方向。但是如果不去均值，算出来的是紫色的方向，因为你从0开始数，从0开始画一条线使它方差最大。那么就是这个紫色的线。**为什么？**

**有人提了 WCCN和ZCA。不知道是什么？**

如果想把降维后的k维向量还原时，只要乘回投影矩阵就行，但是时会损失数据的，不能完全还原，有损失率。**到底是怎么做的？**






## 关于PCA的进一步考察


若A是m×n阶矩阵，不妨认为m>n，则\(A^TA\)是n×n阶方阵。根据下式计算：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kFeAD9799b.png?imageslim)


我就用PCA的计算过程把A这个矩阵分成了U和V这两个方阵和一个对角阵 \(\Sum\) 的乘积。

\(\Sum\) 的主对角线上放的是什么呢？放的是特征值开放得到的东西 \(\sigma_i=\sqrt{\lambda_i}\) ,这个东西很好，把它叫做奇异值。而这种过程叫做奇异值分解 SVD。

\(\Sum\) 到底是什么？

奇异值不如叫做优值，优秀的那些东西。**为什么优秀？**

仔细看下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/36gb385jDD.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CJBLKJKekG.png?imageslim)这个就是u


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/A1Lfda5jef.png?imageslim)单位化之后的特征向量相乘还是1。


**利害的。**

# 再琢磨一下这个SVD：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CGI7dBl2LH.png?imageslim)

## SVD举例：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/GbiE876fH6.png?imageslim)


注意：虽然 $\sum$ 是唯一确定的，但是 SVD 却不是唯一确定的：


## 奇异值分解不是唯一的

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4ch9iEJBbb.png?imageslim)


## SVD的四个矩阵

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kbJgA5fBAA.png?imageslim)


实践中我们得 到这个分解之后，可以进行数据压缩，或者降维，或者数据整理，比如上图中的只保留前面的前k个。**什么是伪逆？为什么这里谈到了伪逆？**



# 现在做一点拓展：


比如给定n篇文档，而且有若干个主题，通过主题，就可以确定一个单词或者字：


## SVD与pLSA


基于概率统计的pLSA模型(probabilistic latentsemantic analysis, 概率隐语义分析)，增加了主题模型，形成简单的贝叶斯网络，可以使用EM算法学习模型参数。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/c9IiHhHJIe.png?imageslim)

所以可以把SVD应用到求主题的这种思路上去。为什么呢？手头上有m篇文档，每个文档有n个词，那么这就是一个m*n的大矩阵，可以进行奇异值分解SVD得到三个矩阵相乘，然后U的前k个就是所谓的主题，因为这k个是原来的很多的特征转换而来的，这些PCA生成的新的特征就可以称作主题。这个是SVD的一种应用。这个方法在十几年前还是i可以的。。现在用的少了

所以PCA生成的特征虽然意义没有之前明确，但是可以说它是一种主题。

另外，在谈到主题模型的时候，会谈到一个基于概率意义下仍然做主题的一个推导，叫概率化的隐语义分析 pLSA。本质上是通过EM算法来做的。

这个pLSA的东西后面再讲。

附：参数含义


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3mF3m7c98D.png?imageslim)


pLSA模型


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/hIlD37mlA0.png?imageslim)





# SVD举例


假定Ben、Tom、John、Fred对6种产品进行了评价，评分越高，代表对该产品越喜欢。0表示未评价。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/a76Dcemlkl.png?imageslim)





### 评分矩阵




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3eJlLmAfl2.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Cmjhe0DBai.png?imageslim)





## SVD分解




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ahfEL8EI79.png?imageslim)




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ladBeAdc5e.png?imageslim)

注意：这个![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ImLhj9j68B.png?imageslim)里面是V不是VT。要修改下。所以才是前两列

把U的前两列画出来：为什么要把前两列画出来？


## 产品矩阵的压缩




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9h481e9D80.png?imageslim)

在这里面，用夹角余弦去度量它们之间的距离。那么这些点里面5和6是最近的。我们看一下A中5和6是不是最近的，的确是比较接近的。**为什么可以用余弦是度量它们之间距离？**


## 用户矩阵的压缩




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8K9kJEgGd5.png?imageslim)

可以看到Fred和Ben是比较近的。**为什么这两列能表示出这些东西？**

那么对于新用户，怎么进行个性化推荐呢？


## 新用户的个性化推荐




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/a92e57d337.png?imageslim)




因为 \(\Sum^{-1}\)  本身是对角阵，因此转置还是它




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7HHjCibbC0.png?imageslim)

**为什么可以这样乘？ 最后的结果的意义又是什么？**

再把Bob的值放到图上去：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/78Fa7AlaFi.png?imageslim)

由于Bob有三个没有评分，而Ben距离他最近，所以，我给他推荐Ben的最好的评价的东西。

如果新来一个产品的话，就没办法像上面这么用了，因为你这个产品还没做呢。因此这个是牵涉到基于产品的冷启动。**什么是冷启动？有些文章说冷启动是一个不值得过分关注的事情。**

上面这个方法不是基于用户的。基于产品的和基于用户的和基于内容的，都是做的近邻。

所以推荐系统中，可以用近邻以外，然后就是这种SVD的方式。**想知道真正的推荐系统中SVD的方式真的又用到吗？于k-近邻的方式哪个好？有什么优缺点？**


# PCA和SVD总结


虽然这里PCA和SVD是一起说的，但是实际上是不一样的。

矩阵对向量的乘法，对应于对该向量的旋转、伸缩。如果对某向量只发生了伸缩而无旋转变化，则该向量是该矩阵的特征向量，伸缩比即为特征值。

PCA用来提取一个场的主要信息(即主成分分量) ，而SVD一般用来分析两个场的相关关系。

两者在具体的实现方法上也有不同，SVD是通过矩阵奇异值分解的方法分解两个场的协方差矩阵的，而PCA是通过分解一个场的协方差矩阵。

PCA可用于特征的压缩、降维；当然也能去噪等；如果将矩阵转置后再用PCA，相当于去除相关度过大的样本数据——但不常见；SVD能够对一般矩阵分解，并可用于个性化推荐等内容。

PCA其实做的是对一个场的数据做的提取，SVD是两类数据的相关性，比如说用户和产品的相关性。这个是要强调的。**嗯 是的。**

虽然技术上都是做矩阵的分解等。但是再实践层面的用途上是完全不一样的。





## REF

1. 七月在线 机器学习
