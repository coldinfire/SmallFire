---
title: "物料单位转换"
date: 2018-08-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### MARM物料单位转换

```JS
CALL function 'MD_CONVERT_MATERIAL_UNIT'
   exporting
     i_matnr                    = matnr
     i_in_me                    = in_me
     i_out_me                   = out_me
     i_menge                    = in_menge
   importing
     e_menge                    = out_menge
   exceptions
     error_in_application       = 1
     error                      = 2
     others                     = 3.
IF sy-subrc <> 0.
   MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

### 物料单位比例获取

```ABAP
DATA: h_denominator(100) TYPE n,
      h_numerator(100)   TYPE n.
CALL FUNCTION 'CONVERSION_FACTOR_GET'
  EXPORTING
    no_type_check              = 'X'
    unit_in                    = ' ' "输入单位"
    unit_out                   = ' ' "输出单位"
  IMPORTING
    denominator                = h_denominator "分母"
    numerator                  = h_numerator   "分子"
IF sy-subrc <> 0.
   MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
"单位比例 = 分子/分母"
```

### 取字段简短描述

```JS
DATA inttab LIKE STANDARD TABLE OF dfies WITH HEADER LINE.
CALL FUNCTION 'DDIF_FIELDINFO_GET'
  EXPORTING
    tabname        = 'MARA'
    langu          = sy-langu
  TABLES
    dfies_tab      = inttab
  EXCEPTIONS
    not_found      = 1
    internal_error = 2
    OTHERS         = 3.
```

