---
title: "SO10创建标准文本"
date: 2019-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap
  - utils
---

通过 Tcode SO10 可以创建标准文本：

​	![SO10](/images/ABAP/SO10.png)

​	

通过占位符替换长文本：

​	![SO10 Symbol](/images/ABAP/SO10_1.png)

```js
DATA lv_name TYPE thead-tdname.
DATA lv_langu LIKE sy-langu VALUE 'EN'.
DATA lt_line TYPE STANDARD TABLE OF tline WITH HEADER LINE.
DATA lv_count TYPE i.
lv_name = 'Z_TEST'.
"read text from SO10
  CALL FUNCTION 'READ_TEXT'
  EXPORTING
    client                  = sy-mandt
    id                      = 'ST'
    language                = lv_langu
    name                    = lv_name
    object                  = 'TEXT'
  TABLES
    lines                   = lt_line
  EXCEPTIONS
    id                      = 1
    language                = 2
    name                    = 3
    not_found               = 4
    object                  = 5
    reference_check         = 6
    wrong_access_to_archive = 7
    OTHERS                  = 8.
  IF sy-subrc EQ 0 .
"initialize the text symbols
  CALL FUNCTION 'INIT_TEXTSYMBOL'.
"set dynamic text symbol
  CALL FUNCTION 'SET_TEXTSYMBOL'
    EXPORTING
      name    = '&l_aa&'
      value   = '输入需要替换的内容'
      replace = 'X'.
  DESCRIBE TABLE lt_line LINES lv_count.
"replace all text symbol in your long text
  CALL FUNCTION 'REPLACE_TEXTSYMBOL'
    EXPORTING
      endline   = lv_count
      startline = 1
    TABLES
      lines     = lt_line.
ENDIF.
LOOP AT lt_line.
  WRITE:/ lt_line-tdline.
ENDLOOP.
```

