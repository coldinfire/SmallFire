---
title: " SALV Filter设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### 过滤设置

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_filters
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*Set Filters
    CALL METHOD set_filters
      CHANGING
        co_alv = o_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
 METHOD set_filters.
    DATA: lo_filters TYPE REF TO cl_salv_filters.
    lo_filters = co_alv->get_filters( ).
*Set the filter for the column ERDAT the filter criteria works exactly same as any
*RANGE or SELECT-OPTIONS works.
    TRY.
        CALL METHOD lo_filters->add_filter
          EXPORTING
            columnname = 'ERDAT'
            sign       = 'I'
            option     = 'EQ'
            low        = '20091214'
*           high       =
            .
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "set_filters"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```
