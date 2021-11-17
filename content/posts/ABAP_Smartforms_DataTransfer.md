---
title: "Smartforms 数据传输"
date: 2018-07-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---



### 数据传输到Smart forms

```ABAP
DATA: wf_name TYPE rs38l_fnam.
DATA: lv_stp TYPE timestampl,
      lv_stp2(22).
DATA: lv_buffid1(18), "header
      lv_buffid2(18). "detail

DEFINE savebuffer.
  perform save_to_buffer using &1 &2.
END-OF-DEFINITION.

GET TIME STAMP FIELD lv_stp.
lv_stp2 = lv_stp.
CONCATENATE 'HEADER' lv_stp2+8(14) INTO lv_buffid1.
CONCATENATE 'DETAIL' lv_stp2+8(14) INTO lv_buffid2.
savebuffer it_header[] lv_buffid1.
savebuffer it_detail[] lv_buffid2.

CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = 'Form_name'
  IMPORTING
    fm_name            = wf_name
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
CALL FUNCTION wf_name
  EXPORTING
    user_settings      = ''
    id_header          = vl_buffid1
    id_detail          = vl_buffid2
    control_parameters = lwa_control
    output_options     = lwa_options
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
" Perform "    
FORM save_to_buffer USING t TYPE table typeid TYPE c .
  wa_indx-aedat = sy-datum.
  wa_indx-usera = sy-uname.
  wa_indx-pgmid = sy-repid.
  EXPORT t TO DATABASE indx(hk) ID typeid from wa_indx.
ENDFORM.                    "Save_To_Buffer
FORM clear_buffer USING buffid TYPE c.
  DELETE FROM DATABASE indx(hk) ID buffid.
ENDFORM.                    "Clear_Buffer
FORM restor_buffer using typeid type c changing t type table.
  IMPORT t FROM DATABASE indx(hk) ID typeid.
ENDFORM. 
```

