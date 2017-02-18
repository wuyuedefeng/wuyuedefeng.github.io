title: pm2部署服务器
author: wsen
tags:
  - pm2
categories:
  - node.js
date: 2017-02-18 11:59:00
---
### 基本使用

* 本地和服务器安装pm2
```
$ npm install -g pm2
```

* 使用pm2启动应用
```
$ pm2 start xxx.js
```

* 开发中启动，代码变更后自动重启应用
```
$ pm2 start xxx.js --watch
```

### cluster模式

默认情况下pm2是以`fork模式`启动应用的，如果以`cluster模式`启动的话，则可以使用pm2自带的负载均衡、零间断重启等功能。
```
$ pm2 start xxx.js -i 4
```
上面的命令会以`cluster`模式启动4个应用进程，并自动为它们提供负载均衡，并且可以使`用gracefulRelo`ad达到更新应用时不中断服务的效果。


### 升级pm2
```
$ npm install -g pm2@latest
$ pm2 updatePM2
```

### 部署

接下来需要建立一个部署的配置文件，这个文件在本机（操作发布的机器）和服务器上都需要有，因此最好放入Git版本管理中，并且推送到远程代码库（Git服务器）。

切换到项目目录下，然后执行
```
$ pm2 ecosystem
```
即可得到一个示例json文件
```
{
"apps" : [{
 "name"      : "API",
 "script"    : "app.js",
 "env": {
   "COMMON_VARIABLE": "true"
 },
 "env_production" : {
   "NODE_ENV": "production"
 }
},{ //没有这个环境可以删除掉
 "name"      : "WEB",
 "script"    : "web.js"
}],
"deploy" : {
 "production" : {
   "user" : "node",//登录账号
   "host" : "212.83.163.1",//服务器地址
   "ref"  : "origin/master",//git 分支
   "repo" : "git@github.com:wuyuedefeng/node-china.club.git",//git地址
   "path" : "/var/www/production",//服务器项目目录
   "post-deploy" : "pm2 startOrRestart ecosystem.json5 --env production"
 },
 "dev" : {
   "user" : "node",
   "host" : "212.83.163.1",
   "ref"  : "origin/master",
   "repo" : "git@github.com:repo.git",
   "path" : "/var/www/development",
   "post-deploy" : "pm2 startOrRestart ecosystem.json5 --env dev",
   "env"  : {
     "NODE_ENV": "dev"
   }
 }
}
}
```
* `apps.name`和`apps.script`应该与PM2识别应用有关，后续执行pm2 restart的时候可以对应到进程（未证实）
* deploy中可以含有多个环境，需要能够通过SSH（公钥认证）登录服务器
* `web`目录并不是真正的放版本库文件的目录，PM2会再建立一个source子目录，这个才是真正放代码的目录
* `post-deploy`是指代码部署完之后执行的命令，这里以Node.js为例子，执行依赖安装，然后重启PM2中的进程 然后就可以使用了

首先需要安装一下
```
$ pm2 deploy ecosystem.json production setup
```
如果已经安装可以运行下面这个命令进行部署(需要添加服务器的ssh key到github上)
```
$ pm2 deploy ecosystem.json production
```
大功告成！