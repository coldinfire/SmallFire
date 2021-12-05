---
title: " 报表开发常用工具 "
date: 2018-08-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

## 单元格中的数据被修改后，将ALV单元格中的数据立即刷新到ABAP对应的内表中

方法一：通过对 REUSE_ALV_GRID_DISPLAY 函数参数 `i_grid_settings-edt_cll_cb` 进行设置

```ABAP
DATA:gs_grid_settings TYPE lv_s_glay. 
gs_grid_settings-edt_cll_cb  = 'X'.
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
  EXPORTING 
    i_grid_settings = gs_grid_settings
```

方法二：通过函数参数 I_CALLBACK_USER_COMMAND 指定的回调 Form 的参数 slis_selfield 进行设置

```ABAP
FORM user_command USING r_ucomm LIKE sy-ucomm
                    rs_selfield TYPE slis_selfield.
  rs_selfield-refresh = 'X'.
  CASE r_ucomm.
    WHEN 'UPDATE'.
    PERFORM frm_update.
  ENDCASE.
ENDFORM. 
```

## 按钮使用后刷新ALV

```ABAP
FORM frm_user_command USING r_ucomm  LIKE sy-ucomm
                         rs_selfield TYPE slis_selfield.
 CASE r_ucomm.
  WHEN 'PROFIT'.
    perform frm_cycle_count_profit.
  WHEN others.
 ENDCASE.
 rs_selfield-refresh = 'X'.
ENDFORM.
```

## 稳定刷新

### OO ALV

如果使用 "REFRESH_TABLE_DISPLAY" 刷新 ALV 后，记录会跳到第一行，以下代码可以使记录仍然定位在当前行 

```ABAP
DATA gs_stable TYPE lvc_s_stbl.
  gs_stable-row = 'X'.
  gs_stable-col = 'X'.
CALL METHOD go_grid->refresh_table_display
  EXPORTING
    is_stable = gs_stable
  EXCEPTIONS
    finished = 1
    OTHERS = 2.
IF sy-subrc <> 0.
ENDIF.
```

### Function ALV

使用类似的方法，但是要先把当前 ALV 网格 “对象化”。

**注意：** FUNCTION ALV 中一般是在 user_command 中加上 `rs_selfield-refresh = 'X'.` 来刷新 ALV 的，所以当使用稳定刷新以后，不能再用该语句了。

```ABAP
DATA go_grid TYPE REF TO cl_gui_alv_grid.
DATA gs_stbl TYPE lvc_s_stbl.
"把当前网格赋给对象 go_grid"
CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
  IMPORTING
    e_grid = go_grid.
" 调用 CHECK_CHANGED_DATA 可以使被修改的数据自动更新到内表中去 "
CALL METHOD go_grid->check_changed_data.
" 稳定刷新 "
gs_stbl-row = 'X'." 基于行的稳定刷新
gs_stbl-col = 'X'." 基于列稳定刷新
CALL METHOD l_guid->refresh_table_display
  EXPORTING
    is_stable      = gs_stbl
    i_soft_refresh = 'X'
  EXCEPTIONS
    finished       = 1
    others         = 2.
```

## Search help 的创建

`PROCESS ON VALUE-REQUEST.`

` AT SELECTION-SCREEN ON VALUE-REQUEST FOR xxx.`

在屏幕的 ON VALUE—REQUEST 事件里可以通过下面方法来创建搜索帮助

### 标准方法

#### 日历对话框

```ABAP
PARAMETERS: p_date TYPE sy-datum.
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_date.
  CALL FUNCTION 'F4_DATE'
    EXPORTING
      date_for_first_month = sy-datum
    IMPORTING
      select_date = p_date  "用户选择后返回的日期"
    EXCEPTIONS
      calendar_buffer_not_loadable = 1
      date_after_range = 2
      date_before_range = 3
      date_invalid = 4
      factory_calendar_not_found = 5
      holiday_calendar_not_found = 6
      parameter_conflict = 7
      OTHERS = 8.
```

### 自定义方法

-   `F4IF_FIELD_VALUE_REQUEST` ： 函数的作用是在运行时，可以动态的为某个屏幕字段指定Search Help ，这个被引用的 Help 来自某个表（或结构）字段上绑定的 Help

-  `F4IF_INT_TABLE_VALUE_REQUEST` : 在程序运行时， 将某个内表动态的用作 Search help 的数据来源,即使用该函数可以将某个内表转换为 Search help ，可实现联动效果

- `TR_F4_HELP `：简单实现 Search Help ，数据来源于内表

```ABAP
DATA:BEGIN OF it_matf4 OCCURS 0 ,
      zucode LIKE zecs_mat-zimport_code,
      g_code LIKE zecs_mat-g_code,
      g_name LIKE zecs_mat-g_name,
      zstate LIKE zecs_hd-zstate,
      zby LIKE zecs_hd-zby,
     END OF it_matf4. 
DATA: it_return LIKE ddshretval OCCURS 0 WITH HEADER LINE.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR S_TIMSTP-LOW.
  SELECT *
    INTO CORRESPONDING FIELDS OF TABLE IT_MATF4
    FROM <table>  
   WHERE <var> = <option>.
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'   "F4输出函数"
    EXPORTING
*   DDIC_STRUCTURE    = ' '
     RETFIELD         = 'ZUCODE'   "field of internal table"
     VALUE_ORG        = 'S'
     CALLBACK_PROGRAM = SY-REPID
     CALLBACK_FORM    = 'FRM_F4CALLBACK'
    TABLES
      VALUE_TAB       = IT_MATF4
*   FIELD_TAB         =
      RETURN_TAB      = IT_RETURN.
  WRITE IT_RETURN-FIELDVAL TO S_TIMSTP-LOW.
  REFRESH IT_MATF4.

FORM frm_f4callback TABLES record_tab STRUCTURE seahlpres
                    CHANGING shlp TYPE shlp_descr
                      callcontrol LIKE ddshf4ctrl.
  DATA: wa_selopt TYPE ddshselopt.
  DATA: lt_value TYPE TABLE OF ddshfprop,
        ls_value TYPE ddshfprop.
  lt_value[] = shlp-fieldprop[].
  READ TABLE lt_value INTO ls_value WITH KEY fieldname = 'F0004'.
  IF sy-subrc = 0.
    ls_value-defaultval = '''4'''.
    MODIFY lt_value FROM ls_value INDEX sy-tabix.
  ENDIF.
  shlp-fieldprop[] = lt_value[].
ENDFORM.                    "FRM_F4CALLBACK"
```

## 双击字段功能

```ABAP
FORM frm_user_command USING r_ucomm LIKE sy-ucomm
                        rs_selfield TYPE slis_selfield. 
  DATA lv_index TYPE i.
  IF r_ucomm = '&IC1'.
    lv_index = rs_selfield-tabindex.
    READ it_table INDEX lv_index.
    "程序调用传参"
    SUBMIT z_pro AND RETURN WITH p_param1 = it_table-param1
                            WITH p_param2 = it_table-param2.
    "如果是TCode - CALL Transation"
    "传入输入参数值并调用其他TCode"
    SET PARAMETER ID 'ANR' FIELD it_table-aufnr.
    CALL TRANSACTION 'CO03' AND SKIP FIRST SCREEN. "跳过第一屏屏幕"
   ENDIF.
ENDFORM.
```

