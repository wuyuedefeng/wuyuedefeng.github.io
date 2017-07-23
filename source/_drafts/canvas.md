title: canvas
author: wsen
date: 2017-07-21 21:15:21
tags:
---
hex to rgb
```
function hexToRgb(hex) {
	var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
	return result ? {
		r: parseInt(result[1], 16),
		g: parseInt(result[2], 16),
		b: parseInt(result[3], 16)
	} : null;
}

```
rgb to hex
```
function rgbToHex(r, g, b) {
	return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
}
```

像素点遍历与修改
```
function decryptColor(canvas, rotten) {
	var ctx = canvas.getContext('2d')
	var imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
	var data = imgData.data;

	for (let i = 0; i < data.length; i+=4) {
		data[i] = data[i]  // r
		data[i+1] = decNumber(data[i+1], i+1, rotten) // g
		data[i+2] = decNumber(data[i+2], i+2, rotten) // b
	}
	ctx.putImageData(imgData, 0, 0)
}
```