---
title: " SALV Functions 设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### Functions 设置

CL_SALV_TABLE 中提供了方法 `get_functions`，`set_default`；通过这两个方法可以创建 Status。

![cl_salv_functions_list](/images/ABAP/SALV8.png)

![cl_salv_functions_list](/images/ABAP/SALV9.png)

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:set_functions
      CHANGING co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD set_functions
      CHANGING co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_functions.
    DATA: lr_functions TYPE REF TO cl_salv_functions_list.
    lr_functions = co_alv->get_functions( ).
    "Setting a default status"
    lr_functions->set_default( abap_true ).
    "Activate All Generic ALV Functions"
    lr_functions->set_all( abap_true )." 
  ENDMETHOD.     "set_pf_status"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 设置自定义 Status：不适用于 CONTAINER 模式

有时默认的标准 GUI Status 并不能完全满足我们的需求，需要在状态栏中添加自定义的按钮，这时要创建一个自定义的状态栏添加到SALV上。

CL_SALV_TABLE 的方法 `SET_SCREEN_STATUS` 可以指定自定义的 Status。

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:add_pf_status
      CHANGING co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2_1 - Begin..............................2_1..*$*$*
    CALL METHOD add_pf_status
      CHANGING co_alv = go_alv.
*$*$*.....CODE_ADD_2_1 - End................................2_1..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD add_pf_status.
    "系统提示的标准Status为SAPLSALV_METADATA_STATUS"
    co_alv->set_screen_status(
      pfstatus      =  'STANDARD_STATUS'
      report        =  sy-repid
      "此参数只对SALV标准的预设保留按钮起作用，也就是说当T001 GUI Status是从系统中"
      "提供的标准Gui Status拷贝时才起作用，即通过此参数来屏蔽或显示某些预置按钮"
      "对自己完全新创建的GUI Status按钮（实质上是根据FunCode来判断的）不起作用"
      set_functions = co_alv->c_functions_all ). "显示所有通用的预设按钮"
    "set_functions = co_alv->c_functions_default)." "显示基本默认选择性的预设按钮"
    "set_functions = co_alv->c_functions_none)." "所有预设按钮都不显示"
  ENDMETHOD.                    "set_pf_status"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

### 在预设工具栏上附加按钮：只适用于 CONTAINER 模式

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    METHODS:add_pf_status
      CHANGING co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2_1 - Begin..............................2_1..*$*$*
    CALL METHOD add_pf_status
      CHANGING co_alv = go_alv.
*$*$*.....CODE_ADD_2_1 - End................................2_1..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD add_pf_status.
    "附加刷新按钮"
    DATA: lr_functions TYPE REF TO cl_salv_functions_list.
    lr_functions = go_alv->get_functions( ).
    lr_functions->set_all( abap_true ). "激活所有的ALV内置通用按钮"
    INCLUDE <icon>.
    DATA: lv_icon TYPE string.
    lv_icon = icon_refresh.
    "附加按钮，只适用于‘可控模式’下的 SALV"
    lr_functions->add_function(
      name = 'refresh'
      icon = lv_icon
      text = '刷新按钮'
      tooltip = '刷新数据'
      "按钮存放的位置：这里在右边附加。该参数的取值信息可查看方法源码"
      position = if_salv_c_function_position=>right_of_salv_functions ).
  ENDMETHOD.                    "set_pf_status"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

