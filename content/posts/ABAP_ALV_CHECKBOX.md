---
title: " ALV 添加复选框，并添加全选，不全选 "
date: 2018-06-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

#### 定义结构中定义该字段

```ABAP
TYPE: BEGIN OF str_print,
        checkbox TYPE flag,
        ...
  END OF str_print.
DATA: gt_print TYPE TABLE OF gt_print WITH HEADER LINE.
```

#### FIELDCAT 添加 CheckBox

```ABAP
"$. Region ALV_Data"
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

#### 自定义按钮

```ABAP
FORM frm_alv_user_command USING r_ucomm LIKE sy-ucomm
                                rs_selfield TYPE slis_selfield.
  CASE r_ucomm.
     WHEN '&SALL' OR '&UALL'.
      PERFORM frm_select USING r_ucomm.
  ENDCASE.
ENDFORM.

FORM frm_select USING cmd TYPE sy-ucomm.
  DATA: flag TYPE c.
  CASE cmd.
    WHEN '&SALL'.
      flag = 'X'.
    WHEN '&UALL'.
      flag = ''.
    WHEN OTHERS.
      RETURN.
  ENDCASE.
  LOOP AT gt_print.
    gt_print-checkbox = flag.
    MODIFY gt_print INDEX sy-tabix.
  ENDLOOP.
ENDFORM.                    "f_select"
```

