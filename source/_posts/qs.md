---
title: 'qs'
date: 1019-12-09 15:40:47
tags: [github,gjs]
published: true
hideInList: false
feature: 
---
[qs](https://github.com/ljharb/qs)

具有一些附加安全性的querystring解析和字符串化库。


<!-- more -->


使用示例：

```javascript
var qs = require('qs');
var assert = require('assert');

var obj = qs.parse('a=c');
assert.deepEqual(obj, { a: 'c' });

var str = qs.stringify(obj);
assert.equal(str, 'a=c');
```