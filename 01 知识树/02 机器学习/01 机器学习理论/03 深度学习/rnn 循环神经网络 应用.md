

# COMMENT：

**很多都可以单独拿出来自己实践后进行补充和修正。**



# 缘由：

总结 rnn 和 lstm 的一些应用：


# 1.图像描述：




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/40akJK4Dd2.png?imageslim)

加强版：注意力模型

代码请戳 https://github.com/jazzsaxmafia/show_attend_and_tell.tensorflow


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1F6H0dcF0B.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/DJAhHcHHgj.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/lF7FGHeJC6.png?imageslim)




# 2.回到生成模型




## 字符级别的生成模型


https://gist.github.com/karpathy/d4dee566867f8291f086


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/aJlAl18eiJ.png?imageslim)




## RNN 生成模型仿照维基百科


数据请戳：http://cs.stanford.edu/people/karpathy/char-rnn/wiki.txt


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kIKb7l6FgA.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0ChKGH7eal.png?imageslim)




## RNN生成模型：到底发生了什么？


依旧是：https://gist.github.com/karpathy/d4dee566867f8291f086


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/D5ig5aAbJG.png?imageslim)

第一行是真正的输入的文本，每一列是输入那个字符之后，猜测的可能后续会输入什么。蓝色表示的是强烈的认为下一个字符是应该是这个字符，是一个正向的非常大的值。**好像没有很明白**


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/e7L9ida6ig.png?imageslim)




## RNN生成模型”写“食谱？


案例：https://gist.github.com/nylki/1efbaa36635956d35bcc

代码继续用：https://gist.github.com/karpathy/d4dee566867f8291f086

数据请戳：http://www.ffts.com/recipes/lg/lg32965.zip


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/8ImkHlJhCi.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/B00f21EIIE.png?imageslim)




## 模仿奥巴马演讲？


https://medium.com/@samim/obama-rnn-machine-generated-political-speeches-c8abd18a2ea0#.9sb793kbm


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Ke0kK5HGCF.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/da18kI6B0G.png?imageslim)



## 合成音乐


如果能把音乐的乐谱表示成文本形式，是不是可以借用RNN？

https://highnoongmt.wordpress.com/2015/05/22/lisls-stis-recurrent-neural-networks-for-folk-music-generation


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/9m6k12F3hl.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/2hi9F5hkB2.png?imageslim)

Abc notaion 转化 参见：

http://abcnotation.com/blog/2010/01/31/how-to-understand-abc-the-basics/


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/CBaI9hF0JH.png?imageslim)




## 更高级的音乐合成模式？


前面的方法合成的音乐比较“单调”，有高级一点的方法对音乐进行合成吗？

http://www.hexahedria.com/2015/08/03/composing-music-with-recurrent-neural-networks/


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/giE8jDGLGa.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/aA77ei045e.png?imageslim)




# 翻译系统

## 关于SMT的一点小背景

* 源语言法语f，目标语言英语e
* 概率公式\(\hat{e}=argmax_ep(e|f)=argmax_ep(f|e)p(e)\)
    * \(p(f|e)\) 为短语到短语的概率，在平行语料上统计得到
    * \(p(e)\)为语言模型，n-gram







![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/kjH4m8GHCF.png?imageslim)






  * 生成翻译模型需要“对齐”


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Idb3fb4jaG.png?imageslim)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/hb1G5Bg2kc.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/h0491h63gB.png?imageslim)




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/J77ai7ehdd.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/B8gB8f2lD9.png?imageslim)




## SMT的“解码”过程


会得到很多候选翻译短语


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/1If3cEf45C.png?imageslim)

树搜索与剪枝 => beam-search


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/j328HJCjHD.png?imageslim)




# 神经网络翻译系统




## 初版：


所有的源语言信息压缩到“记忆”里，再从“记忆”里解码

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/DCjhEbd82D.png?imageslim)

编码过程


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/l9giH57f3i.png?imageslim)

解码过程


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ClhmJiBCE1.png?imageslim)

最小化交叉熵损失


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7dd7lA4kLF.png?imageslim)




## 小小的改进，稠密向量(embedding dense vector)


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/mcHIgHggaK.png?imageslim)



## 所以效果如何？

* 这种方式的NMT模型，比传统的SMT模型要差
* 整体差很多,某些条件下差距小


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/iaChHlb2b5.png?imageslim)



* 句子越长，效果越差
* 词表越大，UNK越少,翻译效果越好




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/g31igjg9Ib.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/17GCe8f59f.png?imageslim)




## 复习：双向RNN


双向RNN

有些情况下，当前的输出不只依赖于之前的序列元素，还可能依赖之后的序列元素

  * 比如从一段话踢掉部分词，让你补全
  * 直观理解：双向RNN叠加




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/D56KJ7E0bA.png?imageslim)




## 改进的模型






  *  双向RNN用于捕获周边(两侧的信息)


  * “注意力”模型关注当前翻译的词


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ibLiBC2hlC.png?imageslim)

Encoder部分：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/h30lbjE17H.png?imageslim)

Decoder部分


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/FdAa5I2d7L.png?imageslim)

Attention部分


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/7ECj62f4fk.png?imageslim)




## 关于encoder更细的解释

* 要避免信息全都压缩到一个向量里
* 双向RNN对每个input x都生成正反的标记向量
* 源语言用n个标记向量表示


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/AL6a4hE16g.png?imageslim)




## 关于“注意力”模型更细的解释

* 句子是变化长度的，要集中精力在某个部分上
* 用不同的权重对标记向量进行加权得到context向量
* 上面的权重是通过反向神经网络用softmax激励得到的




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/Aag809CKij.png?imageslim)




## Tensorflow序列到序列的学习



* https://www.tensorflow.org/versions/r0.10/tutorials/seq2seq/
index.html

* https://github.com/tensorflow/tensorflow/tree/master/tensorflow/
models/rnn/translate


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/80aDaCmgLL.png?imageslim)


## 来看看Tensorflow的实现

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/HCg7a9I42C.png?imageslim)

一些tricks




  * Sampled softmax and output projection




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/J2JG4lE1c5.png?imageslim)






  * Bucketing and padding




![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/0m3HBC87GI.png?imageslim)




## 神经网络翻译系统的现状？



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/GHCF26LDej.png?imageslim)








## REF：

1. 七月在线 深度学习
