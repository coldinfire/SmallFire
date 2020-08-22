---
title: " SALV Layout设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Layout设置

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_layout
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_layout
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_layout.
    DATA: lo_layout  TYPE REF TO cl_salv_layout,
          lf_variant TYPE slis_vari,
          ls_key    TYPE salv_s_layout_key.
*Get layout object
    lo_layout = co_alv->get_layout( ).
*Set Layout save restriction
* 1. Set Layout Key .. Unique key identifies the Differenet ALVs
    ls_key-report = sy-repid.
    lo_layout->set_key( ls_key ).
* 2. Remove Save layout the restriction.
    lo_layout->set_save_restriction( if_salv_c_layout=>restrict_none ).
    lf_variant = 'DEFAULT'.
    lo_layout->set_initial_layout( lf_variant ).
  ENDMETHOD.                    "set_layout"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```
