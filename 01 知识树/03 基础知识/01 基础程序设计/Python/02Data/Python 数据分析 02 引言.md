[TOC]

# 主要内容

主要是介绍高效处理数据的Python工具。

在处理数据的过程中，主要有这几种任务：

- 与各种格式的数据来源进行交互。读写各种各样的文件格式和数据库。
- 准备数据。对数据进行清理、修整、整合、规范化、重塑、切片切块、变形等处理以便进行 分析。
- 转换数据。对数据集做一些数学和统计运算以产生新的数据集。比如说，根据分组变量对一个 大表进行聚合。
- 建模和计算。将数据跟统计模型、机器学习算法或其他计算工具联系起来。
- 展示数据。创建交互式的或静态的图片或文字摘要。

$\color{red}\large \textbf{的确是有这些任务。}$ $\color{red}\large \textbf{看完之后要对应一下，看看是不是还有遗漏}$



OK，下面我们主要看下三个例子：

现在这些例子仅仅是为了提起兴趣，因此只会在一个比较髙的层次进行讲解。后续的章节将会对此进行非常详细的讲解。

# 一份从生成 .gov 或 .mil 短链接的用户那里收集来的匿名数据

这里我们提供了一份从生成 .gov 或 .mil 短链接的用户那里收集来的匿名数据。

以毎小时快照为例，文件中各行的格式为 JSON ( 即JavaScript Object Notation，这是一 种常用的Web数据格式 )。

## 我们先读取一行

例如，如果我们只读取某个文件中的第一行：

```
path='path = 'datasets/bitly_usagov/example.txt'
open(path).readline()
```

结果如下：

```
{ "a": "Mozilla\\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\\/535.11 (KHTML, like Gecko) Chrome\\/17.0.963.78 Safari\\/535.11", "c": "US", "nk": 1, "tz": "America\\/New_York", "gr": "MA", "g": "A6qOVH", "h": "wfLQtf", "l": "orofrog", "al": "en-US,en;q=0.8", "hh": "1.usa.gov", "r": "http:\\/\\/www.facebook.com\\/l\\/7AQEFzjSi\\/1.usa.gov\\/wfLQtf", "u": "http:\\/\\/www.ncbi.nlm.nih.gov\\/pubmed\\/22415991", "t": 1331923247, "hc": 1331822918, "cy": "Danvers", "ll": [ 42.576698, -70.954903 ] }
```

注意：上面的路径是斜杠，不是反斜杠，而且这是在 windows 下面的，也要用斜杠。$\color{red}\large \textbf{关于斜杠和反斜杠，要把那篇文章的引用放在这里。}$

## 使用 json 模块查看下

Python有许多内置或第三方模块可以将 JSON 字符串转换成Python卞典对象。这里，我将 使用json模块及其loads函数逐行加载已经下载好的数据文件：

```python
import json
path = 'datasets/bitly_usagov/example.txt'
records=[json.loads(line) for line in open(path)]
records[0]
print(records[0]['tz'])
```

输出：

```
{'a': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.78 Safari/535.11', 'c': 'US', 'nk': 1, 'tz': 'America/New_York', 'gr': 'MA', 'g': 'A6qOVH', 'h': 'wfLQtf', 'l': 'orofrog', 'al': 'en-US,en;q=0.8', 'hh': '1.usa.gov', 'r': 'http://www.facebook.com/l/7AQEFzjSi/1.usa.gov/wfLQtf', 'u': 'http://www.ncbi.nlm.nih.gov/pubmed/22415991', 't': 1331923247, 'hc': 1331822918, 'cy': 'Danvers', 'll': [42.576698, -70.954903]}
America/New_York
```

注意，Python的索引是从 0 开始的，其实大部分语言都是从 0开始的，但是 R 语言是从 1 开始的。

## OK，我们现在想知道数据集中最常出现的是哪个时区（即 tz 字段）

我们先看看一些时区到底是什么，我们先看10个时区：

```
import json
path = 'datasets/bitly_usagov/example.txt'
records=[json.loads(line) for line in open(path)]
timezones = [rec['tz'] for rec in records if 'tz' in rec]
print(timezones[:10])
```

注意：并不是所有记录都有时区字段，因此要进行 if in 判断。

```
['America/New_York', 'America/Denver', 'America/New_York', 'America/Sao_Paulo', 'America/New_York', 'America/New_York', 'Europe/Warsaw', '', '', '']
```

嗯，结果可见，有些时区的确是空着的。

虽然可以将它们过滤掉，但现在我们先留着。

接下来，我们对时区进行计数，计数的办法之一是在遍历时区的过程中 将计数值保存在字典中。

这里介绍两个办法：一个较难（只使用标 准Python库），另一个较简单（使用pandas）。



def get一counts（sequence）: counts = {} for x in sequence:

if x in counts: counts[x] += i

else:

counts[x] = 1 return counts

如果非常f解Python^准库，那么你可能会将代码写得更简洁一些： from collections import defaultdict

def get_counts2(sequence):

counts = defaultdict(int) #所权的值均会被初始化为0 for x in sequence:

counts[x] += 1 return counts

我将代码写到函数中是为了获得更高的可重用性。要用它对时区进行处理，只需将 time_zones 传入即口I":

In [31]: counts = get_counts(time_zones)

In [32]: counts['America/New York*]

Out[32]: 1251

In [33]: len(time zones)

Out[33]： 3440

如果想要得到前10位的时区及其计数值，我们需要用到一些有关字典的处理技巧：

def top_counts(countdict, n=io):

valuekeypairs = [(count, tz) for tz, count in count^dict.items()] value_key_pairs.sort() return valuej<ey_pairs[-n:]

现在我们就可以:

In [35]: topcounts(counts) Out[35]："

[(33, u1America/SaoPaulo'), (35, u.Europe/Madrid1),

(36, u1 Pacific/Honolulu *), (37, u'Asia/Tokyo'),

(74, u'Europe/London’),

(191， u.America/Denver.)， (382, u'America/LosAngeles' (400, u*America/Chicago1 (521, u..),

(1251， u1America/New—York.)]

###### 你可以在Python标准库中找到collections.Counter类，它能使这个任务变得更简单:

In [49]: from collections import Counter

In [50]: counts = Counter(time^zones)

In [51]: counts.mostcommon(io)

Out[5l]:

[(u'America/New^York11251),

(u.., 521),—

(u*America/Chicago', 400),

(u'America/LoS-Angeles1, 382), (u'America/Denver*, 191),

(u'Europe/London', 74),

(u'Asia/Tokyo', 37)，

(u'Pacific/Honolulu', 36), (u'Europe/Madrid* y 35),

(u’America/SaoPaulo., 33)]

是pandas屮最重要的数据结构，它用于将数据表示为一个表格。从一组原始 记录中创建DataFrame是很简单的：

In [289]: from pandas import DataFrame, Series

In [290]: import pandas as pd; import numpy as np In [291]: frame = DataFrame(records)

In [292]: frame

Out[292]:

〈class 'pandas.core.frame.DataFrame'>

Int64lndex: 3560 entries, 0 to 3559

Data columns:

heartbeat^ 120 non-null values a    3440 non-null values

| al   | 3094 | non-null | values |
| ---- | ---- | -------- | ------ |
| C    | 2919 | non-null | values |
| cy   | 2919 | non-null | values |
| g    | 3440 | non-null | values |
| gr   | 2919 | non-null | values |
| h    | 3440 | non-null | values |
| he   | 3440 | non-null | values |
| hh   | 3440 | non-null | values |

kw    93 non-null values

| 1    | 3440 | non-null | values |
| ---- | ---- | -------- | ------ |
| 11   | 2919 | non-null | values |
| nk   | 3440 | non-null | values |
| r    | 3440 | non-null | values |
| t    | 3440 | non-null | values |
| tz   | 3440 | non-null | values |
| u    | 3440 | non-null | values |

dtypes: float64(4), object(14)

In [293]: frame[1tz'][:1O]

Out[293]：

0    America/New^York

1    America/Denver

2    America/New^York

3    America/SaoPaulo

4    America/New_York

5    America/New_York

6    Europe/Warsaw

7

8 9

Name: tz

###### 这里frame的输出形式是摘要视图(summary view)，主要用于较大的DataFrame对象。 frame[ 'tz']所返回的Series对象有一个value_counts方法，该方法可以i上我们得到所需 的信息：

In [294]: tz_counts = frame['tz'].value_counts()

In [295]: tz_counts[:10]

Out[295]：

然后，我们想利用绘图库（即matplotlib）为这段数据生成一张图片。为此，我们先给id

录中未知或缺失的时区填h—个替代值。fillna函数可以替换缺失值（NA），而未知K （空字符串）则可以通过布尔型数组索引加以替换：

In [296]: clean—tz = frame['tz' ] .fillna('Missing*) In [297]: clean—tz[clean一tz ==’•]= 'Unknown'

In [298]: tzcounts = clean^tz.value_counts()

In [299]: tz_counts[:10]

0ut[299]:

America/NewYork 1251 Unknown 521 America/Chicago 400 America/Los_Angeles 382 America/Denver 191 Missing 120 Europe/London 74 Asia/Tokyo 37 Pacific/Honolulu 36 Europe/Madrid 35

利用counts1^3对象的plot方法即可得到一张水平条形阁*注4:

In [301]: tz—counts[:10].plot(kind=.barh.， rot=0)

最终结果如图2-1所示。我们还可以对这种数据进行很多处理。比如说，a字段含有执行 URL短缩操作的浏览器、设备、应用程序的相关信息：

In [302]: frame['a'][l]

Out[302]: u'GoogleMaps/RochesterNY'

In [303]: frame['a'][50]

Out[3O3]: u'Mozilla/5.0 (Windows NT 5.1； rv:10.0.2) Gecko/20100101 Firefox/10.0.21

In [304]: framel^^tsi]

Out[3O4]: u'Mozilla/5.0 (Linux; U; Android 2.2.2; en-us; LG-P925/VlOe Build/FRG83G) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1'

译注3:应该是tz^counts。

译注4:注意，一定要以pylab模式打开，否则这条代码没效果。包括很多缩写，pylab都直接弄好 了，如果不是用这种模式打开，后面很多代码一样会遇到问題，虽然不是什么大毛病，但 毕竟麻烦。后面如果遇到这没定义那找不到的情况，就请注意是不是因为这个。

图2-1: 1.usa.gov示例数据中最常出现的时区

将这些“agent”字符中屮的所有信息都解析出来是一件挺郁闷的工作。不过只要你 掌握了Python内置的字符串函数和正则表达式，事情就好办了。比如说，我们可以将这 种字符串的第一节(与浏览器大致对应)分离出来并得到另外一份用户行为摘要：

In [305]: results = Series([x.split()[o] for x in frame.a.dropna()])

In [306]: results[：5]

Out[3O6]:

0    Mozilla/5.0

1    GoogleMaps/RochesterNY

2    Mozilla/4.0

3    Mozilla/5.0

4    Mozilla/5.0

In [307]: results.valuecounts()[:8]

Out[307]：

Mozilla/5.0 2594 Mozilla/4.0 601 GoogleMaps/RochesterNY 121 Opera/9.8O 34 TEST_INTERNET_AGENT 24 GoogleProducer 21 Mozilla/6.0 5 BlackBerry852O/5.O.O.68l 4

现在，假设你想按Windows和非Windows用户对时区统计信息进行分解。为了简单起 见，我们假定只要agent字符串屮含有“Windows”就1人为该用户为Windows用户。巾干 有的agent缺失，所以首先将它们从数据中移除：

In [308]: cframe = frame[frame.a.notnull()]

其次根据a值计算出各行是否是Windows:

译注5:即浏览器的USER_AGENT信息。

In [309]: operatingsystem = np.where(cframe['a'].str.contains("Windows*),

'Windows', 'Not Windows1)

In [310]: operating system[:5]

0ut[310]:

0    Windows

1    Not Windows

2    Windows

3    Not Windows

4    Windows Name: a

接下来就可以根据时区和新得到的操作系统列表对数据进行分组了：

In [311]: by_tz—os = cframe.groupby(['tz', operating一system])

然后通过size对分组结果进行计数(类似于上面的value_counts函数)，并利用unstack对 计数结果进行重塑：

In [312]: agg^counts = by_tz_os.size() .unstack().fillna(o)

In [313]： agg—counts[:10]

Out[313]：

a    Not Windows

Windows

276

3

1

2

1

1

1

0

1

1



tz

245

最后，我们来选取最常出现的时区。为了达到这个目的，我根据agg^ounts中的行数构 造了一个间接索引数组：

\#用子按升序排列

In [314]: indexer = agg^counts.sum(1).argsort() In [315]: indexer[:10]

Out[315]：

tz

24

America/Argentina/BuenosAires 57 America/Argentina/Cordoba 26 America/Argentina/Mendoza 55

然后我通过take按照这个顺序截取了最后10行：

In [316]: count subset = agg counts.take(indexer)[-10:]

In [317]： count^subset Out[317]：

| a                  | Not Windows | Windows |
| ------------------ | ----------- | ------- |
| tzAmerica/SaoPaulo | 13          | 20      |
| Europe/Madrid      | 16          | 19      |
| Pacific/Honolulu   | 0           | 36      |
| Asia/Tokyo         | 2           | 35      |
| Europe/London      | 43          | 31      |
| America/Denver     | 132         | 59      |
| America/LosAngeles | 130         | 252     |
| America/Chicago    | 115         | 285     |
|                    | 245         | 276     |
| America/New York   | 339         | 912     |

这里也"f以生成一张条形图。我将使用stacked=True来生成一张堆积条形图(如图2-2所 示)：

In [319]: count subset.plot(kind=1barh', stacked=True)

由T在这张图中不太容易看清楚较小分组中Windows用户的相对比例，因此我们可以将 各行规范化为“总计为1”并重新绘图(如图2-3所示)：

In [321]: normed subset = count_subset.div(count_subset.sum(l), axis=0)

In [322]: normed subset.plot(kind='barh', stacked=True)

这里所用到的所有方法都会在本书后续的章节中详细讲解。

#### MovieLens 1M 数据集

GroupLens Research ([http://www.grouplens.org/node/73)](http://www.grouplens.org/node/73)%e9%87%87%e9%9b%86%e4%ba%86%e4%b8%80%e7%bb%84%e4%bb%8e20%e4%b8%96%e7%ba%aa90%e5%b9%b4%e6%9c%ab)[采集了一组从20世纪90年末](http://www.grouplens.org/node/73)%e9%87%87%e9%9b%86%e4%ba%86%e4%b8%80%e7%bb%84%e4%bb%8e20%e4%b8%96%e7%ba%aa90%e5%b9%b4%e6%9c%ab) 到21世纪初由MovieLens用户提供的电影评分数据。这些数据中包括电影评分、电影元 数据(风格类型和年代)以及关于用户的人口统计学数据(年龄、邮编、性别和职业 等)。基干机器学习算法的推荐系统一般都会对此类数据感兴趣。虽然我不会在本朽中 详细介绍机器学习技术，但我会告诉你如何对这种数据进行切片切块以满足实际需求。

图2-2:按Windows和非Windows用户统计的最常出现的时区

图2-3:按Windows和非Windows用户比例统计的最常出现的时区

MovieLens 1 M数据集含有来自6000名用户对4000部电影的100万条评分数据。它分为 三个表：评分、用户信息和电影信息。将该数据从zip文件中解压出来之后，可以通过 pandas.read_table将各个表分别读到一个pandas DataFrame对象屮：

import pandas as pd

unames = ['userid', •gender、 'age', 'occupation', 'zip*]

users = pd.read_table（'ml-lm/users.dat', sep='::', header=None, names=unames） rnames = [•userid、 ’movie id’，"rating*, 'timestamp']

ratings = pd.read_table（'ml-lm/ratings.dat', sep=*::', header=None, names=rnames） mnames = ['movieid*, 'title', 'genres']

movies = pd.read_table（'ml-lm/movies.dat', sep=*::1, header=None, names=mnames）

利用Python的切片语法，通过査看毎个DataFrame的前儿行即可验证数据加载C作是否 一切顺利：

In [334]: users[:5] Out[334]：

| user 一 | id   | gender | age  | occupation | zip   |
| ------- | ---- | ------ | ---- | ---------- | ----- |
| 0       | 1    | F      | l    | 10         | 48067 |
| 1       | 2    | M      | 56   | 16         | 70072 |
| 2       | 3    | M      | 25   | 15         | 55117 |
| 3       | 4    | M      | 45   | 7          | 02460 |
| 4       | 5    | M      | 25   | 20         | 55455 |

In [335]: ratings[：5] Out[335]：

| user 一0 | id1       | movie_id1193 | rating timestamp |           |
| -------- | --------- | ------------ | ---------------- | --------- |
| 5        | 978300760 |              |                  |           |
| l        | 1         | 661          | 3                | 978302109 |
| 2        | l         | 914          | 3                | 978301968 |
| 3        | 1         | 3408         | 4                | 978300275 |
| 4        | 1         | 2355         | 5                | 978824291 |

In [336]: movies[：5] Out[336]:

|      | movie」d | title                                | genres                         |
| ---- | -------- | ------------------------------------ | ------------------------------ |
| 0    | 1        | Toy Story （1995）                   | Animation\|Children's\|Comedy  |
| 1    | 2        | Dumanji （1995）                     | Adventure\|Children's\|Fantasy |
| 2    | 3        | Grumpier Old Men （1995）            | Comedy\|Romance                |
| 3    | 4        | Waiting to Exhale （1995）           | Comedy\|Drama                  |
| 4    | 5        | Father of the Bride Part II （1995） | Comedy                         |

In [337]： ratings

Out[337]：

〈class •pandas.core.frame.DataFrame'>

Int64lndex: 1000209 entries, 0 to 1000208

Data columns: user^id    1000209

non-null values non-null values non-null values non-null values



movie^id    1000209

rating    1000209

timestamp    1000209

dtypes: int64（4）

注意，其屮的年龄和职业是以编码形式给出的，它们的具体含义请参考该数据集的 README文件。分析散布在三个表中的数据可不是一件轻松的事情。假设我们想要根据性 别和年龄计算某部电影的平均得分，如果将所有数据都合并到一个表中的活问题就简中.

多了。我们先用pandas的merge函数将ratings跟users合并到一起，然后再将movies也合 并进去。pandas会根据列名的重叠情况推断出哪些列是合并（或连接）键：

In [338]: data = pd.merge(pd.merge(ratings, users)， movies

In [339]: data

Out[339]：

〈class •pandas.core.frame.DataFrame1 > Int64lndex: 1000209 entries, 0 to 1000208 Data columns:



user^id

movie id

rating

timestamp

gender

age

occupation

zip

title

genres



1OOO2O9

1000209

1000209

1000209

1000209

1000209

1000209

1000209

1000209

1000209



non-null

non-null

non-null

non-null

non-null

non-null

non-null

non-null

non-null

non-null



values

values

values

values

values

values

values

values

values

values



dtypes: int64(6)， object(4)

In [340]: data.ix[o]

Out[34O]:

userid

movieid

rating

timestamp

gender

age

occupation zip title



1

1

5

978824268

F

1

10

48067



genres Name: 0



Toy Story (1995) Animation|Children's!Comedy



现在，只要稍微熟悉一下pandas，就能轻松地根据任意个用户或电影属性对评分数据 进行聚合操作了。为了按性别计算每部电影的平均得分，我们可以使用pivotjtable 方法：

In [341]: mean_ratings = data.pivot_table('rating*, rows='title',

"":    cols=•gender•, aggfunc=,mean1)

In [342]: mean一ratings[:5]

Out[342]:

gender

title

3.375000

3.388889

2.675676

2.793478

3.828571



2.761905

3.352941

2.733333

2.962085

3.689024



$1,000,000 Duck (1971)

'Night Mother (1986)

•Til There Was You (1997) ’burbs, The (1989)

•••And Justice for All (1979)

该操作产生了另一个DataFrame，其内容为电影平均得分，行标为电影名称，列标为性 别。现在，我打算过滤掉评分数据不够250条的电影(随便选的一个数字)。为了达到 这个目的，我先对title进行分组，然后利用size()得到一个含有各电影分组大小的Series 对象：



In [343]： ratingsby—title = data.groupby（'title'）.size（）

In [344]: ratingsby title[:10]

0ut[344]：

title



$1,000,000 Duck （1971）

'Night Mother （1986）

•Til There Was You （1997）

•burbs, The （1989）

•••And Justice for All （1979） 1-900 （1994）

10 Things I Hate About You （1999） 101 Dalmatians （1961）

101 Dalmatians （1996）

12 Angry Men （1957）



37

70

52

303

199



700

565

364

616



In [345]: active—titles = ratingsby一title.index[ratings_by一title >= 250]

In [346]: active_titles

0ut[346]:

Index（['burbs, The （1989）, 10 Things I Hate About You （1999）,

101 Dalmatians （1961）, •••， Young Sherlock Holmes （1985），

Zero Effect （1998）， eXistenZ （1999）], dtype=object）



该索引中含有评分数据大于250条的电影名称，然后我们就可以据此从前面的mean ratings中选取所需的行了：



In [347]: mean^ratings = meanratings.ix[activetitles]

In [348]: mean^ratings

Out[348]:    "

〈class •pandas.core.frame.DataFrame * >

Index: 1216 entries, ’burbs, The （1989） to eXistenZ （1999） Data columns:

F    1216 non-null values

M    1216 non-null values

dtypes: float64（2）



为f 了解女性观众最飪欢的电影，我们可以对F列降序排列:



In [350]: top femaleratings = mean_ratings.sort_index（by=,F*, ascending=False）



In [351]: top_female_ratings[:l0]

Out[35l]： 一 "

gender

title

Close Shave, A （1995）



4.644444 4.473795



Wrong Trousers, The （1993）

4.588235

4.572650

4.563107

4.562602

4.539075

4.537879

4.536667

4.513889

4.513317



4.478261

4.464589

4.385075

4.491415

4.560625

4.293255

4.372611

4.272277

4.518248



Sunset Blvd. （a.k.a. Sunset Boulevard） （1950）

Wallace & Gromit: The Best of Aardman Animation （1996） Schindler's List （1993）

Shawshank Redemption, The （1994）

Grand Day Out, A （1992）

To Kill a Mockingbird （1962）

Creature Comforts （1990）

Usual Suspects, The （1995）

##### 计算评分分歧

假没我们想要找出男性和女性观众分歧最大的电影。一个办法是给meanjatings加上-个用于存放平均得分之差的列，并对其进行排序：

In [352]: meanratings[.dif〒.]=mean—ratings[] • mean一ratings[•F•]

按“difr排序即可得到分歧最大且女性观众更喜欢的电影：

In [353]: sortedby—diff = meanratings.sort_index(by=1diff')

In [354】：sortedbydiff[:15] Out[354]：

| gender                                  | F        | M        | diff      |
| --------------------------------------- | -------- | -------- | --------- |
| title                                   |          |          |           |
| Dirty Dancing （1987）                  | 3.790378 | 2.959596 | •0.830782 |
| Jumpin' Jack Flash （1986）             | 3.254717 | 2.578358 | -0.676359 |
| Grease （1978）                         | 3.975265 | 3.367041 | -0.608224 |
| Little Women （1994）                   | 3.870588 | 3.321739 | -0.548849 |
| Steel Magnolias （1989）                | 3.901734 | 3.365957 | -0.535777 |
| Anastasia （1997）                      | 3.800000 | 3.281609 | -0.518391 |
| Rocky Horror Picture Show, The （1975） | 3.673016 | 3.160131 | -0.512885 |
| Color Purple, The （1985）              | 4.158192 | 3.659341 | -0.498851 |
| Age of Innocence, The （1993）          | 3.827068 | 3.339506 | -0.487561 |
| Free Willy （1993）                     | 2.921348 | 2.438776 | -0.482573 |
| French Kiss （1995）                    | 3.535714 | 3.056962 | -0.478752 |
| Little Shop of Horrors, The （i960）    | 3.65OOOO | 3.179688 | •0.470312 |
| Guys and Dolls （1955）                 | 4.051724 | 3.583333 | -0.468391 |
| Mary Poppins （1964）                   | 4.197740 | 3.730594 | -0.467147 |
| Patch Adams （1998）                    | 3.473282 | 3.008746 | -0.464536 |

对排序结果反序并取出前15行，得到的则是男性观众更喜欢的电影:

林对行反序，并取出前15行

In [355]: sortedbydiff[::-1][：15] Out[355]：

| gender                                   | F        | M        | diff     |
| ---------------------------------------- | -------- | -------- | -------- |
| title                                    |          |          |          |
| Good, The Bad and The Ugly, The （1966） | 3.494949 | 4.221300 | 0.726351 |
| Kentucky Fried Movie, The （1977）       | 2.878788 | 3.555147 | 0.676359 |
| Dumb & Dumber （1994）                   | 2.697987 | 3.336595 | 0.638608 |
| Longest Day， The （1962）               | 3.411765 | 4.031447 | 0.619682 |

| Cable Guy, The （1996）                | 2.25OOOO | 2.863787 | 0.613787 |
| -------------------------------------- | -------- | -------- | -------- |
| Evil Dead II （Dead By Dawn） （1987） | 3.297297 | 3.909283 | 0.611985 |
| Hidden, The （1987）                   | 3.137931 | 3.745098 | 0.607167 |
| Rocky III （1982）                     | 2.361702 | 2.943503 | 0.581801 |
| Caddyshack （1980）                    | 3.396135 | 3.969737 | 0.573602 |
| For a Few Dollars More （1965）        | 3.409091 | 3.953795 | 0.544704 |
| Porky's （1981）                       | 2.296875 | 2.836364 | 0.539489 |
| Animal House （1978）                  | 3.628906 | 4.167192 | 0.538286 |
| Exorcist, The （1973）                 | 3.537634 | 4.067239 | 0.529605 |
| Fright Night （1985）                  | 2.973684 | 3.5OOOOO | 0.526316 |
| Barb Wire （1996）                     | 1.585366 | 2.100386 | 0.515020 |

如果只是想要找出分歧最大的电影(不考虑性别因紊)，则可以计算得分数据的方差或 标准差：

\#根据电影名称分纟a的得分数据的标准差

In [356]: rating_std_by_title = data.groupby(1 title')['rating*].std()

林根据active_titles进行过波

In [357]: ratingstd—by title = rating std_bytitle.ix[active_titles]

林根据值对Series进行降序排列

In [358]: ratingstd—bytitle.order(ascending=False)[:10]

Out[358]:

title

Dumb & Dumber （1994）    1.321333

Blair Witch Project, The    （1999）    1.316368

Natural Born Killers （1994）    1.307198

Tank Girl （1995）    1.277695

Rocky Horror Picture Show, The （1975）    1.260177

Eyes Wide Shut （1999）    1.259624

Evita （1996）    1.253631

Billy Madison （1995）    1.249970

Fear and Loathing in Las    Vegas （1998）    1.246408

Bicentennial Man （1999）    1.245533

Name: rating

可能你已经注意到了，电影分类是以竖线(I)分隔的字符串形式给出的。如果想对电影 分类进行分析的话，就需要先将其转换成更有用的形式才行。我将在本书后续章节屮讲 到这种转换处理，到时还会再用到这个数据。

#### 1880—2010年间全美婴儿姓名

美国社会保障总署(SSA)提供了一份从1880年到2010年的婴儿名字频率数据，Hadley Wickham (许多流FrR包的作者)经常用这份数据來演示R的数据处理功能。

In [4]: names.head(io)

Out[4]:

name    sex    births year

0 Mary    F    7065    1880

| 1                            | Anna      | F    | 2604 | 1880 |
| ---------------------------- | --------- | ---- | ---- | ---- |
| 2                            | Emma      | F    | 2003 | 1880 |
| 3                            | Elizabeth | F    | 1939 | 1880 |
| 4                            | Minnie    | F    | 1746 | 1880 |
| 5                            | Margaret  | F    | 1578 | 1880 |
| 6                            | Ida       | F    | 1472 | 1880 |
| 7                            | Alice     | F    | 1414 | 1880 |
| 8                            | Bertha    | F    | 1320 | 1880 |
| 9                            | Sarah     | F    | 1288 | 1880 |
| 你可以用这个数据集做很多事， | 例如:     |      |      |      |

•    计算指定名字（可以是你自己的，也可以是别人的）的年度比例。

•    计算某个名字的相对排名。

•    计算各年度最流行的名字，以及增长或减少最快的名字。

•    分析名字趋势：元音、辅音、长度、总体多样性、拼写变化、首尾字母等。

•    分析外源性趋势：圣经中的名字、名人、人口结构变化等。

利用前面介绍过的那些工具，这些分析工作都能很轻松地完成，因此我会尽量多讲一 些。我建议你下载这些数据并亲自试一试。如果你在这些数据中找到了某个有趣的模 式，我将非常乐意听上一听。

到编写本书时为止，美国社会保障总署将该数据库按年度制成了多个数据文件，其屮给 出了毎个性别/名字组合的出生总数。这些文件的原始档案可以在这里获取：

<http://www.ssa.gov/oact/babynames/limits.html>?

如果你在阅读本书的时候这个页面已经不见了，也可以用搜索引擎找找。下载 “National data”文件names.zip，解压后的目录中含有一组文件（如yobl880.txt）。我

用UNIX的head命令査看了其中一个文件的前10行（在Windows上，你可以用more命令， 或直接在文本编辑器中打开）：

In [367]: !head -n 10 names/yobl880.txt

Mary,F,7065

Anna 人 2604

Emma,F，2OO3

Elizabeth,F,1939

Minnie,F,1746

Margaret,F,1578

Ida,F,1472

Alice,F,1414

Bertha,F,1320

Sarah,F,1288

译注6:如下链接可能不可用，读者可直接在本书的github上下载。

由干这是一个非常标准的以逗号隔开的格式，所以可以用pandas.readjsv将其加载到 DataFrame 中:

In [368]: import pandas as pd

In [369]: namesl88O = pd.readcsv(•names/yobl88o.txt•， names=['name*, 'sex',

'births'])

In [370]: namesl880 Out[37O]:

〈class 'pandas.core.frame.DataFrame'>

Int64lndex: 2000 entries, 0 to 1999 Data columns:

name    2000    non-null    values

sex    2000    non-null    values

births    2000    non-null    values

dtypes: int64(l), object(2)

这些文件中仅含有当年出现超过5次的名字。为了简单起见，我们可以用births列的sex分 组小计表示该年度的births总计：

In [371]: namesl880.groupby('sex').births.sum()

Out[37l]：

sex

F 90993 M    110493

Name: births

由于该数据集按年度被分隔成了多个文件，所以第一件事情就是要将所有数据都组装到 一个DataFrame里面，并加h—个year字段。使用pandas.concat即卩J*达到这个冃的：

林2010是目前最后一个备效统计年度 years = range(l88O, 2011) pieces =[]

columns = ['name', 'sex', 'births']

for year in years:

path = 'names/yob%d.txt' % year

frame = pd.read_csv(path, names=columns)

frame['year'] = year pieces.append(frame)

\#将所蒋数据幣合到单个DataFrame中

names = pd.concat(pieces, ignore_index=True)

这里需要注意几件#情。第一，concat默认是按行将多个DataFrame组合到一起的；第 二，必须指定ignore_index=True，因为我们不番望保留read_csv所返回的原始行号。现 在我们得到了一个非常大的DataFrame，它含有全部的名字数据。

现在names这个DataFrame对象看上去应该适这个样子:

In [373]: names

Out[373]：

〈class 'pandas.core.frame.Data Frame'> Int64lndex: 1690784 entries, 0 to 1690783 Data columns:

name

sex

births

year

dtypes:



1690784

1690784

1690784

1690784

int64(2),



non-null

non-null

non-null

non-null

object(2)



values

values

values

values



有了这残数据之后，我们就吋以利用groupby或pivot_table在year和sex级别上对K•进行 聚合了，如图2-4所示：

In [374]: total_births = names•pivot—table(•births•， rows=fyear',

…：    cols=1 sex', aggfunc=sum)

In [375]: totalbirths.tail()

| Out[375]： |         |         |
| ---------- | ------- | ------- |
| sex        | F       | M       |
| year       |         |         |
| 2006       | 1896468 | 2050234 |
| 2007       | 1916888 | 2069242 |
| 2008       | 1883645 | 2032310 |
| 2009       | 1827643 | 1973359 |
| 2010       | 1759010 | 1898382 |

In [376]: totalbirths.plot(title='Total births by sex and year.)

下面我们来插入一个prop列，用干存放指定名字的婴儿数相对于总出生数的比例。prop 值为0.02表示毎100名婴儿屮有2名取了当前这个名字。因此，我们先按year和sex分组， 然后再将新列加到各个分组上：

def addprop(group):

\#瘡数除法会向下岡整

births = group.births.astype(float)

group["prop1] = births / births.sum() return group

names = names.groupby(['year', 'sex']).apply(add_prop)

注意：由干births是幣数，所以我们在计算分式时必须将分子或分母转换成浮点数(除非你止在使 用Python 3! ) 0

图2-4:按性别和年度统计的总出生数

现在，完整的数据集就有了下面这些列:

In [378]: names

Out[378]:

〈class •pandas.core.frame.DataFrame'> Int64lndex: 1690784 entries, 0 to 1690783 Data columns:

| name    | 1690784     | non-null   | values    |
| ------- | ----------- | ---------- | --------- |
| sex     | 1690784     | non-null   | values    |
| births  | 1690784     | non-null   | values    |
| year    | 1690784     | non-null   | values    |
| prop    | 1690784     | non-null   | values    |
| dtypes: | float64(l), | int64(2)； | 丨object⑺ |

在执行这样的分组处理时，一般都应该做一些有效性检査，比如验i止所有分组的prop的 总和是否为1。由干这是一个浮点型数据，所以我们应该用np.allclose来检查这个分组 总计值是否足够近似于(可能不会精确等干)1:

In [379]: np.allclose(names.groupby(['year', * sex1]).prop.sum()，l)

Out[379]: True

这样就算完活r。为f便于实现更进一步的分析，我需要取出该数据的一个子集：每对 sex/year组合的前1000个名字。这又是一个分组操作：

def gettoplOOO(group):

return group.sort_index(by='births *, ascending=False)[:1000]

grouped = names.groupby(['year1, 'sex'])

topiOOO = grouped.apply(get toplOOO)

如果你喜欢DIY的活，也可以这样:

pieces =[]

for year, group in names.groupby(['year', 'sex']):

pieces•append(group.sort_index(by=•births、scending=False)[:1000])

toplOOO = pd.concat(pieces, ignore_index=True)

现在的结果数据集就小多了：

In [382]: toplOOO Out[382]:

〈class 1 pandas.core.frame.DataFrame'>

Int64lndex: 261877 entries, 0 to 261876 Data columns:

| name   | 261877 | non-null | values |
| ------ | ------ | -------- | ------ |
| sex    | 261877 | non-null | values |
| births | 261877 | non-null | values |
| year   | 261877 | non-null | values |
| prop   | 261877 | non-null | values |

dtypes: float64(l), int64(2)， object(2)

掊下来的数据分析工作就针对这个toplOOO数据集了。

##### 分析命名趋势

有了完整的数据集和刚才生成的toplOOO数据集，我们就可以开始分析各种命名趋势了。 首先将前1000个名字分为男女两个部分：

In [383]: boys = toplOOO[toplOOO.sex ==    ]

In [384]: girls = toplOOO[toplOOO.sex == 'F']

这是两个简单的时间序列，只需稍作整理即可绘制出相应的图表(比如每年叫做John和 Mary的婴儿数)。我们先生成一张按year和name统计的总出生数透视表：

In [385]: total^births = toplOOO.pivot_table(* births1, rows=.year., cols=.name’，

…：    aggfunc=sum)

现在，我们用DataFrame的plot方法绘制几个名字的曲线图：

In [386]: totalbirths Out[386]:    "

〈class •pandas.core.frame.DataFrame'>

Int64lndex: 131 entries, 1880 to 2010 Columns: 6865 entries, Aaden to Zuri dtypes: float64(6865)

In [387]: subset = total births[[•〕ohrf, 'Harry', •Mary., 'Marilyn1]]

In [388]: subset.plot(subplots=True^ figsize=(12，10), grid=False，

…：    title=MNumber of births per year")

最终结果如图2-5所示。从图中可以看出，这几个名字在美国人民的心目中巳经风光不再 了。但事实并非如此简单，我们在下一节中就能知道是怎么一回事了。

图2-5:几个男孩和女孩名字随时间变化的使用数量

###### 评估命名多样性的增长

图2-5所反映的降低情况可能意味着父母愿意给小孩起常见的名字越来越少。这个假设可 以从数裾中得到验证。一个办法是计算最流行的1000个名字所占的比例，我按yeai•和sex 进行聚合并绘图：

In [390]: table = toplOOO.pivot_table(* prop1, rows=,year,,

…：    cols='sex', aggfunc=sum)

In [391]: table.plot(title=,Sum of tablelOOO.prop by year and sex',

•••:    yticks=np.linspace(O, 1.2， 13)， xticks=range(l88O， 2020, 10))

结果如图2-6所示。从图中可以看出，名字的多样性确实出现了增长(前1000项的比例 降低)。另一个办法是计算占总出生人数前50%的不同名字的数量，这个数字不太好计 算。我们只考虑2010年男孩的名字：

In [392]: df = boys[boys.year == 2010]

In [393]： df Out[393]：

〈class •pandas.core.frame.DataFrame'>

Int64lndex: 1000 entries, 260877 to 261876 Data columns:

name 1000 non-null values

| sex     | 1000                         | non-null values |
| ------- | ---------------------------- | --------------- |
| births  | 1000                         | non-null values |
| year    | 1000                         | non-null values |
| prop    | 1000                         | non-null values |
| dtypes: | float64⑴,int64(2), object(2) |                 |

图2-6:分性别统计的前1000个名字在总出生人数中的比例

在对prop降序排列之后，我们想知道前面多少个名字的人数加起来才够50%。虽然编写 一个for循环确实也能达到目的，但NumPy有一种更聪明的矢量方式。先i|•算prop的累 计和cumsum，然后再通过searchsorted方法找出0.5应该被插入在哪个位置才能保证不破 坏顺序：

In [394]: prop_cumsum = df.sort_index(by=1 prop*, ascending=False).prop.cumsum()

In [395]: prop—cumsum[:10]

Out[395]：

260877 0.011523 260878 0.020934 260879 0.029959 260880 0.038930 260881    0.047817

260882 0.056579 260883 0.065155 260884 0.073414 260885 0.081528 260886 0.089621

In [396]: prop一cumsum.searchsorted(0.5)

Out[396]: 116

由干数组索引是从0开始的，因此我们要给这个结果加1，即a终结果为117。拿1900年 的数据来做个比较，这个数字要小得多：

In [397】：df = boys[boys.year == 1900]

In [398]: inl900 = df.sort_index(by=,prop', ascending=False).prop.cumsum()

In [399]: inl900.searchsorted(0.5) + 1 Out[399]： 25

现在就可以对所有year/sex组合执行这个计算了。按这两个字段进行groupby处理，然后 用一个函数汁算各分组的这个值：

def getquantilecount(group, q=0.5)：

group = group.sort_index(by=1 prop1, ascending=False) return group.prop.cumsum().searchsorted(q) + 1

diversity = toplOOO.groupby(['year*, 1 sex']).apply(getquantilecount) diversity = diversity.unstack('sex*)

现在，diversity这个DataFrame拥有两个时间序列(每个性别各一个，按年度索引)。 通过IPython，你可以査看其内容，还可以像之前那样绘制图表(如图2-7所示)：

| In [401]: diversity.head() |                                                            |      |
| -------------------------- | ---------------------------------------------------------- | ---- |
| Out[401]:                  |                                                            |      |
| sex                        | F                                                          | M    |
| year                       |                                                            |      |
| 1880                       | 38                                                         | 14   |
| 1881                       | 38                                                         | 14   |
| 1882                       | 38                                                         | 15   |
| 1883                       | 39                                                         | 15   |
| 1884                       | 39                                                         | 16   |
| In [402]:                  | diversity.plot(title="Number of popular names in top 50%") |      |

图2-7:按年度统计的密度表

从图中可以看出，女孩名字的多样性总是比男孩的髙，而且还在变得越来越高。读者们 可以自己分析一下具体是什么在驱动这个多样性(比如拼写形式的变化)。

###### “最后一个字母”的变革

2007年，—名婴儿姓名研究人员Laura Wattenberg在她自己的网站上指出(http://www. babynamewizard.com) ••近百年来，男孩名字在最后一个字母上的分布发生了显著的变 化。为了了解具体的情况，我皆先将全部出生数据在年度、性别以及末字母上进行了聚合：

\#从name列取出始后一个字母 get_last_letter = lambda x: x[-l] lastletters = names.name.map(get_last_letter) lastletters.name = 'lastletter1

table = names.pivottable('births', rows=last_letters,

cols=['sex1, 'year*aggfunc=sum)

然后，我选出具有一定代表性的三年，并输出前面几行：

In [404]: subtable = table.reindex(columns=[1910, i960， 2010]} level='year')

In [405]: subtable.head()

0ut[405]：

| sex         | F      | M      |        |       |        |        |
| ----------- | ------ | ------ | ------ | ----- | ------ | ------ |
| year        | 1910   | I960   | 2010   | 1910  | I960   | 2010   |
| last^letter |        |        |        |       |        |        |
| a           | 108376 | 691247 | 670605 | 977   | 5204   | 28438  |
| b           | NaN    | 694    | 450    | 411   | 3912   | 38859  |
| c           | 5      | 49     | 946    | 482   | 15476  | 23125  |
| d           | 6750   | 3729   | 2607   | 22111 | 262112 | 44398  |
| e           | 133569 | 435013 | 313833 | 28655 | 178823 | 129012 |

接下来我们需要按总出生数对该表进行规范化处理，以便讣兑出各性别各末字母占总出 生人数的比例：

| In        | [406]: | subtable.sum() |
| --------- | ------ | -------------- |
| Out[406]: |        |                |
| sex       | year   |                |
| F         | 1910   | 396416         |
|           | I960   | 2022062        |
|           | 2010   | 1759010        |
| M         | 1910   | 194198         |
|           | I960   | 2132588        |
|           | 2010   | 1898382        |
| In        | [407]: | letter_prop =  |

有了这个字母比例数据之后，就可以生成一张各年度各性别的条形图了，如图2-8所示

import matplotlib.pyplot as pit

fig, axes = plt.subplots(2, l, figsize=(io, 8))

letter—prop[.M'].plot(kind=,bar,, rot=0, ax=axes[O], title^'Male')

letter_prop['F1].plot(kind='bar', rot=0, ax=axes[l], title=.Female., legend=False)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/EcakckiCcj.jpg?imageslim)


图2-8:男孩女孩名字中各个末字母的比例

从图2-8中可以看出，从20世纪60年代开始，以字母“n”结尾的男孩名字出现了显著的 增长。回到之前创建的那个完整表，按年度和性别对其进行规范化处理，并在男孩名字 中选取几个字母，最后进行转置以便将各个列做成一个时间序列：

In [410】： letter^prop = table / table.sum().astype(float)

In [4ll]: dnyts = letter_prop.ix[['d''n', *y'], 'M1].T

| In [412]: dny一ts.head() |          |          |
| ------------------------ | -------- | -------- |
| 0ut[412]:                |          |          |
| year                     | d        | n        |
| 1880                     | 0.083055 | 0.153213 |
| 1881                     | 0.083247 | 0.153214 |
| 1882                     | 0.085340 | 0.149560 |
| 1883                     | 0.084066 | 0.151646 |
| 1884                     | 0.086120 | 0.149915 |

0.075760

0.077451

0.077537

0.079144

0.080405



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180803/1mF9ClCgf9.jpg?imageslim)


有了这个时间序列的DataFrame之后，就可以通过其plot方法绘制出一张趋势图了（如图 2-9所示）：

In [414]: dny_ts.plot()

###### 引言丨45

图2-9:各年出生的男孩中名字以d/n/y结尾的人数比例

###### 变成女孩名字的男孩名字(以及相反的情况)

另一个有趣的趋势是，早年流行于男孩的名字近年来“变性了”，例如Lesley或Leslie。 回到toplOOO数据集，找出其中以“lesl”幵头的一组名字：

In [415]: all_names = toplOOO.name.unique()

In [416]: mask = np.array(['lesl* in x.lower() for x in all^names])

In [417]: lesleylike = all_names[mask]

In [418]: lesley—like

Out[418]: array([Leslie, Lesley, Leslee, Lesli, Lesly], dtype=object)

然后利用这个结果过滤其他的名字，并按名字分组计算出生数以查看相对频率：

In [419]: filtered = toplOOO [toplOOO. name. isin(lesley_like)]

In [420]: filtered.groupby(1 name') .births.sum()

Out[42O]:

name

Name: births

接下来，我们按性别和年度进行聚合，并按年度进行规范化处理：

In [421]: table = filtered. pivot_table(' births', rows=fyear1,

…：    cols=f sex', aggfunc=' sum')

In [422]: table = table.div(table.sum(l), axis:。)

In [423]: table.tail()

Out[423]：

sex F M year

2006    1    NaN

2007    1    NaN

2008    1    NaN

2009    1    NaN

2010    1    NaN

现在，我们就可以轻松绘制一张分性别的年度曲线图了(如图2-10所示)：

In [425]： table.plot(style=pM*: 'k-*, 'F*:

图2-10:各年度使用“Lesley型”名字的男女比例

#### 小结及展望

本章中的这些例子都非常简单，但它们可以让你大致了解后续章节的相关内容。本书关 注的焦点是工具而不是那些复杂精妙的分析方法。掌握本书所介绍的技术将使你能够立 马开展自己的分析工作（假没你已经知道要做什么了）。

引言I 47
