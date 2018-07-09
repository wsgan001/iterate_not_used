Ajax 与 Comet

本章内容

□使用 XMLHt tpReques t 对象 □使用 XMLHttpRequest 事件 □跨域Ajax通信的限制

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-109.jpg)

 

年，Jesse James Garrett 发表了一篇在线文章，题为 “Ajax: A new Approach to Web ications”( <http://www.adaptivepath.com/ideas/essays/archivcs/000385.php> )o 他在这篇文章 里介绍了一种技术，用他的话说，就叫Ajax,是对Asynchronous JavaScript + XML的简写。达一技术 能够向服务器请求额外的数据而无须卸载页面，会带来更好的用户体验。Garrett还解释了怎样使用这 一技术改变自从Web诞生以来就一直沿用的“单击，等待”的交互模式。

Ajax技术的核心是XMLHttpRequest对象(简称XHR),这是由微软首先引人的一个特性，其他 浏览器提供商后来都提供了相同的实现。在XHR出现之前，Ajax式的通信必须借助-些hack手段来实 现，大多数是使用隐藏的框架或内嵌框架。XHR为向服务器发送»求和解析服务器响应提供了流畅的 接口。能够以异步方式从服务器取得吏多信息，意味蔚用户单击后，可以不必刷新页面也能取得新数据。 也就是说，可以使用XHR对象取得新数据，然后再通过DOM将新数据插入到页面中。另外，虽然名 字中包含XML的成分，但Ajax通信与数据格式无关；这种技术就是无须刷新页面即可从服务器取得数 据，但不一定是XML数据。

实际上，Garrett提到的这种技术已经存在很长时间了。在Garrett撰写那篇文章之前，人们通常将 这种技术叫做远程脚本(remote scripting),而旦早在1998年就有人采用不同的手段实现了这种浏览器 与服务器的通信。再往前推，JavaScript需要通过Java applet或Flash电影等中间层向服务器发送请求。 而XHR则将浏览器原生的通信能力提供给了开发人员，简化了实现同样操作的任务。

在重命名为Ajax之后，大约是2005年底2006年初，这种浏览器与服务器的通信技术可谓红极一 时。人们对JavaScript和Web的全新认识，催生了很多使用原有特性的新技术和新模式。就目前来说， 熟练使用XHR对象Ll经成为所有Web开发人员必须辛握的一种技能。

21.1 XMLHttpRequest 对象

IE5是第一款引人XHR对象的浏览器。在IE5中，XHR对象是通过MSXML库中的一个ActiveX 对象实现的。因此，在IE中可能会遇到三种不同版本的XHR对象，即MSXML2.XMLHttp、 MSXML2.XMLHttp.3.0 和 MXSML2 . XMLHttp. 6.0。要使用 MSXML 库中的 XHR 对象，需要像第 18 章讨论创建XML文档时一样，编写一个函数，例如：

//适用于IE7之前的版本 ! function createXHR(){

if (typeof arguments.callee.activeXString != "string"){

var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",

*MSXML2.XMLHttp"J z

i, len；

for (i=0,len=versions.length； i < len； i++}{ try {

new ActiveXObject(versions[i]);

arguments.callee.activeXString = versions[i];

break;

} catch (ex){

"跳过

}

)

}

return new ActiveXObject(arguments.callee.activeXString);

}

这个函数会尽力根据IE中可用的MSXML库的悄况创建最新版本的XHR对象。

IE7+、Firefox, Opera、Chrome和Safari都支持原生的XHR对象，在这些浏览器中创建XHR对象 要像下面达样使用XMLHttpRequest构造涵数。

var xhr = new XMLHttpRequest{);

假如你只想支持IE7及更髙版本，那么大可丢掉前面定义的那个函数，而只用原生的XHR实现。 但是，如果你必须还要支持IE的早期版本，那么则可以在这个createXHR(>函数中加人对原生XHR 对象的支持。

function createXHR(){

if (typeof XMLHttpRequest "undefined"){ return new ZHLHttpRequest{);

} else if (typeof ActiveXObject 1= "undeflnod")<

if (typeof arguments.callee.activeXString != "string"){

var versions = [ "MSXML2.XMLHttp.6.0*, "MSXML2.XMLHttp.3.0",

"MSXML2.XMLHttp-】，

i, len；

for (i=0,len=versions-length; i < len； i++){ try {

new Act iveXObj ect(versions[i]);

arguments.callee.activeXString = versions[i];

break；

} catch (ex){

//狹过

}

}

}

return new ActiveXObject{arguments.caliee.activeXString);

} else {

throw new Error(”No XHR object available.**};

XHRExampleOl. htm

这个函数屮新增的代码首先检测原生XHR对象是否存在，如果存在则返回它的新实例。如果原生 对象不存在，则检测ActiveX对象。如果这两种对象都不存在，就抛出-斗错误。然后，就可以使用下 面的代码在所冇浏览器中创建XHR对象了。

var xhr = createXHRO ；

由于其他浏览器中对XHR的实现与IE最早的实现是兼容的，因此就可以在所有浏览器屮都以相同 方式使用上面创建的xhr对象。

21.1.1 XHR的用法

在使用XHR对象时，要调用的第一个方法是openO,它接受3个参数：要发送的请求的类型 (-get' ”post-等)、请求的URL和表示是否异步发送请求的布尔值。下面就是调用这个方法的例子。

xhr.open("get", "example.php"/ false);

这行代码会启动一个针对example.php的GET请求。有关这行代码，需要说明两点：一是URL 相对于执行代码的当前页面(当然也可以使用绝对路径)；二是调用open ()方法并不会真正发送请求， 而只是启动一个请求以备发送。

只能向同一个城中使用相同端口和协议的URL发送请求。如果URL与启动请求 的页面有任何差别，都金引发安全错误。

要发送特定的请求，必须像下面这样调用send ()方法:

xhr.open("get", "example.txt", false); xhr.send(null)；

21

 

XHRExampleOl. hfm

这里的send 0方法接收一个参数，即要作为谪求主体发送的数据。如果不需要通过请求主体发送 数据，则必须传人mill,因为这个参数对有些浏览器来说是必需的。调用send (＞之后，谙求就会被分 派到服务器。

由于这次诸求是同步的，JavaScript代码会等到服务器响应之后再继续执行c在收到响应后，响应 的数据会自动填充XHR对象的属性，相关的属件简介如下。

□    responseText：作为响应主体被返回的文本。

□    responseXML：如果响应的内容类S是11text/xml'■或"application/xmV，这个属性中将保 存包含着响AV:数据的XML DOM文档。

□    status：响应的HTTP状态。

□    statusText： HTTP状态的说明。

在接收到响应后，第一步是检查status属性，以确定响应已经成功返回。一般来说，可以将HTTP 状态代码为200作为成功的标志。此时，responseText属性的内容已经就绪，而且在内容类型正确的 情况下，responseXML也应该能够访问了。此外，状态代码为304表示请求的资源并没有被修改，可 以直接使用浏览器中缓存的版本；当然，也意味着响应是有效的„为确保接收到适当的响应，应该像下* 面这样检査上述这两种状态代码：

xhr.open<"get■, "example.txt■, false);

xhr.send(null)；

if ((xhr.status >= 200 && xhr.status < 300> II xhr.status =» 304){ alert(xhr.responeeText);

} else <

alert("Request was unsuccessful: " + xhr.status);

XHRExampleOl. htm

根据返回的状态代码，这个例子可能会显示由服务器返回的内容，也可能会显示一条错误消息。我 们建议读者要通过检测status来决定下一步的操作，不要依赖statusText,因为后者在跨浏览器使 用时不太可靠。另外，无论内容类型是什么，响应主体的内容都会保存到responseText属性中；而 对于非XML数据而言，responseXML属性的值将为null。

(^\    有的浏览器会错误地报告204状态代码。EE中XHR的ActiveX版本会将204设

置为1223，而IE中原生的XHR则会将204规范化为200。Opera会在取得204时报 告status的值力0。

像前面这样发送同步清求当然没有问题，但多数情况下，我们还是要发送异步请求，才能让 JavaScript继续执行而不必等待响应。此时，可以检测XHR对象的readyState属性，该属性表示请求 /响应过程的当前活动阶段。这个属性可取的值如下。

□    0:未初始化。尚未调用open(>方法。

□    1:后动。已经调用open<)方法，但尚未调用send()方法。

□    2：发送。已经调用send()方法，但尚未接收到响应。

□    3：接收。已经接收到部分响应数据。

□    4:完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

只要readyState属性的值由• •个值变成另一个值，都会触发一次readystatechange事件。可 以利用这个事件来检测每次状态变化后readyState的值。通常，我们只对readyState值为4的阶 段感兴趣，因为達时所打数据都已经就绪。不过，必须在调用open()之前指定onreadystatechange 事件处理程序才能确保跨浏览器兼容性。下面来看一个例子。

var xhr = createXHR(};

xhr.onreadystatechange = function(){

if (xhr.readyState == 4){

if ((xhr.status >- 200 && xhr.status < 300) II xhr.status == 304){ alert(xhr.responseText)；

} else {

alert("Request was unsuccessful: " + xhr.status);

}

}

};

xhr.open("get"# "example.txt", true)； xhr.send(null)；

XHRAsyncExampleOl. htm

以上代码利用DOM 0级方法为XHR对象添加了事件处理程序，原因是并非所有浏览器都支持DOM2 级方法。与其他事件处理程序不同，这里没有向onreadystatechange事件处理程序中传递event对象; 必须通过XHR对象本身来确定下一步该怎么做。

这个例子在onreadystatechange枣件处理我序中使用了 xhr对象，没有使用 this对象，原因是onreadystatechange事件处理程序的作用城问题》如果使用 this对象，在有的浏览器中会导致函教执行失败，或者导致钳误发生。因此，使用 实际的XHR对象实例变量是较为可靠的一种方式。

另外，在接收到响应之前还可以调用abort ()方法来取消异步请求，如下所示： xhr.abort()；

调用这个方法后，XHR对象会停止触发市件，而M也不再允许访问任何与响应有关的对象属性。在 终止请求之后，还应该对XHR对象进行解引用操作。由于内存原因，不建议重用XHR对象。21.1.2 HTTP头部信息

每个HTTP请求和响应都会带有相应的头部信息.其中有的对开发人员有用，有的也没冇什么用。 XHR对象也提供了操作这两种头部(即请求头部和响应头部)信息的方法。

默认情况下，在发送XHR请求的同时，还会发送下列头部信息。

□    Accept：浏览器能够处理的内容类型。

□    Accept -Charset:湖览器能够S示的字符集。

□    Accept-Encoding:浏览器能够处理的压缩编码。

□    Accept -Language:浏览雅当前设置的语言。

21

 

□    Connection：浏览器与服务器之间连接的类型。

□    Cookie：当前页面设置的任何Cookieo

□    Host:发出请求的页面所在的域。

□    Referer：发出诂求的页面的URI。注意，HTTP规范将这个头部字段拼写错丁，而为保证与规 范一致，也只能将错就错了。(这个英文单同的正确拼法应该是referrer。)

□    User-Agent:浏览器的用户代理字符串。

虽然不同浏览器实际发送的头部信息会有所不同，但以上列出的基本上是所有浏览器都会发送的。 使用setRequestHeaderO方法可以设S自定义的请求头部信息。这个方法接受两个参数：头部字段 的名称和头部字段的值。要成功发送请求头部信息，必须在调用open ()方法之后II调用send ()方法 之前调用seLRequestHeader (),如下面的例子所示。

var xhr = createXHR();

xhr.onreadystatechange = function。{ if (xhr.readyState == 4){

if {(xhr.status >= 200 && xhr.status < 300) II xhr.status == 304){ alert{xhr.responseText)；

} else {

alert(*Request was unsuccessful: • + xhr.status);

} i

xhr.open("get■, "example.php°, true)?

xhr. setRequestHeader ("MyHeadern, MMyValu©**) 7

xhr.send(null);

XHRRequestHeadcfsExampleOl. htm

服务器在接收到这种n定义的头部信息之后，可以执行相应的后续操作。我们建议渎者使用自定义 的头部字段名称，不要使用浏览器正常发送的字段名称，否则存可能会影响服务器的响应。有的浏览器 允许开发人员道写默认的头部倍息，但有的浏览器则不允许这样做。

调用XHR对象的getResponseHeader()方法并传人头部字段名称，可以取得相应的响应头部信 息。而调用getAllResponseHeaders ()方法则可以取得一个包含所有头部信息的长字符串。来看下 面的例子。

var myHeader = xhr.getResponseHeader(HMyHeaderM; var allHeaders xhr.getAllResponseHeaders()；

在服务器端，也可以利用头部信息向浏览器发送额外的、结构化的数据。在没有A定义信息的情况 下，getAHResponse- Headers ()方法通常会返回如下所豕的多行文木内容：

Date： Sun, 14 Nov 2004 18:04:03 GMT

Server： Apache/1.3.29 (Unix)

Vary： Accept

X-Powered-By: PHP/4.3.8

Connection： close

Content-Type: text/html； charset=iso-8859-1

这种格式化的输出可以方便我们检査响应中所有头部字段的名称，而不必一个一个地检4某个字段 是否存在，

21.1.3 GET 请求

GET是最常见的清求类型，最常用于向服务器査询某些信息。必要时，可以将查询字符串参数追加 到URL的末尾，以便将信息发送给服务器。对XHR而言，位于传人open(>方法的URL末尾的査询字 符串必须经过正确的编码才行。

使用GET请求经常会发生的一个错误，就是丧询字符申的格式冇问题。査询字符串中毎个参数的名 称和值都必须使用encodeURlComponent ()进彳f编码，然后才能放到URL的末尾；而且所有名-值对 儿都必须由和号(&)分隔，如下面的例子所示=

xhr.open("get", "example.php?namel=valuel&name2=value2*, true);

下面这个函数可以辅助向现布URL的末尾添加査询字符串参数：

function adaURLParam(url, name, value) {

url += (url.indexOf <"?") ==： -1 ?    ； - &");

url += encodeURIComponent(name) + "=• + encodeURIComponent(value)) return url;

}

这个addURLParam()函数接受三个参数：要添加参数的URL、参数的名称和参数的值。这个函数 首先检査URL是卉包含问号(以确定是否已经存参数存在〉。如果没有，就添加一个问号；否则，就添 加一个和号。然后，将参数名称和值进行编码，再添加到URL的末尾。最后返同添加参数之后的URL。

下面是使用这个函数来构建请求URL的示例。 var url = *example.php*；

//添加参数

url = addURLParam(url, "name", "Nicholas")；

url = addURLParam(url# "book", "Professional JavaScript");

//初始化请求

xhr.open("get■url, false)；

在这里使用addURLParamO函数可以确保査询字符串的格式良好，并可靠地用于XHR对象。

21.1.4 POST 请求

使用频率仅次于GET的是POST请求，通常用于向服务器发送应该被保存的数据。POST请求应该 把数据作为请求的主体提交，而GET请求传统上不是这样。POST请求的主体可以包含非常多的数据， 而且格式不限。在openO方法第一个参数的位置传人》pOst-，就可以初始化一个POST请求，如下面 的例子所示。

xhr.open("post", "example.php*# true)；

发送POST请求的第二步就是向send ()方法中传人某些数据。由于XHR最初的设计主要是为了处 理XML,因此可以在此传人XMLDOM文档，传人的文裆经序列化之后将作为请求主体被提交到服务 器。当然，也可以在此传人任何想发送到服务器的字符串。

默认情况下，服务器对POST请求和提交Web表单的请求并不会一视同仁。因此，服务器端必须有 程序来读取发送过来的原始数据，并从中解析出有用的部分。不过，我们可以使用XHR来模仿表单提 交：首先将Content-Type义-部信息设置为application/x-www-form-urTencoded,也就是表单 提交时的内容类型，其次是以适当的格式创建-个字符串。第14章曾经讨论过，POST数据的格式与査 询字符串格式相同。如果需要将页面中表单的数据进行序列化，然后冉通过XHR发送到服务器，那么 就可以使用第14章介绍的serialize ()函数來创建这个字符串：

21

 

function subroitData(>{

var xhr = createXHR()；

xhr.onreadystatechange - function(){

if (xhr.readyState == 4){

if {(xhr.status >= 200 && xhr.status < 300) I I xhr.status == 304){ alert(xhr.responseText}；

} else {

alert("Request was unsuccessful: ” + xhr.status)；

}

}?

xhr.open(npoot"/ "poBtexanqple.php", true) /

xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded"); var £orm » document.g«tElementById("user-info"); xhr.send(seri«lize(form)):

XHRPostExampleOl. htm

这个闲数可以将ID为"user-info”的表单中的数据序列化之后发送给服务■器。而下面的7K例PHP 文件post examp 1 e. php就可以通过$_POST取得提交的数据丫：

<?php

header<"Content-Type: text/plain"); echo <«EOF

Name: {$_POST[ 4user-name* ]}

Email: {$_POST[ 1user-email* 】)

EOF;

postexample.php

如果不设置Content-Type头部信息，那么发送给服务器的数据就不会出现在$_POST超级全局变 量中。这时候，要访问同样的数据，就必须借助$HTTP_RAW_POST_DATA。

与GET请求相比，POST请求消耗的资源会更多一些。从性能角度来看，以发送 相同的数据计，GET请求的速度最多可达到POST请求的两倍。

21.2 XMLHttpRequest2 级

鉴于XHR已经得到广泛接受，成为了事实标准，W3C也若手制定相应的标准以规范其行为。 XMLHttpRcquest 1级只是把已有的XHR对象的实现细节描述了出来。而XMLHttpRequest2级则进一步 发展了 XHR。并非所有浏览器都完整地实现了 XMLHttpReqUeSt2级规范，但所有浏览器都实现了它规 定的部分内容。

21.2.1 FormData

现代Web应用中频繁使用的一项功能就是表单数据的序列化，XMLHttpRequest 2级为此定义了 FormData类型。FormData为序列化表单以及创建与表单格式相同的数据(用于通过XHR传檢)提供 了便利。下面的代码创建了一个FormData对象，并向其中添加了一些数据。

var data = new FormData(); data.append(■ name"# "Nicholas” '•

这个append (>方法接收两个参数：键和值，分别对应表单字段的名字和字段中包含的值。可以像 这样添加任意多个键值对儿。而通过向FormData构造函数中传入表单元素，也可以用表单元素的数据 预先向其中填人键值对儿：

var data = new FormData(document.forms[0]);

创建了 FormData的实例后，可以将它直接传给XHR的send ()方法，如下所示：

var xhr = createXHR{); xhr.onreadystatechange = function(){

if (xhr.readyState == 4){

304) {

 

if "xhr.status >= 200 && xhr.status < 300) II xhr.status == alert{xhr.responseText);

} else {

alert("Request was unsuccessful： ■ + xhr.status)；

}

}

}?

xhr.open("post","postexample.php"z true);

var form = document.getElementById<*user-info");

xhr.send(new FormData(form)};

XHRFormDataExampleO 1. htm

使用FormData的方便之处体现在不必明确地在XHR对象上设置请求头部。XHR对象能够识别传 人的数据类型是FormData的实例，并配置适当的头部信息。

支持 FormData 的浏览雜冇 Firefox 4+、Safari 5+、Chrome 和 Android 3+版 WebKit。

21.2.2超时设定

IE8为XHR对象添加了一个timeout属性，表示请求在等待响应多少毫秒之后就终止。在给 timeout设置一个数值后，如果在规定的时间内浏览器还没有接收到响应，那么就会触发timeout事 件，进而会调用ontimeout事件处理程序。这项功能后来也被收人了 XMLHttpRequest2级规范中。来 看下面的例子。

var xhr - createXHR();

xhr.onreadystatechange = function(){

if {xhr.readyState == 4){ try {

if {(xhr.status >= 200 && xhr.status < 300) I I xhr.status == 304){ alert(xhr.responseText);

21

 

} else {

alert("Request was unsuccessful: ■ + xhr.status);

}

} catch (ex){

//假设由ontimeout事件处理程序处理

}

)

};

xhr.open("get*"timeouL.php", true)；

xhr.timeout - 1000; //将超时设置为1秒钟(仅适用于IE8+) xhr.oatimeout ■ function(){

alert (nRecjuoat did not return in a second. R);

};

xhr.send(null)；

XHRTimeoutExampleOhhtm

这个例子示范了如何使用timeout属性。将这个属性没置为1000毫秒，意味着如果请求在丨秒钟 内还没有返间，就会自动终止。请求终止时，会调用ontimeout事件处理程序。但此时readyState 可能已经改变为4 了，这意味狞会调用onreadystatechange事件处理程序。可是，如果在超时终止 请求之后再访问status属性，就会导致错误。为避免浏览器报告错误，可以将检査status属性的语

句封装在一个try-catch语句当中。

在写作本书时，IE 8M然是唯一支持超时设定的浏览器。

21.2.3 overrideMimeType ()方法

Firefox射:单引人了 overrideMime*Type ()方法，用丁重K XHR响应的MIME类型。这个方法后 来也被纳人了 XMLHttpRequest2级规范。因为返回响应的MIME类型决定了 XHR对象如何处理它，所 以提供-•种方法能够重写服务器返回的MIME类型是很有用的。

比如，服务器返回的MIME类型是text/plain，但数据中实际包含的是XML。根据MIME类型， 即使数据是XML, responseXML属性中仍然是null。通过调用overrideMimeType ()方法，可以保 证把响应当作XML而非纯文本来处理3

var xhr = createXHR()；

xhr. open (■ get *', " text .php" / true) ?

xhr.overrideMimeType ("text/xml'*) /

xhr.send(null);

这个例子强迫XHR对象将响应当作XML而非纯文本来处理。调用overrideMimeType ()必须在 send()方法之前，才能保证重写响应的MIME类型。

支持 overrideMimeType ()方法的浏览器有 Firefox、Safari 4+、Opera 10.5 和 Chromeo

21.3进度事件

Progress Events规范是W3C的一个E作草案，定义了与客户端服务器通信有关的事件。这些事件最 早其实只针对XHR操作，但目前也被其他API借鉴。有以下6个进度事件。

□    loadstart：在接收到响应数据的第一个字节时触发。

□    progress：在接收响应期间待续不断地触发y

□    error：在请求发生错误时触发。

□    abort:在因为调用abort ()方法而终止连接时触发。

口 load:在接收到完整的响应数据时触发。

□    loadend：在通信完成或者触发error、abort或load事件后触发。

每个请求都从触发loadstart事件开始，接下来是一或多个progress事件，然后触发error、 abort或load事件中的一个，最后以触发loadend事件结束。

支持前 5 个事件的浏览器有 Firefox 3.5+、Safari 4+、Chrome、iOS 版 Safari 和 Android 版 WebKit。 Opera (从第11版开始)、IE 8+只支持load事件。目前还没右浏览器支持loadend事件。

这些事件大都很K观，但其中两个事件存一些细节需要注意。

21.3.1 load 事件

Hrefox在实现XHR对象的某个版本时，曾致力于简化异步交互模型。最终，Firefox实现中引人了 load事件，用以替代readystatechange事件。响应接收完毕后将触发load事件，因此也就没有必 要去检查readyState属性了。而onload事件处理程序会接收到一个event对象，其target属性 就指向XHR对象实例，因而可以访问到XHR对象的所有方法和属性。然而，并非所有浏览器都为这个

事件实现了适当的事件对象。结果，开发人员还是要像下面这样被迫使用XHR对象变it,

var xhr = createXHR();

xhr.onload * function(){

if ((xhr.status >■ 200 && xhr.status < 300》II xhr.status =■ 304){ alert(xhr.responseText);

} else {

alert("Request waa unsuccessful: ■ + xhr.status);

)

xhr.open("get", "altevents.php", true); xhr.send(null)；

XHRProgressEventExampleOl. htm

只要浏览器接收到服务器的响应，不管其状态如何，都会触发load事件。而这意味着你必须要检 査status属性，才能确定数据是否真的已经可用了。Firefox、Opera、Chrome和Safari都支持load 事件。

21.3.2 progress 事件

Mozilla对XHR的另一个革新是添加了 progress事件，这个事件会在浏览器接收新数据期间周期 性地触发。而onprogress事件处理程序会接收到一个event对象，其target涵性是XHR对象，但 包含着三个额外的属性：lengthComputable、position 和 totalSize。其中，lengthComputable 是一个表示进度信息是否可用的布尔值，position表示已经接收的字节数，totalSize表示根据 Content-Length响应头部确定的预期字节数。有了这些信息，我们就可以为用户创建-个进度指示器 了。下面展示了为用户创建进度指示器的一个示例。

var xhr = createXHR();

21

 

xhr.onload = function(event){

if ((xhr.status >= 200 && xhr.status < 300) II xhr.status == 304){

alert(xhr.responseText);

)else {

alert{"Request was unsuccessful: " + xhr.status);

}

} ?

xhr.onprogress « function(event){

var divStatus « document.getElementById("status”)； if (event.lengthComputable)(

divStatus.innerHTML = "Received ” + event.position + " of ” + event.totalSize    bytes

}

xhr.open("get■# "altevents.php*, true)? xhr.send(null);

XHRProgressEventExampleOl .htm

为确保正常执行，必须在调用open ()方法之前添加onprogress事件处理程序。在前面的例子中,

毎次触发progress事件，都会以新的状态信息更新HTML元索的内容。如果响应头部中包含 Content-Length字段，那么也可以利用此信息来计算从响应中已经接收到的数据的百分比。

21.4跨源资源共享

通过XHR实现Ajax通信的一个主要限制，来源于跨域安全策略。默认情况下，XHR对象只能访 问与包含它的页面位于同一个域中的资源。这种安全策略可以预防某些恶意行为。但是，实现合理的跨 域请求对开发某些浏览器应用程序也是至关重要的。

CORS （ Cross-Origin Resource Sharing,跨源资源共享）是W3C的一个工作草案，定义了在必须访 问跨源资源时，浏览器与服务器应该如何沟通。CORS背后的基本思想，就是使用自定义的HTTP头部 让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。

比如一个简单的使用GET或TOST发送的猜求，它没有fl定义的头部，而主体内容是text/plain。在 发送该请求时.笛要给它附加一个额外的Origin头部，其中包含请求页面的源信息（协议、域名和端 U）,以便服务器根据这个头部信息来决定是否给予响应。下面是Origin头部的一个示例：

Origin： <http://www.nczonline.net>

如果服务器认为这个请求可以接受，就在Access-Control-Allow-Origin头部中回发相同的源 信息（如果是公共资源，可以回发•*•）。例如：

Access-Control-Allow-Origin： http：//www.nczonline.net

如果没有这个头部，或者有这个头部但源倍息不匹配，浏览器就会驳回请求。正常悄况下，浏览器 会处理请求。注意，请求和响应都不包含cookie信息。

21.4.1 旧对CORS的实现

微软在IE8中引人了 XDR （ XDomainRequest ）类型。这个对象勾XHR类似，但能实现安全可靠 的跨域通信。XDR对象的安全机制部分实现了 W3C的CORS规范。以下是XDR与XHR的一些不同之 处。

□ cookie不会随请求发送，也不会随响应返回。

□只能设货请求头部信息中的Content-Type字段。

□不能访问响应头部信息。

□只支持GET和POST请求。

这些变化使 CSRF （ Cross-Site Request Forgery,跨站点请求伪造）和 XSS （Cross-Site Scripting,跨 站点脚本）的问题得到了缓解。被请求的资源可以根据它认为合适的任意数据（用户代理、来源页面等） 来决定是否设置Access-Control - Allow-Origin头部。作为请求的一部分，Origin头部的值表示 请求的来源域，以便远程资源明确地识别XDR清求。

XDR对象的使用方法与XHR对象非常相似。也是创建一个XDomainRequest的实例，调用open U 方法，冉调用send （＞方法。但与XHR对象的open （）方法不同，XDR对象的open（）方法只接收两个 参数：请求的类型和URL。

所有XDR请求都是异步执行的，不能用它来创建同步请求。请求返冋之后，会触发load事件， 响应的数据也会保存在responseText属性中，如下所示。

var xdr = new XDomainRequest();

xdr.onload = function(){

alert(xdr.responseText)；

} ?

xdr .open{"get" t "http： //\mww. soroewhere-else. com/page/"); xdr.send(null)；

XDomainRequestExampleOl. hfm

在接收到响应后，你只能访问响应的原始文本；没有办法确定响应的状态代码。而且，只要响应有 效就会触发lead事件，如果失败(包括响应中缺少Access-ConCrol-Allow-Origin头部)就会触 发error事件。遗憾的是，除了错误本身之外，没有其他信息可用，W此唯一能够确定的就只有请求 未成功了。要检测错误，可以像下而这样指定一个onerror事件处理程序。

var xdr - new XDomainRequest{); xdr.onload = function。{

alert(xdr.responseText);

);

xdr.onerror = function(){

alert("An error occurred.n);

)；

xdr.open("get", "http;//[www.somewhere-else.com/page/*](http://www.somewhere-else.com/page/*)); xdr.send(null)；

XDomainRequestExampleOl. htm

鉴于导致XDR请求失败的因素很多，因此建议你不要忘记通过onerror事件处 理程序来捕获该事件；否则，即使请求失败也不会有任何提示。

21

 

在请求返回前调用abort ()方法可以终止清求：

xdr. abort (); //终止请求

与XHR—样，XDR对象也支持timeout属性以及on timeout事件处理程序。下面是一个例子。

var xdr = new XDomainRequest()；

xdr.onload = function(){

alert(xdr.responseText)；

};

xdr.onerror = function(){

alert{"An error occurred.");

}；

xdr.tImeout ■ 1000;

xdr.ontImeout = function(){

alert("Request took too long.**);

)；

xdr.open("get", "<http://www.somewhere-else.com/page/>"); xdr.send(null};

这个例子会在运行1秒钟后超吋，并随即调用ontimeout事件处理程序。

为支持POST请求，XDR对象提供了 contentType属性，用來表示发送数据的格式，如下面的例

子所示。

var xdr = new XDomainRequest(); xdr.onload = function{){

alert(xdr.responseText)；

};

xdr.onerror function

alert("An error occurred.”};

};

xdr.open{"post", "http：//[www.somewhere-else.com/page/n)](http://www.somewhere-else.com/page/n)%ef%bc%9b)[；](http://www.somewhere-else.com/page/n)%ef%bc%9b) xdr.contentiype = "application/x-www-form-urlencoded"； xdr. send (Miiamel=valuel&naine2=value2"};

这个域性是通过XDR对象影响头部信息的唯一方式。

21.4.2其他浏览器对CORS的实现

Firefox3.5+、Safari4+、Chrome, iOS版 Safari 和 Android平台中的 WebKit都通过 XMLHttpRequest 对象实现了对CORS的原生支持。在尝试打开不同来源的资源时，无需额外编写代码就可以触发这个行 为。要请求位于另•一个域中的资源，使用标准的XHR对象并在open ()方法中传人绝对URL即可，例如：

var xhr = createXHR(}；

xhr. onready st at echange ：= function (} {

if (xhr.readyState -= 4){

if ((xhr,status >= 200 && xhr.status < 300) II xhr.status == 304){ alert(xhr.responseText);

} else {

alert{"Request was unsuccessful: " + xhr.status);

}

};

xhr.open("get", "<http://www.somewhere-else.com/page/>", true)/

xhr.send(null);

与IE中的XDR对象不同，通过跨域XHR对象町以访问status和staCusText屑性，而且还支 持同步清求。跨域XHR对象也有一些限制，但为了安全这些限制是必需的。以下就是这些限制。

□不能使用setRequestHeader()设置!3定义头部。

□不能发送和接收cookie。

□调用getAllResponseHeaders (>方法总会返回空字符串。

由于无论同源请求还是跨源请求都使用相同的接口，因此对于本地资源，最好使用相对URL,在访 问远程资源时再使用绝对URL。这样做能消除歧义，避免出现限制访问头部或本地cookie信息等问题。

21.4.3 Preflighted Reqeusts

CORS通过一种叫做Preflighted Requests的透明服务器验证机制支持开发人员使用自定义的头部、 GET或POST之外的方法，以及不同类型的主体内容。在使用下列高级选项来发送请求时，就会向服务 器发送一个Preflight埔求。这种请求使用OPTIONS方法，发送下列头部。

□    Origin：与简单的请求相同。

□    Access-Control-Request-Method：请求自身使用的方法。

□    Access-Control-Request-Headers:(可选)自定义的头部信息，多个头部以逗号分隔。

以下是一个带有自定义头部NCZ的使用POST方法发送的诸求。

Origin： <http://www.nczonline.net>

Access-Control-Request-Method： POST

Access-Control-Request-Headers: NCZ

发送这个请求后，服务器可以决定是否允许这种类型的请求。服务器通过在响应中发送如下头部与 浏览器进行沟通。

口 Access-Control-Allow-Origin：与简单的请求相同。

□    Access-Control-Allow-Methods：允许的方法，多个方法以逗号分隔。

□    Access-Control-A1 low-Headers:允许的头部，多个头部以逗号分隔。

□    Access-Control-Max-Age：应该将这个Preflight请求缓存多长时间（以秒表;^ ）。

例如：

Access-Control-Allow-Origin： http：//www.nczonline.net

Access-Control-Allow-Methods: POST, GET

Access-Control-Allow-Headers: NCZ

Access-Control-Max-Age: 1728000

Preflight请求结束后，结果将按照响应中指定的时间缓存起来。而为此付出的代价只是第一次发送 这种请求时会多一次HTTP请求。

支持Pre flight请求的浏览器包括Firefox 3.5+、Safari 4+和Chrome。IE 10及更早版本都不支持。

21.4.4带凭据的请求

默认情况下，跨源请求不提供凭据（cookie、HTTP认证及客户端SSL证明等）。通过将 wichCredentials属性设置为true,可以指定某个请求应该发送凭据。如果服务器接受带凭据的请 求，会用下面的HTTP头部来响应。

Access-Control-Allow-Credentials： true

21

 

如果发送的是带凭据的请求，但服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给 JavaScript （于是，responseText中将是空字符串，status的值为0,而且会调用onerror （》事件处 理程序）。另外，服务器还可以在Prdlight响应中发送这个HTTP头部，表示允许源发送带凭据的请求。

支持withCredentials属性的浏览器有Firefox 3.5+s Safari 4+和Chrome。EE 10及更早版本都不

支持。

21.4.5跨浏览器的CORS

即使浏览器对CORS的支持程度并不都-•样，但所有浏览器都支持简单的（非Preflight和不带凭据 的）请求，因此有必要实现一个跨浏览器的方案。检测XHR是否支持CORS的最简单方式，就是检査 是否存在withCredentials属性。再结合检测XDomai.nRequesL对象是否存在，就可以兼顾所冇浏 览器了。

function createCORSRequest(method, url){ var xhr = new XMLHttpRequest()； if ("withCredentials" in xhr){

xhr.open(method, url, true);

)else if (typeof XDoma in Request 1 =： * undefined") {

vxhr = new XDomainRequest()； xhr.open(method, url)；

} else {

xhr =： null ；

}

return xhr；

}

var request = createCORSRequest(MgetN f "[http://www.somewhere-else.com/page/")](http://www.somewhere-else.com/page/%22)%ef%bc%9b)[；](http://www.somewhere-else.com/page/%22)%ef%bc%9b) if (request){

request .onload = function" {

//对 request. responseTex匕进行处理

};

request.send()；

}

CrossBrowserCORSRequestExampie01.htm

Firefox、Safari 和 Chrome 屮的 XMLHttpRequest 对象与 IE 中的 XDomainReguest 对象类似，都 提供了够用的接u,因此以上模式还是相当有用的。这两个对象共同的属性/方法如下。

□    aborco：用于停止正在进行的清求。

□    onerror：用于替代 onreadystatechange 检测错误。

□    onload：用于替代 onreadystatechange 检测成功。

□    responsc'l'ext:用于取得响应内容。

□    send!).：用于发送请求。

以上成员都包含在createCORSRequest ()闲数返间的对象中，在所有浏览器中都能正常使用。

21.5其他跨域技术

在CORS出现以前，要实现跨域Ajax通信颇费一些周折。开发人员想出了一些办法，利用DOM中 能够执行跨域请求的功能，在不依赖XHR对象的情况下也能发送某种请求。虽然CORS技术已经无处 不在，但开发人员自己发明的这些技术仍然被广泛使用，毕竞这样不需要修改服务器端代码。

21.5.1 图像 Ping

上述第--种跨域请求技术是使用＜1!^＞标签。我们知道，一个网页可以从任何网贞中加载图像，不 用担心跨域不跨域。这也是在线广告跟踪浏览量的主要方式。正如第13章讨论过的，也可以动态地创 建阁像，使用它们的onioad和onerror事件处理程序来确定是否接收到了响应。

动态创建图像经常用于图像Ping:,图像Ping是与服务器进行简单、单向的跨域通信的一种方式。 请求的数据足通过杳询字符串形式发送的，而响应可以是任意内容，但通常是像素图或204响应。通过 图像Ping,浏览器得不到任何具体的数据，但通过侦听load和error事件，它能知道响应是什么时 候接收到的。來看下面的例子。

var img = new Image();

img.onload = img.onerror = function(){

alert(H Done!•);

};

img .src = "http： //www.example.com/test?name=Nicholas*' ；

ImagePingExample01.htm

这里创建了一个Image的实例，然后将onload和onerror事件赴理程序指定为同一个函数。这 样无论是什么响应，只要请求完成，就能得到通知。请求从设S src属性那一刻开始，而这个例户在请 求中发送了■—个name参数。

图像Ping肢常用于跟踪用户点击页面或动态广告曝光次数。图像Ping有两个主要的缺点，-是只 能发送GET诂求，二是无法访问服务器的响应文本。因此，图像Ping只能用于浏览器与服务器间的单 向通信。

21.5.2 JSONP

JSONP是JSONwith padding （填充式JSON或参数式JSON ）的简写，是应用JSON的-种新方法， 在后来的Web服务中非常流行。JSONP看起来与JSON差不多，只不过是被包含在函数调用中的JSON, 就像下面这样。

callback（｛ "namen: "Nicholas" ｝）;

JSONP由两部分组成：回调函数和数据。回调函数是当响应到来吋应该在页而中调用的函数。回调 函数的名宇一般是在请求中指定的。而数据就是传人回调兩数屮的JSON数据。下面是---个典型的JSONP 请求。

<http://freegeoip.net/json/?callback=handleResponse>

这个URL是在请求一个JSONP地理定位服务c通过査询字符串來指定JSONP服务的回调参数是很 常见的，就像卜.面的URL所示，这里指定的回调函数的名字叫handleResponseO。

JSONP是通过动态＜script＞元索（要了解详细信息，请参考第13章）来使用的，使用时可以为 src属性指定一个跨域URL。这里的＜script＞元素与＜img＞元素类似，都有能力不受限制地从艿他域 加载资源。因为JSONP是宥效的JavaScript代码，所以在请求完成后，即在JSONP响应加载到页面巾 以后，就会立即执行。来看一个例子。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-110.jpg)

 

function handleResponse(response){

alert ("You* re at IP address ,r + response, ip + ", which is in

response.city + M, " + response.region_name);

var script = document.createElement("script")；

script.src    "<http://freegeoip.net/j> son/?calIback=handLeResponse"；

document.body.insertBefore(script, document.body.firstChiId);

JSONPExampleOl .htm

这个例子通过査询地理定位服务来显示你的IP地址和位置信息。

JSONP之所以在开发人员中极为流行，主要原因是它非常简单易用。与图像Ping相比，它的优点 在于能够直接访问响应文本，支持在浏览器与服务器之间双向通信。不过，JSONP也有两点不足。

首先，JSONP是从其他域中加载代码执行。如果其他域不安全，很可能会在响应中夹带一些恶意代 而此时除了完全放弃JSONP调用之外，没有办法追究。因此在使用不是你自己运维的Web服务时，

一定得保证.它安全可靠。

其次，要确定JSONP请求是否失败并不容易。虽然1^1^5给＜3＜：］^91；＞元素新墦了一个 onerror 事件处理程序，但目前还没有得到任何浏览器支持。为此，开发人员不得不使用计时器检测指定时间内

是否接收到了响应。佝就算这样也不能尽如人意，毕竟不是每个用户上网的速度和带宽都一样。

21.5.3 Comet

Comet是Alex Russell®发明的一个词儿，指的是一种更髙级的Ajax技术(经常也有人称为“服务器 推送”)。Ajax是一种从页面甸服务器清求数据的技术，而Comet则是一种服务器向页面推送数据的技 术。Comet能够让信息近乎实时地被推送到页面上，非常适合处理体育比赛的分数和股票报价。

有两种实现Comet的方式：长轮询和流。长轮询是传统轮询(也称为短轮询)的一个翻版，即浏览 器定时向服务器发送讲求，看有没有更新的数据。图21-1展示的是短轮洵的时间线。

长轮询把短轮询颠倒了一下。页面发起一个到服务器的请求，然后服务器一直保持连接打开，直到 有数据可发送。发送完数据之后，浏览器关闭连接，随即又发起一个到服务器的新诸求。这一过程在页 面打开期间-直持续不断。阁21-2展示了长轮询的吋间线。

图 21-2

无论是短轮询还是长轮询，浏览器都要在接收数据之前，先发起对服务器的连接。两者最大的区别 在于服务器如何发送数据。短轮询是服务器立即发送响应，无论数据是否有效，而长轮询是等待发送响 应。轮询的优势是所冇浏览器都支持，因为使用XHR对象和SetTimeout()就能实现。而你要做的就 是决定什么吋候发送请求。

第二种流行的Comet实现是HTTP流。流不同于上述两种轮询，因为它在页面的整个生命周期内只 使用一个HTTP连接。具体來说，就是浏览器向服务器发送一个请求，而服务器保持连接打开，然后周 期性地向浏览器发送数据。比如，下而这段PHP脚本就是采用流实现的服务器中常见的形式。

<?php

$i = 0; while(true){

①Alex Russell是著名JavaScript框架Dojo的创始人,

//榆出■-些数据，然后立即利新输出缓存 echo "Number is $i *; flush()；

✓ /等几秒钟

sleep(10);

}

所有服务器端语言都支持打印到输出缓存然后刷新(将输出缓存中的内容一次性全部发送到客户 端)的功能。而这正是实现HTTP流的关键所在。

在 Firefox、Safari、Opera和 Chrome 中，通过侦听 readystatechange 事件及检测 readyState 的值是否为3,就可以利用XHR对象实现HTTP流。在上述这些浏览器中，随着不断从服务器接收数 据，readyState的值会周期性地变为3。当readyState值变为3时，responseText厲性中就会保 存接收到的所有数据。此时，就需要比较此前接收到的数据，决定从什么位置开始取得最新的数据。使 用XHR对象实现HTTP流的典型代码如下所示。

function createStreamingClient(url, progress, finished){

I    var xhr = new XMLHttpRequest(),

received = 0；

xhr.open{"get"# url, true);

xhr.onreadystatechange = function(){

var result;

if (xhr.readyState == 3){

//只取得最新数据并调整计数器

result = xhr.responseText.substring(received); received += result.length;

21

 

//倒用progress &调函数 progress(result);

} else if (xhr.readyState == 4){ finished(xhr.responseText)；

}

};

xhr.send(null); return xhr;

)

var client = createStreamingClient("streaming.php", function(data){ alert{"Received： " + data)；

}z function(data){ alert("Done!*};

});

HTTPStreamingExampleO 1. htm

这个createStreamingClient ()函数接收三个参数：要连接的URL、在接收到数据时调用的兩 数以及关闭连接时调用的函数。有时候，当连接关闭时，很可能还需要重新建立，所以关注连接什么吋

候关闭还是有必要的。

只要ready st at echange事件发生，而且readyState值为3，就对responseText进行分割以 取得敁新数据、这里的received变量用于记录已经处理了多少个字符，每次readyState值为3时都 递增。然后，通过progress回调函数来处理传人的新数据„而当readyState值为4时，则执行 finished問调阴数，传人响应返回的全部内容。

虽然这个例子比较简单，而且也能在大多数浏览器中正常运行(IE除外).佴管理Comet的连接是 很容易出错的，需要时间不断改进才能达到完美。浏览器社区认为Comet是未来Web的一个重要组成 部分，为了简化这一技术，又为Comet创建了两个新的接口。

21.5.4服务器发送事件

SSE ( Server-Sent Events,服务器发送事件)是围绕只读Comet交互推出的API或者模式。SSE API 用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。服务器响应的MIME 类型必须是text/event-stream,而且是浏览器中的JavaScript API能解析格式输出。SSE支持短轮 询、长轮询和HTTP流，而且能在断开连接时自动确定何时熏新连接。有了这么简单实用的API,再实 现Comet就容易多了。

支持 SSE 的浏览器有 Firefox 6+、Safari 5+、Opera 11+、Chrome 和 iOS 4+版 Safari,,

\1. SSE API

SSE的JavaScript API与其他传递消息的JavaScript API很相似。要预订新的事件流，首先要创建--个新的Event Source对象，并传进一个人口点：

var source = new EventSource("myevents.php");

注意，传人的URL必须与创建对象的页面同源(相同的URL模式、域及端口)。EventSource的 实例有一个readyState «性，值为0表示正连接到服务器，值为1表示打开了连接，值为2表示关闭 了连接。

另外，还有以下三个事件。

□    open：在建立连接时触发。

□    message：在从服务器接收到新事件时触发。

□    error：在无法建立连接时触发。

就一般的用法而言，onmessagc事件处理程序也没有什么特别的。

source.onmessage = function(event){ var data = event.data；

//处理数据

};

服务器发回的数据以字符串形式保存在event .data中。

默认情况下，EventSource对象会保持与服务器的活动连接。如果连接断开，还会重新连接。这 就意味着SSE适合长轮询和HTTP流。如果想强制立即断开连接并且不再重新连接，可以调用closet) 方法。

source.close();

2.事件流

所谓的服务器事件会通过一个持久的HTTP响应发送，这个响应的MIME类型为text/event-

stream。响应的格式是纯文本，最简单的悄况是毎个数据项都带有前缀data:，例如： data： foo

data： bar

data: foo data: bar

对以上响应而言，事件流中的第一个message事件返回的event .data值为"foo",第二个 message琅件返回的event.data值为MbarM，第三个message事件返回的event .data值为 "foo\nbar» （注意中间的换行符）。对于多个连续的以data:开头的数据行，将作为多段数据解析， 每个值之间以一个换行符分隔。只有在包含data:的数据行后面有空行时，才会触发message事件， 因此在服务器上生成事件流时不能忘了多添加这一行。

通过id:前缀可以给特定的事件指定一个关联的ID,这个ID行位于data:行前而或后面皆可：

data： foo id: 1

-设置了 ID后，Event Source对象会跟踪上一次触发的事件。如果连接断开，会向服务器发送一个 包含名为Last-Evcnt-ID的特殊HTTP头部的请求/以便服务器知道下一次该触发哪个事件。在多次 连接的事件流中，这种机制nf以确保浏览器以正确的顺序收到连接的数据段。

21.5.5 Web Sockets

要说最令人津津乐道的新浏览器API,就得数Web Sockets 了。Web Sockets的目标是在一个单独的 持久连接上提供全双T、双向通信。在JavaScript中创建了 Web Socket之后，会冇一个HTTP谙求发送 到浏览器以发起连接。在取将服务器响应后，建立的连接会使用HTTP升级从HTTP协议交换为Web Socket协议。也就是说，使用标准的HTTP服务器无法实现Web Sockets,只有支持这种协议的专门服 务器才能正常工作。

21

 

由于Web Sockets使用了自定义的协议，所以URL模式也略有不同。未加密的连接不再是hctp：//, 而是ws://;加密的连接也不是https://,而是wss://。在使用Web Socket URL时，必须带者这个 模式，因为将来还有可能支持其他模式。

使用自定义协议而非HTTP协议的好处是，能够在客户端和服务器之间发送非常少®的数据，而不 必担心HTTP那样字节级的开销。由T传递的数据包很小，因此Web Sockets非常适合移动应用。毕竟 对移动应用而言，带宽和网络延迟都是关键问题。使用自定义协议的缺点在于，制定协议的时间比制定 JavaScript API的时间还要长。Web Sockets曾儿度搁浅，就因为不断有人发现这个新协议存在一致性和 安全性的问题。Firefox4和Opera 11都曾默认启用Web Sockets,但在发布前夕乂禁用了，因为又发现 了安全隐患。目前支持'^吐80（:1«18的浏览器/!1如<'<«6+、Safari 5+ x Chrome和iOS 4+版Safari。

\1. Web Sockets API

要创建Web Socket,先实例一个WebSocket对象并传人要连接的URL： var socket = new WebSocket{Bws://[www.example.com/server.php"）](http://www.example.com/server.php%22%ef%bc%89);

注意，必须给WebSocket构造闲数传人绝对URL。同源策略对Web Sockets不适用，因此可以通 过它打开到任何站点的连接。至于是否会与某个域中的页面通信，则完全取决于服务器。（通过握手信 息就可以知道请求来自何方。）

实例化了 WebSocket对象后，浏览器就会马上尝试创建连接。与XHR类似，WebSocket也有-个表示当前状态的readyState属性。不过，这个属性的值与XHR并不相同，而是如下所示。

□    WebSocket.OPENING (0):正在建立连接。

□    WebSocket.OPEN (1)：已经建立连接。

口 WebSocket.CLOSING (2):正在关闭连接。

□    WebSocket.CLOSE (3)：已经关闭连接。

WebSocket没有readys tat echange事件；不过，它存其他事件，对应着不同的状态。readyState 的值永远从0开始。

要关闭Web Socket连接，可以在任何时候调用close (》方法。 socket.close();

调用了 closeU之后，readyState的值立即变为2 (正在关闭)，而在关闭连接后就会变成3。

2.发送和接收数据

Web Socket打开之后，就可以通过连接发送和接收数据。要向服务器发送数据，使用send 方法 并传人任意字符串，例如：

var socket = new WebSocket("ws:"[www.example.com/server.php](http://www.example.com/server.php)");

socket.send("Hello world I");

因为Web Sockets只能通过连接发送纯文本数据，所以对于复杂的数据结构，在通过连接发送之前， 必须进行序列化。下面的例子展示丫先将数据序列化为一个JSON字符串，然后再发送到服务器：

var message = {

time: new Date()r text: "Hello world!", clientld: "asdfp8734rew*

}；

socket.send(JSON.stringify(message));

接下来，服务器要读取其中的数据，就要解析接收到的JSON字符串。

当服务器向客户端发来消息时，WebSocket对象就会触发message事件。这个message事件与 其他传递消息的协议类似，也是把返回的数据保存在event.data属性中。

socket.onmessage - function(event)( var data = event.data;

//处理数据

};

与通过send发送到服务器的数据一样，event .data中返阿的数据也是字符串。如果你想得到 其他格式的数据，必须手丁_解析这些数据。

3.其他事件

WebSocket对象还有其他三个事件，在连接生命周期的不同阶段触发。

□    open：在成功建立连接时触发。

□    error：在发生错误时触发，连接不能持续。

□    close：在连接关闭时触发。

WebSocket对象不支持DOM 2级事件侦听器，因此必须使用DOM 0级语法分别定义每个事件处

理程序。

var socket = new WebSocket("ws://[www.example.com/server.php](http://www.example.com/server.php)");

socket.onopen = function(){

alerc{"Connection established.”)；

};

socket.onerror = function(){

alert("Connection error.");

};

socket.onelose = function(){

alert("Connection closed.■);

};

在这三个事件中，只有close事件的event对象冇额外的倍息。这个事件的事件对象有三个额外 的属性：wasClean、code和reason。•中，wasClean是■-个布尔值，表示连接姑否已经明确地关 闭；code是服务器返回的数值状态码；而reason是一个字符串，包含服务器发回的消息。可以把这 些信息显示给用户，也可以记录到日志中以便将来分析。

socket.onelose = function(event){

console.log("Was clean? • + event.wasClean + ” Code=" + event.code 4 ■ Reason” f event.reason);

);21.5.6 SSE 与 Web Sockets

面对某个体的用例，在考虑是使用SSE还是使用Web Sockets时，可以考虑如下几个因索。疗先， 你是否有fl由度建立和维护Web Sockets服务器？因为Web Socket协议不同于HTTP,所以现冇服务器 不能用于Web Socket通信。SSE倒是通过常规HTTP通信，因此现夯服务器就可以满足需求。

21

 

第二个要考虑的问题是到底需不需要双向通信。如果用例只需读取服务器数据(如比赛成绩)，那 么SSE比较容易实现。如果用例必须双向通信(如聊天室)，那么Web Sockets显然更好。别忘了，在 不能选择Web Sockets的情况下，组合XHR和SSE也是能实现双向通信的。

21.6安全

讨论Ajax和Comet安全的文章町谓连篇累牍，而相关主题的书也已经出了很多本了。大型Ajax应 用程序的安全问题涉及面非常之广，仴我们可以从普遍意义上探讨一些基本的问题。

首先，可以通过XHR访问的任何URL也可以通过浏览器或服务器来访问。下面的URL就是一个 例子。

/getuserinfo.php?id=23

如果是向这个URL发送谙求，町以想象结果会返t!l ID为23的用户的某些数据。谁也无法保证别 人不会将这个URL的用户ID修改为24、56或其他值。W此，getuserinfo.php文件必须知道话求者 是否真的行权限访问要请求的数据；否则，你的服务器就会门户大开,_任何人的数据都可能被泄漏出去。

对于未被授权系统有权访问某个资源的W况，我们称之为CSRF (Cross-Site Request Forgery,跨站 点谙求伪造)。未被授权系统会伪装A己，让处理清求的服务器认为它是合法的。受到CSRF攻击的Ajax

程序有大冇小，攻击行为既有旨在揭示系统徧洞的恶作剧，也有恶意的数据窃取或数据销毁。

为确保通过XHR访问的URL安全，通行的做法就是验证发送请求者是否有权限访问相应的资源。

有下列几种方式可供选择。

□要求以SSL连接来访问可以通过XHR请求的资源。

□要求每。-次请求都要附带经过相应算法计算得到的验证码。

请注意，下列招施对防范CSRF攻击不起作用。

□要求发送POST而不是GET请求一很容易改变。

□检査来源URL以确定是否可信一来源记录很容易伪造。

□基于cookie信息进行验证一同样很容易伪造。

XHR对象也提供了一些安全机制，虽然表面上看可以保证安全，但实际上却相当不可靠。实际上, 前面介绍的open ()方法还能再接收两个参数：要随请求一起发送的用户名和密码。带有这两个参数的 请求可以通过SSL发送给服务器上的页面，如下面的例子所示。

xhr. open {"get", "example-php", true, "username", "password"); //不鮝这样做！ ！

(^\    即便可以考虑这种安全机制，但还是尽量不要这样做。把用户名和密码保存在

JavaScript代码中本身就是极为不安全的。任何人，只要他会使用JavaScript调试器，

就可以通过查看相应的变量发现纯文本形式的用户名和密码。

21.7小结

Ajax是无®刷新页面就能够从服务器取得数据的一种方法。关于Ajax,可以从以下几方面来总结 下。

口负责Ajax运作的核心对象是XMLHttpRequest (XHR)对象。

□ XHR对象由微软最早在IE5中引人，用于通过JavaScript从服务器取得XML数据。

□在此之后，Firefox、Safari、Chrome和Opera都实现了相同的特性，使XHR成为了 Web的一个 事实标准。

□虽然实现之间存在差异，但XHR对象的基本用法在不同浏览器间还是相对规范的，因此可以放 心地用在Web开发当中。

同源策略是对XHR的一个主要约束，它为通信设S了“相同的域、相同的端口、相同的协议”这一 限制。试图访问上述限制之外的资源，都会引发安全错误，除非采用被认可的跨域解决方案。这个解决 方案叫做 CORS ( Cross-Origin Resource Sharing,跨源资源共亨)，IE8 通过 XDomainRequest 对象支持 CORS.其他浏览器通过XHR对象原生支持CORS。阁像Ping和JSONP是另外两种跨域通信的技术， 但不如CORS稳妥。

Comet是对Ajax的进一步扩展，让服务器几乎能够实时地向客户端推送数据。实现Comet的手段 主要有两个：长轮询和HTTP流。所有浏览器都支持长轮询，而只有部分浏览器原生支持HTTP流。SSE (Server-Sent Events,服务器发送事件)是一种实现Comet交互的浏览器API,既支持长轮询.也支持 HTTP 流。

Web Sockets是一种与服务器进行全双工、双向通信的信道。与其他方案不同，Web Sockets不使用 HTTP协议，而使用一种自定义的协议。这种协议专门为快速传输小数据设计。虽然要求使用不同的 Web服务器，但却具有速度上的优势。

各方面对Ajax和Comet的鼓吹吸引了越来越多的开发人员学习JavaScript,人们对Web开发的关注 也再度升温。与Ajax有关的概念都还相对比较新，这些概念会随着时间推移继续发展。

Ajax是一个非常庞大的主题，完整地讨论这个主题超出了本书的范围。要想了解 有关Ajax的更多信息，请读者参考＜Ajax离级程序设计（第2版）＞。



