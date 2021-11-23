---
title: " Smartform 循环打印&页码统计 "
date: 2018-07-31
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### Smartforms 循环打印数据

在 smartform 的使用中，偶尔会遇到用户要求按某一条件进行数据的分组打印，并进行页码的统计和区分，这里记录下自己使用的方法。

```ABAP
FORM sub_data_print .
  SORT itab_total BY matkl budat zcdnr.
  SORT s_fenlei BY low.
  "ALV Data" 
  DATA: fm_name TYPE rs38l_fnam.
  DATA: ls_control_param TYPE ssfctrlop .
  DATA: ls_composer_param TYPE ssfcompop .
  DATA: outopt TYPE ssfcresop.
  DATA: i_job_output_info TYPE ssfcrescl.
  
  DATA: itab_print LIKE TABLE OF wand.
  DATA: mid TYPE c LENGTH 20.

  ls_control_param-langu = sy-langu.
  ls_control_param-no_open = 'X'.
  ls_control_param-no_close = 'X'.

  CALL FUNCTION 'SSF_OPEN'
    EXPORTING
      control_parameters = ls_control_param
      output_options     = ls_composer_param
    IMPORTING
      job_output_options = outopt
    EXCEPTIONS
      formatting_error   = 1
      internal_error     = 2
      send_error         = 3
      user_canceled      = 4
      OTHERS             = 5.

  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
    EXIT.
  ENDIF.
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

  LOOP AT s_fenlei.
    REFRESH itab_print.
    LOOP AT itab_total INTO wand WHERE fenlei = s_fenlei-low.
      APPEND wand TO itab_print.
    ENDLOOP.
    IF itab_print IS INITIAL.
      CONTINUE.
    ENDIF.
   CONCATENATE sy-uname sy-uzeit INTO mid.
   "将内表数据存入 ABAP 内存"
   EXPORT a = itab_print TO DATABASE indx(hk) ID mid.
   CALL FUNCTION fm_name
     EXPORTING
       control_parameters = ls_control_param
       output_options     = ls_composer_param
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
   "删除内存数据"
    DELETE FROM DATABASE indx(hk) ID mid.
  ENDLOOP.
  CALL FUNCTION 'SSF_CLOSE'
    IMPORTING
      job_output_info  = i_job_output_info
    EXCEPTIONS
      formatting_error = 1
      internal_error   = 2
      send_error       = 3
      OTHERS           = 4.
  IF sy-subrc <> 0.
    MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  ENDIF.
ENDFORM.                    " SUB_DATA_PRINT "
```

