



函数允许在代码段中构造程序来执行单独的任务。创建函数的典型情况是在程序需要多次执行相同的动作时。

将代码片段标准化为函数具有几个优点：

* 函数帮助程序员保持组织性。通常有助于概念化程序。

* 函数将一个动作编码在一个地方，以便函数只需要考虑一次和调试一次。

* 如果代码需要更改，这也减少了修改错误的几率。

* 由于代码段被多次重复使用，函数使整个草图更小更紧凑。

* 通过将代码模块化以令其在其他程序中重复使用变得更容易，通过使用函数使得代码更具可读性。


在Arduino草图或程序中有两个必需的函数，即setup()和loop()。其他函数必须在这两个函数的括号之外创建。

定义函数的最常用的语法是：


![定义函数](https://img.w3cschool.cn/attachments/tuploads/arduino/function.jpg)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/4JcH8BgdEH.png?imageslim)







## 函数声明


函数在循环函数之上或之下的任何其他函数之外声明。

我们可以用两种不同的方式声明函数：

第一种方法是在循环函数上面写入被称为**函数原型**的函数的一部分，它包括：




  * 函数返回类型


  * 函数名称


  * 函数参数类型，不需要写参数名称


函数原型后面必须加上分号(;)。

以下示例为使用第一种方法的函数声明的示范。


### 例子




    int sum_func (int x, int y) // function declaration {
       int z = 0;
       z = x+y ;
       return z; // return the value
    }

    void setup () {
       Statements // group of statements
    }

    Void loop () {
       int result = 0 ;
       result = Sum_func (5,6) ; // function call
    }



第二种方法，称为函数定义或声明，必须在循环函数的下面声明，它包括：




  * 函数返回类型


  * 函数名称


  * 函数参数类型，这里必须添加参数名称


  * 函数体（调用函数时执行的函数内部的语句）





以下示例演示了使用第二种方法的函数声明。


### 例子




    int sum_func (int , int ) ; // function prototype

    void setup () {
       Statements // group of statements
    }

    Void loop () {
       int result = 0 ;
       result = Sum_func (5,6) ; // function call
    }

    int sum_func (int x, int y) // function declaration {
       int z = 0;
       z = x+y ;
       return z; // return the value
    }



第二种方法只是在循环函数下面声明函数。












# REF

1. [Arduino教程](https://www.w3cschool.cn/arduino/)
