# HTML `<div>` 和 `<span>`

**可以通过 `<div>` 和 `<span>` 将 HTML 元素组合起来。**

## HTML 块元素

大多数 HTML 元素被定义为块级元素或内联元素。

编者注：“块级元素”译为 block level element，“内联元素”译为 inline element。

块级元素在浏览器显示时，通常会以新行来开始（和结束）。

例子：`<h1>`, `<p>`, `<ul>`, `<table>`

## HTML 内联元素

内联元素在显示时通常不会以新行开始。

例子：`<b>`, `<td>`, `<a>`, `<img>`

## HTML `<div>` 元素

HTML `<div>` 元素是块级元素，它是可用于组合其他 HTML 元素的容器。

`<div>` 元素没有特定的含义。除此之外，由于它属于块级元素，浏览器会在其前后显示折行。

如果与 CSS 一同使用，`<div>` 元素可用于对大的内容块设置样式属性。<span style="color:red;">怎么使用？</span>

`<div>` 元素的另一个常见的用途是文档布局。它取代了使用表格定义布局的老式方法。使用 `<table>` 元素进行文档布局不是表格的正确用法。`<table>` 元素的作用是显示表格化的数据。<span style="color:red;">嗯</span>

## HTML `<span>` 元素

HTML `<span>` 元素是内联元素，可用作文本的容器。

`<span>` 元素也没有特定的含义。

当与 CSS 一同使用时，`<span>` 元素可用于为部分文本设置样式属性。<span style="color:red;">嗯</span>

## HTML 分组标签

| 标签                                             | 描述                                       |
| ------------------------------------------------ | ------------------------------------------ |
| [`<div>`](http://www.w3school.com.cn/tags/tag_div.asp)  | 定义文档中的分区或节（division/section）。 |
| [`<span>`](http://www.w3school.com.cn/tags/tag_span.asp) | 定义 span，用来组合文档中的行内元素。      |









# HTML 类


对 HTML 进行分类（设置类），使我们能够为元素的类定义 CSS 样式。

为相同的类设置相同的样式，或者为不同的类设置不同的样式。<span style="color:red;">嗯</span>

### 实例
<span style="color:red;">为什么是.cities ？不是 cities ？</span>
```html
<!DOCTYPE html>
<html>
<head>
<style>
.cities {
    background-color:black;
    color:white;
    margin:20px;
    padding:20px;
}
</style>
</head>

<body>

<div class="cities">
<h2>London</h2>
<p>
London is the capital city of England.
It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.
</p>
</div>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/A3mjehAf8b.png?imageslim)

## 分类块级元素

HTML `<div>` 元素是*块级元素*。它能够用作其他 HTML 元素的容器。

设置 `<div>` 元素的类，使我们能够为相同的 `<div>` 元素设置相同的类：

### 实例

```html
<!DOCTYPE html>
<html>
<head>
<style>
.cities {
    background-color:black;
    color:white;
    margin:20px;
    padding:20px;
}
</style>
</head>

<body>
<div class="cities">
<h2>London</h2>
<p>London is .</p>
</div>

<div class="cities">
<h2>Paris</h2>
<p>Paris is </p>
</div>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/ggBgmHaGBk.png?imageslim)

## 分类行内元素

HTML `<span>` 元素是行内元素，能够用作文本的容器。

设置 `<span>` 元素的类，能够为相同的 `<span>` 元素设置相同的样式。

### 实例
<span style="color:red;">为什么是用 span.red ？</span>
```html
<!DOCTYPE html>
<html>
<head>
<style>
span.red {
    color:red;
}
</style>
</head>

<body>

<h1>我的<span class="red">重要的</span>标题</h1>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/H424A4fGdH.png?imageslim)









# HTML 布局


网站常常以多列显示内容（就像杂志和报纸）。

## 使用 `<div>` 元素的 HTML 布局

注释：`<div>` 元素常用作布局工具，因为能够轻松地通过 CSS 对其进行定位。

这个例子使用了四个 `<div>` 元素来创建多列布局：

### 实例

```html
<body>

<div id="header">
<h1>City Gallery</h1>
</div>

<div id="nav">
London<br>
Paris<br>
Tokyo<br>
</div>

<div id="section">
<h1>London</h1>
<p>
London is the capital city of England. It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.
</p>
<p>
Standing on the River Thames, London has been a major settlement for two millennia,
its history going back to its founding by the Romans, who named it Londinium.
</p>
</div>

<div id="footer">
Copyright W3School.com.cn
</div>

</body>
```
<span style="color:red;">很 nice 啊</span>
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/3aAEfkeK6m.png?imageslim)

### 其中的 CSS 部分：

```html
<style>
#header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px;
}
#nav {
    line-height:30px;
    background-color:#eeeeee;
    height:300px;
    width:100px;
    float:left;
    padding:5px;
}
#section {
    width:350px;
    float:left;
    padding:10px;
}
#footer {
    background-color:black;
    color:white;
    clear:both;
    text-align:center;
    padding:5px;
}
</style>
```

## 使用 HTML5 的网站布局

HTML5 提供的新语义元素定义了网页的不同部分：

### HTML5 语义元素

| header  | 定义文档或节的页眉             |
| ------- | ------------------------------ |
| nav     | 定义导航链接的容器             |
| section | 定义文档中的节                 |
| article | 定义独立的自包含文章           |
| aside   | 定义内容之外的内容（比如侧栏） |
| footer  | 定义文档或节的页脚             |
| details | 定义额外的细节                 |
| summary | 定义 details 元素的标题        |
<span style="color:red;">这个还是不清楚指的到底是哪个地方。</span>

下面这个例子使用 `<header>`, `<nav>`, `<section>`, 以及 `<footer>` 来创建多列布局：

### 实例

```html
<body>

<header>
<h1>City Gallery</h1>
</header>

<nav>
London<br>
Paris<br>
Tokyo<br>
</nav>

<section>
<h1>London</h1>
<p>
London is the capital city of England. It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.
</p>
<p>
Standing on the River Thames, London has been a major settlement for two millennia,
its history going back to its founding by the Romans, who named it Londinium.
</p>
</section>

<footer>
Copyright W3School.com.cn
</footer>

</body>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/5mbJkji41K.png?imageslim)

### 对应的CSS如下：

```html
<style>
header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px;
}
nav {
    line-height:30px;
    background-color:#eeeeee;
    height:300px;
    width:100px;
    float:left;
    padding:5px;
}
section {
    width:350px;
    float:left;
    padding:10px;
}
footer {
    background-color:black;
    color:white;
    clear:both;
    text-align:center;
    padding:5px;
}
</style>
```

## 使用表格的 HTML 布局

注释：`<table>` 元素不是作为布局工具而设计的。

`<table>` 元素的作用是显示表格化的数据。

使用 `<table>` 元素能够取得布局效果，因为能够通过 CSS 设置表格元素的样式：

### 实例

```html
<!DOCTYPE html>
<html>
<head>
<style>
table.lamp {
    width:100%;
    border:1px solid #d4d4d4;
}
table.lamp th, td {
    padding:10px;
}
table.lamp th {
    width:40px;
}

</style>
</head>

<body>

<table class="lamp">
<tr>
  <th>
    <img src="http://www.ypppt.com/uploads/allimg/180303/1-1P3031301360-L.jpg" alt="Note" style="height:32px;width:32px">
  </th>
  <td>
    The table element was not designed to be a layout tool.
  </td>
</tr>
</table>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/f12bj72lIk.png?imageslim)





# HTML 响应式 Web 设计

## 什么是响应式 Web 设计？

- RWD 指的是响应式 Web 设计（Responsive Web Design）
- RWD 能够以可变尺寸传递网页 <span style="color:red;">什么意思？</span>
- RWD 对于平板和移动设备是必需的

## 创建您自己的响应式设计

创建响应式设计的一个方法，是自己来创建它：

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
<style>
.city {
float: left;
margin: 5px;
padding: 15px;
width: 300px;
height: 300px;
border: 1px solid black;
}
</style>
</head>

<body>

<h1>W3School Demo</h1>
<h2>Resize this responsive page!</h2>
<br>

<div class="city">
<h2>London</h2>
<p>London is the capital city of England.</p>
<p>It is the most populous city in the United Kingdom,
with a metropolitan area of over 13 million inhabitants.</p>
</div>

<div class="city">
<h2>Paris</h2>
<p>Paris is the capital and most populous city of France.</p>
</div>

<div class="city">
<h2>Tokyo</h2>
<p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
and the most populous metropolitan area in the world.</p>
</div>

</body>
</html>
```
当浏览器打开的比较大的时候：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/gl05C3LdEa.png?imageslim)

当浏览器窗口开的比较小的时候：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/BfJ0273cEJ.png?imageslim)


## 使用 Bootstrap

另一个创建响应式设计的方法，是使用现成的 CSS 框架。

Bootstrap 是最流行的开发响应式 web 的 HTML, CSS, 和 JS 框架。

Bootstrap 帮助您开发在任何尺寸都外观出众的站点：显示器、笔记本电脑、平板电脑或手机：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet"
  href="http://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
</head>

<body>

<div class="container">
<div class="jumbotron">
  <h1>W3School Demo</h1>
  <p>Resize this responsive page!</p>
</div>
</div>

<div class="container">
<div class="row">
  <div class="col-md-4">
    <h2>London</h2>
    <p>London is the capital city of England.</p>
    <p>It is the most populous city in the United Kingdom,
    with a metropolitan area of over 13 million inhabitants.</p>
  </div>
  <div class="col-md-4">
    <h2>Paris</h2>
    <p>Paris is the capital and most populous city of France.</p>
  </div>
  <div class="col-md-4">
    <h2>Tokyo</h2>
    <p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
    and the most populous metropolitan area in the world.</p>
  </div>
</div>
</div>

</body>
</html>
```
浏览器窗口比较大的时候：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/aj8mLIBLhl.png?imageslim)

浏览器窗口比较小的时候：<span style="color:red;">很 nice 啊</span>

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/CBm0ajFjK2.png?imageslim)

<span style="color:red;">嗯，非常不错，但是如果还想再这个基础上定制一些css 要怎么做？直接对这个 class 进行自定义吗？那么他的响应式的特性还会不会继承？</span>

如需学习更多有关 Bootstrap 的知识，请阅读我们的 Bootstrap 教程。<span style="color:red;">嗯，是要看下的，因为这个比较方便，可以很大程度上减少自定义的响应式的工作量。</span>




# HTML 框架

**通过使用框架，你可以在同一个浏览器窗口中显示不止一个页面。**<span style="color:red;">什么意思？</span>

## 实例

- [垂直框架](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_cols)
  本例演示：如何使用三份不同的文档制作一个垂直框架。
- [水平框架](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_rows)
  本例演示：如何使用三份不同的文档制作一个水平框架。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_frames.asp#more_examples)。）

## 框架

通过使用框架，你可以在同一个浏览器窗口中显示不止一个页面。每份 HTML 文档称为一个框架，并且每个框架都独立于其他的框架。

使用框架的坏处：

- 开发人员必须同时跟踪更多的HTML文档
- 很难打印整张页面
- 框架结构标签（<frameset>）
  框架结构标签（<frameset>）定义如何将窗口分割为框架每个 frameset 定义了一系列行 *或* 列 rows/columns 的值规定了每行或每列占据屏幕的面积

编者注：frameset 标签也被某些文章和书籍译为框架集。<span style="color:red;">嗯，一般什么时候使用 frameset 呢？布局不是可以直接通过div来限定吗？为什么要用这个frameset？有什么好处吗？感觉像 WPF 的 page。</span>

## 框架标签（Frame）

Frame 标签定义了放置在每个框架中的 HTML 文档。

在下面的这个例子中，我们设置了一个两列的框架集。第一列被设置为占据浏览器窗口的 25%。第二列被设置为占据浏览器窗口的 75%。HTML 文档 "frame_a.htm" 被置于第一个列中，而 HTML 文档 "frame_b.htm" 被置于第二个列中：<span style="color:red;">嗯，看到这个就感觉相当于把一个页面插入到这个页面中来。但是为什么不能用一些后端框架的 {% include %} ? 还是说这中间有什么区别？</span>

```html
<frameset cols="25%,75%">
   <frame src="frame_a.htm">
   <frame src="frame_b.htm">
</frameset>
```

## 基本的注意事项 - 有用的提示：

假如一个框架有可见边框，用户可以拖动边框来改变它的大小。为了避免这种情况发生，可以在 `<frame>` 标签中加入：noresize="noresize"。

为不支持框架的浏览器添加 `<noframes>` 标签。

重要提示：不能将 `<body></body>` 标签与 `<frameset></frameset>` 标签同时使用！不过，假如你添加包含一段文本的 `<noframes>` 标签，就必须将这段文字嵌套于 `<body></body>` 标签内。（在下面的第一个实例中，可以查看它是如何实现的。）

## 更多实例

- [如何使用  标签](http://www.w3school.com.cn/tiy/t.asp?f=html_noframes)
  本例演示：如何使用 `<noframes>` 标签。
- [混合框架结构](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_mix)
  本例演示如何制作含有三份文档的框架结构，同时将他们混合置于行和列之中。
- [含有 noresize="noresize" 属性的框架结构](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_noresize)
  本例演示 noresize 属性。在本例中，框架是不可调整尺寸的。在框架间的边框上拖动鼠标，你会发现边框是无法移动的。
- [导航框架](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_navigation)
  本例演示如何制作导航框架。导航框架包含一个将第二个框架作为目标的链接列表。名为 "contents.htm" 的文件包含三个链接。
- [内联框架](http://www.w3school.com.cn/tiy/t.asp?f=html_iframe)
  本例演示如何创建内联框架（HTML 页中的框架）。
- [跳转至框架内的一个指定的节](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_jump)
  本例演示两个框架。其中的一个框架设置了指向另一个文件内指定的节的链接。这个"link.htm"文件内指定的节使用 `<a name="C10">` 进行标识。
- [使用框架导航跳转至指定的节](http://www.w3school.com.cn/tiy/t.asp?f=html_frame_navigation2)
  本例演示两个框架。左侧的导航框架包含了一个链接列表，这些链接将第二个框架作为目标。第二个框架显示被链接的文档。导航框架其中的链接指向目标文件中指定的节。













# HTML Iframe

**iframe 用于在网页内显示网页。**

## 添加 iframe 的语法

```html
<iframe src="URL"></iframe>
```
*URL* 指向隔离页面的位置。

## Iframe - 设置高度和宽度

height 和 width 属性用于规定 iframe 的高度和宽度。

属性值的默认单位是像素，但也可以用百分比来设定（比如 "80%"）。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<iframe src="/example/html/demo_iframe.html" width="200" height="200"></iframe>

<p>某些老式的浏览器不支持内联框架。</p>
<p>如果不支持，则 iframe 是不可见的。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/d11fHhh78m.png?imageslim)

## Iframe - 删除边框

frameborder 属性规定是否显示 iframe 周围的边框。

设置属性值为 "0" 就可以移除边框：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<iframe src="/example/html/demo_iframe.html" frameborder="0"></iframe>

<p>某些老式的浏览器不支持内联框架。</p>
<p>如果不支持，则 iframe 是不可见的。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/a2aKjkEgIi.png?imageslim)

## 使用 iframe 作为链接的目标

iframe 可用作链接的目标（target）。

链接的 target 属性必须引用 iframe 的 name 属性：<span style="color:red;">target="_blank"的时候是在新窗口打开</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<iframe src="/example/html/demo_iframe.html" name="iframe_a"></iframe>

<p><a href="http://www.w3school.com.cn" target="iframe_a">W3School.com.cn</a></p>

<p><b>注释：</b>由于链接的目标匹配 iframe 的名称，所以链接会在 iframe 中打开。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/4G5H4HbJmB.png?imageslim)

点击 `W3School.com.cn` 的链接之后：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/H4IBcK2966.png?imageslim)



## HTML iframe 标签

| 标签  | 描述  |
| ------- | ----- |
| [`<iframe>`](http://www.w3school.com.cn/tags/tag_iframe.asp) | 定义内联的子窗口（框架） |




# HTML 背景

**好的背景使站点看上去特别棒。**

## 实例

- [搭配良好的背景和颜色](http://www.w3school.com.cn/tiy/t.asp?f=html_back_good)
  一个背景颜色和文字颜色搭配良好的例子，使页面中的文字易于阅读。
- [搭配得不好的背景和颜色](http://www.w3school.com.cn/tiy/t.asp?f=html_back_bad)
  一个背景颜色和文字颜色搭配得不好的例子，使页面中的文字难于阅读。

（[可以在本页底端找到更多实例](http://www.w3school.com.cn/html/html_backgrounds.asp#more_examples)。）

## 背景（Backgrounds）

`<body>` 拥有两个配置背景的标签。背景可以是颜色或者图像。

### 背景颜色（Bgcolor）

背景颜色属性将背景设置为某种颜色。属性值可以是十六进制数、RGB 值或颜色名。

```html
<body bgcolor="#000000">
<body bgcolor="rgb(0,0,0)">
<body bgcolor="black">
```

以上的代码均将背景颜色设置为黑色。

### 背景（Background）

背景属性将背景设置为图像。属性值为图像的URL。如果图像尺寸小于浏览器窗口，那么图像将在整个浏览器窗口进行复制。

```html
<body background="clouds.gif">
<body background="http://www.w3school.com.cn/clouds.gif">
```

URL可以是相对地址，如第一行代码。也可以使绝对地址，如第二行代码。

提示：如果你打算使用背景图片，你需要紧记一下几点：

- 背景图像是否增加了页面的加载时间。小贴士：图像文件不应超过 10k。
- 背景图像是否与页面中的其他图象搭配良好。
- 背景图像是否与页面中的文字颜色搭配良好。
- 图像在页面中平铺后，看上去还可以吗？
- 对文字的注意力被背景图像喧宾夺主了吗？

## 基本的注意事项 - 有用的提示：

`<body>` 标签中的背景颜色（bgcolor）、背景（background）和文本（text）属性在最新的 HTML 标准（HTML4 和 XHTML）中已被废弃。W3C 在他们的推荐标准中已删除这些属性。<span style="color:red;">那么使用什么呢？确认下</span>

应该使用层叠样式表（CSS）来定义 HTML 元素的布局和显示属性。

## 更多实例

- [可用性强的背景图像](http://www.w3school.com.cn/tiy/t.asp?f=html_back_img)
  背景图像和文字颜色使页面文本易于阅读的例子。
- [可用性强的背景图像 2](http://www.w3school.com.cn/tiy/t.asp?f=html_back_img2)
  另一个背景图像和文字颜色使页面文本易于阅读的例子。
- [可用性差的背景图像](http://www.w3school.com.cn/tiy/t.asp?f=html_back_imgbad)
  背景图像和文字颜色使页面文本不易阅读的例子。






# HTML 脚本


**JavaScript 使 HTML 页面具有更强的动态和交互性。。**

## 实例

- [插入一段脚本](http://www.w3school.com.cn/tiy/t.asp?f=html_script)
  如何将脚本插入 HTML 文档。
- [使用  标签](http://www.w3school.com.cn/tiy/t.asp?f=html_noscript)
  如何应对不支持脚本或禁用脚本的浏览器。

## HTML script 元素

`<script>` 标签用于定义客户端脚本，比如 JavaScript。

script 元素既可包含脚本语句，也可通过 src 属性指向外部脚本文件。

必需的 type 属性规定脚本的 MIME 类型。

JavaScript 最常用于图片操作、表单验证以及内容动态更新。

下面的脚本会向浏览器输出“Hello World!”：

```html
<script type="text/javascript">
document.write("Hello World!")
</script>
```

提示：如果需要学习更多有关在 HTML 中编写脚本的知识，请访问我们的 [JavaScript 教程](http://www.w3school.com.cn/js/index.asp)。<span style="color:red;">这个需要好好学习的</span>

## `<noscript>` 标签

`<noscript>` 标签提供无法使用脚本时的替代内容，比方在浏览器禁用脚本时，或浏览器不支持客户端脚本时。

noscript 元素可包含普通 HTML 页面的 body 元素中能够找到的所有元素。

只有在浏览器不支持脚本或者禁用脚本时，才会显示 noscript 元素中的内容：<span style="color:red;">还可以这样</span>

```html
<script type="text/javascript">
document.write("Hello World!")
</script>
<noscript>Your browser does not support JavaScript!</noscript>
```

## 如何应付老式的浏览器

如果浏览器压根没法识别 `<script>` 标签，那么 `<script>` 标签所包含的内容将以文本方式显示在页面上。为了避免这种情况发生，你应该将脚本隐藏在注释标签当中。那些老的浏览器（无法识别 `<script>` 标签的浏览器）将忽略这些注释，所以不会将标签的内容显示到页面上。而那些新的浏览器将读懂这些脚本并执行它们，即使代码被嵌套在注释标签内。<span style="color:red;">嗯，原来是这样，怪不得之前看到把 script 用注释包起来了。</span>

### 实例

#### JavaScript:

```html
<script type="text/javascript">
<!--
document.write("Hello World!")
//-->
</script>
```
<span style="color:red;">为什么这个地方有//？是为了对应老的浏览器的比较特殊的注释吗？</span>
#### VBScript:

```html
<script type="text/vbscript">
<!--
document.write("Hello World!")
-->
</script>
```
<span style="color:red;">这个注释的 --> 左边应该有个单引号的。是不是</span>

| 标签   | 描述     |
| ------------- | --------- |
| [`<script>`](http://www.w3school.com.cn/tags/tag_script.asp)   | 定义客户端脚本。                         |
| [`<noscript>`](http://www.w3school.com.cn/tags/tag_noscript.asp) | 为不支持客户端脚本的浏览器定义替代内容。 |







# HTML 头部元素


## 亲自试一试 - 实例

- [文档的标题](http://www.w3school.com.cn/tiy/t.asp?f=html_title)
  `<title>` 标题定义文档的标题。
- [所有链接一个目标](http://www.w3school.com.cn/tiy/t.asp?f=html_base)
  如何使用 base 标签使页面中的所有标签在新窗口中打开。
- [文档描述](http://www.w3school.com.cn/tiy/t.asp?f=html_meta)
  使用 `<meta>` 元素来描述文档。
- [文档关键词](http://www.w3school.com.cn/tiy/t.asp?f=html_keywords)
  使用 `<meta>` 元素来定义文档的关键词。
- [重定向用户](http://www.w3school.com.cn/tiy/t.asp?f=html_redirect)
  如何把用户重定向到新的网址。

## HTML `<head>` 元素

`<head>` 元素是所有头部元素的容器。`<head>` 内的元素可包含脚本，指示浏览器在何处可以找到样式表，提供元信息，等等。

以下标签都可以添加到 head 部分：`<title>`、`<base>`、`<link>`、`<meta>`、`<script>` 以及 `<style>`。

## HTML `<title>` 元素

`<title>` 标签定义文档的标题。

title 元素在所有 HTML/XHTML 文档中都是必需的。

title 元素能够：

- 定义浏览器工具栏中的标题
- 提供页面被添加到收藏夹时显示的标题
- 显示在搜索引擎结果中的页面标题

一个简化的 HTML 文档：

```html
<!DOCTYPE html>
<html>
<head>
<title>Title of the document</title>
</head>

<body>
The content of the document......
</body>

</html>
```

## HTML `<base>` 元素

`<base>` 标签为页面上的所有链接规定默认地址或默认目标（target）：

```html
<!DOCTYPE html>
<html>
<head>
<base href="http://www.w3school.com.cn/images/" />
<base target="_blank" />
</head>

<body>
<a href='/i/eg_bg_05.gif'>The content of the document......</a>
</body>

</html>
```
点击快捷方式：弹出一个网页，地址为：http://www.w3school.com.cn/i/eg_bg_05.gif，图像为：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/7IlFBlEh0H.png?imageslim)

## HTML `<link>` 元素

`<link>` 标签定义文档与外部资源之间的关系。

`<link>` 标签最常用于连接样式表：

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css" />
</head>
```

## HTML `<style>` 元素

`<style>` 标签用于为 HTML 文档定义样式信息。

您可以在 style 元素内规定 HTML 元素在浏览器中呈现的样式：

```html
<head>
<style type="text/css">
body {background-color:yellow}
p {color:blue}
</style>
</head>
```

## HTML `<meta>` 元素

元数据（metadata）是关于数据的信息。

`<meta>` 标签提供关于 HTML 文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。

典型的情况是，meta 元素被用于规定页面的描述、关键词、文档的作者、最后修改时间以及其他元数据。

`<meta>` 标签始终位于 head 元素中。

元数据可用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他 web 服务。

### 针对搜索引擎的关键词

一些搜索引擎会利用 meta 元素的 name 和 content 属性来索引您的页面。

下面的 meta 元素定义页面的描述：

```html
<meta name="description" content="Free Web tutorials on HTML, CSS, XML" />
```

下面的 meta 元素定义页面的关键词：

```html
<meta name="keywords" content="HTML, CSS, XML" />
```

name 和 content 属性的作用是描述页面的内容。

## HTML `<script>` 元素

`<script>` 标签用于定义客户端脚本，比如 JavaScript。

我们会在稍后的章节讲解 script 元素。

## HTML 头部元素

| 标签    | 描述      |
| --------- | ------- |
| [`<head>`](http://www.w3school.com.cn/tags/tag_head.asp)   | 定义关于文档的信息。                     |
| [`<title>`](http://www.w3school.com.cn/tags/tag_title.asp)  | 定义文档标题。                           |
| [`<base>`](http://www.w3school.com.cn/tags/tag_base.asp)   | 定义页面上所有链接的默认地址或默认目标。 |
| [`<link>`](http://www.w3school.com.cn/tags/tag_link.asp)   | 定义文档与外部资源之间的关系。           |
| [`<meta>`](http://www.w3school.com.cn/tags/tag_meta.asp)   | 定义关于 HTML 文档的元数据。             |
| [`<script>`](http://www.w3school.com.cn/tags/tag_script.asp) | 定义客户端脚本。                         |
| [`<style>`](http://www.w3school.com.cn/tags/tag_style.asp)  | 定义文档的样式信息。                     |




# HTML 字符实体

**HTML 中的预留字符必须被替换为字符实体。**

## HTML 实体

在 HTML 中，某些字符是预留的。

在 HTML 中不能使用小于号（<）和大于号（>），这是因为浏览器会误认为它们是标签。

如果希望正确地显示预留字符，我们必须在 HTML 源代码中使用字符实体（character entities）。

字符实体类似这样：

```text
&entity_name;
或者
&#entity_number;
```

如需显示小于号，我们必须这样写：`&lt;` 或 `&#60;` <span style="color:red;">嗯。</span>

提示：使用实体名而不是数字的好处是，名称易于记忆。不过坏处是，浏览器也许并不支持所有实体名称（对实体数字的支持却很好）。

## 不间断空格（non-breaking space）

HTML 中的常用字符实体是不间断空格( `&nbsp;` )。

浏览器总是会截短 HTML 页面中的空格。如果您在文本中写 10 个空格，在显示该页面之前，浏览器会删除它们中的 9 个。如需在页面中增加空格的数量，您需要使用 `&nbsp;` 字符实体。

## HTML 实例示例

```html
<!DOCTYPE html>
<html>
<body>

<h2>字符实体</h2>

<p>&#174;</p>
<p>&pound;</p>

</body>
</html>
```

输出：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/jcgi4C2I41.png?imageslim)

## HTML 中有用的字符实体

注释：实体名称对大小写敏感！

| 显示结果 | 描述   | 实体名称    | 实体编号 |
| ---- | -------- | -------- | -------- |
|          | 空格              | `&nbsp;`            | `&#160;`   |
| <        | 小于号            | `&lt;`             | `&#60;`    |
| >        | 大于号            | `&gt;`              | `&#62;`    |
| &        | 和号              | `&amp;`             | `&#38;`    |
| "        | 引号              | `&quot;`            | `&#34;`    |
| '        | 撇号              | `&apos;` (IE不支持) | `&#39;`    |
| ￠       | 分（cent）        | `&cent;`            | `&#162;`   |
| £        | 镑（pound）       | `&pound;`           | `&#163;`   |
| ¥        | 元（yen）         | `&yen;`             | `&#165;`   |
| €        | 欧元（euro）      | `&euro;`            | `&#8364;`  |
| §        | 小节              | `&sect;`            | `&#167;`   |
| ©        | 版权（copyright） | `&copy;`            | `&#169;`   |
| ®        | 注册商标          | `&reg;`             | `&#174;`   |
| ™        | 商标              | `&trade;`           | `&#8482;`  |
| ×        | 乘号              | `&times;`           | `&#215;`   |
| ÷        | 除号              | `&divide;`          | `&#247;`   |

如需完整的实体符号参考，请访问我们的 [HTML 实体符号参考手册](http://www.w3school.com.cn/tags/html_ref_entities.html)。








# HTML 统一资源定位器


URL 也被称为网址。

URL 可以由单词组成，比如 “w3school.com.cn”，或者是因特网协议（IP）地址：192.168.1.253。大多数人在网上冲浪时，会键入网址的域名，因为名称比数字容易记忆。

## URL - Uniform Resource Locator

当您点击 HTML 页面中的某个链接时，对应的 <a> 标签指向万维网上的一个地址。

统一资源定位器（URL）用于定位万维网上的文档（或其他数据）。

网址，比如 <http://www.w3school.com.cn/html/index.asp>，遵守以下的语法规则：

```text
scheme://host.domain:port/path/filename
```

解释：

- scheme - 定义因特网服务的类型。最常见的类型是 http <span style="color:red;">还有什么别的类型吗？</span>
- host - 定义域主机（http 的默认主机是 www）<span style="color:red;">http 的默认主机是 www 是什么意思？</span>
- domain - 定义因特网域名，比如 w3school.com.cn
- :port - 定义主机上的端口号（http 的默认端口号是 80）<span style="color:red;">这个默认端口号是什么意思？一直不是很清楚，https 是什么？</span>
- path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
- filename - 定义文档/资源的名称

编者注：URL 的英文全称是 Uniform Resource Locator，中文也译为“统一资源定位符”。<span style="color:red;">嗯</span>

## URL Schemes

以下是其中一些最流行的 scheme：<span style="color:red;"> https 的端口是什么？怎么从http 转化成 https ？</span>

| Scheme | 访问   | 用于...       |
| ------ | ------ | ------- |
| http   | 超文本传输协议     | 以 http:// 开头的普通网页。不加密。 |
| https  | 安全超文本传输协议 | 安全网页。加密所有信息交换。     |
| ftp    | 文件传输协议  | 用于将文件下载或上传至网站。        |
| file   |     | 您计算机上的文件。   |






# HTML URL 字符编码

**URL 编码会将字符转换为可通过因特网传输的格式。**

## URL - 统一资源定位器

Web 浏览器通过 URL 从 web 服务器请求页面。

URL 是网页的地址，比如 *http://www.w3school.com.cn*。

## URL 编码

URL 只能使用 [ASCII 字符集](http://www.w3school.com.cn/tags/html_ref_ascii.asp)来通过因特网进行发送。

由于 URL 常常会包含 ASCII 集合之外的字符，URL 必须转换为有效的 ASCII 格式。<span style="color:red;">嗯</span>

URL 编码使用 "%" 其后跟随两位的十六进制数来替换非 ASCII 字符。

URL 不能包含空格。URL 编码通常使用 + 来替换空格。


## URL 编码示例

| 字符 | URL 编码 |
| ---- | -------- |
| €    | %80      |
| £    | %A3      |
| ©    | %A9      |
| ®    | %AE      |
| À    | %C0      |
| Á    | %C1      |
| Â    | %C2      |
| Ã    | %C3      |
| Ä    | %C4      |
| Å    | %C5      |

如需完整的 URL 编码参考，请访问我们的 [URL 编码参考手册](http://www.w3school.com.cn/tags/html_ref_urlencode.html)。






# HTML Web Server

**如果希望向世界发布您的网站，那么您必须把它存放在 web 服务器上。**<span style="color:red;">一直不清楚，什么是web服务器？</span>

## 托管自己的网站

在自己的服务器上托管网站始终是一个选项。有几点需要考虑：

### 硬件支出

如果要运行“真正”的网站，您不得不购买强大的服务器硬件。不要指望低价的 PC 能够应付这些工作。您还需要稳定的（一天 24 小时）高速连接。

### 软件支出

请记住，服务器授权通常比客户端授权更昂贵。同时请注意，服务器授权也许有用户数量限制。

### 人工费

不要指望低廉的人工费用。您必须安装自己的硬件和软件。您同时要处理漏洞和病毒，以确保您的服务器时刻正常地运行于一个“任何事都可能发生”的环境中。

## 使用因特网服务提供商（ISP）

从 ISP 租用服务器也很常见。

大多数小公司会把网站存放到由 ISP 提供的服务器上。其优势有以下几点：

### 连接速度

大多数 ISP 都拥有连接因特网的高速连接。

### 强大的硬件

ISP 的 web 服务器通常强大到能够由若干网站分享资源。您还要看一下 ISP 是否提供高效的负载平衡，以及必要的备份服务器。<span style="color:red;">什么是负载均衡？怎么实现的？用什么工具？什么情况下使用？什么是备份服务器？</span>

### 安全性和可靠性

ISP 是网站托管方面的专家。他们应该提供 99% 以上的在线时间，最新的软件补丁，以及最好的病毒防护。

## 选择 ISP 时的注意事项

### 24 小时支持

确保 ISP 提供 24 小时支持。不要使自己置于无法解决严重问题的尴尬境地，同时还必须等待第二个工作日。如果您不希望支付长途电话费，那么免费电话服务也是必要的。

### 每日备份

确保 ISP 会执行每日备份的例行工作，否则您有可能损失有价值的数据。<span style="color:red;">ISP 会执行每日的备份吗？备份的是什么？</span>

### 流量

研究一下 ISP 的流量限制。如果出现由于网站受欢迎而激增的不可预期的访问量，那么您要确保不会因此支付额外费用。

### 带宽或内容限制

研究一下 ISP 的带宽和内容限制。如果您计划发布图片或播出视频或音频，请确保您有此权限。

### E-mail 功能

请确保 ISP 支持您需要的 e-mail 功能。<span style="color:red;">一直不知道e-mail 功能是怎么支持的？</span>

### 数据库访问

如果您计划使用网站数据库中的数据，那么请确保您的 ISP 支持您需要的数据库访问。

在您选取一家 ISP 之前，请务必阅读 W3School 的 [Web 主机教程](http://www.w3school.com.cn/hosting/index.asp)。<span style="color:red;">嗯，这个也需要总结下。</span>



# HTML 颜色

**颜色由红色、绿色、蓝色混合而成。**

## 颜色值

颜色由一个十六进制符号来定义，这个符号由红色、绿色和蓝色的值组成（RGB）。

每种颜色的最小值是0（十六进制：#00）。最大值是255（十六进制：#FF）。

这个表格给出了由三种颜色混合而成的具体效果：

| Color | Color HEX | Color RGB        |
| ----- | --------- | ---------------- |
|       | #000000   | rgb(0,0,0)       |
|       | #FF0000   | rgb(255,0,0)     |
|       | #00FF00   | rgb(0,255,0)     |
|       | #0000FF   | rgb(0,0,255)     |
|       | #FFFF00   | rgb(255,255,0)   |
|       | #00FFFF   | rgb(0,255,255)   |
|       | #FF00FF   | rgb(255,0,255)   |
|       | #C0C0C0   | rgb(192,192,192) |
|       | #FFFFFF   | rgb(255,255,255) |

## 颜色名

大多数的浏览器都支持颜色名集合。

提示：仅仅有 16 种颜色名被 W3C 的 HTML4.0 标准所支持。它们是：aqua, black, blue, fuchsia, gray, green, lime, maroon, navy, olive, purple, red, silver, teal, white, yellow。<span style="color:red;">嗯</span>

如果需要使用其它的颜色，需要使用十六进制的颜色值。

| Color | Color HEX | Color Name   |
| ----- | --------- | ------------ |
|       | #F0F8FF   | AliceBlue    |
|       | #FAEBD7   | AntiqueWhite |
|       | #7FFFD4   | Aquamarine   |
|       | #000000   | Black        |
|       | #0000FF   | Blue         |
|       | #8A2BE2   | BlueViolet   |
|       | #A52A2A   | Brown        |

## Web安全色

数年以前，当大多数计算机仅支持 256 种颜色的时候，一系列 216 种 Web 安全色作为 Web 标准被建议使用。其中的原因是，微软和 Mac 操作系统使用了 40 种不同的保留的固定系统颜色（双方大约各使用 20 种）。

我们不确定如今这么做的意义有多大，因为越来越多的计算机有能力处理数百万种颜色，不过做选择还是你自己。

### 216 跨平台色

最初，216 跨平台 web 安全色被用来确保：当计算机使用 256 色调色板时，所有的计算机能够正确地显示所有的颜色。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180624/c6JG0iCG6m.png?imageslim)




# HTML <!DOCTYPE>

**<!DOCTYPE> 声明帮助浏览器正确地显示网页。**

## <!DOCTYPE> 声明

Web 世界中存在许多不同的文档。只有了解文档的类型，浏览器才能正确地显示文档。

HTML 也有多个不同的版本，只有完全明白页面中使用的确切 HTML 版本，浏览器才能完全正确地显示出 HTML 页面。这就是 <!DOCTYPE> 的用处。<span style="color:red;">嗯</span>

<!DOCTYPE> 不是 HTML 标签。它为浏览器提供一项信息（声明），即 HTML 是用什么版本编写的。

提示：W3School 即将升级为最新的 HTML5 文档类型。

## 实例

带有 HTML5 DOCTYPE 的 HTML 文档：

```html
<!DOCTYPE html>
<html>
<head>
<title>Title of the document</title>
</head>

<body>
The content of the document......
</body>

</html>
```

## HTML 版本

从 Web 诞生早期至今，已经发展出多个 HTML 版本：

| 版本      | 年份 |
| --------- | ---- |
| HTML      | 1991 |
| HTML+     | 1993 |
| HTML 2.0  | 1995 |
| HTML 3.2  | 1997 |
| HTML 4.01 | 1999 |
| XHTML 1.0 | 2000 |
| HTML5     | 2012 |
| XHTML5    | 2013 |

## 常用的声明
<span style="color:red;">除了 html5 之外，别的还会用到吗？</span>

### HTML5

```html
<!DOCTYPE html>
```

### HTML 4.01

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

### XHTML 1.0

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

如需完整的文档类型声明列表，请访问我们的 [DOCTYPE 参考手册](http://www.w3school.com.cn/tags/tag_doctype.asp)。








# HTML 4.01 快速参考


**来自 W3School 的 HTML 快速参考。可以打印它，以备日常使用。**

## HTML Basic Document

```html
<html>
<head>
<title>Document name goes here</title>
</head>
<body>
Visible text goes here
</body>
</html>
```

## Text Elements

```html
<p>This is a paragraph</p>
<br> (line break)
<hr> (horizontal rule)
<pre>This text is preformatted</pre>
```

## Logical Styles

```html
<em>This text is emphasized</em>
<strong>This text is strong</strong>
<code>This is some computer code</code>
```

## Physical Styles

```html
<b>This text is bold</b>
<i>This text is italic</i>
```

## Links, Anchors, and Image Elements

```html
<a href="http://www.example.com/">This is a Link</a>
<a href="http://www.example.com/"><img src="URL"
alt="Alternate Text"></a>
<a href="mailto:webmaster@example.com">Send e-mail</a>A named anchor:
<a name="tips">Useful Tips Section</a>
<a href="#tips">Jump to the Useful Tips Section</a>
```

## Unordered list

```html
<ul>
<li>First item</li>
<li>Next item</li>
</ul>
```

## Ordered list

```html
<ol>
<li>First item</li>
<li>Next item</li>
</ol>
```

## Definition list

```html
<dl>
<dt>First term</dt>
<dd>Definition</dd>
<dt>Next term</dt>
<dd>Definition</dd>
</dl>
```

## Tables

```html
<table border="1">
<tr>
  <th>someheader</th>
  <th>someheader</th>
</tr>
<tr>
  <td>sometext</td>
  <td>sometext</td>
</tr>
</table>
```

## Frames

```html
<frameset cols="25%,75%">
  <frame src="page1.htm">
  <frame src="page2.htm">
</frameset>
```

## Forms
<span style="color:red;">这个submit 和 reset 为什么是 input html中没有 button 吗？</span>
```html
<form action="http://www.example.com/test.asp" method="post/get">
<input type="text" name="lastname"
value="Nixon" size="30" maxlength="50">
<input type="password">
<input type="checkbox" checked="checked">
<input type="radio" checked="checked">
<input type="submit">
<input type="reset">
<input type="hidden">

<select>
<option>Apples
<option selected>Bananas
<option>Cherries
</select>

<textarea name="Comment" rows="60"
cols="20"></textarea>
</form>
```

## Entities

```html
&lt; is the same as <
&gt; is the same as >
&#169; is the same as ©
```

## Other Elements

```html
<!-- This is a comment -->
<blockquote>
Text quoted from some source.
</blockquote>
<address>
Address 1<br>
Address 2<br>
City<br>
</address>
```

**Source : http://www.w3school.com.cn/html/html_quick.asp**
