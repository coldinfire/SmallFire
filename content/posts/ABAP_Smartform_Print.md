---
title: " Smartforms 连续打印 "
date: 2018-07-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### Smartforms 设置参数实现连续打印

调用 smartforms 时直接调用打印机打印，不出现打印预览窗口

```ABAP
DATA: fm_name TYPE rs38l_fnam.
DATA: control_param TYPE ssfctrlop .
DATA: composer_param TYPE ssfcompop .
	
control_param-langu = sy-langu.
control_param-no_open = 'X'.
control_param-no_close = 'X'.
control_param-no_dialog = 'X'.  " Not show dialog "
composer_param-tddest = 'LP01'. " Printer "
composer_param-tdimmed = 'X'.   " Print Immediately (Print Parameters) "
composer_param-tddelete = 'X'.  " Delete After Printing (Print Parameters) "   
composer_param-tdcopies = 2.    " 重复打印次数"
composer_param-tdpageslct = '1-3'. "控制打印的页码，1,2/1-3"
* 根据 SmartForm 名称获得 Form 的 Function Name
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname = 'FORM_NAME'
  IMPORTING
     fm_name = fm_name
  EXCEPTIONS
     no_form = 1
     no_function_module = 2
    OTHERS = 3 .
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
* 调用Function 实现打印
CALL FUNCTION fm_name
  EXPORTING
    control_parameters = control_param
    output_options     = composer_param
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
  WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
```

