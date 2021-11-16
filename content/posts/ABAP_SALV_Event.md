---
title: " SALV 事件处理 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### 标准事件处理

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
    DATA: lo_events TYPE REF TO cl_salv_events_table.
    lo_events = co_alv->get_event( ).
    "Instantiate the event handler object"
    DATA: lo_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lo_event_handler.
    "Event handler register"
    SET HANDLER lo_event_handler->on_link_click FOR lo_events.
    SET HANDLER lo_event_handler->on_double_click FOR lo_events.
  ENDMETHOD.                    "generate_output"
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*
    
FORM show_cell_info USING p_row TYPE i
                          p_column TYPE lvc_fname.
  DATA: lv_row TYPE char10,
        lv_text TYPE char50.
  WRITE p_row TO lv_row LEFT-JUSTIFIED.
  CONCATENATE lv_row 'Line,' p_column 'Column is selected.' INTO lv_text SEPARATED BY space.
  MESSAGE i001(00) WITH lv_text.
ENDFORM.
```

### 附加按钮事件

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
    DATA: lo_events TYPE REF TO cl_salv_events_table.
    lo_events = co_alv->get_event( ).
    "Instantiate the event handler object"
    DATA: lo_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lo_event_handler.
    "Event handler register"
    SET HANDLER lo_handle_event->on_user_command FOR lo_events.
  ENDMETHOD.                    "generate_output"
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*

FORM handle_user_command USING p_function TYPE salv_de_function.
  CASE p_function.
    WHEN 'REFRESH'.
      go_alv->refresh( ).
  ENDCASE.
ENDFORM.
```

