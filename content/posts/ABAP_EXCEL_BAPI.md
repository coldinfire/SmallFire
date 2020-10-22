---
title: "SAP Excel 操作实例(BAPI)"
date: 2018-11-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - Excel
  - abaputils
---

### SAP Excel 操作实现 (BAPI)

```html
*&---------------------------------------------------------------------*
*& Report  ZEXCEL_BAPI
*&---------------------------------------------------------------------*
REPORT  zexcel_bapi.
TABLES: sscrfields,rlgrap.
TYPE-POOLS: slis.
" TEXT-000 : Select file to upload "
" TEXT-001 : Select file "
SELECTION-SCREEN BEGIN OF BLOCK file_name WITH FRAME TITLE text-000.
SELECTION-SCREEN BEGIN OF LINE.
SELECTION-SCREEN COMMENT 1(31) text-001 FOR FIELD p_file.
PARAMETERS: p_file LIKE rlgrap-filename .
SELECTION-SCREEN END OF LINE.
SELECTION-SCREEN END OF BLOCK file_name.

SELECTION-SCREEN FUNCTION KEY 1.

TYPES: BEGIN OF tp_template,
  pspid  TYPE proj-pspid,   " Project Definition
*  stufe  TYPE prps-stufe,   " Level in Project Hierarchy
  posid  TYPE prps-posid,   " WBS Element
  post1  TYPE prps-post1,   " WBS Element Desc
*  wbs_up   TYPE ps_posid,   " WBS Up Level Element
*  wbs_left TYPE ps_posid,   " WBS Up Element
  usr00  TYPE prps-usr00,   " Cost Category
  usr01  TYPE prps-usr01,   " Cost Item
  usr02  TYPE prps-usr02,   " Cost Type
  usr03  TYPE prps-usr03,   " Capex/Opex
  belkz TYPE prps-belkz,    " Acct asst elem.
  fakkz TYPE prps-fakkz,    " Billing element
END OF tp_template.
DATA: gt_template TYPE STANDARD TABLE OF tp_template.

INITIALIZATION.
  " 设置选择屏幕下载模板按钮 "
  PERFORM frm_init_screen.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
  " 给文件搜索帮助 "
  PERFORM frm_get_filename CHANGING p_file.

AT SELECTION-SCREEN.
  IF sscrfields-ucomm EQ 'FC01'.
    " 从服务器(SMW0)下载模板 "
    PERFORM frm_download_file.
  ELSEIF sscrfields-ucomm EQ 'ONLI'.
    " 查看本地文件是否存在 "
    PERFORM frm_check_file.
  ENDIF.

START-OF-SELECTION.
  " 将Excel模板转换为内表数据 "
  PERFORM frm_get_data.

*&---------------------------------------------------------------------*
*&      Form  FRM_INIT_SCREEN
*&---------------------------------------------------------------------*
FORM frm_init_screen .
  " Dynpro text define "
  DATA: gs_dyntxt TYPE smp_dyntxt.
  gs_dyntxt-icon_id = '@49@'.
  gs_dyntxt-quickinfo = 'Download Template'.
  gs_dyntxt-icon_text = 'Download Template'.
  sscrfields-functxt_01 = gs_dyntxt.
ENDFORM.                    " FRM_INIT_SCREEN
*&---------------------------------------------------------------------*
*&      Form  FRM_GET_FILENAME
*&---------------------------------------------------------------------*
FORM frm_get_filename  CHANGING p_file.
" Method 1 "
  CALL FUNCTION 'WS_FILENAME_GET'
    EXPORTING
      def_path           = 'C:\'
      mask               = ',Excel(*.xls),*.XLS,*.XLSX,*.xlsx,'
      mode               = 'O'
      title              = 'Please choose file'
    IMPORTING
      filename           = p_file
*   RC                   =
    EXCEPTIONS
      inv_winsys         = 1
      no_batch           = 2
      selection_cancel   = 3
      selection_error    = 4
      OTHERS             = 5.
  IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*         WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
" Method 2 "
*  CALL FUNCTION 'KD_GET_FILENAME_ON_F4'
*    CHANGING
*      file_name = cv_file.
*  IF sy-subrc <> 0.
*    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
*    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
*    EXIT.
*  ENDIF.
ENDFORM.                    " FRM_GET_FILENAME
*&---------------------------------------------------------------------*
*&      Form  FRM_DOWNLOAD_FILE
*&---------------------------------------------------------------------*
FORM frm_download_file .
  DATA: ls_filekey  TYPE wwwdatatab,
        lt_mime     TYPE STANDARD TABLE OF w3mime.
  DATA: lv_download_path TYPE rlgrap-filename,
        lv_rc TYPE sy-subrc,
        lv_temp TYPE c,
        lv_message TYPE string.
  ls_filekey-relid = 'MI'.
  ls_filekey-objid = 'ZPSR_UPLOAD_WBS'. " SMW0定义的对象名称
  " 判断模板是否存在 "
  CALL FUNCTION 'WWWDATA_IMPORT'
    EXPORTING
      key               = ls_filekey
    TABLES
      mime              = lt_mime
    EXCEPTIONS
      wrong_object_type = 1
      import_error      = 2
      OTHERS            = 3.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
         WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    STOP.
  ENDIF.

" 判断服务器模板是否存在 "
*SELECT SINGLE relid objid INTO CORRESPONDING FIELDS OF ls_filekey
*  FROM wwwdata
*  WHERE srtf2 EQ 0
*  AND relid EQ 'MI'
*  AND objid EQ 'ZPSR_UPLOAD_WBS'.      "SY-CPROG.上传的文件对象名"
*IF sy-subrc NE 0.
*  MESSAGE 'Template is not exist' TYPE 'E'.
*  RETURN.
*ENDIF.

  " 选择下载路径 "
  CALL FUNCTION 'WS_FILENAME_GET'
  EXPORTING
    def_path           = 'C:\'
    mask               = ',Excel(*.xls),*.XLS,*.XLSX,*.xlsx,'
    mode               = 'S'
    title              = 'Please choose file'
  IMPORTING
    filename           = lv_download_path
*   RC                   =
  EXCEPTIONS
    inv_winsys         = 1
    no_batch           = 2
    selection_cancel   = 3
    selection_error    = 4
    OTHERS             = 5.
  IF sy-subrc <> 0.
    MESSAGE 'Please choose save path' TYPE 'I'.
    STOP.
  ENDIF.
  " 下载模板 "
  CALL FUNCTION 'DOWNLOAD_WEB_OBJECT'
    EXPORTING
      key         = ls_filekey
      destination = lv_download_path
    IMPORTING
      rc          = lv_rc
    CHANGING
      temp        = lv_temp.
  IF lv_rc EQ 0.
    CONCATENATE 'The File Download Directory:' lv_download_path INTO lv_message.
    MESSAGE lv_message TYPE 'S'.
  ELSE.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
        WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    " FRM_DOWNLOAD_FILE
*&---------------------------------------------------------------------*
*&      Form  FRM_EXCUTE_CHECK
*&---------------------------------------------------------------------*
FORM frm_check_file .
  DATA l_file_exist TYPE c.
  CLEAR l_file_exist.
  CALL FUNCTION 'WS_QUERY'
    EXPORTING
      filename = p_file
      query    = 'FE'
    IMPORTING
      return   = l_file_exist.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
  IF l_file_exist <> 1 OR l_file_exist IS INITIAL.
    MESSAGE 'File not found' TYPE 'E'.
  ENDIF.
ENDFORM.                    " FRM_EXCUTE_CHECK "
*&---------------------------------------------------------------------*
*&      Form  FRM_GET_DATA
*&---------------------------------------------------------------------*
FORM frm_get_data .
  DATA: lt_intern TYPE STANDARD TABLE OF alsmex_tabline,
        ls_intern LIKE LINE OF lt_intern.
  DATA: ls_template LIKE LINE OF gt_template,
        lv_index  LIKE sy-tabix.
  FIELD-SYMBOLS: <fv_value> .
  " 将Excel数据转换到内表数据
  CALL FUNCTION 'ALSM_EXCEL_TO_INTERNAL_TABLE'
    EXPORTING
      filename                = p_file
      i_begin_col             = 1
      i_begin_row             = 2
      i_end_col               = 50
      i_end_row               = 65536
    TABLES
      intern                  = lt_intern
    EXCEPTIONS
      inconsistent_parameters = 1
      upload_ole              = 2
      OTHERS                  = 3.
  IF sy-subrc <> 0.
    MESSAGE 'Excel parsing failed' TYPE 'S' DISPLAY LIKE 'E' .
    LEAVE LIST-PROCESSING.
  ENDIF.
  LOOP AT lt_intern INTO ls_intern.
    lv_index = ls_intern-col.
    UNASSIGN <fv_value>.
    ASSIGN COMPONENT lv_index OF STRUCTURE ls_template TO <fv_value>.
    MOVE ls_intern-value TO <fv_value>.
    AT END OF row.
      APPEND ls_template TO gt_template.
      CLEAR: ls_template.
    ENDAT.
    CLEAR:ls_intern.
  ENDLOOP.
  CHECK gt_template[] IS NOT INITIAL.
ENDFORM.                    " FRM_GET_DATA
```

