---
date: 2017-11-12
layout: post
title: html5 FileReader API
thread: 9
categories: Translate
tags: [BIT]
excerpt: html5 FileReader API
---

HTML5看到了许多可用于处理浏览器中的文件的新API。 这些API使得完成诸如读取和写入文件或上传使用JavaScript创建的文件等任务变得更加容易。

在这篇博文中，您将学习如何使用FileReader API从本地硬盘读取文件的内容。 您将创建两个演示应用程序。 第一个应用程序将处理阅读，然后显示文本文件的内容。 第二个将读取一个图像文件，然后生成一个数据URL，用来在页面上显示图像。

现在开始！

## The FileReader Interface

> FileReader接口提供了许多可用于读取File或Blob对象的方法。 这些方法都是异步的，这意味着你的程序在文件读取时不会停顿。 这在处理大文件时特别有用。

```
var reader = new FileReader();
```
接下来部分我们开始看下FileReader所提供的方法。

### 1.readAsText()

```
var reader = new FileReader();

reader.onload = function(e) {
  var text = reader.result;
}

reader.readAsText(file, encoding);
```
>  读取文本文件。该方法含两个形参，第一个为所要读取的File 或者Blob 对象，第二个为所用的编码格式（可选，默认UTF-8）。鉴于这是一个异步方法我们需要为文件加载结束时添加一个事件监听器。

### readAsDataURL()

```
var reader = new FileReader();

reader.onload = function(e) {
  var dataURL = reader.result;
}

reader.readAsDataURL(file);
```
> 该方法接收一个文件或Blob并产生一个data URL。通常是一个base64的文件数据字符。你可以用此data URL去做类似为image设置src属性的事情。

### readAsBinaryString()

```
var reader = new FileReader();

reader.onload = function(e) {
  var rawData = reader.result;
}

reader.readAsBinaryString(file);
```
> 用于读取任何类型的文件。返回文件的原生二进制数据。如果你用readAsBinaryString() 结合 XMLHttpRequest.sendAsBinary() 方法，你利用js向服务器可以上传任何类型的文件。

### readAsArrayBuffer()

```
var reader = new FileReader();

reader.onload = function(e) {
  var arrayBuffer = reader.result;
}

reader.readAsArrayBuffer(file);
```
> 一个ArrayBuffer是一个固定长度的二进制数组buffer。在处理文件时（如将JPEG图像转换为PNG），它们可以派上用场。

### abort

```JS
reader.abort();
```
## 接下来综合几个例子看下此API的应用

### FileReader读取文件

[FileReader][1]

### Read Image

[ReadImage][2]

## FileReader API 的浏览器支持性

IE |FIREFOX|CHROME|SAFARIOPERA
---|---|---|---|---|---
10+|	3.6+	|6.0+|	6.0+	|11.1+
Source: [http://caniuse.com/filereader][3]

## 总结

从历史上看，本地应用程序的功能与使用纯Web技术构建的应用程序的功能之间存在着巨大的分歧。 虽然我并不否认这种差距依然存在，但像FileReader这样的API确实有助于弥合鸿沟。

在这篇文章中，您已经学习了如何使用FileReader API从用户的硬盘读取文件，并在页面上显示它的内容。 如果您觉得自己是一个挑战，为什么不尝试创建一个允许用户将文件放到页面上而不是使用<input>元素的应用程序。 我以前的文章实施本地拖放应该有助于让你开始。

### 参考链接

- [Can I Use… FileReader API][4]
- [FileReader Docs (MDN)][5]
- [File API Specification (W3C)][6]


  [1]: https://codepen.io/yanlee26/pen/wpjWro
  [2]: https://codepen.io/yanlee26/pen/VyxaNq
  [3]: http://caniuse.com/filereader
  [4]: https://caniuse.com/#feat=filereader
  [5]: https://developer.mozilla.org/en-US/docs/Web/API/FileReader
  [6]: https://www.w3.org/TR/FileAPI/#FileReader-interface