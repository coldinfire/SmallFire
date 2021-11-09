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
REPORT  zgrid_lvc_demo.
TYPE-POOLS: slis.
TABLES: lqua,aufk,afpo,zdemo,rlgrap.
"ALV Data"
DATA: gt_demo TYPE STANDARD TABLE OF zdemo,
      gs_dmeo TYPE zdmeo.
"ALV Parameter"
DATA: gt_fieldcat TYPE lvc_t_fcat,
      gs_fieldcat TYPE lvc_s_fcat,
      gs_layout   TYPE lvc_s_layo.
"Select screen"
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN POSITION 1.
PARAMETERS: p_chk1 RADIOBUTTON GROUP zr01 DEFAULT 'X' USER-COMMAND zchg.
SELECTION-SCREEN COMMENT 3(8) text-002 FOR FIELD p_chk1.
SELECTION-SCREEN POSITION 12.
PARAMETERS: p_chk2 RADIOBUTTON GROUP zr01. " USER-COMMAND zchg."
SELECTION-SCREEN COMMENT 14(12) text-003 FOR FIELD p_chk2.
SELECTION-SCREEN END OF LINE.

SELECTION-SCREEN BEGIN OF BLOCK blk1 WITH FRAME TITLE text-001.
PARAMETERS: p_lgnum TYPE lqua-lgnum OBLIGATORY.
PARAMETERS: p_file  LIKE rlgrap-filename MODIF ID z01.
SELECTION-SCREEN END OF BLOCK blk1.

SELECTION-SCREEN BEGIN OF BLOCK blk2 WITH FRAME TITLE text-004.
PARAMETERS: p_werks TYPE aufk-werks MODIF ID z02.
SELECT-OPTIONS: s_matnr FOR matnr MODIF ID z02,
                s_seqnr FOR aufk-seqnr MODIF ID z02,
                s_kdpos FOR afpo-kdpos MODIF ID z02.
SELECTION-SCREEN END OF BLOCK blk2.
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
    FROM zdemo
    INTO CORRESPONDING TABLE gt_demo
   WHERE lgnum EQ p_lgnum
     AND matnr IN s_matnr.
  IF gt_demo IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
ENDFORM.                    " FRM_EXTRACT_DATA"
*&---------------------------------------------------------------------*
*&      Form  FRM_DISPLAY
*&---------------------------------------------------------------------*
FORM frm_display .
  CLEAR gs_layout.
  gs_layout-zebra = 'X'.
  gs_layout-col_opt = 'X'.
  gs_layout-cwidth_opt = 'X'.
  CLEAR gt_fieldcat.
  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
    EXPORTING
      i_structure_name       = 'ZDEMO_STRUCTURE'
    CHANGING
      ct_fieldcat            = gt_fieldcat
    EXCEPTIONS
      inconsistent_interface = 1
      program_error          = 2
      OTHERS                 = 3.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
  DELETE gt_fieldcat WHERE fieldname EQ 'STATUS'.
  "显示 ALV"
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      i_callback_program       = sy-repid
      i_callback_pf_status_set = 'FRM_SET_STATUS'
      i_callback_user_command  = 'FRM_USER_COMMAND'
      is_layout_lvc            = gs_layout
      it_fieldcat_lvc          = gt_fieldcat
      i_default                = 'X'
      i_save                   = 'X'
    TABLES
      t_outtab                 = gt_demo
    EXCEPTIONS
      program_error            = 1.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    " FRM_DISPLAY"
*&---------------------------------------------------------------------*
*&      Form  frm_set_status
*&---------------------------------------------------------------------*
FORM frm_set_status USING extab TYPE slis_t_extab.
  DATA: lt_fcode TYPE STANDARD TABLE OF sy-ucomm.
  CLEAR lt_fcode.
    APPEND '&CHNG' TO lt_fcode.
    APPEND '&MODI' TO lt_fcode.
    APPEND '&XDPL' TO lt_fcode.
  SET PF-STATUS 'STATUS' EXCLUDING LT_FCODE.
ENDFORM.                    "frm_set_status"
*&---------------------------------------------------------------------*
*&      Form  frm_user_command
*&---------------------------------------------------------------------*
FORM frm_user_command USING r_ucomm LIKE sy-ucomm
                        rs_selfield TYPE slis_selfield.
  CASE r_ucomm.
    WHEN '&F03'.
      RETURN.
    WHEN  '&F15'.
      RETURN.
    WHEN  '&F15'.
      RETURN.
    WHEN OTHERS.
  ENDCASE.
  rs_selfield-refresh = 'X'.
ENDFORM.                    "frm_user_command"
```

