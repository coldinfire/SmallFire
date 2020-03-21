---
title: " SALV Event处理 "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### Event 处理

```JS
CLASS lcl_event_handler DEFINITION.
  PUBLIC SECTION.
    METHODS:
      on_link_click FOR EVENT link_click OF cl_salv_events_table
        IMPORTING row column.
ENDCLASS.                    "lcl_event_handler DEFINITION

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
 METHOD generate_output.
    DATA: lx_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          IMPORTING
            r_salv_table = o_alv
          CHANGING
            t_table      = t_vbak ).
      CATCH cx_salv_msg INTO lx_msg.
    ENDTRY.
    
*   Get the event object
    DATA: lo_events TYPE REF TO cl_salv_events_table.
    lo_events = o_alv->get_event( ).
*   Instantiate the event handler object
    DATA: lo_event_handler TYPE REF TO lcl_event_handler.
    CREATE OBJECT lo_event_handler.
*   event handler
    SET HANDLER lo_event_handler->on_link_click FOR lo_events.
*   Displaying the ALV
    o_alv->display( ).
  ENDMETHOD.                    "generate_output
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
    
CLASS l_cl_handle_events IMPLEMENTATION.
  METHOD on_double_click.
    PERFORM show_cell_info USING row column 'is selected'.
  ENDMETHOD.
ENDCLASS.

FORM show_cell_info USING p_row TYPE i
                          p_column TYPE lvc_fname
                          p_text TYPE string.
  DATA: l_row TYPE char10.
  WRITE p_row TO l_row LEFT-JUSTIFIED.

  CONCATENATE l_row 'Line,' p_column 'Column' p_text INTO p_text SEPARATED BY space.
  MESSAGE i001(00) WITH p_text.

ENDFORM.
```
