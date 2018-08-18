第O章

BOM

本章内容

□理解window对象-BOM的核心

□控制窗口、框架和弹出窗口 □利用location对象中的贞面信息 □使用navigator Xd■象了解浏览器

「CMAJ

 

Script是JavaScript的核心，但如果要在Web中使用JavaScript,那么BOM (浏览器对象模 则无疑才是真正的核心。BOM提供了很多对象，用于访问浏览器的功能，这些功能与任 何网页内容无关。多年来，缺少事实上的规范导致BOM既有意思又冇问题，因为浏览器提供商会按照各 自的想法随意去扩展它。T是，浏览器之间共有的对象就成为了事实上的标准。这些对象在浏览器中得以 存在，很大程度上是由于它们提供丫与浏览器的互操作性。W3C为了把浏览器中JavaScript最基本的部分 标准化，已经将BOM的主要方面纳入了 HTML5的规范中。

8.1 window 对象

BOM的核心对象是window,它表示浏览器的一个文例。在浏览器巾，window对象有双重角色， 它既是通过JavaScript访问浏览器窗口的一个接U, 乂是ECMAScript规定的Global对象。这意味着 在网页中定义的任何一个对象、变量和函数，都以window作为其Global对象，因此有权访问 parselnt {> 等方法。

8.1.1全局作用域

由于window对象同时扮演着ECMAScript中Global对象的角色，因此所有在全局作用域中声明 的变量、函数都会变成window对象的属性和方法。来看K面的例子。

var age = 29; function sayAge(){

alert(this.age);

}

我们在全局作用域中定义了一个变tt age和一个函数sayAgeO,它们被自动归在了 window对象 名下。于是，可以通过window.age访问变量age,可以通过window. sayAge ()访问函数sayAge () □

由于sayAge（）存在于全局作用域中，因此this.age被映射到window.age,最终显示的仍然是正确 的结果。

抛开全局变量会成为window对象的属性不谈，定义全局变量与在window Xf象上直接定义属性还 是有一点差别：全局变《不能通过delete操作符删除，而直接在window对象上的定义的属性可以。 例如：

var age = 23;

window.color = "red";

//在IE < 9时拋出錯误，在其他所有洌览器中都返筠false delete window.age;

//在IE < 9时拋出错误，在其他所有浏览器中都返田true delete window.color； //returns true

alert (window, age) ;    "29

alert(window.color)； //undefined

DeleteOperatorExampleOl .htm

刚才使用var语句添加的window厲性右一个名为[[Configurable]]的特性，这个特性的值被 设置为false，因此这样定义的属性不可以通过delete操作符删除。1E8及更早版本在遇到使川delete 刪除window属性的语句时，不管该属性最初是如何创建的，都会抛出错误，以示警告。IE9及更髙版 本不会抛出错误=

另外，还要记住一件事：尝试访问未声明的变M会抛出错误，但是通过査询window对象，可以知 道某个可能未声明的变摄是否存在。例如：

//这里会拋出错误，因为oldValue未定义 var newValue = oldValue；

//这里不会抛出鏘误，因为这是一次羼性查询

//newValue 的值是 undefined

var newValue = window.oldValue;

本章后面将要讨论的很多全局JavaScript对象（如location和navigator ）实际上都是window 对象的属性c

Windows Mobile平台的IE湖览器不允许通过window.property = value之类 的形式，直接在window对象上创建新的属性或方法。可是，在全局作用域中声明的 所有变量和函数，照样会变成window对象的成员。

8.1.2窗口关系及框架

如果页面巾包含框架，贝IJ每个框架都拥有自己的windowX橡.，并且保存在frames彙合中。在frames 集合中，可以通过数值索引（从0开始，从左至右，从上到下）或者框架名称来访问相应的window对 象。每个window对象都存一个name属性，其中包含框架的名称。下面是一个包含框架的页面：

<html>

<head>

<title>Frameset Example</1i11e>

</head>

<£rameseL rows="160/*■>

<frame src="frame.htm" name="topFrame">

<frameset cols="50%,50%M>

<frame src="anotherframe.htm" name="1eftFrame">

<frame src="yetanotherframe.htm" name-"rightFrame■>

</frameset>

</frameset>

FramesetExampleOl. htm

以上代码创建了一个框架集，其中一个框架居上，两个框架居下。对这个例子而言，可以通过 window, frames [0]或者window, frames [ "topFrame"]来引用上方的框架。不过，恐怕你最好使用 top而非window来引用这些框架（例如，通过top. frames [0]）。

我们知道，top对象始终指向最髙（最外）层的框架，也就是浏览器窗口。使用它可以确保在一个 框架中正确地访问另一个框架。因为对于在一个框架中编写的任何代码来说，其中的window对象指向 的都是那个框架的特定实例，而非最髙层的框架。图8-1展示丫在最髙层窗口中，通过代碍来访问前面 例子中毎个框架的不同方式。

| window.frames(OJ1 window.framesnopFrame"] top.frames[0] top.frames["topFramewJ frames[0Jframes^topFrame"] |                               |
| ------------------------------------------------------------ | ----------------------------- |
| window.frames[l]                                             | window.frames[2]              |
| window.frames(MleftFrameKl                                   | window .frames( "rightFrameM] |
| top.frames[lj                                                | top.framesl2】                |
| top.framesrieftFrame*]                                       | top.frames["rightFrameMJ      |
| frames[l]                                                    | frames[2]                     |
| frames("leftFrame"]                                          | frames["rightFrame"l圓圓      |

图8-1

与top相对的另一个window对象是parent»顾名思义，parent （父）对象始终指向当前框架的 S接上层框架。在某些情况下，parent有可能等于top;但在没有框架的情况下，parent —定等于 top （此时它们都等于window）。再看下面的例子。

<htinl>

<head>

<title>Frameset Example</title>

</head>

<franeset rows="100,*">

<frame src = "frame.htm" name=■topFrame*>

<frameset cols=w50%,50%">

<frame src="anotherfrane.htm" name="leftFrame">

<frame src=ManotherfraineBet.htm" name*"rightFramo">

</frameset>

</frameset>

</html>

framesetl.htm

这个框架集中的一个框架包含了另一个框架集，该框架集的代码如下所示。

<head>

<title>Frameset Example</title>

</head>

<frameset cols=B50%,50%">

<frame src="red.htm" name="redFramc">

<frame src='blue.htm" name="blueFrame■>

</frameset>

</html>

anotherframeset • htm

浏览器在加载完第一个框架集以后，会继续将第二个框架集加载到rightFrame中。如果代码位于 redFrame (或blueFrame )中，那么parent对象指向的就是rightFrame。可是，如果代码位于 topFrame中，则parent指向的是top, H为topFrame的S接上层框架就是最夕卜层框架。困8-2展 示了在将前面例子加载到浏览器之后，不同window对象的值。

图8-2

注意，除非最髙展窗口是通过window, open （）打开的（本章后面将会讨论），否则window对象 的name属性不会包含任何值o

与框架有关的最后一个对象是self，它始终指向window;实际上，self和window对象可以互 换使用。弓|人self对象的目的只是为了与top和parent对象对应起来，因此它不格外包含其他值。

所有这些对象都是window对象的屑性，可以通过window.parent、window, top等形式来访问。 同时，这也意味着可以将不同层次的window对象连缀起来，例如window .parent .parent. frames [0] o

使用框架的情况下，浏览器中会存在多个Global对象。在每个框架中定义的 全局变:t会自动成为框架中window对象的属性。由于每个window对象都也含原生 类型的构造函数，因此每个框架都有一套自己的构造函数，这些构造函数一一对应，| 但并不相等。例如，top.Object并不等于top.frames[0] .Object。这个问題会i 澎响到对跨框架传递的对象使用instanceof操作符。

8.1.3窗口位置

用来确定和修改window对象位置的属性和方法有很多。IE、Safari、Opera和Chrome都提供了 screenLeft和screenTop属性，分别用于表示窗口相对于屏幕左边和上边的位置。Firefox则在 screenX和screenY属性巾提供相同的窗口位靑信息，Safari和Chrome也同时支持这两个属性。Opera 虽然也支持screenX和screenY厲性，但与screenLeft和screenTop厲性并不对应，因此建议大 家不要在Opera中使用它们。使用下列代码可以跨浏览器取得窗口左边和上边的位置。

var leftPos = (typeof window.screenLeft == "number") ?

window.screenLeft : window.screenX；

var topPos = {typeof window.screenTop == ■number") ?

window.screenTop : window.screenY；

Windo wPositionExampleOl. htm

这个例子运用二元操作符首先确定screenLeft和screenTop属性是否存在，如果是（在IE、 Safari、Opera和Chrome中），则取得这两个属性的值。如果不存在（在Firefox中），则取得screenX 和screenY的值。

在使用这些值的过程中，还必须注意一S小问题■，在IE、Opera和Chrome中，screenLeft和screenTop 中保存的是从屏幕左边和上边到由window对象表示的页面可见区域的距离。换句话说，如果window 对象是最外层对象，而且浏览器窗U紧贴屏葙最上端一即;v轴坐标为0,那么screenTop的值就是 位于页面可见区域上方的浏览器工具栏的像素高度。但是，在Firefox和Safari中，screenY或screenTop 中保存的是整个浏览器窗口相对于屏幕的坐标值，即在窗门的y轴坐标为0时返回0。

更让人捉摸不透是，Firefox、Safari和Chrome始终返回页面中每个框架的top.screenX和 top.screenY值。即使在页面由于被设置了外边距而发生偏移的情况下，相对于window对象使用 screenX和screenY每次也都会返回相同的值。而EE和Opera则会给出框架相对于屏幕边界的精确坐 标值。

最终结果，就是无法在跨浏览器的条件下取得窗口左边和上边的精确坐标值。然i,使用moveToO 和moveByO方法倒是有可能将窗口精确地移动到一个新位置。这两个方法都接收两个参数，其中

moveToO接收的是新位智的*和坐标值，而moveByO接收的是在水平和垂直方向上移动的像素数。

下面来看几个例子：

//将窗O移动到界幕左上角 window.moveTo(0,0);

//将窗向下移动100像索 window.moveBy(0,100);

//将窗口移动到(200,300) window.moveTo(200,300);

//将窗口向左移动50像索 window.moveBy(-50,0)；

需要注意的是，这两个方法可能会被浏览器禁用；而且，在Opera和IE 7(及更髙版本)中默认就 是禁用的。另外，这两个方法都不迢用于框架，只能对最外层的window对象使用。

8.1.4窗口大小

跨浏览器确定一个窗口的大小不是一件简单的事o IE9+、Firefox、Safari、Opera和Chrome均为此提 供了 4 个属性：innerWidth、inner He ights outerWidth 和 outerHeight。在 IE9+、Safari 和 Firefox 中，outerWidth和outerHeight返回浏览器窗口木身的尺寸(无论是从最外层的window对象还是从 某个框架访问)。在Opera中，这两个屑性的值表兩页面视阁容器$的大小。而innerWidth和innerHeight 则表示该容器中页面视图区的大小(减去边框宽度)。在Chrome中，outerWidth、outerHeight与 innerWidth、innerHeight返回相同的值，即视口( viewport )大小而非浏览器窗口大小o

脱及更¥版本没有提供取得当前浏览器窗口尺寸的属性；不过，它通过DOM讎了页面可见K域 的相关信息。

在 IE、Firefox、Safari、Opera 和 Chrome 中，document. document Element. clientwidth document. documentElement. clientHeight中保存页面视口的信息。在IE6中，这些属性必须在 标准模式F才有效;如果是混杂模式,就必须通过document. body, clientwidth和document. body. clientHeight取得相同信息。而对于混杂模式下的Chrome,则无论通过document .documentElement 还是document.body屮的clientwidth和clientHeight属性，都可以取得视口的大小。

虽然最终无法确定浏览器窗U本身的大小，但却可以取得页面视U的大小，如下所示。

var pageWidth - window.innerWidth, pageHeight -window.innerHeight；

if (typeof pageWidth != "number"){

if (document.compatMode == ”CSSlCompat"){

pageWidth = document.documentElement.clientwidth; pageHeight = document.documentElement.clientHeight;

} else {

pageWidth = document.body.clientwidth; pageHeight - document.body.clientHeight;

}

}

WindowSizeExampleO! .htm

①这里所谓的“页面视阁容器”指的是Opera中单个标签页对应的浏览器窗口。

在以上代码中，我们首先将window.innerWidth和window.innerHeight的值分别赋给■厂 pageWidth和pageHeight。然后检喪pageWidth屮保存的是不是-个数值；如果不是，则通过检盘 document. compatMode (这个M性将在第10章全面讨论)来确定页面是否处于标准模式。如果是，则 分别使用 document. document Element. cl ientWidth 和 document .documentElement .client-Height 的倍。否贝1J，就使用 document. body, cl ientWidth 和 document. body. clientHeight 的值。

对于移动设备，window. innerWidth和window. innerHeight保存着时见视口，也就是屏幕上可 见页面区域的大小。移动IE浏览器不支持这鸣属性，何通过document. docximentElenent. client -Width和document. documentElement. clientHeihgt提供了相同的信息a随着A面的缩放，这些值 也会相应变化。

在其他移动浏览器中，document. documentElement度ffi的是布局视口，即渲染后页面的实际大 小(与可见视口不同，可见视口只是整个页面中的一小部分)c移动IE浏览器把布局视口的信息保存在 document.body.clienLWidth和document.body.clientHeight 中。这些值不会随着页面缩放变化。

由于与桌面浏览器间存在这些爱异，最好是先检测一下用户是否在使用移动设备，然后再决定使用 哪个属性。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-38.jpg)

 

有关移动设备视口的话题比较复杂，有很多非常规的情形，也有各种各样的建议。 移动开发咨询师Peter-Paul Koch记述了他对这个问题的研究：[http://tCn/zOZsOTz](http://tCn/zOZsOTz%e3%80%82%e5%a6%82)[。如](http://tCn/zOZsOTz%e3%80%82%e5%a6%82) 果你在做移动Web开发，推荐你读一读这篇文章。

另夕卜，使用resizeToU和resizeBy ()方法可以调整浏览器窗口的大小。这两个方法都接收两个 参数，其中resizeToO接收浏览器窗口的新宽度和新高度，而:resizeBy ()接收新窗口与原窗口的宽 度和髙度之差。来看下面的例子。

//调整到100x100

window.resizeTo(100, 100);

//调整到200x150 window.resizeBy{100, 50);

//调整到300x300

window.resizeTo(300z 300);

®要注意的是，这两个方法与移动窗位置的方法类似，也有可能被浏览器禁用；而且，在Opera 和IE7(及更髙版本)中默认就是黎用的。另外，这两个方法同样不适用于框架，而只能对最外层的 window对象使用。

8.1.5导航和打开窗口

使用window, open (＞方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口。这个 方法可以接收4个参数：要加载的URL、窗口 g标、一个特性字符串以及一个表示新页面是否取代浏览 器历史记录屮当前加载页面的布尔值。通常只须传递第一个参数，最后一个参数只在不打开新窗口的情 况下使用。

如果为window.open()传递了第二个参数，而且该参数是已冇窗口或框架的名称，那么就会在具 有该名称的窗口或框架中加载第-个参数指定的URL。看下面的例子。

//等冏于< a href =,rhttp: //vzww.wrox.com" targets■ topFrame■ ></a> window.open{ "<http://www.wrox.com/>" / topFrame'*）;

调用这行代码，就如同用户单击了 href属性为http://www.wrox.com/, target属性为"topFrame" 的链接，如果有一个名叫•topFrame、的窗口或者框架，就会在该窗口或框架加载这个URL;否则，就 会创建一个新窗U并将其命名为•• topFrame %此外，第二个参数也可以是下列任何一个特殊的窗口名 称：_self、_parent、_top ^_blank0

1.弹出窗口

如果给window.open（>传递的第二个参数并不是一个已经存在的窗口或框架，那么该方法就会根 据在第三个参数位置I:传人的字符串创建-个新窗口或新标签页。如果没有传人第三个参数，那么就会 打开一个带有全部默认没靑（工具栏、地址栏和状态栏等）的新浏览器窗【I （或者打开一个新标签页一 一根据浏览器设置）。在不打开新窗口的情况下，会忽略第个参数。

第二个参数是一个逗号分隔的设置字符串，表示在新窗口中都显示哪些特性。下表列出了可以出现 在这个字符串中的设置选项。

| 设    置   | 值        | 说    明                                                     |
| ---------- | --------- | ------------------------------------------------------------ |
| fullscreen | yes 或 no | 表示浏览器窗是否最大化。仅限IE                               |
| height     | 数值      | 表示新窗口的髙度。+能小于100                                 |
| left       | 数值      | 表茨新窗n的友坐标。不能是负值                                |
| location   | yes 或 no | 表示是否在浏览器窗口中显示地址栏D不同浏览器的默认值不同。如果 设腎为no,地&拦可能会隐藏，也可能会被禁用（取决于浏览器） |
| menubar    | yes^no    | 衷示是否在浏览器窗口中显示菜单栏。默汄值为no                 |
| resizable  | yes 或 no | 表示是否可以通过拖动浏览器窗口的边框改变其大小。駄认倍为no   |
| scrollbars | yes 或 no | 表示如果内容在视口中显示不下，是否允许滚动。默认值为no       |
| status     | yes 或 no | 表示是否在浏览器窗口中显示状态栏。默认值为no                 |
| toolbar    | yes 或 no | 表示是否在浏览器窗口中显示工具栏。默认值为no                 |
| top        | 数值      | 表示新窗U的上坐标。不能是负值                                |
| width      | 数值      | 表示新窗口的宽度。不能小T100                                 |

表中所列的部分或全部设置选项，都可以通过逗号分隔的名值对列表来指定。其中，名值对以等号 表示（注意，整个特性字符串巾不允许出现空格），如F面的例子所示。

window.open（"hr.r.p：/Zwww.wrox.com/", "wroxWindow",

"height=400,width=400,top=10,lefc=10,resizable=yes"）;

这行代码会打开一个新的可以调整大小的窗口，窗口初始大小为400x400像索，并且距屏幕上沿 和左边各10像索。

window, open （）方法会返回一个指向新窗口的引用。引用的对象与其他window对象大致相似，但 我们对以对井进行更多控制3例如，有些浏览器托默认情况下可能不允许我们针对主浏览器脔门调幣大 小或移动位置，但却允许我们针对通过window.openO创建的窗口调整大小或移动位置。通过这个返 回的对象，可以像操作其他窗U—样操作新打开的脔U,如下所示。

var wroxWin = window.open("http：//www.wrox.com/","wroxWindow",

"height=400,width=400,top=10,left=10fresizable=yes");

//调整大小

wroxWin.res i 2eTo(500,500);

//移动位里

wroxWin.raoveTo(100,100}?

调用close ()方法还可以关闭新打开的銜口。 wroxWin.close{);

但是，这个方法仅适用于通过window.open()打开的弹出窗口。对于浏览器的主街口，如果没存 得到用户的允许是不能关闭它的。不过，弹出窗口倒是可以调用top. close ()在不经用户允许的悄况 下关闭自己。弹出窗口关闭之后，窗口的引用仍然还在，但除了像下面这样检测其closed属性之外， 已经没有其他用处了。

wroxWin.close();

alert(wroxWin.closed); //true

新创建的window对象有•■斗opener属性，其中保存着打开它的原始窗口对象。这个属性只在弹出 窗口中的最夕卜层window X寸象(top )中有定义，而且指向调用window.open ()的窗U或框架。例如：

var wroxWin = window.open("http：Z/www.wrox.com/","wroxWindow"y

"height=400/width=400/匕op=10,left=10,resizable=yes")；

alert(wroxWin.opener ■= window); //true

虽然弹出窗n屮有一个指针指向打开它的原始窗口，伹原始窗口中并没有这样的指针指向弹出窗 口。窗u并不跟踪记录它们打开的弹出窗n，因此我们只能在必要的时候自己来手动实现跟踪。

有些浏览器(如1E8和Chrome)会在独立的进程中运行每个标签页。当一个标签页打开另一个标 签页时，如果两个window对象之间需要彼此通信，那么新标签页就不能运行在独立的进程中。在Chrome 中，将新创建的标签页的opener属性设置为null,即表示在单独的进程中运行新标签页，如下所示。

var wroxWin = window, open {**http： //www.wrox.com/" f "wroxWindow** #

11 height=400, width=400, top=10, left=10, resizable=yes")；

wroxWin.opener - null;

将opener属性没Jt为null就是告诉浏览器新创建的标签页不需要与打开它的标签页通信，因此 可以在独立的进程中运行。标签页之间的联系一旦切断，将没冇办法恢

2.安全限制

曾经有一段时间，广告商在网上使用弹出窗口达到了肆无忌惮的程度。他们经常把弹出窗口打扮成 系统对话框的模样，引诱用户去点击其中的广告。由于看起来像是系统对话框，一般用户很难分辨是真 是假。为了解决这个问题，有些浏览器开始在弹出窗口配置方面增加限制。

WindowsXPSP2中的IE6对弹出窗11施加了多方面的安全限制，包括不允许在屏幕之外创建弹出窗 口、不允许将弹出窗t:1移动到屏幕以外、不允许关闭状态栏等。IE7则增加了更多的安全限制，如不允 许关闭地址栏、默认情况下不允许移动弹出窗LI或调整其大小。Fircfox 1从一开始就不支持修改状态栏， 因此无论给window.open()传人什么样的特性字符串，弹出窗口中都会无一例外地®示状态栏。后来 的Rrefox 3又强制始终在弹出窗口中显示地址栏。Opera只会在主浏览器窗口中打开弹出窗a,但不允 许它们出现在可能与系统对话框混淆的地方。

此外，有的浏览器只根据用户操作来创建弹出窗口。这样一来，在页而尚未加载完成时调用 window, open()的语句根本不会执行，而且还可能会将错误消息显示给用户。换句话说，只能通过单 击或者击键来打开弹出窗口。

对于那些不是用户有意打开的弹出窗口，Chrome采取了不同的处理方式。它不会像其他浏览器那 样简单地屏蔽这些弹出窗口，而是只显示它们的标题栏，并把它们放在浏览器窗口的右下角。

在打开计算机硬盘中的网页时，IE会解除对弹出窗口的某些限制。但是在服务器 上执行这些代码会受到对弹出窗口的限制。

3.弹出窗口屏蔽程序

大多数浏览器都内置有弹出窗口屏蔽程序，而没有内置此类程序的浏览器，也可以安装Yahoo! Toolbar等带#内置屏蔽程序的实用工具。结果就是用户可以将绝大多数不想看到弹出窗口屏蔽掉。于 是，在弹出窗口被屏蔽时，就应该考虑两种可能性3如果是浏览器内置的屏蔽程序阻止的弹出窗口，那 么window.open(＞很可能会返回null。此时，只要检测这个返回的值就可以确定弹出窗口是否被屏蔽 了，如下面的例子所示。

var wroxWin =： window.open ("http://www.wrox.com* , "_blank"); if (wroxWin == null){

alert{"The popup was blocked!■)；

如果是浏览器扩展或其他程序阻止的弹出窗U,那么window.open 0通常会抛出一个错误。因此, 要想准确地检测出弹出窗口是否被屏蔽，必领在检测返回值的同时，将对window.open ()的调用封装 在一个try-catch块中，如下所示。

var blocked s false;

try {

var wroxWin = window.open(*http:"www.wrox.com*, "_blankw)； if (wroxWin -= null){

blocked = true;

}

} catch (ex){

blocked ■ true;

\>

if (blocked){

alert("The popup was blockedI")/

PopupB lockerExampleO l.htm

在任何情况下，以上代码都可以检测出调用window.openO打开的弹出窗门是不是被屏蔽了。但 要注意的是，检测弹出窗口是否被屏蔽只是一方面，它并不会阻止浏览器显示勺被屏蔽的弹出窗口有关 的消息。

8.1.6间歇调用和超时调用

JavaScript是单线程语言，但它允许通过设置超时值和间歇时间值来调度代码在特定的时刻执行。 前若是在指定的时间过后执行代码，而后者则是每隔指定的时间就执行一次代砰。

超时调用需要使用window对象的setTimeoutU方法，它接受两个参数：要执彳f的代码和以毫秒 表示的时间(即在执行代码前需要等待多少奄秒)。其中，第一个参数可以是一个包含JavaScript代码的 字符串(就和在eval ()函数中使用的字符串-样)，也可以是一个函数。例如，下面对setTimeout () 的两次调用都会在一秒钟后显示一个警告框，，

//不建议传递字符争！

setTimeout("alert('Hello world!')    1000)；

//推存的调用方式 setTimeout(function() {

alert("Hello world!");

}t 1000);

TimeoutExampleOl. htm

虽然这两种调用方式都没有问题，但由于传递字符串可能导致性能损失，因此不建议以字符串作为 第-个参数。

第二个参数是一个表示等待多长时间的毫秒数，但经过该时间后指定的代码不一定会执行。 JavaScript是一个单线程序的解释器，因此一定时间内只能执行一段代码。为了控制要执行的代码，就 有一个JavaScript任务队列。这些任务会按照将它们添加到队列的顺序执行。setTimeout ()的第一.个 参数告诉JavaScript冉过多长时间把与前任务添加到队列中。如果队列是空的，那么添加的代码会立即 执行；如果队列不是空的，那么它就要等前面的代码执行完了以后再执行。

调用setTimeout <)之后，该方法会返回一个数值ID,表示超时调用。这个超时调用1D是计划执 行代码的唯一标识符，可以通过它来取消超时调用。要取消尚未执行的超时调用计划，可以调用 clearTimeoutO方法并将相应的超时调用ID作为参数传递给它，如下所示。

//设置超时调用

var timeoutId = setTimeout(function(> { alert (*Hello world!”；

b 1000)?

//注意：把它取消

clearTimeout(timeoutld);

TimeoutExample02. htm

只要是在指定的时问尚未过去之前调用clearTimeout (),就可以完全取消超时调用。前面的代码 在设置超时调用之后马上又调用了 clearTimeout (),结果就跟什么也没有发生一样。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-39.jpg)

 

超时调用的代码都是在全局作用城中执行的，因此函数中this的值在非严格模 式下4旨向window对象，在戸格模式下是undefined。

间歇调用与超时调用类似，只不过它会按照指定的时间间隔重复执行代码，直至间歇调用被取消或 者页面被卸载。设R间歇调用的方法是setlntervalO,它接受的参数与setTimeout ()相同：要执

行的代码(字符串或函数)和每次执行之前需要等待的毫秒数。下面来看-个例子。 //不建议传递字符串！

setInterval ("alert('Hello world!1) 10000);

//推荐的调用方式

setlnterval (functionU { alert ("Hello world!”；

}, 10000)?

IntervalExampleO 1. htm

调用setmcervalO方法同样也会返回一个间歇调用ID,该ID可用于在将来某个时刻取消间歇 调用。要取消尚未执行的间歇调用，可以使用cl earintervalO方法并传人相应的间歇调用ID。取消 间歇调用的a要性要远远髙于取消超吋凋川，因为在不加干涉的情况下，间歌调用将会一直执行到页面 卸载。以T是一个常见的使用间歇调用的例了 =

var num = 0;

var max = 10;

var intervalld = null;

function incrementNumber() { num+4.；

//如果执行次数达到了 max设定的值，则取消后续尚未执行的调用 if (num max) {

clearIritervsLl( interval工d) alert("Done"};

}

intervalld = setlnterval(incrementNumber, 500)；

IntetvalExampleO2. htm

在这个例子中，变量num毎半秒钟递增一次，当递增到摄大值时就会取消先前设定的间歇调用。这 个模式也可以使用超时调用来实现，如下所示。

var num = 0； var max = 10；

function incrementNumber()    {

num++;

/✓如果执行次数未达到max设定的值，则设置另一次超时调用 i£ (num < max) {

setTimeout(incrementNumber, 500)j } else {

alert("Done")/

}

setTimeout(incrementNumber, 500)j

TtmeouiExample03. htm

可见，在使用超时调用时，没有必要跟踪超时调用ID, W为每次执行代码之后，如果不再设置另 一次超时调用，调用就会A行停止。一般认为，使用超时调用来模拟间歇调用的是一种最佳模式。在开 发环境齐，很少使用真正的间歇调用，原因是后一个间歇调用可能会在前一个间歇调用结束之前后动。 而像前面示例中那样使用超时调用，则完全可以避免这一点。以，最好不要使用间歇调用。

8.1.7系统对话框

浏览器通过alert (＞、conf irm ()和prompt ()方法可以调用系统对话框向用户显ZK消息。系统对 话框与在浏览器中显示的网页没存关系，也不包含HTML。它们的外观由操作系统及(或)浏览器设置 决定，而不是由CSS决定。此外，通过这几个方法打开的对话框都是同步和模态的。也就是说，显示这 些对话框的吋候代码会停止执行，而关掉这些对话框后代码乂会恢复执行。

本书各章经常会用到alert (＞方法，这个方法接受一个字符串并将其显示给用户。具体来说，调用 alert (＞方法的结果就是向用户显示一个系统对话框，其中包含指定的文本和一个OK ( “确定”)按钮。 例如，alert ( "Hello world! •)会在Windows XP系统的IE中生成如阁8-3所示的对话框。

通常使用alert ()生成的"警吿"对话框向用户显示一些他们无法控制的消息，例如错误消息。而 用户只能在看完消息后关闭对话框。

第二种对话框是调用conf irm ＜＞方法生成的。从向用户显示消息的方面来看，这种“确认”对话 框很像是一个“警吿”对话框。但二者的主要区别在于“确认"对话框除了显示OK按钮外，还会显示 —个Cancel ( “取消”)按钮，两个按钮可以让用户决定是否执行给定的操作。例如，confirm ("Are you sure?•＞会显示如图8*4所示的确认对话框。

Mlcrosofl Internet    U

HeJIowoHd*

r 'ok

 

Mlcroioft Internet Explorer Q

 

Am you sure?

| OK | j    Cane:~~|

 

图8-3

 

图8-4

为f确定用户是单击Z OK还是Cancel, nJ以检査confirm (}方法返回的布尔值：true表示单击 了 OK, false表示单击了 Cancel或单击了右上角的X按钮。确认对话框的典型用法如下。

if (confirm("Are you sure?"))(

alert("I'm so glad you're sure!");

} else {

alert("I'm sorry to hear you're not sure.");

}

在这个例子中，第一行代码(if条件语句)会向用户显示一个确认对话框。如果用户单击了 OK, 则通过一个繁传框向用户fi示消息I’m so glad you’re sure!。如果用户单击的是Cancel按钮，则通过瞀 告框显示I’m sorry to hear you’re not sure.。这种模式经常在用户想要执行删除操作的时候使用，例如删 除电子邮件。

最后一种对话框是通过调用prompt (＞方法生成的，这是一个“提示”框，用于提示用户输入一些 文本。提示框中除了显示OK和Cancel按钮之外，还会显示一个文本输入域，以供用户在其中输人内容。 prompt ()方法接受两个参数：要显示给用户的文本提示和文本输人域的默认值(可以是一个空字符串)。

调用prompt {"What' s your name?", "Michael0)会得到如图8-5所东的对话框o

|                 | 卿喇y吻喇嫩腾媒細腳糊.囑■ | IQ                       |
| --------------- | ------------------------- | ------------------------ |
| Prompt:         |                           | 1    OK    \|            |
| What yotr name? |                           | 1 C«a(. J                |
| ! MohMl         |                           | i                        |
|                 |                           | -....................... |
|                 | 图8-5                     |                          |

如果用户单击了 OK按钮，则prompt ()返回文本输人域的值；如采用户承击了 Cancel或没有单击 OK而是通过其他方式关闭了对话框，则该方法返回null。下面是一个例子。

var result = prompt("What is your name?、，""); if (result !=- null) {

alert("Welcome, " + result)；

)

综上所述，这些系统对话根很适合向用户显示消息并清用户作出决定。由于不涉及HTML、CSS或 JavaScript,因此它们是增强Web应用程序的一种便捷方式。

除了上述三种对话椎之外，Google Chrome浏览器还引人了一种新特性。如果当前脚本在执行过程 中会打开两个或多个对话框，那么从第二个对话框开始，每个对话框中都会M示一个复选框，以便用户 阻止后续的对话框显示，除非用户刷新页面(见图8-6)。

图8-6

如果用户勾选了其中的复选框，并且关闭了对W框，那么除非用户刷新页面，所有后续的系统对话 框(包括警吿框、确认框和提示框)都会被屏蔽=Chrome没有就对话框是否显示向开发人员提供任何 信息。由于浏览器会在空闲时甫置对话框计数器，因此如果两次独立的用户操作分别打开两个餐告框， 那么这两个筲告框中都不会显示复选框。而如果是同一次用户操作会生成两个警告根，那么第二个蝥吿 框中就会显示复选框，这个新特性出现以后，1E9和Firefox4也实现了它。

还冇两个可以通过JavaScript打开的对话框，即“査找”和“打印”。这两个对话框都是舁步显示 的，能够将控制权立即交还给脚本。这两个对话框与用户通过浏览器菜单的“查找”和"打印”命令 打开的对话框相同。而在JavaScript中则可以像下面这样通过window对象的f ind ()和print ()方法 打开它们。

//显示“打印”对话框 window.print();

//显示“查找”对话框 window.find();

这两个方法同样不会就用户相对话框中的操作给出任何倌息，因此它们的用处有限，，另外，既然这 两个对话框是异步显示的，那么Chrome的对话框计数器就不会将它们计算在内，所以它们也不会受用 户禁用后续对话框显示的影响。

8.2 location 对象

location是最冇用的BOM对象之一，它提供了与当前窗口中加载的文朽有关的信息，还提供丫一 些导航功能。事实上，location对象是很特别的一个对象，W为它既是window对象的属性，也是 document对象的属性；换句话说，window, location和document.location引用的是同--个对象。 location对象的用处不只表现在它保存着H前文档的信息，还表现在记将URL解析为独立的片段，让 开发人员可以通过不同的属性访问这些片段。下表列出了 location对象的所有属性（注：省略了每个属

性前面的location前缀）o

| 厲性名   | 例    子                              | 说    明                                                     |
| -------- | ------------------------------------- | ------------------------------------------------------------ |
| hash     | "^contents*                           | 返回URL中的hash （托后跟零或多个字符），如果URL 中不包含散列，则返回空宇符串 |
| host     | "www.wrox.com：80"                    | 返间服务器名称和端口号（如果冇）                             |
| hostname | "[www.wrox.com](http://www.wrox.com)" | 返回不带端口 M■的服务器名称                                  |
| href     | "http： /ww-wrox^com'*                | 返回当前加载页面的完整URL。而location对象的 toStringO方法也返M这个值 |
| pathname | "ZWileyCDA/"                          | 返间URL中的Q录和（或）文件名                                 |
| port     | "8080"                                | 返凹URL中指定的端口号。如果URL中不包含端口号，则 这个属性返问空字符串 |
| protocol | "http:•                               | 返W页餌使用的协议。通常是http:或https:                       |
| search   | ■?q=javascript"                       | 返WURL的査洵字符串。这个字符申以问号开头                     |

8.2.1查询字符串参数

51然通过上面的属性可以访问到location对象的大多数信息，但其中访问URL包含的査询宇符 串的周性并不方便。尽管location.search返回从•到URL末尾的所有内容，但却没有办法逐个 访问其中的每个査询字符串参数。为此，可以像下面这样创建一个函数，用以解析査询字符串，然后返 回包含所有参数的一个对象：

function getQuerystringArgs(){

//取锊壹询字符串并去拌开头的问号

var qs = (location.search.length > 0 ? location.search.substring{1) : "•),

//保存数掘的对象 args = {),

//取锊每一項

items = qs . length ? qs . split (■&■):门， item = null,

name = null.

value null,

//在for獨环中使用

i = 0/

len = items.length；

//逐个将每一項添加釗args对象中

for (i=0; i < len； i++){

item = items[iJ.split(■=■)；

name = decodeURlComponent;

value = decodeURlComponent(item[1]);

if (name.length) {

args[name] = value;

}

return args;

LocationExampleO l.htm

这个函数的第一步是先去掉査询字符串开头的问号。当然，前提是location.search中必须要 包含一或多个字符。然后，所有参数将被保存在args对象中，该对象以字面董形式创建。接下来， 根据和号(&)来分割査询字符串，并返冋name=value格式的字符串数组。下面的for循环会迭代 这个数组，然后再根据等于号分割每一项，从而返冋第一项为参数名，第二项为参数值的数组。再使 用decodeURlComponent (＞分别解码name和value (因为查询字符串应该是被编码过的)。最后， 将name作为args对象的属性，将value作为相应属性的值。下面给出了使用这个函数的示例。

//假设查询字苻事是？q=javascript&num=10

var args = geCQueryStringArgs();

alert(args["q"));    //"javascript-

alert(args["num"]); //"10"

可见，每个查询字符串参数都成了返回对象的尿性。这样就极大地方便了对每个参数的访问。

8.2.2位置操作

使用location对象可以通过很多方式来改变浏览器的位置。首先，也是最常用的方式，就是使用 assign ()方法并为其传递一个URL,如下所示。

location.assign("http：//www.wrox.com")；

这样，就可以立即打开新URL并在浏览器的历史记滾中生成一条记录。如果是将location.href 或window, location设置为一个URL值，也会以该值调用assign (＞方法。例如，下列两行代码与 显式调用assigns方法的效果完全一样。

window.location = "<http://www.wrox.com>"; location, href = "http: "[www.wrox.com](http://www.wrox.com)";

在这些改变浏览器位置的方法屮，最常用的是设S location.href属性。

另外.修改location对象的其他属性也可以改变当前加载的災曲。下面的例了-展示了通过将hash、 search、hostname、pathname和port属性设置为新值来改变URL。

//假设初姑 URL 为 <http://www.wrox.com/WileyCDA/>

//将 URL 修改为"[http://www.wrox.com/WileyCDA/#sectionl](http://www.wrox.com/WileyCDA/%23sectionl)"

location.hash = "#sectionl"；

//将 URL 修改为"http： //www. wrox.com/WileyCDA/?q=javascript"

location.search - •?q=javascript■;

//将 URL 修改为"<http://www.yahoo.com/WileyCDA/>"

location.hostname = •www.yahoo.com";

//将 URL 修改为■http://www.yahoo.com/mydir/" location.pathname = "mydir";

"将 URL 修改为"http: //[www.yahoo](http://www.yahoo). com: 8080/Wi leyCDA/ •

location.port = 8080;

每次修改location的屑性（hash除外），页面都会以新URL i新加载。

C^\ 在 IE8、Firefox 1> Safari 2+、Opera 9+和 Chrome 中，修改 hash 的值会在浏览 器的历史记录中生成一条新记录。在IE的早期版本中，hash属性不会在用户单击“后 退”和“前进”按钮时被更新，而只会在用户单击包含hash的URL时才会被更新。

当通过上述任何一种方式修改URL之后，浏览器的历史记录中就会生成一条新记录，因此用户通 过单击“后退"按钮都会导航到前一个页面。要禁用这种行为，可以使用replaced方法。这个方法 只接受一个参数，即要导航到的URL;结果虽然会导致浏览器位置改变，但不会在历史记录中生成新记 录。在调用replaced方法之后，用户不能回到前一个页面，来看下面的例子：

<!DOCTYPE html>

<html>

<head>

<title>You won*t be able to get back here</title>

</head>

<body>

<p>Enjoy this page for a second, because you won't be coming back here.</p> <script type="text/javascript:*>

setTimeout(function (> {

location.replace("[http://www.wrox.com/")](http://www.wrox.com/%22)%ef%bc%9b)[；](http://www.wrox.com/%22)%ef%bc%9b)

}, 1000);

</script>

</body>

</html>

LocationReplaceExampleOl .him

如果将这个页面加栽到浏览器中，浏览器就会在1秒钟后重新定向到www.wrox.com。然后，“后退” 按钮将处于禁用状态，如果不重新输人完整的URL,则无法返回示例页面。

与位置有关的最后一个方法是reloadU,作用足重新加载当前显示的页面。如果调用reload() 时不传递任何参数，页面就会以最存效的方式重新加载。也就足说，如果页面自上次请求以来并没有改 变过，贞面就会从浏览器缓存中重新加载。如果要强制从服务器重新加载，则需要像下面这样为该方法 传递参数true。

location.reload（｝;    //重浙加载（有可能从緩4•中加载）

location, reload （true） ;    //重新加载（从服务S重新加载）

位于reload （）调用之后的代码可能会也可能不会执行，这要取决于网络延迟或系统资源等因素。 为此，最好将reload （）放在代码的最后一行。

8.3 navigator 对象

最早由Netscape Navigator 2.0引人的navigator对象，现在已经成为识另1j客户端浏览器的事实标 准。虽然其他浏览器也通过其他方式提供了相同或相似的信息（例如，IE中的window.clientlnfor-mation和Opera巾的window.opera ）,但navigator对象却是所有支持JavaScript的浏览器所共有 的。与其他BOM对象的情况一样，每个浏览器中的navigator对象也都有一套自己的属性c下表列 出了存在于所有浏览器中的属性和方法，以及支持它们的浏览器版本。

| 属性或方法                   | 说    明                                                     | IE    | Firefox | Safari/Chrome | Opera |
| ---------------------------- | ------------------------------------------------------------ | ----- | ------- | ------------- | ----- |
| appCodeKame                  | 浏览器的名称。通常都是Mozilla，即 使在非MoziUa浏览器中也楚如此 | 3.0+  | 1.0+    | 1.0+          | 7.(K  |
| appMinorVers i on            | 次版本信息                                                   | 4.0+  | -       | -             | 9.5+  |
| a 卯 Name                    | 完整的浏览器名称                                             | 3.(H- | 1.04-   | 1.0+          | 7.0+  |
| appVersion                   | 浏览器的版本。。般不与实际的浏览器 版本对应                  | 3.0+  | 1.0+    | 1.0+          | 7.0+  |
| buildID                      | 浏览器编译版本                                               | -     | 2.0+    | -             | -     |
| cookieEnabled                | 表示cookie是否启用                                           | 4.0+  | 1.0+    | 1.0+          | 7.0+  |
| cpuClass                     | 客户端计算机中使用的CPU类迆（x86、 68K、Alpha, PPC或Other）  | 4.0+  | -       | -             | -     |
| javaEnabled()                | 表示当前浏览器中是否后用了 Java                              | 4.0+  | l.(H-   | 1.0+          | 7.0+  |
| language                     | 浏览器的主语言                                               | -     | 1.(^    | 1.0+          | 7.0+  |
| mimeTypes                    | 在浏览器中注册的MIME类型数组                                 | 4.(H- | 1.0+    | 1.0+          | 7.0+  |
| onLine                       | 表示浏览器是否连接到广因特网                                 | 4.0+  | 1.0+    | -             | 9.5+  |
| opsProfile                   | 似乎旱就+用了。査不到相关文档                                | 4.(H- | -       | 一            | -     |
| oscpu                        | 客户端计算机的操作系统或使用的CPU                            | -     | 1.0+    | -             | -     |
| Platform                     | 浏览器所在的系统平台                                         | 4.0+  | 1.0+    | 1.0+          | 7.0+  |
| plugins                      | 浏览器中安装的插件信思的数组                                 | 4.0+  | 1.0+    | 1.0+          | 7.0+  |
| preference()                 | 设置用户的首选项                                             | -     | 1.5+    | -             | -     |
| product                      | 产品名称（如Gecko ）                                         | -     | 1.04-   | 1.0+          | -     |
| productSub                   | 关于产品的次要信息（如Gecko的版本）                          | -     | l.(R    | 1.0+          | -     |
| register-ContentHandler()    | 针对特定的MIME类型将•-个站点注册 为处理程序                  | -     | 2.(H-   | -             | -     |
| register-ProtocolHc»ndler (} | 针对特定的协议将一个站点注册为处 理程序                      | -     | 2.0     | -             | -     |
| securityPolicy               | 已经废弃。安全策略的名称。为丫与 Netscape Navigator 4向后兼容而保贸下来 | -     | 1.0+    | -             | -     |

| （续）          |                                                              |      |          |               |       |
| --------------- | ------------------------------------------------------------ | ---- | -------- | ------------- | ----- |
| 属性或方法      | 说    明                                                     | IE   | Fi refox | Safari/Chrome | Opera |
| sys temLanguage | 操作系统的语言                                               | 4.0+ | -        | 一            | -     |
| taintEnabled()  | 已经废弃。表示是否允许变虽被修改 （taint）。为了 ^Netscape Navigator 3向后 兼容而保留下来 | 4.0+ | 1.0+     |               | 7.0+  |
| userAgent       | 浏览器的用户代理字符串                                       | 3.0+ | 1.0+     | l‘(K          | 7.0+  |
| userLanguage    | 操作系统的默汄语言                                           | 4.0+ | -        | -             | 7.0+  |
| userProfile     | 借以访问用户个人信息的对象                                   | 4.0+ | -        | 一            | -     |
| vendor          | 浏览器的品牌                                                 | -    | 1.0+     | l.(b-         | —     |
| vendorSub       | 有关供应商的次要信息                                         | -    | 1.0+     | 1.0+          | -     |

表屮的这些navigator对象的属性通常用于检测敁示网页的浏览器类型（第9章会详细讨论）。

8.3.1检测插件

检测浏览器巾是否安装了特定的插件是一种最常见的检测例程。对于非IE浏览器，可以使用 Plugins数组來达到这个目的。该数组中的每一项都包含下列属性。

□    name：插件的名字。

□    description：插件的描述。

□    filename：插件的文件名。

□    length：插件所处理的MIME类型数竜。

一般来说，name属性中会包含检测插件必需的所有信息.但有时候也不完全如此。在检测插件时, 需要像下面这样循环迭代每个插件并将插件的name与给定的名字进行比较。

//检測插件(在IE中无效)

function hasPlugin(name)<

name = name.toLowerCase();

for (var i-0； i < navigator.plugins.length; i++){

if (navigator, plugins [i].name.toLowerCase().indexOf(name) > -1){

return true；

}

return false；

}

//检測 Flash

alert(hasPlugin("Flash*))；

//检測 QuickTime

alert(hasPlug£n("QuickTime"));

PluginDetectionExampleOl. him

这个hasPluginU函数接受一个参数：要检测的插件名。第一步是将传人的名称转换为小写形式, 以便于比较。然后，迭代plugins数组，通过indexOEG检测每个name属性，以确定传人的'名称是 否出现在字符串的某个地方。比较的字符串都使用小写形式可以避免因大小写不一致导致的错误。而传 人的参数应该尽可能具体，以避免混淆。应该说，像Flash和QuickTime这样的字符串就比较具体了, 不容易导致混淆。在Firefox、Safari、Opera和Chrome中可以使用这种方法来检测插件。

C^\    每个插件对象本身也是一个MimeType对象的数组，这些对象可以通过方括号语

法来访问。每个MimeType对象有4个展性：包含MIME类型描述的description、

招插件对象的enabledPlugin、表示与MIME类型对应的文件扩展名的字符串 suffixes (以逗号分陆)和表示完整MIME类型字符串的type。

检测IE中的插件比较麻烦，因为IE不支持Netscape式的插件。在IE中检测插件的唯一方式就是 使用专有的并尝试创建一个特定插件的实例。IE是以COM对象的方式实现插 件的，而COM对象使用唯一标识符来标识。因此，要想检査特定的插件，就必须知道其COM标识符。 例如，Flash的标识符是ShockwaveFlash. ShockwaveFlash。知道唯一标识符之后，就可以编写类似 下面的函数来检测IE中是否安装相应插件了。

//检測1E中的插件

function hasIEPlugin(name){ try {

new Act iveXObj ect(name)； return true；

} catch {ex){

return false；

}

//检测 Flash

alert(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"))；

"检測 QuickTime

alert(hasIEPlugin(*QuickTime.QuickTime*});

PluginDetectionExample02. htm

在这个例子中，函数hasIEPluginO只接收一个COM标识符作为参数。在函数内部，首先会尝 试创建一个COM对象的实例。之所以要在try-catch语句中迸行实例化，是因为创建未知COM对象 会导致拋出错误。这样，如果实例化成功，则函数返囲true;否则，如果抛出了错误，则执行catch 块,结果就会返回false。例子最后捡测IE中是否安装了 Flash和QuickTime插件。

鉴丁检测这两种插件的方法差别太大，冈此典型的做法是针对每个插件分别创建检测函数，而不是 使用前而介绍的通用检测方法。来看下面的例子。

//检测所有洌览》中的Flash

function hasFlash(}{

var result = hasPluginC"Flash")； if {!result){

result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash")；

return result;

)

/ /检測所有洌览器中的QuickTime

function hasQuickTime(){

var result = hasPlugin("QuickTime"): if (!result){

result = hasIEPlugin("QuickTime.QuickTime")；

}

return result；

//检測 Flash alert(hasFlash{))；

//检利 QuickTime alert(hasQuickTime());

PluginDetectionExample03. htm

上面代码中定义了两个函数：hasFlashO和iiasQuickTitneO。每个函数都是先尝试使用不针对 IE的插件检测方法。如果返回了 false (在IE中会这样)，那么再使用针对1E的插件检测方法。如果 IE的插件检测方法再返回false,则整个方法也将返M false。只要任何一次检测返回true,整个方 法都会返回true。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-41.jpg)

 

plugins集合有一个名叫refresh!)的方法，用于刷新plugins以反映最新安 装的插件。这个方法接收一个参数：表示是否应该重新加载页面的一个布尔值。如果 将这个值设置为true,则会重■新加栽包含插件的所有页面；否则，只更新plugins 集合，不重新加载页面。

8.3.2注册处理程序

Firefox 2 为 navigator 对象新增了 registerContentHandler ()和 registerProtocolHandler ()方 法(这两个方法是在HTML5中定义的，相关内容将在第22章讨论)。这两个方法可以让_个站点指明 它可以处理特定类型的信息。随着RSS阅读器和在线电子邮件程序的兴起，注册处理程序就为像使用桌 面应用程序一样默认使用这些在线应用程序提供了一种方式。

K-中，registerContentHandler (＞方法接收三个参数：耍处理的MIME类逝、可以处理该MIME 类型的页面的URL以及应用程序的名称。举个例子，要将一个站点注册为处理RSS源的处理程序，可 以使用如卞代码。

navigator.registerContentHandler("application/rss+xml",

■http://www.somereader.com?feed=%s", "Some Reader”；

第个参数是RSS源的MIME类型。第二个参数是应该接收RSS源URL的URL,其中的％S表示 RSS源URL,由浏览器自动插人。当下一次请求RSS源时，浏览器就会打开指定的URL,而相应的 Web应用程序将以适当方式来处理该请求。

Firefox 4及之前版本只允许在registerContentHandler（）方法中使用三个 MIME 类application/rss+xml、application/atom+xml 和 application/ vnd.mozilia .maybe. feed。这三个MIME类型的作用都一样，即为RSS或ATOM 新闻源（feed）注册处理程序。1

类似的调用方式也适用于registerProtocolHandler ｛）方法，它也接收三个参数：要处理的协 议（例如，rnailto或ftp）、处理该协议的页面的URL和应用程序的名称。例如，要想将一个应用程 序注册为默认的邮件客户端，可以使用如下代码。

navigator.registerProtocolHandler（"mailto",

"http: //www. somemailclient .com?cmd=%s,r, "Some Mail Client"）；

这个例子注册了~个mailto协议的处理程序，该程序指向一^基于Web的电子邮件客户端。同样, 第二个参数仍然是处理相应请求的URL,而%8则表示原始的请求。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-42.jpg)

 

Firefox 2虽然实现了 registerProtocolHandler!）,但该方法还不能用。 Firefox 3完整实现这个方法。

8.4 screen 对象

JavaScript屮有几个对象在编程中用处不大，而screen对象就是其中之--。screen对象基本上只 用来表明客户端的能力，其中包括浏览器窗U外部的显示器的信息，如像素宽度和高度等。毎个浏览器 屮的screen对象都包含着各不相同的属性，下表列出了所有屈性及支持相应属性的浏览器。

| 属    性              | 说    明                                     | IE   | Firefox | Safari/Chrome | Opera |
| --------------------- | -------------------------------------------- | ---- | ------- | ------------- | ----- |
| availHeight           | 屏幕的像素高度减系统部件商度之后的值（只读） | V    |         | V             |       |
| availLeft             | 未被系统部件占用的最左侧的像素值（只读）     |      |         |               |       |
| availTop              | 未被系统部件占用的嚴上方的像素值（只读）     |      |         | V             |       |
| availWidt'n           | 屏幕的像素宽度减系统部件宽度之后的值（只读） |      |         | V             | V     |
| bufferDepth           | 读、写用于呈现屏外位阁的位数                 |      |         |               |       |
| colorDepth            | 用于表现颜色的位数；多数系统都是32（只读）   | V    |         | V             | V     |
| deviceXDPI            | 屏幕实际的水平DPI （只读）                   | >/   |         |               |       |
| aeviceYDPI            | 屏蒋实际的垂莨DPI （只读）                   |      |         |               |       |
| fontSmooth-ingEnabled | 表示是否启用了字体平滑（只读）               |      |         |               |       |
| height                | 屏菥的像索髙度                               |      |         |               |       |
| left                  | 当前屏幕距左边的像素距离                     |      | V       |               |       |
| logicalXDPI           | 屏幕逻辑的水平DPI （只渎）                   |      |         |               |       |
| logicalYDPi           | 屏幕逻辑的垂直DPI （只读）                   |      |         |               |       |
| pixelDepth            | 屏幕的位深（只读）                           |      |         |               |       |

(续)

| 属    性       | 说    明                           | 旧   | Firefox | Safari/Chrome | Opera |
| -------------- | ---------------------------------- | ---- | ------- | ------------- | ----- |
| top            | 当前屏幕距h边的像素距离            |      |         |               |       |
| updatelnterval | 读、写以毫秒表示的屏幕刷新吋间同隔 | V    |         |               |       |
| width          | 屏菘的像素宽度                     |      |         | >1            | V     |

这些信息经常集中出现在测定客户端能力的站点踉踪工具中，似通常不会用于影响功能。不过，有 时候也可能会用到其中的信息来调整浏览器窗口大小，使其占据屏幕的可用空间，例如：

window.resizeTo(screen.availWidth, screen.availHeight)；

前面曾经提到过，许多浏览器都会禁用调整浏览器窗口大小的能力，因此L面这行代码不一定在所 有坏境下都有效。

涉及移动设备的屏幕大小时，情况有点不一样。运行iOS的设备始终会像是把设备竖着拿在手里一 样》因此返回的值是768 x 1024。而Android设备则会相应调用screen.width和screen.height的值。

8.5 history 对象

history对象保存着用户上网的历史记录，从窗口被打开的那一刻算起„因为history是window 对象的属性，因此每个浏览器窗口、每个标签页乃至每个框架，都有自己的history对象与特定的 window对象关联。出于安全方面的考虑，开发人员无法得知用户浏览过的URL。不过，借由用户访问 过的页面列表，同样可以在不知道实际URL的情况下实现沿退和前进。

使用goU方法可以在用户的历史记录中任意跳转，可以向后也可以向前。这个方法接受一个参数， 表示向后或向前跳转的页面数的一个整数值。负数表示向后跳转(类似于单击浏览器的“后退”按钮), 正数表示向前跳转(类似于单击浏览器的“前进"按钮)。来看下面的例子。

//后退一页 history.go(-1)?

//前进一页 history .go ⑴；

//前进两页 history.go(2)；

也可以给goU方法传递一个字符串参数，此时浏览器会跳转到历史记录中包含该字符串的第一个 位置一^可能后退，也可能前进，具体要看哪个位置最近。如果历史记录中不®含该字符串，那么这个 方法什么也不做，例如：

//跳抟到最近的wrox.com页面 hi story.go <"wrox.com");

//跳转到最近的nczonline.net页面 history.go("nczonline.net"}? *

另夕卜，还可以使用两个简写方法back{)和forward()来代替go()。顾名思义，这两个方法可以 模仿浏览器的“后退”和“前进”按钮。

//后退一页 history.back();

//前进一■页

history.forward（）;

除了上述几个方法外，history对象还有一个length属性，保存着历史记录的数量。这个数量 包括所冇历史记录，即所有向后和向前的记录。对于加载到窗口、标签页或框架中的第一个页面而3, history, length等于0。通过像下面这样测试该属性的值，可以确定用户是否一开始就打开了你的 页面。

if （history.length == 0）{

//这应该是用户打开窗口后的第一个页面

}

虽然history并不常用，但在创建自定义的“后退”和"前进”按钮，以及检测当前页面是不是 用户历史记录中的第一个贞面时，还是必须使用它。

当页面的URL改变时，就会生成一条历史记录。在IE8及更高版本、Opera、

X-Z Firefox、Safari 3及更高版本以及Chrome中，这里所说的改变包括URL中hash的变

化（因此，设置local ion. hash会在这些測览器中生成一条新的历史记录）。

8.6小结

浏览器对象模型（BOM ）以window对象为依托，表示浏览器窗口以及页面可见区域„同时，window 对象还是ECMAScript中的Global对象，因而所有全局变量和函数都是它的属性，且所有原生的构造 函数及其他函数也都存在于它的命名空间下。本章讨论了下列BOM的组成部分。

□在使用框架时，每个框架都有自己的window对象以及所房原生构造函数及其他函数的副本。 毎个框架都保存在frames集合中，可以通过位置或通过名称来访问。

□有一些窗口指针，可以用来引用其他框架，包括父框架。

□    top对象始终指向最外围的框架，也就是整个浏览器窗口。

□    parent对象表示包含当前框架的框架，而self对象则回指window。

□使用location对象町以通过编程方式来访问浏览器的导航系统。设S相应的属性，可以逐段 或整体性地修改浏览器的URL。

□调用replace G方法可以导航到一个新URL,同时该URL会替换浏览器历史记录中当前显示 的贞面。

□    navigator对象提供了与浏览器有关的信息。到底提供哪典信息，很大程度上取决于用户的浏 览器；不过，也有一些公共的属性（如userAgent）存在于所有浏览器中。

BOM中还有两个对象：screen和history,但它们的功能有限。screen对象中保存着与客户端 显示器有关的信息，这些信息一般只用于站点分析。history对象为访问浏览器的历史记录开了一个 小缝隙.开发人员坷以据此判断历史记录的数量，也可以在历史记录中向后或向前导航到任意页面。