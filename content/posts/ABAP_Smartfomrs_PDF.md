---
title: "Smartforms打印成PDF"
date: 2018-12-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



#### 1.程序中使用Smartform模板

```JS
"SMARTFORMS变量定义"
DATA: lv_form_name TYPE tdsfname VALUE 'ZZ_TEST', "Smartforms Name"
      lv_fm_name   type rs38l_fnam, "Function Name"
      ls_control   type ssfctrlop,  "Control structure"
      ls_option    type ssfcompop,  "Smart Composer (transfer) options"
      ls_ssfcrescl type ssfcrescl.  "Return value at end of form printing"

"获取SMARTFORMS经由SAP编译后的函数名"
  CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
    EXPORTING
      FORMNAME           = lv_form_name
*     VARIANT            = ' '
*     direct_call        = ' '
    IMPORTING
      FM_NAME            = lv_fm_name  "接收返回值"
    EXCEPTIONS
      NO_FORM            = 1
      NO_FUNCTION_MODULE = 2
      OTHERS             = 3.
  IF sy-subrc <> 0.
     MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
             WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
     EXIT.
  ENDIF.  
 " Parameters when call smartforms "
  clear: ls_control.
  ls_control-no_open = 'X'.
  ls_control-no_close = 'X'.
  ls_control-langu = sy-langu.
  ls_control-no_dialog = space.
  ls_control-preview = 'X'.
  clear: ls_option.
  ls_option-tddest = ''.
  ls_option-tdimmed = ''.
  ls_option-tdcopies = 1.
" Open pinting request "
CALL FUNCTION 'SSF_OPEN'
    exporting
*       ARCHIVE_PARAMETERS = ARCHIVE_PARAMETERS
      user_settings      = ''
*       MAIL_SENDER        = MAIL_SENDER
*       MAIL_RECIPIENT     = MAIL_RECIPIENT
*       MAIL_APPL_OBJ      = MAIL_APPL_OBJ
      output_options     = ls_option
      control_parameters = ls_control
*   IMPORTING
*       JOB_OUTPUT_OPTIONS = JOB_OUTPUT_OPTIONS
    exceptions
      formatting_error   = 1
      internal_error     = 2
      send_error         = 3
      user_canceled      = 4.
  IF sy-subrc ne 0.
     MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
             WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
     EXIT.
  ENDIF.
"Call smartforms to print"  
CALL FUNCTION lv_fm_name
  EXPORTING
    CONTROL_PARAMETERS = ls_control
    OUTPUT_OPTIONS     = ls_option
  IMPORTING
    JOB_OUTPUT_OPTIONS = ls_ssfcrescl "输出参数"
  EXCEPTIONS
    FORMATTING_ERROR   = 1
    INTERNAL_ERROR     = 2
    SEND_ERROR         = 3
    USER_CANCELED      = 4
    OTHERS             = 5.
 IF SY-SUBRC NE 0.
   MESSAGE id sy-msgid type sy-msgty number sy-msgno
         with sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4 . 
 ENDIF.
```

#### 2.打印成PDF

```JS
" Internal tables declaration "
DATA:
  " Internal table to hold the OTF data "
  t_otf TYPE itcoo OCCURS 0 WITH HEADER LINE,
  " Internal table to hold OTF data recd from the SMARTFORM "
  t_otf_from_fm TYPE ssfcrescl,
  " Internal table to hold the data from the FM CONVERT_OTF "
  t_pdf_tab LIKE tline OCCURS 0 WITH HEADER LINE.
  t_otf[] = t_otf_from_fm-otfdata[].
* FM -> CONVERT_OTF 将 OTF 转为 PDF
 CALL FUNCTION 'CONVERT_OTF'
  EXPORTING
   format = 'PDF'
   max_linewidth = 132
  IMPORTING
   bin_filesize = w_bin_filesize
  TABLES
   otf = t_otf
   lines = t_pdf_tab
  EXCEPTIONS
   err_max_linewidth = 1
   err_format = 2
   err_conv_not_possible = 3
   err_bad_otf = 4
   OTHERS = 5.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
* 显示保存对话框
CALL METHOD cl_gui_frontend_services=>file_save_dialog
  CHANGING
    filename = w_file_name
    path = w_file_path
    fullpath = w_full_path
* USER_ACTION =
* FILE_ENCODING =
  EXCEPTIONS
    cntl_error = 1
    error_no_gui = 2
    not_supported_by_gui = 3
    OTHERS = 4.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
* FM -> GUI_DOWNLOAD 下载PDF 
CALL FUNCTION 'GUI_DOWNLOAD'
  EXPORTING
    bin_filesize = w_bin_filesize
    filename = w_full_path
    filetype = 'BIN'
  TABLES
    data_tab = t_pdf_tab
"Close print request"
CALL FUNCTION 'SSF_CLOSE'
  IMPORTING
    JOB_OUTPUT_INFO  = ls_ssfcrescl
  EXCEPTIONS
    FORMATTING_ERROR = 1
    INTERNAL_ERROR   = 2
    SEND_ERROR       = 3
    OTHERS           = 4.
IF sy-subrc ne 0.
  "Error
ENDIF.
```

