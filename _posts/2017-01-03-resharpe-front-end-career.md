---
date: 2017-01-03
layout: post
title: Becoming a Great Web Front-end Developer
thread: 9
categories: Career
tags: [BIT]
excerpt: Becoming a Great Web Front-end Developer
---
# <center>成就一名卓越的前端开发者

> 本文记录了两位工程师为web开发者们所提出的多条建议，其中一位推荐了多种实用的工具与技术，而另一位则对于如何克服浏览器开发时所面临的挑战提出了诸多建议。
Rebecca Murphey是来自于Bazaarvoice的一位软件工程师。今年早些时候，她发布了一篇博客文章[“前端（JS）开发者的基本素质之2015版”](http://rmurphey.com/blog/2015/03/23/a-baseline-for-front-end-developers-2015)，为JavaScript开发者在进行客户端web开发时使用的工具与开发方式提出了一些建议。她在文章的总结中写道：

### 1. 学习ECMAScript 2015

推荐的参考资料有：[《Understanding ES6》](https://www.infoq.com/news/2015/06/ecmascript-2015-es6)、ES6 Rocks以及BabelJS。我们在此还要加上一条，即Axel Rauschmayer的著作[《探索ES6》](http://www.infoq.com/cn/news/2015/07/exploring-es6)。考虑到在当前这个时间点上似乎还没有必要了解ECMAScript 2015的所有细节，Murphey建议开发者更深入地了解如何使用异步调用、回调以及promise。

### 2.使用模块

Murphey相信，模块毫无疑问应当作为客户端web应用程序的构建块。她最近在使用webpack以实现模块化的效果，但她希望让每个人都能够使用ECMAScript标准模块的那一天能够早日到来。

### 3.测试你的代码

在Murphey看来，为你的代码编写测试，并且保证代码的可测试性是至关重要的。
虽然她对于Intern“非常中意”，但出于习惯，她还是坚持使用Mocha。关于这一方面，
她也强烈推荐Michael Feathers的著作[《修改代码的艺术》](http://philipwalton.com/articles/how-to-become-a-great-front-end-engineer/)。

### 4.实现流程自动化

Murphey曾经尝试使用Grunt与Gulp，但她最终还是选择了Yeoman。因为在“使用不熟悉的技术开始一个全新的项目”，或是对第三方JavaScript应用的开发进行标准化时，Yeoman的表现“非常出色”。Murphey也提到了Broccoli，认为它将来或许能够取代Grunt和Yeoman。

### 5.编写高质量的代码

她的建议是，对“违反了项目中经过良好定义的风格指南”的代码进行重构，还应当使用lint工具，例如JSCS或ESLint。

### 6.使用Git

Murphey建议在Git中使用特性分支，因此得以“通过交互式rebase，在与他人分享提交时对提交进行清理，并且尽可能地在较小的单元上进行工作，以减少冲突的发生机率”。此外还应当通过ghooks在push操作与commit操作前运行钩子操作。

### 7.在服务端生成HTML

出于性能方面的考虑，Murphey推荐在大型项目中尽可能在服务端生成HTML。“预先生成这些文件，将其作为静态文件保存，以加快处理请求的速度。随后在客户端的相应事件中可通过客户端代码操作这些HTML文件，并在客户端模板中修改。”

### 8. 拥抱Node

Murphey建议web开发者熟练掌握Node.js的相关知识，至少要了解如何初始化一个Node项目、如何搭建一台Express服务器、以及如何使用request模块转发请求。
Philip Walton是来自Google的一位软件工程师，他最近撰写了一篇博客文章[Working-Effectively-Legacy-Michael-Feathers](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)。这篇文章的观点另辟蹊径，他并没有向读者推荐任何工具或框架，而是专注于如何处理这一领域中的某些挑战。在他看来，优秀员工与真正杰出的人才的差别不在于他们的知识量，而在于他们的思考方式。他是这样描述开发者的智慧的：

### 9.真正理解背后的过程

对于Walton来说，仅仅编写出可以运行的代码算不得优秀。他见过许多编写CSS与JavaScript的人，他们 “只求找到能够运行的代码，然后就继续下一步工作了。”很多时候，开发者并不了解某段代码运行的机制。Walton建议开发者进行深入钻研：

>要充分理解代码的工作原理或许会很耗时间，但我向你保证，从长远来说，这种方式最终将节省你大量的时间。一旦你充分理解你所参与的系统是如何运作的，你就无需不断地进行猜测与检验这些费时的工作了。
预先了解浏览器将产生的改动。Web开发者应当持续了解有哪些浏览器的改动会破坏现有的代码。以下代码在IE10中必然会导致整个JavaScript框架的方法出错：
var isIE6 = !isIE7 && !isIE8 && !isIE9;

### 10仔细阅读规范

Walton指出，虽然阅读规范是一项艰辛的任务，但一旦出现浏览器对某个页面的渲染不同的情况，这一任务就是不可避免的了。他为此特别举例说明：

>最近我遇到这样一个例子，与可伸缩（flex）元素的默认最小尺寸有关。根据规范所说，可伸缩元素的min-width与min-height的初始值是auto，而不是0，这就意味着在默认情况下，这些元素不可能收缩到比其中的内容尺寸还小。而在过去8个月中，Firefox是唯一一个正确地实现了这一特性的浏览器。
如果你遇到了这个跨浏览器的不一致性问题，并且注意到你的网站在Chrome、IE、Opera和Safari上的展现完全相同，只在Firefox上有所差别，那你很可能会认为是Firefox的问题。实际上，我曾多次发现这一情况，在我的Flexbugs项目中，有许多由用户报告的bug其实都是由这种不一致性所导致的。而如果我按照用户所提议的那些临时方案来改变实现方式，那么在两周前所发布的Chrome 44中又会产生问题。由于这些临时方案选择了违背规范的方式，它们在无形中起到了提倡不正确行为的负面作用。

### 11.代码审查

Walton表示，从阅读他人的代码中可以学到很多知识，它可以拓宽你的思路，了解“新的工作方法”，同时也有助于你在团队中的工作。实际上，这一点确实相当必要，因为“作为一位工程师来说，你的时间大部分都是在一个现有的代码库中添加或修改代码”，而不是从头开始编写全新的代码。

### 12.与更聪明的人一起工作

Walton“强烈”建议你至少在职业生涯的初期阶段要尽量在某个团队中进行工作，向更有经验的团队成员学习，并让他们审查你的代码。如果之后选择了自由职业者这条职业路线，那么Walton建议你参与开源项目，这同样可以感受到在团队中工作的益处。

### 13.重复发明轮子

Walton相信，虽然“重复发明轮子对于业务来说是有害的”，但它对于学习很有好处。在有些情况下，他建议你自己编写代码，而不是依赖于第三方的代码，因为这一过程将让你学到许多东西。当然这也要看情况而定。

### 14.将你的经验记录下来

Walton的最后一条建议是将你所学到的东西用文字记录下来：“按我的经验来看，写作、演讲以及开发demo，这些方法能够迫使我对知识点进行充分的挖掘，并做到从内到外的完全理解。哪怕你写的东西完全没人看，但写作的过程本身就已经值得你付出的努力了。”

[参考链接](https://www.infoq.com/news/2015/08/great-front-end-developer)