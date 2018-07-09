##### 第15*

使用Canvas绘图

本章内容

□理解＜canvas;＞元素

□绘制简单的2D图形

□使月I WebGL绘制3D图形

用说，HTML5添加的最受欢迎的功能就是＜canvaS＞元素。这个元素负责在页面中设定一个 域，然后就可以通过JavaScript动态地在这个区域中绘制图形。《抑时^元素最早是由苹

果公司推出的，当时主要用在其Dashboard微件中。很快，HTML5加人了这个元素，主流浏览器也迅 速开始支持它。IE9+、Firefoxl.5+、Safari 2+^ Opera 9+, Chrome、iOS 版 Safari 以及 Android 版 WebKit 都在某种程度h支持＜canvas＞。

与浏览器环境中的其他组件类似，＜canvaS＞由几组API构成，但并非所有浏览器都支持所有这些 API。除丁具备基本绘图能力的2D上下文，＜canvas＞还建议了一个名为WebGL的3D上下文。目前， 支持该元素的浏览器都支持2D上下文及文本API,但对WebGL的支持还不够好。由于WebGL还是实 验性的，因此要得到所有浏览器支持还需要很长一段时间。FirefcX4+和Chrome支持WebGL规范的早 期版本，但一些老版本的浏览器，比如Windows XP,由于缺少必要的绘图驱动程序，即便安装了这两 款浏览器也无济于事。

15.1基本用法

要使用＜CanvaS＞元索，必须先设置其width和height属性，指定可以绘图的区域大小。出现在 开始和结束标签中的内容是后备信息，如果浏览器不支持＜CanvaS＞元索，就会显示这些信息。下面就 是＜031^/^3＞元素的例子。

＜canvas id="drawing" widths" 200" height=B200"＞A drawing of something.＜/canvas＞

与其他元素一样，＜canvas＞元素对应的DOM元素对象也有width和height属性，可以随意修 改。而且，也能通过CSS为该元素添加样式，如果不添加任何样式或者不绘制任何图形，在页面中是看 不到该元素的。

要在这块画布（canvas）上绘图，需要取得绘图上下文。而取得绘图上下文对象的引用，需要调用 getContexU）方法并传人上下文的名字。传人-2d-,就可以取得2D上下文对象。

var drawing = document.getElementById("drawing")；

/ /磯定湖览器支持＜canvas＞元素

if (drawing.getContext){

var context = drawing.getContext{n 2d");

//更多代码

}

在使用＜canvaS＞元素之前，首先要检测getContext ()方法是否存在t这一步非常重要。有些浏 览器会为HTML规范之外的元索创迚默认的HTML元索对象在这种情况下，即使drawing变fl中 保存着一个有效的元素引用，也检测不到getContext ()方法。

使用toDataURLO方法，可以导出在＜CanvaS＞元素上绘制的图像、这个方法接受一个参数，即图 像的MIME类型格式，而且适合用于创建图像的任何上下文。比如，要取得画布中的一幅PNG格式的 图像，可以使用以下代码。

var drawing = document.getElemenCById{"drawing");

/ /确定浏览器支持＜canvas＞元者 if (drawing.getContext){

"取得图像的数据TOI

var imgURI « drawing.toDataORL("image/png")；

//显示图像

var image = document. createElement (" img11) J image.src x imgURI；

document.body.appendChiId(image);    •

}

2DjDataUrlExampleO 1 .htm

默认悄况下，浏览器会将图像编码为PNG格式(除非另行指定)。Firefox和Opera也支持基于 "image/jpeg”参数的JPEG编格式。由于这个方法是后来才追加的，所以支持＜031^^3＞的浏览器也 是在较新的版本中才加人了对它的支持，比如IE9、Firefox 3.5和Opera 10。

如果绘制到画布上的图像源自不同的城，toDataURLO方法会'抛出错误。本幸后 面还将介绍更多相关内容。

15.2 2D上下文

使用2D绘图上下文提供的方法，可以绘制简单的2D图形，比如矩形、弧线和路径。2D上下文的 坐标开始于＜canvas＞元索的左上角，原点坐标是(0,0)。所有坐标值都基于这个原点计算，X值越大表示 越靠右，值越大表示越靠下。默汄愔况下，width和height表示水平和垂直两个方向上可用的像素 数H。

15.2.1填充和描边

2D上下文的两种基本绘图操作是填充和描边。填充，就是用指定的样式(颜色、渐变或图像)填 充图形；描边，就是只在图形的边缘画线。大多数2D t下文操作都会细分为填充和描边两个操作，而

①假设你想在Firefox 3中使用＜canvas＞元索。虽然浏览器会为该标签创建一个DOM对象，而且也可以弓|用它，但 这个对象中并没有getContext 0方法。(据作者回S〉

操作的结果取决于两个属性：fillStyle和strokeStyle。

这两个属性的值可以是字符串、渐变对象或模式对象，而fi它们的默认值都是4000000。。如果为

它们指定表示颜色的字符串值，可以使用CSS中指定颜色值的任何格式，包括颜色名、十六进制码、 rgb、rgba、hsl 或 hsla。举个例子：

var drawing = document.getElementById{"drawing"）；

/ /确定湖览器支特＜canvas＞元素 if (drawing.getContext){

var context = drawing.getContext（"2d"）； context.strokeStyle = "red"； context.fillStyle = "#0000ff";

}

以上代码将strokeStyle没置为red （ CSS中的颜色名），将fillStyle设罝为#0000ff （蓝色）。 然后，所有涉及描边和填充的操作都将使用这两个样式，直至重新设置这两个值。如前所述，这两个属 性的值也可以是渐变对象或模式对象。本章后面会讨论这两种对象。

15.2.2绘制矩形

矩形是唯一一种可以直接在2D上下文中绘制的形状。与矩形有关的方法包括fillRectO, strokeRect （＞和clearRect （＞ o这三个方法都能接收4个参数：矩形的x坐标、矩形的少坐标、矩形 宽度和矩形髙度。这些参数的单位都是像素。

首先，fillRecU）方法在画布上绘制的矩形会填充指定的颜色。填充的颜色通过fillStyle属 性指定，比如：

var drawing = document.getElementById（"drawing"）;

/ /靖定浏览器支持＜canvas＞元索 if （drawing.getContext）{

var context = drawing.getContext("2d");

*根据Kozilla的文档

* [http://developer.mozilla.org/en/docs/Canvas—tutorial:Basic_usage](http://developer.mozilla.org/en/docs/Canvas%e2%80%94tutorial:Basic_usage)

//纷制红色錶形

context.fi UStyle » "#f£0000";

context.fi HRect(10, 10, 50, 50);

//纷制半进明的蓋色矩形

context ♦ fi UStyle « Nrgba(0,0,255,0.5) "/ context.fi llRect(30, 30, 50, 50);

•    2DFiURectExampleO 1. htm

以上代码首先将fillStyle设置为红色，然后从（10，10曲:开始绘制矩形，矩形的宽和离均为50像 素。然后，通过rgbaO格式再将fillStyle设置为半透明的蓝色，在第一个矩形上面绘制第二个矩 形。结果就是可以透过蓝色的矩形看到红色的矩形(见图15-1)。

strokeRectO方法在画布上绘制的矩形会使用指定的颜色描边。描边颜色通

过strokeStyle属性指定。比如：

var drawing = document.getElementById("drawing")；

/ /碗定測览器支持＜canvas＞元素 if (drawing.getContext){

var context = drawing.getContext("2d");

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-84.jpg)

 

图 15-1

 

/*

*根据Mozilla的文档

* <http://developer.mozilla.Org/en/docs/Canvas_tutorial:Basic_usage> */

"绘制红色描边矩形

context.strokeStyle « "#ff0000";

context.strokeRect(10, 10, 50, 50);

//焓制半透明的麄色描边矩形

context.strokeStyle ■ "rgba(0, 0,255, 0,5) ••/ context.strokeRect(30# 30, 50, 50);

2DStrokeRectExample01. htm

以上代码绘制了两个重叠的矩形。不过，这两个矩形都只有框线，内部并没有填充颜色(见图15-2)。

图 15-2

描边线条的宽度由Xinewidth属性控制，:该属性的值可以是任意整数a另外， 通过1 ineCap A性可以控麵钱傘麥棟的系状是平头、破奉率是方头bufctas "round**或**square"),通过lineJoin属性可以控制线条相交的方式是圆交、斜 交还是斜接("round11、wbevel **或    )。

 

最后，clearRecU)方法用于清除画布上的矩形区域。本质上，这个方法可以把绘制上下文中的某 一矩形区域变透明。通过绘制形状然后再清除指定区域，就可以生成有意思的效果，例如把某个形状切 掉一块。下面看一个例子。

var drawing = document.getElementById{"drawing");

/ /确定浏览器支持＜canvas＞元素

if (drawing.getcontext){

var context = drawing.getContext(■2d*)；

*根据Mozilla的文極

\* http：//developer.mozilla.org/en/docs/Canvas_tutorial:Basic_usage

//飨制红色矩形

context.fillStyle = "#ffOOOO";

context.fillRect(10, 10, 50, 50)；

//绘制半透明的蓝色矩形

context.fillStyle = "rgba(0,0,255,0.5)"； context.fillRect(30, 30, 50, 50) ?

//在两个任形重叠的地方清除一个小矩形 context.clearRect(40, 40, 10, 10)j

2DClearRectExample01 .htm

如图15-3所示，两个填充矩形重叠在一起，而重赉的地方又被清除了一个4、

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-86.jpg)

 

矩形区域。

15.2.3绘制路径

2D绘制上下文支持很多在画布上绘制路径的方法。通过路径可以创造出复

杂的形状和线条。要绘制路径，首先必须调用beginPathU方法，表示要开始    图15-3

绘制新路径。然后，再通过调用下列方法来实际地绘制路径。

□    arc (x, y, radius, startAngle, endAngle, counterclockwise):以(x,y> 为圆心给 制一条弧线，弧线半径为radius,起始和结束角度(用弧度表示)分别为startAngle和 endAngleo最后一个参数表示start Angle和endAngle是否按逆时针方向计算，值为false 表示按顺时针方向计算。

□    arcTo(xl, yl, x2, y2, radius):从上一点开始绘制一条弧线，到(x2,y2)为止，并且以 给定的半径radius穿过(xl,yl) 0

□    bezierCurveTo (clx, cly, c2x, c2y, x, y):从匕一点开始绘制一条曲线，劉(x,y)为 止，并且以(c2x, cly)和(c?2x, c2y)为控制点。

□    lineTo (x, y):从上一点开始绘制一条直线，到(x,y)为止。

□    moveTofx, y):将绘图游标移动到(x,y>,不画线。

□    quadraticCurvel'o(cx, cy, x, y)：从上一点开始绘制一条二次曲线，到(x,y》为止，并 且以(cx,^z>作为控制点。

□    rect (x, y, width, height):从点(x,y)开始绘制一个矩形，宽度和髙度分别由width和 height指定。这个方法绘制的是矩形路径，而不是strokeRect ()和fillRect ()所绘制的独 立的形状。

创建了路径后，接下来有几种可能的选择。如果想绘制一条连接到.路径起点的线条，可以调用 closePathO。如果路径已经完成，你想用fillStyle填充它，可以调用fill ()方法。另外，还可 以调用stroke (>方法对路径描边，描边使用的是strokeStyle。最后还可以调用clip(),这个方法 可以在路径上创建一个剪切区域。

下面看一个例子，即绘制一个不带数字的时钟表盘。

var drawing = document. getElenient3yId ("drawing");

//场定測览器支持〈canvas ＞元素

if (drawing.geCContext){

var context = drawing.getContext("2d");

✓ ✓开始路径

context. begizxPath () t

//给側外®

context.arc(100r 100, 99, 0, 2 * Math.PI, false)j

//始■制内®

context.moveTo(194, 100);

context.arc(100r 100, 94, 0, 2 * Math.PI# false)/

//綸制分针

context.moveTo(100/ 100); context.lineTo(100 / 15);

//纷剩时针

context.moveTo(100, 100》； context.lineTo(35, 100)；

"搞边路径 context.stroke() i

)

2DPathExample0 i. htm

这个例子使用arc<)方法绘制了两个圆形：一个外圆和一个内圆，构成了表盘的边框。外圆的半径 是99像素，圆心位于点(100,100),也是画布的中心点。为了绘制一个完整的圆形，我们从0弧度开始， 绘制2ji弧度(通过Math. PI来计算)。在绘制内圆之前，必须把路径移动到外圆上的某--点，以避免 绘制出多余的线条。第二次调用arc ()使用了小一点的半径，以便创造边框的效果。然后，组合使用 moveTo<)和lineTo()方法来绘制时针和分针。最后一步是调用stroke<)方法，这样才能把图形绘制 到画布上，如图154所示。

图 15-4

在2D绘圈上下文中，路径是一种主要的绘图方式，因为路径能为要绘制的图形提供更多控制。由 于路径的使用很频繁，所以就有了一个名为isPointlnPathU的方法。这个方法接收x和;r坐标作为 参数，用于在路径被关闭之前确定脚布上的某一点是否位于路径上，例如：

if {context.isPointInPath{100, 100)){

alert("Point (100, 100) is in the path.")；

}

2D上下文中的路径API已经非常稳定，可以利用它们结合不同的填充和描边样式，绘制出非常复 杂的图形来。

15.2.4绘制文本

文本与图形总是如影随形。为此，2D绘图上下文也提供了绘制文本的方法。绘制文本主要有两个 方法：fillText()和strokeTextG。这两个方法都可以接收4个参数：要绘制的文本字符串、*坐 标、y坐标和可选的最大像素宽度。而且，这两个方法都以下列3个属性为基础。

口 font：表示文本样式、大小及字体，用CSS中指定字体的格式来指定，例如《10px Arial%

口仁6又仁*1:1卯：表示文本对齐方式。可能的值有"3131；(:"、"end"、"left" x "right"和"center"。 建议使用-start”和’•《!<!•■，不要使用》left-和’’right'因为前两者的意思更稳妥，能同时 适合从左到右和从右到左显示(阅读)的语言。

□ textBaseline:表示文本的基线。可能的值有"top"、"hanging" , "middle11、"alphabetic" s ’ ideograph!c"和"bot tom"。

这几个属性都有默认值，因此没有必要每次使用它们都重新设置一遍值。fillTextO方法使用 fillStyle属性绘制文本，而strokeText ()方法使用strokeStyle属性为文本推边。相对来说，还 是使用fillText()的时候更多，因为该方法模仿了在网页中正常显示文本。例如，下面的代码在前一 节创建的表盘I:方绘制了数字12:

context.font = "bold 14px Arial"; context. textAlign =： "center" j context .textBaseline = "middle* •, context.fillText("12", 100, 20);

2D TextExampleO 1, htm

结果如图15-5所示。

图 15-5

因为这里把textAlign设置为"center",把textBaseline设置为"middle",所以坐标(100,20) 表示的是文本水平和垂直中点的坐标。如果将textAlign设置为”start-,则X坐标表示的是文本左 端的位置(从左到右阅读的语言)；设置为* end”，则;v坐标表示的是文本右端的位置(从左到右阅读的 语言)。例如：

//正常

context.font = "bold 14px Arial"; context.textAlign = "center"； context.textBaseline a "middle"; context.fillText<•12■, 100, 20);

//起点对齐

context.textAlign * "start"j cozxtext.fi llText ("12", 100, 40};

//终点对齐

context.textAlign 雪"end"; context.fi 1IText("12", 100, 60);

2DTextExampleO2. htm

这一问绘制了三个字符串• 12-,每个字符串的jc坐标值相同，但textAlign值不同。另外，后两 个字符串的JV坐标依次增大，以避免相互重叠。结果如图15-6所示。

表盘中的分针恰好位于正中间，因此文本的水平对齐方式如何变化也能够一B了然。类似地，修改 textBaseline属性的值可以调整文本的垂直对齐方式：值为y坐标表示文本顶端；值为 "bottom”，坐标表示文本底端；值为"hanging"、"alphabetic"和’ideographic”，则少坐标分 别指向字体的特定基线坐标。

由于绘制文本比较复杂，特别是需要把文本控制在某一区域中的时候，2D上下文提供了辅助确定 文本大小的方法measureText ()。这个方法接收•-个参数，即要绘制的文本；返冋一个TextMetrics 对象。返回的对象目前只有一个width属性，但将来还会增加更多度fi属性。

measureText ()方法利用font、textAlign和textBaseline的当前值贺指定文本的大小。 比如，假设你想在一个140像素宽的矩形区域中绘制文本Hello world!,下面的代码从100像素的字体 大小开始递减，最终会找到合适的字体大小。

var fontSize = 100;

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-90.jpg)

 

context.font = fontSize 4 "px Arial";

while(context.measureText("Hello world I")-width ＞ 140){ fontSize-星;

context.font = fontSize + "px Arial";

}

context.fillText("Hello world!", 10, 10) ?

context.fillText{"Font size is " + fontSize + "px", 10, 50);

2DTextExampleO3.htm

前面提到过，fillText和strokeText ()方法都可以接收第四个参数，

也就是文本的最大像素宽度。不过，这个可选的参数尚未得到所有浏览器支持    一 ]

(最争支持它的是Firefox 4 )□提供这个参数后，调用fillTextO或    宇体大小为26像素

strokeText ()时如果传人的字符串大于最大宽度，贝lj绘制的文本字符的髙度    图15-7

正确，但宽度会收缩以适应最大宽度。图15-7展示了这个效果。

绘制文本还是相对比较复杂的操作，因此支持＜(^1^33＞元索的浏览器也并未完全实现所有与绘制 文本相关的API。

15.2.5变换

通过上下文的变换，可以把处理后的图像绘制到画布上。2D绘制上下文支持各种基本的绘制变换。 创建绘制上下文时，会以默认值初始化变换矩阵，在默认的变换矩阵卞，所有处理都按描述直接绘制。 为绘制上下文应用变换，会导致使用不同的变换矩阵应用处理，从而产生不同的结果。

可以通过如下方法来修改变换矩阵。

□    rotate (angle):围绕原点旋转图像angle弧度o

□    scale(scaleX, scaleY):缩放网像，在方向乘以 scaleX，在y方向乘以 scaleYo scaleX 和scaleY的默认值都是1.0。

□    translate (x, y)：将坐标原点移动到(x,y)。执行这个变换之后，坐标(0,0)会变成之前由(xzy) 表示的点。

□    transform(ml_l, ml_2, m2_l, m2_2, dx, dy):直接修改变换矩阵，方式是乘以如下 矩阵。

□    setTrans form    , ml_2z m2_l, m2_2, dx, dy):将变换矩阵重置为獻认状态，然后

再调用 transform^ o

变换有可能很简单，但也可能很复杂，这都要视情况而定。比如，就拿前面例子中绘制表针来说， 如果把原点变换到忐盘的中心，然后再绘制表针就容易多了。请看下面的例子。

var drawing = document .getE lenient By Id ("drawing")；

//碗定浏览器支持＜canvas＞元素

if (drawing.getContext){

var context = drawing.getContext(■2d");

//开始路径

context.beginPath();

//绘制外B

context.arc(100, 100, 99, 0,2* Math.PI, false);

//绘制内®

context.moveTo(194, 100);

context.arc(100, 100, 94, 0,2* Math.PI, false)；

"变换原点

context.translate(100, 100)/

"給側分针

context.moveTo(0f 0>;

context.ltneTo(0, -85)j

//绔制时针

context.moveTo(0, 0);

context.lineTo(-65/ 0)/

//撗边路径

context.stroke <);

2DTransformExampieQl. htm

把原点变换到时钟表盘的中心点(100,100)后，在同一方向上绘制线条就变成了简单的数学问题了。 所有数学计算都基于(0,0)，而不是(100,100＞。还可以更进-步，像下面这样使用rotate(＞方法旋转时 钟的表针。

var drawing = document,getElementById("drawing"}；

//戏定測蹵器支持〈canvas >元者

if (drawing.getContext){

var context = drawing.getContext(*2d*);

//斤始路径

context.beginPath(J；

//绘制外困

context.arc(100, 100， 99, 0,2* Math.PI, false)?

"绘制内困

context.moveTo(194, 100);

context.arc(100, 100, 94, 0, 2 * Math.PI, false)；

//变换原点

context.translate(100, 100);

"旋转表针

context.rotate(1);

//绘制分针

context.moveTo(0,0); context.lineTo(0, -85);

//给制时针

context.moveTo(0, 0); context.lineTo(-65, 0)；

//槁边路径

context.stroke();

2DTransformExampleO 1. htm

因为原点已经变换到了时钟表盘的中心点，所以旋转也是以该点为圆心的。结果就像是表针真地被 固定在表盘中心一样，然后向右旋转了一定角度。结果如图15-8所示。

无论是刚才执行的变换，还是fillStyle, strokeStyle等属性，都会在当前上下文中一直有效, 除非再对上下文进行什么修改。虽然没有什么办法把上下文中的一切都重置网默认值，但有两个方法可 以跟踪上下文的状态变化。如果你知道将来还要返冋某组属性与变换的组合，可以调用Save()方法。 调用这个方法后，当时的所有设置都会进人一个栈结构，得以妥善保管。然后可以对上下文进行其他修 改。等想要回到之前保存的设置时，可以调用restored方法，在保存设置的桟结构中向前返回一级， 恢复之前的状态。连续调用save()可以把更多设置保存到栈结构中，之后再连续调用restored则可 以一级•-级返回。下面来看一个例子。

context.fillStyle = "#ffOOOO"； i    context.save()；

context.fillStyle = "#00£f00*; context.translate(100 f 100); context.save()；

context.fillStyle = "#0000ff•;

context. fillRectCO, 0, 100, 200);"从点(100,100)开始绘制蓝色矩形

context.restore();

context. fillRect {10，10，100，200》；//从点(110,110)开始绘剩綵色矩形 context.restore()；

context. EillRecr.(O, 0, 100, 200) ; //从点(0,0)开始绘制红色矩形

2DSaveResloreExampleO l.htm

首先，将fillStyle设置为红色，并调用save ()保存上下文状态。接下来，把fillStyle修改 为绿色，把坐标原点变换到(100，100),再调用save ()保存上下文状态=然后，把fillStyle修改为蓝 色并绘制蓝色的矩形。因为此时的坐标原点已经变了，所以矩形的左上角坐标实际上是(100,100)。然后 调用restored,之后fillStyle变回了绿色，因而第二个矩形就是绿色。之所以第二个矩形的起点 坐标是(110,110)，是因为坐标位置的变换仍然起作用。再调用一次restoreO,变换就被取消了，而 fillStyle也返回了红色。所以最后一个矩形是红色的，而且绘制的起点是(0,0)。

需要注意的是，save ()方法保存的只是对绘图上下文的设置和变换，不会保存绘图上下文的 内容。

15.2.6绘制图像

2D绘图上下文内置了对图像的支持。如果你想把一幅阁像绘制到画布上，可以使用drawImageO 方法。根据期望的最终结果不同，调用这个方法时，可以使用三种不同的参数组合。最简单的调用方式 是传人-个HTML<img>元素，以及绘制该图像的起点的jc和y坐标。例如：

var image = document.images[0];

\    context.drawlmage(image, 10, 10);

2DDrawlmageExample0l.htm

这两行代码取得了文档中的第一幅图像，然后将它绘制到上下文中，起点为(10,10)。绘制到画布上 的图像大小与原始大小一样。如果你想改变绘制后图像的大小，可以再多传人两个参数，分別表示目标 宽度和目标髙度。通过这种方式来缩放團像并不影响上下文的变换矩阵=例如：

context.draw工mage(image, 50, 10, 20, 30);

2DDrawImageExampleOJ.htm

执行代码后，绘制出来的图像大小会变成20x30像素。

除了上述两种方式，还可以选择把图像中的某个区域绘制到上下文巾。drawImageO方法的这种调 用方式总共需要传人9个参数：要绘制的團像、源图像的;v坐标、源图像的y坐标、源图像的宽度、源 图像的高度、目标图像的x坐标、目标图像的y坐标、目标图像的宽庞、目标图像的髙度。这样调用 drawlmage ()方法可以获得最多的控制a例如：

context.drawlmage(imagez 0, 10, 50, 50, 0, 100, 40, 60);

2 DDrawImageExampleO l.htm

这行代码只会把原始阁像的一部分绘制到画布上。原始图像的这一部分的起点为(0,10),宽和髙都 是50像索。最终绘制到上下文中的困像的起点是(0,100),而大小变成了 40x60像素。

这种调用方式可以创造出很有意思的效果，如阁15-9所示。

# fc

图 15-9

除了给drawlmage( ＞方法传人HTML ＜11[19＞元素外，还可以传人另一个＜canvass＜^E素作为其第一 个参数。这样，就可以把另_个阃布内容绘制到当前画布上。

结合使用drawImageG和其他方法，可以对图像进行各种基本操作。而操作的结果可以通过 toDataURLU方法获得®。不过，有一个例外，即图像不能来自其他域。如果图像来自其他域，调用 toDataURiU)会抛出一个错误。打个比方，假如位于www.example.com上的页面绘制的图像来自于 www.wrox.com,那当前上下文就会被认为“不干净”，因而会抛出错误。

15.2.7阴影

2D上下文会根据以下几个厲性的值，自动为形状或路径绘制出阴影。

□    shadowColor：用CSS颜色格式表示的阴影顏色，默认为黑色。

□    shadowOffsetX：形状或路径x轴方向的阴影偏移量，默认为0。

□    shadowOf fsetY:形状或路径^轴方向的阴影偏移量，默认为0。

□    shadowBlur:模糊的像素数，默认0,即不模糊。

这些属性都可以通过context对象来修改。只要在绘制前为它们设置适当的值，就能自动产生阴 影。例如：

var context = drawing.getContext(w2dw);

/✓设置阴影

contoxt.shadowOffsetX « 5;

context.shadowOf£aetY - 5;

context.8hado«Blur    - 4；

context.shadowColor    » "rgba(0, 0, 0, 0.5)";

//馀制红色矩形

context.fillStyle = "#ffOOOO”；

context.fillRect(10, 10z 50, 50);

//绘制筮色矩形

context. fillStyle = -rgba (0,0,255,1)⑽'•

①请读者注意，虽然本章至今一直在讨论2D绘阁上下文，但toDataURL()是Canvas对象的方法，不是上下文对 象的方法。

context.fillRect(30, 30, 50, 50);

2DEillRectShado^Example01. htm

两个矩形的阴影样式相同，结果如图15-10所示。

图 15-10

 

不同浏览器对阴影的支持有一些差异。IE9、Firefox 4和Opera 11的行为

最为规范，其他浏览器多多少少会有一些奇怪的现象，其至根本不支持阴影。 Chrome (直至第10版)不能正确地为描边的形状应用实心阴影。Chrome和 Safari (直至第5版)在为带透明像索的图像应用阴影时也会有问题：不透明 部分的下方本来是该有阴影的，但此时则一概不见了。Safari也不能给渐变图 形应用阴影，其他浏览器都可以。

15.2.8渐变

渐变由CanvasGradient实例表示，很容易通过2D上下文来创建和修改。要创建一个新的线性渐

可以调用createLinearGradientU方法。这个方法接收4个参数：起点的jc坐标、起点的y坐 标、终点的坐标、终点的y坐标。调用这个方法后，它就会创建一个指定大小的渐变，并返回 CanvasGradient对象的实例。

创建丫渐变对象诉，下一步就是使用addColorStopO方法来指定色标。这个方法接收两个参数： 色标位置和CSS颜色值。色标位置是一个0 (开始的颜色)到1 (结束的颜色)之间的数字。例如：

J var gradient = context.createLinearGradienC(30, 30, 70, 70);

gradient.addColorStop(0, "white"); gradient.addColorStop(1, "black");

2DFillRectGradientExample01.htm

此时，gradient对象衣示的是一个从岡布上点(30,30)到点(70,70)的渐变。起点的色标是白色，终 点的色标是黑色。然沿就可以把fillStyle或strokeStyle设置为这个对象，从而使用渐变来绘制 形状或描边：

//烩制红色矩形

context.fillStyle = "#ff0000 •; context.fillRect(10, 10, 50, 50);

//绘制渐变矩形

context.fillStyle = gradient;

context.fillRect(30, 30, 50, 50);

2DFUlRectGradientExample01. htm

 

为了让渐变覆盖整个矩形，而不是仅应用到矩形的一部分，矩形和渐变对 象的坐标必须匹配才行。以上代码会得到如图15-11所示的结果。

图 15-J]

 

如果没有把矩形绘制到恰当的位置，那可能就只会显示部分渐变效果。 例如：

context.fillStyle = gradient;

context.fillRect(50, 50, 50, 50);

2DFiURectGradientExampleO2.htm

这两行代执行后得到的矩形只有左上角稍微有-•点白色。这主要是因为矩形的起点位于渐变的中 间位置，而此吋渐变差不多已经结束了。由于渐变不重复，所以矩形的大部分区域都是黑色。确保渐变 与形状对齐非常®要，有时候可以考虎使用娥数来确保坐标合适。例如：

function createRectLinearGradient (context# x., y, width, height) { return context.createLinearGradient(x, yt x+widthf y+height);

}

2DFillRectGradientExampleO3.htm

这个函数基于起点的x和义坐标以及宽度和髙度值来创建渐变对象，从而让我们可以在fillRect () 中使用相同的值。

var gradient = createRectLinearGradient(contextr 30, 30, 50, 50);

gradient.addColorStop(0z nwhite"); gradient.addColorStop(1y "black")；

//绘釗漸变矩形

context.£i UStyle = gradient; context.fillRect{30, 30« 50, 50);

2DFillRectGradientExampleO3,htm

使用画布的时候，确保坐标卩1i配很煮要，也需要一S技巧。类似createRectLinearGradient (} 这样的辅助方法nr以让控制坐标更容易一些。

要创建径向渐变(或放射渐变)，可以使用createRadialGradient ()方法。这个方法接收6个参 数，对应着两个脚的阐心和半径。前三个参数指定的是起点圆的原心(x和y)及半径，后三个参数指 定的是终点阏的原心U和JV)及半径。可以把径向渐变想象成-个长圆桶，而这6个参数定义的正是 这个柚的两个圆形开口的位置。如果把一个圆形开口定义得比另一个小一些，那这个岡桶就变成了圆锥 体，而通过移动每个圆形开口的位置，就可达到像旋转这个岡锥体一样的效果。

如果想从某个形状的中心点开始创建一个向外扩散的径向渐变效果，就要将两个圆定义为同心圆。 比如，就幸前面创建的矩形来说，径向渐变的两个圆的岡心都应该在(55,55)，因为矩形的区域是从(30,30) 到(80,80)。请看代码：

var gradient = context.createRadialGradient(55, 55, 10, 55, 55, 30)；

gradient.addColorStop(0, "white"); gradient.addColorStop{1, Kblack");

//绘制红色矩形

context.fillStyle = "#ff0000"； context.fillRect(10, 10, 50, 50);

//绘制渐变矩形

context.fillStyle = gradient; context.fillRect{30, 30, 50, 50);

2DFillRectGradientExampleO4.htm

运行代码，会得到如图15-12所示的结果。

图 15-12

 

因为创建比较麻烦，所以径向渐变并不那么容易控制„不过，一般来说， 让起点圆和终点圆保持为同心圆的情况比较多，这时候只要考虑给两个圆设置 不同的半径就好了。

15.2.9模式

模式其实就是重复的图像，可以用來填充或描边图形。要创建一个新模式，可以调用 createPattern ()方法并传人两个参数：一个HTML ＜1呵＞元素和一个表示如何重复图像的字符串。 某中，第二个参数的值与CSS的background-repeat属性值相同，包括"repeat"、"repeat-x"、

"repeat-y"和"no-repeat"o 看--个例子。

var image = document.images[0],

}    pattern s context.createPattern(image, "repeat");

//绘制矩形

context.fillStyle = pattern； context.fillRect(10 r 10, 150, 150);

2DFiHRectPatternExampleO 1. htm

需要注意的是，模式与渐变一样，都足从画布的原点(0,0)开始的。将填充样式(fillStyle)设置 为模式对象，只表示在某个特定的区域内显示重复的图像，而不是要从某个位置开始绘制重复的图像。 上面的代B会得到如图15-13所示的结果。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-95.jpg)

 

图 15-13

createPattern ()方法的第一个参数也可以是一个＜video＞元素，或者另一个＜canvas＞元索。

15.2.10使用图像数据

2D上下文的一个明i的长处就是，可以通过getlmageDatM)取得原始图像数据。这个方法接收 4个参数：要取得其数据的晡而区域的*和y坐标以及该区域的像素宽度和髙度。例如，要取得左上角 坐标为(10,5)、大小为50x50像素的区域的图像数据，可以使用以下代码：

var imageData = context.getlmageDatadO, 5, 50, 50);

这S返回的对象是ImageData的实例。每个ImageData对象都有三个属性：width、height和 data。其中data属性是•个数组，保存着图像中毎一个像素的数据。在data数组中，每一个像素用 4个元素来保存，分别衷示红、绿、蓝和透明度值。因此，第-个像索的数据就保存在数组的第0到第 3个元素中，例如：

var data = imageData.datar red = data[O]/ green = data[1], blue = daca [2], alpha = data[3];

数组中每个元素的值都介于0到255之间（包括0和255 ）。能够直接访问到原始图像数据，就能够 以各种方成来操作这些数据。例如，通过修改图像数据，可以像下面这样创建一个简单的灰阶过滤器。

var drawing - document.getElementById{"drawing")；

//场定浏览器支持＜canvas＞元素

if (drawing.getContext){

var context = drawing.getContext("2d")# image = document - images[0], imageData, data,

i, len, averagez

red, green, blue, alpha；

//绘制原始图像

context.drawlmagc(image, 0, 0);

//取得图像数据

imageData = context.getImageData(0, 0, image.width, image.height); data = imageData.data；

for (i-0, len^data.length; i ＜ len； i+=4){

red = data[i]； green = data[i+lj; blue = data[i+2]; alpha = data(i+3];

//求得rgb平均值

average = Math.floor((red + green + blue) / 3)；

//设置颜色值，透明度不变

data[i] = average；

data[i+1] = average；

data[i+2] = average；

//回写图像数据并显示结果

imageData.data = data；

context.putlmageData(imageData, 0, 0);

2DImageDataExample01 • hfm

这个例子首先在画面上绘制了一幅图像，然后取得了原始图像数据。其中的for循环適历了图像数 据中的每一个像素。这里要注意的是，每次循环控制变量i都递增4。在取得每个像素的红、绿、蓝颜

色值后，i卜算出它们的平均值。再把这个平均值没货为毎个颜色的值，结果就是去棹了每个像素的颜色, 只保留了亮度接近的灰度值（即彩色变黑白）。在把data数组回写到imageData对象后，调用 putiroageDataO方法把图像数据绘制到画布上。最终得到了图像的黑白版。

当然，通过操作原始像索值不仅能实现灰阶过滤，还能实现其他功能。.要了解通过操作原始图像数 据实现过滤器的更多信息，请参考Ilmari Heikkinen的文章“Making Image Filters with Canvas”（基于 Canvas 的阁像过滤器）：<http://www.html5rocks.eom/en/tutorials/canvas/imagefilters/o>

只有在画布“干净”的情况下（即图像并非来自其他域），才可以取得图像数据。 如果画布“不干净”，那么访问图像数据时会导致JavaScript错误。

15.2.11 合成

还有两个会应用到2D上下文中所有绘制操作的属性：global Alpha和globalComposition-Operation。其中，global Alpha是一个介于0和1之间的值（包括0和1 ）,用于指定所有绘制的透 明度。默认值为0。如果所有后续操作都要基于相同的透明度，就可以先把globalAlpha设置为适当 值，然后绘制，最后再把它设置回默认值0。下面来看，，个例子。

//絵制红色矩形

S context. fillStyle = M#ffOOOO,';

context.fillRect（10, 10, 50, 50）;

//修改全局进明度

context.globalAlpha = 0.5;

//绘制蓝色矩形

context. fillStyle = ,frgba(0,0,255( 1) ■; context.fillRect{30, 30, 50, 50);

//重置全局透明度

context.globalAlpha = 0；

2DGlobalA IphaExampleOl. htm

在这个例子中，我们把蓝色矩形绘制到了红色矩形上面。因为在绘制蓝色矩形前，globalAlpha 已经被设置为0.5,所以蓝色矩形会呈现半透明效果，透过它可以看到下面的红色矩形。

第二个厲性globalCompositionOperation表示后绘制的图形;S样与先绘制的图形结合。这个 属性的值是字符串，可能的值如下。

□    source-over （默认值）：后绘制的图形位于先绘制的阁形上方。

□    source-in：后绘制的图形与先绘制的图形電叠的部分可见，两者其他部分完全透明。

□    source-out:后绘制的图形与先绘制的图形不重叠的部分可见，先绘制的图形完全透明。

□    source-atop：后绘制的图形与先绘制的阁形承養的部分可见，先绘制图形不受影响c

□    destination-over:后绘制的图形位于先绘制的图形下方，只有之前透明像索卜'的部分才可见。

□    destination-in：后绘制的图形位于先绘制的阁形下方，两者不重叠的部分完全透明。

□    destination-out：后绘制的图形擦除与先绘制的图形重叠的部分。

□    destination-atop：后绘制的图形位于先绘制的图形下方，在两者不重叠的地方，先绘制的

图形会变透明。

□    lighter：后绘制的图形与先绘制的图形重释部分的值相加，使该部分变亮。

□    copy：后绘制的图形完全替代与之電叠的先绘制图形。

□    xor：后绘制的图形与先绘制的图形策释的部分执行“异或”操作。

这个合成操作实际上用语言或者黑图像是很难说清楚的。要了解每个操作的具体效果，请参见 <https://developer.mozilla.org/samples/canvas-tutorial/6_l_canvas_composite.htmlo> 推荐使用 IE9+或 Firefox 4+访问前面的网页，因为这两款浏览器对Cairns的£现最完_善。下面來看一个例子。

//绘制红色矩形

context.fillStyle = n#ff0000"; context.fillRect(10z 10， 50, 50);

//设置合成操作

context.globalCompoBiteOperation = Mdestination-over"；

//绘制蓝色矩形

context.fillStyle = "rgba(0,0,255,1)"； context.fillRect(30z 30, 50, 50);

2DGbbalCompositeOperationExample01 .htm

如果不修改globalCowpositionOperation ,那么蓝色矩形应该位于红色矩形之上D但把 globalCompositionOperation设S为"destination-over"之后，红色矩形跑到了蓝色矩形上面。

在使用globalCompositionOperation的情况下，一定要麥测试一些浏览器。因为不同浏览器 对这个属性的实现仍然存在较大的差别。Safari和Chrome在这方面还有问题，至于有什么问题，大家 可以比较在打开上述页面的情况下，IE9+和Firefox 4十与它们有什么差异。

15.3 WebGL

WebGL是针对Canvas的3D上下文。与其他Web技术不同，WebGL并不是W3C制定的标准，而 是由Khronos Group制定的。M官方网站是达样介绍的：“Khronos Group是一-个非盈利的由会员资助的 协会，专注于为并行计算以及各种平台和设备t的图形及动态媒体制定无版税的开放标准。” Khronos Group也设计了其他图形处理API,比如OpenGLES2.0。浏览器中使用的WebGL就是基于OpenGLES 2.0制定的。

OpenGL等3D困形语言是非常复杂的，本书不可能介绍其中每一个概念。熟悉OpenGLES 2.0的读 者可能会觉得WebGL更好理解一些，因为好多概念是相通的。

本节将适当地介绍OpenGL ES 2.0的一些概念，尽力解释其中的某些部分在WebGL中的实现。要 全面了解 OpenGL，请访问 www.opengl.org。要全面学习 WebGL,请参考 www.leamingwebgi.com,其 中包含非常棒的系列教程®。

15.3.1类型化数组

WebGL涉及的复杂计算需要提前知道数值的精度，而标准的JavaScript数值无法满足需要。为此,

WebGL引人了一个概念，叫类型化数组(typedarrays)。类型化数组也是数组，只不过其元索被设置为 特定类型的值。

类型化数组的核心就是一个名为ArrayBuffer的类聖。每个ArrayBuffer对象表祐的只是内存 中指定的字节数，但不会指定这些字节用于保存什么类型的数据=通过ArrayBuffer所能做的，就是 为了将来使用而分配一定数景的字节。例如，下面这行代码会在内存中分配20B。

var buffer = new ArrayBuffer(20)?

创建了 ArrayBuffer对象后，能够通过该对象获得的信息只有它包含的字节数，方法是访问其 byteLength 属性：

var bytes - buffer.byteLength;

虽然ArrayBuffer对象本身没有多少可说的，但对WebGL而言，使用它是极其S要的。而且， 在涉及视图的时候，你才会发现它原来还是很有意思的。

1.视图

使用ArrayBuffer (数组缓冲器类型)的一种特别的方式就是用它来创建数组缓冲器视图。其中， 最常见的视阁是DataView,通过它可以选择ArrayBuffer中一小段字节。为此，可以在创建DataView 实例的时候传人一个ArrayBuffer, 一个可选的字节偏移量(从该字节开始选择)和一个可选的要选 择的字节数。例如：

//基于整个緩冲器创建一个新视图

var view = new DataView(buffer);

//创建一个开始于字节9的新视图

var view = new DataView(buffer, 9);

//创建一个从字节9开始到字节18的新视图 var view = new DataView(buffer, 9, 10);

实例化之后，DataView对象会把字节偏移量以及字节长度信息分别保存在byteoffset和 byteLength 屈性巾o

alert(view.byteOffset｝； alert(view.byteLength)；

通过这两个属性可以在以后方便地丫解视图的状态。另外，通过其buffer属性也可以取得数组缓 冲器。

读取和写人DataView的时候，要根据实际操作的数据类型，选择相应的getter和setter方法。下 表列出了 DataView支持的数据类型以及相应的读写方法。

| 数据类型       | getter                             | setter                                     |
| -------------- | ---------------------------------- | ------------------------------------------ |
| 宥符号8位整数  | getlnt8(byteOffset)                | setlnt8{byteOffset, value)                 |
| 无符号8位整数  | getUint8(byteOffset)               | setuint8(byteOffset, value)                |
| 有符号16位整数 | getInti6(byteOffset,XittleEndian)  | setlntl6(byteOffset, value,littleEndian)   |
| 无符号16位整数 | getUintl6(byteOffset,littleEndian) | setuintl6(byteOffset,value, littleEndian.) |
| 冇符号32位整数 | getlnt32(byteOffset,littleEndian)  | setlnt32(byteOffset, value,littleEndian)   |

|                |                                     | (续)                                       |
| -------------- | ----------------------------------- | ------------------------------------------ |
| 数据类型       | getter                              | setter                                     |
| 无符号32位整数 | getUint32(byteOffset,littleEndian)  | setuint32(byteOffset,value, littleEndian)  |
| 32位浮点数     | getFloat32(byteOffset,littleEndian) | setFloat32(byteOffset,value, littleEndian) |
| 64位浮点数     | getFloat64(byteOffset,littleEndian) | setFloat64(byteOffset,value, littleEndian) |

所有这些方法的第一参数都是一个字节偏移量，表示要从哪个字节开始读取或写人。不要忘了， 要保存有些数据类型的数据，可能需要不止1B。比如，无符号8位整数要用1B,而32位浮点数则要用 4B。使用DataView,就需要你Q己来管理这些细节，即要明确知道自己的数据需要多少字节，并选择 正确的读写方法。例如：

var buffer = new ArrayBuffer(20)#

|    view = new DataView{buffer),

value；

view.setUintl6{Qt 25);

view.setUintl6(2r 50) ； //不能从字节1开始，因为16位整数要用2B value = view.getuintl6(0)；

Data ViewExampleOI.htm

以上代码把两个无符号16位整数保存到了数组缓冲器中。因为每个16位整数要用2B,所以保存 第一个数的字节偏移量为0,而保存第二个数的字节偏移量为2。

用于读写16位或更大数值的方法都有一个可选的参数littleEndiano这个参数是一个布尔值， 表示渎写数值时是否采用小端字节序(即将数据的最低有效位保存在低内存地址中)，而不是大端字节 序(即将数据的最低有效位保存在髙内存地址中)。如果你也不确定应该使用哪种字节序，那不用管它， 就采用默认的大端字节序方式保存即可。

因为在这里使用的是字节偏移量，而非数组元素数,所以可以通过几种不同的方式来访问同一字节。 例如：

var buffer = new ArrayBuffer(20),

I    view = new DataView(buffer),

value；

view.setUintl6{0, 25)； value = view.getlnt8(0)；

alert(value); //0

Data ViewExampleO2. htm

在这个例子中，数值25以16位无符号整数的形式被写人，字节偏移量为0。然后，再以8位有符 号整数的方式读取该数据，得到的结果是0。这是因为25的二进制形式的前8位(第一个字节)全部是 0，如图15-14所示。

字节o

字节I

 

tr

o|o|o|o|o|ojo|o|o|o|o|o丨 丨0|?1

8位整数

16位憝数 图 15-14

可见，虽然DataView能让我们在字节级别上读写数组缓冲器中的数据，但我们必须自己记住要将 数据保存到哪里，需要占用多少字节。这样一来，就会带来很多工作量，因此类型化视图也就应运而生。

2.类型化视图

类型化视图一般也被称为类型化数组，因为它们除了元素必须是某种特定的数据类型外，与常规的 数组无异。类型化视图也分几种，而且它们都继承了 DataViewo

□    Int8Array:表示8位二补整数。

□    Uint8Array：表示8位无符号整数。

□    Inti6Array：表示16位二补整数。

□    Uint 16Array：表示16位无符号整数。

口工nt32Array:表位二补整数。

□    Uint32Array:表示32位无符号整数。

□    Float32Array:表示 32 位 IEEE 浮点值。

□    Float64Array：表承 64 位 IEEE 浮点值。

每种视图类型都以不同的方式表示数据，而同一数据视选择的类型不同有可能占用一或多字节。例 如，20B 的 ArrayBuffer 可以保存 20 个：Ent8Array 或 Uint 8 Array,或者 10 个 Inti 6 Array 或 Uint 16Array,或者 5 个 Int32Array > Uint32Array 或 Float32Array,或者 2 个 Float64Array0

由于这些视图都继承fl DataView,因而可以使用相同的构造函数参数来实例化。第一个参数是要

使用ArrayBuffer对象，第二个参数是作为起点的字节偏移量(默认为0 )，第二个参数是要包含的字

节数。三个参数中只有第一个是必需的。下面来看几个例子。

//创建一个新数组，使用整个缓冲器

var int8s = new Int8Array(buffer);

//只使用从字节9开始的缓冲器

var intlfis = new Intl6Array(buffer, 9);

//只使用从字节9到字节18的缓冲器

var uintlGs = new Uintl6Array(buffer, 9, 10)；

能够指定缓冲器中可用的字节段，意味着能在同一个缓冲器中保存不同类型的数值。比如，下面的 代码就是在缓冲器的开头保存8位整数，而在其他字节中保存16位整数。

//使用缓冲器的一部分保存8位整數，另一部分保存16位整教 var int8s = new Int8Array(buffer， 0, 10); var uintl6s = new Uint16Array<buffer, 11, 10);

每个视图构造函数都有一个名为BYTES_PER_ELEMENT的域性，表示类型化数组的每个元素需要多 少字节。因此，Uint8Array，BYTES_PER_ELEMENT 就是 1，而 Float32Array.BYTES_PER_ELEMENT

则为4。可以利用这个属性来辅助初始化。

//需要10个元素空间

var int8s = new In18Array{buffer, 0 > 10 * Int8Array.BYTES_PER_ELEMENT);

//需要5个元素空间

var uinCl6s = new Uintl6Array(buffer, int8s.byteOffset + int8s.byLeLength,

5 * Uintl6Array.BYTES_PER_ELEMENT);

以上代码基于同一个数组缓冲器创建了两个视图。缓冲器的前10B用于保存8位整数，而其他字节 用于保存无符号16位整数。在初始化Uintl6Array的时候，使用了 Int8Array的byteOffset和 byteLength属性，以确保uintl6s开始于8位数据之后。

如前所述，类型化视图的目的在于简化对二进制数据的操作。除了前面看到的优点之外，创建类型 化视图还可以不用首先创建ArrayBuffer对象。只要传人希望数组保存的元素数，相应的构造函数就 可以自动创建一个包含足够字节数的Array Buffer对象，例如：

//创建一个数组保存10个8位整数(10字节) var int8s = new Int8Array(10)?

//创建一个数组保存10个16位整數(20字节) var intlfis - new Int16Array(10);

另外，也可以把常规数组转换为类型化视图，只要把常规数组传人类型化视图的构造函数即可：

//创建一个教组保存5个8位螯數(10字节)

var int8s = new Int8Array([10, 20, 30, 40, 50】};

这是用默认值来初始化类型化视图的最佳方式，也是WebGL项目中最常用的方式。

以这种方式来使用类型化视图，可以让它们看起来更像Array对象，同时也能确保在读写信息的 时候使用正确的数据类型。

使用类型化视图时，可以通过方括号语法访问每一个数据成员，可以通过length属性确定数组中 有多少元素。这样，对类型化视團的迭代与对Array对象的迭代就是一样的了。

for (var i=0, len=int8s.length； i < len； i++){

console.log("Value at position *+i+*is"+ int8s[i]);

}

当然，也可以使用方括号语法为类型化视阁的元索賦值。如果为相应元素指定的字节数放不下相应 的值，则实际保存的值是最大可能值的模。例如，无符号16位整数所能表示的最大数值是65535,如果 你想保存65536,那实际保存的值是0;如果你想保存65537,那实际保存的值是1,依此类推。

var uintl6s = new Uintl6Array(10)； uintl6s[0] = 65537； alert (uintl6s (01) ; "1

数据类型不匹配时不会抛出错误，所以你必须自己保证所赋的值不会超过相应元素的字节限制。

类型化视图还有一个方法，即subarray(),使用这个方法可以基于底层数组缓冲器的子集创建一 个新视图。这个方法接收两个参数：开始元素的索引和可选的结束元素的索引。返回的类型与调用该方 法的视图类型相同。例如：

var uintl6s = new Uintl6Array(10),

sub a uint 16s.subarroLy {2, 5);

在以上代码中，sub也是Uintl6Array的一个实例，而且底层与uintl6s都基于同一个 ArrayBuffero通过大视阁创建小视图的主要好处就是，在操作大数组中的一部分元素时，无需担心意 外修改了其他元素。

类型化数组是WebGL项目中执行各种操作的重要基础。

15.3.2 WebGL 上下文

目前，在支持的浏览器屮，WebGL的名字叫1'experimental-webgl*,这是因为WebGL规范仍 然未制定完成。制定完成后，这个上下文的名字就会变成简单的。webgl%如果浏览器不支持WebGL, 那么取得该上下文时会返回null。在使用WebGL上下文时，务必先检测一下返回值。

var drawing = document.getElementById{"drawing")；

/ /确定浏览器支持＜canvas＞元素 if (drawing.getContext){

var gl = drawing.getContext{"experimental-webgl"}; if (gi)＜

"使用WebGL

}

}

WebGLExampleOl. htm

一般都把WebGL上下文对象命名为gl。大多数WebGL应用和示例都遵守这一约定,因为OpenGL ES2.0规定的方法和值通常都以-gl ’•开头。这样做也可以保证JavaScript代码与OpenGL程序更相近。

取得了 WebGL上下文之后，就町以开始3D绘图了。如前所述，WebGL是OpenGL ES 2.0的Web 版，因此本节讨论的概念实际上就是OpenGL概念在JavaScript中的实现。

通过给getContextG传递第二个参数，可以为WebGL h下文没置一些选项。这个参数本身是一 个对象，可以包含下列属性。

□    alpha：值为true,表示为上下文创建一个Alpha通道缓冲区；默认值为true。

□    depth：值为true,表示可以使用16位深缓冲区；默汄值为true。

□    stencil:值为true,表示可以使用8位模板缓冲区；默认值为false。

□    antialias：值为true,表示将使用默认机制执行抗锯齿操作；默认值为true。

□    premultipliedAlpha:值为true,表示绘图缓冲区有预乘Alpha值；默认值为true。

口 preserveDrawingBuffer：值为true,表示在绘图完成后保留绘阁缓冲区；默认值为false。 建议确实有必要的情况下再开启这个值，因为可能影响性能。

传递这个选项对象的方式如下：

var drawing = document.getElementByIdp drawing")；

//确定浏览器支持＜canvas＞元素 if {drawing.getConeext){

var gl = drawing.getContext("experimental-webgl", { alpha: false})f if ＜gi)(

//使用 WebGL

WebGLExampleO 1. htm

大多数上下文选项只在髙级技巧中使用。很多时候，各个选项的默认值就能满足我们的要求。

如果getContextO无法创建WebGL上下文，有的浏览器会抛出错误。为此，最好把调用封装到

一个 try-catch 块中。

Insert IconMargin    [download]var drawing = document.getElementById{"drawing"),

gi;

/ /确定浏览器支待＜canvas＞元責 if (drawing.getContext){

try {

gl = drawing.getContext("experimental-webgln;

} catch (ex) {

//什么也不做

}

if (gl){

//使用 WebGL

} else {

alert("WebGL context could not be created.");

)

WebGLExampleOl • htm

\1.    常量

如果你熟悉OpenGL,那肯定会对各种操作巾使用非常多的常量印象深刻。这些常最在OpenGL中 都带前缀GL_。在WebGL中，保存在上下文对象中的这些常景都没有GL_前缀。比如说， GL_COLOR_BUFFER_BIT 常量在 WebGL 上下文中就是 gl. COLOR_BUFFER^BIT。WebGL 以这种方式支 持大多数OpenGL常量（有一部分常量是不支持的）。

\2.    方法命名

OpenGL （以及WebGL）中的很多方法都试图通过名字传达有关数据类型的信息，如果某方法可以 接收不同类型及不同数景的参数，看方法名的后缀就可以知道。方法名的后缀会包含参数个数（1到4） 和接收的数据类型（f表示浮点数，i表示整数）。例如，gl.Unifcmn4f（＞意味着要接收4个浮点数， 而gl .uniform3i （）则表示要接收3个整数。

也有很多方法接收数组参数而非一个个单独的参数。这样的方法其名字中会包含字母v （即vector, 矢量）。因此，gl.unifonn3iv（）可以接收一个包含3个值的整数数组。请大家记住以上命名约定，这 样对理解后面关于WebGL的讨论很有帮助。

\3.    准备绘图

在实际操作WebGL上下文之前，一般都要使用某种实色清除＜canvas＞,为绘图做好准备。为此，

酋先必须使用clearColor＜）方法来指定要使用的颜色值，该方法接收4个参数：红、绿、蓝和透明度。

每个参数必须是一个0到1之间的数值，表示每种分量在最终颜色中的强度。来看下面的例子。

gl.clearColor（0,0,0,1）；    //black

gl.clear（gl.COLOR_BUFFER_BIT）;

WebGLExampleOI .htm

470 第15章 使用Canvas绘囷

以上代码把清理颜色缓冲区的值没S为黑色，然后调用了 clear ()方法，这个方法与OpenGL中的 glClear 等价,，传人的参数gl. COLOR_BUFFER_BIT吿诉WebGL使用之前定义的颜色来填充相应区 域＜,一般來说，都要先清理缓冲区，然历甩执行他绘阁操作。

4.视口与坐标

开始绘图之前，通常要先定义WebGL的视口( viewport )。默认情况下，视口可以使用整个＜Canvas＞ 区域,，要改变视口大小，可以调用viewport ()方法并传人4个参数：(视口相对于＜CanvaS＞元素的) x坐标、:v坐标、宽度和髙度。例如，下面的调用就使用了＜canvaS＞元素：

gl.viewport(0, 0, drawing.width, drawing.height);

视口坐标与我们通常熟悉的网页坐标不一样。视口坐标的原点(0,0)在＜canvas＞元素的左下角，X 轴和J轴的正方向分别是向右■和向上，可以定义为(width-1, height-1),如图1545所示。

＜canvas＞    (width-1, height-1)

(0,0)

图 15-15

知道怎么定义视口大小，就可以只在＜canvaS＞元素的部分区域中绘图。来看下面的例子。 //視口是＜canvas＞^下角的四分之一区域

gl.viewport(0, 0, drawing.width/2, drawing.height/2)；

//视口是ccanvas＞左上角的四分之一区域

gl.viewport(0, drawing.height/2, drawing.width/2, drawing.height/2)；

//视口是＜canvas＞右下角的四分之一区域

gl.viewport(drawing.width/2, 0, drawing.width/2, drawing.height/2)?

另外，视u内部的坐标系与定义视口的坐标系也不一样。在视口内部，坐标原点(0,0)是视口的中心 点，因此视口左下角坐标为(-1,-1),而右上角坐标为(1,1),如图15-16所示。

——--1(1，1)

.(0,0)

图 15-16

如果在视n内部绘阁时使用视门外部的坐标，结果nJ能会被视口剪切。比如，要绘制的形状有一个 顶点在(1＞2)，那么该形状在视口右侧的部分会被剪切掉。

5.缓冲区

顶点信息保存在JavaScript的类型化数组中,使用之前必须转换到WebGL的缓冲区。要创建缓冲区， 可以调用gl.createBufferO,然后使用gl.bindBuf fer ()绑定到WebGL上下文。这两步做完之 后，就可以用数据来填充缓冲区了。例如：

var buffer = gl.createBuffer()；

gl.bindBuffer(gl.ARRAY_BUFFER# buffer);

gl.bufferData(gl.ARRAY 一BUFFER, new Float32Array([◦, 0.5, 1]), gl.STATIC_DRAW);

调用gl.bindBuffer()可以将buffer设置为上下文的当前缓冲区。此后，所有缓冲区操作都 直接在buffer中执行。因此，调用gl .bufferData ()时不需要明确传入buffer也没存问题。最后 一行代码使用Float32Array中的数据初始化了 buffer (—般都是用Float32Array来保存顶点信

息)。如果想使用drawElements {)输出缓冲区的内容，也可以传人gl. ELEMENT_ARRAY_BUFFERO gl.bufferDataO的最后一个参数用于指定使用缓冲区的方式，取值范闱是如下几个常量。

□    gl.STATIC_DRAW:数据只加载一次，在多次绘图中使用。

□    gl. STREAM_DRAW:数据只加载一次，在几次绘图中使用。

□    gl. DYNAMIC_DRAW：数据动态改变，在多次绘图中使用。

如果不是非常有经验的OpenGL程序员，多数情况下将缓冲区使用方式设置为gl. STATIC_DRAW

即可。

在包含缓冲区的页面重载之前，缓冲区始终保留在内存中。如果你+想要某个缓冲区了，可以直接 调用gl.deleteBuffer ()释放内存：

gl.deleteBuffer(buffer)；

\6.    错误

JavaScript与WebGL之间的一个最大的区别在于，WebGL操作一般不会抛出错误。为了知道是否 有错误发生，必须在调用某个可能出错的方法后，手工调用gl.getErrorG方法。这个方法返回一个 表示错误类型的常量。可能的错误常量如下。

□    gl.NO_ERROR：上一次操作没有发生错误(值为0)。

□    gl.INVALID_ENUM：应该给方法传人WebGL常量，但却传错了参数。

□    gl.INVALID_VALUE:在需要无符号数的地方传人了负值。

□    gl. INVALID_OPERATION:在当前状态下不能完成操作。

□    gl.OUT_OFJ4EMORY:没有足够的内存完成操作。

□    gl. C0NTEXT_L0ST_WE3GL:由于外部事件(如设备断电)干扰丢失了当前WebGL上下文。

每次调用gl.getError ()方法返回一个错误值。第-次调用后，后续对gl.getError ()的调用

可能会返回另一个错误值。如果发生了多个错误，需要反复调用gl.getError()直至它返回

gl.NO^ERRORo在执行了很多操作的情况下，最好通过一个循环来调用getError (),如下所示：

var errorCode = gl.getError(); while(errorCode){

console.log("Error occurred: • + errorCode); errorCode = gl.getError();

}

如果WebGL脚本输出不正确，那在脚本中放几行gl.getError ()有助于找出问题所在。

\7.    着色器

着色器(shader)是OpenGL中的另一个概念。WebGL中有两种着色器：顶点着色器和片段(或像 素)着色器。顶点着色器用于将3D顶点转换为需要浪染的2D点。片段着色器用于准确计算要绘制的

秘个像索的颜色。WebGL着色器的独特之处也是其难点在于，它们并不是用JavaScript写的。这些着色 器是使用 GLSL （ OpenGLShading Language, OpenGL卷色语言）写的，GLSL是—*种与 C 和 JavaScript 完全不同的语言。

8.编写着色器

GLSL是一种类C语言，专门用于编写OpenGL着色器。因为WebGL是OpenGLES2.0的实现，所 以OpenGL中使用的着色器可以直接在WebGL中使用3这样就方便丫将桌面图形应用移植到浏览器中。

每个着色器都苻一个main U方法，该方法在绘阁期间会复执行„为若色器传递数据的方式有两 种：Attribute和Uniform。通过Attribute可以向顶点着色器中传人顶点信息，通过Uniform可以向任何 着色器传人常量值。Attribute和Uniform在main （）方法外部定义，分别使用关键字attribute和 uniform。在这两个值类型关键字之后，是数据类型和变嚴名。下面是一个简单的顶点着色器的例子。

//OpenGL屠色语言

J    //着色器，斗者Bartek Drozdz,摘自他的文章

//http：//www.necmagazine.com/tutorials/get-started-webgl-draw-square attribute vec2 aVertexPosition；void main（） {

gl_Position = vgc4{aVertexPosition, 0.0,1.0）；

WebGLExampleO2.htm

这个顶点着色器定义了一个名为aVertexPosition的Attribute,这个Attribute是一个数组，包含 两个元素（数据类型为vec2）,表示x和y坐标。即使只接收到两个坐标，顶点着色器也必须把一个包 含四方面信息的顶点赋值给特殊变量gl_POSitiOn。这里的着色器创建了一个新的包含四个元素的数 组（vec4）,填补缺失的坐标，结果是把2D坐标转换成了 3D坐标。

除了只能通过Uniform传人数据外，片段着色器与顶点着色器类似。以下是片段着色器的例子。

//OpenGL着色语言

//着色崧，知者Bartek Drozdz,摘自他的文章

//http://www.netmagazine.com/tutorials/get-started-webgl-draw-square uniform vec4 uColor；

void main（） {

gl_FragColor = uColor;

}

J¥ebGLExamp!eO2. htm

片段着色器必须返回一个值，赋给变量gl_FragCOlor,表示绘图时使用的颜色。这个着色器定义 了一个包含四方面信息（vec4 ）的统一的颜色uColor。从以上代碑看，这个着色器除了把传人的值赋 给gl_FragColor什么也没做。uColor的值在这个若色器内部不能改变。

OpenGL着色语言比这里看到的还番复杂。专门讲解这门语言的卒有很多，本节 只是从辅助使用WebGL的角度简要介绍一下该语言。要了解更多信息，请参考Randi J. Rost 编著的 OpenGL Shading Language （Addison-Wesley>2006）。

9.编写着色器程序

浏览器不能理解GLSL程序，因此必须准备好字符串形式的GLSL程序，以便编译并链接到着色器 程序。为便于使用，通常是把着色器包含在页面的<scripL>^签内，并为该标签指定一个自定义的type 属性。由于无法识别type属性值，浏览器不会解析<Script4S签中的内容，但这不影响你读写其中 的代码。例如：

ocript type="x-webgl/x-vertex-shader" id-HvertexShader"> j attribute vec2 aVertexPosition；

void mainO (

gl_Position = vec4(aVertexPosition, 0.0, 1.0);

)

</script>

<script type="x-webgl/x-fragment ^shader" i d="fragment Shader"> uniform vec4 uColor；

void main() {

gl_FragColor = uColor;

}

</script>

WebGLExampleO2. htm

然后，可以通过text属性提取出＜script＞元素的内容：

var vertexGlsl = document.getElementById{"vertexShader■).text,

fragraentGlsl = document.getElementByld(■fragmentShader").text；

复杂一些的WebGL应用可能会通过Ajax (详见第21章)动态加载着色器。而使用着色器的关键是 要有宇符串形式的GLSL程序。

取得了 GLSL字符串之后，接下来就是创逮着色器对象。要创建着色器对象，可以调用gl.create-Shader ()方法并传人要创建的着色器类型(gl. VERTEX_SHADER或gl. FRAGMENT_SHADER )0编译着色 器使用的是gl.compileShader () „请看下面的例子。

var vertexShader = gl.createShader(gl.VERTEX_SH7ODER); gl.shaderSource(vertexShader, vertexGlsl); gl.compileShader{vertexShader);

var fragmentShader = gl.createShader(gl.FRAGMENT_SHADER); gl.shaderSource(fragmentShader, fragmentGlsl)； gl.compileShader(fragmentShader)；

WebGLExampieO2 .htm

以上代码创建了两个着色器，并将它们分別保存在vertexShadei•和fragmentShader中。而使 用下列代码，可以把这两个对象链接到着色器程序中。

var program = gl.createProgram(); gl.attachShader(program, vertexShader); gl.attachShader(program, fragmentShader)； gl.linkProgram(program);

WebGLExampleO2. htm

第一行代码创建了程序，然后调用attachShaderU方法又包含了两个着色器。最后调用gl.link-Program ()则把两个着色器封装到了变量program中。链接完程序之后，就可以通过gl .useProgram () 方法通知WebGL使用这个程序了。

gl.useProgram(program);

调用gl. useProgram 0方法后，所有后续的绘图操作都将使用这个程序。

10.为着色器传入值

前面定义的着色器都必须接收一个值才能T作。为了给着色器传人这个值，必须先找到要接收这个 值的变量。对于Uniform变fi,可以使用gl .getUniformLocation (>，这个方法返回一个对象，表示 Uniform变量在内存中的位置。然后可以基于变最的位置来轼值。例如：

var uColor = gl.getUniformLocation(program, "uColor");

I    gl.uniform4fv{uColor, [0# 0, 0, 1));

WebGLExampleO2. htm

第一行代码从program中找到Uniform变量uColor，返回了它在内存中的位置。第二行代码使用 gl .uniform4fv<)给 uColor 赋值。

对于顶点着色器中的Attribute变量，也是差不多的赋值过程。要找到Attribute变量在内存中的位置， 可以调用gl.getAttribLocationO。取得了位置之后，就可以像下面这样赋值了：

var aVertexPosition = gl.getAtcribLocation(program, "aVertexPosition"); gl.enableVertexAt t r ibArray(aVertexPosition)；

gl.vertexAttribPointer{aVertexPosition, itemSize, gl.FLOAT, false, 0, 0)；

WebGLExampleO2. htm

在此，我们取得了 aVertexPosition 的位置，然后乂通过 gl. enableVertexAttr ibAr ray {} 启用它。最后一行创建了指针，指向由gl.bindBuffer()指定的缓冲区，并将其保存在 aVertexPosition中，以便顶点着色器使用。

11.调试着色器和程序

与WebGL中的其他操作一样，着色器操作也可能会失败，而且也是静默失败。如果你想知道着色 器或程序执行中是否发生了错误，必须亲自询问WebGL上下文。

对于着色器，可以在操作之后调用gl.getShaderParaneter{),取得着色器的编译状态：

if (!gl.getShaderParameter(vertexShader, gl.COMPILE一STATUS}){ i    alert{gl.getShader工nfoLog(vertexShader))；

IVebGLExampleO2. htm

这个例子检测了 vertexShader的编译状态。如果着色器编译成功，调用gl .getShader-Parameter ()会返回true。如果返冋的是false,说明编译期间发生了错误，此时调用gl .getShader-InfoLogO并传人相应的着色器就可以取得错误消息。错误消息就是一个表示问题所在的字符串。无论 是顶点着色器,还是片段着色器，都可以使用91.96[5113<^什3[311飢61：()和91.9613113<161?111£01^09() 方法。

程序也可能会执行失败，因此也有类似的方法-gl. getProgramParameter (),可以用来检测

执行状态。最常见的程序失败发生在链接过程中，要检测链接错误，可以使用下列代码。

if (tgl .getProgramParameter (program, gl. LINK__STATUS>) { alert(gl.getProgramlnfoLog(program)J；

)

fVebGLExampleO2.htm

与 gl.getShaderParameter ()类似，gl .getProgramParameter ()返网 true 表示链接成功， 返回false表尔链接失败。同样，也有一个gl .getProgramInfoLog(＞方法，用于捕获程序失败的 消息。

以上介绍的这些方法主要在开发过程中用于凋试。只要没有依赖外部代码，就可以放心地把它们从 产品代码中删除„

12.绘图

WebGL只能绘制.三种形状：点、线和三角。其他所有形状都是由这三种基本形状合成之后，再绘 制到三维空间中的。执彳f绘图操诈要调用gl .drawArrays ()或gl .drawElemencs (＞方法，前者用于 数组缓冲区，后者用于元素数组缓冲区。

gl.drawArrays U或gl .drawElements n的第一个参数都是一个常墦，表示要绘制的形状。可 取值的常#范围包括以下这些。

□    gl. POINTS:将每个顶点当成一个点来绘制。

□    g：.'LINES:将数组当成一系列顶点，在这些顶点间画线。每个顶点既是起点也是终点，因此数 组中必须包含偶数个顶点才能完成绘制。

□    f .LINE_LOOP:将数组当成• •系列顶点，在这些顶点间画线。线条从第-个顶点到第二个顶点， 再从第二个顶点到第三个顶点，依此类推，直至最后一个顶点。然后再从最后一个顶点到第一 个顶点画一条线。结果就足•一个形状的轮廓。

□    gl.LINE.STRIP:除f不画最后一个顶点与第一个顶点之间的线之外，其他与gl.LINE_LOOP 相同。

□    gl. TRIANGLES:将数组当成一系列顶点，在这些顶点间绘制三角形„除非明确指定，每个三角 形都单独绘制，不与其他二角形共卒顶点。

□    gl.TRT.ANGLES_STRIP:除了将前三个顶点之后的顶点当作第1个顶点与前两个顶点共同构成 一个新三角形外，其他都与gl.TRIANGLES相同。例如，如果数组中包含A、B、C、D叫个顶 点，则第一个1角形连接ABC,而第二个三角形连接BCD。

□    gl. TRIANGLES_FAN:除了将前H个顶点之后的顶点当作第-:个顶点与前一•个顶点及第一个顶 点共同构成一个新三角形外，其他都与gl.TRIANGLES相同。例如，如果数组中包含A、B、C、 D四个顶点，则第一个三角形连接ABC,而第二个三角形连接ACD。

gl.drav/ArraysO方法接收上面列出的常量中的一个作为第--个参数，接收数组缓冲区中的起始 索引作为第二个参数，接收数组缓冲区中包含的顶点数(点的集合数)作为第H个参数。下面的代码使 用gl. drawArrays (＞在画布上绘制了一个；｛角形。

//假设巳经使用本节黹面定义的着色器清除了视口

//完义三个顶点以及每个頂点的x和y坐标

var vertices - new Float32Array([ 0, 1, 1, -1, -1,    ]),

buffer = gl.createBuffer(), vertexSetSize = 2,

vertexSetCoun t = vertices.length/vertexSetSize, uColor, aVertexPosition；

//把数据放到缓冲区

gl.bindBuffer(gl.ARRAY_BUFFERZ buffer)；

gl.buf ferData {g 1. ARRAY_BUFFER, vertices, gl..STATTC_DRAW);

//为片段着色器传入颜色值

uColor = gi.getUniformLocation(program, ■uColor");

gl.uniform4fv(uColorz [ 0, 0, 0, 1 J);

/ /为着色器佬入顶点信息

aVertexPosition = gl.getAttribLocation{program, "aVertexPosition™＞；

gl.enableVertexAttribArray(aVertexPosition)；

gl • vertexAttrd.bPointer(aVertexPosition, vertexSetSize, gl.FLOAT, false, 0, 0);

//绘制三角形

gl.drawArrays(gl.TRIANGLES, 0, vertexSetCount);

WebGLExampleO2. htm

这个例+定义了一个Fioat32Array,包含三组顶点(每个顶点由两点表示)。这里关键是要知道 顶点的大小及数量，以便将来计算时使用。把VertexSetSize设置为2之后，就可以计算出 vertexSetCount的值。把顶点的信息保存在缓冲区中后，又把颜色信息传给了片段着色器。

接下来，给顶点着色器传人顶点大小以及gl.FLOAT,后者表示顶点坐标是浮点数。传人的第四个 参数是一个布尔值，false在此表示坐稼不是梅淮化的。第五个参数是步长值(stride value),表示取 得下一个值的吋候，要跳过多少个数组元索。除非你K.需要跳过数组元素，否则传人0即可。最后一个 参数是起点偏移呈，值为0表示从第一个元索开始。

最后一步就是使用gl. drawArrays ()绘制三角形。传人gi. TRIANGLES作为第一个参数，表示在 (0,1)、和(-1,-1)点之间绘制二角形，并使川传给片段着色器的颜色来填充它。第二个参数是缓冲 区中的起点偏移量，最后一个参数是要读取的顶点总数。这次绘阁操作的结果如图15-17所示。

图 15-17

通过修改gl .drawArrays ()的第一个参数，可以修改绘制三角形的方式。图15-18展7K了传人不

gl.LINE_LOOP

 

图

 

13.纹理

WebGL的纹理可以使用DOM中的图像。要创建一个新纹理，可以调用gl. createTexture (), 然后再将-•幅图像绑定到该纹理。如果图像尚未加载到内存中，可能需要创建一个image对象的实例， 以便动态加载图像。图像加载完成之前，纹理不会初始化，因此，必须在load事件触发后才能设置纹 理。例如：

var image = new Image(), texture；

image.src = "smile.gif■; image.onload = function(){

texture = gl.createTexture();

gl.bindTexture(gl.TEXTURE_2D# texture);

gl-pixelStorei(gl.UNPACK一FLIP_Y_WEBGL, true};

gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA， gl.RGBA, gl.UNSIGNED_BYTE# image); gl.texParameteri(gl.TEXTURE_2D， gl.TEXTURE_MAG_FILTER/ gl.NEAREST); gl.texParameteri(gl.TEXTURE_2D#gl.TEXTURE_MIN_FILTER, gl.NEAREST);

//清除当前纹理

gl.bindTexture(gl.TEXTURE一2D， null)?

除了使用DOM中的图像之外，以上步骤与在OpenGL中创建纹理的步骤相同。最大的差异是使用 gl.pixelStorel()设置像素存储格式。gl .UNPACK_FLIP_Y_WEBGL是WebGL独有的常量，在加载 Web中的图像时，多数情况下都必须使用这个常量。这主要是因为GIF, JPEG和PNG图像与WebGL 使用的坐标系不一样，如果没有这个标志，解析图像时就会发生混乱。

用作纹理的图像必须与包含贞面来自同一个域，或者是保存在启用了 CORS ( Cross-Origin Resource Sharing,跨域资源共李)的服务器上。第21 $将讨论CORS。

图像、加载到＜^1(360＞元素中的视频，甚至其他＜031^33＞元素都可以用作纹理。 跨域资源限制同样适用于视频。

14.读取像素

与2D上下文类似，通过WebGL上下文也能读取像素值。读取像素值的方法readPixels ()与 OpenGL中的同名方法只有一点不同，即最后一个参数必须是类型化数组。像素信息是从帧缓冲区读取

的，然后保存在类型化数组中。readPixelsO方法的参数有：X、_v、觉度、髙度、图像格式、数据类 型和类型化数组„前4个参数指定读取哪个区域屮的像素。图像格式参数儿乎总是gl.RGBA。数据类型 参数用于指定保存在类型化数组中的数据的类型，但有以下限制。

□如果类型是gl.UNSIGNE3_BYTE,则类型化数组必须是Uint8Array。

□如果类型是 gl. UNSIGNED_SHORT_5_6_5、gl. UNSIGNED_SHORT_4_4_4_4 或 gl.UNSIGNED. SHORT_5_5_5_1,则类型化数组必须是Uint 16Array。

下面是一个简单的例子。

var pixels = nev; Uint8Array (25*25);

gl<readPixels{0, 0, 25, 25r gl.RGBAr g1.UNSIGNED_BYTE# pixels);

以上代码从帧缓冲区中读取了 25*25像索的K域，将读取到的像索信息保存到了 pixels数组中。 其中，毎个像素的颜色由4个数组元素表示，分别代表红、绿、蓝和透明度。好个数组元索的值介于0 到255之间(包含0和255)。不要忘了根据返回的数据大小初始化类型化数组。

在浏览溫绘制更新的WebGL图像之前调用readPixelsU不会有什么意外。绘制发生后，帧缓冲 区会恢复其原始的干净状态，而调用readPixelsG返回的像素数据反映的就是清除缓冲区后的状态。 如果你想在绘制发生后谈取像素数据，那在初始化WebGL上下文吋必须传人适当的 preserveDrawingBuf f er 选项(前面讨论过)o

var gl = drawing.getContext("experimental-webgl"r ( preserveDrawingBuffer： true; });

设置这个标志的意思是让帧缓冲区在下一次绘制之前，保招其最后的状态。这个选项会导致性能损 失，因此能不用最好不要用。

15.3.3支持

Firefox4+和 Chrome都实现了 WebGLAPl。Safari 5.1 也实现了 WebGL，但默认是禁用的。WebGL 比较特别的地方在于，某个浏览器的某个版本实现了它，并不一定盘味着就真能使用它。某个浏览器支 持WebGL,至少意味着两件事：首先，浏览器本身必须实现了 WebGL API;其次，计算机必须升级显 示驱动程序。运行Windows XP等操作系统的一些老机器，其驱动程序一般都不是最新的。因此，这些 计算机中的浏览器都会禁用WebGL。从稳妥的角度考虎，在使用WebGL之前，最好检测其是否得到了 支持，而不是只检测特定的浏览器版本。

大家别忘了，WebGL还是一个正在制定和发展中的规范。不管娃函数名、函数签名，还是数据类 型，都有可能改变。可以说，WebGL □前只适合实验性地学习，不适合真正开发和应用。

15.4小结

HTML5的《31^33>元素提供了一组JavaScript API,让我们可以动态地创建图形和图像。图形是在一 个特定的上下文中创建的，而上下文对象H前有两种。第一种是2D上下文，可以执行原始的绘图操作， 比如：

□设H填充、描边颜色和模式

□绘制矩形

□绘制路径

□绘制文本

□创建渐变和模式

第二种是3D上下文，即WebGL上下文。WebGL是从OpenGLES 2.0移植到浏览器中的，而OpenGL ES2.0是游戏开发人员在创建计算机阁形阁像时经常便用的一种语言。WebGL支持比2D上下文更丰富 和更强大的图形图像处埋能力，比如：

15

 

□用GLSL （ OpenGL Shading Language, OpenGL着色语言）编写的顶点和片段着色器

□支持类沏化数组，即能够将数组屮的数据限定为某种特定的数值类型

□创建和操作纹理

目前，主流浏览器的较新版本大都已经支持＜^1^33＞标签。同样地，这些版本的浏览器基本上也 都支持2D上下文。但对于WebGL而言，目前还只有Firefox 4+和Chrome支持它。