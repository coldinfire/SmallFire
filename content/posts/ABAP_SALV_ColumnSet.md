---
title: " SALV 字段目录设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Fieldcat 设置

Function ALV、OO ALV 可通过 Fieldcat 对列进行相关设置，SALV 也有类似于这样的东西，只不过不是 Fieldcat，而是通过 `CL_SALV_COLUMN` 对象来实现的。可以修改列的相关属性，包括列宽度，列的名字，删除列，列显示的位置，列字段参照的 DDIC 对象等。

![CL_SALV_COLUMN](/images/ABAP/SALV5.png)

#### CL_SALV_COLUMNS_TABLE

设置整体的属性。当使用 set_optimize 时，单列属性设置 set_output_length 方法无效。

![CL_SALV_COLUMNS_TABLE](/images/ABAP/SALV6.png)

#### CL_SALV_COLUMN_TABLE

设置单个列的属性。

![CL_SALV_COLUMN_TABLE](/images/ABAP/SALV7.png)

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
*Set the various column properties
    METHODS:set_columns
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*Setting up the Columns
    CALL METHOD me->set_columns
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_columns.
    DATA: lo_cols TYPE REF TO cl_salv_columns_table.
    DATA: lo_column TYPE REF TO cl_salv_column_table.
    "Get all the Columns"
    lo_cols = go_alv->get_columns( ).
    "Set the Column optimization"
    lo_cols->set_optimize( 'X' ).
    "Process individual columns"
    DATA: ls_color TYPE lvc_s_colo. "颜色设置"
    "Change the properties of the Columns KUNNR"
    TRY.
        lo_column ?= lo_cols->get_column( 'KUNNR' ). "需要处理的列"
        lo_column->set_long_text( 'Sold-To Party' ).
        lo_column->set_medium_text( 'Sold-To Party' ).
        lo_column->set_short_text( 'Sold-To' ).
        lo_column->set_output_length( 10 ).
        "隐藏设置"
        lo_column->set_visible( if_salv_c_bool_sap=>false ).  "隐藏在Layout里"
        lo_column->set_technical( if_salv_c_bool_sap=>true ). "完全隐藏"
        "颜色设置"
        CLEAR ls_color.
        ls_color-col = '6'.
        ls_color-int = '1'.
        ls_color-inv = '0'.
        lo_column->set_color( ls_color ).
        "数值为空时，不显示0"
        lo_column ?= lo_cols->get_column( 'BET01' ).
        lo_column->set_zero( ' ' ).
        "热点列设置"
        lo_column ?= lo_cols->get_column( 'AUFNR' ).
        lo_column->set_cell_type( if_salv_c_cell_type=>hotspot ).
        ...
      CATCH cx_salv_not_found.  "#EC NO_HANDLER"
    ENDTRY.
  ENDMETHOD.                    "SET_COLUMNS"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 单元格 Style设置

单元格 style 在 ALV 中扮演着十分重要的角色，可以通过单元格 style 将单元格设置成文本、checkbox、热点(hotspot)、链接(link)、按钮、下拉列表等。

![IF_SALV_C_CELL_TYPE](/images/ABAP/SALV10.png)

- 在 SALV 最终输出内表中定义一个保存 style 的字段，字段类型为 salv_t_int4_column(表类型)，表类型中的结构(structure)定义如下，由一个列名和对应值组成。当不指定列名，只对 value 赋值，意味着整行的单元格都应用同一个style
  - ![SALV_S_INT4_COLUMN](/images/ABAP/SALV11.png)
- 将单元格的 style 保存到内表定义的字段
- 调用 cl_salv_columns_table->set_cell_type_column( ) 指定保存 style 的字段

```ABAP
  METHOD get_data.
    FIELD-SYMBOLS: <lfs_vbak> LIKE LINE OF gt_vbak.
    DATA: lt_celltype TYPE salv_t_int4_column.
          ls_celltype LIKE LINE OF lt_celltype.
    LOOP AT gt_vbak ASSIGNING <lfs_vbak>.
      CLEAR: lt_celltype.
      IF sy-tabix = 2.
        "第二行VBELN列设定hotspot"
        ls_celltype-columnname = 'VBELN'.
        ls_celltype-value      = if_salv_c_cell_type=>hotspot.
        APPEND ls_celltype TO lt_celltype.
      ELSEIF sy-tabix = 3.
        "第三行ERDAT单元格设定成按钮"
        ls_celltype-columnname = 'ERDAT'.
        ls_celltype-value      = if_salv_c_cell_type=>button.
        APPEND ls_celltype TO lt_celltype.
      ELSEIF sy-tabix = 5.
        "不指定列就意味着整个第5行都设置成hotspot"
        ls_celltype-columnname = ''.
        ls_celltype-value      = if_salv_c_cell_type=>hotspot.
        APPEND ls_celltype TO lt_celltype.
      ENDIF.
      <lfs_vbak>-t_celltype = lt_celltype.
    ENDLOOP.
  ENDMETHOD.                    "get_data"

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_columns.
    "Get all the Columns"
    DATA: lo_cols TYPE REF TO cl_salv_columns_table.
    lo_cols = o_alv->get_columns( ).
    lo_cols->set_optimize( 'X' ).
    "设置单元格style的字段"
    TRY.
        lo_cols->set_cell_type_column( 'T_CELLTYPE' ).
      CATCH cx_salv_data_error.
    ENDTRY.
  ENDMETHOD.                    "SET_COLUMNS"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

