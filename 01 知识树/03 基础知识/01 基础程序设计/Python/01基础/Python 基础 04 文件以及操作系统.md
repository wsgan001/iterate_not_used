TODO

* 还需要补充，还是有很多读取文件的问题不确定的。

---

[TOC]

# 这次我们主要了解一下python 是如何处理文件的

这个教程中大部分时间都是使用pandas.read_csv来读取数据，所以了解pytohn如何处理文件是很重要的。

用内建的open函数能打开、读取、写入一个文件，要给open一个相对路径或绝对路径：


```
path = '../examples/segismundo.txt'
f = open(path)
for line in f:
    pass
```

上面的 `..` 表示返回上一个层级

文件中的每一个line都是有 end-of-line（EOL），行终结标志的，所以我们经常用下面的方法读到没有行终结标志的 list of line：==是这样吗？上面的方法读进来的是有EOL 的吗？确认下==


```python
lines = [x.rstrip() for x in open(path)]
lines
```

输出：
```
['Sueña el rico en su riqueza,',
 'que más cuidados le ofrece;',
 '',
 'sueña el pobre que padece',
 'su miseria y su pobreza;',
 '',
 'sueña el que a medrar empieza,',
 'sueña el que afana y pretende,',
 'sueña el que agravia y ofende,',
 '',
 'y en el mundo, en conclusión,',
 'todos sueñan lo que son,',
 'aunque ninguno lo entiende.',
 '']
```

当我们open文件后，一定要记得在结束后 close 文件，这样就不会占用操作系统的资源了：


```
f.close()
```

另一种方法可以在运行结束后自动关闭文件，用with语句。其实这样的用法更常见，推荐用with：

```
with open(patn) as f:
    lines = [x.rstrip() for x in f]
```

如果我们输入 `f = open(path, 'w')` ，那么会在 `../examples/` 下创建一个新的segismundo.txt文件，如果有同名文件的话会被覆盖。w 就表示写入的意思。==同名的话会被覆盖吗？那么怎么才能如果有就接着写，如过没有就创建？嗯，从下表看就是 a 模式==

即打开文件的同时就规定好我们是以哪种模式打开的，下面就是一些有效的打开模式：

| 模式 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| r    | 单一读取模式                                                 |
| w    | 单一写入模式；创建一个新文件（消除同名文件的数据）           |
| x    | 单一写入模式；创建一个新文件，但如果path已经存在的话会失败   |
| a    | 添加内容到已经存在的文件里（如果文件不存在的话创建新文件）   |
| r+   | 读取和写入双模式                                             |
| b    | 针对二进制文件的模式（'rb': 或 'wb'）                        |
| t    | 文本模式（自动编码bytes为Unicode）。如果不明说的话默认就是这种模式。添加t到其他模式后面（'rt' or 'xt'） |

这里有一张表格会更全一些：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180615/J2IFk4Cf9d.png?imageslim)

想要写文本到文件里的话，用file的write或writelines方法。比如，我们想给prof_mod.py写一个没有空白行的版本：

In [13]:

```python
with open('../examples/tmp.txt', 'w') as handle:
    handle.writelines(x for x in open(path) if len(x) > 1)
```

```python
with open('../examples/tmp.txt') as f:
    lines = f.readlines()
    print(lines)
```

输出：

```
['Sueña el rico en su riqueza,\n',
 'que más cuidados le ofrece;\n',
 'sueña el pobre que padece\n',
 'su miseria y su pobreza;\n',
 'sueña el que a medrar empieza,\n',
 'sueña el que afana y pretende,\n',
 'sueña el que agravia y ofende,\n',
 'y en el mundo, en conclusión,\n',
 'todos sueñan lo que son,\n',
 'aunque ninguno lo entiende.\n']
```

下面的方法，有需要再去看吧：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180615/H4KmD1eFd5.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180615/AGjjff0mJA.png?imageslim)

==到底什么时候用 flush？ 这个tell 和seek 到底是怎么用的？什么时候会用 closed 来判断是否关闭？如果没有关闭怎么办？强制关闭吗？怎么做？==

# Bytes and Unicode with Files

不论是读取还是写入，默认的python文件都是 text mode（文本模式），意味着你是与python string（i.e., Unicode）打交道。这和 binary mode（二进制模式）形成了对比。这里举个栗子（下面的文件包含non-ASCII字符，用UTF-8编码）：


```
with open(path) as f:
    chars = f.read(10)
chars
```
输出：
```
'Sueña el r'
```

UTF-8是一种长度可变的Unicode编码，所以我们想要从文件中读取一定数量的字符时，python会读取足够的bytes（可能从10到40）然后解码城我们要求数量的字符。而如果我们用'rb'模式的话，read只会读取相应的bytes数量：==嗯，也就是说，我输入的10 指的是 10个字，但是不一定有多少个 bytes==

```
with open(path, 'rb') as f:
    data = f.read(10)
data
```
输出：

```
b'Sue\xc3\xb1a el '
```

取决于文本的编码，你能够把 bytes 解码为 str，不过如果编码的Unicode字符不完整的话，是无法解码的：


```
data.decode('utf8')
data[:4].decode('utf8')# 不完整的话是无法解码的。
```
输出：
```
'Sueña el '
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-21-300e0af10bb7> in <module>()
----> 1 data[:4].decode('utf8')

UnicodeDecodeError: 'utf-8' codec can't decode byte 0xc3 in position 3: unexpected end of data
```

在使用 open 的时候，文本模式是有一个编码选项的，这能更方便我们把一种Unicode编码变为另一种：==这里我非常想知道，我在读取的时候怎么才能知道这个文本是用的什么编码？而且，如果是用某个编码进行读取，里面如果有无效字符，那么怎么处理？==

```
sink_path = '../examples/sink.txt'
with open(path) as source:
    with open(sink_path, 'xt', encoding='iso-8859-1') as sink:
        sink.write(source.read())
with open(sink_path, encoding='iso-8859-1') as f:
    print(f.read(10))
```
输出：
```
Sueña el r
```

注意：在任何模式下使用 seek 打开文件都可以，除了二进制模式。如果文件的指针落在bytes（Unicode编码）的中部，那么之后使用read会报错：==没想到还会这样！但是真的乎落到 byets 的中部吗？使用 binary 来读吗？确认下==


```
f = open(path)
f.read(5)
f.seek(4)# 这个会直接返回4
f.read(1)
f.close()
```

输出：==这个地方的 read(1) 为什么我试了几个 seek 的地方，都没有出现这个error？而且，我这个地方open 的时候 不是 b 的模式，那么 我这个地方的 read(1) 的1指的是什么？一个 byte？还是一个字？要确认下==
```
'Sueña'
4
---------------------------------------------------------------------------
UnicodeDecodeError                        Traceback (most recent call last)
<ipython-input-28-7841103e33f5> in <module>()
----> 1 f.read(1)

/Users/xu/anaconda/envs/py35/lib/python3.5/codecs.py in decode(self, input, final)
    319         # decode input (taking the buffer into account)
    320         data = self.buffer + input
--> 321         (result, consumed) = self._buffer_decode(data, self.errors, final)
    322         # keep undecoded input until the next call
    323         self.buffer = data[consumed:]

UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb1 in position 0: invalid start byte
```

如果我们在做数据分析时，经常使用 non-ASCII 文本数据的话，掌握 python 的 Unicode 功能是很有用的。

关于字符串和编码，这里推荐一篇文章，写的非常详细：[字符串，那些你不知道的事](http://liujiacai.net/blog/2015/11/20/strings/)



# REF
* [字符串，那些你不知道的事](http://liujiacai.net/blog/2015/11/20/strings/)
*
