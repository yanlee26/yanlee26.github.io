---
date: 2015-10-01
layout: post
title: Design Pattern
thread: 9
categories: JS
tags: [BIT]
excerpt: Design Pattern.
---

> 了解一下经典的设计模式，总之一句话：大道至简，要言不繁！

## 设计模式

> 代表了最佳实践，是软件开发过程中面临的一般问题解决方案。
两大原则：
- 对接口而非实现编程
- 首选对象而非继承

### 设计模式类型：创建型(Creational)，结构型(Structural)，行为型(Behavoral)

|模式&描述|子类|
|:---|---:|
|创建型|工厂模式（Factory Pattern）抽象工厂模式（Abstract Factory Pattern）单例模式（Singleton Pattern）建造者模式（Builder Pattern）原型模式（Prototype Pattern）|
|结构型| 适配器模式（Adapter Pattern）桥接模式（Bridge Pattern）过滤器模式（Filter、Criteria Pattern）组合模式（Composite Pattern）装饰器模式（Decorator Pattern）外观模式（Facade Pattern）享元模式（Flyweight Pattern）代理模式（Proxy Pattern）|
|行为型| 责任链模式（Chain of Responsibility Pattern）命令模式（Command Pattern）解释器模式（Interpreter Pattern）迭代器模式（Iterator Pattern）中介者模式（Mediator Pattern）备忘录模式（Memento Pattern）观察者模式（Observer Pattern）状态模式（State Pattern）空对象模式（Null Object Pattern）策略模式（Strategy Pattern）模板模式（Template Pattern）访问者模式（Visitor Pattern）|
|J2EE 模式|MVC 模式（MVC Pattern）业务代表模式（Business Delegate Pattern）组合实体模式（Composite Entity Pattern）数据访问对象模式（Data Access Object Pattern）前端控制器模式（Front Controller Pattern）拦截过滤器模式（Intercepting Filter Pattern）服务定位器模式（Service Locator Pattern）传输对象模式（Transfer Object Pattern）|

### 设计模式的六大原则

- 开闭:对扩展开发，对修改关闭
- 里氏替换：任何基类可以出现的地方，子类一定可以出现
- 依赖倒转：针对接口编程，依赖于抽象而不依赖于具体
- 接口隔离：使用多个隔离的接口，比使用单个接口要好
- 最少知道：一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立
- 合成复用：尽量使用合成/聚合的方式，而不是使用继承

## 工厂模式

> 建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象

## 抽象工厂模式

> 围绕一个超级工厂创建其他工厂.接口是负责创建一个相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。

## 单例模式

> 涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

- 注意
	+ 单例类只能有一个实例。
	+ 单例类必须自己创建自己的唯一实例。
	+ 单例类必须给所有其他对象提供这一实例。

## 建造者模式

> 使用多个简单的对象一步一步构建成一个复杂的对象

## 原型模式

> 用于创建重复的对象，同时又能保证性能。实现了一个原型接口，该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式

### 适配器模式

> 作为两个不兼容的接口之间的桥梁.这种模式涉及到一个单一的类，该类负责加入独立的或不兼容的接口功能。

## 桥接模式

> 把抽象化与实现化解耦，使得二者可以独立变化。
涉及到一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响。

## 过滤器模式

> 允许开发人员使用不同的标准来过滤一组对象，通过逻辑运算以解耦的方式把它们连接起来。

## 组合模式

> 把一组相似的对象当作一个单一的对象。组合模式依据树形结构来组合对象，用来表示部分以及整体层次。这种类型的设计模式属于结构型模式，它创建了对象组的树形结构。
这种模式创建了一个包含自己对象组的类。该类提供了修改相同对象组的方式。

## 装饰器模式

> 允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。

## 外观模式

> 隐藏系统的复杂性，并向客户端提供了一个客户端可以访问系统的接口。这种类型的设计模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性。

## 享元模式

> 主要用于减少创建对象的数量，以减少内存占用和提高性能。

## 代理模式

> 一个类代表另一个类的功能

## 责任链模式

> 为请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。

## 命令模式

> 种数据驱动的设计模式，它属于行为型模式。请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命
令。

## 解释器模式

> 解释器模式（Interpreter Pattern）提供了评估语言的语法或表达式的方式，它属于行为型模式。这种模式实现了一个表达式接口，该接口解释一个特定的上下文。这种模式被用在 SQL 解析、符号处理引擎等。

## 迭代器模式

> 是 Java 和 .Net 编程环境中非常常用的设计模式。这种模式用于顺序访问集合对象的元素，不需要知道集合对象的底层表示。

## 中介者模式

> 用来降低多个对象和类之间的通信复杂性。这种模式提供了一个中介类，该类通常处理不同类之间的通信，并支持松耦合，使代码易于维护。

##备忘录模式

> 保存一个对象的某个状态，以便在适当的时候恢复对象。

## 观察者模式

> 针对一对多的对象关系。当一个对象被修改时自动通知它的依赖对象。

## 状态模式


> 类的行为基于其状态而变。创建表示各种状态的对象和一个行为随着状态改变而改变的context对象。

## 空对象模式
> 一个空对象取代NULL对象实例的检查。Null 对象不是检查空值，而是反应一个不做任何动作的关系。这样的 Null 对象也可以在数据不可用的时候提供默认的行为。


## 策略模式

> 一个类的行为或算法可以在运行时更改。

## 模板模式

> 一个抽象类公开定义了执行它的方法的方式/模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。


## 访问者模式

> 改变元素类的执行算法，据此执行算法可以随着访问者而变。

##  MVC模式

> Model-View-Controller（模型-视图-控制器） 模式。这种模式用于应用程序的分层开发。
- Model（模型）模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。
- View（视图）视图代表模型包含的数据的可视化。
- Controller（控制器）控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。

## 业务代表模式

> 对表示层和业务层解耦。它基本上是用来减少通信或对表示层代码中的业务层代码的远程查询功能。在业务层中我们有以下实体。
- 客户端（Client）表示层代码可以是 JSP、servlet 或 UI java 代码。
- 业务代表（Business Delegate）一个为客户端实体提供的入口类，它提供了对业务服务方法的访问。
- 查询服务（LookUp Service）查找服务对象负责获取相关的业务实现，并提供业务对象对业务代表对象的访问。
- 业务服务（Business Service）业务服务接口。实现了该业务服务的实体类，提供了实际的业务实现逻辑。

## 组合实体模式

> 用在 EJB 持久化机制中。

## 数据访问对象模式

## 前端控制器模式

## 拦截过滤器模式

## 服务定位器模式

## 传输对象模式



























