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
METHOD WDDOINIT .
*1.数据定义
*=========================================================
 DATA: alv_usage       TYPE REF TO if_wd_component_usage,  "①"
       alv_controller  TYPE REF TO iwci_salv_wd_table,     "②"
       alv_config      TYPE REF TO cl_salv_wd_config_table."③"
 DATA: column_settings TYPE REF TO if_salv_wd_column_settings,
       table_settings  TYPE REF TO if_salv_wd_table_settings,
       column_nd       TYPE REF TO cl_salv_wd_column,
       column_header   TYPE REF TO cl_salv_wd_column_header,
       function        TYPE REF TO cl_salv_wd_function,
       button          TYPE REF TO cl_salv_wd_fe_button.
 DATA: columns  TYPE salv_wd_t_column_ref,
       column   TYPE salv_wd_s_column_ref,
       fields   TYPE TABLE OF dfies,
       field    TYPE dfies.
" Add Check Box "
 DATA:checkbox      TYPE REF TO cl_salv_wd_uie_checkbox,
      input_field   TYPE REF TO cl_salv_wd_uie_input_field.
 " Get Context note "
 DATA context_node TYPE REF TO if_wd_context_node.
 context_node = wd_context->get_child_node( name = wd_this->wdctx_ZALV_ZPEFF_TOTAL ).
*2.实例化alv 组件
*=========================================================
" 实例化ALV 组件 "
  alv_usage = wd_this->wd_cpuse_alv( ). "① 将引用的ALV组件实例(ALV)复制给参数"
  IF alv_usage->has_active_component( ) IS INITIAL.
    alv_usage->create_component( ).
  ENDIF.
" 通过向导引入控制器中的Context节点和其对应的SET_DATA方法，并将节点传入控制器 "
  alv_controller = wd_this->wd_cpifc_alv( ). "② 将引用的ALV组件实例（ALV_OUTPUT）复制给参数"
  alv_controller->set_data( r_node_data = lo_nd_node ). "上面定义的节点参数"  
" 获取参考模型 "
  alv_config = alv_controller->get_model( ). "③ 获取控制器的GET_MODEL方法，实现ALV定制"
  column_settings ?= alv_config.
  table_settings  ?= alv_config.
*3.ALV Table Standard Functions Setting
*=========================================================
  alv_config->if_salv_wd_std_functions~set_export_allowed( abap_true ) ."设置是否可以导出"
  alv_config->if_salv_wd_std_functions~set_edit_append_row_allowed( abap_false ). "附加行"
  alv_config->if_salv_wd_std_functions~set_edit_delete_row_allowed( abap_false ). "删除行"
  alv_config->if_salv_wd_std_functions~set_edit_insert_row_allowed( abap_false ). "插入行"
  alv_config->if_salv_wd_std_functions~set_pdf_allowed( abap_false ).             "打印版本"
  alv_config->if_salv_wd_std_functions~set_count_records_allowed( abap_true ). "Count Records"
  alv_config->if_salv_wd_std_functions~set_edit_check_available( abap_false ). "控制是否显示检查按钮"
*4.ALV Table 设置
*=========================================================
  table_settings->SET_READ_ONLY( ABAP_FALSE )."设置ALV整体不可编辑"
  table_settings->SET_VISIBLE_ROW_COUNT( '50' )."设置可见行"
  table_settings->SET_FIXED_TABLE_LAYOUT( ABAP_FALSE ).  "使列宽可自动调节"
  table_settings->SET_ROW_SELECTABLE( ABAP_TRUE )."设置行选择"
  table_settings->SET_SCROLLABLE_COL_COUNT( '10' )."设置滚动条"
  " 控制是否有默认的选择黄条 "
  table_settings->SET_SELECTION_MODE( cl_wd_table=>e_selection_mode-single_no_lead ).
  table_settings->SET_WIDTH( '80%' )."设置ALV宽度"
  table_settings->SET_EDIT_MODE( IF_SALV_WD_C_TABLE_SETTINGS=>EDIT_MODE ).  "设置编辑模式"
  table_settings->SET_EDIT_MODE( IF_SALV_WD_C_TABLE_SETTINGS=>EDIT_MODE_STANDARD )."设置不可编辑模式"
  table_settings->SET_ENABLED( ABAP_TRUE ) ."可处理的"
  table_settings->SET_EMPTY_TABLE_TEXT( 'Empty' ) ."设置空表时显示的文本"
  table_settings->SET_DISPLAY_EMPTY_ROWS( ABAP_FALSE ).  "不展示空表"
*5.ALV Columns 设置
*=========================================================
  columns = column_settings->get_columns(  ).
  LOOP AT columns INTO column. 
    column_nd = column-r_column.
    CASE column-id.
      WHEN 'WERKS'.
        column_nd->set_position( 0 ).
        column_nd->SET_RESIZABLE( abap_true ).
        column_header = column_nd->get_header(  ) .
        column_header->set_ddic_binding_field(  ).
        column_header->set_text('Plant').
      WHEN 'MEINS'.
        column_settings->delete_column( column-id ).
      WHEN 'OTHERS'.
        " Setting Color field:WDUI_TABLE_CELL_DESIGN (Table Cell Design) "
        column_nd->set_cell_design_fieldname( 'COLOR' ). 
    ENDCASE.
  ENDLOOP.
  column_settings->delete_column( 'COLOR' ). "Delete column"
  column_nd = column_settings->get_column( 'BDAUT' ). "Get column"
  column_nd->set_visible( cl_wd_uielement=>e_visible-none ). " Set colums not display in alv "
*6.ALV Table设置选择按钮和ALV hearder text 设置,ZSELE添加在Context中
*==========================================================
  CREATE OBJECT checkbox
    EXPORTING
      checked_fieldname = 'ZSELE'.
  column_nd = column_settings->get_column( 'ZSELE' ).
  CREATE OBJECT input_field
    EXPORTING
      value_fieldname = 'ZSELE'.
  column_nd->set_cell_editor( input_field ).
  column_nd->set_cell_editor( checkbox ).
  column_header = column_nd->create_header( ).
  column_header->set_text( 'Selected' ).
endmethod.
```

