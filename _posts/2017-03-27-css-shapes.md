---
date: 2017-03-27
layout: post
title: CSS Shapes
thread: 9
categories: Translate
tags: [BIT]
excerpt: CSS Shapes
---
很长一段时间，网页设计师被迫在矩形的约束范围内创建。 网络上的大多数内容仍然被困在简单的盒子里，因为大多数创意冒险进入非矩形布局，结果令人沮丧。 即将从Chrome 37开始引入的CSS Shapes即将改变。



CSS形状允许网页设计师围绕自定义路径（如圆形，椭圆形和多边形）进行内容包装，从而摆脱矩形的限制。



形状可以手动定义，也可以从图像中推断出来。



我们来看一个非常简单的例子。



也许你和我一样幼稚，首先用透明的部分浮动图像，期望内容包裹并填补空白，但只是为了保持元素周围的矩形包裹形状而失望。 CSS形状可以用来解决这个问题。



```

<img class=”element” src=”image.png” />

<p>Lorem ipsum…</p> 

<style> 

.element{ 

   shape-outside: url(image.png); 

   shape-image-threshold: 0.5; float: left; 

} 

</style>

```

shape-outside：url（image.png）CSS声明告诉浏览器从图像中提取一个形状。

shape-image-threshold属性定义将用于创建形状的像素的最小不透明度级别。它的值必须在0.0（完全透明）和1.0（完全不透明）之间。因此，shape-image-threshold：0.5意味着只有不透明度等于或大于50％的像素才会用于创建形状。



float属性在这里是关键的。虽然shape-outside属性定义了内容将围绕的区域的形状，但不包含浮动，您将看不到形状的效果。



元素在浮动值的另一侧有一个浮动区域。例如，如果具有咖啡杯图像的元素正在向左浮动，浮动区域将被创建在杯子的右侧。即使您可以设计两侧有空白的图像，内容也只会围绕浮动属性指定的对象的形状左右两侧，而不是两者。



将来，可以使用shape-outside元素，这些元素不会与前面介绍的CSS排除项目一起浮动。



## 手动Creating shapes



除了从图像中提取形状之外，您还可以手动对其进行编码。 您可以从几个函数值中进行选择以创建形状：circle（），ellipse（），inset（）和polygon（）。 每个形状函数接受一组坐标，并与建立坐标系的参考框配对。 有关参考框的更多信息。

circle（）函数



圆形形状值的完整符号是圆形（r在cx cy），其中r是圆的半径，而cx和cy是X轴和Y轴上圆心的坐标。 圆心的坐标是可选的。 如果省略它们，则元素的中心（对角线的交点）将被用作默认值。



```

.element{

shape-outside: circle(50%);

width: 300px;

height: 300px; float: left; }

```

在上面的例子中，内容将环绕圆形路径的外部。 单个参数50％指定了圆的半径，在这种情况下，这个半径等于元素宽度或高度的一半。 更改元素的尺寸将影响圆形的半径。 这是CSS Shapes如何响应的基本示例。

在进一步讨论之前，先快速放一下：重要的是要记住CSS Shapes只影响元素周围的浮点区域的形状。 如果元素具有背景，则不会被形状裁剪。 为了达到这个效果，你必须使用CSS Masking的属性 - clip-path或者mask-image。 clip-path属性非常方便，因为它遵循与CSS Shapes相同的表示法，所以可以重复使用值。



本文档中的插图使用剪切来突出显示形状并帮助您理解效果。



## 回到circle shape.



当使用百分比来表示圆半径时，实际上用一个稍微复杂的公式计算该值：sqrt（width ^ 2 + height ^ 2）/ sqrt（2）。理解这一点很有用，因为它可以帮助你想象一下，如果元素的尺寸不相等，那么结果的圆形将会是什么。



所有的CSS单位类型都可以在形状函数坐标中使用 - px，em，rem，vw，vh等等。你可以选择一个足够灵活或足够满足你的需求的。



您可以通过为其中心的坐标设置明确的值来调整圆的位置。

```

.element{

shape-outside: circle(50% at 0 0);

}

```

这将圆心置于坐标系的原点。什么是坐标系？这是我们介绍参考框的地方。

CSS形状的参考框



参考框是元素周围的虚拟框，它建立了用于绘制和定位形状的坐标系。坐标系的原点位于左上角，X轴指向右侧，Y轴指向下。



请记住，shape-outside会更改内容将围绕的浮动区域的形状。浮动区域延伸到由边距属性定义的框的外边缘。这被称为边界框，如果没有明确提到，它就是形状的默认参考框。



## 下述两个CSS declarations 异曲同工:

```

.element{

shape-outside: circle(50% at 0 0); /* identical to: */

shape-outside: circle(50% at 0 0) margin-box; 

}

```



我们尚未给element添加margin。在此点假设坐标系原点和圆心都在元素内容区域的左上角。当你设置margin时这点会变化：

```

.element{

shape-outside: circle(50% at 0 0) margin-box;

margin: 100px; 

}

```

坐标系统的原点现在位于元素的内容区域之外（向上100px，向左100px），圆心也是如此。 圆弧半径的计算值也增加，以考虑由边距框参考框建立的坐标系的增加的表面。



有几个参考框可供选择：边框，边框，填充框和内容框。 他们的名字暗示了他们的界限。 我们之前解释了保证金框。 边框由元素边框的外边限制，填充框由元素的填充约束，而内容框与元素中内容使用的实际表面区域相同。



只有一个参考框可以在给定的时间用于形状外声明。 每个参考框都会以不同的方式影响形状。 还有另一篇文章深入研究，并帮助您理解CSS形状的参考框。

### The ellipse() function



椭圆看起来像压扁的circle。 它们被定义为椭圆（rx ry在cx cy），其中rx和ry是椭圆在X轴和Y轴上的半径，而cx和cy是椭圆中心的坐标。

```

.element{

shape-outside: ellipse(150px 300px at 50% 50%);

width: 300px;

height: 600px; 

}

```

百分比值将从坐标系统的维度计算。 这里不需要有趣的数学。 您可以省略椭圆中心的坐标，它们将从坐标系的中心推断出来。

X轴和Y轴上的半径也可以用关键字定义：最远侧的半径等于椭圆中心与参考框最远的一侧之间的距离，而最靠近的一侧意思是相反 - 使用 中心和一边之间的最短距离。



```

.element{

shape-outside: ellipse(closest-side farthest-side at 50% 50%); /* identical to: */

shape-outside: ellipse(150px 300px at 50% 50%);

width: 300px;

height: 600px;

}

```

当元素的尺寸（或参考框）可能以不可预知的方式变化时，这可能会派上用场，但是您希望椭圆形状适应。

circle() shape function中的半径也可以使用相同的最远侧和最近侧的关键字。



### The polygon() function



If circles and ellipses are too limiting, the polygon shape function opens up a world of options. The format is polygon(x1 y1, x2 y2, ...) where you specify pairs of x y coordinates for each vertex (point) of a polygon. The minimum number of pairs to specify a polygon is three, a triangle.

```

.element{

shape-outside: polygon(0 0, 0 300px, 300px 600px);

width: 300px;

height: 600px; 

}

```



顶点放在坐标系上。 对于响应式多边形，可以使用某些或全部坐标的百分比值。



```

.element{ /* polygon responsive to font-size*/

shape-outside: polygon(0 0, 0 100%, 100% 100%);

width: 20em;

height: 40em;

}

```



有一个从SVG导入的可选填充规则参数，它指示浏览器在自相交路径或封闭形状的情况下如何考虑多边形的“内部”。 Joni Trythall很好地解释了填充规则属性在SVG中的工作原理。 如果未定义，则填充规则默认为非零。

```

.element{

shape-outside: polygon(0 0, 0 100%, 100% 100%); /* identical to: */

shape-outside: polygon(nonzero, 0 0, 0 100%, 100% 100%);

}

```



### The inset() function

The inset() shape function 允许您创建包装内容的矩形形状。 这可能听起来违反直觉，考虑到CSS从简单的盒子形状免费网页内容的初始前提。 这很可能是。 我还没有找到一个用于inset（）的用例，这个用例还没有用浮点数和边距来实现，也没有用polygon（）来实现。 尽管inset（）确实为矩形提供了比polygon（）更可读的表达式。

插入形状函数的完整符号是插入（右下角左上角半径）。 前四个位置参数是从元素的边缘向内偏移的。 最后一个参数是矩形形状的边界半径。 这是可选的，所以你可以离开它。 它遵循你已经在CSS中使用的边界半径速记符号。



```

.element{

shape-outside: inset(100px 100px 100px 100px); /* yields a rectangular shape which is 100px inset on all sides */ float: left; 

}

```



## Creating shapes from reference-boxes



如果不为shape-outside属性指定形状函数，则可以允许浏览器从元素的引用框中派生形状。 默认参考框是边距框。 到目前为止，没有任何异国情调，这就是花车已经工作。 但是应用这种技术，你可以重用元素的几何。 我们来看一下border-radius属性。

如果使用它来旋转浮动元素的拐角，则会得到削波效果，但浮动区域仍然是矩形。 添加shape-outside：border-box以环绕由border-radius创建的轮廓。

```

.element{

border-radius: 50%;

shape-outside: border-box; float: left; 

}

```

当然，你可以用这种方式使用所有的参考框。 这是派生形状的另一种用法 - 偏移拉式引号。



可以通过仅使用浮动和保证金属性来实现抵消拉式报价效果。 但是，这需要您将引用元素放置在HTML树中您想要呈现的位置。



以下是如何在增加灵活性的情况下实现相同的抵消拉式报价效果

```

.pull-quote{

shape-outside: content-box;

margin-top: 200px; float: left; 

}

```

我们明确地设置了形状坐标系的内容框参考框。 在这种情况下，拉式引用中的内容量定义了外部内容将围绕其包裹的形状。 无论在HTML树中的位置如何，margin-top属性都用于定位（偏移）拉式引用。

形状边缘



你会注意到，在一个形状周围包裹内容可以使它与元素过于紧密地摩擦。 您可以使用形状边缘属性在形状周围添加间距。

```

.element{

shape-outside: circle(40%);

shape-margin: 1em; float: left; 

}

```

效果与使用常规边距属性时的效果类似，但形状边距仅影响形状外部值周围的空间。 只有在坐标系中有空间的情况下，它才会在形状周围增加间距。 这就是为什么在上面的例子中，圆的半径被设置为40％，而不是50％。 如果半径设置为50％，则该圆将占据坐标系中的所有空间，而不留下形状边缘的影响。 请记住，形状最终受限于元素的边距框（元素加上其周围边距）。 如果形状较大并且溢出，则会将其剪裁到边距框，最终形成一个矩形。

理解形状边缘只接受一个正值是很重要的。 它没有一个长期的符号。 无论如何，什么是一个circle的形状边缘顶部？



## Animating shapes



您可以将CSS形状与许多其他CSS功能（如转场和动画）混合使用。 尽管如此，我必须强调，当阅读文本时，用户会发现文本布局的变化非常烦人。 如果您决定支持动画形状，请密切关注体验。

只要在浏览器可以插入的值中定义半径和中心，就可以为circle（）和ellipse（）形状创建动画。 从circle（30％）到circle（50％）是可能的。 但是，圆圈（最接近边）与圆圈（最远边）之间的动画会阻塞浏览器。

```

.element{

   shape-outside: circle(30%);

   transition: shape-outside 1s; float: left; 

} 

.element:hover{

   shape-outside: circle(50%); 

}

```



polygon()变形时，可以实现更有趣的效果，注意多边形在两个动画状态之间必须具有相同数量的顶点。 如果添加或删除顶点，则浏览器无法进行插值。



一个诀窍是添加您需要的最大数量的顶点，并将它们聚集在动画状态中，在这种状态下，您想要的形状的边缘更少。

```

.element{ 

   /* four vertices (looks like rectangle) */

   shape-outside: polygon(0 0, 100% 0, 100% 100%, 0 100%);

   transition: shape-outside 1s; 

} 

.element:hover{ 

   /* four vertices, but second and third overlap (looks like triangle) */shape-outside: polygon(0 0, 100% 50%, 100% 50%, 0 100%); 

}

```



## Wrapping content inside a shape



CSS Shapes规范的初稿包括一个shape-inside属性，它允许你将内容包装到一个形状中。 Chrome和Webkit甚至有一段时间的实现。 但是在自定义路径中包装任意定位的内容需要更多的努力和研究来覆盖所有可能的场景并避免错误。 这就是为什么形状内部属性已被推迟到CSS形状2级和实现已被撤回。



但是，通过一些努力和一些妥协，您仍然可以实现在自定义形状内包装内容的效果。 黑客是使用两个形状外浮动元素，位于一个容器的两侧。 妥协是你必须使用一个或两个没有语义意义的空元素，而是作为支撑来创造内部形状的幻觉。

```

<div> <div class="left-shape"></div> <div class="right-shape"></div>



Lorem ipsum...

</div>

```

The position of the .left-shape and .right-shape strut elements at the top of the container is important because they will be floated to the left and to right in order to flank the content.

```

.left-shape{

shape-outside: polygon(0 0, ...); float: left;

width: 50%;

height: 100%; } .right-shape{

shape-outside: polygon(50% 0, ...); float: right;

width: 50%;

height: 100%; 

}

```

这种造型使得两个浮动的支柱占据了元件内的所有空间，但是形状外部的特性为其余内容创造了空间。



如果浏览器不支持CSS形状，通过推送所有的内容会产生丑陋的效果。这就是为什么以逐步增强的方式使用该功能非常重要的原因。



在之前的形状动画示例中，您会注意到文字转换可能很麻烦。并不是所有的用例都需要动画形状。但是，您可以为与CSS形状交互的其他属性创建动画，以便在有意义的位置添加效果。



在“CSS形状的爱丽丝梦游仙境”演示中，我们使用滚动位置来更改内容的上边距。文本被挤压在两个浮动的元素之间。当它向下移动时，它必须根据两个浮动元素外部的形状重新布局。这给人的印象是文本正在走下去的兔子洞，它增加了故事的经验。边界无偿？也许。但它看起来很酷。



由于文本布局是由浏览器本地完成的，因此性能要优于使用JavaScript解决方案。但是改变scroll-margin就会触发大量重新布局和绘制事件，这可能会明显降低性能。谨慎使用！但是，使用CSS Shapes而不使用动画效果并不会带来可感知的性能。



## 逐步增强



首先假定浏览器没有CSS Shapes支持，并在检测到该功能时建立起来。 Modernizr是做功能检测的一个很好的解决方案，并且在“非核心检测”部分有一个CSS形状的测试。

某些浏览器通过@supports规则在CSS中提供功能检测，而不需要外部库。 Google Chrome也支持CSS Shapes，它理解@supports规则。这是你如何使用它逐步增强：



.element {/ *所有浏览器的样式* /} @supports（shape-outside：circle（50％））{/ *仅用于支持CSS Shapes的浏览器的样式* / .element {

形状外：圈（50％）; }}



Lea Verou撰写了更多关于如何使用CSS @supports规则的文章。

从CSS排除消除歧义



我们今天知道的CSS样式在规范的早期被称为CSS排除和形状。命名上的转变可能看起来很微妙，但实际上非常重要。 CSS排除，现在是一个单独的规范，使任意位置的元素包装内容，而不需要一个浮动属性。想象一下，将内容包装在绝对定位的元素周围;这是CSS排除的用例。 CSS形状只是定义内容将包裹的路径。

所以，形状和排除不是一回事，但它们是相辅相成的。 CSS形状现在在浏览器中可用，而CSS排除尚未与形状交互实现。

##Tools for working with CSS Shapes



Y您可以在经典的图像创作工具中创建路径，但在撰写本文时，它们都不会导出CSS Shapes值所需的语法。即使他们这样做，这样工作也不会太实际。

CSS形状是为了在浏览器中使用，他们在页面上的其他元素作出反应。可视化编辑形状对围绕它的内容的影响是非常有用的。有几个工具可以帮助你完成这个工作流程：



括号：Brackets的CSS Shapes Editor扩展使用代码编辑器的实时预览模式叠加交互式编辑器来编辑形状值。



谷歌浏览器：谷歌浏览器的CSS形状编辑器扩展扩展浏览器的开发工具与控制来创建和编辑形状。它将一个交互式编辑器放置在所选元素的顶部。



Google Chrome浏览器内置的支持突出显示形状。将鼠标悬停在具有形状外属性的元素上，将会亮起以说明形状。



图像形状：如果您更喜欢生成图像并使浏览器从中提取图像，Rebecca Hauck已经为Photoshop编写了一个很好的教程。



填充：谷歌浏览器是第一个发布CSS形状的主要浏览器。即将到来的对苹果iOS 8和Safari 8的功能的支持。其他浏览器供应商可能会考虑在未来。在那之前，有一个CSS Shapes polyfill来提供基本的支持。



## 结论



在一个网页中，内容大多被困在简单的框中，CSS Shapes提供了一种创建富有表现力的布局的方式，弥补了网页和印刷设计之间的保真度差距。当然，形状可以被滥用，造成分心。但是，当应用了味道和良好的判断力时，形状可以增强内容呈现，并以一种独特的方式集中用户的注意力。

我给你留下了一些其他人的作品集，大部分来自印刷品，这些作品展示了非平面布局的有趣用途。我希望这启发你尝试CSS形状和尝试新的设计思路。