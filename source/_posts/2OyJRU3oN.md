---
title: 'Svelte教程翻译（十四、上下文API）'
date: 2021-08-06 14:42:34
tags: [js,Svelte]
published: true
hideInList: false
feature: 
isTop: false
---
## 上下文API

上下文 API 为组件提供了一种相互“对话”的机制，无需将数据和函数作为props传递，也无需分派大量事件。这是一项高级功能，但很有用。

以使用Mapbox GL地图的示例应用程序为例。我们希望使用`<MapMarker>`组件显示标记，但我们不希望必须在每个组件上传递对基础Mapbox实例的引用作为prop。

上下文API有两部分：setContext和getContext。如果组件调用`setContext(key,context)`，那么任何子组件都可以使用`const context=getContext(key)`检索上下文。

让我们先设置上下文。在Map.svelte中，从svelte导入setContext，从mapbox.js导入key，并调用setContext：

```javascript
import { onMount, setContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

setContext(key, {
	getMap: () => map
});
```

上下文对象可以是您喜欢的任何对象。与生命周期函数一样，组件初始化期间必须调用setContext和getContext。之后调用它（例如在onMount内部调用）将抛出一个错误。在本例中，由于直到组件挂载后才创建映射，因此我们的上下文对象包含一个getMap函数，而不是映射本身。

在MapMarker.svelte中，我们现在可以获得对Mapbox实例的引用：

```javascript
import { getContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

const { getMap } = getContext(key);
const map = getMap();
```

标签现在可以添加到地图中。

> 一个更完善的`<MapMarker>`版本也可以处理删除和prop更改，但我们这里只演示上下文。

## 上下文关键字

在mapbox.js中，您将看到以下行：

```javascript
const key = {};
```

我们可以使用任何名称作为键：例如，我们可以使用`setContext('mapbox'，…)`。使用字符串的缺点是不同的组件库可能会意外地使用同一个；使用对象意味着保证键在任何情况下都不会冲突（因为对象本身只有引用等式，即`{}!=={}`而"x"==="x"），即使在多个组件层上有多个不同的上下文操作。

## 上下文和存储

上下文和存储似乎相似。它们的不同之处在于，应用程序的任何部分都可以使用存储，而上下文仅对组件及其子组件可用。如果您希望使用组件的多个实例，而其中一个实例的状态不干扰其他实例的状态，这将非常有用。

事实上，你可以将两者结合使用。由于上下文不是被动的，因此随时间变化的值应使用存储：

```javascript
const { these, are, stores } = getContext(...);
```

完整代码：

mapbox.js：

```javascript
import mapbox from 'mapbox-gl';

// https://docs.mapbox.com/help/glossary/access-token/
mapbox.accessToken = MAPBOX_ACCESS_TOKEN;

const key = {};

export { mapbox, key };
```

MapMarker.svelte：

```html
<script>
	import { getContext } from 'svelte';
	import { mapbox, key } from './mapbox.js';

	const { getMap } = getContext(key);
	const map = getMap();

	export let lat;
	export let lon;
	export let label;

	const popup = new mapbox.Popup({ offset: 25 })
		.setText(label);

	const marker = new mapbox.Marker()
		.setLngLat([lon, lat])
		.setPopup(popup)
		.addTo(map);
</script>
```

Map.svelte：

```html
<script>
	import { onMount, setContext } from 'svelte';
	import { mapbox, key } from './mapbox.js';

	setContext(key, {
		getMap: () => map
	});

	export let lat;
	export let lon;
	export let zoom;

	let container;
	let map;

	onMount(() => {
		const link = document.createElement('link');
		link.rel = 'stylesheet';
		link.href = 'https://unpkg.com/mapbox-gl/dist/mapbox-gl.css';

		link.onload = () => {
			map = new mapbox.Map({
				container,
				style: 'mapbox://styles/mapbox/streets-v9',
				center: [lon, lat],
				zoom
			});
		};

		document.head.appendChild(link);

		return () => {
			map.remove();
			link.parentNode.removeChild(link);
		};
	});
</script>

<div bind:this={container}>
	{#if map}
		<slot></slot>
	{/if}
</div>

<style>
	div {
		width: 100%;
		height: 100%;
	}
</style>
```

App.sevlte：

```html
<script>
	import Map from './Map.svelte';
	import MapMarker from './MapMarker.svelte';
</script>

<Map lat={35} lon={-84} zoom={3.5}>
	<MapMarker lat={37.8225} lon={-122.0024} label="Svelte Body Shaping"/>
	<MapMarker lat={33.8981} lon={-118.4169} label="Svelte Barbershop & Essentials"/>
	<MapMarker lat={29.7230} lon={-95.4189} label="Svelte Waxing Studio"/>
	<MapMarker lat={28.3378} lon={-81.3966} label="Svelte 30 Nutritional Consultants"/>
	<MapMarker lat={40.6483} lon={-74.0237} label="Svelte Brands LLC"/>
	<MapMarker lat={40.6986} lon={-74.4100} label="Svelte Medical Systems"/>
</Map>
```

这一部分概念比较多，实例很丰富，推荐去官网看一下实例：[context-api](https://svelte.dev/tutorial/context-api)


