---
title: " SALV CheckBox 设置 "
date: 2019-06-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 添加复选框功能

要获得可编辑的复选框，首先我们需要从 column 对象中获取显示内表中定义的 checkbox 字段列。

此后，我们需要使用方法 SET_CELL_TYPE 将单元格类型设置为 IF_SALV_C_CELL_TYPE => CHECKBOX_HOTSPOT。

更新复选框中的值，需要处理事件 ON_LINK_CLICK。当单击启用热点的复选框时，将触发 LINK_CLICK 事件。在事件处理程序方法中，需要更改复选框字段的值并调用 REFRESH 方法以刷新 ALV 上的值。

```ABAP
*&---------------------------------------------------------------------*
*& SALV Table, editable checkbox
*&---------------------------------------------------------------------*
REPORT  zsalv_editable_checkbox.

CLASS lcl_event_handler DEFINITION.
  PUBLIC SECTION.
    METHODS:
      on_link_click FOR EVENT link_click OF cl_salv_events_table
        IMPORTING row column.
ENDCLASS.                    "lcl_event_handler DEFINITION"

CLASS lcl_report DEFINITION.
  PUBLIC SECTION.
    TYPES: BEGIN OF str_vbak,
           vbeln TYPE vbak-vbeln,
           erdat TYPE erdat,
           auart TYPE auart,
           kunnr TYPE kunnr,
           check TYPE flag,  "Check box"
           END OF str_vbak.
    DATA: gt_vbak TYPE STANDARD TABLE OF str_vbak.
    "ALV reference"
    DATA: gr_table TYPE REF TO cl_salv_table.
    METHODS:
      get_data,
      generate_output.
ENDCLASS.                    "lcl_report DEFINITION"
*----------------------------------------------------------------------*
*       CLASS lcl_report IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_report IMPLEMENTATION.
  METHOD get_data.
    SELECT vbeln erdat auart kunnr
           INTO TABLE gt_vbak
           FROM vbak
           UP TO 20 ROWS.
  ENDMETHOD.                    "get_data"
  METHOD generate_output.
    DATA: lv_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = gr_table
          CHANGING
            t_table      = gt_vbak ).
      CATCH cx_salv_msg INTO lv_msg.
    ENDTRY.
    DATA: lr_columns TYPE REF TO cl_salv_columns_table.
    DATA: lr_column  TYPE REF TO cl_salv_column_table.
    lr_columns ?= gr_table->get_columns( ).
    lr_columns->set_optimize( 'X' ).
    "Change the properties of the Columns KUNNR"
    TRY.
        lr_column ?= lr_columns->get_column( 'CHECK' ).
        lr_column->set_cell_type( if_salv_c_cell_type=>checkbox_hotspot ).
        lr_column->set_output_length( 10 ).
      CATCH cx_salv_not_found.   "EC NO_HANDLER"
    ENDTRY.
    "Get the event object"
    DATA: lr_events TYPE REF TO cl_salv_events_table.
    lr_events = gr_table->get_event( ).
    "Instantiate the event handler object"
    DATA: lr_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lr_event_handler.
    "Event handler"
    SET HANDLER lr_event_handler->on_link_click FOR lr_events.
    "Displaying the ALV"
    gr_table->display( ).
  ENDMETHOD.               "generate_output"
ENDCLASS.                  "lcl_report IMPLEMENTATION"

START-OF-SELECTION.
  DATA: lo_report TYPE REF TO lcl_report.
  CREATE OBJECT lo_report.
  lo_report->get_data( ).
  lo_report->generate_output( ).

CLASS lcl_event_handler IMPLEMENTATION.
  METHOD on_link_click.
    "Get the value of the checkbox and set the value accordingly refersh the table"
    FIELD-SYMBOLS: <lfa_data> LIKE LINE OF lo_report->gt_vbak.
    READ TABLE lo_report->gt_vbak ASSIGNING <lfa_data> INDEX row.
    CHECK sy-subrc IS INITIAL.
    IF <lfa_data>-check IS INITIAL.
      <lfa_data>-check = 'X'.
    ELSE.
      CLEAR <lfa_data>-check.
    ENDIF.
    lo_report->gr_table->refresh( ).
  ENDMETHOD.                 "on_link_click"
ENDCLASS.                    "lcl_event_handler IMPLEMENTATION"
```

