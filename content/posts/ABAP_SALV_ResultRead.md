---
title: " ALV 结果读取 "
date: 2019-06-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 传递 ALV 内表数据到被调用的程序

#### 使用 SAP MEMORY 或者 ABAP MEMORY

- 在调用的程序中：EXPORT it_tab TO MEMORY 'Z_SUBMIT_MEMORY'.

- 在被调用的程序中：IMPORT T_ITAB FROM MEMORY 'Z_SUBMIT_MEMORY'.

#### 使用类 CL_SALV_BS_RUNTIME_INFO

这个是 SAP 提供的 API 所以我们不关心如何存储所以该方法不需要修改目标程序就可以直接得到 ALV 显示的结果， 但不能设置目标程序的中断点，需显示 ALV 的函数执行完毕方可获取到数据。

CL_SALV_BS_RUNTIME_INFO 与读取 ALV 有关的方法：

- SET( )：此方法初始化类（清除内存区域），然后允许标志的设置让任何后续 ALV 对象如何工作。它应该在装程序调用 ALV 报告程序之前被调用。
  - DISPLAY：将它设为 abap_false 强制所有后续 ALV 报告不会被输出到 GUI。
  - METADATA：将它设为 abap_false 防止基本信息（布局，字段目录等）被取到内存中，一般我们不需要。
  - DATA：将它设为 abap_true 迫使数据表导出到内存而不是显示报表。

- GET_DATA_REF( )：非常灵活的 GET_DATA * 方法，这种方法可以用来访问该数据表变量的引用（动态而且易用），所以即使不知道 ALV 数据表的结构也没关系。
  - R_DATA：输出 ALV 数据表。
  - R_DATA_LINE：如果执行的 ALV 有 HEADER 的（可选）。

- GET_DATA( )：如果知道需要调用的 ALV 数据表的结构，可以使用这个方法。
  - T_DATA：输出参数数据表。
  - T_DATA_LINE：如果执行的 ALV 有 HEADER（可选）。

- CLEAR_ALL( )： 此方法清除在 set 方法设置的标志。如果之后本程序还需要显示其他 ALV 那么这个方法尤为重要。如果不清除设置，你的 ALV 就不会被显示出来。

#### 代码示例

- 将 Submit 的 salv 设置为不显示模式
- Submit SALV 程序
- 调用 cl_salv_bs_runtime_info=>get_data_ref() 取得结果
- 调用 cl_salv_bs_runtime_info=>clear_all() 清空设置

```ABAP
REPORT zalv_result_get.
FIELD-SYMBOLS: <lt_data> TYPE STANDARD TABLE.
FIELD-SYMBOLS: <fs>.
DATA: lr_data TYPE REF TO data.
DATA: lr_data_descr  TYPE REF TO cl_abap_datadescr.
"设定SALV运行模式"
cl_salv_bs_runtime_info=>set( 
  EXPORTING  
    display   = abap_false
    metadata  = abap_false
    data      = abap_true ).
" 调用目标程序 "
SUBMIT z_report
  EXPORTING LIST TO MEMORY AND RETURN
    WITH s_pspid IN it_project_number
    WITH s_erdat IN it_wbs_creat_date.
TRY.
   " 获取ALV显示数据 "
   cl_salv_bs_runtime_info=>get_data_ref(
       IMPORTING 
         r_data_descr = lr_data_descr ).
   CHECK lr_data_descr IS NOT INITIAL.
   CREATE DATA lr_data TYPE HANDLE lr_data_descr.
   ASSIGN lr_data->* TO <lt_data>.
   cl_salv_bs_runtime_info=>get_data(
      IMPORTING
        t_data = <lt_data> ).
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

