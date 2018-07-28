# Dropout

# 4.Dropout（随机失活）

## Dropout ( 随机失活) ： 别一次开启所有学习单元

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/hGH4Clk0cF.png?imageslim)


### 代码如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/3chdAea5IL.png?imageslim)

H1是一个ReLU层，U1生成H1相同shape的随机数，并且随机数小于0.5时为False，大于0.5时为True。然后H1*=U1就得到了过滤后的H1。

但是predict的时候，这个信息过不过去的数学期望就是这个值乘以p。**为什么？为什么这个地方讲数学期望了？**






## 实际实现 ：


上面的方式存在一个问题，用户只关心predict的速度，不关心你训练用了多长时间。

所以，可以把预测阶段的概率p转移到训练阶段。注意，是以数学期望的形式转移的。**还是有点不明白，而且，一个Ture False的矩阵除以p是什么意思？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/327GGFjji8.png?imageslim)




## Dropout理解：


防止过拟合 的第1种理解方式：

* 别让你的神经⽹网络记住那么多东西( ( 虽然 CNN 记忆⼒力好? )?

* 就是⼀一只猫而已 ，要有一些泛化能力


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/8G9IKA2g5d.png?imageslim)

从上面的图可看出，有部分信息是冗余的。

防止过拟合的第2 种理解方式 ：

* 每次都关掉一部分感知器 ， 就可以看作一个新模型 ，那么多个模型对最后的结果做预测会降低overfitting的风险。


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/lf6hE5dh47.png?imageslim)




## 需要补充的

**将参考文献的内容补充进来，这个讲的还是不明确。**

对 Dropout 想要有更细致的了解，参见：

* 2014, Hinton, etc《 Dropout: A Simple Way to Prevent Neural Networks from Overfitting》

* 2013, Stefan Wager, etc 《Dropout Training as Adaptive Regularization》


## REF
