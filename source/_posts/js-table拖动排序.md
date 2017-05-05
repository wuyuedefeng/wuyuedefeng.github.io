title: js table拖动排序
author: wsen
tags:
  - js
  - ''
  - table
categories:
  - js
date: 2017-05-05 18:22:00
---
1. table添加类`.draggable-items`
2. 将可拖动的`tr`写到`tbody`中
3. 添加下面js
```
var cloneEl = null
var currentDragEl = null
var parentNode = document.querySelector('.draggable-items tbody')
var trs = parentNode.querySelectorAll('tr')
trs.forEach(function (el, index) {
  el.setAttribute('draggable', true)
  el.orSeq = index
  el.index = index

  el.onmousedown = function (event) {
    cloneEl = el.cloneNode(true)
    parentNode.insertBefore(cloneEl, el)
    cloneEl.index = el.index
    el.style.display = 'none'
    currentDragEl = el

    cloneEl.onmouseup = currentDragEl.onmouseup = function (event) {
      currentDragEl.index = cloneEl.index
      parentNode.removeChild(currentDragEl)
      parentNode.insertBefore(currentDragEl, cloneEl)
      parentNode.removeChild(cloneEl)
      currentDragEl.style.display = 'table-row'
    }
  }

  el.ondragover = function (event) {
    var nowElIndex = el.index
    if (el.index > cloneEl.index) {
      parentNode.removeChild(cloneEl)
      for (let i = cloneEl.index + 1; i <= el.index; i++) {
        el.index = el.index - 1
      }
      if (parentNode.lastChild === el) {
        parentNode.appendChild(cloneEl)
      } else {
        parentNode.insertBefore(cloneEl, el.nextSibling)
      }
      cloneEl.index = nowElIndex
    } else if (el.index < cloneEl.index) {
      for (let i = el.index; i < cloneEl.index; i++) {
        el.index = el.index + 1
      }
      parentNode.removeChild(cloneEl)
      parentNode.insertBefore(cloneEl, el)
      cloneEl.index = nowElIndex
    }
  }
})
```