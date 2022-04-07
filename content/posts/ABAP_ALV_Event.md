---
title: " Grid ALV 事件处理 "
date: 2018-06-25
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### Grid ALV 事件

`DATA: gt_event TYPE slis_t_event.`

| Event                            | Default formname  |
| :------------------------------- | :---------------- |
| slis_ev_item_data_expand         | ITEM_DATA_EXPAND  |
| slis_ev_reprep_sel_modify        | REPREP_SEL_MODIFY |
| **slis_ev_caller_exit_at_start** | CALLER_EXIT       |
| **slis_ev_user_command**         | USER_COMMAND      |
| slis_ev_top_of_page              | TOP_OF_PAGE       |
| **slis_ev_data_changed**         | DATA_CHANGED      |
| slis_ev_top_of_coverpage         | TOP_OF_COVERPAGE  |
| slis_ev_end_of_coverpage         | END_OF_COVERPAGE  |
| **slis_ev_pf_status_set**        | PF_STATUS_SET     |
| slis_ev_context_menu             | CONTEXT_MENU      |

### ALV 列字段的自动更新事件 DATA_CHANGED

ALV 可修改字段输入数据后，自动更新对应的结果列。

#### 事件定义

```ABAP
DATA: ls_event TYPE LINE OF slis_t_event.
ls_event-name = slis_ev_data_changed.
ls_event-form = 'FRM_DATA_CHANGED'.
APPEND ls_event TO gt_event.
```

#### 事件使用

```ABAP
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
  EXPORTING
    i_callback_program       = sy-repid
    i_callback_pf_status_set = 'FRM_SET_STATUS'
    i_callback_user_command  = 'FRM_USER_COMMAND'
    it_events                = gt_event[]
    is_layout_lvc            = gs_layo
    it_fieldcat_lvc          = gt_fcat
    i_save                   = 'A'
  TABLES
    t_outtab                 = gt_alv
  EXCEPTIONS
    program_error            = 1
    OTHERS                   = 2.
```

#### 事件功能 FORM

```ABAP
FORM frm_alv_data_change USING uo_data_changed
              TYPE REF TO cl_alv_changed_data_protocol.
  FIELD-SYMBOLS: <fs_table> TYPE STANDARD TABLE,
                 <fs_line> TYPE str_alv.
  DATA: lt_mod TYPE lvc_t_modi,
        ls_mod TYPE lvc_s_modi.
  DATA: lv_row_id TYPE int4,
        lv_tabix TYPE int4,
        lv_fieldname TYPE lvc_fname.

  IF uo_data_changed->mp_mod_rows IS NOT INITIAL.
    " 将数据变化所在行数据赋值到指针
    ASSIGN uo_data_changed->mp_mod_rows->* TO <fs_table>.
    " 读取数据变化所在的具体单元格
    MOVE uo_data_changed->mt_mod_cells TO lt_mod.
    READ TABLE lt_mod INTO ls_mod INDEX 1.
    lv_row_id = ls_mod-row_id.
    lv_tabix = ls_mod-tabix.
    " 循环指针内的数据
    LOOP AT <fs_table> ASSIGNING <fs_line>.
      IF <fs_line>-lifnr_n IS NOT INITIAL.
        SELECT SINGLE name1 INTO <fs_line>-name1_n
          FROM lfa1
         WHERE lifnr = <fs_line>-lifnr_n.
        lv_fieldname = 'NAME1_N'.
        CALL METHOD uo_data_changed->modify_cell
          EXPORTING
            i_fieldname = lv_fieldname
            i_row_id    = lv_row_id
            i_tabix     = lv_tabix
            i_value     = <fs_line>-name1_n.
      ENDIF.
      IF <fs_line>-matnr_n IS NOT INITIAL.
        SELECT SINGLE maktx INTO <fs_line>-maktx_n
          FROM makt
         WHERE matnr = <fs_line>-matnr_n
           AND spras = sy-langu.
        lv_fieldname = 'MAKTX_N'.
        CALL METHOD uo_data_changed->modify_cell
          EXPORTING
            i_fieldname = lv_fieldname
            i_row_id    = lv_row_id
            i_tabix     = lv_tabix
            i_value     = <fs_line>-maktx_n.
      ENDIF.
    ENDLOOP.
  ENDIF.

ENDFORM.                    "frm_alv_data_change
```

### 自定义 F4 帮助



#### 事件功能 FORM

```ABAP
FORM caller_exit USING ls_data TYPE slis_data_caller_exit.
  DATA: l_ref_alv TYPE REF TO cl_gui_alv_grid.
  data: lt_wa_f4 type lvc_s_f4,
        lt_f4 type lvc_t_f4.
  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    IMPORTING
      e_grid = l_ref_alv.
  set handler lcl_event_receiver=>on_f4 for l_ref_alv.
  set handler lcl_event_receiver=>on_data_changed for l_ref_alv.
  lt_wa_f4-fieldname  = 'MTART'.
  lt_wa_f4-register   = 'X'.
  lt_wa_f4-getbefore  = 'X'.
  lt_wa_f4-chngeafter = 'X'.
  append lt_wa_f4 to lt_f4.
  call method l_ref_alv->register_f4_for_fields
    EXPORTING
      it_f4 = lt_f4.
ENDFORM.                    "CALLER_EXIT
```

### ALV 上的下拉框

#### 事件定义

```ABAP
DATA: ls_event TYPE LINE OF slis_t_event.
ls_event-name = 'CALLER_EXIT'.
ls_event-form = 'FRM_CALLER_EXIT'.
APPEND ls_event TO gt_event.
```

#### 设置 Fieldcat 属性

设置下拉字段为可输入`fieldcat-edit = 'X'.`，设置 `fieldcat-drdn_hndl = '1'.`

- 1：为下拉框对应的组，可以用数字来标记下拉框的组，以此来实现多个下拉框

#### 设置下拉框内容

```ABAP
DATA: lt_ddval TYPE lvc_t_drop,
      ls_ddval TYPE lvc_s_drop.
ls_ddval-handle = '1'.
ls_ddval-value  = 'value1'.
APPEND ls_ddval TO lt_ddval.
ls_ddval-handle = '1'.
ls_ddval-value  = 'value2'.
APPEND ls_ddval TO lt_ddval.
```

#### 事件功能 FORM

```ABAP
FORM frm_caller_exit USING ls_data TYPE slis_data_caller_exit.
  DATA: lo_grid TYPE REF TO cl_gui_alv_grid.
  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    IMPORTING
      e_grid = lo_grid.
  CALL METHOD lo_grid->set_drop_down_table
    EXPORTING
      it_drop_down = lt_ddval.
ENDFORM.                    "FRM_CALLER_EXIT
```

