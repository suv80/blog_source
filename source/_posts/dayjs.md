---
title: 'Day.js'
date: 1019-12-09 15:08:44
tags: [github,gjs]
published: true
hideInList: false
feature: 
---
[Day.js](https://github.com/iamkun/dayjs)

Day.js 是一个轻量的处理时间和日期的 JavaScript 库，和 Moment.js 的 API 设计保持完全一样. 如果您曾经用过 Moment.js, 那么您已经知道如何使用 Day.js


<!-- more -->


使用示例：

```javascript
dayjs('2018-08-08') // parse
dayjs().format('{YYYY} MM-DDTHH:mm:ss SSS [Z] A') // display
dayjs().set('month', 3).month() // get & set
dayjs().add(1, 'year') // manipulate
dayjs().isBefore(dayjs()) // query
```