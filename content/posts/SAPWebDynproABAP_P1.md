---
title: " Web Dynpro ABAP Program:ALV_VIEW-WDDOINIT "
date: 2018-10-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---

## ALV_VIEW: WDDOINIT

```JS
method WDDOINIT .
*1.数据定义
*=========================================================
 DATA:
    lo_alv_usage       TYPE REF TO if_wd_component_usage, "①重要的下面要用
    lo_if_controller   TYPE REF TO iwci_salv_wd_table, "②重要的下面要用
    lo_config          TYPE REF TO cl_salv_wd_config_table, "③重要的下面要用
    lo_column_settings TYPE REF TO if_salv_wd_column_settings,
    lo_salv_settings   TYPE REF TO if_salv_wd_table_settings,
    lo_column          TYPE REF TO cl_salv_wd_column,
    lo_function        TYPE REF TO cl_salv_wd_function,
    lo_button          TYPE REF TO cl_salv_wd_fe_button,
    lt_columns         TYPE salv_wd_t_column_ref,
    ls_column          TYPE salv_wd_s_column_ref,
    lo_col_header      TYPE REF TO cl_salv_wd_column_header.
"添加选择Check Box 
DATA: lo_checkbox      TYPE REF TO cl_salv_wd_uie_checkbox,
      lo_input_field   TYPE REF TO cl_salv_wd_uie_input_field.
 DATA lo_column_hdr    TYPE REF TO cl_salv_wd_column_header.

  DATA lo_nd_node TYPE REF TO if_wd_context_node.
  lo_nd_node = wd_context->get_child_node( name = wd_this->wdctx_ZALV_ZPEFF_TOTAL ).

" 通过向导引入控制器中的LT_STUDENT 节点和其对应的SET_DATA 方法，并将节点传入控制器
  DATA lo_interfacecontroller TYPE REF TO iwci_salv_wd_table .
  lo_interfacecontroller = wd_this->wd_cpifc_alv( ).
  lo_interfacecontroller->set_data( r_node_data = lo_nd_node ). "上面定义的节点参数

*2.实例化alv 组件
*=========================================================
" Instantiate the ALV Component 实例化ALV 组件
  lo_alv_usage = wd_this->wd_cpuse_alv( ). "①重要的将引用的ALV 组件实例（ALV_OUTPUT）复制给参数
  IF lo_alv_usage->has_active_component( ) IS INITIAL.
    lo_alv_usage->create_component( ).
  ENDIF.
" Get reference to model 获取参考模型
  lo_if_controller = wd_this->wd_cpifc_alv( ). "②重要的将引用的ALV 组件实例（ALV_OUTPUT）复制给参数
  lo_config = lo_if_controller->get_model( ). "③重要的获取控制器的GET_MODEL 方法，实现定制
" Set the UI elements.设置UI 元素
  lo_column_settings ?= lo_config.
  lt_columns = lo_column_settings->get_columns( ).
" Set colums not display in alv
  lo_column = lo_column_settings->get_column( 'BDAUT' ).
  lo_column->set_visible( cl_wd_uielement=>e_visible-none ).
  
*3.ALV Table 设置是否可以输入
*=========================================================
  lo_salv_settings ?= lo_config.
** set read_only
  lo_salv_settings->set_read_only( abap_false ).
" lo_salv_settings->set_read_only( abap_true ).

*4.ALV Table 设置是否可以导出
*=========================================================
  lo_config->if_salv_wd_std_functions~set_export_allowed( abap_true ) ."设置是否可以导出
  lo_config->if_salv_wd_std_functions~set_edit_append_row_allowed( abap_false ). "附加行
  lo_config->if_salv_wd_std_functions~set_edit_delete_row_allowed( abap_false ). "删除行
  lo_config->if_salv_wd_std_functions~set_edit_insert_row_allowed( abap_false ). "插入行
  lo_config->if_salv_wd_std_functions~set_pdf_allowed( abap_false ). "打印版本
  lo_config->if_salv_wd_std_functions~set_edit_check_available( abap_false ). "控制是否显示检查按钮

*5.ALV Table 可显示行设置
*=========================================================
  lo_config->if_salv_wd_table_settings~set_visible_row_count( '15' ). "显示的行数

*6.ALV Table 控制是否有默认的选择黄条，但是能点出黄条
*=========================================================
  lo_config->if_salv_wd_table_settings~set_selection_mode( cl_wd_table=>e_selection_mode-single_no_lead ).

*7.ALV Table 设置选择按钮和ALV hearder text 设置,ZSELE添加在Context中
  CREATE OBJECT lo_checkbox
    EXPORTING
      checked_fieldname = 'ZSELE'.
  lo_column_settings ?= lo_config.
  lo_column = lo_column_settings->get_column( 'ZSELE' ).
  CREATE OBJECT lo_input_field
    EXPORTING
      value_fieldname = 'ZSELE'.
  lo_column->set_cell_editor( lo_input_field ).
  lo_column->set_cell_editor( lo_checkbox ).
  lo_column_hdr = lo_column->create_header( ).
  lo_column_hdr->set_text( 'Selected' ).

endmethod.
```

