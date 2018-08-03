
# Python 递归修改文件名

需要把 .markdown 后缀的文件都改为 .md 后缀的。



```python
import os

def modify_filename(path):
    for p in os.listdir(path):
        p = os.path.join(path, p)
        if os.path.isdir(p):
            modify_filename(p)
        else:
            os.rename(p, os.path.join(os.path.dirname(p), os.path.basename(p).replace('markdown', 'md')))


modify_filename("C://Users//evo//Desktop//iterate")
```


说明一下：

需要用到 os 模块下的如下函数：

os.listdir(path)：某路径下的全部文件，包括目录
os.path.isdir(path)：判断是否为文件夹
os.path.dirname(path)/os.path.basename()：路径信息，文件名信息
os.path.join(,)：路径（dirname）和文件名（basename）的拼接（/）
os.rename()：修改文件名



## REF

- [Python Tricks（十）—— 递归修改文件名](https://blog.csdn.net/lanchunhui/article/details/51474540)
