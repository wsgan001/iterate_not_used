
## 缘由：


在kaggle上看到别人在做数据挖掘项目的时候，总是不断的分析，不断的画图，来分析哪些特征是重要的，虽然有的时候使用的是matplot，有的时候使用的是seaborn，但是matplot对于平时的使用来说还是很有用的，比如对于少量的数据，想大概看一下是怎么分布的，数据大概有哪些特种，就可以使用matplot画出来。


## 要点：




### 1.简单的画图 plot show




    # 最简单的画图 plot show
    import matplotlib.pyplot as plt
    import numpy as np

    x = np.linspace(-1, 1, 50)  # 从-1到1均匀取50个点
    y = x ** 2
    plt.plot(x, y)  # 横坐标 纵坐标
    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab653b995951.png)





### 2.在两个figure上面画图，画出两张图




    #画出两张图 两个figure
    import numpy as np
    import matplotlib.pyplot as plt

    x = np.linspace(-1, 1, 50)
    y1 = x ** 2
    y2 = x ** 3

    # 每个figure下面的所有的东西都是属于它的，除非再声明一下
    # figure下面的plot都是画到这个figure里面的
    plt.figure()
    plt.plot(x, y1)

    plt.figure(num=10, figsize=(8, 5))  # num为空的时候编号是默认从上到下排列出来的
    plt.plot(x, y2)
    plt.plot(x, y1, color='red', linewidth=3.0, linestyle='--')
    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6547f93615.png)





![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab654889dcf5.png)





### 3.figure中的坐标轴刻度改成形容程度的文字




    # figure中的坐标轴刻度改成形容程度的文字

    import numpy as np
    import matplotlib.pyplot as plt

    x = np.linspace(-1, 1, 50)
    y1 = x ** 2
    y2 = x ** 3
    y3 = x ** 4
    y4 = x ** 5

    plt.figure()
    plt.plot(x, y2)
    plt.plot(x, y1, color='red', linewidth=3, linestyle='--')

    plt.xlim((-1, 2))  # 设定取值范围
    plt.ylim((-2, 3))

    plt.xlabel('I am x')  # 坐标轴的名称
    plt.ylabel('I am y')

    # 改变坐标轴的tick
    new_ticks = np.linspace(-1, 2, 5)
    print(new_ticks)
    plt.xticks(new_ticks)  # 讲x的ticks换成设定的
    # 对y的ticks进行一一对应
    # 前后加$ 是因为$里面就是读取的地方。 这样的话可以在里面设定一些特殊符号比如\alpha  这个在写论文的时候会用到
    # 在$里面的时候空格是显示不出来的，要转制\  而且前面要加上r
    plt.yticks([-2, -1.8, -1, 1.22, 3, ],
               [r'$really\ bad$', r'$bad\ \alpha$', 'normal', 'good', 'very good'])



    plt.show()


输出：


    [-1.   -0.25  0.5   1.25  2.  ]




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab654ca9a5cd.png)





### 4.移动坐标轴的位置




    # 将坐标轴移动位置

    import numpy as np
    import matplotlib.pyplot as plt

    x = np.linspace(-1, 1, 50)
    y1 = x ** 2
    y2 = x ** 3
    y3 = x ** 4
    y4 = x ** 5

    plt.figure()
    plt.plot(x, y2)
    plt.plot(x, y1, color='red', linewidth=3, linestyle='--')

    plt.xlim((-1, 2))  # 设定取值范围
    plt.ylim((-2, 3))

    plt.xlabel('I am x')  # 坐标轴的名称
    plt.ylabel('I am y')

    # 改变坐标轴的tick
    new_ticks = np.linspace(-1, 2, 5)
    print(new_ticks)
    plt.xticks(new_ticks)  # 讲x的ticks换成设定的
    # 对y的ticks进行一一对应
    # 前后加$ 是因为$里面就是读取的地方。 这样的话可以在里面设定一些特殊符号比如\alpha  这个在写论文的时候会用到
    # 在$里面的时候空格是显示不出来的，要转制\  而且前面要加上r
    plt.yticks([-2, -1.8, -1, 1.22, 3, ],
               [r'$really\ bad$', r'$bad\ \alpha$', 'normal', 'good', 'very good'])

    # 修改坐标轴的位置 将坐标轴挪到figure中间
    # gca='get current axis'
    ax = plt.gca()
    # spines 就是figure的四个边框
    ax.spines['right'].set_color('none')  # 将上面的轴设置为没有
    ax.spines['top'].set_color('none')
    ax.xaxis.set_ticks_position('bottom')  # 设定x坐标轴用哪一个轴代替
    ax.yaxis.set_ticks_position('left')
    # data:通过值来选择，axes:百分比   outward:
    # 纵坐标的-1作为原点
    ax.spines['bottom'].set_position(('data', -1))
    ax.spines['left'].set_position(('data', 0))

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6551d22ddc.png)





### 5.给图像添加图例




    # 添加图例

    import numpy as np
    import matplotlib.pyplot as plt

    x = np.linspace(-1, 1, 50)
    y1 = x ** 2
    y2 = x ** 3
    y3 = x ** 4
    y4 = x ** 5

    plt.figure()

    plt.xlim((-1, 2))  # 设定取值范围
    plt.ylim((-2, 3))

    plt.xlabel('I am x')  # 坐标轴的名称
    plt.ylabel('I am y')

    # 改变坐标轴的tick
    new_ticks = np.linspace(-1, 2, 5)
    print(new_ticks)
    plt.xticks(new_ticks)  # 讲x的ticks换成设定的
    # 对y的ticks进行一一对应
    # 前后加$ 是因为$里面就是读取的地方。 这样的话可以在里面设定一些特殊符号比如\alpha  这个在写论文的时候会用到
    # 在$里面的时候空格是显示不出来的，要转制\  而且前面要加上r
    plt.yticks([-2, -1.8, -1, 1.22, 3, ],
               [r'$really\ bad$', r'$bad\ \alpha$', 'normal', 'good', 'very good'])

    # 打上图例：legend
    # 如果想传到legend里面去，一定要加上一个逗号
    line1, = plt.plot(x, y2, label='up')
    line2, = plt.plot(x, y1, color='red', linewidth=3, linestyle='--',
                      label='down')
    # handles
    # loc ->location  upper right ,best,
    plt.legend(handles=[line1, line2],
               labels=['aaa', 'bbb'],  # 对应的是line1和line2 而不再使用plt.plot里面设定的label
               loc='best')

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6554ecb525.png)





### 6.在图上进行文字和箭头的标注，以及特殊字符的写法




    # 在图上进行文字和箭头的标注 以及特殊字符的写法

    import matplotlib.pyplot as plt
    import numpy as np

    x = np.linspace(-3, 3, 50)
    y = 2 * x + 1
    plt.figure(num=1, figsize=(8, 5))
    plt.plot(x, y)
    ax = plt.gca()
    ax.spines['right'].set_color('none')
    ax.spines['top'].set_color('none')
    ax.xaxis.set_ticks_position('bottom')
    ax.spines['bottom'].set_position(('data', 0))
    ax.yaxis.set_ticks_position('left')
    ax.spines['left'].set_position(('data', 0))

    # 将点显示出来
    x0 = 1
    y0 = 2 * x0 + 1
    plt.scatter(x0, y0, s=50, color='b')

    # 一条虚线
    plt.plot([x0, x0], [y0, 0], 'k--', lw=2.5)  # k代表的是black --是线条的style

    #
    # 标注
    plt.annotate(r'$2x+1=%s$' % y0,
                 xy=(x0, y0),  # xy的坐标
                 xycoords='data',
                 xytext=(+30, -30),
                 textcoords='offset points',
                 fontsize=16,
                 arrowprops=dict(arrowstyle='->', connectionstyle='arc3,rad=0.2'))  # 箭头的设定

    # 还有一种方式
    plt.text(x=-3.7, y=3.,
             s=r'$This\ is\ the\ some\ text\ \mu\ \sigma_i\ \alpha_t$',# 注意角标的写法
             fontdict={'size': 16, 'color': 'r'})

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab655960ee47.png)





### 7.将被线条挡住的刻度文字显示在前面




    # 将被线条挡住的刻度文字显示在前面
    # 图上的数据比较多的时候，坐标轴的tick被挡住了 因此图层还是很有必要知道的，不然很多刻度会被线挡住


    import matplotlib.pyplot as plt
    import numpy as np

    x = np.linspace(-3, 3, 50)
    y = 2 * x + 1
    plt.figure(num=1, figsize=(8, 5))
    plt.plot(x, y, linewidth=10, zorder=1)  # 设置稍低的图层
    plt.ylim(-2, 2)
    ax = plt.gca()
    ax.spines['right'].set_color('none')
    ax.spines['top'].set_color('none')
    ax.xaxis.set_ticks_position('bottom')
    ax.spines['bottom'].set_position(('data', 0))
    ax.yaxis.set_ticks_position('left')
    ax.spines['left'].set_position(('data', 0))

    for label in ax.get_xticklabels() + ax.get_yticklabels():
        label.set_fontsize(12)
        # bbox 是label后面的小框框
        label.set_bbox(dict(facecolor='white',
                            edgecolor='None',
                            alpha=0.0,  # 0是完全透明的box 1是完全不透明的box
                            zorder=2))  # 设置图层较高，就不会被线挡住

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab655dccf19a.png)





### 8.散点图




    # 散点图

    import matplotlib.pyplot as plt
    import numpy as np

    n = 1024
    X = np.random.normal(loc=0, scale=1, size=n)
    Y = np.random.normal(0, 1, size=n)
    T = np.arctan2(Y, X)  # for color value

    # c即color
    plt.scatter(X, Y, s=75, c=T, cmap=None, alpha=0.5)
    plt.scatter(np.linspace(-1,1,10),np.linspace(-1,1,10),c='red')
    plt.xlim(-1.5, 1.5)
    plt.ylim(-1.5, 1.5)
    plt.xticks(())  # 将x的ticks和y的ticks都设置为没有
    plt.yticks(())
    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab656072db8d.png)





### 9.条形图




    #条形图

    import matplotlib.pyplot as plt
    import numpy as np

    n = 12
    X = np.arange(n)
    Y1 = (1 - X / float(n)) * np.random.uniform(0.5, 1.0, n)
    Y2 = (1 - X / float(n)) * np.random.uniform(0.5, 1, n)

    # 画出条形图
    plt.bar(X, +Y1, facecolor='#9999ff', edgecolor='white')
    plt.bar(X, -Y2, facecolor='#ff9999', edgecolor='white')

    # 添加标注的值
    for x, y in zip(X, Y1):
        # ha horizontal 水平对齐方式 va纵向对齐方式
        # 当你设定ha和va之后，x和y就可以不用调整，因为这个文字会把底部的水平的中心点对应到你的x和y点上
        plt.text(x, y, '%0.2f' % y, ha='center', va='bottom')  # 保留两位小数
    for x, y in zip(X, -Y2):
        plt.text(x, y, '%0.2f' % y, ha='center', va='top')  # 这个也是，会把顶部的中心点对应到你的x和y点上

    plt.xlim(-0.5, n)
    plt.ylim(-1.5, 1.5)

    # plt.xticks(())# 将标注隐藏起来
    # plt.yticks(())

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6564079049.png)





### 10.等高线




    # 等高线
    import matplotlib.pyplot as plt
    import numpy as np


    def f(x, y):
        # the hight function
        return (1 - x / 2 + x ** 3 + y ** 4) * np.exp(-x ** 2 - y ** 2)


    n = 256
    x = np.linspace(-4, 4, n)
    y = np.linspace(-4, 4, n)
    X, Y = np.meshgrid(x, y)  # 将X，Y绑定成我们的网格值

    # 开始画颜色  f每一个值对应一个颜色的点
    slice_num = 20  # 即等高线分的等级 0的时候有1条
    plt.contourf(X, Y, f(X, Y), slice_num, alpha=0.75, cmap=plt.cm.cool)  # cool和hot

    # 开始画等高线
    C = plt.contour(X, Y, f(X, Y), slice_num, colors='black', linewidth=0.5)

    # 添加标识文字
    plt.clabel(C, inline=True, fontsize=10)

    plt.xticks(())
    plt.yticks(())
    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6566c8e15c.png)





### 11.将array里面的数据画成一个图片




    # 将array里面的数据画成一个图片

    import matplotlib.pyplot as plt
    import numpy as np

    # image data
    a = np.random.random(9).reshape(3, 3)
    a[0, 0] = 0.999
    a[2, 2] = 0
    print(a)
    # 白色对应的是最大的值，黑色对应的小的值
    # 因为对于array来说最上面一行是0开始的，而在plt画的图总，左下角是0开始的，
    # 因此实际上默认的时候图像看起来是上下反着的，如果把ticks隐藏起来的话很容易弄错
    # 而要想调整到跟array一样，左上角对应图片的左上角，则origin可以设置为upper
    # 因此，xtick有的时候先不隐藏，知道看到图片OK了，再隐藏。
    # 至于interpolation可以设置的值可以看下
    # https://matplotlib.org/examples/images_contours_and_fields/interpolation_methods.html
    plt.imshow(a, interpolation='nearest', cmap='bone', origin='upper')

    # 开始话图片右边的colorbar
    plt.colorbar(shrink=0.9)

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab65693431cd.png)





### 12.画3D的图像




    # 3D数据 怎么画3D的图像
    import numpy as np
    import matplotlib.pyplot as plt
    from mpl_toolkits.mplot3d import Axes3D

    fig = plt.figure()
    ax = Axes3D(fig)

    # X,Y value
    X = np.arange(-4, 4, 0.25)
    Y = np.arange(-4, 4, 0.25)
    X, Y = np.meshgrid(X, Y)
    print(X, Y)
    R = np.sqrt(X ** 2 + Y ** 2)
    # hight value
    Z = np.sin(R)

    ax.plot_surface(X, Y, Z,
                    rstride=1, cstride=1,  # rowstride 和column stride 是跨度是多少 比较小的话线就密集
                    cmap=plt.get_cmap('rainbow'))
    ax.contourf(X, Y, Z,
                zdir='z',  # 是从X轴压下去还是从z轴压下去
                offset=-1,  # 距离z=0的平面的距离
                cmap='rainbow')
    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab656d140cc4.png)





### 13.图像分格显示在一个figure里面，4种方法




    # 感觉还是前两种比较方便
    import matplotlib.pyplot as plt
    import matplotlib.gridspec as gridspec

    # method 1
    plt.figure(num=1)
    plt.subplot(2, 1, 1)  # 两行一列，的第一个位置 所以占上半部分
    plt.plot([0, 1], [0, 1])
    plt.subplot(2, 2, 3)  # 两行两列的第三个位置
    plt.plot([0, 1], [0, 2])
    plt.subplot(2, 2, 4)
    plt.plot([0, 1], [0, 2])

    # method2 :subplot2grid
    plt.figure(num=2)
    # 这个grid是3行3列的即（3，3），从（0，0）开始plot， 然后占据的是3列，1行
    ax1 = plt.subplot2grid((3, 3), (0, 0), colspan=3, rowspan=1)
    ax1.plot([1, 2], [1, 2])
    ax1.set_title('aaa')
    ax2 = plt.subplot2grid((3, 3), (1, 0), colspan=2, rowspan=1)
    ax3 = plt.subplot2grid((3, 3), (2, 0), colspan=1, rowspan=1)
    ax4 = plt.subplot2grid((3, 3), (2, 1), colspan=1, rowspan=1)
    ax5 = plt.subplot2grid((3, 3), (1, 2), colspan=1, rowspan=2)

    # method3 :gridspec
    plt.figure(num=3)
    gs = gridspec.GridSpec(3, 3)
    ax1 = plt.subplot(gs[0, :])
    ax2 = plt.subplot(gs[1, :2])
    ax3 = plt.subplot(gs[1:, 2])
    ax4 = plt.subplot(gs[-1, 0])
    ax5 = plt.subplot(gs[-1, -2])

    # method4 :easy to define structure
    # sharex 指的是x和y轴的坐标共用。
    f, ((ax11, ax12), (ax21, ax22)) = plt.subplots(2, 2, sharex=True, sharey=True)
    ax11.scatter([1, 2], [1, 2])

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab657f20ec76.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab657ff30349.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6580947e68.png)




![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab65811329ea.png)





### 14.图中图




    # 图中图
    #plot in plot
    import random
    import numpy as np
    import matplotlib.pyplot as plt
    import matplotlib.gridspec as gridspec
    fig=plt.figure()
    x=range(7)
    y=np.random.randn(7)

    left,bottom,width,height=0.1,0.1,0.8,0.8# 占整个figure的百分比，的范围
    ax1=fig.add_axes([left,bottom,width,height])# 添加一个新的ax进取
    ax1.plot(x,y,'r')# 再这个axes里面画
    ax1.set_xlabel('x')
    ax1.set_ylabel('y')
    ax1.set_title('title')



    left,bottom,width,height=0.15,0.6,0.25,0.25# 占整个figure的百分比，的范围
    ax2=fig.add_axes([left,bottom,width,height])
    ax2.plot(x,y,'b')
    ax2.set_xlabel('x')
    ax2.set_ylabel('y')
    ax2.set_title('title inside 1')


    plt.axes([0.6,0.2,0.25,0.25])
    plt.plot(y[::-1],x,'g')
    plt.xlabel('x')
    plt.ylabel('y')
    plt.title('title inside 2')


    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab65892900e4.png)





### 15.次坐标轴 secondary axis





    # 次坐标 secondary axis
    # 两根线，一根对应左边的坐标轴，一根对应右边的坐标轴，
    # 两根线公用x轴
    # 而且可以看出想添加几条线都可以，但是右侧的y轴会重叠

    import matplotlib.pyplot as plt
    import numpy as np

    x = np.arange(0, 10, 0.1)
    y1 = 0.05 * x ** 2
    y2 = -1 * y1
    y3 = x * np.random.random(100)

    # 这个是什么意思？
    fig, ax1 = plt.subplots()  # 注意，这个地方是plots而不是plot
    ax2 = ax1.twinx()  # Create a twin Axes sharing the xaxis
    ax3 = ax1.twinx()
    ax1.plot(x, y1, 'g-')
    ax2.plot(x, y2, 'b--')
    ax3.plot(x, y3, 'r')

    ax1.set_xlabel('X data')  # 因为是twinx，因此这个地方大xlabel也是ax2的xlabel
    ax1.set_ylabel('Y1', color='g')  # 这个是y的坐标轴的文字的颜色
    ax2.set_ylabel('Y2', color='b')
    ax3.set_ylabel('     Y3', color='r')

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab658d5dc254.png)





### 16.animation 动画





    # matplotlib animation 动画
    import numpy as np
    import matplotlib.pyplot as plt
    from matplotlib import animation

    LIMIT_MIN = 0
    LIMIT_MAX = 2 * np.pi
    FRAME_COUNT = 100

    fig, ax = plt.subplots()
    x = np.arange(LIMIT_MIN, LIMIT_MAX, 0.01)
    line, = ax.plot(x, np.sin(x))  # 返回出的是一个列表，第一个是line


    # 如果是一个循环的图，要想形成不断的循环的画面，一定要加上i/FRAME_COUNT*(LIMIT_MAX-LIMIT_MIN)
    def ani_func(i):  # 传进去的是frame的帧序号
        line.set_ydata(np.sin(x + i / FRAME_COUNT * (LIMIT_MAX - LIMIT_MIN)))
        return line,


    def ani_init_func():
        line.set_ydata(np.sin(x))
        return line,


    # 其中一种animation的方式，一般使用FuncAnimation就好
    ani = animation.FuncAnimation(fig=fig,  # 要进行animation的fig
                                  func=ani_func,
                                  frames=FRAME_COUNT,  # 100帧
                                  init_func=ani_init_func,  # 动画最开始的时候是什么样子的
                                  interval=15,  # 多少ms更新一次
                                  blit=True, )  # 是更新整张图片的所有点呢？还是只更新变化的点？。。Mac用户的True会报错

    plt.show()


输出：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5ab6591fe9b18.png)





## COMMENT：


**还需要补充，尽管一般情况下够用了，但是还是有些图的样式没有录入，而且没有一些复杂的例子， 而且图的使用的场景没有标注**




## REF：


1.[莫烦Python Matplotlib数据可视化神器](https://morvanzhou.github.io/tutorials/data-manipulation/plt/) （**入门推荐**）
