---
title: " ABAP 截取中文字符串 "
date: 2018-06-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 截取包含中文字符串 ###

`strlen()` 只能计算包含英文字符串的长度，不能计算中文字符串的长度。

可以通过`cl_abap_list_utilities=>dynamic_output_length`获取精确长度。

```JS
FUNCTION zotfm001.
*"----------------------------------------------------------------------
*"*"本地接口：
*"  IMPORTING
*"     VALUE(I_STRING) TYPE  STRING
*"     VALUE(I_STRLEN) TYPE  I
*"  EXPORTING
*"     VALUE(E_STRING1) TYPE  STRING
*"     VALUE(E_STRING2) TYPE  STRING
*"----------------------------------------------------------------------
DATA:lv_char TYPE string,
   lv_len  TYPE i,
   lv_st1  TYPE i,
   lv_st2  TYPE i,
   lv_str  TYPE i.
CHECK i_string IS NOT INITIAL AND i_strlen IS NOT INITIAL.
lv_str = strlen( i_string ).
DO.
  IF lv_str >= sy-index.
   lv_char = i_string+0(sy-index).
   CALL METHOD cl_abap_list_utilities=>dynamic_output_length
     EXPORTING
       field = lv_char
     RECEIVING
       len   = lv_len.
    IF lv_len >= i_strlen.
      e_string1 = lv_char.
      lv_st1 = strlen( lv_char ).
      lv_st2 = lv_str - lv_st1.
      e_string2 = i_string+lv_st1(lv_st2).
      EXIT.
    ENDIF.
  ELSE.
    e_string1 = i_string.
    e_string2 = ''.
    EXIT.
  ENDIF.
ENDDO.
ENDFUNCTION.
```

