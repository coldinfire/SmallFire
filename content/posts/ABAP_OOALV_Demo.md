---
title: "OO ALV Demo"
date: 2018-07-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### OO ALV 简单实例

其中屏幕的创建， 和对应PAI、PBO 以及 Status title 等可以自定调整。

```ABAP
REPORT  zoo_alv_demo.
TYPE-POOLS: slis.
" Global data Decleration. "
TYPES:BEGIN OF str_output,
      ebeln TYPE ebeln,
      aedat TYPE erdat,
      ernam TYPE ernam,
      ebelp TYPE ebelp,
      matnr TYPE matnr,
      werks TYPE werks_d,
      menge TYPE bstmg,
      meins TYPE bstme,
      netwr TYPE bwert,
      END OF str_output.
DATA: gt_ekko TYPE TABLE OF ekko,
      gs_ekko TYPE ekko,
      gt_ekpo TYPE TABLE OF ekpo,
      gs_ekpo TYPE ekpo,
      gt_output TYPE TABLE OF str_output,
      gs_output TYPE str_output.
"Global Data Definitions for ALV"
"$. Region ALV_Data"
DATA go_container TYPE REF TO cl_gui_custom_container.
DATA go_grid      TYPE REF TO cl_gui_alv_grid.        
DATA gt_fieldcat  TYPE lvc_t_fcat.   
DATA gs_fieldcat  TYPE lvc_s_fcat.
DATA layout       TYPE lvc_s_layo.   
DATA gt_exclude   TYPE ui_functions. "Button Exclude"
DATA gt_variant   TYPE disvariant.   "Variant"
DATA gt_sort      TYPE lvc_t_sort.   "Sotrt table"
DATA gt_filt      TYPE lvc_t_filt.   "Filter table"
DATA gt_select    TYPE lvc_t_cell.   "选中单元格方法参数"
DATA gt_selrow    TYPE lvc_t_row.    "选中行方法参数"
"MARCO"
DEFINE mrc_fieldcat.
  clear: gs_fieldcat.
  gs_fieldcat-fieldname = &1.
  gs_fieldcat-seltext   = &2.
  gs_fieldcat-col_pos   = &3.
  gs_fieldcat-outputlen = &4.
  append gs_fieldcat TO gt_fieldcat.
END-OF-DEFINITION.
"Event "
CLASS cl_event_receiver DEFINITION DEFERRED.
DATA event_receiver TYPE REF TO cl_event_receiver.
"Select Screen"
START-OF-SELECTION.
  PERFORM get_data.
  IF gt_output IS NOT INITIAL.
    CALL SCREEN 100.
  ENDIF.
*&---------------------------------------------------------------------*
*&      Form  GET_DATA
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM get_data .
  CLEAR gt_ekko.
  SELECT * FROM ekko INTO TABLE gt_ekko UP TO 20 ROWS.
  IF gt_ekko IS NOT INITIAL.
    CLEAR gt_ekpo
    SELECT * FROM ekpo INTO TABLE gt_ekpo
       FOR ALL ENTRIES IN gt_ekko
       WHERE ebeln = gt_ekko-ebeln.
  ENDIF.
  LOOP AT gt_ekpo INTO gs_ekpo.
    gs_output-ebeln = gs_ekpo-ebeln.
    gs_output-ebelp = gs_ekpo-ebelp.
    gs_output-matnr = gs_ekpo-matnr.
    gs_output-werks = gs_ekpo-werks.
    gs_output-menge = gs_ekpo-menge.
    gs_output-meins = gs_ekpo-meins.
    gs_output-netwr = gs_ekpo-netwr.
    READ TABLE gt_ekko INTO gs_ekko WITH KEY ebeln = gs_ekpo-ebeln.
    IF sy-subrc IS INITIAL.
      gs_output-aedat = gs_ekko-aedat.
      gs_output-ernam = gs_ekko-ernam.
    ENDIF.
    APPEND gs_output TO gt_output.
    CLEAR: gs_output,gs_ekpo,gs_ekko.
  ENDLOOP.
ENDFORM.                    " GET_DATA "
*&---------------------------------------------------------------------*
*&      Module  STATUS_0100  OUTPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'STATS_100'.
  SET TITLEBAR  'TITLE_100'.
  PERFORM prepare_field_catalog CHANGING gt_fieldcat.
  PERFORM prepare_layout CHANGING layout.
  IF go_grid IS INITIAL .
*----Creating custom container instance
    CREATE OBJECT go_container
      EXPORTING
        container_name              = 'CONTAINER'
      EXCEPTIONS
        cntl_error                  = 1
        cntl_system_error           = 2
        create_error                = 3
        lifetime_error              = 4
        lifetime_dynpro_dynpro_link = 5
        OTHERS                      = 6.
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
*----Creating alv grid instance
    CREATE OBJECT go_grid
      EXPORTING
        i_parent          = go_container
      EXCEPTIONS
        error_cntl_create = 1
        error_cntl_init   = 2
        error_cntl_link   = 3
        error_dp_create   = 4
        OTHERS            = 5.
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
        WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
*----Here will be additional preparations
*--e.g. initial sorting criteria, initial filtering criteria, excluding functions
*----Display ALV 
    CALL METHOD go_grid->set_table_for_first_display
      EXPORTING
*      i_buffer_active               =
*      i_bypassing_buffer            =
*      i_consistency_check           =
*      i_structure_name              =      
          "Name of the DDIC structure, the catalog is generated automatically (Have priority)"
*      is_variant                    =
        i_save                        = 'A'
        i_default                     = 'X'
        is_layout                     = layout
*      is_print                      =
*      it_special_groups             =
*      it_toolbar_excluding          =
*      it_hyperlink                  =
*      it_alv_graphics               =
*      it_except_qinfo               =
*      ir_salv_adapter               =
      CHANGING
        it_outtab                     = gt_output
        it_fieldcatalog               = gt_fieldcat
*      it_sort                       =
*      it_filter                     =
      EXCEPTIONS
        invalid_parameter_combination = 1
        program_error                 = 2
        too_many_lines                = 3
        OTHERS                        = 4.
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
        WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
  ELSE .
    CALL METHOD go_grid->refresh_table_display
*     EXPORTING
*     IS_STABLE =
*     I_SOFT_REFRESH =
      EXCEPTIONS
        finished = 1
        OTHERS = 2 .
    IF sy-subrc <> 0.
    ENDIF.
  ENDIF.
*---- Event Create
  CREATE OBJECT event_receiver.
  SET HANDLER event_receiver->handle_double_click FOR go_grid. "双击事件"
  SET HANDLER event_receiver->handle_data_changed FOR go_grid. "数据修改事件"
  CALL METHOD go_grid->register_edit_event "注册编辑事件，否则不会触发更新事件"
    EXPORTING
      I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_MODIFIED.
ENDMODULE.                 " STATUS_0100  OUTPUT "
*&---------------------------------------------------------------------*
*&      Module  USER_COMMAND_0100  INPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE user_command_0100 INPUT.
  CASE sy-ucomm.
    WHEN 'BACK' OR 'UP' OR 'CANCEL'.
      LEAVE PROGRAM.
  ENDCASE.
ENDMODULE.                 " USER_COMMAND_0100  INPUT "
*&---------------------------------------------------------------------*
*&      Form  prepare_field_catalog
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->PT_FIELDCAT  text
*----------------------------------------------------------------------*
FORM prepare_field_catalog  CHANGING pt_fieldcat TYPE lvc_t_fcat.
  mrc_fieldcat 'EBELN' 'Purchase Order'      '1' '18'.
  mrc_fieldcat 'EBELP' 'Purchase Order Item' '2' '5'.
  mrc_fieldcat 'MATNR' 'Material Number'     '3' '18'.
  mrc_fieldcat 'WERKS' 'Plant'               '4' '5'.
  mrc_fieldcat 'MENGE' 'Quantity'            '5' '18'.
  mrc_fieldcat 'MEINS' 'Unit'                '6' '18'.
  mrc_fieldcat 'NETWR' 'Value'               '7' '18'.
  mrc_fieldcat 'AEDAT' 'Create Date'         '8' '18'.
  mrc_fieldcat 'ERNAM' 'Create By'           '9' '18'.
ENDFORM.                    " PREPARE_FIELD_CATALOG "
*&---------------------------------------------------------------------*
*&      Form  PREPARE_LAYOUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      <--P_GS_LAYOUT  text
*----------------------------------------------------------------------*
FORM prepare_layout  CHANGING ps_layout TYPE lvc_s_layo.
  ps_layout-zebra = 'X' .
  ps_layout-cwidth_opt  = 'X'.
  ps_layout-grid_title = 'Material Document List' .
  ps_layout-smalltitle = 'X' .
*  ps_layout-info_fname  = 'ROWCOLOR'.
ENDFORM.                    " PREPARE_LAYOUT "
"事件类定义"
CLASS cl_event_receiver DEFINITION.
  METHODS handle_double_click FOR EVENT double_click OF cl_gui_alv_grid 
    IMPORTING e_row e_column es_row_no.
  METHODS handle_data_changed FOR EVENT data_changed OF cl_gui_alv_grid
    IMPORTING er_data_changed.
ENDCLASS. "CL_EVENT_RECEIVER"
"事件类实现"
CLASS cl_event_receiver IMPLEMENTATION.
  METHODS handle_double_click.
    CONDENSE e_row     NO-GAPS.
    CONDENSE e_column  NO-GAPS.
    DATA: ls_output TYPE str_output.
    READ TABLE gt_output INDEX e_row INTO ls_output.
    CASE e_column.
      WHEN 'MATNR'.
        SET PARAMETER ID 'MAT' FIELD ls_output-matnr.
        SET PARAMETER ID 'MXX' FIELD 'D'.
        SET PARAMETER ID 'WRK' FIELD ls_output-werks.
        CALL TRANSACTION 'MM03' AND SKIP FIRST SCREEN.
      WHEN OTHERS.
    ENDCASE.
  ENDMETHOD.
  METHOD handle_data_changed.
    DATA: ls_output TYPE str_output,
          ls_good   TYPE lvc_s_modi,
          lv_menge  TYPE ekpo-netwr.
    LOOP AT er_data_changed->mt_good_cells INTO ls_good.
      CLEAR: ls_output,lv_menge.
      IF ls_good-fieldname = 'NETWR'.
        READ TABLE gt_output INDEX ls_good-row_id INTO ls_output.
        IF sy-subrc = 0.
          CALL METHOD er_data_changed->get_cell_value
            EXPORTING
              i_row_id    = ls_good-row_id
              i_fieldname = ls_good-fieldname
            IMPORTING
              e_value     = lv_menge.
          ......
          CALL METHOD er_data_changed->modify_cell
            EXPORTING
              i_row_id    = ls_good-row_id
              i_fieldname = ls_good-fieldname
              i_value     = lv_menge.
        ENDIF.
      ENDIF.
    ENDLOOP.
  ENDMETHOD.
ENDCLASS. "CL_EVENT_RECEIVER"
```

