---
title: " 冲销物料凭证 "
date: 2019-07-05
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI

---

### 全部冲销 (Full reversal)

如果必须取消整个物料凭证，则应使用 BAPI_GOODSMVT_CANCEL + BAPI_TRANSACTION_COMMIT 。 冲销凭证存储在 MSEG 中。

#### BAPI 参数

Import

- MATERIALDOCUMENT：Number of a Material Document
- MATDOCUMENTYEAR：Material Document Year
- GOODSMVT_PSTNG_DATE：Posting Date
- GOODSMVT_PR_UNAME：User Name for Printing Goods Receipt/Issue Slip

Export

- GOODSMVT_HEADRET：系统成功取消物料单据后，返回的凭证号码和凭证年

Tables

- RETURN：BAPI 执行中产生的消息。
- GOODSMVT_MATDOCITEM：可以输入要冲销的物料凭证行项目。 如果该表为空，则取消物料凭证中的所有行项目。

#### 实例代码

```ABAP
DATA:MATERIALDOCUMENT TYPE BAPI2017_GM_HEAD_02-MAT_DOC,
     MATDOCUMENTYEAR TYPE BAPI2017_GM_HEAD_02-DOC_YEAR,
     GOODSMVT_PSTNG_DATE TYPE BAPI2017_GM_HEAD_02-MAT_DOC,
     GOODSMVT_PR_UNAME TYPE BAPI2017_GM_HEAD_01-PR_UNAME.
DATA:GOODSMVT_HEADRET LIKE BAPI2017_GM_HEAD_RET,
     GOODSMVT_MATDOCITEM LIKE TABLE OF BAPI2017_GM_ITEM_04.
DATA:lt_return LIKE TABLE OF bapiret2,
     ls_return LIKE bapiret2.

MATERIALDOCUMENT = 'XXXXXXXXXX'.
GOODSMVT_PSTNG_DATE = sy-datum.
GOODSMVT_PR_UNAME =  sy-uname.
CLEAR:GOODSMVT_MATDOCITEM[],GOODSMVT_MATDOCITEM.
GOODSMVT_MATDOCITEM-MATDOC_ITEM = 0001.
APPEND GOODSMVT_MATDOCITEM.
CLEAR lt_return[].
CALL FUNCTION 'BAPI_GOODSMVT_CANCEL'
  EXPORTING
    materialdocument    = MATERIALDOCUMENT
    matdocumentyear     = sy-datum(4)
    goodsmvt_pstng_date = GOODSMVT_PSTNG_DATE
    goodsmvt_pr_uname   = GOODSMVT_PR_UNAME
  IMPORTING
    goodsmvt_headret    = GOODSMVT_HEADRET
  TABLES
    return              = lt_return
    goodsmvt_matdocitem = GOODSMVT_MATDOCITEM.
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING
    wait = 'X'.
```

### 部分冲销 (Partial reversal)

如果原始物料凭证仅应部分冲销（例如，只有整个数量的一部分必须冲销），则应使用 BAPI_GOODSMVT_CREATE，与 BAPI_GOODSMVT_CANCEL 一样，无法更改要冲销的凭证中的任何数据（ 比如减少数量）。

#### BAPI 参数

GOODSMVT_HEADER

- PSTNG_DATE：Posting Date in the Document
- DOC_DATE：Document Date in Document
- REF_DOC_NO：需要被冲销的凭据号

GOODSMVT_CODE：取决于要取消的物料凭证的移动代码

GOODSMVT_ITEM：根据需求输入尽可能详细的数据

- MOVE_TYPE：冲销的移动类型
- MVT_IND：取决于 GOODSMVT_CODE
- XSTOB：X，冲销标志 移动类型为正向
  - 冲销时与正常创建凭证一样，移动类型为正向，比如261发料，做262的冲销，移动类型仍给261，不需要修改，此处打叉即可

#### 示例程序

```ABAP
DATA: ls_head   LIKE bapi2017_gm_head_01,
      lt_item   TYPE STANDARD TABLE OF bapi2017_gm_item_create,
      ls_item   LIKE bapi2017_gm_item_create,
      lt_return TYPE STANDARD TABLE OF bapiret2,
      ls_return LIKE bapiret2,
      ls_headret LIKE bapi2017_gm_head_ret.

PARAMETERS: p_mblnr TYPE mblnr,
            p_pstdat LIKE bapi2017_gm_head_01-pstng_date,
            p_docdat LIKE bapi2017_gm_head_01-doc_date,
            p_matnr  LIKE bapi2017_gm_item_create-material,
            p_plant  LIKE bapi2017_gm_item_create-plant,
            p_sloc   LIKE bapi2017_gm_item_create-stge_loc,
            p_quant  LIKE bapi2017_gm_item_create-entry_qnt,
            p_batch  LIKE bapi2017_gm_item_create-batch..
* Prepare Data for Goods Movement
  ls_head-pstng_date = p_pstdat.
  ls_head-doc_date = p_docdat.
  ls_head-pr_uname = sy-uname.
  ls_head-ref_doc_no = p_mblnr.
  
  ls_item-material = p_matnr.
  ls_item-plant = p_plant.
  ls_item-stge_loc = p_sloc.
  ls_item-move_type = '561'.
  ls_item-entry_qnt = p_quant.
  ls_item-batch = p_batch.
  ls_item-xstob = 'X'.
  APPEND ls_item TO lt_item.
  CLEAR ls_item.

CALL FUNCTION 'BAPI_GOODSMVT_CREATE'
  EXPORTING
    GOODSMVT_HEADER  = ls_head
    GOODSMVT_CODE    = '05'
  IMPORTING
    goodsmvt_headret = ls_headret
  TABLES
    GOODSMVT_ITEM    = lt_item
    RETURN           = lt_return.
```

