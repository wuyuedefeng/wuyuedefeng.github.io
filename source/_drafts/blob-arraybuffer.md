title: blob arraybuffer
author: wsen
date: 2017-07-01 16:48:02
tags:
---
### `blob` to `arraybuffer`
```
var arrayBuffer;
var fileReader = new FileReader();
fileReader.onload = function() {
    arrayBuffer = this.result;
};
fileReader.readAsArrayBuffer(blob);
```

### `arraybuffer` to `blob`
```
var blob = new Blob([arraybuffer])
```

### arrayBufferToBase64
```
function arrayBufferToBase64( buffer ) {
    var binary = '';
    var bytes = new Uint8Array( buffer );
    var len = bytes.byteLength;
    for (var i = 0; i < len; i++) {
        binary += String.fromCharCode( bytes[ i ] );
    }
    return window.btoa( binary );
}
```

### base64ToArrayBuffer
```
function base64ToArrayBuffer(base64) {
    var binary_string =  window.atob(base64);
    var len = binary_string.length;
    var bytes = new Uint8Array( len );
    for (var i = 0; i < len; i++)        {
        bytes[i] = binary_string.charCodeAt(i);
    }
    return bytes.buffer;
}
```

### write to file
```
// chunk is the Uint8Array object
fs.appendFile(path, new Buffer(chunk), function (err) {
    if (err) {
      fut.throw(err);
    } else {
      fut.return(chunk.length);
    }
});
```


### node js
`file` to `base64` to `file`

```javascript
let path = '/Users/mars/Desktop/111.jpg'

var fs = require('fs');

// function to encode file data to base64 encoded string
function base64_encode(file) {
    // read binary data
    var bitmap = fs.readFileSync(file);
    // convert binary data to base64 encoded string
    return new Buffer(bitmap).toString('base64');
}

// function to create file from base64 encoded string
function base64_decode(base64str, file) {
    // create buffer object from base64 encoded string, it is important to tell the constructor that the string is base64 encoded
    var bitmap = new Buffer(base64str, 'base64');
    // write buffer to file
    fs.writeFileSync(file, bitmap);
    console.log('******** File created from base64 encoded string ********');
}

let en = base64_encode(path)
base64_decode(en, '/Users/mars/Desktop/12.jpg')
```

### hex to base64
```
var base64String = new Buffer(hexString, 'hex').toString('base64')
```

### base64 to hex
```
var hexString = new Buffer(base64String, 'base64').toString('hex')
```

参考资料
* http://shiwah.me/2015/12/29/20151229_arrayBuffer/