DOM

本章内容

□ H解包含不同层次节点的DOM □使用不同的节点类型 □克服浏览器兼容性问题及各种陷阱

DOM （文档对象模型）是针对HTML和XML文档的--个API （应用程序编程接口）。DOM描 绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。DOM脱胎于

Netscape及微软公司创始的DHTML（动态HTML）,但现在它已经成为表现和操作页面标记的真正的跨 平台、语言屮立的方式。

1998年10月DOM1级规范成为W3C的推荐标准，为基本的文档结构及丧询提供了接口。本章主

要讨论与浏览器中的HTML页面相关的DOMI级的特性和应用，以及JavaScript对DOM1级的实现。 IE、Firefox、Safari、Chrome 和 Opera都非常完善地实现T DOM。

i 注意，IE中的所有DOM对象都是以COM对象的形式实现的。这意味着IE中的

DOM对象与原生JavaScript对象的行为或活动特点并不一致。本章将较多地谈及这呰 差异。

10.1节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类 型，每种类型分别表示文杓中不同的信息及（或）标记。每个节点都拥有各自的特点、数据和方法，另 外也与其他节点存在某种关系。节点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点 为根节点的树形结构。以下面的HTML为例：

10

 

<head>

<title>Sample Page</title>

</head>

<body>

<p>Hello World!</p>

</body>

</html>

可以将这个简单的HTML文档表示为一个层次结构，如阁10-1所示。

文档节点是每个文样的根节点。在这个例子中，文档节点只有一个子节点，元素，我们

称之为文档元素。文档元素是文档的最外层元素，文档中的其他所存元素都包含在文档元岽中。每个文 档只能有一个文档元素。在HTML页面中，文档元素始终都是chtmb元素。在XML中.没有预定义 的元素，因此任何元素都可能成为文档元素。

每一段标记都可以通过树中的一个节点来表示：HTML元素通过元素节点表示，特性（attribute） 通过特性节点表示，文档类型通过文档类型节点表示，而注释则通过注释节点表乐。总共有12种节点 类瑠，这些类型都继承自一个基类型。

Document

—| Element htnd

| —\| Element head | ]                |                     |
| ---------------- | ---------------- | ------------------- |
|                  | —\|Element title |                     |
|                  |                  | -1 Text Sample Page |

| -1 Element body |              |                      |
| --------------- | ------------ | -------------------- |
|                 | -1 Element p |                      |
|                 |              | -1 Text Hello World! |

图1（M

10.1.1 Node 类型

DOMI级定义了一个Node接口，该接U将由DOM中的所有节点类型实现。这个Node接口在 JavaScript中是作为Node类型实现的；除了 IE之外，在其他所有浏览器中都可以访问到这个类型。 JavaScript中的所有节点类型都继承ft Node类型，因此所有节点类型都共享着相同的基本属性和方法。

每个节点都有一个nodeType属性，用于表明节点的类型。节点类型由在Node类型中定义的下列 12个数值常量来表示，任何节点类型必居其一：

Q Node.ELEMENT_NODE（ 1）;

□    Node.ATTRIBUTE_NODE（2）; a Node. TEXT_NODE（3）;

□    Node.CDATA_SECTION_NODE（4）;

□    Node.ENTITY一REFERENCE一NODE（5）;

□    Node. ENTITY_NODE（6）;

□    Node.PROCESSING_INSTRUCTION_NODE（7）;

□    Node.COMMENT_NODE（8）;

□    Node. D0CUMENT_N0DE(9);

□    Node.DOCUMENT一TYPE_NODE( 10);

□    Node. DOCUMENT_FRAGMENT_NODE( 11);

口 Node.NOTATION一NODE( 12)。

通过比较上面这些常S,可以很容易地确定节点的类型，例如：

if (someNode.nodeType == Node.ELEMENT_NODE) { "在 IE 中无效 alert("Node is an element.”；

}

这个例子比较了 someNode.nodeType与Node.ELEMENT_NODE常最。如果二者相等，则意味着 someNode确实是一个元素。然而，由于IE没有公开Node类型的构造函数，因此上面的代码在IE中 会导致错误。为了确保跨浏览器兼容，嚴好还是将nodeType属性与数字值进行比较，如下所示：

if (someNode.nodeType == 1) {    //适用于所有浏览苏

alert ("Node is an element,);

并不是所有节点类型都受到Web浏览器的支持。开发人员最常用的就是元素和文本节点。本章后 面将详细讨论每个节点类型的受支持情况及使用方法。

\1.    nodeName 和 nodeValue 属性

要了解节点的具体信息，可以使用nodeName和nodeValue这两个属性。这两个属性的值完全取 决于节点的类锄。在使用这两个值以前，最好是像下面这样先检测-下节点的类型。

if (someNode.nodeType == 1){

value = soroeNode.nodeName；    //nodeName 的值是元余的标签名

}

在这个例子中，首先检查节点类型，看它是不是一个元素。如果是，则取得并保存nodeName的值。 对于元素节点，nodeName中保存的始终都是元索的标签名，而nodeValue的值则始终为null。

\2.    节点关系

文档中所有的节点之间都存在这样或那样的关系。节点间的各种关系可以用传统的家族关系來描 述，相当于把文档树比喻成家谱。在HTML中，可以将＜body＞元素看成是素的子元索；相应 地，也就可以将＜html＞元素番成是＜body＞元素的父元素。而＜head＞元素，则卩I以看成是＜130(^＞元索 的同胞元素，因为它们都是同一个父元素＜1^!^＞的直接子元素。

10

 

每个节点都有一个childNodes屈性，其中保存着一个NodeList X才象。NodeList是一种类数组 对象，用于保存一组有序的节点，可以通过位置来访问这驻节点。请注意，虽然可以通过方括号语法来 访问NodeList的值，而且这个对象也有length属性，但它并不是Array的实例„ NodeList对象的 独特之处在于，它实际上是基于DOM结构动态执行査询的结果，因此DOM结构的变化能够自动反映 在NodeList对象中。我们常说，NodeList是棺也命、有呼吸的对象，而不是在我们第一次访问它们 的某个瞬间拍摄下来的_张快照。

下面的例子展示了如何访问保存在NodeList中的节点——可以通过方括号，也可以使用itemO 方法。

var firstChild - someNode.childNodes10]; var secondChild = someNode.childNodes.item(l); var count = someNode.childNodes.length;

无论使用方括号还是使用item (>方法都没有问题，但使用方括号语法看起来与访问数组相似，因 此颇受一些开发人员的贵睐。另夕卜，要注意length属性表示的是访问NodeList的那一刻，其中包含 的节点数量。我们在本书前面介绍过，对arguments对象使用Array .prototype, slice ()方法可以 将其转换为数组。而采用同样的方法，也町以将NodeList对象转换为数组。来看下面的例子：

//在IE8及之钫版本中无效

var arrayOfNodes = Array.prototype.slice.cal1(someNode.childNodes,0);

除[E8及更早版本之外，这行代码能在任何浏览器中运行。由于IE8及更早版本将NodeList 实现为一个COM对象，而我们不能像使用JScript对象那样使用这种对象，冈此上面的代码会导致 错误。耍想4:IE中将NOdeLiSC转换为数组，必须手动枚举所有成员。下列代码在所有浏览器中都 可以运行：

function convertToArray(nodes){ var array = null;    .

try (

array = Array.prototype. slice.call (nodesr 0); //针对非 IE 浏览器 } catch (ex) {

array = new Array();

for (var i=0, len=nodes.length； i < len； i++){ array.push(nodes[i])；

}

}

return array；

}

这个convertToArrayO函数首先尝试了创建数组的最简单方式。如果导致了错误(说明是在 IE8及更M-版本中)，则通过try-catch块来捕获错误，然后手动创建数组。这是另一种检测怪癖的 形式。

每个节点都"ft'—个parentNode属性，该属性指向文档树中的父节点。包含在childNodes列表中 的所有节点都具有相同的父节点，因此它们的parentNode属性都指向同一个节点。此外，包含在 chi ldNodes列表中的每个节点相互之间都是同胞节点。通过使用列表中每个节点的previousSibling 和next Sibling属性，可以访问同一列表中的其他节点。列表中第一个节点的previousSibling属性 值为null,而列表中最后一个节点的nextSibling属性的值同样也为null,如下面的例子所示：

if (someNode.nextsibling === null){

alert ("Last node in the parent's chi ldNodes list.”；

)else if {someNode.previousSibling === null){

alert("First node in the parent's childNodes list.*};

}

当然，如果列表中只有一个节点，那么该节点的nextSibling和previousSibling都为null。

父节点与其第一个和最后一个子节点之间也存在特殊关系。父节点的firstChild和lastChild 属性分别指向其chi ldNodes列表中的第T*和最后一个节点。其中，someNode. firstChild的值 始终等于 someNode.chi 1 dNodes[0]，而 someNode.lastChild 的值始终等于 someNode. childNodes [ someNode. chi ldNodes. length-1 ] 0 在只有一个子节点的情况下，firstChild 和 lastChild指向同一个节点。如果没有子节点，那么firstChild和lastChild的值均为null。明 确这些关系能够对我们査找和访问文档结构中的节点提供极大的便利。图10-2形象地展示了上述关系。

chi丄dNodes

图 10-2

 

在反映这些关系的所有属性当中，childNodes属性与其他属性相比更方便一些，因为只须使用简 单的关系指针，就町以通过它访问文档树巾的任何节点。另外，hasChildNodesO也是一个非常有用 的方法，这个方法在节点包含一或多个子节点的情况下返回true;应该说，这是比査询childNodes 列表的length属性更简单的方法。

所有节点都有的最后一个属性是ownerDocument,该属性指向表示整个文档的文档节点。这种关 系表示的是任何节点都属于它所在的文档，任何节点都不能同时存在于两个或更多个文档中。通过这个 属性，我们可以不必在节点层次中通过层层回溯到达顶端，而是可以直接访问文捫节点。

虽然所有节点类型都继承自Node,但并不是每种节点都有干节点。本章后面将 会讨论不同节点类型之间的差异。

3.操作节点

因为关系指针都是只读的，所以DOM提供了一件操作节点的方法。其中，最常用的方法是 appendChild(), 用于1旬childNodes列表的末尾添加一个节点。添加节点后，childNodes的新增 节点、父节点及以前的最后一个子节点的关系指针都会相应地得到更新。更新完成后，appendChildO 返回新增的节点。来看下面的例子：

10

 

var retumedNode = someNode. appendChi 1 d (newNode) ? alert(returnedNode == newNode);    //true

a1ert(someNode.las tChiId == newNode); //true

如果传人到appendChildO中的节点已经是文档的一部分丁，那结果就是将该节点从原来的位置 转移到新位置。即使可以将DOM树看成是由一系列指针连接起来的，但任何DOM节点也不能同时出 现在文档中的多个位置上。因此，如果在调用appendChildO时传人了父节点的第一个子节点，那么 该节点就会成为父节点的最后一个子节点，如T面的例子所示。

/ / someNode有多个子节点

var returnedNode = someNode.appendChiId(someNode.firstChild); alert(returnedNode == someNode.firstChild)；    //false

alert(returnedNode == someNode.lastChild);    //true

如果需要把节点放在childNodes列表中某个特定的位置上，而不是放在末尾，那么可以使用 insertBeforeO方法。这个方法接受两个参数：要插人的节点和作为参照的节点。插人节点后，被插 人的节点会变成参照节点的前一个同胞节点(previousSibling),同时被方法返冋。如果参照节点是 null,则InsertBefore ()与appendChild()执行相同的操作，如下面的例子所示。

//插入后成为最后一个子节点

returnedNode = someNode.insertBefore(newNode, null)； alert(newNode == someNode.lastChild); //true

//插入后成为第一个子节点

var returnedNode someNode.insertBefore(newNode, someNode.firstChiId); alert(returnedNode == newNode);    //true

alert(newNode == someNode.firstChild); //true

//插入到最后一个子节点前面

returnedNode = someNode.insertBefore(newNode, someNode.lastChild)；

alert(newNode == someNode.childNodes[someNode.childNodes.length-2]); //true

前面介绍的appendChild()和insert3efore()方法都只插入节点，不会移除节点。而下面要介 绍的replaceChildG方法接受的两个参数是：要插入的节点和要替换的节点。要替换的节点将由这个 方法返回并从文档树中被移除，同时由要插入的节点占据其位置。来看下面的例子。

//替换弟一个子节点

var returnedNode = someNode.replaceChiId(newNode, someNode.firstChild)；

//替挟最后一个子节点

returnedNode = someNode.replaceChiId(newNode, someNode.lastChild);

在使用replaceChildU插人一个节点时，该节点的所有关系指针都会从被它替换的节点复制过 来。尽管从技术上讲，被替换的节点仍然还在文档中，但它在文裆中已经没有了自己的位置。

如果只想移除而非替换节点，可以使用removeChildO方法。这个方法接受一个参数，即要移除 的节点。被移除的节点将成为方法的返回值，如下面的例子所示。

//移除第一个子节点

var fonnerFirstChild = someNode.removeChiId(someNode.firstChild);

//移除最后一个子节点

var formerLastChild = someNode.removeChiId(someNode.lastChild)；

与使用replaceChi Id <)方法一样，通过removeChi Id (>移除的节点仍然为文约所有，只不过在 文档中已经没相了自己的位置,，

前面介绍的四个方法操作的都是某个节点的子节点，也就是说，要使用这几个方法必须先取得父节 点(使用parentNode域性)。另外，并不是所有类铟的节点都奋子节点，如果在不支持子节点的节点 上调用了这些方法，将会导致错误发生。

4.其他方法

有两个方法是所有类型的节点都有的。第-个就是cloneNodet),用于创述调用这个方法的节点 的一个完全相同的副本。cloneNodeO方法接受一个布尔值参数，表示是否执行深笈制。在参数为true 的情况下，执行深复制，也就是复制节点及其整个子节点树；在参数为false的情况下，执行浅复制， 即只复制节点木身。复制后返回的节点副本属于文档所有，但并没有为它指定父节点。因此，这个节点 副本就成为 r 一个“孤儿”，除非通过 appendChild () s insertBefore <)或 replaceChi Id ()将它

添加到文档中。例如，假设有下面的HTML代码。

<ul>

<li>itera 2</li>

<li>item 3</li>

</ul>

如果我们已经将<ul>元素的引用保存在了变量HVList中，那么通常下列代码就可以看出使用 cloneNodeO方法的两种模式。

var deepList = myList.cloneNode(true);

alert (deepList.childNodes. length) ；    //3 (IE < 9)或 7 (其他浏览器)

var shallowList = myList.cloneNode(false); alert(shallowList.childNodes.length};    //0

在这个例子中，deepList中保存着一个对myList执行深复制得到的副本。因此，deepList中 包含3个列表项，每个列丧项中都包含文本。而变量shallowList中保存肴对myList执行浅复制得 到的副本，因此它不包含子节点。deepList. childNodes . length中的差异主要是因为IE8及更早版 本与其他浏览器处理空白字符的方式不一样。IE9之前的版本不会为空白符创建节点。

cloneNode ()方法不会复制添加到DOM节点中的JavaScript爲性，例如事件处 '理租序等。这个方法只复制特性、(在明确指定的情况下也复制)子节点，其他一切 都不会复制。IE在此存在一个bug,即它会复制事件处理程序，所以我们建议在复制 之前最好先移除事件处理租序。

我们要介绍的最后一个方法是normalize!),这个方法唯一的作用就是处理文档树中的文本节点。 由于解析器的实现或DOM操作等原因，可能会出现文本节点不包含文本，或者接连出现两个文本廿点 的情况。当在某个节点上调用这个方法时，就会在该节点的后代节点中査找上述两种情况。如果找到了 空文本节点，则删除它；如果找到相邻的文本节点，则将它们合并为一个文本节点。本車后面还将进一 步讨论这个方法。

10.1.2 Document 类型

10

 

JavaScript通过Document类型表示文桂=在浏览器中，document对象是HTMLDocument (继承 自Document类塑)的一个实例，表示盤个HTML页面。而fi，document对象是window对象的一个 屈性，因此可以将其作为全局对象來访问。Document节点具有下列特征：

□    nodeType 的值为 9;

□    r.odeName 的值为"#document，；

□    nodeValue 的值为 null;

□    parentNode 的值为 null ；

□    ownerDocument 的值为 null;

□其子节点可能是一个 DocumentType (最多Element (最多-斗)、Processinglnstruction 或 Commento

Document类型可以表示HTML页面或者其他基于XML的文档。不过，最常见的应用还是作为 HTMLDocument实例的document对象。通过这个文档对象，不仅可以取得与页面有关的信息，而丑还 能操作页面的外观及其底层结构。

在Firefox、Safari、Chrome和Opera中，可以通过脚本访问Document类型的构 造函数和原型。但在所有浏览器中都可以访问HTMLDocument类型的构造函数和原型， 包括IE8及后续版本。

1.文档的子节点

然 DOM标准规定 Document 节点的子节点可以是 DocuroentType、Element、Processingln-st ruction或Comment，但还有两个内置的访问子节点的快捷方式。第一个就是document Element 属性，该屈性始终指向HTML页面中的＜11心1＞元素。另一个就是通过childNodes列表访问文档元素， 仴通过documentElement属性则能更快捷、更直接地访问该元素。以下面这个简单的页面为例。

<hcml>

<body> ＜/body＞

＜/html＞

这个页面在经过浏览器解析后，其文档中只包含一个子节点，即＜111^1＞元素。可以通过 document Element或childNodes列表来访问这个元素，如下所示。

var html = document .documentElement;    //取得的幻用

alert(html === document.childNodes[0])；    //true

alert(html === document.firstChild)；    //true

这个例了-说明，document Element f irstChild 和 childNodes [0]的值相同，都指向 ＜html＞ 元素D

作为HTMLDocument的$例，document对象还有一个body属性，直接指向＜body＞元素。因为开 发人员经常要使用这个元素，所以docwnent.body在JavaScript代码中出现的频率非常高，其用法如下。

var body = document.body ；    //取得对＜body＞的？| 用

所有浏览器都支持 document .documentElement 和 document. body 属性。

Document另一个可能的子节点是Document Type。通常将＜!DOCTYPE＞标签看成一个与文构其他

部分不同的实体，可以通过doc type属性(在浏览器中是document. doctype )来访问它的信息。 var doctype = document.doctype；    //取得对＜!DOCTYPE＞的芩1 用

浏览器对document.doctype的支持差别很大，可以给出如下总结。

□    IE8及之前版本：如果存在文档类型声明，会将其错误地解释为一个注释并把它当作Comment 节点；而document.doctype的值始终为null。

□    IE9+及Firefox:如果存在文档类型声明，则将其作为文档的第一个子节点；document.doctype 是一个 Document Type 节点，也可以通过 document. f irstChild 或 document. childNodes [0] 访问同一个节点。

□    Safari, Chrome和Opera:如果存在文杓类型声明，则将其解析，但不作为文档的子节点。document. doctype是一个DocumentType节点，但该节点不会出现在document. childNodes中。

由于浏览器对document .doctype的支持不一致，因此这个属性的用处很有限。

从技术上说，出现在<htmb元素外部的注释应该算是文档的子节点。然而，不同的浏览器在是否

解析这些注释以及能否正确处理它们等方面，也存在很大差异。以下面简单的HTML页面为例。

<!-■'第一条注释-->

<html>

<body>

</body>

<!--第二条注释—>

看起来这个页面应该有3个子节点：注释、<html>元素、注释。从逻辑上讲，我们会认为 document.childNodes中应该包含与这3个节点对应的3项。何是，现实中的浏览器在处理位于 <html>外部的注释方而存在如下差异。

□    IE8及之前版本、Safari 3.1及更髙版本、Opera和Chrome只为第，•条注释创建节点，不为第二 条注释创建节点。结果，第一条注释就会成为document, chi ldNodes中的第一个子节点。

□    IE9及更髙版本会将第一务注释创建为document. childNodes中的一个注释节点，也会将第 二条注释创建为document. chi ldNodes中的注释子节点。

□    Firefox以及Safari 3.1之前的版本会完全忽略这两条注释。

同样，浏览器间的这种不一致性也导致f位F<htrnl>元素外部的注释没有什么用处。

多数情况下，我们都用不着在document对象h调用appendChild（>、removeChild （>和 replaceChildO方法，因为文档类型（如果存在的话）是只读的，而且它只能有一个元索子节点（该 节点通常早就已经存在了）。

2.文档信息

作为HTMLDocument的一个实例，document对象还有一些标准的Document对象所没有的属性。 这些属性提供了 document对象所表现的网页的一些信息。其中第一个属性就是title,包含着 <t i t ：^>元素中的文本——显示在浏览器窗口的标题栏或标签页上。通过这个属性可以取得当前页面的 标题，也可以修改当前页面的标题#反映在浏览器的标题栏中。修改title属性的值不会改变<title> 元素。来看下面的例子。

//肷得文桔标題

var originalTitle = document.title;

10

 

〃设置文桔标题

document.title = "New page title";

接下来要介绍的3个属性都与对网页的涛求有关，它们是URL、domain和referrer。URL腐性 中包含页面究整的URL （即地址栏中显示的URL）, domain属性中只包含贞面的域名，而referrer 属性中则保存着链接到当前页面的那个页面的URL。在没有来源页面的情况下，referrer属性中可能 会包含空字符串。所有这些信息都存在于请求的HTTP头部，只不过是通过这些属性让我们能够在 JavaScrip中访问它们而已，如下面的例子所示。

//馭得完整的URL

var url = document-URL；

//取挦域名

var domain = document.domain；

//取得来源页面的URL

var referrer = document. ref.errer;    ,

URL 与 domain 屈性足•相瓦关联的。例如，如果 document.URL 等于 <http://www.wrox.com/WileyCDA/>, 那么 document. domain 就等于 www.wrox.com。

在这3个属性中，只有domain是可以设置的。但由于安全方面的限制，也并非可以给domain设 置任何值。如果URL中也含一个子域名，例如p2p.wrox.com，那么就只能将domain设置为"wrox.com” (URL中包含11 www",如www.wrox.com时，也是如此)。不能将这个属性设覚为URL中不包含的域， 如下面的例子所示。

//假设页面来自p2p.wrox.com域

document.domain = **wrox.com" ；    // 成功

document .domain = ■nczonline.net" ；    // 出错！

当页面中包含来fj其他子域的框架或内嵌框架时，能够设置document, domain就非常方便了。由 于跨域安全限制，来自不同子域的页面无法通过JavaScript通信。而通过将毎个页面的 document. domain没S为相同的值，这些页谢就可以互相访问对方包含的JavaScript对象了。例如， 假设有-个页面加栽ft [www.wrox.com](http://www.wrox.com),    中包含一个内嵌框架，框架内的页面加载自p2p.wox.com。

由于document.domain字符申不一样，内外两个页面之间无法相互访问对方的JavaScript对象。但如 果将这两个页面的document. domain值都设S为"wrox. com",它们之间就可以通信了。

浏览器对domain属性还看一个限制，即如果域名一开始是“松散的” (loose ),那么不能将它再设 置为“紧翊的”(tight)。换句话说，在将document .domain没置力” wrox. com •之后，就不能再将其 设置回-p2p.wrox.com-,否则将会导致错误，如下面的例子所示s

//假设页面来自于p2p.wrox.com域

document .domain = "wrox.com":    //松教的(成功)

document.domain = ■ p2p.wrox.com°;    //紧瑚的(出错！)

所有浏览器中都存在这个限制，但IE8是实现这一限制的最早的IE版本„

3.查找元素

说到最常见的D0M应用，恐怕就要数取得特定的某个或某组元素的引用，然后再执行--些操作了。 取得元素的操作可以使用document对象的几个方法来完成。其中，Document类型为此提供了两个方 法：getElementBy工d()和 getElementsByTagName()□

第一个方法，getElementByldO ,接收一个参数：要取得的元索的ID。如果找到相应的元素则 返回该元素，如果不存在带存相应ID的元索，则返囬null。注意，这里的1D必须与贞面中元素的id 特性(attribute)严格匹配，包括大小写。以下而的元素为例。

<div id= HmyDivM>Some text</div>

可以使用卩面的代码取得这个元素：

var div = document.getElementById("myDiv")；

//取俘<div>元素的引用

 

但是，下面的代码在除IE7及更早版本之外的所有浏览器中都将返回null。

var div = document. getElementById ( "mydiv") ；    //无效的 ID (在 IE7 及更早版本中 V以)

IE8及较低版本不区分ID的大小写，因此-myDiv•’和-mydiv•会被当作相同的元素ID。

如果页面中多个元素的ID值相同，getElementByldO只返回文档中第一次出现的元素。IE7及较

低版本还为此方法添加了一个有意思的“怪癖”：name特性与给定ID匹配的表单元素(<input>、 <textarea>、<buttons^<select> )也会被该方法返回。如果右哪个表单元素的name特性等于指 定的ID,而且该元素在文档中位f带有给定ID的元素前面，那么IE就会返冋那个表单元素。来看下面 的例子。

<input type="text" name="myElement* value="Text field">

<div id="myElement">A div</div>

基于这段 HTML 代码，在 IE7 中调用 document .getElementByld ("myElement ")，结果会返 元素；而在其他所有浏览器中，都会返回对<div>元素的引用。为了避免IE中存在的这个问

题，最好的办法是不让表单字段的name特性与其他元素的ID相同。

另一个常用于取得元素引用的方法是getElementsByTagNameO。这个方法接受一个参数，即要

取得元素的标签名，而返回的是包含零或多个元素的NodeList。在HTML文档中，这个方法会返回一 个HTMLCollection对象，作为一个“动态”集合，该对象与NodeList非常类似。例如，下列代码 会取得页面中所有的<img>元素，并返网一个HTMLCollection。

var images = document.getElementsByTagName{*img");

这行代码会将一个HTMLCol lection对象保存在images变量中。与NodeList对象类似，可以 使用方括号语法或item()方法来访问HTMLCollection对象中的项。而这个对象中元素的数量则可以 通过其length属性取得，如下面的例子所示。

alert {images . length) ；    //檢出图像的致量

alert {images (0] .src);    //徐出第一个图像元索的src特性

alert (images. item(0) .src);    //掩出第一个图像元索的src特性

HTMLCollection对象还有一个方法，叫做namedltem(),使用这个方法可以通过元素的name 特性取得集合中的项。例如，假没上面提到的贞面中包含如下<img>元素：

<img src="myimage.gif" name="myImage■>

那么就可以通过如下方式从images变贵中取得这f<img>元素： var mylmage = images.namedltem("myImage");

10

 

在提供按索引访问项的基础上，HTMLCollection还支持按名称访问项，这就为我们取得实际想要 的元索提供了便利。而旦，对命名的项也町以使用方括号语法来访问，如下所示：

var mylmage = images [ ■Tnylmage* ]:

对HTMLCollection而言，我们可以向方括号中传人数值或字符串形式的索引值。在后台，对数 值索引就会调用icem(),而对字符串索引就会调用naraedltemO。

要想取得文档中的所有元素，可以向getElementsByTagName ()中传人• * •。在JavaScript及CSS 中，星号(* )通常表示“全部”。下面看一个例子。

var allElements = doctunent. getElementsByTagName (■*");

仅此一行代码返冋的HTMLCollection中，就包含了整个页面中的所有元素一一按照它们出现的 先后顺序。换句话说，第-项是＜html＞元素，第二项是＜head＞元索，以此类推。由于IE将注释(Comment ) 实现为元素(Element ),因此在IE中调用getElementsByTagName ("*■)将会返回所存注释节点。

(^\    虽然标准规定标签名需要区分大小写，但为了最大限度地与既有HTML页面兼

容，传给getElementsByTagName()的标签名是不需■要区分大小写的。但对于XML 页面而言(包括XHTML ) , getElementsByTagName()方法就会区分大小写。

第三个方法，也是只有HTMLDocument类型才有的方法，是getElementsByName ()。顾名思义， 这个方法会返回带有给定name特性的所有元素。最常使用getElementsByNair.e ()方法的悄况是取得 单选按钮；为了确保发送给浏览器的值正确无误，所有单选按钮必须具有相同的name特性，如下面的 例子所示。

<fieldset>

<legend>Which color do you prefer?<Zlegend> <ul>

<li><input <label

 

type="radio" value=wred" name="color" id=■colorRed"> f or= " colorRed">Red</labelx/li>

id="colorGreen">

 

<li><input type=■radio" value="green" name=McolorH <label for=*colorGreen">Green</labelx/li>

<li><input 〈label

 

type="radio" value="blue" name="color" id=BcolorBlue*> for="colorBlue">Blue</labelx/li>

</ul>

</fieldset>

如这个例子所示，K中所有单选按钮的name特性值都是-color'但它们的ID可以不同。ID的 作用在于将＜label＞元索应用到每个单选按钮，而name特性则用以确保三个值中只有一个被发送给浏 览器。这样，我们就可以使用如下代码取得所有单选按钮.•

var radios = document.getElementsByName{"color")；

与 getElementsByTagName。类似，getElernentsByName()方法也会返回一个 HTMLCollectioin0 但是，对于这里的单选按钮来说，namedltemO方法则只会取得第一项(因为每一项的name特性都相同)。

4.特殊集合

除了属性和方法，document对象还有-•辟特殊的集合。这些集合都是HTMLCollection对象， 为访问文档常用的部分提供了快捷方式，包括：

□    document.anchors,包含文档中所有带name特性的＜a＞元素；

口 document. applets,包含文样中所存的〈applet＞元素，因为不再推荐使用〈applet＞元素， 所以这个集合已经不建议使用了；

□    document, forms,包含文约中所有的 ＜£。道＞兀素，与 document .getElementsByTagName (* form") 得到的结果相同；

□    document. images,包含文档中所有的＜img＞元素，与 document .getElementsByTagName ("img")得到的结果相同；

□    document. links,包含文钓中所有带href特性的＜a＞元素。

这个特殊集合始终都可以通过HTMLDocument对象访问到，而H.，与HTMLCol lection对象类似， 集合中的项也会随着当前文档内弃的更新而史新。

5. DOM —致性检測

由于DOM分为多个级别，也包含多个部分，因此检测浏览器实现了 DOM的哪邱部分就分必要 了。document. implementation屈性就是为此提供相应信息和功能的对象，与浏览器对DOM的实现 直接对应。DOMI级只为document. implementation规定r一个方法，即hasFeature ()。这个方 法接受两个参数：要检测的DOM功能的名称及版本号。如果浏览器支持给定名称和版本的功能，则该 方法返回true,如下面的例子所示：

var hasXmlDom = document.implementation.hasFeature("XML", "1.0")； 下表列出了可以检测的不同的值及版本号。

| 功    能       | 版本号        | 说    明                                           |
| -------------- | ------------- | -------------------------------------------------- |
| Core           | 1.0、2.0、3.0 | 基本的DOM,用于描述表现文档的节点树                 |
| XML            | 1.0、2.0、3.0 | Core的XML扩展，添加了对CDATA、处理指令及实体的支持 |
| HTML           | 1.0、2.0      | XML的IITML扩展，添加了对HTML特有元素及实体的支持   |
| Views          | 2.0           | 基于某些样式完成文档的格式化                       |
| StyleSheets    | 2.0           | 将样式表乂联到文档                                 |
| CSS            | 2.0           | 对层免样式表1级的支持                              |
| CSS2           | 2.0           | 对层叠样式表2级的支持                              |
| Events         | 2.0, 3.0      | 常规的DOM事件                                      |
| UIEvents       | 2.0, 3.0      | 用户界面事件                                       |
| MouscEvents    | 2.0, 3.0      | 由鼠标引发的事件(click、mouseover等)               |
| MutationEvcnts | 2.0, 3.0      | DOM树变化时引发的事件                              |
| HTMLEvents     | 2.0           | HTML4.01 事件                                      |
| Range          | 2.0           | 用于操作DOM树屮某个范围的对象和方法                |
| Traversal      | 2.0           | 遍历DOM树的方法                                    |
| LS             | 3.0           | 文件与DOM树之间的同步加栽和保存                    |
| LS-Async       | 3.0           | 文件与DOM树之间的异步加载和保存                    |
| Validation     | 3.0           | 在确保有效的前提下修改DOM树的方法                  |

尽管使用hasFeatureO确实方便，但也有缺点。因为实现者可以自行决定是否与DOM规范的不 同部分保持一致。事实上，要想让hasFeartureO方法针对所有值都返回true很容易，但返回true 有时候也不意味着实现与规范一致。例如，Safari 2_x及更早版本会在没有完全实现某些DOM功能的情 况下也返回true。为此，我们建议多数情况下，在使用DOM的某典特殊的功能之前，最好除了检测 hasFeature(>之外，还同时使用能力检测。

10

 

6.文档写入

有一个document对象的功能已经存在很多年了，那就是将输出流写人到网页中的能力。这个能力 体现在下列 4 个方法中：write() x writelnO、open<)和 close ()。其中，write{)和 writelnG 方法都接受一个字符串参数，即要写入到输出流中的文本。write()会原样写人，而writelnU则会 在字符串的末尾添加一个换行符(\n)。在页面被加载的过程中，可以使用这两个方法向页面中动态地 加人内容，如下面的例子所示。

<html>

<head>

<Litle>document.write() Example</title>

</head>

<body>

<p>The current date and time is:

<script type="text/javascript">

document .write ("<strong>" + (new Date () ) . toString () + M</strong>'t｝；

</script>

</P>

</body>

</html>

•    Document WriteExampleOl. htm

这个例子展示了在页面加载过程中输出当前日期和时间的代码。其中，日期被包含在一个＜strOng＞ 元素中，就像在HTML页面中包含普通的文本一样。这样做会创建一个DOM元素，而且可以在将来访 问该元素。通过write U和writeln (＞输出的任何HTML代码都将如此处理。

此夕卜，还可以使用write()和writeln( ＞方法动态地包含外部资源，例如JavaScript文件等。在包 含JavaScript文件时，必须注意不能像下面的例子那样直接包含字符串、＜/SCript＞＞,因为这会导致该 字符串被解释为脚本块的结束，它后面的代码将无法执行。

<html>

<head>

<title>document.write() Example 2</title>

</head>

<body>

<script type="text/javascript">

document.write("<script type=：\"text/javascript\" src=\"file.js\">" + ■</script>">；

</script>

</body>

Document 而 teExampleO2. litm

即使这个文件看起来没错，但字符串”＜/script＞-将被解释为与外部的＜script＞标签匹配，结果 文本”)；将会出现在页面中。为避免这个问题，只需把这个字符串分开写即可；第2章也曾经提及这个 问题，解决方案如下。

<html>

<head>

<title>document.write() Example 3</title>

</head>

<body>

<script type="text/javascript">

document.write(*<script type=\"text/javascriptsrc=\"file.js\+ "<\/Bcript>");

</script>

</body>

</html>

Document Wr“eExample03. htm

字符串•<Vscripo1•不会被当作外部<script>标签的关闭标签，因而页面中也就不会出现多余 的内容了。

前面的例子使用document.write(>在页面被呈现的过程中直接向其中输出了内容。如果在文档 加载结束后再调用document.write()，那么输出的内容将会重写整个页面，如下面的例子所示：

<html>

<head>

<title>document.write() Example 4</title>

</head>

<body>    •

<p>This is some content that you won.'t get to see because it will be overwritten.</p> <script type=*text/javascript

window.onload = function(H

document.write(*Hello world!"};

};

</script>

</body>

</html>

Document WriteExample04. htm

在这个例子中，我们使用了 window.onload事件处理程序(事件将在第13章讨论)，等到页面完 全加载之后延迟执行函数。函数执行之后，字符串-Hello world! •会重写整个页面内容。

方法open()和close ()分别用于打开和义闭网页的検出流。如果是在页曲_加载期f日j使用write () 或wricelnO方法，则不需要用到这两个方法。

戸格型XHTML文梢不支持文档写入。对于那些按照application/xml+xhtml 内容类型提供的页面，这两个方法也同样无效。

10.1.3 Element 类型

除了 Document类型之外，Element类型就要算是Web编程中最常用的类型了。Element类型用 于表现XML或HTML元素，提供了对元素标签名、子节点及特性的访问。Element节点具有以下特征：

□    nodeType 的值为 1;

□    nodeNane的值为元素的标签名；

10

 

□    nodeValue 的值为 null;

□ parentNode 可能是 Document 或 Element;

O 其子节点可能是 Element、Text、Comment、Processing工nstruction、CDATASection 或

EntityRe ferenc e o

要访问元素的标签名，可以使用nodeName属性，也可以使用tagName属性；这两个属性会返冋 相同的值(使用后者主要是为了清晰起见)。以下面的元索为例：

<div id-"myDiv*x/div>

可以像下面这样取得这个元素及其标签名:

var div = document.getElementById("myDiv");

alert(div.tagName)；    //"DIV"

alert(div.tagName == div.nodeName)； //true

这里的元素标签名是div，它拥有一个值为"myDiv•的ID。可是，div.tagName实际上输出的是 -DIV-而非-div’％在HTML中，标签名始终都以全部大写表示；而在XML （有时候也包括XHTML ） 中，标签名则始终会与源代碑中的保持一致。假如你不确定自己的脚本将会在HTML还是XML文档中 执行，最好是在比较之前将标签名转换为相同的大小写形式，如下面的例子所示：

if {e 1 ement. tagName = = "div"） { //不能这抖比校，很落易出域！ //在此执行果些操作

}

if （element. tagName.toLo^zerCase （） -= "divM）{ //这样袭好（适用于任何文核） //在此执行某些操作

）

这个例子展示了围绕tagName属性的两次比较操作。第一次比较非常容易出错，因为其代码在 HTML文档中不管用。第二次比较将标签名转换成丁全部小写，是我们推荐的做法，因为这种做法遥用 于HTML文捫，也适用于XML文档。

可以在任何浏览器中通过脚本访问Element类型的构造函数及原型，包括圧8及 之前版本.在Safari2之前版本和Opera8之前的版本中，不能访问Element类型的构 造函数。

1.HTML元素

所有HTML元素都由HTMLElement类型表示，不是直接通过这个类型，也是通过它的子类型来表 示。HTMLElement类型直接继承Element并添加了一些属性。添加的这些属性分别对应于每个HTML 元素中都存在的下列标准特性。

□    id,元索在文档中的唯一标识符。

□    title,有关元素的附加说明信息，一般通过工具提示条显示出来。

□    lang,元素内容的语言代码，很少使用。

□    dir,语言的方向，值为-ltr* （ left-to-right,从左至右）或”rtl" （ right-to-left,从右至左）， 也很少使用。

□    className,与元素的class特性对应，即为元素指定的CSS类。没有将这个属性命名为class, 是因为class是ECMAScript的保留字（有关保留字的信息，请参见第1章）。

上述这些属性都可以用来取得或修改相应的特性值。以下面的HTML元素为例：

<div id="myDiv" class="bd" title="Body text" lang="en- dir="ltr"x/div>

HTMLElementsExampleOl .htm

元素中指定的所有信息，都可以通过下列JavaScript代码取得:

var div = document.getElementByld("myDiv")；

""myDivM" //■bd"

//"Body text" //"en"

 

alert{div.id); alert(div.className); alert(div.title); alert(div.lang)； alert(div.dir)；

当然，像下面这样通过为每个属性赋予新的值，也可以修改对应的每个特性:

div.id = "someOtherld"/ div.className = "ft"; div.title = "Some other text"j div.lang = "fr"; div.dir =*rtl"；

 

IfTMLElementsExampleOJ. him

并不是对所有属性的修改都会在页面中直观地表现出来。对id或lang的修改对用户而言是透明 不可见的（假设没有基于它们的值设置的CSS样式），而对title的修改则只会在鼠标移动到这个元素 之上时才会迠示出来。对dir的修改会在属性被重写的那一刻，立即影响页面中文本的左、右对齐方式。 修改className时，如果新类关联了与此前不同的CSS样式，那么就会立即应用新的样式。

前面提到过，所有HTML元素都是由HTOLElement或者其更具体的子类型来表示的。下表列出了 所有HTML元素以及与之关联的类型（以斜体印刷的元紊表示已经不推荐使用了＞。注意，表中的这些 类型在Opera、Safari, Chrome和Firefox中都可以通过JavaScript访问，但在IE8之前的版本中不能通 过 JavaScript 访问。

 

| 元    素   | 类    型                  | 元    索 | 类    型              |
| ---------- | ------------------------- | -------- | --------------------- |
| A          | HTMLAnchorElement         | EM       | HTMLElement           |
| ABBR       | HTMLElement               | FIELDSET | H*TMLFieldSet Element |
| ACRONYM    | HTMLElement               | FONT     | HTMI.Fon tF.l en?ent  |
| ADDRESS    | HTMLElement               | FORM     | HTMLFormElement       |
| APPLET     | HTMLAppl eCEl emenC       | FRAME    | HTMLFrameElement      |
| AREA       | HTMLAreaEleraent          | FRAMESET | HTMLFrajneSetEleroent |
| B          | HTMLElement               | Hl       | HTMLHeadingElement    |
| BASE       | IITMLBa seE l.ement       | H2       | HTMI.HeadingElement   |
| BASEFONT   | tITMLBas e Font El emen t | H3       | HTMLHeadingElemen t   |
| BDO        | HTMLElement               | H4       | HTMLHeadingElement    |
| BIG        | HTMLElement               | H5       | HTMLHeadingElement    |
| BLOCKQUOTE | HTMLQuoteElement          | H6       | HTMLHeadingElement    |
| BODY       | HTMLBodyElement           | HEAD     | HTMLHeadElement       |
| BR         | HTWLBRElement             | HR       | HTMLHRElement         |
| BUTTON     | HTMLButtonElement         | HTML     | HTMLHtmlElement       |
| CAPTION    | HTMLTableCapt i onElement | I        | HTMLElement           |
| CENTER     | HTMLElement               | IFRAME   | HTMLIFrameElement     |
| CITE       | HTMLElement               | IMG      | HTMLTmageElement      |
| CODE       | HTMLElement               | INPUT    | HTMLInputElement      |
| COL        | HTMLTableColEleraent      | INS      | HTMLModElement        |
| COLGROUP   | HTMLTableColElement       | IS1NDEX  | HTMLIsIndexE2ement    |
| DD         | HTMLElement               | KBD      | HTMLElement           |
| DEL        | HTMLModElemGiit           | LABEL    | HTMLLabelElement      |
| DFN        | HTMLElement               | LEGEND   | HTMljLegendEl ement   |
| DIR        | HTMbDi rectory El ement   | LI       | HTMLLIE1ement         |
| DIV        | HTMLDivElement    '       | LINK     | HTMLLinkElement       |
| DL         | HTMLDLi stElemeut         | MAP      | HTMLMapEl ement.      |
| DT         | HTMLElement               | MENU     | HTMLMenuElement       |

 

10

 

(续)

| 元    索 | 类    型             | 元    素 | 类    型                  |
| -------- | -------------------- | -------- | ------------------------- |
| META     | HTMLMetaElement      | STRONG   | HTMLElement               |
| NOFRAMES | HTMLElement          | STYLE    | KTMLStyleElement          |
| NOSCRIPT | HTMLElement          | SUB      | HTMLElement               |
| OBJECT   | HTMLObjectElement    | SUP      | HTMLElement               |
| OL       | HTMLOListElement     | TABLE    | HTMLTableElement          |
| OPTGROUP | HTMLOptGroupElement  | TBODY    | HTMLTableSectionEleirient |
| OPTION   | HTKLOptionElement    | TD       | HTMLTableCellElement      |
| P        | HTMLParagraphElement | TEXTAREA | HTMLTextAreaElement       |
| PARAM    | HTMLParamEleir.Gnt   | TFOOT    | HTMLTableSectionElement   |
| PRE      | HTMLPreElement       | TH       | HTMLTableCellElement      |
| Q        | HTMLQuoteElement     | THEAD    | HTMLTableSectionElement   |
| S        | HTMLElement          | TITLE    | HTMLTitleElement          |
| SAMP     | HTMLElement          | TR       | HTMLTableRowElement       |
| SCRIPT   | HTMLScriptElement    | TT       | HTMLElement               |
| SELECT   | HTMLSelectElement    | U        | HTMLElement               |
| SMALL    | HTMLElement          | UL       | HTMLUListE1ement          |
| SPAN     | HTMLElement          | VAR      | HTMLElement               |
| STRIKE   | HTMLEi ement.        |          |                           |

表屮的每一种类型都有与之相关的特性和方法。本书将会讨论其中很多类型。

2取得特性

每个元素都冇一或多个特性，这些特性的用途是给出相应元素或其内容的附加信息。操作特性的 DOM方法主要有三个，分另［j是 getAttribute (>、setMtribute ()和 removeAttribute ()。这三 个方法可以针对任何特性使用，包括那狴以HTMLElement类沏属性的形式定义的特性。来看下面的例子：

var div = document.getElementById("myDiv")； alert(div.ge仁Attribute("idn));    //"myDiv"

alert{div.getAttribute("class*));    //"bd"

alert (div.getAttribute ("title*)) ;    //"Body text，'

alert(div.getAttribute("lang")}；    //"en™

alert(div.getAttribute("dir"));    //"ltr"

注意，传递给getAttributeG的特性名与实际的特性名相同。因此要想得到class特性值，应 该传人-class••而不是-className",后者只有在通过对象属性访问特性时才用。如果给定名称的特性 不存在，getAttribute<)返回 null:>

通过getAttributeG方法也可以取得自定义特性(即标准HTML语言中没有的特性)的值，以 下面的元索为例：

<div id=*myDiv" my_special_attribute="hello!"></div>

这个元素包含一个名为my_special_attribute的自定义特性，它的值是》hello!，。可以像取 得其他特性一样取得这个值，如下所示：

var value = div.getAttribute ("my_special_attribute-)，-

不过，特性的名称是不区分大小写的，即3D’•和-id，代衷的都是同一个特性。另外也要注意，根 据HTML5规范，自定义特性应该加上data-前缀以便验证。

任何元索的所有特性，也都可以通过DOM元素本身的属性来访问。当然，HTMLElement也会有5 个属性与相应的特性一--对应。不过，只有公认的(非自定义的)特性才会以属性的形式添加到DOM 对象中。以下面的元素为例：

<div id= *myDiv" align="leff my_special_attribute="helloI"></div>

因为id和align在HTML中是<div^J公认特性，因此该元素的DOM对象中也将存在对应的属 性。不过，自定义特性my_special_attribute在Safari、Opera、Chrome及Firefox中是不存在的； 但IE却会为Q定义特性也创建属性，如下面的例子所示：

alert(div.id};    //'myDiv"

alert(div.roy_special_attribute);    //undefined    (IE除夕H

alert(div.align);    //"left"

ElementA ttributesExantpleO2. htm

有两类特殊的特性，它们虽然有对应的属性名，但属性的值与通过getAttributeO返回的值并不 相同。第一类特性就是style，用于通过CSS为元素指定样式。在通过getAttribute ()访问时，返 回的style特性值中包含的是CSS文本，而通过属性来访问它则会返回一个对象。由于style属性是 用于以编程方式访问元素样式的(本章后面讨论)，因此并没有直接映射到style特性。

第二类与众不同的特性是onclick这样的事件处理程序。当在元素上使用时，onclick特性中包 含的是JavaScript代码，如果通过getAttribute()访问，则会返回相应代码的字符串。而在访问 onclick属性时，则会返回一个JavaScript兩数(如果未在元素中指定相应特性，则返回null )。这是 因为onclick及其他事件处理程序属性本身就应该被赋予函数值。

由于存在这些差别，在通过JavaScript以编程方式操作DOM时，开发人员经常不使用getAttri-buteO,而是只使用对象的属性。只有在取得自定义特性值的情况下，才会使用getAttributeO方法。

在IE7及以前版本中，通过geCAttribute ()方法访问style特性或onclick这样 的事件处理特性时，送回的值与属性的值相同。换句话说，getAttribute^style")返 回一个对象，而getAttribute (-onclick->返回一个函数。虽然正8已经修复了这个 bug,但不同正版本问的不一致性，也是导致开发人员不使用get Attribute (>访问HTML 特性的一个原因。

10

 

div.setAttribute("id",

 

div. setAttribute ("class[1](#bookmark2) [2](#bookmark3) div.setAttribute("title" div.setAttribute("lang", div.setAttribute("dir",

 

"someOtherld")；

 

z "ft"｝；

,"Some other text" "fr")；

"rtl")；

 

ElementA ttributesExampleOl. htm

通过setAttributeG方法既可以操作HTML特性也可以操作自定义特性。通过这个方法设置的 特性名会被统一转换为小写形式，即‘，ID»最终会变成》id”。

因为所有特性都足属性，所以直接给属性赋值可以设S特性的值，如下所示。

div. id ss " someOtherld;

div.align = "left"；

不过，像下面达样为DOM元素添加一个自定义的诫性，该域性不会自动成为元素的特性。

div.mycolor = "red";

alert (div.getAttribute("mycolor"), ； //null (IE 除外)

这个例子添加了一个名为mycolor的属性并将它的值设B为-red%在大多数浏览器中，这个属 性都不会自动变成元素的特性，因此想通过getAttributeU取得同名特性的值，结果会返四null。 可是，自定义属性在IE屮会被当作元素的特性，反之亦然。

在1E7及以前版本中，setAttribiite()存在一些异常行为。通过这个方法设置 'c+lass和style特性，没有任何效果，而使角这个方法设置事件处理租序特性时也 一样。尽管到了 IE8才解决这些问题，但我们还是推荐通过属性来设置特性。

要介绍的最后一个方法是removeAttributeU,这个方法用于彻底删除元素的特性。调用这个方 法不仅会清除特性的值，而且也会从元素中完全删除特性，如下所示：

div.removeAttribute("class");

这个方法并不常用，但在序列化DOM元索吋，可以通过它来确切地指定要包含哪些特性。

IE6及以前版本不支持removeAttribute ()。

\4. attributes 属性

Element类型是使用attributes屈性的唯一一个DOM节点类型。attributes诚性中包含一个 NamedNoaeMap,与NodeList类似，也是一个“动态”的集合。元素的每一个特性都由一个Attr节 点表示，每个节点都保存在NamedNodeMap对象中。NamedNodeMap对象拥有下列方法。

□    getNamedltem (name):返回 nodeName 属性等于 name 的节点；

□    removeNamedltem (name):从列表中移除nodeName厲性等于name的节点；

□    setNamedltem(node):向列表中添加节点，以节点的nodeName属性为索引；

□    item (pos):返回位于数字pos位置处的节点。

attributes屈性中包含一系列节点，每个节点的nodeName就是特性的名称，而节点的nodeValue 就是特性的值。要取得元素的id特性，可以使用以下代码。

var id = element.attributes.getNamedltem{"id").nodeValue； 以下是使用方括号语法通过特性名称访问节点的简写方式。

var id - element.attributes[■id"].nodeValue；

也可以使用这种语法來设置特性的值，即先取得特性节点，然后再将其nodevalue设置为新值, 如下所示。

element .attributes [ "id" ] .nodeValue = "someOtherld11;

调用removeNamedltem ()方法与在元素上调用removeAttribute ()方法的效果相同-直接删

除其有给定名称的特性。下面的例子展示丫两个方法间唯一的区别，即removeNamedltenU)返回表示 被删除特性的Attr节点。

var oldAttr = element•attributes.removeNamed工tem(nid"};

最后，setNamedltemG是一个很不常用的方法，通过这个方法可以为元素添加•个新特性，为此 笛要为它传人一个特性节点，如下所示。

element.attributes.setNamedItem{newAttr);

一般来说，由于前面介绍的attributes的方法不够方便，因此开发人员更多的会使用 getAttribute (＞、removeAttribute ()和 set At tribute ()方法 0

不过，如果想要遍历元素的特性，attributes属性倒是可以派上用场。在需要将DOM结构序列 化为XML或HTML字符串时，多数都会涉及遍历元素特性。以下代砰展示了如何迭代元素的每一个特 性，然后将它们构选成name="va2ue* na/ne= "value"这样的字符率格式。

| function outputAttributes(element){ var pairs = new Array(),attrName/attrValue,len; |                                                              |                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- |
|                                                              | for (i=0, len=element.attributes.length; i < len; attrName = element.attributes[i].nodeName； attrValue = element.attributes[i].nodeVaLue? |                                   |
|                                                              | pairs.push(attrName + *=\"" + attrValue + n\"}return pairs.join{"")； | u);                               |
| }                                                            |                                                              |                                   |
|                                                              |                                                              | Element A ttributesExampleO3. htm |

这个函数使用了~个数组来保存名值对，最后再以空格为分隔符将它们拼接起来(这是序列化长字 符串时的一种常用技巧)。通过attributes, length屁性，for循环会遍历每个特性，将特性的名称 和值输出为字符串。关于以上代码的运行结果，以下是两点必耍的说明。

10

 

□针对attributes对象中的特性，不同浏览器返回的顺序不同。这些特性在XML或HTML代 码中出现的先后•顺序，不一定与它们出现在attributes对象屮的顺序一致。

□ IE7及更早的版本会返间HTML元素中所有可能的特性，包括没有指定的特性。换句话说，返 回100多个特性的情况会很常见。

针对IE7及更早版本中存在的问题，可以对上面的函数加以改进，让它只返冋指定的特性。每个特 性节点都有一个名为specified的属性，这个诚性的值如果为true,则意味肴S:么是在HTML中柑 定广相应特性，要么是通过seCAttribute()方法设置丫该特性。在IE屮，所有朱设S过的特性的该 属性值都为false,而在其他浏览器中根本会为这类特性生成对应的特性节点(㈥此，在这种浏览器 中，任何特性节点的specified值始终为true)。改进后的代码如下所承。

function outputAttributes(element){ var pairs = new Array(},

attrName,

attrValue,

i,

len；

for (i = 0, len=eleinent. attributes. length; i < len； i++) { attrName = element.attributes[i].nodeName； attrValue = element.attributes[i].nodevalue；

if (element.attributes[1].specified) {

pairs .push(attrName + " = \"M + attrValue + "\M);

}

return pairs.join("");

ElementAttributesExampleO4.htm

这个经过改进的函数町以确保即使在IE7及更早的版本中，也会只返回指定的特性。

5.创建元素

使用document.createElementU方法可以创建新元素。这个方法只接受一个参数，即要创建元 素的标签名。这个标签名在HTML文档中不区分大小马，而在XML (包括XHTML)文档中，则是区 分大小写的。例如，使用下面的代码可以创建一f＜div＞元素。

var div = document.createElement("divn)；

在使用createElement (}方法创建新元素的同时，也为新元索设置了 ownerDocuemnt屈性。此

时，还可以操作元素的特性，为它添加更多子节点，以及执行其他操作。来看下面的例子。

div.id = "myNewDiv"; div.className = "box’；

在新元素上设a这些特性只是给它们赋予了相应的信息。由于新元索尚未被添加到文捫树中，因此 设置这些特性不会影响浏览器的显示。要把新元索添加到文档树，可以使用appendChild＜＞、inser-tBefore O或replaceChildO方法。下面的代朽会把新创建的元素添加到文档的＜1)0＜^＞元素中。

document.body.appendChild(div);

CyeateElementExampleOl .htm

—旦将元索添加到文挡树中，浏览器就会立即呈现该元素。此后，对这个元素所作的任何修改都会 实时反映在浏览器中。

在IE中可以以另一种方式使用createElement (),即为这个方法传人完整的元素标签，也可以包 含属性，如下面的例T所示。

var div = document.createElement("＜div id=\"myNewDiv\" class=\"box\"＞＜/div ＞");

这种方式有助于避开在IE7及更早版本巾动态创建元素的某些问题。下面是已知的一些这类问题。 □不能设置动态创建的＜i frame＞元素的name特性。

□不能通过表单的reset 0方法重设动态创建的＜input＞元素(第13章将讨论reset {)方法)。

□动态创建的Cype特性值为"reset"lW<buttou>兀索爾设不了表傘。

□动态创建的一批name相同的单选按钮彼此毫无关系。name值相同的一组單选按钮本来应该用 于表示同一选项的不同值，但动态创建的一批这种单选按钮之间却没有这种关系。

上述所有问题都可以通过在createSlementO屮指定完整的HTML标签来解决，如下面的例子所示o if (client.browser.ie && client.browser.ie <-7){

//创建一个带name特性的iframe元者

var iframe = document.createElement("<iframe name=\Hmyframe\"></iframe>"J；

//创建input元索

var input = document.createElement('<input type=\"checkbox\">");

//创建button元紫

var button document.createElement{"<button type=\•reset\"></button>")；

//创建单选按钮

var radiol = document .createElement ("<input type=Vradioname=\-choice\" " + -value=\"l \ ">**) j

var radio2 = document.createElement(*<input type=\"radio\" name=\"choice\* " + -value=\n2\">")；

}

与使用createElement ()的惯常方式一样，这样的用法也会返回一个DOM元素的引用、可以将 这个引川添加到文档中，也可以对其加以增强。但是，由于这样的用法要求使川浏览器检测，因此我们 建议只在需要避开IE及更早版本屮上述某个问题的悄况下使川。Jt他浏览器都小'支持这种用法。

6.元素的子节点

元索可以有任意数目的子节点和后代节点，因为元索可以是其他元素的子节点。元素的 ckildNodes属性中包含了它的所有子节点，这些子节点有可能是元素、文本节点、注释或处理指令。 不同浏览器在看待这些节点方面存在显著的不同，以下面的代码为例。

<ul id二*myList">

<li>Itera 3</li>

</ul>

如果是IE来解析这些代W,那么<ul>元岽会有3个子节点，分别是3个<li>元素。但如果是在其 他浏览器中，<ul>元素都会有7个元索，包括3个<li>元索和4个文本节点(表示<li>元素之间的空 白符)。如果像下面这样将元素问的空A符删除，那么所有浏览器都会返回相同数H的子节点。

10

 

<ul i<a="myListB><li>Item l</lixli>Iten 2</lixli>Itero 3</lix/ul>

对于这段代码，仍：1>元素在任何浏览器中都会包含3个-T节点。如果需要通过childNodes属性 遍历子节点，那么一定不要忘记浏览器间的这一差别。这意味着在执行某项操作以前，通常都要先检査 一下nodeTpye屈性，如下而的例子所示o

for (var i=0, len=element.childNodes.length; i < len； i++}{ if (element.chiIdNodes[iJ.nodeType -- 1){

//扶行某些操作

这个例子会循环遍历特定元索的每一个子节点，然后只在子节点的nodeiype等于丨(表示是元素 节点)的悄况下，才会执行某些操作。

如果想通过某个特定的标签名取得子节点或后代节点该怎么办呢？实际上，元素也支持 getElementsByTagName()方法。在通过元素调用这个方法时，除了搜索起点是当前元素之外，其他 方面都跟通过document调用这个方法相同，因此结果只会返回当前元素的后代。例如，要想取得前而 <U1>元素中包含的所有<li>元素，可以使用下列代码。

var ul = document .getElementByld (,fmyList"); var items = ul.getElementsByTagName(*li");

要注意的是，这里的后代中只包含直接子元素。不过，如果它包含更多层次的后代元索，那 么各个层次中包含的<li>元素也都会返回。

10.1.4 Text 类型

文本节点由Text类塑表示，包含的是可以照字面解释的纯文本内容a纯文本中可以包含转义后的 HTML字符，但不能包含HTML代码。Text节点具有以下特征：

□    nodeType 的值为 3;

□    nodeName 的值为”拌text";

□    nodeValue的值为节点所包含的文本；

□    parentNode 是一个 Element;

□不支持(没有)子节点。

可以通过nodeValue属性或data滅性访问Text节点中包含的文本，这两个屈性中包含的值相 同。对nodeValue的修改也会通过data反映出来，反之亦然。便用下列方法可以操作节点中的文本。

□    appendData( text):将text添加到节点的末尾。

□    deleteData (offset, count):从offset指定的位置开始删除count个字符c

口 insertData (offset, text):在 offset 指定的位S插人 text。

□    replaceData (offsetf count, text):用 text 替换从 offset 指定的位置开始到 offset+ count为止处的文本。

□    splitText (offset):从offset.指定的位置将当前文本节点分成两个文本节点。

□    substringData(offset, count):提取从 offset 指定的位置开始到 offset+count 为止 处的字符串。

除了这些方法之外，文本节点还有一个length厲性，保存着节点中字符的数目。而且， nodeValue. length和data, length中也保存着同样的值。

在默认情况下，每个可以包含内容的元素最多只能肴一个文本节点，而旦必须确实有内容存在。来 看几个例子。

<!--没有内容，也就没有文本节点

<div></div>

<!--有空格，因而有一个文本节点-->

<div> </div>

<' ~~有内容，因而有■个丈本节点

<div>Hello World!</div>

上面代码给出的第一个＜(3“＞元素没有内容，因此也就不存在文本节点。开始与结束标签之间只要 存在内容，就会创建一个文本T/点。W此，第二个cdiv^ij素中虽然只包含一个空格，但仍然有一个文 本子节点；文本节点的nodeValue值長一个空格。第三个＜div＞也有一个文本节点，其nodeValue的 值为-Hello World!”。可以使川以下代码来访问这些文本子节点。

var text Node = div. f irstChild；    //或者 div.childNodes [0]

在取得了文本节点的引用后，就可以像K面这样来修改它了。 div.firstChild.nodeValue = "Some other message";

TextNodeExampleO I .htm

如果这个文本节点当前存在亍文档树中，那么修改文本节点的结果就会立即得到反映。另外，在修 改文本节点时还要注意，此时的字符串会经过HTML (或XML,取决于文档类型)编码。换句话说， 小于号、大于号或引号都会像下面的例子一样被转义。

//伶出紹果是"Some &lt; strong&gt ；other&lt; / strong&gt; message"

div.firstChild.nodeValue = "Some <strong>other</strong> message";

TextNodeExample02. htm

应该说，这是在向DOM文档中插人文本之前，先对其•进行HTML编码的一种有效方式。

在IE8、Firefox、Safari、Chrome和Opera中，可以通过脚本访问Text类型的构造 函数和原赉。

1.创建文本节点

可以使用document. createTextNode ()创建新文本节点，这个方法接受个参数-要插人节点

中的文本。与设置已有文本节点的值一样，作为参数的文本也将按照HTML或XML的格式进行编码。

var textNode = document.createTextNode(*<strong>Hello</strong> world!B)；

在创述新文本节点的同时，也会为M•设置ownerDocuraent属性。不过，除非把新节点添加到文样 树中已经存在的节点中，否则我们不会在浏览器窗口中看到新节点。下而的代码会创建一t＜div＞元索 并向其中添加一条消息。

10

 

var element = document.createElement{"div")； element.classNgune = "message"；

var t. ext Node = document. createTextNode (• Hello world! ■)；

el ement.. appendChild (text Node};

document.body.appendChild(element)；

TextNodeExample03. htm

这个例子创建丫一个新《31^＞元素并为它指定f值为-message•的class特性。然后，又创建了 一个文本节点，并将其添加到前面创建的元素中。最后一步，就是将这个元素添加到了文档的＜body＞ 元素中，这样就可以在浏览器中看到新创建的元索和文本节点了。

一般愔况下，每个元素只有一个文本子节点。不过，在某些情况下也可能位含多个文本子节点，如 F面的例子所示。

var element = document .createElement (,,div*')； element.className = "message"；

var textNode = document .createTextNode ("Hello world!”； element.appendChiId(textNode)；

var anotherTextNode = document.createTextNode("Yippee I"); element.appendchiId(anotherTextNode)； document.body.appendChiId(element);

TextNodeExample04 • htm

如果两个文本节点是相邻的同胞节点，那么这两个节点中的文本就会连起来显示，中间不会有空格。

2.规范化文本节点

DOM文档中存在相邻的同胞文本节点很容易导致混乱，因为分不淸哪个文本节点表示哪个字符串。 另外，DOM文档屮出现相邻文本节点的悄况也不在少数，于是就催生了一个能够将相邻文本节点合并 的方法。这个方法是由Node类型定义的（因而在所有节点类型中都存在），名叫normalize 0。如果 在一个包含两个或多个文本节点的父元素上调用normalized方法，则会将所有文本节点合并成一个 节点，结果节点的nodeValue等于将合并前每个文本节点的nodeValue值併接起来的值。来看一个 例子。

var element = document .createElement {11 aiv");

element.className = "message";

var textNode - document.createTextNode("Hello world!"); element.appendChiId(textNode)；

var anotherTextNode = document.createTextNode(nYippee!")； element.appendChild(anotherTextNode);

document.body.appendChiId(element)；

alert(element.childKodes.length);    "2 element.normalize();

alert(element.childNodes.length);    //I

alert(element.firetChiId.nodeValue)t    // "Hello world!Yippee 1"

TexfNodeExample05. htm

浏览器在解析文档时永远不会创建相邻的文本节点。这种情况只会作为执行DOM操作的结果出现。

在某些情况下，执行normalized方法合导致IE6崩溃。不过，在IE6后来的 补丁中，可能已经修复了这个问題（未经证实）。IE7及更高版本中不存在这个问题。

3.分割文本节点

Text类型提供了一个作用与normalize()相反的方法：splitText()0这个方法会将一个文本节 点分成两个文本节点，即按照指定的位置分割nodevalue值。原来的文本节点将包含从开始到指定位 置之前的内容，新文本节点将包含剩下的文本。这个方法会返M—个新文本节点，该节点与原节点的 parenzNode相同。来看下面的例子。

var element = document.createElement(*div")； i element.className = "message"；

var textNode = document.createTextNode("Hello world!"); element.appendChiId(textNode)；

document.body.appendChild(element);

var newNode » element•firstChild.spl.itText (5> ; alert (element. firstChild.nodeValue) /    //"Hello**

alert(newNode.nodeValue)t    //" world!■

alert{element.childKodes.length);    //2

TextNodeExample06. htm

在这个例子中，包含-Hello world!-的文本节点被分割为两个文本节点，从位置5开始。位置5 是“Hello••和-world!-之间的空格，因此原来的文本节点将包含字符串。Hello■,而新文木节点将包 含文本”world!"（包含空格）o

分割文本节点是从文本节点中提取数据的一种常用DOM解析技术。

10.1.5 Comment 类型

注释在DOM中是通过Comment类型来表承的。Comment节点具有下列特征:

□    nodeType 的值为 8;

□    nodeName 的值为■ # comment";

□    nodeValue的值是注释的内容；

□ parentNode 可能是 Document 或 Element;

□不支持（没有）子节点。

Coinnent类型与Text类型继承自相同的基类，因此它拥有除splitText ｛）之外的所有字符串操 作方法。与Text类型相似，也可以通过nodeValue或data属性来取得注释的内容。

10

 

注释节点可以通过其父节点来访问，以下面的代码为例。

<div id=*myDiv**>< !--A comment --></div>

在此，注释节点是<div>元索的--个子节点，W此可以通过下面的代码来访问它。

var div = document. get.ElementByld《"myDiv" };

var comment = div.firstChild；

alert(comment.data)；    //"A comment"

CommentNodeExampleOl. htm

另夕卜，使用document. createCoirunent ()并为其传递注释文本也可以创建注释节点，如下面的例 子所示。

var comment = document. createComment {11A comment ");

敁然，开发人员很少会创建和访问注释节点，因为注释节点对算法鲜有影响。此外，浏览器也不会 识别位标签后面的注释。如果要访问注释节点，一定要保证它们是＜html＞元素的后代(即 位于＜肚!111＞和＜/]11：1111＞之间)。

在Firefox、Safari、Chrome和Opera中，可以访问Comment类型的构造函数和 原型。在IE8中，注释节点被视作标签名为。！《的元素。也就是说，使用 getElementsByTagNamcH )可以取得注释节点。尽管IE9没有把注释当成元素，但 它仍然通过一个名为HTMLCommentElement的构造函数来表示注释。

10.1.6 CDATASection 类型

CDATASection类型只针对基于XML的文档，表示的是CDATA区域。与Comment类似， CDATASection类型继承自Text类型，因此拥有除splitText ()之外的所有字符串操作方法。 CDATASection节点具有下列特征：

□    nodeType 的值为 4;

□    nodeKame 的值为” #cdata-section";

□    nodevalue的值是CDATA区域中的内容；

□    parentNode 可能是 Document 或 Element;

□不支持(没有)子节点。

CDATA区域只会出现在XML文档中，因此多数浏览器都会把CDATA区域错误地解析为Comment 或Element。以下面的代码为例：

＜div id-',niyDiv"＞＜i [CDATA[This is some concent.) ] ＞＜/div＞

这个例子中的＜div＞元索应该包含一个CDATASection节点。可是，四大主流浏览器无一能够这样 解析它。即使对于有效的XHTML页面，浏览器也没有正确地支持嵌人的CDATA区域。

在真正的XML文®中，可以使用document .createCDataSection (}来创建CDATA区域，只需 为其传人节点的内容即可。

在Firefox、Safari、Chrome和Opera中，可以访问CDATASection类型的构造函 数和原型。IE9及之前版本不支持这个类型。

10.1.7 DocumentType 类型

DocumentType类型在Web浏览器中并不常用，仅有Firefox、Safari和Opera支持它Document -

① Chrome 4.0 也支持 Documentiype 类塑。

Type包含着与文档的doctype有关的所有信它具有下列特征：

□    nodeType 的值为 10;

□    nodeName的值为doctype的名称；

□    nodeValue 的值为 null;

□    parentNode 是 Document;

□不支持(没有)子节点。

在OOM1级中，DocamentType对象不能动态创建，而只能通过解析文档代码的方式来创建。支 持它的浏览器会把Document Type对象保存在document. doctype中。DOMI级描述了 DocumentType对象的3个属性：name、entities和notations。其中，name表示文档类型的名称； entities是由文档类型描述的实体的NamedNodeMap对象；notations是由文辫类型描述的符号的 NamedNodeMap对象。通常，浏览器中的文档使用的都是HTML或XHTML文档类型，因而entities 和notations都是空列表(列表中的项来自行内文格类型声明)。但不管怎样，只有name属性是有用 的。这个属性中保存的是文档类型的名称，也就是出现在<! DOCTYPE之后的文本。以下面严格型HTML 4.01的文档类型声明为例：

<!DOCTYPE HTML PUBLIC •-//W3C//DTD HTML 4.01//EN" nhttp：//www.w3.org/TR/html4/strict,dtd">

DocumentType的name属性中保存的就是"HTML•: alert(document.doctype.name)；    //"HTML"

IE及更早版本不支持DocumentType，因此document .doctype的值始终都等于null。可是， 这些浏览器会把文档类型声明错误地解释为注释，并且为它创建一个注释节点。IE9会给 document.doctype赋正确的对象，但仍然不支持访问DocumentType类型。

10.1.8 DocvimentFragment 类型

在所有节点类型中，只有DocumentFragment在文档中没有对应的标记。DOM规定文档片段 (document fragment)是种“轻董级”的文档，可以包含和控制节点，但不会像完整的文档那样占用 额外的资源。DocumentFragment节点具有下列特征：

□    nodeType 的值为 11;

10

 

□    nodeName 的值为"Mocument-fragment ■;

□    nodeValue 的值为 null；

□    parentNode 的值为 null;

□子节点可以是 Element、ProcessinglnstructionN Comment、Text、CDATASection 或 EntityReferenceo

虽然不能把文档片段直接添加到文档中，但可以将它作为一个“仓库”来使用，即可以在里面保存将 来可能会添加到文档中的节点。要创建文档片段，可以使用document. createDocumentFragment ()方 法，如卜"所示：

var fragment = document. createE>ocumentFragment ();

文档片段继承了 Node的所有方法，通常用于执行那些针对文档的DOM提作。如果将文档中的节 点添加到文片段中，就会从文档树中移除该节点，也不会从浏览器中再看到该节点。添加到文档片段 中的新节点同样也不属于文梢树。可以通过appendChi Id (＞或insertBefore ()将文档片段中内容添 加到文档屮。在将文档片段作为参数传递给这两个方法吋，实际上只会将文档片段的所有了-节点添加到 相应位置上；文档片段本身永远不会成为文档树的_部分。来看下面的HWL示例代码：

＜ul id="myList"＞＜/ul＞

假设我们想为这个＜111＞元素添加3个列表项。如果逐个地添加列表项，将会导致浏览器反复渲染(呈 现)新信息。为避免这个问题，可以像下面这样使用一个文档片段来保存创建的列表项，然后再一次性 将它们添加到文档中。

var fragment = document.createDocurnentFragment(); var ul = document.gecElemenLById("myList"); var li = null;

for (var i=0; i ＜ 3; i++)＜

li = document»createElement ("li •*);

li.appendChild(document.createTextNode("Item " + {i+1)))； fragment.appendChiId(li)；

}

ul .appendChi Id (fragment.)；

DocumentFragmentExampleO 1 .htm

在这个例子中，我们先创建一个文档片段并取得了对元素的引用。然后，通过for循环创建 3个列表项，并通过文本表示它们的顺序。为此，需要分别创建＜li＞元素、创建文本节点，再把文本节 点添加到＜li＞元素。接着使用appendChildO将＜li＞元素添加到文档片段中。循环结束后，再调用 appendChild ()并传人文档片段，将所有列表项添加到＜ul＞元素中。此时，文档片段的所有子节点都 被删除并转移到了 ＜^1＞元素中。

[1](#footnote1)

设置特性

[2](#footnote2)

与getAttributeU对应的方法是setAttributeG ,这个方法接受两个参数：要设置的特性名和 值。如果特性已经存在,setAttribute ()会以指定的值替换现有的值;如果特性不存在,set Attribute (> 则创建该屈性并设置相应的值。来看下面的例

10.1.9 Attr 类型

元素的特性在DOM巾以Attr类型来表示。在所有浏览器中(包括正8),都可以访问Attr类型 的构造函数和原型。从技术角度讲，特性就是存在于元素的attributes属性中的节点。特性节点具有 下列特征：

□    nodeType 的值为 11;

□    nodeName的倚是特性的名称；

□    nodeValue的值是特性的值；

□    parentNode 的值为 null;

□在HTML中不支持(没有)子节点；

口在XML巾子节点可以是Text或EnLityReference。

尽管它们也是节点，但特性却不被认为是DOM文档树的一部分。开发人员最常使用的是9的；^-tribute ()、setAttribute U 和 remveAttribute ()方法，很少直接引用特性节点。

Attr对象有3个属性：name、value和specified。其中，name是特性名称(与nodeName的 值相同)，value是特性的值(与nodeValue的值相同)，而specified是一个布尔值，用以区别特 性是在代W中指定的，还是默汄的。

使用document. createAttribute (｝并传人特性的名称可以创建新的特性节点。例如，要为元素 添加align特性，可以使用下列代码：

var attr = document.createAttribute("align");

attr.value = •left"；

element.setAttributeNode(attr)；

alert(element.attributes["align"].value);    //"left"

alerc(element.getAttributeNode("align").value); //"left" alert(element-getAttribute("align"))?    //"left"

d ttrExampleOJ. htm

这个例子创建了一个新的特性节点。由于在调用createAttributeO吋已经为name属性赋了值， 所以后面就不必给它赋值了。之后，又把value属性的值设置为为了将新创建的特性添加到 元素中，必须使用元素的setAttributeNode (》方法。添加特性之后，可以通过下列任何方式访问该 特性：attributes 属性、getAtXributeNode ()方法以及 getAttribute ()方法。其中，attributes 和getAttributeNode {)都会返回对应特性的Attr节点，而getAttribute ()则只返回特性的值。

我们并不建议直接访问特性节点P实际上，使用getAttribute () setAttribute (}和removeAttribute ()方法远比操作特性节点更为方便。

10.2 DOM操作技术

很多时候，D0M操作都比较简明，因此用JavaScript生成那残通常原本是用HTML代码生成的内 容并不麻烦。不过，也有一些时候，操作DOM并不像表面t看起来那么简单。由于浏览器中充斥着隐 藏的陷阱和不兼容问题，用JavaScript代码处理DOM的某些部分要比处理其他部分更复杂一些。不过, 也有一些吋候，操作D0M并不像表面上看起来那么简单。

10.2.1动态脚本

10

 

使用素可以向页面中插人JavaScript代码，一种方式是通过其src特性包含外部文件， 另一种方式就是用这个元素本身来包含代码。而这一节要讨论的动态脚本，指的是在页面加载时不存在， 但将来的某一时刻通过修改DOM动态添加的脚本。跟操作HTML元素_样，创建动态脚本也有两种方 式：插人外部文件和直接插人JavaScript代码。

动态加载的外部JavaScript文件能够立即运行，比如下面的＜SCript＞元素：

＜script type="text/javascript" src=Mclient.js"x/script＞

这个＜SCript＞元素包含了第9章的客户端检测脚本。而创建这个节点的DOM代码如下所示：

var script = document.createElement("script"); script.type = "text/javascript*;

script-src = "client.js"; document.body.appendChild(script);

显然，这里的DOM代码如实反映了相应的HTML代码。不过，在执行最后一行代码把<30^?(^> 元素添加到页面中之前，是不会下载外部文件的。也可以把这个元索添加到<1^3<3>元素中，效果相同。 整个过程可以使用下面的函数来封装：

function loadScript(url){

var script = document.createElement("script■); script.type = "text/javascript";

script.src = url；

document.body.appendChild{script);

}

然后，就可以通过调用这个函数来加载外部的JavaScript文件了 ••

loadScript{"client.js")；

加载完成后，就可以在页面中的其他地方使用这个脚本了。问题只有一个：怎么知道脚本加载完成 呢？遗憾的是，并没有什么标准方式来探知这一点。不过，与此相关的一些事件倒是可以派上用场，但 要取决于所用的浏览器，详细讨论请见第13章。

另一种指定JavaScript代码的方式是行内方式，如下面的例+所示：

<script type="text/j avascript"> function sayHi(){

alert("hi");

}

</script>

从逻辑上讲，下面的DOM代码是有效的：

var script = document-createElement("script")； script .type    "text/javascript* ?

script-appendChild(document.createTextNode("function sayHi(){alert('hi; document.body.appendChiId(script);

在Firefox、Safari、Chrome和Opera中，这些DOM代码可以正常运行。但在IE中，则会导致错误。 1£将<3<^#1:>视为一个特殊的元素，不允许DOM访问其子节点。不过，可以使用<3(:匕口1:>元素的 text属性来指定JavaScript代码，像下面的例子这样：

var script = document.createElement("script")； script.type = "text/javascript"；

script.text 3 "function sayHi(){alert(* hi');}";

document.body.appendChiId(script);

DynamicScriptExampleOl .htm

经过这样修改之后的代码可以在IE、Firefox、Opera和Safari 3及之后版本中运行。Safari 3.0之前 的版本虽然+能正确地支時text域性，但却允许使用文本节点技术来指定代码。如果需要兼容早期版 本的Safari,可以使用下列代码：

var script = document.createElement("script")； script.type = "text/javascript";

var code = wfunction sayHi {) {alert (• hi *);}•*;

try {

script.appendChiId ＜ document.createTextNode("code"))z

} catch (ex){

script.text ■ ”code"j

}

document.body.appandChild(script)；

这里，首先尝试标准的DOM文本节点方法，因为除了 IE (在IE中会导致抛出错误)，所有浏览器 都支持这种方式。如果这行代码抛出了错误，那么说明是IE,于足就必须使W text属性了。整个过程 可以用以下函数来表示：

function loadscriptstring(code){

var script = document.createElement(■script•)； script.type = "text/javascript";

try {

script.appendChiId(document.createTextNode(code));

} catch (ex){

script.text = code；

}

document.body.appendChild(script);

}

下面是调用这个函数的示例：

loadscriptstring{"function sayHi(){alert('hi*);}");

DynamicScjipt£xampleO2. htm

以这种方式加载的代码会在全局作用域中执行，而且当脚本执行后将立即可用。实际上，这样执行 代码与在全局作用域中把相同的字符串传递给evalO是一样的。

10.2.2动态样式

能够把CSS样式包含到HTML页面中的元素有两个。其中，＜link＞元素用于包含来自外部的文件, 元素用于指定嵌人的样式。与动态脚本类似，所谓动态样式是指在页面刚加载时不存在的样

式；动态样式是在页面加载完成后动态添加到贞面中的。

我们以下面这个典型的＜link＞元素为例：

clink rel = * sty1esheet" type-"text/css" href="styles.css"＞

10

 

使用DOM代码可以很容易地动态创建出这个元素：

var* link = document.createElement("link"); link.rel = "stylesheet"; link.type = "text/css"； link.href = "style.css"；

var head = document.getElementsByTagName("head")[0]; he«ad.appendChild{link＞;

以上代砰在所有主流浏览器中都可以正常运行。笛要注意的是，必须将＜link＞元素添加到＜head＞ 而不是＜body＞元素，才能保证在所有浏览器中的行为一致。整个过程可以用以下函数来表示：

function loadStyles(url){

var link = document.createElement("link")?

link.rel = "stylesheet *; link.type = "text/css"； link.href = url；

var head = document.getElementsByTagName{*head")[0]; head.appendChild(link)；

}

调用loadStylesO函数的代码如下所示：

loadstyles("styles.css");

加载外部样式文件的过程是异步的，也就足加载样式与执行JavaScript代码的过程没有固定的次序。 一般来说，知不知道样式已经加载完成并不重要；不过，也存在几种利用事件来检测这个过程是否完成 的技术，这些技术将在第13章讨论。

另一种定义样式的方式是使用<817^>元素来包含嵌人式CSS,如下所示：

<style type-"text/css-> body {

background-color: red；

}

</style>

按照相同的逻辑，下列DOM代码应该是有效的：

var style = document.createElement("style")； style.type = "text/css";

style.appendChild(document.createTextNode{■body{background-color:red}"))； var head = document.getElementsByTagName("head")[0]； head.appendChild(style)；

DynamicStyleExampleOl .htm

以上代码可以在Firefox、Safari、Chrome和Opera巾运行，在IE中则会报错。历将＜81716＞视为 一个特殊的、与script似的节点，不允许访问其子节点。事实上，ffi此时抛出的错误与向＜SCriPt＞ 元素添加子节点时抛出的错误相同。解决IE中这个问题的办法，就是访问元素的stylesheet属性， 该属性又有一个cssText属性，卩了以接受CSS代码（第13章将进一步讨论这两个属性），如下面的例 子所示o

var style = document.createElement("style"); style.type = wtext/css"；

try{

style.appendChiId(document.createTextNode("body{background-color:red}"))；

} catch (ex){

style•stylesheet.cssText » "body{background-color:red}wj

}

var head = document.getElementsByTagName("head")[0J; head.appendChiId(style);

与动态添加嵌人式脚本类似，重写后的代码使用了 tiy-catch语句来捕获ffi抛出的错误，然后再 使用针对IE的特殊方式来没g样式。因此，通用的解决方案如下。

function loadstylestring(css){

var style = document.createElement{"style")；

style.type = "text/css";

try{

sty1e.appcndChiId(document.createTextNode(css)}； } catch (ex){

style.stylesheet.cssText = css;

}

var head = document^getElementsByTagName("head")[0]； head.appendChiId(style);

DynamicStyleExampleO2. htm

 

调用这个函数的示例如下:

loadStyleString<"body{background-color：red}">;

这种方式会实时地向页面中添加样式，因此能够马上看到变化。

如果专门针对IE编耳代码，务必小心使用stylesheet. cssText属性。在$用 同一个<3(^16>元素并再次设置这个属性时，有可能会导致浏览器崩濟。同样，将 cssText属性设置为空字符串也可能导致浏览器崩溃。我们希望正中的这个bug能 够在将来被修复。

10.2.3操作表格

<table>元素是HTML中最复杂的结构之一。要想创建表格，一般都必须涉及表示表格行、单元格、 表头等方面的标签。由于涉及的标签多，因而使用核心DOM方法创建和修改表格往往都免不了要编写 大量的代码。假设我们要使用DOM来创建下面的HTML表格。

<table border="1" width="100%"> <tbody>

<tr>

<td>Cell 1,l</td> <td>Cell 2#l</td>

</tr>

<tr>

l,2</td> 2,2</td>

 

10

 

<td>Cell

<td>Cell

</tr>

</tbody>

要使用核心DOM方法创建这些元素，得需要像下面这么多的代码:

//创建 table

var table = document.createElement("table"); table.border = 1； table.width = "100%"?

//创建 tbody

var tbody = document.createElement("tbody");

table.appendChi]d(tbody)；

//创建第一行

var rowl = document.createElement{"tr");

tbody.appendChild(rowl);

var ceLll_l = document.createElement("td");

cel 11一 L . appendChild (document. createTextNode {"Cell 1,1""; rowl.appendChild(celll_l);

var ce丄12_1 = document.createElement("td")；

cel12_1.appendChild(document-createTextNode("Cell 2,1")); rowl.appendChild(cell2_l)；

//创建第二行

var row2 = document.createElement("tr•);

tbody.appendChild{row2)；

var celll_2 = document.createElement("td*);

celll_2.appendChild(document.createTextNode("Cell 1,2"))；

row2.appendChild(cell1_2);

var cell2_2= document.createElement("td")；

cell2_2.appendChild(document.createTextNode("Cell 2,2n));

row2.appendChild(cell2_2);

//将表格添加到文档主体中

document.body.appendChild(table)；

显然，DOM代码很长，还有点不太好懂。为了方便构建表格，HTMLDOM还为＜table＞、＜nbody＞ 和＜1^＞元素添加了一些属性和方法。

*＜table＞元素添加的属性和方法如下。

口 caption：保存着对＜。&口1:;1011＞元素(如果有)的指针。

□    tBodies:是一个＜t:body＞元素的 HTMLCollection。

口 tFoot：保存着对＜[1001:＞元素(如果存)的指针。

□    tHead：保存着对＜比的＜3＞元素(如果有)的指针。

□    rows： 是一个表格中所有行的HTMLCollection。

□    createTHead():创建＜thead＞元素，将其放到表格中，返回引用。

□    createTFoot ():创建＜tfoot＞元家，将其放到表格巾，返回引用。

□    createCaption():创建＜caption＞元素，将其放到表格中，返回引用。

□    deleteTHead ()；删除＜thead＞元素。

□    deleteTFoot ():删除＜tfoot＞元素。

□    deleteCaption ():删除＜caption＞元素o

□    deleteRow (pos):删除指定位置的行。

□    insertRow(pos):向rows集合中的措定位置插人一行。

为＜tbody＞元素添加的属性和方法如下。

□    rows：保存着 ＜tbody＞ 元素中行的 HTMLCol lection。

□    deleteRow (pos):删除指定位置的行。

□    insertRow(pos):向rows集合中的指定位置插人一行，返回对新插人行的引用。

为＜1^＞元素添加的属性和方法如下。

□    cells：保存着＜1：1?＞元素中单元格的HTMLCollection。

□    deleteCell (pos):删除指定位H的单兀格。

□    insertcell (pos)：向cells集合中的指定位置插人一个单元格，返回对新插人单元格的引用。 使用这些属性和方法，可以极大地减少创建表格所需的代码数错。例如，使用这些属性和方法可以

将前面的代码重写如F (加阴影的部分是重写后的代码)。

//创建 table

var table = document.createElement. ("table")； table.border = 1; table .width =： "LOO%*1;

//创建 tbody

var tbody = document.createElement{"tbody"); table.appendChiId(tbody);

//刻建第一行

tbody.insertRow(0};

tbody.rows[0].InsertCellCO)j

tbody.rows[0].cells【0].appendChiId(document.croateTextNodo("Cell 1,1"))； tbody.rows[0].insertCel1(1)?

tbody.rows[0].cells[11.appendChiId(docuznent.createTextNode("Cell 2,1"))；

//釗建第二行

tbody.insertRow(l)/

tbody.rows[1】.insertCell<0);

tbody.rows[1].cells[0].appendChiId(document.createTextNode("Cell 1,2")); tbody.rows[1].inaertCell(l)t

tbody.rows f 1 ] .cells (1].appendChild(document.createTextNode(**Cell 2,2n));

//将表格添加到文档主体中

document. body. appendChi Id (table);

在这次的代码中，创建＜^13]^＞和＜比0办＞的代码没有变化。不同的是创建两行的部分，其屮使用 了 HTML DOM定义的表格属性和方法。在创建第一行时，通过＜tbody＞元索调用了 inSertROW()方 法，传入f参数0~表示应该将插人的行放在什么位置上。执行这一行代码后，就会自动创建一行并 将其插人到＜1:1＞0办＞元素的位S 0上，因此就可以马上通过tbody.rows [0】来引用新插人的行。

创建单元格的方式也十分相似，即通过＜比＞元素调用insertcell (＞方法并传人放置单元格的位 置。然后，就可以通过tbOdy.rOWS[0].CeUS[0]来引用新插人的单元格，因为新创建的单元格被插 入到了这一行的位置0上。

10

 

总之，使用这些属性和方法创建表格的逻辑性更强，也更容易看懂，尽赞技术上这两套代码都足正 确的。

10.2.4 使用 NodeList

理解NodeList及典“近亲” NamedNodeMap和HTMLCollection,是从整体上透彻理解DOM的 关键所在。这三个集合都是"动态的”；换句话说，每当文档结构发生变化时，它们都会得到更新。闲 此，它们始终都会保存着最新、最准确的信息。从本质上说，所有NodeList对象都是在访问DOM文 档时实时运行的査询。例如，下列代码会导致无限循环：

var divs = document.getElementsByTagNarae("div*}, i,

div;

for {i^O； i ＜ divs.length? i++){

div = document.createElement(Mdiv")； document.body.appendChild(div);

}

第一行代碑会取得文档中所有《3什＞元素的HTMLCollection。由于这个集合是“动态的”，因此 只要冇新＜(3“＞元素被添加到页面中，这个元索也会被添加到该集合中。浏览器不会将创建的所有集 合都保存在一个列表巾，而是在下一次访问集合时再更新集合。结果，在遇到上例中所示的循环代码 时，就会导致-个有趣的问题。毎次循杯都要对条件1 ＜ divs. length求值，意味着会运行取得所 有＜出7＞元素的査询。考虑到循环体每次都会创建一个新＜div＞元紊并将其添加到文档中，因此 divs. length的值在每次循环后都会递増。既然i和divs . length每次都会同时递增，结果它们的 值永远也不会相等。

如果想要迭代一个NodeList.摄好是使用length域性初始化第二个变量，然后将迭代器与该变 量进行比较，如下面的例子所示：

var divs = document.getElementsByTagName('div"), i#

len,

div；

for (i«0, len=divs.length； i ＜ len; i++){

div = document.createElement(M div"); document.body•appendChild(div);

}

这个例子中初始化了第二个疫量leno由于len中保存着对divs. length在循环开始时的一个快 照，因此就会避免上-个例子中出现的无限循环何题。在本牵演示迭代NodeList对象的例子中，使用 的都是这种更为保险的方式。

一•般来说，应该尽地咸少访问NodeList的次数=因为每次访问NodeList,都会运行一次基于文 档的査询。所以，可以考虑将从NodeList中取得的值缓存起来。

10.3小结

DOM是语言中立的API，用于访问和操作HTML和XML文档。DOM1级将HTML和XML文档 形象地看作一个层次化的节点树，可以使用JavaScript来操作这个节点树，迸而改变底层文挡的外观和 结构。

DOM由各种节点构成，简耍总结如下。

□最基本的节点类型是Node,用于抽象地衷示文档中-个独立的部分；所有其他类型都继承自 Nodeo

□    Document类麼表示漿个文矜，是一组分层节点的根节点。在JavaScript巾，document对象是 Document的一个实例3使用document X寸象，有很多种方式可以査询和取得节点。

□    Element节点表示文档中的所符HTML或XML元素，可以用来操作这些元素的内容和特性。

□另外还有一些节点类型，分别表示文本内容、注释、文档类型、CDATA区域和文档片段。

访问DOM的操作在多数情况卜都很直观，不过在处理元素时还是存在一些 复杂性。由于这两个元素分别包含脚本和样式信息，因此浏览器通常会将它们与其他元素区别对待。这 些区别导致了在针对这些元素使用innerHTML时，以及在创建新元素时的一些问题。

理解DOM的关键，就娃理解DOM对性能的影响。DOM操作往往是JavaScript程序中开销最大的 部分，而因访问NodeList导致的问题为最多+•> NodeList对象都是“动态的”，这就意味着每次访问 NodeList对象，都会运行•次査询。有鉴于此，最好的办法就是尽量减少D0M操作。