# Welcome to Leanote! 欢迎来到Leanote!

## 1. 排版

**粗体** *斜体*

~~这是一段错误的文本。~~

引用:

> 引用Leanote官方的话, 为什么要做Leanote, 原因是...

有充列表:
  1. 支持Vim
  2. 支持Emacs

无序列表:

 - 项目1
 - 项目2


## 2. 图片与链接

图片:
![leanote](http://leanote.com/images/logo/leanote_icon_blue.png)
链接:

[这是去往Leanote官方博客的链接](http://leanote.leanote.com)

## 3. 标题

以下是各级标题, 最多支持5级标题

```
# h1
## h2
### h3
#### h4
##### h4
###### h5
```

## 4. 代码

示例:

    function get(key) {
        return m[key];
    }

代码高亮示例:

``` javascript
/**
* nth element in the fibonacci series.
* @param n >= 0
* @return the nth element, >= 0.
*/
function fib(n) {
  var a = 1, b = 1;
  var tmp;
  while (--n >= 0) {
    tmp = a;
    a += b;
    b = tmp;
  }
  return a;
}

document.write(fib(10));
```

```python
class Employee:
   empCount = 0

   def __init__(self, name, salary):
        self.name = name
        self.salary = salary
        Employee.empCount += 1
```

# 5. Markdown 扩展

Markdown 扩展支持:

* 表格
* 定义型列表
* Html 标签
* 脚注
* todo list
* 目录
* 时序图与流程图
* MathJax 公式

## 5.1 表格

| Item     | Value  |
| -------- | ------ |
| Computer | \$1600 |
| Phone    | \$12   |
| Pipe     | \$1    |

可以指定对齐方式, 如Item列左对齐, Value列右对齐, Qty列居中对齐

| Item     |  Value | Qty  |
| :------- | -----: | :--: |
| Computer | \$1600 |  5   |
| Phone    |   \$12 |  12  |
| Pipe     |    \$1 | 234  |


## 5.2 定义型列表

名词 1
:   定义 1（左侧有一个可见的冒号和四个不可见的空格）

代码块 2
:   这是代码块的定义（左侧有一个可见的冒号和四个不可见的空格）

        代码块（左侧有八个不可见的空格）

## 5.3 Html 标签

支持在 Markdown 语法中嵌套 Html 标签，譬如，你可以用 Html 写一个纵跨两行的表格：

    <table>
        <tr>
            <th rowspan="2">值班人员</th>
            <th>星期一</th>
            <th>星期二</th>
            <th>星期三</th>
        </tr>
        <tr>
            <td>李强</td>
            <td>张明</td>
            <td>王平</td>
        </tr>
    </table>



<table>
    <tr>
        <th rowspan="2">值班人员</th>
        <th>星期一</th>
        <th>星期二</th>
        <th>星期三</th>
    </tr>
    <tr>
        <td>李强</td>
        <td>张明</td>
        <td>王平</td>
    </tr>
</table>

**提示**, 如果想对图片的宽度和高度进行控制, 你也可以通过img标签, 如:

<img src="http://leanote.com/images/logo/leanote_icon_blue.png" width="50px" />

## 5.4 脚注

Leanote[^footnote]来创建一个脚注
[^footnote]: Leanote是一款强大的开源云笔记产品.

## 5.5 todo list

Leanote 近期任务安排:

- [x] bbs 维护
- [ ] Desktop 发布新版
    - [x] Markdown编辑器添加Todo list
    - [x] 修复白屏问题
    - [ ] 修复issue3
- [ ] Leanote 维护
    - [ ] 修复issue4

## 5.6 目录

通过 `[TOC]` 在文档中插入目录, 如:

[TOC]

## 5.7 时序图与流程图

```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

流程图:

```flow
st=>start: Start
e=>end
op=>operation: My Operation
cond=>condition: Yes or No?

st->op->cond
cond(yes)->e
cond(no)->op
```

> **提示:** 更多关于时序图与流程图的语法请参考:

> - [时序图语法](http://bramp.github.io/js-sequence-diagrams/)
> - [流程图语法](http://adrai.github.io/flowchart.js)

## 5.8 MathJax 公式

$ 表示行内公式：

质能守恒方程可以用一个很简洁的方程式 $E=mc^2$ 来表达。

$$ 表示整行公式：

$$\sum_{i=1}^n a_i=0$$

$$f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $$

$$\sum^{j-1}_{k=0}{\widehat{\gamma}_{kj} z_k}$$

更复杂的公式:
$$
\begin{eqnarray}
\vec\nabla \times (\vec\nabla f) & = & 0  \cdots\cdots梯度场必是无旋场\\
\vec\nabla \cdot(\vec\nabla \times \vec F) & = & 0\cdots\cdots旋度场必是无散场\\
\vec\nabla \cdot (\vec\nabla f) & = & {\vec\nabla}^2f\\
\vec\nabla \times(\vec\nabla \times \vec F) & = & \vec\nabla(\vec\nabla \cdot \vec F) - {\vec\nabla}^2 \vec F\\
\end{eqnarray}
$$

访问 [MathJax](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference) 参考更多使用方法。


$$
\left[ \begin{matrix}
	b_{1}&c_{1}& & & &0 \\
	a_{2}&b_{2}&c_{2}& & & \\
	 &a_{3}&b_{3}&\ddots& &  \\
	 & &\ddots&\ddots&c_{n-1} & \\
	0& & & &a_{n}&b_{n}
\end{matrix}\right]
\left[ \begin{matrix}
	x_{1} \\
	x_{2} \\
	x_{3} \\
	\vdots\\
	x_{n}
\end{matrix}\right]  =
\left[ \begin{matrix}
	d_{1} \\
	d_{2} \\
	d_{3} \\
	\vdots\\
	d_{n}
\end{matrix}\right]
\tag{2}
$$

$$c'_i =\begin{cases} \begin{array}{lcl}  \cfrac{c_i}{b_i}&& ; i = 1 \\ \cfrac{c_i}{b_i - a_i c'_{i - 1}}  & & ; i = 2, 3, \dots, n-1 \\\end{array}\end{cases}\tag{3}$$
$$

$$
$$f(x;\mu,\sigma^2) = \frac{1}{\sigma\sqrt{2\pi}} e^{ -\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2 } \tag{1}$$

where $\mu$ is the mean value, $\sigma^2$ is standard deviation.



Fig 1: Rigidly terminated string with the simplest frequency-dependent loss filter.  All loss factors (possibly including losses due to yielding terminations) have been consolidated at a single point and replaced by a one-zero filter approximation
![KarplusStrong](http://img.blog.csdn.net/20150925112421105)



![Panda](https://img-blog.csdn.net/20151109165400641)
