---
title: " SALV Display 设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### Display设置

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_display_setting
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_display_setting
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_display_setting.
    DATA: lo_display TYPE REF TO cl_salv_display_settings.
*Get display object
    lo_display = co_alv->get_display_settings( ).
*Set ZEBRA pattern
    lo_display->set_striped_pattern( 'X' ).
*Title to ALV
    lo_display->set_list_header( 'ALV Test for Display Settings' ).
  ENDMETHOD.                    "SET_DISPLAY_SETTING"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```