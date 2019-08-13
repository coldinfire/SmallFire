---
title: "字符串前拼接空格、物料单位转换"
date: 2018-08-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils

---

### 字符串前拼接空格

在Grid ALV上也显示敲到空格：
```JS
IF <fw_output>-name2+0(1) eq space AND <fw_output>-name2 IS NOT INITIAL.
  h_white = cl_abap_conv_in_ce=>uccpi( 160 ).
  REPLACE ALL OCCURRENCES OF REGEX '\s' IN <fw_output>-name2 WITH h_white.
ENDIF.
```
### MARM物料单位转换

```JS
call function 'MD_CONVERT_MATERIAL_UNIT'
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
     others                     = 3
               .
if sy-subrc <> 0.
   MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
     WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
 endif.
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

