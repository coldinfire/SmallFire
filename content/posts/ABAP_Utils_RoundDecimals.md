---
title: " ABAP 数值四舍五入函数 "
date: 2021-06-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### ABAP 保留指定小数位

#### ROUND函数

`INT_SORT-MENGE = ROUND( VAL = LTB-EMENG * P_QPA DEC = 3 MODE = 1 ).`

mode代表着小数省略的规则，

- 1： 默认值，这个值总是从 0 四舍五入到更大的绝对值
- 5： 这个值总是从 0 四舍五入到更小的绝对值

#### Function：HR_NZ_ROUNDING_DECIMALS

```ABAP
DATA : dat  TYPE p DECIMALS  VALUE '12.5445' ,
       dat_result TYPE p DECIMALS  .
CALL FUNCTION 'HR_NZ_ROUNDING_DECIMALS'
  EXPORTING
    value_in                 = dat
    conv_dec                 = 2      " 设置保留几位小数
  IMPORTING
    value_out                = dat_result
  EXCEPTIONS
    no_rounding_required     = 1
    decimals_greater_than_10 = 2
    rounding_error           = 3
    OTHERS                   = 4.
```

#### Function：Round

```ABAP
DATA : dat  TYPE p DECIMALS  VALUE '12.540' ,
       dat_result TYPE p DECIMALS  .
CALL FUNCTION 'ROUND'
  EXPORTING
    decimals      = 2
    input         = dat
    sign          = '-' "+ 向上取舍,- 向下取舍(负数也一样)"
  IMPORTING
    output        = dat_result
  EXCEPTIONS
    input_invalid = 1
    overflow      = 2
    type_invalid  = 3
    OTHERS        = 4.
```

