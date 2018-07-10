##### 第18*

JavaScript 与 XML

本章内容

□检测浏览器对XML DOM的支持 □理解 JavaScript 中的 XPath □使用XSLT处理器

几何时，XML—度成为存储和通过因特网传输结构化数据的标准„透过XML的发展，能够 清晰地看到Web技术发展的轨迹。DOM规范的制定，不仅是为了方便在Web浏览器中使用

18

 

XML,也是为了在桌面及服务器应用程序中处理XML数据。此前，由于浏览器无法解析XML数据,

很多开发人员都要动手编写自己的XML解析器。而自从DOM出现后，所有浏览器都内置了对XML的 原生支持(XMLDOM),同时也提供了一系列相关的技术支持。

18.1浏览器对XML DOM的支持

在正式的规范诞生以前，浏览器提供商实现的XML解决方案不仅对XML的支持程度参差不齐， 而且对同一特性的支持也各不相同。DOM2级是第一个提到动态创建XML DOM概念的规范。D0M3 级进一步增强了 XML DOM,新增了解析和序列化等特性。然而，当DOM3级规范的各项条款尘埃落 定之后，大多数浏览器也都实现了各自不同的解决方案。

18.1.1 D0M2 级核心

我们在第 12章曾经提到过,DOM2级在 document. implementation 中引人了 createDocument () 方法。IE9+、Firefox、Opera、Chrome和Safari都支持这个方法。想一想，或许你还记得可以在支持DOM2 级的浏览器中使用以下语法来创建一个空白的XML文档：

var xmldom = document.implementation.createDocument(namespaceUri, root, doctype)；

在通过JavaScript处理XML时，通常只使用参数root,因为这个参数指定的是XMLDOM文档元 索的标签名。而namespaceUri参数则很少用到，原因是在JavaScrip中管理命名空间比较困难。最后， doctype参数用得就更少了。

因此，要想创建一个新的、文档元素为＜root;JXML文档，可以使用如下代码：

var xmldom = document.implementation.createDocument(*"r "root", null};

alert(xmldom.documentElement.tagName)；    //-root"

var child = xmldom.createElement("child*)； xmldom.documentElement.appendChiId(child)；

DOMLevel2CoreExampleO 1. htm

这个例子创建了一个XML DOM文档，没存默认的命名空间，也没有文档类型。但要注意的是，尽 管不需要指定命名空间和文档类型，也必须传人相应的参数。具体来说，给命名空间URI传入一个空字 符串，就意味若未指定命名空间，而给文档类型传人null,就意味着不指定文梏类型。变量xmldom 中保存着一个DOM2级Document类型的实例，带有第12章讨论过的所有DOM方法和属性。我们这 个例子显示了文档元索的标签名，然后又创建并给文档元素添加了一个新的子元素。

要检测浏览器是否支持DOM2级XML,可以使用下面这行代码：

var hasXmlDom = document.implementation.hasFeature("XML", B2.0");

在实际开发中，很少需要从头开始创建-个XML文档，然后再使用DOM文档为其添加元素。更 常见的情况往往是将某个XML文档解析为DOM结构，或者反之。由于DOM2级规范没有提供这种功 能，因此就出现f 一些事实^；•准。

18.1.2 DOMParser 类型

为了将XML解析为DOM文档，Firefox引入了 DOMParser类型；后来，IE9、Safari、Chrome和 Opera也支持了这个类型。在解析XML之前，首先必须创建一个DOMParser的实例，然后再调用 parseFromStringO方法。这个方法接受两个参数：要解析的XML字符串和内容类型(内容类型始 终都应该是"text/xml")。返回的值是一个Document的丈例。来看下面的例子。

var parser = new DOMParser()；

var xmldom = parser .parseFromString(•<rootxchild/></root>", ■ text/xml■) ?

alert(xmldom.documentElement.tagName);    Z/■root"

alert(xmldom.documentElement.firstChiId.tagName)；    //"child"

var anotherChild = xmldom.createElement{"child"); xmldom.documentElement.appendChild(anotherChild};

var children = xmldom.getElementsByTagName("child"}； alert(children.length);    //2

DOMParserExampleOl. htm

在这个例子中，我们把一个简单的XML字符申解析成了一个DOM文档。解析得到的DOM结构以 <root>作为其文档元索，该元素还有一个《:1111<1>子元素。此后，就可以使用DOM方法对返回的这个 文档进行操作了。

DOMParser只能解析格式良好的XML，因而不能把HTML解析为HTML文档。在发生解析错误 时，仍然会从parseFromStringU中返回一个Document对象，但这个对象的文档元素是 <parsererror>,而文档元素的内容是对解析错误的描述。下面是一个例子。

<parsererror xmlns="http： "[www.mozilla.org/newlayout/xml/parsererror.xml">XML](http://www.mozilla.org/newlayout/xml/parsererror.xml%22%3eXML)

Parsing Error： no element found Location： <file:///I:/My%20Writing/My%20BooksZ>

Professional%20JavaScript/Second%2 0Edition/Exaniples/Chl5/DOMParserExainple2.htm Line

Number L, Column 7： <sourcetext> & It;root & gt;------^</sourcetext > < /parsererror>

Firefox和Opera都会返回这种格式的文档。Saferi和Chrome返回的文档也包含<parsererror>元素， 但该元素会出现在发生解析错误的地方。E9会在调用parseFromString <)的地方抛出一MS析错误。 由于存在这些差别，因此确定是否发生解析错误的最佳方式就是，使用一个try catch语句块，如果没

有错误，则通过getElementsByTagName ()来査找文档中是否存在<parsererror>元素，如下面的例 子所示。

var parser = new DOMParser(), xmldom' errors;

try {

xmldom = parser.parseFromString("<root>", *text/xml"); errors = xmldom.getElementsByTagName("parsererror")； if (errors.length > 0){

throw new Error{"Parsing error!"};

}

} catch (ex) {

alert (* Parsing error!”：

)

DOMParserExample02. htm

18

 

这例子显示，要解析的字符串中缺少了闭标签</root>,而这会导致解析错误。在IE9+中，此时会 抛出错误。在Firefox和Opera中，文格元素将是<parsererror>,而在Safari和Chrome中， <parsererror>JS<root>(iO第一个子元索o 调用 getElementsByTagName ("parsererror")能够 应对这两种情况。如果这个方法返回了元素，就说明有错误发生，继而通过一个瞀告框显示出来。当 然，你还可以更进一步，从错误元素中提取出错误信息。

18.1.3 XMLSerializer 类型

在引入DOMParser的同时，F4refox还引人了 XMLSerializer类型，提供了相反的功能：将DOM 文椅序列化为XML字符串。后来，IE9+、Opera、Chrome和Safari都支持了 XMLSerializer。

要序列化DOM文档，首先必须创建XMLSerializer的实例，然后将文档传入其serializeTo-String ()方法，如下面的例子所东。    •

var serializer = new XMLSerializer():

var xml = serializer.serializeToString(xmldom)； alert(xml);

XMLSerializerExampleO 1 .htm

但是，serializeToStringU方法返PI的字符串并不适合打印，因此看起来会显得乱糟糟的。 XMLSerializer可以序列化任何冇效的DOM对象，不仅包括个别的节点，也包括HTML文档。

将HTML文档传人serializeToStringO以后，HTML文档将被视为XML文档，因此得到的代码也 将是格式良好的。

如果将非DOM对象传入serializeToString(),会寺致错谋发生。

18.1.4旧8及之前版本中的XML

事实上，IE是第一个原生支持XML的浏览器，而这一支持是通过ActiveX对象实现的。为了便于 桌面应用程序开发人员处理XML,微软创建了 MSXML库；但微软并没有针对JavaScript创建不同的对

象，而只是让Web开发人员能够通过浏览器访问相同的对象。

第8章啓经介绍过ActiveXObject类型，通过这个类型可以在JavaScript中创建ActiveX对象的

实例。同样，要创建一个XML文档的实例，也要使用ActiveXObject构造函数并为其传人一个表示 XML文档版本的字符申。有6种不同的XML文档版本可以供选择。

□    Microsoft .XmlDom：最初随同发布；不建议使用。

□    MSXML2.D0MDocument：为方便脚本处理而更新的版本，建议仅在特殊悄况下作为后备版本 使用。

□    MSXML2 . DOKDocument .3.0：为了在JavaScript中使用，这是嚴低的建议版本。

□    MSXML2 .DOMDocument .4.0:在通过脚本处理时并不可靠，使用这个版本可能导致安全警告。

□    MSXML2 .DOMDocument .5.0：在通过脚本处理时并不可靠，使用这个版本同样可能导致安全

警吿。

□    MSXML2 . DOMDocument .6.0：通过脚木能够可靠处理的最新版本。

在这 6 个版本中，微软只推荐使用 MSXML2 .DOMDocument.6.0 或 MSXML2.DOMDocument.3.0; 前者是最新最可靠的版本，而后者则是大多数Windows操作系统都支持的版本。可以作为后备版本的 MSXML2.DOMDocument,仅在针对IE5.5之前的浏览器开发时才有必要使用。

通过尝试创建每个版本的实例并观察是否有错误发生，可以确定哪个版本可用。例如： function createDocument(){

if (typeof arguments.callee.activeXString != "string"){

var versions = ["MSXML2.DOMDocument.6.0•f "MSXML2.DOMDocument.3.0",

•MSXML2.DOMDocument■],

i, len;

for (i=0,len=versions.length; i < len; i++>{ try {

new ActiveXObject(versions(i]);

arguments.callee.activeXString = versions[i]；

break；

} catch (ex){

"跳过

}

}

}

return new ActiveXObject(arguments.callee.activeXString)；

IEXmlDomExampleO 1. htm

这个闲数中使用for循环迭代了毎个4能的ActiveX版本。如果版本无效，则创建新 ActiveXObject的调用就会抛出错误；此时，catch语句会捕获错误，循环继续。如果没有发生错误, 则可用的版本将被保存在这个函数的activeXString属性中。这样，就不必在每次调用这个函数时都 重复检査可用版本了一直接创建并返回对象即可。

要解析XML字符串，首先必须创建一个DOM文档，然后调用loadXMLO方法。新创建的XML 文档完全是一个空文档，因而不能对其执行任何操作。为loadXKLf)方法传人的XML字符串经解析之 后会被填充到DOM文档中。来看下面的例子。

var xmldom = createDocument{)；

xmldom. loadXML {"<root><child/></root>") '•

alert(xmldom.documentElement.tagName)?    //"root”

alert(xmldom.documentElement.firstChild.LagName)；    //"child"

var anotherChild = xmldom.createElement(■child*);

xmldom.documentElement.appendChild(anotherChild);

var children = xmldom.getElementsByTagName("child")；

alert(children.length);    //2

fEXmlDomExampleOl .him

在新DOM文档中填充了 XML内容之后，就可以像操作其他DOM文档一样操作它了（可以使用任 何方法和属性）。

如果解析过程中出错，可以在parseError属性中找到错误消息。这个属性本身是_个包含多个属 性的对象，每个属性都保存着有关解析错误的某一方面信息。

18

 

□    errorCode：错误类型的数值编码；在没有发生错误时值为0。

□    filePos：文件中导致错误发生的位置。

□    line:发生错误的行。

□    linepos：发生错误的行中的字符。

□    reason：对错误的文本解释。

□    srcText：导致错误的代码。

□    url：导致错误的文件的URL （如果有这个文件的话）。

另夕卜，parseError的valueOf （）方法返回errorCode的值，因此可以通过下列代码检测是否发 生了解析错误。

if (xmldom.parseError != 0)(

alert("Parsing error occurred."};

}

错误类型的数值编码可能是正值，也可能是负值，因此我们只需检测它是不是等于0。要取得有关 解析错误的详细信息也很容易，而且可以将这些信息组合起来给出更有价值的解释。来看下面的例子。

if (xmldom.parseError != 0){

Alert("An error occurred:\nZrror Code:"

\+ xmldom.parseError.ezxorCode + "\n"

♦ "Line: " + xmldom.parseError.line -i- "\n*

\+ "Line Pob： " 4 xaildOTi.parseError.linepos * "\n"

\+ "Reason: • + xmldom.parseError.reason);

IEXmlDomExample02. htm

应该在调用loadXMLO之后、査询XML文档之前，检査是否发生了解析错误。

1.序列化XML

IE将序列化XML的能力内置在了 DOM文档中。每个DOM节点都有一个xml属性，其中保存着 表示该节点的XML字符串。例如：

alert (xmldom.xml)；

文档屮的毎个节点都支持这个简单的序列化机制，无论是序列化整个文档还是某个子文档树，都非 常方便。

2.加载XML文件

IE中的XML文档对象也可以加载来自服务器的文件。与DOM3级中的功能类似，要加载的XML 文档必须与页面中运行的JavaScript代码来自同一台服务器。同样与D0M3级规范类似，加载文档的方 式也可以分为同步和异步两种。要指定加载文档的方式，可以设S async属性，true表示异步，false 表示同步(默认值为true )。来看下面的例子。

var xmldom = creaceDocument(J; xmldom.async = false；

在确定了加载XML文档的方式后，调用load()可以启动F载过程。这个方法接受一个参数，即 要加载的XML文件的URL„在同步方式下，调用load (＞后可以立即检测解析错误并执行相关的XML 处理，例如：

var xmldom createDocumenc(); xmldom.async = false；

xznldom.load( "example.xml") /

if (xmldom.parseError 1= 0)(

//处理嫌误

} else {

alert (xmldom. documentBlement. tagName) ; //"root"

alert(xmldom.documentSleaent.firatChild.tagName); //"child"

var auotherChild s xmldom.createBlement("child");

xioldom. documentBlement .appendChiId(onotherChiId);

var children = xmldom.getSlemezxtsByTagNane ("child"); alert(children.length);    //2

alert (xzaldom.xml)；

IEXmlDomExample03. htm

由于是以同步方式处理XML文件，因此在解析完成之前，代码不会继续执行，这样的编程丄作要 简单一点。虽然同步方式比较方便，何如果下载时间太长，会导致程序反应很慢。因此，在加载XML 文档时，通常都使用异步方式。

在异步加载XML文件的情况下，：要为XML DOM文样的onreadystatechange事件指定处理 程序。有4个就绪状态(ready state )。

□    1: DOM正在加载数据。

□    2: DOME经加载完数据。

□    3： DOM已经可以使用，供某些部分对能还无法访问。

□    4： DOM已经完全可以使用。

在实际开发中，要关注的只有一个就绪状态：4。这个状态表示XML文件已经全部加载完毕，而且 已经全部解析为DOM文档。通过XML文档的readyState属性可以取得其就绪状态。以异步方式加 载XML文件的典型模式如下。

var xmldom = createDocument();

xmldom.async = true;

xmldaa.onreadystatechange » function(){ if (xmldcnn.readyState e= 4) {

if (zmldom.parseError 1= 0){

alert("An error occurred:\nSrror Code: n

\+ xnldom.parseError.errorCode ♦ "\n"

\+ "Line： " + xmldom.paraeError.line    "\n"

\+ ”Line Poe: " + xzoldom.parseError.linepos 4- w\n" ♦ "Reason： ■ + xmldom.parseError.reason);

} else {

alert (xznldom. document Element. tagNsune) / //"root"

alert(xmldom.documentElement.firstChild.tagName)； //"child"

var anotherChild s xmldom.createBlement (**childR)；

xmldom.docunentElement.appendChild(anotherChild);

18

 

var children = xmldom.getElementsByTagName ("child**); alert(children.length)/    //2 alert (xmldom.xml)；

}

)

}t

xmldom.load( "example.xml”> ;

IEXmlDomExample04. htm

要注意的是，为onreadystatechange事件指定处理程序的语句，必须放在调用load＜）方法的 语句之前；这样，才能确保在就绪状态变化时调用该事件处理程序。另外，在事件处理程序内部，还必 须注意要使用XML文档变量的名称（xmldom ）,不能使用this对象。原因是ActiveX控件为预防安全 问题不允许使用this对象。当文裆的就绪状态变化为4时，就可以放心地检测是否发生了解析错误， 并在未发生错误的情况下处理XML 了。

虽然可以通过XML DOM文档对象加载XML文件,但公认的还是使用XMLHttp-Request对象比较好。有关XMLHttpRequest对象及Ajax的相关内容，将在第21 章讨论。

18.1.5跨浏览器处理XML

很少有开发人员能够有福气专门针对一款浏览器做开发=因此，编写能够跨浏览器处理XML的函 数就成为了常见的需求。对解析XML而言，下面这个函数可以在所有四种主要浏览器中使用。

function parseXml（xml）｛ var xmldom = null；

if (typeof DOMParser != "undefined"){

xmldom = (new DOMParser()).parseFromString(xml, "text/xml")；

var errors s xroldom.getElementsByTagName("parsererror")； if (errors.length){

throw new Error("XML parsing error:" + errors[0].textContent);

}

} else if (typeof ActiveXObject    *undefined"){

xinldom = createDocument () ? xmlaom.loadXKL(xml)； if (xmldom.parseError 1= 0){

throw new Error("XML parsing error: " + xmldom.parseError.reason);

}

} else {

throw new Error("No XML parser available.")；

}

return xmldom;

CmssBrowserXmlExampleOl .htm

这个parseXmlO函数只接收一个参数，即可解析的XML字符串。在函数内部，我们通过能力检 测來确定要使用的XML解析方式。DOMParser类型是受支持最多的解决方案，因此首先检测该类型是 否有效。如果是，则创建一个新的DOMParser对象，并将解析XML字符串的结果保存在变量xrnldom 中。由于DOMParser对象在发生解析错误时不抛出错误(除IE94■之外)，因此还要检测返问的文档以 确定解析过程是否顺利。如果发现了解析错误，则根据错误消息拋出一个错误。

函数的最后一部分代码检测了对ActiveX的支持，并使用前面定义的createDocument ()函数来创 建适当版本的XML文档。与使用DOMParser时一样，这里也需要检测结果，以防有错误发生。如果 确实有错误发生，同样也需要抛出一个包含错误原W的错误。

如果上述XML解析器都不nJ用，函数就会抛出一个错误，表示无法解析广。

在使用这个函数解析XML字符串时，应该将它放在try-catch语句当中，以防发生错误。来看 下面的例子

var xmldom = null;

try {

xmldom = parseXml ("<root><child/x/root>")； } catch (ex){

alert(ex.message);

}

//进一步处理

CrossBrowserXmlExampleOJ.ktm

对序列化XML而言，也可以按照同样的方式编写一个能够在四大浏览器中运行的函数。例如：

function serializeXml(xmldom){

if (typeof XMLSerializer != "undefined"){

return (new XMLSerializer()).serializeToString(xmldom);

} else if (typeof xmldom.xml !- "undefined"){

return xmldom.xml;

} else (

throw new Error("Could not serialize XML DOM."};

}

CrossBrowserXmlExample02. htm

这个serializeXml ()函数接收一个参数，即要序列化的XML DOM文约。与parseXml ()函数 —样，这个函数旨先也是检测受到最广泛支持的特性，即XMLSerializero如果这个类型有效，则使 用它来生成并返回文裆的XML字符串。由于ActiveX方案比较简单，只使用丫一个xml属性，因此这 个函数直接检测了该属性。如果上述两方面尝试都失败f，函数就会抛出一个错误，说明序列化不能进 行。一般来说，只要针对浏览器使用了适当的XMLDOM对象，就不会出现无法序列化的情况，因而也 就没有必要在try-catch语句中调用serializeXml () „结果，就只需如下一行代码即对：

var xml = serializeXml;

18

 

只不过由于序列化过程的差异，相同的DOM对象在不同的浏览器下，有可能会得到不同的XML 字符串。

18.2浏览器对XPath的支持

XPath是设计用来在DOM文档中査找节点的一种手段，因而对XML处理也很重要。但是，D0M3 级以前的标准并没有就XPath的API作出规定；XPath是在DOM3级XPath模块中首次跻身推荐榇准行 列的。很多浏览器都实现了这个推荐标准，但IE则以自己的方式实现了 XPath。

18.2.1 DOM3 级 XPath

DOM3级XPath规范定义了在DOM中对XPath表达式求值的接口。要确定某浏览器是否支持DOM3 级XPath，可以使用以下JavaScript代码：

var supportsXPath = document.implejnentation.hasFeature("XPath', "3.0");

在D0M3级XPath规范定义的类型中，最重要的两个类型是XPathEvaluator和XPathResult。 XPathEvaluator用于在特定的上下文中对XPath表达式求值。这个类型有下列3个方法。

□    creat eExpr ess ion (expression, nsresol ver):将 XPath 表达式及相应的命名空间信息转 换成一个XPathExpression,这是丧询的编译版。在多次使用同一个查询时很有用。

□    createNSResolver (node):根据node的命名空间信息创建一个新的XPathNSResolver对 象。在基丁-使用命名空间的XML文档求值时，需要使用XPathNSResolver对象。

□    evaluate (expression, context, nsresolver, type, result):在给定的上下文中， 基于特定的命名空间信息来对XPath表达式求值。剩下的‘参数指定如何返回结果。

在 Firefox、Safari、Chrome和 Opera 中，Document 类型通常都是与 XPathEvaluator 接口一起实 现的。换句话说，在这®浏览器屮，既可以创建XPathEvaluator的新实例，也可以使用Document 实例中的方法(XML或HTML文档均是如此)。

在上面这三个方法中，evaluate(＞是最常川的。这个方法接收5个参数：XPath表达式、上下文 节点、命名空间求解器、返回结果的类型和保存结果的XPathResult对象（通常是null,因为結果 也会以函数值的形式返回）。其中，第三个参数（命名空间求解器）只在XML代码中使用了 XML命名 空间时有必要指定；如果XML代码中没有使用命名空间，则这个参数应该指定为null。第四个参数（返 回结果的类型）的取值范围是下列常fl之一。

口 XPathResult. ANY_TYPE:返回与XPath表达式匹配的数据类型。

□    XPathResult.NUMBER_TY?E:返回数值。

□    XPathResult . STRING_TYPE:返回字符串值。

口 XPathResult. BOOLEAN_TYPE:返回布尔值0

□    XPathResult.UNORDERED_NODE_ITERATOR_TYPE:返回匹配的资点集合，但集合中节点的次 序不一定与它们在文档中的次序一致。

□    XPathResult.ORDERED_NODE_ITERATOR_TYPE:返LBJ匹配的节点集合，集合中节点的次序与 它们在文档中的次序一致。这是最常用的结果类型。

□    XPathResult.UNORDERED_NODE_SNAPSHOT_TYPE:返回节点集合的快照，由于是在文档外部 捕获节点，因此对文档的后续操作不会影响到这个节点集合。集合中节点的次序不一定与它们 在文档中的次序••致。

□    XPathResult.ORDERED_NODE_SNAPSHOT_TYPE：返回节点集合的快照，由于是在文档外部捕 获节点，因此对文档的后续操作不会影响到这个节点集合。集合中节点的次序与它们在文挡中 的次序一致。

□    XPathResult .ANY_UNORDERED_NODE_TYPE:返回匹配的节点集合，但集合中节点的次序不 一定与它们在文档中的次序一致

□    XPathResult.FIRST_ORDERED_NODE_TYPE:返回只包含一个节点的节点集合，包含的这个 节点就是文档中第一个匹配的节点。

指定的结果类型决定了如何取得结果的值。下面来看一个典型的例子。

var result - xmldom.evaluate（"employee/name", xmldom.documentElement, null,

XPathResult.ORDERED_NODE_ITERATOR_TYPE, null）;

if (result    null) {

var node = result.iterateNext(); while(node) {

alert(node.tagName); node = node.iterateNext{)?

}

}

DomXPatkExampleO 1. htm

这个例+中为返回结果指定的是XPathResult. ORDERED_NODE_ITERATOR_TYPE,也是最常用的 结果类型。如果没有节点匹配XPath表达式，evaluate （）返回null;否则,它会返回一个XPathResult 对象。这个XPathResult对象带有的属性和方法，可以用来取得特定类型的结果。如果节点是一个节 点迭代器，无论是次序一致还是次序不一致的，都必须要使用iterateNext （）方法从节点中取得匹配 的节点。在没有更多的匹配节点时，RerateNextO返回null。

如果指定的是快照结果类型（不管是次序•-致还是次序不一致的），就必须使用snapshotltemO 方法和snapshotLength属性，例如：

var result = xmldom.evaluate(*employee/name*, xmldom.documentElement# null,

XPathResult.ORDERED_NODE_SNAPSHOT_TYPE/ null)i

if (result !== null) {

for (var i»0, len=result.snapshotLength; i < len; i++) { alert(result.snapshotltem(i).tagName)j

}

DomXPathExample02. htm

这里，snapshotLength返回的是快照中节点的数量，而snapshotltem()则返回快照中给定位 置的节点(与NodeList中的length和item ()相似)o

1.单节点结果

指定常量XPathResult. FIRST_ORDERED_NODE_TYPS会返回第-、个匹配的节点，可以通过结果 的singleNodeValue属性来访问该节点。例如：

var result = xmldom.evaluate('employee/name", xmldom.documentElement, null,

18

 

XPathResult.PIRST_ORDERED_NODE_TYPE, null)/

if (result !== null) {

alert(result•singleNodeValue.tagName)/

}

f)omXPathExamp!e03. htm

与前面的査询一样，在没有匹配节点的情况下，evaluate{)返回null。如果有节点返回，那么就 可以通过singleNodeValue属性来访问它。

2简单类型结果

通过XPath也可以取得简单的非节点数据类型，这时候就要使用XPathResult的布尔值、数值和 字符串类型了。这几个结果类型分别会通过booleanValue、numberValue和stringValue属性返 回一个值。对于布尔值类型，如果至少有一个节点与XPath表达式匹配，则求值结果返回true,否则 返回false。来看下面的例子。

var result = xmldom.evaluate("employee/name", xmldom.documentElement, null, XPathResult .BOOLEAN_TYPE, null"

alert(result.booleanValue);

DomXPathExample04. htm

在这个例子中，如果有节点K配"employee/name",则booleanValue属性的值就是true。

对于数值类型，必须在XPath表达式参数的位置上指定一个能够返回数值的XPath函数，例如计算

与给定模式匹配的所有节点数量的count ()o来看下面的例子。

var result = xmldom.evaluate("count(employee/name)■, xmldom.documentElement, null, XPathResult.NUMBER_TYPE, null};

alert(result.numberValue);

DomXPathExample05. htm

以上代码会输出与1*employee/naine”PQ配的节点数虽(即2 )。如果使用这个方法的时候没有指定 与前例类似的XPath函数，那么numberValue的值将等于NaNo

对于字符串类型，evaluate (>方法会查找与XPath表达式匹配的第一个节点，然后返网其第一个 子节点的值(实际上是假设第一个子节点为文本节点)。如果没有匹配的节点，结果就是个空字符串。 来看一个例子。

t var result = xmldom.evaluate(-employee/name"r xmldom.documentElement, null, XPathResult.STRING__TYPE# null);

alert(result.stringValue);

DomXPathExample06. htm

这个例子的输出结果中包含着与•element/name-K配的第一个元索的第--个子节点中包含的字 符串。

3.默认类型结果

所有XPath表达式都会ft动映射到特定的结果类塑。像前面那样设置特定的结果类型，可以限制表 达式的输出。而使用XPathResult.ANY_TYPE常量可以自动确定返回结果的类型。一般来说，fl动选 择的结果类型吋能是布尔值、数值、字符串值或一个次序不一致的节点迭代器。要确定返回的是什么结 果类型，可以检测结果的resultType属性，如下面的例子所示。

var result = xmldom.evaluate(■employee/name", xmldom.documentElement, null,

XPathResult .ANY_TYI>E, null) j

if (result !== null) {

switch(result.resultType) {

case XPathResult.STRING_TYPE:

//处茂乎符争类炎 break;

case XPathResult.NUKBKR_TYPE: //处茂数值类塑 break;

case XPathResult.BOOLBAN_TYPE: //处殖布尔值类型 break；

case ZPathResuit.DKORDBRBD_NODE_ITKRATOR_TYPK: "处殖次序不一致的节点遑代器美负 break;

default:

//处攻其他可能的結果类《

)

逋然，XPathResult.ANY_TYPE可以让我们更灵活地使用XPath,但是却要求有更多的处理代码 来处理返回的结果。

4.命名空间支持

对于利用了命名空间的XML文档，XPathEvaluator必须知道命名空间信息，然后才能正确地进 行求值。处理命名空间的方法也不止一种。我们以下面的XML代码为例。

<?xml version="l.0" ?>

<wrox:books xmlns:wrox="<http://www.wrox.com/>"> <wxox：book>

<wrox：title>Professional <wrox:author>Nicholas C.

JavaScript for Web Developers</wrox:t i 11e> Zakas</wrox：author>

 

Ajax</wrox：title> Zakas</wrox:author>

 

</wrox:book>

<wrox：book>

<wrox：title>Professional <wrox：author>Nicholas C.

<wrox:author>Jeremy KcPeak</wrox：author> <wrox:author>Joe Fawcett</wrox：author>

</wrox：book>

</wrox:books>

在这个XML文档中，所有元索定义都来自http: //www.wrox.com/命名空间，以前缀wrox标识。 如果要对这个文档使用XPath,就需要定义要使用的命名空间；否则求值将会失败。

处理命名空间的第一种方法是通过createNSResolver ()来创建XPathNSResolver对象。这个 方法接受一个参数，即文档中包含命名空间定义的节点。对于前面的XML文构来说，这个节点就是文 档元素＜wrox:books＞，它的xmlns特性定义了命名空间。可以把这个节点传递给createNS-ResolverO ,然后可以像下面这样在evaluate ()屮使用返问的结果。

18

 

var nsresolver =： xmldora.createNSResolver(xmldom.documentElement)?

var result = xmldom.evaluate("wrox:book/wrox:author0,

xmldom.documentElement, nsresolver,

XPathResult.ORDERED_NODE_SNAPSHOT_TYPE, null);

alert(result.snapshotLength)?

DomXPathExampleO 7. htm

在将nsresolver对象传人到evaluate ()之后，就可以确保它能够理解XPath表达式中使用的 wrox前缀。读者可以试一试使用相同的表达式，如果不使用XPathNSResolver的话，就会导致错误s

处理命名空间的第二种方法就是定义一个函数，让它接收--个命名空间前缀，返回关联的URI, 例如：

var nsresolver - function(prefix){ switch(prefix){

case "wrox": return "<http://www.wrox.com/>";

//其他前级

}

var result = xmldom.evaluate("count(wrox:book/wrox：author)",

xmldom.documentElement, nsresolver, XPathResult.NUMBER—TYPE, null);

alert(result.numberValue);

DomXPathExample08. htm

在不确定文档中的哪个货点包含命名空间定义的情况下，这个命名空间解析函数就可以派上用场 了。只要你知道前缀和URI,就可以定义一个返回该信息的函数，然后将它作为第H个参数传递给 evaluate ()即可o

18.2.2 IE 中的 XPath

IE对XPath的支持是内置在基于ActiveX的XML DOM文档对象中的，没有使用DOMParser返回 的DOM对象。因此，为了在IE9及之前的版本中使用XPath,必须使用基于ActiveX的实现。这个接 口在每个节点上额外定义了两个的方法：selectSingleNode<)和selectNodes() o其巾， selectSingleNodeO方法接受一个XPath模式，在找到配节点时返回第一个匹配的节点，如果没有 找到匹配的节点就返凹null。例如：

var element = xmldom.dociinientElement. selectSingleNode ("employee/name") ?

if (element 1== null){ alert(element.xml)；

}

IEXPathExampleO 1. htm

这里，会返回匹配"employ ee/name"的第-个节点。上下文节点是xmldom. document Element, 因此就调用了该节点上的selectSingleNodeO。由于调用这个方法可能会返回null值，因而有必 要在使用返回的节点之前，先检査确定它不是null。

另一个方法selectNodes ()也接收一个XPath模式作为参数，但它返回与模式匹配的所有节点的 NodeList (如果没有匹配的节点，则返回一个包含零项的NodeList )。来看下面的例子。

var elements = xmldom.documentElement.selectNodes("employee/name")； alert{elements.length)；

IEXPathExampleO2. htm

对这个例子而言，PU配"employee/name"的所有元索都会通过NodeList返回。由T不可能返问 null值，因此可以放心地使用返间的结果。但要记住，既然结果是NodeList,而其包含的元素可能 会动态变化，所以每次访问它都有可能得到不同的结果。

IE对XPath的支持非常简单。除了能够取得一个节点或一个NodeList外，不可能取得其他结果 类型。

IE对命名空间的支持

要在IE中处理包含命名空间的XPath表达式，你必须知道A己使用的命名空间，并按照下列格式 创建一个字符串：

"xmlns：prefixl='uril' xmlns:prefix2='uri2' xmlns:prefix3=*uri3 *"

然后，必须将这个字符串传入到XMLDOM文档对象的特殊方法set Property (>中，这个方法接 收两个参数：耍没置的厲性名和属性值。在这坦，属性名应该是-SelectionNamespaces，，属性值就 是按照前面格式创建的字符串。下面来看一个在DOM XPath命名空间中对XML文档求值的例子。

xmldom.set Property(•SelectionNamespaces", "xmlns:wrox= * [http://www.wrox.com/'*}](http://www.wrox.com/'*%7d);

var result = xmldom.documentElement.selectNodes("wrox：book/wrox：author"); alert(result.length);

IEXPathExampleO3. htm

对于这个DOMXPath的例子来说，如果不提供命名空间解析信息，就会在对表达式求值时导致-个错误，

18.2.3跨浏览器使用XPath

鉴丁- IE对XPath功能的支持有限，因此枠浏览器XPath只能保证达到IE支持的功能。换句话说， 也就是要在其他使用DOM3级XPath对象的浏览器中，重新创建selectSingleNodeU和 selectNodes<)方法。第一•个困数是selectSingleNode (),它接收三个参数：上下文节点、XPath 表达式和可选的命名空间对象。命名空间对象应该是下面这种字面量的形式。

{

prefixl: *uril”， prefix2: "uri2", prefix3: *uri3"

}

以这种方式提供的命名空间信息，可以方便地转换为针对特定浏览器的命名空间解析格式。下面给 出了 selectSingleNode ()函数的完整代码。

18

 

function selectSingleNode{context, expression, namespaces){

var doc = (context.nodeType != 9 ? context.ownerDocuroent : context)；

if (typeof doc.evaluate != "undefined"){ var nsresolver = null; if (namespaces instanceof Object}{

nsresolver = function (pref ixH return namespaces[prefix];

}；

}

var result = doc.evaluate(expression, context, nsresolver,

XPathResult.FIRST_ORDERED_NODE_TYPE# nul1);

return (result J == null ? result.singleNodeValue : null);

} else if (typeof context.selectSingleNode i= "undefined"){

//创建命名空间字符亊

if (namespaces instanceof Object){ var ns =""；

for (var prefix in namespaces){

if (namespaces.hasOwnProperty(prefix)}{

ns += "xmlns：■ + prefix + "='" + namespaces tprefix] + *'

}

}

doc.setProperty{•SelectionNamespaces*, ns)；

}

return context.selectSingleNode(expression)；

} else {

throw new Error("No XPath engine found.");

}

CrossBrowserXPathExampleOJ .htm

这个函数首先要确定XML文档，以便基于该文档对表达式求值。由于上下文节点可能是文档，所 以必须要检测nodeiype属性。此后，变量doc中就会保存对XML文档的引用。然后，可以检测文档 中是否存在evaluate ()方法，即是否支持DOM3级XPath。如果支持，接下来就是检测传人的 namespaces对象。在这里使用instanceof操作符而不是typeof,是因为后者对null也返回 "object-。然后将nsresolver变量初始化为null,如来提供了命名空间信息的话，就将其改为一 个函数。这个函数是一个闭包，它使用传人的namespaces对象来返回命名空间的URI。此后，调用 evaluated方法，并对其结果进行检测，在确定是节点之后再返冋该结果。

在这个函数针对DE的分支中，需要检査context节点中是否存在selectSingleNode()方法：，与UOM 分支一样，这里的第一步是有选择地构建命名空间信息。如果传人了 namespaces对象，则迭代其属性并以 适冉格式创建一^字符串。注意，这里使用了 hasOwnProperty。方法来确保对Object .prototype的任何 修改都不会影晌到当前函数。最后，调用原生的selectSingleNodeO方法并返回结果。

如果前面两种方法都没有得到支持，这个函数就会抛出一个错误，表示找不到XPath处理引擎。下 面是使用selecCSingleNode ()涵数的示例。

var result = selectSingleNode(xmldom.documentElement, "wrox:book/wrox：author",

{ wrox: "<http://www.wrox.com/>" });

alert(seriali2eXml(result))；

CrossBrowserXPathExampleOl .htm

类似地，也町以创建一个跨浏览器的selectNodes ()函数。这个两数接收与selectSingle-Node()相同的三个参数，而且大部分逻辑都相似。为了便于看清楚，我们用加粗字体突出了这两个函 数的差别所在。    '

function selectNodes(context, expression, namespaces}{

var doc = (context.nodeType 9 ? context.ownerDocument : context);

if (typeof doc.evaluate [= ■undefined"){ var nsresolver = null； if (namespaces instanceof Object){

nsresolver = function(prefix){ return namespaces[prefix]；

};

}

var result = doc.evaluate(expresBion, context, nsresolver,

XPathReault.ORDERED_NODE_SNAPSHOT_TYPE, null);

var nodes ■ new Array()j

if (result 1== null){

for (var i=0, len-reault.snapshotLezigtb.; i < lezx; i++) { nodes.push(result.snapshotItem(i))t

}

}

return nodes;

} else if {typeof context.selectNodes i= ■undefined-){

//创建命名空间字符串

if (namespaces instanceof Object){ var ns = "■；

for (var prefix in namespaces){

if (namespaces.hasOwnProperty(prefix)){

ns ♦= "xmlns:" + prefix + ■=•■ + namespaces[prefix] +

)

}

doc.setProperty("SelectionNamespaces", ns);

}

var result 里 context.selectNodes(expression)j var nodes = new Array();

for (var i*0,len-result.length; i < len； i++){ nodes.push(result[i】>;

)

return nodes；

} else {

throw new Error("No XPath engine found."};

}

I—MW— 18

 

CrossBro\vserXPathExample02. htm

很明显，其中有很多逻辑都与selectSingleNodeO方法相同。在函数针对DOM的部分，使用 了有序快照结果类型，然后将结果保存在了一个数组中。为了与IE的实现看齐，这个函数应该在没找 到匹配项的情况下也返回一个数组，因而最终都要返回数组nodes。在函数针对IE的分支中，调用了 selectNodes 0方法并将结果复制到_T一个数组中。因为IE返回的是一个NodeList,所以最好将节 点都复制到一个数组中，这样就可以确保在不同浏览器下，函数都能返回相同的数据类型。使用这个函 数的示例如下：

var result = selectNodes(xmldom.documentElement, *wrox:book/wrox:author■, { wrox： •http://www.wrox.com/" });

alert(result.length);

CrossBrawserXPathExample02. htm

为了求得最佳的浏览器廉容性，我们建议在JavaScript中使用XPath时，只考虑使用这两个方法。

18.3浏览器对XSLT的支持

XSLT是与XML相关的一种技术，它利用XPath将文档从一种表现形式转换成另一种表现形式。与 XML和XPath不同，XSLT没有正式的API,在正式的DOM规范中也没有它的位置。结果，只能依靠 浏览器开发商以自己的方式来实现它。IE是第一个支持通过JavaScript处理XSLT的浏览器。18.3.1 IE 中的 XSLT

与IE对其他XML功能的支持一样，它对XSLT的支持也是通过ActiveX对象实现的。从MSXML3.0 （即圧6.0 ）时代起，IE就支持通过JavaScript实现完整的XSLT 1.0操作。IE9中通过DOMParser创建 的DOM文档不能使用XSLT。

1.简单的XSLT转換

使用XSLT样式表转换XML文档的最简单方式，就是将它们分别加到一个D0M文档中，然后再 使用transformNodeO方法。这个方法存在于文档的所有节点中，它接受一个参数，即包含XSLT样 式表的文档。调用transformNode ()方法会返回一个包含转换信息的字符串。來看一个例子。

//加载XML和XSLT (仅限于IE) xmldom.load("employees.xml"); xsltdom.load{"employees.xslt")；

//转换

var result = xmldom.transformNode(xsltdom};

IEXsltExampleO 1. htm

这个例子加载了一个XML的DOM文档和一个XSLT样式表的DOM文档。然后，在XML文档节 点上调用了 transformNodeO方法，并传人XSLT。变盘result中最后就会保存一个转换之后得到 的字符串。需要注意的是，由于是在文档节点级别上调川的transformNodeO,因此转换是从文档节 点开始的。实际上，XSLT转换可以在文档的任何级别上进行，只要在想要开始转换的节点上调用 transfortnNode 0方法即可。下面我们來看一个例子。

result = xmldom.documentElement.transformNode{xsltdom}; result = xmldom.documentElement.childNodes[1].transformNode(xsltdom)； result = xmldom.getElementsRyTagName("name")[0].transformNode(xsltdom)； result = xmldom.documentElement.firstChild.lastChild.transformNode(xsltdom);

如果不是在文档元素上调用transformNodeO,那么转换就会从调用节点上面开始。不过，XSLT 样式表则始终都可以针对调用节点所在的整个XML文档，而无需更换。

2.复杂的XSLT转换

虽然transformNodeO方法提供了基本的XSLT转换能力，但还有使用这种语言的更复杂的方式。

为此，必须要使用XSL模板和XSL处理器。第一步是要把XSLT样式表加载到一个线程安全的XML

文申当中。而这可以通过使用ActiveX对象MSXML2 . FreeThreadedDOMDocument来做到。这个ActiveX

对象与IE中常规的DOM支持相同的接口。此外，创建这个对象时应该尽可能使用最新的版本。例如：

function createThreadSafeDocument(){

if (typeof arguments.callee.activeXString != "string"){

var versions - [nMSXML2.FreeThreadedDOMDocument.6^0"z "MSXML2.FreeThreadedDOMDocument.3.0*,

"MSXML2.FreeThreadedDOMDocument,

i, len；

for (i=0,len=versicms.length; i < len； i++){ try {

new ActiveXObject(versions[i]); arguments.callee.activeXString = versions[i]; break;

} catch (ex){

//跳过

}

}

}

return new ActiveXObject{arguments.callee.activeXString)；

IEXsltExampleO2. htm

除了签名不同之外，线程安全的XMLDOM文梢与常规XMLDOM文档的使用仍然是一样的，如 下所示：

var xsltdom = createThreadSafeDocument()? xsltdom.async = false; xsltdom.load("employees.xslt")；

在创建并加载f自由线程的DOM文档之后，必须将它指定给一个XSL模板，这也是一个ActiveX 对象。而这个模板是用来创建XSL处理器对象的，后者则是用来转换XML文料的。同样，也需要使用 最新版本来创建这个对象，如下所示：

function createXSLTemplate(){

if (typeof arguments.callee.acLiveXString 1= "string"){

var versions = ["MSXML2.XSLTemplate.6.0*,

-MSXML2.XSLTemplate.3.C",

"MSXML2.XSLTemplate"1#

i, len；

18

 

for (i=0#len=versions.length； i < len； i++){ try {

new ActiveXObject(versions[i])；

arguments.callee.activeXString = versions[i];

break;

} catch (ex){

//跳过

}

}

return new AcLiveXObject(arguments.callee.activeXString);

}

IEXsltExampleO2. htm

使用这个createXSLTemplate ()函数可以创建这个对象最新版本的实例，用法如下：

var template = createXSLTemplate()； template.stylesheet = xsltdom；

var processor = template.createProcessor(); processor.input = xmldom； processor.transform(); var result = processor.output；

IEXsltExampleO2. htm

在创建了 XSL处理器之后，必须将要转换的节点指定给input屈性。这个值可以是一个文档，也 可以是文档中的任何节点。然后，调用tranSform()方法即可执行转换并将结果作为字符串保存在 output属性中。这些代码实现了与transformNode()相同的功能。

XSL模板对象的3.0和6.0版本存在显著的差别。在3.0版本中，必须给input 属性指定一个完整的文档；如果指定的是节点，就会导致钳谈。而在6.0版本中，则 可以为input展性指定文桂中的任何节点。

使用XSL处理器可以对转换进行更多的控制，同时也支持更髙级的XSLT特性。例如，XSLT样式 表可以接受传人的参数，并将其用作局部变量。以下面的样式表为例：

<?xml version="1.0*?>

<xsl:stylesheet version="1.0" xmlns:xsl = *http: "[www.w3.org/1999/XSL/Transform](http://www.w3.org/1999/XSL/Transform)">

<xsl：output method=•html"/>

<xsl: parajn name="message"/>

<xsl：template match="/">

<ul>

<xsl:apply-templates selects**•/>

</ul>

<p>Message: <xsl: value-of select = -$message''/></p>

</xsl:template>

<xsl:template match="employee">

<lixxsl ： value-of select = "name" />,

<emxxsl: value-of select=w@title"/></em></li>

</xsl:template>

</xsl:stylesheet>

employees.xslt

这个样式表定义了一个名为message的参数，然后将该参数输出到转换结果中。要设置message 的值，可以在调用transform{）之前使用addParameterG方法。addParameter（）方法接收两个参 数：要设置的参数名称（与在<xsl :%^1（1>的name特性中指定的一样）和要指定的值（多数情况下是 字符串，但也可以是数值或布尔值）。下面就是这样•、个例子。

processor.input = xmldom.document E1emen t; processor.addParameter（"message", "Hello World!"}; processor.transform（）;

IEXsltExampleO3. htm

通过设置参数的值，这个值就可以在输出中反映出来。

XSL处理器的另一个髙级特性，就是能够设置一种操作模式。在XSLT中，可以使用mode特性为 模板定义一种模式。在定义了模式后，如果没有将<xsl:apply-templates:^匹配的mode特性一起 使用，就不会运行该模板。下面来看一个例子。

<xsl:stylesheet version='l.0” xmlns：xsl="http：//www,w3.org/1999/XSL/Transform">

<xsl:output method="html"/>

<xsl:param name="message"/>

<xsl:template match="/">

<ul>

<xsl:apply-templates select=B*■/>

</ul>

<p>Message: <xsl：value-of select=*$message"/></p>

</xsl:template〉

<xsl：template match= * employee">

<lixxsl：value-of select:"name”/>,

<emxxsl: value-of select-■ itlew/x/emx/li> </xsl:template〉

<xsl:template match="employee" mode="title-first"> <lixemxxsl:value-of select="@title"/x/em>#

<xsl:value-of select:"name"/></li>

</xsl:template〉

</xsl:stylesheet>

employees 3.xs It

这个样式表定义了一个模板，并将其mode特性设置为“title-first-(即“先显示职位”)。在这 个模板中，首先会输出员工的职位，其次才输出员工的名字。为了使用这个模板，必须也要将 <xsl:apply-templates>元素的模式设置为"title-first"。在使用送个样式表时，默认情况下其 输出结果与前面一样，先显示员工的名字，再显示员工的职位。但是，如果在使用这个样式表时，使用 JavaScript将模式设置为"title-first"，那么结果就会先输出员工的职位。在JavaScript中使用 setStartMode {)方法设置模式的例f如下。

18

 

processor.input = xmldom；

processor .addParameter {"message", "Hello World!”； processor.setStartMode{"title-first"); processor.transform()?

!EXsltExampleO5. htm

setStartMode^方法只接受一个参数，即要为处理器设置的模式。与addParameter (>—样，设 置模式也必须在调用transform!)之前进行。

如果你打算使用同一个样式表进行多次转换，可以在每次转换之后重置处理器。调用reset (>方法 后，就会清除原先的输人和输出属性、启动模式及其他指定的参数。调用reset ()方法的例子如下：

processor.reset ();    //准备下一次转换

因为处理器已经编译了 XSLT样式表，所以与使用transformNodeU相比，这样进行重复转换的 速度会更快一些。

MSXML只支持XSLT 1.0。由于微软的战略重点转移到了 .NETFramework,因而 fMSXML的开发被停止了。我们希望在不久的将来，能够通过JavaScript访问XML和 XSLT .NET 对象

18.3.2 XSLTProcessor 类型

Mozilla通过在Firefox中创建新的类型，实现了 JavaScript对XSLT的支持。开发人员可以通过 XSLTProcessox•类型使用XSLT转换XML文格，其方式与在IE中使用XSL处理器类似。因为这个类 型是申•先出现的，所以Chrome、Safari和Opera都借鉴丫相同的变现，最终使XSLTProcessor成为了 通过JavaScript进行XSLT转换的事实标准。

与IE的实现类似，第一步也是加载两个DOM文裆，一个基于XML,另一个基于XSLT。然后， 创建•个新XSLTProcessor对象，并使用importstylesheet ()方法为其指定一个XSLT，如下面 的例子所示。

var processor = new XSLTProcessor{) processor.importStylesheet(xaltdom);

XsltProcessorExampleOl. him

最后一步就是执行转换。这■'步有两种不同的方式，如果想返问~个完整的DOM文档，可以调用 transf ormToDocument () a而通过调用transf ormToFragment ()则可以得到一个文相片段对象。一 般来说，便用transformToFragmentO的唯一理由，就是你还想把返回的结果添加到另一个DOM文 档中。

在使川transformToDocument (＞时，只要传入XML DOM,就可以将结果作为一个完全不同的 DOM文档来便用。来看下面的例子。

var result = processor.transformToDocument(xmldom)； alert{serializeXml(result));

XsltProcessorExampleO 1 .htm

而transf ormToFragment ()方法接收两个参数：要转换的XML DOM和应该拥有结果片段的文 档。换句话说，如果你想将返M的片段插人到页面中，只要将document作为第二个参数即可。下面来 看一个例子。

var fragment = processor.transformToDocument(xmldom, document)； var c.iv = document .getElementById( "divResult")； div.appendChiId(fragment)；

XsltProcessorExample02、htm

这S,处理器创建T-个由document对象拥有的片段。这样，就可以将返回的片段添加到页面中 已有的《3iV；•元索中了。

在XSLT样式表的输出格式为‘•3^1»或41；11＞1 •的情况下，创建文档或文档片段会非常有用。不过， 在输出格式为“text。时，我们通常只希望得到转换的文本结果。时惜的是，没有方法能够直接返回文 本。当输出格式为"text"时调用transformToDocument (),仍然会返回一个完整的XML文档，但 这个文档的内容在不同浏览器中却不一样。例如，Safari会返回一个完整的HTML文档，而Opera和 Firefox则会返回~个只包含一个元索的文档.这个元素巾包含着输出的文本。

使用transf ormToFragment (＞方法可以解决这个问题，这个方法返回的是只包含一个子节点的文 档片段，而子节点中包含着结果文本。然后，使用下列代码就可以取得其中的文本。

var fragment = processor.transformToFragment(xmldom, document); var text = fragment.firstchild.nodeValue； alert(text):

以上代码能够在支持的浏览器中一致地运行，而且能够恰好返问转换得到的输出文本。

1.使用参数

XSLTProcessor也支持使JH setParameterU来设置XSLT的参数，这个方法接收三个参数：命名空间

URI、参数的内部名称和要没置的值。通常，命名空间URI都是null，而内部名称就是参数的名称。另外， 必须在凋用transfonnToDocuinerJ: (｝或transformToFragrnent ()之前调用这个方法。下面来看例子。

var processor = new XSLTProcessor() processor.importStylesheet(xsltaom);

processor.setParameter(nullf "message", "Hello World!"); var result = processor.transformToDocument(xmldom)；

XsltProcessorExamp!e03 .htm

还々W个与参数有关的方法，getParameter ()和remove Parameter (),分别用于取得和移除.当 前参数的值。这两个方法都要接受命名空间参数(同样，通常是null)和参数的内部名称。例如：

var processor = new XSLTProcessor() processor.importStylesheet(xsltdom);

processor. setParameter (null, "message", -Hello World! ;

alert (processor .getParameter (null, "message")) ;    //檢出"Hello World!"

18

 

processor.removeParameter(null, *message"};

var result = processor.transformToDocument(xmldom)；

这两个方法并不常用，提供它们只是为了方便起见。

2.重置处理器

每个XSLTProcessor的实例都可以重用，以便使川不同的XSLT样式表执行不同的转换。重置处 理器时耍调用reset ()方法，这个方法会从处理器中移除所有参数和样式表。然后，你就可以再次调用 importStylesheet (),以加载不同的XSLT样式表，如下面的例子所示。

var processor = new XSLTProcessor() processor.importStylesheet(xsltdom)；

//执行转换

processor.reset();

processor.importStylesheet(xsltdom2)；

//再执行转换

在滞要基于多个样式表进行转换时，重用一个XSLTProcessor可以节省内存。

18.3.3跨浏览器使用XSLT

IE对XSLT转换的支持与XSLTProcessor的IX别实在太大，闽此要想重新实现二者所有这方面的 功能并不现实。因此，跨浏览器兼容性最好的XSLT转换技术，只能是返冋结果字符串。为此在IE中只 需在上下文节点上调用transformNodeU即可，而在其他浏览器中则雷要序列化transformTo-Document {)操作的结果。下'面这个函数町以在IE、Fircfox、Chrome、Safari和Opera中使用。

function transform(context, xslt){

if (typeof XSLTProcessor != "undefined"){

var processor = new XSLTProcessor(}; processor.importStylesheet(xslt);

var result = processor.transformToDocument(context); return (new XMLSerializer()).serializeToString(result);

} else if (typeof context.transformNode i = "undefined")    {

return context.trans formNode(xslt);

\> else {

throw new Error("No XSLT processor available.*);

}

CrossBrawserXsltExampleOl. htm

这个transform()函数接收两个参数：要执行转换的上下文节点和XSLT文档对象。首先，它检 测是否有XSLTProcessor类型的定义，如果有则使用该类型来进行转换。在调用transformTo-Document ()方法之后，将返冋的结果序列化为字符串。如果上下文节点中有trans formNode ()方法， 则调用该方法并返回结果。与本章中其他的跨浏览器函数一样，transform)也会在XSLT处理器无效 的情况下抛出错误。下面是使用这个函数的示例。

var result = cransform(xmldom, xsltdom);

使用IE的transformNodeO方法，可以确保不必使用线程安全的DOM文待进行转换。

注意，由于不同浏览器的XSLT引擎不一样，因此转换得到的结果在不同浏览器

'C(/间可能会稍有不同，也可能会差别很大。因此，不能绝对依箱在JavaScript中使用XSLT 进行转换的结果。

18.4小结

JavaScript对XML及其相关技术有相当大的支持。然而，由于缺乏规范，共同的功能却存在一些不 同的实现。D0M2级提供了创建空XML文档的API,但没有涉及解析和序列化。既然规范没有对这些 功能作出规定，浏览器提供商就各行其是，拿出了自己的实现方案。IE采取了下列方式。

□通过ActiveX对象来支持处理XML,而相同的对象也可以用来构建桌面应用程序。

□    Windows携带了 MSXML库，JavaScript能够访问这个库。

□这个库中包含对基本XML解析和序列化的支持，同时也支持XPath和XSLT等技术。

Fircfox为处理XML的解析和序列化，实现丫两个新类型，简介如下。

□    DOMParser类型比较简单，其对象可以将XML字符串解析为DOM文档。

□    XMLSerializer类型执行相反的操作，即将DOM文裆序列化为XML字符串。

由于Firefox中的类型比较简单，用户众多，1E9、Opera、Chrome和Safari都相继实现了相同的类 型。因此，这些类型也就成为了 Web开发中的事实标准。

D0M3级引人了一^t"针对XPath API的规范，该规范已经由Firefox、Safari、Chrome和Opera实现。 这些API可以让JavaScript基于DOM文档运行任何XPath査询，并且能够返冋任何数据的结果。IE以 自己的方式实现了对XPath的支持；具体来说，就是两个方法：selectSingleNodeO和 selectNodesOo虽然与DOM3级API相比还存在诸多限制，但使用这两个方法仍然能够执行基本的 XPath功能，即在DOM文档中査找节点或节点集合。

与XML相关的最后一种技术是XSLT,没有公开发布的标准针对这种技术的功能定义相应的API。 Firefox为通过JavaScript处理转换创建了 XSLTProcessor类渤；此后不久，Safari、Chrome、和Opera 也都实现了同样的类塑。IE则针对XSLT提供了自己的方案，一个是简卑的transformNodeO方法， 另一个是较为复杂的模板/处理器手段。

目前，IE、Firefox、Chrome和Opera都能够较好地支持XML。虽然IE的实现与其他浏览器相比差 异比较大，但仍然还是宥较多的公共功能可供我们实现跨浏览器的方案。