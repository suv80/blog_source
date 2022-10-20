---
title: 'Svelte教程翻译（九、过渡）'
date: 2021-07-20 11:14:08
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 过渡

我们可以通过优雅地将元素显示到DOM中或从DOM中移除，来创建更具吸引力的用户界面。使用transition指令，在Svelte中这变得非常容易。

首先，从svelte/transition导入淡入淡出功能：

```html
<script>
	import { fade } from 'svelte/transition';
	let visible = true;
</script>
```

然后将其添加到`<p>`元素：

```html
<p transition:fade>Fades in and out</p>
```

完整代码：

```html
<script>
	import { fade } from 'svelte/transition';
	let visible = true;
</script>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<p transition:fade>
		Fades in and out
	</p>
{/if}
```

## 添加参数

过渡函数可以接受参数。用`fly`替换`fade`：

```html
<script>
	import { fly } from 'svelte/transition';
	let visible = true;
</script>
```

并将其应用于`<p>`以及一些选项：

```html
<p transition:fly="{{ y: 200, duration: 2000 }}">
	Flies in and out
</p>
```

请注意，过渡是可逆的。如果您在过渡进行时切换复选框，它将从当前点过渡，而不是从开始或结束点。

完整代码：

```html
<script>
	import { fly } from 'svelte/transition';
	let visible = true;
</script>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<p transition:fly="{{ y: 200, duration: 2000 }}">
		Flies in and out
	</p>
{/if}
```

## 淡入淡出

一个元素可以有一个in或out指令，或者两者都有，而不是transition指令。导入`fade`和`fly`：

```javascript
import { fade, fly } from 'svelte/transition';
```

然后将过渡指令替换为单独的输入和输出指令：

```html
<p in:fly="{{ y: 200, duration: 2000 }}" out:fade>
	Flies in, fades out
</p>
```

在这种情况下，过渡不会反转。

## 自定义CSS过渡

svelte/transition模块有一些内置的过渡，但很容易创建自己的过渡。举例来说，这是fade过渡的源代码：

```javascript
function fade(node, {
	delay = 0,
	duration = 400
}) {
	const o = +getComputedStyle(node).opacity;

	return {
		delay,
		duration,
		css: t => `opacity: ${t * o}`
	};
}
```

该函数接受两个参数：应用过渡的节点和传入的参数对象，并返回具有以下属性的对象：

- delay：过渡开始前的毫秒数

- duration：过渡的时长（毫秒）

- easing：一个`p=>t`函数

- css：一个`(t,u)=>css`函数，其中`u===1-t`

- tick：一个 `(t, u) => {...}`函数，对节点有一定影响

t=0时，从头开始播放动画，t=1时，反转动画。

大多数情况下，您应该返回css属性而不是tick属性，因为css动画会从主线程运行。Svelte“模拟”过渡并构造一个CSS动画，然后让它运行。

例如，“淡入淡出”（fade）过渡生成类似以下内容的CSS动画：

```css
0% { opacity: 0 }
10% { opacity: 0.1 }
20% { opacity: 0.2 }
/* ... */
100% { opacity: 1 }
```

不过，我们可以变得更有创意：

```html
<script>
	import { fade } from 'svelte/transition';
	import { elasticOut } from 'svelte/easing';

	let visible = true;

	function spin(node, { duration }) {
		return {
			duration,
			css: t => {
				const eased = elasticOut(t);

				return `
					transform: scale(${eased}) rotate(${eased * 1080}deg);
					color: hsl(
						${~~(t * 360)},
						${Math.min(100, 1000 - 1000 * t)}%,
						${Math.min(50, 500 - 500 * t)}%
					);`
			}
		};
	}
</script>
```

记住：强大的力量带来巨大的责任。

完整代码：

```html
<script>
	import { fade } from 'svelte/transition';
	import { elasticOut } from 'svelte/easing';

	let visible = true;

	function spin(node, { duration }) {
		return {
			duration,
			css: t => {
				const eased = elasticOut(t);

				return `
					transform: scale(${eased}) rotate(${eased * 1080}deg);
					color: hsl(
						${~~(t * 360)},
						${Math.min(100, 1000 - 1000 * t)}%,
						${Math.min(50, 500 - 500 * t)}%
					);`
			}
		};
	}
</script>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<div class="centered" in:spin="{{duration: 8000}}" out:fade>
		<span>transitions!</span>
	</div>
{/if}

<style>
	.centered {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%,-50%);
	}

	span {
		position: absolute;
		transform: translate(-50%,-50%);
		font-size: 4em;
	}
</style>
```

## 自定义JS过渡

虽然您通常应该尽可能多地使用CSS进行过渡，但有一些效果是没有JavaScript无法实现的，例如打字机效果：

```javascript
function typewriter(node, { speed = 50 }) {
	const valid = (
		node.childNodes.length === 1 &&
		node.childNodes[0].nodeType === Node.TEXT_NODE
	);

	if (!valid) {
		throw new Error(`This transition only works on elements with a single text node child`);
	}

	const text = node.textContent;
	const duration = text.length * speed;

	return {
		duration,
		tick: t => {
			const i = ~~(text.length * t);
			node.textContent = text.slice(0, i);
		}
	};
}
```

完整代码：

```html
<script>
	let visible = false;

	function typewriter(node, { speed = 50 }) {
		const valid = (
			node.childNodes.length === 1 &&
			node.childNodes[0].nodeType === Node.TEXT_NODE
		);

		if (!valid) {
			throw new Error(`This transition only works on elements with a single text node child`);
		}

		const text = node.textContent;
		const duration = text.length * speed;

		return {
			duration,
			tick: t => {
				const i = ~~(text.length * t);
				node.textContent = text.slice(0, i);
			}
		};
	}
</script>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<p in:typewriter>
		The quick brown fox jumps over the lazy dog
	</p>
{/if}

```

## 过渡事件

知道过渡的开始和结束时间可能很有用。与任何其他DOM事件一样，Svelte可以监听过渡事件：

```html
<p
	transition:fly="{{ y: 200, duration: 2000 }}"
	on:introstart="{() => status = 'intro started'}"
	on:outrostart="{() => status = 'outro started'}"
	on:introend="{() => status = 'intro ended'}"
	on:outroend="{() => status = 'outro ended'}"
>
	Flies in and out
</p>
```

完整代码：

```html
<script>
	import { fly } from 'svelte/transition';

	let visible = true;
	let status = 'waiting...';
</script>

<p>status: {status}</p>

<label>
	<input type="checkbox" bind:checked={visible}>
	visible
</label>

{#if visible}
	<p
		transition:fly="{{ y: 200, duration: 2000 }}"
		on:introstart="{() => status = 'intro started'}"
		on:outrostart="{() => status = 'outro started'}"
		on:introend="{() => status = 'intro ended'}"
		on:outroend="{() => status = 'outro ended'}"
	>
		Flies in and out
	</p>
{/if}
```

## 局部过渡

通常，当添加或销毁任何容器块时，过渡将在元素上起作用。在这里的示例中，切换整个列表的可见性也会将过渡应用于各个列表元素。

相反，我们希望仅在添加和删除单个项目时播放过渡。

我们可以通过局部转换来实现这一点，该转换仅在添加或删除当前节点时播放：

```html
<div transition:slide|local>
	{item}
</div>
```

完整代码：

```html
<script>
	import { slide } from 'svelte/transition';

	let showItems = true;
	let i = 5;
	let items = ['one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'ten'];
</script>

<label>
	<input type="checkbox" bind:checked={showItems}>
	show list
</label>

<label>
	<input type="range" bind:value={i} max=10>

</label>

{#if showItems}
	{#each items.slice(0, i) as item}
		<div transition:slide|local>
			{item}
		</div>
	{/each}
{/if}

<style>
	div {
		padding: 0.5em 0;
		border-top: 1px solid #eee;
	}
</style>
```

## 延迟过渡

Svelte的过渡引擎的一个特别强大的功能是延迟过渡的能力，这样它们就可以在多个元素之间进行协调。

以这对todo列表为例，在其中切换todo会将其发送到相反的列表。在现实世界中，物体的行为不是这样的，它们通过一系列中间位置移动，而不是在另一个地方消失和重新出现。使用motion可以帮助用户理解应用程序中发生的事情。

我们可以使用crossfade函数来实现这个效果，它创建了一对名为send和receive的过渡。当一个元素被“发送”时，它会寻找一个对应的元素“接收”，并生成一个过渡，将该元素过渡到对应的位置并淡出。当一个元素被“接收”时，情况正好相反。如果没有对应项，则使用回退过渡。

完整代码：

```html
<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	let uid = 1;

	let todos = [
		{ id: uid++, done: false, description: 'write some docs' },
		{ id: uid++, done: false, description: 'start writing blog post' },
		{ id: uid++, done: true,  description: 'buy some milk' },
		{ id: uid++, done: false, description: 'mow the lawn' },
		{ id: uid++, done: false, description: 'feed the turtle' },
		{ id: uid++, done: false, description: 'fix some bugs' },
	];

	function add(input) {
		const todo = {
			id: uid++,
			done: false,
			description: input.value
		};

		todos = [todo, ...todos];
		input.value = '';
	}

	function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}

	function mark(todo, done) {
		todo.done = done;
		remove(todo);
		todos = todos.concat(todo);
	}
</script>

<div class='board'>
	<input
		placeholder="what needs to be done?"
		on:keydown={e => e.key === 'Enter' && add(e.target)}
	>

	<div class='left'>
		<h2>todo</h2>
		{#each todos.filter(t => !t.done) as todo (todo.id)}
			<label
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
			>
				<input type=checkbox on:change={() => mark(todo, true)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>

	<div class='right'>
		<h2>done</h2>
		{#each todos.filter(t => t.done) as todo (todo.id)}
			<label
				class="done"
				in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
			>
				<input type=checkbox checked on:change={() => mark(todo, false)}>
				{todo.description}
				<button on:click="{() => remove(todo)}">remove</button>
			</label>
		{/each}
	</div>
</div>

<style>
	.board {
		display: grid;
		grid-template-columns: 1fr 1fr;
		grid-gap: 1em;
		max-width: 36em;
		margin: 0 auto;
	}

	.board > input {
		font-size: 1.4em;
		grid-column: 1/3;
	}

	h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
		margin: 0 0 0.5em 0;
	}

	label {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		margin: 0 0 0.5em 0;
		border-radius: 2px;
		user-select: none;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}

	input[type="checkbox"] {
		position: absolute;
		left: 0.5em;
		top: 0.6em;
		margin: 0;
	}

	.done {
		border: 1px solid hsl(240, 8%, 90%);
		background-color:hsl(240, 8%, 98%);
	}

	button {
		position: absolute;
		top: 0;
		right: 0.2em;
		width: 2em;
		height: 100%;
		background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
		background-size: 1.4em 1.4em;
		border: none;
		opacity: 0;
		transition: opacity 0.2s;
		text-indent: -9999px;
		cursor: pointer;
	}

	label:hover button {
		opacity: 1;
	}
</style>
```

过渡动画的教程示例很精彩，推荐到官网查看。[过渡](https://svelte.dev/tutorial/transition)


