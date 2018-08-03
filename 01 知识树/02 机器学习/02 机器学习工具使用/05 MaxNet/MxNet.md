

TODO

- **没有自己使用过**




# 缘由：


降到了MxNet，因此总结下。


# 要点：




## 1.MxNet初始化


MxNet官网如下：

http://mxnet.incubator.apache.org/index.html

很多同学用过dmlc的Xgboost。**难道Xgboost与MxNet有关系？dmlc是什么？Xgboost怎么使用的？之前只听说过。GBDT**


## 2.MxNet实现MLP




### 读入数据




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/IfCd4G68iE.png?imageslim)




### 定义网络

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Eh47G8LFJK.png?imageslim)

### 训练

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/K8mfl2ag5g.png?imageslim)




### 预测




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/c7lHH00maH.png?imageslim)




## 3.MxNet与ResNet


ResNet通过直接将input直接送到后续层里，在一定程度上克服了梯度衰减问题，可以训练更深的网络。但是更深的网络结构，同时加上BN层之后，训练的参数量变大，对存储的消耗变大。**BN层是什么？**

试试MxNet，它对显存的利用率高一些。**什么是显存的利用率？为什么对显存的利用率更高一些？**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/74dJe0lC0G.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/7A5cIfL1iL.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/8CJK9Jd469.png?imageslim)








# REF：

1. 七月在线 深度学习
