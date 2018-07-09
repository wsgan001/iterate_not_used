

# TODO
- 现在只有关于List 的拷贝的内容。后续进行补全






# List的拷贝
## 问题缘由
实际上，在python的使用中，list的拷贝还是经常会用到的，因为有的函数会修改你传递进取的list，这样，你在传递之前就只能拷贝一份了，那么有各种拷贝的方法，在list里面的item也是list的时候，用那种呢？


## 代码示例



```python
import copy

a = [[10], 20]

b = a[:]
c = list(a)
d = a * 1
e = copy.copy(a)
f = copy.deepcopy(a)

a.append(21)
a[0].append(11)

print(id(a), a)

print(id(b), b)  # 可见b,c,d,e 这几种方式都是差不多的
print(id(c), c)
print(id(d), d)
print(id(e), e)
print(id(f), f)  # 还是使用copy.deepcopy 最保险
```

输出：

```text
1886551599112 [[10, 11], 20, 21]
1886551600328 [[10, 11], 20]
1886551691912 [[10, 11], 20]
1886551693640 [[10, 11], 20]
1886551693704 [[10, 11], 20]
1886551693384 [[10], 20]
```

可见，还是使用copy.deepcopy 最保险。



# REF
- [[Python] 正确复制列表的方法](http://www.cnblogs.com/ifantastic/p/3811145.html)
