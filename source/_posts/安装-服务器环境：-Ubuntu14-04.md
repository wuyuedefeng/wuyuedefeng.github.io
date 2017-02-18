title: 服务器环境安装nginx： Ubuntu14.04
author: wsen
tags:
  - install
categories:
  - nginx
date: 2017-02-17 16:16:00
---
### 安装

服务器环境： `Ubuntu14.04`

apt-get 安装
```
$ [sudo] apt-get update  
$ [sudo] apt-get install nginx
```

配置文件： (配置文件所在目录：`/etc/ngnix/sites-enabled/`)
```
$ cd /etc/nginx/nginx.conf
$ vim nodeChinaClub.conf
```

写入：
```
server {
  server_name itrydo.com;
  location / {
    proxy_pass http://localhost:8088;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
  }
}
```

使配置生效：
```
service nginx reload
```

