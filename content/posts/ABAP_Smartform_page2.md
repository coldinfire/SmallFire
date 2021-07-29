---
title: "Smartforms 强制分页打印"
date: 2018-08-01
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



强制分页注意事项：

- 如果是模板，强制分页命令要放在循环中
- 如果是表格，强制分页命令要放在表的主要区域循环中(main)

强制分页可以控制 Form 每页固定显示的数据量。

