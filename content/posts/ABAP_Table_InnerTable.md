---
title: " ABAP 内表使用 "
date: 2018-05-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

#### 工作区和内表的关系

![自定义表创建](/images/ABAP/ABAP_Basis_1.png)

### 工作区

工作区是单行数据与对应的内表具有相同的格式。 它用于一次处理一个内部表中的数据，相当于一维数组。

#### 间接定义

通过 TYPES 定义并创建结构行，不能再程序中直接使用，需要通过 DATA 定义对应的工作区或内表 。

```ABAP
TYPES: BEGIN OF str_order.
  aufnr  TYPE afko-aufnr,
  dauat  TYPE afpo-dauat,
 END OF str_order.
DATA: lt_table TYPE TABLE OF str_order,
      ls_table TYPE str_order.
```

#### 直接定义

直接使用 DATA 声明一个结构对象，可以在后续程序中直接使用该工作区。

```ABAP
DATA: BEGIN OF str_matnr,   
  matnr TYPE matnr,
  werks TYPE bukrs,
END OF str_matnr.
```

参照 DB 或结构创建工作区：`DATA <wa> TYPE <dbtab>|<str>.`

参照内表创建工作区：`DATA <wa> LIKE LINE OF <itab>.`

#### 结构复用

作用是将结构类型 structure_type 与结构变量 structure 的所有组件字段拷贝到当前结构定义的指定位置。

`INCLUDE { {TYPE struc_type} | {STRUCTURE struc} } [AS name [RENAMING WITH SUFFIX suffix]].`


- 结构对象复用

     ```ABAP
     DATA: BEGIN OF gt_result OCCURS 0,
       endcount TYPE zz_final_count,
       enddiffs TYPE zz_final_diffs. "直接定义组件字段，但前面语句后面使用逗号"
       INCLUDE STRUCTURE str_matnr.  "直接将结构对象包括进来,也可以是已经定义的结构"
       INCLUDE TYPE str_order.   "直接将结构类型包括进来"
       DATA:comm LIKE ztest_str. "直接参照"
     DATA: END OF gt_result.
     ```

- 结构类型复用

     ```ABAP
     TYPES: BEGIN OF str_result,
       endcount TYPE zz_final_count,
       enddiffs TYPE zz_final_diffs. "直接定义字段，但是保留前面的逗号"
       INCLUDE STRUCTURE str_matnr.  "直接将结构对象包括进来"
       INCLUDE TYPE str_order.       "直接将结构类型包括进来"
       TYPES: uname type c,
         ustatus type c.
     TYPES: END OF str_pidoc.
     ```

### 内表

INTERNAL TABLE 用于对数据库表的子集执行表计算，也用于根据用户需要重新组织数据库表的内容，以便在 ABAP 中动态使用。 内表中的每一行都具有相同的字段结构。 内表的主要用途是在程序中存储和格式化来自数据库表的数据。内表仅在程序运行时存在。

#### 内表类型

- 索引表：标准表(Standard table)、排序表(Sorted table)


- 标准表：每一行数据都有关键字和系统生成的逻辑索引。


- 排序表：排序表按照关键字升序排序后再进行存储。


- 哈希表：(Hashed table) 没有索引的表，只能靠关键字进行寻址，用哈希算法管理数据。

#### 内表定义

可以通过引用现有表来创建内表。 现有表可以是标准 SAP 表、Z 表或其它内表。

- `DATA table_name TYPE TABLE OF sap_table|ztable|itab [WITH HEADER LINE].`
- LIKE LINE OF 后面接一个内表，表示一个 data 参数具有和内表一样的结构，可当做 Work Area 使用

可以通过引用现有结构来创建内表。现有结构可以是 SAP 结构，Z 结构或程序内定义的结构。

- `DATA table_name TYPE TABLE OF structure [WITH HEADER LINE].`
- `DATA table_name LIKE TABLE OF structure [WITH HEADER LINE].` ：定义内表，该内表和后面的结构字段一致

WITH HEADER LINE 作用：

- 系统会在此处自动创建工作区，此工作区称为 HEADER LINE
- 工作区具有与内部表相同的数据类型，itab[] 来标识内表，itab 标识工作区
- 在 Header Line 可以完成对表格内容的所有更改或任何操作。 因此，记录可以直接插入表中或直接从内部表中访问

### 内表操作

#### 内表清空

- `CLEAR itab`：仅清空工作区，对内表数据存储空间不影响。

-    `REFRESH itab`：清空内表数据存储空间，对工作区不影响。


-    `REFRESH itab FROM TABLE dbtab`：清空内表存储空间，填充从数据库表所获数据。
  
-    `FREE itab`：清空内表数据存储空间，对工作区不影响。

#### APPEND：增加，内表赋值

有 HEADER LINE 的内表，数据被赋给内表 HEADER LINE 后再 APPEND 到表中最后一行。

- `APPEND itab.`    

对于没有 HEADER LINE 的内表，只能通表外部工作区来传递数据。

- `APPEND wa TO itab.`
- `APPEND LINES OF itab1 [FROM idx1] [TO idx2] TO itab2.`

添加初始行，初始行向表中添加一条用其类型的初始值初始化的行。

- `APPEND INITIAL LINE TO itab.`

#### INSERT：向内表插入数据


- `INSERT wa INTO TABLE itab.`：将结构体中数据新增到内表
- `INSERT wa INTO TABLE itab INDEX index.` ：将结构体中数据插入到内表指定位置


- `INSERT INITIAL LINE INTO itab.`：向内表中插入一条空行数据

复制内表

- `INSERT LINES OF itab1 [FROM idx1] [TO idx2] INTO itab2 [INDEX idx3].`：将 itab1 指定范围数据插入到 itab2 
- `MOVE itab1 TO itab2. ` & `itab2 = itab1.` 
- 如果有表头行，则需要使用 `itab2[] = itab1[].` 

#### DELETE：删除数据

- `DELETE TABLE itab WITH TABLE KEY k1=v1...kn=vn.`：删除单条。多条时只删除第一条，条件为表关键字。


- `DELETE TABLE itab [FROM wa].`：删除单条。多条数据时，只删除第一条。条件为所有关键字段，值来自工作区。
- `DELETE itab INDEX idx.`：根据索引删除具体行数据。
- `DELETE itab [FROM n1] [TO n2] WHERE cond1 AND cond2 .`: 根据条件删除多条数据


- `DELETE itab FROM idx1 TO idx2.`：根据索引删除具体行数范围间数据。


- `DELETE ADJACENT DUPLICATES FROM itab [COMPARING f1 f2 | ALL FIELDS].`：删除重复数据，仅保留第一条。执行此条件前必须先按照内表关键字声明的顺序进行排序。
- 如果指定了 COMPARING 选项，则需要根据指定的比较字段顺序进行排序，才能删除所有重复数据

#### MODIFY：修改内表数据

按内表位置或者具体内表字段值相等条件修改内表数据。

- `MODIFY itab [INDEX idx] FROM wa [TRANSPORTING f1...fn] WHERE condition.`

#### 从内表中读取数据

READ：使用 READ 读取数据，如果找到具有指定索引的条目，则将 SY-SUBRC 设置为 0。如果指定的索引小于 0，则会发生运行时错误。如果指定的索引超过表大小，则 SY-SUBRC 设置为 4。

- `READ TABLE itab [INTO wa] FROM wa.`：以表关键字为查找条件，条件来自 工作区。


- `READ TABLE itab [INTO wa] WITH KEY k1=v1...kn=vn [BINARY SEARCH].`
  - 二分法查找出来的数据是第一条符合条件的数据，必须先按指定的关键字段排序


- `READ TABLE itab [INTO wa] INDEX index.`

LOOP ... ENDLOOP：循环读取内表数据，在循环中使用系统变量 SY-TABIX 可获取当前所执行的行数。

- `LOOP AT ITAB [INTO WA] FROM n1 TO n2.`：读取内表具体索引行数之间的数据。


- `LOOP AT ITAB [INTO WA] WHERE cond.`：按具体字段条件读取内表。
- `LOOP AT ITAB [ASSIGNING wa ] WHERE cond.`：根据查询条件循环内表数据并分配给字段符号。

#### COLLECT：内表数据分类汇总

COLLECT 是另一种用于填充内部表的语句。 通常在将行插入具有唯一标准键的内部表时使用 COLLECT。

- `COLLECT [wa INTO] itab.`

如果内表带有标题行，则省略 INTO 选项。 假设已经有一个条目与尝试添加的条目具有相同的键，那么不会在表中添加新行，会汇总了两个条目的数字字段，并且仅存在与该键对应的一个条目。

#### AT...ENDAT：设置内表循环触发条件

该语法为事件控制函数，应用于 LOOP 循环语句中，用于获取内表的数据变化事件。需要在声明结构时按调用顺序排序。

Loop 的时候不能加条件；AT 和 ENDAT 之间不能使用 loop into 的 working area。

- `AT NEW field.`：当某个字段数据与上一行数据值不同时触发该事件。
  - 当 field 字段或者 field 字段左边的字段内容发生变化时该事件后面的语句都会执行
  - field 必须是内表的第一个字段
  - 内表中 field 之后的字段的值都会变成 " * "


- `AT END OF field.`：当内表中某个字段当前行值与下一行值不同时触发该事件。


- `AT FIRST.`：当执行内表第一行时触发该事件。


- `AT LAST.`：当执行内表最后一行时触发该事件。

#### DESCRIBE：获取内表具体属性

- `DESCRIBE TABLE itab LINES n.`：获取内表当前总行数，n 为整型 i。
- `n = lines (itab). `

#### SORT：内表排序操作

- `SORT itab ASCENDING|DESCENDING BY field1 field2 field3.`
  - 指示符在 BY 在前面，表示后面的字段都用这个升降序，作用范围是后面 BY 所有的字段
- `SORT itab BY field1 field2 field3 ASCENDING|DESCENDING.`
  - 指示符在 BY 的后面，则只是对这个指示符前面的字段起作用，其他的字段还是默认的方式排序







