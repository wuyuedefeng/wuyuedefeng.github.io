title: linux基础
author: wsen
date: 2017-04-26 09:28:57
tags:
---
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