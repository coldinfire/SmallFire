---
title: " 获取Domain的 Value Range "
date: 2020-06-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils 

---

### Function：DD_DOMVALUES_GET

Domain 的 Value Range 值维护后，会保存到表 DD07V 中，函数 `DD_DOMVALUES_GET` 就是从该表中获取数据。

```ABAP
FORM FRM_GET_DOMAIN TABLES R_VALUE STRUCTURE DD07V
                     USING PV_NAME.
  DATA: LV_RETURN TYPE SY-SUBRC.
  CALL FUNCTION 'DD_DOMVALUES_GET'
    EXPORTING
      DOMNAME        = PV_NAME
      TEXT           = 'X'
      LANGU          = SY-LANGU
    IMPORTING
      RC             = LV_RETURN
    TABLES
      DD07V_TAB      = R_VALUE
    EXCEPTIONS
      WRONG_TEXTFLAG = 1
      OTHERS         = 2.
  IF SY-SUBRC <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO 
      WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
ENDFORM.
```