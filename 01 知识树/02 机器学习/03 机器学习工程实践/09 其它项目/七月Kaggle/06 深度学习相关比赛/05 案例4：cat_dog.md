实际上，不管你使用什么特征来处理这种图片，都已经 out 了。现在基本大家都使用 DL 来处理图片了。


gan 深度对抗网络，后面的课会讲。


# 猫狗辨别

Kaggle竞赛：<https://www.kaggle.com/c/dogs-vs-cats-redux-kernels-edition>

标准的 ConvNet 做图片分类。

先导入要用的库


```python
import os, cv2, random
import numpy as np
import pandas as pd

import matplotlib.pyplot as plt
from matplotlib import ticker
import seaborn as sns
%matplotlib inline

from keras.models import Sequential
from keras.layers import Input, Dropout, Flatten, Convolution2D, MaxPooling2D, Dense, Activation
from keras.optimizers import RMSprop
from keras.callbacks import ModelCheckpoint, Callback, EarlyStopping
from keras.utils import np_utils
```


```
Using Theano backend.
```

<span style="color:red;">使用 Theano 和 Tensorflow 的 backend 有什么区别？现在是不是没什么人使用 Theano 了？</span>


## 准备数据

我们先把所有的数据 load 进来。

因为时间关系，就不 train 所有的数据集了。。太慢。。

我把猫和狗里面各拿出100个来跑一个：


```python
TRAIN_DIR = '../input/train/'
TEST_DIR = '../input/test/'

# 把猫和狗分开读入
# 想全部读入的话，就把那个 if 去掉。
# 记得把 label 也存好（0/1）
train_dogs =   [(TRAIN_DIR+i, 1) for i in os.listdir(TRAIN_DIR) if 'dog' in i]
train_cats =   [(TRAIN_DIR+i, 0) for i in os.listdir(TRAIN_DIR) if 'cat' in i]

# 训练集还是得全盘放进来的
# 这个 testset 就随便放个 label
test_images =  [(TEST_DIR+i, -1) for i in os.listdir(TEST_DIR)]

# 合成一个数据集（取个 100 个玩玩）
train_images = train_dogs[:100] + train_cats[:100]
# 把数据散好
random.shuffle(train_images)
# 取个10个玩玩
test_images =  test_images[:10]
```



我们需要用到 OpenCV 去读入图片（ IMREAD() ）

为了图片标准统一，我们把所有的图片 resize 进 `64*64` 的方格。<span style="color:red;">这个是图片预处理中比较重要的，这样才能接上卷积神经网络。</span>


```python
ROWS = 64
COLS = 64

def read_image(tuple_set):
    file_path = tuple_set[0]
    label = tuple_set[1]
    img = cv2.imread(file_path, cv2.IMREAD_COLOR)
    # 你这里的参数，可以是彩色或者灰度(GRAYSCALE)
    return cv2.resize(img, (ROWS, COLS), interpolation=cv2.INTER_CUBIC), label
    # 这里，可以选择压缩图片的方式，zoom（cv2.INTER_CUBIC & cv2.INTER_LINEAR）还是shrink（cv2.INTER_AREA）
```

一般来说，为了保持图片的信息特征不被扭曲，会使用 INTER_CUBIC 这个形式，他会成比例缩放，空的地方会补全。

预处理图片：

把图片数据变成我们便于用的numpy数组


```python
CHANNELS = 3
# 代表RGB三个颜色频道

def prep_data(images):
    no_images = len(images)
    data = np.ndarray((no_images, CHANNELS, ROWS, COLS), dtype=np.uint8)
    labels = []

    for i, image_file in enumerate(images):
        image, label = read_image(image_file)
        data[i] = image.T
        labels.append(label)
    return data, labels
```

好的，这下我们可以一步刷完所有的 train 和 test 集了

这个我们有个木有用的 y_shit，记得扔一边儿去


```python
x_train, y_train = prep_data(train_images)
x_test, y_shit = prep_data(test_images)
```

看看shape：


```python
print(x_train.shape)
print(x_test.shape)
```

```
(200, 3, 64, 64)
(10, 3, 64, 64)
```

## CNN模型构造

这个是一个标准的 VGG CNN 构造形式。VGG 这样的构造形式在很多图片数据集上都跑过，的确是比很多的别的构造要好一些。这个VGG 他们也不是有理论来说就这么构建就最好，他们实际上也是无数次试验之后，发现这个结构是最好的。

论文里有讲为什么选32、64、128、256 这些逐级放大的意义。


```python
optimizer = RMSprop(lr=1e-4)
objective = 'binary_crossentropy'

# 建造模型
model = Sequential()

model.add(Convolution2D(32, 3, 3, border_mode='same', input_shape=(3, ROWS, COLS), activation='relu'))
model.add(Convolution2D(32, 3, 3, border_mode='same', activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))

model.add(Convolution2D(64, 3, 3, border_mode='same', activation='relu'))
model.add(Convolution2D(64, 3, 3, border_mode='same', activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))

model.add(Convolution2D(128, 3, 3, border_mode='same', activation='relu'))
model.add(Convolution2D(128, 3, 3, border_mode='same', activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))

model.add(Convolution2D(256, 3, 3, border_mode='same', activation='relu'))
model.add(Convolution2D(256, 3, 3, border_mode='same', activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))

model.add(Flatten())
model.add(Dense(256, activation='relu'))
model.add(Dropout(0.5))

model.add(Dense(256, activation='relu'))
model.add(Dropout(0.5))

model.add(Dense(1))
model.add(Activation('sigmoid'))

model.compile(loss=objective, optimizer=optimizer, metrics=['accuracy'])
```


## 训练与预测

这里，**做图片处理，很容易 overfitting，** 我们可以用 keras 自带的 earlyStopping 来检测 validation data (取20%数据)。

什么是提前结束呢？你在训练的时候，从你的train 里面拿出了一小部分作为 validation data，用来判断每次训练之后的 model 是变好了还是变坏了。用它我们可以设定一些政策，如果说我们走了10步，准确度一直没有在降，就可以说，我们已经 overfitting 了，我们就可以让他停下来，然后输出10步之前没有 overfitting.

earlystopping 在图片里面是非常重要的。

```python
nb_epoch = 10
batch_size = 10

## 每个 epoch 之后，存下 loss，便于画出图
class LossHistory(Callback):
    def on_train_begin(self, logs={}):
        self.losses = []
        self.val_losses = []

    def on_epoch_end(self, batch, logs={}):
        self.losses.append(logs.get('loss'))
        self.val_losses.append(logs.get('val_loss'))

early_stopping = EarlyStopping(monitor='val_loss', patience=3, verbose=1, mode='auto')

# 跑模型
history = LossHistory()

model.fit(x_train, y_train, batch_size=batch_size, nb_epoch=nb_epoch,
          validation_split=0.2, verbose=0, shuffle=True, callbacks=[history, early_stopping])

predictions = model.predict(x_test, verbose=0)
```

上面的LossHistory 从 Callback 继承，是一个标准的写法，

EarlyStopping 是一个 keras 自带的一个类。patience 就是容忍我的程序一直不提升几步，这里是三步。verbose 是是否输出中间计算的一些记录。

batch_size 就是把多少张图片放在一起放进去做计算，nb_epoch 就是要跑多少个 epoch。validation_split 是在我们的训练集中分出多少作为我们的验证集，shuffle 是是否要把原数据进行洗牌。callbacks 放入我们的history 和early_stopping 。

epoch 就是一轮的意思。<span style="color:red;">什么叫一轮呢？好像 w 的调整就是根据连续的几张图片计算出的平均的误差进行调整，而调整一次叫做一轮。确认下。</span>

```
Epoch 00005: early stopping
```

在第 5 次跑完所有样本的时候就已经提前结束了。

```python
loss = history.losses
val_loss = history.val_losses

plt.xlabel('Epochs')
plt.ylabel('Loss')
plt.title('VGG-16 Loss Trend')
plt.plot(loss, 'blue', label='Training Loss')
plt.plot(val_loss, 'green', label='Validation Loss')
plt.xticks(range(0,nb_epoch)[0::2])
plt.legend()
plt.show()
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180726/da5mll8c7d.png?imageslim)


由于是第5次的时候停止的，而patience =3，所以是在第2次的时候停下来最好。<span style="color:red;">这个地方是5-3=2 还是 5-3+1=3？ 确认下。而且，我最后生成的模型在哪里？我怎么生成第二个 epoch 的时候的模型？而且模型要怎么加载？这些这里没有讲。</span>


## 总结

跟之前的分类问题一样。

这是一套整个图片分类的流程。

真正实战的情况下，你需要一台够好的电脑，最好带显卡。


这个猫狗分类与其他的分类其实也差不多，不过少了一堆特征处理的过程。由于我们使用深度学习的时候，更加希望这个是一个端对端的过程，就是图片进，label 出，中间不做任何的认为处理，全部交给算法本身来做。

<span style="color:red;">没明白，为什么深度学习一定要做成端到端的学习？有这个硬性要求吗？还是说这样做有些好处？</span>

OK，上面这些就是一个标准的，也是经常会给出最好结果的，CNN 用来处理图片的程序的模板，。

上面这个猫狗分类的最后一层是 `model.add(Dense(1))` ，如果是多分类问题，可以构建成 one-hot 。
