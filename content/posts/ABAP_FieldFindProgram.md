---
title: " ABAP通过字段找表程序 "
date: 2018-06-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 获取数据保存在哪个表

1. 前台对指定栏位 使用 F1 帮助找表
2. **ST05 跟踪业务操作过程，检索需要的数据表.（此方法找表很高效）**
3. 对于文本字段找表，可以找到前台维护处-> 维护长文本 -> 表头 -> 表名
4. F1某些时候找表找到的是结构，可以试着通过结构找 include 组件，有找到表的可能，还可以通过相关视图去查找，（找到视图基本就能找到表）
5. 通过相关标准 report /query 去查找
6. 找到对应程序，或者相应操作，使用调试 + 设置 watchpoint 查找
7. 通过结构或者检查表找
8. 通过 data element ->domain -> 值表

### 使用程序

```JS
*&---------------------------------------------------------------------* 
*& Report ZTABLEFIND 
*&---------------------------------------------------------------------*
REPORT ztablefind. 
TABLES: dd02t, dd03l, dd02l. 
DATA: BEGIN OF field1 OCCURS 0. 
INCLUDE STRUCTURE dd03l. 
DATA: END OF field1. 
DATA: BEGIN OF field2 OCCURS 0. 
INCLUDE STRUCTURE dd03l. 
DATA su TYPE i. 
DATA: END OF field2. 
DATA:field_sum TYPE i.

SELECT-OPTIONS ified FOR dd03l-fieldname. 
SELECT-OPTIONS ittype FOR dd02l-tabclass. 
DATA fieldsum TYPE i. 
SELECT * FROM dd03l INTO TABLE field1 WHERE fieldname IN ified. 
SORT field1 BY tabname. 
LOOP AT ified.
  fieldsum = sy-tabix. 
ENDLOOP. 
LOOP AT field1. 
  field_sum = field_sum + 1. 
  MOVE-CORRESPONDING field1 TO field2. 
  AT END OF tabname. 
*   field2-tabname = field1-tabname. 
*   move-corresponding field1 to field2. 
    field2-su = field_sum. 
    COLLECT field2. 
    CLEAR field2. 
    CLEAR field_sum. 
  ENDAT. 
ENDLOOP. 
LOOP AT field2 WHERE su = fieldsum. 
  SELECT SINGLE * FROM dd02t 
    WHERE tabname = field2-tabname AND ddlanguage = sy-langu. 
  SELECT SINGLE * FROM dd02l 
    WHERE tabname = field2-tabname AND tabclass IN ittype AND 
    as4local = field2-as4local AND as4vers = field2-as4vers. 
IF sy-subrc = 0. 
WRITE: / field2-tabname,dd02l-tabclass,dd02t-ddtext. 
ENDIF. 
ENDLOOP.
```

