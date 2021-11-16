---
title: " SALV 页头和页尾设置 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 添加页头(Top of page)和页脚(End of page)

```ABAP
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
  PRIVATE SECTION.
    "Set Top of page"
    METHODS: set_top_of_page
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
    "Set End of page"
    METHODS: set_end_of_page
        CHANGING
          co_alv TYPE REF TO cl_salv_table.
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
    CALL METHOD me->set_top_of_page
      CHANGING
        co_alv = go_alv.
    CALL METHOD me->set_end_of_page
      CHANGING
        co_alv = go_alv.
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
  METHOD set_top_of_page.
    DATA: lo_header  TYPE REF TO cl_salv_form_layout_grid,
          lo_h_label TYPE REF TO cl_salv_form_label,
          lo_h_flow  TYPE REF TO cl_salv_form_layout_flow.
    "Header object"
    CREATE OBJECT lo_header.
    "To create a Lable or Flow we have to specify the target row and column number"
    "where we need to set up the output text."
    "Information in Bold"
    lo_h_label = lo_header->create_label( row = 1 column = 1 ).
    lo_h_label->set_text( 'Header in Bold' ).
    "Information in tabular format"
    lo_h_flow = lo_header->create_flow( row = 2  column = 1 ).
    lo_h_flow->create_text( text = 'This is text of flow' ).
    lo_h_flow = lo_header->create_flow( row = 3  column = 1 ).
    lo_h_flow->create_text( text = 'Number of Records in the output' ).
    lo_h_flow = lo_header->create_flow( row = 3  column = 2 ).
    lo_h_flow->create_text( text = 20 ).
    "Set the top of list using the header for Online."
    co_alv->set_top_of_list( lo_header ).
    "Set the top of list using the header for Print."
    co_alv->set_top_of_list_print( lo_header ).
  ENDMETHOD.                    "set_top_of_page"

  METHOD set_end_of_page.
    DATA: lo_footer  TYPE REF TO cl_salv_form_layout_grid,
          lo_f_label TYPE REF TO cl_salv_form_label,
          lo_f_flow  TYPE REF TO cl_salv_form_layout_flow.
    "Footer object"
    CREATE OBJECT lo_footer.
    "Information in bold"
    lo_f_label = lo_footer->create_label( row = 1 column = 1 ).
    lo_f_label->set_text( 'Footer .. here it goes' ).
    "Tabular information"
    lo_f_flow = lo_footer->create_flow( row = 2  column = 1 ).
    lo_f_flow->create_text( text = 'This is text of flow in footer' ).
    lo_f_flow = lo_footer->create_flow( row = 3  column = 1 ).
    lo_f_flow->create_text( text = 'Footer number' ).
    lo_f_flow = lo_footer->create_flow( row = 3  column = 2 ).
    lo_f_flow->create_text( text = 1 ).
    "Online footer"
    co_alv->set_end_of_list( lo_footer ).
    "Footer in print"
    co_alv->set_end_of_list_print( lo_footer ).
  ENDMETHOD.                    "set_end_of_page"
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
```

