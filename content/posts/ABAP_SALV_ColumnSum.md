---
title: " SALV 汇总设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Apply Aggregations

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:
      set_aggregations
        CHANGING
          co_alv  TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_aggregations
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
 METHOD set_aggregations.
    DATA: lo_aggrs TYPE REF TO cl_salv_aggregations.
    lo_aggrs = co_alv->get_aggregations( ).
*Add TOTAL for COLUMN NETWR
    TRY.
        CALL METHOD lo_aggrs->add_aggregation
          EXPORTING
            columnname  = 'NETWR'
            aggregation = if_salv_c_aggregation=>total.
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
    ENDTRY.
*Bring the total line to top
    lo_aggrs->set_aggregation_before_items( ).
  ENDMETHOD.                    "set_aggregations"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```
