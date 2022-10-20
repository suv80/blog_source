---
title: 'Svelte教程翻译（十一、动作）'
date: 2021-07-20 11:17:21
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 自定义动作

动作本质上是元素级生命周期功能。它们在以下方面很有用：

- 与第三方库接口
- 延迟加载的图像
- 提示
- 添加自定义事件处理程序

在这个应用程序中，我们要使橙色框“可平移”。它有panstart、panmove和panend事件的事件处理程序，但这些不是本地DOM事件。我们得自己派他们去。首先，导入pannable函数。。。

```javascript
import { pannable } from './pannable.js';
```

然后与元素一起使用：

```html
<div class="box"
	use:pannable
	on:panstart={handlePanStart}
	on:panmove={handlePanMove}
	on:panend={handlePanEnd}
	style="transform:
		translate({$coords.x}px,{$coords.y}px)
		rotate({$coords.x * 0.2}deg)"
></div>
```

打开pannable.js文件：

```javascript
export function pannable(node) {
	let x;
	let y;

	function handleMousedown(event) {
		x = event.clientX;
		y = event.clientY;

		node.dispatchEvent(new CustomEvent('panstart', {
			detail: { x, y }
		}));

		window.addEventListener('mousemove', handleMousemove);
		window.addEventListener('mouseup', handleMouseup);
	}

	function handleMousemove(event) {
		const dx = event.clientX - x;
		const dy = event.clientY - y;
		x = event.clientX;
		y = event.clientY;

		node.dispatchEvent(new CustomEvent('panmove', {
			detail: { x, y, dx, dy }
		}));
	}

	function handleMouseup(event) {
		x = event.clientX;
		y = event.clientY;

		node.dispatchEvent(new CustomEvent('panend', {
			detail: { x, y }
		}));

		window.removeEventListener('mousemove', handleMousemove);
		window.removeEventListener('mouseup', handleMouseup);
	}

	node.addEventListener('mousedown', handleMousedown);

	return {
		destroy() {
			node.removeEventListener('mousedown', handleMousedown);
		}
	};
}
```

与转换函数类似，动作函数接收node和一些可选参数，并返回动作对象。该对象可以有一个destroy函数，在卸载元素时调用该函数。

我们希望当用户在元素上按下鼠标时触发panstart事件，当用户拖动它时触发panmove事件（用dx和dy属性显示鼠标移动的距离），当用户松开鼠标时触发panend事件。

更新pannable函数并尝试移动框。

> 这种实现是为了演示目的——更完整的一个也将考虑touch事件。

App.svelte：

```html
<script>
	import { spring } from 'svelte/motion';
	import { pannable } from './pannable.js';

	const coords = spring({ x: 0, y: 0 }, {
		stiffness: 0.2,
		damping: 0.4
	});

	function handlePanStart() {
		coords.stiffness = coords.damping = 1;
	}

	function handlePanMove(event) {
		coords.update($coords => ({
			x: $coords.x + event.detail.dx,
			y: $coords.y + event.detail.dy
		}));
	}

	function handlePanEnd(event) {
		coords.stiffness = 0.2;
		coords.damping = 0.4;
		coords.set({ x: 0, y: 0 });
	}
</script>

<style>
	.box {
		--width: 100px;
		--height: 100px;
		position: absolute;
		width: var(--width);
		height: var(--height);
		left: calc(50% - var(--width) / 2);
		top: calc(50% - var(--height) / 2);
		border-radius: 4px;
		background-color: #ff3e00;
		cursor: move;
	}
</style>

<div class="box"
	use:pannable
	on:panstart={handlePanStart}
	on:panmove={handlePanMove}
	on:panend={handlePanEnd}
	style="transform:
		translate({$coords.x}px,{$coords.y}px)
		rotate({$coords.x * 0.2}deg)"
></div>
```

推荐去官网体验一下，演示很炫酷。[actions](https://svelte.dev/tutorial/actions)

## 添加参数

像过渡和动画一样，一个动作可以采用一个参数，动作函数将与它所属的元素一起调用。

在这里，我们使用一个longpress操作，每当用户在给定的持续时间内按住按钮时，它就会触发一个具有相同名称的事件。现在，如果切换到longpress.js文件，就会看到它被硬编码为500ms。

```javascript
export function longpress(node, duration) {
	let timer;
	
	const handleMousedown = () => {
		timer = setTimeout(() => {
			node.dispatchEvent(
				new CustomEvent('longpress')
			);
		}, duration);
	};
	
	const handleMouseup = () => {
		clearTimeout(timer)
	};

	node.addEventListener('mousedown', handleMousedown);
	node.addEventListener('mouseup', handleMouseup);

	return {
		update(newDuration) {
			duration = newDuration;
		},
		destroy() {
			node.removeEventListener('mousedown', handleMousedown);
			node.removeEventListener('mouseup', handleMouseup);
		}
	};
}
```

我们可以将action函数更改为接受duration作为第二个参数，并将该duration传递给setTimeout调用：

回到App.svelte，我们可以将duration值传递给操作：

```html
<button use:longpress={duration}
```

完整的App.svelte代码：

```html
<script>
	import { longpress } from './longpress.js';

	let pressed = false;
	let duration = 2000;
</script>

<label>
	<input type=range bind:value={duration} max={2000} step={100}>
	{duration}ms
</label>

<button use:longpress={duration}
	on:longpress="{() => pressed = true}"
	on:mouseenter="{() => pressed = false}"
>press and hold</button>

{#if pressed}
	<p>congratulations, you pressed and held for {duration}ms</p>
{/if}
```

这几乎奏效，事件现在只在2秒后触发。但是如果你把持续时间缩短，它仍然需要两秒钟。

为了改变这一点，我们可以在longpress.js中添加一个update方法。只要参数发生更改，就会调用此函数：

```javascript
return {
	update(newDuration) {
		duration = newDuration;
	},
	// ...
};
```

> 如果您需要将多个参数传递给一个操作，请将它们组合到一个对象中，就像使用中的那样：longpress={duration，spiciness}}

推荐去官网体验一下。[adding-parameters-to-actions](https://svelte.dev/tutorial/adding-parameters-to-actions)


