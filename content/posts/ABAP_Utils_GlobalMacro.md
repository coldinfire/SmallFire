---
title: " SAP 全局宏设置和使用 "
date: 2021-08-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 宏定义和使用

在 ABAP 代码的开发中通过使用`DEFINE ... END-OF-DEFINITION.` 来创建局部的宏变量，通过局部宏变量的设定可以让代码变得更加简单，同时也避免了代码冗余。

最常用的场景就是设置 ALV 的显示字段参数，通过宏定于可以节省很多代码的编写。

### 全局宏使用

全局宏使用于公共的工具类代码，可以通过全局宏来定义，并在指定代码中直接使用。在节省代码量的同时可能会导致代码的阅读没那么方便。

#### 创建全局宏

SAP 的 Global Macro 是通过 SM30 表维护在表 *TRMAC* 中维护新的全局宏设置来实现的。

#### 代码中使用

直接在代码中使用在表中维护的宏变量，使用方式和局部定义的宏一样。

