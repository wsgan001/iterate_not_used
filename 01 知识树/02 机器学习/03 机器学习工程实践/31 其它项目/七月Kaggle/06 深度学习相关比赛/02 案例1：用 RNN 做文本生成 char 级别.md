# 用 RNN 做文本生成

我们会直接抽取出这一类型的案例的最本质的玩法，给大家讲一下基本是怎么玩的。

举个小小的例子，来看看 LSTM 是怎么玩的

我们这里用温斯顿丘吉尔的人物传记作为我们的学习语料。语料要很重要，中文英文都可以，因为中文的话可以用 jieba 分词，分完词之后大家都一样，后续的处理与英语是相同的。

(各种中文语料可以自行网上查找，英文的小说语料可以从古登堡计划网站下载txt平文本：<https://www.gutenberg.org/wiki/Category:Bookshelf>)

第一步，一样，先导入各种库

我们这里使用 keras ：

```python
import numpy
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import Dropout
from keras.layers import LSTM
from keras.callbacks import ModelCheckpoint
from keras.utils import np_utils
```

Dense 就是指普通的神经网络。

接下来，我们把文本读入


```python
raw_text = open('../input/Winston_Churchil.txt').read()
raw_text = raw_text.lower()
```

既然我们是以每个字母为层级，字母总共才26个，所以我们可以很方便的用One-Hot来编码出所有的字母（当然，可能还有些标点符号和其他noise）

由于我们要使用 深度学习，因此，我们想做成端到端的，那么我们就不做 tf-idf 等一些特征了。

```python
chars = sorted(list(set(raw_text)))
char_to_int = dict((c, i) for i, c in enumerate(chars))
int_to_char = dict((i, c) for i, c in enumerate(chars))
```

总共而言，我们并不一定只有 26 个字母，因为还有一些标点符号。

我们看到，全部的chars：


```python
chars
```


```
['\n',
 ' ',
 '!',
 '#',
 '$',
 '%',
 '(',
 ')',
 '*',
 ',',
 '-',
 '.',
 '/',
 '0',
 '1',
 '2',
 '3',
 '4',
 '5',
 '6',
 '7',
 '8',
 '9',
 ':',
 ';',
 '?',
 '@',
 '[',
 ']',
 '_',
 'a',
 'b',
 'c',
 'd',
 'e',
 'f',
 'g',
 'h',
 'i',
 'j',
 'k',
 'l',
 'm',
 'n',
 'o',
 'p',
 'q',
 'r',
 's',
 't',
 'u',
 'v',
 'w',
 'x',
 'y',
 'z',
 '‘',
 '’',
 '“',
 '”',
 '\ufeff']
```

一共有：


```python
len(chars)
```


```
61
```

可见，一共有 61 个不同的字符。

同时，我们的原文本一共有:


```python
len(raw_text)
```

```
276830
```

我们这里简单的文本预测就是，给了前置的字母以后，下一个字母是谁？

比如，Winsto, 给出 n， Britai 给出 n。


## 构造训练测试集

我们需要把我们的 raw text 变成可以用来训练的 x,y :

这个案例中，x 是一些前置字母们 ，y 是后一个字母。因此，我们直接便利整篇文章，来生成 x,y 。<span style="color:red;">为什么这里用的 x 是100个前置字母？y 是紧跟着这100个字母的后置字母？为什么 x 不用一个字母？而且，如果 x 是100 个字母的话，测试的时候，不是也需要用 100个字母作为x 输入？</span>


```python
seq_length = 100
x = []
y = []
for i in range(0, len(raw_text) - seq_length):
    given = raw_text[i:i + seq_length]
    predict = raw_text[i + seq_length]
    x.append([char_to_int[char] for char in given])
    y.append(char_to_int[predict])
```

我们可以看看我们做好的数据集的长相：


```python
print(x[:3])
print(y[:3])
```

```
[[60, 45, 47, 44, 39, 34, 32, 49, 1, 36, 50, 49, 34, 43, 31, 34, 47, 36, 57, 48, 1, 47, 34, 30, 41, 1, 48, 44, 41, 33, 38, 34, 47, 48, 1, 44, 35, 1, 35, 44, 47, 49, 50, 43, 34, 9, 1, 31, 54, 1, 47, 38, 32, 37, 30, 47, 33, 1, 37, 30, 47, 33, 38, 43, 36, 1, 33, 30, 51, 38, 48, 0, 0, 49, 37, 38, 48, 1, 34, 31, 44, 44, 40, 1, 38, 48, 1, 35, 44, 47, 1, 49, 37, 34, 1, 50, 48, 34, 1, 44], [45, 47, 44, 39, 34, 32, 49, 1, 36, 50, 49, 34, 43, 31, 34, 47, 36, 57, 48, 1, 47, 34, 30, 41, 1, 48, 44, 41, 33, 38, 34, 47, 48, 1, 44, 35, 1, 35, 44, 47, 49, 50, 43, 34, 9, 1, 31, 54, 1, 47, 38, 32, 37, 30, 47, 33, 1, 37, 30, 47, 33, 38, 43, 36, 1, 33, 30, 51, 38, 48, 0, 0, 49, 37, 38, 48, 1, 34, 31, 44, 44, 40, 1, 38, 48, 1, 35, 44, 47, 1, 49, 37, 34, 1, 50, 48, 34, 1, 44, 35], [47, 44, 39, 34, 32, 49, 1, 36, 50, 49, 34, 43, 31, 34, 47, 36, 57, 48, 1, 47, 34, 30, 41, 1, 48, 44, 41, 33, 38, 34, 47, 48, 1, 44, 35, 1, 35, 44, 47, 49, 50, 43, 34, 9, 1, 31, 54, 1, 47, 38, 32, 37, 30, 47, 33, 1, 37, 30, 47, 33, 38, 43, 36, 1, 33, 30, 51, 38, 48, 0, 0, 49, 37, 38, 48, 1, 34, 31, 44, 44, 40, 1, 38, 48, 1, 35, 44, 47, 1, 49, 37, 34, 1, 50, 48, 34, 1, 44, 35, 1]]
[35, 1, 30]
```

此刻，楼上这些表达方式，类似就是一个词袋，或者说 index。

接下来我们做两件事：

1. 我们已经有了一个input的数字表达（index），我们要把它变成LSTM需要的数组格式： [样本数，时间步伐，特征]。<span style="color:red;">这个地方老师解释了一下，但是没明白。想要知道更多，老实说：样本数就是总共有多少个，实践步伐就是从前往后一共看多少步，特征就是每个字符只用一维来表达就是1~61 这些数字。还是没有很明白，尤其是时间步伐，嗯，特征的确是可以是很多维的。</span>
2. 第二，对于 output ，我们在 Word2Vec 里学过，用 one-hot 做 output的预测可以给我们更好的效果，相对于直接预测一个准确的 y 数值的话。比如，现在有 61 个词，那么 1~61 可以变成 61 维 的one-hot 表达式，也就是说，我们最后预测出的是一个 61维的向量，只有一个是1，其他的都是0 ，实际上，每个向量的点表示他是这个字符的可能性。我们最后可以从这61 个树里面选择最大的作为我们的输出。<span style="color:blue;">嗯，简单的说，就是把 1~61 这61 个数看成 category 类型的，然后进行 one-hot 处理。</span>


```python
n_patterns = len(x)
n_vocab = len(chars)

# 把 x 变成 LSTM 需要的样子
x = numpy.reshape(x, (n_patterns, seq_length, 1))
# 简单 normal 到0-1之间
x = x / float(n_vocab)
# output变成 one-hot
y = np_utils.to_categorical(y)

print(x[11])
print(y[11])
```

<span style="color:red;">没明白为什么要 reshape ，之前的 x 不满足条件吗？</span>

<span style="color:red;">`np_utils.to_categorical(y)` 直接把数变成了one-hot 有些厉害，嗯，方便。</span>

```
[[ 0.80327869]
 [ 0.55737705]
 [ 0.70491803]
 [ 0.50819672]
 [ 0.55737705]
 [ 0.7704918 ]
 [ 0.59016393]
 [ 0.93442623]
 [ 0.78688525]
 [ 0.01639344]
 [ 0.7704918 ]
 [ 0.55737705]
 [ 0.49180328]
 [ 0.67213115]
 [ 0.01639344]
 [ 0.78688525]
 [ 0.72131148]
 [ 0.67213115]
 [ 0.54098361]
 [ 0.62295082]
 [ 0.55737705]
 [ 0.7704918 ]
 [ 0.78688525]
 [ 0.01639344]
 [ 0.72131148]
 [ 0.57377049]
 [ 0.01639344]
 [ 0.57377049]
 [ 0.72131148]
 [ 0.7704918 ]
 [ 0.80327869]
 [ 0.81967213]
 [ 0.70491803]
 [ 0.55737705]
 [ 0.14754098]
 [ 0.01639344]
 [ 0.50819672]
 [ 0.8852459 ]
 [ 0.01639344]
 [ 0.7704918 ]
 [ 0.62295082]
 [ 0.52459016]
 [ 0.60655738]
 [ 0.49180328]
 [ 0.7704918 ]
 [ 0.54098361]
 [ 0.01639344]
 [ 0.60655738]
 [ 0.49180328]
 [ 0.7704918 ]
 [ 0.54098361]
 [ 0.62295082]
 [ 0.70491803]
 [ 0.59016393]
 [ 0.01639344]
 [ 0.54098361]
 [ 0.49180328]
 [ 0.83606557]
 [ 0.62295082]
 [ 0.78688525]
 [ 0.        ]
 [ 0.        ]
 [ 0.80327869]
 [ 0.60655738]
 [ 0.62295082]
 [ 0.78688525]
 [ 0.01639344]
 [ 0.55737705]
 [ 0.50819672]
 [ 0.72131148]
 [ 0.72131148]
 [ 0.6557377 ]
 [ 0.01639344]
 [ 0.62295082]
 [ 0.78688525]
 [ 0.01639344]
 [ 0.57377049]
 [ 0.72131148]
 [ 0.7704918 ]
 [ 0.01639344]
 [ 0.80327869]
 [ 0.60655738]
 [ 0.55737705]
 [ 0.01639344]
 [ 0.81967213]
 [ 0.78688525]
 [ 0.55737705]
 [ 0.01639344]
 [ 0.72131148]
 [ 0.57377049]
 [ 0.01639344]
 [ 0.49180328]
 [ 0.70491803]
 [ 0.8852459 ]
 [ 0.72131148]
 [ 0.70491803]
 [ 0.55737705]
 [ 0.01639344]
 [ 0.49180328]
 [ 0.70491803]]
[ 0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.  0.
  1.  0.  0.  0.  0.  0.]
```

整个过程没有任何的人为提取，如果做人为提取，效果可能会更好，但是就不会纯粹的深度算法了。我们想让整个的过程就是端到端的过程。


## 模型建造

LSTM模型构建

```python
model = Sequential()
model.add(LSTM(128, input_shape=(x.shape[1], x.shape[2])))
model.add(Dropout(0.2))
model.add(Dense(y.shape[1], activation='softmax'))
model.compile(loss='categorical_crossentropy', optimizer='adam')
```

为什么要加 Dense 呢？加这一层的神经网络，把整个数据做一个归拢。

可见，只有两层，第一层是一个128个神经元的 LSTM，第二层是一个 62维的普通神经网络。<span style="color:red;">为什么这个地方是128个神经元？不是应该是  seq_length*1=100  吗？不相等的话怎么输入？</span>

## 跑模型


```python
model.fit(x, y, nb_epoch=10, batch_size=32)
```


```
Epoch 1/10
 21120/276730 [=>............................] - ETA: 1078s - loss: 3.0638
```

从上面的输出可以看出，这个简单的两层的神经网络，单单跑一遍是 1078 s。还是比较长时间的。

我们来写个程序，看看我们训练出来的LSTM的效果：

下面是三个方法：

```python
def predict_next(input_array):
    x = numpy.reshape(input_array, (1, seq_length, 1))
    x = x / float(n_vocab)
    y = model.predict(x)
    return y

def string_to_index(raw_input):
    res = []
    for c in raw_input[(len(raw_input)-seq_length):]:
        res.append(char_to_int[c])
    return res

def y_to_char(y):
    largest_index = y.argmax()
    c = int_to_char[largest_index]
    return c
```

第一个是想要预测的时候，要注意输入的x 也要满足lstm 的格式，第二个方法是string 转化成 index 列表，第三个方法是 y 转化成 char 。

好，写成一个大程序：给定一个 init ，然后我们就可以输出 500 个单词。

```python
def generate_article(init, rounds=500):
    in_string = init.lower()
    for i in range(rounds):
        n = y_to_char(predict_next(string_to_index(in_string)))
        in_string += n
    return in_string
```


```python
init = 'Professor Michael S. Hart is the originator of the Project'
article = generate_article(init)
print(article)
```


结果没有输出来，自己跑一下试试。
