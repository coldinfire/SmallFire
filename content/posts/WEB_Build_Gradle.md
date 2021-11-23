---
title: " Gradle 使用 "
date: 2018-12-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - 工具

tags: 
  - Gradle

---

### Gradle 简介

Gradle 是一种开源构建自动化工具，其设计足够灵活，几乎可以构建任何类型的软件。Gradle 采用了领域特定语言 Groovy 的配置，大大简化了构建代码的行数。

#### 基于声明的构建和基于约定的构建

Gradle 的核心在于基于 Groovy 的丰富而可扩展的域描述语言(DSL)。 Groovy 通过声明性的语言元素将基于声明的构建推向下层，你可以按你想要的方式进行组合。 这些元素同样也为支持 Java， Groovy，OSGi，Web 和 Scala 项目提供了基于约定的构建。 并且，这种声明性的语言是可以扩展的。你可以添加新的或增强现有的语言元素。 因此，它提供了简明、可维护和易理解的构建。

#### Gradle 核心

- Gradle 是一个通用的构建工具
- Gradle 核心模型是基于 Task
- Gradle 有几个固定的构建阶段
- Gradle 的扩展方式不止一种
- 构建脚本针对 API 运行

#### Gradle 和 Maven 对比 

Maven更加的标准和规范，Gradle更加的灵活和高效。

Gradle 抛弃了 Maven 的基于 XML 的繁琐配置。

### Gradle 使用

[下载 Gradle ](https://gradle.org/releases/)

### Gradle 生命周期

Gradle 分三个阶段评估和执行构建脚本

#### Initialization

为构建设置环境并确定哪些项目将参与其中。

#### Configuration

为构建构建和配置任务图，然后根据用户想要运行的任务确定需要运行哪些任务以及以何种顺序运行。

#### Execution

运行在配置阶段结束时选择的任务。