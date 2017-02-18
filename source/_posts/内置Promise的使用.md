title: 内置Promise的使用
author: wsen
tags:
  - promise
categories:
  - node.js
date: 2017-02-18 15:33:00
---
### 引入方法

高版本node已经内嵌了Promise机制

```javascript
var Promise = require('promise');
```

### 简单使用
> new Promise`依赖关系顺序执行`
```javascript
function testPromise(begin) {
    return new Promise(function (resolve, reject) {
        // 在这里写异步方法, resolve是then的第一个参数, reject是then的第二个参数
        if (begin <= 3){
            resolve(begin);
        }
        reject('begin 不允许 > 3');
    }).then(function (begin) {
        console.log(begin);
    }, function(err){
        console.log(err);
    });
}
// 调用测试
testPromise(0); // 0
testPromise(4); // begin 不允许 > 3
```

### 高级使用
> new Promise.all`非依赖同时执行`
```javascript
function onePromise(begin) {
    return new Promise(function (resolve, reject) {
        if (begin <= 3){
            resolve(begin);
        }
        reject('begin = '+ begin +' 不允许 > 3');
    });
}
function testPromise2(begin){
    return new Promise.all([onePromise(begin), onePromise(begin+1), onePromise(begin+2)]).then(function(datas){
        console.log(datas);
    }, function(err){
        console.log(err);
    })
}
// 调用测试
testPromise2(1); // [ 1, 2, 3 ]
testPromise2(2); // begin = 4 不允许 > 3
```

---
### 其他Promise 第三方库
 如果node版本较低,可以使用其他实现Promise标准的库
* `bluebird` [github](https://github.com/petkaantonov/bluebird)
* `Q` [github](https://github.com/kriskowal/q)
* `Deferred `[github](https://github.com/medikoo/deferred)
* `then `[github](https://github.com/teambition/then.js)
* `when `[github](https://github.com/cujojs/when)
* `async `[github](https://github.com/caolan/async)
* `eventproxy `(事件驱动)[github](https://github.com/JacksonTian/eventproxy)

