---
title: " SAP 获取表字段所有信息 "
date: 2022-03-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

###  DDIF_FIELDINFO_GET

Reading Text on Tables or Types.

SE37 执行 DDIF_NAMETAB_GET 输入表名：

![Parameter](/images/ABAP/ABAP_TableField1.png)

点击执行后，返回表信息：

![Result](/images/ABAP/ABAP_TableField2.png)

表字段的详细信息：

![Table Info](/images/ABAP/ABAP_TableField3.png)

### 使用到的核心表

DD03L：Table Fields

DD03T：DD: Texts for fields (language dependent)

DD04L：Data elements

DD04T：R/3 DD: Data element texts

DDFTX：Run-time object with Screen Painter texts

