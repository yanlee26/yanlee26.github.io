---
date: 2018-02-15
layout: post
title: Event loop and the rise of Async programming + 5 ways to better coding with async/await
thread: 9
categories: Translate
tags: [JS,scope, ES6, ES2017]
excerpt: Event loop and the rise[](of Async programming + 5 ways to better coding with async/await

---

原文 - <https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5>
>
欢迎来到博客系列，致力于解释JS和它的组成。在验证和描述其核心元素的过程中，我们也分享一些当构建[SessionStack](https://www.sessionstack.com/?utm_source=medium&utm_medium=blog&utm_content=Post-4-eventloop-intro),一个JSapp，曾经很robust且性能高去保持具有竞争力时我们用的规则。
你错过前三章了吗？这里可以找到：

- [An overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf?source=collection_home---2------1----------------)
- [Inside Google’s V8 engine + 5 tips on how to write optimized code](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e?source=collection_home---2------2----------------)
- [Memory management + how to handle 4 common memory leaks](https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec?source=collection_home---2------0----------------)

现在我们通过回顾单线程环境编程的弊端和如何克服它们去构建稳健的JS UI，去扩展第一篇post。依照传统的发展变化，在本文最后我们将分享关于如何用async/await写清晰的code的5条建议。

## 为何单线程是个限制？

首篇文章我们阐述，我们假想当你调用function在耗费很长时间执行的call stack里什么将发生的一些问题。
想象，比如，一个复杂的图片变换算法运行在browser里。

而call stack有function要执行，browser不能做任何事情——被阻塞了。即browser无法render，不能跑任何别的code，只是被困了。

你的app被困了。

在一些case里，这不是如此残酷的issue。但呜呼——这里有个更大的问题。当你的browser开始解析许多tasks在callstack里，它将持续很久停止响应。在那一点上，许多browser将痛过提出error，询问它们是否要终止page采取行动。

这点很搓，且完全毁灭你的UX：

![https://cdn-images-1.medium.com/max/1600/1*MCt4ZC0dMVhJsgo1u6lpYw.jpeg](https://cdn-images-1.medium.com/max/1600/1*MCt4ZC0dMVhJsgo1u6lpYw.jpeg)

## JS程序构建块

你可能在一个.js文件里写你的js app，但你的程序很可能有很多块组成，只有其中之一要现在执行，其余的随后执行。最常见的块单元是function。

JS新手常见问题看起来是*随后*紧随*现在*没必要严格发生。也即，不能现在完成的task，按定义，将异步执行，即你将没有上述作为你将下意识期待或希望的阻塞行为。

看如下例子：
```
// ajax(..) is some arbitrary Ajax function given by a library
var response = ajax('https://example.com/api');

console.log(response);
// `response` won't have the response
```

你或许意识到ajax request不完全同步，即在code被执行ajax function的时候，其并没有任何返回值给一个返回的变量。

一个简单的方式‘等待’一个一步函数返回其结果是用回调函数：

```
ajax('https://example.com/api', function(response) {
    console.log(response); // `response` is now available
});

```
注意：你可以事实上制作同步ajax 请求。绝不那样做。否则，你的JS app UI将被阻塞，用户无法点击，输入data，导航，滚动。这将阻塞一切UX，是一个可怕的practise。

这是它看起来的样子，但请永远不用——不要毁灭web：
```
// This is assuming that you're using jQuery
jQuery.ajax({
    url: 'https://api.example.com/endpoint',
    success: function(response) {
        // This is your callback.
    },
    async: false // And this is a terrible idea
});
```
我们用ajax 请求作为例子，你可以异步执行任何代码片段。

这可以通过```setTimeout(callback, milliseconds)```去做。``` setTimeout```function做的是建立一个稍后发生的事件，看：
```
function first() {
    console.log('first');
}
function second() {
    console.log('second');
}
function third() {
    console.log('third');
}
first();
setTimeout(second, 1000); // Invoke `second` after 1000ms
third();
//first third second
```

### 剖析Event Loop
我们以一个古怪的chaim开始——不管允许async JS 代码（如setTimeout我们讨论过的），直到ES6，JS自己事实上从未有过嵌入其内异步的任何直接notion。JS引擎从未在任何时刻做多于执行单个chunk的事情。

更多细节关于JS引擎的工作（谷歌V8专门地），查阅我们该话题的[前些文章](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)。

因此，谁告诉JS引擎去执行你的程序的chunks。事实上，JS引擎不独立跑，它跑在一个hosting环境，对于多数开发者而言是传统的web浏览器或node.js。事实上，如今，JS被嵌入所有种类的设备，从机器人到灯泡。每个单独的设备代表一个不同的hosting环境对于JS引擎。

通常的在所有环境的分母里是一个被称为**event loop**的内嵌机制，随时操控你程序中大量chunks执行，每次都唤起JS引擎。

这意味着JS引擎只是一个按需执行环境，对于任何任意的JS代码。这是安排events的周遭环境。

所以，比如，当你的js程序做一个ajax请求去fetch一些data到server，你建立‘response’代码在一个function（回调）里，JS引擎告诉宿主环境：
“Hey， 我准备暂停执行现在，但无论你结束网络请求的什么，你有一些数据，请回头调用这个function。“

浏览器然后被建立起来，去监听网络请求，当有东西给你的时候，它将通过降之雅茹event loop安排回调执行。

让我们看看下图：

![https://cdn-images-1.medium.com/max/1600/1*FA9NGxNB6-v1oI2qGEtlRQ.png](https://cdn-images-1.medium.com/max/1600/1*FA9NGxNB6-v1oI2qGEtlRQ.png)

你可以读更多关于内存堆和调用栈在我们[前文](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

那么这些web API是什么呢》 事实上，它们是你无法访问的线程，你只能调用它们。它们是brwoswer并发作用的一部分。如果你是node的开发者，这些是C++ API。

然而，何谓event loop？
![https://cdn-images-1.medium.com/max/1600/1*KGBiAxjeD9JT2j6KDo0zUg.png](https://cdn-images-1.medium.com/max/1600/1*KGBiAxjeD9JT2j6KDo0zUg.png)

> Event Loop 有一个简单的工作——去监测call stack和callback queue。如果调用栈为空，它将从队列中去首个event并将之推送至有效执行它的调用栈。

这个交互被称为Event Loop里的tick。每个event只是一个function 回调。
```
console.log('Hi');
setTimeout(function cb1() { 
    console.log('cb1');
}, 5000);
console.log('Bye');
```
让我们执行代码看看啥发生了：

1. 状态干净。浏览器console干净，调用栈为空。

![](https://cdn-images-1.medium.com/max/1600/1*9fbOuFXJHwhqa6ToCc_v2A.png)

2.  ```console.log('Hi')```被加入调用栈

![](https://cdn-images-1.medium.com/max/1600/1*dvrghQCVQIZOfNC27Jrtlw.png)

3.  ```console.log('Hi')```被执行

![](https://cdn-images-1.medium.com/max/1600/1*yn9Y4PXNP8XTz6mtCAzDZQ.png)

4.  ```console.log('Hi')```被移除调用栈

![](https://cdn-images-1.medium.com/max/1600/1*iBedryNbqtixYTKviPC1tA.png)

5.  ```setTimeout(function cb1() { ... })```被加入调用栈

![](https://cdn-images-1.medium.com/max/1600/1*HIn-BxIP38X6mF_65snMKg.png)

6.  ```setTimeout(function cb1() { ... })```被执行。浏览器创建一个timmer作为web API的一部分。将为你处理计数

![](https://cdn-images-1.medium.com/max/1600/1*vd3X2O_qRfqaEpW4AfZM4w.png)

7.  ```setTimeout(function cb1() { ... })```自己完成并被移出调用栈。

![](https://cdn-images-1.medium.com/max/1600/1*_nYLhoZPKD_HPhpJtQeErA.png)

8.  ```console.log('Bye')```被加入调用栈

![](https://cdn-images-1.medium.com/max/1600/1*1NAeDnEv6DWFewX_C-L8mg.png)

9.  ```console.log('Bye')```被执行

![](https://cdn-images-1.medium.com/max/1600/1*UwtM7DmK1BmlBOUUYEopGQ.png)

10.  ```console.log('Bye')```被移出调用栈

![](https://cdn-images-1.medium.com/max/1600/1*-vHNuJsJVXvqq5dLHPt7cQ.png)

11. 至少5000ms后，timmer完成，它将cb1推入回调队列。
![](https://cdn-images-1.medium.com/max/1600/1*eOj6NVwGI2N78onh6CuCbA.png)


12. Event Loop将cb1从会调队列中取出并推送至调用栈。

![](https://cdn-images-1.medium.com/max/1600/1*jQMQ9BEKPycs2wFC233aNg.png)

13. cb1被执行并加入```console.log('cb1')```到调用栈。

![](https://cdn-images-1.medium.com/max/1600/1*hpyVeL1zsaeHaqS7mU4Qfw.png)

14. ```console.log('cb1')```被执行。

![](https://cdn-images-1.medium.com/max/1600/1*lvOtCg75ObmUTOxIS6anEQ.png)

15. ```console.log('cb1')```被移出调用栈。

![](https://cdn-images-1.medium.com/max/1600/1*Jyyot22aRkKMF3LN1bgE-w.png)

16. ```cb1```被移出调用栈。

![](https://cdn-images-1.medium.com/max/1600/1*t2Btfb_tBbBxTvyVgKX0Qg.png)

一个快速概述：

![](https://cdn-images-1.medium.com/max/1600/1*TozSrkk92l8ho6d8JxqF_w.gif)

注意到ES6定义event loop 应该如何work，即技术上其在JS引擎职责之内，不再仅仅作为宿主环境的角色。作此改变的一个主要原因是ES6中Promise的引入，由于扁平化的require access到一个在event loop队列安排操作上的直接，容易的控制（后边大量细节将讲述）。

### setTimeout是如何工作的

注意到setTimeout不是自动地将你的回调放到event loop队列很重要。它建立一个timer/当这个timmer 到期，这个环境将你的回调放入event loop，以便将来tick将捡起并执行它。看如下code：
```
setTimeout(myCallback, 1000);
```
这并不是意味着1000ms后myCallback将被执行，而是在1000ms后，myCallback将被加入（事件）队列。队列，然而，可能还有其它被更早加入的事件——你的callback将不得不等待。

有很多文章和教程关于起步async code在js中建议做setTimeout（callback，0）。好，现在你知道Event Loop做了什么和setTimeout是如何工作的：调用setTimeout用0作为参数仅仅延迟了callback直到Call Stack是干净的。

看一下如下code：
```
console.log('Hi');
setTimeout(function() {
    console.log('callback');
}, 0);
console.log('Bye');
//Hi Bye callback
```
### ES6中的jobs是什么

一个新概念叫‘Job Queue’被引入ES6.它处于EventLoop queue的顶层。你很有可能用到它当处理Promise（我们也将谈及）的异步行为时。

我们现在将触及这个概念，以便于当谈及用Promise的异步行为时，稍后，你理解这些action时如何被安排和执行的。

想象它如下：Job 队列是一个在事件循环队列里贴附在每个tick底部的队列。特定异步行为可能发生在一个事件循环的tick中，将不会引起一个全新的要添加到事件队列的event loop，但将加一个item（aka job）到当前tick的Job队列底部。

这意味着你可以添加另一个要稍后执行的功能特性，你可以放心去保证它将稍后被执行，在任何别的东西之前。

一个Job也可以引起更多要加入同样队列底部的Job。理论上，这可能对于一个Job‘循环’（一个Job保持添加其它Job等）陷入无限循环，因此消减程序有必要。因此这雷同于只表达一个长运行或无限循环（如while（true））在你的code里。

Jobs看起来像setTimeout（callback，0）的‘hack’但在如此方式暗含了，它们引入一个更好定义的和有保障的顺序：稍后，但尽早。

### 回调

据你所知，回调是目前最常用的表达和管理异步js程序的方式。事实上，回调是最基本的异步模式，在JS里。数不尽的JS程序，即便是非常古老复杂的那种，也比别的方式被更多书写。

除非回调不带来缺陷。许多开发者试图寻找更好的异步模式。然而，不可能去有效利用任何抽象如果你不理解事情的本质。

接下来的一章，我们将探索一些这些抽象的底层，去展示为什么更复杂的一步模式（这将在随后的帖子里讨论）有必要且更值得推荐。

### 嵌套回调

看如下代码：
```
listen('click', function (e){
    setTimeout(function(){
        ajax('https://api.example.com/endpoint', function (text){
            if (text == "hello") {
	        doSomething();
	    }
	    else if (text == "world") {
	        doSomethingElse();
            }
        });
    }, 500);
});
```
我们看到三个function嵌套城的链条，每一个代表一个异步系列的一步。

这种code被称为‘回调地狱’。但回调地狱事实上与嵌套/缩进无关。这是比那个更深的问题。

首先，我们期待‘click’事件，然后我们期待时间去触发，然后我们期待ajax请求返回，在这点这可能得到所有的重复。

At first glance, this code may seem to map its asynchrony naturally to sequential steps like:
```
listen('click', function (e) {
	// ..
});
```
然后我们得到：
```
setTimeout(function(){
    // ..
}, 500);
```
再后我们得到：
```
ajax('https://api.example.com/endpoint', function (text){
    // ..
});
```
最终：
```
if (text == "hello") {
    doSomething();
}
else if (text == "world") {
    doSomethingElse();
}
```
因此这个表达你的异步代码的顺序方式看起来更自然，不是吗？一定有这种方式，对吧？

### Promise
看一眼如下代码：
```
var x = 1;
var y = 2;
console.log(x + y);
```
一切都这么直接：它求和了x和y的值并输出到log里。然而，如果，x或y的值丢了或者一直需要确定呢？即我们需要从服务端获取x和y的值，在其被用于表达式之前。让我们想象下我们有专门从服务端加载x和y值到函数loadX和loadY。然后设想我们有个sum函数去sumx和y的值一旦其被加载。

代码看起来如下（很丑）：
```
function sum(getX, getY, callback) {
    var x, y;
    getX(function(result) {
        x = result;
        if (y !== undefined) {
            callback(x + y);
        }
    });
    getY(function(result) {
        y = result;
        if (x !== undefined) {
            callback(x + y);
        }
    });
}
// A sync or async function that retrieves the value of `x`
function fetchX() {
    // ..
}


// A sync or async function that retrieves the value of `y`
function fetchY() {
    // ..
}
sum(fetchX, fetchY, function(result) {
    console.log(result);
});
```
这里有些非常重要的事情——在那个snippet里，我们把x和y当作将来的值，我们定义了一个操作sum（在外部）不管x或y或全部有或没有。

当然，这个基于callback的方式有很多是需要的。这只是通往理解利用future value的而不担心何时它们可获取的好处之路的一小步。

### Promise Value
让我们简要瞥一下在promise里我们如何表达x+y：

```
function sum(xPromise, yPromise) {
	// `Promise.all([ .. ])` takes an array of promises,
	// and returns a new promise that waits on them
	// all to finish
	return Promise.all([xPromise, yPromise])

	// when that promise is resolved, let's take the
	// received `X` and `Y` values and add them together.
	.then(function(values){
		// `values` is an array of the messages from the
		// previously resolved promises
		return values[0] + values[1];
	} );
}

// `fetchX()` and `fetchY()` return promises for
// their respective values, which may be ready
// *now* or *later*.
sum(fetchX(), fetchY())

// we get a promise back for the sum of those
// two numbers.
// now we chain-call `then(...)` to wait for the
// resolution of that returned promise.
.then(function(sum){
    console.log(sum);
});
```

这个snippeet里有两层Promise。
fetchX（）和fetchY（）直接被调用，其所返回的值（promises）被传入sum（）。underlying的值现在活稍后可用，但每个promise正规化其同样结果的行为。我们以一个依赖于时间的方式得到x和y。它们是future value。

第二层是sum（）构建的promise和我们通过调用then获取的返回。当sum操作完成，我们sum future value就绪并且我们可以打印输出。我们隐含等待sum函数内x和y的future value逻辑。

**注意：** 在sum内，Promise.all（[]）调用一个promise（等待promiseX和promiseY返回)。链式的call到.then构建另一个promise，它返回value[0] + value[1]。

有了Promise，then的调用事实上可以带两个function，第一个是成功回调（前述），第二个是失败回调。
```
sum(fetchX(), fetchY())
.then(
    // fullfillment handler
    function(sum) {
        console.log( sum );
    },
    // rejection handler
    function(err) {
    	console.error( err ); // bummer!
    }
);
```
如果在获取x或y时出现问题，或者在添加期间某种方式失败了，那么sum（...）返回的promise将被拒绝，并且传递给then（...）的第二个回调错误处理程序将收到拒绝 来自诺言的价值。

由于Promises封装了时间依赖状态 - 等待基础价值的实现或拒绝 - 从外部看，Promise本身是时间无关的，因此Promises可以以可预测的方式组合（组合），而不管时间或结果如何 下。

而且，一旦一个promise解决了，它就会永远保持这种状态 - 它在那个时候成为一个不变的价值 - 然后可以根据需要多次观察。

串联promise是事实上的确非常有用：

```
function delay(time) {
    return new Promise(function(resolve, reject){
        setTimeout(resolve, time);
    });
}

delay(1000)
.then(function(){
    console.log("after 1000ms");
    return delay(2000);
})
.then(function(){
    console.log("after another 2000ms");
})
.then(function(){
    console.log("step 4 (next Job)");
    return delay(5000);
})
// ...
```
calling delay（2000）创建了一个将在2000ms完成的promise，然后我们从第一个（...）履行回调中返回，这导致第二个（...）的promise等待2000ms的promise。

**注意：**因为Promise一旦解决就是外部不可变的，现在可以安全地将该值传递给任何一方，并知道它不能被意外或恶意修改。 关于观察解决诺言的多方，这一点尤其如此。 一方不可能影响另一方遵守Promise解决方案的能力。 不变性可能听起来像是一个学术话题，但它实际上是Promise设计的最基本和最重要的方面之一，不应该随便传递。


### 要不要Promise？

关于promise的一个重要细节是确切地知道某个值是否是实际的promise。 换句话说，这是一种会表现得像一个promise的价值吗？

我们知道Promise是由新的Promise（...）语法构造的，您可能认为Promise的instanceof将是一个足够的检查。 好吧，不是。

主要是因为您可以从另一个浏览器窗口（例如iframe）接收Promise值，该窗口具有与当前窗口或框架中的promise不同的Promise，并且该检查无法识别Promise实例。

此外，图书馆或框架可能会选择出售自己的promise，而不是使用原生ES6promise实施来实现。 事实上，你可能会在早期的浏览器中使用Promises和Promise来实现Promise。

### 吃掉异常

如果在创建Promise或观察其解决方案的任何时候发生JavaScript异常错误（例如TypeError或ReferenceError），该异常将被捕获，并且它将强制有问题的Promise被拒绝。

例如：

```
var p = new Promise(function(resolve, reject){
    foo.bar();	  // `foo` is not defined, so error!
    resolve(374); // never gets here :(
});

p.then(
    function fulfilled(){
        // never gets here :(
    },
    function rejected(err){
        // `err` will be a `TypeError` exception object
	// from the `foo.bar()` line.
    }
);
```
但是如果一个Promise被实现，但观察期间（在一个当时（...）注册的回调中）有一个JS异常错误会发生什么？ 即使它不会丢失，你可能会发现它们的处理方式有点令人惊讶。 直到你深入一点：

```
var p = new Promise( function(resolve,reject){
	resolve(374);
});

p.then(function fulfilled(message){
    foo.bar();
    console.log(message);   // never reached
},
    function rejected(err){
        // never reached
    }
);
```
它看起来像来自foo.bar（）的异常真的被吞噬了。 不过，这不是。 然而，有些事情发生了错误，但我们没有听到。 p.then（...）调用本身会返回另一个promise，这就是那个将被TypeError异常拒绝的promise。

### 处理异常

还有其他的方法，很多人会说更好。

一个常见的建议是Promise应该已经完成了（...），它们基本上将Promise链标记为“已完成”。done（...）不会创建并返回Promise，所以回调函数将传递给done。 ）显然没有连接到向不存在的链式promise报告问题。

它的处理方式与您在未捕获的错误情况中通常所期待的一样：done（..）拒绝处理程序中的任何异常都将作为全局未捕获错误引发（基本上在开发人员控制台中）：

```

var p = Promise.resolve(374);

p.then(function fulfilled(msg){
    // numbers don't have string functions,
    // so will throw an error
    console.log(msg.toLowerCase());
})
.done(null, function() {
    // If an exception is caused here, it will be thrown globally 
});
```

### 在ES8里发生了什么？Async/await

JavaScript ES8引入了async/await，这使得使用Promises的工作更容易。我们将简要介绍async/await提供的可能性以及如何利用它们来编写异步代码。

那么，让我们看看async/await如何工作。

您可以使用异步函数声明定义一个异步函数。这样的函数返回一个AsyncFunction对象。 AsyncFunction对象表示执行包含在该函数中的代码的异步函数。

当一个异步函数被调用时，它返回一个Promise。当异步函数返回一个值时，这不是一个Promise，Promise将会自动创建，并且会使用函数返回的值来解析。当异步函数抛出异常时，Promise将被抛出的值拒绝。

异步函数可以包含await表达式，暂停执行该函数并等待传递的Promise的解析，然后恢复异步函数的执行并返回解析后的值。

您可以将JavaScript中的promise视为Java未来或C＃任务的等同物。

同样，抛出异常的函数等价于返回已被拒绝的promise的函数：
```
function f1() {
    return Promise.reject('Some error');
}
async function f2() {
    throw 'Some error';
}
```
await关键字只能用于异步功能，并允许您同步等待Promise。 如果我们在异步函数之外使用promise，我们仍然必须使用回调：

```
async function loadData() {
    // `rp` is a request-promise function.
    var promise1 = rp('https://api.example.com/endpoint1');
    var promise2 = rp('https://api.example.com/endpoint2');
   
    // Currently, both requests are fired, concurrently and
    // now we'll have to wait for them to finish
    var response1 = await promise1;
    var response2 = await promise2;
    return response1 + ' ' + response2;
}
// Since, we're not in an `async function` anymore
// we have to use `then`.
loadData().then(() => console.log('Done'));
```
您还可以使用“异步函数表达式”来定义异步函数。 异步函数表达式与异步函数语句非常相似，语法几乎相同。 异步函数表达式与异步函数语句之间的主要区别在于函数名称，在异步函数表达式中可以省略该函数名称以创建匿名函数。 异步函数表达式可以用作IIFE（立即调用的函数表达式），只要定义它就立即运行。

它看起来像这样：
```
var loadData = async function() {
    // `rp` is a request-promise function.
    var promise1 = rp('https://api.example.com/endpoint1');
    var promise2 = rp('https://api.example.com/endpoint2');
   
    // Currently, both requests are fired, concurrently and
    // now we'll have to wait for them to finish
    var response1 = await promise1;
    var response2 = await promise2;
    return response1 + ' ' + response2;

```
更重要的是，async/await广被主流浏览器支持：
![](https://cdn-images-1.medium.com/max/1600/0*z-A-JIe5OWFtgyd2.)

在一天结束时，重要的是不要盲目选择编写异步代码的“最新”方法。 理解异步JavaScript的内部特性至关重要，了解为什么它非常重要，并深入了解所选方法的内部。 与编程中的其他所有方法一样，每种方法都有优点和缺点。

### 5条建议关于书写可维护，不脆弱的异步代码。

1. Clean code: 用async/await 让你写更少的代码。 每次用async/await 你就跳过很多不必带步骤: 写 .then, 构造匿名函数处理response, 从回调中命名resonse， 如下.

```
// `rp` is a request-promise function.
rp(‘https://api.example.com/endpoint1').then(function(data) {
 // …
});
```
Versus:
```
var response = await rp(‘https://api.example.com/endpoint1');

```
2. Error handling: Async/await 让以同样的代码块处理sync 和 async errors 成为可能
— 广为人知到 try/catch 语句。 对比Promises:
```
function loadData() {
    try { // Catches synchronous errors.
        getJSON().then(function(response) {
            var parsed = JSON.parse(response);
            console.log(parsed);
        }).catch(function(e) { // Catches asynchronous errors
            console.log(e); 
        });
    } catch(e) {
        console.log(e);
    }
}
```
Versus:
```
async function loadData() {
    try {
        var data = JSON.parse(await getJSON());
        console.log(data);
    } catch(e) {
        console.log(e);
    }
}
```
3. Conditionals: 写条件代码利用async/await 更直接:
```
function loadData() {
  return getJSON()
    .then(function(response) {
      if (response.needsAnotherRequest) {
        return makeAnotherRequest(response)
          .then(function(anotherResponse) {
            console.log(anotherResponse)
            return anotherResponse
          })
      } else {
        console.log(response)
        return response
      }
    })
}
```
Versus:
```
async function loadData() {
  var response = await getJSON();
  if (response.needsAnotherRequest) {
    var anotherResponse = await makeAnotherRequest(response);
    console.log(anotherResponse)
    return anotherResponse
  } else {
    console.log(response);
    return response;    
  }
}
```
4. Stack Frames: 不同于with async/await,  promise chain返回的error stack， 对于哪里出错不给出线索.看:

```
function loadData() {
  return callAPromise()
    .then(callback1)
    .then(callback2)
    .then(callback3)
    .then(() => {
      throw new Error("boom");
    })
}
loadData()
  .catch(function(e) {
    console.log(err);
// Error: boom at callAPromise.then.then.then.then (index.js:8:13)
});
```
Versus:

```
async function loadData() {
  await callAPromise1()
  await callAPromise2()
  await callAPromise3()
  await callAPromise4()
  await callAPromise5()
  throw new Error("boom");
}
loadData()
  .catch(function(e) {
    console.log(err);
    // output
    // Error: boom at loadData (index.js:7:9)
});
```
5. Debugging: 
如果你使用过promise，你知道调试它们是一场噩梦。例如，如果您在.then块内设置断点并使用调试快捷方式（如“停止”），则调试器将不会移动到以下位置，因为它仅通过同步代码“执行”。
通过异步/等待，您可以完全按照正常的同步功能一步一步地等待呼叫。

编写异步JavaScript代码不仅对于应用程序本身而且对于库也很重要。

例如，SessionStack库会记录您的Web应用程序/网站中的所有内容：所有DOM更改，用户交互，JavaScript异常，堆栈跟踪，失败的网络请求和调试消息。

这一切都必须在您的生产环境中发生，而不会影响任何用户体验。我们需要大量优化代码并尽可能使其异步，以便我们可以增加事件循环正在处理的事件数量。

而不只是图书馆！当您在SessionStack中重放用户会话时，我们必须在发生问题时渲染用户浏览器中发生的所有事情，并且我们必须重构整个状态，允许您在会话时间轴中来回跳转。为了实现这一点，我们正在大量使用JavaScript提供的异步机会。
There is a free plan that allows you to [get started for free](https://www.sessionstack.com/?utm_source=medium&utm_medium=blog&utm_content=Post-4-eventloop-GetStarted).

![](https://cdn-images-1.medium.com/max/1600/0*xSEaWHGqqlcF8g5H.)

Resources:

- <https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch2.md>
- <https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md>
- <http://nikgrozev.com/2017/10/01/async-await/>