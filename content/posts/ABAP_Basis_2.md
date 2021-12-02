---
title: " ABAP 简介 "
date: 2018-05-11
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### 什么是 ABAP

ABAP (Advanced Business Application) 是由 SAP 创建的高级编程语言，可帮助企业定制 SAP ERP。 ABAP 可以帮助定制财务会计、物料管理、资产管理和 SAP 的所有其他模块的工作流。

SAP 目前的开发平台 NetWeaver 也同时支持 ABAP 和 Java。 

### ABAP 运行时环境

所有 ABAP 程序都存储在 SAP 数据库中。 在数据库中，所有代码都是用 ABAP 编写的，以两种不同的形式存在：

- 源代码：可以在 ABAP Workbench 工具的帮助下查看和编辑
- 生成的代码：它是一种二进制表示，与 Java 字节码非常相似

ABAP 程序允许您控制运行时系统，它是 SAP 内核的一部分。 运行时系统还允许处理 ABAP 语句。 它控制屏幕的逻辑并响应用户点击或鼠标悬停等用户事件。

### ABAP 程序类型

SAP ABAP 程序要么是一个可执行单元，要么是一个库，是一个可重用的代码。 但是，它不能单独执行。

ABAP 可执行程序分为两类：

- Reports
- Module pools

不可执行的程序类型是：

- INCLUDE modules
- Subroutine pools
- Function groups
- Object classes
- Interfaces Type pools

### SAP ABAP 组件

ABAP Editor：主要用于维护程序。

ABAP Dictionary：用于维护 Dictionary 对象。

Repository Browser：用于显示包中组件的层次结构。

Menu Painter：用于开发 GUI，包括菜单栏和工具栏。

Screen Painter：用于维护在线程序的屏幕组件。

存储库信息系统：存储有关开发和运行时对象的信息，如数据模型、表结构、程序和功能。

Function Builder：该组件帮助您创建和维护功能组和功能模块。

测试和分析工具：如语法检查和调试器。

数据建模器：此工具支持图形建模。

Workbench Organizer：它可以帮助您维护由开发人员管理的多个开发项目进行分发。

