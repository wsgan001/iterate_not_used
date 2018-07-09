# TODO
- 之前我用这个enumerate 的时候发现了个问题，就是 对一个list enumerate 然后进行遍历之后，再遍历这个 enumerate 的结果就不行了，好像这个结果并没有保存下来。<font color=red>确认下</font>
-


# 缘由：


之前看到别人for循环中使用enumerate，将一个list转为index和value，有些厉害，总结一下。
这个enumerate用起来还是挺简单的，现在看来，是真的很方便。


# 要点：


代码如下：
```python
#enumerate 在列表或元组既要遍历索引又要遍历元素的时候使用
l = [1, 2, 3]
for i in range(len(l)):
    print(i, l[i])

for index, value in enumerate(l):
    print(index, value)
print()

e=enumerate(l)
print(e)
print(type(e))#格式为enumerate
```

输出：

```text
0 1
1 2
2 3
0 1
1 2
2 3

<enumerate object at 0x000001D802AA74C8>
<class 'enumerate'>
```
