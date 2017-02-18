title: redis的安装和配置
author: wsen
tags:
  - install
categories:
  - redis
date: 2017-02-18 15:43:00
---
【[参考](http://www.cnblogs.com/_popc/p/3684835.html)】

---
### 安装Redis
```bash
# 希望将redis安装到此目录 
/usr/local/redis
# 希望将安装包下载到此目录 
/usr/local/src

那么安装过程指令如下： 
$ mkdir /usr/local/redis  
$ cd /usr/local/src  
$ wget http://download.redis.io/releases/redis-3.0.7.tar.gz
$ tar xzf redis-3.0.7.tar.gz
$ cd redis-3.0.7
$ make PREFIX=/usr/local/redis install #安装到指定目录中

#如果make失败，一般是你们系统中还未安装gcc,那么可以通过yum安装： 
$ yum install gcc

#在安装redis成功后，你将可以在/usr/local/redis看到一个bin的目录，里面包括了以下文件： 
redis-benchmark  redis-check-aof  redis-check-dump  redis-cli  redis-server
```
---
### 将Redis做成服务
##### redis默认运行脚本
* ps: `/etc/init.d/`目录下的脚本就类似与windows中的注册表，在系统启动的时候某些指定脚本将被执行 
* 按以上步骤安装Redi时，其服务脚本位于：`/usr/local/src/redis-3.0.7/utils/redis_init_script `

##### 拷贝脚本到`/etc/init.d/`目录下，并命名为redis
```bash
$ [sudo] cp /usr/local/src/redis-3.0.7/utils/redis_init_script /etc/init.d/redis
```
---
### 对启动脚本进行修改
打开redis脚本文件，并进行编辑
```bash
$ vim /etc/init.d/redis

#修改到正确的路径
#EXEC=/usr/local/bin/redis-server
EXEC=/usr/local/redis/bin/redis-server
#CLIEXEC=/usr/local/bin/redis-cli
CLIEXEC=/usr/local/redis/bin/redis-cli
# 设置配置文件路径
#CONF="/etc/redis/${REDISPORT}.conf"
```
---
### 配置Redis启动信息
在上面shell脚本指定的配置目录中创建文件，并进行编辑。更详细配置参考[这里](https://github.com/wuyuedefeng/redis-conf)
```bash
$ [sudo] mkdir /etc/redis/
$ [sudo] vim /etc/redis/6379.conf

写入配置:
#!/bin/sh
# 是否以后台进程运行，默认为no
daemonize yes
# 如以后台进程运行，则需指定一个pid，默认为/var/run/redis.pid
pidfile /var/run/redis.pid
# 监听端口，默认为6379
port 6379
#绑定主机IP，默认值为127.0.0.1（注释）
bind 127.0.0.1
# 超时时间，默认为300（秒）
timeout 300
# 日志记录等级，有4个可选值，debug，verbose（默认值），notice，warning
loglevel verbose
#可用数据库数，默认值为16，默认数据库为0
databases 16
#指出在多长时间内，有多少次更新操作，就将数据同步到数据文件。这个可以多个条件配合，比如默认配置文件中的设置，就设置了三个条件。
# 900秒（15分钟）内至少有1个key被改变
save 900 1
# 300秒（5分钟）内至少有10个key被改变
save 300 10
# 存储至本地数据库时是否压缩数据，默认为yes
rdbcompression yes
# 本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb
# 本地数据库存放路径，默认值为 ./
dir /usr/apps/redis_db
#开启之后，redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用。当你的使 用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存
activerehashing yes
```
---
### 启动redis服务
手动启动命令
```bash
$ service redis start
```
方便redis客户端的使用，可以将Redis的所在目录添加到系统参数PATH中
* 修改profile文件：`/etc/profile`
* 最文件最后一行添加`export PATH="$PATH:/usr/local/redis/bin"`
* 应用配置执行
```bash
$ . /etc/profile  
``` 
* 至此可以直径调用redis-cli的命令了
```bash
$ redis-cli   
redis 127.0.0.1:6379> auth superman  
OK   
redis 127.0.0.1:6379> ping  
PONG   
redis 127.0.0.1:6379>
```

