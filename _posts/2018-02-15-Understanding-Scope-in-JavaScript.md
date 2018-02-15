---
date: 2018-02-15
layout: post
title: Understanding-Scope-in-JavaScript
thread: 9
categories: Translate
tags: [JS,scope, ES6, ES2017]
excerpt: Understanding-Scope-in-JavaScript
---

> 原文 - []()
>
> 原文作者 - [Hammad Ahmed (@shammadahmed)](https://scotch.io/@shammadahmed) February 15, 2017
>
> 译者 - [yanlee](https://github.com/yanlee26)
>
> 译文地址 - <>

## Table of Contents

1.  [Introduction](#introduction)
1.  [What is Scope?](#scope)
1.  [Why Scope? The Principle of Least Access](#why)
1.  [Scope in JavaScript](#js)
1.  [Global Scope](#global)
1.  [Local Scope](#local)
1.  [Block Statements](#block)
1.  [Context](#context)
1.  [Execution Context](#execution)
1.  [Lexical Scope](#lexical)
1.  [Closures](#closure)
1.  [Public and Private Scope](#pp)
1.  [Changing Context with .call(), .apply() and .bind()](#change)
1.  [Conclusion](#conslusion)

<a name="introduction"></a>

# Introduction

JS 有个Scope特性。尽管scope的概念不为广大新手所熟知，我将尽力以最简洁的方式解释一下。理解scope将使你的code出色，减少错误并且帮你制作强大的设计模式。

<a name="scope"></a>

# What is Scope?

Scope是变量，方法和对象在你code运行时的一些特定部分
的变量访问能力。换句话说，scope决定了在你code里变量和其它资源的可访问性。

<a name="why"></a>

# Why Scope? The Principle of Least Access

因此，限制变量的可访问性和不让你代码中的一切可获取的意义在哪里？一个好处是scope提供了一些对你代码的保护。一个普遍的电脑安全原则是用户应该只有自己所需要的访问权限，在一定时间内。

想象一下一个电脑管家。鉴于他们拥有电脑系统的很多控制权，对于他们允许全权访问看起来没问题。假如你有一个有三个管理员对公司，他们都有全权访问系统和正常运行的所有一切。但突然坏事发生了并且你的系统之一感染了malicious病毒。现在你不知道这是谁的锅？你意识到你应该授予他们基本用户权限且只在需要时给予特权。这就是所谓的**The Principle of Least Access**。看起来直观（intuitive）？这个原则也用于编程语言的设计，就是所谓的scope，在大多数编程语言中，包括我们下午要研究的JS。

在你的编程长途中，你将意识到你代码中的scope部分帮你提高效率，追踪并减少bug。Scope也解决了命名空间问题。记住，不要混淆scope和context。他们是不同feature。


<a name="js"></a>

# Scope in JavaScript

在JS语言里有两种类型scope：
- Global Scope
- Local Scope

定义在函数之内的变量是local scope，之外的是global scope。每个function被调用时创造新scope。

<a name="global"></a>

# Global Scope
 
当你在一个文档里开始写JS时，你已在Global scope里了。只有一个global scope通观一个JS文档。如果一个变量定义在一个function之外，那它就在global scope。
```
var name = 'Hammad';
```
global scope的变量可以在任意别的scope里访问和改变。
```
var name = 'Hammad';

console.log(name); // logs 'Hammad'

function logName() {
    console.log(name); // 'name' is accessible here and everywhere else
}

logName(); // logs 'Hammad'
```

<a name="local"></a>

# Local Scope
定义在函数里的变量都在local scope里。并且它们有不同的scope对应该方法的所有调用。意味着同名变量可以被用于不同的方法。这是因为那些变量暴露于其相应的方法，每个都有不同的scope，且在其他方法里不可访问。

相关课程：[Getting Started with JavaScript for Web Development](https://bit.ly/2rVqDcs)

```
// Global Scope
function someFunction() {
    // Local Scope #1
    function someOtherFunction() {
        // Local Scope #2
    }
}

// Global Scope
function anotherFunction() {
    // Local Scope #3
}
// Global Scope
```




<a name="block"></a>

# Block Statements
块语句如if和switch条件或者for和while循环，不同于function，不创建新scope。定义在块语句的变量将保留在其所在的scope里。
```
if (true) {
    // this 'if' conditional block doesn't create a new scope
    var name = 'Hammad'; // name is still in the global scope
}

console.log(name); // logs 'Hammad'
```

ES6推荐let和const关键字。这些关键字被用于取代var关键字。
```
var name = 'Hammad';

let likes = 'Coding';
const skills = 'Javascript and PHP';
```
与let对比之下，let和const支持块语句内的local scope的变量声明。

```
if (true) {
    // this 'if' conditional block doesn't create a scope

    // name is in the global scope because of the 'var' keyword
    var name = 'Hammad';
    // likes is in the local scope because of the 'let' keyword
    let likes = 'Coding';
    // skills is in the local scope because of the 'const' keyword
    const skills = 'JavaScript and PHP';
}

console.log(name); // logs 'Hammad'
console.log(likes); // Uncaught ReferenceError: likes is not defined
console.log(skills); // Uncaught ReferenceError: skills is not defined

```
Global scope 与你的app同生共死。Local scope与你的function被调用和执行同生死。

<a name="context"></a>

# Context
很多开发者常为scope和context感到困惑，好像他们指的是同一个概念。但事实并非如此。Scope时上述所说的而Context在你code的某些特定部分里常被用做this的引用。Scope指向变量的可访问性而context指向同一个scope下的this。我们也可以通过方法的方式改变context，后边会做讨论这个。在Global scope里context经常时window对象。

```
// logs: Window {speechSynthesis: SpeechSynthesis, caches: CacheStorage, localStorage: Storage…}
console.log(this);

function logFunction() {
    console.log(this);
}
// logs: Window {speechSynthesis: SpeechSynthesis, caches: CacheStorage, localStorage: Storage…}
// because logFunction() is not a property of an object
logFunction(); 
```

如果scope在一个对象的方法里，那么context将是方法所在的对象。

```
class User {
    logName() {
        console.log(this);
    }
}

(new User).logName(); // logs User {}
```
> (new User).logName() 是在一个变量里存储对象且调用其上的logName方法的简写。这里你无需create 新变量。

需要注意的一个事情是context的值会有不同如果你用new call function。context将被随之设置为被call的function。考虑下用new 关键字call上述例子。
```
function logFunction() {
    console.log(this);
}

new logFunction(); // logs logFunction {}
```
> 当一个function在strict mode里被call， context将默认为undefined。


<a name="execution"></a>

# Execution Context

为了移除所有困惑和我们上述所学，*context*在**ExecutionContext**里指向scope而非context。有个奇怪的命名惯例但鉴于JS的特点，我们被绑死在这里了。

JS是单线程的因此它可以在一个时间执行单个task。其余的task被放在Execution Context里排队。如我之前所说的JS解释器开始执行你的code，这时context（scope）默认为global。这个global context依附于事实上开启execution context的首个context的execution context。

而后，每个function call将依附于其context的EC。同样的事情发生于另一个方法在一个方法里或者其它别处被调用。
> 每个方法create其自己的EC。

一旦浏览器解析完其内的context，那个context将pop off于EC并且current context在EC里将被转入其parent context。浏览器经常执行在execution stack顶部的EC（你code的最内部级别的scope）。

> 只能有一个global context但可以任意个function context。

EC拥有creation和code execution这两个阶段。

### Creation Phase
首个阶段是creation部分当一个function被call，但其code并未executed。三个主要的事情在creation阶段是：

- Creation of the Variable (Activation) Object（创建变量对象）,
- Creation of the Scope Chain, and（创建Scope chain 和）
- Setting of the value of context (this)（设置上下文）

### Variable Object （变量对象）
变量对象，所谓的活动对象（activation object）包含所有变量，方法和其它在特定EC分支上的声明。当一个function被call，解释器扫描其内所有资源，包括function arguments，variables和other declarations，当被打包进一个single object，变成变量对象。
```
'variableObject': {
    // contains function arguments, inner variable and function declarations
}
```
### Scope Chain

在EC的creation 阶段， scope chain随VO创建之后而创建。scope chain本身包含VO。Scope Chain被用于解决变量，JS经常开始于code层的最里边并且保持跳回其parent scope直到找到变量或任何其它它所搜索的的资源。Scope chain可以简单地定义为一个包含其自身EC和所有其他其父EC的VO的对象，一个拥有其它对象的对象。

```
'scopeChain': {
    // contains its own variable object and other variable objects of the parent execution contexts
}
```
### The Execution Context(EC) Object
EC可以呈现为为如下抽象对象：
```
executionContextObject = {
    'scopeChain': {}, // contains its own variableObject and other variableObject of the parent execution contexts
    'variableObject': {}, // contains function arguments, inner variable and function declarations
    'this': valueOfThis
}
```

### Code Execution Phase
第二个EC的阶段，即Code Execution阶段，其它值被赋予且code最终被执行。


<a name="lexical"></a>

# Lexical Scope
Lecical Scope意味着函数嵌套，内部函数拥有访问其parent scope的变量和资源的权限。即child function词法性地bound to其parents的EC。词法作用域有时也指向Static Scope。

```
function grandfather() {
    var name = 'Hammad';
    // likes is not accessible here
    function parent() {
        // name is accessible here
        // likes is not accessible here
        function child() {
            // Innermost level of the scope chain
            // name is also accessible here
            var likes = 'Coding';
        }
    }
}
```
你讲注意到词法作用域即其work所向，即name可以背起children的EC访问。但反之不行。这也告诉我们在不同EC拥有同名的变量，在execution stask上自上而下获取优先权（precedence），更内的function将拥有更高的优先权。



<a name="closure"></a>

# Closures
闭包的概念与上述的Lexical Scope相近。当一个inner funtion试图访问其outer function的作用域链的时候，一个闭包被创建，即在立即词法作用域外（A Closure is created when an inner function tries to access the scope chain of its outer function meaning the variables outside of the immediate lexical scope）。闭包包含其自己的scope chain，这个scope chain包含了其parents和global的scope。

> 一个闭包不仅可以访问其outer function里的variables，也可以访问其outer function里的arguments。

一个闭包也可以访问其outer function里的变量，即便这个function被return。这点允许返回的function活的其outer function的访问所有资源的权限。

当你从一个function返回一个inner function时，当你试图call outer function时返回的function将不能被call。你必须首先保存outer function的调用，在一个独立的变量里，然后作为function调用。考虑如下例子：
```
function greet() {
    name = 'Hammad';
    return function () {
        console.log('Hi ' + name);
    }
}

greet(); // nothing happens, no errors

// the returned function from greet() gets saved in greetLetter
greetLetter = greet();

 // calling greetLetter calls the returned function from the greet() function
greetLetter(); // logs 'Hi Hammad'
```
关键的之处在于```that greetLetter ```function可以访问greet function里的name变量，即便其被return。一个没有变量声明的调用从greet function返回函数的方式是利用parentheses（）两次（）（），如下：
```
function greet() {
    name = 'Hammad';
    return function () {
        console.log('Hi ' + name);
    }
}

greet()(); // logs 'Hi Hammad'
```



<a name="public"></a>

# Public and Private Scope
很多别的编程语言，你可以设置一个class的属性和方法的访问权限，利用public和private和protected scope。考虑如下php语言的例子：
```
// Public Scope
public $property;
public function method() {
  // ...
}

// Private Sccpe
private $property;
private function method() {
  // ...
}

// Protected Scope
protected $property;
protected function method() {
  // ...
}
```
public（global）scope包裹function使其免于攻击修改。但JS中没有这个如public或private scope的东西。然而，我们可以可以利用闭包实现这个feature。要保持所有东西独立于global我们必须首先如下包裹function：
```
(function () {
  // private scope
})();
```
函数最后的分号告诉解释器立即执行。我们可以添加方法和变量在其内，它们在外不可访问。但如果我们想在外访问，即我们想让它们一些变成public一些变成private？一种闭包类型我们可用，就是所谓的Module Pattern，这允许我们scope我们的function，利用public和private scope，在一个object。
### The Module Pattern

```
var Module = (function() {
    function privateMethod() {
        // do something
    }

    return {
        publicMethod: function() {
            // can call privateMethod();
        }
    };
})();
```
Module里的return 语句包含我们的public function。没有返回的都是private function。不返回function让其不可访问。但我们public function可以访问private function使得其对于helper funcion如ajax和其它的东西可控。
```
Module.publicMethod(); // works
Module.privateMethod(); // Uncaught ReferenceError: privateMethod is not defined
```
一个惯例是给私有function加underscore，返回匿名对象，包含public function。这使得容易维护一个长对象。看起来如下：

```
var Module = (function () {
    function _privateMethod() {
        // do something
    }
    function publicMethod() {
        // do something
    }
    return {
        publicMethod: publicMethod,
    }
})();
```
### Immediately-Invoked Function Expression (IIFE)
另一个类型的闭包是IIFE。




<a name="change"></a>

# Changing Context with .call(), .apply() and .bind()
call和apply被用于改变context，当调用一个function时。这给予你难以置信的编程能力（和一些巨大的能力去重组世界）。
```
function hello() {
    // do something...
}

hello(); // the way you usually call it
hello.call(context); // here you can pass the context(value of this) as the first argument
hello.apply(context); // here you can pass the context(value of this) as the first argument
```
call和apply的区别在于其参数：
```
function introduce(name, interest) {
    console.log('Hi! I\'m '+ name +' and I like '+ interest +'.');
    console.log('The value of this is '+ this +'.')
}

introduce('Hammad', 'Coding'); // the way you usually call it
introduce.call(window, 'Batman', 'to save Gotham'); // pass the arguments one by one after the contextt
introduce.apply('Hi', ['Bruce Wayne', 'businesses']); // pass the arguments in an array after the context

// Output:
// Hi! I'm Hammad and I like Coding.
// The value of this is [object Window].
// Hi! I'm Batman and I like to save Gotham.
// The value of this is [object Window].
// Hi! I'm Bruce Wayne and I like businesses.
// The value of this is Hi.
```
> call 在性能上比apply稍快。

如下示例获取文档的list表单并挨个打印到log
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Things to learn</title>
</head>
<body>
    <h1>Things to Learn to Rule the World</h1>
    <ul>
        <li>Learn PHP</li>
        <li>Learn Laravel</li>
        <li>Learn JavaScript</li>
        <li>Learn VueJS</li>
        <li>Learn CLI</li>
        <li>Learn Git</li>
        <li>Learn Astral Projection</li>
    </ul>
    <script>
        // Saves a NodeList of all list items on the page in listItems
        var listItems = document.querySelectorAll('ul li');
        // Loops through each of the Node in the listItems NodeList and logs its content
        for (var i = 0; i < listItems.length; i++) {
          (function () {
            console.log(this.innerHTML);
          }).call(listItems[i]);
        }

        // Output logs:
        // Learn PHP
        // Learn Laravel
        // Learn JavaScript
        // Learn VueJS
        // Learn CLI
        // Learn Git
        // Learn Astral Projection
    </script>
</body>
</html>
```
HTML只包含一个无序列表的items。JS然后从dom里全选它们，list被循环直到最后一个item。

Object可以有methonds，如function的object也可以有方法。事实上，JS function拥有如下内嵌的方法：
- Function.prototype.apply()
- Function.prototype.bind() (Introduced in ECMAScript 5 (ES5))
- Function.prototype.call()
- Function.prototype.toString()

直到现在我们讨论了.call(), .apply(), and toString()。不同于call和apply，bind自己不call function，它只被用于bindcontext值和别的调用function的arguments。如下事例：
```
(function introduce(name, interest) {
    console.log('Hi! I\'m '+ name +' and I like '+ interest +'.');
    console.log('The value of this is '+ this +'.')
}).bind(window, 'Hammad', 'Cosmology')();

// logs:
// Hi! I'm Hammad and I like Cosmology.
// The value of this is [object Window].
```
Bind如call function， 允许你传递其余arguments，一个接一个，通过逗号分隔，不像apply。


<a name="conslusion"></a>

# Conclusion

这些概念对于JS很激进，重要的是去理解，如果你想深入更高阶的话题。我希望你对JS Scope和围绕它的事情能获得一个更好的理解。






