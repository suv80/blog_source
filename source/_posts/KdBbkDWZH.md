---
title: 'Svelte教程翻译（十三、插槽）'
date: 2021-07-20 11:18:33
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
就像元素可以有子元素一样：

```html
<div>
	<p>I'm a child of the div</p>
</div>
```

组件也可以。但是，在组件接受子级之前，它需要知道将它们放在何处。我们使用`<slot>`元素来实现这一点。把这个放进Box.svelte里：

```html
<div class="box">
	<slot></slot>
</div>
```

你现在可以把东西放进Box里了：

```html
<Box>
	<h2>Hello!</h2>
	<p>This is a box. It can contain anything.</p>
</Box>
```

完整代码：

Box.svelte

```html
<div class="box">
	<slot></slot>
</div>

<style>
	.box {
		width: 300px;
		border: 1px solid #aaa;
		border-radius: 2px;
		box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
		padding: 1em;
		margin: 0 0 1em 0;
	}
</style>
```

App.svelte

```html
<script>
	import Box from './Box.svelte';
</script>

<Box>
	<h2>Hello!</h2>
	<p>This is a box. It can contain anything.</p>
</Box>
```

## 插槽后备内容

组件可以通过将内容放入`<slot>`元素中，为任何留空的插槽指定后备内容：

```html
<div class="box">
	<slot>
		<em>no content was provided</em>
	</slot>
</div>
```

我们现在可以创建`<Box>`的实例，而不需要任何子对象：

```html
<Box>
	<h2>Hello!</h2>
	<p>This is a box. It can contain anything.</p>
</Box>

<Box/>
```

## 具名插槽

上一个示例包含一个默认插槽，用于呈现组件的直接子级。有时您需要对放置进行更多的控制，例如使用`<ContactCard>`。在这些情况下，我们可以使用命名插槽。

在ContactCard.svelte中，为每个插槽添加名称属性：

```html
<article class="contact-card">
	<h2>
		<slot name="name">
			<span class="missing">Unknown name</span>
		</slot>
	</h2>

	<div class="address">
		<slot name="address">
			<span class="missing">Unknown address</span>
		</slot>
	</div>

	<div class="email">
		<slot name="email">
			<span class="missing">Unknown email</span>
		</slot>
	</div>
</article>
```

然后，在`<ContactCard>`组件中添加具有相应`slot="…"`属性的元素：

```html
<ContactCard>
	<span slot="name">
		P. Sherman
	</span>

	<span slot="address">
		42 Wallaby Way<br>
		Sydney
	</span>
</ContactCard>
```

完整代码：

ContactCard.svelte

```html
<article class="contact-card">
	<h2>
		<slot name="name">
			<span class="missing">Unknown name</span>
		</slot>
	</h2>

	<div class="address">
		<slot name="address">
			<span class="missing">Unknown address</span>
		</slot>
	</div>

	<div class="email">
		<slot name="email">
			<span class="missing">Unknown email</span>
		</slot>
	</div>
</article>

<style>
	.contact-card {
		width: 300px;
		border: 1px solid #aaa;
		border-radius: 2px;
		box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
		padding: 1em;
	}

	h2 {
		padding: 0 0 0.2em 0;
		margin: 0 0 1em 0;
		border-bottom: 1px solid #ff3e00
	}

	.address, .email {
		padding: 0 0 0 1.5em;
		background:  0 0 no-repeat;
		background-size: 20px 20px;
		margin: 0 0 0.5em 0;
		line-height: 1.2;
	}

	.address {
		background-image: url(tutorial/icons/map-marker.svg);
	}
	.email {
		background-image: url(tutorial/icons/email.svg);
	}
	.missing {
		color: #999;
	}
</style>
```

App.svelte

```html
<script>
	import ContactCard from './ContactCard.svelte';
</script>

<ContactCard>
	<span slot="name">
		P. Sherman
	</span>

	<span slot="address">
		42 Wallaby Way<br>
		Sydney
	</span>
</ContactCard>
```

## 插槽内容检测

在某些情况下，您可能希望根据父级是否传入特定槽的内容来控制组件的某些部分。可能在该插槽周围有一个包装器，如果插槽为空，则不希望渲染它。或者，您希望仅在存在槽的情况下应用类。您可以通过检查特殊$$slots变量的属性来实现这一点。

$$slots是一个对象，其键是父组件传入的插槽的名称。如果父级将某个槽留空，$$slots将没有该槽的条目。

请注意，在本例中，`<Project>`的两个实例都呈现了一个用于注释的容器和一个通知点，即使只有一个实例有注释。我们希望使用$$slots来确保仅在父级`<App>`传入`comments` 插槽的内容时呈现这些元素。

在Project.svelte中，更新`<article>`中的`class:has-discussion` ：

```html
<article class:has-discussion={$$slots.comments}>
```

接下来，if块中增加`$$slots.comments`判断：

```html
{#if $$slots.comments}
	<div class="discussion">
		<h3>Comments</h3>
		<slot name="comments"></slot>
	</div>
{/if}
```

现在，当`<App>`将注释槽留空时，注释容器和comments将不会呈现。

完整代码：

Project.svelte：

```html
<script>
	export let title;
	export let tasksCompleted = 0;
	export let totalTasks = 0;
</script>

<article class:has-discussion={$$slots.comments}>
	<div>
		<h2>{title}</h2>
		<p>{tasksCompleted}/{totalTasks} tasks completed</p>
	</div>
	{#if $$slots.comments}
		<div class="discussion">
			<h3>Comments</h3>
			<slot name="comments"></slot>
		</div>
	{/if}
</article>

<style>
	article {
		border: 1px #ccc solid;
		border-radius: 4px;
		position: relative;
	}

	article > div {
		padding: 1.25rem;
	}

	article.has-discussion::after {
		content: '';
		background-color: #ff3e00;
		border-radius: 10px;
		box-shadow: 0 2px 4px rgba(0,0,0,0.2);
		height: 20px;
		position: absolute;
		right: -10px;
		top: -10px;
		width: 20px;
	}

	h2,
	h3 {
		margin: 0 0 0.5rem;
	}

	h3 {
		font-size: 0.875rem;
		font-weight: 500;
		letter-spacing: 0.08em;
		text-transform: uppercase;
	}

	p {
		color: #777;
		margin: 0;
	}

	.discussion {
		background-color: #eee;
		border-top: 1px #ccc solid;
	}
</style>
```

Comment.svelte

```html
<script>
	export let name;
	export let postedAt;

	$: avatar = `https://ui-avatars.com/api/?name=${name.replace(/ /g, '+')}&rounded=true&background=ff3e00&color=fff&bold=true`;
</script>

<article>
	<div class="header">
		<img src={avatar} alt="" height="32" width="32">
		<div class="details">
			<h4>{name}</h4>
			<time datetime={postedAt.toISOString()}>{postedAt.toLocaleDateString()}</time>
		</div>
	</div>
	<div class="body">
		<slot></slot>
	</div>
</article>

<style>
	article {
		background-color: #fff;
		border: 1px #ccc solid;
		border-radius: 4px;
		padding: 1rem;
	}

	.header {
		align-items: center;
		display: flex;
	}

	.details {
		flex: 1 1 auto;
		margin-left: 0.5rem
	}

	h4 {
		margin: 0;
	}

	time {
		color: #777;
		font-size: 0.75rem;
		text-decoration: underline;
	}

	.body {
		margin-top: 0.5rem;
	}

	.body :global(p) {
		margin: 0;
	}
</style>
```

App.svelte

```html
<script>
	import Project from './Project.svelte'
	import Comment from './Comment.svelte'
</script>

<h1>
	Projects
</h1>

<ul>
	<li>
		<Project
			title="Add Typescript support"
			tasksCompleted={25}
			totalTasks={57}
		>
			<div slot="comments">
				<Comment name="Ecma Script" postedAt={new Date('2020-08-17T14:12:23')}>
					<p>Those interface tests are now passing.</p>
				</Comment>
			</div>
		</Project>
	</li>
	<li>
		<Project
			title="Update documentation"
			tasksCompleted={18}
			totalTasks={21}
		/>
	</li>
</ul>

<style>
	h1 {
		font-weight: 300;
		margin: 0 1rem;
	}

	ul {
		list-style: none;
		padding: 0;
		margin: 0.5rem;
		display: flex;
	}

	@media (max-width: 600px) {
		ul {
			flex-direction: column;
		}
	}

	li {
		padding: 0.5rem;
		flex: 1 1 50%;
		min-width: 200px;
	}
</style>
```

## 插槽Props

如果需要，可以重命名变量-在父组件中将其称为active：

```html
<Hoverable let:hovering={active}>
	<div class:active>
		{#if active}
			<p>I am being hovered upon.</p>
		{:else}
			<p>Hover over me!</p>
		{/if}
	</div>
</Hoverable>
You can have as many of these com
```

您可以拥有任意数量的这些组件，并且插槽Props将保留在声明它们的组件的本地位置。

> 命名槽也可以有Props；在具有`slot="…"`属性的元素上使用let指令，而不是在组件本身上。

完整代码：

Hoverable.svelte

```html
<script>
	let hovering;

	function enter() {
		hovering = true;
	}

	function leave() {
		hovering = false;
	}
</script>

<div on:mouseenter={enter} on:mouseleave={leave}>
	<slot hovering={hovering}></slot>
</div>
```

App.svelte

```html
<script>
	import Hoverable from './Hoverable.svelte';
</script>

<Hoverable let:hovering={active}>
	<div class:active>
		{#if active}
			<p>I am being hovered upon.</p>
		{:else}
			<p>Hover over me!</p>
		{/if}
	</div>
</Hoverable>

<Hoverable let:hovering={active}>
	<div class:active>
		{#if active}
			<p>I am being hovered upon.</p>
		{:else}
			<p>Hover over me!</p>
		{/if}
	</div>
</Hoverable>

<Hoverable let:hovering={active}>
	<div class:active>
		{#if active}
			<p>I am being hovered upon.</p>
		{:else}
			<p>Hover over me!</p>
		{/if}
	</div>
</Hoverable>

<style>
	div {
		padding: 1em;
		margin: 0 0 1em 0;
		background-color: #eee;
	}

	.active {
		background-color: #ff3e00;
		color: white;
	}
</style>
```

推荐到官网体验一下。[slot](https://svelte.dev/tutorial/slots)


