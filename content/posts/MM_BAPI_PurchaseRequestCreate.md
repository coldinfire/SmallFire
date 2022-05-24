---
title: " BAPI 创建 Purchase Request "
date: 2020-06-12
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

#### Purchase Request 内容

- PR Header : 采购订单编号、供应商、货币、付款期限、采购订单日期
  
- PR Item : 物料编号、短文本、发货日期、采购订单数量、采购订单价格、分类

BAPI: 

- ME51N：BAPI_PR_CREATE & BAPI_PR_CHANGE

- ME51：BAPI_REQUISITION_CREATE & BAPI_REQUISITION_CHANGE

#### BAPI_PR_CREATE 实例

```JS
DATA:PREQ_NO      TYPE  BAPIMEREQHEADER-PREQ_NO,
     PRHEADER    TYPE  BAPIMEREQHEADER,
     PRHEADERX   TYPE  BAPIMEREQHEADERX,
     RETURN      TYPE TABLE OF  BAPIRET2 WITH HEADER LINE,
     PRITEM   TYPE TABLE OF BAPIMEREQITEMIMP WITH HEADER LINE,
     PRITEMX  TYPE TABLE OF BAPIMEREQITEMX WITH HEADER LINE.
DATA:LV_POS TYPE I.
" Fill Header "
PRHEADER-PR_TYPE = 'NB'.
PRHEADERX-PR_TYPE = 'X'.
" Fill Item "
PRITEM-PREQ_ITEM = LV_POS. " Item Number of Purchase Requisition "
PRITEM-PUR_GROUP = '011'.  " Purchasing Group "
PRITEM-MATERIAL = LV_MATNR.
PRITEM-PLANT    = LV_PLANT.
PRITEM-STORE_LOC = LV_LGORT. 
PRITEM-QUANTITY = LV_MENGE.
PRITEM-UNIT     = LV_MEINS.
PRITEM-DELIV_DATE = SY_DATUM. "Delivery Date of Goods"
APPEND PRITEM.

PRITEMX-PREQ_ITEM = LV_POS.
PRITEMX-PREQ_ITEMX = 'X'.
PRITEMX-PUR_GROUP = 'X'.
PRITEMX-PREQ_NAME = 'X'.
PRITEMX-MATERIAL = 'X'.
PRITEMX-PLANT = 'X'.
PRITEMX-STORE_LOC = 'X'.
PRITEMX-QUANTITY = 'X'.
PRITEMX-UNIT = 'X'.
PRITEMX-DELIV_DATE = 'X'.
APPEND PRITEMX.  

CALL FUNCTION 'BAPI_PR_CREATE'
  EXPORTING
    PRHEADER              = PRHEADER
    PRHEADERX             = PRHEADERX
    TESTRUN               = TESTRUN
  IMPORTING
    NUMBER                = PREQ_NO
    PRHEADEREXP           = PRHEADEREXP
  TABLES
    RETURN                = RETURN
    PRITEM                = PRITEM
    PRITEMX               = PRITEMX.

READ TABLE RETURN WITH KEY TYPE = 'E' TRANSPORTING NO FIELDS.
IF SY-SUBRC NE 0.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      WAIT = 'X'.
ELSE.
  CLEAR PREQ_NO.
ENDIF.
```

#### BAPI_REQUISITION_CREATE Demo1

```ABAP
FORM create_pr_batch .
  DATA: BEGIN OF gt_data1 OCCURS 0,
    bsart   TYPE string, "凭证类型"
    bnfpo   TYPE string, "项目"
*   KNTTP   TYPE STRING, "科目分配类别"
    matnr   TYPE string, "商品代码"
*   TXZ01   TYPE STRING, "短文本"
    menge   TYPE string, "数量"
    meins   TYPE string, "单位"
    eeind   TYPE string, "交货日期"
*   MATKL   TYPE STRING, "物料组"
    werks   TYPE string, "工厂"
    ekgrp   TYPE string, "采购组"
    afnam   TYPE string, "申请者"
    bednr   TYPE string, "需求跟踪号"
    sakto   TYPE string, "总帐科目"
    kostl   TYPE string, "成本中心"
    anln1   TYPE string, "资产"
    aufnr   TYPE string, "订单"
    waers   TYPE string, "币种"
    peinh   TYPE string, "价格单位"
    dispo   TYPE string, "MRP控制者"
    str1    TYPE string, "行项目文本-传送文本"
    str2    TYPE string, "行项目文本-预算年度"
    str3    TYPE string, "行项目文本-资产类别"
  END OF gt_data1.
  DATA: BEGIN OF gt_data OCCURS 0,
    bednr   TYPE string, "需求跟踪号"
    bsart   TYPE string, "凭证类型"
    bnfpo   TYPE string, "项目"
*   KNTTP   TYPE STRING, "科目分配类别"
    matnr   TYPE string, "商品代码"
*   TXZ01   TYPE STRING, "短文本"
    menge   TYPE string, "数量"
    meins   TYPE string, "单位"
    eeind   TYPE string, "交货日期"
*   MATKL   TYPE STRING, "物料组"
    werks   TYPE string, "工厂"
    ekgrp   TYPE string, "采购组"
    afnam   TYPE string, "申请者"
    sakto   TYPE string, "总帐科目"
    kostl   TYPE string, "成本中心"
    anln1   TYPE string, "资产"
    aufnr   TYPE string, "订单"
    preis   TYPE string, "评估价格"
    waers   TYPE string, "币种"
    peinh   TYPE string, "价格单位"
    dispo   TYPE string, "MRP控制者"
    str1    TYPE string, "行项目文本-传送文本"
    str2    TYPE string, "行项目文本-预算年度"
    str3    TYPE string, "行项目文本-资产类别"
  END OF gt_data.
  "=============== Upload File ==============="
  LOOP AT gt_data.
    bnfpo = gt_data-bnfpo.
    CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
      EXPORTING
        input  = bnfpo
      IMPORTING
        output = bnfpo.
    CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
      EXPORTING
        input  = gt_data-matnr
      IMPORTING
        output = matnr.
    pr_item-doc_type          = gt_data-bsart.     "凭证类型"
    pr_item-preq_item         = bnfpo.             "项目"
*   PR_ITEM-ACCTASSCAT        = GT_DATA-KNTTP.     "科目分配类别"
    pr_item-material          = matnr.             "商品代码"
*   PR_ITEM-SHORT_TEXT        = GT_DATA-TXZ01.     "短文本"
    pr_item-quantity          = gt_data-menge.     "数量"
    pr_item-unit              = gt_data-meins.     "单位"
    pr_item-deliv_date        = gt_data-eeind.     "交货日期"
*    PR_ITEM-MAT_GRP          = GT_DATA-MATKL.     "物料组"
    pr_item-plant             = gt_data-werks.     "工厂"
    pr_item-pur_group         = gt_data-ekgrp.     "采购组"
    pr_item-preq_name         = gt_data-afnam.     "申请者"
    pr_item-trackingno        = gt_data-bednr.     "需求跟踪号"
    pr_item-c_amt_bapi        = gt_data-preis.     "评估价格"
    pr_item-currency          = gt_data-waers.     "货币码"
    pr_item-price_unit        = gt_data-peinh.     "价格单位"
    pr_item-mrp_contr         = gt_data-dispo.     "MRP控制者"
    APPEND pr_item.
    CLEAR pr_item.
    pr_account-preq_item = bnfpo.        "项目"
    pr_account-g_l_acct = gt_data-sakto. "总帐科目"
    pr_account-cost_ctr = gt_data-kostl. "成本中心"
    pr_account-asset_no = gt_data-anln1. "资产"
    pr_account-order_no = gt_data-aufnr. "订单"
    pr_account-co_area = 'BELL'.
    APPEND pr_account.
    CLEAR pr_account.
    pr_item_id-preq_item = bnfpo.
    pr_item_id-text_id = 'B03'.
    pr_item_id-text_line = gt_data-str1."行项目文本-传送文本"
    APPEND pr_item_id.
    CLEAR pr_item_id.
    pr_item_id-preq_item = bnfpo.
    pr_item_id-text_id = 'B07'.
    pr_item_id-text_line = gt_data-str2."行项目文本-预算年度"
    APPEND pr_item_id.
    CLEAR pr_item_id.
    pr_item_id-preq_item = bnfpo.
    pr_item_id-text_id = 'B08'.
    pr_item_id-text_line = gt_data-str3."行项目文本-资产类别"
    APPEND  pr_item_id.
    CLEAR  pr_item_id.
    AT END OF bednr.
    
    CALL FUNCTION 'BAPI_REQUISITION_CREATE'
	" EXPORTING
	"   SKIP_ITEMS_WITH_ERROR                =
	"   AUTOMATIC_SOURCE                     = 'X'
      IMPORTING
        number                               = pr_no
      TABLES
        requisition_items                    = pr_item
        requisition_account_assignment       = pr_account
        requisition_item_text                = pr_item_id
	"   REQUISITION_LIMITS                   =
	"   REQUISITION_CONTRACT_LIMITS          =
	"   REQUISITION_SERVICES                 =
	"   REQUISITION_SRV_ACCASS_VALUES        =
        return                               = pr_return
	"   REQUISITION_SERVICES_TEXT            =
	"   REQUISITION_ADDRDELIVERY             =
	"   EXTENSIONIN                          =
                .
      CLEAR: lv_message.
      LOOP AT pr_return WHERE type = 'E' OR type = 'A'.
        MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
			INTO lv_message
            WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
      ENDLOOP.
      IF sy-subrc = 0.
        CLEAR: lv_message.
        CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
        LOOP AT pr_return INTO l_return WHERE type = 'E' .
          CONCATENATE lv_message l_return-message ';'
            INTO lv_message.
        ENDLOOP.
        CONCATENATE gt_data-bednr lv_message INTO gt_out-text.
        APPEND gt_out.
        CLEAR gt_out.
      ELSE.
        CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
          EXPORTING
            wait = 'X'.
        CONCATENATE pr_no '创建成功' INTO gt_out-text.
        APPEND gt_out.
        CLEAR gt_out.
      ENDIF.
      FREE pr_item.
      FREE pr_account.
      FREE pr_item_id.
      FREE pr_return.
    ENDAT.
  ENDLOOP.
ENDFORM.                    " CREATE_PR "
```

Demo2

```ABAP
*&---------------------------------------------------------------------*
*& BAPI_REQUISITION_CREATE 和 BAPI_PR_CREATE 相关问题查看NOTE
*& 499627 - FAQ BAPIs for purchase requisitions
*&---------------------------------------------------------------------*
REPORT ZLM_CREATE_PR.
DATA: LT_ITEM     LIKE TABLE OF  BAPIEBANC,
      LT_RETURN   LIKE TABLE OF  BAPIRETURN,
      LS_RETURN   LIKE  BAPIRETURN,
      LS_ITEM     LIKE BAPIEBANC.
*&如果有增强字段
DATA: LT_EXTENSIONIN  TYPE TABLE OF  BAPIPAREX .
DATA: LW_ITM    TYPE BAPI_TE_REQUISITION_ITEM.
DATA: LV_PR_NO  TYPE BAPIEBANC-PREQ_NO.
PARAMETERS:P_MATNR1 TYPE MATNR .
PARAMETERS:P_MATNR2 TYPE MATNR.
PARAMETERS:P_EKORG  TYPE EKORG .
PARAMETERS:P_WERKS  TYPE WERKS_D.
START-OF-SELECTION.
  CLEAR LT_ITEM[].
  CLEAR LS_ITEM.
  LS_ITEM-DOC_TYPE      = 'NB'.              "凭证类型"
  LS_ITEM-PREQ_ITEM     = '00010'.           "项目"
  LS_ITEM-MATERIAL      = P_MATNR1.          "商品代码"
  LS_ITEM-QUANTITY      = 1.                 "数量"
  LS_ITEM-DELIV_DATE    = SY-DATUM.          "交货日期"
  LS_ITEM-PLANT         = P_WERKS.           "工厂"
  LS_ITEM-PURCH_ORG     = P_EKORG.

  APPEND LS_ITEM TO LT_ITEM.
  IF P_MATNR2 IS NOT INITIAL.
    LS_ITEM-DOC_TYPE     = 'NB'.           "凭证类型"
    LS_ITEM-PREQ_ITEM    = '00020'.        "项目"
    LS_ITEM-MATERIAL     = P_MATNR2.       "商品代码"
    LS_ITEM-QUANTITY     = 1.              "数量"
    LS_ITEM-DELIV_DATE   = SY-DATUM.       "交货日期"
    LS_ITEM-PLANT        = P_WERKS.        "工厂"
    LS_ITEM-PURCH_ORG    = P_EKORG.
    APPEND LS_ITEM TO LT_ITEM.
  ENDIF.
*  extensionin-structure = 'BAPI_TE_REQUISITION_ITEM'.
*  extensionin-valuepart1 = lw_itm.
*  APPEND EXTENSIONIN.

  CALL FUNCTION 'BAPI_REQUISITION_CREATE'
    IMPORTING
      NUMBER            = LV_PR_NO
    TABLES
      REQUISITION_ITEMS = LT_ITEM
*     requisition_account_assignment = pr_account
*     requisition_item_text          = pr_item_id
      RETURN            = LT_RETURN.
*      extensionin                    = extensionin[].

  LOOP AT LT_RETURN INTO LS_RETURN WHERE TYPE = 'E' .
    WRITE LS_RETURN-MESSAGE.
  ENDLOOP.
  WRITE LV_PR_NO.
```

