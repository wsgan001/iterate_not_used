# TODO





注意：环境是 CentOS7 环境，不同的系统，配置和指令都有不同的。
OK，
假如说，我们现在 django、nginx 、gunicorn 都已经安装好了，
而且 django 的项目也已经在 virtualenv 里面都弄好了。
那么，现在看一下，这么一个项目怎么连接nginx、gunicorn的，主要解决下面几个问题：

* nginx 的作用：用来处理 static 文件，比django自己处理要快，而且，把对 ip 地址和 域名的访问，正确的转到 127.0.0.1:8000 这个地址上。这样我们在自己的电脑上，直接访问地址或者域名就能成功访问自己的网站了。
* gunicorn 作用：


安装完成后在项目的settings.py中的install_app加入gunicorn应用 这个为什么有的教程里面没有提到？





OK，总结一下 nginx+gunicorn +django 的配置的写法：

第一种，简单的：

nginx 部分：


  * cd /etc/nginx

  * vim nginx.conf


    * 把 user nginx; 改成： user evo;　如果不改，那么nginx没有权限读 static 文件，css 会无法加载

    * 把原来的 http 里面的server注释掉，换成：


    server {
        listen         80;
        server_name    iterate.site
        charset UTF-8;
        access_log      /var/log/nginx/iterate_site_access.log;
        error_log       /var/log/nginx/iterate_site_error.log;

        #client_max_body_size 75M;


        location /static {
            expires 30d;
            autoindex on;
            add_header Cache-Control private;
            alias /home/evo/sites/iterate.site/blogproject/static;
         }
         location /{
            proxy_pass http://127.0.0.1:8000;
         }
     }








django 部分：
  * settings.py 添加 STATIC_ROOT = os.path.join(BASE_DIR, 'static')
  * python manage.py collectstatic
gunicorn 部分：
  * 把 gunicorn 加入到 settings.py 的 INSTALLED_APPS 里面
运行：


  * 使用  gunicorn blogproject.wsgi:application 而不是 python manage.py runserver


第二种方法：

在第一种的基础上：

nginx 部分：


  * cd /etc/nginx
  * mkdir sites-available
  * cd sites-available
  * vim iterate.site


    * 把下面的代码复制进去：

```conf
server {
    listen         80;
    server_name    iterate.site
    charset UTF-8;
    access_log      /var/log/nginx/iterate_site_access.log;
    error_log       /var/log/nginx/iterate_site_error.log;

    #client_max_body_size 75M;


    location /static {
        expires 30d;
        autoindex on;
        add_header Cache-Control private;
        alias /home/evo/sites/iterate.site/blogproject/static;
     }
     location /{
        proxy_set_header Host $host;
        proxy_pass http://unix:tmp/iterate.site.socket;
     }
 }
```








  * cd /etc/nginx

  * mkdir sites-enabled

  * ln -s /etc/nginx/sites-available/iterate.site /etc/nginx/sites-enabled/iterate.site

  * cd /etc/nginx

  * 把 nginx.conf 的 server 部分注释掉，换成一句：include /etc/nginx/sites-enabled/*; #注意分号

  * cd /lib/systemd/system

  * vim nginx.service


    * 把 PrivateTmp 改为 false，否则的话 nginx 看不到 gunicorn 创建的 tmp socket ，会有 502 Bad way。**P****rivateTmp=True 表示给服务分配独立的临时空间。确认下privateTemp 能不能改？ **





gunicorn 部分：


  * 使用 gunicorn --bind unix:/tmp/iterate.site.socket blogproject.wsgi:application  来运行




总结：


  * **遇到问题，首先看有没有 log，根据 log 来查**
  * 看看官网上搜一下，或者 stackoverflow 上 有没有解决的
  * **要系统的掌握知识，一知半解连执行都困难，别提有新点子了。**











# REF

1. [CentOS7部署Flask/Gunicorn/Nginx/Supervisor](http://www.madmalls.com/blog/post/deploy-flask-gunicorn-nginx-supervisor-on-centos7/)
2. [写给Web开发人员看的Nginx介绍](https://fraserxu.me/2013/06/22/Nginx-for-developers/)
3. [【Python】Centos7下部署Django（nginx+gunicorn）](https://blog.csdn.net/ns2250225/article/details/54952378)
4. [How to Deploy Python WSGI Apps Using Gunicorn HTTP Server Behind Nginx](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-apps-using-gunicorn-http-server-behind-nginx)
5. [nginx之proxy_pass指令完全拆解](https://my.oschina.net/foreverich/blog/1512304)
6. [Nginx实现虚拟路径代理](http://www.ywnds.com/?p=9613)
7. [502 Bad Gateway django+gunicorn+nginx configuration](https://stackoverflow.com/questions/34799276/502-bad-gateway-djangogunicornnginx-configuration?rq=1)
8. [Nginx cannot find unix socket file with Unicorn (no such file or directory)](https://stackoverflow.com/questions/22272943/nginx-cannot-find-unix-socket-file-with-unicorn-no-such-file-or-directory)
9. [Setting up Unix Socket with Nginx and Django](https://stackoverflow.com/questions/28856878/setting-up-unix-socket-with-nginx-and-django)
