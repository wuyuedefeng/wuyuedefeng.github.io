title: angular中$filter过滤器的使用
author: wsen
tags:
  - $filter
categories:
  - angular.js
date: 2017-02-18 15:52:00
---
### 系统提供过滤器
#### `currency`（货币处理）
```javascript
{{num | currency : '￥'}}  //默认为 '$' 
```

#### `date`（日期格式化）更详细配置参见[这里](http://iangular.meteor.com/posts/MX7esKCtJX4MiwHtT])
```javascript
{{ today | date:'yyyy-MM-dd' }} //2015-01-01
# y M d h m s E 分别表示 年 月 日 时 分 秒 星期
# 年月日：({{ today | date:'yyyy-MM-dd' }}) 2015-10-19
# 时分秒：({{ today | date:'HH:mm:ss.sss' }}) 10:02:15.701
# 英文星期：({{ today | date:'EEEE' }})Monday
# 英文星期简写：({{ today | date:'EEE' }})Mon
```

#### `filter`（匹配字串）
```javascript
$scope.childrenArray = [
        {name:'kimi',age:3},
        {name:'cindy',age:4},
        {name:'anglar',age:4},
        {name:'shitou',age:6},
        {name:'tiantian',age:5}
    ];
$scope.func = function(e){return e.age>4;}
{{ childrenArray | filter : 'a' }} //匹配属性值中含有a的
{{ childrenArray | filter : 4 }}  //匹配属性值中含有4的
{{ childrenArray | filter : {name : 'i'} }} //参数是对象，匹配name属性中含有i的
{{childrenArray | filter : func }}  //参数是函数，指定返回age>4的
```

#### `json`（对象格式化成json字符串）
```javascript
{{ jsonTest | json}} //没有参数。这东西有什么用呢，我一般也不会在页面上输出一个json串啊，官网说它可以用来进行调试，嗯，是个不错的选择。
```

#### `limitTo`（限制数组长度或者字符串长度）
```javascript
#有点鸡肋，首先只能从数组或字符串的开头进行截取，其次，js原生的函数就可以代替它了
{{ childrenArray | limitTo : 2 }}  //将会显示数组中的前两项
```

#### `lowercase`（小写）

#### `uppercase`（大写）

#### `number`（格式化数字）
```javascript
//在html中用法
{{ number_expression | number : fractionSize}}
//在js中用法
$filter('number')(number, fractionSize)
//参数
//number 待精确的数字
//factionSize(可选) 小数点后精确位数，默认值是3.
#(默认情况下保留的小数位数小于等于3. 比如: 1234-->1234；1234.56789-->1234.568；1234.56-->1234.56 ) 
```

#### `orderBy` （排序）

---
### 自定义过滤器

```javascript
//定义filters module
var filters = angular.module('filters', []);
  filters.filter('stringAdd',function(){
    return function (obj, addString) {
        return obj + addString;
  }
});
//html中使用
{{ '07' | stringAdd:':00' }}  // 07:00
// js中使用
# 注入$filter
$filter('stringAdd')('07', ':00'); // 07:00

// 过滤器有对个参数用 ：隔开
// {{ yourExpression | yourFilter: arg1:arg2:... }}
```
