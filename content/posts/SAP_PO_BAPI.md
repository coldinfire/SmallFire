---
title: "生产订单标准BAPI"
date: 2019-08-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - PO

---



### 创建PO



```jsp
*&---------------------------------------------------------------------*
*&      Form  FRM_ORDER_CREATE
*&---------------------------------------------------------------------*
*----------------------------------------------------------------------*
"-->  p1        text
"<--  p2        text
*----------------------------------------------------------------------*
  FORM frm_order_create .
  DATA: ls_upload LIKE LINE OF gt_upload,
        ls_style  TYPE lvc_s_styl,
        lv_index  LIKE sy-tabix.
  DATA: ls_order  TYPE bapi_pp_order_create,
        ls_return TYPE bapiret2,
        lv_ordnum TYPE bapi_order_key-order_number,
        lv_ordtyp TYPE bapi_order_copy-order_type.
  LOOP AT gt_upload INTO ls_upload WHERE ztype EQ space AND box EQ 'X'.
    lv_index = sy-tabix.
    CLEAR: ls_order, ls_return, lv_ordnum, lv_ordtyp, ls_style.
    IF ls_upload-externind EQ 'X'.
      ls_order-order_number = ls_upload-aufnr.
    ENDIF.
    ls_order-material = ls_upload-plnbez.
    ls_order-plant = ls_upload-dwerk.
    ls_order-planning_plant = ls_upload-dwerk.
    ls_order-order_type = ls_upload-dauat.
    ls_order-quantity = ls_upload-gamng.
    ls_order-quantity_uom = ls_upload-gmein.
    ls_order-basic_start_date = ls_upload-gstps.
    ls_order-basic_start_time = ls_upload-gsuzs.
    ls_order-basic_end_date = ls_upload-gltps.
    ls_order-basic_end_time  = ls_upload-gluzs.
    ls_order-unloading_point = ls_upload-ablad.
    ls_order-sequence_number = ls_upload-cy_seqnr.
    IF ls_upload-verid NE space.
      ls_order-prod_version = ls_upload-verid.
    ENDIF.
    IF ls_upload-lgort NE space.
      ls_order-storage_location = ls_upload-lgort.
    ENDIF.
    CALL FUNCTION 'BAPI_PRODORD_CREATE'
      EXPORTING
        orderdata    = ls_order
      IMPORTING
        return       = ls_return
        order_number = lv_ordnum
        order_type   = lv_ordtyp.
    IF lv_ordnum NE space.
      CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
        EXPORTING
          wait = 'X'.
      CLEAR: ls_upload-fstyle, ls_upload-fstyle[].
      ls_style-style = cl_gui_alv_grid=>mc_style_disabled.
      INSERT ls_style INTO TABLE ls_upload-fstyle.
      ls_upload-aufnr = lv_ordnum.
      ls_upload-ztype = 'S'.
      ls_upload-message = 'Update successfully'.
    ELSE.
      ls_upload-ztype = ls_return-type.
      CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
    ENDIF.
    IF ls_return-type NE space.
      APPEND ls_return TO ls_upload-retmsg.
    ENDIF.
    IF ls_upload-ztype = 'E' .
      ls_upload-zcol = 'C610' .
    ENDIF .
    MODIFY gt_upload FROM ls_upload INDEX lv_index.
  ENDLOOP.
  ENDFORM.                    " FRM_ORDER_CREATE
```

