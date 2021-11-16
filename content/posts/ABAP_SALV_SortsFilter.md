---
title: " SALV 排序&分类汇总&过滤 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 排序设置

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS: set_sort
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_sort
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_sort.
    DATA: lo_sort TYPE REF TO cl_salv_sorts.
    "Get Sort object"
    lo_sort = co_alv->get_sorts( ).
    "Set the SORT on the AUART with Subtotal"
    TRY.
        CALL METHOD lo_sort->add_sort
          EXPORTING
            columnname = 'AUART'
            position   = 1 "排序的顺序，如果根据多个字段来排时，决定哪个先排"
            sequence   = if_salv_c_sort=>sort_up "升序"
            subtotal   = if_salv_c_bool_sap=>true."是否需要以此字段进行分类小计"
        CALL METHOD lo_sort->add_sort
          EXPORTING
            columnname = 'ERDAT'
            position   = 2 
            sequence   = if_salv_c_sort=>sort_down "降序"
            subtotal   = if_salv_c_bool_sap=>false.
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "set_sort"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 分类汇总：Apply Aggregations

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_aggregation
        CHANGING
          co_alv  TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_aggregation
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
 METHOD set_aggregation.
    DATA: lo_aggrs TYPE REF TO cl_salv_aggregations.
    lo_aggrs = co_alv->get_aggregations( ).
    "Add TOTAL for COLUMN NETWR"
    "如果不先进行排序，则只有汇总，不会进行分类小计"
    TRY.
        CALL METHOD lo_aggrs->add_aggregation
          EXPORTING
            columnname  = 'NETWR'
            aggregation = if_salv_c_aggregation=>total.
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
    ENDTRY.
    "Bring the total line to top"
    lo_aggrs->set_aggregation_before_items( ).
  ENDMETHOD.                    "set_aggregation"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 过滤设置

SALV 的标准按钮中已经有过滤的功能，我们也可以在初始显示的时候就设置过滤条件。

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_filter
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*Set Filters
    CALL METHOD set_filter
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
 METHOD set_filter.
    DATA: lo_filter TYPE REF TO cl_salv_filters.
    lo_filter = co_alv->get_filters( ).
    "Set the filter for the column ERDAT the filter criteria works exactly same as"
    "any RANGE or SELECT-OPTIONS works."
    TRY.
        CALL METHOD lo_filter->add_filter
          EXPORTING
            columnname = 'ERDAT'
            sign       = 'I'
            option     = 'EQ'
            low        = '20190602'
            high       = '201906*'.
      CATCH cx_salv_not_found .                         "#EC NO_HANDLER"
      CATCH cx_salv_data_error .                        "#EC NO_HANDLER"
      CATCH cx_salv_existing .                          "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "set_filter"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

