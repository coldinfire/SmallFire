---
title: "ABAP 数据类型"
date: 2018-05-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### ABAP基本数据类型

#### 基本数据类型

| Type | Description                | Type | Description    |
| ---- | -------------------------- | ---- | -------------- |
| C    | Character text             | D    | Date(YYYYMMDD) |
| N    | Numeric text(不能进行计算) | T    | Time(HHMMSS)   |
| I    | Integer                    | F    | Floating point |
| P    | Packed(包类型:1-16)        | X    | 十六进制       |

#### 常用系统变量

| Value    | Description            | Value    | Description          |
| -------- | ---------------------- | -------- | -------------------- |
| SY-UNAME | 用户登录名             | SY-TCODE | 当前执行的TCode      |
| SY-SUBRC | 表示系统执行成功与否   | SY-TMAXL | 内表总行数           |
| SY-CPROG | 当前程序名             | SY-BATCH | 程序是否后台JOB执行  |
| SY-DATUM | 当前系统日期           | SY-HOST  | 服务器名称           |
| SY-UZEIT | 当前系统时间           | SY-DYNNR | 屏幕的编号           |
| SY-INDEX | DO-ENDDO 中是有效的    | SY-DBCNT | DB操作处理过的表行号 |
| SY-TABIX | LOOP索引，Read内表索引 | SY-MSGV1 | Message Variable     |
| SY-MSGID | Message Class          | SY-MSGV2 | Message Variable     |
| SY-MSGTY | Message Type           | SY-MSGV3 | Message Variable     |
| SY-MSGNO | Message Number         | SY-MSGV4 | Message Variable     |

### 变量的声明

#### 自定义变量类型

`DATA <var>(len) TYPE <type> VALUE <value> [<decimals>]`

#### 参考字段定义变量

`DATA <var1> like <var2>`  & `DATA <var1> TYPE <var2>`

- 透明表、结构、数据字典：既是类型又是对象，可用 type 和 like 。
- 只能使用 LIKE 引用另一自定义变量的类型，不可以使用 TYPE

#### 继承结构

```JS
DATA:BEGIN OF STAFFINFO.
    INCLUDE STRUCTURE USER_INFO.
 DATA:BIRTHDAY TYPE D,
    ADDRESS(50) TYPE C,
END OF STAFFINFO.

TYPES: BEGIN OF STR_DATA,
    BIRTHDAY TYPE D.
    INCLUDE STRUCTURE USER_INFO.
TYPES: END OF STR_DATA.
```

### 定义常量、宏

**常量定义**

 `CONSTANTS <var>(len) TYPE <type> VALUE <value>.`

`CONSTANTS <var>(len) LIKE <var2> VALUE <value>.`

**宏定义** 

```ABAP
DEFINE alv_ref1.
  clear gw_field.
  gw_field-fieldname = &1.
  gw_field-coltext  = &2.
  gw_field-outputlen = &3.
  gw_field-ref_field = &4.
  gw_field-ref_table = &5.
  gw_field-edit = &6.
  gw_field-f4availabl = &7.
append gw_field to gt_field.
END-OF-DEFINITION.
```
使用：`alv_ref1 'para1' 'para2' ... 'paran'.`

###  DESCRIBE使用

`DESCRIBE TABLE lt_mat LINES lv_cont.`：这行的意思是 计算内表 lt_mat 的行数 ，将行数放到变量 lv_cont 里。

字段属性：`DESCRIBE FIELD <field> [mes var]...
 `     (一个data的类型、长度、小数点、输出长度等信息)

内表：`DESCRIBE TABLE itab [kind knd] [LINES lin] [COCCURS n].`

两个字段不同：`DESCRIBE distance ...`







