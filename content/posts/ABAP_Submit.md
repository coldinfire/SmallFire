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

```JS
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
SUBMIT ztest_submit1
  WITH s1_matnr IN s2_matnr
  WITH s1_lgort EQ 'WA01' SIGN 'I'
  .......
  AND RETURN.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
```

### 使用 SELECTION-TABLE 调用

```JS
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

```JS
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

### 传递ALV内表数据到被调用的程序

#### 使用 SAP MEMORY 或者 ABAP MEMORY

- 在调用的程序中：EXPORT it_tab TO MEMORY 'Z_SUBMIT_MEMORY'.

- 在被调用的程序中：IMPORT T_ITAB FROM MEMORY 'Z_SUBMIT_MEMORY''

#### 使用 cl_salv_bs_runtime_info 获取 report 结果并输入到内表

这个是 SAP 提供的 API 所以我们不关心如何存储所以该方法不需要修改目标程序就可以直接得到 ALV 显示的结果， 但不能设置目标程序的中断点，需显示 ALV 的函数执行完毕方可获取到数据。

CL_SALV_BS_RUNTIME_INFO 与读取 ALV 有关的方法：

1. SET( ) - 此方法初始化类（清除内存区域），然后允许标志的设置让任何后续 ALV 对象如何工作。它应该在装程序调用 ALV 报告程序之前被调用。
   参数：
   DISPLAY - 将它设为 abap_false 强制所有后续 ALV 报告不会被输出到 GUI。
   METADATA - 将它设为 abap_false 防止基本信息（布局，字段目录等）被取到内存中，一般我们不需要。
   DATA - 将它设为 abap_true 迫使数据表导出到内存而不是显示报表。
2. GET_DATA_REF( ) - 非常灵活的 GET_DATA * 方法，这种方法可以用来访问该数据表变量的引用（动态而且易用），所以即使不知道 ALV 数据表的结构也没关系。
   参数：
   R_DATA - 输出 ALV 数据表。
   R_DATA_LINE - 如果执行的 ALV 有 HEADER 的（可选）。
3. GET_DATA( ) - 如果知道需要调用的 ALV 数据表的结构，可以使用这个方法。
   参数：
   T_DATA - 输出参数数据表。
   T_DATA_LINE - 如果执行的 ALV 有 HEADER（可选）。
4. CLEAR_ALL( ) - 此方法清除在 set 方法设置的标志。如果之后本程序还需要显示其他 ALV 那么这个方法尤为重要。如果不清除设置，你的 ALV 就不会被显示出来。

#### 代码示例

```JS
FIELD-SYMBOLS: <lt_data> TYPE STANDARD TABLE.
FIELD-SYMBOLS: <fs>.
DATA: lr_data TYPE REF TO data.
DATA: lr_data_descr  TYPE REF TO cl_abap_datadescr.
" 初始设置 " 
cl_salv_bs_runtime_info=>set( EXPORTING  display   = abap_false
                                         metadata  = abap_false
                                         data      = abap_true ).
" 调用目标程序 "
SUBMIT z_report
  EXPORTING LIST TO MEMORY AND RETURN
   WITH p_user   EQ i_usnam
   WITH s_pspid  IN it_project_number
   WITH s_posid  IN it_wbs_element
   WITH s_order  IN it_order
   WITH s_level  IN it_level
   WITH s_prodat IN it_project_creat_date
   WITH s_erdat  IN it_wbs_creat_date.
TRY.
   " 获取ALV显示数据 "
   cl_salv_bs_runtime_info=>get_data_ref(
       IMPORTING 
         r_data_descr = lr_data_descr
    ).
   CHECK lr_data_descr IS NOT INITIAL.
   CREATE DATA lr_data TYPE HANDLE lr_data_descr.
   ASSIGN lr_data->* TO <lt_data>.
   cl_salv_bs_runtime_info=>get_data(
      IMPORTING
        t_data       =      <lt_data>
        ).
   IF <lt_data> IS ASSIGNED.
     LOOP AT <lt_data> ASSIGNING <fs>.
       MOVE-CORRESPONDING <fs> TO result_data.
       APPEND result_data.
       CLEAR: result_data.
     ENDLOOP.
   ENDIF.
 CATCH cx_salv_bs_sc_runtime_info.
   MESSAGE `Error when call function get data` TYPE 'E'.
ENDTRY.
" 清除初始设置 "
cl_salv_bs_runtime_info=>clear_all( ).
```



