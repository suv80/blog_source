---
title: '一些 JS 数组精简技巧，要记好笔记了'
date: 2020-03-02 18:45:24
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
数组是 JS 最常见的一种数据结构，咱们在开发中也经常用到,在这篇文章中,提供一些小技巧,帮助咱们提高开发效率。

### 删除数组的重复项
![](http://blog.mayiya.vip/post-images/1583145981932.jpeg)

### 替换数组中的特定值
有时在创建代码时需要替换数组中的特定值，有一种很好的简短方法可以做到这一点，咱们可以使用.splice(start、value to remove、valueToAdd)，这些参数指定咱们希望从哪里开始修改、修改多少个值和替换新值。
![](http://blog.mayiya.vip/post-images/1583146021103.jpeg)

### Array.from 达到 .map 的效果
咱们都知道 .map() 方法，.from() 方法也可以用来获得类似的效果且代码也很简洁。
![](http://blog.mayiya.vip/post-images/1583146048902.jpeg)

### 置空数组
有时候我们需要清空数组，一个快捷的方法就是直接让数组的 length 属性为 0，就可以清空数组了。
![](http://blog.mayiya.vip/post-images/1583146088365.jpeg)

### 将数组转换为对象
有时候，出于某种目的，需要将数组转化成对象，一个简单快速的方法是就使用展开运算符号(...):
![](http://blog.mayiya.vip/post-images/1583146115734.jpeg)

### 用数据填充数组
在某些情况下，当咱们创建一个数组并希望用一些数据来填充它，这时 .fill()方法可以帮助咱们。
![](http://blog.mayiya.vip/post-images/1583146137492.jpeg)

### 数组合并
使用展开操作符，也可以将多个数组合并起来。
![](http://blog.mayiya.vip/post-images/1583146163702.jpeg)

### 求两个数组的交集
求两个数组的交集在面试中也是有一定难度的正点，为了找到两个数组的交集，首先使用上面的方法确保所检查数组中的值不重复，接着使用.filter 方法和.includes方法。如下所示：
![](http://blog.mayiya.vip/post-images/1583146189180.jpeg)

### 从数组中删除虚值
在 JS 中，虚值有 false, 0，''， null, NaN, undefined。咱们可以 .filter() 方法来过滤这些虚值。
![](http://blog.mayiya.vip/post-images/1583146213974.jpeg)

### 从数组中获取随机值
有时我们需要从数组中随机选择一个值。一种方便的方法是可以根据数组长度获得一个随机索引，如下所示：
![](http://blog.mayiya.vip/post-images/1583146242679.jpeg)

### 反转数组
现在，咱们需要反转数组时，没有必要通过复杂的循环和函数来创建它，数组的 reverse 方法就可以做了：
![](http://blog.mayiya.vip/post-images/1583146276835.jpeg)

### lastIndexOf() 方法
![](http://blog.mayiya.vip/post-images/1583146295947.jpeg)

### 对数组中的所有值求和
JS 面试中也经常用 reduce 方法来巧妙的解决问题
![](http://blog.mayiya.vip/post-images/1583146320324.jpeg)

在本文中，介绍了13个技巧，希望它们可以帮助编写简洁代码，如果你还有更好的办法，欢迎留言讨论。

[原文：https://dev.to/duomly/13-useful-javascript-array-tips-and-tricks-you-should-know-2jfo]
(https://dev.to/duomly/13-useful-javascript-array-tips-and-tricks-you-should-know-2jfo)

---

[转载：前端小智](https://www.toutiao.com/i6797557368944067076/)