



Boosting

Boosting 是一族可将弱学习器提升为强学习器的算法。这族算法的工作机 制类似：先从初始训练集训练出一个基学习器，再根据基学习器的表现对训练 样本分布进行调整，使得先前基学习器做错的训练样本在后续受到更多关注, 然后基于调整后的样本分布来训练下一个基学习器；如此重复进行，直至基学习器数目达到事先指定的值 T ,最终将这 T 个基学习器进行加权结合.

Boosting 族算法最著名的代表是 AdaBoost , 其描述如图8.3所示,其中 $y_i\in\{-1,+1\}$ , $f$ 是真实函数.

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/L1e4dhGI9J.png?imageslim)

AdaBoost算法有多种推导方式，比较容易理解的是基于“加性模型”(additive model),即基学习器的线性组合

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/Ibh8A99KDJ.png?imageslim)


来最小化指数损失函数(exponential loss function)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/iCmBciEKkb.png?imageslim)



若 $H(x)$ 能令指数损失函数最小化，则考虑式(8.5)对 $H(x)$ 的偏导

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/6G0GE2e18G.png?imageslim)

令式(8.6)为零可解得

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/1aiKgLa10D.png?imageslim)

因此，有

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/f786Bb8IBf.png?imageslim)


这意味着 $Sign(H(x))$ 达到了贝叶斯最优错误率.换言之，若指数损失函数最小化，则分类错误率也将最小化；这说明指数损失函数是分类任务原本 0/1 损失函数的一致的(consistent)替代损失函数.由于这个替代函数有更好的数学性质，例如它是连续可微函数，因此我们用它替代 0/1 损失函数作为优化目标.

在AdaBoost算法中，第一个基分类器 $h_1$ 是通过直接将基学习算法用于初始数据分布而得;此后迭代地生成 $h_t$ 和 $\alpha_t$ ，当基分类器 $h_t$ 基于分布 $\mathcal{D}_t$ 产生后，该基分类器的权重 $\alpha_t$ 应使得  $\alpha_th_t$最小化指数损失函数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/giajI29eHj.png?imageslim)

其中 $\epsilon_t=P_{x\sim \mathcal{D}_t}(h_t(x)\neq f(x))$ 考虑指数损失函数的导数

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/m2LcIE24fB.png?imageslim)

令式(8.10)为零可解得

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/E51A141hkf.png?imageslim)

这恰是图8.3中算法第6行的分类器权重更新公式.

AdaBoost算法在获得 $H_{t-1}$ 之后样本分布将进行调整，使下一轮的基学习器 $h_t$ 能纠正 $H_{t-1}$ 的一些错误.理想的 $h_t$ 能纠正 $H_{t-1}$ 的全部错误，即最小化

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/8kic4a7Acb.png?imageslim)

注意到 $f^2(x) = h_t^2(x) = 1$ ,式(8.12)可使用 $e^{-f(x)h_t(x)}$ 泰勒展式近似为

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/BdCbg5cGaJ.png?imageslim)

于是，理想的基学习器

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/7em554Ig8h.png?imageslim)




注意到 $\mathbb{E}_{x\sim \mathcal{D} }[e^{-f(x)H_{t-1}(x)}]$ 是一个常数。令 $\mathcal{D}_t$ 表示一个分布

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/8Fd84BjHme.png?imageslim)

则根据数学期望的定义，这等价于令

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/0F3d3KmLlj.png?imageslim)

由 $f(x),h(x) \in\{-1,+1\}$ ,有

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/JFD20lEkh3.png?imageslim)

则理想的基学习器

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/Kie51ElK9c.png?imageslim)


由此可见，理想的 $h_t$ 将在分布 $\mathcal{D}_t$ 下蕞小化分类误差.因此，弱分类器将基于分布 $\mathcal{D}_t$ 来训练，且针对 $\mathcal{D}_t$ 的分类误差应小于0.5.这在一定程度上类似“残差逼近”的思想.考虑到 $\mathcal{D}_t$ 和 $\mathcal{D}_{t+1}$ 的关系，有

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/hBj6KbehEF.png?imageslim)



这恰是图8.3中算法第7行的样本分布更新公式.

于是，由式(8.11)和(8.19)可见，我们从基于加性模型迭代式优化指数损失函数的角度推导出了图8.3的AdaBoost算法.

Boosting 算法要求基学习器能对特定的数据分布进行学习，这可通过“重赋权法”(re-weighting)实施，即在训练过程的每一轮中，根据样本分布为每个训练样本重新赋予一个权重。对无法接受带权样本的基学习算法，则可通过 “重采样法”(re-sampling)来处理，即在每一轮学习中，根据样本分布对训练集重新进行采样，再用重采样而得的样本集对基学习器进行训练.

一般而言，这两种做法没有显著的优劣差别.需注意的是，Boosting 算法在训练的每一轮都要检查当前生成的基学习器是否满足基本条件(例如图8.3的第5行，检查当前 基分类器是否是比随机猜测好)，一旦条件不满足，则当前基学习器即被抛弃, 且学习过程停止.在此种情形下,初始设置的学习轮数 T 也许还远未达到，可能导致最终集成中只包含很少的基学习器而性能不佳。

若采用“重采样法”，则可获得“重启动”机会以避兔训练过程过早停止, 即在抛弃不满足条件的当前基学习器之后，可根据当前分布重新对训练样本进行采样，再基于新的采样结果重新训练出基学习器，从而使得学习过程可以持续到预设的 T 轮完成.


从偏差-方差分解的角度看，Boosting主要关注降低偏差，因此 Boosting 能基于泛化性能相当弱的学习器构建出很强的集成.我们以决策树桩为基学习 器，在表 4.5 的西瓜数据集 3.0a 上运行AdaBoost算法，不同规模(size)的集成及其基学习器所对应的分类边界如图8.4所示。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180628/gidmGgiaC5.png?imageslim)





# REF
1. 《机器学习》周志华
