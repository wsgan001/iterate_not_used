# 神经网络 NN


正式对神经网络进行总结。从来源到原因到使用。




# 神经网络介绍


## 它的功能很强

神经网络一大类机器学习方法：




  * 支持处理图像、文本、语音以及序列等多种类型的数据。


  * 而且支持分类、回归、预测等多种应用


可见还是很利害的。


## 主要的几种网络


人工神经网络的基础形态是前向全连接网络（FC，Full Connected Forward Network），同时它还拥有多种变形，这些变形构成了目前深度学习的主要内容：




  * 卷积神经网络（CNN）属于部分连接网络，是深度学习核心结构之一。


  * 递归神经网络（RNN）是更为复杂的网络结构，能够很好应对序列数据。


  * 自编码器（AutoEncoder）是一种数据特征学习，类似PCA的作用。




## 神经网络的缺点






  * 神经网络理论支撑不如其他机器学习⽅方法 **现在发展怎么样了？**


  * 而且神经网络以工程实践为导向，在实践的过程中充满了各种 trick 以及 dark art。比如调参等。**了解的不够，需要补充。**




尽管人工神经网络和生物神经网络有一定的关联，但目前学术界普遍态度是不刻意强调。**嗯**


#





# 神经网络大概是个什么样的结构




## 大概的结构




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/bCHJ8Aggfc.png?imageslim)

理论上来说，可以对任何形式的输入数据进行学习。**为什么一定要是层级结构？之前好像看到过不是层级结构的神经网络。对，的确看到过，而且好像很厉害的。**




## 浅层的神经网络




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7h0LGdc4b9.png?imageslim)

一般浅层的网络主要看浅层宽网络。实际上保证足够多的单元数目的话，单隐层网络可以拟合任意函数的，这个是有证明的。（**真的假的？要看一下，多少算足够？**）

与 SVM/RF/LR 等算法一样，浅层宽网络能够很好处理向量样本的分类问题，但是和其他算法比，训练较慢且容易过拟合。


## 深度神经网络


即 DNN （Deep Neural Network）


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/FkbgLh7jIE.png?imageslim)






# 组成神经网络的基本单元




## 神经元的介绍


神经元是构成神经网络的基础单元。同样也叫做感知器（Perception）

一个独立神经元包含：\(\{n\, w_i\, b \,h\}\)：




  * 输入：n 维的向量 \(x\)


  * 线性加权： \(z=\sum_{i=1}^{n}w_ix_i+b\)  其中w是针对每个x的一个权重。


  * 激活函数： \(a=h(z)\)  激活函数要求要是非线性的，而且还要容易求导，因为用后向传播来更新权重的时候就是用的链式求导的法则。


  * 输出标量：\(a\)  一个神经元数出的是一个标量。




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mgk3I4Ie8b.png?imageslim)






## 神经元的激活函数


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/B0f8G19i2F.png?imageslim)

\(f\) 就是传递函数，也叫激活函数 (activation function)，实际上神经元的激活函数还是有很多种的，现在最常用的激活函数就是ReLU。**不知道有没有什么更新？**

为什么需要一个非线性的激活函数呢？




  1. 我们所处的环境是很复杂的，不是所有的信号都需要理会，而有些信号又很重要，因此这个传递函数就是允不允许信号通过，以及，以多大的程度通过。有些要压到0，有些要全部通过。


  2. 如果没有非线性的action，那么即使有很多的 layer，但是最后得到的结果对于原始输入来说仍然是线性的，这意味着此时的很多的 layer 与单单一层是没有区别的。


激活函数的种类还是很多的：


  * Sigmoid


  * Tanh(双曲正切)


  * ReLU  修正线性单元   用的最多的


  * Leaky ReLU 对ReLU做一些修正


  * ELU  对Leaky ReLU 做一些修正


  * Maxout


**图片重新确认下，并且补全 而且再找一下不同的激活函数的对比。**


### sigmoid 函数


这个在逻辑回归里面经常用，它输出是 0~1 ，因此可以把输出的值看作是概率值，而概率值可以作为一个判别的标准。比如有了概率之后，我们通常可以给定一个分界线，比如 0.5 ，这样就可以来判定这个样本是正样本还是负样本。

以前这个是最常用的激活函数，但是现在一般只用在输出层，中间层很少使用。。这是因为它有两个缺点：




  1. sigmoid 函数的两头过于平坦，导致如果 x 大于 5 时候，对 sigmoid 函数求导的话基本是0，这样在应用链式法则进行求导的时候，导数非常容易是0，会导致 [梯度消失](http://106.15.37.116/2018/04/25/gradient-explosions-and-gradients-disappear/)。


  2. 而且，它的输出值是 0~1 ，没有负数，这样是不对称的，因此这会导致比如，输入的时候我还是有负值的，但是我下一层的输出就全是大于0的了。**但是这个具体会有什么影响？**




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/m01KiAC5Ai.png?imageslim)

### tanh 函数


这个比sigmoid好一点


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/LEfeEB067C.png?imageslim)




### ReLU 和 Leaky ReLU


ReLU（Rectified linear unit）是现在最常用的，在卷积网络中基本上是标配。

它有下面几个特点：




  * 不存在 saturate（饱和）区域


  * 收敛速度比 sigmoid / tanh 函数快，这个是在实践中证明了的。


  * 计算高效简单


但是它也有一个缺点：


  * 如果你的输入不巧全部在ReLU的左侧，即 Dead Area，那么这个神经元就挂掉了，再也没法激活，在后向传播时，相应的神经元的参数都不会更新。但是呢，这个发生的概率很小，因为是一批一批训练的。所以虽然可能很脆弱，但是大家都在用。


所以，为了能够使它不至于挂掉，改进出了 Leaky ReLU：


  * 在小于0的时候，斜率为很小的一个比如0.1，这样在反向传播的时候也能少许的改变一下权重。




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Hm8c961KBG.png?imageslim)




### ELU


所有的LU相关的都是从ReLU出来的，这个ELU 也是对ReLU的左侧修改了下。

**需要补充**![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9A9glHe4ig.png?imageslim)






# 将神经元稍微组合起来


实际上在机器学习种，我们已经学过很多的分类算法了，那么为什么还要用神经网络这种方法呢？到底有什么利害的地方？

我们从解决线性分类问题和非线性分类问题的角度来看一下它厉害在哪里：


## 一般的分类方法是如何处理非线性的分类问题的？



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/baE87lljf1.png?imageslim)

对于普通的线性分类问题来说，LR、linear SVM，都可以作线性分割。他们要做的就是得到一条决策边界。然后把不同的类别分开而已。**LR 和 SVM的核函数再看下？**

那么，对于非线性可分的问题，比如说上面的第二个图，这个时候已经找不到一个超平面来分割两个类别了，那么怎么处理呢？

实际上我们还是有几种方法的：




  1. 如果这个时候我们用 LR 或者 SVM，我们就可以引入一些非线性的 feature，比如构造一些平方项或者立方项作为特征：\(x_1x_2\)，\(x_1^2\)，\(x_2^2\) ，\(x_1^2x_2\) 等。


  2. 如果我们用 SVM 的话可以加一个 kernal ，把特征做一个映射，然后再做分割。


  3. 我们也可以做一个分类器的组合，比如用多个 weak learner 去组成一个 GBDT ，这样就可以组合成一个决策增强树这样一个分类器。


上面这些方法当然是可以的，但是呢，左边这个只是一个简单的情况，在实际的应用中，比如说一个电商的推荐系统，它的特征很多，导致维度很高，在这样的情况下你就没办法像左边一样可视化，也就无法知道你的样本到底是什么样的分布。

所以这时候第一个方法就会有两个问题：


  * 当你看不到样本的分布的时候，你就不知道到底是 \(x_1^2\) 有用 还是 \(x_2^2\) 有用，还是两个的乘积有用。


  * 而且假如你有100万个特征，你去做特征组合，那么最后的维度是非常高的，而且你不知道那个是有用的。


而第二方法这个时候效果也不是很好。**为什么不好？**

在工业界，这个时候大部分会使用第三个方法，即用多个线性分类器组合起来的非线性分类器做分类，但是这也是非常麻烦的。



然而神经网络理论上来说，这些都不是问题，它可以完成任何的一种分布的划分。




## 那么神经网络对于非线性可分是怎么做的？




### 首先我们作一个简单的问题，把下面的圈和差两类样本点分割开


下图中，差是正样本点，圈是负样本点。如果用线性分类器，就很难做。因为不是线性可分的。

但是如果能够把这个区域抠出来，那么就可以分了。而正好右图的两条线围成的范围可以看作p1和p2对应的分类器的交集。OK，拿我们先看看怎么完成交集，即逻辑与。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HGH35mIgmB.png?imageslim)




### 用神经元完成逻辑与


这样的一组\(\theta\) （-30，20，20）就可以完成这个操作  **嗯 是的**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/47fc6lhI62.png?imageslim)




### 用神经元组合解决问题


那么我们这个时候再找一组\(\theta\)来达成p1对应的分类功能，也可以再找一组\(\theta\)来达成p2对应的分类的功能。然后在这个的基础上取（-30，20，20）来对两个分类进行与。这样就完成了非线性切分。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Fb507GLkag.png?imageslim)




### 顺便用神经元完成逻辑 或




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ihb93c720k.png?imageslim)

那么，也就是说单个 Perception 可以完成 and 和 or 这样的操作。


### 对线性分类器的 与 或 的组合


完美对平面样本点分布进行分类，分成不相接的四类。

神经网络会找到一个线性的分类器去贴近轮廓的边缘，每个边缘都对应一个分类器，这个分类器会说自己的左边是红色，右边是绿色等等，然后可以把他们的意见汇总一下，我们要做到的是，听取大家的意见，有了每个边缘的判定之后，就可以用一组参数\(\theta\)and或者or把一块抠出来，**没明白这个例子是要做什么？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2JafFL8KIA.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CF24fCl4GD.png?imageslim)


* 加了一层就可以求and，就可以把一块区域抠出来，得到开的凸区域或者封闭的凸区域。
* 如果再加一层，可以在他的基础上or，刚才抠出来的区域就可以用or组起来，所以你任意的空间的形状都可以去通过增加神经元的方式，把抠出来的区域再重新并在一起。


or和and的选择取决于训练的时候自己做的调整。

所以神经网络可以对一个问题不断的分解来解决一件事情，

到底是加隐层还是加神经元？

**决策区域和区域形状是什么意思？与单隐层双隐层有什么关系？**








# 继续组合成一层层的神经网络


把相同结构的单元组合在一起，构成神经网络的层。




  * 输入层，输入向量


  * 中间层（隐含层）


  * 输出层，输出向量，比如说输出一些概率分布，比如 softmax 就是。用于预测、分类以及回归




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/J6L6KAAfCj.png?imageslim)

OK，到这里，神经网络基本上就从神经元建立起来了，那么这个网络到底怎么起作用的呢？




# 神经网络的前向传播




神经网络的前向传播的计算的过程如下


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/bcm0i4DA32.png?imageslim)

**需要补充吗这个地方？**












# 神经网络的反向回归算法 BP


训练神经网络的目的是寻找 W 和 b ，使得 LossFunction 值最小。而BP算法就是反复利用链式规则，求解dw和db：




  1. forward 计算一次


  2. 逐层计算 backward， 得到各个层的dw，db


  3. 应用SGD算法，更新 w，b




## BP算法介绍


BP算法，也叫做\(\delta \)算法




  * 正向传播求损失


  * 反向传播回传误差，根据误差信号修正每层的权重。


**正向传播很容易理解，但是这个反向传播的误差是怎么传递回来的？下面这个图没有明白？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4C8dH0l7Jc.png?imageslim)


## 误差是如何展开到前面的层的？



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/E8feGEELKA.png?imageslim)

标准答案为d，输出为o，

那么根据L2 loss来评判，输出层的误差如下：（\(\frac{1}{2}\) 是为了求导方便而添加的）

\[E=\frac{1}{2}(d-O)^2=\frac{1}{2}\sum_{k=1}^{j}(d_k-o_k)^2\]

误差展开至隐层：（每个o是前面的net的输出然后经过一个f得到的）

\[E=\frac{1}{2}\sum_{k=1}^{j}[d_k-f(net_k)]^2=\frac{1}{2}\sum_{k=1}^{j}[d_k-f(\sum_{j=0}^{m}\omega_{jk}y_j)]^2\]

展开至输入层：**是的**

\[E=\frac{1}{2}\sum_{k=1}^{j}[d_k-f[\sum_{j=0}^{m}\omega_{jk}f(net_j)]]^2=\frac{1}{2}\sum_{k=1}^{j}[d_k-f[\sum_{j=0}^{m}\omega_{jk}f(\sum_{j=0}^{n}v_{ij}\chi_i)]]^2\]

那么我们现在要做的就是求这个E的最小值。因为这个E已经把里面的参数全部展开了。


## 使用SGD来迭代减小误差 随机梯度下降


误差\(E\)已经有了，怎么调整权重让误差不断减小？

所以现在就是要求这个函数的最小值，有很多的办法，比如二阶的办法，但是DL中很少看到二阶的优化方法，因为Hesen矩阵的量级太大了，用DL求解的时候存储不下，因此通常的情况下只能用一阶去求解。没明白这一段。

一阶里面最简单的方式就是SGD，即随机梯度下降。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/E48Id15IJF.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/imGl0AB54g.png?imageslim)


\[\Delta \omega_{jk}=-\eta \frac{\partial E}{\partial \omega_{jk} }\; j=0,1,2,\cdots ,m;k=1,2,\cdots ,l\]

\[\Delta v_{ij}=-\eta \frac{\partial E}{\partial v_{ij} }\; i=0,1,2,\cdots ,n;j=1,2,\cdots ,m\]

上面的这个E对于\(\omega\)求导的时候由于是复合函数，因此使用的是链式法则

**嗯，这个地方学习了梯度下降之后就比较明白了。**

有个地方要注意，我们在图像，在NLP中出现的SGD，实际上都是指的是mini-batch GD，即一批一批数据训练的。习惯把它叫做SGD。比如tensorflow 、caffe等实际上做的事情都是在mini-batch里面求的。



# 通过一个例子来说明整个计算过程


比如这个例子：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/m36elkAf87.png?imageslim)

我们需要拿到的结果是0.01和0.99，我们希望找到合适的w和b来得到这个输出。

我们线随手设定一些w和b。


## 前向运算：


仅仅是想看我的输出与标准答案的区别有多大。

\[net_{h1}=w_1*i_1+w_2*i_2+b_1*1\]

\[net_{h1}=0.15*0.05+0.2*0.1+0.35*1=0.3775\]

\[out_{h1}=\frac{1}{1+e^{-net_{h1} } }=\frac{1}{1+e^{-0.3775} }=0.59326999\]

\[out_{h2}=0.596884378\]



\[net_{o1}=w_5*out_{h1}+w_6*out_{h2}+b_2*1\]

\[net_{o1}=0.4*0.59326999+0.45*0.596884378+0.6*1=1.105905967\]

\[out_{o1}=\frac{1}{1+e^{-net_{o1} } }=\frac{1}{1+e^{-1.105905967} }=0.75136507\]

\[out_{o2}=0.772928465\]



\[E_{total}=\sum \frac{1}{2}(target-output)^2\]

\[E_{total}=E_{o1}+E_{o2}=0.2748110083+0.023560026=0.298371109\]


## 反向传播：




### 求 $w_5$ 试试


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kA8CIJhDc3.png?imageslim)

也就是说 E 对于 \(\w_5\) 的偏导满足：

\[\frac{\partial net_{o1} }{\partial w_5}*\frac{\partial out_{o1} }{\partial net_{o1} }*\frac{\partial E_{total} }{\partial out_{o1} }=\frac{\partial E_{total} }{\partial w_5}\]

那么开始挨个求：


\[E_{total}=\frac{1}{2}(target_{o1}-out_{o1})^2+\frac{1}{2}(target_{o2}-out_{o2})^2\]


\[\frac{\partial E_{total} }{\partial out_{o1} }=2*\frac{1}{2}(target_{o1}-out{o1})^{2-1}*(-1)+0\]

\[\frac{\partial E_{total} }{\partial out_{o1} }=-(target_{o1}-out_{o1})=-(0.01-0.75136507)=074136507\]

\[out_{o1}=\frac{1}{1+e^{-net_{o1} } }\]

\[\frac{\partial out_{o1} }{\partial net_{o1} }=out_{o1}(1-out_{o1})=0.75136507(1-0.75136507)=0.186815602\]

\[net_{o1}=w_5*out_{h1}+w_6*out_{h2}+b_2*1\]

\[\frac{\partial net_{o1} }{\partial w_5}=1*out_{h1}*w_5^{(1-1)}+0+0=out_{h1}=0.593269992\]

\[\begin{align*}\frac{\partial E_{total} }{\partial w_5}&=-(target_{o1}-out_{o1})*out_{o1}(1-out_{o1})*out_{h1}\\&=0.74136507*0.186815602*0.593269992=0.082167041\end{align*}\]

所以开始改变\(w_5\)：

\[w_5^+=w_5-\eta *\frac{\partial E_{total} }{\partial w_5}=0.4-0.5*0.082167041=0.35891648\]

类似的：

\[w_6^+=0.408666186\]

\[w_7^+=0.511301270\]

\[w_8^+=0.561370121\]


### 求\(w_1\)试试




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/h52CDaGHHi.png?imageslim)

这里要知道，\(E_{total}\)分成了两部分，而\(h_1\)的out与这两部分都有关系，因此要加在一起。

\[\frac{\partial E_{total} }{\partial w_1}=(\sum_{o}^{ }\frac{\partial E_{total} }{\partial out_o}*\frac{\partial out_o}{\partial net_o}*\frac{\partial net_o}{\partial out_{h1} }）*\frac{\partial out_{h1} }{\partial net_{h1} }*\frac{\partial net_{h1} }{\partial w_1}\]

\[w_1^+=w_1-\eta *\frac{\partial E_{total} }{\partial w_1}=0.15-0.5*0.000438568=0.149780716\]

**厉害的例子，最好自己手动算一下加深印象。而且，最好后面所有的网络都这么算一下。就比较厉害了。**



OK，到现在为止我们已经知道到底这个神经网络的整体的计算过程以及参数的调整过程是怎样的了，也就是说主体的东西OK了，那么下面我们看看还有什么需要注意的：




# 真正做的时候需要注意的初始化及预处理




## 如果不对数据进行处理的话存在的问题


* 如果输入数据各个维度数据是相关的，将导致各个权重互相影响，不利于优化算法寻找局部最优。**CRF呢？它有这么多维度，怎么解决的？能不能用在这里？**

* 同样如果各个维度的scale不一致，将导致对应的Error Surface容易出现狭窄区域，不利于优化算法寻找局部最优。如果一个维度的范围是-1~+1 ，另一个维度的范围是从+100到-100， 那么会出现狭窄区域，而因为我们用的是随机梯度下降，会导致在这个狭窄区域震荡，很难回到中心这个地方，因为太窄了，很容易跳出这个区域。**再琢磨一下**



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0mlgGbbjHl.png?imageslim)



**什么是Error Surface？**





## 输入数据的处理


对于线性的神经网络，做去除相关 会使效果大大提升。比如PCA：

* 丢掉小的成分，留主成分，当然，这个会损失一些维度。
* 另外PCA选取后的东西，要做一个白化处理，压成圆形


对向量数据必须采用PCA+White处理，一般数据只需要做到 Zero Center 以及 Normalization 。比如图像数据，而NLP数据甚至不需要处理。

**这部分再总结一下，到底怎么做？做不做的后果会怎样？要清楚，而且原样本还要不要用了？**

一共是下面的五步。**再仔细在PCA哪一节中总结下。**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/53DKeiJ1Kf.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0C8aBAfbcL.png?imageslim)



##  权重 W 的初始化


利⽤用随机分布初始化W，来消除对称性，因为对称的网络每个神经元将学习到相同的权重，如果不消除对称，那么每个神经元学到的东西是一样的。因为对单个层的每个神经元来说，输入是一样的。**是的**

那么随机初始化权重的方法具体为：

* 采用小的数值：0.001 * randn(D, H) ~ u(0, 1) 就是简单的用0.001乘以一个随机数。

* 根据 Fan-in 的大小计算：w = randn(n) / sqrt(n) 就是使每一层的Var基本不变，计算如下：**没明白？什么是Fan-in？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/BJE9AJ5A2I.png?imageslim)

w = randn(n) / sqrt(n)




## 添加惩罚

一般用下面这三种方法：

* 我们要确保在损失函数的基础上有正则化的手段，可以在Loss Function 增加 L2/L1 norm项目（**这个L2/L1 norm项目是什么？**）**这个公式里面的\lambda 要取多少？每一层是不是要使用相同的\lambda ？  **\(L=\frac{1}{N}\sum_{i}^{N}L_i+λ\sum_{j}^{K}W_j^2\)

* 在训练的过程中对样本/权重增加噪声，也就是每次我不是使用原始的样本值来训练的，而是给样本添加一点噪声。比如说给input图像加一点噪声，或者把input图像左右颠倒一下，在比如我的样本图像使27*27的，而我的网络的输入设定为24*24的，而这个24*24就是从27*27里面随机切取的，这个也可以认为使增加了噪声。**利害，没想到可以这样，相当于在输入的时候就是一个随机滑动的窗口作为输入。**

* Dropout ：即训练过程中保持部分网络的连接，一些神经元不参与计算。


这些方法和手段在不同的领域中有不同的应用。**也要总结下。**



# 神经网络的几个比较重要的问题




## 层次与单层神经元个数多少才合适？


理论上说单隐层神经网络可以逼近任何连续函数（只要隐层的神经元个数足够多）。为什么呢？因为可以求AND，而很多的很多的线的AND拼在一起就可以拟合出一个合适的曲线。

虽然从数学上看表达能力一致，但是多隐藏层的神经网络比单隐藏层的神经网络工程效果好很多。比如1000个节点的单隐层效果不如2层的100个节点的。**为什么呢？原因是什么？**

对于一些分类数据（比如CTR预估里），3层神经网络效果优于2层神经网络，但是如果把层数再不断增加(4,5,6层)，对最后结果的帮助就没有那么大的跳变了。**层数与效果之间的关系到底是什么样的？CTR预估是什么？ **

注意上一段说的分类数据不是对图像而言的，图像的话，更多层会表达更深层次的信息。这说明图像的数据信息非常的丰富，丰富到层次往上拉伸，却还没有到overfitting的程度，比如100多层。图像数据比较特殊，是一种深层(多层次)的结构化数据，深层次的卷积神经网络，能够更充分和准确地把这些层级信息表达出来。**为什么图像是一种深层的结构化数据？为什么呢？到底根本原因是什么？而且到底要多少层才是好的？与图像的像素数目有关系吗？**


## 过拟合要怎么避免或者处理？


提升隐层层数或者隐层神经元个数，神经网络“容量”会变大，空间表达力会变强：如下图所示，只有一个隐层，只是改变了隐层神经元的个数：**那么隐层层数与隐层神经元数目有没有什么等价关系呢？或者说是不是二者的提升从某整程度上来说是等价的？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3Bm96D2C79.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fDllHleE0i.png?imageslim)

过多的隐层和神经元节点，会带来过拟合问题。但是不要因为害怕overfitting，而去把神经网络的参数量降下来。因为overfitting，说明它能力强，但是需要一些限制，而降低参数量可能会导致能力不不足。**一直想知道多少叫做过多？**


## 多少layer和neural合适？


**不要试图通过降低神经网络参数量来减缓过拟合**，而要用一些正则化手段或者dropout。比如说L1正则化，L2正则化。或者dropout。正则化不是用来减少参数的，是用来约束我们的搜索空间。dropout是随机失活。**为什么不要试图通过降低参数数量来减缓过拟合？正则化和dropout起到的作用是相同的吗？**

一般没有公式可以确定layer的个数和每层的neural的个数，有一些paper讲了一些指导性的建议，但是工业界做的事情是针对已经有的比较成功的网络的结构做一些简单的变更和调整。


## BP只能得到局部最优，那么怎么才能得到尽可能大的范围内的局部最优呢？


首先，多层网络的 Error Surface 是非常复杂的，因此存在很多的局部最优，而简单的使用BP算法，只能得到一个局部最优解，但是局部最优解也不是说不可以，只要我实际中可以用就行，那么找不到全局最优，就想找一个尽可能大的范围内的局部最优。怎么办呢？


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/DDai4iJ0I6.png?imageslim)




### Mini Batch SGD


一次选32、64、或128个样本进行SGD。

注意：这个地方的Batch的大小不能太大，因为你的 GPU 的 memory 有可能放不下这些样本。

在神经网络中经常会出现这种梯度极度变化而且不平滑的情况，因此这个时候的步长的调整就需要想一些方法了：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9704Ji8Chl.png?imageslim)

**什么意思？这张图？**


### SGD-momentum


**没怎么讲**

动量法不是更新 W ，而是更新 "更新W" 的速度。

当位于距离较长的”坡”的时候，及比较平滑的时候，动量法可以加快滑动速度，当在平缓区域的也能保持一定的速度。

典型值：α=0.99，ε=0.01  **这个值怎么得到的？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/3BH3GCm7bd.png?imageslim)




### SGD-Adaptive learning rate


**没怎么讲**

多层神经网络总，每个权重的梯度相差较大，采用统一的学习率不合适，因此可以对每个权重选择不同的更新参数


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lDcgb8fhmG.png?imageslim)

### SGD-rmsprop


**没怎么讲**

这个用的也是比较多的，使用 recent running average 平滑


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/4f7ebaIi04.png?imageslim)

### 那么到底怎么才能选择合适的SGD算法呢？


实际上，在不同的情况下，SGD算法也没有说有一个固定很好的：

* 小批量的样本 vs 大批量的样本
* 深度网络 vs RNN vs 浅层宽网络
* 超参取值


因此，我们只能从简单的算法实验起，在少量样本上尝试不同的算法效果


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/fbAdm97jch.png?imageslim)

**epoch是什么？到底怎么选择？什么叫从简单的算法实验起来？**


## 训练网络的时候还有什么需要注意的吗？

* 首先，要有试车，即确保在小批量样本上是收敛的，如果说小批量样本都不能保证它的 loss 越来越小的话，那么SGD算法一定选择错了。
* 要添加一些监控训练过程中的仪表盘程序
* Early Stop机制 **这个是什么？没有说？ 比如说我每做一个epoch就测试一下validation accuracy，如果training accuracy 在上升，但是validation accuracy在下降，说明过拟合了，就触发了Early Stop。实际中这个是怎么做的？模块里有吗？**

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HLfeEjL7ic.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9K2g8gddCm.png?imageslim)



所有的样本都跑完一遍，叫做一个epoch。与 batch 不同，batch是一次迭代所用的样本数量。因此，以epoch为单位，感觉还是比较平滑的，以batch为单位就看起来非常的震荡。



# 对自己实现的BP算法进行数值校验


OK，基本上应该知道的都已经OK了，包括计算方法，预处理，过拟合的调整。那么在实践中我们还会遇到一个问题：

如果自己要写相关的BP算法的时候，由于里面有各种求导的过程，因此比较容易出错，因此需要有必要的手段进行检查。那么怎么检查呢？

我们先看一下导数的定义：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/KHh0Je2IDE.png?imageslim)

所以，当我反向传播求出 w 之后，我可以用这种方法验证 loss 对于这个 w 的偏导：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ci6FeE8793.png?imageslim)

我求出这个值之后，就可以跟我的BP算法比较一下。**还是没明白？怎么比较的？这个不是w与w的偏导之间的差吗？返回值说明了什么呢？**

注意：这种校验方法，一次只能校验一个权重

实际上由于如果需要自己设计一些新的神经网络模型的时候，类似的验证都必须的，不然你根本不知道你的网络设计的对不对。

**这一点放在这里是不是不合适？ 可以单独拿出来做成 tips**








# 一个很Nice的网站：


tensorflow playground


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/6j1cCF5C0H.png?imageslim)

**刚尝试了一组，但是不知道为什么输出一直在震荡，而且震荡的非常厉害，什么原因呢？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ih0hLlk6e1.png?imageslim)

**还有一次learning rate设置为1，算着算着没有了，看了下线上的 w 都非常小。**



# COMMENT：


**感觉很多东西讲的不够，而且很多东西可以拆分出来，比如与或非的实现，过拟合的处理及防止的方法。反向传播原理及例子。都是可以拆分出来的更深挖掘的。**

**课程对应的实例还没有补充进来**






# 可能需要添加的：


优化算法需要计算dW和db，BP算法就是计算W和b导数的算法，而BP算法核心是链式规则（Chain Ruler）：

\[\frac{dy}{dt}=\frac{dy}{dx}\frac{dx}{dt}\]

\[\frac{\partial y}{\partial x_i}=\sum_{ι=1}^{m}\frac{\partial y}{\partial u_ι}\frac{\partial u_ι}{\partial x_i}\]


## BP的计算过程

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ff5ChGHA71.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/41IK94l4AC.png?imageslim)

注：之所以要计算对于 x 的导数，是因为 x 作为中间变量，我用链式法则在求它之前一层的 w 的偏导时候会用到对于这个中间变量的求导，因此，这个对于 x 的求导实际上是为了求在上一层对 w 求偏导的时候用的。



## REF

1. 七月在线 深度学习
2. 七月在线 机器学习
