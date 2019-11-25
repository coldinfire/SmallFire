---
title: "ALV添加复选框，并添加全选，不全选功能"
date: 2018-07-016
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---



#### 定义结构中定义该字段

```JS
DATA: BEGIN OF gt_print OCCURS 10,
        CHECKBOX TYPE flag,
        ......
DATA: END OF gt_print.
```

#### FIELDCAT添加并定义CheckBox

```JS
"$. Region ALV_Data
TYPE-POOLS:slis.
DATA: alv_fieldcat TYPE STANDARD TABLE OF slis_fieldcat_alv WITH HEADER LINE,
      alv_layout   TYPE slis_layout_alv.

	alv_fieldcat-fieldname = 'CHECKBOX'.
    alv_fieldcat-scrtext_m = 'Choose'.
    alv_fieldcat-checkbox  = 'X'.
    alv_fieldcat-edit      = 'X'.
    alv_fieldcat-just      = 'C'.
    APPEND alv_fieldcat.
```

