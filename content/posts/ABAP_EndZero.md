---
title: "小数后面去除后缀0"
date: 2018-06-13
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

## 小数后面去除后缀0 ##

```JS
form DATA_DELETE_ZERO  using p_field z_result.
   DATA:var1 TYPE p DECIMALS 3,
        var2 TYPE p DECIMALS 2,
        var3 TYPE p DECIMALS 1,
        var4 TYPE i.
        move p_field to var1.
        move p_field to var2.
        move p_field to var3.
        move p_field to var4.
        IF var2 = var1.
          IF var3 = var1.
            IF var4 = var1.
              z_result = var4.
            ELSE.
              z_result = var3.
            ENDIF.
          ELSE.
            z_result = var2.
          ENDIF.
        ELSE.
          z_result = var1.
        ENDIF.
endform.
```