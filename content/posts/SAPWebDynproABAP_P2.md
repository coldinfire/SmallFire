---
title: " Web Dynpro ABAP Program:DATA_LOAD "
date: 2018-10-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

## Component Controller: DATA_LOAD

```ABAP
METHOD DATA_LOAD .
  " ALV 输出的结构定义 "
  DATA: lt_ZALV_ZPEFF_TOTAL   TYPE TABLE OF ZALV_ZPEFF_TOTAL,
        ls_ZALV_ZPEFF_TOTAL   TYPE ZALV_ZPEFF_TOTAL,
        lt_ZALV_PROD_EFF      TYPE TABLE OF ZALV_PROD_EFF,
        ls_ZALV_PROD_EFF      TYPE ZALV_PROD_EFF,
        lo_node               TYPE REF TO if_wd_context_node.
  "  选择屏幕数据的单属性定义 "
  DATA: lo_nd_zoption   TYPE REF TO if_wd_context_node,
        lo_el_zoption   TYPE REF TO if_wd_context_element,
        ls_zoption      TYPE wd_this->element_zoption,
        lv_p_total      TYPE wd_this->element_zoption-p_total,
        lv_p_detail     TYPE wd_this->element_zoption-p_detail. 
  " 选择屏幕数据的多属性定义 "
  DATA: RARBPL   TYPE REF TO data,
        RKOSTL   TYPE REF TO data,
        RAUFNR   TYPE REF TO data,
        RAUART   TYPE REF TO data,
        RBUDAT   TYPE REF TO data.
  FIELD-SYMBOLS:
        <RARBPL> TYPE table,
        <RKOSTL> TYPE table,
        <RAUFNR> TYPE table,
        <RAUART> TYPE table,
        <RBUDAT> TYPE table.
  DATA: l_name TYPE string.
  " 获取用户名:需要在Content Administration中配置 Application Para 'userid=<User.LogonUid>' "
  l_name = wdr_task=>client_window->if_wdr_client_info_object~get_parameter( 'USERID' ).
  TRANSLATE l_name TO UPPER CASE.
  " 选择屏幕数据的单属性获取 "
  lo_nd_zoption = wd_context->get_child_node( name = wd_this->wdctx_zoption ).
  lo_el_zoption = lo_nd_zoption->get_element( ).
  IF lo_el_zoption IS NOT INITIAL.
    lo_el_zoption->get_attribute(
      EXPORTING
        name =  'P_TOTAL'
      IMPORTING
        value = lv_p_total ).
    lo_el_zoption->get_attribute(
      EXPORTING
        name =  'P_DETAIL'
      IMPORTING
        value = lv_p_detail ).
  ENDIF.
  " 选择屏幕数据的多属性获取 "
  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'ARBPL'
    RECEIVING
      rt_range_table = RARBPL.
  ASSIGN RARBPL->* TO <RARBPL>.
  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'KOSTL'
    RECEIVING
      rt_range_table = RKOSTL.
  ASSIGN RKOSTL->* TO <RKOSTL>.
  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'AUFNR'
    RECEIVING
      rt_range_table = RAUFNR.
  ASSIGN RAUFNR->* TO <RAUFNR>.
  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'AUART'
    RECEIVING
      rt_range_table = RAUART.
  ASSIGN RAUART->* TO <RAUART>.
  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'BUDAT'
    RECEIVING
      rt_range_table = RBUDAT.
  ASSIGN RBUDAT->* TO <RBUDAT>.
  " 调用SAP Funciton 获取数据 "
  CALL FUNCTION 'Z_GET_ZPEFF' DESTINATION 'WIKR3'
    EXPORTING
      iv_total  = lv_p_total
      iv_detail = lv_p_detail
   TABLES
      IT_ARBPL = <RARBPL>
	  IT_KOSTL = <RKOSTL>
	  IT_AUFNR = <RAUFNR>
	  IT_AUART = <RAUART>
	  IT_BUDAT = <RBUDAT>
	  IT_TOTAL =  lt_ZALV_ZPEFF_TOTAL
	  IT_DETAIL = lt_ZALV_PROD_EFF.
  " 结果集的上下文节点绑定 "
  IF lv_p_total = 'X'.
    lo_node = wd_context->get_child_node( name = 'ZALV_ZPEFF_TOTAL' ).
    lo_node->bind_table( lt_ZALV_ZPEFF_TOTAL ).
  ENDIF.
  " Color field setting "
  LOOP AT lt_ZALV_PROD_EFF into ls_ZALV_PROD_EFF.
  	ls_ZALV_PROD_EFF-color = cl_wd_abstr_master_table_col=>e_cell_design-calendar_green.
    " ls_ZALV_PROD_EFF-color = '27'. "
    " ls_ZALV_PROD_EFF-color = '02'. "
    " ls_ZALV_PROD_EFF-color = cl_wd_abstr_master_table_col=>e_cell_design-badvalue_dark. "
    MODIFY lt_ZALV_PROD_EFF FROM ls_ZALV_PROD_EFF.
    CLEAR ls_ZALV_PROD_EFF.
  ENDLOOP.
  IF lv_p_detail = 'X'.
    lo_node = wd_context->get_child_node( name = 'ZALV_PROD_EFF' ).
    lo_node->bind_table( lt_ZALV_PROD_EFF ).
  ENDIF.
ENDMETHOD.
```

