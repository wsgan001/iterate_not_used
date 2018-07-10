TODO

* 大概知道 JavaScript 是由ECMAScript 和 BOM 和 DOM 三部分组成的。

------

[TOC]



OK，我们先看一下这次的主要内容：

- JavaScript历史问顾
- JavaScript 是什么 
- JavaScript 与 ECMAScript 的关系 $\color{red}\large \textbf{嗯}$
- JavaScript的不同版本





JS 诞生的主要目的：

JavaScript 诞生于1995年。当时，它的主要目的是处理以前由服务器端语言（如Perl ）负责的一 些输人验证操作。

在JavaScript问世之前，必须把表单数据发送到服务器端才能确定用户是否没有填写某个必填域，是否瑜人了无效的值。Netscape Navigator希望通过JavaScript来解决这个问题。 在人们普遍使用电话拔号上网的年代,能够在客户端完成一些基本的验证任务绝对是令人兴奋的。毕竞, 拨号上网的速度之慢，导致了与服务器的毎一次数据交换事实上都成了对人们耐心的一次考验。$\color{red}\large \textbf{竟然是这样，嗯，看来每个东西都是有来由的。}$

从此以后，JavaScript逐渐成为市面上常见浏览器必备的一项特色功能。如今，JavaScript的用途早 已不冉局限于简单的数据验证，而是具备了与浏览器窗口及其内容等几乎所有方面交互的能力。今天的 JavaScript 已经成为一门功能全面的编程语言，能够处理复杂的计算和交互，拥有了闭包、匿名（lamda）函数，甚至元编程等特性。作为Web的一个重要组成部分，JavaScript的重要性是不言而喻的， 就连手机浏览器，甚至那些专为残障人士设计的浏览器等非常规浏览器都支持它$\color{red}\large \textbf{有这种浏览器吗？}$。当然，微软的例子更 为典型。虽然有自己的客户端脚本语言 VBScript ，但微软仍然在 Internet Explorer 的早期版本中加人了自己的JavaScript实现。

JavaScript从一个简单的输人验证器发展成为一门强大的编程语言，完全出乎人们的意料。应该说, 它既是一门非常简单的语言，又是一门非常复杂的语言。说它简单，是因为学会使用它只需片刻功夫； 而说它复杂，是因为要真正掌握它则需要数年时间。要想全面理解和掌握 JavaScript，关键在于弄清楚 它的本质、历史和局限性。$\color{red}\large \textbf{厉害，本质是什么，历史局限性是什么？}$

# 1.JavaScript 简史

在Web日益流行的同时，人们对客户端脚本语言的需求也越来越强烈。

为什么呢？因为，那个时候的绝大多数因特网用户都使用速度仅为 28.8kbit/s的“猫”（调制解调器）上网，但是但网页的大小和复杂性却不断增加。即使是简单的表单验证也要与服务器交换数据。

想象一下：

用户填写 完一个表单，单击“提交”按钮，然后等待30秒钟，最终服务器返回消息说有一个必填字段没有。

因此，当时走在技术革新最前沿的 Netscape 公司，决定着手开发一种客户端语言，用来处理这种简单的验证。

然后 JavaScript 诞生了。

 JavaScript 1.0 获得了巨大成功，然后，微软决定向其Internet Explorer 3中加入了名为 JScript 的 JavaScript 实现（命名为 JScript是为了避开与Netscape冇关的授权问题）。

然后，就有了3个不同的JavaScript版木：

- Netscape Navigator 的 JavaScript
- Internet Explorer 的 Jscript
- ScriptEase 的 CEnvi

因此，JavaScript的标准化问题被提上了议事日程。

1997年，以JavaScript 1.1为蓝本的建议被提交给了欧洲计算机制造商协会（ECMA, European Computer Manufacturers Association ），经过数月的努力完成了一种名为 ECMAScript （发 音为“ek-ma-script”）的新脚本语言的标准。$\color{red}\large \textbf{原来 ECMAScript 在这里出现的。}$

第二年，ISO/IEC （International Organization for Standardization and International Electrotechnical Commission,国标标准化组织和国际电工委员会）也采用了 ECMAScript作为标准（即ISO/IEC-16262 ）自此以后，浏览器开发商就开始致力于将 ECMAScript 作为各自 JavaScript 实现的基础，也在不同程度 上取得了成功。

# 2.JavaScript 实现

虽然 JavaScript 和 ECMAScript 通常都被人们用来表达 相同的含义，但 JavaScript 的含义却比ECMA-262中规定的 要多得多。没错，一个完整的JavaScript实现应该由下列三 个不同的部分组成（见图1-1 ）。

![1528791339848](assets/1528791339848.png)

- 核心（ECMAScript）
- 文档对象模铟（DOM）
- 浏览器对象模型（B0M）

$\color{red}\large \textbf{嗯，可见，JavaScript 包括 ECMAScript}$

## 2.1 ECMAScript

由 ECMA-262 定义的 ECMAScript 与Web 浏览器没有依赖关系。实际上，这门语言本身并不包含输 人和输出定义。ECMA-262定义的只是这门语言的基础，而在此基础之上可以构建更完善的脚本语言。 我们常见的 Web 浏览器只是 ECMAScript 实现可能的宿主环境之一。

宿主环境不仅提供基本的 ECMAScript实现.同时也会提供该语言的扩展，以便语言与环境之间对接交互。而这些扩展一如 DOM，则利用ECMAScript 的核心类型和语法提供更多更具体的功能，以便实现针对环境的操作。其他宿主环境包括Node （ —种服务端JavaScript平台）和 Adobe Flash。$\color{red}\large \textbf{AdobeFlash 的脚本也是 ECMAScript吗？Node也是一种宿主环境吗？}$

既然ECMA-262标准没有参照Web浏览器，那它都规定了些什么内容呢？大致说来，它规定了这 门语言的下列组成部分：

- 语法
- 类型
- 语句
- 关键字
- 保留字
- 操作符
- 对象

ECMAScript 就是对实现该标准规定的各个方面内容的语言的描述。JavaScript实现了 ECMAScript, Adobe ActionScript 同样也实现了 ECMAScript。$\color{red}\large \textbf{嗯。}$

### 1. ECMAScript 的版本

ECMAScript的不同版本又称为版次，以第 x 版表示（意即描述特定实现的ECMA-262规范的第x 个版本）。ECMA-262的最近一版是第5版，发布于2009年。而ECMA-262的第1版本质上与Netscape 的JavaScript 1.1相同——只不过删除了所有针对浏览器的代码并作了 -些较小的改动：ECMA-262要求 支持Unicode标准（从而支持多语言开发），对象也变成了平台无关的（Netscape JavaScript 1.1的对 象在不同平台中的实现不一样，例如Date对象）。这也是JavaScript 1.1和1.2与ECMA-262第1版不一 致的主要原因。

ECMA-262第2版主要是编辑加工的结果。这一版中内容的更新是为了与1SO/IEC-16262保持严格 —致，没有作任何新增、修改或删节处理。因此，一般不使用第2版来衡量ECMAScript实现的兼容性。

ECMA-262第3版才是对该标准第一次真正的修改。修改的内容涉及字符串处理、错误定义和数 值输出。这一版还新增了对正则表达式、新控制语句、try-catch异常处理的支持，并围绕标准的 国际化做出了一些小的修改。从各方面综合来看，第3版标志着 ECMAScript 成为了一门真正的编程 语言。

ECMA-262第4版对这门语言进行了一次全面的检核修订。由于JavaScript在Web上日益流行，开 发人员纷纷建议修订ECMAScript,以使其能够满足不断增长的Web开发需求。作为回应，ECMATC39 重新召集相关人员共同谋划这门语言的未来。结果，**出台后的标准几乎在第3版基础上完全定义了一门 新语言**。第4版不仅包含了强类型变量、新语句和新数据结构、真正的类和经典继承，还定义了与数据 交互的新方式。

与此同时，TC39下属的一个小组也提出了一个名为ECMAScript 3.1的替代性建议，该建议只对这 门语言进行了较少的改进。这个小组认为第4版给这门语言带来的跨越太大了。因此，该小组建议对这 门语言进行小幅修订，能够在现有JavaScript引擎基础上实现。最终，ES3.I附属委员会获将的支持超过 了 TC39，**ECMAS-262第4版在正式发布前被放弃**。

**ECMAScript 3.1 成为 ECMA-262 第5版**，并于2009年12月3日正式发布。第5版力求澄淸第3 版中已知的歧义并增添了新的功能。新功能包括原生JSON对象（用于解析和序列化JSON数据）、继承的方法和髙级属性定义，另外还包含一种严格模式，对ECMAScript引擎解释和执行代码进行了补充说明。

### 2.    什么是ECMAScript兼容

ECMA-262给出了 ECMAScript兼容的定义。要想成为ECMAScript的实现，则该实现必须做到：

- 支持ECMA-262描述的所有“类型、值、对象、属性、函数以及程序句法和语义"（ECMA-262 第1页）
- 支持Unicode字符标准。

此外，兼容的实现还可以进行下列扩展。

- 添加 ECMA-262 没有描述的"更多类型、值、对象、属性和函数”。ECMA-262 所说的这些新增特性，主要是指该标准中没有规定的新对象和对象的新属性。
- 支持 ECMA-262 没有定义的"程序和正则表达式语法”。（也就是说，可以修改和扩展内置的正 则表达式语法。）

上述要求为兼容实现的开发人员基于ECMAScript开发一门新语言提供了广阔的空间和极大的灵活 性，这也从另一个侧面说明了 ECMAScript受开发人员欢迎的原因。

### 3.    Web浏览器对ECMAScript的支持

到了 2008 年，五大主流 Web浏览器（lE、Firefox、Safari、Chrome和Opera）全部做到了与 ECMA-262兼容。IE8是第一个着手实现ECMA-262第5版的浏览器，并在IE9中提供了完整的支持。Firefox4也 紧随其府做到兼容。

| 浏览器                         | ECMAScript 兼容性 | 浏览器            | ECMAScript 兼容性 |
| ------------------------------ | ----------------- | ----------------- | ----------------- |
| Netscape Navigator 2           | —                 | Opera 6 — 7.1     | 第2版             |
| Netscape Navigator 3           | 一                | Opera 7.2+        | 第3版             |
| Netscape Navigator 4 ~ 4.05    | —                 | Safari 1 — 2.0jc  | 第3版.            |
| Netscape Navigator 4.06 ~ 4.79 | 第】版            | Safari 3jt        | 第3版             |
| Netscape 6+ ( Mozilla 0.6.0+)  | 第3版             | Safari            | 第5版.            |
| IE3                            | 一                | Chronic 1+        | 第3版             |
| IE4                            | —                 | Firefox 1 ~2      | 第3版             |
| IE5                            | 第1版             | Firefox 3.0 jc    | 第3版             |
| IE5.5-DE7                      | 第3版             | Firefox 3.5 ~ 3.6 | 第5版.            |
| 1E8                            | 第5版             | Firefox 4.0       | 第5版             |
| IE9-F                          | 第5版             |                   |                   |

•不完全兼容的实现



## 2.2 文档对象模型(DOM)

文档对象模型 (DOM, Document Object Model) 是针对XML但经过扩展用于HTML的应用程序编程接口(API, Application Programming Interface )。$\color{red}\large \textbf{DOM 是API吗？它是一个API吗？}$

DOM把整个页面映射为一个多层节点结构。HTML 或XML页面中的每个组成部分都是某种类型的节点。这些节点又包含着不同类型的数据。看下面这个 HTML页面：

```html
<html>
<head>
    <title>Sample Page</title>
</head>
<body>
<p>Hello World!</p>
</body>
</html>
```

在DOM中，这个页面可以通过如图1.2 所示的分层节点图表示：

![1528792315024](assets/1528792315024.png)

通过DOM创建的这个表示文档的树形图，开发人员获得了控制页面内容和结构的主动权。借助 DOM提供的API。开发人员可以轻松自如地删除，添加、替换或修改任何节点。$\color{red}\large \textbf{借助 DOM提供的API 这句是什么意思？}$

### 1.为什么要使用DOM

在 Internet Explorer 4 和 Netscape Navigator 4 分别支持的不同形式的 DHTML ( Dynamic HTML )基础上，开发人员首次无需重新加载网页，就可以修改其外观和内容了。然而，DHTML在给Web技术发 展带来巨大进步的同时，也带来了巨大的问题。由于Netscape和微软在开发DHTML方面各持己见，过去那个只编写一个HTML页面就能够在任何浏览器中运行的时代结束了。

对开发人员而言，如果想继续保持Web跨平台的天性，就必须额外多做一些工作。而人们真正担心的是，如果不对Netscape和微软加以控制，Web开发领域就会出现技术上两强割据，浏览器互不兼容的局面。此时，负责制定Web通信标准的W3C ( World Wide Web Consortium,万维网联盟)开始着手规划DOM。

### 2.DOM级别

DOM1级(DOM Level 1 )于1998年10月成为W3C的推荐标准。DOM1级由两个模块组成：DOM 核心(DOM Core)和DOM HTML。其中，DOM核心规定的是如何映射基于XML的文档结构，以便 简化对文档中任意部分的访问和操作。DOM HTML模块则在DOM核心的基础上加以扩展，添加了针 对HTML的对象和方法。

请读者注意，DOM并不只是针对 JavaScript 的，很多别的语言也都实现了 DOM。 不过，在Web浏览器中，基于ECMAScript实现的DOM的确已经成为JavaScript这 门语言的一个重要组成部分。

如果说DOM1级的目标主要是映射文档的结构，那么DOM2级的目标就要宽泛多了。DOM2级在 原来DOM的基础上又扩充了( DHTML —直都支持的)鼠标和用户界面事件、范围、遍历(迭代DOM 文档的方法)等细分模块，而通过对象接口增加了对CSS (Cascading Style Sheets，层叠样式表) 的支持。DOM1级中的D0M核心模块也经过扩展开始支持XML命名空间。

DOM2级引人了下列新模块，也给出了众多新类型和新接口的定义。

- DOM视图(DOM Views)：定义了跟踪不同文档(例如，应用CSS之前和之后的文梢)视图的 接口；
- DOM事件(DOMEvents)：定义了事件和事件处理的接口；
- DOM样式(DOM Style )：定义了基于CSS为元索应用样式的接U ;
- DOM遍历和范围(DOMTraveisal and Range)：定义了遍历和操作文饩树的接口。

DOM3级则进一步扩展了 DOM，引人了以统一方式加载和保存文档的方法--在DOM加载和保存(DOM Load and Save)模块中定义；新增了验证文档的方法--在DOM验证(DOM Validation )模块中定义。

DOM3级也对DOM核心进行了扩展，开始支持XML 1.0规范，涉及XML Infoset、XPath 和 XML Base 。

在阅读DOM标准的时候，读者可能会看到DOMO级（DOM Level 0）的字眼。

实际上，DOMO级标准是不存在的；所谓DOMO级只是DOM历史坐标中的一个参照 点而已。具体说来，DOMO 级推的是 Internet Explore 4.0 和 Netscape Navigator 4.0 最初支持的DHTML。

### 3.其他DOM标准

除了 DOM核心和DOM HTML接口之外，另外几种语言还发布了只针对自己的DOM标准。下面列出的语言都是基于XML的，毎种语言的DOM标准都添加了与特定语言相关的新方法和新接口：

- SVG （ Scalable Vector Graphic,可伸缩矢适图）1.0 $\color{red}\large \textbf{没想到 SVG 也是一种 DOM}$
- MathML （ Mathematical Markup Language,数学标记语言）1.0 $\color{red}\large \textbf{这个现在还有吗？}$
- SMIL （ Synchronized Multimedia Integration Language，同步多媒体集成语言）。

还有一些语言也开发了自己的DOM实现，例如Mozilla的XUL（ XML User Interface Language，XML 用户界面语言）。但是，只有上面列出的几种语言是W3C的推荐标准。

### 4. Web浏览器对DOM的支持

现在基本上都支持了。

| 浏览器                         | DOM兼容性                         |
| ------------------------------ | --------------------------------- |
| Netscape Navigator 1. ~ 4jc    | —                                 |
| Netscape 6+ ( Mozilla 0.6.(H-) | 1级、2级（几乎全部）、3级（部分） |
| IE2-IE4jc                      | —                                 |
| IE5                            | 1级（最小限度）                   |
| IE5.5-IE8                      | 1级（几乎全部）                   |
| IE9+                           | 1级、2级、3级                     |
| Opera 1 ~6                     | —                                 |
| Opera 7~8jc                    | 1级（几乎全部）、2级（部分）      |
| Opera 9 — 9.9                  | 1级、2级（几乎全部）、3级（部分） |
| Opera 10+                      | 1级、2级、3级（部分）             |
| Safari 1.0 jc                  | 1级                               |
| Safari 2+                      | 1级、2级（部分）                  |
| Chrome 1+                      | 1级、2级（部分）                  |
| Firefox 1+                     | 1级、2级（几乎全部）、3级（部分） |



## 2.3 浏览器对象模型（BOM）

Internet Explorer 3 和 Netscape Navigator 3 有一个共同的特色，那就是支持可以访问和操作浏览器窗口的浏览器对象模塑（BOM, Browser Object Model ）。开发人员使用 BOM 可以控制浏览器显示的页面 以外的部分。

而BOM真正与众不同的地方（也是经常会导致问题的地方），还是它作为JavaScript实现 的一部分但却没有相关的标准。这个问题在HTML5中得到了解决，HTML5致力于把很多BOM功能写 人正式规范。HTML5发布后，很多关于BOM的困惑烟消云散。

从根本上讲，BOM只处理浏览器窗口和框架；仴人们习惯上也把所有针对浏览器的JavaScript扩展 算作BOM的一部分。下面就是一些这样的扩展：

- 弹出新浏览器窗口的功能；
- 移动、缩放和关闭浏览器窗口的功能；
- 提供浏览群详细信息的navigator对象；
- 提供浏览器所加载页面的详细信息的location对象；
- 提供用户显示器分辨率详细信息的screen对象；
- 对cookies的支持；
- 像 XMLHttpRequest 和 IE 的 ActiveXObject 这样的自定义对象。

由于没有BOM标准可以遵循，因此每个浏览器都有自己的实现。虽然也存在一些事实标准，例如要有 window 对象和 navigator 对象等，但每个浏览器都会为这两个对象乃至其他对象定义自己的属 性和方法。现在有了 HTML5, BOM实现的细节有望朝着兼容性越来越髙的方向发展。$\color{red}\large \textbf{嗯。}$第8章将深入讨论 BOM。

# 3 JavaScript 版本

作为Netscape “继承人”的Mozilla公司，是S前唯一还在沿用最初的JavaScript版本编号序列的浏览器开发商。在Netscape将源代码提交给开源的Mozilla项目的时候，JavaScript在浏览器中的最后一个 版本号是1.3。（如前所述，1.4版是只针对服务器的实现。）后来，随着Mozilla基金会继续开发 JavaScript, 添加新的特性、关键字和语法，JavaScript 的版本号继续递增。下表列出了 Netscape/Mozilla浏览器中 JavaScript版本号的递增过程：

| 浏览器                        | JavaScript 版本 | 浏览器      | JavaScrip 搬本 |
| ----------------------------- | --------------- | ----------- | -------------- |
| Netscape Navigator 2          | 1.0             | Firefox 1.5 | 1.6            |
| Netscape Navigator 3          | 1,1             | Firefox 2   | 1.7            |
| Netscape Navigator 4          | 1.2             | Firefox 3   | 1.8            |
| Netscape Navigator 4.06       | 1.3             | Firefox 3.5 | 1.8.1          |
| Netscape 6+ ( Mozilla 0.6.Q+J | 1.5             | Firefox 3.6 | 1.8.2          |
| Firefox 1                     | 1.5             |             |                |

实际上，上表中的编号方案源自 Firefox 4 将内置 JavaScript 2.0 这一共识。因此，2.0 版之前每个递增的版本号，表示的是相应实现与 JavaScript 2.0 开发目标还有多大的距离。虽然原计划是这样，但 JavaScript的这种发展速度让这个计划成为不再可行。目前，JavaScript2.0还没有目标实现。

请注意，只有 Netscape/Mozilla 浏览器才遵循这种编号模式。例如，IE 的 JScript 就采用了另一种版本命名方案。换句话说，JScript 的版本号与上表令 JavaScript的版 本号之间不存在任何对应关系。而且，大多数浏览器在提及对 JavaScript 的支持情况 时，一叛都以 ECMAScript 兼容性和对 DOM 的支持情况为准。$\color{red}\large \textbf{嗯，这个还是要知道的。}$

# 4.小结

JavaScript是一种专为与网页交互而设计的脚本语言，由下列三个不同的部分组成：

- ECMAScript，由ECMA-262定义，提供核心语言功能
- 文档对象模型（DOM），提供访问和操作网页内容的方法和接口
- 浏览器对象模型（BOM），提供与浏览器交互的方法和接口

JavaScript 的这三个组成部分，在当前五个主要浏览器（IE、Firefox, Chrome、Safari 和 Opera ）中 都得到了不同程度的支持。其中，所有浏览器对 ECMAScript第3版的支持大体上都还不错，而对 ECMAScript 5 的支持程度越来越髙，但对DOM的支持则彼此相差比较多。对HTML5已经正式纳入标准的BOM来说，尽管各浏览器都实现了某些众所周知的共同特性，但其他特性还是会因浏览器而异。$\color{red}\large \textbf{嗯}$