---
author: evo
comments: true
date: 2018-05-05 06:55:30+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e5%ae%89%e8%a3%85/
slug: arduino-%e5%ae%89%e8%a3%85
title: Arduino 安装
wordpress_id: 5261
categories:
- 基础工具使用
tags:
- Arduino
---

<!-- more -->

[mathjax]

**注：非原创，推荐直接看原文**


# ORIGINAL






  1. [Arduino教程](https://www.w3cschool.cn/arduino/)




# TODO






  * aaa




# MOTIVE






  * aaa





* * *



在了解Arduino UNO板的主要部分后，我们准备学习如何设置Arduino IDE。一旦我们学到这一点，我们将准备在Arduino板上上传我们的程序。

在本节中，我们将在简单的步骤中学习如何在我们的计算机上设置Arduino IDE，并准备板通过USB线接收程序。

**步骤1** - 首先，你必须有Arduino板（你可以选择你喜欢的板）和一根USB线。如果你使用Arduino UNO，Arduino Duemilanove，Nano，Arduino Mega 2560或Diecimila，你将需要一个标准USB线（A插头到B插头）。如下图所示为你将连接到USB打印机的类型。


标准USB电缆

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/aIK3F11FD6.png?imageslim)


如果使用Arduino Nano，你将需要一条A到Mini-B线，如下图所示。

A 到 Mini-B 电缆：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/FaFfg416JE.png?imageslim)


**步骤2 - 下载Arduino IDE软件。**

你可以从Arduino官方网站的[下载页面](https://www.arduino.cc/en/Main/Software)获得不同版本的Arduino IDE。你必须选择与你的操作系统（Windows，IOS或Linux）兼容的软件。文件下载完成后，解压缩文件。

解压缩文件

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/IlBD04KHG9.png?imageslim)








**步骤3 - 打开板的电源。**

Arduino Uno，Mega，Duemilanove和Arduino Nano通过USB连接到计算机或外部电源自动获取电源。如果你使用Arduino Diecimila，则必须确保板的配置为从USB连接获取电源。电源选择使用跳线，一小块塑料安装在USB和电源插孔之间的三个引脚中的两个。检查它是否在最靠近USB端口的两个引脚上。

使用USB线将Arduino板连接到计算机。绿色电源LED等（标有PWR）应该发光。

**步骤4 - 启动Arduino IDE。**

下载Arduino IDE软件后，需要解压缩该文件夹。在文件夹中，你可以找到带有无穷大标签(application.exe)的应用程序图标。双击该图标以启动IDE。


启动 Arduino IDE


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/5GHlH1mhmc.png?imageslim)





**步骤5 - 打开你的第一个项目。**

一旦软件启动，你有两个选项：




  * 创建一个新项目。


  * 打开一个现有的项目示例。


要创建新项目，请选择Flie→New。

新建项目

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/F83fD8G5Cl.png?imageslim)





要打开现有项目示例，请选择File→Example→Basics→Blink。

打开现有项目示例

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/2g5EBFfl2h.png?imageslim)





在这里，我们只选择一个名为** Blink **的示例。它打开和关闭LED有一些时间延迟。你可以从列表中选择任何其他示例。

**步骤6 - 选择你的Arduino主板。**

为了避免在将程序上载到板上时出现任何错误，必须选择正确的Arduino板名称，该名称与连接到计算机的电路板相匹配。

转到Tools→Board，然后选择你的板。


选择您的Arduino主板

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/e3ccCIJmA9.png?imageslim)





在这里，根据我们的教程选择了Arduino Uno板，但是你必须选择与你使用的板匹配的名称。

**步骤7 - 选择串行端口。**

选择Arduino板的串行设备。转到**Tools→Serial Port**菜单。这可能是COM3或更高（COM1和COM2通常保留为硬件串行端口）。要弄清楚的话，你可以断开你的Arduino板，并重新打开菜单，那么消失的条目应该是Arduino板。重新连接板并选择该串行端口。


选择串行端口

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/jem0C0B0e4.png?imageslim)





**步骤8 - 将程序上传到你的板。**

在解释如何将我们的程序上传到板之前，我们必须演示Arduino IDE工具栏中出现的每个符号的功能。


将程序上传到您的主板


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/F79lf6k1dc.png?imageslim)






- **A** 用于检查是否存在任何编译错误。
- **B** 用于将程序上传到Arduino板。
- **C** 用于创建新草图的快捷方式。
- **D** 用于直接打开示例草图之一。
- **E** 用于保存草图。
- **F** 用于从板接收串行数据并将串行数据发送到板的串行监视器。

现在，只需点击环境中的“Upload”按钮。等待几秒钟，你将看到板上的RX和TX LED灯闪烁。如果上传成功，则状态栏中将显示“Done uploading”消息。

**注意** - 如果你有Arduino Mini，NG或其他电路板，则需要在单击Arduino软件上的上传按钮之前，立即按下电路板上的复位按钮。























* * *





# COMMENT
