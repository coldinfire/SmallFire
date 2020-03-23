---
title: " ALV tree显示BOM结构 "
date: 2018-11-08
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - BOM

---



### 代码



```JS
REPORT z_barry_alv_tree1_bom MESSAGE-ID oo.
TABLES: stpox.
INCLUDE <icon>.
CLASS: cl_gui_column_tree DEFINITION LOAD,
       cl_gui_cfw DEFINITION LOAD .

DATA: tree1  TYPE REF TO cl_gui_alv_tree ,
      mr_toolbar TYPE REF TO cl_gui_toolbar .

DATA: gs_stpox       TYPE stpox,
      gt_stpox       TYPE stpox OCCURS 0,
      gt_fieldcatalog  TYPE lvc_t_fcat,
      gt_item_layout   TYPE lvc_t_laci,
      gs_item_layout   TYPE lvc_s_laci,
      okcode           LIKE sy-ucomm .

TYPES: BEGIN OF gs_f.
        INCLUDE STRUCTURE stpox.
TYPES: node_key      TYPE lvc_nkey,
       END   OF gs_f.
DATA: gs_xstpox       TYPE gs_f ,
      gt_xstpox       TYPE gs_f OCCURS 0.
DATA: l_custom_container TYPE REF TO cl_gui_custom_container.
DATA: wa_topmat TYPE cstmat,
      wa_dstst TYPE csdata-xfeld.
DATA: it_matcat TYPE STANDARD TABLE OF cscmat.

PARAMETERS: p_matnr LIKE mara-matnr DEFAULT 'YW25K',
            p_werks LIKE ekpo-werks DEFAULT '1010' .

START-OF-SELECTION.
  PERFORM getdata.
  CALL SCREEN 9000.
*&---------------------------------------------------------------------*
*&      Form  getdata
*&---------------------------------------------------------------------*
FORM getdata.
  CALL FUNCTION 'CS_BOM_EXPL_MAT_V2'
    EXPORTING
      capid                 = 'CAD1'      " p_capid
      datuv                 = sy-datum
      mehrs                 = 'X'         "p_mehrs
      stlal                 = '01'     " 可选 BOM
      stlan                 = '2'      "BOM 用途
      mtnrv                 = P_MATNR
      werks                 = P_WERKS
      emeng                 = 1
    IMPORTING
      topmat                = wa_topmat
      dstst                 = wa_dstst
    TABLES
      stb                   = gt_stpox
      matcat                = it_matcat
    EXCEPTIONS
      alt_not_found         = 1
      call_invalid          = 2
      material_not_found    = 3
      missing_authorization = 4
      no_bom_found          = 5
      no_plant_data         = 6
      no_suitable_bom_found = 7
      conversion_error      = 8
      OTHERS                = 9.
  CASE sy-subrc .
    WHEN 1 .
      MESSAGE e899(fi) WITH 'alt_not_found'.
    WHEN 2 .
      MESSAGE e899(fi) WITH 'call_invalid '.
    WHEN 3 .
      MESSAGE e899(fi) WITH 'material_not_found'.
    WHEN 4 .
      MESSAGE e899(fi) WITH 'missing_authorization'.
    WHEN 5 .
      MESSAGE e899(fi) WITH 'no_bom_found'.
    WHEN 6 .
      MESSAGE e899(fi) WITH 'no_plant_data'.
    WHEN 7 .
      MESSAGE e899(fi) WITH 'no_suitable_bom_found'.
    WHEN 8 .
      MESSAGE e899(fi) WITH 'conversion_error'.
    WHEN 9 .
      MESSAGE e899(fi) WITH 'OTHERS Error'.
  ENDCASE.

  LOOP AT gt_stpox INTO gs_stpox.
    MOVE-CORRESPONDING gs_stpox TO gs_xstpox .
    APPEND gs_xstpox TO gt_xstpox.
  ENDLOOP.

ENDFORM.                    "getdata
*----------------------------------------------------------------------*
*  MODULE status_9000 OUTPUT
*----------------------------------------------------------------------*
MODULE status_9000 OUTPUT.
  SET PF-STATUS 'MAIN'.
  SET TITLEBAR 'TITLE'.
  IF tree1 IS INITIAL.
    PERFORM init_tree.
  ENDIF.
  CALL METHOD cl_gui_cfw=>flush.
ENDMODULE.                 " PBO_9000  OUTPUT
*----------------------------------------------------------------------*
*  MODULE user_command_9000 INPUT
*----------------------------------------------------------------------*
MODULE user_command_9000 INPUT.
  CASE okcode.
    WHEN 'EXIT' OR 'BACK' OR 'CANC'.
      CALL METHOD tree1->free.
      LEAVE PROGRAM .

    WHEN OTHERS.
      CALL METHOD cl_gui_cfw=>dispatch.
  ENDCASE.

  CLEAR okcode.
  CALL METHOD cl_gui_cfw=>flush.
ENDMODULE.                 " okcode  INPUT

*&---------------------------------------------------------------------*
*&      Form  init_tree
*&---------------------------------------------------------------------*
FORM init_tree .
  PERFORM build_fieldcatalog.

* IF sy-batch IS INITIAL.
* CREATE OBJECT l_custom_container
* EXPORTING
* container_name              = 'TREE1'
* EXCEPTIONS
* cntl_error                  = 1
* cntl_system_error           = 2
* create_error                = 3
* lifetime_error              = 4
* lifetime_dynpro_dynpro_link = 5.
* IF sy-subrc <> 0.
* MESSAGE e000 WITH ' 创建容器：TREE1 错误 '.
* ENDIF.
* ENDIF.

  CREATE OBJECT tree1
    EXPORTING
*     parent                      = l_custom_container
      parent                      = cl_gui_container=>screen0
      node_selection_mode         = cl_gui_column_tree=>node_sel_mode_single
      item_selection              = 'X'
      no_html_header              = 'X'
      no_toolbar                  = ' '
    EXCEPTIONS
      cntl_error                  = 1
      cntl_system_error           = 2
      create_error                = 3
      lifetime_error              = 4
      illegal_node_selection_mode = 5
      failed                      = 6
      illegal_column_name         = 7.
  IF sy-subrc <> 0.
    MESSAGE e000 WITH ' 创建 TREE 错误 '.
  ENDIF.

  DATA l_hierarchy_header TYPE treev_hhdr.
  PERFORM build_hierarchy_header CHANGING l_hierarchy_header.

  DATA: ls_variant TYPE disvariant.
  ls_variant-report = sy-repid.

  CALL METHOD tree1->set_table_for_first_display
    EXPORTING
      is_hierarchy_header = l_hierarchy_header
      i_background_id     = 'ALV_BACKGROUND'
      i_save              = 'A'
      is_variant          = ls_variant
    CHANGING
      it_outtab           = gt_stpox "table must be emty !!
      it_fieldcatalog     = gt_fieldcatalog.

  DATA: l1 TYPE lvc_nkey ,l2 TYPE lvc_nkey ,l3 TYPE lvc_nkey ,l4 TYPE lvc_nkey ,
        l5 TYPE lvc_nkey ,l6 TYPE lvc_nkey ,l7 TYPE lvc_nkey ,l8 TYPE lvc_nkey ,
        l_key TYPE lvc_nkey,
        l_last_key TYPE lvc_nkey  ,
        added .
  LOOP AT gt_xstpox INTO gs_xstpox .

    MOVE-CORRESPONDING gs_xstpox TO gs_stpox.

    CASE gs_stpox-stufe .
      WHEN '1'.
        l_key = ''.
      WHEN '2'.
        l_key = l1.
      WHEN '3'.
        l_key = l2.
      WHEN '4'.
        l_key = l3.
      WHEN '5'.
        l_key = l4.
      WHEN '6'.
        l_key = l5.
    ENDCASE.

    PERFORM add_complete_line USING  gs_stpox l_key
                            CHANGING l_last_key.
    gs_xstpox-node_key = l_last_key.

    CASE gs_stpox-stufe .
      WHEN '1'.
        l1 = l_last_key.
      WHEN '2'.
        l2 = l_last_key.
      WHEN '3'.
        l3 = l_last_key.
      WHEN '4'.
        l4 = l_last_key.
      WHEN '5'.
        l5 = l_last_key.
      WHEN '6'.
        l6 = l_last_key.
    ENDCASE.

    MODIFY gt_xstpox FROM gs_xstpox .
  ENDLOOP.

  CALL METHOD tree1->update_calculations.
  CALL METHOD tree1->frontend_update.
ENDFORM.                    " init_tree

*&---------------------------------------------------------------------*
*&      Form  build_fieldcatalog
*&---------------------------------------------------------------------*
FORM build_fieldcatalog.
  CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
    EXPORTING
      i_structure_name = 'STPOX'
    CHANGING
      ct_fieldcat      = gt_fieldcatalog.

  DATA: ls_fieldcatalog TYPE lvc_s_fcat.
  LOOP AT gt_fieldcatalog INTO ls_fieldcatalog.

* CASE ls_fieldcatalog-fieldname.
* WHEN 'CARRID' OR 'CONNID' OR 'FLDATE'.
* ls_fieldcatalog-no_out = 'X'.
* ls_fieldcatalog-key    = ''.
* WHEN 'PRICE' OR 'SEATSOCC' OR 'SEATSMAX' OR 'PAYMENTSUM'.
**        ls_fieldcatalog-do_sum = 'X'.
* WHEN 'PLANETYPE'.
* ls_fieldcatalog-edit = 'X'.
* ls_fieldcatalog-style = cl_gui_alv_grid=>mc_style_enabled .
* ENDCASE.
  MODIFY gt_fieldcatalog FROM ls_fieldcatalog.
  ENDLOOP.
  ENDFORM.                               " build_fieldcatalog

*&---------------------------------------------------------------------*
*&      Form  build_hierarchy_header
*&---------------------------------------------------------------------*
FORM build_hierarchy_header CHANGING
                               p_hierarchy_header TYPE treev_hhdr.
*
  p_hierarchy_header-heading = 'BOM 层次 '.
  p_hierarchy_header-tooltip = 'ToolTip'.
  p_hierarchy_header-width = 30.
  p_hierarchy_header-width_pix = ''.
*
ENDFORM.                               " build_hierarchy_header

*&---------------------------------------------------------------------*
*&      Form  add_complete_line
*&---------------------------------------------------------------------*
FORM add_complete_line USING  ps_stpox TYPE stpox
                               p_relat_key TYPE lvc_nkey
                     CHANGING  p_node_key TYPE lvc_nkey.
  DATA: l_node_text TYPE lvc_value.
* set item-layout
  DATA: lt_item_layout TYPE lvc_t_layi,
        ls_item_layout TYPE lvc_s_layi.
  DATA: stufe_num(2) TYPE n.

  ls_item_layout-fieldname = tree1->c_hierarchy_column_name.
  ls_item_layout-class     = cl_gui_column_tree=>item_class_text.
* ls_item_layout-editable  = 'X'.
* ls_item_layout-chosen    = 'X'.  " 设置为选中状态
  APPEND ls_item_layout TO lt_item_layout.
* l_node_text =  ps_stpox-ojtxp.
  stufe_num = ps_stpox-stufe.
  CONCATENATE stufe_num ',' ps_stpox-ojtxp INTO l_node_text.

  CALL METHOD tree1->add_node
    EXPORTING
      i_relat_node_key = p_relat_key
      i_relationship   = cl_gui_column_tree=>relat_last_child
      is_outtab_line   = ps_stpox
      i_node_text      = l_node_text
      it_item_layout   = lt_item_layout
    IMPORTING
      e_new_node_key   = p_node_key.
ENDFORM.                               " add_complete_line
```

