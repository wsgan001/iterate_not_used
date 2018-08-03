python 函数式编程与lambda表达式

## 缘由：


实际上，再C#的时候就经常使用lambda表达式，但是一直有种不透彻的感觉，因此还是要总结下：


## 要点：




### 1.概念与简单的使用


















python 使用 lambda 来创建匿名函数。




  * lambda只是一个表达式，函数体比def简单很多。

  * lambda的主体是一个表达式，而不是一个代码块。仅仅能在lambda表达式中封装有限的逻辑进去。

  * lambda函数拥有自己的命名空间，且不能访问自有参数列表之外或全局命名空间里的参数。

  * 虽然lambda函数看起来只能写一行，却不等同于C或C++的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

  * **很重要的一点**，Lambda表达式可以看起来很厉害。什么是函数式编程？就是类似于全篇程序都用python中lambda这样的一行代码来解决问题。


Lambda函数的语法：   lambda [arg1 [,arg2,.....argn]]:expression

简单的使用：


















    my_sum = lambda arg1, arg2: arg1 + arg2
    print(my_sum(10,20))
    
    g = lambda x: x * 2
    print(g(3))
    print((lambda x: x * 2)(4))


输出：


    30
    6
    8




### 2.reduce与lambda函数


Python中的reduce内建函数是一个二元操作函数，他用来将一个数据集合(列表，元组等)中的所有数据进行如下操作：传给reduce中的函数func() (必须是一个二元操作函数)先对集合中的第1，2个数据进行操作，得到的结果再与第三个数据用func()函数运算，最后得到一个结果。

即：reduce就是要把一个list给缩成一个值。所以你必须用二元操作函数。


    from functools import reduce
    
    l = [1, 2, 3, 4, 5]
    # 把list中的值，一个个放进lamda的x,y中
    print(reduce(lambda x, y: x + y, l))
    # 如果你给出一个初始值，可以放在list后面, x开始的时候被赋值为10，然后依次
    print(reduce(lambda x, y: x + y, l, 10))


输出：


    15
    25


注：**想知道reduce这个函数是在什么时候用到，感觉这个函数的使用有些奇怪**

**当我再次看到MapReduce这个名字的时候，我震惊了。。**


### 3.map与lambda函数


map函数应用于每一个可迭代的项，返回的是一个结果list。如果有其他的可迭代参数传进来，map函数则会把每一个参数都以相应的处理函数进行迭代处理。map()函数接收两个参数，一个是函数，一个是序列，map将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回。

格式：map(func, seq1[, seq2...] )

Python函数式编程中的map()函数是将func作用于seq中的每一个元素，并用一个列表给出返回值。本质上是把原有的list根据lambda法则变成另外一个list

代码如下：


    l = [1, 2, 3]
    # Py3里，这样的map外面需要套个list：
    # 这是为了让里面的值给显示出来，要不然你会得到这是个map函数
    new_list = list(map(lambda i: i + 1, l))
    print(new_list)
    
    func = lambda x, y: x + y
    print(func(3, 5))
    
    # WARINING 这个地方要注意，map中的lambda表达式后面的参数，一定要是列表格式的
    res=list(map(lambda x,y:x+y,[3],[5]))
    print(res)


​    
    # 也可以对两个数组进行操作
    l1 = [1, 2, 3]
    l2 = [2, 3, 4]
    res = list(map(lambda x1, x2: x1 + x2, l1, l2))
    print(res)



输出：


    [2, 3, 4]
    8
    [8]
    [3, 5, 7]


注：这种对于map的用法还是第一次见，下次看到类似的再记录下，感觉很厉害的用法。另外对于map本身的用法再看下，**又看到了lambda与map的配合，感觉是挺厉害的**


### 4.filter与lambda函数


filter()函数可以对序列做过滤处理，就是说可以使用一个自定的函数过滤一个序列，把序列的每一项传到自定义的过滤函数里处理，并返回结果做过滤。最终一次性返回过滤后的结果。 和map()类似，filter()也接收一个函数和一个序列。和map()不同的时，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

语法： filter(func, seq)


    l = [1, 2, 3, 4]
    new_list = list(filter(lambda x: x < 3, l))
    print(new_list)


输出：


    [1, 2]


注：filter的本身的用法再看下


### 5.一个厉害的综合的例子，使用了上面的map、reduce、filter ，而且体现出了函数式编程的厉害




    # 函数式编程
    # 将偶数取出来，然后乘以3，再转化为string
    from functools import reduce


​    
    def even_filter(nums):
        return filter(lambda x: x % 2 == 0, nums)


​    
    def multiply_by_three(nums):
        return map(lambda x: x * 3, nums)


​    
    def convert_to_string(nums):
        return map(lambda x: 'The Number: %s' % x, nums)


​    
    # 可以这样调用，但是看起来就不是很美观
    nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    pipeline = convert_to_string(
        multiply_by_three(
            even_filter(nums)
        ))
    print(list(pipeline))


​    
    # 也可以通过这样来调用，厉害
    def pipeline_func(data, funcs):
        # 将data作为初始值，
        # 将funcs的第一个和第二个函数拿出来，执行出来一个结果然后
        # 这个地方理解的不透彻？
        return reduce(lambda x, y: y(x), funcs, data)


​    
    res = pipeline_func(nums,
                        [even_filter, multiply_by_three, convert_to_string])
    print(list(res))


输出：


    ['The Number: 6', 'The Number: 12', 'The Number: 18', 'The Number: 24', 'The Number: 30']
    ['The Number: 6', 'The Number: 12', 'The Number: 18', 'The Number: 24', 'The Number: 30']


注：**上面式子中的reduce(lambda x,y:y(x),funs,data)部分仍然理解的不透彻**


## COMMENT：


不知道lambda还有没有什么精妙的用法，还是说上面的reduce，map，filter 已经足以对应所有的情况了？


## REF：


1.[函数式编程](https://coolshell.cn/articles/10822.html)
