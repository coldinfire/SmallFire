---
title: " Web Dynpro ABAP:ALV dropdown "
date: 2020-08-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### ALV Dropdown 

#### Step1:给ALV结构添加VALUE SET字段。

Attribute Name : TYPE_SET

Type : WDR_CONTEXT_ATTR_VALUE_LIST

#### Step2:将TYPE_SET字段设置为值范围

```html
METHOD wddoinit .
  DATA:
    lo_node      TYPE REF TO if_wd_context_node,
    lo_elem      TYPE REF TO if_wd_context_element,
    ls_order     TYPE wd_this->element_order,
    lt_order     TYPE wd_this->elements_order,
    ls_value_set TYPE wdr_context_attr_value.
  CLEAR:ls_order.
  ls_order-object_id = '0210002047'.
  ls_order-process_type = 'ZSV1'.
  ls_value_set-value = 'ZSV2'.
  ls_value_set-text = 'ZSV2'.
  APPEND ls_value_set TO ls_order-type_set.
  ls_value_set-value = 'ZSV3'.
  ls_value_set-text = 'ZSV3'.
  APPEND ls_value_set TO ls_order-type_set.
  ls_value_set-value = 'ZSV4'.
  ls_value_set-text = 'ZSV4'.
  APPEND ls_value_set TO ls_order-type_set.
  APPEND ls_order TO lt_order.
  CLEAR:ls_order.
  ls_order-object_id = '0210002048'.
  ls_order-process_type = 'ZSO1'.
  ls_value_set-value = 'ZSO2'.
  ls_value_set-text = 'ZSO2'.
  APPEND ls_value_set TO ls_order-type_set.
  ls_value_set-value = 'ZSO3'.
  ls_value_set-text = 'ZSO3'.
  APPEND ls_value_set TO ls_order-type_set.
  ls_value_set-value = 'ZSO4'.
  ls_value_set-text = 'ZSO4'.
  APPEND ls_value_set TO ls_order-type_set.
  APPEND ls_order TO lt_order.
  lo_node = wd_context->get_child_node( name = wd_this->wdctx_order ).
  lo_node->bind_table( lt_order ).
  lo_node->set_lead_selection_index( -1 ).
* use ALV
  DATA:
    lr_salv_wd_table_usage TYPE REF TO if_wd_component_usage,
    lr_salv_wd_table       TYPE REF TO iwci_salv_wd_table,
    lr_table               TYPE REF TO cl_salv_wd_config_table,
    lr_column              TYPE REF TO cl_salv_wd_column,
    lr_dropdown            TYPE REF TO cl_salv_wd_uie_dropdown_by_idx.
  lr_salv_wd_table_usage = wd_this->wd_cpuse_alv( ).
  IF lr_salv_wd_table_usage->has_active_component( ) IS INITIAL.
    lr_salv_wd_table_usage->create_component( ).
  ENDIF.
* get reference to ALV component interface
  lr_salv_wd_table = wd_this->wd_cpifc_alv( ).
  lr_salv_wd_table->set_data( lo_node ).
* get ConfigurationModel from ALV Component
  lr_table = lr_salv_wd_table->get_model( ).
* get the column
  CALL METHOD lr_table->if_salv_wd_column_settings~get_column
    EXPORTING
      id    = 'PROCESS_TYPE'
    RECEIVING
      value = lr_column.
* create UI element
  CREATE OBJECT lr_dropdown
    EXPORTING
      selected_key_fieldname = 'PROCESS_TYPE'.
* set the value set
  CALL METHOD lr_dropdown->set_valueset_fieldname
    EXPORTING
      value = 'TYPE_SET'.
* set editor
  CALL METHOD lr_column->set_cell_editor
    EXPORTING
      value = lr_dropdown.
* 很重要 Dropdownlist 才会出现
  CALL METHOD lr_table->if_salv_wd_table_settings~set_read_only
    EXPORTING
      value = abap_false.
ENDMETHOD.
```

