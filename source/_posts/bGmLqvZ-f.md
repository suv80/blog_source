---
title: 'Svelte教程翻译（十五、特殊元素）'
date: 2021-08-06 14:43:15
tags: [js,Svelte]
published: true
hideInList: false
feature: 
isTop: false
---
## `<svelte:self>`

Svelte提供多种内置元素。第一个是`<svelte:self>`，允许组件递归地包含自身。

它对于类似于文件夹树视图的内容非常有用，在该视图中，文件夹可以包含其他文件夹。在Folder.svelte中，我们希望能够执行此操作：

```html
{#if file.files}
	<Folder {...file}/>
{:else}
	<File {...file}/>
{/if}
```

但这是不可能的，因为模块无法导入自身。相反，我们使用`<svelte:self>`。

```html
{#if file.files}
	<svelte:self {...file}/>
{:else}
	<File {...file}/>
{/if}
```

完整代码：

File.svelte：

```html
<script>
	export let name;
	$: type = name.slice(name.lastIndexOf('.') + 1);
</script>

<span style="background-image: url(tutorial/icons/{type}.svg)">{name}</span>

<style>
	span {
		padding: 0 0 0 1.5em;
		background: 0 0.1em no-repeat;
		background-size: 1em 1em;
	}
</style>
```

Folder.svelte：

```html
<script>
	import File from './File.svelte';

	export let expanded = false;
	export let name;
	export let files;

	function toggle() {
		expanded = !expanded;
	}
</script>

<span class:expanded on:click={toggle}>{name}</span>

{#if expanded}
	<ul>
		{#each files as file}
			<li>
				{#if file.files}
					<svelte:self {...file}/>
				{:else}
					<File {...file}/>
				{/if}
			</li>
		{/each}
	</ul>
{/if}

<style>
	span {
		padding: 0 0 0 1.5em;
		background: url(tutorial/icons/folder.svg) 0 0.1em no-repeat;
		background-size: 1em 1em;
		font-weight: bold;
		cursor: pointer;
	}

	.expanded {
		background-image: url(tutorial/icons/folder-open.svg);
	}

	ul {
		padding: 0.2em 0 0 0.5em;
		margin: 0 0 0 0.5em;
		list-style: none;
		border-left: 1px solid #eee;
	}

	li {
		padding: 0.2em 0;
	}
</style>
```

App.svelte：

```html
<script>
	import Folder from './Folder.svelte';

	let root = [
		{
			name: 'Important work stuff',
			files: [
				{ name: 'quarterly-results.xlsx' }
			]
		},
		{
			name: 'Animal GIFs',
			files: [
				{
					name: 'Dogs',
					files: [
						{ name: 'treadmill.gif' },
						{ name: 'rope-jumping.gif' }
					]
				},
				{
					name: 'Goats',
					files: [
						{ name: 'parkour.gif' },
						{ name: 'rampage.gif' }
					]
				},
				{ name: 'cat-roomba.gif' },
				{ name: 'duck-shuffle.gif' },
				{ name: 'monkey-on-a-pig.gif' }
			]
		},
		{ name: 'TODO.md' }
	];
</script>

<Folder name="Home" files={root} expanded/>
```

实例有很多知识点，可以去官方亲自体验一下：[svelte-self](https://svelte.dev/tutorial/svelte-self)

## `<svelte:component>`

动态加载组件可以使用`<svelte:component>`。而不是一系列的if块：

```html
{#if selected.color === 'red'}
	<RedThing/>
{:else if selected.color === 'green'}
	<GreenThing/>
{:else if selected.color === 'blue'}
	<BlueThing/>
{/if}
```

我们可以使用一个单独的动态组件：

```html
<svelte:component this={selected.component}/>
```

`this`值可以是任何组件构造函数，也可以是假值如，果是假值，则不会呈现任何组件。

原代码：

RedThing.svelte

```html
<strong>Red thing</strong>

<style>
	strong {
		color: red;
	}
</style>
```

GreenThing.svelte

```html
<strong>Green thing</strong>

<style>
	strong {
		color: green;
	}
</style>
```

BlueThing.svelte

```html
<strong>Blue thing</strong>

<style>
	strong {
		color: blue;
	}
</style>
```

App.svelte

```html
<script>
	import RedThing from './RedThing.svelte';
	import GreenThing from './GreenThing.svelte';
	import BlueThing from './BlueThing.svelte';

	const options = [
		{ color: 'red',   component: RedThing   },
		{ color: 'green', component: GreenThing },
		{ color: 'blue',  component: BlueThing  },
	];

	let selected = options[0];
</script>

<select bind:value={selected}>
	{#each options as option}
		<option value={option}>{option.color}</option>
	{/each}
</select>

<svelte:component this={selected.component}/>
```

推荐到官网体验一下：[svelte-component](https://svelte.dev/tutorial/svelte-component)

## `<svelte:window>`

正如您可以向任何DOM元素添加事件侦听器一样，您也可以使用`<svelte:window>`向窗口对象添加事件侦听器。

```html
<script>
	let key;
	let keyCode;

	function handleKeydown(event) {
		key = event.key;
		keyCode = event.keyCode;
	}
</script>

<svelte:window on:keydown={handleKeydown}/>

<div style="text-align: center">
	{#if key}
		<kbd>{key === ' ' ? 'Space' : key}</kbd>
		<p>{keyCode}</p>
	{:else}
		<p>Focus this window and press any key</p>
	{/if}
</div>

<style>
	div {
		display: flex;
		height: 100%;
		align-items: center;
		justify-content: center;
		flex-direction: column;
	}

	kbd {
		background-color: #eee;
		border-radius: 4px;
		font-size: 6em;
		padding: 0.2em 0.5em;
		border-top: 5px solid rgba(255, 255, 255, 0.5);
		border-left: 5px solid rgba(255, 255, 255, 0.5);
		border-right: 5px solid rgba(0, 0, 0, 0.2);
		border-bottom: 5px solid rgba(0, 0, 0, 0.2);
		color: #555;
	}
</style>
```

> 与DOM元素一样，您可以添加事件修饰符，如preventDefault。

我们还可以绑定到窗口的某些属性，例如scrollY。

```html
<svelte:window bind:scrollY={y}/>
```

可以绑定到的属性列表如下所示：

- `innerWidth`
- `innerHeight`
- `outerWidth`
- `outerHeight`
- `scrollX`
- `scrollY`
- `online` —  `window.navigator.onLine`的别名

除scrollX和scrollY之外的所有文件都是只读的。

完整代码：

```html
<script>
	const layers = [0, 1, 2, 3, 4, 5, 6, 7, 8];

	let y;
</script>

<svelte:window bind:scrollY={y}/>

<a class="parallax-container" href="https://www.firewatchgame.com">
	{#each layers as layer}
		<img
			style="transform: translate(0,{-y * layer / (layers.length - 1)}px)"
			src="https://www.firewatchgame.com/images/parallax/parallax{layer}.png"
			alt="parallax layer {layer}"
		>
	{/each}
</a>

<div class="text">
	<span style="opacity: {1 - Math.max(0, y / 40)}">
		scroll down
	</span>

	<div class="foreground">
		You have scrolled {y} pixels
	</div>
</div>

<style>
	.parallax-container {
		position: fixed;
		width: 2400px;
		height: 712px;
		left: 50%;
		transform: translate(-50%,0);
	}

	.parallax-container img {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		will-change: transform;
	}

	.parallax-container img:last-child::after {
		content: '';
		position: absolute;
		width: 100%;
		height: 100%;
		background: rgb(45,10,13);
	}

	.text {
		position: relative;
		width: 100%;
		height: 300vh;
		color: rgb(220,113,43);
		text-align: center;
		padding: 4em 0.5em 0.5em 0.5em;
		box-sizing: border-box;
		pointer-events: none;
	}

	span {
		display: block;
		font-size: 1em;
		text-transform: uppercase;
		will-change: transform, opacity;
	}

	.foreground {
		position: absolute;
		top: 711px;
		left: 0;
		width: 100%;
		height: calc(100% - 712px);
		background-color: rgb(32,0,1);
		color: white;
		padding: 50vh 0 0 0;
	}

	:global(body) {
		margin: 0;
		padding: 0;
		background-color: rgb(253, 174, 51);
	}
</style>
```

可以去官网体验一下：[svelte-window](https://svelte.dev/tutorial/svelte-window)

## `<svelte:body>`

与`<svelte:window>`类似，`<svelte:body>`元素允许您侦听在`document.body`上触发的事件。这对于mouseenter和mouseleave事件很有用，它们不会在窗口上触发。

将mouseenter和mouseleave处理程序添加到`<svelte:body>`标签：

```html
<svelte:body
	on:mouseenter={handleMouseenter}
	on:mouseleave={handleMouseleave}
/>
```

完整代码：

```html
<script>
	let hereKitty = false;

	const handleMouseenter = () => hereKitty = true;
	const handleMouseleave = () => hereKitty = false;
</script>

<svelte:body
	on:mouseenter={handleMouseenter}
	on:mouseleave={handleMouseleave}
/>

<!-- creative commons BY-NC http://www.pngall.com/kitten-png/download/7247 -->
<img
	class:curious={hereKitty}
	alt="Kitten wants to know what's going on"
	src="tutorial/kitten.png"
>

<style>
	img {
		position: absolute;
		left: 0;
		bottom: -60px;
		transform: translate(-80%, 0) rotate(-30deg);
		transform-origin: 100% 100%;
		transition: transform 0.4s;
	}

	.curious {
		transform: translate(-15%, 0) rotate(0deg);
	}

	:global(body) {
		overflow: hidden;
	}
</style>
```

官网体验地址：[svelte-body](https://svelte.dev/tutorial/svelte-body)

## `<svelte:head>`

`<svelte:head>`元素允许您在文档的`<head>`中插入元素：

```html
<svelte:head>
	<link rel="stylesheet" href="tutorial/dark-theme.css">
</svelte:head>
```

> 在服务器端呈现（SSR）模式下，`<svelte:head>`的内容与HTML的其余部分分开返回。

完整代码：

```html
<svelte:head>
	<link rel="stylesheet" href="tutorial/dark-theme.css">
</svelte:head>

<h1>Hello world!</h1>
```

官网体验地址：[svelte-head](https://svelte.dev/tutorial/svelte-head)

## `<svelte:options>`

`<svelte:options>`元素允许您指定编译器选项。

我们将使用immutable选项作为示例。在此应用程序中，`<Todo>`组件在接收到新数据时闪烁。单击其中一项可通过创建更新的todos数组来切换其完成状态。这会导致其他`<Todo>`项闪烁，即使它们最终不会对DOM进行任何更改。

我们可以通过告诉`<Todo>`组件期望不可变的数据来优化这一点。这意味着我们承诺永远不会改变todo prop，而是在情况发生变化时创建新的todo对象。

将以下内容添加到Todo.svelte文件的顶部：

```html
<svelte:options immutable={true}/>
```

> 如果愿意，您可以将其缩短为`<svelte:options immutable/>`。

现在，当您通过单击TODO来切换TODO时，只有更新的组件才会闪烁。

可在此处设置的选项有：

- `immutable={true}` — 您从不使用可变数据，因此编译器可以执行简单的引用相等性检查以确定值是否已更改
- `immutable={false}` — 默认值。对于可变对象是否已更改，Svelte将更加保守
- `accessors={true}` — 为组件的prop添加getter和setter
- `accessors={false}` — 默认值
- `namespace="..."` — 将使用此组件的命名空间，最常见的是“svg”
- `tag="..."` — 将此组件编译为自定义元素时使用的名称

有关这些选项的更多信息，请参阅API参考。

完整代码：

```html
<script>
	import Todo from './Todo.svelte';

	let todos = [
		{ id: 1, done: true, text: 'wash the car' },
		{ id: 2, done: false, text: 'take the dog for a walk' },
		{ id: 3, done: false, text: 'mow the lawn' }
	];

	function toggle(toggled) {
		todos = todos.map(todo => {
			if (todo === toggled) {
				// return a new object
				return {
					id: todo.id,
					text: todo.text,
					done: !todo.done
				};
			}

			// return the same object
			return todo;
		});
	}
</script>

<h2>Todos</h2>
{#each todos as todo}
	<Todo {todo} on:click={() => toggle(todo)}/>
{/each}
```

官网体验地址：[svelte-options](https://svelte.dev/tutorial/svelte-options)

## `<svelte:fragment>`

`<svelte:fragment>`元素允许您将内容放置在命名槽中，而无需将其包装在容器DOM元素中。这将保持文档的流布局完好无损。

在本例中，请注意我们是如何将间距为1em的flex布局应用于长方体的。

```html
<!-- Box.svelte -->
<div class="box">
	<slot name="header">No header was provided</slot>
	<p>Some content between header and footer</p>
	<slot name="footer"></slot>
</div>

<style>
	.box {        
		display: flex;
		flex-direction: column;
		gap: 1em;
	}
</style>
```

![image-20210806142958584](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210806142958584.png)

但是，页脚中的内容不会按照此规则隔开，因为将其包装在div中会创建一个新的流布局。

我们可以通过在应用程序组件中更改`<div slot="footer">`来解决这个问题。将`<div>`替换为`<svelte:fragment>`：

```html
<svelte:fragment slot="footer">
	<p>All rights reserved.</p>
	<p>Copyright (c) 2019 Svelte Industries</p>
</svelte:fragment>
```

![image-20210806143018941](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210806143018941.png)

完整代码：

```html
<!-- Box.svelte -->
<div class="box">
	<slot name="header">No header was provided</slot>
	<p>Some content between header and footer</p>
	<slot name="footer"></slot>
</div>

<style>
	.box {
		width: 300px;
		border: 1px solid #aaa;
		border-radius: 2px;
		box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.1);
		padding: 1em;
		margin: 0 0 1em 0;
		
		display: flex;
		flex-direction: column;
		gap: 1em;
	}
</style>
```

```html
<!-- App.svelte -->
<script>
	import Box from './Box.svelte'
</script>

<Box>
	<svelte:fragment slot="footer">
		<p>All rights reserved.</p>
		<p>Copyright (c) 2019 Svelte Industries</p>
	</svelte:fragment>
</Box>
```

官网体验地址：[svelte-fragment](https://svelte.dev/tutorial/svelte-fragment)


