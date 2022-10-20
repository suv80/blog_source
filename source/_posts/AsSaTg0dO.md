---
title: 'Svelte教程翻译（八、补间动画）'
date: 2021-07-20 11:13:40
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 运动

设置值和观察DOM自动更新是很酷的。知道什么更酷吗？这些值之间的差异。Svelte包括一些工具，可以帮助您构建流畅的用户界面，使用动画来传达更改。

我们先将`progress`存储更改为tweened值：

```javascript
<script>
	import { tweened } from 'svelte/motion';

	const progress = tweened(0);
</script>
```

单击按钮将使进度条变化到新值。不过，这有点机械化，令人不满意。我们需要增加一个缓慢结束的功能：

```html
<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	const progress = tweened(0, {
		duration: 400,
		easing: cubicOut
	});
</script>
```

> svelte/easing模块包含Penner-easing函数，或者您可以提供自己的`p=>t`函数，其中p和t都是介于0和1之间的值。

Tween提供的选项：

- delay：在tween开始之前的毫秒数
- duration：以毫秒为单位的tween的持续时间，或者`(from, to) => milliseconds`，允许您自定义变化。
- easing：一个 `p => t` 函数
- interpolate：`(from, to) => t => value`用于在任意值之间插值的自定义函数。默认情况下，Svelte将在数字、日期以及形式相同的数组和对象之间进行插值（只要它们仅包含数字和日期或其他有效数组和对象）。如果要插值（例如）颜色字符串或变换矩阵，请提供自定义插值器

您还可以将这些选项作为第二个参数传递给`progress.set`和`progress.update`，在这种情况下，它们将覆盖默认值。set和update方法都返回一个在tween完成时解析的promise。

完整的代码：

```html
<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	const progress = tweened(0, {
		duration: 400,
		easing: cubicOut
	});
</script>

<progress value={$progress}></progress>

<button on:click="{() => progress.set(0)}">
	0%
</button>

<button on:click="{() => progress.set(0.25)}">
	25%
</button>

<button on:click="{() => progress.set(0.5)}">
	50%
</button>

<button on:click="{() => progress.set(0.75)}">
	75%
</button>

<button on:click="{() => progress.set(1)}">
	100%
</button>

<style>
	progress {
		display: block;
		width: 100%;
	}
</style>
```

## 回弹

回弹函数是tweend函数的一种替代方法，它通常对频繁变化的值更有效。

在本例中，我们有两个存储—一个表示圆的坐标，另一个表示圆的大小。让我们将它们转换为spring：

```html
<script>
	import { spring } from 'svelte/motion';

	let coords = spring({ x: 50, y: 50 });
	let size = spring(10);
</script>
```

两个springs都有默认的stiffness和damping，它们控制着回弹的弹性。我们可以指定自己的初始值：

```javascript
let coords = spring({ x: 50, y: 50 }, {
	stiffness: 0.1,
	damping: 0.25
});
```

晃动鼠标，并尝试拖动滑块，以了解它们如何影响回弹的行为。请注意，可以在回弹仍处于运动状态时调整这些值。

完整代码：

```html
<script>
	import { spring } from 'svelte/motion';

	let coords = spring({ x: 50, y: 50 }, {
		stiffness: 0.1,
		damping: 0.25
	});

	let size = spring(10);
</script>

<div style="position: absolute; right: 1em;">
	<label>
		<h3>stiffness ({coords.stiffness})</h3>
		<input bind:value={coords.stiffness} type="range" min="0" max="1" step="0.01">
	</label>

	<label>
		<h3>damping ({coords.damping})</h3>
		<input bind:value={coords.damping} type="range" min="0" max="1" step="0.01">
	</label>
</div>

<svg
	on:mousemove="{e => coords.set({ x: e.clientX, y: e.clientY })}"
	on:mousedown="{() => size.set(30)}"
	on:mouseup="{() => size.set(10)}"
>
	<circle cx={$coords.x} cy={$coords.y} r={$size}/>
</svg>

<style>
	svg {
		width: 100%;
		height: 100%;
	}
	circle {
		fill: #ff3e00;
	}
</style>
```

这节是动画效果，强烈推荐去官网体验一下。[补间动画](https://svelte.dev/tutorial/tweened)


