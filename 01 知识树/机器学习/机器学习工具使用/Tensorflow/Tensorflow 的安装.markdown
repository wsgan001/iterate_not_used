# TODO

* 要补充GPU版本的安装和linux下的安装。



# ORIGIN


之前我在安装tensorflow的时候由于已经安装了pycharm，而且电脑只有CPU，因此使用的比较简单的方法，但是万一只能在pip或者docker情况下安装呢？因此记一下，


## 要点：




### 1.安装的方法


我自己的安装方法，（前提：已经安装了pycharm）：

在安装好Anaconda之后，直接在pycharm中像普通的包一样在Setting的Project Interpreter里面搜索安装就行。

别的安装情况：

参考：[使用 Pip, Docker, Virtualenv, Anaconda 或 源码编译的方法安装 TensorFlow.](http://wiki.jikexueyuan.com/project/tensorflow-zh/get_started/os_setup.html)


### 2.安装完之后的简单测试


```python
import tensorflow as tf

hello = tf.constant('hello , tensorflow')
sess = tf.Session()
print(sess.run(hello))

a = tf.constant(10)
b = tf.constant(32)
print(sess.run(a + b))
```

输出：


    b'hello , tensorflow'
    42





