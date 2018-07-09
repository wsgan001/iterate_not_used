### MapReduce的特性

本章探讨MapReduce的一些高级特性，其中包括计数器、数据集的排序和连接

##### 9.1计数器

在许多情况下，用户需要了解待分析的数据，尽管这并非所要执行的分析任务的 核心内容。以统计数据集中无效记录数目的任务为例，如果发现无效记录的比例 相当高，那么就需要认真思考为何存在如此多无效记录。是所采用的检测程序存 在缺陷，还是数据集质量确实很低，包含了大量无效记录？如果确实是数据集的 质量问题，则可能需要扩大数据集的规模以增大有效记录的比例，从而进行有意义 的分析。

计数器是收集作业统计信息的有效手段之一，用于质量控制或应用级统计。计数 器还可辅助诊断系统故障。如果需要将日志信息传输到map或reduce任务，更好 的方法通常是看能否用一个计数器值来记录某一特定事件的发生。对于大型分布 式作业而言，使用计数器更为方便。除了因为获取计数器值比输出日志更方便， 还有根据计数器值统计特定事件的发生次数要比分析一堆日志文件容易得多。

###### 9.1.1内置计数器

Hadoop为每个作业维护若干内置计数器，以描述多项指标。例如，某些计数器记 录已处理的字节数和记录数，使用户可监控已处理的输入数据量和已产生的输出 数据量。

这些内置计数器被划分为若干个组，参见表9-1。

表9-1.内置的计数器分组

| 组别MapReduce 任务计数器 | 名称/类别org.apache.hadoop.mapreduce.TaskCounter             | 参考表9-2 |
| ------------------------ | ------------------------------------------------------------ | --------- |
| 文件系统计数器           | org.apache.hadoop.mapreduce.FileSystemCounter                | 表9-3     |
| FilelnputFormat计数器    | org.apache.hadoop.mapreduce.lib.input.FilelnputFormatCounter | 表9-4     |
| FileOutputFormat计数器   | org.apache.hadoop.mapreduce.lib.output.FileOutputFormatCounter | 表9-5     |
| 作业计数器               | org.apache.hadoop.mapreduce.JobCounter                       | 表9-6     |

各组要么包含任务计数器（在任务处理过程中不断更新），要么包含作业计数器（在 作业处理过程中不断更新）。这两种类型将在后续章节中进行介绍。

1.任务计数器

在任务执行过程中，任务计数器采集任务的相关信息，每个作业的所有任务的结 果会被聚集起来。例如，MAP_INPUT_RECORDS计数器统计每个map任务输人记 录的总数，并在一个作业的所有map任务上进行聚集，使得最终数字是整个作业 的所有输入记录的总数。

任务计数器由其关联任务维护，并定期发送给application master。因此，计数器能 够被全局地聚集。（参见7.1.5节中对进度和状态更新的介绍。）任务计数器的值每 次都是完整传输的，而非传输自上次传输之后的计数值，从而避免由于消息丢失 而引发的错误。另外，如果一个任务在作业执行期间失败，则相关计数器的值会 减小。

虽然只有当整个作业执行完之后计数器的值才是完整可靠的，但是部分计数器 仍然可以在任务处理过程中提供一些有用的诊断信息，以便由Web界面监

##### 控。例如，PHYSICAL_MEMORY_BYTES、VIRTUAL_MEMORY_BYTES 和 COMMITTED, HEAP_BYTES计数器显示特定任务执行过程中的内存使用变化情况。

内置的任务计数器包括在MapReduce任务计数器分组中的计数器（表9-2）以及在文 件相关的计数器分组（表9-3、表9-4和表9-5）中的计数器。

表9-2.内置的MapReduce任务计数器

| 计数器名称map输人的记录数(MAP-INPUT-RECORDS)         | 说明作业中所有map已处理的输入记录数。每次RecordReader读 到一条记录并将其传给map的maP()函数时，该计数器的值 递增 |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| 分片(split)的原始字节数 (SPLIT—RAW-BYTES)            | 由map读取的输人-分片对象的字节数。这些对象描述分片元 数据(文件的位移和长度)，而不是分片的数据自身，因此总 规模是小的 |
| map输出的记录数(MAP^OUTPUT^RECORDS)                  | 作业中所有map产生的map输出记录数。每次某一个map 的OutputCollector调用collect()方法时，该计数器的 值增加 |
| map输出的字节数(MAP—OUTPUT一BYTES)•                  | 作业中所有map产生的未经压缩的输出数据的字节数。每次 某一个map的OutCollector调用collect()方法时，该计 数器的值增加 |
| map输出的物化字节数(MAPJ)UTPUT_MATERIALIZED—BYTES)   | map输出后确实写到磁盘上的字节数；若map输出压缩功能 被启用，则会在计数器值上反映出来 |
| combine输入的记录数(COMBINE—INPUT一RECORDS)          | 作业中所有combiner (如果有)已处理的输人记录数。 combiner的迭代器每次读一个值，该计数器的值增加。注 意：本计数器代表combiner已经处理的值的个数，并非不同 的键组数(后者并无实质意义，因为对于combiner而e，并不 要求每个键对应一个组，详情参见2.4.2节和7.3节 |
| combine输出的记录数(COMBINE J)UTPUT__RECORDS)        | 作业中所有combiner(如果有)已产生的输出记录数。每当一 个 combiner 的 OutputCollector 调用 collect()方法时，该计数 器的值增加 |
| reduce输人的组 (REDUCE一INPUTJ5ROUPS)                | 作业中所有reducer已经处理的不同的码分组的个数。每当某 一个reducer的reduce()被调用时，该计数器的值增加 |
| reduce输入的记录数 (REDUCE__INPUT_RECORDS)           | 作业中所有reducer已经处理的输人记录的个数。每当某个 reducer的迭代器读一个值时，该计数器的值增加。如果所有 reducer已经处理数完所有输人，则该计数器的值与计数器 “map输出的记录”的值相同 |
| reduce输出的记录数(REDUCE—OUTPUT—RECORDS)            | 作业中所有map已经产生的reduce输出记录数。毎当某个 reducer 的 OutputCollector 调用 collect()方法时，该计 数器的值增加 |
| reduce经过shuffle的字节数 (REDUCE一SHUF F LE一BYTES) | 由shuffle复制到reducer的map输出的字节数                      |
| 溢出的记录数(SPILLED RECORDS)                        | 作业中所有map和reduce任务溢出到磁盘的记录数                  |
| CPU毫秒(CPU^MILLISECONDS)                            | 一个任务的总CPU时间，以毫秒为单位，可由 /proc/cpuinfo 获取   |
| 物理内存字节数(PHYSICAL— MEMORY^BYTES)               | 一个任务所用的物理内存，以字节数为单位，可由 /proc/meminfo 获取 |

续表

| 计数器名称                                              | 说明                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| 虚拟内存字节数(VIRTUAL^MEMORY^BYTES)有效的堆字节数      | 一个任务所用虚拟内存的字节数，由/proc/meminfo获取在JVM中的总有效内存量(以字节为单位)，可由Runtime. |
| (COMMITTED_HEAP_BYTES)GC运行时间毫秒数                  | getRuntime() • totalMemory ()获取在任务执行过程中，垃圾收集器(garbage collection)花费的时 |
| (GC一TIME一MILLIS)                                      | 间(以毫秒为单位)，可由GarbageCollector MXBean. getCollectionTime()获取 |
| 由shuffle传输的map输出数 (SHUFFLED_MAPS)失败的shuffle数 | 由shuffle传输到reducer的map输出文件数，详情参见7.3节shuffle过程中，发生map输出拷贝错误的次数 |
| (FAILED^SHUFFLE)被合并的map输出数                       | shuffle过程中，在reduce端合并的map输出文件数                 |
| (MERGED」4AP一OUTPUTS)                                  |                                                              |

表9-3.内置的文件系统任务计数器

| 计数器名称 文件系统的读字节数 (BYTES一READ) | 说明由map任务和reduce任务在各个文件系统中读取的字节数，各 个文件系统分别对应一个计数器，文件系统可以是Local, HDFS、S3 等 |
| ------------------------------------------- | ------------------------------------------------------------ |
| 文件系统的写字节数 (BYTES—WRITTEN)          | 由map任务和reduce任务在各个文件系统中写的字节数              |
| 文件系统读操作的数量(READJDPS)              | 由map任务和reduce任务在各个文件系统中进行的读操作的数 量(例如,open操作，file status操作) |
| 文件系统大规模读操作的数量(LARGE READ OPS)  | 由map和reduce任务在各个文件系统中进行的大规模读操作(例 如，对干一个大容量目录进行list操作)的数量 |
| 文件系统写操作的数量(WRITEJDPS)             | 由map任务和reduce任务在各个文件系统中进行的写操作的数 量(例如，create操作，append操作) |

表9-＜内置的Filel叩utFormat任务计数器

| 计数器名称               | 说明                                     |
| ------------------------ | ---------------------------------------- |
| 读取的字节数(BYTES^READ) | 由map任务通过Filel叩utFormat读取的字节数 |

表9-5.内置的FileOutputFormat任务计数器

| 计数器名称 写的字节数 (BYTES一WRITTEN) | 说明由map任务(针对仅含map的作业)或者reduce任务通过 FileOutputFormat 写的字节数 |
| -------------------------------------- | ------------------------------------------------------------ |
|                                        |                                                              |

2.作业计数器

作业计数器（表9-6）由application master维护，因此无需在网络间传输数据，这一

点与包括“用户定义的计数器”在内的其他计数器不同。这些计数器都是作业级 别的统计量，其值不会随着任务运行而改变。例如，TOTAL_LAUNCHED_MAPS统计 在作业执行过程中启动的map任务数，包括失败的map任务。

表9-6.内置的作业计数器

| 计数器名称                                   | 说明                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| 启用的map任务数(TOTAL-LAUNCHED—MAPS)         | 启动的map任务数，包括以“推测执行”方式启动的任务，详 情参见7.4.2节 |
| 启用的reduce任务数 (TOTAL-LAUNCHED REDUCES)  | 启动的reduce任务数，包括以“推测执行”方式启动的任务           |
| 启用的uber任务数 (TOTAL-LAUNCHED一UBE盯ASKS) | 启用的uber任务数，详情参见7.1节                              |
| uber任务中的map数                            | 在uber任务中的map数                                          |
| (NUMJJBER—SUBMAPS)                           |                                                              |
| Uber任务中的reduce数 (NUMJJBER—SUBREDUCES)   | 在uber任务中的reduce数                                       |
| 失败的map任务数 （NUM一FAILED一MAPS）        | 失败的map任务数，用户可以参见7.2.1节对任务失败的讨 论，了解失败原因 |
| 失败的reduce任务数(NUM一FAILED—REDUCES)      | 失败的reduce任务数                                           |
| 失败的uber任务数(NUM—FAILEDJJBERTASKS)       | 失败的uber任务数                                             |
| 被中止的map任务数 （NUM」＜ILLED—MAPS）      | 被中止的map任务数，可以参见7.2.1节对任务失败的讨论， 了解中止原因 |
| 被中止的reduce任务数                         | 被中止的reduce任务数                                         |
| (NUM^KILLED^REDUCES)                         |                                                              |
| 数据本地化的map任务数 （DATA一LOCAL一MAPS）  | 与输入数据在同一节点上的map任务数                            |
| 机架本地化的map任务数 （RACK—LOCALJ1APS0     | 与输入数据在同一机架范围内但不在同一节点上的map任务数        |
| 其他本地化的map任务数 （OTHER—LOCAL一MAPS）  | 与输入数据不在同一机架范围内的map任务数。由于机架之间 的带宽资源相对较少，Hadoop会尽量让map任务靠近输入数 据执行，因此该计数器值一般比较小。详情参见图2-2 |

续表

说明

map任务的总运行时间，单位毫秒。包括以推测执行方式启动 的任务。可参见相关的度量内核和内存使用的计数器

(VCORES_MILLIS_MAPS 和 MB_MILLIS_MAPS)

reduce任务的总运行时间 (MILLIS_REDUCES)



reduce任务的总运行时间，单位毫秒。包括以推测执行方式启

动的任务。可参见相关的度量内核和内存使用的计数器

(VCORES_MILLIS_REDUCES 和 MB_MILLIS_REDUCES)

###### 9.1.2用户定义的Java计数器

MapReduce允许用户编写程序来定义计数器，计数器的值可在mapper或reducer 中增加，计数器由一个Java枚举(enum)类型来定义，以便对有关的计数器分组。 一个作业可以定义的枚举类型数量不限，各个枚举类型所包含的字段数量也不 限。枚举类型的名称即为组的名称，枚举类型的字段就是计数器名称。计数器是 全局的。换言之，MapReduce框架将跨所有map和reduce聚集这些计数器，并在 作业结束时产生一个最终结果。

在第6章中，我们创建了若干计数器来统计天气数据集中不规范的记录数。范例 9-1中的程序对此做了进一步扩展，能统计缺失记录和气温质量代码的分布情况。

范例9-1.统计最高气温的作业，包括统计气温值缺失的记录、不规范的字段和质量代码

public class MaxTemperatureWithCounters extends Configured implements Tool {

enum Temperature { MISSING, MALFORMED

}

static class MaxTemperatureMapperWithCounters extends Mapper<LongWritableJ Text, Text, IntWritable> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key. Text value， Context context) throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

int airTemperature = parser.getAirTemperature(); context.write(new Text(parser.getYear())，

new IntWritable(airTemperature));

} else if (parser.isMalformedTemperature()) {

System.err.println(HIgnoring possibly corrupt input: " + value); context.getCounter(Temperature.MALFORMED).increment(1);

} else if (parser.isMissingTemperature()) { context.getCounter(Temperature.MISSING).increment(1);

}

// dynamic counter

context.getCounter(.•TemperatureQuality’、parser.getQuality()).increment(1);

}

}

^Override

public int run(String[] args) throws Exception {

Dob job = DobBuilder.parselnputAndOutputCthis^ getConf(), args); if (job == null) {

return -1;

}

job<setOutputKeyClass(Text.class);

job.setOutputValueClass(IntWritable.class);

job.setMapperClass(MaxTemperatureMapperWithCounters.class); job.setCombinerClassCMaxTemperatureReducer.class); job.setReducerClassCMaxTemperatureReducer.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new MaxTemperatureWithCounters(), args); System.exit(exitCode);

}

}

理解上述程序的最佳方法是在完整的数据集上运行一遍:

% hadoop jar hadoop-examples•jar MaxTemperatureWithCounters \ input少ncdc/all output-counters

作业成功执行完毕之后会输出各计数器的值（由作业的客户端完成）。以下是一组我 们感兴趣的计数器的值。

Air Temperature Records Malformed=3 Missing=66136856

TemperatureQuality

0=1

1=973422173

2=1246032

4=10764500

5=158291879

6=40066

9=66136858

注意，为使得气温计数器的名称可读性更好，使用了 Java枚举类型的命名方式(使 用下划线作为嵌套类的分隔符)，这种方式称为“资源捆绑” (resource bundle),例如*例中 为MaxTemperatu}vWithCounters_Tefnperatiov.pn)perties，该捆绑包含显示名称的映射关系。

1.动态计数器

上述代码还使用了动态计数器，这是一种不由Java枚举类型定义的计数器。由于 Java枚举类型的字段在编译阶段就必须指定，因而无法使用枚举类型动态新建计数器。范 例8-1统计气温质量的分布，尽管通过格式规范定义了可以取的值，但相比之下，使用 动态计数器来产生实际值更加方便。在该例中，Context对象的getCounter() 方法有两个String类型的输入参数，分别代表组名称和计数器名称：

public Counter getCounter(String groupName^ String counterName)

鉴于Hadoop需先将Java枚举类型转变成String类型，再通过RPC发送计数器 值，这两种创建和访问计数器的方法(即使用枚举类型和String类型)事实上是等 价的。相比之下，枚举类型易于使用，还提供类型安全，适合大多数作业使用。 如果某些特定场合需要动态创建计数器，则可以使用String接口。

2.获取计数器

除了通过Web界面和命令行(执行mapped job -counter指令)之外，用户还可 以使用Java API获取计数器的值。通常情况下，用户一般在作业运行完成、计数 器的值已经稳定下来时再获取计数器的值，而Java API还支持在作业运行期间就 能够获取计数器的值。范例9-2展示了如何统计整个数据集中气温信息缺失记录 的比例。

范例9-2.统计气温信息缺失记录所占的比例

import org.apache.hadoop.conf.Configured; import org.apache.hadoop.mapreduce.*; import org.apache.hadoop.util.*;

public class MissingTemperatureFields extends Configured implements Tool {

^Override

public int run(String[] args) throws Exception { if (args.length != 1) {

JobBuilder.printUsage(this, "<job ID>n); return -1;

}

String jobID = args[0];

Cluster cluster = new Cluster(getConf());

Job job = cluster.getDob(DobID.forName(jobID)); if (job == null) {

System.err.printf(HNo job with ID %s found.\n", jobID); return -1;

}

if (!job.isComplete()) {

System.err.printf("Dob %s is not complete.Xn", jobID); return -1;

}

Counters counters = job.getCounters();

long missing = counters.findCounter(

MaxTemperatureWithCounters.Temperature.MISSING).getValue();

long total = counters.findCounter(TaskCounter.MAP_INPUT_RECORDS).getValue();

System.out.printf(HRecords with missing temperature fields: %.2f%%\nn> 100.0 * missing / total);

return 0;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new MissingTemperatureFieldsO^ args); System.exit(exitCode);

}

}

首先，以作业ID为输入参数调用get］ob()方法，从cluster中获取一个］ob对 象。通过检查返回是否为空来判断是否有一个作业与指定ID相匹配。有多种因素 可能导致无法找到一个有效的］ob对象，例如，错误地指定了作业ID，或是该作 业不再保留在作业历史中。

其次，如果确认该作业已经完成，则调用该］ob对象的getCounters()方法会返 回一个Counters对象，封装了该作业的所有计数器。Counters类提供了多个方 法用于获取计数器的名称和值。上例调用findCounter^)方法，它通过一个枚举 值来获取气温信息缺失的记录数和被处理的记录数(根据一个内置计数器)。

最后，输出气温信息缺失记录的比例。针对整个天气数据集的运行结果如下 所示：

% hadoop jar hadoop-examples.jar MissingTemperatureFields job_1410450250506_0007 Records with missing temperature fields: 5.47%

###### 9.1.3用户定义的Streaming计数器

使用Streaming的MapReduce程序可以向标准错误流发送一行特殊格式的信息来 增加计数器的值，这种技术可以作为一种计数器控制手段。信息的格式如下:

reporter:counter:group,counter,amount

以下Python代码片段将Temperature组的Missing计数器的值增加1:

sys.stderr.write（"reporter:counter:Temperature,Missing,l\n"）

状态信息也可以类似方法发出，格式如下所示：

reporter:status:message

##### 9.2排序

排序是MapReduce的核心技术。尽管应用本身可能并不需要对数据排序，但仍可 能使用MapReduce的排序功能来组织数据。本节将讨论几种不同的数据集排序方 法，以及如何控制MapReduce的排序。12.8节介绍了如何对Avro数据进行 排序。

###### 9.2.1 准备

下面将按气温字段对天气数据集排序。由于气温字段是有符号整数，所以不能将 该字段视为Text对象并以字典顺序排序。'反之，我们要用顺序文件存储数据， 其IntWritable键代表气温（并且正确排序），其Text值就是数据行。

范例9-3中的MapReduce作业只包含map任务，它过滤输入数据并移除包含有无 效气温的记录。各个map创建并输出一个块压缩的顺序文件。相关指令如下：

% hadoop jar hadoop-examples•jar SortDataPreprocessor input/ncdc/all \ input/ncdc/all-seq

»

范例9-3.该MapReduce程序将天气数据转成SequenceFile格式

public class SortDataPreprocessor extends Configured implements Tool {

static class CleanerMapper

extends Mapper<LongWritableJ Text, IntWritable^ Text> {

①有一个常用的方法能解决这个问题（特别是针对基于文本的Streaming应用）：首先，增加偏移 量以消除所有负数；其次，在数字前面增加0,使所有数字的长度相等。9.2.4节要介绍另一

种方法。

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key, Text value. Context context) throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

context.write(new IntWritable(parser.getAirTemperature()value);

}

}

}

^Override

public int run(String[] args) throws Exception {

Job job = DobBuilder.parselnputAndOutput(thisgetConf()^ args); if (job == null) {

return -1;

}

job.setMappepClass(CleanerMapper.class); job.setOutputKeyClass(IntWritable.class); job.setOutputValueClass(Text.class); job.setNumReduceTasks(0);

job.setOutputFormatClass(SequenceFileOutputFormat.class); SequenceFileOutputFormat.setCompressOutput(job, true);

SequenceFileOutputFormat.setOutputCompressorClass(job, GzipCodec.class); SequenceFileOutputFormat.setOutputCompressionType(job,

CompressionType.BLOCK);

return job.waitForCompletion(true) ? 0 : 1;

},

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new SortDataPreprocessor(), args);

System.exit(exitCode);

}

}

###### 9.2.2部分排序

如9.1.1节所述，在默认情况下，MapReduce根据输入记录的键对数据集排序。 范例9-4则是一个变种，它利用IntWritable键对顺序文件排序。

范例9-4.程序调用默认HashPartitioner按IntWritable键排序顺序文件

public class SortByTemperatureUsingHashPartitioner extends Configured implements Tool {

^Override

public int run(String[] args) throws Exception {

Job job = ]obBuilder.parseInputAndOutput(this, getConf()^ args);

if (job == null) { return -1;

}

job.setInputFormatClass(SequenceFileInputFormat.class); job.setOutputKeyClass(IntWritable.class); job.setOutputFormatClass(SequenceFileOutputFormat.class); SequenceFileOutputFormat.setCompressOutput(job, true); SequenceFileOutputFormat.setOutputCompressorClass(job^ GzipCodec.class); SequenceFileOutputFormat.setOutputCompressionType(job,

CompressionType.BLOCK);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new SortByTemperatureUsingHashPartitioner()

args);

System.exit(exitCode);

}

}

控制排列顺序

键的排列顺序是由RawComparator控制的，规则如下。

\1.    若属性 mapreduce.job.output.key.comparator.class 已经显式 设置，或者通过〕ob类的setSortComparatorClass()方法进行设 置，则使用该类的实例，旧版API使用］obConf类的setOutputKey ComparatorClass()方法。

\2.    否则，键必须是WritableComparable的子类，并使用针对该键类的 已登j己的comparator。

\3.    如果还没有已登记的comparator,则使用RawComparator。 RawComparator将字节流反序列化为一个对象，再由WritableComparable 的compareTo()方法进行操作。

上述规则彰显了为自定义Writable类登记RawComparators优化版本的重要 性，详情可参见5.3.3节介绍的如何为提高速度实现一个RawComparator。同时，通 过定制comparator来重新定义排序顺序也很直观，详情可参见9.2.4节对辅助排 序的讨论。

假设采用30个reducer来运行该程序：°

% hadoop jar hadoop-examples•jar SortByTemperatureUsingHashPartitioner \

-D mapreduce•job.reduces=30 input/ncdc/all-seq output-hashsort

该指令产生30个已排序的输出文件。但是如何将这些小文件合并成一个有序的文 件却并非易事。例如，直接将纯文本文件连接起来无法保证全局有序。

幸运的是，许多应用并不强求待处理的文件全局有序。例如，对于通过键进行查

找来说，部分排序的文件就已经足够了。本书范例代码中的

SortByTemperatureToMapFile 类和 LookupRecordsByTemperature 类对这个

问题进行了探究。通过使用map文件代替顺序文件，第一时间发现一个键所属的 相关分区（使用partitioned是可能的，然后在map文件分区中执行记录查找操作效 率将会更高。

###### 9.2.3全排序

如何用Hadoop产生一个全局排序的文件？最简单的方法是使用一个分区（a single partition）。®但该方法在处理大型文件时效率极低，因为一台机器必须处理所有输 出文件，从而完全丧失了 MapReduce所提供的并行架构的优势。

事实上仍有替代方案：首先，创建一系列排好序的文件；其次，串联这些文件； 最后，生成一个全局排序的文件。主要的思路是使用一个partitioner来描述输出的 全局排序。例如，可以为上述文件创建4个分区，在第一分区中，各记录的气温 小于-10°C，第二分区的气温介于-10°C和0°C之间，第三个分区的气温在0°C和10 °C之间，最后一个分区的气温大于10°C。

该方法的关键点在于如何划分各个分区。理想情况下，各分区所含记录数应该大 致相等，使作业的总体执行时间不会受制于个别reducer.在前面提到的分区方案 中，各分区的相对大小如下所示。

| 气温范围       | <-10°C | [-10°C,0°C) | [0°C, 10°C) | >10°C |
| -------------- | ------ | ----------- | ----------- | ----- |
| 记录所占的比例 | ini%   | 13%         | 17%         | 59%   |

①    参见5.4.1节对排序和合并顺序文件的介绍，了解如何运用Hadoop的排序程序来实现相同的 功能。

②    更好的回答是使用Pig（参见16.6.4节）、Hive（参见17.7.1节）、Crunch或Spark，两者都可以 用一条指令来进行排序。

显然，记录没有均匀划分。只有深入了解整个数据集的气温分布才能建立更均匀 的分区。写一个MapReduce作业来计算落入各个气温桶的记录数，并不困难。例 如，图9-1显示了桶大小为1°C时各桶的分布情况，各点分别对应一个桶。

获得气温分布信息意味着可以建立一系列分布非常均匀的分区。但由于该操作需 要遍历整个数据集，因此并不实用。通过对键空间进行采样，就可较为均匀地划 分数据集。采样的核心思想是只查看一小部分键，获得键的近似分布，并由此构 建分区。幸运的是，Hadoop已经内置若干采样器，不需要用户自己写。

InputSampler类实现了 Sampler接口，该接口的唯一成员方法(即getSample) 有两个输入参数(一个InputFormat对象和一个]ob对象)，返回一系列样本键：

public interface Sampler<K, V> {

K[] getSample(InputFormat<K, V> inf, Job job) throws IOException, InterruptedException;

}

zoi



#### on



-40    -20    0    20    40

Temperature

60



图9-1.天气数据集合的气温分布

该接口通常不直接由客户端调用，而是由InputSampler类的静态方法 writePartitionFile()调用，目的是创建一个顺序文件来存储定义分区的键：

public static <K, V> void writePartitionFile(3ob job, SamplerxK, V> sampler) throws IOException, ClassNotFoundException^ InterruptedException

顺序文件由TotalOrderPartitioner使用，为排序作业创建分区。范例9-5整 合了上述内容。

范例9-5.调用TotalOrderPartitioner按IntWritable键对顺序文件进行全局排序

public class SortByTemperatureUsingTotalOrderPartitioner extends Configured implements Tool {

^Override

public int run(String[] args) throws Exception {

Job job = ]obBuilder.parseInputAndOutput(this, getConf()^ args); if (job == null) {

return -1;

}

job.setInputFormatClass(SequenceFileInputFormat.class); job.setOutputKeyClass(IntWritable.class); job.setOutputFormatClass(SequenceFileOutputFormat.class); SequenceFileOutputFormat.setCompressOutput(job, true); SequenceFileOutputFormat.setOutputCompressorClass(job, GzipCodec.class); SequenceFileOutputFormat.setOutputCompressionType(jobj

CompressionType.BLOCK);

job.setPartitionerClass(TotalOrderPartitioner.class);

InputSampler.Sampler<IntWritableJ Text> sampler = new InputSampler.RandomSampler<IntWritable> Text>(0.1， 10000， 10);

InputSampler.writePartitionFile(job, sampler);

// Add to DistributedCache

Configuration conf = job.getConfiguration();

String partitionFile =TotalOrderPartitioner.getPartitionFile(conf);

URI partitionllri = new URI(partitionFile);    job.addCacheFile(partitionUri);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(

new SortByTemperatureUsingTotalOrderPartitionerOargs);

System.exit(exitCode);

}

}

该程序使用RandomSampler以指定的采样率均匀地从一个数据集中选择样本。在 本例中，采样率被设为0.1。RamdomSampler的输入参数还包括最大样本数和最 大分区（本例中这两个参数分别是10,000和10,这也是InputSampler作为应用 程序运行时的默认设置），只要任意一个限制条件满足，即停止采样。采样器在客 户端运行，因此，限制分片的下载数量以加速采样器的运行就尤为重要。在实践 中，采样器的运行时间仅占作业总运行时间的一小部分。

为了和集群上运行的其他任务共享分区文件，InputSampler需将其所写的分区文 件加到分布式缓存中（参见9.4.2节）。

以下方案别以_5.6°C、13.9°C和22.0°C为边界得到4个分区。易知，新方案比旧方 案更为均匀。

气温范



<5.6°C



[-5.6'C，13.9°C)



[13.9°C,22.0°C)



\> 22.0°C



记录所占的比例 29%



24%



23%



24%



输入数据的特性决定如何挑选最合适的采样器。以SplitSampler为例，它只采 样一个分片中的前〃条记录。由于并未从所有分片中广泛采样，该采样器并不适 合已经排好序的数据。

另一方面，IntervalSample以一定的间隔定期从分片中选择键，因此对于已排 好序的数据来说是一个更好的选择。RandomSampler是优秀的通用采样器。如果 没有采样器可以满足应用需求（记住，采样目的是创建大小近似相等的一系列分 区），则只能写程序来实现Sampler接口。

InputSampler类和TotalOrderPartitioner类的一个好特性是用户可以自由

定义分区数，即reducer的数目。然而，由于TotalOrderPartitioner只用于分区

边界均不相同的时候， 冲突。



而当键空间较小时，设置太大的分区数可能会导致数据



以下是运行方式:

% hadoop jar hadoop-examples.jar SortByTemperatureUsingTotalOrderPartitioner \ -D mapreduce•job•reduces=30 input/ncdc/all-seq output-totalsort

该程序输出30个已经内部排好序的分区。且分区f中的所有键都小于分区中 的键。

①在某些应用中，待处理的数据文件通常要么已经排好序，要么至少已部分排序，例如，天气 数据|就是按时间排序的。对干这类数据，使用SplitSampler会造成采样集合偏倚，因此 最好采用 RandomSampler。

###### 9.2.4辅助排序

MapReduce框架在记录到达reducer之前按键对记录排序，但键所对应的值并没有 排序。甚至在不同的执行轮次中，这些值的排序也不固定，因为它们来自不同的 map任务且这些map任务在不同轮次中的完成时间各不相同。一般来说，大多数 MapReduce程序会避免让reduce函数依赖于值的排序。但是，有时也需要通过特 定的方法对键进行排序和分组等以实现对值的排序。

例如，考虑如何设计一个MapReduce程序以计算每年的最高气温。如果全部记录 均按照气温降序排列，则无需遍历整个数据集即可获得查询结果一获取各年份的 首条记录并忽略剩余记录。尽管该方法并不是最佳方案，但演示了辅助排序的工 作机理。

为此，首先构建一个同时包含年份和气温信息的组合键，然后对键值先按年份升 序排序，再按气温降序排列：

1900 35°C 1900 34°C

1900    34°C

• •參

1901    36°C 1901 35°C

如果仅仅是使用组合键的话，并没有太大的帮助，因为这会导致同一年的记录可 能有不同的键，通常这种情况下记录并不会被送到同一个reducer中。例如， (1900, 35°C)和(1900, 34°C)就可能被送到不同的reducer中。通过设置一个按照键 的年份进行分区的patitioner，可以确保同一年的记录会被发送到同一个reducer 中。但是，这样做还不够。因为partitioner只保证每一个reducer接受一个年份的 所有记录，而在一个分区之内，reducer仍是通过键进行分组的分区：

分区 组

1900 35dC 1900 34X：

1900 34€

1900 36°C 1900 35°C

该问题的最终解决方案是进行分组设置。如果reducer中的值按照键的年份进行分 组，则一个reducer组将包括同一年份的所有记录。鉴于这些记录已经按气温降序

排列，所以各组的首条记录就是这一年的最高气温:

分区 组

1900 35°C 1900 34°C 1900 34°C

1900 36°C

1900 35°C    |

下面对记录按值排序的方法做一个总结。

•定义包括自然键和自然值的组合键。

•根据组合键对记录进行排序，即同时用自然键和自然值进行排序。 •针对组合键进行分区和分组时均只考虑自然键。

\1. Java代码

综合起来便得到范例9-6中的源代码，该程序再一次使用了纯文本输入。

范例9-6.该应用程序通过对键中的气温进行排序来找出最高气温

public class MaxTemperatureUsingSecondarySort extends Configured implements Tool {

static class MaxTemperatureMapper extends MapperxLongWritable, Text, IntPair, NullWritable> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key. Text value,

Context context) throws IOException, InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

context.write(new IntPair(parser.getYearInt()

parser.getAirTemperature()), NullWritable•get());

}

}

}

static class MaxTemperatureReducer extends Reducer<IntPair> NullWritable^ IntPair, NullWritable〉 {

@Override

protected void reduce(IntPair key, Iterable<NullWritable> values. Context context) throws IOException, InterruptedException {

context.write(keyNullWritable•get());

}

}

public static class FirstPartitioner extends Partitioner<IntPair, NullWritable> {

^Override

public int getPartition(IntPair key, NullWritable value, int numPartitions) { // multiply by 127 to perform some mixing return Math.abs(key•getFirst() * 127) % numPartitions;

}

}

public static class KeyComparator extends WritableComparator { protected KeyComparator() {

super(IntPair.classtrue);

}

^Override

public int compare(WritableComparable wl, WritableComparable w2) { IntPair ipl = (IntPair) wl;

IntPair ip2 = (IntPair) w2;

int cmp = IntPair.compare(ipl.getFirstO, ip2.getFirst()); if (cmp != 0) {

return cmp;

}

return -IntPair.compare(ipl.getSecond(), ip2•getSecond()); //reverse

}

}、

public static class GroupComparator extends WritableComparator { protected GroupComparator() {

super(IntPair.classtrue);

}

^Override

public int compare(WritableComparable wl, WritableComparable w2) { IntPair ipl = (IntPair) wl;

IntPair ip2 = (IntPair) w2;

return IntPair. compare (ipl. get FirstO^ ip2.getFirst());

}

}

^Override

public int run(String[] args) throws Exception {

Dob job = ]obBuilder.parseInputAndOutput(this, getConf()^ args); if (job == null) {

return -1;

}

•

job.setMapperClass(MaxTemperatureMapper.class);

job.setPartitionerClass(FirstPartitioner.class); job.setSortComparatorClass(KeyComparator•class); job.setGroupingComparatorClass(GroupComparator•class);

job>SGtReducerClass(MaxTemperatureReducer.class);

job.setOutputKeyClass(IntPair.class); job.setOutputValueClass(NullWritable.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception {

int exitCode = ToolRunner.run(new MaxTemperatureUsingSecondarySort(), args); System.exit(exitCode);

}

}

在上述mapper中，我们利用IntPair类定义了一个代表年份和气温的组合键， 该类实现了 Writable接口。IntPair与TextPair类相似，后者可以参见5.3.3 节的相关讨论。由于可以根据各reducer的组合键获得最高气温，因此无需在值上 附加其他信息，使用NullWritable即可。根据辅助排序，reducei•输出的第一个 键就是包含年份和最高气温信息的IntPair对象。IntPair的toString()方法

返回一个以制表符分隔的字符串， 温对。



而该程序输出一组由制表符分隔的年份/气



许多应用需要访问所有已排序的值，而非像上例一样只需要第一个值。鉴于在 reducer中用户只能够获取第一个键，所以必须通过填充值字段来获取所有已排 序的值，这样不可避免会在键和值之间产生一些冗余信息。

我们创建一个自定义的partitioner以按照组合键的首字段(年份)进行分区，即 FirstPartitioner。为了按照年份(升序)和气温(降序)排列键，我们使用 setSortComparatorClass()设置一个自定义键 comparator(即 KeyComparator)，以

抽取字段并执行比较操作。类似的，为了按年份对键进行分组，我们使用 SetGroupingCompapatorClass来自定义一个分组comparator，只取键的首字段进行 比较，

运行该程序，返回各年的最高气温：

% hadoop jar hadoop-examples.jar MaxTemperatureUsingSecondarySort input/ncdc/all \ > output-secondarysort

% hadoop fs -cat output-secondarysort/part-* | sort | head

①为简单起见，这里自定义的comparator并未经过优化。参见5.3.3节对RawComparator的介 绍，了解如何提高运行效率。

\2. Streaming

我们可以借助Hadoop所提供的一组库来实现Streaming的辅助排序，下面就是用 来进行辅助排序的驱动：

% hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*>jar \ -D stream.num•map•output.key.fields=2 \

-D mapreduce.partition.keypartitioner.options=-kl,1 \

-D mapreduce.job.output•key.comparator.class=\

orgiapache.hadoop•mapred•lib•KeyFieldBasedComparator \

-D mapreduce.partition•keycomparator.options="-kin -k2nr" \

-files secondary_sort_map.pysecondary_sort_reduce.py \

-input input/ncdc/all \

-output output-secondarysort-streaming \

-mapper ch09-mr-features/src/main/python/secondary一sort一map.py \

-partitioner org•apache•hadoop•mapred.lib.KeyFieldBasedPartitioner \ -reducer ch09-mr-features/src/main/python/secondary一sort一reduce.py

范例9-7中的map函数输出年份和气温两个字段。为了将这两个字段看成一个组 合键，需要将stream.num.map.output.key.fields的值设为2。这意味着值 是空的，就像Java程序（范例9-6）—样。

范例9-7.针对辅助排序的map函数（Python版本）

\#!/usr/bin/env python

import re    •

import sys

for line in sys.stdin: val = line.strip()

(year, temp, q) = (val[15:19], int(val[87:92]), val[92:93]) if temp == 9999:

sys•stderr.write("reporter:counter:Temperature,Missing,l\n") elif re.match(,'[01459],\ q):

print H%s\t%sH % (year， temp)

鉴干我们并不期望根据整个组合键来划分数据集，因此可以利用 KeyFieldBasedPatitioner类以组合键的一部分进行划分。具体实现是使用 mapreduce. partition, keypart it ioner. opt ions 酉己置 i亥 partitionero 在上例 中，值-kl,l表示该partitioner只使用组合键的第一个字段。mapreduce.map.

output.key.field.separator属性所定义的字符串能分隔各个字段（默认是制

表符）。

接下来，我们还需要一个comparator以对年份字段升序排列、对气温字段降序排 列，使reduce函数能够方便地返回各组中的第一个记录。Hadoop提供的 KeyFieldBasedComparator类能有效解决这个问题。该类通过mapreduce. partition.keycomparator.options属性来设置排列次序，其格式规范与GNU s加类似o本例中的-kin -k2nr选项表示“首字段按数值顺序排序，字段按数值顺序反

向排序”。与 KeyFieldBasedPartitioner 类似，KeyFieldBasedComparator 使用 map

输出键分隔符将一个键划分成多个字段。

Java版本的程序需要定义分组comparator。但是在Streaming中，组并未以任何方 式划分，因此必须在reduce函数中不断地查看年份是否改变来检测组的边界（范例9-8）。

范例9-8.针对辅助排序的reduce函数(Python版本) #!/usr/bin/env python import sys

last一group = None

for line in sys.stdin: val = line.strip()

(year, temp) = val.split("\tN) group = year if last_group != group:

print val

last_group = group

运行此程序之后，得到与Java版本一样的结果。

最后牢记一点：KeyFieldBasedPartitioner 和 KeyFieldBasedComparator 不仅在 Streaming程序中使用，也能在Java版本的MapReduce程序中使用。

##### 9.3连接

MapReduce能够执行大型数据集间的“连接”（join）操作，但是，自己从头写相关 代码来执行连接的确非常棘手。除了写MapReduce程序，还可以考虑采用更高级 的框架，如Pig、Hive、Cascading、Cruc或Spark等，它们都将连接操作视为整 个实现的核心部分。

先简要地描述待解决的问题。假设有两个数据集：气象站数据库和天气记录数据 集，并考虑如何合二为一。一个典型的查询是：输出各气象站的历史信息，同时

各行记录也包含气象站的元数据信息，如图9-2所示

连接操作的具体实现技术取决于数据集的规模及分区方式。如果一个数据集很大 （例如天气记录）而另外一个集合很小，以至于可以分发到集群中的每一个节点之中 （例如气象站元数据），则可以执行一个MapReduce作业，将各个气象站的天气记 录放到一块（例如，根据气象站ID执行部分排序），从而实现连接。mapper或 reducer根据各气象站ID从较小的数据集合中找到气象站元数据，使元数据能够 被写到各条记录之中。该方法将在9.4节中详细介绍，它侧重于将数据分发到集 群中节点的机制。

Stations    Records



| Station ID番 | Station Name    |
| ------------ | --------------- |
| 011990-99999 | SIHCCAJAVRI     |
| 012650-99999 | TYNSET-HANSMOEN |



![img](Hadoop43010757_2cdb48_2d8748-134.jpg)



| Station ID   | Timestamp    | Temperature |
| ------------ | ------------ | ----------- |
| 012650-99999 | 194903241200 | 111         |
| 012650-99999 | 194903241800 | 78    1     |
| 011990-99999 | 195005150700 | 0           |
| 011990-99999 | 195005151200 | 22          |
| 011990-99999 | 195005151800 | -11         |



Join



| Station ID   | Station Name    | Timestamp    | Temperature |
| ------------ | --------------- | ------------ | ----------- |
| 011990-99999 | SIHCCAJAVRI     | 195005150700 | 0           |
| 011990-99999 | SIHCCAJAVRI     | 195005151200 | 22          |
| 01199099999  | SIHCCAJAVRI     | 195005151800 | -11         |
| 012650-99999 | TYNSET-HANSMOEN | 194903241200 | 111         |
| 012650-99999 | TYNSET-HANSMOEN | 194903241800 | 78          |

连接操作如果由mapper执行，则称为“map端连接”；如果由reducer执行，则 称为“reduce端连接”。

如果两个数据集的规模均很大，以至于没有哪个数据集可以被完全复制到集群的 每个节点，我们仍然可以使用MapReduce来进行连接，至于到底采用map端连接 还是reduce端连接，则取决于数据的组织方式。最常见的一个例子便是用户数据 库和用户活动日志（例如访问日志）。对于一个热门服务来说，将用户数据库（或日 志）分发到所有MapReduce节点中是行不通的。

266 第9章

###### 9.3.1 map端连接

在两个大规模输入数据集之间的map端连接会在数据到达map函数之前就执行连 接操作。为达到该目的，各map的输人数据必须先分区并且以特定方式排序。各 个输入数据集被划分成相同数量的分区，并且均按相同的键（连接键）排序。同一键 的所有记录均会放在同一分区之中。听起来似乎要求非常严格（的确如此），但这的 确合乎MapReduce作业的输出。

map端连接操作可以连接多个作业的输出，只要这些作业的reducer数量相同、键 相同并且输出文件是不可切分的（例如，借助于小于一个HDFS块、或进行gzip压 缩来实现）。在天气例子中，如果气象站文件以气象站ID部分排序，记录文件也 以气象站ID部分排序，而且reducer的数量相同，则就满足了执行map端连接的 前提条件。

利用 org.apache.hadoop.mapreduce. join 包中的 CompositelnputFormat 类来运行 一个map端连接。CompositelnputFormat类的输入源和连接类型（内连接或外连 接）可以通过一个连接表达式进行配置，连接表达式的语法简单。详情与示例可参 见包文档。

org.apache. hadoop.examples. Doin是一个通用的执行map端连接的命令行程 序样例。该例运行一个基于多个输人数据集的mapper和reducer的MapReduce作 业，以执行给定的连接操作。

###### 9.3.2 reduce 端连接

由于reduce端连接并不要求输入数据集符合特定结构，因而reduce端连接比map 端连接更为常用。但是，由于两个数据集均需经过MapReduce的shuffle过程，所 以reduce端连接的效率往往要低一些。基本思路是mapper为各个记录标记源，并 且使用连接键作为map输出键，使键相同的记录放在同一个reducer中。以I、*技术 能帮助实现reduce端连接。

1.多输入

数据集的输入源往往有多种格式，因此可以使用Multiplelnputs类（参见8.2.4 节）来方便地解析和标注各个源。

2.辅助排序

如前所述，reducer将从两个源中选出键相同的记录，但这些记录不保证是经过排 序的。然而，为了更好地执行连接操作，一个源的数据排列在另一个源的数据前 是非常重要的。以天气数据连接为例，对应每个键，气象站记录的值必须是最先 看到的，这样reducer能够将气象站名称填到天气记录之中再马上输出。虽然也可 以不指定数据传输次序，并将待处理的记录缓存在内存之中，但应该尽量避免这 种情况，因为其中任何一组的记录数量可能非常庞大，远远超出reducer的可用内 存容量。

9.2.4节介绍如何对reducer所看到的每个键的值进行排序，所以在此也用到了辅 助排序技术。

为标记每个记录，我们使用第5章的TextPair类，包括键（存储气象站ID）和

“标记”。在这里，“标记”是一个虚拟的字段，其唯一目的是用于记录的排 序，使气象站记录比天气记录先到达。一种简单的做法就是：对于气象站记录，

“标记”值为0;对于天气记录，“标记”值为1。范例9-9和范例9-10分别描述了执 行该任务的两个mapper类。

范例9-9.在reduce端连接中，标记气象站记录的mapper

public class DoinStationMapper

extends MapperxLongWritable, Text, TextPair, Text> {

private NcdcStationMetadataParser parser = new NcdcStationMetadataParser();

^Override

protected void map(LongWritable key. Text value. Context context) throws IOException, InterruptedException {

if (parser.parse(value)) {

context .write(new TextPair(parser .getStationldO, n0")， new Text(parser.getStationName()));

}

}

}

范例9-10.在reduce端连接中标记天气记录的mapper

public class JoinRecordMapper

extends Mapper<LongWritable, Text, TextPair^ Text> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key, Text value. Context context) throws IOException, InterruptedException {

parser.parse(value);

context.write(new TextPair(parser.getStationId("1"), value);

}

}

reducer知道自己会先接收气象站记录。因此从中抽取出值，并将其作为后续每条 输出记录的一部分写到输出文件。如范例9-11所示。

范例9-11.用于连接已标记的气象站记录和天气记录的reducer

public class JoinReducer extends Reducer<TextPair> Text, Text, Text> {

^Override

protected void reduce(TextPair key， Iterable<Text> values, Context context) throws IOException^ InterruptedException {

Iterator<Text> iter = values.iterator^);

Text stationName = new Text(iter.next()); while (iter.hasNext()) {

Text record = iter.next();

Text outvalue = new Text(stationName.toString() + "\tH + record.toString()); context.write(key.getFirst(), outvalue);

}

}

}

上述代码假设天气记录的每个气象站ID恰巧与气象站数据集中的一条记录准确匹 配。如果该假设不成立，则需要泛化代码，使用另一个TextPair将标记放入值 的对象中。reduce()方法在处理天气记录之前，要能够区分哪些记录是气象站名 称，检测(和处理)缺失或重复的记录。

![img](Hadoop43010757_2cdb48_2d8748-135.jpg)



在reducer的迭代部分中，对象被重复使用(为了提高效率)。因此，从第一个 Text对象获得站点名称(即stationName)就非常关键。

Text stationName = new Text(iter.next());

如果不执行该语句，stationName就会指向上一条记录的值，这显然是错的。

将作业连接在一起通过驱动类来完成，如范例9-12所示。这里，关键点在于根据

组合键的第一个字段(即气象站ID)进行分区和分组，即使用一个自定义的

partitioner(即 KeyPartitioner)和一个自定义的分组 comparator (FirstComparator,作为 TextPair 的嵌套类)。

范例9-12.对天气记录和气象站名称执行连接操作

public class DoinRecordWithStationName extends Configured implements Tool {

public static class KeyPartitioner extends Partitioner<TextPair> Text> { ^Override

public int getPartition(TextPair key. Text value, int numPartitions) { return (key.getFirst().hashCode() & Integer.MAX一VALUE) % numPartitions;

} "

}

^Override

public int run(String[] args) throws Exception { if (args.length != 3) {

3obBuilder.printUsage(this., M<ncdc input>〈station input> <output>"); return -1;

}

Dob job = new ]ob(getConf(), "]oin weather records with station names") job.set3arByClass(getClass());

Path ncdcInputPath = new Path(args[0]);

Path stationlnputPath = new Path(args[l]);

Path outputPath = new Path(args[2]);

MultipleInputs.addInputPath(job, ncdcInputPath,

TextInputFormat.class, JoinRecordMapper.class);

MultipleInputs.addInputPath(job, stationlnputPath^

TextInputFormat.class, DoinStationMapper.class);

FileOutputFormat>setOutputPath(job, outputPath);

job.setPartitionerClass(KeyPartitioner.class);

job.setGroupingComparatorClass(TextPair.FirstComparator.class);

job.setMapOutputKeyClass(TextPair.class);

job.setReducerClass(DoinReducer.class);

job.setOutputKeyClass(Text.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new 3oinRecordWithStationName(), args); System•exit(exitCode);

}

}

在样本数据上运行这个程序，获得以下输出：

011990-99999 SIHCCA3AVRI    0067011990999991950051507004...

011990-99999 SIHCCA3AVRI    0043011990999991950051512004...

011990-99999 SIHCCA3AVRI    0043011990999991950051518004...

012650-99999 TYNSET-HANSMOEN 012650-99999 TYNSET-HANSMOEN



0043012650999991949032412004...

0043012650999991949032418004...



##### 9.4边数据分布

“边数据” (side data)是作业所需的额外的只读数据，以辅助处理主数据集。所面 临的挑战在于如何使所有map或reduce任务(这些任务散布在集群内部)都能够方 便而高效地使用边数据。

###### 9.4.1利用JobConf来配置作业

Configuration 类（或者旧版 MapReduce API 的 JobConf 类）的各种 setter 方法能

够方便地配置作业的任一键-值对。如果仅需向任务传递少量元数据则非常有用

在任务中，用户可以通过Context类的getConfiguration()方法获得配置信

息。



__ (在旧版API中，做法更加复杂一点：需要重写Mapper或者Reducer类的 configure()方法，并调用传入DobConf对象的getter方法。通常情况下，可 将数据以实例字段的形式保存，使得其可在map()或者reduce()方法中使用。) 一般情况下，基本类型足以应付元数据编码。但对于更复杂的对象，用户要么自 己处理序列化工作(这需要实现一个对象与字符串之间的双向转换机制)，要么使用

Hadoop 提供的 Stringifier 类。DefaultStringif ier 使用 Hadoop 的序列化框

架来序列化对象。详情参见5.3节。

但是这种机制会加大MapReduce组件的内存开销压力，因此，并不适合传输多达 几千字节的数据量。作业配置总是由客户端、application master和任务JVM读 取。每次读取配置时，所有项都被读入到内存(即使暂时不用的属性项也不例外)。

###### 9.4.2分布式缓存

与在作业配置中序列化边数据的技术相比，Hadoop的分布式缓存机制更受青睐， 它能够在任务运行过程中及时地将文件和存档复制到任务节点以供使用。为了节 约网络带宽，在每一个作业中，各个文件通常只需要复制到一个节点一次。

1.用法

对于使用GenericOptionsParser(本书中多处程序均用到该类，参见6.2.2节的相关讨 论）的工具来说，用户可以使用-files选项指定待分发的文件，文件内包含以逗 号隔开的URI列表。文件可以存放在本地文件系统、HDFS或其他Hadoop可读文 件系统（例如S3）之中。如果尚未指定文件系统，则这些文件被默认是本地的。即 使默认文件系统并非本地文件系统，这也是成立的。

用户可以使用-archives选项向自己的任务中复制存档文件（JAR文件、ZIP文 件、tar文件和gzipped tar文件），这些文件会被解档到任务节点。-libjars选项 会把JAR文件添加到mapper和reducer任务的类路径中。如果作业JAR文件并没 包含库JAR文件，这点会很有用。

以下指令显示如何使用分布式缓存来共享元数据文件，从而得到气象站的名称:

% hadoop jar hadoop-examples.jar \

MaxTemperatureByStationNameUsingDistributedCacheFile \

-files input/ncdc/metadata/stations-fixed-width.txt input/ncdc/all output

该命令将本地文件stations-fixed-width. M未指定文件系统，从而被自动解析为本

地文件)复制到任务节点，从而可以查找气象站名称。范例9-13描述了类 MaxTemperatureByStationNameUsingDistributedCacheFile 的代码。

范例9~13.查找各气象站的最高气温并显示气象站名称，气象站文件是一个分布式缓存文件

public class MaxTemperatureByStationNameUsingDistributedCacheFile extends Configured implements Tool {

static class StationTemperatureMapper extends Mapper<LongWritable> Text, Text, IntWritable> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key. Text value. Context context) throws IOException^ InterruptedException {

parser.parse(value);

if (parser.isValidTemperature()) {

context.write(new Text(parser.getStationld())， new IntWritable(parser.getAirTemperature()));

}

}

}

static class MaxTemperatureReducerWithStationLookup extends ReducerxText, IntWritable^ Text, IntWritable> {

private NcdcStationMetadata metadata;

^Override

protected void setup(Context context)

throws IOExceptionj InterruptedException {

metadata = new NcdcStationMetadata();

metadata.initialize(new File("stations-fixed-width.txtn));

^Override

protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {

String stationName = metadata.getStationName(key.toString());

int maxValue = Integer.MIN_VALUE; for (IntWritable value : values) {

maxValue = Math.max(maxValue, value.get());

}

context.write(new Text(stationName), new IntWritable(maxValue));

}

}

(^Override

public int run(String[] args) throws Exception {

Dob job = JobBuilder.parselnputAndOutputCthis^ getConf()^ args); if (job == null) {

return -1;

}

job.setOutputKeyClass(Text.class);

job.setOutputValueClass(IntWritable.class);

job.setMapperClass(StationTemperatureMapper.class);

job.setCombinerClass(MaxTemperatureReducer.class);

job.setReducerClass(MaxTemperatureReducerWithStationLookup.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(

new MaxTemperatureByStationNameUsingDistributedCacheFile()args); System.exit(exitCode);

}

}

该程序通过气象站查找最高气温，因此mapper (StationTemperatureMapper)仅 仅输出(气象站ID，气温)对。对于combiner,该程序重用MaxTemperatureReducer (参见第2章和第6章)来为map端的map输出分组获得最高气温。reducer

(MaxTemperatureReducerWithStationLookup)则有所不同，它不仅要查找最高

气温，还需要根据缓存文件査找气象站名称。

该程序调用reducer的setup()方法来获取缓存文件；输入参数是文件的原始名 称，文件的路径与任务的工作目录相同。

1当文件无法整个放到内存中时，可以使用分布式缓存进行复制。由于充当了一 ■k 种在盘检索格式(参见5.4.2节)，Hadoop map文件在这方面非常有用。由干 jWL map文件是一组已定义目录结构的文件，用户可以将这些文件整理成存档格式 •叫、(JAR, ZIP, tar或gzippedtar)，再用-archives选项将其加入缓存。

以下是输出的小片段，显示部分气象站的最高气温值。

| PEATS RIDGE     | WARATAH | 372  |
| --------------- | ------- | ---- |
| STRATHALBYN     | RACECOU | 410  |
| SHEOAKS ANS     |         | 399  |
| WANGARATTA AERO | 409     |      |
| MOOGARA         |         | 334  |
| MACKAY AERO     |         | 331  |

2.工作机制

当用户启动一个作业，Hadoop会把由-files、-archives和-libjars等选项所 指定的文件复制到分布式文件系统(一般是HDFS)之中。接着，在任务运行之前， 节点管理器将文件从分布式文件系统复制到本地磁盘(缓存)使任务能够访问文件。 此时，这些文件就被视为“本地化”了。从任务的角度来看，这些文件就已经在 那儿了，以符号链接的方式指向任务的工作目录。此外，由-libjars指定的文件 会在任务启动前添加到任务的类路径(classpath)中。

节点管理器为缓存中的文件各维护一个计数器来统计这些文件的被使用情况。当 任务即将运行时，该任务所使用的所有文件的对应计数器值增1;当任务执行完毕 之后，这些计数器值均减1。仅当文件不在使用中(此时计数达到0)，才有资格删 除。当节点缓存的容量超过一定范围(默认10 GB)时，需要根据最近最少使用原则 删除文件以腾出空间来装载新文件。缓存的大小可以通过属性yarn.nodemanager, localizer.cache.target-size-mb 来酉己置。

尽管该机制并不确保在同一个节点上运行的同一作业的后续任务肯定能在缓存中 找到文件，但是成功的概率相当大。原因在于作业的多个任务在调度之后几乎同 时开始运行，因此，不会有足够多的其他作业在运行而导致原始任务的文件从缓 存中被删除。

3.分布式缓存API

由于可以通过GenericOptionsParser间接使用分布式缓存(如范例9-13所示)， 大多数应用不需要使用分布式缓存API。然而，如果没有使用GenericOptionsParser, 那么可以使用〕ob中的API将对象放进分布式缓存中®。以下是]ob中相关的 方法：

public void addCacheFile(URI uri) public void addCacheArchive(URI uri) public void setCacheFiles(URI[] files) public void setCacheArchives(URI[] archives) public void addFileToClassPath(Path file) public void addArchiveToClassPath(Path archive) public void createSymlink()

在缓存中可以存故两类对象：文件(files)和存档(achives)。文件被直接放置在任务

节点上，而存档则会被解档之后再将具体文件放置在任务节点上。每种对象类型

都包含三种方法：addCacheXXXX() , setCacheX狀Xs()和 addXXXXToClassPath()。其中，addCacheXXXXO方法将文件或存档添加到分布 式缓存，setCacheXXXXs()方法将一次性向分布式缓存中添加一组文件或存档(之 前调用所生成的集合将被替换)，addXXXXToClassPath()方法将文件或存档添加 到MapReduce任务的类路径。表9-7对上述API方法与GenericOptionsParser 选项(参见表6-1)做了一个比较。

①如果用的是老版本的 MapReduce API,可以在 org.apache.hadoop.filecache. DistributedCache中找到同样的方法。

表9-7.分布式缓存API

| Job的API名称addCacheFile(URI uri) setCacheFiles(URI[] files) | GenericOptionsParser 的等价选项-files fiLel,fiLe2” | 说明• •将文件添加到分布式缓存，以备 将来被复制到任务节点     |
| ------------------------------------------------------------ | -------------------------------------------------- | ------------------------------------------------------------ |
| addCacheArchive(URI uri)                                     | -archives                                          | 将存档添加到分布式缓存，以备                                 |
| setCacheArchives(URI[] files)                                | archivel^ archive2J …                              | 将来被复制到任务节点，并在节点 解档                          |
| addFileToClassPath                                           | -libjars                                           | 将文件添加到分布式缓存，以备                                 |
| (Path file)                                                  | jar% jar2> …                                       | 将来被复制到MapReduce任务的 类路径中。文件并不会被解档，因此适合向类路径添加JAR文件 |
| addArchiveToClassPath (Path archive)                         | 无                                                 | 将存档添加到分布式缓存，以备将 来解档、添加^ MapReduce的类路径 |

中。当想向类路径添加目录和文 件时，这种方式比较有用，因为用 户可以创建一个包含指定文件的存 档。此外，用户也可以创建一个JAR 文件，并使用 addFileTo ClassPathO， 效果相同

add和set方法中的输入参数URI是指在作业运行时位干共享文件系统中的（一 组）文件。而GenericOptionsParser选项（例如，-files）所指定的文件可以 是本地文件；如果是本地文件的话，则会被复制到默认的共享文件系统（一般是

HDFS)O



这也是使用Java API和使用GenericOptionsParser的关键区别：Java API的 add和set方法不会将指定文件复制到共享文件系统中，但GenericOptionsParser

会这样做。

从任务中获取分布式缓存文件在工作机理上和以前是一样的：通过名称访问本地 化的文件，如范例9-13中所示。之所以起作用，是因为MapReduce总会在任务的 工作目录和添加到分布式缓存中的每个文件或存档之间建立符号化链接。®存档 被解档后就能使用嵌套的路径访问其中的文件。

①在Hadoop 1中，本地化文件并不总是符号化链接的，有时必须得用］obContext提供的方法 来获取本地化文件的路径。Had00p2中这个限制没有了。

##### 9.5 MapReduce 库类

Hadoop还为mapper和reducer提供了一个包含了常用函数的库。表9-8简要描述 了这些类。如需了解详细用法，可参考相关Java文档。

表 9-8. MapReduce 库的类

| 类的名称ChainMapper, ChainReducer                            | 描述在一个mapper中运行多个mapper，再运行一个 reducer，随后在一个reducer中运行多个mapper0 （符号表示：M+RM*,其中M是mapper，R是 reducer。）与运行多个MapReduce作业相比，该方案 能够显著降低磁盘I/O开销 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| FieldSelectionMapReduce（旧版 API）：FieldSelectionMapper和 FieldSelectionReducer （新版 API） | 能从输入键和值中选择字段（类似Unix的cut命 令），并输出键和值的mapper和reducer |
| IntSumReducer, LongSumReducer                                | 对各键的所有整数值执行求和操作的reducer                      |
| InverseMapper                                                | 一个能交换键和值的mapper                                     |
| MultithreadedMapRunner （旧版 API） MultithreadedMapper （新版 API） | 一个能在多个独立线程中分别并发运行mappei•的 mapper（或者旧版API中的map runner）0该技术对于 非CPU受限的mapper比较有用 |
| TokenCounterMapper                                           | 将输人值分解成独立的单词（使用Java的 StringTokenizer）并输出每个单词和计数值丨的mapper |
| RegexMapper                                                  | 检査输入值是否匹配某正则表达式，输出匹配字符 串和计数为1的mapper |

![img](Hadoop43010757_2cdb48_2d8748-137.jpg)



9-2.两个数据集的内连接



#### 第III部分
