---
title: " List ALV Demo "
date: 2018-08-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 程序实例

```JS
*&---------------------------------------
*& Report  ZWR_PI_LOG
*&----------------------------------------
REPORT  zwr_pi_log.
TABLES: lqua ,zpidoc.
TYPE-POOLS: slis.
DATA: lt_pidoc TYPE STANDARD TABLE OF zpidoc,
      ls_pidoc TYPE zpidoc .
"ALV Data"
DATA: gt_fieldcat TYPE lvc_t_fcat,
      gs_fieldcat TYPE lvc_s_fcat,
      lw_layout TYPE lvc_s_layo.
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
PARAMETERS: p_status TYPE zpidoc-STATUS.
PARAMETERS: p_file LIKE rlgrap-filename MODIF ID z01.
SELECTION-SCREEN END OF BLOCK blk1.

SELECTION-SCREEN BEGIN OF BLOCK blk2 WITH FRAME TITLE text-004.
PARAMETERS: p_werks TYPE aufk-werks DEFAULT '1040' MODIF ID z02.
SELECT-OPTIONS: s_matnr FOR matnr MODIF ID z02,
                s_seqnr FOR aufk-seqnr MODIF ID z02,
                s_ablad FOR afpo-ablad MODIF ID z02,
                s_plnbez FOR afko-plnbez MODIF ID z02,
                s_fevor FOR afko-fevor MODIF ID z02,
                s_dispo FOR afko-dispo MODIF ID z02,
                s_arbpl FOR /sapcem/kla_arb-arbpl MODIF ID z02,
                s_gstrp FOR afko-gstrp MODIF ID z02,
                s_gltrp FOR afko-gltrp MODIF ID z02,
                s_auart FOR aufk-auart MODIF ID z02,
                s_kdauf FOR afpo-kdauf MODIF ID z02,
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

"Event"
START-OF-SELECTION .
  "Pre-check"
  PERFORM frm_pre_check.
  "Extract data"
  PERFORM frm_extract_data.
  "ALV"
  PERFORM frm_display.
END-OF-SELECTION.
*&---------------------------------------------------------------------*
*&      Form  FRM_PRE_CHECK
*&---------------------------------------------------------------------*
  FORM frm_pre_check .
  DATA:e_message TYPE char100.
  AUTHORITY-CHECK OBJECT 'XXXX'
           "ID 'ACTVT' FIELD '03'"
           ID 'LGNUM' FIELD p_lgnum.
  IF sy-subrc <> 0.
    CONCATENATE 'You have no authorization ' p_lgnum INTO e_message RESPECTING BLANKS.
    MESSAGE e_message TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
  ENDFORM.                    " FRM_PRE_CHECK"
  *&---------------------------------------------------------------------*
  *&      Form  FRM_EXTRACT_DATA
  *&---------------------------------------------------------------------*
  FORM frm_extract_data .
   SELECT *
    FROM zpidoc
     INTO TABLE lt_pidoc
     WHERE lgnum EQ p_lgnum
      AND PIDOC  IN s_PIDOC.
  IF lt_pidoc IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
  ENDFORM.                    " FRM_EXTRACT_DATA"
  *&---------------------------------------------------------------------*
  *&      Form  FRM_DISPLAY
  *&---------------------------------------------------------------------*
  FORM frm_display .

  lw_layout-zebra = 'X'.
  lw_layout-col_opt = 'X'.
  lw_layout-cwidth_opt = 'X'.

  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
    EXPORTING
      i_structure_name       = 'ZPIDOC_STRUCTURE'
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
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      i_callback_program       = sy-repid
      i_callback_pf_status_set = 'FRM_SET_STATUS'
      i_callback_user_command  = 'FRM_USER_COMMAND'
      is_layout_lvc            = lw_layout
      it_fieldcat_lvc          = gt_fieldcat
      i_default                = 'X'
      i_save                   = 'X'
    TABLES
      t_outtab                 = lt_pidoc
    EXCEPTIONS
      program_error            = 1.

ENDFORM.                    " FRM_DISPLAY"
*&---------------------------------------------------------------------*
*&      Form  frm_set_status
*&---------------------------------------------------------------------*
  FORM frm_set_status USING extab TYPE slis_t_extab.
  DATA: lt_fcode TYPE STANDARD TABLE OF sy-ucomm.
    APPEND '&CHNG' TO lt_fcode.
    APPEND '&MODI' TO lt_fcode.
    APPEND '&XDPL' TO lt_fcode.
  SET PF-STATUS 'STANDARD_COPY' EXCLUDING LT_FCODE.

ENDFORM.                    "frm_set_status"
*&---------------------------------------------------------------------*
*&      Form  frm_user_command
*&---------------------------------------------------------------------*
  FORM frm_user_command USING r_ucomm     LIKE sy-ucomm
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

