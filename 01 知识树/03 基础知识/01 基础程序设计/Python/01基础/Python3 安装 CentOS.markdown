TODO
- 怎么说是：Successfully installed pip-9.0.1 setuptools-28.8.0  ？setuptools 不是应该是从 1.几 开始的吗？ 既然说已经成功安装了，那么后面的 setuptools 的1.几版本的还要不要装？



# 在安装 python 之前需要知道的


如果你是想做科学计算，要用 numpy 、pandas 等等，那么 最好直接在 Linux 上安装 Anaconda ，它包括了Python及其关于科学计算的很多包，管理也更方便。

如果是想做web，比如只是用来做爬虫、或者django，那么按照本篇来安装 python 是可以的。




# 首先，不要碰系统自带的 python




## 系统自带的 python


首先，不要碰系统 default 的 python，因为这个是 YUM 要用到的，对于这个 YUM，一定要小心，不要改动它或它依赖的一些东西。不要认为系统自带的一些库是给你用的，需要养成建立你自己需要用到的库的习惯。

而且，CentOS 为了稳定性，使用的 python 版本都是比较旧的。


## 我们准备安装的东西


我们准备安装 python 3.6.0 其它的 python 版本的安装也都是类似的。（同一个电脑上是可以同时安装 python2 和 python 3 的）

而且，我们会安装 pip 和 vitualenv， 这个都是开发所必须的。

当我们安装完成后，就可以在电脑上使用你所安装的 python 版本了，并且可以为每个版本下载和管理Python包。创建和使用virtual environments。 **这几点做到了吗？没有吧？要怎么做到？**


## 可以安装的 python 版本
可以通过查看发布页面来查看 [所有可用的Python 版本](https://www.python.org/ftp/python/)。这些都是可以安装的。
# 准备好系统
## 更新系统默认程序
在我们开始安装之前，让我们确保更新默认系统应用程序以获得最新版本。

  1. yum -y update




## 获取一些开发工具


CentOS发行版本是比较精简的，这意味着它们不包含许多你可能需要的常用应用程序和工具。因此，这些库和工具也是需要安装的。如果不安装，后面的一些编译可能会无法执行。

有两种方法可以使用 yum 获取开发工具：




  1. 一个接一个下载这些工具（如make，gcc等）。（**不推荐**）


  2. 使用 yum 软件组通过单个命令下载一堆工具，软件组由大量常用工具（应用程序）捆绑在一起组成，可通过执行单个命令并指定组名称同时下载。


我们应该是肯定选择第二个方法的，我们下载这个开发软件组：


  * yum groupinstall -y development


注：如果这句在早期的CentOS上没法使用，可以使用： yum groupinstall -y 'development tools'


## **再安装一些附加软件包**


一些 python 大部分任务都会用到的东西，比如安装，不然python 编译的时候可能会出问题：




  * yum install -y zlib-dev openssl-devel sqlite-devel bzip2-devel


readline：如果这个不安装，那么在进入 python 的shell 里面之后，回车，上下左右 都没有办法正常使用，会打出，比如：^H^[[D 这样的东西。安装之后即可。


  * yum install readline-devel.x86_64




## 还有一些要安装的东西


这些东西基本上系统是自带的，但是也没准，因此也都安装一下。

XZ 工具：该文件使用 XZ 库进行压缩。可以运行以下命令安装 XZ 库：




  * yum install xz-libs









# 进行 Python 的安装


我们会通过 源代码来安装 Python。


## 关于版本需要知道的


我们这里使用的是 “3.6.0” ，但是实际上，你可以安装任何一个版本，比如 “3.3.3”，只要替换我安装过程中的一些版本相关的指令即可。

但是你安装完成后，想要运行的时候，比如要执行它们的版本，比如，我安装的是  “3.6.0” ，那么我想要运行某个程序的时候，就是使用 python3.6 ，而不是 python。


## 将会使用到的几个工具介绍


接下来的操作中，我们会用到几个工具：




  * 下载压缩的源代码包（wget），


  * 从这个包中提取文件（tar），


  * 配置和构建应用程序（autoconf（configure）/ make）。


简单介绍一下：


  * wget 是一个用于通过各种协议（如HTTP，FTP）下载文件的应用程序。它现在是默认被安装在系统里的，老的系统里面可能需要自己装这个 wget。


    * wget的示例用法：wget [URL] 。





  * tar 基本上是一个文件档案创建和操作工具。使用各种可用选项，可以创建压缩包并在稍后提取它们。


    * tar 使用示例：tar [options] [arguments]





  * autoconf 和 make 是两种不同的工具，（大部分）一起用于配置源代码。


    * 用法：一般，我们会：在安装之前使用 ./configure来配置所有内容。然后使用 make 来连接库和源，比如说用make install 来构建源代码，make altinstall 来生成二进制文件，并按照配置使用我们的系统在我们的系统上安装应用程序 ./configure。**没明白？**


    * [autoconf 手册](http://www.gnu.org/software/autoconf/manual/autoconf.html#Introduction)， [make 手册](http://www.gnu.org/software/make/manual/make.html) 。**要仔细掌握下。**





OK，正式开始：


## OK，正式开始，下载 Python


下载，构建（编译）和安装Python
在本节中，给出的所有说明可用于下载任何版本的Python。。

下载源文件

查看 [所有可用的Python 版本](https://www.python.org/ftp/python/) ，然后选定一个版本，把地址写道下面的指令中进行下载，我这里使用的是 3.6.0 ：




  * wget http://www.python.org/ftp/python/3.6.0/Python-3.6.0.tar.xz




## 提取压缩的源文件


下载完之后，使用 ls 查看一下当前文件夹下面的东西。然后：

阶码 XZ 文件：decode (-d) the XZ encoded tar archive:




  * xz -d Python-3.6.0.tar.xz


然后，解压 tar 文件：


  * tar -xvf Python-3.6.0.tar


OK，这时候使用 ls 就可以看到已经解压出了一个文件夹。


## 配置


在构建源代码之前，我们需要确保所有的依赖关系都在那里并准备好环境。这是通过使用 ./configure来为我们处理任务而自动实现的。

进入刚刚解压的文件夹：




  * cd Python-3.6.0


开始配置：设置安装路径：默认是安装到 /usr/local 里面的，也可以修改。


  * ./configure --prefix=/usr/local


OK，到这里，应该不会有什么问题， 因为我们已经下载了所有必要的工具和应用程序。

完成后，我们将继续下一步：构建和安装。


## 构建和安装


配置完后，我们就可以继续构建（编译）源代码并安装应用程序。

注意：通常，会使用 “make install”，但是，为了不覆盖系统已经使用的 Python，我们将使用make altinstall 。 这个是要注意的，因为，如果覆盖了系统的，那么我**暂时不知道怎么办。**

构建（编译）源文件：可能会需要一段时间：




  * make


在构建好之后，安装：


  * make altinstall


注：我现在使用的是 CentOS 7.5 ，在执行完上面的 make altinstall 之后，已经自动把 pip 和 setuptools 安装好了。

**有个疑问：这个时候怎么说是：Successfully installed pip-9.0.1 setuptools-28.8.0  ？setuptools 不是应该是从 1.几 开始的吗？ 既然说已经成功安装了，那么后面的 setuptools 的1.几版本的还要不要装？**


## [可选步骤] 将新的 Python 安装位置添加到 PATH


如果上面不是安装到 /usr/local，而是安装到别的地方，那么需要把位置添加到Path，如果使用默认的  /usr/local 就不用执行这一小节的东西。

如果不把路径存放在PATH 变量中，那么就只能通过制定完整的位置（路径） （例如/usr/local/bin/python3.6）来访问生成的二进制文件（即我们选择的版本的Python解释器）

因此还是添加到PATH 变量更为方便：比如我们安装到/usr/local/bin 下面，那么我们可以这么添加：（要了解有关PATH的更多信息，请考虑阅读Linux信息项目中的 [PATH定义](http://www.linfo.org/path_env_var.html)）




  * export PATH="/usr/local/bin:$PATH"





# 设置通用 Python 工具 pip 和 virtualenv


在安装了Python之后，如果没有自动安装好 pip 和 setuptools，那么这两个也是要进行安装的：

关于这两种工具的更多信息，请阅读：[常见Python工具：使用virtualenv，使用Pip安装和管理软件包](https://www.digitalocean.com/community/articles/common-python-tools-using-virtualenv-installing-with-pip-and-managing-packages)。


## 安装 pip 之前，我们需要先安装它的依赖 setuptools


setuptools 建立在Python的分发实用程序工具集（称为distutils）的（标准）功能上。鉴于 distutils 是默认提供的，因此我们所需要的只是setuptools。




  * wget --no-check-certificate https://pypi.python.org/packages/source/s/setuptools/setuptools-1.4.2.tar.gz


  * tar -xvf setuptools-1.4.2.tar.gz


  * cd setuptools-1.4.2


  * python3.6 setup.py install


注意：在执行这些语句之前，先到我们之前进入的 Python-3.6.0 外面。


## 安装 pip


之后安装pip本身是一个非常简单的过程。我们将利用上述文章中的说明，我们使用 cURL 库自动安全地下载和安装它。

注意：要了解关于cURL的更多信息，可以参考 [此处](https://www.digitalocean.com/community/articles/common-python-tools-using-virtualenv-installing-with-pip-and-managing-packages) 的解释部分。

让我们下载pip的安装文件并让Python（3.6）安装它：




  * curl https://raw.githubusercontent.com/pypa/pip/master/contrib/get-pip.py | python3.6 -


要了解如何使用pip，请参阅常见Python工具文章：使用 virtualenv，使用 Pip 安装和管理软件包。

现在我们已经准备好了软件包管理器，在系统上获得 virtualenv 是一件轻而易举的事情。


## 安装 virtualenv
  * pip install virtualenv
要了解如何使用virtualenv，请参考关于通用Python工具的文章：使用virtualenv，使用Pip安装和管理软件包。
OK，到这里，python 和 pip 、virtualenv 都已经安装好了。





# REF
1. [How To Set Up Python 2.7.6 and 3.3.3 on CentOS 6.4](https://www.digitalocean.com/community/tutorials/how-to-set-up-python-2-7-6-and-3-3-3-on-centos-6-4)
2. [自定义安装python，退格，方向键无法正常使用（已解决）](http://bbs.chinaunix.net/thread-2322983-1-1.html)
