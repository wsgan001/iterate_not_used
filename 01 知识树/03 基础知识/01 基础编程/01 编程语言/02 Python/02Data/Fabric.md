
# TODO
- 不知道还有没有更厉害的。<font color=red>确认下</font>
- 详细的总结下，还有结合别的东西的使用情况


# 缘由
初次看到 Fabric 的功能的时候还是很震惊的，竟然有这么好用的东西，这样部署太方便了，
现在已经有 Fabric3 了


```python
from fabric.api import env,run
from fabric.operations import sudo
# 这个是对fabfile.py 的备份。
GIT_REPO='https://github.com/evo-li/blogproject.git'
env.user='xxxxx'# 填写自己的用户名
env.password='xxxxx'# 填写自己的密码

# 填写自己的主机对应的域名
env.hosts=['iterate.site']
# 一般情况下为 22 端口，如果非 22 端口请查看你的主机服务提供商提供的信息
env.port = '22'


def deploy():
    source_folder = '/home/evo/sites/iterate.site/blogproject'

    run('cd %s && git pull' % source_folder)
    run("""
        cd {} &&
        ../_env3.6/bin/pip install -r requirements.txt &&
        ../_env3.6/bin/python3 manage.py collectstatic --noinput &&
        ../_env3.6/bin/python3 manage.py migrate
        """.format(source_folder))
    run("""
    cd {} &&
    ../_env3.6/bin/gunicorn blogproject.wsgi:application -c deployfiles/gunicorn.conf.py
    """.format(source_folder))
    sudo('systemctl restart nginx')
```



# REF
1. [使用nginx,gunicorn,fabric设置网站自动部署](https://www.jianshu.com/p/bc1111e7e6d0)
2. [PYTHON FABRIC实现远程操作和部署](http://wklken.me/posts/2013/03/25/python-tool-fabric.html#zhi-xing-ben-ji-cao-zuo)
3. 官网：[http://www.fabfile.org/](http://www.fabfile.org/)
4. 文档：[http://docs.fabfile.org/](http://docs.fabfile.org/)
