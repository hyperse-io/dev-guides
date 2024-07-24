# domain setup with `/webapps` static page proxy without cache

```shell
upstream www_domain_node_oss {
  server   127.0.0.1:3001;
}
upstream www_domain_node_mp {
  server   127.0.0.1:3002;
}

server {
  listen               80;
  access_log off;
  server_name          wwww.domain.com domain.com;
  return 301           https://domain.com$request_uri;
}
server {
  if ($host ~ ^www\.) {
    rewrite ^ $scheme://domain.comt$request_uri permanent;
  }
  listen               443 ssl;
  server_name          www.domain.com domain.com;
  ssl_certificate      sslkey/domain/domain.com.crt;
  ssl_certificate_key  sslkey/domain/domain.com.key;
  ssl_session_cache    shared:SSL:1m;
  ssl_session_timeout  5m;
  ssl_session_timeout  5m;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_prefer_server_ciphers  on;
  access_log off;

  location ~* \.(tpl|inc|cfg|less|scss)$ {
    deny all;
  }

  location ~ /\. {
    deny all;
    log_not_found off;
  }

  location = /robots.txt {
    alias /opt/applications/robots.txt ;
  }

  location /webapps/ {
    root /opt/applications/statics/production;
    add_header Cache-Control private;
    set $flag 0;
    if ($request_filename ~ .html$) {
      more_clear_headers "Last-Modified";
      more_clear_headers "ETag";
    }
    if (!-e $request_filename) {
      set $flag "${flag}1";
    }
    if ($request_filename !~ .html$) {
      set $flag "${flag}1";
    }
    if ($flag = "011"){
      rewrite ^(.*)$ $1.html last;
      more_clear_headers "Last-Modified";
      more_clear_headers "ETag";
      break;
    }
    if (-d $request_filename){
      rewrite ^/(.*)([^/])$ http://$host/$1$2/index.html last;
      more_clear_headers "Last-Modified";
      more_clear_headers "ETag";
      break;
    }
  }

  location / {
    root   /opt/applications/team;
    index  index.html index.htm;
  }
}
```
