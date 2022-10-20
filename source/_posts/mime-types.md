---
title: 'mime-types'
date: 1019-12-09 15:43:59
tags: [github,gjs]
published: true
hideInList: false
feature: 
---
[https://github.com/jshttp/mime-types](https://github.com/jshttp/mime-types)

content-type库

<!-- more -->

使用示例：

```javascript
mime.lookup('json')             // 'application/json'
mime.lookup('.md')              // 'text/markdown'
mime.lookup('file.html')        // 'text/html'
mime.lookup('folder/file.js')   // 'application/javascript'
mime.lookup('folder/.htaccess') // false

mime.lookup('cats') // false
```