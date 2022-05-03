---
title: " SAP 获取Activate物料 "
date: 2022-04-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
---

### DEMO

```ABAP
TYPES: BEGIN OF type_active,
  plwrk TYPE werks_d,
  werks TYPE werks_d,
  matnr LIKE mara-matnr,
END OF type_active.
DATA: gt_active TYPE TABLE OF type_active WITH HEADER LINE.

SELECT plwrk plwrk AS werks matnr INTO CORRESPONDING FIELDS OF TABLE gt_active
  FROM mdkp AS a INNER JOIN mdtb AS b ON a~dtnum = b~dtnum
 WHERE a~matnr IN s_matnr
   AND a~plwrk IN s_werks
   AND b~mng01 > 0
   AND a~dtart = 'MD'
   AND ( b~plumi = '-' OR b~plumi = '+' OR b~plumi = 'B' ).
SORT gt_active BY werks matnr.
DELETE ADJACENT DUPLICATES FROM gt_active COMPARING werks matnr.
```

