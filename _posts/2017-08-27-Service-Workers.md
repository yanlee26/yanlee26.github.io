---
date: 2017-08-27
layout: post
title: service worker
thread: 9
categories: Translate
tags: [JS,多线程,高并发,性能,service worker]
excerpt: service worker
---


## 什么是service worker

> service worker是一个浏览器背后运行的脚步，独立于web页面，为无需一个页面或用户交互的功能打开了大门。今日，它包含了推送通知和背景异步（push notifications and background sync）的功能。将来，service worker将支持包括periodic sync or geofencing的功能。 本教程中讨论的核心功能是拦截和处理网络请求的能力，包括以编程方式管理响应缓存。

这是一个令人兴奋的API的原因是它允许您支持离线体验，让开发人员完全控制体验。

此前，还有一个API为用户提供了一个名为AppCache的离线体验。 AppCache API有许多问题需要service worker来避免。

## 关于service worker:

它是一个JavaScript Worker，故不可以直接访问DOM。相反，service worker可以通过响应通过postMessage接口发送的消息来与其控制的页面进行通信，并且如果需要，那些页面可以操纵DOM。service worker是一个可编程的网络代理，允许你控制你处理页面的网络请求的方式

它在不使用时终止（terminated），并在下一次需要时重新启动，所以不能依赖service worker的onfetch和onmessage处理程序中的全局状态。 如果存在需要在重新启动时持续并重复使用的信息，则service worker可以访问IndexedDB API。

terminated广泛使用promise，所以如果你不熟悉promise，那么你应该停止阅读这个promise，并checkout promise介绍。



## service worker的生命周期



service worke的生命周期与您的网页完全分开。要为您的网站安装service worke，您需要注册，并在网页的JavaScript中进行注册。 注册service worke将导致浏览器在后台启动service worke安装步骤。



通常在安装步骤中，您需要缓存一些静态资源。 如果所有文件都缓存成功，则service worke将被安装。 如果任何文件无法下载和缓存，则安装步骤将失败，service worke将不会激活（即不会被安装）。 如果发生这种情况，不要担心，下次再试一次。 但是，这意味着如果它安装，你知道你有这些静态资源在缓存中。



安装后，激活步骤将随之而来，这是处理旧缓存管理的好机会，我们将在service worker更新部分介绍。在激活步骤之后，service worker将控制所有属于其范围的页面，尽管第一次注册service worker的页面将不会被控制，直到再次加载。 



service worker一旦掌控，它将处于以下两种状态之一：service worker将被终止以节省内存，或者处理从网页发出网络请求或消息时发生的提取和消息事件。



以下是第一次安装时service worker生命周期的非常简化版本。



## 先决条件Prerequisites



- Browser支持

Browser 可选性在增加. Service workers 被Chrome, Firefox 和 Opera支持. Microsoft Edge 现在广泛支持. 甚至Safari 已经放弃了未来开发的限制. 您可以在Jake Archibald的ServiceServiceer就绪站点上查看所有浏览器的进度。



- 需要HTTPS



在开发过程中，您可以通过本地主机使用service worker，但要将其部署到您需要在服务器上进行HTTPS设置的站点上。使用service worker，您可以劫持（ hijack）连接，制作和过滤响应。 强大的东西。 虽然你会用这些功能，但中间人（man-in-the-middle）可能不会。 为了避免这种情况，只能在通过HTTPS服务的页面上注册service worker，所以我们知道浏览器收到的service worker在通过网络的过程中没有被篡改（tampered）。



Github页面都采用了HTTPS, 因此这里是放 demos的好地方.



如果你想添加HTTPS到你的服务器，那么你需要获得一个TLS证书并为你的服务器进行设置。 这取决于您的设置，因此请检查您的服务器的文档，并确保检查Mozilla的SSL配置生成器的最佳做法。



## 注册一个service worker



要安装serviceWorker，您需要将其注册到您的页面，以启动该过程。 这告诉浏览器你的serviceWorkerJavaScript文件在哪里.



```

if ('serviceWorker' in navigator) {

   window.addEventListener('load', function() {

   navigator.serviceWorker.register('/sw.js').then(function(registration) { 

       // Registration was successful

   console.log('ServiceWorker registration successful with scope: ', registration.scope); 

   }, 

   function(err) { 

       // registration failed :(

   console.log('ServiceWorker registration failed: ', err); }); 

   }); 

}

```



这些代码检查service worker API 是否可用, 可用则页面加载时就注册.你可以在每次页面加载时注册不必担心什么，浏览器会识别出service worker是否已经注册或者相应处理它。



register（）方法的一个微妙之处是service worker文件的位置。 在这种情况下，您会注意到service worker文件位于域的根目录下。 这意味着serviceworker的范围将是整个起源。 换句话说，这个service worker将会收到这个域上所有东西的提取事件。 如果我们在/example/sw.js注册service worker文件，那么service worker将只能看到URL以/ example /（例如/ example / page1 /，/ example / page2 /）开头的页面的提取事件。



现在，您可以通过访问chrome：// inspect /＃service-workers并查找您的站点来检查是否启用了service worker。







当serviceworker首次实施时，您还可以通过chrome：// serviceworker-internals查看您的serviceworker详细信息。 如果只是了解service worker的生命周期，这可能仍然是有用的，但是如果在以后的日子里被chrome：// inspect /＃service-workers完全替代，不要感到惊讶。



您可能会发现在隐身窗口中测试serviceworker是非常有用的，因为您知道以前的serviceworker不会影响新窗口，因此可以关闭并重新打开。 在隐身窗口内创建的任何注册和缓存将在该窗口关闭后清除。



## 安装一个service worker



在受控页面启动注册过程之后，让我们转到处理安装事件的serviceworker脚本的角度。

对于最基本的示例，您需要为安装事件定义回调并决定要缓存哪些文件。



```

self.addEventListener('install', function(event) { // Perform install steps });

```



在我们的安装回调里面，我们需要采取以下步骤：

打开一个缓存。

缓存我们的文件。

确认是否所有必需的资产都被缓存。



```

var CACHE_NAME = 'my-site-cache-v1';

var urlsToCache = [ '/', '/styles/main.css', '/script/main.js' ]; 

self.addEventListener('install', function(event) { 

   // Perform install steps

    event.waitUntil(

       caches.open(CACHE_NAME).then(function(cache) {

       console.log('Opened cache'); 

       return cache.addAll(urlsToCache); 

   })); 

});

```

在这里你可以看到我们调用了caches.open（）和我们想要的缓存名称，之后我们调用cache.addAll（）并传入我们的文件数组。 这是一个承诺链（caches.open（）和cache.addAll（））。 event.waitUntil（）方法接受一个承诺，并使用它来知道安装需要多长时间，以及它是否成功。



如果所有文件都被成功缓存，那么serviceworker将被安装。 如果有任何文件无法下载，则安装步骤将失败。 这使您可以依靠拥有所有您定义的资产，但意味着您需要小心在安装步骤中决定要缓存的文件列表。 定义一长串文件将增加一个文件可能无法缓存的机会，导致您的serviceworker没有得到安装。



这只是一个例子，您可以在安装事件中执行其他任务，或避免完全设置安装事件侦听器。



## Cache 和return requests



现在你已经安装了一个serviceworker，你可能想要返回一个缓存的响应，对吧？

在安装了serviceworker并且用户导航到不同页面或刷新之后，serviceworker将开始接收提取事件，下面是一个例子。

```

self.addEventListener('fetch', function(event) { event.respondWith(

caches.match(event.request) .then(function(response) { // Cache hit - return response if (response) { return response; } return fetch(event.request); } ) ); });

```

在这里我们定义了fetch事件，在event.respondWith（）中，我们传递了一个来自caches.match（）的承诺。 此方法查看请求，并查找来自serviceworker创建的任何缓存的任何缓存结果。

如果我们有一个匹配的响应，我们返回缓存的值，否则我们返回一个调用的结果获取，这将作出网络请求，并返回数据，如果任何东西可以从网络中检索。 这是一个简单的例子，它使用我们在安装步骤中缓存的所有缓存资产。



如果我们想累积缓存新的请求，我们可以通过处理获取请求的响应，然后将其添加到缓存中，如下所示。

```

self.addEventListener('fetch', function(event) { event.respondWith(

caches.match(event.request) .then(function(response) { // Cache hit - return response if (response) { return response; } // IMPORTANT: Clone the request. A request is a stream and // can only be consumed once. Since we are consuming this // once by cache and once by the browser for fetch, we need // to clone the response. var fetchRequest = event.request.clone(); return fetch(fetchRequest).then( function(response) { // Check if we received a valid response if(!response || response.status !== 200 || response.type !== 'basic') { return response; } // IMPORTANT: Clone the response. A response is a stream // and because we want the browser to consume the response // as well as the cache consuming the response, we need // to clone it so we have two streams. var responseToCache = response.clone();



caches.open(CACHE_NAME) .then(function(cache) {

cache.put(event.request, responseToCache); }); return response; } ); }) ); });

```



我们正在做的是这样的：

向获取请求上的.then（）添加回调。

一旦我们得到回应，我们执行以下检查：

确保回复有效。

检查响应状态是200。

确保响应类型是基本的，这表明这是来自我们原点的请求。这意味着对第三方资产的请求也不会被缓存。





如果我们通过检查，我们克隆响应。这是因为响应是Stream，所以只能消耗一次。既然我们要返回浏览器使用的响应，并将其传递给缓存使用，我们需要克隆它，以便我们可以发送一个到浏览器，一个发送到缓存。

更新serviceworker



将有一个时间点，您的serviceworker将需要更新。到时候，您需要按照以下步骤操作：

更新您的serviceworkerJavaScript文件。当用户浏览到您的网站时，浏览器会尝试重新下载在后台定义serviceworker的脚本文件。如果在serviceworker文件中甚至有一个字节与当前的字节不同，则认为它是新的。

您的新serviceworker将启动并安装事件将被解雇。

在这一点上，旧的serviceworker仍在控制当前的页面，所以新的serviceworker将进入等待状态。

当您的网站当前打开的页面关闭时，旧的serviceworker将被终止，新的serviceworker将控制。

一旦你的新serviceworker接管了控制权，它的激活事件将被激发。

在激活回调中发生的一个常见任务是缓存管理。你要在激活回调中这样做的原因是，如果你要在安装步骤中清除所有旧的缓存，任何保留所有当前页面的旧serviceworker将会突然停止服务来自该缓存的文件。



比方说，我们有一个叫做“my-site-cache-v1”的缓存，我们发现我们想把它分成一个缓存页面和一个缓存博客文章。这意味着在安装步骤中，我们将创建两个缓存“pages-cache-v1”和“blog-posts-cache-v1”，在激活步骤中，我们希望删除旧的“my-site-cache- V1' 。



下面的代码将通过循环遍历serviceworker中的所有缓存并删除高速缓存白名单中未定义的任何缓存来完成此操作。

```

self.addEventListener('activate', function(event) { var cacheWhitelist = ['pages-cache-v1', 'blog-posts-cache-v1']; event.waitUntil(

caches.keys().then(function(cacheNames) { return Promise.all(

cacheNames.map(function(cacheName) { if (cacheWhitelist.indexOf(cacheName) === -1) { return caches.delete(cacheName); } }) ); }) ); });

```



> Rough edges and gotchas陷阱



这东西真的很新鲜 这里有一些问题的集合。 希望这部分可以尽快删除，但现在这些值得留意。

如果安装失败，我们并不擅长告诉你



如果一个工作人员注册，但没有出现在chrome：// inspect /＃service-workers或chrome：// serviceworker-internals中，则可能由于抛出错误而无法安装，或被拒绝的promise被传递给事件 。等到（）。

要解决此问题，请转至chrome：// serviceworker-internals并选中“打开DevTools窗口并暂停serviceworker启动时的JavaScript执行以进行调试”，并在安装事件开始时输入调试器语句。 这与未捕获的异常一起暂停，应该揭示这个问题。



## defaults of fetch()



没有默认的凭据

当您使用提取时，默认情况下，请求不会包含cookie等凭据。 如果您需要凭据，请调用：



```

fetch(url, {

credentials: 'include' 

})

```

这种行为是有目的的，如果URL是相同来源的，而且XHR更复杂的默认发送证书，但是可以忽略它们，则可以说这种行为是有效的。 Fetch的行为更像其他CORS请求，例如<img crossorigin>，除非您选择使用<img crossorigin =“use-credentials”>，否则不会发送cookie。

非CORS默认失败



默认情况下，如果不支持CORS，从第三方URL获取资源将失败。您可以在请求中添加一个无CORS选项来克服这个问题，虽然这会导致一个“不透明”的响应，这意味着您将无法判断响应是否成功。

cache.addAll（urlsToPrefetch.map（function（urlToPrefetch）{return new Request（urlToPrefetch，{mode：'no-cors'}）;}））。then（function（）{

console.log（'所有的资源已被提取和缓存'）; }）;



处理响应式图像

srcset属性或<picture>元素将在运行时选择最合适的图像资源并发出网络请求。

对于serviceworker，如果您想在安装步骤中缓存映像，您可以选择以下几个选项：



安装<picture>元素和srcset属性将请求的所有图像。

安装一个低分辨率的图像版本。

安装一个高分辨率的图像版本。

实际上，你应该选择选项2或3，因为下载所有的图像将浪费存储空间。



假设您在安装时选择了低分辨率版本，并且您希望在加载页面时尝试从网络中检索更高分辨率的图像，但是如果高分辨率图像失败，请回退到低分辨率版本。这是可以做的，但是有一个问题。



如果我们有以下两个图像：



屏幕DensityWidthHeight1x4004002x800800



在srcset图像中，我们会有这样的标记：



```

<img src =“image-src.png”srcset =“image-src.png 1x，image-2x.png 2x”/>

```

如果我们在2x显示器上，那么浏览器将选择下载image-2x.png，如果我们不在线，则可以.catch（）这个请求，并返回image-src.png而不是缓存，但浏览器会期望这是一个考虑到2x屏幕上额外像素的图像，所以图像将显示为200x200的CSS像素，而不是400x400的CSS像素。唯一的方法是在图像上设置一个固定的高度和宽度。



```

<img src="image-src.png" srcset="image-src.png 1x, image-2x.png 2x" style="width:400px; height: 400px;" />

```



对于用于艺术指导的<picture>元素，这变得相当困难，并将严重依赖于您的图像是如何创建和使用的，但是您也许可以对srcset使用类似的方法。

## 更多

在[https://jakearchibald.github.io/isserviceworkerready/resources](https://jakearchibald.github.io/isserviceworkerready/resources)上维护的serviceworker的文档列表，您可能会发现有用。