---
title: '更安全的Node.js缓冲区API'
date: 1019-12-09 15:09:36
tags: [github,gnodejs]
published: true
hideInList: false
feature: 
---
[更安全的Node.js缓冲区API](https://github.com/feross/safe-buffer)

使用示例：

```javascript
var Buffer = require('safe-buffer').Buffer

// Existing buffer code will continue to work without issues:

new Buffer('hey', 'utf8')
new Buffer([1, 2, 3], 'utf8')
new Buffer(obj)
new Buffer(16) // create an uninitialized buffer (potentially unsafe)

// But you can use these new explicit APIs to make clear what you want:

Buffer.from('hey', 'utf8') // convert from many types to a Buffer
Buffer.alloc(16) // create a zero-filled buffer (safe)
Buffer.allocUnsafe(16) // create an uninitialized buffer (potentially unsafe)
```