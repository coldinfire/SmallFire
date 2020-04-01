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

## Component Controller: WDDOINIT

```JS
method WDDOINIT .
TYPES:BEGIN OF soption,
        typename TYPE string, " Selection screen field ref type element
        id       TYPE string, " selection screen field name
      END OF soption.
DATA:  l_alv_cmp_usage     TYPE REF TO if_wd_component_usage,
       l_alvctrl           TYPE REF TO iwci_salv_wd_table,
       l_alvmodel          TYPE REF TO cl_salv_wd_config_table,
       l_it_range_table    TYPE REF TO data,
       l_read_only         TYPE abap_bool,
       l_soption_cmp_usage TYPE REF TO if_wd_component_usage,
       l_alvcolset         TYPE REF TO if_salv_wd_column_settings,
       l_column            TYPE REF TO cl_salv_wd_column,
       l_it_field          TYPE TABLE OF dfies,
       l_wa_field          TYPE dfies,
       l_fieldname         TYPE string,
       l_invisible         TYPE c,
       l_position          TYPE i,
       l_it_soption        TYPE TABLE OF soption,
       l_wa_soption        TYPE soption,
       lo_select_options   TYPE REF TO iwci_wdr_select_options,

       l_it_range_tableh    TYPE REF TO data,
       l_soption_cmp_usageh TYPE REF TO if_wd_component_usage,
       l_col_head           TYPE REF TO cl_salv_wd_column_header ,
       l_it_soptionh        TYPE TABLE OF soption,
       l_wa_soptionh        TYPE soption,
       lo_select_optionsh   TYPE REF TO iwci_wdr_select_options.
  DATA: l_abap_bool        TYPE abap_bool.

  l_alv_cmp_usage = wd_this->wd_cpuse_alv( ).
  IF l_alv_cmp_usage->has_active_component( ) IS INITIAL.
    l_alv_cmp_usage->create_component( ).
  ENDIF.

* Get reference to model 获取参考模型
  l_alvctrl = wd_this->wd_cpifc_alv( ).
* Select Screen node define
  DATA  lo_nd_zoption TYPE REF TO if_wd_context_node.
  DATA: lo_el_zoption TYPE REF TO if_wd_context_element,
        ls_zoption    TYPE wd_this->element_zoption ,
        lv_p_total  TYPE wd_this->element_zoption-p_total,
        lv_p_detail  TYPE wd_this->element_zoption-p_detail.
  lo_nd_zoption   = wd_context->get_child_node( name = wd_this->wdctx_zoption ).
  lo_el_zoption = lo_nd_zoption->get_element( ).

  " 选择界面复选框设置默认值
  IF lo_el_zoption IS NOT INITIAL.
  lo_el_zoption->set_attribute(
    EXPORTING
      name =  `P_DETAIL`
      value = 'X' ).
  ENDIF.
* create the used component
  l_soption_cmp_usage = wd_this->wd_cpuse_select_options( ).

  IF l_soption_cmp_usage->has_active_component( ) IS INITIAL.
    l_soption_cmp_usage->create_component( ).
  ENDIF.
  lo_select_options = wd_this->wd_cpifc_select_options( ).
* init the select screen
  wd_this->m_handler = lo_select_options->init_selection_screen( ).
  wd_this->m_handler->set_global_options(
                              i_display_btn_cancel  = abap_false
                              i_display_btn_check   = abap_false
                              i_display_btn_reset   = abap_false
                              i_display_btn_execute = abap_false ).
* Select screen group init
  l_wa_soption-typename = 'ARBPL'.
  l_wa_soption-id       = 'ARBPL'.
  APPEND l_wa_soption TO l_it_soption.
  l_wa_soption-typename = 'KOSTL'.
  l_wa_soption-id       = 'KOSTL'.
  APPEND l_wa_soption TO l_it_soption.
  l_wa_soption-typename = 'AUFNR'.
  l_wa_soption-id       = 'AUFNR'.
  APPEND l_wa_soption TO l_it_soption.
  l_wa_soption-typename = 'AUFART'.
  l_wa_soption-id       = 'AUART'.
  APPEND l_wa_soption TO l_it_soption.
  l_wa_soption-typename = 'BUDAT'.
  l_wa_soption-id       = 'BUDAT'.
  APPEND l_wa_soption TO l_it_soption.
* Select screen default value define
  DATA: lt_werks_table TYPE RANGE OF werks_d .
  DATA: ls_werks_table LIKE LINE OF lt_werks_table .
  FIELD-SYMBOLS: <it_werks_table> LIKE lt_werks_table .
  
  LOOP AT l_it_soption INTO l_wa_soption.
    l_it_range_table = wd_this->m_handler->create_range_table( i_typename = l_wa_soption-typename ).
    " 界面ranges给初始值
    IF l_wa_soption-typename = 'BUDAT'.
      ASSIGN l_it_range_table->* TO <it_budat_table> .
      ls_budat_table-sign   = 'I' .
      ls_budat_table-option = 'EQ' .
      ls_budat_table-low    = sy-datum .
      APPEND ls_budat_table TO <it_budat_table> .
    ENDIF.

    wd_this->m_handler->add_selection_field( i_id  = l_wa_soption-id
                                     i_obligatory  = ''
                                        it_result  = l_it_range_table
                                      i_read_only  = l_read_only ).
  ENDLOOP.

* create the used component
  l_soption_cmp_usageh = wd_this->wd_cpuse_select_options( ).
  IF l_soption_cmp_usageh->has_active_component( ) IS INITIAL.
    l_soption_cmp_usageh->create_component( ).
  ENDIF.
  lo_select_optionsh = wd_this->wd_cpifc_select_options( ).
* init the select screen
  wd_this->m_handlerh = lo_select_optionsh->init_selection_screen( ).
  wd_this->m_handlerh->set_global_options(
                              i_display_btn_cancel  = abap_false
                              i_display_btn_check   = abap_false
                              i_display_btn_reset   = abap_false
                              i_display_btn_execute = abap_false ).

  LOOP AT l_it_soptionh INTO l_wa_soptionh.
    l_it_range_tableh = wd_this->m_handlerh->create_range_table( i_typename = l_wa_soptionh-typename ).
    wd_this->m_handlerh->add_selection_field( i_id  = l_wa_soptionh-id
                                          it_result = l_it_range_tableh
                                        i_read_only = l_read_only ).
  ENDLOOP.
endmethod.
```

