---
title: 'Svelte教程翻译（七、存储）'
date: 2021-07-20 11:12:20
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 存储

并非所有应用程序状态都应用于程序的组件层次结构内。有时，您的值需要在多个不相关的组件或常规JavaScript模块之间访问。

## writable

在Svelte，我们通过存储来做到这一点。 存储只是一个带有subscribe方法的对象，它允许在存储值发生变化时进行同步操作。

在App.svelte中，`count`是一个存储，我们在`count.subscribe`回调中设置了`count_value`。

```html
<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';

	let count_value;

	const unsubscribe = count.subscribe(value => {
		count_value = value;
	});
</script>

<h1>The count is {count_value}</h1>

<Incrementer/>
<Decrementer/>
<Resetter/>
```

stores.js文件中定义计数。它是一个可写的存储，这意味着它除了`subscribe`之外还有`set`和`update`方法。

```javascript
import { writable } from 'svelte/store';

export const count = writable(0);
```

Incrementer.svelte文件，编写+按钮组件事件代码：

```html
<script>
	import { count } from './stores.js';

	function increment() {
		count.update(n => n + 1);
	}
</script>

<button on:click={increment}>
	+
</button>
```

单击+按钮累加计数。而Decrementer.svelte文件中的代码是进行递减操作：

```html
<script>
	import { count } from './stores.js';

	function decrement() {
		count.update(n => n - 1);
	}
</script>

<button on:click={decrement}>
	-
</button>
```

最后，在Resetter.svelte文件中，实现重置操作：

```html
<script>
	import { count } from './stores.js';

	function reset() {
		count.set(0);
	}
</script>

<button on:click={reset}>
	reset
</button>
```

## 自动subscribe

上一个示例中的应用程序可以工作，但有一个微妙的错误——存储是subscribe状态，但从未unsubscribe。如果组件被多次实例化和销毁，这将导致内存泄漏。

首先在App.svelte中定义常量unsubscribe：

```javascript
const unsubscribe = count.subscribe(value => {
	count_value = value;
});
```

例如通过`onDestroy`生命周期销毁

```html
<script>
	import { onDestroy } from 'svelte';
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';

	let count_value;

	const unsubscribe = count.subscribe(value => {
		count_value = value;
	});

	onDestroy(unsubscribe);
</script>

<h1>The count is {count_value}</h1>
```

Svelte有一个技巧，可以通过在存储名称前加上`$`来引用store值：

```html
<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';
</script>

<h1>The count is {$count}</h1>
```

> 自动订阅仅适用于在组件的顶级范围内声明（或导入）的存储变量。

您也不仅限于在HTML中使用`$count`， 您也可以在任何地方使用它，例如在事件处理程序或响应式声明中。

> 任何以`$`开头的名称都被假定为引用存储值。它实际上是一个保留字符——Svelte将阻止您使用`$`前缀声明自己的变量。

完整代码：

stores.js：

```javascript
import { writable } from 'svelte/store';

export const count = writable(0);
```

Incrementer.svelte：

```html
<script>
	import { count } from './stores.js';

	function increment() {
		count.update(n => n + 1);
	}
</script>

<button on:click={increment}>
	+
</button>
```

Decrementer.svelte：

```html
<script>
	import { count } from './stores.js';

	function decrement() {
		count.update(n => n - 1);
	}
</script>

<button on:click={decrement}>
	-
</button>
```

Resetter.svelte：

```html
<script>
	import { count } from './stores.js';

	function reset() {
		count.set(0);
	}
</script>

<button on:click={reset}>
	reset
</button>
```

App.svelte：

```html
<script>
	import { count } from './stores.js';
	import Incrementer from './Incrementer.svelte';
	import Decrementer from './Decrementer.svelte';
	import Resetter from './Resetter.svelte';
</script>

<h1>The count is {$count}</h1>

<Incrementer/>
<Decrementer/>
<Resetter/>
```

## 可读stores

并不是所有的存储都应该由引用它们的人来写。例如，您可能有一个表示鼠标位置或用户地理位置的存储，并且不能从“外部”设置这些值。对于这些情况，我们创建可读的存储。

`stores.js`文件。readable的第一个参数是一个初始值，如果您还没有初始值，则可以为`null`或`undefined`。第二个参数是一个start函数，它接受一个set回调并返回一个stop函数。当存储获得第一个subscribe时调用start函数；当最后一个unsubscribes时调用stop。

```javascript
import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

```

App.sevlte

```html
<script>
	import { time } from './stores.js';

	const formatter = new Intl.DateTimeFormat('en', {
		hour12: true,
		hour: 'numeric',
		minute: '2-digit',
		second: '2-digit'
	});
</script>

<h1>The time is {formatter.format($time)}</h1>
```

## 派生存储

您可以创建一个存储，其值基于一个或多个具有派生属性的其他存储的值。在前面的示例的基础上，我们可以创建一个存储，它派生出一个计算页面打开时间的函数：

```javascript
export const elapsed = derived(
	time,
	$time => Math.round(($time - start) / 1000)
);
```

> 可以从多个输入派生存储，并显式设置值而不是返回值（这对于异步派生值很有用）。有关更多信息，请参阅API参考。

stores.js

```javascript
import { readable, derived } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

const start = new Date();

export const elapsed = derived(
	time,
	$time => Math.round(($time - start) / 1000)
);
```

App.sevlte

```html
<script>
	import { time, elapsed } from './stores.js';

	const formatter = new Intl.DateTimeFormat('en', {
		hour12: true,
		hour: 'numeric',
		minute: '2-digit',
		second: '2-digit'
	});
</script>

<h1>The time is {formatter.format($time)}</h1>

<p>
	This page has been open for
	{$elapsed} {$elapsed === 1 ? 'second' : 'seconds'}
</p>
```

## 自定义stores

只要对象正确实现subscribe方法，它就是一个存储。除此之外，什么都可以。因此，使用特定于域的逻辑创建自定义存储非常容易。

例如，我们前面示例中的计数存储可以包括递增、递减和重置方法，并避免外露set和update：

```javascript
function createCount() {
	const { subscribe, set, update } = writable(0);

	return {
		subscribe,
		increment: () => update(n => n + 1),
		decrement: () => update(n => n - 1),
		reset: () => set(0)
	};
}
```

stores.js：

```javascript
import { writable } from 'svelte/store';

function createCount() {
	const { subscribe, set, update } = writable(0);

	return {
		subscribe,
		increment: () => update(n => n + 1),
		decrement: () => update(n => n - 1),
		reset: () => set(0)
	};
}

export const count = createCount();
```

App.sevlte：

```html
<script>
	import { count } from './stores.js';
</script>

<h1>The count is {$count}</h1>

<button on:click={count.increment}>+</button>
<button on:click={count.decrement}>-</button>
<button on:click={count.reset}>reset</button>
```

## 存储绑定

如果一个存储是可写的，即它有一个set方法，那么可以绑定到它的值，就像绑定到本地组件一样。

在本例中，我们有一个可写的存储`name`和一个派生的存储`greeting`。更新`<input>`元素：

```html
<input bind:value={$name}>
```

更改输入值将更新`name`及其所有从属项。

我们也可以直接赋值来存储组件中的值。添加`<button>`元素：

```html
<button on:click="{() => $name += '!'}">
	Add exclamation mark!
</button>
```

`$name+='!'`赋值等价于`name.set($name+'!')`。

stores.js：

```javascript
import { writable, derived } from 'svelte/store';

export const name = writable('world');

export const greeting = derived(
	name,
	$name => `Hello ${$name}!`
);
```

App.sevlte

```html
<script>
	import { name, greeting } from './stores.js';
</script>

<h1>{$greeting}</h1>
<input value={$name}>
```

想要了解更多Sevlte，欢迎访问[官网](https://svelte.dev/tutorial/store-bindings)

