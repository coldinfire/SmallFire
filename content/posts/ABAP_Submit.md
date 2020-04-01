---
title: "ABAP Submit 实现程序间互相调用"
date: 2018-12-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

ABAP 代码中通过Submit实现程序的调用以及调用时数据参数的传递.

### 将要被调用的Report: ZTEST_SUBMIT1

```JS
REPORT ZTEST_SUBMIT1.
DATA: lv_matnr TYPE matnr.
DATA: lv_charg TYPE charg.

PARAM
SELECT-OPTIONS: s1_matnr FOR matnr,
                s1_lgort FOR lgort.
                
START-OF-SELECTION.
  DATA: lv_line TYPE i.
  lv_line = LINES( s1_matnr ).
  WRITE: / 'S1_MATNR',lv_line.
  lv_line = LINES( s1_lgort ).
  WRITE: / 'S1_CHARG',lv_line.
```

### 使用Submit的Report: ZTEST_SUBMIT2

```JS
REPORT ZTEST_SUBMIT2.
DATA: lv_matnr TYPE matnr.

SELECT-OPTIONS: s2_matnr FOR matnr.
                
START-OF-SELECTION.
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
* 在该代码块实现用不同的方式调用Reprot1
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

SUBMIT使用语法：

```JS
SUBMIT {report|(name)} [selscreen_options]
    [list_options]
	[job_options]
	[AND RETURN].
```

#### 不使用参数直接调用

`SUBMIT ztest_submit1 AND RETURN.`

#### 直接使用参数调用

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
SUBMIT ztest_submit1
  WITH s1_matnr IN s2_matnr
  WITH s1_lgort EQ 'WA01' SIGN 'I'
  .......
  AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

#### 使用SELECTION-TABLE调用

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
DATA: lt_seltab  TYPE TABLE OF rsparams,
      ls_seltab  LIKE LINE OF lt_seltab.
 
  LOOP AT s2_matnr.
    ls_seltab-selname = 'S1_MATNR'.  " Report1中的屏幕字段名
    ls_seltab-KIND    = 'S'.
    ls_seltab-SIGN    = s2_matnr-SIGN.
    ls_seltab-OPTION  = s2_matnr-OPTION.
    ls_seltab-LOW     = s2_matnr-LOW.
    APPEND ls_seltab TO lt_seltab.
  ENDLOOP.
 
  ls_seltab-selname = 'S1_LGORT'.
  ls_seltab-KIND    = 'S'.
  ls_seltab-SIGN    = 'I'.
  ls_seltab-OPTION  = 'EQ'.
  ls_seltab-LOW     = 'WA01'.
  APPEND ls_seltab TO lt_seltab.
 
  SUBMIT ztest_submit1
    WITH SELECTION-TABLE lt_seltab
    AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

#### 使用Report Variant调用

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
 "‘TEST’ 是report:ztest_submit1中保存的变式名称 
 SUBMIT ztest_submit1
    USING SELECTION-SET 'TEST'
    AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

#### 传递ALV内表数据到被调用的程序

需要用 SAP MEMORY 或者 ABAP MEMORY

- 在调用的程序中：EXPORT it_tab TO MEMORY 'Z_SUBMIT_MEMORY'.
- 在被调用的程序中：IMPORT T_ITAB FROM MEMORY 'Z_SUBMIT_MEMORY''.