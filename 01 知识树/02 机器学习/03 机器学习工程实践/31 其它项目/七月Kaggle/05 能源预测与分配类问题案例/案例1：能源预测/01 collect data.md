TODO

- 还是非常全面的，完整的描述了一个项目从什么都没有，到做出真正可以用的模型之间的过程。
- 数据链接还是要补充进来的
-



关于天气的信息，如果是你在公司里工作做项目，实际上不一定有人提供好你给的，比如滴滴要做一些需求，他需要天气数据就直接从气象局买的，有些是可以自己采集到的。


## 获取数据


这个项目的数据是从 New York State Independent Service Authority (NYISO) 获得的。他们把所有的历史数据都以 CSV 格式存储在 FTP 服务器上。

想了解更多的信息可以戳下面的页面: http://www.nyiso.com/public/markets_operations/market_data/load_data/index.jsp

数据也可以从 [这个页面](http://mis.nyiso.com/public/P-58Blist.htm) 直接获得。

整个纽约州的电能由12个“区域”生产和提供，这12个区有自己独立的能源市场。下面这张图可以给你一个直观的印象，大概的一个分布状况。我们这里采集到的数据，也会按照这12个区域做分割，其中区域的名称会写在"name"字段里。<span style="color:red;">嗯。</span>


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180724/FaekHBH3AG.png?imageslim)


```python
import zipfile
import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import requests
import urllib
import os
import pandas as pd
import numpy as np
```

## 直接下载数据

我们直接找到数据源下载纽约州 2001-2015 年的相关数据。

直接拉下来的数据会以 zip 形式存储在 `../data/nyiso` 文件夹下。

然后咱们解压缩打包文件到 `../data/nyiso/all/raw_data` 文件夹。


```python
print "download and unzipping..."

# 把需要遍历的日期都产生出来
dates = pd.date_range(pd.to_datetime('2001-01-01'), \
                       pd.to_datetime('2015-12-31'), freq='M')

for date in dates:
    url = 'http://mis.nyiso.com/public/csv/pal/{0}{1}01pal_csv.zip'.format(date.year, str(date.month).zfill(2))
    urllib.urlretrieve(url, "../data/nyiso/{0}".format(url.split('/')[-1]))


```

```
download and unzipping...
```

解压缩：

```python
zips = []
for file in os.listdir("../data/nyiso"):
    if file.endswith(".zip"):
        zips.append(file)

def unzip(source_filename, dest_dir):
    with zipfile.ZipFile(source_filename) as zf:
        try:
            zf.extractall(dest_dir)
        except:
            print source_filename
            return

for z in zips:
    try:
        unzip('../data/nyiso/' + z, '../data/nyiso/all/raw_data')
        print '../data/nyiso/' + z + "extract done!"
    except:
        print '../data/nyiso/' + z
        continue
```

```
../data/nyiso/20010101pal_csv.zip
../data/nyiso/20010201pal_csv.zip
../data/nyiso/20010301pal_csv.zip
../data/nyiso/20010401pal_csv.zip
../data/nyiso/20010501pal_csv.zipextract done!
../data/nyiso/20010601pal_csv.zipextract done!
../data/nyiso/20010701pal_csv.zipextract done!
../data/nyiso/20010801pal_csv.zipextract done!
../data/nyiso/20010901pal_csv.zipextract done!
../data/nyiso/20011001pal_csv.zipextract done!
... 此处略去很多
../data/nyiso/20150901pal_csv.zipextract done!
../data/nyiso/20151001pal_csv.zipextract done!
../data/nyiso/20151101pal_csv.zipextract done!
../data/nyiso/20151201pal_csv.zipextract done!
```


文件多了处理起来麻烦，咱们整合到一个合并的文件里吧: combined_nyiso.csv

把文件的地址放到一个 list 里面：

```python
csvs = []
for file in os.listdir("../data/nyiso/all/raw_data"):
    if file.endswith("pal.csv"):
        csvs.append(file)
```

把 csv 合并起来。

```python
fout=open("../data/nyiso/all/combined_iso.csv","a")

# 第一个文件咱们保存头部column name信息:
for line in open("../data/nyiso/all/raw_data/"+csvs[0]):
    fout.write(line)# 把 column 信息保存下来

# 后面的部分可以跳过 headers:
for file in csvs[1:]:
    f = open("../data/nyiso/all/raw_data/"+file)
    f.next() # 跳过 header 开始读取
    for line in f:
         fout.write(line)
    f.close() # 关闭文件

fout.close()
```


为什么手写一个文件合并呢？因为数据量非常大，而 pandas 的合并都是要放到内存里的，因此内存会爆掉。而这样的手写，每次只会加载一个文件。<span style="color:red;">嗯。</span>


## 清洗和了解数据

这里总共有 14 年的数据，我们把它们放到一个完整的 pandas 数据帧(dataframe)里。


```python
df = pd.read_csv("../data/nyiso/all/combined_iso.csv")
```

这里所谓的数据清洗实际上是一个数据的选择过程，我们留下来我们感兴趣的 4 列: timestamp, region name, id, and load (说明一下，load 是电力需求，单位是 **兆瓦/Megawatts** ).


```python
cols = df.columns
df.columns = [col.lower().replace(' ', '') for col in cols]
df = df[['timestamp', 'name', 'ptid', 'load']]
```

重新把需要的数据写回 csv 文件中


```python
df.to_csv('../data/nyiso/all/combined_iso.csv', index=False)
```

看一下具体有哪些区域：之所以要看有哪些区，是因为我们要看看这些区的天气状况。

```python
df.name.unique()
```


```
array(['CAPITL', 'CENTRL', 'DUNWOD', 'GENESE', 'HUD VL', 'MHK VL',
       'MILLWD', 'N.Y.C._LONGIL', 'NORTH', 'WEST', 'LONGIL', 'N.Y.C.'], dtype=object)
```




## 建立**天气站/weather stations**的映射关系

我们的数据帧里区域的名称已经有了，我们要建立起它们和对应的城市还有天气站之间的映射关系（简单地理解就是需要关联几个数据表用）。我们直接把它们放在一个`python dict/字典`里，一会儿下一个 notebook 会用到这个映射关系。

这些区在气象局的网站上并不会一定以这些名字来命名，所以这里我们建立了映射表。

<span style="color:red;">看来 python 的基本语法在数据分析的时候也要掌握，因为这种基础的构造数据集的操作和一些不能使用 pandas 直接操作的也要自己来做。</span>

```python
regions = list(df.name.unique())
region_names = ['Capital', 'Central', 'Dunwoodie', 'Genese', 'Hudson Valley', 'Long Island', 'Mohawk Valley', 'Millwood', 'NYC', 'North', 'West']
cities = ['Albany', 'Syracuse', 'Yonkers', 'Rochester', 'Poughkeepsie', 'NYC', 'Utica', 'Yonkers', 'NYC', 'Plattsburgh', 'Buffalo']
weather_stations = ['kalb', 'ksyr', 'klga', 'kroc', 'kpou', 'kjfk', 'krme', 'klga', 'kjfk', 'kpbg', 'kbuf']
```


```python
weather_dict = dict(zip(regions, zip(weather_stations, region_names, cities)))
weather_dict
```


```
{'CAPITL': ('kalb', 'Capital', 'Albany'),
 'CENTRL': ('ksyr', 'Central', 'Syracuse'),
 'DUNWOD': ('klga', 'Dunwoodie', 'Yonkers'),
 'GENESE': ('kroc', 'Genese', 'Rochester'),
 'HUD VL': ('kpou', 'Hudson Valley', 'Poughkeepsie'),
 'LONGIL': ('kbuf', 'West', 'Buffalo'),
 'MHK VL': ('kjfk', 'Long Island', 'NYC'),
 'MILLWD': ('krme', 'Mohawk Valley', 'Utica'),
 'N.Y.C._LONGIL': ('klga', 'Millwood', 'Yonkers'),
 'NORTH': ('kjfk', 'NYC', 'NYC'),
 'WEST': ('kpbg', 'North', 'Plattsburgh')}
```



```python
import cPickle as pickle
pickle.dump(weather_dict, open('weather_dict.pkl','wb'))
```

## 把数据按区域切分

我们把数据按照 12 个区进行切分，更细致的切分可以让天气数据更精确。同时在测试阶段，一个区一个统一的文件也会方便很多。


```python
for region in weather_dict.keys():
    subset = df[df.name == region].copy()
    filename = weather_dict[region][1].lower().replace(' ', '') + '.csv'
    subset.to_csv('../data/nyiso/all/' + filename, index=False)
```

其中的一个文件大概长这样

|      | timestamp           | name   | ptid  | load   |
| ---- | ------------------- | ------ | ----- | ------ |
| 0    | 2012-01-01 00:00:00 | CAPITL | 61757 | 1084.4 |
| 1    | 2012-01-01 00:05:00 | CAPITL | 61757 | 1055.3 |
| 2    | 2012-01-01 00:10:00 | CAPITL | 61757 | 1056.6 |
| 3    | 2012-01-01 00:15:00 | CAPITL | 61757 | 1050.8 |
| 4    | 2012-01-01 00:20:00 | CAPITL | 61757 | 1050.8 |




# 输出2012年的数据用于测试


```python
print "output 2012 data for test..."
capital = pd.read_csv("../data/nyiso/all/capital.csv")
#capital[capital.timestamp < pd.to_datetime('2013-01-01')].to_csv('load2012.csv', index=False)
capital[capital.timestamp < '2013-01-01'].to_csv('load2012.csv', index=False)
csvs = []
for file in os.listdir("../data/wunderground/kalb"):
    if file.startswith("2012"):
        csvs.append(file)
print csvs
fout=open("weather2012.csv","a")

# 写入整个文件:
for line in open("../data/wunderground/kalb/"+csvs[0]):
    fout.write(line)
# 跳过头部:
for file in csvs[1:]:
    f = open("../data/wunderground/kalb/"+file)
    f.next()
    for line in f:
         fout.write(line)
    f.close()
fout.close()
```

```
output 2012 data for test...
```


## 下载NYISO的预测结果
NYISO会公布一个 "day-ahead" 预测数据（每次提前一天他们都会预测下一条的用电需求状况）。 如果咱们能够比公司做的预测系统效果还要好，那妥妥的表示咱们的模型有比较好的效果。 我们先把它在 2014-2016 年的预测结果下载下来，以便最后比对。

网址如下: http://www.nyiso.com/public/markets_operations/market_data/custom_report/index.jsp?report=load_forecast


```python
nyiso_forecast = pd.read_csv('../data/nyiso_dayahead_forecasts/forecast_2014_2016.csv')
```


```python
len(nyiso_forecast)
```

```
211442
```

```python
nyiso_forecast.columns = ['timestamp', 'zone', 'forecast', 'gmt']
```


OK，到这里，我们已经有实际的数据，而且是已经按照区域切分好了，还有 nyiso 公司的预测数据。

下面，我们就要准备一些特征了，首先，是天气特征。
