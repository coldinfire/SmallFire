---
title: " ABAP将数字金额转为大写 "
date: 2021-06-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

SAP 实际业务中可能会有将金额、重量等数字格式转换为中文大写或则英文大写的银行金额内容。可以使用标准的函数 `SPELL_AMOUNT` 来进行处理，但是要注意该函数可能出现的问题。

### 金额转换为大写

**CURRENCY**：该参数在金额转换时必须输入对应的货币类型 ，否则函数会将 AMOUNT 字段当作非金额数据处理。

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_1.png)

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_2.png)

转换金额字段时，AMOUNT 参数必须要有两位小数。如果不是两位小数的话，函数会自动处理转换成两位小数的类型。这将导致函数返回结果值发生变化。

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_3.png)

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_4.png)

### 数字转换为大写

**CURRENCY**：该参数值在数字转换时为空。

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_5.png)

转换数字时，AMOUNT 参数如果为小数时，函数会自动将该参数的小数点右移，转换成整数来处理。这将导致函数返回结果值变大。

![SPELL_AMOUNT](/images/ABAP/ABAP_AmountToString_6.png)

### 解决小数问题方法

将金额字段进行拆分处理。

```ABAP
DATA: lv_amount TYPE string VALUE '1235.346',
      lv_result TYPE string.
DATA: lv_integer TYPE string,
      lv_decimal TYPE string,
      lv_len     TYPE i,
      lv_words   TYPE spell.
"Split String Field By ."
CLEAR: lv_integer, lv_deciaml.
SPLIT lv_amount AT '.' INTO lv_integer lv_deciaml.
CONDENSE lv_integer NO-GAPS.
CONDENSE lv_decimal NO-GAPS.
"For Integer"
CLEAR lv_words.
CALL FUNCTION 'SPELL_AMOUNT'
  EXPORTING
    AMOUNT          = lv_integer
*   CURRENCY        = 'RMB'
*   FILLER          = ' '
    LANGUAGE        = sy-langu
  IMPORTING
    IN_WORDS        = lv_words
  EXCEPTIONS
    NOT_FOUND       = 1
    TOO_LARGE       = 2
    OTHERS          = 3.
CLEAR lv_integer.
CONCATENATE 'RMB ' lv_words-word INTO lv_integer.
"For Decimal"
CLEAR: lv_words,lv_len.
SHIFT lv_decimal RIGHT DELETING TRAILING space.
SHIFT lv_decimal RIGHT DELETING TRAILING '0'.
CONDENSE lv_decimal NO-GAPS.
lv_len = strlen( lv_decimal ).
CALL FUNCTION 'SPELL_AMOUNT'
  EXPORTING
    AMOUNT          = lv_deciaml
*   CURRENCY        = ' '
*   FILLER          =
    LANGUAGE        = 'E'
  IMPORTING
    IN_WORDS        = lv_words
  EXCEPTIONS
    NOT_FOUND       = 1
    TOO_LARGE       = 2
    OTHERS          = 3.
CLEAR lv_deciaml.
IF lv_words-word NE 'ZERO'.
  CASE lv_len.
    WHEN 3.
      CONCATENATE 'POINT' g_titl-dig03 g_titl-dig02 g_titl-dig01 'ONLY.'
        INTO lv_deciaml SEPARATED BY ' '.
    WHEN 2.
      CONCATENATE 'POINT' g_titl-dig02 g_titl-dig01 'ONLY.'
        INTO lv_deciaml SEPARATED BY ' '.
    WHEN 1.
      CONCATENATE 'POINT' g_titl-dig01 'ONLY.'
        INTO lv_deciaml SEPARATED BY ' '.
    WHEN OTHERS.
  ENDCASE.
ENDIF.
CONCATENATE lv_integer lv_deciaml INTO lv_result.
```

