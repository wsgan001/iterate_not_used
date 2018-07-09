# TODO

* 只是零碎的配置，需要系统的整理







```nginx
server {
        listen 80;
        server_name iterate.site ;
        location /{
                proxy_pass http://127.0.0.1:8000;
        }
}
```

nohup gunicorn blogproject.wsgi:application -b 127.0.0.1:8000 &
gunicorn blogproject.wsgi:application -b 127.0.0.1:8000
gunicorn blogproject.wsgi:application
gunicorn --bind unix:/tmp/iterate.site.socket blogproject.wsgi:application
gunicorn wsgi:app -c deploy/gunicorn.conf.py



```nginx
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





```nginx
server{
        charset utf-8;
        listen 80;
        server_name iterate.site;
        location /static {
                alias /home/evo/sites/iterate.site/blogproject/static;
        }
        location / {
                proxy_set_header Host $host;
                proxy_pass http://unix:tmp/iterate.site.socket;
        }
}
```

gunicorn --bind unix:/tmp/iterate.site.socket blogproject.wsgi:application

