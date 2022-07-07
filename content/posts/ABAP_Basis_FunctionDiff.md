---
title: " RFC-BAPI-BADI-BDC 区别 "
date: 2021-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### RFC

RFC 代表远程函数调用。 它是 SAP 环境中不同系统应用程序之间通信的协议，包括 SAP 系统之间以及 SAP 系统与非 SAP 系统之间的连接。 它是用于不同 SAP 系统之间通信的标准 SAP 接口。 它描述了 SAP 系统中可用的系统功能模块的外部接口。 R/3 应用程序的功能可以使用 RFC 接口从外部程序扩展。

仅使用 RFC，SAP 系统无法连接到非 SAP 系统来检索数据。 只有通过 BAPI，RFC 才能从非 SAP 系统访问 SAP 系统，反之亦然。可以说 BAPI 是 RFC 的子集。 RFC 通过 BAPI 连接到另一个系统。

RFC 只是一个功能模块，您可以在外部系统中将其作为功能调用进行调用。

#### 特点

- RFC 是独立处理异常的。

### BAPI 介绍

BAPI (Business Application Programming Interface) 代表业务应用程序编程接口。

BAPI 是存储在 SAP 系统的 BOR（Business Object Repository ）中以执行特定业务任务的特定方法。 它支持 SAP 组件之间以及 SAP 和非 SAP 组件之间的业务数据交换。 它允许在业务级别而不是技术级别进行集成。 它作为 RFC 启用的功能模块存储在 ABAP Workbench Function Builder 中。 

从技术上讲，BAPI 函数是可以使用 RFC 技术远程调用的功能模块，但 BAPI 基本上是一个业务对象的方法，它被称为外部系统中的方法。要使用 BAPI 方法访问 SAP 业务对象中的数据，应用程序只需要知道如何使用 BAPI 名称和导入/导出参数调用该方法。 

标准 BAPI 更易于使用，因为它可以防止用户不得不处理大量不同的 BAPI。 标准 BAPI 优于单个 BAPI。

#### 特点

- BAPI 无法处理异常，调用程序必须处理
- BAPI 是直接更新数据库，无法处理程序员为满足用户需求而进行的所有流程逻辑检查和增强
- BAPI 支持 API 方法，而 FM 不支持 API 方法
- BAPI 会执行数据完整性所需的所有检查

### BDC

BDC（批量数据通信）是一种用于数据传输的技术。 它用于通过 SAP 事务本身传输数据。 当您使用 BDC 进行数据传输时，步骤顺序与您使用标准 sap 事务屏幕进行数据上传时相同。 唯一的区别是您可以使用不同的选项进行前景/背景处理。

BDC 主要用于将数据上传到 SAP R/3 系统。通过 Dialog 屏幕模拟用户输入,

生成 ABAP 程序。 Abaper 将创建一个程序读取文本文件并上传到 SAP 系统。事物码 SHDB 将用于记录用户使用的前台执行操作。 仿真后，Abaper 可以生成一个示例程序并从那里修改。BDC 使编程更容易和更快。

#### 特点

- BDC 通过屏幕流运行

### BADI

BADI 是用户出口的面向对象版本。 不是将程序代码输入某个功能模块（如在客户出口中），而是定义一些必须实现预定义方法的类，并且这些方法在预定义点被触发，就像旧的用户出口一样。 一些 BADI 可以有多个独立的实现，这对于软件部署要好得多，因为多个开发人员可以独立实现相同的 BADI。