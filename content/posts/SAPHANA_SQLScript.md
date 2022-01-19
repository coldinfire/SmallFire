---
title: " SAP HANA SQLScript 使用 "
date: 2021-11-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

SQLScript 是 SAP HANA 的专用 SQL 语言。SQLScript 是为 SAP HANA 数据库层的 SAP HANA 开发而设计的。SQLScript 在 code pushdown paradigm 转换中起着关键作用。

SQLScript 背后的动机是将数据密集型逻辑嵌入 SAP HANA 数据库本身，这称为 Code Pushdown：

![Code Pushdown](/images/HANA/HANA_CodePushdown.png)

#### SQLScript 特点

- 在 SQLScript 中，可以从单个 SQLScript 语句中获得多个结果集，而单个 SQL 语句将只返回一个结果集。
- 在 SQLScript 中也可以进行模块化； 也就是说，一组业务逻辑可以拆分为更小的代码片段，然后捆绑成可重用的逻辑语句组。这些组更具可读性和可理解性。
- 在 SQLScript 中可以使用像 IF/ELSE 语句这样的控制语句，但在常规命令行 SQL 中不可用。
- 在 SQLScript 中可以使用局部变量以及用于过渡结果的控制语句。

### DDL (Data Definition Language)

### DML (Data Manipulation Language)

### DCL (Data Control Language)

### SQLScript Debug