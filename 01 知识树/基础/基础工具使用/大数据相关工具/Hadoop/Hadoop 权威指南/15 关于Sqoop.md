#### 第15章

（作者：Aaron Kimball）

Hadoop平台的最大优势在于它支持使用不同形式的数据。HDFS能够可靠地存储 日志和来自不同渠道的其他数据，MapReduce程序能够解析多种“特定的”(ad hoc)数据格式，抽取相关信息并将多个数据集组合成非常有用的结果。

但是为了能够和HDFS之外的数据存储库进行交互，MapReduce程序需要使用外 部API来访问数据。通常，一个组织中有价值的数据都存储在关系型数据库系统 (RDBMS)等结构化存储器中。Apache Sqoop(/z即.•//叫⑽戸戸是一个开源工 具，它允许用户将数据从结构化存储器抽取到Hadoop中，用于进一步的处理。抽 取出的数据可以被MapReduce程序使用，也可以被其他类似于Hive的工具使 用。(甚至可以使用Sqoop将数据从数据库转移到HBase。)一旦生成最终的分析结 果，Sqoop便可以将这些结果导回数据存储器，供其他客户端使用。

在本章中，我们将了解Sqoop是如何工作的，学习如何在数据处理过程中使 用它。

##### 15.1 获取 Sqoop

在几个地方都可以获得Sqoop。该项目的主要位置是在Apache软件基金会 ([http://sqoop.apache.org/)，](http://sqoop.apache.org/)%ef%bc%8c%e8%bf%99%e9%87%8c%e6%9c%89Sqoop%e7%9a%84%e6%89%80%e6%9c%89%e6%ba%90%e4%bb%a3%e7%a0%81%e5%92%8c%e6%96%87%e6%a1%a3%e3%80%82%e5%9c%a8%e8%bf%99%e4%b8%aa%e7%ab%99%e7%82%b9%e5%8f%af%e4%bb%a5%e8%8e%b7)[这里有Sqoop的所有源代码和文档。在这个站点可以获](http://sqoop.apache.org/)%ef%bc%8c%e8%bf%99%e9%87%8c%e6%9c%89Sqoop%e7%9a%84%e6%89%80%e6%9c%89%e6%ba%90%e4%bb%a3%e7%a0%81%e5%92%8c%e6%96%87%e6%a1%a3%e3%80%82%e5%9c%a8%e8%bf%99%e4%b8%aa%e7%ab%99%e7%82%b9%e5%8f%af%e4%bb%a5%e8%8e%b7) 得Sqoop的官方版本和当前正在开发的新版本的源代码，这里同时还提供项目编 译说明。另外，也可从Hadoop供应商的发行版中获得Sqoop。

如果是从Apache下载的版本，它将被放在一个类似于//?⑽

的目录中。我们称这个目录为$SQOOP_HOME。可以通过运行可执行脚本 $SQOOP_HOME/bin/sqoop 来启动 Sqoop。

如果使用供应商的版本，那么安装包会把Sqoop的脚本放在类似于叫⑽;? 的标准位置。可以通过在命令行上简单地键入sqoop来运行它。无论通过何种方式 安装了 Sqoop，从现在起，我们都用执行sqoop脚本来表示运行它。

Sqoop 2

Sqoop 2对Sqoop进行了重写，以解决Sqoop 1架构上的局限性。例如， Sqoopl是命令行工具，不提供Java API，因此很难嵌入到其他程序中。另外， Sqoop 1的所有连接器都必须掌握所有输出格式，为此编写新的连接器就需要 做大量工作。Sqoop 2具有用以运行作业的服务器组件和一整套客户端，包括 命令行接口 (CLI)、网站用户界面、REST API和Java API。Sqoop 2还能使用其 他执行引擎，例如Spark。注意，Sqoop 2的CLI与Sqoop 1的CLI并不兼容。

Sqoop 1是目前比较稳定的发布版本，本章使用的也是Sqoop 1。Sqoop 2正处 于开发期间，可能并不具备Sqoop 1的所有功能，因此，如果你想要在自己的 产品中使用Sqoop 2，首先应当检查一下它是否支持你的用例。

不带参数运行Sqoop是没有什么意义的:

% sqoop

Try sqoop help for usage.

Sqoop组织成一组工具或命令。不选择工具，Sqoop便无所适从。help是其中一 个工具的名称，它能够打印出可用工具的列表，如下所示：

% sqoop help

usage: sqoop COMMAND [ARGS]

Available commands:

codegen    Generate code to interact with database records

create-hive-table    Import a table definition into Hive

eval    Evaluate a SQL statement and display the results

export    Export an HDFS directory to a database table

help    List available commands

import    Import a table from a database to HDFS

import-all-tables Import tables from a database to HDFS job    Work with saved jobs

list-databases    List available databases on a server

list-tables    List available tables in a database

merge

metastore

version



Merge results of incremental imports Run a standalone Sqoop metastore Display version information

See •sqoop help COMMAND * for information on a specific command.

根据它的解释， 说明：•



Jj



t过将特定工具的名称作为参



，help还能提供对该工具的使用



% sqoop help import

usage: sqoop import [GENERIC-ARGS] [TOOL-ARGS]

Common arguments:

--connect <jdbc-uri> Specify JDBC connect string --driver <class-name> Manually specify DDBC driver class to use --hadoop-home <dir> Override $HADOOP HOME

--help    Print usage instructions

-P    Read password from console

--password 〈password〉 Set authentication password --username <username> Set authentication username --verbose    Print more information while working

运行Sqoop工具的另外一种方法是使用与之对应的特定脚本。这样的脚本一般被 命名为sqoop-tooLname，例如，sqoop-help恥sqoop-’import等。运行这两个脚本与 运行sqoop help或sqoop import命令是一样的。

##### 15.2 Sqoop连接器

Sqoop拥有一个可扩展的框架，使得它能够从（向）任何支持批量数据传输的外部存 储系统导人（导出）数据。一个Sqoop连接器（connector）就是这个框架下的一个模块 化组件，用于支持Sqoop的导入和导出操作。Sqoop附带的连接器能够支持大多 数常用的关系型数据库系统，包括MySQL、PostgreSQL、Oracle、SQL Server、 DB2和Netezza。同时还有一个通用的JDBC连接器，用于连接支持JDBC.协议的 数据库。Sqoop所提供的MySQL、PostgreSQL、Oracle和Netezza连接器都是经 过优化的，通过使用数据库特定的API来提供高效率的批量数据传输（第15.5.4小 节中将会进行详细介绍）。

除了内置的Sqoop连接器外，还有很多针对各种数据存储器的第三方连接器可 用，能够支持对企业级数据仓库（例如Teradata）和NoSQL存储器（例如Couchbase） 的连接。这些连接器必须另外单独下载，可以根据连接器所附带的安装说明将其 添加到已有的Sqoop安装中。

##### 15.3 —个导入的例子

在安装了 Sqoop之后，可以用它将数据导入（import）到Hadoop。在本章的所有示 例中，我们都使用了支持多种平台的易用数据库系统MySQL作为外部数据源。

安装和配置MySQL时，可以参考在线文档叫/.cow/tfoc），特别是其中的 第2章应该很有帮助。基于Debian的Linux系统（如Ubuntu）的用户可以通过键入 sudo apt-get install mysql-client mysql-server 进行安装;RedHat 的用 户可以通过键人sudo yum install mysql mysql-server进行安装。

现在，MySQL已经安装好，让我们先登录，然后创建一个数据库（范例15-1）

范例15-1..创建一个新的MySQL数据库模式

% mysql -u root -p

Enter password:

Welcome to the MySQL monitor. Commands end with ; or \g.

Your MySQL connection id is 235

Server version: 5.6.21 MySQL Community Server (GPL)

Type 'help;' or *\h* for help. Type '\c' to clear the current input statement.

mysql>CREATE DATABASE hadoopguide; Query OK, 1 row affected (0.00 sec)

mysql>GRANT ALL PRIVILEGES ON hadoopguide.* TO    localhost■;

Query OK, 0 rows affected （0.00 sec）

mysql>quit;

Bye

前面的密码提示要求输入root用户的密码，就像root用户通过密码进行shell登录 一样。如果你正在使用Ubuntu或其他不支持root直接登录的Linux系统，则键入 安装MySQL时设定的密码。如果没有设置密码，就直接键人回车。

在这个会话中，我们创建了一个新的名为hadoopguide的数据库模式，本章将一 直使用这个数据库模式，然后我们允许所有本地用户查看和修改hadoopguide模 式的内容，最后，关闭这个会话。

现在让我们登录到数据库（这次不是作为root,而是你自己），然后创建一个将被导

①当然，在生产环境中，我们需要更谨慎地对待访问控制，而这里只是用于演示的目的。上述 的授权也假设你正在使用一个伪分布式Hadoop实例。如果使用的是一个真正的分布式 Hadoop集群，至少需要一个启用了远程访问的用户，他的账户用干通过Sqoop执行导入和 导出。

人HDFS的表(范例15-2)。 范例15-2.填充数据库

% mysql hadoopguide

Welcome to the MySQL monitor. Commands end with ; or \g.

Your MySQL connection id is 257

Server version: 5.6.21 MySQL Community Server (GPL)

Type 'help;1 or 1\h1 for help. Type W to clear the current input statement.

mysql> CREATE TABLE widgets(id INT NOT NULL PRIMARY KEY AUTOJENCREMENT,

■> widget一name VARCHAR(64) NOT NULL,

-> price DECIMAL(10,2),

-> design_date DATE,

-> version INT,

-> design一comment VARCHAR(100));

Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO widgets VALUES (NULL, 'sprocket、 0.25, '2010-02-10*

->1, 'Connects two gizmos');

Query OK, 1 row affected (0.00 sec)

mysql>INSERT INTO widgets VALUES (NULL, 1 gizmo\ 4.00, •2009-11-30•, 4, ->NULL);

Query OK, 1 row affected (0.00 sec)

mysql>INSERT INTO widgets VALUES (NULL, 'gadget1, 99.99, 11983-08-131 -> 13, 'Our flagship product1);

Query OK, 1 row affected (0.00 sec) mysql> quit;

在前面的示例中，我们创建了一个名为widgets的新表。在本章后面的例子中我 们都将使用这个虚构的产品数据库。widgets表有几个不同数据类型的字段。

在进行下一步之前，还需要下载MySQL的JDBC驱动的MR文件(Connector/J)， 并将其存放在Sqoop的lib目录下，从而使之被添加到Sqoop的类路径中。• 现在让我们使用Sqoop将这个表导入HDFS：

% sqoop import --connect jdbc:mysql://localhost/hadoopguide \ > --table widgets -ml

14/10/28 21:36:23



INFO tool.CodeGenTool: Beginning code generation 14/10/28 21:36:41 INFO mapreduce.Dob: Dob job_1413746845532_0008 completed successfully

14/10/28 21:36:28 14/10/28 21:36:35 uber mode : false 14/10/28 21:36:35 14/10/28 21:36:41



INFO mapreduce.Dob: INFO mapreduce.Dob:

INFO mapreduce.Dob: INFO mapreduce.Dob:



Running job: job_1413746845532_0008 Job job_1413746845532_0008 running in

map 0% reduce 0%

map 100% reduce 0%



14/10/28 21:36:41 INFO mapreduce.ImportDobBase: Retrieved 3 records.

Sqoop的import工具会运行一个MapReduce作业，该作业会连接MySQL数据库 并读取表中的数据。默认情况下，该作业会并行使用4个map任务来加速导入过 程。每个任务都会将其所导入的数据写到一个单独的文件，但所有4个文件都位 于同一个目录中。在本例中，由于我们知道只有三行可以导入的数据，因此指定 Sqoop只使用一个map任务（-m 1），这样我们就只得到一个保存在HDFS中的 文件。

我们可以检査这个文件的内容，如下所示：

% hadoop fs -cat widgets/part-m-00000

1,    sprocket,0.25,2010-02-10,1,Connects two gizmos

2,    gizmo,4.00,2009-11-30,4,null

3>gadgetJ99.99,1983-08-13,13Our flagship product

![img](Hadoop43010757_2cdb48_2d8748-183.jpg)



本例中使用的连接字符串（jdbc:mysql://localhost/hadoopguide）轰明需要从本地机 器上的数据库中读取数据。如果使用了分布式Hadoop集群，则连接字符串中 不能使用localhost，否则与数据库不在同一台机器上运行的map任务都将 无法连接到数据库。即使是从数据库服务器所在主机上运行Sqoop,也需要为数据 库服务器指定完整的主机名。

在默认情况下，Sqoop会将我们导入的数据保存为逗号分隔的文本文件。如果导 入数据的字段内容中存在分隔符，我们可以另外指爸分隔符、字段包围字符和转 义字符。使用命令行参数可以指定分隔符、文件格式、压缩方式以及对导入过程 进行更细粒度的控制，Sqoop自带的《Sqoop使用指南》1或Sqoop的在线帮助 （sqoop help import,或者在CDH中使用man sqoop-import）找到对应于相 关参数的描述。

###### 文本和二进制文件格式

Sqoop可以将数据导入成几种不同的格式。文本文件（默认）是一种人类可读的数据 表示形式，并且是平台独立和最简单的数据格式。但文本文件不能保存二进制字 段（例如数据库中类型为VARBINARY的列），并且在区分null值和字符串null时 可能会出现问题（尽管使用--null-string选项可以控制空值的表示方式）。

①可以从Apache软件基金会网站（//即••//叫获取。

为了处理这些情况，应该使用Sqoop的SequenceFile格式、Avro格式或Parquet

文件。这些二进制格式能够为导人的数据提供最精确的表示方式，同时还允许对 数据进行压缩，并支持MapReduce并行处理同一文件的不同部分。然而，Sqoop 的目前版本还不能将Avro或SequenceFile文件加载到Hive中(尽管可以手动地 Avro数据文件加载到Hive中，而Parquet则可以通过Sqoop直接加载到Hive 中)。SequenceFile文件格式的另一个缺点是它只支持Java语言，而Avro和 Parquet数据文件却可以被很多种语言处理。

##### 15.4生成代码

除了能够将数据库表的内容写到HDFS, Sqoop同时还生成了一个Java源文件 (widgets.java),保存在当前的本地目录中。在运行了前面的sqoop import命 令之后，可以通过Is widgets, java命令看到这个文件。

在15.5节中，我们将看到Sqoop在把源数据库的表数据写到HDFS之前，会首先 用生成的代码对其进行反序列化。

生成的类(widgets)中能够保存一条从被导入表中取出的记录。该类可以在 MapReduce中使用这条记录，也可以将这条记录保存在HDFS中的一个 SequenceFile文件中。在导入过程中，由Sqoop生成的类会将每一条被导入的 行保存在SequenceFile文件的键-值对格式中“值”的位置上。

也许你不想将生成的类命名为widgets,因为每一个类的实例只对应于一条记 录。我们可以使用另外一个Sqoop工具来生成源代码，但并不真正执行导入操 作：但生成的代码仍然会检査数据库表，以确定与每个字段相匹配的数据类型：

% sqoop codegen --connect jdbc:mysql://localhost/hadoopguide \

\>--table widgets --class-name Widget

codegen工具只是简单地生成代码，它不执行完整的导入操作。我们指定生成一 个名为Widget的类，这个类将被写到Widget.java文件中。在之前执行的导人 过程中，我们也可以指定类名--class-name和其他代码生成参数。如果你意外 地删除了生成的源代码，或希望使用不同于导入过程的设定来生成代码，都可以 用这个工具来重新生成代码。

如果计划使用导入到SequenceFile文件中的记录，你将不可避免地用到生成的 类(对SequenceFile文件中的数据进行反序列化)。在使用文本文件中的记录时不 需要用到生成的代码，但在15.6节中，我们将看到Sqoop生成的代码有助于解决 数据处理过程中的一些繁琐问题。

###### 其他序列化系统

最近的Sqoop版本支持基于Avro的序列化和模式生成(参见第12章)，允许在项目 中使用Sqoop，而无须集成生成的代码。

##### 15.5深入了解数据库导入

如前所述，Sqoop是通过一个MapReduce作业从数据库中导入一个表，这个作业 从表中抽取一行行记录，然后将记录写入HDFS。MapReduce是如何读取记录的 呢？本节将解释Sqo叩的底层工作机理。

图15-1粗略演示了 Sqoop是如何与源数据库及Hadoop进行交互的。像Hadoop — 样，Sqoop是用Java语言编写的。Java提供了一个称为JDBC(Java Database Connectivity)的API,应用程序可以使用这个API来访问存储在RDBMS中的数 据，同时可以检查数据类型。大多数数据库厂商都提供了 JDBC驱动程序，其中 实现了 JDBC API并包含用于连接其数据库服务器的必要代码。

Metadata

(column names, types, etc)

Generated record container class

MapReduce Job



launches



15-1. Sqoop的导入过程

![img](Hadoop43010757_2cdb48_2d8748-186.jpg)



根据用于访问数据库的连接字符串中的URL, Sqoop尝试预测应该加载哪个驱 动程序，而你要事先下载所需的JDBC驱动并且将其安装在Sqoop客户端。在 Sqoop无法判定使用哪个JDBC驱动程序时，用户可以明确指定如何将JDBC 驱动程序加载到Sqoop，这样一来，Sqo叩就能够支持大多数的数据库平台。

在导人开始之前，Sqoop使用JDBC来检查将要导入的表。它检索出表中所有的列 以及列的SQL数据类型。这些SQL类型(VARCHAR、INTEGER等)被映射为Java数 据类型(String、Integer等)，在MapReduce应用中将使用这些对应的Java类型 来保存字段的值。Sqoop的代码生成器使用这些信息来创建对应表的类，用于保 存从表中抽取的记录。

例如，之前提到的Widget类包含下列方法，这些方法用于从抽取的记录中检索 所有的列：

public Integer get id()j

public String get_widget name(); public java.math.BigDecimal get price(); public java.sql.Date get design date(); public Integer get version();

public String get design comment();

不过，对于导入来说，更关键的是DBWritable接口的序列化方法，这些方法能 使Widget类和JDBC进行交互：

public void readFields(ResultSet dbResults) throws SQLException; public void write(PreparedStatement dbStmt) throws SQLException;

H)BC的ResultSet接口提供了一个用于从查询结果中检索记录的游标；这里的 readFields()方法将用ResultSet中一行数据的列来填充Widget对象的字 段。这里的write()方法允许Sqoop将新的Widget行插入表，这个过程称为导 出(exporting)。15.8节将对此进行讨论。

Sqoop启动的MapReduce作业用到一个InputFormat，它可以通过JDBC从一个

数据库表中读取部分内容。Hadoop提供的DataDrivenDBInputFormat能够为几个map 任务对查询结果进行划分。

使用一个简单的查询通常就可以读取一张表的内容，例如：

SELECT coLl}coL2fcoL3t ... FROM tabLeName

但是，为了获得更好的导入性能，人们经常将这样的查询划分到多个节点上执 行。査询是根据一个划分列(splitting column)来进行划分的。根据表的元数据，

Sqoop会选择一个合适的列作为划分列（如果主键存在的话，通常是表的主键）。主 键列中的最小值和最大值会被读出，与目标任务数一起用来确定每个map任务要 执行的查询。

例如，假设widgets表中有100 000条记录，其id列的值为0〜99 999。在导入 这张表时，Sqoop会判断出id是表的主键列。启动MapReduce作业时，用来执行 导人的 DataDrivenDBInputFormat 便会发出一条类似于 SELECT MIN（id）, MAX （id） FROM widgets的査询语句。检索出的数据将用于对整个数据集进行划 分。假设我们指定并行运行5个map任务（使用-m 5），这样便可以确定每个map 任务要执行的査询分别为:SELECT id, widget_name, . •. FROM widgets

WHERE id >= 0 AND id < 20000，SELECT id, widget_name, . • • FROM widgets WHERE id >= 20000 AND id < 40000，…，以此类推。

划分列的选择是影响并行执行效率的重要因素。如果id列的值不是均匀分布的 （也许在id值50 000到75 000的范围内没有记录），那么有一部分m叩任务可能 只有很少或没有工作要做，而其他任务则有很多工作要做。在运行一个导入作业 时，用户可以指定一个列作为划分列（使用一split-参数），从而调整作业的划分 使其符合数据的真实分布。如果使用-m 1参数来让一个任务执行导入作业，就不 再需要这个划分过程。

在生成反序列化代码和配置InputFormat之后，Sqoop将作业发送到MapReduce

集群。map任务执行查询并且将ResultSet中的数据反序列化到生成类的实例， 这些数据要么直接保存在SequenceFile文件中，要么在写到HDFS之前被转换

成分隔的文本。

###### 15.5.1导入控制

Sqoop不需要每次都导入整张表。例如，可以指定仅导入表的部分列。用户也可 以在査询中加入WHERE子句（使用--where参数），以此来限定需要导入的记 录。例如，如果上个月已经将id为0〜99 999的记录导入，而本月供应商的产品 目录中增加了 1000种新部件，那么导入时在查询中加入子句WHERE id >= 100000，就可以实现只导入所有新增的记录。用户提供的WHERE子句会在任务 分解之前执行，并且被下推至每个任务所执行的查询中。

通过指定--query参数可以实现更多控制，例如执行列变换。

###### 15.5.2导入和一致性

在向HDFS导入数据时，重要的是要确保访问的是数据源的一致性快照。从一个 数据库中并行读取数据的map任务分别运行在不同的进程中，因此它们不可能共 享同一个数据库事务。保证一致性的最好方法是在导入时不允许运行任何对表中 现有数据进行更新的进程。

###### 15.5.3增量导入

定期运行导入是一种很常见的方式，这样做可以使HDFS的数据与数据库的数据 保持同步。为此，需要识别哪些是新数据。对于某一行来说，只有当特定列（由--check-column参数指定）的值大于指定值（通过--last-value没置）时，Sqoop才 会导入该行数据。

通过--last-value所指定的值可以是严格递增的行号，例如在MySQL中有 AUTO.INCREMENT属性的主键。这种模式很适用干数据库中的表只有新行添加， 而不存在对现有行更新的情况。这称为append模式，它通过--incremental append来激活。另一种情况是基于时间的增量导入（通过--incremental lastmodified激活），它适用于现有行也有可能被更新的情况，此时需要指定列 （通过--check-column参数指定）以记录最近一次更新的时间。

增量导入结束时，程序显示在下次导入时将被指定为--last-value的值，这对 于手工运行的增量导入来说非常重要，但对定期运行的增量导入，最好使用 Sqoop的saved job工具，它可以自动保存最近一次的值并在下次作业运行时使 用。要想了解saved job的更多用法，可以输入sqoop job -help命令。

###### 15.5.4直接模式导入

Sqoop的架构支持它在多种可用的导入方法中进行选择，而大多数数据库都使用 上述基于DataDrivenDBInputFormat的方法。一些数据库提供了能够快速抽取 数据的特定工具，例如MySQL的mysqldump能够以大于JDBC的吞吐率从表中 读取数据。在Sqoop的文档中将这种使用外部工具的方法称为直接模式（direct mode）。由于直接模式并不像JDBC方法那样通用，（例如，MySQL的直接模式不 能处理大对象数据，类型为CLOB或BLOB的列，Sqoop需要使用JDBC专用的 API将这些列载入HDFS。）所以使用直接模式导入时必须由用户明确地启动（通

过--direct 参数）。

对于那些提供了此类特定工具的数据库，Sqoop使用这些工具就能够得到很好的 效果。采用直接模式从MySQL中导入数据通常比基于JDBC的导入更加高效（就 map任务和所需时间而言）。Sqoop仍然并行启动多个m叩任务，接着这些任务将 分别创建mysqldump程序的实例并且读取它们的运行结果。Sqoop也支持采用直 接模式从PostgreSQL、Oracle和Netezz中导入数据。

即使用直接模式来访问数据库的内容，元数据的查询仍然要通过JDBC来实现

##### 15.6使用导入的数据

一旦数据导入HDFS,就可以供定制的MapReduce程序使用。导入的文本格式数 据可以供Hadoop Streaming中的脚本或以TextlnputFormat为默认格式运行的 MapReduce作业使用。

为了使用导入记录的个别字段，必须对字段分隔符（以及转义/包围字符）进行解 析，抽出字段的值并转换为相应的数据类型。例如，在文本文件中，“sprocket” widget的id表示成字符串“1”，但必须被解析为Java的Integer或int类型 的变量。Sqoop生成的表类能够自动完成这个过程，使你可以将精力集中在真正 要运行的MacReduce作业上。每个自动生成的类都有几个名为parse（）的重载方 法，这些方法可以对表示为Text、CharSequence、char□或其他常见类型的数 据进行操作。

名为MaxWidgetld的MapReduce应用（在示例代码中）可以找到具有最大ID的部 件。使用示例代码附带的Maven POM，这个类可以和—起编译成一个 JAR文件。该］AR文忤名为sqoop-examples.jar，并且像下面这样运行：

% HADOOP_CLASSPATH=$SQOOP_HOME/sqoop-version.jar hadoop jar \

\> sqoop-examples•jar MaxWidgetld -libjars $SQOOP_HOME/sqoop-version.jar

MaxWidgetId.run（）方法在运行时以及map任务在集群上运行时（通过-libjars 雜），该命令繼了 Sqoop位于本鵬鷄径中（舰$HADOOP_CLASSPATH）0

运行之后，HDFS的maxwidgets路径中便有一个名为part-r-00000的文件，其内 容如下：

3,gadget,99.99,1983-08-13,13,Our flagship product

注意，在这个MapReduce示例程序中，一个Widget对象从mapper被发送到 reducer,这个自动生成的Widget类实现了 Hadoop提供的Writable接口，该接 口允许通过Hadoop的序列化机制来发送对象以及写到SequenceFile文件或从 SequenceFile文件读出对象。

这个MaxWidgetID的例子建立在新的MapReduce API之上。虽然某些高级功能 （例如使用大对象数据）只有在新的API中使用起来才更方便，但无论新旧API，都 可以用来构建依赖于Sqoop生成代码的MapReduce应用。

前面12.7节介绍了用于处理Avro格式导入的API。采用Avro通用映射时， MapReduce程序不需要使用针对数据表的模式所生成的代码（尽管使用Avro的特 定编译器时这也是其中一个选项；在这种情况下，Sqoop不会生成代码）。示例代 码中包含有一个名为MaxWidgetldGenericAvro的程序，用于找出具有最大ID的部件 并将结果写入一个Avro数据文件。

###### 导入的数据与Hive

我们将在第17章看到，对于很多类型的分析任务来说，使用类似于Hive的系统 来处理关系操作有利于加快分析任务的开发。特别是对于那些来自于关系数据源 的数据，使用Hive是非常有帮助的。Hive和Sqoop共同构成了一个强大的服务于 分析任务的工具链。

假设在我们的系统中有另外一组数据记录，来自一个基于Web的零部件采购系 统。这个系统返回的记录文件中包含部件ID、数量、送货地址和订单日期。

下面是此类记录的例子：

1.15.120    Any St.,Los Angeles,CA,90210,2010-08-01

3.4.120    Any St.,Los Angeles,CA,90210,2010-08-01 2,5,400 Some Pl.,Cupertino,CA,95014,2010-07-30 2,7,88 Mile Rd.,Manhattan,NY,10005,2010-07-18

通过使用Hadoop来分析这组采购记录，我们可以深入了解我们的销售业务。将这 些数据与来自关系数据源（Widgets表）的数据相结合，可以使我们做得更好。在 这个例子中，我们将计算哪个邮政编码区域的销售业绩最好，便可以让我们的销 售团队更加关注该区域。为了做到这一点，我们同时需要来自销售记录和widgets 表的数据。

上述销售记录数据保存在一个名为sales.log的本地文件中 首先，让我们将销售数据载人Hive:

hive>CREATE TABLE sales(widget_id INT, qty INT,

\>street STRING, city STRING, state STRING,

\>zip INT, sale_date STRING)

\>ROW FORMAT DELIMITED FIELDS TERMINATED BY

OK

Time taken: 5.248 seconds

hive> LOAD DATA LOCAL INPATH "chl5-sqoop/sales.log" INTO TABLE sales;

Loading data to table default.sales

Table default.sales stats: [numFiles=l, numRows=0, totalSize=189, rawDataSize=0] OK

Time taken: 0.6 seconds

Sqoop能够根据一个关系数据源中的表来生成一个Hive表。既然我们已经将 widgets表的数据导入到HDFS，那么我们就直接生成相应Hive表的定义，然后 加载保存在HDFS中的数据：

% sqoop create-hive-table --connect jdbc:mysql://localhost/hadoopguide \ > --table widgets --fields-terminated-by •/

hive.Hivelmport: OK

hive.Hivelmport: Time taken: 1.098 seconds hive.Hivelmport: Hive import complete.



14/10/29 11:54:52 INFO 14/10/29 11:54:52 INFO 14/10/29 11:54:52 INFO % hive

hive> LOAD DATA INPATH "widgets" INTO TABLE widgets; Loading data to table widgets OK

Time taken: 3.265 seconds

在为一个特定的已导入数据集创建相应的Hive表定义时，我们需要指定该数据集 所使用的分隔符。否则，Sqoop将允许Hive使用它自己的默认分隔符（与Sqo叩的 默认分隔符不同）。

Hive的数据类型不如大多数SQL系统的丰富。很多SQL类型在Hive中都没有 直接对应的类型。当Sqoop为导入操作生成Hive表定义时，它会为数据列选 择最合适的Hive类型，这样可能会导致数据精度的下降。一旦出现这种情 况，Sqoop就会提供一条警告信息，如下所示：

14/10/29 11:54:43 WARN hive.TableDefWriter:

Column design—date had to be cast to a less precise type in Hive

如果想直接从数据库将数据导入到Hive,可以将上述的三个步骤（将数据导入 HDFS；创建Hive表；将HDFS中的数据导入Hive）缩短为一个步骤。在进行导入 时，Sqoop可以生成Hive表的定义，然后直接将数据导入Hive表。如果我们还没 有执行过导入操作，就可以使用下面的命令，根据MySQL中的数据直接创建 Hive 中的 widgets 表:

% sqoop import --connect jdbc:mysql://localhost/hadoopguide \ >--table widgets -m 1 --hive-import

![img](Hadoop43010757_2cdb48_2d8748-187.jpg)



使用--hive-import参数来运行sqoop import工具，可以从源数据库中直接 将数据载入Hive,它自动根据源数据库中表的模式来推断Hive表的模式。这 样，只需要一条命令，你就可以在Hive中来使用自己的数据。

无论选择哪一种数据导入的方式，现在我们都可以使用widgets数据集和sales 数据集来计算最赚钱的邮政编码地区。让我们来做这件事，并且把查询的结果保 存在另外一张表中，供将来使用：

hive> CREATE TABLE zip_profits

\>    AS

\>    SELECT SUM(w.price * s.qty) AS sales_vol, s.zip FROM SALES s

\>    JOIN widgets w ON (s.widget_id = w.id) GROUP BY s.zip;

參 •    •

Moving data to: hdfs://localhost/user/hive/warehouse/zip_profits

OK

hive> SELECT * FROM zip一profits ORDER BY sales一vol DESC;

OK

403.71 90210 28.0 10005 20.0 95014

##### 15.7导入大对象

很多数据库都支持在一个字段中保存大量的数据，根据数据是文本还是二进制类 型，通常将其保存在表中CLOB或BLOB类型的列中。数据库一般会对这些“大对 象”进行特殊处理。大多数的表在磁盘上的物理存储都如图15-2所示。通过行扫 描来确定哪些行匹配特定的查询条件时，通常需要从磁盘上读出每一行的所有 列。如果也是以这种方式“内联”（inline）存储大对象，它们会严重影响扫描的性 能。因此，一般将大对象与它们的行分开存储，如图15-3所示。在访问大对象 时，需要通过行中包含的引用来“打开”它。

![img](Hadoop43010757_2cdb48_2d8748-188.jpg)



![img](Hadoop43010757_2cdb48_2d8748-189.jpg)



15-2.数据库通常以行数组的方式来存储表，行中所有列存储在相邻的位置

15-3.大对象通常保存在单独的存储区域，行的主存储区域包含指向大对象的间接引用



![img](Hadoop43010757_2cdb48_2d8748-191.jpg)



在数据库中使用大对象的困难表明，像Hadoop这样的系统更适合于处理大型的、 复杂的数据对象，也是存储此类信息的理想选择。Sqoop能够从表中抽取大对象 数据，并且将它们保存在HDFS中供进一步处理。

同在数据库中一样，MapReduce在将每条记录传递给mapper之前一般要对其进行 物化(materialize)。如果单条记录真的很大，物化操作将非常低效。

如前所示，Sqoop所导入的记录在磁盘上的存储格式与数据库的内部数据结构非 常相似：将每条记录的所有字段放在一起组成的一个记录数组。在导入的记录上 运行一个MapReduce程序时，每个map任务必须将所读记录的所有字段完全物 化。如果MapReduce程序所处理的输入记录中仅有很小一部分的大对象字段的内 容，那么将所有记录完全物化将导致程序效率低下。此外，从大对象的大小来 看，在内存中进行完全物化也许是无法实现的。

为了克服这些困难，当导入的大对象数据大于阈值16 M时(通过sqoop. inline.lob.length.max设置，以字节为单位)，Sqoop将导入的大对象数据存 储在LobFile格式的单独文件中。LobFile格式能够存储非常大的单条记录(使 用了 64位的地址空间)，每条记录保存一个大对象。LobFile格式允许客户端持

有对记录的引用，而不访问记录内容，对记录的访问是通过

java.io.InputStream(用干二进制对象)或java.io.Reader(用于字符对象)来实 现的。

在导入一条记录时，所有的“正常”字段会在一个文本文件中一起被物化，同时 还生成一个指向保存CLOB或BLOB列的LobFile文件的引用。例如，假设我们的 widgets表有一个名为schematic的BLOB字段，该字段用于保存每个部件的原 理图。

导入的记录可能像下面这样：

2, gizmo,4.00,2009，11-30,4, null) externalLob(lf,lobfile0，100,5011714)

externalLob(...)是对外部存储大对象的一个引用，这个大对象以LobFile格式 (If)存储在lobfileO文件中，同时还给出了该对象在文件中的字节位移和长度。

在使用这条记录时，Widget.get_schematic()方法会返回一个为BlobRef类型 的对象，用于引用schematic歹I］，但这个对象并不真正包含记录的内容。 BlobRef.getDataStream()方法实际会打开LobFile文件并返回一个 InputStream,用于访问schematic字段的内容。

在使用一个MapReduce作业来处理许多Widget记录时，可能你只需要访问少数 几条记录的schematic字段。单个原理图数据可能有几兆大小或更大，使用这种 方式时，只需要承担访问所需大对象的I/O开销。

在一个map任务中，BlobRef和ClobRef类会缓存对底层LobFile文件的引 用。如果你访问几个顺序排列记录的schematic字段，就可以利用现有文件指针 来定位下一条记录。

##### 15.8执行导出

E Sqoop中，导入(import)是指将数据从数据库系统移动到HDFS。与之相反，导 U(expOrt)是将HDFS作为数据源，而将一个远程数据库作为目标。在前面的几个

小节中，我们导入了一些数据并且使用Hive对数据进行了分析。我们可以将分析 的结果导出到一个数据库中，供其他工具使用。

将一张表从HDFS导出到数据库时，必须在数据库中创建一张用于接收数据的目 标表。虽然Sqoop可以推断出哪个Java类型适合存储SQL数据类型，但反过来却 是行不通的（例如，有几种SQL列的定义都可以用来存储Java的String类型， 如CHAR（64）、VARCHAR（200）或其他一些类似定义）。因此，必须由用户来确定哪 些类型是最合适的。

我们打算从Hive中导出zip_profits表。首先需要在MySQL中创建一个具有相 同列顺序及合适SQL类型的目标表：

% mysql hadoopguide

mysql>CREATE TABLE saleszip (volume DECIMAL(8,2), zip INTEGER);

Query OK, 0 rows affected (0.01 sec)

接着我们运行导出命令：

% sqoop export --connect jdbc:mysql://localhost/hadoopguide -m 1 \

\>--table sales—by一zip --export-dir /user/hive/v«rehouse/zip_profits \ >--input-fields-terminated-by '\0001*

參 •聲

14/10/29 12:05:08 INFO mapreduce.ExportJobBase: Transferred 176 bytes in 13.5373 seconds (13.0011 bytes/sec)

14/10/29 12:05:08 INFO mapreduce.ExportJobBase: Exported 3 records.

最后，可以通过检査MySQL来确认导出成功：

% mysql hadoopguide -e 'SELECT ♦ FROM sales_by__zip'

在Hive中创建zip_profits表时，我们没有指定任何分隔符。因此Hive使用了 自己的默认分隔符：字段之间使用Ctrl-A字符（Unicode编码0x0001）分隔，每条 记录末尾使用一个换行符分隔。当我们使用Hive来访问这张表的内容时（SELECT 语句），Hive将数据转换为制表符分隔的形式，用于在控制台上显示。但是直接从 文件中读取这张表时，我们要将所使用的分隔符告知Sqoop。Sqcx）p默认记录是以 换行符作为分隔符，但还需要将字段分隔符Ctrl-A告之Sqoop。可以在sqoop export命令中使用--input-fields-terminated-by参数来指定字段分隔符。

Sqoop在指定分隔符时支持几种转义序列（以字符T井始）。

在示例语法中，所用的转义序列被包围在1单引号’中，以确保shell会按字面意义 处理它。如果不使用引号，前导的反斜杠就需要转义处理（例如，--input-fields-terminated-by \\0001）。表 15-1 列出了 Sqoop 所支持的转义序列。

表15-1.转义序列可以用于指定非打印字符作为Sqoop中字段和记录的分隔符

| 转义序列 | 描述                                                         |                  |
| -------- | ------------------------------------------------------------ | ---------------- |
| \b       | 退格                                                         |                  |
| \n       | 换行                                                         |                  |
| \r       | 回车                                                         |                  |
| \t       | 制表符                                                       |                  |
| V        | 单引号                                                       |                  |
| \w       | 双引号                                                       |                  |
| \\       | 反斜杠                                                       |                  |
| \0       | NUL。用于在字段或行之间插人NUL字符或在--enclosed-by、 enclosed-by和--escaped-by参数中使用时表示禁用包围/转义 | --optionally -   |
| \Qooo    | 一个Unicode字符代码点的八进制表示，实际字符由八进制值⑻o      | 指定             |
| \Qxhhh   | 一个Unicode字符代码点的十六进制表示，采用\0xMh的形式， 制值。例如，-•fields-terminated-by ’\0xl0•指定的是回车符 | 其中hh/7是十六进 |

##### 15.9深入了解导出功能

Sqoop导出功能的架构与其导入功能的非常相似（参见图15-4）。在执行导出操作之 前，Sqoop会根据数据库连接字符串来选择一个导出方法。对于大多数系统来 说，Sqoop都会选择JDBC。然后，Sqoop会根据目标表的定义生成一个Java类。 这个生成的类能够从文本文件中解析出记录，并能够向表中插入类型合适的值（除 了能够读取ResultSet中的列）。接着，会启动一个MapReduce作业，从HDFS 中读取源数据文件，使用生成的类解析出记录，并且执行选定的导出方法。

基于JDBC的导出方法会产生一批INSERT语句，每条语句会向目标表中插入多条 记录。在大部分的数据库系统中，通过一条浯句插入多条记录的执行效率要高于 多次执行插入单条记录的INSERT语句。多个独立的线程被用干从HDFS读取数 据并与数据库进行通信，以确保涉及不同系统的I/O操作尽量能够重叠执行。

(2)launches

MapReduce Job

▼

hdfs

Database

table

娜霸難纂

hdfs

......(l)Metadata

(column names, types, etc.)

Generated record container class

15-4.使用MapReduce并行执行导出

对于MySQL数据库来说，Sqoop可以采取使用mysqlimport的直接模式方法。 每个map任务会生成一个mysqlimport进程，该进程通过本地文件系统上的一个 命名FIFO通道进行通信，数据通过这个FIFO通道流入mysqlimport，然后再被 写入数据库。

虽然从HDFS读取数据的MapReduce作业大多根据所处理文件的数量和大小来选 择并行度（map任务的数量），但Sqoop的导出工具允许用户明确设定任务的数量。 由于导出性能会受并行的数据库写入线程数量的影响，所以Sqoop使用 CombineFilelnputFormat类将输入文件分组分配给少数几个map任务去执行。

###### 15.9.1导出与事务

进程的并行特性决定了导出操作往往不是原子操作。Sqoop会生成多个并行执行 的任务，分别导出数据的一部分。这些任务的完成时间各不相同，即使在每个任 务内部都使用事务，不同任务的执行'结果也不可能同时提交。此外，数据库系统 经常使用固定大小的缓冲区来存储事务数据，这使得一个任务中的所有操作不可 能在一个事务中完成。Sqoop每导入几千条记录便会执行一次提交，以确保不会 出现内存不足的情况。在导出操作进行过程中，提交过的中间结果都是可见的。 因此在导出过程完成前，不要启动那些要使用导出结果的应用程序，否则这些应 用会看到不完整的导出结果。

为了解决这个问题，Sqoop可以先将数据导出到一个临时阶段表中，然后在导出 任务完成前（即导出操作成功执行后），在一个事务中将临时阶段表中的数据全部移 动到目标表中。可以通过--staging-table选项来指定一个临时阶段表，这个临

时阶段表必须是已经存在的，并且和目标表具有相同的模式定义。如果不使用--

clear-staging-table选项，则这个临时阶段表必须是空表。

使用临时阶段表会降低执行速度，因为它需要写两次数据：先写入临时阶段 表，再写入目标表。整个导出过程运行所使用的空间也更多，因为从临时阶段 表向目标表导出时存在数据的两个复本。

###### 15.9.2 导出和 SequenceFile

之前的导出示例是从一个Hive表中读取源数据，该Hive表以分隔文本文件形式 保存在HDFS中。Sqoop也可以从非Hive表的分隔文本文件中导出数据。例如， Sqoop可以导出MapReduce作业结果的文本文件。

Sqoop还可以将存储在SequenceFile中的记录导出到输出表，不过有一些限 制。SequenceFile中不能保存任意类型的记录。Sqoop的导出工具从 SequenceFile中读取对象后直接发送到OutputCollector，由它将这些对象传 递给数据库导出OutputFormat，因此为了能让Sqoop使用，记录必须被保存在 SequenceFile键-值对格式的“值”部分，并且必须继承抽象类 com.cloudera.sqoop. lib.SqoopRecord（像 Sqoop 生成的所有类那样）。

如果基于导出目标表，使用codegen工具（sqoop-codegen）为记录生成一个 SqoopRecord的实现，那就可以写一个MapReduce程序，填充这个类的实例并将 它们写入SequenceFile,接着sqoop-export就可以将这些SequenceFile文 件导出到表中。还有另外一种方法，即将数据放入SqoopRecord实例中，然后保 存到SequenceFile中。如果数据是从数据库表导人HDFS的，那么在经过某种 形式的修改后，可以将结果保存在持有相同数据类型记录的SequenceFile中。

在这种情况下，Sqoop应当利用现有的类定义从SequenceFile中读取数据，而 不是像导出文本记录时所做的那样，为执行导出生成一个新的（临时的）记录容器 类。通过为Sqoop提供--class-name和--jar-file参数，可以禁止它生成代 码，而使用现有的记录类和jar包。在导出记录时，Sqoop将使用指定jar包中指 定的类。

在下面的例子中，我们将重新导入widgets表到SequenceFile中，然后再将其 导出到另外一个数据库表：

% sqoop import --connect jdbc:mysql://localhost/hadoopguide \

\>--table widgets -ml --class-name WidgetHolder --as-sequencefile \

\>--target-dir widget_sequence一files --bindir .

14/10/29 12:25:03 INFO mapreduce.ImportDobBase: Retrieved 3 records.

% mysql hadoopguide

mysql>CREATE TABLE widgets2(id INT, widget一name VARCHAR(100),

->price DOUBLE, designed DATE, version INT, notes VARCHAR(200));

Query OK, 0 rows affected (0.03 sec)

mysql> exit;

% sqoop export --connect jdbc:mysql://localhost/hadoopguide \ >--table widgets2 -ml --class-name WidgetHolder \

\>--jar-file WidgetHolder.jar --export-dir widget_sequence一files

14/10/29 12:28:17 INFO mapreduce.ExportJobBase: Exported 3 records.

在导入过程中，我们指定使用SequenceFile格式，并且将JAR文件放入当前目 录（使用--bindir）,这样将来便可以重用它，否则它将会被保存在一个临时目录 中。然后我们创建一个用于导出的目标表，该表在模式上稍有不同（但与源数据兼 容）。最后，运行导出操作，使用现有的生成代码从SequenceFile中读取记录并 将它们写入数据库。

关于 Sqoop 419

##### 15.10延伸阅读

有关Sqoop的更多使用信息，请参阅O’Reilly在2013年出版的Apache Sqoop Cookbook ([http://shop.oreilly.com/product/0636920029519.do)](http://shop.oreilly.com/product/0636920029519.do),%e4%bd%9c%e8%80%85)[,作者](http://shop.oreilly.com/product/0636920029519.do),%e4%bd%9c%e8%80%85) Kathleen Ting 和 JarekJarcec Cechoo
