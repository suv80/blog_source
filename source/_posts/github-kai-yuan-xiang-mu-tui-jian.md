---
title: ' GitHub 开源项目推荐'
date: 2019-12-31 15:01:34
tags: [github,js]
published: true
hideInList: false
feature: /post-images/github-kai-yuan-xiang-mu-tui-jian.jpg
isTop: false
---
12306、腾讯、美团点评、陌陌企业开源项目

<!-- more -->

### 12306 购票助手，支持集群、多账号、多任务购票以及 Web 页面管理

![](http://blog.mayiya.vip/post-images/1577776440586.png)
![](http://blog.mayiya.vip/post-images/1577776451646.png)

* 多日期查询余票
* 自动打码下单
* 用户状态恢复
* 电话语音通知
* 多账号、多任务、多线程支持
* 单个任务多站点查询
* 分布式运行
* Docker 支持
* 动态修改配置文件
* 邮件通知
* Web 管理页面
* 微信消息通知
* 代理池支持 (pyproxy-async)
 
GitHub地址：[https://github.com/pjialin/py12306](https://github.com/pjialin/py12306)
 

### Hippy：腾讯开源的跨端开发框架
 
Hippy 是一个新生的跨端开发框架，目标是使开发者可以只写一套代码就直接运行于三个平台（iOS、Android 和 Web）。
 
Hippy 的设计是面向传统 Web 开发者的，特别是之前有过 React Native 和 Vue 的开发者用起来会更为顺手，Hippy 致力于让前端开发跨端 App 更加容易。
 
到目前为止，腾讯内已经有了 18 款流行 App 在使用 Hippy 框架，每日触达数亿用户。

* 为传统 Web 前端设计，官方支持 React 和 Vue 两种主流前端框架。
* 不同的平台保持了相同的接口。
* 通过 JS 引擎 binding 模式实现的前终端通讯，具备超强性能。
* 提供了高性能的可复用列表。
* 皆可平滑迁移到 Web 浏览器。
* 完整支持 Flex 的布局引擎。
 
GitHub地址：[https://github.com/Tencent/Hippy](https://github.com/Tencent/Hippy)
 
### Logan：美团点评开源的移动端基础日志组件

![](http://blog.mayiya.vip/post-images/1577776806002.png)
![](http://blog.mayiya.vip/post-images/1577776815617.png)
![](http://blog.mayiya.vip/post-images/1577776822475.png)
![](http://blog.mayiya.vip/post-images/1577776828678.png)
 
Logan 是美团点评集团移动端基础日志组件。名称是 Log 和 An 的组合，代表个体日志服务，同时也是金刚狼大叔的大名。
 
Logan 承载着各种日志的聚合、存储、分析，作为基础日志库，Logan 已经接入了集团众多日志系统，例如端到端日志、用户行为日志、代码级日志、崩溃日志等。
 
GitHub地址：[https://github.com/Meituan-Dianping/Logan](https://github.com/Meituan-Dianping/Logan)
 
### MLN：陌陌开源的高性能、小巧、易上手的移动跨平台开发框架

![](http://blog.mayiya.vip/post-images/1577777002254.gif)
![](http://blog.mayiya.vip/post-images/1577777009174.png)

MLN 是一个移动跨平台开发框架，让开发者用一套代码构建 Android、iOS 应用。
MLN 设计思路贴近原生开发，客户端开发者的经验，可以迅速迁移到 MLN 上，轻易构建出跨平台移动应用。

* 易上手，MLN 专为客户端开发者设计，iOS、Android 程序员非常容易上手。使用 MLN 不需要学习 JS、vue、ES6 这些对客户端开发陌生的前端技术，也不强迫使用响应式框架。花几个小时了解下 lua 语言和文档就能轻松上手，客户端开发者的整个技术栈在这里都派得上用场。
* 增加包体积极小，1.2M。
* 真正的原生性能。跨平台往往意味着性能受损，得益于 lua 虚拟机的高性能和 MLN 优化，我们极大减少了中间介质影响，即使在低端安卓手机加载页面也极快。
* 支持热重载。
 
GitHub地址：[https://github.com/momotech/MLN](https://github.com/momotech/MLN)

### xray：一款功能强大的安全评估工具

![](http://blog.mayiya.vip/post-images/1577777247530.svg)
 
一款完善的安全评估工具，支持常见 web 安全问题扫描和自定义 poc
 
新的检测模块将不断添加

* XSS漏洞检测 (key: xss) 利用语义分析的方式检测XSS漏洞
* SQL 注入检测 (key: sqldet) 支持报错注入、布尔注入和时间盲注等
* 命令/代码注入检测 (key: cmd_injection) 支持 shell 命令注入、PHP 代码执行、模板注入等
* 目录枚举 (key: dirscan) 检测备份文件、临时文件、debug 页面、配置文件等10余类敏感路径和文件
* 路径穿越检测 (key: path_traversal) 支持常见平台和编码
* XML 实体注入检测 (key: xxe) 支持有回显和反连平台检测
* poc 管理 (key: phantasm) 默认内置部分常用的 poc，用户可以根据需要自行构建 poc 并运行。
* 文件上传检测 (key: upload) 支持常见的后端语言
* 弱口令检测 (key: brute_force) 社区版支持检测 HTTP 基础认证和简易表单弱口令，内置常见用户名和密码字典
* jsonp 检测 (key: jsonp) 检测包含敏感信息可以被跨域读取的 jsonp 接口
* ssrf 检测 (key: ssrf) ssrf 检测模块，支持常见的绕过技术和反连平台检测
* 基线检查 (key: baseline) 检测低 SSL 版本、缺失的或错误添加的 http 头等
* 任意跳转检测 (key: redirect) 支持 HTML meta 跳转、30x 跳转等
* CRLF 注入 (key: crlf_injection) 检测 HTTP 头注入，支持 query、body 等位置的参数
* Struts2 系列漏洞检测 (高级版，key: struts) 检测目标网站是否存在Struts2系列漏洞，包括s2-016、s2-032、s2-045等常见漏洞
* Thinkphp系列漏洞检测 (高级版，key: thinkphp) 检测ThinkPHP开发的网站的相关漏洞
 
GitHub地址：[https://github.com/chaitin/xray](https://github.com/chaitin/xray)
 
---

转载：【微信公众号：开发者头条】