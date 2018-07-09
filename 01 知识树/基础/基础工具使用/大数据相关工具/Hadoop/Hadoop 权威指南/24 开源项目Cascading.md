#### 第24章

（作者：Chris K. Wensel）

Cascading是一个开源Java库和API，它为MapReduce提供了一个抽象层，允许 开发者建立可以在Hadoop集群上运行的复杂、任务关键型的数据处理应用。

Cascading项目始于2007年之夏。首次公开发布的版本0.1版则诞生于2008年1 月。版本1.0在2009年1月发布。可以从该项目网站(/?即.•//www.coscWwg.cow)下 载二进制文件、源代码和附加模块。

map和reduce操作提供了功能强大的原语。但是，对于创建能够在不同开发者之 间共享的复杂且高度可组合性代码来说，它们的粒度层级往往不符合要求。而且 很多开发者发现，当面对现实世界的问题时，用MapReduce的技术概念进行“思 考”是件困难的事。

为了解决第一个问题，Cascading用简单的字段名和一个数据元组模型(data tuple

model)来代替MapReduce中使用的键(keys)和值(values)，其中元组仅仅是一个值 的列表。为了解决第二个问题，Cascading通过引进更髙级抽象作为替代而直接从 map 和 reduce 操作分离出来，这些抽象为 Functions, Filters, Aggregators 和 Buffers。

其他一些替代方案大约也是在Cascading项目最初公开发布的同时出现的。考虑到 大部分替代架构强置要求了前置(pre-)和后置(post-)约束条件，或者其他一些预 期，Cascading被设计用作这些替代方案的补充。

例如，在其他几个MapReduce工具里，运行应用之前，必须将数据预格式化、过 滤或导入到HDFS中，准备数据的步骤必须在编程抽象的外部执行。相比之下， 在Cascading中，准备和管理数据则是编程抽象必不可少的一部分。

本实例研究从介绍Cascading主要概念开始，然后以概述ShareThis 如何在其基础设施中使用Cascading作为结束。

为了获取更深入的有关Cascading处理模型的介绍，请查看项目网站上的 “Cascading 用户指南”，网址为 h<ftp://www>. cascading.org/documentation/。

##### 24.1字段、元组和管道

MapReduce模型使用键和值实现输入数据和map函 reduce函数和输出数据的连接。

map函数和reduce函数、



但是众所周知，现实世界的Hadoop应用通常包含不止一个链接在一起的 MapReduce作业。思考一下MapReduce中实现的标准字数统计范例。如果需要将

数值型计数结果按降序排列，这是一个不太可能实现的要求，需要在第二个 MapReduce作业中完成。

这样，在抽象层面，键和值不仅将map和reduce连接在一起，也连接reduce到下 一个map,然后再到下一个reduce,以此类推（参见图24-1）。也就是说，键/值对 来源于输入文件，流经map和reduce操作组成的链，最终停留于一个输出文件。 当实现了足够多的这些链接在一起的MapReduce应用，将会看到一个定义良好的 键-值操作集合，它被反复使用以修改键-值数据流。

Cascading简化了这一过程，它将键和值抽象出来并用具有相应字段名的元组来替 代它们，类似干关系型数据库中表和列名的概念。在处理过程中，当这些字段和元 组流经由管道连接在一起的用户自定义操作时，就会得到相应的操作，具体如 图24-2所示。

①译者注：ShareThis是一个社会化分享应用开发平台，一个致力于互联网分享工具开发的创业 平台。

24-2.由字段和元组连接的管道

这样一来，Map Reduce的键和值就被简化为以下形式。

•字段（fields）

一个字段是String名称（比如“first_name”）、数值型位置（比如2或-1，分别对应第三和最后的位置），或两者的组合。这样，字段被用于声 明元组中值的名称，以及从元组中通过名称选取值。后者类似于SQL的 select 调用。

元组（tuples）

一个元组其实是一组java.lang.Comparable对象。一个元组非常像

数据库的一行或一条记录。

而map和reduce操作被抽象于一个或多个管道实例之后（参见图24-3）。

• Each

Each管道一次处理一个输入元组。它可以对输入元组应用Function或 Filter操作（简短描述）。

• GroupBy

GroupBy管道依据分组字段(grouping fields)对元组进行分组。它的行为 类似SQL中的GROUP BY语句。它也能将多个输入元组流合并为单个 流，如果它们全都共享相同的字段名。

• CoGroup

CoGroup管道通过共同字段名将多个元组流连接在一起，并且也通过共 同分组字段对元组进行分组。所有标准的连接类型(内部的、外部的等等) 和自定义连接都可以对两个或更多个元组流实施。

• Every

Every管道一次处理一个元组分组，该分组是GroupBy或CoGroup管 •道操作的结果。Every管道可对分组过程应用Aggregator或Buffer

操作。

SubAssembly

SubAssembly管道允许在单个管道内的组装嵌套(nesting of assemblies),该管道可进而被嵌入更复杂的组装。

24-3.管道类型



![img](Hadoop43010757_2cdb48_2d8748-288.jpg)



所有这些管道链接在一起被开发者装入“管道组装” (pipe assemblies),其中每个 组装可有很多输人元组流(sources)和很多输出元组流(sinks)。如® 24-4所示。

表面上，这也许看起来比传统MapReduce模型更复杂。并且，不可否认这里有比 map, reduce,键和值更多的概念。但实际上，有更多的概念必须协同工作以提供 不同的行为。

PipeAssembiy

图24-4. —个简单的管道组装

例如，一个开发者如果想要提供对reducer值的“辅助排序”，将需要实现一个 map、一个reduce、一个“复合”(composite)键(两个键嵌套在一个父键中)、一个 值、一个分区器(partitioner)、一个“输出值分组”比较器(comparator)以及一个 “输出键”比较器，所有这些将以各种方式彼此耦合，并且有很大的可能性在随 后的应用中不能重用。

在 Cascading 中，这将是一行代码：new GroupBy(〈previous〉，〈grouping fields〉， 〈secondary sorting fields〉)，其中〈previous〉来自于之前的管道。

##### 24.2操作

正如之前所提到的，通过引入替代操作(operation)，Cascading从M叩Reduce分离 出来，这些替代操作被应用于个体元组或元组的分组(参见图24-5)。

24-5.操作类型

• Function

Function对单个输入元组进行操作，对于每一个输入可能返回0或更 多输出元组。Function操作由Each管道使用0

Filter

Filter是一个特殊种类的函数，返回的是布尔值，指示当前输入元组 是否要从元组流中移除。Function也用作这一目的，但是Filter对于 这一情况是经过优化的，并且很多过滤器可通过“逻辑”过滤器（如 AND、OR、XOR和NOT）进行分组，从而快速创建更复杂的过滤操作。

• Aggregator

Aggregator针对一组元组执行一些操作，这些元组通过共同的字段值

集合进行分组（例如，所有具有相同“last-name”值的元组）。常见的 Aggregator 实现有 Sum，Count，Average, Max 和 Min0

Buffer

Bu幵er与Aggregator类似，但为了在特定的分组过程中能用作“滑 动窗口”，在所有的元组之间滑动，对Buffer进行了优化。当开发者 需要在一个有序元组集合里高效插入缺失值（比如缺失的日期或持续时间） 或创建一个滑动平均（running average）时，这是非常有用的。当处理元组 分组时，Aggregator通常是首选操作，因为很多Aggregators可以很 高效的链接在一起，但有时Buffer是处理作业的最佳工具。

当一个管道组装被创建时，各项操作就和管道绑定在一起了，如图24-6所示

PipeAssembly



![img](Hadoop43010757_2cdb48_2d8748-292.jpg)



图24-6.操作的组装

管道Each和Every提供了一种简单的机制，可以在值被传递到子操作之前，从 一个输入元组中选出部分或全部值。并且有一种简单的机制用于将操作结果与原 始输入元组合并，以创建输出元组。无需深入细节，这种方式允许每个操作只关 注作为参数的元组值和字段，而不用关注当前输入元组中的整个字段集。随后， 操作能够以与Java方法重用同样的方式，在应用之间重用。

例如在 Java 中，一个被声明为 concatenate(String first. String second) 的方法比concatenate(Person person)更加抽象。第二种情形中， concatenate()函数必须“知道” Person对象；而在第一种情形中，数据从何而 来是不可知的。Cascading操作展示了相同的特性。

##### 24.3 Taps, Schemes 和 Flows

在先前的许多图中，提到了 “source”和“sink”。在Cascading中，所有数据从 Tap实例读取或写入Tap实例，但是通过Scheme对象与元组实例来回转换。

•    Tap

Tap负责“如何”以及“到何处”访问数据。例如，数据是在HDFS还是在 本地文件系统中？是通过Amazon S3还是通过HTTP获取？

•    Scheme

Scheme负责读取原始数据并转换成元组和/或将元组写入原始数据之 中，原始数据可以是文本行、Hadoop二进制序列文件或某专有格式。

注意，Taps不是管道组装的一部分，因此它们不是一种Pipe。但如果它们要成 为集群可执行的，就要和管道组装连接在一起。当一个管道组装与必要数量的 source和sink的Tap实例相连接时，我们便得到了一个Flow。这些Tap发出或 捕获管道组装期望的字段名。也就是说，如果一个Tap发出一个带有字段名 “line”的元组(通过从HDFS上的文件里读取数据)，管道组装的头部(head)—定 会期待着一个“line”值。否则，连接管道组装和这些Tap的进程将立即因为错误 而失效。

所以管道组装实际上是一种对数据处理的定义，并且其自身并不可“执行”。在 能够在集群上运行之前，必须要与source和sink的Tap实例连接。这种Tap和管 道组装的分离，是Cascading为什么如此强大的原因之一。

如果你把管道组装想作一个Java类，那么一个Flow就像一个Java对象实例，如 24-7所示。也就是说，在相同的应用中，相同的管道组装可被多次“实例化

(instantiated)”为新的Flow,而无需担心它们之间会互相干扰。这使得能像标准 Java库一样创建和共享管道组装。..

#### ii



PipeAssembly

[Source

職識麵懸.



图24-7.—个Flow示例

##### 24.4 Cascading 实践应用

既然我们知道了 Cascading是什么，并且对它如何工作有了一个清楚的了解，那么 按照Cascading写成的应用看起来是什么样的呢？详见范例24-1。

范例24-1.字数统计和排序

Scheme sourceScheme =

new TextLine(new Fields(Hline*1)); ®

Tap source =

new Hfs(sourceSchemeinputPath);②

Scheme sinkScheme = new TextLine();③

Tap sink =

new Hfs(sinkScheme, outputPath, SinkMode.REPLACE);④

Pipe assembly = new Pipe("wordcount"); (5)

String regexString = ••(?<!\\pL)(?=\\pL)[A ]*(?<=\\pL)(?!\\pL)H;

Function regex = new RegexGenerator(new Fields(nword’’)， regexString);

assembly =

new Each(assembly, new Fields("line"), regex);⑥

assembly =

new GroupBy(assembly^ new Fields(”word’’))；⑦

Aggregator count = new Count(new Fields("count"));

assembly = new EveryCassembly^ count);⑧

assembly =

new GroupBy(assembly^ new Fields("count"), new Fields("word"));⑨

FlowConnector flowConnector = new FlowConnector();

Flow flow =

flowConnector.connect("word-countsource, sink, assembly);⑲

flow.complete。；⑪

①创建一个新的Scheme,读取简单的文本文件，并为名为“line”的字段中 的每一行发布一个新的Tuple，用Fields实例作为声明。

③创建一个新的Scheme，写入简单的文本文件，允许一个Tuple具有任意 数目字段/值。如果存在不止一个值，它们将在输出文件中通过制表符

分隔。

②④创建source和sink的Tap实例，分别指向输入文件和输出目录。sink 的Tap实例将重写任何可能已经存在的文件。

⑤构建管道组装的头部，并将其命名为“wordcount”。这一名称被用来将 source和sink的Tap与管道组装绑定。如果头部或尾部有多个，那么命 名时需要将它们区分幵。

⑥用一个函数构建一个Each管道，该函数将把遇到的每个字的“line”字段 解析进一个新的Tuple。

⑦构建一个GroupBy管道，它将为字段“word”中的每个唯一值创建一个新 的Tuple分组。

用一个Aggregator构建一个Every管道，该Aggregator将计算第（7） 步每个分组中的Tuple数量。结果存储于名为“count”的字段中。

⑨构建一个GroupBy管道，它将为字段“count”中的每个唯一值创建一个 新的Tuple分组，并根据字段“word”中的值进行辅助排序。结果将是 一个“count”和“word”值的列表，其中“count”按升序排序。

⑩⑪在一个Flow中将管道组装连接至它的source和sink,然后在集群上 执行该Flow。

在本例中，对输入文档中遇到的字数进行了统计，并按自然顺序（递增）对计数进行 排序。如果一些字具有相同的“cmmt”值，这些字将按它们的自然顺序（字母顺序） 进行排序。

本例的一个明显问题在于某些情况下一些字可能具有大写字母——例如，“the” 和出现在句首的“The”。可以考虑插入一个新的操作将所有字强制变为小写，但 是我们认识到未来所有需要从文档解析字的应用都将具有相同的行为，因此创建 一个名为SubAssembly的可重用管道作为解决方案，正如在一个传统应用中我们 会通过创建一个子程序（subroutine）来解决该问题一样，详见范例24-2。

范例 24-2.创建一个 SubAssembly

public class ParseWordsAssembly extends SubAssembly ①

{

public ParseWordsAssembly(Pipe previous)

{

String regexString = ••(?< !\\pL)(?=\\pL) [A ]*(?<=\\pL)(?! WpL)";

Function regex = new RegexGenerator(new Fields(nworcT), regexString); previous = new Each(previous, new Fields(’.linen), regex);

String exprString = nword.toLowerCase()";

Function expression =

new ExpressionFunction(new Fields("word"), exprString^ String.class);② previous = new EachCprevious^ new Fields("wordn)expression);

setTails(previous);③

}

}

(1)    对SubAssembly类划分子类，SubAssembly类自身就是一种Pipe管道。

(2)    创建一个Java表达式函数，它将对“word”字段中的String值调用 toLowerCase()0调用时必须传入表达式期望“word”所属的Java类型，在本例 中，该类型是String。底层使用的是Janino编译器

(3)    告诉SubAssembly超类管道子组装的尾端在哪里。

首先，创建一个SubAssembly管道来支持用于解析字词(parse words)的管道组 装。由于这是一个Java类，因此只要有名为“word”的字段输入(参见范例24-

3)，它可被重用于任何其他应用中。注意，有一些方法能使这一函数更加通用， 详情请査阅 “Cascading 用户指南” ([http://www.cascading.org/documentation)](http://www.cascading.org/documentation)%e3%80%82)[。](http://www.cascading.org/documentation)%e3%80%82)

范例24-3.用一个SubAssembly来扩展字数统计和排序

Scheme sourceScheme = new TextLine(new Fields("linen)); Tap source = new Hfs(sourceScheme, inputPath);

Scheme sinkScheme = new TextLine(new Fields(nworcT, ncount”))； Tap sink = new Hfs(sinkScheme, outputPath, SinkMode.REPLACE);

Pipe assembly = new Pipe(HwordcountM;

assembly =

new ParseWordsAssembly(assembly); (1)

assembly = new GroupBy(assembly, new Fields(,,wordH));

Aggregator count = new Count (new Fields( count"));

assembly = new Every(assembly, count);

assembly = new GroupBy(assembly., new Fields(’’countn), new Fields("word"));

FlowConnector flowConnector = new FlowConnector();

Flow flow = flowConnector.connect(Hword-countsource, sink, assembly);

flow.complete();

①用ParseWordsAssembly管道代替来自之前范例中的Each管道。

最后，我们只是用新的SubAssembly替换了之前范例中所用的Each和字词解析 函数。这一嵌套可以在必要时继续深入。

##### 24.5灵活性

让我们退后一步来看看新的模型给了我们什么，或者，它带走了什么。

可以看到，我们不再考虑MapReduce作业，或Mapper和Reducer接口实现，以 及如何将后继MapReduce作业绑定或链接至前续作业。在运行期间，Cascading规 划软件“Planner”计算出将管道组装分割成MapReduce作业并管理它们之间联系 e佳方案，参见图24-8。

![img](Hadoop43010757_2cdb48_2d8748-294.jpg)



Reduce

Reduce

24-8. 一个Flow如何转换为链接的MapReduce作业



| i    |      |      |
| ---- | ---- | ---- |
| ik   | unc  |      |
|      |      | Jr   |



正因如此，开发者能够构建任意粒度的应用。他们可以从一个仅过滤日志文件的 小应用开始，然后根据需要将更多功能迭代构建到该应用中。

为Cascading是一个API而不是像SQL字符串那样的语法，所以它更加灵活。

首先，开发者能够使用他们喜欢的语言-比如Groovy、JRuby、Jython、Scala

及其他，详见项目网站//即来创建特定领域语言(DSLs，

domain-specific languages)。其次，开发者能够扩展Cascading的不同部分，比如 可以定制要读/写的Thrift或JSON对象，并允许它们通过元组流传递。

##### 24.6 ShareThis 中的 Hadoop 和 Cascading

ShareTh\s(<http://www.sharethis>. cow)是一个让分享任何在线内容变得简单的共享网 络。通过点击网页上按钮或浏览器插件，ShareThis使得用户只要在线，可以无缝 访问他们的联系人和网络，并通过email、IM、Facebook、Digg、移动SMS以及 类似服务分享内容，甚至无需离开当前页面。发布者能够部署ShareThis按钮来挖 掘该服务的全球共享能力，从而驱动流量、激发病毒式传播，以及追踪在线内容 的共享。ShareThis通过减少网页上的混乱以及提供跨越社交网络、会员组和社区 的即时内容分发，也简化了社交媒体服务。

因为ShareThis用户通过在线小工具(widgets)分享页面和信息，一个持续的事件流 便进人了 ShareThis网络。这些事件首先被过滤和处理，然后交给不同的后端系 统，包括 AsterData、Hypertable 和 Katta。

这些事件的量可能很巨大，以至于无法用传统系统处理。这一数据也有可能会由 干来自欺诈系统、浏览器漏洞或有缺陷的小工具的“注入式攻击”而非常脏 (dirty)。为此，ShareThis的开发者选择在后端系统前部署Hadoop进行预处理和编 排(orchestration)。他们也选择使用Amazon Web Services,以便将服务器托管在弹 性计算云(EC2，Elastic Computing Cloud)上，实现在简单存储服务(S3，Simple Storage Service)上提供长期存储，同时也期望利用弹性M叩Reduce(EMR，Elastic MapReduce)。

在本章概述中，我们将关注于“日志处理管线”(log processing pipeline)(如图24-9所示。这一管线只是获取存储于S3存储段(bucket)中的数据，对其进行处理，并 将结果存储进另一个存储段。简单队列服务(SQS: Simple Queue Service)用于协调 那些标记数据处理运行开始和结束的事件。在下游，其他进程拉(pull)数据载入

AsterData，从Hypertable拉取URL列表来获取网络爬虫(web crawl)的源头，或拉 取被抓取(crawled)的页面数据来创建Katta所使用的Lucene索引。注意，Hadoop 对于ShareThis架构极为重要，它被用于协调架构组件之间数据的处理和移动

Amazon Web Services

24-9. ShareThis曰志处理管线

有了 Hadoop作为前端，所有事件日志在被载入AsterData集群或被任何其他组件 使用之前，都能够根据一个规则集进行解析、过滤、清理和组织。AsterData是一 个集群化数据仓库，支持大的数据集，并允许基于标准SQL语法的复杂特定査 询。ShareThis选择在Hadoop集群上清理和准备输入的数据集，然后将数据载入 AsterData集群用于特定分析和报告。尽管AsterData也能完成这一处理，但用 Hadoop作为处理管线的第一环节仍然具有重要的意义，因为可以抵消主数据仓库 的负荷。

为简化开发流程，Cascading被选为主要的数据处理API,制定数据在架构组件之 间的协调规则，并提供面向开发者的组件接口。这代表了同更为“传统的” Hadoop应用案例的分离，这些案例本质上只是査询存储的数据。Cascading和 Hadoop 一起为完整的解决方案提供了更好且更简单的端到端结构，这样可以为用 户提供更多价值。

对于开发者来说，Cascading使得编程更简单，程序员可以从执行简单文本解析的 一个简单单元测试(通过对cascading.ClusterTestCase子类化而创建)开始， 然后逐层添加更多的处理规则，同时为便于维护，保持应用根据逻辑进行组织。 Cascading提供两种方式支持这种逻辑组织。第一种方式，独立的操作(Functions Filters等等)能够独立地被编写和测试。第二种方式，应用被分为多个阶段：一 个用于解析，一个用于规则，而最后一个阶段用于装箱/整理(binning/collating)数 据，所有这些都是通过之前描述的基类SubAssembly来完成的。

来自ShareThis日志记录器的数据看起来很像Apache日志，具有日期/时间戳、共 享URLs、推荐者URLs以及少量元数据。为了将数据用于下游的分析，URLs需 要被拆包(解析出査询字符串、域名等等)。为此，创建一个高层的SubAssembly

来封装解析过程。如果一些特定字段十分复杂难以解析，那么可以在 SubAssembly中嵌套子组装(child subassemblies)来处理这些字段解析。

当使用规则时，需要做同样的事。当每个Tuple通过规则SubAssembly传递 时，一旦触发任何规则，将被标记为“bad”。与“bad”标签一起，还有一段关 于记录为何标记为“bad”的描述被添加进Tuple用于后续查看。

最后，创建一个分解器(splitter)SubAssembly，主要用于做两件事。第一，它允许 元组流分解成两部分：“good”数据流和“bad”数据流。第二，分解器将数据装 箱(bin)成一个个区间，例如每小时一个区间。为了实现上述目标，仅有两个操作 是必须的：首先，根据已经出现在流中的时间戳创建区间；其次，使用区间和 g⑽元数据来创建一个目录路径(例如，05/good/,其中“05”表示5a.m.，而 “good”表示Tuple通过了所有规则)。该路径将由Cascading的TemplateTap 使用，这是一个特殊的Tap，它能够根据Tuple中的值动态地将元组流输出到不 同位置。在本章中，TemplateTap使用“path”值来创建最终的输出路径。

开发者也创建了第四个SubAssembly。该SubAssembly在单元测试期间将使用 Cascading的Assertion。这些Assertion进行双重检查，以确保规则和解析子

组装各司其职。

在范例24-4的单元测试中，我们看到分解器未被测试，实际上它被添加进另一个 未在此介绍的集成测试中。

范例24-4.针对Flow的单元测试

public void testLogParsing() throws IOException {

Hfs source = new Hfs(new TextLine(new Fields("line"))， sampleData);

Hfs sink =

new Hfs(new TextLine(), outputPath + "/parser", SinkMode.REPLACE);

Pipe pipe = new Pipe("parser");

// split "line" on tabs

pipe = new Each(pipe, new Fields("linen), new RegexSplitter("\t*'));

pipe = new LogParser(pipe);

pipe = new LogRules(pipe);

// testing only assertions

pipe = new ParserAssertions(pipe);

Flow flow = new FlowConnector().connect(source^ sink, pipe); flow.complete(); // run the test flow

// Verify there are 98 tuples and 2 fields, and matches the regex pattern // For TextLine schemes the tuples are { "offset", "linen } validateLength(flow, 98, 2, Pattern.compile("A[0-9]+(\\t[A\\t]*){19}$")); }

为了集成和部署，Cascading中构建了很多功能，以方便和外部系统集成，且获得 更大的处理宽容度(process tolerance)。

在生产过程中，所有子组装连接在一起并被规划进一个Flow,在该Flow中，除 T source和sink的Tap之外，还设计了陷阱(trap)Tap(参见图24-10)。通常，当 一个操作从一个远程mapper或reducer任务中抛出异常时，该Flow将失效并终结 它管理的所有MapReduce作业。如果一个Flow包含陷阱，那么任何异常将被捕 捉，而引发异常的数据将被存入与当前陷阱相关联的Tap。随后会接着处理下一 个Tuple而无需停止该Flow。有时你希望Flow因错误而失效，但是在这种情况 下，ShareThis开发者知道他们能够退回去查看失效数据，并在数据生产系统保持 运行的同时升级他们的测试单元。损失几个小时的处理时间要比损失几个坏记录 更加糟糕。

![img](Hadoop43010757_2cdb48_2d8748-297.jpg)



![img](Hadoop43010757_2cdb48_2d8748-298.jpg)



24-10. ShareThis日志处理流



使用Cascading的事件监听器，可以集成Amazon SQS。当一个Flow结束时，将 发送一条消息通知其他系统数据已准备就绪，可从Amazon S3获取。若出现失

效，则将发送一条不同的消息，向其他进程告警。

日志处理管线在不同的独立集群上运行结束后，都将由余下的下游进程接续。目 前日志处理管线一天运行一次，由于毫无必要让一个100个节点的集群闲置23小 时而无所事事，所以它将退出运作并在24小时之后重新投入运作。

将来根据业务需要，把更小集群上的这一任务执行间隔提升至每6个小时或1小 时一次将很容易。其他集群将根据负责各组件的业务单元的需求，各自以不同间 隔启动和关闭。例如，网络爬虫组件(采用Bixo，由EMI和ShareThis开发的一个 基于Cascading的网络爬虫工具包)可在Hypertable集群的配合下，在一个小集群 上连续不断地运行。这一按需模型与Hadoop结合的效果很好，可以对每个集群根 据其预期处理的工作量类型进行调优。

##### 24.7总结

对于处理和协调数据在多种架构组件之间的移动而言，Hadoop是一个非常强大的 平台。其唯一缺点在于主要计算模型为MapReduce。

Cascading旨在通过一个非常合理的API来帮助开发者快速而简单地构建强大的应 用，而无需思考MapReduce的具体实现，并且将数据分发、复制、分布式进程管 理和活性(1 iveness)等繁重工作留给了 Hadoop o

想要进一步了解Cascading,请加入在线社区，访问项目网站Az炉.•//[www.coycWwg.ozg/](http://www.coycWwg.ozg/%ef%bc%8c)[，](http://www.coycWwg.ozg/%ef%bc%8c) 下载示例应用。
