---
title: " Web Dynpro ABAP:OVS搜索帮助 "
date: 2018-10-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### 普通字段

普通字段，表字段的搜索帮助（在创建节点的时候指定搜索帮助OVS，或者后面加上去）

**Step1** ：创建WDA程序，双击程序组件，在Used Components页签添加OVS组件

- OVS_OWNER  <----->  WDR_OVS

**Step2** ：在需要使用的视图中，添加步骤1中定义的OVS组件

双击视图，在视图属性页签，点击新建将Step1中的两个组件全部添加进视图中。

**Step3** ：将定义的OVS组件添加到节点属性字段的搜索帮助

![Web Dynpro OVS](/images/webdynproABAP/Portal30.png)

**Step4** ：为OVS事件定义方法

输入方法名，选择事件类型为EVENT，在事件里F4选择前面定义的OVS事件。

**Step5** ：代码实现OVS搜索帮助

```html
method ON_OVS_OWNER .
  types:
    begin of lty_stru_input,"定义查询参数结构
      CODEGRUPPE type QCODEGRP,
    end of lty_stru_input.
  types:
    begin of lty_stru_list,"定义搜搜帮助返回结果字段列表
      CODEGRUPPE type QCODEGRP,
      CODE type QCODE,
      KURZTEXT type QTXT_CODE,
      GROUPTEXT type QKTEXTGR,
    end of lty_stru_list.

  data: ls_search_input  type lty_stru_input,
        ls_select_list   type lty_stru_list,
        lt_select_list   type standard table of lty_stru_list,
        ls_text          type wdr_name_value,
        lt_label_texts   type wdr_name_value_list,
        lt_column_texts  type wdr_name_value_list,
        lv_window_title  type string,
        lv_group_header  type string,
        lv_table_header  type string.

  DATA: ls_ORDER_QCODE type ZV_ORDER_QCODE,
        lt_ORDER_QCODE type table of  ZV_ORDER_QCODE.

  field-symbols: <ls_query_params> type lty_stru_input,
                 <ls_selection>    type lty_stru_list.

  DATA lo_nd_l_so_header_n TYPE REF TO if_wd_context_node.

  DATA lo_el_l_so_header_n TYPE REF TO if_wd_context_element.
  DATA ls_l_so_header_n TYPE wd_this->Element_l_so_header_n.
  DATA lv_symptoms_code_g TYPE wd_this->Element_l_so_header_n-symptoms_code_g.

  lo_nd_l_so_header_n = wd_context->get_child_node( name = wd_this->wdctx_l_so_header_n ).
  lo_el_l_so_header_n = lo_nd_l_so_header_n->get_element( ).
  lo_el_l_so_header_n->get_attribute(
    EXPORTING
      name =  `SYMPTOMS_CODE_G`
    IMPORTING
      value = lv_symptoms_code_g ).

  case ovs_callback_object->phase_indicator.
    when if_wd_ovs=>co_phase_0.
      lv_window_title = 'Choice Symptoms Code:'.
      ovs_callback_object->set_configuration(
                label_texts  = lt_label_texts
                column_texts = lt_column_texts
                group_header = lv_group_header
                window_title = lv_window_title
                table_header = lv_table_header
                col_count    = 3
                row_count    = 20 ).

    when if_wd_ovs=>co_phase_1.

    when if_wd_ovs=>co_phase_2.
      select * into corresponding fields of table lt_ORDER_QCODE
      from ZV_ORDER_QCODE
      where KATALOGART = 'Z1'
        and CODEGRUPPE = lv_symptoms_code_g
        AND SPRACHE = SY-LANGU."ADD DEFAULT LANGU BY LY 20170113 .

      clear: lt_select_list.

      loop at lt_ORDER_QCODE into ls_ORDER_QCODE.
        ls_select_list-CODEGRUPPE = ls_ORDER_QCODE-CODEGRUPPE.
        ls_select_list-GROUPTEXT = ls_ORDER_QCODE-GROUPTEXT.
        ls_select_list-CODE = ls_ORDER_QCODE-CODE.
        ls_select_list-KURZTEXT = ls_ORDER_QCODE-KURZTEXT.

        append ls_select_list to lt_select_list.
      endloop.
      ovs_callback_object->set_output_table( output = lt_select_list ).

    when if_wd_ovs=>co_phase_3.
      if ovs_callback_object->selection is not bound.
      endif.

      assign ovs_callback_object->selection->* to <ls_selection>.
      if <ls_selection> is assigned."返回选择结果，可返回多个字段
        ovs_callback_object->context_element->set_attribute(
                               name  = `SYMPTOMS_CODE`
                               value = <ls_selection>-CODE ).
      endif.
  endcase.

endmethod.
```

### Select Option中设置OVS

与普通字段不同的是需要在SELECT OPTION中添加OVS对象

```html
METHOD init_select_options .
  DATA:
    lt_range_table TYPE REF TO data,
    rt_range_table TYPE REF TO data,
    read_only      TYPE abap_bool,
    typename       TYPE string,
    lv_value       TYPE string.
  DATA:
    lr_componentcontroller TYPE REF TO ig_componentcontroller,
    l_ref_cmp_usage        TYPE REF TO if_wd_component_usage.
  DATA:
    display_btn_cancel  TYPE abap_bool,
    display_btn_check   TYPE abap_bool,
    display_btn_reset   TYPE abap_bool,
    display_btn_execute TYPE abap_bool.
  DATA:gt_value_set TYPE wdy_key_value_table,
       gw_value_set TYPE wdy_key_value,
       gt_type      TYPE TABLE OF crmc_proc_type_t,
       gw_type      LIKE LINE OF gt_type.

* create the used component
  l_ref_cmp_usage = wd_this->wd_cpuse_selection( ).
  IF l_ref_cmp_usage->has_active_component( ) IS INITIAL.
    l_ref_cmp_usage->create_component( ).
  ENDIF.
* get a pointer to the interface controller of the select options
*component
  wd_this->m_wd_select_options = wd_this->wd_cpifc_selection( ).

* init the select screen
  wd_this->m_handler =
  wd_this->m_wd_select_options->init_selection_screen( ).

  "sales_org
* create a range table that consists of this new data element
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'CRMT_SALES_ORG' ).
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'SALES_ORG'
                          it_result = lt_range_table
                          i_read_only = read_only ).



* create a range table that consists of this new data element
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'CRMT_OBJECT_ID_DB' ).
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'OBJECT_ID'
                          it_result = lt_range_table
                          i_read_only = read_only ).

* create a range table PROCESS TYPE
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'CRMT_PROCESS_TYPE' ).
  "ADD DEFAULT VALUE SET
  SELECT * INTO TABLE gt_type FROM crmc_proc_type_t
    WHERE process_type IN ('ZSO2','ZSO3','ZSO5')
    AND   langu        = sy-langu.
  LOOP AT gt_type INTO gw_type.
    gw_value_set-key = gw_type-process_type.
    gw_value_set-value = gw_type-p_description.
    APPEND gw_value_set TO gt_value_set.
  ENDLOOP.
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'PROCESS_TYPE'
                          it_result = lt_range_table
                          it_value_set = gt_value_set
                          i_read_only = read_only ).

* create a range table that consists of this new data element
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'BU_PARTNER' ).
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'PARTNER'
                          it_result = lt_range_table
                          i_read_only = read_only ).

* create a range table that consists of this new data element
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'CRMT_POSTING_DATE' ).
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'POSTING_DATE'
                          i_description = lv_value
                          it_result = lt_range_table
                          i_read_only = read_only ).

* create a range table that consists of this new data element
  lt_range_table = wd_this->m_handler->create_range_table( i_typename = 'CRM_J_STATUS' ).
* add a new field to the selection
  wd_this->m_handler->add_selection_field(
                          i_id = 'STAT'
                          it_result = lt_range_table
                          i_value_help_type = 'OVR'
                          i_value_help_id = 'OVS'
                          i_read_only = read_only ).


*End add
* adjust the global options
  wd_this->m_handler->set_global_options(
      i_display_btn_cancel  = display_btn_cancel
      i_display_btn_check   = display_btn_check
      i_display_btn_reset   = display_btn_reset
      i_display_btn_execute = display_btn_execute ).


ENDMETHOD.
```

然后就与普通字段相同的设置

```html
METHOD ovs .
  CHECK i_ovs_data-m_selection_field_id EQ 'STAT'.

  TYPES:
    BEGIN OF lty_stru_input,
      stsma TYPE j_stsma,    "搜索条件
      estat TYPE j_estat,
      txt30 TYPE j_txt30,
    END OF lty_stru_input.
  TYPES:
    BEGIN OF lty_stru_list,
*   add fields for the selection list here
      stsma TYPE j_stsma,    "搜索条件
      estat TYPE j_estat,
      txt04 TYPE j_txt04,
      txt30 TYPE j_txt30,
    END OF lty_stru_list.

  DATA: ls_search_input TYPE lty_stru_input,
        lt_select_list  TYPE STANDARD TABLE OF lty_stru_list,
        ls_select_list  TYPE lty_stru_list,
        ls_text         TYPE wdr_name_value,
        lt_label_texts  TYPE wdr_name_value_list,
        lt_column_texts TYPE wdr_name_value_list,
        lv_window_title TYPE string,
        lv_group_header TYPE string,
        lv_table_header TYPE string,
        gt_tj30t        TYPE TABLE OF tj30t,
        gw_tj30t        LIKE LINE OF gt_tj30t.

  FIELD-SYMBOLS: <ls_query_params> TYPE lty_stru_input,
                 <ls_selection>    TYPE lty_stru_list.

  CASE i_ovs_data-m_ovs_callback_object->phase_indicator.

    WHEN if_wd_ovs=>co_phase_0.  "configuration phase, may be omitted
*   in this phase you have the possibility to define the texts,
*   if you do not want to use the defaults (DDIC-texts)

      ls_text-name = `STSMA`.  "must match a field name of search
      ls_text-value = `Status Profile`. "wd_assist->get_text( `001` ).
      INSERT ls_text INTO TABLE lt_label_texts.

      ls_text-name = `ESTAT`.  "must match a field name of search
      ls_text-value = `User Status`. "wd_assist->get_text( `001` ).
      INSERT ls_text INTO TABLE lt_label_texts.

      ls_text-name = `TXT30`.  "must match a field name of search
      ls_text-value = `Object status`. "wd_assist->get_text( `001` ).
      INSERT ls_text INTO TABLE lt_label_texts.

      ls_text-name = `STSMA`.  "must match a field in list structure
      ls_text-value = `Status Profile`. "wd_assist->get_text( `002` ).
      INSERT ls_text INTO TABLE lt_column_texts.

      ls_text-name = `ESTAT`.  "must match a field in list structure
      ls_text-value = `User Status`. "wd_assist->get_text( `002` ).
      INSERT ls_text INTO TABLE lt_column_texts.

      ls_text-name = `TXT04`.  "must match a field in list structure
      ls_text-value = `Individual status of an object`. "wd_assist->get_text( `002` ).
      INSERT ls_text INTO TABLE lt_column_texts.

      ls_text-name = `TXT30`.  "must match a field in list structure
      ls_text-value = `Object status`. "wd_assist->get_text( `002` ).
      INSERT ls_text INTO TABLE lt_column_texts.

*      lv_window_title = wd_assist->get_text( `003` ).
*      lv_group_header = wd_assist->get_text( `004` ).
*      lv_table_header = wd_assist->get_text( `005` ).

      i_ovs_data-m_ovs_callback_object->set_configuration(
                label_texts  = lt_label_texts
                column_texts = lt_column_texts
                group_header = lv_group_header
                window_title = lv_window_title
                table_header = lv_table_header
                col_count    = 4
                row_count    = 20 ).


    WHEN if_wd_ovs=>co_phase_1.  "set search structure and defaults
*   In this phase you can set the structure and default values
*   of the search structure. If this phase is omitted, the search
*   fields will not be displayed, but the selection table is
*   displayed directly.
*   Read values of the original context (not necessary, but you
*   may set these as the defaults). A reference to the context
*   element is available in the callback object.

      i_ovs_data-m_ovs_callback_object->context_element->get_static_attributes(
          IMPORTING static_attributes = ls_search_input ).
*     pass the values to the OVS component
      i_ovs_data-m_ovs_callback_object->set_input_structure(
          input = ls_search_input ).


    WHEN if_wd_ovs=>co_phase_2.
*   If phase 1 is implemented, use the field input for the
*   selection of the table.
*   If phase 1 is omitted, use values from your own context.

      IF i_ovs_data-m_ovs_callback_object->query_parameters IS NOT BOUND.
******** TODO exception handling
      ENDIF.
      ASSIGN i_ovs_data-m_ovs_callback_object->query_parameters->*
                              TO <ls_query_params>.
      IF NOT <ls_query_params> IS ASSIGNED.
******** TODO exception handling
      ENDIF.

      REFRESH:gt_tj30t.
      DATA: rt_object_id TYPE REF TO data.
      DATA:gt_file TYPE TABLE OF crmc_proc_type,
           gw_file LIKE LINE OF gt_file.
      FIELD-SYMBOLS: <process_type> TYPE table.
      rt_object_id = wd_this->m_handler->get_range_table_of_sel_field( i_id = 'PROCESS_TYPE' ).
      ASSIGN rt_object_id->* TO <process_type>.
      IF <process_type> IS ASSIGNED.
        SELECT * INTO TABLE gt_file FROM crmc_proc_type
          WHERE process_type IN <process_type>
          AND process_type IN ('ZSO2','ZSO3','ZSO5').
      ELSE.
        SELECT * INTO TABLE gt_file FROM crmc_proc_type WHERE process_type IN ('ZSO2','ZSO3','ZSO5').
      ENDIF.
      IF gt_file IS NOT INITIAL.
        SELECT * INTO CORRESPONDING FIELDS OF TABLE lt_select_list
          FROM tj30t FOR ALL ENTRIES IN gt_file WHERE stsma = gt_file-user_stat_proc
          AND spras = sy-langu.
      ENDIF.


*     call business logic for a table of possible values
*     lt_select_list = ???

      i_ovs_data-m_ovs_callback_object->set_output_table( output = lt_select_list ).


    WHEN if_wd_ovs=>co_phase_3.
*   apply result
      FIELD-SYMBOLS : <lt_sel_opt_result> TYPE STANDARD TABLE.

      IF i_ovs_data-m_ovs_callback_object->selection IS NOT BOUND.
******** TODO exception handling
      ENDIF.

      ASSIGN i_ovs_data-m_ovs_callback_object->selection->* TO <ls_selection>.
      IF <ls_selection> IS ASSIGNED.
        ASSIGN i_ovs_data-mt_selected_values->* TO <lt_sel_opt_result>.
        INSERT <ls_selection>-estat INTO TABLE <lt_sel_opt_result>.
*      or
*        I_OVS_DATA-m_ovs_callback_object->context_element->set_static_attributes(
*                               static_attributes = <ls_selection> ).

      ENDIF.
  ENDCASE.
ENDMETHOD.
```

