---
title: 'Svelte教程翻译（三、逻辑渲染）'
date: 2021-06-24 16:58:24
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 条件渲染

HTML没有表达逻辑的方式，比如条件和循环，Svelte有。

为了有条件地呈现某些内容，我们将其包装在`if`块中：

```html
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{/if}

{#if !user.loggedIn}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

完整代码如下：

```html
<script>
	let user = { loggedIn: false };

	function toggle() {
		user.loggedIn = !user.loggedIn;
	}
</script>

{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{/if}

{#if !user.loggedIn}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

点击按钮，按钮将在【Log out】与【Log in】之间切换。

因为有两个条件：`if user.loggetin`和`if！Loggetin`，它们是互斥的，我们可以通过使用`else`块来简化这个组件：

```html
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

> `#`字符总是表示块开始标记。`/`字符总是表示块结束标记。`:`字符，如`{:else}` ，表示块继续标记。别担心，你已经学会了几乎所有Svelte新增加到HTML中的语法。

完整代码如下：

```html
<script>
	let user = { loggedIn: false };

	function toggle() {
		user.loggedIn = !user.loggedIn;
	}
</script>

{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```

噢，忘记了，还有多条件的情况：

```html
<script>
	let x = 7;
</script>

{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```

## 循环渲染

如果你需要循环遍历数据列表，使用`each`块：

```html
<ul>
	{#each cats as cat}
		<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
			{cat.name}
		</a></li>
	{/each}
</ul>
```

> 表达式(本例中为cats)可以是任何数组或类似数组的对象(即它具有length属性)。您可以这样使用`each [...iterable]`。

你可以使用第二个参数作为当前遍历对象的索引变量，如下所示：

```html
{#each cats as cat, i}
	<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
		{i + 1}: {cat.name}
	</a></li>
{/each}
```

> 如果您愿意，可以使用解构方式`each cats as {id,name}`，这样可以用`id`和`name`替换`cat.id`和`cat.name`。

完整代码如下：

```html
<script>
	let cats = [
		{ id: 'J---aiyznGQ', name: 'Keyboard Cat' },
		{ id: 'z_AbfPXTKms', name: 'Maru' },
		{ id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
	];
</script>

<h1>The Famous Cats of YouTube</h1>

<ul>
	{#each cats as { id, name }, i}
		<li><a target="_blank" href="https://www.youtube.com/watch?v={id}">
			{i + 1}: {name}
		</a></li>
	{/each}
</ul>
```

## key的作用及使用

用话术描述有点吃力，看例子：

![image-20210624160215738](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210624160215738.png)

下面，让我们点击【Remove first thing】按钮，这里，你要知道，删除第一行，你想要得到怎样的结果：

![image-20210624160334482](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210624160334482.png)

噢，NO！！！这不是我想要的，我想这也不是你想要的结果，banana怎么会是苹果？

如何处理？

这时key登场了。

没有key的代码：

```html
<button on:click={handleClick}>
	Remove first thing
</button>

{#each things as thing}
	<Thing name={thing.name}/>
{/each}
```

增加key的代码：

```html
<button on:click={handleClick}>
	Remove first thing
</button>

{#each things as thing (thing.id) }
	<Thing name={thing.name}/>
{/each}
```

看出差别了么？这里，`thing.id`是密钥，它告诉Svelte如何在组件更新时确定要更改哪个DOM节点。

> 您可以使用任何对象作为键，因为Svelte在内部使用Map。换句话说，您可以使用`thing`而不是`thing.id`。但是，使用字符串或数字通常更安全，因为这意味着标识不会引用等式，例如，当使用来自API服务器的新数据进行更新时。

完整代码如下：

文件App.sevlte

```html
<script>
	import Thing from './Thing.svelte';

	let things = [
		{ id: 1, name: 'apple' },
		{ id: 2, name: 'banana' },
		{ id: 3, name: 'carrot' },
		{ id: 4, name: 'doughnut' },
		{ id: 5, name: 'egg' },
	];

	function handleClick() {
		things = things.slice(1);
	}
</script>

<button on:click={handleClick}>
	Remove first thing
</button>

{#each things as thing (thing.id) }
	<Thing name={thing.name}/>
{/each}
```

文件Thing.sevlte

```html
<script>
	const emojis = {
        apple: "🍎",
        banana: "🍌",
        carrot: "🥕",
        doughnut: "🍩",
        egg: "🥚"
	}

	// the name is updated whenever the prop value changes...
	export let name;

	// ...but the "emoji" variable is fixed upon initialisation of the component
	const emoji = emojis[name];
</script>

<p>
	<span>The emoji for { name } is { emoji }</span>
</p>

<style>
	p {
		margin: 0.8em 0;
	}
	span {
		display: inline-block;
		padding: 0.2em 1em 0.3em;
		text-align: center;
		border-radius: 0.2em;
		background-color: #FFDFD3;
	}
</style>
```

## 异步渲染

看文档时，当看到这个地方的时候，我眼前一亮，下面来介绍一下Sevlte中，给HTML增加的异步渲染功能块。

大多数web应用程序在某些时候必须处理异步数据。可以很容易地在你的标记中等待数据返回：

```html
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

> 只考虑最新的promise，这意味着您不需要担心时序问题。

如果你知道你的promise不会被拒绝，你可以省略`catch`块。如果你不想显示任何内容，你也可以省略第一块，直到promise结束：

```ht
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```

完整的代码如下：

```html
<script>
	async function getRandomNumber() {
		const res = await fetch(`tutorial/random-number`);
		const text = await res.text();

		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}
	
	let promise = getRandomNumber();

	function handleClick() {
		promise = getRandomNumber();
	}
</script>

<button on:click={handleClick}>
	generate random number
</button>

{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

运行结果：

![image-20210624161849629](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210624161849629.png)

![image-20210624161910270](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210624161910270.png)

或者

![image-20210624161959885](https://gitee.com/limeng1984/pstore/raw/master/blog/image-20210624161959885.png)

如果你觉得看得不过瘾，想亲手试试，可以前往官方网站在线体验一下：[await-blocks](https://svelte.dev/tutorial/await-blocks)


