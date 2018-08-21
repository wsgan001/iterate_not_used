新兴的API

本章内容

□创建平滑的动M □操作文件

□使用Web Workers在后台执行JavaScript

着HTML5的出现，面向未来Web应用的JavaScript API也得到了极大的发展。这些API没有 包含在HTML5规范中，而是各自有各自的规范。但是，它们都属于“HTML5相关的API”。

本章介绍的所有API都在持续制定中，还没有完全固定下来。

无论如何，浏览器己经着手实现这些API,而…吐应用开发人员也都开始使用^^们了。读者应该能

够注意到，其中很多API都带布特定于浏览器的前缀，比如微软是ms,而Chrome和Safari是webkit。 通过添加这些前缀，不同的浏览器可以测试还在开发中的新API,不过请记住，去枠前缀之后的部分在 所有浏览器中都是一致的。

25.1 reguestAnimationFrame()

很长时间以来，计时器和循环间隔一直都是JavaScript动画的最核心技术。虽然CSS变换及动W为 Web开发人员提供了实现动画的简单手段，但JavaScript动画开发领域的状况这些年来并没有大的变化。 Firefox 4 最早为 JavaScript 动脑添加 f 一个新 API,即 mozRequestAnimationFraxne ()。这个方法会 吿诉浏览器：有一个动画开始f。进而浏览器就可以确定重绘的进佳方式。

25.1.1早期动画循环

在JavaScript中创建动画的典堃方式，就是使用setlntervalO方法来控制所有动画。以下是一个 使用setlnterval ()的基本动画循环：

(function(){

function updateAnimations(){ doAniinationl () ? doAnimation2()；

//其他动画

setlnterval{updateAnimations， 100); })()；

为了创建—小型动岡库，updateAnimations ()方法就得不断循环地运行每个动R，并相应地改 变不同元素的状态(例如，同时显示一个新闻跑马灯和一个进度条)。如果没有动画雒要更新，这个方 法可以退出，什么也不用做，甚至可以把动阃循环停下來，等待下一次需要更新的动画。

编写这种动画循环的关键是要知道延迟时间多长合诂。--方面，循环间隔必须足够短，这样才能让 不同的动岡效果姓得更平滑流畅；另一方面，循环间隔还要足够长，这样才能确保浏览器有能力渲染产 生的变化。大多数电脑显示器的刷新频率是60Hz,大概相当于每秒钟重绘60次。大多数浏览器都会对 重绘操作加以限制，不超过显示器的i绘频率，因为即使超过那个频率用户体验也不会有提升。

因此，坡平滑动画的最佳循环间隔是1000ms/60,约等于17ms。以这个栃环间隔敢绘的动画是最平 滑的，因为这个速度最接近浏览器的最高限速。为了适应17ms的循环间隔，多重动賊可能需要加以节 制，以便不会完成得太快。

虽然与使用多组setTimeout(＞的循环方式相比，使用set Interval (I的动画循环效率更高，但 后者也不是没有问题。无论是setlnterval ()还是setTimeout (＞都不十分精确。为它们传人的第二 个参数，实际上只是指定了把动画代码添加到浏览器UI线程队列中以等待执行的时间。如果队列前面 已经加人了其他任务，那动画代码就要等前面的任务完成辰再执行。简言之，以毫秒表示的延迟时间并 不代表到时候一定会执行动画代码，而仅代表到时候会把代码添加到任务队列中。如果UI线程繁忙， 比如忙于处理用户操作，那么即使把代码加人队列也不会立即执行。

25.1.2循环间隔的问题

知道什么时候绘制下一帧是保证动画平滑的关键。然而.直至最近，开发人员都没有办法确保浏览 器按时绘制下一帧。随狞＜Canvas＞元素越来越流行，新的基于浏览器的游戏也开始崭露头脚，面对不 十分精确的setlnterval ()和setTimeout (),开发人员一筹莫展。

浏览器使用的计时器的精度进一步恶化了问题。具体地说，浏览器使用的计时器并非精确到毫秒级 别。以下是几个浏览器的计时器精度。

□    1E8及更早版本的计时器精度为15.625ms。

□    IE9及更晚版本的计时器精度为4nw。

□    Firefox和Safari的计时器精度大约为10ms。

□    Chrome的计时器精度为4ms□

IE9之前版本的计时器精度为15.625ms,因此介于0和15之间的任何值只能是0和15。IE9把计时 器精度提髙到了 4ras,但这个精度对于动岡來说仍然不够明确。Chrome的计时器精度为4ms,而Firefox 和Safari的精度是10ms。更为复杂的是，浏览器都开始限制后台标签页或不活动标签页的计时器。因此， 即使你优化了循环间隔，结果仍然只能接近你想要的效果。

25.1.3 mozReguestAnimationFrame

Mozilla的Robert O’Callahan认识到了这个问题，提出了一个非常独特的方案。他指出，CSS变换 和动画的优势在于浏览器知道动画什么时候开始，因此会计算出正确的循环间隔，在恰汽的时候刷新 UI。而对于JavaScript动岡.浏览器无从知晓什么时候开始。因此他的方案就是创造一个新方法 mozRequestAnimationFrame (),通过它否诉浏览器某牲JavaScript代碍将要执行动曲j。这样浏览器 可以在运行某些代码后迸行适当的优化。

mozRequestAnimationFrame (｝方法接收一个参数，即在重绘屏幕前调用的一个涵数。这个函数 负责改变下一次重绘时的DOM样式。为了创建动画循环，可以像以前使用從1^瓶50此()一样，把多 个对mozRequestAnimat ionFrame ()的调用连缀起来。比如：

function updateProgress(){

var div = document.getElementById{"status■);

div.style.width = (parselnt(div.style.width, 10) +5) + "%•;

if (div.style.left != "100%"){

mozReguestAnimat ionFrame(updateProgress);

}

mozRequestAnimationFrame(updateProgress)；

因为mozRequestAnimationFrame ()只运行一次传人的函数，因此在需要冉次修改UI从而生成 动画吋，需要再次手工调用它。同样，也需要同时考虑什么吋候停止动画。这样就能得到非常平滑流畅 的动画。

目前来看，mozRequestAnimationFrame ()解决了浏览器不知道JavaScript动画什么时候开始、 不知道最徒循环间隔时间的问题，但不知道代码到底什么时候执行的问题呢？同样的方案也可以解决这 个问题。

我们传递的mozRequestAnimationFrame ()涵数也会接收一个参数，它是一个时间码(从1970 年1月1日起至今的宼秒数)，表示下一次直绘的实际发生时间。注意，这一点很重要： mozRequestAnimationFrame()会根据这个吋间碑设定将来的某个时刻进行重绘，而根据这个时间 码，你也能知道那个时刻是什么时间，然后，再优化动画效果就有了依据。

要知道距离上一次重绘已经过去f麥长时间，可以邊询mozAnimationStartTime,其中包含上-次重绘的时间码。用传人四调函数的时间码减去这个时间就能计算出在屏幕上重绘下一组变化之前 要经过多长时间。使用这个值的典型方式如下：

function draw(timestamp){

//计算两次重绘的时间间塥

var diff = timestamp - startTime；

//使用diff确定下一步的绘制时间

//把startTime重写为这一次的绘制时间 startTime = timestamp；

//重绘UI

mozRequestAnimationFrame (draw);

var startTime = mozAnimationStartTime; mozRequestAnimationFrame(draw);

这里的关键是第一次读取mozAnimationStartTime的值,必须在传递给mozRequest Animat ion Frame ()的回调函数外面进行。如果是在回调函数内部渎取mozAnimationStartTime，得别的值与传 入的时间码是相等的。

25.1.4 webkitReguestAnimationFrame 与 msRequestAnimationFrame

基于 mozRequestAnimationFrame () , Chrome 和    也都给出 了自己的实现，分别叫 webkit-

Reques t Animat ion Fr ame (｝和 msRequestAnimationFrame () o 这两个版本与 Mozilla 的版本有•两个 方面的微小差异。首先，不会给回调函数传递时间码，因此你无法知道下一次重绘将发生在什么时间。 其次，Chrome 乂增加了第二个可选的参数，即将要发生变化的DOM元素。知道了重绘将发生在页面中 哪个特定元素的区域内，就可以将重绘限定在该区域中。

既然没有下一次重绘的时间码，那Chrome和IE没有提供mozAnimationStartTirae的实现也就 很容易理解了——没有那个时间码，实现这个属性也没有什么用。不过，Chrome倒是又提供了另一个 方法webkitCancelAnimationFrame (),用于取消之前计戈!I执行的i绘操作。

假如你木需要知道精确的时间差，那么可以在Firefox4+、IE 10+和Chrome中可以参考以下模式创 建动画循环。

{function(){

function draw(timestamp){

/ /计算两次重绘的时间间隔

var drawstart = {timestamp II Date.now())r diff = drawStart - startTime;

//使用diff确定下一步的绘制时间

//把startTime重写为这一次的绘制时间 startTime = drawStart;

//i绘U工

requestAnimationFrame (draw)；

var requestAnimationFrame = window.requestAnimationFrame |I

window.mozRequestAnimationFrame II window.webkitRequestAnimationFrame II window.msRequestAnimationFrame,

startTime = window.mozAnimationStartTime jI Date.now(); requestAnimationFrame(draw);

})()；

以上模式利用已有的功能创建了一个动画循坏，大致计算出了两次:E绘的时间间隔c在Firefox中， 计算吋间间隔使用的是既有的时间码，而在Chrome和IE中，则使用不十分精确的Date对象。这个模 式bI以大致体现出两次重绘的时间间隔，但不会吿诉你在Chrome和IE中的时间fuj隔到底是多少。不过, 大致知道吋间间隔总比一点儿概念也没有好些。

因为首先检测的是标准函数名，其次才是特定于浏览器的版本，所以这个动R循环在将来也能够 使用。

目前，W3C 已经着手起草 r equest Animat ionFrame (> API，而且作为 Web Performance Group 的 一部分，Mozilla和Google正共同参与该标准草案的制定工作。

25.2 Page Visibility API

不知道用户是不是正在与贞面交互.这是闲扰广大Web开发人员的一个主要问题。如果页面最小 化了或者隐載在了其他标签页后面，那么有些功能是可以停下来的，比如轮询服务器或者某些动画效果。 而Page Visibility API (页面可见性API)就是为了让开发人员知道页面是否对用户可见而推出的。

这个API本身非常简单，由以下三部分组成。

口 document.hidden：表示页面是否隐藏的布尔值。页面隐藏包括页面在后台标签页中或者浏览 器最小化。

口 document .visibilityState：表呆下列4个可能状态的值。

■页面在后台标签页中或浏览器最小化。

■页面祚前台标签页中。

■实际的页面已经隐藏，但用户可以看到页曲的预览(就像在Windows7中，用户把鼠标移动到 任务栏的图标上，就可以显示浏览器中气前页面的预览)。

■页面在屏幕外执行预渲染处理。

口 visibiiitychange事件：当文档从可见变为不可见或从不可见变为可见时，触发该事件。

在编写本书时，只有IE10和Chrome支持Page Visibility API0 IF,的版本是在每个属性或#件前面加

上ms前缀，而Chrome则是加上webkit前缀。因此document.hidden在IE的实现中就是 document .rasHidden,而在Chrome的实现中则足document .webkitHidden。检査浏览器是否支持 这个API的最佳方式如下：

function isHiddenSupported(){

return typeof (document.hidden I I document.msHidden I I

document .webkitHidden) ! = "undefined11;

}

Page VisibilityAPIExampleOl .htm

类似地，使用同样的模式可以检测页面是否隐藏：

if (document.hidden I I document.msHidden I I document.webKitHidden){ //页面隐藏了

} else {

/ /页面未味藏

}

Page VisibilityA PlExampleOl. htm

注意，以上代码在不支待该API的浏览器中会提示页面未隐藏。这是Page Visibility API有意设计的 结果，目的是为了向后兼容。

为了在页面从可见变为不可见或从不可见变为可见时收到通知，可以侦听visibilitychange事 件。注IE中，这个事件叫msvisibilitychange，而在Chrome中这个事件叫webkitvisibility-changeo为了在两个浏览器中都能侦听到该事件，可以像下面的例子一样，为毎个事件都指定相同的 事件处理程序：

function handleVisibilityChange(){

var output = document. get Element By Id (■•output ”，

msg;

if (document.hidden II document.msHidden I I document.webkitHidden){ msg - "Page is now hidden. " + (new Date()) + "<br>"；

} else {

msg = "Page is now visible. ” + (new DateO ) + "<br>" ?    •

}

output.innerHTML msg；

)

//要为两个事件部拍定事件处理42序

Eventutil.addHandler(docwnenL, "msvisibilitychange", handleVisibilityChange); Eventutil.addHandler(document, "webkitvisibilitychange", handleVisibilityChange)；

Page VisibilityAPIExampleOl .htm

以上代码同时适用于IE和Chrome。而i，API的这一部分已经相对稳定，因此在实际的Web开发 中也可以使用以上代码。

关于达一 API的实现，差异最大的是document.visibilityState属性。IE10 PR 2的 document .msVisibilityState是一个表示如下4种状态的数字值。

(1)    document .MS_PAGE__HIDDEN (0)

(2)    document .MS_PAGE_VISIBLE (1)

(3)    document. MS_PAGE_PREVIEW (2)

(4)    document .MS_PAGE_PRERENDER (3)

在 Chrome 中，document. webkitVisibilityState 可能是下列 3 个字符串值：

(1)    ^hidden*

(2)    "visible"

(3)    ° prerender"

Chrome并没有给每个状态定义对应的常设，但最终的实现很可能会使用常量。

由于存在以上差异，所以建议大家先不要完全依赖带前缀的document .visibilityState,最好 只使用 document. hidden 属性。

25.3 Geolocation API

地理定位(geolocation )是最令人兴奋，而且得到了广泛支持的-个新API。通过这套API, JavaScript 代码能够访问到用户的当前位置信息。当然，访问之前必须得到用户的明确许可，即同意在页面中井李 其位a信息。如果页面尝试访问地理定位信息，浏览器就会显示一个对话框，请求用户许可共享其位置 信息。闸25-1展示了 Chrome中的这样一4"对话框。

图 25-1

Geolocation API在浏览器中的实现是navigator.geolocation对象，这个对象包含3个方法。 第一个方法是getCurrentPositionO ,调用这个方法就会触发请求用户共享地理定位信息的对话框。

这个方法接收3个参数：成功回调函数、可选的失败回调函数和可选的选项对象。

其中，成功回调兩数会接收到一个Position对象参数,该对象有两个属性:coords和timestamp。

而coords对象中将包含下列与位置相关的信息。

□    latitude：以十进制度数表示的纬度。

□    longitude：以十进制度数表示的经度。

□    accuracy:经、纬度坐标的精度，以米为.单位。

有些浏览器还町能会在coords对象中提供如下屈性。

□    altitude:以米为单位的海拔髙度，如果没有相关数据则值为null。

□    altitudeAccuracy：海拔高度的精度，以米为单位，数值越大越不精确。

□    heading：指南针的方向，0°表示正北，值为NaN表乐没有检测到数据。

□    speed：速度，即每秒移动多少米，如果没有相关数据则值为null。

在实际开发中，latitude和longitude是大多数Web应用最:常用到的属性。例如，以下代码将 在地阐上绘制用户的位置：

navigator.geolocation.getCurrentPosition{function(position){

drawMapCenteredAt{position.coords.latitude, positions.coords.longitude};

});

以上介绍的是成功回调函数。getCurrentPosition ()的第二个参数，即失败回调函数，在被调 用的时候也会接收到一个参数。这个参数是一个对象，包含两个属性:mes+sage和code。其中，message 属性中保存着给人看的文本消息，解释为什么会出错，而code属性屮保存着一个数值，表示错误的类 型：用户拒绝共李(1)、位置无效(2)或者超时(3)。实际开发中，大多数Web应用只会将错误消息 保存到日志文件中，而不一定会因此修改用户界面。例如：

navigator.geolocation.getCurrentPosition(function(position){

drawMapCenteredAt(position.coords.latitude, positions.coords.longitude);

}, function(error){

console.log("Error code: n + error.code); console.log(MError message: " + error.message);

));

getCurrentPosiLion()的第三个参数是一个选项对象，用于设定信息的类型。可以设置的选项 有三个：enableHighAccuracy是一个布尔值，表示必须尽可能使用最准确的位置信息；timeout是 以毫秒数表示的等待位置信息的最长时间；maximumAge表示上-次取得的坐标信息的有效时间，以毫 秒表示，如果时间到则重新取得新坐标信息。例如：

navigator.geolocation.getCurrentPosition(function(position){

drawMapCenteredAt(position.coords.latitude, positions.coords.longitude)；

}, function(error){

console.log{*'Error code: •• + error.code) ? console. log ("Error meaaage： '* + error .message)；

}，{

enableHighAccuracys true, timeout: 5000, xoaximiunAge: 25000

})/

 

这三个选项都是可选的，可以单独设置，也可以与其他选项一起设置。除非确实需要非常精确的信 息，否则建议保持enableHighAccuracy的false值(默认值)。将这个选项设置为true需要更长 的时候，而丑在移动没备上还会导致消耗更多电量。类似地，如果不需要频繁更新用户的位置信息，那 么可以将maximumAge设置为Infinity,从而始终都使用上一次的坐标信息。

如果你希®跟踪用户的位置，那么可以使用另一个方法watchPositionO。这个方法接收的参数 与getCurrent Posit ion ()方法完全相同。实际上，watchPosition ()与定时调用 getCurrentPositionU的效果相同。在第一次调用watchPosition (＞方法后，会取得当前位置，执 行成功回调或者错误回调。然后，watchPosition()就地等待系统发出位置已改变的信号(它不会自 己轮询位置)。

调用watchPosition ()会返问一个数值标识符，用于跟踪监控的操作。基于这个返M值可以取消 监控操作，只要将其传递给clearWatch()方法即可(与使用setTimeout U和clearTimeout {)类 似)。例如：

var watchld = navigator.geolocation.watchPosition(function(position){

drawMapCenteredAt(position.coords.latitude, positions.coords.longitude)；

}, function(error){

console.log("Error code： ” + error.code)j console.log("Error message： H + error.message)；

})；

clearWatch(watchld);

以上例子调用了 watchPosition (＞方法，将返回的标识符保存在了 watchld中。然后，又将 watchld传给了 clearWatch (),取消了监控操作。

支持地理定位的浏览器有〖E9+、Firefox 3.5+、Opera 10.6+、Safari 5+ x Chrome、iOS 版 Safari、Android 版WebKit。要了解使用地理定位的更多精彩抱例，请访问http://html5demos.com/geo。

25.4 File API

不能直接访问用户计算机中的文件，一直都是Web应用开发中的一大障碍。2000年以前，处理文 件的唯方式就是在表单中加人＜input type=-file•’＞字段，仅此而已。File API (文件API)的宗旨 是为Web开发人员提供一种安全的方式，以便在客户端访问用户计算机中的文件，并更好地对这些文 件执行操作。支持 File API 的浏览器有 IE 10+、Firefox 4+, Safari 5.0.5+, Opera 11.1+和 Chrome。

File API在表单中的文件输人字段的基础h, 乂添加丫一些直接访问文件信息的接U。HTML5在 DOM中为文件输人元素添加了一个files集合。在通过文件输人字段选择了--或多个文件吋，files 集合中将包含一组file对象，每个File对象对应着一个文件。每个File对象都有下列只读属性。

□    name：本地文件系统中的文件名。

□    size：文件的字节大小。

□    type：字符串，文件的MIME类型。

□    lastModifiedDate：字符串，文件上一次被修改的时间(只有Chrome实现了这个属性)。

举个例子，通过侦听change事件并读取files集合就可以知道选择的每个文件的信息：

var filesList = document .getElement.ById( * files-list"); EventUtil.addHandler(filesList, "change", function(event){

var files = EventUtil.getTarget(event).files, i = 0,

len = files.length;

while (i < len){

console.log(files[i]•name + " (" + filea[i].type +    + files[i]-size +

"bytes) "}

十；

)

});

FileAPlExampleOl .htm

这个例子把毎个文件的信息输出到了控制台中。仅仅这一项功能，对Web应用开发来说就已经是 非常大的进步了。不过，File API的功能还不止于此，通过它提供的FileReader类型甚至还可以读取 文件中的数据。

25.4.1 FileReader 类型

FileReader类堕实现的是一种异步文件读取机制。可以把FileReader想象成XMLHttpRequest， 区别只是它读取的是文件系统，而不是远程服务器。为丁读取文件中的数据，FileReader提供了如下 几个方法。

□    readAsText (file, encoding):以纯文本形式读取文件，将读取到的文本保存在result属 性中。第二个参数用于指定编码类型，是可选的。

□    readAsDataURL ( file}:读取文件并将文件以数据URI的形式保存在result屁性中。

□    readAsBinaryString(file):读取文件并将一个字符串保存在result属性中，字符串中的 每个字符表示一字节。

□    readAsArrayBuffer(file):读取文件并将一个包含文件内容的ArrayBuffer保存在 result属性中。

这些读取文件的方法为灵活地处理文件数据提供了极人便利。例如，可以读取图像文件并将其保存 为数据URI,以便将其麗示给用户，或者为了解析方便，可以将文件读取为文本形式。

由于读取过程是异步的，因此FileReader也提供了几个事件。其中最有用的三个事件是 progress, error和load,分别表示是否又读取f新数据、是否发生了错误以及是否已经读完了整个 文件。

每过50ms左右，就会触发一次progress事件，通过事件对象可以获得与XHR的progress事 件相同的信息(属性)：lengthComputable、loaded和total。另外，尽管可能没存包含全部数据， 但每次progress事件中都却以通过FileReader的result厲性读取到文件内容。

由于种种原因无法读取文件，就会触发error事件。触发error事件时，相关的信息将保存到 FileReader的error属性中。这个屑性中将保存一个对象，该对象只有一个属性code,即错误码。 这个错误码是1表示未找到文件，是2表示安全性错误，是3表示读取中断，是4表示文件不可读，是 5表示编码错误。

文件成功加载后会触发l oad事件；如果发生了 error事件，就不会发生load事件。以下是一个 使用上述三个事件的例子。

var filesLisc - document.getElementById("files-list"); BventUcil.addHandler(filesList,    * change", function(event){

var info = •n,

output = document.getElementById(■output"), progress = document.getElementById("progress■), files = EventUtil .getTarget (event:} • files, type = "default",

reader = new FileReader(}；

if (/image/.test(files[0].type)){ reader.readAsDataURL(files[0]}; type = "image"；

} else {

reader.readAsTexC(files(0]); type - "text";

}

reader .onerror = function。{

output.irmerHTML = "Could not read file, error code is " + reader.error.code;

}；

reader.onprogress = function(event){ if (event.lengthComputable){

progress.innerHTML = event.loaded + "/• + event-total；

}

)；

reader.onload - function()<

var html = "n;

switch(type){

case "image":

html = •<img src=\"" + reader.result + H\">B； break;

case "text":

html = reader.result； break；

}

output.innerHTML = html;

)；

});

FUeAPIExampleO2.htm

这个例子读取了表单字段中选择的文件，并将其内容拔示在了页面中。如果文件有MTM1类型，表 示文件是图像，因此在load事件中就把它保存为数据URI,并在页面中将这幅图像显示出来。如果文 件不是图像，则以字符串形式读取文件内容，然后如实在H面中显示读取到的内容。这里使用了 progress肀件来跟踪读取了多少字节的数据，而error事件则用于监控发生的错误。

如果想巾断读取过程，可以调用abort ()方法，这样就会触发abort事件。在触发load、error 或abort事件后，会触发另一个事件loadend。loadend事件发生就意味着已经读取完整个文件，或 者读取吋发生了错误，或者读取过程被中断。

25

 

实现File API的所有浏览器都支持readAsText <)和readAsDataURL ()方法。但IE10 PR 2并未 实现 readAsBinaryString ()和 readAsArrayBuf f er ()方法 0

25.4.2读取部分内容

有时候，我们只想渎取文件的一部分而不是全部内容。为此，File对象还支持一个slice()方法， 这个方法在Firefox中的实现叫mozSlice O ,在Chrome中的实现叫webkitSlice () , Safari的5.1及 之前版本不支持这个方法。slice()方法接收两个参数：起始字节及要读取的字节数。这个方法返冋_ 个Blob的实例,Blob是File类型的父类型。下面是一个通用的函数，町以在不同实现中使用sliceO 方法：

function blobSlice(blobz startByce, length){ if {blob.slice){

return blob.slice{startByte, length)；

} else if (blob.webkitSlice){

return blob.webkitSlice{startByte, length)； } else if (blob.mozSlice){

return blob.mozSlice{startByte, length);

} else {

return null;

}

}

Fi!eAPIExampleO3. htm

Blob类型有一个size属性和一个type属性，而i它也支持slice ()方法，以便进一步切割数 据。通过FileReader也可以从Blob中读取数据。下面这个例子只读取文件的32B内容c

var filesList - document.getElementByTd(* files-list");

EventUtil.addHandler(filesList# "change", function(event){

var info = ■",

output = document.getElementById{"output"), progress = document.getElementById("progress"), files = EventUtil.getTarget(event).files, reader = new FileReader{),

blob « blobSlice(files[0]# 0, 32)；

if (blob){

reader.readAsText(blob);

reader.onerror = function(){

output.innerHTML = "Could not read file, error code is ' + reader.error.code；

}；

reader.onload = function(){

output.innerHTML = reader.result;

}；

} else {

alert("Your browser doesn' t support slice{).");

}

});

FileAPIExampleO3. htm

只读取文件的--部分可以节省时间，非常适合只关注数据中某个特定部分(如文件头部)的情况。

25.4.3 对象 URL

对象URL也被称为blob URL,指的是引用保存在F+ile或Blob中数据的URL。使用对象URL的 好处是可以不必把文件内容读取到JavaScript巾而直接使用文件内容。为此，只要在需要文件内容的地 方提供对象URL即可。要创建对象URL,可以使用window.URL.createOt＞jectURL()方法，并传人 File 或 Blob 对象。这个方法在 Chrome 中的实现叫 window.webkitURL.createObjectURL()，因 此可以通过如下函数來消除命名的差异：

function createObjectURL(blob){ if (window.URL){

return window.URL.createObjectURL(blob)；

} else if (window.webkitURL){

return window.webkitURL.createObjectURL(blob);

} else {

return null;

}

}

FileAPIExampleO4. htm

这个函数的返M值是一个字符串，指向一块内存的地址。因为这个字符串是URL,所以在DOM中 也能使用=例如，以下代码可以在页面中敁示一个阁像文件：

var filesList = document.getElGanentByld{"files-list");

'    EventUtil.addHandler(filesList, "change”， function(event){

var info = 0",

output = document.getElementById("output"), progress = document.getElemenCByld{"progress ° J, files = EventUt il.getTarget(event).files, reader = new FileReader(),

url = createObjectURL(files【0]);

if (url){

if (/image/.test(files 10].type)){

output.innerHTML « "<img arce\"" + url + w\">"；

} else {

output.innerHTML = "Not an image.";

}

} else {

output.innerHTML = 'Your browser doesn’t support object URLs.";

}

});

Fi!eAPIExampleO4. htm

直接把对象URL放在＜img＞标签中，就省去了把数据先读到JavaScript中的麻烦。另一方面，＜img＞ 标签则会找到相应的内存地址，直接读取数据并将阁像显示在页面中。

如果不再需要相应的数据，最好释放它占用的内容。但只要宥代码在引用对象URL,内存就不会释 放。要手•释放内存，可以把对象URL传给window.URL.revokeOjbectURLQ (在Chrome中是 window.webkitURL.revokeObjectURL () )o要兼容这两种方法的变现，可以使用以下函数：

function revokeObjeccURL(url){ if (window.URL){

window.URL.revokeObjecCURL(url)；

} else if (window.webkitURL){

window. webkitURL.revokeObjectURL(url)；

页面卸载时会肖动释放对象URL占用的内存。不过，为了确保尽可能少地占用内存，最好在不需 要某个对象URL时，就马上手工释放其占用的内存。

支持对象URL的浏览器有IE10+、Firefox 4和Chrome。

25.4.4读取拖放的文件

围绕读取文件信息，结合使用HTML5拖放API和文件API,能够创造出令人瞩H的用户界面：在页 面卜.创建了自定义的放》目标之后，你可以从桌面.k把文件拖放到该H标。与拖放一张图片或者一个链接 类似，从桌面上把文件拖放到浏览器中也会触发drop事件。而旦可以在event. dataTransfer. files 中读取到被放置的文件，当然此时它是一个File对象，与通过文件输人宇段取得的File对象-样。

下面这个例子会将放置到页面中自定义的放置5标中的文件信息显示出来：

var droptarget = document.getElementByld( "droptargec");

function handleEvent(event){ var info = ■*,

output = document.getElementByld(*output"), files, i, len；

EventUtil.preventsDefault (event);

if

 

(event.type == "drop"){

files = event.dataTransfer.files；

i = 0;

len = files.length;

while (i < len){

info += files [i] .nair.e + " (■，+ files[i] .type + " r " + files[i] .size + "bytes)<br>"；

i++;

}

output.innerHTML= info；

}

)

EventUtil.addHandler(droptarget, "dragenter", handleEvent); EventUtil.addHandler{droptarget, "dragover", handleEvent)； EventUtil.addHandler{droptarget, "drop”，handleEvent);

Fi!eAPIExampleO5. htm

与之前展示的拖放示例~样，这里也必须取消dragenter、dragover和drop的默认行为。在 drop事件中，町以通过event.dataTransfer. files读取文件信息。还有-•种利用迭个功能的流行 做法，即结合XMLHttpRequest和拖放文件来实现上传。

25.4.5使用XHR上传文件

通过File API能够访问到文件内容，利用这一点就可以通过XHR直接把文件上传到服务器。当然 啦，把文件内容放到send()方法中，再通过POST请求，的确很容易就能实现上传。但这样做传递的 是文件内容，因而服务器端必须收集提交的内容，然后再把它们保存到另一个文件中。其实，更好的做 法是以表单提交的方式来h传文件。

这样使用FormData类型就很容易做到/(第21章介绍过FormData )。首先,要创逮一个FormData 对象，通过它调用append()方法并传人相应的File对象作为参数。然后，再把FormData对象传递 给XHR的send ()方法，结果与通过表单上传--•模一样。

, var droptarget = document.getElementById("droptargetw);

function handleEvent(event){ var info = ""z

output = document.getElementByld("output"), data, xhr, files, i, len?

EventUtil .preventDefaulfc (event"

if (event.type == "drop"){ data =» new FormData (); files = event.dataTransfer.files; i = 0；

len = files.length；

while (i < len){

data►append<"fileM + i, £iles[i]);

i++;

}

xhr = new XMLHttpRequesfc();

xhr.open("post", "FileAPIExample06Upload.php"z true)； xhr.onreadystatechange = function(){

if (xhr.readyState == 4){

alert(xhr.responseText);

}

}；

xhr.send(data)；

}

}

EventUtil.addKandler{droptarget, "dragenter"z handleEvent);

EventUtil.addHandler(droptarget, "dragover", handleEvent)?

EventUtil.addHandler(droptargety "drop", handleEvent);

FileAPIExampleO6. htm

这个例子创建一个FormData对象，与每个文件对应的键分別是fileO、filel, file2这样的格 式。注意，不用额外写任何代码，这些文件就可以作为表单的值提交。而且，也不必使用FileReader, 只要传人File对象即可。

使用FormData上传文件，在服务器端就好像是接收到了常规的表单数据一样，--切按部就班地处 理即可。换句话说，如果服务器端使用的是PHP,那么$_FILES数组中就会保存着上传的文件。支持 以这种方式上传文件的浏览器有Firefox 4+、Safari 5+和Chrome。

25.5 Web 计时

页面性能一立都是Web开发人员最关注的领域。但直到最近，度量页面性能指标的唯一方式，就 是提髙代码复杂程度和巧妙地使用JavaScript的Date对象。Web Timing API改变f这个局面，让开发 人员通过JavaScript就能使用浏览器内部的度ft结果，通过直接读取这些信息可以做任何想做的分析。 与本章介绍过的其他API不同，Web Timing API文际上巳经成为了 W3C的建议标准，只不过目前支持 它的浏览器还不够多。

Web计吋机制的核心是window.performance对象。对页面的所有度量信息，包括那些规范中已 经定义的和将来才能确定的，都包含在这个对象里面。Web Timing规范一开始就为performance对象 定义了两个属性。

其中，performance.navigation属性也是一个对象，包含着与页面导航有戈的多个属性，如下所示。

□    redirectCount：页面加载前的重定向次数。

□    type：数值常埔，表示刚刚发生的导航类型。

■    performance.navigation.TYPE_NAVTGATE (0):页面第一次加载。

■    performance.navigation.TYPE_RELOAD (1)：页面重载过。

■    performance.navigation.TYPE_BACK_FORWARD (2)：页面是通过“后退”或“前进”按 钮打开的。

另外，performance. timing属性也是一个对象，但这个对象的属性都是时间戳(从软件纪元开 始经过的毫秒数)，不同的事件会产生不同的吋间值。这些属性如下所示。

□    navigationStart:开始导航到当前页面的吋间。

□    unloadEventStart:前' -个页面的unload事件JF•始的时间。担只有在前一个页面与当前页 面来自同一个域时这个属性才会有值；否则，值为0。

□    un 1 oadEventEnd:前一个页面的unload事件结束的时间。但只有在前一个页面与当前页面 来自同一个域时这个属性才会有值；否则，值为0。

□    redirectstart：到当前页面的重定向开始的时间。但只有在重定向的贞面来自同一个域时这 个属性才会有值；否则，值为0。

□    redirectEnd：到当前页面的重定向结束的时间。但只有在重定向的页面来自同一个域时这个 厲性方会有值；否则，值为0。

□    fetchStart:开始通过HTTP GET取得页面的时间。

□    domainLookupStart:开始査询当前页面DNS的时间。

□    domainLookupEnd:査询当前页面DNS结束的时间。

□    connectStart：浏览器尝试连接服务器的时间。

□    connect End：浏览器成功连接到服务器的时间。

□    secureConnectionStart:浏览器尝试以SSL方式连接服务器的时间。不使用SSL方式连接 时，这个属性的值为0。

□    requestStart：浏览器开始请求页面的时间。

□    responseStart:浏览器接收到页面第一字节的时间。

□    responseEnd：浏览器接收到页面所有内容的时间。

□    domLoading: document. readyState 变为” loading"的时间o

□    domlnteractive： document.readyState interactive"

□    domContentLoadedEventStart:发生 DOMContentLoaded 事件的时间 o

□    domContentLoadedEventEnd: DOMContentLoaded事件已经发生且执行完所有事件处理程 序的时间。

□    domComplete： document.readyState 变为"complete"的时间o

□    loadEventStart:发生load事件的时间。

□    loadEventEnd： load事件已经发生i执行完所有事件处理程序的时间。

通过这些时间值，就可以全面了解页面在被加载到浏览器的过程中都经历了哪些阶段，而哪些阶段 可能是影响性能的瓶颈。给大家推荐-个使用Web Timing API的绝好示例，地址是 <http://webtimingdcmo.appspot.eom/o>

支持Web Timing API的浏览器布IE10+和Chrome。

25.6 Web Workers

随着Web应用复杂性的与日俱增，越來越复杂的计算在所难免。长时间运行的JavaScript进程会导 致浏览器冻结用户界面，让人感觉屏幕“冻结”了。Web Workers规范通过让JavaScript在后台运行解决 了这个问题。浏览器实现Web Workers规范的方式冇很多种，可以使用线程、后台进程或者运行在其他 处理器核心上的进程，等等。具体的实现细节其实没冇那么重要，重要的是开发人员现在可以放心地运 行JavaScript,而不必担心会影响用户体验Z。

0 前支持 Web Workers 的浏览器有 IE10+、Firefox 3.5+、Safari 4+、Opera 10.6+、Chrome 和 iOS 版 的 Safari。

25.6.1 使用 Worker

实例化Worker对象并传人要执行的JavaScript文件名就可以创建一个新的Web Worker。例如： var worker = new Worker("stufftodo.js");

这行代码会导致浏览器下载stufftodo.js,但只有Worker接收到消息才会实际执行文件中的代 码。要给Worker传递消息，可以使用postMessage ()方法(与XDM中的postMessage ()方法类似)：

worker.postMessage( “start! ")?

消息内容可以是任何能够被序列化的值，不过与XDM不同的是，在所有支持的浏览器中， postMessage ()都能接收对象参数(Safari 4是支持Web Workers的浏览器中最后一个只支持字符串参 数的)。因此，可以随便传递任何形式的对象数据，如下面的例子所示：

worker.postMessage({ type： "command", message： * start!"

一般来说，可以序列化为JSON结构的任何值都町以作为参数传递给postMessageOo换句话说, 这就意味着传人的值是被复制到Worker中，而非直接传过去的(与XDM类似)。

Worker是通过message和error事件与页面通信的。这里的message事件与XDM中的message 事件行为相同，来自Worker的数据保存在event.data中。Worker返回的数据也可以是任何能够被序 列化的值：

worker.onmessage = funct i on(evont){ var data = event.data；

//对数据进行处理

}

Worker不能完成给定的任务时会触发error事件。具体来说，Worker内部的JavaScript在执行过 程中只要遇到错误，就会触发error事件。发生error事件时，事件对象中包含=个属性：filename, lineno和message,分别表示发生错误的文件名、代码行号和完整的错误消息。

worker.onerror = function(event){

console.log("ERROR: ” + event.filename + " {■+ event.1ineno + "): " + event.message);

)；

建议大家在使用Web Workers时，始终都要使用onerror事件处理程序，即使这个函数(像上面 例子所示的)除f把错误记录到日志中什么也不做都可以。否则，Worker就会在发生错误吋，悄无声息 地失败了。

任何时候，只要调用terminate (＞方法就町以停止Worker的工作。而且，Worker巾的代码会立即 停止执行，后续的所有过程都不会再发生(包括error和message事件也不会再触发)。

worker .terminate {) ?    //立即停止 Worker 的工作

25.6.2 Worker全局作用域

关于Web Worker，最重要的是要知道它所执行的JavaScript代码完全在另一个作用域中，与当前网 页中的代码不共享作用域。在Web Worker中，同样有一个全局对象和其他对象以及方法。但是，Web Worker中的代码不能访问DOM,也无法通过任何方式影响页面的外观。

Web Worker中的全局对象是worker对象本身。也就是说，在这个特殊的全局作用域中，this和 self引用的都是worker对象。为便于处理数据，Web Worker本身也是一个最小化的运行环境。

□最小化的 navigator 对象，包括 onLine、appName、appVersion、userAgent 和 platform 属性；

□只读的location对象；

□    setTimeout ()、setlnterval ()、clearTimeout ()和 clear工nterval ()方法；

□    XMLHttpRequest 构造涵数0

显然，Web Worker的运行环境与贞面环境相比，功能是相当有限的。

当K面在worker对象上调用postMessage (＞时，数据会以异步方式被传递给worker,进而触发 worker中的message事件。为了处理来H页面的数据，同样也®要创建一个onmessage事件处理 程序。

//Web Worker内部的代码

self.onmessage = function(event){ var data = event.data;

//处理数据

｝；

大家看清楚，这里的self引用的是Worker全局作用域中的worker对象(与页面中的Worker对 象不同一个对象)。Worker完成T•作后，通过调用postMessage (＞可以把数据冉发回页面。例如，下 面的例子假设需要Worker对传人的数组进行排序，而Worker在排序之后又将数组发回了页面：

/ / Web Worker内部的代码

self.onmessage = function(event){ var data = event.data/

//别忘了，默认的sort ()方法只比故字符串

data.sort(function(a, b){ return a - bj

}>/

self.postMessage(data)/

}；

Web WorkerExampleO 1 .js

传递消息就是贞面与Worker相互之间通信的方式d在Worker中调用postMessage ()会以异步方 式触发页面中Worker实例的message事件。如果页面想要使用这个Worker,可以这样：

//在页面中

var data = [23,4,7,9,2，14,6,651，87,41，7798,24],

worker « new Worker("WebWorkerExampleO1.js");

worker.onmessage function(event){ var data = event.data；

//对排序后的数纽进行操作

｝；

//将数组发送给worker排序 worker.postMessage(data);

JVebWorkerExampleO! .htm

排序的确是比较消耗时间的操作，因此转交给Worker做就不会阻塞用户界面了。另外，把彩色图 像转换成灰阶图像以及加密解密之类的操作也是相当费吋的。

在Worker内部，调用close()方法也可以停止工作。就像托页面中调用terminate()方法一样， Worker停止T作后就不会再有事件发生了。

//Web Worker内部的代码 self.close();

25.6.3包含其他脚本

既然无法在Worker中动态创建新的＜SCriPt＞元素，那是不是就不能向Worker中添加其他脚本了

呢？不是，Worker的全局作坩域提供这个功能，即我们可以调用iroportScripts ◦方法。达个方法接

收一个或多个指向JavaScript文件的URL。每个加载过程都是异步进行的，因此所有脚本加载并执行之

后，imporiScripts 0才会执行。例如：

//Web Worker内部的代码

importScripts("filel.js", ”file2.js”)；

即使file2.jS先于filers下载完，执行的时候仍然会按照先后顺序执行。而且，这些脚本是 在Worker的全局作用域中执行，如果脚本中包含与页面有关的JavaScript代码，SP么脚本可能无法正确 运行。请记住，Worker中的脚本-般都具有特殊的用途，不会像页面中的脚本那么功能宽泛。

25.6.4 Web Workers 的未来

Web Workers规范还在继续制定和改进之屮。本节所讨论的Worker LI前被称为“专用Worker" (dedicatedworker),因为它们是专门为某个特定的页面服务的，不能在页面间共享。该规范的另外一个 概念是“共享Worker” (shared worker),这种Worker可以在浏览器的多个标签中打开的同一个页面间 共享。虽然Safari 5、Chrome和Opera 10.6都实现了共享Worker,但由于该规范尚未完稿，因此很可能 还会有变动。

另外，关于在Worker内部能访问什么不能访问什么，到如今仍然争论不休。有人认为Worker应该 像页面一样能够访问任意数据，不光是XHR,还有localStroage、sessionSLorage、Indexed DB, Web Sockets、Server-Send Events等。好像支持这个观点的人更多一些，因此未來的Worker全局作用域 很可能会衣更大的空间。

25.7小结

与HTML5同时兴起的是另外一批JavaScript API。从技术规范角度讲，这批API不厲于HTML5, 但从整体h可以称它们为HTML5 JavaScript API。这些API的标准有不少虽然还在制定当中，但已经得 到了浏览器的广泛支持，因此本章®点讨论了它们。

□    reouestAnimationFrame ():是一个着眼于优化JavaScript动画的API,能够在动画运行期间 发出信号。通过这种机制，浏览器就能够U动优化屏幕重绘操作。

□    Page Visibility API：让开发人员知道用户什么时候正在看着页面，而什么时候页面是隐藏的。

口 Geolocation API:在得到许可的情况下，可以确定用户所在的位置3在移动Web应用中，这个 API非常®要而且常用。

□    File API:可以读取文件内容，用于显示、处瑰和上传。与HTML5的拖放功能结合，很容易就 能创造出拖放I:传功能

□    Web Timing：给出了页面加载和渲染过程的很多信息，对性能优化非常有价值。

□    Web Workers：可以运行异步JavaScript代码，避免阻塞川户界面。在执行复杂计算和数据处理 的时候，这个API 1h常有用；要+然，这些任务耗则会占用很长时间，ffi则会导致用户无法与 页面交互。

ECMAScript Harmony

12004年Web开发重新焕发生机的大背景下，浏览器开发商和其他相关组织之间进行了一系列 会谈，讨论应该如何改进JavaScript。ECMA-262第四版的制定工作就建立在两大相互竞争的

提案基础上：一个是Netscape的JavaScript 2.0,另一"T•是Microsoft的JScript.NET。各方抛开在浏贤器 领域的竞争，聚集在ECMA麾下，提出了希望能以JavaScript为蓝本设计出一门新语言的建议方案。最 初的工作草案叫做ECMAScript4,而且很长时间以来，它好像就是JavaScript的下一个版本。后来，一 个叫ECMAScript3.1反提案的加人，令JavaScript的未来再次充满了疑问。在反复争论之后,ECMAScript

3.1成为了 JavaScript的下一个版本，而且未来的工作成果-代号Harmony （和谐），将力争让

ECMAScript 4 向 ECMAScript 3.1 靠拢。

ECMAScript 3.1最终改名为ECMAScript 5,很快就完成了标准化。ECMAScript 5的详细内容本书 已经介绍过丫。ECMAScript 5的标准化工作一完成，Harmony立即被提上日程。Harmony与ECMAScript 5的指导思想比较一致，就是只进行增量调整，不彻底改造语言。虽然到2011年的时候，Harmony,也 就是未来的ECMAScript 6,还没宥全部制定完成，但其中的几个部分已经尘埃落定。本附录所要介绍 的就是那興将来肯定能进人最终规范的部分。不过也提醒一下大家，在将来的实现中，这些内容的细节 有可能与你在这里看到的不一样。

A.1 一般性变化

Harmony为ECMAScript引人了一些基本的变化。对这门语言来说，这些S然不算是大的变化，但 的确也弥补了它功能上的一些缺憾。

A.1.1常量

没有正式的常量是JavaScript的一个明显缺陷„为了弥补这个缺陷，标准制定者为Harmony增加了 用const关键字声明常量的语法。使用方式与var类似，但const声明的变貸在初始赋值后，就不能 再重新赋值了。来看一个例子。

const MAX_SIZE = 25;

可以像声明变盘一样在任何地方声明常《。但在同一作用域中，常量名不能与其他变量或函数名重 名，因此下列声明会导致错误：

const FLAG = true；

var FLAG = false; //错误!

除了值不能修改之外，可以像使用任何变景一样使用常ft。修改常景的值，不会有任何效果，如下 所示：

const FLAG = true;

FLAG = false; alert (FLAG);"正确

支持常量的浏览器有 Firefox、Safari 3+、Opera 9*+•和 Chrome。在 Safari 和 Opera 中，const 与 var 的作用一样，因为前者定义的常量的值是可以修改的。

A.1.2块级作用域及其他作用域

本书时不时就会提醒读者一句：JavaScript没有块级作用域。换句话说，在语句块中定义的变量与 在包含函数中定义的变量共享相同的作用域。Harmony新增了定义块级作用域的语法:使用let关键字。

与const和var类似，可以使用let在任何地方定义变量并为变量赋值。区别在于，使用let定 义的变量在定义它的代码块之外没有定义。比如说吧，下面是一个非常常见的代码块：

for (var i=0； i < 10； i+十){

//执行茱些操作

}

alert(i)； //10

在t面的代码块中，变足i是作为代W块所在函数的局部变量来声明的。也就是说，在for循环 执行完毕后，仍然能够读取i的值。如果在这里使用let代替var,则循环之后，变量i将不复存在。 看下面的例子。

for (let i*0; i < 10; i++) {

//执行莱些操作

}

alert (i), //嫌误！变量i没有定义

以上代码执行到最A7—行的时候，就会出现错误，因为for循环一结束，变量i就已经没有定义 了。因为不能对没有定义的变量执行操作，所以发生错误是自然的。

还有另外一种使用let的方式，即创建let语句，在其中定义只能在后续代码块中使用的变量， 像下面的例子这样：

var num = 5;

let (num=10, multiplier=2){

alert(num * multiplier)； //20

}

alert (num) ； "5

以上代码通过let语句定义了一个区域，这个区域中的变量num等丁• 10, multiplier等于2。 此时的num覆盖了前面用var声明的同名变量，因此在let语句块中,num乘以multiplier等于20。 而出了 let语句块之后，mm变量的值仍然是5。这是因为let语句创建了自己的作用域，这个作用域 里的变量与外面的变量无关。

使用同样的语法还可以创建let表达式，其中的变量只在表达式中有定义。再看一个例子。

var result = let(num=10# multiplier=2) num * multiplier; alert(result)? //20

这里的let表达式使用两个变ft计算后得到一个值，保存在变量result中。执行表达式之后，num 和multiplier变葡就不存在了。

在JavaScript中使用块级作用域，可以更精细地控制代码执行过程中变ft的存废。

A.2函数

大多数代码都是以函数方式编写的，因此Harmony从几个方面改进了函数，使其更便于使用。与 Harmony中典他部分类似，对函数的改进也集中在开发人员和实现人员共同面临的难题上。

A.2.1剩余参数与分布参数

Harmony中不再有arguments对象，因此也就无法通过它来读取到未声明的参数。不过，使用剩 余参数(restarguments)语法，也能表示你期待给函数传人可变数量的参数。剩余参数的语法形式是三 个点后跟一个标识符。使用这种语法可以定义可能会传进来的更多参数,然后把它们收集到一个数组中。 来看一个例子。

function sum{numl, num2, ...nums}{ var result = numl + num2；

for (let i=0, len=nums.length； i < len? i++){ result += nums(i];

}

return result；

}

var result = sumd, 2, 3, 4, 5, 6);

以上代码定义了一个sm()函数，接收至少两个参数。这个函数还能接收更多参数，而其余参数都 将保存在nums数组中。与原来的arguments对象不同，剩余参数都保存在Array的一个实例中，因 此町以使用任何数组方法来操作它们。另外,即使并没有多余的参数传人函数，剩余参数对象也是Array 的实例。

与剩余参数紧密相关的另一种参数语法是分布参数(spread arguments)。通过分布参数，可以向函 数中传人一个数组，然后数组中的元素会映射到函数的每个参数上。分布参数的语法形式与剩余参数的 语法相同，就是在值的前面加三个点。唯一的区别是分布参数在调用函数的时候使用，而剩余参数在定 义函数的时候使用„比如，我们可以不给3um()函数-个一个地传人参数，而是传人分布参数：

var result = sum(...(1, 2, 3, 4# 5, 6]>；

在这里，我们将一个数组作为分布参数传给了 sum{)函数。以上代码在功能k与下面这行代码等价: var result = sum. apply (this, [1, 2, 3, 4, 5, 6";

A.2.2默认参数值

ECMAScript函数中的所有参数都是可选的，因为实现不会检査传人的参数数量。不过，除了手工

检卉传人了哪个参数之外，你还可以为参数指定默认值。如果岡用函数时没有传人该参数，那么该参数 就会使用默认值。

要为参数指定默认值，可以在参数名后面直接加上等于号和默认值，就像下面这样：

function sum(n.uml, num2=0) { return numl + num2；

}

var result1 = sum(5); var result2 = sum(5, 5);

这个sun (>函数接收两个参数，但第二个参数是可选的，因为它的默认值为0。使用可选参数的好 处是开发人员不用再去检査是否给某个参数传人了值，如果没有的话就使用某个特定的值。默认参数值 帮你解除了这个困扰。

A.2.3生成器

所谓生成器，其实就是一个对象，它每次能生成一系列值中的一个。对Harmony而言，要创建生成 器，可以让闲数通过yield操作符返回某个特殊的值。对于使用yield操作符返回值的函数，调用它 时就会创建并返回一个新的Generator实例。然后，在这个实例上调用next ()方法就能取得生成器 的第一个值。此时，执行的是原来的函数，但执行流到yield语句就会停止，只返回特定的值。从这 个角度看，yield与return很相似。如果再次调用next <)方法，原来涵数中位于yield语句后的代 码会继续执行，直到洱次遇见yield语句时停止执行，此时再返回一个新值3来看下面的例子。

function myNumbers(){

for (var i=0; i < 10； i++){

yield i * 2；

}

)

var generator = myNumbers{)；

try {

while(true){

document .write (generator, next () + "<br />”；

}

} catch(ex){

//有意没有写代码

} finally {

generator.close{);

}

调用myNumbers {)函数后，会得到一个生成器。myNumbers ()兩数本身非常简单，包含一个每次 循环都产生一个值的for循环。每次调用next U方法都会执行一次for循环，然后返回下一个值。第 一个值是0,第二个值是2,第三个值是4,依此类推。在myNumbers ()函数完成退出而没有执行yield 语旬时(摄后一•次循环判断i不小于10的时候)，生成器会抛出Stopiteration错设。因此，为了输 出生成器能产生的所有数值，这里用一个try-catch结构包装了一个while循环，以避免出错时屮断 代码执行，

如果不再需要某个生成器，最好是调用它的close G方法。这样会执行原始函数的其他部分，包括 try-catch相关的finally语句块。

在需要一系列值，而每一个值又与前-个值存在某种关系的情况下，可以使用生成器。

A.3数组及其他结构

Harnumy的另一个重点是数组。数组是JavaScript使用最频繁的一种数据结构，因此定义一些更直 观更方便地使用数组的方式，绝对是改进这门语言时最优先考虑的事。

A.3.1迭代器

迭代器也是一个对象，它能迭代一系列值并每次返回其中一个值。想象一下使用for或for-in循 环，这时候就是在迭代一批值，而且每次操作其中的一个值y迭代器的作用相同，只不过用不着使用循 环了。Harmony为各种类型的对象都定义了迭代器。

要为对象创建迭代器，可以调用Iterator构造闲数，传人想要迭代其值的对象。要取得对象中的 下一个值，可以调用迭代器的next U方法。默认情况卜\这个方法会返回一个数组。如果迭代的是数 组，那么返回数组的第一个元素是值的索引，如果迭代的是对象，那么返回数组的第一个元素是值的属 性名；返冋数组的第二个元素是值本身。如果所有值都已经迭代了一遍，则再调用next (}会抛出 Stoplteration错误。看下面这个例子。

var person = {

name: "Nicholas' age： 29

)；

var iterator = new Iterator(person);

try {

while(true)<

let value = iterator.next()；

document.write(value.join(":") + *<br>"):

}

} catch(ex){

//有意没有写代码

}

以上代码为person对象创建了一个迭代器。第一次调用next()方法，返回数组[” name-, -Nicholas-],第二次调用返冋数组[” age' 29]。以上代码的输人结果为：

name:Nicholas age:29

如果为非数组对象创建迭代器，则迭代器会按照与使用for-in循环一样的顺序，返0对象的每个 属性。这就意味着迭代器也只能返回对象的实例屈性，而且返回属性的顺序也会因实现而异。为数组创 建的迭代器也类似，即按数组元素顺序依次返凹值，下面是一个例子。

var colors ■ ["red", "green*, "blue1*]; var iterator - new Iterator(colors);

try {

while(true){

let value =5 iterator .next (J ;

document.write{value.join("：*) + -<br>");

}

} catch(fex){

)

以上代码的输出结果如F:

0 ：red 1：green 2:blue

如果你只想让nextO方法返回对象的属性名或者数组的索引值，可以在创建迭代器时为Iterator 构造函数传人第二个参数true,如下所示：

var iterator = new Iterator(colors, true)；

在这样创建的迭代器上每次调用next G方法，只会返回数组巾好个值的索引，而不会返回包含索 引和值数组。

如果想为自定义类型创建迭代器，需要定义一个特殊的方法__iterator_(>, 这个方法应该返回一个包含next ()方法的对象。当把自定义类塑传给Iterator构 造函数时，就会调用那个特殊的方法。

A.3.2数组领悟

所谓数组领悟(array comprehensions )，指的是用一组符合某个条件的值来初始化数组。Harmony 定义的这项功能借鉴了 Python屮流行的一个语言结构。JavaScript中数组领悟的基本形式如下：

array - [ value for each (variable in values) condition ]；

其中，value是实际会包含柱数组中的值，它源ft values数组。for each结构会循环values 中的每一个值，并将每个值保存在变量variable中。如果保存在variable巾的值符合condition 条件，就会将这个值添加到结果数组中。下面是一个例子。

//原始数组

var numbers =丨0,1,2,3,4'5,6'7,8'9'10];

//把所有元索复制到新数组

var duplicate = (i for each (i in numbers)J;

//只把偶数复制到新数纽

var evens = [i for each, (i in numbers) if (i % 2 == 0)];

//把每个数乘以2后的結果放到新数组中

var doubled = [i*2 for each (i in numbers}];

//把每个奇数乘以3后的結果放到新数组中

var tripledOdds = [i*3 for each (i in numbers) if (i % 2 > 0)];

在以上代码的数组领悟部分，我们使用变i迭代了 numbers中的所有值，而其巾一些语句给出了 条件，以筛选最终包含在数组中的结果。本质上讲，只要条件求值为true,该值就会添加到数组中。与 自己编写同样功能的for循环相比，数组领悟的语法稍有不同，但却更加简洁。Firefi«2+是唯一支持数

组领悟的浏览器，而且要使用这个功能，必须将type属性值指定为”application/

javascript;version=l.7"o

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-125.jpg)

 

数组领悟语法的values也可以是一个生成器或者一个迭代器。

A.3.3解构赋值

从一组值中挑出一或多个值，然后把它们分别賦给独立的变量，这也是一个很常见的需求。就拿迭 代器的next ()方法返回的数组来说，假设这个数组包含着对象中一个属性的名称和值。为了把这个属 性和值分别保存在各自的变量巾，需要写两个语句，如下所示。

var nextValue = ["color", "red"]; var name = nextValue(0]; var value = nextValue[1];

而使用解构赋僚(destructuring assignments )语法，用一条语句即可解决问题:

var [name, value] = ("color", "red"】； alert{name);    //"color"

alert(value)；    //"red"

在传统的JavaScript中，数组字面盘是不能出现在等于号(賦值操作符)左边的。解构賦值的这种 语法表示的是把等于号右边数组中包含的值，分别賦给等于号左边数组中的变量。结果就是变量name 的值为• color",变it value的值为"red■。

如果你不想取得数组中所有的值，可以只在数组字面景中给出对应的变量，比如：

var (, value] = ["color", "red"]； alert(value);    //"red"

这样就只会给变ft value赋值，值为《red»。

有了解构赋值，还可做点有创意的事儿，比如交换变量的值。在ECMAScript3中，要交换两个变 擞的值，-•般是要这样写代码的：

var valuel =5; var value2 = 10;

var temp = valuel; valuel = value2; value2 - temp?

利用解构后的数组赋值，可以省掉那个临时变最temp,比如:

var valuel = 5;

var value2 = 10;

(value2, valuel] = [valuel, value2]；

解构赋值同样适用于对象，看下面这个例子:

var person -- {

name： "Nicholas*, age： 29

var { name： personName, age: personAge } = person；

alert(personNameJ; //"Nicholas" alert(personAge);    //29

与使用数组字面虽一样，看到等于号左边出现了对象字面盘，那就是解构赋值表&式。这条语句实 际上定义了两个变personName和personAge,它们分别得到了 person对象中对应的值。与数 组解构赋值一样，在对象解构赋值中也可以选择要取符的值，比如：

var { age： personAge } = person； alert(personAge); //29

以上代码只取得了 person对象巾age属性的值，将它赋给了变tt personAge。

A.4新对象类型

Harmony为JavaScript定义了几个新的对象类型。这几个新类型提供f以前只有JavaScript引擎才能 使用的功能。

A.4.1代理对象

Harmony为JavaScript引人了代理的概念。所谓代理(proxy),就是一个表示接口的对象，对它的 操作不一定作用在代理对象本身。举个例子，设置代理对象的一个属性，实际上可能会在另一个对象上 调用一个函数。代理是一种非常有用的抽象机制，能够通过API只公开部分信息，同时还能对数据源进 行全面控制。

要创建代理对象，可以使用Proxy, create (＞方法，传人一个handler (处理程序)对象和一个 可选的prototype (原型)对象：

var proxy = Proxy.create(handler);

//创建一个以myObject为原型的代理对象

var proxy = Proxy.create(handler, myObject);

其中，handler对象包含用于定义捕捉器(trap)的属性。捕捉器本身是函数，用于处理(捕捉) 原生功能，以便该功能能够以另一种方式来处理。要确保代理对象能够按照预期工作，至少要实现以下 7种基本的捕捉器。

□    getOwnPropertyDescriptor:当在代理对象上调用 Object .getOwnPropertyDescriptor (} 时调用的函数。这个函数以接收到的屈性名作为参数，返回属性描述符，或者在属性不存在时返 回 null。

□    getPropertyDescriptor：当在代理对象上调用 Object.getPropertyDescriptor ()时调 用的函数。(这是Harmony中的新方法。)这个函数以接收到的属性名作为参数，返回属性描述 符，或者在属性不存在时返回mill。

口 getOwnPropertyNaraes:当在代理对象 I:调用 Obj ect. getOwnPropertyNames ()时调用的 函数。这个函数以接收到的属性名作为参数，应该返回一个字符串数组。

□    get Proper tyNames:当在代理对象上调用Object .get Proper tyNames ()时调用的函数。

(这是Harmony中的新方法。)这个函数以接收到的属性名作为参数，应该返回一个字符串数组。

□    def ineProperty:当在代理对象上调用Object .defineProperty (＞时调用的函数。这个困 数以接收到的属性名和属性描述符作为参数。

□    delete：定义在对象属性上使用delete操作符时调用的函数。属性名以参数形式传进来，如 果删除成功则返回true,删除失败返凹false。

□    fix：当调用 Object. freeze ()、Object. seal (＞或 Object.preventExtensions ()时调 用的函数。当在代理对象上调用这几个方法时，返回undefined以抛出错误。

除了这7个基本的捕捉器，还有6个派生的捕捉器(derivedtrap)。与基本捕捉器不同，少定义一个 或几个派生捕捉器不会导致错误。每个派生的捕捉器都会覆盖一种默认的JavaScript行为。

□    has在对象上使用in操作符(例如"name" in object)时调用的兩数。以接收到的属性名作 为参数，返回true表示对象包含该属性，否则返回false。

□    hasown：在代理对象上调用hasOwnPropertyO方法时调用的函数。以接收到的属性名作为参 数，返回true表示对象包含该属性，否则返回false。

□    get：在读取属性吋调用的函数。这个函数接收两个参数，即包含被读属性的对象的引用及属性 名。这个对象引用可能是代理对象本身，也可能是继承了代理对象的对象。

□    sec：在写人属性时调用的函数。这个函数接收5个参数，即包含被写属性的对象的引用、属性名 和属性值。与get类似，这个对象引用可能是代理对象本身，也可能是继承了代理对象的对象。

□    enumerate：当代理对象被放在for-in循环中时调用的函数。这个函数必须返回一个字符串 数组，其中包含在for-in循环中使用的相应属性名。

□    keys:当在代理对象上调用Object.keys ()时调用的函数。与enumerate类似，这个兩数也 必须返回一个字符串数组。

在需要公开API,而同时乂要避免使用者直接操作底层数据的时候，可以使用代理。例如，假设你 想实现一个传统的栈数据类型。虽然数组可以作为找来使用，但你想保证人们只使用push(), pop() 和length。在这种情况下，就可以基于数组创建一个代理对象，只对外公开这三个对象成员。

*实验ES 6代理对象，这个实验在数纽的基砧上创建一个栈数*结构。

*代遭在此用于从接O过滤-puslV. -pop.和-length-之外的成资，让数紐成为一个纯粹的栈， *任何人不能直接操作其内容。

♦/

var Stack = {function(}{

var stack = [1,

allowed = ["push", "pop", "length"]；

return Proxy.create{ {

get: function(receiver, name){；

if (allowed.indexOf(name) > -1){

if(typeof stack[name] == "function"){

return stack[name].bind(stack)；

} else {

return stack[name];

}

} else {

return undefined；

n;

))；

var mystack = new Stack()；

mystack.push("hi"); mystack.push("goodbye");

console.log(mystack.length)；    //I

console. log(mystack(0]) ;    //未定义

console.log(mystack.pop{)); //"goodbye"

以上代码创建了一个构造函数Stack。但它没有使用this,而是返回了一个对数组操作进行包装 的代理对象。这个代理对象只定义了一个get捕捉器，该函数检测了一组允许的属性，然后才返回相应 的值。如果引用的是不被允许的属性，那么捕捉器就返回undefined;如果引用的是pUSh＜＞、pop() 和length,则一切正常。这里的关键是get捕捉器，它根据允许的成员过滤了对象的成员。如果该成 员是函数，就返回一个与底层数组对象绑定的函数，这样操作针对的就是数组而非代理对象。

A.4.2代理函数

除了创建代理对象之外，Harmony还支持创建代理函数(proxy fimction )。代理函数与代理对象的 区别是它可以执行。要创建代理困数，可以调用Proxy.createFunction( ＞方法，传人一个handler (处理程序*)对象、一个调用捕捉器函数和一个可选的构造函数捕捉器函数。例如：

var proxy = Proxy.createFunction(handler, function(){), function(){));

与代理对象一样，handler对象也有同样多的捕捉器。调用捕捉器函数是在代理函数执行(如 proxy ())时运行的代码。构造函数捕捉器是在用new操作符调用代理函数(如new proxy ())时运 行的代码。如果没有指定构造函数捕捉器，则使用调用捕捉器作为构造函数。

A.4.3映射与集合

Map类型，也称为简单映射，只有一个目的：保存一组键值对儿。开发人员通常都使用普通对象来 保存键值对儿,但问题是那样做会导致键容易与原生属性混淆。简单映射能做到键和值与对象属性分离， 从而保证对象属性的安全存储。以下是使用简单映射的几个例子。

var map = new Map();

map.set("name*, "Nicholas");

map.set("book■f "Professional JavaScript*);

console.log (map.has (■name■”； //true console.log(map.get("name"))； //"Nicholas"

map.delete{"name")；

简单映射的基本API包括get (＞、set()和delete()，每个方法的作用看名字就知道了。键可以 是原始值，也可是引用值。

与简单映射相关的是Set类型。集合就是-组不重复的元素。与简单映射不同的是，集合中只有键，

没有与键关联的值。在集合中，添加元素要使用add (>方法，检査元素是否存在要使用has <)方法，而

删除元素要使用delete ()方法。以下是基本的使用示例。

var set = new Set()? set.add(•name")；

console.log{set.has("name *)); //true set.delete(■name");

console.log(set.has("name")); //false

截止到2011年10月，规范中关于Map和set的内容还没有最后定稿。因此，在JavaScript引擎实 现该规范时，有些细节可能会发生变化。

A.4.4 WeakMap

WeakMap是ECMAScript中唯一一个能让你知道什么时候对象已经完全解除引用的类型。WeakMap 与简单映射很相似，也是用来保存键值对儿的。它们的主要区别在于，WeakMap的键必须是对象，而 在对象已经不存在时，相关的键值对儿就会从WeakMap中被删除。例如：

var key = {},

map = new WeakMap()；

map.set{key, "Hello!");

//解除对键的引用，从而測除该值 key = null;

至于什么情况下适合使用WeakMap,目前还不清楚。不过，Java中倒是有一个相同的数据结构叫 WeakHashMap;于是，JavaScript乂多了一种数据类型。

A.4.5 StructType

JavaScript—个最大的不足是使用一种数据类型表示所有数值。WebGL为解决这个问题引人了类型 化数组，而ECMAScript 6则引入了类型化结构，为这fj语言带来了更多的数值数据类型。结构类塑 (StructType )与C语言中的结构类似；在C语言中，可以把多个属性组合成一条记录。对于JavaScript 的结构类型，通过指定属性及其保存的数据类型，也可以创建类似的数据结构。早期的实现定义了以下 几种块类型。

□    uint8：无符号8位整数。

□    int8：有符号8位整数。

□    uintlS:无符号16位整数。

□    intl6：有符号16位整数。

□    uint32：无符号32位整数。

□    int32：有符号32位整数c

□    float32： 32位浮点数。

□    float64： 64位浮点数。

这些块类型都只能包含一个值。将来还有银在这8种类型基础上进一步扩展。

要创建结构类型的对象，可以使用new操作符调用StructType,传人对象字面量形式的属性定义。

var Size = new StructType({ width: uint32/ height: uint32 })；

以上代W创建了一个名为Size的新结构类型，该类型带有两个属性：width和height。这两个 属性都应该保存无符号32位整数。而变量Size实际上是一个构造函数，可以像使用对象的构造函数 一样使用它。要实例化这个结构类型，需要向构造函数中传人一个带属性值的对象字面量。

var boxSize = new Size({ width： 80, height: 60 }); console.log(boxSize.width)；    Z/80

console.log(boxSize.height)； //60

这样，就创建了 Size的一个宽为80、髙为60的实例。实例的属性可以被读写，但始终都必须包 含32位无符号整数。

将属性定义为另一个结构类型，可以得到更复杂的结构类型。例如：

var Location = new StructType({ x： int32r y： int32 ))；

var Box new StructType({ size： Size, location； Location })；

var boxlnfo = new Box({ size： { width：80, height：60 }, location: { x： 0, y: 0 })); console, log (boxlnfo. size, width) ； "80

这个例子创建了一个简单的结构类型Location,又创建了一个复杂的结构类型Box。Box的属性 本身也是结构类型。Box构造函数仍然接收对象字面量参数，以便为每个属性定义值，但它会检査传入 值的数据类型，以确保作为屑性值的数据类型正确。A.4.6 ArrayType

与结构类型密切相关的是数组类型。通过数组类型(ArrayType)可以创建一个数组，并限制数组 的值必须是某种特定的类型(与WebGL中的类型化数组很相似)。要创建新的数组类型，可以调用 ArrayType构造函数，并传人它应该保存的数据类型以及应该保存的元素数目。例如：

var SizeArray = new ArrayType(Size, 2);

var boxes = new BoxArray(E { width： 80, height: 60 }, { width： 50, height: 50 }])；

以上代码创建了一个名为SizeArray的数组类型，这个数组类型只能保存Size的实例，同时也 给数组分配了两个该实例的位置。要实例化数组类型，可以传人一个数组，其中包含应该转换的数据。 数据可以是字面量，只要该字面量能提升为正确的数据类型即可(比如在这个例子中，传入的字面量可 以提升为结构类型)。

A.5类

开发人员-直吵着要在JavaScript中增加一种语法，用于定义类似于Java的类。ECMAScript6最终 确实定义了这种语法。但JavaScript中的类只是一种语法糖，覆盖在目前基于构造函数和基于原型的方 式和类型之上。先看看下面的类型定义。

function Person(name, age){ this.name = name; this.age = age；

)

Person.prototype.sayName = function(){ alert(this.name)；

Person.prototype.getOlder s function(years)( this.age += years；

再看看使用新语法定义的类：

class Person (

constructor(name, age){ public name = name； public age = age；

}

sayName () {

alert (this.name);

)

getOlder(years){

this.age += years；

}

)

新语法以关键字class开头，然后就是类型名，而花括号中定义的是属性和方法。定义方法不必 再使用function关键字，有方法名和圆括号就可以。如果把方法命名为constructor,那它就是这 个类的构造函数(与前一个例子中的Person函数一样)。在这个类中定义的方法和属性都会添加到原 型上，具体来说，sayName<)和getOlder ()都是在Person.prototype上定义的。

在构造兩数中，public和private关键字用于创建对象的实例厲性。这个例子中的name和age 都是公有属性。

A.5.1私有成员

关于类语法的建议是默认支持私有成员的，包括实例中的私有成员和原型中的私有成员。private 关键字表示成员是私有的，不能在类方法之外访问。要访问私有成员，可以使用一种特殊的语法，即调 用private (>函数并传人this对象，然后再访问私有成员。例如，下面这个例子把Person类的age 改成为私有厲性：

class Person {

constructor(name, age){ public name = name;

private age = age；

}

sayName(){

alert(this.name)；

}

getOlder(years){

private(this).age += years；

这种用于访问私有成员的语法还没冇定论，将来很可能会改变。

A.5.2 getter 和 setter

新的类语法支持哀接为厲性定义getter和setter,从而避免丫调用Object • def ineProperty () 的麻烦。为屈性定义getter和setter与定义方法类似，只不过要在方法名前加上get和set关键 字。例如：

class Person {

constructor(name, age){ public name = name； public age = age?

private ianerTitle = get title(){

return inn白rTitlej

\>

set title(value){

IanerTitle = value;

)

}

sayName(){

alert(this.name)；

}

SetOlder(years){

this.age += years；

}

\>

这个Person类为title屑性定义了一■个getter和一"t- setter。这两个操作innerTitle变U 的函数都定义在广构造函数中。耍为原型属性定义getter和setter,语法相同，但要在构造函数外 部定义。

A.5.3继承

使用类语法而不是过去那种JavaScript语法，最大的好处是容易实现继承。有了类语法，只要使用

与其他语言相同的extends关键字就能实现继承，而不必去考虑借用构造函数或者原型连缀。例如：

class Employee extends Person { constructor(name, age){

super(name,age)；

\>

}

以卜-代码创建了一个新类Employee,它继承了 Person类。在简单的语法背后，已经动实现了 原型连缀，而i通过使用super ()函数，也正式支持了借用构造函数。从逻辑上看，上面的代码与下面 的代码是等价的：

function Employee(name, age){

Person.call(this, name, age);

Employee.prototype = new Person()?

除了这种风格的继承，类语法还允许直接将对象指定为其原型，方法就是用prototype关键字代 替 extends：

var basePerson = {

sayName: function(){

alert(this.name);

)<

getOlder： function(years){ this.age += years；

)

}；

class Employee prototype basePerson { constructor(name, age){

public name - name； public age = age；

)

}

这个例子将basePerson对象直接指定为Employee .prototype ,从而实现了与目前使用 Object. create ()实现的一样的继承。

A.6模块

模块(或者"命名空间”、“包”)是组织JavaScript应用代码的重要方法。每个模块都包含着独立于 其他模式的特定、独一无二的功能。JavaScript开发中曾出现过一些临时性的模块格式，而ECMAScript6 则对如何创建和管理模块给出了标准的定义。

模块在其自己的顶级执行环境中运行，因而不会污染导入它的全局执行环境。默认情况下.模块中 声明的所有变最、函数、类等都是该模块私有的。对于应该向外部公开的成员，可以在前面加上export 关键字。例如：

module MyModule {

//公开这些成员

export let myobject = {);

export function hello(){ alert("hello"); }；

//隐藏这些成员 function goodbye(){

}

}

这个模块公开了一个名为myobject的对象和一个名为hello <)的函数。可以在页面或其他模块

中使用这个模块，也可以只导人模块中的一个成员或者两个成员。导入模块要使用import命令：

//只年入 myobject

import myobject from MyModule?

console.log(myobject):

//导人所有公开的成员 import * from MyModule?

console.log(myobject); console.log(hello);

//列出要导入的成员名

import {myobject, hello) from MyModule； console.log(myobject)? console.log(hello)；

//不导入，直接使用模块

console.log(MyModule.myobject);

console.log(MyModule.hello);

在执行环境能够访问到模块的情况下，可以直接调用模块中对外公开的成员。导人操作只不过是把 模块中的个别成员拿到当前执行环境中，以便直接操作而不必引用模块。

外部模块

通过提供模块所在外部文件的URL,也可以动态加载和导人模块。为此，首先要在模块声明后面加 上外部文件的URL,然后再导人模块成员：

module MyModule from "mymodule.js"； import myobject from MyModule；

以上声明会通知JavaScript引擎卜"'载mymodule. js文件，然府从中加载名为MyModule的模块。 请读者注意，这个调用会阻塞进程。换句话说，JavaScript引擎在下载完外部文件并对其求值之前，不 会处理后面的代码。

如果你只想包含模块中对外公开的某些成员，不想把整个模块都加载进来，可以像下面这样使用 import 指令：

import myobject from "mymodule.js";

总之，模块就是-•种组织相关功能的手段，而且能够保护全局作用域不受污染。

附录

严格模式

ECMAScript5最早引人了 “严格模式”（strictmode）的概念。通过严格格式，可以在函数内部 选择进行较为严格的全局或局部的错误条件检测。使用严格模式的好处是可以提早知道代码中

存在的错误，及时捕获一些可能导致编程错误的ECMAScript行为。

理解严格模式的规则非常重要，ECMAScript的下一个版本将以严格模式为基础制定。支持严格模 式的浏览器包括丨E10+、Firefox4+、Safari 5.1+和 Chrome。

B.1选择使用

要选择进人严格模式，可以使川严格模式的编译指示（pragma ）,实际上就是一个不会赋给任何变 量的字符串：

"use strict*?

这种语法（从ECMAScript 3开始支持）可以向后兼容那些不支持严格模式的JavaScript引擎。支持 严格模式的引擎会启动这种模式，而不支持该模式的引擎就当遇到了一个未!8值的字符串字面会忽 略这个编译指示。

如果是在全局作用域中（函数外部）给出这个编译指示，则整个脚本都将使用严格模式。换句话说， 如果把带有这个编译指示的脚本放到其他文件中，则该文件中的JavaScript代码也将处于严格模式下。

也可以只在函数中打开严格模式，就像下面这样：

function doSomething(){ "use strict";

//其他代码

如果你没有控制页面中所有脚本的权力，建议只在需要測试的特定函数中开启严格模式。

B.2变量

在严格模式下，什么吋候创建变量以及怎么创建变童都是打限制的。首先，不允许意外创建全局变 量。在非严格模式下，可以像下面这样创建全局变量：

//未声明变量

//非严格模式：创建全扁变量 //严格模    据出 ReferenceError

message

 

Hello world!

 

即使message前面没有var关键字，即使没有将它定义为某个全局对象的属性，也能将message 创建为全局变童。但在严格模式下，如果给•一个没宥声明的变贵赋值，那代码在执行时就会抛出 ReferenceErroro

其次，不能对变量调用delete操作符。非严格模式允许这样操作，但会静默失败(返回false)。 而在严格模式下，删除变量也会导致错误。

//剩除变量

//非严格模式：静默失败

//严格構式：据出ReferenceError

var color = "red"; delete color?

严格模式下对变量也有限制。特别地，不能使用implements、interface、let、package、 private、protected、public、static和yield作为变量名。这些都是保留字，将来的ECMAScript 版本中可能会用到它们。在严格模式T,用以上标识符作为变量名会导致语法错误。

B.3对象

在严格模式F操作对象比在非严格模式F更容易导致错误。一般来说，非严格模式下会静默失败的 情形，在产格模式下就会抛出错误。因此，在开发中使用严格模式会加大早发现错误的可能性。

在下列情形下操作对象的属性会导致错误：

□为只读属性赋值会拋出TypeError;

□对不可配置的(nonconfigurable )的属性使用delete操作符会抛出TypeError;

□为不可扩展的(nonextensible)的对象添加属性会抛出TypeError。

使用对象的另一个限制与通过对象字面量声明对象有关。在使用对象字面量时，属性名必须唯一。 例如：

//重名属性

//非严格糢式：没有铋误，以第二个属性为准 //尸格模式：拋出语法错误

var person = {

name： "Nicholas', name: ■Greg"

}；

这里的对象person有两个属性，都叫name。在非严格模式下，person对象的name属性值是第 二个，而在严格模式下，这样的代码会导致语法错误。

B.4函数

首先，严格模式要求命名函数的参数必须唯一。以下面这个函数为例：

//重名参数

//非严格模式：没有蟾谈，只能访闪第二个参數 〃严格模式：拋出语法错误

function sum (num, nuro) {

//do something

}

在非严格模式下，这个函数声明不会抛出错误。通过参数名只能访问第二个参数，要访问第一个参 数必须通过arguments对象。

在严格模式下，arguments对象的行为也有所不同。在非严格模式下，修改命名参数的值也会反 映到arguments对象中，而严格模式下这两个值是完全独立的。例如：

//修改命名参数的值

//非严格模X:修改会反映到arguments中 //严格模式：修改不会反快到arguments +

function showValue(value){ value = "Foo"；

alert (value) ;    /"Foo*

alert (arguments [0]) ; //非严格模式：MFoou

//严格模式："Hi"

}

showValue("Hi"};

以上代码中，函数showValue ()只有一个命名参数value。调用这个函数时传人了一个参数”Hi”， 这个值赋给了 valueo而在涵数内部，value被改为"Foon。在非严格模式下，这个修改也会改变 arguments [0]的值，但在严格模式下，arguments [0]的值仍然是传人的值。

另—个变化是淘汰了 arguments .callee和arguments .caller。在非严格模式下，这两个属 性一个引用函数本身，一个引用调用函数。而在严格模式下，访问哪个属性都会抛出TypeErroro 例如：

"访问 arguments.callee

//非严格模式：没有问題

//严格模式：据出TypeError

function factorial(num){ if (num <= 1) {

return 1；

} else {

return num * arguments-callee(num-1}

}

}

var result=factorial(5);

类似地，尝试读写函数的caller属性，也会导致抛出TypeError。所以，对于上面的例子而言， 访问factorial .caller也会抛出错误。

与变摄类似，严格模式对兩数名也做出7"限制，不允许用implements、inter face、let、package、 private、protected、public、static 和 yield 作为函数名。

对函数的最后一点限制，就是只能在脚本的顶级和在函数内部声明函数。也就是说，在if语句中 声明函数会导致语法错误：

//在if语句中声明函数

//非严格模式：将轟数提升到if语句外部

//严格模式：抛出语法错误

if (true)<

function doSomething(){

"...

}

)

在非严格模式下，以上代码能在所有浏览器巾运行，但在严格模式F会导致语法错误。

B.5 eval ()

饱受话病的eval ()函数在严格模式下也得到了提升。最大的变化就是它在包含上下文中不再创建 变M或函数。例如：

〃使用eval<)创建定f

//非严格模式：弹出对诺框显示10

//严格樣式：译用alert (x)时会槐出ReferenceBrror

function doSomething(){ eval (Mvar x=l0” ； alert(x)；

}

如果是在非严格模式下，以上代码会在函数doSomethingO中创建一个局部变董x，然后alert () 还会显示该变量的值。但在严格模式下，在doSomethingO函数中调用evalU不会创建变景x,因此 调用alert ()会导致抛出ReferenceError,因为x没有定义。

可以在eval U中声明变量和函数，但这些变贵或函数只能在被求值的特殊作用域中有效，随活就 将被销毁。因此，以下代码可以运行，没有问题：

"use strict1* ；

var result = eval(uvar x=10, y=ll； x+y"}； alert(result); //21

这里在eval()中声明了变Mx和y，然后将它们加在一-起，返回了它们的和。于是，result变 fi的值是21,即x和y相加的结果。而在调用alert ()时，尽管x和y已经不存在了，result变量 的值仍然是有效的。

B.6 eval 与 arguments

严格模式已经明确禁止使用eval和arguments作为标识符，也不允许读写它们的值。例如：

//把eval和arguments作为变量引用

//非严鉻模式：没闪题，不出错

//严格模式：拋出语法错误

var eval = 10；

var arguments - "Hello world 111;

在非严格模式下，可以重写eval,也可以给arguments赋值。但在严格模式下，这样做会导致语 法错误。不能将它们用作标识符，意味者以下几种使用方式都会抛出语法错误：

口使用var声明；

□赋予另一个值；

□尝试修改包含的值，如使用

□用作函数名；

□用作命名的函数参数；

口在try-catch语句中用作例外名。

B.7 抑制 this

JavaScript中一个最大的安全问题，也是最容易让人迷茫的地方，就是在某些情况下如何抑制this 的值。在非严格模式下使用函数的apply ()或call ()方法时，null或undefined值会被转换为全局 对象。而在严格模式下，函数的this值始终是指定的值，无论指定的是什么值。例如：

//访问属性

//非严格模式：访问全局属性

//严格模式：拋出错误，囚为this的值为null

var color = "red";

function displayColor{){ alert(this.color);

}

displayColor.call(null);

以上代妈向displayColor. call <)中传入了 null，如果在是非严格模式下，这意味着函数的this 值是全局对象。结果就是弹出对话框显示-red，。而在严格模式下，这个函数的this的值是null,因 此在访问null的属性时就会抛出错误。

B.8其他变化

严格模式还有其他一些变化，希望读者也能留意。首先是抛弃了 with语句。非严格模式F的with 语句能够改变解析栋识符的路径，但在严格模式下，with被简化掉了。因此，在严格模式下使用with 会导致语法错误。

//with的语句用法 //非严格模式：允许 //严格模式：抛出语法错误

with(location){ alert(href);

}

严格模式也去掉了 JavaScript中的八进制字面量。以0开头的八进制字面#过去经常会导致很多错 误。在严格模式下，八进制字面世已经成为无效的语法了。

//使用八进制字面责 //非严格模式：值泠8 //严格模式：拋出语法镁谈

var value = 010?

本书前面提到过，ECMAScript5也修改了严格模式下parselnt()的行为。如今，八进制字面量在 严格模式下会被当作以0开头的十进制字面量。例如：

//使用parselnt{)解析八进制字面量 //非严格模式：值为8 //严格援式：伍为10

var value = parselnt("010");

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-127.jpg)

 

附录c

JavaScript 库

 

、疇& :v. . . •• 、w聽？:：

、:爵打

W-S-.

 

JavaScript库可以帮助我们跨越浏览器差异的鸿沟，并对复杂的浏览器功能提供更为简便的访问 方式。程序库存两种形式：通用库和专用库。通用JavaScript库提供了对常见浏览器功能的访

问，可以作为网站或者Web应用的基础。专用库则只做特定的事，仅用于网站或者Web应用的某呰部 分。本附录给出了这些库与其功能的概况，并提供了相关网站作为你的参考资源。

C.1通用库

通川JavaScript库提供横跨几个主题的功能。所有的通用库都尝试通过使用新API包装常见功能来 统一浏览器的接口、减小实现差异。某些API看上去与原生功能很相似，而另一件则完全不同。通用库 一般提供与DOM交互的功能、支持Ajax、同时还有一些协助常见任务的工具方法。

C.1.1 YUI

它是一个开源JavaScript与CSS库，以一种组件方式设计的。这个库不只有一个文件；它包含了 很多文件，提供各种不同的配置，让你可以按需载人。YUI （ YahooiUser Interface Library,雅虎用户 界面库）涵盖了 JavaScript的所有方面，从基本的工具及帮助函数到完善的浏览器部件。在雅虎有一支 专门的软件'E程师团队负贵YUI,他们提供f优秀的文档和支持。

□协议：BSD许可证

□网站：http://yuilibrary.com

C.1.2 Prototype

它是一个提供了常见任务API的开源库。最初是针对Ruby on Rails框架中的使用而开发的,Prototype 是类驱动的，旨在为JavaScript提供类定义和继承。因此，Prototype提供了很多类，用于将常见或复杂 功能封装为简单的API调用。Prototype只有一个单独的文件，可以很容易地放人任意页面。它是由Sam Stephenson撰写并维护的。

□协议：MIT 许可证或者是 Creative Commons Attribution-Share Alike 3.0 Unported

□网站：http://www.prototypejs.org/

C.1.3 Dojo Toolkit

Dojo Toolkit开源庫基于--种包系统建模，■■组功能组成一个包，可以按需载人。Dojo提供了范围 广泛的选项和配晋，几乎涵盖r你要用JavaScript做的任何事情。Dojo Toolkit由AJex Russell创违，并

由Dojo基金会的雇员和志愿者维护。

口协议：新BSD许可证或学术自由协议2.1版 □网站：http://www.dojotoolkit.org/

C.1.4 MooTools

MooTools是一个为了精简和优化而世计的开源库，它为内置JavaScript对象添加了各种方法，以通 过接近的接U提供新功能，或者直接提供新的对象。MooTools的短小精悍受到一些Web开发者的青睐。

□协议：MIT许TST证

口 网站：http://www.mootools.net/

C.1.5 jQuery

jQuery是一个给JavaScript提供了函数式编程接口的开源库。它是一个完整的库，其核心是构建于 CSS选择器上的，用来操作D0M元索。通过链式调用，jQuery代码看t去更像是对于应该发生什么的 描述而不是JavaScript代码。这种代码风格在设计师和原型制作人中非常流行。jQuery是由John Resig 撰写并维护的。

□协议：MIT许可证或通用公共许可证（GPL）

□网站：http://jquery.com/

C.1.6 MochiKit

MochKit是一个由一些小工具组成的开源库，它以完善的文档和完整的测试见长，拥有大量AH及 相关范例文档以及数百个测试来确保质量。MochiKit是由Boblppolito撰写并维护的。

□协议：M1T许可者或学术（*3由许可证2.1版

I□网站：http://www.mochikit.com/

C.1.7 Underscore.js

虽然严格来讲Underscorejs并不是一个通用的库，但它的确为JavaScript中的功能性编程提供了很 多额外的功能。其文椅称Underscore.js是对jQuery的补充，提供了操作对象、数组、函数和其他JavaScript 数据类型的更多的低级功能。Underscore.js由DcoumentCloud的Jeremy Ashkenas维护。

□协议：MIT许可证

I□网站：http://documentcloud.github.com/underscore/

C.2互联网应用

互联网应用库是针对于简化完整的Web应用开发而没汁的。它们并不提供应用问题的小块组件， 而是提供了快速应用开发的整个概念框架。虽然这些库也可能提供一些底层功能，但他们的□标是帮助 用户快速开发Web应用。

C.2.1 Backbone.js

Backbone.js是构建于Underscore.js基础之上的一个迷你MVC开源库，它针对单页应用进行优化，

让你能够随着应用状态变化方便地更新页面的任意部分。Backbone.js由DcoumentCloud的Jeremy Ashkenas 维护 „

□协议：MIT许可证

□网站：http://documentcloud.github.com/backbone/

C.2.2 Rico

Rico是一个开源库，旨在让行为丰富的互联网应用的开发更加简单。它提供了 Ajax、动画、样式 以及部件的T具。这个库由-些志愿者组成的小团队维护，但是2008年起开发速度大大减慢r。

□协议：Apache许可证2.0 U 网站：http://openrico.org/

C.2.3 qooxdoo

它是一个旨在协助整个互联网应用开发周期的开源库。qooxdoo实现了它Q己的类和接口，用于创 建类似于传统面向对象语言的编程模型。这个库包含了一个完整的GUI T具包以及用于简化前端构建过 程的编译器。qooxdoo起初是l&lwebhosting公司（www.landl.com）的内部使用库，后来基于并源协 议发布了。I&1骋用了一些全职开发者来维护和开发这个库。

□协议：GNU较宽松公共许可证（LGPL）或者Eclipse公共许可证（EPL）

□网站：http://www.qooxdoo.org/

C.3动画和特效

动画和其他视觉特效也成为了 Web开发的5:要部分。在网页上做出流畅的动画是一个很重要的任 务，一些开发者已经做出了易用的库，提供流畅的动画和特效。前面提到的很多通用JavaScript库也有 动画功能。

C.3.1 script.aculo.us

script.aculo.us是Prototype的“同伴”，它提供了出色特效的简单使用方式，使用的东西不超过是CSS 和DOM。Prototype必须在使用script.aculo.us之前载人。script.aculo.us是最流行的特效库之一，世界上 很多网站和Web应用都在使用它。它的作者Thomas Fuchs积极地维护着script.aculo.us。

□协议：MIT许可证

I□网站：<http://script.aculo.us/>

C.3.2 moo.fx

moo.fx开源动画库是设计在Prototype或者MooTools之上运行的。它的目标是尽可能小（最新的版 本是3KB）,并支持开发人员用尽可能少的代码创建动画。MooTools是默认包含moo.fic的，但也可以单 独下载用于Prototype中。

□协议：M1T许可证

I□网站：http://moofx.mad4rnilk.net/

C.3.3 Lightbox

Lightbox是一个用于在任何页面上创建图像浮动层的JavaScript库，依赖于Prototype和 script.aculo.us来实现它的视觉特效。基本的理念是让用户在一个浮动层中浏览一个或者一系列图像， 而不必离开当前页面。Lightbox浮动层无论是外观还是过渡效果都可以自定义。Lightbox由Lokesh Dhakar开发并维护。

□协议：创作共用协议2.5

口 网站：http://www.huddletotegher.com/projects/lightbox2/

C.4加密

随着Ajax应用的流行，对于浏览器端加密以确保通讯安全的需求也越来越多。幸好，一些人已经 在JavaScript中实现了常用的安全算法。这些库大部分并没有其作者的正式支持，但还是被广泛应用着。

C.4.1 JavaScript MD5

该开源库实现了 MD4、MD5以及SIIA-1安全散列函数。作者Paul Johnston和其他一些贡献者每个 算法_个文件的创建了这么丰富的库，它可以用于Web应用。主页上提供了散列算法的概述，对于其 弱点的讨论以及适当的使用方法。

□协议：BSD许可证

I□网站：http://pajhome.org.uk/crypt/md5

C.4.2 JavaScrypt

该JavaScript库实现了 MD5和AES （256位）加密算法„ JavaScrypt的网站提供了很多关于密码学 历史及其在计算机中应用的信息。但是缺乏关于如何将该库集成到Web应用中的基本文档，JavaScrypt 的代码里面全都是深奥的数学处理和计算。'

□协议：公共域

□网站：ht^p://[www.fourmilab.ch/javascrypt/](http://www.fourmilab.ch/javascrypt/)

JavaScript 工具

•^JavaScript代码和用其他语言编写代码很像，使用工具能够提髙工作效率。JavaScript开发人 员可用的工具数量一度爆发性增长，使得查找问题、优化和部署基于JavaScript的解决方案更

为简单。艽中一些工具是专为JavaScript设计使用的，而其他一些可以在浏览器之外运行。本附录对其 中一些工具给出了概述，并额外提供了信息资源。

D.1校验器

JavaScript调试有一4*问题，很多IDE并不能在输人的时候Q动指出语法错误。大多数开发者写了 一部分代码之后要将其载人到浏览器中查找错误。你可以通过在部署之前校验JavaScript代码，以便显 著地减少此类错误。校验器提供了基本的语法检査，并给出某些风格的警告。

D.1.1 JSLint

JSLint是一个由Douglas Crockford撰写的JavaScript校验器。它可以从核心层次上检査语法错误， 伴随跨浏览器问题的最小共同点检查（它遵循最严格的规则来确保代码到处都能运行）。你可以启用 Crockford对于代码风格的警告，包括代码格式、未声明的全局变量的使用以及其他更多警告。尽管JSLint 是用JavaScript写的，但是通过基于Java的Rhino解释器，它可以在命令行中运行，或者通过WScript 或者其他JavaScript解释器。网站上提供了针对各种命令行解释器的自定义版本。

口价格：免费

口 网站：http://www.jslint.com/

D.1.2 JSHint

JSHint是JSLint的一个分支，为应用规则提供了更多的自定义功能。与JSLint类似，它首先检査语 法错误，然后检査有问题的编碎模式。JSLint的每一项检查JSHint都有，但开发人员可以更好地控制应 用什么规则。与JSLint—样，JSHint也能使用Rhino在命令行中运行。

□价格：免费

□网站：http://www.jshint.com/

D.1.3 JavaScript Lint

它和JSLint完全不相干，JavaScript Lint是Matthias Miller写的一个基于C的JavaScript校验器。它 使用了 SpiderMonkey （即Fkefox所用的JavaScript解释器）来分析代码并査找语法错误。这个工具包含

大量选项，可以启用额夕卜关T编码风格的警告，以及未声明的变谩和不可到达的代码繁吿。Windows和 Macintosh h都有可用的JavaScript Lint,源代码也可以自由取得。

□价格：免费

口 网站：http://www.javascriptlint.com/

D.2压缩器

JavaScript构建过程中很重要的-•部分，是压缩输出并移除多余的字符。这样做可以确保传送到浏 览器的字节数最小化，最终加速了用户体验。有几种压缩比率不同的工具可以选择=D+2.1 JSMin

JSMin是由Douglas Crockford写的+—个基于C的压缩器，进行最基本的JavaScript JR缩。它主要是 移除空白和注释，确保最终的代码依然可以被顺利执行。JSMin有Windows执行程序，包括C版本代码， 还有其他语言的代码：

口价格：免费

□网i占：<http://www.crockford.cotn/javascript/jsmin.html>

D.2.2 Dojo ShrinkSafe

负责Dojo Toolkit的同一批人开发了一个叫做ShrinkSafe的工具，它使用f Rhino JavaScript解释器 首先将JavaScript代码解析为记号流，然后用它们来安全压縮代码。和JSMin _样，ShrinkSafe移除多 余的空白符（不包括换行）和注释，但是还更进一步将局部变景替换为两个字符长的变ft名。最后可以 比JSMin产生更小输出，而没有引人语法错误的风险。

□价格：免费

□网站：http:// shrinksafe .dojotoolkit.org/

D.2.3 YU I Compressor

YUI 小组有一个叫做 YUI Compressor 的压缩器。和 ShrinkSafe 类似，YUI Compressor利用丁 Rhino 解释器将JavaScript代码解析为记号流，并移除注释和空白字符并替换变量名。与SbrinkSafe不同，YUI Compressor还移除换行并进行一些细微的优化进一步节省字节数。一般来说，YU1 Compressoi•处理过的 文件要小于JSMin或者ShrinkSafe处理过的文件。

□价格：免费

□网站：http://yuilibraiy.com/projects/yuicompressor

D.3单元测试

TDD （Test-drivendevelopment,测试驱动开发）是一种以单元测试为核心的软件开发过程。直到最 近，才出现了一些在JavaScript进行单元测试的工具。现在多数JavaScript库都在它们自已的代码中使用 了某种形式的单元测试，其中一些发布了单元测试梢架让他人使用。

D.3.1 JsUnit

最早的JavaScript单元测试框架，不绑定于任何特定的JavaScript库。JsUnit是Java知名的JUnit测 试框架的移植。测试在页面中运行，并可以设置为自动测试并将结果提交到服务器。它的网站上包含了 例子和基本的文档：

□价格：免费

□网站：http://www.jsunit.net/

D.3.2 YUI Test

作为YUI的一部分，YUI Test不仅可以用于测试使用YUI的代码，也可以测试网站或者应用中的 任何代码。YUI Test包含了简单和复杂的断言，以及--种模拟简单的鼠标和键盘事件的方法。该框架在 Yahoo! Developer Network上有完整的文约描述，包含了例子、API文约和更多内容。测试时在浏览器中 运行，结果輪出在页面上。YU1便使用YUI Test来测试整个库。

□价格：免费

□网站：http://yuilibrary.com/projects/yuitest/

D.3.3 DOH

DOH( Dojo Object Harness )在发布给大家使用之前，最初是作为Dojo内部的单元测试工具出现的。 和其他框架一样.单元测试是在浏览器中运行的。

□价格：免费

[□网站：http://www.dojotoolkit.org/

D.3.4 qUnit

qUnit是为测试jQuery而开发的一个单元测试框架。jQuery本身的确使用qUnit进行各项测试。除 此之外，qUnit与jQuery并没有绑定关系，也可以用它来测试所有JavaScript代码。qUnit的特点是简单 易用，一般开发人员很容易上手。

□价格：免费

□网站：https://github.com/jquery/qunit

D.4文档生成器

大多数IDE对于主流语言都包含了文档生成器。由于相vaScript并没有官方的IDE,过去文档一般 都是手工完成，或者是利用针对其他语言的文档生成器。然而，现在终浐冇一些专门针对;lavaScript的 文档生成器了。

D.4.1 JsDoc Toolkit

JsDoc Toolkit是最早Hi现的JavaScript文档生成器之一。它要求你在代码中输人类似Javadoc的注释. 然后处理这些注释并输出为HTML文件。你可以自定义HTML的格式，这需使用预定义的JsDoc模板 或者创建自己的模版。JsDoc Toolkit可以以Java包的形式获得。

□价格：免费

□网站：<http://code.google.eom/p/jsdoc-toolkit/>

D.4.2 YU I Doc

YUI Doc是YUI的文档生成器,，该生成器以Python书写，所以它蕃求安装有Python运行时环境。 YUI Doc可以输出集成了属性和方法搜索（用YUI的自动完成挂件实现的）的HTML文件。和JsDoc 一样，yui Doc要求源代码中使用类似Javadoc的注释。默认的HTML可以通过修改默认的HTML模板 文件和相关的样式表来更改。

□价格：免费

□网站：http://www.yuilibrary.com/projects/yuidoc/

D.4.3 AjaxDoc

AjaxDoc的目标和前面提到的牛成器奄些差异。它不为JavaScript文档生成HTML文件，而是创建 与针对.NET语言（如C#和Visual Basic.NET）所创建文件相同格式的XML文件。这样做就可以由标准 的.NET文档生成器创建HTML文件形式的文档。AjaxDoc使用类似于所有.NET语言用到的文档注释格 式。创建AjaxDoc是针对ASP.NET的Ajax解决方案，但是它也可以用于单独的项目。

□价格：免费

□网站：http://www.codeplex.com/ajaxdoc

D.5安全执行环境

随着mashup应用越来越流行,对于允许来自外界的JavaScript存在于同一个页面上并执行宥着越来 越多的需求。这导致了一些访问受限功能的安全问题。以下工具旨在创建安全的执行环境，其中不同来 源的JavaScript可以共存，而不会互相影响。

D.5.1 ADsafe

由Douglas Crockford创建，ADsafe是JavaScript的了-集，这个子集被认为可以被第二:方脚本安全访 问。对于用ADsafe运行的代码，页面必须包含ADsafe JavaScript库并标记为ADsafe挂件格式。因此， 代码可以在任何页面上安全执行。

□价格：免费

□网站：http://www.adsafe.org/

D.5.2 Caja

Caja用•一种独特的方式来确保JavaScript的安全执行。类似于ADsafe, Caja定义了 JavaScript的一 个可以用安全方式使用的子集。Caja继而可以清理JavaScript代码并验证它只按照预期的方式运行。作 为该项H的| -部分，有一种叫做Cajita的语言，它是JavaScript功能的一种更小的子窠。Caja还处于幼 年期，但是已经展示了很多前景，允许多个脚本在同一个页面执行而没有恶意活动的可能。

□价格：免费

□网站：<http://code.googlc.eom/p/googlc-caja/>