---
title: " VA02 字段增强控制 "
date: 2021-12-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - enhance

---

### MV45AFZZ

SAP 预留的控制销售订单界面字段状态的增强。

```ABAP
FORM userexit_field_modification.
  IF sy-tcode EQ 'VA02' AND vbak-auart = 'ZXXX'.
    CASE screen-name.
      WHEN 'VBKD-BSTKD'.
        screen-input = 0.
      WHEN 'VBKD-BSTKD_E'.
        screen-input = 0.
    ENDCASE.
  ENDIF.
ENDFORM.
```

