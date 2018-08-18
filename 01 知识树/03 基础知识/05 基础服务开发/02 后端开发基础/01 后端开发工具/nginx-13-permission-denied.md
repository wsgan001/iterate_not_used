# nginx 13: Permission denied


TODO

* **一定要以官方手册为准，即使是英文的，即使太系统，也一定要看。**网上的很多资料都各说各的，很难找到确切对应自己的情况的，比如经常看到的都是 Ubuntu 的， CentOS的比较少，但是 nginx 在ubuntu和CentOS上好像是不同的，因此就比较麻烦。而且，各种写法千奇百怪。
* **系统的掌握知识还是非常有必要的，比如我如果系统的掌握了网络相关的知识，那么判断起问题的原因就会又很多思路和方法，如果我系统的掌握了如何 debug，那么我会第一时间想到打 log 来看。**
* **英文必须掌握，比较系统的资料经常是英文的，比如 stackoverflow**




## 运行的环境

* CentOS





# 缘由


我把基于django的网站放到网上的时候，把 settings.py 里面的 DEBUG 设为 False，这样，django 就不会自动处理 static 文件了，这时候就必须自己手动处理，

我使用了 nginx 来处理文件，这时候有两个地方需要改：

settings.py 里面，添加了：


    # 指明了静态文件的收集目录，即项目根目录下面的static 文件夹
    STATIC_ROOT =( os.path.join(BASE_DIR, 'static'))


然后在 manage.py 同级文件夹里运行：




  * python manage.py collectstatic


这样，我的 应用的 static 文件都拷贝到 manage.py 所在的文件夹的 static 文件夹里面了。

然后，我需要配置 nginx：

修改 nginx.conf：


  * cd /etc/nginx


  * vim nginx.conf


把 server 里面改成：


    server {
        listen         80;
        server_name    127.0.0.1
        charset UTF-8;
        access_log      /var/log/nginx/django_pro01_access.log;
        error_log       /var/log/nginx/django_pro01_error.log;

        client_max_body_size 75M;

        location /{
            proxy_pass http://127.0.0.1:8000;
        }
        location /static {
            expires 30d;
            autoindex on;
            add_header Cache-Control private;
            alias /home/evo/sites/iterate.site/blogproject/static;
         }
     }


按理说，这样就已经OK了， 我只需要 ：




  * systemctl restart nginx


然后就可以  python  manage.py runserver 了。

但是，这时候，发现，页面内容是能展示出来的，但是 css、js 好像都没有加载过来。

嗯，感觉：


  * 可能是加载过来了，但是网页引用的地址错了，


  * 可能是没有加载过来，服务器没有提供过来


第一个问题，确认了下，网页的原码对应的 href 是正确的，而且看了下 模板 html 中对于 static 的使用，也应该是对的，而且，确认了下 STATIC_ROOT 的写法，也应该是对的。嗯，第一个问题的情况应该是OK的。

那么就是第二个问题：

之前不知道打 nginx 的log，总是以为是 nginx.conf 和 STATIC_ROOT 的配置可能不配套或者语句可能错误，查了几遍，发现是OK的。

后来查找的的时候发现可以打  nginx 的 log，OK，打了之后真正问题才出现：


  * 2018/06/04 12:52:26 [error] 29426#0: *1 open() "/xxxxxx/xxx/xxx/xxxx/xxxxx/static/blog/js/pace.min.js" failed (13: Permission denied), client: xx.xx.xx.xx, server: 127.0.0.1, request: "GET /static/blog/js/pace.min.js HTTP/1.1", host: "iterate.site.", referrer: "http://iterate.site./"


发现，问题实际上是 13:Permission denied ，那么这个问题怎么解决呢？

刚开始是想，这个肯定是权限问题，那么我nginx的运行的时候，我是 用的 root，按理说应该没有问题呀，不过，我还是chmod 777 了一下 static文件夹，还是不行。

又查找了网上的一些资料，没想到最后是在百度经验里看到的解法：


  * vim nginx.conf


  * 修改user nginx  为当前系统用户，如：user root


这个的确是可以的，我也才知道，nginx  运行的时候的权限竟然是在nginx.conf 里面设定的。**确认下。**








## REF

1. [nginx报错:failed (13: Permission denied)](https://jingyan.baidu.com/article/64d05a023f8d46de55f73b37.html)
2. [解决Nginx出现403 forbidden (13: Permission denied)报错的三种办法](http://www.shuchengxian.com/article/658.html)
3. [chmod 777 修改权限](https://www.cnblogs.com/sipher/articles/2429772.html)
4. [nginx open() "" failed (13: Permission denied), client:错误解决办法](https://blog.csdn.net/watsy/article/details/10010009)
