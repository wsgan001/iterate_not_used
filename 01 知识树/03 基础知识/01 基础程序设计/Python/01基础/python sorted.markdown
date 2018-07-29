

  * 再《机器学习实战》书中总是看到sorted函数，不知道到底是怎么使用的，因此总结一下





* * *





# sorted函数介绍


sorted() 函数可以对所有可迭代的对象进行排序操作。

sort 与 sorted 区别：




  * sort 是应用在 list 上的方法，list 的 sort 方法返回的是对已经存在的列表进行操作。


  * sorted 可以对所有可迭代的对象进行排序操作，返回的是一个新的list，而不是在原来的基础上进行的操作。




# sorted函数说明


sorted(iterable[, cmp[, key[, reverse]]])

参数说明：




  * iterable -- 可迭代对象。


  * cmp -- 比较的函数，这个具有两个参数，参数的值都是从可迭代对象中取出，此函数必须遵守的规则为，大于则返回1，小于则返回-1，等于则返回0。


  * key -- 主要是用来进行比较的元素，只有一个参数，具体的函数的参数就是取自于可迭代对象中，指定可迭代对象中的一个元素来进行排序。


  * reverse -- 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。


返回值：返回重新排序的列表。

示例：


    import operator

    a = [5,7,6,3,4,1,2]
    b=sorted(a)
    print(a)
    print(b)


    a=[('b',2),('a',1),('c',3),('d',4)]
    b=sorted(a, key=lambda x:x[1])               # 利用key
    print(a)
    print(b)

    a= [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
    b=sorted(a, key=lambda s: s[2])  # 按年龄排序
    c=sorted(a, key=lambda s: s[2], reverse=True)  # 按降序
    print(a)
    print(b)
    print(c)




输出：


    [5, 7, 6, 3, 4, 1, 2]
    [1, 2, 3, 4, 5, 6, 7]
    [('b', 2), ('a', 1), ('c', 3), ('d', 4)]
    [('a', 1), ('b', 2), ('c', 3), ('d', 4)]
    [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
    [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
    [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
