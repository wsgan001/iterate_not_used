Apache Crunch    叩ac/ze.org/）是用来写 MapReduce 管线的高层 API。

与普通的MapReduce相比，Crunch的主要优势体现在它注重程序员友好的Java 类型(如String)以及旧式的纯Java对象，另外还有一组丰富的数据变换操作和多级 管线(不需要显式地管理MapReduce工作流中的每个作业)。

从上述角度来看，Crunch与Java版的Pig很像。在使用Pig时，经常会遇到的问 题是写用户自定义函数所使用的语言(Java或Python)与写Pig脚本所使用的语言 (Pig Latin)不同，因此程序员需要在两种不同的表示和语言之间切换，这使得他们 的开发体验缺乏连贯性，而这一点正是Crunch所回避的。Crunch与Pig不同，其 程序和用户自定义函数(UDF)使用的都是同一种语言(Java或Scala),并且UDF可 以很好地嵌入到程序中。Crunch的整体编程体验非常像是在写一个非分布式的程 序。虽然Crunch与Pig有很多相似之处，但Crunch的灵感却是来自于 FlumeJava, 一个由谷歌开发的用于构建MapReduce管线的Java库。

请不要把FlumeJava与第14章介绍的Apache Flume混淆，后者是用于收集流 式事件数据的系统。要想更详细地了解FlumeJava,可参考由Craig Chambers 等人合著的 FlumeJava: Easy, Efficient Data-Parallel Pipelines ,网址为 http:"bit. ly/data-parallel」)ipelines。

为Crunch位于上层，所以Crunch管线是高度可组合的，并且可以把常用功能 提取到库中以提供给其他程序重用，这一点与MapReduce不同，想要重用 MapReduce的代码是很困难的，因为除了一些简单的情况(例如调用恒等函数或者

像LongSumReducer这样的简单求和函数)之外，大多数程序都有自定义的 mapper和reducer实现。在MapReduce中，要想为各式各样的变换操作(如排序和

连接）写mapper和reducer库并非易事，但在Crunch中这则是一件很自然的事 情。例如，org.apache.crunch, lib.Sort类中的sort（）方法可以对任何输入 的Crunch集合进行排序。

虽然Crunch最初的设计是使用Hadoop MapReduce执行引擎来运行的，但Crunch 并不依赖于MapReduce,实际上也可以使用Apache Spark（参见第19章）作为分布 式执行引擎来运行Crunch管线。不同的引擎具有不同的特点。例如，倘若在作业 与作业之间需要传递大量的中间数据，那么使用Spark就要比使用MapReduce效 果更好，因为Spark可以把数据缓存在内存中，而不是像MapReduce那样把数据 物化到磁盘上。毋须重写程序就能够在不同的引擎上试运行Crunch管线是一个非 常强大的特色，因为这使得程序的功能性与程序的执行效率可以被区别对待，一 般来说，随着执行引擎的不断调整，程序的执行效率也不断得到提高。

本章将介绍如何利用Crunch来编写数据处理程序。更详细的内容可以参考Crunch 用户指南，网址为 [http://crunch.apache.org/user-guide.html](http://crunch.apache.org/user-guide.html%e3%80%82)[。](http://crunch.apache.org/user-guide.html%e3%80%82)

##### 18.1 示例

我们先通过一个简单的Crunch管线示例来说明一些基本概念。范例18-1中给出 的是按年份计算气象数据集中最高温度的Crunch版程序，这个程序就是我们在第 2章中首次遇到的程序。

范例18-1.使用Crunch找出最高温度的应用程序

public class MaxTemperatureCrunch {

public static void main(String[] args) throws Exception { if (args.length != 2) {

System.err.println(HUsage: MaxTemperatureCrunch 〈input path〉 〈output path〉’.); System.exit(-l);

}

Pipeline pipeline = new MRPipeline(getClass());

PCollection<String> records = pipeline.readTextFile(args[0]);

PTable<StringJ Integer〉 yearTemperatures = records

.parallelDo(toYearTempPairsFn()tableOf(strings(), ints()));

PTable<StringJ Integer〉 maxTemps = yearTemperatures .groupByKey()

.combineValues(Aggregators.MAX_INTS());

maxTemps.write(To.textFile(args[1])); PipelineResult result = pipeline.done();

System.exit(result.succeeded() ? 0 : 1);

}

static DoFrxString, Pair<String, Integer>> toYearTempPairsFn() { return new DoFn<String, Pair<String>Integer>>() {

NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void process(String input， Emitter<Pair<String> Integer》 emitter) { parser.parse(input); if (parser.isValidTemperature()) {

emitter•emit(Pair.of(parser.getYear(), parser•getAirTemperature()));

}

}

};

}

}

按照惯例，在检查命令行参数之后，程序首先构建一个Crunch Pipeline对象， 它代表了我们希望运行的计算。顾名思义，管线可以有多个阶段，也就是说具有 多个输入、输出、分枝和迭代的管线都是有可能的，尽管本例从一个单阶段管线 入手。我们将使用MapReduce来运行这个管线，因此这里创建的是一个 MRPipeline，不过，你也可以选择使用MemPipeline (在内存中运行管线以用于 测试)或者SparkPipeline(利用Spark引擎执行相同的计算)。

管线接受来自一个或多个输入源的数据。本例中的输入源是一个文本文件，文件 名由命令行的第一个参数args[0]指定。Pipeline类有一个readTextFile()方 法，它可以把文本文件转换成String类型的PCollection对象，其中每个 String表示一行文本。PCollection<S>是Crunch的最基本的数据类型，它表示 由S类型的元素组成的不可修改且无序的分布式集合。PCollection<S>&可被视 为非物化的:java.util. Collection,之所以称为非物化，是因为它的元素并没

有被读取到内存中。本例的输入是由文本文件中的文本行构成的一个分布式的集 合，用 PCollection<String>来表示。

Crunch对PCollection进行各种计算操作并产生一个新的PCollection。我们

需要做的第一件事是解析输入文件中的每一行文本并过滤掉任何不良记录。这项

任务使用PCollection的parallelDo()方法来完成，它为PCollection中的 每个元素都执行某种操作，并返回一个新的PCollection。parallelDo()的方 法签名如下：

<T> PCollection<T> parallelDo(DoFn<S,T> doFn, PType<T> type);

基本思路是，编写能够把类型S的实例变换为一个或多个类型T实例的DoFn 实现，然后Crunch将该函数应用于PCollection的每个元素。显然，这个操作 可以在MapReduce作业的map任务中并行执行。parallelDo()方法的第二个参 数是PType<T>对象，它向Crunch传递有关T的Java类型以及如何序列化该类型 的信息。

实际上，本例使用的是重载形式的parallelDo()，它创建的是一个扩展的 PCollection,称为PTable<K, V>0 PTable<K, V>是由键-值对构成的分布式 multi-map。(multi-map是可以具有重复键-值对的map。)因此，我们可以把年份作 为键，把温度作为值来表示，从而在稍后的管线中能够对它进行分组及聚合操 作。这个方法的签名是：

<K, V> PTable<K， V> parallelDo(DoFn<SJ PaircK, V>> doFn, PTableType<K> V> type);

在本例中，DoFn解析输入文本文件中的文本行，并输出年份/温度对：

static DoFn<String, Pair<String, Integer〉〉 toYearTempPairsFn() { return new DoFn<String，Pair<StringJ Integer>>() {

NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void process(String input, Emitter<Pair<StringJ Integer” emitter)

{parser.parse(input); if (parser.isValidTenperature()) {

emitter.emit(Pair.of(parser.getYear()， parser.getAirTemperature()));

}

}

}；

}

在应用该函数后，得到一个年份/温度对的列表：

PTable<String^ Integer〉 yearTemperatures = records

•parallelDo(toYearTempPairsFn(), tableOf(strings(), ints()));

parallelDo()方法的第二个参数是通过Crunch Writables类的静态方法构造的 PTableType<K, V>实例(之所以使用Writables类的静态方法，是因为我们选择 了使用Hadoop的Writable序列化框架来处理由Crunch写入的任何中间数据)。 tableOf()方法则根据指定的键/值类型创建一个PTableType。strings()方法 声明内存中的键使用Java String对象来表示，并序列化为Hadoop Text。值的类 型是 Java int,并序列化为 Hadoop IntWritables。

此刻，我们的数据表示已经变得更加结构化，但是记录数量并没有改变，这是 为输入文件中的每一行对应于yearTemperatures表中的一个表项。为了计算数 据集中每年最大的温度读数，我们需要对这些表项按年份来分组，然后找出每年

的最高温度值。幸运的是，Crunch恰好提供了此类操作，它们是PTable的API 的一部分。groupByKey()方法执行的是MapReduce的混洗(shuffle)操作，它按键 对表项进行分组，并返回第三种PCollection,我们称之为PGroupedTable<K, V〉，其combineValues()方法能够把一个键的所有值聚合起来，就像MapReduce的 reducer所做的那样：

PTable<String> Integer〉 maxTemps = yearTemperatures .groupByKeyO

.combineValues(Aggregators.MAX—INTS());

combineValues()方法的输入是一个Crunch Aggregator实例，它是一个简单的

接口，用于表示一组任意类型值的聚合。在这里，我们可以充分利用 Aggregators类中被称为MAX_INTS的内置聚合程序，其作用是找出一组整数值 中的最大值。

此管线的最后一步是调用write()把maxTemps表写入一个文件，write()的输 入就是通过静态工厂To的textFile方法创建的一个文本文件目标对象。Crunch 实际上使用了 Hadoop的TextOutputFormat格式来完成这个操作，这就意味着 输出的每一行由制表符分隔的键和值组成：

maxTemps.write(To.textFile(args[l]));

目前为止，上述程序仅仅考虑管线的构造。为了执行这个管线，我们必须调用 done()方法，此时，程序进入阻塞状态，直至管线执行完毕。Crunch返回一个 PipelineResult对象，该对象封装了管线中运行的各种作业的统计数据以及管 线运行成功与否的标志，后者被我们用来设置适当的程序退出代码。

当我们在样本数据集上运行该程序后，得到以下结果：

% hadoop jar crunch-examples.jar crunch.MaxTemperatureCrunch \ input/ncdc/sample.txt output

% cat output/part-r-00000

1949    111

1950    22

##### 18.2 Crunch 核心 API

本节将介绍Crunch的核心接口。从设计上来说，Crunch API是一种高层的API， 此程序员可以集中精力考虑计算的逻辑操作，而不是具体的执行细节。

###### 18.2.1基本操作

Crunch的核心数据结构是PCollection<S>,它是一个不可修改且无序的分布式

S类型元素的集合。本节将讨论基于PCollection的基本操作及其派生出的类 型：PTable 和 PGroupedTable。

\1. union()

Crunch的最简单的基本操作是union()，它返回一个PCollection,在这个返回 的PCollection中包含了调用该函数的PCollection以及作为输入参数的 PCollection的所有元素。例如：

PCollection<Integer> a = MemPipeline.collectionOf(1^ 3); PCollection<Integer> b = MemPipeline.collectionOf(2); PCollection<Integer> c = a.union(b);

assertEquals("{2，l，3}n, dump(c));

MemPipeline的collectionOf ()方法用于创建一个由少量元素构成的 PCollection,通常这种PCollection被用于测试或演示。dump()方法是此处 引入的另一个实用方法，它把一个小型的PCollection中的内容以字符串的形式 呈现出来。dump()不属于Crunch,但可以在本书随附示例代码的PCollections 类中找到它的实现。由于PCollection是无序的，因此c中的元素的排序顺序没 有定义。

在对两个PCollection进行并集操作时，它们必须是由同一管线创建的(否则该 操作在运行时会失败)，并且必须具有相同的类型。后一个条件在编译时强制执 行，这是因为PCollection是一种参数化的类型，且进行并集操作的两个 PCollection的类型参数必须匹配。

\2. parallelDo()

第二个基本操作是parallelDo(),它为输入的PCollection<S>中的每个元素调 用某个函数，并返回包含该调用结果的一个新的输出PCollection <T>。 parallelDo()的最简单的形式包含两个参数：DoFn<S, T>实现和PType<T>实 例。DoFn<S, 了>实现定义了一个用于把类型为S的元素变换为类型为T的元素的 函数，PType<T>实例则用于描述输出类型T，详情参见18.2.2节对PTypes的详细 介绍。

下面这段代码演示的是如何利用parallelDo()来为String类型的 PCollection应用一个计算字符串长度的函数：

PCollection<String> a = MemPipeline.collectionOf("cherry", "apple", "banana. PCollection<Integer> b = a.parallelDo(new DoFn<String, Integer>() {

^Override

public void process(String input, Emitter<Integer> emitter) { emitter.emit(input.length());

}

}, ints());

assertEquals("{6,5,6}"， dump(b));

关于 Crunch 523本例输出一个整数型的PCollection，其元素数目与输入PCollection的元素

数目相同，



![img](Hadoop43010757_2cdb48_2d8748-223.jpg)



此也可以使用DoFn中的MapFn子类来实现这种一对一的映射:



PCollection<Integer> b = a.parallelDo(new MapFn<String, Integer>() { ^Override

public Integer map(String input) { return input.length();

}

}, ints());

assertEquals("{6,5,6}", dump(b));

parallelD0()经常被用于滤除那些在后续处理步骤中不需要的数据。Crunch为 此专门提供了 filter^)方法，其参数是一个特殊的DoFn，称为FilterFn。在这 段实现中只需要实现accept()方法来指示一个元素是否应当出现在输出中。例 如，下面这段代码用于仅保留有偶数个字符的字符串：

PCollection<String> b = a.filter(new FilterFn<String>() {

@Override

public boolean accept(String input) { return input.length() % 2 == 0; // even

}

});

assertEquals("{cherry,banana}"， dump(b));

请注意，在filter^)方法的签名中没有PType，这是 输入PCollection的类型相同。

为输出PCollection与



![img](Hadoop43010757_2cdb48_2d8748-224.jpg)



如果DoFn明显地改变了它所操作的PCollection中的元素数目，则应当重写其 scaleFactor()方法，以给Crunch规划器(planner)—个提示，用以估计输出 PCollection的相对大小，这样做可以提高效率。

FilterFn的scaleFactor()方法的返回值为0.5，换句话说就是假设FilterFn

将会过滤掉大约一半的PCollection元素。如果自己的过滤函数将要过滤掉的 元素明显多于或少于一半，可以考虑重写scaleFactorO方法。

有一种重载形式的parallelDo()可以从PCollection产生一个PTable。回顾 18.1节中的示例，PTable<K，V>*—个由键-值对构成的多重映射，或者用Java语 言来说，PTable<KJ V>就是 PCollection<Pair<KJ V>>,其中 Pair<K，V>是 Crunch的键■■值对类。

下面这段代码通过使用DoFn来创建一个PTable，其中DoFn的功能是把输入字 符串变换为键-值对，以字符串的长度作为键，字符串本身作为值：

PTable<Integer』 String> b = a.parallelDo(

new DoFn<String> Pair<IntegerJ String>>() {

^Override

public void process(String input, Emitter<Pair<Integer, String” emitter) { emitter.emit(Pair.of(input.length(), input));

}

}, tableOf(ints(), strings()));

assertEquals("{(6,cherry)，(5,apple),(6,banana)}", dump(b));

从由某些值构成的PCollection中提取键以形成一个PTable,此类任务很常 见，因此Crunch专门提供了一种方法，称为by()。by()方法的参数是MapFn<S, K>，它的功能就是把输入值S映射为键K:

PTable<Integer, String〉 b = a.by(new MapFn<String, Integer>() { ^Override

public Integer map(String input) { return input.length();

}

}, ints());

assertEquals(M{(6^cherry(5,apple)，(6,banana)}", dump(b));

\3. groupByKey()

第三个基本操作是groupByKey()，用于把PTable<K, V>中具有相同键的所有值 聚合起来。这个操作可以看作是MapReduce的混洗(shuffle)操作，事实上，在使 用MapReduce执行引擎的情况下，它就是这样实现的。groupByKey()返回的 Crunch 类型是 PGroupedTable<K> V>，即 PCollection<Pair<KJ Iterable<V>>>,或者说是一个multi-map,其中每个键根据它的值与一个可迭代 (iterable)集合配对。

继续讨论前面的代码，如果我们按照键来对这个由“长度-字符串” (length-string) 映射构成的PTable进行分组，那么将会得到如下结果(其中方括号中的项表示一 个 iterable 集合):

PGroupedTable<IntegerJ String〉 c = b.groupByKey(); assertEquals(,,{(5> [apple]), (6, [banana,cherry])}’\ dump(c));

Crunch可以根据表的大小为groupByKey()操作设置分区数量(以减少MapReduce

中的任务数)。在大多数情况下，分区数量使用默认设置即可，不过，如果有必 要，也可以使用重载形式的groupByKey(int)来明确地设置分区数量。

\4. combineValues()

尽管从命名上看，PGroupedTable好像是PTable的一个子类，但实际上它不 是，所以你不能为PGroupedTable调用像groupByKey()这样的方法。这是因为 没有理由对一个已经按键分过组的PTable再次按键分组。你也可以把 PGroupedTable视为产生另一个PTable之前的中间表示。归根结底，之所以我 们需要按键来分组，是因为这样做可以针对每个键的值执行一些操作，而这正是 第四个基本操作的基础。

combineValues()的最常见的形式是以组合函数CombineFn<K,V>作为输入。 CombineFn<K，V>就是 DoFn<Pair<K，Iterable<V>>, Pair<K, V>>的简写，它 返回一个PTable<KJ V>0为了方便观察程序执行，可以考虑下面这个组合函 数，它把一个键对应的所有字符串值连接起来，并以分号作为分隔符：

PTable<Integer, String〉 d = c.combineValues(new CombineFn<Integer> String>() { ^Override

public void process(Pair<Integer^ Iterable<String>> input， Emitter<Pair<IntegerJ String>> emitter) {

StringBuilder sb = new StringBuilder();

for (Iterator i = input.second().iterator(); i.hasNext(); ) { sb.append(i.next()); if (i.hasNext()) { sb.append(";••); }

}

emitter.emit(Pair.of(input.first()， sb.toString()));

}

});

assertEqualsC.ns，apple), (6,banana;cherry)}"，dump(d));

字符串的连接操作是不可交换的，因此得到的结果没有确定性。这对于你的应 用程序来说，可能非常重要，也可能无关紧要。

由于在processO方法中使用了 Pair对象，因而使得这段代码显得有些杂乱。 Pair对象必须通过first()和second()调用展开，并创建一个新的Pair对象

以传出新的键-值对。这个组合函数对键来说没有做任何改变，因此我们可以使用 一种重载形式的combineValues(),它以Aggregator对象作为输入，并且仅对 值进行操作，而对键则不做任何改变地直接传递。更妙的是，我们可以使用 Aggregators类的内置的Aggregator实现来对所有字符串实施连接操作。于 是，该代码变为：

PTable<Integer> String〉 e = c.combineValues(Aggregators.STRING—CONCAT(";", false));

assertEquals("{(5,apple)，(6,banana;cherry)}.、 dump(e));

有的时候，你可能想聚合PGroupedTable中的值并返回一个与被分组的值的类型 不同的结果。这可以通过mapValues()方法来实现，它的MapFn可以把iterable 集合变换为另一种对象。例如，下面这段代码用于计算每个键所对应的值的个 数：

PTable<Integer, Integer〉 f = c.mapValues(new MapFn<Iterable<String>, Integer>() { ^Override

public Integer map(Iterable<String> input) { return Iterables.size(input);

}

}, ints());

assertEquals("{(5,l),(6,2)}", dump(f));

注意，虽然值都是字符串类型，但是应用这个map函数得到的结果却是整数类 型。这段代码利用了 Guava的Iterables类来计算iterable集合的大小。

你可能想知道，既然已经有强大的mapValues()方法，为什么还需要有

combineValues()方法呢？原



是 combineValues()可以被当作 MapReduce 的

combiner来运行，因此，通过在map端运行combineValues()方法可减少混洗操 作需要传输的数据量，从而使性能得到提升(参见2.4.2节)。由于mapValues()方 法被解释为parallelDo()操作，因此在这种情况下，它只能在reduce端运行， 所以不可能通过combiner来提高其性能。

最后，PGroupedTable 还有一种方法，称为 ungroup(),它用于把 PGroupedTable<K> V> 还原为PTable<K, V〉，即groupByKey()操作的逆操作。但是，ungroup()不属 于基本操作，因为它是通过parallelDo()来实现的。对一个PTable来说，先调 用groupByKey(),再调用ungroup()的效果相当于对该表执行了按键的不完全 排序，但一般来说还是利用Sort库更加方便，因为它可以实现完全排序(通常这 才是你所需要的)，并且还提供了一些排序选项。

###### 18.2.2类型

每个PCollection<S>都有一个关联的类，称为PType<S>,它用于封装有关 PCollection中的元素类型的信息。PType<S>指明了.PCollection中的元素的 Java类型为S,同时也给出从持久存储器读取到PCollection的序列化格式，以 及相反方向从PCollection写入持久存储器的序列化格式。

Crunch有两个PType家族：Hadoop Writables和Avro。大致来说，具体选择哪一 个PType家族取决于管线使用的文件格式：Writables用于顺序文件，Avro用于 Avro数据文件。这两个PType家族都可用于文本文件。管线也可以使用来自不同 家族的PTypes的混合体(因为与PType关联的是PCollection,而非管线)，但 这种做法通常没有必要，除非你眼前需要跨家族，比如文件格式转换。

一般情况下，Crunch会尽可能地隐藏不同序列化格式之间的差异，从而使编程人 员可以在代码中使用自己熟悉的Java的类型。(这样做的另一个好处是能够方便地 编写有关Crunch集合的库和工具程序，而不需要考虑它们属于哪个序列化家族。) 例如，我们可以用普通的Java String对象来表示从文本文件中读取的文本行， 而不需要使用Writable Text变量或Avro的UTF8对象。

PCollection所使用的PType在PCollection创建时指定，尽管有些时候它是 隐式指定的。例如，在读取文本文件时使用的是默认的Writables,如下面这段测 试代码所示：

PCollection<String> lines = pipeline.read(From.textFile(inputPath)); assertEquals(WritableTypeFamily•getlnstance(), lines.getPType().getFamily());

不过，也可以显式地指定使用Avro序列化，只需要把恰当的PType传递给 textFile()方法即可。此处，我们利用Avros类的静态工厂方法来创建一个 PType<String>的Avro序列化表示：

PCollection<String> lines = pipeline.read(From.textFileCinputPath^

Avros.strings()));

同理，对于那些将会创建新的PCollection的操作来说，必须为该操作指定 PType,并且要求它与PCollection的类型参数匹配。®例如，在前面的例子

①某些操作不要求指定PType，因为它们可以从相应的PCollection中推断出PType。例如， filter()返回的PCollection应当与输入的PType相同。

中，parallelDo()操作从PCollection<String>中提取整数型的键，并将其变 换为 PTable<Integer, String〉，且指明匹配的 PType 为

tableOf(ints()3 strings())

上述三种方法都是从Writables中静态导入的。

1.记录和元组

在处理由多个字段构成的复杂对象时，可以选择使用Crunch的记录(record)或者元 组(tuple)。记录就是通过名称来访问字段的类，比如Avro的GenericRecord对 象、旧式的纯Java对象(对应于Avro的Specific或Reflect对象)或者是定制的 Writable。另一方面，元组是通过位置来访问字段的类。Crunch为此提供了 Tuple接口，另外还有一些可用于少量元素组成元组的便捷类：Pair<K, V>、 Tuple3<Vl, V2, V3>、Tuple4<Vl, V2, V3, V4>,以及具有任意固定数量的 值的元组TupleN。

用记录写的Crunch程序更加易于阅读和理解，因此，我们应当尽可能使用记录而 非元组。如果气象记录用由年份、温度和站点ID字段构成的WeatherRecord类 来表示，那么使用

EmittersPair<Integer, WeatherRecord>>

将会比

Emitter<Pair<Integer, Tuple3<Integer> Integer, String>>

更简单。

WeatherRecord清楚地表达了它是什么，元组则不同，它的类型名称没有传达任 何有意义的信息。

正如这个例子所暗示的，我们不可能做到完全避免使用Crunch的Pair对象， 为它是Crunch用来表示一个表集合的基本方法之一，回想一下，PTable<K, V> 其实就是PCollection<Pair<K, V>>。不过，在很多情况下还是可以想办法限 制Pair对象的使用，这样做可以让代码更加易读。例如，在创建一个表时，如果

表中的值与PCollection的值相同，那么使用PCollection的by()方法要比使 用parallelDo()方法更好(参见18.2.1节对parallelDo()的讨论)，或者使用以 Aggregator 对象作为输入的 PGroupedTable 的 combineValues()方法(参见 18.2.1节中对combineValues()的讨论)，而不是使用CombineFn方法。

在Crunch管线中使用记录的最便捷的方式是定义一个其字段能够被Avro Reflect 序列化的Java类和一个无参数的构造函数，比如说下面的WeatherRecorcJ类：

public class WeatherRecord { private int year; private int temperature; private String stationld;

public WeatherRecord() {

}

public WeatherRecord(int year, int temperature^ String stationld) { this.year = year; this.temperature = temperature; this.stationld = stationld;

}

"…getters elided }

有了上述定义，就可以直观地从PCollection<String>生成PCollection 〈WeatherRecord〉，并利用parallelDo()方法把每一行文本解析到 WeatherRecord 对象中：

PCollection<String> lines = pipeline.read(From•textFile(inputPath)); PCollection<WeatherRecord> records = lines.parallelDo(

new DoFn<StringJ WeatherRecord>() {

NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void process(String input, Emitter<WeatherRecord> emitter) { parser•parse(input); if (parser,isValidTemperature()) {

emitter.emit(new WeatherRecord(parser.getYearInt(), parser.getAirTemperature()parser.getStationld()));

}

}

Avros.records(WeatherRecord.class));

如前面这段代码所示，recordsO方法为Avro Reflect数据模型返回一个Crunch PType，不过，它也支持的Avro的Specific和Generic数据模型。如果要使用 Avro Specific,那么应当先利用Avro模式文件来定义一个定制类型并生成相应的 Java类，然后用生成的Java类调用records()0对于Avro Generic,则应当声明 一个 GenericRecord 类0

Writables也提供了 records()方法以用于定制类型，不过，它们的定义更加繁 琐，因为你必须编写自己的序列化逻辑(参见5.3.3节)。

有了记录的集合，就可以用Crunch类库或者我们自己的处理函数对其进行计算。 例如，下面这段代码按照字段的定义顺序来执行对气象记录的完全排序，按照先 年份，再温度，最后站点ID的顺序：

PCollection<WeatherRecord> sortedRecords = Sort.sort(records);

###### 18.2.3源和目标

本节将介绍Crunch的各种数据源和目标，以及这些源和目标的用法。

1.读取源

Crunch管线的起点是一个或多个Source<T>实例，它们指明输入数据的存储位置 以及PType<T>。对于读取文本文件这种简单情况而言，使用Pipeline的 readTextFileO方法效果就很好，但是对其他类型的数据源则需要使用read() 方法，它以Source<T>对象为输入。实际上，下面的代码：

PCollection<String> lines = pipeline.readTextFile(inputPath);

简写形式如下：

PCollection<String> lines = pipeline.read(From.textFile(inputPath));

在org.apache.crunch.io包中的From类就像是各种文件源静态工厂方法的集 合，而文本文件只是其中的一个例子。

另一种常见情况是读取由Writable键-值对所构成的顺序文件。此时，数据源是 内容为键-值对的TableSource<K, V〉，并返回一个PTable<K, V〉。例如，由 IntWritable类型的键和Text类型的值构成的顺序文件所产生的是一个 PTable<Integer， String>：

TableSource<IntegerJ String> source =

From.sequenceFile(inputPath, Writables.ints()， Writables.strings());

PTable<Integer> String> table = pipeline.read(source);

同样，也可以把Avro数据文件读取到PCollection中，如下所示：

Source<WeatherRecord> source =

From.avroFile(inputPath^ Avros.records(WeatherRecord.class)); PCollection<WeatherRecord> records = pipeline.read(source);

通过 From 类的 formattedFile()方法，任何 MapReduce(在新 MapReduce API 中 的)FilelnputFormat都可被用作TableSource，这使得Crunch能够访问大量

Hadoop支持的不同文件格式。Crunch的数据源并不止From类中给出的这几种， 另外还包括以下几种。

•    AvroParquetFileSource,用于以 Avro PType 读取 Parquet 文件。

•    FromHBase的table()方法，用于把HBase表中的行读取到PTable< ImmutableBytesWritable, Result:^ 0ImmutableBytesWritable 是 HBase的一个类，用于字节形式表示的行键(row key),并在Result中包含行 扫描得到的单元格，且通过配置能够仅返回某个特定列或列族的单元格。

2.写入目标

要想把PCollection写入目标非常简单，只需在调用PCollection的write() 方法时使用所期望的Target即可。最常见的目标是一个文件，并且可以通过To 类的静态工厂方法来选择文件类型。例如，下面这段代码用于在默认文件系统的 output目录下写一个Avro文件：

collection.write(To.avroFile("output"));

而上述代码即为以下代码的简写：

pipeline.write(collection， To.avroFile("output"));

由于PCollection被写入的是Avro文件， 则管线运行将会失败。

此必须使用Avro家族的PType，否



在To类工厂中有一些方法可用于创建文本文件、顺序文件以及任何MapReduce FileOutputFonmat 格式的文件。对于 Parquet 文件格式(AvroParquetFileTarget^fl HBase(ToHBase)而言，Crunch 也有内置的 Target 实现。

Crunch试图以顺其自然的方式向目标文件写入集合的类型。例如，使用Pair 记录模式把PTable写入Avro文件，其中键和值的字段与PTable匹配。同样， PCollection的值作为顺序文件的值写入(键为空)，而PTable写入文本文件时 使蹦鋪分醐键和值。

3.输出已存在

如果基于文件的目标已经存在，那么在调用write()方法时Crunch将会引发 CrunchRuntimeException错误。这种行为保留了 MapReduce的做法，这是一种 保守行为，也就是说，除非用户明确指示，否则不会覆盖已存在的输出(参见

2.3.3 节）。

我们可以向write（）方法传递一个标志，用于指示覆盖已存在的输出，示例如下：

collection.write（To.avroFile（"outputH, Target.WriteMode.OVERWRITE）;

倘若owput已存在，那么在管线运行之前将其删除。

另外还有一种写模式是APPEND模式，它在输出目录下添加一个新文件' 并保留 上一次运行产生的任何现有文件完好无损。Crunch负责在文件名中利用唯一标识 符来避免新产生的文件覆盖先前已产生的文件的可能性。®

最后一种写模式是CHECKPOINT模式，它用于把当前工作保存在一个文件中，从

而使新的管线可以从检查点而不是管线的起点开始执行。这种模式的详情将在 18.3.5节介绍。

4.组合的源和目标

有的时候，你可能希望一边写入目标，一边又将其作为源来读取（即在同一个程序 的另一个管线中）。对于这种情况，Crunch提供了一种既是Source<T>又是Target 的SourceTarget<T>接口。在At类中有一些静态工厂方法可用于为文本文件、顺序文 件和Avro文件创建SourceTarget实例。

###### 18.2.4 函数

不论什么样的Crunch程序，其关键部位一定是那些能够把一种PCollection变 换为另一种PCollection的函数（用DoFn来表示）。本节将讨论在写自定义函数 时需要注意的一些事项。

1.函数的序列化

在写MapReduce程序时，需要把mapper和reducer代码打包到作业的JAR文件 中，从而使Hadoop能够通过搜索类路径找到用户代码（参见6.5.1节）。Crunch则 采取了不同的方式。在管线执行时，所有DoFn实例都被序列化到一个文件中，并 通过Hadoop的分布式缓存机制把该文件分发给各任务节点（参见9.4.2节），然后

①    虽然名为APPEND，但这种模式并不会把内容直接附加到已存在的输出文件中。

②    HBaseTarget不检査已存在的输出，因此它的行为就如同使用APPEND模式一样。

任务本身反序列化，从而使得这些DoFn能够被调用。

其结果是，作为用户的你不需要做任何打包工作，只需要确保自己的DoFn实现能 够通过标准的Java序列化机制进行序列化。® 在大多数情况下，没有什么额外工作需要做，因为DoFn的基类被声明为 java.io.Serializable接口实现。如果你的函数是无状态的，那么也就是说没 有字段需要序列化，因此在序列化时不会出现问题。

不过，还是有一些需要注意的事项。假如DoFn作为一个内部类(也称为非静态嵌 套类，如匿名类)被定义在一个没有实现Serializable的外部类中，那么就会出 现问题：

public class NonSerializableOuterClass {

public void runPipeline() throws IOException {

II ...

PCollection<String> lines = pipeline.readTextFile(inputPath); PCollection<String> lower = lines>parallelDo(new DoFn<String, String>() {

^Override

public void process(String input, Emitter<String> emitter) { emitter.emit(input.toLowerCase());

}

}, strings。)；

}

}

由于内部类可隐式地引用自己的类实例，因此，如果封装该函数的类没有被序列

化，那么该函数也没有序列化，于是管线执行将会出现 CrunchRuntimeException错误。要想解决这个问题很简单，可以把该函数定义

为一个(命名的)静态嵌套类或者一个顶级类，也可以让封装该函数的类实现 Serializable 接口。

另一个问题是，如果函数依赖于一个以实例变量形式表示的非序列化的状态，并 且它的类没有Serializable,那么，在这种情况下可以将非序列化的实例变量 标记为transient(瞬态)，从而使Java不去尝试対其进行序列化，然后在DoFn 的initialize()方法中设置这个变量。Crunch会在首次调用process()方法之 前先来调用initialize()方法：

①参见文档，网址为 <http://bit>. ly/interface^serializable0

public class CustomDoFn<S^ T> extends DoFn<S1 T> {

transient NonSerializableHelper helper;

^Override

public void initialize。{ helper = new NonSerializableHelper();

}

^Override

public void process(S input, Emitter<T> emitter) { // use helper here

}

}

虽然我们在这里没有举例，不过，也可以通过其他非瞬态实例变量(比如字符串)来 传递状态，以初始化瞬态的实例变量。

2.对象重用

在使用MapReduce时，reducer的值迭代器中的对象是可重用的，其目的是为了提 高效率(避免对象分配的开销)。对于PGroupedTable的combineValues()和 mapValues()方法的迭代器来说，Crunch具有相同的行为。因此，如果你想要在 迭代器的外部引用一个对象，那么就应当复制该对象，以避免对象标识错误。

为了说明这个问题，我们编写了一段通用的实用程序，它为PTable中的每个键查 找唯一值的集合，如范例18-2所示。

范例18-2.为PTable中的每个键查找唯一值集合

public static <K， V> PTable<K， Collection<V>> uniqueValues(PTable<K, V> table) { PTypeFamily tf = table.getTypeFamily(); final PType<V> valueType = table.getValueType(); return table.groupByKey().mapValues(•’unique",

new MapFn<Iterable<V>, Collection<V>>() {

^Override

public void initialize。{

valueType.initialize(getConfiguration());

}

^Override

public Set<V> map(Iterable<V> values) {

Set<V> collected = new HashSet<V>(); for (V value : values) {

collected.add(valueType.getDetachedValue(value));

}

return collected;

}

tf.collections(table.getValueType()));

其基本思路是，先按键分组，再遍历与每个键关联的所有值，并把唯一值收集到 一个可以自动删除重复值的Set中。由于我们希望在迭代器之外保存这些值，因 此在把这些值放入Set之前，需要先复制每个值。

幸运的是，我们并不需要编写代码来复制每一种可能出现的Java类，实际上，本 例使用的是Crunch提供的getDetachedValue()方法。根据表中的值的类型可以 得到PType,有了 PType就可以通过getDetachedValue()方法对Java类进行复 制。请注意，我们还是需要在DoFn的initialize()方法中初始化PType，从而 使这个PType能够访问配置以执行复制操作。

对于像Strings或Integers这样的不可变对象，调用getDetachedValue()方 法实际上是一个空操作，但是对于可变的Avm或Writable对象，调用 getDetachedValue()方法则会为每个值做深层复制。

###### 18.2.5物化

物化(Materialization)是让PCollection中的值变得可用的过程，只有物化后的值 才能够被程序读取。例如，你可能希望读取一个(通常很小的)PCollection的所 有值并将其显示出来，或者把这些值发送给程序的其他部分，而不是把它们写入 Crunch目标。物化PCollection的另一个理由是为了将PCollection的内容作

为下一个处理步骤的判断基础。例如，测试一个迭代算法的收敛性，详情参见 18.3.4 节。

有多种方法可以物化PCollection,其中最直接的方法是调用materialize(), 它返回一个包含了 PCollection的值的Iterable集合。如果PCollection还没有 被物化，那么Crunch必须执行管线以确保PCollection中的对象都已经被计算 并存储到一个临时文件中，这样才有可能对这些值进行遍历操作。®

下面这段程序用于使文本文件中的文本行小写字母化：

Pipeline pipeline = new MRPipeline(getClass());

PCollection<String> lines = pipeline.readTextFile(inputPath);

PCollection<String> lower = lines.parallelDo(new ToLowerFn(), strings());

①在这个例子中，并没有显式地调用run()或done()来运行管线，不过在管线运行完成后调用 done()仍然不失为好的做法，这样可以使中间文件得到处理。

Iterable<String> materialized = lower.materialize(); for (String s : materialized) { // pipeline is run

System.out.printIn(s);

}

pipeline.done();

上面这段程序利用ToLowerFn函数对文本文件中的行进行变换操作，ToLowerFn 函数是单独定义的，因此它可以重复使用：

public class ToLowerFn extends DoFn<String, String〉 { ^Override

public void process(String input, Emitter<String> emitter) { emitter.emit(input•toLowerCase());

}

}

变量 lower 调用 materialize()并返回一个 Iterable<String>。但是，调用 materialize()并不会导致管线运行，只有从Iterable中创建出一个Iterator 后(通过每个for each循环语句隐式地创建)，Crunch才会执行管线。当管线执 行完毕，就可以对已物化的PCollection执行迭代过程，在本例中就是把小写字 母化的文本行打印到控制台。

你可能会认为PTable的materializeToMap()方法在行为上类似干 materialize()方法，不过，这两种方法之间存在两个重要区别。首先， materializeToMap()返回的是Map<K, V〉，而不是迭代器(iterator)，也就是说整

张表会被立刻加载到内存中，而这种行为对于较大的集合来说是应当避免的。第 二，虽然PTable是多映射(multi-map)的，但Map接口并不支持单个键具有多个 值，因此，如果这张表中的一个键有多个值，那么在返回的Map中只能保留一个 值，其余值将丢失。

为了避免上述问题，我们只需要简单地为一个表调用materialize()方法以获得

Iterable<Pair<K, V>>即可。

\1. PObject

物化PCollection的另一种方式是使用PObject。PObject<T>是一个被标志戈 future的对象，也就是说程序在创建PObject时，类型为T的值的计算可能还沒 有完成。计算结果可以通过调用PObject的getValue()方法获取，而在计算完 成(通过Crunch管线的执行)并返回值之前，getValue()方法将处于阻塞状态。

调用 PObject 的 getValue()方法类似于调用 PCollection 的 materialize()

方法，因为它们都会触发管线运行来物化所需的集合。事实上，我们可以使用 PObject来重写文本文件行小写字母化的程序，如下所示：

Pipeline pipeline = new MRPipeline(getClass());

PCollection<String> lines = pipeline.readTextFile(inputPath);

PCollection<String> lower = lines.parallelDo(new ToLowerFn(), strings());

PObject<Collection<String>> po = lower•asCollection();

for (String s : po«getValue()) { // pipeline is run System.out.printIn(s);

}

pipeline.done();

asCollection()方法把 PCollection<T>变换为普通的 Java Collection<T>。® 由于本例使用的是PObject的方式，因此，如果有必要，这个变换操作可以被推 迟到程序的稍后某刻再执行。本例在获得PObject后立即调用了 PObject的 getValue()方法，以便对getValue()方法返回的Collection进行遍历操作。

![img](Hadoop43010757_2cdb48_2d8748-225.jpg)



asCollection()会把PCollection中的所有对象都物化在内存中，因而此方法 只适用于较小的PCollection,比如计算结果中只包含少数几个对象时。而 materialize()的使用则没有这样的限制，它对Collection进行遍历操作，而 不是把整个集合一次性存储在内存中。

在写作本章内容时，Crunch还没有提供任何方法用于在管线执行当中检査 PObject，比如在DoFn内部检査PObject0只有在管线执行完毕后，才能对 PObject进行检査。

##### 18.3管线执行

在管线构建期间，Crunch会建立一个内部执行计划。这个执行计划要么由用户显 式地运行，要么由Crunch隐式地运行(参见18.2.5节对物化的讨论)。这个执行计 划是由PCollection操作构成的一个有向无环图，凡在计划内的每个 PCollection都与产生它的操作之间存在引用关系，并且除了这些PCollection 之外，还包含各种操作的参数。此外，每个PCollection都有一个内部状态，用 于记录它是否已被物化。

①PTable<K, 7>的asMap()方法也能返回一个类型为PObject<Map<K, V>>的对象。

###### 18.3.1运行管线

通过调用Pipeline的rim()方法可显式地执行管线操作，步骤如下。

首先进行优化处理，把执行计划分为若干阶段。优化的细节取决于执行引擎。也 就是说，同样的计划，针对MapReduce的优化与针对Spark的优化是不同的。

接下来，执行优化计划中的各阶段(在有可能的情况下并行执行)，从而使结果得到 的PCollection被物化。将要写入Target的PCollection被物化为目标本 身，有可能是HDFS的一个输出文件，也可能是HBase的一张表。而中间 PCollection的物化则是通过把该集合的序列化对象写入一个HDFS的临时中间 文件来实现的。

最后，runO方法向调用者返回一个PipelineResult对象，其中包含了刚才运 行的每个阶段的信息(持续时间以及MapReduce计数器®)，以及管线运行是否成功

(使用 succeeded()方法)。

clean()方法用于清除物化PCollection时创建的所有临时中间文件。clean()

方法的调用应当在管线执行完毕后，以释放HDFS的磁盘空间。clean()方法有 一个布尔参数，用于指示是否需要强行删除临时文件。如果这个参数设置为 false,那么临时文件将在管线的所有目标均已创建完毕后才能删除。

与其先调用run()，再调用clean(false),还不如直接调用done()更方便，它 们具有相同的效果，包括指示管线运行启动，并在不需要临时文件时进行清理。

1.异步执行

run()方法是一种阻塞调用，也就是说在返回之前，它会等待直至管线执行完毕。 runAsync()方法是run()的配对方法，只不过它在管线运行启动后即刻返回。 run ()方法也可用以下方法来实现：

public PipelineResult run() {

PipelineExecution execution = runAsync(); execution.waitUntilDone();

return execution.getResult();

}

有些时候，你可能希望直接使用runAsyncO方法。最典型的例子是当你需要在等

①可以利用DoFn的increment()方法来增加一些定制的Crunch计数器。

待管线执行的同时运行其他代码，并且希望利用PipelineExecution提供的一 些方法(例如检査执行计划、发现执行状态或者在中途停止管线执行)时。

PipelineExecution 实现了 Future<PipelineResult> (来自 java.util. concurrent),它提供下述简单代码来执行后台工作：

PipelineExecution execution = pipeline.runAsync();

// meanMhiLej do other things here PipelineResult result = execution.get(); // bLocks

2.调试

当发生故障时，通过调用Pipeline的enableDebug()方法可以获取MapReduce 任务日志中的详细调试信息。

另一个有用的设置是配置属性crunch.log.job.progress。假如把这个属性设 置为true,那么MapReduce作业处理过程的每个阶段都会被记录到控制台：

pipeline.getConfiguration().setBoolean("crunch.log.job.progressHtrue);

###### 18.3.2停止管线

有时候，你可能需要在管线完成之前停止它。这也许是因为管线已经启动后，你 才突然意识到代码中存在一个编程错误，所以希望停止管线的运行，等问题解决 之后再重新启动。

如果管线是通过调用阻塞进程run()或done()来完成的，那么使用标准的Java 线程中断机制可以让run()或done()返回。但是，在集群上运行的所有作业仍将 继续运行，因为它们不会被Crunch杀死。

事实上，为了正确停止管线，需要异步启动该管线，以保留对PipelineExecution对象 的引用：

PipelineExecution execution = pipeline，runAsync();

在这种情况下，停止管线及其作业就只需要调用PipelineExecution上的 kill()方法，并等待管线完成：

execution.kill(); execution.waitUntilDone();

此时，PipelineExecution 的状态是 PipelineExecution.Status. KILLED,

且管线在此之前运行在这个集群上的所有作业都将被杀死。此模式的一种有效的 应用方式是将其应用于java虚拟机的关机hook中，于是在使用组合键Ctrl+C来 关闭java应用程序时就可以安全地终止当前正在运行的管线。

PipelineExecution 实现的是 Future<PipelineResult>,因此，调用 kill(〉可 以获得与调用CanCel(trUe)相同的效果。

###### 18.3.3查看Crunch计划

在某些时候，查看优化的执行计划是非常有必要的，或者至少是启发性的。下面 这段代码给出了如何获得一个以字符串形式表示的管线操作图的DOT文件，并将 其写入一个文件(使用Guava的Files类)。这段代码的重点就在于它能够访问正 在异步运行的管线所返回的PipelineExecution：

PipelineExecution execution = pipeline.runAsync();

String dot = execution.getPlanDotFile();

Files.write(dot, new File(Hpipeline.dotH)Charsets.UTF_8); execution.waitUntilDone(); pipeline.done();

为了便于查看，需要使用dot命令行工具把DOT文件转换成图形格式，如 PNG。例如，调用下面的命令可以把当前目录下的所有DOT文件都转换为PNG

格式，因此pipeline.dot被转换为一个名为pipeline.dot.png的文件:

% dot -Tpng -0 *.dot

有一个技巧可以在不具备PipelineExecution对象时获取DOT文件，比如同步 ■L 或隐式地运行管线时(参见18.2.5节)。Crunch可以把DOT文件的表示存储在作 H业M巾，⑽在管馳械成后触：

PipelineResult result = pipeline.done();

String dot = pipeline.getConfiguration().get("crunch.planner.dotfile");

Files.write(dot, new File("pipeline.dot"), Charsets.UTF一8);

下面来看一个重要管线的计划，它用于为存储在inputPath目录下的文本文件计 算词频统计直方图(参见范例18-3)。这个管线若作为产品，可以变得很长，包含 几十个MapReduce作业，但是下面这段代码虽然不长，却说明了 Crunch计划的 一些特点。

范例18-3. —个用于计算词频统计直方



的Crunch管线



PCollection<String> lines = pipeline.readTextFile(inputPath); PCollection<String> lower = lines.parallelDo(nlower", new ToLowerFn(), PTable<String, Long〉 counts = lower.count();

PTablecLong^ String〉 inverseCounts = counts.parallelDo("inverse"， new InversePairFn<String, Long>(), tableOf(longs(), strings()));

PTable<Long> Integer〉 hist = inverseCounts

.groupByKeyO

.mapValues("count values", new CountValuesFn<String>(), ints()); hist.write(To.textFile(outputPath)^ Target.WriteMode.OVERWRITE); pipeline.done();



strings());



这个管线计划的框图如图18-1所示。



![img](Hadoop43010757_2cdb48_2d8748-226.jpg)



18-1.用于计算字数的直方图的Crunch管线的计划

源和目标以文件夹图标的形式在图中呈现。图的顶部显示了输入源，底部显示了 输出目标。我们可以看到，图中共有两个MapReduce作业(分别被标记为Crunch /和Crunch Job 2),其中一个作业的输出被写入到由Crunch生成的临时顺序 文件中，而另一个作业则把该文件作为输入读取。管线执行完毕后，调用clean() 删除临时文件。

Crunch Job 2(实际上，它才是先运行的作业)由map阶段和reduce阶段组成，如图 中标注框所示。每个map和reduce都被分解为更小的操作，它们在图中所标记的 名称对应于代码中Crunch基本操作的名称。例如，map阶段的第一次 parallelDo()操作被标记为lower,它的功能就是小写字母化PCollection中 的字符串。

![img](Hadoop43010757_2cdb48_2d8748-227.jpg)



为管线所使用的PCollection重载方法以及相关类命名的时候，应当取一些对 操作来说有意义的名称。这样可以增强计划框图的可读性。

在小写字母化操作之后，程序接下来所做的变换是通过内置的count()方法产生 一个PTable,其中包含每个唯一字符串出现的次数。count()方法实际上执行了 三个Crunch基本操作：一个是称为的parallelDo()操作；另一

个是在图中标记为的groupByKey()操作；还有一个是被标记为的 combineValues()操作。

每个GBK操作都将作为MapReduce的混洗(shuffle)步骤实现，而groupByKey() 和combineValues()操作则在reduce阶段运行。d從rega/e.⑺的 parallelDoQ操作在map阶段运行，但是请注意，它与lower操作处于同一个 map中，因为Crunch planner试图最小化管线需要运行的MapReduce作业数量。 同样，/wverse parallelDo()操作属于前一个reduce阶段。

最后的变换操作是对PTable进行逆向计数，以找出每个计数出现的频率。例 如，如果字符串apple和orange都出现了三次，那么计数3的频率为2。这种 变换是另一种形式的GBK操作，因此，必须使用一个新的MapReduce作业 (Crunch Job 1),此作业中的第二个操作是被命名为va/wes的mapValues()

①这种优化称为parallelDo fusion,要想了解详细情况，请参阅本章开头引用的FlumeJava文 献，网址为[http://bit.ly/data-parallelpipelines](http://bit.ly/data-parallelpipelines,%e5%85%b6%e4%b8%ad%e8%bf%98%e5%8c%85%e6%8b%ac%e4%b8%80%e4%ba%9bCrunch%e4%bd%bf%e7%94%a8%e7%9a%84%e5%85%b6%e4%bb%96%e4%bc%98%e5%8c%96%e6%96%b9)[,其中还包括一些Crunch使用的其他优化方](http://bit.ly/data-parallelpipelines,%e5%85%b6%e4%b8%ad%e8%bf%98%e5%8c%85%e6%8b%ac%e4%b8%80%e4%ba%9bCrunch%e4%bd%bf%e7%94%a8%e7%9a%84%e5%85%b6%e4%bb%96%e4%bc%98%e5%8c%96%e6%96%b9) 法。需要注意的是，parallelDo fusion允许在不损失任何效率的条件下，将管线操作分解为 更小的、逻辑上独立的函数，因为Crunch要把它们融合为尽可能少的MapReduce阶段。

操作。mapValues()操作是一种简单的parallelDo()操作，因此可以在reduce

中运行。

请注意，图中省略了 Crunch Job 1的map阶段， Crunch基本操作。

为在这个阶段没有运行任何



###### 18.3.4迭代算法

PObjects的一种常见用法是检査迭代算法的收敛性。分布式迭代算法的一个典型 的例子是PageRank算法，它为一组互相链接的网页(如World Wide Web)排名打 分，以体现每个网页的相对重要性。®在用Crunch实现的PageRank中，控制流如

下所示:

PTable<String， PageRankData> scores = readllrlsCpipeline^ urllnput); Float delta = 1.0f; while (delta > 0.01) {

scores = pageRank(scores， 0.5f);

PObject<Float> pDelta = computeDelta(scores); delta = pDelta.getValue();

即使我们不了解PageRank算法本身的操作细节，也可以理解作为高级程序的 Crunch是如何运行的。

该程序的输入是一个文本文件，其中每一行文本包含了两个URL： 一个是网页的 URL,另一个是该网页的出站超链接。例如，下面这个文件的含义是指网页A有 超链接到达网页B和网页C，并且网页B有超链接到达网页D:

www.A•com [www.B.com](http://www.B.com) [www.A.com](http://www.A.com) [www.C.com](http://www.C.com) [www.B.com](http://www.B.com) [www.D.com](http://www.D.com)

重新回到代码，第一行代码读取输入文本并为每个唯一的网页创建一个初始 PageRankData对象。PageRankData是一个简单的Java类，其字段包括得分字 段、上次得分字段(用于检査收敛性)以及出站链接列表：

public static class PageRankData { public float score; public float lastScore; public List<String> urls;

①有关详细信息，请参见维基百科，网址为<http://en>. Wikipedia. org/wiki/PageRank0

II... methods eLided

这个算法的目标是计算每个网页的得分，并通过得分来表示网页的相对重要性。 所有网页一开始时的得分相同，每个网页的初始得分都是1，且上次得分为0。使 用Crunch的分组操作，按第一个字段(网页)对输入进行分组，然后把值(即出站链 接)聚合到一张列表中，就可以创建一个出站链接列表。'

迭代操作通过使用常规的Java while循环语句实现。每次迭代循环都要调用 PageRank()方法来更新得分。PageRank()方法使用了一系列的Crunch操作以实 现PageRank算法。如果前一组得分与新得分之间的增量(delta)小于一个足够小的 量(如0.01)，那么说明得分已收敛，算法结束。这个增量值是通过 computeDelta()方法计算得到的，它是一个Crunch的聚合函数，用干为集合中 的所有网页找出得分的最大绝对差。

那么，管线在什么时候运行？答案是在每次调用pDelta.getValue()时。在第一 次循环中，由于PCollection都没有物化，因此为了计算增量，必须运行 readUrls()、pageRank()和computeDelta()作业。而后续的迭代循环则只需 要运行用于计算新得分(pageRank ())和增量(computeDelta())的作业。

![img](Hadoop43010757_2cdb48_2d8748-228.jpg)



对于此管线而言，如果在调用pageRank()后立即调用scores .materialize (). iterator^)，那么Crunch planner优化执行计划的工作做得不错。它确保 scores表被显式地物化，因此在下一次迭代循环时它就可被用于下一个执行 计划。如果不调用materialize(),这段程序也能产生相同的结果，但效率比 较低，因为planner可能会选择物化不同的中间结果，那么在下一个迭代循环 中，有些计算必须重新执行才能获得scores并传递给pageRankO。

###### 18.3.5



###### 给管线设置检查点



在上一节中我们看到，先前任意一次运行中被物化的PCollection,只要是在同 一管线中，都能被Crunch重用。不过，假如你创建了一个新的管线实例，那么它 不会自动分享来自其他管线的任何物化的PCollection,哪怕输入源相同。这就 使得管线的开发变得相当耗时，因为即使已经接近管线底部，只要某个计算有细 微的修改，Cmnch也要从头开始运行这个新的管线。

解决的办法是在持久存储器(通常是HDFS)上设置一个PCollection检査点，以

①在Crunch综合测试包的一个名为PageRankIT的类中可以找到完整的源代码。

便Crunch可以从这个检査点启动新的管线。

回顾范例18-3中用于计算词频统计直方图的Crunch计划。我们看到，Crunch planner把该管线划分为两个MapReduce作业。如果再次运行程序，Crunch将重 新运行这两个MapReduce作业并覆盖原来的输出，因为WriteMode被设置为

关于 Crunch 545

OVERWRITE

相反，如果我们设置了检査点inverseCounts,那么第二次运行只会启动一个 MapReduce作业（即用于计算hist的作业，因为它完全在inverseCounts后才执 行）。设置检查点实际上就是把一个PCollection写入目标，并将其WriteMode 设置为 CHECKPOINT：

PCollection<String> lines = pipeline.readTextFile(inputPath); PTable<String, Long> counts = lines.count();

PTable<Long1 String> inverseCounts = counts.parallelDo(

new InversePairFn<String> Long>(), tableOf(longs()，strings。)); inverseCounts•write(To.sequenceFile(checkpointPath)J

Target.WriteMode.CHECKPOINT);

PTable<LongJ Integer〉 hist = inverseCounts

.groupByKey()

.mapValues(new CountValuesFn<String>()} ints()); hist.write(To.textFile(outputPath), Target•WriteMode.OVERWRITE); pipeline.done();

Crunch会比较输入文件的时间戳与这些检查点文件的时间戳，如果有任何输入文 件的时间戳晚于检査点文件的时间戳，那么自动重新计算相关检查点，因而不存 在使用了管线中的过时数据的风险。

由于检査点在两次管线运行之间被持久保存，因此它们不会被Crunch清除，所以 一旦代码如愿以偿地产生了你所预期结果后，你应当删除这些检査点。

##### 18.4 Crunch 库

Crunch自带了一组功能强大的库函数，在org.apache.crunch.lib包中，表 18-1对这些函数进行了简单的概括。

Crunch的一个强项是如果它没有提供你所需的函数，那么很简单，你可以自己编 写，通常只需要几行Java语句。前面的范例18-2给出了一个通用函数的例子，用 干査找PTable中的唯一值。

Aggregate 类有 length()、min()、max()和 count()方法，而在 PCollection

上也有相对应的方法。类似地，Aggregate类的top()方法(及其派生的 bottom())、collectValues()方法，还有］oin 的 join()方法以及 Cogroup 的 cogroup()方法，对于PTable来说都是完全一样的。

表 18-1. Crunch 库

| 类Aggregate | 方法length()    | 描述返回封装在PObject中的PCollection的元素数目               |
| ----------- | --------------- | ------------------------------------------------------------ |
|             | min()           | 返回封装在PObject中的PCollection的最小值元素                 |
|             | max()           | 返回封装在PObject中的PCollection的最大值元素                 |
|             | count()         | 返回输入PCollection中的元素(唯一的)与其计数的 映射表         |
|             | top()           | 返回由PTable的前TV位或后TV位键-值对构成的 表，按值排序       |
|             | collectValues() | 按表中的键分组，并把值聚合到Java Collection 中，返回 PTable<K, Collection<V>> |
| Cartesian   | cross()         | 计算两个PCollection或PTable的向量积                          |
| Channels    | split()         | 把    PCollection<Pair<T,    U>> 拆分为Pair<PCollection<T>, Collection<U>> |
| Cogroup     | cogroup()       | 按键分组，把元素聚合为两个或多个PTable                       |
| Distinct    | distinct()      | 创建一个删除了重复元素的新PCollection或 PTable               |
| Doin        | join()          | 按键对两个PTable执行内连接。另外还有左连接、 右连接和全连接方式 |
| Mapred      | map()           | 对 PTable<Kl, Vl>运行(旧的 API 的)mapper,以产 生 PTable<K2, V2> |
|             | reduce()•       | 对 PGroupedTable<Kl, Vl> 运行(旧的 API 的)reducer,以产生 PTable<K2, V2> |
| Mapreduce   | map()reduce()   | 类似于Mapred,只不过使用新的MapReduce API                     |
| PTables     | asPTable()      | 把 PCollection<Pair<K, V>>变换为 PTable<K, V>                |
|             | keys()          | 返回一个PCollection,其中包含PTable的键                       |
|             | values()        | 返回一个PCollection,其中包含PTable的值                       |
|             | mapKeys()       | 对PTable的所有键应用某个映射函数，并保持值不变               |
|             | mapValues()     | 对PTable或PGroupedTable的所有值应用某个映 射函数，并保持键不变 |
| Sample      | sample()        | 以指定的概率，每个元素独立选择创建一个                       |

| PCollection 样本  |                                             |
| ----------------- | ------------------------------------------- |
| reservoirSample() | 创建一个指定大小的PCollection样本，其中每个 |

元素出现的概率相同

续表

Secondary Sort    sortAndApply（）    按照先 K 后 VI 的顺序对 PTable<K，Pair<VlJ

V2>>进行排序，然后应用函数以给出一个输出 PCollection 或 PTable

![img](Hadoop43010757_2cdb48_2d8748-229.jpg)



difference()

返回包含两个PCollection的差集的PCollection

| intersection) | 返回包含两个PCollection的交集的PCollection                   |
| ------------- | ------------------------------------------------------------ |
| comm()        | 返回一个三元组PCollection，它对两个 PCollection中的元素进行分类，以判断每个元素是 仅出现在第一个集合中，还是仅出现在第二个集合 中，或者是在两个集合中都有。（类似于UNIX的 comm命令） |

| Shard1 | shard() | 创建一个PCollection,其中包含的元素与输入 PCollection完全相同，只不过通过指定数量的文件 进行分区（共享） |
| ------ | ------- | ------------------------------------------------------------ |
| Sort   | sort()  | 按照元素的自然顺序，对一个PCollection执行升 序（默认）或降序的完全排序。另外还有一些方法可以 按键对PTable排序，或者以指定的顺序按照列的子 集来对Pair或元组的集合进行排序 |

范例18-4中的代码使用了一些聚合方法。

范例18-4. PCollection和PTable中的聚合方法的使用

PCollection<String> a = MemPipeline.typedCollectionOf(stringsO^

•’cherry", napplen, ’.banana", "banana");

assertEquals((Long) 4L, a.length().getValue());

assertEquals("apple", a.min().getValue());

assertEquals("cherry, a.max().getValue());

PTable<StringJ Long> b = a.count();

assertEquals("{(apple，1)，(banana，2)，(cherry，I)}.dump(b));

爆

PTable<String, Long〉 c = b.top(l);

assert Equals (•’{(banana, 2)}", dump(c));

PTable<StringJ Long〉 d = b.bottom(2);

assertEquals("{(apple, 1)，(cherry, 1)}", dump⑷);

##### 18.5延伸阅读

本章简单介绍了 Crunch0要想了解更多信息，请参阅Crunch用户指南，网址为 <http://crunch>. apache, org/user-guide. html0
