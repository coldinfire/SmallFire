---
title: " GRID ALV Demo "
date: 2018-06-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
---

### GRID ALV 程序实例

```ABAP
*&---------------------------------------------------------------------*
*& Report  ZGRID_ALV_DEMO
*&---------------------------------------------------------------------*
REPORT  zgrid_alv_demo.
TYPE-POOLS: slis.
TABLES: mara,marc,rlgrap.
"ALV Data"
TYPES: BEGIN OF str_demo,
  sel TYPE flag,
  id TYPE char25,         " Red Green color "
  message TYPE string,    " Message "
  index TYPE i,
  matnr TYPE mara-matnr,
  werks TYPE marc-werks,
  dismm TYPE marc-dismm,
  dispo TYPE marc-dispo,
END OF str_demo.
DATA: gt_demo TYPE STANDARD TABLE OF str_demo,
      gs_dmeo TYPE str_demo.
"ALV Parameter"
DATA: gt_fieldcat TYPE slis_t_fieldcat_alv,
      gs_fieldcat TYPE slis_fieldcat_alv,
      gt_events TYPE slis_t_event,
      gs_events TYPE slis_alv_event,
      gs_grid_settings TYPE lvc_s_glay,
      layout   TYPE slis_layout_alv.
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
  clear: gs_fieldcat.
  gs_fieldcat-fieldname = &1.
  translate gs_fieldcat-fieldname to upper case.
  gs_fieldcat-reptext_ddic = &2.
  gs_fieldcat-seltext_l = &2.
  gs_fieldcat-seltext_m = &2.
  gs_fieldcat-seltext_s = &2.
  gs_fieldcat-no_zero = 'X'.
  if gs_fieldcat-fieldname eq 'SEL'.
    gs_fieldcat-checkbox = 'X'.
    gs_fieldcat-edit     = 'X'.
    gs_fieldcat-just     = 'C'.
  endif.
  append gs_fieldcat to gt_fieldcat.
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
  INTO CORRESPONDING FIELDS OF TABLE gt_demo
  WHERE mara~matnr IN s_matnr
  AND werks EQ p_werks.
  IF gt_demo IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
ENDFORM.                    " FRM_EXTRACT_DATA"
*&---------------------------------------------------------------------*
*&      Form  FRM_DISPLAY
*&---------------------------------------------------------------------*
FORM frm_display .
  CLEAR layout.
  layout-zebra = 'X'.
  layout-colwidth_optimize = 'X'.
  layout-box_fieldname = 'SEL'. "设置选择列"
  CLEAR gt_fieldcat.
  PERFORM frm_init_fcat.
  PERFORM frm_event.
  "修改后刷新数据到内表"
  gs_grid_settings-edt_cll_cb = 'X'.
  "显示 ALV"
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING
      i_callback_program       = sy-repid
      it_events                = gt_events
      i_callback_pf_status_set = 'PF_STATUS_SET'
      i_callback_user_command  = 'USER_COMMAND'
      is_layout                = layout
      it_fieldcat              = gt_fieldcat
      i_grid_settings          = gs_grid_settings
      i_save                   = 'A'
    TABLES
      t_outtab                 = gt_demo.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    " FRM_DISPLAY"
*&---------------------------------------------------------------------*
*&      Form  frm_set_status
*&---------------------------------------------------------------------*
FORM pf_status_set USING rt_extab TYPE slis_t_extab.
  DATA: lt_fcode TYPE STANDARD TABLE OF sy-ucomm.
  CLEAR lt_fcode.
*  APPEND '&CHNG' TO lt_fcode.
  SET PF-STATUS 'STATUS' EXCLUDING lt_fcode.

ENDFORM.                    "frm_set_status"
*&---------------------------------------------------------------------*
*&      Form  frm_user_command
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
ENDFORM.                    "frm_user_command"
*&---------------------------------------------------------------------*
*&      Form  FRM_INIT_FCAT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM frm_init_fcat .
*  mro_fcat 'sel'          'Choose'. " 设置check box
  mro_fcat 'index'        'Index'.
  mro_fcat 'id'           'Icon' .
  mro_fcat 'message'      'Message'.
  mro_fcat 'werks'        'Plant'.
  mro_fcat 'matnr'        'P/N'.
  mro_fcat 'dismm'        'MRP TYpe'.
  mro_fcat 'dispo'        'MRP Controller'.

  "半自动创建"
*  CALL FUNCTION 'REUSE_ALV_FIELDCATALOG_MERGE'
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
ENDFORM.                    " FRM_INIT_FCAT "
*&---------------------------------------------------------------------*
*&      Form  frm_event
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
FORM frm_event.
  CLEAR gt_events.
  CALL FUNCTION 'REUSE_ALV_EVENTS_GET'
    EXPORTING
      i_list_type = 0
    IMPORTING
      et_events   = gt_events.
ENDFORM.                    "frm_event"
```

