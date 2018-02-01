---
date: 2017-10-21
layout: post
title: Script Loading
thread: 9
categories: Translate
tags: [BIT]
excerpt: script loading
---

> Introduction

在这篇文章中，我将教你如何在浏览器中加载一些JavaScript并执行它。

不，等等，回来！ 我知道这听起来很平常而且简单，但请记住，浏览器中发生这种情况时，理论上简单的问题就变成了传统驱动的怪癖。 了解这些怪癖可以让您选择最快，最不干扰的方式来加载脚本。 如果您的日程安排紧张，请跳到快速参考。

对于初学者来说，这是规范如何定义脚本可以下载和执行的各种方式：

像WHATWG的所有规格一样，它最初看起来像一个拼字游戏集群中的集束炸弹的后遗症，但是一旦你第五次阅读并擦掉了你的眼睛里的血液，这实际上是非常有趣的：

首个script 包括

```

<script src="//other-domain.com/1.js"></script>

<script src="2.js"></script>

```



啊，幸福的简单。在这里，浏览器将同时下载两个脚本，并尽快执行它们，维护它们的顺序。 “2.js”只有在“1.js”执行完毕（或不执行）之后才会执行，“1.js”只有在前一个脚本或样式表执行完毕后才会执行，等等。



不幸的是，当所有这一切都发生时，浏览器会阻止进一步的页面渲染。这是由于“网络的第一个时代”的DOM API允许字符串被附加到解析器正在咀嚼的(chewing through)内容上，如document.write。较新的浏览器将继续在后台扫描或解析文档，并触发下载可能需要的外部内容（js，图像，css等），但渲染仍然被阻止。



这就是为什么表现世界的伟大和好处建议将脚本元素放在文档的最后，因为它尽可能地阻止了内容。不幸的是，这意味着你的脚本不会被浏览器看到，直到它下载你的所有的HTML，并开始下载其他内容，如CSS，图像和iframes。现代浏览器足够聪明，可以优先考虑JavaScript而不是图像，但我们可以做得更好。



感谢IE！ （不，我不是讽刺）

```

<script src="//other-domain.com/1.js" defer></script>

<script src="2.js" defer></script>

```



微软认识到这些性能问题，并推出了“defer”到Internet Explorer 4.这基本上说：“我保证不使用document.write注入东西到解析器。如果我违反了这个承诺，你可以自由地以任何你认为合适的方式来惩罚我。该属性将其转换为HTML4并出现在其他浏览器中。



在上面的示例中，浏览器将并行地下载这两个脚本，并在DOMContentLoaded触发之前执行它们，维护它们的顺序。



像羊厂里的集束炸弹一样，“defer”变成了一团乱麻。在“src”与“defer”属性之间，脚本标签与动态添加的脚本之间，我们有6种添加脚本的模式。当然，浏览器并不同意他们应该执行的命令。在这个问题上，Mozilla在2009年写了一篇很棒的文章。



WHATWG使行为明确，声明“defer”对动态添加的脚本或缺少“src”的脚本没有影响。否则，defer的脚本应该在文档解析后按照添加的顺序运行。



感谢IE！ （好吧，现在我正在讽刺）

它给予，它取消。不幸的是，在IE4-9中有一个令人讨厌的错误，可能会导致脚本以意外的顺序执行。以下是发生的事情：



```

//1.js

console.log('1');

document.getElementsByTagName('p')[0].innerHTML = 'Changing some content';

console.log('2');

//2.js

console.log('3');

```

假设页面上有段落，日志的预期顺序是[1,2,3]，但是在IE9和下面你会得到[1,3,2]。 特定的DOM操作会导致IE暂停当前的脚本执行并执行其他未决的脚本，然后再继续。



但是，即使在IE10和其他浏览器这样没有任何问题的实现中，脚本执行也会延迟，直到整个文档已经下载并解析。 如果您要等待DOMContentLoaded，这可能会很方便，但是如果您希望在性能方面非常积极，则可以开始添加侦听器并尽快引导。



HTML5 to the rescue

```

<script src="//other-domain.com/1.js" async></script>

<script src="2.js" async></script>

```

HTML5给了我们一个新的属性，"async"，假设你不打算使用document.write，但不会等到文档解析执行。浏览器将同时下载这两个脚本，并尽快执行它们。



不幸的是，因为他们要尽快执行，所以“2.js”可以在“1.js”之前执行。这很好，如果他们是独立的，也许“1.js”是一个跟“2.js”无关的跟踪脚本。但是，如果你的“1.js”是“2.js”所依赖的jQuery的CDN副本，那么你的页面将会被涂上错误，就像集群炸弹一样......我不知道...我什么也没有这个。



我知道我们需要什么，一个JavaScript库！

圣杯有一套脚本立即下载，不会阻止渲染，并按照添加的顺序尽快执行。不幸的是HTML不喜欢你，不会让你这样做。



这个问题是由JavaScript在几个口味解决。有些需要对JavaScript进行更改，并将其封装在库调用正确顺序的回调（例如RequireJS）中。其他人会使用XHR并行下载，然后eval（）以正确的顺序下载，除非他们有一个CORS头并且浏览器支持，否则它不适用于另一个域上的脚本。有些甚至使用超级魔术黑客，如LabJS。



黑客涉及欺骗浏览器下载资源的方式，在完成时触发事件，但避免执行它。在LabJS中，脚本会添加一个不正确的MIME类型，例如<script type =“script / cache”src =“...”>。一旦所有的脚本都下载完毕，他们会再次被添加一个正确的类型，希望浏览器能够直接从缓存中获取它们，并且立即执行它们。这取决于方便但未指定的行为，并在HTML5声明的浏览器不应下载具有无法识别的类型的脚本时破坏。值得注意的是LabJS适应了这些变化，现在使用本文中的方法的组合。



然而，脚本加载器有自己的性能问题，您必须等待库的JavaScript下载并解析，然后才能开始下载它管理的任何脚本。另外，我们将如何加载脚本加载器？我们如何加载脚本来告诉脚本加载器要加载什么？谁在守着守望者？我为什么裸体？这些都是难题。



基本上，如果您在下载其他脚本之前必须下载额外的脚本文件，那么您已经失去了性能上的战斗。



DOM来拯救

答案实际上是在HTML5规范中，尽管它隐藏在脚本加载部分的底部。



异步IDL属性控制元素是否将异步执行。如果元素的“force-async”标志被设置，那么在获取时，异步IDL属性必须返回true，并且在设置时，“force-async”标志必须首先被取消设置。

我们把它翻译成“Earthling”：

```



[

 '//http://other-domain.com/1.js',

 '2.js'

].forEach(function(src) {

 var script = document.createElement('script');

 script.src = src;

 document.head.appendChild(script);

});

```



动态创建并添加到文档中的脚本默认是异步的，它们不会在下载时立即阻止渲染并执行，这意味着它们可能以错误的顺序出现。 但是，我们可以明确地标记它们不是异步的：



```

[

 '//http://other-domain.com/1.js',

 '2.js'

].forEach(function(src) {

 var script = document.createElement('script');

 script.src = src;

 script.async = false;

 document.head.appendChild(script);

});

```



这给我们的脚本混合了纯HTML无法实现的行为。通过显式不是异步，脚本被添加到执行队列中，这是我们第一个纯HTML示例中添加的相同队列。但是，通过动态创建，它们在文档解析之外执行，因此在下载时不会阻止呈现（不要将非异步脚本加载与同步XHR混淆，这绝不是一件好事）。



上面的脚本应该被内联地包含在页面的头部，尽可能地排队脚本下载，而不会中断渐进渲染，并按照指定的顺序尽快执行。 “2.js”可以在“1.js”之前自由下载，但直到“1.js”成功下载执行或者不能执行，才会执行。Hurrah！异步下载，但有序执行！



所有支持async属性的脚本都支持以这种方式加载脚本，Safari 5.0除外（5.1很好）。另外，Firefox和Opera的所有版本都受支持，因为不支持async属性的版本可以方便地按照添加到文档中的顺序执行动态添加的脚本。



这是加载脚本的最快方法吗？对？

那么，如果你正在动态地决定加载哪些脚本，是的，否则，可能不是。通过上面的例子，浏览器必须解析并执行脚本来发现要下载的脚本。这会从预加载扫描程序中隐藏您的脚本。浏览器使用这些扫描程序发现下一个可能访问的页面上的资源，或者在分析程序被另一个资源阻止时发现页面资源。



我们可以通过把这个放在文件的头部来添加可发现性：

```



<link rel="subresource" href="//other-domain.com/1.js">

<link rel="subresource" href="2.js">

```



这告诉浏览器页面需要1.js和2.js.链接[rel = subresource]类似于链接[rel = prefetch]，但具有不同的语义。不幸的是，目前只有Chrome支持，你必须声明哪些脚本加载两次，一次通过链接元素，并再次在脚本中。



更正：我原来声明这些是由预加载扫描程序拾取的，他们不是，它们是由常规解析器拾取的。但是，预加载扫描程序可以选择这些，但它还没有，而由可执行代码包含的脚本永远不能被预加载。感谢Yoav Weiss在评论中纠正了我。



我觉得这篇文章令人沮丧。

情况令人沮丧，你应该感到沮丧。在控制执行顺序的同时，没有非重复性的声明性方式来快速和异步地下载脚本。



使用HTTP2 / SPDY，您可以将请求开销减少到以多个小型可单独缓存的文件提供脚本成为最快的方式。想像：



```

<script src="dependencies.js"></script>

<script src="enhancement-1.js"></script>

<script src="enhancement-2.js"></script>

<script src="enhancement-3.js"></script>

<script src="enhancement-10.js"></script>

```



每个增强脚本处理特定的页面组件，但需要dependencies.js中的实用程序功能。理想情况下，我们要异步下载，然后以任何顺序尽快执行增强脚本，但在dependencies.js之后。这是渐进式的渐进式增强！



不幸的是，除非将脚本本身修改为跟踪dependencies.js的加载状态，否则没有声明方法来实现此目的。即使async = false也不能解决这个问题，因为enhancement-10.js的执行将在1-9上阻塞。事实上，只有一个浏览器可以在没有黑客的情况下实现这一点。



IE有一个想法！

IE加载脚本与其他浏览器不同。

```

var script = document.createElement（'script'）;

script.src ='whatever.js';

```

IE现在开始下载“whatever.js”，其他浏览器不会开始下载，直到脚本添加到文档中。 IE也有一个事件，“readystatechange”和属性，“readystate”，告诉我们加载的进度。这实际上非常有用，因为它可以让我们独立地控制脚本的加载和执行。



```

var script = document.createElement('script');



script.onreadystatechange = function() {

 if (script.readyState == 'loaded') {

   // Our script has download, but hasn't executed.

   // It won't execute until we do:

   document.body.appendChild(script);

 }

};



script.src = 'whatever.js';



```



我们可以通过选择何时将脚本添加到文档中来构建复杂的依赖模型。 IE从第6版开始就支持这种模式。虽然很有趣，但它仍然存在着与async = false相同的预加载器可发现性问题。



足够！ 我应该如何加载脚本？

好的好的。 如果你想以不妨碍渲染的方式加载脚本，不需要重复，并且支持优秀的浏览器，下面是我的建议：



```

<script src="//other-domain.com/1.js"></script>

<script src="2.js"></script>

```



那。 在身体元素的末尾。 是的，作为网页开发人员，就像是西西弗国王（繁荣！希腊神话中的100个时髦点）。 HTML和浏览器的限制使我们无法做得更好。



我希望JavaScript模块能够通过提供一种声明式的非阻塞方式来加载脚本并控制执行顺序来节省我们，尽管这需要将脚本编写为模块。



呃，我们现在可以用更好的东西吗？

对于奖励积分来说，如果你想要表现出真正的进取心，并且不介意一点点的复杂性和重复性，那么你可以把上面的一些技巧结合起来。



首先，我们为预加载器添加子资源声明：



```

<link rel =“subresource”href =“// other-domain.com/1.js”>

<link rel =“subresource”href =“2.js”>

```



然后，在文档的头部内联，我们用JavaScript加载我们的脚本，使用async = false，回退到IE的基于readystate的脚本加载，回落到延迟。

```

var scripts = [

 '1.js',

 '2.js'

];

var src;

var script;

var pendingScripts = [];

var firstScript = document.scripts[0];



// Watch scripts load in IE

function stateChange() {

 // Execute as many scripts in order as we can

 var pendingScript;

 while (pendingScripts[0] && pendingScripts[0].readyState == 'loaded') {

   pendingScript = pendingScripts.shift();

   // avoid future loading events from this script (eg, if src changes)

   pendingScript.onreadystatechange = null;

   // can't just appendChild, old IE bug if element isn't closed

   firstScript.parentNode.insertBefore(pendingScript, firstScript);

 }

}



// loop through our script urls

while (src = scripts.shift()) {

 if ('async' in firstScript) { // modern browsers

   script = document.createElement('script');

   script.async = false;

   script.src = src;

   document.head.appendChild(script);

 }

 else if (firstScript.readyState) { // IE<10

   // create a script and add it to our todo pile

   script = document.createElement('script');

   pendingScripts.push(script);

   // listen for state changes

   script.onreadystatechange = stateChange;

   // must set src AFTER adding onreadystatechange listener

   // else we’ll miss the loaded event for cached scripts

   script.src = src;

 }

 else { // fall back to defer

   document.write('<script src="' + src + '" defer></'+'script>');

 }

}

```



稍后的几个技巧和缩小，它是362个字节+您的脚本URL：

```

!function(e,t,r){function n(){for(;d[0]&&"loaded"==d[0][f];)c=d.shift(),c[o]=!i.parentNode.insertBefore(c,i)}for(var s,a,c,d=[],i=e.scripts[0],o="onreadystatechange",f="readyState";s=r.shift();)a=e.createElement(t),"async"in i?(a.async=!1,e.head.appendChild(a)):i[f]?(d.push(a),a[o]=n):e.write("<"+t+' src="'+s+'" defer></'+t+">"),a.src=s}(document,"script",[

 "//http://other-domain.com/1.js",

 "2.js"

])

```

与简单的脚本包含在一起的额外字节值得吗？如果您已经使用JavaScript来有条件地加载脚本，就像BBC一样，您也可以从早期触发这些下载中受益。否则，也许不会，坚持简单的结束方法。



唷，现在我知道为什么WHATWG脚本加载部分是如此之大。我需要一杯饮料。



快速参考

简单的脚本元素

```

<script src =“// other-domain.com/1.js”> </ script>

<script src =“2.js”> </ script>

```

规格说：一起下载，在任何未决的CSS，块渲染，直到完成后，按顺序执行。



浏览者说：是的，先生！



推迟

```

<script src =“// other-domain.com/1.js”defer> </ script>

<script src =“2.js”defer> </ script>

```

Spec说：一起下载，在DOMContentLoaded之前执行。忽略没有“src”的脚本的“延迟”。



IE <10说：我可能在1.js的执行过程中执行2.js。这不是很有趣吗？



红色的浏览器说：我不知道这个“延迟”是什么，我要加载脚本，就好像它不在那里一样。



其他浏览器说：好的，但我可能不会忽略没有“src”的脚本“推迟”。



异步

```

<script src =“// other-domain.com/1.js”async> </ script>

<script src =“2.js”async> </ script>

```

Spec说：一起下载，按照他们下载的顺序执行。



红色的浏览器说：什么是“异步”？我将加载脚本，就好像它不在那里一样。



其他浏览器说：是的，好的。



异步错误

```

[

 '1.js'，

 '2.js'

] .forEach（function（src）{

 var script = document.createElement（'script'）;

 script.src = src;

 script.async = false;

 document.head.appendChild（脚本）;

}）;

```

Spec说：一起下载，尽快下载。

Firefox <3.6，Opera说：我不知道这个“异步”是什么，但是我只是按照添加的顺序执行通过JS添加的脚本。

Safari 5.0说：我明白“异步”，但不明白与JS设置为“假”。我会马上执行你的脚本，无论顺序如何。

IE <10表示：不知道“异步”，但有一个解决方法使用“onreadystatechange”。

其他红色浏览器说：我不明白这个“异步”的东西，我会尽快执行你的脚本，以任何顺序。
其他的都说：我是你的朋友，我们要按照这本书去做。