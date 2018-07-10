
# 14.2 MovieLens 1M Dataset（MovieLens 1M数据集）

这个数据集是电影评分数据：包括电影评分，电影元数据（风格类型，年代）以及关于用户的人口统计学数据（年龄，邮编，性别，职业等）。

MovieLens 1M数据集含有来自6000名用户对4000部电影的100万条评分数据。分为三个表：评分，用户信息，电影信息。这些数据都是dat文件格式，可以通过pandas.read_table将各个表分别读到一个pandas DataFrame对象中：


```python
import pandas as pd
```


```python
# Make display smaller
pd.options.display.max_rows = 10
```


```python
unames = ['user_id', 'gender', 'age', 'occupation', 'zip']
users = pd.read_table('../datasets/movielens/users.dat', sep='::', 
                      header=None, names=unames)
```

    /Users/xu/anaconda/envs/py35/lib/python3.5/site-packages/ipykernel/__main__.py:3: ParserWarning: Falling back to the 'python' engine because the 'c' engine does not support regex separators (separators > 1 char and different from '\s+' are interpreted as regex); you can avoid this warning by specifying engine='python'.
      app.launch_new_instance()
    

因为sep='::'有点像是正则表达式，于是有了上面的错误。在这个[帖子](https://stackoverflow.com/questions/27301477/python-file-path-failing-in-pycharm-regex-confusion)找到了解决方法，设置engine为python即可。

Looks like on Python 2.7 Pandas just doesn't handle separators that look regexish. The initial "error" can be worked around by adding engine='python' as a named parameter in the call, as suggested in the warning.




```python
users = pd.read_table('../datasets/movielens/users.dat', sep='::', 
                      header=None, names=unames, engine='python')
```


```python
rnames = ['user_id', 'movie_id', 'rating', 'timestamp']
ratings = pd.read_table('../datasets/movielens/ratings.dat', sep='::', header=None, names=rnames, engine='python')
```


```python
mnames = ['movie_id', 'title', 'genres']
movies = pd.read_table('../datasets/movielens/movies.dat', sep='::', header=None, names=mnames, engine='python')
```

加载前几行验证一下数据加载工作是否顺利


```python
users[:5]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>gender</th>
      <th>age</th>
      <th>occupation</th>
      <th>zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>F</td>
      <td>1</td>
      <td>10</td>
      <td>48067</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>M</td>
      <td>56</td>
      <td>16</td>
      <td>70072</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>M</td>
      <td>25</td>
      <td>15</td>
      <td>55117</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>M</td>
      <td>45</td>
      <td>7</td>
      <td>02460</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>M</td>
      <td>25</td>
      <td>20</td>
      <td>55455</td>
    </tr>
  </tbody>
</table>
</div>




```python
ratings[:5]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>movie_id</th>
      <th>rating</th>
      <th>timestamp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1193</td>
      <td>5</td>
      <td>978300760</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>661</td>
      <td>3</td>
      <td>978302109</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>914</td>
      <td>3</td>
      <td>978301968</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>3408</td>
      <td>4</td>
      <td>978300275</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2355</td>
      <td>5</td>
      <td>978824291</td>
    </tr>
  </tbody>
</table>
</div>




```python
movies[:5]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>title</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Toy Story (1995)</td>
      <td>Animation|Children's|Comedy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Jumanji (1995)</td>
      <td>Adventure|Children's|Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Grumpier Old Men (1995)</td>
      <td>Comedy|Romance</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Waiting to Exhale (1995)</td>
      <td>Comedy|Drama</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Father of the Bride Part II (1995)</td>
      <td>Comedy</td>
    </tr>
  </tbody>
</table>
</div>



注意，年龄和职业是以编码形式给出的，它们的具体含义请参考改数据集的REAMDE文件。分析散布在三个表中的数据不是一件轻松的事情。假设我们想要根据性别和年龄来计算某部电影的平均得分，如果将所有的数据都合并到一个表中的话，问题就简单多了。我们先用pandas的merge函数将ratings和users合并到一起，然后再将movies也合并进去。pandas会根据列名的重叠情况推断出哪些列是合并（或连接）键：


```python
data = pd.merge(pd.merge(ratings, users), movies)
```


```python
data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_id</th>
      <th>movie_id</th>
      <th>rating</th>
      <th>timestamp</th>
      <th>gender</th>
      <th>age</th>
      <th>occupation</th>
      <th>zip</th>
      <th>title</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1193</td>
      <td>5</td>
      <td>978300760</td>
      <td>F</td>
      <td>1</td>
      <td>10</td>
      <td>48067</td>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1193</td>
      <td>5</td>
      <td>978298413</td>
      <td>M</td>
      <td>56</td>
      <td>16</td>
      <td>70072</td>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12</td>
      <td>1193</td>
      <td>4</td>
      <td>978220179</td>
      <td>M</td>
      <td>25</td>
      <td>12</td>
      <td>32793</td>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15</td>
      <td>1193</td>
      <td>4</td>
      <td>978199279</td>
      <td>M</td>
      <td>25</td>
      <td>7</td>
      <td>22903</td>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>4</th>
      <td>17</td>
      <td>1193</td>
      <td>5</td>
      <td>978158471</td>
      <td>M</td>
      <td>50</td>
      <td>1</td>
      <td>95350</td>
      <td>One Flew Over the Cuckoo's Nest (1975)</td>
      <td>Drama</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.iloc[0]
```




    user_id                                            1
    movie_id                                        1193
    rating                                             5
    timestamp                                  978300760
    gender                                             F
    age                                                1
    occupation                                        10
    zip                                            48067
    title         One Flew Over the Cuckoo's Nest (1975)
    genres                                         Drama
    Name: 0, dtype: object



现在，只要稍微熟悉一下pandas，就能轻松地根据任意个用户或电影属性对评分数据进行聚合操作了。为了按性别计算每部电影的平均得分，我们可以使用pivot_table方法：


```python
mean_ratings = data.pivot_table('rating', index='title',
                                columns='gender', aggfunc='mean')
```


```python
mean_ratings[:5]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>gender</th>
      <th>F</th>
      <th>M</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>$1,000,000 Duck (1971)</th>
      <td>3.375000</td>
      <td>2.761905</td>
    </tr>
    <tr>
      <th>'Night Mother (1986)</th>
      <td>3.388889</td>
      <td>3.352941</td>
    </tr>
    <tr>
      <th>'Til There Was You (1997)</th>
      <td>2.675676</td>
      <td>2.733333</td>
    </tr>
    <tr>
      <th>'burbs, The (1989)</th>
      <td>2.793478</td>
      <td>2.962085</td>
    </tr>
    <tr>
      <th>...And Justice for All (1979)</th>
      <td>3.828571</td>
      <td>3.689024</td>
    </tr>
  </tbody>
</table>
</div>



该操作产生了另一个DataFrame，其内容为电影平均得分，行标为电影名称，列表为性别。现在，我们打算过滤掉评分数据不够250条的电影（这个数字可以自己设定）。为了达到这个目的，我们先对title进行分组，然后利用size()得到一个含有各电影分组大小的Series对象：


```python
ratings_by_title = data.groupby('title').size()
```


```python
ratings_by_title[:10]
```




    title
    $1,000,000 Duck (1971)                37
    'Night Mother (1986)                  70
    'Til There Was You (1997)             52
    'burbs, The (1989)                   303
    ...And Justice for All (1979)        199
    1-900 (1994)                           2
    10 Things I Hate About You (1999)    700
    101 Dalmatians (1961)                565
    101 Dalmatians (1996)                364
    12 Angry Men (1957)                  616
    dtype: int64




```python
active_titles = ratings_by_title.index[ratings_by_title >= 250]
```


```python
print(active_titles)
```

    Index([''burbs, The (1989)', '10 Things I Hate About You (1999)',
           '101 Dalmatians (1961)', '101 Dalmatians (1996)', '12 Angry Men (1957)',
           '13th Warrior, The (1999)', '2 Days in the Valley (1996)',
           '20,000 Leagues Under the Sea (1954)', '2001: A Space Odyssey (1968)',
           '2010 (1984)',
           ...
           'X-Men (2000)', 'Year of Living Dangerously (1982)',
           'Yellow Submarine (1968)', 'You've Got Mail (1998)',
           'Young Frankenstein (1974)', 'Young Guns (1988)',
           'Young Guns II (1990)', 'Young Sherlock Holmes (1985)',
           'Zero Effect (1998)', 'eXistenZ (1999)'],
          dtype='object', name='title', length=1216)
    

上面的active_titles中的电影，都是评论是大于250条以上的。我们可以用这些标题作为索引，从mean_ratings中选出这些评论大于250条的电影：


```python
mean_ratings = mean_ratings.loc[active_titles]
mean_ratings
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>gender</th>
      <th>F</th>
      <th>M</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>'burbs, The (1989)</th>
      <td>2.793478</td>
      <td>2.962085</td>
    </tr>
    <tr>
      <th>10 Things I Hate About You (1999)</th>
      <td>3.646552</td>
      <td>3.311966</td>
    </tr>
    <tr>
      <th>101 Dalmatians (1961)</th>
      <td>3.791444</td>
      <td>3.500000</td>
    </tr>
    <tr>
      <th>101 Dalmatians (1996)</th>
      <td>3.240000</td>
      <td>2.911215</td>
    </tr>
    <tr>
      <th>12 Angry Men (1957)</th>
      <td>4.184397</td>
      <td>4.328421</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Young Guns (1988)</th>
      <td>3.371795</td>
      <td>3.425620</td>
    </tr>
    <tr>
      <th>Young Guns II (1990)</th>
      <td>2.934783</td>
      <td>2.904025</td>
    </tr>
    <tr>
      <th>Young Sherlock Holmes (1985)</th>
      <td>3.514706</td>
      <td>3.363344</td>
    </tr>
    <tr>
      <th>Zero Effect (1998)</th>
      <td>3.864407</td>
      <td>3.723140</td>
    </tr>
    <tr>
      <th>eXistenZ (1999)</th>
      <td>3.098592</td>
      <td>3.289086</td>
    </tr>
  </tbody>
</table>
<p>1216 rows × 2 columns</p>
</div>



想要查看女性观众喜欢的电影，可以按F列进行降序操作：


```python
top_female_ratings = mean_ratings.sort_values(by='F', ascending=False)
top_female_ratings[:10]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>gender</th>
      <th>F</th>
      <th>M</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Close Shave, A (1995)</th>
      <td>4.644444</td>
      <td>4.473795</td>
    </tr>
    <tr>
      <th>Wrong Trousers, The (1993)</th>
      <td>4.588235</td>
      <td>4.478261</td>
    </tr>
    <tr>
      <th>Sunset Blvd. (a.k.a. Sunset Boulevard) (1950)</th>
      <td>4.572650</td>
      <td>4.464589</td>
    </tr>
    <tr>
      <th>Wallace &amp; Gromit: The Best of Aardman Animation (1996)</th>
      <td>4.563107</td>
      <td>4.385075</td>
    </tr>
    <tr>
      <th>Schindler's List (1993)</th>
      <td>4.562602</td>
      <td>4.491415</td>
    </tr>
    <tr>
      <th>Shawshank Redemption, The (1994)</th>
      <td>4.539075</td>
      <td>4.560625</td>
    </tr>
    <tr>
      <th>Grand Day Out, A (1992)</th>
      <td>4.537879</td>
      <td>4.293255</td>
    </tr>
    <tr>
      <th>To Kill a Mockingbird (1962)</th>
      <td>4.536667</td>
      <td>4.372611</td>
    </tr>
    <tr>
      <th>Creature Comforts (1990)</th>
      <td>4.513889</td>
      <td>4.272277</td>
    </tr>
    <tr>
      <th>Usual Suspects, The (1995)</th>
      <td>4.513317</td>
      <td>4.518248</td>
    </tr>
  </tbody>
</table>
</div>



# 1 Measuring Rating Disagreement（计算评分分歧）

假设我们想要找出男性和女性观众分歧最大的电影。一个办法是给mean_ratings加上一个用于存放平均得分之差的列，并对其进行排序：


```python
mean_ratings['diff'] = mean_ratings['M'] - mean_ratings['F']
```

按‘diff’排序即可得到分歧最大且女性观众更喜欢的电影：


```python
sorted_by_diff = mean_ratings.sort_values(by='diff')
sorted_by_diff[:15]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>gender</th>
      <th>F</th>
      <th>M</th>
      <th>diff</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Dirty Dancing (1987)</th>
      <td>3.790378</td>
      <td>2.959596</td>
      <td>-0.830782</td>
    </tr>
    <tr>
      <th>Jumpin' Jack Flash (1986)</th>
      <td>3.254717</td>
      <td>2.578358</td>
      <td>-0.676359</td>
    </tr>
    <tr>
      <th>Grease (1978)</th>
      <td>3.975265</td>
      <td>3.367041</td>
      <td>-0.608224</td>
    </tr>
    <tr>
      <th>Little Women (1994)</th>
      <td>3.870588</td>
      <td>3.321739</td>
      <td>-0.548849</td>
    </tr>
    <tr>
      <th>Steel Magnolias (1989)</th>
      <td>3.901734</td>
      <td>3.365957</td>
      <td>-0.535777</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>French Kiss (1995)</th>
      <td>3.535714</td>
      <td>3.056962</td>
      <td>-0.478752</td>
    </tr>
    <tr>
      <th>Little Shop of Horrors, The (1960)</th>
      <td>3.650000</td>
      <td>3.179688</td>
      <td>-0.470312</td>
    </tr>
    <tr>
      <th>Guys and Dolls (1955)</th>
      <td>4.051724</td>
      <td>3.583333</td>
      <td>-0.468391</td>
    </tr>
    <tr>
      <th>Mary Poppins (1964)</th>
      <td>4.197740</td>
      <td>3.730594</td>
      <td>-0.467147</td>
    </tr>
    <tr>
      <th>Patch Adams (1998)</th>
      <td>3.473282</td>
      <td>3.008746</td>
      <td>-0.464536</td>
    </tr>
  </tbody>
</table>
<p>15 rows × 3 columns</p>
</div>



对行进行反序操作，并取出前15行，得到的则是男性更喜欢，而女性评价较低的电影：


```python
# Reverse order of rows, take first 10 rows
sorted_by_diff[::-1][:10]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>gender</th>
      <th>F</th>
      <th>M</th>
      <th>diff</th>
    </tr>
    <tr>
      <th>title</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Good, The Bad and The Ugly, The (1966)</th>
      <td>3.494949</td>
      <td>4.221300</td>
      <td>0.726351</td>
    </tr>
    <tr>
      <th>Kentucky Fried Movie, The (1977)</th>
      <td>2.878788</td>
      <td>3.555147</td>
      <td>0.676359</td>
    </tr>
    <tr>
      <th>Dumb &amp; Dumber (1994)</th>
      <td>2.697987</td>
      <td>3.336595</td>
      <td>0.638608</td>
    </tr>
    <tr>
      <th>Longest Day, The (1962)</th>
      <td>3.411765</td>
      <td>4.031447</td>
      <td>0.619682</td>
    </tr>
    <tr>
      <th>Cable Guy, The (1996)</th>
      <td>2.250000</td>
      <td>2.863787</td>
      <td>0.613787</td>
    </tr>
    <tr>
      <th>Evil Dead II (Dead By Dawn) (1987)</th>
      <td>3.297297</td>
      <td>3.909283</td>
      <td>0.611985</td>
    </tr>
    <tr>
      <th>Hidden, The (1987)</th>
      <td>3.137931</td>
      <td>3.745098</td>
      <td>0.607167</td>
    </tr>
    <tr>
      <th>Rocky III (1982)</th>
      <td>2.361702</td>
      <td>2.943503</td>
      <td>0.581801</td>
    </tr>
    <tr>
      <th>Caddyshack (1980)</th>
      <td>3.396135</td>
      <td>3.969737</td>
      <td>0.573602</td>
    </tr>
    <tr>
      <th>For a Few Dollars More (1965)</th>
      <td>3.409091</td>
      <td>3.953795</td>
      <td>0.544704</td>
    </tr>
  </tbody>
</table>
</div>



如果只是想要找出分歧最大的电影（不考虑性别因素），则可以计算得分数据的方差或标准差：


```python
# 根据电影名称分组的得分数据的标准差
rating_std_by_title = data.groupby('title')['rating'].std()
```


```python
# 根据active_titles进行过滤
rating_std_by_title = rating_std_by_title.loc[active_titles]
```


```python
# Order Series by value in descending order
rating_std_by_title.sort_values(ascending=False)[:10]
```




    title
    Dumb & Dumber (1994)                     1.321333
    Blair Witch Project, The (1999)          1.316368
    Natural Born Killers (1994)              1.307198
    Tank Girl (1995)                         1.277695
    Rocky Horror Picture Show, The (1975)    1.260177
    Eyes Wide Shut (1999)                    1.259624
    Evita (1996)                             1.253631
    Billy Madison (1995)                     1.249970
    Fear and Loathing in Las Vegas (1998)    1.246408
    Bicentennial Man (1999)                  1.245533
    Name: rating, dtype: float64



这里我们注意到，电影分类是以竖线`|`分割的字符串形式给出的。如果想对不同的电影分类进行分析的话，就需要先将其转换成更有用的形式才行。
