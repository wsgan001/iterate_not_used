
这里，我们将要手机天气数据来做天气特征。


## 采集天气数据

我们主要是遍历和纽约州每个区关联的所有天气站，使用 Weather Underground API 同步历史上的实时天气信息。


```python
import pandas as pd
import numpy as np
import time
import random
import cPickle as pickle
import os
```

第一步的时候，我们已经把地区与天气站的映射存到了文件中，这里，我们要把它加载进来使用。

```python
weather_dict = pickle.load(open('weather_dict.pkl','rb'))
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

列出气象站：

```python
airports = [i[0] for i in weather_dict.values()]
#去重
airports = list(set(airports))
airports
```

```
['kalb', 'kpou', 'kroc', 'kpbg', 'kbuf', 'kjfk', 'ksyr', 'krme', 'klga']
```



```python
def write_daily_weather_data(airport, dates):
    '''把2个python list（天气和日期）整合成一个CSV文件

    整合好的CSV文件有以下的字段:

    timeest | temperaturef | dewpointf | humidity | sealevelpressurein | visibilitymph | winddirection | windspeedkmh | gustspeedmph

        | precipitationmm | events | conditions | winddirdegrees | dateutc
    '''
    for d in dates:
        try:
            # 这个地方，网页也可以 read_csv 吗？
            df0 = pd.read_csv('https://www.wunderground.com/history/airport/{0}/{1}/{2}/{3}/DailyHistory.html?format=1'\
                                 .format(airport, d.year, d.month, d.day))
            cols = df0.columns

            df0.columns = [col.lower().replace(' ','').replace('<br/>', '').replace('/','') for col in cols]
            #print df0.columns
            df0.dateutc = df0.dateutc.apply(lambda x: pd.to_datetime(x.replace('<br />', '')))

            df0.gustspeedkmh = df0.gustspeedkmh.replace('-', 0)
            df0.windspeedkmh = df0.windspeedkmh.replace('Calm', 0)
            df0.precipitationmm = df0.precipitationmm.replace('NaN', 0)
            df0.events = df0.events.replace('NaN', 0)

            filepath = '../data/wunderground/'+ airport +'/' + str(d.date()).replace('-','') + '.csv'
            print filepath
            df0.to_csv(filepath, index=False)

            t = 3
            time.sleep(t)

            if type(df0.dateutc[0]) == pd.tslib.Timestamp:
                continue
            else:
                print "Something is wrong"
                break
        except:
            print "date ",d ," can't be downloaded!"
            continue

    print "Files for %s have been written" % airport
    return
```

<span style="color:red;">时间是一个非常重要的特征，因此 python 里面与 时间相关的函数一定要掌握，或者手边有可以查的表，要用到什么的时候自己去查。</span>

遍历气象站，导出天气文件


```python
dates = pd.date_range(pd.to_datetime('2001-05-01'), \
                       pd.to_datetime('2016-03-11'), freq='D')


for a in airports:
    write_daily_weather_data(a, dates)
```

```
../data/wunderground/kalb/20010501.csv
../data/wunderground/kalb/20010502.csv
../data/wunderground/kalb/20010503.csv
../data/wunderground/kalb/20010504.csv
../data/wunderground/kalb/20010505.csv
../data/wunderground/kalb/20010506.csv
../data/wunderground/kalb/20010507.csv
../data/wunderground/kalb/20010508.csv
../data/wunderground/kalb/20010509.csv
...此处略去非常多
../data/wunderground/kalb/20041204.csv
../data/wunderground/kalb/20041205.csv
../data/wunderground/kalb/20041206.csv
../data/wunderground/kalb/20041207.csv
../data/wunderground/kalb/20041208.csv
../data/wunderground/kalb/20041209.csv
../data/wunderground/kalb/20041210.csv
../data/wunderground/kalb/20041211.csv
../data/wunderground/kalb/20041212.csv
```

可见，存放下来的就是以气象站和日期命名的 csv 文件。

```python
dates = pd.date_range(pd.to_datetime('2012-07-03'), \
                       pd.to_datetime('2013-01-01'), freq='D')

write_daily_weather_data('kalb', dates)
```

```
../data/wunderground/kalb/20120703.csv
date  2012-07-04 00:00:00  can't be downloaded!
date  2012-07-05 00:00:00  can't be downloaded!
date  2012-07-06 00:00:00  can't be downloaded!
date  2012-07-07 00:00:00  can't be downloaded!
../data/wunderground/kalb/20120708.csv
../data/wunderground/kalb/20120709.csv
date  2012-07-10 00:00:00  can't be downloaded!
date  2012-07-11 00:00:00  can't be downloaded!
date  2012-07-12 00:00:00  can't be downloaded!
date  2012-07-13 00:00:00  can't be downloaded!
date  2012-07-14 00:00:00  can't be downloaded!
date  2012-07-15 00:00:00  can't be downloaded!
date  2012-07-16 00:00:00  can't be downloaded!
date  2012-07-17 00:00:00  can't be downloaded!
date  2012-07-18 00:00:00  can't be downloaded!
date  2012-07-19 00:00:00  can't be downloaded!
date  2012-07-20 00:00:00  can't be downloaded!
date  2012-07-21 00:00:00  can't be downloaded!
date  2012-07-22 00:00:00  can't be downloaded!
```


把相同天气站的数据合并起来：

```python

def combine_weather_data(airport):
    '''Combine the weather data for each day at an airport into one combined csv'''
    csvs = []
    for file in os.listdir("../data/wunderground/"+airport+"/"):
        if file.endswith(".csv"):
            csvs.append(file)

    fout=open("../data/wunderground/"+airport+"_all.csv","a")

    # 第一个文件完整地写进去:
    for line in open("../data/wunderground/"+airport+"/"+csvs[0]):
        fout.write(line)
    # 后续的文件，去掉头部信息:
    for file in csvs[1:]:
        f = open("../data/wunderground/"+airport+"/"+file)
        f.next() # 跳过header
        for line in f:
             fout.write(line)
        f.close()
    fout.close()
    print "Files for %s have been combined" % airport
```


```python
for a in airports:
    combine_weather_data(a)
```
