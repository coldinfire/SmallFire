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

```JS
method DATA_LOAD .

  DATA:  rARBPL   TYPE REF TO data,
         rKOSTL   TYPE REF TO data,
         rAUFNR   TYPE REF TO data,
         rAUART   TYPE REF TO data,
         rBUDAT   TYPE REF TO data,

        """"为alv输出的结构
        lt_ZALV_ZPEFF_TOTAL   TYPE TABLE OF ZALV_ZPEFF_TOTAL,
        ls_ZALV_ZPEFF_TOTAL   TYPE ZALV_ZPEFF_TOTAL,
        lt_ZALV_PROD_EFF   TYPE TABLE OF ZALV_PROD_EFF,
        ls_ZALV_PROD_EFF   TYPE ZALV_PROD_EFF,
        lo_node          TYPE REF TO if_wd_context_node,
        lo_nd_zoption   TYPE REF TO if_wd_context_node,
        lo_el_zoption   TYPE REF TO if_wd_context_element,
        ls_zoption      TYPE wd_this->element_zoption,
        lv_p_total         TYPE wd_this->element_zoption-p_total,
        lv_p_detail         TYPE wd_this->element_zoption-p_detail.

  FIELD-SYMBOLS:<RARBPL>          TYPE table,
                <RKOSTL>          TYPE table,
                <RAUFNR>          TYPE table,
                 <RAUART>          TYPE table,
                <RBUDAT>          TYPE table.


  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'ARBPL'
    RECEIVING
      rt_range_table = rARBPL.

  ASSIGN rARBPL->* TO <rARBPL>.

  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'KOSTL'
    RECEIVING
      rt_range_table = rKOSTL.

  ASSIGN rKOSTL->* TO <RKOSTL>.

  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'AUFNR'
    RECEIVING
      rt_range_table = rAUFNR.

  ASSIGN rAUFNR->* TO <RAUFNR>.

  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'AUART'
    RECEIVING
      rt_range_table = rAUART.

  ASSIGN rAUART->* TO <RAUART>.

  CALL METHOD wd_this->m_handler->get_range_table_of_sel_field
    EXPORTING
      i_id           = 'BUDAT'
    RECEIVING
      rt_range_table = rBUDAT.

  ASSIGN rBUDAT->* TO <RBUDAT>.


  """"
  lo_nd_zoption = wd_context->get_child_node( name = wd_this->wdctx_zoption ).
  lo_el_zoption = lo_nd_zoption->get_element( ).

* get single attribute
  lo_el_zoption->get_attribute(
    EXPORTING
      name =  'P_TOTAL'
    IMPORTING
      value = lv_p_TOTAL ).


* get single attribute
  lo_el_zoption->get_attribute(
    EXPORTING
      name =  'P_DETAIL'
    IMPORTING
      value = lv_p_DETAIL ).

  """"
  CALL FUNCTION 'Z_GET_ZPEFF' DESTINATION 'WIKR3'
    EXPORTING
      iv_total         = lv_p_total
      iv_detail        = lv_p_detail
   TABLES:
IT_ARBPL  = <RARBPL>
IT_KOSTL = <RKOSTL>
IT_AUFNR = <RAUFNR>
IT_AUART = <RAUART>
IT_BUDAT = <RBUDAT>
IT_TOTAL =  lt_ZALV_ZPEFF_TOTAL
IT_DETAIL = lt_ZALV_PROD_EFF.


  if lv_p_total = 'X'.
    lo_node = wd_context->get_child_node( name = 'ZALV_ZPEFF_TOTAL' ).
    lo_node->bind_table( lt_ZALV_ZPEFF_TOTAL ).
  ELSEIF lv_p_detail = 'X'.
    lo_node = wd_context->get_child_node( name = 'ZALV_PROD_EFF' ).
    lo_node->bind_table( lt_ZALV_PROD_EFF ).
  ENDIF.

endmethod.
```

