---
title: " SALV Status设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 设置Status

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_pf_status
        CHANGING 
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_pf_status
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_pf_status.
    DATA: lo_functions TYPE REF TO cl_salv_functions_list.
    lo_functions = co_alv->get_functions( ).
    lo_functions->set_default( abap_true ).
    "lo_functions->set_all( abap_true ).
  ENDMETHOD.                    "set_pf_status
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 自定义Status：不适用于CONTAINER模式

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_pf_status
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_pf_status
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_pf_status.
    co_alv->set_screen_status(
      pfstatus      =  'SALV_STANDARD'
      report        =  'SALV_DEMO_TABLE_SELECTIONS'
      set_functions = co_alv->c_functions_all). "显示所有通用的预设按钮
	"set_functions = co_alv->c_functions_default)."显示基本默认选择性的预设按钮
    "set_functions = co_alv->c_functions_none). "所有预设按钮都不显示
  ENDMETHOD.                    "set_pf_status
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 在预设工具栏上附加按钮：只适用于CONTAINER模式

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_pf_status
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_pf_status
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_pf_status.
   
  ENDMETHOD.                    "set_pf_status
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

