---
title: 'Svelte教程翻译（一、响应）'
date: 2021-06-24 16:55:58
tags: [Svelte,js]
published: true
hideInList: false
feature: 
isTop: false
---
## 响应

Svelte的核心是一个强大的响应系统，可以使DOM与应用程序状态保持同步ーー例如，响应一个事件。

为了演示它，我们首先需要触发一个事件的程序:

```html
<button on:click={handleClick}>
```

在handleClick函数中，我们需要做的就是改变 count 的值：

```javascript
function handleClick() {
	count += 1;
}
```

完整代码：

```html
<script>
	let count = 0;

	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```



## 响应声明

当组件的状态发生变化时，Svelte会自动更新DOM。通常，组件状态的某些部分需要从其他部分计算(例如从firstname和lastname派生的fullname) ，并在它们发生更改时重新计算。

对于这些，我们这样做声明：

```javascript
let count = 0;
$: doubled = count * 2;
```

>不要担心，这看起来虽然有点怪异，但它是有效的JavaScript，Svelte将其解释为“当任何引用的值发生变化时，运行这段代码”。一旦你习惯了，就回不去了。

让我们在HTML中实现翻倍效果：

```html
<p>{count} doubled is {doubled}</p>
```

当然，您可以只在HTML中编写`{count * 2}` ，而不必使用响应。当您需要多次引用响应时，或者您的值依赖于其他响应时，响应就变得特别有价值。

完整代码：

```html
<script>
	let count = 0;

	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

我们并不局限于响应变量ーー我们也可以在响应中运行任意语句。例如，如果count的值发生变化，我们可以打印输出：

```javascript
$: console.log(`the count is ${count}`);
```

你也可以很容易地将语句组合成一个代码块：

```javascript
$: {
	console.log(`the count is ${count}`);
	alert(`I SAID THE COUNT IS ${count}`);
}
```

你甚至可以把` $:`放在判断分支之类的语法前面：

```javascript
$: if (count >= 10) {
	alert(`count is dangerously high!`);
	count = 9;
}
```

完整的代码如下：

```html
<script>
	let count = 0;

	$: if (count >= 10) {
		alert(`count is dangerously high!`);
		count = 9;
	}

	function handleClick() {
		count += 1;
	}
</script>

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```



## 更新数组和对象

因为Svelte的响应是由赋值触发的，所以使用像push和splice这样的数组方法不会自动导致更新。

解决这个问题的一个方法是添加一段改变数组长度的代码：

```javascript
function addNumber() {
	numbers.push(numbers.length + 1);
	numbers = numbers;
}
```

但是还有一个更为简洁的解决方案：

```javascript
function addNumber() {
	numbers = [...numbers, numbers.length + 1];
}
```

您可以使用类似的模式来替换pop、shift、unshift和concat。

对数组和对象属性的赋值ーー例如`obj.foo+=1`或`array[i]=x--`与对值本身的赋值工作方式相同。

```javascript
function addNumber() {
	numbers[numbers.length] = numbers.length + 1;
}
```

一个简单的经验法则：更新后的变量的名称必须出现在赋值的左侧。比如这个：

```javascript
const foo = obj.foo;
foo.bar = 'baz';
```

`obj.foo.bar`是不会更新的，除非你用`obj=obj`。

完整代码：

```html
<script>
	let numbers = [1, 2, 3, 4];

	function addNumber() {
		numbers = [...numbers, numbers.length + 1];
	}

	$: sum = numbers.reduce((t, n) => t + n, 0);
</script>

<p>{numbers.join(' + ')} = {sum}</p>

<button on:click={addNumber}>
	Add a number
</button>
```

如果想亲自演练以上的功能，可以稳步到Sevlte官网教程中亲自体验：[reactive](https://svelte.dev/tutorial/reactive-assignments)


