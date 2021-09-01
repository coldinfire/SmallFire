---
title: " BABI-BADI-RFC-BDC 区别 "
date: 2021-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils 

---



### BAPI 介绍

BAPI (Business Application Programming Interface) 代表业务应用程序编程接口。 BAPI 是存储在 SAP 系统的 BOR（业务对象库）中以执行特定业务任务的特定方法。 它支持 SAP 组件之间以及 SAP 和非 SAP 组件之间的业务数据交换。 它允许在业务级别而不是技术级别进行集成。 它作为 RFC 启用的功能模块存储在 ABAP Workbench Function Builder 中。 要使用 BAPI 方法访问 SAP 业务对象中的数据，应用程序只需要知道如何使用 BAPI 名称和导入/导出参数调用该方法。 标准 BAPI 更易于使用，因为它可以防止用户不得不处理大量不同的 BAPI。 标准 BAPI 优于单个 BAPI。



### RFC

RFC 代表远程函数调用。 它是 SAP 环境中不同系统应用程序之间通信的协议，包括 SAP 系统之间以及 SAP 系统与非 SAP 系统之间的连接。 它是用于不同 SAP 系统之间通信的标准 SAP 接口。 它描述了 SAP 系统中可用的系统功能模块的外部接口。 R/3 应用程序的功能可以使用 RFC 接口从外部程序扩展。

仅使用 RFC，SAP 系统无法连接到非 SAP 系统来检索数据。 只有通过 BAPI，RFC 才能从非 SAP 系统访问 SAP 系统，反之亦然。