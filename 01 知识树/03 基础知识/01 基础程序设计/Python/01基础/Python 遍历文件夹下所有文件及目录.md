# TODO

  1. **需要把所有的遍历相关的使用都总结进来**

# 缘由
会在遍历文件进行传送的时候用到。

# 遍历的方法
遍历文件夹中的所有子文件夹及子文件使用os.walk()方法非常简单。 
语法格式大致如下：

```
os.walk(top[, topdown=True[, onerror=None[, followlinks=False]]])
```

 - top – 根目录下的每一个文件夹(包含它自己), 产生3-元组 (dirpath, dirnames, 
  filenames)【文件夹路径, 文件夹名字, 文件名】。
 - topdown –可选，为True或者没有指定, 一个目录的的3-元组将比它的任何子文件夹的3-元组先产生 
  (目录自上而下)。如果topdown为 False, 一个目录的3-元组将比它的任何子文件夹的3-元组后产生 (目录自下而上)。
 - onerror – 可选，是一个函数; 它调用时有一个参数, 一个OSError实例。报告这错误后，继续walk,或者抛出exception终止walk。
 - followlinks – 设置为 true，则通过软链接访问目录。


# 示例
显示目录下所有文件
```python
#conding=utf8  
import os 

g = os.walk(r"e:\test")  

for path,dir_list,file_list in g:  
    for file_name in file_list:  
        print(os.path.join(path, file_name) )
```
显示所有子目录
```python
#conding=utf8  
import os 

g = os.walk("e:\test")  

for path,dir_list,file_list in g:  
    for dir_name in dir_list:
        print(os.path.join(path, dir_name) )
```

# REF
  1. [Python遍历文件夹下的所有文件和目录](https://blog.csdn.net/mighty13/article/details/77995857)