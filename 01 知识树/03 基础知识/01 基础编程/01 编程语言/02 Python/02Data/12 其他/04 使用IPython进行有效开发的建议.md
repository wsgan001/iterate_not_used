
# B.4 Tips for Productive Code Development Using IPython（使用IPython进行有效开发的建议）

# 1 Reloading Module Dependencies（重新加载模块依赖）

假设我们在一个test_script.py中有下面的代码：

```
import some_lib

x = 5
y = [1, 2, 3, 4]
result = some_lib.get_answer(x, y)
```

然后我们进入IPython环境，运行`%run test_script.py`，然后更改了some_lib.py，再次运行`%run test_script.py`的时候，使用的还是旧版本的some_lib.py。因为python默认只加载一次。

第一种解决办法，使用importlib模块的reload方法：
```
import some_lib 
import importlib

importlib.reload(some_lib)
```
这样能保证每次运行test_script.py的时候，都能得到some_lib的最新版本。不过如果依赖更多，我们不得不写很多reload。针对这一点，ipython有一个dreload函数（不是魔法函数），用于递归式地重新加载模块。如果运行some_lib.py，然后输入dreload(some_lib)，会自动重新加载some_lib，而在some_lib中用到的依赖，也会被重新加载。如果不起作用的话，只能重新启动ipython了。

# 2 Code Design Tips（代码设计建议）

## Keep relevant objects and data alive（保持代码随时可用）


假设我们有下面这样一段代码，在ipyhton里运行：

```
from my_functions import g

def f(x, y):
    return g(x + y)

def main():
    x = 6 
    y = 7.5 
    result = x + y

if __name__ == '__main__':
    main()
```
这样的话有一个问题，运行之后，ipython shell里不会留下任何变量或结果。所以我们应该在`main()`的部分（或`if __name__ == '__main__':`内），写一些全局的变量。这样的话，即使用%run运行完代码，我们也能检查main中定义变量。

## Flat is better than nested（扁平好过嵌套）

嵌套的代码就像洋葱，一层层拨开才能找到感兴趣的部分。所以写函数和类应该尽量模块化，方便test，debug。

## Overcome a fear of longer files（克服对长文件的恐惧）

如果你有java的背景，可能会注意保持让文件短小。对于大部分语言，这也说得通，长文件说明没有很好地对代码结构做调整。但是使用ipython的话，处理2，3个长文件很方便，但处理10个短文件反而更麻烦。因为文件小说明加载的模块少，必须在各个文件之间跳转。等我们的工作做的差不多，可以再考虑把文件重构为几个小文件。

