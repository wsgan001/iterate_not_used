关于 MapReduce

MapReduce是一种可用于数据处理的编程模型。该模型比较简单，但要想写出有 用的程序却不太容易。Hadoop可以运行各种语言版本的MapReduce程序。在本章 中，我们将看到同一个程序的Java、Ruby和Python语言版本。最重要的是， MapReduce程序本质上是并行运行的，因此可以将大规模的数据分析任务分发给任何一个拥有足够多机器的数据中心。MapReduce的优势在于处理大规模数据集，所以这里先来看一个数据集。<span style="color:red;">为什么在本质上是并行运行的？怎么做到的？</span>

# 1. 气象数据集

在我们的例子里，要写一个挖掘气象数据的程序。分布在全球各地的很多气象传 感器每隔一小时收集气象数据和收集大量日志数据，由于我们希望处理所有这些数据，而且这些数据是半结构化的且是按照记录方式存储的，因此非常适合用 MapReduce 来分析。

数据格式

我们使用的数据来自美国国家气候数据中心(National Climatic Data Center，简称 NCDC, http://www.ncdc.noaa.gov/)。这些数据按行并以ASCII格式存储，其中每一行是一条记录。该存储格式支持丰富的气象要素，其中许多要素可以选择性地列人收集范围或其数据所需的存储长度是可变的。为了简单起见，我们重点讨论一些基本要素(比如气温)，这些要素始终都有而且长度都是固定的。

范例2-1显示了一行采样数据，其中重要字段加了注释。这一行数据被分成很多行以突出每个字段，但在实际文件中，这些字段合并成一行，没有任何分隔符。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/BJc0421l3B.png?imageslim)


数据文件按照日期和气象站进行组织。从 1901 年到 2001 年，每一年都有一个目 录，每一个目录中包含各个气象站该年气象数据的打包文件及其说明文件。例如，1999年对应文件夹下面就包含下面的记录：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/meC7eEBeB6.png?imageslim)

气象台有成千上万个，所以整个数据集由大量的小文件组成。通常情况下，处理 少量的大型文件更容易、更有效，因此，这些数据需要经过预处理，将每年的数据文件拼接成一个单独的文件。具体做法请参见附录C。<span style="color:red;">怎么拼接的？</span>

# 2. 使用Unix工具来分析数据

在这个数据集中，每年全球气温的最高记录是多少？我们先不使用Hadoop来解决 这个问题，因为只有提供了性能基准和结果检查工具，才能和Hadoop进行有效的 对比。

传统处理按行存储数据的工具是范例 2-2 是一个程序脚本，用于计算每年的最高气温。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/H8A5lgmDkL.png?imageslim)

<span style="color:red;">看来 bash 也要会写的，因为最初的处理还是需要部分的脚本来处理的。</span>

这个脚本循环遍历按年压缩的数据文件，首先显示年份，然后使用处理每一个文件。从数据中提取两个字段：气温和质量代码。气温值加0后转换为整数。接着测试气温值是否有效（用 9999 替代 NCDC数据集中的缺失的值），通过质量代码来检测读取的数值是否有疑问或错误。如果数据读取正确，那么该值将与目前读取到的最大气温值进行比较，如果该值比原先的最大值大，就替换目前的最大值。处理完文件中所有的行后，再执行END块中的代码并在屏幕上输出最大气温值。

下面是某次运行结果的起始部分:

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/6KcgF4haCH.png?imageslim)

由于源文件中的气温值被放大10倍，所以1901年的最高气温是31.7°C (20世纪初记录的气温数据比较少，所以这个结果也是可能的)。使用亚马逊的 EC2 High-CPU Extra Large Instance 运行这个程序，只需要42分钟就可以处理完一个世纪的气象数据，找出最高气温。

为了加快处理速度，我们需要并行处理程序来进行数据分析。从理论上讲，这很 简单：我们可以使用计算机上所有可用的硬件线程(hardware thread)来处理，每个线程负责处理不同年份的数据。但这样做仍然存在一些问题。

首先，将任务划分成大小相同的作业通常并不是一件容易的事情。在我们这个例 子中，不同年份数据文件的大小差异很大，所以有一部分线程会比其他线程更早 结束运行。即使可以再为它们分配下一个作业，但总的运行时间仍然取决于处理 最长文件所需要的时间。<span style="color:red;">是的。</span>另一种更好的方法是将输入数据分成固定大小的块 (chunk)，然后每块分到各个进程去执行，这样一来，即使有一些进程可以处理更多数据，我们也可以为它们分配更多的数据。<span style="color:red;">嗯。</span>

其次，合并各个独立进程的运行结果，可能还需要额外进行处理。在我们的例子 中，每年的结果独立于其他年份，所以可能需要把所有结果拼接起来，然后再按 年份进行排序。如果使用固定块大小的方法，则需要一种精巧的方法来合并结果。在这个例子中，某年的数据通常被分割成几个块，每个块独立处理。我们最终获得每个块的最高气温，所以最后一步找出最大值作为该年的最高气温，其他年份的数据都像这样处理。

最后，还是得受限于单台计算机的处理能力。即便开足马力，用上所有处理器， 至少也得花20分钟，系统无法更快了。另外，某些数据集的增长可能会超出单台 计算机的处理能力。一旦开始使用多台计算机，整个大环境中的其他因素就会互 相影响，主要归类为协调性和可靠性两个方面。哪个进程负责运行整个作业？我 们如何处理失败的进程？<span style="color:red;">是呀，Hadoop 是怎么处理这些问题的？</span>

因此，虽然并行处理也是可行的，但实际上也很麻烦。可以借助于Hadoop类似框架来解决这些问题。

# 3. 使用Hadoop来分析数据

为了充分利用Hadoop提供的并行处理优势，我们需要将查询表示成MapReduce 作业。完成某种本地端的小规模测试之后，就可以把作业部署到在集群上运行。<span style="color:red;">嗯，看来小规模的测试是必须的。</span>

## 3.1 map 和 reduce

MapReduce任务过程分为两个处理阶段：map阶段和reduce阶段。每阶段都以键 值对作为输入和输出，其类型由程序员来选择。程序员还需要写两个函数：map 函数和 reduce 函数。

map 阶段的输入是 NCDC 原始数据。我们选择文本格式作为输入格式，将数据集的每一行作为文本输入。键是某一行起始位置相对于文件起始位置的偏移量，不 过我们不需要这个信息，所以将其忽略。

我们的 map 函数很简单。由于我们只对年份和气温属性感兴趣，所以只需要取出这两个字段数据。在本例中，map函数只是一个数据准备阶段，通过这种方式来准备数据，使 reduce 函数能够继续对它进行处理：即找出每年的最高气温。map 函数还是一个比较适合去除已损记录的地方：此处，我们筛掉缺失的、可疑的或错误的气温数据。

为了全面了解map的工作方式，我们考虑以下输入数据的示例数据（考虑到篇 幅，去除了一些未使用的列，并用省略号表示）：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/m5BJACAKEg.png?imageslim)


这些行以键-值对的方式作为map函数的输入：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/GL1HlHec5H.png?imageslim)

键（key）是文件中的行偏移量，map 函数并不需要这个信息，所以将其忽略。map 函数的功能仅限干提取年份和气温信息（以粗体显示），并将它们作为输出（气温值已用整数表示）：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/901aLk4315.png?imageslim)


map 函数的输出经由 MapReduce 框架处理后，最后发送到 reduce 函数。这个处理过程基于键来对键-值对进行排序和分组。因此，在这一示例中，reduce函数看到 的是如下输入：嗯，剩下的就是排序了。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/3DEjg52BcA.png?imageslim)

每一年份后紧跟着一系列气温数据。reduce 函数现在要做的是遍历整个列表并从 中找出最大的读数：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/EJj5k59G4b.png?imageslim)

这是最终输出结果，每一年的全球最高气温记录。


整个数据流如图2-1 所示。在图的底部是Unix管线，用于模拟整个MapReduce的流程，部分内容将在讨论 Hadoop Streaming 时再次涉及。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/DjJ7h0Gkm1.png?imageslim)

<span style="color:red;">嗯，大概知道了，但是，为什么这个就叫做 map 了？</span>


## 3.2 Java MapReduce

明白MapReduce程序的工作原理之后，下一步就是写代码实现它。我们需要三样 东西：一个map函数、一个reduce函数和一些用来运行作业的代码。map函数 由 Mapper 类来表示，后者声明一个抽象的 map() 方法。

范例2-3显示了我们的 map函数实现。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180625/leJFCD23Ag.png?imageslim)

范例2-3.查找最高气温的Mapper类
```java
import java.io.IOException;

import org.apache.hadoop.io.Intkritable;
import org• apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class MaxTemperatureMapper
extends MapReduceBase implements Mapper < LongWritable,Text, Text, IntWritable > {
    private static final int MISSING = 9999;

    ©Override
    public void map(LongWritable key,Text value, Context context) throws IOException,
    InterruptedException {

        String line = value.toString();
        String year = line.substring(15, 19);
        int airTemperature;
        if (line.charAt(87) == '+') {
            // parselnt doesn't like leading plus signs airTemperature = Integer.parselnt(line.substring(88, 92));
        } else {
            airTemperature = Integer.parselnt(line.substring(87, 92));
        }
        String quality = line.substring(92, 93);

        if (airTemperature != MISSING && quality.matches("[01459]")) {
            context.write(new Text(year), new IntWritable(airTemperature));
        }
    }
}
```

这个Mapper类是一个泛型类型，它有四个形参类型，分别指定 map 函数的输入键、输入值、输出键和输出值的类型。就现在这个例子来说，输入键是一个长整数偏移量，输入值是一行文本，输出键是年份，输出值是气温（整数）。Hadoop本 身提供了一套可优化网络序列化传输的基本类型，而不直接使用Java内嵌的类 型。这些类型都在org.apache.hadoop.io包中。这里使用LongWritable类型（相当于Java的Long类型）、Text类型（相当于Java中的String类型）和 IntWritable 类型（相当干 Java 的 Integer 类型）。

map() 方法的输入是一个键和一个值。我们首先将包含有一行输入的 Text 值转换 成 Java 的 String类型，之后用substring（）方法提取我们感兴趣的列。

map() 方法还提供Context实例用于输出内容的写入。在这种情况下，我们将年份数据按Text对象进行读/写（因为我们把年份当作键），将气温值封装在 IntWritable类型中。只有气温数据不缺并且所对应质量代码显示为正确的气温读数时，这些数据才会被写入输出记录中。

以类似方法用Reducer来定义reduce函数。

如范例2-4所示:查找最高气温的Reducer类

```java
import java.io.IOException;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class MaxTemperatureReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
@Override
public void reduce(Text key， Iterable<IntWritable> values， Context context)
	throws IOException, InterruptedException {
		int maxValue = Integer.MIN_VALUE;
		for (IntWritable value : values) {
			maxValue = Math.max(maxValue,value.get());
		}
		context.write(key, new IntWritable(maxValue));
	}
}
```

同样，reduce函数也有四个形式参数类型用于指定输入和输出类型。reduce函数 的输入类型必须匹配map函数的输出类型：即 Text 类型和 IntWritable 类型。 在这种情况下，reduce函数的输出类型也必须是Text和IntWritable类型，分别输出年份及其最高气温。这个最高气温是通过循环比较每个气温与当前所知最高气温所得到的。

第三部分代码负责运行MapReduce作业，参见范例2-5。

范例2-5.这个应用程序在气象数据集中找出最高气温

import java.io.IOException;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Dob;
import org.apache.hadoop•mapreduce.input.FileOutputFormat;
import org.apache.hadoop•mapredduce.input.FileOutputFormat;

public class MaxTemperature {
public static void main(String[] args) throws Exception { if (args.length != 2) {
System.err.printIn(HUsage: MaxTemperature 〈input path> 〈output path>H); System.exit(-l);
}

Dob job = new Job();
job.setDarByClass(MaxTemperature.class); job.set：JobName(’’Max temperature");

FilelnputFormat.addInputPath(job, new Path(args[0]));
FileOutputFormat.setOutputPath(job, new Path(args[l]));
job.setMapperClass(MaxTemperatureMapper.class); job.setReducerClass(MaxTemperatureReducer.class);
job.setOutputKeyClass(Text.class);
job.setOutputValueClass(IntWritable.class);
System.exit(job.waitForCompletion(true) ? 0 : 1);
}
}

］0b对象指定作业执行规范。我们可以用它来控制整个作业的运行。我们在 Hadoop集群上运行这个作业时，要把代码打包成一个JAR文件(Hadoop在集群上 发布这个文件)。不必明确指定JAR文件的名称，在］ob对象的set］arByClass() 方法中传递一个类即可，Hadoop利用这个类来查找包含它的JAR文件，进而找到 相关的JAR文件。

构造］ob对象之后，需要指定输入和输出数据的路径。调用FilelnputFormat 类的静态方法addInputPath()来定义输入数据的路径，这个路径可以是单个的文

件、一个目录(此时，将目录下所有文件当作输入)或符合特定文件模式的一系列文件。由 函数名可知，可以多次调用addInputPath()来实现多路径的输入。

调用FileOutputFormat类中的静态方法setOutputPath()来指定输出路径(只 能有一个输出路径)。这个方法指定的是reduce函数输出文件的写入目录。在运 行作业前该目录是不应该存在的，否则Hadoop会报错并拒绝运行作业。这种预 防措施的目的是防止数据丢失(长时间运行的作业如果结果被意外覆盖，肯定是非 常恼人的)。

接着，通过setMapperClass()和setReducerClass()方法指定要用的map类 型和reduce类型。

setOutputKeyClass()和 setOutputValueClass()方法控制 reduce 函数的输出 类型，并且必须和Reduce类产生的相匹配。map函数的输出类型默认情况下和 reduce函数是相同的，因此如果mapper产生出和reducer相同的类型时(如同本例 所示)，不需要单独设置。但是，如果不同，则必须通过setMapOutputKeyClass()和 setMapOutputValueClass()方法来设置mq)函数的瑜出类型。

输入的类型通过输入格式来控制，我们的例子中没有设置，因为使用的是默认的 TextlnputFormat(文本输入格式)。

在设置定义map和reduce函数的类之后，可以开始运行作业。］ob中的

waitForCompletion()方法提交作业并等待执行完成。该方法唯一的参数是一个 标识，指示是否已生成详细输出。当标识为true(成功)时，作业会把其进度信息 写到控制台。

waitForCompletion()方法返回一个布尔值，表示执行的成(true)败(false)，这 个布尔值被转换成程序的退出代码0或者1。



本章及全书使用的Java MapReduce API，被为“新API”，取代了功能上等价的 旧版本API。附录D阐述了新旧API之间的区别，同时介绍了一些新旧API转 换的小技巧。读者在附录D中也可以找到等价的、用旧API写的“査找最高气 温”应用。

2.3.2.1运行测试

写好MapReduce作业之后，通常要拿一个小型数据集进行测试以排除代码问 题。首先，以独立(本机)模式安装Hadoop,详细说明请参见附录A。在这种模 式下，Hadoop在本地文件系统上运行作业程序。然后，使用本书网站上的指令 安装和编译示例。

以前面讨过的5行采样数据为例来测试MapReduce作业(考虑到篇幅，这里对输 出稍有修改，删除了一些行)：

% export HADOOP_CLASSPATH=hadoop-examples.jar % hadoop MaxTemperature input/ncdc/sample.txt output

14/09/16 09:48:39 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable 14/09/16 09:48:40 WARN mapreduce.JobSubmitter: Hadoop command-line option parsing not performed. Implement the Tool interface and execute your application with ToolRunner to remedy this.

14/09/16 09:48:40 INFO input.FilelnputFormat: Total input paths to process : 1 14/09/16 09:48:40 INFO mapreduce.lobSubmitter: number of splits:l 14/09/16 09:48:40 INFO mapreduce.JobSubmitter: Submitting tokens for job: job-local26392882_0001

14/09/16 09:48:40 INFO mapreduce.Job: The url to track the job: http://localhost:8080/ 14/09/16 09:48:40 INFO mapreduce.Job: Running job: job_local26392882__0001 14/09/16 09:48:40 INFO mapped.LocalDobRunner: OutputCommitter set in config null 14/09/16 09:48:40 INFO mapred.LocalDobRunner: OutputCommitter is org.apache.hadoop.mapreduce.lib.output.FileOutputCommitter

14/09/16 09:48:40 INFO mapped.LocallobRunner: Waiting for map tasks

14/09/16 09:48:40 INFO mapred.LocalZJobRunner: Starting task:

attempt local26392882 0001 m 000000 0

14/09/16 09:48:40 INFO mapred.Task: Using ResourceCalculatorProcessTree : null 14/09/16 09:48:40 INFO mapred.LocallobRunner:

14/09/16 09:48:40 INFO mapred.Task: Task:attempt_local26392882_0001_m_000000_0 is done. And is in the process of committing

peiTej T= sdew psi^nqs 0T=spjOD3y panics

2=spjOD9d ^nd^no aDnpey

S=spj039j qnduT SDnpay T9=sa4Aq ajj^nqs aDnpaa

2=sdnoj§ ^ndui sonpey 0=spjo?9J zyidzyio euiqujoj

0=spjODaj ^nduT suiquioj

63T=sa4Xq q.ixds vidui X9=sa4Aq pezTxeiJa^eiu qndqno dew St7=S94Xq ^ndzpio dew S=spjOD9J ^ndqno dew S=spjO3aj ^ndui dew

0=suoTq.euado aq.TJM jx) jaqiunN 0=suoiq.ejado peau sSjbx jaqumN 0=suoiq.ejado pesu jaquriN t79fr838=U9WJM ss^Aq /o uaqiunN 89TZ.ZE=PB9vi ss^Aq jaquinN

:31Id

:31Id

：31Id

:31Id

:31Id


sjaq.uno3 tueq.sAs ajid

0£ :sJ9q.uno3 :qoc^onpajdew OdNI Tt7：8V：60 9T/60M Xiinj-sssDDns pe^siduiOD 1000"388?6E93TB3Oi-qoC qo[ :qoc-eonpejdBUJ OdNI 访:肋:60 9T/6Q/PT %0ei e^npsj %00i deui ^qoc^onpaudeuj OdNI Tfr:肋：60 9T/60/PT asiej. : dpoui ueqn ut Suiuunj T000—Z8826E9r[e〕OT一叩.〔qoc :qoc*93npajdeuj OdNI Tt7:肪:60 9I/60M •sq.siduioo jovidsxs >,seq. sonpeu :j9uunyqociBDO-|• peudeiu OdNI 0V:8V:60 9T/60M 0—000000"J~T000^8836E93ieooi~4.dueq4B

:>|seq. SuTqsxufd :jeuunyqocieooi• pdjdeui OdNI 0P-8P-60 91/60/PT •auop ,0"000000^"1000"38836£9ZIBDOT"vJu»w.B, >|SBi :>)SBi-p9ddeuj OdNI 抑:肋:60 9T/60/PT djnpaj < dDnpdj :jauunyqociBDOi•peudetu OdNI 0V:肪:60 9T/60/VT

000000""vTT000—Z8826£93TB3Oi^>|SBV0/^ejodiu9^ /qndvio/>|ooq -doopeq /aDBds>|jow->jooq/ujoq./sJ9sn/: dm    000000""J"T000^38836£9?TB3OX • • • 4.du©q4B (

>|se^ zyidzpio P9ABS : jswuwodvidvoexid -    vio OdNI 抑:肋:60 9T/60/M

mou q.xuwoD oq.

P9M0ITB ST 0


puv *9U0p ST 0


•J一T000一Z88Z6£9ne〕oi;一vlwezue >|sei : >|se丄•psjdeuj OdNI 抑:胙:60 9T/60/tT •pajdoD T / T :J9uunyqocxBDO-| • peudeiu OdNI 抑:肪:60 9T/60/VT 3ut44iuiuod ssaooud aqq. ut st •T000一38836E93TBDOi^due^B: >|SBi : >jSBi*p9Jdeuj OdNI 脒:肋:60 9T/60M •psidoo T / T :dsuunyqocTBDOi-psudeiu OdNI 抑:8V:60 9T/60/VT sa^Xq es ：9Zis xew

jo ^91    sq-ueiuSss    t    qq.iM    fssBd-99j9iu    q.sex sqq.    oq. uwoa    :JaSjewpsudeuj    OdNI    抑:肪:60    9T/60/frT

sqxieuflas    pe^jos t    3ui3jew    :jsSjewpaudeiu    OdNI    妳:卵:60    9T/60/M

S94Aq 0S ：9ZTS xbw

jo ^91    sq.ueui§9S    t    qq.TM    "ssed-eSuauj    q.sex aqq.    oq. umoq    :jsSjewpejdeuj    OdNI    0V：8fr：60    9T/60/VT

sq.ueiu3as    peqjos i    SutSjsw    : jaSjswpejdeuj    OdNI    0V:8V:60    9T/60/VT

• psidoD x / t : jeuunyqocieDOi • paudeuj OdNI 0V:8tS60 9T/60M Tinu : 99JissaDOJdJoq.exnD-[B3e3Jnos9a Suisn : >)sei-p5Jdeuj OdNI 0V:肪：60 9T/60/17T 0"000000""J"T000"38826£9ZTe3Oi"^dui9we :>|seq. §UT^jeq.s :JauunyqocTeooi • psudeuj OdNI 抑:卵:60 9T/60/VT s>jseq. eonpsj J04 3uiq.ieM :jsuunaqociBDOi-peudetu OdNI 0V:卵：60 91/60/tzT •9q.9ldui03 joqjioaxs >|seq. deuj :jeuunyqocxeDOi • peudew OdNI 0tz:8V:60 9T/60/tzT 0—000000—LU" T000-28836E93TBDOT^duje^B :>|seq. Suiqsiuid :jauunaqocTBDOi• psudeui OdNI 0V：8V：60 9T/60/M T000"?88?6£93Te3OT"q.due^e, >|se± :>|SB±-pajdeuj OdNI 0V：8V：60 91/60/frT deuj : jeuunaqociBDOi • psudeiu OdNI 0V:8t7:60 9T/60/n

•auop ,0


Shuffles=0

Merged Map outputs=l GC time elapsed (ms)=39

Total committed heap usage (bytes)=226754560 File Input Format Counters

Bytes Read=529 File Output Format Counters

Bytes Written=29

如果调用hadoop命令的第一个参数是类名，Hadoop就会启动一个JVM（Java虚 拟机）来运行这个类。该Hadoop命令将Hadoop库（及其依赖关系）添加到类路径 中，同时也能获得Hadoop配置信息。为了将应用类添加到类路径中，我们定义了 一个HADOOP_CLASSPATH环境变量，然后由价也叩脚本来执行相关操作。



以本地（独立）模式运行时，本书中所有程序均假设已按照这种方式来设置 HADOOP_CLASSPATH。命令的运行需要在范例代码所在的文件夹下进行。

运行作业所得到的输出提供了一些有用的信息。例如，我们可以看到，这个作业 有指定的标识，即job_local26392882_0001,并且执行了一个map任务和一 个 reduce 任务（使用 attempt_local26392882_ 0001_m_000000_0 和 attempt_local26392882_0001_r_000000_0 两个 ID）。在调试 MapReduce 作业 时，知道作业ID和任务ID是非常有用的。

输出的最后一部分，以Counters为标题，显示Hadoop上运行的每个作业的一些 统计信息。这些信息对检查数据是否按照预期进行处理非常有用。例如，我们查 看系统输出的记录信息可知：5个map输入记录产生5个map输出记录（由于 mapper为每个合法的输入记录产生一个输出记录），随后，分为两组的5个reduce 输入记录（一组对应一个唯一的键）产生两个reduce输出记录。

泰

输出数据写入⑽目录，其中每个reducer都有一个输出文件。我们例子中的作 业只有一个reducer,所以只能找到一个名为卯⑽的文件：

% cat output/part-r-00000

1949    111    '

1950    22

这个结果和我们之前手动寻找的结果一样。我们把这个结果解释为1949年的最高 气温记录为而1950年为2.2°C。

2.4横向扩展
前面介绍了 MapReduce针对少量输入数据是如何工作的，现在我们开始鸟瞰整个 系统以及有大量输入时的数据流。为了简单起见，到目前为止，我们的例子都只 是用了本地文件系统中的文件。然而，为了实现横向扩展(scaling out),我们需要 把数据存储在分布式文件系统中(典型的为HDFS,将在第3章中介绍)。通过使用 Hadoop资源管理系统YARN, Hadoop可以将MapReduce计算转移到存储有部分 数据的各台机器上(参见第4章)。下面我们看看具体过程。

2.4.1数据流
首先定义一些术语。MapReduce作业(job)是客户端需要执行的一个工作单元：它 包括输入数据、MapReduce程序和配置信息。Hadoop将作业分成若干个任务 (task)来执行，其中包括两类任务：map任务和reduce任务。这些任务运行在集群 的节点上，并通过YARN进行调度。如果一个任务失败，它将在另一个不同的节 点上自动重新调度运行。

Hadoop将MapReduce的输入数据划分成等长的小数据块，称为输入分片(i叩ut split)或 简称“分片”。Hadoop为每个分片构建一个map任务，并由该任务来运行用户自 定义的map函数从而处理分片中的每条记录。

拥有许多分片，意味着处理每个分片所需要的时间少于处理整个输入数据所花的 时间。因此，如果我们并行处理每个分片，且每个分片数据比较小，那么整个处 理过程将获得更好的负载平衡，因为一台较快的计算机能够处理的数据分片比一 台较慢的计算机更多，且成一定的比例。即使使用相同的机器，失败的进程或其 他并发运行的作业能够实现满意的负载平衡，并且随着分片被切分得更细，负载 平衡的质量会更高。

另一方面，如果分片切分得太小，那么管理分片的总时间和构建map任务的总时 间将决定作业的整个执行时间。对于大多数作业来说，一个合理的分片大小趋向 于HDFS的一个块的大小，默认是128 MB,不过可以针对集群调整这个默认值 (对所有新建的文件)，或在每个文件创建时指定。

Hadoop在存储有输入数据(HDFS中的数据)的节点上运行map任务，可以获得最 佳性能，因为它无需使用宝贵的集群带宽资源。这就是所谓的“数据本地化优 化” (data locality optimization)。但是，有时对于一个map任务的输人分片来说， 存储该分片的HDFS数据块复本的所有节点可能正在运行其他map任务，此时作业 调度需要从某一数据块所在的机架中的一个节点上寻找一个空闲的map槽(slot)来运行 该map任务分片。仅仅在非常偶然的情况下(该情况基本上不会发生)，会使用其他机架 中的节点运行该map任务，这将导致机架与机架之间的网络传输。图2-2显示了这三 种可能性。

现在我们应该清楚为什么最佳分片的大小应该与块大小相同：因为它是确保可以 存储在单个节点上的最大输入块的大小。如果分片跨越两个数据块，那么对于任 何一个HDFS节点，基本上都不可能同时存储这两个数据块，因此分片中的部分 数据需要通过网络传输到map任务运行的节点。与使用本地数据运行整个map任 务相比，这种方法显然效率更低。

參

map任务将其输出写入本地硬盘，而非HDFS。这是为什么？因为map的输出是 中间结果：该中间结果由reduce任务处理后才产生最终输出结果，而且一旦作业 完成，map的输出结果就可以删除。因此，如果把它存储在HDFS中并实现备 份，难免有些小题大做。如果运行map任务的节点在将map中间结果传送给 reduce任务之前失败，Hadoop将在另一个节点上重新运行这个map任务以再次 构建map中间结果。


2-2.本地数据(a)、本地机架(b)和跨机架(c)map任务

reduce任务并不具备数据本地化的优势，单个reduce任务的输入通常来自于所有 mapper的输出。在本例中，我们仅有一个reduce任务，其输入是所有map任务的输 出。因此，排过序的map输出需通过网络传输发送到运行reduce任务的节点。数据在 reduce端合并，然后由用户定义的reduce函数处理。reduce的输出通常存储在HDFS中 以实现可靠存储。如第3章所述，对于reduce输出的每个HDFS块，第一个复本存储 在本地节点上，其他复本出于可靠性考虑存储在其他机架的节点中。因此，将reduce的 输出写入HDFS确实需要占用网络带宽，但这与正常的HDFS管线写入的消耗一样。

一个reduce任务的完整数据流如图2-3所示。虚线框表示节点，虚线箭头表示节 点内部的数据传输，而实线箭头表示不同节点之间的数据传输。

reduce任务的数量并非由输入数据的大小决定，相反是独立指定的。8.1.1节将介 绍如何为指定的作业选择reduce任务的数量。

如果有好多个reduce任务，每个map任务就会针对输出进行分区（partition），即为 每个reduce任务建一个分区。每个分区有许多键（及其对应的值），但每个键对应 的键•值对记录都在同一分区中。分区可由用户定义的分区函数控制，但通常用默认 的partitioner通过哈希函数来分区，很高效。

input

HDFS

map

merge

map


HDFS replication



2-3. 一个reduce任务的MapReduce数据流

一般情况下，多个reduce任务的数据流如图2-4所示。该图清楚地表明了为什么 map任务和reduce任务之间的数据流称为shuffle（混洗），因为每个reduce任务的

输入都来自许多map任务。shuffle 一般比图中所示的更复杂，而且调整混洗参数 对作业总执行时间的影响非常大，详情参见7.3节。

input

HDFS

1卿I:
output

HDFS


HDFS replication

map

merge

•**»


Ift


HDFS replication


split 2 I

■ •奉寧鑛麵辱難聯Ml 4V



2-4.多个reduce任务的数据流


最后，当数据处理可以完全并行（即无需混洗时），可能会出现无reduce任务的情 况（示例参见8.2.2节）。在这种情况下，唯一的非本地节点数据传输是map任务 将结果写入HDFS（参见图2-5｝。

input

HDFS


output

HDFS

卿

.—m :



>HDFS

replication


i


HDFS replication





HDFS replication


2-5•无reduce任务的MapReduce数据流

2.4.2 combiner 函数
集群上的可用带宽限制了 MapReduce作业的数量，因此尽量避免map和reduce任

务之间的数据传输是有利的。Hadoop允许用户针对map任务的输出指定一个 combiner (就像mapper和reducer —样)，combiner函数的输出作为reduce函数的输

入。由干combiner属于优化方案，所以Hadoop无法确定要对一个指定的map任 务输出记录调用多少次combiner (如果需要)。换而言之，不管调用combiner多少 次，o次、1次或多次，reducer的输出结果都是一样的。

combiner的规则制约着可用的函数类型。这里最好用一个例子来说明。还是假设

以前计算最高气温的例子，1950年的读数由两个map任务处理(因为它们在不同 的分片中)。假设第一个map的输出如下:

(1950, 0) (1950, 20) (1950, 10)

第二个map的输出如下:

(1950, 25)

(1950, 15)

reduce


函数被调用时，


输入如下:


(1950, [0, 20, 10, 25, 15])

因为25为该列数据中最大的，所以它的输出如下:

(1950, 25)

我们可以像使用reduce函数那样，使用combiner找出每个map任务输出结果中的 最高气温。如此一来，reduce函数调用时将被传入以下数据：

(1950, [20, 25])

reduce输出的结果和以前一样。更简单地说，我们可以通过下面的表达式来说明 气温数值的函数调用：

max(0, 20, 10, 25, 15) = max(max(0, 20, 10), max(25, 15)) = max(20, 25) = 25



并非所有函数都具有该属性。'例如，如果我们计算平均气温，就不能用求平均函 数mean作为我们的combiner函数，因为

/neon(0, 20, 10, 25, 15) = 14

但是又有

mean(mean(Q} 20^ 10), mean(25> 15)) = mean(10, 20) = 15

combiner函数不能取代reduce函数。为什么呢？我们仍然需要reduce函数来处理 不同map输出中具有相同键的记录。但combiner函数能帮助减少mapper和 reducer之间的数据传输量，因此，单纯就这点而言，在MapReduce作业中是否使 用combiner函数还是值得斟酌的。

指定一个combiner

让我们回到Java MapReduce程序，combiner是通过Reducer类来定义的，并且 在这个例子中，它的实现与MaxTemperatureReducer中的reduce函数相同。唯 一的改动是在］ob中设置combiner类(参见范例2-6)。

范例2-6.用combiner函数快速找出最高气温

public class MaxTemperatureWithCombiner { public static void main(String[] args) throws Exception {

if (args.length 丨=2) {

System.err.printIn(nUsage: MaxTemperatureWithCombiner 〈input path> ••〈output path〉1’)；

System.exit(-l);

}

Job job = new Z)ob();

job.setDarByClass(MaxTemperatureWithCombiner.class); job.set]obName("Max temperature");

FilelnputFormat.addInputPath(job, new Path(args[0])); FileOutputFormat.setOutputPath(job^ new Path(args[l]));

job.setMapperClass(MaxTemperatureMapper.class);

job.setCombinerClass(MaxTemperatureReducer.class);

①有此属性的函数叫commutative和associative。有时也有文章将它们称为distributive，比如在 Gray 等人 1995 年发表的论文 “Data Cube: A Relational Aggregation Operatior Generalizing Group by，Cross-Tab, and Sub-Totals” 中。

job.setReducerClass(MaxTGmperatureReducer.class);

job.setOutputKeyClass(Text.class);

job.setOutputValueClass(IntWritable.class);

System.exit(job.waitForCompletion(true) ? 0 : 1);

}

}

2.4.3运行分布式的MapReduce作业
这个程序用不着修改便可以在一个完整的数据集上直接运行。这是MapReduce的 优势：它可以根据数据量的大小和硬件规模进行扩展。这里有一个运行结果：在 一个10节点EC2集群运行High-CPU Extra Large Instance,程序执行时间只花了 短短6分钟。®

我们将在第6章分析在集群上运行程序的机制。

2.5 Hadoop Streaming
Hadoop提供了 MapReduce的API,允许你使用非Java的其他语言来写自己的 map和reduce函数。Hadoop Streaming使用Unix标准流作为Hadoop和应用程序 之间的接口，所以我们可以使用任何编程语言通过标准输入/输出来写MapReduce 程序。⑦

Streaming天生适合用于文本处理。map的输人数据通过标准输入流传递给map函 数，并且是一行一行地传输，最后将结果行写到标准输出。map输出的键-值对是 以一个制表符分隔的行，reduce函数的输入格式与之相同(通过制表符来分隔的 键-值对)并通过标准输入流进行传输。reduce函数从标准输入流中读取输入行，该 输入已由Hadoop框架根据键排过序，最后将结果写入标准输出。

下面使用Streaming来重写按年份查找最高气温的MapReduce程序。

①    这比在单台机器上通过awk串行运行快7倍。性能没有得到线性增长的主要原因是输入数据 并不是均匀分块的。为了方便起见，数据已经按照年份压缩(gzip)，导致后续年份的文件比较 大，因为这些年份的天气记录更多。

②    对于C++程序员而言，Hadoop Pipes来代替Streaming。它用套接字(socket)与运行C++语言写 的map或reduce函数的进程通信。

2.5.1 Ruby 版本
范例2-7显示了用Ruby编写的map函数。

范例2-7.用Ruby编写查找最高气温的map函数

#!/usr/bin/env ruby

STDIN.eachline do |line| val = line

year， temp， q = val[15,4], val[87,5], val[92，l]

puts "#{year}\t#{temp}n if (temp != "+9999n && q =〜/[01459]/)

end

程序通过程序块读取STDIN（—个10类型的全局常量）中的每一行来迭代执行标准 输入中的每一行。该程序块从输入的每一行中取出相关字段，如果气温有效，就 将年份以及气温以制表符\t隔幵写为标准输出（使用puts）。



值得一提的是Streaming和Java MapReduce API之间的设计差异。Java AP丨控 制的map函数一次只处理一条记录。针对输入数据中的每一条记录，该框架均 需调用Mapper的map（）方法来处理。然而在Streaming中，map程序可以自己 决定如何处理输入数据，例如，它可以轻松读取并同时处理若干行，因为它受 读操作的控制。

用户的Java map实现的是“推”记录方式，但它仍然可以同时处理多行，具体 做法是通过mapper中实例变量将之前读取的多行汇聚在一起。'在这种情况 下，需要实现cleanup。方法，以便知道何时读到最后一条记录，进而完成对最后一 组记录行的处理。

由于这个脚本只能在标准输入和输出上运行，所以最简单的方式是通过Unix管道 进行测试，而不使用Hadoop:

% cat input/ncdc/sample.txt | ch02-mr-intro/src/main/njby/max一temperaturejnap.rb

范例2-8显示的reduce函数更复杂一些

①另一种方法是，在新版MapReduce API中使用“拉”的方式来处理。详情可以参见附录D。

范例2-8.用Ruby编写的查找最高气温的reduce函数 #!/usr/bin/env ruby

last key, max val = nil, -1000000

STDIN.each line do |line| key， val = line.split("\tn) if last key && last key != key

puts "#{last key}\t#{max val}1' last key, max val = key, val.to i

else

last key, max val = key, [max val, val.to i].max end

end

puts "#{last key}\t#{max val}" if last key

同样，程序遍历标准输人中的行，但在我们处理每个键组时，要存储一些状态。 在这种情况下，键是年份，我们存储最后一个看到的键和迄今为止见到的该键对 应的最高气温。MapReduce框架保证了键的有序性，我们由此可知，如果读到一 个键与前一个键不同，就需要开始处理一个新的键组。相比之下，Java API系统 提供一个针对每个键组的迭代器，而在Streaming中，需要在程序中找出键组的边 界。

我们从每行取出键和值，然后如果正好完成一个键组的处理(last J<ey & last_key != key),就针对该键组写入该键及其最高气温，用一个制表符来进 行分隔，最后开始处理新键组时我们需要重置最高气温值。如果尚未完成对一个 键组的处理，那么就只更新当前键的最高气温。

程序的最后一行确保处理完输人的最后一个键组之后，会有一行输出 现在可以用Unix管线来模拟整个MapReduce管线，该管线与图2-1中显示的 Unix管线是相同的：

% cat input/ncdc/sample.txt | \ ch02-mr-intro/src/main/ruby/max一temperature一 map.rb | \ sort | ch02-mr-intro/src/main/ruby/max_temperature_reduce.rb

1949    111

1950    22

输出结果和Java程序的输出一样，所以下一步是通过Hadoop运行它。

hadoop命令不支持Streaming，因此，我们需要在指定Streaming JAR文件流与jar选 项时指定。Streaming程序的选项指定了输入和输出路径以及map和reduce脚本。 如下所示：

% hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \

-input input/ncdc/sample.txt \

-output output \

•mapper ch02-mr-intro/src/main/ruby/max一temperature一map.rb \

•reducer ch02-mr-intro/src/main/ruby/max_temperature_reduce.rb

在一个集群上运行一个庞大的数据集时，我们应该使用-combiner选项来设置 combiner。

% hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \

•files chOZ-mr-intro/src/main/ruby/max^temperature^map.rbjX

ch02-mr-intro/src/main/ruby/max_temperature_reduce.rb \

-input input/ncdc/all \

-output output \

-mapper ch02-mr-intro/src/main/ruby/max_temperature一map.rb \

-combiner ch02-mr-intro/src/main/ruby/max_temperaturG_reduce.rb \

•reducer ch02-mr-intro/src/main/ruby/max_temperature一reduce.rb

还需要注意-files选项的使用，在集群上运行Streaming程序时，我们会使用这 个选项，从而将脚本传输到集群。

2.5.2 Python 版本
Streaming支持任何可以从标准输入读/写到标准输出中的编程语言，因此对于更熟 悉Python的读者，下面提供了同一个例子的Python版本。％ap脚本参见范例2-9,

40 第2章



reduce脚本参见范例2-10

范例2-9.用于查找最高气温的map函数(python版)

#!/usr/bin/env python

import re

import sys

for line in sys.stdin: val = line.strip()

(year， temp， q) = (val[15:19], val[87:92], val[92:93]) if (temp != "+9999" and re.match(H[01459],,> q)):

print n%s\t%sn % (year, temp)

范例2-10.用于查找最高气温的reduce函数(python版)

林！/usr/bin/env python import sys

(last_key, max__val) = (None, -sys.maxint)

①作为Streaming的替代方案，Python程序员可以考虑Dumbo（网址为http://klbostee. github.io/dumbof},它能使 Streaming MapReduce 接 口更像 Python,更好用。

for line in sys.stdin:

(key， val) = line.strip().split("\t") if last_key and last_key != key:

print H%s\t%s" % (last_key> max_val)

(last_key1 max_val) = (key, int(val))

else:

(last_key，max_val) = (key，max(max_val，int(val))) if last_key:

print n%s\t%sH % (last_key, max_val)

我们可以像测试Ruby程序那样测试程序并运行作业。例如，可以像下面这样运行 测试：

% cat input/ncdc/sample.txt | \ ch02-mr•intro/src/main/python/max一temperaturejnap•py | \ sort | ch02-mr-intro/src/main/python/max一temperature一reduce.py

1949    111    一    —

1950    22
