---
author: evo
comments: true
date: 2018-05-05 07:08:11+00:00
layout: post
link: http://106.15.37.116/2018/05/05/arduino-%e5%ad%97%e7%ac%a6%e4%b8%b2/
slug: arduino-%e5%ad%97%e7%ac%a6%e4%b8%b2
title: Arduino 字符串
wordpress_id: 5285
categories:
- 基础工具使用
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



字符串用于存储文本。它们可用在LCD或Arduino IDE串口监视器窗口中显示文本。字符串也可用于存储用户输入。例如，用户在连接到Arduino的键盘上键入的字符。

在Arduino编程中有两种类型的字符串：



 	
  * 字符数组，与C编程中使用的字符串相同。

 	
  * Arduino字符串，它允许我们在草图中使用字符串对象。


在本章中，我们将学习Arduino草图中的字符串，对象和字符串的使用。在本章末尾，你将学习在草图中使用哪种类型的字符串。


## 字符串字符数组


我们要学习的第一种类型的字符串是** char **类型的一系列字符。在前面的章节中，我们学习了一个数组是什么：存储器中存储的相同类型的变量的连续序列。一个字符串是一个char变量的数组。

字符串是一个特殊的数组，在字符串的末尾有一个额外的元素，其值总是为0（零）。这被称为“空终止字符串”。


### 字符串字符数组示例


此示例将显示如何创建字符串并将其打印到串口监视器窗口。

**例**

    
    void setup() {
       char my_str[6]; // an array big enough for a 5 character string
       Serial.begin(9600);
       my_str[0] = 'H'; // the string consists of 5 characters
       my_str[1] = 'e';
       my_str[2] = 'l';
       my_str[3] = 'l';
       my_str[4] = 'o';
       my_str[5] = 0; // 6th array element is a null terminator
       Serial.println(my_str);
    }
    
    void loop() { 
    
    }
    


以下示例显示了字符串由什么组成。一个具有可打印字符的字符数组和0作为数组的最后一个元素，表示这是字符串结束的位置。通过使用** Serial.println()**并传递字符串的名称，可以将字符串打印到Arduino IDE串口监视器窗口。

同样的例子可以用更方便的方式编写，如下所示：

**示例**

    
    void setup() {
       char my_str[] = "Hello";
       Serial.begin(9600);
       Serial.println(my_str);
    }
    
    void loop() {
    
    }
    


在这个草图中，编译器计算字符串数组的大小，并自动使用空值0终止字符串。一个长度为六个元素长，由五个字符后跟一个零组成的数组，其创建方式与上一个草图完全相同。


## 操作字符串数组


我们可以在草图中更改字符串数组，如下图所示。


### 例子



    
    void setup() {
       char like[] = "I like coffee and cake"; // create a string
       Serial.begin(9600);
       // (1) print the string
       Serial.println(like);
       // (2) delete part of the string
       like[13] = 0;
       Serial.println(like);
       // (3) substitute a word into the string
       like[13] = ' '; // replace the null terminator with a space
       like[18] = 't'; // insert the new word
       like[19] = 'e';
       like[20] = 'a';
       like[21] = 0; // terminate the string
       Serial.println(like);
    }
    
    void loop() {
    
    }




### 结果



    
    I like coffee and cake
    I like coffee
    I like coffee and tea


以上草图按以下方式工作。


### （1）创建和打印字符串


在上面给出的草图中，创建了一个新的字符串，然后打印出来显示在串口监视器窗口中。


### （2）缩短字符串


通过用空终止0替换字符串中的第14个字符来缩短字符串。这是从0开始计算的字符串数组中的13号元素。

打印字符串时，所有字符都打印到新的空终止0。其他字符不消失；它们仍然存在于内存中，并且字符串数组仍然是相同的大小。唯一的区别是任何使用字符串的函数只能看到第一个空终止符前的字符串。


### （3）更改字符串中的单词


最后，草图用“tea”代替“cake”一词。它首先必须用空格替换空终止符，如[13]，以便将字符串恢复为原来的格式。

新字符用单词“tea”覆盖单词“cake”的“cak”。这是通过覆盖单个字符来完成的。“cake”的“e”被替换为新的空终止字符。结果是字符串实际上终止于两个空字符，即字符串末尾的原始字符，以及替换“cake”中的“e”的新字符。这在打印新字符串时没有区别，因为打印字符串的函数在遇到第一个空终止字符时将停止打印字符串字符。


## 操作字符串数组的函数


上一个草图通过访问字符串中的单个字符，以手动方式操作字符串。为了更方便操作字符串数组，你可以编写自己的函数来执行，也可以使用** C **语言库中的一些字符串函数。

下面显示了操作字符串数组的列表函数。

下一个草图使用了一些C字符串函数。


### 例子



    
    void setup() {
       char str[] = "This is my string"; // create a string
       char out_str[40]; // output from string functions placed here
       int num; // general purpose integer
       Serial.begin(9600);
    
       // (1) print the string
       Serial.println(str);
    
       // (2) get the length of the string (excludes null terminator)
       num = strlen(str);
       Serial.print("String length is: ");
       Serial.println(num);
    
       // (3) get the length of the array (includes null terminator)
       num = sizeof(str); // sizeof() is not a C string function
       Serial.print("Size of the array: ");
       Serial.println(num);
    
       // (4) copy a string
       strcpy(out_str, str);
       Serial.println(out_str);
    
       // (5) add a string to the end of a string (append)
       strcat(out_str, " sketch.");
       Serial.println(out_str);
       num = strlen(out_str);
       Serial.print("String length is: ");
       Serial.println(num);
       num = sizeof(out_str);
       Serial.print("Size of the array out_str[]: ");
       Serial.println(num);
    }
    
    void loop() {
    
    }




### 结果



    
    This is my string
    String length is: 17
    Size of the array: 18
    This is my string
    This is my string sketch.
    String length is: 25
    Size of the array out_str[]: 40


以上草图按以下方式工作。


### （1）打印字符串


最新创建的字符串将打印到串口监视器窗口，如之前的草图所完成的。


### （2）获取字符串的长度


strlen()函数用于获取字符串的长度。字符串的长度仅对于可打印字符，不包括空终止符。

该字符串包含17个字符，因此我们在串口监视器窗口中看到17个字符。


### （3）获取数组的长度


运算符sizeof()用于获取包含字符串的数组的长度。长度包括空终止符，因此长度比字符串的长度多1。

sizeof()看起来像一个函数，但技术上是一个运算符。它不是C字符串库的一部分，但在草图中用于显示数组大小和字符串大小（或字符串长度）之间的差异。


### （4）复制字符串


strcpy()函数用于将str[]字符串复制到out_num[]数组。strcpy()函数将传递给它的第二个字符串复制到第一个字符串中。现在，字符串的副本存在于out_num[]数组中，但只占用了数组的18个元素，因此在数组中仍然有22个空闲的char元素。这些空闲元素在内存中的字符串的后面可以找到。

将字符串复制到数组中，以便我们在数组中有一些额外的空间用于草图的下一部分，即在字符串的末尾添加一个字符串。


### （5）将字符串附加到字符串（连接）


草图将一个字符串加到另一个字符串，这称为串联。这是使用strcat()函数完成的。strcat()函数将传递给它的第二个字符串放到传递给它的第一个字符串的末尾。

串联后，打印字符串的长度以显示新的字符串长度。然后打印数组的长度，以显示在40个元素长的数组中有一个25个字符长度的字符串。

请记住，25个字符长度的字符串实际上占用了数组的26个字符，因为还有空终止0。


## 数组边界


使用字符串和数组时，在字符串或数组的边界内工作是非常重要的。在示例草图中，创建了一个长度为40个字符的数组，以分配可用于操作字符串的内存。

如果数组太小，而我们尝试复制比数组大的字符串，那么字符串将复制到超出数组的末尾。超出数组末尾的内存可能包含草图中使用的其他重要数据，然而它们将被字符串覆盖。如果超出字符串末尾的内存超出范围，则可能会导致草图崩溃或导致意外行为。























* * *





# COMMENT



