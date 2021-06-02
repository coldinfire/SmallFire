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

## EL表达式

表达式语言（Expression Language，EL），EL 表达式是用 `${expression}` 括起来的脚本，用来更方便的读取对象。EL 表达式主要用来读取数据，进行内容的显示。

使用 EL 表达式可以方便地获取 4 个内置对象（域）中的数据，自定义对象中的数据、提交的参数、JavaBean、甚至集合。

使用 EL 表达式可以很方便地引用一些 JavaBean 以及其属性，不会抛出 NullPointerException 之类的错误。但是，EL 表达式非常有限，它不能遍历集合，做逻辑的控制。

### 获取数据

#### 运算符

算术运算符：+、-、*、/、%

比较运算符：>、<、>=、<=、==、!=

逻辑运算符：&&、||、!

空运算符：用于判断字符串、集合、数组对象是否长度为0；

- 表达式：`${empty list}`

- EL 三元运算符: ${empty 对象 ? 表达式1 : 表达式2}

#### 获取值

EL 表达式只能从域对象中获取值，`${域名称.键名}` 从指定域中获取指定键的值。

域名称:

- pageScope              ->   pageContext
- requestScope         ->   request
- sessionScope         ->   session
- applicationScope   ->   application (ServletContext)

web 中的四个域对象（容器对象）范围：从小到大顺序：

- page < request < session < application(ServletContext)

EL 表达式从四个域中取值，就是在代替使用 PageContext 中的getAttribute 方法取值的麻烦，底层依然在使用 PageContext 中的getAttribute 方法。

使用 pageContext 的 getAttribute方法或者 findAttribute 方法从4个范围中取出数据的时候，如果指定的 key 不存在会得到 null，而使用 EL 表达式取出的时候指定的 key 不存在，页面上什么都没有。

如果不知道数据在哪个范围中，这时可以不用指定范围直接书写 key值即可：`${键名}`。依次从最小的域中查找是否有该键对应的值，知道找到为止。

#### EL 表达式获取复杂数据

在 EL 表达式中，获取对象的属性的值的时候，其实不是在看这个对象所在的类是否有这个属性，只要这个对象所在的类中有 getXxxxx 方法，就可以使用 EL 表达式获取 Xxxx 值。

- 获取数组数据：`${arr1[3]}`
- 获取集合中的值：`${list[2]}`
- 获取自定义对象的属性值：`${object.attirbute}`

## JSP 标准标签库（JSTL）

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