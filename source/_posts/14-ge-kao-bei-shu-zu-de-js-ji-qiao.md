---
title: '14 个拷贝数组的 JS 技巧'
date: 2019-11-13 14:15:03
tags: [js]
published: true
hideInList: false
feature: /post-images/14-ge-kao-bei-shu-zu-de-js-ji-qiao.jpg
---
>为了保证的可读性，本文采用意译而非直译。

数组拷贝经常被误解，但这并不是因为拷贝过程本身，而是因为缺乏对 JS 如何处理数组及其元素的理解。JS 中的数组是可变的，这说明在创建数组之后还可以修改数组的内容。
这意味着要拷贝一个数组，咱们不能简单地将旧数组分配给一个新变量，它也是一个数组。如果这样做，它们将共享相同的引用，并且在更改一个变量之后，另一个变量也将受到更改的影响。这就是我们需要克隆这个数组的原因。
接着来看看一些关于拷贝何克隆数组的有趣方法和技巧。

<!-- more -->

### 技巧 1 - 使用Array.slice方法
```javascript
const numbers = [1, 2, 3, 4, 5]

const copy = numbers.slice()
copy.push(6) // 添加新项以证明不会修改原始数组

console.log(copy)
console.log(numbers)

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 2 - 使用Array.map方法
```javascript
const numbers = [1, 2, 3, 4, 5]

const copy = numbers.map( num => num )
copy.push(6) // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 3 - 使用Array.from方法
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = Array.from(new Set(numbers));
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 4 - 使用展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = [...numbers];
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出 
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 5 - 使用 Array.of 方法和展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = Array.of(...numbers);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出 
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

Array.of() 方法创建一个具有可变数量参数的新数组实例，而不考虑参数的数量或类型。Array.of() 和 Array 构造函数之间的区别在于处理整数参数：Array.of(7) 创建一个具有单个元素 7 的数组，而 Array(7) 创建一个长度为7的空数组（注意：这是指一个有7个空位(empty)的数组，而不是由7个undefined组成的数组）。
```javascript
Array.of(7);       // [7] 
Array.of(1, 2, 3); // [1, 2, 3]

Array(7);          // [ , , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

### 技巧 6 - 使用 Array 构造函数和展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = new Array(...numbers);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出 
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 7 - 使用解构
```javascript
const numbers = [1, 2, 3, 4, 5];

const [...copy] = numbers;
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 8 - 使用 Array.concat 方法
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = numbers.concat();
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 9 - 使用 Array.push 方法和展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

let copy = [];
copy.push(...numbers);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 10 - 使用 Array.unshift 方法和展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

let copy = [];
copy.unshift(...numbers);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 11 - 使用 Array.forEach 方法和展开操作符
```javascript
const numbers = [1, 2, 3, 4, 5];

let copy = [];
numbers.forEach((value) => copy.push(value));
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 12 - 使用 for 循环
```javascript
const numbers = [1, 2, 3, 4, 5];

let copy = [];
for (let i = 0; i < numbers.length; i++) {
    copy.push(numbers[i]);
}
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 13 - 使用 Array.reduce 方法

这个做法是可行，但比较多余，少用
```javascript
const numbers = [1, 2, 3, 4, 5];

const copy = numbers.reduce((acc, x) => { acc.push(x); return acc; }, []);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 技巧 14 - 使用古老的 apply 方法
```javascript
const numbers = [1, 2, 3, 4, 5];
let copy = [];
Array.prototype.push.apply(copy, numbers);
copy.push(6); // 添加新项以证明不会修改原始数组

console.log(copy);
console.log(numbers);

// 输出
// [1, 2, 3, 4, 5, 6]
// [1, 2, 3, 4, 5]
```

### 总结
请注意，上面这些方法执行的是浅拷贝，就是数组是元素是对象的时候，咱们更改对象的值，另一个也会跟着变，就能技巧4来说，如果咱们的数组元素是对象，如下所示：
```javascript
const authors = [
  { name: '前端小智', age: 25 }, 
  { name: '王大冶', age: 30 }, 
]

const copy = [...authors ]
copy[0].name = '被更改过的前端小智'

console.log(copy)
console.log(authors)
```
输出
![](http://lionney.coding.me//post-images/1573626015252.jpg)
所以上面的技巧适合简单的数据结构，复杂的结构要使用深拷贝。数组拷贝经常被误解，但这并不是因为拷贝过程本身，而是因为缺乏对 JS 如何处理数组及其元素的理解。

作者：[前端小智](https://juejin.im/post/5dcb425f6fb9a04aa20361d8)
来源：掘金
著作权归作者所有。