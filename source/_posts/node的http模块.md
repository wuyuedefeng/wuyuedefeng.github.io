title: node的http模块
author: wsen
tags:
  - http
categories:
  - node.js
date: 2017-02-18 12:35:00
---
### 创建简单服务器
[参考](http://blog.csdn.net/youyudehexie/article/details/11760059)
```
var http = require('http');
http.createServer(function (request, response) {
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.end('Hello World\n');
}).listen(1337, '127.0.0.1');
console.log('Server running at http://127.0.0.1:1337/');
```

### 根据url下载文件
```javascript
var http = require('http');
var fs = require('fs');
var url = 'http://www.baidu.com/dd2.jpg';
var downloadFilePath = '/c/abc.png';
var file = fs.createWriteStream(downloadFilePath, {flags: 'w'});
var req = http.get(url, function (res) {
    res.pipe(file, {end: 'false'});
    res.on('end', function () {
        file.end();
        console.log('download success');
    }).on('error', function(e) {
        console.log('error:', e);
    });
});
req.on('error', function(err){
      //错误处理，处理res无法处理到的错误
      console.error('download file error out:', url);
});
```