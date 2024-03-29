---
title: 'Typescript，我永远不会回到JavaScript了'
date: 2020-02-25 13:33:05
tags: [typesrcipt]
published: true
hideInList: false
feature: 
isTop: false
---
JavaScript可能是现代网络的事实上的语言，但长期以来，它也是我选择脚本编写的语言。 使用Python进行编码很有趣，但是如果我有一些自动化的东西，我会去用node。 我选择的服务器端语言曾经是C＃，但如今它虽然功能强大，但感觉却很笨拙。 我曾经天真的将Node描述为"泛滥成灾"，它满足了我编写的所有内容的需求。 我什至可以用多种不同的方式用JavaScript编写Android和iOS应用程序。

<!-- more -->


并不是说它是适用于一切的完美工具，但是多年来我一直为我服务。 我一直在讽刺地承认在家庭主力马网上出现的缺陷，包括加里·伯恩哈特（Gary Bernhardt）嘲讽的" Wat？"。 闪电般的谈话让我仍在思考。 如果您以前从未看过它，请立即停止阅读并继续观看。 您可以稍后感谢我。

闪电般的谈话已成为我推荐给前端开发人员的推荐阅读清单（查看清单还是消耗清单？）的一部分，还有乔纳森·克里默的" 23世纪JavaScript架构"。 尽管自ES6问世以来，上一篇文章中的某些内容似乎是多余的，但是它对于解释JavaScript所需的思路还有很长的路要走，这需要从"常规"编程语言（如果有的话）转变思维方式。 仅那篇文章是我5年前开始自己的JavaScript Odyssey的大部分原因。

那么，为什么我现在扬言要离开，再也不回来了？ 答案很简单。 打字稿。

### 什么是Typescript？

我假设您听说过Typescript，但如果没有，请尝试总结一下：Typescript是强类型的Javascript。 它添加了您期望强类型语言的功能，并且需要一个编译步骤（从技术上来说是"翻译"）才能将Typescript转换为JavaScript。 这是因为浏览器不了解Typescript。 从技术上讲，Typescript不会添加任何新的运行时功能，而是为开发人员添加功能。

我听说有人形容Typescript是喜欢它还是讨厌它，但是我还没有和任何尝试过并讨厌它的人交谈。 我已经与许多犹豫不决的人进行了交谈-大约在2016年中，我就是其中之一。 但是，我听到的同行对Typescript的最严厉批评是，这是一种额外的学习工具。 不仅是语言，而且还有构建过程。 但是，大多数现代的Web应用程序在其构建链中至少有一个链接以Babel的形式保留给JavaScript，Babel是一种允许较新的JavaScript在较旧的浏览器上运行的工具。 将TypeScript添加到该管道中不应该是火箭科学，尤其是因为Babel自v7起就支持Typescript编译。

它不应该是火箭科学，但有时会有这种感觉，这就是Typescript新手有时会挣扎并回到浏览器中运行的JavaScript受欢迎的原因之一。

### 好处

但是正如我所说，我从未听过有人采用Typescript并为此感到后悔。 好处远胜于困难，尤其是在使用Angular CLI或Create React App的时代-两者都使您可以在几分钟之内开始使用启用了Typescript的新项目。

我假设您知道这些好处是什么，但是如果您不知道，请允许我总结一下：

- Typescript的编译步骤意味着您可以及早发现问题，而不必等待运行时失败。
- 您的IDE现在可以在编译步骤更早的时候提示您。 想象一下，当您引用一个不存在的变量或使用错误的参数调用函数时，您的IDE会告诉您！ 真正的革命！ （当然，因为"真正的"编程语言已经享受了多年，所以我一直在调侃。）编辑：我对此强调不够。 我最近将一个旧项目从JavaScript转换为Typescript，以了解我从别人那里继承来的代码库的复杂性。 这是一个循序渐进的过程，在此期间，团队继续提供新功能，但是我们遇到了一些示例代码，这些示例中我们错误地使用了第三方库。 也许API发生了更改，或者有人误读了文档，但是我们传递了某些配置变量，这些变量绝对没有任何作用。 Typescript立即告诉我们，我们删除了有问题的代码。 显然，它是不需要的，并且非常容易误导。
- 当ECMAScript（驱动JavaScript的标准）中提出新功能时，TypeScript可以尽早采用它们，从而使您始终处于最前沿。

但实际上，主要好处是Typescript使您的代码能够以JavaScript无法做到的方式进行自我描述。 当我是项目的唯一开发人员时，JavaScript并不是很糟糕，因为我可以将所有事情都牢记在心，并且我拥有完美的记忆，对吗？ 错误。 即使是唯一的开发人员，我编写的代码也容易失忆。 如今，当我编写普通的JavaScript时，我讨厌不得不去阅读函数定义以弄清楚如何使用它。 我不知道它返回什么（如果有的话）或它接受什么样的参数。 我可能知道参数的数量及其名称，但这并不总是足够的：

``` javascript
function doTheThing() {  let error = false;    // About a million lines of code that might cause an error    return error;}function consumeTheThing() {  let error = doTheThing();  if (error) {    // What do I do with the error now?  }}
```

这就是我的意思。 "错误"是布尔值吗？ 当我看到第2行时，它看起来肯定是这样，但是那到" doTheThing"末尾之间的一百万行代码又如何呢？ 它可以是字符串，对象或知道什么的人。

您可能会说这种灵活性是一种资产。 可能是这样，但更常见的是它是一个陷阱，诱使您采用简单的方式，这会在您和您的同伴之间引起许多微小的挫败感。 曾经在别人的代码中间，觉得自己在迷宫中吗？ 那就是别人的代码。 您可以用JavaScript编写"好的"代码，但是很少有人鼓励您这样做。

这就是TypeScript的美。 这并不会迫使您成为一名优秀的开发人员。 但这为您提供了实现此目标的工具。

### 缺点

有缺点吗？ 当然有。

- 从字义上来说，更多的打字。 您可以更快地穿破键盘（对于使用蝶形键盘的人来说，这可能是个问题）。 没什么大不了的，但是一些JavaScript开发人员讨厌必须用每个新函数或变量写出类型。 您不必这样做，但是如果您不使用类型，为什么要使用Typescript？
- 由于您必须编译代码，因此每次更新和每次测试运行都将花费一点时间。 但是，我们正在谈论几秒钟。 我使用Typescript节省的时间远远超过了编译它所花费的时间。
- 有时，构建项目的复杂性可能是一个真正的痛苦。 这是真实的-如果您不熟悉构建工具，则设置新项目可能会令人沮丧。 在短期内，我的建议是找到一个入门项目以助您一臂之力—如果您正考虑使用那些框架，那么上述Angular CLI和Create React App在这方面非常有用。 长期？ 花时间学习工具-tsc，Babel，WebPack和所有这些好东西。 如果您使用的是节点，请尝试ts-node。 如果您使用的是Mocha，请尝试ts-mocha。 有一些简单的捷径可以帮助您入门。

有点像是我的观点是单方面的-我并没有在缺点和优点上投入太多精力 我想，出于同样的原因，我不会花很多时间在每顿饭上都吃麦当劳的好处-这样做的好处有限（不必再做饭了……我想就是这样）。

### 未来

我认为Typescript不会在短期内取代JavaScript-了解后者对于使前者发挥作用至关重要。 而且我们不会看到Typescript在任何浏览器中本机运行。 它是一个预处理器，就像之前的CoffeeScript一样。 Typescript和Coffescript之间的区别在于后者是对其所基于语言的根本改变。 Typescript是自然的演变。 这不仅使学习变得更容易，而且使将JavaScript项目转换为Typescript的过程更加繁琐。 首先将* .js重命名为* .ts。 现在，您有了Typescript项目！ 然后，您可以一次开始采用语言功能。 我不能保证这会很容易，但是它将告诉您有关JavaScript从未有过的事情。 当您开始下一个项目时，Typescript编译器将等待

这就是为什么我永不回头。

(本文翻译自Jamie Morris的文章《I'll Never Go Back to JavaScript》，参考：[https://levelup.gitconnected.com/ill-never-go-back-to-javascript-16370dc264a0](https://levelup.gitconnected.com/ill-never-go-back-to-javascript-16370dc264a0))