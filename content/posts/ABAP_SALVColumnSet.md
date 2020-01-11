---
title: " SALV Fieldcat设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### Fieldcat设置

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
*   Set the various column properties
    METHODS:
      set_columns
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
* Setting up the Columns
    CALL METHOD me->set_columns
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_columns.
*...Get all the Columns
    DATA: lo_cols TYPE REF TO cl_salv_columns.
    lo_cols = o_alv->get_columns( ).
*   set the Column optimization
    lo_cols->set_optimize( 'X' ).
*...Process individual columns
    DATA: lo_column TYPE REF TO cl_salv_column.
	DATA: g_color TYPE lvc_s_colo.
*   Change the properties of the Columns KUNNR
    TRY.
        lo_column = lo_cols->get_column( 'KUNNR' ). "需要处理的列
        lo_column->set_long_text( 'Sold-To Party' ).
        lo_column->set_medium_text( 'Sold-To Party' ).
        lo_column->set_short_text( 'Sold-To' ).
        lo_column->set_output_length( 10 ).
        "隐藏设置
        lo_column->set_visible( cl_salv_column_table=>false )."隐藏后在布局里可以看到
		lo_column->set_technical( 'X' ). "完全隐藏
 	    "颜色设置
         g_color-col = '6'.
         g_color-int = '1'.
         g_color-inv = '0'.
         lo_column->set_color( g_color ).
         ...
      CATCH cx_salv_not_found.                          "#EC NO_HANDLER
    ENDTRY.
  ENDMETHOD.                    "SET_COLUMNS
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

