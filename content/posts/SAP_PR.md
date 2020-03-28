---
title: "创建PR BAPI_REQUISITION_CREATE"
date: 2020-02-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI

---

引用链接：[https://mp.weixin.qq.com/s/2nt_V5dpgxkspDQsldJJ3A](https://mp.weixin.qq.com/s/2nt_V5dpgxkspDQsldJJ3A)

### 创建PR BAPI_REQUISITION_CREATE

```JS
*&---------------------------------------------------------------------*
*& BAPI_REQUISITION_CREATE 和 BAPI_PR_CREATE 相关问题查看NOTE
*& 499627 - FAQ BAPIs for purchase requisitions
*&---------------------------------------------------------------------*
REPORT ZLM_CREATE_PR.
DATA: LT_ITEM     LIKE TABLE OF  BAPIEBANC,
      LT_RETURN   LIKE TABLE OF  BAPIRETURN.
DATA: LS_RETURN   LIKE            BAPIRETURN.
DATA: LS_ITEM     LIKE            BAPIEBANC.
*&如果有增强字段
DATA: LT_EXTENSIONIN  TYPE TABLE OF  BAPIPAREX .
DATA: LW_ITM       TYPE BAPI_TE_REQUISITION_ITEM.
DATA: LV_PR_NO        TYPE BAPIEBANC-PREQ_NO.
PARAMETERS:P_MATNR1 TYPE MATNR .
PARAMETERS:P_MATNR2 TYPE MATNR.
PARAMETERS:P_EKORG TYPE EKORG .
PARAMETERS:P_WERKS TYPE WERKS_D .
START-OF-SELECTION.
  CLEAR LT_ITEM[].
  CLEAR LS_ITEM.
  LS_ITEM-DOC_TYPE          = 'NB'.              "凭证类型
  LS_ITEM-PREQ_ITEM         = '00010'.           "项目
  LS_ITEM-MATERIAL          = P_MATNR1.             "商品代码
  LS_ITEM-QUANTITY          = 1.                 "数量
  LS_ITEM-DELIV_DATE        = SY-DATUM.        "交货日期
  LS_ITEM-PLANT             = P_WERKS.            "工厂
  LS_ITEM-PURCH_ORG             = P_EKORG.

  APPEND LS_ITEM TO LT_ITEM.
  IF P_MATNR2 IS NOT INITIAL.
    LS_ITEM-DOC_TYPE          = 'NB'.              "凭证类型
    LS_ITEM-PREQ_ITEM         = '00020'.           "项目
    LS_ITEM-MATERIAL          = P_MATNR2.             "商品代码
    LS_ITEM-QUANTITY          = 1.                 "数量
    LS_ITEM-DELIV_DATE        = SY-DATUM.        "交货日期
    LS_ITEM-PLANT             = P_WERKS.            "工厂
    LS_ITEM-PURCH_ORG             = P_EKORG.
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



