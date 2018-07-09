### Hadoop的I/O操作

Hadoop自带一套原子操作用于数据I/O操作。其中有一些技术比Hadoop本身更 常用，如数据完整性保持和压缩，但在处理多达好几个TB的数据集时，特别值 得关注。其他一些则是Hadoop工具或API,它们所形成的构建模块可用干开发分 布式系统，比如序列化框架和在盘(on-disk)数据结构。

##### 5.1数据完整性

Hadoop用户肯定都希望系统在存储和处理数据时不会丢失或损坏任何数据。尽管 磁盘或网络上的每个I/O操作不太可能将错误引入自己正在读/写的数据中，但是 如果系统中需要处理的数据量大到Hadoop的处理极限时，数据被损坏的概率还是 很高的。

检测数据是否损坏的常见措施是，在数据第一次引入系统时计算校验和(checksum)并在 数据通过一个不可靠的通道进行传输时再次计算校验和，这样就能发现数据是否 损坏。如果计算所得的新校验和与原来的校验和不匹配，我们就认为数据已损 坏。但该技术并不能修复数据——它只能检测出数据错误。(这正是不使用低端硬件的 原因。具体说来，一定要使用ECC内存。)注意，校验和也是可能损坏的，不只 是数据，但由于校验和比数据小得多，所以损坏的可能性非常小。

常用的错误检测码是CRC-32(32位循环冗余校验)，任何大小的数据输入均计算得 到一个32位的整数校验和。Hadoop ChecksumFileSystem使用CRC-32计算校验 和，HDFS用于校验和计算的则是一个更有效的变体CRC-32C。

###### 5.1.1 HDFS的数据完整性

HDFS会对写入的所有数据计算校验和，并在读取数据时验证校验和。它针对每 个由dfs.bytes-per-checksum指定字节的数据计算校验和。默认情况下为512 个字节，由于CRC-32校验和是4个字节，所以存储校验和的额外开销低于1%。

datanode负责在收到数据后存储该数据及其校验和之前对数据进行验证。它在收 到客户端的数据或复制其他datanode的数据时执行这个操作。正在写数据的客户 端将数据及其校验和发送到由一系列datanode组成的管线(详见第3章)，管线中 最后一个datanode负责验证校验和。如果datanode检测到错误，客户端便会收到 一个IOException异常的一个子类，对于该异常应以应用程序特定的方式来处 理，比如重试这个操作。

客户端从datanode读取数据时，也会验证校验和，将它们与datanode中存储的校 验和进行比较。每个datanode均持久保存有一个用于验证的校验和日志(persistent log of checksum verification),所以它知道每个数据块的最后一次验证时间。客户 端成功验证一个数据块后，会告诉这个dataiKKle，datanode由此更新日志。保存这些 统计信息对于检测损坏的磁盘很有价值。

不只是客户端在读取数据块时会验证校验和，每个datanode也会在一个后台线程 中运行一个DataBlockScanner,从而定期验证存储在这个datanode上的所有数 据块。该项措施是解决物理存储媒体上位损坏的有力措施。11-1.4节将详细描述如何 访问扫描报告。

由于HDFS存储着每个数据块的复本(replica)，因此它可以通过数据复本来修复损 坏的数据块，进而得到一个新的、完好无损的复本。基本思路是，客户端在读取 数据块时，如果检测到错误，首先向namenode报告已损坏的数据块及其正在尝试 读操作的这个datanode,再抛出ChecksumException异常。namenode将这个数

据块复本标记为已损坏，这样它不再将客户端处理清求直接发送到这个节点，或 尝试将这个复本复制到另一个datanode0之后，它安排这个数据块的一个复本复 制到另一个datanode，如此一来，数据块的复本因子(replication factor)又回到期望 水平。此后，已损坏的数据块复本便被删除。

在使用open()方法读取文件之前，将false值传递给FileSystem对象的 setVerifyChecksum()方法，即可以禁用校验和验证。如果在命令解释器中使用 带-get选项的-ignoreCrc命令或者使用等价的-copyToLocal命令，也可以达 到相同的效果。如果有一个已损坏的文件需要检查并决定如何处理，这个特性是 非常有用的。例如，也许你希望在删除该文件之前尝试看看是否能够恢复部分

也L丄口

可以用hadoop的命令fs -checksum来检查一个文件的校验和。这可用于在 HDFS中检查两个文件是否具有相同内容，命令也具有类似的功能。详情可 以参见3.7节。

###### 5.1.2 LocalFileSystem

Hadoop的LocalFileSystem执行客户端的校验和验证。这意味着在你写入一个 名为州ename的文件时，文件系统客户端会明确在包含每个文件块校验和的同一 个目录内新建一个隐藏文件。文件块的大小由属性file.bytes-per-checksum控制，默认为512个字节。文件块的大小作为元数据存储在.crc文件 中，所以即使文件块大小的设置已经发生变化，仍然可以正确读回文件。在读取 文件时需要验证校验和，并且如果检测到错误，LocalFileSystem还会抛出一个 ChecksumException 异常。

校验和的计算代价是相当低的（在Java中，它们是用本地代码实现的），一般只是 增加少许额外的读/写文件时间。对大多数应用来说，付出这样的额外开销以保证 数据完整性是可以接受的。此外，我们也可以禁用校验和计算，特别是在底层文 件系统本身就支持校验和的时候。在这种情况下，使用RawLocalFileSystem替 代LocalFileSystem。要想在一个应用中实现全局校验和验证，需要将 fs.file.impl 属性设置为 org.apache.hadoop. fs.RawLocalFileSystem 进

而实现对文件URI的重新映射。还有一个可选方案可以直接新建一个 RawLocalFileSystem实例。如果想针对一些读操作禁用校验和，这个方案非常 有用。示例如下：

Configuration conf =…

FileSystem fs = new RawLocalFileSystem(); fs.initializeCnull^ conf);

###### 5.1.3 ChecksumFileSystem

LocalFileSystem通过ChecksumFileSystem来完成自己的任务，有了这个

类，向其他文件系统（无校验和系统）加入校验和就非常简单，

ChecksumFileSystem 类继承自 FileSystem 类。一般用法如下:

FileSystem rawFs =…

FileSystem checksummedFs = new ChecksumFileSystem(rawFs);

底层文件系统称为“源”(raw)文件系统，可以使用ChecksumFileSystem实例的 getRawFileSystem()方法获取它。ChecksumFileSystem类还有其他一些与校 验和有关的有用方法，比如getChecksumFile()可以获得任意一个文件的校验和 文件路径。请参考文档了解其他方法。

如果ChecksumFileSystem类在读取文件时检测到错误，会调用自己的 r印ortChecksumFailure()方法。默认实现为空方法，但LocalFileSystem类会将这 个出错的文件及其校验和移到同一存储设备上一个名为badJiles的边际文件夹 (side directory)中。管理员应该定期检查这些坏文件并采取相应的行动。

##### 5.2压缩

文件压缩有两大好处：减少存储文件所需要的磁盘空间，并加速数据在网络和磁 盘上的传输。这两大好处在处理大量数据时相当重要，所以我们值得仔细考虑在 Hadoop中文件压缩的用法。

有很多种不同的压缩格式、工具和算法，它们各有千秋。表5-1列出了与Hadoop 结合使用的常见压缩方法。

表5-1.压缩格式总结

| 压缩格式DEFLATE^ | 工具无 | 算法DEFLATE | 文件扩展名 .deflate | 是否可切分否 |
| ---------------- | ------ | ----------- | ------------------- | ------------ |
| gzip             | gzip   | DEFLATE     | •gz                 | 否           |
| bzip2            | bzip2  | bzip2       | .bz2                | 是           |
| LZO              | lzop   | LZO         | •lzo                | 否⑦          |
| LZ4              | 无     | LZ4         | •lz4                | 否           |
| Snappy           | 无     | Snappy      | .snappy             | 否           |

①    DEFLATE是一个标准压缩算法，该算法的标准实现是zlib。没有可用于生成DEFLATE文件的常 用命令行工具，因为通常都用gzip格式。注意，gzip文件格式只是在DEFLATE格式上增加了文 件头和一个文件尾。.deflate文件扩展名是Hadoop约定的。

②    但是如果LZO文件已经在预处理过程中被索引了，那么LZO文件是可切分的。详情参见 5.2.2 节。

所有压缩算法都需要权衡空间/时间：压缩和解压缩速度更快，其代价通常是只能 节省少量的空间。表5-1列出的所有压缩工具都提供9个不同的选项来控制压缩 时必须考虑的权衡：选项-1为优化压缩速度，-9为优化压缩空间。例如，下述命令 通过最快的压缩方法创建一个名为片&识的压缩文件：

%gzip -1 file

不同压缩工具有不同的压缩特性。gzip是一个通用的压缩工具，在空间/时间性能 的权衡中，居于其他两个压缩方法之间。bzip2的压缩能力强于gzip,但压缩速度 更慢一点。尽管bzip2的解压速度比压缩速度快，但仍比其他压缩格式要慢一些。 另一方面，LZO、LZ4和Snappy均优化压缩速度，其速度比gzip快一个数量级， 但压缩效率稍逊一筹。Snappy和LZ4的解压缩速度比LZO高出很多。®

表5-1中的“是否可切分”列表不对应的/土缩算法是否支持切分(splitable)，也就 是说，是否可以搜索数据流的任意位置并进一步往下读取数据。可切分压缩格式尤 其适合MapReduce，更多讨论，可以参见5.2.2节。

###### 5.2.1 codec

Codec是压缩-解压缩算法的一'种实现。在Hadoop中，一个对 CompressionCodec 接口的实现代表一个 codec。例如，GzipCodec 包装了 gzip 的压缩和解压缩算法。表5-2列举了 Hadoop实现的codec0

表 5-2. Hadoop 的压缩 codec

| 压缩格式 DEFLATE | HadoopCompressionCodecorg.apache.hadoop.io.compress.DefaultCodec |
| ---------------- | ------------------------------------------------------------ |
| gzip             | org.apache.hadoop.io.compress.GzipCodec                      |
| bzip2            | org.apache.hadoop.io.compress.BZip2Codec                     |
| LZO              | com.hadoop.compression.lzo.LzopCodec                         |
| LZ4              | org.apache.hadoop.io.compress.Lz4Codec                       |
| Snappy           | org.apache.hadoop.io.compress.SnappyCodec                    |

LZO代码库拥有GPL许可，因而可能没有包含在Apache的发行版本中，因此， Hadoop 的 codec 需要单独从 GQQg\e(<http://code.google.com/p/hadoop-gpl-compression>)

①对于综合性压缩测试集，可以参考jvm-cotnpressor-benchmark,这里针对JVM兼容类库(包括 一些原始类库)提供了相当不错的介绍。

或 GitHub([http://github.com/kevimveil/hadoop-lzoyp](http://github.com/kevimveil/hadoop-lzoyp%e8%bd%bd%ef%bc%8c%e8%af%a5%e4%bb%a3%e7%a0%81%e5%ba%93%e5%8c%85%e5%90%ab%e6%9c%89%e4%bf%ae%e6%ad%a3%e7%9a%84%e8%bd%af%e4%bb%b6)[载，该代码库包含有修正的软件](http://github.com/kevimveil/hadoop-lzoyp%e8%bd%bd%ef%bc%8c%e8%af%a5%e4%bb%a3%e7%a0%81%e5%ba%93%e5%8c%85%e5%90%ab%e6%9c%89%e4%bf%ae%e6%ad%a3%e7%9a%84%e8%bd%af%e4%bb%b6) 错误及其他一些工具。LzopCodec与/zop工具兼容，LzopCodec基本上是LZO格 式的但包含额外的文件头，因此这通常就是你想要的。也有针对纯LZO格式的 LzoCodec，并使用作为文件扩展名(类似于DEFLATE，是gzip格式但不 包含文件头)。

1.通过CompressionCodec对数据流进行压缩和解压缩

CompressionCodec包含两个函数，可以轻松用于压缩和解压缩数据。如果要对写 入输出数据流的数据进行压缩，可用createOutputStream (OutputStream out)

方法在底层的数据流中对需要以压缩格式写入在此之前尚未压缩的数据新建一个 CompressionOutputStream对象。相反，对输人数据流中读取的数据进行解压缩的 时候，贝IU周用 createInputStream(InputStream in)获取 CompressionlnputStream, 可以通过该方法从底层数据流读取解压缩后的数据。

CompressionOutputStream 和 CompressionlnputStream,类似于 java.util. zip.DeflaterOutputStream 和 java.util.zip.DeflaterInputStream,只不

过前两者能够重置其底层的压缩或解压缩方法，对于某些将部分数据流(section of data stream)压缩为单独数据块(block)的应用，例如SequenceFile(详情参见5.4.1 节对SequenceFile类的讨论)，这个能力是非常重要的。

范例5-1显示了如何用API来压缩从标准输入中读取的数据并将其写到标准 输出。

范例5-1.该程序压缩从标准输入读取的数据，然后将其写到标准输出 public class StreamCompressor {

public static void main(String[] args) throws Exception {

String codecClassname = args[0];

Class<?> codecClass = Class.forName(codecClassname);

Configuration conf = new Configuration();

CompressionCodec codec = (CompressionCodec)

ReflectionUtils.newlnstance(codecClass, conf);

CompressionOutputStream out = codec.createOutputStream(System.out);

IOUtils.copyBytes(System.inout, 4096, false); out.finish();

}

}

该应用希望将符合CompressionCodec实现的完全合格名称作为第一个命令行参 数。我们使用ReflectionUtils新建一个codec实例，并由此获得在System.out上支持 压缩的一个包裹方法。然后，对IOUtils对象调用copy Bytes ()方法将输入胃复制到输 出，(输出由CompressionOutputStream对象压缩)。最后，我们对 CompressionOutputStream对象调用finish()方法，要求压缩方法完成到压缩

数据流的写操作，但不关闭这个数据流。我们可以用下面这行命令做一个测试，通过 GzipCodec的StreamCompressor对象对字符串“Text”进行压缩，然后使用 从标准输入中对它进行读取并解压缩操作：

% echo "Text" | hadoop StreamCompressor org•apache•hadoop•io• compress.GzipCodec \ I gunzip

Text

2.通过 CompressionCodecFactory 推断 CompressionCodec

在读取一个压缩文件时，通常可以通过文件扩展名推断需要使用哪个codec。如果 文件以.gz结尾，则可以用GzipCodec来读取，如此等等。前面的表5-1为每一种 压缩格式列举了文件扩展名。

通过使用其getCodec()方法，CompressionCodecFactory提供了一种可以将文 件扩展名映射到一个CompressionCodec的方法，该方法取文件的Path对象作 为参数。范例5-2所示的应用便使用这个特性来对文件进行解压缩

范例5-2.该应用根据文件扩展名选取codec解压缩文件

public class FileDecompressor {

public static void main(String[] args) throws Exception {

String uri = args[0];

Configuration conf = new Configuration。；

FileSystem fs = FileSystem.get(URI.create(uriconf);

Path inputPath = new Path(uri);

CompressionCodecFactory factory = new CompressionCodecFactory(conf); CompressionCodec codec = factory.getCodec(inputPath); if (codec == null) {

System. err. print In (HNo codec found for •• + uri);    *

System.exit(l);

}

String outputUri =

CompressionCodecFactory.removeSuffix(uricodec.getDefaultExtension());

InputStream in = null;

OutputStream out = null; try {

in = codec.createlnputStream(fs.open(inputPath)); out = fs.create(new Path(outputUri));

IOUtils.copyBytes(in， out， conf);

} finally {

IOUtils.closestream（in）;

IOUtils.closeStream（out）;

}

}

}

一旦找到对应的codec ,便去除文件扩展名形成输出文件名，这是通过 CompressionCodecFactory对象的静态方法removeSuffix（）来实现的。按照 这种方法，一个名为力/e.gz的文件可以通过调用该程序解压为名为力/e的文件：

% hadoop FileDecompressor file.gz

CompressionCodecFactory加载表5-2中除LZO之外的所有codec,同样也加载 io.compression.codecs配置属性（参见表5-3）列表中的所有codec。在默认情况 下，该属性列表是空的，你可能只有在你拥有一个希望注册的定制codec（例如外 部管理的LZO codec）时才需要加以修改。每个codec都知道自己默认的文件扩展 名，因此CompressionCodecFactory可通过搜索注册的codec找到匹配指定文 件扩展名的codec（如果有的话）。

表5-3.压缩codec的属性

黑滿腿

用于压缩/解压缩的额外的CompressionCodec 类的列表.



属性名称    类型

io. compression. codecs 逗号分隔的类名

3.原生类库

为了提高性能，最好使用“原生”（native）类库来实现压缩和解压缩。例如，在一 个测试中，使用原生gzip类库可以减少约一半的解压缩时间和约10%的压缩时间（与内置 的Java实现相比）。表5-4说明了每种压缩格式是否有Java实现和原生类库实现。所有的 格式都有原生类库实现，但是并非所有格式都有Java实现（如LZO）。

表5-4.压缩代码库的实现

| 压缩格式DEFLATE | 是否有Java实现是 | 是否、有原生实现是 |
| --------------- | ---------------- | ------------------ |
| gzip            | 是               | 是                 |
| bzip2           | 是               | 否                 |
| LZO             | 否               | 是                 |
| LZ4             | 否               | 是                 |
| Snappy          | 否               | 是                 |

Apache Hadoop二进制压缩包本身包含有为64位Linux构建的原生压缩二进制代

码，称为libhadoop.so0对于其他平台，需要自己根据位于源文件树最顶层的 BUILDING.txt指令编译代码库。

可以通过Java系统的java.library.path属性指定原生代码库。etc/hadoop文 件夹中的/Z67而即脚本可以帮你设置该属性，但如果不用这个脚本，则需要在应用 中手动设置该属性。

默认情况下，Hadoop会根据自身运行的平台搜索原生代码库，如果找到相应的代 码库就会自动加载。这意味着，你无需为了使用原生代码库而修改任何设置。但 是，在某些情况下，例如调试一个压缩相关问题时，可能需要禁用原生代码库。 将属性io.native.lib.available的值设置成false即可，这可确保使用内置 的Java代码库（如果有的话）。

\4. CodecPool

如果使用的是原生代码库并且需要在应用中执行大量压缩和解压缩操作，可以考 虑使用CodecPool,它支持反复使用压缩和解压缩，以分摊创建这些对象的 开销。

范例5-3中的代码显示了 API函数，不过在这个程序中.，它只新建了一个 Compressor,并不需要使用压缩/解压缩池。

范例5-3.使用压缩池对读取自标准输入的数据进行压缩，然后将其写到标准输出

public class PooledStreamCompressor {

public static void main(String[] args) throws Exception { String codecClassname = args[0];

Class<?> codecClass = Class.forName(codecClassname); Configuration conf = new Configuration(); CompressionCodec codec = (CompressionCodec)

ReflectionUtils.newlnstance(codecClass, conf); Compressor compressor = null; try {

compressor = CodecPool.getCompressor(codec);

CompressionOutputStream out =

codec.createOutputStream(System.out> compressor);

IOUtils.copyBytes(System.inout, 4096, false); out.finish();

} finally {

CodecPool.returnCompressor(compressor);

}

}

}

在codec的重载方法createOutputStream()中，对于指定的CompressionCodec，我们从池中 获取一个Compressor实例。通过使用finally数据块，我们在不同的数据流之 间来回复制数据，即使出现IOException异常，也可以确保compressor可以返回

池中。

###### 5.2.2压缩和输入分片

在考虑如何压缩将由MapReduce处理的数据时，理解这些压缩格式是否支持切分 (splitting)是非常重要的。以一个存储在HDFS文件系统中且压缩前大小为1 GB的 文件为例。如果HDFS的块大小设置为128 MB,那么该文件将被存储在8个块 中，把这个文件作为输入数据的MapReduce作业，将创建8个输入分片，其中每 个分片作为一个单独的map任务的输入被独立处理。

现在想象一下，文件是经过gzip压缩的，且压缩后文件大小为1 GB。与以前一 样，HDFS将这个文件保存为8个数据块。但是，将每个数据块单独作为一个输 入分片是无法实现工作的，因为无法实现从gzip压缩数据流的任意位置读取数 据，所以让map任务独立于其他任务进行数据读取是行不通的。gzip格式使用 DEFLATE算法来存储压缩后的数据，而DEFLATE算法将数据存储在一系列连续 的压缩块中。问题在于每个块的起始位置并没有以任何形式标记，所以读取时无 法从数据流的任意当前位置前进到下一块的起始位置读取下一个数据块，从而实现 与整个数据流的同步。由于上述原因，gzip并不支持文件切分。

三这种情况下，MapReduce会采用正确的做法，它不会尝试切分gzip压缩文件， ］为它知道输入是gzip压缩文件(通过文件扩展名看出)且gzip不支持切分。这是 「行的，但牺牲了数据的本地性：一个map任务处理8个HDFS块，而其中大多 C块并没有存储在执行该map任务的节点上。而且，map任务数越少，作业的粒 ［就较大，因而运行的时间可能会更长。

在前面假设的例子中，如果文件是通过LZO压缩的，我们会面临相同的问题，

为这个压缩格式也不支持数据读取和数据流同步。但是，在预处理LZO文件的时 候使用包含在Hadoop LZO库文件中的索引工具是可能的，你可以在5.2.1所列的 Google和GitHub网站上获得该类库。该工具构建了切分点索引，如果使用恰当 的MapReduce输入格式可有效实现文件的可切分特性。

另一方面，bzip2文件提供不同数据块之间的同步标识(pi的48位近似值)，因而它

支持切分。可以参见表5-1，了解每个压缩格式是否支持切分。

应该使用哪种压缩格式?

Hadoop应用处理的数据集非常大，因此需要借助于压缩。使用哪种压缩格式与 待处理的文件的大小、格式和所使用的工具相关。下面有一些建议，大致是按 照效率从高到低排列的。

•    使用容器文件格式，例如顺序文件（见5.4.1节）、Avro数据文件（参见 12.3 节）、ORCFiles（见 5.4.3 节）或者 Parquet 文件（参见 13.2 节），所 有这些文件格式同时支持压缩和切分。通常最好与一个快速压缩工具联 合使用，例如LZO，LZ4，或者Snappy。

•    使用支持切分的压缩格式，例如bzip2（尽管bzip2非常慢），或者使用 通过索引实现切分的压缩格式，例如LZO。

•    在应用中将文件切分成块，并使用任意一种压缩格式为每个数据块建 立压缩文件（不论它是否支持切分）。这种情况下，需要合理选择数据 块的大小，以确保压缩后数据块的大小近似于HDFS块的大小。

•    存储未经压缩的文件。

对大文件来说，不要使用不支持切分整个文件的压缩格式，因为会失去数据的 本地特性，进而造成MapReduce应用效率低下。

###### 5.2.3在MapReduce中使用压缩

前面讲到通过CompressionCodecFactory来推断CompressionCodec时指出，如果输 入文件是压缩的，那么在根据文件扩展名推断出相应的codec后，MapReduce会 在读取文件时自动解压缩文件。

要想压缩MapReduce作业的输出，应在作业配置过程中将mapreduce. output.fileoutputformat.compress 属性设为 true， 将 mapre-duce. output.fileoutputformat.compress.codec 属性设置为打算使用的压缩 codec 的 类名。另一种方案是在FileOutputFormat中使用更便捷的方法设置这些属性，如范 例5-4所示。

范例5-4.对查找最高气温作业所产生输出进行压缩

public class MaxTemperatureWithCompression {

public static void main(String[] args) throws IOException { if (args.length != 2) {

System.err.println(HUsage: MaxTemperatureWithCompression 〈input path〉 + "〈output path〉")；

System.exit(-l);    -

}

Dob job = new ]ob();

job.setDarByClass(MaxTemperature.class);

FilelnputFormat.addInputPath(job^ new Path(args[0]));

FileOutputFormat.setOutputPath(job, new Path(args[l]));

job.setOutputKeyClass(Text.class);

job.setOutputValueClass(IntWritable.class);

曹

FileOutputFormat，setCompressOutput(job, true);

FileOutputFormat.setOutputCompressorClass(job, GzipCodec.class);

job.setMapperClass(MaxTemperatureMapper.class); job.setCombinerClass(MaxTemperatureReducer.class); job.setReducerClass(MaxTemperatureReducer.class);

System.exit(job.waitForCompletion(true) ? 0 : 1);

}

}

我们按照如下指令对压缩后的输入运行程序(输出数据不必使用相同的压缩格式进 行压缩，尽管本例中不是这样)：

% hadoop MaxTemperatureWithCompression input/ncdc/sample.txt•gz output

最终输出的每个部分都是经过压缩的。在这里，只有一部分结果：

% gunzip -c output/part-r-00000.gz

1949    111

1950    22

如果为输出生成顺序文件(sequence file)，可以设置mapreduce.out

put.fileoutputformat.compress• type属性来控制限制使用压缩格式。默认

值是RECORD,即针对每条记录进行压缩。如果将其改为BLOCK,将针对一组记录进 行压缩，这是推荐的压缩策略，因为它的压驗率更高(参见5.4.1节)。

SequenceFileOutputFormat 类另外还有一个静态方法 putCompressionType()，可以

用来便捷地设置该属性。

表5-5归纳概述了用于设置MpaReduce作业输出的压缩格式的配置属性。如果你 的MapReduce驱动使用Tool接口（参见6.2.2节），则可以通过命令行将这些属性 传递给程序，这比通过程序代码来修改压缩属性更加简便。

表5-5. MapReduce的压缩属性

| •BW:傑、」‘    '    • : •    ，網' ■::■‘巧居女XAt属性名, | ;•，興象v稔々4緣纖秀类型 | ■ ■ . ' ' -:默认值                          | 描述                                                    |
| -------------------------------------------------------- | ------------------------ | ------------------------------------------- | ------------------------------------------------------- |
| mapreduce.output.fileoutputformat.compress               | boolean                  | false                                       | 是否压缩输出                                            |
| mapreduce.output.fileoutputformat.compress.codec         | 类名称                   | org.apache.hadoop.io. compress.DefaultCodec | map输出所用的压缩codec                                  |
| mapreduce.output.fileoutputformat.compress.type          | String                   | RECORD                                      | 顺序文件输出可以使 用的压缩类型：NONE、RECORD 或者BLOCK |

对map任务输出进行压缩

尽管MapReduce应用读/写的是未经压缩的数据，但如果对map阶段的中间输入 进行压缩，也可以获得不少好处。由于map任务的输出需要写到磁盘并通过网络 传输到reducer节点，所以通过使用LZO、LZ4或者Snappy这样的快速压缩方 式，是可以获得性能提升的，因为需要传输的数据减少了。启用map任务输出压 缩和设置压缩格式的配置属性如表5-6所示。

表5-6. map任务输出的压缩属性

属性名称 k类型 默认值 mapreduce.map. boolean false output.compress

描述

是否对map任务输 出进行压缩



map输出所用的压 缩 codec



mapreduce.map.    Class

org.apache.hadoop.io. compress.DefaultCodec



output.compress.codec

下面是在作业中启用map任务输出gzip压缩格式的代码（使用新API）：

Configuration conf = new Configuration();

conf.setBoolean(〕ob.MAPJ3UTPUTJZOMPRESS, true);

conf.setClass(Dob•MAP^OUTPUT^COMPRESS^CODEC, GzipCodec.class, CompressionCodec.class);

Job job = new ZJob(conf);

在旧的API中(参见附录D)，JobConf对象中可以通过更便捷的方法实现该 功能：

conf•setCompressMapOutput(true);

conf.setMapOutputCompressorClass(GzipCodec.class);

##### 5.3序列化

序列化(serialization)是指将结构化对象转化为字节流以便在网络上传输或写到磁盘 进行永久存储的过程。反序列化(deserialization)是指将字节流转回结构化对象的逆 过程。

序列化用于分布式数据处理的两大领域：进程间通信和永久存储。

在Hadoop中，系统中多个节点上进程间的通信是通过“远程过程调用” (RPC, remote procedure call)实现的。RPC协议将消息序列化成二进制流后发送到远程节 点，远程节点接着将二进制流反序列化为原始消息。通常情况下，RPC序列化格 式如下。

紧凑

紧凑格式能充分利用网络带宽(数据中心中最稀缺的资源)。

快速

进程间通信形成了分布式系统的骨架，所以需要尽量减少序列化和反序 列化的性能开销，这是最基本的。

可扩展

为了满足新的需求，协议不断变化。所以在控制客户端和服务器的过程 中，需要直接引进相应的协议。例如，需要能够在方法调用的过程中增 添新的参数，并且新的服务器需要能够接受来自老客户端的老格式的消 息(无新增的参数)。

•支持互操作

对于某些系统来说，希望能支持以不同语言写的客户端与服务器交互， 所以需要设计需要一种特定的格式来满足这一需求。

表面看来，序列化框架对选择用于数据持久存储的数据格式应该会有不同的要 求。毕竟，RPC的存活时间不到1秒钟，持久存储的数据却可能在写到磁盘若干 年后才会被读取。但结果是，RPC序列化格式的四大理想属性对持久存储格式而 言也很重要。我们希望存储格式比较紧凑（进而高效使用存储空间）、快速（读/写数 据的额外开销比较小）、可扩展（可以透明地读取老格式的数据）且可以互操作（以可 以使用不同的语言读/写永久存储的数据）。

Hadoop使用的是自己的序列化格式Writable，它绝对紧凑、速度快，但不太容 易用Java以外的语言进行扩展或使用。因为Writable是Hadoop的核心（大多数 MapReduce程序都会为键和值类型使用它），所以在接下来的三个小节中，我们要 进行深入探讨，然后再介绍Hadoop支持的其他序列化框架。Avro（—个克服了 Writable部分不足的序列化系统）将在第12章中讨论。

###### 5.3.1 Writable 接口

Writable接口定义了两个方法：一个将其状态写入DataOutput二进制流，另一 个从Datalnput二进制流读取状态：

package org.apache.hadoop.io;

import java.io.DataOutput; import java.io.Datalnput; import java.io.IOException;

public interface Writable { void write(DataOutput out) throws IOException; void readFields(Datalnput in) throws IOException;

}

让我们通过一个特殊的Writable类来看看它的具体用途。我们将使用 IntWritable来封装Java int类型。我们可以新建一个对象并使用set()方法来 设置它的值：

IntWritable writable = new IntWritable(); writable.set(163);

也可以通过使用一个整数值作为输入参数的构造函数来新建一个对象:

IntWritable writable = new IntWritable(163);

为 了检查 IntWritable 的序列化形式，我们在 java. io • DataOutput St ream

(java.io.DataOutput



的一个实现）中加入一



个帮助函数来封装



java.io.ByteArrayOutputSteam，以便在序列化流中捕捉字节:

public static byte[] serialize(Writable writable) throws IOException { ByteArrayOutputStream out = new ByteArrayOutputStream(); DataOutputStream dataOut = new DataOutputStream(out);

writable.write(dataOut); dataOut.close(); return out.toByteArray();

}

一个整数占用4个字节（



![img](Hadoop43010757_2cdb48_2d8748-67.jpg)



为我们使用］Unit4进行声明）:



byte[] bytes = serialize(writable); assertThat(bytes.length, is(4));

每个字节是按照大端顺序写入的（按照java.io.DataOutput接口中的声明，最重 要的字节先写入流），并且通过Hadoop的StringUtils,我们可以看到这些字节 的十六进制表示：

assertThat (StringUtils. byteToHexStringCbytes)^ is(,,000000a3,'));

让我们试试反序列化。我们再次新建一个辅助方法，从一个字节数组中读取一个

Writable 对象：

public static byte[] deserialize(Writable writable, byte[] bytes) throws IOException {

ByteArraylnputStream in = new ByteArrayInputStream(bytes); DatalnputStream dataln = new DatalnputStream(in); writable.readFields(dataln);

dataln.close(); return bytes;

}

我们构建了一个新的、空值的IntWritable对象，然后调用deserialize（）方法从我

们刚写的输出数据中读取数据。最后，我们看到该值（通过get（）方法获取）是原始 的数值163:

IntWritable newWritable = new IntWritable(); deserialize(newWritable^ bytes); assertThat(newWritable•get(), is(163));

WritableComparable 接□和 comparator

IntWritable 实现原始的 WritableComparable 接口，该接口继承自 Writable 和 java.lang.Comparable 接口：

package org.apache.hadoop.io;

public interface WritableComparable<T> extends Writable, Comparable<T> {

对MapReduce来说，类型比较非常重要，因为中间有个基于键的排序阶段。 Hadoop提供的一个优化接口是继承自]ava Comparator的RawCompanator

接口：

package org.apache.hadoop.io;

import java.util.Comparator;

public interface RawComparator<T> extends Comparator<T> { public int compare(byte[] bl, int si, int 11， byte[] b2, int s2, int 12);

}

该接口允许其实现直接比较数据流中的记录，无须先把数据流反序列化为对象， 这样便避免了新建对象的额外开销。例如，我们根据IntWritable接口实现的 comparator实现原始的compare()方法，该方法可以从每个字节数组bl和b2中 读取给定起始位置(si和S2)以及长度(11和12)的一个整数进而直接进行比较。

WritableComparator 是对继承自 WritableComparable 类的 RawComparator 类的一 个通用实现。它提供两个主要功能。第一，它提供了对原始compare()方法的一

个默认实现，该方法能够反序列化将在流中进行比较的对象，并调用对象的 compare()方法。第二，它充当的是RawComparator实例的工厂(已注册 Writable的实现)。例如，为了获得IntWritable的comparator,我们直接如下 调用：

RawComparator<IntWritable> comparator = WritableComparator.get (IntWritable.class);

这个comparator可以用于比较两个IntWritable对象：

IntWritable wl = new IntWritable(163);

IntWritable w2 = new IntWritable(67);

assertThat(comparator.compareCwl^ w2)greaterThan(O));

或其序列化表示：

byte[] bl = serialize(wl); byte[] b2 = serialize(w2);

assertThat(comparator.compare(bl^ 0, bl.length, b2, 0^ b2.1ength)? greaterThan(O));

###### 5.3.2 Writable 类

Hadoop自带的org.apache.hadoop.io包中有广泛的Writable类可供选择。它们

的层次结构如图5-1所示。

![img](Hadoop43010757_2cdb48_2d8748-68.jpg)



tsr S



Primitives    Others



![img](Hadoop43010757_2cdb48_2d8748-71.jpg)



![img](Hadoop43010757_2cdb48_2d8748-72.jpg)



![img](Hadoop43010757_2cdb48_2d8748-73.jpg)



![img](Hadoop43010757_2cdb48_2d8748-74.jpg)



![img](Hadoop43010757_2cdb48_2d8748-75.jpg)



I L : ' • +•

Text

，BytesWntabie ：

\- .：

MDSHash

醐

:：'|gth,n



?續刪腑



![img](Hadoop43010757_2cdb48_2d8748-78.jpg)



![img](Hadoop43010757_2cdb48_2d8748-79.jpg)



![img](Hadoop43010757_2cdb48_2d8748-80.jpg)



5-1. Writable类的层次结构



\1. Java基本类型的Writable封装器

Writable类对所有Java基本类型(参见表5-7)提供封装，char类型除外(可以存 储在IntWritable中)。所有的封装包含get()和set()两个方法用于读取或存 储封装的值。

表5-7. Java基本类型的Writable类

| Java基本类型 | Writable 实现   | 序列化大小(字节) |
| ------------ | --------------- | ---------------- |
| boolean      | BooleanWritable | 1                |
| byte         | ByteWritable    | 1                |
| Short        | ShortWritable   | 2                |
| int          | IntWritable     | 4                |
|              | VintWritable    | 1〜5             |
| float        | FloatWritable   | 4                |
| long         | LongWritable    | 8                |
|              | VlongWritable   | 卜9              |
| double       | DoubleWritable  | 8                |

对整数进行编码时，有两种选择，即定长格式(IntWritale和LongWritable)和 变长格式(VIntWritable和VLongWritable)。需要编码的数值如果相当小(在-127和127之间，包括-127和127)，变长格式就是只用一个字节进行编码；否 则，使用第一个字节来表示数值的正负和后跟多少个字节。例如，值163需要两 个字节：

byte[] data = serialize(new VIntWritable(163));

assertThat(StringUtils.byteToHexString(data), is(n8fa3"));

如何在定长格式和变长格式之间进行选择呢？定长格式编码很适合数值在整个值 域空间中分布非常均匀的情况，例如使用精心设计的哈希函数。然而，大多数数 值变量的分布都不均匀，一般而言变长格式会更节省空间。变长编码的另一个优 点是可以在VIntWritable和VLongWritable转换，因为它们的编码实际上是一 致的。所以选择变长格式之后，便有增长的空间，不必一开始就用8字节的long 表不。

\2. Text类型

Text是针对UTF-8序列的Writable类。一般可以认为它是java.lang.String的

Writable 等价。

Text类使用整型（通过边长编码的方式）来存储字符串编码中所需的字节数，因此该 最大值为2 GB。另外，Text使用标准UTF-8编码，这使得能够更简便地与其他理 解UTF-8编码的工具进行交互操作。

索引由于着重使用标准的UTF-8编码，因此Text类和Java String类之间存

在一定的差别。对Text类的索引是根据编码后字节序列中的位置实现的，并非字 符串中的Unicode字符，也不是Java char的编码单元（如String）。对于ASCII 字符串，这三个索引位置的槪念是一致的。charAt（）方法的用法如下例所示：

Text t = new Text(HhadoopH);

assertThat(t.getLength(), is(6));

assertThat(t.getBytes().lengthy is(6));

assertThat(t.charAt(2)is((int) 'd•));

assertThat(HOut of bounds", t.charAt(100), is(-l));

注意：harAt()方法返回的是一个表示Unicode编码位置的int类型值，而 String返回一个char类型值。Text还有一个findO方法，该方法类似于 String 的 indexOf ()方法：

Text t = new Text(Hhadoopn);

assertThat (''Find a substring", t .find ("do"), is(2)); assertThatC'Finds first •o.", t.find("o"), is(3));

assertThat(HFinds 1o* from position 4 or later", t.find("o", 4), is(4)); assertThat("No match", t.find("pig"), is(-l));

Unicode —旦开始使用需要多个字节来编码的字符时，Text和String之间的 区别就昭然若揭了。考虑表5-8显示的Unicode字符。① 所有字符（除了表中最后一个字符U+10400），都可以使用单个Java char类型来表 示。U+10400是一个候补字符，并且需要两个Java char来表示，称为“字符代理 对” （surrogate pair）。范例5-5中的测试显示了处理一个字符串（表5-8中的由4个 字符组成的字符串）时String和Text之间的差别。

①本例基干 Norbert Lindenberg 和 Masayoshi Okutsu 发表干 2004 年 5 月的文章 “Supplementary Character in the Java Platform ” (Java 平台中的增补字符)，网址为 <http://bit.ly/> java_supp—characters，中文版网址为 <http://m.blog.csdn.net/article/details?id=>7345527Q

Is

表 5-8. Unicode 字符

| Unicode. . ■ 编码点 | U+0041        |                          | U+6771‘%職•事賴勸黎1:'::耀鄕'• | U+10400              |
| ------------------- | ------------- | ------------------------ | ------------------------------ | -------------------- |
| 名称                | 拉丁大写字母A | 拉丁小写字母无（统一表示 | DESERET                        |                      |
|                     |               | SHARP S                  | 的汉字）                       | CAPITAL LETTERLONG I |
| UTF-8 编码单元      | 41            | c39f                     | e69dbl                         | F0909080             |
| Java表示            | \u0041        | \uOODF                   | \u6771                         | \uuD801\uDC00        |

范例5-5.验证String类和Text类的差异性的测试

public class StringTextComparisonTest {

@Test

public void string() throws UnsupportedEncodingException {

String s = "\u0041\u00DF\u6771\uD801\uDC00";

assertThat(s.length()， is(5));

assertThat(s.getBytes("UTF-8")•length， is(10));

assertThat(s.indexOf(,,\u0041,1), is(0)); assert That (s. indexOf (n\u00DF"), is⑴); assertThat(s.indexOf("\u6771"), is(2)); assertThat(s.indexOf("\uD801\uDC00")，is(3));

is(.\u0041.));

is(.\u00DF.));

is('\u6771'));

is^\uD80r));

isCXuDCeO1));



assertThat(s.charAt(0), assertThat(s.charAt(l), assertThat(s.charAt(2), assertThat(s.charAt(3) assertThat(s.charAt(4)

assertThat(s.codePointAt(0), is(0x0041)); assertThat(s>codePointAt(1), is(0X00DF)); assertThat(s.codePointAt(2), is(0x6771)); assertThat(s•codePointAt(3), is(0x10400));

}

@Test

public void text() {

Text t = new Text(,,\u0041\u00DF\u6771\uD801\uDC00n);

assertThat(t.getLength()， is(10));

assertThat(t.find(u\u0041H), is(0));

assertThat(t.find("\u00DF") ^ is⑴);

assertThat(t.find("\u6771"), is(3));

assertThat(t.find("\uD801\uDC00"), is(6));

assertThat(t.charAt(0), is(0x0041)); assertThat(t.charAt(1)^ is(0x00DF)); assertThat(t.charAt(3)is(0x6771));

assertThat(t.charAt(6)> is(0x10400));

这个测试证实String的长度是其所含char编码单元的个数(5,由该字符串的前 三个字符和最后的一个代理对组成)，但Text对象的长度却是其UTF-8编码的字 节数(10=1+2+3+4)。相似的，String类的indexOf()方法返回char编码单元中 的索引位置，Text类的find()方法则返回字节偏移量。

当代理对不能代表整个Unicode字符时，String类中的charAt()方法会根据指 定的索引位置返回char编码单元。根据char编码单元索引位置，需要 codePointAt()方法来获取表示成int类型的单个Unicode字符。事实上，Text 类中的charAt()方法与String中的codePointAt()更加相似(相较名称而言)。 唯一的区别是通过字节的偏移量进行索引。

迭代利用字节偏移量实现的位置索引，对Text类中的Unicode字符进行迭代是

非常复杂的，因为无法通过简单地增加索引值来实现该迭代。同时迭代的语法有 些模糊(参见范例5-6):将Text对象转换为java.nio.ByteBuffer对象，然后 利用缓冲区对Text对象反复调用bytesToCodePoint()静态方法。该方法能够

获取下一代码的位置，并返回相应的int值，最后更新缓冲区中的位置。当 bytesToCodePoint()返回-1时，则检测到字符串的末尾。

范例5-6.遍历Text对象中的字符

public class Textlterator {

public static void main(String[] args) {

Text t = new Text(H\u0041\u00DF\u6771\uD801\uDC00H);

ByteBuffer buf = ByteBuffer.wrap(t.getBytes(), 0, t.getLength()); int cp;

while(buf.hasRemaining() && (cp = Text.bytesToCodePoint(buf))!=-1){ System.out.println(Integer.toHexString(cp));

}

}

}

Hadoop 的 I/O 操作 117



运行这个程序，打印出字符串中四个字符的编码点(code point)：

% hadoop Textlterator

41

df

6771

10400

可变性与String相比，Text的另一个区别在于它是可变的(与所有Hadoop的

Writable接口实现相似，NullWritable除外，它是单实例对象)。可以通过调用 其中一个set()方法来重用Text实例。例如：

Text t = new Text("hadoop");

t.set("pig");

assertThat(t•getLength()is(3));

assertThat(t•getBytes().length, is(3));

在某些情况下，getBytes()方法返回的字节数组可能比getLength()函数返回 的长度更长：

Text t = new Text("hadoop"); t.set(new Text("pig')); assertThat(t.getLength(), is(3));

assertThat("Byte length not shortened", t.getBytes().length, is(6));

以上代码说明了在调用getBytesO之前为什么始终都要调用getLengthO方法，因 为可以由此知道字节数组中多少字符是有效的。

讨String重新排序Text类并不像java.lang.String类那样有丰富的字符串 喿作API。所以，在多数情况下需要将Text对象转换成String对象。这一转换 遺常通过调用toString()方法来实现：

assertThat(new Text("hadoop •).toString(), is(HhadoopH));

\3. BytesWritable

BytesWritable是对二进制数据数组的封装。它的序列化格式为一个指定所含数 据字节数的整数域(4字节)，后跟数据内容本身。例如，长度为2的字节数组包含 数值3和5,序列化形式为一个4字节的整数(00000002)和该数组中的两个字节

(03 和 05):

BytesWritable b = new BytesWritable(new byte[] { 3， 5 }); byte[] bytes = serialize(b);

assertThat(StringUtils.byteToHexString(bytes)is(.’000000020305"));

BytesWritable是可变的，其值可以通过set()方法进行修改。和Text相似， BytesWritable类的getBytes()方法返回的字节数组长度(容量)可能无法体现 BytesWritable所存储数据的实际大小。可以通过getLength()方法来确定 BytesWritable的大小。示例如下：

b.setCapacity(ll);

assertThat(b.getLength()is(2)); assertThat(b.getBytes().length, is(ll));

\4. NullWritable

NullWritable是Writable的特殊类型，它的序列化长度为0。它并不从数据流 中读取数据，也不写入数据。它充当占位符；例如，在MapReduce中，如果不需 要使用键或值的序列化地址，就可以将键或值声明为NullWritable,这样可以高 效存储常量空值。如果希望存储一系列数值，与键•值对相对，NullWritable也 可以用作在SequenceFile中的键。它是一个不可变的单实例类型，通过调用 NullWritable. get （）方法可以获取这个实例。

\5. ObjectWritable 和 GenericWritable

ObjectWritable 是对 Java 基本类型（String，enum, Writable, null 或这些 类型组成的数组）的一个通用封装。它在Hadoop RPC中用于对方法的参数和返回 类型进行封装和解封装。

当一个字段中包含多个类型时，ObjectWritable非常有用：例如，如果 SequenceFile中的值包含多个类型，就可以将值类型声明为ObjectWritable,并将每个 类型封装在一个ObjectWritable中。作为一个通用的机制，每次序列化都写封 装类型的名称，这非常浪费空间。如果封装的类型数量比较少并且能够提前知 道，那么可以通过使用静态类型的数组，并使用对序列化后的类型的引用加入位 置索引来提高性能。GenericWritable类采取的就是这种方式，所以你得在继承 的子类中指定支持什么类型。

\6. Writable 集合类

org.apache. hadoop. io 软件包中共有 6 个 Writable 集合类，分别是 ArrayWritable、 ArrayPrimitiveWritable、TwoDArrayWritable > MapWritable、SortedMapWritable 以及 EnumMapWritableo

ArrayWritable和TwoDArrayWritable是对Writable的数组和两维数组（数组 的数组）的实现。ArrayWritable或TwoDArrayWritable中所有元素必须是同一

类的实例（在构造函数中指定），如下所示：

ArrayWritable writable = new ArrayWritable（Text.class）;

如果Writable根据类型来定义，例如SequenceFile的键或值，或更多时候作 为MapReduce的输入，则需要继承ArrayWritable（或相应的TwoDArray

Writable类)并设置静态类型。示例如下:

public class TextArrayWritable extends ArrayWritable { public TextArrayWritable() {

super(Text.class);

}

}

ArrayWritable 和 TwoDArrayWritable 都有 get()、set()和 toArray()方法。 toArray()方法用于新建職龃(或且)的一个“浅拷贝”(shallow copy)。

ArrayPrimitiveWritable是对Java基本数组类型的一个封装。调用set()方法 时，可以识别相应组件类型，因此无需通过继承该类来设置类型。

MapWritable 和 SortedMapWritable 分另U实现了 java. util.Map<Writable, Writable〉和 java .util. SortedMap<WritableComparable, Writable〉。每个键/值字段使用的类

型是相应字段序列化形式的一部分。类型存储为单个字节(充当类型数组的索引)。 在org.apache.hadoop.io包中，数组经常与标准类型结合使用，而定制的

Writable类型也通常结合使用，但对于非标准类型，则需要在包头指明所使用的 数组类型。根据实现，MapWritable类和SortedMapWritable类通过正byte值 来指示定制的类型，所以在MapWritable和SortedMapWritable实例中最多可 以使用127个不同的非标准Wirtable类。下面显示使用了不同键值类型的 MapWritable 实例：

MapWritable src = new MapWritable();

src.put(new IntWritable(l), new Text("catn)); src.put(new VIntWritable(2), new LongWritable(163));

MapWritable dest = new MapWritable();

WritableUtils.clonelnto(dest, src);

assertThat((Text) dest.get(new IntWritable(l)), is(new Text(,,catH))); assertThat((LongWritable) dest.get(new VIntWritable(2))， is(new LongWritable(163)));

显然，可以通过Writable集合类来实现集合和列表。可以使用MapWritable类 型(或针对排序集合，使用SortedMapWritable类型)来枚举集合中的元素，用 NullWritable类型枚举值。对集合的枚举类型可采用EnumSetWritable。对于 单类型的Writable列表，使用ArrayWritable就足够了，但如果需要把不同的 Writable类型存储在单个列表中，可以用GenericWritable将元素封装在一个 ArrayWritable中。另一个可选方案是，可以借鉴MapWritable的思路写一个 通用的 ListWritableo

###### 5.3.3实现定制的Writable集合

Hadoop有一套非常有用的Writable实现可以满足大部分需求，但在有些情况 下，我们需要根据自己的需求构造一个新的实现。有了定制的Writable类型，就 可以完全控制二进制表示和排序顺序。由于Writable是MapReduce数据路径的 核心，所以调整二进制表示能对性能产生显著效果。虽然Hadoop自带的Writable实

现已经过很好的性能调优，但如果希望将结构调整得更好，更好的做法往往是新 建一个Writable类型（而不是组合打包的类型）。

如果你正考虑写一个定制的Writable,值得尝试另一种序列化框架，例如 CjT Avro,允许你以声明方式定义定制的类型。详情可以参见5.3.4节有关序列化

框架馳容及第12章。

为了演示如何新建一个定制的Writable,我们写一个表示一对字符串的实现，名 为TextPair。范例5-7展示了最基本的实现。

范例5-7.存储一对Text对象的Writable实现

import java.io.*;

import org.apache.hadoop.io.*;

\>

public class TextPair implements WritableComparable<TextPair> {

private Text first;

private Text second;

public TextPair() { set(new Text(), new Text());

}

public TextPair(String firsts String second) { set(new Text(first), new Text(second));

}

public TextPair(Text firsts Text second) { set(first， second);

}

public void set(Text firsts Text second) { this.first = first; this.second = second;

}

public Text getFirst() { return first;

public Text getSecond() { return second;

}

^Override

public void write(DataOutput out) throws IOException { first.write(out); second.write(out);

}

^Override

public void readFields(DataInput in) throws IOException { first.readFields(in); second.readFields(in);

}

^Override

public int hashCode() {

return first.hashCode() * 163 + second.hashCode();

}

^Override

public boolean equals(Object o) { if (o instanceof TextPair) {

TextPair tp = (TextPair) o;

return first.equals(tp.first) && second.equals(tp.second);

}

return false;

}

^Override

public String toString() { return first + "\t" + second;

}

^Override

public int compareTo(TextPair tp) { int cmp = first.compareTo(tp.first); if (cmp != 0) {

return cmp;

}

return second.compareTo(tp.second);

}

}

这个定制Writable实现的第一部分非常直观：包括两个Text实例变量(first 和second)和相关的构造函数，以及setter和getter(即设置函数和提取函数)。所 有Writable实现都必须有一个默认的构造函数以便MapReduce框架可以对它们 进行实例化，然后再调用readFields()函数查看(填充)各个字段的值。

Writable实例是可变的并且通常可以重用，所以应该尽量避免在write()或 readFields()方法中分配对象。

通过让Text对象自我表示，TextPair类的write()方法依次将每个Text对象 序列化到输出流中。类似的，通过每个Text对象的表示，readFields()方法对 来自输入流的字节进行反序列化。DataOutput和Datalnput接口有一套丰富的 方法可以用于对Java基本类型进行序列化和反序列化，所以，在通常情况下，.你 可以完全控制Writable对象在线上传输/交换(的数据)的格式(数据传输格式)。

就像针对Java语言构造的任何值对象那样，需要重写java.lang.Object中的 hashCode()、equals()和 toString()方法。HashPartitioner (MapReduce 中 的默认分区类)通常用hashCode()方法来选择reduce分区，所以应该确保有一个 比较好的哈希函数来保证每个reduce分区的大小相似。

![img](Hadoop43010757_2cdb48_2d8748-81.jpg)



即便计划结合使用TextOutputFormat和定制的Writable,也得自己动手实 现 toString()方法。TextOutputFormat 对键和值调用 toString()方法，

将键和值转换为相应的输出表示。针对TextPair，我们将原始的Text对象作 为字符串写到输出，各个字符串之间要用制表符来分隔。

TextPair 是 WritableComparable 的一个实现，所以它提供了 compareTo()方

法，该方法可以强制数据排序：先按第一个字符排序，如果第一个字符相同，则 按照第二个字符排序。注意，除了可存储的Text对象数目，TextPair不同于 TextArrayWritable(前一小节中已经提到)，因为TextArrayWritable只继承 Writable,并没有继承WritableComparable。

1.为提高速度实现一个RawComparator

范例5-7中的TextPair代码可以按照其描述的基本方式运行；但我们也可以进一 步优化。按照5.3.1节的说明，当TextPair被用作MapReduce中的键时，需要将 数据流反序列化为对象，然后再调用compareToQ方法进行比较。那么有没有可 能看看它们的序列化表示就可以比较两个TextPair对象呢？

事实证明，我们可以这样做，因为TextPair是两个Text对象连接而成的，而 Text对象的二进制表示是一个长度可变的整数，包含字符串之UTF-8表示的字节 数以及UTF-8字节本身。诀窍在于读取该对象的起始长度，由此得知第一个Text 对象的字节表示有多长；然后将该长度传给Text对象的RawComparator方法， 最后通过计算第一个字符串和第二个字符串恰当的偏移量，这样便可以实现对象

的比较。详细过程参见范例5-8（注意，这段代码已嵌入TextPair类中）。 范例5-8.用于比较TextPair字节表示的RawComparator

public static class Comparator extends WritableComparator {

private static final Text.Comparator TEXT COMPARATOR = new Text.Comparator^);

public Comparator^) { super(TextPair.class);

^Override

public int compare(byte[] bl^ int si, int 11,

byte[] b2) int s2, int 12) {

int firstLl = WritableUtils.decodeVIntSize(bl[sl]) + readVInt(bl, si); int firstL2 = WritableUtils.decodeVIntSize(b2[s2]) + readVInt(b2, s2); int cmp = TEXT COMPARATOR.compare(bl^ si, firstLl, b2, s2, firstL2); if (cmp != 0) {

return cmp;

}

return TEXT COMPARATOR.compare(bl^ si + firstLl, 11 - firstLl, b2, s2 + firstL2, 12 • firstL2);

} catch (IOException e) { throw new IllegalArgumentException(e);

} • •

}

} static {

WritableComparator.define(TextPair.class, new Comparator^));

}

事实上，我们采取的做法是继承WritableComparable类，而非实现RawComparator

接口，因为它提供了一些比较好用的方法和默认实现。这段代码最本质的部分是 计算firstLl和finstL2,这两个参数表示每个字节流中第一个Text字段的长 度。两者分别由变长整数的长度（由WritableUtils的decodeVIntSize（）方法 返回）和编码值（由readVInt（）方法返回）组成。

2.定制的 comparator

从TextPair可以看出，编写原始的comparator需要谨慎，因为必须要处理字节级别的细 节。如果真的需要自己写con平arator，有必要参考org.apache.hadoop.io包中对Writable 接口的实现。WritableUtils提供的方法也比较好用。

如果可能，定制的comparator也应该继承自RawComparator。这些comparator定

义的排列顺序不同于默认comparator定义的自然排列顺序。范例5-9显示了一#对 TextPair 类型的comparator，称为 FirstCompartator，它只考虑 TextPair 对象的第一

个字符串。注意，我们重载了针对该类对象的compare()方法，使两个 compare()方法有相同的语法。

范例5-9.定制的RawComparator用于比较TextPair对象字节表示的第一个字段

public static class FirstComparator extends WritableComparator {

private static final Text.Comparator TEXT COMPARATOR = new Text.Comparator^); public FirstComparator() {

super(TextPair.class);

}

^Override

public int compare(byte[] bl， int si, int 11,

byte[] b2j int s2， int 12) {

try {

int firstLl = WritableUtils.decodeVIntSize(bl[sl]) + readVInt (bl, si); int firstL2 = WritableUtils.decodeVIntSize(b2[s2j) + readVInt (b2, s2); return TEXT COMPARATOR.compare(blsi, firstLl, b2, s2, firstL2);

} catch (IOException e) { throw new IllegalArgumentException(e);

}

}

^Override

public int compare(WritableComparable a, WritableComparable b) { if (a instanceof TextPair && b instanceof TextPair) {

return ((TextPair) a).first.compareTo(((TextPair) b).first);

}

return super.compare(a, b);

}

}

第9章在介绍MapReduce的连接操作和辅助排序(参见9.3节)的时候，将使用这 个 comparator。

###### 5.3.4序列化框架

尽管大多数MapReduce程序使用的都是Writable类型的键和值，但这并不是 MapReduce API强制要求使用的。事实上，可以使用任何类型，只要能有一种机 制对每个类型进行类型与二进制表示的来回转换就可以。

为了支持这一机制，Hadoop有一个针对可替换序列化框架(serialization framework) 的API。序列化框架用一个Serialization实现(包含在org.apache.hadoop. io.serializer 包中)来表示。例如，WritableSenialization 类是对 Writable 类型的 Serialization 实现0

Serialization对象定义了从类型到Serializer实例（将对象转换为字节流）和 Deserializer实例（将字节流转换为对象）的映射方式。

为了注册Serialization实现，需要将io.serizalizations属性设置为一个由 逗号分隔的类名列表。它的默汄值包括org.apache.hadoop.io.serializer. WritableSerialization 和 Avro 指定（Specific）序列化及 Reflect（自反）序列化类 （详见12.1节），这意味着只有Writable对象和Avro对象才可以在外部序列化和 反序列化。

Hadoop 包含一个名为 DavaSerialization 的类，该类使用 Java Object Serialization。 尽管它方便了我们在MapReduce程序中使用标准的Java类型，如Integer或 String,但不如Writable高效，所以不建议使用（参见以下的补充内容）。

序列化IDL

还有许多其他序列化框架从不同的角度来解决该问题：不通过代码来定义类型， 而是使用“接口定义语言” （IDL, Interface Description Language）以不依赖于具体 语言的方式进行声明。由此，系统能够为其他语言生成类型，这种形式能有效提 高互操作能力。它们一般还会定义版本控制方案（使类型的演化直观易懂）。

两个比较流行的序列化框架 Apache Thrift（http://thrifi.apache.org/）和Google 的 Protocol Buffers （[http://code.google.eom/p/protobuf/）,](http://code.google.eom/p/protobuf/%ef%bc%89,%e5%b8%b8%e5%b8%b8%e7%94%a8%e4%bd%9c%e4%ba%8c%e8%bf%9b%e5%88%b6%e6%95%b0%e6%8d%ae%e7%9a%84%e6%b0%b8%e4%b9%85%e5%ad%98)[常常用作二进制数据的永久存](http://code.google.eom/p/protobuf/%ef%bc%89,%e5%b8%b8%e5%b8%b8%e7%94%a8%e4%bd%9c%e4%ba%8c%e8%bf%9b%e5%88%b6%e6%95%b0%e6%8d%ae%e7%9a%84%e6%b0%b8%e4%b9%85%e5%ad%98) 储格式。MapReduce格式对该类的支持有限，®但在Hadoop内部，部分组件仍使 用上述两个序列化框架来实现KPC和数据交换。

Avro是一个基于IDL的序列化框架，非常适用于Hadoop的大规模数据处理。我 们将在第12章讨论Avro。

为什么不用 Java Object Serialization?

Java有自己的序列化机制，称为“Java Object Serialization”（通常简称为“Java Serialization”），该机制与编程语言紧密相关，所以我们很自然会问为什么不在 Hadoop中使用该机制。针对这个问题，Doug Cutting是这样解释的：“为什么

① Twitter 的大象鸟项目([http://github.corn/kevinweil/elephant-bird)](http://github.corn/kevinweil/elephant-bird)%e5%8c%85%e5%90%ab%e4%b8%80%e5%94%89)[包含一唉](http://github.corn/kevinweil/elephant-bird)%e5%8c%85%e5%90%ab%e4%b8%80%e5%94%89) 1:具，用十介: Hadoop 中与 Thrif 和 Protocol Buffers 结合使用。

开始设计Hadoop的时候我不用Java Serialization?因为它看起来太复杂，而我 认为需要有一个至精至简的机制，可以用于精确控制对象的读和写，这个机制

将是Hadoop的核心。使用Java Serialization虽然可以获得一些控制权，但用 起来非常纠结。

不用RMI（Remote Method Invocation远程方法调用）也出于类似的考虑。高效、 高性能的进程间通信是Hadoop的关键。我觉得我们需要晴确控制连接、延迟和缓冲 的处理方式，RMI对此无能为力。”

问题在于Java Serialization不满足先前列出的序列化格式标准：精简、快速、 可扩展、支持互操作。

##### 5.4基于文件的数据结构

对于某些应用，我们需要一种特殊的数据结构来存储自己的数据。对于基于 MapReduce的数据处理，将每个二进制数据大对象（blob）单独放在各自的文件中不 能实现可扩展性，所以，Hadoop为此开发了很多更高层次的容器。

###### 5.4.1 关于 SequenceFile

考虑日志文件，其中每一行文本代表一条日志记录。纯文本不合适记录二进制类 型的数据。在这种情况下，Hadoop的SequenceFile类非常合适，为二进制键-

值对提供了一个持久数据结构。将它作为日志文件的存储格式时，你可以自己选 择键（比如LongWritable类型所表示的时间戳），以及值可以是Writable类型 （用于表示日志记录的数量）。

SequenceFiles也可以作为小文件的容器。HDFS和MapReduce是针对大文件优 化的，所以通过SequenceFile类型将小文件包装起来，可以获得更高效率的存储 和处理。在8.2.1节中，我们讲到将整个文件作为一条记录处理时，提供了一个程序，它 将若干个小文件打包成一个SequenceFile类®。

①无独有偶，Stuart Sierra的博客文章“A Million Little Files”中也包含将tar文件转为 SequenceFile 的代码，参见 [http://stuartsierra.com/2008/04/24/a-million-little-files](http://stuartsierra.com/2008/04/24/a-million-little-files%e3%80%82)[。](http://stuartsierra.com/2008/04/24/a-million-little-files%e3%80%82)

\1. SequenceFile 的写操作

通过createWriter()静态方法可以创建SequenceFile对象，并返回 SequenceFile.Writer实例。该静态方法有多个重载版本，但都需要指定待写入 的数据流(FSDataOutputStream 或 FileSystem 对象和 Path 对象)，

Configuration对象，以及键和值的类型。另外，可选参数包括压缩类型以及相 应的codec , Progressable回调函数用于通知写入的进度，以及在

SequenceFile头文件中存储的Metadata实例。

存储在SequenceFile中的键和值并不一定需要是Writable类型。只要能被 Serialization序列化和反序列化，任何类型都可以。

一旦拥有SequenceFile.Writer实例，就可以通过append()方法在文件末尾附 加键-值对。写完后，可以调用close()方法(SequenceFile.Writer实现了 java.io.Closeable 接口)。

范例5-10显示了一小段代码，它使用刚才描述的API将键-值对写入一个 SequenceFile。

范例5-10.写入SequenceFile对象

public class SequenceFileWriteDemo { private static final String[] DATA = { "One, two, buckle my shoe"

"Three， four, shut the door、

•’Five, six, pick up sticks",

•■Seven, eighty lay them straight”， ■•Nine，ten，a big fat henn

public static void main(String[] args) throws IOException {

String uri = args[0];

Configuration conf = new Configuration();

FileSystem fs = FileSystem.get(URI.create(uriconf);

Path path = new Path(uri);

IntWritable key = new IntWritable();

Text value = new Text();

SequenceFile.Writer writer = null;

try {

writer = SequenceFile.createWriter(fs, confpath, key.getClass()， value.getClass());

for (int i = 0; i < 100; i++) { key•set(100 - i); value.set(DATA[i % DATA.length]);

System.out.printf(n[%s]\t%s\t%s\nHwriter.getLength()key, value);

writer.append(key, value);

}

} finally {

IOUtils•closeStream(writer);

}

}

}

顺序文件中存储的键-值对，键是从100到1降序排列的整数，表示为 IntWritable对象，值是Text对象。在将每条记录追加到SequenceFile. Writer实例末尾之前，我们调用getLength()方法来获取文件的当前位置。(在 下一小节中，如果不按顺序读取文件，则使用这一信息作为记录的边界。)我们把 这个位置信息和键-值对输出到控制台。结果如下所示：

% hadoop SequenceFileMriteDemo numbers.seq

| [128] | 100  |
| ----- | ---- |
| [173] | 99   |
| [220] | 98   |
| [264] | 97   |
| [314] | 96   |
| [359] | 95   |
| [404] | 94   |
| [451] | 93   |
| [495] | 92   |
| [545] | 91   |





One, two^ buckle my shoe Three， four， shut the door Five, six， pick up sticks Seven, eight, lay them straight Nine， ten, a big fat hen One, two, buckle my shoe Three, four, shut the door Five， six, pick up sticks Seven, eight, lay them straight Nine， ten， a big fat hen

One, two, buckle my shoe Three, four\ shut the door Five, six， pick up sticks Severy eight， lay them straight Nine， ten， a big fat hen

[4557]    5    One, two, buckle my shoe

[4602]    4    Three, four, shut the door

[4649]    3    Five, six, pick up sticks

[4693]    2    Seven, eight, lay them straight

[4743]    1    Nine, ten, a big fat hen

\2. SequenceFile 的读操作

从头到尾读取顺序文件不外乎创建SequenceFile.Reader实例后反复调用 next()方法迭代读取记录。读取的是哪条记录与你使用的序列化框架相关。如果 使用的是Writable类型，那么通过键和值作为参数的next()方法可以将数据流 中的下一条键-值对读入变量中：

public boolean next(Writable key, Writable val)

如果键-值对成功读取，则返回true，如果已读到文件末尾，则返回false。

对于其他非Writable类型的序列化框架（比如Apache Thrift）,则应该使用下面两 个方法：

public Object next(Object key) throws IOException

public Object getCurrentValue(Object val) throws IOException

在这种情况下，需要确保io.serializations属性已经设置了你想使用的序列 化框架，详情参见5.3.4节。

如果next()方法返回的是非null对象，则可以从数据流中读取键、值对，并且 可以通过getCurrentValue()方法读取该值。否则，如果next()返回null 值，则表示已经读到文件末尾。

范例5-11中的程序显示了如何读取包含Writable类型键、值对的顺序文件。注意 如何通过调用getKeyClass()方法和getValueClass()方法进而发现SequenceFile中所 使用的类型，然后通过ReflectionUtils对象生成常见键和值的实例。通过这个技 术，该程序可用于处理有Writable类型键、值对的任意一个顺序文件。

范例 5-11.读取 SequenceFile

public class SequenceFileReadDemo {

public static void main(String[] args) throws IOException {

String uri = args[0];

Configuration conf = new Configuration();

FileSystem fs = FileSystem.get(URI.create(uri)conf);

Path path = new Path(uri);

SequenceFile.Reader reader = null; try {

reader = new SequenceFile.Reader(fs^ path, conf);

Writable key = (Writable)

ReflectionUtils.newlnstance(reader.getKeyClass(), conf);

.Writable value = (Writable)

ReflectionUtils.newlnstance(reader.getValueClass(), conf); long position = reader.getPosition(); while (reader.nextCkey^ value)) {

String syncSeen = reader.syncSeen() ? 11 *M :

System.out.printf("[%s%s]\t%s\t%s\n", position, syncSeen, key, value); position = reader.getPosition(); // beginning of next record

}

} finally {

IOUtils.closestream(reader);

}

}

}

该程序的另一个特性是能够显示顺序文件中同步点的位置信息。所谓同步点，是 指数据读取迷路(lost)后能够再一次与记录边界同步的数据流中的某个位置，例 如，在数据流中由于搜索而跑到任意位置后可采取此动作。同步点是由 SequenceFile.Writer记录的，后者在顺序文件写入过程中插入一个特殊项以便每 隔几个记录便有一个同步标识。这样的特殊项非常小，因而只造成很小的存储开 销，不到1%。同步点始终位于记录的边界处。

运行范例5-11的程序后，会显示星号表示的顺序文件中的同步点。第一同步点位 于2021处(第二个位于4075处，但本例中并没有显示出来)：

% hadoop SequenceFileReadDemo numbers.seq



[451] 93 [495] 92 [545] 91 [590] 90



[1976] 60 [2021*] 59 [2088] 58 [2132] 57 [2182] 56





One, two, buckle my shoe Three, four\ shut the door Five^ six, pick up sticks Seven, eight, lay them straight Nine, ten， a big fat hen One, two, buckle my shoe Three^ four，shut the door

Five， six, pick up sticks Seven, eight, lay them straight Nine， ten， a big fat hen One, two, buckle my shoe

One, two, buckle my shoe Three， four^ shut the door Five, six， pick up sticks Severy eight， lay them straight Nine, ten， a big fat hen

One, two， buckle my shoe Three， four， shut the door Five, six^ pick up sticks Seven, eight, lay them straight Nine， tei% a big fat hen

在顺序文件中搜索给定位置有两种方法。第一种是调用seek()方法，该方法将读 指针指向文件中指定的位置。例如，可以按如下方式搜查记录边界：

reader.seek(359);

assertThat(reader.next(key, value), is(true)); assertThat(((IntWritable) key).get()，is(95));

但如果给定位置不是记录边界，调用next()方法时就会出错：

reader.seek(360);

reader.next(keyvalue); // fails with IOException

第二种方法通过同步点查找记录边界。SequenceFile.Reader对象的sync(long

position)方法可以将读取位置定位到position之后的下一个同步点。如果position 之后没有同步了，那么当前读取位置将指向文件末尾。这样，我们对数据流中的 任意位置调用sync()方法(不一定是一个记录的边界)而且可以重新定位到下一个同 步点并继续向后读取：

reader.sync(360);

assertThat(reader>getPosition()^ is(2021L)); assertThat(reader.next(key4value)is(true)); assertThat(((IntWritable) key).get(), is(59));

![img](Hadoop43010757_2cdb48_2d8748-83.jpg)



SequenceFile.Writer对象有一个sync()方法，该方法可以在数据流的当前 位置插入一个同步点。不要把它和Syncable接口中定义的hsync()方法混为 一谈，后者用于底层设备缓冲区的同步。详情可以参见3.6.3节。

可以将加入同步点的顺序文件作为MapReduce的输入，因为该类顺序文件允许切 分，由此该文件的不同部分可以由独立的map任务单独处理。参见8.2.3节对

SequenceFilelnputFormat 的详细介绍。

1.通过命令行接口显示SequenceFile

hadoop fs命令有一个-text选项可以以文本形式显示顺序文件。该选项可以查 看文件的代码，由此检测出文件的类型并将其转换成相应的文本。该选项可以识 别gzip压缩文件、顺序文件和Avm数据文件；否则，便假设输入为纯文本文件。

对于顺序文件，如果键和值是有具体含义的字符串表示，那么这个命令就非常有 用(通过toString()方法定义)。同样，如果有自己定义的键或值的类，则需要确 保它们在Hadoop类路径目录下。

对前一小节中创建的顺序文件执行这个命令，我们得到如下输出:

| % hadoop fs -text numbers.seq \| head |                                   |
| ------------------------------------- | --------------------------------- |
| 100                                   | One, two, buckle my shoe          |
| 99                                    | Three， four\ shut the door       |
| 98                                    | Five^ six^ pick up sticks         |
| 97                                    | Seven， eighty lay them straight  |
| 96                                    | Nine， ten^ a big fat hen         |
| 95                                    | One, two, buckle my shoe          |
| 94                                    | Three, four, shut the door        |
| 93                                    | Five, six, pick up sticks         |
| 92                                    | Seven， eight， lay them straight |
| 91                                    | Nine, ten， a big fat hen         |

\2. SequenceFile的排序和合并

MapReduce是对多个顺序文件进行排序（或合并）最有效的方法。MapReduce本身 是并行的，并且可由你指定要使用多少个reducer（该数决定着输出分区数）。例 如，通过指定一个reducer,可以得到一个输出文件。我们可以使用Hadoop发行 版自带的例子，通过指定键和值的类型来将输入和输出指定为顺序文件：

% hadoop jar \

$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*ejar \ sort -r 1 \

-inFormat org.apache.hadoop.mapreduce.lib.input.SequenceFilelnputFormat \ -outFormat org.apache•hadoop•mapreduce.lib•output•SequenceFileOutputFormat \ -outKey org•apache.hadoop.io.IntWritable \

-outvalue org•apache•hadoop•io.Text \ numbers.seq sorted

% hadoop fs -text sorted/part-r-00000 | head

1    Nine, ten， a big fat hen

2    Severy eighty lay them straight

3    Five, six^ pick up sticks

4    Three, four, shut the door

5    One, two, buckle my shoe

6    Nine, ten^ a big fat hen

7    Seven, eight, lay them straight

8    Five, six, pick up sticks

9    Three, four\ shut the door

10    One, two, buckle my shoe

更多详情可以参见9.2节。

除了通过MapReduce实现排序/归并，还有一种方法是使用SequenceFile.Sorter类中的 sort（）方法和merge（）方法。它们比MapReduce更早出现，比MapReduce更底

层（例如，为了实现并行，需要手动对数据进行分区），所以对顺序文件进行排序合 并时采用MapReduce是更佳的选择。

\3. SequenceFile 的格式

顺序文件由文件头和随后的一条或多条记录组成（参见图5-2）。顺序文件的前三个 字节为SEQ（顺序文件代码），紧随其后的一个字节表示顺序文件的版本号。文件头 还包括其他字段，例如键和值类的名称、数据压缩细节、用户定义的元数据以及 同步标识。®如前所述，同步标识用于在读取文件时能够从任意位置开始识别记录 边界。每个文件都有一个随机生成的同步标识，其值存储在文件头中。同步标识

①这些字段的格式细节可参见SequenceFile的文档（/z//p://ZH7./y/^we«ceXcfocs）和源码。

位于顺序文件中的记录与记录之间。同步标识的额外存储开销要求小于1%，所以 没有必要在每条记录末尾添加该标识（特别是比较短的记录）。

![img](Hadoop43010757_2cdb48_2d8748-84.jpg)



Record 姻 Syp< Record



No

compression

Record

compression

4

4

Bo



5-2.压缩前和压缩后的顺序文件的内部结构



记录的内部结构取决于是否启用压缩。如果已经启用压缩，则结构取决于是记录 压缩还是数据块压缩。

如果没有启用压缩（默认情况），那么每条记录则由记录长度（字节数）、键长度、键 和值组成。长度字段为4字节长的整数，遵循java.io.DataOutput类中writelnt（） 方法的协定。为写入顺序文件的类定义Serialization类，通过它来实现键和值 的序列化。

记录压缩格式与无压缩情况基本相同，只不过值是用文件头中定义的codec压缩 的。注意，键没有被压缩。

如图5-3所示，块压缩（block compression）是指一次性压缩多条记录，因为它可以

利用记录间的相似性进行压缩，所以相较于单条记录压缩方法，该方法的压缩效 率更高。可以不断向数据块中压缩记录，直到块的字节数不小于io.seqfile. compress.blocksize属性中设置的字节数：默认为1 MB。每一个新块的开始处 都需要插入同步标识。数据块的格式如下：首先是一个指示数据块中字节数的字 段；紧接着是4个压缩字段（键长度、键、值长度和值）。

图5-3.采用块压缩方式之后，顺序文件的内部结构

###### 5.4.2 关于 MapFile

MapFile是已经排过序的SequenceFile,它有索引，所以可以按键查找。索引 自身就是一个SequenceFile,包含了 map中的一小部分键(默认情况下，是每 隔128个键)。由于索引能够加载进内存，因此可以提供对主数据文件的快速查 找。主数据文件则是另一个SequenceFile,包含了所有的map条目，这些条目 都按照键顺序进行了排序。

MapFile提供了一个用于读写的、与SequenceFile非常类似的接口。需要注意 的是，当使用MapFile.Writer进行写操作时，map条目必须顺序添加，否则会 抛出IOException异常。

\1. MapFile的变种

Hadoop在通用的键-值对MapFile接口上提供了一些变种

• SetFile是一个特殊的MapFile，用于存储Writable键的集合。键必

须按照排好的顺序添加。

•    ArrayFile也是一个MapFile变种，该变种中的键是一个整型，用于 表示数组中元素的索引，而值是一个Writable值。

•    BloomMapFile也是一个MapFile变种，该变种提供了 get()方法的一 个高性能实现，对稀疏文件特别有用。该实现使用一个动态的布隆过滤 器来检测某个给定的键是否在map文件中。这个测试非常快，因为是在 内存中完成的，但是该测试结果出现假阳性的概率大于零。仅当测试通 过时(键存在)，常规的get()方法才会被调用。

###### 5.4.3其他文件格式和面向列的格式

顺序文件和map文件是Hadoop中最早的、但并不是仅有的二进制文件格式，事 实上，对于新项目而言，有更好的二进制文件格式可供选择。

Avro数据文件（详见12.3节）在某些方面类似顺序文件，是面向大规模数据处理而 设计的（紧凑且可切分）。但是Avro数据文件又是可移植的，它们可以跨越不同的 编程语言使用。Avro数据文件中存储的对象使用模式来描述，而不是像 Writable对象的实现那样使用Java代码（例如顺序文件就是这样的情况，这样的 弊端是过于以Java为中心）。Avro数据文件被Hadoop生态系统的各组件广为支 持，因此它们被默认为是对二进制格式的一种比较好的选择。

顺序文件、map文件和Avm数据文件都是面向行的格式，意味着每一行的值在文 件中是连续存储的。在面向列的格式中，文件中的行（或等价的，Hive中的一张表） 被分割成行的分片，然后每个分片以面向列的形式存储：首先存储每行第1列的 值，然后是每行第2列的值，如此以往。该过程如图5-4所示。

Logical table

|      | co"  | col2  | col3 |
| ---- | ---- | ----- | ---- |
| row! | 1    | 2     | 幽   |
| row2 | 4    | 5     |      |
| row3 | 7    | ':::8 | 顯   |
| row4 | 10   | 11    | 12   |

Row-oriented layout (SequenceFile)

| rowl | row2 | row3             | row4 |      |      |      |      |      |      |      |      |      |
| ---- | ---- | ---------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 2    | 3i.    .*•    '1 | 4    | 5    | \|6  | 7    | 8    | Q    |      | 10   | iij  | 12   |

Column-oriented layout (RCFile)

J row split 1    J row split 2

5-4.面向行的和面向列的存储

面向列的存储布局可以使一个查询跳过那些不必访问的列。让我们考虑一个只需 要处理图5-4中表的第2列的査询。在像顺序文件这样面向行的存储中，即使是 只需要读取第二列，整个数据行（存储在顺序文件的一条记录中）将被加载进内存。 虽说“延迟反序列化” （lazy deserialization）策略通过只反序列化那些被访问的列字 段能节省一些处理开销，但这仍然不能避免从磁盘上读入一个数据行所有字节而 付出的开销。

如果使用面向列的存储，只需要把文件中第2列所对应的那部分（图中高亮部分）读 入内存。一般来说，面向列的存储格式对于那些只访问表中一小部分列的査询比 较有效。相反，面向行的存储格式适合同时处理一行中很多列的情况。

由于必须在内存中缓存行的分片、而不是单独的一行，因此面向列的存储格式需 要更多的内存用于读写。并且，当出现写操作时（通过flush或sync操作），这种缓 存通常不太可能被控制，因此，面向列的格式不适合流的写操作，这是因为，如 果writer处理失败的话，当前的文件无法恢复。另一方面，对于面向行的存储格 式，如顺序文件和Avro数据文件，可以一直读取到writer失败后的最后的同步 点。由于这个原因，Flume（详见第14章）使用了面向行的存储格式。

Hadoop中的第一个面向列的文件格式是Hive的7?CF//e（Record Columnar File）, 它已经被 Hive 的（9/?CF/7e（Optimized Record Columnar File）及    取代（详见第

13章）。Parquet是一个基于Google Dremel的通用的面向列的文件格式，被 Hadoop组件广为支持。Avro也有一个面向列的文件格式，称为Trevni0
