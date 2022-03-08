---
title: " SALV 布局列分组 "
date: 2019-06-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 布局列分组 (Columns Specific Grouping)

通常情况下 ALV 的布局(Layout)下是没有列分组的(可以把列分组理解成过滤器)，列分组就是为了方便大家在布局中选择字段轻而易举的找到所想要的字段。

布局列分组用到了类 CL_SALV_SPECIFIC_GROUPS。

- 调用 cl_salv_specific_groups->add_specific_group() 添加列分组名
- 调用 cl_salv_column_list->set_specific_group() 将 ALV 中的字段加入到列分组下

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_column_specific_group
        CHANGING
          co_alv TYPE REF TO cl_salv_table..
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
  "Set the colors to ALV display"
  CALL METHOD set_column_specific_group
      CHANGING
        co_alv = gr_table.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_column_specific_group.
    "Create the Groups"
    DATA: lr_functional_settings TYPE REF TO cl_salv_functional_settings,
          lr_specific_groups     TYPE REF TO cl_salv_specific_groups,
          lv_text                TYPE cl_salv_specific_groups=>y_text.
    lr_functional_settings = co_alv->get_functional_settings( ).
    lr_specific_groups = lr_functional_settings->get_specific_groups( ).
    "Add group GRP1"
    TRY.
        lv_text = 'External supplier'.
        lr_specific_groups->add_specific_group( id   = 'GRP1'
                                                text = lv_text ).
      CATCH cx_salv_existing.                           
    ENDTRY.
    "Add group GRP2"
    TRY.
        lv_text = 'Internal supplier'.
        lr_specific_groups->add_specific_group( id   = 'GRP2'
                                                text = lv_text ).
      CATCH cx_salv_existing.
    ENDTRY.
    "Assign the group to columns"
    DATA: lr_columns TYPE REF TO cl_salv_columns_table,
          lr_column  TYPE REF TO cl_salv_column_list,
          lt_cols    TYPE salv_t_column_ref,
          ls_cols    LIKE LINE OF lt_cols.
    lr_columns = co_alv->get_columns( ).
    lt_cols = lr_columns->get( ).
    TRY.
        "隐藏KUNNR字段，Customer"
        lr_column ?= lr_columns->get_column( 'KUNNR' ).
        lr_column->set_technical( if_salv_c_bool_sap=>true ).
      CATCH cx_salv_not_found.
    ENDTRY.
    LOOP AT lt_cols INTO ls_cols.
      lr_column ?= ls_cols-r_column.    "Narrow casting"
      CASE ls_cols-columnname+0(2).
        "将以ET开始的表字段分配给group GRP1,然后设置为不显示"
        WHEN 'ET'.
          lr_column->set_specific_group( id = 'GRP1' ).
          lr_column->set_visible( '' ).
        "将以IT开始的表字段分配给group GRP2,然后设置为不显示"
        WHEN 'IT'.
          lr_column->set_specific_group( id = 'GRP2' ).
          lr_column->set_visible( '' ).
      ENDCASE.
      CLEAR ls_cols.
    ENDLOOP.
  ENDMETHOD.                    "set_column_specific_group"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

