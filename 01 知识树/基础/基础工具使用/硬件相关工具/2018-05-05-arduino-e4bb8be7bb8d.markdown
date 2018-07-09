---
author: evo
comments: true
date: 2018-05-05 06:51:00+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e4%bb%8b%e7%bb%8d/
slug: arduino-%e4%bb%8b%e7%bb%8d
title: Arduino 介绍
wordpress_id: 5253
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































## 






Arduino是一个基于易用硬件和软件的原型平台(开源)。它包由可编程的电路板（简称微控制器）和称为Arduino IDE（集成开发环境）的现成软件组成，用于将计算机代码写入并上传到物理板。Arduino提供将微控制器的功能打破成更易于使用的软件包的标准外形。

![Arduino教程](https://www.w3cschool.cn/attachments/image/20170105/1483603420630934.png)


## 适用人群


本教程面向对Arduino感兴趣的的学生或爱好者。使用Arduino，我们可以非常快地了解微控制器和传感器的基础知识，并且可以开始构建原型，而只需很少的投资。本教程旨在让你在学习如何使用Arduino及其各种功能，并能学以致用。


## 学习前提


在开学习行本教程之前，我们假设你已经熟悉C和C++的基础知识。如果你不太清楚这些概念，那么我们建议你先学习一下我们的[C](https://www.w3cschool.cn/c/)和[C++](https://www.w3cschool.cn/cpp/)的相关教程。对微控制器和电子器件的基本理解是有帮助的。


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
<table >
<tbody >
<tr >
板名称
工作电压
时钟速度
数字i/o
模拟输入
PWM
UART
编程接口
</tr>
<tr >

<td >Arduino Uno R3
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >USB通过ATMega16U2
</td>
</tr>
<tr >

<td >Arduino Uno R3 SMD
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >USB通过ATMega16U2
</td>
</tr>
<tr >

<td >Red Board
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >USB通过FTDI
</td>
</tr>
<tr >

<td >Arduino Pro 3.3v/8 MHz
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino Pro 5V/16MHz
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino mini 05
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >8
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino Pro mini 3.3v/8mhz
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >14
</td>

<td >8
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino Pro mini 5v/16mhz
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >8
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino Ethernet
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Arduino Fio
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >14
</td>

<td >8
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >LilyPad Arduino 328 main board
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >LilyPad Arduino simply board
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >9
</td>

<td >4
</td>

<td >5
</td>

<td >0
</td>

<td >FTDI兼容头
</td>
</tr>
</tbody>
</table>
**基于ATMEGA32u4微控制器的Arduino板卡**
<table >
<tbody >
<tr >
板名称
工作电压
时钟速度
数字i/o
模拟输入
PWM
UART
编程接口
</tr>
<tr >

<td >Arduino Leonardo
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >20
</td>

<td >12
</td>

<td >7
</td>

<td >1
</td>

<td >本机USB
</td>
</tr>
<tr >

<td >Pro micro 5V/16MHz
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >本机USB
</td>
</tr>
<tr >

<td >Pro micro 3.3V/8MHz
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >本机USB
</td>
</tr>
<tr >

<td >LilyPad Arduino USB
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >14
</td>

<td >6
</td>

<td >6
</td>

<td >1
</td>

<td >本机USB
</td>
</tr>
</tbody>
</table>
**基于ATMEGA2560微控制器的Arduino板卡**
<table >
<tbody >
<tr >
板名称
工作电压
时钟速度
数字i/o
模拟输入
PWM
UART
编程接口
</tr>
<tr >

<td >Arduino Mega 2560 R3
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >54
</td>

<td >16
</td>

<td >14
</td>

<td >4
</td>

<td >USB通过ATMega16U2B
</td>
</tr>
<tr >

<td >Mega Pro 3.3V
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >54
</td>

<td >16
</td>

<td >14
</td>

<td >4
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Mega Pro 5V
</td>

<td >5V
</td>

<td >16MHz
</td>

<td >54
</td>

<td >16
</td>

<td >14
</td>

<td >4
</td>

<td >FTDI兼容头
</td>
</tr>
<tr >

<td >Mega Pro Mini 3.3V
</td>

<td >3.3V
</td>

<td >8MHz
</td>

<td >54
</td>

<td >16
</td>

<td >14
</td>

<td >4
</td>

<td >FTDI兼容头
</td>
</tr>
</tbody>
</table>
**基于AT91SAM3X8E微控制器的Arduino板卡**
<table >
<tbody >
<tr >
板名称
工作电压
时钟速度
数字i/o
模拟输入
PWM
UART
编程接口
</tr>
<tr >

<td >Arduino Mega 2560 R3
</td>

<td >3.3V
</td>

<td >84MHz
</td>

<td >54
</td>

<td >12
</td>

<td >12
</td>

<td >4
</td>

<td >本机USB
</td>
</tr>
</tbody>
</table>




























* * *





# COMMENT



