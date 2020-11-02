---
title: " SAP系统间数据表的Copy "
date: 2020-10-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
---

### 需求场景

在功能开发完成后，需要使用一些测试数据来进行测试，有时可能需要将一些比较新的生产数据复制到测试系统，为了完成数据表的复制，需要使用一些SAP标准的BAPI来完成。

### 功能实现

####  BAPI:RFC_READ_TABLE

```html
DATA: SO_OPTION TYPE TABLE OF rfc_db_opt WITH HEADER LINE,
      SO_FIELDS TYPE TABLE OF rfc_db_fld,
      WA_FIELDS TYPE rfc_db_fld,
      SO_DATA TYPE TABLE OF tab512,
      WA_SO_DATA TYPE tab512.
DATA: T_FIELDS TYPE TABLE OF DDSHSELOPT WITH HEADER LINE.
DATA: LV_WHERE TYPE STRING.

PARAMETERS: p_table TYPE dd02l-tabname DEFAULT 'RSEG'.
PARAMETERS: p_dest TYPE rfcdes-rfcdest.

START-OF-SELECTION.
REFRESH:T_RSEG1,I_WHERE,I_FIELDS.

CLEAR T_FIELDS.
T_FIELDS-SHLPFIELD = 'BELNR'.
T_FIELDS-SIGN      = 'I'.
T_FIELDS-OPTION    = 'BT'.
T_FIELDS-LOW       = '5100000009'.
T_FIELDS-HIGH      = '5100000019'.
APPEND T_FIELDS.

* Convert selopt into string               *
CALL FUNCTION 'F4_CONV_SELOPT_TO_WHERECLAUSE'
*   EXPORTING
*     GEN_ALIAS_NAMES       = ' '
*     ESCAPE_ALLOWED        = ' '
  IMPORTING
    WHERE_CLAUSE          = LV_WHERE
  TABLES
    SELOPT_TAB            = T_FIELDS.

SO_OPTIONS-TEXT = LV_WHERE.
APPEND SO_OPTIONS.

REFRESH T_FIELDS.
CLEAR T_FIELDS.
T_FIELDS-SHLPFIELD = 'GJAHR'.
T_FIELDS-SIGN      = 'I'.
T_FIELDS-OPTION    = 'EQ'.
T_FIELDS-LOW       = '2020'.
APPEND T_FIELDS.

CLEAR LV_WHERE.
* Convert selopt into string               *
CALL FUNCTION 'F4_CONV_SELOPT_TO_WHERECLAUSE'
*   EXPORTING
*     GEN_ALIAS_NAMES       = ' '
*     ESCAPE_ALLOWED        = ' '
  IMPORTING
    WHERE_CLAUSE          = LV_WHERE
  TABLES
    SELOPT_TAB            = T_FIELDS.

CLEAR T_WHERE.
SO_OPTIONS-TEXT(3) = 'AND'.
SO_OPTIONS-TEXT+4  = LV_WHERE.
APPEND SO_OPTIONS.

CALL FUNCTION 'RFC_READ_TABLE' DESTINATION p_dest
  EXPORTING
    query_table                = p_table
*   DELIMITER                  = ' '
*   NO_DATA                    = ' '
*   ROWSKIPS                   = 0
*   ROWCOUNT                   = 0
  TABLES
    OPTIONS                    = SO_OPTIONS
    fields                     = SO_FIELDS
    data                       = SO_DATA
* EXCEPTIONS
*   TABLE_NOT_AVAILABLE        = 1
*   TABLE_WITHOUT_DATA         = 2
*   OPTION_NOT_VALID           = 3
*   FIELD_NOT_VALID            = 4
*   NOT_AUTHORIZED             = 5
*   DATA_BUFFER_EXCEEDED       = 6
*   OTHERS                     = 7
            .
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ELSE.
  MODIFY (p_table) FROM TABLE SO_DATA.
  IF sy-subrc = 0.
    COMMIT WORK AND WAIT.
  ENDIF.
ENDIF.
```

#### BAPI:RFC_READ_TABLE使用限制

- 行数限制：RFC_READ_TABLE的行数限制为512个字符；也就是说，每行数据不能超过 512 个字符
- OPTION 保留查询条件： 查询的长度限制为 75 个字符
- Float：RFC_READ_TABLE 不返回任何包含 Float 数据类型的字段
- 数据量太大：600,000 多个条目将导致 SYSTEM FAILURE 或 PAGE ALLOC FAILED 错误。

