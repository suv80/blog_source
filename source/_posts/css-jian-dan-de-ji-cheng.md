---
title: 'CSS简单的继承'
date: 2019-11-15 16:58:16
tags: [css]
published: true
hideInList: false
feature: /post-images/css-jian-dan-de-ji-cheng.jpg
---
也许你瞧不起以前的 css ，但是你不该再轻视眼下的 css 。近年来 css 的变量系统已逐步得到各大浏览器厂商支持，自定义选择器等强势袭来，嵌套系统/模块系统也在路上...为了更好的掌握 css 这门语言，很有必要把之前零零散散的 css 知识回炉重造下。

<!-- more -->

css 作为一门语言而，也有其继承原理，虽然简单，你却未必掌握。

### 属性的是否默认继承

初始值是指当属性没有指定值时的默认值，如这些语句的值都是默认值：`background-color: transparent`、`left: auto` 、`float: none`、`width: auto` 等。

css 的继承很简单，分为默认继承的和默认不继承的，但所有属性都可以通过设置 `inherit` 实现继承。

当没有指定值时，默认继承的属性取父元素的同属性的计算值（相当于设置了 `inherit` ），默认不继承的属性取属性的初始值（时相当于设置了 `initial` ）。

#### 默认继承的 ("Inherited: Yes") 的属性：

- 所有元素默认继承：visibility、cursor
- 内联元素默认继承：letter-spacing、word-spacing、white-space、line-height、color、font、 font-family、font-size、font-style、font-variant、font-weight、text- decoration、text-transform、direction
- 块状元素默认继承：text-indent、text-align
- 列表元素默认继承：list-style、list-style-type、list-style-position、list-style-image
- 表格元素默认继承：border-collapse

#### 默认不继承的("Inherited: No") 的属性：

- 文本属性默认不继承：vertical-align、text-decoration、text-shadow、white-space、unicode-bidi
- 盒子属性默认不继承：display、width、height、padding、margin、border、min-width、min-height、max-width、max-height、overflow、clip
- 背景属性默认不继承：background、background-color、background-image、background-repeat、background-position、background-attachment
- 定位属性默认不继承：float、clear、position、top、right、bottom、left、z-index
- 内容属性默认不继承：content、counter-reset、counter-increment
- 轮廓属性默认不继承：outline-style、outline-width、outline-color、outline
- 页面属性默认不继承：size、page-break-before、page-break-after
- 声音属性默认不继承：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

可以看到涉及到文本相关的属性，都是默认继承的，毕竟 css 设计之初就是为了更好的在网页上呈现文字。

需要注意的是，部分属性的默认值是会根据元素的 `display` 属性的值而计算的，比如 `vertical-align` 属性，如果是 `display: inline` 元素，则其计算值为基线对齐 `vertical-align: baseline` ，如果是 `display: inline-block` 元素，则其计算值为 `vertical-align: bottom` 。详情请参考 [这篇文章](https://juejin.im/post/5ce607a7e51d454f6f16eb3d#heading-18)。

### 通用属性值 initial、inherit 和 unset

css 为控制继承提供了四个特殊的通用属性值（属性 `revert` 只有很少的浏览器支持，所以实际上是三个），每个 css 属性都能使用这些值。

- inherit

  设置该属性会使子元素属性和父元素相同。实际上，就是“开启继承”。

- initial

  将属性的初始值应用于元素。实际上，就是“重置为默认值”。

- unset

  将属性重置为自然值，也就是如果属性是自然继承的那么就是 `inherit` ，否则和 `initial` 一样。

#### 实例

这些通用属性值可以有很多妙用，举个栗子：

**1.利用 `inherit` 实现如下图片倒影：**

![](http://lionney.coding.me//post-images/1573808451348.jpg)

```css
div::after {
    content: "";
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    bottom: -100%;
    background-image: inherit; // 背景图片继承，这一般人想不到吧...
    transform: rotateX(180deg);
}
```

**2.利用 `initial` 重置 `left` 为默认值 `auto`：**

```css
div {
  position: absolute;
  left: 20px;
  top: 20px;
}
div + div {
  left: initial;
  right: 20px;
}
```

![](http://lionney.coding.me//post-images/1573808467846.jpg)

例子中 `div` 设置过了 `left` ，div2 的 `right` 若要生效，须将 `left` 重置为初始值 `initial` （或者 `unset` ）。

**3.利用 `unset` 将属性重置为未设置之前的值：**

![](http://lionney.coding.me//post-images/1573808478837.jpg)

例子中 `p` 标签的 `color` 是默认继承属性，所以此时 `color: unset` 相当于 `color: inherit` 。 `p` 标签的 `border` 属性是默认不继承属性，所以此时 `border: unset` 相当于 `border: initial` 。

```css
.unset {
  border: unset;
  color: unset;
}
```

### 总结

css 的继承真的很简单，只需要记住那些默认继承的，剩下的都是默认不继承的。而默认继承的元素除了文本相关的哪些，只有 `visibility、cursor` 比较常用了，也是比较容易记得的。

### 后记

本文确确实实参考了[这篇文章](https://github.com/chokcoco/iCSS/issues/13)，但是我在看原文之时确确实实也是没有完全搞明白 css 的继承到底是什么样的，所以我花了很多时间查了很多文档来整理思路，最后才明白了 css 的继承就是这么简单的分为默认继承和默认不继承两种而已。很抱歉一开始没有附上原文链接，但是原文有60个章节，我只是摘了其中一章的知识点来重新梳理，来让自己更加清晰并让大家更容易理解 css 的继承。且我本身是为了完成耗子叔的 `ARTS` 打卡计划而写这篇文章的，我掘金上发布的最近两篇文章也是，都有抄袭的痕迹，这没办法我本身水平不咋地如果完全靠自己输出根本写不出来什么文章，但我坚信把别人的东西完全消化了变成自己的，也是一种非常有效的学习方法。

我年前这几个月被996了，时间少的可怜，能够挤出来一点写篇文章甚是不易，我当然希望掘友们能够认可。尽管是拿来主义，但我会对内容进行部分总结加工，让他更加宜懂，更有针对性，更容易记忆。我最近打算把我的 css 知识回炉重造下，所以未来几篇文章肯定都是关于 css 的并且会有几篇出自 [这里](https://github.com/chokcoco/iCSS)，我也会把 《css揭秘》、《css选择器世界》看完并写出自己的总结篇。如果人人都那么爱读书，那么我写的文章肯定没人看。但事实上并不是。所以我的文章目前是写给那些没有看过那么多书的读者，我也真心的希望能够在我自身进步的同时，帮助大家一起进步，帮助大家更好的学习。

***

作者：[幻灵尔依](https://juejin.im/post/5dcb89186fb9a04a752ba034)
来源：掘金
著作权归作者所有。