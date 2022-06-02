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

### VL09：交货单冲销

```ABAP
FORM cx_dn USING gt_out TYPE typ_out.
  DATA: ls_emkpf TYPE emkpf,
        lt_mesg TYPE STANDARD TABLE OF mesg.
  SELECT SINGLE wbstk,vbtyp INTO @data(l_likp)
    FROM likp
   WHERE vbeln = gt_out-vbeln.
  CLEAR: ls_emkpf,lt_mesg.
  CALL FUNCTION 'WS_REVERSE_GOODS_ISSUE'
    EXPORTING
      i_vbeln                   = gt_out-vbeln
      i_budat                   = gt_out-budat
      i_vbtyp                   = l_likp-vbtyp
      i_tcode                   = 'VL09'
    IMPORTING
      es_emkpf                  = ls_emkpf
    TABLES
      t_mesg                    = lt_mesg
    EXCEPTIONS
      error_reverse_goods_issue = 1
      others                    = 2.
  IF sy-subrc <> 0.
    ROLLBACK WORK.
    MESSAGE e000 WITH 'DN冲销失败'.
  ELSE.
    COMMIT WORK AND WAIT.
    MESSAGE s000 WITH 'DN冲销成功'.
  ENDIF.
ENDFROM.
```

