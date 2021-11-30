---
title: " GRID LVC ALV Demo "
date: 2018-06-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### GRID LVC ALV 程序实例

```ABAP
*&-----------------------------------------------------------*
*& Report  ZGRID_ALV_LVC_DEMO
*&-----------------------------------------------------------*
REPORT  zgrid_alv_lvc_demo.
TYPE-POOLS: slis.
TABLES: mara,marc,rlgrap.
"Internal Table Data"
TYPES: BEGIN OF str_alv,
  sel TYPE flag,
  id TYPE char25,         " Red Green color "
  message TYPE string,    " Message "
  index TYPE i,
  matnr TYPE mara-matnr,
  werks TYPE marc-werks,
  dismm TYPE marc-dismm,
  dispo TYPE marc-dispo,
END OF str_alv.
DATA: gt_alv TYPE STANDARD TABLE OF str_alv,
      gs_alv TYPE str_alv.
"ALV Parameter"
DATA: gt_lvc_fieldcat TYPE lvc_t_fcat,
      gs_lvc_fieldcat TYPE lvc_s_fcat,
      gs_grid_settings TYPE lvc_s_glay,
      layout   TYPE lvc_s_layo.
"Select screen"
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN POSITION 1.
PARAMETERS: p_chk1 RADIOBUTTON GROUP zr01 DEFAULT 'X' USER-COMMAND zchg.
SELECTION-SCREEN COMMENT 3(8) text-002 FOR FIELD p_chk1.
SELECTION-SCREEN POSITION 12.
PARAMETERS: p_chk2 RADIOBUTTON GROUP zr01.
SELECTION-SCREEN COMMENT 14(12) text-003 FOR FIELD p_chk2.
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
PARAMETERS: p_mtart TYPE mara-mtart.
PARAMETERS: p_file  LIKE rlgrap-filename MODIF ID z01.
SELECTION-SCREEN END OF BLOCK blk1.
SELECTION-SCREEN BEGIN OF BLOCK blk2 WITH FRAME TITLE text-004.
PARAMETERS: p_werks TYPE marc-werks MODIF ID z02.
SELECT-OPTIONS: s_matnr FOR mara-matnr MODIF ID z02.
SELECTION-SCREEN END OF BLOCK blk2.
"Marco Define"
DEFINE mro_fcat.
  clear gs_lvc_fieldcat.
  gs_lvc_fieldcat-fieldname = &1.
  translate gs_lvc_fieldcat-fieldname to upper case.
  gs_lvc_fieldcat-reptext   = &2.
  gs_lvc_fieldcat-scrtext_l = &2.
  gs_lvc_fieldcat-scrtext_m = &2.
  gs_lvc_fieldcat-scrtext_s = &2.
  if gs_lvc_fieldcat-fieldname eq 'SEL'.
    gs_lvc_fieldcat-checkbox = 'X'.
    gs_lvc_fieldcat-edit     = 'X'.
    gs_lvc_fieldcat-just     = 'C'.
  endif.
  append gs_lvc_fieldcat to gt_lvc_fieldcat.
END-OF-DEFINITION.
" Screen Group control "

AT SELECTION-SCREEN OUTPUT.
  LOOP AT SCREEN.
    IF p_chk1 EQ 'X'.
      IF screen-group1 EQ 'Z02'.
        screen-input = 0.
        screen-invisible = 1.
        MODIFY SCREEN.
      ELSEIF screen-group1 EQ 'Z01'.
        screen-input = 1.
        screen-invisible = 0.
        MODIFY SCREEN.
      ENDIF.
    ELSE.
      IF screen-group1 EQ 'Z01'.
        screen-input = 0.
        screen-invisible = 1.
        MODIFY SCREEN.
      ELSEIF screen-group1 EQ 'Z02'.
        screen-input = 1.
        screen-invisible = 0.
        MODIFY SCREEN.
      ENDIF.
    ENDIF.
  ENDLOOP.
  "Screen Event"

START-OF-SELECTION .
  "Extract data"
  PERFORM frm_extract_data.
  "Display alv"
  PERFORM frm_display.
*&---------------------------------------------------------------------*
*&      Form  FRM_EXTRACT_DATA
*&---------------------------------------------------------------------*
FORM frm_extract_data .
  SELECT *
  FROM mara INNER JOIN marc
  ON mara~matnr = marc~matnr
  INTO CORRESPONDING FIELDS OF TABLE gt_alv
  WHERE mara~matnr IN s_matnr
  AND werks EQ p_werks.
  IF gt_alv IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
ENDFORM.                    " FRM_EXTRACT_DATA"
*&---------------------------------------------------------------------*
*&      Form  FRM_DISPLAY
*&---------------------------------------------------------------------*
FORM frm_display .
  CLEAR layout.
  layout-zebra   = 'X'.
  layout-col_opt = 'X'.
  layout-cwidth_opt = 'X'.
  layout-box_fname  = 'SEL'. "设置选择列字段"
  CLEAR gt_lvc_fieldcat.
  PERFORM frm_init_fcat.
  "修改后刷新数据到内表"
  gs_grid_settings-edt_cll_cb = 'X'.
  "显示 ALV"
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      i_callback_program       = sy-repid
      i_callback_pf_status_set = 'PF_STATUS_SET'
      i_callback_user_command  = 'USER_COMMAND'
      is_layout_lvc            = layout
      it_fieldcat_lvc          = gt_lvc_fieldcat
      i_grid_settings          = gs_grid_settings
      i_default                = 'X'
      i_save                   = 'X'
    TABLES
      t_outtab                 = gt_alv
    EXCEPTIONS
      program_error            = 1.

  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    "FRM_DISPLAY"
*&---------------------------------------------------------------------*
*&      Form  pf_status_set
*&---------------------------------------------------------------------*
FORM pf_status_set USING extab TYPE slis_t_extab.
  DATA: lt_fcode TYPE STANDARD TABLE OF sy-ucomm.
  CLEAR lt_fcode.
  APPEND '&CHNG' TO lt_fcode.
  APPEND '&MODI' TO lt_fcode.
  APPEND '&XDPL' TO lt_fcode.
  SET PF-STATUS 'STATUS' EXCLUDING lt_fcode.
  "设置编辑事件"
*  DATA: lo_grid TYPE REF TO cl_gui_alv_grid.
*  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
*    IMPORTING
*      e_grid = lo_grid.
*  CALL METHOD lo_grid->register_edit_event
*    EXPORTING
*      i_event_id = cl_gui_alv_grid=>mc_evt_modified.
*  IF sy-subrc <> 0.
*  ENDIF.
ENDFORM.                    "pf_status_set"
*&---------------------------------------------------------------------*
*&      Form  user_command
*&---------------------------------------------------------------------*
FORM user_command USING r_ucomm LIKE sy-ucomm
      rs_selfield TYPE slis_selfield.

  CASE r_ucomm.
    WHEN '&IC1'.
    WHEN 'BACK' OR 'EXIT'.
      LEAVE PROGRAM .
    WHEN 'CANCEL'.
      LEAVE PROGRAM.
    WHEN OTHERS.
  ENDCASE.
  rs_selfield-refresh = 'X'.
ENDFORM.                    "user_command"
*&---------------------------------------------------------------------*
*&      Form  FRM_INIT_FCAT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM frm_init_fcat .
*  mro_fcat 'sel'            'Choose'.
  mro_fcat 'index'          'Index'.
  mro_fcat 'id'             'Icon'                   .
  mro_fcat 'message'        'Message'                .
  mro_fcat 'werks'          'Plant'                 .
  mro_fcat 'matnr'          'P/N'                   .
  mro_fcat 'dismm'          'MRP TYpe'              .
  mro_fcat 'dispo'          'MRP Controller'        .
  APPEND gs_lvc_fieldcat TO gt_lvc_fieldcat.
  "半自动创建"
*  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
*    EXPORTING
*      i_structure_name       = 'ZDEMO_STRUCTURE'
*    CHANGING
*      ct_fieldcat            = gt_fieldcat
*    EXCEPTIONS
*      inconsistent_interface = 1
*      program_error          = 2
*      OTHERS                 = 3.
*  IF sy-subrc <> 0.
*    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
*    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
*  ENDIF.
ENDFORM.                    " FRM_INIT_FCAT"
```

