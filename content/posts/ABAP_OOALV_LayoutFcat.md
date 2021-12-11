---
title: " OO ALV 修改Layou和FieldCatalog "
date: 2018-07-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - OOALV

---


### 显示后，修改字段目录和布局

在运行期间，可以在首次显示列表后设置新的布局或新字段目录。 这些组件具有 get/set 方法来完成此操作。

- Field Catalog
  - GET_FRONTEND_FIELDCATALOG
  - SET_FRONTEND_FIELDCATALOG
- Layout
  - GET_FRONTEND_LAYOUT
  - SET_FRONTEND_LAYOUT

#### 代码示例

```ABAP
DATA ls_fcat TYPE lvc_s_fcat .
DATA lt_fcat TYPE lvc_t_fcat .
DATA ls_layout TYPE lvc_s_layo .
"Field Cattalog"
CALL METHOD go_grid->get_frontend_fieldcatalog
  IMPORTING
    et_fieldcatalog = lt_fcat[] .
LOOP AT lt_fcat INTO ls_fcat .
  IF ls_fcat-fieldname = 'Field_name' .
    ls_fcat-no_out = space .
    MODIFY lt_fcat FROM ls_fcat .
  ENDIF .
  CLEAR ls_fcat .
ENDLOOP .
CALL METHOD go_grid->set_frontend_fieldcatalog
  EXPORTING
    it_fieldcatalog = lt_fcat[] .
"Layout"
CALL METHOD obj_alv->get_frontend_layout
  IMPORTING
    es_layout = ls_layout .
    ls_layout-grid_title = 'Title name' .
CALL METHOD obj_alv->set_frontend_layout
  EXPORTING
    is_layout = ls_layout .
```

### 将字段作为下拉菜单

有时候我们可以把一些字段设置为下拉，比如一些类型，一些字段的值是比较固定的一些值；也可以通过搜索帮助来做。

使用一个内表存放了句柄和对应的值，该表类型为 `LVC_T_DROP`；下拉的内表传递需要使用方法 `SET_DROP_DOWN_TABLE`。

#### 设置整列为下拉

在字段目录中，把控制字段 `DRDN_HNDL` 指向对应的下拉内表的句柄就可以了。

- `gs_fieldcat-drdn_hndl = 1.`

#### 设置单元格下拉

需要在数据显示的内表中增加一个句柄字段(如果是有多个不同的字段需要设置下拉，可以增加多个字段)，同时需要在字段目录里设置 `DRDN_FIELD`。

- `gs_fieldcat-drdn_field = 'DROP_HNDL'.`

#### 示例代码

```ABAP
DATA: gt_fieldcat  TYPE lvc_t_fcat,   
      gs_fieldcat  TYPE lvc_s_fcat.
DATA BEGIN OF gt_list OCCURS 0 .
  INCLUDE STRUCTURE SFLIGHT .
  DATA drop_hndl TYPE int4 .
DATA END OF gt_list .
gs_fieldcat-drdn_hndl = 1.`
gs_fieldcat-drdn_field = 'DROP_HNDL'.
"定义下拉的句柄内表"
FORM prepare_drop_down_values.
  DATA lt_ddval TYPE lvc_t_drop .
  DATA ls_ddval TYPE lvc_s_drop .
  CLEAR lt_ddval.
    ls_ddval-handle = '1' .
    ls_ddval-value = 'JFK-12' .
    APPEND ls_ddval TO lt_ddval .
    ls_ddval-handle = '1' .
    ls_ddval-value = 'JSF-44' .
    APPEND ls_ddval TO lt_ddval .
    ls_ddval-handle = '1' .
    ls_ddval-value = 'KMDA-53' .
    APPEND ls_ddval TO lt_ddval .
    ls_ddval-handle = '1' .
    ls_ddval-value = 'SS3O-N' .
    APPEND ls_ddval TO lt_ddval .
  "准备好内表以后,使用方法 set_drop_down_table 来传递给ALV"
  CALL METHOD go_grid->set_drop_down_table
    EXPORTING
      it_drop_down = lt_ddval .
ENDFORM. " prepare_drilldown_values "
```

