---
title: '100DaysOfReact-day 2:改造'
date: 2020-08-07 13:55:44
tags: [reactjs,100DaysOfCode]
published: true
hideInList: false
feature: 
isTop: false
---
为了更好的配合100天计划，对create-react-app生成的app进行一次改造。增加支持多页面。

第一步，安装路由：
![](https://blog.iiba.fun/post-images/1602914291717.png)

第二步，改造index.js：
增加四个组件：Demo1.js，Demo2.js，NoMatch.js，以及相对应的Demo1.css，Demo2.css，NoMatch.css
只列出其中Demo1.js的代码
![](https://blog.iiba.fun/post-images/1602914493512.png)

第三步，修改index.js
修改后，代码如下：
![](https://blog.iiba.fun/post-images/1602914602548.png)

学习了：
1、路由的基本配置
2、组件的创建及引用
