---
title: '通过 20 个棘手的ES6面试问题来提高咱们的 JS 技能'
date: 2019-11-12 10:43:53
tags: [js,es6]
published: true
hideInList: false
feature: /post-images/tong-guo-20-ge-ji-shou-de-es6-mian-shi-wen-ti-lai-ti-gao-zan-men-de-js-ji-neng.jpg
---
ECMAScript 6（以下简称ES6）是 JS 语言的下一代标准，已经在2015年6月正式发布了。它的目标，是使得 JS 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。接下来咱们来看看 20 道棘手的面试题，通过做题，顺带提升一下咱们的 JS 的技能。

<!-- more -->

### 问题1：可以解释一下 ES5 和ES6的区别吗?

>主题: JavaScript
>难度: ⭐⭐⭐

ECMAScript 5 (ES5)：ECMAScript 的第五版，于2009年标准化，该标准已在所有现代浏览器中完全支持。
ECMAScript 6 (ES6)/ ECMAScript 2015 (ES2015):ECMAscript 第 6 版，2015 年标准化。这个标准已经在大多数现代浏览器中部分实现。

以下是ES5和ES6之间的一些主要区别：

#### 箭头函数和字符串插值

```javascript
const greetings = (name) => {
  return `hello ${name}`;
}
```
也可以这样写：
```javascript
const greetings = name => `hello ${name}`;    
```

const：const 表示无法修改变量的原始值。需要注意的是，const表示对值的常量引用，咱们可以改变被引用的对象的属性值，但不能改变引用本身。
```javascript
const NAMES = [];
NAMES.push("Jim");
console.log(NAMES.length === 1); // true
NAMES = ["Steve", "John"]; // error    
```
**块作用域：ES6 中 let, const 会创建块级作用域，不会像 var 声明变量一样会被提升。**
默认参数：默认参数使咱们可以使用默认值初始化函数。当参数省略或 undefined 时使用默认参数值。
```javascript
function multiply (a, b = 2) {
   return a * b;
}
multiply(5); // 10    
```

#### 类定义与继承

ES6 引入了对类(class关键字)、构造函数(constructor关键字)和 extend 关键字(用于继承)的语言支持。

**for-of 运算符**

for...of 语句创建一个遍历可迭代对象的循环。

**展开操作符**
```javascript
const obj1 = { a: 1, b: 2 }
const obj2 = { a: 2, c: 3, d: 4}
const obj3 = {...obj1, ...obj2}    
```

**Promises**: Promises 提供了一种机制来处理异步操作的结果和错误。可以使用回调来完成相同的事情，但是Promises 通过方法链接和简洁的错误处理来提高可读性。

```javascript
const isGreater = (a, b) => {
  return new Promise ((resolve, reject) => {
    if(a > b) {
      resolve(true)
    } else {
      reject(false)
    }
    })
}
isGreater(1, 2)
  .then(result => {
    console.log('greater')
  })
 .catch(result => {
    console.log('smaller')
 })    
```

**模块导出**
```javascript
const myModule = { x: 1, y: () => { console.log('This is ES5') }}
export default myModule;   
```
**和导入**
```javascript
import myModule from './myModule';
```

### 问题 2：什么是 IIFE (立即调用的函数表达式)

>主题: JavaScript
>难度: ⭐⭐⭐

IIFE是一个立即调用的函数表达式，它在创建后立即执行
```javascript
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!"
```
常常使用此模式来避免污染全局命名空间，因为在IIFE中使用的所有变量(与任何其他普通函数一样)在其作用域之外都是不可见的。

### 问题 3：何时在 ES6 中使用箭头函数？

>主题: JavaScript
>难度: ⭐⭐⭐

以下是一些经验分享：

- 在全局作用域内和Object.prototype属性中使用 function 。
- 为对象构造函数使用 class。
- 其它情况使用箭头函数。

为啥大多数情况都使用箭头函数？

- 作用域安全性:当箭头函数被一致使用时，所有东西都保证使用与根对象相同的thisObject。如果一个标准函数回调与一堆箭头函数混合在一起，那么作用域就有可能变得混乱。
- 紧凑性:箭头函数更容易读写。
- 清晰度:使用箭头函数可明确知道当前 this 指向。

### 问题 4:将 Symbol 引入ES6 的目的是什么？

>主题: JavaScript
>难度: ⭐⭐⭐

Symbol 是一种新的、特殊的对象，可以用作对象中惟一的属性名。使用 Symbol 替换string 可以避免不同的模块属性的冲突。还可以将Symbol设置为私有，以便尚无直接访问Symbol权限的任何人都不能访问它们的属性。
Symbol 是JS新的基本数据类型。与number、string和boolean 原始类型一样，Symbol 也有一个用于创建它们的函数。与其他原始类型不同，Symbol没有字面量语法。创建它们的唯一方法是使用以下方法中的Symbol构造函数
```javascript
let symbol = Symbol();    
```

### 问题 5: 在 ES6 中使用展开(spread)语法有什么好处? 它与剩余(rest)语法有什么不同?

>主题: JavaScript
>难度: ⭐⭐⭐

ES6 的展开语法在以函数形式进行编码时非常有用，因为咱们可以轻松地创建数组或对象的副本，而无需求助于Object.create，slice或库函数。Redux 和rx.js项目中经常使用此特性。
```javascript
function putDookieInAnyArray(arr) {
  return [...arr, 'dookie'];
}

const result = putDookieInAnyArray(['I', 'really', "don't", 'like']); 
// ["I", "really", "don't", "like", "dookie"]

const person = {
  name: 'Todd',
  age: 29,
};

const copyOfTodd = { ...person };  
```
ES6 的 rest 语法提供了一种捷径，其中包括要传递给函数的任意数量的参数。
就像展开语法的逆过程一样，它将数据放入并填充到数组中而不是展开数组，并且它在函数变量以及数组和对象解构分中也经常用到。
```javascript
function addFiveToABunchOfNumbers(...numbers) {
  return numbers.map(x => x + 5);
}

const result = addFiveToABunchOfNumbers(4, 5, 6, 7, 8, 9, 10); 
// [9, 10, 11, 12, 13, 14, 15]

const [a, b, ...rest] = [1, 2, 3, 4]; // a: 1, b: 2, rest: [3, 4]

const { e, f, ...others } = {
  e: 1,
  f: 2,
  g: 3,
  h: 4,
}; // e: 1, f: 2, others: { g: 3, h: 4 }   
```

### 问题 6: ES6 类和 ES5 函数构造函数有什么区别？

>主题: JavaScript
>难度: ⭐⭐⭐

```javascript
// ES5 Function Constructor
function Person(name) {
  this.name = name;
}

// ES6 Class
class Person {
  constructor(name) {
    this.name = name;
  }
}  
```

对于简单的构造函数，它们看起来非常相似。
构造函数的主要区别在于使用继承。如果咱们创建一个继承Person类的Student子类并添加一个studentId字段，以下是两种方式的使用：
```javascript
// ES5 Function Constructor
function Student(name, studentID) {
  // 调用你类的构造函数以初始化你类派生的成员。
  Person.call(this. name)
  // 初始化子类的成员。
  this.studentId = studentId
}

Student.prototype = Object.create(Person.prototype)
Student.prototype.constructor = Student

// ES6 Class
class Student extends Person {
  constructor(name, studentId) {
    super(name)
    this.studentId = studentId
  }
}
```

在 ES5 中使用继承要复杂得多，而且 ES6 版本更容易理解和记住。

### 问题 7: .call 和 .apply 区别是啥？

>主题: JavaScript
>难度: ⭐⭐⭐

.call和.apply均用于调用函数，并且第一个参数将用作函数中this的值。但是，.call将逗号分隔的参数作为下一个参数，而.apply将参数数组作为下一个参数。简单记忆法：C用于call和逗号分隔，A用于apply和参数数组。
```javascript
function add(a, b) {
  return a + b;
}

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3   
```

### 问题 8: 为什么要使用 ES6 类？

>主题: JavaScript
>难度: ⭐⭐⭐

选择使用类的一些原因：

语法更简单，更不容易出错。

使用新语法比使用旧语法更容易(而且更不易出错)地设置继承层次结构。

class可以避免构造函数中使用new的常见错误（如果构造函数不是有效的对象，则使构造函数抛出异常）。

用新语法调用父原型方法的版本比旧语法要简单得多，用super.method()代替ParentConstructor.prototype.method.call(this) 或Object.getPrototypeOf(Object.getPrototypeOf(this)).method.call(this)

考虑下面代码：
![](http://lionney.coding.me//post-images/1573528413636.jpg)

使用 ES6 实现上述功能：
![](http://lionney.coding.me//post-images/1573528421667.jpg)

### 问题 9: 在 JS 中定义枚举的首选语法是什么

>主题: JavaScript
>难度: ⭐⭐⭐

可以 Object.freeze 来实现枚举
```javascript
var DaysEnum = Object.freeze({
    "monday": 1,
    "tuesday": 2,
    "wednesday": 3,
    ...
})
```
或者
```javascript
var DaysEnum = {
    "monday": 1,
    "tuesday": 2,
    "wednesday": 3,
    ...
}
Object.freeze(DaysEnum)
```
但是，这阻止咱们把值分配给变量：
```javascript
let day = DaysEnum.tuesday
day = 298832342 // 不会报错
```

### 问题 10: 解释一下 Object.freeze() 和 const 的区别

>主题: JavaScript
>难度: ⭐⭐⭐

const和Object.freeze是两个完全不同的概念。
const 声明一个只读的变量，一旦声明，常量的值就不可改变：
```javascript
const person = {
    name: "Leonardo"
};
let animal = {
    species: "snake"
};
person = animal; // ERROR "person" is read-only    
```
Object.freeze适用于值，更具体地说，适用于对象值，它使对象不可变，即不能更改其属性。
```javascript
let person = {
    name: "Leonardo"
};
let animal = {
    species: "snake"
};
Object.freeze(person);
person.name = "Lima"; //TypeError: Cannot assign to read only property 'name' of object
console.log(person); 
```

### 问题 11: JS 的提升是什么

>主题: JavaScript
>难度: ⭐⭐⭐⭐

提升是指 JS 解释器将所有变量和函数声明移动到当前作用域顶部的操作，提升有两种类型

- 变量提升
- 函数提升

只要一个var(或函数声明)出现在一个作用域内，这个声明就被认为属于整个作用域，并且可以在任何地方访问。
```javascript
var a = 2
foo() // 正常运行, foo 已被提升

function foo() {
  a = 3
  console.log(a)   // 3
  var a                        
}

console.log( a )   // 2
```

### 问题 12: 解释一下原型设计模式(Prototype Pattern)

>主题: JavaScript
>难度: ⭐⭐⭐⭐

原型模式会创建新的对象，而不是创建未初始化的对象，它会返回使用从原型或样本对象复制的值进行初始化的对象。原型模式也称为属性模式。
原型模式有用的一个例子是使用与数据库中的默认值匹配的值初始化业务对象。原型对象保留默认值，这些默认值将被复制到新创建的业务对象中。
传统语言很少使用原型模式，但是JavaScript作为一种原型语言，在构建新对象及其原型时使用这种模式。

### 问题 13: ES6 中的临时死区是什么

>主题: JavaScript
>难度: ⭐⭐⭐⭐

在 ES6 中，let 和const 跟 var、class和function一样也会被提升，只是在进入作用域和被声明之间有一段时间不能访问它们，这段时间是临时死区(TDZ)。
```javascript
//console.log(aLet)  // would throw ReferenceError

let aLet;
console.log(aLet); // undefined
aLet = 10;
console.log(aLet); // 10
```

### 问题 14: 什么时候不使用箭头函数? 说出三个或更多的例子

>主题: JavaScript
>难度: ⭐⭐⭐⭐

不应该使用箭头函数一些情况：

- 当想要函数被提升时(箭头函数是匿名的)
- 要在函数中使用this/arguments时，由于箭头函数本身不具有this/arguments，因此它们取决于外部上下文
- 使用命名函数(箭头函数是匿名的)
- 使用函数作为构造函数时(箭头函数没有构造函数)
- 当想在对象字面是以将函数作为属性添加并在其中使用对象时，因为咱们无法访问 this 即对象本身。

### 问题 15: ES6 中的 WeakMap的实际用途是什么？

>主题: JavaScript
>难度: ⭐⭐⭐⭐

WeakMaps 提供了一种从外部扩展对象而不影响垃圾收集的方法。当咱们想要扩展一个对象，但是因为它是封闭的或者来自外部源而不能扩展时，可以应用WeakMap。
WeakMap只适用于 ES6 或以上版本。WeakMap是键和值对的集合，其中键必须是对象。
```javascript
var map = new WeakMap();
var pavloHero = {
    first: "Pavlo",
    last: "Hero"
};
var gabrielFranco = {
    first: "Gabriel",
    last: "Franco"
};
map.set(pavloHero, "This is Hero");
map.set(gabrielFranco, "This is Franco");
console.log(map.get(pavloHero)); //This is Hero
```
WeakMaps的有趣之处在于，它包含了对map内部键的弱引用。弱引用意味着如果对象被销毁，垃圾收集器将从WeakMap中删除整个条目，从而释放内存。

### 问题 16: 说明下列方法为何不能用作 IIFE，要使其成为 IIFE，需要进行哪些更改？

>主题: JavaScript
>难度: ⭐⭐⭐⭐

```javascript
function foo(){ }();
```
IIFE 代表立即调用的函数表达式。JS解析器读取函数foo(){}();作为函数foo(){}和();，前者是一个函数声明，后者(一对括号)是尝试调用一个函数，但没有指定名称，因此它抛出Uncaught SyntaxError: Unexpected token 异常。
咱们可以使用void操作符:void function foo(){ }();。不幸的是，这种方法有一个问题。给定表达式的求值总是undefined的，所以如果IIFE 函数有返回值，则不能使用它，如下所示：
```javascript
const foo = void
function bar() {
    console.log('前端小智')
    return 'foo';
}();

console.log(foo); // undefined
```

### 问题 17: 能否比较模块模式与构造函数/原型模式的用法？

>主题: JavaScript
>难度: ⭐⭐⭐⭐

模块模式通常用于命名空间，在该模式中，使用单个实例作为存储来对相关函数和对象进行分组。这是一个不同于原型设计的用例,它们并不是相互排斥,咱们可以同时使用它们(例如，将一个构造函数放在一个模块中，并使用new MyNamespace.MyModule.MyClass(arguments) )。
构造函数和原型是实现类和实例的合理方法之一。它们与模型并不完全对应，因此通常需要选择一个特定的scheme或辅助方法来实现原型中的类。

### 问题 18: ES6 Map 和 WeakMap 有什么区别？

>主题: JavaScript
>难度: ⭐⭐⭐⭐⭐

当它们的键/值引用的对象被删除时，它们的行为都不同，以下面的代码为例:
```javascript
var map = new Map()
var weakmap = new WeakMap()

(function() {
    var a = {
        x: 12
    };
    var b = {
        y: 12
    };

    map.set(a, 1);
    weakmap.set(b, 2);
})()
```
执行上面的 IIFE，就无法再引用{x：12}和{y：12}。垃圾收集器继续运行，并从 WeakMa中删除键b指针，还从内存中删除了{y：12}。
但在使用 Map的情况下，垃圾收集器不会从Map中删除指针，也不会从内存中删除{x：12}。
WeakMap 允许垃圾收集器执行其回收任务，但Map不允许。对于手动编写的 Map，数组将保留对键对象的引用，以防止被垃圾回收。但在WeakMap中，对键对象的引用被“弱”保留，这意味着在没有其他对象引用的情况下，它们不会阻止垃圾回收。

### 问题 19: 举一个柯里化函数的例子，并说明柯里化的好处？

>主题: JavaScript
>难度: ⭐⭐⭐⭐⭐

柯里化是一种模式，其中一个具有多个参数的函数被分解成多个函数，当被串联调用时，这些函数将一次累加一个所需的所有参数。这种技术有助于使用函数式编写的代码更容易阅读和编写。需要注意的是，要实现一个函数，它需要从一个函数开始，然后分解成一系列函数，每个函数接受一个参数。
```javascript
function curry(fn) {
  if (fn.length === 0) {
    return fn;
  }

  function _curried(depth, args) {
    return function(newArgument) {
      if (depth - 1 === 0) {
        return fn(...args, newArgument);
      }
      return _curried(depth - 1, [...args, newArgument]);
    };
  }

  return _curried(fn.length, []);
}

function add(a, b) {
  return a + b;
}

var curriedAdd = curry(add);
var addFive = curriedAdd(5);

var result = [0, 1, 2, 3, 4, 5].map(addFive); // [5, 6, 7, 8, 9, 10]
```

### 问题 20: 如何在 JS 中“深冻结”对象

>主题: JavaScript
>难度: ⭐⭐⭐⭐⭐

如果咱们想要确保对象被深冻结，就必须创建一个递归函数来冻结对象类型的每个属性：

没有深冻结
```javascript
let person = {
    name: "Leonardo",
    profession: {
        name: "developer"
    }
};
Object.freeze(person); 
person.profession.name = "doctor";
console.log(person); //output { name: 'Leonardo', profession: { name: 'doctor' } }
```
深冻结
```javascript
function deepFreeze(object) {
    let propNames = Object.getOwnPropertyNames(object);
    for (let name of propNames) {
        let value = object[name];
        object[name] = value && typeof value === "object" ?
            deepFreeze(value) : value;
    }
    return Object.freeze(object);
}
let person = {
    name: "Leonardo",
    profession: {
        name: "developer"
    }
};
deepFreeze(person);
person.profession.name = "doctor"; // TypeError: Cannot assign to read only property 'name' of object
```

***

作者：[前端小智](https://juejin.im/post/5dc8a231f265da4d40712f8a)
来源：掘金
著作权归作者所有。