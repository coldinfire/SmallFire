---

title: " ALV Demo "
date: 2018-08-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---







```JS
*&---------------------------------------------------------------------*
*& Report  ZWR_PI_LOG
*&
*&---------------------------------------------------------------------*
*&
*&
*&---------------------------------------------------------------------*

REPORT  zwr_pi_log.

TABLES: lqua ,zpidoc.
TYPE-POOLS: slis.

DATA: lt_pidoc TYPE STANDARD TABLE OF zpidoc,
      ls_pidoc TYPE zpidoc .

"ALV Data
DATA: gt_fieldcat TYPE lvc_t_fcat,
      gs_fieldcat TYPE lvc_s_fcat,
      lw_layout TYPE lvc_s_layo.

"Select screen
PARAMETERS: p_lgnum TYPE lqua-lgnum OBLIGATORY.
PARAMETERS: p_status TYPE zpidoc-STATUS.

SELECT-OPTIONS: s_lgtyp FOR lqua-lgtyp,
                s_create FOR sy-datum OBLIGATORY,
                s_ctdate FOR sy-datum ,
                s_pidoc FOR ZPIDOC-PIDOC,
                s_lgpla FOR lqua-lgpla,
                s_lenum FOR lqua-lenum,
                s_matnr FOR lqua-matnr.

"Event
START-OF-SELECTION .
  "Pre-check
  PERFORM frm_pre_check.
  "Extract data
  PERFORM frm_extract_data.
  "ALV
  PERFORM frm_display.
END-OF-SELECTION.
*&---------------------------------------------------------------------*
*&      Form  FRM_PRE_CHECK
*&---------------------------------------------------------------------*
  FORM frm_pre_check .
  DATA:e_message TYPE char100.
  AUTHORITY-CHECK OBJECT 'L_LGNUM'
           "ID 'ACTVT' FIELD '03'
           ID 'LGNUM' FIELD p_lgnum.
  IF sy-subrc <> 0.
    CONCATENATE 'You have no authorization of warehouse ' p_lgnum INTO e_message RESPECTING BLANKS.
    MESSAGE e_message TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
  ENDFORM.                    " FRM_PRE_CHECK
  *&---------------------------------------------------------------------*
  *&      Form  FRM_EXTRACT_DATA
  *&---------------------------------------------------------------------*
  FORM frm_extract_data .
  SELECT *
     FROM zpidoc
     INTO TABLE lt_pidoc
     WHERE lgnum EQ p_lgnum
      AND STATUS EQ p_status
      AND lgtyp  IN s_lgtyp
      AND LGPLA  IN s_LGPLA
      AND PIDOC  IN s_PIDOC
      AND ERDAT  In s_create
      AND count_date IN s_ctdate
      AND lenum  IN s_lenum
      AND matnr  In s_matnr
      .
  IF lt_pidoc IS INITIAL.
    MESSAGE 'No data.' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
  ENDFORM.                    " FRM_EXTRACT_DATA
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

ENDFORM.                    " FRM_DISPLAY

*&---------------------------------------------------------------------*
*&      Form  frm_set_status
*&---------------------------------------------------------------------*
  FORM frm_set_status USING extab TYPE slis_t_extab.

  DATA: ls_slis_extab TYPE slis_extab.

  SET PF-STATUS 'STATUS1' EXCLUDING extab.

ENDFORM.                    "frm_set_status

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

ENDFORM.                    "frm_user_command
```

