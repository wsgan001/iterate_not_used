E4X

本耷内容

□ E4X新增的类型 □使用E4X操作XML □语法的变化

2002年，由BEA Systems为行的儿家公司建议为ECMAScript增加一项扩展，以便在这门语言 中添加原生的XML支持。2004年6月，E4X ( ECMAScript for XML)以ECMA-357标准的形

式发布；2005年12月乂发布了修订版。E4X本身不是，门语言.它只是ECMAScript语言的可选扩展。 就其本身而言，E4X为处理XML定义了新的语法，也定义了特定于XML的对象。

尽管浏览器实现这个扩展标准的步伐非常缓慢，但Firefoxl.5及更髙版本则支持几乎全部E4X标准。 本章主要讨论Firefox对E4X的文现。

19.1 E4X的类型

作为对ECMAScript的扩展，E4X定义了如下几个新的全局类型。

□ XML： XML结构中的任何一个独立的部分,,，

□ XMLList： XML对象的集合。

□ Namespace：命名空间前缀与命名空间URI之间的映射。

□ QName：由内部名称和命名空间URI组成的一个限定名。

E4X定义的这个4个类型可以表现XML文档中的所有部分，其内部机制是将每一种类型(特别是 XML和XMLList )都映射为多个D0M类铟。

19.1.1 XML 类型

XML类沏足E4X中定义的一个重耍的新类型，nf以用它来表现XML结构屮任何独立的部分。XML 的文例可以表现元索、特性、注释.处理指令或文本节点。xml类坦继承自Object类型，因此它也继 承了所有对象默认的所有属性和方法。创建XML对象的方式不止-种，第-种方式屋像下面这样调用其 构造函数：

var x = new XML();

这行代码会创建一个空的XML对象，我们能够向其中添加数据。另外，也可以向构造函数中传人一 个XML字符申，如下面的例子所示：

var x = new XML(*<employee position=\"Software Engineer\"><name>Nicholas • + "Zakas</name></einployee>*};

传入到构造函数屮的XML字符串会被解析为分层的XML对象。除此之外，还可以向构造函数中 传人DOM文档或节点，以便它们的数据可以通过E4X来表现，语法如下：

var x s new XML(xmldom)；

虽然这种创建XML对象的方式都还不错，但最强大也吸引人的方法，则是使用XML字面fi将XML 数据直接指定给一个变量。XML字面量就足嵌人到JavaScript代码巾的XML代码。下面来看--个例子。

var employee = < employee pos it ions11 Software Engineer* >

<narae>Nicholas C. Zakas</name>

</employee>;

XMLTypeExampIeOl. htm

在这个例子中，我们将•-个XML数据结构直接指定给了一个变a,这种简洁的语法M样可以创建 一个XML对象，外将它赋值给employee变量。

Firefox对E4X的实现不支持解析XML的开头代码(prolog )：,无论<?xml version="1.0° 出现在伸递给XML构造函数的文本中，还是出现在XML字面量 中，都会导致语法错误。

19

 

XML类型的toXMLString()方法会返回XML对象及其子节点的XML字符串表示。另一方面，该 类型的toStringU方法则会基于不同XML对象的内容返回不同的字符串。如果内容简单(纯文本)， 则返回文本；否贝!I，toString ()方法与toXMLString()方法返回的字符串一样。来看下面的例子。

var data = <name>Nicholas C. Zakas</name>;

alert(data.toStringO)；    //"Nicholas C. Zakas"

alsrt(data.toXMLString())；    //"<naine>Nicholas C. Zakas</name>"

使用这两个方法，几乎可以满足所有序列化XML的崙求。

19.1.2 XMLList 类型

XMLList类型表现XML对象的有序集合。XMLList的DOM对等类型是NodeList,但与Node和 NodeList之间的K别相比，XML和XMLList之间的［X:别是有意设汁得比较小的。要逋式地创建一^ XMLList对象，可以像下面这样使用XMLList构造函数：

var list = new XMLList()；

与XML构造函数一样，也可以向其中传入一个待解析的XML字符串。这个字符串可以不止包含一 个文捫元素，如下面的例子所示：

var list = new XMLList {"<item/><item/>”；

XMLListTypeExampleO 1. htm

结果，保存在这个list变ttip的XMLList就包含了两个XML对象，分别是两个<item/>元素。

还可以使用加号(+ )操作符来组合两个或多个XML对象，从而创建XMLList对象。加号操作符 在E4X中已经被弟:载，可以用于创建XMLList,如下所示：

var list = <item/> + <item/> ；

这个例子使用加号操作符组合了两个XML字面量，结果得到一个XMLList。同样的组合操作也可 以使用特殊的和</>语法来完成，此时不使用加号操作符，例如：

var list = <><item/xitem/></>；

尽管可以创建独立的XMLList对象,但是这类对象通常是在解析较人的XML结构的过程中捎带着 被创建出来的。来看下面的例子：

var employees = <employees>

<employee positions"Software Engineer">

<name>Nicholas C. Zakas</name>

</employee>

<employee position="Salesperson,r> <name>Jim Smith</name>

</employee>

</employees>;

XMLListTypeExampleO2. htm

以上代码定义的employees变量中包含着一个XML对象，表示<employees/〉元素。由于这个元 素又包含两个<employee/>元索，因而就会创建相应的XMLList对象，•并将其保存在employees, employee中。然后，可以使用方括号语法及位置来访问毎个元素：

var firstEmployee = employees.employee[0]； var seconaEmployee = employees.employee[1];

每个XMLList对象都有length ()方法，用于返同对象中包含的元素数量。例如： alert(employees.employee.length(})； Z/2

注意，length ()是方法，不是属性。这一点是故意与数组和NodeList相区别的。

E4X有意模糊XML和XMLList类型之间的区别，这一点很值得关注。实际上，一个XML对象与一 个只包含一个XML对象的XMLList之间，并没有显而易见的区别。为了减少两者之间的区别，毎个XML 对象也同样有•十length()方法和一个由[0]引用的滅性(返回XML对象自身)。

XML与XMLList之间的这种兼容性可以简化E4X的使用，因为有些方法可以返回任意一个类型。

XMLList对象的toString()和toXMLString ()方法返回相同的字符串值，也就是将其包含的 XML对象序列化之后再拼接起来的结果。

19.1.3 Namespace 类型

E4X中使用Namespace对象來表现命名空间。通常，Namespace对象是用来映射命名空间前缀和 命名空间URI的，不过有时候并不需要前缀。要创建Namespace对象，可以像下面这样使用Namespace 构造函数：

var ns - new Namespace^};

而传人URI或前缀加URI,就可以初始化Namespace对象，如下所示:

var ns = new Namespace ("http: "[www.wrox.com/•](http://www.wrox.com/%e2%80%a2));

var wrox = new Namespace("wrox", *[http://www.wrox.com/*)](http://www.wrox.com/*)%ef%bc%9b)[；](http://www.wrox.com/*)%ef%bc%9b)

 

可以使用prefix和uri属性来取得Namespace对象中的信息:

 

alert{ns.uri); alert{ns.prefix)； alert{wrox.uri)； alert(wrox.prefix);

 

""<http://www.wrox.com>"

//undefined

//"http：//www.wrox.com/" //"wrox"

 

//没有前蜓的命名空间 //wrox命名空间

Namespace TypeExampleO 1. him

NamespaceT^peExampleO I. htm

 

在没有给Namespace对象指定前缀的情况下，prefix属性会返回undefined。要想创建默认的 命名空间，应该将前缀设置为空字符串。

如果XML字面量中包含命名空间，或者通过XML构造函数解析的XML字符串中包含命名空间信息, 那么就会自动创建Namespace对象。然后，就以通过前缀和namespace ()方法来取得对Namespace 对象的引用。来看下面的例子：

var xral = <wrox:root xmlns：wrox=*http：//www.wrox.com/*> <wrox:message>Hello World!</wrox:messago

19

 

</wrox：root>;

var wrox = xml.namespace("wrox")；

alert(wrox.uri};

alert(wrox.prefix);

NamespaceiypeExampleO2. htm

在这个例子中，我们以XML字面的形式创建了一个包含命名空间的XML片段。而表现wrox命 名空间的Namespace对象可以通过namespace ("wrox")取得，然后就可以访问这个对象的uri和 prefix属性了。如果XML片段中有馱认的命名空间，那么向namespace()中传人空字符串，即可取 得相应的Namespace对象<>

Namespace对象的toString ()方法始终会返回命名空间URI。

19.1.4 QName 类型

QName类型表现的是XML对象的限定名，即命名空间与内部名称的组合。向QName构造函数中传 人名称或Namespace对象和名称，可以手工创建新的QNaroe对象，如下所示：

var wrox = new Namespace(*wrox", "[http://www.wrox.com/")](http://www.wrox.com/%22)%ef%bc%9b)[；](http://www.wrox.com/%22)%ef%bc%9b)

var wroxMessage = new QName (wrox, "message")；    "表示"wrox:message"

QNameTypeExampleOl .htm

创建了 QName对象之后，可以访问它的两个属性：uri和JLocalName。其中，uri属性返回在创 建对象时指定的命名空间的URI (如果朱指定命名空间，则返回空字符串)，而localName属性返回限 定名中的内部名称，如下面的例子所示：

alert(wroxMessage.uri)；    ""<http://www.wrox.com/>

alert (wroxMessage. localName) ;    /"message"

QNameTypeExampleO 1 .htm

这两个属性是只读的，如果你想修改它们的值，会导致错误发生。QName对象重写了 toStringO 方法，会以uri : : localName形式返阿一个字符串，对于前面的例+来说，就是-<http://www.wrox>. com/::message"o

在解析XML结构吋，会为表示相应元素或特性的XML对象自动创建QName对象。可以使用这个XML 对象的naneO方法取得与该XML对象关联的QName对象，如下面的例子所示：

var xml < wrox：root xmlns：wrox="<http://www.wrox.com/>">

<wrox：message>Hello World!</wrox:roessage>

</wrox：root> ；

var wroxRoot = xml.name{)；

alert(wroxRoot.uri)；    "-<http://www.wrox.com/>"

alert(wroxRoot.localName);    //"root"

QNameTypeExampleO2.htm

这样，即便没有指定命名空间信息，也会根据XML结构中的元素和特性创建一个QName对象。

使用setNameO方法并传入一个新QName对象，可以修改XML对象的限定名，如下所示： xml.setName(new QName("newroot"))；

通常，这个方法会在修改相应命名空间下的元素标签名或特性名吋用到。如果该名称不属于任何命 名空间，则可以像下面这样使用setLocalNamet)方法来修改内部名称：

xml.setLocalName("newtagname");

19.2    —般用法

在将XMI.对象、元素、特性和文本集合到一个层次化对象之后，就可以使用点号加特性或标签名的 方式来访问其中不同的层次和结构。毎个子元素都是父元索的一个属性，而属性名与元素的内部名称相 同。如果子元素只包含文本，则相应的属性只返冋文本，如下面的例子所示。

var employee = <employee position?Software Engineer*>

<name>Nicholas C. Zakas</name>

</employee>;

alert(employee.name)/ //"Nicholas C- ZakasM

以上代码巾的<name/>^&素只包含文本。访问employee. name即可取得该文本，而在内部需要定 位到<name/>元索，然后返回相应文本。由于传人莉alert U时，会隐式调川toString ()方法，因此 显示的中包含的文本。这就使得访问XML文档中包含的文本数据非常方便。如果有多个元素 具有相同的标签名，则会返回XMLList。下面再看，-个例子。

var employees = <employees>

<employee position="Software Engineer■>

<name>Nicholas C. Zakas</naroe>

</employee>

<employee position="Salesperson">

<name>Jim Smith</name>

</employee>

</employees>；

alert(employees.employee[0].name);    //"Nicholas C. Zakas*

alert (employees. employee [1J .name) ;    Smith**

这个例子访问了每个<emPlOyee/>元素并返间了它们<naine/>元素的值。如果你不确定子元素的内

部名称，或者你想访问所有子元素，不管其名称是什么，也可以像下面这样使用星号(* )。

. var allchildren = employees. * ；    //返因所有子元素，不管其名称是什么

alert (employees. * [0] .nameJ;    /"Nicholas C. Zakas■

UsageExampleOl. htm

与M•他属性一样，星号也可能返回XML对象，或返回XMLList对象，这要取决于XML结构。

要达到同样的目的，除了属性之外，还可以使川childU方法。将屈性名或索引倌传递给child!) 方法，也会得到相同的偵。來看下面的例子。

var firstChild = employees.child(0) ;    //与 employees. * [0]相同

var employeeList = en^loyees.child("employee")；    "与 employees.employee 相同

var allChildren = employees.child{" *") ;    //与 employees .*相同

为了再方便一些，还有一个children(1方法始终返回所有子元索。例如：

19

 

var allChildren = employees. children ()；    "与 employees .*相同

而另一^方法elements()的行为与chiW()类似，区别仅在于它只返回表示元索的狐对象。例如：

var employeeL丄st = employees.elements ("employee") ;    //与 employees-employee 相同

var allChildren = employees. elements {"*'*);    //与 employees 相同

这些方法为JavaScript开发人员提供了访问XML数据的较为熟悉的语法。

要删除子元素，可以使用delete操作符，如下所示：

delete employees.employee(01;

alert(employees.employee.length{))；    //I

显然，这也IE是将子节点看成嵐性的一个主要的优点。

19.2.1访问特性

访问特性也可以使用点语法，不过其语法稍有扩充。为了区分特性名与子元素的标签名，必须在名

称前面加上一个@字符。这是从XPath中借鉴的语法；XPath也是使用@来区分特性和标签的名称。不过，

结果可能就是这种语法看起来比较奇怪，例如：

var employees = <employees>

<employee position」Software Engineer■>

<name>Nicholas C. 2akas</nanie>

</employee>

〈employee position= ^Salespersonw>

<name>Jiin Smith</nanie>

</employee>

</employees>;

alert(employees.employee[0].^position); //"Software Engineer"

A tributesExampleO 1. htm

与元素-样，每个特性都由一个属性来表示，而且可以通过这种简写语法来访问。以这种语法访问 特性会得到一个表示特性的XML对象，对象的toStringO方法始终会返间特性的值。要取得特性的名 称，可以使用对象的name ()方法。

另外，也可以使用child ()方法来访问特性，只要传人带有@前缀的特性的名称即可。 alert(employees.employee(0].child(K@position"));    //"Software Engineer"

AitributesExampleOl .htm

由T-访问XML对象的属性时也可以使用childO,因此必须使用@字符麥区分标签名和特性名。 使用attributed方法并传人特性名，可以只访问XML对象的特性。*与child(＞方法不同，使

用attributed方法时，不需要传人带@字符的特性名。下面是一个例子。

alert(employees.employee[0].attribute("position"))； //"Software Engineer*

AttributesExampleOl .htm

这三种访问特性的方式同时适用于XML和XMLList类型。对于XML对象来说，会返回一个表示相 应特性的XML对象；对XMLList对象来说，会返回一个XMLList对象，其中包含列表中所有元素的 特性XML对象。对于前面的例子而言，employees. employee. ©position返回的XMLList将包含两 个对象：一个对象表示第一个〈employee/;^素中的position特性，另一个对象表示第二个元素中 的同一特性。

要取得XML或XMLList对象中的所有特性，可以使用attributes ()方法。这个方法会返冋一个 表示所有特性的XMLList对象。使用这个方法与使用@*的结果相同，如下面的例子所示。

/ /下面两种方式都会取得所有特性

var attsl = employees.employee[0].0*;

var atts2 = employees.employee[0].attributes();

在E4X中修改特性的值与修改属性的值一样非常简单，只要像下面这样为特性指定一个新值即可。 employees .employee [0] .©position = "Author**;    //修改 position 特性

修改的特性会在内部反映出来，换句话说，此后再序列化XML对象，就会使用新的特性值。同样， 为特性K值的语法也可以用来添加新特性，如下面的例子所示。

employees.employee[0] .©experience = "8 years*;    "添加 experience 特性

employees.employee[0] .©manager = "Jim Smith"；    "添加 manager 特性

由于特性与其他ECMAScript属性类似，因此也可以使用delete操作符来删除特性，如下所示。 delete employees.employee [0] . ©position；    //州除 position 特性

通过属性来访问特性极大地简化了与底层XML结构交互的操作。

19.2.2其他节点类型

E4X定义了表现XML文档中所有部分的类型，包括注释和处理指令。在默认情况上，E4X不会解 析注释或处理指令，因此这些部分不会出现在最终的对象层次中。如果想让解析器解析这些部分，可以 像下面这样设置XML构造函数的下列两个属性。

XML.ignoreComments = false；

xml.ignoreProcessinglnstructions = false；

在设置了这两个属性之后，E4X就会将注释和处理指令解析到XML结构中。

由于XML类型可以表示所有节点，因此必须有一种方式来确定节点类型。使用nodeKindU方法可

以得到XML对象表#的类型，该访问可能会返回11 text*、"element"、"comment"、"processing-

instruction"或"attribute"。以下面的 XML 对象为例。

var employees = <employees>

<?Dont forget the donutb?>

<employee position:"Software Engineer">

<name>Nicholas C. zakas</name>

</employee>

<I--just added-->

<employee position?Salesperson">

<name>Jim Smith</name>

</employee>

</employees> ;

我们可以通过下面的表格来说明nodeKindO返回的节点类型。

语 句    返回值

employees.nodeKind()

employees.* fO].nodeKind()

employees.employee[0].^position.nodeKindO employees.employee[0].nodeKind() employees.* 12J.nodeKind() emp] oyees . emp] cyee【0: . r.ame • * [ C j . nodeKi nd ()

 

■element"

•processing-instruction"

 

■element" • comment" •text"

 

19

 

不能在包含多个XML对象的XMLList上调用nodeKindO方法；否则，会抛出一个错误。

可以只取得特定类型的节点，而这就要用到下列方法。

□    attributes ():返回XML对象的所有特性。

□    comments ():返回XML对象的所有子注释节点。

口 elements ( LagName):返回XML X寸象的所省子元素。可以通过提供元素的tagName (标签名) 来过滤想要返回的结果。

□    processinglnstructions (name):返冋XML对象的所有处理指令。可以通过提供处理指令 的name (名称)来过滤想要返回的结果。

□    text ():返冋XML对象的所有文本子节点。

上述的每一个方法都返回一个包含适当XML对象的XMLList0

使用hasSimpleContent ()和hasComplexContent ()方法，可以确定XML对象中是只包含文本， 还是包含更复杂的内容。如果XML对象中只包含子文本节点，则前一个方法会返回tsrue;如果XML对 象的子节点中有任何非文本节点，则后一个方法返回true。来看下面的例子。

alert(employees.employee[0 J.hasComplexCont ent());    //true

alert(employees.employee[0].hasSimpleContent());    //false

alert{employees-employee[0].name.hasComplexContent());    //false

alert(employees.employee[0].name.hasSimpleContent())；    //true

利用这些方法，以及前面提到的其他方法，可以极大地方便查找XML结构中的数据。

19.2.3查询

实际上，E4X提供的査询语法在很多方面都与XPath类似。取得元素或特性值的简单操作是最基本

的查询，在査尚之前,不会创建表现XML文档结构中不同部分的XML对象，从底层来看,XML和XMLList 的所有属性事实上都是查询的结果。也就是说，引用不表现XML结构中某一部分的W性仍然会返回 XMLList;只不过这个XMLList中什么也不会包含。例如，如果基于前面的XML示例执行下列代码， 则返四的结果就是空的。

var cats = employees.cat; alert (cats, length⑴；    Z/0

QueryingExampleO 1 .htm

这个査询想要査找＜emplOyeeS/＞中的＜cat/＞元索，似这个元素并不存在。L面的第一行代码会返 囲一个空的XMLList对象。虽然返冋的是空对象，但査询可以照常进行，而不会发生异常。

前面我们看到的大多数例子都使/U点语法来访M直接的子节点。而像下面这样使用两个点.则以 进一步扩展杏询的深度，查尚到所有后代节点3

var allDescendants = employees.    //取俘＜employees/＞^所有后代节点

上面的代码会返回＜emplOyeeS/＞X素的所有后代节点。结果中将会包含元素、文本、注释和处理 指令，最后两种节点的有无取决于在XML构造函数上的设H (前面曾经讨论过)；但结果中不会包含特 性。要想取得特定标签的元素.需要将星号替换成实恥的标签名„

var a^-lNames = employees, .name；    //取坏作*＜employees/＞后代的所有cname/^节点

同样的奄询可以使用descendants (＞方法来完成。在不给这个方法传递参数的情况F,它会返回 所有后代节点(与使用相同)，而传递一个名称作为参数则可以限制结果。下面就是这两种情况的 例子。

var a] lDescendants = employees .descendants () ；    //所有后代节点

var allNames = employees .descendants ("name");    //后代中的所有

还nr以取得所有后代元素屮的所打特性，方法是使用F列任何一行代码。

var allAttributes = employees. .；    //取得所有后代元素中的所有特性

var allAttributes2 = employees.descendants ("@*") ;    //同上

与限制结果中的后代元素一样，也町以通过用完整的特性名来荇换星号达到过滤特性的目的。例如:

var allAttributes = employees, .©position；    //取得所有 position 特性

var aLlAttributes2 - employees .descendants ("©position") ；    //同上

除了访问后代元素之外，还可以指定査询的条件。例如，要想返回position特性值为 "Salesperson"的所有＜ employee/＞元素，可以使用下面的丧询：

var salespeople = employees.employee.(^position == *Salesperson");

同样的语法也可以用尸修改XML结构中的某一部分。例如，可以将第一位销售员(salesperson) 的position特性修改为MSenior Salesperson",代码如下：

employees.employee.(^position == "Salesperson")[0].©positions "Senior Salesperson"；

注意，阏括号中的表达式会返冋一个包含结果的XMLList,而力IS号返冋其中的第一项，然后我们 童写了©position厲性的值。

使用parent （）方法能够在XML结构中上亂这个方法会返M' +•个XML对象，表示当前XML对象 的父元素。如果在XMLList上调用parent:（>方法，则会返间列表中所有对象的公共父元素。下而是 一个例子。

var employees2 = employees.employee.parent｛）;

这ffl,变虽employees2中包含着与变S: employees相同的值。在处理来源未知的XML对象时， 经常会用到parent U方法。

19.2.4构建和操作XML

将XML数据转换成XML对象的方式有很多种,，前面曾经讨论过，可以将XML字符串传递到XML 构造函数中，也可以使用XML字面fi。相对而言，XML字面量方式更方便一些，因为可以在字面S中嵌 人JavaScript变fi,语法是使用花括号（（ > ）。可以将JavaScript变量嵌人到字面皺中的任意位置上，如 下面的例子所示。

var tagName = "color-；

var color = "red";

var xml = <{tagName}>{color}< Z{tagName}>;

alert(xml.toXMLScring())；    //"<color>red</color>

19

 

XML ConstructionExample01. htm

在这个例了•中，XML字面量的标签名和文本值都是使用花括号语法插入的。有了这个语法，就可以 省去在构建XML结构时拼接字符串的麻烦。

E4X也支持使用标准的JavaScript语法来构建完整的XML结构。如前所述，大多数必要的操作都 是查询，而且即便元素或特性不存在也不会抛出错误。在此基础上更进一步，如果将一个值指定给一个 不存在的元素或特性，E4X就会首先在底层创建相应的结构，然后完成赋值。来看下面的例子。

var employees = <employees/>;

employees.employee.name = "Nicholas C. Zakas"； employees.employee.@posiCion = "Software Engineer"；

XMLConstructionExample02. htm

这个例子一开始声明了<employeeS/>元索，然后在这个元索基础上开始构建XML结构。第二行 代砰在〈employees/〉1!1创建了—■个<employee/>元素和一个<name/>元素，并指定了文本值。第三行 代码添加了一个position特性并为该特性指定了值。此时构建的XML结构如下所示。

<employees>

<employee position="Software Engineer>

<namc>Nicholas C. Zakas</name>

</employee>

</employees>

当然，使川加号操作符也可以19添加一f<emPlOyee/>元素，如下所示。

employees.employee += <employee position="Salesperson>

<name>Jim Smith</name>

</employee>；

XML ConstructionExample02. htm

最终构建的XML结构如下所示:

<eraployees>

<employee position:”Software Engineer*>

<name>Nicholas C. Zakas</name>

</employee>

<employee position="Salesperson■>

<name>Jim Smith</name>

</employee；*

</employees>

除了上面介绍的基本的XML构建语法之外，还有一些类似DOM的方法，简介如下。

□    appendChild(child):将给定的chi Jd作为子节点添加到XMLList的末尾。

□    copy ():返回XML.对象副本。

□    insertChildAfter (refNode,    :将 chi 2d 作为子节点插入到 XMLList 中 refNode 的后面。

□    insert Chi ldBe fore (refNode, chiJd):将 child 作为子节点插人到 XMLList 中 refNode 的前面。

□    prependChild(child):将给定的child作为子节点添加到XMLList的开始位置。

□    replace (property Name f value): 用value值替换名为propertyName的屈性，这个属性 可能是一个元素，也可能是一个特性。

□    setChildren( children):用children替换当前所有的子元素，chi J dr en可以是XML对 象，也可是XMLList对象。

这些方法既非常有用，也非常容易使用。下列代码展示了这些方法的用途。

var employees 二 <employees>

<employee positions"Software Engineer->

<name>Nicholas C. Zakas</name>

</employee〉

<employee position-"Salesperson">

<name>Jim Smith</name>

</employee>

</employees>;

employees.appendChild(<axoployee positions"Vic© PresidentN> <name>Benjamin AnderBon< /ziame>

</employee>)j

eaployees.prependChiId(<employee position置"User Interface Designer*> <ziame>Micbael Johnson< / name >

</employee>);

employees.insertChildBefore(employees.child(2),

<employee positions■Human Resources ManagerR>

<namo>Margaret Jones</name>

<Zemployee>);

employees.setchildren(<employee position*"President">

<name>Richard McMichael</name>

</employee> +

<employee positions"Vice PresidentM>

<ziame>Rebecca    Znai&e>

</einployee>) j

XMLConstructionExample03. him

以上代码首先在员工列表的底部添加了一个名为Benjamin Anderson的副总统(vice president)。然 后，在员工列表顶部又添加了一个名为Michael Johnson的界面设计师。接着，在列表中位置为2的员

工-此时这个员_丁.是Jim Smith,因为他前面还有Michael Johnson和Nicholas C. Zakas-之前又添加

了一个名为Margaret Jones的人力资源部经理。始后，所有这些子元素都被总统Richard McMichael和副 总统Rebecca Smith替代。结果XML如下所示。

<employees>

〈employee posi t i on="Pres ident■>

<name>Richard McMichael</name>

</employee>

<employee position="Vice President">

<name>Rebecca Smith</name>

</employee>

</employees>

熟练运用这些技术和方法，就能够使用E4X执行任何DOM风格的操作。

19.2.5解析和序列化

E4X将解析和序列化数据的控制放在了 XML构造函数的一些设置当中。与XML解析相关的设置有 如下三个。

19

 

□    ignoreComments：表示解析器应该忽略标记中的注释。献认设置为true。

□    ignoreProcessinglnstructions:表豕解析器应该忽略标记中的处理指令。默认设S为true。

□    ignoreWhitespace：表示解析器应该忽略元素间的空格，而不是创建表现这些空格的文本节 点。默认设置为 trueo

这三个设置会影响对传人到XML构造函数中的字符串以及XML字面量的解析。

另外，与XML数据序列化相关的设置有如下两个。

□    pretty Indent：表乐•在序列化XML时，每次缩进的空格数量。默认值为2。

□    prettyPrinting:表示应该以方便人类认读的方式输出XML,即每个元素重起一行，而且子 元素都要缩进。默认设置为true。

这两个设置将影响到toString()和toXMLString()的输出。

以上五个设置都保存在settings对象中，通过XML构造函数的settings ()方法可以取得这个对 象，如下所示。

var settings = XML.settings();

alert(settings.ignoreWhitespace)；    //true

alert(settings.ignoreComments);    //true

Pars ingAndSerializationExampleO 1. htm

通过向setSettingsO方法中传人包含全部5项设置的对象，可以一次性指定所有设置。在需要 临时改变设置的情况下，这种设置方式非常相用，如下所示。

var settings = XML.settings(); XML.prettyIndent = 8;

XML.ignoreComments = false;

//执行某些处理

XML. setSettings (settings) ;    //重前面的设置

而使用defaulcSettingsf)方法则可以取得•个包含默认设置的对象，因此任何时候都可以使用 下面的代码重置设置、.

XML.setSettings(XML.defaultSettings()};

19.2.6命名空间

E4X提供了方便使用命名空间的特性。前面曾经讨论过，使用r.amspaceO方法对以取得与特定前 缀对应的Namespace Xt象j,而通过使用setNamespace ()并传人Namespace X寸象，也可以为给定元 素设置命名空间。来看下面的例子。

var messages = <messages>

<message>Hello world!</message>

</messages>?

messages.setNamespace (new Namespace ("wrox", "http ： //wv/w. wrox. com/ ■));

调用setNamespace <)方法后，相应的命名空间只会应用到调用这个方法的元素。此时，序列化 messages变景会得到如下結果。

<wrox:messages xmlns:wrox="<http://www.wrox.com/>">

<message>Hello world!</message>

</wrox：messages>

可见，由于调用了 setNamespace (}方法，<1^353965/>元索有了 wrox命名空间前缀，而 <message/>元素则没有变化。

如果只想添加一个命名空间声明，而不想改变元素，可以使用addNamespaceU方法并传人 Namespace对象，如下面的例子所示。

messages. addNamespace (new Namespace ("wrox", "http: "ww.wrox.com/")};

在将这行代码应用于原先的〈niessages/^元素时，就会创建如下所示的XML结构。

<messages xmlns: wrox?http: "[www.wrox.com/](http://www.wrox.com/) •>

<message>Hello world I</message>

</niessages>

凋用removeNamespace ()方法并传人Namespace对象，可以移除表Z5特定命名空间前缀和URI 的命名空间声明；注意，必须传人丝毫不差的表示命名空间的Namespace对象。例如：

messages.removeNamespace(new Namespace{^wrox", Mhttp：//www.wrox.com/"));

这行代码可以移除wrox命名空间。不过，引用前缀的限定名不会受影响。

有两个方法町以返回与节点相关的Namespace对象的数组：namespaceDeclarations ()和 inScopeNamespacesOc前者返回在给定节点上声明的所有命名空间的数组，后者返回位于给定节点 作用域中(即包括在节点自身和祖先元素中声明的)所有命名空间的数组。如下面的例7所示：

var messages = cmessages xmlns:wrox=Mhttp://www.wrox.com/">

<message>Hello world!</mGssage>

</messages>;

alert(messages.namespaceDeclarations()); alert(messages.inScopeNamespaces());

//"http：//www.wrox.com" //",http：//www.wrox.com"

 

alert(messages.message.namespaceDeclarations())?    //”*

alert {messages.message. inScopeNamespaces ()) ;    //**, http://www.wrox.com-

这里，〈messages/＞元素在调用namespaceDeclarations ()时，会返回包含一命名空间的数组， 而在调用inScopeNamespaces ()时，则会返回包含两个命名空间的数组。作用域中的这两个命名空间， 分别是默认命名空间(由空字符串表示)和wrox命名空间。在＜iuesSage/＞元素上调用这些方法时， namespaceDeclarat ions (),会返回一个空数组，而inScopeNamespaces ()方法返回的结果与在 ＜meSSageS/＞元素上调用时的返回结果相同。

使用双H号(：：)也可以基于Namespace对象来査询XML结构中具有特定命名空间的元素。例 如，要取得包含在wrox命名空间中的所有cmessage/〉元素，可以参考下面的代码。

var messages = 〈messages xmlns:wrox-"<http://www.wrox.com/>"> <wrox:message>Hello world!</message>

</messages>；

var wroxNS = new Namespace("wrox", "<http://www.wrox.com/>")? var wroxMessages = messages.wroxNS::message;

这里的双冒号表示返回的元素应该位于其中的命名空间。注意.这里使用的是JavaScript变贵，而 不是命名空间前缀。

还可以为某个作用域中的所有XML对象设置默认命名空间。为此，要使用default xml namespace 语句，并将一个Namespace对象或一个命名空间URI作为值赋给它。例如：

default xml namespace - "[http://www.wrox.com/"](http://www.wrox.com/%22%ef%bc%9b)[；](http://www.wrox.com/%22%ef%bc%9b)

19

 

function doSomething(){

/ /只为这个轟数设置默认的命名空间

default xml namespace = new Namespace("your", "http://www.yourdomain.com"};

}

在doSomething ()函数体内设置默认命名空间并不会改变全局作用域中的默认XML命名空间。 在给定作用域中，当所有XML数据都需要使用特定的命名空间时，就可以使用这个语句，从而避免多 次引用命名空间的麻烦。

19.3其他变化

为了与ECMAScript做到无缝集成，E4X也对语言基础进行了一些修改。其中之_就是引人了 for-each-in循环，以便迭代遍历每一个属性并返冋属性的值，如下面的例子所示。

var employees = <employees>

<employee position?Software Engineer">

<name>Nicholas C. Zakas</name>

</employee>

<employee position="Salesperson">

<name>Jim Smith</name>

</employee>

</employees>;

for each (var child in employees){ alert(child.coXKLString())?

}

ForEachlnExampleO L htm

在这个例子的for-each-in循环中，〈employees/;^每个子节点会依次被赋值给child变盘， 其中包括注释、处理指令和域文本节点。要想返回特性节点,则需要对一个由特性节点组成的XMLList 对象进行操作，如下所示。

for each (var attribute in employees.@*) { //遍历特41 alert(attribute);

}

虽然for-each-in循环是在E4X中定义的，但这个语句也可以用于常规的数组和对象，例如：

var colors - ["red","green","blue"]; for each(var color in colors){

alert(color)；

}

ForEachlnExampleO 1. htm

对于数组，for-eaCh-in循环会返回数组中的每一项。对于非XML对象，这个循环返回对象每个 属性的值。

E4X还添加了一个全局函数，名叫isXMLNameO。这个函数接受一个字符申，并在这个字符串是

元素或特性的冇效内部名称的情况下返冋true。在使用未知字符串构建XML数据结构时，这个确数可

以为开发人员提供方便。来看下面的例子。

alert(isXMLNamef"color"));    //true

alert (isXMIiName( "hello world") } ?    //false

如果你不确定某个字符申的来源，而又需要将该字符串川作一个内部名称，那么最好在使用它之前 先通过isXMLNameO检测一下是否有效，以防发生错误。

E4X对标准ECMAScript的最后一个修改是typeof操作符。在对XML对象或XMLList对象使用 这个操作符时，typeof返回字符串-xml’•。但在对其他对象使用这个操作符时，返回的都是"object”， 例如：

var xml - new XML()； var list = new XMLList(); var object = {};

alert(typeof xml); //"xml" alert(typeof list);    //"xml"

alert(typeof object); //"object0

多数悄况下，都没有必要区分XML和XMLList对象。在E4X中，这两个对象都被看成是基本数据 类型.因而也无法通过instanceof操作符来将它们区分开来。

19.4全面启用E4X

鉴于E4X在很多方面给标准JavaScript带来了不同，因此Firefox在默认情况下只启用E4X中与其

他代码能够相安无事的那些特性。要想完整地启用E4X,需要将<Script>标签的type特性设置为 "text/javascript ；e4x=ln,例如：

<script type="text/javascript; e4x=l,f src= -e4x_file. js"></script>

在打开这个“开关”之后，就会全面启用E4X,从而能够正确地解析嵌入在E4X字面最中的注释 和CData片段。在没有完整泊用E4X的情况下使用注释和/或CData片段会导致语法错误。

19.5小结

E4X是以ECMA-357标准的形式发布的对ECMAScript的一个扩展。E4X的H的是为操作XML数 据提供与标准ECMAScript更相近的语法。E4X具有下列特征。

□与DOM不同，E4X只用一个类铟来表示XML中的各种节点。

□ XML对象中封装了对所有节点都有用的数据和行为。为表现多个节点的集合，这个规范定义了 XMLList 类S。

□另外两个类趣，Namespace和QName,分别表现命名空间和限定名。

为便于査询XML结构，E4X还修改了标准了的ECMAScript语法，修改的地方如下。

口使用两个点（..）表示要匹配所有后代元家，使用@字符表示应该返回一或多个特性。

□星号字符（* ）是一个通配符，可以匹配任意类铟的节点。

□所有这些査询都町以通过--组执行相同操作的方法来实现。

到2011年底，Firefox还是唯一一个支持E4X的浏览器。尽管没冇其他浏览器提供商承诺会实现E4X, 但在服务器上，由于BE A Workshop for WebLogic和Yhaoo! YQL的推动，E4X已经取得了不小的成功。