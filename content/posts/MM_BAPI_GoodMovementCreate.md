---
title: " BAPI_GOODSMVT_CREATE 解析及示例 "
date: 2019-07-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM
  - BAPI

---

## Inventory Management 使用的 BAPI 解析

### 使用到的 BAPI

There are two BAPIs for posting Goods Movements:

- BAPI_GOODSMVT_CREATE (universal BAPI for posting Goods Movements)
- BAPI_GOODSMVT_CANCEL (only for reverting Goods Movements)

And two in combination with for the transactional handling:

- BAPI_TRANSACTION_COMMIT (Commit the posting, general)
- BAPI_TRANSACTION_ROLLBACK (Rollback the posting, general)

### 参数

关于要创建的物料凭证的以下信息传递到 BAPI

- A structure with the header data
- A structure with the code for the movement
- A table with the item data
- A table with the serial numbers
- The posting is made by the function module `MB_CREATE_GOODS_MOVEMENT`
- Messages are returned in the Return parameter

- BAPI 还提供了测试运行功能，传入  `TESTRUN = 'X'` 

输入参数规则

- Material number 18-character with leading zeros
- Batches with uppercase letters
- 必输：PSTNG_DATE field & DOC_DATE field (import structure GOODSMVT_HEADER) 

#### Goods Movement Code

| GM_CODE | TCODE | Description                                              |
| ------- | ----- | -------------------------------------------------------- |
| 01      | MB01  | Goods receipt for purchase order                         |
| 02      | MB31  | Goods receipt for production order                       |
| 03      | MB1A  | Goods issue                                              |
| 04      | MB1B  | Transfer posting                                         |
| 05      | MB1C  | Other goods receipt                                      |
| 06      | MB11  | Reversal of goods movements                              |
| 07      | MB04  | Subsequent adjustment with regard to a subcontract order |

根据 Goods Movement Code 输入 movement indicator (ITEM-MVT_IND)

| Fixed Value | Descript                                                   |
| ----------- | ---------------------------------------------------------- |
| SPACE       | Goods movement w/o reference                               |
| B           | Goods movement for purchase order                          |
| F           | Goods movement for production order                        |
| L           | Goods movement for delivery note                           |
| K           | Goods movement for kanban requirement (WM - internal only) |
| O           | Subsequent adjustment of "material-provided" consumption   |
| W           | Subsequent adjustment of proportion/product unit material  |

### Example Code for calling the GM BAPI to post a 561 Goods Movement

```ABAP
*&---------------------------------------------------------------------*
*& Report  Z_BAPI_GDSMVT
*&---------------------------------------------------------------------*
REPORT  z_bapi_gdsmvt.
DATA: ls_head   LIKE bapi2017_gm_head_01,
      lt_item   TYPE STANDARD TABLE OF bapi2017_gm_item_create,
      ls_item   LIKE bapi2017_gm_item_create,
      lt_return TYPE STANDARD TABLE OF bapiret2,
      ls_return LIKE bapiret2,
      ls_headret LIKE bapi2017_gm_head_ret,
      ls_serial   LIKE bapi2017_gm_serialnumber,
      lt_serial   LIKE  STANDARD TABLE OF bapi2017_gm_serialnumber.
PARAMETERS: p_pstdat LIKE bapi2017_gm_head_01-pstng_date,
            p_docdat LIKE bapi2017_gm_head_01-doc_date,
            p_matnr  LIKE bapi2017_gm_item_create-material,
            p_plant  LIKE bapi2017_gm_item_create-plant,
            p_sloc   LIKE bapi2017_gm_item_create-stge_loc,
            p_quant  LIKE bapi2017_gm_item_create-entry_qnt,
            p_batch  LIKE bapi2017_gm_item_create-batch.
START-OF-SELECTION.
* Prepare Data for Goods Movement
  ls_head-pstng_date = p_pstdat.
  ls_head-doc_date = p_docdat.
  ls_head-pr_uname = sy-uname.

  ls_item-material = p_matnr.
  ls_item-plant = p_plant.
  ls_item-stge_loc = p_sloc.
  ls_item-move_type = '561'.
  ls_item-entry_qnt = p_quant.
  ls_item-batch = p_batch.
  APPEND ls_item TO lt_item.
  CLEAR ls_item.
* Call BAPI
  CALL FUNCTION 'BAPI_GOODSMVT_CREATE'
    EXPORTING
      goodsmvt_header  = ls_header
      goodsmvt_code    = '05'
    IMPORTING
      goodsmvt_headret = ls_headret
    TABLES
      goodsmvt_item    = lt_item
      return           = lt_return.
* If no error, commit
  IF lt_return IS INITIAL.
    WRITE: 'Material Document posted:', ls_headret-mat_doc, ' ', ls_headret-doc_year.
    CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
      EXPORTING
        wait = 'X'.
*   Alternative COMMIT WORK.
  ELSE.
    CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
*   Alternative ROLLBACK WORK.
    WRITE: 'Error during posting Material document:', /.
    LOOP AT lt_return INTO ls_return.
      ...
    ENDLOOP.
  ENDIF.
```

#### 循环调用 BAPI 示例

```ABAP
*&---------------------------------------------------------------------*
*& Report  ZBAPI_GDSMVT
*&---------------------------------------------------------------------*
REPORT  zbapi_gdsmvt. 
* DATA: ...
* PARAMETERS: ...
START-OF-SELECTION.
* Prepare data for first Goods Movement
* Call BAPI to create Goods Movement
CALL FUNCTION 'BAPI_GOODSMVT_CREATE' DESTINATION 'NONE'
* If no error, commit
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT' DESTINATION 'NONE'
    EXPORTING
      wait = 'X'.
* ELSE
* Error handling 
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK' DESTINATION 'NONE'.
* Close RFC connection
  CALL FUNCTION 'RFC_CONNECTION_CLOSE'
    EXPORTING
      destination = 'NONE'.
* Prepare data for next Goods Movement
* Call BAPI to create Goods Movement
CALL FUNCTION 'BAPI_GOODSMVT_CREATE' DESTINATION 'NONE'
* If no error, commit
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT' DESTINATION 'NONE'
    EXPORTING
      wait = 'X'.
* ELSE
* Error handling 
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK' DESTINATION 'NONE'.
* Close RFC connection
  CALL FUNCTION 'RFC_CONNECTION_CLOSE'
    EXPORTING
      destination = 'NONE'.
```

### 特殊库存类型需要参数

Special stock (e.g. sales order, project, vendor etc.)

#### Sales Order Stock

- SPEC_STOCK (Special stock indicator for Sales order stock)
- SALES_ORD & S_ORD_ITEM
- VAL_SALES_ORD & VAL_S_ORD_ITEM 

#### Project Stock

- SPEC_STOCK (Special stock indicator for Project stock)
- WBS_ELEM & VAL_WBS_ELEM



**参考**

- [Goods Movements with BAPI](https://wiki.scn.sap.com/wiki/display/ERPSCM/Goods+Movements+with+BAPI)