---
title: "Smartforms 统计打印次数"
date: 2018-07-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 打印次数的记录

通过参数控制和对调用 Smart forms 完成后返回的参数值，判断用户是否完成Form打印。

```html
DATA: fm_name TYPE rs38l_fnam.
DATA: control_param TYPE ssfctrlop .
DATA: composer_param TYPE ssfcompop .
DATA: output_info TYPE ssfcrescl.
composer_param-tdiexit = 'X'.   " 预览打印后直接退出 "
* 根据SmartForm名称获得Form的Function Name
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
* 调用Function实现打印，并判断是否输出到打印机
CALL FUNCTION fm_name
  EXPORTING
    control_parameters = control_param
    output_options     = composer_param
  IMPORTING
    job_output_info    = output_info
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
IF sy-subrc = 0.  
  if output_info-outputdone = 'X'. " 是否已经输出到打印机 "  
    " 增加打印次数，并把它写到相应的自定义表中 " 
  endif.  
ENDIF. 
```

