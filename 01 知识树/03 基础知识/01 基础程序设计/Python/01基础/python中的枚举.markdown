# python中的枚举



## 缘由：


视频里说到python中的enum的时候，感觉与其他的语言有很大的不同，因此，还是要总结下的


## 要点：


代码如下：


    # 感觉python里面的enum与其他语言的又很大的不同

    from enum import Enum

    # 为什么这个地方还有一个Month？
    Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr'))

    # value为什么是从1开始的？
    for name, member in Month.__members__.items():
        print(name, '=>', member, ',', member.value)

    jan = Month.Jan
    print(jan)


输出：


    Jan => Month.Jan , 1
    Feb => Month.Feb , 2
    Mar => Month.Mar , 3
    Apr => Month.Apr , 4
    Month.Jan


注：**为什么Enum里面还有一个Month？而且为什么会有name，member和member.value？**


## COMMENT：


**还是没有很明白**
