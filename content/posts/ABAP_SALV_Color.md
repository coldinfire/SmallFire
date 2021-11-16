---
title: " SALV Color 设置 "
date: 2019-06-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 

---

### ALV颜色设置

#### 单元格颜色

为特定的单元格设置颜色，这需要在 ALV 输出内表中添加一个专门保存颜色的字段，类型为 LVC_T_SCOL。设置完颜色后（包括列名字，行号码），通过调用方法 set_color_column() 将颜色字段传递给 SALV。

#### 行颜色

与单元格颜色设置方法类似，只是不用指定列名字，只要指定行号就可以了。

#### 列颜色

从 COLUMNS 获得对应的列，通过方法 set_color() 设置特定列的颜色。

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_color
        CHANGING
          co_alv  TYPE REF TO cl_salv_table
          ct_vbak TYPE ty_t_vbak.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    "Set the colors to ALV display"
    CALL METHOD set_color
      CHANGING
        co_alv  = go_alv
        ct_vbak = gt_vbak.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_color.
    "Color for COLUMN"
    DATA: lo_cols TYPE REF TO cl_salv_columns_table,
          lo_column TYPE REF TO cl_salv_column_table.
    DATA: ls_color TYPE lvc_s_colo. "Colors strucutre"
    INCLUDE <color>. 
    lo_cols = co_alv->get_columns( ).
    "列颜色设置"
    TRY.
        lo_column ?= lo_cols->get_column( 'ERDAT' ).
        ls_color-col = col_total. "color"
        lo_column->set_color( ls_color ).
      CATCH cx_salv_not_found.
    ENDTRY.
    "Color for Specific Cell & Rows"
    DATA: lt_s_color TYPE lvc_t_scol, 
          ls_s_color TYPE lvc_s_scol,
          ls_vbak    LIKE LINE OF ct_vbak,
          l_count    TYPE i.
    LOOP AT ct_vbak INTO ls_vbak.
      l_count = l_count + 1.
      CASE l_count.
        WHEN 3.
          "Apply RED color to the AUART Cell of the 3rd Row"
          ls_s_color-fname     = 'AUART'.
          ls_s_color-color-col = col_negative.
          ls_s_color-color-int = 0.
          ls_s_color-color-inv = 0.
          APPEND ls_s_color TO lt_s_color.
          CLEAR  ls_s_color.
        WHEN 5.
          "Apply GREEN color to the entire row 5 For entire row"
          ls_s_color-color-col = col_positive.
          ls_s_color-color-int = 0.
          ls_s_color-color-inv = 0.
          APPEND ls_s_color TO lt_s_color.
          CLEAR  ls_s_color.
      ENDCASE.
      "Modify that data back to the output table"
      ls_vbak-t_color = lt_s_color.
      MODIFY ct_vbak FROM ls_vbak.
      CLEAR  ls_vbak.
      CLEAR  lt_s_color.
    ENDLOOP.
    "Set this COLOR table field name of the internal table to" 
    "COLUMNS tab reference for the specific colors"
    TRY.
        lo_cols->set_color_column( 'T_COLOR' ).
      CATCH cx_salv_data_error. 
    ENDTRY.
  ENDMETHOD.                    "set_colors"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

