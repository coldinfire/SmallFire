---
title: " ALV 中自定义搜索帮助 "
date: 2018-08-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
---

### 方法一

如果希望 ALV 中某字段具有搜索帮助，第一种办法当然是对表中某字段的引用，设置ref_table、ref_field，将自动触发该字段所带的搜索帮助。

### 方法二

第二种办法就是利用自定义代码来实现 ALV 的搜索帮助，显然它的功能更强大、更灵活。

#### OO ALV

针对在 OO ALV 中实现搜索帮助，其主要步骤有：

1：对需要自定义搜索帮助的字段，设置其 fieldcat 时:

-  `gs_fieldcat-f4availabl = 'X'.`

2：在 ALV 的事件处理类中添加 Search Help Method，方法定义如下：

```ABAP
CLASS lcl_event_receiver DEFINITION.
  PUBLIC SECTION.
    METHODS: handle_onf4 FOR EVENT onf4 OF cl_gui_alv_grid
            IMPORTING e_fieldname es_row_no er_event_data et_bad_cells sender.
ENDCLASS.  "LCL_EVENT_RECEIVER DEFINITION"
CLASS lcl_event_receiver IMPLEMENTATION.
  METHOD  handle_onf4.
    "搜索帮助参数的自定义f4检索帮助"
    PERFORM f4_help_value USING e_fieldname es_row_no er_event_data sender.
    "设置后，alv稳定刷新"
    PERFORM refresh_table_alv.
  ENDMETHOD.     "HANDLE_ONF4"
ENDCLASS.   "LCL_EVENT_RECEIVER IMPLEMENTATION"
DATA go_evt_receiver TYPE REF TO lcl_event_receiver.
```

- e_fieldname：代表用户点击了 ALV 的哪个字段来触发搜索帮助
- es_row_no：代表了当前行信息，es_row_no-row_id：就是 ALV 中内表记录的 index
- er_event_data：代表了当前用户对 ALV 进行了哪些编辑的信息。在 Method 的最后，记得加上 `er_event_data->m_event_handled = 'X'.` 通知系统搜索事件处理完毕，这样就不会调用系统标准的Search Help。
- et_bad_cells：不正确的单元格

3：在创建 ALV 对象之后，要对需要自定义搜索帮助的字段进行注册并为其指定事件处理类。

```ABAP
DATA: lt_f4 TYPE lvc_t_f4,
      ls_f4 TYPE lvc_s_f4.
CLEAR:lt_f4,ls_f4.
ls_f4-fieldname = 'FIELD_NAME'. "需要添加搜索帮助的内表字段"
ls_f4-register = 'X'.
ls_f4-chngeafter = 'X'.
ls_f4-getbefore = 'X'.
INSERT ls_f4 INTO TABLE lt_f4.
"事件注册"
CALL METHOD go_alv_grid->register_f4_for_fields
  EXPORTING
    it_f4 = lt_f4.
"事件处理类"
CREATE OBJECT go_evt_receiver.
SET HANDLER go_evt_receiver->handle_onf4 FOR go_alv_grid.
```

- lvc_s_f4 中的字段 getbefore 和 changeafter 代表是否触发 data_changed 事件。


4：方法代码实现

```ABAP
*&---------------------------------------------------------------------*
*&      Form  F4_HELP_VALUE
*&---------------------------------------------------------------------*
*       窗口时间参数的自定义f4检索帮助
*----------------------------------------------------------------------*
FORM f4_help_value USING p_fieldname  TYPE lvc_fname
                         p_row_no     TYPE lvc_s_roid
                         p_event      TYPE cl_alv_event_data
                         p_sender     TYPE REF TO cl_gui_alv_grid.
  DATA: lt_return TYPE STANDARD TABLE OF ddshretval,
        ls_return TYPE ddshretval.
  DATA: ls_modi TYPE lvc_s_modi.
  FIELD-SYMBOLS: <lv_itab> TYPE lvc_t_modi.
  DATA: lt_value_tab TYPE TABLE OF XXX.
  "获取搜索帮助数据 存入表LT_VALUE_TAB 
  CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
    EXPORTING
      retfield         = 'WERKS'       "内表里面的字段"
      dynpprog         = sy-repid
      dynpnr           = sy-dynnr
      dynprofield      = 'WERKS'       "画面上绑定字段"
      value_org        = 'S'
      callback_program = sy-repid
    TABLES
      value_tab        = lt_value_tab  "需要显示帮助的值内表"
      return_tab       = lt_return     "返回值"
    EXCEPTIONS
      parameter_error  = 1
      no_values_found  = 2
      OTHERS           = 3.
  IF sy-subrc = 0.
    ASSIGN p_event->m_data->* TO <lv_itab> .
    READ TABLE lt_return INTO ls_return INDEX 1.
    IF sy-subrc EQ 0 .
      ls_modi-row_id  = p_row_no-row_id.
      ls_modi-fieldname = p_fieldname.
      ls_modi-value = ls_return-fieldval.
      APPEND ls_modi TO <lv_itab>.
    ENDIF.
  ENDIF.
*  Inform ALV Grid that event 'onf4' has been processed
   p_event->m_event_handled = 'X'.
ENDFORM.  " F4_HELP_VALUE "
*&---------------------------------------------------------------------*
*&      Form  REFRESH_TABLE_ALV
*&---------------------------------------------------------------------*
*       设置后，alv稳定刷新
*----------------------------------------------------------------------*
FORM refresh_table_alv .
  DATA: stbl TYPE lvc_s_stbl.
  stbl-row = 'X'.  " 基于行的稳定刷新 "
  stbl-col = 'X'.  " 基于列稳定刷新 "
  CALL METHOD go_alv_grid->refresh_table_display
    EXPORTING
      is_stable = stbl.
ENDFORM.       " REFRESH_TABLE_ALV "
```

#### GRID ALV

注册事件：

```ABAP
DATA: gt_events TYPE slis_t_event WITH HEADER LINE,
      gs_event  LIKE LINE OF gt_events.
DATA: go_grid TYPE REF TO cl_gui_alv_grid.
gt_events-name = 'CALLER_EXIT'.   "slis_ev_caller_exit_at_start事件"
gt_events-form = 'FRM_CALLER_EXIT'.
APPEND gt_events.
CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
 EXPORTING
   it_evetns = gt_evetns[].
```

事件功能：

```ABAP
*&---------------------------------------------------------------------*
*&      Form  FRM_CALLER_EXIT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->E_GRID     text
*----------------------------------------------------------------------*
FORM frm_caller_exit USING e_grid TYPE slis_data_caller_exit.
  DATA: lt_f4 TYPE lvc_t_f4,
        ls_f4 TYPE lvc_s_f4.
  CLEAR ls_f4.
  ls_f4-fieldname  = 'WERKS'.   "需要定义F4帮助按钮的字段"
  ls_f4-register   = 'X'.
  ls_f4-getbefore  = 'X'.
  ls_f4-chngeafter = 'X'.
  INSERT ls_f4 INTO TABLE lt_f4.
  "获取 OO ALV 对象"
  CALL FUNCTION 'GET_GLOBALS_FROM_SLVC_FULLSCR'
    IMPORTING
      e_grid  = go_grid.
  "事件注册"
  CALL METHOD go_grid->register_f4_for_fields
    EXPORTING
      it_f4 = lt_f4[].
  "事件处理类"
  CREATE OBJECT go_evt_receiver.
  SET HANDLER go_evt_receiver->handle_onf4 FOR go_grid.
ENDFORM.                    "frm_caller_exit"
```

### DEMO

```ABAP
TYPE-POOLS: slis.
*---------------------------------------------------------------------*
*       CLASS lcl_event_receiver DEFINITION
*---------------------------------------------------------------------*
CLASS lcl_event_receiver DEFINITION.
  PUBLIC SECTION.
    CLASS-METHODS:
    handle_onf4 FOR EVENT onf4 OF cl_gui_alv_grid
      IMPORTING e_fieldname es_row_no er_event_data,
    on_f4 FOR EVENT onf4 OF cl_gui_alv_grid
      IMPORTING sender e_fieldname e_fieldvalue es_row_no
                er_event_data et_bad_cells e_display,
    on_data_changed FOR EVENT data_changed OF cl_gui_alv_grid
      IMPORTING e_onf4 e_onf4_before e_onf4_after er_data_changed
                e_ucomm sender.
  PRIVATE SECTION.
    TYPES: ddshretval_table TYPE TABLE OF ddshretval.
    CLASS-METHODS: my_f4 
    IMPORTING sender TYPE REF TO cl_gui_alv_grid
      et_bad_cells   TYPE lvc_t_modi
      es_row_no      TYPE lvc_s_roid
      er_event_data  TYPE REF TO cl_alv_event_data
      e_display      TYPE c
      e_fieldname    TYPE lvc_fname
    EXPORTING im_lt_f4 TYPE ddshretval_table.
ENDCLASS.                    "lcl_event_receiver DEFINITION"

*---------------------------------------------------------------------*
*       CLASS lcl_event_receiver IMPLEMENTATION
*---------------------------------------------------------------------*
CLASS lcl_event_receiver IMPLEMENTATION.
  METHOD handle_onf4.
    DATA: ls_modi TYPE lvc_s_modi,
          lt_ret_tab TYPE TABLE OF ddshretval WITH HEADER LINE.
    FIELD-SYMBOLS <modtab> TYPE lvc_t_modi.
    IF e_fieldname = 'FIELD_NAME'. "我们自定义搜索的字段名"
      READ TABLE gt_alv_data INDEX es_row_no-row_id.
      CHECK sy-subrc = 0.
**  这里可以添加代码以对lt_hitlist内表进行填充
      CALL FUNCTION 'F4IF_INT_TABLE_VALUE_REQUEST'
        EXPORTING
          retfield        = 'HIT_FIELD'
          value_org       = 'S'
        TABLES
          value_tab       = lt_hitlist
          return_tab      = lt_ret_tab
        EXCEPTIONS
          parameter_error = 1
          no_values_found = 2
          OTHERS          = 3.
      IF sy-subrc = 0.
**  Update the value in ALV cell
        READ TABLE lt_ret_tab INDEX 1.
        IF sy-subrc = 0. " User didn't cancel "
          ls_modi-row_id = es_row_no-row_id.
          ls_modi-fieldname = e_fieldname.
          ls_modi-value = lt_ret_tab-fieldval.
          ASSIGN er_event_data->m_data->* TO <modtab>.
          APPEND ls_modi TO <modtab>.
        ENDIF.
      ENDIF.
**  Inform ALV Grid that event 'onf4' has been processed
      er_event_data->m_event_handled = 'X'.
    ENDIF.
  ENDMETHOD.                    "handle_onf4"
  
  METHOD on_f4.
    DATA: ls_f4 TYPE ddshretval,
          lt_f4 TYPE TABLE OF ddshretval.
    FIELD-SYMBOLS: <itab> TYPE lvc_t_modi.
    DATA: ls_modi TYPE lvc_s_modi.
    CALL METHOD my_f4
      EXPORTING
        sender        = sender
        es_row_no     = es_row_no
        er_event_data = er_event_data
        et_bad_cells  = et_bad_cells
        e_display     = e_display
        e_fieldname   = e_fieldname
      IMPORTING
        im_lt_f4      = lt_f4.
    ASSIGN er_event_data->m_data->* TO <itab>.
    READ TABLE lt_f4 INTO ls_f4 WITH KEY fieldname = 'MTART'.
    IF NOT ls_f4 IS INITIAL.
      ls_modi-row_id    = es_row_no-row_id.
      ls_modi-fieldname = 'MTART'.
      ls_modi-value     = ls_f4-fieldval.
      APPEND ls_modi TO <itab>.
      ls_modi-row_id = es_row_no-row_id.
      ls_modi-fieldname = 'VALUE'.
      IF ls_f4-fieldval < 'D'.
        ls_modi-value = 100.
      ELSEIF ls_f4-fieldval < 'R'.
        ls_modi-value = 1000.
      ELSE.
        ls_modi-value = 10000.
      ENDIF.
      APPEND ls_modi TO <itab>.
    ENDIF.
    READ TABLE lt_f4 INTO ls_f4 WITH KEY fieldname = 'MTBEZ'.
    IF NOT ls_f4 IS INITIAL.
      ls_modi-row_id    = es_row_no-row_id.
      ls_modi-fieldname = 'MTBEZ'.
      ls_modi-value     = ls_f4-fieldval.
      APPEND ls_modi TO <itab>.
    ENDIF.
    er_event_data->m_event_handled = 'X'.
  ENDMETHOD.                                                "on_f4"
*---------------------------------------------------------------------*
*       METHOD on_data_changed                                        *
*---------------------------------------------------------------------*
  METHOD on_data_changed.
  ENDMETHOD.                    "on_data_changed"
*---------------------------------------------------------------------*
*       METHOD my_f4  insert here your own f4-help                    *
*---------------------------------------------------------------------*
  METHOD my_f4.
    DATA: lw_tab      LIKE LINE OF gt_data,
          lt_fcat     TYPE lvc_t_fcat,
          ls_fieldcat TYPE lvc_s_fcat,
          l_tabname   TYPE dd03v-tabname,
          l_fieldname TYPE dd03v-fieldname,
          l_help_valu TYPE help_info-fldvalue,
          lt_bad_cell TYPE lvc_t_modi,
    lp_wa       TYPE REF TO data.
    FIELD-SYMBOLS: <l_field_value> TYPE ANY,
                   <ls_wa>         TYPE ANY.
    CALL METHOD sender->get_frontend_fieldcatalog
      IMPORTING
        et_fieldcatalog = lt_fcat.
    READ TABLE gt_data INDEX es_row_no-row_id INTO lw_tab.
    CREATE DATA lp_wa LIKE LINE OF gt_data.
    ASSIGN lp_wa->* TO <ls_wa>.
    <ls_wa> = lw_tab.
    READ TABLE lt_fcat WITH KEY fieldname = e_fieldname INTO ls_fieldcat.
    MOVE ls_fieldcat-ref_table TO l_tabname.
    MOVE ls_fieldcat-fieldname TO l_fieldname.
    ASSIGN COMPONENT ls_fieldcat-fieldname OF STRUCTURE lw_tab TO <l_field_value>.
    WRITE <l_field_value> TO l_help_valu.
    PERFORM f4_set USING sender
          lt_fcat
          lt_bad_cell
          es_row_no-row_id
          <ls_wa>.
    CALL FUNCTION 'F4IF_FIELD_VALUE_REQUEST'
      EXPORTING
        tabname          = l_tabname
        fieldname        = l_fieldname
        display          = e_display
        value            = l_help_valu
        callback_program = 'ZTEST7'
        callback_form    = 'F4'
      TABLES
        return_tab       = im_lt_f4
      EXCEPTIONS
        parameter_error  = 1
        no_values_found  = 2
        OTHERS           = 3.
  ENDMETHOD.                        "my_f4"
ENDCLASS.                    "lcl_event_receiver IMPLEMENTATION"
```

