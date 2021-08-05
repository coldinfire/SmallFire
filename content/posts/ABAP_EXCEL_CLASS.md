---
title: "SAP Excel 操作实例(CLASS)"
date: 2018-12-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - abaputils

---

### SAP Excel 操作实现 (CLASS)

```ABAP
*&---------------------------------------------------------------------*
*& Report  ZEXCEL_CLASS_DEMO
*&---------------------------------------------------------------------*
REPORT  zexcel_class_demo.
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
  pspid  TYPE proj-pspid,   " Project Definition "
*  stufe  TYPE prps-stufe,  " Level in Project Hierarchy "
  posid  TYPE prps-posid,   " WBS Element "
  post1  TYPE prps-post1,   " WBS Element Desc "
*  wbs_up   TYPE ps_posid,  " WBS Up Level Element "
*  wbs_left TYPE ps_posid,  " WBS Up Element "
  usr00  TYPE prps-usr00,   " Cost Category "
  usr01  TYPE prps-usr01,   " Cost Item "
  usr02  TYPE prps-usr02,   " Cost Type "
  usr03  TYPE prps-usr03,   " Capex/Opex "
  belkz TYPE prps-belkz,    " Acct asst elem "
  fakkz TYPE prps-fakkz,    " Billing element "
END OF tp_template.
DATA: gt_template TYPE STANDARD TABLE OF tp_template.

INITIALIZATION.
  PERFORM frm_init_screen.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_file.
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
  PERFORM frm_get_data.
*&---------------------------------------------------------------------*
*&      Form  FRM_INIT_SCREEN
*&---------------------------------------------------------------------*
FORM frm_init_screen .
  DATA gs_dyntxt TYPE smp_dyntxt.
  gs_dyntxt-icon_id = '@49@'.
  gs_dyntxt-quickinfo = 'Download Template'.
  gs_dyntxt-icon_text = 'Download Template'.
  sscrfields-functxt_01 = gs_dyntxt.
ENDFORM.                    " FRM_INIT_SCREEN "
*&---------------------------------------------------------------------*
*&      Form  FRM_GET_FILENAME
*&---------------------------------------------------------------------*
FORM frm_get_filename  CHANGING p_file.
  DATA: lt_file TYPE filetable,
        lv_rc   TYPE i.

  CALL METHOD cl_gui_frontend_services=>file_open_dialog
    EXPORTING
      window_title            = '选择上传文件'
      default_extension       = '*.XLS'
      file_filter             = 'All Files (*.*)|*.*|NotePad Files(*.txt)|*.txt 
                                |Excel Files(*.xls)|*.xls|Word files(*.doc)|*.doc'
      initial_directory       = 'C:/'  "初始化的目录"
*      default_filename        =
*      with_encoding           =
*      multiselection          = 'X'       "是否可以同时打开多个文件"
    CHANGING
      file_table              = lt_file  "你打开文件名的列表"
      rc                      = lv_rc    "返回打开文件的数量"
*      user_action             =
*      file_encoding           =
    EXCEPTIONS
      file_open_dialog_failed = 1
      cntl_error              = 2
      error_no_gui            = 3
      not_supported_by_gui    = 4
      OTHERS                  = 5
          .
  IF sy-subrc EQ 0 AND lv_rc EQ 1.
    READ TABLE lt_file INDEX 1 INTO p_file.
  ENDIF.
ENDFORM.                    " FRM_GET_FILENAME "
*&---------------------------------------------------------------------*
*&      Form  FRM_DOWNLOAD_FILE
*&---------------------------------------------------------------------*
FORM frm_download_file .
  DATA: ls_filekey  TYPE wwwdatatab,
        lt_mime     TYPE STANDARD TABLE OF w3mime,
        lt_param    TYPE STANDARD TABLE OF wwwparams,
        ls_param    TYPE wwwparams,
        lv_filesize TYPE i.
  DATA: lv_filefilter       TYPE string,
        lv_default_filename TYPE string,
        lv_filename         TYPE string,
        lv_path             TYPE string,
        lv_fullpath         TYPE string,
        lv_user_action      TYPE i.
  " 判断服务器模板是否存在,并获取模板内容 "
  ls_filekey-relid = 'MI'.
  ls_filekey-objid = 'ZPSR_UPLOAD_WBS'.
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
    MESSAGE 'Template is not exist' TYPE 'E'.
    RETURN.
  ENDIF.
  " 弹出下载框函数，确定下载路径 "
  CALL METHOD cl_gui_frontend_services=>file_save_dialog
    EXPORTING
      default_file_name    = 'Test_Template.XLSX'
    CHANGING
      filename             = lv_filename
      path                 = lv_path
      fullpath             = lv_fullpath
      user_action          = lv_user_action
    EXCEPTIONS
      cntl_error           = 1
      error_no_gui         = 2
      not_supported_by_gui = 3
      OTHERS               = 4.
  IF sy-subrc <> 0.
    MESSAGE 'Please choose save path' TYPE 'I'.
    STOP.
  ENDIF.
  " 保存文件到选择的下载路径 "
  SELECT * FROM wwwparams
    INTO CORRESPONDING FIELDS OF TABLE lt_param
    WHERE relid = ls_filekey-relid
    AND objid = ls_filekey-objid.
  READ TABLE lt_param INTO ls_param WITH KEY name = 'filesize'.
  IF sy-subrc EQ 0.
    lv_filesize = ls_param-value.
  ENDIF.

  IF lv_user_action EQ cl_gui_frontend_services=>action_ok.
    CALL METHOD cl_gui_frontend_services=>gui_download
      EXPORTING
        bin_filesize = lv_filesize
        filename     = lv_fullpath
        filetype     = 'BIN'
      CHANGING
        data_tab     = lt_mime.
    IF sy-subrc <> 0.
      MESSAGE 'Error occurs when download' TYPE 'E'.
      RETURN.
    ENDIF.
  ENDIF.
ENDFORM.                    " FRM_DOWNLOAD_FILE "
*&---------------------------------------------------------------------*
*&      Form  FRM_CHECK_FILE
*&---------------------------------------------------------------------*
FORM frm_check_file .
  DATA:lv_filename TYPE string,
        lv_result TYPE c.
  lv_filename = p_file.
  CALL METHOD cl_gui_frontend_services=>file_exist
    EXPORTING
      file                 = lv_filename
    RECEIVING
      result               = lv_result
    EXCEPTIONS
      cntl_error           = 1
      error_no_gui         = 2
      wrong_parameter      = 3
      not_supported_by_gui = 4
      OTHERS               = 5.
  IF sy-subrc NE 0.
  ENDIF.
  IF lv_result = ''.
    MESSAGE 'The File Not Found In The Direct!' TYPE 'E'.
  ENDIF.
ENDFORM.                    " FRM_CHECK_FILE "
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
ENDFORM.                    " FRM_GET_DATA "
```

