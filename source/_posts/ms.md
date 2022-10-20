---
title: 'ms'
date: 1019-12-09 15:43:11
tags: [github,gjs]
published: true
hideInList: false
feature: 
---
[ms](https://github.com/zeit/ms)

使用此包可以轻松地将各种时间格式转换为毫秒。


<!-- more -->


使用示例：

```javascript
ms('2 days')  // 172800000
ms('1d')      // 86400000
ms('10h')     // 36000000
ms('2.5 hrs') // 9000000
ms('2h')      // 7200000
ms('1m')      // 60000
ms('5s')      // 5000
ms('1y')      // 31557600000
ms('100')     // 100
ms('-3 days') // -259200000
ms('-1h')     // -3600000
ms('-200')    // -200
```