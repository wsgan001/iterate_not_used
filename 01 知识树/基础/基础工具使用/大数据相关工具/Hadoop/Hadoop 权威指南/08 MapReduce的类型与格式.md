### MapReduce的类型与格式

MapReduce数据处理模型非常简单：map和reduce函数的输入和输出是键-值对。 本章深入讨论MapReduce模型，重点介绍各种类型的数据（从简单文本到结构化的 二进制对象）如何在MapReduce中使用。

##### 8.1 MapReduce 的类型

Hadoop的MapReduce中，map函数和reduce函数遵循如下常规格式：

map： (Kl, VI) -* list(K2， V2) reduce： (K2, list(V2)) 一 list(K3, V3)

一般来说，map函数输入的键/值类型(Kl和VI坏同于输出类型(K2和V2)。然而， reduce函数的输入类型必须与map函数的输出类型相同，但reduce函数的输出类 型(K3和V3)可以不同于输入类型。例如以下Java接口代码：

public class MapperxKEYIN, VALUEIN, KEYOUT, VALUEOUT〉 { public class Context extends MapContext<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {

}

protected void map(KEYIN key, VALUEIN value. Context context) throws IOException, InterruptedException {

II ...

}

}

public class ReducerxKEYIN, VALUEIN, KEYOUT, VALUEOUT〉 {

public class Context extends ReducerContext<KEYIN^ VALUEIN, KEYOUT, VALUEOUT〉 {

//…

}

protected void reduce(KEYIN key, Iterable<VALUEIN> values^ Context context) throws IOException,

InterruptedException {

"...

}

}

Context类对象用于输出键-值对， 法的说明如下：

此它们通过输出类型参数化，这样write()方



Public void write(KEYOUT key, VALUEOUT value) throws IOException, InterruptedException

由于Mapper和Reducer是单独的类，因此类型参数可能会不同，所以Mapper中 KEYIN(say)的实际类型可能与Reducer中同名的类型参数(KEYIN)的类型不一致。例 如，在前面章节的求最高温度例子中，Mapper中KEYIN为LongWritable类型，而 Reducer中为Text类型。

类似的，即使map输出类型与reduce的输入类型必须匹配，但这在Java编译器中 并不是强制要求的。

类型参数(type parameter)的命名不同于抽象类型的命名(KEYIN对应于K1等)，但它 ff］的形式是相同的。

如果使用combiner函数，它与reduce函数(是Reducer的一个实现)的形式相同，

不同之处是它的输出类型是中间的键-值对类型(K2和V2)，这些中间值可以输入 reduce 函数：

map: (Kl, VI) ->list(K2, V2)

combiner: (K2，list(V2)) -<ist(K2，V2) reduce: (K2, list(V2)) list(K3, V3)

combiner函数与reduce函数通常是一样的，在这种情况下，K3与K2类型相同， V3与V2类型相同。

partition函数对中间结果的键-值对(K2和V2)进行处理，并且返回一个分区索引 (partition index)。实际上，分区由键单独决定(值被忽略)。

partition： (K2, V2) 一 integer

或用Java：

public abstrack class Partitioner<KEYJ VALUE〉 { public abstract int gerPartition(KRY key, VALUE value, int numPartitions);

}

在旧版本的API(见附录D)中，MapReduce的用法非常类似，类型参数的实际命 名也为Kl、VI等。在新旧版本API中类型上的约束也是完全一样的：

public interface Mapper<Kl, VI, K2, V2> extends ]obConfigurable, Closeable { void map(Kl key, VI value, OutputCollector<K2, V2> output, Reporter reporter) throws

IOExceptionj

public interface Reducer<K2, V2, K3^ V3> extends DobConfigurable^ Closeable { void reduce(K2 key, Iterator<V2> values,

OutputCollector<K3, V3> output, Reporter reporter) throws IOException;

}

public interface Partitioner<K2, V2> extends JobConfigurable { int getPartition(K2 key, V2 value, int numPartitions);

}

这些理论对配置MapReduce作业有帮助吗？表8 -1总结了新版本API的配置选项 (表8-2为旧版本API的)，把属性分为可以设置类型的属性和必须与类型相容的属 性。

输入数据的类型由输入格式进行设置。例如，对应于TextlnputFormat的键类型 是LongWritable，值类型是Text。其他的类型通过调用］ob类的方法来进行显 式设置(旧版本API中使用JobConf类的方法)。如果没有显式设置，则中间的类 型默认为(最终的)输出类型，也就是默认值LongWritable和Text。因此，如果 K2与！＜3是相同类型，就不需要调用setMapOutputKeyClass(),因为它将调用 setOutputKeyClass()来设置；同样，如果V2与V3相同，只需要使用 setOutputValueClass()0

这些为中间和最终输出类型进行设置的方法似乎有些奇怪。为什么不能结合 mapper和reducer导出类型呢？原来，Java的泛型机制有很多限制：类型擦除 (type erasure)导致运行过程中类型信息并非一直可见，所以Hadoop不得不进行明 确设定。这也意味着可能会在MapReduce配置的作用中遇到不兼容的类型，因为 这些配置在编译时无法检查。与MapReduce类型兼容的设置列在表8-1中。类型 冲突是在作业执行过程中被检测出来的，所以一个比较明智的做法是先用少量数 据跑一次测试任务，发现并修正任何一个类型不兼容的问题。

表8-1.新的MapReduce API中的设置类型



![img](Hadoop43010757_2cdb48_2d8748-116.jpg)



![img](Hadoop43010757_2cdb48_2d8748-117.jpg)



:懸:s    :



属性设置方法



输入类型 K1 V1



中间类型

:

K2 V2



输出类型 K3 V3



•，-燃 &    -

可以设置类型的属性

| mapreduce.job.inputformat.class             | setlnputFormatClass()        |      | 本   |      |      |      |      |
| ------------------------------------------- | ---------------------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| mapreduce.map.output.key.class              | setMapOutputKeyClass()       |      |      | 本   |      |      |      |
| mapreduce.map.output.value.class            | setMapOutputValueClass()     |      |      |      | 本   |      |      |
| mapreduce.job.output.key.class              | setOutputKeyClass()          |      |      |      |      | 本   |      |
| mapreduce.job.output.value.class            | setOutputValueClass()        |      |      |      |      |      | *    |
| 类型必须一致的属性mapreduce.job.map.class   | setMapperClass()             | 本   | *    | 本   | 木   |      |      |
| mapreduce.job.combine.class                 | setCombinerClass()           |      |      | 本   | 本   |      |      |
| mapreduce.job.partitioner.class             | setPartitionerClass()        |      |      | 本   | ♦    |      |      |
| mapreduce.job.output.key.comparator.class   | setSortComparatorClass()     |      |      |      |      |      |      |
| mapreduce.job.output.group•comparator.class | setGroupingComparatorClass() |      |      | *    |      |      |      |
| mapreduce.job.reduce.class                  | setReducerClass()            |      |      | 本   | ♦    | 本   | 本   |
| mapreduce.job.outputformat.class            | setOutputFormatClass()       |      |      |      |      | 本   | 本   |



表8-2 旧版本MapReduce API的设置类型

属性



属性设置方法



输入类型 中间类型 输出类型 K1 V1 K2 V2 K3 V3

可以设置类型的属性

| mapred.input.format.class    | setlnputFormat()         | *    | 木   |
| ---------------------------- | ------------------------ | ---- | ---- |
| mapred.mapoutput•key.class   | setMapOutputKeyClass()   |      | *    |
| mapred.mapoutput.value.class | setMapOutputValueClass() |      | 本   |
| mapred.output.key.class      | setOutputKeyClass()      |      | 本   |
| mapred.output.value.class    | setOutputValueClass()    |      | 本   |

类型必须一致的属性

| mapred.mapper.class                | setMapperClass()                   | *    | 本   | ♦    | *    |      |      |
| ---------------------------------- | ---------------------------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| mapred.map.runner.class            | setMapRunnerClass()                |      |      |      |      |      |      |
| mapred.combiner.class              | setCombinerClass()                 |      |      | 本   | *    |      |      |
| mapred.partitioner.class           | setPartitionerClass()              |      |      | ♦    | *    |      |      |
| mapred.output.key.comparator•class | setOutputkeyComparatorelass()      |      |      | *    |      | •    |      |
| mapred.output.value.groupfn•class  | setOutputValueGroupingComparator() |      |      | 木   |      |      |      |
| mapred.reducer.class               | setReducerClass()                  |      |      | 本   | ♦    | ♦    | *    |

mapred.output.format.class

setOutputFormat()

*

*

###### 8.1.1 默认的MapReduce作业

如果不指定mapper或reducer就运行MapReduce,会发生什么情况?我们运行一个 最简单的MapReduce程序来看看：

public class MinimalMapReduce extends Configured implements Tool {

^Override

public int run(String[] args) throws Exception { if (args.length != 2) {

System.err.prirrtfC'Usage: %s [generic options] <input> <output>\n.、 getClass().getSimpleName());

ToolRunner.printGenericCommandUsage(System.err); return -1;

}

Dob job = new 〕ob(getConf());

job.set3arByClass(getClass());

FilelnputFormat•addInputPath(conf, new Path(args[0])); FileOutputFormat.setOutputPath(confnew Path(args[l])); return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new MinimalMapReduce(), args); System.exit(exitCode);

}

}

我们唯一设置的是输入路径和输出路径。在气象数据的子集上运行以下命令:

% hadoop MinimalMapReduce "input/ncdc/all/190{l,2}.gz" output

输出目录中得到命名为part-r-00000的输出文件。这个文件的前几行如下(为适应 页面而进行了截断处理)：

0-0029029070999991901010106004f643334^23450FM- 124«00599999V0202701N01591...

0->0035029070999991902010106004^643334«23450FM-12+000599999V0201401N01181... 135-*0029029070999991901010113004+643334B23450FM-124€00599999V0202901N00821... 141—0035029070999991902010113004^643334€23450FM-124«00599999V0201401N01181... 270-*0029029070999991901010120004+643334€23450FM-124«00599999V0209991C00001... 282-*0035029070999991902010120004^643334€23450FM-12+000599999V0201401N01391...

5—行以整数开始，接着是制表符(Tab)，然后是一段原始气象数据记录。虽然这 牛不是一个有用的程序，但理解它如何产生输出确实能够洞悉Hadoop是如何使用 K认设置运行MapReduce作业的。范例8-1的示例与前面MinimalMapReduce完 K的事情一模一样，但是它显式地把作业环境设置为默认值。

范例8-1.最小的MapReduce驱动程序，默认值显式设置

public class MinimalMapReduceWithDefaults^extends Configured implements Tool {

^Override

public int run(String[] args) throws IOException {

Job job = ]obBuilder.parseInputAnOutput(this, getConf(), args);

if (job == null) { return -1;

}

job.setInputFormat(TextInputFormat.class);

job.setMapperClass(Mapper.class);

job.setMapOutputKeyClass(LongWritable•class); job.setMapOutputValueClass(Text.class);

job.setPartitionerClass(HashPartitioner.class);

job.setNumReduceTasks(l);

job.setReducerClass(Reducer.class);

job«setOutputKeyClass(LongMritable.class); job•setOutputValueClass(Text.class);

job.setOutputFormat(TextOutputFormat.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new MinimalMapReduceWithDefaults(), args);

System.exit(exitCode);

}

}

通过把打印使用说明的逻辑抽取出来并把输入/输出路径设定放到一个帮助方法 中，实现对run()方法的前几行进行了简化。几乎所有MapReduce驱动程序都有 两个参数(输入与输出)，所以此处进行这样的代码约简是可行的。以下是 〕obBuilder类中的相关方法，供大家参考：

public static Job parseInputAndOutput(Tool tool. Configuration conf String[] args) throws IOException {

if (args.length != 2) { printUsage(tool, "<input> <output>"); return null;

}

Job job = new 3ob(conf);

job.setDarByClass(tool.getClass());

FilelnputFormat.addlnputPath(job, new Path(args[0]));

FileOutputFormat.setOutputPath(job4 new Path(args[l])); return job;

}

public static void printUsage(Tool tool. String extraArgsUsage) { System.err.printf("Usage: %s [genericOptions] %s\n\n",

tool.getClass()•getSimpleName(), extraArgsUsage); GenericOptionsParser.printGenericCommandUsage(System.err);

}

回到范例8-1中的MinimalMapReducewithDefaults类，虽然有很多其他的默认 作业设置，但加粗显示的部分是执行一个作业最关键的代码。接下来我们逐一讨 论。

在默认的输入格式是TextlnputFormat,它产生的键类型是LongWritable（文件中 每行中开始的偏移量值），值类型是Text（文本行）。这也解释了最后输出的整数的 含义：行偏移量。

默认的mapper是Mapper类，它将输入的键和值原封不动地写到输出中：

public class Mapper<KEYIN, VALUEIN, KEYOUT, VALUEOUT〉 { protected void map(KEYIN key, VALUEIN value，

Context context) throws IOException， InterruptedException { context.write((KEYOUT) key，(VALUEOUT) value);

}

}

Mapper是一个泛型类型(generic type),它可以接受任何键或值的类型。在这个例 子中，map的输入输出键是LongWritable类型，map的输入输出值是Text类 型。

默认的partitioner是HashPartitioner,它对每条记录的键进行哈希操作以决定 该记录应该属于哪个分区。每个分区由一个reduce任务处理，所以分区数等于作 业的reduce任务个数：

public class HashPartitionerxK, V> extends Partitioner<KJ V> {

public int getPartition(K key, V value,

int numPartitions) {

return (key.hashCode() & Integer.MAX VALUE) % numPartitions;

}

键的哈希码被转换为一个非负整数，它由哈希值与最大的整型值做一次按位与操 作而获得，然后用分区数进行取模操作，来决定该记录属于哪个分区索引。

默认情况下，只有一个reducer,因此，也就只有一个分区，在这种情况下，由于 所有数据都放入同一个分区，partitioner操作将变得无关紧要了。然而，如果有多 个reduce任务，了解HashPartitioner的作用就非常重要。假设基于键的散列

函数足够好，那么记录将被均匀分到若干个reduce任务中，这样，具有相同键的 记录将由同一个reduce任务进行处理。

你可能已经注意到我们并没有设置map任务的数量。原因是该数量等于输入文件 被划分成的分块数，这取决于输人文件的大小以及文件块的大小(如果此文件在 HDFS中)。关于控制块大小的操作，可以参见8.2.1节。

选择reducer的个数

对Hadoop新手而言，单个reducer的默认配置很容易上手。但在真实的应用 中，几乎所有作业都把它设置成一个较大的数字，否则由于所有的中间数据都 会放到一个reduce任务中，作业处理极其低效。

为一个作业选择多少个reducer与其说是一门技术，不如说更多是一门艺术。 由于并行化程度提高，增加reducer的数量能缩短reduce过程。然而，如果做 过了，小文件将会更多，这又不够优化。一条经验法则是，目标reducer保持 在每个运行5分钟左右、且产生至少一个HDFS块的输出比较合适。

默i人的reducer是Reducer类型，它也是一个泛型类型，只是把所有的输入写到 输出中：

public class Reducer<KEYIN, VALUEIN, KEYOUT, VALUEOUT〉 { protected void reduce(KEYIN key, Iterable<VALUEIN> values, Context context

Context context) throws IOException^ InterruptedException { for (VALUEIN value: values) {

context.write((KEYOUT) key， (VALUEOUT) value);

}

}

}

对干这个任务来说，输出的键是LongWritable类型，而值是Text类型。事实 上，对于这个MapReduce程序来说，所有键都是LongWritable类型，所有值都 是Text类型，因为它们是输入键/值，并且map函数和reduce函数是恒等函数。 然而，大多数MapReduce程序不会一直用相同的键或值类型，所以就像上一节所 描述的那样，必须配置作业来声明使用的类型。

记录在发送给reducer之前，会被MapReduce系统进行排序。在这个例子中，键 是按照数值的大小进行排序的，因此来自输入文件中的行会被交叉放入一个合并 后的输出文件。

默认的输出格式是TextOutputFormat,它将键和值转换成字符串并用制表符分 隔开，然后一条记录一行地进行输出。这就是为什么输出文件是用制表符(Tab)分 隔的，这是TextOutputFormat的特点。

###### 8.1.2默认的Streaming作业

在Streaming方式下，默认的作业与Java方式是相似的，但也有差别。基本形式

如下：

% hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \ -input input/ncdc/sample.txt \

-output output \

-mapper /bin/cat

如果我们开发一个非Java的mapper,并且当前是默认的文本模式(-io text)，那 么Streaming会做一些特殊的处理。它并不会把键传递给mapper，而是只传递值。对于 其他输入类型，将stream.map.input.ignoreKey设置为true也可以达到相同 的效果。这样做事实上是非常有用的，因为键只是文件中的行偏移量，而值是行 中的数据，这才是几乎所有应用都关心的内容。这个作业的效果就是对输入的值 进行排序。

将更多的默认设置写出来，那么命令行看起来如下所示（注意，Streaming使用的是 旧版本 MapReduce API 类）：

% hadoop jar $HADOOPJHOME/share/hadoop/tools/lib/hadoop-streaming-*•jar \

-input input/ncdc/sample.txt \

-output output \

-inputformat org.apache.hadoop•mapred•TextlnputFormat \

-mapper /bin/cat \

-partitioner org.apache.hadoop.mapred•lib.HashPartitioner \

■numReduceTasks 1 \

-reducer org•apache•hadoop•mapred•lib•IdentityReducer \

-outputformat org.apache•hadoop.mapred.TextOutputFormat -io text

参数-mapper和参数-reducer可以是一条命令或一个Java类 combiner 参数指定一个 combiner。

我们可以用



Streaming中的键和值

Streaming应用可以决定分隔符的使用，该分隔符用于通过标准输人把键-值对转换

«■>

为一串比特值发送到map函数或reduce函数。默认情况下是Tab（制表符），但是 如果键或值中本身含有Tab分隔符，能将分隔符修改成其他符号是很有用的。

类似地，当map和reduce输出结果键-值对时，也需要一个可配置的分隔符来进行

分隔。更进一步，来自输出的键可以由多个字段进行组合：它可以由一条记录的 前打个字段组成（由 stream.num.map.output.key.fields 或 stream.num. reduce.output.key.fields进行定义），剩下的字段就是值。例如，一个 Streaming处理的输出是“a，b，c”（分隔符是逗号），《设为2,则键解释为“a、 b”，而值是“c”。

mapper和reducer的分隔符是单独配置的。这些属性可以参见表8-3，数据流 以参见图8-1。

表8-3. Streaming的分隔符属性

i



stream.map.input .field.separator std in1



stream.map.output .field.separator std out



stream.reduce.input .field.separator



• .A »    *



衿灘變擊汉.卿'

翊 Streaming 詹 process；



std in ★



stream.reduce.output .field.separator std out



属性名称

类型

默认值描述

:孤

| stream.map.input.field• separator | String | \t   | 此分隔符用于将输入键/值字符串作为字节 流传递到流map |
| --------------------------------- | ------ | ---- | --------------------------------------------------- |
| stream, map. output, field.       | String | \t   | 此分隔符用于把流map处理的输出分割成                 |
| separator                         |        |      | map输出需要的键/值字符串                            |
| tream•num.map.output.             | int    | 1    | 由 st ream, map .output .field, separator 分隔的    |
| key.fields                        |        |      | 字段数i这些字段作为map输出键                        |
| stream. reduce. input ♦ field，   | String | \t   | 此分隔符用于将输入键/值字符串作为字节               |
| separator                         |        |      | 流传递到流reduce                                    |
| stream.reduce.output.             | String | \t   | 此分隔符用于将来自流reduce处理的输出                |
| field.separator                   |        |      | 分成reduce最终输出需要的键/值字符串                 |
| stream.num.reduce.                | int    | 1    | 由 strean.neduoe.output.fifild.separator 分隔的     |
| output.key.fields                 |        |      | 字段数，这些字段作为reduce输出键                    |
|                                   |        |      |                                                     |

input-

j jMapfask

shuffle

ReduceTask 围

b','.：＜■/.

8-1 •在Streaming MapReduce作业中使用分隔符的位置

这些属性与输人和输出的格式无关。例如，如果stream.reduce.output, field.separator被设置成冒号，reduce的stream过程就把a : b行写入标准输出， 那么Streaming的reducer就会知道a作为键，b作为值。如果使用标准的 TextOutputFormat,那么这条记录就用Tab将a和b分隔开并写到输出文件。可以 设置 mapreduce.output.textoutput format.separator 来修改 TextOutputFormat 的分隔符。

##### 8.2输入格式

从一般的文本文件到数据库，Hadoop可以处理很多不同类型的数据格式。本节将 探讨数据格式问题。

###### 8.2.1输入分片与记录

第2章中讲过，一个输入分片（split）就是一个由单个map操作来处理的输入块。每 一个map操作只处理一个输人分片。每个分片被划分为若干个记录，每条记录就 是一个键•值对，map —个接一个地处理记录。输入分片和记录都是逻辑概念，不 必将它们对应到文件，尽管其常见形式都是文件。在数据库的场景中，一个输人分 片可以对应于一个表上的若干行，而一条记录对应到一行（如同DBInputFormat，这

种输入格式用于从关系型数据库读取数据） 输入分片在Java中表示为InputSplit接口（和本章提到的所有类一样，它也在 org.apache.hadoop.mapreduce 包中）0 1

public abstract class InputSplit {

public abstract long getLength() throws IOException^ InterruptedException; public abstract String[] getLocations() throws IOException,

InterruptedException;

}

InputSplit包含一个以字节为单位的长度和一组存储位置（即一组主机名）。注 意，分片并不包含数据本身，而是指向数据的引用（reference）。存储位置供 MapReduce系统使用以便将map任务尽量放在分片数据附近，而分片大小用来排 序分片，以便优先处理最大的分片，从而最小化作业运行时间（这也是贪婪近似算

①如果是老版本的MapReduce API，这些类包含在org.apache.hadoop.mapred中o

法的一个实例)。

MapReduce应用开发人员不必直接处理InputSplit，因为它是由InputFormat 创建的(InputFormat负责创建输入分片并将它们分割成记录)。在我们探讨 InputFormat的具体例子之前，先简单看一下它在MapReduce中的用法。接口 如下：

public abstract class InputFormat<KJ V> { public abstract List<InputSplit> getSplits(DobContext context)

throws IOException, InterruptedException;

public abstract RecordReader<KJ V> createRecordReader(InputSplit split, TaskAttemptContext context)

throws IOException, InterruptedException;

} •

运行作业的客户端通过调用getSPlits()计算分片，然后将它们发送到 application master，application master使用其存储位置信息来调度map任务从而在 集群上处理这些分片数据。map任务把输入分片传给InputFormat的 createRecordReaderO方法来获得这个分片的RecordReader。RecordReader就像是记录上 的迭代器，map任务用一个RecordReader来生成记录的键-值对，然后再传递给 map函数。査看Mapper的run()方法可以看到这些情况：

public void run(Context context) throws IOException, InterruptedException { setup(context);

while (context.nextKeyValue()) {

map(context.getCurrentKey(), context.getCurrentValue()} context);

}

cleanup(context);

}

运行setup()之后，再重复调用Context上的nextKeyValue()(委托给 RecordRader的同名方法)为mapper产生键-值对象。通过Context,键/值从 RecordReaden中被检索出并传递给map()方法。当reader读到stream的结尾 时，nextKeyValue()方法返回false, map任务运行其cleanup()方法，然后

结束。

![img](Hadoop43010757_2cdb48_2d8748-120.jpg)



尽管这段代码没有显示，由于效率的原因，RecordReader程序每次调用 gerCurrentKey()和getCurrentValue()时将返回相同的键-值对象。只是这 些对象的内容被reader的nextKeyValue()方法改变。用户对此可能有些惊讶， 他们可能希望键/值是不可变的且不会被重用。在map()方法之外有对键/值的 引用时，这可能引起问题，因为它的值会在没有警告的情况下被改变。如果确

实需要这样的引用，那么请保存你想保留的对象的一个副本，例如，对于Text对 象，可1拥饿廈制购®趣fc new Text（value）。

这样的情况在reducer中也会发生。reducer迭代器中的值对象被反复使用，所 以，在调用迭代器之间，一定要复制任何需要保留的任何对象（参见范例9-11）。

最后，注意Mapper的run（）方法是公共的，可以由用户定制。MultithreadedMapRunner是

另一个MapRunnable接口的实现，它可以使用可配置个数的线程来并发运行多个 mapped mapreduce.mapper, multithreadedmapper.threads 设置）0 对干大多数

数据处理任务来说，默认的执行机制没有优势。但是，对于因为需要连接外部服 务器而造成单个记录处理时间比较长的mapper来说，它允许多个mapper在同一 个JVM下以尽量避免竞争的方式执行。

\1. FilelnputFormat 类

FilelnputFormat是所有使用文件作为其数据源的InputFormat实现的基类（参见 图8-2）。它提供两个功能：一个用于指出作业的输入文件位置；一个是为输入文 件生成分片的代码实现。把分片分割成记录的作业由其子类来完成。

8-2. InputFormat类的层次结构

\2. FilelnputFormat类的输入路径

作业的输入被设定为一组路径，这对限定输入提供了很强的灵活性。 FilelnputFormat提供四种静态方法来设定]ob的输入路径：

public static void addInputPath(Job job. Path path)

public static void addInputPaths(3ob job, String commaSeparatedPaths)

public static void setl叩utPaths(]ob job. Path... inputPaths)

public static void setInputPaths(]ob job. String commaSeparatedPaths)

其中，addInputPath()和addInputPaths()方法可以将一个或多个路径加入路 径列表。可以分别调用这两种方法来建立路径列表。setInputPaths()方法一次设定 完整的路径列表(替换前面调用中在Job上所设置的所有路径)。

一条路径可以表示一个文件、一个目录或是一个glob，即一个文件和目录的集 合。路径是目录的话，表示要包含这个目录下所有的文件，这些文件都作为作业 的输入。关于glob的使用，3.5.5节在讲到“文件模式”时有详细讨论。

一个被指定为输入路径的目录，其内容不会被递归处理。事实上，这些目录只 包含文件：如果包含子目录，也会被解释为文件(从而产生错误)。处理这个问

1    题的方法是：使用一个文件glob或一个过滤器根据命名模式(name pattern)限定

选择目录中的文件。另一种方法是将mapreduce.input• fileinputformat. input.dir.recursive设置为true从而强制对输入目录进行递归地读取。

add方法和set方法允许指定包含的文件。如果需要排除特定文件，可以使用 FilelnputFormat的setInputPathFilter()方法设置一个过滤器。过滤器的详 细讨论参见3.5.5节中对PathFilter的讨论。

即使不设置过滤器，FilelnputFormat也会使用一个默认的过滤器来排除隐藏文 件(名称中以“•”和开头的文件)。如果通过调用setInputPathFilter()设置 了过滤器，它会在默认过滤器的基础上进行过滤。换句话说，自定义的过滤器只 能看到非隐藏文件。

路径和过滤器也可以通过配置属性来设置(参见表8-4)，这对于Streaming作业来 说很方便。Streaming接口使用-input选项来设置路径，所以通常不需要直接进 行手动设置。

表8-4.输入路径和过滤器属性

属性名称

mapreduce.input, fileinputformat. inputdir



类型    默认值描述

逗号分隔的路径    无    作业的输入文件。包含逗号的路径

中的逗号由“符号转义。例 如，glob {a，b}变成了 {a\, b}

mapreduce. input.    PathFilter类名    无    应用干作业输入文件的过滤器

pathFilter.class

\3. FilelnputFormat类的输入分片

假设有一组文件，FilelnputFormat如何把它们转换为输入分片呢？ FilelnputFormat只分割大文件。这里的“大”指的是文件超过HDFS块的大 小。分片通常与HDFS块大小一样，这在大多应用中是合理的；然而，这个值也 可以通过设置不同的Hadoop属性来改变，如表8-5所示。

表8-5.控制分片大小的属性

麵觀欄魏縣



属性名称    ISI

mapreduce.input fileinputformat split.minsize



类型    默认值

int    1



一个文件分片最小的有效字 节数

| mapreduce.input, fileinputformat. split.maxsize1 | long | Long.MAXJALUE，即9223372036854775807 | 一个文件分片中最大的有效字 节数（以字节算） |
| ------------------------------------------------ | ---- | ------------------------------------ | ------------------------------------------- |
| dfs.blocksize                                    | long | 128 MB,即 134217728                  | HDFS中块的大小（按字节）                    |

①这个属性在老版本的MapReduce API中没有出现（除了 CombineFilel叩utFormat）。然而，这个值是被 间接计算的。计算方法是作业总的输入大小除以map任务数，该值由mapreduce.job.maps （或 DobConf上的SetNumMapTasks（）方法）设置。因为map任务的数目默认情况下是1，所以，分片的最 大值就是输入的大小

最小的分片大小通常是1个字节，不过某些格式可以使分片大小有一个更低的下 界。例如，顺序文件在流中每次插入一个同步入口，所以，最小的分片大小不得 不足够大以确保每个分片有一个同步点，以便reader根据记录边界进行重新同步。详 见5.4.1节。

应用程序可以强制设置一个最小的输入分片大小：通过设置一个比HDFS块更大 一些的值，强制分片比文件块大。如果数据存储在HDFS上，那么这样做是没有 好处的，因为这样做会增加对map任务来说不是本地文件的文件块数。

![img](Hadoop43010757_2cdb48_2d8748-124.jpg)



最大的分片大小默认是由Java的long类型表示的最大值。只有把它的值被设置 成小于块大小才有效果，这将强制分片比块小。

分片的大小由以下公式计算，参见FilelnputFormat的computeSplitSize（）方法:

znox(minimumSize, znin(maximumSize, blockSize))

在默认情况下：

minimumSize < blockSize < maximumSize

所以分片的大小就是blocksize。 小，请参见表8-6的详细说明。

这些参数的不同设置及其如何影响最终分片大



表8-6.举例说明如何控制分片的大小

| 最小分片大小1（默认值） | 最大分片大小Long.MAX J/ALUE （默认值）"" | 块的大小 128 MB（默认值） | 分狀小128 MB | 赚:^^^關人糠5•:翁$拉劣纖事■领说明默认情况下，分片大小与 块大小相同 |
| ----------------------- | ---------------------------------------- | ------------------------- | ------------ | ------------------------------------------------------------ |
| 1（默认值）             | Long.MAX VALUE (默认值)                  | 256 MB                    | 256 MB       | 增加分片大小最自然的方 法是提供更大的HDFS 块，通过 dfs.blocksize 或在构建文件时以单个文 件为基础进行设置 |
| 256MB                   | Long.MAX VALUE (默认值)                  | 128 MB （默认值）         | 256 MB       | 通过使最小分片大小的值 大于块大小的方法来增大 分片大小，但代价是增加 了本地操作 |
| 1（默认值）             | 64 MB                                    | 128 MB（默认值）          | 64 MB        | 通过使最大分片大小的值 大于块大小的方法来减少                |

分片大小

4.小文件与 CombineFilelnputFormat

相对于大批量的小文件，Hadoop更合适处理少量的大文件。一个原因是 FilelnputFormat生成的分块是一个文件或该文件的一部分。如果文件很小 （“小”意味着比HDFS的块要小很多），并且文件数量很多，那么每次map任务 只处理很少的输入数据，（一个文件）就会有很多map任务，每次map操作都会造 成额外的开销。请比较一下把1GB的文件分割成8个128 MB块与分成10000个 左右100 KB的文件。10000个文件每个都需要使用一个map任务，作业时间比一 个输入文件上用8个map任务慢几十倍甚至几百倍。

CombineFilelnputFormat可以缓解这个问题，它是针对小文件而设计的。 FilelnputFormat为每个文件产生1个分片，而CombineFilelnputFormat把多个文件打包 到一个分片中以便每个mapper可以处理更多的数据。关键是，决定哪些块放入同 一个分片时，CombineFilelnputFormat会考虑到节点和机架的因素，所以在典 型MapReduce作业中处理输入的速度并不会下降。

当然，如果可能的话应该尽量避免许多小文件的情况，因为MapReduce处理数据 的最佳速度最好与数据在集群中的传输速度相同，而处理小文件将增加运行作业 而必需的寻址次数。还有，在HDFS集群中存储大量的小文件会浪费namenode的 内存。一个可以减少大量小文件的方法是使用顺序文件（sequence file）将这些小文 件合并成一个或多个大文件（参见范例8-4）：可以将文件名作为键（如果不需要键，可 以用NullWritable等常量代替），文件的内容作为值。但如果HDFS中已经有大 批小文件，CombineFilelnputFormat方法值得一试。

![img](Hadoop43010757_2cdb48_2d8748-125.jpg)



CombineFilelnputFormat不仅可以很好地处理小文件，在处理大文件的时候

也有好处。这是因为，它在每个节点生成一个分片，分片可能由多个块组成。 本质上，CombineFilelnputFormat使map操作中处理的数据量与HDFS中文件 的块大小之间的耦合度降低了。

5.避免切分

有些应用程序可能不希望文件被切分，而是用一个mapper完整处理每一个输入文 件。例如，检査一个文件中所有记录是否有序，一个简单的方法是顺序扫描每一 条记录并且比较后一条记录是否比前一条要小。如果将它实现为一个map任务， 那么只有一个map操作整个文件时，这个算法才可行。®

有两种方法可以保证输入文件不被切分。第一种（最简单但不怎么漂亮）方法就是增 加最小分片大小，将它设置成大于要处理的最大文件大小。把它设置为最大值 long.MAX_VALUE即可。第二种方法就是使用FilelnputFormat具体子类，并且 重写isSplitable（）方法@把返回值设置为false。例如，以下就是一个不可分 割的 TextlnputFormat：

import org.apache.hadoop.fs.path;

import org.apache.hadoop.mapreduce.JobContenxt;

①    SortValidator.RecordStatsChecker 中的 mapper 就是这样实现的。

②    isSplitable（）的方法名中，“splitable”只有一个“t”（通常拼写为“splittable”），此书中使用的 是这种拼写。

import org.apache.hadoop.mapreduce.lib.input.TextInpusFormat;

public class NonSplittableTextInputFormat extends TextlnputFormat { ◎override

protected boolean isSplitable(3obContext contextPath file) { return false;

}

}

\6. mapper中的文件信息

处理文件输入分片的mapper可以从作业配置对象的某些特定属性中读取输入分片 的有关信息，这可以通过调用在Mapper的Context对象上的getInputSplit() 方法来实现。当输入的格式源自于FilelnputFormat时，该方法返回的 InputSplit可以被强制转换为一个FileSplit,以此来访问表8-7列出的文件

信息。

在旧版本的MapReduce API和Streaming接口中，同一个文件分片的信息可通过从 mapper配置的可读属性获取。(在旧版本的MapReduce API中，可以通过在Mappe 类中写configure()方法访问］obConf对象来实现。)

除了表8-7中的属性，所有mapper和reducer都可以访问7.4.1节中列出的属性。



表8-7.文件输入分片的属性

，纖

说明

| getPath()   | i-a-    wmapreduce.map.input•file | Path/String | 正在处理的输入文件的路径 |
| ----------- | --------------------------------- | ----------- | ------------------------ |
| getStart()  | mapreduce.map.input.start         | long        | 分片开始处的字节偏移量   |
| getLength() | mapreduce.map.input.length        | long        | 分片的长度(按字节)       |

下一节将讨论在需要访问分块的文件名时如何使用FileSplit。

MapReduce的类型与格式 225

7.把整个文件作为一条记录处理

有时，mapper需要访问一个文件中的全部内容。即使不分割文件，仍然需要一个 RecordReader来读取文件内容作为record的值。范例8-2的 WholeFilelnputFormat 展示了实现的方法。

范例8-2.把整个文件作为一条记录的InputFormat

public class WholeFilelnputFormat

extends FilelnputFormat<NullWritable, BytesWritable> {

^Override

protected boolean isSplitable(3obContext context， Path file) { return false;

}

Kt

public RecordReader<NullWritableJ BytesWritable> createRecordReader(

InputSplit split, TaskAttemptContext context) throws IOException, InterruptedException {

WholeFileRecordReader reader = new WholeFileRecordReader(); reader.initialize(splits context);

return reader;

}

}

WholeFilelnputFormat中没有使用键，此处表示为NullWritable,值是文件 内容，表示成BytesWritable实例。它定义了两个方法：一个是将isSplitable()

方法重写返回false值，以此来指定输入文件不被分片；另一个是实现了 createRecordReader()方法，以此来返回一个定制的RecordReader实现，如 范例8-3所示。

范例8-3. WholeFHelnputFormat使用RecordReader将整个文件读为一条记录

class WholeFileRecordReader extends RecordReaderxNullWritable, BytesWritable> {

private FileSplit fileSplit; private Configuration conf;

private BytesWritable value = new BytesWritable(); private boolean processed = false;

^Override

public void initialize(InputSplit split, TaskAttemptContext context) throws IOException, InterruptedException {

this.fileSplit = (FileSplit) split; this.conf = context.getConfiguration();

}

^Override

public boolean nextKeyValue() throws IOException, InterruptedException { if (!processed) {

byte[] contents = new byte[(int) fileSplit.getLength()];

Path file = fileSplit.getPath();

FileSystem fs = file.getFileSystem(conf);

FSDatalnputStream in = null; try {

in = fSaOpen(file);

IOUtils.readFully(irb contents^ 0, contents.length); value.set(contents， 0,contents.length);

} finally {

IOUtils.closeStream(in);

}

processed = true;

return true;

}

return false;

}

^Override

public NullWritable getCurrentKey() throws IOException^ InterruptedException { return NullWritable.get();

}

^Override

public BytesWritable getCurrentValue() throws IOException, InterruptedException {

return value;

}

^Override

public float getProgress() throws IOException { return processed ? 1.0f : 0.0f;

}

^Override

public void close() throws IOException { // do nothing

}

參

}

WholeFileRecordReader负责将FileSplit转换成一条记录，该记录的键是 null,值是这个文件的内容。因为只有一条记录，WholeFileRecordReader要 么处理这条记录，要么不处理，所以它维护一个名称为processed的布尔变量来 表示记录是否被处理过。如果当nextKeyValue()方法被调用时，文件没有被处 理过，就打开文件，产生一个长度是文件长度的字节数组，并用Hadoop的 IOUtils类把文件的内容放入字节数组。然后再被传递到next()方法的 BytesWritable实例上设置数组，返回值为true则表示成功读取记录。

其他一些方法都是一些直接的用来访问当前的键和值类型、获取reader进度的方 法，还有一个close()方法，该方法由MapReduce框架在reader完成后调用。

现在演示如何使用WholeFileInputFormato假设有一个将若干个小文件打包成 顺序文件的MapReduce作业，键是原来的文件名，值是文件的内容。如范例84所示。

范例8-4.将若干个小文件打包成顺序文件的MapReduce程序

public class SmallFilesToSequenceFileConverter extends Configured implements Tool {

static class SequenceFileMapper

extends Mapper<NullWritableJ BytesWritable, Text, BytesWritable〉 {

private Text filenameKey;

^Override

protected void setup(Context context) throws IOException,

InterruptedException {

InputSplit split = context.getInputSplit();

Path path = ((FileSplit) split).getPath(); filenameKey = new Text(path.toString());

}

^Override

protected void map(NullWritable key, BytesWritable value. Context context) throws IOException^ InterruptedException {.

context.write(filenameKey, value);

}

}

^Override

public int run(String[] args) throws IOException {

Job job = JobBuilder.parseInputAndOutput(this, getConf(), args); if (conf == null) {

return -1;

}

job.setInputFormatClass(WholeFileInputFormat.class); job.setOutputFormatClass(SequenceFileOutputFormat.class);

job.setOutputKeyClass(Text.class);

job.setOutputValueClass(BytesWritable.class);

job.setMapperClass(SequenceFileMapper.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new SmallFilesToSequenceFileConverter(), args); System.exit(exitCode);

}

}

由于输入格式是wholeFilelnputFormat,所以mapper只需要找到文件输入分片 的文件名。通过将InputSplit从context强制转换为FileSplit来实现这点， 后者包含一个方法可以获取文件路径。路径存储在键对应的的一个Text对象中。reducer 的类型是相同的(没有明确设置)，输出格式是SequenceFileOutputFormat。

以下是在一些小文件上运行样例。此处使用了两个reducer,所以生成两个输出顺 序文件：

% hadoop jar job. jar SmallFilesToSequenceFileConverter \

-conf conf/hadoop-localhost. xml -D mapreduce.job.reduces=2 \ input/smallfiles output

由此产生两部分文件，每一个对应一个顺序文件，可以通过文件系统shell的-text选项来进行检査：

% hadoop fs -conf conf/hadoop-localhost•xml -text output/part-r-00000

hdfs://localhost/user/tom/input/smallfiles/a    61 61 61 61 61 61 61 61 61 61

hdfs://localhost/user/tom/input/smallfiles/c    63 63 63 63 63 63 63 63 63 63

hdfs://localhost/user/tom/input/smallfiles/e

输入文件的文件名分别是\ C、乂 e和/，每个文件分别包含10个相应字母 （比如，a文件中包含10个“a”字母），e文件例外，它的内容为空。我们可以看 到这些顺序文件的文本表示，文件名后跟着文件的十六进制的表示。

![img](Hadoop43010757_2cdb48_2d8748-126.jpg)



至少有一种方法可以改进我们的程序。前面提到，一个mapper处理一个文件 的方法是低效的，所以较好的方法是继承CombineFilelnputFormat而不是 FilelnputFormat。

###### 8.2.2文本输入

Hadoop非常擅长处理非结构化文本数据。本节讨论Hadoop提供的用于处理文本

的不同InputFormat类。

\1. TextlnputFormat

TextlnputFormat是默认的InputFormat。每条记录是一行输入。键是 LongWritable类型，存储该行在整个文件中的字节偏移量。值是这行的内容， 不包括任何行终止符（换行符和回车符），它被打包成一个Text对象。所以，包含 如下文本的文件被切分为包含4条记录的一个分片：

On the top of the Crumpetty Tree The Quangle Wangle sat.

But his face you could not see, On account of his Beaver Hat.

每条记录表示为以下键-值对：

(0, On the top of the Crumpetty Tree)

(33, The Quangle Wangle sat,)

(57, But his face you could not see,) (89, On account of his Beaver Hat.)

很明显，键并不是行号。一般情况下，很难取得行号，因为文件按字节而不是按 行切分为分片。每个分片单独处理。行号实际上是一个顺序的标记，即每次读取 一行的时候需要对行号进行计数。因此，在分片内知道行号是可能的，但在文件 中是不可能的。

然而，每一行在文件中的偏移量是可以在分片内单独确定的，而不需要知道分片 的信息，因为每个分片都知道上一个分片的大小，只需要加到分片内的偏移量 上，就可以获得每行在整个文件中的偏移量了。通常，对于每行需要唯一标识的 应用来说，有偏移量就足够了。如果再加上文件名，那么它在整个文件系统内就 是唯一的。当然，如果每一行都是定长的，那么这个偏移量除以每一行的长度即 可算出行号。

输入分片与HDFS块之间的关系

FilelnputFormat定义的逻辑记录有时并不能很好地匹配HDFS的文件块。 例如，TextlnputFormat的逻辑记录是以行为单位的，那么很有可能某一行 会跨文件块存放。虽然这对程序的功能没有什么影响，如行不会丢失或出错， 但这种现象应该引起注意，因为这意味着那些“本地的” map（即map运行在 输入数据所在的主机上）会执行一些远程的读操作。由此而来的额外开销一般 不是特别明显。

8-3展示了一个例子。一个文件分成几行，行的边界与HDFS块的边界没有 对齐。分片的边界与逻辑记录的边界对齐（这里是行边界），所以第一个分片包 含第5行，即使第5行跨第一块和第二块。第二个分片从第6行开始。

file

lines



block

boundary



split



block

boundary



split



block

boundary



split



block

boundary



8-3. TextlnputFormat 的逻辑记录和 HDFS 块

2.控制一行最大的长度

如果你正在使用这里讨论的文本输入格式中的一种，可以为预期的行长设一个最

大值，对付被损坏的文件。文件的损坏可以表现为一个超长行，这会导致内存溢 出错误，进而任务失败。通过将mapreduce.input, linerecordread er.line.maxlength设置为用字节数表示的、在内存范围内的值（适当超过输入 数据中的行长），可以确保记录reader跳过（长的）损坏的行，不会导致任务失败。

3.关于 KeyValueTextlnputFormat

TextlnputFormat的键，即每一行在文件中的字节偏移量，通常并不是特别有

用。通常情况下，文件中的每一行是一个键-值对，使用某个分界符进行分隔，比

如制表符。例如由TextOutputFormat （即Hadoop默认OutputFormat）产生的输 出就是这种。如果要正确处理这类文件，KeyValueTextlnputFormat比较合

适。

可以通过 mapreduce.input• keyvaluelinerecordreader.key.value.separator 属性来指

定分隔符。它的默认值是一个制表符。以下是一个范例，其中一表示一个（水平方 向的）制表符：

linel 峙On the top of the Crumpetty Tree line2 -*The Quangle Wangle sat, line3 峙But his face you could not see, line4 -*0n account of his Beaver Hat.

与TextlnputFormat类似，输入是一个包含4条记录的分片，不过此时的键是每 行排在制表符之前的Text序列：

(linel, On the top of the Crumpetty Tree) (line2, The Quangle Wangle sat,)

(line3, But his face you could not see,) (line4. On account of his Beaver Hat.)

4.关于 NLinelnputFormat

I过 TextlnputFormat 和 KeyValueTextlnputFormat,每个 mapper 收到的输

＜行数不同。行数取决干输入分片的大小和行的长度。如果希望mappei•收到固定 亍数的输入，需要将NLinelnputFormat作为InputFormat使用。与 extlnputFormat—样，键是文件中行的字节偏移量，值是行本身。

W是每个mapper收到的输入行数。7V设置为1（默认值）时，每个mapper正好收到 一行输入。mapreduce.input.lineinputformat• linespermap 属性控制 AH直

的设定。仍然以刚才的4行输入为例：

On the top of the Crumpetty Tree The Quangle Wangle sat,

But his face you could not see^ On account of his Beaver Hat.

例如，如果W是2，则每个输入分片包含两行。一个mapper收到前两行键•值对:

(0， On the top of the Crumpetty Tree)

(33^ The Quangle Wangle sat,)

另一个mapper则收到后两行：

(57^ But his face you could not see^)

(89, On account of his Beaver Hat.)

键和值与TextlnputFormat生成的一样。不同的是输入分片的构造方法。

通常来说，对少量输入行执行map任务是比较低效的(任务初始化的额外开销造成 的)，但有些应用程序会对少量数据做一些扩展的(也就是CPU密集型的)计算任 务，然后产生输出。仿真是一个不错的例子。通过生成一个指定输入参数的输入 文件，每行一个参数，便可以执行一个参数扫描分析(parameter sweep)：并发运行 一组仿真试验，看模型是如何随参数不同而变化的。

![img](Hadoop43010757_2cdb48_2d8748-127.jpg)



在一些长时间运行的仿真实验中，可能会出现任务超时的情况。一个任务在10 分钟内没有报告状态，application master就会认为任务失败，进而中止进程(参 见7.2.1节的详细讨论)。

I

这个问题最佳解决方案是定期报告状态，如写一段状态信息，或增加计数器的 值。详情可以参见7.1.5节的补充材料“MapReduce中进度的组成”。

另一个例子是用Hadoop引导从多个数据源(如数据库)加载数据。创建一个“种 子”输入文件，记录所有的数据源，一行一个数据源。然后每个mapper分到一个 数据源，并从这些数据源中加载数据到HDFS中。这个作业不需要reduce阶段， 所以reducer的数量应该被设成0(通过调用］ob的setNumReduceTasks()来设 置)。进而可以运行MapReduce作业处理加载到HDFS中的数据。范例参见附 录Co

5.关于XML

大多数XML解析器会处理整个XML文档，所以如果一个大型XML文档由多个 输入分片组成，那么单独解析每个分片就相当有挑战。当然，可以在一个mappei 上(如果这个文件不是很大)，可以用8.2.1节介绍的方法来处理整个XML文档。

由很多“记录”（此处是XML文档片断）组成的XML文档，可以使用简单的字符 串匹配或正则表达式匹配的方法来查找记录的开始标签和结束标签，而得到很多 记录。这可以解决由MapReduce框架进行分割的问题，因为一条记录的下一个开 始标签可以通过简单地从分片开始处进行扫描轻松找到，就像TextlnputFormat 确定新行的边界一样。

Hadoop 提供了 StreamXmlRecordReader 类（在 org.apache.hadoop. streaming.mapreduce包中，还可以在Streaming之外使用）。通过把输入格式设为 StreamlnputFormat,把 stream.recordreader.class属性设为 org.apache.hadoop. streaming.mapreduce.StreamXmlRecordReader 来用 StreamXmlRecordReader

类。reader的配置方法是通过作业配置属性来设reader开始标签和结束标签（详情 参见这个类的帮助文档）。® 例如，维基百科用XML格式来提供大量数据内容，非常适合用MapReduce来并 行处理。数据包含在一个大型的XML打包文档中，文档中有一些元素，例如包含 每页内容和相关元数据的page元素。使用StreamXmlRecordReader'后，这些 page元素便可解释为一系列的记录，交由一个mapper来处理。

###### 8.2.3二进制输入

Hadoop的MapReduce不只是可以处理文本信息，它还可以处理二进制格式的 数据。

1.关于 SequenceFilelnputFormat 类

Hadoop的顺序文件格式存储二进制的键-值对的序列。由于它们是可分割的（它们 有同步点，所以reader可以从文件中的任意一点与记录边界进行同步，例如分片 的起点），所以它们很符合MapReduce数据的格式要求，并且它们还支持压缩，可 以使用一些序列化技术来存储任意类型。详情参见5.4.1节。

如果要用顺序文件数据作为MapReduce的输入，可以使用 SequenceFilelnputFormat。键和值是由顺序文件决定，所以只需要保证map输 入的类型匹配。例如，如果顺序文件中键的格式是IntWritable，值是Text，就

①对于完善的XML输入格式说明，可以参见Mahout的XmllnputFormat ,网址为 <http://mahout>, apache. org/Q

像第5章中生成的那样，那么mapper的格式应该是Mapper<IntWritable，Text, K, V>,其中K和V是这个mapper输出的键和®的类型。

![img](Hadoop43010757_2cdb48_2d8748-128.jpg)



虽然从名称上看不出来，但SequenceFileinputFormat可以读map文件 和顺序文件。如果在处理顺序文件时遇到目录，SequenceFilelnputFormat会认为自

己正在读map文件，使用的是其数据文件。因此，如果没有

MapFilelnputFormat类，也是可以理解的。

2.关于 SequenceFileAsTextlnputFormat 类

SequenceFileAsTextlnputFormat 是 SequenceFilelnputFormat 的变体，它

将顺序文件的键和值转换为Text对象。这个转换通过在键和值上调用 toString（）方法实现。这个格式使顺序文件作为Streaming的合适的输入类型。

3.关于 SequenceFileAsBinarylnputFormat 类

SequenceFileAsBinary Input Format SequenceFilelnputFormat 的一种变 体，它获取顺序文件的键和值作为二进制对象。它们被封装为BytesWritable对 象，因而应用程序可以任意解释这些字节数组。与使用SequenceFile.Reader的 appendRaw（）方法或 SequenceFileAsBinary OutputFormat 创建顺序文件的过 程相配合，可以提供在MapReduce中可以使用任意二进制数据类型的方法（作为顺 序文件打包），不过呢，插人Hadoop序列化机制通常更简洁，详情参见5.3.4节。

4.关于 FixedLengthlnputFormat 类

FixedLengthlnputFormat用于从文件中读取固定宽度的二进制记录，当然这些 记录没有用分隔符分开。必须通过fixedlengthinputformat. record• length

设置每个记录的大小。

參

參

###### 8.2.4多个输入

虽然一个MapReduce作业的输人可能包含多个输入文件（由文件glob、过滤器和路 径组成），但所有文件都由同一个InputFormat和同一个Mapper来解释。然而， 数据格式往往会随时间演变，所以必须写自己的mapper来处理应用中的遗留数据 格式问题。或者，有些数据源会提供相同的数据，但是格式不同。对不同的数据 集进行连接（join，也称“联接”）操作时，便会产生这样的问题。详情参见9.3.2 节。例如，有些数据可能是使用制表符分隔的文本文件，另一些可能是二进制的 顺序文件。即使它们格式相同，它们的表示也可能不同，因此需要分别进行 解析 这些问题可以用Multiplelnputs类来妥善处理，它允许为毎条输入路径指定 InputFormat和Mapper。例如，我们想把英国Met Office®的气象数据和NCDC 的气象数据放在一起来分析最高气温，则可以按照下面的方式来设置输入路径：

MultipleInputs.addInputPath(job, ncdcInputPath^

TextInputFormat.class, MaxTemperatureMapper.class);

Multiplelnputs.addlnputPath(jobjmetofficelnputPath

TextInputFormat.class, MetofficeMaxTemperatureMapper.class);

这段代码取代了对 FilelnputFormat. addlnputPath()和 job. setMapperClass()的常规调 用。Met Office和NCDC的数据都是文本文件，所以对两者都使用TextlnputFormat数

据类型。但这两个数据源的行格式不同，所以我们使用了两个不一样的mapper。 MaxTemperatureMapper读取NCDC的输入数据并抽取年份和气温字段的值。 MetOfficeMaxTemperatureMapper读取Met Office的输人数据，抽取年份和气 温字段的值。重要的是两个mapper的输出类型一样，因此，reducer看到的是聚集 后的map输出，并不知道这些输人是由不同的mapper产生的。

Multiplelnputs类有一个重载版本的addInputPath()方法，它没有mapper 参数：

public static void addInputPath(3ob job, Path path,

class<? extends InputFormat〉 inputFormatClass)

如果有多种输人格式而只有一个mapper(通过Job的setMapperClass()方法设 定)，这种方法很有用。

###### 8.2.5数据库输入(和输出)

DBInputFormat这种输入格式用于使用JDBC从关系型数据库中读取数据。因为

它没有任何共享能力，所以在访问数据库的时候必须非常小心，在数据库中运行 太多的mapper读数据可能会使数据库受不了。正是由于这个原因， DBInputFormat最好用于加载小量的数据集，如果需要与来自HDFS的大数据集

①Met Office数据-•般只用干科研和学术领域。然而，有少部分每月气象站数据可以从以下网址

获取：http://www.metoffice.gov.uk/climate/uk/stationdata/。

连接，要使用Multiplel叩uts。与之相对应的输出格式是DBOutputFormat，它

适用于将作业输出数据(中等规模的数据)转储到数据库。

在关系型数据库和HDFS之间移动数据的另一个方法是：使用Sqoop,具体描述 可以参见第15章。

HBase的TablelnputFormat的设计初衷是让MapReduce程序操作存放在HBase 表中的数据。而TableOutputFormat则是把MapReduce的输出写到HBase表。

##### 8.3输出格式

针对前一节介绍的输入格式，Hadoop都有相应的输出格式。OutputFormat类的层次 结构如图心4所示。

图8-4. OutputFormat类的层次结拘

###### 8.3.1文本输出

默认的输出格式是TextOutputFormat,它把每条记录写为文本行。它的键和值 可以是任意类型，因为TextOutputFormat调用toString()方法把它们转换为 字符串。每个键-值对由制表符进行分隔，当然也可以设定mapreduce.output, textoutputformat. separator 属性改变默认W分隔符。与 TextOutputFormat 对应 的输入格式是KeyValueTextlnputFormat,它通过可配置的分隔符将键-值对文本行分 隔，详情参见8.2.2节。

可以使用NullWritable来省略输出的键或值（或两者都省略，相当于 NullOutputFormat输出格式，后者什么也不输出）。这也会导致无分隔符输出， 以使输出适合用TextlnputFormat读取。

###### 8.3.2二进制输出

1.关于 SequenceFileOutputFormat

蜃

正如名称所示，SequenceFileOutputFormat将它的输出写为一个顺序文件。如 果输出需要作为后续MapReduce任务的输入，这便是一种好的输出格式，因为它W 格式紧凑，很容易棚M。由SequenceFileOutputFormat的静态方法来实现，详情 参见5.2.3节。9.2节用一个例子展示了如何使用SequenceFileOutputFormat。

2.关于 SequenceFileAsBinaryOuputFormat

SequenceFileAsBinaryOutpirtFormat 与 SequenceFileAsBinarylnpirtFormat 相对应，它以原

始的二进制格式把键-值对写到一个顺序文件容器中。

3.关于 MapFileOutputFormat

MapFileOutputFormat把map文件作为输出。MapFile中的键必须顺序添加，所 以必须确保reducer输出的键已经排好序。

\~~~~一 reduce输入的键一定是有序的，但输出的键由reduce函数控制，MapReduce框 架中没有硬性规定reduce输出键必须是有序的。所以reduce输出的键必须有序 是对 MapFileOutputFormat 的一个额外限制。

###### 8.3.3多个输出

FileOutputFormat及其子类产生的文件放在输出目录下。每个reducer —个文件 并且文件由分区号命名：part-r-00000, part-r-00001,等等。有时可能需要对输出

的文件名进行控制或让每个reducer输出多个文件。MapReduce为此提供了 MultipleOutputFormat 类。®

1.范例：数据分割

考虑这样一个需求：按气象站来区分气象数据。这需要运行一个作业，作业的输 出是每个气象站一个文件，此文件包含该气象站的所有数据记录。

一种方法是每个气象站对应一个reducer。为此，我们必须做两件事。第一，写一 个partitioner,把同一个气象站的数据放到同一个分区。第二，把作业的reducer 数设为气象站的个数。partitioner如下：

public class StationPartitioner extend Partitioner<LongWritable, Text> {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

public int getPartition(LongWritable key. Text value, int numPartitions) parser.parse(value);

![img](Hadoop43010757_2cdb48_2d8748-131.jpg)



return getPartition(parser.getStationId());

}

private int getPartition(String stationld) {

}

}

这里没有给出getPartition(String)方法的实现，它将气象站ID转换成分区 索引号。为此，它的输入是一个列出所有气象站ID的列表，然后返回列表中气象 站ID的索引。

这样做有两个缺点。第一，需要在作业运行之前知道分区数和气象站的个数。虽 然NCDC数据集提供了气象站的元数据，但无法保证数据中的气象站ID与元数 据匹配。如果元数据中有某个气象站但数据中却没有该气象站的数据，就会浪费 一个reduce任务。更糟糕的是，数据中有但元数据中却没有的气象站，也没有对 应的reduce任务，只好将这个气象站扔掉。解决这个问题的方法是写一个作业来

①在旧版本的MapReduce API中，有两个类用于产生多个输出：MultipleOutputFormat和 MultipleOutputs。简单地说，虽然 MultipleOutputs 更具有特色，但 MultipleOutputs 在输出目录结构和文件命名上有更多的控制。新版本API中的MultipleOutputs结合了旧

版本API中两种多个输出类的特点。本书网站上的代码包含了本节例子的旧版本API等价样 例，该样例使用了 MultipleOutputs 和 MultipleOutputFormat。

抽取唯一的气象站ID，但很遗憾，这需要额外的作业来实现。

第二个缺点更微妙。一般来说，让应用程序来严格限定分区数并不好，因为可能 导致分区数少或分区不均。让很多reducer做少量工作不是一个高效的作业组织方 法，比较好的办法是使用更少reducer做更多的事情，因为运行任务的额外开销减 少了。分区不均的情况也是很难避免的。不同气象站的数据量差异很大：有些气 象站是一年前刚投入使用的，而另一些气象站可能已经工作近一个世纪了。如果 其中一些reduce任务运行时间远远超过另一些，那么作业执行时间将由它们来决 定，从而导致作业运行时间超出预

/VJ 0



![img](Hadoop43010757_2cdb48_2d8748-132.jpg)



在以下两种特殊情况下，让应用程序来设定分区数（等价于reducer的个数）是有 好处的。

•    0个reducer这个情况很罕见：没有分区，因为应用只需执行map任务

•    1个reducer可以很方便地运行若干小作业，把以前作业的输出合并成单个文 件。前提是数据量足够小，以便一个reducer能轻松处理

最好让集群为作业决定分区数：可用的集群资源越多，作业完成就越快。这就是 默认的HashPartitioner表现如此出色的原因，因为它处理的分区数不限，并且 确保每个分区都有一个很好的键组合使分区更均匀。

如果使用HashPartitioner,每个分区就会包含多个气象站，因此，要实现每个

气象站输出一个文件，必须安排每个reducer写多个文件，由此就有了 MultipleOutput。

2.关于 MultipleOutput 类

MultipleOutput类可以将数据写到多个文件，这些文件的名称源于输出的键和 值或者任意字符串。这允许每个reducer（或者只有map作业的mapper）创建多个文 件。采用name-m-nnnnn形式的文件名用于map输出，name-r-nnnnn形式的文 件名用于reduce输出，其中name是由程序设定的任意名字，nnnnn是一个指明 块号的整数（从00000开始）。块号保证从不同分区（mapper或reducer）写的输出在 相同名字情况下不会冲突。

范例8-5显示了如何使用MultipleOutputs按照气象站划分数据。

范例8-5.用MultipleOutput类将整个数据集分区到以气象站ID命名的文件

public class PartitionByStationUsingMultipleOutputs extends Configured implements Tool {

static class StationMapper extends MapperxLongWritable, Textj Text, Text〉 {

private NcdcRecordParser parser = new NcdcRecordParser();

^Override

protected void map(LongWritable key^ Text value, Context context) throws IOExceptiorij Interrupted Except ion {

parser.parse(value);

context.write(new Text(parser.getStationld()value);

}

}

static class MultipleOutputsReducer

extends Reducer<Text> Text) NullWritable) Text> {

private MultipleOutputs<NullWritable> Text〉 multipleOutputs;

^Override

protected void setup(Context context)

throws IOExceptiorb InterruptedException {

multipleOutputs = new MultipleOutputs<NullWritableJ Text>(context);

}

^Override

public void reduce(Text key， Iterable<Text> values， Context context) throws IOException^ InterruptedException {

for (Text value : values) {

multipleOutputs•write(NullWritable•get()， value， key•toString());

}

}

^Override

protected void cleanup(Context context)

throws IOException, InterruptedException {

multipleOutputs.close();

}

}

^Override

public int run(String[] args) throws Exception {

Job job = ]obBuilder.parseInputAndOutput(this, getConf()^ args); if (job == null) {

return -1;

}

job.setMapperClass(StationMapper.class);

job.setMapOutputKeyClass(Text.class);

job.setReducerClass(MultipleOutputsReducer.class);

job.setOutputKeyClass(NullWritable.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new PartitionByStationUsingMultipleOutputs()

args);

System.exit(exitCode);

}

}

在生成输出的reducer中，在setup()方法中构造一个MultipleOutputs的实例 并将它赋给一个实例变量。在reduce()方法中使用MultipleOutputs实例来写输出， 而不是context0 write()方法作用于键、值和名字。这里使用气象站标识符作为 名字，因此最后产生的输出名字的形式为stotion_identifier_r-nnnnnQ 运行一次后，前面几个输出文件的命名如下:

/output/010010-99999-r-00027

/output/010050-99999-r-00013

/output/010100-99999-r-00015

/output/010280-99999-r-00014

/output/010550-99999-r-00000

/output/010980-99999-r-00011

/output/011060-99999-r-00025

/output/012030-99999-r-00029

/output/012350-99999-r-00018

/output/012620-99999-r-00004

在MultipleOutputs的write()方法中指定的基本路径相对干输出路径进行解 释，因为它可以包含文件路径分隔符(/)，创建任意深度的子目录是有可能的。例 如，下面的改动将数据根据气象站和年份进行划分，这样每年的数据就被包含到

一个名为气象站ID的目录中(例如029070-99999/1901/part-r-00000):

^Override

protected void reduce(Text key, Iterable<Text> values， Context context) throws IOException^ InterruptedException {

for (Text value : values) { parser.parse(value);

String basePath = String.format("%s/%s/part", parser.getStationld(), parser.getYear());

multipleOutputs.write(NullWritable.get(value, basePath);

}

}

MultipleOutput 传递给 mapper 的 OutputFormat ，该例子中为 TextOutputFormat,但可能有更复杂的情况。例如，可以创建命名的输出，每 个都有自己的OutputForamt、键和值的类型(这可以与mapper或reducer的输出 类型不相同)。此外，mapper或reducer可以为每条处理的记录写多个输出文件。 可以査阅Java帮助文档，获取更多信息。

###### 8.3.4延迟输出

FileOutputFormat的子类会产生输出文件(part-r-nnnnn)，即使文件是空的。 有些应用倾向于不创建空文件，此时LazyOutputFormat就有用武之地了。它是

一个封装输出格式，可以保证指定分区第一条记录输出时才真正创建文件。要使 用它，用］obConf和相关的输出格式作为参数来调用setOutputFormatClass() 方法即可。

Streaming 支持-LazyOutput 选项来启用 LazyOutputFormat 功能。

###### 8.3.5数据库输出

写到关系型数据库和HBase的输出格式可以参见8.2.5节。
