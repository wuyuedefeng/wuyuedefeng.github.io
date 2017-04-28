title: ssh
author: wsen
tags:
  - install ssh
categories:
  - shell
date: 2017-04-28 16:38:00
---
### Generating a new SSH key

1. 生成秘钥
```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

2. 把公钥文件id_rsa.pub拷贝到需要登录的服务器上


3. 登录远程服务器，在用户根目录下新建.ssh文件夹（如果不存在），在其中新建authorized_keys文件（如果不存在），把id_rsa.pub添加到authorized_keys文件中

4. 退出当前远程登录，之后就可以不使用密码远程登录了