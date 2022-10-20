---
title: 认识下浏览器的5种观察器API
date: 2022-06-17 11:22:36
tags: [js,html]
---

## 前言

”图片懒加载“，这个词语想必大家再熟悉不过了。传统的实现方法是，监听scroll事件，获取img元素相对于视口的顶点位置`el.getBoundingClientRect().top`，只要这个值小于浏览器的高度`window.innerHeight`就说明进入可视区域，当图片进入可视区域时再加载图片资源。

这种方法的缺点是，由于scroll事件密集发生，计算量很大，容易造成性能问题。

目前浏览器API中的 IntersectionObserver 交叉观察器，可自动"观察"目标元素与根元素的交叉区域的变化，以此判断元素是否可见。利用这个方法，在观察到元素可见时，再去加载图片资源。这样“图片懒加载”实现起来就很容易了。

当然浏览器的观察器，不仅IntersectionObserver这一种。下面我们依次介绍下浏览器5种观察器的基本用法，以及它们的应用。

首先来看一下，什么是浏览器的观察器？

## 一、什么是浏览器的观察器？

针对一些不是由用户直接触发的事件，比如DOM元素从不可见到可见、DOM大小、属性的改变和子节点个数的修改等，浏览器提供特定的api去监控这些变化，这些api就是浏览器的观察器。

## 二、浏览器的观察器有哪些？

浏览器的观察器共有 5 种 ：IntersectionObserver（交叉观察器）、MutationObserver（变化观察器）、ResizeObserver（大小观察器）、PerformanceObserver（性能观察器）、ReportingObserver（报告观察器） 。

##### 2.1 IntersectionObserver 交叉观察器

该观察器自动"观察"目标元素与根元素交叉区域的变化。默认根元素为文档视口，此时交叉区域的变化决定了用户在当前视口能否看到目标元素，因此它经常被用于“元素可见性”观察。比如：图片懒加载、无限滚动、广告曝光量统计等。

![图片](https://imgbed.iiba.fun/blog/640-20220617113235990.gif)

![图片](https://imgbed.iiba.fun/blog/640-20220617112359734.gif)

上图中，目标元素粉色方块不仅会随着窗口滚动，还会在容器Box1里面滚动，目标元素与视口(或指定的根元素)产生的交叉区域会不断变化。我们将这个交叉区域占目标元素的比例，称为目标元素的交叉比例intersectionRatio。

注意：根元素为视口时（上图），交叉比例大于0，即元素可见，交叉比例等于0，即元素不可见。指定其他元素为根元素时（下图），根元素必须是目标元素的祖先节点，此时交叉比例大于0不一定代表元素在当前视口可见。

### 2.1.1 基本用法

通过`new IntersectionObserver(callback[, options])` 创建观察器实例 observer,并按照options配置，指定根元素root、根元素的外边距rootMargin、执行callback的交叉比例的阈值threshold。

```javascript
let options = { //配置observer实例的对象
    // root: document.querySelector('#parentBox'), // 指定根元素，必须是目标元素的父级元素; 默认：文档视口
    // rootMargin: "0px 0px 0px 0px", //根元素的外边距。类似于 CSS 中的 margin 属性。默认值是"0px 0px 0px 0px",分别表示 top、right、bottom 和 left 四个方向的值，用来扩展或缩小rootBounds这个矩形的大小，从而影响intersectionRect交叉区域的大小。
    threshold: [0] //目标元素和根元素相交部分的比例达到该值的时候，callback 函数将会被执行，eg: 1 、[0.5 , 1],当为数组时每达到该值都会执行 callback 函数。默认值为[0]。
}
let observer = new IntersectionObserver(callback, options);
```

定义观察到目标元素与根元素交叉区域变化时的回调函数`callback（entries, observer）`。entries数组中,每个成员都是一个IntersectionObserverEntry对象,如果同时有两个被观察的对象的可见性发生变化，entries数组就会有两个成员。一般会触发两次callback。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）。

```javascript
let callback = (entries, observer) => { 
    entries.forEach(entry => {
        consloe.log(entry) //包含目标元素的信息的对象
        // entry.time：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
        // entry.target：被观察的目标元素，是一个 DOM 节点对象
        // entry.rootBounds：根元素的矩形区域的信息，getBoundingClientRect()方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回null
        // entry.boundingClientRect：目标元素的矩形区域的信息
        // entry.intersectionRect：目标元素与视口（或根元素）的交叉区域的信息
        // entry.intersectionRatio：根和目标元素的交叉区域的比例值，即intersectionRect占boundingClientRect的比例，0 为完全不可见，1 为完全可见
        // entry.isIntersecting：true表示从不可视状态变为可视状态。false表示从可视状态到不可视状态：false
    });
};
```

`observer.observe(targetNode)` 指定目标元素 targetNode1、targetNode2，开始观察。

```javascript
//observe的参数是一个 DOM 节点对象。如果要观察多个节点，就要多次调用这个方法。
observer.observe(targetNode1);
observer.observe(targetNode2); //开始观察目标元素。
// observer.disconnect(); //关闭观察器。
// observer.takeRecords(); //返回所有观察目标对象数组。
// observer.unobserve(targetNode1); //停止观察特定目标元素。
```

### 2.1.2 实例：图片懒加载

创建交叉观察器，通过observe为所有的图片资源img开启了交叉观察，当某个图片资源，从不可视状态变为可视状态时，便添加图片的src属性，从而引发图片资源的加载。

```javascript
const observer = new IntersectionObserver((entries, observer) => { 
    entries.forEach(entry => {
        if (entry.isIntersecting) { //true表示从不可视状态变为可视状态
            let img = entry.target;
            img.setAttribute('src', img.getAttribute('data-src')) 
            observer.unobserve(img); // 停止观察已开始加载的图片 
        } 
    }) 
}, {}); 
Array.from(document.querySelectorAll('img')).forEach((item) => {
  observer.observe(item)  //观察所有图片资源，开始观察item
});
```

### 2.1.3 实例：无限滚动

无限滚动时，在页面底部加一个footerSentinel元素。一旦footerSentinel可见，就表示页面滚动到了底部，从而加载新的条目放在footerSentinel前面。

```javascript
const observer = new IntersectionObserver((entries, observer) => { 
    entries.forEach(entry => {
        if (entry.intersectionRatio <= 0) return; // 如果不可见，就返回
        let newData = [1,2,3,4,5];
        appendChildBeforeFooter(newData); //新数据追加在footerSentinel之前
    }) 
});

// 开始观察
observer.observe(
  document.querySelector('.footerSentinel')
);
```

### 2.1.4 实例：网页广告的曝光量统计

很多时候，广告图片不一定需要全部展示才算被用户看到，有时候图片只展示了60%时，主要信息已经被用户看到，这种情况其实是可以算作一次曝光量的统计。为了实现这种广告的曝光量的精确统计，我们可以创建交叉管理器，观察到广告目标元素的交叉比例intersectionRatio达到0.6时，判定广告的曝光量+1

```javascript
 const intersectionObserver = new IntersectionObserver((entries)=> {
     entries.forEach(entry => {
         if(entry.intersectionRatio > 0){
            console.log('info:');
            console.log('广告位元素和可视区域相交部分的比例：'+entry.intersectionRatio + ',广告曝光量➕1')
            intersectionObserver.unobserve(document.querySelector('.DemoIntersectionObserver .ad'))
         }
     })
 },{ 
     threshold: 0.6,
 });
intersectionObserver.observe(document.querySelector('.DemoIntersectionObserver .ad'));
```

看一下实际效果

![图片](https://imgbed.iiba.fun/blog/640-20220617112401657.gif)

### 2.2 MutationObserver 变化观察器

该观察器"观察"目标元素属性和子节点的变化。目标元素DOM发生变动就会触发观察器的回调函数。注意：异步触发，DOM的变动并不会马上触发，而是要等到当前所有DOM操作都结束才触发。

### 2.2.1 基本用法

定义观察到目标元素的特定变动时的回调函数`callback（mutationList, observer）`回调函数，mutationList为包含目标元素DOM变化相关信息的对象的数组，数组中,每个成员都是一个MutationRecord对象

```javascript
let callback = (mutationList, observer)  => { 
    mutationList.forEach((mutation) => {
    console.log(mutation.target) //发生变动的DOM节点
    console.log(mutation.previousSibling) //前一个同级节点，如果没有则返回null。
    console.log(mutation.nextSibling) //下一个同级节点，如果没有则返回null。
    switch(mutation.type) {//目标元素变化类型'childList' || 'attributes' | 'characterData'
        case 'childList':
            console.log(mutation.addedNodes) //新增的 DOM 节点
            console.log(mutation.removedNodes) //删除的 DOM 节点
            break;
        case 'attributes':
            console.log(mutation.attributeName) //被更改的属性名称，如果设置了attributeFilter，则只返回预先指定的属性。
            console.log(mutation.oldValue) //该属性之前的值，这个属性只对attribute和characterData变动有效，如果发生childList变动，则返回null。
            break;
        }
    });
};
```

通过`new MutationObserver(callback)` 创建观察器实例 observer

```javascript
let observer = new MutationObserver(callback);
```

`observer.observe(targetNode[, options])`， 按照options配置指定要观察的特定变动，并开始观察目标元素 targetNode。其中：childList，attributes、characterData 三个DOM变动类型的属性之中，至少有一个必须为true，若均未指定将报错。

```javascript
let targetNode = document.querySelector("#someElement");
let options = {
    childList: true,  //  DOM 变动类型：是否观察目标子节点添加或删除，默认为false。
    attributes: true, //  DOM 变动类型：是否观察目标节点属性变动，默认为false。
    characterData: false, //  DOM 变动类型：是否观察文本节点变化。无默认值
    subtree: true, // 是否观察后代节点，默认为false。
    //注意：childList，attributes、characterData 三个属性之中，至少有一个必须为 true
    // attributeOldValue: true, //表示观察attributes变动时，是否需要记录变动前的属性值。
    // characterDataOldValue: true, //表示观察characterData变动时，是否需要记录变动前的值。
    // attributeFilter: ['class','src'], //数组，表示需要观察的特定属性（比如['class','src']）。
}
observer.observe(targetNode, options); //开始观察目标元素，按照options配置指定所要观察的特定变动。
// observer.disconnect(); //停止观察。
// observer.takeRecords(); //返回所有观察目标对象数组。
```

### 2.3 ResizeObserver 大小观察器 （实验）

该观察器"观察"Element内容区域的改变或SVGElement的边界框的改变,每次元素内容或边框的大小变化时都会向观察者传递通知。

### 2.3.1 基本用法

定义观察到目标元素的大小变化的回调函数`callback（entries, observer）`，entries为包含目标元素大小变化的相关信息的数组，数组中,每个成员都是一个ResizeObserverEntry对象

```javascript
let callback = (entries, observer) => {
    entries.forEach(entry => {
        console.log('当前大小', `${entry.contentRect.width} x ${entry.contentRect.height}`)
        /**
         * entry.target ：目标元素
         * entry.borderBoxSize: 包含目标元素的新边框框大小的对象
         * entry.contentBoxSize: 包含目标元素的新内容框大小的对象
         * entry.contentRect: 包含目标元素的新大小的对象
         * entry.devicePixelContentBoxSize 包含目标元素以设备像素为单位的新内容框大小的对象
         * */
    });
}
```

通过`new ResizeObserver(callback)` 创建观察器实例 observer

```javascript
let observer = new ResizeObserver(callback);
```

`observer.observe(targetNode)`; 开始观察目标元素 targetNode。

```javascript
let targetNode = document.querySelector("#someElement");
observer.observe(targetNode);//开始观察目标元素
// observer.disconnect(); //停止观察。
// observer.takeRecords(targetNode); //停止观察目标元素。
```

### 2.4 PerformanceObserver 性能观察器

该观察器用于“观察”记录 performance 数据的行为，一旦记录了就会触发回调，可以在回调里上报这些性能相关的数据。

> 浏览器API performance 用于记录一些时间点、某个时间段、资源加载的耗时等；附上：performance详细用法

### 2.4.1 基本用法

定义观察到目标元素的特定变动时的回调函数`callback（list, observer）`。`list.getEntries()`为包含options中指定的相关performance数据的对象的数组,每个成员都是一个PerformanceEntry对象

```javascript
let callback = (list, observer) => {
    list.getEntries().forEach(entry => {
        console.log(entry); //entry为按startTime排序的performance上报的数据对象，自动根据所请求资源的变化而改变，也可以用mark(),measure()方法自定义添加
        /**
         * entry.name：资源名称，是资源的绝对路径或调用mark方法自定义的名称
         * entry.entryType：资源类型，entryType类型不同数组中的对象结构也不同
         * entry.startTime:开始时间
         * entry.duration：加载时间
         * entry.entryType == 'paint' && entry.name == 'first-paint':'首次绘制，绘制Body',
         * entry.entryType == 'paint' && entry.name == 'first-contentful-paint':'首次有内容的绘制，第一个dom元素绘制完成',
         * entry.entryType == 'paint' && entry.name == 'first-meaningful-paint':'首次有意义的绘制',
        */
    });
}
```

通过`new PerformanceObserver(callback)` 创建观察器实例 observer

```javascript
const observer = new PerformanceObserver(callback)
```

`observer.observe(options)`, 按照options配置，指定所要观察的performance数据相关变化。

```javascript
let options = {
    entryTypes:[// 类型为string[],必填，且数组不能为空，数组中某个字符串取的值无效，浏览器会自动忽略它
        'longtask', // 长任务 （>50ms）
        'frame', // 帧的变化，常用于动画监听，使用时注意兼容
        'navigation', // 页面加载||刷新||重定向
        'resource', // 资源加载
        'mark',//  自定义记录的某个时间点
        'measure',//  自定义记录的某个时间段
        'paint'//  浏览器绘制
    ]
};
observer.observe(options); //当记录的性能指标在指定的 entryTypes 之中时，将调用性能观察器的回调函数。
// observer.disconnect(); //阻止性能观察器接收任何性能指标事件。
// observer.takeRecords(); //返回存储在性能观察器中的性能指标的列表，并将其清空。
```

### 2.4.2 实例：“小鸡仔的一生”

下面我们通过”小鸡仔的一生“来看一下MutationObserver、ResizeObserver、PerformanceObserver的使用。

首先，它是只会下蛋的母鸡，但鸡蛋时而被偷。且现在疫情肆虐，小鸡可能会发烧生病，需随时关注小鸡的健康状态，及时收鸡蛋。等到小鸡长大“成熟”，给它卖掉。并记录下顾客看到小鸡的商品图到真正下单的间隔时长。

首先给小鸡搭建好“笼子”，把“小鸡”和“鸡蛋”放进去

```html
<div class="Demo">
    <div class="chicken normal">
        <img src='./image/chicken.jpeg' alt="" />
        <div class="egg"></div>
    </div>
</div>
```

创建MutationObserver观察器，观察小鸡的变化，观察到它的“健康属性”className变化和“鸡蛋”（子节点）数量变化时，分别“提醒”小鸡健康状态和鸡蛋个数变化如下

```javascript
const mutationObserver = new MutationObserver((mutationsList) => {
   mutationsList.forEach((mutation) => {
       switch(mutation.type) {
         case 'childList':
             if(mutation.addedNodes.length>0){console.log('小鸡下蛋了') }
             else if(mutation.removedNodes.length>0){ console.log('鸡蛋被偷了1个') }
             break;
         case 'attributes':
           if(mutation.target.className.indexOf('hot')>-1){ console.log('小鸡发烧了') }
           else{ console.log('小鸡健康') }
           break;
       }
   });
});
mutationObserver.observe(document.querySelector('.Demo .chicken'),{childList: true,attributes: true,});
```

创建ResizeObserver观察器，观察小鸡的大小的变化，当小鸡“成熟”时，将小鸡“卖出”

```javascript
const resizeObserver = new ResizeObserver(entries => {
    entries.forEach(entry => {
        if(entry.contentRect.width > 200){
            console.log('小鸡卖出！当前大小', `${entry.contentRect.width} x ${entry.contentRect.height}`)
        }
        else {
            console.log('小鸡当前大小', `${entry.contentRect.width} x ${entry.contentRect.height}`)
        }
    });
});
resizeObserver.observe(document.querySelector(".Demo .chicken img"));
```

创建定时器，随机更新小鸡的健康状态和鸡蛋个数, 并随机更新小鸡的大小

```javascript
let timer = setInterval(() => {
   let random = Math.ceil(Math.random() * 4);
   targetNodeImg?.setAttribute('style',`width:${targetNodeImg.offsetWidth*1.2}px`)
   switch (random) {
       case 1: targetNodes.className="chicken hot";break;
       case 2: targetNodes.className="chicken";break;
       case 3: const dom = document.createElement('div'); dom.className = 'egg'; targetNodes.appendChild(dom);targetNodes.appendChild(dom.cloneNode());break;
       case 4: document.querySelectorAll('.egg')[0]&&document.querySelectorAll('.egg')[0].remove(); break;
       default:break;
   }
},2000);
```

小鸡已经“成熟”，停止观察，并清空定时器，然后展示小鸡的商品页

```javascript
setTimeout(()=>{
    clearInterval(timer)
    mutationObserver.disconnect();
    resizeObserver.disconnect();
    //此时，小鸡已经“成熟”，展示商品页信息
},16000)
```

小鸡的商品页，初始化查看商品等方法。查看商品时，记录一个时间点Start-Mark，下单时，记录一个时间点End-Mark，上报时间Start-End-Send为End-Mark-Start-Mark。

```javascript
function startMark() { performance.mark('Start-Mark') } //查看商品
function endMark() { performance.mark('End-Mark') } //下单
function measureClick() { performance.measure('Start-End-Send','Start-Mark','End-Mark'); } //上报点击【查看商品】到点【下单】中间所用的时间
```

创建PerformanceObserver观察器，观察到performance上报的数据时，打印对应的时间等数据信息（这其中就包含了我们记录的mark信息和页面资源加载等信息）。

```javascript
let callback = (list, observer) => {
    list.getEntries().forEach(entry => {
      switch (entry.entryType) {
        case 'mark':console.log(`（自定义上报-时间点）：${entry.name}，时刻:  - ${entry.startTime}`);break;
        case 'measure':console.log(`（自定义上报-时间段）：${entry.name}，时间: ${entry.duration} `);break;
        default:break;
      }  
    });
}
const observer = new PerformanceObserver(callback) 
observer.observe({ entryTypes:['mark','measure'] });
```

小鸡仔“长大”到被“卖出”，效果展示

![图片](https://imgbed.iiba.fun/blog/640-20220617112402504.gif)

### 2.5 ReportingObserver 报告观察器（实验）

该观察器“观察”过时的api、浏览器的一些干预行为报告，在回调里上报

### 2.5.1 基本用法

通过`new ReportingObserver(callback,options)`创建观察器实例, 按照options配置指定所要观察的report数据。观察到有报告数据时，调取`callback（reports, observer）`回调函数，reports为包含options中指定的相关报告数据的对象report的数组;

```javascript
let options = {
    types: ['intervention', 'deprecation'] //string[], 常可用值有：deprecation(观察使用过时的api)、 intervention（观察[浏览器干预行为](https://chromestatus.com/features#intervention)）
    //buffered: 在观察者能够被创建之前生成的报告是否应该是可观察的；可观察的(true) 或不可观察的 (false)
};
let callback = (reports, observer) => {
    reports.forEach(report => {
        console.log(report.body);// 返回report正文,包含详细的report对象,目前只有两种body对象(取决于type的返回值)
        // report.type 生成的报告类型，例如deprecation或intervention。
        // report.url 生成报告的文档的 URL。
    });
}
const observer = new ReportingObserver(callback, options) //创建观察器实例, 按照`options`配置指定所要观察的report数据。
```

`observer.observe()`开始观察options中指定的报告队列中收集报告，数据上报时调用回调函数

```javascript
observer.observe(); //开始观察options中指定的报告队列中收集报告，数据上报时调用回调函数 。
// observer.disconnect(); //阻止之前开始观察的报告观察者收集报告。
// observer.takeRecords(); //返回当前包含在观察者报告队列中的报告列表，并清空队列。
```

注意：报告观察器现在还是试验性的API，浏览器的支持程度还不够，尤其是Safari浏览器完全不支持。其他观察器相对比较成熟，但也存在部分兼容问题，使用时要视具体情况考虑

![图片](https://imgbed.iiba.fun/blog/640.jpeg)

## 三、总结

通过以上介绍，相信大家对浏览器的5种观察器都有了一定了解。

## 四、感谢

- [【第2535期】浏览器的5种Observer](http://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651252773&idx=1&sn=168ba97ba069bc99d0946bd32d559bfe&chksm=bd4931a18a3eb8b7569912dd35958220e10e44406c6fa5b2bda5e5fac5a7d57c9d9d5769e570&scene=21#wechat_redirect)
- IntersectionObserver API 使用教程
- MutationObserver 介绍
- 通过自定义指令实现前端曝光埋点

## 关于本文
作者：@李静静
原文：https://mp.weixin.qq.com/s/0Kj0cUYrQtx7Bh2VuZ-pGQ
