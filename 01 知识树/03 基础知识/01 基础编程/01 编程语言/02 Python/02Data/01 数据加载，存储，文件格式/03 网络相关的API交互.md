
# 6.3 Interacting with Web APIs (网络相关的API交互)

很多网站都有公开的API，通过JSON等格式提供数据流。有很多方法可以访问这些API，这里推荐一个易用的requests包。

找到github里pandas最新的30个issues，制作一个GET HTTP request, 通过使用requests包：


```python
import pandas as pd
import numpy as np
```


```python
import requests
```


```python
url = 'https://api.github.com/repos/pandas-dev/pandas/issues'
```


```python
resp = requests.get(url)
```


```python
resp
```




    <Response [200]>



response的json方法能返回一个dict，包含可以解析为python object的JSON：


```python
data = resp.json()
data[0]['title']
```




    'Optimize data type'




```python
data[0]
```




    {'assignee': None,
     'assignees': [],
     'author_association': 'NONE',
     'body': 'Hi guys, i\'m user of mysql\r\nwe have an "function" PROCEDURE ANALYSE\r\nhttps://dev.mysql.com/doc/refman/5.5/en/procedure-analyse.html\r\n\r\nit get all "dataframe" and show what\'s the best "dtype", could we do something like it in Pandas?\r\n\r\nthanks!',
     'closed_at': None,
     'comments': 1,
     'comments_url': 'https://api.github.com/repos/pandas-dev/pandas/issues/18272/comments',
     'created_at': '2017-11-13T22:51:32Z',
     'events_url': 'https://api.github.com/repos/pandas-dev/pandas/issues/18272/events',
     'html_url': 'https://github.com/pandas-dev/pandas/issues/18272',
     'id': 273606786,
     'labels': [],
     'labels_url': 'https://api.github.com/repos/pandas-dev/pandas/issues/18272/labels{/name}',
     'locked': False,
     'milestone': None,
     'number': 18272,
     'repository_url': 'https://api.github.com/repos/pandas-dev/pandas',
     'state': 'open',
     'title': 'Optimize data type',
     'updated_at': '2017-11-13T22:57:27Z',
     'url': 'https://api.github.com/repos/pandas-dev/pandas/issues/18272',
     'user': {'avatar_url': 'https://avatars0.githubusercontent.com/u/2468782?v=4',
      'events_url': 'https://api.github.com/users/rspadim/events{/privacy}',
      'followers_url': 'https://api.github.com/users/rspadim/followers',
      'following_url': 'https://api.github.com/users/rspadim/following{/other_user}',
      'gists_url': 'https://api.github.com/users/rspadim/gists{/gist_id}',
      'gravatar_id': '',
      'html_url': 'https://github.com/rspadim',
      'id': 2468782,
      'login': 'rspadim',
      'organizations_url': 'https://api.github.com/users/rspadim/orgs',
      'received_events_url': 'https://api.github.com/users/rspadim/received_events',
      'repos_url': 'https://api.github.com/users/rspadim/repos',
      'site_admin': False,
      'starred_url': 'https://api.github.com/users/rspadim/starred{/owner}{/repo}',
      'subscriptions_url': 'https://api.github.com/users/rspadim/subscriptions',
      'type': 'User',
      'url': 'https://api.github.com/users/rspadim'} }



data中的每一个元素都是一个dict，这个dict就是在github上找到的issue页面上的信息。我们可以把data传给DataFrame并提取感兴趣的部分：


```python
issues = pd.DataFrame(data, columns=['number', 'title', 
                                    'labels', 'state'])
issues
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>number</th>
      <th>title</th>
      <th>labels</th>
      <th>state</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>18272</td>
      <td>Optimize data type</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>1</th>
      <td>18271</td>
      <td>BUG: Series.rank(pct=True).max() != 1 for a la...</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>2</th>
      <td>18270</td>
      <td>(Series|DataFrame) datetimelike ops</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>3</th>
      <td>18268</td>
      <td>DOC: update Series.combine/DataFrame.combine d...</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>4</th>
      <td>18266</td>
      <td>DOC: updated .combine_first doc strings</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>5</th>
      <td>18265</td>
      <td>Calling DataFrame.stack on an out-of-order col...</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>6</th>
      <td>18264</td>
      <td>cleaned up imports</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>7</th>
      <td>18263</td>
      <td>Tslibs offsets paramd</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>8</th>
      <td>18262</td>
      <td>DEPR: let's deprecate</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>9</th>
      <td>18258</td>
      <td>DEPR: deprecate (Sparse)Series.from_array</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>10</th>
      <td>18255</td>
      <td>ENH/PERF: Add cache='infer' to to_datetime</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>11</th>
      <td>18250</td>
      <td>Categorical.replace() unexpectedly returns non...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>12</th>
      <td>18246</td>
      <td>pandas.MultiIndex.reorder_levels has no inplac...</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>13</th>
      <td>18245</td>
      <td>TST: test tz-aware DatetimeIndex as separate m...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>14</th>
      <td>18244</td>
      <td>RLS 0.21.1</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>15</th>
      <td>18243</td>
      <td>DEPR: deprecate .ftypes, get_ftype_counts</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>16</th>
      <td>18242</td>
      <td>CLN: Remove days, seconds and microseconds pro...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>17</th>
      <td>18241</td>
      <td>DEPS: drop 2.7 support</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18238</td>
      <td>BUG: Fix filter method so that accepts byte an...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>19</th>
      <td>18237</td>
      <td>Deprecate Series.asobject, Index.asobject, ren...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>20</th>
      <td>18236</td>
      <td>df.plot() very slow compared to explicit matpl...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>21</th>
      <td>18235</td>
      <td>Quarter.onOffset looks fishy</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>22</th>
      <td>18231</td>
      <td>Reduce copying of input data on Series constru...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>23</th>
      <td>18226</td>
      <td>Patch __init__ to prevent passing invalid kwds</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>24</th>
      <td>18222</td>
      <td>DataFrame.plot() produces incorrect legend lab...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>25</th>
      <td>18220</td>
      <td>DataFrame.groupy renames columns when given a ...</td>
      <td>[]</td>
      <td>open</td>
    </tr>
    <tr>
      <th>26</th>
      <td>18217</td>
      <td>Deprecate Index.summary</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>27</th>
      <td>18216</td>
      <td>Pass kwargs from read_parquet() to the underly...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>28</th>
      <td>18215</td>
      <td>DOC/DEPR: ensure that @deprecated functions ha...</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
    <tr>
      <th>29</th>
      <td>18213</td>
      <td>Deprecate Series.from_array ?</td>
      <td>[{'url': 'https://api.github.com/repos/pandas-...</td>
      <td>open</td>
    </tr>
  </tbody>
</table>
</div>



通过一些体力活，我们可以构建一些高层级的界面，让web API直接返回DataFrame格式，以便于分析。


```python

```
