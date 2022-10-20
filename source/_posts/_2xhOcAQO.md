---
title: '透明背景图片'
date: 2021-10-19 14:37:45
tags: [片断,css,html]
published: true
hideInList: false
feature: 
isTop: false
---
CSS 中没有background-opacity属性，但是您可以通过插入一个具有常规不透明度的伪元素来伪造它，它与后面元素的确切大小相同。

```css
div {
  width: 200px;
  height: 200px;
  display: block;
  position: relative;
}

div::after {
  content: "";
  background: url(image.jpg);
  opacity: 0.5;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  position: absolute;
  z-index: -1;   
}
```

**Q：此代码段的浏览器支持是什么？**

A：Firefox 3.5+, Opera 10+, Safari 4+, Chrome 4+, IE 9+

**Q：有什么技巧可以在 IE 7 和 8 上使用它吗？**

A：filter: alpha(opacity=40); /* For IE8 and earlier */

**Q：这很好用，但我无法选择/单击“顶部”div 中的文本。有人找到了解决方法吗？ **

A：你不能点击里面很可能是因为你有多个div在里面。使用div类的解决方案见下面的代码：

```css
div.transparentBox {
  width: 500px;
	height: 350px;
	display: block;
	position: fixed;
	top: 30%;
	left: 30%;
}

div.transparentBox::after {
	content: “”;
	background-color: #ffffff;
	opacity: 0.5;
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	position: absolute;
	z-index: -1;
}
```

A：你也可以用这样的东西来欺骗它：

```css
div * {
  z-index: 0;
}
```


