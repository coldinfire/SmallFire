---
title: "JSP 标签库"
date: 2017-11-17
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - JSP
---

### EL表达式

表达式语言（Expression Language，EL），EL 表达式是用`${}`括起来的脚本，用来更方便的读取对象！EL 表达式主要用来读取数据，进行内容的显示。

使用 EL 表达式可以方便地读取对象中的属性、提交的参数、JavaBean、甚至集合。

使用 EL 表达式可以很方便地引用一些 JavaBean 以及其属性，不会抛出 NullPointerException 之类的错误！但是，EL 表达式非常有限，它不能遍历集合，做逻辑的控制。

### JSP 标准标签库（JSTL）

JSP 标准标签库（JSTL）是一个 JSP 标签集合，它封装了 JSP 应用的通用核心功能。

为什么要使用 JSTL：EL 表达式不够完美，需要 JSTL 的支持。JSTL 与 HTML 代码十分类似，遵循着 XML 标签语法，使用 JSTL 让 JSP 页面显得整洁，可读性非常好，重用性非常高，可以完成复杂的功能。

JSTL 支持通用的、结构化的任务，比如迭代，条件判断，XML 文档操作，国际化标签，SQL 标签。 除了这些，它还提供了一个框架来使用集成 JSTL 的自定义标签。

根据 JSTL 标签所提供的功能，可以将其分为5个类别。

- 核心标签
- 格式化标签
- SQL 标签
- XML 标签
- JSTL 函数

### JSTL 使用

使用任何库，你必须在每个JSP文件中的头部包含`<taglib>`标签。