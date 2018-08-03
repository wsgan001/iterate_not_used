# Arduino 板的说明


# TODO

在本章中，我们将了解Arduino板上的不同组件。将学习Arduino UNO板，因为它是Arduino板系列中最受欢迎的。此外，它是开始使用电子和编码的最佳板。有些板看起来与下面给出的有些不同，但多数Arduino中的这些组件大部分是共同的。




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/LIEhH7B5Gd.png?imageslim)


<table >
<tbody >
<tr >

<td width="9%;" >1
</td>

<td >**电源USB**

Arduino板可以通过使用计算机上的USB线供电。你需要做的是将USB线连接到USB接口。
</td>
</tr>
<tr >

<td width="9%" >2
</td>

<td >**电源（桶插座）**

Arduino板可以通过将其连接到电影插口直接从交流电源供电。
</td>
</tr>
<tr >

<td width="9%" >3
</td>

<td >**稳压器**

稳压器的功能是控制提供给Arduino板的电压，并稳定处理器和其他元件使用的直流电压。
</td>
</tr>
<tr >

<td width="9%;" >4
</td>

<td >**晶体振荡器**

晶振帮助Arduino处理时间问题。Arduino如何计算时间？答案是，通过使用晶体振荡器。在Arduino晶体顶部打印的数字是16.000H9H。它告诉我们，频率是16,000,000赫兹或16MHz。
</td>
</tr>
<tr >

<td width="9%;" >5,17
</td>

<td >**Arduino重置**

你可以重置你的Arduino板，例如从一开始就启动你的程序。可以通过两种方式重置UNO板。首先，通过使用板上的复位按钮（17）。其次，你可以将外部复位按钮连接到标有RESET（5）的Arduino引脚。
</td>
</tr>
<tr >

<td width="9%;" >6,7,8,9
</td>

<td >**引脚（3.3，5，GND，Vin）**




  * 3.3V（6） - 提供3.3输出电压


  * 5V（7） - 提供5输出电压


  * 使用3.3伏和5伏电压，与Arduino板一起使用的大多数组件可以正常工作。


  * GND（8）（接地） - Arduino上有几个GND引脚，其中任何一个都可用于将电路接地。


  * VVin（9） - 此引脚也可用于从外部电源（如交流主电源）为Arduino板供电。



</td>
</tr>
<tr >

<td width="9%;" >10
</td>

<td >**模拟引脚**

Arduino UNO板有六个模拟输入引脚，A0到A5。这些引脚可以从模拟传感器（如湿度传感器或温度传感器）读取信号，并将其转换为可由微处理器读取的数字值。
</td>
</tr>
<tr >

<td width="9%;" >11
</td>

<td >**微控制器**

每个Arduino板都有自己的微控制器（11）。你可以假设它作为板的大脑。Arduino上的主IC（集成电路）与板对板略有不同。微控制器通常是ATMEL公司的。在从Arduino IDE加载新程序之前，你必须知道你的板上有什么IC。此信息位于IC顶部。有关IC结构和功能的更多详细信息，请参阅数据表。
</td>
</tr>
<tr >

<td width="9%;" >12
</td>

<td >**ICSP引脚**

大多数情况下，ICSP（12）是一个AVR，一个由MOSI，MISO，SCK，RESET，VCC和GND组成的Arduino的微型编程头。它通常被称为SPI（串行外设接口），可以被认为是输出的“扩展”。实际上，你是将输出设备从属到SPI总线的主机。
</td>
</tr>
<tr >

<td width="9%;" >13
</td>

<td >**电源LED指示灯**

当你将Arduino插入电源时，此LED指示灯应亮起，表明你的电路板已正确通电。如果这个指示灯不亮，那么连接就出现了问题。
</td>
</tr>
<tr >

<td width="9%;" >14
</td>

<td >**TX和RX LED**

在你的板上，你会发现两个标签：TX（发送）和RX（接收）。它们出现在Arduino UNO板的两个地方。首先，在数字引脚0和1处，指示引脚负责串行通信。其次，TX和RX LED（13）。发送串行数据时，TX LED以不同的速度闪烁。闪烁速度取决于板所使用的波特率。RX在接收过程中闪烁。
</td>
</tr>
<tr >

<td width="9%;" >15
</td>

<td >**数字I/O**

Arduino UNO板有14个数字I/O引脚（15）（其中6个提供PWM（脉宽调制）输出），这些引脚可配置为数字输入引脚，用于读取逻辑值（0或1） ；或作为数字输出引脚来驱动不同的模块，如LED，继电器等。标有“〜”的引脚可用于产生PWM。
</td>
</tr>
<tr >

<td width="9%;" >16
</td>

<td >**AREF**

AREF代表模拟参考。它有时用于设置外部参考电压（0至5伏之间）作为模拟输入引脚的上限。
</td>
</tr>
</tbody>
</table>













## REF

1. [Arduino教程](https://www.w3cschool.cn/arduino/)
