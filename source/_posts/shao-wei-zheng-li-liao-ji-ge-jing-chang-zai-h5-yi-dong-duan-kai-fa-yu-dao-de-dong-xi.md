---
title: '稍微整理了几个经常在H5移动端开发遇到的东西'
date: 2019-10-16 17:19:59
tags: [css,html]
published: true
hideInList: false
feature: /post-images/shao-wei-zheng-li-liao-ji-ge-jing-chang-zai-h5-yi-dong-duan-kai-fa-yu-dao-de-dong-xi.jpg
---
不用说我也知道，此类文章太多太多了，常见的譬如：

viewport、强制浏览器全屏、IOS的Web APP模式、可点击元素出现阴影（这个我觉得真没必要去掉，用户点击是需要反馈的，而这个背景色刚刚好提供了一种反馈）等等，太多啦，这些相信大家百度一下就可以查到很多资料

本篇文章主要是讲一些其他的或者优化手段。内容不多
<!-- more -->

### 弹出数字键盘

```html

<!-- 有"#" "*"符号输入 -->
<input type="tel">
<!-- 纯数字 -->
<input pattern="\d*">

```
安卓跟IOS的表现形式应该不一样，大家可以自己试试。当运用了正则pattern后，就不用关注input的类型了
![](http://lionney.coding.me//post-images/1571217719268.gif)
​
### 调用系统的某些功能

```html

<!-- 拨号 -->
<a href="tel:10086">打电话给: 10086</a>
<!-- 发送短信 -->
<a href="sms:10086">发短信给: 10086</a>
<!-- 发送邮件 -->
<a href="mailto:839626987@qq.com">发邮件给：839626987@qq.com</a>
<!-- 选择照片或者拍摄照片 -->
<input type="file" accept="image/*">
<!-- 选择视频或者拍摄视频 -->
<input type="file" accept="video/*">
<!-- 多选 -->
<input type="file" multiple>

```
同上‍♂️
![](http://lionney.coding.me//post-images/1571217870768.gif)
​

### 打开原生应用

```html

<a href="weixin://">打开微信</a>
<a href="alipays://">打开支付宝</a>
<a href="alipays://platformapi/startapp?saId=10000007">打开支付宝的扫一扫功能</a>
<a href="alipays://platformapi/startapp?appId=60000002">打开支付宝的蚂蚁森林</a>

```
这种方式叫做URL Scheme，是一种协议，一般用来访问APP或者APP中的某个功能/页面（如唤醒APP后打开指定页面或者使用某些功能）

URL Scheme的基本格式如下：

scheme://[path][?query]
scheme-应用标识
path-行为(应用的某个功能/页面) 
query-功能需要的参数

一般是由APP开发者自己定义，比如规定一些参数或者路径让其他开发者来访问，就像上面的例子

注意事项：

- 唤醒APP的条件是你的手机已经安装了该APP
- 某些浏览器会禁用此协议，比如微信内部浏览器（除非开了白名单）

​![](http://lionney.coding.me//post-images/1571218210599.gif)

后面应该会专门写一篇文章用来探讨唤醒APP

### 解决active伪类失效

```html

<body ontouchstart></body>

```

给`body`注册一个空事件即可

### 忽略自动识别

```html

<!-- 忽略浏览器自动识别数字为电话号码 -->
<meta name="format-detection" content="telephone=no">
<!-- 忽略浏览器自动识别邮箱账号 -->
<meta name="format-detection" content="email=no">

```

当页面上的内容包含了手机号/邮箱等，会自动转换成可点击的链接

比如你有如下代码：
```html

<p>13192733603</P>

```

但是有些浏览器会识别为手机，并且可以点击拨号

### 解决input失焦后页面没有回弹

![](http://lionney.coding.me//post-images/1571218337679.gif)
​
一般出现在IOS设备中的微信内部浏览器，出现的条件为：

- 页面高度过小
- 聚焦时，页面需要往上移动的时候

所以一般`input`在页面上方或者顶部都不会出现无法回弹

解决办法为，在聚焦时，获取当前滚动条高度，然后失焦时，赋值之前获取的高度：
```html

<template>
    <input type="text" @focus="focus" @blur="blur">
</template>
<script>
export default {
    data() {
        return {
            scrollTop: 0
        }
    },
 
    methods: {
        focus() {
            this.scrollTop = document.scrollingElement.scrollTop;
        },
 
        blur() {
            document.scrollingElement.scrollTo(0, this.scrollTop);
        }
    }
}
</script>

```

### 禁止长按

![](http://lionney.coding.me//post-images/1571218468138.gif)
​
以上行为可以总结成这几个（每个手机以及浏览器的表现形式不一样）：长按图片保存、长按选择文字、长按链接/手机号/邮箱时呼出菜单。

想要禁止这些浏览器的默认行为，可以使用以下CSS：
```css

// 禁止长按图片保存
img {
    -webkit-touch-callout: none;
    pointer-events: none; // 像微信浏览器还是无法禁止，加上这行样式即可
}
// 禁止长按选择文字
div {
    -webkit-user-select: none;
}
// 禁止长按呼出菜单
div {
    -webkit-touch-callout: none;
}

```

### 滑动不顺畅，粘手

一般出现在IOS设备中，自定义盒子使用了`overflow: auto || scroll`后出现的情况。

优化代码：
```css

div {
    -webkit-overflow-scrolling: touch;
}

```

### 屏幕旋转为横屏时，字体大小会变

具体出现的情况不明，有时候有有时候没有，欢迎指出。

优化代码：
```css

* {
    -webkit-text-size-adjust: 100%;
}

```

### 最简单的rem自适应

大家都知道，rem的值是根据根元素的字体大小相对计算的，但是我们每个设备的大小不一样，所以根元素的字体大小要动态设置
```html

html {
    font-size: calc(100vw / 3.75);
}
body {
    font-size: .14rem;
}

```
效果如下：
![](http://lionney.coding.me//post-images/1571218644031.gif)
​
像我一般的话，直接搞lib-flexible、postcss-pxtorem就完事啦！

### 滑动穿透

当你想在出现遮罩的时候，锁住用户的滚动行为，你可以这么做。

假设HTML结构如下：
```html

<div class="mask">
    <div class="content">我是弹框</div>
</div>

```

CSS样式如下：
```css

.mask {
    position: fixed;
    top: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    background-color: rgba($color: #333, $alpha: .6);
        .content {
            padding: 20px;
            background-color: #fff;
            width: 300px;
        }
}

```

效果如下：
![](http://lionney.coding.me//post-images/1571218869210.gif)
​
可以看到，当在遮罩上滑动的时候，是会穿透到父节点的，最简单的办法就是阻住默认行为：
```javascript

document.querySelector(".mask").addEventListener("touchmove", event => {
    event.preventDefault();
});

```
如果在vue中，你可以这么写：
```html

<div class="mask" @touchumove.prevent></div>

```
如果.content也有滚动条，那么只要阻止遮罩本身就行：
```javascript

document.querySelector(".mask").addEventListener("touchmove", event => {
    if (event.target.classList.contains("mask")) event.preventDefault();
});

```
或者：
```html

<div class="mask" @touchumove.self.prevent></div>

```
这样，当出现遮罩的时候用户的滑动就会被锁住啦

### 最后

像1px边框、唤醒APP后面会单独出文章，因为涉及的内容比较多。

文章是有点水，请轻喷‍♂️

如果你觉得这篇文章不错，请别忘记点个赞跟关注哦~

---
作者：聪明的汤姆
链接：[https://juejin.im/post/5d6e1899e51d453b1e478b29](https://juejin.im/post/5d6e1899e51d453b1e478b29)
来源：掘金