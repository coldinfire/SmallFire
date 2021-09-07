---
title: "OO ALV 工具"
date: 2018-07-15
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

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
CALL METHOD obj_alv->get_frontend_fieldcatalog
  IMPORTING
    et_fieldcatalog = lt_fcat[] .
LOOP AT lt_fcat INTO ls_fcat .
  IF ls_fcat-fieldname = 'Field_name' .
    ls_fcat-no_out = space .
    MODIFY lt_fcat FROM ls_fcat .
  ENDIF .
  CLEAR ls_fcat .
ENDLOOP .
CALL METHOD obj_alv->set_frontend_fieldcatalog
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

使用内表存放句柄和对应的值，表类型："LVC_T_DROP"

内表通过方法"
SET_DROP_DOWN_TABLE"

