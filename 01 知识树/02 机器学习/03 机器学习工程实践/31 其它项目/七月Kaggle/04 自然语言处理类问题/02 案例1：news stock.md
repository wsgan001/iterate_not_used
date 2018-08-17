这个是第二讲的案例，老师放在这里讲了。


项目的说明和数据下载：

https://www.kaggle.com/aaron7sun/stocknews/home

通过每日新闻判断股市涨跌，这个东西是完全不靠谱的。
但是可以作为一个自然语言处理的训练。

在 Redgdit 上，有个每日新闻的频道，每天都会有人 vote 今天最关注的新闻是什么。这样就可以免费得到大家认为的免费新闻。

然后就提取了历史 8 年的每天的前 25 条新闻。

对应的是道琼斯工业指数，直接在 yahoo 上可以得到。

工业指数，之分成了两类：涨或者不涨。


OK，我们先用最简单的方法来处理这类文本分类问题。

# 用每日新闻预测金融市场变化（标准版）

TF-IDF + SVM 是文本分类问题的基准线。<span style="color:red;">嗯。</span>

这篇教程我直接用最简单直接的方式处理。

高级版本的教程会在日后的课程中放出。


```python
from sklearn.feature_extraction.text import CountVectorizer,TfidfVectorizer
import pandas as pd
import numpy as np
from sklearn.svm import SVC
from sklearn.metrics import roc_auc_score
from datetime import date
```

### 监视数据

我们先读入数据。这里我提供了一个已经combine好了的数据。


```python
data = pd.read_csv('../input/Combined_News_DJIA.csv')
```

这时候，我们可以看一下数据长什么样子。<span style="color:red;">嗯，第一步一定是做这个。看一下数据</span>


```python
data.head()
```





|      | Date       | Label | Top1                                              | Top2                                              | Top3                                              | Top4                                              | Top5                                              | Top6                                              | Top7                                              | Top8                                              | ...  | Top16                                             | Top17                                             | Top18                                             | Top19                                             | Top20                                             | Top21                                             | Top22                                             | Top23                                             | Top24                                             | Top25                                               |
| ---- | ---------- | ----- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ---- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------- |
| 0    | 2008-08-08 | 0     | b"Georgia 'downs two Russian warplanes' as cou... | b'BREAKING: Musharraf to be impeached.'           | b'Russia Today: Columns of troops roll into So... | b'Russian tanks are moving towards the capital... | b"Afghan children raped with 'impunity,' U.N. ... | b'150 Russian tanks have entered South Ossetia... | b"Breaking: Georgia invades South Ossetia, Rus... | b"The 'enemy combatent' trials are nothing but... | ...  | b'Georgia Invades South Ossetia - if Russia ge... | b'Al-Qaeda Faces Islamist Backlash'               | b'Condoleezza Rice: "The US would not act to p... | b'This is a busy day: The European Union has ...  | b"Georgia will withdraw 1,000 soldiers from Ir... | b'Why the Pentagon Thinks Attacking Iran is a ... | b'Caucasus in crisis: Georgia invades South Os... | b'Indian shoe manufactory - And again in a se...  | b'Visitors Suffering from Mental Illnesses Ban... | b"No Help for Mexico's Kidnapping Surge"            |
| 1    | 2008-08-11 | 1     | b'Why wont America and Nato help us? If they w... | b'Bush puts foot down on Georgian conflict'       | b"Jewish Georgian minister: Thanks to Israeli ... | b'Georgian army flees in disarray as Russians ... | b"Olympic opening ceremony fireworks 'faked'"     | b'What were the Mossad with fraudulent New Zea... | b'Russia angered by Israeli military sale to G... | b'An American citizen living in S.Ossetia blam... | ...  | b'Israel and the US behind the Georgian aggres... | b'"Do not believe TV, neither Russian nor Geor... | b'Riots are still going on in Montreal (Canada... | b'China to overtake US as largest manufacturer'   | b'War in South Ossetia [PICS]'                    | b'Israeli Physicians Group Condemns State Tort... | b' Russia has just beaten the United States ov... | b'Perhaps *the* question about the Georgia - R... | b'Russia is so much better at war'                | b"So this is what it's come to: trading sex fo...   |
| 2    | 2008-08-12 | 0     | b'Remember that adorable 9-year-old who sang a... | b"Russia 'ends Georgia operation'"                | b'"If we had no sexual harassment we would hav... | b"Al-Qa'eda is losing support in Iraq because ... | b'Ceasefire in Georgia: Putin Outmaneuvers the... | b'Why Microsoft and Intel tried to kill the XO... | b'Stratfor: The Russo-Georgian War and the Bal... | b"I'm Trying to Get a Sense of This Whole Geor... | ...  | b'U.S. troops still in Georgia (did you know t... | b'Why Russias response to Georgia was right'      | b'Gorbachev accuses U.S. of making a "serious ... | b'Russia, Georgia, and NATO: Cold War Two'        | b'Remember that adorable 62-year-old who led y... | b'War in Georgia: The Israeli connection'         | b'All signs point to the US encouraging Georgi... | b'Christopher King argues that the US and NATO... | b'America: The New Mexico?'                       | b"BBC NEWS \| Asia-Pacific \| Extinction 'by man... |
| 3    | 2008-08-13 | 0     | b' U.S. refuses Israel weapons to attack Iran:... | b"When the president ordered to attack Tskhinv... | b' Israel clears troops who killed Reuters cam... | b'Britain\'s policy of being tough on drugs is... | b'Body of 14 year old found in trunk; Latest (... | b'China has moved 10 *million* quake survivors... | b"Bush announces Operation Get All Up In Russi... | b'Russian forces sink Georgian ships '            | ...  | b'Elephants extinct by 2020?'                     | b'US humanitarian missions soon in Georgia - i... | b"Georgia's DDOS came from US sources"            | b'Russian convoy heads into Georgia, violating... | b'Israeli defence minister: US against strike ... | b'Gorbachev: We Had No Choice'                    | b'Witness: Russian forces head towards Tbilisi... | b' Quarter of Russians blame U.S. for conflict... | b'Georgian president says US military will ta...  | b'2006: Nobel laureate Aleksander Solzhenitsyn...   |
| 4    | 2008-08-14 | 1     | b'All the experts admit that we should legalis... | b'War in South Osetia - 89 pictures made by a ... | b'Swedish wrestler Ara Abrahamian throws away ... | b'Russia exaggerated the death toll in South O... | b'Missile That Killed 9 Inside Pakistan May Ha... | b"Rushdie Condemns Random House's Refusal to P... | b'Poland and US agree to missle defense deal. ... | b'Will the Russians conquer Tblisi? Bet on it,... | ...  | b'Bank analyst forecast Georgian crisis 2 days... | b"Georgia confict could set back Russia's US r... | b'War in the Caucasus is as much the product o... | b'"Non-media" photos of South Ossetia/Georgia ... | b'Georgian TV reporter shot by Russian sniper ... | b'Saudi Arabia: Mother moves to block child ma... | b'Taliban wages war on humanitarian aid workers'  | b'Russia: World "can forget about" Georgia\'s...  | b'Darfur rebels accuse Sudan of mounting major... | b'Philippines : Peace Advocate say Muslims nee...   |



其实看起来特别的简单直观。如果是1，那么当日的DJIA就提高或者不变了。如果是1，那么DJIA那天就是跌了。

接下来我们把每一天的 25 个 headlines 先合并起来。因为我们显然是需要考虑这一天的所有的 news 的。


```python
data["combined_news"] = data.filter(regex=("Top.*")).apply(lambda x: ''.join(str(x.values)), axis=1)
```

把Top 开头的列都取出来，然后通过这个 lambda 表达式合并起来。通过这一步就得到了一个新的列 combined_news。


### 分割测试/训练集

这下，我们可以把数据给分成 Training/Testing data


```python
train = data[data['Date'] < '2015-01-01']
test = data[data['Date'] > '2014-12-31']
```

<span style="color:red;">这样的切分效果不是很好吧？</span>

通过日期进行分割


### 提取features

也就是文本中的特征。这里一定要注意，fit你的model的时候，要用training set，不能一股脑的把所有的数据都放进来。（当然，现实中你可以这么做）因为我们要假设testing set我们在训练的时候是完全接触不到的，是不可知的。

这里没有进行文本预处理 preprocessing ，是一个简单版教程，直接把我们的 feature 通过 tf-idf 这个方法提取出来。

```python
feature_extraction = TfidfVectorizer()
X_train = feature_extraction.fit_transform(train["combined_news"].values)
```

fit_transform 是两个步骤，fit 是把这里面的文本信息训练一遍，并且把它 transform 成我们所需要的 tf-idf 数字表达式。注意：fit_transform 一定只能用在训练集上。

fit_transform 完成之后，feature_extraction 就记住了我们在训练集上训练的 tf-idf 模型的样子。

所以*X_train* 搞完以后，直接给 *X_test* 做个Transform


```python
X_test = feature_extraction.transform(test["combined_news"].values)
```

这样，就直接把它用在了测试集上，测试集是不会经过训练的，而是通过我训练集的fit之后的模型直接把测试集 transform 成他应该有的表达形式。也就是变成我们的 X_test。


同理，y就是我们已经准备好的label


```python
y_train = train["Label"].values
y_test = test["Label"].values
```

.values 的意思就是把它变成一个 numpy 。

这时候，我们的 X_train X_test  y_train y_test 都已经齐全了。

### 训练模型


```python
clf = SVC(probability=True, kernel='rbf')
```

SVC 就是 svm 的分类版。<span style="color:red;">好吧，才知道。</span>

把你的*X_train*和*y_train*给fit进去


```python
clf.fit(X_train, y_train)
```


```
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape=None, degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=True, random_state=None, shrinking=True,
  tol=0.001, verbose=False)
```



### 预测


```python
predictions = clf.predict_proba(X_test)
```

为什么要用 predict_proba 呢？因为在0-1 分类的问题中，如果你的参数调整的不好，没用sem 的话，可能输出的全是1 ，也可能全是0，因为，加入说你的样本不是均衡的 1非常多，那么我全输出1 结果就已经很好了。<span style="color:red;">这个地方使用 predict_proba 的原因还是美很清楚，有几句话没听清，尤其是 “没用 sem 的话” 不知道说的是不是 sem</span>

### 验证准确度

按照我之前给的要求，用AUC作为binary classification的Metrics


```python
print('ROC-AUC yields ' + str(roc_auc_score(y_test, predictions[:,1])))
```

```
ROC-AUC yields 0.574260752688
```


可见，我们用最直接简单的方法。什么预处理都没有做，直接把 tf-idf 取出来，然后就计算了。

OK，我们看看添加了文本预处理之后的效果：

## 进阶版

### 文本预处理

我们这样直接把文本放进TF-IDF，虽然简单方便，但是还是不够严谨的。我们可以把原文本做进一步的处理。

- 首先，我们把它小写化 / 分成小tokens，然后去除一些符号。


```python
X_train = train["combined_news"].str.lower().str.replace('"', '').str.replace("'", '').str.split()
X_test = test["combined_news"].str.lower().str.replace('"', '').str.replace("'", '').str.split()
print(X_test[1611])
```

```
['[', 'most', 'cases', 'of', 'cancer', 'are', 'the', 'result', 'of', 'sheer', 'bad', 'luck', 'rather', 'than', 'unhealthy', 'lifestyles,', 'diet', 'or', 'even', 'inherited', 'genes,', 'new', 'research', 'suggests.', 'random', 'mutations', 'that', 'occur', 'in', 'dna', 'when', 'cells', 'divide', 'are', 'responsible', 'for', 'two', 'thirds', 'of', 'adult', 'cancers', 'across', 'a', 'wide', 'range', 'of', 'tissues.', 'iran', 'dismissed', 'united', 'states', 'efforts', 'to', 'fight', 'islamic', 'state', 'as', 'a', 'ploy', 'to', 'advance', 'u.s.', 'policies', 'in', 'the', 'region:', 'the', 'reality', 'is', 'that', 'the', 'united', 'states', 'is', 'not', 'acting', 'to', 'eliminate', 'daesh.', 'they', 'are', 'not', 'even', 'interested', 'in', 'weakening', 'daesh,', 'they', 'are', 'only', 'interested', 'in', 'managing', 'it', 'poll:', 'one', 'in', '8', 'germans', 'would', 'join', 'anti-muslim', 'marches', 'uk', 'royal', 'familys', 'prince', 'andrew', 'named', 'in', 'us', 'lawsuit', 'over', 'underage', 'sex', 'allegations', 'some', '40', 'asylum-seekers', 'refused', 'to', 'leave', 'the', 'bus', 'when', 'they', 'arrived', 'at', 'their', 'destination', 'in', 'rural', 'northern', 'sweden,', 'demanding', 'that', 'they', 'be', 'taken', 'back', 'to', 'malm', 'or', 'some', 'big', 'city.', 'pakistani', 'boat', 'blows', 'self', 'up', 'after', 'india', 'navy', 'chase.', 'all', 'four', 'people', 'on', 'board', 'the', 'vessel', 'from', 'near', 'the', 'pakistani', 'port', 'city', 'of', 'karachi', 'are', 'believed', 'to', 'have', 'been', 'killed', 'in', 'the', 'dramatic', 'episode', 'in', 'the', 'arabian', 'sea', 'on', 'new', 'years', 'eve,', 'according', 'to', 'indias', 'defence', 'ministry.', 'sweden', 'hit', 'by', 'third', 'mosque', 'arson', 'attack', 'in', 'a', 'week', '940', 'cars', 'set', 'alight', 'during', 'french', 'new', 'year', 'salaries', 'for', 'top', 'ceos', 'rose', 'twice', 'as', 'fast', 'as', 'average', 'canadian', 'since', 'recession:', 'study', 'norway', 'violated', 'equal-pay', 'law,', 'judge', 'says:', 'judge', 'finds', 'consulate', 'employee', 'was', 'unjustly', 'paid', '$30,000', 'less', 'than', 'her', 'male', 'counterpart', 'imam', 'wants', 'radical', 'recruiters', 'of', 'muslim', 'youth', 'in', 'canada', 'identified', 'and', 'dealt', 'with', 'saudi', 'arabia', 'beheaded', '83', 'people', 'in', '2014,', 'the', 'most', 'in', 'years', 'a', 'living', 'hell', 'for', 'slaves', 'on', 'remote', 'south', 'korean', 'islands', '-', 'slavery', 'thrives', 'on', 'this', 'chain', 'of', 'rural', 'islands', 'off', 'south', 'koreas', 'rugged', 'southwest', 'coast,', 'nurtured', 'by', 'a', 'long', 'history', 'of', 'exploitation', 'and', 'the', 'demands', 'of', 'trying', 'to', 'squeeze', 'a', 'living', 'from', 'the', 'sea.', 'worlds', '400', 'richest', 'get', 'richer,', 'adding', '$92bn', 'in', '2014', 'rental', 'car', 'stereos', 'infringe', 'copyright,', 'music', 'rights', 'group', 'says', 'ukrainian', 'minister', 'threatens', 'tv', 'channel', 'with', 'closure', 'for', 'airing', 'russian', 'entertainers', 'palestinian', 'president', 'mahmoud', 'abbas', 'has', 'entered', 'into', 'his', 'most', 'serious', 'confrontation', 'yet', 'with', 'israel', 'by', 'signing', 'onto', 'the', 'international', 'criminal', 'court.', 'his', 'decision', 'on', 'wednesday', 'gives', 'the', 'court', 'jurisdiction', 'over', 'crimes', 'committed', 'in', 'palestinian', 'lands.', 'israeli', 'security', 'center', 'publishes', 'names', 'of', '50', 'killed', 'terrorists', 'concealed', 'by', 'hamas', 'the', 'year', '2014', 'was', 'the', 'deadliest', 'year', 'yet', 'in', 'syrias', 'four-year', 'conflict,', 'with', 'over', '76,000', 'killed', 'a', 'secret', 'underground', 'complex', 'built', 'by', 'the', 'nazis', 'that', 'may', 'have', 'been', 'used', 'for', 'the', 'development', 'of', 'wmds,', 'including', 'a', 'nuclear', 'bomb,', 'has', 'been', 'uncovered', 'in', 'austria.', 'restrictions', 'on', 'web', 'freedom', 'a', 'major', 'global', 'issue', 'in', '2015', 'austrian', 'journalist', 'erich', 'mchel', 'delivered', 'a', 'presentation', 'in', 'hamburg', 'at', 'the', 'annual', 'meeting', 'of', 'the', 'chaos', 'computer', 'club', 'on', 'monday', 'december', '29,', 'detailing', 'the', 'various', 'locations', 'where', 'the', 'us', 'nsa', 'has', 'been', 'actively', 'collecting', 'and', 'processing', 'electronic', 'intelligence', 'in', 'vienna.', 'thousands', 'of', 'ukraine', 'nationalists', 'march', 'in', 'kiev', 'chinas', 'new', 'years', 'resolution:', 'no', 'more', 'harvesting', 'executed', 'prisoners', 'organs', 'authorities', 'pull', 'plug', 'on', 'russias', 'last', 'politically', 'independent', 'tv', 'station]']
```


-  然后，我们删除一些停止词


```python
from nltk.corpus import stopwords
stop = stopwords.words('english')
```

- 删除数字


```python
import re
def hasNumbers(inputString):
    return bool(re.search(r'\d', inputString))
```

- lemma 把所有的词型都归一成一种表达


```python
from nltk.stem import WordNetLemmatizer
wordnet_lemmatizer = WordNetLemmatizer()
```

我们把这些处理过程合成一个 function:


```python
def check(word):
    """
    如果需要这个单词，则True
    如果应该去除，则False
    """
    if word in stop:
        return False
    elif hasNumbers(word):
        return False
    else:
        return True
```

然后我们把整个流程放进我们的 DF 中处理


```python
X_train = X_train.apply(lambda x: [wordnet_lemmatizer.lemmatize(item) for item in x if check(item)])
X_test = X_test.apply(lambda x: [wordnet_lemmatizer.lemmatize(item) for item in x if check(item)])
print(X_test[1611])
```

```
['[', 'case', 'cancer', 'result', 'sheer', 'bad', 'luck', 'rather', 'unhealthy', 'lifestyles,', 'diet', 'even', 'inherited', 'genes,', 'new', 'research', 'suggests.', 'random', 'mutation', 'occur', 'dna', 'cell', 'divide', 'responsible', 'two', 'third', 'adult', 'cancer', 'across', 'wide', 'range', 'tissues.', 'iran', 'dismissed', 'united', 'state', 'effort', 'fight', 'islamic', 'state', 'ploy', 'advance', 'u.s.', 'policy', 'region:', 'reality', 'united', 'state', 'acting', 'eliminate', 'daesh.', 'even', 'interested', 'weakening', 'daesh,', 'interested', 'managing', 'poll:', 'one', 'german', 'would', 'join', 'anti-muslim', 'march', 'uk', 'royal', 'family', 'prince', 'andrew', 'named', 'u', 'lawsuit', 'underage', 'sex', 'allegation', 'asylum-seekers', 'refused', 'leave', 'bus', 'arrived', 'destination', 'rural', 'northern', 'sweden,', 'demanding', 'taken', 'back', 'malm', 'big', 'city.', 'pakistani', 'boat', 'blow', 'self', 'india', 'navy', 'chase.', 'four', 'people', 'board', 'vessel', 'near', 'pakistani', 'port', 'city', 'karachi', 'believed', 'killed', 'dramatic', 'episode', 'arabian', 'sea', 'new', 'year', 'eve,', 'according', 'india', 'defence', 'ministry.', 'sweden', 'hit', 'third', 'mosque', 'arson', 'attack', 'week', 'car', 'set', 'alight', 'french', 'new', 'year', 'salary', 'top', 'ceo', 'rose', 'twice', 'fast', 'average', 'canadian', 'since', 'recession:', 'study', 'norway', 'violated', 'equal-pay', 'law,', 'judge', 'says:', 'judge', 'find', 'consulate', 'employee', 'unjustly', 'paid', 'le', 'male', 'counterpart', 'imam', 'want', 'radical', 'recruiter', 'muslim', 'youth', 'canada', 'identified', 'dealt', 'saudi', 'arabia', 'beheaded', 'people', 'year', 'living', 'hell', 'slave', 'remote', 'south', 'korean', 'island', '-', 'slavery', 'thrives', 'chain', 'rural', 'island', 'south', 'korea', 'rugged', 'southwest', 'coast,', 'nurtured', 'long', 'history', 'exploitation', 'demand', 'trying', 'squeeze', 'living', 'sea.', 'world', 'richest', 'get', 'richer,', 'adding', 'rental', 'car', 'stereo', 'infringe', 'copyright,', 'music', 'right', 'group', 'say', 'ukrainian', 'minister', 'threatens', 'tv', 'channel', 'closure', 'airing', 'russian', 'entertainer', 'palestinian', 'president', 'mahmoud', 'abbas', 'entered', 'serious', 'confrontation', 'yet', 'israel', 'signing', 'onto', 'international', 'criminal', 'court.', 'decision', 'wednesday', 'give', 'court', 'jurisdiction', 'crime', 'committed', 'palestinian', 'lands.', 'israeli', 'security', 'center', 'publishes', 'name', 'killed', 'terrorist', 'concealed', 'hamas', 'year', 'deadliest', 'year', 'yet', 'syria', 'four-year', 'conflict,', 'killed', 'secret', 'underground', 'complex', 'built', 'nazi', 'may', 'used', 'development', 'wmds,', 'including', 'nuclear', 'bomb,', 'uncovered', 'austria.', 'restriction', 'web', 'freedom', 'major', 'global', 'issue', 'austrian', 'journalist', 'erich', 'mchel', 'delivered', 'presentation', 'hamburg', 'annual', 'meeting', 'chaos', 'computer', 'club', 'monday', 'december', 'detailing', 'various', 'location', 'u', 'nsa', 'actively', 'collecting', 'processing', 'electronic', 'intelligence', 'vienna.', 'thousand', 'ukraine', 'nationalist', 'march', 'kiev', 'china', 'new', 'year', 'resolution:', 'harvesting', 'executed', 'prisoner', 'organ', 'authority', 'pull', 'plug', 'russia', 'last', 'politically', 'independent', 'tv', 'station]']
```

到这里，文本预处理就结束了。

因为外部库，比如sklearn 只支持string输入，所以我们把调整后的 list 再变回string。<span style="color:red;">嗯。</span>


```python
X_train = X_train.apply(lambda x: ' '.join(x))
X_test = X_test.apply(lambda x: ' '.join(x))
print(X_test[1611])
```

```
[ case cancer result sheer bad luck rather unhealthy lifestyles, diet even inherited genes, new research suggests. random mutation occur dna cell divide responsible two third adult cancer across wide range tissues. iran dismissed united state effort fight islamic state ploy advance u.s. policy region: reality united state acting eliminate daesh. even interested weakening daesh, interested managing poll: one german would join anti-muslim march uk royal family prince andrew named u lawsuit underage sex allegation asylum-seekers refused leave bus arrived destination rural northern sweden, demanding taken back malm big city. pakistani boat blow self india navy chase. four people board vessel near pakistani port city karachi believed killed dramatic episode arabian sea new year eve, according india defence ministry. sweden hit third mosque arson attack week car set alight french new year salary top ceo rose twice fast average canadian since recession: study norway violated equal-pay law, judge says: judge find consulate employee unjustly paid le male counterpart imam want radical recruiter muslim youth canada identified dealt saudi arabia beheaded people year living hell slave remote south korean island - slavery thrives chain rural island south korea rugged southwest coast, nurtured long history exploitation demand trying squeeze living sea. world richest get richer, adding rental car stereo infringe copyright, music right group say ukrainian minister threatens tv channel closure airing russian entertainer palestinian president mahmoud abbas entered serious confrontation yet israel signing onto international criminal court. decision wednesday give court jurisdiction crime committed palestinian lands. israeli security center publishes name killed terrorist concealed hamas year deadliest year yet syria four-year conflict, killed secret underground complex built nazi may used development wmds, including nuclear bomb, uncovered austria. restriction web freedom major global issue austrian journalist erich mchel delivered presentation hamburg annual meeting chaos computer club monday december detailing various location u nsa actively collecting processing electronic intelligence vienna. thousand ukraine nationalist march kiev china new year resolution: harvesting executed prisoner organ authority pull plug russia last politically independent tv station]
```


重新Fit一遍我们的clf


```python
feature_extraction = TfidfVectorizer(lowercase=False)
X_train = feature_extraction.fit_transform(X_train.values)
X_test = feature_extraction.transform(X_test.values)
```

再跑一遍


```python
clf = SVC(probability=True, kernel='rbf')
clf.fit(X_train, y_train)
predictions = clf.predict_proba(X_test)
print('ROC-AUC yields ' + str(roc_auc_score(y_test, predictions[:,1])))
```

```
ROC-AUC yields 0.539566532258
```


不小心发现，折腾一圈以后，这个结果还不如之前的简单版。是的。<span style="color:red;">而且，感觉文本预处理的过程对于这个问题来说没有什么实在意义。而且也说明了这个问题其实结果随机性很大。</span>

<span style="color:red;">当然，这里面的模型没有进行调参，都是使用的默认的参数来跑的，也会有些偏差。调参可能会带来非常明显的效果。 grid-search 其实里面就是 loop 的。</span>



下面我们分析一下造成如此的原因有几种：

- 数据点太少

8年*365 2500  对于我们的 NLP 来说，几千条数据的数据量本身是量很少的。因为对于 NLP 来说，单词特征是非常多的，

在大量的数据下，标准的文本预处理流程还是需要的，以提高机器学习的准确度。

- One-Off result

我们到现在都只是跑了一次而已。如果我们像前面的例子一样，用 Cross Validation 来使用 z-folder 玩这组数据，说不定我们会发现，分数高的 clf 其实是 overfitted 了的。<span style="color:red;">好吧。</span>

所以，我们在做kaggle竞赛的时候，最好是要给自己的 clf 做好 Cross Validation 验证。<span style="color:red;">嗯，因为 overfitting 是没准的。</span>



OK，这个案例就讲完了。整个过程还是很清晰的，不过不是太完整，调参的过程没有讲，而且只是用了 tf-idf 没有使用个word2vec 什么的。

## 数据下载

链接：https://pan.baidu.com/s/1l3Af9POz1i3HLQiyr9FHnw 密码：7tl2

## REF
