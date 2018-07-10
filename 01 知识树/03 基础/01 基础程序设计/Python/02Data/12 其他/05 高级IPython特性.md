
# B.5 Advanced IPython Features（高级IPython特性）

# 1 Making Your Own Classes IPython-Friendly（让自己的类IPython友好）

ipython在命令行形式上能很好的展示python的各种对象，看起来很方便。但是用户自己定义的类就不能保证了，所以我们自己应该保证输出的效果。假设我们有一个简单的类：


```python
class Message:
    def __init__(self, msg):
        self.msg = msg
```

如果按上面这么写，我们会对输出的效果很失望：


```python
x = Message('I have a secret')
```


```python
x
```




    <__main__.Message at 0x104ad1668>



ipython中返回的字符串是经过`__repr__`魔法函数处理过的（`output=repr(obj)`），然后才打印出来。因此，我们最好添加一个`__repr__`方法：


```python
class Message:
    def __init__(self, msg):
        self.msg = msg
    def __repr__(self):
        return 'Message: %s' % self.msg
```


```python
x = Message('I have a secret')
```


```python
x
```




    Message: I have a secret



# 2 Profiles and Configuration（配置文件和配置）

ipython和jupyter的一些外观（颜色，提示符，空格等），都是通过配置文件来设定的。通过配置文件我们可以设置下面一些参数：

- 颜色
- 改变输入和输出的提示符
- 每次打开ipython的时候可以执行一段代码，导入某些库
- 开启ipython扩展，比如line_profiler中的%lprun
- 开启jupyter扩展
- 定义自己的魔法函数或别名

ipython shell的设置文件为`ipython_config.py`，通常位于主目录下的`.ipython/`文件夹内。每次打开ipython的时候，会默认加载`profile_default`中的`default`文件。在作者的linux系统下，默认的ipython配置文件为：

`/home/wesm/.ipython/profile_default/ipython_config.py`

初始化的话，可以在终端运行：

`ipython profile create`

如果我们有一个ipython配置是针对某个项目的，我们可以新建一个配置文件：

    ipython profile create secret_project
    
新创建的配置文件在profile_secret_project目录下，我们可以按需要更改配置文件。然后按下面的方式启动ipython：

![](http://oydgk2hgw.bkt.clouddn.com/pydata-book/v4ugs.png)

创建jupyter的配置文件：

    jupyter notebook --generate-config
    
配置文件会保存到主目录下的`.jupyter/jupyter_notebook_config.py`。按需求更改配置文件后，可以重命名：

    mv ~/.jupyter/jupyter_notebook_config.py ~/.jupyter/my_custom_config.py

启动jupyter的时候，添加对应的`--config`参数：

    jupyter notebook --config=~/.jupyter/my_custom_config.py
