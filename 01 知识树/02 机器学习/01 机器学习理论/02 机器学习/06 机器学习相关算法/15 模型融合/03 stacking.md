# Kaggle机器学习之模型融合（stacking）心得

雷锋网 AI科技评论按，本文作者[吉他手](https://www.zhihu.com/people/zhu-a-zhu-83/activities)，雷锋网(公众号：雷锋网) AI科技评论获其授权发布。

此文道出了本人学习Stacking入门级应用的心路历程。

在学习过程中感谢[@贝尔塔](http://www.zhihu.com/people/91e47643e8c66a1ec575b01590540edd)的[模型融合方法](https://zhuanlan.zhihu.com/p/25836678)，以及[如何在 Kaggle 首战中进入前 10%](https://dnc1994.com/2016/04/rank-10-percent-in-first-kaggle-competition/)这篇文章（作者是章凌豪）。对于两位提供的信息，感激不尽。同时还有 Kaggle 上一些关于 ensemble 的文章和代码，比如[这篇](http://link.zhihu.com/?target=https%3A//www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python)（https://www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python）。

本文适用于被 stacking 折磨的死去活来的新手，在网上为数不多的 stacking 内容里，我已经假设你早已经看过了上述所提到的那几篇有用的文章了。但是，看完之后内心还是卧槽的。我希望下面的内容能成为，你在学习stacking的曲折道路上的一个小火把，给你提供一些微弱的光亮。

本文以Kaggle的Titanic（[泰坦尼克预测](https://www.kaggle.com/c/titanic)）入门比赛来讲解stacking的应用（两层！）。

数据的行数：train.csv有890行，也就是890个人，test.csv有418行（418个人）。

而数据的列数就看你保留了多少个feature了，因人而异。我自己的train保留了 7+1（1是预测列）。

在网上为数不多的stacking内容里，相信你早看过了这张图：![Kaggle机器学习之模型融合（stacking）心得](https://static.leiphone.com/uploads/new/article/pic/201709/71979ed976c68dcc834bfbe35ae73d79.jpg)

这张图，如果你能一下子就能看懂，那就OK。

如果一下子看不懂，就麻烦了，在接下来的一段时间内，你就会卧槽卧槽地持续懵逼......

因为这张图极具‘误导性’。（注意！我没说这图是错的，尽管它就是错的！！！但是在网上为数不多教学里有张无码图就不错啦，感恩吧，我这个小弱鸡）。

我把图改了一下：

![Kaggle机器学习之模型融合（stacking）心得](https://static.leiphone.com/uploads/new/article/pic/201709/cfb37e4250561177520f6f7af86d42f4.jpg)对于每一轮的 5-fold，Model 1都要做满5次的训练和预测。

Titanic 栗子：

Train Data有890行。(请对应图中的上层部分）

每1次的fold，都会生成 713行 小train， 178行 小test。我们用Model 1来训练 713行的小train，然后预测 178行 小test。预测的结果是长度为 178 的预测值。

这样的动作走5次！ 长度为178 的预测值 X 5 = 890 预测值，刚好和Train data长度吻合。这个890预测值是Model 1产生的，我们先存着，因为，一会让它将是第二层模型的训练来源。

重点：这一步产生的预测值我们可以转成 890 X 1 （890 行，1列），记作 P1 (大写P)

接着说 Test Data 有 418 行。(请对应图中的下层部分，对对对，绿绿的那些框框）

每1次的fold，713行 小train训练出来的Model 1要去预测我们全部的Test Data（全部！因为Test Data没有加入5-fold，所以每次都是全部！）。此时，Model 1的预测结果是长度为418的预测值。

这样的动作走5次！我们可以得到一个 5 X 418 的预测值矩阵。然后我们根据行来就平均值，最后得到一个 1 X 418 的平均预测值。

重点：这一步产生的预测值我们可以转成 418 X 1 （418行，1列），记作 p1 (小写p)

走到这里，你的第一层的Model 1完成了它的使命。

第一层还会有其他Model的，比如Model 2，同样的走一遍， 我们有可以得到  890 X 1  (P2) 和  418 X 1 (p2) 列预测值。

这样吧，假设你第一层有3个模型，这样你就会得到：

来自5-fold的预测值矩阵 890 X 3，（P1，P2， P3）  和 来自Test Data预测值矩阵 418 X 3， （p1, p2, p3）。

\-----------------------------------------

到第二层了..................

来自5-fold的预测值矩阵 890 X 3 作为你的Train Data，训练第二层的模型
来自Test Data预测值矩阵 418 X 3 就是你的Test Data，用训练好的模型来预测他们吧。

\---------------------------------------

最后 ，放出一张Python的Code，在网上为数不多的stacking内容里， 这个几行的code你也早就看过了吧，我之前一直卡在这里，现在加上一点点注解，希望对你有帮助：

![Kaggle机器学习之模型融合（stacking）心得](https://static.leiphone.com/uploads/new/article/pic/201709/d9b737ad6edbc00ffa15aa082fa4bb19.png)

雷锋网版权文章，未经授权禁止转载。详情见[转载须知](http://dwz.cn/4ErMxZ)。



# REF

- [Kaggle机器学习之模型融合（stacking）心得](https://www.leiphone.com/news/201709/zYIOJqMzR0mJARzj.html)作者[吉他手](https://www.zhihu.com/people/zhu-a-zhu-83/activities)
- [思颖](https://www.leiphone.com/author/siying985)
