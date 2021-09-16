---
title: " SAP ABAP 代码扫描工具 "
date: 2020-03-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 使用场景

在不知道某功能模块的名称，并且需要查找使用 RFC 的位置，则可以使用诸如 CALL FUNCTION 或DESTINATION 之类的关键字来运行 ABAP 代码扫描工具。

当增强程序出现问题，但是不好定位具体的程序或则增强位置，可以使用报错信息 Message 或则其他条件等关键信息来运行ABAP代码搜索。

SAP提供两个代码扫描工具

- T-code：`code_scanner`

- Program：`RS_ABAP_SOURCE_SCAN` - 该程序没有T-code，需要使用SE38去执行

