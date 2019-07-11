title: linux rails 部署 配置基础
author: wsen
tags:
  - 部署
categories:
  - rails
  - linux
date: 2017-04-26 09:28:00
---
### important参考文章
* http://thelazylog.com/deploying-rails-application-with-nginx-puma-and-mina/
* https://gorails.com/deploy/ubuntu/
* http://xufei.logdown.com/posts/2014/03/05/rails-mina-puma-nginx

### 用户管理

#### 查询当前用户信息
```
$ who am i  # 伪终端的用户名 伪终端序号(pts/0) 打开时间
$ who -a	# 打印能打印的全部
$ who -d	# 打印死掉的进程
$ who -m	# 同am i,mom likes
$ who -q	# 打印当前登录用户数及用户名
$ who -u	# 打印当前登录用户登录信息
$ who -r	打印运行等级
```

#### 创建用户

新建一个叫 mars 的用户：

```
sudo adduser mars # 默认生成的 用户组 和 用户名 相同
```

切换用户到mars
```
su -l mars
```

#### 用户组

##### 查看用户属于哪些用户组
```
groups mars
```

##### 将其它用户加入 sudo 用户组

```
$ su -l mars # 切换登录用户
$ sudo ls
```
会提示 mars 不在 sudoers 文件中，意思就是 mars 不在 sudo 用户组中，至于 sudoers 文件（/etc/sudoers）你现在最好不要动它，操作不慎会导致比较麻烦的后果。

使用 `usermod` 命令可以为用户添加用户组
* -a<追加> 　把用户追加到某些组中，仅与-G选项一起使用。 
* -g<群组> 　修改用户所属的群组。 
* -G<群组> 　修改用户所属的附加群组。 

usermod -g 这条命令编辑/etc/passwd文件，修改相应用户的组ID

usermod -G 这条命令编辑/etc/group文件，增加组成员。

eg: 把`mars`用户加入`sudo`组： `usermod -aG sudo mars # 多个组之间用空格隔开 `
```
$ groups mars
$ sudo usermod -G sudo mars
$ groups mars
```

##### 删除用户
```
sudo deluser mars --remove-home
```




### Linux 文件权限

#### 变更文件所有者

```
$ sudo chown [新用户名] [文件名]
```

#### 修改文件权限

```
$ chmod 700 iphone6 # 现在，其他用户已经不能读这个“ iphone6 ”文件了
```

方式二：加减赋值操作

```
$ chmod go-rw iphone6
```
`g`、`o` 还有 `u` 分别表示 `group`、`others` 和 `user`，`+` 和 `-` 分别表示增加和去掉相应的权限。


### 添加SSH 到服务器
```
cd ~
mkdir .ssh
cd .ssh
vim authorized_keys # 将本机id_rsa.pub中的key粘贴进去
```

### 服务器生成ssh key
```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

### 安装nginx
rails ngnix配置 [参考1](http://ruby-journal.com/how-to-setup-rails-app-with-puma-and-nginx/)
```
$ [sudo] apt-get update  
$ [sudo] apt-get install nginx
```
配置文件： (配置文件所在目录：/etc/ngnix/sites-enabled/)
```
$ cd /etc/nginx/conf.d
$ vim nodeChinaClub.conf
```
使配置生效：
```
sudo service nginx reload
```
```
sudo service nginx start
sudo service nginx stop
sudo service nginx restart
```

vim /etc/nginx/sites-available/wedesign.conf
```
upstream wedesign {
  server unix:///mnt/www/wedesign/shared/tmp/sockets/puma.sock;
}

server {
  listen 80;
  server_name rails.itrydo.com; # change to match your URL
  root /mnt/www/wedesign/shared/public/assets; # I assume your app is located at that location

  location / {
    proxy_pass http://wedesign; # match the name of upstream directive which is defined above
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location ~* ^/assets/ {
    # Per RFC2616 - 1 year maximum expiry
    expires 1y;
    add_header Cache-Control public;

    # Some browsers still send conditional-GET requests if there's a
    # Last-Modified header or an ETag header even if they haven't
    # reached the expiry date sent in the Expires header.
    add_header Last-Modified "";
    add_header ETag "";
    break;
  }
}

```
creating symlink in `/etc/nginx/sites-enabled`
```
sudo ln -sf /etc/nginx/sites-available/wedesign.conf /etc/nginx/sites-enabled/wedesign.conf
```
启动报错可以执行下面命令，会打印出错误信息（个人测试）
```
sudo nginx -t -c /etc/nginx/nginx.conf
```
mina
```
bundle exec puma -e production -b unix:///mnt/www/wedesign/shared/tmp/sockets/puma.sock
# 后台启动 -d
bundle exec puma -e production -d -b unix:///mnt/www/wedesign/shared/tmp/sockets/puma.sock
```

### 安装git
```
sudo apt-get install git
```

### 安装rbenv

```
sudo apt-get install rbenv ruby-build
```
手动安装: 

[参考1](http://deangerber.com/blog/2012/05/30/install-ruby-on-ubuntu-using-rbenv/)
```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
cd ~/.rbenv && src/configure && make -C src
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
~/.rbenv/bin/rbenv init
exec $SHELL
# 安装ruby-build
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
# 通过 gem 命令安装完 gem 后无需手动输入 rbenv rehash 命令, 推荐
git clone git://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash
# 通过 rbenv update 命令来更新 rbenv 以及所有插件, 推荐
git clone git://github.com/rkh/rbenv-update.git ~/.rbenv/plugins/rbenv-update
# 使用 Ruby China 的镜像安装 Ruby, 国内用户推荐
git clone git://github.com/AndorChen/rbenv-china-mirror.git ~/.rbenv/plugins/rbenv-china-mirror
```

### 安装ruby
```
sudo apt-get install -y libssl-dev libreadline-dev zlib1g-dev
rbenv install 2.4.1
rbenv versions
rbenv local 2.4.1
gem install bundler
```


### 安装mysql

检查系统中是否已经安装了MySQL
```
sudo netstat -tap | grep mysql
```
如果没有安装，则安装MySQL.
```
sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```
测试安装是否成功：
```
sudo netstat -tap | grep mysql
```
也可通过登录MySQL测试
```
mysql -uroot -p
```
MySQL的一些简单管理：
```
# 启动MySQL服务：                       
sudo service mysql start 
# 停止MySQL服务：                       
sudo service mysql stop
# 修改 MySQL 的管理员密码：     
sudo mysqladmin -u root password newpassword
# 设置远程访问(正常情况下，mysql占用的3306端口只是在IP 127.0.0.1上监听，拒绝了其他IP的访问（通过netstat可以查看到）。取消本地监听需要修改 my.cnf 文件：)：    
sudo vi /etc/mysql/my.cnf
bind-address = 127.0.0.1 //找到此内容并且注释
```
MySQL安装后的目录结构分析(此结构只针对于使用apt-get install 在线安装情况)：
```
# 数据库存放目录：               
/var/lib/mysql/
# 相关配置文件存放目录：          
/usr/share/mysql
# 相关命令存放目录：             
/usr/bin(mysqladmin mysqldump等命令)
# 启动脚步存放目录：            
/etc/rc.d/init.d/
```
图形软件
```
Navicat
Sequel pro
```

### 安装node
```
sudo apt-get install nodejs
```

### mina 和 cap部署的配置方式
[参考1: cap_and_mina](http://blog.fir.im/cap_and_mina/)

[参考2: Mina + Puma](https://ruby-china.org/topics/26132)

[参考3: ](https://ruby-china.org/topics/32451)

[中文项目，英文项目单独部署](https://ruby-china.org/topics/25529)


### mina
首次部署可能无法自动创建数据库
```
cd /mnt/www/wedesign/current
# then run 
rake db:create RAILS_ENV=production
```



### 成功部署相关配置

#### /mnt/www/wedesign/shared/config/
puma.rb
```
#!/usr/bin/env puma

environment "production"

daemonize true
workers 2
threads 2,16

bind "unix:///mnt/www/wedesign/shared/tmp/sockets/puma.sock"

pidfile "/mnt/www/wedesign/shared/tmp/pids/puma.pid"
state_path "/mnt/www/wedesign/shared/tmp/sockets/puma.state"

stdout_redirect '/mnt/www/wedesign/shared/log/stdout', '/mnt/www/wedesign/shared/log/stderr'

directory "/mnt/www/wedesign/current"

activate_control_app 'unix:///mnt/www/wedesign/shared/tmp/sockets/pumactl.sock'

prune_bundler
```
database.yml
```
default: &default
  adapter: mysql2
  encoding: utf8
  pool: 5
  username: root
  password:
  host: localhost
production:
  <<: *default
  database: [project_database_name]
  username: [database_username]
  password: [database_password]
```
secrets.yml 
```
production:
  #secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
  # or run rake secret
  secret_key_base: [secret_key]
```

### /etc/nginx/sites-available/
wedesign.conf
```
upstream wedesign {
  server unix:///mnt/www/wedesign/shared/tmp/sockets/puma.sock;
}

server {
  listen 80;
  server_name rails.itrydo.com; # change to match your URL
  root /mnt/www/wedesign/shared/public; # I assume your app is located at that location

  location / {
    proxy_pass http://wedesign; # match the name of upstream directive which is defined above
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location ~* ^/assets/ {
    # Per RFC2616 - 1 year maximum expiry
    expires 1y;
    add_header Cache-Control public;

    # Some browsers still send conditional-GET requests if there's a
    # Last-Modified header or an ETag header even if they haven't
    # reached the expiry date sent in the Expires header.
    add_header Last-Modified "";
    add_header ETag "";
    break;
  }
}
```
then
```
$ sudo ln -sf /etc/nginx/sites-available/wedesign.conf /etc/nginx/sites-enabled/wedesign.conf
```