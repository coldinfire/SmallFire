---
title: " HU_CREATE_GOODS_MOVEMENT "
date: 2022-01-04
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI
---

### BAPI：HU_CREATE_GOODS_MOVEMENT

```ABAP
DATA: lv_posted TYPE sysubrc,
      ls_mess TYPE huitem_messages ,
      lt_mess TYPE huitem_messages_t WITH HEADER LINE,
      ls_emkpf TYPE emkpf ,
      lt_move_to TYPE hum_data_move_to_t,
      lt_exidv   TYPE hum_exidv_t WITH HEADER LINE,
      ls_move_to TYPE hum_data_move_to,
      ls_hu_items TYPE hum_humseg,
      lv_venum TYPE vekp-venum,
      gv_temp_message TYPE string.
DATA: lt_vepo TYPE TABLE OF vepo WITH HEADER LINE.
lv_venum = '100000012'. " Internal HU Number "
CLEAR: lt_vepo[].
SELECT * FROM vepo INTO TABLE lt_vepo
  WHERE venum = lv_venum.
IF sy-subrc NE 0.
  MESSAGE 'Please enter a valid HU !' TYPE 'I'.
  EXIT.
ENDIF.
*** Move Parameter
CLEAR: lt_move_to[],ls_move_to.
LOOP AT lt_vepo.
  ls_move_to-huwbevent = '0010'.        " Process Indicator "
  ls_move_to-matnr     = '01010101'.    " Material Number "
  ls_move_to-lgort     = lt_vepo-lgort. " Storage Location "
  ls_move_to-grund     = '0004'.        " Fixed Reason "
  ls_move_to-bwart     = '309'.         " Movement Type "
  ls_hu_items-venum = lt_vepo-venum.
  ls_hu_items-vepos = lt_vepo-vepos.
  APPEND ls_hu_items TO ls_move_to-hu_items.
  APPEND ls_move_to TO lt_move_to.
ENDLOOP.
*** Refresh
CALL FUNCTION 'HU_PACKING_REFRESH'.
PERFORM refresh_change_stock(saplv51e).
*** Call Function
CALL FUNCTION 'HU_CREATE_GOODS_MOVEMENT'
  EXPORTING
*   if_event       = lv_event
    if_simulate    = ' '
    if_commit      = ' '
*   if_tcode       = 'MOBIL'
    it_move_to     = lt_move_to[]
    it_external_id = lt_exidv[]
  IMPORTING
    ef_posted      = lv_posted
    es_message     = ls_mess
    et_messages    = lt_mess[]
    es_emkpf       = ls_emkpf.
*** Check Result
IF NOT lv_posted = 1.
  ROLLBACK WORK.
  CALL FUNCTION 'HU_PACKING_REFRESH'.
  CALL FUNCTION 'SERIAL_INTTAB_REFRESH'.
  MESSAGE 'Problem occured!' TYPE 'I'.
ELSE.
  COMMIT WORK AND WAIT.
  CONCATENATE ls_emkpf-mblnr ls_emkpf-mjahr 'document created!'
         INTO gv_temp_message SEPARATED BY SPACE.
  MESSAGE gv_temp_message TYPE 'I'.
ENDIF.
```

