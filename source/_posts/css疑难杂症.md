title: css疑难杂症
author: wsen
tags: []
categories:
  - css
date: 2017-06-06 09:52:00
---
### 垂直居中

> 单行文字居中
* line-height
```html
<div style="line-height: 100px">
	<span>文字垂直居中</span>
</div>
```
* table-cell, [vertical-align](https://segmentfault.com/a/1190000002668492)
```html
<div style="height: 300px; display: table-cell; vertical-align: middle;">
	<p>aaa</p>
</div>
```

> 多行文字居中
* table-cell
```html
<div style="height: 300px; display: table-cell; vertical-align: middle;">
	<p>aaa</p>
	<p>bbb</p>
</div>
```

> 单行文字和图片垂直居中 (前提：内部字体相对较小)
* line-height, [vertical-align](https://segmentfault.com/a/1190000002668492)
```html
<div style="line-height: 500px;">
	<img src="./titles-calc.png" style="/*display: inline-block;*/vertical-align: middle;">
	<span style="vertical-align: middle">aaa</span>
</div>
```
* flex 布局
```html
<div style="height: 500px; display: flex; align-items: center;">
	<img src="./titles-calc.png">
	<span>aaa</span>
</div>
```