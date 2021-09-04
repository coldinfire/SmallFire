---
title: " CS12 ALV 的增强 "
date: 2019-11-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BOM

---

#### Tcode：CS12的执行程序查看

![CS12](/images/ABAP/BOM_Enhance1.png)

可以看到，最终执行的程序是RCS12001，因此可以在该程序中查找增强。

![RCS12001](/images/ABAP/BOM_Enhance2.png)

#### 执行的程序：

- RCS11001 Display BOM Level by Level

- RCS12001 Display Multilevel BOM

- RCS13001 Summarized BOM - Multilevel

#### User Exits:

- PCSD0001：Applications development R/3 BOMS

- PCSD0002： BOMs: Customer fields in item

- PCSD0003： BOMs: Customer fields in header

- PCSD0004： BOM comparison

- PCSD0005： BOMs: component check for material items

- PCSD0006： Mass changes user exit

- PCSD0007： Check changes in STKO

- PCSD0008： WBS BOM: Customer-specific explosion for creating

- PCSD0009： Order/WBS BOM, determine URL page

- PCSD0010： Order/WBS BOM, determine explosion date

- PCSD0011： Knowledge-based order BOM, parallel update

- PCSD0012： Customer - Mat. number/mat. number during material exchange

- PCSD0013： Customer-specific processing of an explosion for BOM browser

- PCSD0014： Knowledge-Based Order BOM: Status