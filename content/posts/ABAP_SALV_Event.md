---
title: " SALV 事件处理 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - SALV
 
---

### 标准事件处理

![CL_SALV_EVENTS_TABLE](/images/ABAP/SALV12.png)

```ABAP
CLASS lcl_event_handler DEFINITION.
  PUBLIC SECTION.
    METHODS: on_link_click FOR EVENT link_click OF cl_salv_events_table
        IMPORTING 
          row     "事件触发所在的行号"
          column. "事件触发所在的列名"
    METHODS: on_double_click FOR EVENT double_click OF cl_salv_events_table
        IMPORTING row column.
ENDCLASS.                    "lcl_event_handler DEFINITION"
CLASS lcl_event_handler IMPLEMENTATION.
  METHOD on_link_click.
    PERFORM show_cell_info USING row column.
  ENDMETHOD.
  METHOD on_double_click.
    PERFORM show_cell_info USING row column.
  ENDMETHOD.
ENDCLASS.                     "lcl_event_handler IMPLEMENTATION"

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
 METHOD generate_output.
    "Get the event object"
    DATA: lr_events TYPE REF TO cl_salv_events_table.
    lr_events = gr_table->get_event( ).
    "Instantiate the event handler object"
    DATA: lr_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lr_event_handler.
    "Event handler register"
    SET HANDLER lr_event_handler->on_link_click FOR lr_events.
    SET HANDLER lr_event_handler->on_double_click FOR lr_events.
  ENDMETHOD.                    "generate_output"
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*
    
FORM show_cell_info USING p_row TYPE i
                          p_column TYPE lvc_fname.
  DATA: ls_vbak TYPE str_vbak.
  READ TABLE me->gt_vbak INTO ls_vbak INDEX p_row.
    IF ls_vbak-vbeln IS NOT INITIAL.
      MESSAGE i398(00) WITH 'You have selected' ls_vbak-vbeln.
    ENDIF.
ENDFORM.
```

### 自定义按钮事件

在 GUI Status 加入自定义按钮后，可以通过注册事件 added_function，并且在对应的 handler method 中写入相关逻辑，来实现点击按钮后的逻辑。

```ABAP
CLASS lcl_event_handler DEFINITION.
  PUBLIC SECTION.
    METHODS: on_user_command FOR EVENT added_function OF cl_salv_events_table
        IMPORTING e_salv_function.
ENDCLASS.                    "lcl_event_handler DEFINITION"
CLASS lcl_event_handler IMPLEMENTATION.
  METHOD on_user_command.
    PERFORM handle_user_command USING e_salv_function.
  ENDMETHOD.
ENDCLASS.                     "lcl_event_handler IMPLEMENTATION"

*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
 METHOD generate_output.
    "Get the event object"
    DATA: lr_events TYPE REF TO cl_salv_events_table.
    lr_events = co_alv->get_event( ).
    "Instantiate the event handler object"
    DATA: lr_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lr_event_handler.
    "Event handler register"
    SET HANDLER lr_event_handler->on_user_command FOR lr_events.
  ENDMETHOD.                    "generate_output"
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

FORM handle_user_command USING p_function TYPE salv_de_function.
  CASE p_function.
    WHEN 'REFRESH'.
      gr_table->refresh( ).
  ENDCASE.
ENDFORM.
```

