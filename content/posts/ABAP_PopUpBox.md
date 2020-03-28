---
title: "可输入弹出框"
date: 2018-08-10
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---

### POPUP_GET_VALUES_USER_HELP ###
```JS
输入表格，SVAL相应的字段信息决定显示的效果：
  tabname  = 'AFKO'.
  fieldname = 'AUFNR'.
  fieldtext = '生产订单号'.
  field_attr = '02'.       //是否可输入和显示
  value = 'val'.
CALL FUNCTION 'POPUP_GET_VALUES_USER_HELP'
  EXPORTING
  *   F1_FORMNAME     = ' '
  *   F1_PROGRAMNAME  = ' '
  *   F4_FORMNAME     = ' '
  *   F4_PROGRAMNAME  = ' '
  *   FORMNAME        = ' '
  popup_title     = 'BAIDUSAP.COM'
  *   PROGRAMNAME     = ' '
  *   START_COLUMN    = '5'
  *   START_ROW       = '5'
  *   NO_CHECK_FOR_FIXED_VALUES       = ' '
  IMPORTING
    returncode      = l_ret
  TABLES
    fields          = git_tab
  EXCEPTIONS
    error_in_fields = 1
    OTHERS          = 2.
```