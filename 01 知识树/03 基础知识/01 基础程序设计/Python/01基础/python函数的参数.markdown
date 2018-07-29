# python函数的参数



# TODO

	* **需要再看下，看的不仔细**



# ORIGIN


看视频的时候，看到说函数的可变参的时候，感觉需要记一下。

代码如下：


    print("********** 函数 *********")


​    
    def func_name(arg_1, arg_2):
        print(arg_1, arg_2)
        return arg_1, arg_2  # 实际上是返回一个元组，即一个只读的list


​    
    r = func_name(2, 3)
    print(type(r))
    print(r[0], r[1])
    
    print("********** 默认参数 *********")


​    
    def func(x, y=500):
        print('x=', x)
        print('y=', y)
        return x + y


​    
    print(func(100, 300))
    print(func(100))
    print(func(y=300, x=100))  # 这样就不用严格对应次序，而且可以知道对应的关系
    print(func(x=100))
    
    print("********** 可变参数  tuple *********")


​    
    def func(name, *numbers):
        # 实际上传进来的是一个元组
        # 进来很多参数之后 怎么处理呢？
        print(type(numbers))
        print(numbers)
        print(numbers[0])
        print(numbers[3])
        return "Done"


​    
    # 后面的所有的参数都会作为一个只读数组传进去
    # python不用考虑类型一致性的问题，只有在计算的时候才考虑
    func('Tom', 1, 2, 3, 4, 'abc')


​    
    # 一个星号是装到一个元组里面
    def my_print(*args):
        print(args)


​    
    my_print(1, 2, 3, 4, 'a', 'b', 'c')
    
    print("********** 可变参数  dict *********")


​    
    # 两个星号是装到一个key value 的字典里面
    # 两个星号会自动把后面的东西打包成一个字典
    def func(name, **kvs):  # 可变参数必须在最后
        print(name)
        print(type(kvs))
        print(kvs)


​    
    func('Tom', china='BeiJing', uk='London')
    
    print("********** 可变参数  *的注意 *********")


​    
    # 星号后面的变量一定要带名字
    def func(a, b, c, *, china, uk):
        print(china, uk)


​    
    # 如果不带名字会有如下的报错
    # #positional argument follows keyword argument
    func(1, 2, 3, china='BJ', uk='LD')
    
    print("********** 可变参数  混合 *********")


​    
    def func2(a, b, c=0, *args, **kvs):
        print(a, b, c)
        print(args)
        print(kvs)


​    
    func2(1, 2)
    func2(1, 2, 3)
    func2(1, 2, 3, 'a', 'b', 'c')
    func2(1, 2, 3, 'a', 'b', china='BJ', uk='LD')
    # 为了代码可读性高一些，做一些修改：
    func2(1, 2, 3, *('a', 'b'), **{'china': 'BJ', 'uk': 'LD'})


​    
    def my_print(*args):
        print(*args)


​    
    my_print('x=', 100, ';y=', 200)



输出：


    ********** 函数 *********
    2 3
    <class 'tuple'>
    2 3
    ********** 默认参数 *********
    x= 100
    y= 300
    400
    x= 100
    y= 500
    600
    x= 100
    y= 300
    400
    x= 100
    y= 500
    600
    ********** 可变参数  tuple *********
    <class 'tuple'>
    (1, 2, 3, 4, 'abc')
    1
    4
    (1, 2, 3, 4, 'a', 'b', 'c')
    ********** 可变参数  dict *********
    Tom
    <class 'dict'>
    {'china': 'BeiJing', 'uk': 'London'}
    ********** 可变参数  *的注意 *********
    BJ LD
    ********** 可变参数  混合 *********
    1 2 0
    ()
    {}
    1 2 3
    ()
    {}
    1 2 3
    ('a', 'b', 'c')
    {}
    1 2 3
    ('a', 'b')
    {'china': 'BJ', 'uk': 'LD'}
    1 2 3
    ('a', 'b')
    {'china': 'BJ', 'uk': 'LD'}
    x= 100 ;y= 200
