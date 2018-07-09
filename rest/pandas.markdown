---
author: evo
comments: true
date: 2018-03-24 09:23:08+00:00
layout: post
link: http://106.15.37.116/2018/03/24/pandas/
slug: pandas
title: pandas
wordpress_id: 732
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


pandas经常用来对要学习的数据进行读取，然后整理，这个也是经常使用的


## 要点：




### 1.DateFrame的初始化相关



    
    # DataFrame的初始化相关
    
    # numpy 是一个列表 但是pandans是一个字典
    # 因为每一行每一列都有一个名字
    
    import pandas as pd
    import numpy as np
    
    # 自动添加了序号
    s = pd.Series([1, 2, 3, 6, np.nan, 44, 1])
    print(s)
    print()
    
    dates = pd.date_range('20160101', periods=6)
    print(dates)
    
    # 用日期定义了行 用abcd定义了列
    df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=['a', 'b', 'c', 'd'])
    df1 = pd.DataFrame(np.arange(12).reshape((3, 4)))
    
    print(df)
    print(df1)
    # 使用字典的方式，每个key-value对应一列 这个还是感觉比较特殊的
    df2 = pd.DataFrame({
        "A": 1.,
        "B": pd.Timestamp('20130101'),
        "C": pd.Series(1, index=list(range(4)), dtype='float32'),
        "D": np.array([3] * 4, dtype='int32'),
        "E": pd.Categorical(["test", "train", "test", "train"]),
        "F": 'foo'
    })
    print(df2)
    print(df2.dtypes)  # 打印出每一列的数据形式 可以查看每一列是那种形式的数字
    
    print(df2.index)  # 输出所有的行的名字
    print(df2.columns)  # 输出所有的列的名字
    
    print(df2.values)  # 输出所有的值
    print(type(df2.values))  # 格式是numpy.ndarray
    
    # 对数据的列进行一些简单的计算
    print(df2.describe())


输出：

    
    0     1.0
    1     2.0
    2     3.0
    3     6.0
    4     NaN
    5    44.0
    6     1.0
    dtype: float64
    
    DatetimeIndex(['2016-01-01', '2016-01-02', '2016-01-03', '2016-01-04',
                   '2016-01-05', '2016-01-06'],
                  dtype='datetime64[ns]', freq='D')
                       a         b         c         d
    2016-01-01  0.183129  0.025323  0.394537 -0.141021
    2016-01-02 -1.066663 -0.957797 -0.192380 -0.380006
    2016-01-03 -0.719181 -0.216180  2.198811 -0.113238
    2016-01-04  0.597224 -0.922469 -0.487665  1.608745
    2016-01-05 -0.243880 -0.262899  0.423801 -1.018344
    2016-01-06  1.463517  0.039078  1.565562 -0.589678
       0  1   2   3
    0  0  1   2   3
    1  4  5   6   7
    2  8  9  10  11
         A          B    C  D      E    F
    0  1.0 2013-01-01  1.0  3   test  foo
    1  1.0 2013-01-01  1.0  3  train  foo
    2  1.0 2013-01-01  1.0  3   test  foo
    3  1.0 2013-01-01  1.0  3  train  foo
    A           float64
    B    datetime64[ns]
    C           float32
    D             int32
    E          category
    F            object
    dtype: object
    Int64Index([0, 1, 2, 3], dtype='int64')
    Index(['A', 'B', 'C', 'D', 'E', 'F'], dtype='object')
    [[1.0 Timestamp('2013-01-01 00:00:00') 1.0 3 'test' 'foo']
     [1.0 Timestamp('2013-01-01 00:00:00') 1.0 3 'train' 'foo']
     [1.0 Timestamp('2013-01-01 00:00:00') 1.0 3 'test' 'foo']
     [1.0 Timestamp('2013-01-01 00:00:00') 1.0 3 'train' 'foo']]
    <class 'numpy.ndarray'>




### 2.DateFrame的一些基本操作



    
    import pandas as pd
    import numpy as np
    
    # 对数据的列进行一些简单的计算
    print(df2.describe())
    
    
    # 用到数学运算的画这个比较重要
    print(df2.T)
    
    # 1就是行还是排序 ascending=False就是倒着排序
    print(df2.sort_index(axis=1, ascending=False))
    print(df2.sort_index(axis=0, ascending=False))
    
    # 对列表中的值进行排序
    print(df2.sort_values(by='E'))


输出：

    
             A    C    D
    count  4.0  4.0  4.0
    mean   1.0  1.0  3.0
    std    0.0  0.0  0.0
    min    1.0  1.0  3.0
    25%    1.0  1.0  3.0
    50%    1.0  1.0  3.0
    75%    1.0  1.0  3.0
    max    1.0  1.0  3.0
                         0                    1                    2  \
    A                    1                    1                    1   
    B  2013-01-01 00:00:00  2013-01-01 00:00:00  2013-01-01 00:00:00   
    C                    1                    1                    1   
    D                    3                    3                    3   
    E                 test                train                 test   
    F                  foo                  foo                  foo   
    
                         3  
    A                    1  
    B  2013-01-01 00:00:00  
    C                    1  
    D                    3  
    E                train  
    F                  foo  
         F      E  D    C          B    A
    0  foo   test  3  1.0 2013-01-01  1.0
    1  foo  train  3  1.0 2013-01-01  1.0
    2  foo   test  3  1.0 2013-01-01  1.0
    3  foo  train  3  1.0 2013-01-01  1.0
         A          B    C  D      E    F
    3  1.0 2013-01-01  1.0  3  train  foo
    2  1.0 2013-01-01  1.0  3   test  foo
    1  1.0 2013-01-01  1.0  3  train  foo
    0  1.0 2013-01-01  1.0  3   test  foo
         A          B    C  D      E    F
    0  1.0 2013-01-01  1.0  3   test  foo
    2  1.0 2013-01-01  1.0  3   test  foo
    1  1.0 2013-01-01  1.0  3  train  foo
    3  1.0 2013-01-01  1.0  3  train  foo




### 3.对行列的选择



    
    # 行列的筛选
    
    import numpy as np
    import pandas as pd
    
    dates = pd.date_range('20130101', periods=6)
    df = pd.DataFrame(np.arange(24).reshape((6, 4)),
                      index=dates,
                      columns=['A', 'B', 'C', 'D'])
    print(df)
    
    # 选择列
    print(df['A'])
    print(df.A)  # 这两种写法是一样的
    
    # 选择行
    print(df[0:3])
    print(df['20130102':'20130104'])  # 上面两种写法是一样的
    
    # 更高级的选择
    # 纯标签筛选:loc->local
    print(df.loc['20130102'])  # 以标签来进行选择
    print(df.loc[:, ['A', 'B']])  # 选择所有的行，然后列的话选择的是A和B
    print(df.loc['20130102', 'A'])
    print(df.loc['20130102', ['A', 'B', 'C']])
    
    # 纯index筛选 :iloc  i->index
    print(df.iloc[1, :])
    print(df.iloc[1:3, 1:3])
    print(df.iloc[[1, 3, 5], 1:3])
    
    # 综合进行筛选 mixed selection :ix
    print(df.ix[:3, ['A', 'B']])
    print(df.ix[1, 'A'])
    
    # Boolean indexing
    print(df[df.A > 8])  # 照出df中A大于8的行
    print(df[df.A > 8])


输出：

    
                 A   B   C   D
    2013-01-01   0   1   2   3
    2013-01-02   4   5   6   7
    2013-01-03   8   9  10  11
    2013-01-04  12  13  14  15
    2013-01-05  16  17  18  19
    2013-01-06  20  21  22  23
    2013-01-01     0
    2013-01-02     4
    2013-01-03     8
    2013-01-04    12
    2013-01-05    16
    2013-01-06    20
    Freq: D, Name: A, dtype: int32
    2013-01-01     0
    2013-01-02     4
    2013-01-03     8
    2013-01-04    12
    2013-01-05    16
    2013-01-06    20
    Freq: D, Name: A, dtype: int32
                A  B   C   D
    2013-01-01  0  1   2   3
    2013-01-02  4  5   6   7
    2013-01-03  8  9  10  11
                 A   B   C   D
    2013-01-02   4   5   6   7
    2013-01-03   8   9  10  11
    2013-01-04  12  13  14  15
    A    4
    B    5
    C    6
    D    7
    Name: 2013-01-02 00:00:00, dtype: int32
                 A   B
    2013-01-01   0   1
    2013-01-02   4   5
    2013-01-03   8   9
    2013-01-04  12  13
    2013-01-05  16  17
    2013-01-06  20  21
    4
    A    4
    B    5
    C    6
    Name: 2013-01-02 00:00:00, dtype: int32
    A    4
    B    5
    C    6
    D    7
    Name: 2013-01-02 00:00:00, dtype: int32
                B   C
    2013-01-02  5   6
    2013-01-03  9  10
                 B   C
    2013-01-02   5   6
    2013-01-04  13  14
    2013-01-06  21  22
                A  B
    2013-01-01  0  1
    2013-01-02  4  5
    2013-01-03  8  9
    4
                 A   B   C   D
    2013-01-04  12  13  14  15
    2013-01-05  16  17  18  19
    2013-01-06  20  21  22  23
                 A   B   C   D
    2013-01-04  12  13  14  15
    2013-01-05  16  17  18  19
    2013-01-06  20  21  22  23




### 4.DateFrame 的赋值修改和添加列



    
    # 赋值，修改，添加列
    
    import numpy as np
    import pandas as pd
    
    dates = pd.date_range('20130101', periods=6)
    df = pd.DataFrame(np.arange(24).reshape(6, 4),
                      index=dates,
                      columns=['A', 'B', 'C', 'D'])
    print(df)
    
    # 普通的修改方法
    df.iloc[2, 2] = 1111
    df.loc['20130101', ['A', 'B']] = 222
    df.ix[3, ['C']] = 333
    print(df)
    
    # 通过设定范围来进行修改
    # 这几种方式经常用到
    # 将A大于10的全部设置为0
    df1 = df.copy()
    df2 = df.copy()
    df3 = df.copy()
    df1.A[df1.A > 10] = 0  # 将A大于10的行的A的值都设置位0
    print(df1)
    df2.B[df2.A > 10] = 0  # 将A大于10的行的B的值都设置为0
    print(df2)
    df3[df3.A > 10] = 0  # 将A大于10的行的所有的值都设置为0
    print(df3)
    
    # 添加一个空的列
    df['E'] = np.nan
    # 添加一个新的序列作为列 把列先定义好在加上去
    df['F'] = pd.Series([1, 2, 3, 4, 5, 6],
                        index=dates, )
    print(df)


输出：

    
                 A   B   C   D
    2013-01-01   0   1   2   3
    2013-01-02   4   5   6   7
    2013-01-03   8   9  10  11
    2013-01-04  12  13  14  15
    2013-01-05  16  17  18  19
    2013-01-06  20  21  22  23
                  A    B     C   D
    2013-01-01  222  222     2   3
    2013-01-02    4    5     6   7
    2013-01-03    8    9  1111  11
    2013-01-04   12   13   333  15
    2013-01-05   16   17    18  19
    2013-01-06   20   21    22  23
                A    B     C   D
    2013-01-01  0  222     2   3
    2013-01-02  4    5     6   7
    2013-01-03  8    9  1111  11
    2013-01-04  0   13   333  15
    2013-01-05  0   17    18  19
    2013-01-06  0   21    22  23
                  A  B     C   D
    2013-01-01  222  0     2   3
    2013-01-02    4  5     6   7
    2013-01-03    8  9  1111  11
    2013-01-04   12  0   333  15
    2013-01-05   16  0    18  19
    2013-01-06   20  0    22  23
                A  B     C   D
    2013-01-01  0  0     0   0
    2013-01-02  4  5     6   7
    2013-01-03  8  9  1111  11
    2013-01-04  0  0     0   0
    2013-01-05  0  0     0   0
    2013-01-06  0  0     0   0
                  A    B     C   D   E  F
    2013-01-01  222  222     2   3 NaN  1
    2013-01-02    4    5     6   7 NaN  2
    2013-01-03    8    9  1111  11 NaN  3
    2013-01-04   12   13   333  15 NaN  4
    2013-01-05   16   17    18  19 NaN  5
    2013-01-06   20   21    22  23 NaN  6




### 5.输入输出


CSV文件如下：

    
    Student ID,Name,Age,Gender
    1100,A,12,Male
    1101,Col,2,Male
    1102,Tony,1,Male
    1103,Kay,3,Male
    1104,Hony,12,Male
    1105,D,56,Male
    1106,a,4,Male
    1107,a,7,Female
    1108,v,8,Female
    1109,cv,8,Female
    1110,f,9,Female
    1111,Tony,9,Female


代码如下：

    
    # 数据的导入导出
    
    # picke 是python自带的压缩的形式？
    # read_gbq 网络的形式
    # csv 格式可以直观的看到
    
    import pandas as pd
    
    data = pd.read_csv('student_info.csv')  # 会自动添加索引
    print(data)
    print(data.loc[:, ['Student ID', 'Gender']])
    
    data.to_pickle('student_info.picke')  # 存储为pickle文件


注：对于支持的一些格式需要补充下


### 6.nan值的处理



    
    # 对nan的处理
    
    import numpy as np
    import pandas as pd
    
    dates = pd.date_range('20130101', periods=6)
    df = pd.DataFrame(np.arange(24).reshape((6, 4)),
                      index=dates,
                      columns=['A', 'B', 'C', 'D'])
    df.iloc[0, 1] = np.nan
    df.iloc[1, 2] = np.nan
    
    print(df)
    
    # 对含有nan的行或列的丢弃
    df1 = df.copy()
    df2 = df.copy()
    df3 = df.copy()
    df4 = df.copy()
    # axis=0是对于行的丢弃  axis=1是对于1的丢弃
    print(df1.dropna(axis=0, how='any'))  # 默认是any 只要又一个是nan就丢掉这一行
    print(df2.dropna(axis=0, how='all'))  # 只有这一行全部是nan才丢掉
    print(df3.dropna(axis=1, how='any'))
    print(df4.dropna(axis=1, how='all'))
    
    # 对于nan的填充
    df1 = df.copy()
    print(df1.fillna(value=0))  # 将nan值填充为0
    
    # 查看是否又丢失数据
    df1 = df.copy()
    print(df2.isnull())
    
    # any 至少有一个
    print(np.any(df.isnull()) == True)


输出：

    
                 A     B     C   D
    2013-01-01   0   NaN   2.0   3
    2013-01-02   4   5.0   NaN   7
    2013-01-03   8   9.0  10.0  11
    2013-01-04  12  13.0  14.0  15
    2013-01-05  16  17.0  18.0  19
    2013-01-06  20  21.0  22.0  23
                 A     B     C   D
    2013-01-03   8   9.0  10.0  11
    2013-01-04  12  13.0  14.0  15
    2013-01-05  16  17.0  18.0  19
    2013-01-06  20  21.0  22.0  23
                 A     B     C   D
    2013-01-01   0   NaN   2.0   3
    2013-01-02   4   5.0   NaN   7
    2013-01-03   8   9.0  10.0  11
    2013-01-04  12  13.0  14.0  15
    2013-01-05  16  17.0  18.0  19
    2013-01-06  20  21.0  22.0  23
                 A   D
    2013-01-01   0   3
    2013-01-02   4   7
    2013-01-03   8  11
    2013-01-04  12  15
    2013-01-05  16  19
    2013-01-06  20  23
                 A     B     C   D
    2013-01-01   0   NaN   2.0   3
    2013-01-02   4   5.0   NaN   7
    2013-01-03   8   9.0  10.0  11
    2013-01-04  12  13.0  14.0  15
    2013-01-05  16  17.0  18.0  19
    2013-01-06  20  21.0  22.0  23
                 A     B     C   D
    2013-01-01   0   0.0   2.0   3
    2013-01-02   4   5.0   0.0   7
    2013-01-03   8   9.0  10.0  11
    2013-01-04  12  13.0  14.0  15
    2013-01-05  16  17.0  18.0  19
    2013-01-06  20  21.0  22.0  23
                    A      B      C      D
    2013-01-01  False   True  False  False
    2013-01-02  False  False   True  False
    2013-01-03  False  False  False  False
    2013-01-04  False  False  False  False
    2013-01-05  False  False  False  False
    2013-01-06  False  False  False  False
    True




### 7.DateFrame的拼接和行的添加 concat append



    
    # DataFrame的拼接以及行的追加
    
    import pandas as pd
    import numpy as np
    
    # concatenating
    df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'])
    df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['a', 'b', 'c', 'd'])
    df3 = pd.DataFrame(np.ones((3, 4)) * 2, columns=['a', 'b', 'c', 'd'])
    print(df1)
    print(df2)
    print(df3)
    res = pd.concat([df1, df2, df3], axis=0)  # 0是竖向的合并 1是横向的合并
    print(res)  # 合并之后row 的flag没有自动改变，
    res1 = pd.concat([df1, df2, df3], axis=0, ignore_index=True)  # 忽略之前的row的index
    print(res1)
    
    # concat 中的 join 两种形式 ['inner','outer']
    # merge 没有讲
    
    df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'], index=[1, 2, 3])
    df2 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['b', 'c', 'd', 'e'], index=[2, 3, 4])
    print(pd.concat([df1, df2], axis=0, join='outer', ignore_index=True))  # 没有的栏用NaN填充
    print(pd.concat([df1, df2], axis=0, join='inner', ignore_index=True))  # 把相同的东西合并在一起，不同的裁剪掉
    
    # join_axes
    res1 = pd.concat([df1, df2], axis=1)
    res2 = pd.concat([df1, df2], axis=1, join_axes=[df1.index])  # 按照df1的index进行合并，df2中没有的用NaN填充
    print(res1)
    print(res2)
    
    # append
    # 追加一个dataFrame作为行
    res1 = df1.append(df2, ignore_index=True)
    res2 = df1.append([df2, df1], ignore_index=True)
    print(res1)
    print(res2)
    # 追加设定的一行
    s3 = pd.Series([1, 2, 3, 4], index=['a', 'b', 'c', 'd'])
    res3 = df1.append(s3, ignore_index=True)
    print(res3)


输出：

    
         a    b    c    d
    0  0.0  0.0  0.0  0.0
    1  0.0  0.0  0.0  0.0
    2  0.0  0.0  0.0  0.0
         a    b    c    d
    0  1.0  1.0  1.0  1.0
    1  1.0  1.0  1.0  1.0
    2  1.0  1.0  1.0  1.0
         a    b    c    d
    0  2.0  2.0  2.0  2.0
    1  2.0  2.0  2.0  2.0
    2  2.0  2.0  2.0  2.0
         a    b    c    d
    0  0.0  0.0  0.0  0.0
    1  0.0  0.0  0.0  0.0
    2  0.0  0.0  0.0  0.0
    0  1.0  1.0  1.0  1.0
    1  1.0  1.0  1.0  1.0
    2  1.0  1.0  1.0  1.0
    0  2.0  2.0  2.0  2.0
    1  2.0  2.0  2.0  2.0
    2  2.0  2.0  2.0  2.0
         a    b    c    d
    0  0.0  0.0  0.0  0.0
    1  0.0  0.0  0.0  0.0
    2  0.0  0.0  0.0  0.0
    3  1.0  1.0  1.0  1.0
    4  1.0  1.0  1.0  1.0
    5  1.0  1.0  1.0  1.0
    6  2.0  2.0  2.0  2.0
    7  2.0  2.0  2.0  2.0
    8  2.0  2.0  2.0  2.0
         a    b    c    d    e
    0  0.0  0.0  0.0  0.0  NaN
    1  0.0  0.0  0.0  0.0  NaN
    2  0.0  0.0  0.0  0.0  NaN
    3  NaN  0.0  0.0  0.0  0.0
    4  NaN  0.0  0.0  0.0  0.0
    5  NaN  0.0  0.0  0.0  0.0
         b    c    d
    0  0.0  0.0  0.0
    1  0.0  0.0  0.0
    2  0.0  0.0  0.0
    3  0.0  0.0  0.0
    4  0.0  0.0  0.0
    5  0.0  0.0  0.0
         a    b    c    d    b    c    d    e
    1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
    2  0.0  0.0  0.0  0.0  0.0  0.0  0.0  0.0
    3  0.0  0.0  0.0  0.0  0.0  0.0  0.0  0.0
    4  NaN  NaN  NaN  NaN  0.0  0.0  0.0  0.0
         a    b    c    d    b    c    d    e
    1  0.0  0.0  0.0  0.0  NaN  NaN  NaN  NaN
    2  0.0  0.0  0.0  0.0  0.0  0.0  0.0  0.0
    3  0.0  0.0  0.0  0.0  0.0  0.0  0.0  0.0
         a    b    c    d    e
    0  0.0  0.0  0.0  0.0  NaN
    1  0.0  0.0  0.0  0.0  NaN
    2  0.0  0.0  0.0  0.0  NaN
    3  NaN  0.0  0.0  0.0  0.0
    4  NaN  0.0  0.0  0.0  0.0
    5  NaN  0.0  0.0  0.0  0.0
         a    b    c    d    e
    0  0.0  0.0  0.0  0.0  NaN
    1  0.0  0.0  0.0  0.0  NaN
    2  0.0  0.0  0.0  0.0  NaN
    3  NaN  0.0  0.0  0.0  0.0
    4  NaN  0.0  0.0  0.0  0.0
    5  NaN  0.0  0.0  0.0  0.0
    6  0.0  0.0  0.0  0.0  NaN
    7  0.0  0.0  0.0  0.0  NaN
    8  0.0  0.0  0.0  0.0  NaN
         a    b    c    d
    0  0.0  0.0  0.0  0.0
    1  0.0  0.0  0.0  0.0
    2  0.0  0.0  0.0  0.0
    3  1.0  2.0  3.0  4.0




### 8.merge



    
    # merge
    # 比concatenate要稍微复杂点
    # join的功能与merge类似 这个地方没有讲
    import pandas as pd
    import numpy as np
    
    # merging two df by key/keys. (may be used in database)
    # simple example
    left = pd.DataFrame({
        'key': ['K0', 'K1', 'K2', 'K3'],
        'A': ['A0', 'A1', 'A2', 'A3'],
        'B': ['B0', 'B1', 'B2', 'B3']
    })
    right = pd.DataFrame({
        'key': ['K0', 'K1', 'K2', 'K3'],
        'C': ['C0', 'C1', 'C2', 'C3'],
        'D': ['D0', 'D1', 'D2', 'D3']
    })
    print(left)
    print(right)
    
    res1 = pd.merge(left, right)
    res2 = pd.merge(left, right, on='key')  # 基于key进行合并
    print(res1)
    print(res2)
    
    # consider two keys
    left = pd.DataFrame({
        'key1': ['K0', 'K0', 'K1', 'K2'],
        'key2': ['K0', 'K1', 'K0', 'K1'],
        'A': ['A0', 'A1', 'A2', 'A3'],
        'B': ['B0', 'B1', 'B2', 'B3']
    })
    right = pd.DataFrame({
        'key1': ['K0', 'K1', 'K1', 'K2'],
        'key2': ['K0', 'K0', 'K0', 'K0'],
        'C': ['C0', 'C1', 'C2', 'C3'],
        'D': ['D0', 'D1', 'D2', 'D3']
    })
    print(left)
    print(right)
    # how : inner outer left right
    print(pd.merge(left=left, right=right,
                   how='inner',  # 只有left与right有相同的key2key2对才会被考虑
                   on=['key1', 'key2']))  # 两个key都要考虑
    print(pd.merge(left=left, right=right,
                   how='outer',  # 如果一个没有这个key对，那么用NaN进行填充
                   on=['key1', 'key2']))
    print(pd.merge(left=left, right=right,
                   how='left',  # 以左边的为准，右边的没有的用NaN填充
                   on=['key1', 'key2']))
    print(pd.merge(left=left, right=right,
                   how='right',  # 以右边的为准，左边的没有的用NaN填充
                   on=['key1', 'key2']))
    
    # indicator 会现实出每一行的合并情况
    df1 = pd.DataFrame({'col1': [0, 1], 'col_left': ['a', 'b']})
    df2 = pd.DataFrame({'col1': [1, 2, 2], 'col_right': [2, 2, 2]})
    print(df1)
    print(df2)
    # 可以设定是否显示，也可以自定义所显示的column的名称
    res1 = pd.merge(left=df1, right=df2, on='col1', how='outer', indicator=True)
    res2 = pd.merge(left=df1, right=df2, on='col1', how='outer', indicator='indicator_column')
    print(res1)
    print(res1.size)
    print(res2)
    
    # merge by index  因为之前是通过的column来进行合并的，现在是通过index进行合并的
    left = pd.DataFrame({'A': ['A0', 'A1', 'A2'],
                         'B': ['B0', 'B1', 'B2']},
                        index=['K0', 'K1', 'K2'])
    right = pd.DataFrame({'C': ['C0', 'C1', 'C2'],
                          'D': ['D0', 'D1', 'D2']},
                         index=['K0', 'K2', 'K3'])
    
    print(left)
    print(right)
    res1 = pd.merge(left=left, right=right,
                    left_index=True,
                    right_index=True, how='outer')
    res2 = pd.merge(left=left, right=right,
                    left_index=True,
                    right_index=True, how='inner')
    print(res1)
    print(res2)
    
    # handle overlapping
    boys = pd.DataFrame({'k': ['K0', 'K1', 'K2'],
                         'age': [1, 2, 3]})
    girls = pd.DataFrame({'k': ['K0', 'K0', 'K2'],
                          'age': [4, 5, 6]})
    res = pd.merge(left=boys, right=girls,
                   on='k',
                   suffixes=['_boy', '_girl'],
                   how='inner')
    print(res)


输出：

    
        A   B key
    0  A0  B0  K0
    1  A1  B1  K1
    2  A2  B2  K2
    3  A3  B3  K3
        C   D key
    0  C0  D0  K0
    1  C1  D1  K1
    2  C2  D2  K2
    3  C3  D3  K3
        A   B key   C   D
    0  A0  B0  K0  C0  D0
    1  A1  B1  K1  C1  D1
    2  A2  B2  K2  C2  D2
    3  A3  B3  K3  C3  D3
        A   B key   C   D
    0  A0  B0  K0  C0  D0
    1  A1  B1  K1  C1  D1
    2  A2  B2  K2  C2  D2
    3  A3  B3  K3  C3  D3
        A   B key1 key2
    0  A0  B0   K0   K0
    1  A1  B1   K0   K1
    2  A2  B2   K1   K0
    3  A3  B3   K2   K1
        C   D key1 key2
    0  C0  D0   K0   K0
    1  C1  D1   K1   K0
    2  C2  D2   K1   K0
    3  C3  D3   K2   K0
        A   B key1 key2   C   D
    0  A0  B0   K0   K0  C0  D0
    1  A2  B2   K1   K0  C1  D1
    2  A2  B2   K1   K0  C2  D2
         A    B key1 key2    C    D
    0   A0   B0   K0   K0   C0   D0
    1   A1   B1   K0   K1  NaN  NaN
    2   A2   B2   K1   K0   C1   D1
    3   A2   B2   K1   K0   C2   D2
    4   A3   B3   K2   K1  NaN  NaN
    5  NaN  NaN   K2   K0   C3   D3
        A   B key1 key2    C    D
    0  A0  B0   K0   K0   C0   D0
    1  A1  B1   K0   K1  NaN  NaN
    2  A2  B2   K1   K0   C1   D1
    3  A2  B2   K1   K0   C2   D2
    4  A3  B3   K2   K1  NaN  NaN
         A    B key1 key2   C   D
    0   A0   B0   K0   K0  C0  D0
    1   A2   B2   K1   K0  C1  D1
    2   A2   B2   K1   K0  C2  D2
    3  NaN  NaN   K2   K0  C3  D3
       col1 col_left
    0     0        a
    1     1        b
       col1  col_right
    0     1          2
    1     2          2
    2     2          2
       col1 col_left  col_right      _merge
    0     0        a        NaN   left_only
    1     1        b        2.0        both
    2     2      NaN        2.0  right_only
    3     2      NaN        2.0  right_only
    16
       col1 col_left  col_right indicator_column
    0     0        a        NaN        left_only
    1     1        b        2.0             both
    2     2      NaN        2.0       right_only
    3     2      NaN        2.0       right_only
         A   B
    K0  A0  B0
    K1  A1  B1
    K2  A2  B2
         C   D
    K0  C0  D0
    K2  C1  D1
    K3  C2  D2
          A    B    C    D
    K0   A0   B0   C0   D0
    K1   A1   B1  NaN  NaN
    K2   A2   B2   C1   D1
    K3  NaN  NaN   C2   D2
         A   B   C   D
    K0  A0  B0  C0  D0
    K2  A2  B2  C1  D1
       age_boy   k  age_girl
    0        1  K0         4
    1        1  K0         5
    2        3  K2         6




### 9.将DateFrame画出来 plot



    
    # plot
    # 关于pandas的画图看来还是要扩充一下 比如想知道箱图等
    import pandas as pd
    import numpy as np
    
    import matplotlib.pyplot as plt
    
    # plot data
    # Series 线性的数据
    data = pd.Series(np.random.randn(1000), index=np.arange(1000))
    data1 = data.cumsum()
    # data.plot()# 可以把数据这样显示出来
    # data1.plot()
    
    # DataFrame
    data = pd.DataFrame(np.random.randn(1000, 4),
                        index=np.arange(1000),
                        columns=list('ABCD'))  # 这也可以！！
    data1 = data.cumsum()
    print(data.head(3))  # 默认的是5
    # data.plot()  # 会有4条线
    # data1.plot()
    
    # plot methods:
    # bar,hist,box,kde,area,scatter,hexbin,pie
    # 这个地方没明白？ax是什么参数？
    ax = data1.plot.scatter(x='A', y='B',
                            color='DarkBlue', label='Class1')
    data1.plot.scatter(x='A', y='C',
                       color='DarkGreen', label='Class2',
                       ax=ax)  # 在一张图上打印出了两种scatter的数据
    plt.show()


输出：

    
              A         B         C         D
    0  1.071231 -1.836988  0.051154  0.675617
    1 -1.078098  0.544506 -1.150166 -0.347396
    2 -1.665255  1.725789 -0.291399 -0.859150




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab61863952c3.png)





## COMMENT：


**需要补充**




## REF：





 	
  1. [数据 Numpy & Pandas](https://morvanzhou.github.io/tutorials/data-manipulation/np-pd/)  **（推荐入门看这个人的莫烦python）**


