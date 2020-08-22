---
title: "报表开发<OO ALV工具>"
date: 2018-07-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---


## 显示后，修改字段目录和布局

​	通过方法在第一次显示之后，设置一个新的布局或则字段目录。

### 方法

- 字段目录：GET_FRONTEND_FIELDCATALOG，SET_FRONTEND_FIELDCATALOG

- 布局：GET_FRONTEND_LAYOUT，SET_FRONTEND_LAYOUT

## 字段下拉

使用内表存放句柄和对应的值，表类型："LVC_T_DROP"

内表通过方法"
SET_DROP_DOWN_TABLE"

