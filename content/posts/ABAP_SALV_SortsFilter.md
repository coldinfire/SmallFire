---
title: " SALV 排序设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Sorts 设置

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS: set_sorts
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*   Set SORT
    CALL METHOD set_sorts
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_sorts.
    DATA: lo_sort TYPE REF TO cl_salv_sorts.
    "Get Sort object"
    lo_sort = co_alv->get_sorts( ).
    "Set the SORT on the AUART with Subtotal"
    TRY.
        CALL METHOD lo_sort->add_sort
          EXPORTING
            columnname = 'AUART'
            subtotal   = if_salv_c_bool_sap=>true.
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "set_sorts"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

