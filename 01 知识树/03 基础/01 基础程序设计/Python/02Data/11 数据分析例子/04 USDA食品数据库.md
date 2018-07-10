
# 14.4 USDA Food Database（美国农业部食品数据库）

这个数据是关于食物营养成分的。存储格式是JSON，看起来像这样：
```
{
    "id": 21441,
    "description": "KENTUCKY FRIED CHICKEN, Fried Chicken, EXTRA CRISPY, Wing, meat and skin with breading",
    "tags": ["KFC"],
    "manufacturer": "Kentucky Fried Chicken",
    "group": "Fast Foods",
    "portions": [
        { "amount": 1,
          "unit": "wing, with skin",
          "grams": 68.0
        }
        ...
      ],
    "nutrients": [
      { "value": 20.8,
        "units": "g",
        "description": "Protein",
        "group": "Composition"
      },
      ...
     ]
}
```

每种食物都有一系列特征，其中有两个list，protions和nutrients。我们必须把这样的数据进行处理，方便之后的分析。

这里使用python内建的json模块：


```python
import pandas as pd
import numpy as np
import json
```


```python
pd.options.display.max_rows = 10
```


```python
db = json.load(open('../datasets/usda_food/database.json'))
len(db)
```




    6636




```python
db[0].keys()
```




    dict_keys(['manufacturer', 'description', 'group', 'id', 'tags', 'nutrients', 'portions'])




```python
db[0]['nutrients'][0]
```




    {'description': 'Protein',
     'group': 'Composition',
     'units': 'g',
     'value': 25.18}




```python
nutrients = pd.DataFrame(db[0]['nutrients'])
nutrients
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
      <th>description</th>
      <th>group</th>
      <th>units</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Protein</td>
      <td>Composition</td>
      <td>g</td>
      <td>25.180</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Total lipid (fat)</td>
      <td>Composition</td>
      <td>g</td>
      <td>29.200</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Carbohydrate, by difference</td>
      <td>Composition</td>
      <td>g</td>
      <td>3.060</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ash</td>
      <td>Other</td>
      <td>g</td>
      <td>3.280</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Energy</td>
      <td>Energy</td>
      <td>kcal</td>
      <td>376.000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>157</th>
      <td>Serine</td>
      <td>Amino Acids</td>
      <td>g</td>
      <td>1.472</td>
    </tr>
    <tr>
      <th>158</th>
      <td>Cholesterol</td>
      <td>Other</td>
      <td>mg</td>
      <td>93.000</td>
    </tr>
    <tr>
      <th>159</th>
      <td>Fatty acids, total saturated</td>
      <td>Other</td>
      <td>g</td>
      <td>18.584</td>
    </tr>
    <tr>
      <th>160</th>
      <td>Fatty acids, total monounsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>8.275</td>
    </tr>
    <tr>
      <th>161</th>
      <td>Fatty acids, total polyunsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.830</td>
    </tr>
  </tbody>
</table>
<p>162 rows × 4 columns</p>
</div>



当把由字典组成的list转换为DataFrame的时候，我们可以吹创业提取的list部分。这里我们提取食品名，群（group），ID，制造商：


```python
info_keys = ['description', 'group', 'id', 'manufacturer']
info = pd.DataFrame(db, columns=info_keys)
info[:5]
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
      <th>description</th>
      <th>group</th>
      <th>id</th>
      <th>manufacturer</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cheese, caraway</td>
      <td>Dairy and Egg Products</td>
      <td>1008</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cheese, cheddar</td>
      <td>Dairy and Egg Products</td>
      <td>1009</td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>Cheese, edam</td>
      <td>Dairy and Egg Products</td>
      <td>1018</td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>Cheese, feta</td>
      <td>Dairy and Egg Products</td>
      <td>1019</td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>Cheese, mozzarella, part skim milk</td>
      <td>Dairy and Egg Products</td>
      <td>1028</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
info.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 6636 entries, 0 to 6635
    Data columns (total 4 columns):
    description     6636 non-null object
    group           6636 non-null object
    id              6636 non-null int64
    manufacturer    5195 non-null object
    dtypes: int64(1), object(3)
    memory usage: 207.5+ KB


我们可以看到食物群的分布，使用value_counts:


```python
pd.value_counts(info.group)[:10]
```




    Vegetables and Vegetable Products    812
    Beef Products                        618
    Baked Products                       496
    Breakfast Cereals                    403
    Legumes and Legume Products          365
    Fast Foods                           365
    Lamb, Veal, and Game Products        345
    Sweets                               341
    Pork Products                        328
    Fruits and Fruit Juices              328
    Name: group, dtype: int64



这里我们对所有的nutrient数据做一些分析，把每种食物的nutrient部分组合成一个大表格。首先，把每个食物的nutrient列表变为DataFrame，添加一列为id，然后把id添加到DataFrame中，接着使用concat联结到一起：


```python
# 先创建一个空DataFrame用来保存最后的结果
# 这部分代码运行时间较长，请耐心等待
nutrients_all = pd.DataFrame()

for food in db:
    nutrients = pd.DataFrame(food['nutrients'])
    nutrients['id'] = food['id']
    nutrients_all = nutrients_all.append(nutrients, ignore_index=True)
```

> 译者：虽然作者在书中说了用concat联结在一起，但我实际测试后，这个concat的方法非常耗时，用时几乎是append方法的两倍，所以上面的代码中使用了append方法。

一切正常的话出来的效果是这样的：


```python
nutrients_all
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
      <th>description</th>
      <th>group</th>
      <th>units</th>
      <th>value</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Protein</td>
      <td>Composition</td>
      <td>g</td>
      <td>25.180</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Total lipid (fat)</td>
      <td>Composition</td>
      <td>g</td>
      <td>29.200</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Carbohydrate, by difference</td>
      <td>Composition</td>
      <td>g</td>
      <td>3.060</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ash</td>
      <td>Other</td>
      <td>g</td>
      <td>3.280</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Energy</td>
      <td>Energy</td>
      <td>kcal</td>
      <td>376.000</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>389350</th>
      <td>Vitamin B-12, added</td>
      <td>Vitamins</td>
      <td>mcg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389351</th>
      <td>Cholesterol</td>
      <td>Other</td>
      <td>mg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389352</th>
      <td>Fatty acids, total saturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.072</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389353</th>
      <td>Fatty acids, total monounsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.028</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389354</th>
      <td>Fatty acids, total polyunsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.041</td>
      <td>43546</td>
    </tr>
  </tbody>
</table>
<p>389355 rows × 5 columns</p>
</div>



这个DataFrame中有一些重复的部分，看一下有多少重复的行：


```python
nutrients_all.duplicated().sum() # number of duplicates
```




    14179



把重复的部分去掉：


```python
nutrients_all = nutrients_all.drop_duplicates()
nutrients_all
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
      <th>description</th>
      <th>group</th>
      <th>units</th>
      <th>value</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Protein</td>
      <td>Composition</td>
      <td>g</td>
      <td>25.180</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Total lipid (fat)</td>
      <td>Composition</td>
      <td>g</td>
      <td>29.200</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Carbohydrate, by difference</td>
      <td>Composition</td>
      <td>g</td>
      <td>3.060</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ash</td>
      <td>Other</td>
      <td>g</td>
      <td>3.280</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Energy</td>
      <td>Energy</td>
      <td>kcal</td>
      <td>376.000</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>389350</th>
      <td>Vitamin B-12, added</td>
      <td>Vitamins</td>
      <td>mcg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389351</th>
      <td>Cholesterol</td>
      <td>Other</td>
      <td>mg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389352</th>
      <td>Fatty acids, total saturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.072</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389353</th>
      <td>Fatty acids, total monounsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.028</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389354</th>
      <td>Fatty acids, total polyunsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.041</td>
      <td>43546</td>
    </tr>
  </tbody>
</table>
<p>375176 rows × 5 columns</p>
</div>



为了与info_keys中的group和descripton区别开，我们把列名更改一下：


```python
col_mapping = {'description': 'food',
               'group': 'fgroup'}
```


```python
info = info.rename(columns=col_mapping, copy=False)
info.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 6636 entries, 0 to 6635
    Data columns (total 4 columns):
    food            6636 non-null object
    fgroup          6636 non-null object
    id              6636 non-null int64
    manufacturer    5195 non-null object
    dtypes: int64(1), object(3)
    memory usage: 207.5+ KB



```python
col_mapping = {'description' : 'nutrient',
               'group': 'nutgroup'}
```


```python
nutrients_all = nutrients_all.rename(columns=col_mapping, copy=False)
nutrients_all
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
      <th>nutrient</th>
      <th>nutgroup</th>
      <th>units</th>
      <th>value</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Protein</td>
      <td>Composition</td>
      <td>g</td>
      <td>25.180</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Total lipid (fat)</td>
      <td>Composition</td>
      <td>g</td>
      <td>29.200</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Carbohydrate, by difference</td>
      <td>Composition</td>
      <td>g</td>
      <td>3.060</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ash</td>
      <td>Other</td>
      <td>g</td>
      <td>3.280</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Energy</td>
      <td>Energy</td>
      <td>kcal</td>
      <td>376.000</td>
      <td>1008</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>389350</th>
      <td>Vitamin B-12, added</td>
      <td>Vitamins</td>
      <td>mcg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389351</th>
      <td>Cholesterol</td>
      <td>Other</td>
      <td>mg</td>
      <td>0.000</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389352</th>
      <td>Fatty acids, total saturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.072</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389353</th>
      <td>Fatty acids, total monounsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.028</td>
      <td>43546</td>
    </tr>
    <tr>
      <th>389354</th>
      <td>Fatty acids, total polyunsaturated</td>
      <td>Other</td>
      <td>g</td>
      <td>0.041</td>
      <td>43546</td>
    </tr>
  </tbody>
</table>
<p>375176 rows × 5 columns</p>
</div>



上面所有步骤结束后，我们可以把info和nutrients_all合并（merge）：


```python
ndata = pd.merge(nutrients_all, info, on='id', how='outer')
ndata.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 375176 entries, 0 to 375175
    Data columns (total 8 columns):
    nutrient        375176 non-null object
    nutgroup        375176 non-null object
    units           375176 non-null object
    value           375176 non-null float64
    id              375176 non-null int64
    food            375176 non-null object
    fgroup          375176 non-null object
    manufacturer    293054 non-null object
    dtypes: float64(1), int64(1), object(6)
    memory usage: 25.8+ MB



```python
ndata.iloc[30000]
```




    nutrient                                       Glycine
    nutgroup                                   Amino Acids
    units                                                g
    value                                             0.04
    id                                                6158
    food            Soup, tomato bisque, canned, condensed
    fgroup                      Soups, Sauces, and Gravies
    manufacturer
    Name: 30000, dtype: object



我们可以对食物群（food group）和营养类型（nutrient type）分组后，对中位数进行绘图：


```python
result = ndata.groupby(['nutrient', 'fgroup'])['value'].quantile(0.5)
```


```python
%matplotlib inline
```


```python
result['Zinc, Zn'].sort_values().plot(kind='barh', figsize=(10, 8))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x109c80d68>




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/kI15iIhCjG.png?imageslim)

我们还可以找到每一种营养成分含量最多的食物是什么：


```python
by_nutrient = ndata.groupby(['nutgroup', 'nutrient'])

get_maximum = lambda x: x.loc[x.value.idxmax()]
get_minimum = lambda x: x.loc[x.value.idxmin()]

max_foods = by_nutrient.apply(get_maximum)[['value', 'food']]

# make the food a little smaller
max_foods.food = max_foods.food.str[:50]
```

因为得到的DataFrame太大，这里只输出'Amino Acids'(氨基酸)的营养群（nutrient group）:


```python
max_foods.loc['Amino Acids']['food']
```




    nutrient
    Alanine                          Gelatins, dry powder, unsweetened
    Arginine                              Seeds, sesame flour, low-fat
    Aspartic acid                                  Soy protein isolate
    Cystine               Seeds, cottonseed flour, low fat (glandless)
    Glutamic acid                                  Soy protein isolate
                                           ...
    Serine           Soy protein isolate, PROTEIN TECHNOLOGIES INTE...
    Threonine        Soy protein isolate, PROTEIN TECHNOLOGIES INTE...
    Tryptophan        Sea lion, Steller, meat with fat (Alaska Native)
    Tyrosine         Soy protein isolate, PROTEIN TECHNOLOGIES INTE...
    Valine           Soy protein isolate, PROTEIN TECHNOLOGIES INTE...
    Name: food, Length: 19, dtype: object
