---
title: " SALV创建ALV "
date: 2019-06-06
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### 创建和显示

```JS
*&---------------------------------------------------------------------*
*& This code snippet will show how to use the CL_SALV_TABLE to
*&   generate the ALV
*&---------------------------------------------------------------------*
REPORT  ztest_oo_alv_main.
*----------------------------------------------------------------------*
*       CLASS lcl_report DEFINITION
*----------------------------------------------------------------------*
CLASS lcl_report DEFINITION.
  PUBLIC SECTION.
*   Final output table
    TYPES: BEGIN OF ty_vbak,
           vbeln TYPE vbak-vbeln,
           erdat TYPE erdat,
           auart TYPE auart,
           kunnr TYPE kunnr,
           netwr TYPE netwr,
           check TYPE flag,   "复选框
           t_color   TYPE lvc_t_scol,"单元格颜色设置
           END   OF ty_vbak.
    TYPES: ty_t_vbak TYPE STANDARD TABLE OF ty_vbak.
    DATA: t_vbak TYPE STANDARD TABLE OF ty_vbak.
*   ALV reference
    DATA: o_alv TYPE REF TO cl_salv_table.
    
    METHODS:
      get_data,  "data selection  
      generate_output. "Generating output
*$*$*.....CODE_ADD_1 - Begin..................................1..*$*$*
*    In this section we will define the private methods which can
*      be implemented to set the properties of the ALV and can be
*      called in the
*$*$*.....CODE_ADD_1 - End....................................1..*$*$*
ENDCLASS.                    "lcl_report DEFINITION

START-OF-SELECTION.
  DATA: lo_report TYPE REF TO lcl_report.
  CREATE OBJECT lo_report.
  lo_report->get_data( ).
  lo_report->generate_output( ).
  
*----------------------------------------------------------------------*
*       CLASS lcl_report IMPLEMENTATION
*----------------------------------------------------------------------*
CLASS lcl_report IMPLEMENTATION.
  METHOD get_data.
    SELECT vbeln erdat auart kunnr
           INTO  TABLE t_vbak
           FROM  vbak
           UP TO 20 ROWS.
  ENDMETHOD.                    "get_data
*.......................................................................
  METHOD generate_output.
  	"Calling the static Factory method
    DATA: lx_msg TYPE REF TO cx_salv_msg.
    TRY.
        cl_salv_table=>factory(
          EXPORTING
            list_display = abap_ture  "以列表还是GUI形式控制
          IMPORTING
            r_salv_table = o_alv      "用来接收工厂产生的实例
          CHANGING
            t_table      = t_vbak ).
      CATCH cx_salv_msg INTO lx_msg.
    ENDTRY.
      
*$*$*.....CODE_ADD_2 - Begin..................................2..*$*$*
*    In this area we will call the methods which will set the
*      different properties to the ALV
*$*$*.....CODE_ADD_2 - End....................................2..*$*$*
   
    "Displaying the ALV
    o_alv->display( ).
  ENDMETHOD.                    "generate_output

*$*$*.....CODE_ADD_3 - Begin..................................3..*$*$*
*    In this area we will implement the methods which are defined in
*      the class definition
*$*$*.....CODE_ADD_3 - End....................................3..*$*$*
	
ENDCLASS.                    "lcl_report IMPLEMENTATION


```


