---
title: " SO10 创建标准文本 "
date: 2019-10-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 创建标准文本

通过事物码 SO10 可以创建标准文本：

![SO10](/images/ABAP/SO10.png)

在标准文本中输入文本内容，文本内容可以加入其他的文本，可以实现文本嵌套。

![SO10 Text](/images/ABAP/SO10_1.png)

### Smartforms 中使用

![SO10 User1](/images/ABAP/SO10_2.png)

#### Step1：Create Text Node

在 Smartforms 的节点中创建 Text 文本节点

#### Step2：Change Text Type

修改节点类型为 Include Text 

#### Step3：Choose The Text

通过 Text Name , Text Object ，Text ID 选择需要添加的文本信息，保存 Smartforms 。

### 通过函数获取

```js
DATA name TYPE thead-tdname.
DATA langu LIKE sy-langu VALUE 'EN'.
DATA lines TYPE STANDARD TABLE OF tline WITH HEADER LINE.
DATA count TYPE i.
name = 'Z_TEST'.
" Read text from SO10 "
CALL FUNCTION 'READ_TEXT'
  EXPORTING
    client                  = sy-mandt
    id                      = 'ST'
    language                = langu
    name                    = name
    object                  = 'TEXT'
  TABLES
    lines                   = lines
  EXCEPTIONS
    id                      = 1
    language                = 2
    name                    = 3
    not_found               = 4
    object                  = 5
    reference_check         = 6
    wrong_access_to_archive = 7
    OTHERS                  = 8.
" Replace Symbol Value in text "
IF sy-subrc EQ 0 .
" Initialize the text symbols "
  CALL FUNCTION 'INIT_TEXTSYMBOL'.
" Set dynamic text symbol "
  CALL FUNCTION 'SET_TEXTSYMBOL'
    EXPORTING
      name    = '&l_aa&'
      value   = '输入需要替换的内容'
      replace = 'X'.
  DESCRIBE TABLE lt_line LINES lv_count.
" Replace all text symbol in your long text "
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

