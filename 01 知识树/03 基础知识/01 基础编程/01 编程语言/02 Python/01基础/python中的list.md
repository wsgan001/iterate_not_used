
## 


python中的list还是挺好用的，但是对于切片的时候的负步长还是有些不是很清楚。


## 要点：




### 元素的位置：




    l=[1,2,3,'a',[2],{'s':2}]
    print(l.index(1))
    print(l.index([2]))
    # 如果不确定元素在 list里面的话 一定要用try catch来捕捉异常
    print(l.index(-1))


输出：


    0
    4
    Traceback (most recent call last):
      File "E:\11.ProgramFiles\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py", line 2881, in run_code
        exec(code_obj, self.user_global_ns, self.user_ns)
      File "<ipython-input-18-9ac3e638a4c8>", line 5, in <module>
        print(l.index(-1))
    ValueError: -1 is not in list


注意：如果不确定元素在list里面的话，一定要用try catch来捕捉异常


### 添加元素：




    # 添加元素
    l_a=[1,2,3]
    l_a.append(4)
    l_a.append(5)
    print(l_a)
    l_b=[6,7,8]
    l_a.extend(l_b)
    print(l_a)
    l_a.append(l_b)#注意 append与extend的对比
    print(l_a)


输出：


    [1, 2, 3, 4, 5]
    [1, 2, 3, 4, 5, 6, 7, 8]
    [1, 2, 3, 4, 5, 6, 7, 8, [6, 7, 8]]


注意：append使用来添加元素的，extend是用来合并列表的


### 切片：




    li = list(range(10))
    print(li)
    
    # 切片  [start:end:steps]  是大于等于start  小于end
    print(li[2:5:1])
    print(li[:4])
    print(li[5:])
    print(li[0:20:3])  # 注意：这里面越界的话是按实际的大小来计算的
    
    # 负值的处理
    print(li[5:-2])
    print(li[9:0:-1])
    print(li[9::-1])  # 为什么这个包括0？
    print(li[::-2])
    
    # 切片生成一个新的对象
    print(li)
    re_li = li[::-1]
    print(re_li)


输出：


    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    [2, 3, 4]
    [0, 1, 2, 3]
    [5, 6, 7, 8, 9]
    [0, 3, 6, 9]
    [5, 6, 7]
    [9, 8, 7, 6, 5, 4, 3, 2, 1]
    [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
    [9, 7, 5, 3, 1]
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]


注意：切片的例子后面再补充一下，尤其是比较奇怪的例子




