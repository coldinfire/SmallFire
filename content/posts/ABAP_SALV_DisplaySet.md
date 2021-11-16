---
title: " SALV Display 设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Display 设置

可以通过类 CL_SALV_DISPLAY_SETTINGS 中的一些方法进行 SALV 显示的设置。

![CL_SALV_DISPLAY_SETTINGS](/images/ABAP/SALV3.png)

- SET_VERTICAL_LINES：设置是否显示垂直线
- SET_HORIZONTAL_LINES：设置是否显示水平线
- SET_STRIPED_PATTERN：斑马条纹（行颜色交替）
- SET_LIST_HEADER_SIZE：报表头
- SET_FIT_COLUMN_TO_TABLE_SIZE：列自适应表格宽度

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS: set_display_setting
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_display_setting
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_display_setting.
    DATA: lo_display TYPE REF TO cl_salv_display_settings.
    "Get display object"
    lo_display = co_alv->get_display_settings( ).
    "Set attributes"
    lo_display->set_striped_pattern( 'X' ).
    lo_display->set_fit_column_to_table_size( 'X' ).
    "Title to ALV"
    lo_display->set_list_header( 'ALV Test for Display Settings' ).
  ENDMETHOD.                    "SET_DISPLAY_SETTING"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```