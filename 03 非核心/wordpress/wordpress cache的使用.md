
# 缘由：

发现网站的加载有点慢，因此想用个cache 的插件。


# 要点：




## 1.使用的插件及设置：


选用的cache的插件：WP Fasted Cache

设置如下：


![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180727/Jm11BDLbkL.png?imageslim)

**注意：submit之后其实是要过大概10分钟才会起作用**

这个插件的设置方法：




  1. [How to use WP Fastest Cache for WordPress Optimization](https://www.youtube.com/watch?v=oJyjFHc34RM)




## 2.测试的方法：


下面两个网站都可以测试：




  1. [https://gtmetrix.com/](https://gtmetrix.com/)


  2. [https://tools.pingdom.com/](https://tools.pingdom.com/)（这个要先登陆，但是能更好的对比出加载的缺陷）


推荐使用 pingdom


## 3.我在pingdom测试了下：


加载主页面的时候：




  * 设置cache之前是2.10s  Test From USA


  * 设置cache后变成1.5s Test From USA


还是不错的。

但是还是提示说：Remove query strings from static resources 这个是红的，即没有优化

因此又安装了一个插件：**Remove Query Strings ** 这个插件直接安装即可，不用设置。

插件装好后再测，虽然没有这个提示了，但是速度并没有更加快。。


# COMMENT：


**不知道还有没有更好的方法。**
