---
title: 'Svelte教程翻译（〇、引言）'
date: 2021-06-22 17:05:08
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 什么是Svelte？

Svelte是用于构建快速Web应用程序的工具。

它类似于React和Vue等JavaScript框架，它们的共同目标是轻松构建流畅的交互式用户界面。

但是有一个关键的区别：Svelte在构建时将先编译您的应用程序，而不是在运行时解析您的应用程序代码。这意味着性能更好，首次运行时更为流畅。

您可以使用Svelte构建整个应用程序，也可以将其逐步添加到现有代码库中。您还可以将组件作为可在任何地方工作的独立包发布，而无需依赖传统框架的开销。

## 从Hello World开始

```html
<script>
	let name = 'world';
</script>

<h1>Hello world!</h1>
```
然后，我们可以在标记中引用name。
```html
<h1>Hello {name}!</h1>
```
在大括号里面，我们可以放任何我们想要的JavaScript。

## 速记属性，属性语法糖

就像可以使用大括号来控制文本一样，也可以使用它们来控制元素属性。

```html
<script>
	let src = 'tutorial/image.gif';
</script>

<img>
```

我们的图像缺少一个srcー让我们添加一个：

```html
<img src={src}>
```

这样好多了，但Svelte给了我们一个警告：

```log
A11y: <img> element should have an alt attribute

11y: < img > 元素应该有一个 alt 属性
```

在构建网络应用时，确保它们能够被更广泛的用户群访问是非常重要的，这些用户群包括视力或运动受损的人，或者没有强大硬件或良好互联网环境的人。可访问性(缩写为a11y)的提示并不会轻易出现，但是如果您编写不可访问的标记，Svelte会通过警告您来提供帮助。

这里，我们忽略了替换属性alt，替换属性描述图片内容，主要提供给使用屏幕阅读器的人们，或者那些网络连接缓慢或不稳定的环境下无法下载图像的情形。让我们再加上一条：

```html
<img src={src} alt="A man dances.">
```

名称和值相同的属性并不罕见，比如src={ src }。为这些情况提供了一个简便的语法糖：

```html
<img {src} alt="A man dances.">
```

## 不会被污染的组件样式

就像在 HTML 中一样，您可以向组件添加一个`<style>`标记。让我们为`<p>`元素添加一些样式：

```html
<p>This is a paragraph.</p>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

重要的是，这些样式规则的范围仅限于组件。您不会意外地改变应用程序中其他地方`<p>`元素的样式，接下来我们将看到这一点。

将整个应用程序放在一个单独的组件中是不切实际的。相反，我们可以从其他文件中引入组件，并将它们包含在内，就好像包含了元素一样。

我们先创建一个neted.svelte文件，内容如下：

```html
<p>This is another paragraph.</p>
```

再创建一个app.svelte文件，内容如下：

```html
<p>This is a paragraph.</p>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

添加一个导入neted.svelte的`<script>`标记。

```html
<script>
	import Nested from './Nested.svelte';
</script>
```

然后加上使用标记：

```html
<p>This is a paragraph.</p>
<Nested/>
```

完整的文件代码：

```html
<script>
	import Nested from './Nested.svelte';
</script>

<p>This is a paragraph.</p>
<Nested/>

<style>
	p {
		color: purple;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

注意，即使neted.svelte有一个`<p>`元素，app.svelte中的样式也不会污染。

![WX20210622-164038@2x.png](https://gitee.com/limeng1984/pstore/raw/master/blog/1240-20210623095118663.png)

还要注意组件名Nested是首字母大写的。我们采用这种约定来区分用户定义的组件和常规的HTML标记。

## HTML输出

通常，字符串作为纯文本插入，这意味着像`<`和`>`这样的字符没有特殊含义。

但有时您需要将HTML直接呈现到组件中。在Svelte中，你可以使用特殊的`{@html... }`标签：

```html
<p>{@html string}</p>
```

在将表达式插入DOM之前，Svelte不会对{@html... }内部的表达式执行任何安全验证。换句话说，如果您使用这个特性，您必须手动转义来自您不信任的源代码的HTML，否则您的用户将面临遭受XSS攻击的风险。

看过之后有没有想上手试试的冲动？
此框架有在线版的代码编辑器，不用在本地安装框架，即可操练起来。
而且官网有详尽的教程和文档。
点击[Svelte REPL](https://svelte.dev/repl)去体验一下。
