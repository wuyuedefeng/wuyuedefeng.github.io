title: nginx配置 【vue、 rails】
author: wsen
date: 2017-12-09 12:01:43
tags:
---
### vue http config
```
server {
    server_name wap.waimai.com;
    listen 80;
    listen [::]:80;

    root /mnt/www/waimai-wap;
    index index.html;
    location ^~ /static/ {
      gzip_static on;
      expires max;
      add_header Cache-Control public;
    }
    location / {
      try_files $uri $uri/ /index.html;
    }
    location ~* \.(css|js|gif|jpe?g|png)$ {
      expires 100d;
      access_log off;
      add_header Pragma public;
      add_header Cache-Control "public";
    }
}
```
 
### vue https config
```
server {
  listen 443;
  listen 80;
  ssl on;
  ssl_certificate /etc/letsencrypt/live/www.itrydo.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/www.itrydo.com/privkey.pem;

  server_name  www.itrydo.com;
  
  if ( $http_user_agent ~* "(Android|iPhone|Windows Phone|UC|Kindle)" ){
      rewrite  ^/(.*)$  http://m.itrydo.com$uri redirect;
   }

  root /mnt/www/waimai/current/dist/;
  index index.html;
  location ^~ /static/ {
   gzip_static on;
   expires max;
   add_header Cache-Control public;
  }
  location / {
     try_files $uri $uri/ /index.html;
  }
}
```
 
### rails config
```
upstream waimai {
  server unix:///mnt/www/relation_api/shared/tmp/sockets/puma.sock;
}
# http
server {
  listen 80;
  server_name api.itrydo.com api.waimai.com adm.waimai.com bus.waimai.com; # change to match your URL
  index index.html;
  root /mnt/www/relation_api/current/public; # I assume your app is located at that location
  try_files $uri/index.html $uri @app;
  location @app {
    # try_files $uri/index.html $uri =404;
    proxy_pass http://waimai; # match the name of upstream directive which is defined above
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  location /cable {
    proxy_pass http://waimai;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }
}

# http redirect https
#server {
#  server_name api.itrydo.com api.waimai.com adm.waimai.com bus.waimai.com;
#  listen 80;
 # return 308 https://$server_name$request_uri;
 # location / {
 #   rewrite ^/(.*)$ https://$server_name/$1 permanent;
 # }
#   rewrite ^ https://$server_name$request_uri? permanent;
#}
# https
server {
  server_name api.itrydo.com api.waimai.com adm.waimai.com bus.waimai.com;
  listen 443 ssl http2;
  index index.html;
  root /mnt/www/relation_api/current/public;
  ssl on;
  ssl_certificate  /home/deploy/letsencrypt/api/api.chained.crt;
  ssl_certificate_key  /home/deploy/letsencrypt/api/api.waimai.com.key;
  ssl_session_cache    shared:SSL:1m;
  ssl_session_timeout  5m;
  ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
  ssl_prefer_server_ciphers  on;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

  try_files $uri/index.html $uri @app;
  
  location /cable {
    proxy_pass http://waimai;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
  }

  location @app {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Ssl on;
    proxy_set_header X-Forwarded-Port 443;
    proxy_set_header X-Forwarded-Host $host;
    proxy_redirect off;
    proxy_pass http://waimai;
  }
  
} 
```