---
title: "Smartforms调用模板"
date: 2018-07-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



#### 程序中使用Smartform模板

```JS
"SMARTFORMS变量定义
DATA: lv_fm_name   type rs38l_fnam, "Function Name
      ls_control   type ssfctrlop,  "Control structure
      ls_option    type ssfcompop,  "Smart Composer (transfer) options
      ls_ssfcrescl type ssfcrescl.  "Return value at end of form printing

"获取SAP编译后的函数名
  CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
    EXPORTING
      FORMNAME           = 'smartforms_name' 
*     VARIANT            = ' '
*     direct_call        = ' '
    IMPORTING
      FM_NAME            = lv_fm_name  "接收返回值
    EXCEPTIONS
      NO_FORM            = 1
      NO_FUNCTION_MODULE = 2
      OTHERS             = 3.
  IF sy-subrc <> 0.
    " Error handling
     MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
             WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
     EXIT.
  ENDIF.
             
 " Parameters when call smartforms
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

" Open pinting request
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
	" Error handling
     MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
             WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
     EXIT.
  ENDIF.
  
"Call smartforms to print  
CALL FUNCTION lv_generated_func
  EXPORTING
    CONTROL_PARAMETERS = ls_control
    OUTPUT_OPTIONS     = ls_option
  IMPORTING
    JOB_OUTPUT_OPTIONS = OPTION "输出参数
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
"Close print request
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