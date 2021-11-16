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

- 整行：在记录中在需要设置 COLOR 的数据中添加详细信息

- 整列：从 COLUMNS 获得对应的列，设置特定列的颜色

- 单元格：在每条记录的 COLOR 表中添加有关字段和颜色的详细信息。

```ABAP
*&---------------------------------------------------------------------*
*& This code snippet will show how to use the CL_SALV_TABLE to
*&   generate the ALV and COLORs for Column, Row and Specific Cell
*&---------------------------------------------------------------------*
REPORT  ztest_oo_alv_color.
*----------------------------------------------------------------------*
*       CLASS lcl_report DEFINITION
*----------------------------------------------------------------------*
CLASS lcl_report DEFINITION.
  PUBLIC SECTION.
    TYPES: BEGIN OF str_vbak,
           vbeln     TYPE vbak-vbeln,
           erdat     TYPE erdat,
           auart     TYPE auart,
           kunnr     TYPE kunnr,
           t_color   TYPE lvc_t_scol,
           END OF str_vbak.
    TYPES: ty_t_vbak TYPE STANDARD TABLE OF str_vbak.
    DATA: gt_vbak TYPE STANDARD TABLE OF str_vbak.
    "ALV reference"
    DATA: go_alv TYPE REF TO cl_salv_table.
    METHODS:
      get_data,
      generate_output.
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_pf_status
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
    METHODS:set_colors
        CHANGING
          co_alv  TYPE REF TO cl_salv_table
          ct_vbak TYPE ty_t_vbak.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
ENDCLASS.                    "lcl_report DEFINITION"

START-OF-SELECTION.
  DATA: lo_report TYPE REF TO lcl_report.
  CREATE OBJECT lo_report.
  lo_report->get_data( ).
  lo_report->generate_output( ).

*----------------------------------------------------------------------*
*       CLASS lcl_report IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_report IMPLEMENTATION.
  METHOD get_data.
    SELECT vbeln erdat auart kunnr
      INTO  CORRESPONDING FIELDS OF TABLE gt_vbak
      FROM  vbak
      UP TO 20 ROWS.
  ENDMETHOD.                    "get_data"
  METHOD generate_output.
    DATA: lv_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = go_alv
          CHANGING
            t_table      = gt_vbak ).
      CATCH cx_salv_msg INTO lv_msg.
    ENDTRY.
    "Set default PF status"
    CALL METHOD set_pf_status
      CHANGING
        co_alv = go_alv.
    "Set the colors to ALV display"
    CALL METHOD set_colors
      CHANGING
        co_alv  = go_alv
        ct_vbak = gt_vbak.
  ENDMETHOD.                    "generate_output"
  METHOD set_pf_status.
    DATA: lo_functions TYPE REF TO cl_salv_functions_list.
    lo_functions = co_alv->get_functions( ).
    lo_functions->set_default( abap_true ).
  ENDMETHOD.                    "set_pf_status"
  METHOD set_colors.
    "Color for COLUMN"
    DATA: lo_cols TYPE REF TO cl_salv_columns_table,
          lo_column TYPE REF TO cl_salv_column_table.
    DATA: ls_color TYPE lvc_s_colo. "Colors strucutre"
    lo_cols ?= co_alv->get_columns( ).
    INCLUDE <color>.
    TRY.
        lo_column ?= lo_cols->get_column( 'ERDAT' ).
        ls_color-col = col_total.
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
        "Apply RED color to the AUART Cell of the 3rd Row"
        WHEN 3.
          ls_s_color-fname     = 'AUART'.
          ls_s_color-color-col = col_negative.
          ls_s_color-color-int = 0.
          ls_s_color-color-inv = 0.
          APPEND ls_s_color TO lt_s_color.
          CLEAR  ls_s_color.
        "Apply GREEN color to the entire row 5 For entire row"
        WHEN 5.
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
      CATCH cx_salv_data_error.                         "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "set_colors"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
ENDCLASS.                    "lcl_report IMPLEMENTATION"
```

