
    1. 




# TODO





# 在Windows下的安装




1.下载源代码

`git clone --recursive https://github.com/Microsoft/LightGBM`

2.编译DLL

进入`LightGBM` 目录，用 VS2015（或者更高的版本）打开 `windows/LightGBM.sln`，选择DLL和x64，按 `Ctrl+Shift+B` 进行编译，dll文件就会在`windows/x64/DLL/`目录里。注意，一定要是VS2015以上的，如果没有VS2015，可以参考下[官网的其它方法](http://lightgbm.apachecn.org/cn/latest/Installation-Guide.html)，写的还是很清楚的。


![](assets/img_5aed9bd7dadc6.png)


3.安装Python包

进入目录`python-package`  执行命令：


    python setup.py install


4.测试一下

进入`examples\python-guide` 执行样例：


    python .\simple_example.py


如果没有报错，那就说明安装成功了。





# REF





1. [ LightGBM 中文文档](http://lightgbm.apachecn.org/cn/latest/index.html)
2. [在Windows下安装LightGBM的Python包](https://blog.csdn.net/jiaqiangbandongg/article/details/53814663)