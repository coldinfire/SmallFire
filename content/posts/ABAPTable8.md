---
title: "OO ALV实例程序"
date: 2018-07-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---



OO ALV简单实例：其中屏幕的创建， 和对应PAI,PBO以及Status title等可以自定调整。

```JS
REPORT  zoo_alv.
TYPE-POOLS: slis.
*-- Global data Decleration.
TYPES:BEGIN OF ty_output,
      ebeln TYPE ebeln,
      aedat TYPE erdat,
      ernam TYPE ernam,
      ebelp TYPE ebelp,
      matnr TYPE matnr,
      werks TYPE werks_d,
      menge TYPE bstmg,
      meins TYPE bstme,
      netwr TYPE bwert,
      END OF ty_output.
DATA: lt_ekko TYPE TABLE OF ekko,
      ls_ekko TYPE ekko,
      lt_ekpo TYPE TABLE OF ekpo,
      ls_ekpo TYPE ekpo,
      lt_output TYPE TABLE OF ty_output,
      ls_output TYPE ty_output.
"Global Data Definitions for ALV
"$. Region ALV_Data
*DATA g_CONTAINER TYPE REF TO CL_GUI_CONTAINER.
*DATA g_splitter TYPE REF TO cl_gui_splitter_container.
DATA container TYPE REF TO cl_gui_custom_container .  "-- Custom container instance reference
DATA grid        TYPE REF TO cl_gui_alv_grid .        "-- ALV Grid instance reference
DATA lt_fieldcat TYPE lvc_t_fcat.  "-- Field catalog table
DATA lw_fieldcat TYPE lvc_s_fcat.
DATA lw_layout   TYPE lvc_s_layo .  "-- Layout structure
DATA lt_exclude  TYPE ui_functions. "-- Button Exclude
DATA lt_variant  TYPE disvariant.   "-- Variant
DATA lt_sort     TYPE lvc_t_sort.   "-- Sotrt table
DATA lt_filt     TYPE lvc_t_filt.   "-- Filter table
"$. Endregion ALV_Data
START-OF-SELECTION.
  PERFORM get_data.
  IF lt_output IS NOT INITIAL.
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
  SELECT * FROM ekko INTO TABLE lt_ekko UP TO 20 ROWS.
  IF lt_ekko IS NOT INITIAL.
    SELECT *
       FROM ekpo
       INTO TABLE lt_ekpo
       FOR ALL ENTRIES IN lt_ekko
       WHERE ebeln = lt_ekko-ebeln.
  ENDIF.

  LOOP AT lt_ekpo INTO ls_ekpo.
    ls_output-ebeln = ls_ekpo-ebeln.
    ls_output-ebelp = ls_ekpo-ebelp.
    ls_output-matnr = ls_ekpo-matnr.
    ls_output-werks = ls_ekpo-werks.
    ls_output-menge = ls_ekpo-menge.
    ls_output-meins = ls_ekpo-meins.
    ls_output-netwr = ls_ekpo-netwr.
    READ TABLE lt_ekko INTO ls_ekko WITH KEY ebeln = ls_ekpo-ebeln.
    IF sy-subrc IS INITIAL.
      ls_output-aedat = ls_ekko-aedat.
      ls_output-ernam = ls_ekko-ernam.
    ENDIF.
    APPEND ls_output TO lt_output.
    CLEAR ls_output.
  ENDLOOP.
ENDFORM.                    " GET_DATA

*&---------------------------------------------------------------------*
*&      Module  STATUS_0100  OUTPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'PF_1000'.
  SET TITLEBAR 'TIT_100'.

  PERFORM prepare_field_catalog CHANGING lt_fieldcat.

  PERFORM prepare_layout CHANGING lw_layout.

  IF grid IS INITIAL .
*----Creating custom container instance
    CREATE OBJECT container
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
      "Exception handling
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.

*----creating alv grid instance
    CREATE OBJECT grid
      EXPORTING
        i_parent          = container
      EXCEPTIONS
        error_cntl_create = 1
        error_cntl_init   = 2
        error_cntl_link   = 3
        error_dp_create   = 4
        OTHERS            = 5.
    IF sy-subrc <> 0.
      "Exception handling
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
     WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
*----Here will be additional preparations
*--e.g. initial sorting criteria, initial filtering criteria, excluding
*--functions
    CALL METHOD grid->set_table_for_first_display
      EXPORTING
*      i_buffer_active               =
*      i_bypassing_buffer            =
*      i_consistency_check           =
*      i_structure_name              =      "Name of the DDIC structure, the catalog is generated automatically (Have priority)
*      is_variant                    =
        i_save                        = 'X'
        i_default                     = 'X'
        is_layout                     = lw_layout
*      is_print                      =
*      it_special_groups             =
*      it_toolbar_excluding          =
*      it_hyperlink                  =
*      it_alv_graphics               =
*      it_except_qinfo               =
*      ir_salv_adapter               =
      CHANGING
        it_outtab                     = lt_output
        it_fieldcatalog               = lt_fieldcat
*      it_sort                       =
*      it_filter                     =
      EXCEPTIONS
        invalid_parameter_combination = 1
        program_error                 = 2
        too_many_lines                = 3
        OTHERS                        = 4
              .
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
      WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    ENDIF.
  ELSE .
    CALL METHOD grid->refresh_table_display
*     EXPORTING
*     IS_STABLE =
*     I_SOFT_REFRESH =
     EXCEPTIONS
      finished = 1
      OTHERS = 2 .
    IF sy-subrc <> 0.
      "-Exception handling
    ENDIF.
  ENDIF.


ENDMODULE.                 " STATUS_0100  OUTPUT
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
ENDMODULE.                 " USER_COMMAND_0100  INPUT

*&---------------------------------------------------------------------*
*&      Form  prepare_field_catalog
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->PT_FIELDCAT  text
*----------------------------------------------------------------------*
FORM prepare_field_catalog  CHANGING pt_fieldcat TYPE lvc_t_fcat.
  DATA ls_fcat TYPE lvc_s_fcat .
  CLEAR ls_fcat .

  ls_fcat-fieldname = 'EBELN'.
  ls_fcat-seltext = 'Purchase Order'.
  ls_fcat-col_pos = 1.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'EBELP'.
  ls_fcat-seltext = 'Purchase Order Item'.
  ls_fcat-col_pos = 2.
  ls_fcat-outputlen = 5.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'MATNR'.
  ls_fcat-seltext = 'Material Number'.
  ls_fcat-col_pos = 3.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'WERKS'.
  ls_fcat-seltext = 'Plant'.
  ls_fcat-col_pos = 4.
  ls_fcat-outputlen = 5.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'MENGE'.
  ls_fcat-seltext = 'Quantity'.
  ls_fcat-col_pos = 5.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'MEINS'.
  ls_fcat-seltext = 'Unit'.
  ls_fcat-col_pos = 6.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'NETWR'.
  ls_fcat-seltext = 'Value'.
  ls_fcat-col_pos = 7.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'AEDAT'.
  ls_fcat-seltext = 'Create Date'.
  ls_fcat-col_pos = 8.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

  ls_fcat-fieldname = 'ERNAM'.
  ls_fcat-seltext = 'Create By'.
  ls_fcat-col_pos = 9.
  ls_fcat-outputlen = 18.
  APPEND ls_fcat TO pt_fieldcat.
  CLEAR ls_fcat.

ENDFORM.                    " PREPARE_FIELD_CATALOG


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
ENDFORM.                    " PREPARE_LAYOUT

```

