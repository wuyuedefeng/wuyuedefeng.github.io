title: form表单提交
author: wsen
date: 2017-07-05 19:02:55
tags:
---
### js form提交文件

#### axis
```
const config = { headers: { 'Content-Type': 'multipart/form-data' } };
let fd = new FormData();
fd.append('file',files[0])
return axios.post("http://localhost:5000/upload", fd, config)
```

多文件提交
```
const config = { headers: { 'Content-Type': 'multipart/form-data' } };
let fd = new FormData();
// 这里必须用循环，一个个添加到表单
Array.prototype.forEach.call(evt.target.files, file => {
  fd.append('files', file)
})
return axios.post("http://localhost:5000/upload", fd, config)
```
