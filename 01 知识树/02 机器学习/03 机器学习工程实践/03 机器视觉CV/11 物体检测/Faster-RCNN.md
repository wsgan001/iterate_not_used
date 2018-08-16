


看到微信群里有人讨论 RPN，有人问大的图像能输入到 RPN 里面吗，然后有人说 YOLO 和 R-CNN 系列方法不一样，只要求图像的一个边是 32 倍，对图像的大小要求没有那么高。<span style="color:red;">对，我也想问，大的图像到底要怎么处理？直接缩到 227*227 不好吧？如果用大尺寸来输入，那么又没有足够的训练资源，别的网络拿过来又不能用了，怎么办？</span>


对于这块我理解的也不够，因此还是要好好总结下的。

需要消化的：

- [faster-rcnn 之 RPN网络的结构解析](https://blog.csdn.net/sloanqin/article/details/51545125)
- [faster-rcnn中，对RPN的理解](https://blog.csdn.net/ying86615791/article/details/72788414)
