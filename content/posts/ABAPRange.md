---
title: " ABAP定义Range "
date: 2018-08-16T17:20:58+08:00
draft: false
author: Small Fire
isCJKLanguage: true
categories: 

  - ABAP

tags: 
  - range
 
---



### Range使用

​	Range Table为SAP R/3系统标准内表的一种，结构与Selection Table一致，由SIGN,OPTION,LOW,HIGH字段组成。Range Table 常用于 Open SQL 语句中的条件筛选，变式判断。

#### 定义

- DATA range_tab {TYPE RANGE OF type} | {LIKE RANGE OF dobj}.
  
-  DATA: gr_werks TYPE RANGE OF werks_d,   gw_werks LIKE LINE  OF gr_werks.
  
- RANGES range_tab FOR dobj [OCCURS n].

  - For 后面字段必须为参考表的字段.

  - ```JS
    TABLES：marc.
    RANGES: gr_matnr FOR marc-matnr.
    ```

#### 使用

```
gr_matnr-sign = 'I'
gr_matnr-option = 'EQ'/'BT'
gr_matnr-low = xxx
gr_matnr-high = xxx
append gr_matnr.
```

