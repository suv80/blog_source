---
title: 'Svelte教程翻译（五、组件绑定）'
date: 2021-06-26 09:13:53
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 输入框绑定

通常的规则是，Svelte中的数据流是自上而下的，父组件可以在子组件上设置Props，组件可以在元素上设置属性，但不能反过来。

有时我们需要打破这个规则。以组件中的`<input>`元素为例，我们可以添加一个`on:input`事件处理程序，它将`name`的值设置为`event.target.value`，但这有点...不灵活。正如我们将看到的，对于其他形式元素，情况会更糟。

相反，我们可以使用`bind:value`指令：

```html
<input bind:value={name}>
```

这意味着对`name`值的更改不仅会更新输入值，而且对输入值的更改也会更新`name`。

在DOM中，所有内容都是字符串。在处理数字输入时，比如type="number"或者type="range"，这是没有帮助的，因为这意味着在使用它之前强制转换`input.value`类型。

使用`bind:value`，Svelte会为你自动处理：

```html
<input type=number bind:value={a} min=0 max=10>
<input type=range bind:value={a} min=0 max=10>
```

完整代码：

```html
<script>
	let a = 1;
	let b = 2;
</script>

<label>
	<input type=number bind:value={a} min=0 max=10>
	<input type=range bind:value={a} min=0 max=10>
</label>

<label>
	<input type=number bind:value={b} min=0 max=10>
	<input type=range bind:value={b} min=0 max=10>
</label>

<p>{a} + {b} = {a + b}</p>
```

## 选择框绑定

复选框用于在状态之间切换。我们不绑定到`input.value`，而是绑定到`input.checked`：

```html
<input type=checkbox bind:checked={yes}>
```

完整代码：

```html
<script>
	let yes = false;
</script>

<label>
	<input type=checkbox bind:checked={yes}>
	Yes! Send me regular email spam
</label>

{#if yes}
	<p>Thank you. We will bombard your inbox and sell your personal details.</p>
{:else}
	<p>You must opt in to continue. If you're not paying, you're the product.</p>
{/if}

<button disabled={!yes}>
	Subscribe
</button>
```

如果有多个选择框为一个值服务，比如性别、单选题目，多选题目等，则可以使用`bind:group`和`value`属性。同一组中的单选框是互斥的; 同一组中的复选框输入会以数组的形式存储。

将`bind:group`添加到每个输入：

```html
<input type=radio bind:group={scoops} value={1}>
```

在这种情况下，我们可以通过将复选框输入移动到`each`块来简化代码。首先，向`<script>`块添加一个menu变量：

```html
let menu = [
	'Cookies and cream',
	'Mint choc chip',
	'Raspberry ripple'
];
```

然后：

```html
<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} value={flavour}>
		{flavour}
	</label>
{/each}
```

现在我们可以很容易地将我们的菜单展现出来。

完整代码：

```html
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];

	function join(flavours) {
		if (flavours.length === 1) return flavours[0];
		return `${flavours.slice(0, -1).join(', ')} and ${flavours[flavours.length - 1]}`;
	}
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} value={flavour}>
		{flavour}
	</label>
{/each}

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {join(flavours)}
	</p>
{/if}
```

## textarea绑定

```html
<textarea bind:value={value}></textarea>
```

在这种情况下，变量名与绑定的元素的属性名一致，可以使用语法糖：

```html
<textarea bind:value></textarea>
```

适用于所有绑定，而不仅仅是文本绑定

## 下拉选择框绑定

```html
<select bind:value={selected} on:change="{() => answer = ''}">
```

示例代码：

```html
<script>
	let questions = [
		{ id: 1, text: `Where did you go to school?` },
		{ id: 2, text: `What is your mother's name?` },
		{ id: 3, text: `What is another personal fact that an attacker could easily find with Google?` }
	];

	let selected;

	let answer = '';

	function handleSubmit() {
		alert(`answered question ${selected.id} (${selected.text}) with "${answer}"`);
	}
</script>

<h2>Insecurity questions</h2>

<form on:submit|preventDefault={handleSubmit}>
	<select value={selected} on:change="{() => answer = ''}">
		{#each questions as question}
			<option value={question}>
				{question.text}
			</option>
		{/each}
	</select>

	<input bind:value={answer}>

	<button disabled={!answer} type=submit>
		Submit
	</button>
</form>

<p>selected question {selected ? selected.id : '[waiting...]'}</p>

<style>
	input {
		display: block;
		width: 500px;
		max-width: 100%;
	}
</style>
```

注意`<option>`的value是对象而不是字符串，Svelte不介意。

> 因为我们没有设置`selected`的初始值，所以绑定会自动将其设置为默认值（列表中的第一个，第一个option）。但是要小心，在绑定初始化之前，`selected`仍然是未定义的，所以我们不能盲目引用，例如在模板 中使用`selected.id`。

## 下拉选择框多选绑定

下拉选择框可以设置为多选，在这种情况下，选中的内容将以数组的形式存储。

示例完整代码：

```html
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];

	function join(flavours) {
		if (flavours.length === 1) return flavours[0];
		return `${flavours.slice(0, -1).join(', ')} and ${flavours[flavours.length - 1]}`;
	}
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

<select multiple bind:value={flavours}>
	{#each menu as flavour}
		<option value={flavour}>
			{flavour}
		</option>
	{/each}
</select>

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {join(flavours)}
	</p>
{/if}
```

这是我们之前用选择框编写的示例，现在用下拉选择框的多选功能重新编写了一遍。

## 支持contenteditable属性的元素绑定

具有`contenteditable="true"`属性元素支持`textContent`和`innerHTML`绑定：

```html
<div
	contenteditable="true"
	bind:innerHTML={html}
></div>
```

## each块事件绑定

```html
{#each todos as todo}
	<input
		type=checkbox
		bind:checked={todo.done}
	>

	<input
		placeholder="What needs to be done?"
		bind:value={todo.text}
	>
{/each}
```

> 请注意，与这些`<input>`元素交互会改变数组值。如果您更喜欢使用不可变数组，则应避免使用这些绑定并改用事件处理程序。

完整示例：

```html
<script>
	let todos = [
		{ done: false, text: 'finish Svelte tutorial' },
		{ done: false, text: 'build an app' },
		{ done: false, text: 'world domination' }
	];

	function add() {
		todos = todos.concat({ done: false, text: '' });
	}

	function clear() {
		todos = todos.filter(t => !t.done);
	}

	$: remaining = todos.filter(t => !t.done).length;
</script>

<h1>Todos</h1>

{#each todos as todo}
	<div class:done={todo.done}>
		<input
			type=checkbox
			checked={todo.done}
		>

		<input
			placeholder="What needs to be done?"
			value={todo.text}
		>
	</div>
{/each}

<p>{remaining} remaining</p>

<button on:click={add}>
	Add new
</button>

<button on:click={clear}>
	Clear completed
</button>

<style>
	.done {
		opacity: 0.4;
	}
</style>
```

## 媒体元素

在`<audio>`和`<video>`元素中可以绑定一些属性。这个例子演示了其中的一些。

```html
<script>
	// These values are bound to properties of the video
	let time = 0;
	let duration;
	let paused = true;

	let showControls = true;
	let showControlsTimeout;

	function handleMousemove(e) {
		// Make the controls visible, but fade out after
		// 2.5 seconds of inactivity
		clearTimeout(showControlsTimeout);
		showControlsTimeout = setTimeout(() => showControls = false, 2500);
		showControls = true;

		if (!(e.buttons & 1)) return; // mouse not down
		if (!duration) return; // video not loaded yet

		const { left, right } = this.getBoundingClientRect();
		time = duration * (e.clientX - left) / (right - left);
	}

	function handleMousedown(e) {
		// we can't rely on the built-in click event, because it fires
		// after a drag — we have to listen for clicks ourselves

		function handleMouseup() {
			if (paused) e.target.play();
			else e.target.pause();
			cancel();
		}

		function cancel() {
			e.target.removeEventListener('mouseup', handleMouseup);
		}

		e.target.addEventListener('mouseup', handleMouseup);

		setTimeout(cancel, 200);
	}

	function format(seconds) {
		if (isNaN(seconds)) return '...';

		const minutes = Math.floor(seconds / 60);
		seconds = Math.floor(seconds % 60);
		if (seconds < 10) seconds = '0' + seconds;

		return `${minutes}:${seconds}`;
	}
</script>

<h1>Caminandes: Llamigos</h1>
<p>From <a href="https://cloud.blender.org/open-projects">Blender Open Projects</a>. CC-BY</p>

<div>
	<video
		poster="https://sveltejs.github.io/assets/caminandes-llamigos.jpg"
		src="https://sveltejs.github.io/assets/caminandes-llamigos.mp4"
		on:mousemove={handleMousemove}
		on:mousedown={handleMousedown}
		bind:currentTime={time}
		bind:duration
		bind:paused>
		<track kind="captions">
	</video>

	<div class="controls" style="opacity: {duration && showControls ? 1 : 0}">
		<progress value="{(time / duration) || 0}"/>

		<div class="info">
			<span class="time">{format(time)}</span>
			<span>click anywhere to {paused ? 'play' : 'pause'} / drag to seek</span>
			<span class="time">{format(duration)}</span>
		</div>
	</div>
</div>

<style>
	div {
		position: relative;
	}

	.controls {
		position: absolute;
		top: 0;
		width: 100%;
		transition: opacity 1s;
	}

	.info {
		display: flex;
		width: 100%;
		justify-content: space-between;
	}

	span {
		padding: 0.2em 0.5em;
		color: white;
		text-shadow: 0 0 8px black;
		font-size: 1.4em;
		opacity: 0.7;
	}

	.time {
		width: 3em;
	}

	.time:last-child { text-align: right }

	progress {
		display: block;
		width: 100%;
		height: 10px;
		-webkit-appearance: none;
		appearance: none;
	}

	progress::-webkit-progress-bar {
		background-color: rgba(0,0,0,0.2);
	}

	progress::-webkit-progress-value {
		background-color: rgba(255,255,255,0.6);
	}

	video {
		width: 100%;
	}
</style>
```

当你点击视频，它会更新`time`，`duration`并且`paused`响应。这意味着我们可以使用它们来构建自定义控件。

> 通常，您会使用`currentTime`，通过侦听`timeupdate`事件进行跟踪。但是这些事件触发的频率太低，导致UI不稳定。Svelte做得更好，因为它的`currentTime`使用的是`requestAnimationFrame`.

全套绑定的`<audio>`和`<video>`是如下六个只读绑定

- `duration` (readonly) ：视频的总时长，以秒为单位
- `buffered`(readonly) ：`{start, end}`对象数组
- `seekable` （只读）：同上
- `played` （只读）：同上
- `seeking` （只读）：布尔值
- `ended` （只读）：布尔值

和五个双向绑定：

- `currentTime` ：视频中的当前时间点，以秒为单位
- `playbackRate`：播放视频的速度有多快，`1`“正常”
- `paused` ：这个应该是不言自明的
- `volume` ：音量，0到1之间的值
- `muted` ：一个布尔值，其中 true 是静音

视频还具有只读绑定`videoWidth`和`videoHeight`

## 元素尺寸绑定

每块级元素有`clientWidth`，`clientHeight`，`offsetWidth`和`offsetHeight`绑定：

```html
<div bind:clientWidth={w} bind:clientHeight={h}>
	<span style="font-size: {size}px">{text}</span>
</div>
```

这些是只读绑定，改变`w`和`h`的值不会产生任何影响。

> 使用与此类似技术的测量元素。会加大一些系统开销，因此不建议将其用于大量元素。
>
> `display: inline`不能用这种方法测量元素；包含其他元素（例如`<canvas>`）的元素也不能。在这些情况下，您需要改为测量父层元素。

完整示例代码：

```html
<script>
	let w;
	let h;
	let size = 42;
	let text = 'edit me';
</script>

<input type=range bind:value={size}>
<input bind:value={text}>

<p>size: {w}px x {h}px</p>

<div bind:clientWidth={w} bind:clientHeight={h}>
	<span style="font-size: {size}px">{text}</span>
</div>

<style>
	input { display: block; }
	div { display: inline-block; }
	span { word-break: break-all; }
</style>
```

## this

只读属性`this`绑定适用于每个元素（和组件），并允许您获取对渲染元素的引用。例如，我们可以获取对`<canvas>`元素的引用：

```html
<canvas
	bind:this={canvas}
	width={32}
	height={32}
></canvas>
```

请注意， 组件安装完毕之前，`canvas`的值是`undefined`，因此我们将逻辑放在`onMount` 生命周期函数中。

完整示例代码：

```html
<script>
	import { onMount } from 'svelte';

	let canvas;

	onMount(() => {
		const ctx = canvas.getContext('2d');
		let frame = requestAnimationFrame(loop);

		function loop(t) {
			frame = requestAnimationFrame(loop);

			const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

			for (let p = 0; p < imageData.data.length; p += 4) {
				const i = p / 4;
				const x = i % canvas.width;
				const y = i / canvas.height >>> 0;

				const r = 64 + (128 * x / canvas.width) + (64 * Math.sin(t / 1000));
				const g = 64 + (128 * y / canvas.height) + (64 * Math.cos(t / 1000));
				const b = 128;

				imageData.data[p + 0] = r;
				imageData.data[p + 1] = g;
				imageData.data[p + 2] = b;
				imageData.data[p + 3] = 255;
			}

			ctx.putImageData(imageData, 0, 0);
		}

		return () => {
			cancelAnimationFrame(frame);
		};
	});
</script>

<canvas
	bind:this={canvas}
	width={32}
	height={32}
></canvas>

<style>
	canvas {
		width: 100%;
		height: 100%;
		background-color: #666;
		-webkit-mask: url(svelte-logo-mask.svg) 50% 50% no-repeat;
		mask: url(svelte-logo-mask.svg) 50% 50% no-repeat;
	}
</style>
```

## 组件绑定

正如您可以绑定到DOM元素的属性一样，您也可以绑定到组件的props。例如，我们可以绑定Keypad组件中的`value`这个props，就好像它是一个表单元素：

```html
<Keypad bind:value={pin} on:submit={handleSubmit}/>
```

现在，当用户与键盘交互时，父组件中的`pin`值会立即更新。

> 谨慎使用组件绑定。如果您的应用程序中的数据流过多，则很难跟踪应用程序周围的数据流，尤其是在没有“单一事实来源”的情况下。

示例完整代码：

Keypad.svelte

```html
<script>
	import { createEventDispatcher } from 'svelte';

	export let value = '';

	const dispatch = createEventDispatcher();

	const select = num => () => value += num;
	const clear  = () => value = '';
	const submit = () => dispatch('submit');
</script>

<div class="keypad">
	<button on:click={select(1)}>1</button>
	<button on:click={select(2)}>2</button>
	<button on:click={select(3)}>3</button>
	<button on:click={select(4)}>4</button>
	<button on:click={select(5)}>5</button>
	<button on:click={select(6)}>6</button>
	<button on:click={select(7)}>7</button>
	<button on:click={select(8)}>8</button>
	<button on:click={select(9)}>9</button>

	<button disabled={!value} on:click={clear}>clear</button>
	<button on:click={select(0)}>0</button>
	<button disabled={!value} on:click={submit}>submit</button>
</div>

<style>
	.keypad {
		display: grid;
		grid-template-columns: repeat(3, 5em);
		grid-template-rows: repeat(4, 3em);
		grid-gap: 0.5em
	}

	button {
		margin: 0
	}
</style>
```

App.sevlte

```html
<script>
	import Keypad from './Keypad.svelte';

	let pin;
	$: view = pin ? pin.replace(/\d(?!$)/g, '•') : 'enter your pin';

	function handleSubmit() {
		alert(`submitted ${pin}`);
	}
</script>

<h1 style="color: {pin ? '#333' : '#ccc'}">{view}</h1>

<Keypad bind:value={pin} on:submit={handleSubmit}/>
```

这一部分的内容比较多，但是总结下来，知识点并不是很多，不过，实践出真知，快去官网上手练练吧！[输入框绑定](https://svelte.dev/tutorial/text-inputs)
