---
author: evo
comments: true
date: 2018-05-06 06:37:09+00:00
layout: post
link: http://106.15.37.116/2018/05/06/python-chinese-path/
slug: python-chinese-path
title: python 中文路径怎么办？
wordpress_id: 5316
categories:
- 随想与反思
tags:
- python
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL





 	
  1. [How to make unicode string with python3](https://stackoverflow.com/questions/6812031/how-to-make-unicode-string-with-python3) 这个是有问题的。

 	
  2. [Python 3.x 使用 opencv 无法读取中文路径如何解决？](https://www.zhihu.com/question/67157462)




# TODO





 	
  * aaa




# MOTIVE





 	
  * 在机器学习中，经常要读取各种文件，文件路径中有中文要怎么处理？





* * *





# 到底路径应该怎么写？里面是 / 还是\\ 还是 \？路径怎么拼接？


在python程序里面我们经常需要对文件进行操作，Windows下的文件目录路径使用反斜杠 “\” 来分隔。但是，Python代码里面，反斜杠“\”是转义符，因此就会有问题：比如：“c:\test.txt”这表示c盘根目录下的一个文件，还是表示一个字符串呢？因此，写路径的时候一定要明确：

可以采用下面任意一种方法书写：



 	
  * 使用斜杠 “/” :  "c:/test.txt" … 不用反斜杠就没法产生歧义了。**因此还是尽量使用斜杠 “/”**

 	
  * 将反斜杠符号转义: "c:\\aaa\\test.txt"… 因为反斜杠是转义符，所以两个"\\"就表示一个反斜杠符号。

 	
  * 使用Python的raw string: r"c:\test.txt" … python下在字符串前面加上字母r，表示后面是一个原始字符串raw string，不过raw string主要是为正则表达式而不是windows路径设计的，所以这种做法尽量少用，可能会出问题。


举例子：

    
    path1 = "E:/测试/regression.test"
    path2 = r"E:/测试/regression.test"
    path3 = r"E:\\测试\\regression.test"
    path4 = r"E:\测试\regression.test"
    path5 = os.path.join('E:', '测试', 'regression.test')
    path6 = os.path.join('E:\\', '测试', 'regression.test')
    path7 = os.path.join('E:\\', u'测试', 'regression.test')
    path8 = r"E:\测试" + r"\regression.test"
    
    print("1    "+path1)
    print("2    "+path2)
    print("3    "+path3)
    print("4    "+path4)
    print("5    "+path5)
    print("6    "+path6)
    print("7    "+path7)
    print("8    "+path8)


输出：

    
    1    E:/测试/regression.test
    2    E:/测试/regression.test
    3    E:\\测试\\regression.test
    4    E:\测试\regression.test
    5    E:测试\regression.test
    6    E:\测试\regression.test
    7    E:\测试\regression.test
    8    E:\测试\regression.test


实际上，上面的路径中除了5是错误的，其它的都是正确的写法，但是要注意，这是python3的。




# 普通的 open 的中文路径问题


实际上，python3中对于中文的支持已经非常好了，比如说：

    
    f = open('E:\\你好.txt', 'w')
    f.write("你好")


这个是完全可以正确运行的。没有问题。


# read_csv 时候的中文路径问题


但是，在使用pandas中的 read_csv 来读取文件的时候，如果 csv 文件的路径是中文，那么就有问题了。

比如：

    
    import pandas as pd
    
    
    path1 = "E:/测试/regression.test"
    df1 = pd.read_csv(path1, header=None, sep='\t')


会报错输出：

    
    Traceback (most recent call last):
      File "E:/01.Learn/meinian/code/trypath.py", line 8, in <module>
        df1 = pd.read_csv(path1, header=None, sep='\t')
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\pandas\io\parsers.py", line 646, in parser_f
        return _read(filepath_or_buffer, kwds)
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\pandas\io\parsers.py", line 389, in _read
        parser = TextFileReader(filepath_or_buffer, **kwds)
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\pandas\io\parsers.py", line 730, in __init__
        self._make_engine(self.engine)
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\pandas\io\parsers.py", line 923, in _make_engine
        self._engine = CParserWrapper(self.f, **self.options)
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\pandas\io\parsers.py", line 1390, in __init__
        self._reader = _parser.TextReader(src, **kwds)
      File "pandas\parser.pyx", line 373, in pandas.parser.TextReader.__cinit__ (pandas\parser.c:4184)
      File "pandas\parser.pyx", line 669, in pandas.parser.TextReader._setup_parser_source (pandas\parser.c:8471)
    OSError: Initializing from file failed


开始，我以为是中文编码的问题，然后在网上查找了很多，但是基本上都是python2的一些方法，而且，尝试了各种各样的encode和decode方式，都不行。最后我才知道，只需要在read_csv的时候 ，设定一下engine='python' 就行：

    
    df1 = pd.read_csv(path1, header=None, sep='\t',engine='python')


这样就可以了。


# cv2.imread 的中文问题


在网上查资料的时候，看到有人说opencv读取图片的时候，中文路径也是有问题的，虽然没遇到，也记一下：

方法1：

    
    import cv2
    
    def cv_imread(file_path = ""):
        file_path_gbk = img_path.encode('gbk')        # unicode转gbk，字符串变为字节数组
        img_mat = cv2.imread(file_path_gbk.decode())  # 字节数组直接转字符串，不解码
        return img_mat


方法2：

    
    # coding: utf-8
    import cv2
    
    def cv_imread(file_path = ""):
        file_path_gbk = img_path.encode('gbk')        # unicode转gbk，字符串变为字节数组
        img_mat = cv2.imread(file_path_gbk.decode())  # 字节数组直接转字符串，不解码
        return img_mat


**这两个方法没有进行尝试。后续尝试之后进行补充。**









* * *





# COMMENT



