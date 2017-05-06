title: git常用命令
author: wsen
tags:
  - git
categories:
  - git
date: 2017-05-06 22:39:00
---

### 命令大全

![png](https://github.com/wuyuedefeng/wuyuedefeng.github.io/blob/develop/images/git-commands.png)

### 删除git代码管理中已经提交的服务器的提交
```
#git reset --hard HEAD~1 # 取消当前版本之前的一次提交
git reset --hard HEAD~2 # 取消当前版本之前的两次提交
git push origin HEAD --force # 强制提交到远程版本库，从而删除之前的两次提交数据
```
### 在mac中自动保存git用户名与密码如此简单

在Mac OS X中这个操作竟然如此简单。只需在Terminal中输入如下的命令：
```
git config --global credential.helper osxkeychain
```
然后在git操作时只要输入一次用户名与密码，以后就不用输入了。

【参考资料】

[Git keeps prompting me for password](http://stackoverflow.com/questions/7773181/git-keeps-prompting-me-for-password)

