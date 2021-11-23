---
title: " Smartforms 打印成PDF "
date: 2018-12-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 1.程序中使用 Smartform 模板

```ABAP
"SMARTFORMS变量定义"
DATA: form_name TYPE tdsfname VALUE 'ZZ_TEST', "Smartforms Name"
      function_name   type rs38l_fnam, "Function Name"
      control   type ssfctrlop,  "Control structure"
      option    type ssfcompop,  "Smart Composer (transfer) options"
      result    type ssfcrescl.  "Return value at end of form printing"

"获取SMARTFORMS经由SAP编译后的函数名"
  CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
    EXPORTING
      FORMNAME           = form_name
*     VARIANT            = ' '
*     direct_call        = ' '
    IMPORTING
      FM_NAME            = function_name
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
  clear: control.
  control-no_open = abap_true.
  control-no_close = abap_true.
  control-langu = sy-langu.
  control-no_dialog = space.    "不显示对话框"
  control-preview = abap_false. "不预览"
  control-getotf = abap_true.   "取得OTF数据"
  clear: option.
  option-tddest = ''.   "指定打印机"
  option-tdimmed = abap_true.  "直接打印"
  option-tdcopies = 1.  "打印的次数"
" Open pinting request "
CALL FUNCTION 'SSF_OPEN'
    exporting
*       ARCHIVE_PARAMETERS = ARCHIVE_PARAMETERS
      user_settings      = ''
*       MAIL_SENDER        = MAIL_SENDER
*       MAIL_RECIPIENT     = MAIL_RECIPIENT
*       MAIL_APPL_OBJ      = MAIL_APPL_OBJ
      output_options     = option
      control_parameters = control
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
CALL FUNCTION function_name
  EXPORTING
    CONTROL_PARAMETERS = control
    OUTPUT_OPTIONS     = option
  IMPORTING
    JOB_OUTPUT_OPTIONS = result "输出参数"
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

### 2.打印成 PDF

```ABAP
DATA:
  bin_filesize TYPE i, 
  " Internal table to hold the OTF data "
  t_otf TYPE itcoo OCCURS 0 WITH HEADER LINE,
  " Internal table to hold OTF data recd from the SMARTFORM "
  result TYPE ssfcrescl,
  " Internal table to hold the data from the FM CONVERT_OTF "
  t_pdf_tab LIKE tline OCCURS 0 WITH HEADER LINE.
  
t_otf[] = result-otfdata[].
* FM -> CONVERT_OTF 将 OTF 转为 PDF
 CALL FUNCTION 'CONVERT_OTF'
   EXPORTING
     format = 'PDF'
     max_linewidth = 132
   IMPORTING
     bin_filesize = bin_filesize
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

