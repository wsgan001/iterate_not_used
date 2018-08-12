### TensorFlow实现循环神 经网络及Word2Vec

本章我们将探索循环神经网络(RNN )和Word2Vec55,并在TensorFlow上实现它们。 循环神经网络是在NLP (Nature Language Processing,自然语言处理)领域最常使用的 神经网络结构，和卷积神经网络在图像识别领域的地位类似。而Word2VeC则是将语言中 的字词转化为计算机可以理解的稠密向量(Dense Vector),进而可以做其他自然语言处 理任务，比如文本分类、词性标注、机器翻译等。

##### 7.1 TensorFlow 实现 Word2Vec

Word2Vec也称Word Embeddings,中文有很多叫法，比较普便的是“词向量”或“词 嵌入”。Word2Vec是一个可以将语言中字词转为向量形式表达(Vector Representations ) 的模型，我们先来看看为什么要把字词转为向量。图像、音频等数据天然可以编码并存储 为稠密向量的形式，比如图片是像素点的稠密矩阵，音频可以转为声音信号的频谱数据。 自然语言处理在Word2Vec出现之前，通常将字词转成离散的单独的符号，比如将“中国” 转为编号为5178的特征，将“北京”转为编号为3987的特征。这即是One-Hot Encoder,

一个饲对应一个向量（向量中只有一个值为1，其余为0 ），通常需要将一篇文章中每一个 词都转成一个向量，而整篇文章则变为一个稀疏矩阵。对文本分类模型，我们使用Bag of Words模型,将文章对应的稀疏矩阵合并为一个向量，即把每一个词对应的向量加到一起， 这样只统计每个词出现的次数，比如“中国”出现23次，那么第5178个特征为23, “北 京”出现2次，那么第3987个特征为2。

使用One-Hot Encoder有一个问题，即我们对特征的编码往往是随机的，没有提供任 何关联信息，没有考虑到字词间可能存在的关系。例如，我们对“中国”和“北京”的从 属关系、地理位置关系等一无所知，我们从5178和3987这两个值看不出任何信息。同时， 将字词存储为稀疏向量的话，我们通常需要更多的数据来训练，因为稀疏数据训练的效率 比较低，计算也非常麻烦。使用向量表达（Vector Representations ）则可以有效地解决这 个问题。向量空间模型（Vector Space Models ）可以将字词转为连续值（相对于One-Hot 编码的离散值）的向量表达，并且其中意思相近的词将被映射到向量空间中相近的位置。 向量空间模型在NLP中主要依赖的假设是Distributional Hypothesis,即在相同语境中出 现的词其语义也相近。向量空间模型可以大致分为两类，一类是计数模型，比如Latent Semantic Analysis;另一类是予页测模型（比如 Neural Probabilistic Language Models ）。计 数模型统计在语料库中，相邻出现的词的频率，再把这些计数统计结果转为小而稠密的矩 阵；而预测模型则根据一个词周围相邻的词推测出这个词，以及它的空间向量。

Word2Vec即是一种计算非常高效的，可以从原始语料中学习字词空间向量的预测模 型。它主要分为 CBOW（ Continuous Bag of Words ）和 Skip-Gram 两种模式，其中 CBOW 是从原始语句（比如：中国的首都是_）推测目标字词（比如：北京）；而Skip-Gram 则正好相反，它是从目标字词推测出原始语句，其中CBOW对小型数据比较合适，而 Skip-Gram在大型语料中表现得更好。

使用Word2VeC训练语料能得到一些非常有趣的结果，比如意思相近的词在向量空间 中的位置会接近。从一份Google训练超大语料得到的结果中看，诸如Beijing、London、 New York等城市的名字会在向量空间中聚集在一起，而Cat、Dog、Fish等动物调汇也会 聚集在一起。同时，如图7-1所示，Word2VeC还能学会一些高阶的语言概念，比如我们 计算“man”到“woman"的向量（词汇都是向量空间中的点，可计算两点间的向量）， 会发现它和“king”到“queen”的向量非常相似，即模型学到了男人与女人的关系；同 时，“walking”到“walked”的向量和“swimming”到“swam”的向量非常相似，模型 学到了进行时与过去时的关系。

Male-Female



walked

Verb tense



Moscow



Canada 一~



JdpAXS    ■■

Vietnaa China —



Tokyo -Hanoi Beijing



Country-Capital



图7-1 Word2Vec模型可学习到的抽象概念

预测模型Neural Probabilistic Language Models通常使用最大似然的方法，在给定前 面的语句A的情况下，最大化目标词汇w,的概率。但它存在的一个比较严重的问题是计 算量非常大,需要计算词汇表中所有单词出现的可能性。在Word2VeC的CBOW模型中， 不需要计算完整的概率模型，只需要训练一个二元的分类模型，用来区分真实的目标词汇 和编造的词汇(噪声)这两类，如图7-2所示。这种用少量噪声词汇来估计的方法，类似 于蒙特卡洛模拟。

Noise classifier



Hidden layer



Projection layer



图7-2 CBOW模型结构示意图



当模型预测真实的目标词汇为高概率，同时预测其他噪声词汇为低概率时，我们训练 的学习目标就被最优化了。用编造的噪声词汇训练的方法被称为Negative Sampling。用 这种方法计算loss function的效率非常高，我们只需要计算随机选择的介个词汇而非词汇 表中的全部词汇，因此训练速度非常快。在实际中，我们使用Noise-Contrastive Estimation (NCE ) Loss,同时在 TensorFlow 中也有 tf.nn.nce_loss()直接实现了这个 loss。

在本节中我们将主要使用Skip-Gram模式的Word2Vec，先来看一下它训练样本的构

造，以 “the quick brown fox jumped over the lazy dog” 这句话为例。我们要构造一个 语境与目标词汇的映射关系，其中语境包括一个单词左边和右边的词汇，假设我们的滑窗 尺寸为 1，可以制造的映射关系包括[the, brown] quick、[quick, fox]    brown、[brown,

jumped] fox等。因为Skip-Gram模型是从目标词汇预测语境，所以训练样本不再是 [the, brown] -> quick,而是 quick the 和 quick brown。我们的数据集就变为了 (quick, the)、(quick, brown)、(brown, quick)、(brown, fox)等。我们训练时，希望模型能 从目标词汇quick预测出语境the，同时也需要制造随机的词汇作为负样本(噪声)，我们 希望预测的概率分布在正样本the上尽可能大，而在随机产生的负样本上尽可能小。这里 的做法就是通过优化算法比如SGD来更新模型中Word Embedding的参数，让概率分布 的损失函数(NCE Loss )尽可能小。这样每个单词的Embedded Vector就会随着训练过 程不断调整，直到处于一个最适合语料的空间位置。这样我们的损失函数最小，最符合语 料，同时预测出正确单词的概率也最高。

下面开始用TensorFlow实现Word2Vec的训练。首先依然是载入各种依赖库，这里因 为要从网络下载数据，因此需要的依赖库比较多。本节代码主要来自TensorFlow的开源 实现'

import collections

import math

import os

import random

import zipfile

import numpy as np

import urllib

import tensorflow as tf

我们先定义下载文本数据的函数。这里使用urllib.request.urlretrieve下载数据的压缩 文件并核对文件尺寸，如果已经下载了文件则跳过。

url = 1http://mattmahoney.net/dc/'

def maybe_download(filenameexpected_bytes): if not os.path.exists(filename):

filenamej _ = urllib.request,urlretrieve(url + filename, filename) statinfo = os.stat(filename)

if statinfo.st_size == expected_bytes: print('Found and verified、filename)

else:

print(statinfo.st_size) raise Exception(

'Failed to verify 1 + filename + '. Can you get to it with a browser?') return filename

filename = maybe_download('text8.zip., 31344016)

接下来解压下载的压缩文件，并使用tf.compat.as_str将数据转成单词的列表。通过程 序输出，可以知道数据最后被转为了一个包含17005207个单词的列表。

def read_data(filename):

with zipfile.ZipFile(filename) as f:

data = tf.compat.as_str(f.read(f.namelist()[0])).split() return data

words = read_data(filename)

print('Data size'j len(words))

接下来创建vocabulary词汇表，我们使用collections.Counter统计单词列表中单词的 频数，然后使用most_common方法取top 50000频数的单词作为vocabulary。再创建一个 diet,将 top 50000 词汇的 vocabulary 放入 dictionary 中，以便快速查询，Python 中 diet 查询复杂度为0(1)，性能非常好。接下来将全部单词转为编号(以频数排序的编号)，top 50000词汇之外的单词，我们认定其为Unkown (未知)，将其编号为0,并统计这类词汇 的数量。下面遍历单词列表，对其中每一个单词，先判断是否出现在dictionary中‘，如果 是则转为其编号，如果不是则转为编号0 (Unkown)。最后返回转换后的编码(data )、每 个单词的频数统计(count)、词汇表(dictionary)及其反转的形式(reverse_dictionary )。 vocabulary一size = 50000

def build_dataset(words): count = [['UNK* -1]]

count.extend(collections.Counter(words).most_common(vocabulary一size - 1))

dictionary = dict() for word_ in count:

dictionary[word] = len(dictionary) data = list() unk一count = 0 for word in words:

if word in dictionary: index = dictionary[word]

else:

index = 0 unk_count += 1

data.append(index) count[0][l] = unk_count

reverse_dictionary = dict(zip(dictionary.values()j dictionary.keys())) return data_, count, dictionaryreverse_dictionary

data, countj dictionary^ reverse_dictionary = build一dataset(words)

然后我们删除原始单词列表，可以节约内存。再打印vocabulary中最高频出现的词汇 及其数量(包括Unknown词汇)，可以看到“UNK”这类一共有418391个，最常出现的 "the”有1061396个，排名第二的"of有593677个。我们的data中前10个单词为[’anarchism1， 'originated', 'as', 'a', 'term', 'of, 'abuse', 'first', 'used', 'against'],对应的编号为[5235, 3084, 12, 6, 195, 2, 3137, 46, 59, 156]

del words

print('Most common words (+UNK)', count[:5])

print('Sample data、data[:10]> [reverse_dictionary[i] for i in data[:10]])

下面生成Word2Vec的训练样本。我们根据前面提到的Skip-Gram模式(从瞬示单词 反推语境)，将原始数据“the quick brown fox jumped over the lazy dog”转为(quick, the)、 (quick, brown)、(brown, quick)、(brown, fox)等样本。我们定义函数 generate_batch 用来 生成训练用的batch数据，参数中batch_size为batch的大小；skip_window指单词最远可 以联系的距离，设为1代表只能跟紧邻的两个单词生成样本，比如quick只能和前后的单 词生成两个样本(quick,the )和(quick，brown ); num_skips为对每个单词生成多少个样本， 它不能大于skip_window值的两倍，并且batch_size必须是它的整数倍(确保每个batch

包含了一个词汇对应的所有样本)。我们定义单词序号datajndex为global变量，因为我 们会反复调用generate_batch，所以要确保data_index可以在函数generate_batch中被修改。 我们也使用assert确保num_skips和batch_size满足前面提到的条件。然后用np.ndarray 将batch和labels初始化为数组。这里定义span为对某个单词创建相关样本时会使用到的 单词数量，包括目标单词本身和它前后的单词，因此span=2*skip_window+l o并创建一 个最大容量为span的deque,即双向队列，在对deque使用append方法添加变量时，只 会保留最后插入的span个变量。

data_index = 0

def generate_batch(batch_sizej num_skipsJ skip_window): global data_index

assert batch_size % num_skips == 0 assert num_skips <= 2 * skip_window

batch = np.ndarray(shape=(batch_size)j dtype=np.int32) labels = np.ndarray(shape=(batch_sizeJ 1), dtype=np.int32) span = 2 * skip_window + 1

buffer = collections.deque(maxlen=span)

接下来从序号data_index开始，把span个单词顺序读入buffer作为初始值。因为buffer 是容量为span的deque，所以此时buffer已填充满，后续数据将替换掉前面的数据。然后 我们进入第一层循环(次数为batch_size//num_skips ),每次循环内对一个目标单词生成样 本。现在buffer中是目标单词和所有相关单词，我们定义target=skip_window，即buffer 中第skip_window个变量为目标单词。然后定义生成样本时需要避免的单词列表 targets_to_avoid,这个列表一开始包括第skip_window个单词(即目标单词)，因为我们 要预测的是语境单词，不包括目标单词本身。接下来进入第二层循环(次数为num_SkipS )， 每次循环中对一个语境单词生成样本，先产生随机数，直到随机数不在targets_to_avoid 中，代表可以使用的语境单词，然后产生一个样本，feature即目标词汇buffer[skip_window], label则是buffer[target]。同时，因为这个语境单词被使用了，所以再把它添加到 targets_to_avoid中过滤。在对一个目标单词生成完所有样本后(num_skips个样本)，我们 再读入下一个单词(同时会抛掉buffer中第一个单词)，即把滑窗向后移动一位，这样我 们的目标单词也向后移动了一个，语境单词也整体后移了，便可以开始生成下一个目标单 词的训练样本。两层循环完成后，我们已经获得了 batch_size个训练样本，将batch和labels

作为函数结果返回。

for 一 in range(span):

buffer.append(data[data_index]) data_index = (data_index + 1) % len(data)

for i in range(batch_size // num_skips): target = skip_window targets_to_avoid = [ skip_window ] for j in range(num_skips):

while target in targets_to_avoid: target = random.randint(03 span - 1)

targets_to_avoid.append(target)

batch[i * num_skips + j] = buffer[skip_window]

labels[i * num_skips + j, 0] = buffer[target]

buffer.append(data[data_index]) data_index = (data_index + 1) % len(data)

return batchy labels

这里调用generate_batch函数简单测试一下其功能。参数中将batch_size设为8， num_skips 设为 2, skip_window 设为 1，然后执行 generate_batch 并获得 batch 和 labels o 再打印batch和labels的数据，可以看到我们生成的样本是“3084 originated -> 5235 anarchism”，“3084 originated -〉12 as”，“12 as -〉3084 originated” 等。以第一个样本 为例，3084是目标单词originated的编号，这个单词对应的语境单词是anarchism,其编 号为5235。

batchy labels = generate_batch(batch_size=8j num_skips=2, skip_window=l) for i in range(8):

print(batch[i], reverse_dictionary[batch[i]]labels[ij 0]j reverse_dictionary[labels[iJ 0]])

我们定义训练时的 batch一size 为 128； embedding_size 为 128, embedding_size 即将单 词转为稠密向量的维度，一般是50〜1000这个范围内的值，这里使用128作为词向量的 维度；Skip_wind0W即前面提到的单词间最远可以联系的距离，设为1; num_SkiPs即对每 个目标单词提取的样本数，设为2。然后我们再生成验证数据valid^xamples，这里随机 抽取一些频数最高的单词，看向量空间上跟它们最近的单词是否相关性比较高。

valid_size=16指用来抽取的验证单词数，valid_window=100是指验证单词只从频数最高的 100个单词中抽取，我们使用np.random.choice函数进行随机抽取。而num_sampled是训 练时用来做负样本的噪声单词的数量。

batch_size = 128

embedding_size = 128

skip_window = 1

num_skips = 2 valid一size = 16

valid_window = 100

valid_examples = np.random.choice(valid_window_, valid_size^ replace=False) num_sampled = 64

下面就开始定义Skip-Gram Word2Vec模型的网络结构。我们先创建一个tf. Graph并 设置为默认的grapho然后创建训练数据中inputs和labels的placeholder,同时将前面随 机产生的valid_examples转为TensorFlow中的constant。接下来，先使用with tf.device(Vcpu:O’)限定所有计算在CPU上执行，因为接下去的一些计算操作在GPU上可能 还没有实现。然后使用tf.random_uniform随机生成所有单词的词向量embeddings,单词 表大小为50000，向量维度为128,再使用tf.nn.embedding_lookup查找输入train_inputs 对应的向量embed。下面使用之前提到的NCE Loss作为训练的优化目标，我们使用 tf.truncated_normal 初始化 NCE Loss 中的权重参数 nce_weights，并将其 nce_biases 初始 化为0。最后使用tf.nn.ncejoss计算学习出的词向量embedding在训练数据上的loss,并 使用tf.reduce_mean进行汇总。

graph = tf.Graph()

with graph.as_default():

train_inputs = tf.placeholder(tf.intBZ, shape=[batch_size]) train_labels = tf.placeholder(tf.int32, shape=[batch_size^ 1]) valid_dataset = tf.constant(valid_examples, dtype=tf.int32)

with tf.device('/cpurO*): embeddings = tf.Variable(

tf.random_uniform([vocabulary_sizej embedding_size]1.0)) embed = tf.nn.embedding_lookup(embeddingstrain_inputs)

nce_weights = tf.Variable(

tf.truncated_normal([vocabulary_sizeJ embedding_size],

stddev=1.0 / math.sqrt(embedding_size)))

nce_biases = tf.Variable(tf.zeros([vocabulary_size]))

loss = tf.reduce_mean(tf.nn.nce_loss(weights=nce_weightsJ biases=nce_biases^ labels=train_labels_, inputs=embed? num_sampled=num_sampled^ num_classes=vocabulary_size))

我们定义优化器为SGD,且学习速率为1.0。然后计算嵌入向量embeddings的L2范 数norm,再将embeddings除以其L2范数得到标准化后的normalized_embeddingso再使 用tf.nn.embeddingjookup查询验证单词的嵌入向量，并计算验证单词的嵌入向量与词汇 表中所有单词的相似性。最后，我们使用tf.global_variables_initializer初始化所有模型参 数。

optimizer = tf.train.GradientDescentOptimizer(1.0).minimize(loss)

norm = tf.sqrt(tf.reduce_sum(tf.square(embeddings1, keep_dims=True)) normalized_embeddings = embeddings / norm valid_embeddings = tf.nn.embedding_lookup(

normalized一embeddingsvalid_dataset) similarity = tf.matmul(

valid_embeddingsJ nopmalized_embeddingsJ transpose_b=True)

init = tf.global_variables_initializer()

我们定义最大的迭代次数为10万次，然后创建并设置默认的session，并执行参数初 始化。在每一步训练迭代中，先使用generate_batch生成一个batch的inputs和labels数据，

并用它们创建feed_dict0然后使用SeSSiOn.nm()执行一次优化器运算(即一次参数更新) 和损失计算，并将这一步训练的loss累积到average_lossD num_steps = 100001

with tf.Session(graph=graph) as session: init.run()

print("Initialized")

average一loss =0

for step in range(num_steps):

batch_inputs? batch_labels = generate_batch( batch_size_, num_skipSj skip_window)

feed_dict = {train_inputs : batch_inputs_, train_labels : batch_labels}

loss_val = session.run([optimizer^ loss]feed_dict=feed一diet) average_loss += loss_val

之后每2000次循环，计算一下平均loss并显示出来。

if step % 2000 == 0: if step > 0:

average_loss /= 2000

print ("Average loss at step step) ":    average_loss)

average一loss = 0

每10000次循环，计算一次验证单词与全部单词的相似度，并将与每个验证单词最相 似的8个单词展示出来。

if step % 10000 == 0: sim = similarity.eval() for i in range(valid_size):

valid_word = reverse_dictionary[valid_examples[i]] top_k = 8

nearest = (-sim[i_, : ]) .argsort() [l:top_k+l] log_str = "Nearest to %s:H % valid_word

for k in range(top_k):

close_word = reverse_dictionary[nearest[k]] log_str = "%s %s/* % (log_str\ close_word)

print(log_str)

final_embeddings = normalized_embeddings.eval()

以下为展示出来的平均损失，以及与验证单词相似度最高的单词，可以看到我们训练 的模型对名词、动词、形容词等类型的单词的相似词汇的识别都非常准确。因此由 Skip-Gram Word2Vec得到的向量空间表达(Vector Representations )是非常高质量的，近 义词在向量空间上的位置也是非常靠近的。

Average loss at step Average loss at step Average loss at step Average loss at step Average loss at step



92000 :    4.70622572589

94000 :    4.61680726242

96000 :    4.73945830989

98000 :    4.63924189049

100000 :    4.67957950294

Nearest to five: six^ four_, seven, eight) threej zero, two, nine.

Nearest to state: government, amalthea, habsburg^ asparagales, cegep, barrac uda^ dasyprocta^ connecticutj

Nearest to over: three, reginae』 from^ replace) trapezohedron^ around) brine, iit,

Nearest to were: are_, have, hadj was, while_, been, be_, wctj

Nearest to at: in, on_, mitral, agouti, triglycerides^ excerpts^ during, with in,

Nearest to called: agouti, akita, homeworld^ layoutsdasyprocta^ UNKj cegep.

referredj

... - '■ •

Nearest to about: disclosed, antimattervec，advocated, surgeries, defiance, disband^ legionnaire^

Nearest to which: that, this, gollancZj but, what4 alsOj it, and,

Nearest to three: four, five, six, two, seven, eightiitj nine.

Nearest to that: which, however, what, this, when, gollancz, butj ramps. Nearest to new: nonviolentaquila, assyrian, gardening, local, charcotj sub sistence^ ssbn_,

Nearest to eight: seven) siXj nine』 four, five, zero) three, mitral^

Nearest to no: any, thaler, boiled, gyroscopic^ pontificia^ grist, occupies, michelobj

Nearest to UNK: cebus, agouti^ cegepj dasyprocta^ mitral^ reginae^ callithri Xj microcebuSj

Nearest to used: referred, known, written, found, dasyprocta^ able, shown, m itralj

Nearest to up: out, thenb him, passes, eat3 pianos, gaku, offj

下面定义一个用来可视化Word2Vec效果的函数。这里low_dim_embs是降维到2维 的单词的空间向量，我们将在图表中展示每个单词的位置。我们使用pkscatter (—般将 matplotlib.pyplot命名为pit)显示散点图(单词的位置)，并用plt.annotate展示单词本身。 同时，使用plt.savefig保存图片到本地文件。

def plot_with_labels(low_dim_embs, labels, filename='tsne.png,):

assert low_dim_embs.shape[0] >= len(labels)"More labels than embeddings" pit.figure(figsize=(18j 18))

for i, label in enumerate(labels):

Xj y = low_dim_embs[ij:] pit.scatter(x? y) pit.annotate(labelj

xy=(x, y), xytext=(5j 2)^ textcoords=1 offset points、 ha='right'j va='bottom')

pit.savefig(filename)

我们使用skleam.manifold.TSNE实现降维，这里直接将原始的128维的嵌入向量降到 2维，再用前面的plotjvithjabels函数进行展示。这里只展示词频最高的100个单词的可 视化结果。

from sklearn.manifold import TSNE

import matplotlib.pyplot as pit

tsne = TSNE(perplexity=30> n_components=2, init='pea', n_iter=5000)

plot_only = 100

low_dim_embs = tsne.fit_transform(final_embeddings[:plot_only3:]) labels = [reverse_dictionary[i] for i in range(plot_only)] plot_with_labels(low_dim_embsj labels)

图7-3所示即为可视化效果，可以看到其中距离相近的单词在语义上具有很高的相似 性。例如，左上角为单个字母的聚集地；而冠词the、an、a和another则聚集在左边中部， 稍微靠右一点则有 him、himself、its、itself 和 them聚集;左下方有 will、could、would、 then。这里我们只展示了部分截图，感兴趣的读者可以在程序画出来的大图中进行观察。 对Word2Vec性能的评价，除了可视化观察，常用的方式还有Analogical Reasoning,即 直接预测语义、语境上的关系，例如让模型回答“king is queen as father is to _”这类 问题。Analogical Reasoning可以比较好地评测Word2Vec模型的准确性。在训练Word2Vec 模型时，为了获得比较好的结果，我们可以使用大规模的语料库，同时需要对参数进行调 试，选取最适合的值。

图7-3 TSNE降维后的Word2Vec的嵌入向量可视化图

##### 7.2 TensorFlow实现基于LSTM的语言模型

循环神经网络出现于20世纪80年代，在其发展早期，应用不是特别丰富。最近几 年由于神经网络结构的进步和GPU上深度学习训练效率的突破，IWN变得越来越流行。 RNN对时间序列数据非常有效，其每个神经元可通过内部组件保存之前输入的信息。

人每次思考时不会重头开始，而是保留之前思考的一些结果为现在的决策提供支持。 例如我们对话时，我们会根据上下文的信息理解一句话的含义，而不是对每一句话重头进 行分析。传统的神经网络不能实现这个功能，这可能是其一大缺陷。例如卷积神经网络虽 然可以对图像进行分类，但是可能无法对视频中每一帧图像发生的事情进行关联分析，我 们无法利用前一帧图像的信息，而循环神经网络则可以解决这个问题。RNN的结构如图 7-4所示，其最大特点是神经元的某些输出可作为其输入再次传输到神经元中，因此可以 利用之前的信息。

如图7-4所示，x,是RNN的输入，乂是RNN的一个节点，而是输出。我们对这个 RNN输入数据x,,然后通过网络计算并得到输出结果//,，再将某些信息(state,状态)传 到网络的输入。我们将输出屯与label进行比较可以得到误差，有了这个误差之后，就能 使用梯度下降(Gradient Descent)和 Back-Propagation Through Time ( BPTT)方法对网 络进行训练，BPTT与训练前馈神经网络的传统BP方法类似，也是使用反向传播求解梯 度并更新网络参数权重。另夕卜，还有一种方法叫Real-Time Recurrent Learning ( RTRL), 它可以正向求解梯度，不过其计算复杂度比较高。此外，还有介于BPTT和RTRL这两种 方法之间的混合方法，可用来缓解因为时间序列间隔过长带来的梯度弥散的问题。

如果我们将RNN中的循环展开成一个个串联的结构，如图7-5所示，就可以更好地 理解循环神经网络的结构了。RNN展开后，类似于有一系列输入x和一系列输出A的串 联的普通神经网络，上一层的神经网络会传递信息给下一层。这种串联的结构天然就非常 适合时间序列数据的处理和分析。需要注意的是，展开后的每一个层级的神经网络，其参 数都是相同的，我们并不需要训练成百上千层神经网络的参数，只需要训练一层RNN的 参数，这就是它结构巧妙的地方，这里共享参数的思想和卷积网络中权值共享的方式也很 类似。

图7-5循环神经网络展开示意图

RNN虽然被设计成可以处理整个时间序列信息，但是其记忆最深的还是最后输入的 一些信号。而更早之前的信号的强度则越来越低，最后只能起到一点辅助的作用，即决定 RNN输出的还是最后输入的一些信号。这样的缺陷导致RNN在早期的作用并不明显，慢 慢淡出了大家的视野。而后随着Long Sort Term Memory ( LSTM ) 57的发现，循环神经 网络重新回到了大家的视野,并逐渐在众多领域取得了很大的成功和突破，包括语音识别、 文本分类、语言模型、自动对话、机器翻译、图像标注等领域。

对于某些简单的问题，可能只需要最后输入的少量时序信息即可解决。但对某些复杂 问题，可能需要更早的一些信息，甚至是时间序列开头的信息，但间隔太远的输入信息, RNN是难以记忆的，因此长程依赖(Long-term Dependencies )是传统RNN的致命伤。 LSTM由Schmidhuber教授于1997年提出，它天生就是为了解决长程依赖而设计的，不 需要特别复杂地调试超参数，默认就可以记住长期的信息。LSTM的内部结构相比RNN 更复杂，如图7-6所示，其中包含了 4层神经网络，其中小圆圈是point-wise的操作，比 如向量加法、点乘等，而小矩形则代表一层可学习参数的神经网络。LSTM单元上面的那 条直线代表了 LSTM的状态state,它会贯穿所有串联在一起的LSTM单元，从第一个 LSTM单元一直流向最后一个LSTM单元，其中只有少量的线性干预和改变。状态state 在这条隧道中传递时，LSTM单元可以对其添加或删减信息，这些对信息流的修改操作由

LSTM中的Gates控制。这些Gates中包含了一个Sigmoid层和一个向量点乘的操作，这 个Sigmoid层的输出是0到1之间的值，它直接控制了信息传递的比例。如果为0代表不 允许信息传递，为1则代表让信息全部通过。每个LSTM单元中包含了 3个这样的Gates, 用来维护和控制单元的状态信息。凭借对状态信息的储存和修改，LSTM单元就可以实现 长程记忆。

©

©



|      |      |
| ---- | ---- |
| A    | J    |

-f>



在RNN的各种变种中，除了 LSTM,另一个非常流行的网络结构是Gated Recurrent Unit ( GRU )□ GRU的结构如图7-7所示，相比LSTM,其结构更加简单，比LSTM减少 了一个Gate，因此计算效率更高(每个单元每次计算时可节约几个矩阵运算操作)，同时 占用的内存也相对较少。在实际使用中，LSTM和GRU的差异不大，一般最后得到的准 确率指标等都近似，但是相对来说，GRU达到收敛状态时所需要的迭代数更少，也可以 说是训练速度更快。

Illustration of (a) LSTM and (b) galcd recurrent units, (a) t, f and o arc ihc inpuu fwgci and output gates, respectively, c and c denote ihc memory cell and the new memory cell content, (b) r and : arc the rcscl and update gales，and h and A arc the activation and ihc candidalc uclivalion.



图7-7 LSTM和GRU的结构

循环神经网络的应用非常广，不过用的最多的地方还是自然语言处理。用RNN训练

出的语言模型(Language Modeling ),其效果令人惊叹。我们可以输入大量莎士比亚的剧 本文字等信息给RNN，训练得到的语言模型可以模仿莎士比亚的文字，自动生成类似的 诗歌、剧本。下面的英文为语言模型生成的莎翁的诗歌，可以说是非常逼真，几乎可以以 假乱真。

PANDARUS:

Alas, I think he shall be come approached and the day When little srain would be attain'd into being never fed, And who is but a chain and subjects of his death,

I should not sleep.

Second Senator:

They are away this miseries, produced upon my soul, Breaking and strongly should be buried, when I perish The earth and thoughts of many states.

DUKE VINCENTIO:

Well, your wit is in the care of side and that.

Second Lord:

They would be ruled after this chamber, and my fair nues begun out of the fact, to be conveyed, Whose noble souls I'll have the heart of the wars.

Clown:

Come, sir, I will make did behold your worship.

VIOLA:

I'll drink it.

语言模型是NLP中非常重要的一个部分，同时也是语音识别、机器翻译和由图片生 成标题等任务的基础和关键。语言模型是一个可以预测语句的概率模型。给定上文的语境, 即历史出现的单词，语言模型可以预测下一个单词出现的概率。Penn Tree Bank ( PTB )

是在语言模型训练中经常使用的一个数据集，它的质量比较高，可以用来评测语言模型的 准确率，同时数据集不大，训练也比较快。下面我们就使用LSTM来实现一个语言模型， 其网络结构来自论文    Neural Network Regularization。

首先，我们下载PTB数据集并解压，确保解压后的文件路径和接下来Python的执行 路径一致。这个数据集中已经做了一些预处理，它包含1万个不同的单词，有句尾的标记， 同时将罕见的词汇统一处理为特殊字符。本节代码主要来自TensorFlow的开源实现58。

wget <http://www.fit.vutbr.cz/~imikolov/rnnlm/simple-examples.tgz> tar xvf simple-examples.tgz

我们下载TensorFlow Models库；并进入目录models/tutorials/mn/ptb。然后载入常用 的库，和TensorFlow Models中的PTB reader,借助它读取数据内容。读取数据内容的操 作比较烦琐，主要是将单词转为唯一的数字编码，以便神经网络处理。这部分实现的细节 我们不做讲解，感兴趣的读者可以阅读其源码。

git clone <https://github.com/tensorflow/models.git>

cd models/tutorials/rnn/ptb

import time

import numpy as np

import tensorflow as tf

import reader

下面定义语言模型处理输入数据的class, PTBInput。其中只有一个初始化方法 _init_()，我们读取参数config中的batch_size、num_steps到本地变量，这里num_steps 是LSTM的展开步数(unrolled steps of LSTM )。然后计算每个epoch的size,即每个epoch 内需要多少轮训练的迭代，可以通过将数据长度整除batch_size和mim_StepS得到。我们 使用 reader.ptb_producer 获取特征数据 input_data,以及 label 数据 targets，这里的 input_data 和targets都已经是定义好的tensor 了，每次执行都会获取一个batch的数据。

class PTB工nput(object):

def_init_(self, config, data, name=None):

self.batch_size = batch_size = config.batch_size

self.num_steps = num_steps = config.num_steps

self.epoch_size = ((len(data) // batch一size) - 1) // num_steps

self.input_dataj self.targets = reader.ptb_producer( data^ batch_size_, num_stepsJ name=name)

接着定义语言模型的class, PTBModelo首先依然是初始化函数_^^_()，其中包含 三个参数，训练标记is_training、配置参数config,以及PTBInput类的实例input_D我们 读取 input_中的 batch_size 和 num_steps，然后读取 config 中的 hidden_size、vocab_size 到 本地变量。这里hidden_size是LSTM的节点数，vocab_size是词汇表的大小。

class PTBModel(object):

def _init_(selfis_trainingj configj input」： self._input = input_

batch_size = input_.batch_size num一steps = input_.num_steps size = config.hidden_size vocab_size = config.vocab_size

接下来使用tf.contrib.mn.BasicLSTMCell设置我们默认的LSTM单元，其中隐含节点 数为前面提取的 hidden_size, forget_bias (即 forget gate 的 bias )为 0, state_is_tuple 也为 True,这代表接受和返回的state将是2-tuple的形式。同时，如果在训练状态且Dropout 的keep_prob小于1，则在前面的lstm_cell之后接一个Dropout层，这里的做法是调用 tf.contrib.mn.DropoutWrapper 函数。最后使用 RNN 堆叠函数 tf.contrib.mn.MultiRNNCell 将前面构造的lstm_cell多层堆叠得到cell，堆叠次数为config中的numjayers,这里同样 将state_is_tuple设为True,并用cell.zero_state设置LSTM单元的初始化状态为0。这里 需要注意，LSTM单元可以读入一个单词并结合之前储存的状态state计算下一个单词出 现的概率分布，并且每次读取一个单词后它的状态state会被更新。

def lstm_cell():

return tf.contrib.rnn.BasicLSTMCell(

size, forget_bias=0.0, state_is_tuple=True)

attn_cell = lstm_cell

if is_training and config.keep_prob < 1: def attn_cell():

return tf.contrib.rnn.DropoutWrapper(

lstm_cell()output_keep_prob=config.keep_prob) cell = tf.contrib.rnn.MultiRNNCell(

[attn_cell() for _ in range(config.num_layers)]j state一is_tuple=True)

self._initial_state = cell.zero_state(batch_size? tf.float32)

我们创建网络的词嵌入embedding部分，embedding即为将one-hot的编码格式的单 词转化为向量表达形式，在7.1节Word2Vec中已经讲到了。因为这部分在GPU中还没有 很好的实现，所以我们依然使用with tf.deviCe(7cpu:0n)将计算限定在CPU中进行。然后 我们初始化embedding矩阵，其行数设为词汇表数vocab_size，列数(即每个单词的向量 表达的维数)设为hidden_size，和LSTM单元中的隐含节点数一致。在训练过程中， embedding的参数可以被优化和更新。接下来使用tf.nn.embeddingjookup查询单词对应 的向量表达获得inputs。同时，如果为训练状态则再添加上一层Dropout。.

with tf.device(”/cpu:0n): embedding = tf.get_variable(

"embedding"j [vocab_size_, size] 4 dtype=tf.float32) inputs = tf.nn.embedding_lookup(embeddingj input_.input一data)

if is_training and config.keep_prob < 1:

inputs = tf.nn.dropout(inputsconfig.keep_prob)

接下来定义输出outputs,我们先使用tf.variable_scope将接下来的操作的名称设为 RNN。一般为了控制训练过程，我们会限制梯度在反向传播时可以展开的步数为一个固 定的值，而这个步数也就是num_steps。这里我们设置一•个循环，循环长度为num_steps， 来控制梯度的传播。并且从第2次循环开始，我们使用tf.get_varible_scope.reuse_variables 设置复用变量。在每次循环内，我们传入inputs和state到堆叠的LSTM单元(即cell) 中。这里注意inputs有3个维度，第1个维度代表是batch中的第几个样本，第2个维度 代表是样本中的第几个单词，第3个维度是单词的向量表达的维度，而inputs[:, time_step，：]代表所有样本的第time_step个单词。这里我们得到输出cell_output和更新后 的state。最后我们将结果cell_output添加到输出列表outputs。

outputs =[]

state = self._initial_state

—「TensorFlow 实战

with tf.variable_scope("RNN"):

for time_step in range(num_steps):

if time_step > 0: tf.get_variable_scope().reuse_variables()

(cell_output_, state) = cell (inputs    time一step,    state)

outputs.append(cell_output)

我们将output的内容用tf.concat串接到一起，并使用tf.reshape将其转为一个很长的 —维向量。接下来是Softmax层，先定义权重softmax_w和偏置softmax_b,然后使用 tf.matmul将输出output乘上权重并加上偏置得到logits，即网络最后的输出。然后定义损 失 loss,这里直接使用 tf.contrib.legacy_seq2seq.sequence_loss_by_example 计算输出 logits 和 targets 的偏差，这里的 sequence_loss 即 target words 的 average negative log probability, 其定义为Zoss = ~^Zitilnptar5et.o然后使用tf.reduce_sum汇总batch的误差，再计算平 均到每个样本的误差cost。并且我们保留最终的状态为fmal_state。此时，如果不是训练 状态，则直接返回。

output = tf.reshape(tf.concat(outputSj 1)_, [-1^ size]) softmax_w = tf.get_variable(

Hsoftmax_w"J [size, vocab_size]_, dtype=tf.float32) softmax_b = tf.get_variable("softmax_bn, [vocab_size]dtype=tf.float32) logits = tf.matmul(outputsoftmax_w) + softmax一b

loss = tf.contrib.Iegacy_seq2seq.sequence_loss_by_example(

[logits],

[tf.reshape(input_.targetsj [-1])]^

[tf.ones([batch_size * num_steps]dtype=tf.float32)]) self._cost = cost = tf.reduce_sum(loss) / batch一size self,_final_state = state

if not is_training: return

下面定义学习速率的变量Jr,并将其设为不可训练。再使用tf.trainablejariables获 取全部可训练的参数tvars。这里针对前面得到的cost,计算tvars的梯度，并用 tf.clip_by_global_nonn 设置梯度的最大范数 max_grad_norm0 这即是 Gradient Clipping 的 方法，控制梯度的最大范数，某种程度上起到正则化的效果。Gradient Clipping可以防止

Gradient Explosion梯度爆炸的问题，如果对梯度不加限制，则可能会因为迭代中梯度过 大导致训练难以收敛。然后定义优化器为GradientDescent优化器。再创建训练操作 _train_op,用optimizer.apply_gradients将前面clip过的梯度应用到所有可训练的参数tvars 上，然后使用 tf.contrib.framework.get_or_create_global_step 生成全局统一的训练步数。

self._lr = tf.Variable(0.0^ trainable=False) tvars = tf.trainable_variables()

grads4 _ = tf.clip_by_global_norm(tf.gradients(cost^ tvars), config.max_grad_norm)

optimizer = tf.train.GradientDescentOptimizer(self._lr) self._train_op = optimizer.apply_gradients(zip(gradstvars),

global_step=tf.contrib.framework.get_or_create_global_step())

这里设置一个名*_new_lr ( new learning rate )的placeholder用以控制学习速率，同 时定义操作_lr_update，它使用tf.assign将」iew_lr的值赋给当前的学习速率_lr。再定义一 个assignji■的函数，用来在外部控制模型的学习速率，方式是将学习速率值传入_new_lr 这个placeholder，并执4〒_update_lr操作完成对学习速率的修改。

self._new_lr = tf.placeholder^

tf .float 32 shape=[] j name="new_learning_rate,*)

self._lr_update = tf.assign(self._lr\ self,_new_lr)

def assign_lr(selfsessiorij lr_value):

session.run(self._lr_updatej feed_dict={self._new_lr: lr_value})

至此，模型定义的部分就完成了。我们再定义这个PTBModel class的一些property, Python中的(^property装饰器可以将返回变.量设为只读，防止修改变量引发的问题。这里 定义 input、initial_state、cost、final_state、lr、train_op 为 property,方便夕卜部访问。

^property def input(self):

return self.一input

@property

def initial_state(self):

TensorFlow 实战

return self._initial_state

^property def cost(self):

return self._cost

^property

def final_state(self): return self._final一state

^property def lr(self):

return self._lr

^property

def train_op(self): return self._train_op

接下来定义几种不同大小的模型的参数。首先是小模型的设置，我们先解释各个参数 的含义，这里的init_scale是网络中权重值的初始scale; leaming_rate是学习速率的初始 值;max_grad_nomi即前面提到的梯度的最大范数;num_layers是LSTM可以堆叠的层数； num_steps是LSTM梯度反向传播的展开步数；hidden_size是LSTM内的隐含节点数； max.epoch是初始学习速率可训练的epoch数，在此之后需要调整学习速率； max_max_epoch是总共可训练的epoch数；keep_prob是dropout层的保留节点的比例； lr_decay是学习速率的衰减速度；batch_size是每个batch中样本的数量。具体每个参数的 值，在不同配置中对比才有意义，我们会在接下来的几个配置中讨论具体数值/

class SmallConfig(object): init_scale = 0.1 learning一rate = 1.0 max_grad_norm = 5 num_layers =2 num一steps = 20 hidden一size = 200

max_epoch = 4 max_max_epoch = 13 keep_prob = 1.0 lr_decay = 0.5 batch_size = 20 vocab一size = 10000

这里可以看到，在MediumConfig中型模型中，我们减小了 init_scale,即希望权重初 值不要过大，小一些有利于温和的训练；学习速率和最大梯度范数不变，LSTM层数也不 变；这里将梯度反向传播的展开步数num_steps从20增大到35 ; hidden_size和 max_max_epoch也相应地增大约3倍；同时：这里开始设置dropout的keep_prob到0.5， 而之前设为1即没有dropout；因为学习的迭代次数增大，因此将学习速率的衰减速率 lr_decay也减小了； batch_size和词汇表vocab_size的大小都保持不变。

class MediumConfig(object): init_scale = 0.05 learning_rate = 1.0 max_grad_norm = 5 num_layers =2 num_steps = 35 hidden_size = 650 max_epoch =6 max_max_epoch = 39 keep_prob = 0.5 lr一decay = 0.8 batch一size =20 vocab_size = 10000

LargeConfig大型模型进一步缩小了 init_scale ;并大大放宽了最大梯度范数 max_grad_norm 到 10;同时将 hidden_size 提升到了 1500,并且 max_epoch、max_max_epoch 也相应地增大了；而keep_drOp则因为模型复杂度的上升继续下降。学习速率的衰减速率 lr_decay也进一步减小。

class LargeConfig(object): init_scale = 0.04

—厂 TensorFlow 实故

learning_rate = 1.0 max_grad_norm = 10 num_layers = 2 num_steps = 35 hidden_size = 1500 max_epoch = 14 max_max一epoch = 55 keep_prob = 0.35 lr_decay = 1 / 1.15 batch_size = 20 vocab_size = 10000

这里的TestConfig只是为测试用，参数都尽量使用最小值，只是为了测试可以完整运 行模型。

class TestConfig(object): init_scale = 0.1 learning_rate = 1.0 max_grad_norm = 1 num_layers = 1 num_steps = 2 hidden_size = 2 max_epoch = 1 max_max_epoch = 1 keep_prob = 1.0 lr_decay = 0.5 batch_size = 20 vocab_size = 10000

下面定义训练一个epoch数据的函数nm_epOch。我们记录当前时间，初始化损失costs 和迭代数iters,并执行model.initial_state来初始化状态并获得初始状态。接着创建输出结 果的字典表fetches，其中包括cost和fmal_state，如果有评测操作eval_op，也一并加入 fetches。接着我们进入训练循环中，次数即为ep0Ch_SiZe。在每次循环中，我们生成训练 用的feed_dict,将全部LSTM单元的state加入feed_dict中，然后传入feed_dict并执行

fetches对网络进行一次训练，并拿到cost和state。这里我们累加cost到costs,并累力口 num_steps到iters。我们每完成约10%的epoch,就进行一次结果的展示，依次展示当前 epoch的进度、perplexity (即平均cost的自然常数指数，是语言模型中用来比较模型性能 的重要指标，越低代表模型输出的概率分布在预测样本上越好)和训练速嵐单词数每秒)。 最后返回perplexity作为函数结果。

def run_epoch(session., model』eval_op=None, verbose=False): start_time = time.time() costs = 0.0

iters =0

state = session.run(model.initial_state)

fetches = {

"cost": model.cost^

"final_staten: model.final_state_,

}

if eval_op is not None:

fetches[”eval一op"] = eval_op

for step in range(model.input.epoch_size): feed一diet = {}

for ij (Cj h) in enumerate(model.initial_state): feed_dict[c] = state[i].c feed_dict[h] = state[i].h

vals = session.run(fetches> feed_dict)

cost = vals["cost"]

state = vals[nfinal_state"]

costs += cost

iters += model.input.num_steps

if verbose and step % (model.input.epoch_size // 10) == 10:

TensorFlow 实战

print("%.3f perplexity: %.3f speed: %.0f wps" %

(step * 1.0 / model.input.epoch_size_, np.exp(costs / iters), iters * model.input.batch_size / (time.time() - start_time)))

return np.exp(costs / iters)

我们使用reader.Ptb_raW_data直接读取解压后的数据，得到训练数据、验证数据和测 试数据。这里定义训练模型的配置为SmallConfig,读者也可自行测试其他大小的模型。 需要注意的是测试配置eval_config需和训练配置一致，这里将测试配置的batch_size和 num_ steps修改为1。

raw_data = reader.ptb_raw_data('simple-examples/data/') train_data, valid_data? test_data^ _ = raw_data

config = SmallConfig()

eval_config = SmallConfig()

eval_config.batch_size = 1

eval_config.num_steps = 1

我们创建默认的Graph,并使用tf.random_uniform_initializer设置参数的初始化器， 令参数范围在［-init_scale, init_scale］之间。然后使用PTBInput和PTBModel创建一个用来 训练的模型m,以及用来验证的模型mvalid和测试的模型mtest，其中训练和验证模型直 接使用前面的config,测试模型则使用前面的测试配置eval_configo

with tf.Graph().as_default():

initializer = tf.random一uniform_initializer(-config.init_scale,

config.init_scale)    .

with tf.name_scope("Train"):

train_input = PTBInput (conf ig=conf ig^ data=train_data, name=,,TrainInput") with tf. variable一scope(nModeln reuse=None_, initializer=initializer):

m = PTBModel(is_training=True, config=config> input_=train_input)

with tf.name_scope("Valid"):

valid_input = PTBInput(config=config,data=valid_data^name="ValidInputn)

with tf.variable_scope("Model"reuse=Truej initializer=initializer): mvalid = PTBModel(is_training=Falsej config=config_, input_=valid_input)

with tf .name_scope(*'Test"):

test_input = PTBInput(config=eval_configj data=test_data_, name="TestInput")

with tf. variable 一 scope(.’Model"_, reuse=Truej initializer=initializer): mtest = PTBModel(is_training=Falsej config=eval_configJ

input_=test_input)

我们使用tf.train.Supervisor()创建训练的管理器sv,并使用sv.managed_session创建默 认session,再执行训练多个epoch数据的循环。在每个epoch循环内，我们先计算累计的 学习速率衰减值，这里只需计算超过max_epoch的轮数，再求lr_decay的超出轮数次幂 即可。然后将初始学习速率乘上累计的衰减，并更新学习速率。然后在循环内执行一个 epoch的训练和验证，并输出当前的学习速率、训练和验证集上的perplexity。在完成全音P 训练后，计算并输出模型在测试集上的perplexity。

sv = tf.train.Supervisor^)

with sv.managed_session() as session:

for i in range(config.max_max_epoch):

lr_decay = config.lr_decay ** max(i + 1 - config.max_epoch_, 0.0) m.assign_lr(sessionconfig.learning_rate * lr_decay)

print("Epoch: %d Learning rate: %.3f" % (i + 1, session.run(m.lr))) train_perplexity = run_epoch(session4 m, eval_op=m.train_opj

verbose=True)

print("Epoch: %d Train Perplexity: %.3f" % (i + 1, train_perplexity)) valid_perplexity = run_epoch(session, mvalid)

print("Epoch: %d Valid Perplexity: %.3f" % (i + 1, valid_perplexity))

test_perplexity = run_epoch(sessionj mtest) print("Test Perplexity: %.3f" % test_perplexity)

我们来看SmallConfig小型模型的最后结果，我们在i7 6900K和GTX 1080上的训

TensorFlow 实战

练速度可达21000单词每秒。同时在最后一个epoch中，训练集上可达36.9的perplexity, 而验证集和测试集上分别可达122.3和116.7的perplexity。

Epoch: 13 Learnign rate: 0.004

| 0.004 perplexity: | 56.003 speed: | 13005 wps |
| ----------------- | ------------- | --------- |
| 0.104 perplexity: | 41.096 speed: | 21836 wps |
| 0.204 perplexity: | 45.000 speed: | 21891 wps |
| 0.304 perplexity: | 43.224 speed: | 21738 wps |
| 0.404 perplexity: | 42.508 speed: | 21529 wps |
| 0.504 perplexity: | 41.803 speed: | 21565 wps |
| 0.604 perplexity: | 40.425 speed: | 21470 wps |
| 0.703 perplexity: | 39.768 speed: | 21418 wps |
| 0.803 perplexity: | 39.088 speed: | 21480 wps |
| 0.903 perplexity: | 37.753 speed: | 21493 wps |

Epoch: 13 Train Perplexity: 36.949

Epoch: 13 Valid Perplexity: 122.300

Test Perplexity: 116.763

读者可以自行测试中型模型和大型模型，在原论文中提到在中型模型上可以达到(训 练集：48.45,验证集：86.16,测试集：82.07 )的效果，在大型模型上，可以达到(训练 集：37.87,验证集：82.62,测试集：78.29 )的效果。本节我们实现了一个基于LSTM的 语言模型，读者应该了解到LSTM在处理文本等时序数据中的作用了。LSTM可以存储 状态，并依靠状态对当前的输入进行处理分析和预测。RNN和LSTM赋予了神经网络记 忆和储存过往信息的能力，可以模仿人类的一些简单的记忆和推理功能。而目前，注意力 (attention )机制是RNN和NLP领域研究的热点，这种机制让机器可以更好地模拟人脑的 功能。在图像标题生成任务中，包含注意力机制的RNN可以对某一区域的图像进行分析， 并生成对应的文字描述，有兴趣的读者可以阅读论文Show, Attend and Tell: Neural Image Caption Generation with Visual dHenft’on 了解这部分的相关信息。

188

[www.aibbt.com](http://www.aibbt.com) 让未来触手可及

##### 7.3 TensorFlow 实现 Bidirectional LSTM Classifier

双向循环神经闲络(Bidirectional Recurrent Neural Networks59，Bi-RNN )是由 Schuster 和Paliwal于1997年首次提出的，和LSTM是在同一年被提出的。Bi-RNN的主要目标是

增加RNN可利用的信息。比如普通的MLP对数据长度等有限制，而RNN虽然可以处理 不固定长度的时序数据，但是无法利用某个历史输入的未来信息。Bi-RNN则正好相反， 它可以同时使用时序数据中某个输入的历史及未来数据。其实现原理很简单，将时序方向 相反的两个循环神经网络连接到同一个输出，通过这种结构，输出层就可以同时获取历史 和未来信息了。

在需要上下文环境的情况中，Bi-RNN将会非常有用，比如在手写文字识别时，如果 有当前要识别的单词的前面和后面一个单词的信息，那么将非常有利于识别。同样，我们 在阅读文章时，有时也需要通过下文的语境来推测文中某句话的准确含义。对Language Modeling这类问题，可能BhRNN并不合适，因为我们的目标就是通过前文预测下一个 单词，这里不能将下文信息传给模型。对很多分类问题，比如手写文字识别、机器翻译、 蛋白结构预测等，使用Bi-RNN将会大大提升模型效果。百度在其语音识别中也是通过 Bi-RNN综合考虑上下文语境，将其模型准确率大大提升。

Bi-RNN网络结构的核心是把一个普通的单向的RNN拆成两个方向，一个是随时序 正向的，一个是逆着时序的反向的，如图7-8所示。这样当前时间节点的输出就可以同时 利用正向、反向两个方向的信息，而不像普通RNN需要等到后面时间节点才可以获取未 来信息。这两个不同方向的RNN之间不会共用state,即正向RNN的输出state只会传给 正向的RNN,反向RNN的输出只会传给反向的RNN,它们之间没有直接连接。如图7-9 所示，每一个时间节点的输入会分别传到正向和反向的RNN中，它们根据各自的状态产 生输出，这两份输出会一起连接到Bi-RNN的输出节点，共同合成最终输出。我们可以看 到，Bi-RNN的网络中虽然两个方向的RNN基本没有交集，但是因为它们共同合成了输 出，所以它们对当前时间节点输出的贡献(或造成的loss)就可以在训练中被计算出来, 并且它们的参数会根据梯度被优化到合适的值。

Bi-RNN在训练时和普通单向RNN非常类似，因为两个不同方向的RNN之间几乎没 有交集，因此它们可以分别展开为普通的前馈网络。不过在使用BPTT( back-propagation through time )算法训练时，我们无法同时更新状态和输出。同时，正向state在t=l时未 知，且反向state在t=I■时未知，即state在各自方向的开始处未知，这里需要人工设置。 此外，正向状态的导数在t=T时未知，且反向state的导数在t=l时未知，即state的导数 在结尾处未知，这里一般需要设为0代表此时对参数更新不重要。然后正式开始训练步骤： 第一步，我们对输入数据做forward pass操作，即inference的操作，我们先沿着1-»T方 向计算正向RNN的state,再沿着T ->1方向计算反向RNN的state,然后获得输出output；

TensorFlow 实战

第二步，我们进行backward pass操作，即对目标函数求导的操作，我们先对输出output 求导，然后沿着T-H方向计算正向RNN的state的导数，再沿着WT方向计算反向RNN 的state的导数；第三步根据求得的梯度值更新模型参数，完成一次训练。

![img](06TensorFlow9e18_c4875a088c74095baibbt-93.jpg)



![img](06TensorFlow9e18_c4875a088c74095baibbt-94.jpg)



⑻    (b)

Structure overview

(a)    unidirectional RNN

(b)    bidirectional RNN

图7-8 RNN和Bi-RNN结构对比图

FORWARD

STATES

BACKWARD STATES

t-1    t    t+1

图7-9 Bi-RNN结构示意图



Bi-RNN中的每个RNN单元既可以是传统的RNN,也可以是LSTM单元或者GRU 单元，思路是一致的，而且我们也可以在一层Bi-RNN上再叠加一层Bi-RNN，即上一层 Bi-RNN的输出再作为下一层Bi-RNN的输入，可以进一步抽象提炼特征。如果最后用作 分类任务，我们可以将Bi-RNN的输出序列连接一个全连接层，或者连接全局平均池化 Global Average Pooling,最后再接Softmax层，这部分和使用卷积网络的输出进行分类 的做法一样。

下面我们就使用 TensorFlow 实现一个 Bidirectional LSTM Classifier,并在 MNIST 数 据集上进行测试。先载入TensorFlow、NumPy,以及TensorFlow自带的MNIST数据读耳又 器。与最开始的几章一样，我们直接使用input_data.read_data_sets下载并读取MNIST数 据集。本节代码主要来自TensorFlow-Examples的开源实现6Q。

import tensorflow as tf

import numpy as np

from tensorflow.examples.tutorials.mnist import input_data

mnist = input_data.read_data_sets（"/tmp/data/", one_hot=True）

然后设置训练参数。我们设置学习速率为0.01 （因为优化器将选择Adam,所以学习 速率较低），最大训练样本数为40万，batCh_Size为128,同时设置每间隔10次训练就展 示一次训练情况。

learning_rate = 0.01

max_samples = 400000

batch_size = 128

display_step = 10

因为MNIST的图像尺寸为2似28,因此输入n_input为28（图像的宽），同时n_steps 即LSTM的展开步数（unrolled steps of LSTM ），也设置为28 （图像的高），这样图像的 全部信息就都使用上了。和前一节使用LSTM处理文本数据时一次读取一个单词类似， 这里是一次读取一行像素（28个像素点），然后下一个时间点再传入下一行像素点。这里 n_hidden （LSTM的隐藏节点数）设为256,而n_classes （MNIST数据集的分类数目）则 设为10。

n_input = 28

n_steps = 28

n_hidden = 256

n_classes = 10

我们创建输入X和学习目标y的placeholder。和使用卷积神经网络做分类时类似， 这里输入x中每一个样本可直接使用二维的结构,而不必像MLP那样需要转为一维结构。 不过这里的样本的二维的含义，和卷积网络中空间的二维不同，我们的样本被理解为一个 时间序列，第一个维度是时间点n_stepS,第二个维度是每个时间点的数据n_inputo同时， 我们设创建最后的Softmax层的weights和biases,这里直接使用tf.random_normal初始化

f TensorFlow 实战

这些参数。因为是双向LSTM,有forward和backwrad两个LSTM的cell,所以weights 的参数量也翻倍，变为2*n_hidden。

x = tf.placeholder("float[None, n_steps, n_input])

y = tf.placeholder("float"j [None, n_classes])

weights = tf.Variable(tf.random_normal([2*n_hiddenj n_classes]))

biases = tf.Variable(tf.random_normal([n_classes]))

下面就定义Bidirectional LSTM网络的生成函数。我们先对数据进行一些处理，把形 状为(batch_size，n_steps, n_input)的输入变成长度为n_steps的列表，而其中元素形状 为(batch_size，n_input)。然后输入进行转置，使用tf.transpose(x，[1，0，2])将第一个维度 batch_size和第二个维度n_steps进行交换。接着使用tf.reshape将输入x变形为 (n_steps*batch_size, n_input)的形状，再使用tf.split将x拆成长度为n_steps的列表，列表 中每个tensor的尺寸都是(batch_size, n_input),这样符合LSTM单元的输入格式。下面 使用 tf.contrib.mn.BasicLSTMCell 分别创建 forward 和 backward 的 LSTM 单元，它们的隐 藏节点数都设为n_hidden，而forget_bias都设为1。然后直接将正向的lstm_fw_cell和反 向的 lstm_bw_cell 传入 Bi-RNN 接口 tf.nn.bidirectional_mn 中，生成双向 LSTM,并传入 x 作为输入。最后对双向LSTM的输出结果outputs做一个矩阵乘法并加上偏置，这里的参 数即为前面定义的weights和biaseso

def BiRNN(x, weights, biases):

x = tf.transpose(x, [1， 0， 2]) x = tf.reshape(Xj [-1^ n_input]) x = tf.split(Xj n_steps)

lstm_fw_cell = tf. contrib. rnn.BasicLSTMCell(n_hidden_, forget_ bias=1.0) lstm_bw_cell = tf.contrib.rnn.BasicLSTMCell(n_hidderb forget_ bias=1.0)

outputs^    = tf.contrib.rnn.static_bidirectional_rnn(lstm_fw_cell^

lstm_bw_ cell, x, dtype=tf.float32)

return tf.matmul(outputs[-1]? weights) + biases

我们使用刚才定义好的函数生成我们的Bidirectional LSTM网络，对最后输出的结果

使用 tf.nn.softmax_cross_entropy_with_logits 进行 Softmax 处理并计算损失，然后使用 tf.reduce_mean计算平均cost。我们定义优化器为Adam,学习速率即为前面定义的 leaming_rate。再使用tf.argmax得到模型预测的类别，然后用tf.equal判断是否预测正确， 最后用tf.reduce_mean求得平均准确率。

pred = BiRNN(x^ weights, biases)

cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits=pred^ labels=y))

optimizer = tf.train.AdamOptimizer(learning_rate=learning_rate).minimize( cost)

correct_pred = tf.equal(tf.argmax(pred^1)^ tf.argmaxCy^l))

accuracy = tf.reduce_mean(tf.cast(correct_pred> tf.float32))

init = tf.global_variables_initializer()

下面开始执行训练和测试操作。第一步是执行初始化参数，然后定义一个训练的循环， 保持总训练样本数(迭代次数*batch_size)小于之前设定的值。在每一轮训练迭代中，我 们使用mnist.train.next_batch拿到一个batch的数据并使用reshape改变其形状。接着，将 包含输入x和训练目标y的feed_dict传入，执行一次训练操作并更新模型参数。每当迭 代数为display_step的整数倍时，我们计算一次当前batch数据的预测准确率和loss并展 示出来。

with tf.Session() as sess: sess.run(init) step =1

while step * batch_size < max_samples:

batch一x, batch_y = mnist.train.next_batch(batch_size) batch_x = batch_x.reshape((batch_size, n_steps_, n_input)) sess.run(optimizer\ feed_dict={x: batch_Xj y: batch_y}) if step % display_step == 0:

acc = sess.run(accuracy_, feed_dict={x: batch_Xj y: batch_y}) loss = sess.run(cost4 feed_dict={x: batch_xJ y: batch_y})

—厂 TensorFlow 实战

print("Iter " + str(step*batch_size) + Minibatch Loss= " + \

"{:.6f}".format(loss) + Training Accuracy: " + \

”{:.5f}".format(acc))

step += 1

print("Optimization Finished!n)

全部训练迭代结束后，我们使用训练好的模型，对mnisttestimages中全部的测试数 据进行预测，并将准确率展示出来。

test_len = 10000

test_data = mnist.test.images[:test_len].reshape((-l4 n_steps, n_input)) test_label = mnist.test.labels[:test_len] print("Testing Accuracy:",

sess.run(accuracyj feed_dict={x: test_data, y: test_label}))

在完成了 40万个样本的训练后，我们看一下模型在训练集和测试集上的表现。在训 练集上我们的预测准确率非常高，基本都是1，而在包含10000个样本的测试集上也有 0.983的准确率。

Iter 394240Minibatch Loss= 0.025686, Training Accuracy= 0.99219

Iter 395520,    Minibatch    Loss=    0.001847,    Training    Accuracy=    1.00000

Iter 396800,    Minibatch    Loss=    0.00904%    Training    Accuracy=    1.00000

Iter 398080^    Minibatch    Loss=    0.015611^    Training    Accuracy=    1.00000

Iter 399360,    Minibatch    Loss=    0.009190)    Training    Accuracy=    1.00000

Optimization Finished!

Testing Accuracy: 0.983

Bidirectional LSTM Classifier在MNIST数据集上的表现虽然不如卷积神经网络，但 也达到了一个很不错的水平。Bi-RNN乃至双向LSTM网络在时间序列分类任务上能达到 较好的表现，是因为它能做到同时利用时间序列的历史和未来信息，结合上下文信息，对 结果进行综合判定。虽然在图片这种空间结构显著的数据上不如卷积神经网络，但在无空 间结构的单纯的时间序列上，相信Bi-RNN和Bi-LSTM会更具优势。

[www.aibbt.com](http://www.aibbt.com) 让未来触手可及
