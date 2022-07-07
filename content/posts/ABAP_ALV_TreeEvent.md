---
title: " ALVTree Event "
date: 2019-06-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV

---

### 事件处理

#### 事件定义

```ABAP
*--------------------------------------------------------------*
*   INCLUDE BCALV_TREE_EVENT_RECEIVER                          *
*--------------------------------------------------------------*
CLASS lcl_tree_event_receiver DEFINITION.
  PUBLIC SECTION.
    METHODS handle_node_ctmenu_request
      FOR EVENT node_context_menu_request OF cl_gui_alv_tree
        IMPORTING node_key
                  menu.
    METHODS handle_node_ctmenu_selected
      FOR EVENT node_context_menu_selected OF cl_gui_alv_tree
        IMPORTING node_key
                  fcode.
    METHODS handle_item_ctmenu_request
      FOR EVENT item_context_menu_request OF cl_gui_alv_tree
        IMPORTING node_key
                  fieldname
                  menu.
    METHODS handle_item_ctmenu_selected
      FOR EVENT item_context_menu_selected OF cl_gui_alv_tree
        IMPORTING node_key
                  fieldname
                  fcode.
    METHODS handle_item_double_click
      FOR EVENT item_double_click OF cl_gui_alv_tree
      IMPORTING node_key
                fieldname.
    METHODS handle_button_click
      FOR EVENT button_click OF cl_gui_alv_tree
      IMPORTING node_key
                fieldname.
    METHODS handle_link_click
      FOR EVENT link_click OF cl_gui_alv_tree
      IMPORTING node_key
                fieldname.
    METHODS handle_header_click
      FOR EVENT header_click OF cl_gui_alv_tree
      IMPORTING fieldname.
ENDCLASS.
*---------------------------------------------------------------------*
*       CLASS lcl_tree_event_receiver IMPLEMENTATION
*---------------------------------------------------------------------*
CLASS lcl_tree_event_receiver IMPLEMENTATION.
  METHOD handle_node_ctmenu_request.
*   append own functions
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER1'
                          text    = 'Usercmd 1'.            "#EC NOTEXT
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER2'
                          text    = 'Usercmd 2'.            "#EC NOTEXT
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER3'
                          text    = 'Usercmd 3'.            "#EC NOTEXT
  ENDMETHOD.
  METHOD handle_node_ctmenu_selected.
    CASE fcode.
      WHEN 'USER1' OR 'USER2' OR 'USER3'.
        MESSAGE i000(0h) WITH 'Node-Context-Menu on Node ' node_key
                              'fcode : ' fcode.             "#EC NOTEXT
    ENDCASE.
  ENDMETHOD.
  METHOD handle_item_ctmenu_request .
*   append own functions
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER1'
                          text    = 'Usercmd 1'.
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER2'
                          text    = 'Usercmd 2'.
    CALL METHOD menu->add_function
                EXPORTING fcode   = 'USER3'
                          text    = 'Usercmd 3'.
  ENDMETHOD.
  METHOD handle_item_ctmenu_selected.
    CASE fcode.
      WHEN 'USER1' OR 'USER2' OR 'USER3'.
        MESSAGE i000(0h) WITH 'Item-Context-Menu on Node ' node_key
                              'Fieldname : ' fieldname.     "#EC NOTEXT
    ENDCASE.
  ENDMETHOD.
  METHOD handle_item_double_click.
* Processing for when user double clicks on ALVtree
  ENDMETHOD.
  METHOD handle_button_click.
* Processing when user clicks button
  ENDMETHOD.
  METHOD handle_link_click.
* ??
  ENDMETHOD.
  METHOD handle_header_click.
* Processing for when user clicks on ALVtree column headers
  ENDMETHOD.
ENDCLASS.
```

#### 注册事件

```ABAP
*&-------------------------------------------------------------*
*&      REGISTER_EVENTS
*&-------------------------------------------------------------*
* define the events which will be passed to the backend
  data: lt_events type cntl_simple_events,
        l_event type cntl_simple_event.
* define the events which will be passed to the backend
  l_event-eventid = cl_gui_column_tree=>eventid_expand_no_children.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_checkbox_change.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_header_context_men_req.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_node_context_menu_req.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_item_context_menu_req.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_item_double_click.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_header_click.
  append l_event to lt_events.
  l_event-eventid = cl_gui_column_tree=>eventid_item_keypress.
  append l_event to lt_events.
  call method gd_tree->set_registered_events
    exporting
      events = lt_events
    exceptions
      cntl_error                = 1
      cntl_system_error         = 2
      illegal_event_combination = 3.
  if sy-subrc <> 0.
    message x208(00) with 'ERROR'.                       "#EC NOTEXT
  endif.
* set Handler
  data: l_event_receiver type ref to lcl_tree_event_receiver.
  create object l_event_receiver.
  set handler l_event_receiver->handle_node_ctmenu_request for gd_tree.
  set handler l_event_receiver->handle_node_ctmenu_selected for gd_tree.
  set handler l_event_receiver->handle_item_ctmenu_request for gd_tree.
  set handler l_event_receiver->handle_item_ctmenu_selected for gd_tree.
  set handler l_event_receiver->handle_item_double_click for gd_tree.
  set handler l_event_receiver->handle_header_click for gd_tree.
```

### ALVtree 工具栏处理

`INCLUDE ZTEST_TOOLBAR_EVENT_RECEIVER.` ：Toobar Process

定义类来处理用户定义的 ALVtree 工具栏按钮。 

```ABAP
*--------------------------------------------------------------*
*   INCLUDE ZTEST_TOOLBAR_EVENT_RECEIVER                       *
*--------------------------------------------------------------*
data mr_toolbar type ref to cl_gui_toolbar.  "Add to top include
CLASS lcl_toolbar_event_receiver DEFINITION.
  PUBLIC SECTION.
    METHODS: on_function_selected
               FOR EVENT function_selected OF cl_gui_toolbar
                 IMPORTING fcode,
             on_toolbar_dropdown
               FOR EVENT dropdown_clicked OF cl_gui_toolbar
                 IMPORTING fcode
                           posx
                           posy.
ENDCLASS.
*-------------------------------------------------------------*
*       CLASS lcl_toolbar_event_receiver IMPLEMENTATION
*-------------------------------------------------------------*
CLASS lcl_toolbar_event_receiver IMPLEMENTATION.
  METHOD on_function_selected.
    DATA: ls_sflight TYPE sflight.
    DATA: lt_list_commentary TYPE slis_t_listheader,
          l_logo             TYPE sdydo_value.
*   Processing for user defined tollbar button goes here
    CASE fcode.
      WHEN 'NEXT'.
      WHEN 'DELETE'.
        DATA: lt_selected_node TYPE lvc_t_nkey.
        DATA: e_selected_node     TYPE lvc_nkey,
              e_fieldname         TYPE lvc_fname.
        DATA: lt_outtab_line(100)  TYPE c,
              lt_node_text         TYPE lvc_value.
        CALL METHOD gd_tree->get_selected_nodes
                CHANGING
                   ct_selected_nodes = lt_selected_node.
      WHEN 'INSERT_LC'.
*     Code for function code INSERT_LC goes here
      WHEN 'INSERT_FC'.
*     Code for function code INSERT_FC goes here
      WHEN 'INSERT_FS'.
*     Code for function code INSERT_FS goes here
      WHEN 'INSERT_LS'.
*     Code for function code INSERT_LS goes here
      WHEN 'INSERT_NS'.
*     Code for function code INSERT_NS goes here
    ENDCASE.
*   update frontend
    CALL METHOD gd_tree->frontend_update.
  ENDMETHOD.
  METHOD on_toolbar_dropdown.
* create contextmenu
    DATA: l_menu TYPE REF TO cl_ctmenu,
          l_fc_handled TYPE as4flag.
    CREATE OBJECT l_menu.
    CLEAR l_fc_handled.
*   Setup Insert button so options are displayed as drop doan menu
    CASE fcode.
      WHEN 'INSERT_LC'.
        l_fc_handled = 'X'.
*       insert as last child
        CALL METHOD l_menu->add_function
                EXPORTING fcode   = 'INSERT_LC'
                text    = 'Insert New Line as Last Child'.  "#EC NOTEXT
*       insert as first child
        CALL METHOD l_menu->add_function
                EXPORTING fcode   = 'INSERT_FC'
                text    = 'Insert New Line as First Child'. "#EC NOTEXT
*       insert as next sibling
        CALL METHOD l_menu->add_function
                EXPORTING fcode   = 'INSERT_NS'
                text    = 'Insert New Line as Next Sibling'."#EC NOTEXT
*       insert as last sibling
        CALL METHOD l_menu->add_function
                EXPORTING fcode   = 'INSERT_LS'
                text    = 'Insert New Line as Last Sibling'."#EC NOTEXT
*       insert as first sibling
        CALL METHOD l_menu->add_function
                EXPORTING fcode   = 'INSERT_FS'
              text    = 'Insert New Line as First Sibling'. "#EC NOTEXT
    ENDCASE.
* show dropdownbox
    IF l_fc_handled = 'X'.
      CALL METHOD mr_toolbar->track_context_menu
        EXPORTING
            context_menu = l_menu
            posx         = posx
            posy         = posy.
    ENDIF.
  ENDMETHOD.
ENDCLASS.

```

#### CHANGE_TOOLBAR

在 `CALL METHOD gd_tree->set_table_for_first_display` 之后执行。

```ABAP
*&-------------------------------------------------------------*
*&      CHANGE_TOOLBAR
*&-------------------------------------------------------------*
* get toolbar control
  call method gd_tree->get_toolbar_object
          importing
              er_toolbar = mr_toolbar.
  check not mr_toolbar is initial.
* add seperator to toolbar
  call method mr_toolbar->add_button
          exporting
              fcode     = ''
              icon      = ''
              butn_type = cntb_btype_sep
              text      = ''
              quickinfo = 'This is a Seperator'.         "#EC NOTEXT
* add Standard Button to toolbar (for Delete Subtree)
  call method mr_toolbar->add_button
          exporting
              fcode     = 'DELETE'           "Function code of button
              icon      = '@18@'             "Icon ID (see )
              butn_type = cntb_btype_button  "Button type
              text      = ''                 "Button text
              quickinfo = 'Delete subtree'.  "Quick info text
* add Dropdown Button to toolbar (for Insert Line)
  call method mr_toolbar->add_button
          exporting
              fcode     = 'INSERT_LC'         "Function code of button
              icon      = '@17@'              "Icon ID (see )
              butn_type = cntb_btype_dropdown "Button type
              text      = ''                  "Button text
              quickinfo = 'Insert Line'.      "Quick info text
* set event-handler for toolbar-control
  data: toolbar_event_receiver type ref to lcl_toolbar_event_receiver.
  create object toolbar_event_receiver.
  set handler toolbar_event_receiver->on_function_selected for mr_toolbar.
  set handler toolbar_event_receiver->on_toolbar_dropdown for mr_toolbar.
```