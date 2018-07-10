---
author: evo
comments: true
date: 2018-04-03 12:14:29+00:00
layout: post
link: http://106.15.37.116/2018/04/03/cv-image-recognition-and-location/
slug: cv-image-recognition-and-location
title: cv 图像识别与定位
wordpress_id: 2754
categories:
- 随想与反思
tags:
- '@todo'
- '@want_to_know'
---

<!-- more -->


# 缘由：


对图像识别与定位进行总结，之前还有一篇，要合并到这里来。


# 1.两种思路：





 	
  * 思路1：视作回归

 	
  * 思路2：借助图像窗口


图像识别与定位：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36a6ca577d.png)


ImageNet

实际上有 识别+定位 两个任务


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36a844175c.png)





# 思路1：看作回归问题


4个数字，用L2 loss/欧⽒氏距离损失?


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ab003cbd.png)


步骤1:



 	
  * 先解决简单问题，搭一个识别图像的神经网络

 	
  * 在AlexNet VGG GoogleLenet ResNet上fine-tune一下




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ac785fcb.png)


步骤2:



 	
  * 在上述神经网络的尾部展开

 	
  * 成为classification + regression模式




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36adf6f5ec.png)


步骤3:



 	
  * Regression(回归)部分用欧氏距离损失

 	
  * 使用SGD训练




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36afa014a4.png)


步骤4:



 	
  * 预测阶段把2个“头部”模块拼上

 	
  * 完成不同的功能




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36b045c403.png)


Regression(回归)的模块部分加在什么位置？



 	
  * (最后的)卷积层后

 	
  * 全连接层后




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36b260c852.png)


能否对主体有更细致的识别？



 	
  * 提前规定好有K个组成部分

 	
  * 做成K个部分的回归




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36b317c78d.png)


应用：如何识别人的姿势？



 	
  * 每个人的组成部分是固定的

 	
  * 对K个组成部分(关节)做回归预测 => 首尾相接的线段




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36b437087c.png)





# 思路2: 图窗+识别与整合





 	
  * 类似刚才的classification + regression思路

 	
  * 咱们取不同的大小的“框”

 	
  * 让框出现在不同的位置

 	
  * 判定得分

 	
  * 按照得分高低对“结果框”做抽取和合并




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36babd9309.png)


实际应用时



 	
  * 尝试各种大小窗口

 	
  * 甚至会在窗口上再做一些“回归”的事情




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36bb71a02b.png)


想办法克服一下过程中的“参数多”与“计算慢”



 	
  * 最初的形式如下




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36bc68a039.png)


想办法克服一下过程中的“参数多”与“计算慢”



 	
  * 用多卷积核的卷积层 替换 全连接层

 	
  * 降低参数量




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36beb4fe25.png)


想办法克服一下过程中的“参数多”与“计算慢”



 	
  * 测试/识别 阶段的计算是可复用的(小卷积)

 	
  * 加速计算




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36c03a5c46.png)





# 物体识别




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36c2331135.png)


再次看做回归问题？


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36c43e0b48.png)


其实你不知道图上有多少个物体…


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36c55e38a1.png)


试着看做分类问题？


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36c770ced0.png)


看做分类问题，难点是？



 	
  * 你需要找“很多位置”，给“很多不同大小的框”

 	
  * 你还需要对框内的图像分类(累计很多次)

 	
  * 框的大小不一定对

 	
  * …

 	
  * 当然，如果你的GPU很强大，恩，那加油做吧…


看做分类问题，有没有办法优化下？

 	
  * 为什么要先给定“框”，能不能找到“候选框”？

 	
  * 想办法先找到“可能包含内容的图框”


关于“候选图框”识别，有什么办法？

 	
  * 自下而上融合成“区域”

 	
  * 将“区域”扩充为“图框”




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ce787f99.png)


“图框”候选：其他方式？


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36d003fbda.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36d0bf2d29.png)





# R-CNN




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36d18456ae.png)


Girschick et al, “Rich feature hierarchies for accurate object detection and semantic segmentation”, CVPR 2014


## 步骤1：找一个预训练好的模型(Alexnet,VGG)针对你的场景做fine-tune




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36d347e4c7.png)





## 步骤2：fine-tuning模型


比如20个物体类别+1个背景


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36d546c9d6.png)





## 步骤3：抽取图片特征





 	
  * 用“图框候选算法”抠出图窗

 	
  * Resize后用CNN做前向运算，取第5个池化层做特征

 	
  * 存储抽取的特征到硬盘/数据库上




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36db753c68.png)





## 步骤4：训练SVM识别是某个物体或者不是(2分类)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ddc11372.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e09d37b9.png)





## 步骤5：bbox regression





 	
  * 微调图窗区域




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e2758943.png)





# R-CNN => Fast-rcnn




## 针对R-CNN的改进1





 	
  * 共享图窗计算，从而加速




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e4609969.png)





## 针对R-CNN的改进2





 	
  * 直接做成端到端的系统




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e58b622c.png)


关于RIP：Region of Interest Pooling


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e8266c61.png)


维度不匹配怎么办：划分格子grid => 下采样


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36e9abbc03.png)


RIP：Region of Interest Pooling   映射关系显然是可以还原回去的


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36eb7ebeba.png)





## 速度对比




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ec2ac3ce.png)





# Fast => Faster-rcnn


Region Proposal(候选图窗)一定要另外独立做吗？

一起用RPN做完得了！


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36ed9baf11.png)


Ren et al, “Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks”, NIPS 2015

关于RPN：Region Proposal Network


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36eedcf47b.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36efbaf4bc.png)


关于Faster R-CNN的整个训练过程


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f09a1530.png)





## 速度/准度对比




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f11e922f.png)





# 新方法：R-FCN


Jifeng Dai,etc “R-FCN: Object Detection via Region-based Fully Convolutional Networks ”, 2016


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f2fc5ed9.png)


每个颜色代表不同的位置选择区域。
The bank of kxk score maps correspond to a kxk spatial grid describing relative positions.

训练损失:


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f5d63325.png)


分类损失:


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f647fc69.png)


Region-sensitive score maps and ROI pooling


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f7a475e0.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac36f81b4516.png)







# COMMENT：


**与之前的http://106.15.37.116/2018/04/02/cv-object-detection/ 有部分是重复的，因此要自己过滤之后进行拆分和梳理。**





R-CNN
(Cafffe + MATLAB): https://github.com/rbgirshick/rcnn (非常慢，看看就好)
Fast R-CNN
(Caffe + MATLAB): https://github.com/rbgirshick/fast-rcnn (非端到端)
Faster R-CNN
(Caffe + MATLAB): https://github.com/ShaoqingRen/faster_rcnn
(Caffe + Python): https://github.com/rbgirshick/py-faster-rcnn
SSD
(Caffe + Python)https://github.com/weiliu89/caffe/tree/ssd
R-FCN
(Caffe + Matlab) https://github.com/daijifeng001/R-FCN
(Caffe + Python) https://github.com/Orpine/py-R-FCN


# REF：





 	
  1. 七月在线 深度学习



