第.12章

DOM2 和 DOM3

本章内容

□    DOM2和D0M3的变化 □操作样式的DOM API

□    DOM遍历与范闱

Domi级主要定义的是html和xml文档的底层结构。dom2和dom3级则在这个结构 的基础上引入了更多的交S:能力，也支持丁更髙级的XML特性。为此，DOM2和DOM3

级分为许多模块(模块之间具有某种关联)，分别描述了 DOM的某个非常具体的子集。这些模块 如下。

□ DOM2级核心(DOM Level 2 Core):在1级核心基础上构建，为节点添加了更多方法和属性。 □ D0M2级视图(DOM Level 2 Views )：为文档定义了基于样式信息的不同视图。

□ DOM2级事件(DOM Level 2 Events )：说明了如何使用事件与DOM文档交互。

□ DOM2级样式(DOM Level 2 Style )：定义了如何以编程方式来访问和改变CSS样式信息。

□ DOM2级遍历和范围(DOM Level 2 Traversal and Range )：引人了遍历DOM文档和选择其特定

部分的新接口。

□ DOM2级HTML (DOMLevel2HTML):在1级HTML基础上构建，添加了更多属性、方法和 新接口。

本章探讨除“DOM2级事件”之外的所有模块，“DOM2级事件”模块将在第13章进行全面讲解。

DOM3级又增加了 “XPath”模块和“加载与保存” 些模块将在第18章讨论。

(Load and Save )模块。这

 

12.1 DOM 变化

DOM2级和3级的目的在于扩展DOMAPI,以满足操作XML的所有需求，同时提供更好的错误处 理及特性检测能力。从某种意义上讲，实现这一R的很大程度意味着对命名空间的支持。“DOM2级核 心”没有引人新类型，它只是在D0M1级的基础上通过增加新方法和新属性来墦强了既有类型。"DOM3 级核心”同样增强了既有类型，但也引人了一些新类型。

类似地，“DOM2级视图”和“DOM2级HTML”模块也增强了 DOM接U,提供了新的属性和方 法。由于这两个模块很小，因此我们将把它们与“D0M2级核心”放在一起，讨论基本JavaScript对象 的变化。可以通过下列代码来确定浏览器是否支持这些DOM模块。

var supportsDOM2Core = document.implementat ion.hasFeature(■Core■, ■2.0"); var supportsDOM3Core = document.implementation.hasFeature("Core", *3.0"); var support8DOM2HTML = document.implementation.hasFeature("HTML", "2.0"); var supportsDOM2Views = document.implementation.hasFeature("Views"/ "2.0"); var supportsDOK2XML = document .implementation. hasFeature ("XML", **2.0"};

(^\z章只讨论那些已经有浏览器实现的部分，任何浏览器都没有实现的部分将不作

Y讨论。

12.1.1针对XML命名空间的变化

有了 XML命名空问，不同XML文档的元素就可以混合在一起，共同构成格式良好的文档，而小' 必担心发生命名冲突。从技术上说，HTML不支持XML命名空间，但XHTML支持XML命名空间。 因此，本节给出的都是XHTML的示例。

命名空间要使用xmlns特性来指定。XHTML的命名空间是http://www.w3.org/1999/xhtml,在任何 格式良好XHTML页面中，都应该将其包含在元素中，如下而的例子所求。

<html xmlns="http：//www.w3.org/1999/xhtmi">

<head>

<title>Example XHTML page</title>

</head>

<body>

Hello world I </'body>

</html>

对这个例子而言，其中的所有元素默认都被视为XHTML命名空间中的元索。要想明确地为XML 命名空间创建前缀，可以使用xmlns后跟冒号，再后跟前缀，如下所示。

<xhtml:html xmlns:xhtrol = ■http:/Zwww.w3.org/1999/xhtml">

<xhtml:head>

<xhtml：title>Example XHTML page</xhtml:title>

</xhtml：head>

<xhtml:body>

Hello world J </xhtml:body>

</xhtml：html>

这里为XHTML的命名空间定义了一个名为xhtml的前缀，并要求所有XHTML元索都以该前缀

开头。有时候为了避免不同语言间的冲突，也需要使用命名空间来限定特性，如下面的例子所示。

<xhtml:html xmlns：xhtml="http：//www.w3.org/1999/xhtml•>

<xhtml:head>

<xhtml:title>Example XHTML page</xhtml:title>

</xhtml:head>

<xhtiol：body xhtml x alassa"homeN >

Hello world J </xhtml:body>

</xhtml:html>

这个例子中的特性class带有一个xhtml前缀。在只基于一种语言编写XML文档的情况下，命 名空间实际上也没有什么用。不过，在混合使用两种语言的情况下，命名空间的用处就非常大了。来看

-看下面这个混合了 XHTML和SVG语言的文档：

<html xmlns="http：//www.w3.org/1999/xhtml">

<head>

<title>Example XHTML page</title>

</head>

<body>

<Bvg xmlnB=!Mhttpj //[www.w3](http://www.w3) .org/2000/svg" version-Ml.l"

viewBox="0 0 100 100" style*"width:100%; height:100%">

<rect x="0M y="0" width="100" height="100w styles"fill:red"/>

</avg>

</body>

</html>

在这个例子中，通过设置命名空间，将<^9>标识为了与包含文档无关的元索。此时，<Svg>元素的 所有子元素，以及这些元素的所有特性，都被认为属于http://www.w3.org/2000/svg命名空间。即 使这个文档从技术上说是一个XHTML文档，但因为有了命名空间，其中的SVG代m仍然是有效的。

对于类似这样的文档来说，最有意思的事发生在调用方法操作文档节点的情况下。例如，在创建一 个元素时，这个元素属于哪个命名空间呢？在查询一个特殊禄签名时，应该将結果包含在哪个命名空间 中呢？ “DOM2级核心”通过为大多数DOM1级方法提供特定于命名空间的版本解决了这个问题。

1-Node类型的变化

在DOM2级中，Node类型包含下列特定于命名空间的属性。

□    localName：不带命名空间前缀的节点名称。

□    namespaceURI:命名空间URI或者（在未指定的情况T■是）null。

□    prefix：命名空间前缀或者（在未指定的情况下是）mill。

当节点使用了命名空间前缀时，其nodeName等于prefix+localName。以下面的文构为例：

<html xralns="http：//www.w3.org/1999/xhtml*>

<head>

<title>Example XHTML page</title>

</head>

<body>

<s：Bvg xznlns：s=N<http://www.w3.orgZ2000/svgN> vorflion=Ml.l"

viowBox="0 0 100 100" stylo*"widthj100%; height:100%">

<s:rect x=w0" y«"0" width-*100" heiffht="100" style="fill:red"/>

</s:svg>

</body>

</html>

NamespaceExample.xml

对于＜html＞兀素来说，它的 localName 和 tagName 是"html"，namespaceURI 是"http://www. w3.org/1999/xhtml",而 prefix 是 null。对于＜s:svg＞元素而言，它的 localName 是"svg"， tagName 是"s : svgh, namespaceURI 是"http: / Z[www.w3](http://www.w3) . org/2000/svg",而 prefix 是1• stt o

D0M3级在此基础上更进一步，又引入了下列与命名空间有关的方法。

□ isDefaultNamespace (namespaceURI):在指定的 namespaceC7/?J 是当前节点的默认命名空 间的情况下返回true。

□    lookupNamespaceURI (prefix):返回给定 prefix 的命名空间。

□    lookupPre fix (namespaceURI):返问给定 namespaceURI 的前缀。

针对前面的例子，可以执行下列代码：

alert(document-body.isDefaultNamespace("[http://www.v/3.org/1999/xhtml")](http://www.v/3.org/1999/xhtml%22)%ef%bc%9b)[；](http://www.v/3.org/1999/xhtml%22)%ef%bc%9b) //true

/ /假设svg中包合着对＜3: svg＞的？I用

alert{svg.lookupPrefix{"[http://www.w3.org/2000/svg"))](http://www.w3.org/2000/svg%22))%ef%bc%9b)[；](http://www.w3.org/2000/svg%22))%ef%bc%9b) /" s"

alert(svg.lookupNamespaceURI("s")); //"<http://www.w3.org/2000/svg>"

在取得了一个节点，但不知道该节点与文档其他元素之间关系的情况下，这些方法是很有用的=

\2.    Document类型的变化

DOM2级中的Document类型也发生了变化，包含了下列与如名空间有关的方法。

□    createElementNS (namespace(7}?I? tagName):使用给定的 tagWa/ne 创建一个属于命名空 间namespaceURI的新元素。

□    createAttributeNS (namespaceURIf attribuLeName):使川给定的 attributeName 建一个属于命名空间namespaceURI的新特性。

□    getElementsByTagNameNS (namespaceURI/ tagName}:返问属于命名空间 的 tagName 元素的 NodeList。

使用这些方法时需要传人表示命名空间的URI (而不是命名空间前缀)，如下面的例子所示。

//创建一个新的SVG元索

var svg = document.createElementNS("[http://www.w3.org/2000/svg"z"svg](http://www.w3.org/2000/svg%22z%22svg)");

/ /创建一个属于某个命名空间的新特性

var att = document .createAttributeNS ("http://www. somewhere.com1', "random");

//取得所有XHTML元素

var elems = document .getElementsByTagNameNS ("http： //[www.w3](http://www.w3) .org/1999/xhcml,' # " *")；

只有在文档中存在两个或多个命名空间时，这些与命名空间有关的方法才是必需的。

\3.    Element类型的变化

“DOM2级核心”中有关Element的变化，主要涉及操作特性。新增的方法如下。

□    getAttributeNS (namespaceURI, localName):取得属于命名空间 namespaceURI 且名为 localName 的特性。

□    getAttributeNodeNS (namespaceURI, localName):取得属于命名空间 namespaceURI 名为localName的特性节点。

□    getElementsByTagNameNS (namespaceURI, tagName):返回属于命名空间 name印dcetZHI 的 tagName 兀素的 NodeList。

□    hashttributeVlS (namespaceURI, localName):确定当前元素是否有一个名为 localNajne 的特性，而且该特性的命名空间是namespaces Jo注意，“DOM2级核心”也增加了一个 hasAttributeO方法，用于不考虑命名空间的情况。

□    removeAttriubteNS (namespaceURI, localName):删除属于命名空间 namespacelTRI 且名 为2oca2_Wawie的特性。

□    set AC t r ibuteNS (namespaceCZRJ, qualifiedNaine, value):设置 W 于命名空间 namespace-URI且名为qualifiedNAme的特性的值为valueo

□    setAttributeNodeNS(attNode):设置属于命名空间 namespaceURI 的特性节点。

除了第一个参数之外，这些方法与DOM1级中相关方法的作用相同；第一个参数始终都是一个命 名空间URI。

4. NamedNodeMap类型的变化

NamedNodeMap类型也新增了下列与命名空间有关的方法。由于特性是通过NamedNodeMap表示 的，㈥此这些方法多数情况下只针对特性使用。

□    getNamedltemNS (nainespaceURI, localName):取得属于命名空间 namespaceURI 且名为 local Name 的项 o

□    r emoveNamedl t emNS (name spa ce UR I, localName):移除属于命名空间 namespa ceURI 且名 为 localName 的项。

□    setNamedltemNS (node):添加node,这个节点已经事先指定了命名空间信息。

由于一般都是通过元素访问特性，所以这些方法很少使用。

12.1.2其他方面的变化

DOM的其他部分在“DOM2级核心”中也发生了一些变化。这些变化与XML命名空间无关，而是 更倾向于确保API的可靠性及完整性。

\1.    Document Type类型的变化

DocuraenCType 类型新增了 3 个属性：publicld、systemld 和 internalSubset。其中，前两 个属性表示的是文档类逛声明中的两个信息段，这两个信息段在DOM1级中是没有办法访问到的。以 下面的HTML文档类型声明为例。

<:DOCTYPE HTML PUBLIC "-Z/W3C//DTD HTML 4.01//EN"

"http://www.w3，org/TR/html4/strict.dtd*>

对这个文档类型声明而言，publicld是//W3C//DTD HTML 4.01//EN■，而 systemld是 //[www.w3.org/TR/html4/strict.dtd-](http://www.w3.org/TR/html4/strict.dtd-%e3%80%82%e5%9c%a8%e6%94%af%e6%8c%81DOM2%e7%ba%a7%e7%9a%84%e6%b5%8f%e8%a7%88%e6%92%b0%e4%b8%ad%ef%bc%8c%e6%88%90%e8%af%a5%e5%8f%af%e4%bb%a5%e8%bf%90%e8%a1%8c%e4%b8%8b%e5%88%97%e4%bb%a3%e7%a0%81%e3%80%82)[。在支持DOM2级的浏览撰中，成该可以运行下列代码。](http://www.w3.org/TR/html4/strict.dtd-%e3%80%82%e5%9c%a8%e6%94%af%e6%8c%81DOM2%e7%ba%a7%e7%9a%84%e6%b5%8f%e8%a7%88%e6%92%b0%e4%b8%ad%ef%bc%8c%e6%88%90%e8%af%a5%e5%8f%af%e4%bb%a5%e8%bf%90%e8%a1%8c%e4%b8%8b%e5%88%97%e4%bb%a3%e7%a0%81%e3%80%82)

alert(document.doctype.publicld)； alert(document.doctype.systemld)；

实际上，很少需要在网页中访问此类倍息。

最后一个属性internalSubset,用于访问包含在文档类型声明中的额外定义，以下面的代码为例。

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"

"<http://www.w3.org/TR/xhtmll/DTD/xhtmll-strict.dtd>"

(<!ELEMENT name (#PCDATA)>] >

访问 document.doctype. internalSubset 将得到"<! ELEMENT name (#PCDATA) >"o 这种内部 子集(internal subset)在HTML中极少用到，在XML中可能会更常见一些。

\2.    Document类型的变化

Document类型的变化中唯一与命名空间无关的方法是importNode () o这个方法的用途是从一个 文档中取得一个节点，然后将其导人到另一个文档，使其成为这个文档结构的-部分。需要注意的是， 每个节点都冇-个ownerDocument属性，表示所属的文档。如果调用aE>pendChild()时传人的节点 域于不同的文档(ownerDocument属性的值不一样)，则会导致错误。但在调用importNode ()时传人 不同文捫的节点则会返回一个新节点，这个新节点的所有权归当前文档所有。

说起来，importNode (>方法与Element的cloneNode(>方法非常相似，它接受两个参数：要复

制的节点和一个表示是否复制子节点的布尔值。返回的结果是原来节点的副木，但能够在当前文档中使 用。来看下面的例子：

var newNode = document. importNode (oldNodG, true) ? //导入节点及其所有子节点 document.body.appendChild(newNode)；

这个方法在HTML文档中并不常用，在XML文档中用得比较多(更多讨论请参见第18章)。

“DOM2级视图”模块添加了一个名为defaultView的属性，其中保存着一个指针，指向拥有给 定文档的窗口(或框架)。除此之外，“视图”规范没有提供什么时候其他视图可用的信息，因而这是唯 一一个新增的屈性。除IE之外的所有浏览器都支持defaultView属性。在IE中有一个等价的厲性名 叫parentWindovUOpera也支持这个属性)。因此，要确定文档的归属窗口，可以使用以下代码，

var parentWindow = document.defaultView I| document.parentWindow；

除了上述一方法和一个属性之外，“DOM2级核心”还为document. implementation对象规定了 两个新方法：createDocumentType ()和 createDocument {) o 前者用于创建一个新的 DocumentType 节点，接受3个参数：文档类型名称、publicld、sysfcemldo例如，下列代码会创建斗新的HTML 4.01 Strict文档类型c

var doctype = document.implementation.createDocumentType("html",

"-//W3C//DTD HTML 4.01//EN*1,

"http: //[www.w3](http://www.w3) . org/TR/htinl4/strict .dtd11);

由于既有文档的文杜类瑚不能改变，因此createDocumentType ()只在创逮新文档时布用；创建 新文钓时需要用到creatcDocumcnt ()方法。这个方法接受3个参数：针对文档中元素的namesp， aceURI ,文档元素的标签名、新文档的文档类型。下面这行代码将会创建一个空的新XML文梢。

var doc = document.implementation.createDocument(■", "root", null);

这行代码会创建一个没有命名空间的新文档，文档元素为＜rOOt＞,而且没有指定文档类型。要想 创建一个XHTML文档，可以使用以下代码。

var doctype = document•implementation.createDocumentType("html",

•| -//W3C//DTD XHTML 1.0 Strict//ENB,

"http: / /www. w3 .org/TR/xhtmll/DTD/xh.tml 1-strict .dtd");

var doc = document.implementation.createDocument("http：//www.w3.org/1999/xhtml", "html", doctype);

这样，就创建了一个带有适当命名空间和文档类型的新XHTML文挡。不过，新文档当前只有文档 元素剩下的所有元素都需要继续添加。

“DOM2 级 HTML"模块也为 document. implementation 新增 了一个方法，名叫 createHTML-Document ()。这个方法的用途是创建' •个完整的HTML文档，包括＜html＞、＜head＞、＜title;^U ＜body＞元素。这个方法只接受一个参数，即新创建文档的标题(放在元素中的字符串)，返回 新的HTML文档，如下所示：

var htmldoc = document.implementation.createHTKLDociwent("New Doc")； alert(htmldoc.title)；    //"New Doc，

alert(typeof htmldoc.body);    //"object"

CreateHTMLDocumentExample. htm

通过调用createHTMLDocximent ()创建的这个文，是HTMLDocument类型的实例，因而具有该 类型的所有属性和方法，包括title和body属性。只有Opera和Safari支持这个方法。

\3. Node类型的变化

Node类型中唯一与命名空间无关的变化，就是添加了 isSupported(}方法。与DOM1级为document. implementation引人的hasFeature ()方法类似，isSupported()方法用于确定当前节点具有 什么能力。这个方法也接受相同的两个参数：特性名和特性版本号。如果浏览器实现了相应特性，而且 能够基于给定节点执行该特性，isSupported()就返回true。来看一个例子：

if (document.body.isSupported("HTML", "2.0*)}{

//执行只有-DOM2级HTML•才支持的操作

)

由于不同实现在决定对什么特性返回true或false时并不一致，这个方法同样也存在与hasFeature {) 方法相同的问题。为此，我们建议在确定某个特性是否可用时，最好还是使用能力检测。

DOM3级引人了两个辅助比较节点的方法：isSameNode{)和isEqualNodeU。这两个方法都接受 一个节点参数，并在传人节点与引用的节点相同或相等时返冋true。所谓相同，指的是两个节点引用的 是同一个对象。所谓相等，指的是两个节点是相同的类型，具有相等的属性(nodeName、nodeValue, 等等)，而且它们的attributes和childNodes属性也相等(相同位S包含相同的值)。来看一个例子。

var divl = document-createElement("div"); divl.setAttribute("class", "box");

var di.v2 = document. createElement ("div"): div2.setAttribute("class", "box*)；

alert(divl.isSameNode(divl))； //true alert(divl.isEquaINode(div2))； //true alert(divl.isSameNode(div2)); //false

这里创建了两个具有相同特性的＜div＞元素。这两个元素相等，但不相同。

DOM3级还针对为DOM节点添加额外数据引人了新方法。其中，setUserDacaO方法会将数据指

定给节点，它接受3个参数：要设置的键、实际的数据(可以是任何数据类型)和处理函数。以下代码 可以将数据指定给一个节点。

document.body.setUserData{"name", ”Nicholas", function(》{} };

然后，使用getUserDataU并传人相同的键，就可以取得该数据，如下所示：

var value = document.body.getUserData("naxe");

传人setUserData()中的处理函数会在带有数据的节点被复制、删除、重命名或引人一个文档时 调用，因而你可以事先决定在上述操作发生时如何处理用户数据。处理函数接受5个参数：表示操作类 型的数值(1表示复制，2表示导人，3表示删除，4表示重命名)、数据键、数据值、源节点和目标节 点。在删除节点时，源节点是null;而在复制节点时，口标节点是null。在函数内部，你可以决定如 何存储数据。来看下面的例子。

var div = document.createElement("div");

div .setUserData ("name**, "Nicholas", function (operation, key, value, src, dest) { if (operation == 1){

dest.setUserData(key, value, function(J{});    )

})；

var newDiv = div.cloneNode(true);

alert{newDiv.getUserData("name"))；    //"Nicholas

UserDataExample.htm

这里，先创建了 jh＜div＞元素，然后又为它添加了一些数据(用户数据)。在使用cloneNodeO 复制这个元素时，就会调用处理函数，从而将数据ft动复制到了副本节点。结果在通过副本节点调用 getUserData ()时，就会返回与原始节点中包含的相同的值c

4.框架的变化

梢架和内嵌框架分别用HTMLFrameEleraent和HTMLIFrameElement表不,它们在DOM2级中都有 了一个新属性，名叫contentDocument。这个属性包含•-个指针，指向表zf;框粱内容的文档对象。在此 之前，无法6:接通过元素取得这个文档对象(只能使用frames集合)。可以像下面这样使用这个属性。

、 var i frame =： document .get ElementBy Id ("my I frame")；

f var ifraineDoc = if rame. content Document;    //在 IE8 以前的版本中无效

IFrameElementExample. htm

由于content Document属性是Document类型的实例，因此可以像使用其他HTML文档一样使 用它，包括所有厲性和方法。Opera、Hrefox、Safari和Chrome支持这个属性。IE8之前不支持框架中 的contencDocument属性，但支持一个名叫contentWinaow的賊性，该属性返M框架的window对 象，而这个window对象又有一个document属性。因此，要想在上述所有浏览器中访问内嵌框架的文 档对象，可以使用下列代码。

var iframe = document.getElementById("mylframe");

var iframeDoc = iframe.contentDocument II iframe.contentWindow.document;

lFrameElementExample2. htm

所有浏览器都支持contentWindow属性。

访问框架或内嵌框架的文档对象要受到跨域安全策略的限制。如果某个框架中的 页面来自其他域或不同子域，或者使用了不同的协议，那么要访问这个框架的文档对 象就会导致错误。

12.2样式

在HTML中定义样式的方式有3种：通过＜link/:＞元素包含外部样式表文件、使用＜style/：＞元素 定义嵌人式样式，以及使用style特性定义针对特定元素的样式。“DOM2级样式"模块围绕这3种应用 样式的机制提供了•一套API。要确定浏览器是杏支持DOM2级定义的CSS能力，可以使用下列代码。

var support sD0M2 CSS = document. implementation, has Feature (HCSS", "2.0”； var supportsDOM2CSS2 = document.implementation.hasFeature("CSS2",    "2.0*);

12.2.1访问元素的样式

任何支持style特性的HTML元素在JavaScript屮都有'一^对应的style属性。这个style对象 是CSSStyleDeclaration的实例，包含着通过HTML的style特性指定的所有样式位息，但不包含 与外部样式表或嵌人样式表经层叠而来的样式。在style特性中指定的任何CSS属性都将表现为这个 style对象的相应属性。对于使用短划线(分隔不同的词汇，例如background-image )的CSS属性 名，必须将艿转换成驼峰大小写形式，才能通过JavaScript来访问。下表列出了几个常见的CSS属性及 其在style对象中对应的属性名。

| cssn性           | JavaScript 厲性       |
| ---------------- | --------------------- |
| background-image | style.backgroundImage |
| color            | style.color           |
| display          | style, display        |
| font-family      | style. font Family    |

多数情况下，都可以通过简单地转换属性名的格式来实现转换。其中一个不能直接转换的CSS属性 就是float。由于float是JavaScript中的保留字，因此不能用作属性名。“D0M2级样式”规范规定 样式对象上相应的属性名应该是cssFloat; Firefox、Safari、Opera和Chrome都支持这个属性，而IE 支持的则是styleFloato

只要取得一个有效的DOM元素的引用，就可以随时使用JavaScript为其设置样式。以下是几个例子。 var myDiv = document •getElementById( •myDiv") ,•

//设置背素顏色

myDiv.style.backgroundedor = "red";

//改变大小

myDiv. style .width = " lOOpx"； myDiv.style.height = ■200px*;

//指定边框

myDiv.style.border = "lpx solid black";

在以这种方式改变样式时，元素的外观会自动被更新。

 

在标准模式下，所有度量值都必须指定一个度量单位。在混杂模式下，可以将 style .width设置为"20*,浏览器会假设它是"20px-;但在标准模式下，将

style.width设置为"20•会导致被忽略-因为没有度量单位。在实践中，最好始

终都指定度量单位。

通过style对象同样可以取得在style特性中指定的样式。以下面的HTML代码为例。 <div id="myDiv" style="background-color:blue； width：lOpx； height:25px"></div> 在style特性中指定的样式信息可以通过下列代码取得。

alert(myDiv.style.backgroundColor)；    //"blue"

alert(myDiv.style.width)；    //■10px-

alert(myDiv.style-height);    //"25px"

如果没有为元素设肾style特性，那么style对象屮可能会包含一独默认的值，但这残值并不能 准确地反映该元素的样式信息。

1.DOM样式属性和方法

“DOM2级样式"规范还为style对象定义了一些属性和方法。这些属性和方法在提供元素的style 特性值的同时，也可以修改样式。下面列出了这些属性和方法。

□    cssText:如前所述，通过它能够访问到style特性中的CSS代码。

□    length：应用给元素的CSS属性的数量。

□    parentRule:表示CSS信息的CSSRule对象d本节后面将讨论CSSRule类型。

□    getPropertyCSSValue (propertyName):返冋包含给定属性值的 CSSValue 对象。

□    get Property Priority (propercyName):如果给定的属性使用了 ! important 设则返回 "important";否则，返回空字符串。

□    getPropertyValue (propertyWame):返M给定属性的字符串值。

□    item (index):返回给定位置的CSS属性的名称。

□    remove Property [propertyName):从样式中删除给定属性。

U setProperty (propertyName, value,priority):将给定属性设置为相应的值，并加上优先 权栋志("important•或者一个空字符串)。

通过cssText属性可以访问style特性中的CSS代码。在读取模式下,cssText返回浏览器对style 特性中CSS代码的内部表示。在写入模式下，赋给cssText的值会重写整个style特性的值；也就是 说，以前通过style特性指定的样式倍息都将丢失。例如，如果通过style特性为元素设置了边框， 然后再以不包含边框的规则重写cssText,那么就会抹去元素上的边框。下面是使用cssText属性的 一个例子。

myDiv.style.cssText = "width: 25px； height: lOOpx； background-color: green"； alert(myDiv.style.cssText)；

设置cssText是为元素应用多项变化最快捷的方式，因为可以一次性地应用所有变化。

设计length属件的R的，就是将其与iteiM )方法配套使用，以便迭代在元素中定义的CSS属性。 在使用length和时，style对象实际上就相当于一个集合，都可以使用方括号语法来代替 item()来取得给定位B的CSS属性，如下面的例子所示。

for (var i=0, len=myDiv.style.length； i < len； i++){ alert (myDiv. style [iI); //或青 myDiv. style. item(i)

}

无论是使用方括号语法还是使用item (}方法，都可以取得CSS属性名("background-color", 然后，就可以在getPropertyValue ()中使用了取得的屈性名进一步取

得属性的值，如下所示。

var prop, value, i, len;

for {i=0, len=myDiv.style.length; i < len; i++)(

prop 面 myDiv. otyle [i] /    "或:# myDiv. style. item(i)

value ■町Div.style.getPropertyValue(prop); alert(prop + " ： ■+ value)；

}

getPropertyValue ()方法取得的始终都是CSS «性值的字符串表禾。如果你需要更多信息，可 以使用getPropertyCSSValue ()方法，它返回一个包含两个属性的CSSValue对象，这两个属性分

别是：cssText和cssValueType。其中，cssText属性的值与getPropertyValue()返网的值相同, 而cssValueType属性则是---个数值常量，表示值的类型：0表示继承的值，1表示基本的值，2表示 值列表，3表示自定义的值。以下代码既输出CSS属性值，也输出值的类型。

var prop, value, i, len;

for (i=0z len=myDiv.style.length; i < len； i++){

prop = myDiv. style [i]; //或老 myDiv. style .item (i)

value = wyDiv.style.getPropertyCSSValue(prop)/

alert(prop + " : ■ + value.cssText + ■ (" + value.cosValueType + ")’)；

)

DOMStyleObjectExample. htm

在实际开发中，getPropertyCSSValue<）使用得比 getPropertyValue （）少得多。IE9+、Safarie 3+以及Chrome支持这个方法。Firefox 7及之前版本也提供这个访问，但调用总返回nul 1。

要从元素的样式中移除某个CSS属性，需要使用removePropertyO方法。使用这个方法移除一 个属性，意味着将会为该属性应用默认的样式（从其他样式表经层叠而来）。例如，要移除通过style 特性设置的border属性，可以使用下面的代碑。

myDiv.style.removeProperty（"border"）；

在不确定某个给定的css属性拥有什么默认值的情况下，就可以使用这个方法。只要移除相应的属 性，就可以为元素应用默认值。

除非另有说明，本节讨论的属性和方法都得到了 IE9+, Firefox、Safari、Opera9+ 以及Chrome的支持。

2.计算的样式

虽然style对象能够提供支持style特性的任何元素的样式信息，但它不包含那些从其他样式表 层叠而来并影响到当前元素的样式信息。“DOM2级样式”增强了 document.defaultView,提供了 getComputedStylef）方法。这个方法接受两个参数：要取得计算样式的元素和一个伪元素字符串（例 如"：after-）。如果不需要伪元素信息，第二个参数可以是null。getComputedStyle （>方法返回一 个CSSStyleDeclaration对象（与style属性的类型相同），其巾包含当前元素的所有计算的样式。 以下面这个HTML页面为例。

<!DOCTYPE html>

<html>

<head>

<title>Computed Styles Example<Ztitle>

<style type=”text/css">

\#myDiv {

background color: blue； width： lOOpx； height: 200px；

}

</style>

</head>

<body>

<div id="myDiv" style=’background-color: red； border: lpx solid black*></div>

</body>

ComputedStylesExample. htm

316 第 12 章 D0M2 和 D0M3

应用給这个例子中＜div＞元素的样式一方面来自嵌人式样式表(＜style＞兀素中的样式)，另一方 面來自其style特性。但是，style特性中设S了 backgroundColor和border，没有设凭width 和height,后者是通过样式表规则应用的。以下代码可以取得这个元素计算后的样式。

var myDiv = document .getsElementBy 工d (* my Di v");

var computedStyLe = document.defaultView.getComputedStyle(myDiv, null);

alert(computedStyle.backgroundColor)；    //    "red”

alert(computedStyle.width)；    //    "100pxrt

alert(computedStyie.height);    //    "200px"

alert (computedStyle.border) ;    //    在某些浏 JL器中是ulpx. solid black"

ComputedStylesExample. htm

在这个元索H嘴后的样式中，背景颜色的值是-red-,宽度值是■lOOpx，，髙度值是QOOpx%我 们注意到，背景颜色不是-blue-,因为这个样式在自身的style特性中已经被覆盖了。边框属性可能 会也可能不会返回样式表中实际的border规则（Opera会返回，但其他浏览器不会）。存在这个差别的 原因是不同浏览器解释综合（rollup ）属性（如border）的方式不同，因为设置这种属性实际上会涉及 很多其他属性。在设置border时，实际上是设置了四个边的边框宽度、颜色、样式属性 （border-left - width、border-top-color、border-bottom-style ,等等）o 因此，.即使 computedS- tyle.border 不会在所有浏览器巾都返回值，但 computedStyie.borderLeftwidth 则会返回值。

需要注意的是，即使有些浏览器支持这种功能，但表示值的方式可能会有所区别C '例如，Fkefox和Safari会将所有顏色转换成RGB格式（例如红色是rgb<255,0，0} ）c

因此，在使用getComputedStyle （）方法时，嚴好多在几种浏览器中測试一下6

IE不支持getComputedStyle （）方法，但它有一种类似的概念。在IE中，每个具冇style属性 的元素还有一个currentStyle属性。这个属性是CSSStyleDeclaration的实例，包含当前元索全 部计算后的样式。取得这些样式的方式也差不多，如下面的例子所示。

var myDiv = document.getEiementByld("myDiv");

var computedStyie » myDiv.currentStyle;

alert(computedStyie.backgroundColor);    //"red"

alert(computedStyie.width)；    //"100pxM

alert(computedStyie.height);    //"200px"

alert(computedStyie.border);    //undefined

IEComputedStyles Example, htm

与DOM版本的方式一样，IE也没有返回border样式，因为这是一个综合属性。

无论在哪个浏览器中，呆ffi要的一条是要记住所有计算的样式都是只读的；不能修改计算后样式对

象中的CSS属性。此外.计算后的样式也包含属于浏览器内部样式表的样式信息，因此任何具衣默认值 的CSS属性都会表现在计算后的样式中。例如，所有浏览器中的visibility属性都冇-个默认值， 但这个值会因实现而异。在默认情况下，有的浏览器将visibility属性设置为-visible'而有的 浏览器则将其设置为-inherit%换句话说，不能指望某个CSS域性的默认值在不同浏览器中是相同 的。如果你需要元素具有某个特定的默认值，应该手工在样式表中指定该值。12.2.2操作样式表

CSSStyleSheet类型表示的是样式表，包括通过＜link＞元索包含的样式表和中定义 的样式表。有读者可能记得，这两个元素本身分别是由HTMLLi'nkElement和HTMbStyleElement类型 表示的。但是，CSSStyleSheet类塑相对更加通用-些，它只表示样式表，而不管这些样式表在HTML 中是如何定义的。此外，上述两个针对元素的类型允许修改》TML特性，但CSSStyleSheet对象则是一 套只读的接口(有一个属性例外)。使用下面的代码可以确定浏览器是否支持DOM2级样式表。

var supportSD0M2Stylesheets =

document.implementation.hasFeature("Stylesheets"x "2.0")?

CSSStyleSheet继承自Stylesheet,后者可以作为一个基础接门来定义非CSS样式表。从 Stylesheet接口继承而来的属性如下。

□    disabled:丧示样式表是否被禁用的布尔值。这个属性是可读的，将这个值设置为true可 以禁用样式表。

□    href：如果样式表挂通过＜link＞;fe含的，则是样式表的URL;否则，是null。

□    media：当前样式表支持的所有媒体类型的集合。与所有DOM集合一样，这个集合也有一个 length®性和一个item()方法。也可以使用方括号语法取得集合中特定的项。如果集合是空 列表，表示样式表适用于所有媒体。在IE中，media是■—个反映＜link;^＜style＞元素media 特性值的字符串。

□    ownerNode：指向拥有当前样式表的节点的指针，样式表可能是在HTML中通过＜1^)＜＞或 ＜styie/＞引人的(在XML中可能是通过处理指令引人的)。如果当前样式表是其他样式表通过 @import?导人的，则这个属性值为null。IE不支持这个屈性。

U parentStyleSheet:在当前样式表是通过@import导人的情况下，这个属性是一个指向导人 它的样式表的指针=

□    title： ownerNode 中 title W性的俄。

□    type:表示样式表类型的字符串。对CSS样式表而言，这个字符奉是"type/css"。

除了 disabled属性之外，其他属性都是只读的。在支持以上所有这些属性的基础上， CSSStyleSheet类型还支持下列属性和方法：

□    cssRules：样式表中包含的样;iS；规则的粱合。IE不支持这个诚性，但有一个类似的rules属性。

□    ownerRule：如果样式表是通过@import导人的，这个属性就是一个指针，指向表示导人的规 则；否则，值为null。IE不支持这个属性。

□    deleteRule( index):删除cssRules集合中指定位置的规则。IE不支持这个方法，但支持 一"l'类似的 removeRule ()方法。

□    insertRule (ru_io, index):向cssRules集合中指定的位置插人字符申。IE不支持这 个方法，但支持一个类似的addRule ()方法。

应用于文档的所有样式表是通过document, stylesheets集合来表示的。通过这个集合的length 厲性可以获知文杓屮样式表的数ft,而通过方括号语法或item ()方法可以访问每一个样式表。來看一个 例子。

var sheet = null;

for (var i=0, len=document.sty 1 eSheets..length; i ＜ len； i++) {

sheet = document.stylesheets[i]; alert(sheet.href);

StyleSheetsExample. htm

以上代码可以输出文档中使用的每一个样式表的href属性（＜31^1^＞元素包含的样式表没有 href属性）。

不同浏览器的document. stylesheets返回的样式表也不同。所有浏览器都会包含＜style＞元索 和rel特性被设置为-stylesheet11的＜link〉元素引入的样式表。1E和Opera也包含rel特性被设置为 "alternate stylesheet"的＜link＞元索引人的样式表。

也可以直接通过＜11111＜＞或＜31¥16＞元素取得CSSStyleSheet对象。DOM规定了一个包含 CSSStyleSheet对象的属性，名叫sheet;除了 IE,其他浏览器都支持这个属性。IE支持的是 stylesheet属性。要想在不同浏览器中都能取得样式表对象，可以使用下列代码。

function getStyleSneet（element）（

return element.sheet II element.stylesheet;

}

//取锊苐一个＜link/＞元索41入的样式表

var link = document.getElementsByTagName("link")[0]; var sheet = getStylesheet{link)?

StyleSheetsExample2. htm

这里的getStylesheet （＞返的样式衷对象与document. styleSheets集合中的样式表对象相同。

1.CSS规则

CSSRule对象表示样式表中的毎一条规则。实际上，CSSRule是一个供其他多种类型继承的基类 型，其屮披常见的就是CSSStyleRule类型，表示样式信息（其他规则还有@import、@font-face、 0page但这狴规则很少有必要通过脚本来访问）。CSSStyleRule对象包含下列属性。

□    cssText:返回整条规则对应的文本。由于浏览器对样式表的内部处理方式不同，返回的文本 可能会与样式表中实际的文本不一样；Safari始终都会将文本转换成全部小写。1E不支持这个 属性。

□    parentRule：如果当前规则是导人的规则，这个属性引用的就是导人规则；否则，这个值为 null。IE不支持这个属性。

□    parentStyleSheet:当前规则所属的样式表。正不支持这个属性。

□    solcctorText：返回当前规则的选择符文本。由于浏览器对样式表的内部处理方式不同，返回 的文本呵能会与样式表中实际的文本不-样（例如，Safari 3之前的版本始终会将文本转换成全 部小写）。在Firefox、Safari、Chrome和IE中这个厲性是只读的。Opera允许修改selectorText。

□    style： 一个CSSStyleDeclaration对象，可以通过它设置和取得规则中特定的样式值。

□    type：表示规则类瑠的常量值。对于样式规则，这个值是1。IE不支持这个属性。

其中二个最常用的屈性是 cssText、selectorText 和 style。cssText 厲性与 style.cssText 厲性类似，也并不相同。前者包含选择符文本和围绕样式信息的花括号，后者只包含样式信息（类似于 元素的style.cssText ）。此外，cssText是只读的，而style.cssText也可以被重写。

大多数情况下，仅使用style属性就可以满足所有操作样式规则的需求了。这个对象就像每个元 素上的style属性-样，可以通过它读取和修改规则中的样式信息。以下面的CSS规则为例。

div.box {

background-color： blue； width: lOOpx; height: 200px；

}

CSSRulesExample. htm

假设这条规则位于页面中的第一个样式表中，而且这个样式表中只有这一条样式规则，那么通过下 列代码可以取得这条规则的各种信息。

var sheet = document.stylesheets(0]； var rules = sheet.cssRules I I sheet.rules? var rule = rules[0];

//取铒规则列表 "取锊第一条规则

//"div.box"

//完整的CSS代码 //"blue" //"100px" //■200px"

 

alert(rule.selectorText);

alert(rule.style.cssText);

alert(rule.style.backgroundColor)；

alert(rule.style.width)；

alert(rule.style.height);

CSSRulesExample. htm

使用这种方式，可以像确定元素的行内样式信息一样，确定与规则相关的样式信息。与使用元素的 方式--样，在这种方式下也可以修改样式信息，如下面的例子所示。

var sheet = document.styleSheets(0］；

var rules = sheet. cssRules II sheet ♦ rules；    //取拜規则列表

var rule = rules (0J;    //取得第一条規则

rule.style.backgroundColor - "red"

CSSRulesExample. htm

必须要注意的是，以这种方式修改规则会影响贞面中适用于该规则的所有元素。换句话说，如果有 两个带有box类的＜div＞元素，那么这两个元素都会应用修改后的样式。

2.创建规则

DOM规定，要向现有样式表中添加新规则，需要使用insertRuleU方法。这个方法接受两个参 数：规则文木和表示在哪里插人规则的索引。下面是一个例子。

sheet. insertRule ("body { background-color: silver }•, 0) ； //DOM 方法

这个例子插人的规则会改变元素的背景颜色。插入的规则将成为样式表中的第一条规则(插人到了

位S 0 )-规则的次序在确定屋叠之后应用到文档的规则时至关重要。Firefox、Safari、Opera和Chrome

都支持insertRule ()方法。

IE8及更早版本支持一个类似的方法，名叫addRuleO ,也接收两必选参数：选择符文本和CSS 样式信息；一个可选参数：插人规则的位置,，在IE中插入与前面例子相同的规则，可使用如下代码。

sheet. addRu 1 e {" body", * background-color: silver", 0) ； //仅对 IE 有效

有关这个方法的规定中说，最多可以使用addRuleO添加4 095条样式规则。超出这个上限的调用 将会导致错误。

耍以垮浏览器的方式向样式表中插人规则，可以使用下面的函数„这个函数接受4个参数：要向其 中添加规则的样式表以及与addRuleO相同的3个参数，如下所示。

I;

 

function insertRule<sheet, selectorText, cssText, position){ if (sheet.insertRule){

position)；

 

sheet. insertRule (selectorText + • {" + cssText 十, } else if (sheet.addRule){

sheet.addRule(selectorText# cssText, position)；

}

J

CSSRulesExample2. htm

下面是调用这个函数的示例代码。

insertRule(document.styleSheets[0], -body", "background-color： silver"r 0)；

虽然可以像这样来添加规则，似随着要添加规则的增多，这种方法就会变得非常繁琐。因此，如果 要添加的规则非常多，我们建议还是采用第10章介绍过的动态加载样式表的技术。

3.删除规则

从样式表中删除规则的方法是deleteRuleO,这个方法接受一个参数：要删除的规则的位置。例 如，要删除样式表中的第一条规则，可以使用以下代码„

sheet .deleteRule (0) ； //DOM 方法

IE支持的类似方法叫removeRuleO,使用方法相同，如下所示： sheet. removeRule (0);    //仅对 IE 有效

下面是一个能够跨浏览器删除规则的函数。第一个参数是要操作的样式表，第二个参数是要删除的 规则的索引。

function deleteRule(sheetr index){ if (sheet.deleteRule){

sheet.deleteRule(index)；

} else if (sheet.removeRule){

sheet.removeRule(index)；

)

}

CSSRulesExample2. htm

调用这个函数的方式如下。

deleteRule(document.stylesheets(0]z 0)?

与添加规则相似，删除规则也不是实际Web开发中常见的做法。考虑到删除规则可能会影响CSS 戾叠的效果，因此请大家慎重使用。

12.2.3元素大小

本节介绍的属性和方法并不厲于“DOM2级样式”规范，但却与HTML元素的样式息息相关。DOM 中没有规定如何确定页面中元素的大小。1E为此率先弓|人了一些属性，以便开发人员使用。目前，所存 主要的浏览器都已经支持这些属性。

1.偏移量

首先要介绍的届性涉及偏移量（offsetdimension）,包括元素在屏幕上占用的所有可见的空间。元素 的讨见大小由其高度、宽度决定，包括所有内边距、滚动条和边框大小（注意，不包括外边距）。通过 下列4个属性可以取得元素的偏移量。

□    offsetHeight:元素在垂直方向上占用的空间大小，以像素计。包括元素的髙度、（可见的） 水平滚动条的髙度、上边框髙度和下边框髙度。

□    offsetWidth：元索在水平方向上rf用的空间大小，以像素计。包括元素的宽度、（可见的）垂 直滚动条的宽度、左边框宽度和右边框宽度。

□    offsetLeft:元素的左外边框至包含元素的左内边框之间的像素距离。

□    offsetTop：元素的上外边框至包含元素的上内边框之间的像素距离。

其中，of fsetLeft和of fsetTop属性与包含元素有关，包含元索的引用保存在of fsetParent 属性中。of fsetParent厲性不一定与parentNode的值相等。例如，＜td＞元素的offsetParent是 作为其祖先元素的＜table＞元素，因为＜（;北16:＞是在DOM层次中距4（3＞最近的一个具有大小的元素。 图12-1形象地展示了上面几个属性表示的不同大小。

of fsetParent

r.'t ' set Top

offsecwidth

图 12-1

要想知道某个元索在页面上的偏移鐘，将这个元索的offsetLeft和offsetTop与其of fsetParent 的相同属性相加，如此循环直至根元索，就对以得到一个基本准确的值。以下两个函数就可以用于分别 取得元索的左和上偏移量。

function getElementbeft(element){

var actualLeft = element.offsetLeft; var current = element.offsetParent;

while (current !== null){

actualLeft += current.offsetLeft; current = current.offsetParent;

}

return actualLeft；

function getElementTop(element){

var accualTop = element-offsetTopj var current = element.offsetParent;

while {current i=- null){

actualTop current. offsetTop； current = current.off sec Parent;

)

return actualTop;

OffsetDimensionsExample. htm

这两个兩数利用offsetParent厲性在DOM层次中逐级向上回溯，将毎个层次中的偏移量属性 合计到一块。对于简单的CSS布局的页面，这两函数可以得到非常精确的结果。对于使用表格和内嵌 框架布局的贞面，由于不同浏览器实现这些元素的方式不同，因此得到的值就不太精确了。一般来说, 贞面中的所有元素都会被包含在几个素中，而这些＜div＞元素的offsetParent又是 ＜body＞元素，所以 getElementLeft ＜)与 geCElementTop ()会返冋与 offsetLeft 和 of f setTop 相同的值。

(^\ 所有这些偏移量属性都是只读的，而且每次访问它们都需要重新计算。因此，应

该尽量避免重复访问这些属性；如果需要重复使用其中某些属性的值，可以将它们保 存在局部变量中，以提高性能。

2客片区大小

元素的客户区大小(clientdimension),指的是元索内容及其内边距所占据的空间大小。有关客户区 大小的属性有两个：clientwidth和clientHeight。其中，clientwidth属性元素内容区宽度加 上左右内边距宽度；clientHeight属性是元素内容区髙度加上上下内边距髙度。團12-2形象地说明 了这些属性表示的大小。

offsetParent

图 12-2

从字面上看，客户区大小就是元素内部的空间大小，因此滚动条占用的空间不计算在内。最常用到 这些属性的情况，就是像第8章讨论的确定浏览器视口大小的时候。如下面的例子所示，要确定浏览器 视U大小，可以使用document, document Element或document. body （在IE7之前的版木中）的 clientWidth 和 clientHeighto

function getViewport(){

if (document.compatMode == "BackCompat•){

return {

width： document.body.clientWidth, height: document.body.clientHeight

};

} else {

return {

width： document.documentElement.clientWidth, height: document.documentElement.clientHeight

这个函数首先检査document.compatMode属性，以确定浏览器是否运行在混杂模式。Safari 3.1 之前的版本不支持这个属性，因此就会自动执彳f else语句。Chrome、Opera和Firefox大多数情况下都 运行在标准模式下，因此它们也会前进到else语句。这个函数会返回一个对象，包含两个属性：width 和height;表示浏览器视口（ ＜肚101＞或＜130＜37＞元素）的大小。

与偏移量相似，客户区大小也是只读的，也是每次访问都要重新计算的。

3.滚动大小

最后要介绍的是滚动大小（scrolldimension）,指的是包含滚动内容的元素的大小。有些元素（例如 ＜11比1＞元素），即使没有执行任何代码也能自动地添加滚动条；但另外一些元素，则需要通过CSS的 overflow属性进行设置才能滚动。以下是4个与滚动大小相关的属性。

□    scrollHeight:在没有滚动条的情况下，元素内容的总髙度。

□    scrollWidth：在没有滚动条的情况下，元素内容的总宽度。

□    scrollLeft:被隐藏在内容区域左侧的像素数。通过设置这个属性可以改变元素的滚动位置。

□    scrollTop：被隐藏在内容区域上方的像素数。通过设S这个属性可以改变元素的滚动位置。 图12-3展示了这些属性代表的大小。

scrollWidth和scrollHeight主要用于确定元素内容的实际大小。例如，通常认*＜htm：l ＞元索 是在Web浏览器的视口中滚动的元素（IE6之前版本运行在混杂模式下时是＜1＞0＜^＞元素）。因此，带有 垂直滚动条的页面总髙度就是document.documentElement .scrollHeight。

对于不包含滚动条的页面而言，scrollWidth和scrollHeight与clientWidth和

clientHeight之间的关系并不十分清晰。在这种情况下，基于document. documentElement査看 这些厲性会在不同浏览器间发现一些不一致性问题，如下所述。

□    Firefox中这两组属性始终都是相等的，但大小代表的是文档内容区域的实际尺寸，而非视口的 尺寸。

□    Opera、Safari 3.1及更髙版本、Chrome中的这两组属性是有差别的，其中scrollWidth和 scrollHeight等于视口大小，而clientWidth和clientHeight等于文档内容区域的大小。

□ IE (在标准模式)中的这网组属性不相等，其中scrollWidth和scrollHeight等丁*文梢内 容区域的大小，而clientWidth和clientHeight等于视口大小。

scrollTop

scrollLeft

图 12-3

在确定文档的总商度时(包括基于视口的最小高度时)，必须取得scrollWidth/clientWidth和 scrollHeight/clientHeight中的最大值，才能保证在跨浏览器的环境下得到精确的结果。下面就 是这样一个例子。

var docHeight = Math.max (document .documentElement.. scrollHeight, document.documentElement.clientHeight)；

var docWidch = Math.max(document.documentElement.scroliWidth#

document.documentElement.clientwidth)；

注意，对于运行在混杂模式下的IE,则需要用document. body代替document. document-Elements

通过scrollLeft和scrollTop屈性既可以确定元素当前滚动的状态，也可以设置元素的滚动位 置。在元素尚未被滚动时，这两个属性的值都等于0。如果元素被垂直滚动了，那么scrollTop的值 会大于0,且表示元素上方不可见内容的像素高度。如果元素被水平滚动了，那么scrollLeft的值会 大于0,旦表示元素左侧不可见内容的像素宽度。这两个属性都是可以设置的，因此将元索的 scrollLeft和scrollTop设置为0,就可以®置元素的滚动位置。下面这个函数会检测元素是否位 于顶部，如果不是就将其回滚到顶部。

function scrollToTop(element){ if {element.scrollTop != 0){

element.scrollTop =0;

}

}

迭个函数既取得了 scrollTop的值，也设置了它的值。

4.确定元素大小

IE、Firefox3+、Safari4+s Opera9.5及 Chrome为每个元素都提供了一个 getBoundingClientRect ()方 法。这个方法返回会一^矩形对象，包含4个厲性：left、top、right和bottom。这搜属性给出了 元素在页面中相对F视口的位置。但是，浏览器的实现稍有不同。IE8及更早版本认为文档的左上角坐 标是(2,2)，而其他浏览器包括IE9则将传统的(0,0)作为起点坐标。W此，就潘要在，-开始检查一下位于 (0,0)处的元素的位置，在IE8及更早版本中，会返回(2,2),而在其他浏览器中会返回(0,0)。来看下面的 函数：

function getBoundingClientRect(element){

if (typeof arguments.callee.offset != "number"){

var scrollTop = document.documentElement.scrollTop? var temp = document.createElement("div")； temp.style.cssText = *position：absoluce；left：0;top：0;"; document.body.appendChiId(temp);

arguments.callee.offset = -temp.getBoundingClientRect().top - scrollTop? document.body.removeChild{temp}； temp = null；

var rect = element.getBoundingClientRect(,; var offset = arguments.callee.offset；

return {

left: rect.left 十 offset, right: rect.right + offset, top： rect.top + offset, bottom： rect.bottom + offset

GetBoundingClientRectExample.htm

这个函数使用了它自身的属性來确定是否要对坐标进行调整。第-步是检测属性是否有定义，如果 没布就定义一个。S终的offset会被设置为新元索上坐标的负值，实际上就是在1E中设置为-2,在 Firefox和Opera中没置为-0。为此，需要创建一个临时的元索，将其位置设置在(0,0),然后再调用其 getBoundingClientRect ()。而之所以要减去视口的scrollTop,是为了防止调用这个函数时窗口 被滚动了。这样编写代码，就无需毎次调用这个函数都执行两次getBoundingClientRect () 了。接 下来，再在传人的元素上调用这个方法并基丁•新的汁算公式创建一个对象。

对于不支持getBoundingClientRect (＞的浏览器，可以通过K他手段取得相同的信息。一般来 说，right和left的差值与offsetWidth的值相等，而bottom和top的差值与offsetHeight 相等。而且，left和top戚性大致等于使用本章前面定义的getElementLeft (＞和getElementTop () 函数取得的值。综合上述，就可以创建出下面这个跨浏览器的函数：

function getBoundingClientRect(element){

var scrollTop = docviment.documentElement. scrollTop; var BcrollLeft = document.documentElement.acrollLeft;

if (element.getBoundingClientRect){

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-54.jpg)

 

if (typeof arguments-callee.offset != "number"){ var temp = document.createElement(； temp.style.cssText = "position：absolute；left：0；top：0;"; document.body.appendChiId(temp);

arguments.callee.offset = -temp.getBoundingClientRect <).top - scrollTop; document.body.removeChiId(temp)；

temp = null；

}

var rect = element.getBoundingClientRect{); var offset = arguments.callee-offset；

return {

left: rect.left + offset, right: rect.right + offset, top: rect.top + offset, bottom： rect.bottom + offset

}；

} else {

var actualLeft ■ getElementLeft{element); var actualTop 龍 getElementTop(element);

return {

left: actualLeft - scrollLeft,

right: actualLeft + element.offaetwidth - acrollLeft, tops actualTop - scrollTop,

bottom: actualTop + element.offsetHeight - scrollTop

GetBoundingCHentRectExample.htm

这个函数在getBoundingClientRect (}有效时，就使用这个原生方法，而在这个方法无效时贝［J 使用默认的计算公式。在某些情况下，这个函数返冋的值可能会有所不同，例如使用表格布局或使用滚 动元素的情况下。

由于这里使用了 arguments.callee,所以这个方法不能在严格模式下使用c

12.3遍历

“DOM2级遍历和范围”模块定义了两个用于辅助完成顺序遍历DOM结构的类型：Nodeiterator 和TreeWalkero这两个类型能够基于给定的起点对DOM结构执行深度优先(depth-first)的遍历操作。 在与DOM兼容的浏览器中(Firefox 1及吏商版本、Safari 1.3及更髙版本、Opera 7.6及更髙版本、Chrome 0.2及更髙版本)，都可以访问到这些类型的对象。IE不支持DOM遍历。使用下列代码可以检测浏览器 对DOM2级遍历能力的支持情况。

var supportsTraversals = document.implementation.hasFeature("Traversal"z "2.0"); var supportsNodelterator = (typeof document.createNodelterator == "function"}; var support sTreeWalker = (typeof document. ere at eTr ee Walker == "function**,；

如前所述，DOM遍历是深度优先的DOM结构遍历，也就是说，移动的方向至少有两个(取决 于使用的遍历类型)。遍历以给定节点为根，不可能向上超出D0M树的根节点。以下面的HTML页 面为例》

<!DOCTYPE html>

<head>

</r.ead>

<body>

<pxb>Hello</b> world!</p> </body>

</html>

阁12-4展示了这个页面的0014树。

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-55.jpg)

 

阁 12-4

任和了节点都可以作为遍历的根节点。如果假设＜130办＞元素为根A点,那么遍历的第一步就是访问＜P＞ 元素，然后再访问同为＜^0办＞元素后代的两个文本节点。不过，这次遍历永远不会到达^）：!!^、＜head＞ 元素，也不会到达不属于＜body＞X素子树的任何节点。而以document为根节点的遍历则可以访问到文 档中的全部节点。阁12-5展示了对以document为根节点的DOM树进行深度优先遍历的先后顺序。

|    head

CD「El邮的:

 

(J) P^exVE;ajnpie |    Element o |

(?)| Te:

ixt Hello

 

图 12-5

从document开始依序向前，访问的第一个节点是document,访问的般后一个节点是包含 〜orldr•的文本节点。从文档最后的文本节点开始，遍历可以反向移动到DOM树的顶端。此时，访 问的第一个节点是包含"Hello1•的文本节点，访问的最后一个节点是document节点。Nodelterator 和TreeWalker都以这种方式执行遍历a

12.3.1 Nodeiterator

Nodeiterator类型是两者中比较简單的一个，可以使用document. createNodelt erat or ()方 法创建它的新实例。这个方法接受下列4个参数。

□    root：想要作为搜索起点的树中的节点。

□    whatToShow：表示要访问哪些节点的数字代码。

□    filter：是一个NodeFilter对象，或者一个表示应该接受还是拒绝某种特定节点的函数。

口 entityReferenceExpansion：布尔值，表术是否要扩展■实体引用。这个参数在HTML页面 中没有用，因为其中的实体弓I用不能扩展。

whatToShow参数是一个位掩码，通过应用一或多个过滤器(filter)来确定要访问哪些节点。这个 参数的值以常量形式在NodeFilter类型中定义，如下所示。

□    NodeFilter.SHOW_ALL：显示所有类型的节点。

□    NodeFilter. SHOW一ELEMENT:显示元素节点0

□    NodeFi 11er.SHOW_ATTRIBUTE:显示特性节点。由于DOM结构原因，实际上不能使用这个值。

□    NodeFilter. SHOW_TEXT：显示文本节点。

□    NodeFilter .SHOW_CDATA_SECTION：显示 CDATA 节点。对 HTML 页面没有用。

□    NodeFilter.SHOW_ENTITY_REFERENCE：显示实体引用节点。对HTML页面没有用。

□    NodeFilter.SHOW_ENTITYE:擺示实体节点。对HTML页囟没有用。

□    NodeFil.ter. SHOW_PROCESSING_INSTRUCTION:显示处理指令节点。对 HTML 页面没有用。

□    NodeFi 1 ter.SHOW_COMMENT：显示注释节点。

□    NodeFilter. SHOW_DOCUMENT：显示文档节点。

□    NodeFilter.SHOW_DOCUMENT_TYPE:显示文档类型节点。

□    NodeFilter.SHOW_DOCUMENT_FRAGMENT：显示文榜片段节点。对HTML页面没有用。

□    NodeFilter. SHOW_NOTATION：显示符号节点。对 HTML %•面没有用。

除了 NodeFilter. SHOW_ALL之外，可以使用按位或操作符来组合多个选项，如下面的例子所示：

var whatToShow = NodeFilter.SHOW_ELEMENT I NodeFilter.SHOW_TEXT;

可以通过createNodelterator ()方法的filter参数来指定自定义的NodeFilter对象，或者 指定一个功能类似节点过滤器(node filter)的函数c每个NodeFilter对象只有一个方法，即accept-Node ();如果应该访问给定的节点，该方法返回NodeFilter. FILTER_ACCEPT，如果不应该访问给 定的节点，该方法返回NodeFilter.FILTER_SKIP。由于NodeFilter是一个抽象的类型，因此不能 直接创建它的实例。在必要时，只要创建一个包含acceptNodeU方法的对象，然后将这个对象传入 createNodelterator ()中即可。例如，下列代码展示了如何创违一个只显示＜口＞元素的节点迭代器。

var filter = {

acceptNode： function(node){

return, node.tagName.toLowerCase() == Mp" ?

NodeFilter.F工LTSR_ACCEPT : NodeFilter.FILTER_SKIP;

var iterator - document.createNodeIterator(root, NodeFilLer.SHOW_ELEMENT, filter, false);

第三个参数也可以是-•个与acceptNodeO方法类似的函数，如下所示。

var filter =« function (node) {

return node.tagNazne. toLowerCase () ==    ?

NodeFilter.FILTER一ACCEPT :

NodePilter.PILTER_SKIP;

\> t

var iterator - document.createNodelterator(rootr NodeFilter.SHOW_ELEMENT, filter, false);

--般来说，这就是在JavaScript中使用这个方法的形式，这种形式比较简单，而且也跟其他的 JavaScript代码很相似。如果不指定过滤器，那么应该在第H个参数的位置上传入null。

下面的代碍创建了一个能够访问所有类型节点的简单的Nodeiterator。

var iterator = document.createNodelterator(document r NodeFilter.SHOW_ALLr null, false);

Nodeiterator类型的两个主要方法是nextNode ()和previousNode () y顾名思义，在深度优先 的DOM子树遍历中，nextNode ()方法用于向前前进一步，而previousNode ()用于向后后退一步。 在刚刚创建的Nodeiterator对象中，有一个内部指针指向根节点，因此第-次调用nextNode ()会 返回根节点。当遍历到DOM子树的最后一个节点时，nextNode ()返回null。previousNode ()方法 的工作机制类似。当遍历到DOM子树的最后一个节点，且previousNode ()返冋根节点之后，再次调 用它就会返回null。

以下面的HTML片段为例。

<div id="divl">

<pxb>Hello</b> worldi</p>

<ul>

<li>List item l</li>

<li>List item 2</li>

<li>List item 3</li>

</ul>

</div>

NodelteratorExamplel. htm

假设我们想要遍历<div>元素中的所有元素，那么可以使用下列代码。

var div = document.getElementById("divl");

var iterator = document.createNodelterator(div, NodeFilter.SHOW_ELEMENT, null, false)；

var node = iterator.nextNode();

while (node !== null) {

alert (node. tagNeirae) ；    //檢出标签名

node = iterator.nextNode()；

NodeIteratorExamplel.htm

在这个例？中，第一次调用nextNodeO返间＜p＞元素。因为在到达DOM子树末端时nextNodeO 返冋null,所以这里使用了 while语句在每次循环时检査对nextNodet)的调用是否返问了 null。 执行上面的代码会显示如卜_标签名：

DIV

P

B

UL

LI

LI

LI

也许用不着显示那么多信息，你只想返M遍历屮遇到的＜ii＞元素=很简单，只要使用一个过滤器 即可，如下面的例子所示。

var div = document.getElementById("divl");

var filter = function(node){

return node.tagName.toLowerCase() == "li" ?

NodeFiIter.FILTER_ACCEPT :

NodeFilter.FILTER^SKIP;

var iterator ■ document.createNodelterator(div, NodePiltor.SHOW_ELEMENT# filter, false};

var node = iterator.nextNode(): while (node !== null) {

alert (node. tagName) ；    //倫出标签名

node = iterator.nextNode{)；

}

NodeIteratorExample2. htm

在这个例子中，迭代器只会返回＜li＞元索。

由于nextNode(》和previousNode (＞方法都基于Nodeiterator在DOM结构中的内部指针工 作，所以DOM结构的变化会反映在遍历的结果中。

Firefox 3.5之前的版本没有实现createNodelterator ()方法，但却支持下一 节要讨论的createTreeWalker ()方法o

12.3.2 TreeWalker

TreeWalker 是 Modelterator 的一个更髙级的版本。除了包括 nextNode ()和 previousNode () 在内的相同的功能之外，这个类型还提供了下列用于在不同方向上遍历DOM结构的方法。

□    parentNodeO：遍历到当前节点的父节点；

□    firstChildO：遍历到当前节点的第-个子节点；

□    lastChildO：遍历到当前节点的最后一个子节点；

□    nextSiblingO :適历到当前节点的下一个同辈节点；

□    previousSibling ():遍历到当前节成的上个同辈节点。

创建TreeWalker对象要使用document. createTreeWalker ()方法，这个方法接受的4个参数

与document .createNodelterator ＜)方法相同：作为遍历起点的根节点、要显示的节点类型、过滤

器和一个表东是否扩展实体引用的布尔值。由于这两个创建方法很相似，所以很容易用TreeWalker

来代替Nodeiterator,如下面的例子所示o

var div = document.getElementById("divl"); var filter = function(node){

return node.tagName.toLowerCase() == •li"?

NodeFilter. FILTER_JkCCEPT :

NodeFilter.F工LTER_SKIP;

}；

var walker= document.createTreeWalker(div, NodeFiIter.SHOW ELEMENT, filter, false);

var node = i terator.nextNode()；

while (node 1== null) {

alert (node.tagName);    //输出标签名

node = iterator.nextNode();

Tree WalkerExamplel.htm

在这里，filter可以返冋的值有所不同。除了 NodeFilter.FILTER_ACCEPT和NodeFilter. FILTEECSKIP 之外，还可以使用 NodeFiiter.FILTER_REJECT。在使用 Nodeiterator 对象时， NodeFilter.FILTER_SKIP与NodeFilter.FILTER_REJECT的作用相同：跳过指定的节点。但在使 用TreeWalker对象时，NodeFi Iter. FILTER_SKIP会跳过相成节点继续前进到子树中的下一个节点， 而NodeFilter.FlLTER_REJECT则会跳过相应节点及该节点的整个子树。例如，将前面例子中的 NodeFilter. FILTER_SKIP修改成NodeFi Iter .FILTER_REJECT，结果就是不会访问任何节点。这是 因为第一个返回的节点是＜div＞,它的标签名不是”li”，于是就会返回NodeFilter.FILTER_REJECT, 这意味着遍历会跳过整个子树。在这个例子中，＜＜5:^＞元素是遍历的根节点，于是结果就会停止遍历。

当然，TreeWalker真正强大的地方在于能够在DOM结构中沿任何方向移动。使用TreeWalker 遍历DOM树，即使不定义过滤器，也可以取得所有＜li＞元索，如下面的代码所示。

var div = document.getElementByld("divl")；

var walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENTZ null, false);

walker. £ir8tChild() t    //传到＜P＞

walker.nextSibling() j    ✓/传剌＜ul＞

var nod© - walker. f iretChild();    "传到第一个＜li＞

while (node !== null) { alert(node.tagName);

node = walker.nextSibling();

}

TreeWalkerExamp!e2. him

因为我们知道＜li＞元素在文挡结构中的位置，所以可以立接定位到那里，即使用firstChildO 转到＜p＞元素，使nextSibling()转到＜ul＞元索，然后再使用f irstChild U转到第一个＜li＞元素。 注意，此处TreeWalker只返间元索(由传人到createTreeWalker ()的第二个参数决定)。因此，可 以放心地使用nextSibling ()访问毎一个＜li＞元素，直至这个方法最后返回null。

TreeWalker类型还有一个属性，名叫currentNode,表示任何遍历方法在上次遍历中返回的 节点。通过设置这个屈性也可以修改遍历继续进行的起点，如下面的例子所示。

var node = walker.nextNode();

alert(node === walker.currentNode)；    //true

walker. currentNode = document. body;    "修改起点

与Nodeiterator相比，TreeWalker类型在遍历DOM时拥有更大的灵活性。由于IE中没有对 应的类型和方法，所以使用遍历的跨浏览器解决方案非常少见。

12.4范围

为了让开发人员更方便地控制页面，“D0M2级遍历和范围”模块定义了 "范围”(range)接口。通 过范围可以选择文档中的-个K域，而不必考虑节点的界限(选择在后台完成，对用户是不可见的)。 在常规的DOM操作不能更有效地修改文档时，使用范围往往可以达到目的。Firefox、Opera、Safari和 Chrome都支持DOM范围。IE以专有方式实现了自己的范围特性。

12.4.1 DOM中的范围

DOM2级在Document类型中定义了 createRange (>方法。在兼容DOM的浏览器中，这个方法 属于document对象。使用hasFeatureO或者直接检测该方法，都吋以确定浏览器是否支持范围。

var supportsRange = document.implementation.hasFeature("Range"# "2.0")； var alsoSupportsRange = (typeof document.createRange == "function")；

如果浏览器支持范围，那么就可以使用createRange ()来创建DOM范围，如下所示： var range = document.createRange();

与节点类似，新创建的范围也直接与创建它的文档关联在一起，不能用于其他文挡。创建了范围之 后，接下来就可以使用它在后台选择文档中的特定部分。而创建范围并设置了其位置之后，还可以针对 范围的内容执行很多种操作，从而实现对底层DOM树的更精细的控制。

每个范围由一个Range类型的实例表示，这个实例拥有很多属性和方法。下列属性提供了当前范 围在文档中的位置信息。

□    startContainer：包含范围起点的节点(即选区中第一个节点的父节点)。

□    startOf£set：泡围在startContainer中起点的偏移量。如果startContainer是文本节 点、注释节点或CDATA节点，那么startOf fset就是范围起点之前跳过的字符数量。否则， startOffset就是范围中第一个子节点的索引。

□    endContainer：包含范围终点的节点(即选区中最后一个节点的父节点)。

口 endOffset:范阐在endContainer中终点的偏移量(与startOffset遵循相同的取值规则)。

□    commonAncestorContainer: startContainer 和 endContainer 共同的祖先Yi点在文相树 中位置最深的那个。

在把范围放到文档中特定的位置时，这些属性都会被賦值。

1.用DOM范围实现简单选择

要使用范围来选择文档中的一部分，最简的方式就是使用selectNode <)或selectNodeContents ()。 这两个方法都接受一个参数，即一个DOM节点，然后使用该节点中的信息来填充范围。其中，

selectNodeU方法选择整个节点，包括其子节点；而selectNodeContents ()方法则只选择节点的 子节点。以下面的HTML代码为例。

<!DOCTYPE html>

<html>

<body>

<p id-"pl"><b>Hello</b> world!</p>

</body>

</html>

我们可以使用下列代码来创建范m:

var range1 = document.createRange(); range2 = document.createRange(); pi = document.getElementById("pi *)；

rangel，selectNode(pi); range?.,selectNodeContents (pi);

DOMRangeExample.htm

这里创建的两个范岡包含文档中不同的部分：rangl包含^/>元索及其所有子元素，而rang2包 含<1>/>元素、文本节点-Hello•和文本节点-world!* (如图12-6所示)。

rangel

I    I

<p id=Mplw><b>Hello</b> worldK/p>

I___I

range2

图 12-6

在调用 selectNode(｝时，startContainerx endContainer 和 conunonAncestorContainer 都等于传人节点的父节点，也就是这个例子中的document.body。而startOffset属性等于给定节 点在其父节点的childNodes集合中的索引(在这个例子中是I―因为兼容DOM的浏览器将空格算 作--个文本节点)，endOffset等于startOffset加1 (因为只选择了一个节点)。

在调用 selectNodeContents ()时，startContainer、endContainer 和 commonAncestorConta-iner等于传人的节点，即这个例子中的<p>元素3而startOffset属性始终等于0,因为范围从给定节 点的第一个子节点开始。最后，endOffset等于子节点的数量(node. childNodes. length),在这个例 子中是2。

此外，为了更精细地控制将哪些节点包含在范围中，还可以使用下列方法。

□    setStartBefore (refWode):将范M的起点设置在refWode之前，因此refJVode也就是范围 选区中的第一个子节点。同时会将startContainer属性设置为re/Wode.parentAtode，将 startOffset屑性设置为refNode在其父节点的childNodes集合中的索引。

□    set Start Af ter (refAfode):将范围的起点运置在refAZode之后，因此refWode也就不在范 围之内了，其下一个同辈节点才是范闹选区中的第-个子节点。同时会将startContainer屈 性设S为refWbde.parentNbde,将startOffset属性设置为ref Node在其父节点的 childNodes集合中的索引加1。

□ setEndBefore (refNode):将范围的终点设置在refNode之前，因此refWbde也就不在范围 之内了，其上一个同辈节攻才是范围选区中的最后一个子节点。同时会将endContainer属性

设置为 retNode.parenLNode,endOff set 属性设置为 refWdde在其父节点的 childNodes 集合中的索引。

口 setEndAfter (refWode):将范围的终点设置在refWode之后，因此refWdde也就是范围选区 中的最后—t•子节点。同时会将endContainer屈性设置为refMxJe.parentJVode，将 endOff set JS性设置为refWode在其父节点的childNodes集合中的索引加1。

在调用这些方法时，所苻属性都会自动为你设S好。不过，要想创建复杂的范围选区，也可以直接 指定这些属性的值。

2.用DOM范围实现复杂选择

要创建复杂的范围就得使用setStart《〉和setEndO方法。这两个方法都接受两个参数：一个参 照节点和一个偏移it值o对setStart (}来说，参照节点会变成startContainer,而偏移量值会变成 startOf fseto对于setEnd()来说，参照节点会变成endContainer,而偏移最值会变成endOff set 0

可以使用这两个方法来模仿selectNode ()和selectNodeContents ＜)。来看下面的例子：

var range1 = document.createRange{〉； range2 = document.createRange{); pi = document.getElementById("pi");

piIndex = -1;

1/ len;

for (i=0, len＞：pi.parentKode.childNodes.length; i ＜ len； i++)( if (pi.parentKode.childNodes[i] ss pi) {

piIndex s i; break；

}

)

range1.setStart(p1.parentNode, pllndex)/ rangel.setEnd(pi.parentNode# pllndex + 1)t range2.setStart(pi, 0);

range2.aetznd{pi, pi.childModea.length);

DOMRangeExample2. htm

显然，要选择这个节点(使用rangel ),就必须确定当前节点(pi)在其父节点的childNodes 集合中的索引。而要选择这个节点的内容(使用ranges ),也不必计算什么；只要通过setStarcO 和 setEnd ()设置默认值即可。模仿 selectNode ()和 selectNodeContents ()并不是 setStart () 和setEndO的主要用途，它们更胜一筹的地方在于能够选择节点的一部分。

假设你只想选择前面HTML示例代码中从“Hello1，的^llo,•到”world! ”的noB-很容易做到。

第一步是取得所有节点的引用，如下面的例子所示：

var pi = document.getElementById{hpl*); helloNode = pi.firstchild.firstChild; worldNode = pi.lastChild;

DOi\^RangeExamp!e3. htm

实际上，”Hello•’文本节点是＜p＞元素的孙子节点，因为它本身是＜b＞元索的一个子节点。W此， pi. f irstchild 取得的是 ＜b＞，而pi. f irstchild. f irstchild 取得的才是这个文木节点。《world!-文本节点是＜P＞元素的第二个子节点(也是最后一个子节点)，因此可以使用pi. lastChild取得该节

点。然后，必须在创建范围时指定相应的起点和终点，如下面的例子所示。

var range = document.createRange(); range.setStart(helloNode, 2); range.setEnd(worldNode, 3);

D0MRangeExample3. htm

因为这个范围的选区垃该从"Hello■中-e>的后面开始，所以在setStart （>中传人helloNode 的同时，传入了偏移量2 （即-e”的下一个位置；的位置是0）。设置选区的终点时，在setEndO 中传人wor ldNode的同时传入了偏移滅3,表示选区之外的第一个字符的位置，这个字符是”r”，它的 位置足• 3 （位S 0上还有一个空格）。如图12-7所示。

range

I    I

<p id=”Pl"><b>lHlellllbK/b>l lwblrgBin</D>

01234    0123456

图 12-7

由于helloNode和worldNode都是文本节点，因此它们分别变成了新建范围的startContainer 和endContainer。此时startOf fset和endOffset分别用以确定两个节点所包含的文本中的位置， 而不是用以确定子节点的位置（就像传人的参数为元索节点时那样）。此时的coiranonAncestor-Container是<p>元素，也就是同时包含这两个节点的第一个祖先元索。

当然，仅仅是选择了文档中的某--部分用处并不大。何重要的是，选择之后才可以对选区进行操作。

3.操作DOM范围中的内容

在创建范围时，内部会为这个范围创建一个文档片段，范围所属的全部节点都被添加到了这个文档 片段中。为了创建这个文档片段，范围内容的格式必须正确有效。在前面的例子中，我们创建的选区分 别开始和结朿于两个文木节点的内部，因此不能算是格式良好的DOM结构，也就无法通过DOM来表 示。但是，范围知道自身缺少哪些开标签和闭标签，它能够重新构建有效的DOM结构以便我们对其进 行操作。

对于前面的例子而言，范围经过计算知道选区中缺少-个开始的<b>标签，因此就会在后台动态加 人一个该标签，同时还会在前面加人一个表示结束的</1»标签以结束W。于是，修改后的DOM就 变成了如下所示。

<pxb>He</bxb>llo</b> world!</p>

另外，文本节点-world!»也被拆分为两个文本节点，一个包含-wo”，另，-个包含-rid!-。最终的 DOM树如阁12-8所示，右侧是表示范围的文档片段的内容P

像这样创建丫范W之后，就可以使用各种方法对范围的内容进行操作了（注意，表示范围的内部文 档片段中的所有节点，都只是指向文档中相应节点的指针）。

第一个方法，也是最容易理解的方法，就是deleteContents ()。这个方法能够从文档中删除范 所包含的内容。例如：

![img](E:/11.ProgramFiles/Typora/JavaScriptd8a70b8fbea1082c34809-57.jpg)

 

var pi = document.getElementById("pi■)； helloNode = pi.firstChild.firstChild; worldNode = pi.lastChild；

range = document.createRange();

range.setstart(helloNode, 2)；

range.setEnd{worldNode, 3);

range.deleteContents(> ?

DO MRangeExample4. htm

文档    范围

團 12-8

执行以上代码后，页面中会显示如下HTML代码：

<pxb>He<Zb>rld! </p>

由于范围选区在修改底层DOM结构时能够保证格式良好，因此即使内容被删除了，最终的DOM 结构依旧是格式良好的。

与deleteContents ()方法相似，extractContents ()也会从文样中移除范围选区。但这两个方

法的区别在于，extractContents(>会返W范围的文杓片段。利用这个返冋的值，可以将范围的内容

插人到文档中的其他地方。如下面的例子所示：

var pi = document. getElementByld (*pl '*); helloNode = pi.firstchild.firstChiId； worldNode - pi-lastChild；

range = document.createRange();

range.setStart(helloNode, 2); range.setEnd(worldNode, 3);

var fragment = range.extractContents()j pi.parentNode.appendChild(fragment)?

在这个例子中，我们将提取出来的文档片段添加到了文档<body>元索的末尾。(记住，在将文档片 段传人appendChildO方法中时，添加到文档中的只是片段的子节点，而非片段本身。)结果得到如下 HTML代码：

<pxb>He< /b>rld! < Zp>

<b>llo</b> wo

还一种做法，即使用cloneContentsU创建范围对象的一个副本，然后在文档的其他地方插人该 副本。如下面的例子所示：

var pi = document .getElementByld (**plh), helloNode = pi.firstChild.firstChild, worldNode = pi.lastChild,

range = document.createRange();

range.setStart(helloNode, 2); range.setEnd(worldNode, 3);

var fragment ■ range.cloneContents(); pi.parontNode.appendChild(fragment);

DOMRangeExample6. htm

这个方法与extractContentsO非常类似，因为它们都返间文档片段。它们的主要区别在于， cloneContents ()返回的文档片段包含的是范闱中节点的副本，而不是实际的节点。执行上面的操作 后，页面中的HTML代码应该如下所示：

<pxb>Hello</b> world!</p>

<b>llo</b> wo

有一点请读者注意，那就是在调用上面介绍的方法之前，拆分的节点并不会产生格式良好的文挡片 段。换句话说，原始的HTML在DOM被修改之前会始终保持不变。

4.插入DOM范围中的内容

利用范围，可以删除或复制内容，还可以像前面介绍的那样操作范围中的内容。使用insertNodeO 方法可以向范围选区的开始处插人一个节点。假没我们想在前面例子中的HTML前面插人以下HTML 代码:

<span style="color： red">Inserted text</span>

那么，就可以使用下列代码：

var pi = document.getElementByld("pi"); helloNode = pi.firstChild.firstChild; worldNode = pi.lastChild；

range = document.createRange{);

range.setStart(helloNode, 2);

range.setEnd(worldNode, 3);

var apan = document.createElement("span")/

span.style.color = "red";

span.appendChiId(document.createTextMode("Inserted text"))； range.insertNode(span);

运行以上JavaScript代码，就会得到如下HTML代码：

<p id="pi"><b>He<span styie="color： red">Inserted text</span>llo</b> world</p>

注意，好被插人到了-Hello•中的-llo，’前面，而该位置就是范围选区的开始位置。还要 注意的是，由于这里没有使用上一节介绍的方法，结果原始的HTML并没有添加或删除<b>元索。使用 这种技术可以插人一跌帮助提示信息，例如在打开新窗U的链接旁边插人一幅图像。

除了向范围内部插人内容之外,还可以环绕范围插人内容，此时就要使用surroundContentsO 方法。这个方法接受一个参数，即环绕范围内容的节点。在环绕范围插人内容时，后台会执行下列 步骤。

(1)    提取出范围中的内容(类似执行extractContent ());

(2)    将给定节点插人到文档中原来范围所在的位置上；

(3)    将文相片段的内容添加到给定节点中。

可以使用这种技术來突出显示网页中的某些词句，例如下列代码：

var pi = document.getElementByld{"pi"); helloNode = pi.firstChiId.firstChiId; worldNode = pi.lastChild;

range = document.createRange();

range.aelectNode(helloNode);

var span = doeument.createBlement("span■); span.style.backgroundColor = "yellow"; range.surroundContents(span);

DOMRangeExample8.htm

会给范围选区加上一个黄色的背景。得到的HTML代码如下所示：

<pxb>He</bxspan style-"background-color：yellow"><b>llo</b> wo</span>rld!</p>

为了插A<span>,必须将<*>元素拆分成两个<b>元素，一个包含W ,另一个包含~llo"。拆分 之后，就可以稳妥地插人<span>了。

5.折叠DOM范围

所谓折叠范围，就是指范围中未选择文档的任何部分。可以用文本框来描述折叠范围的过程。假设 文本框中有一行文本，你用跃标选择了其中一个完整的单词。然后，你单击鼠标左键，选区消失，而光 标则落在了其中两个字母之间。同样，在折叠范围时，典位置会落在文档中的两个部分之问，可能是范 围选区的开始位置，也可能是结束位置。图12-9展示了折叠范围时发生的情形。

使用collapse()方法来折叠范围，这个方法接受一个参数，一个布尔值，表示要折叠到范围的哪 一端。参数true表示折叠到范围的起点，参数false表示折叠到范围的终点。要确定范围已经折畳完 毕，可以检査collapsed属性，如下所示：

range.collapse(true)； alert(range.collapsed)?

//折叠到起点 "鍮出 true

 

<p id=*,pl"><b>H^llo</b> wcjrld! </p> 原始范围

<p id=hpl"><b>Helllo</b> world! </p> 折香到开始位置

<p id— "pi" ><b> He 1 lo< /b> 折登到结束位置

wcjrld!</p>

 

图 12-9

检测某个范围是否处于折叠状态，可以帮我们确定范围中的两个节点是否紧密相邻。例如，对于下 面的HTML代码：

<p id= "pi" >Paragraph l</pxp id-"p2">Paragraph 2</p>

如果我们不知道其实际构成(比如说，这行代码是动态生成的)，那么可以像下面这样创建一个范围。

var pi = document.getElementById("pi")f p2 = document.getElementById("p2w), range = document.createRange()；

range.setStartAfter(pi)；

range.setStartBefore(p2);

alert (range, col lapsed) ；    //梭出 true

在这个例子中，新创建的范围是折叠的，因为pi的后面和p2的前面什么也没有。

6.比较DOM范围

在有多个范围的情况下，可以使用compareBoundaiyPoints <)方法来确定这些范围是否有公共 的边界(起点或终点)。这个方法接受两个参数：表示比较方式的常量值和要比较的范围。表示比较方 式的常量值如下所示。

□    Range.START_TO_START(0):比较第一个范围和第二个范围的起点；

□    Range.START_TO_END (1):比较第一个范围的起点和第二个范围的终点；

□    Range. END_TO_END (2)：比较第一个范围和第二个范围的终点；

□    Range. END_TO_START (3):比较第一个范围的终点和第一个范围的起点。 compareBoundaryPoints()方法可能的返回值如下：如果第一个范围中的点位于第二个范围中的

点之前，返回-1;如果两个点相等，返回0;如果第一个范围中的点位于第二个范围中的点之后，返回 1。来看下面的例子。

var rangel = document.createRange{)? var range2 = document.createRange()? var pi = document.getElementByld("pi")；

rangel.selectNodeContents(pi)；

range2.selectNodeContents(pi)；

range2.setEndBefore{pi,lastChild)；

alert(rangel.compareBoundaryPoints(Range.START_TO_START, range2))；    "0

alert(rangel.compareBoundaryPoints(Range.END_TO_END, range2))；    "1

在这个例子中，两个范围的起点实际上是相同的，因为它们的起点都是由selectNodeContents {) 方法设置的默认值来指定的。因此，第一次比较返回0。但是，range2的终点由于调用setEndBefore {) 已经改变了，结果是rangel的终点位于ranges的终点后面(见图12-10)，因此第二次比较返回1。

rangel

I-1

<p id= ,,pl,,><b>Hello</b> world!</p>

range2 图 12-10

\7.    复制DOM范围

可以使用cloneRangeO方法复制范围。这个方法会创建调用它的范围的一个副本。 var newRange = range.cloneRange();

新创建的范围与原来的范阐包含相同的厲性，而修改它的靖点不会影响原来的范围。

\8.    清理DOM范围

在使用完范围之后，最好是调用detach(>方法，以便从创建范围的文挡中分离出该范围。调用 detach()之后，就可以放心地解除对范围的引用，从而让垃圾回收机制回收其内存了。来看下面的 例子。

range.detach() ；    //从文档中分离

range = null;    //解除引用

在使用范闱的最后再执行这两个步骤是我们推荐的方式。一旦分离范闱，就不能再恢复使用了。

lERangeExamplel .htm

12.4.2旧8及更早版本中的范围

虽然IE9支持DOM范围，但IE8及之前版本不支持DOM范围。不过，IE8及早期版本支持一种类 似的概念，即文本范围(textrange)。文本范ffl是IE专有的特性，其他浏览器都不支持。顾名思义，文 本范围处理的主要是文本(不一定是DOM节点)。通过<body>、<button>、<input>^<textarea> 等这几个元索，可以调用createTextRange()方法来创建文本范围。以下是一个例子：

var range = document.body.createTextRange();

像这样通过document创建的范围可以在页面中的任何地方使用(通过其他元索创建的范围则只能 在相应的元素中使用)。与DOM范围类似，使用ffi文本范围的方式也肴很多种a

1.用IE范围实现简单的选择

选择页面中某一区域的最简单方式，就是使用范围的findTextU方法。这个方法会找到第一次出 现的给定文本，并将范闱移过来以环绕该文本。如果没有找到文本，这个方法返回false;否则返回 true。同样，仍然以下面的HTML代码为例。

<p id-"pi"><b>Hello<Zb> world!</p>

要选择-Hello•‘，可以使用下列代码。

var range = document.body.createTextRange()； var found = range.findText{KHello")；

在执行完第二行代科之后，文本”Hello•就被包围在范M之内了。为此，可以检杏范闱的text属 性来确认(这个性返回范围中包含的文本)，或者也可以检查findText()的返回值——在找到了文 本的情况下返回值为Crue。例如：

alert(found)；    //true

alert(range.text);    //"Hello"

还可以为findTextO传人另一个参数，即一个表示向哪个方向继续搜索的数值。负值表示应该从 当前位置向后搜索，而正值表示应该从当前位置向前搜索。因此，要査找文档巾前两个，Hello。的实例， 应该使用下列代码。

var found = range.findText("Hello");

var foundAgain = range.f i ndText("Hello"# 1)；

IE中与DOM中的selectNode ()方法最接近的方法是moveToElementText (),这个方法接受一 个DOM元素，并选择该元素的所有文本，包括HTML标签。下面是一个例子。

var range = document.body.createTextRange()； var pi = document.getElementById("pi")； range.moveToElementText(pi);

IERangeExample2. htm

在文本范围巾包含HTML的悄况下，可以使用htmlTexc属性取得范围的全部内容，包括HTML 和文本，如下面的例子所示。

alert(range.htralText)；

IE的范围没有任何属性可以随着范围选区的变化而动态吏新。不过，其parentElementO方法倒 是与 DOM 的 commonAncestorContainer 属性类似。

var ancestor = range.parentElement()；

这样得到的父元素始终都可以反映文本选区的父节点。

2.使用IE范围实现复杂的选择

在E中创建复杂范围的方法，就是以特定的增量向四周移动范闱。为此，IE提供了 4个方法： move(), moveStart()、moveEndG和expand()。这些方法都接受两个参数：移动单位和移动单位 的数量。其中，移动单位是下列一种字符串值。

□    "character":逐个字符地移动

□    "word-:逐个单同(一系列非空格字符)地移动。

□    "sentence":逐个句子(一系列以句号、问号或叹号结尾的字符)地移动。

□    -textedit"：移动到当前范围选区的开始或结束位置。

通过moveStarCO方法可以移动范围的起点，通过moveEndO方法可以移动范围的终点，移动'的

幅度由单位数量指定，如下面的例子所示。

range.moveStart ("word*, 2) ;    //起点移动 2 个单词

range.moveEnd( "character", 1);    //终点移动 1 个+符

使用expand (＞方法可以将范围规范化。换句话说，expand ()方法的作用是将任何部分选择的文 本全部选中。例如，当前选择的是一个.单词中间的两个字符，调用expand (-word。)可以将整个单词都 包含在范围之内。

而move ()方法则首先会折叠3前范国(U:起点和终点相等)，然后再将范围移动指定的单位数量， 如下面的例子所示。

range.move {"character" , 5);    //移动 5 个字符

调用move ()之后，范围的起点和终点相同，因此必须再使用moveStart U或moveEnd()创建新 的选区。

3.操作IE范围中的内容

在IE中操作范围中的内容可以使用text厲性或pasteHTML ()方法。如前所述，通过text厲性 可以取得范围中的内容文本；但是，也可以通过这个属性设置范围中的内容文本。来看一个例子。

var range = document.body.createTextRange(); range.findText("Hello")； range.text = "Howdy *;

如果仍以前面的Hello World代码为例，执行以上代码后的HTML代码如下。

<p id="pi"><b>Howdy</b> worldl</p>

注意，在设置cext属性的情况下，HTML标签保持不变。

要向范围中插人HTML代码，就得使用pasteHTML (>方法，如下面的例子所示。

var range = document.body.createTextRange{)? range.findText(■Hello*):

range.pasteHTML(n<em>Howdy</em>R);

IERangeExample3. htm

执行这些代码后，会得到如下HTML。

<p id= "pi• ><bxem>Howdy</em></b> world!</p>

不过，在范围中包含HTML代码时，不应该使用pasteHTML<>,因为这样很容易导致不可预料的 结果一很可能是格式不正确的HTML。

\4.    折旧范围

IE为范围提供的col lapse ()方法与相应的DOM方法用法一样：传人true把范围折番到起点， 传人false把范围折叠到终点。例如：

range, col lapse (true) ；    //折 4 到起点

可惜的是，没有对应的collapsed属性让我们知道范围是否已经折叠完毕。为此，必须使用 boundingWidth属性，该属性返回范围的宽度(以像素为单位)。如果boundingWidth属性等于0, 就说明范围已经折番了：

var isCollapsed = (range.boundingWidth == 0);

此外，还有bouridingHeight、boundingLeft和boundingTop等属性，虽然它们都不像 boundingWidth那么有用，但也可以提供一些有关范围位置的信息。

\5.    比较IE范围

IE 中的 compareEndPoints ()方法与 DOM 范阐的 compareBoundaryPoints ()方法类似。这个 方法接受两个参数：比较的类型和要比较的范围。比较类型的取值范围是下列几个字符串值：

"StartToStart"、-StartToEnd"、-EndToEnd”和"EndToStart"。这几种比较类型与比较 DOM 范 围时使用的几个值是相同的。

同样与DOM类似的是，compareEndPoints (＞方法也会按照相同的规则返回值，即如果第一个范 围的边界位于第二个范围的边界前面，返回-1;如果二者边界相同，返回0;如果第一个范围的边界位 于第二个范m的边界后面，返回1。仍以前面的Hello World代码为例，下列代码将创建两个范围，一个 选择"Hello world!"(包括＜b＞标签)，另个选择"Hello"。

var range1 = document.body.createTextRange(); var range2 = document.body.createTextRange();

rangel.findText("Hello world!■); range2.findText("Hello");

alert(rangel.compareEndPoints("StartToStart", range2));    //0

alert(range1.compareEndPoints(■EndToEnd", range2))；    "1

IERangeExample5. htm

由于这两个范围共享同一个起点，所以使用compareEndPoints ()比较起点返间0。而rangel 的终点在range2的终点后面，所以compareEndPoints ()返问1。

IE中还冇两个方法，也是用于比较范闱的：isEqual()用于确定两个范围是否相等，inRangeO

用于确定一个范围是否包含另一个范ffl。下面是相应的示例。

var rangel = document.body-createTextRange(); var range2 = document.body.createTextRange{)； rangel.findText{"Hello World"};

range2.findText{"Hello");

alert("range1.isEqual(ronge2): " + rangel.isBqual(range2));    //false

alert("rangel.inRange(range2)s" + rangel.inRange(range2));    //true

IERa”geExample6. htm

这个例子使用了与前面相同的范围来示范这两个方法。由于这两个范围的终点不同，所以它们不相 等，调用isP；qual(＞返回false。由于range2实际位于rangel内部，它的终点位于后者的终点之 前、起点之后，所以range2被包含在rangel内部，调用inRange (＞返回true。

6.复制旧范围

在［E中使用duplicated方法可以复制文木范围，结果会创建原范围的-个副本，如下面的例子 所示。

var newRange = range.duplicate();

新创建的范围会带有与原范围完全相同的属性。

12.5小结

DOM2级规范定义了一些模块，用于增强DOMIS。“DOM2级核心”为不同的DOM类型引人了 一些与XML命名空间有关的方法。这些变化只在使用XML或XHTML文档时才有用；对于HTML文 档没有实际意义。除7•与XML命名空间有关的方法外，“DOM2级核心”还定义了以编程方式创建 Document变例的方法，也支持了创建Document Type对象u

“D0M2级样式”模块主要针对操作元素的样式信息而开发，其特性简要总结如下■:

□每个元素都杳一个关联的style对象，可以用来确定和修改行内的样式。

□要确定某个元素的计算样式(包括应用给它的所有CSS规则)，可以使用getcomputedstyle () 方法。

□    EE不支持getcomputedstyle ()方法，但为所有元索都提供了能够返回相同信息current Style 属性。

□可以通过document .styleSheets集合访问样式表。

□除IE之外的所有浏览器都支持针对样式表的这个接口，也为几乎所冇相应的DOM功能提供 了自己的--套属性和方法。

“D0M2级遍历和范模块提供丫与DOM结构交年的不同方式，简要总结如下。

□遍历即使用Nodeiterator或TreeWalker对DOM执行深度优先的遍历c

□    Nodeiterator是一个简单的接U，只允许以一个节点的步幅前后移动。而TreeWalker在提 供相同功能的同时，还支持在DOM结构的各个方向上移动，包括父节点、同辈节点和子节点等 方向=

口范围是选择DOM结构中特定部分，然后再执行相应操作的一种手段。

□使用范围选区可以在删除文档巾某些部分的同时，保持文档结构的格式良好，或者复制文档中 的相应部分。

□    IE8及更早版本不支持“DOM2级遍历和范围”模块，但它提供了一个专有的文本范围对象，可 以用来完成简单的基于文本的范围操作。IE9完全支持DOM遍历。