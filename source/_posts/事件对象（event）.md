title: 事件对象（event）
author: wsen
tags:
  - event
categories:
  - javascript
  - ''
date: 2017-02-18 12:21:00
---
* `onabort`	图像加载被中断
* `onblur`	元素失去焦点	
* `onchange`	用户改变域的内容
* `onclick`	鼠标点击某个对象
* `ondblclick`	鼠标双击某个对象	
* `onerror`	当加载文档或图像时发生某个错误
* `onfocus`	元素获得焦点
* `onkeydown`	某个键盘的键被按下
* `onkeypress`	某个键盘的键被按下或按住
* `onkeyup`	某个键盘的键被松开
* `onload`	某个页面或图像被完成加载
* `onmousedown`	某个鼠标按键被按下
* `onmousemove`	鼠标被移动
* `onmouseout`	鼠标从某元素移开
* `onmouseover`	鼠标被移到某元素之上
* `onmouseup`	某个鼠标按键被松开
* `onreset`	重置按钮被点击
* `onresize`	窗口或框架被调整尺寸
* `onselect`	文本被选定
* `onsubmit`	提交按钮被点击
* `onunload`	用户退出页面

#### event对象有哪些属性或者方法
* `type`：事件类型。用来描述这个事件是什么类型的，比如onclick的事件类型为click。
* `srcElement/target`：事件源，就是发生事件的元素； FF下是target，IE下是srcElement
* `cancelBubble`：一个布尔属性，把它设置为true的时候，将停止事件进一步起泡到包容层次的元素；(e.cancelBubble = true; 相当于 e.stopPropagation();) 。FF下是后者。
* `keyCode`: 这个不陌生了吧。也非常的常用，用来判断你按下了哪个键
* `attachEvent(), detachEvent()/addEventListener(), removeEventListener`：为指定DOM对象事件类型注册多个事件处理函数的方法，它们有两个参数，第一个是事件类型，第二个是事件处理函数。在 attachEvent()事件执行的时候，this关键字指向的是window对象，而不是发生事件的那个元素；

当然还有其他的一些属性，我这里列出来的都是最常用的。比如还有:
`button`、 `clientX,clientY`、`offsetX,offsetY` `layerX,layerY`、`x,y` `pageX,pageY`、`altKey,ctrlKey,shiftKey`、`fromElement,toElement`、`screenX、screenY`。

---
Event `altKey、ctrlKey、shiftKey`属性 

> * `event.altKey `
 * 功能：检测事件发生时Alt键是否被按住了。
 * 语法：event.altKey
 * 取值：true | false
 * 说明：
altKey属性为true表示事件发生时Alt键被按下并保持，为false则Alt键没有按下。 
 * altKey属性可结合鼠标或键盘使用，多用于制作一些快捷操作方式。 
 
* `event.ctrlKey `
 * 功能：检测事件发生时Ctrl键是否被按住了。
 * 语法：event.ctrlKey
 * 取值：true | false
 * 说明： ctrlKey属性为true表示事件发生时Ctrl键被按下并保持，为false则Ctrl键没有按下。 
 * ctrlKey属性可结合鼠标或键盘使用，多用于制作一些快捷操作方式。 
 
> * `event.shiftKey `
 * 功能：检测事件发生时Shift键是否被按住了。
 * 语法：event.shiftKey
 * 取值：true | false
 * 说明： shiftKey属性为true表示事件发生时Shift键被按下并保持，为false则Shift键没有按下。 
 * shiftKey属性可结合鼠标或键盘使用，多用于制作一些快捷操作方式。 

