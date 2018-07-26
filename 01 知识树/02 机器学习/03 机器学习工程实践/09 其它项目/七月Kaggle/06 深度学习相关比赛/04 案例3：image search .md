
猫狗分辨是一个经典的例子。

数据：

链接：https://pan.baidu.com/s/18mIytQ2X0dly2Lz-r_O9Dw 密码：h4hi


# 图片搜索器

在深度学习还没有流行的时候，人们是怎么做特征提取这件事情的：

我们借用猫狗的图片库来做一个图片搜索器。

什么是图片搜索器？就是用一张图片来搜索跟它相同或者类似的图片。

百度google 都有这种以图搜索的功能。

今天我们来简单实现一个图片搜索器。

分四步走：

- 生成图片特征。如果不用深度学习就要认为生成。
- Index 数据库
- 比较相似度
- 搜索 走起~

跟之前课上讲过的高维数据数据一样，图片搜索器也就是把图片变成了一个个特征向量，然后我们通过比较特征向量的相似度，来判断该返回哪一个值。

## 图片特征

了解摄影的同学，都知道，图片有个色彩直方图，可以看出 RGB 三种颜色的分布，来判断图片的好坏，因为可以判断图片的色彩是否均匀。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180726/hHIaKfL72l.jpg?imageslim)

同时这个也可以作为我们图片的特征，因为对于一个指定的物体来说，他的色彩分布应该是固定的。

比如一个猫，他不管怎么拍，颜色的分布应该是差不多的。当然，如果一只猫前面是白的，后面是黑的，那么就不准了。


OpenCV 也可以把图片读入，并得到这样的色彩直方图。我们可以用它作为我们的特征数据。

我们来定义一个类，就叫 HistogramGenerator（色彩直方图数据生成器）


```python
import numpy as np
import cv2

class HistogramGenerator:
    def __init__(self, bins):
        # bin指的是RGB中每个颜色有多少色域（有点像0-255的分块）
        self.bins = bins

    def generate(self, image):
        # calcHist就是calculate Histogram的意思
        # 签名如下：cv2.calcHist(images, channels, mask, histSize, ranges[, hist[, accumulate]])
        hist = cv2.calcHist([image], [0, 1, 2],
            None, self.bins, [0, 256, 0, 256, 0, 256])
        # 平滑一下曲线
        hist = cv2.normalize(hist, hist)
        # 因为我们有三个颜色，上面一步搞完变成一个3D的数组了。
        # 3D搞起运算来太麻烦，我们直接flatten成1D数组
        return hist.flatten()
```

opencv 在图像处理中的地位就相当于 sklearn 在机器学习中的地位一样。大家都会用他来处理我们读入的图片。




## 读入图片

我们还用刚刚的猫狗图片。你们自己的话，可以随便拉一把图片出来玩玩。


```python
import os
import random
# 文件夹
DIR = '../input/train/'
# 猫狗图片
image_dogs =   [DIR+i for i in os.listdir(DIR) if 'dog' in i]
image_cats =   [DIR+i for i in os.listdir(DIR) if 'cat' in i]
# 各取前20个
images = image_dogs[:20] + image_cats[:20]
# 洗洗牌
random.shuffle(images)
```

洗牌是为了使我们的图片混合的好一点，不至于训练的时候一直取到猫的图片。

## Index图片特征

索引我们的特征。

简单说就是把所有的图片库都跑一遍，generate出他们的色彩直方图


```python
# 把那个生成器先init了
# 我选个小一点的色域。你们可以弄到足够大 --> [255,255,255]
hist_generator = HistogramGenerator([8,8,8])

index = {}

for image in images:
    # 读入图片
    image_data = cv2.imread(image)
    # 特征值
    feature = hist_generator.generate(image_data)
    # 存进我们的index dict中
    index[image] = feature
```

这样，我们就把图片的地址和特征都放到 index 这个字典里面了。后面用的时候就可以索引了。

## 相似度

接下来，我们把我们的搜索器写好：

我们可以用各种方法来算两个向量的相似度（cos，squared distance 等等）

这次我们用个卡方距离：

$$\frac12\sum_{i=1}^{n}\frac{(x_i-y_i)^2}{(x_i+y_i)}$$


```python
def similarity(x, y, eps = 1e-10):
    sim = 0.5 * np.sum([((a - b) ** 2) / (a + b + eps)
        for (a, b) in zip(x, y)])
    return sim
```

## 搜索器

接下来，我们用个方法来做我们的搜索器

输入是我们的图片路径，输出是排好的相似图片


```python
import numpy as np

def search(search_dir):
    # 把我们要输出的结果放起来
    results = {}

    # 读入图片
    image_data = cv2.imread(search_dir)
    # 特征值
    search_feature = hist_generator.generate(image_data)

    # 搜索嘛，就得全部数据库都跑一遍
    for (k, features) in index.items():
        sim = similarity(features, search_feature)
        # 记录结果
        results[k] = sim
    # 按照相似度高低排个序
    results = sorted([(v, k) for (k, v) in results.items()])

    return results
```

## 跑一个

我们选一个图片来试试

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180726/adI3fbG3EH.jpg?imageslim)

```python
results = search('../input/train/dog.10011.jpg')
print(results)
```

搜索结果如下：

```
[(0.0, '../input/train/dog.10011.jpg'), (1.1575965819194203, '../input/train/dog.10013.jpg'), (1.1686852434862907, '../input/train/dog.10003.jpg'), (1.2922303100778598, '../input/train/dog.100.jpg'), (1.5248667191454135, '../input/train/cat.1.jpg'), (1.5680995885748055, '../input/train/dog.1000.jpg'), (1.5807108650464656, '../input/train/cat.10.jpg'), (1.6168986050745535, '../input/train/cat.10002.jpg'), (1.6222009504832313, '../input/train/cat.1000.jpg'), (1.7455928589497471, '../input/train/cat.10006.jpg'), (1.7709849807618534, '../input/train/cat.10008.jpg'), (1.7713906467314766, '../input/train/dog.10001.jpg'), (1.7880980871856056, '../input/train/cat.10012.jpg'), (1.8532573914033232, '../input/train/dog.10005.jpg'), (1.9396707268642424, '../input/train/cat.10013.jpg'), (1.9519813483802371, '../input/train/dog.10.jpg'), (2.0306899689654117, '../input/train/dog.10010.jpg'), (2.0448013268158691, '../input/train/dog.10012.jpg'), (2.0549485887051744, '../input/train/cat.10001.jpg'), (2.0861727523170015, '../input/train/dog.10000.jpg'), (2.1372516107377688, '../input/train/cat.10000.jpg'), (2.1819938136684405, '../input/train/cat.10004.jpg'), (2.1866095573250894, '../input/train/dog.10004.jpg'), (2.1993509673002372, '../input/train/cat.10011.jpg'), (2.2208851092698199, '../input/train/dog.1.jpg'), (2.2332924717371023, '../input/train/cat.10005.jpg'), (2.2408593399533823, '../input/train/cat.10003.jpg'), (2.2658609099545193, '../input/train/dog.1001.jpg'), (2.3044345879891668, '../input/train/dog.10009.jpg'), (2.3214207286782624, '../input/train/dog.10007.jpg'), (2.4116737997241118, '../input/train/dog.10002.jpg'), (2.4744486546723383, '../input/train/cat.100.jpg'), (2.5259872935768843, '../input/train/cat.10009.jpg'), (2.5726709805088195, '../input/train/dog.0.jpg'), (2.614676200647887, '../input/train/dog.10006.jpg'), (2.816816468457199, '../input/train/cat.1001.jpg'), (3.0930462394321956, '../input/train/cat.10010.jpg'), (3.2991360721952296, '../input/train/dog.10008.jpg'), (4.0753444584356382, '../input/train/cat.0.jpg'), (4.1189897751549154, '../input/train/cat.10007.jpg')]
```

我们来看看结果：

我们看到，最相似的就是他自己：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180726/adI3fbG3EH.jpg?imageslim)

这是最不相似的图片，是一张完全不同风格的猫：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180726/j6aFDLlA0G.jpg?imageslim)

## 总结：

当然，这里的 feature 只看了色彩的运用，

我们还是有更多的方法来表示出更多的 features，

比如变成灰度图，再计算 histogram，

比如加上各种不同的滤镜（类似CNN的第一层），再做计算

要让结果更加酷炫，我们的色域也要稍微大点，比如 256 个bin。
