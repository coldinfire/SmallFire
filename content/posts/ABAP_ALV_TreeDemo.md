---
title: " ALVTree Demo "
date: 2019-06-19
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
  - DEMO

---

### 创建一个完整的 ALVTree Control SAP dynpro 程序

 生成的 ALVTree Report 使用 OBJECT METHOD 功能来生成树结构的 ALV 输出。 创建 ALVtree Report 首先需要创建一个简单的程序来构建 ALV 详细信息(例如字段目录)，并调用将用于显示 ALVTree 的屏幕。 使用 "custom control" 创建屏幕（SCREEN_CONTAINER），然后在其中显示 ALVtree Report。 

#### 创建主程序代码

```ABAP
*&-------------------------------------------------------------*
*& Example of a simple ALV Grid Report                         *
*& ...................................                         *
*& The basic requirement for this demo is to display a number  *
*& of fields from the EKPO and EKKO table in a tree structure. *
*&-------------------------------------------------------------*
REPORT  ZDEMO_ALVTREE.
*Data Declaration
TABLES:ekko.
TYPE-POOLS: slis.
INCLUDE <icon>.
"ALV Declarations"
TYPES: BEGIN OF t_ekko,
  ebeln TYPE ekpo-ebeln,
  ebelp TYPE ekpo-ebelp,
  statu TYPE ekpo-statu,
  aedat TYPE ekpo-aedat,
  matnr TYPE ekpo-matnr,
  menge TYPE ekpo-menge,
  meins TYPE ekpo-meins,
  netpr TYPE ekpo-netpr,
  peinh TYPE ekpo-peinh,
END OF t_ekko.
DATA: it_ekko     TYPE STANDARD TABLE OF t_ekko INITIAL SIZE 0,
      it_ekpo     TYPE STANDARD TABLE OF t_ekko INITIAL SIZE 0,
      it_emptytab TYPE STANDARD TABLE OF t_ekko INITIAL SIZE 0,
      wa_ekko     TYPE t_ekko,
      wa_ekpo     TYPE t_ekko.
DATA: ok_code LIKE sy-ucomm,           "OK-Code"
      save_ok LIKE sy-ucomm.
*ALV data declarations
DATA: fieldcatalog  TYPE lvc_t_fcat WITH HEADER LINE.
DATA: gd_fieldcat   TYPE lvc_t_fcat,
      gd_tab_group  TYPE slis_t_sp_group_alv,
      gd_layout     TYPE slis_layout_alv.
*ALVtree data declarations
CLASS cl_gui_column_tree DEFINITION LOAD.
CLASS cl_gui_cfw DEFINITION LOAD.
DATA: gd_tree             TYPE REF TO cl_gui_alv_tree,
      gd_hierarchy_header TYPE treev_hhdr,
      gd_report_title     TYPE slis_t_listheader,
      gd_logo             TYPE sdydo_value,
      gd_variant          TYPE disvariant.
*Create container for alv-tree
DATA: gd_tree_container_name(30) TYPE c,
      gd_custom_container        TYPE REF TO cl_gui_custom_container.

DEFINE frm_fieldcat.
  CLEAR: &1.
  &1-fieldname = &2 .
  &1-scrtext_m = &3 .
  &1-outputlen = &4 .
  &1-col_pos   = &5 .
  APPEND &1.
END-OF-DEFINITION.
************************************************************************
*Includes
INCLUDE ZDEMO_ALVTREEO01. "Screen PBO Modules"
INCLUDE ZDEMO_ALVTREEI01. "Screen PAI Modules"
INCLUDE ZDEMO_ALVTREEF01. "ABAP Subroutines(FORMS)"
INCLUDE ZTEST_TOOLBAR_EVENT_RECEIVER. "Toobar Process"
INCLUDE 
************************************************************************
START-OF-SELECTION.
* ALVtree setup data
  PERFORM data_retrieval.
  PERFORM build_fieldcatalog.
  PERFORM build_layout.
  PERFORM build_report_title USING gd_report_title gd_logo.
  PERFORM build_variant.
* Display ALVtree report
  CALL SCREEN 100.
*&---------------------------------------------------------------------*
*&      Form  DATA_RETRIEVAL
*&---------------------------------------------------------------------*
*       Retrieve data into Internal tables
*----------------------------------------------------------------------*
FORM data_retrieval.
  SELECT ebeln UP TO 10 ROWS
   FROM ekko
   INTO CORRESPONDING FIELDS OF TABLE it_ekko.
  LOOP AT it_ekko INTO wa_ekko.
    SELECT ebeln ebelp statu aedat matnr menge meins netpr peinh
     FROM ekpo
     APPENDING TABLE it_ekpo
     WHERE ebeln EQ wa_ekko-ebeln.
  ENDLOOP.
ENDFORM.                    " DATA_RETRIEVAL "
*&---------------------------------------------------------------------*
*&      Form  BUILD_FIELDCATALOG
*&---------------------------------------------------------------------*
*       Build Fieldcatalog for ALV Report
*----------------------------------------------------------------------*
FORM build_fieldcatalog.
* Please notice there are a number of differences between the structure of
* ALVtree fieldcatalogs and ALVgrid fieldcatalogs.
* For example the field seltext_m is replace by scrtext_m in ALVtree.
  frm_fieldcat:
    fieldcatalog 'EBELN' 'Purchase Order'     0 15,
    fieldcatalog 'EBELP' 'PO Item'            1 15,
    fieldcatalog 'STATU' 'Status'             2 15,
    fieldcatalog 'AEDAT' 'Item change date'   3 15,
    fieldcatalog 'MATNR' 'Material Number'    4 15,
    fieldcatalog 'MENGE' 'PO quantity'        5 15,
    fieldcatalog 'MEINS' 'Order Unit'         6 15,
    fieldcatalog 'NETPR' 'Net Price'          7 15,
    fieldcatalog 'PEINH' 'Price Unit'         8 15.

  LOOP AT fieldcatalog.
    IF fieldcatalog-fieldname EQ 'NETPR'.
      fieldcatalog-datatype = 'CURR'.
    ENDIF.
    MODIFY fieldcatalog.
    CLEAR fieldcatalog.
  ENDLOOP.

  gd_fieldcat[] = fieldcatalog[].

ENDFORM.                    " BUILD_FIELDCATALOG "
*&---------------------------------------------------------------------*
*&      Form  BUILD_LAYOUT
*&---------------------------------------------------------------------*
*       Build layout for ALV grid report
*----------------------------------------------------------------------*
FORM build_layout.
  gd_layout-no_input          = 'X'.
  gd_layout-colwidth_optimize = 'X'.
  gd_layout-totals_text       = 'Totals'(201).
*  gd_layout-totals_only        = 'X'.
*  gd_layout-f2code            = 'DISP'.  "Sets fcode for when double
*                                         "click(press f2)
*  gd_layout-zebra             = 'X'.
*  gd_layout-group_change_edit = 'X'.
*  gd_layout-header_text       = 'helllllo'.
ENDFORM.                    " BUILD_LAYOUT "
*&---------------------------------------------------------------------*
*&      Form  BUILD_REPORT_TITLE
*&---------------------------------------------------------------------*
*       Build table for ALVtree header
*----------------------------------------------------------------------*
*  <->  p1        Header details
*  <->  p2        Logo value
*----------------------------------------------------------------------*
FORM build_report_title CHANGING
  pt_report_title  TYPE slis_t_listheader
  pa_logo             TYPE sdydo_value.
  DATA: ls_line TYPE slis_listheader,
        ld_date(10) TYPE c.
* List Heading Line(TYPE H)
  CLEAR ls_line.
  ls_line-typ  = 'H'.
* ls_line-key     "Not Used For This Type(H)"
  ls_line-info = 'PO ALVTree Display'.
  APPEND ls_line TO pt_report_title.
* Status Line(TYPE S)
  ld_date(2) = sy-datum+6(2).
  ld_date+2(1) = '/'.
  ld_date+3(2) = sy-datum+4(2).
  ld_date+5(1) = '/'.
  ld_date+6(4) = sy-datum(4).
  ls_line-typ  = 'S'.
  ls_line-key  = 'Date'.
  ls_line-info = ld_date.
  APPEND ls_line TO pt_report_title.
* Action Line(TYPE A)
  CLEAR ls_line.
  ls_line-typ  = 'A'.
  CONCATENATE 'Report: ' sy-repid INTO ls_line-info SEPARATED BY space.
  APPEND ls_line TO pt_report_title.
ENDFORM.                    " build_report_title "
*&---------------------------------------------------------------------*
*&      Form  BUILD_VARIANT
*&---------------------------------------------------------------------*
*       Build variant
*----------------------------------------------------------------------*
FORM build_variant.
* Set repid for storing variants
  gd_variant-report = sy-repid.
ENDFORM.                    " BUILD_VARIANT "
```

#### 屏幕设置

创建屏幕并设置PBO、PAI；定义 OK_CODE 变量；设置 pf-status；创建 Custom control。

![Custom control](/images/ABAP/ALV_TreeDemo1.png)

#### ALVTree 代码

为了存储 ALVTree Report 所需的 ABAP 代码，需要创建三个 include：一个用于 PBO 模块，一个用于 PAI 模块，一个用于子例程（FORM）。

`INCLUDE ZDEMO_ALVTREEO01.`  ：Screen PBO Modules

```ABAP
*&---------------------------------------------------------------------*
*&  Include           ZDEMO_ALVTREEO01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Module  STATUS_0100  OUTPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'STATUS1'.
*  SET TITLEBAR 'xxx'.
* If ALVtree already exists then it mush not be re-created as this will cause a runtime error.
* Create ALVtree (must be performed within screen PBO module)
  IF gd_tree IS INITIAL.
*§1. Create container for alv-tree
    PERFORM create_alvtree_container.
*§2. Create tree control
    PERFORM create_object_in_container.
*§3. Create Hierarchy-header
    PERFORM build_hierarchy_header CHANGING gd_hierarchy_heade.
*§4. Create empty alvtree Control
    PERFORM create_empty_alvtree_control.
*§5. Create ALVtree Hierarchy
    PERFORM create_alvtree_hierarchy.
*§6. Send data to frontend.
  "this method must be called to send the data to the frontend"
  CALL METHOD gd_tree->frontend_update.
*§7. Flush.
  CALL METHOD cl_gui_cfw=>flush
    EXCEPTIONS
      cntl_system_error = 1
      cntl_error        = 2.
  IF sy-subrc NE 0.
    CALL FUNCTION 'POPUP_TO_INFORM'
      EXPORTING
        titel = 'Automation Queue failure'(801)
        txt1  = 'Internal error:'(802)
        txt2  = 'A method in the automation queue'(803)
        txt3  = 'caused a failure.'(804).
  ENDIF.
ENDMODULE.                 " STATUS_0100  OUTPUT "
```

`INCLUDE ZDEMO_ALVTREEI01.`   ：Screen PAI Modules

```ABAP
*&---------------------------------------------------------------------*
*&  Include           ZDEMO_ALVTREEI01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Module  USER_COMMAND_0100  INPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE user_command_0100 INPUT.
  DATA return TYPE REF TO cl_gui_event.
  save_ok = ok_code.
  CASE ok_code.
    WHEN 'BACK' OR '%EX' OR 'RW'.
      gd_custom_container->free( ).
      LEAVE TO SCREEN 0.
*   Process ALVtree user actions
    WHEN OTHERS.
      CALL METHOD cl_gui_cfw=>get_current_event_object
        RECEIVING
          event_object = return.
      CALL METHOD cl_gui_cfw=>dispatch.
  ENDCASE.
ENDMODULE.                 " USER_COMMAND_0100  INPUT "
```

`INCLUDE ZDEMO_ALVTREEF01.`  ：ABAP Subroutines(FORMS) 

```ABAP
*&---------------------------------------------------------------------*
*&  Include           ZDEMO_ALVTREEF01
*&---------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&      Form  CREATE_ALVTREE_CONTAINER
*&---------------------------------------------------------------------*
*        Create container for alv-tree
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM create_alvtree_container .
* Create container for alv-tree
  gd_tree_container_name = 'SCREEN_CONTAINER'.
  CREATE OBJECT gd_custom_container
    EXPORTING
      container_name              = gd_tree_container_name
    EXCEPTIONS
      cntl_error                  = 1
      cntl_system_error           = 2
      create_error                = 3
      lifetime_error              = 4
      lifetime_dynpro_dynpro_link = 5.
  IF sy-subrc <> 0.
    MESSAGE x208(00) WITH 'ERROR'.
  ENDIF.
ENDFORM.                    " CREATE_ALVTREE_CONTAINER "
*&---------------------------------------------------------------------*
*&      Form  CREATE_OBJECT_IN_CONTAINER
*&---------------------------------------------------------------------*
*       Create ALVtree control
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM create_object_in_container .
* Create tree control
  CREATE OBJECT gd_tree
    EXPORTING
      parent                      = gd_custom_container
      node_selection_mode         = cl_gui_column_tree=>node_sel_mode_single
      item_selection              = 'X'
      no_html_header              = 'X'
      no_toolbar                  = ''
    EXCEPTIONS
      cntl_error                  = 1
      cntl_system_error           = 2
      create_error                = 3
      lifetime_error              = 4
      illegal_node_selection_mode = 5
      failed                      = 6
      illegal_column_name         = 7.
  IF sy-subrc <> 0.
    MESSAGE x208(00) WITH 'ERROR'.
  ENDIF.
ENDFORM.                    " CREATE_OBJECT_IN_CONTAINER "
*&---------------------------------------------------------------------*
*&      Form  build_hierarchy_header
*&---------------------------------------------------------------------*
*       build hierarchy-header-information
*----------------------------------------------------------------------*
*      -->P_L_HIERARCHY_HEADER  strucxture for hierarchy-header
*----------------------------------------------------------------------*
FORM build_hierarchy_header CHANGING p_hierarchy_header type treev_hhdr.
  p_hierarchy_header-heading = 'Month/Carrier/Date'(300).
  p_hierarchy_header-tooltip = 'Flights in a month'(400).
  p_hierarchy_header-width = 30.
  p_hierarchy_header-width_pix = ' '.
endform.                               " build_hierarchy_header "
*&---------------------------------------------------------------------*
*&      Form  CREATE_EMPTY_ALVTREE_CONTROL
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM create_empty_alvtree_control .
* Create emty tree-control
  CLEAR: it_emptytab.
  REFRESH: it_emptytab.
  CALL METHOD gd_tree->set_table_for_first_display
    EXPORTING
      is_hierarchy_header  = gd_hierarchy_header
      it_list_commentary   = gd_report_title
      i_logo               = gd_logo
*     i_background_id      = 'ALV_BACKGROUND'
      i_save               = 'A'
      is_variant            = gd_variant
    CHANGING
      it_outtab            =  it_emptytab      "Must be empty"
      it_fieldcatalog      =  gd_fieldcat.
ENDFORM.                    " CREATE_EMPTY_ALVTREE_CONTROL"
*&---------------------------------------------------------------------*
*&      Form  CREATE_ALVTREE_HIERARCHY
*&---------------------------------------------------------------------*
*       Builds ALV tree display, (inserts nodes, subnodes etc)
*----------------------------------------------------------------------*
*  -->  p1        text
*  <--  p2        text
*----------------------------------------------------------------------*
FORM create_alvtree_hierarchy .
  DATA: ls_sflight TYPE sflight,
        lt_sflight TYPE sflight OCCURS 0.
  DATA: ld_ebeln_key TYPE lvc_nkey,
        ld_ebelp_key TYPE lvc_nkey.
  LOOP AT it_ekko INTO wa_ekko.
    PERFORM add_ekko_node USING wa_ekko '' CHANGING ld_ebeln_key.
    LOOP AT it_ekpo INTO wa_ekpo WHERE ebeln EQ wa_ekko-ebeln.
      PERFORM add_ekpo_line USING wa_ekpo ld_ebeln_key CHANGING ld_ebelp_key.
    ENDLOOP.
  ENDLOOP.
* calculate totals
*  CALL METHOD gd_tree->update_calculations.
ENDFORM.                    " CREATE_ALVTREE_HIERARCHY "
*&---------------------------------------------------------------------*
*&      Form  ADD_EKKO_NODE
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->P_WA_EKPO  text
*      -->P_0553   text
*      <--P_EBELN_KEY  text
*----------------------------------------------------------------------*
FORM add_ekko_node USING ps_ekko LIKE wa_ekko value(p_relate_key)
                CHANGING p_node_key.
  DATA: ld_node_text TYPE lvc_value,
        ls_sflight TYPE sflight.
* Set item-layout
  DATA: lt_item_layout TYPE lvc_t_layi,
        ls_item_layout TYPE lvc_s_layi.
  ls_item_layout-t_image   = '@3P@'.
  ls_item_layout-fieldname = gd_tree->c_hierarchy_column_name.
  ls_item_layout-style     = cl_gui_column_tree=>style_default.
  ld_node_text             = ps_ekko-ebeln.
  APPEND ls_item_layout TO lt_item_layout.
* Add node
  CALL METHOD gd_tree->add_node
    EXPORTING
      i_relat_node_key = p_relate_key
      i_relationship   = cl_gui_column_tree=>relat_last_child
      i_node_text      = ld_node_text
      is_outtab_line   = ps_ekko
      it_item_layout   = lt_item_layout
    IMPORTING
      e_new_node_key   = p_node_key.
ENDFORM.                    " ADD_EKKO_NODE "
*&---------------------------------------------------------------------*
*&      Form  ADD_EKPO_LINE
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->P_WA_EKPO  text
*      -->P_LD_EBELN_KEY  text
*      <--P_LD_EBELP_KEY  text
*----------------------------------------------------------------------*
FORM add_ekpo_line USING ps_ekpo LIKE wa_ekpo value(p_relate_key)
                CHANGING p_node_key.
  DATA: ld_node_text TYPE lvc_value,
        ls_sflight TYPE sflight.
* Set item-layout
  DATA: lt_item_layout TYPE lvc_t_layi,
        ls_item_layout TYPE lvc_s_layi.
  ls_item_layout-t_image   = '@3P@'.
  ls_item_layout-fieldname = gd_tree->c_hierarchy_column_name.
  ls_item_layout-style     = cl_gui_column_tree=>style_default.
  ld_node_text             = ps_ekpo-ebelp.
  APPEND ls_item_layout TO lt_item_layout.
* Add node
  CALL METHOD gd_tree->add_node
    EXPORTING
      i_relat_node_key = p_relate_key
      i_relationship   = cl_gui_column_tree=>relat_last_child
      i_node_text      = ld_node_text
      is_outtab_line   = ps_ekpo
      it_item_layout   = lt_item_layout
    IMPORTING
      e_new_node_key   = p_node_key.
ENDFORM.                    " ADD_EKPO_LINE "
```









