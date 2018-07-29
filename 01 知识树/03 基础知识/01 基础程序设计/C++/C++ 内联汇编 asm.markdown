
# ORIGINAL
  1. [C++ Inline ASM 内联汇编祥解](https://blog.csdn.net/masefee/article/details/3943024)
  2. [asm基础——在c/c++语言中调用asm函数](https://blog.csdn.net/jiangwei0512/article/details/50857839)
  3. [ARM内联汇编](https://blog.csdn.net/gameit/article/details/13169391)


# TODO
  * **需要整理一下，这个asm到底起的什么作用？**
  * **到底怎么正确的在使用汇编的时候带参数？**


# 缘由


今天看一个算法问题的时候，它用了这个来实现加法：

```c++
class Solution {
public:
	int Add(int left, int right) {
		__asm __volatile__
			(
			"addl %1,%0;\n"     /* 相当于 add b, a结果存储在a中*/
			: "=m"(left)
			: "r"(right), "m"(left)
			//  :
			);
		return left;
	}
};
```

但是这个报错了，就是在   : "=m"(left)  的冒号的地方。而且，之前从来没见到过 __asm ，因此要总结一下。























* * *





# COMMENT
