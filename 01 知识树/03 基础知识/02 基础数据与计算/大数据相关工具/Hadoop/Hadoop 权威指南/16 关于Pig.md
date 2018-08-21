Apache ?々([http://pig.apache.org/)](http://pig.apache.org/)%e4%b8%ba%e5%a4%a7%e5%9e%8b%e6%95%b0%e6%8d%ae%e9%9b%86%e7%9a%84%e5%a4%84%e7%90%86%e6%8f%90%e4%be%9bT%e6%9b%b4%e9%ab%98%e5%b1%82%e6%ac%a1%e7%9a%84%e6%8a%bd%e8%b1%a1%e3%80%82)[为大型数据集的处理提供T更高层次的抽象。](http://pig.apache.org/)%e4%b8%ba%e5%a4%a7%e5%9e%8b%e6%95%b0%e6%8d%ae%e9%9b%86%e7%9a%84%e5%a4%84%e7%90%86%e6%8f%90%e4%be%9bT%e6%9b%b4%e9%ab%98%e5%b1%82%e6%ac%a1%e7%9a%84%e6%8a%bd%e8%b1%a1%e3%80%82)MapReduce使作为程序员的你能够自己定义一个map函数和一个紧跟其后的 reduce函数。但是，你必须使数据处理过程与这一连续的map和reduce模式相匹 配。很多时候，数据处理需要多个MapReduce过程才能实现。而使得数据处理过 程与该模式匹配可能很困难。有了 Pig，就能使用更为丰富的数据结构。这些数据 结构往往都是多值和嵌套的。Pig还提供了一套更强大的数据变换操作，包括在 MapReduce中被忽视的连接(join)操作。

Pig包括两部分。

(1)    用于描述数据流的语言，称为Pig Latin。

(2)    用于运行Pig Latin程序的执行环境。当前有两个环境：单JVM中的本地 执行环境和Hadoop集群上的分布式执行环境。

Pig Latin程序由一系列的“操作”(operation)或“变换”(transformation)组成。每 个操作或变换对输入进行数据处理，并产生输出结果。从整体上看，这些操作描 述了一个数据流。Pig执行环境把数据流翻译为可执行的内部表示并运行它。在 Pig内部，这些变换操作被转换成一系列MapReduce作业。但作为程序员，多数 情况下你并不需要知道这些转换是如何进行的，如此便可以将精力集中在数据 上，而非执行细节上。

Pig是一种探索大规模数据集的脚本语言。MapReduce的一个缺点是开发周期太 长。写mapper和reducer,对代码进行编译和打包，提交作业，获取结果，这整 个过程非常耗时。即便使用Streaming能在这一过程中去除代码的编译和打包步 骤，仍不能改善这一情况。Pig的诱人之处在于仅用控制台上的五六行Pig Latin 代码就能够处理TB级的数据。事实上，正是由于雅虎公司想让科研人员和工程 师能够更便捷地挖掘大规模数据集，才设计开发了 Pig。Pig提供了多个命令来检 查和处理程序中已有的数据结构。因此，它能够很好地支持程序员写查询。Pig的 一个更有用的特性是它支持在输入数据的一个有代表性的子集上试运行。这样一来，用 户可以在处理整个数据集前检查程序执行时是否会有错误。

Pig被设计为可扩展的。处理路径中的几乎每个部分，包括载入、存储、过滤、分 组、连接都可以定制。这些操作都可以使用用户定义函数(user-defined function, UDF)进行修改。这些用户定义函数作用于Pig的嵌套数据模型。因此，它们可以 在底层与Pig的操作集成。UDF的另一个好处是，相较于为了写MapReduce程序 而开发的代码库，它们更易于重用。

在有些情况下，Pig的表现不如MapReduce程序。但随着新版本的发布，Pig的开 发团队使用了复杂、精巧的算法来实现Pig的关系型操作，二者的差距在不断缩 小。公平地说，除非你愿意花大量时间来优化Java MapReduce程序，否则用Pig Latin来写查询的确能够帮你节约时间。

##### 16.1安装与运行Pig

Pig是作为一个客户端应用程序运行的。即使准备在Hadoop集群上运行Pig，也 无需在集群上额外安装什么东西：Pig从工作站上发出作业，并在工作站上和 HDFS(或其他Hadoop文件系统)进行交互。

Pig的安装很简单。从[http://pig.apache.org/releases.html](http://pig.apache.org/releases.html%e4%b8%8b%e8%bd%bd%e4%b8%80%e4%b8%aa%e7%a8%b3%e5%ae%9a%e7%89%88%e6%9c%ac%ef%bc%8c%e7%84%b6%e5%90%8e)[下载一个稳定版本，然后](http://pig.apache.org/releases.html%e4%b8%8b%e8%bd%bd%e4%b8%80%e4%b8%aa%e7%a8%b3%e5%ae%9a%e7%89%88%e6%9c%ac%ef%bc%8c%e7%84%b6%e5%90%8e) 把tar压缩包解压到工作站上的合适路径：

% tar xzf pig-x.y.z.tar.gz

把Pig的二进制文件路径添加到命令行路径也很方便，例如:

% export PIG_HOME=^/sw/pig-x.y.z % export PATH=$PATH:$PIG_HOME/bin

还需要设置］AVA_HOME环境变量，指明Java的安装路径。 输人pig -help可获得使用帮助。

Pig有两种执行类型或称为模式(mode):本地模式(local mode)和MapReduce模式 (MapReduce mode)。在本书写作期间，用于Apache Tez和Spark(参见第19章)的 执行模式尚在开发之中。这两种模式都承诺将在性能上远远超出MapReduce模 式，因此，如果你正在使用的Pig版本中这两种模式是可用的，不妨一试。

1.本地模式

在本地模式下，Pig运行在单个JVM中，访问本地文件系统。该模式只适用于处 理小规模数据集或试用Pig时。

执行类型可用-x或-exectype选项进行设置。如果要使用本地模式运行，应把该 选项设置为local：

% pig -x local grunt>

这样就能够启动Grunt。Grunt是与Pig进行交互的shell环境。稍后我们要对它进 行详细讨论。

\2. MapReduce 模式

在MapReduce模式下，Pig将查询翻译为MapReduce作业，然后在Hadoop集群 上执行。集群可以是伪分布的，也可以是全分布的。如果要用Pig处理大规模数 据集，应该使用(全分布集群上的)MapReduce模式。

要使用MapReduce模式，首先需要检查下载的Pig版本是否与正在使用的Hadoop 版本兼容。Pig发布版本只和特定的Hadoop版本对应。发行说明中记录了版本的 对应关系。

Pig根据HADOOP_HOME环境变量来寻找并运行对应的Hadoop客户端。但是，如 果该环境变量没有被设置过，那么Pig会运行捆绑在Pig中的Hadoop库。注意， 捆绑的库的版本可能和集群上的Hadoop版本不一样。所以，最好明确指定

HADOOP HOME。

然后，需要将Pig指向集群的namenode和资源管理器。如果安装在HADOOPJ1OME

下的Hadoop已经进行了设置，那么不需要进行额外的配置。否则，可以把 HADOOP_CONF_DIR 设为包含 fs.defaultFS、yarn.resourcemanager. address 和

mapreduce.framework.name定义的Hadoop站点文件（后者应被设置为yarn）的

目录。

另一种办法是，在Pig的conf目录（或由PIG_CONF_DIR指定的目录）下的 pig.properties文件中设置这些属性。下面是为一个伪分布集群所做的设置：

fs.defaultFS=hdfs://localhost/

mapreduce.framework.name二yarn

yarn.resourcemanager.address=localhost:8032

一旦设置好Pig到Hadoop集群的连接，就可以设置-x选项为mapreduce或忽略 该选项来运行Pig。可以忽略该选项的原因是MapReduce模式是Pig的默认执行模 式。使用-brief选项可以阻止时间戳的生成：

% pig -brief

Logging error messages to: /Users/tom/pig_1414246949680.log Default bootup file /Users/tom/.pigbootup not found Connecting to hadoop file system at: hdfs://localhost/ grunt>

从输出中可以看到，Pig报告了它所连接的文件系统（而并非YARN资源管理器）。

在MapReduce模式下，通过选项设置可启用肢?必（将pig.auto.local enabled选项值设置为true），当输入数据小于100 MB时（通过 pig.auto.local.input.maxbytes 选项设置，默认值为 100 000 000），运行过程 被优化为数个本地运行的小作业，并且所使用的reducer的数量不超过1个。

###### 16.1.2运行Pig程序

有三种执行Pig程序的方法。它们在本地和MapReduce模式下都适用。

\1.    脚本

Pig可以运行包含Pig命令的脚本文件。例如，pig script.pig将运行在本地文 件wr/pz.pfg中的命令，或者，对于很短的脚本，也可以使用-e选项直接在命令行 中以字符串形式输入脚本。

\2.    Grunt

Gnmt是运行Pig命令的交互式shell环境。如果没有指明Pig要运行的文件，而且

也没有使用-e选项，Pig就会启动Grunt。在Grunt环境中，可以通过run和 exec命令运行Pig脚本。

3.嵌入式方法

还可以在Java中通过PigServer类来运行Pig程序。这就像能够在Java中使用

JDBC运行SQL程序一样。如果要以编程的方式访问Grunt,则需要使用 PigRunner0

###### 16.1.3 Grunt

Grunt包含的行编辑功能类似于GNU Readline（用在bash shell环境等命令行应用 中）。例如，按组合键Ctrl+E可以将光标移到行末。Grunt也记录过去执行过的命 令。°可以使用Ctrl-P和Ctrl-N或上下键来回显命令历史缓存中的上或下一行。

Grunt的另一个有用的特色是自动补全机制。它能够在你按下Tab键时试 全Pig Latin的关键词和函数。例如，如果有如下未完成的命令行：

自动补



grunt> a = foreach b ge

此时如果按下Tab键，那么ge会自动扩展成Pig Latin的关键词generate：

grunt> a =foreach b generate

可以创建一个名为autocomplete的文件，并将其放置在Pig的类路径（如Pig的 install目录的cow/目录）或者启动Grunt的目录下，以此来定制自动补全的单词。 这个文件中每个单词占一行，且单词中不能出现空白字符。自动补全的匹配是大 小写敏感的。在这个文件中列出一些常用的文件路径特别有用（因为Pig并不提供 文件名自动补全）。在该文件中列出你创建的用户自定义函数也能带来很多便利。

可以使用help命令来列出命令列表。结束一个Grunt会话时，可以使用quit命 令或等价的快捷键\q退出。

###### 16.1.4 Pig Latin 编辑器

有多种编辑器都提供了对Pig Latin语法的高亮显示，包括Eclipse、IntelliJ IDEA、Vim, Emacs和TextMate。详情请参见维基百科关于Pig的描述，网址为

①历史记录保存在home目录下的.pig_history文件中。

<https://cwiki.apache.org/confluence/display/PIG/PigTooIs> o

不少 Hadoop 发行版都附有 Hue web interface （http://gethue.com/），其中包含 Pig 脚 本编辑器和启动程序。

##### 16.2示例

现在，让我们用Pig Latin写一个计算天气数据集中年度最高气温的程序（与第2章 中用MapReduce写的程序功能相同），作为示例。这个程序只需要很少几行代码：

--max一temp.pig: Finds the maximum temperature by year records = LOAD 'input/ncdc/micro-tab/sample.txt *

AS (year:chararray, temperature:int, quality:int); filtered_records = FILTER records BY temperature != 9999 AND quality IN (0, 1, 4, 5, 9);

grouped_records = GROUP filtered_records BY year;

maxjtemp = FOREACH grouped_records GENERATE group,

MAX(filtered_records.temperature);

DUMP max一temp;

为了了解这个程序都做了些什么事情，我们将使用Pig的Grunt解释器。它让我们

能够输入几行代码，然后通过交互来理解程序在做什么。在本地模式下启动 Grunt,然后输入Pig脚本的第一行：

grunt>records =LOAD •input/ncdc/micro-tab/sample.txt•

\>>AS （year:chararray, temperature:int, quality:int）;

为了简单起见，程序假设输入是由制表符分割的文本，每行只包含年度、气温和 质量三个字段。（事实上，如后文所述，Pig的输入格式处理能力要比这个灵活得 多）。这行代码描述的是我们要处理的输入数据。year:chararray给出了字段的 名称和类型。chararray和Java字符串类似，int和Java的int类似。LOAD操

作接受一个URI参数作为输入。在这个示例中，我们只使用了一个本地文件，不 过我们也可以引用一个HDFS URI。AS子句（可选的）设定了字段的名称，以便在 后面的语句中更方便地引用它们。

LOAD操作的结果和Pig Latin其他所有操作的结果一样，都是一个关系（relation）, 即一个元组集合。元组类似于数据库表中的一行数据，包含按照特定顺序排列的 多个字段。在这个示例中，LOAD函数根据输入文件，生成一个（年份，气温，质 量）元组的集合。我们把关系写出来就是每个元组一行，每行由括号括起，每项字 段由逗号分隔：

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

关系被赋予一个名称或别名(alias)以便于引用。这个关系的别名是records。我们 可以使用DUMP操作来查看别名所对应的关系的内容：

grunt>DUMP records;

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

我们还可以把describe操作作用于一个关系的别名，来查看该关系的结构，即 关系的模式(schema):

grunt>DESCRIBE records;

records: {year: chararray^temperature: inequality: int}

从输出中可以知道，records有三个字段，别名分别为year、temperature和 quality。字段的别名是我们在AS子句中指定的。同样，字段的类型也是在AS

子句中指定的。我们在后面会对Pig中的数据类型进行更深入的介绍 第二条语句去除了没有气温的记录(即用值9999表示气温的记录)以及读数质量不 令人满意的记录。在这个很小的数据集中，没有记录被过滤掉：

grunt〉 filtered_records = FILTER records BY temperature != 9999 AND >> quality IN (0, 1, 4, 5, 9); grunt>DUMP filtered_records;

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

第三条语句使用GROUP函数把records关系中的记录按照year字段分组。让我 们用DUMP来查看GROUP的结果:

grunt> grouped_records = GROUP filtered_records BY year; grunt> DUMP grouped_records;

(1949,{(1949,78,1),(1949,111,1)})

(1950,((1950,-11,1),(1950,22,1),(1950,0,1)})

这样，我们就有了两行，或者称为两个元组。每个元组对应于输入数据中的一个 年度。每个元组的第一个字段是用于分组的字段(即年度)。第二个字段是该年度的 元组的包（bag）。包是一个元组的无序集。在Pig Latin里，包用大括号来表示。

通过上述分组方式，我们已经为每个年度创建了一行。剩下的事情就是在每个包 中找到包含最高气温的那个元组。在此之前，让我们先来理解grouped_records 这一关系的结构：

grunt>DESCRIBE grouped—records;

grouped_records: {group: chararray,filtered_records: {year: chararray^ temperature: int^quality: int} }

从输出结果可以看到，Pig给分组字段起了个别名group。第二个字段和被分组的 filtered^records关系的结构相同。根据这些信息，我们可以试着执行第四条 语句对数据进行变换：

grunt>max_temp =FOREACH grouped_records GENERATE group, >> MAX(filtered一records.temperature);

FOREACH对每一行数据进行处理，并生成一组导出的行。导出的行的字段由 GENERATE子句定义。在这个示例中，第一个字段是group，也就是年度。第二个 字段稍微复杂一点。filtered_records.temperature 弓I用了 grouped_records 关系中的filtered_records包中的temperature字段。MAX是计算包中字段的 最大值的内置函数。在这个例子中，它计算了 filtered_records包中的最高温 度。我们看一下它的结果：

grunt> DUMP max^temp; (1949,111)

(1950,22)

这样，我们便已成功计算出每年的最高气温。

###### 生成示例

在前面这个示例中，我们使用了一个仅包含少数行的抽样数据集，以简化数据流 跟踪和调试。创建一个精简的数据集是一门艺术。理想情况下，这个数据集的内 容应该足够丰富，能够覆盖查询中可能碰到的各种情况（满足完备性[completeness] 条件），同时，这个数据集应该足够小，能够被程序员直观理解（满足简明性 [conciseness]条件）。通常情况下，使用随机取样并不能满足要求，因为连接和过 滤这两个操作往往会去除掉所有的随机取样的数据，而导致产生一个空的结果 集。这样是无法描述典型的数据流的。

Pig通过ILLUSTRATE操作提供了生成相对完备和简明的数据集的工具。下面列出

的是运行ILLUSTRATE后的输出(进行了少许格式重排):

grunt>ILLUSTRATE max_temp;

| \| records | \| year:chararray | \| temperature:int | \| quality:int    \| |
| ---------- | ----------------- | ------------------ | -------------------- |
| 1          | \| 1949           | \| 78              | Il    1              |
| 1          | \| 1949           | \| 111             | 1 1 1                |
| 1          | \| 1949           | \| 9999            | 1 1 1                |

| \| filtered_records | \| year: chararray | temperature: int | \| quality: int    \| |
| ------------------- | ------------------ | ---------------- | --------------------- |
| 1                   | \| 1949            | \| 78    \|      | 1 1                   |
| 1                   | j 1949             | \| 111 \|        | 1 1                   |

I grouped一records | group:chararray | filtered—records:bag{:tuple(year: chararray, temperature: int^quality: int)}

| 1949    | {(1949, 78, 1), (1949, 111, 1)}

| max一temp | \| group:chararray | 1 :int    \| |
| --------- | ------------------ | ------------ |
| 1         | \| 1949            | \| 111 \|    |

注意，Pig既使用了部分的原始数据(这对于保持生成数据集的真实性很重要)，也 创建了一些新的数据。Pig注意到查询中9999这一值，所以创建了一个包含该值 的元组来测试FILTER语句。

综上所述，ILLUSTRATE的输出易于跟踪，而且也能帮助理解查询的执行过程。

##### 16.3与数据库进行比较

我们已经演示了如何运行Pig。看上去，Pig Latin和SQL很相似。GROUP BY和 DESCRIBE之类的操作更加强了这种感觉。但是，这两种语言之间，以及Pig和 关系型数据库管理系统(RDBMS)之间，有几个方面是不同的。

它们之间最显著的不同是：Pig Latin是一种数据流编程语言，而SQL是一种声明 式编程语言。换句话说，一个Pig Latin程序是一组针对输入关系的一步步操作。 其中每一步都是对数据的简单变换。相反，SQL语句是一个约束的集合。这些约 束结合在一起，定义了输出。从很多方面看，用Pig Latin编程更像在RDBMS中 查询规划器(query planner)这一层对数据进行操作。查询规划器决定了如何将声明 式语句转化为一系列系统化的执行步骤。

RDBMS把数据存储在严格定义了模式的表内。Pig对它所处理的数据要求则宽松 得多：你可以在运行时定义模式，而且这是可选的。本质上，Pig可以在任何来源 的元组上进行操作（当然，数据源必须支持并行的读操作，例如存放在多个文件 中）。它使用UDF从原始格式中读取元组。^最常用的输入格式是用制表符分隔的 字段组成的文本文件。Pig为这种输入格式提供了内置加载函数。和传统的数据库 不同，Pig并不提供专门的数据导入过程将数据加载到RDBMS。从文件系统（通常 是HDFS）中加载数据是处理的第一个步骤。

Pig对复杂的嵌套数据结构的支持也使其不同于只能处理平面数据类型的SQL。 Pig的语言能够和UDF以及流式操作紧密集成。Pig Latin的这一能力及其嵌套数 据结构，使它比大多数SQL的变种具有更强的定制能力。

RDBMS具有一些支持在线和低延迟查询的特性，如事务和索引。然而，这些特性 Pig都没有。Pig并不支持随机读和几十毫秒级别的查询。它也不支持针对一小部 分数据的随机写。同MapReduce—样，所有的写都是批量的、流式的写操作。

Hive（参见第17章）介于Pig和传统的RDBMS之间。同Pig —样，Hive也被设计 为用HDFS作为存储，但是它们之间有着显著的区别。Hive的查询语言HiveQL 是基于SQL的。任何熟悉SQL的人都可以轻松使用HiveQL写查询。和RDBMS 相同，Hive要求所有数据必须存储在表中，而表必须有模式，且模式由Hive进行 管理。但是，Hive允许为预先存储在于HDFS的数据关联一个模式。所以，数据 的加载步骤是可选的。通过使用HCatalog, Pig也可以处理Hive表，详情参见 16.4.5 节0

##### 16.4 PigLatin

本节对Pig Latin编程语言的语法和语义进行非正式的介绍。@本小节并不是完整的 编程语言参考，®但是这里的内容足以帮助大家很好地理解Pig Latin的组成。

①    或像 “ Pig 哲学” （[http://pig.apache.org/philosophy.html）](http://pig.apache.org/philosophy.html%ef%bc%89%e6%89%80%e8%af%b4%e2%80%a2%e2%80%a2)[所说••](http://pig.apache.org/philosophy.html%ef%bc%89%e6%89%80%e8%af%b4%e2%80%a2%e2%80%a2)    “ 猪什么都吃” （Pigs eat

anything）。

②    不要把Pig Latin编程语言和语言游戏Pig Latin混淆。Pig Latin游戏就是把单词开始的声母 移到单词最后，并加上“ay”的尾音。例如，“pig”变为“ig-pay”，而“Hadoop”则变为

“Adoop-hayw 0

③    Pig Latin并没有正式的语言定义。但通过Pig网站的链接，可以找到 Pig Latin语言的完整指南。

###### 16.4.1 结构

一个Pig Latin程序由一组语句构成。一个语句可以理解为一个操作，或一个命 令。®例如，GROUP操作是这样一种语句：

grouped一records = GROUP records BY year;

另一个语句的例子是列出Hadoop文件系统中文件的命令：

如前面的GROUP语句所示，一条语句通常用分号结束。实际上，那是一条必须 用分号表示结束的语句。如果省略了分号，它会产生一个语法错误。而另一方 面，Is命令则可以不使用分号结束。一般的规则是：在Grunt中，交互使用的语 句或命令不需要表示结束的分号。这包括交互式的Hadoop命令以及用于诊断的操 作，例如DESCRIBE。加上表示结束的分号总是不会错。因此，如果不确定是否 需要分号，把它加上是最简单的解决办法。

必须用分号表示结束的语句可以分成多行以便于阅读：

records = LOAD ?input/ncdc/micro-tab/sample.txtJ AS (yearrchararray, temperature:int^ quality:int);

Pig Latin有两种注释方法。双减号表示单行注释。Pig Latin解释器会忽略从第一 个减号开始一直到行尾的所有内容：

•-My program

DUMP A; -- MhatJs in A?

c语言风格的注释更灵活。这是因为它使用/*和*/符号表示注释块的开始和结束 这样，注释既可以跨多行，也可以内嵌在某一行内：

/*

\*    Description of my program spanning

\*    muLtipLe Lines.

*/

A = LOAD ’i叩ut/pig/join/A’；

B = LOAD ’input/pig/join/B’；

C = JOIN A BY $0, /* ignored */ B BY $1; DUMP C；

①在Pig Latin的文档里，有时这些术语是可以相互替换的。例如，“GROUP命令” “GROUP 操作”和“GROUP语句”的含义相同。

Pig Latin有一个关键词列表。其中的单词在Pig Latin中有特殊含义，不能用作标 识符。这些单词包括操作（LOAD，ILLUSTRATE）、命令（cat，Is）、表达式 （matches, FLATTEN）以及函数（DIFF，MAX）等。它们会在随后几个小节介绍。

Pig Latin采用混合的大小写敏感规则。操作和命令是大小写无关的（这样能使得交 互式操作更“宽容”），而别名和函数名则是大小写敏感的。

###### 16.4.2语句

在Pig Latin程序执行时，每个命令按顺序进行解析。如果遇到句法错误或其他（语 义）错误，例如未定义的别名，解释器会终止运行，并显示一条错误消息。解释器 会给每个关系操作建立一个逻辑计划（logical plan）。逻辑计划构成了 Pig Latin程序 的核心。解释器把为一个语句创建的逻辑计划加到到目前为止已经解析完的程序 的逻辑计划上，然后继续处理下一条语句。

特别要注意，在构造整个程序的逻辑计划时，Pig并不处理数据。我们仍然以前 的Pig Latin程序为例：

--max_temp.pig: Finds the maximum temperature by year records = LOAD ^input/ncdc/micro-tab/sample.txt^

AS (year:chararray, temperaturezint^ quality:int); filtered_records = FILTER records BY temperature != 9999 AND quality IN (0， 1， 4， 5， 9);

grouped_records = GROUP filtered_records BY year;

max一temp = FOREACH grouped__records GENERATE group;

MAX(filtered—records.temperature);

DUMP max_temp;

Pig Latin解释器看到第一行LOAD语句时，首先确认它在语法和语义上是正确 的，然后再把这个操作加入逻辑计划。但是，解释器并不是真的从文件加载数据 （它甚至不去检査该文件是否存在）。Pig到底要把文件加载到哪里呢？数据是加载 到内存吗？即使这些数据可以放入内存，Pig又如何处理数据呢？我们可能并不需 要所有的数据（因为后续的语句可能会过滤数据），因此加载数据没有意义。关键问 题在于，在没有定义整个数据流之前，开始任何处理都是没有意义的。与此类 似，Pig验证GROUP和FOREACH " GENERATE语句，并把它们加人逻辑计划 中，但并不执行这两条语句。让Pig开始真正执行的是DUMP语句。此时，逻辑 计划被编译成物理计划，并执行。

多查询执行

由于DUMP是一个诊断工具，因此它总是会触发语句的执行。STORE命令与 DUMP不同。在交互模式下，STORE和DUMP 一样，总是会触发语句的执行（这 一过程包含了 run命令）。但是，在批处理模式下，它不会触发执行（此时包含 了 exec命令）。这是为了从性能角度考虑而进行的设计。在批处理模式下， Pig会解析整个脚本，看看是否能够为减少写或读磁盘的数据量进行优化。考 虑如下的简单示例：

A = LOAD 'input/pig/multiquery/A';

B = FILTER A BY $1 == 'banana1;

C = FILTER A BY $1 != 'banana*;

STORE B INTO 'output/b1；

STORE C INTO 'output/c';

• •

关系B和C都是从A导出的。因此，为了防止读两遍A，Pig可以用一个 MapReduce作业从A读取数据，并把结果写到两个输出文件中去：一个给 B, 一个给C。这一特性称为多查询执行（multiquery execution）。

Pig的以前版本不包括这一特性：批处理模式下脚本中的每个STORE语句都 会触发语句的执行，从而使每个STORE语句都有一个对应的作业。可以在执 行时对pig使用或-nojnultiquery选项来禁用多查询执行，恢复使用以 前的设置。

Pig的物理计划是一系列的MapReduce作业。在本地模式下，这些作业在本地 JVM中运行，而在MapReduce模式下，它们在Hadoop集群上运行。

![img](Hadoop43010757_2cdb48_2d8748-194.jpg)



可以用EXPLAIN命令对一个关系査看Pig所创建的逻辑和物理计划（例如 EXPLAIN max一temp;）。

EXPLAIN也显示MapReduce计划，即显示物理操作是如何组成MapReduce作 业的。这是查看Pig为查询运行多少个MapReduce作业的好办法。

表16-1概括了能够作为Pig逻辑计划一部分的关系操作。16.6节将详细介绍这些 操作。

有些种类的语句并不会被加到逻辑计划中去。例如，诊断操作DESCRIBE、 EXPLAIN以及ILLUSTRATE。这些操作是用来让用户能够与逻辑计划进行交互以 进行调试的（参见表16-2）。DUMP也是一种诊断操作，它只能用于与很小的结果集 进行交互调试，或与LIMIT结合使用，来获得某个较大的关系的一小部分行。当

输出包含的行比较多的时候，应该使用STORE语句，这是 入文件中，而不是在控制台上显示。

![img](Hadoop43010757_2cdb48_2d8748-195.jpg)



为STORE语句将结果存



表16-1. Pig Latin的关系操作

| 类型加载与存储 | 操作LOAD                             | 描述从文件系统或其他存储加载数据，存入关系                   |
| -------------- | ------------------------------------ | ------------------------------------------------------------ |
| STORE          | 将一个关系存放到文件系统或其他存储中 |                                                              |
| DUMP           | 将关系打印到控制台                   |                                                              |
| 过滤           | FILTER                               | 从关系中删除不需要的行                                       |
|                | DISTINCT                             | 从关系中删除重复的行                                         |
|                | FOREACH...GENERATE                   | 在关系中增加或删除字段                                       |
|                | MAPREDUCE                            | 以一个关系作为输入运行某个MapReduce作业                      |
| •              | STREAM                               | 使用外部程序对一个关系进行变换                               |
|                | SAMPLE                               | 对一个关系进行随机取样                                       |
|                | ASSERT                               | 确保一个关系中的所有行就某个条件而言都是真的，否则失败       |
| 分组与连接     | JOIN                                 | 连接两个或多个关系                                           |
|                | COGROUP                              | 对两个或更多关系中的数据进行分组                             |
|                | GROUP                                | 在一个关系中对数据进行分组                                   |
|                | CROSS                                | 创建两个或更多关系的乘积（叉乘）                             |
|                | CUBE                                 | 为一个关系中指定列的所有组合生成聚集数据                     |
| 排序           | ORDER                                | 根据一个或多个字段对某个关系进行排序                         |
|                | RANK                                 | 为一个关系中的每个元组分配一个顺序号，可以选择 先根据字段排序再分配。 |
|                | LIMIT                                | 将一个关系的元组个数限定在一定数量内                         |
| 组合和切分     | UNION                                | 合并两个或多个关系为一个关系                                 |

SPLIT    把某个关系切分两个或多个关系

表16-2. Pig Latin的诊断操作

| 操作                                                         | 描述                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| DESCRIBE                                                     | 打印关系的模式                             |
| EXPLAIN                                                      | 打印逻辑和物理计划    "                    |
| ILLUSTRATE                                                   | 使用生成的输入子集显示逻辑计划的试运行结果 |
| 为了在Pig脚本中使用宏和用户自定义函数，PigLatin还提供了 REGISTER、 DEFINE和IMPORT这三个语句（参见表16-3）。 |                                            |

表16-3. Pig Latin的宏和UDF语句

语句    描述

REGISTER    在Pig运行时环境中注册一个JAR文件

| DEFINE | 为宏、UDF、流式脚本或命令规范新建别名 |
| ------ | ------------------------------------- |
| IMPORT | 把在另一个文件中定义的宏导入脚本      |

为这些命令并不处理关系，所以它们不会被加入逻辑计划。相反，这些命令会

| 被立即执行。Pig提供了与Hadoop文件系统和MapReduce进行交互的命令及其他 一些工具命令（参见表16-4）。与Hadoop文件系统进行交互的命令对在Pig处理前 和处理后进行的数据移动非常有用。 |               |                                                       |
| ------------------------------------------------------------ | ------------- | ----------------------------------------------------- |
| 表 16-4. PigLatin 命令                                       |               |                                                       |
| 类别                                                         | 命令          | 描述                                                  |
| Hadoop文件系统                                               | cat           | 打印一个或多个文件的内容                              |
|                                                              | cd •          | 改变当前目录                                          |
|                                                              | copyFromLocal | 复制本地文件或目录                                    |
|                                                              | copyToLocal   | 将一个文件或目录从Hadoop文件系统复制到本 地文件系统   |
|                                                              | CP            | 把一个文件或目录复制到另一个目录                      |
|                                                              | fs            | 访问Hadoop文件系统shell                               |
|                                                              | Is            | 打印文件列表信息                                      |
|                                                              | mkdir         | 创建新目录                                            |
|                                                              | mv            | 将一个文件或目录移动到另一个目录                      |
|                                                              | pwd           | 打印当前工作目录的路径                                |
|                                                              | rm            | 删除一个文件或目录                                    |
|                                                              | rmf           | 强制删除文件或目录（即使文件或目录不存在也 不会失败） |
| Hadoop MapReduce 工具                                        | kill          | 终止某个MapReduce作业                                 |
|                                                              | exec          | 在新的Grunt shell中以批处理模式运行脚本               |
|                                                              | help          | 显示可用的命令和选项                                  |
|                                                              | history       | 打印当前Grunt会话中运行的査询语句                     |
|                                                              | quit(\q)      | 退出解释器                                            |
|                                                              | run           | 在当前Grunt shell中运行脚本                           |
|                                                              | set           | 设置Pig选项和MapReduce作业属性                        |
|                                                              | sh            | 在Grunt中运行shell命令                                |
| 文件系统相关的命令可以对任何Hadoop文件系统的文件或目录进行操作。这些命 |               |                                                       |

令和hadoopfs命令很像（这是意料之中的，因为两者都是Hadoop FileSystem接 口的简单封装）。可以使用Pig的fs命令访问所有的Hadoop文件系统的shell命 令。例如，fs -Is显示文件列表，fs -help则显示所有可用命令的帮助信息。

准确地说，使用哪个Hadoop文件系统是由Hadoop Core站点文件中的 fs.default.name属性决定的。3.3节详细介绍了如何设置这个属性。

除了 set命令以外，这些命令的含义大都不言自明。set命令用于设置控制Pig 行为的选项，包括所有MapReduce作业的属性。debug选项用于在脚本中打开或 关闭调试日志（也可以在启动Pig时使用-d或-debug选项控制日志的级别）：

grunt>set debug on

另一个很有用的选项是job.name。它为Pig作业设定一个有意义的名称。这样， 即可方便地知道在共享的Hadoop集群上有哪些Pig MapReduce作业是自己的。如 果Pig正在运行某个脚本（而不是通过Grunt运行交互式查询），默认作业名称是基 于脚本的名称的。

表16-4中有两个命令可以运行Pig脚本：exec和run。它们的区别是exec在一 个新的Grunt shell环境中以批处理方式运行脚本。因此，所有脚本中定义的别名 在脚本运行结束后不能在shell中再被访问。另一方面，如果使用run运行脚本， 那么效果就和在shell中手工输入脚本的内容是一样的。因此，运行该脚本的shell 命令历史中包含脚本的所有语句。只能用exec进行多查询执行，即Pig以批处理 方式一次执行一批语句（详情参见16.4.2节的补充内容“多查询执行”），而不能使 用run命令进行多査询执行。

控制流

Pig Latin的设计中缺少原生的控制流语句。如果想撰写需要条件逻辑或循环结 构的程序，建议把Pig Latin夜入到其他语言之中，如Python、JavaScript或 Java,由该语言管理控制流。在这一模型下，宿主脚本使用compile-bind-run API来执行Pig脚本，并获得脚本的状态。要想获得API的详细信息，请参考 Pig的帮助文档。

嵌入式Pig程序总是在JVM中运行。如果要执行Python或JavaScript程序， 应使用pig命令，后面跟脚本名。合适的Java脚本引擎（对Python而言是 Jython，对JavaScript而言是Rhino）会被自动选择来执行脚本。

###### 16.4.3表达式

在Pig中，可以通过计算表达式得到某个值。表达式可以作为包含关系操作的语 句的一部分。Pig可以使用丰富的表达式类型。Pig中的很多表达式类型和其他编 程语言中的表达式相像。表16-5列出了各种表达式及其简要说明和示例。本章将 有很多这样的表达式。

表 16-5. Pig Latin 表达式

| 类别             | 表达式           | 描述                                                   | 示例                                    |
| ---------------- | ---------------- | ------------------------------------------------------ | --------------------------------------- |
| 常数             | 文字             | 常量值（参见表16-6中“文字 示例” 一栏）                 | 1.0,                                    |
| 字段（位置指定） | $n               | 第《个字段（以0为基数）                                | $0                                      |
| 字段（名字指定） | f                | 字段名f                                                | year                                    |
| 字段（消除歧义） | r:: f            | 分组或连接后关系r中的名为 f的字段                      | A::year                                 |
| 投影             | c.$n,c.f         | 在容器0（关系、包或元组）中的 字段（按位置或名称指定） | records.$0^ records.year                |
| Map查找          | mttk             | 在映射中键A所对应的值                                  | items#’Coat’                            |
| 类型转换         | (t) f            | 将字段f转换为类型t                                     | (int) year                              |
| 算术             | x +    -y        | 加法和减法                                             | $1 + $2,$1 - $2                         |
|                  | x *y,x / y       | 乘法和除法                                             | $2 * $2，$2 / $2                        |
|                  | x % y            | 取模运算，即火除以火后的余数                           | $1 % $2                                 |
|                  | +xJ-x            | 正和负                                                 | +1,-1                                   |
| 条件             | x ? y : z        | 二值条件三元运算符，如果;r为 真，则/，否则为z          | quality == 0 ? 0 : 1                    |
|                  | CASE             | 多条件                                                 | CASE q WHEN 0 THEN 1good1ELSE 'bad1 END |
| 比较             | x = y^x != y     | 相等和不等                                             | quality == 0, temperature != 9999       |
|                  | x >y^x <y        | 大于和小于                                             | quality > 0, quality < 10               |
|                  | x >= y,x<= y     | -ken 姑丁^门,1 y T-雄丁                                | quality >= 1, quality <= 9              |
|                  | 大十寺十欄寸寺十 |                                                        |                                         |
|                  | xmatchesy        | 正则表达式匹配                                         | quality matches J[01459]^               |
|                  | xisnull          | 是空值                                                 | temperature is null                     |
|                  | xisnotnull       | 不是空值                                               | temperature is not null                 |
| 布尔型           | xory             | 逻辑或                                                 | q == 0 or q == 1                        |
|                  | xandy            | 逻辑与                                                 | q == 0 and r == 0                       |
|                  | notx             | 逻辑非                                                 | not q matches ’[01459]’                 |
|                  | IN x             | 设定成员                                               | q IN (0, 1, 4, 5, 9)                    |
| 函数型           |                  | 在fl，/2等字段上应用函数fn                             | isGood(quality)                         |
| 平面化           | FLATTEN(f)       | 从包和元组中去除嵌套                                   | FLATTEN(group)                          |
|                  |                  |                                                        |                                         |

###### 16.4.4 类型

前面出现过Pig的一些简单数据类型，例如int和chararray。本节将更详细地 讨论Pig的内置数据类型。

Pig 有六种数值类型：int、long、float、double、biginteger 和 bigdecimal。它们和Java中对应的数值类型相同。此外，Pig还有bytearray 类型，它类似于Java用来表示二进制大对象的byte数组；chararray类型则类 似于用UTF-16格式表示文本数据的java.lang.String。chararray也可以被 加载或存储为UTF-8格式；datetime类型为短日期时间格式，精确度到毫秒， 并包含时区。

Pig没有任何一种数据类型对应于java的byte、short或char。这些数据类型都 能使用Pig的int类型或chararray类型（针对char）来方便地表示。

布尔、数值、文本、二进制与时间类型都是原子类型。PigLatin有三种用于表示嵌 套结构的复杂类型：tuple（元组）、bag（包）和map（映射）。表16-6列出了 PigLatin 的所有数据类型。

表16-6. Pig Latin数据类型

| 别尔类布 | 数据类型 boolean | 描述true/false 值                                            | 文字示例 true                |
| -------- | ---------------- | ------------------------------------------------------------ | ---------------------------- |
| 数值     | int              | 32位有符号整数                                               | 1                            |
|          | long             | 64位有符号整数                                               | 1L                           |
|          | float            | 32位浮点数                                                   | 1.0F                         |
|          | double           | 64位浮点数                                                   | 1.0                          |
|          | biginteger       | 任意精度整数                                                 | '10000000000'                |
|          | bigdecimal       | 任意精度带符号小数                                           | '0.110001000000000000000001' |
| 文本     | chararray        | UTF-16格式的字符数组                                         | •a*                          |
| 二进制   | bytearray        | 字节数组                                                     | 不支持                       |
| 噸       | datetime         | 带时区的日期和时间                                           | 不支持，使用内置的ToDate函数 |
|          | tuple            | 任何类型的字段序列                                           | (1/ pomegranate1)            |
|          | bag              | 元组的无序多重集合（允许 重复元组）                          | {(1,•pomegranate1),(2)}      |
|          | map              | 一个键-值对的集合。键必 须是字符数组，值可以是 任何类型的数据 | [1 a1#!pomegranate*]         |

复杂类型通常从文件加载或者使用关系操作进行构建。但是要注意，表16-6中的文字 示例只是在PigLatin程序中表示常数值时使用的形式，当使用PigStorage加载 器从文件加载时，数据的原始形式往往与之不同。例如，在文件中如表16-6所示 的包的数据可能形如{(1,pomegranate), (2)}，注意，此时没有单引号。如果有 合适的模式，该数据可以加载到一个关系。该关系只有一个仅有一个字段的行， 而该字段的值是包。

438 第16章

##### Pig提供了内置的TOTUPLE、TOBAG以及TOMAP函

ik/



它们被用来将表达式转化



为元组、包以及映射。

虽然关系和包在概念上是相同的(本质上它们都是元组的无序多重集合)，但实际上 Pig对它们的处理稍有不同。关系是顶层构造结构，而包必须在某个关系中。正常 情况下，不必操心它们的区别，但是它们在使用时会有一些限制。对于新手，这 些限制仍然可能导致错误。例如，不能根据包文字直接创建一个关系。因此，如 下语句会运行失败：

A = {(1,2),(3,4)}; --Error

针对这种情况，最简单的解决办法是使用LOAD语句从文件加载数据

另一个例子是，对待关系不能像处理包那样把一个字段投影为一个新的关系(例 如，使用位置符号，用$0指向A的第一个字段)：

B = A.$0;

要达到这个目的，必须使用关系操作将一个关系A转换为另一个关系B:

B = FOREACH A GENERATE $0;

Pig Latin将来的版本可能采用相同的方法来处理关系和包，旨在消除这种不一致 性。

###### 16.4.5模式

Pig中的一个关系可以关联一个模式。模式为关系中的字段指定名称和类型。我们 前面已经介绍过load语句的as子句如何在关系上附以模式：

grunt>records = LOAD 'input/ncdc/micro-tab/sample.txt'

\>> AS (yeariint^ temperature:int, quality:int)j grunt>DESCRIBE records;

records: {year: int，temperature: int，quality: int}

这次，虽然加载的是和上次一样的文件，但年份已声明为整数类型，而不是 chararray类型。如果要对年份这一字段进行算术操作(例如将它变为一个时间 戳)，那么用整数类型更为合适。相反，如果只是想把它作为一个简单的标识符， 那么表示为chararray类型则更合适。Pig的这种模式声明方式提供了很大的灵 活性。这和传统SQL数据库要求在数据加载前必须先声明模式的方式截然不同。 Pig的设计目的是用它来分析不包含数据类型信息的纯文本输入文件，因此，它为 字段确定类型的时机比RDBMS要晚也是理所当然的。

我们也可以完全忽略类型声明：

grunt> records = LOAD * input/ncdc/micro-tab/sample.txt'

\>> AS (year， temperature, quality); grunt>DESCRIBE records;

records: {year: bytearray^temperature: bytearray^quality: bytearray}

在这个例子中，我们在模式中只确定了字段的名称：year、temperature和 quality。默认数据类型为最通用的bytearray，即二进制串。

不必为每一个字段都给出类型。你可以让某些字段的类型为默认的bytearray, 就像如下模式声明示例中的year字段：

grunt> records = LOAD 'input/ncdc/micro-tab/sample.txt'

\>> AS (year, temperature:int, quality:int)j grunt〉 DESCRIBE records;

records: {year: bytearraytemperature: int,quality•• int}

但是，如果要用这种方式来确定模式，必须在模式中定义每一个字段。同样，不 能只确定字段的类型而不给出其名称。另一方面，模式本身是可选的。可以省略 AS子句，如下所示：

grunt> records = LOAD •input/ncdc/micro-tab/sample.txt•; grunt> DESCRIBE records;

Schema for records unknown.

对于没有对应模式的关系中的字段，只能使用位置符号进行引用：$0表示关系中 的第一个字段，$1表示第二个，依此类推。它们的类型都是默认的bytearray:

grunt>projected一records    FOREACH records GENERATE $0, $1, $2;

grunt>DUMP projected_records;

(1950,0,1)    "

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

grunt〉 DESCRIBE projected_records; projected_records: {bytearray,bytearray,bytearray}

虽然不为字段指明类型很省事（特别是在撰写查询的开始阶段），但如果指定了字段 的类型，我们可以使PigLatin程序更清晰，也使程序运行得更高效。因此，在一 般情况下，建议指明字段的数据类型。

1.通过 HCatalog 使用 Hive

虽然在查询中声明模式的方式是灵活的，但这种方式并不利于模式重用。处理相 同输入数据的一组Pig查询常常使用相同的模式。如果一个査询要处理很多字 段，那么在每个查询中维护重复出现的模式会很困难。

##### HCatalog（Hive的一个组件）通过提供基于Hive的metastore表的元数据服务解决了

这一问题。这样，Pig查询就可以通过名称引用模式，而不必每次都得指明整个模

式。例如，通过运行17.2节的示例将数据加载到一个名为records的Hive表

后，Pig可以用下述方式访问该表的模式和数据:

% pig -useHCatalog

grunt〉 records = LOAD 'records1 USING org.apache.hcatalog.pig.HCatLoader(); grunt〉 DESCRIBE records;

records: {year: chararray,temperature: int,quality: int}

grunt〉 DUMP records;

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

2.验证与空值

SQL数据库在加载数据时，会强制检查表模式中的约束。例如，试图将一个字符

串加载到声明为数值型的列会失败。在Pig中，如果一个值无法被强制转换为模

式中声明的类型，Pig会用空值null替代。如果有如下天气数据输入，在定义为

整数型的地方出现了一个字符“e”，我们来看一下这一验证机制是如何工作的:

1950

11111



1950

1950

1949

1949

Pig在处理损坏的行时会为违例的值产生一个null。在输出到屏幕（或使用STORE 存储）时，空值null被显示（或存储）为一个空位：

grunt>records = LOAD ’input/ncdc/micro-tab/sample一corrupt.txt’

\>>AS (year:chararray, temperature:int, quality:int); grunt〉 DUMP records;

(1950,0,1)

(1950.22.1)

(1950,,1)

(1949.111.1)

(1949.78.1)

Pig会为非法字段产生警告（在此没有显示），但是它不会终止处理。大的数据集普 遍都有损坏值、无效值或意料之外的值，因而逐步修正每一条无法解析的记录一 般都不太现实。作为一种替代方法，我们可以一次性地把所有的非法记录都找出 来，然后再一起处理它们。我们可以修正我们的程序（因为这些记录表示我们写程 序时犯了错误），或把这些记录过滤掉（因为这些数据无法使用）：

grunt>corrupt_records = FILTER records BY temperature is null; grunt> DUMP corrupt一records;

(1950,,1)    "

清注意，这里对isnull操作的使用和SQL中的类似。事实上，我们可以在原始 记录中获得更多的信息（例如标识符和无法被解析的值等），帮助分析问题数据。

可以用对关系中的行进行计数的常用语句来获得损坏记录的条数，如下所示:

grunt〉 grouped = GROUP corrupt_records ALL;

grunt〉 all_grouped = FOREACH grouped GENERATE group, COUNT(corrupt_records); grunt〉 DUMP all_grouped;

(all,l)

16.6.3节在介绍GROUP时详细解释了分组和ALL操作。

另一个有用的技巧是使用SPLIT操作把数据划分成“好”和“坏”两个关系，然 后再分别对它们进行分析：

grunt>SPLIT records INTO good一records IF temperature is not null, >> bad—records OTHERWISE; grunt>DUMP good_records;

(1950,0,1)    "

(1950.22.1)

(1949.111.1)

(1949.78.1)

grunt>DUMP bad_records;

(1950”1)    "

让我们回到前面temperature数据类型未声明的情况，此时无法轻松检测到损坏 的数据，因为它没有被当作null值：

grunt> records = LOAD 'input/ncdc/micro-tab/sample_coprupt.txt >> AS (yearichararray^ temperature, quality:int);

grunt〉 DUMP records;

(1950,0,1)

(1950.22.1)

(1950,e,l)

(1949.111.1)

(1949.78.1)

grunt〉 filtered—records = FILTER records BY temperature != 9999 AND » quality IN (0^ 1, 4, S, 9);

grunt〉 grouped_records = GROUP filtered—records BY year;

grunt〉 max_temp = FOREACH grouped_records GENERATE group,

\>> MAX(filtered_records.temperature);

grunt> DUMP max一temp;

(1949,111.0)    "

(1950,22.0)

在这种情况下，temperature字段被解释为bytearray，因此在数据加载时，损 坏的字段并没有被检测出来。在传输给MAX函数时，因为MAX只能处理数值类 型，所以temperature字段被强制转换为double类型。损坏的字段不能被表示 为double,所以它被当作null处理，MAX则会忽略这个值。通常，最好的解决 办法是在加载数据时声明数据类型，并在进行主要的处理前查看关系中缺失的值或 损坏的值。

有时，因为有些字段缺失，损坏的数据被显示为比较短的元组。可以用SIZE函数 对它们进行过滤，如下所示：

grunt〉A = LOAD input/pig/corrupt/missing_fieldsJ; grunt〉 DUMP A;

(2,Tie)

(4,Coat)

(3)

(1,Scarf)

grunt〉 B = FILTER A BY SIZE(TOTUPLE(*)) > 1； grunt〉 DUMP B;

(2,Tie)

(4,Coat)

(1,Scarf)

3.模式合并

在Pig中，不用为数据流中每一个新产生的关系声明模式。在大多数情况下，Pig 能够根据输入关系的模式来确定关系操作的输出结果的模式。

那么模式是如何传播到新关系的呢？有些关系操作不会改变模式。因此，limit 操作（对一个关系的最大元组数进行限制）产生的关系就和它所处理的关系具有相同 的模式。对于其他操作，情况可能更复杂一些。例如，UNION操作将两个或多个 关系合并成一个，并试图同时合并输入关系的模式。如果这些模式由于数据类型 或字段个数不同而不兼容，那么UNION产生的模式是未知的。

针对数据流中的任何关系，都可以使用DESCRIBE操作来获取它们的模式。如果 要重新定义一个关系的模式，可以使用带AS子句的FOREACH...GENERATE操作来定 义输入关系的一部分或全部字段的模式。

16.5节在讨论用户自定义函数时将进一步讨论模式

###### 16.4.6 函数

Pig中的函数有四种类型

1.计算函数(Evalfunction)

计算函数获取一个或多个表达式作为输入，并返回另一个表达式。MAX就是一个 内置计算函数的例子，它返回一个包内所有项中的最大值。有些计算函数是聚集 函数(aggregate function),这意味着它们作用于包，并产生一个“标量值” (scalar value)o MAX就是一个聚集函数。此外，很多聚集函数是代数的(algebraic)。也就 是说这些函数的结果可以增量计算。如果用MapReduce的术语来表示，就是通过 使用combiner进行计算，代数函数的计算效率可以提高很多(参见2.4.2节对 combine!•函数的讨论)。MAX是一个代数函数，而计算一组值的“中位数” (median) 的函数则不是代数函数。

2.过滤函数(Filterfunction)

过滤函数是一类特殊的计算函数。这类函数返回的是逻辑布尔值。正如其名，过 滤函数被FILTER操作用于移除不需要的行。它们也可以用于其他以布尔条件作 为输入的关系操作或用于使用布尔或条件表达式的表达式。IsEmpty就是一个内 置过滤函数。它测试一个包或映射是否含有元素。

3.加载函数(Loadfunction)

加载函数指明如何从外部存储加载数据到一个关系

4.存储函数(Storefunction)

存储函数指明如何把一个关系中的内容存到外部存储。通常，加载和存储函数由 相同的类型实现。例如，PigStorage从分隔的文本文件中加载数据，也能以相同

的格式存储数据。



Pig有很多内置的函数 多标准数学、字符串、 些函数列表。



表16-7列出了其中的一部分。完整的内置函数列表包括很 日期/时间和集合函数。Pig的每个发布版本文档都包含这

| 表16-7. Pig的部分内置函数 |                        |                                                              |
| ------------------------- | ---------------------- | ------------------------------------------------------------ |
| 类别计算                  | 函数名称AVG            | 描述计算包中项的平均值                                       |
|                           | CONCAT                 | 把两个字节数组或字符数组连接成一个                           |
|                           | COUNT                  | 计算一个包中非空值的项的个数                                 |
|                           | COUNT^STAR             | 计算一个包中的项的个数，包括空值                             |
|                           | DIFF                   | 计算两个包的差。如果两个参数不是包，那么如果它们相同，则返回 一个包含这两个参数的包；否则返回一个空的包 |
|                           | MAX                    | 计算一个包中项的最大值                                       |
|                           | MIN                    | 计算一个包中项的最小值                                       |
|                           | SIZE                   | 计算一个类型的大小。数值型的大小总是丨；对于字符数组，它返回 字符的个数*对于字节数组，它返回字节的个数；对于容器 （container,包括元组、包、映射），它返回其中项的个数 |
|                           | SUM                    | 计算一个包中项的值的总和                                     |
|                           | TOBAG                  | 把一个或多个表达式转换为单独的元组，然后把这些元组放人包，它 是（）的同义词 |
|                           | TOKENIZE               | 对一个字符数组进行标记解析，并把结果词放入一个包             |
|                           | TOMAP                  | 将偶数个表达式转换为一个键-值对的映射，它是［］的同义词      |
|                           | TOP    .               | 计算包中最前面的《个元组                                     |
|                           | TOTUPLE                | 将一个或多个表达式转换为一个元组，它是｛｝的同义词           |
| 过滤                      | IsEmpty                | 判断一个包或映射是否为空                                     |
| 加载/ 存储                | PigStorage             | 用字段分隔文本格式加载或存储关系。用一个可设置的分隔符（默认 为一个制表符）把每一行分隔为字段后，将它们分别存储于元组的各 个字段。这是不指定加载/存储方式时的默认存储函数35 |
|                           | TextLoader             | 从纯文本格式加载一个关系。每一行对应于一个元组。每个元组只包 含一个字段，即该行文本 |
|                           | JsonLoader^JsonStorage | 从（Pig定义的）JSON格式加载关系，或将关系存储为JSON格式。每 个元组存储为一行 |

①通过设置pig.default.load.func和pig.default.store.func可以更改默认存储为完全 限定的加载和存储函数类名。

续表

| 类别  | 函数名称                    | 描述                                                     |
| ----- | --------------------------- | -------------------------------------------------------- |
| 加载/ | AvroStorage                 | 从Avro数据文件中加载关系，或将关系存储至Avro数据文件中   |
| 存储  | ParquetLoader^ParquetStorer | 从Parquet文件中加载关系，或将关系存储至Parquet文件中     |
|       | OrcStorage                  | 从Hive ORCFiles中加载关系，或将关系存储至Hive ORCFiles中 |
|       | HBaseStroage                | 从HBase表中加载关系，或将关系存储至HBase表中             |

5. 一些其他库

如果没有找到你需要的函数，你可以撰写自己的用户自定义函数(或简称为 UDF)，如16.5节所述。但在此之前，可以先查看一下Piggy Bank (<https://cwiki.apache.org/confluence/display/PIG/PiggyBank?)0> 这是一个 Pig 社区共 享Pig函数的库。例如，在Piggy Bank中有CSV文件、Hive RCFiles、 SequenceFiles,以及XML文件的加载和存储函数。Pig网站中有关于Piggy Bank 的JAR文件，不需要配置就可直接使用。Pig的API文档中包含Piggy Bank提供 的函数列表。

Apache DataFu ([http://datafu.incubator.apache.org/)](http://datafu.incubator.apache.org/)%e6%98%af%ef%bc%8c%e5%8f%a6%e4%b8%80%e4%b8%aa%e8%95%b4%e5%bf%b5%e7%be%8a%e5%af%8c)[是，另一个蕴念羊富](http://datafu.incubator.apache.org/)%e6%98%af%ef%bc%8c%e5%8f%a6%e4%b8%80%e4%b8%aa%e8%95%b4%e5%bf%b5%e7%be%8a%e5%af%8c)    UDF 的

库。除了常用函数之外，它还包括计算基本统计数据的函数、执行取样与评估的 函数、实现散列的函数以及与web数据(会话流程、链路分析)相关的函数。

###### 16.4.7 宏

宏提供了在Pig Latin内部对可重用的Pig Latin代码进行打包的功能。例如，我们 可以把对关系进行分组并在每一组内查找最大值的Pig Latin程序抽出来，定义成 如下的宏：

DEFINE max一by—group(X, groupjcey, max—field) .RETURNS Y {

A = GROUP $X by $group_key;

$Y = FOREACH A GENERATE group, MAX($X.$max_field);

}; —

ik.L



一个关系X以及两个字段 在宏的体内，参数和返回别



这个宏的名字为max_by_group0它包含三个参 名:group_key和max_field0它返回一个关系 名通过使用$前缀进行引用，例如$X。

宏的使用方法如下:

records = LOAD 'input/ncdc/micro-tab/sample.txt1 AS (yearzchararray^ temperature:int, quality:int); filtered_records = FILTER records BY temperature != 9999 AND quality IN (0, 1, 4, 5, 9);

max一temp = max^by^groupCfiltered^records, year, temperature);

DUMP max一temp

在运行时，Pig将用宏的定义展开宏。展开后的程序如下所示，被展开的部分用粗 体表示：

records = LOAD 'input/ncdc/micro-tab/sample.txt *

AS (yearichararray, temperature:int^ quality:int); filtered_records = FILTER records BY temperature != 9999 AND quality IN (0， 1， 4， 5， 9);

macro_max_by_group_A_0 = GROUP filtered_records by (year); max—temp = FOREACH macro_max_by_group_A_0 GENERATE group,

MAX(filtered_records.(temperature));

DUMP max_temp

一般情况下，你看不到程序展开后的形式，这是因为Pig是在内部进行这一操作 的。但是，有些情况下，在撰写和调试宏时，如果能看到展开后的形式会比较有 益，那么可以通过给pig命令传递-dryrun参数让Pig只进行宏扩展(而不执行 脚本)。

注意，传递给宏的参数(filtered_records、year以及temperature)已经取代

了宏定义中的参数名。宏定义中不使用$前缀的别名，例如示例中的A,仅在宏定 义的局部有效。在展开时，它们被重写，以避免和程序的其他部湖饱I哈在这个

例子中，A在展开后的献为macro_max_byjgroup_A_0o

为了方便重用，也可以在Pig脚本以外的文件中定义宏。这种情况下，需要在使 用宏的脚本中导入这些文件。导入语句的形式如下：

IMPORT •./chl6-pig/src/main/pig/max_temp.macro1;

##### 16.5用户自定义函数

Pig设计者认识到以插件形式提供使用用户定制代码的能力是一个关键问题，但这 也是数据处理中最琐碎的工作。因此，他们的设计简化了用户自定义函数的定义与 使用。我们在本节中只介绍Java UDF。你也可以使用Python、JavaScript、Ruby 或Groovy来写UDF，它们都使用Java Scripting API来运行。

###### 16.5.1 过滤 UDF

我们通过编写一个过滤不满足气温质量读数要求的天气记录的函数来演示如何写 过滤函数。我们的基本思路是修改下面的代码：

filtered—records = FILTER records BY temperature != 9999 AND quality IN (0， 1， 4，5, 9);

修改后的代码如下：

filtered records = FILTER records BY temperature != 9999 AND isGood(quality);

这样写有两个好处：使Pig脚本更精简；而且还封装了处理逻辑，以便轻松重用 干其他脚本。如果只是写一个即时查询，我们可能不需要麻烦地为此而写一个 UDF，只有在需要不断做相同的处理时，才需要如此写可重用的UDF。

所有的过滤函数都是FilterFunc的子类，而FilterFunc本身是EvalFunc的 子类。我们后面会对EvalFunc进行更详细的介绍。在这里，我们只需要知道 EvalFunc本质上就像下面的类这样：

public abstract class EvalFunc<T> { public abstract T exec(Tuple input) throws IOException;

}

EvalFunc只有一个抽象方法exec()。它的输入是一个元组，输出则只有一个

值，其(参数化)类型为T。输入元组的字段包含传递给函数的表达式，在这个例子 里，它是一个整数。对于FilterFunc，T是Boolean类型的，对于那些不应该 被过滤掉的元组，该方法应该返回true。

对干本例中的质量过滤器，我们要写一个IsGoodQuality类，扩展FilterFunc 并实现exec()，参见范例16-1。Tuple类本质上是一个与某个类型关联的对象列 表。在这里，我们只关心第一个字段(因为函数只有一个参数)。我们用Tuple的 get()方法，根据序号来获取这个字段。该字段的类型是整型。因此，如果它非 空，我们就对它进行类型转换，检查它是否表示气温读数是正确的，并根据检查 结果返回相应的值：true或者false。

范例16-1.该FiterFunc UDF删除包含不符合质量要求的气温读数记录 package com.hadoopbook.pig;

import java.io.IOException; import java.util.ArrayList; import java.util.List;

import org.apache.pig.FilterFunc;

import

import

import

import



public



org.apache.pig.backend.executionengine.ExecExceptionj org.apache.pig.data.DataType; org.apache.pig.data.Tuple;

org.apache.pig.impl.logicalLayer.FrontendException;

class IsGoodQuality extends FilterFunc {

^Override

public Boolean exec(Tuple tuple) throws IOException { if (tuple == null || tuple.size() == 0) {

return false;

}

try {

Object object = tuple.get(0); if (object == null) {

return false;

}

int i = (Integer) object;

return i == 0 || i == 1 || i == 4 || i == 5 || i == 9; } catch (ExecException e) {

throw new IOException(e);

}

}

}

为了使用新函数，我们首先进行编译，并把它打包到一个JAR文件（在本书所附的 示例代码中，包含如何进行打包的指导）。然后，我们通过REGISTER操作指定文 件的本地路径（不带引号），告诉Pig这个JAR文件的信息：

grunt>REGISTER pig-examples•jar;

后，我们就可以调用这个函数:

grunt>filtered_records = FILTER records BY temperature != 9999 AND >> com•hadoopbook.pig•IsGoodQuality(quality);

Pig把函数名作为Java类名并试图用该类名来加载类以完成函数调用。（这也就是 为什么函数名是大小写敏感的，因为Java类名是大小写敏感的。）在搜索类的时 候，Pig会使用包含已注册JAR文件的类加载器。运行于分布式模式时，Pig会确 保将JAR文件传输到集群。

对于本例中的 UDF, Pig 使用 com.hadoopbook.pig.IsGoodQuality 名称进行 查找，能在我们注册的JAR文件中找到它。

内置函数的解析也使用同样的方式处理。内置函数和UDF的处理有一个区别:

Pig会搜索一组内置包。因此，对于内置函数的调用并不一定要提供完整的名称。

例如，函数MAX实际上是由包org.apache.pig.builtin中的类MAX实现的。

这也是Pig搜索的内置包，所以我们在Pig程序中可以使用MAX而不需要用

org.apache.pig.builtin.MAX0

我们可以通过使用以下命令行参数唤醒Grunt,从而把我们的包加入搜索路径中：

-Dudf.import.list=com.hadoopbook•pig

或者，我们也可以使用DEFINE操作为函数定义别名，以缩短函数名:

grunt〉 DEFINE isGood com.hadoopbook•pig.IsGoodQuality();

grunt>filtered_records = FILTER records BY temperature != 9999 AND isGood(quality);

需要在一个脚本里多次使用同一个函数时，为该函数定义别名是一个好办法。如 果要向UDF的实现类传递参数，必须定义别名。

使用类型



如果注册了 JAR文件并且将函数别名写入主目录下的识文件中，那么 只要启动Pig，它们就会运行。

只有在质量字段的类型定义为int时，前面定义的过滤器才能正常工作。如果没 有类型信息，这个UDF就不能正常处理，因为此时该字段的类型是默认类型

bytearray,表示为 DataByteArray 类，而 DataByteArray 不是整型，因此类

型转换失败。

修正这一问题最直接的办法是在exec()方法中把该字段转换成整型。但更好的办 法是告诉Pig该函数所期望的各个字段的类型。EvalFunc为此提供了 getArgToFuncMapping()方法。我们可以重载这个方法来告诉Pig第一个字段应 该是整型。

^Override

public List<FuncSpec> getArgToFuncMapping() throws FrontendException { List<FuncSpec> funcSpecs = new ArrayList<FuncSpec>(); funcSpecs.add(new FuncSpec(this.getClass().getName()

new Schema(new Schema.FieldSchema(null, DataType.INTEGER))));

return funcSpecs;

}

这个方法为传递给exec()方法的那个元组的每个字段返回一个FuncSpec对象。

在这个例子里，只有一个字段。我们构造一个匿名FieldSchema (因为Pig在进 行类型转换时忽略其名称，因此其名称以null传递)。该类型使用Pig的 DataType类的常量INTEGER进行指定。

使用这个修改后的函数，Pig将尝试把传递给函数的参数转换成整型。如果无法转 换这个字段，则把这个字段传递为null。如果字段为null, exec()方法返回的 结果总是false。在这个应用中，因为我们想要在过滤掉质量字段无法理解的记 录，所以这样做很合适。

###### 16.5.2 计算 UDF

写计算函数比写过滤函数的步骤要稍微多一些。让我们考虑范例16-2中的UDF。 它类似于java.lang.String中trim()方法，可以从chararray值中去掉开头

和结尾的空白符。®

范例16-2.该EvalFuncUDF从chararray值中去除开头和结尾的空白符

public class Trim extends PrimitiveEvalFunc<String^ String〉 {

^Override

public String exec(String input) { return input.trim();

}

}

上述示例利用r PrimitiveEvalFunc函数，它是EvalFunc函数在输入数据类型为一个 原子类型时的“特化” (specialization)0 Trim UDF的输入和输出数据类型皆为

String"o

在下面的语句中，B的模式由函数



在写计算函数的时候，需要考虑输出模式

udf决定：

B = FOREACH A GENERATE udf($0)；

如果udf创建了有标量字段的元组，那么Pig可以通过“反射”(reflection)来确定 B的模式。对于复杂数据类型，例如包、元组或映射，Pig需要更多的信息。此时 需要实现outputSchema()方法将输出模式的相关信息告诉Pig。

①    实际上Pig中有一个等价的内置函数，称为TRIM。

②    虽然和这个示例无关，但如果计算函数要处理一个包(bag)，可能还需要另外实现Pig的 Algebraic或Accumulator接口来提高以块(chunk)方式处理包的效率。

Trim UDF返回一个字符串。Pig把返回值翻译为chararray类型，如以下会话 所示：

grunt>DUMP A;

(pomegranate)

(banana )

(apple)

(lychee )

grunt〉 DESCRIBE A;

A: {fruit: chararray}

grunt>B = FOREACH A GENERATE com.hadoopbook.pig.Trim(fruit); grunt>DUMP B;

(pomegranate)

(banana)

(apple)

(lychee)

grunt>DESCRIBE B;

B: {chararray}

A包含的chararray字段中有开头和结尾的空白符。我们将Trim函数应用于A 的第一个字段(名为fruit),从而创建了 B。B的字段被正确推断为chararray。

动态调用

有时你希望使用由某个Java库提供的函数，而不是自己撰写UDF。动态调用器 (invoker)使你可以在Pig脚本中直接调用Java方法。使用这种方法的代价是，对 于方法的调用是通过Java的反射(reflection)机制进行的。如果要为一个很大的数 据集的每行记录进行调用，则会导致巨大的处理开销。所以，对于需要重复运行 的脚本，最好使用专用的UDF。

下面的代码片段展示如何定义和使用基于Apache Commons Lang的StringUtils 类的 trim UDF：

grunt>DEFINE trim

InvokeForString(1org.apache.commons•lang•StringUtils.trim','String'); grunt>B = FOREACH A GENERATE trim(fruit); grunt>DUMP B;

(pomegranate)

(banana)

(apple)

(lychee)

使用调用器InvokeForString的原因是该方法的返回值类型是String。此外还有 InvokeForlnt、InvokeForLong、InvokeForDouble、InvokeForFload。调用器构造函

数的第一个参数应当是可被调用的方法。第二个参数为以空格分隔的方法参数类 的列表。

###### 16.5.3 加载 UDF

我们将演示一个定制的加载函数，该函数可以通过指定纯文本的列的区域定义字 段，和Unix的cut命令非常类似。°它的用法如下：

grunt>records = LOAD Jinput/ncdc/micro/sample.txt^

\>>USING com.hadoopbook.pig.CutLoadFunc(’16-l%88-92,93-93’) >>AS (yeanintj temperature:intj quality:int);

grunt〉 DUMP records;

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

传递给CutLoadFunc的字符串是对列的说明：每一个由逗号分隔的区域定义了一

个字段。字段的名称和数据类型通过AS子句进行定义。让我们来看范例16-3给 出的CutLoadFunc的实现：

范例11-3.该加载函数以列区域作为字段加载元组

public class CutLoadFunc extends LoadFunc {

private static final Log LOG = LogFactory.getLog(CutLoadFunc.class);

private final List<Range> ranges;

private final TupleFactory tupleFactory = TupleFactory.getlnstance(); private RecordReader reader;

public CutLoadFunc(String cutPattern) { ranges = Range.parse(cutPattern);

}

^Override

public void setLocation(String location， Job job) throws IOException {

FilelnputFormat.setInputPaths(job^ location);

}

^Override

public InputFormat getInputFormat() { return new TextInputFormat();

}

^Override

public void prepareToRead(RecordReader reader, PigSplit split) { this.reader = reader;

}

①在Piggy Bank中有一个功能更全面的UDF可以完成同样的工作，称为FixedWidthLoader。

^Override

public Tuple getNext() throws IOException {

![img](Hadoop43010757_2cdb48_2d8748-197.jpg)



if (!reader•nextKeyValue()) { return null;

}

Text value = (Text) reader.getCurrentValue();

String line = value.toString();

Tuple tuple = tupleFactory.newTuple(ranges.size()); for (int i = 0; i < ranges.size(); i++) {

Range range = ranges.get(i);

if (range.getEnd() > line.length()) {

LOG.warn(String.format(

” Range end (%s) is longer than line length (%s)” ) range.getEnd(), line.length()));

continue;

}.

tuple.set(i，new DataByteArray(range.getSubstring(line)));

}

return tuple;

} catch (InterruptedException e) { throw new ExecException(e);

}

}

}

和Hadoop类似，Pig的数据加载先于mapper运行，所以保证数据可以被分割成能 被各个mapper独立处理的部分非常重要，8.2.1节在讨论输入分片和记录时，介 绍了更多背景知识。LoadFunc 一般使用底层已有的InputFormat来创建记录， 而LoadFunc自身则提供把返回记录变为Pig元组的程序逻辑。

CutLoadFunc类使用说明了每个字段列区域的字符串作为参数进行构造。解析该 字符串并创建内部Range对象列表以封装这些区域的程序逻辑包含在Range类 中。这里没有列出这些代码(可在本书所附的示例代码中找到)。

为

此我们只用



'ig调用LoadFunc的setLocation()把输入位置传输给加载器 :utLoadFunc使用TextlnputFormat把输入切分成行， ilelnputFormat的一个静态方法传递设置输入路径的位置信息，

I Pig使用了新的MapReduceAPI 0因此，我们使用的是org. apache, hadoop.mapreduce包的输人和输出格式及其关联的类。

然后，和在MapReduce中一样，Pig调用getl叩utFormat()方法为每一个分片

新建一个 RecordReader。Pig 把每个 RecordReader 传递给 CutLoadFunc 的 prepareToReadO方法以便通过引用来进行传递，这样，我们就可以在 getNext()方法中用它遍历记录。

Pig运行时环境会反复调用getNext()，然后加载函数从reader中读取元组直到 reader读到分片中的最后一条记录。此时，加载函数返回空值null以报告已经没 有可读的元组。

负责把输人文件的行转换为Tuple对象是getNext()的任务。它利用Pig用于创 建Tuple实例的类TupleFactory来完成这一工作。newTuple()方法新建一个 包含指定字段数的元组。字段数就是Range类的个数，而这些字段使用Range对 象所确定的输入行中的子串填充。

我们还需要考虑输入行比设定范围短的情况。一种选择是抛出异常并停止进一步 的处理。如果你的应用不准备在碰到不完整或损坏的记录时继续工作，这样处理 当然没有问题。在很多情况下，另一种更好的选择是返回一个有null字段的元 组，然后让Pig脚本根据情况来处理不完整的数据。我们这里采取的是后一种方

法：当区域超出行尾时，通过终止for循环把元组随后的字段都设成默认值

null.

使用模式

现在让我们来考虑加载的字段数据类型。如果用户指定模式，那么字段就需要转 换成相应的数据类型。但在Pig中，这是在加载后进行的。因此，加载器应该始 终用类型DataByteArray来构造包含bytearray字段的元组。当然，我们也可 以让加载器函数来完成类型转换。这时需要重载getLoadCaster(),以返回包含 一组类型转换方法的定制LoadCaster接口实现。

CutLoadFunc 并没有重载 getLoadCaster()。因为默认的 getLoadCaster()实现 返回了 UtfSStorageConverter。它提供了 UTF-8编码数据到Pig数据类型的标 准的转换功能。

在有些情况下，加载函数本身可以确定模式。例如，如果我们在加载XML或 JSON这样的自描述数据，则可以为Pig创建一个模式来处理这些数据。此外，加 载函数可以使用其他方法来确定模式，例如使用外部文件，或通过传递模式信息 给构造函数。为了满足这些需要，加载函数应该(在实现LoadFunc接口之外)实现 LoadMetadata接口，向Pig运行时环境提供模式。但是请注意，如果用户通过

LOAD的AS子句定义模式，那么它的优先级将高于通过LoadMetadata接口定义的模

式。

为此时加载 在示例中， 鉴于此，我



加载函数还可以实现LoadPushDown接口，了解查询需要哪些列。

器可以只加载查询需要的列，因此这可能有助于按列存储的优化。 CutLoadFunc需要读取元组的整行，所以只加载部分列不容易实现，

们在这里不使用这种优化技术。

##### 16.6数据处理操作

###### 16.6.1数据的加载和存储

在本章中，我们已经看过Pig如何从外部存储加载数据来进行处理。与之相似， 存储处理结果也是非常直观的。下面的例子使用PigStorage将元组存储为以冒 号分隔的纯文本值：

grunt〉STORE A INTO，ouV USING PigStorage。：，

grunt> cat out

Joe:cherry:2

Ali:apple:3

Doe:banana:2

Eve:apple:7

其他内置存储函数参见前面的表16-7。

###### 16.6.2数据的过滤

如果你已经把数据加载到关系中，那么下一步往往是对这些数据进行过滤，移除 你不感兴趣的数据。通过在整个数据处理流水线的早期对数据进行过滤，可以使 系统数据处理总量最小化，从而提升处理性能。

\1. FOREACH...GENERATE 操作

我们已经介绍了如何使用带有简单表达式和UDF的FILTER操作从一个关系中移 除行。FOREACH...GENERATE操作用于逐个处理一个关系中的行。它可用于移除字 段或创建新的字段。在这个示例里，我们既要删除字段，也要创建字段：

grunt〉 DUMP A; (Joe,cherry,2) (Ali,apple3)

(Joe^banana^)

(Eve,apple，7)

.grunt>B = FOREACH A GENERATE $0, $2+1, JConstant grunt>DUMP B;

(ZJoeJ,Constant)

(Ali"onstant)

(Doe^^Constant)

(Eve^^Constant)

在这里，我们已经创建了一个有三个字段的新关系B。它的第一个字段是A的第 一个字段($0)的投影。B的第二个字段是A的第三个字段($2)加1。B的第三个字

段是一个常量字段(即B中每一行在第三个字段的取值都相同)，其类型为

chararray,取值为 Constant。

FOREACH...GENERATE操作可以使用嵌套形式以支持更复杂的处理。在如下示例 中，我们计算天气数据集的多个统计值：

--year_stats.pig REGISTER pig-examples.jar;

DEFINE isGood com.hadoopbook.pig.IsGoodQuality(); records = LOAD Jinput/ncdc/all/19{l，2y，5}0*’

USING com.hadoopbook.pig.CutLoadFunc(’5-10,11-15,16-19,88-92,93-93’)

AS (usaf:chararray, wban:chararray, year:int, temperature:int, quality:int);

grouped_records = GROUP records BY year PARALLEL 30;

year_stats = FOREACH grouped_records { uniq_stations = DISTINCT records.usaf; good一records = FILTER records BY isGood(quality);

GENERATE FLATTEN(group), COUNT(uniq_stations) AS station_count,

COUNT(good_records) AS good—record_count, COUNT(records) AS record_count;

} "

DUMP year_stats;

通过使用我们前面开发的cut UDF，我们从输人数据集加载多个字段到records 关系中。接下来，我们根据年份对records进行分组。请注意，我们使用关键字 PARALLEL来设置要使用多少个reducer。这在使用集群进行处理时非常重要。然 后，我们使用嵌套的FOREACH...GENERATE操作对每个组分别进行处理。第一重嵌 套语句使用DISTINCT操作为每一个气象观测站的USAF标识创建一个关系。第 二层嵌套浯句使用FILTER操作和一个UDF为包含“好”的读数的记录创建一个 关系。最后一层嵌套语句是GENERATE语句(嵌套FOREACH…GENERATE语句必须 以GENERATE语句作为最后一层嵌套语句)。该语句使用分组后的记录和嵌套语句 块创建的关系生成了需要的汇总字段。

在若干年数据上运行以上程序，我们得到如下结果:

(1920,8L,8595L,8595L)

(1950,1988L,8635452L,86413S3L) ^1930,121L,89245L,89262L) (1910,7L,7650L,7650L) (1940,732L,1052333L,1052976L)

这些字段分别表示年份、不同气象观测站的个数、好的读数的总数和总的读 从中我们可以看到气象观测站个数和读数个数是如何随着时间变化而增长的。

\2. STREAM 操作

STREAM操作让你可以用外部程序或脚本对关系中的数据进行变换。这一操作的命 名对应于Hadoop的Streaming,后者为MapReduce的提供类似能力（参见2.5节对 Hadoop Streaming 的讨论）。

STREAM可以使用内置命令作为参数。下面的例子使用Unix cut命令从A中每个 元组抽取第二个字段。注意，命令及其参数要用反向撇号引用：

grunt>C = STREAM A THROUGH 'cut -f 2'; grunt〉 DUMP C;

(cherry)

(apple)

(banana)

(apple)

STREAM操作使用PigStorage来序列化/反序列化关系，输出为程序的标准输出流或 从标准输入流读人。A中的元组变换成由制表符分隔的行，然后传递给脚本。脚 本的输出结果被逐行读人，并根据制表符来划分以创建新的元组，然后输出到关 系C。也可以使用DEFINE操作，通过实现PigToStream和StreamToPig（两者 都包含在org.apache.pig包中）来提供定制的序列化和反序列化程序。

在编写定制的处理脚本时，Pig流式处理是最有用的。以下的Python脚本用于筛 选气温记录：

\#!/usr/bin/env python

import re

import sys

for line in sys.stdin:

(year， temp， q) = line.strip().split() if (temp != "9999" and re.match("[01459]", q)):

print "%s\t%s" % (year, temp)

要使用这一脚本，需要把脚本传输到集群上。这可以通过DEFINE子句来完成。 该子句还为STREAM命令创建了一个别名。然后，便可以像下面的Pig脚本那样在 STREAM语句中使用该别名：

--max_ temp_fi Lter_st ream, pig

DEFINE is_good__quality ' is_good_quality. py'

SHIP (* chl6-pig/src/main/python/is_good_quality.py*)； records ; LOAD 1input/ncdc/micro-tab/sample.txt1

AS (year:chararray, temperature:int, quality:int); filtered_records = STREAM records THROUGH is_good_quality

AS (year:chararray, temperature:int); grouped_records = GROUP filtered_records BY year; max一temp = FOREACH grouped_records GENERATE group,

MAX(filtered_records.temperature);

DUMP max一tempj

###### 16.6.3数据的分组与连接

在MapReduce中对数据集进行连接操作需要程序员写不少程序，详情参见9.3节 对连接的讨论。Pig为连接操作提供很好的内置支持，简化了数据集的连接。因为 只有非规范化的大规模数据集才最适宜使用Pig(或MapReduce)这样的工具进行分 析，因此连接在Pig中的使用频率远小于在SQL中的使用频率。

1.关于JOIN语句

让我们来看一个内连接的示例。考虑有如下关系A和B:

grunt〉 DUMP A;

(2,Tie)

(4,Coat)

(3,Hat)

(l^Scarf) grunt〉 DUMP B;

(]oe,2)

(Hank,)

(Ali,0)

(Eve,3)

(Hank,2)

我们可以在两个关系的数值型(标识符)属性上对它们进行连接操作:

grunt>C = JOIN A BY B BY $1; grunt>DUMP C;

(2,Tie,Joe,2)

(2，Tie，Hank，2)

(3，Hat，Eve,3)

(4,Coat,Hank,4)

这是一个典型的内连接(inner join)操作：两个关系元组的每次匹配都和结果中的一 行相对应。因为连接谓词(join predicate)为相等，所以这其实是一个等值连接 (equijoin)。结果中的字段由所有输人关系的所有字段组成。

如果要进行连接的关系太大，不能全部放在内存中，则应该使用通用的连接操 作。如果有一个关系小到能够全部放在内存中，则可以使用一种特殊的连接操 作，即分段复制连接(fragment replicate join),它把小的输入关系发送到所有 mapper，并在map端使用内存查找表对(分段的)较大的关系进行连接。要使用特 殊的语法让Pig使用分段复制连接'

grunt>C = JOIN A BY $0, B BY $1 USING "replicated";

这里，第一个关系必须是大的关系，后面则是一个或多个相对较小的关系(能够全 部存放在内存中)。

Pig也支持通过使用类似于SQL的语法(17.7.3节在讨论外连接时将介绍Hive相关 语法)进行外连接(outerjoin)。例如:

grunt>C = JOIN A BY $0 LEFT OUTER, B BY $1； grunt>DUMP C;

了1,Scarf,J ^^Tie^Joe^)

<2,Tie,Hank,2)

(3,Hat,Eve,3)

<4,Coat,Hank,4)

2.关于COGROUP语句

JOIN结果的结构总是“平面”的，即一组元组。COGROUP语句和］OIN类似，但 是不同点在于，它会创建一组嵌套的输出元组集合。如果你希望利用如下语句中 输出结果那样的结构，那么COGROUP将会有用：

grunt>D = COGROUP A BY $0, B BY $1; grunt>DUMP D;

(0,{},{(Ali,0)})

(l,{(l,Scarf)},{})

(2,{(2,Tie)h{(Hank,2),(：loe,2)})

(3,{(3,Hat)h{(Ev〜3)})

(4,{(4,Coat)},{(Hank,4)})

①在USING子句中还可以使用其他关键同，包括“skewed”(为包含偏斜的键值空间的大规模 数据集使用)、“merge”(为在要连接的键上已经进行了排序的输入关系上使用合并连接)和 “merge-sparse” (小于等干1%的数据是匹配的)。具体如何使用这些特殊的连接操作，请 参见Pig的帮助文档。

COGROUP为每个不同的分组键值生成一个元组。每个元组的第一个字段就是那 个键值。其他字段是各个关系中匹配该键值的元组所组成的包。第一个包中包含 关系A中有该键值的匹配元组。同样，第二个包中包含关系B中有该键值的匹配元 组0 如果某个键值在一个关系中没有匹配的元组，那么对应于这个关系的包就为空。

在前面的示例中，因为没有人购买围巾（ID为1），所以对应元组的第二个包就为 空。这是一个外连接的例子。COGROUP的默认类型是外连接。可以使用关键词

OUTER来显式指明使用外连接，COGROUP产生的结果和前一个语句相同:

D = COGROUP A BY $0 OUTER, B BY $1 OUTER;

也可以使用关键词INNER让COGROUP使用内连接的语义，剔除包含空包的行。 INNER关键词是针对关系进行使用的，因此如下语句只去除关系A中不匹配的行 （在这个示例中就是去掉未知商品0对应的行）：

grunt>E = COGROUP A BY $0 INNER, B BY $1; grunt>DUMP E;

(1,{(1,Scarf)},{}) (2J(2,Tie)h{(Hank,2),(〕oe,2)}) (3,{(3,Hat)h{(EVe,3)}) (4,{(4JCoat)},{(Hank,4)})

我们可以把这个结构平面化，从A找出买了每一项商品的人。

grunt>F = FOREACH E GENERATE FLATTEN(A), B.$0; grunt>DUMP F;

(1,Scarf,{})

(2,Tie,{(Hank),(〕oe)})

(3,Hat,{(Eve)})

Woat^nHank)})

把COGROUP、INNER和FLATTEN(消除嵌套)组合起来使用相当于实现了(内)连接:

grunt>G = COGROUP A BY $0 INNER, B BY $1 INNER; grunt>H = FOREACH G GENERATE FLATTEN($1), FLATTEN($2)； grunt>DUMP H;

(2，Tie，Hank，2)

(3,Hat,Eve,3)

(4，Coat川ank，4)

460 第16章



##### 这和］OIN A BY $0,B BY $1的结果是一样的。

如果要连接的键由多个字段组成，则可以在］OIN或COGROUP语句的BY子句中把

它们都列出来。这时要保证毎个BY子句中的字段个数相同。

I、*面是如何在Pig中进行连接的另一个示例 测站报告的最尚气温：

该脚本计算输人的时间段内毎个观



--mox_temp_station_name.pig

REGISTER pig-examples.jar;

DEFINE isGood com.hadoopbook.pig.IsGoodQuality();

stations = LOAD input/ncdc/metadata/stations-fixed-width.txt USING com•hadoopbook.pig•CutLoadFunc(’1-6,8-12,14-42’)

AS (usaf:chararray, wban:chararray, name:chararray);

trimmed_stations = FOREACH stations GENERATE usaf, wban, TRIM(name);

records = LOAD Jinput/ncdc/all/191*^

USING com.hadoopbook.pig.CutLoadFunc(’5-10,11-15,88-92,93-93’)

AS (usaf:chararray, wban:chararray, temperature:int, quality:int);

filtered—records = FILTER records BY temperature != 9999 AND isGood(quality); grouped一records = GROUP filtered_records BY (usaf) wban) PARALLEL 30; maxjtemp = FOREACH grouped_records GENERATE FLATTEN(group)

MAX(filtered_records.temperature)j

max一temp_named = JOIN max一temp BY (usaf, wban), trimmed_stations BY (usaf, wban) PARALLEL 30;

max_temp_result = FOREACH max^temp—named GENERATE $0) $1) $5, $2;

STORE max_temp_result INTO ’maxjtemp_by_station’；

我们使用先前开发的cut UDF来加载包括气象观测站1D(USAF和WBAN标识)、 名称的关系以及包含所有气象记录且以观测站ID为键的关系。我们在根据气象观 测站进行连接之前，先根据观测站ID对气象记录进行分组和过滤，并计算最高气 温的聚集值。最后，在进行连接之后，我们把所需要的字段——HU USAF、 WBAN,观测站名称和最高气温一一投影到最终结果。

下面是20世纪头10年的结果：

因为观测站的元数据较少，所以这个查询可以通过使用分段复制连接来进一步提 升运行效率。

3.关于CROSS语句

PigLatin包含叉乘(cross-product,也称“笛卡儿积”)操作。这一操作把一个关系

中的每个元组和第二个中的所有元组进行连接(如果有更多的关系，那么这个操作 就进一步把结果逐一和这些关系的每一个元组进行连接)。这个操作的输出结果的 大小是输入关系的大小的乘积。输出结果可能会非常大：

grunt>I = CROSS A, B; grunt>DUMP I; (2,Tie,：Joe,2)

(2,Tie,Hank,4)

(2,Tie,Ali,0)

《2,Tie,Eve,3$

(2，Tie，Hank，2)

(4,Coat,]oe,2)

(4，Coat，Hank，4)

(4,Coat,Ali,0)

(4,Coat,Eve,3)

(4JCoat>Hank>2)

(3,Hat,〕oe,2)

(3，Hat，Hank,4)

(3,Hat,Ali，0)

(3,Hat,Eve,3$

(3,Hat,Hank,2)

(1,Scarf,]oej2) (l，Scarf，Hank，4)

(1,Scarf,Ali,0) (l,Scarf,Eve,3) (l，Scarf，Hank，2)

在处理大规模数据集时，应该尽量避免会产生平方(或更差)级中间结果的操作。只 有在极少数情况下，才需要对整个输入数据集计算叉乘。

例如，一开始，用户可能觉得必须生成文档集合中所有文档的两两配对组合才能 计算文档两两之间的相似度。但是，随着对数据和应用的深人了解，他会发现大 多数文档配对的相似度为零(即它们之间没有关系)。于是，我们就能找到一种更好 的算法来计算相似度。

在此，解决这一问题的主要思路是把计算聚焦于用于计算相似度的实体，如文档 中的关键词(term),让它们成为算法的核心。事实上，我们还要删去对区分文档没 有帮助的词，即停用词(stop-word)，进一步缩减问题的搜索空间。使用这一技 术，分析近一百万个(106)文档大约会产生约十亿个(109)中间结果文档配对。®而如 果用朴素的方法(即生成输入集合的叉乘)或不消除停用词，会产生一万亿个(1012)

①引文出自 Tamer Elsayed，Jimmy Lin 和 Douglas Woard 的文章，标题为 “Pairwise Document Similarity in Large Collections with MapReduce”，Proceedings of the 46th Annual Meeting of the Association of Computational Linguistics, June 2008，网址:Xj http://bit，ly/doc_similarity 0

个文档配对。

4.关于GROUP语句

COGROUP用于把两个或多个关系中的数据放到一起，而GROUP语句则对一个关系 中的数据进行分组。group不仅支持对键值进行分组(即把键值相同的元组放到一 起)，你还可以使用表达式或用户自定义函数作为分组键。例如，有如下关系A:

grunt> DUMP A;

(ZJoe,cherry)

(Ali,apple)

(Joe^banana)

(Eve,apple)

我们根据这个关系的第二个字段的字符个数进行分组:

grunt>B = GROUP A BY SIZE($1); grunt>DUMP B;

(5,{(Eve,apple),(Ali,apple)}) (6,{(Joe^banana)(Doe^cherry)})

GROUP会创建一个关系，它的第一个字段是分组字段，其别名为group。第二个 字段是包含与原关系(在本示例中就是A)模式相同的披分组字段的包。

有两种特殊的分组操作：ALL和ANY。ALL把一个关系中的所有元组放入一个包。 这和使用某个常量函数作为分组函数所获得的结果一样：

grunt〉 C = GROUP A ALL;

grunt〉 DUMP C;

(all^{(Eve^apple),(Doe,banana),(Ali^apple),(Joe^cherry)})

注意，在这种GROUP语句中，没有关键词BY。ALL分组常用于计算关系中的元组 个数(参见16.4.5节对验证与空值的讨论)。

关键词ANY用于对关系中的元组随机分组。它对于取样非常有用。

###### 16.6.4数据的排序

Pig中的关系是无序的。考虑如下关系A:

grunt> DUMP A;

(2.3)

(1,2)

(2.4)

Pig按什么顺序来处理这个关系中的行是不一定的。特别是在使用DUMP或STORE

检索A中的内容时，Pig可能以任何顺序输出结果。如果想设置输出的顺序，可 以使用ORDER操作按照某个或某几个字段对关系中的数据进行排序。默认的排序 方式是对具有相同类型的字段值使用自然序进行排序，而不同类型字段值之间的 排序则是任意的、确定的(例如，一个元组总是小于一个包)。

如下示例对A中元组根据第一个字段的升序和第二个字段的降序进行排序：

grunt> B = ORDER A BY 多0, $1 DESC; grunt> DUMP B;

(1,2)

(2,4)

(2,3)

对排序后关系的后续处理并不保证能够维持已排好的顺序。例如：

grunt> C = FOREACH B GENERATE

即使关系C和关系B有相同的内容，关系C用DUMP或STORE仍然可能产生以任 意顺序排列的输出结果。正是由于这样，通常只在获取结果前一步才使用ORDER 操作。

LIMIT语句对于限制结果的大小以快速获得一个关系样本，非常有用。(用于随机 取样的SAMPLE操作或者使用ILLUSTRATE命令的“取原型化”(prototyping)操作 则更适用于根据数据产生有代表性的样本。)LIMIT语句可紧跟ORDER语句使用， 来获得排在最前面的个元组。通常，LIMIT会随意选择一个关系中的n个元 组。但是，当它紧跟ORDER语句使用时，ORDER产生的次序会继续保持(这和其他 操作不保持输入关系数据顺序的规则不同，是一个例外)：

grunt>D = LIMIT B 2; grunt>DUMP D;

(1,2)

(2,4)

如果所给的限制值远远大于关系中所有元组个数的总 操作没有作用)。

，则返回所有元组(LIMIT



为Pig会在处理流水线中尽早使用限制操 此，如果不需要所有输出数据，就应该用



使用LIMIT能够提升系统的性能。

作，以最小化需要处理的数据总量。

LIMIT操作。

###### 16.6.5数据的组合和切分

有时，你希望把几个关系组合在一起。为此，可以使用UNION语句。例如:

grunt>DUMP A;

(2.3)

(1,2)

(2.4)

grunt>DUMP B;

(2,x,8)

(w,y,l)

grunt> C = UNION A, B; grunt> DUMP C;

(2.3)

(z,x,8)

(1,2)

(w,y,l)

(2.4)

c是关系A和B的“并”（union）。因为关系本身是无序的，因此C中元组的顺序 是不确定的。另外，如示例中那样，我们可以对两个模式不同或字段个数不同的 关系进行并操作。Pig会试图合并正在执行UNION操作的两个关系的模式。在这 个例子中，两个模式是不兼容的，因此C没有模式：

grunt>DESCRIBE A;

A: {f0: int^fl: int}

grunt>DESCRIBE B;

B: {f0: chararray^fl: chararray,f2: int} grunt>DESCRIBE C;

Schema for C unknown.

如果输出关系没有模式，就需要脚本能处理字段个数和数据类型都不同的元组

SPLIT操作是UNION操作的反操作。它把一个关系划分成两个或多个关系。可以 参考16.4.5节讨论验证与空值时所提供的示例，了解如何使用SPLIT。

##### 16.7 Pig 实战

在开发和运行Pig应用程序时，知道一些实用技术是非常有帮助的。本小节将介 绍一些这样的技术。

###### 16.7.1并行处理

运行在MapReduce模式时，一件重要的事情是使得处理的并行度与所处理的数据 集大小相匹配。默认情况下，Pig将根据输入数据的大小设置reducer的个数：每 1 GB输入使用一个reducei•，且reducer的个数不超过999。可以通过设置 pig. exec. reducers. bytes. per. reducer（默认为 1 000000000 字节）和 pig. exec. reducers.max（默认为 999）来修改这一设置。

为了吿诉Pig每个作业要用多少个reducer,可以在reduce阶段的操作中使用 PARALLEL子句。在reduce阶段使用的操作包括所有的“分组”（grouping）和“连 接”（joining）操作（GROUP、COGROUP、JOIN 和 CROSS），以及 DISCTINCT 和 ORDER。 下面的代码将GROUP的reducer个数设为30:

grouped一records = GROUP records BY year PARALLEL 30;

也可以通过设置default_parallel选项来达到同样的目的。修改的选项将作用 于所有后续的作业：

grunt>set default_parallel 30

设置reduce任务个数的一种比较好的方式是把该参数设为稍小于集群中的reduce

<    •    瓠 + •

任务的时隙（slot）的个数。对于这个问题的详细讨论，可以参见8.1.1节，了解如何 选择reducer的个数。

map任务的个数由输入的大小决定（每个HDFS块一个map）,不受PARALLEL子句 的影响。

###### 16.7.2匿名关系

在刚刚定义了一个关系之后，通常都会紧跟着DUMP或DESCRIBE之类的诊断的操 作。由于这种情况非常普遍，因此Pig使用@来指向前一个关系，这是一种快捷方 式。同样，在使用解释器时需要为每个关系取名也是件烦人的事。Pig允许使用特 殊语法=>来创建没有别名的关系，而此类关系就只能通过@来引用。示例如下：

grunt>=> LOAD •d^put/ncdc/micro-tab/sample.txf; grunt>DUMP @

(1950,0,1)

(1950.22.1)

(1950,-11,1)

(1949.111.1)

(1949.78.1)

###### 16.7.3参数代换

如果有定期运行的Pig脚本，你可能希望让这个脚本能够在不同参数设置下运 行。例如，一个每天运行一次的脚本可能要根据日期来决定它要处理哪些输入文 件。Pig支持参数代换(parametersubstitution)，即用运行时提供的值替换脚本中的 参数。参数由前缀为$字符的标识符来表示。例如，在以下脚本中，$input和 $output用来指定输入和输出路径：

--max一temp一param.pig

records = LOAD J$inputJ AS (yearzchararray^ temperature lint, quality:int); filtered_records = FILTER records BY temperature != 9999 AND

quality IN (0， 1， 4， 5， 9); grouped一records = GROUP filtered一records BY year; max一temp = FOREACH grouped records GENERATE group,

MAX(filtered_records.temperature);

STORE max_temp into ’$output’；

参数值可以在启动Pig时使用-param选项指定，

每个参数一个:



% pig-param input=/user/tom/input/ncdc/micro-tab/sample.txt \

\>    -param output=/tmp/out \

\>    chl6-pig/src/main/pig/max一temp—param.pig

也可以把参数值放在文件中，通过-param_file选项把参数传递给Pig。例如， 把参数定义放在文件中，也可以获得相同的结果:

\#    Input file

input=/user/tom/input/ncdc/micro-tab/sample.txt

\#    Output file output=/tmp/out

对Pig的调用相应进行如下调整：

% pig -param一file chl6-pig/src/main/pig/max一temp一param.param \ > chl6-pig/src/main/pig/max_temp_param.pig

可以重复使用-pararrvfile来指定多个参数文件，还可以同时使用-param和-paran^file选项。如果同一个参数在参数文件和命令行中都有定义，那么命令行 中最后出现的参数值优先级最高。

1.动态参数

针对使用-param选项来提供的参数，很容易使其值变成动态的，运行命令或脚本 即可变为动态的。很多Unix的shell环境中都用反引号引用的命令来替换实际 值。我们可以使用这一功能实现根据日期来确定输出目录：

% pig -param input=/user/tom/input/ncdc/micpo-tab/sample•txt \

\>    ，param output=/tmp/'date "+%Y-%m,%d" '/out \

\>    chl6-pig/src/main/pig/max一temp-param.pig

Pig也支持在参数文件中的反引号，在shell中执行用反引号引用的命令，并使用 shell的输出结果作为替换值。如果命令或脚本返回一个非零的退出状态并退出， Pig会报告错误消息并终止执行。在参数文件中使用反引号是一种很有用的特性， 它意味着可以使用完全相同的方法在文件中或命令行中定义参数。

2.参数代换处理

参数代换是脚本运行前的一个预处理步骤。可以使用-dryrun选项运行Pig来查 看预处理器所进行的代换。在-dryrun模式下，Pig对参数进行代换（以及宏扩展） 并生成一个使用了代换值的原来脚本的副本，但并不执行该脚本。在普通模式 下，可以在运行之前查看生成的脚本，检查参数代换是否合理（例如，在动态生成 代换的情况下）。

##### 16.8延伸阅读

本章介绍了如何使用Pig的基本情况。若想了解更详细的内容，请参考O’Rdll） 在 20II 年出版的 Programming Pig ,网址为（http://shop.oreilly.com/produc /0636920018087.do,作者 Alan Gates。
