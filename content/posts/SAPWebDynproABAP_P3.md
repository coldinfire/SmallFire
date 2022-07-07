---
title: " Web Dynpro ABAP Program:WDDOINIT "
date: 2018-10-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

## Component Controller/INPUT_VIEW: WDDOINIT

```ABAP
METHOD WDDOINIT.
TYPES:BEGIN OF soption,
     typename TYPE string, " Selection screen field ref type element "
     id       TYPE string, " selection screen field name "
   END OF soption.
DATA: it_soption TYPE TABLE OF soption,
      wa_soption TYPE soption.
DATA it_range_table  TYPE REF TO data.
" ALV Object Setting "
DATA: alv_cmp_usage   TYPE REF TO if_wd_component_usage,  
      alv_controller  TYPE REF TO iwci_salv_wd_table,     
      alv_config      TYPE REF TO cl_salv_wd_config_table.
DATA: column_settings TYPE REF TO if_salv_wd_column_settings,
      table_settings  TYPE REF TO if_salv_wd_table_settings,
      column_nd       TYPE REF TO cl_salv_wd_column,
      column_header   TYPE REF TO cl_salv_wd_column_header.
DATA: fields TYPE TABLE OF dfies,
      field  TYPE dfies,
      l_fieldname TYPE string,
      l_invisible TYPE c,
      l_position  TYPE i.
" Select Screen option node setting "
DATA: soption_cmp_usage TYPE REF TO if_wd_component_usage,
      select_options    TYPE REF TO iwci_wdr_select_options.
DATA: nd_zoption TYPE REF TO if_wd_context_node,
      el_zoption TYPE REF TO if_wd_context_element.
DATA: lt_zoption TYPE wd_this->elements_zoption,
      ls_zoption TYPE wd_this->element_zoption,
      p_total TYPE wd_this->element_zoption-p_total,
      p_detail TYPE wd_this->element_zoption-p_detail.
" Create the alv component usage "
  alv_cmp_usage = wd_this->wd_cpuse_alv( ).
  IF alv_cmp_usage->has_active_component( ) IS INITIAL.
    alv_cmp_usage->create_component( ).
  ENDIF. 
  " Get Option Node Field "
  nd_zoption = wd_context->get_child_node( name = wd_this->wdctx_zoption ).
  " Get Option Element "
  el_zoption = nd_zoption->get_element( ).
  " Set Attribute status "
  IF el_zoption IS NOT INITIAL.
    " Set attribute "
    el_zoption->set_attribute(
        name =  `P_DETAIL`
        value = 'X' ).
    " Get attribute "
    el_zoption->get_attribute(
      EXPORTING
        name =  `P_TOTAL`
      IMPORTING
        value = p_total ).
    " Get all declared attributes "
    el_zoption->get_static_attributes(
      IMPORTING
        STATIC_ATTRIBUTES = ls_zoption ).
    el_zoption->get_static_attributes_table(
      IMPORTING
        NEW_ITEMS = lt_zoption ).
  ENDIF.
  " Create the select options component usage " 
  soption_cmp_usage = wd_this->wd_cpuse_select_options( ).
  IF soption_cmp_usage->has_active_component( ) IS INITIAL.
    soption_cmp_usage->create_component( ).
  ENDIF.
  " Get the select option usage "
  select_options = wd_this->wd_cpifc_select_options( ).
  " Init the select option "
  wd_this->m_handler = select_options->init_selection_screen( ).
  wd_this->m_handler->set_global_options(
                              i_display_btn_cancel  = abap_false
                              i_display_btn_check   = abap_false
                              i_display_btn_reset   = abap_false
                              i_display_btn_execute = abap_false ).
  " Collect all select options element "
  CLEAR wa_soption.
  wa_soption-typename = 'ARBPL'.
  wa_soption-id       = 'ARBPL'.
  APPEND wa_soption TO it_soption.
  CLEAR wa_soption.
  wa_soption-typename = 'KOSTL'.
  wa_soption-id       = 'KOSTL'.
  APPEND wa_soption TO it_soption.
  CLEAR wa_soption.
  wa_soption-typename = 'AUFNR'.
  wa_soption-id       = 'AUFNR'.
  APPEND wa_soption TO it_soption.
  CLEAR wa_soption.
  wa_soption-typename = 'AUFART'.
  wa_soption-id       = 'AUART'.
  APPEND wa_soption TO it_soption.
  CLEAR wa_soption.
  wa_soption-typename = 'BUDAT'.
  wa_soption-id       = 'BUDAT'.
  APPEND wa_soption TO it_soption.
  " Select screen default value define "
  DATA: lt_budat_table TYPE RANGE OF datum .
  DATA: ls_budat_table LIKE LINE OF lt_budat_table .
  FIELD-SYMBOLS: <it_budat_table> LIKE lt_budat_table .
  " Cycle create Range Table "
  LOOP AT it_soption INTO wa_soption.
    " Create one range table "
    it_range_table = wd_this->m_handler->create_range_table( i_typename = wa_soption-typename ).
    " 界面ranges给初始值 "
    IF wa_soption-typename = 'BUDAT'.
      ASSIGN it_range_table->* TO <it_budat_table> .
      ls_budat_table-sign   = 'I' .
      ls_budat_table-option = 'EQ' .
      ls_budat_table-low    = sy-datum .
      APPEND ls_budat_table TO <it_budat_table> .
    ENDIF.
    wd_this->m_handler->add_selection_field( i_id  = wa_soption-id
                                        it_result  = it_range_table
                                      i_read_only  = ABAP_FALSE
                                      i_obligatory = ABAP_FALSE
                                    i_no_extension = ABAP_FALSE
                                    i_no_intervals = ABAP_FALSE ).
    CLEAR wa_soption.	
  ENDLOOP.
endmethod.
```

