---
date: 2018-02-05
layout: post
title: JavaScript 核心（第二版）
thread: 9
categories: Translate
tags: [JS,prototype, ES6, ES2017]
excerpt: JavaScript 核心（第二版）
---

> 原文 - [http://dmitrysoshnikov.com/ecmascript/javascript-the-core-2nd-edition/?utm_source=mybridge&utm_medium=email&utm_campaign=read_more](http://dmitrysoshnikov.com/ecmascript/javascript-the-core-2nd-edition/?utm_source=mybridge&utm_medium=email&utm_campaign=read_more)
>
> 原文作者 - [Dmitry Soshnikov](http://dmitrysoshnikov.com/) in ECMAScript	| 2017-11-14
>
> 译者 - [yanlee](https://github.com/yanlee26)
>
> 译文地址 - <https://yanlee26.github.io/2018/02/05/JavaScript-The-Core-2nd-Edition/>

这是第二版中的[JavaScript的。Core](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/)概述讲座，致力于ECMAScript编程语言及其运行时系统的核心组件。


**观众**：有经验的程序员，专家。

1. [Object](#object)
2. [Prototype](#prototype)
3. [Class](#class)
4. [Execution context](#context)
5. [Environment](#environment)
6. [Closure](#closure)
7. [This](#this)
8. [Realm](#realm)
9. [Job](#job)
10. [Agent](#agent)


在[第一版](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/)的文章涵盖了JS语言的通用方面，采用抽象大多是从传统ES3规范，与在ES5和ES6（又名ES2015）进行适当的修改提供一些参考。

自ES2015开始，规范改变了一些核心组件的描述和结构，引入了新的模型等。在这个版本中，我们关注更新的抽象，更新的术语，但仍然保持非常基本的JS结构，在整个规范版本中保持一致。

本文介绍ES2017 +运行时系统。


**注意**：最新版本的ECMAScript规范可以在TC-39网站上找到。

我们从一个*object*的概念开始讨论，这个*object*是ECMAScript的基础。

<a name="object"></a>
# Object

ECMAScript是一个*object-oriented*的编程语言，它是基于原型的组织，以*object*的概念为核心抽象。

> 1：**对象：**一个object是一个属性的集合，并有一个单一的原型对象(prototype)。原型可能是一个对象或null。

我们来看一个object的基本例子。内部[[Prototype]]属性引用了object的原型，该__proto__属性通过该属性暴露给用户级代码。

代码如下：
```
let point = {
  x: 10,
  y: 20,
};
```
我们有两个显示的（explicit）自己的属性和一个隐含的（implicit__proto__属性的结构，这是对原型的引用point：

![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/js-object.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/js-object.png)

图1.带有原型的基本对象

**Note:** objects may store also symbols. You can get more info on symbols in [this documentation.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

原型对象用于实现动态调度机制（dynamic dispatch）的继承。我们来考虑一下*原型链*概念，以便详细了解这个机制

<a name="prototype"></a>
# Prototype

每一个对象，当被创建时，都会接收到它的原型。如果原型没有*明确*设置，对象接收*默认原型default prototyp*作为它们的*继承对象inheritance object*。

> 2：Prototype:：原型是一个实现*基于原型prototype-based inheritance*的继承的代理对象delegation object。

原型可以通过属性或方法明确设置：__proto__或者Object.create

```
// Base object.
let point = {
  x: 10,
  y: 20,
};
 
// Inherit from `point` object.
let point3D = {
  z: 30,
  __proto__: point,
};
 
console.log(
  point3D.x, // 10, inherited
  point3D.y, // 20, inherited
  point3D.z  // 30, own
);
```
注意：默认情况下，对象接收Object.prototype为其继承对象。

任何对象都可以作为另一个对象的原型，原型本身可以有自己的原型。如果原型具有对其原型的非空*non-null*引用等，则称其为原型链*prototype chain*。

> 3：原型链：原型链是一个用于实现对象的链继承*implement inheritance*和共享属性*shared properties*的有限链*finite*。

![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/prototype-chain.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/prototype-chain.png)

图2.一个原型链。

规则非常简单：如果在对象本身中找不到属性，则试图在原型中解决*resolve*该属性; 在原型的原型等 - 直到整个原型链被访问。

从技术上讲，这种机制被称为动态调度或委托*dynamic dispatch or delegation.*。

> 4：委托：用于解决继承链中属性的机制。该过程发生在运行时，因此也被称为动态调度。

注意：与在编译时解析引用的静态调度相反，动态调度在运行时解析引用。

并且如果最终在原型链中找不到属性，该值则返回undefined：
```
// An "empty" object.
let empty = {};
 
console.log(
 
  // function, from default prototype
  empty.toString,
   
  // undefined
  empty.x,
 
);
```
正如我们所看到的，默认的对象实际上是永远不会空的*never empty* -它总是从Object.prototype继承的东西。为了创建一个无原型的字典，我们必须明确地将其原型设置为null：

```
// Doesn't inherit from anything.
let dict = Object.create(null);
 
console.log(dict.toString); // undefined
```
该动态调度机制允许*full mutability*充分的可变性的继承链，提供改变代理对象的能力：
```
let protoA = {x: 10};
let protoB = {x: 20};
 
// Same as `let objectC = {__proto__: protoA};`:
let objectC = Object.create(protoA);
console.log(objectC.x); // 10
 
// Change the delegate:
Object.setPrototypeOf(objectC, protoB);
console.log(objectC.x); // 20
```
**注意：** 即使__proto__属性是当今规范，并且有更容易使用的说法，在实践上更喜欢使用API方法原型操作，比如```Object.create，Object.getPrototypeOf，
Object.setPrototypeOf```，和类似的```Reflect```模块。

在Object.prototype例子中，我们看到同一个原型可以在多个对象之间共享。基于这个原则，基于类的继承*class-based inheritance*在ECMAScript中实现*implemented*。让我们来看看这个例子，并且看一下JS中“类”抽象的底层。

<a name="class"></a>
# Class

当几个对象共享相同的初始状态和行为时，它们形成一个分类*classification*。

> **5：Class：**一个类是一个标准的抽象集，它指定了它的对象的初始状态和行为。

如下我们需要从同一个原型继承多个对象，我们当然可以创建这个原型，并从新创建的对象中明确地继承它：
```
// Generic prototype for all letters.
let letter = {
  getNumber() {
    return this.number;
  }
};
 
let a = {number: 1, __proto__: letter};
let b = {number: 2, __proto__: letter};
// ...
let z = {number: 26, __proto__: letter};
 
console.log(
  a.getNumber(), // 1
  b.getNumber(), // 2
  z.getNumber(), // 26
);
```
我们可以在下图中看到这些关系：

![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/shared-prototype.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/shared-prototype.png)
图3.共享原型

但是，这显然是麻烦的*cumbersome*。而类抽象服务应运而生 - 作为一个语法糖（即语义上相同，但是以更好的语法形式）的构造，它允许用方便的模式创建这样的多个对象：
```
class Letter {
  constructor(number) {
    this.number = number;
  }
 
  getNumber() {
    return this.number;
  }
}
 
let a = new Letter(1);
let b = new Letter(2);
// ...
let z = new Letter(26);
 
console.log(
  a.getNumber(), // 1
  b.getNumber(), // 2
  z.getNumber(), // 26
);
```
**注：** ECMAScript中基于类的继承是在基于原型的委托之上实现的。

**注：**一个“类”仅仅是一个理论抽象。从技术上讲，它可以像在Java或C ++中一样使用静态分发，也可以在JavaScript，Python，Ruby等中动态分发（委托）。

从技术上讲，“class”被表示为“constructor function + prototype”对。因此，构造函数创建对象，并自动为其新创建的实例设置原型。这个原型存储在```<ConstructorFunction>.prototype```属性中。

> **6：Constructor：**构造器是用于创建实例，并自动设置其原型的功能。

显式使用构造函数是可能的。而且，在引入类抽象之前，JS开发者曾经这么做过，没有更好的选择（我们仍然可以在互联网上找到很多这样的遗留代码）：

```
function Letter(number) {
  this.number = number;
}
 
Letter.prototype.getNumber = function() {
  return this.number;
};
 
let a = new Letter(1);
let b = new Letter(2);
// ...
let z = new Letter(26);
 
console.log(
  a.getNumber(), // 1
  b.getNumber(), // 2
  z.getNumber(), // 26
);
```

虽然创建单层*single-level*构造函数非常容易，但是父类的继承模式需要更多的样板*boilerplate*。目前，这个样板文件作为一个实现细节*implementation detail*被隐藏起来，而这正是我们在JavaScript中创建一个类时底层发生的。

注意： 构造函数只是基于类的继承的细节实现。

让我们看看对象和他们的类的关系：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/js-constructor.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/js-constructor.png)
图4.构造函数和对象关系

上图显示每个对象都有一个关联的原型。即使构造函数（类）```Letter```也有自己的原型，这是```Function.prototype```。请注意，这```Letter.prototype```是字母的原型实例，那就是```a```，```b```和```z```。

**注意：**任何对象的实际原型始终是__proto__引用。而构造函数的显式prototype属性只是对其实例原型的引用 ; 从实例它仍然被__proto__引用。在 [这里](http://dmitrysoshnikov.com/ecmascript/chapter-7-2-oop-ecmascript-implementation/#explicit-codeprototypecode-and-implicit-codeprototypecode-properties)看到细节

您可以在ES3中找到关于通用OPP概念的详细讨论（包括基于类的原型的详细描述等）。[ES3 7.1 OOP](http://dmitrysoshnikov.com/ecmascript/chapter-7-1-oop-general-theory/)：一般的理论文章。

现在，当我们理解ECMAScript对象之间的基本关系时，让我们深入了解一下JS 运行时系统。我们将会看到，几乎所有的东西都可以作为一个对象来呈现。

<a name="context"></a>
# Context

为了执行JS代码并跟踪其运行时评估，ECMAScript规范定义了执行上下文的概念。逻辑执行上下文是使用一个堆栈*stack*（执行上下文堆栈，我们将很快看到）来维护，这对应于调用堆栈的通用概念。

> **7：执行上下文：** 一个执行上下文是用于跟踪码的运行时评估的规范装置。

ECMAScript代码有几种类型：全局代码，功能代码，eval代码和模块代码*the global code, function code, eval code, and module code;* ; 每个代码都在其执行上下文中进行评估。不同的代码类型及其适当的对象可能会影响执行上下文的结构：例如，生成器函数将其生成器对象保存在上下文中。

让我们考虑一个递归函数调用：
```
function recursive(flag) {
 
  // Exit condition.
  if (flag === 2) {
    return;
  }
 
  // Call recursively.
  recursive(++flag);
}
 
// Go.
recursive(0);
```
当一个函数被调用时，一个新的执行上下文被创建，并被压入堆栈 - 在这一点上它成为一个活跃的执行上下文*active execution context*。当一个函数返回时，它的上下文从堆栈中弹出*popped*。

调用另一个上下文的上下文称为*caller*。而被调用的上下文也是*callee*。在我们的例子中，```recursive```函数扮演着一个被调用者和一个调用者的角色 - 当递归调用自己时。

> **8：执行上下文堆栈**：一个执行上下文堆栈是用于维持执行的控制流程和顺序的LIFO结构。

对于我们上面的示例，我们有以下堆栈“push-pop”变化：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/execution-stack.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/execution-stack.png)
图5.一个执行上下文栈

我们也可以看到，全局上下文始终处于堆栈的底部，在执行任何其他上下文之前创建。

你可以在 [特定的章节](http://dmitrysoshnikov.com/ecmascript/chapter-1-execution-contexts/)中找到关于执行上下文的更多细节。

一般情况下，上下文的代码运行到完成，但正如我们上面提到的，一些对象，如生成器，可能会违反堆栈的LIFO顺序。生成器函数可以暂停其运行上下文，并在完成之前将其从堆栈中移除。一旦发生器再次被激活，其上下文恢复并再次被推入堆栈：

```
function *gen() {
  yield 1;
  return 2;
}
 
let g = gen();
 
console.log(
  g.next().value, // 1
  g.next().value, // 2
);
```

yield这里的语句将该值返回给调用者，并弹出上下文。在第二次next调用时，同样的上下文再次被压入堆栈，并被恢复*resumed*。这样的背景可能比创造它的caller长，因此违反了后进先出法的结构。

注意您可以在[本文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)中阅读关于生成器和迭代器的更多信息。

现在我们将讨论执行环境的重要组成部分。特别是我们应该看看ECMAScript运行时如何管理变量存储以及由代码的嵌套块创建的作用域。这是词汇环境的通用概念，用于JS存储数据，并用闭包机制解决“Funarg问题”。

<a name="environment"></a>
# Environment

每个执行上下文都有一个相关的词法环境*lexical environment*。

> **9：lexical environment：**词法环境是用于定义出现在与它们的值的上下文之间的关联的结构。每个环境都可以引用可选的父级环境。

所以一个环境是在一个范围内定义的变量，函数和类的*variables, functions, and classes *存储。

从技术上讲，一个环境是一对*pair*，由一个环境记录（一个实际的存储表，将标识符映射到值）和一个对父（可以是null）的引用组成。

代码：
```
let x = 10;
let y = 20;
 
function foo(z) {
  let x = 100;
  return x + y + z;
}
 
foo(30); // 150
```
全局上下文的环境结构和foo函数的上下文如下所示：

![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/environment-chain.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/environment-chain.png)

图6.一个环境链。

从逻辑上讲，这提醒了我们上面讨论过的原型链。标识符解析的规则非常相似：如果在自己的环境中找不到变量，则尝试在父环境中，在父级的父级中查找它，等等 - 直到整个环境链被遍历。

> **10：标识符解析Identifier resolution**：解析环境链中变量（绑定）的过程。unresolved的绑定导致```ReferenceError```。

这就解释了为什么变量x是解决的100，但不是10- 直接在自己的环境中找到foo; 为什么我们可以访问参数z- 它也只是存储在激活环境中 ; 也是为什么我们可以访问变量y- 它是在父母的环境中找到的。

与原型类似，相同的父级环境可以由多个子级环境共享：例如，两个全局函数共享相同的全局环境。

**注意：**你可以在[本文](http://dmitrysoshnikov.com/ecmascript/es5-chapter-3-2-lexical-environments-ecmascript-implementation/)中获得有关词汇环境的详细信息。

环境记录因类型type而异。有对象object环境记录和声明式declarative环境记录。在声明性记录之上还有function环境记录和模块module环境记录。每种类型的记录都只有它的特性。但是，标识符解析的通用机制在所有环境中都是通用的，并且不依赖于记录的类型。

对象环境记录的例子可以是global环境的记录。这样的记录也有相关联的绑定对象binding object，它可以存储记录中的一些属性，而不是其他属性，反之亦然。绑定对象也可以作为this值来提供。
```
// Legacy variables using `var`.
var x = 10;
 
// Modern variables using `let`.
let y = 20;
 
// Both are added to the environment record:
console.log(
  x, // 10
  y, // 20
);
 
// But only `x` is added to the "binding object".
// The binding object of the global environment
// is the global object, and equals to `this`:
 
console.log(
  this.x, // 10
  this.y, // undefined!
);
 
// Binding object can store a name which is not
// added to the environment record, since it's
// not a valid identifier:
 
this['not valid ID'] = 30;
 
console.log(
  this['not valid ID'], // 30
);
```
如下图所示：

![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/env-binding-object.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/env-binding-object.png)
图7.绑定对象


请注意，绑定对象的存在是为了覆盖legacy constructs ，例如var -declarations和with- statements，它们也将它们的对象作为绑定对象提供。当环境被表示为简单对象时，这是历史原因。目前，环境模型更加优化，但结果是，我们无法再作为属性访问绑定。

我们已经看到环境是如何通过父链接相关的。现在我们将看到一个环境如何超越创造它的环境。这是我们即将讨论的closures机制的基础。

<a name="closure"></a>
# Closure

ECMAScript中的函数是first-class一等公民。这个概念是函数式编程的基础，JavaScript支持哪些方面。

> **11：First-class function：**可以作为普通数据参与的函数：存储在变量中，作为参数传递，或作为另一个函数的值返回。

与first-class的概念所谓的“Funarg问题”是相关的（或“一个功能论点的问题”）。当函数需要处理自由变量时，问题就出现了。

> **12：自由变量：**一个变量，它是既不是参数，也不是局部变量这个函数。

让我们来看看Funarg问题，看看它在ECMAScript中是如何解决的。

考虑下面的代码片段：
```
let x = 10;
 
function foo() {
  console.log(x);
}
 
function bar(funArg) {
  let x = 20;
  funArg(); // 10, not 20!
}
 
// Pass `foo` as an argument to `bar`.
bar(foo);
```
对于函数来说foo变量x是空闲的。当foo功能激活（通过funArg参数） - 它应该在哪里解决x绑定？从外部范围其中函数被创建，或者从呼叫者范围，从那里的功能被称为？正如我们所看到的，调用者，也就是bar函数，也为x值提供了绑定20。

上面所描述的用例被称为向下的funarg问题，即在确定绑定的正确环境时的模糊性：它应该是创建时间的环境，还是呼叫时间的环境？

这是通过使用静态范围的协议来解决的，也就是创建时间的范围。

> 13：静态范围：一种语言实现静态范围，如果只通过查看源代码就可以确定绑定在哪个环境中解决。

静态范围有时也被称为词法范围，因此也就是词法环境的命名。

从技术上讲，静态范围是通过捕获创建函数的环境来实现的。

注意：您可以阅读本文中的静态和动态范围。

在我们的例子中，foo函数捕获的环境是全球环境：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/closure.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/closure.png)
图8.一个闭包

我们可以看到，一个环境指向函数，这反过来这个函数又指向环境。

> **14：Closure:**闭包一个在其定义时捕获环境的函数。此外，此环境用于标识符解析。

**注意：**在全新的活动对象*activation environment*中调用一个函数，该环境存储局部变量和参数。活动对象的父环境被设置为函数的封闭环境*closured environment*，从而产生词法*lexical scope*范围语义。（好晦涩。。。）

Funarg问题的第二个子类型被称为向上的funarg*upward funarg problem*问题。这里唯一的区别是捕捉环境超出*outlives*创建它的上下文。

我们来看一个例子：
```
function foo() {
  let x = 10;
   
  // Closure, capturing environment of `foo`.
  function bar() {
    return x;
  }
 
  // Upward funarg.
  return bar;
}
 
let x = 20;
 
// Call to `foo` returns `bar` closure.
let bar = foo();
 
bar(); // 10, not 20!
```
同样，从技术上说，它与捕获定义环境的确切机制没有区别。就在这种情况下，如果没有闭包，活动上下文foo 就会被破坏。但是我们捕获了它，所以它不能被释放*deallocated*，并被保留 - 以支持静态作用域语义。

通常对闭包的理解是不完整的 - 通常开发人员仅仅考虑向上的问题（实际上更有意义）来考虑闭包。但是，正如我们所看到的，向下和向上函数问题的技术机制是完全一样的 - 而且是静态范围的机制。

正如我们上面提到的，与原型类似，可以跨几个闭包共享相同的父环境。这允许访问和变更共享数据：
```
function createCounter() {
  let count = 0;
 
  return {
    increment() { count++; return count; },
    decrement() { count--; return count; },
  };
}
 
let counter = createCounter();
 
console.log(
  counter.increment(), // 1
  counter.decrement(), // 0
  counter.increment(), // 1
);
```
由于两个封闭件，increment和decrement被包含的范围之内创建的count变量，它们共享这个*parent scope*。也就是说，捕获总是发生“通过引用” - 意味着对整个父环境的引用被存储。

我们可以在下面的图片看到这个：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/shared-environment.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/shared-environment.png)
图9.共享环境

有些语言可能会捕获值，制作捕获的变量的副本，并且不允许在父范围中更改它。但是，在JS中，重复一遍，它始终是对父范围的引用。

**注意：**实现可能优化这一步，而不捕获整个环境。只捕获使用的自由变量*free-variables*，但它们仍然是在父范围内保持不变的可变数据*invariant of mutable*。？？

你可以在 [适当的章节](http://dmitrysoshnikov.com/ecmascript/chapter-6-closures/)中找到有关闭包和Funarg问题的详细讨论。

所以所有的标识符都是静态的。然而，在ECMAScript中有一个动态范围的值。这是价值this。

<a name="this"></a>
# This

该this值是一个特殊的对象，动态地和隐式地传递给上下文的代码。我们可以把它看作是一个隐含的额外参数，我们可以访问，但是不能改变。

this值的目的是为多个对象执行相同的代码。

> **15：This**一个隐式的上下文对象，可以从一个执行上下文的代码中访问，以便为多个对象应用相同的代码。

主要的用例是基于类的OOP。一个实例方法（在原型上定义）存在于一个范例中，但在该类的所有实例中共享。
```
class Point {
  constructor(x, y) {
    this._x = x;
    this._y = y;
  }
 
  getX() {
    return this._x;
  }
 
  getY() {
    return this._y;
  }
}
 
let p1 = new Point(1, 2);
let p2 = new Point(3, 4);
 
// Can access `getX`, and `getY` from
// both instances (they are passed as `this`).
 
console.log(
  p1.getX(), // 1
  p2.getX(), // 3
);
```
当该getX方法被激活时，会创建一个新的环境来存储局部变量和参数。另外，函数环境记录获取[[ThisValue]]传入的参数，这个参数是根据函数的调用方式动态绑定的。当它被p1调用时，this是完全相同的，而在第二种情况下则相应为p2。

另一个应用this是泛型接口函数（generic interface functions），可以用在mixin或traits中。

在下面的例子中，Movable接口包含泛型函数move，该泛型函数期望这个mixin的用户能够实现_x以及_y属性：

```
// Generic Movable interface (mixin).
let Movable = {
 
  /**
   * This function is generic, and works with any
   * object, which provides `_x`, and `_y` properties,
   * regardless of the class of this object.
   */
  move(x, y) {
    this._x = x;
    this._y = y;
  },
};
 
let p1 = new Point(1, 2);
 
// Make `p1` movable.
Object.assign(p1, Movable);
 
// Can access `move` method.
p1.move(100, 200);
 
console.log(p1.getX()); // 100
```
作为替代方案，mixin也可以应用于原型级别，而不是像上例中那样每个实例。

为了展现this价值的动态性，考虑这个例子，我们把这个例子留给读者来解决：
```
function foo() {
  return this;
}
 
let bar = {
  foo,
 
  baz() {
    return this;
  },
};
 
// `foo`
console.log(
  foo(),       // global or undefined
 
  bar.foo(),   // bar
  (bar.foo)(), // bar
 
  (bar.foo = bar.foo)(), // global
);
 
// `bar.baz`
console.log(bar.baz()); // bar
 
let savedBaz = bar.baz;
console.log(savedBaz()); // global
```
因为只有通过查看foo函数的源代码，我们才能知道this它在特定的调用中会有什么价值，所以我们说这个this值是动态范围的。

**注意：**您可以详细解释如何this确定价值，以及为什么上面的代码在[适当的章节](http://dmitrysoshnikov.com/ecmascript/chapter-3-this/)中按照它的方式工作。

箭头函数（arrow functions）是在以下方面的特殊this价值：它们this是词法（静态），而不是动态的。即他们的function环境记录不提供this值，而是从父环境中获取。
```
var x = 10;
 
let foo = {
  x: 20,
 
  // Dynamic `this`.
  bar() {
    return this.x;
  },
 
  // Lexical `this`.
  baz: () => this.x,
 
  qux() {
    // Lexical this within the invocation.
    let arrow = () => this.x;
 
    return arrow();
  },
};
 
console.log(
  foo.bar(), // 20, from `foo`
  foo.baz(), // 10, from global
  foo.qux(), // 20, from `foo` and arrow
);
```
就像我们所说的，在global context内，this值是全局对象（全球环境记录的绑定对象）。 以前只有一个全局对象。 在当前版本的规范中，可能有多个全局对象是代码领域(realm)的一部分。 我们来讨论一下这个结构。

<a name="realm"></a>
# Realm

在被评估之前，所有ECMAScript代码都必须与一个领域realm相关联。从技术上来说，一个领域只是提供一个环境的全局环境。

>**16：Realm：**代码realm是一个对象，它封装了单独的全局环境。

当一个执行上下文被创建时，它与一个特定的代码领域相关联，这个代码领域为这个上下文提供了全局的环境。该关联进一步保持不变。

**注意：**浏览器环境中的直接领域是iframe元素，它恰好提供了一个自定义的全局环境。在Node.js中，它靠近[vm模块](https://nodejs.org/api/vm.html)的沙箱。

规范的当前版本没有提供显式创建realms的能力，但是它们可以由实现隐含地创建。有一个[提议](https://github.com/tc39/proposal-realms/)，致以公开这个API的用户代码。

从逻辑上讲，堆栈中的每个上下文总是与其realm相关联：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/context-realm.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/context-realm.png)
图10.上下文和realm关联

让我们看看单独的realm的例子，使用vm模块：
```
const vm = require('vm');
 
// First realm, and its global:
const realm1 = vm.createContext({x: 10, console});
 
// Second realm, and its global:
const realm2 = vm.createContext({x: 20, console});
 
// Code to execute:
const code = `console.log(x);`;
 
vm.runInContext(code, realm1); // 10
vm.runInContext(code, realm2); // 20
```
现在我们正在接近ECMAScript运行时的大样。然而，我们仍然需要看到代码的入口点和初始化过程。这是由工作机会和工作队列管理（jobs and job queues.）的。

<a name="job"></a>
# Job

一些操作可以被推迟，并在执行上下文堆栈上有可用点时立即执行。

> **17：Job：**一个job是一个抽象操作时，将启动一个ECMAScript的计算没有其他的ECMAScript计算是目前正在进行中。

作业排队在作业队列中，在当前的spec版本中有两个作业队列：**ScriptJobs**和**PromiseJobs**。

和最初工作的ScriptJobs队列为主要切入点，以我们的节目-这是装载并计算初始脚本：一种境界创建，全球范围内创建并与该领域相关的，它是压入堆栈，以及全局代码被执行。

注意，ScriptJobs队列管理着脚本和模块。

此外，这个上下文可以执行其他上下文，或排队其他作业。一个可以产生和排队的job的例子是一个promise。

如果没有正在运行的执行上下文，并且执行上下文堆栈为空，则ECMAScript实现会从job队列中删除第一个挂起的作业，创建执行上下文并开始执行。

**注意：**job队列通常由被称为“事件循环”的抽象来处理。ECMAScript标准没有指定事件循环，而是将其留给实现，但是您可以在这里找到一个教育示例。

例：
```
// Enqueue a new promise on the PromiseJobs queue.
new Promise(resolve => setTimeout(() => resolve(10), 0))
  .then(value => console.log(value));
 
// This log is executed earlier, since it's still a
// running context, and job cannot start executing first
console.log(20);
 
// Output: 20, 10
```
**注意：**您可以在本文档中阅读有关承诺的更多信息。

该async function可以awaitpromise，所以他们也排队promise的工作：

```
async function later() {
  return await Promise.resolve(10);
}
 
(async () => {
  let data = await later();
  console.log(data); // 10
})();
 
// Also happens earlier, since async execution
// is queued on the PromiseJobs queue.
console.log(20);
 
// Output: 20, 10
```
注意：在[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)阅读更多关于异步函数。

现在我们已经非常接近当前JS Universe的最终画面。现在我们将看到我们讨论的所有组件的主要所有者，Agent。

<a name="agent"></a>
# Agent

在并发和并行(concurrency and parallelism)使用ECMAScript中实现的代理模式。代理模式非常接近Actor模式 - 一个具有消息传递风格的轻量级进程。

> **18：代理：**一个agent是一种抽象封装执行上下文堆栈，将作业队列，和代码realms的。

依赖代理的实现可以在同一个线程上运行，也可以在单独的线程上运行。将Worker在浏览器环境中代理的一个例子代理的概念。

代理是相互隔离的状态，可以通过发送消息进行通信。一些数据可以在代理之间共享，例如SharedArrayBuffers。代理也可以组合成代理群集。

在下面的例子中，index.html调用agent-smith.jsworker，传递共享的内存块：
```
// In the `index.html`:
 
// Shared data between this agent, and another worker.
let sharedHeap = new SharedArrayBuffer(16);
 
// Our view of the data.
let heapArray = new Int32Array(sharedHeap);
 
// Create a new agent (worker).
let agentSmith = new Worker('agent-smith.js');
 
agentSmith.onmessage = (message) => {
  // Agent sends the index of the data it modified.
  let modifiedIndex = message.data;
 
  // Check the data is modified:
  console.log(heapArray[modifiedIndex]); // 100
};
 
// Send the shared data to the agent.
agentSmith.postMessage(sharedHeap);
```
而worker代码：

```
// agent-smith.js
 
/**
 * Receive shared array buffer in this worker.
 */
onmessage = (message) => {
  // Worker's view of the shared data.
  let heapArray = new Int32Array(message.data);
 
  let indexToModify = 1;
  heapArray[indexToModify] = 100;
 
  // Send the index as a message back.
  postMessage(indexToModify);
};
```
你可以在这个[要点](https://gist.github.com/DmitrySoshnikov/b75a2dbcdb60b18fd9f05b595135dc82)中找到上面例子的完整代码。

（注意，如果你在本地运行这个例子，请在Firefox中运行它，因为由于安全原因，Chrome不允许从本地文件加载web worker）

下面是ECMAScript运行时的图片：
![http://dmitrysoshnikov.com/wp-content/uploads/2017/11/agents-1.png](http://dmitrysoshnikov.com/wp-content/uploads/2017/11/agents-1.png)
图11. ECMAScript运行时。

就是这样; 那就是在ECMAScript引擎的引擎下发生的事情！

现在我们走到了尽头。这是我们可以在概述文章中涵盖的JS内核的信息量。就像我们提到的，JS代码可以被分组到模块中，对象的属性可以被对象跟踪Proxy等。 - 许多用户级别的细节可以在JavaScript语言的不同文档中找到。

尽管我们试图表示一个ECMAScript程序本身的逻辑结构，希望能够澄清这些细节。如果您有任何问题，建议或反馈意见，我将一如既往地乐于在评论中讨论这些问题。

我要感谢TC-39代表和规范编辑帮助澄清本文。讨论可以在这个Twitter主题中找到。

祝您学习ECMAScript！

撰写者： Dmitry Soshnikov 
发布日期： 2017年11月14日



