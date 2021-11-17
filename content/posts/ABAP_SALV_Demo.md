---
title: " SALV ALV Demo"
date: 2019-06-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### SALV 框架代码

![SALV Demo](/images/ABAP/SALV13.png)

```ABAP
*&---------------------------------------------------------------------*
*& Report  ZSALV_DEMO
*&---------------------------------------------------------------------*
REPORT  zsalv_demo.
TABLES: mara,marc,rlgrap.
TYPES: BEGIN OF str_demo,
  matnr TYPE mara-matnr,
  werks TYPE marc-werks,
  dismm TYPE marc-dismm,
  dispo TYPE marc-dispo,
END OF str_demo.
DATA: gt_alv TYPE STANDARD TABLE OF str_demo,
      gs_alv TYPE str_demo.
"SALV Data"
DATA: list_display TYPE sap_bool.
DATA: go_container TYPE REF TO cl_gui_custom_container.
DATA: go_alv       TYPE REF TO cl_salv_table.
DATA: go_error     TYPE REF TO cx_salv_error.
DATA: go_message   TYPE REF TO cx_salv_msg.
"Selection Screen"
SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
PARAMETERS: p_werks TYPE marc-werks .
SELECT-OPTIONS: s_matnr FOR mara-matnr .
SELECTION-SCREEN END OF BLOCK blk1 .
"Event Handle"
*----------------------------------------------------------------------*
*       CLASS lcl_handle_events DEFINITION
*----------------------------------------------------------------------*
CLASS lcl_handle_events DEFINITION.
  PUBLIC SECTION.
    METHODS:
      on_user_command FOR EVENT added_function OF cl_salv_events
         IMPORTING e_salv_function,
      on_before_salv_function FOR EVENT before_salv_function OF cl_salv_events
         IMPORTING e_salv_function,
      on_after_salv_function FOR EVENT after_salv_function OF cl_salv_events
         IMPORTING e_salv_function,
      on_double_click FOR EVENT double_click OF cl_salv_events_table
         IMPORTING row column,
      on_link_click FOR EVENT link_click OF cl_salv_events_table
         IMPORTING row column.
ENDCLASS.             " lcl_handle_events DEFINITION "
*----------------------------------------------------------------------*
*       CLASS lcl_handle_events IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_handle_events IMPLEMENTATION.
  METHOD on_user_command.
    DATA: lv_flag TYPE char1.
    DATA: lo_selections TYPE REF TO cl_salv_selections.
    DATA: lt_selected_rows TYPE salv_t_row,
          ls_selected_row  LIKE LINE OF lt_selected_rows.
    lo_selections = go_alv->get_selections( ) .
    lt_selected_rows  = lo_selections->get_selected_rows( ).
    CASE e_salv_function.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.                    "on_user_command"
  METHOD on_before_salv_function.
  ENDMETHOD.                    "on_before_salv_function"
  METHOD on_after_salv_function.
  ENDMETHOD.                    "on_after_salv_function"
  METHOD on_double_click.
  ENDMETHOD.                    "on_double_click"
  METHOD on_link_click.
  ENDMETHOD.                    "on_single_click"
ENDCLASS.            " lcl_handle_events IMPLEMENTATION "

START-OF-SELECTION.
  PERFORM get_data.
  PERFORM process_data.
  PERFORM show_alv USING gt_alv.
*&---------------------------------------------------------------------*
*&      Form  GET_DATA
*&---------------------------------------------------------------------*
FORM get_data .
  CLEAR gt_alv.
  SELECT *
    FROM mara INNER JOIN marc ON mara~matnr = marc~matnr
    INTO CORRESPONDING FIELDS OF TABLE gt_alv
   WHERE mara~matnr IN s_matnr
     AND werks EQ p_werks.
  IF gt_alv IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
ENDFORM.                    " GET_DATA "
*&---------------------------------------------------------------------*
*&      Form  PROCESS_DATA
*&---------------------------------------------------------------------*
FORM process_data .
ENDFORM.                    " PROCESS_DATA"
*&---------------------------------------------------------------------*
*&      Form  show_alv
*&---------------------------------------------------------------------*
FORM show_alv USING gt_output.
  DATA: lo_functions TYPE REF TO cl_salv_functions_list.
  TRY .
      cl_salv_table=>factory(
        EXPORTING
          list_display   = list_display
         "R_CONTAINER    = "
         "CONTAINER_NAME = "
        IMPORTING
          r_salv_table   = go_alv
        CHANGING
          t_table        = gt_output
     ).
    CATCH cx_salv_msg INTO go_message.
  ENDTRY.
  "set default set of generic functions"
  lo_functions = go_alv->get_functions( ).
  lo_functions->set_all( if_salv_c_bool_sap=>true ).
  "define settings"
  PERFORM define_settings USING go_alv.
  "display ALV"
  go_alv->display( ).
ENDFORM.                    " show_alv "
*&---------------------------------------------------------------------*
*&      Form  define_settings
*&---------------------------------------------------------------------*
FORM define_settings USING co_alv TYPE REF TO cl_salv_table.
  PERFORM: set_display USING co_alv,
           set_columns USING co_alv,
           set_sorts USING co_alv,
           set_aggregs USING co_alv,
           set_filter USING co_alv,
           set_layout USING co_alv
           set_handler USING co_alv.
ENDFORM.                    " define_settings "
*&---------------------------------------------------------------------*
*&      Form  set_display
*&---------------------------------------------------------------------*
FORM set_display USING p_alv TYPE REF TO cl_salv_table.
  DATA: lo_display TYPE REF TO cl_salv_display_settings,
        lo_selections TYPE REF TO cl_salv_selections,
        lv_title   TYPE lvc_title.
  "get display settings object"
  lo_display = p_alv->get_display_settings( ).
  "set header"
  lv_title = 'SALV Demo'.
  lo_display->set_list_header( value = lv_title ).
  "set horizontal lines off"
  lo_display->set_horizontal_lines( value = ' ' ).
  "set striped pattern"
  lo_display->set_striped_pattern( value = 'X' ).
  "set the selection mode"
  lo_selections = p_alv->get_selections( ).
  lo_selections->set_selection_mode( value  = if_salv_c_selection_mode=>cell ).
ENDFORM.                    " set_display "
*&---------------------------------------------------------------------*
*&      Form  set_columns
*&---------------------------------------------------------------------*
FORM set_columns USING p_alv TYPE REF TO cl_salv_table.
  DATA: lo_columns TYPE REF TO cl_salv_columns_table.
  DATA: lo_column  TYPE REF TO cl_salv_column.
  "help field for tooltip"
  DATA: l_lvc_tip TYPE lvc_tip.
  "help field for column position"
  DATA: l_pos TYPE i.
  lo_columns = p_alv->get_columns( ).
  "optimize columns' width"
  lo_columns->set_optimize( 'X' ).
  "fix key fields"
  lo_columns->set_key_fixation( if_salv_c_bool_sap=>true ).
  "Column setting"
  lo_column ?= lo_columns->get_column( 'MATNR' ).
  lo_column->set_short_text( 'Material' ).
  lo_column->set_medium_text( 'Material' ).
  lo_column->set_long_text( 'Material' ).

  lo_column ?= lo_columns->get_column( 'WERKS' ).
  lo_column->set_short_text( 'Plant' ).
  lo_column->set_medium_text( 'Plant' ).
  lo_column->set_long_text( 'Plant' ).
ENDFORM.                    " set_columns "
*&---------------------------------------------------------------------*
*&      Form  set_sorts
*&---------------------------------------------------------------------*
FORM set_sorts  USING  p_alv TYPE REF TO cl_salv_table.
  DATA: lo_sorts TYPE REF TO cl_salv_sorts.
  lo_sorts = p_alv->get_sorts( ).
ENDFORM.                    " set_sorts "
*&---------------------------------------------------------------------*
*&      Form  set_aggregs
*&---------------------------------------------------------------------*
FORM set_aggregs  USING p_alv TYPE REF TO cl_salv_table.
  DATA: lo_aggregs TYPE REF TO cl_salv_aggregations.
  lo_aggregs = p_alv->get_aggregations( ).
ENDFORM.                    " set_aggregs "
*&---------------------------------------------------------------------*
*&      Form  set_filter
*&---------------------------------------------------------------------*
FORM set_filter USING  p_alv TYPE REF TO cl_salv_table.
  DATA: lo_filter TYPE REF TO cl_salv_filters.
  lo_filter = p_alv->get_filters( ).
ENDFORM.                    " set_sorts "
*&---------------------------------------------------------------------*
*&      Form  set_layout
*&---------------------------------------------------------------------*
FORM set_layout USING p_alv TYPE REF TO cl_salv_table.
  DATA: lo_layout TYPE REF TO cl_salv_layout,
        ls_key    TYPE salv_s_layout_key.
  lo_layout = p_alv->get_layout( ).
  "set the layout key"
  ls_key-report = sy-cprog.
  lo_layout->set_key( value = ls_key ).
  lo_layout->set_save_restriction( if_salv_c_layout=>restrict_none ).
  "allow setting a default layout"
  lo_layout->set_default( value = 'X' ).
  "Customized Toolbar"
  CALL METHOD p_alv->set_screen_status
    EXPORTING
      pfstatus = 'SALV_TABLE_STANDARD'
      report   = sy-repid.
ENDFORM.                    " set_layout "
*&---------------------------------------------------------------------*
*&      Form  SET_HANDLER
*&---------------------------------------------------------------------*
FORM set_handler  USING  p_alv TYPE REF TO cl_salv_table.
  DATA: lo_events TYPE REF TO cl_salv_events_table.
  DATA: lo_handle_event TYPE REF TO lcl_handle_events.
  lo_events = p_alv->get_event( ).
  CREATE OBJECT lo_handle_event.
  SET HANDLER lo_handle_event->on_user_command FOR lo_events.
  SET HANDLER lo_handle_event->on_before_salv_function FOR lo_events.
  SET HANDLER lo_handle_event->on_after_salv_function FOR lo_events.
  SET HANDLER lo_handle_event->on_double_click FOR lo_events.
  SET HANDLER lo_handle_event->on_link_click FOR lo_events.
ENDFORM.                    " set_handler "
```

