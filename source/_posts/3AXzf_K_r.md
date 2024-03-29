---
title: '分享几个浏览器的API接口(很实用)'
date: 2020-03-02 18:34:01
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
在日常开发中总是和各种 API 打交道，我们名为前端工程师实为 API 调用工程师。这篇文章我就分享 7 个有趣的 API，你若通过阅读这篇文章对前端增加一点点的乐趣，对我来说也是一种鼓励。这些API在不同的浏览器中兼容性也不一样，使用前请仔细审查。
<!-- more -->


### API使用示例

我们先看下其中几个API的实战效果图：
![](http://blog.mayiya.vip/post-images/1583145289329.gif)

### 1、监听屏幕旋转变化接口: orientationchange

定义：

这个 API 可以将你手机是否横屏的情况暴露给需要知道的人知道。

使用：
```javascript
screenOrientation: function(){
 let self = this;
 let orientation = screen.orientation || screen.mozOrientation || screen.msOrientation;
 window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", function() {
 self.angle = orientation.angle;
 });
}
```
![](http://blog.mayiya.vip/post-images/1583145345588.jpeg)
如果只是样式的改变也可以通过媒体查询来监听：
```css
/* 竖屏 */
@media screen and (orientation: portrait) {
 // some css code
}
/* 横屏 */
@media screen and (orientation: landscape) {
 // some css code
}
```

### 2、电池状态：navigator.getBattery()

定义：

这个 API 可以将你手机电池状态的情况暴露给需要知道的人知道。

这个 api 返回的是一个 promise 对象，会给出一个 BatteryManager 对象，对象中包含了以下信息：
![](http://blog.mayiya.vip/post-images/1583145416901.jpeg)

使用：
```javascript
getBatteryInfo: function(){
 let self = this;
 if(navigator.getBattery){
 navigator.getBattery().then(function(battery) {
 // 判断是否在充电
 self.batteryInfo = battery.charging ? `在充电 : 剩余 ${battery.level * 100}%` : `没充电 : 剩余 ${battery.level * 100}%`;
 // 电池充电状态改变事件
 battery.addEventListener('chargingchange', function(){
 self.batteryInfo = battery.charging ? `在充电 : 剩余 ${battery.level * 100}%` : `没充电 : 剩余 ${battery.level * 100}%`;
 });
 });
 }else{
 self.batteryInfo = '不支持电池状态接口';
 }
}
```

### 3、让你的手机震动: window.navigator.vibrate(200)

定义：

这个 API 可以让你的手机按你的想法震动。

使用：

震动效果会在很多游戏使用。比如欢乐斗地主中，地主打完王炸后手机都会有震动的效果，以此来表达地主嘚瑟的心情也很是合理。

示例代码如下：
```javascript
vibrateFun: function(){
 let self = this;
 if( navigator.vibrate ){
 navigator.vibrate([500, 500, 500, 500, 500, 500, 500, 500, 500, 500]);
 }else{
 self.vibrateInfo = "您的设备不支持震动";
 }
 <!--
 // 清除震动 
 navigator.vibrate(0);
 // 持续震动
 setInterval(function() {
 navigator.vibrate(200);
 }, 500);
 -->
}
```

### 4、当前语言：navigator.language

定义：

这个 API 可以告诉你当前应该使用什么语言。

如果你需要和我一样做多语言适配，这个 API 或许可以给你提供一个不错的思路。

使用：
![](http://blog.mayiya.vip/post-images/1583145501908.jpeg)

不同浏览器返回的值稍微有点差异。你可以通过以下封装好的方法来消除这种差异：
```javascript
function getThisLang(){
 const langList = ['cn','hk','tw','en','fr'];
 const langListLen = langList.length;
 const thisLang = (navigator.language || navigator.browserLanguage).toLowerCase();
 for( let i = 0; i < langListLen; i++ ){
 let lang = langList[i];
 if(thisLang.includes(lang)){
 return lang
 }else {
 return 'en'
 }
 }
}
```
不同的语言就对应不同的语言文案就好。

### 5、联网状态：navigator.onLine

定义：

这个 API 可以告诉让你知道你的设备的网络状态是否连接着。

使用：

比如我上午在看一篇文章，可是没看完就到了吃饭的点，这么热爱学习的我肯定是选择吃完午饭回来继续看。

30 分钟过后……

吃晚饭回到公司，打开电脑继续把那篇文章看完，看完了打算点了赞，发现给了这个提示：
![](http://blog.mayiya.vip/post-images/1583145557505.jpeg)

这个提示让我有点懵（就是没网络了）。

这个时候我们就可以使用这个 API，这样就可以准确的告诉用户“您的网络无法连接，请检查”。这样用户是不是可以有更好的体验呢？

代码如下：
```javascript
mounted(){
 let self = this;
 window.addEventListener('online', self.updateOnlineStatus, true);
 window.addEventListener('offline', self.updateOnlineStatus, true);
},
methods: {
 updateOnlineStatus: function(){
 var self = this;
 self.onLineInfo = navigator.onLine ? "online" : "offline";
 },
}
```

注意：navigator.onLine 只会在机器未连接到局域网或路由器时返回 false，其他情况下均返回 true。也就是说，机器连接上路由器后，即使这个路由器没联通网络，navigator.onLine 仍然返回 true。

### 6、浏览器活跃窗口监听: window.onblur & window.onfocus

定义：

这两个 api 分别表示窗口失去焦点和窗口处于活跃状态。

浏览其他窗口、浏览器最小化、点击其他程序等， window.onblur 事件就会触发;

回到该窗口， window.onfocus 事件就会触发。

使用：
![](http://blog.mayiya.vip/post-images/1583145615165.gif)
上面的截图是微信网页版的消息提示。代码很简单：
```javascript
mounted(){
 let self = this;
 window.addEventListener('blur', self.doFlashTitle, true);
 window.addEventListener('focus', function () {
 clearInterval(self.timer);
 document.title = '微信网页版';
 }, true);
},
methods: {
 doFlashTitle: function(){
 var self = this;
 self.timer = setInterval( () => {
 if (!self.flashFlag) {
 document.title = "微信网页版";
 } else {
 document.title = `微信（${self.infoNum}）`;
 }
 self.flashFlag = ! self.flashFlag
 }, 500)
 },
}
```

### 7、全屏 API（Fullscreen API）

定义：

这个 API 可以使你所打开的页面全屏展示，没有其他页面外的内容展示在屏幕上。

使用：

Element.requestFullscreen() 方法用于发出异步请求使元素进入全屏模式。

调用此 API 并不能保证元素一定能够进入全屏模式。如果元素被允许进入全屏幕模式，document 对象会收到一个 fullscreenchange 事件，通知调用者当前元素已经进入全屏模式。如果全屏请求不被许可，则会收到一个 fullscreenerror 事件。

当进入/退出全屏模式时,会触发 fullscreenchange 事件。你可以在监听这个事件，做你想做的事。
```javascript
fullScreenFun: function(){
 let self = this;
 var fullscreenEnabled = document.fullscreenEnabled ||
 document.mozFullScreenEnabled ||
 document.webkitFullscreenEnabled ||
 document.msFullscreenEnabled;
 if (fullscreenEnabled) {
 let de = document.documentElement;
 if(self.fullScreenInfo === '打开全屏'){
 if( de.requestFullscreen ){
 de.requestFullscreen();
 }else if( de.mozRequestFullScreen ){
 de.mozRequestFullScreen();
 }else if( de.webkitRequestFullScreen ){
 de.webkitRequestFullScreen();
 }
 self.fullScreenInfo = '退出全屏'
 } else {
 if( document.exitFullscreen ){
 document.exitFullscreen();
 }else if( document.mozCancelFullScreen ){
 document.mozCancelFullScreen();
 }else if( document.webkitCancelFullScreen ){
 document.webkitCancelFullScreen();
 }
 self.fullScreenInfo = '打开全屏'
 }
 } else {
 self.fullScreenInfo = '浏览器当前不能全屏';
 }
}
```

相关：

document.fullscreenElement: 当前处于全屏状态的元素 element
document.fullscreenEnabled: 标记 fullscreen 当前是否可用
document.exitFullscreen(): 退出全屏

### 小结

这篇文章很简单。我的目的是想介绍一下有趣的 API，来提高大家对前端的一点兴趣。

我认为这些 API 比较有趣，若你一直疲于业务，那么这些 API 或许可以给你不一样的感受。

---

[转载：沪漂程序员的生活史](https://www.toutiao.com/i6682529032929542669/)