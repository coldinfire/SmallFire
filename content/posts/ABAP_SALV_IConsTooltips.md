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

为了能够在 SALV 上添加 ICON 和工具提示，需要做以下内容：

- **列值作为 ICON 值**： 在输出表中，需要添加 **ICON** 作为值。这样就可以将 ICON_GREEN_LIGHT 用作交通绿灯
- **将 SALV 的 ICON 列设置为 ICON**： 从 SALV 获取 COLUMNS 对象，获取特定的列对象，调用方法 SET_ICON 将列注册为 ICON
- **设置工具提示**： 工具提示对象是 “功能设置” 的一部分。首先获取功能对象。从中获取工具提示对象。调用方法 ADD_TOOLTIP 添加工具提示

```ABAP
REPORT  ZTEST_NP_SALV_ICON_TOOLTIP.
CLASS lcl_main DEFINITION.
  PUBLIC SECTION.
    DATA go_alv TYPE REF TO cl_salv_table .
    TYPES:
      BEGIN OF str_output,
        status TYPE icon_d,
        field1 TYPE char30,
      END OF str_output.
    DATA: gt_output TYPE STANDARD TABLE OF str_output.
    METHODS:
      select_data,
      generate_alv.
ENDCLASS.                    "lcl_main DEFINITION"

START-OF-SELECTION.
  DATA: lo_main TYPE REF TO lcl_main.
  CREATE OBJECT lo_main.
  lo_main->select_data( ).
  lo_main->generate_alv( ).

CLASS lcl_main IMPLEMENTATION.
  METHOD select_data.
    INCLUDE: <icon>.
    DATA: ls_output LIKE LINE OF gt_output.
    DO 3 TIMES.
      ls_output-status = icon_green_light.
      ls_output-field1 = sy-abcde.
      APPEND ls_output TO gt_output.
      ls_output-status = icon_yellow_light.
      APPEND ls_output TO gt_output.
      ls_output-status = icon_red_light.
      APPEND ls_output TO gt_output.
      ls_output-status = icon_led_green.
      APPEND ls_output TO gt_output.
      ls_output-status = icon_led_red.
      APPEND ls_output TO gt_output.
      ls_output-status = icon_led_yellow.
      APPEND ls_output TO gt_output.
    ENDDO.
  ENDMETHOD.                    "select_Data"
  METHOD generate_alv.
    DATA: lo_functions            TYPE REF TO cl_salv_functions_list.
    DATA: lo_functional_settings  TYPE REF TO cl_salv_functional_settings.
    DATA: lo_columns              TYPE REF TO cl_salv_columns.
    DATA: lo_column               TYPE REF TO cl_salv_column_table.
    DATA: lo_tooltips             TYPE REF TO cl_salv_tooltips,
          lv_value                TYPE lvc_value.
    INCLUDE: <icon>.
    "ALV Object"
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = go_alv
          CHANGING
            t_table      = gt_output ).
      CATCH cx_salv_msg.    "#EC NO_HANDLER"
    ENDTRY.
    "Functions"
    lo_functions = go_alv->get_functions( ).
    lo_functions->set_all( abap_true ).
    "Set the columns"
    lo_columns = go_alv->get_columns( ).
    lo_columns->set_optimize( abap_true ).
    TRY.
        lo_column ?= lo_columns->get_column( 'STATUS' ).
        lo_column->set_icon( if_salv_c_bool_sap=>true ).
        lo_column->set_long_text( 'Status' ).
        lo_column->set_alignment( if_salv_c_alignment=>centered ).
        lo_column->set_output_length( 20 ).
        lo_column ?= lo_columns->get_column( 'FIELD1' ).
        lo_column->set_long_text( 'Message' ).
      CATCH cx_salv_not_found.                          
    ENDTRY.
    "Tooltips:为异常列单无格不同的值添加不同的冒泡提示"
    lo_functional_settings = go_alv->get_functional_settings( ).
    lo_tooltips = lo_functional_settings->get_tooltips( ).
    TRY.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = space    "不同的图形不同的冒泡提示"
          tooltip = 'Everything is Processed' ).        
      CATCH cx_salv_existing.                           
    ENDTRY.
    TRY.
        lv_value = icon_green_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Everything is Processed' ).        
      CATCH cx_salv_existing.                           
    ENDTRY.
    TRY.
        lv_value = icon_yellow_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Partially processed' ).            
      CATCH cx_salv_existing.                           
    ENDTRY.
    TRY.
        lv_value = icon_red_light.
        lo_tooltips->add_tooltip(
          TYPE    = cl_salv_tooltip=>c_type_icon
          VALUE   = lv_value
          tooltip = 'Nothing Yet processed' ).          
      CATCH cx_salv_existing.                           
    ENDTRY.
    "Display the table"
    go_salv->display( ).
  ENDMETHOD.                    "generate_alv"
ENDCLASS.                    "lcl_main IMPLEMENTATION"
```
