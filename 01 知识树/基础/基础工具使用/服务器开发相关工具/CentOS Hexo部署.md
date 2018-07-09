

# AIMS

在 CentOS 上使用 Hexo 搭建 wiki，最好能使用 Travis 实现自动化部署

注：不是通过Github，也没有使用 Githubpages。

# 大概准备怎么做

首先，在服务器上放这么两个地方：

* git 的地方，用来放网站的还未构建的文件
* deploy 的地方，网站的文件deploy的位置是在这里，同时nginx 的root也设置到这里。

大概步骤是这样的：

* 我在自己的电脑上用 hexo 生成一套东西，然后 push 到 server 的library里面，然后，我用hooks 启动一个脚本，来把这个更新的library deploy 到 我真正的html 文件的地方，然后就可以直接访问了。
* 后面，我如果更换了电脑，直接从 server 上clone 下来，然后修改以下 md 文章，然后 push 上去就行，自己的电脑就不需要再装 hexo 了。

涉及到的几个地址：

* /data/GitLibrary 里面放了一个 hexo.git 来存放原始的网站
* /data/www/hexo 用来存放真正的被deploy 的网站，nginx 的root 也是这个

OK，尝试一下





# 1.安装必要的环境及配置 

创建文件夹和一个临时测试网页

```
mkdir -p /data/www/hexo
mkdir -p /data/GitLibrary
vim /data/www/hexo/index.html
```

```
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <meta charset="UTF-8">
  </head>
  <body>
    <p>Nginx running</p>
  </body>
</html>
```



安装并配置 nginx

```
yum -y update
yum install -y nginx
vim /etc/nginx/nginx.conf
```

```
......
server {
      listen       80 default_server;
      listen       [::]:80 default_server;
      server_name  www.xxx.com; # 填写个人域名
      root         /data/www/hexo;
  }
......
```

尝试访问一下服务器的 IP 或者域名，网页上显示：Nginx running 说明到这里 配置OK。



git 的配置

```
cd /data/GitLibrary
git init --bare hexo.git
vim /data/GitLibrary/hexo.git/hooks/post-receive
```

```
#!/bin/bash
git --work-tree=/data/www/hexo --git-dir=/data/GitLibrary/hexo.git checkout -f
```

注意，上面的第二行是没有换行的。

保存并退出后, 给该文件添加可执行权限 

```
chmod +x /data/GitLibrary/hexo.git/hooks/post-receive
```



nodejs



git



hexo

1. 在执行 hexo deploy 后,出现 error deployer not found:git 的错误处理

输入代码： `npm install hexo-deployer-git --save` 







将本地部署到服务器

- 清除缓存

  ```
  hexo clean
  ```

- 生成静态页面:

  ```
  hexo generate
  ```

- 将本地静态页面目录部署到云服务器

  ```
  hexo deploy
  ```

# REF

* [Hexo 个人博客部署到 CentOS 个人服务器](https://segmentfault.com/a/1190000010680022) 是在本地的_config.yml 中把 deploy 地址设到 server 上的，也就是说转化的过程是发生在本地的。
* [Hexo 官网](https://hexo.io/zh-cn/)