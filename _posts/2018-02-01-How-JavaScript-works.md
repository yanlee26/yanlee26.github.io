---
date: 2018-02-01
layout: post
title: How JavaScript works
thread: 9
categories: Translate
tags: [BIT]
excerpt: How JavaScript works
---

# JavaScript 是如何工作的：内存管理 + 如何处理四种常见的内存泄漏

> 原文 - How JavaScript works: memory management + how to handle 4 common memory leaks
>
> 原文作者 - [Alexander Zlatkov](https://blog.sessionstack.com/@zlatkov?source=post_header_lockup)
>
> 原文地址 - <https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec>
> 
> 译者 - [yanlee](https://github.com/yanlee26)
>
> 译文地址 - <https://yanlee26.github.io/2018/02/01/How-JavaScript-works/>
>
> 知乎专栏 - <https://zhuanlan.zhihu.com/p/33483627>
> 掘金专栏 - 
<https://juejin.im/post/5a725c6ff265da3e2e62d0d1>

几个星期前，我们开始了一系列旨在深入研究JavaScript及其实际工作原理的系列文章：我们认为通过了解JavaScript的构建块以及它们如何一起玩，您将能够编写更好的代码和应用程​​序。

本系列的第一篇文章重点介绍了引擎，运行时和调用堆栈的概述。Thе 第二后仔细研究谷歌的V8 JavaScript引擎的内部零件，也提供了有关如何写出更好的JavaScript代码的一些提示。

在第三篇文章中，我们将讨论另一个越来越被开发人员忽视的关键主题，因为日常使用的编程语言（内存管理）越来越成熟和复杂。我们也将提供关于如何处理JavaScript中的内存泄漏，我们在一些技巧SessionStack遵循我们需要确保SessionStack不会造成内存泄漏，或不增加的Web应用程序，我们正在整合的内存消耗。

概观
像C这样的语言具有低级的内存管理原语，比如malloc()和free()。开发人员使用这些原语来显式分配和释放操作系统的内存。

同时，当事物（对象，字符串等）被创建时，JavaScript分配内存，并在不再使用时自动释放内存，称为垃圾收集。这种释放资源的看似“自动化”特性是混淆的一个原因，给JavaScript（和其他高级语言）的开发人员带来了他们可以选择不关心内存管理的错误印象。这是一个大错误。

即使使用高级语言，开发人员也应该理解内存管理（至少是基本的）。有时，自动内存管理存在问题（例如垃圾收集器中的错误或实施限制等），开发人员必须了解这些问题才能正确处理这些问题（或者找到适当的解决方法，并且具有最小的权衡和代码债务）。

内存生命周期
无论您使用什么编程语言，内存生命周期几乎都是一样的：

![https://cdn-images-1.medium.com/max/1600/1*slxXgq_TO38TgtoKpWa_jQ.png](https://cdn-images-1.medium.com/max/1600/1*slxXgq_TO38TgtoKpWa_jQ.png)

以下是对循环中每个步骤发生的情况的概述：

- 分配内存  - 内存由操作系统分配，允许程序使用它。在低级语言中（例如C），这是一个作为开发人员应该处理的显式操作。然而，在高级语言中，这是为你照顾的。
- 使用内存 - 这是您的程序实际上使用以前分配的内存的时间。读取和写入操作正在您的代码中使用分配的变量。
- 释放内存  - 现在是释放你不需要的整个内存的时间，以便它可以变成空闲的并且可以再次使用。与分配内存操作一样，这个操作在低级语言中是明确的。

有关调用堆栈和内存堆的概念的快速概述，您可以阅读我们[关于主题的第一篇文章](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)。

## 什么是内存？

在直接跳到JavaScript中的内存之前，我们将简要地讨论一下内存的概况以及它是如何工作的。

在硬件层面上，计算机内存由大量的
触发器组成。每个触发器包含一些晶体管，并能够存储一位。单独的触发器可以通过唯一的标识符来寻址，所以我们可以读取和覆盖它们。因此，从概念上讲，我们可以将整个计算机内存看作是我们可以读写的一大块位。

既然作为人类，我们并不善于把所有的思想和算术都做成一点点，我们把它们组织成更大的群体，它们可以一起用来表示数字。8位称为1个字节。除字节外，还有单词（有时是16，有时是32位）。

很多东西都存储在这个内存中：

1. 所有程序使用的所有变量和其他数据。
2. 程序的代码，包括操作系统的代码。

编译器和操作系统一起工作，为您处理大部分的内存管理，但是我们建议您看看底下发生了什么。

编译代码时，编译器可以检查原始数据类型，并提前计算它们需要多少内存。然后将所需的金额分配给调用堆栈空间中的程序。分配这些变量的空间称为堆栈空间，因为随着函数被调用，它们的内存被添加到现有的内存之上。当它们终止时，它们以LIFO（后进先出）顺序被移除。例如，请考虑以下声明：
```
int n; // 4个字节
int x [4]; // 4个元素的数组，每4个字节
双m; // 8个字节
```
编译器可以立即看到代码需要  
4 + 4×4 + 8 = 28个字节。

> 这就是它与目前的整数和双打的尺寸。大约20年前，整数通常是2个字节，双4字节。您的代码不应该依赖于此刻基本数据类型的大小。

编译器将插入与操作系统交互的代码，以便为堆栈中的变量存储所需的字节数。

在上面的例子中，编译器知道每个变量的确切内存地址。实际上，每当我们写入这个变量n，它就会在内部翻译成“内存地址4127963”。

注意，如果我们试图访问x[4]这里，我们将访问与m关联的数据。这是因为我们正在访问数组中不存在的元素 - 它比数组中最后一个实际分配的元素多了4个字节x[3]，并且可能最终读取（或覆盖）了一些m比特。这对方案的其余部分几乎肯定会产生非常不希望的后果。

![https://cdn-images-1.medium.com/max/1600/1*5aBou4onl1B8xlgwoGTDOg.png](https://cdn-images-1.medium.com/max/1600/1*5aBou4onl1B8xlgwoGTDOg.png)

当函数调用其他函数时，每个函数调用时都会得到自己的堆栈块。它保留了所有的局部变量，而且还有一个程序计数器，它记录了执行的地方。当功能完成时，其内存块再次可用于其他目的。

## 动态分配

不幸的是，当我们不知道编译时变量需要多少内存时，事情并不那么容易。假设我们想要做如下的事情：

int n = readInput（）; //从用户读取输入
...
//用“n”个元素创建一个数组
这里，在编译时，编译器不知道数组需要多少内存，因为它是由用户提供的值决定的。

因此，它不能为堆栈上的变量分配空间。相反，我们的程序需要在运行时明确地向操作系统请求适当的空间。这个内存是从堆空间分配的。下表总结了静态和动态内存分配之间的区别：

![https://cdn-images-1.medium.com/max/1600/1*qY-yRQWGI-DLS3zRHYHm9A.png](https://cdn-images-1.medium.com/max/1600/1*qY-yRQWGI-DLS3zRHYHm9A.png)
静态和动态分配的内存之间的差异

为了充分理解动态内存分配是如何工作的，我们需要在指针上花费更多的时间，这可能与本文主题偏离太多。如果您有兴趣了解更多信息，请在评论中告诉我们，我们可以在以后的文章中详细介绍指针。

## 在JavaScript中分配
现在我们将解释第一步（分配内存）如何在JavaScript中工作。

JavaScript使开发人员免于处理内存分配的责任 - JavaScript本身就是这样做的，同时还声明了值。

```
var n = 374; // allocates memory for a number
var s = 'sessionstack'; // allocates memory for a string 
var o = {
  a: 1,
  b: null
}; // allocates memory for an object and its contained values
var a = [1, null, 'str'];  // (like object) allocates memory for the
                           // array and its contained values
function f(a) {
  return a + 3;
} // allocates a function (which is a callable object)
// function expressions also allocate an object
someElement.addEventListener('click', function() {
  someElement.style.backgroundColor = 'blue';
}, false);
```
一些函数调用也会导致对象分配：
```
var d = new Date(); // allocates a Date object
var e = document.createElement('div'); // allocates a DOM element
```
方法可以分配新的值或对象：
```
var s1 = 'sessionstack';
var s2 = s1.substr(0, 3); // s2 is a new string
// Since strings are immutable, 
// JavaScript may decide to not allocate memory, 
// but just store the [0, 3] range.
var a1 = ['str1', 'str2'];
var a2 = ['str3', 'str4'];
var a3 = a1.concat(a2); 
// new array with 4 elements being
// the concatenation of a1 and a2 elements
```
## 在JavaScript中使用内存
基本上在JavaScript中使用分配的内存，意味着在其中读写。

这可以通过读取或写入变量或对象属性的值，甚至将参数传递给函数来完成。

## 当内存不再需要时释放
大部分内存管理问题都是在这个阶段。

这里最困难的任务是确定何时不再需要分配的内存。它通常需要开发人员确定程序中的哪个部分不再需要这些内存，并将其释放。

高级语言嵌入了一个名为垃圾收集器的软件，其工作是跟踪内存分配和使用情况，以便在不再需要分配内存的情况下自动释放内存。

不幸的是，这个过程是一个近似值，因为知道是否需要某些内存的一般问题是不可判定的（不能由算法来解决）。

大多数垃圾收集器通过收集不能被访问的内存来工作，例如指向它的所有变量超出范围。然而，这是可以收集的一组内存空间的近似值，因为在任何时候内存位置可能仍然有一个指向它的变量，但它将不会被再次访问。

## 垃圾收集
由于发现一些内存是否“不再需要”的事实是不可判定的，所以垃圾收集实现了对一般问题的解决方案的限制。本节将解释理解主要垃圾收集算法及其局限性的必要概念。

## 内存引用
垃圾收集算法所依赖的主要概念是参考之一。

在内存管理的情况下，如果一个对象访问后者（可以是隐含的或显式的），则称该对象引用另一个对象。例如，JavaScript对象具有对其原型（隐式引用）及其属性值（显式引用）的引用。

在这种情况下，“对象”的概念被扩展到比普通JavaScript对象更广泛的范围，并且还包含函数作用域（或全局词法作用域）。

词法范围定义了如何在嵌套函数中解析变量名称：即使父函数已经返回，内部函数也包含父函数的作用域。
## 引用计数垃圾收集
这是最简单的垃圾收集算法。如果有零个引用指向它，则该对象被认为是“垃圾收集” 。

看看下面的代码：
```
var o1 = {
  o2: {
    x: 1
  }
};
// 2 objects are created. 
// 'o2' is referenced by 'o1' object as one of its properties.
// None can be garbage-collected

var o3 = o1; // the 'o3' variable is the second thing that 
            // has a reference to the object pointed by 'o1'. 
                                                       
o1 = 1;      // now, the object that was originally in 'o1' has a         
            // single reference, embodied by the 'o3' variable

var o4 = o3.o2; // reference to 'o2' property of the object.
                // This object has now 2 references: one as
                // a property. 
                // The other as the 'o4' variable

o3 = '374'; // The object that was originally in 'o1' has now zero
            // references to it. 
            // It can be garbage-collected.
            // However, what was its 'o2' property is still
            // referenced by the 'o4' variable, so it cannot be
            // freed.

o4 = null; // what was the 'o2' property of the object originally in
           // 'o1' has zero references to it. 
           // It can be garbage collected.
```

## 循环造成问题
在周期方面有一个限制。在下面的例子中，创建两个对象并相互引用，从而创建一个循环。在函数调用之后，它们会超出范围，所以它们实际上是无用的，可以被释放。然而，引用计数算法认为，由于两个对象中的每一个被引用至少一次，所以两者都不能被垃圾收集。
```
function f() {
  var o1 = {};
  var o2 = {};
  o1.p = o2; // o1 references o2
  o2.p = o1; // o2 references o1. This creates a cycle.
}

f();
```

![https://cdn-images-1.medium.com/max/1600/1*GF3p99CQPZkX3UkgyVKSHw.png](https://cdn-images-1.medium.com/max/1600/1*GF3p99CQPZkX3UkgyVKSHw.png)

## 标记和扫描算法
为了确定是否需要对象，此算法确定对象是否可达。

标记和扫描算法经过这3个步骤：

- 根：通常，根是代码中引用的全局变量。例如，在JavaScript中，可以充当根的全局变量是“窗口”对象。Node.js中的相同对象称为“全局”。所有根的完整列表由垃圾收集器构建。
- 算法然后检查所有根和他们的孩子并且标记他们是活跃的（意思，他们不是垃圾）。任何根不能达到的将被标记为垃圾。
- 最后，垃圾回收器释放所有未标记为活动的内存块，并将该内存返回给操作系统。

![https://cdn-images-1.medium.com/max/1600/1*WVtok3BV0NgU95mpxk9CNg.gif](https://cdn-images-1.medium.com/max/1600/1*WVtok3BV0NgU95mpxk9CNg.gif)

标记和扫描算法的可视化

这个算法比前一个算法更好，因为“一个对象有零引用”导致这个对象无法访问。正如我们已经看到周期一样，情况正好相反。

截至2012年，所有现代浏览器都发布了标记式的垃圾回收器。JavaScript垃圾收集（代码/增量/并发/并行垃圾收集）领域中所做的所有改进都是对这种算法（标记和扫描）的实现改进，但不是对垃圾收集算法本身的改进，也不是它的目标是决定一个对象是否可达。

在[本文中](https://en.wikipedia.org/wiki/Tracing_garbage_collection)，您可以详细阅读有关跟踪垃圾回收的更详细信息，这些垃圾回收也涵盖了标记和扫描以及其优化。

## 循环不再是问题了
在上面的第一个例子中，在函数调用返回之后，两个对象不再被全局对象可访问的东西引用。因此，它们将被垃圾收集器发现无法访问。
![https://cdn-images-1.medium.com/max/1600/1*FbbOG9mcqWZtNajjDO6SaA.png](https://cdn-images-1.medium.com/max/1600/1*FbbOG9mcqWZtNajjDO6SaA.png)

即使在对象之间有引用，它们也不能从根目录访问。

## 抵制垃圾收集器的直观行为
尽管垃圾收集者很方便，但他们也有自己的一套权衡。其中之一是非决定论。换句话说，GC是不可预测的。你不能真正知道什么时候收集。这意味着在某些情况下，程序会使用更多的内存，这是实际需要的。在其他情况下，在特别敏感的应用程序中，短暂暂停可能是显而易见的。虽然非确定性意味着不能确定何时执行集合，但大多数GC实现共享在分配期间进行收集通行证的通用模式。如果没有执行分配，大多数GC保持空闲状态。考虑以下情况：

- 大量的分配被执行。
- 大多数这些元素（或所有这些元素）被标记为无法访问（假设我们将一个引用指向我们不再需要的缓存）。
- 没有进一步的分配执行。
在这种情况下，大多数GC不会运行任何进一步的收集通行证。换句话说，即使有不可用的引用可用于收集，这些收集器不会声明。这些并不是严格的泄漏，但仍会导致内存使用率高于平时。

## 什么是内存泄漏？
就像内存建议一样，内存泄漏是应用程序过去使用的内存片段，但不再需要，但尚未返回到操作系统或可用内存池。

![https://cdn-images-1.medium.com/max/1600/1*0B-dAUOH7NrcCDP6GhKHQw.jpeg](https://cdn-images-1.medium.com/max/1600/1*0B-dAUOH7NrcCDP6GhKHQw.jpeg)

编程语言有利于不同的内存管理方式。但是，是否使用某一段内存实际上是一个不可判定的问题。换句话说，只有开发人员可以明确是否可以将一块内存返回到操作系统。

某些编程语言提供了帮助开发人员执行此操作 其他人则希望开发人员能够完全清楚一段内存何时未被使用。维基百科有关手动和自动内存管理的好文章。

## 四种常见的JavaScript泄漏
### 1：全局变量
JavaScript以一种有趣的方式处理未声明的变量：当引用未声明的变量时，在全局对象中创建一个新变量。在浏览器中，全局对象将是window，这意味着
```
function foo(arg) {
    bar = "some text";
}
```
相当于：
```
function foo(arg) {
    window.bar = "some text";
}
```
让我们说的目的bar是在foo函数中只引用一个变量。如果您不使用var声明，将会创建一个冗余的全局变量。在上述情况下，这不会造成太大的伤害。你当然可以想象一个更具破坏性的场景。

你也可以用下面的方法不小心创建一个全局变量this：
```
function foo() {
    this.var1 = "potential accidental global";
}
// Foo called on its own, this points to the global object (window)
// rather than being undefined.
foo();
```
> 您可以通过‘use strict’;在JavaScript文件的开始处添加以避免所有这些，这将开启更严格的解析JavaScript模式，从而防止意外创建全局变量。

意外的全局变量当然是一个问题，然而，更多的时候，你的代码会受到垃圾收集器无法收集的显式全局变量的影响。需要特别注意用于临时存储和处理大量信息的全局变量。如果您必须使用全局变量来存储数据，那么确保将其**分配为空值**，或者在完成后**重新分配**。

### 2：被遗忘的定时器或回调

让我们setInterval举个例子，因为它经常用在JavaScript中。

提供接受回调的观察者和其他工具的库通常确保所有对回调的引用在其实例无法访问时变得无法访问。不过，下面的代码并不是一个难得的发现：

var serverData = loadData();
setInterval(function() {
    var renderer = document.getElementById('renderer');
    if(renderer) {
        renderer.innerHTML = JSON.stringify(serverData);
    }
}, 5000); //This will be executed every ~5 seconds.
上面的代码片段显示了使用引用节点或不再需要的数据的定时器的结果。

该renderer对象可能会在某些时候被替换或删除，这会使间隔处理程序封装的块变得冗余。如果发生这种情况，那么处理程序及其依赖项都不会被收集，因为间隔需要先停止（请记住，它仍然是活动的）。这一切都归结为serverData确实存储和处理负载数据的事实也不会被收集。

当使用观察者时，你需要确保你做了一个明确的调用来删除它们（或者不再需要观察者，否则对象将变得不可用）。

幸运的是，大多数现代浏览器都会为你做这件事：即使你忘记删除监听器，观察对象变得无法访问，它们也会自动收集观察者处理程序。过去一些浏览器无法处理这些情况（旧的IE6）。

但是，尽管如此，一旦对象变得过时，这是符合最佳实践的。看下面的例子：
```
var element = document.getElementById('launch-button');
var counter = 0;
function onClick(event) {
   counter++;
   element.innerHtml = 'text ' + counter;
}
element.addEventListener('click', onClick);
// Do stuff
element.removeEventListener('click', onClick);
element.parentNode.removeChild(element);
// Now when element goes out of scope,
// both element and onClick will be collected even in old browsers // that don't handle cycles well.
```
removeEventListener在现代浏览器支持可以检测这些周期并适当处理它们的垃圾收集器之前，不再需要调用节点。

如果您利用jQueryAPI（其他库和框架也支持这一点），您也可以在节点过时之前删除侦听器。即使应用程序在较旧的浏览器版本下运行，库也会确保没有内存泄漏。

### 3：关闭
JavaScript开发的一个关键方面是闭包：一个内部函数，可以访问外部（封闭）函数的变量。由于JavaScript运行时的实现细节，可能以下列方式泄漏内存：

```
ar theThing = null;
var replaceThing = function（）{
  var originalThing = theThing; 
  var unused = function（）{ 
    if（originalThing）//对'originalThing'的引用
      console.log（“hi”）; 
  };
  theThing = { 
    longStr：new Array（1000000）.join（'*'），
    someMethod：function（）{ 
      console.log（“message”）; 
    } 
  }; 
};
setInterval（replaceThing，1000）;
```
一旦replaceThing被调用，theThing获取由一个大数组和一个新的闭包（someMethod）组成的新对象。然而，originalThing被unused变量所持有的闭包所引用（这个theThing变量是前一次调用的变量replaceThing）。需要记住的是，**一旦在同一父作用域中为闭包创建了闭包的作用域，作用域就被共享了。**

在这种情况下，为闭包创建的范围将someMethod与之共享unused。unused有一个参考originalThing。即使unused从未使用过，someMethod 也可以theThing在整个范围之外使用replaceThing（例如全球某个地方）。而且someMethod与封闭范围一样unused，引用unused必须originalThing强制它保持活跃（两封闭之间的整个共享范围）。这阻止了它的收集。

在上面的例子中，所述封闭创建的范围someMethod与共享unused，而unused引用originalThing。someMethod可以theThing在replaceThing范围之外使用，尽管这unused是从来没有使用的事实。事实上，未使用的引用originalThing要求它保持活跃，因为someMethod与未使用的共享封闭范围。

所有这些都可能导致相当大的内存泄漏。当上面的代码片段一遍又一遍地运行时，您可以预期会看到内存使用率的上升。当垃圾收集器运行时，其大小不会缩小。创建一个闭包的链表（theThing在这种情况下它的根是可变的），并且每个闭包范围都带有对大数组的间接引用。

Meteor团队发现了这个问题，他们有一篇很好的文章，详细描述了这个问题。

### 4：超出DOM引用
有些情况下开发人员在数据结构中存储DOM节点。假设你想快速更新表格中几行的内容。如果在字典或数组中存储对每个DOM行的引用，则会有两个对同一个DOM元素的引用：一个在DOM树中，另一个在字典中。如果你决定摆脱这些行，你需要记住使两个引用无法访问。
```
var elements = {
    button: document.getElementById('button'),
    image: document.getElementById('image')
};
function doStuff() {
    elements.image.src = 'http://example.com/image_name.png';
}
function removeImage() {
    // The image is a direct child of the body element.
    document.body.removeChild(document.getElementById('image'));
    // At this point, we still have a reference to #button in the
    //global elements object. In other words, the button element is
    //still in memory and cannot be collected by the GC.
}
```
在涉及DOM树内的内部节点或叶节点时，还有一个额外的考虑因素需要考虑。如果您<td>在代码中保留对表格单元格（标签）的引用，并决定从DOM中删除该表格，并保留对该特定单元格的引用，则可以预期会出现严重的内存泄漏。你可能会认为垃圾收集器会释放除了那个单元之外的所有东西。但情况并非如此。由于单元格是表格的子节点，并且子节点保持对父节点的**引用，所以对表格单元格的这种单引用可以将整个表格保存在内存中。**

我们在SessionStack尝试遵循这些最佳实践，编写正确处理内存分配的代码，原因如下：
一旦将SessionStack集成到生产Web应用程序中，它就会开始记录所有事件：所有DOM变更，用户交互，JavaScript异常，堆栈跟踪，网络请求失败，调试消息等等。  
使用SessionStack，您可以在Web应用程序中重放问题，看到你的用户发生的一切。所有这些都必须在您的网络应用程序没有性能影响的情况下进行。
由于用户可以重新加载页面或导航你的应用程序，所有的观察者，拦截器，变量分配等都必须正确处理，所以它们不会导致任何内存泄漏，或者不会增加Web应用程序的内存消耗我们正在整合。

有一个免费的计划，所以你可以[试试看](https://www.sessionstack.com/?utm_source=medium&utm_medium=blog&utm_content=Post-3-v8-getStarted)

![https://cdn-images-1.medium.com/max/1600/1*kEQmoMuNBDfZKNSBh0tvRA.png](https://cdn-images-1.medium.com/max/1600/1*kEQmoMuNBDfZKNSBh0tvRA.png)

## 参考

- <http://www-bcf.usc.edu/~dkempe/CS104/08-29.pdf>
- <https://blog.meteor.com/an-interesting-kind-of-javascript-memory-leak-8b47d2e7f156>
- <http://www.nodesimplified.com/2017/08/javascript-memory-management-and.html>
- <https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/>

