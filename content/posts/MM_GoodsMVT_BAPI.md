---
title: " BAPI_GOODSMVT_CREATE Demo "
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

### Inventory Management使用的BAPI

There are two BAPIs for posting Goods Movements:

- BAPI_GOODSMVT_CREATE (universal BAPI for posting Goods Movements)
- BAPI_GOODSMVT_CANCEL (only for reverting Goods Movements)

And two in combination with for the transactional handling:

- BAPI_TRANSACTION_COMMIT (Commit the posting, general)
- BAPI_TRANSACTION_ROLLBACK (Rollback the posting, general)

#### 参数

**关于要创建的物料凭证的以下信息传递到 BAPI：**

- A structure with the header data
- A structure with the code for the movement
- A table with the item data
- A table with the serial numbers
- The posting is made by the function module MB_CREATE_GOODS_MOVEMENT.
- Messages are returned in the Return parameter.

**Goods Movement Code:**

| GM_CODED | TCODE | Desc                                                     |
| -------- | ----- | -------------------------------------------------------- |
| 01       | MB01  | Goods receipt for purchase order                         |
| 02       | MB31  | Goods receipt for production order                       |
| 03       | MB1A  | Goods issue                                              |
| 04       | MB1B  | Transfer posting                                         |
| 05       | MB1C  | Other goods receipt                                      |
| 06       | MB11  | Reversal of goods movements                              |
| 07       | MB04  | Subsequent adjustment with regard to a subcontract order |

输入 **movement indicator**

- GM_Code 01 : B
- GM_Code 02 : F
- Others : blank.

### Example Code for calling the GM BAPI to post a 561 Goods Movement

```JS
*&---------------------------------------------------------------------*
*& Report  Z_BAPI_GDSMVT
*&
*&---------------------------------------------------------------------*
 
REPORT  z_bapi_gdsmvt.
 
DATA: ls_mmdochdr LIKE bapi2017_gm_head_01,
      lt_gm       TYPE STANDARD TABLE OF bapi2017_gm_item_create,
      ls_gm       LIKE bapi2017_gm_item_create,
      lt_ret      TYPE STANDARD TABLE OF bapiret2,
      ls_ret      LIKE bapiret2,
      ls_hdr      LIKE bapi2017_gm_head_ret,
      ls_ser      LIKE bapi2017_gm_serialnumber,
      lt_ser      LIKE  STANDARD TABLE OF bapi2017_gm_serialnumber.
 
PARAMETERS: p_pstdat LIKE bapi2017_gm_head_01-pstng_date,
            p_docdat LIKE bapi2017_gm_head_01-doc_date,
            p_matnr  LIKE bapi2017_gm_item_create-material,
            p_plant  LIKE bapi2017_gm_item_create-plant,
            p_sloc   LIKE bapi2017_gm_item_create-stge_loc,
            p_quant  LIKE bapi2017_gm_item_create-entry_qnt,
            p_batch  LIKE bapi2017_gm_item_create-batch.
 
START-OF-SELECTION.
 
* Prepare Data for Goods Movement
  ls_mmdochdr-pstng_date = p_pstdat.
  ls_mmdochdr-doc_date = p_docdat.
 
  ls_gm-move_type = '561'.
  ls_gm-material = p_matnr.
  ls_gm-plant = p_plant.
  ls_gm-stge_loc = p_sloc.
  ls_gm-entry_qnt = p_quant.
  ls_gm-batch = p_batch.
 
  APPEND ls_gm TO lt_gm.
  CLEAR ls_gm.
 
* Call BAPI
  CALL FUNCTION 'BAPI_GOODSMVT_CREATE'
    EXPORTING
      goodsmvt_header  = ls_mmdochdr
      goodsmvt_code    = '05'
    IMPORTING
      goodsmvt_headret = ls_hdr
    TABLES
      goodsmvt_item    = lt_gm
      return           = lt_ret.
 
* If no error, commit
  IF lt_ret IS INITIAL.
    WRITE: 'Material Document posted:', ls_hdr-mat_doc, ' ', ls_hdr-doc_year.
 
    CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
      EXPORTING
        wait = 'X'.
*   Alternative COMMIT WORK.
  ELSE.
    CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
*   Alternative ROLLBACK WORK.
    WRITE: 'Error during posting Material document:', /.
    LOOP AT lt_ret INTO ls_ret.
      WRITE: ls_ret-type,
             ls_ret-id,
             ls_ret-number,
             ls_ret-message,
             ls_ret-log_no,
             ls_ret-log_msg_no,
             ls_ret-message_v1,
             ls_ret-message_v2,
             ls_ret-message_v3,
             ls_ret-message_v4.
    ENDLOOP.
  ENDIF.
```



**循环调用BAPI:**

```JS
*&---------------------------------------------------------------------*
*& Report  ZBAPI_GDSMVT
*&
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

#### BAPI_GOODSMVT_CANCEL 冲销物料凭证



**参考**

- [Goods Movements with BAPI](https://wiki.scn.sap.com/wiki/display/ERPSCM/Goods+Movements+with+BAPI)