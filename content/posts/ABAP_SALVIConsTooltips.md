---
title: " SALV 图标设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### 图标设置

为了能够在 SALV 上添加 ICON 和工具提示，需要：

- **列值作为 ICON 值–**在输出表中，需要添加 **ICON** 作为值。这样就可以将 ICON_GREEN_LIGHT 用作交通绿灯
- **将 SALV 的 ICON 列设置为 ICON –**从 SALV 获取 COLUMNS 对象，获取特定的列对象，调用方法 SET_ICON 将列注册为 ICON
- **设置工具提示–**工具提示对象是 “功能设置” 的一部分。首先获取功能对象。从中获取工具提示对象。调用方法 ADD_TOOLTIP 添加工具提示

```JS
REPORT  ZTEST_NP_SALV_ICON_TOOLTIP.
CLASS lcl_main DEFINITION.
  PUBLIC SECTION.
    DATA o_salv TYPE REF TO cl_salv_table .
    TYPES:
      BEGIN OF ty_output,
        status TYPE char10,
        field1 TYPE char30,
      END   OF ty_output.
    DATA: t_output TYPE STANDARD TABLE OF ty_output.
    METHODS:
      select_data,
      generate_alv.
ENDCLASS.                    "lcl_main DEFINITION
*
START-OF-SELECTION.
  DATA: o_main TYPE REF TO lcl_main.
  CREATE OBJECT o_main.
  o_main->select_data( ).
  o_main->generate_alv( ).
*
CLASS lcl_main IMPLEMENTATION.
  METHOD select_data.
    INCLUDE: <icon>.
    DATA: ls_output LIKE LINE OF t_output.
    DO 3 TIMES.
      ls_output-status = icon_green_light.
      ls_output-field1 = sy-abcde.
      APPEND ls_output TO t_output.
      ls_output-status = icon_yellow_light.
      APPEND ls_output TO t_output.
      ls_output-status = icon_red_light.
      APPEND ls_output TO t_output.
      ls_output-status = icon_led_green.
      APPEND ls_output TO t_output.
      ls_output-status = icon_led_red.
      APPEND ls_output TO t_output.
      ls_output-status =  icon_led_yellow.
      APPEND ls_output TO t_output.
    ENDDO.
  ENDMETHOD.                    "select_Data
  METHOD generate_alv.
    DATA: lo_functions            TYPE REF TO cl_salv_functions_list.
    DATA: lo_functional_settings  TYPE REF TO cl_salv_functional_settings.
    DATA: lo_tooltips             TYPE REF TO cl_salv_tooltips,
          lv_value                TYPE lvc_value.
    DATA: lo_columns              TYPE REF TO cl_salv_columns.
    DATA: lo_column               TYPE REF TO cl_salv_column_table.
 
    INCLUDE: <icon>.
* ALV Object
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = o_salv
          CHANGING
            t_table      = t_output ).
      CATCH cx_salv_msg.                                "#EC NO_HANDLER
    ENDTRY.
* Functions
    lo_functions = o_salv->get_functions( ).
    lo_functions->set_all( abap_true ).
* Set the columns
    lo_columns = o_salv->get_columns( ).
    "lo_columns->set_optimize( abap_true ).
    TRY.
        lo_column ?= lo_columns->get_column( 'STATUS' ).
        lo_column->set_icon( if_salv_c_bool_sap=>true ).
        lo_column->set_long_text( 'Hover for Tooltip' ).
        lo_column->set_alignment( if_salv_c_alignment=>centered ).
        lo_column->set_output_length( 20 ).
      CATCH cx_salv_not_found.                          "#EC NO_HANDLER
    ENDTRY.
* Tooltips
    lo_functional_settings = o_salv->get_functional_settings( ).
    lo_tooltips = lo_functional_settings->get_tooltips( ).
    TRY.
        lv_value = icon_green_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Everything is Processed' ).            "#EC NOTEXT
      CATCH cx_salv_existing.                           "#EC NO_HANDLER
    ENDTRY.
    TRY.
        lv_value = icon_yellow_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Partially processed' ).                "#EC NOTEXT
      CATCH cx_salv_existing.                           "#EC NO_HANDLER
    ENDTRY.
    TRY.
        lv_value = icon_red_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Nothing Yet processed' ).              "#EC NOTEXT
      CATCH cx_salv_existing.                           "#EC NO_HANDLER
    ENDTRY.
 
*... display the table
    o_salv->display( ).
  ENDMETHOD.                    "generate_alv
 
ENDCLASS.                    "lcl_main IMPLEMENTATION
```
