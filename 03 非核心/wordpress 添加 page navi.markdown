

# 缘由：


我比较喜欢wordpress的twentytwelve的主题模板，但是这个模板在博文很多的时候没有办法直接跳转到之前的博文的页面，即，没有这个：


![](http://106.15.37.116/wp-content/uploads/2018/03/img_5abf167483cf6.png)


因此想添加这样的，方便跳转。


# 要点：




## 1.插件的寻找和安装


Page navi slider


## 2.插件的设置


这个插件设置比较简单，但是要想正常使用插件，需要修改一些文件。


## 3.相关文件的修改




### 1.准备工作，到底要怎么修改相关的文件？


这个插件不仅是要设置，而且还需要修改模板的php文件，而如果在原始的twentytwelve模板上修改，感觉不是很合适（如果直接在原始的twentytwelve模板上修改，可以直接通过外观-编辑打开想要修改的php文件，这里不推荐这种做法），而一般来说，凡是涉及模板的都可以从标准模板上继承一个可以自定义的模板，因此找了下，wordpress也是有这个的，因此准备先从twentytwelve模板上继承一个可以自定义的子模版twentytwelve_child。


### 2.根据主题模板生成子主题模板


登陆到服务器上，查找wordpress对应的文件夹：


    find / -name wp-content -print


输出：


    /data/wwwroot/default/wp-content


然后进入里面的theme文件夹。并创建一个twentytwelve_child文件夹：


    cd /data/wwwroot/default/wp-content
    ls
    cd themes
    mkdir twentytwelve_child


然后开始创建一个style.css文件：


    cd twentytwelve_child
    vim style.css


并将如下内容复制并保存到style.css文件中：（css是继承而不是覆盖的，因此调用到这里的时候会调用到twentytwelve的css里面）


    /*
    Theme: Twenty Twelve Child
    Description: Child Theme for twenty twelve theme to add page navi
    Author: evo_li
    Template: twentytwelve
    */

    @import url("../twentytwelve/style.css");


这个时候twentytwelve_child就已经从twentytwelve继承style了。也就是说，这个时候twentytwelve_child就已经是一个可用的主题了，而且跟twentytwelve一样。


### 3.拷贝并修改php文件


下面就是将 page navi 的调用替换原来的navi：

首先将一些php文件从twentytwelve文件夹中拷贝到twentytwelve_child中：


    cd /data/wwwroot/default/wp-content/themes/
    cp twentytwelve/archive.php twentytwelve_child/archive.php
    cp twentytwelve/author.php twentytwelve_child/author.php
    cp twentytwelve/category.php twentytwelve_child/category.php
    cp twentytwelve/index.php twentytwelve_child/index.php
    cp twentytwelve/search.php twentytwelve_child/search.php
    cp twentytwelve/tag.php twentytwelve_child/tag.php


**注意：不同的主题需要修改的文件是不同的，关键是要找到有类似：content_nav 这句话的php文件。**

拷贝过来之后，将这些文件中的：




  * _<?php twentytwelve_content_nav( 'nav-below' ); ?>_


  * _<?php twentytwelve_content_nav( 'nav-above' ); ?>_


替换为：


  * _<?php if(function_exists('page_navi_slider')){page_navi_slider();}?>_


**注意：不同的主题需要替换的也不同，因此最好参考：[Page nav slider](https://wordpress.org/plugins/page-navi-slider/) 的FAQ的第一个问题。**

这样，文件的修改就已经结束了。


## 4.在博客的控制台中更换主题


这个时候，就可以在博客的控制面板的主题里面找到刚刚的twentytwelve_child这个主题，然后使用即可。




# COMMENT：





# REF：






  1. [wordpress 子主题](https://codex.wordpress.org/zh-cn:%E5%AD%90%E4%B8%BB%E9%A2%98)


  2. [Page navi slider](https://wordpress.org/plugins/page-navi-slider/#installation)
