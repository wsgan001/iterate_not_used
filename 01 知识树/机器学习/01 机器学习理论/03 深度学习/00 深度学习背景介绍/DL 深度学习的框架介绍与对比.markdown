

# REF：
1. 七月在线 深度学习



# 缘由：
实际上现在的深度学习的框架是在是太多了，当然最厉害的还是Tensorflow，但是还是有一些也比较厉害的，因此总结下，下次有看到好的分析再进行更新。






# 1.Caffe vs Torch vs TensorFlow vs MxNet




![](C:\Users\evo\Desktop\blog_gitserver\gittest\_images\img_5ac1eb0672de3.png)




速度对比：


![](C:\Users\evo\Desktop\blog_gitserver\gittest\_images\img_5ac2124384f3e.png)


训练截断消耗存储（batch=128）**这个图对吗？怎么是naive什么的？**


![](C:\Users\evo\Desktop\blog_gitserver\gittest\_images\img_5ac2125e01409.png)




一些评价：




  * 图像的问题Caffe很方便，训练只需写prototxt  ** 要试下**

  * Caffe是目前产品化最多的库  ，现在借口已经比较稳定了，**很多论文也是用它完成第一版的model，然后再用Tensorflow或者MxNet去复写model。**

  * 应该多关注TensorFlow，毕竟google是亲爹，而且可以用TensorBoard去观察训练的状态

  * MxNet对显存利用率很高，而且Amazon也大力支持。AWS的官方的dl的package是MxNet





## 2.Computation graph vs layer based


计算模型




  * 首先构造好整个计算链路

  * 可以对链路进行优化

  * 分布式调度




![](C:\Users\evo\Desktop\blog_gitserver\gittest\_images\img_5ac1f47fd2ce7.png)


基于层模型




  * 每个层的计算，固定实现 forward/backward

  * 必须手动指定目标GPU卡




## 3.命令式编程 vs 声明式编程


深度学习系统，在编程接口设计上，都采用将一个领域特定语言(domain specific language)嵌入到一个主语言中。例如numpy将矩阵运算嵌入到python中。**这句话没明白是什么意思？什么叫将领域特定语言嵌入到主语言中？**




  * 浅嵌入 => 命令式编程(imperative programming)


    * Numpy / Torch





  * 提供(针对应用)迷你语言 => 声明式语言(declarativeprograming)


    * Caffe / theano / tensorflow





在命令式编程上MXNet提供张量运算，声明式编程中MXNet支持符号表达式。将两者衔接。**什么意思？还是没明白什么是命令式编程，什么是声明式编程。**




# COMMENT：


**实际上这个感觉可以删掉了，不系统，而且已经是旧的了，不知道现在的情况是什么样子的。**
