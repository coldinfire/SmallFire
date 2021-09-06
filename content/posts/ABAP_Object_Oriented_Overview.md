---
title: "ABAP Object Oriented 概述"
date: 2018-09-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Utils

---

### ABAP Object Oriented

通过使用面向对象技术可以减少错误发生的隐患以及增强代码的可维护性。

ABAP Object Oriented 的年代史：

- SAP Basis Release 4.5 发布了 ABAP OO 的一个版本，引入了类接口的概念，并可以通过类来创建对象（实例化类）。
- SAP Basis Release 4.6 发布了 ABAP OO 的完全版本，引入了 OO 方式的重要概念继承，可以通过多个接口来建立一个复合的接口。
-  SAP WEB APPLICATION SERVER 6.10/6.20 SAP basis 的下一代版本，在类之间引入了 Friendship 的概念。并引入了对象服务（object service）可以把对象存储在数据库中。
- SAP WEB APPLICATION SERVER 6.40 引入了共享对象（Shared Objects）的概念，即允许在应用服务器的共享内存中存储对象。这样在这个服务器中的任何一个程序都可以访问它。

#### 几个关键点

- ABAP OO 是 ABAP 编程语言的扩展

- ABAP OO 是向下兼容的

- SAP发布ABAP OO是为了进一步增强代码的可重用性

### 面向过程和面向对象的区别

 随着 ABAP OO 的发布，ABAP 运行时支持面向过程和面向对象两种模式。

#### 面向过程编程

面向过程的模式，程序的运行通常是从 Screen 的 Dialog module 或Selection Screen 的 START-OF-SELECTION 事件开始的。

- 在这些处理模块中操作全局变量来实现需求的功能。

- 通过内部的 Form 和外部的 Function Module 来实现程序的模块化。这些过程除了可以操作全局变量外还可以具备内部的本地变量来协助实现内部的一些特定功能。

#### 面向对象编程

面向对象编程里唯一的结构单位就是类(Class)，这里类的实例对象取代了全局变量。这些对象封装了应用的状态和行为。

- 应用的状态：是用属性来代表的，它取代了面向过程中的全局变量。

- 应用的行为：是通过方法来实现的，他们用来改变应用的属性或者调用其它对象的方法。

ABAP OO 支持 Object Oriented 和面向过程的两种模式，这样在传统的 ABAP 程序（比如报表，模块池，功能池等）中你也可以使用 ABAP 对象类。在这些程序里也就可以使用基于面向对象的新技术了，比如一些用户界面，避免了要想使用这些新技术必须重新编写程序。

纯粹的 ABAP OO 模式，所有的代码都封装在类中。你的应用中并不直接触presentation layer(SAP Gui , Business Server Pages etc.)、persistent data(database table,system file)。他们是通过类库中的相应服务类来提供的。
比如 SAP Control Framework、Desktop Office Integration，and Business Pages提供了与表现层的接口。

对于 SAP Web Application 6.10 以上提供了与数据库层接口的服务对象。虽然纯粹的OO模式技术上是可行的，但是现实中还存在着大量的两种模式的混合体。

ABAP 面向对象和面向过程的技术同时应用，调用常用的功能模块，调用屏幕或者直接访问数据库等在对象中都存在。混合的模式即利用了新技术又保护了已投入的成本。