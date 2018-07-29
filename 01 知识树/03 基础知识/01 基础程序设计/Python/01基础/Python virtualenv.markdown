---
author: evo
comments: true
date: 2018-05-03 10:47:27+00:00
layout: post
link: http://106.15.37.116/2018/05/03/python-virtualenv/
slug: python-virtualenv
title:
wordpress_id: 5025
categories:
- 基础程序设计
tags:
- python
---

<!-- more -->

[mathjax]

**注：非原创，所有版权属于原作者，原文已列在 ORIGINAL 中。为了方便个人学习做了整合、修改，仅供个人学习使用。**


# ORIGINAL






  * [python基础教程 w3cschool](https://www.w3cschool.cn/python/)

  * [Python 3 教程 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)

  * [windows下使用pycharm配置python的virtualenv环境](https://blog.csdn.net/hy245120020/article/details/50776197)

  * [PyCharm中使用virtualenv](https://segmentfault.com/a/1190000003758895)




# TODO






  * **要不要使用 virtualenvwrapper**

  * **在 windows 和 linux 上分别是怎么用的？**

  * **virtualenv 与 docker 有相似之处吗？**





* * *





# INTRODUCTION






  *






# pip 和 virtualenv 到底有什么不同？


这两者是不同的东西，pip 是用来管理 python的包，virtualenv 是用来建立一个虚拟环境，这个虚拟环境有别于python 所在的系统环境，virtualenv 建立这个虚拟环境之后，这个虚拟环境里面就有了它自己的python 、pip ，你如果在这个虚拟环境里安装什么包，就只会安装到 这个虚拟环境的 python 的site-packages 里面，而不是系统的python的packages里面。


# 需要注意的


virtualenv 创建的虚拟环境与主机的 Python 环境完全无关，
你主机配置的库不能在 virtualenv 中直接使用。
你需要在虚拟环境中利用 pip install 再次安装配置后才能使用。


# virtualenv 的使用




## 在 windows 中使用


打开 cmd，


安装 virtualenv：







  * pip install virtualenv




建立 virtualenv：







  * mkdir testenv

  * cd testenv  #进入一个希望创建虚拟 python 环境的文件夹下面

  * virtualenv venv #在这个文件夹里面创建了一个 venv 文件夹，里面就是建好的python 虚拟环境


    * virtualenv venv --python=python3.6  **注意：如果安装了两个 python 版本，其中一个是3.6 ，现在想用3.6 创建虚拟环境，则需要这么写，式子中不是单引号，是波浪号按键对应的符号。而且是两个横杠，不是一个。**





  *



查看当前目录下生成了 venv 目录：







  * dir

  * tree




开启关闭 virtualenv：







  * 进入venv目录

  * Scripts\activate    #激活

  * deactivate     #关闭




## 在 Linux 中使用


同样的 ：




  * pip install virtualenv

  * mkdir testenv

  * cd testenv

  * virtualenv venv --python=python3.6

  * source newenv/bin/activate  # 好像这个地方不一样，确认下

  * deactivate





# virtualenv 在 pycharm 中是怎么使用的？




## 配置环境






  1. 打开PyCharm，在 Welcome to PyCharm 界面点击 Configure --> Preferences

  2. 在弹出的窗口搜索，project、interpreter等关键字均可，然后找到 Project Interpreter

  3. 点击该界面的最右侧的一个齿轮形状的按钮，在下拉列表中有个 "Create VirtualEnv" 选项

  4. 在弹出的对话框中输入要配置的环境信息：


        1. Name中输入名称，如 flask


            2. Location选择：选择配置好的 virtualenv 的默认目录，如 /Users/oper/.virtualenvs/flask


            3. Base interpreter：默认就好(我的是：/System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7）







## 在环境中创建项目


例如创建flask项目：




    1. 选择flask项目

    2. Location选择 /User/oper/.virtualenvs/flask/<项目名>    即选择了VirtualEnv环境的Location

  3. Interpreter选择刚刚创建的 VirutalEnv (名称为flask的那个)







# 使用 virtualenvwrapper **要不要使用？**


virtualenv 是一个创建 Python 独立环境的包，virtualenvwrapper 使得virtualenv变得更好用


    # 安装:
    (sudo) pip install virtualenv virtualenvwrapper

    # 修改.bash_profile 或 .zshrc（如果你用 zsh 的话），添加以下语句
    export WORKON_HOME=$HOME/.virtualenvs
    export PROJECT_HOME=$HOME/workspace
    source /usr/local/bin/virtualenvwrapper.sh






  * mkvirtualenv ENV：创建运行环境ENV

  * rmvirtualenv ENV：删除运行环境ENV

  * mkproject mic：创建mic项目和运行环境mic

  * mktmpenv：创建临时运行环境

  * workon bsp: 工作在bsp运行环境

  * lsvirtualenv: 列出可用的运行环境

  * lssitepackages: 列出当前环境安装了的包


创建的环境是独立的，互不干扰，无需sudo权限即可使用 pip 来进行包的管理。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180728/ALFB73ef8d.png?imageslim)






















* * *





# COMMENT
