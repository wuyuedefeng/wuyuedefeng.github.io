title: https
author: wsen
date: 2017-08-04 23:00:08
tags:
---
```
$ ssh deploy@IPADDRESS
$ cd ~
$ git clone https://github.com/letsencrypt/letsencrypt
$ cd letsencrypt/
$ ./letsencrypt-auto --agree-dev-preview --server https://acme-v01.api.letsencrypt.org/directory auth
```

```
$ cd ~
$ mkdir etc
$ openssl dhparam -out dhparams.pem 2048
```

```
upstream vitamin {
  server unix:///mnt/www/vitamin/shared/tmp/sockets/puma.sock;
}

server {
  listen 443 ssl;
  server_name xn--n8jhv5b1dva9290n.com;
  root /mnt/www/vitamin/shared/public;

  location / {
    proxy_pass http://vitamin; # match the name of upstream directive which is defined above
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  ssl on;
  ssl_certificate /etc/letsencrypt/live/xn--n8jhv5b1dva9290n.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/xn--n8jhv5b1dva9290n.com/privkey.pem;

  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
  ssl_prefer_server_ciphers on;
  ssl_session_cache shared:SSL:10m;
  ssl_dhparam /home/deploy/etc/dhparams.pem;
}
```