---
title: " Web Dynpro ABAP:ALV Image Light "
date: 2020-09-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro

---

### Context Component Controller

![Web Dynpro Context](/images/webdynproABAP/Portal34.png)

### WDDOINIT

```ABAP
METHOD INIT_DATA_NODE .
*... Create an instance of ALV component
  data: l_ref_cmp_usage type ref to if_wd_component_usage.
  l_ref_cmp_usage = wd_this->wd_cpuse_my_alv( ).
  if l_ref_cmp_usage->has_active_component( ) is initial.
    l_ref_cmp_usage->create_component( ).
  endif.
*... Get data node
  data: lr_node type ref to if_wd_context_node.
  lr_node = wd_context->get_child_node( 'TEST_DATA' ).
*... Invoke a method of the ALV Interfacecontroller
  data: l_ref_interfacecontroller type ref to iwci_salv_wd_table.
  l_ref_interfacecontroller = wd_this->wd_cpifc_my_alv( ).
*... Set the data
  l_ref_interfacecontroller->set_data(
    r_node_data = lr_node  " Ref to if_Wd_Context_Node "
  ).
*... Get model
  data: l_value type ref to cl_salv_wd_config_table.
  l_value = l_ref_interfacecontroller->get_model( ).
*... Hide column field2
  data: lr_column_settings type ref to if_salv_wd_column_settings.
  data: lr_column type ref to cl_salv_wd_column.
  lr_column_settings ?= l_value.
  lr_column = lr_column_settings->get_column( 'FIELD2' ).
  lr_column->set_visible( CL_WD_UIELEMENT=>E_VISIBLE-NONE ).
*... Define new cell editor image for column field1
  lr_column = lr_column_settings->get_column( 'FIELD1' ).
  data: lr_image type ref to cl_salv_wd_uie_image.
  create object lr_image.
  lr_image->set_source_fieldname( 'FIELD1' ).
  lr_column->set_cell_editor( lr_image ).
ENDMETHOD.
```

### DATALOAD

```ABAP
data: lt_stock type salv_wd_t_test,
      ls_stock type salv_wd_s_test.
   ls_stock-field1 = 'ICON_RED_LIGHT'.                      "#EC NOTEXT"
*  ls_stock-field1 = '{SALV_WD_LAYOUT_UI}/salv_wd_first_page.gif'.
*  ls_stock-field1 = '/SAP/BC/WebDynpro/SAP/WDR_TEST_FLUG_BUCHEN_SUCH/flug2.jpg'.     "#EC NOTEXT"
*  ls_stock-field1 = '/SAP/BC/WebDynpro/SAP/WDR_TEST_FLUG_BUCHEN_SUCH/doughnut.png'.  "#EC NOTEXT"
*  ls_stock-field1 = '/SAP/BC/WebDynpro/SAP/WDR_TEST_FLUG_BUCHEN_SUCH/Details.bmp'.   "#EC NOTEXT"
*  ls_stock-field1 = '/SAP/BC/WebDynpro/SAP/WDR_TEST_FLUG_BUCHEN_SUCH/mastercard.jpg'."#EC NOTEXT"
  ls_stock-field2 = 'Stock empty'.                          "#EC NOTEXT"
  ls_stock-field3 = 'Pullover'.                             "#EC NOTEXT"
  ls_stock-field4 = 'Regal1'.                               "#EC NOTEXT"
  ls_stock-num1   =  0.
  append ls_stock to lt_stock.

  ls_stock-field1 = 'ICON_YELLOW_LIGHT'.                    "#EC NOTEXT
*  ls_stock-field1 = '/SAP/BC/WebDynpro/SAP/WDR_TEST_FLUG_BUCHEN_SUCH/OrderLine.bmp'.  "#EC NOTEXT"
  ls_stock-field2 = 'Stock low'.                            "#EC NOTEXT"
  ls_stock-field3 = 'Hemden'.                               "#EC NOTEXT"
  ls_stock-field4 = 'Regal2'.                               "#EC NOTEXT"
  ls_stock-num1   =  5.
  append ls_stock to lt_stock.

  ls_stock-field1 = 'ICON_GREEN_LIGHT'.                     "#EC NOTEXT"
  ls_stock-field2 = 'Stock o.k.'.                           "#EC NOTEXT"
  ls_stock-field3 = 'Hosen'.                                "#EC NOTEXT"
  ls_stock-field4 = 'Regal3'.                               "#EC NOTEXT"
  ls_stock-num1   =  100.
  append ls_stock to lt_stock.

  ls_stock-field1 = '@0A@'.                                 "#EC NOTEXT"
  ls_stock-field2 = 'Stock o.k.'.                           "#EC NOTEXT"
  ls_stock-field3 = 'Hemden'.                               "#EC NOTEXT"
  ls_stock-field4 = 'Regal4'.                               "#EC NOTEXT"
  ls_stock-num1   =  100.
  append ls_stock to lt_stock.
*... Get data node
  data: lr_node type ref to if_wd_context_node.
  lr_node = wd_context->get_child_node( 'TEST_DATA' ).
  lr_node->bind_table(
    new_items = lt_stock
    set_initial_elements = abap_true ).
```

