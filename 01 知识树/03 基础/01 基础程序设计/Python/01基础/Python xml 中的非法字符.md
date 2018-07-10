# 缘由

在使用Python 处理 xml 的时候，经常会说：invalid-token，非法字符，因此，要在处理 xml 文件的时候，先对非法字符进行过滤。
根据W3C的标准，以下16进制的字符是不被允许出现在XML文件中的：

 - //x00-//x08 
 - //x0b-//x0c 
 - //x0e-//x1f

所以，需要对在这3个范围段的字符进行排除。

具体如下：
```python
import re
import sys
default_encoding = 'utf-8'
if sys.getdefaultencoding() != default_encoding:
    reload(sys)
    sys.setdefaultencoding(default_encoding)

path1 = 'wordpress-xml/tobeafriendoftime.wordpress.2018-06-10.xml'
path2 = 'wordpress-xml/tobeafriendoftime.wordpress.2018-06-10.new.xml'

file1 = open(path1, 'r')
file2 = open(path2, 'w')

text = re.sub(u"[\x00-\x08\x0b-\x0c\x0e-\x1f]+", u"", file1.read())
file2.write(text)
file2.flush()
file1.close()
file2.close()
```

# REF

  1. [xml非法字符](https://blog.csdn.net/a_heng/article/details/5287390)