# Python 的特点

Python现在已经成为最受欢迎的动态编程语言之一。





# Python 擅长的与不擅长的

擅长的：

- 解决了两种语言的问题。以前做研究用一门语言写原型（比如R，SAS），效果好了才会用其他语言去重新实现一遍（比如Java，C#，C++），部署到实际任务中。而Python的优势在于既适合做研究，又适合直接部署。
- 用作数据分析。Python拥有一个巨大而活跃的科学计算（scientific computing）社区。近年来，由 于Python有不断改良的库（主要是pandas），使其成为数据处理任务的一大替代方案。实际上，我们完全可以只使用Python 这一种语言去构建以数 据为中心的应用程序。
- 用作粘合剂。Python是一门胶水语言，可以把不同语言整合起来，比如上层代码使用Python编写，底层代码用C，C++等语言实现。

不擅长的：

虽然Python非常适合构建计算密集型科学应用程序以及几乎各种各样的通用系统，但它对于不少应用场景仍然力有不逮。

- Python 运行的比较慢。因为Python是解释性程序设计语言（interpreted programming language），其运行速度比Java或C++慢。如果觉得慢一点没关系，可以用Python，但如果现实场景中需要系统低延迟，使用效率高，还是使用C++这样的语言比较好。
- Python不支持线程。用Python编写多线程应用（multithreaded applications）并不方便，因为Python有一个叫做全局解释器锁（global interpreter lock (GIL)）的机制，这个机制让编译器只能在一次运行一个Python指令。对于一些大数据量的处理，Python并不合适。GIL并不会在短时间内消失。虽然很多大数据处理应用程序为了能在较短的时间内完成 数据集的处理工作都需要运行在计算机集群上，但是仍然有一些情况需要用单进程多线程系统来解决。$\color{red}\large \textbf{还有一些什么情况？}$ $\color{red}\large \textbf{为什么不能用 Python 的多进程？}$ $\color{red}\large \textbf{关于Python 的多进程的使用还是要好好总结下。}$但并不是说Python不能运行多线程，并行代码。Python C扩展能使用本地多线程（通过C或C++）来并行运行代码，而不通过GIL机制，前提是不和Python object（对象）进行过多交互。$\color{red}\large \textbf{什么叫不进行过多交互？到底应该怎么处理？}$比如说，Cython项目可以集成OpenMP （一个用于并行计算的C框架） 以实现并行处理循环进而大幅度提髙数值算法的速度。$\color{red}\large \textbf{什么意思？Cython 是怎么实现的？要总结下。}$ $\color{red}\large \textbf{Cython 到底与我们平时用的Python 有什么区别？}$



# 一些 Python 的种类

## CPython

敁近这几年，Cython项目（http://cython.org）已经成为Python领域中创建编译型扩展以及对接 C/C++ 代码的一大途径。$\color{red}\large \textbf{Cython 到底有什么厉害的地方？要总结下}$

