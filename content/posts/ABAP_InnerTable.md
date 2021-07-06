---
title: "ABAP 内表使用"
date: 2018-05-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### 内表定义

#### 工作区域

工作区域可以存放多个变量数据，相当于一维数组。

间接定义：通过 Type 定义结构类型，然后通过DATA赋值

```ABAP
TYPES: BEGIN OF str_order.
  aufnr  TYPE afko-aufnr,
  dauat  TYPE afpo-dauat,
 END OF str_order.
" TYPE定义的只是一个类型，不可以在程序中直接使用，必须用DATA赋值 "
DATA: lt_table TYPE TABLE OF str_order,
      ls_table TYPE str_order.
```

直接定义：直接使用 DATA 声明一个结构对象，可以在后续程序中直接使用该工作区

```ABAP
DATA: BEGIN OF str_matnr,   
  matnr TYPE matnr,
  werks TYPE bukrs,
END OF str_matnr.
```

参照DB或则结构创建工作区：`DATA <wa> TYPE <dbtab>|<str>.`

参照内表创建工作区：`DATA  <wa>  LIKE LINE OF <dbtab>.`

继承结构：结构复用，作用是将结构类型 structure_type 与结构变量 structure 的所有组件字段拷贝到当前结构定义的指定位置。


- `INCLUDE { {TYPE struc_type} | {STRUCTURE struc} }
          [AS name [RENAMING WITH SUFFIX suffix]]`.
      
- 结构对象复用
      
      
      - ```javascript
           DATA: BEGIN OF gt_result OCCURS 0,
             endcount TYPE zz_final_count,
             enddiffs TYPE zz_final_diffs. "直接定义组件字段，但前面语句后面使用逗号"
             INCLUDE STRUCTURE str_matnr.  "直接将结构对象包括进来,也可以是已经定义的结构"
             INCLUDE TYPE str_order.   "直接将结构类型包括进来"
             DATA:comm LIKE ztest_str. "直接参照"
           DATA: END OF gt_result.
           ```
      
- 结构类型复用


  - ```ABAP
    TYPES: BEGIN OF str_result,
      endcount TYPE zz_final_count,
      enddiffs TYPE zz_final_diffs. "直接定义字段，但是保留前面的逗号"
      INCLUDE STRUCTURE str_matnr.  "直接将结构对象包括进来"
      INCLUDE TYPE str_order.       "直接将结构类型包括进来"
      TYPES: uname type c,
        ustatus type c.
    TYPES: END OF str_pidoc.
    ```

#### 内表类型

- 索引内表：标准内表(Standard table)、排序表(Sorted table)


- 标准内表：每一行数据都有关键字和系统生成的逻辑索引。


- 排序表：排序表按照关键字升序排序后再进行存储。


- 哈希表：(Hashed table) 没有索引的表，只能靠关键字进行寻址，用哈希算法管理数据。

#### 内表定义

可以参考结构体、其他内表、透明表等方式定义内表。

参考结构：`DATA <table_name> TYPE STANDARD TABLE OF <structure> [WITH HEADER LINE].`  

参考内表：`DATA <table_name> TYPE TABLE OF <内表或透明表> [WITH HEADER LINE].`

   - WITH HEADER LINE ，用 itab[] 和 itab 来区分内表和工作区
   - 分别定义内表和工作区
   - 定义结构时用 OCCUR 0 直接定义
   - LIKE LINE OF 后面接一个内表，表示一个 data 参数具有和内表一样的结构，可当做 Work Area 使用
   - LIKE TABLE OF 后面接一个结构，表示一个 data 参数是一个内表，这个内表和后面接的结构一样

### 内表操作

#### 内表清空

- `CLEAR <ITAB>`：仅清空HEADER LINE，对内表数据存储空间不影响。

-    `REFRESH <ITAB>`：清空内表数据存储空间，对HEADER LINE不影响。


-    `REFRESH <itab> FROM TABLE <dbtab>`：清空内表存储空间，填充从数据库表所获数据。
  
-    `FREE <ITAB>`：清空内表数据存储空间，对HEADER LINE不影响。

#### APPEND(增加，内表赋值)

有HEADER LINE内表，数据被赋给内表HEADER LINE后再APPEND到表中最后一行。

- `APPEND ITAB.`    

对于没有HEADER LINE的内表，只能通表外部WORK AREA来传递数据。

- `APPEND (<work area> into) <ITAB>.`

#### INSERT(向内表插入数据)

- INSERT itab INDEX idx. :若itab有多行数据，将新记录新增到第一行


- INSERT wa INTO TABLE itab.   :将结构体中数据新增到内表


- INSERT LINES OF itab1 [FROM idx] [TO idx2] INTO itab2 [INDEX idx3].:将itab1指定范围数据插入到itab2

#### DELETE(删除数据)

- DELETE TABLE itab WITH TABLE KEY k1=v1...kn=vn.：按具体值删除。


- DELETE TABLE itab [FROM wa].：参照其它内表值删除。
- DELETE itab [FROM n1] [TO n2] [WHERE condition]. : 根据条件删除多条数据


- DELETE itab INDEX idx.：根据索引删除具体行数据。


- DELETE itab FROM idx1 TO idx2.：根据索引删除具体行数范围间数据。


- DELETE ADJACENT DUPLICATES FROM itab.：删除重复数据，执行此条件前必须先排序。

  - 删除重复数据，保留第一条。

#### MODIFY:(修改内表数据)

按内表位置或者具体内表字段值相等条件修改内表数据。

- MODIFY itab [FROM wa] [INDEX idx] [TRANSPORTING f1...fn] WHERE condition.

#### LOOP....ENDLOOP：（循环读取内表数据)

循环读取内表数据，在循环中使用系统变量SY-TABIX可获取当前所执行的行数。

- LOOP AT ITAB [INTO WA] FROM n1 TO n2.：读取内表具体行数间数据。


- LOOP AT ITAB [INTO WA] WHERE cond.：按具体字段条件读取内表。
- LOOP AT ITAB [ASSIGNING wa ] WHERE cond.：分配给字段符号。

#### AT...ENDAT（设置内表循环触发条件）

该语法为事件控制函数，应用于LOOP循环语句中，用于获取内表的数据变化事件。

Loop 的时候不能加条件；AT 和 ENDAT 之间不能使用 loop into 的 working area。

- AT NEW field.：当某个字段数据与上一行数据值不同时触发该事件。
  - 当 field 字段或者 field 字段左边的字段内容发生变化时该事件后面的语句都会执行
  - field 必须是内表的第一个字段
  - 内表中 field 之后的字段的值都会变成 " * "


- AT END OF field：当内表中某个字段当前行值与下一行值不同时触发该事件。


- AT FIRST.：当执行内表第一行时触发该事件。


- AT LAST.：当执行内表最后一行时触发该事件。

#### READ:(读取)

- READ TABLE itab [INTO wa] FROM wa.


- READ TABLE itab [INTO wa] WITH [TABLE] KEY k1=v1...kn=vn [BINARY SEARCH].

  - **二分法搜出来的数据是第一条符合条件的数据，必须先按关键字段排序**


- READ TABLE itab [INTO wa] INDEX i.

#### COLLECT（内表数据分类汇总）

 将内表中相同的字段合并，若有类型为`I`的字段，则将其值加总。

- COLLECT [wa INTO] itab.

#### DESCRIBE（获取内表具体属性）

- DESCRIBE TABLE itab LINES n：获取内表当前总行数，n为整型(i)。
- n = lines (itab). 

#### SORT（内表排序操作）

- SORT itab ASCENDING|DESCENDING BY field1 field2 field3.
  - 指示符在 BY 在前面，表示后面的字段都用这个升降序，作用范围是后面 BY 所有的字段
- SORT itab BY field1 field2 field3 ASCENDING|DESCENDING.
  - 指示符在 BY 的后面，则只是对这个指示符前面的字段起作用，其他的字段还是默认的方式排序







