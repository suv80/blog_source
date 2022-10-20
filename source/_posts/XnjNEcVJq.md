---
title: 'Svelte教程翻译（十、动画）'
date: 2021-07-20 11:16:28
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
在上一章中，我们使用延迟过渡来创建元素从一个todo列表移动到另一个todo列表时的运动。

为了完成效果，我们还需要将运动应用到没有过渡的元素上。为此，我们使用animate指令。

首先，从svelte/animate导入flip函数-flip代表“First，Last，Invert，Play”：

```javascript
import { flip } from 'svelte/animate';
```

然后将其添加到`<label>`元素：

```html
<label
	in:receive="{{key: todo.id}}"
	out:send="{{key: todo.id}}"
	animate:flip
>
```

在这种情况下，移动有点慢，因此我们可以添加一个duration参数：

```html
<label
	in:receive="{{key: todo.id}}"
	out:send="{{key: todo.id}}"
	animate:flip="{{duration: 200}}"
>
```

>  duration也可以是一个`d => milliseconds`函数，其中d是元素必须移动的像素值。

请注意，所有的过渡和动画都是用CSS而不是JavaScript应用的，这意味着它们不会阻止（或被阻止）主线程。

完整代码：

```html
<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

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
				animate:flip
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
				animate:flip
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

推荐去官网体验一下：[animate](https://svelte.dev/tutorial/animate)


