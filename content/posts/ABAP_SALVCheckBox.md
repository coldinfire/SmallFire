---
title: " SALV CheckBox设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 添加复选框功能

​	要获得可选（可编辑）复选框，我们需要从 column 对象中获取特定的列。此后，我们需要使用方法 SET_CELL_TYPE 将单元格类型设置为 IF_SALV_C_CELL_TYPE => CHECKBOX_HOTSPOT。要更新复选框中的值，我们需要处理事件 LINK_CLICK。当我们单击启用热点的复选框时，将触发 LINK_CLICK 事件。在事件处理程序方法中，我们需要更改复选框字段的值并调用 REFRESH 方法以刷新 ALV 上的值。

```JS
*&---------------------------------------------------------------------*
*& SALV Table, editable checkbox
*&---------------------------------------------------------------------*
REPORT  zsalv_editable_checkbox.

CLASS lcl_report DEFINITION.
  PUBLIC SECTION.
*   Final output table
    TYPES: BEGIN OF ty_vbak,
           vbeln TYPE vbak-vbeln,
           erdat TYPE erdat,
           auart TYPE auart,
           kunnr TYPE kunnr,
           check TYPE flag,  "Check box
           END   OF ty_vbak.
    DATA: t_vbak TYPE STANDARD TABLE OF ty_vbak.
*   ALV reference
    DATA: o_alv TYPE REF TO cl_salv_table.

    METHODS:
      get_data,
      generate_output.
ENDCLASS.                    "lcl_report DEFINITION

CLASS lcl_event_handler DEFINITION.
  PUBLIC SECTION.
    METHODS:
      on_link_click FOR EVENT link_click OF cl_salv_events_table
        IMPORTING row column.
ENDCLASS.                    "lcl_event_handler DEFINITION

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
           INTO  TABLE t_vbak
           FROM  vbak
           UP TO 20 ROWS.
  ENDMETHOD.                    "get_data
  METHOD generate_output.
    DATA: lx_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = o_alv
          CHANGING
            t_table      = t_vbak ).
      CATCH cx_salv_msg INTO lx_msg.
    ENDTRY.
    DATA: lo_cols TYPE REF TO cl_salv_columns.
    lo_cols = o_alv->get_columns( ).
	" set the Column optimization
    lo_cols->set_optimize( 'X' ).
    DATA: lo_column TYPE REF TO cl_salv_column_list.
*   Change the properties of the Columns KUNNR
    TRY.
        lo_column ?= lo_cols->get_column( 'CHECK' ).
        lo_column->set_cell_type( if_salv_c_cell_type=>checkbox_hotspot ).
        lo_column->set_output_length( 10 ).
      CATCH cx_salv_not_found.                          "#EC NO_HANDLER
    ENDTRY.

*   Get the event object
    DATA: lo_events TYPE REF TO cl_salv_events_table.
    lo_events = o_alv->get_event( ).
*   Instantiate the event handler object
    DATA: lo_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lo_event_handler.
*   event handler
    SET HANDLER lo_event_handler->on_link_click FOR lo_events.
*   Displaying the ALV
    o_alv->display( ).
  ENDMETHOD.                    "generate_output
ENDCLASS.                    "lcl_report IMPLEMENTATION

*----------------------------------------------------------------------*
*       CLASS lcl_event_handler IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_event_handler IMPLEMENTATION.
  METHOD on_link_click.
*   Get the value of the checkbox and set the value accordingly
*   Refersh the table
    FIELD-SYMBOLS: <lfa_data> LIKE LINE OF lo_report->t_vbak.
    READ TABLE lo_report->t_vbak ASSIGNING <lfa_data> INDEX row.
    CHECK sy-subrc IS INITIAL.
    IF <lfa_data>-check IS INITIAL.
      <lfa_data>-check = 'X'.
    ELSE.
      CLEAR <lfa_data>-check.
    ENDIF.
    lo_report->o_alv->refresh( ).
  ENDMETHOD.                    "on_link_click
ENDCLASS.                    "lcl_event_handler IMPLEMENTATION
```