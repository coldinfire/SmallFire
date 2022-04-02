---
title: " FB02 修改行文本函数 "
date: 2022-03-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO

---

### FM：CHANGE_DOCUMENT 

```ABAP
data: lt_bkpf type table of bkpf with header line,
      lt_bseg type table of bseg with header line,
      lt_bkdf type table of bkdf with header line,
      lt_bsec type table of bsec with header line,
      lt_bset type table of bset with header line,
      lt_bsed type table of bsed with header line,
      lt_bseg2 type table of bseg with header line.
SELECT * FROM bkpf INTO TABLE lt_bkpf
 WHERE bukrs = p_bukrs
   AND belnr in s_belnr
   AND gjahr = p_gjahr.
SELECT * FROM bseg INTO TABLE lt_bseg
  WHERE bukrs = p_bukrs
    AND belnr in s_belnr
    AND gjahr = p_gjahr.
LOOP AT lt_bseg.
  lt_bseg-sgtxt = 'Test change document'.
  MODIFY lt_bseg INDEX sy-tabix transporting sgtxt.
ENDLOOP.
CALL FUNCTION 'CHANGE_DOCUMENT'
  TABLES
    t_bkdf = lt_bkdf
    t_bkpf = lt_bkpf
    t_bsec = lt_bsec
    t_bsed = lt_bsed
    t_bseg = lt_bseg
    t_bset = lt_bset
  EXCEPTIONS
    others = 1.
IF sy-subrc <> 0.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
ELSE.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      WAIT = 'X'.
ENDIF.
```

