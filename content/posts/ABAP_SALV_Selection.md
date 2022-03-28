---
title: " SALV Selections 设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 选择数据

CL_SALV_SELECTIONS 可以处理 ALV 中的选择数据方式和获取选择的具体内容。

![CL_SALV_SELECTIONS](/images/ABAP/SALV15.png)

GET_SELECTED_ROWS：获取 ALV 中选中的行

- SALV_T_ROW

GET_SELECTED_COLUMNS：获取 ALV 中选中的列

- SALV_T_COLUMN

GET_CURRENT_CELL：获取 ALV 中当前选中的单元格

- SALV_S_CELL

GET_SELECTED_CELLS：获取 ALV 中选中的单元格

- SALV_T_CELL

```ABAP
DATA: select_rows TYPE salv_t_row.
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS: get_select_rows
        EXPORTING rows TYPE TABLE salv_t_row
        CHANGING  co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2_1 - Begin..............................2_1..*$*$*
  CALL METHOD get_select_rows
    IMPORTING rows   = select_rows
    CHANGING co_alv = gr_table.
*$*$*.....CODE_ADD_2_1 - End................................2_1..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD get_select_rows.
    DATA: lr_selections TYPE REF TO cl_salv_selections.
    lr_selections = co_alv->get_selections( ).
    "set selection mode"
    rows = lr_selections->get_selected_rows().
  ENDMETHOD.                    "set_selection"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

#### 选择模式

CL_SALV_SELECTIONS 中的 set_selection_mode 可以设置ALV的选择模式： 

- `if_salv_c_selection_mode=>row_column `

![if_salv_c_selection_mode](/images/ABAP/SALV14.png)

- SINGLE：单行选择
- MULTIPLE：多行选择
- CELL：单元格选择
- ROW_COLUMN：行，列选择
- NONE：无，不能选择

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS: set_selection
      CHANGING co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
  CALL METHOD set_selection
    CHANGING co_alv = gr_table.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_selection.
    DATA: lr_selections TYPE REF TO cl_salv_selections.
    lr_selections = co_alv->get_selections( ).
    "set selection mode"
    lr_selections->set_selection_mode( if_salv_c_selection_mode=>row_column ).
  ENDMETHOD.                    "set_selection"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

