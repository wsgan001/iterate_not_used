## 缘由：

这个点，之前我遇到过，这次看视频又看到了讲了这个点，

## 代码如下：



```python
# 一维情况下是赋值，但是二维的时候是浅拷贝，也就是一个引用
li_2d = [[0] * 3] * 3  # 后面的乘法是做的一个引用，因此实际上是引用了三次
print(li_2d)
li_2d[0][0] = 100
print(li_2d)

li_2d = [[0] * 3 for i in range(3)]
li_2d[0][0] = 100
print(li_2d)
```

输出：

```text
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
[[100, 0, 0], [100, 0, 0], [100, 0, 0]]
[[100, 0, 0], [0, 0, 0], [0, 0, 0]]
```

从上面的例子可以看出，一维的时候 [0]*3 这样的每个是不重复的，但是 [[0,0,0]]*3 这样生成的二维的列表，实际上每一行都是指到同一块[0,0,0]上的，因此对[0][0]的修改会造成三处显示的数同时变化。

即：

```python
a = [[0, 0, 0]] * 3
print(a)
a[0][0] = 1
print(a)
```

输出：

```
[[0, 0, 0], [0, 0, 0], [0, 0, 0]]
[[1, 0, 0], [1, 0, 0], [1, 0, 0]]
```
