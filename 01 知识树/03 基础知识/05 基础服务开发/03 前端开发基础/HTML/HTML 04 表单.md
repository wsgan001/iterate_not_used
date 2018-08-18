# HTML 表单


**HTML 表单用于搜集不同类型的用户输入。**

## `<form>` 元素

HTML 表单用于收集用户输入。

`<form>` 元素定义 HTML 表单：

### 实例

```html
<form>
 .
form elements
 .
</form>
```

## HTML 表单包含*表单元素*。

表单元素指的是不同类型的 input 元素、复选框、单选按钮、提交按钮等等。

## `<input>` 元素

*`<input>`* 元素是最重要的*表单元素*。

`<input>` 元素有很多形态，根据不同的 *type* 属性。

这是本章中使用的类型：

| 类型   | 描述                                 |
| ------ | ------------------------------------ |
| text   | 定义常规文本输入。                   |
| radio  | 定义单选按钮输入（选择多个选择之一） |
| submit | 定义提交按钮（提交表单）             |

注释：您稍后将在本教程学到更多有关输入类型的知识。

## 文本输入

*`<input type="text">`* 定义用于*文本输入*的单行输入字段：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form>
First name:<br>
<input type="text" name="firstname">
<br>
Last name:<br>
<input type="text" name="lastname">
</form>

<p>请注意表单本身是不可见的。</p>
<p>同时请注意文本字段的默认宽度是 20 个字符。</p>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/hcCJHbALJI.png?imageslim)

注释：表单本身并不可见。还要注意文本字段的默认宽度是 20 个字符。<span style="color:red;">文本字段的宽度怎么修改？</span>

## 单选按钮输入

*`<input type="radio">`* 定义*单选按钮*。

单选按钮允许用户在有限数量的选项中选择其中之一：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form>
<input type="radio" name="sex" value="male" checked>Male
<br>
<input type="radio" name="sex" value="female">Female
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/K7d9fAAGli.png?imageslim)

## 提交按钮

*`<input type="submit">`* 定义用于向*表单处理程序*（form-handler）*提交*表单的按钮。<span style="color:red;">难道只有这一个按钮吗？</span>

表单处理程序通常是包含用来处理输入数据的脚本的服务器页面。<span style="color:red;">这个处理程序是放在那里的？</span>

表单处理程序在表单的 *action* 属性中指定：<span style="color:red;">嗯</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" name="firstname" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit" value="Submit">
</form>

<p>如果您点击提交，表单数据会被发送到名为 demo_form.asp 的页面。</p>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/1CaFb4gidE.png?imageslim)

点击之后：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/4jC06gmdIf.png?imageslim)
<span style="color:red;">上面这个例子是设置到一个asp 页面里面，那么怎么设置成一个 js 函数？</span>

## Action 属性

*action 属性*定义在提交表单时执行的动作。

向服务器提交表单的通常做法是使用提交按钮。

通常，表单会被提交到 web 服务器上的网页。

在上面的例子中，指定了某个服务器脚本来处理被提交表单：

```html
<form action="action_page.php">
```

如果省略 action 属性，则 action 会被设置为当前页面。<span style="color:red;">什么意思？没明白？设置为当前页面会怎样？</span>

## Method 属性

*method 属性*规定在提交表单时所用的 HTTP 方法（*GET* 或 *POST*）：

```html
<form action="action_page.php" method="GET">
```

或：

```html
<form action="action_page.php" method="POST">
```
## 何时使用 GET？

您能够使用 GET（默认方法）：

如果表单提交是被动的（比如搜索引擎查询），并且没有敏感信息。

当您使用 GET 时，表单数据在页面地址栏中是可见的：

```
action_page.php?firstname=Mickey&lastname=Mouse
```

注释：GET 最适合少量数据的提交。浏览器会设定容量限制。

## 何时使用 POST？

您应该使用 POST：

如果表单正在更新数据，或者包含敏感信息（例如密码）。<span style="color:red;">嗯</span>

POST 的安全性更加，因为在页面地址栏中被提交的数据是不可见的。

## Name 属性

如果要正确地被提交，每个输入字段必须设置一个 name 属性。

本例只会提交 "Last name" 输入字段：<span style="color:red;">嗯，这个要注意</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit" value="Submit">
</form>

<p>如果您点击提交，表单数据会被发送到名为 demo_form.asp 的页面。</p>

<p>first name 不会被提交，因为此 input 元素没有 name 属性。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/hkDIgD0hLD.png?imageslim)

点击提交之后：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/L0Ea83lgc2.png?imageslim)



## 用 `<fieldset>` 组合表单数据

*`<fieldset>`* 元素组合表单中的相关数据

*`<legend>`* 元素为 `<fieldset>` 元素定义标题。<span style="color:red;">这个fieldset 是放在 form里面的，是这样吗？fieldset 感觉与 WPF 的group 很像。</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
<fieldset>
<legend>Personal information:</legend>
First name:<br>
<input type="text" name="firstname" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit" value="Submit">
</fieldset>
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/63mjeHE49D.png?imageslim)

点击 submit 之后：
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/IAi7GeglJk.png?imageslim)

## HTML Form 属性

HTML `<form>` 元素，设置所有可能的属性，是这样的：

### 实例

```html
<form action="action_page.php" method="GET" target="_blank" accept-charset="UTF-8"
ectype="application/x-www-form-urlencoded" autocomplete="off" novalidate>
.
form elements
 .
</form>
```

下面是 <form> 属性的列表：

| 属性   | 描述            |
| -------- | --------------- |
| accept-charset | 规定在被提交表单中使用的字符集（默认：页面字符集）。       |
| action         | 规定向何处提交表单的地址（URL）（提交页面）。              |
| autocomplete   | 规定浏览器应该自动完成表单（默认：开启）。                 |
| enctype        | 规定被提交数据的编码（默认：url-encoded）。                |
| method         | 规定在提交表单时所用的 HTTP 方法（默认：GET）。            |
| name           | 规定识别表单的名称（对于 DOM 使用：document.forms.name）。 |
| novalidate     | 规定浏览器不验证表单。                                     |
| target         | 规定 action 属性中地址的目标（默认：_self）。              |

<span style="color:red;">这个target 有哪几种？_blank? _self?</span>
注释：您将在下面的章节学到更多关于属性的知识。






# HTML 表单元素

**本章描述所有 HTML 表单元素。**

## `<input>` 元素

最重要的表单元素是 *`<input>`* 元素。

`<input>` 元素根据不同的 *type* 属性，可以变化为多种形态。

注释：下一章讲解所有 HTML 输入类型。

## `<select>` 元素（下拉列表）

*`<select>`* 元素定义*下拉列表*：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
<select name="cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat">Fiat</option>
<option value="audi">Audi</option>
</select>
<br><br>
<input type="submit">
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/c2G9HB9hLf.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/96AElikAm8.png?imageslim)


*`<option>`* 元素定义待选择的选项。

列表通常会把首个选项显示为被选选项。

您能够通过添加 selected 属性来定义预定义选项。

### 实例

```html
<option value="fiat" selected>Fiat</option>
```


## `<textarea>` 元素

*`<textarea>`* 元素定义多行输入字段（*文本域*）：

### 实例

```html
<html>
<body>
<textarea name="message" rows="10" cols="30">
The cat was playing in the garden.
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
</textarea>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/bdk1LmfE61.png?imageslim)


## `<button>` 元素

*`<button>`* 元素定义可点击的*按钮*：

### 实例

```html
<!DOCTYPE html>
<html>
<body>
<button type="button" onclick="alert('Hello World!')">点击我！</button>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/GD2fGKkddF.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/g8BE8BIc7D.png?imageslim)

<span style="color:red;">button 与 input 的 submmit 有什么区别？什么情况下用哪个？</span>


## HTML5 表单元素

HTML5 增加了如下表单元素：<span style="color:red;">还有什么别的新元素吗？keygen 和 output 没有讲。</span>

- `<datalist>`
- `<keygen>`
- `<output>`

注释：默认地，浏览器不会显示未知元素。新元素不会破坏您的页面。

## HTML5 `<datalist>` 元素

*`<datalist>`* 元素为 `<input>` 元素规定预定义选项列表。

用户会在他们输入数据时看到预定义选项的下拉列表。

`<input>` 元素的 *list* 属性必须引用 `<datalist>` 元素的 *id* 属性。

### 实例

通过 `<datalist>` 设置预定义值的 `<input>` 元素：<span style="color:red;">感觉这个与 select 比较像。</span>

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">

<input list="browsers" name="browser">
<datalist id="browsers">
  <option value="Internet Explorer">
  <option value="Firefox">
  <option value="Chrome">
  <option value="Opera">
  <option value="Safari">
</datalist>
<input type="submit">
</form>

<p><b>注释：</b>Safari 或 IE9（以及更早的版本）不支持 datalist 标签。</p>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/mFJGGcICCD.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/k9F33JC2B5.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/liAmfCCddJ.png?imageslim)









# HTML 输入类型

**本章描述 `<input>` 元素的输入类型。**

## 输入类型：text

*`<input type="text">`* 定义供*文本输入*的单行输入字段：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" name="firstname">
<br>
Last name:<br>
<input type="text" name="lastname">
<br><br>
<input type="submit">
</form>

<p>请注意表单本身是不可见的。</p>
<p>同时请注意文本字段的默认宽度是 20 个字符。</p>
</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/Jh2H6m0I3D.png?imageslim)

## 输入类型：password

*`<input type="password">`* 定义*密码字段*：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="">
User name:<br>
<input type="text" name="userid">
<br>
User password:<br>
<input type="password" name="psw">
</form>

<p>密码字段中的字符被掩码（显示为星号或圆点）。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/A5kfibjm5j.png?imageslim)

注释：password 字段中的字符会被做掩码处理（显示为星号或实心圆）。

## 输入类型：submit

*`<input type="submit">`* 定义*提交*表单数据至*表单处理程序*的按钮。

表单处理程序（form-handler）通常是包含处理输入数据的脚本的服务器页面。

在表单的 action 属性中规定表单处理程序（form-handler）：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" name="firstname" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit" value="Submit">
</form>

<p>如果您点击提交，表单数据会被发送到名为 demo_form.asp 的页面。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/emJK55k70C.png?imageslim)

如果您省略了提交按钮的 value 属性，那么该按钮将获得默认文本：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
First name:<br>
<input type="text" name="firstname" value="Mickey">
<br>
Last name:<br>
<input type="text" name="lastname" value="Mouse">
<br><br>
<input type="submit">
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/iCdCD0k7Lh.png?imageslim)

<span style="color:red;">默认文本是怎么得出的？</span>

## Input Type: radio

`<input type="radio">` 定义单选按钮。

Radio buttons let a user select ONLY ONE of a limited number of choices:
<span style="color:blue;">用到是同一个 name，但是value 不同，想知道它提交到服务器的是什么？是 value 里面的 string 吗？是的</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
<input type="radio" name="sex" value="male" checked>Male
<br>
<input type="radio" name="sex" value="female">Female
<br><br>
<input type="submit">
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/8Eiai05igB.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/Bbh7Ha1EAH.png?imageslim)


## Input Type: checkbox

`<input type="checkbox">` 定义复选框。

复选框允许用户在有限数量的选项中选择零个或多个选项。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
<input type="checkbox" name="vehicle" value="Bike">I have a bike
<br>
<input type="checkbox" name="vehicle" value="Car">I have a car
<br><br>
<input type="submit">
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/ka4BBcED2b.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/4Gm8f1Lj1K.png?imageslim)


## Input Type: button

*`<input type="button>`* 定义*按钮*。

### 实例

```
<!DOCTYPE html>
<html>
<body>

<button type="button" onclick="alert('Hello World!')">Click Me!</button>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/9BkFaAD58g.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/3hG6aiGakH.png?imageslim)



## HTML5 输入类型

HTML5 增加了多个新的输入类型：

- color
- date
- datetime
- datetime-local
- email
- month
- number
- range
- search
- tel
- time
- url
- week

注释：老式 web 浏览器不支持的输入类型，会被视为输入类型 text。

## 输入类型：number

*`<input type="number">`* 用于应该包含数字值的输入字段。

您能够对数字做出限制。

根据浏览器支持，限制可应用到输入字段。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
数值约束会应用到输入字段中。
</p>

<form action="/demo/demo_form.asp">
  数量（1 到 5 之间）：
  <input type="number" name="quantity" min="1" max="5">
  <input type="submit">
</form>

<p><b>注释：</b>IE9 及早期版本不支持 type="number"。</p>

</body>
</html>
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/7D6kI8KiK0.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/gf72jFC3aH.png?imageslim)

## 输入限制

这里列出了一些常用的输入限制（其中一些是 HTML5 中新增的）：

| 属性      | 描述                               |
| --------- | ---------------------------------- |
| disabled  | 规定输入字段应该被禁用。           |
| max       | 规定输入字段的最大值。             |
| maxlength | 规定输入字段的最大字符数。         |
| min       | 规定输入字段的最小值。             |
| pattern   | 规定通过其检查输入值的正则表达式。 |
| readonly  | 规定输入字段为只读（无法修改）。   |
| required  | 规定输入字段是必需的（必需填写）。 |
| size      | 规定输入字段的宽度（以字符计）。   |
| step      | 规定输入字段的合法数字间隔。       |
| value     | 规定输入字段的默认值。             |

您将在下一章学到更多有关输入限制的知识。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
Depending on browser support:<br>
Fixed steps will apply in the input field.
</p>

<form action="action_page.php">
  Quantity:
  <input type="number" name="points"
   min="0" max="100" step="10" value="30">
  <input type="submit">
</form>

<p><b>Note:</b>type="number" is not supported in IE9 and earlier.
</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/1b3LHBl38b.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/GaEFcjEHLl.png?imageslim)

<span style="color:red;">这种提示还是不错的。</span>

## 输入类型：date

*`<input type="date">`* 用于应该包含日期的输入字段。

根据浏览器支持，日期选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="/demo/demo_form.asp">
  生日：
  <input type="date" name="bday">
  <input type="submit">
</form>

<p><b>注释：</b>Firefox 或者
Internet Explorer 11 以及更早版本不支持 type="date"。</p>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/dEGgeehka2.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/d3CCJ7GHcF.png?imageslim)


您可以向输入添加限制：

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
请输入 1980-01-01 之前的日期：<br>
<input type="date" name="bday" max="1979-12-31"><br><br>
请输入 2000-01-01 之后的日期：<br>
<input type="date" name="bday" min="2000-01-02"><br><br>
<input type="submit">
</form>

<p><b>注释：</b>
Internet Explorer 不支持 type="date"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/1IkfiBI3jE.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/B01FibDKma.png?imageslim)



## 输入类型：color

*`<input type="color">`* 用于应该包含颜色的输入字段。

根据浏览器支持，颜色选择器会出现输入字段中。<span style="color:red;">很少看到有用到 color 的。</span>

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
Depending on browser support:<br>
A color picker can pop-up when you enter the input field.
</p>

<form action="action_page.php">
  Select your favorite color:
  <input type="color" name="favcolor" value="#ff0000">
  <input type="submit">
</form>

<p><b>Note:</b> type="color" is not supported in Internet Explorer.</p>

</body>
</html>

```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/jCaD2kJbfA.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/kKFckkahEA.png?imageslim)

## 输入类型：range

*`<input type="range">`* 用于应该包含一定范围内的值的输入字段。

根据浏览器支持，输入字段能够显示为滑块控件。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
输入类型 "range" 可显示为滑动控件。
</p>

<form action="/demo/demo_form.asp" method="get">
  Points:
  <input type="range" name="points" min="0" max="10">
  <input type="submit">
</form>

<p><b>注释：</b>IE9 及早期版本不支持 type="range"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/H1Hcam9KmI.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/bGFIih5kfa.png?imageslim)

您能够使用如下属性来规定限制：min、max、step、value。

## 输入类型：month

*`<input type="month">`* 允许用户选择月份和年份。

根据浏览器支持，日期选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="/demo/demo_form.asp">
  生日（月和年）：
  <input type="month" name="bdaymonth">
  <input type="submit">
</form>

<p><b>注释：</b>Firefox 或者
Internet Explorer 11 以及更早版本不支持 type="month"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/mKbAC9mk40.png?imageslim)

## 输入类型：week

*`<input type="week">`* 允许用户选择周和年。

根据浏览器支持，日期选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="action_page.php">
  Select a week:
  <input type="week" name="year_week">
  <input type="submit">
</form>

<p><b>注释：</b>
Internet Explorer 不支持 type="week"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/132AK6721J.png?imageslim)

## 输入类型：time

*`<input type="time">`* 允许用户选择时间（无时区）。

根据浏览器支持，时间选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="/demo/demo_form.asp">
  请选取一个时间：
  <input type="time" name="usr_time">
  <input type="submit">
</form>

<p><b>注释：</b>Firefox 或者
Internet Explorer 11 以及更早版本不支持 type="time"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/ikIFdfAeGJ.png?imageslim)

## 输入类型：datetime

*`<input type="datetime">`* 允许用户选择日期和时间（有时区）。

根据浏览器支持，日期选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="action_page.php">
  生日（日期和时间）：
  <input type="datetime" name="bdaytime">
  <input type="submit">
</form>

<p><b>注释：</b>Chrome、Firefox 或 Internet Explorer 不支持 type="datetime"。</p>

</body>
</html>

```
<span style="color:red;">这个在 chrome 上试了下，是不支持的。</span>
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/6F8g1Jji5K.png?imageslim)


## 输入类型：datetime-local

*`<input type="datetime-local">`* 允许用户选择日期和时间（无时区）。

根据浏览器支持，日期选择器会出现输入字段中。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<p>
根据浏览器支持：<br>
当您填写输入字段时会弹出日期选择器。
</p>

<form action="/demo/demo_form.asp">
  生日（日期和时间）：
  <input type="datetime-local" name="bdaytime">
  <input type="submit" value="Send">
</form>

<p><b>注释：</b>Firefox 或者
Internet Explorer 不支持 type="datetime-local"。</p>

</body>
</html>

```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/8eEkD9BI6H.png?imageslim)

## 输入类型：email

*`<input type="email">`* 用于应该包含电子邮件地址的输入字段。

根据浏览器支持，能够在被提交时自动对电子邮件地址进行验证。

某些智能手机会识别 email 类型，并在键盘增加 ".com" 以匹配电子邮件输入。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
  E-mail:
  <input type="email" name="email">
  <input type="submit">
</form>

<p>
<b>注释：</b>IE9 及更早版本不支持 type="email"。</p>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/2cElG6F88d.png?imageslim)

## 输入类型：search

*`<input type="search">`* 用于搜索字段（搜索字段的表现类似常规文本字段）。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="/demo/demo_form.asp">
  搜索谷歌：
  <input type="search" name="googlesearch">
  <input type="submit">
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/mm0F5k8g4L.png?imageslim)


<span style="color:red;">不知道这个有什么区别？</span>

## 输入类型：tel

*`<input type="tel">`* 用于应该包含电话号码的输入字段。

目前只有 Safari 8 支持 tel 类型。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="action_page.php">
  Telephone:
  <input type="tel" name="usrtel">
  <input type="submit">
</form>

<p><b>注释：</b>Safari 8 及更新版本支持 type="tel"。</p>

</body>
</html>

```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/6bFCA5CLJL.png?imageslim)

<span style="color:red;">不知道这个有什么区别？</span>

## 输入类型：url

*`<input type="url">`* 用于应该包含 URL 地址的输入字段。

根据浏览器支持，在提交时能够自动验证 url 字段。

某些智能手机识别 url 类型，并向键盘添加 ".com" 以匹配 url 输入。

### 实例

```html
<!DOCTYPE html>
<html>
<body>

<form action="action_page.php">
  请添加您的首页：
  <input type="url" name="homepage">
  <input type="submit">
</form>

<p><b>Note:</b>IE9 及其更早版本不支持 type="url"。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/HaALFgjC0m.png?imageslim)








# HTML Input 属性

## value 属性

*value* 属性规定输入字段的初始值：

### 实例

```html
<form action="">
 First name:<br>
<input type="text" name="firstname" value="John">
<br>
 Last name:<br>
<input type="text" name="lastname">
</form>
```


## readonly 属性

*readonly* 属性规定输入字段为只读（不能修改）：

### 实例

```html
<form action="">
 First name:<br>
<input type="text" name="firstname" value="John" readonly>
<br>
 Last name:<br>
<input type="text" name="lastname">
</form>
```

readonly 属性不需要值。它等同于 readonly="readonly"。<span style="color:red;">嗯</span>

## disabled 属性

*disabled* 属性规定输入字段是禁用的。

被禁用的元素是不可用和不可点击的。

被禁用的元素不会被提交。<span style="color:red;">嗯，这个不知道</span>

### 实例

```html
<form action="">
 First name:<br>
<input type="text" name="firstname" value="John" disabled>
<br>
 Last name:<br>
<input type="text" name="lastname">
</form>
```

disabled 属性不需要值。它等同于 disabled="disabled"。

## size 属性

*size* 属性规定输入字段的尺寸（以字符计）：

### 实例

```html
<form action="">
 First name:<br>
<input type="text" name="firstname" value="John" size="40">
<br>
 Last name:<br>
<input type="text" name="lastname">
</form>
```

## maxlength 属性

*maxlength* 属性规定输入字段允许的最大长度：

### 实例

```html
<form action="">
 First name:<br>
<input type="text" name="firstname" maxlength="10">
<br>
 Last name:<br>
<input type="text" name="lastname">
</form>
```

如设置 maxlength 属性，则输入控件不会接受超过所允许数的字符。

该属性不会提供任何反馈。如果需要提醒用户，则必须编写 JavaScript 代码。<span style="color:red;">好吧，看来这个方法不是很好</span>

注释：输入限制并非万无一失。JavaScript 提供了很多方法来增加非法输入。如需安全地限制输入，则接受者（服务器）必须同时对限制进行检查。<span style="color:red;">嗯，看来服务器端的检查是必须的。</span>

## HTML5 属性

HTML5 为 `<input>` 增加了如下属性：

- autocomplete
- autofocus
- form
- formaction
- formenctype
- formmethod
- formnovalidate
- formtarget
- height 和 width
- list
- min 和 max
- multiple
- pattern (regexp)
- placeholder
- required
- step

并为 `<form>` 增加如需属性：

- autocomplete
- novalidate

## autocomplete 属性

autocomplete 属性规定表单或输入字段是否应该自动完成。

当自动完成开启，浏览器会基于用户之前的输入值自动填写值。

提示：您可以把表单的 autocomplete 设置为 on，同时把特定的输入字段设置为 off，反之亦然。

autocomplete 属性适用于 `<form>` 以及如下 `<input>` 类型：text、search、url、tel、email、password、datepickers、range 以及 color。

### 实例

自动完成开启的 HTML 表单（某个输入字段为 off）：

```html
<form action="action_page.php" autocomplete="on">
   First name:<input type="text" name="fname"><br>
   Last name: <input type="text" name="lname"><br>
   E-mail: <input type="email" name="email" autocomplete="off"><br>
   <input type="submit">
</form>
```

提示：在某些浏览器中，您也许需要手动启用自动完成功能。

## novalidate 属性

novalidate 属性属于 `<form>` 属性。

如果设置，则 novalidate 规定在提交表单时不对表单数据进行验证。

### 实例

指示表单在被提交时不进行验证：<span style="color:red;">什么时候会不进行验证？</span>

```html
<form action="action_page.php" novalidate>
   E-mail: <input type="email" name="user_email">
   <input type="submit">
</form>
```


## autofocus 属性

autofocus 属性是布尔属性。

如果设置，则规定当页面加载时 `<input>` 元素应该自动获得焦点。<span style="color:red;">嗯，这个比较实用的。</span>

### 实例

使 "First name" 输入字段在页面加载时自动获得焦点：

```html
First name:<input type="text" name="fname" autofocus>
```

## form 属性

form 属性规定 `<input>` 元素所属的一个或多个表单。

提示：如需引用一个以上的表单，请使用空格分隔的表单 id 列表。

### 实例

输入字段位于 HTML 表单之外（但仍属表单）：<span style="color:red;">这也可以</span>

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get" id="form1">
First name: <input type="text" name="fname" /><br />
<input type="submit" value="提交" />
</form>

<p>下面的 "Last name" 字段位于 form 元素之外，但仍然是表单的一部分。</p>

Last name: <input type="text" name="lname" form="form1" />

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/d4gdB3aI12.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/2kAfJ3ffhc.png?imageslim)


## formaction 属性

formaction 属性规定当提交表单时处理该输入控件的文件的 URL。

formaction 属性覆盖 `<form>` 元素的 action 属性。

formaction 属性适用于 type="submit" 以及 type="image"。<span style="color:red;">type="image" 是什么？</span>

### 实例

拥有两个两个提交按钮并对于不同动作的 HTML 表单：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
First name: <input type="text" name="fname" /><br />
Last name: <input type="text" name="lname" /><br />
<input type="submit" value="提交" /><br />
<input type="submit" formaction="/example/html5/demo_admin.asp" value="以管理员身份提交" />
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/4K62gfdgda.png?imageslim)

## formenctype 属性

formenctype 属性规定当把表单数据（form-data）提交至服务器时如何对其进行编码（仅针对 method="post" 的表单）。<span style="color:red;">嗯</span>

formenctype 属性覆盖 <form> 元素的 enctype 属性。

formenctype 属性适用于 type="submit" 以及 type="image"。

### 实例

发送默认编码以及编码为 "multipart/form-data"（第二个提交按钮）的表单数据（form-data）：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_post_enctype.asp" method="post">
  First name: <input type="text" name="fname" /><br />
<input type="submit" value="提交" />
<input type="submit" formenctype="multipart/form-data" value="以 Multipart/form-data 编码提交" />
</form>

</body>
</html>
```
<span style="color:red;">这个例子在 w3school 上尝试的时候有问题，以 Multipart/form-data 编码提交的时候没有结果，因此不知道具体是什么区别，要确认下。</span>

## formmethod 属性

formmethod 属性定义用以向 action URL 发送表单数据（form-data）的 HTTP 方法。

formmethod 属性覆盖 `<form>` 元素的 method 属性。

formmethod 属性适用于 type="submit" 以及 type="image"。

### 实例

第二个提交按钮覆盖表单的 HTTP 方法：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
  First name: <input type="text" name="fname" /><br />
  Last name: <input type="text" name="lname" /><br />
<input type="submit" value="提交" />
<input type="submit" formmethod="post" formaction="/example/html5/demo_post.asp" value="使用 POST 提交" />
</form>

</body>
</html>
```


## formnovalidate 属性

novalidate 属性是布尔属性。

如果设置，则规定在提交表单时不对 `<input>` 元素进行验证。

formnovalidate 属性覆盖 `<form>` 元素的 novalidate 属性。

formnovalidate 属性可用于 type="submit"。

### 实例

拥有两个提交按钮的表单（验证和不验证）：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
E-mail: <input type="email" name="userid" /><br />
<input type="submit" value="提交" /><br />
<input type="submit" formnovalidate="formnovalidate" value="进行没有验证的提交" />
</form>

</body>
</html>
```


## formtarget 属性

formtarget 属性规定的名称或关键词指示提交表单后在何处显示接收到的响应。

formtarget 属性会覆盖 `<form>` 元素的 target 属性。

formtarget 属性可与 type="submit" 和 type="image" 使用。

### 实例

这个表单有两个提交按钮，对应不同的目标窗口：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
  First name: <input type="text" name="fname" /><br />
  Last name: <input type="text" name="lname" /><br />
<input type="submit" value="提交" />
<input type="submit" formtarget="_blank" value="提交到新窗口/选项卡" />
</form>

</body>
</html>
```


## height 和 width 属性

height 和 width 属性规定 `<input>` 元素的高度和宽度。

height 和 width 属性仅用于 `<input type="image">`。

注释：请始终规定图像的尺寸。如果浏览器不清楚图像尺寸，则页面会在图像加载时闪烁。

### 实例

把图像定义为提交按钮，并设置 height 和 width 属性：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
  First name: <input type="text" name="fname" /><br />
  Last name: <input type="text" name="lname" /><br />
  <input type="image" src="/i/eg_submit.jpg" alt="Submit" width="128" height="128"/>
</form>

<p><b>注释：</b>默认地，image 输入类型会发生点击图像按钮时的 X 和 Y 坐标。</p>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/0Gb7gj2A21.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/AcGi40JK7b.png?imageslim)
<span style="color:red;">为什么这个坐标也会提交到 server？</span>

## list 属性

list 属性引用的 `<datalist>` 元素中包含了 `<input>` 元素的预定义选项。

### 实例

使用 `<datalist>` 设置预定义值的 `<input>` 元素：

```html
<input list="browsers">

<datalist id="browsers">
   <option value="Internet Explorer">
   <option value="Firefox">
   <option value="Chrome">
   <option value="Opera">
   <option value="Safari">
</datalist>
```


## min 和 max 属性

min 和 max 属性规定 `<input>` 元素的最小值和最大值。

min 和 max 属性适用于如需输入类型：number、range、date、datetime、datetime-local、month、time 以及 week。

### 实例

具有最小和最大值的 `<input>` 元素：

```html
Enter a date before 1980-01-01:
<input type="date" name="bday" max="1979-12-31">

 Enter a date after 2000-01-01:
<input type="date" name="bday" min="2000-01-02">

 Quantity (between 1 and 5):
<input type="number" name="quantity" min="1" max="5">
```


## multiple 属性

multiple 属性是布尔属性。

如果设置，则规定允许用户在 `<input>` 元素中输入一个以上的值。

multiple 属性适用于以下输入类型：email 和 file。

### 实例

接受多个值的文件上传字段：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
选择图片：<input type="file" name="img" multiple="multiple" />
<input type="submit" />
</form>
<p>请尝试在浏览文件时选取一个以上的文件。</p>

</body>
</html>
```

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/DgbJG8e1CK.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/12lc2AJG9D.png?imageslim)

## pattern 属性

pattern 属性规定用于检查 `<input>` 元素值的正则表达式。

pattern 属性适用于以下输入类型：text、search、url、tel、email、and password。

提示：请使用全局的 title 属性对模式进行描述以帮助用户。

提示：请在我们的 JavaScript 教程中学习更多有关正则表达式的知识。

### 实例

只能包含三个字母的输入字段（无数字或特殊字符）：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
国家代码：<input type="text" name="country_code" pattern="[A-z]{3}"
title="三个字母的国家代码" />
<input type="submit" />
</form>

</body>
</html>
```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/F2H34746h2.png?imageslim)

## placeholder 属性

placeholder 属性规定用以描述输入字段预期值的提示（样本值或有关格式的简短描述）。

该提示会在用户输入值之前显示在输入字段中。

placeholder 属性适用于以下输入类型：text、search、url、tel、email 以及 password。

### 实例

拥有占位符文本的输入字段：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
<input type="search" name="user_search" placeholder="Search W3School" />
<input type="submit" />
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/E99DaCh9fd.png?imageslim)

## required 属性

required 属性是布尔属性。

如果设置，则规定在提交表单之前必须填写输入字段。<span style="color:red;">嗯，这个也是很常用的</span>

required 属性适用于以下输入类型：text、search、url、tel、email、password、date pickers、number、checkbox、radio、and file.

### 实例

必填的输入字段：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
Name: <input type="text" name="usr_name" required="required" />
<input type="submit" value="提交" />
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/97KD2LIGFb.png?imageslim)

## step 属性

step 属性规定 <input> 元素的合法数字间隔。

示例：如果 step="3"，则合法数字应该是 -3、0、3、6、等等。

提示：step 属性可与 max 以及 min 属性一同使用，来创建合法值的范围。

step 属性适用于以下输入类型：number、range、date、datetime、datetime-local、month、time 以及 week。

### 示例

拥有具体的合法数字间隔的输入字段：

```html
<!DOCTYPE HTML>
<html>
<body>

<form action="/example/html5/demo_form.asp" method="get">
<input type="number" name="points" step="3" />
<input type="submit" />
</form>

</body>
</html>

```
![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/5b8FD2hm3A.png?imageslim)
