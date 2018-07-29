# Arduino 介绍






# TODO






  * aaa




# MOTIVE






  * aaa





* * *































##






Arduino是一个基于易用硬件和软件的原型平台(开源)。它包由可编程的电路板（简称微控制器）和称为Arduino IDE（集成开发环境）的现成软件组成，用于将计算机代码写入并上传到物理板。Arduino提供将微控制器的功能打破成更易于使用的软件包的标准外形。

![Arduino教程](https://www.w3cschool.cn/attachments/image/20170105/1483603420630934.png)


## 适用人群


本教程面向对Arduino感兴趣的的学生或爱好者。使用Arduino，我们可以非常快地了解微控制器和传感器的基础知识，并且可以开始构建原型，而只需很少的投资。本教程旨在让你在学习如何使用Arduino及其各种功能，并能学以致用。


## 学习前提


在开学习行本教程之前，我们假设你已经熟悉C和C++的基础知识。如果你不太清楚这些概念，那么我们建议你先学习一下我们的 [C](https://www.w3cschool.cn/c/) 和 [C++](https://www.w3cschool.cn/cpp/) 的相关教程。对微控制器和电子器件的基本理解是有帮助的。


## Arduino主要特点


* Arduino板卡能够读取来自不同传感器的模拟或数字输入信号，并将其转换为输出，例如激活电机，打开/关闭LED，连接到云端等多种操作。

* 你可以通过Arduino IDE（简称上传软件）向板上的微控制器发送一组指令来控制板功能。

* 与大多数以前的可编程电路板不同，Arduino不需要额外的硬件（称为编程器）来将新代码加载到板上。你只需使用USB线即可。

* 此外，Arduino IDE使用C++的简化版本，使其更容易学习编程。

* 最后，Arduino提供了一个标准的外形规格，将微控制器的功能打破成更易于使用的软件包。




![Arduino板卡](http://www.w3cschool.cn/attachments/tuploads/arduino/board.jpg)







## Arduino板的类型


根据使用的不同微控制器，可提供各种Arduino板。然而，所有Arduino板都有一个共同点：它们通过Arduino IDE编程。

差异基于输入和输出的数量（可以在单个板上使用的传感器，LED和按钮的数量），速度，工作电压，外形尺寸等。一些板被设计为嵌入式，并且没有编程接口（硬件），因此你需要单独购买。有些可以直接从3.7V电池运行，其他至少需要5V。

以下是可用的不同Arduino板的列表。


**基于ATMEGA328微控制器的Arduino板**

| 板名称                         | 工作电压 | 时钟速度 | 数字i/o | 模拟输入 | PWM  | UART | 编程接口          |
| ------------------------------ | -------- | -------- | ------- | -------- | ---- | ---- | ----------------- |
| Arduino Uno R3                 | 5V       | 16MHz    | 14      | 6        | 6    | 1    | USB通过ATMega16U2 |
| Arduino Uno R3 SMD             | 5V       | 16MHz    | 14      | 6        | 6    | 1    | USB通过ATMega16U2 |
| Red Board                      | 5V       | 16MHz    | 14      | 6        | 6    | 1    | USB通过FTDI       |
| Arduino Pro 3.3v/8 MHz         | 3.3V     | 8MHz     | 14      | 6        | 6    | 1    | FTDI兼容头        |
| Arduino Pro 5V/16MHz           | 5V       | 16MHz    | 14      | 6        | 6    | 1    | FTDI兼容头        |
| Arduino mini 05                | 5V       | 16MHz    | 14      | 8        | 6    | 1    | FTDI兼容头        |
| Arduino Pro mini 3.3v/8mhz     | 3.3V     | 8MHz     | 14      | 8        | 6    | 1    | FTDI兼容头        |
| Arduino Pro mini 5v/16mhz      | 5V       | 16MHz    | 14      | 8        | 6    | 1    | FTDI兼容头        |
| Arduino Ethernet               | 5V       | 16MHz    | 14      | 6        | 6    | 1    | FTDI兼容头        |
| Arduino Fio                    | 3.3V     | 8MHz     | 14      | 8        | 6    | 1    | FTDI兼容头        |
| LilyPad Arduino 328 main board | 3.3V     | 8MHz     | 14      | 6        | 6    | 1    | FTDI兼容头        |
| LilyPad Arduino simply board   | 3.3V     | 8MHz     | 9       | 4        | 5    | 0    | FTDI兼容头        |

**基于ATMEGA32u4微控制器的Arduino板卡**

| 板名称              | 工作电压 | 时钟速度 | 数字i/o | 模拟输入 | PWM  | UART | 编程接口 |
| ------------------- | -------- | -------- | ------- | -------- | ---- | ---- | -------- |
| Arduino Leonardo    | 5V       | 16MHz    | 20      | 12       | 7    | 1    | 本机USB  |
| Pro micro 5V/16MHz  | 5V       | 16MHz    | 14      | 6        | 6    | 1    | 本机USB  |
| Pro micro 3.3V/8MHz | 5V       | 16MHz    | 14      | 6        | 6    | 1    | 本机USB  |
| LilyPad Arduino USB | 3.3V     | 8MHz     | 14      | 6        | 6    | 1    | 本机USB  |

**基于ATMEGA2560微控制器的Arduino板卡**

| 板名称               | 工作电压 | 时钟速度 | 数字i/o | 模拟输入 | PWM  | UART | 编程接口           |
| -------------------- | -------- | -------- | ------- | -------- | ---- | ---- | ------------------ |
| Arduino Mega 2560 R3 | 5V       | 16MHz    | 54      | 16       | 14   | 4    | USB通过ATMega16U2B |
| Mega Pro 3.3V        | 3.3V     | 8MHz     | 54      | 16       | 14   | 4    | FTDI兼容头         |
| Mega Pro 5V          | 5V       | 16MHz    | 54      | 16       | 14   | 4    | FTDI兼容头         |
| Mega Pro Mini 3.3V   | 3.3V     | 8MHz     | 54      | 16       | 14   | 4    | FTDI兼容头         |

**基于AT91SAM3X8E微控制器的Arduino板卡**

| 板名称               | 工作电压 | 时钟速度 | 数字i/o | 模拟输入 | PWM  | UART | 编程接口 |
| -------------------- | -------- | -------- | ------- | -------- | ---- | ---- | -------- |
| Arduino Mega 2560 R3 | 3.3V     | 84MHz    | 54      | 12       | 12   | 4    | 本机USB  |





## REF

1. [Arduino教程](https://www.w3cschool.cn/arduino/)
