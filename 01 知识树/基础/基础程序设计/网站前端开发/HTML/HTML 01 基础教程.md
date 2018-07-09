# HTML 简介

## 实例

```
<html>
<body>
<h1>My First Heading</h1>
<p>My first paragraph.</p>
</body>
</html>
```

## 什么是 HTML？

HTML 是用来描述网页的一种语言。

- HTML 指的是超文本标记语言 (**H**yper **T**ext **M**arkup **L**anguage)
- HTML 不是一种编程语言，而是一种*标记语言* (markup language)
- 标记语言是一套*标记标签* (markup tag)
- HTML 使用*标记标签*来描述网页

## HTML 标签

HTML 标记标签通常被称为 HTML 标签 (HTML tag)。

- HTML 标签是由*尖括号*包围的关键词，比如 <html>
- HTML 标签通常是*成对出现*的，比如 <b> 和 </b>
- 标签对中的第一个标签是*开始标签*，第二个标签是*结束标签*
- 开始和结束标签也被称为*开放标签*和*闭合标签*

## HTML 文档 = 网页

- HTML 文档*描述网页*
- HTML 文档*包含 HTML 标签*和纯文本
- HTML 文档也被称为*网页*

Web 浏览器的作用是读取 HTML 文档，并以网页的形式显示出它们。浏览器不会显示 HTML 标签，而是使用标签来解释页面的内容：

```
<html>
<body>
<h1>My First Heading</h1>
<p>My first paragraph.</p>
</body>
</html>
```

### 例子解释

- `<html>` 与 `</html>` 之间的文本描述网页
- `<body>` 与 `</body>` 之间的文本是可见的页面内容
- `<h1>` 与 `</h1>` 之间的文本被显示为标题
- `<p>` 与 `</p>` 之间的文本被显示为段落








# 基本的 HTML 标签 - 四个实例

**本章通过实例向您演示最常用的 HTML 标签。**

提示：不要担心本章中您还没有学过的例子，您将在下面的章节中学到它们。

提示：学习 HTML 最好的方式就是边学边做实验。我们为您准备了很好的 HTML 编辑器。使用这个编辑器，您可以任意编辑一段 HTML 源码，然后单击 TIY 按钮来查看结果。

## HTML 标题

HTML 标题（Heading）是通过 `<h1>` - `<h6>` 等标签进行定义的。

### 实例

```
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

## HTML 段落

HTML 段落是通过 `<p>` 标签进行定义的。

### 实例

```
<p>This is a paragraph.</p>
<p>This is another paragraph.</p>
```

## HTML 链接

HTML 链接是通过 `<a>` 标签进行定义的。<font color=red>为什么 a 是链接？</font>

### 实例

```
<a href="http://www.w3school.com.cn">This is a link</a>
```

注释：在 href 属性中指定链接的地址。

（您将在本教程稍后的章节中学习更多有关属性的知识）。

## HTML 图像

HTML 图像是通过 `<img>` 标签进行定义的。

### 实例

```
<img src="w3school.jpg" width="104" height="142" />
```

注释：图像的名称和尺寸是以属性的形式提供的。








# HTML 元素

**HTML 文档是由 HTML 元素定义的。**

## HTML 元素

HTML 元素指的是从开始标签（start tag）到结束标签（end tag）的所有代码。

| 开始标签                  | 元素内容            | 结束标签 |
| ------------------------- | ------------------- | -------- |
| `<p>`                     | This is a paragraph | `</p>`   |
| `<a href="default.htm" >` | This is a link      | `</a>`   |
| `<br />`                  |                     |          |

注释：开始标签常被称为开放标签（opening tag），结束标签常称为闭合标签（closing tag）。

## HTML 元素语法

- HTML 元素以*开始标签*起始
- HTML 元素以*结束标签*终止
- *元素的内容*是开始标签与结束标签之间的内容
- 某些 HTML 元素具有*空内容（empty content）*
- 空元素*在开始标签中进行关闭*（以开始标签的结束而结束）
- 大多数 HTML 元素可拥有*属性*

提示：您将在本教程的下一章中学习更多有关属性的内容。

## 嵌套的 HTML 元素

大多数 HTML 元素可以嵌套（可以包含其他 HTML 元素）。

HTML 文档由嵌套的 HTML 元素构成。

### HTML 文档实例

```
<html>

<body>
<p>This is my first paragraph.</p>
</body>

</html>
```

上面的例子包含三个 HTML 元素。

## HTML 实例解释

### `<p>` 元素：

```
<p>This is my first paragraph.</p>
```

这个 `<p>` 元素定义了 HTML 文档中的一个段落。

这个元素拥有一个开始标签 `<p>`，以及一个结束标签 `</p>`。

元素内容是：This is my first paragraph。

### `<body>` 元素：

```
<body>
<p>This is my first paragraph.</p>
</body>
```

`<body>` 元素定义了 HTML 文档的主体。

这个元素拥有一个开始标签 `<body>`，以及一个结束标签 `</body>`。

元素内容是另一个 HTML 元素（p 元素）。

### `<html>` 元素：

```
<html>

<body>
<p>This is my first paragraph.</p>
</body>

</html>
```

`<html>` 元素定义了整个 HTML 文档。

这个元素拥有一个开始标签 `<html>`，以及一个结束标签 `</html>`。

元素内容是另一个 HTML 元素（body 元素）。

## 不要忘记结束标签

即使您忘记了使用结束标签，大多数浏览器也会正确地显示 HTML：

```
<p>This is a paragraph
<p>This is a paragraph
```

上面的例子在大多数浏览器中都没问题，但不要依赖这种做法。忘记使用结束标签会产生不可预料的结果或错误。

注释：未来的 HTML 版本不允许省略结束标签。

## 空的 HTML 元素

没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。

`<br>` 就是没有关闭标签的空元素（`<br>` 标签定义换行）。

在 XHTML、XML 以及未来版本的 HTML 中，所有元素都必须被关闭。

在开始标签中添加斜杠，比如 `<br />`，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。

即使 `<br>` 在所有浏览器中都是有效的，但使用 `<br />` 其实是更长远的保障。

## HTML 提示：使用小写标签

HTML 标签对大小写不敏感：`<P>` 等同于 `<p>`。许多网站都使用大写的 HTML 标签。

W3School 使用的是小写标签，因为万维网联盟（W3C）在 HTML 4 中*推荐*使用小写，而在未来 (X)HTML 版本中*强制*使用小写。





# HTML 属性

**属性为 HTML 元素提供附加信息。**

## HTML 属性

HTML 标签可以拥有*属性*。属性提供了有关 HTML 元素的*更多的信息*。

属性总是以名称/值对的形式出现，比如：*name="value"*。

属性总是在 HTML 元素的*开始标签*中规定。

## 属性实例

HTML 链接由 `<a>` 标签定义。链接的地址在 href 属性中指定：

```
<a href="http://www.w3school.com.cn">This is a link</a>
```

## 更多 HTML 属性实例

### 属性例子 1:

`<h1>` 定义标题的开始。

`<h1 align="center">` 拥有关于对齐方式的附加信息。


### 属性例子 2:

`<body>` 定义 HTML 文档的主体。

`<body bgcolor="yellow">` 拥有关于背景颜色的附加信息。


### 属性例子 3:

`<table>` 定义 HTML 表格。（您将在稍后的章节学习到更多有关 HTML 表格的内容）

`<table border="1">` 拥有关于表格边框的附加信息。

## HTML 提示：使用小写属性

属性和属性值对大小写*不敏感*。

不过，万维网联盟在其 HTML 4 推荐标准中推荐小写的属性/属性值。

而新版本的 (X)HTML 要求使用小写属性。

## 始终为属性值加引号

属性值应该始终被包括在引号内。双引号是最常用的，不过使用单引号也没有问题。

在某些个别的情况下，比如属性值本身就含有双引号，那么您必须使用单引号，例如：

```html
name='Bill "HelloWorld" Gates'
```

## HTML 属性参考手册

我们的完整的 HTML 参考手册提供了每个 HTML 元素可使用的合法属性的完整列表：<font color=red>完整列表要补充进来</font>
[完整的 HTML 参考手册](http://www.w3school.com.cn/tags/index.asp)

下面列出了适用于大多数 HTML 元素的属性：

| 属性  | 值                 | 描述                                     |
| ----- | ------------------ | ---------------------------------------- |
| class | *classname*        | 规定元素的类名（classname）              |
| id    | *id*               | 规定元素的唯一 id                        |
| style | *style_definition* | 规定元素的行内样式（inline style）       |
| title | *text*             | 规定元素的额外信息（可在工具提示中显示） |

如需更多关于标准属性的信息，请访问：

[HTML 标准属性参考手册](http://www.w3school.com.cn/tags/html_ref_standardattributes.asp)






# HTML 标题

**在 HTML 文档中，标题很重要。**

## HTML 标题

标题（Heading）是通过 `<h1>` - `<h6>` 等标签进行定义的。

`<h1>` 定义最大的标题。`<h6>` 定义最小的标题。<font color=red> 6 是最小的标题吗？</font>

### 实例

```html
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

注释：浏览器会自动地在标题的前后添加空行。
注释：默认情况下，HTML 会自动地在块级元素前后添加一个额外的空行，比如段落、标题元素前后。<font color=red>为什么要自动添加空行？</font>

## 标题很重要

请确保将 HTML heading 标签只用于标题。不要仅仅是为了产生粗体或大号的文本而使用标题。

搜索引擎使用标题为您的网页的结构和内容编制索引。<font color=red>嗯</font>

因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。

应该将 h1 用作主标题（最重要的），其后是 h2（次重要的），再其次是 h3，以此类推。

## HTML 水平线

`<hr />` 标签在 HTML 页面中创建水平线。

hr 元素可用于分隔内容。

### 实例

```html
<p>This is a paragraph</p>
<hr />
<p>This is a paragraph</p>
<hr />
<p>This is a paragraph</p>
```

提示：使用水平线 (`<hr>` 标签) 来分隔文章中的小节是一个办法（但并不是唯一的办法）。

## HTML 注释

可以将注释插入 HTML 代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。

注释是这样写的：

### 实例

```html
<!-- This is a comment -->
```

注释：开始括号之后（左边的括号）需要紧跟一个叹号，结束括号之前（右边的括号）不需要。

提示：合理地使用注释可以对未来的代码编辑工作产生帮助。

## HTML 提示 - 如何查看源代码

您一定曾经在看到某个网页时惊叹道 “WOW! 这是如何实现的？”

如果您想找到其中的奥秘，只需要单击右键，然后选择“查看源文件”（IE）或“查看页面源代码”（Firefox），其他浏览器的做法也是类似的。这么做会打开一个包含页面 HTML 代码的窗口。

## 来自本页的实例

[标题](http://www.w3school.com.cn/tiy/t.asp?f=html_headers)

[隐藏的注释](http://www.w3school.com.cn/tiy/t.asp?f=html_comment)

[水平线](http://www.w3school.com.cn/tiy/t.asp?f=html_hr)

## HTML 标签参考手册

W3School 的标签参考手册提供了有关这些标题及其属性的更多信息。

您将在本教程下面的章节中学到更多有关 HTML 标签和属性的知识。

| 标签                                                         | 描述             |
| ------------------------------------------------------------ | ---------------- |
| [`<html>`](http://www.w3school.com.cn/tags/tag_html.asp) | 定义 HTML 文档。 |
| [`<body>`](http://www.w3school.com.cn/tags/tag_body.asp) | 定义文档的主体。 |
| [`<h1>` to `<h6>`](http://www.w3school.com.cn/tags/tag_hn.asp) | 定义 HTML 标题   |
| [`<hr>`](http://www.w3school.com.cn/tags/tag_hr.asp) | 定义水平线。     |
| [`<!-->`](http://www.w3school.com.cn/tags/tag_comment.asp) | 定义注释。       |





# HTML 段落

**可以把 HTML 文档分割为若干段落。**

## HTML 段落

段落是通过 `<p>` 标签定义的。

### 实例

```html
<p>This is a paragraph</p>
<p>This is another paragraph</p>
```
注释：浏览器会自动地在段落的前后添加空行。（`<p>` 是块级元素）

提示：使用空的段落标记 `<p></p>` 去插入一个空行是个坏习惯。用 `<br />` 标签代替它！（但是不要用 `<br />` 标签去创建列表。不要着急，您将在稍后的篇幅学习到 HTML 列表。）

## 不要忘记结束标签

即使忘了使用结束标签，大多数浏览器也会正确地将 HTML 显示出来：

### 实例

```html
<p>This is a paragraph
<p>This is another paragraph
```

上面的例子在大多数浏览器中都没问题，但不要依赖这种做法。忘记使用结束标签会产生意想不到的结果和错误。

注释：在未来的 HTML 版本中，不允许省略结束标签。

提示：通过结束标签来关闭 HTML 是一种经得起未来考验的 HTML 编写方法。清楚地标记某个元素在何处开始，并在何处结束，不论对您还是对浏览器来说，都会使代码更容易理解。

## HTML 折行

如果您希望在不产生一个新段落的情况下进行换行（新行），请使用 `<br />` 标签：

```html
<p>This is<br />a para<br />graph with line breaks</p>
```


`<br />` 元素是一个空的 HTML 元素。由于关闭标签没有任何意义，因此它没有结束标签。

## `<br>` 还是 `<br />`

您也许发现 `<br>` 与 `<br />` 很相似。

在 XHTML、XML 以及未来的 HTML 版本中，不允许使用没有结束标签（闭合标签）的 HTML 元素。

即使 `<br>` 在所有浏览器中的显示都没有问题，使用 `<br />` 也是*更长远的保障*。<font color=red>嗯</font>

## HTML 输出 - 有用的提示

我们无法确定 HTML 被显示的确切效果。屏幕的大小，以及对窗口的调整都可能导致不同的结果。

对于 HTML，您无法通过在 HTML 代码中添加额外的空格或换行来改变输出的效果。

当显示页面时，浏览器会移除*源代码中*多余的空格和空行。所有连续的空格或空行都会被算作一个空格。需要注意的是，HTML 代码中的所有连续的空行（换行）也被显示为一个空格。<font color=red>这个要注意</font>


## 来自本页的实例

- [HTML 段落](http://www.w3school.com.cn/tiy/t.asp?f=html_paragraphs1)
  如何在浏览器中显示 HTML 段落。
- [换行](http://www.w3school.com.cn/tiy/t.asp?f=html_paragraphs)
  在 HTML 文档中使用换行。
- [在 HTML 代码中的排版一首唐诗](http://www.w3school.com.cn/tiy/t.asp?f=html_poem)
  浏览器在显示 HTML 时，会省略源代码中多余的空白字符（空格或回车等）。

### 更多实例

- [更多段落](http://www.w3school.com.cn/tiy/t.asp?f=html_paragraphs2)
  段落的默认行为。

## HTML 标签参考手册

W3School 的标签参考手册提供了有关 HTML 元素及其属性的更多信息。

| 标签                                            | 描述                   |
| ----------------------------------------------- | ---------------------- |
| [`<p>`](http://www.w3school.com.cn/tags/tag_p.asp)   | 定义段落。             |
| [`<br />`](http://www.w3school.com.cn/tags/tag_br.asp) | 插入单个折行（换行）。 |






# HTML 样式

style 属性用于改变 HTML 元素的样式。

```html
<html>
<body style="background-color:PowderBlue;">

<h1>Look! Styles and colors</h1>

<p style="font-family:verdana;color:red">
This text is in Verdana and red</p>

<p style="font-family:times;color:green">
This text is in Times and green</p>

<p style="font-size:30px">This text is 30 pixels high</p>

</body>
</html>
```
输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/f73DBm5754.png?imageslim)


## HTML 的 style 属性

style 属性的作用：

**提供了一种改变所有 HTML 元素的样式的通用方法。**

样式是 HTML 4 引入的，它是一种新的首选的改变 HTML 元素样式的方式。通过 HTML 样式，能够通过使用 style 属性直接将样式添加到 HTML 元素，或者间接地在独立的样式表中（CSS 文件）进行定义。

您可以在我们的 [CSS 教程](http://www.w3school.com.cn/css/index.asp)中学习关于样式和 CSS 的所有知识。

在我们的 HTML 教程中，我们将使用 style 属性向您讲解 HTML 样式。

## 不赞成使用的标签和属性

在 HTML 4 中，有若干的标签和属性是被废弃的。被废弃（Deprecated）的意思是在未来版本的 HTML 和 XHTML 中将不支持这些标签和属性。

这里传达的信息很明确：请避免使用这些被废弃的标签和属性！
<span style="color:red;">这个 font 标签不推荐使用了，那么怎么设置字体颜色呢？确认下:这么写 `<span style="color:red;"></span>`</span>


### 应该避免使用下面这些标签和属性：

| 标签                 | 描述               |
| -------------------- | ------------------ |
| `<center> `            | 定义居中的内容。   |
| `<font> `和 `<basefont>` | 定义 HTML 字体。   |
| `<s>` 和 `<strike>`      | 定义删除线文本     |
| `<u>`                  | 定义下划线文本     |


| 属性    | 描述               |
| ------- | ------------------ |
| align   | 定义文本的对齐方式 |
| bgcolor | 定义背景颜色       |
| color   | 定义文本颜色       |

对于以上这些标签和属性：请使用样式代替！

## HTML 样式实例 - 背景颜色

background-color 属性为元素定义了背景颜色：

```html
<html>
<body style="background-color:yellow">
<h2 style="background-color:red">This is a heading</h2>
<p style="background-color:green">This is a paragraph.</p>
</body>
</html>
```
输出：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/laliaKk9a1.png?imageslim)


style 属性淘汰了“旧的” bgcolor 属性。<span style="color:red;">嗯，这个bgcolor 也不推荐使用了。</span>

## HTML 样式实例 - 字体、颜色和尺寸

font-family、color 以及 font-size 属性分别定义元素中文本的字体系列、颜色和字体尺寸：

```html
<html>
<body>
<h1 style="font-family:verdana">A heading</h1>
<p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
</body>
</html>
```
输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/3lKKjHl9fA.png?imageslim)

style 属性淘汰了旧的 `<font>` 标签。

## HTML 样式实例 - 文本对齐

text-align 属性规定了元素中文本的水平对齐方式：

```html
<html>
<body>

<h1 style="text-align:center">This is a heading</h1>
<p>上面的标题相对于页面居中对齐。</p>

</body>
</html>
```
输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/Hb3bHHDDgC.png?imageslim)


style 属性淘汰了旧的 "align" 属性。








# HTML 文本格式化
<span style="color:red;">这个还是要好好看下的，基本没怎么细看</span>

**HTML 可定义很多供格式化输出的元素，比如粗体和斜体字。**

**下面有很多例子，您可以亲自试试：**

## HTML 文本格式化实例

- [文本格式化](http://www.w3school.com.cn/tiy/t.asp?f=html_textformatting)
  此例演示如何在一个 HTML 文件中对文本进行格式化
- [预格式文本](http://www.w3school.com.cn/tiy/t.asp?f=html_preformattedtext)
  此例演示如何使用 pre 标签对空行和空格进行控制。
- [“计算机输出”标签](http://www.w3school.com.cn/tiy/t.asp?f=html_computeroutput)
  此例演示不同的“计算机输出”标签的显示效果。
- [地址](http://www.w3school.com.cn/tiy/t.asp?f=html_address)
  此例演示如何在 HTML 文件中写地址。
- [缩写和首字母缩写](http://www.w3school.com.cn/tiy/t.asp?f=html_abbracronym)
  此例演示如何实现缩写或首字母缩写。
- [文字方向](http://www.w3school.com.cn/tiy/t.asp?f=html_bdo)
  此例演示如何改变文字的方向。
- [块引用](http://www.w3school.com.cn/tiy/t.asp?f=html_quotations)
  此例演示如何实现长短不一的引用语。
- [删除字效果和插入字效果](http://www.w3school.com.cn/tiy/t.asp?f=html_delins)
  此例演示如何标记删除文本和插入文本。

## 如何查看 HTML 源码

您是否有过这样的经历，当你看到一个很棒的站点，你会很想知道开发人员是如何将它实现的？

你有没有看过一些网页，并且想知道它是如何做出来的呢？

要揭示一个网站的技术秘密，其实很简单。单击浏览器的“查看”菜单，选择“查看源文件”即可。随后你会看到一个弹出的窗口，窗口内就是实际的 HTML 代码。

## 文本格式化标签

| 标签                                                        | 描述                                  |
| ----------------------------------------------------------- | ------------------------------------- |
| [`<b>`](http://www.w3school.com.cn/tags/tag_font_style.asp)      | 定义粗体文本。                        |
| [`<big>`](http://www.w3school.com.cn/tags/tag_font_style.asp)      | 定义大号字。                          |
| [`<em>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp) | 定义着重文字。                        |
| [`<i>`](http://www.w3school.com.cn/tags/tag_font_style.asp)      | 定义斜体字。                          |
| [`<small>`](http://www.w3school.com.cn/tags/tag_font_style.asp)      | 定义小号字。                          |
| [`<strong>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp) | 定义加重语气。                        |
| [`<sub>`](http://www.w3school.com.cn/tags/tag_sup.asp)             | 定义下标字。                          |
| [`<sup>`](http://www.w3school.com.cn/tags/tag_sup.asp)             | 定义上标字。                          |
| [`<ins>`](http://www.w3school.com.cn/tags/tag_ins.asp)             | 定义插入字。                          |
| [`<del>`](http://www.w3school.com.cn/tags/tag_del.asp)             | 定义删除字。                          |
| [`<s>`](http://www.w3school.com.cn/tags/tag_strike.asp)          | *不赞成使用。*使用 `<del>` 代替。       |
| [`<strike>`](http://www.w3school.com.cn/tags/tag_strike.asp)          | *不赞成使用。*使用 `<del>` 代替。       |
| [`<u>`](http://www.w3school.com.cn/tags/tag_u.asp)               | *不赞成使用。*使用样式（style）代替。 |

## “计算机输出”标签

| 标签                                                                | 描述                            |
| ------------------------------------------------------------------- | ------------------------------- |
| [`<code>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp) | 定义计算机代码。                |
| [`<kbd>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp)  | 定义键盘码。                    |
| [`<samp>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp)         | 定义计算机代码样本。            |
| [`<tt>`](http://www.w3school.com.cn/tags/tag_font_style.asp)              | 定义打字机代码。                |
| [`<var>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp)         | 定义变量。                      |
| [`<pre>`](http://www.w3school.com.cn/tags/tag_pre.asp)                     | 定义预格式文本。                |
| `<listing> `                                                          | *不赞成使用。*使用 `<pre>` 代替。 |
| `<plaintext> `                                                        | *不赞成使用。*使用 `<pre>` 代替。 |
| `<xmp> `                                                              | *不赞成使用。*使用 `<pre>` 代替。 |

## 引用、引用和术语定义

| 标签                                                        | 描述               |
| ----------------------------------------------------------- | ------------------ |
| [`<abbr>`](http://www.w3school.com.cn/tags/tag_abbr.asp)    | 定义缩写。         |
| [`<acronym>`](http://www.w3school.com.cn/tags/tag_acronym.asp)         | 定义首字母缩写。   |
| [`<address>`](http://www.w3school.com.cn/tags/tag_address.asp)         | 定义地址。         |
| [`<bdo>`](http://www.w3school.com.cn/tags/tag_bdo.asp)             | 定义文字方向。     |
| [`<blockquote>`](http://www.w3school.com.cn/tags/tag_blockquote.asp)      | 定义长的引用。     |
| [`<q>`](http://www.w3school.com.cn/tags/tag_q.asp)               | 定义短的引用语。   |
| [`<cite>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp) | 定义引用、引证。   |
| [`<dfn>`](http://www.w3school.com.cn/tags/tag_phrase_elements.asp) | 定义一个定义项目。 |

## 延伸阅读：

[改变文本的外观和含义](http://www.w3school.com.cn/html/html_style.asp)







# HTML 引用

## 引用（Quotation）

这是摘自 WWF 网站的引文：

> 五十年来，WWF 一直致力于保护自然界的未来。 世界领先的环保组织，WWF 工作于 100 个国家，并得到美国一百二十万会员及全球近五百万会员的支持。

## HTML `<q>` 用于短的引用

HTML *`<q>`* 元素定义*短的引用*。

浏览器通常会为 `<q>` 元素包围*引号*。

### 实例

```html
<!DOCTYPE html>
<html>
<body>
<p>浏览器通常会在 q 元素周围包围引号。</p>
<p>WWF 的目标是 <q>构建人与自然和谐相处的世界。</q></p>
</body>
</html>
```
输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/LkfKHF2lDl.png?imageslim)

## 用于长引用的 HTML `<blockquote>`

HTML *`<blockquote>`* 元素定义被引用的节。

浏览器通常会对 `<blockquote>` 元素进行*缩进*处理。

### 实例

```html
<!DOCTYPE html>
<html>
<body>
<p>浏览器通常会对 blockquote 元素进行缩进处理。</p>
<blockquote cite="http://www.worldwildlife.org/who/index.html">
五十年来，WWF 一直致力于保护自然界的未来。
WWF 工作于 100 个国家，并得到美国一百二十万会员及全球近五百万会员的支持。
</blockquote>
</body>
</html>
```
输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/c77bIDBa1H.png?imageslim)

## 用于缩略词的 HTML `<abbr>`

HTML *`<abbr>`* 元素定义*缩写*或首字母缩略语。

对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。<span style="color:red;">什么意思？</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p><abbr title="World Health Organization">WHO</abbr> 成立于 1948 年。</p>

<p>对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。</p>

</body>
</html>
```
输出：<span style="color:red;">为什么这个WHO 字样下面有一些点点？什么时候使用这个 abbr？</span>

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/AF9K8F121g.png?imageslim)

## 用于定义的 HTML `<dfn>`

HTML *`<dfn>`* 元素定义项目或缩写的*定义*。

`<dfn>` 的用法，按照 HTML5 标准中的描述，有点复杂：<span style="color:red;">没明白？</span>

 1. 如果设置了 `<dfn>` 元素的 title 属性，则定义项目：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>The <dfn title="World Health Organization">WHO</dfn> was founded in 1948.</p>

<p>对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。</p>

</body>
</html>

```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/6gJe5al7Gl.png?imageslim)

2. 如果 `<dfn>` 元素包含具有标题的 `<abbr>` 元素，则 title 定义项目：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>The
<dfn><abbr title="World Health Organization">WHO</abbr></dfn>
was founded in 1948.
</p>

<p>对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/CBmecKa8lF.png?imageslim)

3. 否则，<dfn> 文本内容即是项目，并且父元素包含定义。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>The
<dfn>WHO</dfn> World Health Organization was founded in 1948.
</p>

<p>对缩写进行标记能够为浏览器、翻译系统以及搜索引擎提供有用的信息。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/fjfBFkdhEI.png?imageslim)

注释：如果您希望简而化之，请使用第一条，或使用 <abbr> 代替。

## 用于联系信息的 HTML `<address>`

HTML *`<address>`* 元素定义文档或文章的联系信息（作者/拥有者）。

此元素通常以*斜体*显示。大多数浏览器会在此元素前后添加折行。<span style="color:red;">还可以这样。</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>
<p>HTML address 元素定义文档或文章的联系信息（作者/拥有者）。</p>
<address>
Written by Jon Doe.<br>
Visit us at:<br>
Example.com<br>
Box 564, Disneyland<br>
USA
</address>
</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/blbBf88Ig0.png?imageslim)

## 用于著作标题的 HTML `<cite>`

HTML *`<cite>`* 元素定义*著作的标题*。

浏览器通常会以斜体显示 `<cite>` 元素。<span style="color:red;">还可以这样。</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>HTML cite 元素定义著作的标题。</p>
<p>浏览器通常会以斜体显示 cite 元素。</p>

<img src="img_the_scream.jpg" width="220" height="277" alt="The Scream">
<p><cite>The Scream</cite> by Edward Munch. Painted in 1893.</p>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/LLgIIaHaAg.png?imageslim)

## 用于双向重写的 HTML `<bdo>`

HTML *`<bdo>`* 元素定义双流向覆盖（bi-directional override）。

`<bdo>` 元素用于覆盖当前文本方向：<span style="color:red;">这个什么时候回用到？</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
如果您的浏览器支持 bdo，则文本将从右向左进行书写 (rtl)：
</p>

<bdo dir="rtl">
This line will be written from right to left
</bdo>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/hG7LiGkgdf.png?imageslim)

## HTML 引文、引用和定义元素

| 标签         | 描述                             |
| ------------ | -------------------------------- |
| `<abbr>`       | 定义缩写或首字母缩略语。         |
| `<address>`    | 定义文档作者或拥有者的联系信息。 |
| `<bdo>`        | 定义文本方向。                   |
| `<blockquote>` | 定义从其他来源引用的节。         |
| `<dfn>`        | 定义项目或缩略词的定义。         |
| `<q>`          | 定义短的行内引用。               |
| `<cite>`       | 定义著作的标题。                 |









# HTML 计算机代码元素


## 计算机代码

```html
var person = {
    firstName:"Bill",
    lastName:"Gates",
    age:50,
    eyeColor:"blue"
}
```

## HTML 计算机代码格式

通常，HTML 使用*可变*的字母尺寸，以及可变的字母间距。

在显示*计算机代码*示例时，并不需要如此。

*`<kbd>`*, *`<samp>`*, 以及 *`<code>`* 元素全都支持固定的字母尺寸和间距。

## HTML 键盘格式

HTML *`<kbd>`* 元素定义*键盘输入*：

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">

<p>HTML kbd 元素表示键盘输入：</p>

<p><kbd>File | Open...</kbd></p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/c0AKe7gAEK.png?imageslim)

## HTML 样本格式

HTML *`<samp>`* 元素定义*计算机输出示例*：

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">

<p>HTML samp 元素表示计算机输出示例：</p>

<samp>
demo.example.com login: Apr 12 09:10:17
Linux 2.6.10-grsec+gg3+e+fhs6b+nfs+gr0501+++p3+c4a+gr2b-reslog-v6.189
</samp>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/aHFe8bEHiA.png?imageslim)

## HTML 代码格式

HTML *`<code>`* 元素定义*编程代码示例*：

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">

<p>编程代码示例：</p>

<code>
var person = {firstName:"Bill", lastName:"Gats", age:50}
</code>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/1ddLcb2H4H.png?imageslim)

`<code>` 元素*不保留*多余的*空格*和*折行*：

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">

<p>code 元素不保留多余的空格和折行：</p>

<code>
var person = {
    firstName:"Bill",
    lastName:"Gates",
    age:50,
    eyeColor:"blue"
}
</code>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/K3ELBJAbi3.png?imageslim)

如需解决该问题，您必须在 `<pre>` 元素中包围代码：<span style="color:red;">是这样吗？为什么要用pre？</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">
<p>code 元素不保留多余的空格和折行：</p>
<p>如需解决该问题，您必须在 pre 元素中包围代码：</p>
<code>
<pre>
var person = {
    firstName:"Bill",
    lastName:"Gates",
    age:50,
    eyeColor:"blue"
}
</pre>
</code>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/hd68mIag7E.png?imageslim)

## HTML 变量格式化

HTML *`<var>`* 元素定义*数学变量*：<span style="color:red;">这也可以！</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body style="font-size:16px">

<p>爱因斯坦的公式：</p>

<p><var>E</var> = <var>m</var> <var>c</var><sup>2</sup></p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/2aiLegBbL1.png?imageslim)

## HTML 计算机代码元素

| 标签   | 描述               |
| ------ | ------------------ |
| `<code>` | 定义计算机代码文本 |
| `<kbd>`  | 定义键盘文本       |
| `<samp>` | 定义计算机代码示例 |
| `<var>`  | 定义变量           |
| `<pre>`  | 定义预格式化文本   |






# HTML 注释

**注释标签 <!-- 与 --> 用于在 HTML 插入注释。**

## HTML 注释标签

您能够通过如下语法向 HTML 源代码添加注释：

### 实例

```html
<!-- 在此处写注释 -->
```

注释：在开始标签中有一个惊叹号，但是结束标签中没有。

浏览器不会显示注释，但是能够帮助记录您的 HTML 文档。

您可以利用注释在 HTML 中放置通知和提醒信息：

### 实例

```html
<!DOCTYPE html>
<html>
<body>
<!--这是一段注释。注释不会在浏览器中显示。-->
<p>这是一段普通的段落。</p>
</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/K32eimBKjb.png?imageslim)

注释对于 HTML 纠错也大有帮助，因为您可以一次注释一行 HTML 代码，以搜索错误：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<!-- 此时不显示图片
<img border="0" src="http://www.ypppt.com/uploads/allimg/180303/1-1P3031301360-L.jpg" alt="Tulip">
-->

</body>
</html>
```


## 条件注释

您也许会在 HTML 中偶尔发现条件注释：

```html
<!--[if IE 8]>
    .... some HTML here ....
<![endif]-->
```

条件注释定义只有 Internet Explorer 执行的 HTML 标签。<span style="color:red;">这个在 IE8 的时候回执行吗？</span>

## 软件程序标签

各种 HTML 软件程序也能够生成 HTML 注释。

例如 <!--webbot bot--> 标签会被包围在由 FrontPage 和 Expression Web 创建的 HTML 注释中。

作为一项规则，这些标签的存在，有助于对创建这些标签的软件的支持。




# HTML CSS

**通过使用 HTML4.0，所有的格式化代码均可移出 HTML 文档，然后移入一个独立的样式表。**

## 实例

- [HTML中的样式](http://www.w3school.com.cn/tiy/t.asp?f=html_style)
  本例演示如何使用添加到 <head> 部分的样式信息对 HTML 进行格式化。
- [没有下划线的链接](http://www.w3school.com.cn/tiy/t.asp?f=html_linknoline)
  本例演示如何使用样式属性做一个没有下划线的链接。
- [链接到一个外部样式表](http://www.w3school.com.cn/tiy/t.asp?f=html_link)
  本例演示如何 <link> 标签链接到一个外部样式表。

## 如何使用样式

当浏览器读到一个样式表，它就会按照这个样式表来对文档进行格式化。有以下三种方式来插入样式表：

### 外部样式表

当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。<span style="color:red;">嗯，的确</span>

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

### 内部样式表

当单个文件需要特别样式时，就可以使用内部样式表。你可以在 head 部分通过 `<style>` 标签定义内部样式表。<span style="color:red;">内部样式表一定是在head 里面的吗？</span>

```html
<head>
<style type="text/css">
body {background-color: red}
p {margin-left: 20px}
</style>
</head>
```

### 内联样式

当特殊的样式需要应用到个别元素时，就可以使用内联样式。 使用内联样式的方法是在相关的标签中使用样式属性。样式属性可以包含任何 CSS 属性。以下实例显示出如何改变段落的颜色和左外边距。

```html
<p style="color: red; margin-left: 20px">
This is a paragraph
</p>
```

访问我们的 [CSS 教程](http://www.w3school.com.cn/css/index.asp)，学习更多有关样式的知识。

| 标签                                                 | 描述                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| [`<style>`](http://www.w3school.com.cn/tags/tag_style.asp)    | 定义样式定义。                                               |
| [`<link>`](http://www.w3school.com.cn/tags/tag_link.asp)     | 定义资源引用。                                               |
| [`<div>`](http://www.w3school.com.cn/tags/tag_div.asp)      | 定义文档中的节或区域（块级）。                               |
| [`<span>`](http://www.w3school.com.cn/tags/tag_span.asp)     | 定义文档中的行内的小块或区域。                               |
| [`<font>`](http://www.w3school.com.cn/tags/tag_font.asp)     | 规定文本的字体、字体尺寸、字体颜色。不赞成使用。请使用样式。 |
| [`<basefont>`](http://www.w3school.com.cn/tags/tag_basefont.asp) | 定义基准字体。不赞成使用。请使用样式。                       |
| [`<center>`](http://www.w3school.com.cn/tags/tag_center.asp)   | 对文本进行水平居中。不赞成使用。请使用样式。                 |









# HTML 链接

**HTML 使用超级链接与网络上的另一个文档相连。**

**几乎可以在所有的网页中找到链接。点击链接可以从一张页面跳转到另一张页面。**

## 实例

- [创建超级链接](http://www.w3school.com.cn/tiy/t.asp?f=html_links)
  本例演示如何在 HTML 文档中创建链接。
- [将图像作为链接](http://www.w3school.com.cn/tiy/t.asp?f=html_imglink)
  本例演示如何使用图像作为链接。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_links.asp#more_examples)）

## HTML 超链接（链接）

超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。

当您把鼠标指针移动到网页中的某个链接上时，箭头会变为一只小手。

我们通过使用 `<a>` 标签在 HTML 中创建链接。

有两种使用 `<a>` 标签的方式：

1. 通过使用 href 属性 - 创建指向另一个文档的链接
2. 通过使用 name 属性 - 创建文档内的书签 <span style="color:red;">什么意思？</span>

延伸阅读：[什么是超文本？](http://www.w3school.com.cn/tags/tag_term_hypertext.asp)

## HTML 链接语法

链接的 HTML 代码很简单。它类似这样：

```html
<a href="url">Link text</a>
```

href 属性规定链接的目标。

开始标签和结束标签之间的文字被作为超级链接来显示。

### 实例

```html
<a href="http://www.w3school.com.cn/">Visit W3School</a>
```

上面这行代码显示为：[Visit W3School](http://www.w3school.com.cn/)

点击这个超链接会把用户带到 W3School 的首页。

提示："链接文本" 不必一定是文本。图片或其他 HTML 元素都可以成为链接。

## HTML 链接 - target 属性

使用 Target 属性，你可以定义被链接的文档在何处显示。<span style="color:red;">这个不错</span>

下面这个会在新窗口打开文档：

```html
<html>
<body>
<a href="http://www.w3school.com.cn/" target="_blank">Visit W3School!</a>
<p>如果把链接的 target 属性设置为 "_blank"，该链接会在新窗口中打开。</p>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/jbh5LlgBgi.png?imageslim)

## HTML 链接 - name 属性

name 属性规定锚（anchor）的名称。

您可以使用 name 属性创建 HTML 页面中的书签。

书签不会以任何特殊方式显示，它对读者是不可见的。

当使用命名锚（named anchors）时，我们可以创建直接跳至该命名锚（比如页面中某个小节）的链接，这样使用者就无需不停地滚动页面来寻找他们需要的信息了。<span style="color:red;">嗯，TOC 应该就是这么做的。</span>

### 命名锚的语法：

```html
<a name="label">锚（显示在页面上的文本）</a>
```

提示：锚的名称可以是任何你喜欢的名字。
提示：您可以使用 id 属性来替代 name 属性，命名锚同样有效。

### 实例

首先，我们在 HTML 文档中对锚进行命名（创建一个书签）：<span style="color:red;">之前不知道锚也是用 a 做的。</span>

```html
<a name="tips">基本的注意事项 - 有用的提示</a>
```

然后，我们在同一个文档中创建指向该锚的链接：

```html
<a href="#tips">跳转到有用的提示</a>
```

您也可以在其他页面中创建指向该锚的链接：

```html
<a href="http://www.w3school.com.cn/html/html_links.asp#tips">跳转到有用的提示</a>
```

在上面的代码中，我们将 # 符号和锚名称添加到 URL 的末端，就可以直接链接到 tips 这个命名锚了。

具体效果：[跳转到有用的提示](http://www.w3school.com.cn/html/html_links.asp#tips)

## 基本的注意事项 - 有用的提示：

注释：请始终将正斜杠添加到子文件夹。假如这样书写链接：href="http://www.w3school.com.cn/html"，就会向服务器产生两次 HTTP 请求。这是因为服务器会添加正斜杠到这个地址，然后创建一个新的请求，就像这样：href="http://www.w3school.com.cn/html/"。<span style="color:red;">这个地方没明白？什么时候回产生两次请求？</span>

提示：命名锚经常用于在大型文档开始位置上创建目录。可以为每个章节赋予一个命名锚，然后把链接到这些锚的链接放到文档的上部。如果您经常访问百度百科，您会发现其中几乎每个词条都采用这样的导航方式。

提示：假如浏览器找不到已定义的命名锚，那么就会定位到文档的顶端。不会有错误发生。<span style="color:red;">嗯</span>

## 更多实例

- [在新的浏览器窗口打开链接](http://www.w3school.com.cn/tiy/t.asp?f=html_link_target)
  本例演示如何在新窗口打开一个页面，这样的话访问者就无需离开你的站点了。
- [链接到同一个页面的不同位置](http://www.w3school.com.cn/tiy/t.asp?f=html_link_locations)
  本例演示如何使用链接跳转至文档的另一个部分
- [跳出框架](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_getfree)
  本例演示如何跳出框架，假如你的页面被固定在框架之内。<span style="color:red;">什么意思？</span>
- [创建电子邮件链接](http://www.w3school.com.cn/tiy/t.asp?f=html_mailto)
  本例演示如何链接到一个邮件。（本例在安装邮件客户端程序后才能工作。）<span style="color:red;">这两个 mailto 好像都没有起作用，是需要安装什么吗？</span>
- [创建电子邮件链接 2](http://www.w3school.com.cn/tiy/t.asp?f=html_mailto2)
  本例演示更加复杂的邮件链接。

## HTML 链接标签

| 标签                                          | 描述     |
| --------------------------------------------- | -------- |
| [`<a>`](http://www.w3school.com.cn/tags/tag_a.asp) | 定义锚。 |






# HTML 图像
**通过使用 HTML，可以在文档中显示图像。**

## 实例

- [插入图像](http://www.w3school.com.cn/tiy/t.asp?f=html_image)
  本例演示如何在网页中显示图像。
- [从不同的位置插入图片](http://www.w3school.com.cn/tiy/t.asp?f=html_image2)
  本例演示如何将其他文件夹或服务器的图片显示到网页中。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_images.asp#more_examples)。）

## 图像标签（`<img>`）和源属性（Src）

在 HTML 中，图像由 `<img>` 标签定义。

`<img>` 是空标签，意思是说，它只包含属性，并且没有闭合标签。

要在页面上显示图像，你需要使用源属性（src）。src 指 "source"。源属性的值是图像的 URL 地址。

定义图像的语法是：

```html
<img src="url" />
```

URL 指存储图像的位置。如果名为 "boat.gif" 的图像位于 www.w3school.com.cn 的 images 目录中，那么其 URL 为 http://www.w3school.com.cn/images/boat.gif。

浏览器将图像显示在文档中图像标签出现的地方。如果你将图像标签置于两个段落之间，那么浏览器会首先显示第一个段落，然后显示图片，最后显示第二段。

## 替换文本属性（Alt）

alt 属性用来为图像定义一串预备的可替换的文本。替换文本属性的值是用户定义的。

```html
<img src="boat.gif" alt="Big Boat">
```

在浏览器无法载入图像时，替换文本属性告诉读者她们失去的信息。此时，浏览器将显示这个替代性的文本而不是图像。为页面上的图像都加上替换文本属性是个好习惯，这样有助于更好的显示信息，并且对于那些使用纯文本浏览器的人来说是非常有用的。

## 基本的注意事项 - 有用的提示：

假如某个 HTML 文件包含十个图像，那么为了正确显示这个页面，需要加载 11 个文件。加载图片是需要时间的，所以我们的建议是：慎用图片。<span style="color:red;">嗯，有哪些好的处理这种图片加载的方法？</span>

## 更多实例

- [背景图片](http://www.w3school.com.cn/tiy/t.asp?f=html_backgroundimage)
  本例演示如何向 HTML 页面添加背景图片。
- [排列图片](http://www.w3school.com.cn/tiy/t.asp?f=html_image_align)
  本例演示如何在文字中排列图像。
- [浮动图像](http://www.w3school.com.cn/tiy/t.asp?f=html_image_float)
  本例演示如何使图片浮动至段落的左边或右边。
- [调整图像尺寸](http://www.w3school.com.cn/tiy/t.asp?f=html_image_size)
  本例演示如何将图片调整到不同的尺寸。
- [为图片显示替换文本](http://www.w3school.com.cn/tiy/t.asp?f=html_image_alt)
  本例演示如何为图片显示替换文本。在浏览器无法载入图像时，替换文本属性告诉读者们失去的信息。为页面上的图像都加上替换文本属性是个好习惯。
- [制作图像链接](http://www.w3school.com.cn/tiy/t.asp?f=html_image_link)
  本例演示如何将图像作为一个链接使用。
- [创建图像映射](http://www.w3school.com.cn/tiy/t.asp?f=html_areamap)
  本例显示如何创建带有可供点击区域的图像地图。其中的每个区域都是一个超级链接。
- [把图像转换为图像映射](http://www.w3school.com.cn/tiy/t.asp?f=html_ismap)
  本例显示如何把一幅普通的图像设置为图像映射。

## 图像标签

| 标签                                             | 描述                         |
| ------------------------------------------------ | ---------------------------- |
| [`<img>`](http://www.w3school.com.cn/tags/tag_img.asp)  | 定义图像。                   |
| [`<map>`](http://www.w3school.com.cn/tags/tag_map.asp)  | 定义图像地图。               |
| [`<area>`](http://www.w3school.com.cn/tags/tag_area.asp) | 定义图像地图中的可点击区域。 |




# HTML 表格
**你可以使用 HTML 创建表格。**

## 实例

- [表格](http://www.w3school.com.cn/tiy/t.asp?f=html_tables)
  这个例子演示如何在 HTML 文档中创建表格。
- [表格边框](http://www.w3school.com.cn/tiy/t.asp?f=html_table_borders)
  本例演示各种类型的表格边框。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_tables.asp#more_examples)。）

## 表格

表格由 `<table>` 标签来定义。每个表格均有若干行（由 `<tr>` 标签定义），每行被分割为若干单元格（由 `<td>` 标签定义）。字母 td 指表格数据（table data），即数据单元格的内容。数据单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。

```html
<table border="1">
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td>row 2, cell 1</td>
<td>row 2, cell 2</td>
</tr>
</table>
```

在浏览器显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/A3ACkHF4IJ.png?imageslim)

## 表格和边框属性

如果不定义边框属性，表格将不显示边框。有时这很有用，但是大多数时候，我们希望显示边框。

使用边框属性来显示一个带有边框的表格：

```html
<table border="1">
<tr>
<td>Row 1, cell 1</td>
<td>Row 1, cell 2</td>
</tr>
</table>
```

## 表格的表头

表格的表头使用 `<th>` 标签进行定义。

大多数浏览器会把表头显示为粗体居中的文本：

```html
<table border="1">
<tr>
<th>Heading</th>
<th>Another Heading</th>
</tr>
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td>row 2, cell 1</td>
<td>row 2, cell 2</td>
</tr>
</table>
```

在浏览器显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/Dg1DjeK0cj.png?imageslim)

## 表格中的空单元格

在一些浏览器中，没有内容的表格单元显示得不太好。如果某个单元格是空的（没有内容），浏览器可能无法显示出这个单元格的边框。

```html
<table border="1">
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td></td>
<td>row 2, cell 2</td>
</tr>
</table>
```

浏览器可能会这样显示：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/2IDleB6F4B.png?imageslim)

注意：这个空的单元格的边框没有被显示出来。为了避免这种情况，在空单元格中添加一个空格占位符，就可以将边框显示出来。

```html
<table border="1">
<tr>
<td>row 1, cell 1</td>
<td>row 1, cell 2</td>
</tr>
<tr>
<td>&nbsp;</td>
<td>row 2, cell 2</td>
</tr>
</table>
```

在浏览器中显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/0D3lJd7KgG.png?imageslim)

## 更多实例
<span style="color:red;">都要总结一下</span>

- [没有边框的表格](http://www.w3school.com.cn/tiy/t.asp?f=html_tables2)
  本例演示一个没有边框的表格。
- [表格中的表头(Heading)](http://www.w3school.com.cn/tiy/t.asp?f=html_table_headers)
  本例演示如何显示表格表头。
- [空单元格](http://www.w3school.com.cn/tiy/t.asp?f=html_table_nbsp)
  本例展示如何使用 "&nbsp;" 处理没有内容的单元格。
- [带有标题的表格](http://www.w3school.com.cn/tiy/t.asp?f=html_tables3)
  本例演示一个带标题 (caption) 的表格
- [跨行或跨列的表格单元格](http://www.w3school.com.cn/tiy/t.asp?f=html_table_span)
  本例演示如何定义跨行或跨列的表格单元格。
- [表格内的标签](http://www.w3school.com.cn/tiy/t.asp?f=html_table_elements)
  本例演示如何显示在不同的元素内显示元素。
- [单元格边距(Cell padding)](http://www.w3school.com.cn/tiy/t.asp?f=html_table_cellpadding)
  本例演示如何使用 Cell padding 来创建单元格内容与其边框之间的空白。
- [单元格间距(Cell spacing)](http://www.w3school.com.cn/tiy/t.asp?f=html_table_cellspacing)
  本例演示如何使用 Cell spacing 增加单元格之间的距离。
- [向表格添加背景颜色或背景图像](http://www.w3school.com.cn/tiy/t.asp?f=html_table_background)
  本例演示如何向表格添加背景。
- [向表格单元添加背景颜色或者背景图像](http://www.w3school.com.cn/tiy/t.asp?f=html_table_cellbackground)
  本例演示如何向一个或者更多表格单元添加背景。
- [在表格单元中排列内容](http://www.w3school.com.cn/tiy/t.asp?f=html_table_align)
  本例演示如何使用 "align" 属性排列单元格内容,以便创建一个美观的表格。
- [框架(frame)属性](http://www.w3school.com.cn/tiy/t.asp?f=html_table_frame)
  本例演示如何使用 "frame" 属性来控制围绕表格的边框。

## 表格标签

| 表格                                               | 描述                   |
| -------------------------------------------------- | ---------------------- |
| [`<table>`](http://www.w3school.com.cn/tags/tag_table.asp)  | 定义表格               |
| [`<caption>`](http://www.w3school.com.cn/tags/tag_caption.asp) | 定义表格标题。         |
| [`<th>`](http://www.w3school.com.cn/tags/tag_th.asp)      | 定义表格的表头。       |
|  [`<tr>`](http://www.w3school.com.cn/tags/tag_tr.asp)     | 定义表格的行。         |
| [`<td>`](http://www.w3school.com.cn/tags/tag_td.asp)      | 定义表格单元。         |
|   [`<thead>`](http://www.w3school.com.cn/tags/tag_thead.asp)    | 定义表格的页眉。       |
|    [`<tbody>`](http://www.w3school.com.cn/tags/tag_tbody.asp)     | 定义表格的主体。       |
|    [`<tfoot>`](http://www.w3school.com.cn/tags/tag_tfoot.asp)      | 定义表格的页脚。       |
|     [`<col>`](http://www.w3school.com.cn/tags/tag_col.asp)    | 定义用于表格列的属性。 |
|   [`<colgroup>`](http://www.w3school.com.cn/tags/tag_colgroup.asp) | 定义表格列的组。       |

















# HTML 列表

**HTML 支持有序、无序和定义列表**

## 实例

- [无序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_list_unordered)
  本例演示无序列表。
- [有序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_list_ordered)
  本例演示有序列表。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_lists.asp#more_examples)。）

## 无序列表

无序列表是一个项目的列表，此列项目使用粗体圆点（典型的小黑圆圈）进行标记。

无序列表始于 `<ul>` 标签。每个列表项始于 `<li>`。

```html
<ul>
<li>Coffee</li>
<li>Milk</li>
</ul>
```

浏览器显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/hB1d9kIik0.png?imageslim)

列表项内部可以使用段落、换行符、图片、链接以及其他列表等等。

## 有序列表

同样，有序列表也是一列项目，列表项目使用数字进行标记。

有序列表始于 `<ol>` 标签。每个列表项始于 `<li>` 标签。

```html
<ol>
<li>Coffee</li>
<li>Milk</li>
</ol>
```

浏览器显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/83Jhe3BKLg.png?imageslim)

列表项内部可以使用段落、换行符、图片、链接以及其他列表等等。

## 定义列表

自定义列表不仅仅是一列项目，而是项目及其注释的组合。

自定义列表以 `<dl>` 标签开始。每个自定义列表项以 `<dt>` 开始。每个自定义列表项的定义以 `<dd>` 开始。

```html
<dl>
<dt>Coffee</dt>
<dd>Black hot drink</dd>
<dt>Milk</dt>
<dd>White cold drink</dd>
</dl>
```

浏览器显示如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/1H8Ehbg32I.png?imageslim)

定义列表的列表项内部可以使用段落、换行符、图片、链接以及其他列表等等。

## 更多实例

- [不同类型的无序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_unordered)
  本例演示一个无序列表。
- [不同类型的有序列表](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_ordered)
  本例演示不同类型的有序列表。
- [嵌套列表](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_nested)
  本例演示如何嵌套列表。
- [嵌套列表 2](http://www.w3school.com.cn/tiy/t.asp?f=html_lists_nested2)
  本例演示更复杂的嵌套列表。
- [定义列表](http://www.w3school.com.cn/tiy/t.asp?f=html_list_definition)
  本例演示一个定义列表。


## 列表标签

| 标签                                             | 描述                       |
| ------------------------------------------------ | -------------------------- |
| [`<ol>`](http://www.w3school.com.cn/tags/tag_ol.asp)   | 定义有序列表。             |
| [`<ul>`](http://www.w3school.com.cn/tags/tag_ul.asp)   | 定义无序列表。             |
| [`<li>`](http://www.w3school.com.cn/tags/tag_li.asp)   | 定义列表项。               |
| [`<dl>`](http://www.w3school.com.cn/tags/tag_dl.asp)   | 定义定义列表。             |
| [`<dt>`](http://www.w3school.com.cn/tags/tag_dt.asp)   | 定义定义项目。             |
| [`<dd>`](http://www.w3school.com.cn/tags/tag_dd.asp)   | 定义定义的描述。           |
| [`<dir>`](http://www.w3school.com.cn/tags/tag_dir.asp)  | 已废弃。使用 `<ul>` 代替它。 |
| [`<menu>`](http://www.w3school.com.cn/tags/tag_menu.asp) | 已废弃。使用 `<ul>` 代替它。 |
