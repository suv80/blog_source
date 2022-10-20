---
title: 'Svelte教程翻译（二、Props）'
date: 2021-06-24 16:57:33
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## Props

到目前为止，我们只处理内部状态，也就是说，只能在给定的组件中访问这些值。

在任何真正的应用程序中，都需要将数据从一个组件传递到其子组件。要做到这一点，我们需要声明属性，通常简称为“ props”。在Svelte中，我们使用`export`关键字来实现这一点。编辑Nested.svelte组件：

```html
<script>
	export let answer;
</script>

<p>The answer is {answer}</p>
```

>就像 $: ，刚开始可能会感觉有点奇怪。在JavaScript模块中导出通常不是这样工作的！现在就顺其自然吧ーー它很快就会成为你的第二天性。

App.svelte文件：

```html
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42}/>
```



## Props默认值

在neted.svelte中，我们可以很容易地为道具指定默认值：

```html
<script>
	export let answer = 'a mystery';
</script>
```

如果我们现在添加第二个没有传值的组件，它将会使用默认值：

```html
<Nested answer={42}/>
<Nested/>
```

输出：

```bash
The answer is 42
The answer is a mystery
```



## Props传值

如果你有一个属性对象，你或者会这样传值：

```html
<script>
	import Info from './Info.svelte';

	const pkg = {
		name: 'svelte',
		version: 3,
		speed: 'blazing',
		website: 'https://svelte.dev'
	};
</script>

<Info name={pkg.name} version={pkg.version} speed={pkg.speed} website={pkg.website}/>
```

但在Svelte中，你可以这样：

```html
<script>
	import Info from './Info.svelte';

	const pkg = {
		name: 'svelte',
		version: 3,
		speed: 'blazing',
		website: 'https://svelte.dev'
	};
</script>

<Info {...pkg}/>
```

Info.svelte代码：

```html
<script>
	export let name;
	export let version;
	export let speed;
	export let website;
</script>

<p>
	The <code>{name}</code> package is {speed} fast.
	Download version {version} from <a href="https://www.npmjs.com/package/{name}">npm</a>
	and <a href={website}>learn more here</a>
</p>
```

>当然，如果需要引用传递到组件中的所有键值，包括那些没有通过`export`声明的键值，则可以直接访问`$props`。一般不推荐使用，因为Svelte很难进行优化，但在少数情况下非常有用。

如果想亲自演练以上的功能，可以稳步到Sevlte官网教程中亲自体验：[Props](https://svelte.dev/tutorial/declaring-props)
