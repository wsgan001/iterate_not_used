住fc题为 “Information Platforms and the Rise of the Data Scientist” 的文章一文中，Jeff Hammerbacher' “信息平台”描述为“企业摄取(ingest)、处理(process)、生成 (generate)信息的行为”与“帮助加速从经验数据中学习”的“中心”。

在Facebook, Jeff团队所构建的信息平台中，最庞大的组成部分是Apache Hive (<https://hive.apache.org/)0> Hive是一个构建在Hadoop上的数据仓库框架，是应 Facebook每天产生的海量新兴社会网络数据进行管理和(机器)学习的需求而产生 和发展的。在尝试了不同系统之后，Jeff团队选择Hadoop来存储和处理数据，因 为Hadoop的性价比高，同时还能够满足他们的可伸缩性要求。

Hive的没计目的是让精通SQL技能但Java编程技能相对较弱的分析师能够对 Facebook存放在HDFS中的大规模数据集执行査询。今天，Hive已经是一个成功 的Apache项目，很多组织把它用作一个通用的、可伸缩的数据处理平台。

当然，SQL并不是所有大数据问题的理想工具。例如，它并不适合用来开发复杂 的机器学习算法。但它对很多分析任务非常有用，而且它的另一个优势是业内人 士都非常熟悉它。此外，SQL是商业智能工具的“通用语言”(可以通过ODBC这 一桥梁来用)，Hive有条件和这些产品进行集成。

①编注：最早提出“数据科学家”这个头衔的人，也是Facebook数据科学团队的负责人， Cloudera的联合创始人。这位出生于1983年的数学天才有一句名言：“我们这一代最杰出的 头脑都在拼命思考如何吸引人们点击更多的广告，这个感觉槽透了。”他后来加入纽约著名 的两奈山医院，成为一名医学研究者，运用自己的数据分析才能去攻克癌症、老年痴呆症、 糖尿病及其他慢性疾病。

本章介绍如何使用Hive。我们假设你用过SQL和常见的数据库体系结构。在介绍 Hive特性的同时，我们会经常将这些特性与其传统RDBMS对应部分进行比较。

##### 17.1 安装 Hive

Hive 一般在工作站上运行。它把SQL查询转换为一系列在Hadoop集群上运行的 作业。Hive把数据组织为表，通过这种方式为存储在HDFS上的数据赋予结构。 元数据（如表模式）存储在metastore数据库中。

刚开始使用Hive时，为了方便，可以让metastore运行在本地机器上。这一设置 是默认设置。此时，创建的Hive表的定义是在本地机器上，所以无法和其他用户 共享这些定义。17.3.3节将介绍如何设置生产环境中常用的远程共享metastore。

安装Hive的过程非常简单。首先必须在本地安装和集群上相同版本的Hadoop。1 当然，在刚开始使用Hive时，你可能会选择在本地以独立模式或伪分布模式运行 Hadoop。对于这些选项的介绍，可参见附录A。

Hive能和哪些版本的Hadoop共同工作？

每个Hive的发布版本都被设计为能够和多个版本的Hadoop共同工作。一般而 言，Hive支持Hadoop最新发布的稳定版本以及之前的老版本。这些信息列在 发布说明中。只要确保hadoop可执行文件在相应的路径中或设置 HADOOP_HOME环境变量，就不必另行告诉Hive当前正在使用哪个版本的 Hadoop。

下载 Hive 的一个发布版本（/z即.•///?/>.然后把压缩包 解压到工作站上合适的位置：

% tar xzf apache-hive-x.y.z-bin.tar>gz

把Hive放在你自己的路径下以便于访问：

% export HIVE_HOME=^/sw/apache-hive-x.y.z-bin % export PATH=$PATH:$HIVE_HOME/bin

现在，键入hive启动Hive的shell环境：

①假设工作站和Hadoop集群之间已有网络连接。可以在运行Hive之前在本地安装Hadoop，并 通过hadoop fs命令执行一些HDFS操作，测试两者的版本是否相同。

% hive

hive>

###### Hive的shell环境

Hive的shell环境是我们和Hive交互、发出HiveQL命令的主要方式。HiveQL是 Hive的查询语言。它是SQL的一种“方言”。它的设计在很大程度上深受 MySQL的影响。因此，如果熟悉MySQL,你会觉得Hive很亲切。

第一次启动Hive时，我们可以通过列出Hive的表来检查Hive是否正常工作，此 时应该没有任何表。命令必须以分号结束，告诉Hive立即执行该命令：

hive> SHOW TABLES；

OK

Time taken: 0.473 seconds

和SQL类似，HiveQL 一般是大小写不敏感的（除了字符串比较以外），因此show tables;和上面的命令效果相同。制表符（Tab）会自动朴全Hive的关键字和函数。

对干全新安装，这个命令会花几秒钟来执行。因为系统采用“延迟”（lazy）策略， 所以直到此时才在机器上创建metastore数据库。（该数据库把相关文件放在运行 hive命令那个位置下的weZa伽目录中。）

也可以以非交互式模式运行Hive的shell环境。使用-f选项可以运行指定文件中 的命令。在这个示例中，我们运行脚本文件•scr/pQ:

% hive -f script.q

对于较短的脚本，可用-e选项在行内嵌入命令。此时不需要表示结束的分号:

% hive -e ^SELECT * FROM dummyJ

OK

X

Time taken: 1.22 seconds. Fetched: 1 row(s

有一个较小的数据表用于测试查询是很有用的。例如，我们可以用文本数据测 试SELECT表达式中的函数（参见17.5.2节对操作符和函数的讨论）。下面是一

个生成一个单行表的方法:



% echo 'X1 > /tmp/dummy.txt

% hive -e ” CREATE TABLE dummy (value STRING); \ LOAD DATA LOCAL INPATH */tmp/dummy.txtf\ OVERWRITE INTO TABLE dummy”

无论是在交互式还是非交互式模式下，Hive都会把操作运行时的信息打印输出到 标准错误输出(standard error),例如运行一个查询所花的时间。町以在启动程序的 时候使用-S选项强制不显示这些消息，只输出查询结果：

% hive -S -e 'SELECT * FROM dummy*

X

其他比较有用的Hive Shell的特性包括.：使用！前缀来运行宿主操作系统的命令： 使用dfs命令来访问Hadoop文件系统。

##### 17.2示例

让我们看一下如何用Hive查询我们在前面几章使用的气象数据集。第一个步骤是 把数据加载到Hive管理的存储。在这里，我们将让Hive把数据存储在本地文件 系统。稍后我们会介绍如何把表存储到HDFS。

和RDBMS 一样，Hive把数据组织成表。我们使用CREATE TABLE语句为气象数 据新建一个表：

CREATE TABLE records (year STRING, temperature INT, quality INT)

ROW FORMAT DELIMITED

FIELDS TERMINATED BY

第一行声明一个records表，包含三列：year, temperature和quality。还

必须指明每一列的数据类型，在这里，年为字符串类型，另外两列为整数型。

到目前为止，所用的SQL都是我们所熟悉的。但是接下来的ROW FORMAT子句是 HiveQL所特有的。这个子句所声明的是数据文件的每一行是由制表符分隔的文 本。Hive按照这一格式读取数据：每行三个字段，分别对应于表中的三列，字段 间以制表符分隔，每行以换行符分隔。

接下来，可以向Hive输入数据。这里出于探索的目的， 据集：

只用一个很小的样本



LOAD DATA LOCAL INPATH 1input/ncdc/micro-tab/sample.txt1 OVERWRITE INTO TABLE records;

这一命令告诉Hive把指定的本地文件放入其仓库目录中。这只是一个简单的文件 系统操作。这个操作并不解析文件或把它存储为内部数据库格式，因为Hive并不 强制使用任何特定文件格式。文件以原样逐字存储，Hive不会对文件进行修改。

在这个示例中，我们把Hive表存储在本地文件系统中(fs.default.name设为默

认值[file:///）](file:///）。在Hive的仓库目录中，表存储为目录。仓库目录由选项)[。在Hive的仓库目录中，表存储为目录。仓库目录由选项](file:///）。在Hive的仓库目录中，表存储为目录。仓库目录由选项) hive. metastore.warehouse. dir 控帯lj，默i人厂 这样，records表的文件便可以在本地文件系统的目录 中找到：

% Is /user/hive/warehouse/record/ sample.txt

在这个示例中，我们现在只有一个文件sample.txt，但是在一般情况下，可以有多 个文件，而且Hive会在查询表的时候读人所有这些文件。

LOAD DATA语句中的OVERWRITE关键字告诉Hive删除表对应目录中已有的所有 文件。如果省去这一关键字，Hive就简单地把新的文件加入目录（除非目录下正好 有同名的文件，此时将替换掉原有的同名文件）。

:据现在已经在Hive中，



我们可以对它运行一个查询:



hive> SELECT year, MAX(temperature)

\>    FROM records

\>    WHERE temperature != 9999 AND quality IN (0, 1, 4, 5, 9)

\>    GROUP BY year;

1949    111

1950    22

这个SQL查询没什么特别的：它是一个带GROUP BY子句的SELECT语句。这个 查询根据年份对行进行分组，然后使用MAX（）聚集函数在每个年份组中找到最高 气温。Hive的优势在于把这个查询转化为一个作业并为我们执行这个作业，然后 把结果打印输出到控制台。虽然Hive和其他数据库有一些细微的差别，例如Hive 支持的SQL的结构以及可以查询中数据的格式等（我们在本章中将探究差别）但能 够在原始数据上执行SQL查询，才能彰显出Hive的强大功能。

##### 17.3 运行 Hive

这一节介绍运行Hive的一些更实用的技术，包括如何设置Hive使其能运行在 Hadoop集群和共享的metastore上。为此，我们会深入介绍Hive体系结构。

###### 17.3.1 配置 Hive

和Hadoop类似，Hive用XML配置文件进行设置。配置文件为///■/&.，/，它在

Hive的目录下。通过这个文件，可以没置每次运行Hive时希望Hive使用的 选项。该目录下还包括///從-加为凌■/（其中记录Hive使用的选项及其默认值）。

传递--config选项参数给hive命令， hive-site.xml文件的目录：



可以通过这种方式重新定义Hive査找



% hive --config /Users/tom/dev/hive-conf

注意，这个选项指定的是包含配置文件的目录，而不是配置文件hive-site.xml本 身。这对于有（对应于多个集群的）多个站点文件时很有用，可以方便地在这些站点 文件之间进行切换。还有另一种方法，可以设置HIVE_CONFJ）IR环境变量来指定 配置文件目录，效果相同。

hive-site.xml文件最适合存放详细的集群连接信息，因为可以使用Hadoop属性 fs.defaultFS 和 yarn.resourcemanager.address 来指定文件系统和资源管理 器（关于配置Hadoop的详细信息，请参见附录A）。如果没有设定这两个参数，它 们就像在Hadoop中一样，被设为默认值，也就是使用本地文件系统和本地（正在 运行的）“作业运行器” Gob runner）,这对于试着用Hive来处理测试数据集非常方 便。metastore的配置选项（参见17.3.3节对metastore的讨论）一般也能在hive-site.xml 中找到。

Hive还允许向hive命令传递-hiveconf选项来为单个会话(per-session)设置属 性。例如，下面的命令设定在会话中使用一个(伪分布)集群：

% hive -hiveconf fs.defaultFS=hdfs://localhost \

-hiveconf mapreduce.framework.name=yarn \

-hiveconf yarn.resourcemanager•address=localhost:8032

如果准备让多个Hive用户共享一个Hadoop集群，则需要使Hive所用的目录对 所有用户可写。以下命令将创建目录，并设置合适的权限：    .

![img](Hadoop43010757_2cdb48_2d8748-202.jpg)



% hadoop fs -mkdir /tmp % hadoop fs -chmod a+w /tmp

% hadoop fs -mkdir -p /user/hive/warehouse % hadoop fs -chmod a+w /user/hive/warehouse

如果所有用户在同一个用户组中，把仓库目录的权限设为g+w就够了。

还可以在一个会话中使用SET命令更改设置。这对于为某个特定的查询修改Hive 设置非常有用。例如，以下命令确保表的定义中都使用“桶” （bucket）,详情可以 参见17.6.2节：

hive> SET hive.enforce•bucketing=true;

可以用只带属性名的SET命令查看任何属性的当前值：

hive> SET hive.enforce.bucketing; hive.enforce.bucketing=true

不带参数的SET命令会列出Hive所设置的所有属性(及其取值)。注意，这个列表 中不包含Hadoop的默认值，除非这个值用本节中介绍的某个方法重写了。使用 SET-v可以列出系统中的所有属性，包括Hadoop的默认值。

设置属性有一个优先级层次。在下面的列表中，越小的值表示优先级越高。

##### (1)    Hive SET 命令。

(2)    命令行-hiveconf选项。

(3)    hive-sife.xml 禾2 Hadoop 站点文件｛core-site.xml、 hdfs-site.xml , mapredsite.xml、yarn-site.xml)。

(4)    Hive 默认值和 Hadoop 默认文件(core-如faidt.xm/、hdfs-default.xml, mapred-default.xml、yarn-default.xml) 0

本书6.2.2节介绍了可以设置哪些属性，可以从中了解更详细地描述了有关 Hadoop属性的配置问题。

1.执行引擎

Hive的原始设计是以MapReduce作为执行引擎(目前仍然是默认的执行引擎)。目 前，Hive 的执行引擎还包括 Apache Tez ([http://tez.apache.org/)](http://tez.apache.org/),%e5%8f%a6%e5%a4%96)[,另外](http://tez.apache.org/),%e5%8f%a6%e5%a4%96) Hive 对 Spark(参见第19章)的支持也正在开发中。Tez和Spark都是通用有向无环图(DAG) 引擎，它们比MapReduce更加灵活，性能也更优越。例如，在使用MapReduce 时，中间作业的输出会被“物化”(materialize)存储到HDFS上，Tez和Spark则 不同，它们可以根据Hive规划器的请求，把中间结果写到本地磁盘上，甚至在内 存中缓存，以避免额外的复制开销。

具体使用哪种执行引擎由属性hive.execution.engine来控制，其默认值为mr (即MapReduce)。基于每查询的执行引擎的切换操作非常简单，因此我们可以观察 不同引擎对特定査询的不同使用效果。下述语句设置Tez为Hive的执行引擎：

hive> SET hive•execution.engine=tez;

注意，首先必须在Hadoop集群上安装Tez，Hive说明文档中包含了详细的最新安 装的最新步骤。

2.日志记录

可以在本地文件系统的5加中找到Hive的错误 日志。错误日志对于诊断配置问题和其他错误非常有用。Hadoop的MapReduce任 务日志对于调试也常有帮助，相关信息请参见6.5.6节对Hadoop用户日志的讨论。

在很多系统中，Hjava.io.tmpdir}就是々wp，如果不是，或者你希望将日志目 录指定到其他位置，可以使用下述命令：

% hive -hiveconf hive.log.dir='/tmp/${user.name}'

日志的配置存放在conf/hlve-log4j.properties中。可以通过编辑这个文件来修改日 志的级别和其他日志相关设置。但是，更方便的办法是在会话中对日志配置进行设 置。例如，下面的语句可以方便地将调试消息发送到控制台：

% hive -hiveconf hive.root.loggersDEBUG.,console

###### 17.3.2 Hive 服务

Hive的shell环境只是hive命令提供的其中一项服务。我们可以在运行时使用--service选项指明要使用哪种服务。键人hive --service help可以获得可用 服务列表。下面介绍一些最有用的服务。

•    cli Hive的命令行接口（shell环境）。这是默认的服务。

•    hiveserver2让Hive以提供Thrift服务的服务器形式运行，允许用不同 语言编写的客户端进行访问。hiveserver2在支持认证和多用户并发方面 比原始的hiveserver有很大改进。使用Thrift、JDBC和ODBC连接器的 客户端需要运行Hive服务器来和Hive进行通信。通过设置hive, server^.thrift.port配置属性来指明服务器所监听的端口号（默认为 10000）。

•    beeline以嵌入方式工作的Hive命令行接口（类似于常规的CL1），或者 使用JDBC连接到一个HiveServer 2进程。

•    hwi Hive的Web接口。在没有安装任何客户端软件的情况下，这个简 单的Web接口可以代替CLI。另外，Hue是一个功能更全面的Hadoop

Web接口，其中包括运行Hive查询和浏览Hive metastore的应用程序。

•    jar与hadoop jar等价。这是运行类路径中同时包含Hadoop和Hive 类Java应用程序的简便方法。

•    metastore默认情况下，metastore和Hive服务运行在同一个进程里。使用这 个服务，可以让metastore作为一个单独的（远程）进程运行。通过设置 METASTORE_PORT环境变量（或者使用-p命令行选项）可以指定服务器监 听的端口号（默认为9083）。

Hive客户端

如果以服务器方式运行Hive （hive --service hiveserver2），可以在应用程序 中以不同机制连接到服务器。Hive客户端和服务之间的联系如图17-1所示。

Thrift客户端Hive服务器提供Thrift服务的运行，因此任何支持Thrift 的编程语言都可与之交互。有些第三方项目还提供Python和Ruby客户 端。详情可访问Hive的英文维基页面（/?即.•///^.（y/Zz/vejerver）。

• JDBC驱动 Hive提供了 Type 4（纯Java）的JDBC驱动，定义在 org.apache.hadoop.hive.jdbc.HiveDriver 类 中 0 在 以 jdbc:hive2://host:port/dbname 形式配置 JDBC URI 以后，Java 应

用程序可以在指定的主机和端口连接到在另一个进程中运行的Hive服 务器。驱动使用Java的Thrift绑定来调用由Hive Thrift客户端实现的 接口。

你可能还希望通过URI jdbc:hive2://,用JDBC内嵌模式来连接Hive。在这个 模式下，Hive和发出调用的应用程序在同一个JVM中运行。这时不需要以独立服 务器方式运行Hive，这是因为此时应用程序并不使用Thrift服务或Hive的Thrift 客户端。

Beeline CLI使用JDBC驱动与Hive通信。

• ODBC驱动Hive的ODBC驱动允许支持ODBC协议的应用程序（比如 商业情报软件）连接到Hive。Apache Hive的发布版本中没有ODBC驱 动，不过有些厂商会提供一个免费版的ODBC驱动。（和JDBC驱动类 似，ODBC驱动使用Thrift和Hive服务器进行通信。）

Hive services



Hive Storage and Compute



Hive dients



![img](Hadoop43010757_2cdb48_2d8748-204.jpg)



Driver



![img](Hadoop43010757_2cdb48_2d8748-205.jpg)



![img](Hadoop43010757_2cdb48_2d8748-206.jpg)



| Metastore       | .....斗 |
| --------------- | ------- |
|                 |         |
| FileSystem      |         |
|                 |         |
| ExecutionEngine |         |
|                 | I       |

MeUstore

(database.

••瓜



■ ?'



Hadoop

duster



ffl 17-1. Hive体系结构

###### 17.3.3 Metastore

metastore l Hive元数据的集中存放地。metastore包括两部分：服务和后台数据 的存储。默认情况下，metastore服务和Hive服务运行在同一个JVM中，它包含 一个内嵌的以本地磁盘作为存储的Derby数据库实例。这称为内嵌metastore配 置(embedded metastore configuration)，参见图 17-2。

![img](Hadoop43010757_2cdb48_2d8748-208.jpg)



Embedded

metastore



Local

metastore



17-2. Metastore 的配置



Hive Service JVM



Hive Service JVM

|        |      | :‘        |
| ------ | ---- | --------- |
| Driver |      | Metastore |



Hive Service JVM

|        |      |            |
| ------ | ---- | ---------- |
| Driver | …食  | :Metastore |



![img](Hadoop43010757_2cdb48_2d8748-210.jpg)



![img](Hadoop43010757_2cdb48_2d8748-211.jpg)



![img](Hadoop43010757_2cdb48_2d8748-212.jpg)



Remote

metastore



![img](Hadoop43010757_2cdb48_2d8748-214.jpg)



使用内嵌metastore是Hive人门最简单的方法。但是，每次只有一个内嵌Derby 数据库可以访问某个磁盘上的数据库文件，这就意味着一次只能为毎个metastore 打开一个Hive会话。如果要试着启动第二个会话，在它试图连接metastore时， 会得到错误信息。

如果要支持多会话(以及多用户)，需要使用一个独立的数据库。这种配置称为本地 metastore配置(local metastore),因为metastore服务仍然和Hive服务运行在同一

个进程中，但连接的却是在另一个进程中运行的数据库，在同一台机器上或在远 程机器上。任何JDBC兼容的数据库都可以通过表17-1列出的javax.jdo.option.* 配置属性来供metastore使用。①

表17-1.重要的metastore配置属性

| 属性名称hive.metastore.warehouse.dir  | 类型URI       | 默认值    ’/user/hive/warehouse                   | 描述相对于fs. default .name的目录，托管表就存储在这里        |
| ------------------------------------- | ------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| hive.metastore.uris                   | 逗号分隔的URI | 未设定                                            | 如果未设置(默认值)，则使用当前的 metastore,否则连接到由URI列表指定要连接的远程metastore服务器。如果有多个远程服务器，则客 户端便以轮询(round robin)方式连接 |
| javax.jdo.option.ConnectionURL        | URI           | jdbc:derby:;databaseName=metastored b;create=true | metastore 数据库的 JDBC URL                                  |
| javax.jdo>optioneConnectionDriverName | 字符串        | org.apache.derby.jdbc.EmbeddedDriver              | JDBC驱动器的类名                                             |
| javax.jdo.option.ConnectionUserName   | 字符串        | APP                                               | JDBC用户名                                                   |
| javax.jdo.option.ConnectionPassword   | 字符串        | mine                                              | JDBC密码                                                     |

对干独立的metastore, MySQL是一种很受欢迎的选择。此时javax.jdo.option. ConnectionURL 应该设为 jdbc :mysql://host/dbname? createDatabaseIfNotExist= true，而 javax.jdo.option.ConnectionDriverName 贝l股为 com.mysql. jdbc.Driver。 当然，还需要设置用户名和密码。MySQL的JDBC驱动的JAR文件(Connector/J)

①这些属性以javax.jdo作为前缀，因为metastore的实现针对持久化］ava对象使用了 Java Data Objects (JDO) API。它使用 了 JDO 的 DataNucleus 实现。

必须在Hive的类路径中，把这个文件放入Hive的肋目录即可。

更进一步，还有一种metastore配置称为远程metastore配置(remote metastore)。在 这种配置下，一个或多个metastore服务器和Hive服务运行在不同的进程内。这 样一来，数据库层可以完全置于防火墙后，客户端则不需要数据库凭据(用户名和 密码)，从而提供了更好的可管理性和安全。

可以通过把hive.metastore.uris设为metastore服务器URI(如果有多个服务 器，各个URI之间用逗号分隔)，把Hive服务设为使用远程metastore。metastore服务 器URI的形式为thrift://host:port。这里，端口号对应于启动metastore服务器时 所设定的METASTORE_PORT值，详情可以参见17.3.2节对Hive服务的讨论。

##### 17.4 Hive与传统数据库相比

Hive在很多方面和传统数据库类似(例如支持SQL接口)，但是其起初对HDFS和 MapReduce底层的依赖意味着它的体系结构有别于传统数据库，而这些区别又影 响着Hive所支持的特性。不过，随着时间的推移，这些局限性已逐渐消失，导致 的结果就是Hive看上去和用起来都越来越像传统的数据库。

###### 17.4.1读时模式vs.写时模式

在传统数据库里，表的模式是在数据加载时强制确定的。如果在加载时发现数据 不符合模式，则拒绝加载数据。因为数据是在写入数据库时对照模式进行检查， 因此这一设计有时被称为“写时模式”(schema on write)。

在另一方面，Hive对数据的验证并不在加载数据时进行，而在査询时进行，这称 为“读时模式”(schema on read)。

用户需要在这两种方法之间进行权衡。读时模式可以使数据加载非常迅速。这是因为 它不需要读取数据来进行“解析”(parse)，再进行序列化并以数据库内部格式存入 磁盘。数据加载操作仅仅是文件复制或移动。这一方法也更为灵活，试想，针对 不同的分析任务，同一个数据可能会有两个模式。Hive使用“外部表” (external table)时，这种情况是可能发生的，参见17.6.1节对托管表和外部表的讨论。

写时模式有利于提升査询性能。因为数据库可以对列进行索引，并对数据进行压 缩。但是作为权衡，此时加载数据会花更多时间。此外，在很多加载时模式未知

的情况下，因为査询尚未确定， “长袖善舞”的地方。

![img](Hadoop43010757_2cdb48_2d8748-215.jpg)



此不能决定使用何种索引。这些情况正是Hive



关于Hive

48117.4.2更新、事务和索引

更新、事务和索引都是传统数据库最重要的特性。但是，直到最近，Hive也还没 有考虑支持这些特性，因为Hive被设计为用MapReduce操作HDFS数据。在这样 的环境下，“全表扫描”(full-table scan)是常态操作，而表更新则是通过把数据变 换后放入新表实现的。对于在大规模数据集上运行的数据仓库应用，这一方式很 见效。

早前的Hive能够利用INSERT INTO语句为表添加新的数据文件，以实现向现有 表中批量增加新行。自0.14.0版开始，Hive可以对表做一些更细粒度的更新。也 就是说，可以使用INSERT INTO TABLE •. .VALUES插入少量通过SQL计算出来 的值。另外，还可以对表中的行执行UPDATE和DELETE操作。

HDFS不提供就地文件更新，因此，插入、更新和删除操作引起的一切变化都被 保存在一个较小的增量文件中。由metastore在后台运行的MapReduce作业会定期 将这些增量文件合并到“基表”(base table)文件中。上述功能只有在事务(由Hive 的0.13.0版引入)的背景环境下才能发挥作用，因此这些正在使用的表必须启用了 事务，以保证对这些表进行读操作的查询可以看到表的一致性快照。

Hive的0.7.0发布版本还引入了表级(table-level)和分区级(partition-level)的锁。有 了锁，就可以防止一个进程删除正在被另一个进程读取的表。锁由ZooKeeper透 明管理，因此用户不必执行获得和释放锁的操作，但仍然可以通过SHOW LOCKS 语句获取已经获得了哪些锁的信息。默认情况下，并未启用锁的功能。

在某些情况下，Hive的索引能加快査询的速度。对于SELECT * from t WHERE x = a这样的査询，因为只需要扫描表文件的一小部分，因此可以利用在列x上

的索引。目前Hive的索引分成两类:紧凑(compact)索引和位图(bitmap)索引。(索 引的实现被设计为可插拔的，所以为了其他目的而设计的索引实现会陆续出现。)

紧凑索引存储每个值的HDFS块号，而不是存储文件内偏移量。因此存储不会占 用过多的磁盘空间，且对于值被聚簇(clustered)存储于相近行的情况，索引仍然有 效。位图索引使用压缩的位集合(bitset)来高效存储具有某个特殊值的行。这种索 引一般适合于具有较少取值可能(low-cardinality)的列(如性别或国别)。

###### 17.4.3 其他 SQL-on-Hadoop 技术

自从Hive诞生以来，针对Hive的局限性，这些年涌现出许多其他的SQL-on-Hadoop 引擎技术，这方面的先锋之一是Cloudera Impala （[http://impala.io/），](http://impala.io/%ef%bc%89%ef%bc%8c%e5%ae%83%e6%98%af)[它是](http://impala.io/%ef%bc%89%ef%bc%8c%e5%ae%83%e6%98%af) 开源交互式SQL引擎，Impala在性能上要比基于M叩Reduce的Hive高一个数量 级。Impala使用专用的守护进程，这些守护进程运行在集群中的每个数据节点 上。当客户端发起查询时，它首先会联系任意一个运行了 Impala守护进程的节 点，这个节点被当作是该查询的协调（coordinator）节点。协调节点向集群中的其他 Impala守护进程分发工作，并收集结果以形成该查询的完整结果集。Impala使用 Hive的metastore并支持Hive格式和绝大多数的HiveQL结构（再加上SQL-92）,

因此，在实际操作中这两个系统可以直观地相互移植，或者运行在同一个集 群上。

当然，Hive也并非止步不前，随着Cloudera的Impala项目启动，Hortonworks也 发起了 Stinger计划，它通过支持Tez作为执行引擎，再加上矢量化查询引擎等其 他一些改进技术，使Hive在性能上得到很大的提升。

其他几个著名的开源Hive替代技术包括Facebook Presto ([http://prestodb.io/)](http://prestodb.io/)%e3%80%81)[、](http://prestodb.io/)%e3%80%81)Apache Drill (http:"drill.apache.org/)与 Spark SQL {<https://spark.apache.org/sql/)0> Presto和Drill的架构类似于Impala,只不过Drill的目标是SQL:2011,而非 HiveQL。Spark SQL使用Spark作为其底层引擎，并允许在Spark程序中嵌人 SQL查询。

![img](Hadoop43010757_2cdb48_2d8748-216.jpg)



Spark SQL与在Hive中使用Spark执行引擎（参见17.3.1节对执行引擎的介绍） 并不是一回事。基于Spark的Hive能够提供Hive的所有功能，因为它本身隶 属于Hive项目。而另一方面，Spark SQL则是一种新兴的SQL引擎，它只在 某种程度上提供与Hive的兼容。

Apache Phoenix    縦Ae.org/）则采取了另一种完全不同的方式，它提

供的是基于HBase的SQL。通过JDBC驱动实现SQL访问，JDBC驱动将査询转 换为HBase扫描，并利用HBase协同处理器来执行服务器端的聚合。另外，其元 数据也存储在HBase中。

##### 17.5 HiveQL

Hive 的 SQL “方言”称为 HiveQL,它是 SQL-92、MySQL 和 Oracle SQL 语言的

混合体。它对SQL-92标准的支持已经变得越来越完善，并且有希望做得更好。 HiveQL也能支持一些由最新的SQL标准提供的功能，例如SQL:2003的窗口函数 （也称为分析函数）。Hive对SQL-92的有些扩展是受MapReduce启发而来的，如 多表插入（详情可以参见17.6.4节）和TRANSFORM，MAP和REDUCE子句（详情可以参见 17.7.2 节）。

本章并不提供HiveQL的完整介绍，完整参考手册可参见Hive文档 （[http://bit.ly/languagemanual）0](http://bit.ly/languagemanual%ef%bc%890%e6%88%91%e4%bb%ac%e5%8f%aa%e8%81%9a%e7%84%a6%e5%b9%b2%e5%b8%b8%e7%94%a8%e7%89%b9%e6%80%a7%ef%bc%8c%e5%b9%b6%e7%89%b9%e5%88%ab%e5%85%b3%e6%b3%a8%e9%82%a3%e4%ba%9b%e4%b8%8d%e5%90%8c%e4%ba%8e)[我们只聚焦干常用特性，并特别关注那些不同于](http://bit.ly/languagemanual%ef%bc%890%e6%88%91%e4%bb%ac%e5%8f%aa%e8%81%9a%e7%84%a6%e5%b9%b2%e5%b8%b8%e7%94%a8%e7%89%b9%e6%80%a7%ef%bc%8c%e5%b9%b6%e7%89%b9%e5%88%ab%e5%85%b3%e6%b3%a8%e9%82%a3%e4%ba%9b%e4%b8%8d%e5%90%8c%e4%ba%8e) SQL-92或像MySQL这样常用数据库的特性。表17-2给出了 SQL和HiveQL特性 的较高层次的比较。

表17-2. SQL和HiveQL的概要比较

| 性新伶更             | SQLUPDATE, INSERT,DELETE                                     | HiveQLUPDATE, INSERT， DELETE                                | 参考17.6.4 节和 17.4.2 节 |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------- |
| 事务                 | 支持                                                         | 有限支持                                                     |                           |
| 索引                 | 支持                                                         | 支持                                                         |                           |
| 延迟                 | 亚秒级                                                       | 分钟级                                                       |                           |
| 数据类型             | 整数、浮点数、定点数、文本和二进制串、时间                   | 布尔型、整数、浮点数、文本和 二进制串、时间戳、数组、映射、 结构 | 17.5.1 节                 |
| 函数                 | 数百个内置函数                                               | 数百个内置函数                                               | 17.5.2 节                 |
| 多表插入             | 不支持                                                       | 支持                                                         | 17.6.4 节                 |
| CREATETABLE ASSELECT | SQL-92中不支持，但有些数据库支持                             | 支持                                                         | 17.6.4 节                 |
| SELECT               | SQL-92                                                       | SQL-92。支持偏序的SORT BY。 可限制返回行数量的LIMIT          | 17.7 节                   |
| 连接                 | SQL-92支持或变相支持内连接、外连接、半连接、映射 （FROM子句中列出连接表，连接、交叉连接 | 17.7.3 节                                                    |                           |

在WHERE子句中列出连接 条件）

| 特性子查询 | SQL在任何子句中支持“相关”的（correlated）或不相关的 （noncorrelated） | HiveQL    参考只能在FROM、WHERE或 HAVING子句中（不支持非 相关子查询） | •• ••' •:、/.:.，•，   、    .：•. .-v, A/:    ,    . •17.7.4 节 |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 视图       | 可更新（是物化的或非物 化的）                                | 只读（不支持物化视图）                                       | 17.7.5 节                                                    |
| 扩展点     | 用户定义函数                                                 | 用户定义函数                                                 | 17.8 节和 17.7.2 节                                          |
|            | 存储过程                                                     | MapReduce 脚本                                               |                                                              |

###### 17.5.1数据类型

Hive支持原子和复杂数据类型。原子数据类型包括数值型、布尔型、字符串类型 和时间戳类型。复杂数据类型包括数组、映射和结构。Hive的数据类型在表17-3 中列出。注意，列出的是它们在HiveQL中使用的形式而不是它们在表中序列化 存储的格式（参见17.6.3节）。

表17-3. Hive的数据类型

类别

基本

数据

类型



| 类型BOOLEAN | 描述true/false                                         | 文字示例TRUE |
| ----------- | ------------------------------------------------------ | ------------ |
| TINYINT     | 1字节（8位）有符号整数，从-128到127                    | 1Y           |
| SMALLINT    | 2字节（16位）有符号整数，从-32 768到32 767             | IS           |
| INT         | 4穿R32位请網讎，从-2147 483 648到2 147 483 647         | 1            |
| BIGINT      | 8 柿从4223372036854775808 到 9 223 372 036 854 775 807 | IL           |
| FLOAT       | 4字节（32位）单精度浮点数                              | 1.0          |
| DOUBLE      | 8字节（64位）双精度浮点数                              | 1.0          |



| DECIMAL   | 任意精度有符号小数   | 1.0                                             |
| --------- | -------------------- | ----------------------------------------------- |
| STRING    | 无上限可变长度字符串 | •a、"a"                                         |
| VARCHAR   | 可变长度字符串       | • 3 • •• 3 •• a    a                            |
| CHAR      | 固定长度字符串       | • a •    ••    11a j    d                       |
| BINARY    | 字节数组             | 不支持                                          |
| TIMESTAMP | 精度到纳秒的时间戳   | 1325502245000, 2012-01-02 03：W： 05.123456789* |
| DATE      | 曰期                 | 12012-01-021                                    |

别杂据型

# 类复数类



| 类型ARRAY | 描述一组有序字段。字段的类型必须相同                         | 文字示例 array(l,2)x                                    |                  |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------- | ---------------- |
| MAP       | 一组无序的键-值对。键的类型必须是原子mapCa\l/b\2)的；值可以是任何类型。同一个映射的键 的类型必须相同，值的类型也必须相同 |                                                         |                  |
| STRUCT    | 一组命名的字段。字段的类型可以不同                           | struct(,a\l>1.0), struct ('coll*,    "a、1, .coir, 1.0) | named一 > 'col2\ |
| UNION     | 值的数据类型可以是多个被定义的数据类型create^unionCl, 'a\    | 63)                                                     |                  |

中的任意一个，这个值通过一个整数(零索 引)来标记其为联合类型中的哪个数据类型



①    数组、映射和结构的文字形式可以通过函数得到：array(), map()、struct()三个函数都 是Hive的内置函数。

②    列命名为coll、col2、col3等。

1.原子类型

虽然有些Hive的原子数据类型的命名受到MySQL数据类型名称的影响(其中有些 又和SQL-92相同)，但这些数据类型基本对应于Java中的类型。BOOLEAN类型用 于存储真值(true)和假值(false)。有四种有符号整数类型：TINYINT、SMALLINT、 INT以及BIGINT,分别等价于Java的byte、short、int和long原子数据类 型。它们分别为1字节、2字节、4字节和8字节有符号整数。

Hive的浮点数据类型FLOAT和DOUBLE对应于Java的float和double类型，分 别为32位和64位浮点数。

DECIMAL类型用于表示任意精度的小数，类似于Java的BigDecimal类型，常常 被用来表示货币值。DECIMAL的值以整数非标度值的形式保存。精度(precision)指 明非标度值的位数，而标度(scale)就是指小数点右侧的位数。因此DECIMAL(5, 2) 保存的是从-999.99到999.99之间的数。如果标度省略，则其默认值为0,因此 DECIMALS)保存的就是从-99,999到99,999范围之间的整数。如果精度省略，则 其默认值为10，因此DECIMAL等价于UECIMAL(10,0)。精度的最大允许值为 38,而标度值不能超过精度值。

在Hive中，存储文本的数据类型有三种。STRING是一个无最大长度声明的变长 字符串。(理论上其中最多能存储2GB的字符数，但如果真要物化存储那么大的 值，效率肯定很低。Sqoop提供了大对象的支持，详见15.7节对导入大对象的讨

论。）VARCHAR与STRING相似，只不过它需要声明最大长度（允I午长度范围在1 到65355之间），例如VARCHAR（100）。CHAR类型是固定长度的字符串，如有必要 则以空格填充尾部，例如CHAR（100）。当CHAR值被用干字符串比较操作时，忽 略尾部空格。

BINARY数据类型用于存储变长的二进制数据。

TIMESTAMP数据类型存储精度为纳秒的时间戳。Hive提供了在Hive时间戳、 Unix时间戳（从UNIX纪元开始的秒数）、字符串之间进行转换的UDF，这样就使 得常用的日期操作较为容易进行处理。但是timestamp中并未封装时区信息。可以 使用to_utc_timestamp和from_utc_timestamp函数来进行时区转换。

DATE类型保存的日期，包括年、月、日三个组成部分

2.复杂类型

Hive有四种复杂数据类型：ARRAY、MAP、STRUCT和UNION。ARRAY和MAP与 Java中的同名数据类型类似，而STRUCT是一种记录类型，它封装了一个命名的 字段集合。UNION是从几种数据类型中指明选择一种，UNION的值必须与这些数 据类型之一完全匹配。

复杂数据类型允许任意层次的嵌套。复杂数据类型声明必须用尖括号符号指明其 中数据字段的类型。如下所示的表定义有三列，每一列对应一种复杂数据类型：

CREATE TABLE complex (

Cl ARRAY<INT>,

c2 MAP<STRING, INT>,

c3 STRUCT<a:STRING, b:INT, c:DOUBLE>,

c4 UNIONTYPE<STRING, INT>

)；

參

如果把表17-3中“文字示例”列中所示ARRAY、MAP、STRCUT和UNION类型的数 据加载到表中(17.6.3节将要介绍需要什么格式的文件)，则如下的查询展示了每种 类型的字段访问操作：

hive> SELECT cl[0], c2['b'], c3.c, c4 FROM complex;

12 1.0 {1:63}

###### 17.5.2操作与函数

Hive提供了普通SQL操作符，包括：关系操作符（例如等值判断：x=*a*;空值判

断：x IS NULL；模式匹配：x LIKE *a%*），算术操作符（例如加法：x + 1），以 及逻辑操作符（例如逻辑或：x OR y））。这些操作符和MySQL的操作符一样，而 和SQL-92不同：||是逻辑或（OR）操作符，而不是字符串“连接” （concatenation）® 作符。在MySQL和Hive中，字符串连接应该用concat函数。

Hive提供的内置函数太多，以至于这里无法一一列举。这些函数分成几个大类， 包括数学和统计函数、字符串函数、日期函数（用于操作表示日期的字符串）、条件 函数、聚集函数以及处理XML（使用xpath函数）和JSON的函数。

可以在Hive的shell环境中输入SHOW FUNCTIONS以获取函数列表 个特定函数的使用帮助，可以使用DESCRIBE命令：

要想了解某



hive> DESCRIBE FUNCTION length;

length(str | binary) - Returns the length of str or number of bytes in binary data

如果没有你需要的内置函数，那么可以自己动手写，详情参见17.8节。

类型转换

原子数据类型形成了一个Hive函数和操作符表达式进行隐式类型转换的层次。例 如，如果某个表达式要使用INT,那么TINYINT会被转换为INT。但是，Hive不 会进行反向转换，它会返回错误，除非使用CAST操作。

隐式类型转换规则概述如下：任何数值类型都可以隐式地转换为一个范围更广的 类型或者文本类型（STRING、VARCHAR、CHAR）。所有文本类型都可以隐式地转换 为另一种文本类型。可能令人惊讶的是文本类型都能隐式转换为DOUBLE或 DECIMAL。BOOLEAN类型不能转换为其他任何数据类型，同样也不能在表达式中 隐式地转换为任何数据类型。TIMESTAMP和DATE可以被隐式转换为文本类型。

你可以使用CAST操作显式进行数据类型转换。例如，CAST（T AS INT）将把字符 串T转换成整数值1。如果强制类型转换失败，例如执行CASTCX’ AS INT）,表 达式就会返回空值NULL。

①或査阅 Hive 函数参考手册(<http://bit.fy/kjnguagemanuaLudf)o>

##### 17.6 表

Hive的表在逻辑上由存储的数据和描述表中数据形式的相关元数据组成。数据一 般存放在HDFS中，但它也可以放在其他任何Hadoop文件系统中，包括本地文件 系统或S3。Hive把元数据存放在关系型数据库中，而不是放在HDFS中，详情参 见17.3.3节对metastore的讨论。

在这一节中，我们将进一步了解如何创建表格、Hive提供的不同物理存储格式以 及如何导入这些不同格式的数据。

多数据库/模式支持

很多关系型数据库提供了多个“命名空间”(namespace)的支持。这样，用户和应 用就可以被隔离到不同的数据库或模式中。Hive也对此提供了同样的支持，可 用的命令包括 CREATE DATABASE dbname. USE dbname以及 DROP DATABASE dbrazne这样的语句。可以通过dbnazne. tabLena/ne来完全限定某一张表。如果

没有指明数据库，那么所指的是在default数据库中的表。

17.6.1托管表和夕|、音陵

在Hive中创建表时，默认情况下Hive负责管理数据。这意味着Hive把数据移入 它的“仓库目录” (warehouse directory)0另一种选择是创建一个外部表(external table)。这会让Hive到仓库目录以外的位置访问数据。

这两种表的区别表现在LOAD和DROP命令的语义上。我们先来看托管表(managed table)。

加载数据到托管表时，Hive把数据移到仓库目录。例如：

CREATE TABLE managedjtable (dummy STRING);

LOAD DATA INPATH */user/tom/data.txt1 INTO table managedjtable;

把文件 hdfs://user/tom/data.txt 移动到 Hive 的 managed_table 表的仓库目 录中，即 hdfs://user/hive/warehouse/managed_tableo b

①只有源和目标文件在同一个文件系统中移动才会成功。当然，作为特例，如果用了 LOCAL关 键字，Hive会把本地文件系统的数据复制到Hive的仓库目录(即使它们在同一个文件系统 中)。在其他所有情况下，最好把LOAD视为一个移动操作。

由于加载操作就是文件系统中的文件移动或文件重命名，因此它的执行速度很 快。但记住，即使是托管表，Hive也并不检査表H录中的文件是否符合为表所 声明的模式。如果有数据和模式不匹配，只有在査询时才会知道。我们通常要 通过査询为缺失字段返回的空值NULL才知道存在不匹配的行。可以发出一个 简单的SELECT语句来査询表中的若干行数据，从而检査数据是否能够被正确 解析。

如果随后要丢弃一个表，可使用以下语句：

DROP TABLE managed_table;

这个表，包括它的元数据和数据，会被一起删除。在此我们要重复强调，因为最 初的LOAD是一个移动操作，而DROP是一个删除操作，所以数据会彻底消失。这 就是Hive所谓的“托管数据”的含义。

对于外部表而言，这两个操作的结果就不一样了：由你来控制数据的创建和删 除。外部数据的位置需要在创建表的时候指明：

CREATE EXTERNAL TABLE external_table （dummy STRING）

LOCATION '/user/tom/external_table *;

LOAD DATA INPATH ’/user/tom/data•txt’ INTO TABLE externaljtable;

使用EXTERNAL关键字以后，Hive知道数据并不由自己管理，因此不会把数据移 到自己的仓库目录。事实上，在定义时，它甚至不会检查这一外部位置是否存 在。这是一个非常有用的特性，因为这意味着你可以把创建数据推迟到创建表之 后才进行。

丢弃外部表时，Hive不会碰数据，而只会删除元数据。

那么，应该如何选择使用哪种表呢？在多数情况下，这两种方式没有太大的区别 （当然DROP语义除外），因此这只是个人喜好问题。作为一个经验法则，如果所有 处理都由Hive完成，应该使用托管表。但如果要用Hive和其他工具来处理同一 个数据集，应该使用外部表。普遍的用法是把存放在HDFS（由其他进程创建）的初 始数据集用作外部表进行使用，然后用Hive的变换功能把数据移到托管的Hive 表。这一方法反之也成立，外部表（未必在HDFS中）可以用于从Hive导出数据供 其他应用程序使用。

需要使用外部表的另一个原因是你想为同一个数据集关联不同的模式

①也可以用INSERT OVERWRITE DIRECTORY把数据导出到Hadoop文件系统中。

###### 17.6.2分区和桶

Hive把表组织成分区(partition)。这是一种根据分区列(partition column，如日期)的 值对表进行粗略划分的机制。使用分区可以加快数据分片(slice)的查询速度。

表或分区可以进一步分为桶(bucket)。它会为数据提供额外的结构以获得更高效的 查询处理。例如，通过根据用户ID来划分桶，我们可以在所有用户集合的随机样 本上快速计算基于用户的查询。

1.分区

以分区的常用情况为例。考虑日志文件，其中每条记录包含一个时间戳。如果我 们根据日期来对它进行分区，那么同一天的记录就会被存放在同一个分区中。这 样做的优点是：对于限制到某个或某些特定日期的查询，它们的处理可以变得非 常高效。因为它们只需要扫描查询范围内分区中的文件。注意，使用分区并不会 影响大范围查询的执行，我们仍然可以查询跨多个分区的整个数据集。

一个表可以以多个维度来进行分区。例如，在根据日期对日志进行分区以外，我 们可能还要进一步根据国家对每个分区进行子分区(subpartition)，以加速根据地理 位置进行的查询。

分区是在创建表的时候用PARTITIONED BY子句定义的' 该子句需要定义列的列 表。例如，对前面提到的假想的日志文件，我们可能要把表记录定义为由时间戳 和日志行构成：

CREATE TABLE logs (ts BIGINT, line STRING) PARTITIONED BY (dt STRING, country STRING);

在我们把数据加载到分区表的时候，要显式指定分区值:

LOAD DATA LOCAL INPATH •input/hive/partitions/filel INTO TABLE logs

PARTITION (dt=*2001-01-01*, country=,GB>);

在文件系统级别，分区只是表目录下嵌套的子目录。把更多文件加载到logs表以 后，目录结构可能像下面这样：

①在创建表后可以使用ALTER TABLE语句来增加或移除分区。

/user/hive/warehouse/logs

|-dt=2001-01-01/

I    |-country=GB/

I    I    I_filel

I    |    1~File2

I    1-country=US/

I    1~File3

1-dt=2001-01-02/

|-country=GB/

I    1_File4

1-country=US/

|_file5 1~File6

曰志表有两个日期分区:2001-01-01和2001-01-02,分别对应于子目录dt=2001-01-01    dt=2001-01-02,和两个国家分区：GB和US，分别对应于嵌套子目录

##### country=GB和country=US0数据文件则存放在底层目录中。

###### 可以用SHOW PARTITIONS命令让Hive告诉我们表中有哪些分区：

hive> SHOW PARTITIONS logs;

dt=2001-01-01/country=GB

dt=2001-01-01/country=US

dt=2001-01-02/country=GB

dt=2001-01-02/country=US

记住，PARTITIONED BY子句中的列定义是表中正式的列，称为分区列(partition column),但是，数据文件并不包含这些列的值，因为它们源于目录名。

可以在SELECT语句中以通常的方式使用分区列。Hive会对输入进行修剪，从而 只扫描相关的分区。例如：

SELECT ts, dt, line FROM logs

WHERE country?GB*;

将只扫描filel、file2和file4。还要注意，这个查询返回dt分区列的值。这 个值是Hive从目录名中读取的，因为它们在数据文件中并不存在。

2.桶

把表(或分区)组织成桶(bucket)有两个理由。第一个理由是获得更高的查询处理效 率。桶为表加上了额外的结构。Hive在处理有些查询时能够利用这个结构。具体 而言，连接两个在(包含连接列的)相同列上划分了桶的表，可以使用map端连接 (map-side join)高效地实现。

把表划分成桶的第二个理由是使“取样”或者说“采样”（sampling）更髙效。在处 理大规模数据集时，在开发和修改査询的阶段，如果能在数据集的一小部分数据 上试运行査询，会带来很多方便。我们在本节的最后将看看如何高效地进行 取样。

首先，我们来看如何告诉Hive 一个表应该被划分成桶。我们使用CLUSTERED BY 子句来指定划分桶所用的列和要划分的桶的个数：

CREATE TABLE bucketed一users （id INT, name STRING）

CLUSTERED BY （id） INTO 4 BUCKETS;

在这里，我们使用用户ID来确定如何划分桶（Hive对值进行哈希并将结果除以桶 的个数取余数。这样，任何一桶里都会有一个随机的用户集合。

对于map端连接的情况，首先两个表以相同方式划分桶，处理左边表内某个桶的 mapper知道右边表内相匹配的行在对应的桶内，这样，mapper只需要获取那个桶 （这只是右边表内存储数据的一小部分）即可进行连接。这一优化方法并不一定要求 两个表必须具有相同的桶的个数，两个表的桶个数是倍数关系也可以。用HiveQL 对两个划分了桶的表进行连接，更多细节可参见17.7.3节对map连接的讨论。

桶中的数据可以根据一个或多个列另外进行排序。由于这样对每个桶的连接变成 了高效的归并排序（merge-sort｝，因此可以进一步提升map端连接的效率。以下语 法声明一个表使其使用排序桶：

CREATE TABLE bucketed_users (id INT, name STRING) CLUSTERED BY (id) SORTED BY (id ASC) INTO 4 BUCKETS；

我们如何保证表中的数据都划分成桶了呢？把在Hive外生成的数据加载到划分成 桶的表中，当然是可以的。其实让Hive来划分桶更容易。这一操作通常针对已有 的表。

![img](Hadoop43010757_2cdb48_2d8748-217.jpg)



Hive并不检査数据文件中的桶是否和表定义中的桶一致（无论是对于桶的数量 或用于划分桶的列）。如果两者不匹配，在査询时可能会碰到错误或未定义的结 果。因此，建议让Hive来进行划分桶的操作。

有一个没有划分桶的用户表:

hive>

0

2

3

4



SELECT * FROM users;

Nat

Doe

Kay

Ann

要向分桶后的表中填充成员，需要将hive.enforce.bucketing属性设置为 true。这样，Hive就知道用表定义中声明的数量来创建桶，然后使用INSERT命 令即可：

INSERT OVERWRITE TABLE bucketed_users SELECT * FROM users;

物理上，每个桶就是表（或分区）目录里的一个文件。它的文件名并不重要，但是桶 «是按照字典序排列的第《个文件。事实上，桶对应于MapReduce的输出文件分 区：一个作业产生的桶（输出文件）和reduce任务个数相同。我们可以通过查看刚 才创建的bucketed^users表的布局来了解这一情况。运行如下命令：

hive> dfs -Is /user/hive/warehouse/bucketed一users;

将显示有4个新建的文件。文件名如下（文件名由Hive产生）:

000000_0 000001一0 000002一0 000003一0

第一个桶里包括用户ID 0和4, 里即除以桶数（4）以后的余数：

为一个INT的哈希值就是这个整数本身，在这



hive> dfs -cat /user/hive/warehouse/bucketed_users/000000—0; 0Nat 4 Ann

用TABLESAMPLE子句对表进行取样，我们可以获得相同的结果。这个子句会将查 询限定在表的一部分桶内，而不是使用整个表：

hive> SELECT * FROM bucketed_users

\> TABLESAMPLE(BUCKET 1 OUT OF 4 ON id);

4 Ann

0 Nat

桶的个数从1开始计数。因此，前面的查询从4个桶的第一个中获取所有的用 户。对于一个大规模的、均匀分布的数据集，这会返回表中约1/4的数据行。我 们也可以用其他比例对若干个桶进行取样（因为取样并不是一个精确的操作，因此 这个比例不一定是桶数的整数倍）。例如，下面的査询返回一半的桶：

①显示原始文件时，因为分隔字符是一个不能打印的控制字符，因此字段都挤在一起。它所使 用的控制字符将在下一节介绍。

hive> SELECT * FROM bucketed_users

\> TABLESAMPLE(BUCKET 1 OUT OF 2 ON id)；

4 Ann

0 Nat

2 Doe

因为查询只需要读取和TABLESAMPLE子句匹配的桶，所以取样分桶表是非常高效 的操作。如果使用rand()函数对没有划分成桶的表进行取样，即使只需要读取很 小一部分样本，也要扫描整个输入数据集：

hive> SELECT * FROM users

\> TABLESAMPLE(BUCKET 1 OUT OF 4 ON rand())；

2 Doe

###### 17.6.3存储格式

Hive从两个维度对表的存储进行管理，分别是行格式(row format)和文件格式(file format)0行格式指行和一行中的字段如何存储。按照Hive的术语，行格式的定义 由SerDe定义。SerDe是“序列化和反序列化工具”(Serializer-Deserializer)的合成 词。

当作为反序列化工具进行使用时，也就是查询表时，SerDe将把文件中字节形式的 数据行反序列化为Hive内部操作数据行时所使用的对象形式。使用序列化工具 时，也就是执行INSERT或CTAS(参见17.6.4节财，表的SerDe会把Hive的数据 行内部表示形式序列化成字节形式并写到输出文件中。

文件格式指一行中字段容器的格式。最简单的格式是纯文本文件，但是也可以使 用面向行的和面向列的二进制格式。

1.默认存储格式：分隔的文本

如果在创建表时没有用ROW FORMAT或STORED AS子句，那么Hive所使用的默 认格式是分隔的文本，每行(line)存储一个数据行(row)。®

默认的行内分隔符不是制表符，而是ASCII控制码集合中的Control-A(它的 ASCII码为1)。选择Control-A(在文档中有时记作AA)作为分隔符是因为和制表符 相比，它出现在字段文本中的可能性比较小。在Hive中无法对分隔符进行转义， 因此，挑选一个不会在数据字段中用到的字符作为分隔符非常重要。

①默认格式可以通过hive.default.fileformat属性设置。

集合类元素的默认分隔符为字符Control-B。它用于分隔ARRAY或STRUCT或MAP的 键-值对中的元素。默认的映射键(map key)^＞隔符为字符Control-C。它用于分隔MAP的 键和值。表中各行之间用换行符分隔。

![img](Hadoop43010757_2cdb48_2d8748-218.jpg)



前面对分隔符的描述对一般情况下的平面数据结构一即只包含原子数据类型 的复杂数据类型一都是没有问题的。但是，对于嵌套数据类型，这还不够。事 实上，嵌套的层次(level)决定了使用哪种分隔符。

例如，对于数组的数组，外层数组的分隔符如前所述是Control-B字符，但内 层数组则使用分隔符列表中的下一项(Control-C字符)作为分隔符。如果不确定 Hive使用哪个字符作为某个嵌套结构的分隔符，可以运行以下命令：

CREATE TABLE nested AS

SELECT array(array(l, 2), array(3， 4))

FROM dummy;

然后再使用hexdump或类似的命令来査看输出文件的分隔符。

实际上，Hive支持8级分隔符，分别对应于ASCII编码的1，2,……，8。但 是你只能重载其中的前三个。

因此，以下语句：

CREATE TABLE ..

等价于下面显式说明的语句:

CREATE TABLE ...

ROW FORMAT DELIMITED FIELDS TERMINATED BY '001'

COLLECTION ITEMS TERMINATED BY 1\0021 MAP KEYS TERMINATED BY *\003'

LINES TERMINATED BY 'Xn*

STORED AS TEXTFILE;

注意，我们可以使用八进制形式来表示分隔符，例如，001表示Control-A。

4ive在内部使用一个名为LazySimpleSerDe的SerDe来处理这种分隔格式以及 税们在第8章看到的面向行的MapReduce文本输入和输出格式。这里使用前缀 “lazy”的原因是这个SerDe对字段的反序列化是延迟处理的，只有在访问字段时 才进行反序列化。但是，由于文本以冗长的形式进行存放，所以这种存储格式并 不紧凑。比如，一个布尔值事实上是以文本字符串true或false的形式存

放的。

这种简单的格式有很多好处，例如，使用其他工具（包括MapReduce程序或 Streaming）来处理这样的格式非常容易。但是还可以选择一些更紧凑和高效的二进 制SerDe。这方面的问题稍后再讨论。

2.二进制存储格式：顺序文件、Avro数据文件、Parquet文件、RCFile与ORCFile

二进制格式的使用方法非常简单，只需要通过CREATE TABLE语句中的STORED AS子句做相应声明。这里不需要指定ROW FORMAT,因为其格式由底层的二进制 文件格式来控制。

二进制格式可划分为两大类：面向行的格式和面向列的格式。一般来说，面向列 的存储格式对于那些只访问表中一小部分列的查询比较有效。相反，面向行的存 储格式适合同时处理一行中很多列的情况。

12章）和顺序文件（参见

Avro还支持模式演化 使用下述语句可以将表



Hive本身支持两种面向行的格式：Avro数据文件（参见第 5.4.1节），它们都是通用的可分割、可压缩的格式。另外 以及多种编程语言的绑定。在Hive 0.14.0之后的版本中， 存储为Avro格式：

SET hive.exec.compress.output=true; SET avro.output.codec=snappy;

CREATE TABLE ... STORED AS AVRO；

请注意，通过设置相应的属性即可支持表压缩。

##### 类似地，Hive利用CREATE TABLE语句中的STORED AS SEQUENCEFILE子句声

明，将顺序文件作为存储格式。与压缩相关的属性可参考5.2.3节。

##### Hive本身可支持的面向列的格式（参见5.4.3节）包括：Parquet（第13章）、RCFile 和ORCFile。下面这个示例使用CREATE TABLE. . .AS SELECT语句（参见17.6.4 节）来创建一个表的Parquet格式的复本。

CREATE TABLE users^parquet STORED AS PARQUET AS

SELECT * FROM users;

3.使用定制的 SerDe: RegexSerDe

现在让我们看看如何使用定制的SerDe来加载数据。我们将使用一个用户贡献的 SerDe，它采用一个正则表达式从一个文本文件中读取定长的观测站元数据：

CREATE TABLE stations （usaf STRING, wban STRING, name STRING）

ROW FORMAT SERDE 1org.apache.hadoop.hive.contrib.serde2.RegexSerDe WITH SERDEPROPERTIES (

-input.regex- = "(\\d{6}) (\\d{5}) (.{29}) •*"

)；

在前面的示例中，我们在ROW FORMAT子句中使用DELIMITED关键字来说明文本

是如何分隔的。在这个示例中，我们用另一种方式，用SERDE关键字和实现 SerDe 的 Java 类的完整类名(即 org. apache, hadoop. hive, cont rib. serde2. RegexSerDe),来指明使用哪个SerDe。

SerDe可以用WITH SERDEPROPERTIES子句来设置额外的属性。在这里，我们 要设置RegexSerDe特有的input.regex属性。

input.regex是在反序列化期间将要使用的正则表达式模式，用来将数据行(row) 中的部分文本转化为列的集合。正则表达式匹配时使用Java的正则表达式语法 ([http://bit.fy/java_regex)0](http://bit.fy/java_regex)0%e9%80%9a%e8%bf%87%e8%af%86%e5%88%ab%e4%b8%80%e7%bb%84%e4%b8%80%e7%bb%84%e7%9a%84%e6%8b%ac%e5%8f%b7%e6%9d%a5%e7%a1%ae%e5%ae%9a%e5%88%97(%e7%a7%b0%e4%b8%ba%e6%8d%95%e8%8e%b7%e7%bb%84%ef%bc%8c%e5%8d%b3capturing)[通过识别一组一组的括号来确定列(称为捕获组，即capturing](http://bit.fy/java_regex)0%e9%80%9a%e8%bf%87%e8%af%86%e5%88%ab%e4%b8%80%e7%bb%84%e4%b8%80%e7%bb%84%e7%9a%84%e6%8b%ac%e5%8f%b7%e6%9d%a5%e7%a1%ae%e5%ae%9a%e5%88%97(%e7%a7%b0%e4%b8%ba%e6%8d%95%e8%8e%b7%e7%bb%84%ef%bc%8c%e5%8d%b3capturing) group)。$在这个示例中，有三个捕获组：usaf(六位数的标识符)、wban(五位数的标 识符)以及name(29个字符的定长列)。

我们像以前一样，用如下LOAD DATA语句向表中输入数据:

LOAD DATA LOCAL INPATH ”input/ncdc/metadata/stations-fixed-width•txt” INTO TABLE stations;

回想一下，LOAD DATA把文件复制或移动到Hive的仓库目录中。在这里，由于源 在本地文件系统中，所以使用的是复制操作。加载操作并不使用表的SerDe。

从文件中检索数据时，如下面这个简单查询所示，反序列化会调用SerDe,从而使 每一行的字段都能被正确解析出来：

hive> SELECT * FROM stations LIMIT 4;

| 10000  | 99999 | BOGUS NORWAY |
| ------ | ----- | ------------ |
| 010003 | 99999 | BOGUS NORWAY |
| 010010 | 99999 | DAN MAYEN    |
| 010013 | 99999 | ROST         |

从上面的示例中可以看出RegexSerDe能够帮助Hive获取数据，但是它的效率很

①有时，可能要在正则表达式中使用括号，而不希望这些括号被当作捕获所用的符号。例如， 模式(ab)+可用于匹配一个或多个连续的ab字符串。这时的解决办法是在左括号后加问号?表 示“非捕获组” (noncapturing group)。有多种表示非捕获组的构造结构(参见Java文档)，但在 这个示例中，我们可以用(?ab)+来避免某个文本序列被捕获。

低，因此一般不用于通用存储格式，应当考虑把数据复制为二进制存储格式。

4.存储句柄

##### 存储句柄（Storage handler）用于Hive自身无法访问的存储系统，比如HBase。存储 句柄使用STORED BY子句指定。它代替了 ROW FORMAT和STORED AS子句。有

关HBase集成的详细信息可以参考Hive的英文维基页面

###### 17.6.4导入数据

我们已经见过如何使用LOAD DATA操作，通过把文件复制或移到表的目录中，从 而把数据导入Hive的表（或分区）。也可以用INSERT语句把数据从一个Hive表填 充到另一个，或在新建表的时候使用CTAS结构，CL4S是CREATE TABLE...AS

##### SELECT的缩写。

如果想把数据从一个关系型数据库直接导入Hive,可以看一下Sqoop。详情参见

##### 15.6.1 节。

攀

\1. INSERT 语句

下面是INSERT语句的一个示例:

INSERT OVERWRITE TABLE target SELECT coll, col2

FROM source;

对于分区的表，可以使用PARTITION子句来指明数据要插入哪个分区:

INSERT OVERWRITE TABLE target PARTITION （dt=，2001-01-0V）

SELECT coll, col2

FROM source;

OVERWRITE关键字意味着目标表（对于前面的第一个例子）或2001-01-01分区（对 于第二个例子）中的内容会被SELECT语句的结果替换掉。如果要向已经填充了内 容的非分区表或分区添加记录，那么可以使用INSERT INTO TABLE。

可以在SELECT语句中通过使用分区值来动态指明分区：

INSERT OVERWRITE TABLE target PARTITION (dt)

SELECT coll, col2, dt

FROM source;

##### 这种方法称为动态分区插入(dynamic-partition insert)。

I ’ Hive从0.14.0版开始允许使用INSERT INTO TABLE.. .VALUES语句来插入一小撮 以文字形式指明的记录。

2.多表插入

_UL»



在HiveQL中，可以把INSERT语句倒过来，把FROM子句放在fe刖 果是一样的：

查询的效



FROM source

INSERT OVERWRITE TABLE target SELECT coll, col2;

可以在同一个查询中使用多个insert子句。此时，这样的语法会让查询的含义 更清楚。这种多表插入(multitable insert)方法比使用多个单独的INSERT语句效率 更高，因为只需要扫描一遍源表就可以生成多个不相交的输出。

下面的例子根据气象数据集来计算多种不同的统计数据:

FROM records2

INSERT OVERWRITE TABLE stationsJ?y_year SELECT year, COUNT(DISTINCT station)

GROUP BY year

INSERT OVERWRITE TABLE records_by__year SELECT year, COUNT(l)

GROUP BY year

INSERT OVERWRITE TABLE good^records^by^year SELECT year, COUNT(l)

WHERE temperature != 9999 AND quality IN (0, 1, 4, 5, 9)

GROUP BY year;

这里只有一个源表(records2)，但有三个表用于存放针对这个源表的三个不同査 询所产生的结果。

\3. CREATE TABLE...AS SELECT 语句

把Hive查询的输出结果存放到一个新的表往往非常方便，这可能是因为输出结果 太多，不适宜于显示在控制台上或基于输出结果还有其他后续处理。

新表的列的定义是从SELECT子句所检索的列导出的。在下面的查询中，target 表有两列，分别名为coll和col2,它们的数据类型和源表中对应的列相同：

CREATE TABLE target AS

SELECT coll, col2 FROM source;



CTAS操作是原子的， 表的。



此，如果SELECT查询由于某种原因失败，是不会创建新

###### 17.6.5表的修改

由于Hive使用读时模式（schema on read）,所以在创建表以后，它可以非常灵活地 支持对表定义的修改。但一般需要警惕，在很多情况下，要由你来确保修改数据 以符合新的结构。

可以使用ALTER TABLE语句来重命名表：

ALTER TABLE source RENAME TO target;

在更新表的元数据以外，ALTER table语句还把表目录移到新名称所对应的目录 下。在这个示例中，/user/hive/warehouse/source 被重命名为 /user/hive/ warehouse/target。对于外部表，这个操作只更新元数据，而不会移动目录。

Hive允许修改列的定义，添加新的列，甚至用一组新的列替换表内已有的列。

例如，考虑添加一个新列:

ALTER TABLE target ADD COLUMNS (col3 STRING);

新的列col3添加在已有（非分区）列的后面。数据文件并没有被更新，因此原来的 查询会为col3的所有值返回空值null（当然，除非文件中原来就已经有额外的字 段）。因为Hive并不允许更新已有的记录，所以需要使用其他机制来更新底层的 文件。为此，更常用的做法是创建一个定义了新列的新表，然后使用SELECT语 句把数据填充进去。

如果我们设想原来的数据类型可以用新的数据类型来进行解释，那么修改一个表 的元数据（如列名或数据类型）就变得更为直观。

要想更进一步了解如何修改表的结构，包括添加或丢弃分区、修改和替换列，修 改表和SerDe的属性，可访问Hive的英文维基页面（/z即.•/Wz.fyA/aZaje/jflwg）。

500 第17章

###### 17.6.6表的丢弃

DROP TABLE语句用于删除表的数据和元数据。如果是外部表，就只删除元数据， 数据不会受到影响。

如果要删除表内的所有数据，但要保留表的定义，可使用TRUNCATE TABLE语 句。例如：

TRUNCATE TABLE my^table；

上述语句对外部表不起作用，这种情况需要在Hive的shell环境中使用dfs -rmr 命令来直接删除外部表目录。

另一种达到类似目的的方法是使用LIKE关键字创建一个与第一个表模式相同的新 表：

CREATE TABLE newjtable LIKE existing_table;

##### 17.7查询数据

这一节讨论如何使用SELECT语句的各种形式从Hive中检索数据

###### 17.7.1排序和聚集

在Hive中可以使用标准的ORDER BY子句对数据进行排序。ORDER BY将对输入 执行并行全排序（类似8.2.3节中描述的全排序）。在很多情况下，并不需要结果是 全局排序的。此时，可以换用Hive的非标准的扩展SORT BY。SORT BY为每个 reducer产生一个排序文件。

在有些情况下，你需要控制某个特定行应该到哪个reducer,其目的通常是为了进 行后续的聚集操作。这就是Hive的DISTRIBUTE BY子句所做的事情。下面的例

子根据年份和气温对气象数据集进行排序，以确保所有具有相同年份的行最终都 在同一个reducer分区中：

hive> FROM records2

\>    SELECT yeai% temperature

\>    DISTRIBUTE BY year

①这是在Hive中对9.2.4节所讨论内容的另一种实现。

\> SORT BY year ASC, temperature DESC;

| 1949 | 111  |
| ---- | ---- |
| 1949 | 78   |
| 1950 | 22   |
| 1950 | 0    |
| 1950 | •11  |

后续的查询（或把这个查询作为内嵌子查询的某个查询，详情参见17.7.4节）可以利 用在同一文件中已经分好组并（以降序）排好序的年份气温。

###### 如果SORT BY和DISTRIBUTE BY中所用的列相同，可以缩写为CLUSTER BY以 便同时指定两者所用的列。

###### 17.7.2 MapReduce 脚本

##### 和 Hadoop Streaming 类似，TRANSFORM、MAP 和 REDUCE 子句可以在 Hive 中调用

外部脚本或程序。假设我们像范例17-1那样，用一个脚本来过滤不符合某个条件 的行，即删除低质量的气温读数。

范例17-1.过滤低质量气象记录的Python脚本

\#!/usr/bin/env python

import re

import sys

for line in sys.stdin:

(year, temp, q) = line.strip().split() if (temp != "9999" and re.match("[01459]", q)):

print ”％s\t%s" % (year, temp)

我们可以这样使用这个脚本：

hive> ADD FILE /Users/tom/book-workspace/hadoop-book/chl7-hive/ src/main/python/is_good一quality.py; hive> FROM records2

\>    SELECT TRANSFORM(year, temperature, quality)

\>    USING 'is_good_quality.py'

\>    AS year, temperature;

1950 0

1950 22 1950-11 1949 111 1949 78

在运行查询之前，我们需要在Hive中注册脚本。通过这一操作，Hive知道需要把 脚本文件传输到Hadoop集群上(参见9.4.2节对分布式缓存的讨论)。

查询本身把year, temperature和quality这些字段以制表符分隔的行的形式 流式传递给脚本is_good_quality.py,并把制表符分隔的输出解析为year和 temperature字段，最终形成查询的输出。

这一示例并不使用reducer。如果要用查询的嵌套形式，我们可以指定map和 reduce函数。这一次我们用MAP和REDUCE关键字。但在这两个地方用SELECT TRANSFORM也能达到同样的效果。max_temperature__reduce.py脚本的内容参 见范例2-10:

FROM (

FROM records2

MAP year, temperature, quality USING 'is_good_quality.py *

AS year, temperature) map_output

REDUCE year, temperature

USING 1 max一temperature一reduce.py1

AS year, temperature;

###### 17.7.3连接

和直接使用MapReduce相比，使用Hive的一个好处在于Hive简化了常用操作。 对比在MapReduce中实现连接(join)要做的事情(参见9.3节)，在Hive中进行连接 操作就能充分体现这个好处。

1.内连接

内连接是最简单的一种连接。输入表之间的每次匹配都会在输出表里生成一行。 It我们来考虑两个演示用的小表：sales列出了人名及其所购商品的ID，things 列出商品的ID和名称：

hive> SELECT * FROM sales;

]oe

2

4

0

3

2



Hank

Ali

Eve

Hank

hive> SELECT

2    Tie 4 Coat

3    Hat

1 Scarf



FROM things;



我们可以像下面这样对两个表进行内连接:

hive> SELECT sales.*, things.*

\> FROM sales JOIN things ON (sales.id = things.id);

| Doe  | 2    | 2    | Tie  |
| ---- | ---- | ---- | ---- |
| Hank | 4    | 4    | Coat |
| Eve  | 3    | 3    | Hat  |
| Hank | 2    | 2    | Tie  |

FROM子句中的表sales和JOIN子句中的表things用ON子句中的谓词进行连 接。Hive只支持等值连接(equijoin)，这意味着在连接谓词中只能使用等号。在这 个示例中，等值条件是两个表的id列必须相同。

在Hive中，可以在连接谓词中使用AND关键字分隔的一系列表达式来连接多个 列。还可以在査询中使用多个］OIN...ON...子句来连接多个表。Hive会智能地以最 少MapReduce作业数来执行连接。

![img](Hadoop43010757_2cdb48_2d8748-219.jpg)



Hive与MySQL和Oracle —样，允许在SELECT语句的FROM子句中列出要连 接的表，而在WHERE子句中指定连接条件。例如，下面的语句是我们刚才举例的 查询的另一种表示方法：：

SELECT sales.*, things.*

FROM sales, things

WHERE sales.id = things.id;

单个的连接用一个MapReduce作业实现。但是，如果多个连接的连接条件中使用 了相同的列，那么平均每个连接可以用少于一个MapReduce作业来实现。⑴你可 以在查询前使用EXPLAIN关键字来查看Hive将为某个查询使用多少个 MapReduce 作业：

EXPLAIN

SELECT sales.things.*

FROM sales JOIN things ON (sales.id = things.id);

EXPLAIN的输出中有很多查询执行计划的详细信息，包括抽象语法树、Hive执行 各阶段之间的依赖图以及每个阶段的信息。一个阶段可能是像MapReduce作业文件移动 这样的一个操作。如果要査看更详细的信息，可以在査询前使用EXPLAIN EXTENDED。

Hive目前使用基于规则的查询优化器来确定查询是如何执行的。但自Hive 0.14.0 开始，Hive也可以使用基于代价的优化器。

①JOIN子句中表的顺序很重要：一般最好将最大的表放在最后。详细的信息，包括如何为Hive 的査询规划器给出提示，可访问Hive的英文维基页面。

2.外连接

外连接可以让你找到连接表中不能匹配的数据行。在前面的示例里，我们在进行 内连接时，Ali那一行没有出现在输出中。因为她所购商品的ID没有在things 表中出现。如果我们把连接的类型改为LEFT OUTER ]OIN，即使左侧表(sales) 中的有些行无法与所要连接的表(things)中的任何数据行对应，查询还是会返回 这个表中的每一个数据行：

hive> SELECT sales.*, things.*

\>    FROM sales LEFT OUTER JOIN things ON (sales•id = things.id);

Doe 2 2 Tie Hank 4 4 Coat Ali 0 NULL NULL Eve 3 3 Hat Hank 2 2 Tie

注意，此时返回了 Ali所在的数据行，但因为这一行无匹配，所以things表的对 应列为空值NULL。

Hive也支持右外连接(right outer join),即和左连接相比，交换两个表的角色。在 这里，things表中的所有商品，即使没有任何人购买它们(scarf)，也都会被返 回：

hive> SELECT sales.*, things.*

\>    FROM sales RIGHT OUTER JOIN things ON (sales.id = things.id);

Doe 2 2 Tie Hank 2 2 Tie Hank 4 4 Coat Eve 3 3 Hat NULL NULL 1 Scarf

最后，还有一种全外连接(full outer join),即两个连接表中的所有行在输出中都有 对应的行：

hive> SELECT sales.% things.*

\>    FROM sales FULL OUTER JOIN things ON (sales.id = things.id);

| Ali  | 0    | NULL | NULL  |
| ---- | ---- | ---- | ----- |
| NULL | NULL | 1    | Scarf |
| Doe  | 2    | 2    | Tie   |
| Hank | 2    | 2    | Tie   |
| Eve  | 3    | 3    | Hat   |
| Hank | 4    | 4    | Coat  |

3.半连接

我们来考虑如下IN子査询，它能够査找things表中在sales表中出现过的所有

##### 尚品:

SELECT *

FROM things

WHERE things.id IN (SELECT id from sales);

我们可以像下面这样重写这个查询:

hive> SELECT *

\> FROM things LEFT SEMI JOIN sales ON (sales.id = things.id);

2    Tie 4 Coat

3    Hat

写LEFT SEMI JOIN查询时必须遵循一个限制：右表(sales)只能在ON子句中出 现。例如，我们不能在SELECT表达式中引用右表。

\4. map连接

回顾最初的内连接示例：

SELECT sales.things.*

FROM sales JOIN things ON (sales.id = things.id);

如果有一个连接表小到足以放入内存，例如示例中的things, Hive就可以把较小 的表放入每个mapper的内存来执行连接操作，这就称为map连接。

执行这个查询不使用reducer,因此这个查询对RIGHT或FULL OUTER ]0IN无 效，因为只有在对所有输入上进行聚集的步骤（即reduce）才能检测到哪个数据行无 法匹配。

map连接可以利用分桶的表（参见17.6.2节），因为作用于左侧表的桶的mapper加 载右侧表中对应的桶即可执行连接。这时使用的语法和前面提到的在内存中进行连 接是一样的，只不过还需要用下面的语法启用优化选项：

SET hive.optimize.bucketmapjoin=true;

###### 17.7.4 子查询

子查询是内嵌在另一个SQL语句中的SELECT语句。Hive对子查询的支持很有 限，它只允许子查询出现在SELECT语句的FROM子句中，或某些特殊情况下的 WHERE子句中。

•T Hive允许不相关子查询，即子查询是在WHERE子句中通过IN或EXISTS引用的 ■k 自包含的査询。而相关子查询(即由外部査询引用的子查询)目前还不支持。

下面的查询可以找到每年每个气象站最高气温的均值:

SELECT station, year, AVG(max—temperature)

FROM (

SELECT station, year, MAX(temperature) AS max一temperature FROM records2

WHERE temperature •= 9999 AND quality IN (0， 1， 4, 5， 9) GROUP BY station, year )mt

GROUP BY station, year;

这里FROM中的子查询用于计算每个气象站/日期组合中的最高气温，然后外层査 询使用AVG聚集函数计算这些最高读数的均值。

外层査询像访问表那样访问子查询的结果，所以我们必须为子查询赋予一个别名 (mt)的原因。子查询中的列必须有唯一的名称，以便外层查询可以引用这些列。

###### 17.7.5 视

视图是一种用SELECT语句定义的虚表(virtual table)。视图可以用来以一种不同于 磁盘实际存储的形式把数据呈现给用户。现有表中的数据常常需要以一种特殊的 方式进行简化和聚集以便于后期处理。视图也可以用来限制用户，使其只能访问 被授权可以看到的表的子集。

在Hive中，创建视图时并不把视图物化存储到磁盘上。相反，视图的SELECT语 句只是在执行引用视图的语句时才执行。如果一个视图要对基表进行大规模的变 换，或视图的查询会频繁执行，你可以选择新建一个表，并把视图的内容存储到 新表中，以此来手工物化它，可以参见17.6.4节对CREATE TABLE...AS SELECT语 句的讨论。

我们可以用视图重写前一节中的查询，它用于查找每年各个气象站气温最大值的 均值。首先，让我们为有效记录(即有特定quality值的记录)创建一个视图：

CREATE VIEW valid_records

AS

SELECT *

FROM records2

WHERE temperature != 9999 AND quality IN (0， 1， 4， 5， 9);

创建视图时并不执行査询，查询只是存储在metastore中。SHOW TABLES命令的输 出结果里包括视图。可以使用DESCRIBE EXTENDED vieM_name命令来查看某个 视图的详细信息，包括用于定义它的那个査询。

接下来，让我们为每个观测站每年的最高气温创建第二个视图。这个视图基于

valid_record 视图：

CREATE VIEW maxjtemperatures (station, year, max一temperature)

AS

SELECT station， year, MAX(temperature) FROM valid一records GROUP BY station, year;

在这个视图定义中，我们显式地列出了列的名称。我们这么做是因为最高气温列 是一个聚集表达式，如果不指明，Hive会自己创建一个别名（例如_c2）。我们也可 以在SELECT语句中使用AS子句来为列命名。

有了这两个视图，现在我们就可以执行査询了:

SELECT station, year, AVG（max_temperature）

FROM max—temperatures GROUP BY station, year;

这个査询的结果和前面使用子査询的査询是一样的。特别地，Hive为它们所创建 的MapReduce作业的个数也是一样的：都是两个，每个GROUP BY使用一个。从 这个例子可以看到，Hive可以把使用视图的查询组织成一系列作业，效果与不使 用视图的査询一样。换句话说，即使在执行时，Hive也不会在不必要的情况下物 化视图。

Hive中的视图是只读的，所以无法通过视图为基表加载或插入数据。

##### 17.8用户定义函数

你要写的查询有时无法轻松（或根本不能）使用Hive提供的内置函数来表示。通过

编写用户定义函数（user-defined function, UDF）, Hive可以方便地插入用户写的处

理代码并在查询中调用它们。

UDF必须用Java语言编写。Hive本身也是用Java写的。对于其他编程语言，可 以考虑使用SELECT TRANSFORM查询，有了它，可以让数据流经用户定义的脚本 （参见17.7.2节）。

Hive 中有三种 UDF：（普通）UDF、用户定义聚集函数（user-defined aggregate function, UDAF）以及用户定义表生成函数（user-defined table-generating function, UDTF）。它 们所接受的输入和产生的输出的数据行的数量不同。

•    UDF操作作用于单个数据行，且产生一个数据行作为输出。大多数函数 （例如数学函数和字符串函数）都属于这一类。

•    UDAF接受多个输入数据行，并产生一个输出数据行。像COUNT和MAX 这样的函数都是聚集函数。

•    UDTF操作作用于单个数据行，且产生多个数据行（即一个表）作为输出。

和其他两种类型相比，表生成函数的知名度较低。所以让我们来看一个示例。考 虑这样二个表，它只有一列X，包含的是字符串数组。回头看看表的定义和填充 方式是很有启发的：

CREATE TABLE arrays (x ARRAY<STRING>) ROW FORMAT DELIMITED

FIELDS TERMINATED BY *\001*

COLLECTION ITEMS TERMINATED BY *\0021;

注意，ROW FORMAT子句指定数组中的项用Control-B字符分隔。我们要加载的示 例文件内容如下，为了显示方便，用AB表示Control-B字符：

aABb

cABdABe

在运行LOAD DATA命令以后，



可以通过下面的查询确认数据已正确加载:



hive> SELECT ♦ FROM arrays;

![img](Hadoop43010757_2cdb48_2d8748-220.jpg)



接下来，我们可以使用explode UDTF对表进行变换。这个函数为数组中的每一

项输出一行



##### “平面化”（flattened）成五行:



此，在这里，输出的列y的数据类型为STRING。其结果是，表被



hive> SELECT explode(x) AS y FROM arrays;

a

b

c

d

e

带UDTF的SELECT语句在使用时有一些限制（例如，它们不能检索额外的列表达

式)，使实际使用时这些语句的用处并不大。为此，Hive支持LATERAL VIEW査 询。这一语句的功能更强大。这里不介绍LATERAL VIEW查询。相关详情可以访 问 Hive 的英文维基页面(/zZZp.7/to./y/toera/_Wew)。

###### 17.8.1 写 UDF

为了演示如何写和使用UDF，我们将写一个简单的剪除字符串尾字符的UDF。 Hive已经有一个内置的名为trim的函数，所以我们把自己的函数称为strip。 Strip Java类的代码如范例17-2所示。

范例17-2.剪除字符串尾字符的UDF package com.hadoopbook.hive;

import org.apache.commons.lang.StringUtils; import org.apache.hadoop.hive.ql.exec.UDF; import org.apache.hadoop.io•Text;

public class Strip extends UDF { private Text result = new Text();

public Text evaluate(Text str) { if (str == null) {

return null;

result.set(StringUtils•strip(str.toString())); return result;

}

public Text evaluate(Text str, String stripChars) { if (str == null) {

return null;

}

result.set(StringUtils.strip(str.toString(), stripChars)); return result;

}

}

一个UDF必须满足下面两个条件：

•    一个UDF 必须是 org.apache.hadoop.hive• ql• exec.UDF 的子类

•    一个UDF必须至少实现了 evaluate()方法

evaluate()方法不是由接口定义的，因为它可接受的参数的个数、它们的数据类 型及其返回值的数据类型都是不确定的。Hive会检查UDF,看能否找到和函数调 用相匹配的evaluate()方法。

这个Strip类有两个evaluate()方法。第一个方法去除输入的前导和结束的空

白字符，而第二个方法则去除字符串尾出现在指定字符集合中的任何字符。实际 的字符处理工作交由Apache Commons项目里的StningUtils类来完成。所以代 码中唯一值得提的是对Hadoop Writable库中Text的使用。实际上，Hive支持在 UDF中使用Java的基本类型(以及其他一些像java.util.List和 java.util.Map这样的类型)，所以，下面这样函数签名(signature)的效果是一样 的：

public String evaluate(String str)

但是，通过使用Text,我们可以利用对象重用的优势，增效节支 使用这种方法。

此一般推荐



为了在Hive中使用UDF,我们需要把编译后的Java类打包成一个JAR文件(可以 用本书所附代码输入mvn package来完成)，接下来，在metastore中注册这个函 数并使用CREATE FUNCTION语句为它起名：

CREATE FUNCTION strip AS •com.hadoopbook.hive.Strip USING ]AR '/path/to/hive-examples.jar1;

本地使用Hive只需要一个本地文件路径就足够了，但是在集群上，则应当将JAR 文件复制到HDFS中，并在USING ]AR子句中使用HDFS的URI。

现在，可以像使用内置函数一样使用UDF:

hive> SELECT strip(• bee •) FROM dummy; bee

hive> SELECT strip(•banana1, 'ab1) FROM dummy; nan

注意，UDF名对大小写不敏感：

hive> SELECT STRIP。 bee •) FROM dummy; bee

如果想要删除这个函数，可使用DROP FUNCTION语句：

DROP FUNCTION strip;

利用TEMPORARY关键字可以创建一个仅在Hive会话期间有效的函数，也就是说 这个函数并没有在metastore中持久化存储。

ADD 〕AR /path/to/hive-examples.jar;

CREATE TEMPORARY FUNCTION strip AS 'com.hadoopbook•hive.Strip';

使用临时函数时，最好在主目录中创建一个./n’verc文件，以包含定义这些UDF的 命令，而这个文件会在每个Hive会话开始时自动运行。

要想在开始时调用add ]AR，还可以在Hive启动时指定査找附加JAR文件的 路径，这个路径会被加入Hive的类路径（也包括任务的类路径）。这种技术对干 敏新Hive时自动肋你的UDF库题有醐。

有两种指明路径的办法：在/n’ve命令后传递--auxpath选项：

% hive --auxpath /path/to/hive-examples.jar

或在运行Hive前设置HIVE_AUX_]ARS_PATH环境变量。附加路径可以是一个 用逗号分隔的JAR文件路径列表或包含JAR文件的目录。

###### 17.8.2 写 UDAF

聚集函数比普通的UDF难写。因为值是在块内进行聚集的（这些块可能分布在很 多任务中），从而实现时要能够把部分的聚集值组合成最终结果。实现此功能的代 码最好用示例来进行解释。让我们来看一个简单的UDAF的实现，它用于计算一 组整数的最大值（范例17-3）。

范例17-3.计算一组整数中最大值的UDAF

package com.hadoopbook.hive;

import org.apache.hadoop.hive.ql.exec.UDAF; import org.apache.hadoop.hive.ql•exec.UDAFEvaluator; import org•apache•hadoop.io.IntWritable;

public class Maximum extends UDAF {

public static class MaximumlntUDAFEvaluator implements UDAFEvaluator {

private IntWritable result;

public void init() { result = null;

}

public boolean iterate(IntWritable value) {

if (value == null) { return true;

}

if (result == null) { result = new IntWritable(value.get());

} else {

result.set(Math.max(result.get()， value.get()));

}

return true;

public IntWritable terminatePartial() { return result;

}

public boolean merge(IntWritable other) { return iterate(other);

}

public IntWritable terminate { return result;

}

这个类的结构和UDF的稍有不同。UDAF必须是org.apache.hadoop. hive.ql.exec.UDAF(注意UDAF中的“A”)的子类，且包含一个或多个嵌套 的、实现了 org. apache. hadoop. hive. ql. UDAF Evaluator 的静态类。在这个 示例中，只有一个嵌套类，MaximumIntUDAFEvaluatoro但是我们也可以添加更 多的计算函数(如 MaximumLongUDAFEvaluator 和 MaximumFloatUDAFEvaluator)来 提供计算长整型、浮点型等类型数最大值的UDAF的重载。

一个计算函数必须实现下面5个方法

• init()方法    init()方法负责初始化计算函数并重设它的内部状态。在

MaximumlntUDAFEvaluator中，我们把存放最终结果的IntWritable对象 设为null。我们使用null来表示目前还没有对任何值进行聚集计算， 这和对空集NULL计算最大值应有的结果是一致的。

• iterate()方法每次对一个新值进行聚集计算时都会调用iterate()方法。计 算函数要根据聚集计算的结果更新其内部状态。iterate()接受的参数 和Hive中被调用函数的参数是对应的。在这个示例中，只有一个参 数。方法首先检查参数值是否为null,如果是，则将其忽略。否则， result变量实例就被设为value的整数值(如果这是方法第一次接受输 入)，或设为当前值和value值中的较大值(如果已经接受一些值)。如果 输入值合法，我们就让方法返回true。

terminatePartial()方法 Hive需要部分聚集结果时会调用 terminatePartial()方法。这个方法必须返回一个封装了聚集计算当

前状态的对象。在这里，因为只需要对已知的最大值或在没有值时的空 值null进行封装，所以使用一个IntWritable即可。

merge()方法在Hive决定要合并一个部分聚集值和另一个部分聚集值 时会调用merge()方法。该方法接受一个对象作为输入。这个对象的类型必须 和terminatePartial()方法的返回类型一致。在这个示例里，merge() 方法可以直接使用iterate)方法，因为部分结果的聚集和原始值的聚 集的表达方法是相同的。但一般情况下不能这样做(我们后面会看到更普 遍的示例)，这个方法实现的逻辑会合并计算函数和部分聚集的状态。

terminate()方法Hive需要最终聚集结果时会调用terminate()方法。

计算函数需要把状态作为一个值返回。在这里，我们返回实例变量

result.

现在，让我们来执行这个新写的函数：

hive> CREATE TEMPORARY FUNCTION maximum AS ，com.hadoopbook.hive.Maximum，； hive> SELECT maximum(temperature) FROM records;

111

17-3显示了计算函数的处理流程。

Table

init() iterate ⑴ iterated) iterated) terminatePartiaK)

iterate(4)

iterate(2)

terminatePartialO



Maximum 丨 ntUDAFEvaluator instances

init() merge ⑶ merge(4) terminateO



MaximumlntUDAFEvaluator

instance

final

result

17-3.包含UDAF部分结果的数据流

1. 一个更复杂的UDAF

刖面的示例有一个特别的现象：部分聚集结果可以使用和最终结果相同的类型 (IntWritable)来表示。对于更复杂的聚集函数，情况并非如此。考虑一个计算一 组double类型值均值的UDAF，就可以看出这一点。从数学角度来看，要把两个 部分的均值合并成最终的均值是不可能的(参见2.4.2节对combiner函数的讨论)。 作为替代，我们可以用一个数对(目前已经处理过的double值的累积和以及目前 已经处理过的数的个数)来表示部分聚集结果。

这个思路在UDAF中的实现如范例17-4所示。注意，部分聚集结果用一个嵌套的 静态类struct实现，类名是PartialResult,由于我们使用了 Hive能够处理的 字段类型(Java原子数据类型)，所以Hive足够“聪明”，能够自己对这个类进行 序列化和反序列化。

在这个示例中，merge()方法和iterate()方法不同，因为它把“部分和” (partial sum)和“部分计数值” (partial count)分别进行成对的加法合并。此外， terminatePartial()的返回类型为PartialResult,这个类型当然不会给调用

函数的用户看到，terminate()的返回类型则是最终用户可以看到的 DoubleWritableo

范例17-4.计算一组double值均值的UDAF

package com.hadoopbook.hive;

import org.apache.hadoop.hive.ql.exec.UDAF;

import org.apache.hadoop.hive•ql•exec.UDAFEvaluator;

import org.apache.hadoop.hive.serde2.io.DoubleWritable;

public class Mean extends UDAF {

public static class MeanDoubleUDAFEvaluator implements UDAFEvaluator { public static class PartialResult {

double sum; long count;

}

private PartialResult partial;

public void init() { partial = null;

}

public boolean iterate(DoubleWritable value) {

if (value == null) { return true;

}

if (partial == null) { partial = new PartialResult();

}

partial.sum += value>get(); partial.count++; return true;

}

public PartialResult terminatePartial()    {

return partial;

}

public boolean merge(PartialResult other) { if (other == null) {

return true;

}

if (partial == null) { partial = new PartialResult();

}

partial.sum += other.sum; partial.count += other.count; return true;

}

public DoubleWritable terminate() { if (partial == null) {

return null;

}

return new DoubleWritable(partial.sum / partial.count);

}

}

}

##### 17.9延伸阅读

有关Hive的更多信息，请参考O’Reilly在2012年出版的Programming Hive,网 址为 [http://shop.oreilly.com/product/0636920023555.do，](http://shop.oreilly.com/product/0636920023555.do%ef%bc%8c%e4%bd%9c%e8%80%85)[作者](http://shop.oreilly.com/product/0636920023555.do%ef%bc%8c%e4%bd%9c%e8%80%85) Edward Capriolo、 Dean Wampler 茅口 Jason Rutherglen。
