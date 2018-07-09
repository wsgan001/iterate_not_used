### MapReduce应用开发

在第2章中，我们介绍了 MapReduce模型。本章中，我们从实现层面介绍在 Hadoop中开发MapReduce应用程序。

MapReduce编程遵循一个特定的流程。首先写map函数和reduce函数，最好使用 单元测试来确保函数的运行符合预期。然后，写一个驱动程序来运行作业，看这 个驱动程序是否可以正确运行，可以先从本地IDE中用一个小的数据集来运行 它。如果驱动程序不能正确运行，就用本地IDE调试器来找出问题根源。根据这 些调试信息，可以通过扩展单元测试来覆盖这一测试用例，从而改进mapper或 reducer,使其能正确处理类似输入。

一旦程序按预期通过小型数据集的测试，就可以考虑把它放到集群上运行了。当 运行程序对整个数据集进行测试的时候，可能会暴露更多的问题，这些问题可以 像前面一样修复，即通过扩展测试用例及修改mapper或reducer函数的方式来应 对新情况。在集群中调试程序很具有挑战性，我们来看一些常用的技术使其变得 更简单一些。

程序可以正确运行之后，如果想进行一些优化调整，首先需要执行一些标准检 查，借此加快MapReduce程序的运行速度，然后再做任务剖析(task profiling)。分 布式程序的分析并不简单，Hadoop提供了钩子(hook)来辅助这个分析过程。

然而在开始写MapReduce程序之前，仍然需要设置和配置开发环境。为此，我们 需要先学习如何配置Hadoop。

##### 6.1用于配置的API

Hadoop中的组件是通过Hadoop自己的配置API来配置的。一个Configuration类的实例 （可以在org.apache.hadoop.conf包中找到）代表配置属性及其取值的一个集 合。每个属性由一个String来命名，而值的类型可以是多种类型之一，包括 Java基本类型（如boolean、int、long和float）,其他有用的类型（如 String、Class 和 java.io.File）及 String 集合0

Configuration从资源（即使用简单结构定义名值对的XML文件）中读取其属性 值。参见范例6-1。

范例6-1.—个简单的配置文件configuration-1.xml

<?xml version=H1.0n?>

〈configuration〉

<property>

<name>color</name>

<value>yellow</value>

<description>Color</description>

</property>

<property>

<name>size</name>

<value>10</value>

<description>Size</description>

</property>

<property>

<name>weight</name> <value>heavy</value> <final>true</final> <description>Weight</description〉

</property>

<property>

<name>size-weight</name> <value>${size}>${weight}</value> <description>Size and weight</description>

</property>

〈/configuration〉

假定一个Configuration位于configuration-1.xml文件中，我们可以通过如下代 码访问其属性：

Configuration conf = new Configuration(); conf.addResource("configuration-1.xml”)；

assertThat(conf.get("color"), is("yellow")); assertThat(conf.getlnt("size' 0), is(10)); assertThat(conf.get("breadth1', "wide"), is("wide"));

有这样几点需要注意：XML文件中不保存类型信息；取而代之的是属性在被读取 的时候，可以被解释为指定的类型；此外，get()方法允许为XML文件中没有定 义的属性指定默认值，正如这一代码中最后一行的breadth属性一样。

###### 6.1.1资源合并

使用多个资源文件来定义一个Configuration时，事情变得有趣了。在Hadoop 中，这用于分离(core-defau/t.xfn/文件内部定义的)系统默认属性与文 件中定义的)位置相关(site-specific)的覆盖属性。范例6-2中的文件定义了 size属 性和weight属性。

范例6-2.第二个配置文件configuration-2.xml

<?xml version=n1.0n?>

〈configuration〉

<property>

<name>size</name>

<value>12</value>

</property> <property>

<name>weight</name>

<value>light</value>

</property>

〈/configuration〉

资源文件按顺序添加到Configuration：

Configuration conf = new Configuration(); conf.addResource("configuration-1•xml”)； conf.addResource(Mconfiguration-2.xml");

后来添加到资源文件的属性会覆盖(override)之前定义的属性。所以，size属性 的取值来自于第二个配置文件configuration-2.xml:

assertThat(conf.getInt(Hsize", 0), is(12));

不过，被标记为final的属性不能被后面的定义所覆盖。在第一个配置文件中， weight属性的final状态是true,因此，第二个配置文件中的覆盖设置失败， weight取值仍然是第一个配置文件中的heavy：

assertThat(conf•get ("weight"), is("heavy"));

试图覆盖final属性通常意味着配置错误，所以最后会弹出警告消息来帮助进行 故障诊断。一般来说，管理员将守护进程站点中的属性标记为final,表明他们 不希望用户在客户端的配置文件或作业提交参数(job submission parameter)中有任 何改动。

###### 6.1.2变量扩展

配置属性可以用其他属性或系统属性进行定义。例如，在第一个配置文件中的 size-weight属性可以定义*${size)^t]${weight}，而且这些属性是用配置文 件中的值来扩展的：

assertThat(conf.get(Msize-weight"), is("12,heavy"));

系统属性的优先级高于资源文件中定义的属性：

System.setProperty(Hsize""14");

assertThat(conf.get("size-weight"), is("14,heavy"));

该特性特别适用于在命令行方式下用JVM参数-Dproperty=vaLL/e来覆盖属性 注意，虽然配置属性可以通过系统属性来定义，但除非系统属性使用配置属性重 新定义，否则，它们是无法通过配置API进行访问的。因此：

System.setProperty("length", "2");

assertThat(conf.get("length"), is((String) null));

##### 6.2配置开发环境

首先新建一个项目，以便编译MapReduce程序并通过命令行或在自己的IDE中以 本地(独立，standalone)模式运行它们。在范例6-3中的Maven POM项目对象模型 (Project Object Model)说明了编译和测试Map-Reduce程序时需要的依赖项 (dependency)。

范例6-3.编译和测试MapReduce应用的Maven POM

<project>

<modelVersion>4.0>0</modelVersion>

<groupld>com.hadoopbook</groupld>

<artifactld>hadoop-book-mr-dev</artifactld>

<version>4.0</version>

〈properties〉

<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

<hadoop.version>2.5.l</hadoop.version>

</properties>

〈dependencies〉

</-- Hadoop main client artifact -->

<dependency>

<groupld>org.apache.hadoop</groupld>

<artifactld>hadoop-client</artifactld>

<version>${hadoop.version}</version>

"dependency〉

</-- Unit test artifacts -->

<dependency>

<groupld>junit</groupld>

<artifactld>junit</artifactld>

<version>4.11</version>

<scope>test</scope>

〈/dependency〉

<dependency>

<groupld>org.apache.mrunit</groupld>

<artifactld>mrunit</artifactld>

<version>1.1.0</version>

<classifier>hadoop2</classifier>

<scope>test</scope>

"dependency〉

</-- Hadoop test artifact for running mini clusters --> <dependency>

<groupld>org.apache.hadoop</groupld> <artifactld>hadoop-minicluster</artifactld> <version>${hadoop.version}</version>

<scope>test</scope>

</dependency>

</dependencies>

<build>

<finalName>hadoop-examples</finalName>

<plugins>

<plugin>

<groupld>org.apache•maven.plugins</groupld> <artifactld>maven-compiler-plugin</artifactld> <version>3.l</version>

〈configuration〉

<source>1.6</source>

<target>l>6</target>

〈/configuration〉

</plugin>

<plugin>

<groupld>org.apache.maven.plugins</groupld>

<artifactld>maven-jar-plugin</artifactld>

<version>2.5</version>

〈configuration〉

<outputDirectory>${basedir}</outputDirectory>

"configuration〉

</plugin>

</plugins>

</build>

</project>

依赖关系是POM中有趣的一部分。（只要你使用此处定义的依赖关系，就可以直 接使用其他的构建工具，例如Gradle或者Ant with Ivy。）要想构建MapReduce作 业，你只需要有hadoop-client依赖关系，它包含了和HDFS及MapReduce交 互所需要的所有Hadoop client-side类。当运行单元测试时，我们要使用junit类； 当写MapReduce测试用例时，我们使用mrunit类。hadoop-minicluster库中 包含了 “mini-”集群，这有助于在一个单JVM中运行Hadcx）p集群进行测试。

很多IDE可以直接读Maven POM,因此你只需要在包含pow.xw/文件的目录中指 向这些Maven POM,就可以开始写代码。也可以使用Maven为IDE生成配置文

件。例如，如下创建Eclipse配置文件以便将项目导入Eclipse:

%

###### 6.2.1



mvn eclipse:eclipse



-DdownloadSources=true -DdownloacOavadocs二true



###### 管理配置



开发Hadoop应用时，经常需要在本地运行和集群运行之间进行切换。事实上，可 能在几个集群上工作，也可能在本地“伪分布式”集群上测试。伪分布式集群是 其守护进程运行在本机的集群，这种运行模式的配置请参见附录A。

应对这些变化的一种方法是使Hadoop配置文件包含每个集群的连接设置，并且在 运行Hadoop应用或工具时指定使用哪一个连接设置。最好的做法是，把这些文件 放在Hadoop安装目录树之外，以便于轻松地在Hadoop不同版本之间进行切换， 从而避免重复或丢失设置信息。

为了方便本书的介绍，我们假设目录conf包含三个配置文件：hadoop-local.xml, hadoop-localhost.xml和这些文件在本书的范例代码里）。注意， 文件名没有特殊要求，这样命名只是为了方便打包配置的设置。（将此与附录A的 表A-1进行对比，后者存放的是对应服务器端的配置信息。） 针对默认的文件系统和用于运行MapReduce作业的本地（指在JVM中的）框架， hadoop-local.xm\ 包含默认的 Hadoop 配置：

<?xml version="1.0"?>

〈configuration〉

<property>

<name>fs.defaultFS</name>

<value>file:///</value>

</property>

<property>

<name>mapreduce.framework.name</name>

<value>local</value>

</property>

〈/configuration〉

hadoop-localhost.xml文件中的设置指向本地主机上运行的namenode和YARN资 源管理器：

<?xml version="1.0u?>

〈configuration〉

<property>

<name>fs.defaultFS</name>

<value>hdfs://localhost/</value>

</property>

<property>

<name>mapreduce.framework.name</name>

<value>yarn</value>

</property>

<property>

<name>yarn.resourcemanager.address</name>

<value>localhost:8032</value>

</property>

〈/configuration〉

最后，hadoop-cluster.xml文件包含集群上namenode和YARN资源管理器地址的

详细信息（事实上，我们会以集群的名称来命名这个文件，而不是这里显示的那样 用cluster泛指）：

<?xml version=n1.0H?>

〈configuration〉

<property>

<name>fs.defaultFS</name>

<value>hdfs://namenode/</value>

</property>

<property>

<name>mapreduce.framework.name</name>

<value>yarn</value>

</property>

<property>

<name>yarn.resourcemanager.address</name>

<value>resourcemanager:8032</value>

</property>

〈/configuration〉

还可以根据需要为这些文件添加其他配置信息。

设置用户标识

在HDFS中，可以通过在客户端系统上运行whoami命令来确定Hadoop用户 标识(identity)。类似，组名(group name)来自groups命令的输出。

如果Hadoop用户标识不同于客户机上的用户账号，可以通过设置 HADOOP_USER_NAME环境变量来显式设定Hadoop用户名。还可以通过 hadoop.user.group.static.mapping.overrides 配置属性来覆盖用户组映 射关系。例如，dr.who=;preston=directors, inventors,表示用户 dr.who 不在组中，而用户preston在directors和inventors组中。

可以通过设置hadoop.http.staticuser.user属性来设置Hadoop网络接口 运行时的用户标识。在默认情况下，这个用户标识是dr.who,但不是超级用 户，因此，不能通过该网络接口访问系统文件。

注意，在默认情况下，系统没有认证机制。10.4节介绍了如何在Hadoop中使 用Kerberos认证。

有了这些设置，便可以轻松通过-conf命令行开关来使用各种配置。例如，下面 的命令显示了一个在伪分布式模式下运行于本地主机上的HDFS服务器上的目录列 表：

% hadoop fs -conf conf/hadoop-localhost•xml -Is .

Found 2 items

drwxr-xr-x - tom supergroup 0 2014-09-08 10:19 input drwxr-xr-x - tom supergroup 0 2014-09-08 10:19 output

如果省略-conf选项，可以从$HADOOP_HOME的etc/hadoop子目录中找到Hadoop 的配置信息。或者如果已经设置了 HADOOP_CONF_DIR，Hadoop的配置信息将从那 个位置读取。

![img](Hadoop43010757_2cdb48_2d8748-88.jpg)



这里介绍另一种管理配覽设置的方法。将etc/hadoop目录从Hadoop的安装位 置拷贝至另一个位置，配置文件也放于该位置(文件中的各项设置 应正确)，再将HADOOP_CONF_DIR环境变量设置为指向该位置。该方法的主要 优点是，不需要每个命令中都指定-conf。并且，由于HADOOP_CONF_DIR路 径下有所有配置文件的拷贝，因此，对文件的修改可以与Hadoop XML配置文 件隔离开来(例如，log4j.properties)0详细介绍可以参见10.3节。

Hadoop自带的工具支持-conf选项，也可以直接用程序(例如运行MapReduce作 业的程序)通过使用Tool接口来支持-conf选项。

###### 6.2.2 辅助类 GenericOptionsParser, Tool 和 ToolRunner

为了简化命令行方式运行作业，Hadoop自带了一些辅助类。GenericOptionsParser 是一个类，用来解释常用的Hadoop命令行选项，并根据需要，为Configuration对 象设置相应的取值。通常不直接使用GenericOptionsParser。更方便的方式是实现 Tool接口，通过ToolRunner来运行应用程序。ToolRunner内部调用 GenericOptionsParser：

public interface Tool extends Configurable { int run(String [] args) throws Exception;

}

范例6-4给出了一个非常简单的Tool的实现，用来打印Tool的Configuration 对象中所有属性的键-值对。

范例6-4. Tool实现用于打印一个Configuration对象的属性的范例

public class ConfigurationPrinter extends Configured implements Tool { static {

Configuration.addDefaultResource(nhdfs-default.xmlH);

Configuration.addDefaultResource卜hdfs-site.xml");

Configuration.addDefaultResource卜yarn-default•xmln);

Configuration•addDefaultResource("yarn-site•xml");

Configuration.addDefaultResource卜mapred-default•xml”)；Configuration.addDefaultResource(nmapred-site.xml");

}

^Override

public int run(String[] args) throws Exception {

Configuration conf = getConf();

for (Entry<StringJ String〉 entry: conf) {

System.out.printf("%s=%s\n, entry.getKey(), entry.getValue());

}

return 0;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new ConfigurationPrinter()args); System.exit(exitCode);

}

}

我们把 ConfigurationPrinter 作为 Configured 的一个子类，Configured 是 Configurable接口的一个实现。Tool的所有实现都需要实现Configurable(

为Tool继承于Configurable), Configured子类通常是一种最简单的实现方式。 run()方法通过 Configurable 的 getConf()方法获取 Configuration,然后重复

执行，将每个属性打印到标准输出。

静态代码部分确保核心配置外的HDFS、YARN和MapReduce配置能够被获取（因 为Configuration已经获取了核心配置）。

可以设置哪些属性?

作为一个有用的工具，ConfigurationPrinter可用于了解在环境中某个属 性是如何进行设置的。对于运行中的守护进程，例如namenode，可以通过查 看其网络服务器上的/c⑽/页面来了解配置情况。

你也可以在Hadoop安装路径的share/doc目录中，查看所有公共属性的默认设 置，相关文件包括 core-default.xml, hdfs-default.xml, yam»default.xml 和 mapred-default.xml这几个文件。每个属性都有用来解释属性作用和取值范围的描述。

默认的配置文档可通过以下网址所链接的页面找到，http://hadoop.apache.oi^/ docs/current/（在导航树中寻找“Configuration”标题）。通过用＜\^以011〉替换前 述URL中的current,可以找到一个特定Hadoop发行版本的默认配置，例 d（口，<http://hadoop>. apache, org/docs/ r2.5.0/o

注意，在客户端配置中设置某些属性，将不会产生影响。例如，如果在作业提

交时想通过设置yarn.nodemanager. resource.memory-mb来改变运行作业

的节点管理器能够得到的内存数量，结果会让你失望的，因为这个属性只能在

节点管理器的yarn-site.xml文件中进行设置。一般情况下，我们可以通过属性 名的组成部分来获知该属性应该在哪里进行设置。由于

yarn.nodemanager.resource.memory-mb 以 yarn.nodemanager 开头，

们知道它只能为节点管理器守护进程进行设置。但是，这不是硬性的，在有些

情况下，我们需要进行尝试，甚至去阅读源代码。

在Hadoop 2中，为了让命名结构看着更常规一些，对配置属性名称进行了 修改。例如，namenode相关的HDFS属性名称都改为带一个dfs.namenode前 缀，这样原先的dfs.name.dir现在称为dfs.namenode. name.dir。类似的， M叩Reduce属性用mapreduce前缀代替了较早的mapped前缀，因此，原先的 mapred .job. name 现在称为 mapreduce .job. name。

本书使用了新属性名，以避免弃用警告。然而，旧属性名仍然有效，通常会在 旧版本的文档被引用。可以在Hadoop网站上找到一张关于弃用属性名及替代 名的列 l（<http://bit>. ly/deprecated_props）。

本书讨论了 Hadoop很多重要的配置属性。

ConfigurationPrinter的main()方法没有直接调用自身的run()方法，而是调 用ToolRunner的静态run()方法，该方法负责在调用自身的run()方法之前， 为 Tool 建立一个 Configuration 对象。ToolRunner 还使用了 GenericOptionsParser来获取在命令行方式中指定所有标准的选项，然后，在 Configuration实例上进行设置。运行下列代码，可以看到在conf/hadoop-localhost.xml中设置的属性。

% mvn compile

% export HADOOP_CLASSPATH: target/classes

% hadoop ConfigurationPrinter -conf conf/hadoop-localhost.xml \

| grep yarn•resourcemanager.address:

yarn.resourcemanager.address=localhost:8032

GenericOptionsParser也允许设置个别属性。例如：

% hadoop ConfigurationPrinter -D color=yellow | grep color

color=yellow

-D选项用于将键color的配置属性值设置为yellow。设置为-D的选项优先级要 高于配置文件里的其他属性。这一点很有用：可以把默认属性放入配置文件中， 然后再在需要时用-D选项来覆盖。一个常见的例子是，通过-D mapreduce. job.reduces=n 来设置 MapReduce 作业中 reducer 的数量。这样会覆 盖集群上或客户端配置属性文件中设置的reducer数量。

GenericOptionsParser和ToolRunner支持的其他选项可以参见表6-1。 Hadoop用于配置的更多API可以在6.1节中找到。

用-D property=\/aLue 选项将 Hadoop 属性设为 GenericOptionsParser (和 ToolRunner)，不同于用 Java 命令-Dpro/?erty=va/.ue 选项对 JVM 系统属

性进行设置。JVM系统属性的语法不允许D和属性名之间有任何空格，而 'GenericOptionsParser'则允许用空格。

JVM系统属性来自干javaJang.System类，而Hadoop属性只能从Configuration 对象中获取。所以，即使已经设置了系统属性color(通过HADOOP_OPTS)，下 面的命令行也不会有任何输出，因为ConfigurationPrinter没有使用 System 类：

% HADOOP_OPTS= *-Dcolor=yellow' \ hadoop ConfigurationPrinter | grep color

如果希望通过系统属性进行配置，则需要在配置文件中反映相关的系统属性。 具体讨论请参见6.1.2节。

表 6-1. GenericOptionsParser 选项和 ToolRunner 选项

，辦紙呀•胡;怼々乂.我卞;’，：.V^'-7:"，/，吻“ •:对/ 安7^>看今）,..《:乂 '•脚    • "<i-{! ^'/•'{' \t.'!' '■ v*v V "•* •； ,,tZr'";' '•- '**>|'. Jt"    ,）'<■' ? '.：■.

选项名称    描述

-D property=value    将指定值赋值给某个Hadoop配置属性。覆盖配置文件里的默认属性或站

点属性，或通过-conf选项设置的任何属性

| -conf filename …             | 将指定文件添加到配置的资源列表中。这是设置站点属性或同时设置一 组属性的简便方法 |
| ---------------------------- | ------------------------------------------------------------ |
| -fs uri                      | 用指定的URI设置默1 人文件系统。这是-D fs.default.FS=uri的快捷方式 |
| -jt host:port                | 用指定主机和端口设置YARN资源管理器。（在Hadoop 1中，该选项用 于设置jobtracker地址，因此沿用了该名称）这是-D yarn. resourcemanager. address= host: port 的快捷方式 |
| -files filel^ file2”.        | 从本地文件系统（或任何指定模式的文件系统）中复制指定文件到 MapReduce所用的共享文件系统（通常是HDFS）,确保在任务工作目录 的MapReduce程序可以访问这些文件，要想进一步了解复制文件到集群 机器的分布式缓存机制，请参见9.4.2节 |
| -archivesarchivel^archive2”. | 从本地文件系统（或任何指定模式的文件系统）复制指定存档到MapReduce 所用的共享文件系统（通常是HDFS）,打开存档文件，确保任务工作目录的 MapReduce程序可以访问这些存档 |
| -libjars jarl^ jar2,…        | 从本地文件系统（或任何指定模式的文件系统）复制指定JAR文件到被 MapReduce使用的共享文件系统（通常是HDFS），把它们加入 MapReduce任务的类路径中。这个选项适用于传输作业需要的JAR文件 |

##### 6.3用MRUnit来写单元测试

在MapReduce中，map函数和reduce函数的独立测试非常方便，这是由函数风格 决定的。MRUnit（⑽?.•//wn^7.叩crc/ze.org/）是一个测试库，它便于将已知的输入传 递给mapper或者检査reducer的输出是否符合预期。MRUnit与标准的测试执行框 架（如JUnit）—起使用，因此可以在正常的开发环境中运行MapReduce作业的测 试。例如，这里描述的所有测试都可根据6.2节介绍的指令在IDE中运行b

###### 6.3.1 关于 Mapper

參

范例6-5是一个mapper的测试。

范例 6-5. MaxTemperatureMapper 的单元测试

import java.io.IOException;

import org.apache.hadoop.io.*;

import org.apache.hadoop.mrunit.mapreduce.MapDriver;

import org.junit

•public class MaxTemperatureMapperTest {

@Test

public void processesValidRecord() throws IOException^InterruptedException {

Text value = new Text(,,0043011990999991950051518004+68750+023550FM-12+0382" +

// Year AAAA

"99999V0203201N00261220001CN9999999N9-00111+9999999999911);

// Temperature AAAAA

new MapDriver<LongWritableJ Text, Text, IntWritable>()

.withMapper(new MaxTemperatureMapper())

.withlnput(new LongWritable(0), value)

•withOutput(new Text(“1950”), new IntWritable(-ll))

.runTest();

}

}

测试很简单：传递一个天气记录作为mapper的输入，然后检査输出是否是读入的 年份和气温。

由于测试的是mapper,所以可以使用MRUnit的MapDriver.在调用runTest()方法执 行这个测试之前，在test(MaxTemperatureMapper)下配置mapper、输入key和 值、期望的输出key(1950,表示年份的Text对象)和期望的输出值，表示 温度的IntWritable)。如果mapper没有输出期望的值，MRUnit测试失败。注意， 由于mapper忽略输入key，因此，输入key可以设置为任何值。

在测试驱动的方式下，范例6-6创建了一个能够通过测试的Mapper实现。由于本

章要进行类的扩展，所以每个类被放在包含版本信息的不同包中。例如，

vl.MaxTemperatureMapper 是 MaxTemperatureMapper 的第一个版本。当然，

不重新打包实际上也可以对类进行扩展。

范例6-6.通过了 MaxTemperatureMapper测试的第一个版本Mapper函数

public class MaxTemperatureMapper extends Mapper<LongWritableJ Text, Text, IntWritable〉 {

^Override

public void map(LongWritable key, Text value. Context context) throws IOException^ InterruptedException {

String line = value.toString();

String year = line.substring(15, 19);

int airTemperature = Integer.parselnt(line.substring(87^ 92)); context.write(new Text(year), new IntWritable(airTemperature));

}

}

这是一个非常简单的实现，从行中抽出年份和气温，并将它们写到Context中。现 在，让我们增加一个缺失值的测试，该值在原始数据中表示气温+9999:

@Test

public void ignoresMissingTemperatureRecord() throws IOException { InterruptedException {

Text value = new Text("004301199的99991950051518004+68750+023550FM-12+0382" +

// Year AAAA

"99999V0203201N00261220001CN9999999N9+99991+99999999999");

// Temperature AAAAA

new MapDriver<LongWritable, Text, Text, IntWritable>()

.withMapper(new MaxTemperatureMapper())

.withlnput(new LongWritable(0), value)

.nunTest();

}

根据withOutput()被调用的次数，MapDriver能用来检查0、1或多个输出记 录。在这个测试中由于缺失温度的记录已经被过滤掉，该测试保证对于这种特定 的输入值不产生任何输出。

由于没有考虑到+9999这样一种特殊情况，新测试以失败告终。与其在mapper中 加入更多的逻辑考虑，还不如给出一个解析类来封装解析逻辑更有意义。详情可 以参见范例6-7。

范例6-7.该类解析NCDC格式的气温记录

public class NcdcRecordParser { private static final int MISSINGJTEMPERATURE = 9999;

private String year; private int airTemperature; private String quality;

public void parse(String record) { year = recorcLsubstring(15， 19);

String airTemperatureString;

// Remove Leading pLus sign as parselnt doesn't Like them (pre-Java 7) if (record.charAt(87) == •+•) {

airTemperatureString = record.substring(88> 92);

} else {

airTemperatureString = record.substring(87^ 92);

}

airTemperature = Integer.parselnt(airTemperatureString); quality = record.substring^' 93);

}

public void parse(Text record) { parse(record.toString());

}

public boolean isValidTemperature() {

return airTemperature != MISSIN6_TEMPERATURE && quality.matches("[01459]");

}

public String getYear() { return year;

}

public int getAirTemperature() { return airTemperature;

}

}

最终的mapper(第二个版本)相当简单(见范例6-8)。它只需要调用解析类的parse() 方法，就能对输人行中感兴趣的字段进行解析，用isValidTemperature()査询方法可以检 查是否是合法气温，如果是，则使用解析类的获取方法得到年份和气温值。需要注崑 是，我们在isValidTemperature()中检查是否有遗漏气温值的同时，也对质量状态字 段进行检查，以便过滤掉不准确的气温读取值。

创建解析类的另一个好处是：相似作业的mapper不需要重写代码。并且，对 ■k 于更多的有针对性的测试而言，该方法也提供了一个机会，即可以直接针对解

猶编钟元测试。

范例6-8.这个mapper使用utility类来解析记录

public class MaxTemperatureMapper

extends MapperxLongWritable, Text, Text, IntWritable> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void map(LongWritable key, Text value. Context context) throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

context.write(new Text(parser.getYear()),

new IntWritable(parser.getAirTemperature()));

}

}

}

这个对mapper的测试通过后，我们接下来写reducer。

###### 6.3.2 关于 Reducer

reducer必须找出指定键的最大值。这是针对此特性的一个简单的测试，其中使用了

—^个 ReduceDriver0 @Test

public void returnsMaximumlntegerlnValuesO throws IOException, InterruptedException {

new ReduceDriver<Text1 IntWritable, Text, IntWritable>()

.withReducer(new MaxTemperatureReducer())

•withlnputKey(new Text("1950"))

Arrays.asList(new IntWritable(10), new IntWritable(5)))

.withOutput(new Text("1950"), new IntWritable(lO))

.runTest();

}

我们对一些IntWritable值构建-一个迭代器来验证MaxTemperatureReducer能找到最大 值。范例6-9里的代码是一个通过测试的MaxTemperatureReducer的实现。

范例6-9.用来计算最高气温的reducer

public class MaxTemperatureReducer extends Reducer<TextJ IntWritable^ Text, IntWritable〉 {

^Override

public void reduce(Text key, Iterable<IntWritable> values,

Context context)

throws IOException, InterruptedException {

int maxValue = Integer.MIN^VALUE; for (IntWritable value : values) {

maxValue = Math.maxCmaxValuG^ value.get());

}

context.write(key, new IntWritable(maxValue));

}

}

##### 6.4本地运行测试数据

现在mapper和reducer已经能够在受控的输入上进行工作了，下一步是写一个作 业驱动程序(job driver),然后在开发机器上使用测试数据运行它。

###### 6.4.1在本地作业运行器上运行作业

通过使用前面介绍的Tool接口，可以轻松写一个MapReducer作业的驱动程序，

用它来计算按照年度査找最高气温，参见范例6-10的MaxTemperatureDriver0

范例6-10.查找最高气温

public class MaxTemperatureDriver extends Configured implements Tool {

^Override

public int run(String[] args) throws Exception { if (args.length != 2) {

System.err•printf("Usage: %s [generic options] <input> <output>\n">getClass()•getSimpleName());

ToolRunner.printGenericCommandUsage(System.err); return -1;

}

Job job = new〕ob(getConf(), ’.Max temperature"); job.set3arByClass(getClass());

FilelnputFormat.addInputPath(job, new Path(args[0])); FileOutputFormat.setOutputPath(job, new Path(args[l]));

job.setMapperClass(MaxTemperatureMapper.class); job>setCombinerClass(MaxTemperatureReducer.class); job.setReducerClass(MaxTemperatureReducer.class);

job.setOutputKeyClass(Text.class);

job，setOutputValueClass(IntWritable.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new MaxTemperatureDriver()args); System•exit(exitCode);

}

}

MaxTemperatureDriver 实现了 Tool 接口，所以，我们能够设置 GenericOptionsParser

支持的选项。run()方法根据工具的配置创建一个］ob对象来启动一个作业。在 所有可能的作业配置参数中，可以设置输入和输出文件路径，mapper、reducer 和combiner以及输出类型(输入类型由输入格式决定，默认为TextlnputFormat,

包括Long Writable键和Text值)。为作业设置一个名称(Max temperature)

也是很好的做法，这样可以在执行过程中或作业完成后方便地从作业列表中查找 作业。默认情况下，作业名称是JAR文件名，通常情况下没有特殊的描述。

现在我们可以在一些本地文件上运行这个应用。Hadoop有一个本地作业运行器 (job runner),它是在MapReduce执行引擎运行单个JVM上的MapReduce作业的 简化版本。它是为测试而设计的，在IDE中使用起来非常方便，因为我们可以在

调试器中单步运行mapper和reducer代码。

如果mapreduce.framework.name被设置为local,则使用本地作业运行器， 这也是默认情况'

可以在命令行方式下输入如下命令来运行驱动程序：

% mvn compile

% export HADOOP_CLASSPATH=target/classes/

% hadoop v2.MaxTemperatureDriver -conf conf/hadoop-local.xml \ input/ncdc/micro output

类似地，可以使用GenericOptionsParser提供的-fs和-jt选项：

% hadoop v2.MaxTemperatureDriver -fs <file:///> -jt local \ input/ncdc/micro output

这条指令使用本地input/ncdc/micro目录作为输入来执行MaxTemperatureDriver,产生的输 出存放在本地⑽妒⑽目录中。注意：虽然我们设置了-fs，可以使用本地文件系统 ([file:///),](file:///),但本地作业运行器实际上可以在包括HDFS在内的任何文件系统上)[但本地作业运行器实际上可以在包括HDFS在内的任何文件系统上](file:///),但本地作业运行器实际上可以在包括HDFS在内的任何文件系统上) 正常工作(如果HDFS里有一些文件，可以马上进行尝试)。

可以如下检查本地文件系统上的输出：

% cat output/part-p-00000

1949    111

1950    22

###### 6.4.2测试驱动程序

參

除了灵活的选项可以使应用程序实现Tool，还可以插入任意Configuration来增加可测 试性。可以利用这点来编写测试程序，它将利用本地作业运行器在已知的输人数 据上运行作业，借此来检查输出是否满足预期。

要实现这个目标，有两种方法。第一种方法是使用本地作业运行器，在本地文件 系统的测试文件上运行作业。范例6-11的代码给出了一种思路。

范例6-11.这个MaxTemperatureDriver测试使用了一个正在运行的本地作业运行器

@Test

public void test() throws Exception {

①在Hadoop 1中，mapred. job.tracker的值决定了执行的方式：值为local时，表示本地作 业运行器；值为冒号分隔开的主机端口对时，表示一个jobtracker地址。

Configuration conf = new Configuration);

conf.set("fs.defaultFS", "<file:///>");

conf.set("mapreduce.framework.name.、"local•.);

conf.setInt("mapreduce.task.io.sort • mb", 1);

Path input = new Path( ,,input/ncdc/microH);

Path output = new Path("output");

FileSystem fs = FileSystem.getLocal(conf);

fs.deleteCoutput^ true); // delete old output

MaxTemperatureDriver driver = new MaxTemperatureDriver(); driver.setConf(conf);

int exitCode = driver.run(new String[] { input.toStringO^ output.toString() });

assertThat(exitCodeis(0));

checkOutput(confoutput);

}

测试代码明确没置了 fs.defaultFS 和 mapreduce.framework.name 所以，它

使用的是本地文件系统和本地作业运行器。随后，通过其Tool接口在少数已知数 据上运行MaxTemperatureDriver。最后，checkOutput()方法调用以逐行对比 实际输出与预期输出。

测试驱动程序的第二种方法是使用一个mini集群来运行它。Hadoop有一组测试 类，名为 MiniDFSCluster、MiniMRCluster 和 MiniYARNCluster，它以程序方式创

建正在运行的集群。不同于本地作业运行器，它们允许在整个HDFS、MapReduce 和YARN机器上运行测试。注意，mini集群上的节点管理器启动不同的JVM来 运行任务，这会使调试更困难。

![img](Hadoop43010757_2cdb48_2d8748-89.jpg)



也可以从命令行运行一个mini集群，如下所示：

% hadoop jar \

$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-*\ -tests.jar minicluster

mini集群广泛应用于Hadoop自带的自动测试包中，但也可以用于测试用户代 码。Hadoop的ClustenMapReduceTestCase抽象类提供了一个编写此类测试 的基础，它的setUp()和tearDown()方法可处理启动和停止运行中的HDFS 和YARN集群的细节，同时产生一个可以配置为一起工作的Configuration 对象。子类只需要得到HDFS中的数据(可能从本地文件中复制得到)，运行 MapReduce作业，然后确认输出是否满足要求。参见本书示例代码中的

MaxTemperatureDriverMiniTest 类0

这样的测试是回归测试，是一个非常有用的输入边界用例和相应的期望结果的资 源库。随着测试用例的增加，简单将其加入输入文件，然后更新相应的输出文件 即可。

##### 6.5在集群上运行

目前，程序已经可以在少量测试数据上正确运行，下面可以准备在Hadcx)p集群的 完整数据集上运行了。第10章将介绍如何建立完全分布的集群，同时，该章中的 方法也可以用在伪分布集群上。

###### 6.5.1打包作业

本地作业运行器使用单JVM运行一个作业，只要作业需要的所有类都在类路径 (classpath)上，那么作业就可以正常执行。

在分布式的环境中，情况稍微复杂一些。开始的时候作业的类必须打包成一个作 业JAR文件并发送给集群。Hadoop通过搜索驱动程序的类路径自动找到该作业 JAR文件，该类路径包含］obConf或］ob上的set］arByClass()方法中设置的类。另 一种方法，如果你想通过文件路径设置一个指定的JAR文件，可以使用set3ar() 方法。JAR文件路径可以是本地的，也可以是一个HDFS文件路径。

通过使用像Ant或Maven的构建工具可以方便地创建作业的JAR文件。当给定范 例6-3中所示的POM时，下面的Maven命令将在包含所有已编译的类的工程目录 中创建一个名为hadoop-examples.jar的JAR文件：

番

% mvn package -DskipTests

如果每个JAR文件都有一个作业，可以在JAR文件的manifest中指定要运行的主 类。如果主类不在manifest中，则必须在命令行指定。

任何有依赖关系的JAR文件应该打包到作业的JAR文件的///?子目录中。当然也

有其他的方法将依赖包含进来，这我们稍后会讨论。类似地，资源文件也可以打 包进一个c/owes子目录。这与Java Web application archive或WAR文件类似，只 不过JAR文件是放在WAR文件的WEB-INF/lib子百录丁，而类则是放在WAR文 件的WEB-INF/classes子目录中。

\1.    客户端的类路径

由hadoop jar ＜：/6^＞设置的用户客户端类路径包括以下几个组成部分：

•作业的JAR文件

•作业JAR文件的/A目录中的所有JAR文件以及目录（如果定义）

• HADOOPJZLASSPH定义的类路径（如果已经设置）

顺便说一下，这解释了如果你在没有作业JAR（hadoop CL455A/4MF）情况下使用本 地作业运行器时，为什么必须设置HADOOPJZLASSPATH来指明依赖类和库。

\2.    任务的类路径

在集群上（包括伪分布式模式），map和reduce任务在各自的JVM上运行，它们的 类路径不受HADOOP_CLASSPATH控制。HADOOP_CLASSPATH是一项客户端设置， 并只针对驱动程序的JVM的类路径进行设置。

反之，用户任务的类路径有以下几个部分组成：

•作业的JAR文件

•作业JAR文件的lib目录中包含的所有JAR文件以及classes目录（如 果存在的话）

MapReduce应用开发 161

•使用-libjars 选项（参见表 64）或 DistributedCache 的 addFileToClassPath（）方法（老版本的API）或］ob（新版本的API）添加到

分布式缓存的所有文件

\3.    打包依赖

给定这些不同的方法来控制客户端和类路径上的内容，也有相应的操作处理作业 的库依赖：

•将库解包和重新打包进作业JAR

•将作业JAR的//Z?目录中的库打包

•保持库与作业JAR分开，并且通过HADOOP_CLASSPATH将它们添加到 客户端的类路径，通过-libjars将它们添加到任务的类路径

从创建的角度来看，最后使用分布式缓存的选项是最简单的，因为依赖不需要在 作业的JAR中重新创建。同时，使用分布式缓存意味着在集群上更少的JAR文件 转移，因为文件可能缓存在任务间的一个节点上了。详情可参见9.4.2节。

4.任务类路径的优先权

用户的JAR文件被添加到客户端类路径和任务类路径的最后，如果Had（x）p使用 的库版本和你的代码使用的不同或不相容，在某些情况下可能会引发和Hadoop内

置库的依赖冲突。有时需要控制任务类路径的次序，这样你的类能够被先提取出 来。在客户端，可以通过设置环境变量HADOOP_USER_CLASSPATH_FIRST为true

强制使Hadoop将用户的类路径优先放到搜索顺序中。对于任务的类路径，你可以 将 mapreduce. job.user.classpath.first 设为 true。注意，设置这些选项就 改变了针对Hadoop框架依赖的类（但仅仅对你的作业而言），这可能会引起作业的 提交失败或者任务失败，因此请谨慎使用这些选项。

###### 6.5.2启动作业

纛

为了启动作业，我们需要运行驱动程序，使用-conf选项来指定想要运行作业的 集群（同样，也可以使用-fs和-jt选项）：

% unset HADOOP_CLASSPATH

% hadoop jar hadoop-examples.jar V2.MaxTemperatureDriver \

-conf conf/hadoop-cluster.xml input/ncdc/all max-temp

![img](Hadoop43010757_2cdb48_2d8748-90.jpg)



我们不设置HADOOPJZLASSPATH环境变量是因为对于该作业没有任何第三方依 赖。如果它被设置为/target/classes/（本章前面的内容），那么Hadoop将找不 到作业JAR , Hadoop会从target/classes而不是从JAR装载 MaxTempratureDriver类，从而导致作业失败。

]ob上的waitForCompletion（）方法启动作业并检查进展情况。如果有任何变 化，就输出一行map和reduce进度总结。输入如下（为了清楚起见，有些行特意删 除了）：

14/09/12 06:38:11 INFO input.FilelnputFormat: Total input paths to process : 101 14/09/12 06:38:11 INFO impl.YarnClientlmpl: Submitted application applicationJL410450250506_0003

14/09/12 06:38:12 INFO mapreduce.Dob: Running job: job_1410450250506_0003 14/09/12 06:38:26 INFO mapreduce.Dob: map 0% reduce 0%

14/09/12 06:45:24 INFO mapreduce.]ob: map 100% reduce 100%

14/09/12 06:45:24 INFO mapreduce.Dob: Dob job_1410450250506_0003 completed successfully

14/09/12 06:45:24 INFO mapreduce.Job: Counters: 49 File System Counters

FILE: Number of bytes read=93995

FILE: Number of bytes written=10273563 FILE: Number of read operations^

FILE:    Number    of    large    read operations=0

FILE:    Number    of    write    operations=0

HDFS:    Number    of    bytes    read=33485855415

HDFS:    Number    of    bytes    written=904

HDFS: Number of read operations=327 HDFS:    Number    of    large    read operations=0

HDFS:    Number    of    write    operations=16

Job Counters

Launched map tasks=101 Launched reduce tasks=8 Data-local map tasks=101

Total time spent by all maps in occupied slots (ms)=5954495 Total time spent by all reduces in occupied slots (ms)=74934 Total time spent by all map tasks (ms)=5954495

Total time spent by all reduce tasks (ms)=74934 Total vcore-seconds taken by all map tasks=5954495 Total vcore-seconds taken by all reduce tasks=74934 Total megabyte-seconds taken by all map tasks=6097402880 Total megabyte-seconds taken by all reduce tasks=76732416

Map-Reduce Framework

Map input records=1209901509 Map output records=1143764653 Map output bytes=10293881877 Map output materialized bytes=14193 Input split bytes=14140

籌

Combine input records=1143764772

Combine output records=234

Reduce input groups=100

Reduce shuffle bytes=14193

Reduce input records=115

Reduce output records=100

Spilled Records=379

Shuffled Maps =808

Failed Shuffles=0

Merged Map outputs=808

GC time elapsed (ms)=101080

CPU time spent (ms)=5113180

Physical memory (bytes) snapshot=60509106176

Virtual memory (bytes) snapshot=167657209856

Total committed heap usage (bytes)=68220878848

Shuffle Errors BAD_ID=0 CONNECTION=0 IO_ERROR=0 WRONG_LENGTH=0 WRONG_MAP=0 WRONG J^EDUCE=0

File Input Format Counters Bytes Read=33485841275

File Output Format Counters Bytes Written=90

输出包含很多有用的信息。在作业开始之前，打印作业ID;如果需要在日志文件 中或通过mapred job命令査询某个作业，必须要有ID信息。作业完成后，统计 信息（例如计数器）被打印出来。这对于确认作业是否完成是很有用的。例如，对于 这个作业，大约分析12亿条记录（“Map input records”），从HDFS读取了大 约34 GB压缩文件（“HDFS:Number of bytes”）。输入数据被分成101个大小 合适的gzipped文件，因此即使不能划分数据也没有问题。

关于计数器的更多介绍，可以参见9.1.1节

作业、任务和任务尝试ID

Hadoop 2中，MapReduce作业ID由YARN资源管理器创建的YARN应用ID 生成。一个应用ID的格式包含两部分：资源管理器（不是应用）开始时间和唯 一标识此应用的由资源管理器维护的增量计数器。例如：ID为 application__1410450250506_0003的应用是资源管理器运行的第三个应用 （0003，应用ID从1开始计数），时间戳1410450250506表示资源管理器开始 时间。计数器的数字前面由0开始，以便于ID在目录列表中进行排序。然 而，计数器达到10000时，不能重新设置，会导致应用ID更长（这些ID就不 能很好地排序了）。

将应用ID的application前綴替换为job前缀即可得到相应的作业ID,如 job_1410450250506_0003<,

任务属于作业，任务ID是这样形成的，将作业ID的job前缀替换为task前 缀，然后加上一个后缀表示是作业里的哪个任务。例如：task_1410450250506_ 0003_m_000003 表示 ID 为 job_1410450250506_0003 的作业的第 4 个 map 任务 （000003，任务ID从0开始计数）。作业的任务ID在作业初始化时产生，因此， 任务ID的顺序不必是任务执行的顺序。

由于失败（参见7.2节）或推测执行（参见7.4.2节），任务可以执行多次，所以， 为了标识任务执行的不同实例，任务尝试（task attempt）都会被指定一个唯一的 ID。例如：attempt_1410450250506_0003_m__000003_0 表示正在运行的 tas<1410450250506_0003_m_000003 任务的第一个尝试（0,任务尝试 ID

从0开始计数）。任务尝试在作业运行时根据需要分配，所以，它们的顺序代 表被创建运行的先后顺序。

###### 6.5.3 MapReduce 的 Web 界面

Hadoop的Web界面用来浏览作业信息，对于跟踪作业运行进度、查找作业完成后 的统计信息和日志非常有用。可以在•洲刈/找到用户 界面信息。

1.资源管理器页

图6-1展示了主页的截屏。“Cluster Metrics”部分给出了集群的概要信息，包括

当前集群上处于运行及其他不同状态的应用的数量，集群上可用的资源数量 (“Memory Total”)及节点管理器的相关信息。

tn at: dr.who

All Applications

\* Cluster

Hoslsi

Aoatlcations

NEW

N£W SAVING SUfiMILUQ ACCCPTEQ RUNNING



Cluster Metrlcd



Af>p» App6 Appa App«    Conu^nert    • Memory    Memory

Submitted    Penang    Running    Completed    Running Used Total    • Reserve:



Active

Reserve j    Nodes

3    0    1    2    16    16 Q8 16.82 QB OB    i

User Metrics for dr.who



App«    Appe    Apps    Apjw

Submmad    Pending    Running    Competed



Containers

Running



CacHtiners

Pwxlir^



Decommissioned Losi    unhealthy

Nodat    Nodes Nod««



Containers

Reserved



4«ftxxy    Memory

UMd    Pending



Rebooted

Nooes



PIMISHCD

EAUXC

KILL£D



SchedtJer

Tools



0    0    1    2    0    0    0    OB    0D    OB

IO



Memory

Reserved



UMr



Queue SUrtTlnw FirtshTlme

N/A



Apphcatior)

\*    Typo 令’ C ■，    z

apohcation 14lQ4SQ25OSQfl QQQ3 ec2* Max    MAPREDUCE ro<X.ec2- Frt. 12

user temperature    user Sep 2014

10:38.11 QMT

aoo»caflon 141Q4SQ2fiOSO6 0002 ec2. Max    MAPREDUCE root.6G2‘ Fn. 12 Fri, 12Sep FJNISHED SUCCEEDED

user t«mp«rature    user Sep 2014    20-4

10:27:23    10:34.36

QMT QMT

aponcaitoft 141Q45Q2SQ6Qfl 0001 ec2. dtmcp    MAPAEDUCE root.ec2- Fn, 12 Fri, 12 Sep FINISHED SUCCEEDED    HtttQfv

bMr    user Sop 2014 2014

0047:09    06:52 56

GMT QVT



Nam« ◊



FlnatQtatufi



3(«te c    M Progrea#

RUNNING UNDEFINED ;



TracHJnfl U»



:tiam







6-1.资源管理器页面的屏薄截

接下来的主表中列出了集群上所有曾经运行或正在运行的应用。有个搜索窗口可 以用于过滤寻找所感兴趣的应用。主视图中每页可以显示100个条目，资源管理 器在同一时刻能够在内存中保存近10 000个已完成的应用(通过设置 yarn, resourcemanager. max-completed-applications),随后只能通过作业

历史页面获取这些应用信息。注意，作业历史是永久存储的，因此也可以通过作 业历史找到资源管理器以前运行过的作业。

作业历史

作业历史指已完成的MapReduce作业的事件和配置信息。不管作业是否成功 执行，作业历史都将保存下来，为运行作业的用户提供有用信息。

作业历史文件由MapReduce的application master存放在HDFS中，通过 mapreduce.jobhistory.done-dir属性来设置存放目录。作业的历史文件

会保存一周，随后被系统删除。

历史日志包括作业、任务和尝试事件，所有这些信息以JSON格式存放在文件 中。特定作业的历史可以通过作业历史服务器的Web界面（通过资源、管理器页 面链接）查看，或在命令行方法下用mapped job -history（指向作业历史文 件中）查看。

\* i    •

\2. MapReduce作业页面

单击“Tracking UI”链接进入application master的web界面（如果应用已经完成则 进入历史页面）。在MapReduce中，将进入作业页面，如图6-2所示。

togged Io as: dr.who

MapReduce Job job一1410450250506一0003

| » Cluster ► Application        | •                            | » • •• • •    *，广 T • • . •    ~    «•赚猶Job Name: Max temperatureState: RUNNING |         |        |         |          | Jot) Overview |
| ------------------------------ | ---------------------------- | ------------------------------------------------------------ | ------- | ------ | ------- | -------- | ------------- |
| ，Job                          | • 1                          | Ubert^ed: false                                              |         |        |         |          |               |
| Overview                       |                              | Started: Fri Sep 12 06:3854 EOT 2014                         |         |        |         |          |               |
| CountersConfiauratfonMao tasks | 1    9Application Master     | Elapsed: Smins, 25sec                                        |         |        |         |          |               |
| Reduce tasks                   | Artompl Number               | Start                                                        |         |        |         | Node     | Logs          |
| 1                              | Frt Sep 12 06 38:19 EDT 2014 |                                                              |         | logs   |         |          |               |
| * Tools                        |                              |                                                              |         |        |         |          |               |
| Task Type                      | Progress    Totai            |                                                              | Pending |        | Running | Complete |               |
|                                | Mflfi                        | 101                                                          | 25      |        | 14      |          | 62•           |
|                                |                              | 6                                                            | 8       |        | 0       |          | 0             |
|                                | 1Atiempi Type                | Hew    Runrwnp                                               |         | failed |         | Kfiied   | Successfui    |
|                                | Maps                         |                                                              | Q       |        | Q       | 62       |               |
|                                | Reduces                      | 8 8                                                          | Q       |        | g       | fi       |               |

6-2.作业页面的屏幕截

作业运行期间，可以在作业页面监视作业进度。底部的表展示map和reduce进 度。“Total”显示该作业map和reduce的总数。其他列显示的是这些任务的状 态：Pending（等待运行）、Running（运行中）或Complete（成功完成）。

表下面的部分显示的是map或reduce任务中失败和被终止的任务尝试的总数。任

务尝试（task attempt）可标记为被终止，如果它们是推测执行的副本，或它们运行的 节点已结束，或它们已被用户终止。7.2.1节对任务失败进行了详细的讨论。

导航栏中还有许多有用的链接。例如，“Configuration”链接指向作业的统一配置 文件，该文件包含了作业运行过程中生效的所有属性及属性值。如果不确定某个 属性的设置值，可以通过该链接查看文件。

###### 6.5.4获取结果

一旦作业完成，有许多方法可以获取结果。每个reducer产生一个输出文件，E 此，在max-temp目录中会有30个部分文件（part file）,命名为part-00000 5lJ part 00029n

![img](Hadoop43010757_2cdb48_2d8748-91.jpg)



正如文件名所示，这些“part”文件可以认为是/TMX-Zewp文件的一部分。

如果输出文件很大（本例不是这种情况），那么把文件分为多个part文件很重 要，这样才能使多个reducer并行工作。通常情况下，如果文件采用这种分割 形式，使用起来仍然很方便：例如作为另一个MapReduce作业的输入。在某些 情况下，可以探索多个分割文件的结构来进行map端连接操作（参见8.3.1节）。

这个作业产生的输出很少，所以很容易从HDFS中将其复制到开发机器上。 hadoop fs命令的-getmerge选项在这时很有用，因为它得到了源模式指定目录 下所有的文件，并将其合并为本地文件系统的一个文件：

% hadoop fs -getmerge max-temp max-temp-local % sort max-temp-local | tail

因为reduce的输出分区是无序的（使用哈希分区函数的缘故），我们对输出进行排 序。对MapReduce的数据做些后期处理是很常见的，把这些数据送入分析工具（例 如R、电子数据表甚至关系型数据库）进行处理。

如果输出文件比较小，另外一种获取输出的方式是使用-cat选项将输出文件打印 到控制台：

% hadoop fs -cat max-temp/*

深人分析后，我们发现某些结柒看起来似乎没有道理。比如，1951年（此处没有显 示）的最高气温是590°C!这个结果是怎么产生的呢？是不正确的输入数据还是程

序中的bug?

###### 6.5.5作业调试

最经典的方法通过打印语句来调试程序，这在Hadoop中同样适用。然而，需要考 虑复杂的情况：当程序运行在几十台、几百台甚至几千台节点上时，如何找到并 检测调试语句分散在这些节点中的输出呢？为了处理我们这种要查找一个不寻常 情况的需求，可以用一个调试语句记录到一个标准错误中，同时配合更新任务状 态信息以提示我们查看错误日志。我们将看到，Web UI简化了这个操作。

我们还要创建一个自定义的计数器来统计整个数据集中不合理的气温记录总数。 这就提供了很有价值的信息来处理如下情况，如果这种情况经常发生，我们需要 从中进一步了解事件发生的条件以及如何提取气温值，而不是简单地丢掉这些记 录。事实上，调试一个作业的时候，应当总想是否能够使用计数器来获得需要找 出事件发生来源的相关信息。即使需要使用日志或状态信息，但使用计数器来衡 量问题的严重程度仍然也是有帮助的（详情参见9.1节）。

如果调试期间产生的日志数据规模比较大，可以有多种选择。一种是将这些信息 写到map的输出流供reduce任务分析和汇总，而不是写到标准错误流。这种方法 通常必须改变程序结构，所以先选用其他技术。另一种是可以写一个程序（当然是 MapReduce程序）来分析作业产生的日志。

我们把调试加入mapper（版本3），而不是reducer，因为我们希望找到导致这些异 常输出的数据源：

public class MaxTemperatureMapper extends Mapper<LongWritable, Text, Text, IntWritable> {

enum Temperature {

OVER一100

}

private NcdcRecordParser parser = new NcdcRecordParser();

@ Override

public void map(LongWritable key. Text value. Context context) throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

int airTemperature = parser.getAirTemperature(); if (airTemperature > 1000) {

System.err•println("Temperature over 100 degrees for input: •• + value); context.setStatus("Detected possibly corrupt record: see logs."); context.getCounter(Temperature.OVER JL00.increment(1));

} _

context.write(new Text(parser.getYear())new IntWritable(airTemperature));

}

}

}

如果气温超过100°C(表示为1000,



![img](Hadoop43010757_2cdb48_2d8748-92.jpg)



为气温只保留小数点后一位)，



我们输出一行



到标准错误流以代表有问题的行，同时使用Context的setStatus()方法来更新

map的状态信息，引导我们查看日志。我们还增加了计数器，在Java中用enum 类型的字段表示。在这个程序中，定义一个OVER_100字段来统计气温超过100°C 的记录数。

完成这些修改，我们重新编译代码，重新创建JAR文件，然后重新运行作业并在 运行时进入任务页面。

1.任务和任务尝试页

作业页面包含了一些查看作业中任务细节的链接。例如，点击“Map”链接，将 进入一个列举了所有map任务的信息的页面。图6-3的屏幕截图显示了一个作业 的任务信息页面，该作业带有调试语句，运行时在任务的“Status”列中显示调试 信息。

從tfi



Map Tasks for job一1410450250506一0006



Logged w: dr who



\> Cluster*

»Application * Job

Qveryicw

Count en Conf)Qurat<on

Mao tasks Reduce tasks AM Loqi



:...... ....... •

Show 2o : emrtaa



![img](Hadoop43010757_2cdb48_2d8748-93.jpg)



raA

idSH 141Q45Q26Q6Qfe 0006 m



Tools



to8Kl41(HSQ2SQ6Q6



rn 000044





Status 右 Detected oosstbly corrupt record： see Jogs. > map Detected pos»Wy corrupt record： se« logs. > map Detected posaoty corrupt record： see



State RUNNING

Search吻⑽

aapsad T«ne $ imtns, 7sgc

Fri,12 Sep 2014 11.35.37 GMT

RUNNING Fn, 12 Sep 2014 11 35:«GMT

N/A



RUNNiNQ Fri, 12 Sep 2014 11.36:11 GMT



N/A



48sec



33sec



ioas. >map



Showing Uo 3 of 3 dnm( htofdd tram 101 totel 酬



![img](Hadoop43010757_2cdb48_2d8748-95.jpg)



6-3.任务页面的屏幕截

点击任务链接将进入任务尝试页面，页面显示了该任务的每个任务尝试。每个任 务尝试页面都有链接指向日志文件和计数器。如果进入成功任务尝试的日志文件 链接，将发现所记录的可疑输入记录。这里考虑到篇幅，已经进行了转行和截断 处理：

Temperature over 100 degrees for input:

0335999999433181957042302005 + 37950 + 139117SAO +0004R3SN V02011359003150070356999 999433201957010100005 + 35317 + 139650SAO +000899999V02002359002650076249N0040005...

此记录的格式看上去与其他记录不同。可能是因为行中有空格，规范中没有这方 面的描述。

作业完成后，査看我们定义的计数器的值，检查在整个数据集中有多少记录超过 100°C。通过Web界面或命令行，可以查看计数器：

% mapred job -counter job_1410450250506_0006 \

'v3.MaxTemperatureMapper$Temperature' OVER_1003

-counter选项的输入参数包括作业ID，计数器的组名（这里一般是类名）和计数器 名称（emun名）。这里，在超过十亿条记录的整个数据集中，只有三个异常记录。 直接扔掉不正确的记录，是许多大数据问题中的标准做法。然而，这里我们需要 谨慎处理这种情况，因为我们寻找的是一个极限值-最高气温值，而不是一个累 计测量值。当然，在本例中，扔掉三个记录可能并不会影响结果。

2.处理不合理的数据

捕获引发问题的输入数据是很有价值的，因为我们可以在测试中用它来检査 mapper的工作是否正常。在这个MRUnit测试中，我们将检查对于不合理的输入 计数器是否进行了更新：

@Test

public void parsesMalformedTemperature() throws IOException,

InterruptedException {

Text value = new Text(H0335999999433181957042302005+37950+139117SAO +0004" + // Year AAAA

nR]SN V02011359003150070356999999433201957010100005+353");

// Temperature 八八A八八

Counters counters = new Counters();

new MapDriver<longWritable> Text, Text, IntWritable()> withMapper [new MaxTemperature Mapper()] withlnput(new LongWritable(0), value) withCounters(counters)

runTest();

Counter c = ounters.findCounter(MaxTemperatureMapper.Temperature.MALFORMED);

assertThat(c.getValue(), is(lL)); }

引发问题的记录与其他行的格式是不同的。范例6-12显示了修改过的程序（版本 4），它使用的解析器忽略了那些没有首符号（+或_）气温字段的行。我们还引入一个 计数器来统计因为这个原因而被忽略的记录数。

范例6-12.该mapper用于查找最高气温

public class MaxTemperatureMapper

extends Mapper<LongWritable> Text, Text, IntWritable> {

enum Temperature {

MALFORMED

}

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

public void map(LongWritable key. Text value, context context throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

int airTemperature = parser.getAirTemperature();

context•write(new Text(parser.getYear())new IntWritable(airTemperature)); } else if (parser.isMaIformedTemperature()) {

System.err.printIn(HIgnoring possibly corrupt input: " + value); context.getCounter(Temperature.MALFORMED).increment(1);

}

}

}

###### 6.5.6 Hadoop 日志

针对不同用户，Hadoop在不同的地方生成日志。表6-2对此进行了总结。

YARN有一个日志聚合（/og uggregW⑽）服务，可以取到已完成的应用的任务日志， 并把其搬移到HDFS中，在那里任务日志被存储在一个容器文件中用于存档。如 果服务已被启用（通过在集群上将yarn, log-aggregation-enable设置为 true）,可以通过点击任务尝试web界面中/ogs链接，或使用mapped job -logs命令查看任务日志。

默认情况下，日志聚合服务处于关闭状态。此时，可以通过访问节点管理器的界

![img](Hadoop43010757_2cdb48_2d8748-96.jpg)



([http://node-manager-host:8042/logs/userlogs)](http://node-manager-host:8042/logs/userlogs)%e6%9f%a5%e7%9c%8b%e4%bb%bb%e5%8a%a1%e6%97%a5)[查看任务日](http://node-manager-host:8042/logs/userlogs)%e6%9f%a5%e7%9c%8b%e4%bb%bb%e5%8a%a1%e6%97%a5)



志。



表6-2. Hadoop日志的类型

| 日志                    | 主要对象 | 描述:穩                                                      | 更多信息                          |
| ----------------------- | -------- | ------------------------------------------------------------ | --------------------------------- |
| 系统守护 进程日志       | 管理员   | 每个Hadoop守护进程产生一个日志文件（使用 log4j）和另一个（文件合并标准输出和错误）。这些 文件分别写入HADOOP^ LOGJ3IR环境变量定义 的目录 | 参见103.2节和 11.2.1 节           |
| HDFS 审计日志           | 管理员   | 这个日志记录所有HDFS请求，默认是关闭状 态。虽然该日志存放位置可以配置，但一般写入 namenode的日志 | 参见11.1.3节                      |
| MapReduce 作业历史 曰志 | 用户     | 记录作业运行期间发生的事件（如任务完成）。集 中保存在HDFS中1 | 参见6.5.3节的 补充内容“作业历 史” |
| MapReduce 任务日志      | 用户     | 每个任务子进程都用Iog4j产生一个日志文件（称 作syslog），一个保存发到标准输出（狄/卯0数据的 | 参见本小节                        |

文件，一个保存标准错误（说的文件。这些文 件写人到YARI\LLOGJ）IR环境变量定义的目录的 userlogs的子目录中

对这些日志文件的写操作是很直观的。任何到标准输出或标准错误流的写操作都 直接写到相关日志文件。当然，在Streaming方式下，标准输出用于map或reduce 的输出，所以不会出现在标准输出日志文件中。

在Java中，如果愿意的话，用Apache Commons Logging API（实际上可以使用任何 能写入log4i的日志API）就可以写入任务的系统日志文件中（羽把文件），如范例6-13所示。

范例6-13.这个等价的Mapper写到标准输出（使用Apache Commons Logging API）

import org.apache.commons.logging.Log;

import org.apache.commons.logging.LogFactory;

import org.apache.hadoop.mapreduce.Mapper;

public class LoggingIdentityMapper<KEYIN> VALUEIN, KEYOUT, VALUEOUT> extends Mapper<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {

private static final Log LOG = LogFactory.getLog(LoggingIdentityMapper.class);

◎Override

@SuppressWarnings("unchecked")

public void map(KEYIN key, VALUEIN value. Context context)

throws IOException, InterruptedException {

// Log to stdout file

System.out•printIn(nMap key: " + key);

// Log to sysLog fiLe LOG.info("Map key: " + key); if (LOG•isDebugEnabled()) {

LOG.debug("Map value: " + value);

}

context.write((KEYOUT) key, (VALUEOUT) value);

}

}

默认的日志级别是INFO,因此DEBUG级别的消息不在syslog任务日志文件中出 现。然而，有时候又希望看到这些消息。这时可以适当设置mapreduce.map. log.level或者mapreduce.reduce.log.level。例如，对于上面的情况你可以为 mapper进行如下设置，以便能够看到日志中的map值。

% hadoop jar hadoop-examples.jar LoggingDriver -conf conf/hadoop-cluster.xml \ -D mapreduce.map.log.level=DEBUG input/ncdc/sample.txt logging-out

有一些控制用于管理任务日志的大小和记录保留时间。在默认情况下，日志最短

在3小时后删除（时间可以通过yarn.nodemanager.log.retain-seconds属性来设

置，当然，如果日志聚合被激活，这个时间可以被忽略）。也可以用

mapreduce.task.userlog.limit.kb属性为每个日志文件的最大规模设置一个阈

值，默认值是0,表示没有上限。

有时你可能需要调试一个问题，这个问题你怀疑在运行一个Hadoop命令的 JVM上发生，而不是在集群上。可以通过如下调用将DEBUG级别的日志发送

1 胃给控制台：

% HADOOP_ROOT_LOGGER=DEBUG,console hadoop fs -text /foo/bar

###### 6.5.7远程调试

当一个任务失败并且没有足够多的记录信息来诊断错误时，可以选择用调试器运 行该任务。在集群上运行作业时，很难使用调试器，因为不知道哪个节点处理哪 部分输入，所以不能在错误发生之前安装调试器。然而，有其他一些方法可以用。

•在本地重新产生错误对于特定的输入，失败的任务通常总会失败。你

可以尝试通过下载致使任务失败的文件到本地运行重现问题，这可以使 用到调试器（如Java的VisualVM）。

![img](Hadoop43010757_2cdb48_2d8748-97.jpg)



使用JVM调试选项失败的常见原



![img](Hadoop43010757_2cdb48_2d8748-98.jpg)



是任务JVM中Java内存溢出。可



以将 mapred.child. java.opts 设为包含-XX:HeapDumpOnOutOfMemory

Error-XX:HeapDumpPath=/path/to/dumps。该设置将产生一个堆转储 （heap dump）,这可以通过y/Ki/ 或 Eclipse Memory Analyzer 这样的工具来 检查。注意，该JVM选项应当添加到由mapned.child, java.opts指定的已有 内存设置中。10.3.3节在讨论YARN和MapReduce的内存时有进一步的 讨论。

•使用任务分析Java的profiler提供了很多JVM的内部细节，Hadoop 提供了分析作业中部分任务的机制。参见6.6节。

在一些情况下保存失败的任务尝试的中间结果文件对于以后的检查是有用的，特 别是在任务工作路径中建立转储或配置文件。可以将mapreduce. task.files.preserve.failedtasks设为true来保存失败的任务文件。

也可以保存成功任务的中间结果文件，以便解释任务没有失败。这时，将属性

mapreduce.task.files.preserve.filepattern 设置为一个正则表达式（与保 留的任务ID匹配）。

对调试有用的另一个属性是 yarn.nodemanager.delete.debug-delay-sec, Q

秒为单位，表示等待删除本地尝试文件（如用于启动任务容器JVM的脚本）的时 间。如果在集群上该属性值被设置为一个比较大的合理值（例如，600，表示10分 钟），那么在文件删除前有足够的时间查看。

为了检查任务尝试文件，登录到任务失败的节点并找到该任务尝试的目录。它在一 个本地MapReduce目录下，由mapreduce.cluster.local.dir的设置决定（细节请参 见10.3.3节）。如果这个属性是以逗号分隔的目录列表（在一台机器的物理磁盘上分 布负载），在找到那个特定的任务尝试（task attempt）之前，需要搜索整个目录。task attemp的目录在以下位置：

mapreduce.cLuster.LocaL.dir/usercache/user/appcache/oppticotion-ID/output/tos/?-attempt，ID

##### 6.6作业调优

作业运行后，许多开发人员可能会问：“能够让它运行得更快一些吗?” 有一些Hadoop相关的“疑点”值得检查一下，看它们是不是引发性能问题的“元 凶”。在开始任务级别的分析或优化之前，必须仔细研究表6-3所示的检查内容。

表6-3.作业调优检查表

范围

mapper的数量

最佳实践

mapper需要运行多长时间？如果平均只运行几秒钟， 则可以看是否能用更少mapper运行更长的时间，通常 是一分钟左右。时间长度取决于使用的输入格式

更多参考信息

8.2.1 节

| reducer的数量 | 检查使用的reducer数目是不是超过1个。根据经验， Reduce任务应运行5分钟左右，且能生产出至少一个 数据块的数据 | 8.1.1 节 |
| ------------- | ------------------------------------------------------------ | -------- |
| combiner      | 作业能否充分利用combiner来减少通过shuffle传输的              | 2.4.2 节 |
|               | 数据量                                                       |          |
| 中间值的压缩  | 对map输出进行压缩几乎总能使作业执行得更快                    | 5.2.3 节 |
| 自定义序列    | 如果使用自定义的Writable对象或自定义的                       | 5.3.3    |
| 參            | comparator,则必须确保已实现RawComparator                     |          |
| 调整shuffle   | MapReduce的shuffle过程可以对一些内存管理的参数               | 7.33     |
|               | 进行调整，以弥补性能的不足                                   |          |

###### 分析任务

正如调试一样，对MapReduce这类分布式系统上运行的作业进行分析也有诸多挑 战。Hadoop允许分析作业中的一部分任务，并且在每个任务完成时，把分析信息 放到用户的机器上，以便日后使用标准分析工具进行分析。

当然，对本地作业运行器中运行的作业进行分析可能稍微简单些。如果你有足够 的数据运行map和reduce任务，那么对于提高mapper和reducer的性能有很大的 帮助。但必须注意一些问题。本地作业运行器是一个与集群完全不同的环境，并 且数据流模式也截然不同。如果MapReduce作业是I/O密集型的（很多作业都属于 此类），那么优化代码的CPU性能是没有意义的。为了保证所有调整都是有效 的，应该在实际集群上对比新老执行时间。这说起来容易做起来难，因为作业执 行时间会随着与其他作业的资源争夺和调度器决定的任务顺序不同而发生改变。 为了在这类情况下得到较短的作业执行时间，必须不断运行（改变代码或不改变代 码），并检查是否有明显的改进。

有些问题（如内存溢出）只能在集群上重现，在这些情况下，必须能够在发生问题的 地方进行分析。

\1. HPROF分析工

许多配置属性可以控制分析过程，这些属性也可以通过］obConf的简便方法获 取。启用分析很简单，将属性mapreduce.task.profile设置为true即可：

% hadoop jar hadoop-examples•jar v4.MaxTemperatureDriver \ -conf conf/hadoop-cluster.xml \

-D mapreduce.task.profile=true \ input/ncdc/all max-temp

上述命令正常运行作业，但是给用于启动节点管理器上的任务容器的Java命令增 加了一个-agentlib参数。可以通过设置属性mapreduce.task, profile.params来精确地控制该新增参数。默认情况下使用HPROF, —个JDK 自带的分析工具，虽然只有基本功能，但是能提供程序的CPU和堆使用情况等有 价值的信息。

分析作业中的所有任务通常没有意义，因此，默认情况下，只有那些ID为0,

1，2的map任务和reduce任务将被分析。可以通过设置mapreduce. task.profile.maps 和 mapreduce.task.profile.reduces 两个属性来指定想 要分析的任务ID的范 每个任务的分析输出和任务日志一起存放在节点管理器的本地日志目录的userlogs 子目录下（和，/叹，stdout, AZt/err文件一起），可以根据日志聚合是否启用，使用 6.5.6节介绍的方法获取到。

##### 6.7 MapReduce的工作流

至此，你已经知道MapReduce应用开发的机制了。我们目前还未考虑如何将数据 处理问题转化成MapReduce模型。

本书前面的数据处理都用来解决十分简单的问题（如在指定年份找到最高气温值的 记录）。如果处理过程更复杂，这种复杂度一般是因为有更多的MapReduce作业， 而不是更复杂的map和reduce函数。换而言之，通常是增加更多的作业，而不是 增加作业的复杂度。

对于更复杂的问题，可考虑使用比MapReduce更高级的语言，如Pig、hive、 Cascading、Crunch或Spark。一个直接的好处是：有了它之后，就用不着处理到 MapReduce作业的转换，而是集中精力分析正在执行的任务。

最后，Jimmy Lin和Chris Dyer合著的《MapReduce数据密集型文本处理》（Data-Intensive Text Processing with    —书是学习 MapReduce 算法设计的优秀资

源，强烈推荐。该书由Morgan & Claypool出版社于2010出版。

###### 6.7.1将问题分解成MapReduce作业

让我们看一个更复杂的问题，我们想把它转换成MapReduce工作流。

假设我们想找到每个气象台每年每天的最高气温记录的均值。例如，要计算 029070〜99999气象台的1月1日的每日最高气温的均值，我们将从这个气象台的 1901年1月1日，1902年1月1日，直到2000年的1月1日的气温中找出每日 最高气温的均值。

我们如何使用MapReduce来计算它呢?计算自然分解为下面两个阶段

计算每对station-date的每日最高气温。

本例中的MapReduce程序是最高气温程序的一个变种， 例中的键是一个综合的station-date对，而不只是年份。

不同之处在于本



（2）计算每个station-day-month键的每日最高气温的均值。

mapper从上一个作业得到输出记录（station-date,最高气温值），丢掉年份 部分，将其值投影到记录（station-day-month，最高气温值）。然后reducer 为每个station-day-month键计算最高气温值的均值。

第一阶段的输出看上去就是我们想要的气象台的信息。范例中的 mean max_daily temp.sh 脚本提供了 Hadoop Streaming 的一个实现：

以上是气象台029070-99999在整个世纪中1月1日的日均最高气温为-6.8°C 只用一个MapReduce过程就能完成这个计算，但是它可能会让部分程序员花更多

一个作业可以包含多个(简单的)MapReduce步骤，这样整个作业由多个可分解的、 可维护的mapper和reducer组成。本书第V部分提到的一些实例学习包括使用 MapReduce来解决的大量实际问题，在每个例子中，数据处理任务都是使用两个 或更多MapReduce作业来实现的。对于理解如何将问题分解成MapReduce工作 流，第V部分所提供的详细介绍非常有价值。

相对于我们已经做的，mapper和reducer完全可以进一步分解。mapper 一般执行 输入格式解析、投影(选择相关的字段)和过滤(去掉无关记录)。在前面的mapper 中，我们在一个mapper中实现了所有这些函数。然而，还可以将这些函数分割到 不同的mapper,然后使用Hadoop自带的ChainMapper类库将它们连接成一个 mapper。结合使用ChainReducer，你可以在一个MapReduce作业中运行一系列 的mapper,再运行一个reducer和另一个mapper链。

###### 6.7.2 关于 JobControl

当MapReduce工作流中的作业不止一个时，问题随之而来：如何管理这些作业按

顺序执行?有几种方法，其中主要考虑是否有一个线性的作业链或一个更复杂的作 业有向无环图(DAG，directed acyclic graph)0

对于线性链表，最简单的方法是一个接一个地运行作业，等前一个作业运行结束 后再运行下一个：

JobClient.runlob(confl); lobClient.run]ob(conf2);

如果一个作业失败，run]ob()方法就抛出一个IOException，这样一来，管道中 后面的作业就无法执行。根据具体的应用程序，你可能想捕获异常，并清除前一 个作业输出的中间数据

这种方法类似新的MapReduce API，除了需要Job上的waitForCompletion()方法的布尔 返回值：true表示作业成功，而false表示失败。

对于比线性链表更复杂的结构，有相关的类库可以帮助你合理安排工作流。它们

也适用于线性链表或一次性作业。最简单的是org.apache.hadoop.mapreduce.

①这个一个很有趣的练习。提示：使用9.2.4节介绍的内容。

jobcontrol 包中的 DobControl 类0 在 org.apache.hadoop.mapped. jobcontrol 包中也有一个等价的类。］obControl的实例表示一个作业的运行图，你可以加入 作业配置，然后告知］obControl实例作业之间的依赖关系。在一个线程中运行 ZlobControl时，它将按照依赖顺序来执行这些作业。也可以查看进程，在作业结 束后，可以査询作业的所有状态和每个失败相关的错误信息。如果一个作业失 败，JobControl将不执行与之有依赖关系的后续作业。

###### 6.7.3 关于 Apache Oozie

Apache Oozie是一个运行工作流的系统，该工作流由相互依赖的作业组成。Oozie 由两部分组成：一个工作流引擎，负责存储和运行由不同类型的Hadoop作业 （MapReduce, Pig, Hive等）组成的工作流；一个引擎，负责基于预定 义的调度策略及数据可用性运行工作流作业。Oozie的设计考虑到了可扩展性，能 够管理Hadoop集群中数千工作流的及时运行，每个工作流的组成作业都可能有好 几十个。

由于在运行工作流中成功的那一部分时不会浪费任何时间，因此在Oozie中更易 处理失败工作流的重运行。任何一个管理过复杂批处理系统的人都知道，由于宕 机或故障而丢失作业后再跟上处理节奏是多么困难，因此他们都会欣赏Oozie的 这个特性。（更进一步，coordinator应用代表了单个的数据管线可以被打包在一 起，然后作为一个单元一起运行。） 不同干在客户端运行并提交作业的］obControl，Ooize作为服务器运行，客户端 提交一个立即或稍后执行的工作流定义到服务器。在Ooize中，工作流是一个由 动作（action）节点和控制流节点组成的DAG（有向无环图）。

动作节点执行工作流任务，例如在HDFS中移动文件，运行MapReduce, Streaming、Pig或Hive作业，执行Sqoop导入，又或者是运行shell脚本或Java 程序。控制流节点通过构建条件逻辑（不同执行分支的执行依赖于前一个动作节点 的输出结果）或并行执行来管理活动之间的工作流执行情况。当工作流结束时， Oozie通过发送一个HTTP的回调向客户端通知工作流的状态。还可以在每次进入 工作流或退出一个动作节点时接收到回调。

1.定义Oozie工作流

工作流定义是使用Hadoop Process Difinition Language以XML格式来书写，这个

规范可在Oozie网站(tepV/boz/e.a;赚//e.org/)找到。范例6-14展示了一个运行单个 MapReduce作业的简单OoZie工作流定义。

范例6-14.用来运行求最高温度的MapReduce作业的Oozie工作流定义

〈workflow-app xmlns="uri:oozie:workflow:0.1" name=.’max-temp-workflow•’> <start to=,,max-temp-mr,,/>

〈action name=,,max-temp-mr,,>

<map-reduce>

<job-tracker>${resourceManager}</job-tracker>

<name-node>${nameNode}</name-node>

<prepane>

〈delete path="${nameNode}/user/${wf:user()}/output'7> </prepare>

〈configuration〉

<property>

<name>mapred.mapper.new-api</name>

<value>true</value>

</property>

<property>

<name>mapred.reducer.new-api</name>

<value>true</value>

</property>

<property>

<name>mapreduce.job.map.class</name> <value>MaxTemperatureMapper</value>

</property>

<property>

<name>mapreduce.job.combine.class</name> <value>MaxTemperatureReducer</value>

</property>

<property>

<name>mapreduce.job.reduce.class</name> <value>MaxTemperatureReducer</value>

</property>

<property>

<name>mapreduce.job.output.key.class</name>

<value>org.apache.hadoop.io.Text</value>

</property>

<property>

<name>mapreduce.job.output.value.class</name> <value>org.apache.hadoop.io.IntWritable</value>

</property>

<property>

<name>mapreduce.input.fileinputformat.inputdir</name>

<value>/user/${wf:user()}/input/ncdc/micro</value>

</property>

<property>

<name>mapreduce.output.fileoutputformat.outputdir</name> <value>/user/${wf:user()}/output</value>

</property>

〈/configuration〉

</map-reduce> <ok to=',end,7>

<error to="fail"/>

</action>

〈kill name="fail">

<message>MapReduce failed,error message[${wf:errorMessage(wf:lastErrorNode())}] </message>

</kill>

<end name=,,end,7>

</workflow-app>

这个工作流有三个控制流节点和一个动作节点：一个start控制节点、一个map-reduce动作节点、一个kill控制节点和一个end控制节点。节点及其之间的转 换如图6-4所示。

每个工作流都必须有一个start节点和一个end节点。当工作流作业开始时，它 转移到有start节点指定的节点上（本例中max-temp-mr动作）。当一个工作流作 业转移到end节点时就意味着它成功完成了。然而，如果一个工作流作业转移到 了 kill节点，那么就被认为失败了并且报告在工作流定义中的message元素指 定的错误消息。

这个工作流定义文件的大部分都是指定map-reduce动作。前两个元素（job-tracker 和 name-node）用于指定提交作业的YARN资源管理器（或Hadoop 1中的 jobtracker）和输入输出数据的namenode（实际上是一个Hadoop文件系统的URI）。 两者都被参数化，使得工作流定义不受限于特定的集群，更有利于测试。这些参 数在提交时指定为工作流属性，我们稍后会看到。

MapReduce 应用开发 18*1

##### 6-4. 一个Oozie工作流的转移

与其名称无关，job-tracker元素是用来指定YARN资源管理器地址和端口的。

可选项prepare元素在MapReduce作业之前运行并用于目录删除（在需要的时候 也可以创建目录，但这里没有指明）。通过确保输出目录在运行作业之前处于一致 的状态，如果作业失败的话，Oozie也可以安全地重新执行。

运行MapReduce作业是在configuration元素中设定的，通过为Hadoop配置的 名值对来设置嵌套的元素。可以把MapReduce配置部分看作本书中其他地方运行 MapReduce程序（如范例2-5所示）使用的驱动类的一个替代。

我们在工作流的定义中的几个地方利用了 JSP Expression Language （EL）语法。 Oozie提供了一组与工作流交互的函数。例如，${wf:user（）}返回开始当前工作 流作业的用户名，我们用它来指定正确的文件系统路径。Oozie规范中列出所有 Oozie支持的EL函数。

2.打包和配置Oozie工作流应用

工作流应用由工作流定义和所有运行它所需的资源（例如MapReduce JAR文件、 Pig脚本等）构成。应用必须遵循一个简单的目录结构，并在HDFS上配置，这样 它们才能被Oozie访问。对于这个工作流应用，我们将所有的文件放到基础目录 max-temp-workflow 中，如下所示：

rnax-tenip-workflow/

5— lib/

1——hadoop-examples,jar ——workflow•xml

工作流定义文件workflow.xml必须在该目录的顶层出现。包含应用的MapReduce 类的JAR文件放在肋目录中。

遵循这种布局的工作流应用能通过很多合适的工具创建，例如Ant或Maven;可 以在本书附带的代码中找到样例。一旦创建应用，就使用正规的Hadoop工具将它 复制到HDFS。命令如下：

% hadoop fs -put hadoop-examples/target/max-temp-workflow max-temp-workflow

3.运行Oozie工作流作业

接下来，我们看看如何为刚刚上载的应用运行一个工作流作业。为此，使用oozie 命令行工具，它是用于和Oozie服务器通信客户端程序。方便起见，我们输出 OOZIE_URL环境变量来告诉oozie命令使用哪个Oozie服务器（这里我们使用本地 运行的服务器）：

% export OOZIE_URL="<http://localhost:11000/oozie>"

oozie工具有很多子命令（输入oozie help可得到这些子命令的列表），但我们将 调用带有-run选项的job子命令来运行工作流作业：

% oozie job -config ch06-mr-dev/src/main/resources/max-temp-workflow.properties \-run

job: 0000001-140911033236814-oozie-oozi-W

-config选项没定本地Java属性文件，它包含工作流XML文件里参数的定义（这里 是nameNode和resourceManager）与oozie.application.path,后者告知 Oozi HDFS 中工作流应用的位置。属性文件的内容如下：

nameNode=hdfs://localhost:8020 resourceManager=localhost:8032

oozie. wf. application. path=${nameNode}/user/${user.name}/max-temp-workf low

为了得到工作流作业的状态信息，要使用-info选项，指定由前面运行的命令打 印的作业ID（输入oozie job将得到所有作业的列表）：

% oozie job -info 0000001-140911033236814-oozie-oozi-W

输出显示如下状态：RUNNING、KILLED或者SUCCEDED。通过Oozie的网页 U\（<http://localhost>: 11000/oozie），你也可以找到这些信息。

作业成功后，可以通过以下常用方法检查结果：

% hadoop fs -cat output/part-*

1949    111

1950    22

这个例子只是简单展示了如何写Oozie工作流。Oozie网站上的文档介绍了如何创 建更复杂的工作流以及如何写和运行coordinator作业。

MapReduce应用开发 183
