---
title: " SALV 创建 ALV "
date: 2019-06-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 创建和显示 ALV

```ABAP
*&---------------------------------------------------------------------*
*& This code snippet will show how to use the CL_SALV_TABLE to
*&   generate the ALV
*&---------------------------------------------------------------------*
REPORT  ztest_oo_alv_main.
*----------------------------------------------------------------------*
*       CLASS lcl_report DEFINITION
*----------------------------------------------------------------------*
CLASS lcl_report DEFINITION.
  PUBLIC SECTION.
    TYPES: BEGIN OF str_vbak,
           vbeln TYPE vbak-vbeln,
           erdat TYPE erdat,
           auart TYPE auart,
           kunnr TYPE kunnr,
           netwr TYPE netwr,
           check TYPE flag,   "复选框"
           t_color TYPE lvc_t_scol,"单元格颜色设置"
           t_celltype TYPE salv_t_int4_column,  "设置单元格style使用"
        END OF str_vbak.
    TYPES: ty_t_vbak TYPE STANDARD TABLE OF str_vbak.
    DATA: gt_vbak TYPE STANDARD TABLE OF str_vbak.
    "ALV reference"
    DATA: gr_table TYPE REF TO cl_salv_table.
    DATA: gr_container TYPE REF TO cl_gui_custom_container.
    "Methods Define"
    METHODS:
      get_data,        "Data prepare" 
      generate_output. "Generating output"

*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
*  In this section we will define the private methods which can be implemented
*    to set the properties of the ALV and can be called in the generate_output
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

ENDCLASS.                    "lcl_report DEFINITION"
 
*----------------------------------------------------------------------*
*       CLASS lcl_report IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_report IMPLEMENTATION.
  METHOD get_data.
    SELECT vbeln erdat auart kunnr
           INTO  TABLE gt_vbak
           FROM  vbak
           UP TO 20 ROWS.
  ENDMETHOD.                    "get_data"
  
  METHOD generate_output.
    "Calling the static Factory Method"
    DATA: lv_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          EXPORTING
            list_display = abap_ture  "以列表还是GUI形式控制"
          IMPORTING
            r_salv_table = gr_table   "用来接收工厂产生的实例"
          CHANGING
            t_table      = gt_vbak ).
      CATCH cx_salv_msg INTO lv_msg.
    ENDTRY.

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*    In this area we will call the methods which will set the
*      different properties to the ALV
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

    "Displaying the ALV"
    gr_table->display( ).
    gr_table->refresh( ).
  ENDMETHOD.                    "generate_output"

*$*$*.....CODE_ADD_2_1 - Begin..................................2_1..*$*$*
*    In this area we will define the methods which will set the
*      different properties to the ALV
*$*$*.....CODE_ADD_2_1 - End....................................2_1..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
*    In this area we will implement the methods which are defined in
*      the class definition
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*

ENDCLASS.                    "lcl_report IMPLEMENTATION"

START-OF-SELECTION.
  DATA: lo_report TYPE REF TO lcl_report.
  CREATE OBJECT lo_report.
  lo_report->get_data( ).
  lo_report->generate_output( ).
```

### CONTAINER 模式创建 SALV

```ABAP
*&---------------------------------------------------------------------*
REPORT  ztest_oo_alv_main.
  TYPES: BEGIN OF str_vbak,
         vbeln TYPE vbak-vbeln,
         erdat TYPE erdat,
         auart TYPE auart,
         kunnr TYPE kunnr,
         netwr TYPE netwr,
      END OF str_vbak.
  TYPES: ty_t_vbak   TYPE STANDARD TABLE OF str_vbak.
  DATA: gt_vbak      TYPE STANDARD TABLE OF str_vbak.
  DATA: gr_table     TYPE REF TO cl_salv_table.
  DATA: gr_functions TYPE REF TO cl_salv_functions_list.
  DATA: gr_container TYPE REF TO cl_gui_custom_container.
  DATA: ok_code TYPE syucomm.
  
START-OF-SELECTION.
  CALL SCREEN 1000. "Screen Number"

MODULE 1000_pbo OUTPUT.
    SELECT vbeln erdat auart kunnr
      INTO  TABLE gt_vbak
      FROM  vbak
      UP TO 20 ROWS.
  "判断是否已分配了一个有效引用"
  IF gr_container IS NOT BOUND.
    "创建容器"
    CREATE OBJECT gr_container
      EXPORTING
        container_name = 'CONTAINER_1'. " 屏幕上用户自定义控件名 "
    "创建ALV"
    cl_salv_table=>factory(
      EXPORTING
          r_container = gr_container
          container_name = 'CONTAINER_1'
      IMPORTING
          r_salv_table = gr_table
      CHANGING
          t_table = gt_data[] ).
    "激活所有的ALV内置通用按钮"
    gr_functions = gr_table->get_functions( ).
    gr_functions->set_all( abap_true ).  
    "显示ALV"
    gr_table->display( ).
  ENDIF.
ENDMODULE.
```

### 以弹出框显示 SALV

```ABAP
DATA: gr_table TYPE REF TO cl_salv_table.
cl_salv_table=>factory(
  IMPORTING 
    r_salv_table = gr_table
  CHANGING 
    t_table = gt_data[] ).
"设置弹出框属性"
gr_table->set_screen_popup(
  start_column = 1
  end_column = 50
  start_line = 1
  end_line = 5 ).
"Display"
gr_table->display( ).
```

