---
title: " ABAP Submit 实现程序间互相调用 "
date: 2018-12-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

ABAP 代码中通过 Submit 实现程序的调用以及调用时数据参数的传递。

### 程序准备

#### 将要被调用的 Report: ZTEST_SUBMIT1

```ABAP
REPORT ZTEST_SUBMIT1.
DATA: lv_matnr TYPE matnr.
DATA: lv_charg TYPE charg.
SELECT-OPTIONS: s1_matnr FOR matnr,
                s1_lgort FOR lgort.
START-OF-SELECTION.
  DATA: lv_line TYPE i.
  lv_line = LINES( s1_matnr ).
  WRITE: / 'S1_MATNR',lv_line.
  lv_line = LINES( s1_lgort ).
  WRITE: / 'S1_CHARG',lv_line.
```

#### 使用 Submit 的 Report: ZTEST_SUBMIT2

```ABAP
REPORT ZTEST_SUBMIT2.
DATA: lv_matnr TYPE matnr.
SELECT-OPTIONS: s2_matnr FOR matnr.                
START-OF-SELECTION.
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
* 在该代码块实现用不同的方式调用Reprot1
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

SUBMIT 使用语法：

```JS
SUBMIT {report|(name)} [selscreen_options]
    [list_options]
	[job_options]
	[AND RETURN].
```

### 不使用参数直接调用

`SUBMIT ztest_submit1 AND RETURN.`

### 直接使用参数调用

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
SUBMIT ztest_submit1
  WITH s1_matnr IN s2_matnr
  WITH s1_lgort EQ 'WA01' SIGN 'I'
  .......
  AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

### 使用 SELECTION-TABLE 调用

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
DATA: lt_seltab  TYPE TABLE OF rsparams,
      ls_seltab  LIKE LINE OF lt_seltab.
LOOP AT s2_matnr.
  ls_seltab-selname = 'S1_MATNR'.  " Report1中的屏幕字段名 "
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

### 使用 Report Variant 调用

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
 " TEST 是report:ztest_submit1中保存的变式名称 "
 SUBMIT ztest_submit1
    USING SELECTION-SET 'TEST'
    AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

### 调用程序，显示选择屏幕界面

被调报表程序的选择屏幕会显示。如果此选择打开，并且还使用了其他参数选项来传输值时，这些值也会显示在屏幕中相应的
输入框中，并且用户可以进一步修改这些值。

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
SUBMIT ztest_submit1 VIA SELECTION-SCREEN AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```



