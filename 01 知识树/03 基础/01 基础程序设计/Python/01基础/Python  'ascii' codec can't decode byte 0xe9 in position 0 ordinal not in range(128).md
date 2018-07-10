# 缘由

在使用 Python2.7 读取文件的时候，提示说：
'ascii' codec can't decode byte 0xe9 in position 0: ordinal not in range(128)"

# 解决办法
要添上：

```python
import sys
default_encoding = 'utf-8'
if sys.getdefaultencoding() != default_encoding:
    reload(sys)
    sys.setdefaultencoding(default_encoding)
```

# REF

  1. [python 处理中文时出现的错误'ascii' codec can't decode byte 0xe9 in position 0: ordinal not in range(128)" 解决方法](https://blog.csdn.net/andoring/article/details/6624533)