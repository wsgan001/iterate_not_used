
# 缘由：


总结rnn和lstm的一些应用：


# 1.图像描述：




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac348c1d179a.png)


加强版：注意力模型

代码请戳 https://github.com/jazzsaxmafia/show_attend_and_tell.tensorflow


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac348dc15556.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac348f24a26a.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac349180903d.png)





# 2.回到生成模型




## 字符级别的生成模型


https://gist.github.com/karpathy/d4dee566867f8291f086


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3493a887b2.png)





## RNN 生成模型仿照维基百科


数据请戳：http://cs.stanford.edu/people/karpathy/char-rnn/wiki.txt


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac33f842b331.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac33f89c815a.png)





## RNN生成模型：到底发生了什么？


依旧是：https://gist.github.com/karpathy/d4dee566867f8291f086


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac33ff55673a.png)


第一行是真正的输入的文本，每一列是输入那个字符之后，猜测的可能后续会输入什么。蓝色表示的是强烈的认为下一个字符是应该是这个字符，是一个正向的非常大的值。**好像没有很明白**


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3400b7782a.png)





## RNN生成模型”写“食谱？


案例：https://gist.github.com/nylki/1efbaa36635956d35bcc

代码继续用：https://gist.github.com/karpathy/d4dee566867f8291f086

数据请戳：http://www.ffts.com/recipes/lg/lg32965.zip


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac340b016114.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac340b86b62e.png)





## 模仿奥巴马演讲？


https://medium.com/@samim/obama-rnn-machine-generated-political-speeches-c8abd18a2ea0#.9sb793kbm


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac3415246e3b.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac341555a203.png)





## 合成音乐


如果能把音乐的乐谱表示成文本形式，是不是可以借用RNN？

https://highnoongmt.wordpress.com/2015/05/22/lisls-stis-recurrent-neural-networks-for-folk-music-generation


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac341bdbd7c6.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac341c0bbccc.png)


Abc notaion 转化 参见：

http://abcnotation.com/blog/2010/01/31/how-to-understand-abc-the-basics/


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac341e94f8f2.png)





## 更高级的音乐合成模式？


前面的方法合成的音乐比较“单调”，有高级一点的方法对音乐进行合成吗？

http://www.hexahedria.com/2015/08/03/composing-music-with-recurrent-neural-networks/


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac349b2a3aa7.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac349c4cd4b2.png)





# 翻译系统




## 关于SMT的一点小背景






  * 源语言法语f，目标语言英语e


  * 概率公式\(\hat{e}=argmax_ep(e|f)=argmax_ep(f|e)p(e)\)


    * \(p(f|e)\) 为短语到短语的概率，在平行语料上统计得到


    * \(p(e)\)为语言模型，n-gram







![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34a1e966fc.png)







  * 生成翻译模型需要“对齐”




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34a498887d.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34a32034a6.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34a4d1af75.png)















![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34a86c964c.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34ab103833.png)





## SMT的“解码”过程


会得到很多候选翻译短语


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34acf97077.png)


树搜索与剪枝 => beam-search


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34ae1193d2.png)





# 神经网络翻译系统




## 初版：


所有的源语言信息压缩到“记忆”里，再从“记忆”里解码


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b059be66.png)


编码过程


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b1862673.png)


解码过程


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b1e2ff7e.png)


最小化交叉熵损失


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b297ee26.png)





## 小小的改进，稠密向量(embedding dense vector)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b388d2e5.png)





## 所以效果如何？






  * 这种方式的NMT模型，比传统的SMT模型要差


  * 整体差很多,某些条件下差距小




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b4ed3fed.png)







  * 句子越长，效果越差


  * 词表越大，UNK越少,翻译效果越好




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b6c98e28.png)




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b70ad2f3.png)





## 复习：双向RNN


双向RNN

有些情况下，当前的输出不只依赖于之前的序列元素，还可能依赖之后的序列元素




  * 比如从一段话踢掉部分词，让你补全


  * 直观理解：双向RNN叠加




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34b9eca3bc.png)





## 改进的模型






  *  双向RNN用于捕获周边(两侧的信息)


  * “注意力”模型关注当前翻译的词


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34bd79207c.png)

Encoder部分：


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34bd42e914.png)


Decoder部分


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c0952e4b.png)


Attention部分


![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c1ecfdef.png)





## 关于encoder更细的解释






  * 要避免信息全都压缩到一个向量里


  * 双向RNN对每个input x都生成正反的标记向量


  * 源语言用n个标记向量表示




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c52cae15.png)





## 关于“注意力”模型更细的解释






  * 句子是变化长度的，要集中精力在某个部分上


  * 用不同的权重对标记向量进行加权得到context向量


  * 上面的权重是通过反向神经网络用softmax激励得到的




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c6d0d156.png)





## Tensorflow序列到序列的学习






  * https://www.tensorflow.org/versions/r0.10/tutorials/seq2seq/
index.html


  * https://github.com/tensorflow/tensorflow/tree/master/tensorflow/
models/rnn/translate




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c95d9e83.png)





## 来看看Tensorflow的实现




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34c9f14bd6.png)


一些tricks




  * Sampled softmax and output projection




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34cadb9d47.png)







  * Bucketing and padding




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34cb528c23.png)





## 神经网络翻译系统的现状？




![](http://106.15.37.116/wp-content/uploads/2018/04/img_5ac34cc4956fc.png)









# COMMENT：


**很多都可以单独拿出来自己实践后进行补充和修正。**


# REF：






  1. 七月在线 深度学习
