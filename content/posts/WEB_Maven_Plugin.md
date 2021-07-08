---
title: "Maven Plugin Goal"
date: 2017-12-30
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Maven

---

### Maven 插件

Maven 插件主要是为 maven 中生命周期中的阶段服务，maven 中只定义了生命周期，以及生命周期的阶段，具体每个阶段中执行何种操作，完全交由插件决定。Maven 实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成。

插件通常提供了一个目标的集合，并且可以使用下面的语法执行：

`<code>mvn [plugin-name]:[goal-name]</code>`

### 插件目标

