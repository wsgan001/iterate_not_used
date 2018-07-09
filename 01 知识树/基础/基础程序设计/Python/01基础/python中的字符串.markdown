---
author: evo
comments: true
date: 2018-03-19 14:24:08+00:00
layout: post
link: http://106.15.37.116/2018/03/19/string-in-python/
slug: string-in-python
title: python中的字符串
wordpress_id: 425
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


以前在写程序的时候，遇到字符串的时候总是感觉到很棘手，用起来也很忐忑，比如字符串的转换，转换成数字，转换成ascii，转换成list，中文字符串，打印字符串时候的参数的形式，format，字符串的查找，等等。。这导致我一遇到这种类似的问题，就感觉心里忐忑，没有把握，因此还是要好好总结一下，让心里有个底。


## 要点：




### 字符串是不可以修改的，可以当作一个数组访问：



    
    a='abc'
    a[0]='b'


会有如下异常：

    
    'str' object does not support item assignment




### 去除空格：



    
    s='  abc  egf  '
    print(s)
    print(s.strip())# 返回的是一个新的字符串 删掉了前后的空格
    print(s.lstrip())
    print(s.rstrip())


输出：

    
      abc  egf  
    abc  egf
    abc  egf  
      abc  egf




### 大小写：



    
    s='abc def'
    print(s.upper())
    print(s.upper().lower())
    print(s.capitalize())


输出：

    
    ABC DEF
    abc def
    Abc def




### 字符串内部一小串的index：



    
    s_1='abcdefg'
    s_2='abdeffxx'
    print(s_1.index('bcd'))
    print(s_2.index('bcd'))


输出：

    
    1
    Traceback (most recent call last):
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2881, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
      File "<ipython-input-5-5a1ccae7cee2>", line 4, in <module>
        print(s_2.index('bcd'))
    ValueError: substring not found




### 字符串之间的比较：



    
    s_1 = 'abcdefg'
    s_2 = 'abdeffxx'
    print(s_1 == s_2)
    print(s_1 < s_2)
    print(s_1 > s_2)


输出：

    
    False
    True
    False




### 字符串长度：



    
    print(len('abcdefg'))
    print(len(''))
    print(len(None))


输出：

    
    7
    0
    Traceback (most recent call last):
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2881, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
      File "<ipython-input-7-672cccf559af>", line 3, in <module>
        print(len(None))#object of type 'NoneType' has no len()
    TypeError: object of type 'NoneType' has no len()




### 空字符串与None：



    
    # 空字符串并不等于None
    s = ''
    if s is None:
        print('None')
    else:
        print('is not None')
    # 空字符串在内存中还是有一个对象的
    # 对于空字符串，与False是等价的
    if not s:
        print("Empty")
    # 但是这样比较是错误的
    if s == False:
        print("s is False")
    else:
        print("s is not False")


输出：

    
    is not None
    Empty
    s is not False




### 字符串的分割与连接：



    
    # 字符串的分割和连接
    # 在python大数据分析处理的时候，经常拿到csv格式的数据，经常要分割csv的每一个行，然后】#
    # 在输出的时候又合并成一个整体
    s = 'abc,def,ghi'
    splitted = s.split(',')
    print(type(splitted))  # 返回的是一个数组
    print(splitted)
    
    s = 'abc,,def,,ghi'
    splitted = s.split(',,')
    print(type(splitted))  # 返回的是一个数组
    print(splitted)
    s = """abc
    def
    ghi
    efg"""
    s_1 = s.split('\n')  # 这两者是等价的，因为三引号的换行本身就是\n
    s_2 = s.splitlines()
    print(s_1)
    print(s_2)
    
    s = ['abc', 'def', 'ghi']
    print(''.join(s))  # 可以很轻松的把结果拼回去
    print('\n'.join(s))


输出：

    
    <class 'list'>
    ['abc', 'def', 'ghi']
    <class 'list'>
    ['abc', 'def', 'ghi']
    ['abc', 'def', 'ghi', 'efg']
    ['abc', 'def', 'ghi', 'efg']
    abcdefghi
    abc
    def
    ghi




### 字符串以什么开始与结束的判断：



    
    s = 'abcdefg'
    print(s.startswith('a'))
    print(s.startswith('abc'))
    print(s.endswith('g'))


输出：

    
    True
    True
    True




### 对字符串里面包含的字符的验证：



    
    # 判断是不是只有 abcdefg 或者 12345
    print('1234abcd'.isalnum())
    print('\t12ab'.isalnum())
    print('abcd'.isalpha())
    print('12345'.isdigit())
    print('   '.isspace())
    print('abcd12345'.islower())
    print('ABCD12345'.isupper())
    print('Hello world'.istitle())


输出：

    
    True
    False
    True
    True
    True
    True
    True
    False




### 数字转化为字符串：



    
    print(str(5))
    print(str(5.))  # 注意 这个地方会自动补0  因此有时候需要特别处理
    print(str(5.1234))
    print(str(-5.1234))


输出：

    
    5
    5.0
    5.1234
    -5.1234




### 字符串转化为数字：(这个要补充下）



    
    print(int('1234'))
    print(float('1234.4'))
    # 系统不会自动做转换，
    # print(int('1234.1234'))#invalid literal for int() with base 10: '1234.1234'
    print(int('1111', 2))
    # 当你拿到一个16进制的数据想转换为10进制的时候一定要加上这个base是多少
    print(int('ffff', 16))
    print(int('7777', 8))


输出：

    
    1234
    1234.4
    15
    65535
    4095




### 字符串转化为list：



    
    # 因为字符串不可以修改，因此想复制到一个数组里面
    s = 'abcdefg'
    l = list(s)
    print(l)


输出：

    
    ['a', 'b', 'c', 'd', 'e', 'f', 'g']






**缘由里面还有很多问题没有解答，要补充**


