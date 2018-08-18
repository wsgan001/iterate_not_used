# gunicorn 与 supervisor 的配置与使用

# ORIGINAL






  1. [配置手册 configure](http://docs.gunicorn.org/en/stable/configure.html) 官方配置手册，按手册来


  2. [gunicorn github](https://github.com/benoitc/gunicorn/blob/master/examples/supervisor.conf) 这个时gunicorn 的开源的github，上面有 example ，有 supervisor.conf


  3. [Where is the Gunicorn config file?](https://stackoverflow.com/questions/12063463/where-is-the-gunicorn-config-file)


  4. [nginx uwsgi django supervisord Centos部署](https://my.oschina.net/u/198124/blog/817335)


  5. [在 CentOS7 上用 MySQL+Nginx+Gunicorn+Supervisor 部署 Django](https://me.iblogc.com/2016/12/08/%E5%9C%A8centos7%E4%BD%BF%E7%94%A8mysql-nginx-gunicorn+supervisor%E9%83%A8%E7%BD%B2django/)




# TODO






  * aaa





* * *





# INTRODUCTION






  * aaa




基本上下载之后，settings.py 里面添加之后，

运行的时候 根据nginx 的配置都似乎可以的：


  * nohup gunicorn blogproject.wsgi:application -b 127.0.0.1:8000 &


  * gunicorn blogproject.wsgi:application -b 127.0.0.1:8000


  * gunicorn blogproject.wsgi:application


  * gunicorn --bind unix:/tmp/iterate.site.socket blogproject.wsgi:application


但是，如果像 自动重启服务，那么就需要用到supervisor来监督它了，当网站down掉的时候，supervisor会自动运行 上面这些语句，来重启网站。

具体配置暂时还没用到

**要补充下。**

gunicorn.conf.py :


    """gunicorn WSGI server configuration."""
    from multiprocessing import cpu_count
    import multiprocessing

    # bind = '127.0.0.1:8001'
    bind = 'unix:/tmp/iterate.site.socket'
    workers = multiprocessing.cpu_count() * 2 + 1
    # daemon = True
    pidfile = '/run/gunicorn.pid'
    loglevel = 'info'
    errorlog = '/home/evo/sites/iterate.site/blogproject/logfiles/gunicorn_error.log'
    accesslog = '/home/evo/sites/iterate.site/blogproject/logfiles/gunicorn_access.log'
    access_log_format = '%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"'









安装 supervisor：




  * yum install supervisor


编辑 /etc/supervisord.d/gunicorn.ini ：


    ; vi  /etc/supervisord.d/gunicorn.ini
    ; 这里面是对 supervisor 的配置

    [program:gunicorn]
    directory=/home/evo/sites/iterate.site/blogproject
    ; 程序的启动目录
    command=/home/evo/sites/iterate.site/_env3.6/bin/gunicorn blogproject.wsgi:application -c /home/evo/sites/iterate.site/blogproject/deployfiles/gunicorn.conf.py
    ; 启动命令，可以看出与手动在命令行启动的命令是一样的


    user = root
    ; 用哪个用户启动
    autostart = true
    ; 在 supervisord 启动的时候也自动启动
    startsecs = 5
    ; 启动 5 秒后没有异常退出，就当作已经正常启动了
    autorestart = true
    ; 程序异常退出后自动重启
    startretries = 3
    ; 启动失败自动重试次数，默认是 3
    redirect_stderr = true
    ; 把 stderr 重定向到 stdout，默认 false
    stdout_logfile_maxbytes = 20MB
    ; stdout 日志文件大小，默认 50MB
    stdout_logfile_backups = 20
    ; stdout 日志文件备份数
    stdout_logfile = /home/evo/sites/iterate.site/blogproject/logfiles/supervisor.log
    ; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）

    ; 可以通过 environment 来添加需要的环境变量，一种常见的用法是修改 PYTHONPATH
    ; environment=PYTHONPATH=$PYTHONPATH:/path/to/somewhere




**冒号后面要有空格**

启动
使用-c指定配置文件。

supervisord -c /etc/supervisord.conf



如果启动时遇到以下报错信息



Error: Another program is already listening on a port that one of our HTTP servers is configured to use. Shut this program down first before starting supervisord.
For help, use /use/bin/supervisord -h



可以使用以下命令解决

复制1

sudo unlink /var/run/supervisor/supervisor.sock



命令行客户端工具 supervisorctl
启动时需要使用和supervisorctl使用一样的配置文件。



supervisorctl -c /etc/supervisord.conf



启动后进入supervisorctl的 shell，在此 shell 里可以执行以下命令



status # 查看程序状态
start example # 启动example程序
stop example # 关闭example程序
restart example # 重启example程序
reread # 读取有更新（增加）的配置文件，不会启动新添加的程序
update # 重启配置文件修改过的程序



也可以不进 shell 执行以上命令



supervisorctl status # 查看程序状态
supervisorctl start example # 启动example程序
supervisorctl stop example # 关闭example程序
supervisorctl restart example # 重启example程序
supervisorctl reread # 读取有更新（增加）的配置文件，不会启动新添加的程序
supervisorctl update # 重启配置文件修改过的程序



开启 web 管理界面
如果要开启 web 管理界面，打开/etc/supervisord.conf把下面几行取消注释即可

复制1
2
3
4

:[inet_http_server] ; inet (TCP) server disabled by default
:port=127.0.0.1:9001 ; (ip_address:port specifier, *:port for all iface)
:username=user ; (default is no username (open server))
:password=123 ; (default is no password (open server))

本文作者： 沙丁鱼













* * *





# COMMENT
