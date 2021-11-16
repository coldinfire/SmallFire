---
title: " SALV 字段目录设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Fieldcat 设置

Function ALV、OO ALV 可通过 Fieldcat 对列进行相关设置，SALV 也有类似于这样的东西，只不过不是 Fieldcat，而是通过 `CL_SALV_COLUMN` 对象来实现的。

![CL_SALV_COLUMN](/images/ABAP/SALV5.png)

#### CL_SALV_COLUMNS_TABLE

![CL_SALV_COLUMNS_TABLE](/images/ABAP/SALV6.png)

#### CL_SALV_COLUMN_TABLE

![CL_SALV_COLUMN_TABLE](/images/ABAP/SALV7.png)

#### 示例代码

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
*Set the various column properties
    METHODS:set_columns
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*Setting up the Columns
    CALL METHOD me->set_columns
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_columns.
    DATA: lo_cols TYPE REF TO cl_salv_columns_table.
    DATA: lo_column TYPE REF TO cl_salv_column_table.
    "Get all the Columns"
    lo_cols = go_alv->get_columns( ).
    "Set the Column optimization"
    lo_cols->set_optimize( 'X' ).
    "Process individual columns"
    DATA: ls_color TYPE lvc_s_colo. "颜色设置"
    "Change the properties of the Columns KUNNR"
    TRY.
        lo_column ?= lo_cols->get_column( 'KUNNR' ). "需要处理的列"
        lo_column->set_long_text( 'Sold-To Party' ).
        lo_column->set_medium_text( 'Sold-To Party' ).
        lo_column->set_short_text( 'Sold-To' ).
        lo_column->set_output_length( 10 ).
        "隐藏设置"
        lo_column->set_visible( cl_salv_column_table=>false ). "隐藏在Layout里"
        lo_column->set_technical( 'X' ).   "完全隐藏"
        "颜色设置"
        CLEAR ls_color.
        ls_color-col = '6'.
        ls_color-int = '1'.
        ls_color-inv = '0'.
        lo_column->set_color( ls_color ).
        "数值为空时，不显示0"
        lo_column ?= lo_cols->get_column( 'BET01' ).
        lo_column->set_zero( ' ' ).
        "热点列设置"
        lo_column ?= lo_cols->get_column( 'AUFNR' ).
        lo_column->set_cell_type( if_salv_c_cell_type=>hotspot ).
        ...
      CATCH cx_salv_not_found.  "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "SET_COLUMNS
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

