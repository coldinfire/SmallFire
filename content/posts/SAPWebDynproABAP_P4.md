---
title: " Web Dynpro ABAP Program:ALV_VIEW-Function "
date: 2018-10-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - WebDynpro
---

### ALV_VIEW Action Function:程序实例

```JS
METHOD ONACTIONPRINT .
  DATA lo_api_controller     TYPE REF TO if_wd_controller.
  DATA lo_message_manager    TYPE REF TO if_wd_message_manager.
  DATA: LT_EKPO TYPE STANDARD TABLE OF ZEKPO_asn,
        LS_EKPO TYPE ZEKPO_asn,
        LT_EKPO_SUM TYPE STANDARD TABLE OF ZEKPO_asn,
        LS_EKPO_SUM TYPE ZEKPO_asn,
        LT_EKPO_SUM_TMP TYPE STANDARD TABLE OF ZEKPO_asn,
        LS_EKPO_SUM_TMP TYPE ZEKPO_asn.
  DATA  lo_lips TYPE REF TO if_wd_context_node.
  data: lt_lips TYPE TABLE OF ZSLIPSASN,
        ls_lips TYPE ZSLIPSASN,
        lt_lips_tmp TYPE TABLE OF ZSLIPSASN,
        ls_lips_tmp TYPE ZSLIPSASN.
  data: l_index TYPE sy-tabix,
        l_name1 TYPE name1,
        l_werks TYPE werks_d,
        l_lines TYPE I,
        l_bpqnum TYPE I,
        l_result TYPE flag.
  data: lv_pdf_xstring TYPE XSTRING.
  data: lo_nd_zvendor   TYPE REF TO if_wd_context_node,
        lo_el_zvendor   TYPE REF TO if_wd_context_element,
        lo_nd_zwerks   TYPE REF TO if_wd_context_node,
        lo_el_zwerks   TYPE REF TO if_wd_context_element.
  data: l_dnnum TYPE char10,
        l_lifnr TYPE lifnr.
  data: l_boxqty TYPE char10,
        l_bpq TYPE char10,
        l_boxtail TYPE char10.
  DATA: l_name           TYPE string,
        l_elifn          TYPE elifn.

 " 将Context内容传入到 ALV内表中 "
  lo_lips = wd_context->get_child_node( name = wd_this->wdctx_zalv_lips ).
  lo_lips->get_static_attributes_table(
  IMPORTING
  table = lt_lips ).
" 获取消息管理器 "
  lo_api_controller ?= wd_this->wd_get_api( ).
  CALL METHOD lo_api_controller->get_message_manager
    RECEIVING
      message_manager = lo_message_manager.
* 获取上下文节点中的单属性值
  lo_nd_zwerks = wd_context->get_child_node( name = wd_this->wdctx_zwerks ).
  lo_el_zwerks = lo_nd_zwerks->get_element( ).
  lo_el_zwerks->get_attribute(
    EXPORTING
      name =  `WERKS`
    IMPORTING
      value = l_werks ).
  lt_lips_tmp[] = lt_lips[].
  SORT lt_lips_tmp by vbeln.
  DELETE lt_lips_tmp WHERE zsele = ''.
  DELETE ADJACENT DUPLICATES FROM lt_lips_tmp COMPARING vbeln.
  DESCRIBE TABLE lt_lips_tmp LINES l_lines.
  IF l_lines = 1.
    READ TABLE lt_lips_tmp INTO ls_lips_tmp INDEX 1.
    if sy-subrc = 0.
      l_dnnum = ls_lips_tmp-vbeln.
    ENDIF.
  ELSEIF l_lines = 0.
    CALL METHOD lo_message_manager->report_error_message
      EXPORTING
        message_text = 'Select at least one line to print'.
    return.
  ENDIF.
  lt_lips_tmp[] = lt_lips[].
  DATA: FNAME             TYPE RS38L_FNAM,
    PDF_TAB               TYPE TABLE OF TLINE,
    LV_BIN_FILESIZE       TYPE I,
    LV_CONTROL_PARAMETERS TYPE SSFCTRLOP,
    L_X                   TYPE C VALUE 'X',
    LV_OUTPUT_OPTIONS     TYPE SSFCOMPOP,
    TAB_OTF_DATA          TYPE SSFCRESCL,
    LT_OTFDATA_tmp        TYPE TABLE OF ITCOO,
    LT_OTFDATA            TYPE TABLE OF ITCOO.
" Smart form "
  CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
  FORMNAME           = 'ZSF_ASN'
*     VARIANT            = ‘ ‘
*     DIRECT_CALL        = ‘ ‘
  IMPORTING
  FM_NAME            = FNAME
  EXCEPTIONS
  NO_FORM            = 1
  NO_FUNCTION_MODULE = 2
  OTHERS             = 3.
  IF SY-SUBRC <> 0.
* Implement suitable error handling here
  ENDIF.

  delete lt_lips WHERE zsele = ''.
  SORT lt_lips by vbeln.
  delete ADJACENT DUPLICATES FROM lt_lips COMPARING vbeln.
  loop at lt_lips INTO ls_lips.
    l_dnnum = ls_lips-vbeln.
   CLEAR:lt_ekpo[],ls_ekpo,LS_EKPO_SUM,LT_EKPO_SUM[],LT_EKPO_SUM_TMP[].
    CLEAR: l_lines.
    LOOP AT lt_lips_tmp INTO ls_lips_tmp WHERE vbeln = l_dnnum AND zsele = 'X'.
      l_lines  = l_lines + 1.
      LS_EKPO-NLINE = l_lines .
      LS_EKPO-EBELN = ls_lips_tmp-vgbel.
      LS_EKPO-EBELP = ls_lips_tmp-vgpos.
      LS_EKPO-MEINS = ls_lips_tmp-meins.
      LS_EKPO-MENGE = ls_lips_tmp-lfimg.
      LS_EKPO-MATNR = ls_lips_tmp-matnr.
      LS_EKPO-TXZ01 = ls_lips_tmp-ARKTX.
      APPEND LS_EKPO TO LT_EKPO.

      LS_EKPO_SUM-MATNR = ls_lips_tmp-matnr.
      LS_EKPO_SUM-TXZ01 = ls_lips_tmp-ARKTX.
      LS_EKPO_SUM-MEINS = ls_lips_tmp-meins.
      LS_EKPO_SUM-MENGE = ls_lips_tmp-lfimg.
      COLLECT LS_EKPO_SUM INTO LT_EKPO_SUM.
      CLEAR: LS_EKPO_SUM.
    ENDLOOP.
    APPEND LINES OF LT_EKPO_SUM TO LT_EKPO_SUM_TMP.
    LT_EKPO_SUM[] = LT_EKPO_SUM_TMP[].

    l_name = wdr_task=>client_window->get_parameter( 'USERID' ).
    l_elifn = l_name.
    DATA: L_DEVTYPE TYPE RSPOPTYPE.
    CALL FUNCTION 'SSF_GET_DEVICE_TYPE'
      EXPORTING
        I_LANGUAGE = SY-LANGU
      IMPORTING
        E_DEVTYPE  = L_DEVTYPE.
    LV_CONTROL_PARAMETERS-GETOTF    = L_X.   "OTF output"
    LV_CONTROL_PARAMETERS-NO_DIALOG = SPACE. "No print dialog"
    LV_CONTROL_PARAMETERS-PREVIEW   = L_X.   "SPACE.No preview"
*lv_output_options-tdprinter     = 'LP01'.
    LV_OUTPUT_OPTIONS-TDPRINTER     =  L_DEVTYPE.
   CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
      EXPORTING
        INPUT  = l_dnnum
      IMPORTING
        OUTPUT = l_dnnum.
   CALL FUNCTION FNAME
      EXPORTING
        CONTROL_PARAMETERS = LV_CONTROL_PARAMETERS
        OUTPUT_OPTIONS     = LV_OUTPUT_OPTIONS
        USER_SETTINGS      = 'X'
        LIFNR              = l_elifn
        NAME1              = l_NAME1
        VBELN              = l_dnnum
        werks              = l_werks
      IMPORTING
        JOB_OUTPUT_INFO    = TAB_OTF_DATA
      TABLES
        IT_EKPO            = LT_EKPO
        IT_EKPO_SUM        = LT_EKPO_SUM.
    LT_OTFDATA_TMP[] = TAB_OTF_DATA-OTFDATA[].
    APPEND LINES OF LT_OTFDATA_TMP to LT_OTFDATA.
  ENDLOOP.
  CALL FUNCTION 'CONVERT_OTF'
    EXPORTING
      FORMAT                = 'PDF'
      MAX_LINEWIDTH         = 132
    IMPORTING
      BIN_FILESIZE          = LV_BIN_FILESIZE
      BIN_FILE              = LV_PDF_XSTRING
    TABLES
      OTF                   = LT_OTFDATA
      LINES                 = PDF_TAB
    EXCEPTIONS
      ERR_MAX_LINEWIDTH     = 1
      ERR_FORMAT            = 2
      ERR_CONV_NOT_POSSIBLE = 3
      ERR_BAD_OTF           = 4
      OTHERS                = 5.
*  FORMSTRING = LV_PDF_XSTRING.
  CHECK lv_pdf_xstring IS NOT INITIAL.
* Open the PDF
  data: l_filename TYPE STRING.
  CONCATENATE 'Page_' l_dnnum '.pdf' INTO l_filename.
  cl_wd_runtime_services=>attach_file_to_response(
    i_filename       = l_filename
    i_content        = lv_pdf_xstring
    i_mime_type      = 'PDF' "application/pdf"
    I_IN_NEW_WINDOW = ABAP_TRUE ).
*  wd_this->fire_OUT_ALV_TO_ASN_view_plg(  ).
ENDMETHOD.
```



