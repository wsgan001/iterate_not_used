# 指针作为函数参数传递

TODO

- 这个一直不是特别清楚，还是经常遇到的，因此要明确下。


这几天在学习C过程中，在使用指针作为函数参数传递的时候出现了问题，根本不知道从何得解:源代码如下:

```cpp
createNode(BinNode *tree,char *p)
{
    tree = (BinNode *) malloc(sizeof(BinNode));
    tree->data = *p;
}
```

该代码段的意图是通过一个函数创建一个二叉树的节点，然而在，调用该函数后，试图访问该节点结构体的成员时候，却发生了内存访问错误，到底问题出在哪儿呢？

一直不明白指针作为函数参数传值的机制，翻开林锐的《高质量C/C++编程指南》，找到了答案。

**如果函数的参数是一个指针，不要指望用该指针去申请动态内存**
​
问题的原因出在 C 编译器原理上：编译器总是要为函数的每个参数制作临时副本，指针参数`tree`的副本是 `_tree`，编译器使 `_tree = tree`。如果函数体内的程序修改了`_tree`的内容，就导致参数`tree`的内容作相应的修改。这就是指针可以用作输出参数的原因。

即上面的函数代码经过编译后成为：

```cpp
createNode(BinNode *tree,char *p)
{
    BinNode *_tree;
    _tree = tree;
    _tree = (BinNode *) malloc(sizeof(BinNode));
    _tree->data = *p;
}
```

如果没有

```cpp
_tree = (BinNode *) malloc(sizeof(BinNode));
```

这个语句，在函数体内修改了`_tree`的内容，将会导致参数`tree`的内容作相应的修改，因为它们指向相同的内存地址。而

```cpp
_tree = (BinNode *) malloc(sizeof(BinNode));
```

这个句，系统重新分配内存给`_tree`指针，`_tree`指针指向了系统分配的新地址，函数体内修改的只是`_tree`的内容，对原`tree`所指的地址的内容没有任何影响。因此，函数的参数是一个指针时，不要在函数体内部改变指针所指的地址，那样毫无作用，需要修改的只能是指针所指向的内容。即应当把指针当作常量。

如果非要使用函数指针来申请内存空间，那么需要使用指向指针的指针

```cpp
​createNode(BinNode **tree,char *p)
{
    *tree = (BinNode *) malloc(sizeof(BinNode));
}
```

<span style="color:red;">对上面这种写法还是不是特别的理解。</span>

上面的是林锐的说法，目前来说不知道怎么去理解，不过可以有另外的方案，通过函数返回值传递动态内存：


```cpp
BinNode *createNode()
{
    BinNode *tree;
    tree = (BinNode *) malloc(sizeof(BinNode));
    return tree;
}
```

这个倒还说得过去，因为函数返回的是一个地址的值，该地址就是申请的内存块首地址。但是，这个容易和另外的一个忠告相混绕


**不要用return语句返回指向“栈内存”的指针，因为该内存在函数结束时自动消亡**


注意：实际上没有混淆，因为这里`tree`是在“堆内”分配的内存，而非在“栈”上。


所谓一份拷贝，就是在函数调用时，将参数入栈，我们对形参的任何修改都是修改到栈上的个拷贝，并不影响我们的实际参数.

任何编程语言的参数传递实际上都是在做传值调用.

所谓的传指针，就是把指针指向者的地址(一个值)传进函数.

也就是那个地址被压栈.

然后我们再通过这个地址进行操作,因为实参和形参同样都是一个地址的值.

所以改变形参指向者的状态时,实参指针也能看到这种变化.

这里区分一下静态内存，栈内存和动态分配的内存(堆内存)的区别:


- 从静态存储区域分配。内存在程序编译的时候就已经分配好，这块内存在程序的整个运行期间都存在。例如全局变量，static变量。

- 在栈上创建。在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。

- 从堆上分配，亦称动态内存分配。程序在运行的时候用malloc或new申请任意多少的内存，程序员自己负责在何时用free或delete释放内存。动态内存的生存期由我们决定，使用非常灵活，但问题也最多。



因此，试图返回一个栈上分配的内存将会引发未知错误

```cpp
​char *GetString(void)
​{
​    char p[] = "hello world";
​    return p; // 编译器将提出警告
​}
```

`p` 是在栈上分配的内存，函数结束后将会自动释放，`p`指向的内存区域内容不是`"hello world"`，而是未知的内容。<span style="color:red;">嗯。</span>

如果是返回静态存储的内存呢:

```cpp
char *GetString(void)
{
    char *p = "hello world";
    return p;
}
```

这里`"hello world"`是常量字符串，位于静态存储区，它在程序生命期内恒定不变。无论什么时候调用`GetString`，它返回的始终是同一个“只读”的内存块。<span style="color:red;">这个之前也不是很清楚，原来返回的是同一个只读的内存块。</span>





下面这两种方法是正确的：


```cpp
#include<iostream> //指向指针的指针
using namespace std;

void GetMemory(char *&p,int num){
    p=(char *)malloc(sizeof(char)*num);
}
void main(void){
    char *str=NULL;
    GetMemory(str,100);
    strcpy(str,"hello");
    cout << str << endl;
    free(str);
}
```


或者：


```cpp
#include<iostream>
using namespace std;
void GetMemory(char * *p,int num){
    *p=(char *)malloc(sizeof(char)*num);
}
void main(void){
    char *str=NULL;
    GetMemory(&str,100);
    strcpy(str,"hello");
    cout << str << endl;
    free(str);
}
```

<span style="color:red;">上面这两种写法不是很理解。再看下。</span>

下面的例子是错误的：


```cpp
#include<iostream>
using namespace std;
void GetMemory(char *p,int num){
    p=(char *)malloc(sizeof(char)*num);
}
void main(void){
    char *str=NULL;
    GetMemory(str,100);
    strcpy(str,"hello");
    cout << str << endl;
    free(str);
}
```


试图用指针申请动态内存，错误的原因上面已经给出了详细的说明。总而言之，指针作为参数时，不能在函数体中改变指针的内存地址，要不然，实参的拷贝（压入栈中）改变了，而实参没有改变，造成内存泄露并且还达不到预期的效果。

上面正确的2个例子都是通过另一种方法绕开了这个问题，改变指针的内容，例如：用了指向指针的指针，给指针的内容改变了，使其变为新分配内存的首地址，从而达到了效果。




## REF

- 林锐《高质量C/C++编程指南》 <span style="color:red;">这个也要整合进来。完善对 C++ 的理解。</span>
- [面试笔试系列3-指针作为函数参数传递](https://blog.csdn.net/olisten/article/details/8823511)
- [C 指针传递变量为什么无法修改变量值？](https://www.zhihu.com/question/41476387) <span style="color:red;">这里面有这个的非常精彩的回答，要总结下。</span>
