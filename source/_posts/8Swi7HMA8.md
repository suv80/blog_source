---
title: '代码详解：Async/Await优于基础Promises的7大原因'
date: 2020-03-02 18:21:23
tags: [js]
published: true
hideInList: false
feature: 
isTop: false
---
Async/await已被引入NodeJS 7.6，当前能在几乎所有先进的浏览器上运行。这绝对是自2017年以来JavaScript最好的附加语法，没有之一。
<!-- more -->

### Async/Await

- Async/await是一种编写异步代码的新方法。以前编写异步代码会用callbacks和promises。
- Async/await 实际上只是一种基于promises的糖衣语法，不能与基础callbacks或节点callbacks一同使用。
- Async/await和promises一样，都是非堵塞式的。
- Async/await让异步代码更具同步代码风格，这也是其优势所在。

句法

假设函数getJSON返回一个promise，该promise通过某个JSON对象进行解析。我们只想调用它并记录该JSON，然后返回“done”。

以下是运用promises执行上述步骤的方式：
```javascript
const makeRequest = () =>
 getJSON()
 .then(data => {
 console.log(data)
 return "done"
 })
makeRequest()
```

以下则是用async/await的效果：
```javascript
const makeRequest = async () => {
 console.log(await getJSON())
 return "done"
}
makeRequest()
```

两者有一些不同：

1. 函数前有关键词async。关键词await 只能用于async定义的函数之内。任一async函数都能隐式返回promise，其解析值是从该函数返回的任意值（在此指字符串“done”）。

2. 上一点意味着不能在代码顶部使用await，因为它不在async定义的函数范围之中。
```javascript
// this will not work in top level
// await makeRequest()
// this will work
makeRequest().then((result) => {
 // do something
})
import pandas as pd
```

3. await getJSON() 意味着 console.log 调用会等到getJSON() promise进行解析后才输出解析值。

### 为何async/await更优越？

1、简洁干净

看看使用async/await能省去写多少行代码的麻烦！即使在上面这个人为设计的例子中，也能明显看到省去了相当多行代码。并且也无需创建一个匿名函数来处理响应，或为不需要使用的变量提供名称数据，还能避免嵌套代码。这些小优势快速叠加起来，在下面的代码示例中变得更加明显。

2、错误处理

async/ wait能用相同的结构和好用的经典 try/catch处理同步和异步错误。在下面带有promises的示例中，如果JSON 解析失败，try/catch不会进行处理，因为它要在promise中运行。因此需要在promise中调用catch并复制错误处理代码，对生产就绪代码而言，这将（希望）比console.log更精密。
```javascript
const makeRequest = () => {
 try {
 getJSON()
 .then(result => {
 // this parse may fail
 const data = JSON.parse(result)
 console.log(data)
 })
 // uncomment this block to handle asynchronous errors
 // .catch((err) => {
 // console.log(err)
 // })
 } catch (err) {
 console.log(err)
 }
}
```
现在来看看使用async/await的相同代码。catch块此时就可以处理解析错误。
```javascript
const makeRequest = async () => {
 try {
 // this parse may fail
 const data = JSON.parse(await getJSON())
 console.log(data)
 } catch (err) {
 console.log(err)
 }
}
```

3、限定条件

想象一下，下面的代码能获取一些数据，并根据数据中的一些值决定是返回数据，还是继续获取更多细节。
```javascript
const makeRequest = () => {
 return getJSON()
 .then(data => {
 if (data.needsAnotherRequest) {
 return makeAnotherRequest(data)
 .then(moreData => {
 console.log(moreData)
 return moreData
 })
 } else {
 console.log(data)
 return data
 }
 })
}
```

光是看着这些代码就非常头疼了，很容易就会迷失在各个嵌套（6级）、大括号，以及只用于将最终结果传递到主promise的返回语句中。

但当使用async/ await重写这个示例时，可读性便大大提高。
```javascript
const makeRequest = async () => {
 const data = await getJSON()
 if (data.needsAnotherRequest) {
 const moreData = await makeAnotherRequest(data);
 console.log(moreData)
 return moreData
 } else {
 console.log(data)
 return data 
 }
}
```

4、中间值

假如调用promise e1，然后使用它返回的结果调用promise e2，接着再用这两个promises的结果调用promise e3。得出的代码很可能是这样的：
```javascript
const makeRequest = () => {
 return promise1()
 .then(value1 => {
 // do something
 return promise2(value1)
 .then(value2 => {
 // do something 
 return promise3(value1, value2)
 })
 })
}
```

如果promise e3不需要值1，就会轻易缩减promise嵌套。如果不能容忍这种情况发生，可以把值1和2都包含在一个promise中，避免更深的嵌套，就像这样：
```javascript
const makeRequest = () => {
 return promise1()
 .then(value1 => {
 // do something
 return Promise.all([value1, promise2(value1)])
 })
 .then(([value1, value2]) => {
 // do something 
 return promise3(value1, value2)
 })
}
```

这种方法为了保证代码的可读性，牺牲了语义的表达。值1和2除非是为了避免嵌套promise，没有理由属于同一个数组。

但使用async/ await，同样的逻辑就会变得异常简单和直观。想想你花了多少时间试图让promises更加简明，而你本可以用这些时间干更多事情！
```javascript
const makeRequest = async () => {
 const value1 = await promise1()
 const value2 = await promise2(value1)
 return promise3(value1, value2)
}
```

5、错误堆栈

假设一段代码在一个链中调用多个promises，然后在链的某个地方出现了错误。
```javascript
const makeRequest = () => {
 return callAPromise()
 .then(() => callAPromise())
 .then(() => callAPromise())
 .then(() => callAPromise())
 .then(() => callAPromise())
 .then(() => {
 throw new Error("oops");
 })
}
makeRequest()
 .catch(err => {
 console.log(err);
 // output
 // Error: oops at callAPromise.then.then.then.then.then (index.js:8:13)
 })
```

从promise链返回的错误堆栈并未显示错误发生的位置。更糟糕的是，它具有误导性。它包含的唯一函数名是压根没有造成错误的callAPromise (但文件和行号仍然有用)。

但async/ await中的错误堆栈能指出包含错误的函数
```javascript
const makeRequest = async () => {
 await callAPromise()
 await callAPromise()
 await callAPromise()
 await callAPromise()
 await callAPromise()
 throw new Error("oops");
}
makeRequest()
 .catch(err => {
 console.log(err);
 // output
 // Error: oops at makeRequest (index.js:7:9)
 })
```

如果在本地环境中进行开发并在编辑器中打开文件，这并不是一个很大的加分项。但它在理解来自生产服务器的错误日志方面却非常有用。在这种情况下，知道错误发生在makeRequest中，总好过知道错误来自下一处的下一处的下一处……

6、调试

使用async/ await的一个极大优势是它更容易调试。调试promises一直是一件痛苦的事情，原因有二：

（1）不能在返回表达式（没有主干）的箭头函数中设置断点。
![](http://blog.mayiya.vip/post-images/1583144917080.jpeg)
（2）如果在.then块中设置断点并使用诸如step-over之类的调试快捷键，调试器不会移动到下面的.then块，因为它只能“跨过”同步代码。

但使用async/ await则无需过多箭头函数，并且能像正常的同步调用一样直接跨过await调用。
![](http://blog.mayiya.vip/post-images/1583144940013.jpeg)


7、可以await一切

最后，await可用于同步和异步表达式。例如，await5就相当于Promise.resolve(5)。这个乍一看来似乎没什么用，但在编写一个库或一个实用函数时，如果不知道输入是同步还是异步，这就是一个很大的优势了。

如果希望记录在应用程序中执行一些API调用所花费的时间，并为此创建一个泛型函数，以下是使用promises的效果：

所有API调用都会返回promises，但如果使用相同的函数来记录同步函数所花费的时间，会发生什么？通常情况下会出错，因为同步函数没有返回一个promise。为了避免这种情况，通常会在Promise.resolve()中写入makeRequest()。

但如果使用async/await，就不必担心这些情况，因为await能够安全处理任何值，不管是否有promises。
```javascript
const recordTime = async (makeRequest) => {
 const timeStart = Date.now();
 await makeRequest(); // works for any sync or async function
 const timeEnd = Date.now();
 console.log('time take:', timeEnd - timeStart);
}
```

### 总结

async/await是过去几年JavaScript上最具革新性的添加特性。它凸显出promises语法上的混乱，并提供了简明易懂的替代。

诚然，有人质疑使用async/await会让异步代码看起来不那么明显。但每当看见一个调用或.then块时，就可辨认出异步代码。人们也许需要几个星期来适应这种新信号，但C#多年前就具有这个特性了，熟悉的它的人自然明白，这微小、暂时的不便不过是瑕不掩瑜。

---
[转载：读芯术](https://www.toutiao.com/i6737094464885490184/)