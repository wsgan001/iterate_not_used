# C++ cstring、string、char*




## 缘由


今天在看程序的时候，发现有：`#include <cstring>` 的。因此相知道，`cstring` 和 `string` 有什么区别？以前有遇到过，没有详细的总结，或者总结了，也忘记了，这里总结一下。注意，这里不讨论 MFC 的 `CString`。


## `include` 的时候的区别


```cpp
#include <cstring> //不可以定义string s；可以用到strcpy等函数
using  namespace  std;

#include <string> //可以定义string s；可以用到strcpy等函数
using  namespace  std;

#include <string.h> //不可以定义string s；可以用到strcpy等函数
```


1. 文件 cstring，和 string.h 对应，C++ 版本的头文件，包含比如 strcpy 之类的字符串处理函数
2. 文件 string.h，和 cstring 对应，C 版本的头文件，包含比如 strcpy 之类的字符串处理函数
3. 文件 string，包含 std::string 的定义，属于 STL 范畴 。


**想知道，这第三个与上面的两个有关系吗？有区别吗？而且上面的两个，什么叫 C++ 版本？什么叫 C 版本？**


# 详细说一下 `<cstring>` 和 `string.h`


`<cstring>` 提供的是一些对应 C 风格的字符串的函数，C 风格的字符串就是没有终止符的字符数组，这些函数包括 `strlen` 、`strcpy` 等，它是 C 时候的 `string.h` 的 C++ 版本。

某些实现中 `<cstring>` 的内容就是:


```cpp
namespace std
{
    #include <string.h>
}
```

可见他们实际上是一回事，都是用来处理 C 风格的字符串的，不过 cstring 是在 C++ 中使用的，而 string.h 是在 C 中使用的。

OK，那么 `<cstring>` 和 `<string>` 呢？


# 那么 `<cstring>` 和 `<string>` 呢？


`<string>` 提供的是 `std::string` 类和一些相关的函数和操作，`<string>` 和 `<cstring>` 虽然有类似的名字，但是他们除了名字类似之外一点也不一样。完全是不同的。

这个地方需要注意下：`<cstring>` 也是在 std:: 命名空间下面的。

OK，我们大概知道了 `<string>` 与 `<cstring>` 完全不同。




# 那么，为什么 g++ 使用 string 的时候不用 `include <string>` 呢？而且 VS好像在对应一些 C 字符串的处理的时候也不用 `include <cstring>`，这是为什么呢？


C++ 标准允许标准的 headers include 别的标准 headers，因此，你虽然没有手动 `include <string>` 但是你也许在 include 别的 headers 的时候已经 `include <string>` 了，比如 `include <iostream>` 。

类似的，对于 `<cstring>` 来说也是一样，总是会包含一些你需要的 headers 的。

因此不要依靠你的特殊的环境来隐式的include 它们，因为这样你会在使你的代码可移植的时候会遇到问题，或者当你相变更 编译器版本的时候，没准这个新版本的编译器会对于这些headers的隐式依赖有不同的实现。



# REF

1. [Difference between <cstring> and <string>](https://stackoverflow.com/questions/12824595/difference-between-cstring-and-string)
