Python pickle 的使用


# TODO
  * **最好将pickle的用法都总结一下。**




# 问题：'python pickle.dump TypeError: must be str, not bytes'
## 错误说明

在学习决策树的时候，使用 pickle.dump 将 tree 存放到文本里面：

```python
import pickle
fw = open(file_name, 'w')
pickle.dump(input_tree, fw)
fw.close()
```

然后报错了：

```text
Traceback (most recent call last):
  File "E:/01.Learn/ml_in_action/Ch03/trees.py", line 126, in <module>
    store_tree(my_tree,'tree.txt')
  File "E:/01.Learn/ml_in_action/Ch03/trees.py", line 110, in store_tree
    pickle.dump(input_tree, fw)
TypeError: write() argument must be str, not bytes
```

## 错误解决
输出的文件必须是二进制文件，即： fw = open(file_name, 'wb')



# REF
-   1. [Using pickle.dump - TypeError: must be str, not bytes](https://stackoverflow.com/questions/13906623/using-pickle-dump-typeerror-must-be-str-not-bytes)
