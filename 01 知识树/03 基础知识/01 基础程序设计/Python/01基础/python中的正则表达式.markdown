---
author: evo
comments: true
date: 2018-03-23 08:45:14+00:00
layout: post
link: http://106.15.37.116/2018/03/23/python-re/
slug: python-re
title: python中的正则表达式
wordpress_id: 677
categories:
- 随想与反思
tags:
- '@todo'
- python
---

<!-- more -->


## 缘由：


正则表达式，每次学习都感觉自己会了，但是过一段时间又忘记了，因为很少会用到，因此这里总结一下，加深印象，在爬虫的时候经常会用到来从网页中提取某些信息。


## 要点：




### 1.正则表达式语法


正则表达式 (Regular Expression) 又称 RegEx, 是用来匹配字符的一种工具. 在一大串字符中寻找你需要的内容. 它常被用在很多方面, 比如网页爬虫, 文稿整理, 数据筛选等等.

    
    import re
    
    # matching string
    pattern1 = "cat"
    pattern2 = "bird"
    string = "dog runs to cat"
    print(pattern1 in string)    # True
    print(pattern2 in string)    # False
    
    
    # regular expression
    pattern1 = "cat"
    pattern2 = "bird"
    string = "dog runs to cat"
    print(re.search(pattern1, string))  # <_sre.SRE_Match object; span=(12, 15), match='cat'>
    print(re.search(pattern2, string))  # None
    
    
    # multiple patterns ("run" or "ran")
    ptn = r"r[au]n"       # start with "r" means raw string
    print(re.search(ptn, "dog runs to cat"))    # <_sre.SRE_Match object; span=(4, 7), match='run'>
    
    
    # continue
    print(re.search(r"r[A-Z]n", "dog runs to cat"))     # None
    print(re.search(r"r[a-z]n", "dog runs to cat"))     # <_sre.SRE_Match object; span=(4, 7), match='run'>
    print(re.search(r"r[0-9]n", "dog r2ns to cat"))     # <_sre.SRE_Match object; span=(4, 7), match='r2n'>
    print(re.search(r"r[0-9a-z]n", "dog runs to cat"))  # <_sre.SRE_Match object; span=(4, 7), match='run'>
    
    
    # \d : decimal digit
    print(re.search(r"r\dn", "run r4n"))                # <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    # \D : any non-decimal digit
    print(re.search(r"r\Dn", "run r4n"))                # <_sre.SRE_Match object; span=(0, 3), match='run'>
    # \s : any white space [\t\n\r\f\v]
    print(re.search(r"r\sn", "r\nn r4n"))               # <_sre.SRE_Match object; span=(0, 3), match='r\nn'>
    # \S : opposite to \s, any non-white space
    print(re.search(r"r\Sn", "r\nn r4n"))               # <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    # \w : [a-zA-Z0-9_]
    print(re.search(r"r\wn", "r\nn r4n"))               # <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    # \W : opposite to \w
    print(re.search(r"r\Wn", "r\nn r4n"))               # <_sre.SRE_Match object; span=(0, 3), match='r\nn'>
    # \b : empty string (only at the start or end of the word)
    print(re.search(r"\bruns\b", "dog runs to cat"))    # <_sre.SRE_Match object; span=(4, 8), match='runs'>
    # \B : empty string (but not at the start or end of a word)
    print(re.search(r"\B runs \B", "dog   runs  to cat"))  # <_sre.SRE_Match object; span=(8, 14), match=' runs '>
    # \\ : match \
    print(re.search(r"runs\\", "runs\ to me"))          # <_sre.SRE_Match object; span=(0, 5), match='runs\\'>
    # . : match anything (except \n)
    print(re.search(r"r.n", "r[ns to me"))              # <_sre.SRE_Match object; span=(0, 3), match='r[n'>
    # ^ : match line beginning
    print(re.search(r"^dog", "dog runs to cat"))        # <_sre.SRE_Match object; span=(0, 3), match='dog'>
    # $ : match line ending
    print(re.search(r"cat$", "dog runs to cat"))        # <_sre.SRE_Match object; span=(12, 15), match='cat'>
    # ? : may or may not occur
    print(re.search(r"Mon(day)?", "Monday"))            # <_sre.SRE_Match object; span=(0, 6), match='Monday'>
    print(re.search(r"Mon(day)?", "Mon"))               # <_sre.SRE_Match object; span=(0, 3), match='Mon'>
    
    
    # multi-line
    string = """
    dog runs to cat.
    I run to dog.
    """
    print(re.search(r"^I", string))                     # None
    print(re.search(r"^I", string, flags=re.M))         # <_sre.SRE_Match object; span=(18, 19), match='I'>
    
    
    # * : occur 0 or more times
    print(re.search(r"ab*", "a"))                       # <_sre.SRE_Match object; span=(0, 1), match='a'>
    print(re.search(r"ab*", "abbbbb"))                  # <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    
    # + : occur 1 or more times
    print(re.search(r"ab+", "a"))                       # None
    print(re.search(r"ab+", "abbbbb"))                  # <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    
    # {n, m} : occur n to m times
    print(re.search(r"ab{2,10}", "a"))                  # None
    print(re.search(r"ab{2,10}", "abbbbb"))             # <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    
    
    # group
    match = re.search(r"(\d+), Date: (.+)", "ID: 021523, Date: Feb/12/2017")
    print(match.group())                                # 021523, Date: Feb/12/2017
    print(match.group(1))                               # 021523
    print(match.group(2))                               # Date: Feb/12/2017
    
    match = re.search(r"(?P<id>\d+), Date: (?P<date>.+)", "ID: 021523, Date: Feb/12/2017")
    print(match.group('id'))                            # 021523
    print(match.group('date'))                          # Date: Feb/12/2017
    
    # findall
    print(re.findall(r"r[ua]n", "run ran ren"))         # ['run', 'ran']
    
    # | : or
    print(re.findall(r"(run|ran)", "run ran ren"))      # ['run', 'ran']
    
    # re.sub() replace
    print(re.sub(r"r[au]ns", "catches", "dog runs to cat"))     # dog catches to cat
    
    # re.split()
    print(re.split(r"[,;\.]", "a;b,c.d;e"))             # ['a', 'b', 'c', 'd', 'e']
    
    
    # compile
    compiled_re = re.compile(r"r[ua]n")
    print(compiled_re.search("dog ran to cat"))     # <_sre.SRE_Match object; span=(4, 7), match='ran'>


输出：

    
    True
    False
    <_sre.SRE_Match object; span=(12, 15), match='cat'>
    None
    <_sre.SRE_Match object; span=(4, 7), match='run'>
    None
    <_sre.SRE_Match object; span=(4, 7), match='run'>
    <_sre.SRE_Match object; span=(4, 7), match='r2n'>
    <_sre.SRE_Match object; span=(4, 7), match='run'>
    <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    <_sre.SRE_Match object; span=(0, 3), match='run'>
    <_sre.SRE_Match object; span=(0, 3), match='r\nn'>
    <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    <_sre.SRE_Match object; span=(4, 7), match='r4n'>
    <_sre.SRE_Match object; span=(0, 3), match='r\nn'>
    <_sre.SRE_Match object; span=(4, 8), match='runs'>
    <_sre.SRE_Match object; span=(5, 11), match=' runs '>
    <_sre.SRE_Match object; span=(0, 5), match='runs\\'>
    <_sre.SRE_Match object; span=(0, 3), match='r[n'>
    <_sre.SRE_Match object; span=(0, 3), match='dog'>
    <_sre.SRE_Match object; span=(12, 15), match='cat'>
    <_sre.SRE_Match object; span=(0, 6), match='Monday'>
    <_sre.SRE_Match object; span=(0, 3), match='Mon'>
    None
    <_sre.SRE_Match object; span=(18, 19), match='I'>
    <_sre.SRE_Match object; span=(0, 1), match='a'>
    <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    None
    <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    None
    <_sre.SRE_Match object; span=(0, 6), match='abbbbb'>
    021523, Date: Feb/12/2017
    021523
    Feb/12/2017
    021523
    Feb/12/2017
    ['run', 'ran']
    ['run', 'ran']
    dog catches to cat
    ['a', 'b', 'c', 'd', 'e']
    <_sre.SRE_Match object; span=(4, 7), match='ran'>


**上面这个没有仔细看，要看下，顺便看下还有什么需要补充的**


### 2.简单的使用


math与search的使用

    
    import re
    
    sentence = 'dog cat dog'
    r1 = r'dog'
    r2 = r'cat'
    
    m1 = re.match(r1, sentence)
    print(m1)
    print(m1.group())
    m2 = re.match(r2, sentence)# 返回的是None 因为没有cat开头的句子
    print(m2)
    
    s = re.search(r1, sentence)
    print(s)
    print(s.group())
    all = re.findall(r2, sentence)
    print(all)


输出：

    
    <_sre.SRE_Match object; span=(0, 3), match='dog'>
    dog
    None
    <_sre.SRE_Match object; span=(0, 3), match='dog'>
    dog
    ['cat']


注：看来match和search虽然作用不同，但是返回的结果类型是一样的 都是SRE_Match object。

group的使用：

    
    import re
    
    sentence = 'Doe, John: 555-1212'
    r = r'(\w+), (\w+): (\S+)'
    m = re.search(r, sentence)
    print(m.group(1))
    print(m.group(2))
    print(m.group(3))
    print(m.group(0))


输出：

    
    Doe
    John
    555-1212
    Doe, John: 555-1212




### 3.几个例子：


提取邮箱地址：

    
    import re
    
    str = 'purple alice-b@google.com monkey dishwasher'
    match = re.search(r'\w+@\w+', str)
    if match:
        print(match.group())  ## 'b@google',因为\w不能匹配到地址中的'-'和'.'
    
    match = re.search(r'[\w.-]+@[\w.-]+', str)
    if match:
        print(match.group())  ## 'alice-b@google.com'




## 输出：



    
    b@google
    alice-b@google.com




## 




## COMMENT：


**这两个例子太简单了，找一下有没有复杂的，比如提取电话号码 或者邮箱等等**




## REF：





 	
  1. [正则表达式](https://morvanzhou.github.io/tutorials/python-basic/basic/13-10-regular-expression/)


