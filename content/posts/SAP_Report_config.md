---
title: " SAP Report 选择和显示栏位配置 "
date: 2020-09-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils
 
---

### SAP 的一些 Report 的选择和显示栏位是可配置的。

MB51 是比较常见的查询物料凭证的报表，在该报表中，如下图所示，默认可以根据物料号码、工厂、移动类型等信息进行查询。系统读取物料凭证的二个表（MKPF、MSEG）到 MB51 的清单中，因此理解 MB51 中的字段关键就是理解物料凭证的二个表 MKPF、MSEG 的值。

![MB51 report](/images/SAPUtils/SAP_REPORT_CONFIG1.png)

其中如果有一些敏感的字段（如金额）信息，可以让其不在报表中显示，同样对于一些字段选择，可以按特殊需求进行配置。

![MB51 report](/images/SAPUtils/SAP_REPORT_CONFIG2.png)

配置路径：IMG–>Materials Management–>Inventory Management and Physical Inventory–>Reporting–>Define Field Selection for Material Document List

