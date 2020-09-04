---
title: "Long Texts"
date: 2019-09-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 介绍

长文本是用于在 SAP 系统中包含长文本的容器，通常将它们附加到业务对象上，用户可以输入自由注释。

长文本只能通过与其相连的业务对象的维护事务来维护，但可以通过 **Tcode:SO10** 维护的 “标准文本” 除外。
定制程序也可以使用标准功能模块来读取和写入它们。

长文本是通过以下 4 个字段的组合唯一标识的：

- ID：4 个字符
- NAME：70 个字符
- OBJECT：10 个字符
- LANGUAGE：1个字符

#### 创建文本

可以通过 **Tcode:SE75** 创建 ID 和对象代码。一个文本对象可以包含多个文本 ID。每个文本 ID 包含多个文本名称。

步骤 1：创建文本对象。输入`Tcode：SE75`单击更改按钮，创建文本对象 `ztest_obj`。

![SE75](/images/ABAP/ABAP_LongTexts1.png)

输入文本对象的名称和描述。在这种情况下，选择保存模式作为Update，因为需要针对每个开发对象表条目更新注释数据。在编辑器中，Editor application的类型为 Standard Text (TX)，线宽为 40，可以根据需要选择。
也可以从此窗口中选择样式和表格，这将决定文本的显示样式。

![SE75 Change](/images/ABAP/ABAP_LongTexts2.png)

![SE75 Change](/images/ABAP/ABAP_LongTexts3.png)


步骤 2：双击文本对象，创建文本ID。

![SE75 Change](/images/ABAP/ABAP_LongTexts4.png)

![SE75 Change](/images/ABAP/ABAP_LongTexts5.png)

步骤 3：一旦创建了文本 ID，前端工作就完成了。

### 标准文本

标准文本是 “对象” 字段等于 “ TEXT” 的文本。只能通过事务 SO10 访问它们。

![SO10](/images/ABAP/ABAP_LongTexts6.png)

### 功能调用

**READ_TEXT** ：从文本对象中检索长文本

```JS
DATA BEGIN OF TEXTHEADER.
        INCLUDE STRUCTURE THEAD.
DATA END OF TEXTHEADER.
DATA BEGIN OF TEXTLINES OCCURS 10.
        INCLUDE STRUCTURE TLINE.
DATA END OF TEXTLINES.
DATA:ID     TYPE THEAD-TDID,
     NAME   TYPE THEAD-TDNAME,
     OBJECT TYPE THEAD-TDOBJECT,
     LANGUAGE TYPE THEAD-TDSPRAS,
     TEXT   TYPE CHAR2000.
CLEAR TEXTHEADER.
OBJECT = 'ZTEST_OBJ'.
ID     = '01'.
LANGUAGE = SY-LANGU.
CONCATENATE OBJECT ID INTO NAME.
CALL FUNCTION 'READ_TEXT'
  EXPORTING
    client                  = sy-mandt
    id                      = ID
    language                = LANGUAGE
    name                    = NAME
    object                  = OBJECT
  IMPORTING
    HEADER                  = TEXTHEADER
  TABLES
    lines                   = TEXTLINES
  EXCEPTIONS
    id                      =  1
    language                =  2
    name                    =  3
    not_found               =  4
    object                  =  5
    reference_check         =  6
    wrong_access_to_archive =  7
    OTHERS                  =  8.
LOOP AT TEXTLINES.
   CONCATENATE TEXT TEXTLINES into TEXT SEPARATED BY space.
ENDLOOP.
```

**SAVE_TEXT** ：将长文本保存到文本对象中

```JS
FORM save_comments.
  DATA: e_header  TYPE thead,
        i_header  TYPE  STANDARD  TABLE  OF thead,
        w_tline   TYPE tline,
        i_tline   TYPE  STANDARD  TABLE  OF tline  WITH  HEADER  LINE.
 
  e_header-tdobject =  'ZCHD_OBJ'.
  e_header-tdid =  'Y0B1'.
  e_header-tdspras = sy-langu.
  e_header-tdlinesize =  72.
 
  CONCATENATE y0bs_Dev-objid e_header-tdid INTO e_header-tdname.
  APPEND 'DevComments for Obj 1" to  i_tline-tdline.
 
  CALL  FUNCTION  'SAVE_TEXT'
    EXPORTING
      client          = sy-mandt
      header          = e_header
      savemode_direct =  'X'
    TABLES
      lines           = i_tline
    EXCEPTIONS
      id              =  1
      language        =  2
      name            =  3
      object          =  4
      OTHERS          =  5.
   IF sy-subrc <>  0.
   ENDIF.
 ENDFORM.
```



