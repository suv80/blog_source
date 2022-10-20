---
title: 'Svelte教程翻译（四、事件）'
date: 2021-06-26 09:10:08
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## DOM事件

在下面的事例你将看到，您可以使用`on:事件名`来监听元素上的任何事件：

```html
<div on:mousemove={handleMousemove}>
	The mouse position is {m.x} x {m.y}
</div>
```

完整代码：

```html
<script>
	let m = { x: 0, y: 0 };

	function handleMousemove(event) {
		m.x = event.clientX;
		m.y = event.clientY;
	}
</script>

<div on:mousemove={handleMousemove}>
	The mouse position is {m.x} x {m.y}
</div>

<style>
	div { width: 100%; height: 100%; }
</style>
```

## 内联处理

您还可以使用内联声明事件处理程序：

```html
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>
```

引号是可选的，但在某些环境中，它们有助于提高语法突显。

> 在某些框架中，您可能会看到由于性能原因而避免使用内联事件处理程序的建议，特别是在循环中。这个建议并不适用于Svelte，编译器总是会做正确的事情，不管你选择哪种形式。

## 事件修饰符

DOM事件处理程序可以使用修饰符来改变其行为。例如，带有`once`修饰符的处理程序只运行一次：

```html
<script>
	function handleClick() {
		alert('no more alerts')
	}
</script>

<button on:click|once={handleClick}>
	Click me
</button>
```

修饰符的完整列表：

- preventDefault：在运行处理程序之前。例如，对于客户端窗体处理非常有用。
- stopPropagation：阻止事件到达下一个元素。冒泡。
- passive：提高触摸/滚轮事件的滚动性能（Svelte 会在安全的地方自动添加滚动）
- nonpassive：可以查看[使用 passive 改善的滚屏性能](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener#使用_passive_改善的滚屏性能)
- capture：添加事件监听器时使用事件捕获模式，即元素自身触发的事件先在此处理，然后才交由内部元素进行处理。
- once：只响应一次
- self：只当在`event.target`是当前元素自身时触发处理函数，即事件不是从内部元素触发的。

你可以多个修饰符同时使用，例如：`on:click|once|capture={...}`

## 组件事件

组件也可以分发事件。为此，它们必须创建一个事件分发器:

```html
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>
```

> 当组件首次实例化时，必须调用`createEventDispatcher`。不可以在后续的函数中使用，例如`setTimeout`回调。这会使事件分发与组件实例绑定起来。

注意，由于`on:message`指令，App组件正在监听Inner组件发送的消息。这个指令是一个前缀为`on:`的属性，后面跟着我们要分发的事件名（在本例中是message）。

完整代码：

Inner.sevlte

```html
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>
```

App.sevlte

```html
<script>
	import Inner from './Inner.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Inner on:message={handleMessage}/>
```

如果没有`on:message`这个属性，消息仍然会被发送，但是应用程序不会对此作出反应。您可以尝试删除`on: message`属性并再次按下按钮。

> 您还可以尝试将事件名称更改为其他名称。例如，在`Inner.svelte`中将`dispatch ('message')`更改为`dispatch ('myevent')` ，并在`App.svelte`组件中将属性名从`on: message`更改为`on: myevent`。

## 事件转发

与DOM事件不同，组件事件不会冒泡。如果希望监听某个嵌套深度很深的组件上的事件，则中间组件必须转发该事件。

在本例中，我们有与前面相同的`App.svelte`和`Inner.svelte`，但是现在有一个`Outer.svelte`组件包含`< inner/>`。

```html
<script>
	import Inner from './Inner.svelte';
</script>

<Inner/>
```

我们解决这个问题的一个方法是将`createEventDispatcher`添加到`Outer.svelte`中，监听消息事件，并为它创建一个处理程序：

```html
<script>
	import Inner from './Inner.svelte';
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function forward(event) {
		dispatch('message', event.detail);
	}
</script>

<Inner on:message={forward}/>
```

但是要编写的代码太多了，所以Svelte给了我们一个等价的简写​：`on:message` 意味着转发所有消息事件。

```html
<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message/>
```

完整代码：

Outer.sevlte

```html
<script>
	import Inner from './Inner.svelte';
</script>

<Inner on:message/>
```

Inner.sevlte

```html
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
</script>

<button on:click={sayHello}>
	Click to say hello
</button>
```

App.sevlte

```html
<script>
	import Outer from './Outer.svelte';

	function handleMessage(event) {
		alert(event.detail.text);
	}
</script>

<Outer on:message={handleMessage}/>
```

## DOM事件转发

事件转发也适用于DOM事件。

CustomButton.svelte

```html
<button>
	Click me
</button>

<style>
	button {
		background: #E2E8F0;
		color: #64748B;
		border: unset;
		border-radius: 6px;
		padding: .75rem 1.5rem;
		cursor: pointer;
	}
	button:hover {
		background: #CBD5E1;
		color: #475569;
	}
	button:focus {
		background: #94A3B8;
		color: #F1F5F9;
	}
</style>
```

App.sevlte

```html
<script>
	import CustomButton from './CustomButton.svelte';

	function handleClick() {
		alert('Button Clicked');
	}
</script>

<CustomButton on:click={handleClick}/>
```

我们想触发组件`<CustomButton/>`的点击事件，需要在`CustomButton.svelte`文件的`<button>`上添加点击事件：

```html
<button on:click>
	Click me
</button>
```

如果想亲自体验事件的魅力，还需要去官网上亲自体验一下。[Sevlte事件](https://svelte.dev/tutorial/dom-events)
