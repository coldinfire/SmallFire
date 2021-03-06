---
title: "ABAP 数据表"
date: 2018-05-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### SAP 数据表类型

**透明表(Transparent table)：** 和数据库具有相同结构的表存储结构，数据库中有一个相应得物理表。

**簇表(Cluster table)：** 该表是 SAP Dict 展现在我们眼前的结构，由透明表转化 <Extras—Change table category> 从单个表去理解与透明表没有差异，是由多个表组成的簇表。它们在物理上对主键只存储一遍，故对簇表的关联查询可以极大提高访问速度。

**池表(Pool table)：** 由透明表转换，可用来存储控制数据，与数据库中的表是多对一关系。

- INSERT 透明表 INTO 簇表：只要透明的簇表的主键都在透明表里面就行。就是透明表主键多了也无所谓。

- INSERT 透明表 INTO 池表：透明表和池表的主键必须相同的。    

**表簇：** 几个簇表可以组合成一个表簇，几个簇表存贮在数据库中一个相应的表里，DB层的物理表。

**结构：** 结构在数据库不存在数据记录。结构用于在程序之间或程序与屏幕之间的接口定义。

**附加结构：** 附加结构定义字段的子集，该字段属于其他表格或结构，但是在修正管理中作为单独的对象。

### SAP数据表的组成元素

**Data element：** 构成结构、表的基本组件，包含 Search Help、Parameter ID、Field Label。

- Goto > Documentation > Change ：修改提示文档信息

**Domain：** 定义数据元素的技术属性，类型，长度，精度。

- Format、Output Charact
  
  - | Type   | Desc            | Type | Desc         | Type | Desc                            |
    | ------ | --------------- | ---- | ------------ | ---- | ------------------------------- |
    | DEC    | doubule         | FLTP | Fload        | DATS | Date(8)                         |
    | INT1   | 0~255           | INT2 | -32768~32767 | TIMS | Time(6)                         |
    | NUMC   | 数字字符(1-255) | INT4 | 四个字节     | CHAR | 字符(1-255)                     |
    | QUAN   | 金额(1-17)      | UNIT | 单位(2-3)    | CLNT | Client(3)                       |
    | CURR   | 货币字段(1-17)  | CUKY | 货币代码(5)  | LANG | Language(internal 1,external 2) |
    | STRING | 字符串          |      |              |      |                                 |
  
- Converse Routine：转换规则设置，注意前导0的补充问题，将该字段设置为 ALPHA。
  
- Lower Case：不勾选，默认会全部转换为大写字母。

- Value Range：设置该 Domain 的固定取值列表和其含义,C,Y,F类型中的一种。

**Field:** 透明表字段最长 16 位，可以作为透明表的主 / 外键，继承了 Data Element 的所有属性。

**Technical Setting**：

透明表中的 Technical Setting 设置 Log data changes 后可以使用 SCU3，STAD 查看日志。

- 非特殊情况不设置，会占用很大的内存

### 数据分类

#### Master Data

业务主数据，即：SAP 各模块中的主数据：总账科目、供应商主数据、物料主数据等。

#### Transaction Data

业务处理数据；在业务处理操作中，生成的凭证号等数据，如：销售订单凭证号、采购订单凭证号、收货凭证号等。

#### System Data

系统数据；包括 ABAP 程序的源码、元数据、文档等数据。

#### Configuration Data

配置数据；存放企业项目实施时，配置的初始化信息，如：货币汇率、订单类型、变式等。

### 维护ABAP数据字典

- SE80 – Repository Browser
- SE11 – ABAP Dictionary
- SE13 – Dictionary technical settings
- SE14 – Database Utility
- SE15 – Repository Information System
- SE16 /SE16N – Data Browser
- SM30 – Maintain Table Views
- SM31 – Table Maintenance

### 复制结构/表中的所有字段

- SE11创建自定义表/结构
- Edit -> Transfer Fields
- 选择要复制的表/结构和要复制的字段![Copy Components](/images/ABAP/Table1.png)
- 选择要复制的字段并选择Copy![Field Selection](/images/ABAP/Table2.png)
-  粘贴所有复制的字段并激活自定义的表/结构![Field paste](/images/ABAP/Table3.png)
