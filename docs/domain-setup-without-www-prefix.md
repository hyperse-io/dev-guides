# domain setup without www prefix

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

  location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host:$server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://www_domain_node;
    proxy_redirect off;
  }

  location /recharge/oss {
    rewrite ^/recharge/oss/?(.*)$ /$1 break;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host:$server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://www_domain_node_oss;
    proxy_redirect off;
  }

  location /recharge/mp {
    rewrite ^/recharge/mp/?(.*)$ /$1 break;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host:$server_port;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://www_domain_node_mp;
    proxy_redirect off;
  }
}
```
