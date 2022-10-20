---
title: '关闭数字输入微调器'
date: 2021-10-19 14:36:02
tags: [片断,input,css,html]
published: true
hideInList: false
feature: 
isTop: false
---
WebKit桌面浏览器向数字输入添加了向上向下箭头的微调器。您可以像这样隐藏它们：

```css
input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  -webkit-appearance: none; 
  margin: 0; 
}
```

请注意，一些其他功能仍然存在，例如能够通过鼠标滚轮增加数字。

**Q：当我们将鼠标光标放在焦点元素上并滚动鼠标滚轮时，我们可以停止修改值吗？如果是，请提供解决方案。**

A：

```javascript
$(':input[type=number]').on('mousewheel', function(e){
    e.preventDefault();
});
```

**Q：有针对 Firefox 29 的解决方案吗？**

A：好的，这是firefox 29的解决方案：

```css
input[type=number] {
  -moz-appearance: textfield;
}
```

**Q：有没有办法实现有的有微调器，有的没有微调器的情况？ 我正在尝试用类名标记某些输入框。**

A：如果您希望某些字段有微调器而其他字段没有，因为` step="0"` 对于微调器来说是无意义的，您可以这样做：

```css
input[step="0"]::-webkit-inner-spin-button { 
  -webkit-appearance: none;
}
```




