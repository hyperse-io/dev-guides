# ECS enginx setup guides

## Linux tengine/nginx

### install tengine

...

### the configuration of tengine

/opt/tengine/conf/nginx.conf

```

# user  op;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#error_log  "pipe:rollback logs/error_log interval=1d baknum=7 maxsize=2G";
error_log  logs/error.log warn;

pid        /opt/tengine/logs/nginx.pid;

worker_rlimit_nofile 51200;

events {
    multi_accept on;
    use epoll;
    worker_connections  51200;
}


http {
    server_tokens off;
    add_header X-Frame-Options SAMEORIGIN;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    more_clear_headers 'Server';
    include       mime.types;
    default_type  application/octet-stream;

    sendfile    on;
    send_timeout  30;
    # fastcgi_buffers 8 128k;
    keepalive_timeout  30;

    # server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 8m;
    client_body_buffer_size 512k;

    # proxy_connect_timeout 5;
    # proxy_read_timeout 30;
    # proxy_send_timeout 5;
    # proxy_buffer_size 16k;
    # proxy_buffers 4 64k;
    # proxy_busy_buffers_size 128k;
    # proxy_temp_file_write_size 128k;

    gzip  on;
    gzip_proxied any;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 6;
    gzip_types text/plain application/json application/javascript application/x-javascript text/css application/xml text/javascript;
    gzip_disable "MSIE [1-6]\.";
    gzip_vary on;
    charset utf-8;
    charset_types *;
    server {
      listen       80;
      server_name  localhost;
      location / {
        root   /opt/applications;
        index  index.html index.htm;
      }
    }
   include sites/xxx.domain.com;
   include sites/xxx.domain2.com;
   include sites/xxx.domain3.com;
}
```

view nginx service status

```shell
systemctl status nginx

```

nginx status show as below

```text

● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2024-06-09 09:20:20 CST; 1 months 12 days ago
 Main PID: 2551738 (nginx)
    Tasks: 2 (limit: 100494)
   Memory: 31.7M
   CGroup: /system.slice/nginx.service
           ├─2551738 nginx: master process /opt/tengine/sbin/nginx -c /opt/tengine/conf/nginx.conf
           └─2551739 nginx: worker process


```

restart nginx service

```shell
  systemctl restart nginx.service
```

view all started services

```shell
systemctl list-units --type=service
```
