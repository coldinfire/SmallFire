---
title: " OO ALV Demo "
date: 2018-07-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - OOALV
  - DEMO

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
  append gs_fieldcat to gt_fieldcat.
END-OF-DEFINITION.
"Event "
CLASS cl_event_receiver DEFINITION DEFERRED.
DATA event_receiver TYPE REF TO cl_event_receiver.
"Select Screen"
*----------------------------------------------------------------------*
*       CLASS cl_event_receiver DEFINITION
*----------------------------------------------------------------------*
CLASS cl_event_receiver DEFINITION.
  PUBLIC SECTION.
    METHODS handle_toolbar FOR EVENT toobal OF cl_gui_alv_grid
      IMPORTING e_object e_interactive.
    METHODS handle_user_command FOR EVENT user_command OF cl_gui_alv_grid
      IMPORTING e_ucomm.
    METHODS handle_double_click FOR EVENT double_click OF cl_gui_alv_grid
      IMPORTING e_row e_column es_row_no.
    METHODS handle_data_changed FOR EVENT data_changed OF cl_gui_alv_grid
      IMPORTING er_data_changed.
ENDCLASS. "CL_EVENT_RECEIVER"
"事件类实现"
*----------------------------------------------------------------------*
*       CLASS cl_event_receiver IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS cl_event_receiver IMPLEMENTATION.
  METHOD handle_toolbar.
    "ADDING A SAVE BUTTONN TO THE ALV TOOLBAR "
    DATA: is_btn TYPE stb_button.
    is_btn-function = ’SAVE‘.
    is_btn-icon = icon_system_save.
    is_btn-text = ‘SAVE’.
    is_btn-quickinfo = ‘SAVE’.
    is_btn-disabled = ‘ ‘.
    APPEND is_btn TO e_object->mt_toolbar.
  ENDMETHOD.    "handle_toolbar"
  METHOD handle_user_command .
    CASE e_ucomm.
      WHEN ‘SAVE’.
        PERFORM update_data_base.
    ENDCASE.
  ENDMETHOD.    "handle_user_command"
  METHOD handle_double_click.
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
    ENDCASE.   "handel_double_click"
  ENDMETHOD.                    "cl_event_receiver"
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
  ENDMETHOD.                    "handle_data_changed"
ENDCLASS. "CL_EVENT_RECEIVER"

START-OF-SELECTION.
  PERFORM get_data.
  PERFORM prepare_fieldcat.
  PERFORM prepare_layout.
END-OF-SELECTION.
    CALL SCREEN 1000.
    
*&---------------------------------------------------------------------*
*&      Form  GET_DATA
*&---------------------------------------------------------------------*
FORM get_data .
  CLEAR gt_ekko.
  SELECT * FROM ekko INTO TABLE gt_ekko UP TO 20 ROWS.
  IF gt_ekko IS NOT INITIAL.
    CLEAR gt_ekpo.
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
*&      Form   PREPARE_FIELDCAT
*&---------------------------------------------------------------------*
FORM prepare_fieldcat .
  CLEAR gt_fieldcat.
  mrc_fieldcat 'EBELN' 'Purchase Order'      '1' '18'.
  mrc_fieldcat 'EBELP' 'Purchase Order Item' '2' '5'.
  mrc_fieldcat 'MATNR' 'Material Number'     '3' '18'.
  mrc_fieldcat 'WERKS' 'Plant'               '4' '5'.
  mrc_fieldcat 'MENGE' 'Quantity'            '5' '18'.
  mrc_fieldcat 'MEINS' 'Unit'                '6' '18'.
  mrc_fieldcat 'NETWR' 'Value'               '7' '18'.
  mrc_fieldcat 'AEDAT' 'Create Date'         '8' '18'.
  mrc_fieldcat 'ERNAM' 'Create By'           '9' '18'.
ENDFORM.                    " PREPARE_FIELDCAT "
*&---------------------------------------------------------------------*
*&      Form  PREPARE_LAYOUT
*&---------------------------------------------------------------------*
FORM prepare_layout.
  CLEAR layout.
  layout-zebra = 'X' .
  layout-cwidth_opt  = 'X'.
  layout-grid_title = 'Material Document List' .
* layout-smalltitle = 'X' .
* layout-info_fname  = 'ROWCOLOR'.
ENDFORM.                    " PREPARE_LAYOUT "
*&---------------------------------------------------------------------*
*&      Module  STATUS_1000  OUTPUT
*&---------------------------------------------------------------------*
MODULE status_1000 OUTPUT.
  SET PF-STATUS 'STATUS_1000'.
ENDMODULE.                 " STATUS_1000  OUTPUT "
*&---------------------------------------------------------------------*
*&      Module  USER_COMMAND_1000  INPUT
*&---------------------------------------------------------------------*
MODULE user_command_1000 INPUT.
  CASE sy-ucomm.
    WHEN 'SAVE'.
      PERFORM update_data_base.
    WHEN 'BACK' OR 'EXIT' OR 'CANCEL'.
      LEAVE PROGRAM.
  ENDCASE.
ENDMODULE.                 " USER_COMMAND_1000  INPUT "
*&---------------------------------------------------------------------*
*&      Module  display_alv  OUTPUT
*&---------------------------------------------------------------------*
MODULE display_alv OUTPUT.
*----Creating custom container instance
  IF go_container IS NOT BOUND.
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
  ENDIF.
*----Creating alv grid instance
  IF go_grid IS NOT BOUND.
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
*---- Event Create
    CREATE OBJECT event_receiver.
    SET HANDLER event_receiver->handle_toolbar FOR go_grid.
    SET HANDLER event_receiver->handle_user_command FOR go_grid.
    SET HANDLER event_receiver->handle_double_click FOR go_grid. "双击事件"
    SET HANDLER event_receiver->handle_data_changed FOR go_grid. "数据修改事件"
    CALL METHOD go_grid->register_edit_event  "注册编辑事件，否则不会触发更新事件"
      EXPORTING
        i_event_id = cl_gui_alv_grid=>mc_evt_modified.
  ENDIF.
  CALL METHOD go_grid->refresh_table_display
*     EXPORTING
*     IS_STABLE =
*     I_SOFT_REFRESH =
    EXCEPTIONS
      finished = 1
      OTHERS = 2 .
    IF sy-subrc <> 0.
    ENDIF.
ENDMODULE.  "display_alv"
*&---------------------------------------------------------------------*
*&      Form  UPDATE_DATA_BASE
*&---------------------------------------------------------------------*
FORM  UPDATE_DATA_BASE .
  CALL METHOD o_alv->check_changed_data
    IMPORTING
      e_valid = check.
  IF it_spfli NE it_spfli_old.
    LOOP AT it_spfli INTO wa_spfli.
      MOVE-CORRESPONDING wa_spfli TO wa_spfli_new.
      APPEND wa_spfli_new TO it_spfli_new.
    ENDLOOP.
    MODIFY spfli FROM TABLE it_spfli_new.
    IF  sy-subrc = 0..
      MESSAGE ‘Change data success’ TYPE ‘S’.
    ENDIF.
  ENDIF.
ENDFORM.                    ” UPDATE_DATA_BASE
```

