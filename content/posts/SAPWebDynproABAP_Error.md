---
title: " Web Dynpro ABAP:Error List "
date: 2020-08-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---

### Error when executing webdynpro application

#### Collection of Node COMPONENTCONTROLLER.1.DATA Violates the Cardinality.

转到您在 COMPONENTCONTROLLER 中创建的节点 DATA。 选择节点并将基数更改为 0...n 或 1...n。由于您要显示多行数据，因此您应该选择 2 个值中的 1 个。 

- 如果 ALV 在某个点上可能根本不包含任何数据，请选择 0...n；
- 如果希望它在任何时间点至少包含 1 行，请选择 1...n。