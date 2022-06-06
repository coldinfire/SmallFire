---
title: " BAPI: BAPI_ROUTING_CREATE "
date: 2019-12-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### 示例代码

```ABAP
*&---------------------------------------------------------------------*
*& Report ZPPRTEST
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT ZPPRTEST.

DATA: l_plnnr TYPE plnnr.

DATA: lt_plko TYPE TABLE OF bapi1012_tsk_c,
      lt_mapl TYPE TABLE OF bapi1012_mtk_c,
      lt_plpo TYPE TABLE OF bapi1012_opr_c,
      lt_subplpo TYPE TABLE OF bapi1012_sub_opr_c,
      lt_bom TYPE TABLE OF bapi1012_com_c,
      lt_msg TYPE TABLE OF bapiret2,
      ls_plko TYPE bapi1012_tsk_c,
      ls_mapl TYPE bapi1012_mtk_c,
      ls_plpo TYPE bapi1012_opr_c,
      ls_subplpo TYPE bapi1012_sub_opr_c,
      ls_bom TYPE bapi1012_com_c,
      ls_msg TYPE bapiret2.

***表头***
CLEAR ls_plko.
ls_plko-valid_from = sy-datum.      "生效日期"
ls_plko-valid_to_date = '99991231'. "有效期限"
ls_plko-plant = '2000'.             "工厂"
ls_plko-task_list_usage = '1'.      "用途"
ls_plko-task_list_status = '4'.     "状态"
ls_plko-task_measure_unit = 'PCS'.  "单位"
ls_plko-lot_size_from = 0.          "批量下限"
ls_plko-lot_size_to = 99999999.     "批量上限"
APPEND ls_plko TO lt_plko.
***物料指派***
CLEAR ls_mapl.
ls_mapl-material = 'CTCZ0185-S2V-3'.
ls_mapl-plant = '2000'.
ls_mapl-valid_from = sy-datum.
ls_mapl-valid_to_date = '99991231'.
APPEND ls_mapl TO lt_mapl.
***作业***
CLEAR ls_plpo.
ls_plpo-activity = '0010'.
ls_plpo-denominator = 1.      "用于转换工艺路线和工序单位的分母"
ls_plpo-nominator = 1.        "用于转换任务清单和工序计量单位的计数器"
ls_plpo-work_cntr = '23001'.  "工作中心"
ls_plpo-control_key = 'ZP10'. "控制码"
ls_plpo-standard_text_key = '1050'.  "标准内文码"
ls_plpo-base_quantity = 1.    "基础数量"
ls_plpo-operation_measure_unit = 'PCS'.  "单位"
ls_plpo-std_value_01 = 60.
ls_plpo-std_value_02 = '77.140'.
ls_plpo-std_value_03 = 0.
ls_plpo-std_value_04 = 10.   "制程时间"
ls_plpo-std_unit_04 = '10'.  "天数单位"
ls_plpo-scrap_factor = 0.
ls_plpo-valid_from = sy-datum.
ls_plpo-valid_to_date = '99991231'.
ls_plpo-cost_relevant = 'X'.
APPEND ls_plpo TO lt_plpo.
CLEAR ls_plpo.
ls_plpo-activity = '0020'.
ls_plpo-denominator = 1. "用于转换工艺路线和工序单位的分母"
ls_plpo-nominator = 1.   "用于转换任务清单和工序计量单位的计数器"
ls_plpo-work_cntr = 'DP2031'.  "工作中心"
ls_plpo-control_key = 'ZP02'.  "控制码"
ls_plpo-standard_text_key = 'M231'.  "标准内文码"
ls_plpo-base_quantity = 600.  "基础数量"
ls_plpo-operation_measure_unit = 'PCS'.  "单位"
ls_plpo-std_value_01 = 1.
ls_plpo-std_value_02 = 0.
ls_plpo-std_value_03 = 1.
ls_plpo-std_value_04 = 0.
ls_plpo-scrap_factor = '0.5'.
ls_plpo-valid_from = sy-datum.
ls_plpo-valid_to_date = '99991231'.
ls_plpo-cost_relevant = 'X'.
APPEND ls_plpo TO lt_plpo.
***子作业***
CLEAR ls_subplpo.
ls_subplpo-sub_activity = '0011'.
ls_subplpo-activity = '0010'.
ls_subplpo-denominator = 1. "用于转换工艺路线和工序单位的分母"
ls_subplpo-nominator = 1.   "用于转换任务清单和工序计量单位的计数器"
ls_subplpo-work_cntr = '31001'.   "工作中心"
ls_subplpo-control_key = 'ZP10'.  "控制码"
ls_subplpo-standard_text_key = '1061'.  "标准内文码"
ls_subplpo-base_quantity = 20.  "基础数量"
ls_subplpo-operation_measure_unit = 'PCS'.  "单位"
ls_subplpo-std_value_01 = 35.
ls_subplpo-std_value_02 = 35.
ls_subplpo-std_value_03 = 0.
ls_subplpo-std_value_04 = 0.
ls_subplpo-scrap_factor = 0.
ls_subplpo-valid_from = sy-datum.
ls_subplpo-valid_to_date = '99991231'.
ls_subplpo-cost_relevant = 'X'.
APPEND ls_subplpo TO lt_subplpo.
***组件分配***
CLEAR ls_bom.
ls_bom-valid_from = sy-datum.
ls_bom-valid_to_date = '99991231'.
ls_bom-bom_type = ls_bom-bom_type_root = 'M'. "STKO-STLTY"
ls_bom-activity = '0010'. "工艺路线作业序号"
ls_bom-item_id = '00000001'.  "STPO-STLKN"
ls_bom-bom_no = ls_bom-bom_no_root = '00007871'. "STKO-STLNR"
ls_bom-alternative_bom = ls_bom-alternative_bom_root = '01'. "STKO-STLAL"
ls_bom-item_no = '0010'. "STPO-POSNR"
ls_bom-plant = '2000'.
APPEND ls_bom TO lt_bom.
CLEAR ls_bom.
ls_bom-valid_from = sy-datum.
ls_bom-valid_to_date = '99991231'.
ls_bom-bom_type = ls_bom-bom_type_root = 'M'.  "STKO-STLTY"
ls_bom-activity = '0010'.  "工艺路线作业序号"
ls_bom-item_id = '00000002'. "STPO-STLKN"
ls_bom-bom_no = ls_bom-bom_no_root = '00007871'.  "STKO-STLNR"
ls_bom-alternative_bom = ls_bom-alternative_bom_root = '01'. "STKO-STLAL"
ls_bom-item_no = '0020'.  "STPO-POSNR"
ls_bom-plant = '2000'.
APPEND ls_bom TO lt_bom.
CLEAR ls_bom.
ls_bom-valid_from = sy-datum.
ls_bom-valid_to_date = '99991231'.
ls_bom-bom_type = ls_bom-bom_type_root = 'M'.  "STKO-STLTY"
ls_bom-activity = '0020'.  "工艺路线作业序号"
ls_bom-item_id = '00000003'.  "STPO-STLKN"
ls_bom-bom_no = ls_bom-bom_no_root = '00007871'.  "STKO-STLNR"
ls_bom-alternative_bom = ls_bom-alternative_bom_root = '01'. "STKO-STLAL"
ls_bom-item_no = '0030'.  "STPO-POSNR"
ls_bom-plant = '2000'.
ls_bom-order_lvl = '01'.   "虚拟件使用"
ls_bom-order_path = '01'.  "虚拟件使用"
ls_bom-path = '000001'.    "虚拟件使用"
APPEND ls_bom TO lt_bom.

CALL FUNCTION 'BAPI_ROUTING_CREATE'
* EXPORTING
*   testrun                 = ' '
*   profile                 =
*   bomusage                =
*   application             =
  IMPORTING
    group                   = l_plnnr
*   groupcounter            =
  TABLES
    task                    = lt_plko
    materialtaskallocation  = lt_mapl
*   sequence                =
    operation               = lt_plpo
    suboperation            = lt_subplpo
*   referenceoperation      =
*   workcenterreference     =
    componentallocation     = lt_bom
*   productionresource      =
*   inspcharacteristic      =
*   textallocation          =
*   text                    =
    return                  = lt_msg
*   task_segment            =
   .
READ TABLE lt_msg INTO ls_msg WITH KEY type = 'E'.
IF sy-subrc = 0.
  ROLLBACK WORK.
ELSE.
  COMMIT WORK.
ENDIF.
```

