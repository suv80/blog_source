---
title: 'Svelte教程翻译（六、生命周期）'
date: 2021-06-30 10:27:51
tags: [Svelte]
published: true
hideInList: false
feature: 
isTop: false
---
## 生命周期

每个组件都有一个生命周期，从创建时开始，到销毁结束。同时在这个过程中也会运行一些叫做生命周期的函数，可以在不同阶段添加处理代码的机会。

## Mount

您最常使用的是`onMount`，它在组件第一次呈现到DOM之后运行。当我们需要在渲染后与元素进行交互时，可以在此函数中增加处理代码。还记得`<canvas/>`这个HTML标签么？如果不记得，可以返回上一章回顾一下。

我们将添加一个`onMount`，在这个函数里添加一些通过网络请求加载数据的处理程序：

```html
<script>
	import { onMount } from 'svelte';

	let photos = [];

	onMount(async () => {
		const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`);
		photos = await res.json();
	});
</script>
```

> 由于服务器端渲染（SSR）机制，建议将fetch放在`onMount`而不是`<script>`的顶层。除了`onDestroy`之外，生命周期函数不会在SSR期间运行，这意味着我们可以在组件装载到DOM之后再获取数据，以达到懒加载的目的。

必须在组件初始化时调用生命周期函数，以便回调绑定到组件实例，而不是像在类似`setTimeout`这种延时函数中。

如果`onMount`回调返回一个函数，则在销毁组件时将调用该函数。

完整示例代码：

```html
<script>
	import { onMount } from 'svelte';

	let photos = [];

	onMount(async () => {
		const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`);
		photos = await res.json();
	});
</script>

<h1>Photo album</h1>

<div class="photos">
	{#each photos as photo}
		<figure>
			<img src={photo.thumbnailUrl} alt={photo.title}>
			<figcaption>{photo.title}</figcaption>
		</figure>
	{:else}
		<!-- this block renders when photos.length === 0 -->
		<p>loading...</p>
	{/each}
</div>

<style>
	.photos {
		width: 100%;
		display: grid;
		grid-template-columns: repeat(5, 1fr);
		grid-gap: 8px;
	}

	figure, img {
		width: 100%;
		margin: 0;
	}
</style>
```

## destroy

若要在组件被销毁时运行代码，请使用`onDestroy` 。

例如，当我们的组件初始化运行时，我们添加`setInterval`，当它不再使用时清理它。这样做可以防止内存泄漏。

```html
<script>
	import { onDestroy } from 'svelte';

	let seconds = 0;
	const interval = setInterval(() => seconds += 1, 1000);

	onDestroy(() => clearInterval(interval));
</script>
```

虽然在组件初始化期间调用生命周期函数很重要，但并不会强制你在特定的地方或固定的方式去调用。因此，如果需要，我们可以将生命周期函数抽象出来`utils.js`：

```javascript
import { onDestroy } from 'svelte';

export function onInterval(callback, milliseconds) {
	const interval = setInterval(callback, milliseconds);

	onDestroy(() => {
		clearInterval(interval);
	});
}
```

然后将其引用到组件中使用：

```html
<script>
	import { onInterval } from './utils.js';

	let seconds = 0;
	onInterval(() => seconds += 1, 1000);
</script>
```

App.sevlte：

```html
<script>
	import { onInterval } from './utils.js';

	let seconds = 0;
	onInterval(() => seconds += 1, 1000);
</script>

<p>
	The page has been open for
	{seconds} {seconds === 1 ? 'second' : 'seconds'}
</p>
```

## beforeUpdate和afterUpdate

`beforeUpdate`在数据与DOM将要同步，即DOM更新，之前自动调用，`afterUpdate`是在数据与DOM同步完成之后自动调用。

官方给了一个实例，实例是一个聊天窗口，当没有使用`update`时，聊天窗口中的新消息不会自动上滚，即，当消息满屏的时候，看不到最新的消息，当使用了`update`之后，消息满屏后，会自动滚屏，可以看到最新的消息。强烈建议去官方亲自体验一下。官网上回复你的是个可爱的机器人噢。

完整的示例代码：

```html
<script>
	import Eliza from 'elizabot';
	import { beforeUpdate, afterUpdate } from 'svelte';

	let div;
	let autoscroll;

	beforeUpdate(() => {
		autoscroll = div && (div.offsetHeight + div.scrollTop) > (div.scrollHeight - 20);
	});

	afterUpdate(() => {
		if (autoscroll) div.scrollTo(0, div.scrollHeight);
	});

	const eliza = new Eliza();

	let comments = [
		{ author: 'eliza', text: eliza.getInitial() }
	];

	function handleKeydown(event) {
		if (event.key === 'Enter') {
			const text = event.target.value;
			if (!text) return;

			comments = comments.concat({
				author: 'user',
				text
			});

			event.target.value = '';

			const reply = eliza.transform(text);

			setTimeout(() => {
				comments = comments.concat({
					author: 'eliza',
					text: '...',
					placeholder: true
				});

				setTimeout(() => {
					comments = comments.filter(comment => !comment.placeholder).concat({
						author: 'eliza',
						text: reply
					});
				}, 500 + Math.random() * 500);
			}, 200 + Math.random() * 200);
		}
	}
</script>

<style>
	.chat {
		display: flex;
		flex-direction: column;
		height: 100%;
		max-width: 320px;
	}

	.scrollable {
		flex: 1 1 auto;
		border-top: 1px solid #eee;
		margin: 0 0 0.5em 0;
		overflow-y: auto;
	}

	article {
		margin: 0.5em 0;
	}

	.user {
		text-align: right;
	}

	span {
		padding: 0.5em 1em;
		display: inline-block;
	}

	.eliza span {
		background-color: #eee;
		border-radius: 1em 1em 1em 0;
	}

	.user span {
		background-color: #0074D9;
		color: white;
		border-radius: 1em 1em 0 1em;
		word-break: break-all;
	}
</style>

<div class="chat">
	<h1>Eliza</h1>

	<div class="scrollable" bind:this={div}>
		{#each comments as comment}
			<article class={comment.author}>
				<span>{comment.text}</span>
			</article>
		{/each}
	</div>

	<input on:keydown={handleKeydown}>
</div>
```

## Tick

tick函数不同于其他生命周期函数，因为你可以在任何时候调用它，而不仅仅可以用在组件第一次初始化的时候。它返回一个Promise，等待正在更新DOM的操作，如果没有，则立即执行。

当你在Svelte中更新组件状态时，它不会立即更新DOM。相反，它将等待下一个小任务，以查看是否需要应用任何其他更改，包括在其他组件中。这样做可以避免不必要的工作，并允许浏览器更有效地对事情进行批处理。

您可以在下面的例子中看到这种行为。选择一些字母并按tab键。此时，选中的字母会变成大写字母，由于文本区值发生了变化，当前的选择状态会被清除，光标会跳到末尾，这很烦人。我们可以通过引用tick来解决这个问题：

```html
<script>
	import { tick } from 'svelte';

	let text = `Select some text and hit the tab key to toggle uppercase`;

	async function handleKeydown(event) {
		if (event.key !== 'Tab') return;

		event.preventDefault();

		const { selectionStart, selectionEnd, value } = this;
		const selection = value.slice(selectionStart, selectionEnd);

		const replacement = /[a-z]/.test(selection)
			? selection.toUpperCase()
			: selection.toLowerCase();

		text = (
			value.slice(0, selectionStart) +
			replacement +
			value.slice(selectionEnd)
		);

		await tick();
		this.selectionStart = selectionStart;
		this.selectionEnd = selectionEnd;
	}
</script>

<style>
	textarea {
		width: 100%;
		height: 200px;
	}
</style>

<textarea value={text} on:keydown={handleKeydown}></textarea>
```

这一章节的内容很有意思，示例很有意思，再次建议去官网亲自体验一下：[lifecycle](https://svelte.dev/tutorial/onmount)


