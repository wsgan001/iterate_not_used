---
author: evo
comments: true
date: 2018-03-21 11:52:05+00:00
layout: post
link: http://106.15.37.116/2018/03/21/python-file-directory/
slug: python-file-directory
title: python中的file与directory
wordpress_id: 502
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


经常处理文件的话，文件的移动，拷贝，复制，查找，命名，批量命名，等都是要用到的以前不总结，导致每次遇到都需要进行查找确认，因此还是需要对这些知识进行整理总结下。


## 要点：


1.使用os库进行操作，这个比较麻烦：

地址的拼接，分割，以及文件名和文件后缀的获取：

    
    import os
    
    # 当前目录的绝对路径
    current_path = os.path.abspath('.')
    png_path=os.path.abspath('test.png')
    print(current_path)
    
    # 先创建一个路径，地址的拼接最好使用join 而不是自己拼接，因为这个可以在不同的操作系统区分符不同。
    # 这个地方仍然心有疑虑
    dir_new = os.path.join('C://newDirectory\\a', 'aa')
    
    print(os.path.split(png_path))  # 可以把一个路径拆分为两部分，后一部分总是最后级别的目录或文件名：
    print(os.path.splitext(png_path))  # 可以得到文件扩展名


输出如下：

    
    E:\01.Learn\01.Python\01.PythonBasic
    ('E:\\01.Learn\\01.Python\\01.PythonBasic', 'test.png')
    ('E:\\01.Learn\\01.Python\\01.PythonBasic\\test', '.png')


注：。。可以看到os库的使用的时候需要注意很多点，而且上面这些点并不是全面的，需要再补充。尤其时关于不同系统下地址的拼接，**这个要补充下，对于什么时候使用正斜杠什么时候使用反斜杠或者双反斜杠仍然有忐忑**。

文件夹的创建，删除，重命名（**因为文件在open的时候用'w'参数的时候会自动创建**）：

    
    import os
    
    
    png_path=os.path.abspath('test.png')
    dir_new = 'C://newDirectory'
    
    # 而且，mkdir只能创建一层文件夹，不能一下创建多层文件夹，不然会报错 FileNotFoundError ？需要再确认下
    # 注意，当文件存在时，会报错，无法创建文件 FileExistsError
    os.mkdir(dir_new)
    # 当路径不正确时，会报错 FileNotFoundError
    # 目录非空的时候，也会报错 OSError 目录不是空的
    os.rmdir(dir_new)
    
    print(os.rename('output1.txt', 'output3.txt'))
    print(os.remove('output2.txt'))
    
    
    # 列出当前目录下的所有目录
    dirs = [x for x in os.listdir('.') if os.path.isdir(x)]
    print(dirs)
    
    # 只列出.py文件
    files = [x for x in os.listdir('.')
             if os.path.isfile(x) and os.path.splitext(x)[1] == '.py']
    print(files)


输出：

    
    None
    None
    ['.idea']
    ['c1_code.py', 'c1_crawler.py', 'c2.py', 'c2_1_variable.py', 'c2_2_str.py', 'c2_3_if_circle.py', 'c2_4_recursion.py', 'c2_5_func.py', 'c2_6_func_as_parameter.py', 'c3_1.py', 'c3_7.py', 'c3_8.py', 'c3_9.py', 'c3_dict.py', 'c3_reverse_by_word.py', 'c3_set.py', 'c3_slice.py', 'c3_two_sum.py', 'c4.py', 'e4.py']


注意：**复制操作os里面没有。**可以读一个文件再写入另外一个文件来作为复制。但是，**shutil 库里面有，作为os的补充，其中有copufile()函数用来复制。**而且，最好使用shutil，而不要使用os。

2.使用shutil库进行操作，推荐使用这个

    
    import shutil
    shutil.copyfile('test.png','test1.png')


备注：**shutil库的用法需要补充**






