---
title: " ABAP Range 定义 "
date: 2018-08-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
 
---

### Range使用

Range Table为SAP R/3系统标准内表的一种，结构与Selection Table一致，由SIGN,OPTION,LOW,HIGH字段组成。Range Table 常用于 Open SQL 语句中的条件筛选，变式判断，可以优化取数效率与程序性能.

- 当然要注意 RANGE 的行项目有上限的，***在 ECC6 中大概 2 万行将导致 ABAP DUMP。***

#### 定义

- DATA: range_tab {TYPE RANGE OF type} | {LIKE RANGE OF dobj}.
- DATA: gr_werks TYPE RANGE OF werks_d WITH HEADER LINE,
  - gw_werks LIKE LINE  OF gr_werks.
- RANGES: range_tab FOR dobj [OCCURS n]. ：创建一个选择表

  - For 后面字段必须为参考表的字段，不能使用 Data Element 来定义.

#### 使用

Range： gr_matnr 字段参照数据库表字段。

```ABAP
RANGES: gr_matnr FOR marc-matnr OCCURS 0.
CLEAR gr_matnr.
gr_matnr-sign = 'I'.
gr_matnr-option = 'EQ'.
gr_matnr-low = xxx.
gr_matnr-high = xxx.
APPEND gr_matnr.
```

### 选择表

系统为每个 SELECT-OPTIONS 语句创建选择表。选择表的目的是按标准化的方式保存复合选择限制。它们可按多种方式使用。它们的主要目的是使用 Open SQL 语句的 WHERE 子句 把选择标准直接传输到数据库表。

选择表是一个带表头行的内表。它的行结构是字段字符串，由四个组件构成，即 SIGN、OPTION、LOW 和 HIGH。每个选择表行表示数据选择的条件：

- SIGN：控制保存在OPTION中的运算符是否需要翻转。
  - I：包含标准-运算符不翻转
  - E：排除标准-运算符翻转
- OPTION：包含选择运算符
  - HIGH为空：可以使用EQ、NE、GT、LE、LT、CP、NP运算符
  - HIGH不空：可以使用BT、NB运算符
- LOW：LOW的数据类型与参照的标准表字段一致
  - HIGH为空：LOW的内容定义单值选择，与OPTION结合指定条件
  - HIGH不空：LOW与HIGH共同组成范围值
- HIGH：为间隔选择指定上界

#### 将选择表转换为 Range

```ABAP
DATA: range_werks TYPE RANGE OF werks_d WITH HEADER LINE,
      range_werk  LIKE LINE OF range_werks.
APPEND LINES OF s_werks TO range_werks.
```

