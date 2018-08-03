[TOC]

# TODO

* **实际上还是有些没有弄清楚的，后续进行跟进**



# ORIGIN


Tensorflow 的一些基础概念


# MAIN




## 1.一些基础的


使用 TensorFlow 之前你需要了解关于 TensorFlow 的以下基础知识 :




  * 使用图 (graphs) 来表示计算 .

  * 在会话 ( Session ) 中执行图 .

  * 使用张量 (tensors) 来代表数据，张量可以用多维数组来表示** 张量还是没明白是什么？为什么可以代表数据？**

  * 通过变量 ( Variables ) 维护状态 . **为什么变量是用来维护状态的？**

  * 使用供给 ( feeds ) 和取回 ( fetches ) 将数据传入或传出任何操作


TensorFlow 是一个以图 (graphs) 来表示计算的编程系统 , 图中的节点被称之为 op(operation 的缩写 ). 一个 op 获得零或多个张量 (tensors) 执行计算 , 产生零或多个张量。张量是一个按类型划分的多维数组。例如 , 你可以将一小组图像集表示为一个四维浮点数数组 , 这四个维度分别是 [batch, height, width, channels] 。？**没明白？为什么这个是一个四维的浮点数数组？**

TensorFlow 的图是一种对计算的抽象描述。在计算开始前 , 图必须在 会话 ( Session() ) 中被启动 . 会话将图的 op 分发到如 CPU 或 GPU 之类的设备 ( Devices() ) 上 , 同时提供执行 op 的方法。这些方法执行后 , 将产生的张量 (tensor) 返回。在 Python 语言中 , 将返回 numpy 的 ndarray 对象 ; 在 C 和 C++ 语言中 , 将返回 tensorflow::Tensor 实例。


## 2.计算图 The computation graph


通常， TensorFlow 编程可按两个阶段组织起来 : 构建阶段和执行阶段 ; 前者用于组织计算图，而后者利用 session 中执行计算图中的 op 操作。

例如 , 在构建阶段创建一个图来表示和训练神经网络，然后在执行阶段反复执行一组 op 来实现图中的训练。

TensorFlow 支持 C 、 C++ 、 Python 编程语言。目前 , TensorFlow 的 Python 库更加易用 , 它提供了大量的辅助函数来简化构建图的工作 , 而这些函数在 C 和 C++ 库中尚不被支持。

这三种语言的会话库 (session libraries) 是一致的。（**什么是会话库？**）


### 构建计算图 Building the graph


刚开始基于 op 建立图的时候一般不需要任何的输入源 (source op) ，例如输入常量( Constance ) ，再将它们传递给其它 op 执行运算。

Python 库中的 op 构造函数返回代表已被组织好的 op 作为输出对象，这些对象可以传递给其它 op 构造函数作为输入。

TensorFlowPython 库有一个可被 op 构造函数加入计算结点的默认图 (defaultgraph) 。对大多数应用来说，这个默认图已经足够用了。阅读 Graph 类文档来了解如何明晰的管理多个图。（**有吗？之前没有注意过**）


### 在会话中载入图 Launching the graph in a session


构建过程完成后就可运行执行过程。为了载入之前所构建的图，必须先创建一个
会话对象 ( Session object) 。会话构建器在未指明参数时会载入默认的图。

代码如下：


```python
import tensorflow as tf
import numpy as np

# op在创建的时候就会被作为一个节点添加到default grouph里面
# 返回的值代表了这个op的输出
matrix1 = tf.constant([[3., 3.]])  # 一个常量操作，constant op，创建一个 1*2 的matrix
matrix2 = tf.constant([[2., ], [2., ]])
product = tf.matmul(matrix1, matrix2)

# 启动计算图
sess = tf.Session()
# 将想要计算的op传进去，所有的这个op需要的别的op也都会被session自动传进去，而且这些op是并行的
# 当run(product)的时候，实际上引发了三个op的执行，两个常量操作，和一个乘法操作
# 输出的结果是勒个numpy的ndarray的object。。实际是一个list。。
res = sess.run(product)
print(res)
print(type(res))
sess.close()
```


输出：


    [[ 12.]]


注：在 sess=tf.Session() 之前，默认图拥有三个节点，两个 constant() op ，一个 matmul() op. 为了真正进行矩阵乘法运算，得到乘法结果 , 必须在一个会话 (session) 中载入这个图。

上面的有几个问题想知道：default graph指的是那个？tf本身就是这个default graph吗？为什么这个地方说the op is added as a node to the default graph？还有，op都是并行执行的吗？run引起的一系列的执行吗？如果两个run一样，是执行那些op两次还是之前的结果有保存？而且，**为什么上面没有 tf.global_variables_initializer() 这句？因为没有使用Variable。 为什么现在的输出使用list了？我看之前的例子都是np.array格式的，这是因为没有强调dtype为tf.float32等的缘故。**

会话在完成后必须关闭以释放资源。你也可以使用 "with" 句块开始一个会话，该会话将在 "with" 句块结束时自动关闭。


    with tf.Session() as sess:
        res=sess.run([product])
        print(res)


TensorFlow 事实上通过一个“翻译”过程，将定义的图转化为不同的可用计算资源间实现分布计算的操作，如 CPU 或是显卡 GPU 。通常不需要用户指定具体使用的 CPU或 GPU ， TensorFlow 能自动检测并尽可能的充分利用找到的第一个 GPU 进行运算。（**只是会充分利用第一个吗？如果有两个的话，不会自动使用两个吗？**）

如果你的设备上有不止一个 GPU ，你需要明确指定 op 操作到不同的运算设备以调用它们。使用 with...Device 语句明确指定哪个 CPU 或 GPU 将被调用：


    with tf.Session() as sess:
        with tf.device("/gpu:1"):
            matrix1 = tf.constant([[3., 3.]])
            matrix2 = tf.constant([[2.],[2.]])
            product = tf.matmul(matrix1, matrix2)
            ....


**上面这个代码没有自己跑过，指定gpu的时候是这样指定编号就行吗？而且op的定义是在with tf.defice里面定义的吗？不能放在外面吗？也就是说gpu之间可以共享op吗？**

**而且，这个时候tf.global_variables_initializer() 这句放在哪里？sess里面吗？**

使用字符串指定设备，目前支持的设备包括 :




  * "/cpu:0" ：计算机的 CPU ；
  * "/gpu:0" ：计算机的第一个 GPU ，如果可用；
  * "/gpu:1" ：计算机的第二个 GPU ，以此类推




## 3.交互式使用 Interactive Usage


文档中的 Python 示例使用一个会话 Session 来启动图 , 并调用 Session.run() 方法执行操作。

考虑到如 IPython 这样的交互式 Python 环境的易用 , 可以使用 InteractiveSession 代替 Session 类 , 使用 Tensor.eval() 和 Operation.run() 方法代替 Session.run() . 这样可以避免使用一个变量来持有会话。（**什么叫避免使用一个变量来持有会话？**）

交互会话 (InteractiveSession) 类，它可以让您更加灵活地构建代码。**交互会话能让你在运行图的时候，插入一些构建计算图的操作。**这能给使用交互式文本 shell 如 iPython 带来便利。**如果你没有使用 InteractiveSession 的话，你需要在开始 session 和加载图之前，构建整个计算图。**（**什么意思？没明白？普通的session要在之前构建整个计算图，而InteractiveSession还可以在运行的时候添加吗？**）


```python
# Enter an interactive TensorFlow Session.
import tensorflow as tf

sess = tf.InteractiveSession()

x = tf.Variable([1.0, 2.0])
a = tf.constant([3.0, 3.0])

# 看来这个每个变量都要initializer.run
# 没明白Variable与constant有什么区别？这个地方好像看不出什么区别？
# Initialize 'x' using the run() method of its initializer op.
x.initializer.run()  # Add an op to subtract 'a' from 'x'. Run it and print the result
sub = tf.subtract(x, a)
print(sub.eval())  # ==> [ − 2. − 1.]

# Close the Session when we're done.
sess.close()
```


输出：


    [-2. -1.]


**为什么上面说Tensor.eval() ？这个地方不是sub.eval()吗？而sub不是类似前面的matmul一样是一个op吗？op和tensor是什么关系？而且，Variable和constant是什么区别？**


## 4.张量 Tensors


TensorFlow 程序使用 tensor 数据结构来代表所有的数据 , 计算图中 , 操作间传递的数据都是 tensor. 你可以把 TensorFlow 的张量看作是一个 n 维的数组或列表 . 一个 tensor包含一个静态类型 rank, 和一个 shape. 想了解 TensorFlow 是如何处理这些概念的 , 参见Rank, Shape, 和 Type 。

**那么前面的 matmul 和 sub 返回的是 tensor 还是 op ？rank是什么？什么是静态类型？这几个Rank，Shape和Type 的概念没有明白。**


## 5.变量 Variable


变量维持了图执行过程中的状态信息。下面的例子演示了如何使用变量实现一个简单的计数器，

```python
import tensorflow as tf

state = tf.Variable(0, name='counter')  # name是什么？用在什么地方的？

one = tf.constant(1)
new_value = tf.add(state, one)
update = tf.assign(state, new_value)  # 将new_value 赋值给state

init_op = tf.global_variables_initializer()  # 这也是一个op，在sess创建后运行

with tf.Session() as sess:
    sess.run(init_op)
    print(sess.run(state))
    for _ in range(3):
        sess.run(update)
        print(sess.run(state))
```

输出：

```text
0
1
2
3
```

代码中 assign() 操作是图所描绘的表达式的一部分 , 正如 add() 操作一样 . 所以在调用 run() 执行表达式之前 , 它并不会真正执行赋值操作。

通常会将一个统计模型中的参数表示为一组变量 . 例如 , 你可以将一个神经网络的权重作为某个变量存储在一个 tensor 中 . 在训练过程中 , 通过重复运行训练图 , 更新这个tensor. **嗯**


## 6.取回 Fetches


为了取回操作的输出内容 , 可以在使用 Session 对象的 run() 调用执行图时 , 传入一些 tensor, 这些 tensor 会帮助你取回结果 . 在之前的例子里 , 我们只取回了单个节点 state ,但是你也可以取回多个 tensor:


```python
import tensorflow as tf

input1 = tf.constant(3.0, dtype=tf.float32)
input2 = tf.constant(2.0, dtype=tf.float32)
input3 = tf.constant(5.0, dtype=tf.float32)

intermed = tf.add(input2, input3)
mul = tf.multiply(input1, intermed)

with tf.Session() as sess:
    # 这个地方会计算你传进去的几个式子的结果,是一个对应式子的list结果
    result = sess.run([mul, intermed])
    print(result)
    print(type(result))
```



输出：

```text
[array([ 2.], dtype=float32)]
<class 'list'>
```

注：**如果dtype不明确说明是tf.int或者tf.float 那么result就不是上面这个格式，而是普通的 [21.0, 7.0] ，因此在计算的时候一定要注意强调数字的格式。**

需要获取的多个 tensor 值，在 op 的一次运行中一起获得（而不是逐个去获取 tensor ）。**嗯**


## 6.供给 Feeds


上述示例在计算图中引入了 tensor, 以 常量 ( Constants ) 或 变量 ( Variables ) 的形式存储 . TensorFlow 还提 供给 (feed) 机制 , 该机制可临时替代图中的任意操作中的 tensor可以对图中任何操作提交补丁 , 直接插入一个 tensor.（**什么意思？临时替代图中的人已操作中的tensor？对图中任意操作提交补丁？插入一个tensor？  还是不是很明白，tensor和op是什么关系？**）

feed 使用一个 tensor 值临时替换一个操作的输出结果 . 你可以提供 feed 数据作为run() 调用的参数 .feed 只在调用它的方法内有效 , 方法结束 , feed 就会消失 . 最常见的用例是将某些特殊的操作指定为 "feed" 操作 , 标记的方法是使用 tf.placeholder() 为这些操作创建占位符。

```python
import tensorflow as tf

input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)
output = tf.multiply(input1, input2)
with tf.Session() as sess:
    res = sess.run([output,], feed_dict={
        input1: [1.],
        input2: [2.]
    })
    print(res)
    print(type(res))
```

输出：

```text
[array([ 2.], dtype=float32)]
<class 'list'>
```

**一般placeholder的使用是什么样子的？嗯，后面有个MNIST全联通feed教程，到时候添加链接过来**




# COMMENT：







# REF


  1. tenforflow 手册
