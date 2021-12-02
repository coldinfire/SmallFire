---
title: " SALV ALV 结果读取 "
date: 2019-06-09
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### ALV 结果读取

通过类 CL_SALV_BS_RUNTIME_INFO 来实现直接获取 submit 其他 SALV 程序后显示的结果。

通过这种方法也可以得到某些标准程序ALV的显示结果，很方便。

- 将 Submit 的 salv 设置为不显示模式
- Submit SALV 程序
- 调用 cl_salv_bs_runtime_info=>get_data_ref() 取得结果

```ABAP
REPORT zalv_result_get.
DATA: gt_outtab TYPE STANDARD TABLE OF str_alv.
FIELD-SYMBOLS: <fgt_outtab> LIKE gt_outtab.
DATA go_data TYPE REF TO data.
"设定SALV运行模式"
cl_salv_bs_runtime_info=>set(
  EXPORTING
    display  = abap_false  "不显示"
    metadata = abap_false
    data     = abap_true 
  ).
"Submit SALV程序"
SUBMIT salv_demo_table_simple AND RETURN.
TRY.
    "取得运行数据"
    cl_salv_bs_runtime_info=>get_data_ref(
          IMPORTING
            r_data = go_data
    ).
    "数据赋值"
    ASSIGN go_data->* TO <git_outtab>.
  CATCH cx_salv_bs_sc_runtime_info.
ENDTRY.
"清空运行模式设置"
CALL METHOD cl_salv_bs_runtime_info=>clear_all.
```

