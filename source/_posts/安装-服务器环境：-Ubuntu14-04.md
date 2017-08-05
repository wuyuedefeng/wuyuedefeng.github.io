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

### nginx location模块--匹配规则

Location语法语法：location [=|~|~*|^~] /uri/ { … }
* = --> 开头表示精确匹配
* ^~ --> 开头表示uri以某个常规字符串开头，理解为匹配url路径即可。 nginx不对url做编码，因此请求为/static/20%/aa，可以被规则^~ /static/ /aa匹配到（注意是空格）。
* ~ --> 开头表示区分大小写的正则匹配
* ~* --> 开头表示不区分大小写的正则匹配
* !~和!~* --> 分别为区分大小写不匹配及不区分大小写不匹配的正则
* / --> 通用匹配，任何请求都会匹配到。

多个location配置的情况下匹配顺序为：

首先匹配=，其次匹配^~, 其次是按文件中顺序的正则匹配，最后是交给 / 通用匹配。当有匹配成功时候，停止匹配，按当前匹配规则处理请求。

例子，有如下匹配规则：
```
location = / {
   #http://localhost/ 访问根目录/
   #规则A
}


location = /login {
   #http://localhost/login
   #规则B
}



location ^~ /static/ {
   #http://localhost/static/a.html
   #http://localhost/static/c.png 则优先匹配到 规则C
   #规则C
}

#http://localhost/a.gif, http://localhost/b.jpg 将匹配规则D和规则E，但是规则D顺序优先，规则E不起作用，
location ~ \.(gif|jpg|png|js|css)$ {

   #规则D
}


location ~* \.png$ {
   #http://localhost/a.PNG 则匹配规则E，而不会匹配规则D，因为规则E不区分大小写。
   #规则E
}


http://localhost/a.xhtml 不会匹配规则F和规则G，
http://localhost/a.XHTML 不会匹配规则G，因为不区分大小写。
规则F，规则G属于排除法，符合匹配规则但是不会匹配到，所以想想看实际应用中哪里会用到。
location !~ \.xhtml$ {
   #规则F
}


location !~* \.xhtml$ {
   #规则G
}

访问http://localhost/category/id/1111 最终匹配到规则H，因为以上规则都不匹配，这个时候应该是nginx转发请求给后端应用服务器，
比如FastCGI（php），tomcat（jsp），nginx作为方向代理服务器存在。
location / {
   #http://localhost/register
   #规则H
}
```

所以实际使用中，个人觉得至少有三个匹配规则定义，如下：
```
#直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。
#这里是直接转发给后端应用服务器了，也可以是一个静态首页
# 第一个必选规则
location = / {
    proxy_pass http://tomcat:8080/index
}

# 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}

#第三个规则就是通用规则，用来转发动态请求到后端应用服务器
#非静态文件请求就默认是动态请求，自己根据实际把握
#毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
location / {
    proxy_pass http://tomcat:8080/
}
```

对于以上基础推荐配置，有一个补充，就是关于转发有一点需要注意。例如下面配置，对一个目录转发：
```
location ^~ /outer/ {
    #case A： url最后以/结尾
    proxy_pass http://tomcat:8080/
    #case B： url最后没有/
    #proxy_pass http://tomcat:8080  
}
```
关键在于最后的/，访问localhost/outer/in.html，其中case A会转发到tomcat:8080/in.html， 而case B会转发到tomcat:8080/outer/in.html，所以务必注意了。

参考自：http://www.cnblogs.com/linjiqin/p/5532119.html

### nginx upstream模块--负载均衡
upstream模块相关说明

1. upstream模块应放于nginx.conf配置的http{}标签内
2. upstream模块默认算法是wrr (权重轮询 weighted round-robin)

---
一、分配方式 Nginx的upstream支持5种分配方式，下面将会详细介绍，其中前三种为Nginx原生支持的分配方式，后两种为第三方支持的分配方式。

#### 1. 轮询

轮询是upstream的默认分配方式，即每个请求按照时间顺序轮流分配到不同的后端服务器，如果某个后端服务器down掉后，能自动剔除。
```
upstream backend {
    server 192.168.1.101:8888;
    server 192.168.1.102:8888;
    server 192.168.1.103:8888;
}
```
#### 2. weight
轮询的加强版，即可以指定轮询比率，weight和访问几率成正比，主要应用于后端服务器异质的场景下。
```
upstream backend {
    server 192.168.1.101 weight=1;
    server 192.168.1.102 weight=2;
    server 192.168.1.103 weight=3;
}
```
#### 3. ip_hash
每个请求按照访问ip（即Nginx的前置服务器或者客户端IP）的hash结果分配，这样每个访客会固定访问一个后端服务器，可以解决session一致问题。
```
upstream backend {
    ip_hash;
    server 192.168.1.101:7777;
    server 192.168.1.102:8888;
    server 192.168.1.103:9999;
}
```
注意：

1. 当负载调度算法为ip_hash时，后端服务器在负载均衡调度中的状态不能是weight和backup。
2. 导致负载不均衡。


#### 4. fair
fair顾名思义，公平地按照后端服务器的响应时间（rt）来分配请求，响应时间短即rt小的后端服务器优先分配请求。如果需要使用这种调度算法，必须下载Nginx的upstr_fair模块。
```
upstream backend {
    server 192.168.1.101;
    server 192.168.1.102;
    server 192.168.1.103;
    fair;
}
```

#### 5. url_hash，目前用consistent_hash替代url_hash

与ip_hash类似，但是按照访问url的hash结果来分配请求，使得每个url定向到同一个后端服务器，主要应用于后端服务器为缓存时的场景下。
```
upstream backend {
    server 192.168.1.101;
    server 192.168.1.102;
    server 192.168.1.103;
    hash $request_uri;
    hash_method crc32;
}
```
其中，hash_method为使用的hash算法，需要注意的是：此时，server语句中不能加weight等参数。

提示：url_hash用途cache服务业务，memcached，squid，varnish。特点：每个rs都是不同的。

---

#### 设备状态
从上面实例不难看出upstream中server指令语法如下：server address [parameters]

参数说明：

* server：关键字，必选。
* address：主机名、域名、ip或unix socket，也可以指定端口号，必选。
* parameters：可选参数，可选参数如下：

	1. down：表示当前server已停用
	2. backup：表示当前server是备用服务器，只有其它非backup后端服务器都挂掉了或者很忙才会分配到请求。
	3. weight：表示当前server负载权重，权重越大被请求几率越大。默认是1.
	4. max_fails和fail_timeout一般会关联使用，如果某台server在fail_timeout时间内出现了max_fails次连接失败，那么Nginx会认为其已经挂掉了，从而在fail_timeout时间内不再去请求它，fail_timeout默认是10s，max_fails默认是1，即默认情况是只要发生错误就认为服务器挂掉了，如果将max_fails设置为0，则表示取消这项检查。
    
参考自：http://www.cnblogs.com/linjiqin/p/5494783.html

### 80端口部署多个静态网页
https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-14-04-lts