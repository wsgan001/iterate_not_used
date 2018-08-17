
刚刚的方法做了一些显而易见的 feature。


# 关键词搜索（进阶版）

Kaggle竞赛题：https://www.kaggle.com/c/home-depot-product-search-relevance

鉴于课件里已经完整的show了NLTK在各个NLP处理上的用法，我这里就不再重复使用了。

本篇的教程里会尽量用点不一样的库，让大家感受一下Python NLP领域各个库的优缺点。

同时，

在进阶版故事中，我们将讨论除了一些显而易见的“自制feature”之外，更加牛x的算法：

- String Distance

- TF-iDF

- Word2Vec（具体原理会在下堂课继续）

注意，前面的预处理部分维持不变（懒得折腾。。预处理这事儿 要玩儿可以玩儿一年。。）

前面两大步骤都是一样的。



## Step 3: 进阶版文本特征

这里我们讨论几个有点逼格的文本特征算法：

#### Levenshtein


使用 Levenshtein 很直观，直接调用 python 标准库即可


```python
import Levenshtein

Levenshtein.ratio('hello', 'hello world')
```

```
0.625
```

Levenshtein 距离，就是左边这个 str 需要经过多少次修改才能变成右边这个 str ，然后再除以左边这个str 的长度。

好的，接下来我们把 search_term 和 product_title 进行比较：

```python
df_all['dist_in_title'] = df_all.apply(lambda x:Levenshtein.ratio(x['search_term'],x['product_title']), axis=1)
```

同理，对产品介绍进行比较：

```python
df_all['dist_in_desc'] = df_all.apply(lambda x:Levenshtein.ratio(x['search_term'],x['product_description']), axis=1)
```

#### TF-iDF

<span style="color:red;">这部分听得还是有点云里雾里，对于 tf-idf ，我之前理解它是对于每个文本来说的，里面的单词占所有文本中的单词的比例，而且，大家都有的单词就去掉，然后，剩下的占比比较大的单词就代表了这个文本。这个理解有问题吗？</span>

TF-iDF 稍微复杂点儿，因为它涉及到一个需要提前把所有文本计算统计一下的过程。

我们首先搞一个新的 column，叫 all_texts, 里面是所有的 texts。（我并没有算上 search term, 因为他们不是一个结构完整的句子，可能会影响 tfidf的学习）。为了防止句子格式不完整，我们也强制给他们加上句号。<span style="color:red;">为什么不算上 search term ？为什么需要加上句号？</span>

*注意：这里我们最严谨的做法是把 train/test 先分开，然后只在 train 上做 tfidf 的学习，并在 test 上直接转化。然而由于我不想把整个文章顺序打乱（因为本来 train/test 分开的步骤在最后），因为我希望大家看的时候可以跟简单版教程有很好的结构对照，所以我直接 tfidf 用在全部的语料集上。如果想安慰自己的话，你可以这么考虑：我们的算法虽然看到了 test 的文本内容，但是没有 test 的 label 。可以姑且认为 test 的文本内容本身也就是可见的。并且，这种行为在 kaggle 的竞赛中并没有被禁止，很多竞赛的 kernel 提交算法都有这问题，所以我们姑且先不 care 。但是我个人非常不赞同这么做。同学们如果自己在线下做实验的时候，希望可以做到严谨。不要cheating哦~*<span style="color:red;">嗯，但是这个看到 test 集的已知数据的方法是一种 cheating吗？</span>


```python
df_all['all_texts']=df_all['product_title'] + ' . ' + df_all['product_description'] + ' . '
```

搞完之后，长这样：


```python
df_all['all_texts'][:5]
```

```
0    simpson strong-ti 12-gaug angl . not onli do a...
1    simpson strong-ti 12-gaug angl . not onli do a...
2    behr premium textur deckov 1-gal. #sc-141 tugb...
3    delta vero 1-handl shower onli faucet trim kit...
4    delta vero 1-handl shower onli faucet trim kit...
Name: all_texts, dtype: object
```



然后，我们取出所有的单字，做成一个我们的单词字典：

基本版教程这里我们用 gensim，为了更加细致的分解 TFIDF 的步骤动作；（其实 sklearn 本身也有更加简单好用的 tfidf 模型，可以一步完成，详情见第二课 stock news 。）

Tokenize可以用各家或者各种方法，就是把长长的 string 变成 list of tokens 。包括 NLTK，SKLEARN 都有自家的解决方案。或者你自己直接用 str 自带的 split() 方法，也是一种 tokenize。只要记住，你这里用什么，那么之后你的文本处理都得用什么。


```python
from gensim.utils import tokenize
from gensim.corpora.dictionary import Dictionary
dictionary = Dictionary(list(tokenize(x, errors='ignore')) for x in df_all['all_texts'].values)
print(dictionary)
```

```
Dictionary(221877 unique tokens: ['fontain', 'extrusion', 'shiftingbreak', 'unfold', 'fibersdesign']...)
```


楼上可见，我们得到了 221877 个单词的大词典。我们以此准备出一个语料库。
因为语料库一般都很大，所以我建议我们做语料的时候都用个 iterator 来实现。<span style="color:red;">嗯。</span>

于是我们写一个类(class)：

这个类所做的事情也很简单，就是扫便我们所有的语料，并且转化成简单的单词的个数计算。（Bag-of-Words）


```python
class MyCorpus(object):
    def __iter__(self):
        for x in df_all['all_texts'].values:
            yield dictionary.doc2bow(list(tokenize(x, errors='ignore')))

# 这里这么折腾一下，仅仅是为了内存friendly。面对大量corpus数据时，你直接存成一个list，会使得整个运行变得很慢。
# 所以我们搞成这样，一次只输出一组。但本质上依旧长得跟 [['sentence', '1'], ['sentence', '2'], ...]一样

corpus = MyCorpus()
```

有了我们标准形式的语料库，我们于是就可以 init 我们的 TFIDFmodel 了。

这里做的事情，就是把已经变成 BoW 向量的数组，做一次 TFIDF 的计算。详情参见课件。


```python
from gensim.models.tfidfmodel import TfidfModel
tfidf = TfidfModel(corpus)
```

好，这下我们看看一个普通的句子放过来长什么样子：


```python
tfidf[dictionary.doc2bow(list(tokenize('hello world, good morning', errors='ignore')))]
```

```
[(985, 0.2947139124944075),
 (3430, 0.28760732706613895),
 (33767, 0.6587176730120703),
 (35249, 0.6296957697663794)]
```

我们做 tf-idf ，实际上这个 vector 的长度是整个 dict 的长度，上面之所以显示了 4 个，是因为大部分是 0 。

怎么判断两个句子的相似度呢？

这里有个trick，因为我们得到的 tfidf 只是『有这个字，就有这个值』，并不是一个全部值。

也就是说，两个 matrix 可能 size 是完全不一样的。

想用 cosine 计算的同学就会问了，两个 matrix 的 size 都不 fix，怎么办？

咦，这里就注意咯。他们的 size 其实是一样的。只是把全部是 0 的那部分给省略了。

于是，我们只要拿其中一个作为 index。扩展开全部的 matrixsize，另一个带入，就可以计算了。


```python
from gensim.similarities import MatrixSimilarity

# 先把刚刚那句话包装成一个方法
def to_tfidf(text):
    res = tfidf[dictionary.doc2bow(list(tokenize(text, errors='ignore')))]
    return res

# 然后，我们创造一个cosine similarity的比较方法
def cos_sim(text1, text2):
    tfidf1 = to_tfidf(text1)
    tfidf2 = to_tfidf(text2)
    index = MatrixSimilarity([tfidf1],num_features=len(dictionary))
    sim = index[tfidf2]
    # 本来 sim 输出是一个 array，我们不需要一个 array 来表示，
    # 所以我们直接 cast 成一个float
    return float(sim[0])
```

来，我们可以做个测试：


```python
text1 = 'hello world'
text2 = 'hello from the other side'
cos_sim(text1, text2)
```

```
0.8566456437110901
```



好，万事俱备，我们现在就只需要把我们的column都转化成tfidf计算出来的相似度了：

因为 sim 的结果是一个 np array，所以我们用'[0]'取里面那个唯一值就行了


```python
df_all['tfidf_cos_sim_in_title'] = df_all.apply(lambda x: cos_sim(x['search_term'], x['product_title']), axis=1)
```

我们来看看长什么样子：


```python
df_all['tfidf_cos_sim_in_title'][:5]
```


```
0    0.274539
1    0.000000
2    0.000000
3    0.133577
4    0.397320
Name: tfidf_cos_sim_in_title, dtype: float64
```



同理，


```python
df_all['tfidf_cos_sim_in_desc'] = df_all.apply(lambda x: cos_sim(x['search_term'], x['product_description']), axis=1)
```

至此，我们又有了两个高质量的 features。

#### Word2Vec

最后，我们的大杀器 w2v 登场了。

最好的情况呢，其实是你在另一个地方用非常大非常完备的语料库做好 w2v 的 training，再跑到这个任务上来直接输出 vector。具体可以参照官网上谷歌新闻语料的一个大大的 w2v 模型。可下载，以后用。<span style="color:red;">嗯。其实这个就是迁移学习的方法。中文有这方面的语料库吗？</span>

我们这里，用个轻量级的解决方案，那就是直接在我们的文本上学习。（跟 tfidf 玩法差不多）

然而，w2v 和 tfidf 有个很大的不同。对于 tfidf 而言，只需要知道一整段 text 中包含了哪些 word 元素就行了。其他都不用 care。

但是w2v要考虑到句子层级的split，以及语境前后的考虑。

所以，刚刚 tfidf 的 corpus 不能直接被这里使用。在这里，我们需要把句子/文字都给分类好。

这里，我们再玩儿一个NLP的杀器：NLTK。


```python
import nltk
# nltk也是自带一个强大的句子分割器。
tokenizer = nltk.data.load('tokenizers/punkt/english.pickle')
```

这个 punkt 就可以把句子分隔开，也叫做 tokenizer。<span style="color:red;">嗯，不错。</span>

我们看看效果


```python
tokenizer.tokenize(df_all['all_texts'].values[0])
```

```
['simpson strong-ti 12-gaug angl .',
 'not onli do angl make joint stronger, they also provid more consistent, straight corners.',
 'simpson strong-ti offer a wide varieti of angl in various size and thick to handl light-duti job or project where a structur connect is needed.',
 'some can be bent (skewed) to match the project.',
 'for outdoor project or those where moistur is present, use our zmax zinc-coat connectors, which provid extra resist against corros (look for a "z" at the end of the model number).versatil connector for various 90 connect and home repair projectsstrong than angl nail or screw fasten alonehelp ensur joint are consist straight and strongdimensions: 3 in.',
 'x 3 in.',
 'x 1-1/2 in.mad from 12-gaug steelgalvan for extra corros resistanceinstal with 10d common nail or #9 x 1-1/2 in.',
 'strong-driv sd screw . ']
```



依照这个方法，我们先把长文本搞成 list of 句子，再把句子变成 list of 单词：


```python
sentences = [tokenizer.tokenize(x) for x in df_all['all_texts'].values]
```

其实这些 sentences 不需要这些层级关系，他们都是平级的，所以：

我们把 list of lists 给 flatten 了。


```python
sentences = [y for x in sentences for y in x]
```

这下我们可以看到，一共1998321个句子。


```python
len(sentences)
```

```
1998321
```

接着，我们把句子里的单词给分好。这事儿我们还是可以用刚刚 Gensim的 tokenizer

这里我们用 nltk 的 word_tokenizer 来试试


```python
from nltk.tokenize import word_tokenize
w2v_corpus = [word_tokenize(x) for x in sentences]
```

注：这个地方如果是中文，那么使用 jieba 来分词。

好，这下我们训练 model

```python
from gensim.models.word2vec import Word2Vec

model = Word2Vec(w2v_corpus, size=128, window=5, min_count=5, workers=4)
```

这时候，每个单词都可以像查找字典一样，读出他们的 w2v 坐标了：


```python
model['right']
```




```
array([ 4.74717617,  2.64686418,  0.35502195,  0.80881947, -2.7076602 ,
       -1.64652073, -2.27783561, -0.92420739, -0.14867701,  5.74324369,
       -0.7946381 ,  4.2346034 ,  0.85286433,  2.52050495, -0.73137748,
        1.9509604 , -1.75785506, -0.81137753,  1.03638244, -1.19456983,
       -0.23727451, -2.1338408 , -0.28541893, -2.99906659, -3.9443922 ,
        3.45797253, -1.05518758,  2.46135116, -2.20839262, -1.80382502,
        4.74548674,  1.45839429,  1.89756656, -0.03712106,  1.1392957 ,
       -0.28602245, -0.8830694 , -0.23989263,  1.52211082, -3.42239022,
        0.01539903,  0.87455851,  0.25244436, -1.04864109, -2.5990386 ,
       -0.72978145,  3.70196033,  1.23104656,  1.96894944,  0.28224495,
       -2.56046462, -1.574512  , -2.20811892,  2.57060266, -2.39487839,
       -2.57700634,  2.41890597,  0.75820315, -2.01431084, -2.13502717,
       -0.1664609 , -0.10223585, -0.93037611,  4.34696054, -0.88387358,
       -4.04143572, -0.76093572, -4.64342451,  5.51188993,  1.15895557,
        0.04778517, -2.19732451,  2.58349681,  0.30812854, -0.05851545,
        0.14742707,  2.65333915, -3.49614239, -0.04863766, -2.16823196,
       -0.75142419,  4.91761446, -0.76077127, -1.32876885,  2.45301771,
       -1.47275043,  0.42842156,  0.99622703, -0.10033795,  1.29662645,
       -2.37165427, -1.56022704, -2.82586884, -0.85700899,  2.06937575,
        1.59914327,  1.34667575,  3.50663733, -0.44751605,  2.495821  ,
       -0.80405504,  1.46751213, -2.97747278,  1.59027314,  1.04117584,
       -3.76480174,  2.03447819,  2.54762697,  0.32881331, -3.20821214,
        2.40063143, -0.48981774, -0.83241153,  1.22963691,  1.81204796,
       -0.20612268, -0.78395975, -0.48303336, -1.06597555, -0.19221658,
       -0.07359049,  2.20414066,  1.43124104, -0.65005296, -1.06764543,
       -0.60594636,  1.24271679, -3.92345881], dtype=float32)
```



跟 TFiDF 一样，我们可以把 textual 的 column 都转化为 w2v 坐标

这里不一样的是，TFiDF 是针对每个句子都可以有的，而 w2v 是针对每个单词的。<span style="color:red;">嗯，他们的层级不一样。</span>

所以我们要有个综合考虑的 idea：

平均化一个句子的 w2v 向量，算作整个 text 的平均 vector：平均化是一个弱化的处理方式，会丢失很多的信息量，还可以把句子里面的word2vec 一字排开，排成一个矩阵，不满的用 0 补上，然后就可以计算着两个矩阵的相似度。

我们写一个方法来实现：


```python
# 先拿到全部的vocabulary
vocab = model.vocab

# 得到任意text的vector
def get_vector(text):
    # 建立一个全是0的array
    res =np.zeros([128])
    count = 0
    for word in word_tokenize(text):
        if word in vocab:
            res += model[word]
            count += 1
    return res/count
```

我们可以试试：


```python
print(get_vector('life is like a box of chocolate'))
```

```
[ 0.463416    0.97560888  0.10863534 -0.82696981  1.41277812  0.79170082
  0.0978372  -0.68150822  0.99790255 -0.44159976  1.00841724 -0.43905148
  0.21946578  1.01400004 -1.97788476 -0.24557593 -1.13167921 -0.22579467
 -0.48376473  0.41615273  0.67180802  0.53008292  0.52145608 -1.9788011
  1.42492926 -0.18045649 -0.65986957 -0.93241019  1.1279408   0.06740171
  0.40830285  0.79715398 -0.84070924  0.35409186  0.29476608 -0.01593437
 -0.2836557  -0.27918024  1.09351952 -0.34930461  2.4379538   0.60693575
 -0.42956293  1.8092562  -0.43648594 -0.58754889 -1.25327353 -0.65632194
  1.29220566  0.52857347  0.08685846  0.04752858 -0.59915119 -0.00281862
 -0.99020389 -0.82765476  0.16989418  1.01381083 -0.0428756   0.98613351
 -1.3658159  -1.43513804 -0.75343464 -0.09422477  0.07967508 -0.87333807
  0.36081878 -0.81233797  0.2529      1.28962335  1.31695796 -1.45198588
  1.3620293   0.11232918 -0.21050634 -0.27940779 -0.13681145 -1.03338094
  0.07509582  0.46833945  0.00504241  1.29592314  0.38266217  1.22406197
  0.49212121 -0.04775342 -0.78706613  0.15082838 -1.38598437  0.85031726
 -1.25327267 -0.74126085  0.19504826 -1.05701404 -0.18221375 -1.85480102
  0.10107571  1.40432313 -1.16039764 -0.56544506  0.47745609  0.47314491
 -0.52448719 -0.36235778  1.88896974 -0.16886154 -0.01455577 -1.06299005
  0.18107549 -0.74452799 -2.65968527  0.5563812  -0.17996723  0.65088516
 -0.25565534 -1.15591651  0.46034975 -1.45567893 -0.11601611 -0.49641909
  1.75690769 -0.28630929 -1.34841273  1.24394214  0.26161586 -0.05742724
 -0.83873362 -0.8819646 ]
```


好，同理，我们需要计算两个 text 的平均 w2v 的 cosine similarity


```python
from scipy import spatial
# 这里，我们再玩儿个新的方法，用scipy的spatial

def w2v_cos_sim(text1, text2):
    try:
        w2v1 = get_vector(text1)
        w2v2 = get_vector(text2)
        sim = 1 - spatial.distance.cosine(w2v1, w2v2)
        return float(sim)
    except:
        return float(0)
# 这里加个try exception，以防我们得到的vector是个[0,0,0,...]
```

报错可能是因为除以 0，也就是整个句子都是 0，那么输出 0 就行，即两个句子毫无相似度。

```python
w2v_cos_sim('hello world', 'hello from the other side')
```




```
0.2397940355621142
```


跟刚刚TFIDF一样，我们计算一下 search term 在 product title 和 product description 中的 cosine similarity


```python
df_all['w2v_cos_sim_in_title'] = df_all.apply(lambda x: w2v_cos_sim(x['search_term'], x['product_title']), axis=1)
df_all['w2v_cos_sim_in_desc'] = df_all.apply(lambda x: w2v_cos_sim(x['search_term'], x['product_description']), axis=1)
```


又多了两个优质的特征。


```python
df_all.head(5)
```




|      | id   | product_title                                     | product_uid | relevance | search_term        | product_description                               | dist_in_title | dist_in_desc | all_texts                                         | tfidf_cos_sim_in_title | tfidf_cos_sim_in_desc | w2v_cos_sim_in_title | w2v_cos_sim_in_desc |
| ---- | ---- | ------------------------------------------------- | ----------- | --------- | ------------------ | ------------------------------------------------- | ------------- | ------------ | ------------------------------------------------- | ---------------------- | --------------------- | -------------------- | ------------------- |
| 0    | 2    | simpson strong-ti 12-gaug angl                    | 100001      | 3.00      | angl bracket       | not onli do angl make joint stronger, they als... | 0.190476      | 0.030418     | simpson strong-ti 12-gaug angl . not onli do a... | 0.274539               | 0.182836              | 0.482381             | 0.425223            |
| 1    | 3    | simpson strong-ti 12-gaug angl                    | 100001      | 2.50      | l bracket          | not onli do angl make joint stronger, they als... | 0.153846      | 0.022901     | simpson strong-ti 12-gaug angl . not onli do a... | 0.000000               | 0.000000              | 0.322772             | 0.110944            |
| 2    | 9    | behr premium textur deckov 1-gal. #sc-141 tugb... | 100002      | 3.00      | deck over          | behr premium textur deckov is an innov solid c... | 0.175000      | 0.017875     | behr premium textur deckov 1-gal. #sc-141 tugb... | 0.000000               | 0.053455              | 0.307271             | 0.445303            |
| 3    | 16   | delta vero 1-handl shower onli faucet trim kit... | 100005      | 2.33      | rain shower head   | updat your bathroom with the delta vero single... | 0.326087      | 0.048632     | delta vero 1-handl shower onli faucet trim kit... | 0.133577               | 0.043712              | 0.532707             | 0.465209            |
| 4    | 17   | delta vero 1-handl shower onli faucet trim kit... | 100005      | 2.67      | shower onli faucet | updat your bathroom with the delta vero single... | 0.382979      | 0.054545     | delta vero 1-handl shower onli faucet trim kit... | 0.397320               | 0.098485              | 0.726722             | 0.486046            |




搞完之后，我们把不能被『机器学习模型』处理的 column 给 drop 掉


```python
df_all = df_all.drop(['search_term','product_title','product_description','all_texts'],axis=1)
```

## Step 4: 重塑训练/测试集

舒淇说得好，要把之前脱下的衣服再一件件穿回来

数据处理也是如此，搞完一圈预处理之后，我们让数据重回原本的样貌

#### 分开训练和测试集


```python
df_train = df_all.loc[df_train.index]
df_test = df_all.loc[df_test.index]
```

#### 记录下测试集的id

留着上传的时候 能对的上号


```python
test_ids = df_test['id']
```

#### 分离出y_train


```python
y_train = df_train['relevance'].values
```

#### 把原集中的label给删去

否则就是cheating了


```python
X_train = df_train.drop(['id','relevance'],axis=1).values
X_test = df_test.drop(['id','relevance'],axis=1).values
```

## Step 5: 建立模型

我们用个最简单的模型：


```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import cross_val_score
```

用CV结果保证公正客观性；并调试不同的alpha值


```python
params = [1,3,5,6,7,8,9,10]
test_scores = []
for param in params:
    clf = RandomForestRegressor(n_estimators=30, max_depth=param)
    test_score = np.sqrt(-cross_val_score(clf, X_train, y_train, cv=5, scoring='neg_mean_squared_error'))
    test_scores.append(np.mean(test_score))
```

画个图来看看：


```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(params, test_scores)
plt.title("Param vs CV Error");
```


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180724/5JGK3eIKi1.png?imageslim)

大概 9 的时候达到了最优解。直观的来看 可能跟前文的差别不大。

但是同学们可以试试其他不同的算法，就可以对比出差距了。

<span style="color:red;">嗯，可以试试一些 ensemble </span>

## Step 6: 上传结果

用我们测试出的最优解建立模型，并跑跑测试集


```python
rf = RandomForestRegressor(n_estimators=30, max_depth=6)
```


```python
rf.fit(X_train, y_train)
```




```
RandomForestRegressor(bootstrap=True, criterion='mse', max_depth=6,
           max_features='auto', max_leaf_nodes=None,
           min_impurity_split=1e-07, min_samples_leaf=1,
           min_samples_split=2, min_weight_fraction_leaf=0.0,
           n_estimators=30, n_jobs=1, oob_score=False, random_state=None,
           verbose=0, warm_start=False)
```




```python
y_pred = rf.predict(X_test)
```

把拿到的结果，放进PD，做成CSV上传：


```python
pd.DataFrame({"id": test_ids, "relevance": y_pred}).to_csv('submission.csv',index=False)
```

## 总结：

这一篇教程中，用了稍微复杂的方法教大家加入更多高质量的features。

同学们可以尝试修改/调试/升级的部分是：

2. **更多的特征**: Deep Learning 的大重点就是黑盒机制。所以，其实我们刚刚得到的 tfidf 或者 w2v vector 可以就不做任何处理塞进我们的 X 里面去。然后我们使用更加深度的学习模型来训练它。这样，我们的算法可以“看见”更加原始的信息。假设他们比人脑好的话，他们就可以作出更好的 predictions。<span style="color:red;">第六课会讲</span>

3. **更好的回归模型**: 根据之前的课讲的Ensemble方法，把分类器提升到极致。


在 kaggle 上大部分展示出来的方法都是非黑盒的，因为要展示自己牛B，脑洞大，想出很多的牛逼的特征。但是论效果 DL 会更好一些。
