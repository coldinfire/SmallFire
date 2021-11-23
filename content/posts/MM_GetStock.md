---
title: " SAP 获取库存函数 "
date: 2020-12-20
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

SAP的库存种类有很多中取数逻辑，这里整理下，汇总成的一个接口函数。

### 库存类型

非批次管理库存：**MARD**

批次库存：**MCHB**

销售库存：**MSKA**

项目库存：**MSPR**

已发货数量：**LIPS、LIKP**

### 程序代码

```ABAP
FUNCTION zwi_get_stock.
*"----------------------------------------------------------------------
*"*"Local Interface:
*"  IMPORTING
*"     VALUE(PLANT) TYPE  WERKS_D
*"     VALUE(MATERIAL) TYPE  MATNR
*"     VALUE(UNIT) TYPE  MEINH
*"     VALUE(STGE_LOC) TYPE  LGORT_D
*"     VALUE(BATCH) TYPE  CHARG_D OPTIONAL
*"     VALUE(VBELN) TYPE  VBELN OPTIONAL
*"     VALUE(POSNR) TYPE  POSNR_BI OPTIONAL
*"     VALUE(STOCK_TYPE) TYPE CHAR2 OPTIONAL
*"  EXPORTING
*"     VALUE(AV_QTY_PLT) TYPE  MNG01
*"  TABLES
*"      E_RETURN STRUCTURE  BAPIRET2
*"----------------------------------------------------------------------
  DATA: ls_mara TYPE mara,
        ls_mchb TYPE mchb,
        ls_mard TYPE mard,
        ls_mska TYPE mska,
        ls_mspr TYPE mspr.
  DATA: menge_ur_status TYPE mng01.  "Valuated Unrestricted-Use Stock"
  DATA: menge_qi_status TYPE mng01.  "Stock in Quality Inspection"
  DATA: menge_blk_status TYPE mng01.  "Blocked Stock"
  DATA: menge_return TYPE mng01. " Stock result

  CLEAR: ls_mara.
  SELECT SINGLE matnr meins xchpf
    INTO (ls_mara-matnr, ls_mara-meins, ls_mara-xchpf)
    FROM mara
    WHERE matnr = material.
  IF ls_mara-xchpf = 'X'.
    "Get batch management stock"
    CLEAR: ls_mchb.
    SELECT SINGLE clabs cinsm cspem
      INTO  (ls_mchb-clabs,ls_mchb-cinsm,ls_mchb-cspem)
      FROM mchb
      WHERE werks = plant
      AND matnr = material
      AND lgort = stge_loc
      AND charg = batch.
    IF sy-subrc = 0.
      menge_ur_status = ls_mchb-clabs.
      menge_qi_status = ls_mchb-cinsm.
      menge_blk_status = ls_mchb-cspem.
    ENDIF.
    "Get project stock"
    SELECT SINGLE prlab prins prspe
      INTO (ls_mspr-prlab,ls_mspr-prins,ls_mspr-prspe)
      FROM mspr
      WHERE werks = plant
      AND matnr = material
      AND lgort = stge_loc
      AND charg = batch.
    IF sy-subrc EQ 0 .
      menge_ur_status = ls_mspr-prlab.
      menge_qi_status = ls_mspr-prins.
      menge_blk_status = ls_mspr-prspe.
    ENDIF.
  ELSE.
  "Get non-batch management stock"
    CLEAR: ls_mard.
    SELECT SINGLE labst insme speme
      INTO (ls_mard-labst,ls_mard-insme,ls_mard-speme)
      FROM mard
      WHERE werks = plant
      AND matnr = material
      AND lgort = stge_loc.
    IF sy-subrc = 0.
      menge_ur_status = ls_mard-labst.
      menge_qi_status = ls_mard-insme.
      menge_blk_status = ls_mard-speme.
    ENDIF.
  ENDIF.
  "Sales order stock"
  IF vbeln IS NOT INITIAL.
    SELECT SINGLE kalab kains kaspe
        INTO (ls_mska-kalab,ls_mska-kains,ls_mska-kaspe)
        FROM mska
        WHERE matnr = material
        AND werks = plant
        AND lgort = stge_loc
        AND charg = batch
        AND vbeln = vbeln
        AND posnr = posnr.
    IF sy-subrc = 0.
      menge_ur_status = ls_mska-kalab.
      menge_qi_status = ls_mska-kains.
      menge_blk_status = ls_mska-kaspe.
    ENDIF.
  ENDIF.

  IF STOCK_TYPE IS INITIAL.  "UR Stock"
    menge_return = menge_ur_status.
  ELSEIF STOCK_TYPE EQ 'QI'. "UR Stock"
    menge_return = menge_qi_status.
  ELSEIF STOCK_TYPE EQ 'BK'.
    megne_return = menge_bkk_status.
  ENDIF.

  CLEAR: menge_return.
  IF ls_mara-meins <> unit.
    CALL FUNCTION 'MD_CONVERT_MATERIAL_UNIT'
      EXPORTING
        i_matnr              = material
        i_in_me              = ls_mara-meins
        i_out_me             = unit
        i_menge              = menge_return
      IMPORTING
        e_menge              = av_qty_plt
      EXCEPTIONS
        error_in_application = 1
        error                = 2
        OTHERS               = 3.
    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
              WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4 INTO e_return-message.
      e_return-type = 'E'.
      RETURN.
    ENDIF.
  ELSE.
    av_qty_plt = menge_return.
  ENDIF.
ENDFUNCTION.
```

