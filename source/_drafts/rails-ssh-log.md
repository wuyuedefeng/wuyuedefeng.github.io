title: rails ssh log
author: wsen
date: 2017-07-24 11:54:39
tags:
---
```
require 'net/ssh'
host = '13.19.17.24'
username = 'deploy'
port = 5331
#连接服务器，执行 tail -f 指令
server_cmd1 = %q(tail -f /mnt/www/nirvana_staging/current/log/production.log)
# 连接到远程主机
ssh = Net::SSH.start(host, username, { :port => port }) do |ssh|
  result = ssh.exec!(server_cmd1) do |ssh, type , data|
    if /INFO/ =~  data
  	  puts "\e[32m#{data}\e[0m" #这里的data就是，每次写入staging.log的数据
    elsif /FATAL/ =~  data
      puts "\e[31m#{data}\e[0m"
    elsif /WARN/ =~  data
      puts "\e[33m#{data}\e[0m"
    else
      p data
    end

  end
  puts result
end
```